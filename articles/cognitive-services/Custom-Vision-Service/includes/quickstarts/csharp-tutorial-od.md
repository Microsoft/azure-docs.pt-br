---
author: PatrickFarley
ms.author: pafarley
ms.service: cognitive-services
ms.date: 09/15/2020
ms.openlocfilehash: 2b14c6e679a25ad5f37a9cb10bce78616126990b
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106112912"
---
Introdução à biblioteca de clientes da Visão Personalizada para .NET. Siga estas etapas para instalar o pacote e experimentar o código de exemplo para criar um modelo de detecção de objetos. Você criará um projeto, adicionará marcas, treinará o projeto em imagens de exemplo e usará a URL do ponto de extremidade de previsão do projeto para testá-lo programaticamente. Use este exemplo como um modelo para criar o próprio aplicativo de reconhecimento de imagem.

> [!NOTE]
> Se desejar criar e treinar um modelo de detecção de objetos _sem_ escrever código, confira as [diretrizes baseadas em navegador](../../get-started-build-detector.md).

Use a biblioteca de clientes da Visão Personalizada para .NET para:

* Criar um novo projeto de Visão Personalizada
* Adicionar marcas ao projeto
* Carregar e marcar imagens
* Treinar o projeto
* Publicar a iteração atual
* Testar o ponto de extremidade de previsão

[Documentação de referência](/dotnet/api/overview/azure/cognitiveservices/client/customvision) | Código-fonte da biblioteca [(treinamento)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.CustomVision.Training) [(previsão)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.CustomVision.Prediction) | Pacote (NuGet) [(treinamento)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training/) [(previsão)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction/) | [Exemplos](/samples/browse/?products=azure&term=vision&terms=vision)

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure – [Criar uma gratuitamente](https://azure.microsoft.com/free/cognitive-services/)
* O [IDE do Visual Studio](https://visualstudio.microsoft.com/vs/) ou a versão atual do [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Após obter a assinatura do Azure, <a href="https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision"  title="Criar um recurso da Visão Personalizada"  target="_blank">crie um recurso da Visão Personalizada </a> no portal do Azure para criar um recurso de treinamento e previsão e obter as chaves e o ponto de extremidade. Aguarde até que ele seja implantado e clique no botão **Ir para o recurso**.
    * Você precisará da chave e do ponto de extremidade dos recursos criados para conectar seu aplicativo à Visão Personalizada. Cole a chave e o ponto de extremidade no código abaixo mais adiante no guia de início rápido.
    * Use o tipo de preço gratuito (`F0`) para experimentar o serviço e atualizar mais tarde para um nível pago para produção.

## <a name="setting-up"></a>Configurando

### <a name="create-a-new-c-application"></a>Criar um aplicativo em C#

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

Usando o Visual Studio, crie um aplicativo .NET Core. 

### <a name="install-the-client-library"></a>Instalar a biblioteca de clientes 

Depois de criar um projeto, instale a biblioteca de clientes clicando com o botão direito do mouse na solução do projeto no **Gerenciador de Soluções** e selecionando **Gerenciar Pacotes NuGet**. No gerenciador de pacotes aberto, selecione **Procurar**, marque **Incluir pré-lançamento** e pesquise `Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training` e `Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction`. Selecione a versão mais recente e escolha **Instalar**. 

#### <a name="cli"></a>[CLI](#tab/cli)

Em uma janela de console (como cmd, PowerShell ou Bash), use o comando `dotnet new` para criar um novo aplicativo do console com o nome `custom-vision-quickstart`. Esse comando cria um projeto simples em C# do tipo "Olá, Mundo" com um arquivo de origem único: *program.cs*. 

```console
dotnet new console -n custom-vision-quickstart
```

Altere o diretório para a pasta do aplicativo recém-criado. É possível criar o aplicativo com:

```console
dotnet build
```

A saída de compilação não deve conter nenhum aviso ou erro. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>Instalar a biblioteca de clientes 

No diretório do aplicativo, instale a biblioteca de clientes da Visão Personalizada para .NET com o seguinte comando:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training --version 2.0.0
dotnet add package Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction --version 2.0.0
```

---

> [!TIP]
> Deseja exibir todo o arquivo de código do início rápido de uma vez? Você pode encontrá-lo no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/CustomVision/ObjectDetection/Program.cs), que contém os exemplos de código neste início rápido.

No diretório do projeto, abra o arquivo *program.cs* e adicione as seguintes diretivas `using`:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_imports)]

No método **Main** do aplicativo, crie variáveis para a chave e o ponto de extremidade do recurso.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_creds)]

> [!IMPORTANT]
> Acesse o portal do Azure. Se os recursos da Visão Personalizada que você criou na seção **Pré-requisitos** tiverem sido implantados com êxito, clique no botão **Acessar o Recurso** em **Próximas Etapas**. Encontre as chaves e o ponto de extremidade nas páginas de **chave e ponto de extremidade** dos recursos, em **gerenciamento de recursos**. Você precisará obter as chaves para os recursos de treinamento e de previsão, juntamente com o ponto de extremidade de API para seu recurso de treinamento.
>
> Lembre-se de remover as chaves do código quando terminar e nunca poste-as publicamente. Para produção, considere o uso de uma maneira segura de armazenar e acessar suas credenciais. Confira o artigo [segurança](../../../cognitive-services-security.md) de Serviços Cognitivos para obter mais informações.

No método **Main** do aplicativo, adicione chamadas para os métodos usados neste guia de início rápido. Você os implementará mais tarde.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_maincalls)]

## <a name="object-model"></a>Modelo de objeto

|Nome|Descrição|
|---|---|
|[CustomVisionTrainingClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclient) | Essa classe lida com a criação, o treinamento e a publicação de seus modelos. |
|[CustomVisionPredictionClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.prediction.customvisionpredictionclient)| Essa classe lida com a consulta de seus modelos para obter previsões de detecção de objetos.|
|[PredictionModel](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.prediction.models.predictionmodel)| Essa classe define uma previsão de objeto em uma imagem. Ela inclui propriedades para a ID e o nome do objeto, a localização da caixa delimitadora do objeto e uma pontuação de confiança.|

## <a name="code-examples"></a>Exemplos de código

Estes snippets de código mostram como realizar as seguintes tarefas com a biblioteca de clientes da Visão Personalizada para .NET:

* [Autenticar o cliente](#authenticate-the-client)
* [Criar um novo projeto de Visão Personalizada](#create-a-new-custom-vision-project)
* [Adicionar marcas ao projeto](#add-tags-to-the-project)
* [Carregar e marcar imagens](#upload-and-tag-images)
* [Treinar o projeto](#train-the-project)
* [Publicar a iteração atual](#publish-the-current-iteration)
* [Testar o ponto de extremidade de previsão](#test-the-prediction-endpoint)

## <a name="authenticate-the-client"></a>Autenticar o cliente

Em um novo método, crie uma instância de clientes de treinamento e previsão usando o ponto de extremidade e as chaves.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_auth)]

## <a name="create-a-new-custom-vision-project"></a>Criar um novo projeto de Visão Personalizada

O próximo método cria um projeto de detecção de objeto. O projeto criado será exibido no [site da Visão Personalizada](https://customvision.ai/). Confira o método [CreateProject](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclientextensions.createproject#Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_CustomVisionTrainingClientExtensions_CreateProject_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_ICustomVisionTrainingClient_System_String_System_String_System_Nullable_System_Guid__System_String_System_Collections_Generic_IList_System_String__&preserve-view=true) para especificar outras opções ao criar seu projeto (explicado no guia do portal da Web [Criar um detector](../../get-started-build-detector.md)).  

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_create)]

## <a name="add-tags-to-the-project"></a>Adicionar marcas ao projeto

Esse método define as marcas em que você treinará o modelo.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_tags)]

## <a name="upload-and-tag-images"></a>Carregar e marcar imagens

Primeiro, baixe as imagens de exemplo para este projeto. Salve o conteúdo da [pasta de imagens de exemplo](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/CustomVision/ObjectDetection/Images) em seu dispositivo local.

> [!NOTE]
> Você precisa de um conjunto mais amplo de imagens para concluir o treinamento? O Trove, um projeto da Microsoft Garage, permite coletar e comprar conjuntos de imagens para fins de treinamento. Depois de coletar suas imagens, você pode baixá-las e, em seguida, importá-las para seu projeto de Visão Personalizada da maneira usual. Visite a [página do Trove](https://www.microsoft.com/ai/trove?activetab=pivot1:primaryr3) para saber mais.

Ao marcar imagens em projetos de detecção de objeto, você precisa especificar a região de cada objeto marcado usando coordenadas normalizadas. O código a seguir associa cada uma das imagens de exemplo à região marcada.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_upload_regions)]

> [!NOTE]
> Em seus projetos, se você não tiver um utilitário do tipo "clicar e arrastar" para marcar as coordenadas das regiões, use a interface do usuário da Web no [site da Web da Visão Personalizada](https://www.customvision.ai/). Neste exemplo, as coordenadas já foram fornecidas.

Em seguida, esse mapa de associações é usado para carregar cada imagem de exemplo com suas coordenadas de região. Você pode carregar até 64 imagens em um único lote. Talvez seja necessário alterar o valor `imagePath` para apontar para os locais de pasta corretos.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_upload)]

Neste ponto, você carregou todas as imagens de amostras e marcou cada uma (**bifurcação** ou **tesoura**) com um retângulo de pixel associado.

## <a name="train-the-project"></a>Treinar o projeto

Esse método cria a primeira iteração de treinamento no projeto. Ele consulta o serviço até que o treinamento seja concluído.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_train)]

> [!TIP]
> Fazer o treinamento com marcas selecionadas
>
> Opcionalmente, você pode treinar apenas um subconjunto de suas marcas aplicadas. Talvez seja interessante fazer isso se você ainda não tiver aplicado um número suficiente de determinadas marcas, mas tiver outras delas. Na chamada [TrainProject](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclientextensions.trainproject#Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_CustomVisionTrainingClientExtensions_TrainProject_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_ICustomVisionTrainingClient_System_Guid_System_String_System_Nullable_System_Int32__System_Nullable_System_Boolean__System_String_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_Models_TrainingParameters_&preserve-view=true), use o parâmetro *trainingParameters*. Construa um [TrainingParameters](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.models.trainingparameters) e defina a propriedade **SelectedTags** dele como uma lista de IDs das marcas que você deseja usar. O modelo será treinado para reconhecer apenas as marcas nessa lista.

## <a name="publish-the-current-iteration"></a>Publicar a iteração atual

Esse método disponibiliza a iteração atual do modelo para consulta. Você pode usar o nome do modelo como uma referência para enviar solicitações de previsão. Você precisa inserir o seu valor para `predictionResourceId`. Encontre a ID de recurso de previsão na guia **Visão geral** do recurso no portal do Azure, listada como **ID da Assinatura**.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_publish)]


## <a name="test-the-prediction-endpoint"></a>Testar o ponto de extremidade de previsão

Este método carrega a imagem de teste, consulta o ponto de extremidade do modelo e gera dados de previsão para o console.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ObjectDetection/Program.cs?name=snippet_prediction)]

## <a name="run-the-application"></a>Executar o aplicativo

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

Execute o aplicativo clicando no botão **Depurar** na parte superior da janela do IDE.

#### <a name="cli"></a>[CLI](#tab/cli)

Execute o aplicativo do seu diretório de aplicativo com o comando `dotnet run`.

```dotnet
dotnet run
```

---

Enquanto o aplicativo é executado, ele deve abrir uma janela do console e gravar a seguinte saída:

```console
Creating new project:
        Training
Done!

Making a prediction:
        fork: 98.2% [ 0.111609578, 0.184719115, 0.6607002, 0.6637112 ]
        scissors: 1.2% [ 0.112389535, 0.119195729, 0.658031344, 0.7023591 ]
```

Em seguida, você pode verificar se a imagem de teste (encontrada em **Images/Test/**) foi marcada corretamente e se a região da detecção está correta. Aqui você pode pressionar qualquer tecla para sair do aplicativo.

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [clean-od-project](../../includes/clean-od-project.md)]

## <a name="next-steps"></a>Próximas etapas

Agora, você concluiu cada etapa do processo de detecção de objetos no código. Este exemplo executa uma iteração de treinamento única, mas muitas vezes você precisará treinar e testar o modelo várias vezes para torná-lo mais preciso. O guia a seguir lida com a classificação de imagens, mas seus princípios são semelhantes aos da detecção de objetos.

> [!div class="nextstepaction"]
> [Testar e readaptar um modelo](../../test-your-model.md)

* O que é Visão Personalizada?
* O código-fonte dessa amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/CustomVision/ObjectDetection/Program.cs)
* [Documentação de referência do SDK](/dotnet/api/overview/azure/cognitiveservices/client/customvision)
