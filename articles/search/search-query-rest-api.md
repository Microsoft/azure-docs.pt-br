---
title: "Consultar um índice (API REST - Azure Search) | Microsoft Docs"
description: "Crie uma consulta de pesquisa na Pesquisa do Azure e use parâmetros de pesquisa para filtrar e classificar os resultados da pesquisa."
services: search
documentationcenter: 
manager: jhubbard
author: ashmaka
ms.assetid: 8b3ca890-2f5f-44b6-a140-6cb676fc2c9c
ms.service: search
ms.devlang: na
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 01/12/2017
ms.author: ashmaka
ms.openlocfilehash: 49062bec233ad35cd457f9665fa94c1855343582
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="query-your-azure-search-index-using-the-rest-api"></a>Consultar seu índice de Pesquisa do Azure usando a API REST
> [!div class="op_single_selector"]
>
> * [Visão geral](search-query-overview.md)
> * [Portal](search-explorer.md)
> * [.NET](search-query-dotnet.md)
> * [REST](search-query-rest-api.md)
>
>

Este artigo mostra como consultar um índice usando a [API REST do Azure Search](https://docs.microsoft.com/rest/api/searchservice/).

Antes de começar este passo a passo, você já deve ter [criado um índice do Azure Search](search-what-is-an-index.md), e este já deve estar [preenchido com os dados](search-what-is-data-import.md). Para obter informações de contexto, veja [Como funciona a pesquisa de texto completo no Azure Search](search-lucene-query-architecture.md).

## <a name="identify-your-azure-search-services-query-api-key"></a>Identificar sua api-key de consulta do serviço de Pesquisa do Azure
Um componente-chave de todas as operações de pesquisa na API REST da Pesquisa do Azure é a *api-key* que foi gerada para o serviço provisionado. Ter uma chave válida estabelece a relação de confiança, para cada solicitação, entre o aplicativo que envia a solicitação e o serviço que lida com ela.

1. Para localizar as api-keys de seu serviço, você deve entrar no [portal do Azure](https://portal.azure.com/)
2. Vá para a folha do serviço de Pesquisa do Azure
3. Clique no ícone "Chaves"

O serviço tem *chaves de administração* e *chaves de consulta*.

* Suas *chaves de administração* principal e secundária concedem direitos totais para todas as operações, incluindo a capacidade de gerenciar o serviço, criar e excluir índices, indexadores e fontes de dados. Há duas chaves para que você possa continuar a usar a chave secundária se decidir regenerar a chave primária e vice-versa.
* As *chaves de consulta* concedem acesso somente leitura a índices e documentos e normalmente são distribuídas para aplicativos cliente que emitem solicitações de pesquisa.

Para consultar um índice, você pode usar uma de suas chaves de consulta. As chaves de administração também podem ser usadas para consultas, mas você deve usar uma chave de consulta no código do aplicativo, já que isso segue melhor o [Princípio do privilégio mínimo](https://en.wikipedia.org/wiki/Principle_of_least_privilege).

## <a name="formulate-your-query"></a>Formular sua consulta
Há duas maneiras de [pesquisar o índice usando a API REST](https://docs.microsoft.com/rest/api/searchservice/Search-Documents). Uma delas consiste em emitir uma solicitação HTTP POST em que os parâmetros de consulta são definidos em um objeto JSON no corpo da solicitação. A outra maneira consiste em emitir uma solicitação HTTP GET em que os parâmetros de consulta são definidos na URL da solicitação. POST tem [limites mais flexíveis](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) quanto ao tamanho dos parâmetros de consulta do que GET. Por esse motivo, é recomendável usar POST, a menos que haja circunstâncias especiais em que o uso de GET seja mais conveniente.

Para POST e GET, você precisa fornecer o *nome do serviço*, o *nome do índice* e a *versão da API* adequada (a versão atual da API é `2016-09-01` no momento da publicação deste documento) na URL da solicitação. Para GET, você fornece os parâmetros de consulta na *cadeia de consulta* no fim da URL. Veja a seguir o formato da URL:

    https://[service name].search.windows.net/indexes/[index name]/docs?[query string]&api-version=2016-09-01

O formato para POST é o mesmo, mas apenas com api-version nos parâmetros da cadeia de caracteres de consulta.

#### <a name="example-queries"></a>Consultas de Exemplo
Veja algumas consultas de exemplo em um índice chamado "hotels". Essas consultas são mostradas nos formatos GET e POST.

Pesquise em todo o índice o termo “budget” e retorne somente o campo `hotelName`:

```
GET https://[service name].search.windows.net/indexes/hotels/docs?search=budget&$select=hotelName&api-version=2016-09-01

POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2016-09-01
{
    "search": "budget",
    "select": "hotelName"
}
```

Aplique um filtro no índice para encontrar os hotéis com valores inferiores a US$ 150 por noite e retorne `hotelId` e `description`:

```
GET https://[service name].search.windows.net/indexes/hotels/docs?search=*&$filter=baseRate lt 150&$select=hotelId,description&api-version=2016-09-01

POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2016-09-01
{
    "search": "*",
    "filter": "baseRate lt 150",
    "select": "hotelId,description"
}
```

Pesquise em todo o índice, ordene por um campo específico (`lastRenovationDate`) em ordem descendente, escolha os dois primeiros resultados e mostre apenas `hotelName` e `lastRenovationDate`:

```
GET https://[service name].search.windows.net/indexes/hotels/docs?search=*&$top=2&$orderby=lastRenovationDate desc&$select=hotelName,lastRenovationDate&api-version=2016-09-01

POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2016-09-01
{
    "search": "*",
    "orderby": "lastRenovationDate desc",
    "select": "hotelName,lastRenovationDate",
    "top": 2
}
```

## <a name="submit-your-http-request"></a>Enviar sua solicitação HTTP
Agora que formulou a consulta como parte da URL de solicitação HTTP (para GET) ou o corpo (POST), você pode definir os cabeçalhos de solicitação e enviar a consulta.

#### <a name="request-and-request-headers"></a>Solicitação e cabeçalhos de solicitação
Você deve definir dois cabeçalhos de solicitação de GET ou três de POST:

1. O cabeçalho `api-key` deve ser definido para a chave de consulta que você encontrou na etapa I acima. Você também pode usar uma chave de administração como o cabeçalho `api-key` , mas é recomendável usar uma chave de consulta, pois ela concede exclusivamente acesso de somente leitura a índices e documentos.
2. O cabeçalho `Accept` deve ser definido para `application/json`.
3. Apenas para POST, o cabeçalho `Content-Type` também deve ser definido para `application/json`.

Veja abaixo uma solicitação HTTP GET para pesquisar o índice "hotels" usando a API REST do Azure Search, usando uma consulta simples que pesquisa o termo "motel":

```
GET https://[service name].search.windows.net/indexes/hotels/docs?search=motel&api-version=2016-09-01
Accept: application/json
api-key: [query key]
```

Veja a mesma consulta de exemplo, desta vez usando HTTP POST:

```
POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2016-09-01
Content-Type: application/json
Accept: application/json
api-key: [query key]

{
    "search": "motel"
}
```

Uma solicitação de consulta bem-sucedida resultará em um Código de Status `200 OK` e os resultados da pesquisa serão retornados como JSON no corpo da resposta. Esta é a aparência dos resultados da consulta acima, supondo que o índice "hotéis" seja preenchido com os dados de exemplo em [Importação de Dados na Pesquisa do Azure usando a API REST](search-import-data-rest-api.md) (observe que o JSON foi formatado para ter clareza).

```JSON
{
    "value": [
        {
            "@search.score": 0.59600675,
            "hotelId": "2",
            "baseRate": 79.99,
            "description": "Cheapest hotel in town",
            "description_fr": "Hôtel le moins cher en ville",
            "hotelName": "Roach Motel",
            "category": "Budget",
            "tags":["motel", "budget"],
            "parkingIncluded": true,
            "smokingAllowed": true,
            "lastRenovationDate": "1982-04-28T00:00:00Z",
            "rating": 1,
            "location": {
                "type": "Point",
                "coordinates": [-122.131577, 49.678581],
                "crs": {
                    "type":"name",
                    "properties": {
                        "name": "EPSG:4326"
                    }
                }
            }
        }
    ]
}
```

Para saber mais, visite a seção "Resposta" de [Pesquisar Documentos](https://docs.microsoft.com/rest/api/searchservice/Search-Documents). Para obter mais informações sobre outros códigos de status HTTP que podem ser retornados em caso de falha, confira [Códigos de status HTTP (Pesquisa do Azure)](https://docs.microsoft.com/rest/api/searchservice/HTTP-status-codes).
