---
title: Criar funções de conversão de definição de interface do usuário
description: Descreve as funções a serem usadas ao converter valores entre tipos de dados e codificações.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: b69cd35b27b343da08727b4c4ee9b4fd025e1df7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "87095727"
---
# <a name="createuidefinition-conversion-functions"></a>Funções de conversão CreateUiDefinition

Essas funções podem ser usadas para converter valores entre tipos de dados JSON e codificações.

## <a name="bool"></a>bool

Converte o parâmetro em um booliano. Essa função dá suporte a parâmetros dos tipos número, cadeia de caracteres e booliano. Semelhante a boolianos em JavaScript, qualquer valor, exceto `0` ou `'false'` retorna `true` .

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

## <a name="decodebase64"></a>decodeBase64

Decodifica o parâmetro de uma cadeia de caracteres codificada em base 64. Essa função dá suporte apenas a parâmetros do tipo cadeia de caracteres.

O exemplo a seguir retorna `"Contoso"`:

```json
"[decodeBase64('Q29udG9zbw==')]"
```

## <a name="decodeuricomponent"></a>decodeUriComponent

Decodifica o parâmetro de uma cadeia de caracteres codificada em URL. Essa função dá suporte apenas a parâmetros do tipo cadeia de caracteres.

O exemplo a seguir retorna `"https://portal.azure.com/"`:

```json
"[decodeUriComponent('https%3A%2F%2Fportal.azure.com%2F')]"
```

## <a name="encodebase64"></a>encodeBase64

Codifica o parâmetro em uma cadeia de caracteres codificada em base 64. Essa função dá suporte apenas a parâmetros do tipo cadeia de caracteres.

O exemplo a seguir retorna `"Q29udG9zbw=="`:

```json
"[encodeBase64('Contoso')]"
```

## <a name="encodeuricomponent"></a>encodeUriComponent

Codifica o parâmetro em uma cadeia de caracteres codificada em URL. Essa função dá suporte apenas a parâmetros do tipo cadeia de caracteres.

O exemplo a seguir retorna `"https%3A%2F%2Fportal.azure.com%2F"`:

```json
"[encodeUriComponent('https://portal.azure.com/')]"
```

## <a name="float"></a>FLOAT

Converte o parâmetro em um ponto flutuante. Essa função dá suporte a parâmetros dos tipos número e cadeia de caracteres.

O exemplo a seguir retorna `1.0`:

```json
"[float('1.0')]"
```

O exemplo a seguir retorna `2.9`:

```json
"[float(2.9)]"
```

## <a name="int"></a>INT

Converte o parâmetro em um inteiro. Essa função dá suporte a parâmetros dos tipos número e cadeia de caracteres.

O exemplo a seguir retorna `1`:

```json
"[int('1')]"
```

O exemplo a seguir retorna `2`:

```json
"[int(2.9)]"
```

## <a name="parse"></a>analisar

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

O exemplo a seguir retorna `{"type":"webapp"}`:

```json
"[parse('{\"type\":\"webapp\"}')]"
```

## <a name="string"></a>string

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

O exemplo a seguir retorna `"{"type":"webapp"}"`:

```json
"[string({\"type\":\"webapp\"})]"
```

## <a name="next-steps"></a>Próximas etapas

* Para obter uma introdução ao Azure Resource Manager, consulte [Visão geral do Azure Resource Manager](../management/overview.md).
