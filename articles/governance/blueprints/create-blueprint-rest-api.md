---
title: 'Início Rápido: Criar um blueprint com a API REST'
description: Neste início rápido, você usa o Azure Blueprints para criar, definir e implantar artefatos usando a API REST.
ms.date: 01/27/2021
ms.topic: quickstart
ms.openlocfilehash: ceea54957e52f3b33d2c3fb2af190f15e6c94ec3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105558968"
---
# <a name="quickstart-define-and-assign-an-azure-blueprint-with-rest-api"></a>Início Rápido: Definir e atribuir um Azure Blueprint com a API REST

Aprender a criar e atribuir blueprints permite definir padrões comuns para desenvolver configurações reutilizáveis e que podem ser implantadas rapidamente com base em modelos do ARM (modelos do Azure Resource Manager), políticas, segurança e muito mais. Neste tutorial, você aprenderá a usar o Blueprint do Azure para executar algumas das tarefas comuns relacionadas à criação, publicação e atribuição de um blueprint dentro de sua organização, como:

## <a name="prerequisites"></a>Pré-requisitos

- Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.
- Registre o provedor de recursos `Microsoft.Blueprint`. Para obter instruções, confira [Provedores e tipos de recursos](../../azure-resource-manager/management/resource-providers-and-types.md).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="getting-started-with-rest-api"></a>Introdução à API REST

Se você não estiver familiarizado com a API REST, comece examinando a [Referência de API REST do Azure](/rest/api/azure/) para obter um entendimento geral sobre a API REST, especificamente o corpo da solicitação e o URI de solicitação. Este artigo usa esses conceitos para fornecer instruções para trabalhar com Azure Blueprints e pressupõe um conhecimento prático deles. Ferramentas como [ARMClient](https://github.com/projectkudu/ARMClient) e outras pessoas podem lidar com autorização automaticamente e são recomendadas para iniciantes.

Para ver as especificações do Azure Blueprints, confira a [API REST do Azure Blueprints](/rest/api/blueprints/).

### <a name="rest-api-and-powershell"></a>API REST e PowerShell

Se você ainda não tiver uma ferramenta para fazer chamadas à API REST, considere usar o PowerShell para essas instruções. A seguir está um cabeçalho de exemplo para autenticação com o Azure. Gere um cabeçalho de autenticação, às vezes chamado de um **token de portador**, e forneça o URI da API REST para se conectar com quaisquer parâmetros ou um **corpo da solicitação**:

```azurepowershell-interactive
# Log in first with Connect-AzAccount if not using Cloud Shell

$azContext = Get-AzContext
$azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
$profileClient = New-Object -TypeName Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient -ArgumentList ($azProfile)
$token = $profileClient.AcquireAccessToken($azContext.Subscription.TenantId)
$authHeader = @{
    'Content-Type'='application/json'
    'Authorization'='Bearer ' + $token.AccessToken
}

# Invoke the REST API
$restUri = 'https://management.azure.com/subscriptions/{subscriptionId}?api-version=2020-01-01'
$response = Invoke-RestMethod -Uri $restUri -Method Get -Headers $authHeader
```

Substitua `{subscriptionId}` na variável **$restUri** acima para obter informações sobre sua assinatura. A variável $response armazena o resultado do cmdlet `Invoke-RestMethod`, que pode ser analisado com os cmdlets, como [ConvertFrom-Json](/powershell/module/microsoft.powershell.utility/convertfrom-json). Se o ponto de extremidade de serviço da API REST espera um **Corpo da Solicitação**, forneça uma variável formatada do JSON para o parâmetro `-Body` de `Invoke-RestMethod`.

## <a name="create-a-blueprint"></a>Criar um plano gráfico

A primeira etapa na definição de um modelo padrão para conformidade é compor um blueprint a partir dos recursos disponíveis. Vamos criar um blueprint chamado 'MyBlueprint' para configurar as atribuições de função e política para a assinatura. Em seguida, vamos adicionar um grupo de recursos, um modelo do ARM e uma atribuição de função ao grupo de recursos.

> [!NOTE]
> Ao usar a API REST, o objeto _blueprint_ é criado primeiro. Para cada _artefato_ com parâmetros a ser adicionado, os parâmetros precisam ser definidos com antecedência no _blueprint_ inicial.

Em cada URI da API REST, há variáveis usadas que precisam ser substituídas com seus próprios valores:

- `{YourMG}` – substitua isso pela ID do grupo de gerenciamento
- `{subscriptionId}`: substitua por sua ID da assinatura

> [!NOTE]
> Especificações técnicas também podem ser criadas no nível da assinatura. Para ver um exemplo, consulte [criar um blueprint em um exemplo de assinatura](/rest/api/blueprints/blueprints/createorupdate#subscriptionblueprint).

1. Crie o objeto _blueprint_ original. O **Corpo da Solicitação** inclui propriedades sobre o blueprint, grupos de recursos que devem ser criados e todos os parâmetros no nível do blueprint. Os parâmetros são definidos durante a atribuição e usados pelos artefatos adicionados nas etapas posteriores.

   - URI da API REST

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint?api-version=2018-11-01-preview
     ```

   - Corpo da solicitação

     ```json
     {
         "properties": {
             "description": "This blueprint sets tag policy and role assignment on the subscription, creates a ResourceGroup, and deploys a resource template and role assignment to that ResourceGroup.",
             "targetScope": "subscription",
             "parameters": {
                 "storageAccountType": {
                     "type": "string",
                     "metadata": {
                         "displayName": "storage account type.",
                         "description": null
                     }
                 },
                 "tagName": {
                     "type": "string",
                     "metadata": {
                         "displayName": "The name of the tag to provide the policy assignment.",
                         "description": null
                     }
                 },
                 "tagValue": {
                     "type": "string",
                     "metadata": {
                         "displayName": "The value of the tag to provide the policy assignment.",
                         "description": null
                     }
                 },
                 "contributors": {
                     "type": "array",
                     "metadata": {
                         "description": "List of AAD object IDs that is assigned Contributor role at the subscription"
                     }
                 },
                 "owners": {
                     "type": "array",
                     "metadata": {
                         "description": "List of AAD object IDs that is assigned Owner role at the resource group"
                     }
                 }
             },
             "resourceGroups": {
                 "storageRG": {
                     "description": "Contains the resource template deployment and a role assignment."
                 }
             }
         }
     }
     ```

1. Adicione a atribuição de função na assinatura. O **Corpo da Solicitação** define o _tipo_ de artefato, as propriedades se alinham com o identificador da definição de função e as identidades de entidade de segurança são passadas como uma matriz de valores. No exemplo a seguir, as identidades de entidade de segurança concedidas à função especificada são configuradas para um parâmetro que é definido durante a atribuição do blueprint. Este exemplo usa a função interna _Colaborador_ com um GUID igual a `b24988ac-6180-42a0-ab88-20f7382dd24c`.

   - URI da API REST

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/roleContributor?api-version=2018-11-01-preview
     ```

   - Corpo da solicitação

     ```json
     {
         "kind": "roleAssignment",
         "properties": {
             "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
             "principalIds": "[parameters('contributors')]"
         }
     }
     ```

1. Adicione a atribuição de política na assinatura. O **Corpo da Solicitação** define o _tipo_ de artefato, as propriedades que se alinham com uma definição de iniciativa ou política e configura a atribuição de política para usar os parâmetros de blueprint definidos na configuração durante a atribuição do blueprint. Este exemplo usa a política interna _Aplicar a marca e seu valor padrão a grupos de recursos_ com um GUID igual a `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71`.

   - URI da API REST

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/policyTags?api-version=2018-11-01-preview
     ```

   - Corpo da solicitação

     ```json
     {
         "kind": "policyAssignment",
         "properties": {
             "description": "Apply tag and its default value to resource groups",
             "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71",
             "parameters": {
                 "tagName": {
                     "value": "[parameters('tagName')]"
                 },
                 "tagValue": {
                     "value": "[parameters('tagValue')]"
                 }
             }
         }
     }
     ```

1. Adicione outra atribuição de política para a marca Armazenamento (reutilizando o parâmetro _storageAccountType_) na assinatura. Este artefato de atribuição de política adicional demonstra que um parâmetro definido no blueprint pode ser usado por mais de um artefato. No exemplo, o **storageAccountType** é usado para definir uma marca no grupo de recursos. Esse valor fornece informações sobre a conta de armazenamento que será criada na próxima etapa. Este exemplo usa a política interna _Aplicar a marca e seu valor padrão a grupos de recursos_ com um GUID igual a `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71`.

   - URI da API REST

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/policyStorageTags?api-version=2018-11-01-preview
     ```

   - Corpo da solicitação

     ```json
     {
         "kind": "policyAssignment",
         "properties": {
             "description": "Apply storage tag and the parameter also used by the template to resource groups",
             "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71",
             "parameters": {
                 "tagName": {
                     "value": "StorageType"
                 },
                 "tagValue": {
                     "value": "[parameters('storageAccountType')]"
                 }
             }
         }
     }
     ```

1. Adicione o modelo sob o grupo de recursos. O **Corpo da Solicitação** para um modelo do ARM inclui o componente normal JSON do modelo e define o grupo de recursos de destino com **properties.resourceGroup**. O modelo também reutiliza os parâmetros de blueprint **storageAccountType**, **tagName** e **tagValue** transmitindo-os para o modelo. Os parâmetros de blueprint são disponibilizados para o modelo definindo **properties.parameters** e dentro do modelo JSON em que o par chave-valor é usado para injetar o valor. Os nomes de parâmetro de blueprint e de modelo podem ser o mesmo, mas foram diferenciados para ilustrar como cada um é transmitido do blueprint para o artefato de modelo.

   - URI da API REST

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/templateStorage?api-version=2018-11-01-preview
     ```

   - Corpo da solicitação

     ```json
     {
         "kind": "template",
         "properties": {
             "template": {
                 "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                 "contentVersion": "1.0.0.0",
                 "parameters": {
                     "storageAccountTypeFromBP": {
                         "type": "string",
                         "defaultValue": "Standard_LRS",
                         "allowedValues": [
                             "Standard_LRS",
                             "Standard_GRS",
                             "Standard_ZRS",
                             "Premium_LRS"
                         ],
                         "metadata": {
                             "description": "Storage Account type"
                         }
                     },
                     "tagNameFromBP": {
                         "type": "string",
                         "defaultValue": "NotSet",
                         "metadata": {
                             "description": "Tag name from blueprint"
                         }
                     },
                     "tagValueFromBP": {
                         "type": "string",
                         "defaultValue": "NotSet",
                         "metadata": {
                             "description": "Tag value from blueprint"
                         }
                     }
                 },
                 "variables": {
                     "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
                 },
                 "resources": [{
                     "type": "Microsoft.Storage/storageAccounts",
                     "name": "[variables('storageAccountName')]",
                     "apiVersion": "2016-01-01",
                     "tags": {
                        "[parameters('tagNameFromBP')]": "[parameters('tagValueFromBP')]"
                     },
                     "location": "[resourceGroups('storageRG').location]",
                     "sku": {
                         "name": "[parameters('storageAccountTypeFromBP')]"
                     },
                     "kind": "Storage",
                     "properties": {}
                 }],
                 "outputs": {
                     "storageAccountSku": {
                         "type": "string",
                         "value": "[variables('storageAccountName')]"
                     }
                 }
             },
             "resourceGroup": "storageRG",
             "parameters": {
                 "storageAccountTypeFromBP": {
                     "value": "[parameters('storageAccountType')]"
                 },
                 "tagNameFromBP": {
                     "value": "[parameters('tagName')]"
                 },
                 "tagValueFromBP": {
                     "value": "[parameters('tagValue')]"
                 }
             }
         }
     }
     ```

1. Adicione atribuição de função sob o grupo de recursos. Semelhante à entrada de atribuição de função anterior, o exemplo abaixo usa o identificador da definição para a função **Proprietário** e fornece a ela um parâmetro diferente do blueprint. Este exemplo usa a função interna _Proprietário_ com um GUID igual a `8e3af657-a8ff-443c-a75c-2fe8c4bcb635`.

   - URI da API REST

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/roleOwner?api-version=2018-11-01-preview
     ```

   - Corpo da solicitação

     ```json
     {
         "kind": "roleAssignment",
         "properties": {
             "resourceGroup": "storageRG",
             "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
             "principalIds": "[parameters('owners')]"
         }
     }
     ```

## <a name="publish-a-blueprint"></a>Publicar um modelo

Agora que os artefatos foram adicionados ao blueprint, é hora de publicá-lo. A publicação disponibiliza a atribuição a uma assinatura.

- URI da API REST

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/versions/{BlueprintVersion}?api-version=2018-11-01-preview
  ```

O valor de `{BlueprintVersion}` é uma cadeia de caracteres de letras, números e hifens (sem espaços ou outros caracteres especiais) com um comprimento máximo de 20 caracteres. Use algo exclusivo e informativo, como **v20180622-135541**.

## <a name="assign-a-blueprint"></a>Atribuir um modelo

Depois que um blueprint é publicado usando a API REST, ele pode ser atribuído a uma assinatura. Atribua o blueprint que você criou a uma das assinaturas em sua hierarquia do grupo de gerenciamento. Se o blueprint for salvo em uma assinatura, ele só poderá ser atribuído a essa assinatura. O **Corpo da Solicitação** especifica o blueprint a ser atribuído, fornece o nome e a localização para quaisquer grupos de recursos na definição do blueprint e fornece todos os parâmetros definidos no blueprint e usados por um ou mais artefatos anexados.

Em cada URI da API REST, há variáveis usadas que precisam ser substituídas com seus próprios valores:

- `{tenantId}` – Substitua pela ID de locatário
- `{YourMG}` – substitua isso pela ID do grupo de gerenciamento
- `{subscriptionId}`: substitua por sua ID da assinatura

1. Forneça à entidade de serviço do Azure Blueprint a função **Proprietário** na assinatura de destino. A AppId é estática (`f71766dc-90d9-4b7d-bd9d-4499c4331c3f`), mas a ID da entidade de serviço varia por locatário. Os detalhes podem ser solicitados para seu locatário usando a API REST a seguir. Ela usa a [API do Graph do Azure Active Directory](/graph/migrate-azure-ad-graph-planning-checklist) que tem uma autorização diferente.

   - URI da API REST

     ```http
     GET https://graph.windows.net/{tenantId}/servicePrincipals?api-version=1.6&$filter=appId eq 'f71766dc-90d9-4b7d-bd9d-4499c4331c3f'
     ```

1. Execute a implantação do blueprint atribuindo-o uma assinatura. Como os parâmetros **contributors** e **owners** exigem que uma matriz de objectIds das entidades de segurança seja concedida à atribuição de função, use a [API do Graph do Azure Active Directory](/graph/migrate-azure-ad-graph-planning-checklist) para coletar as objectIds a serem usadas no **Corpo da Solicitação** de seus usuários, grupos ou entidades de serviço.

   - URI da API REST

     ```http
     PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Blueprint/blueprintAssignments/assignMyBlueprint?api-version=2018-11-01-preview
     ```

   - Corpo da solicitação

     ```json
     {
         "properties": {
             "blueprintId": "/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint",
             "resourceGroups": {
                 "storageRG": {
                     "name": "StorageAccount",
                     "location": "eastus2"
                 }
             },
             "parameters": {
                 "storageAccountType": {
                     "value": "Standard_GRS"
                 },
                 "tagName": {
                     "value": "CostCenter"
                 },
                 "tagValue": {
                     "value": "ContosoIT"
                 },
                 "contributors": {
                     "value": [
                         "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
                         "38833b56-194d-420b-90ce-cff578296714"
                     ]
                 },
                 "owners": {
                     "value": [
                         "44254d2b-a0c7-405f-959c-f829ee31c2e7",
                         "316deb5f-7187-4512-9dd4-21e7798b0ef9"
                     ]
                 }
             }
         },
         "identity": {
             "type": "systemAssigned"
         },
         "location": "westus"
     }
     ```

   - Identidade gerenciada atribuída pelo usuário

     Uma atribuição de blueprint também pode usar uma [identidade gerenciada atribuída por usuário](../../active-directory/managed-identities-azure-resources/overview.md).
     Nesse caso, a parte **identidade** do corpo da solicitação é alterada da seguinte maneira. Substitua `{yourRG}` e `{userIdentity}` pelo nome do grupo de recursos e pelo nome da identidade gerenciada atribuída por usuário, respectivamente.

     ```json
     "identity": {
         "type": "userAssigned",
         "tenantId": "{tenantId}",
         "userAssignedIdentities": {
             "/subscriptions/{subscriptionId}/resourceGroups/{yourRG}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{userIdentity}": {}
         }
     },
     ```

     A **identidade gerenciada atribuída por usuário** pode estar em qualquer assinatura e grupo de recursos aos quais o usuário que atribui o blueprint tem acesso.

     > [!IMPORTANT]
     > O Azure Blueprints não gerencia a identidade gerenciada atribuída por usuário. Os usuários são responsáveis por atribuir funções e permissões suficientes ou a atribuição de blueprint falhará.

## <a name="clean-up-resources"></a>Limpar os recursos

### <a name="unassign-a-blueprint"></a>Cancelar a atribuição de um blueprint

Você pode remover um blueprint de uma assinatura. A remoção geralmente é feita quando os recursos de artefato não são mais necessários. Quando um blueprint é removido, os artefatos atribuídos como parte desse blueprint são deixados para trás. Para remover uma atribuição de blueprint, use a seguinte operação de API REST:

- URI da API REST

  ```http
  DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Blueprint/blueprintAssignments/assignMyBlueprint?api-version=2018-11-01-preview
  ```

### <a name="delete-a-blueprint"></a>Excluir um blueprint

Para remover um blueprint em si, use a seguinte operação de API REST:

- URI da API REST

  ```http
  DELETE https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint?api-version=2018-11-01-preview
  ```

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou, atribuiu e removeu um blueprint com a API REST. Para saber mais sobre o Azure Blueprints, prossiga para o artigo de ciclo de vida do blueprint.

> [!div class="nextstepaction"]
> [Saiba mais sobre o ciclo de vida do blueprint](./concepts/lifecycle.md)