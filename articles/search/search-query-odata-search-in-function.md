---
title: Referência de função do OData search.in - Azure Search
description: Função search.in do OData em consultas de pesquisa do Azure.
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: brjohnstmsft
ms.author: brjohnst
ms.manager: cgronlun
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
ms.openlocfilehash: a61291e547021077341a5f1b3db7422afa5b9440
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67449981"
---
# <a name="odata-searchin-function-in-azure-search"></a>OData `search.in` função no Azure Search

Um cenário comum em [expressões de filtro OData](query-odata-filter-orderby-syntax.md) é verificar se um único campo em cada documento é igual a um dos muitos valores possíveis. Por exemplo, isso é como implementam alguns aplicativos [filtragem de segurança](search-security-trimming-for-azure-search.md) – Verificando um campo que contém uma ou mais IDs de entidade de segurança em relação a uma lista de IDs de entidade que representa o usuário emite a consulta. Uma maneira de escrever uma consulta como esta é usar o [ `eq` ](search-query-odata-comparison-operators.md) e [ `or` ](search-query-odata-logical-operators.md) operadores:

    group_ids/any(g: g eq '123' or g eq '456' or g eq '789')

No entanto, há uma maneira mais curta para escrever isso, usando o `search.in` função:

    group_ids/any(g: search.in(g, '123, 456, 789'))

> [!IMPORTANT]
> Além de ser mais curta e fácil de ler, usando `search.in` também fornece [benefícios de desempenho](#bkmk_performance) e evita certos [as limitações dos filtros de tamanho](search-query-odata-filter.md#bkmk_limits) quando houver centenas ou milhares de valores para incluir no filtro. Por esse motivo, é altamente recomendável usar `search.in` em vez de uma disjunção mais complexa de expressões de igualdade.

> [!NOTE]
> Versão 4.01 do OData padrão introduziu recentemente o [ `in` operador](https://docs.oasis-open.org/odata/odata/v4.01/cs01/part2-url-conventions/odata-v4.01-cs01-part2-url-conventions.html#_Toc505773230), que tem um comportamento semelhante como o `search.in` função no Azure Search. No entanto, Azure Search não suporta esse operador, portanto, você deve usar o `search.in` function em vez disso.

## <a name="syntax"></a>Sintaxe

A seguir EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) define a gramática do `search.in` função:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
search_in_call ::=
    'search.in(' variable ',' string_literal(',' string_literal)? ')'
```

Um diagrama de sintaxe interativa também está disponível:

> [!div class="nextstepaction"]
> [Diagrama de sintaxe do OData para o Azure Search](https://azuresearch.github.io/odata-syntax-diagram/#search_in_call)

> [!NOTE]
> Ver [referência de sintaxe de expressão OData para o Azure Search](search-query-odata-syntax-reference.md) para o EBNF completa.

O `search.in` função testa se um campo de cadeia de caracteres fornecida ou variável de intervalo é igual a um de uma determinada lista de valores. A igualdade entre a variável e cada valor na lista é determinada de maneira diferencia maiusculas de minúsculas, da mesma forma que para o `eq` operador. Portanto, uma expressão como `search.in(myfield, 'a, b, c')` é equivalente a `myfield eq 'a' or myfield eq 'b' or myfield eq 'c'`, exceto que `search.in` produzirá um desempenho muito melhor.

Há duas sobrecargas do `search.in` função:

- `search.in(variable, valueList)`
- `search.in(variable, valueList, delimiters)`

Os parâmetros são definidos na tabela a seguir:

| Nome do parâmetro | Type | DESCRIÇÃO |
| --- | --- | --- |
| `variable` | `Edm.String` | Uma referência de campo de cadeia de caracteres (ou uma variável de intervalo de um campo de coleção de cadeia de caracteres no caso em que `search.in` é usado dentro de um `any` ou `all` expressão). |
| `valueList` | `Edm.String` | Uma cadeia de caracteres que contém uma lista delimitada de valores para correspondência com o `variable` parâmetro. Se o `delimiters` parâmetro não for especificado, os delimitadores padrão são espaço e vírgula. |
| `delimiters` | `Edm.String` | Uma cadeia de caracteres em que cada caractere é tratado como um separador ao analisar o `valueList` parâmetro. O valor padrão desse parâmetro é `' ,'` o que significa que todos os valores com espaços de e/ou vírgulas entre eles serão separados. Se você precisar usar separadores diferentes espaços e vírgulas como seus valores incluem esses caracteres, você pode especificar como delimitadores alternativos `'|'` nesse parâmetro. |

<a name="bkmk_performance"></a>

### <a name="performance-of-searchin"></a>Desempenho de `search.in`

Se você usar `search.in`, espere um tempo de resposta abaixo de um segundo quando o segundo parâmetro contiver uma lista de centenas ou milhares de valores. Não há nenhum limite explícito no número de itens você pode passar para `search.in`, embora você ainda estiver limitado pelo tamanho máximo da solicitação. No entanto, a latência aumenta à medida em que cresce o número de valores.

## <a name="examples"></a>Exemplos

Localize todos os hotéis com o nome igual a 'Motel exibição Sea' ou 'Hotel orçamento'. Frases contenham espaços, que é um delimitador padrão. Você pode especificar um delimitador alternativo entre aspas simples como o terceiro parâmetro de cadeia de caracteres:  

    search.in(HotelName, 'Sea View motel,Budget hotel', ',')

Localize todos os hotéis com o nome igual a 'Motel exibição Sea' ou 'Hotel do orçamento' separados por ' |'):

    search.in(HotelName, 'Sea View motel|Budget hotel', '|')

Localize todos os hotéis com os ambientes que têm a marca 'Wi-Fi' ou 'tub':

    Rooms/any(room: room/Tags/any(tag: search.in(tag, 'wifi, tub')))

Localize uma correspondência em frases em uma coleção, como 'toalha aquecido racks' ou 'hairdryer incluído' de marcas.

    Rooms/any(room: room/Tags/any(tag: search.in(tag, 'heated towel racks,hairdryer included', ','))

Localize todos os hotéis sem o motel' tag' ou 'Cabine':

    Tags/all(tag: not search.in(tag, 'motel, cabin'))

## <a name="next-steps"></a>Próximas etapas  

- [Filtros no Azure Search](search-filters.md)
- [Visão geral de linguagem de expressão do OData para o Azure Search](query-odata-filter-orderby-syntax.md)
- [Referência de sintaxe de expressão OData para o Azure Search](search-query-odata-syntax-reference.md)
- [Pesquisar documentos &#40;API REST do Serviço do Azure Search&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
