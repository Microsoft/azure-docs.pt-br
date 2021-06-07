---
title: Implantação condicional com modelos
description: Descreve como implantar condicionalmente um recurso em um modelo de Azure Resource Manager (modelo ARM).
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: 409d258d7dfe3ed186e5cf97cc0dbe6dc149b849
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101741167"
---
# <a name="conditional-deployment-in-arm-templates"></a>Implantação condicional em modelos do ARM

Às vezes, você precisa, opcionalmente, implantar um recurso em um modelo de Azure Resource Manager (modelo ARM) ou arquivo bicep. Para modelos JSON, use o `condition` elemento para especificar se o recurso é implantado. Para bicep, use a `if` palavra-chave para especificar se o recurso é implantado. O valor da condição é resolvido como verdadeiro ou falso. Quando o valor for true, o recurso será criado. Quando o valor for false, o recurso não será criado. O valor só pode ser aplicado para o recurso inteiro.

> [!NOTE]
> A implantação condicional não é em cascata para [recursos filho](child-resource-name-type.md). Se você quiser implantar condicionalmente um recurso e seus recursos filho, deverá aplicar a mesma condição a cada tipo de recurso.

## <a name="deploy-condition"></a>Condição de implantação

Você pode passar um valor de parâmetro que indica se um recurso é implantado. O exemplo a seguir implanta uma zona DNS condicionalmente.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "deployZone": {
      "type": "bool"
    }
  },
  "functions": [],
  "resources": [
    {
      "condition": "[parameters('deployZone')]",
      "type": "Microsoft.Network/dnsZones",
      "apiVersion": "2018-05-01",
      "name": "myZone",
      "location": "global"
    }
  ]
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param deployZone bool

resource dnsZone 'Microsoft.Network/dnszones@2018-05-01' = if (deployZone) {
  name: 'myZone'
  location: 'global'
}
```

---

Para obter um exemplo mais complexo, consulte [servidor lógico do SQL do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-logical-server).

## <a name="new-or-existing-resource"></a>Recurso novo ou existente

Você pode usar a implantação condicional para criar um novo recurso ou usar um existente. O exemplo a seguir mostra como implantar uma nova conta de armazenamento ou usar uma conta de armazenamento existente.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountName": {
      "type": "string"
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    },
    "newOrExisting": {
      "type": "string",
      "defaultValue": "new",
      "allowedValues": [
        "new",
        "existing"
      ]
    }
  },
  "functions": [],
  "resources": [
    {
      "condition": "[equals(parameters('newOrExisting'), 'new')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-06-01",
      "name": "[parameters('storageAccountName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
      },
      "kind": "StorageV2",
      "properties": {
        "accessTier": "Hot"
      }
    }
  ]
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param storageAccountName string
param location string = resourceGroup().location

@allowed([
  'new'
  'existing'
])
param newOrExisting string = 'new'

resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = if (newOrExisting == 'new') {
  name: storageAccountName
  location: location
  sku: {
    name: 'Standard_LRS'
    tier: 'Standard'
  }
  kind: 'StorageV2'
  properties: {
    accessTier: 'Hot'
  }
}
```

---

Quando o parâmetro `newOrExisting` é definido como **New**, a condição é avaliada como true. A conta de armazenamento é implantada. No entanto, quando `newOrExisting` é definido como **existente**, a condição é avaliada como false e a conta de armazenamento não é implantada.

Para obter um modelo de exemplo completo que usa o elemento `condition`, confira [VM com uma Rede Virtual nova ou existente, um Armazenamento e um IP público](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-new-or-existing-conditions).

## <a name="runtime-functions"></a>Funções de tempo de execução

Se você usar uma função de [referência](template-functions-resource.md#reference) ou de [lista](template-functions-resource.md#list) com um recurso que é implantado condicionalmente, a função será avaliada mesmo que o recurso não seja implantado. Você receberá um erro se a função se referir a um recurso que não existe.

Use a função [If](template-functions-logical.md#if) para certificar-se de que a função é avaliada apenas para condições quando o recurso for implantado. Consulte a [função If](template-functions-logical.md#if) para obter um modelo de exemplo que usa `if` e `reference` com um recurso implantado condicionalmente.

Você define um [recurso como dependente](define-resource-dependency.md) de um recurso condicional exatamente como faria com qualquer outro recurso. Quando um recurso condicional não é implantado, Azure Resource Manager o remove automaticamente das dependências necessárias.

## <a name="complete-mode"></a>Modo completo

Se você implantar um modelo com o [modo completo](deployment-modes.md) e um recurso não for implantado porque `condition` é avaliado como false, o resultado dependerá da versão da API REST usada para implantar o modelo. Se você usar uma versão anterior à 2019-05-10, o recurso **não será excluído**. Com o 2019-05-10 ou posterior, o recurso **é excluído**. As versões mais recentes do Azure PowerShell e CLI do Azure excluir o recurso quando a condição for falsa.

## <a name="next-steps"></a>Próximas etapas

* Para obter um Microsoft Learn módulo que aborda a implantação condicional, consulte [gerenciar implantações de nuvem complexas usando recursos avançados de modelo ARM](/learn/modules/manage-deployments-advanced-arm-template-features/).
* Para obter recomendações sobre como criar modelos, consulte [práticas recomendadas do modelo ARM](template-best-practices.md).
* Para criar várias instâncias de um recurso, consulte [iteração de recursos em modelos ARM](copy-resources.md).
