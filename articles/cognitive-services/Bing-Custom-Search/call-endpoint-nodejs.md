---
title: 'Início Rápido: Chamar o ponto de extremidade da Pesquisa Personalizada do Bing usando o Node.js | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Use este início rápido para começar a solicitar os resultados da pesquisa na instância da Pesquisa Personalizada do Bing, usando o Node.js
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 07/26/2019
ms.author: aahi
ms.openlocfilehash: e95b9aa8f01142065202e029c6298fc4c4cb1294
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68565742"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-nodejs"></a>Início Rápido: Chamar o ponto de extremidade da Pesquisa Personalizada do Bing usando o Node.js

Use este início rápido para começar a solicitar os resultados da pesquisa na instância da Pesquisa Personalizada do Bing. Embora esse aplicativo seja escrito em JavaScript, a API de Pesquisa Personalizada do Bing é um serviço Web RESTful compatível com a maioria das linguagens de programação. O código-fonte deste exemplo está disponível no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingCustomSearchv7.js).

## <a name="prerequisites"></a>Pré-requisitos

- Uma instância da Pesquisa Personalizada do Bing. Confira [Início Rápido: Criar sua primeira instância da Pesquisa Personalizada do Bing](quick-start.md) para obter mais informações.

- [Node.js](https://www.nodejs.org/)

- A [biblioteca de solicitações JavaScript](https://github.com/request/request)

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Criar e inicializar o aplicativo

1. Crie um arquivo JavaScript em seu IDE ou editor favorito e adicione uma instrução `require()` à biblioteca de solicitações. Crie variáveis para sua chave de assinatura, uma ID de Configuração Personalizada e um termo de pesquisa. 

    ```javascript
    var request = require("request");
    
    var subscriptionKey = 'YOUR-SUBSCRIPTION-KEY';
    var customConfigId = 'YOUR-CUSTOM-CONFIG-ID';
    var searchTerm = 'microsoft';
    ```

## <a name="send-and-receive-a-search-request"></a>Enviar e receber uma solicitação de pesquisa 

1. Crie uma variável para armazenar as informações que estão sendo enviadas na solicitação. Construa a URL de solicitação acrescentando o termo de pesquisa ao parâmetro de consulta `q=` e a ID de Configuração Personalizada da instância de pesquisa a `customconfig=`. separe os parâmetros com um caractere `&`. 

    ```javascript
    var info = {
        url: 'https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?' + 
            'q=' + searchTerm + "&" +
            'customconfig=' + customConfigId,
        headers: {
            'Ocp-Apim-Subscription-Key' : subscriptionKey
        }
    }
    ```

1. Use a Biblioteca de Solicitações JavaScript para enviar uma solicitação de pesquisa à instância da Pesquisa Personalizada do Bing e imprimir informações sobre os resultados, incluindo o nome, a URL e a data em que a página da Web foi rastreada pela última vez.

    ```javascript
    request(info, function(error, response, body){
            var searchResponse = JSON.parse(body);
            for(var i = 0; i < searchResponse.webPages.value.length; ++i){
                var webPage = searchResponse.webPages.value[i];
                console.log('name: ' + webPage.name);
                console.log('url: ' + webPage.url);
                console.log('displayUrl: ' + webPage.displayUrl);
                console.log('snippet: ' + webPage.snippet);
                console.log('dateLastCrawled: ' + webPage.dateLastCrawled);
                console.log();
            }
    ```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar um aplicativo Web de Pesquisa Personalizada](./tutorials/custom-search-web-page.md)
