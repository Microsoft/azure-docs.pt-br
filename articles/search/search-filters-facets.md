---
title: Filtros de faceta para navegação de pesquisa em aplicativos – Azure Search
description: Critérios de filtro por identidade de segurança do usuário, localização geográfica ou valores numéricos para reduzir os resultados da pesquisa em consultas no Azure Search, um serviço de pesquisa de nuvem hospedado no Microsoft Azure.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 10/13/2017
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 8793f6f4d135d6099541d24aa5f5cfc0b6c21b30
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61290040"
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

As facetas podem ser calculadas por campos de valor único e por coleções. Campos que funcionam mais bem em navegação facetada têm baixa cardinalidade: um pequeno número de valores distintos que se repetem em documentos em seu corpo de pesquisa (por exemplo, uma lista de nomes de marca, países ou cores). 

A faceta é habilitada campo a campo quando você cria o índice, definindo os seguintes atributos como TRUE: `filterable`, `facetable`. Somente campos filtráveis podem ser facetados.

Todos os [tipos de campo](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) que poderiam ser usados na navegação facetada são marcados como “facetável”:

+ Edm.String
+ Edm.DateTimeOffset
+ Edm.Boolean
+ Edm.Collections
+ Tipos de campo numérico: Edm.Int32, Edm.Int64, Edm.Double

Não é possível usar Edm.GeographyPoint na navegação facetada. Facetas são construídas com texto ou números legíveis para humanos. Dessa maneira, as facetas não têm suporte para coordenadas geográficas. Seria necessário um campo de cidade ou região para facetar por local.

## <a name="set-attributes"></a>Definir atributos

Atributos de índice que controlam como um campo é usado são adicionados às definições de campo individual no índice. No exemplo a seguir, os campos com baixa cardinalidade, úteis para a faceta, consistem em: categoria (hotel, motel, hostel), comodidades e classificações. 

Na API do .NET, os atributos de filtragem precisam ser definidos explicitamente. Na API REST, facetar e filtrar estão habilitados por padrão, o que significa que você só precisa definir explicitamente os atributos quando quiser desativá-los. Embora não seja tecnicamente necessário, mostramos as atribuições no seguinte exemplo de REST para fins de instrução. 

> [!Tip]
> Como uma prática recomendada para o desempenho e otimização de armazenamento, desative a faceta para os campos que nunca devem ser usados como uma faceta. Em particular, os campos de cadeia de caracteres para valores únicos, como uma ID ou nome de produto, devem ser definidos como “Facetável”:false para evitar seu uso acidental (e ineficaz) na navegação facetada.


```http
{
    "name": "hotels",  
    "fields": [
        {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false},
        {"name": "baseRate", "type": "Edm.Double"},
        {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
        {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
        {"name": "hotelName", "type": "Edm.String", "facetable": false},
        {"name": "category", "type": "Edm.String", "filterable": true, "facetable": true},
        {"name": "tags", "type": "Collection(Edm.String)", "filterable": true, "facetable": true},
        {"name": "parkingIncluded", "type": "Edm.Boolean",  "filterable": true, "facetable": true, "sortable": false},
        {"name": "smokingAllowed", "type": "Edm.Boolean", "filterable": true, "facetable": true, "sortable": false},
        {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
        {"name": "rating", "type": "Edm.Int32", "filterable": true, "facetable": true},
        {"name": "location", "type": "Edm.GeographyPoint"}
    ]
}
```

> [!Note]
> Esta definição de índice é copiada de [Criar um índice do Azure Search usando a API REST](https://docs.microsoft.com/azure/search/search-create-index-rest-api). Ela é idêntica, exceto por diferenças superficiais nas definições de campo. Os atributos filtráveis e facetáveis são adicionados explicitamente nos campos de categoria, marcas, parkingIncluded, smokingAllowed e classificação. Na prática, você pode ser filtrado e facetado gratuitamente em tipos de campo de Edm.String, Edm.Boolean e Int32. 

## <a name="build-and-load-an-index"></a>Criar e carregar um índice

Uma etapa intermediária (e talvez óbvia) é que você precisa [criar e preencher o índice](https://docs.microsoft.com/azure/search/search-create-index-dotnet#3---construct-index) antes de formular uma consulta. Mencionamos essa etapa aqui para fins de integridade. Uma maneira de determinar se o índice está disponível é verificando a lista de índices no [portal](https://portal.azure.com).

## <a name="add-facet-filters-to-a-query"></a>Adicionar filtros de faceta a uma consulta

No código do aplicativo, construa uma consulta que especifica todas as partes de uma consulta válida, incluindo expressões de pesquisa, facetas, filtros, perfis de pontuação – tudo o que for usado para formular uma solicitação. O exemplo a seguir cria uma solicitação que cria a navegação por faceta com base no tipo de acomodação, classificação e outras comodidades.

```csharp
SearchParameters sp = new SearchParameters()
{
  ...
  // Add facets
  Facets = new List<String>() { "category", "rating", "parkingIncluded", "smokingAllowed" },
};
```

### <a name="return-filtered-results-on-click-events"></a>Retornar resultados filtrados em eventos de clique

A expressão de filtro manipula o evento de clique no valor da faceta. Dada uma faceta de Categoria, clicar na categoria "motel" é implementado por meio de uma expressão `$filter` que seleciona acomodações desse tipo. Quando um usuário clica em "motéis" para indicar que somente motéis devem ser mostrados, a próxima consulta que o aplicativo envia inclui $filter=category eq ‘motéis’.

O seguinte snippet de código adiciona a categoria ao filtro se um usuário seleciona um valor usando a faceta de categoria.

```csharp
if (categoryFacet != "")
  filter = "category eq '" + categoryFacet + "'";
```
Ao usar a API REST, a solicitação seria articulada como `$filter=category eq 'c1'`. Para tornar a categoria um campo de múltiplos valores, use a seguinte sintaxe: `$filter=category/any(c: c eq 'c1')`

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
