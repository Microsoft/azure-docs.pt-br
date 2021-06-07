---
title: Atualizar versões da API REST
titleSuffix: Azure Cognitive Search
description: Examine as diferenças nas versões de API e saiba quais ações são necessárias para migrar o código existente para a versão mais recente da API REST do serviço de Pesquisa Cognitiva do Azure.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/09/2020
ms.openlocfilehash: d7734fde529c24e8113ea3b019d343b7223f0122
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91929635"
---
# <a name="upgrade-to-the-latest-rest-api-in-azure-cognitive-search"></a>Atualizar para a API REST mais recente no Azure Pesquisa Cognitiva

Se você estiver usando uma versão anterior da [**API REST de pesquisa**](/rest/api/searchservice/), este artigo o ajudará a atualizar seu aplicativo para a versão de API mais recente disponível, **2020-06-30**.

A versão 2020-06-30 inclui um novo recurso importante ([repositório de conhecimento](knowledge-store-concept-intro.md)) e apresenta várias alterações de comportamento secundárias. Dessa forma, essa versão é, na maioria das vezes, compatível com versões anteriores, portanto, as alterações de código devem ser mínimas se você estiver atualizando da versão anterior (2019-05-06).

> [!NOTE]
> Um serviço de pesquisa dá suporte a uma variedade de versões da API REST, incluindo as anteriores. Você pode continuar a usar essas versões de API, mas é recomendável migrar seu código para a versão mais recente para que você possa acessar novos recursos. Ao longo do tempo, as versões mais desatualizadas da API REST serão preteridas e [não mais suportadas](search-api-versions.md#unsupported-versions).

<a name="UpgradeSteps"></a>

## <a name="how-to-upgrade"></a>Como atualizar

Ao atualizar para uma nova versão, você provavelmente não precisará fazer muitas alterações em seu código, além de alterar o número de versão. As únicas situações em que talvez seja necessário alterar o código ocorrem quando:

* O código falha quando propriedades não reconhecidas são retornadas em uma resposta da API. Por padrão, o aplicativo deve ignorar propriedades que não entende.

* O código persiste solicitações de API e tenta reenviá-las à nova versão da API. Por exemplo, isso poderá acontecer se o aplicativo persistir tokens de continuação retornados da API de Pesquisa (para obter mais informações, procure `@search.nextPageParameters` na [Referência de API de Pesquisa](/rest/api/searchservice/Search-Documents)).

* Seu código faz referência a uma versão de API que é preactualizada 2019-05-06 e está sujeita a uma ou mais das alterações significativas nessa versão. A seção [atualizar para 2019-05-06](#upgrade-to-2019-05-06) fornece mais detalhes. 

Se qualquer uma dessas situações se aplicar a você, talvez seja necessário alterar seu código de acordo. Caso contrário, nenhuma alteração deve ser necessária, embora você possa querer começar a usar os recursos adicionados na nova versão.

## <a name="upgrade-to-2020-06-30"></a>Atualizar para o 2020-06-30

A versão 2020-06-30 é a nova versão geralmente disponível da API REST. Há uma alteração significativa e várias diferenças comportamentais. 

Os recursos agora estão disponíveis para o público em geral nesta versão de API:

* [Loja de conhecimento](knowledge-store-concept-intro.md), armazenamento persistente de conteúdo aprimorado criado por meio de habilidades, criado para análise e processamento downstream por meio de outros aplicativos. Com esse recurso, um pipeline de enriquecimento de ia controlado por indexador pode preencher uma loja de conhecimento além de um índice de pesquisa. Se você usou a versão de visualização desse recurso, ele é equivalente à versão disponível. A única alteração de código necessária é modificar a versão de API.

### <a name="breaking-change"></a>Alteração da falha

O código existente escrito em versões anteriores da API será interrompido na API-Version = 2020-06-30 e posterior se o código contiver a seguinte funcionalidade:

* Qualquer literal EDM. Date (uma data composta do ano-mês-dia, como `2020-12-12` ) em expressões de filtro deve seguir o formato EDM. DateTimeOffset: `2020-12-12T00:00:00Z` . Essa alteração foi necessária para lidar com resultados de consulta incorretos ou inesperados devido a diferenças de fuso horário.

### <a name="behavior-changes"></a>Alterações de comportamento

* O [algoritmo de classificação BM25](index-ranking-similarity.md) substitui o algoritmo de classificação anterior por uma tecnologia mais nova. Novos serviços usarão esse algoritmo automaticamente. Para os serviços existentes, você deve definir parâmetros para usar o novo algoritmo.

* Os resultados ordenados para valores nulos foram alterados nesta versão, com valores nulos aparecendo primeiro se a classificação for `asc` e a última se a classificação for `desc` . Se você escreveu o código para manipular como os valores nulos são classificados, lembre-se dessa alteração.

## <a name="upgrade-to-2019-05-06"></a>Atualizar para o 2019-05-06

A versão 2019-05-06 é a versão mais antiga disponível da API REST. Os recursos que se tornaram disponíveis em geral nesta versão de API incluem:

* O [preenchimento automático](index-add-suggesters.md) é um recurso typeahead que conclui uma entrada de termo parcialmente especificada.
* [Tipos complexos](search-howto-complex-data-types.md) fornecem suporte nativo para dados de objeto estruturado no índice de pesquisa.
* Os [modos de análise de JsonLines](search-howto-index-json-blobs.md), parte da indexação de BLOBs do Azure, cria um documento de pesquisa por entidade JSON que é separado por uma nova linha.
* O [enriquecimento de ia](cognitive-search-concept-intro.md) fornece indexação que aproveita os mecanismos de enriquecimento de ia de serviços cognitivas.

### <a name="breaking-changes"></a>Alterações de quebra

O código existente escrito em versões anteriores da API será interrompido na API-Version = 2019-05-06 e posterior se o código contiver a seguinte funcionalidade:

#### <a name="indexer-for-azure-cosmos-db---datasource-is-now-type-cosmosdb"></a>O indexador para Azure Cosmos DB-DataSource agora é "tipo": "cosmosdb"

Se você estiver usando um [indexador Cosmos DB](search-howto-index-cosmosdb.md ), será necessário alterar `"type": "documentdb"` para `"type": "cosmosdb"` .

#### <a name="indexer-execution-result-errors-no-longer-have-status"></a>Os erros de resultado da execução do indexador não têm mais status

A estrutura de erros para a execução do indexador anteriormente tinha um `status` elemento. Este elemento foi removido porque não estava fornecendo informações úteis.

#### <a name="indexer-data-source-api-no-longer-returns-connection-strings"></a>A API de fonte de dados do indexador não retorna mais cadeias de conexão

Nas versões de API 2019-05-06 e 2019-05-06-preview em diante, a API de fonte de dados não retorna mais cadeias de conexão na resposta de qualquer operação REST. Nas versões anteriores da API, para fontes de dados criadas usando POST, o Azure Pesquisa Cognitiva retornou **201** seguido pela resposta do OData, que continha a cadeia de conexão em texto sem formatação.

#### <a name="named-entity-recognition-cognitive-skill-is-now-discontinued"></a>A habilidade cognitiva de reconhecimento de entidade nomeada agora está descontinuada

Se você chamou a habilidade de [reconhecimento de entidade de nome](cognitive-search-skill-named-entity-recognition.md) em seu código, a chamada falhará. A funcionalidade de substituição é o [reconhecimento de entidade](cognitive-search-skill-entity-recognition.md). Você deve ser capaz de substituir a referência de habilidades sem outras alterações. A assinatura de API é a mesma para ambas as versões. 

### <a name="upgrading-complex-types"></a>Atualizando tipos complexos

A versão de API 2019-05-06 adicionou suporte formal para tipos complexos. Se seu código implementou recomendações anteriores para equivalência de tipo complexo no 2017-11-11-Preview ou 2016-09-01-Preview, há alguns limites novos e alterados a partir da versão 2019-05-06 da qual você precisa estar ciente:

+ Os limites na profundidade de subcampos e o número de coleções complexas por índice foram reduzidos. Se você criou índices que excedem esses limites usando as versões de API de visualização, qualquer tentativa de atualizar ou recriá-los usando a versão de API 2019-05-06 falhará. Se isso se aplicar a você, você precisará recriar o esquema para se ajustar aos novos limites e, em seguida, recriar o índice.

+ Há um novo limite a partir da versão de API 2019-05-06 no número de elementos de coleções complexas por documento. Se você criou índices com documentos que excedem esses limites usando as versões de API de visualização, qualquer tentativa de reindexar esses dados usando a versão de API 2019-05-06 falhará. Se isso se aplicar a você, você precisará reduzir o número de elementos de coleção complexos por documento antes de reindexar seus dados.

Para obter mais informações, consulte [limites de serviço para o Azure pesquisa cognitiva](search-limits-quotas-capacity.md).

#### <a name="how-to-upgrade-an-old-complex-type-structure"></a>Como atualizar uma estrutura antiga de tipo complexo

Se o seu código estiver usando tipos complexos com uma das versões mais antigas da API de visualização, você poderá estar usando um formato de definição de índice parecido com este:

```json
{
  "name": "hotels",  
  "fields": [
    { "name": "HotelId", "type": "Edm.String", "key": true, "filterable": true },
    { "name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": true, "facetable": false },
    { "name": "Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.microsoft" },
    { "name": "Description_fr", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.microsoft" },
    { "name": "Category", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "sortable": false, "facetable": true, "analyzer": "tagsAnalyzer" },
    { "name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "sortable": true, "facetable": true },
    { "name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": true, "sortable": true, "facetable": true },
    { "name": "Rating", "type": "Edm.Double", "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address", "type": "Edm.ComplexType" },
    { "name": "Address/StreetAddress", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "searchable": true },
    { "name": "Address/City", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address/StateProvince", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address/PostalCode", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address/Country", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Location", "type": "Edm.GeographyPoint", "filterable": true, "sortable": true },
    { "name": "Rooms", "type": "Collection(Edm.ComplexType)" }, 
    { "name": "Rooms/Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.lucene" },
    { "name": "Rooms/Description_fr", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene" },
    { "name": "Rooms/Type", "type": "Edm.String", "searchable": true },
    { "name": "Rooms/BaseRate", "type": "Edm.Double", "filterable": true, "facetable": true },
    { "name": "Rooms/BedOptions", "type": "Edm.String", "searchable": true },
    { "name": "Rooms/SleepsCount", "type": "Edm.Int32", "filterable": true, "facetable": true },
    { "name": "Rooms/SmokingAllowed", "type": "Edm.Boolean", "filterable": true, "facetable": true },
    { "name": "Rooms/Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "facetable": true, "analyzer": "tagsAnalyzer" }
  ]
}  
```

Um formato mais recente do tipo árvore para definir campos de índice foi introduzido na versão de API 2017-11-11-Preview. No novo formato, cada campo complexo tem uma coleção Fields onde seus subcampos são definidos. Na API versão 2019-05-06, esse novo formato é usado exclusivamente e a tentativa de criar ou atualizar um índice usando o formato antigo falhará. Se você tiver índices criados usando o formato antigo, precisará usar a versão de API 2017-11-11-Preview para atualizá-los para o novo formato antes que eles possam ser gerenciados usando a versão de API 2019-05-06.

Você pode atualizar índices "simples" para o novo formato com as seguintes etapas usando a versão de API 2017-11-11-Preview:

1. Execute uma solicitação GET para recuperar o índice. Se já estiver no novo formato, você terminará.

2. Traduza o índice do formato "flat" para o novo formato. Você precisará escrever código para isso, já que não há nenhum código de exemplo disponível no momento da redação deste artigo.

3. Execute uma solicitação PUT para atualizar o índice para o novo formato. Certifique-se de não alterar nenhum outro detalhe do índice, como a pesquisa/filtragem de campos, pois isso não é permitido pela API do índice de atualização.

> [!NOTE]
> Não é possível gerenciar índices criados com o antigo formato "plano" do portal do Azure. Atualize seus índices da representação "simples" para a representação de "árvore" da sua primeira conveniência.

## <a name="next-steps"></a>Próximas etapas

Examine a documentação de referência da API REST de pesquisa. Se você tiver problemas, peça ajuda em [Stack Overflow](https://stackoverflow.com/) ou [entre em contato com o suporte](https://azure.microsoft.com/support/community/?product=search).

> [!div class="nextstepaction"]
> [Referência da API REST do serviço de pesquisa](/rest/api/searchservice/)
