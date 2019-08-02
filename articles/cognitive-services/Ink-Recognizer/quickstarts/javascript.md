---
title: 'Início Rápido: Reconhecer tinta digital com a API REST de Reconhecimento de Tinta Digital e o Node.js'
titleSuffix: Azure Cognitive Services
description: Use a API de Reconhecimento de Tinta Digital para começar a reconhecer traços de tinta digital.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: quickstart
ms.date: 05/02/2019
ms.author: aahi
ms.openlocfilehash: 7e158b0ae27780eeecb1ee7948087bf59b1502e1
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67721260"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-javascript"></a>Início Rápido: Reconhecer tinta digital com a API REST de Reconhecimento de Tinta Digital e JavaScript

Use este guia de início rápido para começar a usar a API de Reconhecimento de Tinta Digital em traços de tinta digital. Este aplicativo JavaScript envia uma solicitação de API que contém dados do traço de tinta formatados em JSON e exibe a resposta.

Embora o aplicativo seja escrito em Javascript e executado no navegador da Web, a API é um serviço Web RESTful compatível com a maioria das linguagens de programação.

Normalmente você chamaria a API em um aplicativo de tinta digital. Este guia de início rápido envia dados do traço de tinta para o exemplo a seguir de um manuscrito de arquivo JSON.

![Imagem de um texto manuscrito](../media/handwriting-sample.jpg)

O código-fonte deste Início Rápido pode ser encontrado no [GitHub](https://go.microsoft.com/fwlink/?linkid=2089905).

## <a name="prerequisites"></a>Pré-requisitos

- Um navegador da Web
- Os dados de traço de tinta do exemplo deste Início Rápido podem ser encontrados no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/javascript/InkRecognition/quickstart/example-ink-strokes.json).


[!INCLUDE [cognitive-services-ink-recognizer-signup-requirements](../../../../includes/cognitive-services-ink-recognizer-signup-requirements.md)]

## <a name="create-a-new-application"></a>Crie um novo aplicativo

1. Em seu IDE ou editor favorito, crie um arquivo novo do `.html`. Em seguida, adicione HTML básico para o código que vamos adicionar mais tarde.
    
    ```html
    <!DOCTYPE html>
    <html>
    
        <head>
            <script type="text/javascript">
            </script>
        </head>
        
        <body>
        </body>
    
    </html>
    ```

2. Dentro da marca `<body>`, adicione o seguinte HTML:
    1. Duas áreas de texto para exibir a solicitação e a resposta de JSON.
    2. Um botão para chamar a função `recognizeInk()` a ser criada posteriormente.
    
    ```HTML
    <!-- <body>-->
        <h2>Send a request to the Ink Recognition API</h2>
        <p>Request:</p>
        <textarea id="request" style="width:800px;height:300px"></textarea>
        <p>Response:</p>
        <textarea id="response" style="width:800px;height:300px"></textarea>
        <br>
        <button type="button" onclick="recognizeInk()">Recognize Ink</button>
    <!--</body>-->
    ```

## <a name="load-the-example-json-data"></a>Carregar o dados JSON de exemplo

1. Dentro da marca `<script>`, crie uma variável para o sampleJson. Em seguida, crie uma função JavaScript chamada `openFile()` que abre um Explorador de Arquivos para que você possa selecionar o arquivo JSON. Ao clicar no botão `Recognize ink`, ele chama essa função e começa a ler o arquivo.
2. Use a função `onload()` do objeto `FileReader` para processar o arquivo de forma assíncrona. 
    1. Substituir `\n` ou `\r` caracteres no arquivo por uma cadeia de caracteres vazia. 
    2. Usar `JSON.parse()` para converter o texto JSON válido
    3. Atualize a caixa de texto `request` no aplicativo. Use `JSON.stringify()` para formatar a cadeia de caracteres JSON. 
    
    ```javascript
    var sampleJson = "";
    function openFile(event) {
        var input = event.target;
    
        var reader = new FileReader();
        reader.onload = function(){
            sampleJson = reader.result.replace(/(\\r\\n|\\n|\\r)/gm, "");
            sampleJson = JSON.parse(sampleJson);
            document.getElementById('request').innerHTML = JSON.stringify(sampleJson, null, 2);
        };
        reader.readAsText(input.files[0]);
    };
    ```

## <a name="send-a-request-to-the-ink-recognizer-api"></a>Enviar solicitação à API de Reconhecimento de Tinta Digital

1. Dentro da marca `<script>`, crie uma função chamada `recognizeInk()`. Depois essa função chamará a API e atualizará a página com a resposta. Adicione o código das etapas a seguir dentro desta função. 
        
    ```javascript
    function recognizeInk() {
    // add the code from the below steps here 
    }
    ```

    1. Crie variáveis para a URL do ponto de extremidade, a chave de assinatura e JSON de exemplo. Em seguida, crie um objeto `XMLHttpRequest` para enviar a solicitação de API. 
        
        ```javascript
        // Replace the below URL with the correct one for your subscription. 
        // Your endpoint can be found in the Azure portal. For example: https://westus2.api.cognitive.microsoft.com
        var SERVER_ADDRESS = "YOUR-SUBSCRIPTION-URL";
        var ENDPOINT_URL = SERVER_ADDRESS + "/inkrecognizer/v1.0-preview/recognize";
        // Replace the subscriptionKey string value with your valid subscription key.
        var SUBSCRIPTION_KEY = "YOUR-SUBSCRIPTION-KEY";
        var xhttp = new XMLHttpRequest();
        ```
    2. Crie a função de retorno para o objeto `XMLHttpRequest`. Essa função analisa a resposta de API a uma solicitação bem-sucedida e a exibe no aplicativo. 
            
        ```javascript
        function returnFunction(xhttp) {
            var response = JSON.parse(xhttp.responseText);
            console.log("Response: %s ", response);
            document.getElementById('response').innerHTML = JSON.stringify(response, null, 2);
        }
        ```
    3. Crie a função de erro do objeto da solicitação. Essa função registra o erro no console. 
            
        ```javascript
        function errorFunction() {
            console.log("Error: %s, Detail: %s", xhttp.status, xhttp.responseText);
        }
        ```

    4. Crie uma função para a propriedade `onreadystatechange` do objeto da solicitação. Quando o estado de prontidão do objeto da solicitação for alterado, as funções de erro e retorno acima serão aplicadas.
            
        ```javascript
        xhttp.onreadystatechange = function () {
            if (this.readyState === 4) {
                if (this.status === 200) {
                    returnFunction(xhttp);
                } else {
                    errorFunction(xhttp);
                }
            }
        };
        ```
    
    5. Envie a solicitação da API. Adicione a chave de assinatura ao cabeçalho `Ocp-Apim-Subscription-Key` e configure `content-type` como `application/json`
    
        ```javascript
        xhttp.open("PUT", ENDPOINT_URL, true);
        xhttp.setRequestHeader("Ocp-Apim-Subscription-Key", SUBSCRIPTION_KEY);
        xhttp.setRequestHeader("content-type", "application/json");
        xhttp.send(JSON.stringify(sampleJson));
        };
        ```

## <a name="run-the-application-and-view-the-response"></a>Executar o aplicativo e exibir a resposta

É possível executar este aplicativo no navegador da Web. Uma resposta bem-sucedida é retornada no formato JSON. Você também pode encontrar a resposta JSON no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/javascript/InkRecognition/quickstart/example-response.json):

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Referência da API REST](https://go.microsoft.com/fwlink/?linkid=2089907)

Para ver como a API do Reconhecimento de Tinta Digital funciona em um aplicativo de escrita à tinta digital, vejamos os seguintes aplicativos de exemplo no GitHub:
* [C# e UWP (Plataforma Universal do Windows)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# e WPF (Windows Presentation Foundation)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Aplicativo de navegador da Web JavaScript](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Aplicativo móvel Java e Android](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Aplicativo móvel Swift e iOS](https://go.microsoft.com/fwlink/?linkid=2089805)
