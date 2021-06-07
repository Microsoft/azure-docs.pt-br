---
author: brjohnstmsft
ms.service: cognitive-search
ms.topic: include
ms.date: 06/13/2018
ms.author: brjohnst
ms.openlocfilehash: 998d0f1a84dc9cb2a07fb55286c1089787a263e1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "80272923"
---
| Tipo de dados | Recursos permitidos em expressões lambda com `any` | Recursos permitidos em expressões lambda com `all` |
|---|---|---|
| `Collection(Edm.ComplexType)` | Tudo exceto `search.ismatch` e `search.ismatchscoring` | Idêntico |
| `Collection(Edm.String)` | Comparações com `eq` ou `search.in` <br/><br/> Combinando subexpressãos com `or` | Comparações com `ne` ou `not search.in()` <br/><br/> Combinando subexpressãos com `and` |
| `Collection(Edm.Boolean)` | Comparações com `eq` ou `ne` | Idêntico |
| `Collection(Edm.GeographyPoint)` | Usando `geo.distance` com `lt` ou `le` <br/><br/> `geo.intersects` <br/><br/> Combinando subexpressãos com `or` | Usando `geo.distance` com `gt` ou `ge` <br/><br/> `not geo.intersects(...)` <br/><br/> Combinando subexpressãos com `and` |
| `Collection(Edm.DateTimeOffset)`, `Collection(Edm.Double)`, `Collection(Edm.Int32)`, `Collection(Edm.Int64)` | Comparações usando,,,, `eq` `ne` `lt` `gt` `le` ou `ge` <br/><br/> Combinando comparações com outras subexpressãos usando `or` <br/><br/> Combinando comparações, exceto `ne` com outras subexpressãos usando `and` <br/><br/> Expressões usando combinações de `and` e `or` no [formato normal de disjunctive (DNF)](https://en.wikipedia.org/wiki/Disjunctive_normal_form) | Comparações usando,,,, `eq` `ne` `lt` `gt` `le` ou `ge` <br/><br/> Combinando comparações com outras subexpressãos usando `and` <br/><br/> Combinando comparações, exceto `eq` com outras subexpressãos usando `or` <br/><br/> Expressões usando combinações de `and` e `or` no [formato normal de conjuntiva (CNF)](https://en.wikipedia.org/wiki/Conjunctive_normal_form) |
