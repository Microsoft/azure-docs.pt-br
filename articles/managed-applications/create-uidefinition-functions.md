---
title: Funções Criar definições de interface do usuário de aplicativos gerenciados pelo Azure | Microsoft Docs
description: Descreve as funções para uso na criação de definições de interface do usuário para aplicativos gerenciados do Azure
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/12/2017
ms.author: tomfitz
ms.openlocfilehash: 80fd593eecf189d516a8c9d7ef2a94ec9f23fc39
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60587913"
---
# <a name="createuidefinition-functions"></a>Funções de CreateUiDefinition
Esta seção contém as assinaturas para todas as funções com suporte de uma CreateUiDefinition.

Para usar uma função, coloque a declaração entre colchetes. Por exemplo: 

```json
"[function()]"
```

Cadeias de caracteres e outras funções podem ser referenciadas como parâmetros de uma função, mas as cadeias de caracteres devem estar entre aspas simples. Por exemplo: 

```json
"[fn1(fn2(), 'foobar')]"
```

Quando aplicável, referencie propriedades da saída de uma função usando o operador de ponto. Por exemplo: 

```json
"[func().prop1]"
```

## <a name="referencing-functions"></a>Referenciando funções
Essas funções podem ser usadas para referenciar saídas das propriedades ou o contexto de uma CreateUiDefinition.

### <a name="basics"></a>conceitos básicos
Retorna os valores de saída de um elemento que é definido na etapa Conceitos básicos.

O seguinte exemplo retorna a saída do elemento chamado `foo` na etapa Conceitos básicos:

```json
"[basics('foo')]"
```

### <a name="steps"></a>etapas
Retorna os valores de saída de um elemento que é definido na etapa especificada. Para obter os valores de saída de elementos na etapa Conceitos básicos, use `basics()`.

O seguinte exemplo retorna a saída do elemento chamado `bar` na etapa chamada `foo`:

```json
"[steps('foo').bar]"
```

### <a name="location"></a>location
Retorna a localização selecionada na etapa Conceitos básicos ou o contexto atual.

O seguinte exemplo poderá retornar `"westus"`:

```json
"[location()]"
```

## <a name="string-functions"></a>Funções de cadeia de caracteres
Essas funções podem ser usadas apenas com cadeias de caracteres JSON.

### <a name="concat"></a>concat
Concatena uma ou mais cadeias de caracteres.

Por exemplo, se o valor de saída `element1` for `"bar"`, este exemplo retornará a cadeia de caracteres `"foobar!"`:

```json
"[concat('foo', steps('step1').element1, '!')]"
```

### <a name="substring"></a>substring
Retorna a subcadeia de caracteres da cadeia de caracteres especificada. A subcadeia de caracteres começa no índice especificado e tem o comprimento especificado.

O exemplo a seguir retorna `"ftw"`:

```json
"[substring('azure-ftw!!!1one', 6, 3)]"
```

### <a name="replace"></a>substitui
Retorna uma cadeia de caracteres na qual todas as ocorrências da cadeia de caracteres especificada na cadeia de caracteres atual são substituídas por outra cadeia de caracteres.

O exemplo a seguir retorna `"Everything is awesome!"`:

```json
"[replace('Everything is terrible!', 'terrible', 'awesome')]"
```

### <a name="guid"></a>GUID
Gera uma cadeia de caracteres global exclusiva (GUID).

O seguinte exemplo poderá retornar `"c7bc8bdc-7252-4a82-ba53-7c468679a511"`:

```json
"[guid()]"
```

### <a name="tolower"></a>toLower
Retorna uma cadeia de caracteres convertida em minúsculas.

O exemplo a seguir retorna `"foobar"`:

```json
"[toLower('FOOBAR')]"
```

### <a name="toupper"></a>toUpper
Retorna uma cadeia de caracteres convertida em maiúsculas.

O exemplo a seguir retorna `"FOOBAR"`:

```json
"[toUpper('foobar')]"
```

## <a name="collection-functions"></a>Funções de coleção
Essas funções podem ser usadas com coleções, como cadeias de caracteres JSON, matrizes e objetos.

### <a name="contains"></a>contains
Retorna `true` se uma cadeia de caracteres contém a subcadeia de caracteres especificada, uma matriz contém o valor especificado ou um objeto contém a chave especificada.

#### <a name="example-1-string"></a>Exemplo 1: cadeia de caracteres
O exemplo a seguir retorna `true`:

```json
"[contains('foobar', 'foo')]"
```

#### <a name="example-2-array"></a>Exemplo 2: matriz
Suponha que `element1` retorne `[1, 2, 3]`. O exemplo a seguir retorna `false`:

```json
"[contains(steps('foo').element1, 4)]"
```

#### <a name="example-3-object"></a>Exemplo 3: objeto
Suponha que `element1` retorne:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

O exemplo a seguir retorna `true`:

```json
"[contains(steps('foo').element1, 'key1')]"
```

### <a name="length"></a>length
Retorna o número de caracteres em uma cadeia de caracteres, o número de valores em uma matriz ou o número de chaves em um objeto.

#### <a name="example-1-string"></a>Exemplo 1: cadeia de caracteres
O exemplo a seguir retorna `6`:

```json
"[length('foobar')]"
```

#### <a name="example-2-array"></a>Exemplo 2: matriz
Suponha que `element1` retorne `[1, 2, 3]`. O exemplo a seguir retorna `3`:

```json
"[length(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>Exemplo 3: objeto
Suponha que `element1` retorne:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

O exemplo a seguir retorna `2`:

```json
"[length(steps('foo').element1)]"
```

### <a name="empty"></a>empty
Retorna `true` se a cadeia de caracteres, matriz ou objeto é nulo ou vazio.

#### <a name="example-1-string"></a>Exemplo 1: cadeia de caracteres
O exemplo a seguir retorna `true`:

```json
"[empty('')]"
```

#### <a name="example-2-array"></a>Exemplo 2: matriz
Suponha que `element1` retorne `[1, 2, 3]`. O exemplo a seguir retorna `false`:

```json
"[empty(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>Exemplo 3: objeto
Suponha que `element1` retorne:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

O exemplo a seguir retorna `false`:

```json
"[empty(steps('foo').element1)]"
```

#### <a name="example-4-null-and-undefined"></a>Exemplo 4: nulo e indefinido
Suponha que `element1` é `null` ou indefinido. O exemplo a seguir retorna `true`:

```json
"[empty(steps('foo').element1)]"
```

### <a name="first"></a>first
Retorna o primeiro caractere da cadeia de caracteres especificada, o primeiro valor da matriz especificada ou a primeira chave e valor do objeto especificado.

#### <a name="example-1-string"></a>Exemplo 1: cadeia de caracteres
O exemplo a seguir retorna `"f"`:

```json
"[first('foobar')]"
```

#### <a name="example-2-array"></a>Exemplo 2: matriz
Suponha que `element1` retorne `[1, 2, 3]`. O exemplo a seguir retorna `1`:

```json
"[first(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>Exemplo 3: objeto
Suponha que `element1` retorne:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```
O exemplo a seguir retorna `{"key1": "foobar"}`:

```json
"[first(steps('foo').element1)]"
```

### <a name="last"></a>last
Retorna o último caractere da cadeia de caracteres especificada, o último valor da matriz especificada ou a última chave e valor do objeto especificado.

#### <a name="example-1-string"></a>Exemplo 1: cadeia de caracteres
O exemplo a seguir retorna `"r"`:

```json
"[last('foobar')]"
```

#### <a name="example-2-array"></a>Exemplo 2: matriz
Suponha que `element1` retorne `[1, 2, 3]`. O exemplo a seguir retorna `2`:

```json
"[last(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>Exemplo 3: objeto
Suponha que `element1` retorne:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

O exemplo a seguir retorna `{"key2": "raboof"}`:

```json
"[last(steps('foo').element1)]"
```

### <a name="take"></a>take
Retorna um número especificado de caracteres contíguos do início da cadeia de caracteres, um número especificado de valores contíguos do início da matriz ou um número especificado de chaves e valores contíguos do início do objeto.

#### <a name="example-1-string"></a>Exemplo 1: cadeia de caracteres
O exemplo a seguir retorna `"foo"`:

```json
"[take('foobar', 3)]"
```

#### <a name="example-2-array"></a>Exemplo 2: matriz
Suponha que `element1` retorne `[1, 2, 3]`. O exemplo a seguir retorna `[1, 2]`:

```json
"[take(steps('foo').element1, 2)]"
```

#### <a name="example-3-object"></a>Exemplo 3: objeto
Suponha que `element1` retorne:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

O exemplo a seguir retorna `{"key1": "foobar"}`:

```json
"[take(steps('foo').element1, 1)]"
```

### <a name="skip"></a>skip
Ignora um número especificado de elementos em uma coleção e, em seguida, retorna os elementos restantes.

#### <a name="example-1-string"></a>Exemplo 1: cadeia de caracteres
O exemplo a seguir retorna `"bar"`:

```json
"[skip('foobar', 3)]"
```

#### <a name="example-2-array"></a>Exemplo 2: matriz
Suponha que `element1` retorne `[1, 2, 3]`. O exemplo a seguir retorna `[3]`:

```json
"[skip(steps('foo').element1, 2)]"
```

#### <a name="example-3-object"></a>Exemplo 3: objeto
Suponha que `element1` retorne:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```
O exemplo a seguir retorna `{"key2": "raboof"}`:

```json
"[skip(steps('foo').element1, 1)]"
```

## <a name="logical-functions"></a>Funções lógicas
Essas funções podem ser usadas em condicionais. Algumas funções podem não dar suporte a todos os tipos de dados JSON.

### <a name="equals"></a>equals
Retorna `true` se ambos os parâmetros têm o mesmo tipo e valor. Essa função dá suporte a todos os tipos de dados JSON.

O exemplo a seguir retorna `true`:

```json
"[equals(0, 0)]"
```

O exemplo a seguir retorna `true`:

```json
"[equals('foo', 'foo')]"
```

O exemplo a seguir retorna `false`:

```json
"[equals('abc', ['a', 'b', 'c'])]"
```

### <a name="less"></a>less
Retorna `true` se o primeiro parâmetro é estritamente menor que o segundo parâmetro. Essa função dá suporte apenas a parâmetros dos tipos número e cadeia de caracteres.

O exemplo a seguir retorna `true`:

```json
"[less(1, 2)]"
```

O exemplo a seguir retorna `false`:

```json
"[less('9', '10')]"
```

### <a name="lessorequals"></a>lessOrEquals
Retorna `true` se o primeiro parâmetro é menor ou igual ao segundo parâmetro. Essa função dá suporte apenas a parâmetros dos tipos número e cadeia de caracteres.

O exemplo a seguir retorna `true`:

```json
"[lessOrEquals(2, 2)]"
```

### <a name="greater"></a>greater
Retorna `true` se o primeiro parâmetro é estritamente maior que o segundo parâmetro. Essa função dá suporte apenas a parâmetros dos tipos número e cadeia de caracteres.

O exemplo a seguir retorna `false`:

```json
"[greater(1, 2)]"
```

O exemplo a seguir retorna `true`:

```json
"[greater('9', '10')]"
```

### <a name="greaterorequals"></a>greaterOrEquals
Retorna `true` se o primeiro parâmetro é maior ou igual ao segundo parâmetro. Essa função dá suporte apenas a parâmetros dos tipos número e cadeia de caracteres.

O exemplo a seguir retorna `true`:

```json
"[greaterOrEquals(2, 2)]"
```

### <a name="and"></a>e
Retorna `true` se todos os parâmetros são avaliados como `true`. Essa função dá suporte apenas a dois ou mais parâmetros do tipo booliano.

O exemplo a seguir retorna `true`:

```json
"[and(equals(0, 0), equals('foo', 'foo'), less(1, 2))]"
```

O exemplo a seguir retorna `false`:

```json
"[and(equals(0, 0), greater(1, 2))]"
```

### <a name="or"></a>ou o
Retorna `true` se, pelo menos, um dos parâmetros é avaliado como `true`. Essa função dá suporte apenas a dois ou mais parâmetros do tipo booliano.

O exemplo a seguir retorna `true`:

```json
"[or(equals(0, 0), equals('foo', 'foo'), less(1, 2))]"
```

O exemplo a seguir retorna `true`:

```json
"[or(equals(0, 0), greater(1, 2))]"
```

### <a name="not"></a>não
Retorna `true` se o parâmetro é avaliado como `false`. Essa função dá suporte apenas a parâmetros do tipo booliano.

O exemplo a seguir retorna `true`:

```json
"[not(false)]"
```

O exemplo a seguir retorna `false`:

```json
"[not(equals(0, 0))]"
```

### <a name="coalesce"></a>coalesce
Retorna o valor do primeiro parâmetro não nulo. Essa função dá suporte a todos os tipos de dados JSON.

Suponha que `element1` e `element2` são indefinidos. O exemplo a seguir retorna `"foobar"`:

```json
"[coalesce(steps('foo').element1, steps('foo').element2, 'foobar')]"
```

## <a name="conversion-functions"></a>Funções de conversão
Essas funções podem ser usadas para converter valores entre tipos de dados JSON e codificações.

### <a name="int"></a>int
Converte o parâmetro em um inteiro. Essa função dá suporte a parâmetros dos tipos número e cadeia de caracteres.

O exemplo a seguir retorna `1`:

```json
"[int('1')]"
```

O exemplo a seguir retorna `2`:

```json
"[int(2.9)]"
```

### <a name="float"></a>flutuante
Converte o parâmetro em um ponto flutuante. Essa função dá suporte a parâmetros dos tipos número e cadeia de caracteres.

O exemplo a seguir retorna `1.0`:

```json
"[float('1.0')]"
```

O exemplo a seguir retorna `2.9`:

```json
"[float(2.9)]"
```

### <a name="string"></a>string
Converte o parâmetro em uma cadeia de caracteres. Essa função dá suporte a parâmetros de todos os tipos de dados JSON.

O exemplo a seguir retorna `"1"`:

```json
"[string(1)]"
```

O exemplo a seguir retorna `"2.9"`:

```json
"[string(2.9)]"
```

O exemplo a seguir retorna `"[1,2,3]"`:

```json
"[string([1,2,3])]"
```

O exemplo a seguir retorna `"{"foo":"bar"}"`:

```json
"[string({\"foo\":\"bar\"})]"
```

### <a name="bool"></a>bool
Converte o parâmetro em um booliano. Essa função dá suporte a parâmetros dos tipos número, cadeia de caracteres e booliano. Semelhante aos boolianos no JavaScript, qualquer valor, exceto `0` ou `'false'`, retorna `true`.

O exemplo a seguir retorna `true`:

```json
"[bool(1)]"
```

O exemplo a seguir retorna `false`:

```json
"[bool(0)]"
```

O exemplo a seguir retorna `true`:

```json
"[bool(true)]"
```

O exemplo a seguir retorna `true`:

```json
"[bool('true')]"
```

### <a name="parse"></a>analisar
Converte o parâmetro em um tipo nativo. Em outras palavras, essa função é o inverso de `string()`. Essa função dá suporte apenas a parâmetros do tipo cadeia de caracteres.

O exemplo a seguir retorna `1`:

```json
"[parse('1')]"
```

O exemplo a seguir retorna `true`:

```json
"[parse('true')]"
```

O exemplo a seguir retorna `[1,2,3]`:

```json
"[parse('[1,2,3]')]"
```

O exemplo a seguir retorna `{"foo":"bar"}`:

```json
"[parse('{\"foo\":\"bar\"}')]"
```

### <a name="encodebase64"></a>encodeBase64
Codifica o parâmetro em uma cadeia de caracteres codificada em base 64. Essa função dá suporte apenas a parâmetros do tipo cadeia de caracteres.

O exemplo a seguir retorna `"Zm9vYmFy"`:

```json
"[encodeBase64('foobar')]"
```

### <a name="decodebase64"></a>decodeBase64
Decodifica o parâmetro de uma cadeia de caracteres codificada em base 64. Essa função dá suporte apenas a parâmetros do tipo cadeia de caracteres.

O exemplo a seguir retorna `"foobar"`:

```json
"[decodeBase64('Zm9vYmFy')]"
```

### <a name="encodeuricomponent"></a>encodeUriComponent
Codifica o parâmetro em uma cadeia de caracteres codificada em URL. Essa função dá suporte apenas a parâmetros do tipo cadeia de caracteres.

O exemplo a seguir retorna `"https%3A%2F%2Fportal.azure.com%2F"`:

```json
"[encodeUriComponent('https://portal.azure.com/')]"
```

### <a name="decodeuricomponent"></a>decodeUriComponent
Decodifica o parâmetro de uma cadeia de caracteres codificada em URL. Essa função dá suporte apenas a parâmetros do tipo cadeia de caracteres.

O exemplo a seguir retorna `"https://portal.azure.com/"`:

```json
"[decodeUriComponent('https%3A%2F%2Fportal.azure.com%2F')]"
```

## <a name="math-functions"></a>Funções matemáticas
### <a name="add"></a>Adicionar
Adiciona dois números e retorna o resultado.

O exemplo a seguir retorna `3`:

```json
"[add(1, 2)]"
```

### <a name="sub"></a>sub
Subtrai o segundo número do primeiro número e retorna o resultado.

O exemplo a seguir retorna `1`:

```json
"[sub(3, 2)]"
```

### <a name="mul"></a>mul
Multiplica dois números e retorna o resultado.

O exemplo a seguir retorna `6`:

```json
"[mul(2, 3)]"
```

### <a name="div"></a>div
Divide o primeiro número pelo segundo número e retorna o resultado. O resultado é sempre um inteiro.

O exemplo a seguir retorna `2`:

```json
"[div(6, 3)]"
```

### <a name="mod"></a>mod
Divide o primeiro número pelo segundo número e retorna a sobra.

O exemplo a seguir retorna `0`:

```json
"[mod(6, 3)]"
```

O exemplo a seguir retorna `2`:

```json
"[mod(6, 4)]"
```

### <a name="min"></a>Min
Retorna o menor dos dois números.

O exemplo a seguir retorna `1`:

```json
"[min(1, 2)]"
```

### <a name="max"></a>max
Retorna o maior dos dois números.

O exemplo a seguir retorna `2`:

```json
"[max(1, 2)]"
```

### <a name="range"></a>range
Gera uma sequência de números integrais dentro do intervalo especificado.

O exemplo a seguir retorna `[1,2,3]`:

```json
"[range(1, 3)]"
```

### <a name="rand"></a>rand
Retorna um número integral aleatório dentro do intervalo especificado. Essa função não gera números aleatórios criptograficamente seguros.

O seguinte exemplo poderá retornar `42`:

```json
"[rand(-100, 100)]"
```

### <a name="floor"></a>floor
Retorna o maior inteiro menor ou igual ao número especificado.

O exemplo a seguir retorna `3`:

```json
"[floor(3.14)]"
```

### <a name="ceil"></a>ceil
Retorna o maior inteiro maior ou igual ao número especificado.

O exemplo a seguir retorna `4`:

```json
"[ceil(3.14)]"
```

## <a name="date-functions"></a>Funções de data
### <a name="utcnow"></a>utcNow
Retorna uma cadeia de caracteres no formato ISO 8601 da data e hora atuais no computador local.

O seguinte exemplo poderá retornar `"1990-12-31T23:59:59.000Z"`:

```json
"[utcNow()]"
```

### <a name="addseconds"></a>addSeconds
Adiciona um número integral de segundos ao carimbo de data/hora especificado.

O exemplo a seguir retorna `"1991-01-01T00:00:00.000Z"`:

```json
"[addSeconds('1990-12-31T23:59:60Z', 1)]"
```

### <a name="addminutes"></a>addMinutes
Adiciona um número integral de minutos ao carimbo de data/hora especificado.

O exemplo a seguir retorna `"1991-01-01T00:00:59.000Z"`:

```json
"[addMinutes('1990-12-31T23:59:59Z', 1)]"
```

### <a name="addhours"></a>addHours
Adiciona um número integral de horas ao carimbo de data/hora especificado.

O exemplo a seguir retorna `"1991-01-01T00:59:59.000Z"`:

```json
"[addHours('1990-12-31T23:59:59Z', 1)]"
```

## <a name="next-steps"></a>Próximas etapas
* Para obter uma introdução ao Azure Resource Manager, consulte [Visão geral do Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

