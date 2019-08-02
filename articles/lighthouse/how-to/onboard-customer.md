---
title: Integrar um cliente no gerenciamento de recursos delegados do Azure – Azure Lighthouse
description: Saiba como integrar um cliente ao gerenciamento de recursos delegado do Azure, permitindo que seus recursos sejam acessados e gerenciados por meio de seu próprio locatário.
author: JnHs
ms.author: jenhayes
ms.service: lighthouse
ms.date: 07/11/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: 1885a6220f14de234710b6980b5d3b6a6172bb7e
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67810850"
---
# <a name="onboard-a-customer-to-azure-delegated-resource-management"></a>Integrar um cliente no gerenciamento de recursos delegados do Azure

Este artigo explica como você, como um provedor de serviços, pode integrar um cliente ao gerenciamento de recursos delegado do Azure, permitindo que seus recursos delegados (assinaturas e/ou grupos de recursos) sejam acessados e gerenciados por meio de seu próprio locatário do Azure Active Directory (Azure AD). Embora possamos nos referir aqui aos provedores de serviços e clientes, as empresas que gerenciam vários locatários podem usar o mesmo processo para consolidar sua experiência de gerenciamento.

Você pode repetir esse processo se estiver gerenciando recursos para vários clientes. Assim, quando um usuário autorizado entra no seu locatário, pode receber autorizado em vários escopos de locatários do cliente para executar operações de gerenciamento sem precisar entrar em cada locatário individual do cliente.

Você pode associar sua ID do MPN (Microsoft Partner Network) às assinaturas integradas para acompanhar o impacto nas participações dos clientes. Para mais informações, veja [Vincular ID de parceiro a suas contas do Azure](https://docs.microsoft.com/azure/billing/billing-partner-admin-link-started).

> [!NOTE]
> Os clientes podem ser integrados automaticamente quando comprarem uma oferta de serviços gerenciados (pública ou privada) que você publicou no Azure Marketplace. Para mais informações, veja [Publicar ofertas de Serviços Gerenciados no Azure Marketplace](publish-managed-services-offers.md). Você também pode usar o processo de integração descrito aqui com uma oferta publicada no Azure Marketplace.

O processo de integração requer que as ações sejam executadas dentro do locatário do provedor de serviços e do locatário do cliente. Todas essas etapas são descritas neste artigo.

> [!IMPORTANT]
> No momento não é possível integrar uma assinatura (ou grupo de recursos em uma assinatura) no gerenciamento de recursos delegados do Azure se a assinatura usar o Azure Databricks. Da mesma forma, se uma assinatura for registrada para integração com o provedor de recursos **Microsoft.ManagedServices**, não será possível criar um espaço de trabalho no Databricks para essa assinatura neste momento.

## <a name="gather-tenant-and-subscription-details"></a>Reunir detalhes do locatário e da assinatura

Para integrar o locatário de um cliente, ele deve ter uma assinatura ativa do Azure. Você precisa saber do seguinte:

- A ID do locatário do provedor de serviços (em que você gerenciará os recursos do cliente)
- A ID do locatário do cliente (cujos recursos serão gerenciados pelo provedor de serviços)
- As IDs de cada assinatura específica no locatário do cliente que serão gerenciadas pelo provedor de serviços (ou que contém os grupos de recursos que o provedor de serviços vai gerenciar)

Se você ainda não tiver essas informações, pode recuperá-las com uma das maneiras a seguir.

### <a name="azure-portal"></a>Portal do Azure

Você pode ver a sua ID de locatário passando o mouse sobre o nome da sua conta no lado superior direito do portal do Azure ou selecionando **Mudar diretório**. Para selecionar e copiar sua ID de locatário, pesquise "Azure Active Directory" no portal, selecione **Propriedades** e copie o valor mostrado no campo **ID de diretório**. Para localizar a ID de uma assinatura, pesquise por "Assinaturas" e selecione a ID da assinatura apropriada.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

Select-AzSubscription <subscriptionId>
```

### <a name="azure-cli"></a>CLI do Azure

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az account set --subscription <subscriptionId/name>
az account show
```


## <a name="ensure-the-customers-subscription-is-registered-for-onboarding"></a>Verificar se a assinatura do cliente está registrada para integração

Cada assinatura deve ser autorizada para integração ao ser registrada manualmente no provedor de recursos **Microsoft.ManagedServices**. O cliente pode registrar uma assinatura seguindo as etapas descritas em [Provedores e tipos de recursos do Azure](../../azure-resource-manager/resource-manager-supported-services.md).

O cliente pode confirmar que a assinatura está pronta para integração com uma das maneiras a seguir.

### <a name="azure-portal"></a>Portal do Azure

1. No portal do Azure, selecione a assinatura.
1. Selecione **Provedores de recursos**.
1. Confirme se o **Microsoft.ManagedServices** é exibido como **Registrado**.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

Set-AzContext -Subscription <subscriptionId>
Get-AzResourceProvider -ProviderNameSpace 'Microsoft.ManagedServices'
```

Deve retornar uma resposta semelhante à seguinte:

```output
ProviderNamespace : Microsoft.ManagedServices
RegistrationState : Registered
ResourceTypes     : {registrationDefinitions}
Locations         : {}

ProviderNamespace : Microsoft.ManagedServices
RegistrationState : Registered
ResourceTypes     : {registrationAssignments}
Locations         : {}

ProviderNamespace : Microsoft.ManagedServices
RegistrationState : Registered
ResourceTypes     : {operations}
Locations         : {}
```

### <a name="azure-cli"></a>CLI do Azure

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az account set –subscription <subscriptionId>
az provider show –namespace "Microsoft.ManagedServices" –-output table
```

Deve retornar uma resposta semelhante à seguinte:

```output
Namespace                  RegistrationState
-------------------------  -------------------
Microsoft.ManagedServices  Registered
```

## <a name="define-roles-and-permissions"></a>Definir funções e permissões do administrador

Como provedor de serviços, talvez você queira usar várias ofertas com um único cliente, exigindo acesso diferente para escopos diferentes.

Para facilitar o gerenciamento, é recomendável usar grupos de usuários do Azure AD para cada função, permitindo que você adicione ou remova usuários individuais do grupo em vez de atribuir as permissões diretamente a esse usuário. Também pode ser que você queira atribuir funções a uma entidade de serviço. Siga o princípio de privilégios mínimos para que os usuários tenham apenas as permissões necessárias para concluir seu trabalho, ajudando a reduzir a chance de erros acidentais. Para saber mais, veja [Práticas de segurança recomendadas](../concepts/recommended-security-practices.md).

> [!NOTE]
> As atribuições de função devem usar [funções internas](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) de RBAC (controle de acesso baseado em função). Atualmente, todas as funções internas têm suporte com o gerenciamento de recursos delegados do Azure, exceto para funções do Proprietário e quaisquer funções internas com a permissão [DataActions](https://docs.microsoft.com/azure/role-based-access-control/role-definitions#dataactions). A função interna de Administrador de Acesso do Usuário tem suporte para uso limitado, como descrito abaixo. As funções personalizadas e as [funções de administrador de assinatura clássica](https://docs.microsoft.com/azure/role-based-access-control/classic-administrators) também não têm suporte.

Para definir autorizações, você precisará saber os valores das IDs de cada usuário, grupo de usuários ou entidade de serviço para os quais você queira conceder acesso. Você também precisa da ID de definição de função para cada função interna que deseja atribuir. Se ainda não tiver essas informações, você pode recuperá-las de uma das maneiras a seguir.



### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# To retrieve the objectId for an Azure AD group
(Get-AzADGroup -DisplayName '<yourGroupName>').id

# To retrieve the objectId for an Azure AD user
(Get-AzADUser -UserPrincipalName '<yourUPN>').id

# To retrieve the objectId for an SPN
(Get-AzADApplication -DisplayName '<appDisplayName>').objectId

# To retrieve role definition IDs
(Get-AzRoleDefinition -Name '<roleName>').id
```

### <a name="azure-cli"></a>CLI do Azure

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# To retrieve the objectId for an Azure AD group
az ad group list –-query "[?displayName == '<yourGroupName>'].objectId" –-output tsv

# To retrieve the objectId for an Azure AD user
az ad user show –-upn-or-object-id "<yourUPN>" –-query "objectId" –-output tsv

# To retrieve the objectId for an SPN
az ad sp list –-query "[?displayName == '<spDisplayName>'].objectId" –-output tsv

# To retrieve role definition IDs
az role definition list –-name "<roleName>" | grep name
```

## <a name="create-an-azure-resource-manager-template"></a>Criar um modelo do Azure Resource Manager

Para integrar seu cliente, você precisa criar um [modelo de Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/) que inclua o seguinte:

|Campo  |Definição  |
|---------|---------|
|**mspName**     |Nome do provedor de serviços         |
|**mspOfferDescription**     |Uma breve descrição da sua oferta (por exemplo, "oferta de gerenciamento de VM da Contoso")      |
|**managedByTenantId**     |Sua ID de locatário         |
|**autorizações**     |Os valores de **principalId** dos usuários/grupos/SPNs do seu locatário, cada um com uma **principalIdDisplayName** para ajudar seu cliente a entender a finalidade da autorização, mapeados no valor interno de **roleDefinitionId** para especificar o nível de acesso         |

Para carregar a assinatura de um cliente, use o modelo apropriado do Resource Manager, fornecido em nosso [repositório de exemplos](https://github.com/Azure/Azure-Lighthouse-samples/), com um arquivo de parâmetros correspondentes que você modifica para corresponder à sua configuração e definir suas autorizações. Modelos separados são fornecidos caso você esteja integrando uma assinatura inteira, um grupo de recursos ou múltiplos grupos de recursos em uma assinatura. Também fornecemos um modelo que pode ser usado por clientes que compraram uma oferta de serviço gerenciado que você publicou no Azure Marketplace, se você preferir integrar suas assinaturas dessa maneira.

|**Como fazer essa integração**  |**Usar este modelo do Azure Resource Manager**  |**E modificar esse arquivo de parâmetros** |
|---------|---------|---------|
|Subscription   |[delegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/delegated-resource-management/delegatedResourceManagement.json)  |[delegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/delegated-resource-management/delegatedResourceManagement.parameters.json)    |
|Resource group   |[rgDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.json)  |[rgDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.parameters.json)    |
|Múltiplos grupos de recursos em uma assinatura   |[multipleRgDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.json)  |[multipleRgDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.parameters.json)    |
|Assinatura (ao usar uma oferta publicada no Azure Marketplace)   |[marketplaceDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.json)  |[marketplaceDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.parameters.json)    |

> [!IMPORTANT]
> O processo descrito aqui requer uma implantação separada para cada assinatura a ser integrada.
> 
> Também serão necessárias implantações separadas se você estiver integrando vários grupos de recursos em assinaturas diferentes. No entanto, a integração de múltiplos grupos de recursos em uma única assinatura pode ser feita em uma implantação.

O exemplo a seguir mostra um arquivo **resourceProjection.parameters.json** modificado que será usado para integrar uma assinatura. Os arquivos de parâmetro do grupo de recursos, localizados na pasta [rg-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management), são semelhantes, mas também incluem o parâmetro **rgName** para identificar os grupos de recursos específicos a integrar.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "mspName": {
            "value": "Fabrikam Managed Services - Interstellar"
        },
        "mspOfferDescription": {
            "value": "Fabrikam Managed Services - Interstellar"
        },
        "managedByTenantId": {
            "value": "df4602a3-920c-435f-98c4-49ff031b9ef6"
        },
        "authorizations": {
            "value": [
                {
                    "principalId": "0019bcfb-6d35-48c1-a491-a701cf73b419",
                    "principalIdDisplayName": "Tier 1 Support",
                    "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
                },
                {
                    "principalId": "0019bcfb-6d35-48c1-a491-a701cf73b419",
                    "principalIdDisplayName": "Tier 1 Support",
                    "roleDefinitionId": "36243c78-bf99-498c-9df9-86d9f8d28608"
                },
                {
                    "principalId": "0afd8497-7bff-4873-a7ff-b19a6b7b332c",
                    "principalIdDisplayName": "Tier 2 Support",
                    "roleDefinitionId": "acdd72a7-3385-48ef-bd42-f606fba81ae7"
                },
                {
                    "principalId": "9fe47fff-5655-4779-b726-2cf02b07c7c7",
                    "principalIdDisplayName": "Service Automation Account",
                    "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
                },
                {
                    "principalId": "3kl47fff-5655-4779-b726-2cf02b05c7c4",
                    "principalIdDisplayName": "Policy Automation Account",
                    "roleDefinitionId": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
                    "delegatedRoleDefinitionIds": [
                        "b24988ac-6180-42a0-ab88-20f7382dd24c",
                        "92aaf0da-9dab-42b6-94a3-d43ce8d16293"
                    ]
                }
            ]
        }
    }
}
```
A última autorização no exemplo acima adiciona **principalId** com a função de Administrador de Acesso do Usuário (18d7d88d-d35e-4fb5-a5c3-7773c20a72d9). Ao atribuir essa função, você deve incluir a propriedade **delegatedRoleDefinitionIds** e uma ou mais funções internas. O usuário criado nessa autorização poderá atribuir essas funções internas a identidades gerenciadas. Observe que nenhuma outra permissão normalmente associada à função Administrador de Acesso de Usuário será aplicada a esse usuário.

## <a name="deploy-the-azure-resource-manager-templates"></a>Implantar os modelos do Azure Resource Manager

Depois de atualizar o arquivo de parâmetros, o cliente deve implantar o modelo do Resource Manager no locatário do cliente como uma implantação em nível de assinatura. Uma implantação separada é necessária para cada assinatura que você deseja integrar ao gerenciamento de recursos delegados do Azure (ou para cada assinatura que contenha grupos de recursos que você deseja integrar).

> [!IMPORTANT]
> A implantação deve ser feita por uma conta que não seja de convidado no locatário do cliente que tem a [função interna de Proprietário](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) na assinatura que está sendo integrada (ou que contém os grupos de recursos a integrar).

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Deploy Azure Resource Manager template using template and parameter file locally
New-AzDeployment -Name <deploymentName> `
                 -Location <AzureRegion> `
                 -TemplateFile <pathToTemplateFile> `
                 -TemplateParameterFile <pathToParameterFile> `
                 -Verbose

# Deploy Azure Resource Manager template that is located externally
New-AzDeployment -Name <deploymentName> `
                 -Location <AzureRegion> `
                 -TemplateUri <templateUri> `
                 -TemplateParameterUri <parameterUri> `
                 -Verbose
```

### <a name="azure-cli"></a>CLI do Azure

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# Deploy Azure Resource Manager template using template and parameter file locally
az deployment create –-name <deploymentName> \
                     --location <AzureRegion> \
                     --template-file <pathToTemplateFile> \
                     --parameters <parameters/parameterFile> \
                     --verbose

# Deploy external Azure Resource Manager template, with local parameter file
az deployment create –-name <deploymentName \
                     –-location <AzureRegion> \
                     --template-uri <templateUri> \
                     --parameters <parameterFile> \
                     --verbose
```

## <a name="confirm-successful-onboarding"></a>Confirmar integração bem-sucedida

Quando uma assinatura de cliente estiver integrada com êxito ao gerenciamento de recursos delegados do Azure, os usuários no locatário do provedor de serviços poderão ver a assinatura e seus recursos (se tiverem recebido acesso a eles por meio do processo acima, seja individualmente ou como um membro de um grupo do Azure AD com as permissões apropriadas). Para confirmar, verifique se a assinatura é exibida de uma das maneiras a seguir.  

### <a name="azure-portal"></a>Portal do Azure

No locatário do provedor de serviços:

1. Navegue até a [página Meus clientes](view-manage-customers.md).
2. Selecione **Clientes**.
3. Confirme que você consegue ver as assinaturas com o nome da oferta fornecido no modelo do Resource Manager.

No locatário do cliente:

1. Navegue até a [página Provedores de serviços](view-manage-service-providers.md).
2. Selecione **Ofertas do provedor de serviços**.
3. Confirme que você consegue ver as assinaturas com o nome da oferta fornecido no modelo do Resource Manager.

> [!NOTE]
> Pode demorar alguns minutos depois da conclusão da implantação até que o portal do Azure reflita as atualizações.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

Get-AzContext
```

### <a name="azure-cli"></a>CLI do Azure

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az account list
```

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre as [experiências de gerenciamento entre locatários](../concepts/cross-tenant-management-experience.md).
- [Exiba e gerencie clientes](view-manage-customers.md) acessando **Meus clientes** no portal do Azure.
