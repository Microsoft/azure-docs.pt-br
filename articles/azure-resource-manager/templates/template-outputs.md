---
title: Saídas em modelos
description: Descreve como definir valores de saída em um modelo de Azure Resource Manager (modelo ARM).
ms.topic: conceptual
ms.date: 11/24/2020
ms.openlocfilehash: f8f13b6caf063cea79dc71775fb936f406a3ee6c
ms.sourcegitcommit: f6f928180504444470af713c32e7df667c17ac20
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/07/2021
ms.locfileid: "97964007"
---
# <a name="outputs-in-arm-templates"></a>Saídas em modelos ARM

Este artigo descreve como definir valores de saída em seu modelo de Azure Resource Manager (modelo ARM). Você usa `outputs` quando precisa retornar valores dos recursos implantados.

O formato de cada valor de saída deve corresponder a um dos [tipos de dados](template-syntax.md#data-types).

## <a name="define-output-values"></a>Definir valores de saída

O exemplo a seguir mostra como retornar a ID de recurso para um endereço IP público:

```json
"outputs": {
  "resourceID": {
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

## <a name="conditional-output"></a>Saída condicional

Na `outputs` seção, você pode retornar um valor condicionalmente. Normalmente, você usa `condition` o no `outputs` quando tiver [implantado condicionalmente](conditional-resource-deployment.md) um recurso. O exemplo a seguir mostra como retornar condicionalmente a ID de recurso para um endereço IP público com base no fato de um novo ter sido implantado:

```json
"outputs": {
  "resourceID": {
    "condition": "[equals(parameters('publicIpNewOrExisting'), 'new')]",
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

Para obter um exemplo simples de saída condicional, consulte [modelo de saída condicional](https://github.com/bmoore-msft/AzureRM-Samples/blob/master/conditional-output/azuredeploy.json).

## <a name="dynamic-number-of-outputs"></a>Número dinâmico de saídas

Em alguns cenários, você não sabe o número de instâncias de um valor que precisa retornar ao criar o modelo. Você pode retornar um número variável de valores usando o `copy` elemento.

```json
"outputs": {
  "storageEndpoints": {
    "type": "array",
    "copy": {
      "count": "[parameters('storageCount')]",
      "input": "[reference(concat(copyIndex(), variables('baseName'))).primaryEndpoints.blob]"
    }
  }
}
```

Para obter mais informações, consulte [Iteration de saída em modelos ARM](copy-outputs.md).

## <a name="linked-templates"></a>Modelos vinculados

Para recuperar o valor de saída de um modelo vinculado, use a função de [referência](template-functions-resource.md#reference) no modelo pai. A sintaxe no modelo pai é:

```json
"[reference('<deploymentName>').outputs.<propertyName>.value]"
```

Ao obter uma propriedade de saída de um modelo vinculado, o nome da propriedade não pode incluir um traço.

O exemplo a seguir mostra como definir o endereço IP em um balanceador de carga recuperando um valor de um modelo vinculado.

```json
"publicIPAddress": {
  "id": "[reference('linkedTemplate').outputs.resourceID.value]"
}
```

Não é possível usar a `reference` função na seção de saídas de um [modelo aninhado](linked-templates.md#nested-template). Para retornar os valores de um recurso implantado em um modelo aninhado, converta seu modelo aninhado em um modelo vinculado.

## <a name="get-output-values"></a>Obter valores de saída

Quando a implantação for realizada com sucesso, os valores de saída serão retornados automaticamente nos resultados da implantação.

Para obter valores de saída do histórico de implantação, você pode usar o script.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
(Get-AzResourceGroupDeployment `
  -ResourceGroupName <resource-group-name> `
  -Name <deployment-name>).Outputs.resourceID.value
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az deployment group show \
  -g <resource-group-name> \
  -n <deployment-name> \
  --query properties.outputs.resourceID.value
```

---

## <a name="example-templates"></a>Modelos de exemplo

Os exemplos a seguir demonstram cenários para o uso de saídas.

|Modelo  |Descrição  |
|---------|---------|
|[Variáveis de cópia](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) | Cria variáveis complexas e gera os valores. Ele não implanta nenhum recurso. |
|[Endereço IP público](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) | Cria um endereço IP público e gera a ID de recurso. |
|[Balanceador de carga](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) | Links para o modelo anterior. Usa a ID do recurso na saída ao criar o balanceador de carga. |

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre as propriedades disponíveis para saídas, consulte [entender a estrutura e a sintaxe de modelos ARM](template-syntax.md).
