---
title: Início rápido da biblioteca de clientes Java do Content Moderator
titleSuffix: Azure Cognitive Services
description: Neste início rápido, saiba como começar a usar a biblioteca de clientes do Content Moderator do Azure para Java. Insira software de filtragem de conteúdo em seu aplicativo para obedecer a regulamentos ou manter o ambiente pretendido para seus usuários.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: include
ms.date: 10/16/2020
ms.custom: devx-track-java, cog-serv-seo-aug-2020
ms.author: pafarley
ms.openlocfilehash: d1574d9728036bac393cbecb37e2097be6ef90e7
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105105805"
---
Introdução à biblioteca de clientes do Content Moderator do Azure para Java. Siga essas etapas para instalar o pacote do Maven e testar o código de exemplo para tarefas básicas. 

O Content Moderator é um serviço de IA que permite que você lide com conteúdos potencialmente ofensivos, arriscados ou, de alguma outra forma, indesejáveis. Use o serviço de moderação de conteúdo baseado em IA para digitalizar texto, imagem e vídeos e aplicar sinalizadores de conteúdo automaticamente. Insira software de filtragem de conteúdo em seu aplicativo para obedecer a regulamentos ou manter o ambiente pretendido para seus usuários.

Use a biblioteca de clientes do Content Moderator para Java para:

* Moderar texto
* Moderar imagens

[Documentação de referência](/java/api/overview/azure/cognitiveservices/client/contentmoderator) | [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cognitiveservices/ms-azure-cs-contentmoderator) |[Artefato (Maven)](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-contentmoderator) | [Exemplos](/samples/browse/?products=azure&term=content-moderator)

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure – [crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services/)
* A versão atual do [JDK (Java Development Kit)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* A [ferramenta de build Gradle](https://gradle.org/install/) ou outro gerenciador de dependência.
* Depois de obter uma assinatura do Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator"  title="Criar um recurso do Content Moderator"  target="_blank">crie um recurso do Content Moderator </a> no portal do Azure para obter uma chave e um ponto de extremidade. Aguarde até que ele seja implantado e clique no botão **Ir para o recurso**.
    * Será necessário obter uma chave e um ponto de extremidade do recurso criado para conectar o aplicativo ao Content Moderator. Cole a chave e o ponto de extremidade no código abaixo mais adiante no guia de início rápido.
    * Use o tipo de preço gratuito (`F0`) para experimentar o serviço e atualizar mais tarde para um nível pago para produção.

## <a name="setting-up"></a>Configurando

### <a name="create-a-new-gradle-project"></a>Criar um novo projeto Gradle

Em uma janela de console (como cmd, PowerShell ou Bash), crie um novo diretório para seu aplicativo e navegue até ele. 

```console
mkdir myapp && cd myapp
```

Execute o comando `gradle init` em seu diretório de trabalho. Esse comando criará arquivos de build essenciais para o Gradle, incluindo o *build.gradle.kts*, que é usado no runtime para criar e configurar seu aplicativo.

```console
gradle init --type basic
```

Quando solicitado a escolher uma **DSL**, escolha **Kotlin**.

## <a name="install-the-client-library"></a>Instalar a biblioteca de clientes

Localize o *build.gradle.kts* e abra-o com seu IDE ou editor de texto preferencial. Depois copie nessa configuração de build a seguir. Essa configuração define o projeto como um aplicativo Java cujo ponto de entrada é a classe **ContentModeratorQuickstart**. Ela importa a biblioteca de clientes do Content Moderator e o SDK do GSON para a serialização JSON.

```kotlin
plugins {
    java
    application
}

application{ 
    mainClassName = "ContentModeratorQuickstart"
}

repositories{
    mavenCentral()
}

dependencies{
    compile(group = "com.microsoft.azure.cognitiveservices", name = "azure-cognitiveservices-contentmoderator", version = "1.0.2-beta")
    compile(group = "com.google.code.gson", name = "gson", version = "2.8.5")
}
```

### <a name="create-a-java-file"></a>Criar um arquivo Java


Em seu diretório de trabalho, execute o comando a seguir para criar uma pasta de origem do projeto:

```console
mkdir -p src/main/java
```

Navegue até a nova pasta e crie um arquivo chamado *ContentModeratorQuickstart.java*. Abra-a no editor ou IDE de sua preferência e adicione as seguintes instruções `import`:

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imports)]

> [!TIP]
> Deseja exibir todo o arquivo de código do início rápido de uma vez? Você pode encontrá-lo no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java), que contém os exemplos de código neste início rápido.

Na classe **ContentModeratorQuickstart** do aplicativo, crie variáveis para a chave e o ponto de extremidade do recurso.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_creds)]

> [!IMPORTANT]
> Acesse o portal do Azure. Caso o recurso do Content Moderator criado na seção **Pré-requisitos** tenha sido implantado com êxito, clique no botão **Acessar o Recurso** em **Próximas Etapas**. Encontre a chave e o ponto de extremidade na página da **chave e do ponto de extremidade** do recurso, em **gerenciamento de recursos**. 
>
> Lembre-se de remover a chave do seu código quando terminar e nunca poste-a publicamente. Para produção, considere o uso de uma maneira segura de armazenar e acessar suas credenciais. Confira o artigo [segurança](../../../cognitive-services-security.md) de Serviços Cognitivos para obter mais informações.

No método **main** do aplicativo, adicione chamadas para os métodos usados neste guia de início rápido. Você os definirá mais tarde.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_maincalls)]


## <a name="object-model"></a>Modelo de objeto

As classes a seguir tratam de alguns dos principais recursos da biblioteca de clientes do Java do Content Moderator.

|Nome|Descrição|
|---|---|
|[ContentModeratorClient](/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.contentmoderatorclient)|Essa classe é necessária para toda a funcionalidade do Content Moderator. Você a instancia com suas informações de assinatura e a usa para produzir instâncias de outras classes.|
|[ImageModeration](/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.imagemoderations)|Essa classe fornece a funcionalidade para analisar imagens de conteúdo para adulto, informações pessoais ou rostos humanos.|
|[TextModerations](/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.textmoderations)|Essa classe fornece a funcionalidade para analisar o texto de idioma, profanação, erros e informações pessoais.|
|[Análises](/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.reviews)|Essa classe fornece a funcionalidade das APIs de Revisão, incluindo os métodos para criar trabalhos, fluxos de trabalho personalizados e revisões humanas.|


## <a name="code-examples"></a>Exemplos de código

Estes snippets de códigos mostram como realizar as seguintes tarefas com a biblioteca de clientes do Content Moderator para Java:

* [Autenticar o cliente](#authenticate-the-client)
* [Moderar texto](#moderate-text)
* [Moderar imagens](#moderate-images)


## <a name="authenticate-the-client"></a>Autenticar o cliente

No método `main` do aplicativo, crie um objeto [ContentModeratorClient](/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.contentmoderatorclient) usando o valor do ponto de extremidade da assinatura e a chave de assinatura.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_client)]



## <a name="moderate-text"></a>Moderar texto

### <a name="set-up-sample-text"></a>Configurar texto de exemplo

Na parte superior da classe **ContentModeratorQuickstart**, defina uma referência a um arquivo de texto local. Adicione um arquivo .txt ao diretório do projeto e insira o texto que você deseja analisar.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_textmod_var)]

### <a name="analyze-text"></a>Analisar o texto

Crie um método que leia o arquivo .txt e chame o método **screenText** em cada linha.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_textmod)]

### <a name="print-text-moderation-results"></a>Imprimir resultados de moderação de texto

Adicione o código a seguir para imprimir os resultados de moderação em um arquivo .JSON no diretório do projeto.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_textmod_print)]

Feche `try` e adicione uma instrução `catch` para concluir o método.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_textmod_catch)]


## <a name="moderate-images"></a>Moderar imagens

### <a name="set-up-sample-image"></a>Configurar imagem de exemplo

Em um novo método, crie um objeto **[BodyModelModel](/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.models.bodymodelmodel)** com determinada cadeia de caracteres que aponte para uma imagem.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod)]


### <a name="define-helper-class"></a>Definir a classe auxiliar

Em seguida, no seu arquivo *ContentModeratorQuickstart.java*, adicione a seguinte definição de classe dentro da classe **ContentModeratorQuickstart**. Essa classe interna é usada no processo de moderação da imagem.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_evaluationdata)]


### <a name="analyze-content"></a>Analisar conteúdo
Essa linha de código verifica a imagem na URL fornecida quanto a conteúdo para adulto ou erótico. Consulte o guia conceitual de moderação de imagem para obter informações sobre esses termos.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_ar)]

### <a name="check-for-text"></a>Verificar se há texto
Essa linha de código verifica a imagem quanto a texto visível.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_text)]

### <a name="check-for-faces"></a>Verificar se há rostos
Essa linha de código verifica a imagem quanto a rostos humanos.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_faces)]

Por fim, armazene as informações retornadas na lista `EvaluationData`.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_storedata)]

### <a name="print-results"></a>Imprimir resultados

Após o loop `while`, adicione o código a seguir, que imprime os resultados no console e em um arquivo de saída, *src/main/resources/ModerationOutput.json*.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_printdata)]

Feche a instrução `try` e adicione uma instrução `catch` para concluir o método.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_catch)]

## <a name="run-the-application"></a>Executar o aplicativo

É possível criar o aplicativo com:

```console
gradle build
```

Execute o aplicativo com o comando `gradle run`:

```console
gradle run
```

Em seguida, navegue até o arquivo *src/main/resources/ModerationOutput.json* e exiba os resultados da sua moderação de conteúdo.

## <a name="clean-up-resources"></a>Limpar os recursos

Se quiser limpar e remover uma assinatura dos Serviços Cognitivos, você poderá excluir o recurso ou grupo de recursos. Excluir o grupo de recursos também exclui todos os recursos associados a ele.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você aprendeu a usar a biblioteca Java do Content Moderator para realizar tarefas de moderação. Em seguida, saiba mais sobre a moderação de imagens ou outra mídia lendo um guia conceitual.

> [!div class="nextstepaction"]
> [Conceitos de moderação de imagem](../../image-moderation-api.md)
