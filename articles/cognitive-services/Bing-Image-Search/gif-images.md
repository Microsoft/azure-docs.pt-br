---
title: Pesquisar imagens GIF usando a API de Pesquisa de Imagem do Bing
titleSuffix: Azure Cognitive Services
description: Use a API de Pesquisa de Imagem do Bing para pesquisar imagens .gif na Web.
services: cognitive-services
author: MikeDodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: article
ms.date: 04/24/2018
ms.author: rosh
ms.custom: seodec2018
ms.openlocfilehash: e7b2c43e06c8fbc1f561defebf29fdd37bc8bfab
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60917905"
---
# <a name="search-for-gif-images"></a>Pesquisar imagens GIF 

A API de Pesquisa de Imagem do Bing permite que você pesquise em toda a Web para obter as imagens .gif mais relevantes.  Os desenvolvedores podem integrar GIFs de engajamento em vários cenários de conversa. 

A URL a seguir é uma consulta de imagens .gif animadas.
```
https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=interesting&imageType=AnimatedGif&mkt=en-us
```
O parâmetro [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#query) especifica os termos de pesquisa.  A consulta anterior também especifica `animatedGif` usando o parâmetro de filtro [imageType](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagetype).

Para ver exemplos de resultados, use a seguinte URL para pesquisar em bing.com.
```
https://www.bing.com/images/search?q=interesting&qft=%20filterui%3Aphoto-animatedgif

```
## <a name="query-parameters"></a>Parâmetros de consulta

Para obter mais informações sobre parâmetros de consulta e opções, veja a [referência da API de Pesquisa de Imagem](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#query-parameters). A seguir está um exemplo sob o título [Pesquisar exemplo de GIF animado usando Java](#gifExample).

## <a name="tips-and-suggestions"></a>Dicas e sugestões

- Você pode especificar os parâmetros [maxFileSize](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#maxfilesize) e [minFileSize](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#minfilesize). É recomendável definir o maxFileSize = 2000000, uma vez que a maioria dos GIFs em nosso índice é menor que 2 MB.  Isso também ajudará a controlar o tamanho dos dados se a largura de banda for uma preocupação, como em cenários de celulares móveis.
- Para ajudar a melhorar o desempenho percebido, carregue a miniatura primeiro antes de carregar a URL de origem.  
- Para a primeira execução ou a experiência de página de aterrissagem na qual você ainda não tem uma consulta de usuário, tente usar nossas pesquisas de GIFs populares para ajudar por meio da [API de imagens populares](trending-images.md).
- Há três configurações para o parâmetro [safeSearch](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#safesearch).  A opção `strict` bloqueia conteúdo para adulto.
- Consulte [mkt](supported-countries-markets.md) para obter uma lista completa de idiomas e localizações compatíveis.
- *AnimatedGifHttps* retornará somente imagens em GIF animado de um endereço https. Para segurança, muitos aplicativos exigem conexão a links Web externos via https. Por exemplo, a Apple App Store requer conexão com serviços Web via HTTPS, o que criptografa dados do usuário protegidos em trânsito.

<a name="gifExample" />

## <a name="example-search-for-animated-gif-using-java"></a>Exemplo de pesquisa de GIF animado usando Java

A URL a seguir pesquisa imagens .gif animadas: `q=interesting`
```
https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=interesting&imageType=AnimatedGif&mkt=en-us

```
Conforme mostrado no exemplo a seguir, a consulta de URL requer o cabeçalho [Ocp-Apim-Subscription-Key](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#headers).

O exemplo de Java a seguir cria e envia a solicitação.

```
package gifSearch;
import java.net.*;
import java.util.*;
import java.io.*;
import javax.net.ssl.HttpsURLConnection;

import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;

/*
 * Gson: https://github.com/google/gson
 * Maven info:
 *     groupId: com.google.code.gson
 *     artifactId: gson
 *     version: 2.8.1
 *
 * Once you have compiled or downloaded gson-2.8.1.jar, assuming you have placed it in the
 * same folder as this file (BingImageSearch.java), you can compile and run this program at
 * the command line as follows.
 *
 * javac GIFsearch.java -classpath .;gson-2.8.1.jar -encoding UTF-8
 * java -cp .;gson-2.8.1.jar GIFsearch
 */


public class GIFsearch {

    // Replace the subscriptionKey string value with your valid subscription key.
    static String subscriptionKey = "YOUR-ACCESS-KEY";

    static String host = "https://api.cognitive.microsoft.com";
    static String path = "/bing/v7.0/images/search";

    static String searchTerm = "interesting";

    public static SearchResults SearchImages (String searchQuery) throws Exception {
        // construct URL of search request (endpoint + query string)
        URL url = new URL(host + path + "?q=" +  URLEncoder.encode(searchQuery, "UTF-8") + "&imageType=AnimatedGif&mkt=en-us");
        HttpsURLConnection connection = (HttpsURLConnection)url.openConnection();
        connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);

        // receive JSON body
        InputStream stream = connection.getInputStream();
        String response = new Scanner(stream).useDelimiter("\\A").next();

        // construct result object for return
        SearchResults results = new SearchResults(new HashMap<String, String>(), response);

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
    }

    // pretty-printer for JSON; uses GSON parser to parse and re-serialize
    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonObject json = parser.parse(json_text).getAsJsonObject();
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }

    public static void main (String[] args) {
        if (subscriptionKey.length() != 32) {
            System.out.println("Invalid Bing Search API subscription key!");
            System.out.println("Please paste yours into the source code.");
            System.exit(1);
        }

        try {
            System.out.println("Searching the Web for: " + searchTerm);

            SearchResults result = SearchImages(searchTerm);

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

}

//Container class for search results encapsulates relevant headers and JSON data
class SearchResults{
 HashMap<String, String> relevantHeaders;
 String jsonResponse;
 SearchResults(HashMap<String, String> headers, String json) {
     relevantHeaders = headers;
     jsonResponse = json;
 }
}

```

## <a name="results"></a>Resultados
O código obtém os seguintes resultados como objetos JSON:

```json
    {
      "webSearchUrl": "https://www.bing.com/images/search?view\u003ddetai...",
      "name": "Very Interesting GIF - Thats Very Interesting - ...",
      "thumbnailUrl": "https://tse1.mm.bing.net/th?id\u003dOIP.yJX6Vz345JPK...",
      "datePublished": "2017-03-12T01:35:00.0000000Z",
      "contentUrl": "https://media.contoso.co/images/c895fa573df8e493ca8d0dec7d93b/raw",
      "hostPageUrl": "https://www.contoso.co/view/thats-very-interesting-christi...",
      "contentSize": "1295633 B",
      "encodingFormat": "animatedgif",
      "hostPageDisplayUrl": "https://www.contoso.co/view/thats-very-christian...",
      "width": 440,
      "height": 186,
      "thumbnail": {
        "width": 474,
        "height": 200
      },
      "imageInsightsToken": "ccid_yJX6Vz34*mid_9FF0FFA42AADA1357F042443D2103B40EA...",
      "insightsMetadata": {
        "recipeSourcesCount": 0,
        "bestRepresentativeQuery": {
          "text": "That\u0027s Very Interesting",
          "displayText": "That\u0027s Very Interesting",
          "webSearchUrl": "https://www.bing.com/images/search?q\u003dThat..."
        },
        "pagesIncludingCount": 19,
        "availableSizesCount": 2
      },
      "imageId": "9FF0FFA42AADA1357F042443D21030EAAA225F",
      "accentColor": "62452D"
    },

```

## <a name="next-steps"></a>Próximas etapas
- [Início Rápido do C#](quickstarts/csharp.md)
- [Tutorial do aplicativo de página única de Pesquisa de Imagem](tutorial-bing-image-search-single-page-app.md)
