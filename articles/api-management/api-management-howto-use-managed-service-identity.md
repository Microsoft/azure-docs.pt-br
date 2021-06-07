---
title: Usar identidades gerenciadas no Gerenciamento de API do Azure | Microsoft Docs
description: Saiba como criar identidades atribuídas pelo usuário e atribuídas pelo sistema no Gerenciamento de API usando o portal do Azure, o PowerShell e um modelo do Resource Manager.
services: api-management
documentationcenter: ''
author: miaojiang
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 03/09/2021
ms.author: apimpm
ms.openlocfilehash: 98237efae89e7d88dd23cb7e8fc9f7e9f05bca70
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102521536"
---
# <a name="use-managed-identities-in-azure-api-management"></a>Usar identidades gerenciadas no Gerenciamento de API do Azure

Este artigo mostra como criar uma identidade gerenciada para uma instância do Gerenciamento de API do Azure e como acessar outros recursos. Uma identidade gerenciada gerada pelo Azure AD (Azure Active Directory) permite que a instância de Gerenciamento de API acesse de maneira fácil e segura outros recursos protegidos pelo Azure AD, como o Azure Key Vault. O Azure gerencia essa identidade para que você não precise provisionar ou girar nenhum segredo. Para obter mais informações sobre identidades gerenciadas, confira [O que são identidades gerenciadas para recursos do Azure?](../active-directory/managed-identities-azure-resources/overview.md).

Você pode conceder dois tipos de identidade para uma instância do Gerenciamento de API:

- Uma *identidade atribuída pelo sistema* é vinculada ao seu serviço e é excluída se o seu serviço é excluído. O serviço pode ter apenas uma identidade atribuída pelo sistema.
- Uma *identidade atribuída pelo usuário* é um recurso independente do Azure que pode ser atribuído ao seu serviço. Um serviço pode ter várias identidades atribuídas pelo usuário.

## <a name="create-a-system-assigned-managed-identity"></a>Criar uma identidade gerenciada atribuída pelo sistema

### <a name="azure-portal"></a>Portal do Azure

Para configurar uma identidade gerenciada no portal do Azure, primeiro crie uma instância do Gerenciamento de API e depois habilite o recurso.

1. Crie uma instância de Gerenciamento de API no portal, como você faria normalmente. Navegue até ela no portal.
2. Selecione **Identidades gerenciadas**.
3. Na guia **Sistema atribuído**, alterne o **Status** para **Ativado**. Clique em **Salvar**.

    :::image type="content" source="./media/api-management-msi/enable-system-msi.png" alt-text="Seleções para habilitar uma identidade gerenciada atribuída pelo sistema" border="true":::

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

As etapas a seguir guiarão você na criação de uma instância do Gerenciamento de API e na atribuição de uma identidade a ela usando o Azure PowerShell. 

1. Se necessário, instale o Azure PowerShell usando as instruções do [Guia do Azure PowerShell](/powershell/azure/install-az-ps). Então, execute `Connect-AzAccount` para criar uma conexão com o Azure.

2. Use o código a seguir para criar a instância. Para obter mais exemplos de como usar o Azure PowerShell com uma instância do Gerenciamento de API, confira [Exemplos do PowerShell do Gerenciamento de API](powershell-samples.md).

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create an API Management Consumption Sku service.
    New-AzApiManagement -ResourceGroupName $resourceGroupName -Name consumptionskuservice -Location $location -Sku Consumption -Organization contoso -AdminEmail contoso@contoso.com -SystemAssignedIdentity
    ```

3. Atualize uma instância existente para criar a identidade:

    ```azurepowershell-interactive
    # Get an API Management instance
    $apimService = Get-AzApiManagement -ResourceGroupName $resourceGroupName -Name $apiManagementName

    # Update an API Management instance
    Set-AzApiManagement -InputObject $apimService -SystemAssignedIdentity
    ```

### <a name="azure-resource-manager-template"></a>Modelo do Azure Resource Manager

Crie uma instância de Gerenciamento de API com uma identidade incluindo a seguinte propriedade na definição de recurso:

```json
"identity" : {
    "type" : "SystemAssigned"
}
```

Essa propriedade faz com que o Azure crie e gerencie a identidade da instância de Gerenciamento de API.

Por exemplo, um modelo completo do Azure Resource Manager pode ter a seguinte aparência:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "0.9.0.0",
    "resources": [{
        "apiVersion": "2019-01-01",
        "name": "contoso",
        "type": "Microsoft.ApiManagement/service",
        "location": "[resourceGroup().location]",
        "tags": {},
        "sku": {
            "name": "Developer",
            "capacity": "1"
        },
        "properties": {
            "publisherEmail": "admin@contoso.com",
            "publisherName": "Contoso"
        },
        "identity": {
            "type": "systemAssigned"
        }
    }]
}
```

Quando a instância é criada, ela tem as seguintes propriedades adicionais:

```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

A propriedade `tenantId` identifica a qual locatário do Azure AD a identidade pertence. A propriedade `principalId` é um identificador exclusivo para a nova identidade da instância. No Azure AD, a entidade de serviço tem o mesmo nome que você deu à sua instância do Gerenciamento de API.

> [!NOTE]
> Uma instância do Gerenciamento de API pode ter identidades atribuídas pelo sistema e atribuídas pelo usuário ao mesmo tempo. Nesse caso, a propriedade `type` seria `SystemAssigned,UserAssigned`.

## <a name="supported-scenarios-using-system-assigned-identity"></a>Cenários com suporte usando a identidade atribuída pelo sistema

### <a name="obtain-a-custom-tlsssl-certificate-for-the-api-management-instance-from-azure-key-vault"></a><a name="use-ssl-tls-certificate-from-azure-key-vault"></a>Obter um certificado TLS/SSL personalizado para a instância de Gerenciamento de API do Azure Key Vault
Você pode usar a identidade atribuída pelo sistema de uma instância do Gerenciamento de API para recuperar certificados TLS/SSL personalizados armazenados no Azure Key Vault. Então, você pode atribuir esses certificados a domínios personalizados na instância do Gerenciamento de API. Lembre-se do seguinte:

- O tipo de conteúdo do segredo deve ser *application/x-pkcs12*.
- Use o ponto de extremidade de segredo do certificado do cofre de chaves, que contém o segredo.

> [!Important]
> Se você não fornecer a versão do objeto do certificado, o Gerenciamento de API obterá automaticamente a versão mais recente do certificado dentro de quatro horas após sua atualização no Key Vault.

O exemplo a seguir mostra um modelo do Azure Resource Manager que contém as seguintes etapas:

1. Criar uma instância do Gerenciamento de API com uma identidade gerenciada.
2. Atualizar as políticas de acesso de uma instância do Azure Key Vault e permitir que a instância de Gerenciamento de API obtenha os segredos dele.
3. Atualizar a instância de Gerenciamento de API configurando um nome de domínio personalizado por meio de um certificado da instância do Key Vault.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "publisherEmail": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "The email address of the owner of the service"
            }
        },
        "publisherName": {
            "type": "string",
            "defaultValue": "Contoso",
            "minLength": 1,
            "metadata": {
                "description": "The name of the owner of the service"
            }
        },
        "sku": {
            "type": "string",
            "allowedValues": ["Developer",
            "Standard",
            "Premium"],
            "defaultValue": "Developer",
            "metadata": {
                "description": "The pricing tier of this API Management instance"
            }
        },
        "skuCount": {
            "type": "int",
            "defaultValue": 1,
            "metadata": {
                "description": "The instance size of this API Management instance."
            }
        },
        "keyVaultName": {
            "type": "string",
            "metadata": {
                "description": "Name of the vault"
            }
        },
        "proxyCustomHostname1": {
            "type": "string",
            "metadata": {
                "description": "Proxy Custom hostname."
            }
        },
        "keyVaultIdToCertificate": {
            "type": "string",
            "metadata": {
                "description": "Reference to the Key Vault certificate. https://contoso.vault.azure.net/secrets/contosogatewaycertificate."
            }
        }
    },
    "variables": {
        "apiManagementServiceName": "[concat('apiservice', uniqueString(resourceGroup().id))]",
        "apimServiceIdentityResourceId": "[concat(resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName')),'/providers/Microsoft.ManagedIdentity/Identities/default')]"
    },
    "resources": [{
        "apiVersion": "2019-01-01",
        "name": "[variables('apiManagementServiceName')]",
        "type": "Microsoft.ApiManagement/service",
        "location": "[resourceGroup().location]",
        "tags": {
        },
        "sku": {
            "name": "[parameters('sku')]",
            "capacity": "[parameters('skuCount')]"
        },
        "properties": {
            "publisherEmail": "[parameters('publisherEmail')]",
            "publisherName": "[parameters('publisherName')]"
        },
        "identity": {
            "type": "systemAssigned"
        }
    },
    {
        "type": "Microsoft.KeyVault/vaults/accessPolicies",
        "name": "[concat(parameters('keyVaultName'), '/add')]",
        "apiVersion": "2015-06-01",
        "dependsOn": [
            "[resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName'))]"
        ],
        "properties": {
            "accessPolicies": [{
                "tenantId": "[reference(variables('apimServiceIdentityResourceId'), '2015-08-31-PREVIEW').tenantId]",
                "objectId": "[reference(variables('apimServiceIdentityResourceId'), '2015-08-31-PREVIEW').principalId]",
                "permissions": {
                    "secrets": ["get"]
                }
            }]
        }
    },
    {
        "apiVersion": "2017-05-10",
        "name": "apimWithKeyVault",
        "type": "Microsoft.Resources/deployments",
        "dependsOn": [
        "[resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName'))]"
        ],
        "properties": {
            "mode": "incremental",
            "templateLink": {
                "uri": "https://raw.githubusercontent.com/solankisamir/arm-templates/master/basicapim.keyvault.json",
                "contentVersion": "1.0.0.0"
            },
            "parameters": {
                "publisherEmail": { "value": "[parameters('publisherEmail')]"},
                "publisherName": { "value": "[parameters('publisherName')]"},
                "sku": { "value": "[parameters('sku')]"},
                "skuCount": { "value": "[parameters('skuCount')]"},
                "proxyCustomHostname1": {"value" : "[parameters('proxyCustomHostname1')]"},
                "keyVaultIdToCertificate": {"value" : "[parameters('keyVaultIdToCertificate')]"}
            }
        }
    }]
}
```

### <a name="authenticate-to-the-back-end-by-using-an-api-management-identity"></a>Autenticar para o back-end usando uma identidade do Gerenciamento de API

Você pode usar a identidade atribuída pelo sistema para autenticação no back-end por meio da política de [identidade gerenciada por autenticação](api-management-authentication-policies.md#ManagedIdentity).

### <a name="connect-to-azure-resources-behind-ip-firewall-using-system-assigned-managed-identity"></a><a name="apim-as-trusted-service"></a>Conectar-se aos recursos do Azure por trás do Firewall de IP usando identidade gerenciada atribuída ao sistema


O Gerenciamento de API é um serviço confiável da Microsoft para os recursos a seguir. Isso permite que o serviço se conecte aos recursos a seguir, por trás de um firewall. Depois de atribuir explicitamente a função apropriada do Azure à [identidade gerenciada atribuída pelo sistema](../active-directory/managed-identities-azure-resources/overview.md) para essa instância de recurso, o escopo de acesso da instância corresponde à função do Azure atribuída à identidade gerenciada.


|Serviço do Azure | Link|
|---|---|
|Armazenamento do Azure | [Acesso confiável ao armazenamento do Azure](../storage/common/storage-network-security.md?tabs=azure-portal#trusted-access-based-on-system-assigned-managed-identity)|
|Barramento de Serviço do Azure | [Acesso confiável ao barramento de serviço do Azure](../service-bus-messaging/service-bus-ip-filtering.md#trusted-microsoft-services)|
|Hub de Eventos do Azure | [Acesso confiável ao hub de eventos do Azure](../event-hubs/event-hubs-ip-filtering.md#trusted-microsoft-services)|


## <a name="create-a-user-assigned-managed-identity"></a>Criar uma identidade gerenciada atribuída ao usuário

> [!NOTE]
> Você pode associar uma instância de Gerenciamento de API com até 10 identidades gerenciadas atribuídas pelo usuário.

### <a name="azure-portal"></a>Portal do Azure

Para configurar uma identidade gerenciada no portal, primeiro crie uma instância do Gerenciamento de API e depois habilite o recurso.

1. Crie uma instância de Gerenciamento de API no portal, como você faria normalmente. Navegue até ela no portal.
2. Selecione **Identidades gerenciadas**.
3. Na guia **Usuário atribuído**, selecione **Adicionar**.
4. Procure a identidade que você criou anteriormente e selecione-a. Selecione **Adicionar**.

   :::image type="content" source="./media/api-management-msi/enable-user-assigned-msi.png" alt-text="Seleções para habilitar uma identidade gerenciada atribuída pelo usuário" border="true":::

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

As etapas a seguir guiarão você na criação de uma instância do Gerenciamento de API e na atribuição de uma identidade a ela usando o Azure PowerShell. 

1. Se necessário, instale o Azure PowerShell usando as instruções do [Guia do Azure PowerShell](/powershell/azure/install-az-ps). Então, execute `Connect-AzAccount` para criar uma conexão com o Azure.

2. Use o código a seguir para criar a instância. Para obter mais exemplos de como usar o Azure PowerShell com uma instância do Gerenciamento de API, confira [Exemplos do PowerShell do Gerenciamento de API](powershell-samples.md).

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create a user-assigned identity. This requires installation of the "Az.ManagedServiceIdentity" module.
    $userAssignedIdentity = New-AzUserAssignedIdentity -Name $userAssignedIdentityName -ResourceGroupName $resourceGroupName

    # Create an API Management Consumption Sku service.
    $userIdentities = @($userAssignedIdentity.Id)

    New-AzApiManagement -ResourceGroupName $resourceGroupName -Location $location -Name $apiManagementName -Organization contoso -AdminEmail admin@contoso.com -Sku Consumption -UserAssignedIdentity $userIdentities
    ```

3. Atualize um serviço existente para atribuir uma identidade ao serviço:

    ```azurepowershell-interactive
    # Get an API Management instance
    $apimService = Get-AzApiManagement -ResourceGroupName $resourceGroupName -Name $apiManagementName

    # Create a user-assigned identity. This requires installation of the "Az.ManagedServiceIdentity" module.
    $userAssignedIdentity = New-AzUserAssignedIdentity -Name $userAssignedIdentityName -ResourceGroupName $resourceGroupName

    # Update an API Management instance
    $userIdentities = @($userAssignedIdentity.Id)
    Set-AzApiManagement -InputObject $apimService -UserAssignedIdentity $userIdentities
    ```

### <a name="azure-resource-manager-template"></a>Modelo do Azure Resource Manager

Crie uma instância de Gerenciamento de API com uma identidade incluindo a seguinte propriedade na definição de recurso:

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {}
    }
}
```

Adicionar o tipo atribuído pelo usuário informa ao Azure para usar a identidade atribuída pelo usuário especificada para a sua instância.

Por exemplo, um modelo completo do Azure Resource Manager pode ter a seguinte aparência:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "0.9.0.0",
    "resources": [{
        "apiVersion": "2019-12-01",
        "name": "contoso",
        "type": "Microsoft.ApiManagement/service",
        "location": "[resourceGroup().location]",
        "tags": {},
        "sku": {
            "name": "Developer",
            "capacity": "1"
        },
        "properties": {
            "publisherEmail": "admin@contoso.com",
            "publisherName": "Contoso"
        },
        "identity": {
            "type": "UserAssigned",
             "userAssignedIdentities": {
                "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]": {}
             }
        },
         "dependsOn": [       
          "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]"
        ]
    }]
}
```

Quando o site é criado, ele tem as seguintes propriedades adicionais:

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {
            "principalId": "<PRINCIPALID>",
            "clientId": "<CLIENTID>"
        }
    }
}
```

A propriedade `principalId` é um identificador exclusivo para a identidade que é usada para a administração do Azure AD. A propriedade `clientId` é um identificador exclusivo para a nova identidade do aplicativo que é usada para especificar qual identidade usar durante as chamadas do runtime.

> [!NOTE]
> Uma instância do Gerenciamento de API pode ter identidades atribuídas pelo sistema e atribuídas pelo usuário ao mesmo tempo. Nesse caso, a propriedade `type` seria `SystemAssigned,UserAssigned`.

## <a name="supported-scenarios-using-user-assigned-managed-identity"></a>Cenários com suporte usando a identidade gerenciada atribuída ao usuário

### <a name="obtain-a-custom-tlsssl-certificate-for-the-api-management-instance-from-azure-key-vault"></a><a name="use-ssl-tls-certificate-from-azure-key-vault-ua"></a>Obter um certificado TLS/SSL personalizado para a instância de Gerenciamento de API do Azure Key Vault
Você pode usar qualquer identidade atribuída pelo usuário para estabelecer a confiança entre uma instância do Gerenciamento de API e o Key Vault. Essa relação de confiança pode ser usada para recuperar certificados TLS/SSL personalizados armazenados no Azure Key Vault. Então, você pode atribuir esses certificados a domínios personalizados na instância do Gerenciamento de API. 

Lembre-se do seguinte:

- O tipo de conteúdo do segredo deve ser *application/x-pkcs12*.
- Use o ponto de extremidade de segredo do certificado do cofre de chaves, que contém o segredo.

> [!Important]
> Se você não fornecer a versão do objeto do certificado, o Gerenciamento de API obterá automaticamente a versão mais recente do certificado dentro de quatro horas após sua atualização no Key Vault.

Para obter o modelo completo, confira [Gerenciamento de API com o SSL baseado no Key Vault usando a identidade atribuída pelo usuário](https://github.com/Azure/azure-quickstart-templates/blob/master/101-api-management-key-vault-create/azuredeploy.json).

Neste modelo, você implantará:

* Gerenciamento de API do Azure
* Identidade gerenciada atribuída pelo usuário do Azure
* Azure Key Vault para armazenar o certificado SSL/TLS

Para executar a implantação automaticamente, clique no seguinte botão:

[![Implantar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-api-management-key-vault-create%2Fazuredeploy.json)

### <a name="authenticate-to-the-back-end-by-using-a-user-assigned-identity"></a>Autenticar no back-end usando uma identidade atribuída pelo usuário

Você pode usar a identidade atribuída pelo usuário para autenticação no back-end por meio da política de [identidade gerenciada por autenticação](api-management-authentication-policies.md#ManagedIdentity).

## <a name="remove-an-identity"></a><a name="remove"></a>Remover uma identidade

Você pode remover uma identidade atribuída pelo sistema desabilitando o recurso por meio do portal ou do modelo de Azure Resource Manager da mesma forma que ele foi criado. As identidades atribuídas pelo usuário podem ser removidas individualmente. Para remover todas as identidades, defina o tipo de identidade como `"None"`.

Remover uma identidade atribuída pelo sistema dessa maneira também a excluirá do Azure Active Directory. As identidades atribuídas pelo sistema também são automaticamente removidas do Azure AD quando a instância do Gerenciamento de API é excluída.

Para remover todas as identidades usando o modelo Azure Resource Manager, atualize esta seção:

```json
"identity": {
    "type": "None"
}
```

> [!Important]
> Se uma instância do Gerenciamento de API estiver configurada com um certificado SSL personalizado do Key Vault e você tentar desabilitar uma identidade gerenciada, a solicitação falhará.
>
> Você pode desbloqueá-lo alternando de um certificado do Azure Key Vault para um certificado codificado embutido e, depois, desabilitando a identidade gerenciada. Para saber mais, confira [Configurar um nome de domínio personalizado](configure-custom-domain.md).

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre identidades gerenciadas para recursos do Azure:

* [O que são identidades gerenciadas para recursos do Azure?](../active-directory/managed-identities-azure-resources/overview.md)
* [Modelos do Gerenciador de Recursos do Azure](https://github.com/Azure/azure-quickstart-templates)
* [Autenticar com uma identidade gerenciada em uma política](./api-management-authentication-policies.md#ManagedIdentity)
