---
title: Início rápido da biblioteca de clientes JavaScript da Pesquisa de Entidade do Bing
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/06/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: b145cc1689ad2c1a39591df0e39bb8d0445333c7
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91377476"
---
Use este início rápido para começar a pesquisar entidades com a biblioteca de clientes da Pesquisa de Entidade do Bing para o JavaScript. Embora a Pesquisa de Entidade do Bing tenha uma API REST compatível com a maioria das linguagens de programação, a biblioteca de clientes é uma forma fácil de integrar o serviço aos seus aplicativos. O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/entitySearch.js).

## <a name="prerequisites"></a>Pré-requisitos

* A versão mais recente do [Node.js](https://nodejs.org/en/download/).
* O [SDK da Pesquisa de Entidade do Bing para JavaScript](https://www.npmjs.com/package/@azure/cognitiveservices-entitysearch)
     *  Para instalar, execute `npm install @azure/cognitiveservices-entitysearch`
* A classe `CognitiveServicesCredentials` do pacote `@azure/ms-rest-azure-js` para autenticar o cliente.
     * Para instalar, execute `npm install @azure/ms-rest-azure-js`

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](~/includes/cognitive-services-bing-entity-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Criar e inicializar o aplicativo

1. Crie um novo arquivo JavaScript em seu IDE ou editor favorito e adicione os seguintes requisitos.

    ```javascript
    const CognitiveServicesCredentials = require('@azure/ms-rest-azure-js').CognitiveServicesCredentials;
    const EntitySearchAPIClient = require('@azure/cognitiveservices-entitysearch');
    ```

2. Crie uma instância de `CognitiveServicesCredentials` usando a chave de assinatura. Em seguida, crie uma instância do cliente de pesquisa com ela.

    ```javascript
    let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
    let entitySearchApiClient = new EntitySearchAPIClient(credentials);
    ```

## <a name="send-a-request-and-receive-a-response"></a>Enviar uma solicitação e receber uma resposta

1. Envie uma solicitação de pesquisa de entidades com `entitiesOperations.search()`. Depois de receber uma resposta, imprima o `queryContext`, o número de resultados retornados e a descrição do primeiro resultado.

    ```javascript
    entitySearchApiClient.entitiesOperations.search('seahawks').then((result) => {
        console.log(result.queryContext);
        console.log(result.entities.value);
        console.log(result.entities.value[0].description);
    }).catch((err) => {
        throw err;
    });
    ```

<!-- Removing until we can replace with a sanitized version.
![Entity results](media/entity-search-sdk-node-quickstart-results.png)
-->

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar um aplicativo Web de página única](../../tutorial-bing-entities-search-single-page-app.md)

* [O que é a API de Pesquisa de Entidade do Bing?](../../overview.md)