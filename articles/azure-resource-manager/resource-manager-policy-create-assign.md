---
title: "Atribuir e gerenciar políticas de recursos do Azure | Microsoft Docs"
description: "Descreve como aplicar as políticas de recursos do Azure a assinaturas e grupos de recursos e como exibir políticas de recursos."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/19/2017
ms.author: tomfitz
ms.openlocfilehash: 64bdd6ed41e98079c8d4112e895aaeddcd629282
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="assign-and-manage-resource-policies"></a>Atribuir e gerenciar políticas de recursos

Para implantar uma política, é necessário executar as seguintes etapas:

1. Verifique as definições de política (incluindo políticas internas fornecidas pelo Azure) para saber se já existe na sua assinatura que atende aos requisitos.
2. Se existir, obtenha seu nome.
3. Se não, defina a regra de política com JSON e adicione-a como uma definição de política em sua assinatura. Essa etapa disponibiliza a política para atribuição, mas não aplica as regras à sua assinatura.
4. Para qualquer caso, atribua a política a um escopo (como uma assinatura ou grupo de recursos). As regras da política agora são impostas.

Este artigo ressalta as etapas para criação de uma definição de política e atribuição dessa definição a um escopo por meio da API REST, do PowerShell ou da CLI do Azure. Se preferir usar o portal para atribuir políticas, consulte [Usar o portal do Azure para atribuir e gerenciar políticas de recurso](resource-manager-policy-portal.md). Este artigo não tem como foco a sintaxe para criação da definição de política. Para obter informações sobre a sintaxe da política, confira [Visão geral da política de recursos](resource-manager-policy.md).

## <a name="exclusion-scopes"></a>Escopos de exclusão

Ao atribuir uma política, você pode excluir um escopo. Esse recurso simplifica a atribuição de políticas porque você pode atribuir uma política no nível da assinatura e especificar onde ela não será aplicada. Por exemplo, em sua assinatura, você tem um grupo de recursos destinado à infraestrutura de rede. Equipes de aplicativo implantam seus recursos em outros grupos de recursos. Você não quer que essas equipes criem recursos de rede que possam levar a problemas de segurança. No entanto, no grupo de recursos de rede, você deseja permitir recursos de rede. Você atribui a política no nível da assinatura, mas exclui o grupo de recursos de rede. É possível especificar vários escopos secundários.

```json
{
    "properties":{
        "policyDefinitionId":"<ID for policy definition>",
        "notScopes":[
            "/subscriptions/<subid>/resourceGroups/networkresourceGroup1"
        ]
    }
}
```

Se você especificar um escopo de exclusão em sua atribuição, use a versão **2017-06-01-preview** da API.

## <a name="rest-api"></a>API REST

### <a name="create-policy-definition"></a>Criar definição de política

Você pode criar uma política com a [API REST para Definições de Política](/rest/api/resources/policydefinitions). A API REST permite que você crie e exclua as definições de políticas e obtenha informações sobre as definições existentes.

Para criar uma definição de política, execute:

```HTTP
PUT https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.authorization/policydefinitions/{policyDefinitionName}?api-version={api-version}
```

Inclua um corpo de solicitação semelhante ao exemplo a seguir:

```json
{
  "properties": {
    "parameters": {
      "allowedLocations": {
        "type": "array",
        "metadata": {
          "description": "The list of locations that can be specified when deploying resources",
          "strongType": "location",
          "displayName": "Allowed locations"
        }
      }
    },
    "displayName": "Allowed locations",
    "description": "This policy enables you to restrict the locations your organization can specify when deploying resources.",
    "policyRule": {
      "if": {
        "not": {
          "field": "location",
          "in": "[parameters('allowedLocations')]"
        }
      },
      "then": {
        "effect": "deny"
      }
    }
  }
}
```

### <a name="assign-policy"></a>Atribuir política

Você pode aplicar a definição de política no escopo desejado por meio da [API REST para atribuições de política](/rest/api/resources/policyassignments). A API REST permite que você crie e exclua as atribuições de políticas e obtenha informações sobre as atribuições existentes.

Para criar uma atribuição de política, execute:

```HTTP
PUT https://management.azure.com /subscriptions/{subscription-id}/providers/Microsoft.authorization/policyassignments/{policyAssignmentName}?api-version={api-version}
```

A {Atribuição da política} é o nome da atribuição da política.

Inclua um corpo de solicitação semelhante ao exemplo a seguir:

```json
{
  "properties":{
    "displayName":"West US only policy assignment on the subscription ",
    "description":"Resources can only be provisioned in West US regions",
    "parameters": {
      "allowedLocations": { "value": ["northeurope", "westus"] }
     },
    "policyDefinitionId":"/subscriptions/{subscription-id}/providers/Microsoft.Authorization/policyDefinitions/{definition-name}",
      "scope":"/subscriptions/{subscription-id}"
  },
}
```

### <a name="view-policy"></a>Exibir política
Para obter uma política, use a operação [Obter definição de política](https://docs.microsoft.com/rest/api/resources/policydefinitions#PolicyDefinitions_Get).

### <a name="get-aliases"></a>Obter aliases
Você pode recuperar aliases por meio da API REST:

```HTTP
GET /subscriptions/{id}/providers?$expand=resourceTypes/aliases&api-version=2015-11-01
```

O exemplo a seguir mostra uma definição de um alias. Como é possível ver, um alias define caminhos em diferentes versões de API, mesmo quando há uma alteração de nome da propriedade. 

```json
"aliases": [
    {
      "name": "Microsoft.Storage/storageAccounts/sku.name",
      "paths": [
        {
          "path": "properties.accountType",
          "apiVersions": [
            "2015-06-15",
            "2015-05-01-preview"
          ]
        },
        {
          "path": "sku.name",
          "apiVersions": [
            "2016-01-01"
          ]
        }
      ]
    }
]
```

## <a name="powershell"></a>PowerShell

Antes de continuar com os exemplos do PowerShell, verifique se você tem [instalada a versão mais recente](/powershell/azure/install-azurerm-ps) do PowerShell do Azure. Parâmetros de política foram adicionados na versão 3.6.0. Se você tiver uma versão mais antiga, os exemplos retornam um erro indicando que o parâmetro não pode ser encontrado.

### <a name="view-policy-definitions"></a>Exibir definições de políticas
Para visualizar todas as definições de política em sua assinatura, utilize o seguinte comando:

```powershell
Get-AzureRmPolicyDefinition
```

Ele retorna todas as definições de política disponíveis, incluindo políticas internas. Cada política é retornada no seguinte formato:

```powershell
Name               : e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceId         : /providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceName       : e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceType       : Microsoft.Authorization/policyDefinitions
Properties         : @{displayName=Allowed locations; policyType=BuiltIn; description=This policy enables you to
                     restrict the locations your organization can specify when deploying resources. Use to enforce
                     your geo-compliance requirements.; parameters=; policyRule=}
PolicyDefinitionId : /providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c
```

Antes de continuar a criar uma definição de política, observe as políticas internas. Se você encontrar uma política interna que aplica os limites necessários, você poderá ignorar a criação de uma definição de política. Em vez disso, atribua a política interna ao escopo desejado.

### <a name="create-policy-definition"></a>Criar definição de política
Você pode criar uma definição de política usando o cmdlet `New-AzureRmPolicyDefinition`.

Para criar uma definição de política de um arquivo, passe o caminho para o arquivo. Para um arquivo externo, use:

```powershell
$definition = New-AzureRmPolicyDefinition `
    -Name denyCoolTiering `
    -DisplayName "Deny cool access tiering for storage" `
    -Policy 'https://raw.githubusercontent.com/Azure/azure-policy-samples/master/samples/Storage/storage-account-access-tier/azurepolicy.rules.json'
```

Para um arquivo local, use:

```powershell
$definition = New-AzureRmPolicyDefinition `
    -Name denyCoolTiering `
    -Description "Deny cool access tiering for storage" `
    -Policy "c:\policies\coolAccessTier.json"
```

Para criar uma definição de política com uma regra embutida, use:

```powershell
$definition = New-AzureRmPolicyDefinition -Name denyCoolTiering -Description "Deny cool access tiering for storage" -Policy '{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "field": "kind",
        "equals": "BlobStorage"
      },
      {
        "not": {
          "field": "Microsoft.Storage/storageAccounts/accessTier",
          "equals": "cool"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}'
```            

A saída é armazenada em um objeto `$definition`, que é usado durante a atribuição da política. 

O exemplo a seguir cria uma definição de política que inclui parâmetros:

```powershell
$policy = '{
    "if": {
        "allOf": [
            {
                "field": "type",
                "equals": "Microsoft.Storage/storageAccounts"
            },
            {
                "not": {
                    "field": "location",
                    "in": "[parameters(''allowedLocations'')]"
                }
            }
        ]
    },
    "then": {
        "effect": "Deny"
    }
}'

$parameters = '{
    "allowedLocations": {
        "type": "array",
        "metadata": {
          "description": "The list of locations that can be specified when deploying storage accounts.",
          "strongType": "location",
          "displayName": "Allowed locations"
        }
    }
}' 

$definition = New-AzureRmPolicyDefinition -Name storageLocations -Description "Policy to specify locations for storage accounts." -Policy $policy -Parameter $parameters 
```

### <a name="assign-policy"></a>Atribuir política

Aplique a política no escopo desejado usando o cmdlet `New-AzureRmPolicyAssignment`. O exemplo a seguir atribui a política a um grupo de recursos.

```powershell
$rg = Get-AzureRmResourceGroup -Name "ExampleGroup"
New-AzureRMPolicyAssignment -Name accessTierAssignment -Scope $rg.ResourceId -PolicyDefinition $definition
```

Para atribuir uma política que requer parâmetros, crie e objete com esses valores. O seguinte exemplo recupera uma política interna e passa em valores de parâmetros:

```powershell
$rg = Get-AzureRmResourceGroup -Name "ExampleGroup"
$definition = Get-AzureRmPolicyDefinition -Id /providers/Microsoft.Authorization/policyDefinitions/e5662a6-4747-49cd-b67b-bf8b01975c4c
$array = @("West US", "West US 2")
$param = @{"listOfAllowedLocations"=$array}
New-AzureRMPolicyAssignment -Name locationAssignment -Scope $rg.ResourceId -PolicyDefinition $definition -PolicyParameterObject $param
```

### <a name="view-policy-assignment"></a>Exibir atribuição de política

Para obter uma atribuição de política específica, use:

```powershell
$rg = Get-AzureRmResourceGroup -Name "ExampleGroup"
(Get-AzureRmPolicyAssignment -Name accessTierAssignment -Scope $rg.ResourceId
```

Para exibir a regra de política de uma definição de política, use:

```powershell
(Get-AzureRmPolicyDefinition -Name coolAccessTier).Properties.policyRule | ConvertTo-Json
```

### <a name="remove-policy-assignment"></a>Remover atribuição de política 

Para remover uma atribuição de política, use:

```powershell
Remove-AzureRmPolicyAssignment -Name regionPolicyAssignment -Scope /subscriptions/{subscription-id}/resourceGroups/{resource-group-name}
```

## <a name="azure-cli"></a>CLI do Azure

### <a name="view-policy-definitions"></a>Exibir definições de políticas
Para visualizar todas as definições de política em sua assinatura, utilize o seguinte comando:

```azurecli
az policy definition list
```

Ele retorna todas as definições de política disponíveis, incluindo políticas internas. Cada política é retornada no seguinte formato:

```azurecli
{                                                            
  "description": "This policy enables you to restrict the locations your organization can specify when deploying resources. Use to enforce your geo-compliance requirements.",                      
  "displayName": "Allowed locations",
  "id": "/providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c",
  "name": "e56962a6-4747-49cd-b67b-bf8b01975c4c",
  "policyRule": {
    "if": {
      "not": {
        "field": "location",
        "in": "[parameters('listOfAllowedLocations')]"
      }
    },
    "then": {
      "effect": "Deny"
    }
  },
  "policyType": "BuiltIn"
}
```

Antes de continuar a criar uma definição de política, observe as políticas internas. Se você encontrar uma política interna que aplica os limites necessários, você poderá ignorar a criação de uma definição de política. Em vez disso, atribua a política interna ao escopo desejado.

### <a name="create-policy-definition"></a>Criar definição de política

Você pode criar uma definição de política usando a CLI do Azure com o comando de definição de política.

Para criar uma definição de política com uma regra embutida, use:

```azurecli
az policy definition create --name denyCoolTiering --description "Deny cool access tiering for storage" --rules '{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "field": "kind",
        "equals": "BlobStorage"
      },
      {
        "not": {
          "field": "Microsoft.Storage/storageAccounts/accessTier",
          "equals": "cool"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}'    
```

### <a name="assign-policy"></a>Atribuir política

Você pode aplicar a política para o escopo desejado usando o comando de atribuição de política. O exemplo a seguir atribui uma política a um grupo de recursos.

```azurecli
az policy assignment create --name coolAccessTierAssignment --policy coolAccessTier --scope /subscriptions/{subscription-id}/resourceGroups/{resource-group-name}
```

### <a name="view-policy-assignment"></a>Exibir atribuição de política

Para exibir uma atribuição de política, forneça o nome da atribuição de política e o escopo:

```azurecli
az policy assignment show --name coolAccessTierAssignment --scope "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}"
```

### <a name="remove-policy-assignment"></a>Remover atribuição de política 

Para remover uma atribuição de política, use:

```azurecli
az policy assignment delete --name coolAccessTier --scope /subscriptions/{subscription-id}/resourceGroups/{resource-group-name}
```

## <a name="next-steps"></a>Próximas etapas
* Para obter orientação sobre como as empresas podem usar o Resource Manager para gerenciar assinaturas de forma eficaz, consulte [Azure enterprise scaffold – controle de assinatura prescritivas](resource-manager-subscription-governance.md).

