---
title: "Criar um índice (API REST - Azure Search) | Microsoft Docs"
description: "Crie um índice no código usando a API REST HTTP da Pesquisa do Azure."
services: search
documentationcenter: 
author: ashmaka
manager: jhubbard
editor: 
tags: azure-portal
ms.assetid: ac6c5fba-ad59-492d-b715-d25a7a7ae051
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 12/08/2016
ms.author: ashmaka
ms.openlocfilehash: 9a64d1436471e406b7d9b700257d3dd96b5edcde
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-azure-search-index-using-the-rest-api"></a>Criar um índice de pesquisa do Azure usando a API REST
> [!div class="op_single_selector"]
>
> * [Visão geral](search-what-is-an-index.md)
> * [Portal](search-create-index-portal.md)
> * [.NET](search-create-index-dotnet.md)
> * [REST](search-create-index-rest-api.md)
>
>

Este artigo o orientará ao longo do processo de criação de um [índice](https://docs.microsoft.com/rest/api/searchservice/Create-Index) de Pesquisa do Azure usando a API REST da Pesquisa do Azure.

Antes de seguir este guia e criar um índice, você já deverá ter [criado um serviço de Pesquisa do Azure](search-create-service-portal.md).

Para criar um índice de Pesquisa do Azure usando a API REST, você emitirá uma única solicitação HTTP POST para o ponto de extremidade da URL do serviço de Pesquisa do Azure. A definição de índice estará contida no corpo da solicitação como conteúdo JSON bem formado.

## <a name="identify-your-azure-search-services-admin-api-key"></a>Identificar a chave de API do administrador de seu serviço de Pesquisa do Azure
Agora que provisionou um serviço de Pesquisa do Azure, você pode emitir solicitações HTTP em relação ao ponto de extremidade de URL do serviço usando a API REST. *Todas* as solicitações de API devem incluir a api-key que foi gerada para o serviço de Pesquisa que você provisionou. Ter uma chave válida estabelece a relação de confiança, para cada solicitação, entre o aplicativo que envia a solicitação e o serviço que lida com ela.

1. Para localizar as api-keys de seu serviço, você deve fazer logon no [portal do Azure](https://portal.azure.com/)
2. Vá para a folha do serviço de Pesquisa do Azure
3. Clique no ícone de "Chaves"

O serviço terá *chaves de administração* e *chaves de consulta*.

* Suas *chaves de administração* principal e secundária concedem direitos totais para todas as operações, incluindo a capacidade de gerenciar o serviço, criar e excluir índices, indexadores e fontes de dados. Há duas chaves para que você possa continuar a usar a chave secundária se decidir regenerar a chave primária e vice-versa.
* As *chaves de consulta* concedem acesso somente leitura a índices e documentos e normalmente são distribuídas a aplicativos cliente que emitem solicitações de pesquisa.

Para criar um índice, você pode usar a chave de administração principal ou secundária.

## <a name="define-your-azure-search-index-using-well-formed-json"></a>Definir o índice de Pesquisa do Azure usando JSON bem formado
Uma única solicitação HTTP POST para o serviço criará o índice. O corpo da solicitação HTTP POST conterá um único objeto JSON que define o índice de Pesquisa do Azure.

1. A primeira propriedade desse objeto JSON é o nome do índice.
2. A segunda propriedade do objeto JSON é uma matriz JSON chamada `fields` que contém um objeto JSON separado para cada campo no índice. Cada um desses objetos JSON contém vários pares de nome/valor para cada um dos atributos do campo, incluindo "nome", "tipo", etc.

É importante ter em mente suas necessidades de negócios e experiência de usuário de pesquisa ao projetar o índice, pois cada campo deve ter os [atributos apropriados](https://docs.microsoft.com/rest/api/searchservice/Create-Index). Esses atributos controlam quais recursos de pesquisa (filtragem, facetas, classificação, pesquisa de texto completo, etc.) se aplicam a quais campos. Para qualquer atributo que você não especificar, o padrão será habilitar o recurso de pesquisa correspondente, a menos que você o desabilite especificamente.

Para nosso exemplo, chamamos o índice de "hotels" e definimos os campos da seguinte maneira:

```JSON
{
    "name": "hotels",  
    "fields": [
        {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false},
        {"name": "baseRate", "type": "Edm.Double"},
        {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
        {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
        {"name": "hotelName", "type": "Edm.String", "facetable": false},
        {"name": "category", "type": "Edm.String"},
        {"name": "tags", "type": "Collection(Edm.String)"},
        {"name": "parkingIncluded", "type": "Edm.Boolean", "sortable": false},
        {"name": "smokingAllowed", "type": "Edm.Boolean", "sortable": false},
        {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
        {"name": "rating", "type": "Edm.Int32"},
        {"name": "location", "type": "Edm.GeographyPoint"}
    ]
}
```

Escolhemos cuidadosamente os atributos de índice para cada campo com base em como achamos que serão usados em um aplicativo. Por exemplo, `hotelId` é uma chave exclusiva que as pessoas que pesquisam hotéis provavelmente não conhecerão. Portanto, desabilitamos a pesquisa de texto completo para esse campo definindo `searchable` como `false`, o que economiza espaço no índice.

Observe que exatamente um campo no índice do tipo `Edm.String` deve ser designado como o campo 'key'.

A definição de índice acima usa um analisador de idioma para o campo `description_fr` porque ele se destina a armazenar texto em francês. Consulte [o tópico de Suporte a idiomas no](https://docs.microsoft.com/rest/api/searchservice/Language-support), bem como a [postagem do blog](https://azure.microsoft.com/blog/language-support-in-azure-search/) correspondente para obter mais informações sobre os analisadores de linguagem.

## <a name="issue-the-http-request"></a>Emitir a solicitação HTTP
1. Usando a definição do índice como o corpo da solicitação, emita uma solicitação HTTP POST para a URL do ponto de extremidade do serviço de Pesquisa do Azure. Na URL, use o nome do serviço como o nome do host e coloque o `api-version` adequado como um parâmetro de cadeia de caracteres de consulta (a versão atual da API é `2016-09-01` no momento da publicação deste documento).
2. Nos cabeçalhos de solicitação, especifique o `Content-Type` como `application/json`. Você também precisará fornecer a chave de administração do serviço que identificou na Etapa I no cabeçalho `api-key` .

Você terá que fornecer sua própria chave de api e o nome do serviço para emitir a solicitação abaixo:

    POST https://[service name].search.windows.net/indexes?api-version=2016-09-01
    Content-Type: application/json
    api-key: [api-key]


Para uma solicitação bem-sucedida, você deverá ver o código de status 201 (Criado). Para obter mais informações sobre como criar um índice por meio da API REST, acesse a [referência de API](https://docs.microsoft.com/rest/api/searchservice/Create-Index). Para obter mais informações sobre outros códigos de status HTTP que podem ser retornados em caso de falha, confira [Códigos de status HTTP (Pesquisa do Azure)](https://docs.microsoft.com/rest/api/searchservice/HTTP-status-codes).

Quando você terminar de usar um índice e desejar excluí-lo, bastará emitir uma solicitação HTTP DELETE. Por exemplo, veja como podemos excluir o índice "hotels":

    DELETE https://[service name].search.windows.net/indexes/hotels?api-version=2016-09-01
    api-key: [api-key]


## <a name="next-steps"></a>Próximas etapas
Após criar um índice do Azure Search, você estará pronto para [carregar o conteúdo no índice](search-what-is-data-import.md) para que possa começar a pesquisar os dados.
