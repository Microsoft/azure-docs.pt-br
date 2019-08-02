---
title: 'Início Rápido: Converter script de texto, Java – API de Tradução de Texto'
titleSuffix: Azure Cognitive Services
description: Neste início rápido, você aprenderá a transliterar texto (converter) de um script para outro usando Java e a API REST de Tradução de Texto. Neste exemplo, o japonês é transliterado para usar o alfabeto latino.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: ee6ad0d98dd5fd45e7b32e80350fd9e3b7534f1e
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67704455"
---
# <a name="quickstart-use-the-translator-text-api-to-transliterate-text-using-java"></a>Início Rápido: Usar a API de Tradução de Texto para transliterar texto usando Java

Neste início rápido, você aprenderá a transliterar texto (converter) de um script para outro usando Java e a API REST de Tradução de Texto. No exemplo fornecido, o japonês é transliterado para usar o alfabeto latino.

Este início rápido requer uma [Conta dos Serviços Cognitivos do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) com um recurso de Tradução de Texto. Se não tiver uma conta, você poderá usar a [avaliação gratuita](https://azure.microsoft.com/try/cognitive-services/) para obter uma chave de assinatura.

>[!TIP]
> Caso queira ver todo o código de uma vez, o código-fonte deste exemplo está disponível no [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Java).

## <a name="prerequisites"></a>Pré-requisitos

* [JDK 7 ou posterior](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Gradle](https://gradle.org/install/)
* Uma chave de assinatura do Azure para a Tradução de Texto

## <a name="initialize-a-project-with-gradle"></a>Inicializar um projeto com Gradle

Vamos começar criando um diretório de trabalho para esse projeto. Na linha de comando (ou terminal), execute esse comando:

```console
mkdir transliterate-sample
cd transliterate-sample
```

Em seguida, você vai inicializar um projeto do Gradle. Esse comando criará arquivos de compilação essenciais para o Gradle, principalmente o `build.gradle.kts`, que é usado para criar e configurar seu aplicativo no tempo de execução. Execute este comando no diretório de trabalho:

```console
gradle init --type basic
```

Quando solicitado a escolher uma **DSL**, escolha **Kotlin**.

## <a name="configure-the-build-file"></a>Configure o arquivo de compilação

Localize `build.gradle.kts` e abra-o com seu editor de texto ou IDE favorito. Depois copie nessa configuração de build:

```
plugins {
    java
    application
}
application {
    mainClassName = "Transliterate"
}
repositories {
    mavenCentral()
}
dependencies {
    compile("com.squareup.okhttp:okhttp:2.5.0")
    compile("com.google.code.gson:gson:2.8.5")
}
```

Observe que esse exemplo tem dependências de OkHttp para solicitações HTTP e de Gson para manipular e analisar o JSON. Para saber mais sobre as configurações de compilação, confira [Criar novas builds de Gradle](https://guides.gradle.org/creating-new-gradle-builds/).

## <a name="create-a-java-file"></a>Criar um arquivo Java

Vamos criar uma pasta para seu aplicativo de exemplo. No diretório de trabalho, execute:

```console
mkdir -p src\main\java
```

Em seguida, nessa pasta, crie um arquivo chamado `Transliterate.java`.

## <a name="import-required-libraries"></a>Importe as bibliotecas necessárias

Abra `Transliterate.java` e adicione essas instruções de importação:

```java
import java.io.*;
import java.net.*;
import java.util.*;
import com.google.gson.*;
import com.squareup.okhttp.*;
```


## <a name="define-variables"></a>Definir variáveis

Primeiro, você precisa criar uma classe pública para seu projeto:

```java
public class Transliterate {
  // All project code goes here...
}
```

Adicione essas linhas à classe `Transliterate`. Você observará que, juntamente com a `api-version`, dois parâmetros adicionais foram anexados à `url`. Esses parâmetros são usados para definir o idioma de entrada e os scripts de transliteração. Neste exemplo, ele é definido como japonês (`jpan`) e latim (`latn`). Verifique se você atualizou o valor de chave de assinatura.

```java
String subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
String url = "https://api.cognitive.microsofttranslator.com/transliterate?api-version=3.0&language=ja&fromScript=jpan&toScript=latn";
```
Se estiver usando uma assinatura de vários serviço cognitivos, você também deve incluir o `Ocp-Apim-Subscription-Region` em seus parâmetros de solicitação. [Saiba mais sobre a autenticação com a assinatura de vários serviços](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="create-a-client-and-build-a-request"></a>Crie um cliente e compile uma solicitação

Adicione essa linha à classe `Transliterate` para instanciar o `OkHttpClient`:

```java
// Instantiates the OkHttpClient.
OkHttpClient client = new OkHttpClient();
```

Em seguida, vamos compilar a solicitação POST. Fique à vontade para alterar o texto para transliteração.

```java
// This function performs a POST request.
public String Post() throws IOException {
    MediaType mediaType = MediaType.parse("application/json");
    RequestBody body = RequestBody.create(mediaType,
            "[{\n\t\"Text\": \"こんにちは\"\n}]");
    Request request = new Request.Builder()
            .url(url).post(body)
            .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey)
            .addHeader("Content-type", "application/json").build();
    Response response = client.newCall(request).execute();
    return response.body().string();
}
```

## <a name="create-a-function-to-parse-the-response"></a>Criar uma função para analisar a resposta

Essa função simples analisa e melhora a aparência da resposta JSON no serviço de Tradução de Texto.

```java
// This function prettifies the json response.
public static String prettify(String json_text) {
    JsonParser parser = new JsonParser();
    JsonElement json = parser.parse(json_text);
    Gson gson = new GsonBuilder().setPrettyPrinting().create();
    return gson.toJson(json);
}
```

## <a name="put-it-all-together"></a>Colocar tudo isso junto

A última etapa é fazer uma solicitação e receber uma resposta. Adicione essas linhas ao projeto:

```java
public static void main(String[] args) {
    try {
        Transliterate transliterateRequest = new Transliterate();
        String response = transliterateRequest.Post();
        System.out.println(prettify(response));
    } catch (Exception e) {
        System.out.println(e);
    }
}
```

## <a name="run-the-sample-app"></a>Executar o aplicativo de exemplo

E, pronto, você já pode executar seu aplicativo de exemplo. Na linha de comando (ou sessão de terminal), navegue até a raiz do diretório de trabalho e execute:

```console
gradle build
```

Quando o build for concluído, execute:

```console
gradle run
```

## <a name="sample-response"></a>Resposta de exemplo

```json
[
  {
    "text": "konnichiwa",
    "script": "latn"
  }
]
```

## <a name="next-steps"></a>Próximas etapas

Confira a referência da API para saber tudo o que você pode fazer com a API de Tradução de Texto.

> [!div class="nextstepaction"]
> [Referência de API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)

## <a name="see-also"></a>Consulte também

* [Traduzir o texto](quickstart-java-translate.md)
* [Identificar a linguagem pela entrada](quickstart-java-detect.md)
* [Obter traduções alternativas](quickstart-java-dictionary.md)
* [Obter uma lista de idiomas com suporte](quickstart-java-languages.md)
* [Determinar o comprimento da frase em uma entrada](quickstart-java-sentences.md)
