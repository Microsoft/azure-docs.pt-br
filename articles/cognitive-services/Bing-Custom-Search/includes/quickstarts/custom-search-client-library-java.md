---
title: Início rápido da biblioteca de clientes Java da Pesquisa Personalizada do Bing
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/27/2020
ms.custom: devx-track-java
ms.author: aahi
ms.openlocfilehash: 300c602a62b0d6b3ba579931b2222d2cd8667656
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98948200"
---
Introdução à biblioteca de clientes da Pesquisa Personalizada do Bing para Java. Siga essas etapas para instalar o pacote e testar o código de exemplo para tarefas básicas. A API de Pesquisa Personalizada do Bing permite que você crie experiências de pesquisa personalizada sem anúncios para os tópicos de seu interesse. O código-fonte dessa amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingCustomSearch)

Use a biblioteca de clientes da Pesquisa Personalizada do Bing para Java:

* Localizar os resultados da pesquisa na Web, na sua instância da Pesquisa Personalizada do Bing.

[Documentação de referência](/java/api/overview/azure/cognitiveservices/client/bingcustomsearch) | [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingCustomSearch) | [Artefato (Maven)](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customsearch/) | [Exemplos](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples)

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure – [crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services/).
* A versão atual do [JDK (Java Development Kit)](https://www.oracle.com/technetwork/java/javase/downloads/index.html).
* A [ferramenta de build Gradle](https://gradle.org/install/) ou outro gerenciador de dependência.
* Uma instância da Pesquisa Personalizada do Bing. Confira [Início Rápido: Criar sua primeira instância da Pesquisa Personalizada do Bing](../../quick-start.md) para obter mais informações.

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](~/includes/cognitive-services-bing-custom-search-signup-requirements.md)]

Depois de obter uma chave do recurso, [crie uma variável de ambiente](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) para a chave, chamada `AZURE_BING_CUSTOM_SEARCH_API_KEY`.

### <a name="create-a-new-gradle-project"></a>Criar um novo projeto Gradle

> [!TIP]
> Se você não estiver usando o Gradle, poderá encontrar os detalhes da biblioteca de clientes e para outros gerenciadores de dependência no [Repositório Central do Maven](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-textanalytics/).

Em uma janela de console (como cmd, PowerShell ou Bash), crie um novo diretório para seu aplicativo e navegue até ele.

```console
mkdir myapp && cd myapp
```

Execute o comando `gradle init` em seu diretório de trabalho. Esse comando cria arquivos de build essenciais para o Gradle, incluindo um arquivo *build.gradle.kts*, que é usado no runtime para configurar seu aplicativo.

```console
gradle init --type basic
```

Quando solicitado a escolher uma **DSL**, escolha **Kotlin**.

## <a name="install-the-client-library"></a>Instalar a biblioteca de clientes

Localize o *build.gradle.kts* e abra-o com seu IDE ou editor de texto preferencial. Depois copie nessa configuração de build. Inclua a biblioteca de clientes em `dependencies`:

```kotlin
plugins {
    java
    application
}
application {
    mainClassName = "main.java.BingCustomSearchSample"
}
repositories {
    mavenCentral()
}
dependencies {
    compile("org.slf4j:slf4j-simple:1.7.25")
    compile("com.microsoft.azure.cognitiveservices:azure-cognitiveservices-customsearch:1.0.2")
}
```

Crie uma pasta para seu aplicativo de exemplo. Do diretório de trabalho, execute o seguinte comando:

```console
mkdir src/main/java
```

Navegue até a nova pasta e crie um arquivo chamado *BingCustomSearchSample.java*. Abra-o e adicione as seguintes instruções `import`:


[!code-java[import statements](~/cognitive-services-java-sdk-samples/Search/BingCustomSearch/src/main/java/BingCustomSearchSample.java?name=imports)]

Crie uma classe denominada `BingCustomSearchSample`

```java
public class BingCustomSearchSample {
}
```

Na classe, crie um método `main` e uma variável para a chave do recurso. Se você tiver criado a variável de ambiente depois de iniciar o aplicativo, feche e reabra o editor, o IDE ou o shell em execução para acessar a variável. Você definirá os métodos mais tarde.

[!code-java[main method](~/cognitive-services-java-sdk-samples/Search/BingCustomSearch/src/main/java/BingCustomSearchSample.java?name=main)]

## <a name="object-model"></a>Modelo de objeto

O cliente da Pesquisa Personalizada do Bing é um objeto [BingCustomSearchAPI](/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchapi) criado no método [authenticate()](/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchmanager.authenticate) do objeto [BingCustomSearchManager](/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchmanager). Você pode enviar uma solicitação de pesquisa usando o método [BingCustomInstances.search()](/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustominstances.search#com_microsoft_azure_cognitiveservices_search_customsearch_BingCustomInstances_search__) do cliente.

A resposta da API é um objeto [SearchResponse](/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.models.searchresponse) que contém informações sobre a consulta de pesquisa e os resultados da pesquisa.

## <a name="code-examples"></a>Exemplos de código

Esses snippets de códigos mostram como realizar as seguintes tarefas com a biblioteca de clientes da Pesquisa Personalizada do Bing para Java:

* [Autenticar o cliente](#authenticate-the-client)
* [Obter resultados da pesquisa na instância de pesquisa personalizada](#get-search-results-from-your-custom-search-instance)

## <a name="authenticate-the-client"></a>Autenticar o cliente

O método principal deve incluir um objeto [BingCustomSearchManager](/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchapi) que usa a chave e chama seu `authenticate()`.

```java
BingCustomSearchAPI client = BingCustomSearchManager.authenticate(subscriptionKey);
```

## <a name="get-search-results-from-your-custom-search-instance"></a>Obter resultados da pesquisa na instância de pesquisa personalizada

Use a função [BingCustomInstances.search()](/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustominstances.search#com_microsoft_azure_cognitiveservices_search_customsearch_BingCustomInstances_search__) do cliente para enviar uma consulta de pesquisa para sua instância personalizada. Defina o `withCustomConfig` para sua ID de configuração personalizada ou por padrão como `1`. Depois de obter uma resposta da API, verifique se foram encontrados resultados da pesquisa. Nesse caso, obtenha o primeiro resultado da pesquisa chamando a função `webPages().value().get()` da resposta e imprima o nome do resultado e a URL.

[!code-java[call the custom search API](~/cognitive-services-java-sdk-samples/Search/BingCustomSearch/src/main/java/BingCustomSearchSample.java?name=runSample)]

## <a name="run-the-application"></a>Executar o aplicativo

Compile o aplicativo com o seguinte comando no diretório principal do projeto:

```console
gradle build
```

Execute o aplicativo com a meta `run`:

```console
gradle run
```

## <a name="clean-up-resources"></a>Limpar os recursos

Se quiser limpar e remover uma assinatura dos Serviços Cognitivos, você poderá excluir o recurso ou grupo de recursos. Excluir o grupo de recursos também exclui todos os recursos associados a ele.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar um aplicativo Web de Pesquisa Personalizada](../../tutorials/custom-search-web-page.md)
