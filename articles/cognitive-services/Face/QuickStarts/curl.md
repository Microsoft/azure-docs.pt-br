---
title: 'Início Rápido: Detectar rostos em uma imagem com a API REST do Azure e cURL'
titleSuffix: Azure Cognitive Services
description: Neste Início Rápido, você usará a API de Detecção Facial do Azure com cURL para detectar faces em uma imagem.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: pafarley
ms.openlocfilehash: 68cdd147977294954051735d70307305aa5dc0cb
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67603323"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-face-rest-api-and-curl"></a>Início Rápido: Detectar rostos em uma imagem com a API REST de Detecção Facial e cURL

Neste Início Rápido, você usará a API REST de Detecção Facial do Azure com cURL para detectar faces em uma imagem.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 

## <a name="prerequisites"></a>Pré-requisitos

- Uma chave de assinatura da API de Detecção Facial. É possível obter uma chave de assinatura de avaliação gratuita em [Experimente os Serviços Cognitivos](https://azure.microsoft.com/try/cognitive-services/?api=face-api). Ou siga as instruções em [Criar uma conta dos Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) para assinar o serviço API de Detecção Facial e obter sua chave.

## <a name="write-the-command"></a>Escrever o comando
 
Você usará um comando semelhante ao mostrado a seguir para chamar a API de Detecção Facial e obter dados de atributo de rosto de uma imagem. Primeiro, copie o código para um editor de texto&mdash;você precisará fazer alterações em determinadas partes do comando antes de você poder executá-lo.

```shell
curl -H "Ocp-Apim-Subscription-Key: <Subscription Key>" "https://westcentralus.api.cognitive.microsoft.com/face/v1.0/detect?returnFaceId=true&returnFaceLandmarks=false&returnFaceAttributes=age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise" -H "Content-Type: application/json" --data-ascii "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/c/c3/RH_Louise_Lillian_Gish.jpg\"}"
```

### <a name="subscription-key"></a>Chave de assinatura
Substitua `<Subscription Key>` pela sua chave de assinatura de Detecção Facial válida.

### <a name="face-endpoint-url"></a>URL de ponto de extremidade de detecção facial

A URL `https://westcentralus.api.cognitive.microsoft.com/face/v1.0/detect` indica o ponto de extremidade da Detecção Facial do Azure para consulta. Talvez você precise alterar a primeira parte dessa URL para que ela corresponda à região referente à sua chave de assinatura. Confira a [documentação da API de Detecção Facial](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) para obter uma lista de todos os pontos de extremidade de região.

### <a name="url-query-string"></a>Cadeia de consulta de URL

A cadeia de consulta da URL de ponto de extremidade de Detecção Facial especifica quais atributos de detecção facial devem ser recuperados. Talvez você queira alterar essa cadeia de caracteres, dependendo de sua intenção.

```
?returnFaceId=true&returnFaceLandmarks=false&returnFaceAttributes=age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise
```

### <a name="image-source-url"></a>URL de origem da imagem
A URL de origem indica que a imagem a ser usada como entrada. Você pode alterar isso para que ele aponte para qualquer imagem que deseje analisar.

```
https://upload.wikimedia.org/wikipedia/commons/c/c3/RH_Louise_Lillian_Gish.jpg
``` 

## <a name="run-the-command"></a>Executar o comando

Depois de fazer suas alterações, abra um prompt de comando e digite o novo comando. Você verá as informações faciais exibidas como dados JSON na janela do console. Por exemplo:

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

## <a name="next-steps"></a>Próximas etapas

Neste Início Rápido, você escreveu um comando cURL que chama a API de Detecção Facial do Azure para detectar faces em uma imagem e retornar seus atributos. Em seguida, explore a documentação de referência de API de Detecção Facial para saber mais.

> [!div class="nextstepaction"]
> [API de Detecção Facial](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
