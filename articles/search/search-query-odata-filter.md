---
title: Referência de filtro OData
titleSuffix: Azure Cognitive Search
description: Referência de linguagem OData e sintaxe completa usada para criar expressões de filtro em consultas de Pesquisa Cognitiva do Azure.
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
ms.openlocfilehash: 0f33b5a28d7c83be7e546c3f61bc517047c51312
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "88934847"
---
# <a name="odata-filter-syntax-in-azure-cognitive-search"></a>Sintaxe de $filter OData no Azure Pesquisa Cognitiva

O Azure Pesquisa Cognitiva usa [expressões de filtro OData](query-odata-filter-orderby-syntax.md) para aplicar critérios adicionais a uma consulta de pesquisa além dos termos de pesquisa de texto completo. Este artigo descreve a sintaxe de filtros em detalhes. Para obter mais informações gerais sobre quais filtros são e como usá-los para perceber cenários de consulta específicos, consulte [filtros no Azure pesquisa cognitiva](search-filters.md).

## <a name="syntax"></a>Sintaxe

Um filtro na linguagem OData é uma expressão booleana que, por sua vez, pode ser um dos vários tipos de expressão, conforme mostrado pelo seguinte EBNF ([formulário estendido Backus-Naur](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)):

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
boolean_expression ::=
    collection_filter_expression
    | logical_expression
    | comparison_expression
    | boolean_literal
    | boolean_function_call
    | '(' boolean_expression ')'
    | variable

/* This can be a range variable in the case of a lambda, or a field path. */
variable ::= identifier | field_path
```

Um diagrama de sintaxe interativa também está disponível:

> [!div class="nextstepaction"]
> [Diagrama de sintaxe do OData para Pesquisa Cognitiva do Azure](https://azuresearch.github.io/odata-syntax-diagram/#boolean_expression)

> [!NOTE]
> Consulte [referência de sintaxe de expressão OData para pesquisa cognitiva do Azure](search-query-odata-syntax-reference.md) para o EBNF completo.

Os tipos de expressões booleanas incluem:

- Expressões de filtro de coleção usando `any` ou `all` . Eles aplicam critérios de filtro aos campos de coleção. Para obter mais informações, consulte [operadores de coleção OData no Azure pesquisa cognitiva](search-query-odata-collection-operators.md).
- Expressões lógicas que combinam outras expressões booleanas usando os operadores `and` , `or` e `not` . Para obter mais informações, consulte [operadores lógicos do OData no Azure pesquisa cognitiva](search-query-odata-logical-operators.md).
- Expressões de comparação, que comparam campos ou variáveis de intervalo a valores constantes usando operadores,,,, `eq` `ne` `gt` `lt` `ge` e `le` . Para obter mais informações, consulte [operadores de comparação OData no Azure pesquisa cognitiva](search-query-odata-comparison-operators.md). As expressões de comparação também são usadas para comparar distâncias entre coordenadas geoespaciais usando a `geo.distance` função. Para obter mais informações, consulte [funções geoespaciais OData no Azure pesquisa cognitiva](search-query-odata-geo-spatial-functions.md).
- Os literais boolianos `true` e `false` . Essas constantes podem ser úteis às vezes ao gerar filtros programaticamente, mas, caso contrário, não tendem a ser usadas na prática.
- Chamadas para funções booleanas, incluindo:
  - `geo.intersects`, que testa se um determinado ponto está dentro de um determinado polígono. Para obter mais informações, consulte [funções geoespaciais OData no Azure pesquisa cognitiva](search-query-odata-geo-spatial-functions.md).
  - `search.in`, que compara um campo ou variável de intervalo com cada valor em uma lista de valores. Para obter mais informações, [consulte `search.in` função OData no Azure pesquisa cognitiva](search-query-odata-search-in-function.md).
  - `search.ismatch` e `search.ismatchscoring` , que executam operações de pesquisa de texto completo em um contexto de filtro. Para obter mais informações, consulte [funções de pesquisa de texto completo OData no Azure pesquisa cognitiva](search-query-odata-full-text-search-functions.md).
- Caminhos de campo ou variáveis de intervalo do tipo `Edm.Boolean` . Por exemplo, se o índice tiver um campo booliano chamado `IsEnabled` e você desejar retornar todos os documentos em que esse campo é `true` , a expressão de filtro poderá ser apenas o nome `IsEnabled` .
- Expressões booleanas entre parênteses. O uso de parênteses pode ajudar a determinar explicitamente a ordem das operações em um filtro. Para obter mais informações sobre a precedência padrão dos operadores OData, consulte a próxima seção.

### <a name="operator-precedence-in-filters"></a>Precedência de operador em filtros

Se você escrever uma expressão de filtro sem parênteses em suas subexpressãos, o Azure Pesquisa Cognitiva irá avaliá-lo de acordo com um conjunto de regras de precedência de operador. Essas regras são baseadas em quais operadores são usados para combinar subexpressãos. A tabela a seguir lista grupos de operadores na ordem da precedência mais alta para a mais baixa:

| Grupo | Operador (es) |
| --- | --- |
| Operadores lógicos | `not` |
| Operadores de comparação | `eq`, `ne`, `gt`, `lt`, `ge`, `le` |
| Operadores lógicos | `and` |
| Operadores lógicos | `or` |

Um operador que é superior na tabela acima será "associado mais rigidamente" a seus operandos do que outros operadores. Por exemplo, `and` é de maior precedência do que `or` e os operadores de comparação são de precedência mais alta do que qualquer um deles, portanto, as duas expressões a seguir são equivalentes:

```odata-filter-expr
    Rating gt 0 and Rating lt 3 or Rating gt 7 and Rating lt 10
    ((Rating gt 0) and (Rating lt 3)) or ((Rating gt 7) and (Rating lt 10))
```

O `not` operador tem a maior precedência de todos--ainda mais alto do que os operadores de comparação. É por isso que, se você tentar escrever um filtro como este:

```odata-filter-expr
    not Rating gt 5
```

Você receberá essa mensagem de erro:

```text
    Invalid expression: A unary operator with an incompatible type was detected. Found operand type 'Edm.Int32' for operator kind 'Not'.
```

Esse erro ocorre porque o operador está associado apenas ao `Rating` campo, que é do tipo `Edm.Int32` , e não com a expressão de comparação inteira. A correção é colocar o operando entre `not` parênteses:

```odata-filter-expr
    not (Rating gt 5)
```

<a name="bkmk_limits"></a>

### <a name="filter-size-limitations"></a>Limitações de tamanho de filtro

Há limites para o tamanho e a complexidade das expressões de filtro que você pode enviar para o Azure Pesquisa Cognitiva. Os limites se baseiam aproximadamente no número de cláusulas na sua expressão de filtro. Uma boa diretriz é que, se você tiver centenas de cláusulas, corre o risco de exceder o limite. É recomendável projetar seu aplicativo de forma que ele não gere filtros de tamanho não associado.

> [!TIP]
> Usar [a `search.in` função](search-query-odata-search-in-function.md) em vez de longas disjunçãos de comparações de igualdade pode ajudar a evitar o limite de cláusula de filtro, uma vez que uma chamada de função conta como uma única cláusula.

## <a name="examples"></a>Exemplos

Localize todos os hotéis com pelo menos uma sala com uma taxa de base inferior a $200, classificada em ou acima de 4:

```odata-filter-expr
    $filter=Rooms/any(room: room/BaseRate lt 200.0) and Rating ge 4
```

Encontre todos os hotéis diferentes de "Motel de exibição do mar" que foram renovadosdos desde 2010:

```odata-filter-expr
    $filter=HotelName ne 'Sea View Motel' and LastRenovationDate ge 2010-01-01T00:00:00Z
```

Encontre todos os hotéis que foram renovadosdos em 2010 ou posteriores. O literal DateTime inclui informações de fuso horário para o horário padrão do Pacífico:  

```odata-filter-expr
    $filter=LastRenovationDate ge 2010-01-01T00:00:00-08:00
```

Encontre todos os hotéis que têm o estacionamento incluído e onde todas as salas não são fumantes:

```odata-filter-expr
    $filter=ParkingIncluded and Rooms/all(room: not room/SmokingAllowed)
```

 \- OU -  

```odata-filter-expr
    $filter=ParkingIncluded eq true and Rooms/all(room: room/SmokingAllowed eq false)
```

Localizar todos os hotéis marcados como Luxo ou que incluem estacionamento e têm uma classificação de 5:  

```odata-filter-expr
    $filter=(Category eq 'Luxury' or ParkingIncluded eq true) and Rating eq 5
```

Localize todos os hotéis com a marca "WiFi" em pelo menos uma sala (onde cada sala tem marcas armazenadas em um `Collection(Edm.String)` campo):  

```odata-filter-expr
    $filter=Rooms/any(room: room/Tags/any(tag: tag eq 'wifi'))
```

Encontre todos os hotéis com qualquer sala:  

```odata-filter-expr
    $filter=Rooms/any()
```

Encontre todos os hotéis que não têm salas:

```odata-filter-expr
    $filter=not Rooms/any()
```

Localiza todos os hotéis em 10 quilômetros de um determinado ponto de referência (em que `Location` é um campo do tipo `Edm.GeographyPoint` ):

```odata-filter-expr
    $filter=geo.distance(Location, geography'POINT(-122.131577 47.678581)') le 10
```

Localiza todos os hotéis em um determinado visor descrito como um polígono (em que `Location` é um campo do tipo EDM. GeographyPoint). O polígono deve ser fechado, o que significa que os primeiros e os últimos conjuntos de pontos devem ser iguais. Além disso, [os pontos devem estar listados na ordem no sentido anti-horário](/rest/api/searchservice/supported-data-types#Anchor_1).

```odata-filter-expr
    $filter=geo.intersects(Location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')
```

Localiza todos os hotéis em que o campo "Descrição" é nulo. O campo será nulo se nunca tiver sido definido ou se tiver sido definido explicitamente como nulo:  

```odata-filter-expr
    $filter=Description eq null
```

Localize todos os hotéis com o nome igual a ' Sea View Motel ' ou ' Budget Orca '). Essas frases contêm espaços e o espaço é um delimitador padrão. Você pode especificar um delimitador alternativo entre aspas simples como o terceiro parâmetro de cadeia de caracteres:  

```odata-filter-expr
    $filter=search.in(HotelName, 'Sea View motel,Budget hotel', ',')
```

Localize todos os hotéis com o nome igual ao ' Sea View Motel ' ou ao ' Budget Hotel ', separados por ' | '):  

```odata-filter-expr
    $filter=search.in(HotelName, 'Sea View motel|Budget hotel', '|')
```

Localize todos os hotéis em que todas as salas têm a marca ' WiFi ' ou ' Tub ':

```odata-filter-expr
    $filter=Rooms/any(room: room/Tags/any(tag: search.in(tag, 'wifi, tub'))
```

Encontre uma correspondência em frases em uma coleção, como "racks de toalha aquecidos" ou "hairdryer incluídos" nas marcas.

```odata-filter-expr
    $filter=Rooms/any(room: room/Tags/any(tag: search.in(tag, 'heated towel racks,hairdryer included', ','))
```

Localizar documentos com as palavras "orla marítima". Essa consulta de filtro é idêntica a uma [solicitação de pesquisa](/rest/api/searchservice/search-documents) com `search=waterfront`.

```odata-filter-expr
    $filter=search.ismatchscoring('waterfront')
```

Localizar documentos com a palavra "hostel" e classificação maior ou igual a 4, ou documentos com a palavra "motel" e classificação igual a 5. Essa solicitação não pôde ser expressa sem a `search.ismatchscoring` função, pois ela combina a pesquisa de texto completo com operações de filtro usando `or` .

```odata-filter-expr
    $filter=search.ismatchscoring('hostel') and rating ge 4 or search.ismatchscoring('motel') and rating eq 5
```

Localizar documentos sem a palavra "luxo".

```odata-filter-expr
    $filter=not search.ismatch('luxury')
```

Localizar documentos com a frase "vista para o mar" ou classificação igual a 5. A consulta `search.ismatchscoring` será executada apenas em relação aos campos `HotelName` e `Description`. Os documentos que corresponderem somente à segunda cláusula da disjunção serão retornados também para hotéis com `Rating` igual a 5. Esses documentos serão retornados com pontuação igual a zero para deixá-lo claro que eles não corresponderam a nenhuma das partes pontuadas da expressão.

```odata-filter-expr
    $filter=search.ismatchscoring('"ocean view"', 'Description,HotelName') or Rating eq 5
```

Encontre Hotéis em que os termos "Hotel" e "aeroporto" não têm mais do que cinco palavras de diferença na descrição, e onde todas as salas não são fumantes. Essa consulta usa a [linguagem de consulta Lucene completa](query-lucene-syntax.md).

```odata-filter-expr
    $filter=search.ismatch('"hotel airport"~5', 'Description', 'full', 'any') and not Rooms/any(room: room/SmokingAllowed)
```

## <a name="next-steps"></a>Próximas etapas  

- [Filtros no Azure Pesquisa Cognitiva](search-filters.md)
- [Visão geral da linguagem de expressão OData para Azure Pesquisa Cognitiva](query-odata-filter-orderby-syntax.md)
- [Referência de sintaxe de expressão OData para Pesquisa Cognitiva do Azure](search-query-odata-syntax-reference.md)
- [Pesquisar documentos &#40;API REST do Azure Pesquisa Cognitiva&#41;](/rest/api/searchservice/Search-Documents)