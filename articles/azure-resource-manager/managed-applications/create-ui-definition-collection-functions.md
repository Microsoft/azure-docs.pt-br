---
title: Criar funções de coleção de definições de interface do usuário
description: Descreve as funções a serem usadas ao trabalhar com coleções, como matrizes e objetos.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: 2a075c5c99f457681cd49e75014487bf9cca263c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "87095728"
---
# <a name="createuidefinition-collection-functions"></a>Funções de coleção CreateUiDefinition

Essas funções podem ser usadas com coleções, como cadeias de caracteres JSON, matrizes e objetos.

## <a name="contains"></a>contém

Retorna `true` se uma cadeia de caracteres contém a subcadeia de caracteres especificada, uma matriz contém o valor especificado ou um objeto contém a chave especificada.

### <a name="example-string-contains"></a>Exemplo: cadeia de caracteres contém

O exemplo a seguir retorna `true`:

```json
"[contains('webapp', 'web')]"
```

### <a name="example-array-contains"></a>Exemplo: matriz contém

Suponha que `element1` retorne `[1, 2, 3]`. O exemplo a seguir retorna `false`:

```json
"[contains(steps('demoStep').element1, 4)]"
```

### <a name="example-object-contains"></a>Exemplo: objeto contém

Suponha que `element1` retorne:

```json
{
  "key1": "Linux",
  "key2": "Windows"
}
```

O exemplo a seguir retorna `true`:

```json
"[contains(steps('demoStep').element1, 'key1')]"
```

## <a name="empty"></a>vazio

Retorna `true` se a cadeia de caracteres, matriz ou objeto é nulo ou vazio.

### <a name="example-string-empty"></a>Exemplo: cadeia de caracteres vazia

O exemplo a seguir retorna `true`:

```json
"[empty('')]"
```

### <a name="example-array-empty"></a>Exemplo: matriz vazia

Suponha que `element1` retorne `[1, 2, 3]`. O exemplo a seguir retorna `false`:

```json
"[empty(steps('demoStep').element1)]"
```

### <a name="example-object-empty"></a>Exemplo: objeto vazio

Suponha que `element1` retorne:

```json
{
  "key1": "Linux",
  "key2": "Windows"
}
```

O exemplo a seguir retorna `false`:

```json
"[empty(steps('demoStep').element1)]"
```

### <a name="example-null-and-undefined"></a>Exemplo: nulo e indefinido

Suponha que `element1` é `null` ou indefinido. O exemplo a seguir retorna `true`:

```json
"[empty(steps('demoStep').element1)]"
```

## <a name="filter"></a>filter

Retorna uma nova matriz após aplicar a lógica de filtragem fornecida como uma função lambda. O primeiro parâmetro é a matriz a ser usada para filtragem. O segundo parâmetro é a função lambda que especifica a lógica de filtragem.

O exemplo a seguir retorna a matriz `[ { "name": "abc" } ]` .

```json
"[filter(parse('[{\"name\":\"abc\"},{\"name\":\"xyz\"}]'), (item) => contains(item.name, 'abc'))]"
```

## <a name="first"></a>first

Retorna o primeiro caractere da cadeia de caracteres especificada, o primeiro valor da matriz especificada ou a primeira chave e valor do objeto especificado.

### <a name="example-string-first"></a>Exemplo: cadeia de caracteres primeiro

O exemplo a seguir retorna `"c"`:

```json
"[first('contoso')]"
```

### <a name="example-array-first"></a>Exemplo: matriz primeiro

Suponha que `element1` retorne `[1, 2, 3]`. O exemplo a seguir retorna `1`:

```json
"[first(steps('demoStep').element1)]"
```

#### <a name="example-object-first"></a>Exemplo: objeto primeiro

Suponha que `element1` retorne:

```json
{
  "key1": "Linux",
  "key2": "Windows"
}
```

O exemplo a seguir retorna `{"key1": "Linux"}`:

```json
"[first(steps('demoStep').element1)]"
```

## <a name="last"></a>last

Retorna o último caractere da cadeia de caracteres especificada, o último valor da matriz especificada ou a última chave e valor do objeto especificado.

### <a name="example-string-last"></a>Exemplo: cadeia de caracteres última

O exemplo a seguir retorna `"o"`:

```json
"[last('contoso')]"
```

### <a name="example-array-last"></a>Exemplo: matriz última

Suponha que `element1` retorne `[1, 2, 3]`. O exemplo a seguir retorna `3`:

```json
"[last(steps('demoStep').element1)]"
```

### <a name="example-object-last"></a>Exemplo: objeto Last

Suponha que `element1` retorne:

```json
{
  "key1": "Linux",
  "key2": "Windows"
}
```

O exemplo a seguir retorna `{"key2": "Windows"}`:

```json
"[last(steps('demoStep').element1)]"
```

## <a name="length"></a>comprimento

Retorna o número de caracteres em uma cadeia de caracteres, o número de valores em uma matriz ou o número de chaves em um objeto.

### <a name="example-string-length"></a>Exemplo: comprimento da cadeia de caracteres

O exemplo a seguir retorna `7`:

```json
"[length('Contoso')]"
```

### <a name="example-array-length"></a>Exemplo: comprimento da matriz

Suponha que `element1` retorne `[1, 2, 3]`. O exemplo a seguir retorna `3`:

```json
"[length(steps('demoStep').element1)]"
```

### <a name="example-object-length"></a>Exemplo: comprimento do objeto

Suponha que `element1` retorne:

```json
{
  "key1": "Linux",
  "key2": "Windows"
}
```

O exemplo a seguir retorna `2`:

```json
"[length(steps('demoStep').element1)]"
```

## <a name="map"></a>mapa

Retorna uma nova matriz depois de chamar uma função lambda em uma matriz fornecida. O primeiro parâmetro é a matriz a ser usada para a função lambda. O segundo parâmetro é a função lambda.

O exemplo a seguir retorna uma nova matriz com cada valor dobrado. O resultado é `[2, 4, 6]`.

```json
"[map(parse('[1, 2, 3]'), (item) => mul(2, item))]"
```

O exemplo a seguir retorna uma nova matriz `["abc", "xyz"]` .

```json
"[map(parse('[{\"name\":\"abc\"},{\"name\":\"xyz\"}]'), (item) => item.name)]"
```

## <a name="skip"></a>skip

Ignora um número especificado de elementos em uma coleção e, em seguida, retorna os elementos restantes.

### <a name="example-string-skip"></a>Exemplo: String Skip

O exemplo a seguir retorna `"app"`:

```json
"[skip('webapp', 3)]"
```

### <a name="example-array-skip"></a>Exemplo: ignorar matriz

Suponha que `element1` retorne `[1, 2, 3]`. O exemplo a seguir retorna `[3]`:

```json
"[skip(steps('demoStep').element1, 2)]"
```

### <a name="example-object-skip"></a>Exemplo: ignorar objeto

Suponha que `element1` retorne:

```json
{
  "key1": "Linux",
  "key2": "Windows"
}
```
O exemplo a seguir retorna `{"key2": "Windows"}`:

```json
"[skip(steps('demoStep').element1, 1)]"
```

## <a name="split"></a>divisão

Retorna uma matriz de cadeias de caracteres que contém as subcadeias da entrada delimitada pelo separador.

O exemplo a seguir retorna a matriz `[ "555", "867", "5309" ]` .

```json
"[split('555-867-5309', '-')]"
```

## <a name="take"></a>take

Retorna um número especificado de caracteres contíguos do início da cadeia de caracteres, um número especificado de valores contíguos do início da matriz ou um número especificado de chaves e valores contíguos do início do objeto.

### <a name="example-string-take"></a>Exemplo: cadeia de caracteres Take

O exemplo a seguir retorna `"web"`:

```json
"[take('webapp', 3)]"
```

### <a name="example-array-take"></a>Exemplo: pegar matriz

Suponha que `element1` retorne `[1, 2, 3]`. O exemplo a seguir retorna `[1, 2]`:

```json
"[take(steps('demoStep').element1, 2)]"
```

### <a name="example-object-take"></a>Exemplo: objeto Take

Suponha que `element1` retorne:

```json
{
  "key1": "Linux",
  "key2": "Windows"
}
```

O exemplo a seguir retorna `{"key1": "Linux"}`:

```json
"[take(steps('demoStep').element1, 1)]"
```

## <a name="next-steps"></a>Próximas etapas

* Para obter uma introdução ao Azure Resource Manager, consulte [Visão geral do Azure Resource Manager](../management/overview.md).
