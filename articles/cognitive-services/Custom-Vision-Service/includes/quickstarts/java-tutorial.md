---
author: PatrickFarley
ms.custom: devx-track-java
ms.author: pafarley
ms.service: cognitive-services
ms.date: 10/13/2020
ms.openlocfilehash: c169572e8af8ba992e604fc51b31db1146f11fda
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106113406"
---
Comece a usar a biblioteca de clientes da Visão Personalizada para Java a fim de criar um modelo de classificação de imagem. Siga essas etapas para instalar o pacote e testar o código de exemplo para tarefas básicas. Use este exemplo como um modelo para criar o próprio aplicativo de reconhecimento de imagem.

> [!NOTE]
> Se desejar criar e treinar um modelo de classificação _sem_ escrever código, confira as [diretrizes baseadas em navegador](../../getting-started-build-a-classifier.md).

Use a biblioteca de clientes da Visão Personalizada para Java a fim de:

* Criar um novo projeto de Visão Personalizada
* Adicionar marcas ao projeto
* Carregar e marcar imagens
* Treinar o projeto
* Publicar a iteração atual
* Testar o ponto de extremidade de previsão

[Documentação de referência](/java/api/overview/azure/cognitiveservices/client/customvision) | Código-fonte da biblioteca [(treinamento)](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cognitiveservices/ms-azure-cs-customvision-training) [(previsão)](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cognitiveservices/ms-azure-cs-customvision-prediction) | Artifact (Maven) [(treinamento)](https://search.maven.org/artifact/com.azure/azure-cognitiveservices-customvision-training/1.1.0-preview.2/jar) [(previsão)](https://search.maven.org/artifact/com.azure/azure-cognitiveservices-customvision-prediction/1.1.0-preview.2/jar) | 
[Exemplos](/samples/browse/?products=azure&terms=custom%20vision)

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure – [crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services/)
* A versão atual do [JDK (Java Development Kit)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* A [ferramenta de build Gradle](https://gradle.org/install/) ou outro gerenciador de dependência.
* Após obter a assinatura do Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesCustomVision"  title="Criar um recurso da Visão Personalizada"  target="_blank">crie um recurso da Visão Personalizada </a> no portal do Azure para criar um recurso de treinamento e previsão e obter as chaves e o ponto de extremidade. Aguarde até que ele seja implantado e clique no botão **Ir para o recurso**.
    * Você precisará da chave e do ponto de extremidade dos recursos criados para conectar seu aplicativo à Visão Personalizada. Cole a chave e o ponto de extremidade no código abaixo mais adiante no guia de início rápido.
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

Localize o *build.gradle.kts* e abra-o com seu IDE ou editor de texto preferencial. Depois copie nessa configuração de build a seguir. Esta configuração define o projeto como um aplicativo Java cujo ponto de entrada é a classe **CustomVisionQuickstart**. Ela importa as bibliotecas da Visão Personalizada.

```kotlin
plugins {
    java
    application
}
application { 
    mainClassName = "CustomVisionQuickstart"
}
repositories {
    mavenCentral()
}
dependencies {
    compile(group = "com.azure", name = "azure-cognitiveservices-customvision-training", version = "1.1.0-preview.2")
    compile(group = "com.azure", name = "azure-cognitiveservices-customvision-prediction", version = "1.1.0-preview.2")
}
```

### <a name="create-a-java-file"></a>Criar um arquivo Java


Em seu diretório de trabalho, execute o comando a seguir para criar uma pasta de origem do projeto:

```console
mkdir -p src/main/java
```

Navegue até a nova pasta e crie um arquivo chamado *CustomVisionQuickstart.java*. Abra-a no editor ou IDE de sua preferência e adicione as seguintes instruções `import`:

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_imports)]

> [!TIP]
> Deseja exibir todo o arquivo de código do início rápido de uma vez? Você pode encontrá-lo no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java), que contém os exemplos de código neste início rápido.


Na classe **CustomVisionQuickstart** do aplicativo, crie variáveis para as chaves e o ponto de extremidade do recurso.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_creds)]


> [!IMPORTANT]
> Acesse o portal do Azure. Se os recursos da Visão Personalizada que você criou na seção **Pré-requisitos** tiverem sido implantados com êxito, clique no botão **Acessar o Recurso** em **Próximas Etapas**. Encontre as chaves e o ponto de extremidade nas páginas de **chave e ponto de extremidade** dos recursos. Você precisará obter as chaves para os recursos de treinamento e de previsão, juntamente com o ponto de extremidade de API para seu recurso de treinamento.
>
> Encontre o valor da ID de recurso de previsão na guia **Propriedades** do recurso, listada como **ID da Assinatura**.
> 
> Lembre-se de remover a chave do seu código quando terminar e nunca poste-a publicamente. Para produção, considere o uso de uma maneira segura de armazenar e acessar suas credenciais. Confira o artigo [segurança](../../../cognitive-services-security.md) de Serviços Cognitivos para obter mais informações.

No método **main** do aplicativo, adicione chamadas para os métodos usados neste guia de início rápido. Você os definirá mais tarde.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_maincalls)]

## <a name="object-model"></a>Modelo de objeto

As classes e interfaces a seguir lidam com alguns dos principais recursos da biblioteca de clientes do Java da Visão Personalizada.

|Nome|Descrição|
|---|---|
|[CustomVisionTrainingClient](/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclient) | Essa classe lida com a criação, o treinamento e a publicação de seus modelos. |
|[CustomVisionPredictionClient](/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.prediction.customvisionpredictionclient)| Essa classe lida com a consulta de seus modelos para obter previsões de classificação de imagem.|
|[ImagePrediction](/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.prediction.models.imageprediction)| Essa classe define uma previsão em uma imagem. Ela inclui propriedades para a ID e o nome do objeto e uma pontuação de confiança.|

## <a name="code-examples"></a>Exemplos de código

Estes snippets de código mostram como realizar as seguintes tarefas com a biblioteca de clientes da Visão Personalizada para Java:

* [Autenticar o cliente](#authenticate-the-client)
* [Criar um novo projeto de Visão Personalizada](#create-a-new-custom-vision-project)
* [Adicionar marcas ao projeto](#add-tags-to-the-project)
* [Carregar e marcar imagens](#upload-and-tag-images)
* [Treinar o projeto](#train-the-project)
* [Publicar a iteração atual](#publish-the-current-iteration)
* [Testar o ponto de extremidade de previsão](#test-the-prediction-endpoint)

## <a name="authenticate-the-client"></a>Autenticar o cliente

No método **main** crie uma instância de clientes de treinamento e previsão usando o ponto de extremidade e as chaves.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_auth)]


## <a name="create-a-custom-vision-project"></a>Criar um projeto de Visão Personalizada

T## Criar um projeto de Visão Personalizada

O próximo método cria um projeto de classificação de imagem. O projeto criado será exibido no [site da Visão Personalizada](https://customvision.ai/) visitado anteriormente. Confira as sobrecargas do método [CreateProject](/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.training.trainings.createproject#com_microsoft_azure_cognitiveservices_vision_customvision_training_Trainings_createProject_String_CreateProjectOptionalParameter_&preserve-view=true) para especificar outras opções ao criar seu projeto (explicado no guia do portal da Web [Criar um detector](../../get-started-build-detector.md)).

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_create)]

## <a name="add-tags-to-your-project"></a>Adicionar marcas ao seu projeto

Esse método define as marcas em que você treinará o modelo.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_tags)]

## <a name="upload-and-tag-images"></a>Carregar e marcar imagens

Primeiro, baixe as imagens de exemplo para este projeto. Salve o conteúdo da [pasta de imagens de exemplo](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/CustomVision/ImageClassification/Images) em seu dispositivo local.

> [!NOTE]
> Você precisa de um conjunto mais amplo de imagens para concluir o treinamento? O Trove, um projeto da Microsoft Garage, permite coletar e comprar conjuntos de imagens para fins de treinamento. Depois de coletar suas imagens, você pode baixá-las e, em seguida, importá-las para seu projeto de Visão Personalizada da maneira usual. Visite a [página do Trove](https://www.microsoft.com/ai/trove?activetab=pivot1:primaryr3) para saber mais.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_upload)]

O snippet de código anterior faz uso de duas funções auxiliares que recuperam as imagens como fluxos de recurso e as carrega no serviço (você pode carregar até 64 imagens em um único lote).

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_helpers)]

## <a name="train-the-project"></a>Treinar o projeto

Esse método cria a primeira iteração de treinamento no projeto. Ele consulta o serviço até que o treinamento seja concluído.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_train)]


## <a name="publish-the-current-iteration"></a>Publicar a iteração atual

Esse método disponibiliza a iteração atual do modelo para consulta. Você pode usar o nome do modelo como uma referência para enviar solicitações de previsão. Você precisa inserir o seu valor para `predictionResourceId`. Encontre a ID de recurso de previsão na guia **Visão geral** do recurso no portal do Azure, listada como **ID da Assinatura**.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_publish)]

## <a name="test-the-prediction-endpoint"></a>Testar o ponto de extremidade de previsão

Este método carrega a imagem de teste, consulta o ponto de extremidade do modelo e gera dados de previsão para o console.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_predict)]

## <a name="run-the-application"></a>Executar o aplicativo

É possível criar o aplicativo com:

```console
gradle build
```

Execute o aplicativo com o comando `gradle run`:

```console
gradle run
```

## <a name="clean-up-resources"></a>Limpar os recursos

Se quiser limpar e remover uma assinatura dos Serviços Cognitivos, você poderá excluir o recurso ou grupo de recursos. Excluir o grupo de recursos também exclui todos os recursos associados a ele.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

[!INCLUDE [clean-ic-project](../../includes/clean-ic-project.md)]

## <a name="next-steps"></a>Próximas etapas

Agora você viu como cada etapa do processo de classificação de imagem pode ser executada em código. Este exemplo executa uma iteração de treinamento única, mas muitas vezes você precisará treinar e testar o modelo várias vezes para torná-lo mais preciso.

> [!div class="nextstepaction"]
> [Testar e readaptar um modelo](../../test-your-model.md)

* O que é Visão Personalizada?
* O código-fonte dessa amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java)
