---
title: Funções de modelo – lógica
description: Descreve as funções a serem usadas em um modelo de Azure Resource Manager (modelo ARM) para determinar valores lógicos.
ms.topic: conceptual
ms.date: 11/18/2020
ms.openlocfilehash: 27d94f10374daf0b9a351469579a5eb659cf5445
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96920481"
---
# <a name="logical-functions-for-arm-templates"></a>Funções lógicas para modelos ARM

O Gerenciador de recursos fornece várias funções para fazer comparações em seu modelo de Azure Resource Manager (modelo ARM):

* [and](#and)
* [bool](#bool)
* [false](#false)
* [if](#if)
* [not](#not)
* [or](#or)
* [true](#true)

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="and"></a>e

`and(arg1, arg2, ...)`

Verifica se todos os valores de parâmetros são verdadeiros. `and`Não há suporte para a função em bicep. `&&`Em vez disso, use o operador.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Type | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |booleano |O primeiro valor para verificar se é verdadeiro. |
| arg2 |Sim |booleano |O segundo valor para verificar se é verdadeiro. |
| argumentos adicionais |Não |boolean |Argumentos adicionais para verificar se são verdadeiros. |

### <a name="return-value"></a>Retornar valor

Retorna **True** se todos os valores forem verdadeiros; caso contrário, **False**.

### <a name="examples"></a>Exemplos

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) a seguir mostra como usar funções lógicas.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "andExampleOutput": {
      "type": "bool",
      "value": "[and(bool('true'), bool('false'))]"
    },
    "orExampleOutput": {
      "type": "bool",
      "value": "[or(bool('true'), bool('false'))]"
    },
    "notExampleOutput": {
      "type": "bool",
      "value": "[not(bool('true'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output andExampleOutput bool = bool('true') && bool('false')
output orExampleOutput bool = bool('true') || bool('false')
output notExampleOutput bool = !(bool('true'))
```

---

O resultado do exemplo anterior é:

| Nome | Type | Valor |
| ---- | ---- | ----- |
| andExampleOutput | Bool | Falso |
| orExampleOutput | Bool | True |
| notExampleOutput | Bool | Falso |

## <a name="bool"></a>bool

`bool(arg1)`

Converte o parâmetro em um booliano.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Type | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |cadeia de caracteres ou inteiro |O valor a ser convertido em um booliano. |

### <a name="return-value"></a>Retornar valor

Um booliano do valor convertido.

### <a name="remarks"></a>Comentários

Você também pode usar [true ()](#true) e [false ()](#false) para obter valores Boolianos.

### <a name="examples"></a>Exemplos

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/bool.json) a seguir mostra como usar um booliano com uma cadeia de caracteres ou um inteiro.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "trueString": {
      "type": "bool",
      "value": "[bool('true')]",
    },
    "falseString": {
      "type": "bool",
      "value": "[bool('false')]"
    },
    "trueInt": {
      "type": "bool",
      "value": "[bool(1)]"
    },
    "falseInt": {
      "type": "bool",
      "value": "[bool(0)]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output trueString bool = bool('true')
output falseString bool = bool('false')
output trueInt bool = bool(1)
output falseInt bool = bool(0)
```

---
A saída do exemplo anterior com os valores padrão é:

| Nome | Type | Valor |
| ---- | ---- | ----- |
| trueString | Bool | True |
| falseString | Bool | Falso |
| trueInt | Bool | True |
| falseInt | Bool | Falso |

## <a name="false"></a>false

`false()`

Retorna false. A `false` função não está disponível em bicep.  `false`Em vez disso, use a palavra-chave.

### <a name="parameters"></a>Parâmetros

A função false não aceita nenhum parâmetro.

### <a name="return-value"></a>Retornar valor

Um booliano que é sempre false.

### <a name="example"></a>Exemplo

O exemplo a seguir retorna um valor de saída falso.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "falseOutput": {
      "type": "bool",
      "value": "[false()]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output falseOutput bool = false
```

---

O resultado do exemplo anterior é:

| Nome | Type | Valor |
| ---- | ---- | ----- |
| falseOutput | Bool | Falso |

## <a name="if"></a>if

`if(condition, trueValue, falseValue)`

Retorna um valor com base em se uma condição é verdadeira ou falsa. `if`Não há suporte para a função em bicep. `?:`Em vez disso, use o operador.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Type | Descrição |
|:--- |:--- |:--- |:--- |
| condition |Sim |booleano |O valor para verificar se é verdadeiro ou falso. |
| trueValue |Sim | cadeia de caracteres, inteiro, objeto ou matriz |O valor a ser retornado quando a condição é verdadeira. |
| falseValue |Sim | cadeia de caracteres, inteiro, objeto ou matriz |O valor a ser retornado quando a condição é falsa. |

### <a name="return-value"></a>Retornar valor

Retorna o segundo parâmetro quando o primeiro parâmetro é **True**; caso contrário, retorna o terceiro parâmetro.

### <a name="remarks"></a>Comentários

Quando a condição for **verdadeira**, somente o valor verdadeiro será avaliado. Quando a condição for **falsa**, somente o valor false será avaliado. Com a `if` função, você pode incluir expressões que são apenas condicionalmente válidas. Por exemplo, você pode fazer referência a um recurso que existe sob uma condição, mas não sob a outra condição. Um exemplo de expressões de avaliação condicional é mostrado na seção a seguir.

### <a name="examples"></a>Exemplos

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/if.json) a seguir mostra como usar a função `if`.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
  ],
  "outputs": {
    "yesOutput": {
      "type": "string",
      "value": "[if(equals('a', 'a'), 'yes', 'no')]"
    },
    "noOutput": {
      "type": "string",
      "value": "[if(equals('a', 'b'), 'yes', 'no')]"
    },
    "objectOutput": {
      "type": "object",
      "value": "[if(equals('a', 'a'), json('{\"test\": \"value1\"}'), json('null'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output yesOutput string = 'a' == 'a' ? 'yes' : 'no'
output noOutput string = 'a' == 'b' ? 'yes' : 'no'
output objectOutput object = 'a' == 'a' ? json('{"test": "value1"}') : json('null')
```

---

O resultado do exemplo anterior é:

| Nome | Type | Valor |
| ---- | ---- | ----- |
| yesOutput | String | sim |
| noOutput | String | não |
| objectOutput | Objeto | { "test": "value1" } |

O [modelo de exemplo](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/conditionWithReference.json) a seguir mostra como usar essa função com expressões que são apenas condicionalmente válidas.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string"
    },
    "location": {
      "type": "string"
    },
    "logAnalytics": {
      "type": "string",
      "defaultValue": ""
    }
  },
  "resources": [
    {
      "condition": "[not(empty(parameters('logAnalytics')))]",
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "apiVersion": "2017-03-30",
      "name": "[concat(parameters('vmName'),'/omsOnboarding')]",
      "location": "[parameters('location')]",
      "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "workspaceId": "[if(not(empty(parameters('logAnalytics'))), reference(parameters('logAnalytics'), '2015-11-01-preview').customerId, json('null'))]"
        },
        "protectedSettings": {
          "workspaceKey": "[if(not(empty(parameters('logAnalytics'))), listKeys(parameters('logAnalytics'), '2015-11-01-preview').primarySharedKey, json('null'))]"
        }
      }
    }
  ],
  "outputs": {
    "mgmtStatus": {
      "type": "string",
      "value": "[if(not(empty(parameters('logAnalytics'))), 'Enabled monitoring for VM!', 'Nothing to enable')]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

> [!NOTE]
> `Conditions` Ainda não foram implementadas no bicep. Consulte [condições](https://github.com/Azure/bicep/issues/186).

---

## <a name="not"></a>not

`not(arg1)`

Converte o valor booliano em seu valor oposto. `not`Não há suporte para a função em bicep. `!`Em vez disso, use o operador.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Type | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |booleano |O valor a ser convertido. |

### <a name="return-value"></a>Retornar valor

Retorna **True** quando o parâmetro é **False**. Retorna **False** quando o parâmetro é **True**.

### <a name="examples"></a>Exemplos

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) a seguir mostra como usar funções lógicas.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "andExampleOutput": {
      "type": "bool",
      "value": "[and(bool('true'), bool('false'))]",
    },
    "orExampleOutput": {
      "type": "bool",
      "value": "[or(bool('true'), bool('false'))]"
    },
    "notExampleOutput": {
      "type": "bool",
      "value": "[not(bool('true'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output andExampleOutput bool = bool('true') && bool('false')
output orExampleOutput bool = bool('true') || bool('false')
output notExampleOutput bool = !(bool('true'))
```

---

O resultado do exemplo anterior é:

| Nome | Type | Valor |
| ---- | ---- | ----- |
| andExampleOutput | Bool | Falso |
| orExampleOutput | Bool | True |
| notExampleOutput | Bool | Falso |

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/not-equals.json) a seguir usa **not** com [equals](template-functions-comparison.md#equals).

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
  ],
  "outputs": {
    "checkNotEquals": {
      "type": "bool",
      "value": "[not(equals(1, 2))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output checkNotEquals bool = !(1 == 2)
```

---

O resultado do exemplo anterior é:

| Nome | Type | Valor |
| ---- | ---- | ----- |
| checkNotEquals | Bool | True |

## <a name="or"></a>ou

`or(arg1, arg2, ...)`

Verifica se qualquer valor do parâmetro é verdadeiro. `or`Não há suporte para a função em bicep. `||`Em vez disso, use o operador.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Type | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |booleano |O primeiro valor para verificar se é verdadeiro. |
| arg2 |Sim |booleano |O segundo valor para verificar se é verdadeiro. |
| argumentos adicionais |Não |boolean |Argumentos adicionais para verificar se são verdadeiros. |

### <a name="return-value"></a>Retornar valor

Retorna **True** se qualquer valor for verdadeiro; caso contrário, **False**.

### <a name="examples"></a>Exemplos

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) a seguir mostra como usar funções lógicas.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "andExampleOutput": {
      "value": "[and(bool('true'), bool('false'))]",
      "type": "bool"
    },
    "orExampleOutput": {
      "value": "[or(bool('true'), bool('false'))]",
      "type": "bool"
    },
    "notExampleOutput": {
      "value": "[not(bool('true'))]",
      "type": "bool"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output andExampleOutput bool = bool('true') && bool('false')
output orExampleOutput bool = bool('true') || bool('false')
output notExampleOutput bool = !(bool('true'))
```

---

O resultado do exemplo anterior é:

| Nome | Type | Valor |
| ---- | ---- | ----- |
| andExampleOutput | Bool | Falso |
| orExampleOutput | Bool | True |
| notExampleOutput | Bool | Falso |

## <a name="true"></a>true

`true()`

Retorna verdadeiro. A `true` função não está disponível em bicep.  `true`Em vez disso, use a palavra-chave.

### <a name="parameters"></a>Parâmetros

A função true não aceita nenhum parâmetro. A `true` função não está disponível em bicep.  `true`Em vez disso, use a palavra-chave.

### <a name="return-value"></a>Retornar valor

Um booliano que é sempre verdadeiro.

### <a name="example"></a>Exemplo

O exemplo a seguir retorna um valor de saída verdadeiro.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "trueOutput": {
      "type": "bool",
      "value": "[true()]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output trueOutput bool = true
```

---

O resultado do exemplo anterior é:

| Nome | Type | Valor |
| ---- | ---- | ----- |
| trueOutput | Bool | True |

## <a name="next-steps"></a>Próximas etapas

* Para obter uma descrição das seções em um modelo do ARM, consulte [entender a estrutura e a sintaxe de modelos do ARM](template-syntax.md).
