---
title: 'Início Rápido: Analisar uma imagem remota – REST, Java'
titleSuffix: Azure Cognitive Services
description: Neste início rápido, você analisará uma imagem usando a API da Pesquisa Visual Computacional com Java.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: eadf3aca848f1b8ad24cd387e1988cdf8ea55580
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67604435"
---
# <a name="quickstart-analyze-a-remote-image-using-the-computer-vision-rest-api-and-java"></a>Início Rápido: Analisar uma imagem remota usando o Java e a API REST da Pesquisa Visual Computacional

Neste início rápido, você analisará uma imagem armazenada remotamente para extrair recursos visuais usando a API REST de Pesquisa Visual Computacional. Com o método [Analyze Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa), é possível extrair recursos visuais com base no conteúdo de imagem.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

- É preciso ter a [Java&trade; Platform e o Standard Edition Development Kit 7 ou 8](https://aka.ms/azure-jdks) (JDK 7 ou 8) instalados.
- Você precisa ter uma chave de assinatura para a Pesquisa Visual Computacional. É possível obter uma chave de avaliação gratuita em [Experimente os Serviços Cognitivos](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Ou siga as instruções em [Criar uma conta dos Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) para assinar a Pesquisa Visual Computacional e obter sua chave.

## <a name="create-and-run-the-sample-application"></a>Criar e executar o aplicativo de exemplo

Para criar e executar o exemplo, siga estas etapas:

1. Crie um projeto Java em seu IDE ou editor favorito. Se a opção estiver disponível, crie o projeto Java com um modelo de aplicativo de linha de comando.
1. Importe as bibliotecas a seguir para seu projeto Java. Se você usar o Maven, as coordenadas do Maven serão fornecidas para cada biblioteca.
   - [Cliente do Apache HTTP](https://hc.apache.org/downloads.cgi) (org.apache.httpcomponents:httpclient:4.5.5)
   - [Núcleo do Apache HTTP](https://hc.apache.org/downloads.cgi) (org.apache.httpcomponents:httpcore:4.4.9)
   - [Biblioteca JSON](https://github.com/stleary/JSON-java) (org.json:json:20180130)
1. Adicione as seguintes instruções `import` ao arquivo que contém a classe pública `Main` para seu projeto.  

   ```java
   import java.net.URI;
   import org.apache.http.HttpEntity;
   import org.apache.http.HttpResponse;
   import org.apache.http.client.methods.HttpPost;
   import org.apache.http.entity.StringEntity;
   import org.apache.http.client.utils.URIBuilder;
   import org.apache.http.impl.client.CloseableHttpClient;
   import org.apache.http.impl.client.HttpClientBuilder;
   import org.apache.http.util.EntityUtils;
   import org.json.JSONObject;
   ```

1. Substitua a classe pública `Main` pelo código a seguir e realize as seguintes alterações no código, se necessário:
   1. Substitua o valor de `subscriptionKey` pela sua chave de assinatura.
   1. Substitua o valor de `uriBase` pela URL do ponto de extremidade para o método [Analyze Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) da região do Azure em que você adquiriu suas chaves de assinatura, se necessário.
   1. Outra opção é substituir o valor de `imageToAnalyze` pela URL de uma imagem diferente que você deseja analisar.

```java
public class Main {
    // **********************************************
    // *** Update or verify the following values. ***
    // **********************************************

    // Replace <Subscription Key> with your valid subscription key.
    private static final String subscriptionKey = "<Subscription Key>";

    // You must use the same Azure region in your REST API method as you used to
    // get your subscription keys. For example, if you got your subscription keys
    // from the West US region, replace "westcentralus" in the URL
    // below with "westus".
    //
    // Free trial subscription keys are generated in the "westcentralus" region.
    // If you use a free trial subscription key, you shouldn't need to change
    // this region.
    private static final String uriBase =
            "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/analyze";

    private static final String imageToAnalyze =
            "https://upload.wikimedia.org/wikipedia/commons/" +
                    "1/12/Broadway_and_Times_Square_by_night.jpg";

    public static void main(String[] args) {
        CloseableHttpClient httpClient = HttpClientBuilder.create().build();

        try {
            URIBuilder builder = new URIBuilder(uriBase);

            // Request parameters. All of them are optional.
            builder.setParameter("visualFeatures", "Categories,Description,Color");
            builder.setParameter("language", "en");

            // Prepare the URI for the REST API method.
            URI uri = builder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);

            // Request body.
            StringEntity requestEntity =
                    new StringEntity("{\"url\":\"" + imageToAnalyze + "\"}");
            request.setEntity(requestEntity);

            // Call the REST API method and get the response entity.
            HttpResponse response = httpClient.execute(request);
            HttpEntity entity = response.getEntity();

            if (entity != null) {
                // Format and display the JSON response.
                String jsonString = EntityUtils.toString(entity);
                JSONObject json = new JSONObject(jsonString);
                System.out.println("REST Response:\n");
                System.out.println(json.toString(2));
            }
        } catch (Exception e) {
            // Display error message.
            System.out.println(e.getMessage());
        }
    }
}
```

## <a name="compile-and-run-the-program"></a>Compilar e executar o programa

1. Salve e crie o projeto Java.
1. Se você usar um IDE, execute `Main`.

Como alternativa, se você estiver executando o programa de uma janela de linha de comando, execute os comandos a seguir. Esses comandos presumem que suas bibliotecas estão em uma pasta chamada `libs` que está na mesma pasta que `Main.java`; se não, você precisará substituir `libs` pelo caminho para suas bibliotecas.

1. Compile o arquivo `Main.java`.

    ```bash
    javac -cp ".;libs/*" Main.java
    ```

1. Execute o programa. Ele enviará a solicitação para a API de QnA Maker para criar a base de dados de conhecimento e, em seguida, sondará os resultados a cada 30 segundos. Cada resposta é impressa na janela de linha de comando.

    ```bash
    java -cp ".;libs/*" Main
    ```

## <a name="examine-the-response"></a>Examinar a resposta

Uma resposta com êxito é retornada em JSON. O aplicativo de exemplo analisa e exibe uma resposta bem-sucedida na janela do console, semelhante ao exemplo a seguir:

```json
REST Response:

{
  "metadata": {
    "width": 1826,
    "format": "Jpeg",
    "height": 2436
  },
  "color": {
    "dominantColorForeground": "Brown",
    "isBWImg": false,
    "accentColor": "B74314",
    "dominantColorBackground": "Brown",
    "dominantColors": ["Brown"]
  },
  "requestId": "bbffe1a1-4fa3-4a6b-a4d5-a4964c58a811",
  "description": {
    "captions": [{
      "confidence": 0.8241405091548035,
      "text": "a group of people on a city street filled with traffic at night"
    }],
    "tags": [
      "outdoor",
      "building",
      "street",
      "city",
      "busy",
      "people",
      "filled",
      "traffic",
      "many",
      "table",
      "car",
      "group",
      "walking",
      "bunch",
      "crowded",
      "large",
      "night",
      "light",
      "standing",
      "man",
      "tall",
      "umbrella",
      "riding",
      "sign",
      "crowd"
    ]
  },
  "categories": [{
    "score": 0.625,
    "name": "outdoor_street"
  }]
}
```

## <a name="next-steps"></a>Próximas etapas

Explore um aplicativo Swing Java que use Pesquisa Visual Computacional para executar OCR (reconhecimento óptico de caracteres), crie miniaturas com recorte inteligente e detecte, categorize, marque e descreva recursos visuais, incluindo rostos, em uma imagem. Para testar rapidamente a API da Pesquisa Visual Computacional, experimente o [Abrir o console de teste de API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Tutorial de Java da API de Pesquisa Visual Computacional](../Tutorials/java-tutorial.md)
