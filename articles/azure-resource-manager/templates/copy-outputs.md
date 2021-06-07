---
title: Definir várias instâncias de um valor de saída
description: Use a operação de cópia em um modelo de Azure Resource Manager (modelo ARM) para iterar várias vezes ao retornar um valor de uma implantação.
ms.topic: conceptual
ms.date: 04/17/2020
ms.openlocfilehash: 5ae20ed9ec3fdb3b76adbd370f5ba22f9386d613
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96905937"
---
# <a name="output-iteration-in-arm-templates"></a>Iteração de saída em modelos ARM

Este artigo mostra como criar mais de um valor para uma saída em seu modelo de Azure Resource Manager (modelo ARM). Ao adicionar o `copy` elemento à seção de saídas do modelo, você pode retornar dinamicamente vários itens durante a implantação.

Você também pode usar copiar com [recursos](copy-resources.md), [Propriedades em um recurso](copy-properties.md)e [variáveis](copy-variables.md).

## <a name="syntax"></a>Sintaxe

O elemento Copy tem o seguinte formato geral:

```json
"copy": {
  "count": <number-of-iterations>,
  "input": <values-for-the-output>
}
```

A `count` propriedade especifica o número de iterações que você deseja para o valor de saída.

A `input` propriedade especifica as propriedades que você deseja repetir. Crie uma matriz de elementos construídos a partir do valor na propriedade `input`. Pode ser uma única propriedade (como uma cadeia de caracteres) ou um objeto com várias propriedades.

## <a name="copy-limits"></a>Limites de cópia

A contagem não pode exceder 800.

A contagem não pode ser um número negativo. Ele pode ser zero se você implantar o modelo com uma versão recente do CLI do Azure, do PowerShell ou da API REST. Especificamente, você deve usar:

* Azure PowerShell **2,6** ou posterior
* CLI do Azure **2.0.74** ou posterior
* API REST versão **2019-05-10** ou posterior
* [Implantações vinculadas](linked-templates.md) devem usar a versão de API **2019-05-10** ou posterior para o tipo de recurso de implantação

As versões anteriores do PowerShell, da CLI e da API REST não dão suporte a zero para contagem.

## <a name="outputs-iteration"></a>Iteração de saída

O exemplo a seguir cria um número variável de contas de armazenamento e retorna um ponto de extremidade para cada conta de armazenamento:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageCount": {
            "type": "int",
            "defaultValue": 2
        }
    },
    "variables": {
        "baseName": "[concat('storage', uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-04-01",
            "name": "[concat(copyIndex(), variables('baseName'))]",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {},
            "copy": {
                "name": "storagecopy",
                "count": "[parameters('storageCount')]"
            }
        }
    ],
    "outputs": {
        "storageEndpoints": {
            "type": "array",
            "copy": {
                "count": "[parameters('storageCount')]",
                "input": "[reference(concat(copyIndex(), variables('baseName'))).primaryEndpoints.blob]"
            }
        }
    }
}
```

O modelo anterior retorna uma matriz com os seguintes valores:

```json
[
    "https://0storagecfrbqnnmpeudi.blob.core.windows.net/",
    "https://1storagecfrbqnnmpeudi.blob.core.windows.net/"
]
```

O próximo exemplo retorna três propriedades das novas contas de armazenamento.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageCount": {
            "type": "int",
            "defaultValue": 2
        }
    },
    "variables": {
        "baseName": "[concat('storage', uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-04-01",
            "name": "[concat(copyIndex(), variables('baseName'))]",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {},
            "copy": {
                "name": "storagecopy",
                "count": "[parameters('storageCount')]"
            }
        }
    ],
    "outputs": {
        "storageInfo": {
            "type": "array",
            "copy": {
                "count": "[parameters('storageCount')]",
                "input": {
                    "id": "[reference(concat(copyIndex(), variables('baseName')), '2019-04-01', 'Full').resourceId]",
                    "blobEndpoint": "[reference(concat(copyIndex(), variables('baseName'))).primaryEndpoints.blob]",
                    "status": "[reference(concat(copyIndex(), variables('baseName'))).statusOfPrimary]"
                }
            }
        }
    }
}
```

O exemplo anterior retorna uma matriz com os seguintes valores:

```json
[
    {
        "id": "Microsoft.Storage/storageAccounts/0storagecfrbqnnmpeudi",
        "blobEndpoint": "https://0storagecfrbqnnmpeudi.blob.core.windows.net/",
        "status": "available"
    },
    {
        "id": "Microsoft.Storage/storageAccounts/1storagecfrbqnnmpeudi",
        "blobEndpoint": "https://1storagecfrbqnnmpeudi.blob.core.windows.net/",
        "status": "available"
    }
]
```

## <a name="next-steps"></a>Próximas etapas

* Para percorrer um tutorial, consulte [tutorial: criar várias instâncias de recurso com modelos ARM](template-tutorial-create-multiple-instances.md).
* Para outros usos do elemento copiar, consulte:
  * [Iteração de recurso em modelos ARM](copy-resources.md)
  * [Iteração de propriedade em modelos ARM](copy-properties.md)
  * [Iteração variável em modelos ARM](copy-variables.md)
* Se você quiser saber mais sobre as seções de um modelo, consulte [entender a estrutura e a sintaxe de modelos ARM](template-syntax.md).
* Para saber como implantar seu modelo, consulte [implantar recursos com modelos ARM e Azure PowerShell](deploy-powershell.md).
