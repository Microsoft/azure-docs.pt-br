---
title: Referência da função search.in do OData
titleSuffix: Azure Cognitive Search
description: Sintaxe e documentação de referência para usar a função search.in no Azure Pesquisa Cognitiva consultas.
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
ms.openlocfilehash: 9ad6f89392846564631b70f0acfb5658a050be80
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "88922813"
---
# <a name="odata-searchin-function-in-azure-cognitive-search"></a>`search.in`Função OData no Azure pesquisa cognitiva

Um cenário comum em [expressões de filtro OData](query-odata-filter-orderby-syntax.md) é verificar se um único campo em cada documento é igual a um dos muitos valores possíveis. Por exemplo, é assim que alguns aplicativos implementam a [remoção de segurança](search-security-trimming-for-azure-search.md) , verificando um campo que contém uma ou mais IDs de entidade em relação a uma lista de IDs de entidade que representa o usuário que está emitindo a consulta. Uma maneira de escrever uma consulta como esta é usar os [`eq`](search-query-odata-comparison-operators.md) operadores e [`or`](search-query-odata-logical-operators.md) :

```odata-filter-expr
    group_ids/any(g: g eq '123' or g eq '456' or g eq '789')
```

No entanto, há uma maneira mais curta de escrever isso, usando a `search.in` função:

```odata-filter-expr
    group_ids/any(g: search.in(g, '123, 456, 789'))
```

> [!IMPORTANT]
> Além de ser mais curto e fácil de ler, o uso `search.in` do também fornece [benefícios de desempenho](#bkmk_performance) e evita determinadas [limitações de tamanho dos filtros](search-query-odata-filter.md#bkmk_limits) quando há centenas ou até milhares de valores a serem incluídos no filtro. Por esse motivo, é altamente recomendável usar `search.in` em vez de uma disjunção mais complexa de expressões de igualdade.

> [!NOTE]
> A versão 4, 1 do padrão OData introduziu recentemente o [ `in` operador](https://docs.oasis-open.org/odata/odata/v4.01/cs01/part2-url-conventions/odata-v4.01-cs01-part2-url-conventions.html#_Toc505773230), que tem comportamento semelhante à `search.in` função no pesquisa cognitiva do Azure. No entanto, o Azure Pesquisa Cognitiva não oferece suporte a esse operador, portanto, você deve usar a `search.in` função em vez disso.

## <a name="syntax"></a>Sintaxe

O seguinte EBNF ([formulário estendido Backus-Naur](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) define a gramática da `search.in` função:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
search_in_call ::=
    'search.in(' variable ',' string_literal(',' string_literal)? ')'
```

Um diagrama de sintaxe interativa também está disponível:

> [!div class="nextstepaction"]
> [Diagrama de sintaxe do OData para Pesquisa Cognitiva do Azure](https://azuresearch.github.io/odata-syntax-diagram/#search_in_call)

> [!NOTE]
> Consulte [referência de sintaxe de expressão OData para pesquisa cognitiva do Azure](search-query-odata-syntax-reference.md) para o EBNF completo.

A `search.in` função testa se um determinado campo de cadeia de caracteres ou variável de intervalo é igual a uma de uma determinada lista de valores. A igualdade entre a variável e cada valor na lista é determinada de uma maneira que diferencia maiúsculas de minúsculas, da mesma maneira que para o `eq` operador. Portanto, uma expressão como `search.in(myfield, 'a, b, c')` é equivalente a `myfield eq 'a' or myfield eq 'b' or myfield eq 'c'`, exceto que `search.in` produzirá um desempenho muito melhor.

Há duas sobrecargas da `search.in` função:

- `search.in(variable, valueList)`
- `search.in(variable, valueList, delimiters)`

Os parâmetros são definidos na tabela a seguir:

| Nome do parâmetro | Tipo | Descrição |
| --- | --- | --- |
| `variable` | `Edm.String` | Uma referência de campo de cadeia de caracteres (ou uma variável de intervalo em um campo de coleção de cadeia de caracteres no caso em que `search.in` é usada dentro de uma `any` `all` expressão ou). |
| `valueList` | `Edm.String` | Uma cadeia de caracteres que contém uma lista delimitada de valores para corresponder ao `variable` parâmetro. Se o `delimiters` parâmetro não for especificado, os delimitadores padrão serão espaço e vírgula. |
| `delimiters` | `Edm.String` | Uma cadeia de caracteres em que cada caractere é tratado como um separador ao analisar o `valueList` parâmetro. O valor padrão desse parâmetro é o `' ,'` que significa que todos os valores com espaços e/ou vírgulas entre eles serão separados. Se você precisar usar separadores diferentes de espaços e vírgulas porque seus valores incluem esses caracteres, você pode especificar delimitadores alternativos, como `'|'` nesse parâmetro. |

<a name="bkmk_performance"></a>

### <a name="performance-of-searchin"></a>Desempenho de `search.in`

Se você usar `search.in`, espere um tempo de resposta abaixo de um segundo quando o segundo parâmetro contiver uma lista de centenas ou milhares de valores. Não há nenhum limite explícito para o número de itens que você pode passar `search.in` , embora ainda esteja limitado pelo tamanho máximo da solicitação. No entanto, a latência aumenta à medida em que cresce o número de valores.

## <a name="examples"></a>Exemplos

Localize todos os hotéis com o nome igual a ' Sea View Motel ' ou ' Budget Orca '. As frases contêm espaços, que é um delimitador padrão. Você pode especificar um delimitador alternativo entre aspas simples como o terceiro parâmetro de cadeia de caracteres:  

```odata-filter-expr
    search.in(HotelName, 'Sea View motel,Budget hotel', ',')
```

Localize todos os hotéis com o nome igual ao ' Sea View Motel ' ou ao ' Budget Hotel ', separados por ' | '):

```odata-filter-expr
    search.in(HotelName, 'Sea View motel|Budget hotel', '|')
```

Localize todos os hotéis com salas que têm a marca ' WiFi ' ou ' Tub ':

```odata-filter-expr
    Rooms/any(room: room/Tags/any(tag: search.in(tag, 'wifi, tub')))
```

Encontre uma correspondência em frases em uma coleção, como "racks de toalha aquecidos" ou "hairdryer incluídos" nas marcas.

```odata-filter-expr
    Rooms/any(room: room/Tags/any(tag: search.in(tag, 'heated towel racks,hairdryer included', ','))
```

Localize todos os hotéis sem a marca ' motel ' ou ' cabin':

```odata-filter-expr
    Tags/all(tag: not search.in(tag, 'motel, cabin'))
```

## <a name="next-steps"></a>Próximas etapas  

- [Filtros no Azure Pesquisa Cognitiva](search-filters.md)
- [Visão geral da linguagem de expressão OData para Azure Pesquisa Cognitiva](query-odata-filter-orderby-syntax.md)
- [Referência de sintaxe de expressão OData para Pesquisa Cognitiva do Azure](search-query-odata-syntax-reference.md)
- [Pesquisar documentos &#40;API REST do Azure Pesquisa Cognitiva&#41;](/rest/api/searchservice/Search-Documents)