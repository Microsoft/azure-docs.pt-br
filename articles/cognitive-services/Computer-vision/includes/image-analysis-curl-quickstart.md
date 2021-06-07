---
title: 'Guia de início rápido: API REST de Análise de Imagem'
titleSuffix: Azure Cognitive Services
description: Neste guia de início rápido, comece a usar a API REST de Análise de Imagem.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 12/02/2020
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 77958746487ffbcf19ad14be71818c59e9520374
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106287362"
---
Use a API REST de Análise de Imagem para:

* Analisar uma imagem quanto a marcas, descrição de texto, rostos, conteúdo para adulto e muito mais.
* Gerar uma miniatura com corte inteligente

> [!NOTE]
> Este guia de início rápido usa comandos cURL para chamar a API REST. Você também pode chamar a API REST usando uma linguagem de programação. Confira as amostras do GitHub para obter exemplos em [C#](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/ComputerVision/REST), [Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/python/ComputerVision/REST), [Java](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/java/ComputerVision/REST), [JavaScript](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/javascript/ComputerVision/REST) e [Go](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/go/ComputerVision/REST).

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure – [crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services/) 
* Depois de obter sua assinatura do Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="Crie um recurso da Pesquisa Visual Computacional"  target="_blank">crie um recurso da Pesquisa Visual Computacional </a> no portal do Azure para obter a chave e o ponto de extremidade. Após a implantação, clique em **Ir para o recurso**.
  * Você precisará da chave e do ponto de extremidade do recurso criado para conectar seu aplicativo ao serviço de Pesquisa Visual Computacional. Cole a chave e o ponto de extremidade no código abaixo mais adiante no guia de início rápido.
  * Use o tipo de preço gratuito (`F0`) para experimentar o serviço e atualizar mais tarde para um nível pago para produção.
* [cURL](https://curl.haxx.se/) instalado

## <a name="analyze-an-image"></a>Analisar uma imagem

Para analisar uma imagem a fim de obter uma variedade de recursos visuais, execute as seguintes etapas:

1. Copie o seguinte comando em um editor de texto.
1. Faça as alterações a seguir no comando quando necessário:
    1. Substitua o valor de `<subscriptionKey>` pela sua chave de assinatura.
    1. Substitua a primeira parte da URL de solicitação (`westcentralus`) pelo texto em sua URL de ponto de extremidade.
        [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]
    1. Outra opção é alterar a URL da imagem no corpo da solicitação (`http://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg\`) para uma URL de uma imagem diferente a ser analisada.
1. Abra una janela de prompt de comando.
1. Cole o comando do editor de texto na janela do prompt de comando e, em seguida, execute-o.

```bash
curl -H "Ocp-Apim-Subscription-Key: <subscriptionKey>" -H "Content-Type: application/json" "https://westcentralus.api.cognitive.microsoft.com/vision/v3.1/analyze?visualFeatures=Categories,Description&details=Landmarks" -d "{\"url\":\"http://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg\"}"
```

### <a name="examine-the-response"></a>Examinar a resposta

Uma resposta com êxito é retornada em JSON. O aplicativo de exemplo analisa e exibe uma resposta bem-sucedida na janela do prompt de comando, semelhante ao exemplo a seguir:

```json
{
  "categories": [
    {
      "name": "outdoor_water",
      "score": 0.9921875,
      "detail": {
        "landmarks": []
      }
    }
  ],
  "description": {
    "tags": [
      "nature",
      "water",
      "waterfall",
      "outdoor",
      "rock",
      "mountain",
      "rocky",
      "grass",
      "hill",
      "covered",
      "hillside",
      "standing",
      "side",
      "group",
      "walking",
      "white",
      "man",
      "large",
      "snow",
      "grazing",
      "forest",
      "slope",
      "herd",
      "river",
      "giraffe",
      "field"
    ],
    "captions": [
      {
        "text": "a large waterfall over a rocky cliff",
        "confidence": 0.916458423253597
      }
    ]
  },
  "requestId": "b6e33879-abb2-43a0-a96e-02cb5ae0b795",
  "metadata": {
    "height": 959,
    "width": 1280,
    "format": "Jpeg"
  }
}
```


## <a name="generate-a-thumbnail"></a>Gerar uma miniatura

Você pode usar a Análise de Imagem para gerar uma miniatura com o corte inteligente. Você especifica a altura e a largura desejada, que podem diferir da imagem de entrada quanto à taxa de proporção. A Análise de Imagem usa o corte inteligente para identificar a área de interesse de modo inteligente e gerar as coordenadas de corte em torno dessa região.
 
Para criar e executar o exemplo, siga estas etapas:

1. Copie o seguinte comando em um editor de texto.
1. Faça as alterações a seguir no comando quando necessário:
    1. Substitua o valor de `<subscriptionKey>` pela sua chave de assinatura.
    1. Substitua o valor de `<thumbnailFile>` pelo caminho e o nome do arquivo no qual salvar a imagem de miniatura retornada.
    1. Substitua a primeira parte da URL de solicitação (`westcentralus`) pelo texto em sua URL de ponto de extremidade.
        [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]
    1. Opcionalmente, altere a URL da imagem no corpo da solicitação (`https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\`) para a URL de uma imagem diferente da qual você deseja gerar uma miniatura.
1. Abra una janela de prompt de comando.
1. Cole o comando do editor de texto na janela do prompt de comando.
1. Pressione Enter para executar o programa.

    ```bash
    curl -H "Ocp-Apim-Subscription-Key: <subscriptionKey>" -o <thumbnailFile> -H "Content-Type: application/json" "https://westus.api.cognitive.microsoft.com/vision/v3.1/generateThumbnail?width=100&height=100&smartCropping=true" -d "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\"}"
    ```

### <a name="examine-the-response"></a>Examinar a resposta

Uma resposta bem-sucedida grava a imagem em miniatura para o arquivo especificado em `<thumbnailFile>`. Se a solicitação falhar, a resposta conterá um código de erro e uma mensagem para ajudar a determinar o que deu errado. Se a solicitação parece ter êxito, mas a miniatura criada não for um arquivo de imagem válido, talvez sua chave de assinatura não seja válida.


## <a name="next-steps"></a>Próximas etapas

Explore a API de Análise de Imagem mais detalhadamente. Para testar rapidamente a API de Análise de Imagem, experimente [Abrir o console de teste da API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b/console).

> [!div class="nextstepaction"]
> [Explorar a API de Análise de Imagem](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b)
