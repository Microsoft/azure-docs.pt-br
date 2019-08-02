---
title: Gerenciar o acesso a recursos do Azure usando modelos do Azure Resource Manager e RBAC | Microsoft Docs
description: Saiba como gerenciar o acesso a recursos do Azure para usuários, grupos e aplicativos usando RBAC (controle de acesso baseado em função) e modelo do Azure Resource Manager.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/19/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: e6511ff84c251577a5ff483f892387ab7d3d4d41
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68360471"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-azure-resource-manager-templates"></a>Gerenciar o acesso a recursos do Azure usando modelos do Azure Resource Manager e RBAC

O [RBAC (controle de acesso baseado em função)](overview.md) serve para gerenciar o acesso aos recursos do Azure. Além de usar o Azure PowerShell ou a CLI do Azure, você pode gerenciar o acesso aos recursos do Azure usando RBAC e [modelos do Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md). Os modelos poderão ser úteis se você precisar implantar recursos de maneira consistente e repetida. Este artigo descreve como gerenciar o acesso usando modelos e RBAC.

## <a name="assign-role-to-resource-group-or-subscription"></a>Atribuir função ao grupo de recursos ou à assinatura

No RBAC, para conceder acesso, você cria uma atribuição de função. O modelo a seguir demonstra:
- Como atribuir uma função a um usuário, grupo ou aplicativo no escopo do grupo de recursos ou da assinatura
- Como especificar as funções de Proprietário, Colaborador e Leitor como um parâmetro

Para usar o modelo, você deve especificar as seguintes entradas:
- O identificador exclusivo de um usuário, grupo ou aplicativo para o qual atribuir a função
- A função a atribuir
- Um identificador exclusivo que será usado para a atribuição de função ou você poderá usar o identificador padrão

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "principalId": {
      "type": "string",
      "metadata": {
        "description": "The principal to assign the role to"
      }
    },
    "builtInRoleType": {
      "type": "string",
      "allowedValues": [
        "Owner",
        "Contributor",
        "Reader"
      ],
      "metadata": {
        "description": "Built-in role to assign"
      }
    },
    "roleNameGuid": {
      "type": "string",
      "defaultValue": "[newGuid()]",
      "metadata": {
        "description": "A new GUID used to identify the role assignment"
      }
    }
  },
  "variables": {
    "Owner": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', '8e3af657-a8ff-443c-a75c-2fe8c4bcb635')]",
    "Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
    "Reader": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
  },
  "resources": [
    {
      "type": "Microsoft.Authorization/roleAssignments",
      "apiVersion": "2018-09-01-preview",
      "name": "[parameters('roleNameGuid')]",
      "properties": {
        "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
        "principalId": "[parameters('principalId')]"
      }
    }
  ]
}
```

Veja a seguir um exemplo de uma atribuição de função de leitor para um usuário para um grupo de recursos após a implantação do modelo.

![Atribuição de função usando um modelo](./media/role-assignments-template/role-assignment-template.png)

O escopo da atribuição de função é determinado do nível da implantação. Os comandos de implantação do grupo de recursos e do nível de assinatura são mostrados neste artigo.

## <a name="assign-role-to-resource"></a>Atribuir função ao recurso

Se você precisar criar uma atribuição de função no nível de um recurso, o formato da atribuição de função será diferente. Você fornece o namespace do provedor de recursos e o tipo de recurso do recurso ao qual atribuir a função. Você também inclui o nome do recurso no nome da atribuição de função.

Para o tipo e o nome da atribuição de função, use o seguinte formato:

```json
"type": "{resource-provider-namespace}/{resource-type}/providers/roleAssignments",
"name": "{resource-name}/Microsoft.Authorization/{role-assign-GUID}"
```

O modelo a seguir implanta uma conta de armazenamento e atribui uma função a ela. Você o implanta com os comandos do grupo de recursos.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "principalId": {
      "type": "string",
      "metadata": {
        "description": "The principal to assign the role to"
      }
    },
    "builtInRoleType": {
      "type": "string",
      "allowedValues": [
        "Owner",
        "Contributor",
        "Reader"
      ],
      "metadata": {
        "description": "Built-in role to assign"
      }
    },
    "roleNameGuid": {
      "type": "string",
      "defaultValue": "[newGuid()]",
      "metadata": {
        "description": "A new GUID used to identify the role assignment"
      }
    },
    "location": {
        "type": "string",
        "defaultValue": "[resourceGroup().location]"
    }
  },
  "variables": {
    "Owner": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', '8e3af657-a8ff-443c-a75c-2fe8c4bcb635')]",
    "Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
    "Reader": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]",
    "storageName": "[concat('storage', uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "apiVersion": "2019-04-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageName')]",
      "location": "[parameters('location')]",
      "sku": {
          "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {}
    },
    {
      "type": "Microsoft.Storage/storageAccounts/providers/roleAssignments",
      "apiVersion": "2018-09-01-preview",
      "name": "[concat(variables('storageName'), '/Microsoft.Authorization/', parameters('roleNameGuid'))]",
      "dependsOn": [
          "[variables('storageName')]"
      ],
      "properties": {
        "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
        "principalId": "[parameters('principalId')]"
      }
    }
  ]
}
```

Veja a seguir um exemplo de uma atribuição de função de colaborador para um usuário para uma conta de armazenamento após a implantação do modelo.

![Atribuição de função usando um modelo](./media/role-assignments-template/role-assignment-template-resource.png)

## <a name="deploy-template-using-azure-powershell"></a>Implantar o modelo usando o Azure PowerShell

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

Para implantar o modelo anterior em um grupo de recursos ou uma assinatura usando Azure PowerShell, siga estas etapas.

1. Crie um novo arquivo chamado rbac-rg.json rbac e copie o modelo anterior.

1. Entre no [Azure PowerShell](/powershell/azure/authenticate-azureps).

1. Obtenha o identificador exclusivo de um usuário, grupo ou aplicativo. Por exemplo, você pode usar o comando [Get-AzADUser](/powershell/module/az.resources/get-azaduser) para listar os usuários do Azure AD.

    ```azurepowershell
    $userid = (Get-AzADUser -DisplayName "{name}").id
    ```

1. O modelo gera um valor padrão para o GUID que é usado para identificar a atribuição de função. Se você precisar especificar um GUID específico, passe esse valor para para o parâmetro roleNameGuid. O identificador tem o formato: `11111111-1111-1111-1111-111111111111`

Para atribuir a função no nível de um recurso ou grupo de recursos, siga estas etapas:

1. Crie um grupo de recursos de exemplo.

    ```azurepowershell
    New-AzResourceGroup -Name ExampleGroup -Location "Central US"
    ```

1. Use o comando [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) para iniciar a implantação.

    ```azurepowershell
    New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-rg.json -principalId $userid -builtInRoleType Reader
    ```

    A seguir, é mostrado um exemplo da saída.

    ```Output
    PS /home/user> New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-rg.json -principalId $userid -builtInRoleType Reader
    
    DeploymentName          : rbac-rg
    ResourceGroupName       : ExampleGroup
    ProvisioningState       : Succeeded
    Timestamp               : 7/17/2018 7:46:32 PM
    Mode                    : Incremental
    TemplateLink            :
    Parameters              :
                              Name             Type                       Value
                              ===============  =========================  ==========
                              principalId      String                     22222222-2222-2222-2222-222222222222
                              builtInRoleType  String                     Reader
                              roleNameGuid     String                     11111111-1111-1111-1111-111111111111
    
    Outputs                 :
    DeploymentDebugLogLevel :
    ```

Para atribuir a função no nível de uma assinatura, use o comando [New-AzDeployment](/powershell/module/az.resources/new-azdeployment) e especifique um local para a implantação.

```azurepowershell
New-AzDeployment -Location centralus -TemplateFile rbac-rg.json -principalId $userid -builtInRoleType Reader
```

Ele tem uma saída semelhante ao comando de implantação para grupos de recursos.

## <a name="deploy-template-using-the-azure-cli"></a>Implantar o modelo usando a CLI do Azure

Para implantar o modelo anterior usando o CLI do Azure para um grupo de recursos ou uma assinatura, siga estas etapas.

1. Crie um novo arquivo chamado rbac-rg.json rbac e copie o modelo anterior.

1. Entre na [CLI do Azure](/cli/azure/authenticate-azure-cli).

1. Obtenha o identificador exclusivo de um usuário, grupo ou aplicativo. Por exemplo, você pode usar o comando [AZ ad User show](/cli/azure/ad/user#az-ad-user-show) para mostrar um usuário do Azure AD.

    ```azurecli
    userid=$(az ad user show --upn-or-object-id "{email}" --query objectId --output tsv)
    ```

1. O modelo gera um valor padrão para o GUID que é usado para identificar a atribuição de função. Se você precisar especificar um GUID específico, passe esse valor para para o parâmetro roleNameGuid. O identificador tem o formato: `11111111-1111-1111-1111-111111111111`

Para atribuir a função no nível de um recurso ou grupo de recursos, siga estas etapas:

1. Crie um grupo de recursos de exemplo.

    ```azurecli
    az group create --name ExampleGroup --location "Central US"
    ```

1. Use o comando [az group deployment create](/cli/azure/group/deployment#az-group-deployment-create) para iniciar a implantação.

    ```azurecli
    az group deployment create --resource-group ExampleGroup --template-file rbac-rg.json --parameters principalId=$userid builtInRoleType=Reader
    ```

    A seguir, é mostrado um exemplo da saída.

    ```Output
    C:\Azure\Templates>az group deployment create --resource-group ExampleGroup --template-file rbac-rg.json --parameters principalId=$userid builtInRoleType=Reader
    
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ExampleGroup/providers/Microsoft.Resources/deployments/rbac-rg",
      "name": "rbac-rg",
      "properties": {
        "additionalProperties": {
          "duration": "PT9.5323924S",
          "outputResources": [
            {
              "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ExampleGroup/providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111",
              "resourceGroup": "ExampleGroup"
            }
          ],
          "templateHash": "0000000000000000000"
        },
        "correlationId": "33333333-3333-3333-3333-333333333333",
        "debugSetting": null,
        "dependencies": [],
        "mode": "Incremental",
        "outputs": null,
        "parameters": {
          "builtInRoleType": {
            "type": "String",
            "value": "Reader"
          },
          "principalId": {
            "type": "String",
            "value": "22222222-2222-2222-2222-222222222222"
          },
          "roleNameGuid": {
            "type": "String",
            "value": "11111111-1111-1111-1111-111111111111"
          }
        },
        "parametersLink": null,
        "providers": [
          {
            "id": null,
            "namespace": "Microsoft.Authorization",
            "registrationState": null,
            "resourceTypes": [
              {
                "aliases": null,
                "apiVersions": null,
                "locations": [
                  null
                ],
                "properties": null,
                "resourceType": "roleAssignments"
              }
            ]
          }
        ],
        "provisioningState": "Succeeded",
        "template": null,
        "templateLink": null,
        "timestamp": "2018-07-17T19:00:31.830904+00:00"
      },
      "resourceGroup": "ExampleGroup"
    }
    ```

Para atribuir a função no nível de uma assinatura, use o comando [AZ Deployment Create](/cli/azure/deployment#az-deployment-create) e especifique um local para a implantação.

```azurecli
az deployment create --location centralus --template-file rbac-rg.json --parameters principalId=$userid builtInRoleType=Reader
```

Ele tem uma saída semelhante ao comando de implantação para grupos de recursos.

## <a name="next-steps"></a>Próximas etapas

- [Início Rápido: Criar e implantar modelos do Azure Resource Manager usando o portal do Azure](../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md)
- [Noções básicas de estrutura e sintaxe dos modelos do Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md)
- [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/?term=rbac)
