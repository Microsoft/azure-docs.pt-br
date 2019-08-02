---
title: Executar operações de imagem – JavaScript
titleSuffix: Azure Cognitive Services
description: Explore um aplicativo JavaScript básico que usa a API da Pesquisa Visual Computacional nos Serviços Cognitivos do Azure. Execute o OCR, crie miniaturas e trabalhe com recursos visuais em uma imagem.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/30/2019
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: 24ef94b702d11977df4e1ca2dab181f5c14a00df
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68564566"
---
# <a name="use-computer-vision-features-with-the-rest-api-and-javascript"></a>Usar recursos de Pesquisa Visual Computacional com a API REST e o JavaScript

Este guia mostra os recursos dos serviços cognitivas do Azure Pesquisa Visual Computacional API REST.

Explore um aplicativo JavaScript que usa a API da Pesquisa Visual Computacional para executar o OCR (reconhecimento óptico de caracteres), criar miniaturas com recorte inteligente, além de detectar, categorizar, marcar e descrever recursos visuais, incluindo rostos, em uma imagem. Este exemplo permite enviar uma URL de imagem para análise ou processamento. É possível usar esse exemplo de software livre como um modelo para compilar seu próprio aplicativo em JavaScript para usar a API REST da Pesquisa Visual Computacional.

O aplicativo de formulário JavaScript já foi gravado, mas não possui funcionalidade de Pesquisa Visual Computacional. Neste guia, você adiciona o código específico à API REST do Pesquisa Visual Computacional para concluir a funcionalidade do aplicativo.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="platform-requirements"></a>Requisitos de plataforma

Você pode seguir as etapas neste guia usando um editor de texto simples.

### <a name="subscribe-to-computer-vision-api-and-get-a-subscription-key"></a>Inscrever-se na API da Pesquisa Visual Computacional e obter uma chave de assinatura

Antes de criar o exemplo, é necessário assinar a API da Pesquisa Visual Computacional, que faz parte dos Serviços Cognitivos do Azure. Para detalhes de assinatura e gerenciamento de chaves, consulte [Assinaturas](https://azure.microsoft.com/try/cognitive-services/). As chaves primária e secundária são válidas para uso neste guia.

## <a name="acquire-incomplete-tutorial-project"></a>Adquirir projeto de tutorial incompleto

### <a name="download-the-project"></a>Baixe o projeto

Clone o [Tutorial da Pesquisa Visual Computacional JavaScript dos Serviços Cognitivos](https://github.com/Azure-Samples/cognitive-services-javascript-computer-vision-tutorial) ou baixe o arquivo .zip e extraia-o em um diretório vazio.

Se preferir usar o projeto concluído com todo o código de tutorial adicionado, você poderá usar os arquivos na pasta **concluído** .

## <a name="add-tutorial-code-to-the-project"></a>Adicionar código de tutorial ao projeto

O aplicativo JavaScript é configurado com seis arquivos .html, um para cada recurso. Cada arquivo demonstra uma função diferente do Pesquisa Visual Computacional (analisar, OCR, etc.). As seis seções não têm interdependências, portanto, você pode adicionar o código do tutorial a um arquivo, a todos os seis arquivos ou apenas a alguns arquivos. E você pode adicionar o código de tutorial aos arquivos em qualquer ordem.

### <a name="analyze-an-image"></a>Analisar uma imagem

O recurso analisar do Pesquisa Visual Computacional examina uma imagem em busca de milhares de objetos reconhecíveis, coisas em ação, cenários e ações. Quando a análise estiver concluída, o recurso Analisar retornará um objeto JSON que descreve a imagem com marcas descritivas, análise de cores, legendas e muito mais.

Para concluir o recurso analisar do aplicativo, execute as seguintes etapas:

#### <a name="add-the-event-handler-code-for-the-analyze-button"></a>Adicionar o código do manipulador de eventos para o botão analisar

Abra o arquivo **analyze.html** em um editor de texto e localize a função **analyzeButtonClick** na parte inferior do arquivo.

A função do manipulador de eventos **analyzeButtonClick** limpa o formulário, exibe a imagem especificada na URL e, em seguida, chama a função **AnalyzeImage** para analisar a imagem. Copie e cole o código a seguir na função **analyzeButtonClick**.

```javascript
function analyzeButtonClick() {

    // Clear the display fields.
    $("#sourceImage").attr("src", "#");
    $("#responseTextArea").val("");
    $("#captionSpan").text("");
    
    // Display the image.
    var sourceImageUrl = $("#inputImage").val();
    $("#sourceImage").attr("src", sourceImageUrl);
    
    AnalyzeImage(sourceImageUrl, $("#responseTextArea"), $("#captionSpan"));
}
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>Adicionar o wrapper para a chamada à API REST

A função **AnalyzeImage** encapsula a chamada da API REST para analisar uma imagem. Após um retorno com êxito, a análise JSON formatada será exibida na área de texto especificada e a legenda será exibida na extensão especificada.

Copie e cole o código de função **AnalyzeImage** imediatamente abaixo da função **analyzeButtonClick**.

```javascript
/* Analyze the image at the specified URL by using Microsoft Cognitive Services Analyze Image API.
 * @param {string} sourceImageUrl - The URL to the image to analyze.
 * @param {<textarea> element} responseTextArea - The text area to display the JSON string returned
 *                             from the REST API call, or to display the error message if there was 
 *                             an error.
 * @param {<span> element} captionSpan - The span to display the image caption.
 */
function AnalyzeImage(sourceImageUrl, responseTextArea, captionSpan) {
    // Request parameters.
    var params = {
        "visualFeatures": "Categories,Description,Color",
        "details": "",
        "language": "en",
    };
    
    // Perform the REST API call.
    $.ajax({
        url: common.uriBasePreRegion + 
             $("#subscriptionRegionSelect").val() + 
             common.uriBasePostRegion + 
             common.uriBaseAnalyze +
             "?" + 
             $.param(params),
                    
        // Request headers.
        beforeSend: function(jqXHR){
            jqXHR.setRequestHeader("Content-Type","application/json");
            jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key", 
                encodeURIComponent($("#subscriptionKeyInput").val()));
        },
        
        type: "POST",
        
        // Request body.
        data: '{"url": ' + '"' + sourceImageUrl + '"}',
    })
    
    .done(function(data) {
        // Show formatted JSON on webpage.
        responseTextArea.val(JSON.stringify(data, null, 2));
        
        // Extract and display the caption and confidence from the first caption in the description object.
        if (data.description && data.description.captions) {
            var caption = data.description.captions[0];
            
            if (caption.text && caption.confidence) {
                captionSpan.text("Caption: " + caption.text +
                    " (confidence: " + caption.confidence + ").");
            }
        }
    })
    
    .fail(function(jqXHR, textStatus, errorThrown) {
        // Prepare the error string.
        var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
        errorString += (jqXHR.responseText === "") ? "" : (jQuery.parseJSON(jqXHR.responseText).message) ? 
            jQuery.parseJSON(jqXHR.responseText).message : jQuery.parseJSON(jqXHR.responseText).error.message;
        
        // Put the error JSON in the response textarea.
        responseTextArea.val(JSON.stringify(jqXHR, null, 2));
        
        // Show the error message.
        alert(errorString);
    });
}
```

#### <a name="run-the-analyze-function"></a>Executar a função Analyze

Salve o arquivo **analyze.html** e abra-o em um navegador da Web. Coloque a chave de assinatura no campo **Chave de assinatura** campo e verifique se você está usando a região correta em **Região de Assinatura**. Insira uma URL de uma imagem para analisar e clique no botão**Analisar Imagem** para analisar uma imagem e ver o resultado.

### <a name="recognize-a-landmark"></a>Reconhecer um ponto de referência

O recurso Ponto de Referência da Pesquisa Visual Computacional analisa uma imagem para pontos de referência naturais e artificiais, como montanhas ou edifícios famosos. Quando a análise estiver concluída, o Ponto de Referência retornará um objeto JSON que identifica os pontos de referência localizados na imagem.

Para concluir o recurso de ponto de referência do aplicativo, execute as seguintes etapas:

#### <a name="add-the-event-handler-code-for-the-landmark-button"></a>Adicionar o código do manipulador de eventos ao botão de referência

Abra o arquivo **landmark.html** em um editor de texto e localize a função **landmarkButtonClick** na parte inferior do arquivo.

A função do manipulador de eventos **landmarkButtonClick** limpa o formulário, exibe a imagem especificada na URL e, em seguida, chama a função **IdentifyLandmarks** para analisar a imagem. Copie e cole o código a seguir na função **landmarkButtonClick**.

```javascript
function landmarkButtonClick() {

    // Clear the display fields.
    $("#sourceImage").attr("src", "#");
    $("#responseTextArea").val("");
    $("#captionSpan").text("");
    
    // Display the image.
    var sourceImageUrl = $("#inputImage").val();
    $("#sourceImage").attr("src", sourceImageUrl);
    
    IdentifyLandmarks(sourceImageUrl, $("#responseTextArea"), $("#captionSpan"));
}
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>Adicionar o wrapper para a chamada à API REST

A função **IdentifyLandmarks** encapsula a chamada da API REST para analisar uma imagem. Após um retorno com êxito, a análise JSON formatada será exibida na área de texto especificada e a legenda será exibida na extensão especificada.

Copie e cole o código de função **IdentifyLandmarks** imediatamente abaixo da função **landmarkButtonClick**.

```javascript
/* Identify landmarks in the image at the specified URL by using Microsoft Cognitive Services 
 * Landmarks API.
 * @param {string} sourceImageUrl - The URL to the image to analyze for landmarks.
 * @param {<textarea> element} responseTextArea - The text area to display the JSON string returned
 *                             from the REST API call, or to display the error message if there was 
 *                             an error.
 * @param {<span> element} captionSpan - The span to display the image caption.
 */
function IdentifyLandmarks(sourceImageUrl, responseTextArea, captionSpan) {
    // Request parameters.
    var params = {
        "model": "landmarks"
    };
    
    // Perform the REST API call.
    $.ajax({
        url: common.uriBasePreRegion + 
             $("#subscriptionRegionSelect").val() + 
             common.uriBasePostRegion + 
             common.uriBaseLandmark +
             "?" + 
             $.param(params),
                    
        // Request headers.
        beforeSend: function(jqXHR){
            jqXHR.setRequestHeader("Content-Type","application/json");
            jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key", 
                encodeURIComponent($("#subscriptionKeyInput").val()));
        },
        
        type: "POST",
        
        // Request body.
        data: '{"url": ' + '"' + sourceImageUrl + '"}',
    })
    
    .done(function(data) {
        // Show formatted JSON on webpage.
        responseTextArea.val(JSON.stringify(data, null, 2));
        
        // Extract and display the caption and confidence from the first caption in the description object.
        if (data.result && data.result.landmarks) {
            var landmark = data.result.landmarks[0];
            
            if (landmark.name && landmark.confidence) {
                captionSpan.text("Landmark: " + landmark.name +
                    " (confidence: " + landmark.confidence + ").");
            }
        }
    })
    
    .fail(function(jqXHR, textStatus, errorThrown) {
        // Prepare the error string.
        var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
        errorString += (jqXHR.responseText === "") ? "" : (jQuery.parseJSON(jqXHR.responseText).message) ? 
            jQuery.parseJSON(jqXHR.responseText).message : jQuery.parseJSON(jqXHR.responseText).error.message;
        
        // Put the error JSON in the response textarea.
        responseTextArea.val(JSON.stringify(jqXHR, null, 2));
        
        // Show the error message.
        alert(errorString);
    });
}
```

#### <a name="run-the-landmark-function"></a>Executar a função de ponto de referência

Salve o arquivo **landmark.html** e abra-o em um navegador da Web. Coloque a chave de assinatura no campo **Chave de assinatura** campo e verifique se você está usando a região correta em **Região de Assinatura**. Insira uma URL de uma imagem para analisar e clique no botão**Analisar Imagem** para analisar uma imagem e ver o resultado.

### <a name="recognize-celebrities"></a>Reconhecer celebridades

O recurso Celebridades da Pesquisa Visual Computacional analisa uma imagem de pessoas famosas. Quando a análise estiver concluída, o recurso Celebridades retornará um objeto JSON que identifica as Celebridades localizadas na imagem.

Para concluir o recurso celebridades do aplicativo, execute as seguintes etapas:

#### <a name="add-the-event-handler-code-for-the-celebrities-button"></a>Adicionar o código do manipulador de eventos para o botão celebridades

Abra o arquivo **celebrities.html** em um editor de texto e localize a função **celebritiesButtonClick** na parte inferior do arquivo.

A função do manipulador de eventos **celebritiesButtonClick** limpa o formulário, exibe a imagem especificada na URL e, em seguida, chama a função **IdentifyCelebrities** para analisar a imagem. Copie e cole o código a seguir na função **celebritiesButtonClick**.

```javascript
function celebritiesButtonClick() {

    // Clear the display fields.
    $("#sourceImage").attr("src", "#");
    $("#responseTextArea").val("");
    $("#captionSpan").text("");
    
    // Display the image.
    var sourceImageUrl = $("#inputImage").val();
    $("#sourceImage").attr("src", sourceImageUrl);
    
    IdentifyCelebrities(sourceImageUrl, $("#responseTextArea"), $("#captionSpan"));
}
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>Adicionar o wrapper para a chamada à API REST

```javascript
/* Identify celebrities in the image at the specified URL by using Microsoft Cognitive Services 
 * Celebrities API.
 * @param {string} sourceImageUrl - The URL to the image to analyze for celebrities.
 * @param {<textarea> element} responseTextArea - The text area to display the JSON string returned
 *                             from the REST API call, or to display the error message if there was 
 *                             an error.
 * @param {<span> element} captionSpan - The span to display the image caption.
 */
function IdentifyCelebrities(sourceImageUrl, responseTextArea, captionSpan) {
    // Request parameters.
    var params = {
        "model": "celebrities"
    };
    
    // Perform the REST API call.
    $.ajax({
        url: common.uriBasePreRegion + 
             $("#subscriptionRegionSelect").val() + 
             common.uriBasePostRegion + 
             common.uriBaseCelebrities +
             "?" + 
             $.param(params),
                    
        // Request headers.
        beforeSend: function(jqXHR){
            jqXHR.setRequestHeader("Content-Type","application/json");
            jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key", 
                encodeURIComponent($("#subscriptionKeyInput").val()));
        },
        
        type: "POST",
        
        // Request body.
        data: '{"url": ' + '"' + sourceImageUrl + '"}',
    })
    
    .done(function(data) {
        // Show formatted JSON on webpage.
        responseTextArea.val(JSON.stringify(data, null, 2));
        
        // Extract and display the caption and confidence from the first caption in the description object.
        if (data.result && data.result.celebrities) {
            var celebrity = data.result.celebrities[0];
            
            if (celebrity.name && celebrity.confidence) {
                captionSpan.text("Celebrity name: " + celebrity.name +
                    " (confidence: " + celebrity.confidence + ").");
            }
        }
    })
    
    .fail(function(jqXHR, textStatus, errorThrown) {
        // Prepare the error string.
        var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
        errorString += (jqXHR.responseText === "") ? "" : (jQuery.parseJSON(jqXHR.responseText).message) ? 
            jQuery.parseJSON(jqXHR.responseText).message : jQuery.parseJSON(jqXHR.responseText).error.message;
        
        // Put the error JSON in the response textarea.
        responseTextArea.val(JSON.stringify(jqXHR, null, 2));
        
        // Show the error message.
        alert(errorString);
    });
}
```

#### <a name="run-the-celebrities-function"></a>Executar a função celebridades

Salve o arquivo **celebrities.html** e abra-o em um navegador da Web. Coloque a chave de assinatura no campo **Chave de assinatura** campo e verifique se você está usando a região correta em **Região de Assinatura**. Insira uma URL de uma imagem para analisar e clique no botão**Analisar Imagem** para analisar uma imagem e ver o resultado.

### <a name="intelligently-generate-a-thumbnail"></a>Gerar uma miniatura de maneira inteligente

O recurso Miniatura da Pesquisa Visual Computacional gera uma miniatura de uma imagem. Ao usar o recurso **Recorte Inteligente** o recurso Miniatura identificará a área de interesse em uma imagem e centralizará a miniatura nessa área, para gerar mais imagens em miniatura esteticamente agradáveis.

Para concluir o recurso de miniatura do aplicativo, execute as seguintes etapas:

#### <a name="add-the-event-handler-code-for-the-thumbnail-button"></a>Adicionar o código do manipulador de eventos para o botão de miniatura

Abra o arquivo **thumbnail.html** em um editor de texto e localize a função **thumbnailButtonClick** na parte inferior do arquivo.

A função do manipulador de eventos **thumbnailButtonClick** limpa o formulário, exibe a imagem especificada na URL e, em seguida, chama a função **getThumbnail** duas vezes para criar duas miniaturas, uma com recorte inteligente e outra sem recorte inteligente. Copie e cole o código a seguir na função **thumbnailButtonClick**.

```javascript
function thumbnailButtonClick() {

    // Clear the display fields.
    document.getElementById("sourceImage").src = "#";
    document.getElementById("thumbnailImageSmartCrop").src = "#";
    document.getElementById("thumbnailImageNonSmartCrop").src = "#";
    document.getElementById("responseTextArea").value = "";
    document.getElementById("captionSpan").text = "";
    
    // Display the image.
    var sourceImageUrl = document.getElementById("inputImage").value;
    document.getElementById("sourceImage").src = sourceImageUrl;
    
    // Get a smart cropped thumbnail.
    getThumbnail (sourceImageUrl, true, document.getElementById("thumbnailImageSmartCrop"), 
        document.getElementById("responseTextArea"));
    
    // Get a non-smart-cropped thumbnail.
    getThumbnail (sourceImageUrl, false, document.getElementById("thumbnailImageNonSmartCrop"),
        document.getElementById("responseTextArea"));
}
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>Adicionar o wrapper para a chamada à API REST

A função **getThumbnail** encapsula a chamada da API REST para analisar uma imagem. Após um retorno com êxito, a miniatura será exibida no elemento img especificado.

Copie e cole a seguinte função **getThumbnail** imediatamente abaixo da função **thumbnailButtonClick**.

```javascript
/* Get a thumbnail of the image at the specified URL by using Microsoft Cognitive Services
 * Thumbnail API.
 * @param {string} sourceImageUrl URL to image.
 * @param {boolean} smartCropping Set to true to use the smart cropping feature which crops to the
 *                                more interesting area of an image; false to crop for the center
 *                                of the image.
 * @param {<img> element} imageElement The img element in the DOM which will display the thumbnail image.
 * @param {<textarea> element} responseTextArea - The text area to display the Response Headers returned
 *                             from the REST API call, or to display the error message if there was 
 *                             an error.
 */
function getThumbnail (sourceImageUrl, smartCropping, imageElement, responseTextArea) {
    // Create the HTTP Request object.
    var xhr = new XMLHttpRequest();
    
    // Request parameters.
    var params = "width=100&height=150&smartCropping=" + smartCropping.toString();

    // Build the full URI.
    var fullUri = common.uriBasePreRegion + 
                  document.getElementById("subscriptionRegionSelect").value + 
                  common.uriBasePostRegion + 
                  common.uriBaseThumbnail +
                  "?" + 
                  params;
    
    // Identify the request as a POST, with the URI and parameters.
    xhr.open("POST", fullUri);
    
    // Add the request headers.
    xhr.setRequestHeader("Content-Type","application/json");
    xhr.setRequestHeader("Ocp-Apim-Subscription-Key", 
        encodeURIComponent(document.getElementById("subscriptionKeyInput").value));
    
    // Set the response type to "blob" for the thumbnail image data.
    xhr.responseType = "blob";
    
    // Process the result of the REST API call.
    xhr.onreadystatechange = function(e) {
        if(xhr.readyState === XMLHttpRequest.DONE) {
            
            // Thumbnail successfully created.
            if (xhr.status === 200) {
                // Show response headers.
                var s = JSON.stringify(xhr.getAllResponseHeaders(), null, 2);
                responseTextArea.value = JSON.stringify(xhr.getAllResponseHeaders(), null, 2);
                
                // Show thumbnail image.
                var urlCreator = window.URL || window.webkitURL;
                var imageUrl = urlCreator.createObjectURL(this.response);
                imageElement.src = imageUrl;
            } else {
                // Display the error message. The error message is the response body as a JSON string. 
                // The code in this code block extracts the JSON string from the blob response.
                var reader = new FileReader();
                
                // This event fires after the blob has been read.
                reader.addEventListener('loadend', (e) => {
                    responseTextArea.value = JSON.stringify(JSON.parse(e.srcElement.result), null, 2);
                });
                
                // Start reading the blob as text.
                reader.readAsText(xhr.response);
            }
        }
    }
    
    // Execute the REST API call.
    xhr.send('{"url": ' + '"' + sourceImageUrl + '"}');
}
```

#### <a name="run-the-thumbnail-function"></a>Executar a função de miniatura

Salve o arquivo **thumbnail.html** e abra-o em um navegador da Web. Coloque a chave de assinatura no campo **Chave de assinatura** campo e verifique se você está usando a região correta em **Região de Assinatura**. Insira uma URL de uma imagem para analisar e clique no botão**Gerar Miniaturas** para analisar uma imagem e ver o resultado.

### <a name="read-printed-text-ocr"></a>Ler texto impresso (OCR)

O recurso OCR (Reconhecimento Óptico de Caracteres) da Pesquisa Visual Computacional analisa uma imagem do texto impresso. Após a conclusão da análise, o OCR retornará um objeto JSON que contém o texto e a localização do texto na imagem.

Para concluir o recurso de OCR do aplicativo, execute as seguintes etapas:

### <a name="add-the-event-handler-code-for-the-ocr-button"></a>Adicionar o código do manipulador de eventos ao botão OCR

Abra o arquivo **ocr.html** em um editor de texto e localize a função **ocrButtonClick** na parte inferior do arquivo.

A função do manipulador de eventos **ocrButtonClick** limpa o formulário, exibe a imagem especificada na URL e, em seguida, chama a função **ReadOcrImage** para analisar a imagem. Copie e cole o código a seguir na função **ocrButtonClick**.

```javascript
function ocrButtonClick() {

    // Clear the display fields.
    $("#sourceImage").attr("src", "#");
    $("#responseTextArea").val("");
    $("#captionSpan").text("");
    
    // Display the image.
    var sourceImageUrl = $("#inputImage").val();
    $("#sourceImage").attr("src", sourceImageUrl);
    
    ReadOcrImage(sourceImageUrl, $("#responseTextArea"));
}
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>Adicionar o wrapper para a chamada à API REST

A função **ReadOcrImage** encapsula a chamada da API REST para analisar uma imagem. Após um retorno com êxito, o JSON formatado descrevendo o texto e a localização do texto será exibido na área de texto especificada.

Copie e cole a seguinte função **ReadOcrImage** imediatamente abaixo da função **ocrButtonClick**.

```javascript
/* Recognize and read printed text in an image at the specified URL by using Microsoft Cognitive 
 * Services OCR API.
 * @param {string} sourceImageUrl - The URL to the image to analyze for printed text.
 * @param {<textarea> element} responseTextArea - The text area to display the JSON string returned
 *                             from the REST API call, or to display the error message if there was 
 *                             an error.
 */
function ReadOcrImage(sourceImageUrl, responseTextArea) {
    // Request parameters.
    var params = {
        "language": "unk",
        "detectOrientation ": "true",
    };

    // Perform the REST API call.
    $.ajax({
        url: common.uriBasePreRegion + 
             $("#subscriptionRegionSelect").val() + 
             common.uriBasePostRegion + 
             common.uriBaseOcr +
             "?" + 
             $.param(params),
        
        // Request headers.
        beforeSend: function(jqXHR){
            jqXHR.setRequestHeader("Content-Type","application/json");
            jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key", 
                encodeURIComponent($("#subscriptionKeyInput").val()));
        },
        
        type: "POST",
        
        // Request body.
        data: '{"url": ' + '"' + sourceImageUrl + '"}',
    })
    
    .done(function(data) {
        // Show formatted JSON on webpage.
        responseTextArea.val(JSON.stringify(data, null, 2));
    })
    
    .fail(function(jqXHR, textStatus, errorThrown) {
        // Put the JSON description into the text area.
        responseTextArea.val(JSON.stringify(jqXHR, null, 2));
        
        // Display error message.
        var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
        errorString += (jqXHR.responseText === "") ? "" : (jQuery.parseJSON(jqXHR.responseText).message) ? 
            jQuery.parseJSON(jqXHR.responseText).message : jQuery.parseJSON(jqXHR.responseText).error.message;
        alert(errorString);
    });
}
```

#### <a name="run-the-ocr-function"></a>Executar a função OCR

Salve o arquivo **ocr.html** e abra-o em um navegador da Web. Coloque a chave de assinatura no campo **Chave de assinatura** campo e verifique se você está usando a região correta em **Região de Assinatura**. Insira uma URL de uma imagem de texto para ler e, em seguida, clique no botão **Ler Imagem** para analisar uma imagem e ver o resultado.

### <a name="read-handwritten-text-handwriting-recognition"></a>Ler texto manuscrito (reconhecimento de manuscrito)

O recurso Reconhecimento de Manuscrito da Pesquisa Visual Computacional analisa uma imagem de texto manuscrito. Após a conclusão da análise, o Reconhecimento de Manuscrito retornará um objeto JSON que contém o texto e a localização do texto na imagem.

Para concluir o recurso de reconhecimento de manuscrito do aplicativo, execute as seguintes etapas:

#### <a name="add-the-event-handler-code-for-the-handwriting-button"></a>Adicione o código do manipulador de eventos ao botão de manuscrito

Abra o arquivo **handwriting.html** em um editor de texto e localize a função **handwritingButtonClick** na parte inferior do arquivo.

A função do manipulador de eventos **handwritingButtonClick** limpa o formulário, exibe a imagem especificada na URL e, em seguida, chama a função **HandwritingImage** para analisar a imagem.

Copie e cole o código a seguir na função **handwritingButtonClick**.

```javascript
function handwritingButtonClick() {

    // Clear the display fields.
    $("#sourceImage").attr("src", "#");
    $("#responseTextArea").val("");
    
    // Display the image.
    var sourceImageUrl = $("#inputImage").val();
    $("#sourceImage").attr("src", sourceImageUrl);
    
    ReadHandwrittenImage(sourceImageUrl, $("#responseTextArea"));
}
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>Adicionar o wrapper para a chamada à API REST

A função **ReadHandwrittenImage** encapsula as duas chamadas de API REST necessárias para analisar uma imagem. Como o Reconhecimento de Manuscrito é um processo lento, é utilizado um processo de duas etapas. A primeira chamada envia a imagem para processamento e a segunda chamada recupera o texto detectado quando o processamento é concluído.

Depois que o texto for recuperado, o JSON formatado que descreve o texto e a localização do texto será exibido na área de texto especificada.

Copie e cole a seguinte função **ReadHandwrittenImage** imediatamente abaixo da função **handwritingButtonClick**.

```javascript
/* Recognize and read text from an image of handwriting at the specified URL by using Microsoft 
 * Cognitive Services Recognize Handwritten Text API.
 * @param {string} sourceImageUrl - The URL to the image to analyze for handwriting.
 * @param {<textarea> element} responseTextArea - The text area to display the JSON string returned
 *                             from the REST API call, or to display the error message if there was 
 *                             an error.
 */
function ReadHandwrittenImage(sourceImageUrl, responseTextArea) {
    // Request parameters.
    var params = {
        "handwriting": "true",
    };

    // This operation requires two REST API calls. One to submit the image for processing,
    // the other to retrieve the text found in the image. 
    //
    // Perform the first REST API call to submit the image for processing.
    $.ajax({
        url: common.uriBasePreRegion + 
             $("#subscriptionRegionSelect").val() + 
             common.uriBasePostRegion + 
             common.uriBaseHandwriting +
             "?" + 
             $.param(params),
        
        // Request headers.
        beforeSend: function(jqXHR){
            jqXHR.setRequestHeader("Content-Type","application/json");
            jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key", 
                encodeURIComponent($("#subscriptionKeyInput").val()));
        },
        
        type: "POST",
        
        // Request body.
        data: '{"url": ' + '"' + sourceImageUrl + '"}',
    })
    
    .done(function(data, textStatus, jqXHR) {
        // Show progress.
        responseTextArea.val("Handwritten image submitted.");
        
        // Note: The response may not be immediately available. Handwriting Recognition is an
        // async operation that can take a variable amount of time depending on the length
        // of the text you want to recognize. You may need to wait or retry this GET operation.
        //
        // Try once per second for up to ten seconds to receive the result.
        var tries = 10;
        var waitTime = 100;
        var taskCompleted = false;
        
        var timeoutID = setInterval(function () { 
            // Limit the number of calls.
            if (--tries <= 0) {
                window.clearTimeout(timeoutID);
                responseTextArea.val("The response was not available in the time allowed.");
                return;
            }

            // The "Operation-Location" in the response contains the URI to retrieve the recognized text.
            var operationLocation = jqXHR.getResponseHeader("Operation-Location");
            
            // Perform the second REST API call and get the response.
            $.ajax({
                url: operationLocation,
                
                // Request headers.
                beforeSend: function(jqXHR){
                    jqXHR.setRequestHeader("Content-Type","application/json");
                    jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key",
                        encodeURIComponent($("#subscriptionKeyInput").val()));
                },
                
                type: "GET",
            })
            
            .done(function(data) {
                // If the result is not yet available, return.
                if (data.status && (data.status === "NotStarted" || data.status === "Running")) {
                    return;
                }
                
                // Show formatted JSON on webpage.
                responseTextArea.val(JSON.stringify(data, null, 2));
                
                // Indicate the task is complete and clear the timer.
                taskCompleted = true;
                window.clearTimeout(timeoutID);
            })
            
            .fail(function(jqXHR, textStatus, errorThrown) {
                // Indicate the task is complete and clear the timer.
                taskCompleted = true;
                window.clearTimeout(timeoutID);
                
                // Display error message.
                var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
                errorString += (jqXHR.responseText === "") ? "" : (jQuery.parseJSON(jqXHR.responseText).message) ? 
                    jQuery.parseJSON(jqXHR.responseText).message : jQuery.parseJSON(jqXHR.responseText).error.message;
                alert(errorString);
            });
        }, waitTime);
    })
    
    .fail(function(jqXHR, textStatus, errorThrown) {
        // Put the JSON description into the text area.
        responseTextArea.val(JSON.stringify(jqXHR, null, 2));
        
        // Display error message.
        var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
        errorString += (jqXHR.responseText === "") ? "" : (jQuery.parseJSON(jqXHR.responseText).message) ? 
            jQuery.parseJSON(jqXHR.responseText).message : jQuery.parseJSON(jqXHR.responseText).error.message;
        alert(errorString);
    });
}
```

#### <a name="run-the-handwriting-function"></a>Executar a função de manuscrito

Salve o arquivo **handwriting.html** e abra-o em um navegador da Web. Coloque a chave de assinatura no campo **Chave de assinatura** campo e verifique se você está usando a região correta em **Região de Assinatura**. Insira uma URL de uma imagem de texto para ler e, em seguida, clique no botão **Ler Imagem** para analisar uma imagem e ver o resultado.

## <a name="next-steps"></a>Próximas etapas

Neste guia, você usou a API REST Pesquisa Visual Computacional com JavaScript para testar muitos dos recursos de análise de imagem disponíveis. Em seguida, consulte a documentação de referência para saber mais sobre as APIs envolvidas.

- [API REST do Pesquisa Visual Computacional](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa)
