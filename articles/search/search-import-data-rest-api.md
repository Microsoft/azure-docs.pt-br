---
title: Carregar dados (API REST - Azure Search) | Microsoft Docs
description: "Aprenda a carregar dados em um índice na Pesquisa do Azure usando a API REST."
services: search
documentationcenter: 
author: ashmaka
manager: jhubbard
editor: 
tags: 
ms.assetid: 8d0749fb-6e08-4a17-8cd3-1a215138abc6
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 12/08/2016
ms.author: ashmaka
ms.openlocfilehash: f22a33ed86fbfc46dfa732239263a49f34c4afee
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="upload-data-to-azure-search-using-the-rest-api"></a>Carregar dados para a Pesquisa do Azure usando a API REST
> [!div class="op_single_selector"]
>
> * [Visão geral](search-what-is-data-import.md)
> * [.NET](search-import-data-dotnet.md)
> * [REST](search-import-data-rest-api.md)
>
>

Este artigo mostrará como usar a [API REST de Pesquisa do Azure](https://docs.microsoft.com/rest/api/searchservice/) para importar dados para um índice de Pesquisa do Azure.

Antes de começar este passo a passo, você já deve ter [criado um índice de Pesquisa do Azure](search-what-is-an-index.md).

Para enviar documentos no índice usando a API REST, você emitirá uma solicitação HTTP POST para o ponto de extremidade da URL do índice. O corpo da solicitação HTTP é um objeto JSON que contém os documentos a serem adicionados, modificados ou excluídos.

## <a name="identify-your-azure-search-services-admin-api-key"></a>Identificar a chave de API do administrador de seu serviço de Pesquisa do Azure
Ao emitir solicitações HTTP em seu serviço usando a API REST, *todas* as solicitações de API devem incluir a api-key que foi gerada para o serviço de Pesquisa provisionado. Ter uma chave válida estabelece a relação de confiança, para cada solicitação, entre o aplicativo que envia a solicitação e o serviço que lida com ela.

1. Para localizar as api-keys de seu serviço, você deve entrar no [portal do Azure](https://portal.azure.com/)
2. Vá para a folha do serviço de Pesquisa do Azure
3. Clique no ícone de "Chaves"

O serviço terá *chaves de administração* e *chaves de consulta*.

* Suas *chaves de administração* principal e secundária concedem direitos totais para todas as operações, incluindo a capacidade de gerenciar o serviço, criar e excluir índices, indexadores e fontes de dados. Há duas chaves para que você possa continuar a usar a chave secundária se decidir regenerar a chave primária e vice-versa.
* As *chaves de consulta* concedem acesso somente leitura a índices e documentos e normalmente são distribuídas a aplicativos cliente que emitem solicitações de pesquisa.

Para importar dados para um índice, você pode usar a chave de administração principal ou secundária.

## <a name="decide-which-indexing-action-to-use"></a>Decidir qual ação de indexação será usada
Ao usar a API REST, você emitirá solicitações HTTP POST com corpos de solicitação JSON para a URL de ponto de extremidade do índice de Pesquisa do Azure. O objeto JSON no corpo da solicitação HTTP conterá uma única matriz JSON chamada "value", que contém objetos JSON que representam documentos que você deseja adicionar ao índice, atualizar ou excluir.

Cada objeto JSON da matriz "value" representa um documento a ser indexado. Cada um desses objetos contém a chave do documento e especifica a ação de indexação desejada (carregar, mesclar, excluir, etc.). Dependendo de qual das ações abaixo você escolher, apenas determinados campos deverão ser incluídos em cada documento:

| @search.action | Descrição | Campos necessários para cada documento | Observações |
| --- | --- | --- | --- |
| `upload` |Uma ação `upload` é semelhante a um "upsert", em que o documento será inserido se for novo e atualizado/substituído se existir. |chave, além de quaisquer outros campos que você quiser definir |Ao atualizar/substituir um documento existente, qualquer campo não especificado na solicitação terá seu campo definido para `null`. Isso ocorre mesmo quando o campo tiver sido definido anteriormente como um valor não nulo. |
| `merge` |Atualiza um documento existente com os campos especificados. Se o documento não existir no índice, a mesclagem falhará. |chave, além de quaisquer outros campos que você quiser definir |Qualquer campo que você especificar em uma mesclagem substituirá o campo existente no documento. Isso inclui campos do tipo `Collection(Edm.String)`. Por exemplo, se o documento contiver um campo `tags` com o valor `["budget"]` e você executar uma mesclagem com o valor `["economy", "pool"]` para `tags`, o valor final do campo `tags` será `["economy", "pool"]`. Ele não será `["budget", "economy", "pool"]`. |
| `mergeOrUpload` |Essa ação se comportará como `merge` se já existir um documento com a chave especificada no índice. Se o documento não existir, ele se comportará como `upload` com um novo documento. |chave, além de quaisquer outros campos que você quiser definir |- |
| `delete` |Remove o documento especificado do índice. |somente chave |Todos os campos que você especificar, exceto o campo de chave, serão ignorados. Se você quiser remover um campo individual de um documento, use `merge` e apenas defina o campo explicitamente para null. |

## <a name="construct-your-http-request-and-request-body"></a>Construir sua solicitação HTTP e o corpo da solicitação
Agora que coletou os valores de campo necessários para as ações de índice, você está pronto para construir a solicitação HTTP real e o corpo da solicitação JSON para importar os dados.

#### <a name="request-and-request-headers"></a>Solicitação e Cabeçalhos de Solicitação
Na URL, você precisará fornecer o nome do serviço, nome do índice ("hotéis", neste caso), bem como a versão da API apropriada (a versão atual da API é `2016-09-01` no momento da publicação deste documento). Você precisará definir os cabeçalhos de solicitação `Content-Type` e `api-key`. Para a última opção, use uma das chaves de administração do serviço.

    POST https://[search service].search.windows.net/indexes/hotels/docs/index?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

#### <a name="request-body"></a>Corpo da solicitação
```JSON
{
    "value": [
        {
            "@search.action": "upload",
            "hotelId": "1",
            "baseRate": 199.0,
            "description": "Best hotel in town",
            "description_fr": "Meilleur hôtel en ville",
            "hotelName": "Fancy Stay",
            "category": "Luxury",
            "tags": ["pool", "view", "wifi", "concierge"],
            "parkingIncluded": false,
            "smokingAllowed": false,
            "lastRenovationDate": "2010-06-27T00:00:00Z",
            "rating": 5,
            "location": { "type": "Point", "coordinates": [-122.131577, 47.678581] }
        },
        {
            "@search.action": "upload",
            "hotelId": "2",
            "baseRate": 79.99,
            "description": "Cheapest hotel in town",
            "description_fr": "Hôtel le moins cher en ville",
            "hotelName": "Roach Motel",
            "category": "Budget",
            "tags": ["motel", "budget"],
            "parkingIncluded": true,
            "smokingAllowed": true,
            "lastRenovationDate": "1982-04-28T00:00:00Z",
            "rating": 1,
            "location": { "type": "Point", "coordinates": [-122.131577, 49.678581] }
        },
        {
            "@search.action": "mergeOrUpload",
            "hotelId": "3",
            "baseRate": 129.99,
            "description": "Close to town hall and the river"
        },
        {
            "@search.action": "delete",
            "hotelId": "6"
        }
    ]
}
```

Nesse caso, estamos usando `upload`, `mergeOrUpload` e `delete` como nossas ações de pesquisa.

Suponha que o índice de exemplo "hotels" já esteja preenchido com vários documentos. Observe como não precisamos especificar todos os campos de documento possíveis ao usar `mergeOrUpload` e como especificamos apenas a chave do documento (`hotelId`) ao usar `delete`.

Além disso, observe que você só pode incluir até 1000 documentos (ou 16 MB) em uma única solicitação de indexação.

## <a name="understand-your-http-response-code"></a>Compreender o código de resposta HTTP
#### <a name="200"></a>200
Após enviar uma solicitação de indexação bem-sucedida, você receberá uma resposta HTTP com o código de status `200 OK`. O corpo JSON de resposta HTTP será o seguinte:

```JSON
{
    "value": [
        {
            "key": "unique_key_of_document",
            "status": true,
            "errorMessage": null
        },
        ...
    ]
}
```

#### <a name="207"></a>207
Um código de status `207` será retornado quando pelo menos um item não tiver sido indexado com êxito. O corpo JSON da resposta HTTP conterá informações sobre o(s) documento(s) sem êxito.

```JSON
{
    "value": [
        {
            "key": "unique_key_of_document",
            "status": false,
            "errorMessage": "The search service is too busy to process this document. Please try again later."
        },
        ...
    ]
}
```

> [!NOTE]
> Muitas vezes, isso significa que a carga no serviço de pesquisa está atingindo um ponto em que as solicitações de indexação começarão a retornar respostas `503`. Nesse caso, é altamente recomendável que o código do cliente faça uma pausa e aguarde antes de tentar novamente. Isso dará ao sistema algum tempo para se recuperar, aumentando as chances de que solicitações futuras sejam bem-sucedidas. Se você repetir rapidamente as solicitações, isso apenas prolongará a situação.
>
>

#### <a name="429"></a>429
Um código de status `429` será retornado quando você excedeu sua cota no número de documentos por índice.

#### <a name="503"></a>503
Um código de status `503` será retornado se nenhum dos itens na solicitação foi indexado com êxito. Esse erro significa que o sistema está sob carga pesada e sua solicitação não pode ser processada no momento.

> [!NOTE]
> Nesse caso, é altamente recomendável que o código do cliente faça uma pausa e aguarde antes de tentar novamente. Isso dará ao sistema algum tempo para se recuperar, aumentando as chances de que solicitações futuras sejam bem-sucedidas. Se você repetir rapidamente as solicitações, isso apenas prolongará a situação.
>
>

Para obter mais informações sobre as ações do documento e as respostas de êxito/erro, consulte [Adicionar, Atualizar ou Excluir Documentos](https://docs.microsoft.com/rest/api/searchservice/AddUpdate-or-Delete-Documents). Para obter mais informações sobre outros códigos de status HTTP que podem ser retornados em caso de falha, confira [Códigos de status HTTP (Pesquisa do Azure)](https://docs.microsoft.com/rest/api/searchservice/HTTP-status-codes).

## <a name="next-steps"></a>Próximas etapas
Depois de popular o índice de Pesquisa do Azure, você estará pronto para começar a emitir consultas para pesquisar documentos. Veja [Consultar seu Índice de Pesquisa do Azure](search-query-overview.md) para obter detalhes.
