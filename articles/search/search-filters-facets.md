---
title: Filtros de faceta para navegação de pesquisa em aplicativos – Azure Search
description: Critérios de filtro por identidade de segurança do usuário, localização geográfica ou valores numéricos para reduzir os resultados da pesquisa em consultas no Azure Search, um serviço de pesquisa de nuvem hospedado no Microsoft Azure.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 5/13/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 88171487fd180931d4659390f0db3c8619fb2d62
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67653449"
---
# <a name="how-to-build-a-facet-filter-in-azure-search"></a>Como criar um filtro de faceta no Azure Search 

A navegação facetada é usada para filtragem direcionada automaticamente nos resultados da consulta em um aplicativo de pesquisa, em que seu aplicativo oferece controles de interface do usuário para direcionar a pesquisa para grupos de documentos (por exemplo, categorias ou marcas) e o Azure Search fornece a estrutura de dados para criar a experiência. Neste artigo, examine rapidamente as etapas básicas para criar uma estrutura de navegação facetada criando a experiência de pesquisa que você deseja fornecer. 

> [!div class="checklist"]
> * Selecionar os campos para filtrar e facetar
> * Definir atributos no campo
> * Criar o índice e carregar dados
> * Adicionar filtros de faceta a uma consulta
> * Manipular resultados

As facetas são dinâmicas e retornadas em uma consulta. As respostas da pesquisa trazem com elas as categorias de faceta usadas para navegar pelos resultados. Se você não estiver familiarizado com facetas, o exemplo a seguir é uma ilustração de uma estrutura de navegação por faceta.

  ![](./media/search-filters-facets/facet-nav.png)

Novo com navegação por faceta e deseja mais detalhes? Consulte [Como implementar a navegação facetada no Azure Search](search-faceted-navigation.md).

## <a name="choose-fields"></a>Selecionar campos

As facetas podem ser calculadas por campos de valor único e por coleções. Campos que funcionam melhor em navegação facetada têm baixa cardinalidade: um pequeno número de valores distintos que se repetem em documentos em seu corpo de pesquisa (por exemplo, uma lista de nomes de marca, países/regiões ou cores). 

A facetagem está habilitada em uma base campo por campo quando você cria o índice, definindo o `facetable` atributo `true`. Você geralmente deve também definir a `filterable` atributo `true` para tal campos para que seu aplicativo de pesquisa pode filtrar esses campos com base em facetas que o usuário final seleciona. 

Ao criar um índice usando a API REST, qualquer [tipo de campo](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) que poderiam ser usados na navegação facetada são marcados como `facetable` por padrão:

+ `Edm.String`
+ `Edm.DateTimeOffset`
+ `Edm.Boolean`
+ Tipos de campo numérico: `Edm.Int32`, `Edm.Int64`, `Edm.Double`
+ Coleções dos tipos acima (por exemplo, `Collection(Edm.String)` ou `Collection(Edm.Double)`)

Não é possível usar `Edm.GeographyPoint` ou `Collection(Edm.GeographyPoint)` campos no painel de navegação facetada. Facetas funcionam melhor em campos com baixa cardinalidade. Devido à resolução de coordenadas geográficas, é raro que quaisquer dois conjuntos de coordenadas será iguais em um determinado conjunto de dados. Dessa maneira, as facetas não têm suporte para coordenadas geográficas. Seria necessário um campo de cidade ou região para facetar por local.

## <a name="set-attributes"></a>Definir atributos

Atributos de índice que controlam como um campo é usado são adicionados às definições de campo individual no índice. No exemplo a seguir, os campos com baixa cardinalidade, úteis para a faceta, consistem em: `category` (hotel, motel, hostel), `tags`, e `rating`. Esses campos têm o `filterable` e `facetable` atributos conjunto explicitamente no exemplo a seguir para fins ilustrativos. 

> [!Tip]
> Como uma prática recomendada para o desempenho e otimização de armazenamento, desative a faceta para os campos que nunca devem ser usados como uma faceta. Em particular, os campos de cadeia de caracteres para valores exclusivos, como uma ID ou nome de produto, devem ser definidos `"facetable": false` para evitar seu uso acidental (e ineficaz) na navegação facetada.


```json
{
  "name": "hotels",  
  "fields": [
    { "name": "hotelId", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false },
    { "name": "baseRate", "type": "Edm.Double" },
    { "name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false },
    { "name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene" },
    { "name": "hotelName", "type": "Edm.String", "facetable": false },
    { "name": "category", "type": "Edm.String", "filterable": true, "facetable": true },
    { "name": "tags", "type": "Collection(Edm.String)", "filterable": true, "facetable": true },
    { "name": "parkingIncluded", "type": "Edm.Boolean",  "filterable": true, "facetable": true, "sortable": false },
    { "name": "smokingAllowed", "type": "Edm.Boolean", "filterable": true, "facetable": true, "sortable": false },
    { "name": "lastRenovationDate", "type": "Edm.DateTimeOffset" },
    { "name": "rating", "type": "Edm.Int32", "filterable": true, "facetable": true },
    { "name": "location", "type": "Edm.GeographyPoint" }
  ]
}
```

> [!Note]
> Esta definição de índice é copiada de [Criar um índice do Azure Search usando a API REST](https://docs.microsoft.com/azure/search/search-create-index-rest-api). Ela é idêntica, exceto por diferenças superficiais nas definições de campo. O `filterable` e `facetable` atributos são adicionados explicitamente nos `category`, `tags`, `parkingIncluded`, `smokingAllowed`, e `rating` campos. Na prática, `filterable` e `facetable` seriam habilitados por padrão, esses campos ao usar a API REST. Ao usar o SDK do .NET, esses atributos devem ser habilitados explicitamente.

## <a name="build-and-load-an-index"></a>Criar e carregar um índice

Uma etapa intermediária (e talvez óbvia) é que você precisa [criar e preencher o índice](https://docs.microsoft.com/azure/search/search-get-started-dotnet#1---create-index) antes de formular uma consulta. Mencionamos essa etapa aqui para fins de integridade. Uma maneira de determinar se o índice está disponível é verificando a lista de índices no [portal](https://portal.azure.com).

## <a name="add-facet-filters-to-a-query"></a>Adicionar filtros de faceta a uma consulta

No código do aplicativo, construa uma consulta que especifica todas as partes de uma consulta válida, incluindo expressões de pesquisa, facetas, filtros, perfis de pontuação – tudo o que for usado para formular uma solicitação. O exemplo a seguir cria uma solicitação que cria a navegação por faceta com base no tipo de acomodação, classificação e outras comodidades.

```csharp
var sp = new SearchParameters()
{
    ...
    // Add facets
    Facets = new[] { "category", "rating", "parkingIncluded", "smokingAllowed" }.ToList()
};
```

### <a name="return-filtered-results-on-click-events"></a>Retornar resultados filtrados em eventos de clique

Quando o usuário final clica em um valor de faceta, o manipulador para o evento de clique deve usar uma expressão de filtro para perceber a intenção do usuário. Considerando um `category` faceta, clicando na categoria "motel" é implementado com um `$filter` expressão que seleciona acomodações desse tipo. Quando um usuário clica em "motel" para indicar que somente motéis devem ser mostrados, a próxima consulta que o aplicativo envia inclui `$filter=category eq 'motel'`.

O seguinte snippet de código adiciona a categoria ao filtro se um usuário seleciona um valor usando a faceta de categoria.

```csharp
if (!String.IsNullOrEmpty(categoryFacet))
    filter = $"category eq '{categoryFacet}'";
```

Se o usuário clica em um valor de faceta para um campo de coleção como `tags`, por exemplo, o "grupo" de valor, seu aplicativo deve usar a seguinte sintaxe de filtro: `$filter=tags/any(t: t eq 'pool')`

## <a name="tips-and-workarounds"></a>Dicas e soluções alternativas

### <a name="initialize-a-page-with-facets-in-place"></a>Inicializar uma página com facetas aplicadas

Se desejar inicializar uma página com facetas aplicadas, você poderá enviar uma consulta como parte da inicialização da página para propagar a página com uma estrutura de faceta inicial.

### <a name="preserve-a-facet-navigation-structure-asynchronously-of-filtered-results"></a>Preservar uma estrutura de navegação por faceta de maneira assíncrona dos resultados filtrados

Um dos desafios da navegação por faceta no Azure Search é que as facetas existem somente para os resultados atuais. Na prática, é comum manter um conjunto estático de facetas para que o usuário possa navegar na ordem inversa, recolhendo etapas para explorar os caminhos alternativos por meio do conteúdo da pesquisa. 

Embora esse seja um caso de uso comum, não é algo que a estrutura de navegação por faceta atualmente forneça por padrão. Os desenvolvedores que desejam facetas estáticas normalmente contornam a limitação emitindo duas consultas filtradas: uma com escopo para os resultados e a outra usada para criar uma lista estática de facetas para fins de navegação.

## <a name="see-also"></a>Consulte também

+ [Filtros no Azure Search](search-filters.md)
+ [Criar API REST do índice](https://docs.microsoft.com/rest/api/searchservice/create-index)
+ [API REST para pesquisar documentos](https://docs.microsoft.com/rest/api/searchservice/search-documents)
