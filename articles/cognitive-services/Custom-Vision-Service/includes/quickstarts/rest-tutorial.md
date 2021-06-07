---
author: PatrickFarley
ms.author: pafarley
ms.service: cognitive-services
ms.date: 12/09/2020
ms.topic: include
ms.openlocfilehash: 269cae85c0f007c487f063b1f7ca7ea4dca15376
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105105823"
---
Introdução à API REST da Visão Personalizada. Siga estas etapas para chamar a API e criar um modelo de classificação de imagem. Você criará um projeto, adicionará marcas, treinará o projeto e usará a URL do ponto de extremidade de previsão do projeto para testá-lo programaticamente. Use este exemplo como um modelo para criar o próprio aplicativo de reconhecimento de imagem.

> [!NOTE]
> A Visão Personalizada é usada com mais facilidade por meio de um SDK de biblioteca de clientes ou por meio de [diretrizes baseadas em navegador](../../get-started-build-detector.md).

Use a biblioteca de clientes da Visão Personalizada para .NET para:

* Criar um novo projeto de Visão Personalizada
* Adicionar marcas ao projeto
* Carregar e marcar imagens
* Treinar o projeto
* Publicar a iteração atual
* Testar o ponto de extremidade de previsão

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure – [Criar uma gratuitamente](https://azure.microsoft.com/free/cognitive-services/)
* Após obter a assinatura do Azure, <a href="https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision"  title="Criar um recurso da Visão Personalizada"  target="_blank">crie um recurso da Visão Personalizada </a> no portal do Azure para criar um recurso de treinamento e previsão e obter as chaves e o ponto de extremidade. Aguarde até que ele seja implantado e clique no botão **Ir para o recurso**.
    * Você precisará da chave e do ponto de extremidade dos recursos criados para conectar seu aplicativo à Visão Personalizada. Cole a chave e o ponto de extremidade no código abaixo mais adiante no guia de início rápido.
    * Use o tipo de preço gratuito (`F0`) para experimentar o serviço e atualizar mais tarde para um nível pago para produção.
* [PowerShell versão 6.0+](/powershell/scripting/install/installing-powershell-core-on-windows) ou um aplicativo de linha de comando semelhante.


## <a name="create-a-new-custom-vision-project"></a>Criar um novo projeto de Visão Personalizada

Você usará um comando como o exibido a seguir para criar um projeto de classificação de imagem. O projeto criado será exibido no [site da Visão Personalizada](https://customvision.ai/).


:::code language="shell" source="~/cognitive-services-quickstart-code/curl/custom-vision/image-classifier.sh" ID="createproject":::

Copie o comando para um editor de texto e faça as seguintes alterações:

* Substitua `{subscription key}` pela sua chave de assinatura de Detecção Facial válida.
* Substitua `{endpoint}` pelo ponto de extremidade que corresponde à sua chave de assinatura.
   [!INCLUDE [subdomains-note](../../../../../includes/cognitive-services-custom-subdomains-note.md)]
* Substitua `{name}` pelo nome do projeto.
* Opcionalmente, defina outros parâmetros de URL para configurar o tipo de modelo que seu projeto usará. Confira a [API CreatProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeae) para obter opções.

Uma resposta JSON semelhante à seguinte será exibida. Salve o valor `"id"` do seu projeto em uma localização temporária.

```json
{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "string",
  "description": "string",
  "settings": {
    "domainId": "00000000-0000-0000-0000-000000000000",
    "classificationType": "Multiclass",
    "targetExportPlatforms": [
      "CoreML"
    ],
    "useNegativeSet": true,
    "detectionParameters": "string",
    "imageProcessingSettings": {
      "augmentationMethods": {}
    }
  },
  "created": "string",
  "lastModified": "string",
  "thumbnailUri": "string",
  "drModeEnabled": true,
  "status": "Succeeded"
}
```

## <a name="add-tags-to-the-project"></a>Adicionar marcas ao projeto

Use o comando a seguir para definir as marcas em que você treinará o modelo.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/custom-vision/image-classifier.sh" ID="createtag":::

* Novamente, insira sua chave e URL de ponto de extremidade.
* Substitua `{projectId}` pela ID do projeto.
* Substitua `{name}` pelo nome da marca que você deseja usar.

Repita esse processo para todas as marcas que você gostaria de usar em seu projeto. Se você estiver usando as imagens de exemplo fornecidas, adicione as marcas `"Hemlock"` e `"Japanese Cherry"`.

Uma resposta JSON semelhante à seguinte será mostrada. Salve o valor `"id"` de cada marca em uma localização temporária.

```json
{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "string",
  "description": "string",
  "type": "Regular",
  "imageCount": 0
}
```

## <a name="upload-and-tag-images"></a>Carregar e marcar imagens

Em seguida, baixe as imagens de exemplo para este projeto. Salve o conteúdo da [pasta de imagens de exemplo](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/CustomVision/ImageClassification/Images) em seu dispositivo local.

> [!NOTE]
> Você precisa de um conjunto mais amplo de imagens para concluir o treinamento? O Trove, um projeto da Microsoft Garage, permite coletar e comprar conjuntos de imagens para fins de treinamento. Depois de coletar suas imagens, você pode baixá-las e, em seguida, importá-las para seu projeto de Visão Personalizada da maneira usual. Visite a [página do Trove](https://www.microsoft.com/ai/trove?activetab=pivot1:primaryr3) para saber mais.

Use o comando a seguir para carregar as imagens e aplicar marcas; uma vez para as imagens "Cicuta" e separadamente para as imagens "Cerejeira". Confira a API [Criar imagens dos dados](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb5) para obter mais opções.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/custom-vision/image-classifier.sh" ID="uploadimages":::

* Novamente, insira sua chave e URL de ponto de extremidade.
* Substitua `{projectId}` pela ID do projeto.
* Substitua `{tagArray}` pela ID da marca.
* Em seguida, preencha o corpo da solicitação com os dados binários das imagens que você deseja marcar.

## <a name="train-the-project"></a>Treinar o projeto

Este método treina o modelo nas imagens marcadas que você carregou e retorna uma ID para a iteração do projeto atual.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/custom-vision/image-classifier.sh" ID="trainproject":::

* Novamente, insira sua chave e URL de ponto de extremidade.
* Substitua `{projectId}` pela ID do projeto.
* Substitua `{tagArray}` pela ID da marca.
* Em seguida, preencha o corpo da solicitação com os dados binários das imagens que você deseja marcar.
* Opcionalmente, use outros parâmetros de URL. Confira a API [Treinar Projeto](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc7548b571998fddee1) para obter opções.

> [!TIP]
> Fazer o treinamento com marcas selecionadas
>
> Opcionalmente, você pode treinar apenas um subconjunto de suas marcas aplicadas. Talvez seja interessante fazer isso se você ainda não tiver aplicado um número suficiente de determinadas marcas, mas tiver outras delas. Adicione o conteúdo JSON opcional ao corpo da solicitação. Preencha a matriz de `"selectedTags"` com as IDs das marcas que você deseja usar.
> ```json
> {
>   "selectedTags": [
>     "00000000-0000-0000-0000-000000000000"
>   ]
> }
> ```

A resposta JSON contém informações sobre seu projeto treinado, incluindo a ID de iteração (`"id"`). Salve esse valor para a próxima etapa.

```json
{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "string",
  "status": "string",
  "created": "string",
  "lastModified": "string",
  "trainedAt": "string",
  "projectId": "00000000-0000-0000-0000-000000000000",
  "exportable": true,
  "exportableTo": [
    "CoreML"
  ],
  "domainId": "00000000-0000-0000-0000-000000000000",
  "classificationType": "Multiclass",
  "trainingType": "Regular",
  "reservedBudgetInHours": 0,
  "trainingTimeInMinutes": 0,
  "publishName": "string",
  "originalPublishResourceId": "string"
}
```

## <a name="publish-the-current-iteration"></a>Publicar a iteração atual

Esse método disponibiliza a iteração atual do modelo para consulta. Use o nome do modelo como uma referência para enviar solicitações de previsão. 

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/custom-vision/image-classifier.sh" ID="publish":::

* Novamente, insira sua chave e URL de ponto de extremidade.
* Substitua `{projectId}` pela ID do projeto.
* Substitua `{iterationId}` pela ID retornada na etapa anterior.
* Substitua `{publishedName}` pelo nome que você deseja atribuir ao modelo de previsão.
* Substitua `{predictionId}` pela sua ID de recurso de previsão. Encontre-a na guia **Visão geral** do recurso de previsão no portal do Azure, listada como **ID da Assinatura**.
* Opcionalmente, use outros parâmetros de URL. Confira a API [Publicar Iteração](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc7548b571998fdded5).

## <a name="test-the-prediction-endpoint"></a>Testar o ponto de extremidade de previsão

Por fim, use este comando para testar seu modelo treinado carregando uma nova imagem para classificar com marcas. Você pode usar a imagem na pasta "Teste" dos arquivos de exemplo baixados anteriormente.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/custom-vision/image-classifier.sh" ID="publish":::

* Novamente, insira sua chave e URL de ponto de extremidade.
* Substitua `{projectId}` pela ID do projeto.
* Substitua `{publishedName}` pelo nome usado na etapa anterior.
* Adicione os dados binários de sua imagem local ao corpo da solicitação.
* Opcionalmente, use outros parâmetros de URL. Confira a API [Classificar Imagem](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.1/operations/5eb37d24548b571998fde5f3).

A resposta JSON retornada listará cada uma das marcas que o modelo aplicou à sua imagem, juntamente com pontuações de probabilidade para cada marca. 

```json
{
  "id": "00000000-0000-0000-0000-000000000000",
  "project": "00000000-0000-0000-0000-000000000000",
  "iteration": "00000000-0000-0000-0000-000000000000",
  "created": "string",
  "predictions": [
    {
      "probability": 0.0,
      "tagId": "00000000-0000-0000-0000-000000000000",
      "tagName": "string",
      "boundingBox": {
        "left": 0.0,
        "top": 0.0,
        "width": 0.0,
        "height": 0.0
      },
      "tagType": "Regular"
    }
  ]
}
```

[!INCLUDE [clean-ic-project](../../includes/clean-ic-project.md)]

## <a name="next-steps"></a>Próximas etapas

Agora, você concluiu cada etapa do processo de classificação de imagens usando a API REST. Este exemplo executa uma iteração de treinamento única, mas muitas vezes você precisará treinar e testar o modelo várias vezes para torná-lo mais preciso.

> [!div class="nextstepaction"]
> [Testar e readaptar um modelo](../../test-your-model.md)

* O que é Visão Personalizada?
* [Documentação de referência da API (treinamento)](/dotnet/api/overview/azure/cognitiveservices/client/customvision)
* [Documentação de referência da API (previsão)](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeae)