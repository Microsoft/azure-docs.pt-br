---
title: Criar funções de data de definição de interface do usuário
description: Descreve as funções a serem usadas ao trabalhar com valores de data.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: 80484fd15e51bae7036c43bd3ca8fe8167387ede
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87095726"
---
# <a name="createuidefinition-date-functions"></a>Funções de data de CreateUiDefinition

As funções a serem usadas ao trabalhar com valores de data.

## <a name="addhours"></a>addHours

Adiciona um número integral de horas ao carimbo de data/hora especificado.

O exemplo a seguir retorna `"1991-01-01T00:59:59.000Z"`:

```json
"[addHours('1990-12-31T23:59:59Z', 1)]"
```

## <a name="addminutes"></a>addMinutes

Adiciona um número integral de minutos ao carimbo de data/hora especificado.

O exemplo a seguir retorna `"1991-01-01T00:00:59.000Z"`:

```json
"[addMinutes('1990-12-31T23:59:59Z', 1)]"
```

## <a name="addseconds"></a>addSeconds
Adiciona um número integral de segundos ao carimbo de data/hora especificado.

O exemplo a seguir retorna `"1991-01-01T00:00:00.000Z"`:

```json
"[addSeconds('1990-12-31T23:59:60Z', 1)]"
```

## <a name="utcnow"></a>utcNow

Retorna uma cadeia de caracteres no formato ISO 8601 da data e hora atuais no computador local.

O seguinte exemplo poderá retornar `"1990-12-31T23:59:59.000Z"`:

```json
"[utcNow()]"
```

## <a name="next-steps"></a>Próximas etapas

* Para obter uma introdução ao Azure Resource Manager, consulte [Visão geral do Azure Resource Manager](../management/overview.md).
