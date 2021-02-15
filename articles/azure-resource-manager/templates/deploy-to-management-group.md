---
title: Implantar recursos no grupo de gerenciamento
description: Descreve como implantar recursos no escopo do grupo de gerenciamento em um modelo de Azure Resource Manager.
ms.topic: conceptual
ms.date: 01/13/2021
ms.openlocfilehash: a203dd2c52bdc889452a6755fb025c7ed5721a59
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99491630"
---
# <a name="management-group-deployments-with-arm-templates"></a>Implantações de grupo de gerenciamento com modelos ARM

À medida que sua organização amadureceu, você pode implantar um modelo de Azure Resource Manager (modelo ARM) para criar recursos no nível do grupo de gerenciamento. Por exemplo, talvez seja necessário definir e atribuir [políticas](../../governance/policy/overview.md) ou [controle de acesso baseado em função do Azure (RBAC do Azure)](../../role-based-access-control/overview.md) para um grupo de gerenciamento. Com os modelos de nível de grupo de gerenciamento, você pode aplicar políticas declarativamente e atribuir funções no nível do grupo de gerenciamento.

## <a name="supported-resources"></a>Recursos compatíveis

Nem todos os tipos de recursos podem ser implantados no nível do grupo de gerenciamento. Esta seção lista os tipos de recursos com suporte.

Para plantas do Azure, use:

* [artifacts](/azure/templates/microsoft.blueprint/blueprints/artifacts)
* [blueprints](/azure/templates/microsoft.blueprint/blueprints)
* [blueprintAssignments](/azure/templates/microsoft.blueprint/blueprintassignments)
* [versões](/azure/templates/microsoft.blueprint/blueprints/versions)

Para políticas do Azure, use:

* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [remediations](/azure/templates/microsoft.policyinsights/remediations)

Para o controle de acesso baseado em função do Azure (RBAC do Azure), use:

* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

Para modelos aninhados que são implantados em assinaturas ou grupos de recursos, use:

* [implantações](/azure/templates/microsoft.resources/deployments)

Para gerenciar seus recursos, use:

* [marcas](/azure/templates/microsoft.resources/tags)

Os grupos de gerenciamento são recursos de nível de locatário. No entanto, você pode criar grupos de gerenciamento em uma implantação de grupo de gerenciamento definindo o escopo do novo grupo de gerenciamento para o locatário. Consulte [grupo de gerenciamento](#management-group).

## <a name="schema"></a>Esquema

O esquema usado para implantações de grupo de gerenciamento é diferente do esquema para implantações de grupo de recursos.

Para modelos, use:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    ...
}
```

O esquema para um arquivo de parâmetro é o mesmo para todos os escopos de implantação. Para arquivos de parâmetros, use:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    ...
}
```

## <a name="deployment-commands"></a>Comandos de implantação

Para implantar em um grupo de gerenciamento, use os comandos de implantação do grupo de gerenciamento.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para CLI do Azure, use [AZ Deployment mg Create](/cli/azure/deployment/mg#az-deployment-mg-create):

```azurecli-interactive
az deployment mg create \
  --name demoMGDeployment \
  --location WestUS \
  --management-group-id myMG \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para Azure PowerShell, use [New-AzManagementGroupDeployment](/powershell/module/az.resources/new-azmanagementgroupdeployment).

```azurepowershell-interactive
New-AzManagementGroupDeployment `
  -Name demoMGDeployment `
  -Location "West US" `
  -ManagementGroupId "myMG" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json"
```

---

Para obter informações mais detalhadas sobre os comandos de implantação e opções para implantar modelos de ARM, consulte:

* [Implantar recursos com modelos ARM e portal do Azure](deploy-portal.md)
* [Implantar recursos com modelos do Resource Manager e a CLI do Azure](deploy-cli.md)
* [Implantar recursos com modelos do Resource Manager e o Azure PowerShell](deploy-powershell.md)
* [Implantar recursos com modelos ARM e Azure Resource Manager API REST](deploy-rest.md)
* [Usar um botão de implantação para implantar modelos do repositório GitHub](deploy-to-azure-button.md)
* [Implantar modelos de ARM de Cloud Shell](deploy-cloud-shell.md)

## <a name="deployment-location-and-name"></a>Nome e local da implantação

Para implantações no nível do grupo de gerenciamento, você deve fornecer um local para a implantação. O local da implantação é separado do local dos recursos que você implanta. O local de implantação especifica onde armazenar os dados de implantação. As implantações de [assinatura](deploy-to-subscription.md) e [locatário](deploy-to-tenant.md) também exigem um local. Para implantações de [grupo de recursos](deploy-to-resource-group.md) , o local do grupo de recursos é usado para armazenar os dados de implantação.

Você pode fornecer um nome da implantação ou usar o nome da implantação padrão. O nome padrão é o nome do arquivo de modelo. Por exemplo, implantar um modelo chamado _azuredeploy.json_ cria um nome de implantação padrão de **azuredeploy**.

O local não pode ser alterado para cada nome de implantação. Você não pode criar uma implantação em um local quando há uma implantação existente com o mesmo nome em um local diferente. Por exemplo, se você criar uma implantação de grupo de gerenciamento com o nome **deployment1** em **centralus**, não será possível criar outra implantação mais tarde com o nome **deployment1** , mas um local de **westus**. Se você receber o código de erro `InvalidDeploymentLocation`, use um nome diferente ou o mesmo local que a implantação anterior para esse nome.

## <a name="deployment-scopes"></a>Escopos de implantação

Ao implantar em um grupo de gerenciamento, você pode implantar recursos em:

* o grupo de gerenciamento de destino da operação
* outro grupo de gerenciamento no locatário
* assinaturas no grupo de gerenciamento
* grupos de recursos no grupo de gerenciamento
* o locatário para o grupo de recursos

Um [recurso de extensão](scope-extension-resources.md) pode ser definido como escopo para um destino diferente do destino de implantação.

O usuário que está implantando o modelo deve ter acesso ao escopo especificado.

Esta seção mostra como especificar escopos diferentes. Você pode combinar esses escopos diferentes em um único modelo.

### <a name="scope-to-target-management-group"></a>Escopo do grupo de gerenciamento de destino

Os recursos definidos na seção de recursos do modelo são aplicados ao grupo de gerenciamento do comando de implantação.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/default-mg.json" highlight="5":::

### <a name="scope-to-another-management-group"></a>Escopo para outro grupo de gerenciamento

Para direcionar outro grupo de gerenciamento, adicione uma implantação aninhada e especifique a `scope` propriedade. Defina a `scope` propriedade como um valor no formato `Microsoft.Management/managementGroups/<mg-name>` .

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/scope-mg.json" highlight="10,17,18,22":::

### <a name="scope-to-subscription"></a>Escopo da assinatura

Você também pode direcionar as assinaturas dentro de um grupo de gerenciamento. O usuário que está implantando o modelo deve ter acesso ao escopo especificado.

Para direcionar uma assinatura dentro do grupo de gerenciamento, use uma implantação aninhada e a `subscriptionId` propriedade.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/mg-to-subscription.json" highlight="9,10,18":::

### <a name="scope-to-resource-group"></a>Escopo para o grupo de recursos

Você também pode direcionar grupos de recursos dentro do grupo de gerenciamento. O usuário que está implantando o modelo deve ter acesso ao escopo especificado.

Para direcionar um grupo de recursos dentro do grupo de gerenciamento, use uma implantação aninhada. Definir as propriedades `subscriptionId` e `resourceGroup`. Não defina um local para a implantação aninhada porque ela é implantada no local do grupo de recursos.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/mg-to-resource-group.json" highlight="9,10,18":::

Para usar uma implantação de grupo de gerenciamento para criar um grupo de recursos em uma assinatura e implantar uma conta de armazenamento para esse grupo de recursos, consulte [implantar na assinatura e no grupo de recursos](#deploy-to-subscription-and-resource-group).

### <a name="scope-to-tenant"></a>Escopo para o locatário

Para criar recursos no locatário, defina `scope` como `/` . O usuário que está implantando o modelo deve ter o [acesso necessário para implantar no locatário](deploy-to-tenant.md#required-access).

Para usar uma implantação aninhada, defina `scope` e `location` .

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/management-group-to-tenant.json" highlight="9,10,14":::

Ou, você pode definir o escopo como `/` para alguns tipos de recursos, como grupos de gerenciamento. A criação de um novo grupo de gerenciamento é descrita na próxima seção.

## <a name="management-group"></a>Grupo de gerenciamento

Para criar um grupo de gerenciamento em uma implantação de grupo de gerenciamento, você deve definir o escopo como `/` para o grupo de gerenciamento.

O exemplo a seguir cria um novo grupo de gerenciamento no grupo de gerenciamento raiz.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/management-group-create-mg.json" highlight="12,15":::

O exemplo a seguir cria um novo grupo de gerenciamento no grupo de gerenciamento especificado como o pai. Observe que o escopo está definido como `/` .

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "mgName": {
            "type": "string",
            "defaultValue": "[concat('mg-', uniqueString(newGuid()))]"
        },
        "parentMG": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('mgName')]",
            "type": "Microsoft.Management/managementGroups",
            "apiVersion": "2020-05-01",
            "scope": "/",
            "location": "eastus",
            "properties": {
                "details": {
                    "parent": {
                        "id": "[tenantResourceId('Microsoft.Management/managementGroups', parameters('parentMG'))]"
                    }
                }
            }
        }
    ],
    "outputs": {
        "output": {
            "type": "string",
            "value": "[parameters('mgName')]"
        }
    }
}
```

## <a name="azure-policy"></a>Azure Policy

As definições de política personalizadas que são implantadas no grupo de gerenciamento são extensões do grupo de gerenciamento. Para obter a ID de uma definição de política personalizada, use a função [extensionResourceId ()](template-functions-resource.md#extensionresourceid) . As definições de política internas são recursos de nível de locatário. Para obter a ID de uma definição de política interna, use a função [tenantResourceId ()](template-functions-resource.md#tenantresourceid) .

O exemplo a seguir mostra como [definir](../../governance/policy/concepts/definition-structure.md) uma política no nível do grupo de gerenciamento e atribuí-la.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "targetMG": {
            "type": "string",
            "metadata": {
                "description": "Target Management Group"
            }
        },
        "allowedLocations": {
            "type": "array",
            "defaultValue": [
                "australiaeast",
                "australiasoutheast",
                "australiacentral"
            ],
            "metadata": {
                "description": "An array of the allowed locations, all other locations will be denied by the created policy."
            }
        }
    },
    "variables": {
        "mgScope": "[tenantResourceId('Microsoft.Management/managementGroups', parameters('targetMG'))]",
        "policyDefinition": "LocationRestriction"
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/policyDefinitions",
            "name": "[variables('policyDefinition')]",
            "apiVersion": "2019-09-01",
            "properties": {
                "policyType": "Custom",
                "mode": "All",
                "parameters": {
                },
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
        },
        {
            "type": "Microsoft.Authorization/policyAssignments",
            "name": "location-lock",
            "apiVersion": "2019-09-01",
            "dependsOn": [
                "[variables('policyDefinition')]"
            ],
            "properties": {
                "scope": "[variables('mgScope')]",
                "policyDefinitionId": "[extensionResourceId(variables('mgScope'), 'Microsoft.Authorization/policyDefinitions', variables('policyDefinition'))]"
            }
        }
    ]
}
```

## <a name="deploy-to-subscription-and-resource-group"></a>Implantar na assinatura e no grupo de recursos

De uma implantação em nível de grupo de gerenciamento, você pode direcionar uma assinatura dentro do grupo de gerenciamento. O exemplo a seguir cria um grupo de recursos em uma assinatura e implanta uma conta de armazenamento para esse grupo de recursos.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "nestedsubId": {
            "type": "string"
        },
        "nestedRG": {
            "type": "string"
        },
        "storageAccountName": {
            "type": "string"
        },
        "nestedLocation": {
            "type": "string"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2020-06-01",
            "name": "nestedSub",
            "location": "[parameters('nestedLocation')]",
            "subscriptionId": "[parameters('nestedSubId')]",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {
                    },
                    "variables": {
                    },
                    "resources": [
                        {
                            "type": "Microsoft.Resources/resourceGroups",
                            "apiVersion": "2020-06-01",
                            "name": "[parameters('nestedRG')]",
                            "location": "[parameters('nestedLocation')]"
                        }
                    ]
                }
            }
        },
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2020-06-01",
            "name": "nestedRG",
            "subscriptionId": "[parameters('nestedSubId')]",
            "resourceGroup": "[parameters('nestedRG')]",
            "dependsOn": [
                "nestedSub"
            ],
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "resources": [
                        {
                            "type": "Microsoft.Storage/storageAccounts",
                            "apiVersion": "2019-04-01",
                            "name": "[parameters('storageAccountName')]",
                            "location": "[parameters('nestedLocation')]",
                            "kind": "StorageV2",
                            "sku": {
                                "name": "Standard_LRS"
                            }
                        }
                    ]
                }
            }
        }
    ]
}
```

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre como atribuir funções, confira [Adicionar atribuições de função do Azure usando modelos de Azure Resource Manager](../../role-based-access-control/role-assignments-template.md).
* Para obter um exemplo de implantação de configurações de workspace para a Central de Segurança do Azure, consulte [deployASCwithWorkspaceSettings.json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).
* Você também pode implantar modelos no nível de [assinatura](deploy-to-subscription.md) e [nível de locatário](deploy-to-tenant.md).
