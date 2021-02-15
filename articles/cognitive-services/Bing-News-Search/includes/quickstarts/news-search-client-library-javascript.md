---
title: Guia de início rápido da biblioteca de clientes JavaScript da Pesquisa de Notícias do Bing
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/12/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: 3760213c7f469dfe89599c0f01afe98168efde2d
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2020
ms.locfileid: "91377213"
---
Use este guia de início rápido para começar a pesquisar notícias com a biblioteca de clientes da Pesquisa de Notícias do Bing para o JavaScript. Embora a Pesquisa de Notícias do Bing tenha uma API REST compatível com a maioria das linguagens de programação, a biblioteca de clientes é uma forma fácil de integrar o serviço aos seus aplicativos. O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/newsSearch.js).

## <a name="prerequisites"></a>Pré-requisitos

* A versão mais recente do [Node.js](https://nodejs.org/en/download/).
* O [SDK da Pesquisa de Notícias do Bing para JavaScript](https://www.npmjs.com/package/@azure/cognitiveservices-newssearch)
     *  Para instalar, execute `npm install @azure/cognitiveservices-newssearch`
* A classe `CognitiveServicesCredentials` do pacote `@azure/ms-rest-azure-js` para autenticar o cliente.
     * Para instalar, execute `npm install @azure/ms-rest-azure-js`

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](~/includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Criar e inicializar o aplicativo

1. Crie uma instância de `CognitiveServicesCredentials`. Crie variáveis para a chave da assinatura e um termo de pesquisa.

    ```javascript
    const CognitiveServicesCredentials = require('@azure/ms-rest-azure-js').CognitiveServicesCredentials;
    let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
    let search_term = 'Winter Olympics'
    ```

2. crie uma instância do cliente:
    
    ```javascript
    const NewsSearchAPIClient = require('@azure/cognitiveservices-newssearch');
    let client = new NewsSearchAPIClient(credentials);
    ```

## <a name="send-a-search-query"></a>Enviar uma consulta de pesquisa

1. Use o cliente para pesquisar com um texto de consulta, nesse caso, "Olimpíadas de Inverno":
    
    ```javascript
    client.newsOperations.search(search_term).then((result) => {
        console.log(result.value);
    }).catch((err) => {
        throw err;
    });
    ```

O código imprime `result.value` itens para o console sem analisar qualquer texto. Os resultados por categoria, se houver, incluirão:

- `_type: 'NewsArticle'`
- `_type: 'WebPage'`
- `_type: 'VideoObject'`
- `_type: 'ImageObject'`

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar um aplicativo Web de página única](../../tutorial-bing-news-search-single-page-app.md)
