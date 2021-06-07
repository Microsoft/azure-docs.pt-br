---
author: areddish
ms.author: areddish
ms.service: cognitive-services
ms.date: 10/26/2020
ms.custom: devx-track-js
ms.openlocfilehash: b78f74ec190f4b871e127fbe4e6b7a0f10d058c1
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106113345"
---
Este guia fornece informações e um código de exemplo para ajudar você a começar a usar a biblioteca de clientes da Visão Personalizada para Node.js para criar um modelo de detecção de objetos. Você criará um projeto, adicionará marcas, treinará o projeto e usará a URL do ponto de extremidade de previsão do projeto para testá-lo programaticamente. Use este exemplo como um modelo para criar o próprio aplicativo de reconhecimento de imagem.

> [!NOTE]
> Se desejar criar e treinar um modelo de detecção de objetos _sem_ escrever código, confira as [diretrizes baseadas em navegador](../../get-started-build-detector.md).

Use a biblioteca de clientes da Visão Personalizada para .NET para:

* Criar um novo projeto de Visão Personalizada
* Adicionar marcas ao projeto
* Carregar e marcar imagens
* Treinar o projeto
* Publicar a iteração atual
* Testar o ponto de extremidade de previsão

Documentação de referência [(treinamento)](/javascript/api/@azure/cognitiveservices-customvision-training/) [(previsão)](/javascript/api/@azure/cognitiveservices-customvision-prediction/) | Código-fonte de biblioteca [(treinamento)](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-customvision-training) [(previsão)](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-customvision-prediction) | Pacote (npm) [(treinamento)](https://www.npmjs.com/package/@azure/cognitiveservices-customvision-training) [(previsão)](https://www.npmjs.com/package/@azure/cognitiveservices-customvision-prediction) | [Exemplos](/samples/browse/?products=azure&terms=custom%20vision&languages=javascript)


## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure – [Criar uma gratuitamente](https://azure.microsoft.com/free/cognitive-services/)
* A versão atual do [Node.js](https://nodejs.org/)
* Após obter a assinatura do Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesCustomVision"  title="Criar um recurso da Visão Personalizada"  target="_blank">crie um recurso da Visão Personalizada </a> no portal do Azure para criar um recurso de treinamento e previsão e obter as chaves e o ponto de extremidade. Aguarde até que ele seja implantado e clique no botão **Ir para o recurso**.
    * Você precisará da chave e do ponto de extremidade dos recursos criados para conectar seu aplicativo à Visão Personalizada. Cole a chave e o ponto de extremidade no código abaixo mais adiante no guia de início rápido.
    * Use o tipo de preço gratuito (`F0`) para experimentar o serviço e atualizar mais tarde para um nível pago para produção.

## <a name="setting-up"></a>Configurando

### <a name="create-a-new-nodejs-application"></a>Criar um novo aplicativo do Node.js

Em uma janela de console (como cmd, PowerShell ou Bash), crie um novo diretório para seu aplicativo e navegue até ele. 

```console
mkdir myapp && cd myapp
```

Execute o comando `npm init` para criar um aplicativo do Node com um arquivo `package.json`. 

```console
npm init
```

### <a name="install-the-client-library"></a>Instalar a biblioteca de clientes

Para escrever um aplicativo de análise de imagem com a Visão Personalizada para Node.js, você precisará dos pacotes NPM de Visão Personalizada. Para instalá-los, execute o seguinte comando no PowerShell:

```shell
npm install @azure/cognitiveservices-customvision-training
npm install @azure/cognitiveservices-customvision-prediction
```

O arquivo `package.json` do seu aplicativo será atualizado com as dependências.

Crie um arquivo chamado `index.js` e importe as seguintes bibliotecas:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_imports)]

> [!TIP]
> Deseja exibir todo o arquivo de código do início rápido de uma vez? Você pode encontrá-lo no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js), que contém os exemplos de código neste início rápido.

Crie variáveis para as chaves e o ponto de extremidade do Azure do recurso. 

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_creds)]

> [!IMPORTANT]
> Acesse o portal do Azure. Se os recursos da Visão Personalizada que você criou na seção **Pré-requisitos** tiverem sido implantados com êxito, clique no botão **Acessar o Recurso** em **Próximas Etapas**. Encontre as chaves e o ponto de extremidade nas páginas de **chave e ponto de extremidade** dos recursos, em **gerenciamento de recursos**. Você precisará obter as chaves para os recursos de treinamento e de previsão, juntamente com o ponto de extremidade de API para seu recurso de treinamento.
>
> Lembre-se de remover a chave do seu código quando terminar e nunca poste-a publicamente. Para produção, considere o uso de uma maneira segura de armazenar e acessar suas credenciais. Confira o artigo [segurança](../../../../cognitive-services/cognitive-services-security.md) de Serviços Cognitivos para obter mais informações.

Além disso, adicione campos para o nome do projeto e um parâmetro de tempo limite para chamadas assíncronas.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_vars)]

## <a name="object-model"></a>Modelo de objeto

|Nome|Descrição|
|---|---|
|[TrainingAPIClient](/javascript/api/@azure/cognitiveservices-customvision-training/trainingapiclient) | Essa classe lida com a criação, o treinamento e a publicação de seus modelos. |
|[PredictionAPIClient](/javascript/api/@azure/cognitiveservices-customvision-prediction/predictionapiclient)| Essa classe lida com a consulta de seus modelos para obter previsões de detecção de objetos.|
|[Previsão](/javascript/api/@azure/cognitiveservices-customvision-prediction/prediction)| Essa interface define uma previsão em uma imagem. Ela inclui propriedades para a ID e o nome do objeto e uma pontuação de confiança.|

## <a name="code-examples"></a>Exemplos de código

Estes snippets de código mostram como realizar as seguintes tarefas com a biblioteca de clientes da Visão Personalizada para JavaScript:

* [Autenticar o cliente](#authenticate-the-client)
* [Criar um novo projeto de Visão Personalizada](#create-a-new-custom-vision-project)
* [Adicionar marcas ao projeto](#add-tags-to-the-project)
* [Carregar e marcar imagens](#upload-and-tag-images)
* [Treinar o projeto](#train-the-project)
* [Publicar a iteração atual](#publish-the-current-iteration)
* [Testar o ponto de extremidade de previsão](#test-the-prediction-endpoint)

## <a name="authenticate-the-client"></a>Autenticar o cliente

Crie uma instância de objetos do cliente com o ponto de extremidade e a chave. Crie um objeto **ApiKeyCredentials** com a sua chave e use-o com o ponto de extremidade para criar um objeto [TrainingAPIClient](/javascript/api/@azure/cognitiveservices-customvision-training/trainingapiclient) e [PredictionAPIClient](/javascript/api/@azure/cognitiveservices-customvision-prediction/predictionapiclient).

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_auth)]

## <a name="add-helper-function"></a>Adicionar função auxiliar

Adicione a função a seguir para ajudar a fazer várias chamadas assíncronas. Você usará isso posteriormente.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_auth)]

## <a name="create-a-new-custom-vision-project"></a>Criar um novo projeto de Visão Personalizada

Inicie uma nova função para conter todas as suas chamadas de função de Visão Personalizada. Adicione o código a seguir para criar um projeto do serviço de Visão Personalizada.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_create)]

## <a name="upload-and-tag-images"></a>Carregar e marcar imagens

Primeiro, baixe as imagens de exemplo para este projeto. Salve o conteúdo da [pasta de imagens de exemplo](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/CustomVision/ObjectDetection/Images) em seu dispositivo local.

> [!NOTE]
> Você precisa de um conjunto mais amplo de imagens para concluir o treinamento? O Trove, um projeto da Microsoft Garage, permite coletar e comprar conjuntos de imagens para fins de treinamento. Depois de coletar suas imagens, você pode baixá-las e, em seguida, importá-las para seu projeto de Visão Personalizada da maneira usual. Visite a [página do Trove](https://www.microsoft.com/ai/trove?activetab=pivot1:primaryr3) para saber mais.

Para adicionar imagens de exemplo ao projeto, insira o código a seguir após a criação da marca. Esse código carrega cada imagem com sua marca correspondente. Ao marcar imagens em projetos de detecção de objeto, você precisa especificar a região de cada objeto marcado usando coordenadas normalizadas. Neste tutorial, as regiões são embutidas no código. As regiões de especificam a caixa delimitadora em coordenadas normalizadas e as coordenadas são fornecidas na ordem: esquerda, superior, largura e altura. Você pode carregar até 64 imagens em um único lote.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_upload)]


> [!IMPORTANT]
> Você precisará alterar o caminho para as imagens (`sampleDataRoot`) com base na localização em que baixou o repositório Amostras do SDK do Python dos Serviços Cognitivos.

> [!NOTE]
> Se você não tiver um utilitário do tipo "clicar e arrastar" para marcar as coordenadas das regiões, use a interface do usuário da Web em [Customvision.ai](https://www.customvision.ai/). Neste exemplo, as coordenadas já foram fornecidas.


## <a name="train-the-project"></a>Treinar o projeto

Este código cria a primeira iteração do modelo de previsão. 

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_train)]


## <a name="publish-the-current-iteration"></a>Publicar a iteração atual

Esse código publica a iteração treinada no ponto de extremidade de previsão. O nome dado à iteração publicada pode ser usado para enviar solicitações de previsão. Uma iteração não fica disponível no ponto de extremidade de previsão até ser publicada.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_publish)]


## <a name="test-the-prediction-endpoint"></a>Testar o ponto de extremidade de previsão

Para enviar uma imagem para o ponto de extremidade de previsão e recuperar a previsão, adicione o código a seguir à sua função. 

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_test)]

Em seguida, feche a função Visão Personalizada e chame-a.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_function_close)]

## <a name="run-the-application"></a>Executar o aplicativo

Execute o aplicativo com o comando `node` no seu arquivo de início rápido.

```shell
node index.js
```

A saída do aplicativo deve aparecer no console. Em seguida, você pode verificar se a imagem de teste (encontrada em **<sampleDataRoot>/Test/** ) foi marcada corretamente e se a região da detecção está correta. Você pode também voltar para o [site da Visão Personalizada](https://customvision.ai) e ver o estado atual do projeto recém-criado.

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [clean-od-project](../../includes/clean-od-project.md)]

## <a name="next-steps"></a>Próximas etapas

Agora, você concluiu cada etapa do processo de detecção de objetos no código. Este exemplo executa uma iteração de treinamento única, mas muitas vezes você precisará treinar e testar o modelo várias vezes para torná-lo mais preciso. O guia a seguir lida com a classificação de imagens, mas seus princípios são semelhantes aos da detecção de objetos.

> [!div class="nextstepaction"]
> [Testar e readaptar um modelo](../../test-your-model.md)

* O que é Visão Personalizada?
* O código-fonte dessa amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js)
* [Documentação de referência do SDK (treinamento)](/javascript/api/@azure/cognitiveservices-customvision-training/)
* [Documentação de referência do SDK (previsão)](/javascript/api/@azure/cognitiveservices-customvision-prediction/)
