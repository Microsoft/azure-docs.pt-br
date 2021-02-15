---
title: Definir várias instâncias de uma variável
description: Use a operação de cópia em um modelo de Azure Resource Manager (modelo ARM) para iterar várias vezes ao criar uma variável.
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: b8acd85659b843cb482e1ccc61e28da03431db1b
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/09/2020
ms.locfileid: "96905886"
---
# <a name="variable-iteration-in-arm-templates"></a>Iteração variável em modelos ARM

Este artigo mostra como criar mais de um valor para uma variável em seu modelo de Azure Resource Manager (modelo ARM). Ao adicionar o `copy` elemento à seção de variáveis do modelo, você pode definir dinamicamente o número de itens para uma variável durante a implantação. Você também evita a repetição da sintaxe do modelo.

Você também pode usar copiar com [recursos](copy-resources.md), [Propriedades em um recurso](copy-properties.md)e [saídas](copy-outputs.md).

## <a name="syntax"></a>Syntax

O elemento Copy tem o seguinte formato geral:

```json
"copy": [
  {
    "name": "<name-of-loop>",
    "count": <number-of-iterations>,
    "input": <values-for-the-variable>
  }
]
```

A `name` propriedade é qualquer valor que identifique o loop. A `count` propriedade especifica o número de iterações que você deseja para a variável.

A `input` propriedade especifica as propriedades que você deseja repetir. Crie uma matriz de elementos construídos a partir do valor na propriedade `input`. Pode ser uma única propriedade (como uma cadeia de caracteres) ou um objeto com várias propriedades.

## <a name="copy-limits"></a>Limites de cópia

A contagem não pode exceder 800.

A contagem não pode ser um número negativo. Ele pode ser zero se você implantar o modelo com uma versão recente do CLI do Azure, do PowerShell ou da API REST. Especificamente, você deve usar:

* Azure PowerShell **2,6** ou posterior
* CLI do Azure **2.0.74** ou posterior
* API REST versão **2019-05-10** ou posterior
* [Implantações vinculadas](linked-templates.md) devem usar a versão de API **2019-05-10** ou posterior para o tipo de recurso de implantação

As versões anteriores do PowerShell, da CLI e da API REST não dão suporte a zero para contagem.

## <a name="variable-iteration"></a>Iteração de variável

O exemplo a seguir mostra como criar uma matriz de valores de cadeia de caracteres:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "itemCount": {
            "type": "int",
            "defaultValue": 5
        }
     },
    "variables": {
        "copy": [
            {
                "name": "stringArray",
                "count": "[parameters('itemCount')]",
                "input": "[concat('item', copyIndex('stringArray', 1))]"
            }
        ]
    },
    "resources": [],
    "outputs": {
        "arrayResult": {
            "type": "array",
            "value": "[variables('stringArray')]"
        }
    }
}
```

O modelo anterior retorna uma matriz com os seguintes valores:

```json
[
    "item1",
    "item2",
    "item3",
    "item4",
    "item5"
]
```

O exemplo a seguir mostra como criar uma matriz de objetos com três propriedades- `name` , `diskSizeGB` e `diskIndex` .

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "itemCount": {
            "type": "int",
            "defaultValue": 5
        }
    },
    "variables": {
        "copy": [
            {
                "name": "objectArray",
                "count": "[parameters('itemCount')]",
                "input": {
                    "name": "[concat('myDataDisk', copyIndex('objectArray', 1))]",
                    "diskSizeGB": "1",
                    "diskIndex": "[copyIndex('objectArray')]"
                }
            }
        ]
    },
    "resources": [],
    "outputs": {
        "arrayResult": {
            "type": "array",
            "value": "[variables('objectArray')]"
        }
    }
}
```

O exemplo anterior retorna uma matriz com os seguintes valores:

```json
[
    {
        "name": "myDataDisk1",
        "diskSizeGB": "1",
        "diskIndex": 0
    },
    {
        "name": "myDataDisk2",
        "diskSizeGB": "1",
        "diskIndex": 1
    },
    {
        "name": "myDataDisk3",
        "diskSizeGB": "1",
        "diskIndex": 2
    },
    {
        "name": "myDataDisk4",
        "diskSizeGB": "1",
        "diskIndex": 3
    },
    {
        "name": "myDataDisk5",
        "diskSizeGB": "1",
        "diskIndex": 4
    }
]
```

> [!NOTE]
> A iteração variável dá suporte a um argumento offset. O deslocamento deve vir após o nome da iteração, como `copyIndex('diskNames', 1)` . Se você não fornecer um valor de deslocamento, o padrão será 0 para a primeira instância.
>

Você também pode usar o `copy` elemento dentro de uma variável. O exemplo a seguir cria um objeto que tem uma matriz como um de seus valores.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "itemCount": {
            "type": "int",
            "defaultValue": 5
        }
    },
    "variables": {
        "topLevelObject": {
            "sampleProperty": "sampleValue",
            "copy": [
                {
                    "name": "disks",
                    "count": "[parameters('itemCount')]",
                    "input": {
                        "name": "[concat('myDataDisk', copyIndex('disks', 1))]",
                        "diskSizeGB": "1",
                        "diskIndex": "[copyIndex('disks')]"
                    }
                }
            ]
        }
    },
    "resources": [],
    "outputs": {
        "objectResult": {
            "type": "object",
            "value": "[variables('topLevelObject')]"
        }
    }
}
```

O exemplo anterior retorna um objeto com os seguintes valores:

```json
{
    "sampleProperty": "sampleValue",
    "disks": [
        {
            "name": "myDataDisk1",
            "diskSizeGB": "1",
            "diskIndex": 0
        },
        {
            "name": "myDataDisk2",
            "diskSizeGB": "1",
            "diskIndex": 1
        },
        {
            "name": "myDataDisk3",
            "diskSizeGB": "1",
            "diskIndex": 2
        },
        {
            "name": "myDataDisk4",
            "diskSizeGB": "1",
            "diskIndex": 3
        },
        {
            "name": "myDataDisk5",
            "diskSizeGB": "1",
            "diskIndex": 4
        }
    ]
}
```

O exemplo a seguir mostra as diferentes maneiras que você pode usar `copy` com variáveis.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {
    "disk-array-on-object": {
      "copy": [
        {
          "name": "disks",
          "count": 5,
          "input": {
            "name": "[concat('myDataDisk', copyIndex('disks', 1))]",
            "diskSizeGB": "1",
            "diskIndex": "[copyIndex('disks')]"
          }
        },
        {
          "name": "diskNames",
          "count": 5,
          "input": "[concat('myDataDisk', copyIndex('diskNames', 1))]"
        }
      ]
    },
    "copy": [
      {
        "name": "top-level-object-array",
        "count": 5,
        "input": {
          "name": "[concat('myDataDisk', copyIndex('top-level-object-array', 1))]",
          "diskSizeGB": "1",
          "diskIndex": "[copyIndex('top-level-object-array')]"
        }
      },
      {
        "name": "top-level-string-array",
        "count": 5,
        "input": "[concat('myDataDisk', copyIndex('top-level-string-array', 1))]"
      },
      {
        "name": "top-level-integer-array",
        "count": 5,
        "input": "[copyIndex('top-level-integer-array')]"
      }
    ]
  },
  "resources": [],
  "outputs": {
    "exampleObject": {
      "value": "[variables('disk-array-on-object')]",
      "type": "object"
    },
    "exampleArrayOnObject": {
      "value": "[variables('disk-array-on-object').disks]",
      "type" : "array"
    },
    "exampleObjectArray": {
      "value": "[variables('top-level-object-array')]",
      "type" : "array"
    },
    "exampleStringArray": {
      "value": "[variables('top-level-string-array')]",
      "type" : "array"
    },
    "exampleIntegerArray": {
      "value": "[variables('top-level-integer-array')]",
      "type" : "array"
    }
  }
}
```

## <a name="example-templates"></a>Modelos de exemplo

Os exemplos a seguir mostram cenários comuns para a criação de mais de um valor para uma variável.

|Modelo  |Descrição  |
|---------|---------|
|[Variáveis de cópia](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) |Demonstra as diferentes maneiras de iteração em variáveis. |
|[Várias regras de segurança](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) |Implanta várias regras de segurança em um grupo de segurança de rede. Cria as regras de segurança a partir de um parâmetro. Para o parâmetro, consulte [vários arquivos de parâmetro NSG](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json). |

## <a name="next-steps"></a>Próximas etapas

* Para percorrer um tutorial, consulte [tutorial: criar várias instâncias de recurso com modelos ARM](template-tutorial-create-multiple-instances.md).
* Para outros usos do elemento copiar, consulte:
  * [Iteração de recurso em modelos ARM](copy-resources.md)
  * [Iteração de propriedade em modelos ARM](copy-properties.md)
  * [Iteração de saída em modelos ARM](copy-outputs.md)
* Se você quiser saber mais sobre as seções de um modelo, consulte [entender a estrutura e a sintaxe de modelos ARM](template-syntax.md).
* Para saber como implantar seu modelo, consulte [implantar recursos com modelos ARM e Azure PowerShell](deploy-powershell.md).
