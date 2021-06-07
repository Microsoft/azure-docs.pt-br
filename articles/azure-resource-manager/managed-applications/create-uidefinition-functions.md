---
title: Criar funções de definição de interface do usuário
description: Descreve as funções para uso na criação de definições de interface do usuário para aplicativos gerenciados do Azure
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: e4255f0d42e28a72ad55d9b7f81d0dc49b2950cb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "87040997"
---
# <a name="createuidefinition-functions"></a>Funções de CreateUiDefinition

Este artigo fornece uma visão geral das funções com suporte para o CreateUiDefinition.

## <a name="function-syntax"></a>Sintaxe da função

Para usar uma função, coloque a chamada entre colchetes. Por exemplo:

```json
"[function()]"
```

Cadeias de caracteres e outras funções podem ser referenciadas como parâmetros de uma função, mas as cadeias de caracteres devem estar entre aspas simples. Por exemplo:

```json
"[fn1(fn2(), 'demo text')]"
```

Quando aplicável, referencie propriedades da saída de uma função usando o operador de ponto. Por exemplo:

```json
"[func().prop1]"
```

## <a name="collection-functions"></a>Funções de coleção

Essas funções podem ser usadas com coleções, como cadeias de caracteres JSON, matrizes e objetos.

* [contains](create-ui-definition-collection-functions.md#contains)
* [empty](create-ui-definition-collection-functions.md#empty)
* [filter](create-ui-definition-collection-functions.md#filter)
* [first](create-ui-definition-collection-functions.md#first)
* [last](create-ui-definition-collection-functions.md#last)
* [length](create-ui-definition-collection-functions.md#length)
* [map](create-ui-definition-collection-functions.md#map)
* [skip](create-ui-definition-collection-functions.md#skip)
* [split](create-ui-definition-collection-functions.md#split)
* [take](create-ui-definition-collection-functions.md#take)

## <a name="conversion-functions"></a>Funções de conversão

Essas funções podem ser usadas para converter valores entre tipos de dados JSON e codificações.

* [bool](create-ui-definition-conversion-functions.md#bool)
* [decodeBase64](create-ui-definition-conversion-functions.md#decodebase64)
* [decodeUriComponent](create-ui-definition-conversion-functions.md#decodeuricomponent)
* [encodeBase64](create-ui-definition-conversion-functions.md#encodebase64)
* [encodeUriComponent](create-ui-definition-conversion-functions.md#encodeuricomponent)
* [float](create-ui-definition-conversion-functions.md#float)
* [int](create-ui-definition-conversion-functions.md#int)
* [parse](create-ui-definition-conversion-functions.md#parse)
* [cadeia de caracteres](create-ui-definition-conversion-functions.md#string)

## <a name="date-functions"></a>Funções de data

* [addHours](create-ui-definition-date-functions.md#addhours)
* [addMinutes](create-ui-definition-date-functions.md#addminutes)
* [addSeconds](create-ui-definition-date-functions.md#addseconds)
* [utcNow](create-ui-definition-date-functions.md#utcnow)

## <a name="logical-functions"></a>Funções lógicas

Essas funções podem ser usadas em condicionais. Algumas funções podem não dar suporte a todos os tipos de dados JSON.

* [and](create-ui-definition-logical-functions.md#and)
* [coalesce](create-ui-definition-logical-functions.md#coalesce)
* [equals](create-ui-definition-logical-functions.md#equals)
* [greater](create-ui-definition-logical-functions.md#greater)
* [greaterOrEquals](create-ui-definition-logical-functions.md#greaterorequals)
* [if](create-ui-definition-logical-functions.md#if)
* [less](create-ui-definition-logical-functions.md#less)
* [lessOrEquals](create-ui-definition-logical-functions.md#lessorequals)
* [not](create-ui-definition-logical-functions.md#not)
* [or](create-ui-definition-logical-functions.md#or)

## <a name="math-functions"></a>Funções matemáticas

* [add](create-ui-definition-math-functions.md#add)
* [ceil](create-ui-definition-math-functions.md#ceil)
* [div](create-ui-definition-math-functions.md#div)
* [Floor](create-ui-definition-math-functions.md#floor)
* [max](create-ui-definition-math-functions.md#max)
* [min](create-ui-definition-math-functions.md#min)
* [mod](create-ui-definition-math-functions.md#mod)
* [mul](create-ui-definition-math-functions.md#mul)
* [rand](create-ui-definition-math-functions.md#rand)
* [range](create-ui-definition-math-functions.md#range)
* [sub](create-ui-definition-math-functions.md#sub)

## <a name="referencing-functions"></a>Referenciando funções

* [algumas](create-ui-definition-referencing-functions.md#basics)
* [local](create-ui-definition-referencing-functions.md#location)
* [resourceGroup](create-ui-definition-referencing-functions.md#resourcegroup)
* [tarefas](create-ui-definition-referencing-functions.md#steps)
* [subscription](create-ui-definition-referencing-functions.md#subscription)

## <a name="string-functions"></a>Funções de cadeia de caracteres

* [concat](create-ui-definition-string-functions.md#concat)
* [endsWith](create-ui-definition-string-functions.md#endswith)
* [guid](create-ui-definition-string-functions.md#guid)
* [indexOf](create-ui-definition-string-functions.md#indexof)
* [lastIndexOf](create-ui-definition-string-functions.md#lastindexof)
* [replace](create-ui-definition-string-functions.md#replace)
* [startsWith](create-ui-definition-string-functions.md#startswith)
* [substring](create-ui-definition-string-functions.md#substring)
* [toLower](create-ui-definition-string-functions.md#tolower)
* [toUpper](create-ui-definition-string-functions.md#toupper)

## <a name="next-steps"></a>Próximas etapas

* Para obter uma introdução ao Azure Resource Manager, consulte [Visão geral do Azure Resource Manager](../management/overview.md).
