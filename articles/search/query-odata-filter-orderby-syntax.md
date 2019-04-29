---
title: Sintaxe de expressão do OData para cláusulas filter e order-by – Azure Search
description: Sintaxe de expressão do OData para cláusulas filter e order-by para consultas do Azure Search.
ms.date: 03/27/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
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
ms.openlocfilehash: ab98c3be75fb59603be66ee84e0d288de56cdc91
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61317093"
---
# <a name="odata-expression-syntax-for-filters-and-order-by-clauses-in-azure-search"></a>Sintaxe de expressão do OData para cláusulas filter e order-by no Azure Search

O Azure Search é compatível com um subconjunto da sintaxe de expressão do OData para expressões **$filter** e **$orderby**. As expressões Filter são avaliadas durante a análise da consulta, restringindo a pesquisa a campos específicos ou adicionando critérios de correspondência usados durante as verificações de índice. As expressões Order-by são aplicadas como uma etapa de pós-processamento sobre um conjunto de resultados. As expressões filter e order-by estão inclusas em uma solicitação de consulta, aderindo a uma sintaxe de OData independente da sintaxe de consulta [simple](query-simple-syntax.md) ou [full](query-lucene-syntax.md) usada em um parâmetro de **pesquisa**. Este artigo fornece a documentação de referência para as expressões OData usadas em expressões de filtragem e de classificação.

## <a name="filter-syntax"></a>Sintaxe de filtragem

Uma expressão **$filter** pode ser executada de forma autônoma como uma consulta totalmente expressa ou refinar uma consulta que tenha parâmetros adicionais. Os exemplos a seguir ilustram alguns dos principais cenários. No primeiro exemplo, o filtro é a substância da consulta.


```POST
POST /indexes/hotels/docs/search?api-version=2017-11-11
    {
      "filter": "(baseRate ge 60 and baseRate lt 300) or hotelName eq 'Fancy Stay'"
    }
```

Outro caso de uso comum é o facetamento combinado de filtros, em que o filtro reduz a área de superfície da consulta com base em uma seleção de navegação de faceta orientada pelo usuário:

```POST
POST /indexes/hotels/docs/search?api-version=2017-11-11
    {
      "search": "test",
      "facets": [ "tags", "baseRate,values:80|150|220" ],
      "filter": "rating eq 3 and category eq 'Motel'"
    }
```

### <a name="filter-operators"></a>Operadores de filtro  

- Operadores lógicos (and, or, not).  

- Expressões de comparação (`eq, ne, gt, lt, ge, le`). Comparações de cadeia de caracteres diferenciam maiúsculas de minúsculas.  

- Constantes dos tipos de [Modelo de Dados de Entidade](https://docs.microsoft.com/dotnet/framework/data/adonet/entity-data-model) (EDM) compatíveis (consulte [Tipos de dados compatíveis &#40;Azure Search&#41;](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) para obter uma lista dos tipos compatíveis). As constantes de tipos de coleção não são compatíveis.  

- Referências a nomes de campos. Só é possível usar campos `filterable` em expressões de filtro.  

- `any` sem parâmetros. Testa se um campo do tipo `Collection(Edm.String)` contém algum elemento.  

- `any` e `all` com suporte limitado de expressão lambda. 
    
  -   `any/all` é compatível em campos do tipo `Collection(Edm.String)`. 
    
  -   `any` só pode ser usado com expressões de igualdade simples ou com uma função `search.in`. Expressões simples consistem em uma comparação entre um único campo e um valor literal, por exemplo, `Title eq 'Magna Carta'`.
    
  -   `all` só pode ser usado com expressões de desigualdade simples ou com `not search.in`.   

- Funções geoespaciais `geo.distance` e `geo.intersects`. A função `geo.distance` retorna a distância em quilômetros entre dois pontos, um deles sendo um campo e o outro uma constante passada como parte do filtro. A função `geo.intersects` retorna “true” se um determinado ponto está dentro de um polígono determinado, em que o ponto é um campo e o polígono é especificado como uma constante passada como parte do filtro.  

  O polígono é uma superfície bidimensional armazenada como uma sequência de pontos que definem um anel delimitador (consulte o exemplo a seguir). O polígono precisa estar fechado, o que significa que os conjuntos de primeiro e último pontos devem ser os mesmos. [Os pontos em um polígono devem estar no sentido anti-horário](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1).

  `geo.distance` retorna a distância em quilômetros no Azure Search. Isso é diferente de outros serviços compatíveis com as operações geoespaciais do OData, que normalmente retornam as distâncias em metros.  

  > [!NOTE]  
  >  Ao usar geo.distance em um filtro, você deve comparar a distância retornada pela função com uma constante usando `lt`, `le`, `gt` ou `ge`. Os operadores `eq` e `ne` não são compatíveis ao comparar as distâncias. Por exemplo, este é um uso correto de geo.distance: `$filter=geo.distance(location, geography'POINT(-122.131577 47.678581)') le 5`.  

- A função `search.in` testa se um determinado campo de cadeia de caracteres é igual a um de uma determinada lista de valores. Ela também pode ser usada em “any” ou “all” para comparar um valor único de um campo de coleção de cadeia de caracteres com uma determinada lista de valores. A igualdade entre o campo e cada valor na lista é determinada de maneira a diferenciar maiúsculas de minúsculas, da mesma forma que para o operador `eq`. Portanto, uma expressão como `search.in(myfield, 'a, b, c')` é equivalente a `myfield eq 'a' or myfield eq 'b' or myfield eq 'c'`, exceto que `search.in` produzirá um desempenho muito melhor. 

   O primeiro parâmetro para a função `search.in` é a referência de campo de cadeia de caracteres (ou uma variável de intervalo em um campo de coleção de cadeia de caracteres no caso em que `search.in` é usado dentro de uma expressão `any` ou `all`). 
  
   O segundo parâmetro é uma cadeia de caracteres que contém a lista de valores, separados por espaços e/ou vírgulas. 
  
   O terceiro parâmetro é uma cadeia de caracteres em que cada caractere da cadeia de caracteres ou subconjunto dessa cadeia de caracteres é tratado como um separador ao analisar a lista de valores no segundo parâmetro. Se você precisar usar separadores diferentes de espaços e vírgulas, pois os seus valores incluem esses caracteres, é possível especificar um terceiro parâmetro opcional para `search.in`. 

  > [!NOTE]   
  > Alguns cenários exigem a comparação de um campo com um grande número de valores constantes. Por exemplo, implementar as restrições de segurança com filtros pode exigir a comparação do campo de ID do documento em relação a uma lista de IDs da qual o usuário solicitante recebe acesso de leitura. Em cenários como esse é altamente recomendável usar a função `search.in` ao invés de uma disjunção mais complicada de expressões de igualdade. Por exemplo, use `search.in(Id, '123, 456, ...')` ao invés de `Id eq 123 or Id eq 456 or ....`. 
  >
  > Se você usar `search.in`, espere um tempo de resposta abaixo de um segundo quando o segundo parâmetro contiver uma lista de centenas ou milhares de valores. Observe que não há nenhum limite explícito para o número de itens que você pode passar para `search.in`, embora você ainda esteja limitado pelo tamanho máximo da solicitação. No entanto, a latência aumenta à medida em que cresce o número de valores.

- A função `search.ismatch` avalia a consulta de pesquisa como parte de uma expressão de filtro. Os documentos que correspondem à consulta da pesquisa serão retornados no conjunto de resultados. As seguintes sobrecargas desta função estão disponíveis:
  - `search.ismatch(search)`
  - `search.ismatch(search, searchFields)`
  - `search.ismatch(search, searchFields, queryType, searchMode)`

  onde: 
  
  - `search`: a consulta de pesquisa (na sintaxe de consulta [simple](query-simple-syntax.md) ou [full](query-lucene-syntax.md)). 
  - `queryType`: "simple" ou "full", assume "simple" como padrão. Especifica qual idioma de consulta foi usado no parâmetro `search`.
  - `searchFields`: lista separada por vírgulas de campos pesquisáveis, assume como padrão todos os campos pesquisáveis no índice.    
  - `searchMode`: "any" ou "all", o padrão é "any". Indica se algum ou todos os termos de pesquisa devem ser correspondidos para contar o documento como uma correspondência.

  Todos os parâmetros acima são equivalentes aos respectivos [parâmetros de solicitação de pesquisa](https://docs.microsoft.com/rest/api/searchservice/search-documents).

- A função `search.ismatchscoring`, como a função `search.ismatch`, retorna “true” para documentos que correspondem à consulta de pesquisa passada como um parâmetro. A diferença entre eles é que a pontuação de relevância dos documentos que correspondem à consulta `search.ismatchscoring` também contribui para a pontuação geral do documento, enquanto que no caso de `search.ismatch`, a pontuação de documento não se altera. As seguintes sobrecargas dessa função estão disponíveis com parâmetros idênticos aos de `search.ismatch`:
  - `search.ismatchscoring(search)`
  - `search.ismatchscoring(search, searchFields)`
  - `search.ismatchscoring(search, searchFields, queryType, searchMode)`

  As funções `search.ismatch` e `search.ismatchscoring` são totalmente ortogonais entre si e com o restante da álgebra do filtro. Isso significa que ambas as funções podem ser usadas na mesma expressão de filtro. 

### <a name="geospatial-queries-and-polygons-spanning-the-180th-meridian"></a>Consultas geoespaciais e polígonos abrangendo o 180º meridiano  
 Para muitas bibliotecas de consulta geoespaciais, a formulação de uma consulta que inclua o 180º meridiano (próximo à hora do meridiano) está fora dos limites ou requer uma solução alternativa, como dividir o polígono em dois, um em cada lado do meridiano.  

 No Azure Search, as consultas geoespaciais que incluem longitude de 180 graus funcionam conforme o esperado se a forma de consulta for retangular e suas coordenadas se alinham a um layout de grade na longitude e na latitude (por exemplo, `geo.intersects(location, geography'POLYGON((179 65,179 66,-179 66,-179 65,179 65))'`). Caso contrário, para formas não retangulares ou desalinhadas, considere a abordagem de polígono dividido.  

<a name="bkmk_limits"></a>

## <a name="filter-size-limitations"></a>Limitações de tamanho de filtro 

 Há limites para o tamanho e a complexidade de expressões de filtro que você pode enviar para o Azure Search. Os limites se baseiam aproximadamente no número de cláusulas na sua expressão de filtro. Uma boa regra prática é que, se você tiver centenas de cláusulas, você correrá o risco de atingir o limite. É recomendável criar seu aplicativo de forma que ele não gere filtros de tamanho ilimitado.  


## <a name="filter-examples"></a>Exemplos de filtro  

 Localize todos os hotéis com uma taxa base menor que US $200 que são classificados ou superior a 4:  

```
$filter=baseRate lt 200.0 and rating ge 4
```

 Localizar todos os hotéis que foram renovados desde 2010 e que não sejam o "Roach Motel":  

```
$filter=hotelName ne 'Roach Motel' and lastRenovationDate ge 2010-01-01T00:00:00Z
```

 Localize todos os hotéis com uma taxa base menor que US $200 que foram renovados desde 2010, com uma data e hora literal que inclui informações de fuso horário para hora oficial do Pacífico:  

```
$filter=baseRate lt 200 and lastRenovationDate ge 2010-01-01T00:00:00-08:00
```

 Localizar todos os hotéis que têm estacionamento incluído e não permitem fumar:  

```
$filter=parkingIncluded and not smokingAllowed
```

 \- OU -  

```
$filter=parkingIncluded eq true and smokingAllowed eq false
```

 Localizar todos os hotéis marcados como Luxo ou que incluem estacionamento e têm uma classificação de 5:  

```
$filter=(category eq 'Luxury' or parkingIncluded eq true) and rating eq 5
```

 Localizar todos os hotéis com a marca "wifi" (em que cada hotel tem marcas armazenadas em um campo Collection(Edm.String)):  

```
$filter=tags/any(t: t eq 'wifi')
```

 Localizar todos os hotéis sem a marca "motel":  

```
$filter=tags/all(t: t ne 'motel')
```

 Localizar todos os hotéis com quaisquer marcas:  

```
$filter=tags/any()
```

Localize todos os hotéis que não têm marcas:  

```
$filter=not tags/any()
```


 Localizar todos os hotéis em um raio de 10 km de determinado ponto de referência (em que o local é um campo do tipo Edm.GeographyPoint):  

```
$filter=geo.distance(location, geography'POINT(-122.131577 47.678581)') le 10
```

 Localizar todos os hotéis dentro de determinado visor descrito como um polígono (em que o local é um campo do tipo Edm.GeographyPoint). Observe que o polígono está fechado (o primeiro e o último pontos devem ser o mesmo) e [os pontos devem estar listados no sentido anti-horário](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1).

```
$filter=geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')
```

 Localizar todos os hotéis que não têm nenhum valor no campo "Descrição" ou cujo valor está definido explicitamente como nulo:  

```
$filter=description eq null
```

Localize todos os hotéis com o nome igual a 'Roach motel' ou 'Hotel orçamento'). Frases contenham espaços, que é um delimitador padrão. Você pode specicfy um delimitador alternativo entre aspas simples como o terceiro parâmetro de cadeia de caracteres:  

```
$filter=search.in(name, 'Roach motel,Budget hotel', ',')
```

Localizar todos os hotéis com o nome igual a “Roach Motel” ou “Budget Hotel” separado por '|'):  

```
$filter=search.in(name, 'Roach motel|Budget hotel', '|')
```

Localizar todos os hotéis com a marca “wifi” ou “piscina”:  

```
$filter=tags/any(t: search.in(t, 'wifi, pool'))
```

Localize uma correspondência em frases em uma coleção, como 'toalha aquecido racks' ou 'hairdryer incluído' de marcas. 

```
$filter=tags/any(t: search.in(t, 'heated towel racks,hairdryer included', ','))
```

Localizar todos os hotéis sem as marcas “motel” ou “cabana”:  

```
$filter=tags/all(t: not search.in(t, 'motel, cabin'))
```

Localizar documentos com as palavras "orla marítima". Essa consulta de filtro é idêntica a uma [solicitação de pesquisa](https://docs.microsoft.com/rest/api/searchservice/search-documents) com `search=waterfront`.

```
$filter=search.ismatchscoring('waterfront')
```

Localizar documentos com a palavra "hostel" e classificação maior ou igual a 4, ou documentos com a palavra "motel" e classificação igual a 5. Observe que essa solicitação não pode ser expressa sem a função `search.ismatchscoring`.

```
$filter=search.ismatchscoring('hostel') and rating ge 4 or search.ismatchscoring('motel') and rating eq 5
```

Localizar documentos sem a palavra "luxo".

```
$filter=not search.ismatch('luxury') 
```

Localizar documentos com a frase "vista para o mar" ou classificação igual a 5. A consulta `search.ismatchscoring` será executada apenas em relação aos campos `hotelName` e `description`.
Observe que os documentos que corresponderam apenas à segunda cláusula da disjunção também serão retornados, ou seja, hotéis com classificação igual a 5. Para deixar claro, esses documentos não corresponderam a nenhuma parte classificada da expressão e serão retornados com pontuação igual a zero.

```
$filter=search.ismatchscoring('"ocean view"', 'description,hotelName') or rating eq 5
```

Localizar documentos em que os termos "hotel" e "aeroporto" estão a 5 palavras de distância um do outro na descrição do hotel e onde fumar não é permitido. Essa consulta usa a [linguagem de consulta Lucene completa](query-lucene-syntax.md).

```
$filter=search.ismatch('"hotel airport"~5', 'description', 'full', 'any') and not smokingAllowed 
```

## <a name="order-by-syntax"></a>Sintaxe de Order-by

O parâmetro **$orderby** aceita uma lista separada por vírgulas de até 32 expressões do formulário `sort-criteria [asc|desc]`. Os critérios de classificação podem ser o nome de um campo `sortable` ou uma chamada para as funções `geo.distance` ou `search.score`. Você pode usar `asc` ou `desc` para especificar explicitamente a ordem de classificação. A ordem padrão é crescente.

Se vários documentos têm os mesmos critérios de classificação e a função `search.score` não é usada (por exemplo, se você classificar por um campo `rating` numérico e três documentos têm uma classificação de 4), os empates são resolvidos pela pontuação do documento em ordem decrescente. Quando as pontuações do documento são as mesmas (por exemplo, quando não há consulta de pesquisa de texto completo especificada na solicitação), então a ordenação relativa dos documentos empatados é indeterminada.
 
Você pode especificar vários critérios de classificação. A ordem das expressões determina a ordem de classificação final. Por exemplo, para classificar em ordem decrescente pela pontuação, seguida pela classificação, a sintaxe é `$orderby=search.score() desc,rating desc`.

A sintaxe para `geo.distance` em **$orderby** é a mesma que em **$filter**. Ao usar `geo.distance` em **$orderby**, o campo ao qual a operação se aplica deve ser do tipo `Edm.GeographyPoint` e também deve ser `sortable`.  

A sintaxe para `search.score` em **$orderby** é `search.score()`. A função `search.score` não aceita nenhum parâmetro.  
 

## <a name="order-by-examples"></a>Exemplos de Order-by

Organizar os hotéis em ordem crescente por taxa base:

```
$orderby=baseRate asc
```

Organizar os hotéis em ordem decrescente por classificação e, em seguida, em ordem crescente por taxa base (lembre-se de que a ordem crescente é o padrão):

```
$orderby=rating desc,baseRate
```

Organizar em ordem decrescente por classificação e, em seguida, em ordem crescente por distância das coordenadas fornecidas:

```
$orderby=rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc
```

Organizar em ordem decrescente por search.score e por classificação e, em seguida, em ordem crescente por distância das coordenadas fornecidas, de modo que entre dois hotéis com classificações idênticas, o mais próximo seja listado primeiro:

```
$orderby=search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc
```
<a name="bkmk_unsupported"></a>

## <a name="unsupported-odata-syntax"></a>Sintaxe de OData não suportada

-   Expressões aritméticas  

-   Funções (exceto as funções geoespaciais de interseção e a distância)  

-   `any/all` com expressões lambda arbitrárias  

## <a name="see-also"></a>Consulte também  

+ [Navegação facetada no Azure Search](search-faceted-navigation.md) 
+ [Filtros no Azure Search](search-filters.md) 
+ [Pesquisar documentos &#40;API REST do Serviço do Azure Search&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) 
+ [Sintaxe de consulta Lucene](query-lucene-syntax.md)
+ [Sintaxe de consulta simples no Azure Search](query-simple-syntax.md)   
