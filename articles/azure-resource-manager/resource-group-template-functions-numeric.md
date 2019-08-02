---
title: Funções do modelo do Azure Resource Manager – numéricas | Microsoft Docs
description: Descreve as funções a serem usadas em um modelo do Resource Manager para trabalhar com números.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 11/08/2017
ms.author: tomfitz
ms.openlocfilehash: f63ce16369fd1ff58d5368b43c3c730008e63d9a
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67206409"
---
# <a name="numeric-functions-for-azure-resource-manager-templates"></a>Funções numéricas para modelos do Azure Resource Manager

O Gerenciador de Recursos fornece as seguintes funções para trabalhar com números inteiros:

* [adicionar](#add)
* [copyIndex](#copyindex)
* [div](#div)
* [float](#float)
* [int](#int)
* [max](#max)
* [min](#min)
* [mod](#mod)
* [mul](#mul)
* [sub](#sub)

<a id="add" />

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="add"></a>Adicionar
`add(operand1, operand2)`

Retorna a soma dos dois inteiros fornecidos.

### <a name="parameters"></a>parâmetros

| Parâmetro | Obrigatório | Type | DESCRIÇÃO |
|:--- |:--- |:--- |:--- | 
|operand1 |Sim |int |Primeiro número a ser adicionado. |
|operand2 |Sim |int |Segundo número a ser adicionado. |

### <a name="return-value"></a>Valor de retorno

Um inteiro que contém a soma dos parâmetros.

### <a name="example"></a>Exemplo

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/add.json) a seguir adiciona dois parâmetros.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

A saída do exemplo anterior com os valores padrão é:

| NOME | Tipo | Value |
| ---- | ---- | ----- |
| addResult | Int | 8 |

Para implantar este modelo de exemplo com a CLI do Azure, use:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/add.json
```

Para implantar este modelo de exemplo com o PowerShell, use:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/add.json 
```

<a id="copyindex" />

## <a name="copyindex"></a>copyIndex
`copyIndex(loopName, offset)`

Retorna o índice de um loop de iteração. 

### <a name="parameters"></a>parâmetros

| Parâmetro | Obrigatório | Type | DESCRIÇÃO |
|:--- |:--- |:--- |:--- |
| loopName | Não | cadeia de caracteres | O nome do loop para obter a iteração. |
| deslocamento |Não |int |O número a ser adicionado ao valor de iteração com base em zero. |

### <a name="remarks"></a>Comentários

Essa função é sempre usada com um objeto **copy** . Se nenhum valor for fornecido para **offset**, o valor de iteração atual retornará. O valor de iteração começa em zero. Use loops de iteração ao definir recursos ou variáveis.

A propriedade **loopName** permite que você especifique se copyIndex se refere a uma iteração de recursos ou a uma iteração de propriedade. Se nenhum valor for fornecido para **loopName**, a iteração de tipo de recurso atual será usada. Forneça um valor para **loopName** durante a iteração em uma propriedade. 
 
Para obter uma descrição completa de como usar **copyIndex**, confira [Criar várias instâncias de recursos no Azure Resource Manager](resource-group-create-multiple.md).

Para obter um exemplo de como usar **copyIndex** ao definir uma variável, consulte [Variáveis](resource-group-authoring-templates.md#variables).

### <a name="example"></a>Exemplo

O exemplo a seguir mostra um loop de cópia e o valor de índice incluído no nome. 

```json
"resources": [ 
  { 
    "name": "[concat('examplecopy-', copyIndex())]", 
    "type": "Microsoft.Web/sites", 
    "copy": { 
      "name": "websitescopy", 
      "count": "[parameters('count')]" 
    }, 
    ...
  }
]
```

### <a name="return-value"></a>Valor de retorno

Um inteiro que representa o índice atual da iteração.

<a id="div" />

## <a name="div"></a>div
`div(operand1, operand2)`

Retorna a divisão de inteiros dos dois inteiros fornecidos.

### <a name="parameters"></a>parâmetros

| Parâmetro | Obrigatório | Type | DESCRIÇÃO |
|:--- |:--- |:--- |:--- |
| operand1 |Sim |int |O número que está sendo dividido. |
| operand2 |Sim |int |O número usado para dividir. Não pode ser 0. |

### <a name="return-value"></a>Valor de retorno

Um inteiro que representa a divisão.

### <a name="example"></a>Exemplo

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/div.json) a seguir divide um parâmetro por outro parâmetro.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

A saída do exemplo anterior com os valores padrão é:

| NOME | Tipo | Value |
| ---- | ---- | ----- |
| divResult | Int | 2 |

Para implantar este modelo de exemplo com a CLI do Azure, use:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/div.json
```

Para implantar este modelo de exemplo com o PowerShell, use:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/div.json 
```

<a id="float" />

## <a name="float"></a>float
`float(arg1)`

Converte o valor em um número de ponto flutuante. Você só usa essa função ao passar parâmetros personalizados para um aplicativo, como um aplicativo lógico.

### <a name="parameters"></a>parâmetros

| Parâmetro | Obrigatório | Type | DESCRIÇÃO |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |cadeia de caracteres ou inteiro |O valor a ser convertido em um número de ponto flutuante. |

### <a name="return-value"></a>Valor de retorno
Um número de ponto flutuante.

### <a name="example"></a>Exemplo

O exemplo a seguir mostra como usar float para passar parâmetros para um aplicativo lógico:

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

<a id="int" />

## <a name="int"></a>int
`int(valueToConvert)`

Converte o valor especificado em um inteiro.

### <a name="parameters"></a>parâmetros

| Parâmetro | Obrigatório | Type | DESCRIÇÃO |
|:--- |:--- |:--- |:--- |
| valueToConvert |Sim |string ou int |O valor a ser convertido em um inteiro. |

### <a name="return-value"></a>Valor de retorno

Um inteiro do valor convertido.

### <a name="example"></a>Exemplo

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/int.json) a seguir converte o valor do parâmetro fornecido pelo usuário em inteiro.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

A saída do exemplo anterior com os valores padrão é:

| NOME | Tipo | Value |
| ---- | ---- | ----- |
| intResult | Int | 4 |

Para implantar este modelo de exemplo com a CLI do Azure, use:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/int.json
```

Para implantar este modelo de exemplo com o PowerShell, use:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/int.json
```

<a id="max" />

## <a name="max"></a>max
`max (arg1)`

Retorna o valor máximo de uma matriz de inteiros ou uma lista de inteiros separados por vírgulas.

### <a name="parameters"></a>parâmetros

| Parâmetro | Obrigatório | Type | DESCRIÇÃO |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |matriz de inteiros ou lista de inteiros separados por vírgulas |A coleção para obtenção do valor máximo. |

### <a name="return-value"></a>Valor de retorno

Um inteiro que representa o valor máximo da coleção.

### <a name="example"></a>Exemplo

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/max.json) a seguir mostra como usar max com uma matriz e uma lista de inteiros:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": [0,3,2,5,4]
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

A saída do exemplo anterior com os valores padrão é:

| NOME | Tipo | Value |
| ---- | ---- | ----- |
| arrayOutput | Int | 5 |
| intOutput | Int | 5 |

Para implantar este modelo de exemplo com a CLI do Azure, use:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/max.json
```

Para implantar este modelo de exemplo com o PowerShell, use:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/max.json
```

<a id="min" />

## <a name="min"></a>Min
`min (arg1)`

Retorna o valor mínimo de uma matriz de inteiros ou uma lista de inteiros separados por vírgulas.

### <a name="parameters"></a>parâmetros

| Parâmetro | Obrigatório | Type | DESCRIÇÃO |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |matriz de inteiros ou lista de inteiros separados por vírgulas |A coleção para obtenção do valor mínimo. |

### <a name="return-value"></a>Valor de retorno

Um inteiro que representa o valor mínimo da coleção.

### <a name="example"></a>Exemplo

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/min.json) a seguir mostra como usar min com uma matriz e uma lista de inteiros:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": [0,3,2,5,4]
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

A saída do exemplo anterior com os valores padrão é:

| NOME | Tipo | Value |
| ---- | ---- | ----- |
| arrayOutput | Int | 0 |
| intOutput | Int | 0 |

Para implantar este modelo de exemplo com a CLI do Azure, use:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/min.json
```

Para implantar este modelo de exemplo com o PowerShell, use:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/min.json
```

<a id="mod" />

## <a name="mod"></a>mod
`mod(operand1, operand2)`

Retorna o restante da divisão de inteiros usando os dois inteiros fornecidos.

### <a name="parameters"></a>parâmetros

| Parâmetro | Obrigatório | Type | DESCRIÇÃO |
|:--- |:--- |:--- |:--- |
| operand1 |Sim |int |O número que está sendo dividido. |
| operand2 |Sim |int |O número usado para dividir. Não pode ser 0. |

### <a name="return-value"></a>Valor de retorno
Um inteiro que representa o resto.

### <a name="example"></a>Exemplo

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/mod.json) a seguir retorna o resto da divisão de um parâmetro por outro parâmetro.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

A saída do exemplo anterior com os valores padrão é:

| NOME | Tipo | Value |
| ---- | ---- | ----- |
| modResult | Int | 1 |

Para implantar este modelo de exemplo com a CLI do Azure, use:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/mod.json
```

Para implantar este modelo de exemplo com o PowerShell, use:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/mod.json
```

<a id="mul" />

## <a name="mul"></a>mul
`mul(operand1, operand2)`

Retorna a multiplicação de dois inteiros fornecidos.

### <a name="parameters"></a>parâmetros

| Parâmetro | Obrigatório | Type | DESCRIÇÃO |
|:--- |:--- |:--- |:--- |
| operand1 |Sim |int |Primeiro número a ser multiplicado. |
| operand2 |Sim |int |Segundo número a ser multiplicado. |

### <a name="return-value"></a>Valor de retorno

Um inteiro que representa a multiplicação.

### <a name="example"></a>Exemplo

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/mul.json) a seguir multiplica um parâmetro por outro parâmetro.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

A saída do exemplo anterior com os valores padrão é:

| NOME | Tipo | Value |
| ---- | ---- | ----- |
| mulResult | Int | 15 |

Para implantar este modelo de exemplo com a CLI do Azure, use:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/mul.json
```

Para implantar este modelo de exemplo com o PowerShell, use:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/mul.json
```

<a id="sub" />

## <a name="sub"></a>sub
`sub(operand1, operand2)`

Retorna a subtração dos dois inteiros fornecidos.

### <a name="parameters"></a>parâmetros

| Parâmetro | Obrigatório | Type | DESCRIÇÃO |
|:--- |:--- |:--- |:--- |
| operand1 |Sim |int |O número do qual é subtraído. |
| operand2 |Sim |int |O número subtraído. |

### <a name="return-value"></a>Valor de retorno
Um inteiro que representa a subtração.

### <a name="example"></a>Exemplo

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/sub.json) a seguir subtrai um parâmetro por outro parâmetro.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

A saída do exemplo anterior com os valores padrão é:

| NOME | Tipo | Value |
| ---- | ---- | ----- |
| subResult | Int | 4 |

Para implantar este modelo de exemplo com a CLI do Azure, use:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/sub.json
```

Para implantar este modelo de exemplo com o PowerShell, use:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/sub.json
```

## <a name="next-steps"></a>Próximas etapas
* Para obter uma descrição das seções de um modelo do Azure Resource Manager, veja [Criando modelos do Azure Resource Manager](resource-group-authoring-templates.md).
* Para mesclar vários modelos, veja [Usando modelos vinculados com o Azure Resource Manager](resource-group-linked-templates.md).
* Para iterar um número de vezes especificado ao criar um tipo de recurso, consulte [Criar várias instâncias de recursos no Gerenciador de Recursos do Azure](resource-group-create-multiple.md).
* Para ver como implantar o modelo que você criou, veja [Implantar um aplicativo com o modelo do Azure Resource Manager](resource-group-template-deploy.md).

