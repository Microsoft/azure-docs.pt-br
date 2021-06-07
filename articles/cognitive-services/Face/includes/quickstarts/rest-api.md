---
title: Guia de início rápido da API REST de Detecção Facial
description: Use a API REST de Detecção Facial com o cURL para detectar rostos, localizar semelhantes (pesquisa de rosto por imagem), identificar rostos (pesquisa de reconhecimento do rosto) e migrar os dados de rosto.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: include
ms.date: 12/06/2020
ms.author: pafarley
ms.openlocfilehash: 1a360a19da8006a8ab6d918560f1b1d566fdd66d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105104571"
---
Comece a usar o reconhecimento do rosto usando a API REST de Detecção Facial. O serviço de Detecção Facial fornece acesso a algoritmos avançados para detectar e reconhecer rostos humanos em imagens.

Use a API REST de Detecção Facial para:

* [Detectar faces em uma imagem](#detect-faces-in-an-image)
* [Encontrar rostos semelhantes](#find-similar-faces)

> [!NOTE]
> Este guia de início rápido usa comandos cURL para chamar a API REST. Você também pode chamar a API REST usando uma linguagem de programação. Confira as amostras do GitHub para obter exemplos em [C#](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/Face/rest), [Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/python/Face/rest), [Java](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/java/Face/rest), [JavaScript](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/javascript/Face/rest) e [Go](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/go/Face/rest).

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure – [Criar uma gratuitamente](https://azure.microsoft.com/free/cognitive-services/)
* Depois de obter sua assinatura do Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title="Crie um recurso da Detecção Facial"  target="_blank">crie um recurso da Detecção Facial </a> no portal do Azure para obter a chave e o ponto de extremidade. Após a implantação, clique em **Ir para o recurso**.
    * Você precisará da chave e do ponto de extremidade do recurso criado para conectar seu aplicativo à API de Detecção Facial. Cole a chave e o ponto de extremidade no código abaixo mais adiante no guia de início rápido.
    * Use o tipo de preço gratuito (`F0`) para experimentar o serviço e atualizar mais tarde para um nível pago para produção.
* [PowerShell versão 6.0+](/powershell/scripting/install/installing-powershell-core-on-windows) ou um aplicativo de linha de comando semelhante.


## <a name="detect-faces-in-an-image"></a>Detectar faces em uma imagem

Você usará um comando semelhante ao mostrado a seguir para chamar a API de Detecção Facial e obter dados de atributo de rosto de uma imagem. Primeiro, copie o código para um editor de texto&mdash;você precisará fazer alterações em determinadas partes do comando antes de você poder executá-lo.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="detection_model_2":::

Faça as seguintes alterações:
1. Atribua `Ocp-Apim-Subscription-Key` à sua chave de assinatura de Detecção Facial válida.
1. Altere a primeira parte da URL de consulta para que ela corresponda ao ponto de extremidade referente à sua chave de assinatura.
   [!INCLUDE [subdomains-note](../../../../../includes/cognitive-services-custom-subdomains-note.md)]
1. Opcionalmente, altere a URL no corpo da solicitação para que ela aponte para outra imagem.

Depois de fazer suas alterações, abra um prompt de comando e digite o novo comando. 

### <a name="examine-the-results"></a>Examinar os resultados

Você verá as informações faciais exibidas como dados JSON na janela do console. Por exemplo:

```json
[
  {
    "faceId": "49d55c17-e018-4a42-ba7b-8cbbdfae7c6f",
    "faceRectangle": {
      "top": 131,
      "left": 177,
      "width": 162,
      "height": 162
    }
  }
]  
```

### <a name="get-face-attributes"></a>Obter atributos faciais
 
Para extrair os atributos faciais, chame a API de Detecção novamente, mas defina `detectionModel` como `detection_01`. Adicione também o parâmetro de consulta `returnFaceAttributes`. Agora o comando deve ter a aparência a seguir. Como antes, insira sua chave de assinatura e o ponto de extremidade de Detecção Facial.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="detection_model_1":::

### <a name="examine-the-results"></a>Examinar os resultados

As informações de face retornadas agora incluem atributos de face. Por exemplo:

```json
[
  {
    "faceId": "49d55c17-e018-4a42-ba7b-8cbbdfae7c6f",
    "faceRectangle": {
      "top": 131,
      "left": 177,
      "width": 162,
      "height": 162
    },
    "faceAttributes": {
      "smile": 0,
      "headPose": {
        "pitch": 0,
        "roll": 0.1,
        "yaw": -32.9
      },
      "gender": "female",
      "age": 22.9,
      "facialHair": {
        "moustache": 0,
        "beard": 0,
        "sideburns": 0
      },
      "glasses": "NoGlasses",
      "emotion": {
        "anger": 0,
        "contempt": 0,
        "disgust": 0,
        "fear": 0,
        "happiness": 0,
        "neutral": 0.986,
        "sadness": 0.009,
        "surprise": 0.005
      },
      "blur": {
        "blurLevel": "low",
        "value": 0.06
      },
      "exposure": {
        "exposureLevel": "goodExposure",
        "value": 0.67
      },
      "noise": {
        "noiseLevel": "low",
        "value": 0
      },
      "makeup": {
        "eyeMakeup": true,
        "lipMakeup": true
      },
      "accessories": [],
      "occlusion": {
        "foreheadOccluded": false,
        "eyeOccluded": false,
        "mouthOccluded": false
      },
      "hair": {
        "bald": 0,
        "invisible": false,
        "hairColor": [
          {
            "color": "brown",
            "confidence": 1
          },
          {
            "color": "black",
            "confidence": 0.87
          },
          {
            "color": "other",
            "confidence": 0.51
          },
          {
            "color": "blond",
            "confidence": 0.08
          },
          {
            "color": "red",
            "confidence": 0.08
          },
          {
            "color": "gray",
            "confidence": 0.02
          }
        ]
      }
    }
  }
]
```

## <a name="find-similar-faces"></a>Encontrar rostos semelhantes

Esta operação usa um rosto detectado (origem) e pesquisa um conjunto de outros rostos (destino) para encontrar correspondências (pesquisa de rosto por imagem). Quando encontra uma correspondência, ele imprime a ID do rosto correspondente no console.

### <a name="detect-faces-for-comparison"></a>Detectar rostos para comparação

Primeiro, detecte rostos em imagens para compará-los. Execute este comando da mesma forma que fez na seção [Detectar rostos](#detect-faces-in-an-image). Esse método de detecção é otimizado para operações de comparação. Ele não extrai atributos de rosto detalhados, como na seção acima, e usa outro modelo de detecção.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="detect_for_similar":::

Encontre o valor `"faceId"` na resposta JSON e salve-o em uma localização temporária. Em seguida, chame o comando acima novamente para essas outras URLs de imagem e salve as IDs de rosto também. Você usará essas IDs como o grupo de destino de rostos com base no qual um rosto semelhante será encontrado.

:::code source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="similar_group":::

Por fim, detecte o rosto de origem que você usará para correspondência e salve a ID dele. Mantenha essa ID separada das outras.

:::code source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="similar_matcher":::

### <a name="find-matches"></a>Encontrar correspondências

Copie o comando a seguir em um editor de texto.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="similar":::

Depois, faça as seguintes alterações:
1. Atribua `Ocp-Apim-Subscription-Key` à sua chave de assinatura de Detecção Facial válida.
1. Altere a primeira parte da URL de consulta para que ela corresponda ao ponto de extremidade referente à sua chave de assinatura.

Use o seguinte conteúdo JSON para o valor de `body`:

:::code language="JSON" source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="similar_body":::

1. Use a ID de rosto de origem para `"faceId"`.
1. Cole as outras IDs de rosto como termos na matriz `"faceIds"`.

### <a name="examine-the-results"></a>Examinar os resultados

Você receberá uma resposta JSON que lista as IDs de rostos que correspondem ao rosto da consulta.

```json
[
    {
        "persistedFaceId" : "015839fb-fbd9-4f79-ace9-7675fc2f1dd9",
        "confidence" : 0.82
    },
    ...
] 
```

## <a name="clean-up-resources"></a>Limpar os recursos

Se quiser limpar e remover uma assinatura dos Serviços Cognitivos, você poderá excluir o recurso ou grupo de recursos. Excluir o grupo de recursos também exclui todos os recursos associados a ele.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você aprendeu a usar a API REST de Detecção Facial para realizar tarefas básicas de reconhecimento do rosto. Em seguida, explore a documentação de referência para saber mais sobre a biblioteca.

> [!div class="nextstepaction"]
> [Referência da API de Detecção Facial](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)

* [O que é o serviço de Detecção Facial?](../../overview.md)