---
title: 'Início Rápido: Reconhecer tinta digital com a API REST de Reconhecimento de Tinta Digital e o Java'
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
ms.openlocfilehash: b1c739b6355d3b32063e5289720ed1d191611e65
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67721234"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-java"></a>Início Rápido: Reconhecer tinta digital com a API REST de Reconhecimento de Tinta Digital e o Java

Use este início rápido para começar a usar a API de Reconhecimento de Tinta Digital em traços de tinta digital. Esse aplicativo Java envia uma solicitação de API que contém dados de traço de tinta formatados em JSON e obtém a resposta.

Embora esse aplicativo seja escrito em Java, a API é um serviço Web RESTful compatível com a maioria das linguagens de programação.

Normalmente, você chamará a API em um aplicativo de escrita à tinta digital. Este início rápido envia dados de traço de tinta para a amostra manuscrita a seguir de um arquivo JSON.

![uma imagem de um texto manuscrito](../media/handwriting-sample.jpg)

O código-fonte deste Início Rápido pode ser encontrado no [GitHub](https://go.microsoft.com/fwlink/?linkid=2089904).

## <a name="prerequisites"></a>Pré-requisitos

- O JDK ([Java&trade; Development Kit) 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) ou posterior.

- Importar essas bibliotecas do repositório do Maven
    - Pacote [JSON em Java](https://mvnrepository.com/artifact/org.json/json)
    - Pacote [Apache HttpClient](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient)

- Os dados de traço de tinta de exemplo deste início rápido podem ser encontrados no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/InkRecognition/quickstart/example-ink-strokes.json).

[!INCLUDE [cognitive-services-ink-recognizer-signup-requirements](../../../../includes/cognitive-services-ink-recognizer-signup-requirements.md)]

## <a name="create-a-new-application"></a>Crie um novo aplicativo

1. Crie um novo projeto Java em seu IDE ou editor favorito e importe as bibliotecas a seguir.

    ```java
    import org.apache.http.HttpEntity;
    import org.apache.http.client.methods.CloseableHttpResponse;
    import org.apache.http.client.methods.HttpPost;
    import org.apache.http.entity.StringEntity;
    import org.apache.http.impl.client.CloseableHttpClient;
    import org.apache.http.impl.client.HttpClients;
    import org.apache.http.util.EntityUtils;
    import java.io.IOException;
    import java.nio.file.Files;
    import java.nio.file.Paths;
    ```

2. Crie variáveis para a chave de assinatura e o ponto de extremidade. Veja abaixo o URI que você pode usar para o reconhecimento de tinta. Ele será acrescentado ao ponto de extremidade de serviço posteriormente para criar a URL de solicitação da API.

    ```java
    // Replace the subscriptionKey string value with your valid subscription key.
    static final String subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
    // Replace the dataPath string with a path to the JSON formatted ink stroke data file.
    static final String dataPath = "PATH_TO_INK_STROKE_DATA";
    
    static final String endpoint = "https://api.cognitive.microsoft.com";
    static final String inkRecognitionUrl = "/inkrecognizer/v1.0-preview/recognize";
    ```

## <a name="create-a-function-to-send-requests"></a>Criar uma função para enviar solicitações

1. Crie uma função chamada `sendRequest()` que usa as variáveis criadas acima. Em seguida, siga estas etapas.

2. Crie um objeto `CloseableHttpClient` que pode enviar solicitações à API. Envie a solicitação para um objeto de solicitação `HttpPut` combinando o ponto de extremidade e a URL de Reconhecimento de Tinta Digital.

3. Use a função `setHeader()` da solicitação para definir o cabeçalho `Content-Type` como `application/json` e adicione a chave de assinatura ao cabeçalho `Ocp-Apim-Subscription-Key`.

4. Use a função `setEntity()` da solicitação para os dados a serem enviados.   

5. Use a função `execute()` do cliente para enviar a solicitação e salve-a em um objeto `CloseableHttpResponse`. 

6. Crie um objeto `HttpEntity` para armazenar o conteúdo da resposta. Obtenha o conteúdo com `getEntity()`. Se a resposta não estiver vazia, retorne-a.
    
    ```java
    static String sendRequest(String apiAddress, String endpoint, String subscriptionKey, String requestData) {
        try (CloseableHttpClient client = HttpClients.createDefault()) {
            HttpPut request = new HttpPut(endpoint + apiAddress);
            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
            request.setEntity(new StringEntity(requestData));
            try (CloseableHttpResponse response = client.execute(request)) {
                HttpEntity respEntity = response.getEntity();
                if (respEntity != null) {
                    return EntityUtils.toString(respEntity, "utf-8");
                }
            } catch (Exception respEx) {
                respEx.printStackTrace();
            }
        } catch (IOException ex) {
            System.err.println("Exception recognizing ink: " + ex.getMessage());
            ex.printStackTrace();
        }
        return null;
    }
    ```

## <a name="send-an-ink-recognition-request"></a>Enviar uma solicitação de reconhecimento de tinta

Crie um método chamado `recognizeInk()` para reconhecer os dados de traço de tinta. Chame o método `sendRequest()` criado acima com o ponto de extremidade, a URL, a chave de assinatura e os dados JSON. Obtenha o resultado e imprima-o no console.

```java
static void recognizeInk(String requestData) {
    System.out.println("Sending an ink recognition request");
    String result = sendRequest(inkRecognitionUrl, endpoint, subscriptionKey, requestData);
    System.out.println(result);
}
```

## <a name="load-your-digital-ink-data-and-send-the-request"></a>Carregar os dados de tinta digital e enviar a solicitação

1. No método principal do aplicativo, leia o arquivo JSON que contém os dados que serão adicionados às solicitações.

2. Chame a função de reconhecimento de tinta criada acima.
    
    ```java
    public static void main(String[] args) throws Exception {
        String requestData = new String(Files.readAllBytes(Paths.get(dataPath)), "UTF-8");
        recognizeInk(requestData);
    }
    ```

## <a name="run-the-application-and-view-the-response"></a>Executar o aplicativo e exibir a resposta

Execute o aplicativo. Uma resposta bem-sucedida é retornada no formato JSON. Você também pode encontrar a resposta JSON no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/InkRecognition/quickstart/example-response.json).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Referência da API REST](https://go.microsoft.com/fwlink/?linkid=2089907)


Para ver como a API do Reconhecimento de Tinta Digital funciona em um aplicativo de escrita à tinta digital, vejamos os seguintes aplicativos de exemplo no GitHub:
* [C# e UWP (Plataforma Universal do Windows)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# e WPF (Windows Presentation Foundation)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Aplicativo de navegador da Web JavaScript](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Aplicativo móvel Java e Android](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Aplicativo móvel Swift e iOS](https://go.microsoft.com/fwlink/?linkid=2089805)
