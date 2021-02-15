---
title: Início rápido da biblioteca de clientes JavaScript da Pesquisa de Imagem do Bing
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/04/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: 037137cf5a6e4ddd66fc15e8ad9775ea77177ef6
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/13/2020
ms.locfileid: "94625414"
---
Use este início rápido para fazer sua primeira pesquisa de imagem usando a biblioteca de clientes da Pesquisa de Imagem do Bing, que é um wrapper para a API e contém os mesmos recursos. Esse aplicativo simples do JavaScript enviar uma consulta de pesquisa de imagem, analisa a resposta JSON e exibe a URL da primeira imagem devolvida.

O código-fonte para esse exemplo está disponível no [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/imageSearch.js) com anotações e tratamento de erro adicionais.

## <a name="prerequisites"></a>Pré-requisitos

* A versão mais recente do [Node.js](https://nodejs.org/en/download/).
* O [SDK a Pesquisa de Imagem do Bing para JavaScript](https://www.npmjs.com/package/@azure/cognitiveservices-imagesearch)
     *  Para instalar, execute `npm install @azure/cognitiveservices-imagesearch`
* A classe `CognitiveServicesCredentials` do pacote `@azure/ms-rest-azure-js` para autenticar o cliente.
     * Para instalar, execute `npm install @azure/ms-rest-azure-js`

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](~/includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Criar e inicializar o aplicativo

1. Criar um novo arquivo JavaScript no seu IDE ou editor favorito e defina o rigor, o https e outros requisitos.

    ```javascript
    'use strict';
    const ImageSearchAPIClient = require('@azure/cognitiveservices-imagesearch');
    const CognitiveServicesCredentials = require('@azure/ms-rest-azure-js').CognitiveServicesCredentials;
    ```

2. No método principal do seu projeto, crie variáveis para sua chave de assinatura válida, os resultados da imagem que serão devolvidos pelo Bing e um termo de pesquisa. Em seguida, instancie o cliente de pesquisa de imagem usando a chave.

    ```javascript
    //replace this value with your valid subscription key.
    let serviceKey = "ENTER YOUR KEY HERE";

    //the search term for the request
    let searchTerm = "canadian rockies";

    //instantiate the image search client
    let credentials = new CognitiveServicesCredentials(serviceKey);
    let imageSearchApiClient = new ImageSearchAPIClient(credentials);

    ```

## <a name="create-an-asynchronous-helper-function"></a>Criar uma função auxiliar assíncrona

1. Crie uma função para chamar o cliente de forma assíncrona e retornar a resposta do serviço de Pesquisa de Imagem do Bing.
    ```javascript
    //a helper function to perform an async call to the Bing Image Search API
    const sendQuery = async () => {
        return await imageSearchApiClient.imagesOperations.search(searchTerm);
    };
    ```
   ## <a name="send-a-query-and-handle-the-response"></a>Enviar uma consulta e lidar com a resposta

1. Chame a função auxiliar e lide com o `promise` para analisar os resultados da imagem devolvidos na resposta.

    Se a resposta contiver os resultados da pesquisa, armazene o primeiro resultado e imprima os detalhes, como URL da miniatura, a URL original, juntamente com o número total de imagens devolvidas.
    ```javascript
    sendQuery().then(imageResults => {
        if (imageResults == null) {
        console.log("No image results were found.");
        }
        else {
            console.log(`Total number of images returned: ${imageResults.value.length}`);
            let firstImageResult = imageResults.value[0];
            //display the details for the first image result. After running the application,
            //you can copy the resulting URLs from the console into your browser to view the image.
            console.log(`Total number of images found: ${imageResults.value.length}`);
            console.log(`Copy these URLs to view the first image returned:`);
            console.log(`First image thumbnail url: ${firstImageResult.thumbnailUrl}`);
            console.log(`First image content url: ${firstImageResult.contentUrl}`);
        }
      })
      .catch(err => console.error(err))
    ```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Tutorial de aplicativo de página única da Pesquisa de Imagem do Bing](../../tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>Confira também

* [O que é a Pesquisa de Imagem do Bing?](../../overview.md)
* [Experimente uma demonstração interativa online](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)
* [Amostras do Node.js para o SDK de Serviços Cognitivos do Azure](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)
* [Documentação dos Serviços Cognitivos do Azure](../../../index.yml)
* [Referência da API de Pesquisa de Imagem do Bing](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)