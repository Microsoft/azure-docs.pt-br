---
title: 'Início Rápido: usar Java para chamar a API REST de Pesquisa na Web do Bing'
titleSuffix: Azure Cognitive Services
description: Use este Início Rápido para enviar solicitações para a API REST de Pesquisa na Web do Bing usando Java e receba uma resposta JSON
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: quickstart
ms.date: 07/22/2019
ms.author: aahi
ms.custom: seodec2018, seo-java-july2019
ms.openlocfilehash: 42cb270955846f1333b79389c3ca979dc4808f05
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68489640"
---
# <a name="quickstart-search-the-web-using-the-bing-web-search-rest-api-and-java"></a>Início Rápido: pesquisar na Web usando a API REST de Pesquisa na Web do Bing e Java

Use este Início Rápido para fazer sua primeira chamada à API de Pesquisa na Web do Bing e receber a resposta JSON. Este aplicativo Java envia uma solicitação de pesquisa à API e exibe a resposta. Embora esse aplicativo seja escrito em Java, a API é um serviço Web RESTful compatível com a maioria das linguagens de programação.

## <a name="prerequisites"></a>Pré-requisitos

Aqui estão alguns itens de que você poderá precisar antes de executar este início rápido:

* [JDK 7 ou 8](https://aka.ms/azure-jdks)
* [Biblioteca Gson](https://github.com/google/gson)
* Uma chave de assinatura

[!INCLUDE [bing-web-search-quickstart-signup](../../../../includes/bing-web-search-quickstart-signup.md)]

## <a name="create-a-project-and-import-dependencies"></a>Criar um projeto e importar dependências

Crie um novo projeto Java em seu IDE ou editor favorito e importe as bibliotecas a seguir. Gson é necessário para converter objetos Java em JSON.

```java
import java.net.*;
import java.util.*;
import java.io.*;
import javax.net.ssl.HttpsURLConnection;
import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;
```

### <a name="declare-gson-in-the-maven-pom-file"></a>Declarar Gson no arquivo POM do Maven

Se você estiver usando Maven, declare Gson no `POM.xml`. Ignore esta etapa se você tiver instalado o Gson localmente.

```xml
<dependency>
    <groupId>com.google.code.gson</groupId>
    <artifactId>gson</artifactId>
    <version>2.8.5</version>
</dependency>
```

## <a name="declare-the-bingwebsearch-class"></a>Declare a classe BingWebSearch

Declare a classe `BingWebSearch`. Incluirá a maior parte do código que examinamos neste início rápido, incluindo o método `main`.  

```java
public class BingWebSearch {

// The code in the following sections goes here.

}
```

## <a name="define-variables"></a>Definir variáveis

Este código define `subscriptionKey`, `host`, `path` e `searchTerm`. Confirme que o ponto de extremidade está correto e substitua o valor `subscriptionKey` por uma chave de assinatura válida da sua conta do Azure. Fique à vontade para personalizar a consulta de pesquisa substituindo o valor para `searchTerm`. Lembre-se de adicionar esse código à classe `BingWebSearch`, conforme observado acima.

```java
// Enter a valid subscription key.
static String subscriptionKey = "enter key here";

/*
 * If you encounter unexpected authorization errors, double-check these values
 * against the endpoint for your Bing Web search instance in your Azure
 * dashboard.
 */
static String host = "https://api.cognitive.microsoft.com";
static String path = "/bing/v7.0/search";
static String searchTerm = "Microsoft Cognitive Services";
```

## <a name="construct-a-request"></a>Construir uma solicitação

Esse método, que reside na classe `BingWebSearch`, cria a `url`, recebe e analisa a resposta e extrai os cabeçalhos HTTP relacionados ao Bing.  

```java
public static SearchResults SearchWeb (String searchQuery) throws Exception {
    // Construct the URL.
    URL url = new URL(host + path + "?q=" +  URLEncoder.encode(searchQuery, "UTF-8"));

    // Open the connection.
    HttpsURLConnection connection = (HttpsURLConnection)url.openConnection();
    connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);

    // Receive the JSON response body.
    InputStream stream = connection.getInputStream();
    String response = new Scanner(stream).useDelimiter("\\A").next();

    // Construct the result object.
    SearchResults results = new SearchResults(new HashMap<String, String>(), response);

    // Extract Bing-related HTTP headers.
    Map<String, List<String>> headers = connection.getHeaderFields();
    for (String header : headers.keySet()) {
        if (header == null) continue;      // may have null key
        if (header.startsWith("BingAPIs-") || header.startsWith("X-MSEdge-")){
            results.relevantHeaders.put(header, headers.get(header).get(0));
        }
    }
    stream.close();
    return results;
}
```

## <a name="handle-the-response"></a>Tratar da resposta

Use Gson para analisar e serializar novamente a resposta.

```java
public static String prettify(String json_text) {
    JsonParser parser = new JsonParser();
    JsonObject json = parser.parse(json_text).getAsJsonObject();
    Gson gson = new GsonBuilder().setPrettyPrinting().create();
    return gson.toJson(json);
}
```

## <a name="declare-the-main-method"></a>Declarar o método Main

Esse método é necessário e é o primeiro invocado quando o programa é iniciado. Neste aplicativo, ele inclui código que valida a `subscriptionKey`, faz uma solicitação e imprime a resposta JSON.

```java
public static void main (String[] args) {
    // Confirm the subscriptionKey is valid.
    if (subscriptionKey.length() != 32) {
        System.out.println("Invalid Bing Search API subscription key!");
        System.out.println("Please paste yours into the source code.");
        System.exit(1);
    }

    // Call the SearchWeb method and print the response.
    try {
        System.out.println("Searching the Web for: " + searchTerm);
        SearchResults result = SearchWeb(searchTerm);
        System.out.println("\nRelevant HTTP Headers:\n");
        for (String header : result.relevantHeaders.keySet())
        System.out.println(header + ": " + result.relevantHeaders.get(header));
        System.out.println("\nJSON Response:\n");
        System.out.println(prettify(result.jsonResponse));
    }
    catch (Exception e) {
        e.printStackTrace(System.out);
        System.exit(1);
    }
}
```

## <a name="create-a-container-class-for-search-results"></a>Criar uma classe de contêiner para os resultados da pesquisa

A classe de contêiner `SearchResults` está fora da classe `BingWebSearch`. Inclui os cabeçalhos pertinentes e dados JSON para a resposta.

```java
class SearchResults{
    HashMap<String, String> relevantHeaders;
    String jsonResponse;
    SearchResults(HashMap<String, String> headers, String json) {
        relevantHeaders = headers;
        jsonResponse = json;
    }
}
```

## <a name="put-it-all-together"></a>Colocar tudo isso junto

A última etapa é compilar o código e executá-lo! Aqui estão os comandos:

```powershell
javac BingWebSearch.java -classpath ./gson-2.8.5.jar -encoding UTF-8
java -cp ./gson-2.8.5.jar BingWebSearch
```

Se você quiser comparar seu código com o nosso, o [exemplo de código está disponível no GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/Search/BingWebSearchv7.java).

## <a name="sample-response"></a>Resposta de exemplo

As respostas da API de Pesquisa na Web do Bing são retornadas como JSON. Este exemplo de resposta foi truncado para mostrar um único resultado.

```json
{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "Microsoft Cognitive Services"
  },
  "webPages": {
    "webSearchUrl": "https://www.bing.com/search?q=Microsoft+cognitive+services",
    "totalEstimatedMatches": 22300000,
    "value": [
      {
        "id": "https://api.cognitive.microsoft.com/api/v7/#WebPages.0",
        "name": "Microsoft Cognitive Services",
        "url": "https://www.microsoft.com/cognitive-services",
        "displayUrl": "https://www.microsoft.com/cognitive-services",
        "snippet": "Knock down barriers between you and your ideas. Enable natural and contextual interaction with tools that augment users' experiences via the power of machine-based AI. Plug them in and bring your ideas to life.",
        "deepLinks": [
          {
            "name": "Face API",
            "url": "https://azure.microsoft.com/services/cognitive-services/face/",
            "snippet": "Add facial recognition to your applications to detect, identify, and verify faces using a Face API from Microsoft Azure. ... Cognitive Services; Face API;"
          },
          {
            "name": "Text Analytics",
            "url": "https://azure.microsoft.com/services/cognitive-services/text-analytics/",
            "snippet": "Cognitive Services; Text Analytics API; Text Analytics API . Detect sentiment, ... you agree that Microsoft may store it and use it to improve Microsoft services, ..."
          },
          {
            "name": "Computer Vision API",
            "url": "https://azure.microsoft.com/services/cognitive-services/computer-vision/",
            "snippet": "Extract the data you need from images using optical character recognition and image analytics with Computer Vision APIs from Microsoft Azure."
          },
          {
            "name": "Emotion",
            "url": "https://www.microsoft.com/cognitive-services/en-us/emotion-api",
            "snippet": "Cognitive Services Emotion API - microsoft.com"
          },
          {
            "name": "Bing Speech API",
            "url": "https://azure.microsoft.com/services/cognitive-services/speech/",
            "snippet": "Add speech recognition to your applications, including text to speech, with a speech API from Microsoft Azure. ... Cognitive Services; Bing Speech API;"
          },
          {
            "name": "Get Started for Free",
            "url": "https://azure.microsoft.com/services/cognitive-services/",
            "snippet": "Add vision, speech, language, and knowledge capabilities to your applications using intelligence APIs and SDKs from Cognitive Services."
          }
        ]
      }
    ]
  },
  "relatedSearches": {
    "id": "https://api.cognitive.microsoft.com/api/v7/#RelatedSearches",
    "value": [
      {
        "text": "microsoft bot framework",
        "displayText": "microsoft bot framework",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+bot+framework"
      },
      {
        "text": "microsoft cognitive services youtube",
        "displayText": "microsoft cognitive services youtube",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+cognitive+services+youtube"
      },
      {
        "text": "microsoft cognitive services search api",
        "displayText": "microsoft cognitive services search api",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+cognitive+services+search+api"
      },
      {
        "text": "microsoft cognitive services news",
        "displayText": "microsoft cognitive services news",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+cognitive+services+news"
      },
      {
        "text": "ms cognitive service",
        "displayText": "ms cognitive service",
        "webSearchUrl": "https://www.bing.com/search?q=ms+cognitive+service"
      },
      {
        "text": "microsoft cognitive services text analytics",
        "displayText": "microsoft cognitive services text analytics",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+cognitive+services+text+analytics"
      },
      {
        "text": "microsoft cognitive services toolkit",
        "displayText": "microsoft cognitive services toolkit",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+cognitive+services+toolkit"
      },
      {
        "text": "microsoft cognitive services api",
        "displayText": "microsoft cognitive services api",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+cognitive+services+api"
      }
    ]
  },
  "rankingResponse": {
    "mainline": {
      "items": [
        {
          "answerType": "WebPages",
          "resultIndex": 0,
          "value": {
            "id": "https://api.cognitive.microsoft.com/api/v7/#WebPages.0"
          }
        }
      ]
    },
    "sidebar": {
      "items": [
        {
          "answerType": "RelatedSearches",
          "value": {
            "id": "https://api.cognitive.microsoft.com/api/v7/#RelatedSearches"
          }
        }
      ]
    }
  }
}
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Tutorial de aplicativo de página única de pesquisa na Web do Bing](../tutorial-bing-web-search-single-page-app.md)

[!INCLUDE [bing-web-search-quickstart-see-also](../../../../includes/bing-web-search-quickstart-see-also.md)]  
