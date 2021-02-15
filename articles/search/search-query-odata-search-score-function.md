---
title: Pesquisa de OData. referência de função de Pontuação
titleSuffix: Azure Cognitive Search
description: Sintaxe e documentação de referência para usar a função Search. Score no Azure Pesquisa Cognitiva consultas.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: 0a84d0310573a1210e21157102a445fff9244782
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88923969"
---
# <a name="odata-searchscore-function-in-azure-cognitive-search"></a>`search.score`Função OData no Azure pesquisa cognitiva

Quando você envia uma consulta para Pesquisa Cognitiva do Azure sem o [parâmetro **$OrderBy** ](search-query-odata-orderby.md), os resultados que retornam serão classificados em ordem decrescente por Pontuação de relevância. Mesmo quando você usa **$OrderBy**, a pontuação de relevância será usada para quebrar as ligações por padrão. No entanto, às vezes, é útil usar a pontuação de relevância como um critério de classificação inicial e alguns outros critérios como o desempate. A `search.score` função permite que você faça isso.

## <a name="syntax"></a>Sintaxe

A sintaxe para `search.score` em **$orderby** é `search.score()`. A função `search.score` não aceita nenhum parâmetro. Ele pode ser usado com o `asc` `desc` especificador de ordem de classificação, assim como qualquer outra cláusula no parâmetro **$OrderBy** . Ele pode aparecer em qualquer lugar na lista de critérios de classificação.

## <a name="example"></a>Exemplo

Classifique os hotéis em ordem decrescente por `search.score` e e `rating` , em seguida, em ordem crescente por distância das coordenadas fornecidas para que entre dois hotéis com classificações idênticas, o mais próximo é listado primeiro:

```odata-filter-expr
    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc
```

## <a name="next-steps"></a>Próximas etapas  

- [Visão geral da linguagem de expressão OData para Azure Pesquisa Cognitiva](query-odata-filter-orderby-syntax.md)
- [Referência de sintaxe de expressão OData para Pesquisa Cognitiva do Azure](search-query-odata-syntax-reference.md)
- [Pesquisar documentos &#40;API do Azure Pesquisa Cognitiva EST&#41;](/rest/api/searchservice/Search-Documents)