---
title: 'Início Rápido: Pesquisar vídeos usando a API REST e o Java – Pesquisa de Vídeo do Bing'
titleSuffix: Azure Cognitive Services
description: Use este início rápido para enviar solicitações de pesquisa de vídeo para a API REST de Pesquisa de Vídeo do Bing usando Java.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: quickstart
ms.date: 05/22/2020
ms.custom: devx-track-java
ms.author: aahi
ms.openlocfilehash: 6c34ec5582b8251d5b64bf24654b50d7c391cee4
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96341386"
---
# <a name="quickstart-search-for-videos-using-the-bing-video-search-rest-api-and-java"></a>Início Rápido: Pesquisar vídeos usando a API de Pesquisa de Vídeo do Bing e Java

> [!WARNING]
> As APIs de Pesquisa do Bing estão migrando dos Serviços Cognitivos para os Serviços de Pesquisa do Bing. A partir de **30 de outubro de 2020**, todas as novas instâncias da Pesquisa do Bing precisam ser provisionadas seguindo o processo documentado [aqui](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> As APIs de Pesquisa do Bing provisionadas por meio dos Serviços Cognitivos terão suporte nos próximos três anos ou até o final do seu Contrato Enterprise, o que ocorrer primeiro.
> Para obter instruções sobre a migração, confira [Serviços de Pesquisa do Bing](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Use este início rápido para fazer sua primeira chamada à API da Pesquisa de Vídeo do Bing. Este aplicativo Java simples envia uma consulta de pesquisa de vídeo HTTP para a API e exibe a resposta JSON. Embora esse aplicativo seja escrito em Java, a API é um serviço Web RESTful compatível com a maioria das linguagens de programação. 

O código-fonte para deste exemplo está disponível [no GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/Search/BingVideoSearchv7.java) com anotações de código, recursos e tratamentos de erro adicionais.

## <a name="prerequisites"></a>Pré-requisitos

* O [JDK (Java Development Kit)](https://www.oracle.com/technetwork/java/javase/downloads/jdk11-downloads-5066655.html)

* A [biblioteca Gson](https://github.com/google/gson)

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](../../../../includes/cognitive-services-bing-video-search-signup-requirements.md)]


## <a name="create-and-initialize-a-project"></a>Criar e inicializar um projeto

1. Crie um projeto do Java em seu IDE ou editor favorito e importe as seguintes bibliotecas:

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

2. Criar uma nova classe chamada `SearchResults` para armazenar os cabeçalhos e uma resposta JSON da API.

    ```java
    // Container class for search results encapsulates relevant headers and JSON data
    class SearchResults{
        HashMap<String, String> relevantHeaders;
        String jsonResponse;
        SearchResults(HashMap<String, String> headers, String json) {
            relevantHeaders = headers;
            jsonResponse = json;
        }
    }
    ```

3. Criar um método chamado `SearchVideos()` com variáveis para seu host do ponto de extremidade e caminho da API, sua chave de assinatura e um termo de pesquisa. Esse método retorna um objeto `SearchResults`. Para o valor `host`, você pode usar o ponto de extremidade global no código a seguir ou usar o ponto de extremidade do [subdomínio personalizado](../../../cognitive-services/cognitive-services-custom-subdomains.md) exibido no portal do Azure para seu recurso.

    ```java
    public static SearchResults SearchVideos (String searchQuery) throws Exception {
        static String subscriptionKey = "enter your key here";
        static String host = "https://api.cognitive.microsoft.com";
        static String path = "/bing/v7.0/videos/search";
        static String searchTerm = "kittens";
    }
    ```

## <a name="construct-and-send-the-search-request"></a>Construir e enviar a solicitação de pesquisa

No método `SearchVideos()`, execute as seguintes etapas:

1. Construa a URL para a sua solicitação combinando seu host de API, caminho e consulta de pesquisa codificada. Use `openConnection()` para criar uma conexão e adicione sua chave de assinatura ao cabeçalho `Ocp-Apim-Subscription-Key`.

     ```java
     URL url = new URL(host + path + "?q=" +  URLEncoder.encode(searchQuery, "UTF-8"));
     HttpsURLConnection connection = (HttpsURLConnection)url.openConnection();
     connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);
     ```

2. Obtenha a resposta da API e armazene a cadeia de caracteres JSON.

     ```java
     InputStream stream = connection.getInputStream();
     String response = new Scanner(stream).useDelimiter("\\A").next();
     ```

 3. Use `getHeaderFields()` para extrair os cabeçalhos HTTP da resposta e armazenar daquelas relacionadas ao Bing no objeto `results`. Em seguida, feche o fluxo e retorne o resultado.

     ```java
     // extract Bing-related HTTP headers
     Map<String, List<String>> headers = connection.getHeaderFields();
     for (String header : headers.keySet()) {
         if (header == null) continue;      // may have null key
         if (header.startsWith("BingAPIs-") || header.startsWith("X-MSEdge-")) {
             results.relevantHeaders.put(header, headers.get(header).get(0));
         }
     }
     stream.close();
     return results;
     ```

## <a name="format-the-response"></a>Formatar a resposta

Crie um método chamado `prettify()` para formatar a resposta retornada da API de Vídeo do Bing. Use a biblioteca de Gson `JsonParser` para converter uma cadeia de caracteres JSON em um objeto. Em seguida, use `GsonBuilder()` e `toJson()` para criar a cadeia de caracteres formatada.

```java
// pretty-printer for JSON; uses GSON parser to parse and re-serialize
public static String prettify(String json_text) {
    JsonParser parser = new JsonParser();
    JsonObject json = parser.parse(json_text).getAsJsonObject();
    Gson gson = new GsonBuilder().setPrettyPrinting().create();
    return gson.toJson(json);
}
```

## <a name="send-the-request-and-print-the-response"></a>Envie a solicitação e imprimir a resposta

No método principal do seu aplicativo, chame `SearchVideos` com seu termo de pesquisa. Em seguida, imprima os cabeçalhos HTTP armazenados na resposta, bem como a cadeia de caracteres JSON retornada pela API.

 ```java
 public static void main (String[] args) {

     SearchResults result = SearchVideos(searchTerm);
     //print the Relevant HTTP Headers
     for (String header : result.relevantHeaders.keySet())
         System.out.println(header + ": " + result.relevantHeaders.get(header));
     System.out.println(prettify(result.jsonResponse));
 }
 ```

## <a name="json-response"></a>Resposta JSON

Uma resposta com êxito é retornada em JSON, conforme mostrado no seguinte exemplo:

```json
{
    "_type": "Videos",
    "instrumentation": {},
    "readLink": "https://api.cognitive.microsoft.com/api/v7/videos/search?q=kittens",
    "webSearchUrl": "https://www.bing.com/videos/search?q=kittens",
    "totalEstimatedMatches": 1000,
    "value": [
        {
            "webSearchUrl": "https://www.bing.com/videos/search?q=kittens&view=...",
            "name": "Top 10 cute kitten videos compilation",
            "description": "HELP HOMELESS ANIMALS AND WIN A PRIZE BY CHOOSING...",
            "thumbnailUrl": "https://tse4.mm.bing.net/th?id=OVP.n1aE_Oikl4MtzBb...",
            "datePublished": "2014-11-12T22:47:36.0000000",
            "publisher": [
                {
                    "name": "Fabrikam"
                }
            ],
            "creator": {
                "name": "Marcus Appel"
            },
            "isAccessibleForFree": true,
            "contentUrl": "https://www.fabrikam.com/watch?v=8HVWitAW-Qg",
            "hostPageUrl": "https://www.fabrikam.com/watch?v=8HVWitAW-Qg",
            "encodingFormat": "h264",
            "hostPageDisplayUrl": "https://www.fabrikam.com/watch?v=8HVWitAW-Qg",
            "width": 480,
            "height": 360,
            "duration": "PT3M52S",
            "motionThumbnailUrl": "https://tse4.mm.bing.net/th?id=OM.j4QyJAENJphdZQ_1501386166&pid=Api",
            "embedHtml": "<iframe width=\"1280\" height=\"720\" src=\"https://www.fabrikam.com/embed/8HVWitAW-Qg?autoplay=1\" frameborder=\"0\" allowfullscreen></iframe>",
            "allowHttpsEmbed": true,
            "viewCount": 7513633,
            "thumbnail": {
                "width": 300,
                "height": 168
            },
            "videoId": "655D98260D012432848F6558260D012432848F",
            "allowMobileEmbed": true,
            "isSuperfresh": false
        },
        . . .
    ],
    "nextOffset": 36,
    "queryExpansions": [
        {
            "text": "Kittens Meowing",
            "displayText": "Meowing",
            "webSearchUrl": "https://www.bing.com/videos/search?q=Kittens+Meowing...",
            "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search...",
            "thumbnail": {
                "thumbnailUrl": "https://tse3.mm.bing.net/th?q=Kittens+Meowing&pid..."
            }
        },
        {
            "text": "Funny Kittens",
            "displayText": "Funny",
            "webSearchUrl": "https://www.bing.com/videos/search?q=Funny+Kittens...",
            "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search...",
            "thumbnail": {
                "thumbnailUrl": "https://tse3.mm.bing.net/th?q=Funny+Kittens&..."
            }
        },
        . . .
    ],
    "pivotSuggestions": [
        {
            "pivot": "kittens",
            "suggestions": [
                {
                    "text": "Cat",
                    "displayText": "Cat",
                    "webSearchUrl": "https://www.bing.com/videos/search?q=Cat...",
                    "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search?...",
                    "thumbnail": {
                        "thumbnailUrl": "https://tse3.mm.bing.net/th?q=Cat&pid=Api..."
                    }
                },
                {
                    "text": "Feral Cat",
                    "displayText": "Feral Cat",
                    "webSearchUrl": "https://www.bing.com/videos/search?q=Feral+Cat...",
                    "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search...",
                    "thumbnail": {
                        "thumbnailUrl": "https://tse3.mm.bing.net/th?q=Feral+Cat&pid=Api&..."
                    }
                }
            ]
        }
    ],
    "relatedSearches": [
        {
            "text": "Kittens Being Born",
            "displayText": "Kittens Being Born",
            "webSearchUrl": "https://www.bing.com/videos/search?q=Kittens+Being+Born...",
            "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search?...",
            "thumbnail": {
                "thumbnailUrl": "https://tse1.mm.bing.net/th?q=Kittens+Being+Born&pid=..."
            }
        },
        . . .
    ]
}
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar um aplicativo Web de página única](../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Confira também 

 [O que é a API da Pesquisa de Vídeo do Bing?](../overview.md)