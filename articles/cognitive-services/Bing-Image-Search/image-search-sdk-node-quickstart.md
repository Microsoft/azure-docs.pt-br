---
title: 'Início Rápido: Pesquisar imagens – SDK da Pesquisa de Imagem do Bing para Node.js'
titleSuffix: Azure Cognitive Services
description: Use este início rápido para fazer sua primeira pesquisa de imagem usando o SDK de Pesquisa de Imagem do Bing, que é um wrapper para a API e contém os mesmos recursos. Esse aplicativo simples do Node.js enviar uma consulta de pesquisa de imagem, analisa a resposta JSON e exibe a URL da primeira imagem devolvida.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: quickstart
ms.date: 03/04/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 24f6de759f233b82cb19a21401d3886a80bd746f
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68423898"
---
# <a name="quickstart-search-for-images-with-the-bing-image-search-sdk-for-nodejs"></a>Início Rápido: Pesquisar imagens com o SDK da Pesquisa de Imagem do Bing para Node.js

Use este início rápido para fazer sua primeira pesquisa de imagem usando o SDK de Pesquisa de Imagem do Bing, que é um wrapper para a API e contém os mesmos recursos. Esse aplicativo simples do JavaScript enviar uma consulta de pesquisa de imagem, analisa a resposta JSON e exibe a URL da primeira imagem devolvida.

O código-fonte para esse exemplo está disponível no [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/imageSearch.js) com anotações e tratamento de erro adicionais.

## <a name="prerequisites"></a>Pré-requisitos
Obtenha uma [chave de acesso de Serviços Cognitivos](https://azure.microsoft.com/try/cognitive-services/) em **Pesquisar**.  Veja também [Cognitive Services Pricing - API de Pesquisa do Bing](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).  Veja também [Cognitive Services Pricing - API de Pesquisa do Bing](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

* O [SDK de Pesquisa de Imagem de Serviços Cognitivos para Node.js](https://www.npmjs.com/package/azure-cognitiveservices-imagesearch)
    * Instale usando `npm install azure-cognitiveservices-imagesearch`
* O módulo [Node.js Azure Rest](https://www.npmjs.com/package/ms-rest-azure)
    * Instale usando `npm install ms-rest-azure`

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Criar e inicializar o aplicativo

1. Criar um novo arquivo JavaScript no seu IDE ou editor favorito e defina o rigor, o https e outros requisitos.

    ```javascript
    'use strict';
    const ImageSearchAPIClient = require('azure-cognitiveservices-imagesearch');
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
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
> [Tutorial de aplicativo de página única da Pesquisa de Imagem do Bing](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app)

## <a name="see-also"></a>Consulte também

* [O que é a Pesquisa de Imagem do Bing?](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [Experimente uma demonstração interativa online](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Obtenha uma chave de acesso de Serviços Cognitivos grátis](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)
* [Amostras do Node.js para o SDK de Serviços Cognitivos do Azure](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)
* [Documentação dos Serviços Cognitivos do Azure](https://docs.microsoft.com/azure/cognitive-services)
* [Referência da API de Pesquisa de Imagem do Bing](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)
