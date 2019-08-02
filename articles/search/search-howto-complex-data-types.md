---
title: Como modelar tipos de dados complexos – Azure Search
description: Estruturas de dados hierárquicas ou aninhadas podem ser modeladas em um índice de Azure Search usando o tipo complexo e coleções de tipos de dados.
author: brjohnstmsft
manager: jlembicz
ms.author: brjohnst
tags: complex data types; compound data types; aggregate data types
services: search
ms.service: search
ms.topic: conceptual
ms.date: 06/13/2019
ms.custom: seodec2018
ms.openlocfilehash: e7e6ddefd13d669c949389bc4fad85fb6cff4d3a
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67621372"
---
# <a name="how-to-model-complex-data-types-in-azure-search"></a>Como modelar os tipos de dados complexos no Azure Search

Conjuntos de dados externos usados para popular um índice de Azure Search podem vir em muitas formas. Às vezes, incluem subestruturas hierárquicas ou aninhadas. Exemplos podem incluir vários endereços para um único cliente, vários tamanhos e cores para um único SKU, vários autores de um único livro e assim por diante. Em termos de modelagem, você poderá ver essas estruturas referidas como *complexos*, *composta*, *composto*, ou *agregação* tipos de dados. O termo de pesquisa do Azure usa para esse conceito é **tipo complexo**. No Azure Search, tipos complexos são modelados usando **campos complexos**. Um campo complexo é um campo que contém filhos (subcampos) que podem ser de qualquer tipo de dados, incluindo outros tipos complexos. Isso funciona da mesma forma como os tipos de dados estruturados em uma linguagem de programação.

Campos complexos representam um único objeto no documento ou uma matriz de objetos, dependendo do tipo de dados. Campos do tipo `Edm.ComplexType` representam objetos únicos, enquanto os campos do tipo `Collection(Edm.ComplexType)` representam matrizes de objetos.

O Azure Search dá suporte nativamente a coleções e tipos complexos. Esses tipos permitem que você modele praticamente qualquer estrutura JSON em um índice de Azure Search. Nas versões anteriores das APIs de pesquisa do Azure, apenas simplificada linha conjuntos não pôde ser importados. Na versão mais recente, o índice agora pode corresponder mais de perto a fonte de dados. Em outras palavras, se sua fonte de dados possui tipos complexos, o índice pode ter tipos complexos também.

Para começar, é recomendável o [conjunto de dados de hotéis](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/README.md), que podem ser carregados na **importar dados** assistente no portal do Azure. O assistente detecta tipos complexos na fonte e sugere um esquema de índice com base nas estruturas de detectado.

> [!Note]
> Suporte para tipos complexos está disponível no `api-version=2019-05-06`. 
>
> Se sua solução de pesquisa é criada em soluções anteriores de conjuntos de dados bidimensionais em uma coleção, você deve alterar o índice para incluir tipos complexos com suporte na versão mais recente da API. Para obter mais informações sobre como atualizar as versões de API, consulte [atualizar para a versão mais recente da API REST](search-api-migration.md) ou [atualizar para a versão mais recente do SDK do .NET](search-dotnet-sdk-migration-version-9.md).

## <a name="example-of-a-complex-structure"></a>Exemplo de uma estrutura complexa

O seguinte documento JSON é composto de campos simples e complexos. Campos de complexo, como `Address` e `Rooms`, ter subcampos. `Address` tem um único conjunto de valores para esses campos subpropriedades, já que ele é um único objeto no documento. Em contraste, `Rooms` tem vários conjuntos de valores para seus sub-campos, um para cada objeto na coleção.

```json
{
  "HotelId": "1",
  "HotelName": "Secret Point Motel",
  "Description": "Ideally located on the main commercial artery of the city in the heart of New York.",
  "Address": {
    "StreetAddress": "677 5th Ave",
    "City": "New York",
    "StateProvince": "NY"
  },
  "Rooms": [
    {
      "Description": "Budget Room, 1 Queen Bed (Cityside)",
      "Type": "Budget Room",
      "BaseRate": 96.99
    },
    {
      "Description": "Deluxe Room, 2 Double Beds (City View)",
      "Type": "Deluxe Room",
      "BaseRate": 150.99
    },
  ]
}
```

## <a name="creating-complex-fields"></a>Criar campos complexos

Como com qualquer definição de índice, você pode usar o portal [API REST](https://docs.microsoft.com/rest/api/searchservice/create-index), ou [SDK do .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) para criar um esquema que inclui tipos complexos. 

O exemplo a seguir mostra um esquema de índice JSON com campos simples, coleções e tipos complexos. Observe que dentro de um tipo complexo, cada campo subpropriedades tem um tipo e pode ter atributos, campos como nível superior. O esquema corresponde aos dados de exemplo acima. `Address` é um campo complexo que não é uma coleção (um hotel tem um endereço). `Rooms` é um campo de coleção complexa (um hotel tem muitos salas).

<!---
For indexes used in a [push-model data import](search-what-is-data-import.md) strategy, where you are pushing a JSON data set to an Azure Search index, you can only have the basic syntax shown here: single complex types like `Address`, or a `Collection(Edm.ComplexType)` like `Rooms`. You cannot have complex types nested inside other complex types in an index used for push-model data ingestion.

Indexers are a different story. When defining an indexer, in particular one used to build a knowledge store, your index can have nested complex types. An indexer is able to hold a chain of complex data structures in-memory, and when it includes a skillset, it can support highly complex data forms. For more information and an example, see [How to get started with knowledge store](knowledge-store-howto.md).
-->

```json
{
  "name": "hotels",
  "fields": [
    { "name": "HotelId", "type": "Edm.String", "key": true, "filterable": true },
    { "name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false },
    { "name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene" },
    { "name": "Address", "type": "Edm.ComplexType",
      "fields": [
        { "name": "StreetAddress", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "searchable": true },
        { "name": "City", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
        { "name": "StateProvince", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true }
      ]
    },
    { "name": "Rooms", "type": "Collection(Edm.ComplexType)",
      "fields": [
        { "name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene" },
        { "name": "Type", "type": "Edm.String", "searchable": true },
        { "name": "BaseRate", "type": "Edm.Double", "filterable": true, "facetable": true }
      ]
    }
  ]
}
```

## <a name="updating-complex-fields"></a>Atualizando campos complexos

Todos os [reindexação de regras](search-howto-reindex.md) que se aplicam a campos em geral ainda se aplicam aos campos complexos. Ajuste algumas das principais regras aqui, adicionando um campo não exige uma recompilação de índice, mas a maioria das modificações fazer.

### <a name="structural-updates-to-the-definition"></a>Atualizações estruturais para a definição

Você pode adicionar novos campos de inferiores a um campo complexo a qualquer momento sem a necessidade de uma recompilação de índice. Por exemplo, adicionando "ZipCode" para `Address` ou "Comodidades" para `Rooms` é permitido, assim como adicionar um campo de nível superior a um índice. Os documentos existentes têm um valor nulo para novos campos até que você explicitamente popular esses campos ao atualizar os dados.

Observe que dentro de um tipo complexo, cada campo subpropriedades tem um tipo e pode ter atributos, campos como nível superior

### <a name="data-updates"></a>Atualizações de dados

Atualização de documentos existentes em um índice com o `upload` ação funciona da mesma maneira para os campos complexos e simples – todos os campos são substituídos. No entanto, `merge` (ou `mergeOrUpload` quando aplicado a um documento existente) não funcionam da mesma em todos os campos. Especificamente, `merge` não dá suporte a mesclagem de elementos dentro de uma coleção. Essa limitação existe para coleções de tipos primitivos e coleções complexas. Para atualizar uma coleção, você precisa para recuperar o valor da coleção completa, fazer alterações e, em seguida, inclua a nova coleção na solicitação de API do índice.

## <a name="searching-complex-fields"></a>Pesquisar campos complexos

Expressões de pesquisa de forma livre funcionem conforme o esperado com tipos complexos. Se corresponder a qualquer campo pesquisável ou campo subpropriedades em qualquer lugar em um documento, o documento em si é uma correspondência.

Get consultas mais variada quando você tem vários termos e operadores, e alguns termos que têm nomes de campo especificados, assim como é possível com o [sintaxe Lucene](query-lucene-syntax.md). Por exemplo, esta consulta tenta corresponder a dois termos, "Portland" e "OR", em relação a dois campos subpropriedades do campo de endereço:

    search=Address/City:Portland AND Address/State:OR

Consultas como são *não correlacionadas* para pesquisa de texto completo, ao contrário de filtros. Em filtros, consultas em relação aos campos de uma coleção complexa subpropriedades são correlacionadas usando variáveis de alcance na [ `any` ou `all` ](search-query-odata-collection-operators.md). A consulta do Lucene acima retorna documentos que contenham "Portland, Maine" e "Portland, Oregon", juntamente com outras cidades em Oregon. Isso acontece porque cada cláusula se aplica a todos os valores do seu campo em todo o documento, portanto, não há nenhum conceito de um "sub documento atual". Para obter mais informações sobre isso, consulte [filtros de coleção de Noções básicas sobre OData no Azure Search](search-query-understand-collection-filters.md).

## <a name="selecting-complex-fields"></a>Selecionar campos complexos

O `$select` parâmetro é usado para escolher quais campos são retornados nos resultados da pesquisa. Para usar esse parâmetro para selecionar campos específicos de subpropriedades de um campo complexo, incluir o campo pai e o campo subpropriedades separados por uma barra (`/`).

    $select=HotelName, Address/City, Rooms/BaseRate

Campos devem ser marcados como recuperável no índice, se você quiser que eles nos resultados da pesquisa. Somente os campos marcados como recuperável podem ser usados em um `$select` instrução.

## <a name="filter-facet-and-sort-complex-fields"></a>Filtrar, faceta e campos de classificação de complexas

O mesmo [sintaxe de caminho OData](query-odata-filter-orderby-syntax.md) usado para filtragem e respondidas pesquisas também podem ser usadas para a faceta, classificação e seleção de campos em uma solicitação de pesquisa. Para tipos complexos, as regras se aplicam que controlam quais campos subpropriedades podem ser marcados como classificáveis ou com faceta. Para obter mais informações sobre essas regras, consulte a [referência de API de criação de índice](https://docs.microsoft.com/rest/api/searchservice/create-index#request).

### <a name="faceting-sub-fields"></a>Facetamento subcampos

Qualquer campo subpropriedades pode ser marcado como facetável, a menos que ele é do tipo `Edm.GeographyPoint` ou `Collection(Edm.GeographyPoint)`.

As contagens de documentos retornadas nos resultados da faceta são calculadas para o documento pai (um hotel), não os sub-documentos em uma coleção complexa (salas). Por exemplo, suponha que um hotel tem 20 salas do tipo "suite". Considerando esse parâmetro de faceta `facet=Rooms/Type`, a contagem de faceta será um para o hotel, não 20 para as salas.

### <a name="sorting-complex-fields"></a>Campos complexos de classificação

Operações de classificação se aplicam a documentos (hotéis) e não documentos sub (salas). Quando você tiver uma coleção de tipo complexo, como as salas, é importante perceber que você não pode classificar salas em todos os. Na verdade, você não pode classificar qualquer coleção.

Operações de classificação funcionam quando campos têm um único valor por documento, se o campo é um campo simples ou um campo de subpropriedades em um tipo complexo. Por exemplo, `Address/City` tem permissão para ser classificáveis porque há apenas um endereço por hotel, portanto, `$orderby=Address/City` classificará hotéis por cidade.

### <a name="filtering-on-complex-fields"></a>Filtrando campos complexos

Você pode se referir aos campos de subpropriedades de um campo complexo em uma expressão de filtro. Basta usar o mesmo [sintaxe de caminho OData](query-odata-filter-orderby-syntax.md) que é usado para a faceta, classificação e seleção de campos. Por exemplo, o filtro a seguir retornará todos os hotéis no Canadá:

    $filter=Address/Country eq 'Canada'

Para filtrar em um campo de coleção complexa, você pode usar um **expressão lambda** com o [ `any` e `all` operadores](search-query-odata-collection-operators.md). Nesse caso, o **variável de intervalo** do lambda a expressão é um objeto com subcampos. Você pode se referir a esses campos subpastas com a sintaxe de caminho OData padrão. Por exemplo, o filtro a seguir retornará todos os hotéis com pelo menos uma sala deluxe e todos os não-fumar salas de:

    $filter=Rooms/any(room: room/Type eq 'Deluxe Room') and Rooms/all(room: not room/SmokingAllowed)

Como com campos simples de nível superior, subcampos simples de campos complexos só podem ser incluídos nos filtros se eles tiverem as **filtrável** atributo definido como `true` na definição do índice. Para obter mais informações, consulte o [referência de API de criação de índice](https://docs.microsoft.com/rest/api/searchservice/create-index#request).

## <a name="next-steps"></a>Próximas etapas

Experimente o [conjunto de dados de hotéis](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/README.md) na **importar dados** assistente. Você precisará as informações de conexão do Cosmos DB fornecidas no arquivo Leiame para acessar os dados.

Com essas informações em mãos, a primeira etapa no assistente é criar uma nova fonte de dados do Azure Cosmos DB. Ainda mais no assistente, quando você chegar à página de índice de destino, você verá um índice com tipos complexos. Criar e carregar esse índice e, em seguida, executar consultas para entender a nova estrutura.

> [!div class="nextstepaction"]
> [Guia de início rápido: Assistente do portal para importação, indexação e consultas](search-get-started-portal.md)