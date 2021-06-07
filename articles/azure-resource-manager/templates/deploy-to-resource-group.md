---
title: Implantar recursos em grupos de recursos
description: Descreve como implantar recursos em um modelo de Azure Resource Manager. Ele mostra como direcionar mais de um grupo de recursos.
ms.topic: conceptual
ms.date: 01/13/2021
ms.openlocfilehash: 1d636be9ffab5a4398e3e12867e601ce6df382bf
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104889783"
---
# <a name="resource-group-deployments-with-arm-templates"></a>Implantações de grupo de recursos com modelos ARM

Este artigo descreve como fazer o escopo de sua implantação em um grupo de recursos. Você usa um modelo de Azure Resource Manager (modelo ARM) para a implantação. O artigo também mostra como expandir o escopo além do grupo de recursos na operação de implantação.

## <a name="supported-resources"></a>Recursos compatíveis

A maioria dos recursos pode ser implantada em um grupo de recursos. Para obter uma lista de recursos disponíveis, consulte [referência de modelo ARM](/azure/templates).

## <a name="schema"></a>Esquema

Para modelos, use o seguinte esquema:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    ...
}
```

Para arquivos de parâmetros, use:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    ...
}
```

## <a name="deployment-commands"></a>Comandos de implantação

Para implantar em um grupo de recursos, use os comandos de implantação do grupo de recursos.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para CLI do Azure, use o [grupo de implantação AZ Create](/cli/azure/deployment/group#az_deployment_group_create). O exemplo a seguir implanta um modelo para criar um grupo de recursos:

```azurecli-interactive
az deployment group create \
  --name demoRGDeployment \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
  --parameters storageAccountType=Standard_GRS
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para o comando de implantação do PowerShell, use [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment). O exemplo a seguir implanta um modelo para criar um grupo de recursos:

```azurepowershell-interactive
New-AzResourceGroupDeployment `
  -Name demoRGDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
  -storageAccountType Standard_GRS `
```

---

Para obter informações mais detalhadas sobre os comandos de implantação e opções para implantar modelos de ARM, consulte:

* [Implantar recursos com modelos ARM e portal do Azure](deploy-portal.md)
* [Implantar recursos com modelos do Resource Manager e a CLI do Azure](deploy-cli.md)
* [Implantar recursos com modelos do Resource Manager e o Azure PowerShell](deploy-powershell.md)
* [Implantar recursos com modelos ARM e Azure Resource Manager API REST](deploy-rest.md)
* [Usar um botão de implantação para implantar modelos do repositório GitHub](deploy-to-azure-button.md)
* [Implantar modelos de ARM de Cloud Shell](deploy-cloud-shell.md)

## <a name="deployment-scopes"></a>Escopos de implantação

Ao implantar em um grupo de recursos, você pode implantar recursos em:

* o grupo de recursos de destino da operação
* outros grupos de recursos na mesma assinatura ou outras assinaturas
* qualquer assinatura no locatário
* o locatário para o grupo de recursos

Um [recurso de extensão](scope-extension-resources.md) pode ser definido como escopo para um destino diferente do destino de implantação.

O usuário que está implantando o modelo deve ter acesso ao escopo especificado.

Esta seção mostra como especificar escopos diferentes. Você pode combinar esses escopos diferentes em um único modelo.

### <a name="scope-to-target-resource-group"></a>Escopo para o grupo de recursos de destino

Para implantar recursos no recurso de destino, adicione esses recursos à seção de recursos do modelo.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/default-rg.json" highlight="5":::

Para obter um modelo de exemplo, consulte [implantar no grupo de recursos de destino](#deploy-to-target-resource-group).

### <a name="scope-to-resource-group-in-same-subscription"></a>Escopo para o grupo de recursos na mesma assinatura

Para implantar recursos em um grupo de recursos diferente na mesma assinatura, adicione uma implantação aninhada e inclua a `resourceGroup` propriedade. Se você não especificar a ID da assinatura ou o grupo de recursos, serão usados a assinatura e o grupo de recursos do modelo pai. Todos os grupos de recursos devem existir antes da execução da implantação.

No exemplo a seguir, a implantação aninhada tem como destino um grupo de recursos denominado `demoResourceGroup` .

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/same-sub-to-resource-group.json" highlight="9,13":::

Para obter um modelo de exemplo, consulte [implantar em vários grupos de recursos](#deploy-to-multiple-resource-groups).

### <a name="scope-to-resource-group-in-different-subscription"></a>Escopo para o grupo de recursos em uma assinatura diferente

Para implantar recursos em um grupo de recursos em uma assinatura diferente, adicione uma implantação aninhada e inclua as `subscriptionId` `resourceGroup` Propriedades e. No exemplo a seguir, a implantação aninhada tem como destino um grupo de recursos denominado `demoResourceGroup` .

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/different-sub-to-resource-group.json" highlight="9,10,14":::

Para obter um modelo de exemplo, consulte [implantar em vários grupos de recursos](#deploy-to-multiple-resource-groups).

### <a name="scope-to-subscription"></a>Escopo da assinatura

Para implantar recursos em uma assinatura, adicione uma implantação aninhada e inclua a `subscriptionId` propriedade. A assinatura pode ser a assinatura para o grupo de recursos de destino ou qualquer outra assinatura no locatário. Além disso, defina a `location` propriedade para a implantação aninhada.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/resource-group-to-subscription.json" highlight="9,10,14":::

Para obter um modelo de exemplo, consulte [Criar grupo de recursos](#create-resource-group).

### <a name="scope-to-tenant"></a>Escopo para o locatário

Para criar recursos no locatário, defina `scope` como `/` . O usuário que está implantando o modelo deve ter o [acesso necessário para implantar no locatário](deploy-to-tenant.md#required-access).

Para usar uma implantação aninhada, defina `scope` e `location` .

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/resource-group-to-tenant.json" highlight="9,10,14":::

Ou, você pode definir o escopo como `/` para alguns tipos de recursos, como grupos de gerenciamento.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/resource-group-create-mg.json" highlight="12,15":::

Para obter mais informações, consulte [grupo de gerenciamento](deploy-to-management-group.md#management-group).

## <a name="deploy-to-target-resource-group"></a>Implantar no grupo de recursos de destino

Para implantar recursos no grupo de recursos de destino, defina esses recursos na `resources` seção do modelo. O modelo a seguir cria uma conta de armazenamento no grupo de recursos especificado na operação de implantação.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-outputs/azuredeploy.json":::

## <a name="deploy-to-multiple-resource-groups"></a>Implantar em vários grupos de recursos

Você pode implantar em mais de um grupo de recursos em um único modelo ARM. Para usar como destino um grupo de recursos diferente daquele para o modelo pai, use um [modelo aninhado ou vinculado](linked-templates.md). Dentro do tipo de recurso de implantação, especifique valores para a ID de assinatura e para o grupo de recursos onde você deseja implantar o modelo aninhado. Os grupos de recursos podem estar presentes em diferentes assinaturas.

> [!NOTE]
> A implantação pode ser feita em **800 grupos de recursos** com uma única implantação. Normalmente, essa limitação significa que você pode implantar em um grupo de recursos especificado para o modelo pai e até 799 grupos de recursos em implantações aninhadas ou vinculadas. No entanto, se o modelo pai contém apenas os modelos aninhados ou vinculados e não implanta, ele mesmo, todos os recursos, você pode incluir até 800 grupos de recursos em implantações aninhadas ou vinculadas.

O exemplo a seguir implanta duas contas de armazenamento. A primeira conta de armazenamento é implantada no grupo de recursos especificado na operação de implantação. A segunda conta de armazenamento é implantada no grupo de recursos especificado nos parâmetros `secondResourceGroup` e `secondSubscriptionID`:

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/crosssubscription.json":::

Se você definir `resourceGroup` como o nome de um grupo de recursos que não existe, a implantação falhará.

Para testar o modelo anterior e ver os resultados, use o PowerShell ou a CLI do Azure.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para implantar duas contas de armazenamento em dois grupos de recursos na **mesma assinatura**, use:

```azurepowershell-interactive
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

New-AzResourceGroup -Name $firstRG -Location southcentralus
New-AzResourceGroup -Name $secondRG -Location eastus

New-AzResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation eastus
```

Para implantar duas contas de armazenamento em **duas assinaturas**, use:

```azurepowershell-interactive
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

$firstSub = "<first-subscription-id>"
$secondSub = "<second-subscription-id>"

Set-AzContext -Subscription $secondSub
New-AzResourceGroup -Name $secondRG -Location eastus

Set-AzContext -Subscription $firstSub
New-AzResourceGroup -Name $firstRG -Location southcentralus

New-AzResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation eastus `
  -secondSubscriptionID $secondSub
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para implantar duas contas de armazenamento em dois grupos de recursos na **mesma assinatura**, use:

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

az group create --name $firstRG --location southcentralus
az group create --name $secondRG --location eastus
az deployment group create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=tfstorage secondResourceGroup=$secondRG secondStorageLocation=eastus
```

Para implantar duas contas de armazenamento em **duas assinaturas**, use:

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

firstSub="<first-subscription-id>"
secondSub="<second-subscription-id>"

az account set --subscription $secondSub
az group create --name $secondRG --location eastus

az account set --subscription $firstSub
az group create --name $firstRG --location southcentralus

az deployment group create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=storage secondResourceGroup=$secondRG secondStorageLocation=eastus secondSubscriptionID=$secondSub
```

---

## <a name="create-resource-group"></a>Criar grupo de recursos

De uma implantação de grupo de recursos, você pode alternar para o nível de uma assinatura e criar um grupo de recursos. O modelo a seguir implanta uma conta de armazenamento no grupo de recursos de destino e cria um novo grupo de recursos na assinatura especificada.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storagePrefix": {
            "type": "string",
            "maxLength": 11
        },
        "newResourceGroupName": {
            "type": "string"
        },
        "nestedSubscriptionID": {
            "type": "string"
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {
        "storageName": "[concat(parameters('storagePrefix'), uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "[variables('storageName')]",
            "location": "[parameters('location')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {
            }
        },
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2020-06-01",
            "name": "demoSubDeployment",
            "location": "westus",
            "subscriptionId": "[parameters('nestedSubscriptionID')]",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                        {
                            "type": "Microsoft.Resources/resourceGroups",
                            "apiVersion": "2020-10-01",
                            "name": "[parameters('newResourceGroupName')]",
                            "location": "[parameters('location')]",
                            "properties": {}
                        }
                    ],
                    "outputs": {}
                }
            }
        }
    ]
}
```

## <a name="next-steps"></a>Próximas etapas

* Para obter um exemplo de implantação de configurações de workspace para a Central de Segurança do Azure, consulte [deployASCwithWorkspaceSettings.json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).
