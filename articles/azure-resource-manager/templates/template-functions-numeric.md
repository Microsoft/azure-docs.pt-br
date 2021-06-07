---
title: Funções de modelo – numéricas
description: Descreve as funções a serem usadas em um modelo de Azure Resource Manager (modelo ARM) para trabalhar com números.
ms.topic: conceptual
ms.date: 11/18/2020
ms.openlocfilehash: f3687581d94f80cc923614a0655da1813bd5c97b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97359703"
---
# <a name="numeric-functions-for-arm-templates"></a>Funções numéricas para modelos de ARM

O Gerenciador de recursos fornece as seguintes funções para trabalhar com inteiros em seu modelo de Azure Resource Manager (modelo ARM):

* [add](#add)
* [copyIndex](#copyindex)
* [div](#div)
* [float](#float)
* [int](#int)
* [max](#max)
* [min](#min)
* [mod](#mod)
* [mul](#mul)
* [sub](#sub)

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="add"></a>add

`add(operand1, operand2)`

Retorna a soma dos dois inteiros fornecidos. `add`Não há suporte para a função em bicep. `+`Em vez disso, use o operador.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Type | Descrição |
|:--- |:--- |:--- |:--- |
|operand1 |Sim |INT |Primeiro número a ser adicionado. |
|operand2 |Sim |INT |Segundo número a ser adicionado. |

### <a name="return-value"></a>Retornar valor

Um inteiro que contém a soma dos parâmetros.

### <a name="example"></a>Exemplo

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/add.json) a seguir adiciona dois parâmetros.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "first": {
      "type": "int",
      "defaultValue": 5,
      "metadata": {
        "description": "First integer to add"
      }
    },
    "second": {
      "type": "int",
      "defaultValue": 3,
      "metadata": {
        "description": "Second integer to add"
      }
    }
  },
  "resources": [
  ],
  "outputs": {
    "addResult": {
      "type": "int",
      "value": "[add(parameters('first'), parameters('second'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param first int = 5
param second int = 3

output addResult int = first + second
```

---

A saída do exemplo anterior com os valores padrão é:

| Nome | Type | Valor |
| ---- | ---- | ----- |
| addResult | int | 8 |

## <a name="copyindex"></a>copyIndex

`copyIndex(loopName, offset)`

Retorna o índice de um loop de iteração.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Type | Descrição |
|:--- |:--- |:--- |:--- |
| loopName | Não | string | O nome do loop para obter a iteração. |
| deslocamento |Não |INT |O número a ser adicionado ao valor de iteração com base em zero. |

### <a name="remarks"></a>Comentários

Essa função é sempre usada com um objeto **copy** . Se nenhum valor for fornecido para **offset**, o valor de iteração atual retornará. O valor de iteração começa em zero.

A propriedade **loopName** permite que você especifique se copyIndex se refere a uma iteração de recursos ou a uma iteração de propriedade. Se nenhum valor for fornecido para **loopName**, a iteração de tipo de recurso atual será usada. Forneça um valor para **loopName** durante a iteração em uma propriedade.

Para obter mais informações sobre como usar a cópia, consulte:

* [Iteração de recurso em modelos ARM](copy-resources.md)
* [Iteração de propriedade em modelos ARM](copy-properties.md)
* [Iteração variável em modelos ARM](copy-variables.md)
* [Iteração de saída em modelos ARM](copy-outputs.md)

### <a name="example"></a>Exemplo

O exemplo a seguir mostra um loop de cópia e o valor de índice incluído no nome.

# <a name="json"></a>[JSON](#tab/json)

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
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
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
  "outputs": {}
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

> [!NOTE]
> Loops e `copyIndex` ainda não estão implementados em bicep.  Consulte [loops](https://github.com/Azure/bicep/blob/main/docs/spec/loops.md).

---

### <a name="return-value"></a>Retornar valor

Um inteiro que representa o índice atual da iteração.

## <a name="div"></a>div

`div(operand1, operand2)`

Retorna a divisão de inteiros dos dois inteiros fornecidos. `div`Não há suporte para a função em bicep. `/`Em vez disso, use o operador.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Type | Descrição |
|:--- |:--- |:--- |:--- |
| operand1 |Sim |INT |O número que está sendo dividido. |
| operand2 |Sim |INT |O número usado para dividir. Não pode ser 0. |

### <a name="return-value"></a>Retornar valor

Um inteiro que representa a divisão.

### <a name="example"></a>Exemplo

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/div.json) a seguir divide um parâmetro por outro parâmetro.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "first": {
      "type": "int",
      "defaultValue": 8,
      "metadata": {
        "description": "Integer being divided"
      }
    },
    "second": {
      "type": "int",
      "defaultValue": 3,
      "metadata": {
        "description": "Integer used to divide"
      }
    }
  },
  "resources": [
  ],
  "outputs": {
    "divResult": {
      "type": "int",
      "value": "[div(parameters('first'), parameters('second'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param first int = 8
param second int = 3

output addResult int = first / second
```

---

A saída do exemplo anterior com os valores padrão é:

| Nome | Type | Valor |
| ---- | ---- | ----- |
| divResult | int | 2 |

## <a name="float"></a>FLOAT

`float(arg1)`

Converte o valor em um número de ponto flutuante. Você só usa essa função ao passar parâmetros personalizados para um aplicativo, como um aplicativo lógico. `float`Não há suporte para a função em bicep.  Consulte [suporte a tipos numéricos diferentes de inteiros de 32 bits](https://github.com/Azure/bicep/issues/486).

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Type | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |cadeia de caracteres ou inteiro |O valor a ser convertido em um número de ponto flutuante. |

### <a name="return-value"></a>Retornar valor

Um número de ponto flutuante.

### <a name="example"></a>Exemplo

O exemplo a seguir mostra como usar float para passar parâmetros para um aplicativo lógico:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "type": "Microsoft.Logic/workflows",
  "properties": {
    ...
    "parameters": {
      "custom1": {
        "value": "[float('3.0')]"
      },
      "custom2": {
        "value": "[float(3)]"
      },
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

> [!NOTE]
> `float`Não há suporte para a função em bicep.  Consulte [suporte a tipos numéricos diferentes de inteiros de 32 bits](https://github.com/Azure/bicep/issues/486).

---

## <a name="int"></a>INT

`int(valueToConvert)`

Converte o valor especificado em um inteiro.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Type | Descrição |
|:--- |:--- |:--- |:--- |
| valueToConvert |Sim |cadeia de caracteres ou inteiro |O valor a ser convertido em um inteiro. |

### <a name="return-value"></a>Retornar valor

Um inteiro do valor convertido.

### <a name="example"></a>Exemplo

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/int.json) a seguir converte o valor do parâmetro fornecido pelo usuário em inteiro.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "stringToConvert": {
      "type": "string",
      "defaultValue": "4"
    }
  },
  "resources": [
  ],
  "outputs": {
    "intResult": {
      "type": "int",
      "value": "[int(parameters('stringToConvert'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param stringToConvert string = '4'

output inResult int = int(stringToConvert)
```

---

A saída do exemplo anterior com os valores padrão é:

| Nome | Type | Valor |
| ---- | ---- | ----- |
| intResult | int | 4 |

## <a name="max"></a>max

`max (arg1)`

Retorna o valor máximo de uma matriz de inteiros ou uma lista de inteiros separados por vírgulas.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Type | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |matriz de inteiros ou lista de inteiros separados por vírgulas |A coleção para obtenção do valor máximo. |

### <a name="return-value"></a>Retornar valor

Um inteiro que representa o valor máximo da coleção.

### <a name="example"></a>Exemplo

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/max.json) a seguir mostra como usar max com uma matriz e uma lista de inteiros:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "arrayToTest": {
      "type": "array",
      "defaultValue": [ 0, 3, 2, 5, 4 ]
    }
  },
  "resources": [],
  "outputs": {
    "arrayOutput": {
      "type": "int",
      "value": "[max(parameters('arrayToTest'))]"
    },
    "intOutput": {
      "type": "int",
      "value": "[max(0,3,2,5,4)]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param arrayToTest array = [
  0
  3
  2
  5
  4
]

output arrayOutPut int = max(arrayToTest)
output intOutput int = max(0,3,2,5,4)
```

---

A saída do exemplo anterior com os valores padrão é:

| Nome | Type | Valor |
| ---- | ---- | ----- |
| arrayOutput | int | 5 |
| intOutput | int | 5 |

## <a name="min"></a>min

`min (arg1)`

Retorna o valor mínimo de uma matriz de inteiros ou uma lista de inteiros separados por vírgulas.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Type | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |matriz de inteiros ou lista de inteiros separados por vírgulas |A coleção para obtenção do valor mínimo. |

### <a name="return-value"></a>Retornar valor

Um inteiro que representa o valor mínimo da coleção.

### <a name="example"></a>Exemplo

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/min.json) a seguir mostra como usar min com uma matriz e uma lista de inteiros:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "arrayToTest": {
      "type": "array",
      "defaultValue": [ 0, 3, 2, 5, 4 ]
    }
  },
  "resources": [],
  "outputs": {
    "arrayOutput": {
      "type": "int",
      "value": "[min(parameters('arrayToTest'))]"
    },
    "intOutput": {
      "type": "int",
      "value": "[min(0,3,2,5,4)]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param arrayToTest array = [
  0
  3
  2
  5
  4
]

output arrayOutPut int = min(arrayToTest)
output intOutput int = min(0,3,2,5,4)
```

---

A saída do exemplo anterior com os valores padrão é:

| Nome | Type | Valor |
| ---- | ---- | ----- |
| arrayOutput | int | 0 |
| intOutput | int | 0 |

## <a name="mod"></a>mod

`mod(operand1, operand2)`

Retorna o restante da divisão de inteiros usando os dois inteiros fornecidos. `mod`Não há suporte para a função em bicep. `%`Em vez disso, use o operador.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Type | Descrição |
|:--- |:--- |:--- |:--- |
| operand1 |Sim |INT |O número que está sendo dividido. |
| operand2 |Sim |INT |O número que é usado para dividir, não pode ser 0. |

### <a name="return-value"></a>Retornar valor

Um inteiro que representa o resto.

### <a name="example"></a>Exemplo

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/mod.json) a seguir retorna o resto da divisão de um parâmetro por outro parâmetro.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "first": {
      "type": "int",
      "defaultValue": 7,
      "metadata": {
        "description": "Integer being divided"
      }
    },
    "second": {
      "type": "int",
      "defaultValue": 3,
      "metadata": {
        "description": "Integer used to divide"
      }
    }
  },
  "resources": [
  ],
  "outputs": {
    "modResult": {
      "type": "int",
      "value": "[mod(parameters('first'), parameters('second'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param first int = 7
param second int = 3

output modResult int = first % second
```

---

A saída do exemplo anterior com os valores padrão é:

| Nome | Type | Valor |
| ---- | ---- | ----- |
| modResult | int | 1 |

## <a name="mul"></a>mul

`mul(operand1, operand2)`

Retorna a multiplicação de dois inteiros fornecidos. `mul`Não há suporte para a função em bicep. `*`Em vez disso, use o operador.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Type | Descrição |
|:--- |:--- |:--- |:--- |
| operand1 |Sim |INT |Primeiro número a ser multiplicado. |
| operand2 |Sim |INT |Segundo número a ser multiplicado. |

### <a name="return-value"></a>Retornar valor

Um inteiro que representa a multiplicação.

### <a name="example"></a>Exemplo

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/mul.json) a seguir multiplica um parâmetro por outro parâmetro.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "first": {
      "type": "int",
      "defaultValue": 5,
      "metadata": {
        "description": "First integer to multiply"
      }
    },
    "second": {
      "type": "int",
      "defaultValue": 3,
      "metadata": {
        "description": "Second integer to multiply"
      }
    }
  },
  "resources": [
  ],
  "outputs": {
    "mulResult": {
      "type": "int",
      "value": "[mul(parameters('first'), parameters('second'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param first int = 5
param second int = 3

output mulResult int = first * second
```

---

A saída do exemplo anterior com os valores padrão é:

| Nome | Type | Valor |
| ---- | ---- | ----- |
| mulResult | int | 15 |

## <a name="sub"></a>sub

`sub(operand1, operand2)`

Retorna a subtração dos dois inteiros fornecidos. `sub`Não há suporte para a função em bicep. `-`Em vez disso, use o operador.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Type | Descrição |
|:--- |:--- |:--- |:--- |
| operand1 |Sim |INT |O número do qual é subtraído. |
| operand2 |Sim |INT |O número subtraído. |

### <a name="return-value"></a>Retornar valor

Um inteiro que representa a subtração.

### <a name="example"></a>Exemplo

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/sub.json) a seguir subtrai um parâmetro por outro parâmetro.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "first": {
      "type": "int",
      "defaultValue": 7,
      "metadata": {
        "description": "Integer subtracted from"
      }
    },
    "second": {
      "type": "int",
      "defaultValue": 3,
      "metadata": {
        "description": "Integer to subtract"
      }
    }
  },
  "resources": [
  ],
  "outputs": {
    "subResult": {
      "type": "int",
      "value": "[sub(parameters('first'), parameters('second'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param first int = 7
param second int = 3

output subResult int = first - second
```

---

A saída do exemplo anterior com os valores padrão é:

| Nome | Type | Valor |
| ---- | ---- | ----- |
| subResult | int | 4 |

## <a name="next-steps"></a>Próximas etapas

* Para obter uma descrição das seções em um modelo do ARM, consulte [entender a estrutura e a sintaxe de modelos do ARM](template-syntax.md).
* Para iterar um número especificado de vezes ao criar um tipo de recurso, consulte [iteração de recurso em modelos ARM](copy-resources.md).
