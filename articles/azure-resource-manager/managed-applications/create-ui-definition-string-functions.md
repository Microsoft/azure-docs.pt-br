---
title: Criar funções de cadeia de caracteres de definição de IU
description: Descreve as funções de cadeia de caracteres a serem usadas ao construir definições de interface do usuário para aplicativos gerenciados do Azure
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: c662948542c36cd93f889ca045ee245c15c7bb11
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "87095720"
---
# <a name="createuidefinition-string-functions"></a>Funções de cadeia de caracteres CreateUiDefinition

Essas funções a serem usadas com cadeias de caracteres JSON.

## <a name="concat"></a>concat

Concatena uma ou mais cadeias de caracteres.

Por exemplo, se o valor de saída `element1` for `"Contoso"`, este exemplo retornará a cadeia de caracteres `"Demo Contoso app"`:

```json
"[concat('Demo ', steps('step1').element1, ' app')]"
```

## <a name="endswith"></a>endsWith

Determina se uma cadeia de caracteres termina com um valor.

O exemplo a seguir retorna true.

```json
"[endsWith('tuvwxyz', 'xyz')]"
```

## <a name="guid"></a>guid

Gera uma cadeia de caracteres global exclusiva (GUID).

O exemplo a seguir retorna um valor como `"c7bc8bdc-7252-4a82-ba53-7c468679a511"` :

```json
"[guid()]"
```

## <a name="indexof"></a>indexOf

Retorna a primeira posição de um valor em uma cadeia de caracteres ou-1 se não for encontrado.

O exemplo a seguir retorna 2.

```json
"[indexOf('abcdef', 'cd')]"
```

## <a name="lastindexof"></a>lastIndexOf

Retorna a última posição de um valor em uma cadeia de caracteres ou-1 se não for encontrado.

O exemplo a seguir retorna 3.

```json
"[lastIndexOf('test', 't')]"
```

## <a name="replace"></a>substituir

Retorna uma cadeia de caracteres na qual todas as ocorrências da cadeia de caracteres especificada na cadeia de caracteres atual são substituídas por outra cadeia de caracteres.

O exemplo a seguir retorna `"Contoso.com web app"`:

```json
"[replace('Contoso.net web app', '.net', '.com')]"
```

## <a name="startswith"></a>startsWith

Determina se uma cadeia de caracteres começa com um valor.

O exemplo a seguir retorna true.

```json
"[startsWith('abcdefg', 'ab')]"
```

## <a name="substring"></a>substring

Retorna a subcadeia de caracteres da cadeia de caracteres especificada. A subcadeia de caracteres começa no índice especificado e tem o comprimento especificado.

O exemplo a seguir retorna `"web"`:

```json
"[substring('Contoso.com web app', 12, 3)]"
```

## <a name="tolower"></a>toLower

Retorna uma cadeia de caracteres convertida em minúsculas.

O exemplo a seguir retorna `"contoso"`:

```json
"[toLower('CONTOSO')]"
```

## <a name="toupper"></a>toUpper

Retorna uma cadeia de caracteres convertida em maiúsculas.

O exemplo a seguir retorna `"CONTOSO"`:

```json
"[toUpper('contoso')]"
```

## <a name="next-steps"></a>Próximas etapas

* Para obter uma introdução ao Azure Resource Manager, consulte [Visão geral do Azure Resource Manager](../management/overview.md).

