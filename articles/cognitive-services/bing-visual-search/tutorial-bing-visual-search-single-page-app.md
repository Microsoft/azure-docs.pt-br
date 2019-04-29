---
title: " Criar um aplicativo Web de página única - Pesquisa Visual do Bing"
titleSuffix: Azure Cognitive Services
description: Mostra como integrar a API da Pesquisa Visual do Bing em um aplicativo Web de página única.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: article
ms.date: 04/05/2019
ms.author: aahi
ms.openlocfilehash: 084aad5540a2bd56d98e343639a45c16f786e599
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60829592"
---
# <a name="create-a-visual-search-single-page-web-app"></a>Criar um aplicativo Web de página única da Pesquisa Visual

A API da Pesquisa Visual do Bing retorna informações para uma imagem. Você pode carregar uma imagem ou fornecer uma URL para uma. Informações são imagens visualmente semelhantes, fontes de compras, páginas da Web que incluem a imagem e muito mais. As informações retornadas pela API da Pesquisa Visual do Bing são similares às mostradas em Bing.com/images.

Este tutorial explica como estender um aplicativo web de página única para a API de pesquisa de imagem do Bing. Para exibir este tutorial ou obter o código-fonte usado aqui, consulte [Tutorial: Criar um aplicativo de página única para a API de Pesquisa de Imagem do Bing](../Bing-Image-Search/tutorial-bing-image-search-single-page-app.md).

O código-fonte completo para este aplicativo (após estendê-lo para usar a API de Pesquisa Visual do Bing), está disponível no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchApp.html).

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="call-the-bing-visual-search-api-and-handle-the-response"></a>Chamar a API de Pesquisa Visual do Bing e tratar da resposta

Edite o tutorial da Pesquisa de Imagem do Bing e adicione o código a seguir ao final do elemento `<script>` (e antes da tag de fechamento `</script>`). O código a seguir lida com uma resposta de pesquisa visual da API, itera através dos resultados e exibe-os:

``` javascript
function handleVisualSearchResponse(){
    if(this.status !== 200){
        console.log(this.responseText);
        return;
    }
    let json = this.responseText;
    let response = JSON.parse(json);
    for (let i =0; i < response.tags.length; i++) {
        let tag = response.tags[i];
        if (tag.displayName === '') {
            for (let j = 0; j < tag.actions.length; j++) {
                let action = tag.actions[j];
                if (action.actionType === 'VisualSearch') {
                    let html = '';
                    for (let k = 0; k < action.data.value.length; k++) {
                        let item = action.data.value[k];
                        let height = 120;
                        let width = Math.max(Math.round(height * item.thumbnail.width / item.thumbnail.height), 120);
                        html += "<img src='"+ item.thumbnailUrl + "&h=" + height + "&w=" + width + "' height=" + height + " width=" + width + "'>";
                    }
                    showDiv("insights", html);
                    window.scrollTo({top: document.getElementById("insights").getBoundingClientRect().top, behavior: "smooth"});
                }
            }
        }
    }
}
```

O código a seguir envia uma solicitação de pesquisa para a API, usando um ouvinte de eventos para chamar `handleVisualSearchResponse()`:

```javascript
function bingVisualSearch(insightsToken){
    let visualSearchBaseURL = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch',
        boundary = 'boundary_ABC123DEF456',
        startBoundary = '--' + boundary,
        endBoundary = '--' + boundary + '--',
        newLine = "\r\n",
        bodyHeader = 'Content-Disposition: form-data; name="knowledgeRequest"' + newLine + newLine;

    let postBody = {
        imageInfo: {
            imageInsightsToken: insightsToken
        }
    }
    let requestBody = startBoundary + newLine;
    requestBody += bodyHeader;
    requestBody += JSON.stringify(postBody) + newLine + newLine;
    requestBody += endBoundary + newLine;

    let request = new XMLHttpRequest();

    try {
        request.open("POST", visualSearchBaseURL);
    } 
    catch (e) {
        renderErrorMessage("Error performing visual search: " + e.message);
    }
    request.setRequestHeader("Ocp-Apim-Subscription-Key", getSubscriptionKey());
    request.setRequestHeader("Content-Type", "multipart/form-data; boundary=" + boundary);
    request.addEventListener("load", handleVisualSearchResponse);
    request.send(requestBody);
}
```

## <a name="capture-insights-token"></a>Capturar token de percepções

Adicione o seguinte código para o `searchItemsRenderer` objeto. Esse código adiciona um link **encontrar semelhantes** que chama a função `bingVisualSearch` quando clicado. A função recebe o `imageInsightsToken` como um argumento.

``` javascript
html.push("<a href='javascript:bingVisualSearch(\"" + item.imageInsightsToken + "\");'>find similar</a><br>");
```

## <a name="display-similar-images"></a>Exibir imagens semelhantes

Adicione o código HTML a seguir à linha 601. Esse código de marcação adiciona um elemento para exibir os resultados da chamada à API de pesquisa Visual do Bing:

``` html
<div id="insights">
    <h3><a href="#" onclick="return toggleDisplay('_insights')">Similar</a></h3>
    <div id="_insights" style="display: none"></div>
</div>
```

Com todos os elementos de código JavaScript e HTML em vigor, os resultados da pesquisa são exibidos com um link **encontrar semelhantes**. Clique no link para preencher a seção **Semelhantes** com imagens semelhantes à que você escolheu. Você pode precisar expandir a seção **Semelhante** para mostrar as imagens.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Tutorial: Recortar uma imagem com o SDK de pesquisa Visual do Bing paraC#](tutorial-visual-search-crop-area-results.md)
