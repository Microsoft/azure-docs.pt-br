---
title: 'Início Rápido: Enviar uma solicitação de pesquisa para a API REST usando o Java – Pesquisa de Entidade do Bing'
titleSuffix: Azure Cognitive Services
description: Use este Início Rápido para enviar uma solicitação para a API REST de Pesquisa de Entidade do Bing usando o Java e receba uma resposta JSON.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 05/08/2020
ms.custom: devx-track-java
ms.author: aahi
ms.openlocfilehash: 82001a07aea6a76936986d076f689f97cf19d57d
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96351445"
---
# <a name="quickstart-send-a-search-request-to-the-bing-entity-search-rest-api-using-java"></a>Início Rápido: Enviar uma solicitação de pesquisa para a API REST da Pesquisa de Entidade do Bing usando o Java

> [!WARNING]
> As APIs de Pesquisa do Bing estão migrando dos Serviços Cognitivos para os Serviços de Pesquisa do Bing. A partir de **30 de outubro de 2020**, todas as novas instâncias da Pesquisa do Bing precisam ser provisionadas seguindo o processo documentado [aqui](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> As APIs de Pesquisa do Bing provisionadas por meio dos Serviços Cognitivos terão suporte nos próximos três anos ou até o final do seu Contrato Enterprise, o que ocorrer primeiro.
> Para obter instruções sobre a migração, confira [Serviços de Pesquisa do Bing](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Use este início rápido para fazer sua primeira chamada à API de Pesquisa de Entidade do Bing e exibir a resposta JSON. Este aplicativo Java simples envia uma consulta de pesquisa de notícias para a API e exibe a resposta.

Embora esse aplicativo seja escrito em Java, a API é um serviço Web RESTful compatível com a maioria das linguagens de programação.

## <a name="prerequisites"></a>Pré-requisitos

* O [JDK (Kit de Desenvolvimento Java)](https://www.oracle.com/technetwork/java/javase/downloads/).
* A [biblioteca Gson](https://github.com/google/gson).


[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Criar e inicializar um projeto

1. Crie um projeto do Java em seu IDE ou editor favorito e importe as seguintes bibliotecas:

   ```java
   import java.io.*;
   import java.net.*;
   import java.util.*;
   import javax.net.ssl.HttpsURLConnection;
   import com.google.gson.Gson;
   import com.google.gson.GsonBuilder;
   import com.google.gson.JsonObject;
   import com.google.gson.JsonParser;
   import com.google.gson.Gson;
   import com.google.gson.GsonBuilder;
   import com.google.gson.JsonObject;
   import com.google.gson.JsonParser;
   ```

2. Em uma classe, crie variáveis para o ponto de extremidade de API, a chave de assinatura e uma consulta de pesquisa. É possível usar o ponto de extremidade global no código a seguir ou o ponto de extremidade do [subdomínio personalizado](../../../cognitive-services/cognitive-services-custom-subdomains.md) exibido no portal do Azure para seu recurso.

   ```java
   public class EntitySearch {

      static String subscriptionKey = "ENTER KEY HERE";
    
        static String host = "https://api.cognitive.microsoft.com";
        static String path = "/bing/v7.0/entities";
    
        static String mkt = "en-US";
        static String query = "italian restaurant near me";
   //...
    
   ```

## <a name="construct-a-search-request-string"></a>Construir uma cadeia de caracteres de solicitação de pesquisa

1. Crie uma função chamada `search()` que retorna uma `String` JSON. codifique a consulta de pesquisa em URL e a adicione a uma cadeia de caracteres de parâmetros com `&q=`. Adicione seu mercado à cadeia de caracteres de parâmetro com `?mkt=`.
 
2. Crie um objeto de URL com as cadeias de caracteres de host, caminho e parâmetros.
    
    ```java
    //...
    public static String search () throws Exception {
        String encoded_query = URLEncoder.encode (query, "UTF-8");
        String params = "?mkt=" + mkt + "&q=" + encoded_query;
        URL url = new URL (host + path + params);
    //...
    ```
      
## <a name="send-a-search-request-and-receive-a-response"></a>Enviar uma solicitação de pesquisa e receber uma resposta

1. Na função `search()` criada acima, crie um objeto `HttpsURLConnection` com `url.openCOnnection()`. Defina o método de solicitação como `GET` e adicione a chave de assinatura ao cabeçalho `Ocp-Apim-Subscription-Key`.

    ```java
    //...
    HttpsURLConnection connection = (HttpsURLConnection) url.openConnection();
    connection.setRequestMethod("GET");
    connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);
    connection.setDoOutput(true);
    //...
    ```

2. Crie um `StringBuilder`. Use um novo `InputStreamReader` como parâmetro ao criar uma instância do `BufferedReader` para ler a resposta da API.  
    
    ```java
    //...
    StringBuilder response = new StringBuilder ();
    BufferedReader in = new BufferedReader(
        new InputStreamReader(connection.getInputStream()));
    //...
    ```

3. Crie um objeto `String` para armazenar a resposta do `BufferedReader`. Itere por ele e acrescente cada linha à cadeia de caracteres. Em seguida, feche o leitor e retorne a resposta. 
    
    ```java
    String line;
    
    while ((line = in.readLine()) != null) {
      response.append(line);
    }
    in.close();
    
    return response.toString();
    ```

## <a name="format-the-json-response"></a>Formatar a resposta JSON

1. Crie uma função chamada `prettify` para formatar a resposta JSON. Crie um `JsonParser`, chame `parse()` no texto JSON e, em seguida, armazene-o como um objeto JSON. 

2. Use a biblioteca Gson para criar um `GsonBuilder()`, use `setPrettyPrinting().create()` para formatar o JSON e, em seguida, retorne-o.    
  
   ```java
   //...
   public static String prettify (String json_text) {
    JsonParser parser = new JsonParser();
    JsonObject json = parser.parse(json_text).getAsJsonObject();
    Gson gson = new GsonBuilder().setPrettyPrinting().create();
    return gson.toJson(json);
   }
   //...
   ```

## <a name="call-the-search-function"></a>Chamar a função de pesquisa

- No método principal do projeto, chame `search()` e use `prettify()` para formatar o texto.
    
    ```java
        public static void main(String[] args) {
            try {
                String response = search ();
                System.out.println (prettify (response));
            }
            catch (Exception e) {
                System.out.println (e);
            }
        }
    ```

## <a name="example-json-response"></a>Resposta JSON de exemplo

Uma resposta com êxito é retornada em JSON, conforme mostrado no seguinte exemplo: 

```json
{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "italian restaurant near me",
    "askUserForLocation": true
  },
  "places": {
    "value": [
      {
        "_type": "LocalBusiness",
        "webSearchUrl": "https://www.bing.com/search?q=sinful+bakery&filters=local...",
        "name": "Liberty's Delightful Sinful Bakery & Cafe",
        "url": "https://www.contoso.com/",
        "entityPresentationInfo": {
          "entityScenario": "ListItem",
          "entityTypeHints": [
            "Place",
            "LocalBusiness"
          ]
        },
        "address": {
          "addressLocality": "Seattle",
          "addressRegion": "WA",
          "postalCode": "98112",
          "addressCountry": "US",
          "neighborhood": "Madison Park"
        },
        "telephone": "(800) 555-1212"
      },

      . . .
      {
        "_type": "Restaurant",
        "webSearchUrl": "https://www.bing.com/search?q=Pickles+and+Preserves...",
        "name": "Munson's Pickles and Preserves Farm",
        "url": "https://www.princi.com/",
        "entityPresentationInfo": {
          "entityScenario": "ListItem",
          "entityTypeHints": [
            "Place",
            "LocalBusiness",
            "Restaurant"
          ]
        },
        "address": {
          "addressLocality": "Seattle",
          "addressRegion": "WA",
          "postalCode": "98101",
          "addressCountry": "US",
          "neighborhood": "Capitol Hill"
        },
        "telephone": "(800) 555-1212"
      },
      
      . . .
    ]
  }
}
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar um aplicativo Web de página única](../tutorial-bing-entities-search-single-page-app.md)

* [O que é a API de Pesquisa de Entidade do Bing?](../overview.md)
* [Referência da API de Pesquisa de Entidade do Bing](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference).