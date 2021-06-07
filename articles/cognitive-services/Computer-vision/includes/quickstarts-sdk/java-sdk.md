---
title: 'Guia de início rápido: Biblioteca de clientes de reconhecimento óptico de caracteres para Java'
description: Neste guia de início rápido, comece a usar a biblioteca de clientes de reconhecimento óptico de caracteres para Java.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.custom: devx-track-java
ms.author: pafarley
ms.openlocfilehash: 84ac8e8309d9f1d0536d0f7a16ab9cd9f3c10a2c
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106284730"
---
<a name="HOLTop"></a>

Use a biblioteca de clientes de reconhecimento óptico de caracteres para ler textos impressos e manuscritos em imagens.

[Documentação de referência](/java/api/overview/azure/cognitiveservices/client/computervision) | [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cognitiveservices/ms-azure-cs-computervision) |[Artefato (Maven)](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-computervision) | [Exemplos](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure – [crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services/)
* A versão atual do [JDK (Java Development Kit)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* A [ferramenta de build Gradle](https://gradle.org/install/) ou outro gerenciador de dependência.
* Depois de obter sua assinatura do Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="Crie um recurso da Pesquisa Visual Computacional"  target="_blank">crie um recurso da Pesquisa Visual Computacional </a> no portal do Azure para obter a chave e o ponto de extremidade. Após a implantação, clique em **Ir para o recurso**.
    * Você precisará da chave e do ponto de extremidade do recurso criado para conectar seu aplicativo ao serviço de Pesquisa Visual Computacional. Cole a chave e o ponto de extremidade no código abaixo mais adiante no guia de início rápido.
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

### <a name="install-the-client-library"></a>Instalar a biblioteca de clientes

Este início rápido usa o gerenciador de dependência do Gradle. Você pode encontrar a biblioteca de clientes e informações para outros gerenciadores de dependência no [Repositório Central do Maven](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-computervision).

Localize o *build.gradle.kts* e abra-o com seu IDE ou editor de texto preferencial. Depois copie nessa configuração de build a seguir. Essa configuração define o projeto como um aplicativo Java cujo ponto de entrada é a classe **ComputerVisionQuickstarts**. Ela importa a biblioteca da Pesquisa Visual Computacional.

```kotlin
plugins {
    java
    application
}
application { 
    mainClassName = "ComputerVisionQuickstarts"
}
repositories {
    mavenCentral()
}
dependencies {
    compile(group = "com.microsoft.azure.cognitiveservices", name = "azure-cognitiveservices-computervision", version = "1.0.4-beta")
}
```

### <a name="create-a-java-file"></a>Criar um arquivo Java

Em seu diretório de trabalho, execute o comando a seguir para criar uma pasta de origem do projeto:

```console
mkdir -p src/main/java
```

> [!TIP]
> Deseja exibir todo o arquivo de código do início rápido de uma vez? Você pode encontrá-lo no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java), que contém os exemplos de código neste início rápido.

Navegue até a nova pasta e crie um arquivo chamado *ComputerVisionQuickstarts.java*. Abra-o no seu editor ou IDE preferido.

### <a name="find-the-subscription-key-and-endpoint"></a>Encontrar a chave de assinatura e o ponto de extremidade

Acesse o portal do Azure. Se o recurso da Pesquisa Visual Computacional criado na seção **Pré-requisitos** tiver sido implantado com êxito, clique no botão **Acessar o Recurso** em **Próximas Etapas**. Encontre a chave de assinatura e o ponto de extremidade na página **Chave e ponto de extremidade** do recurso em **Gerenciamento de recursos**. 

Defina a classe **ComputerVisionQuickstarts**. Crie variáveis para a chave de assinatura e o ponto de extremidade da Pesquisa Visual Computacional. Cole a chave de assinatura e o ponto de extremidade no código a seguir, quando indicado. O ponto de extremidade da Pesquisa Visual Computacional tem o formato `https://<your_computer_vision_resource_name>.cognitiveservices.azure.com/`.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_imports_and_vars)]

> [!IMPORTANT]
> Lembre-se de remover a chave de assinatura do código quando terminar e nunca a poste publicamente. Para produção, considere o uso de uma maneira segura de armazenar e acessar suas credenciais. Por exemplo, o [Azure Key Vault](../../../../key-vault/general/overview.md).

No método **main** do aplicativo, adicione chamadas para os métodos usados neste guia de início rápido. Você os definirá mais tarde.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_beginmain)]

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_authinmain)]

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_readinmain)]

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_endmain)]
> [!div class="nextstepaction"]
> [Configurei o cliente](?success=set-up-client#object-model) [Encontrei um problema](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Java&Section=set-up-client)

## <a name="object-model"></a>Modelo de objeto

As classes e as interfaces a seguir lidam com alguns dos principais recursos do SDK de OCR do Java.

|Nome|Descrição|
|---|---|
| [ComputerVisionClient](/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervisionclient) | Essa classe é necessária para toda a funcionalidade da Pesquisa Visual Computacional. Você a instancia com suas informações de assinatura e a usa para produzir instâncias de outras classes.|

## <a name="code-examples"></a>Exemplos de código

Estes snippets de código mostram como realizar as seguintes tarefas com a biblioteca de clientes de OCR para Java:

* [Autenticar o cliente](#authenticate-the-client)
* [Ler texto impresso e manuscrito](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>Autenticar o cliente

Em um novo método, crie uma instância do objeto [ComputerVisionClient](/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervisionclient) com o ponto de extremidade e a chave.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_auth)]

> [!div class="nextstepaction"]
> [Autentiquei o cliente](?success=authenticate-client#read-printed-and-handwritten-text) [Encontrei um problema](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Java&Section=authenticate-client)



## <a name="read-printed-and-handwritten-text"></a>Ler texto impresso e manuscrito

O serviço de OCR pode ler um texto visível em uma imagem e convertê-lo em um fluxo de caracteres. Esta seção define um método, `ReadFromFile`, que usa um caminho de arquivo local e imprime o texto da imagem no console.

> [!TIP]
> Também é possível ler texto em uma imagem remota referenciada pela URL. Confira os métodos de [ComputerVision](/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervision), como **read**. Ou veja o código de exemplo no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java) para ver cenários que envolvem imagens remotas.

### <a name="set-up-test-image"></a>Configurar imagem de teste

Crie uma pasta **resources/** na pasta **src/main/** do seu projeto e adicione uma imagem com base na qual você gostaria de ler o texto. Você pode baixar uma [imagem de amostra](https://raw.githubusercontent.com/MicrosoftDocs/azure-docs/master/articles/cognitive-services/Computer-vision/Images/readsample.jpg) para usar aqui.

Em seguida, adicione a seguinte definição de método à sua classe **ComputerVisionQuickstarts**. Altere o valor de `localFilePath` para corresponder ao arquivo de imagem. 

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_setup)]

### <a name="call-the-read-api"></a>Chamar a API de Leitura

Adicione o código a seguir para chamar o método **readInStreamWithServiceResponseAsync** para a imagem fornecida.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_call)]

O bloco de código a seguir extrai a ID da operação da resposta da chamada de leitura. Ele usa essa ID com um método auxiliar para imprimir os resultados de leitura de texto no console. 

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_response)]

Feche o bloco try/catch e a definição de método.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_catch)]

### <a name="get-read-results"></a>Obter os resultados da leitura

Em seguida, adicione uma definição para o método auxiliar. Esse método usa a ID da operação da etapa anterior para consultar a operação de leitura e obter os resultados do OCR quando eles estiverem disponíveis.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_result_helper_call)]

O restante do método analisa os resultados do OCR e os imprime no console.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_result_helper_print)]

Por fim, adicione o outro método auxiliar usado acima, que extrai a ID da operação da resposta inicial.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_opid_extract)]

> [!div class="nextstepaction"]
> [Li o texto](?success=read-printed-handwritten-text#run-the-application) [Encontrei um problema](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Java&Section=read-printed-handwritten-text)

## <a name="run-the-application"></a>Executar o aplicativo

É possível criar o aplicativo com:

```console
gradle build
```

Execute o aplicativo com o comando `gradle run`:

```console
gradle run
```

> [!div class="nextstepaction"]
> [Executei o aplicativo](?success=run-the-application#clean-up-resources) [Encontrei um problema](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Java&Section=run-the-application)

## <a name="clean-up-resources"></a>Limpar os recursos

Se quiser limpar e remover uma assinatura dos Serviços Cognitivos, você poderá excluir o recurso ou grupo de recursos. Excluir o grupo de recursos também exclui todos os recursos associados a ele.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

> [!div class="nextstepaction"]
> [Limpei os recursos](?success=clean-up-resources#next-steps) [Encontrei um problema](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Java&Section=clean-up-resources)

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você aprendeu a usar a biblioteca de OCR para Java a fim de realizar tarefas básicas. Em seguida, explore a documentação de referência para saber mais sobre a biblioteca.

> [!div class="nextstepaction"]
>[Referência do SDK do OCR (Java)](/java/api/overview/azure/cognitiveservices/client/computervision)


* [Visão geral de OCR](../../overview-ocr.md)
* O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java).
