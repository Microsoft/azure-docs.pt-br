---
title: Criar funções matemáticas de definição de interface do usuário
description: Descreve as funções a serem usadas ao executar operações matemáticas.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: 022e59d847a4d89b4243223637fc6fd1e73255a9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "87095722"
---
# <a name="createuidefinition-math-functions"></a>Funções matemáticas CreateUiDefinition

As funções permitem que você execute operações matemáticas.

## <a name="add"></a>add

Adiciona dois números e retorna o resultado.

O exemplo a seguir retorna `3`:

```json
"[add(1, 2)]"
```

## <a name="ceil"></a>ceil

Retorna o maior inteiro maior ou igual ao número especificado.

O exemplo a seguir retorna `4`:

```json
"[ceil(3.14)]"
```

## <a name="div"></a>div

Divide o primeiro número pelo segundo número e retorna o resultado. O resultado é sempre um inteiro.

O exemplo a seguir retorna `2`:

```json
"[div(6, 3)]"
```

## <a name="floor"></a>floor

Retorna o maior inteiro menor ou igual ao número especificado.

O exemplo a seguir retorna `3`:

```json
"[floor(3.14)]"
```

## <a name="max"></a>max

Retorna o maior dos dois números.

O exemplo a seguir retorna `2`:

```json
"[max(1, 2)]"
```

## <a name="min"></a>min

Retorna o menor dos dois números.

O exemplo a seguir retorna `1`:

```json
"[min(1, 2)]"
```

## <a name="mod"></a>mod

Divide o primeiro número pelo segundo número e retorna a sobra.

O exemplo a seguir retorna `0`:

```json
"[mod(6, 3)]"
```

O exemplo a seguir retorna `2`:

```json
"[mod(6, 4)]"
```

## <a name="mul"></a>mul

Multiplica dois números e retorna o resultado.

O exemplo a seguir retorna `6`:

```json
"[mul(2, 3)]"
```

## <a name="rand"></a>rand

Retorna um número integral aleatório dentro do intervalo especificado. Essa função não gera números aleatórios criptograficamente seguros.

O seguinte exemplo poderá retornar `42`:

```json
"[rand(-100, 100)]"
```

## <a name="range"></a>range

Gera uma sequência de números integrais dentro do intervalo especificado.

O exemplo a seguir retorna `[1,2,3]`:

```json
"[range(1, 3)]"
```

## <a name="sub"></a>sub

Subtrai o segundo número do primeiro número e retorna o resultado.

O exemplo a seguir retorna `1`:

```json
"[sub(3, 2)]"
```

## <a name="next-steps"></a>Próximas etapas

* Para obter uma introdução ao Azure Resource Manager, consulte [Visão geral do Azure Resource Manager](../management/overview.md).
