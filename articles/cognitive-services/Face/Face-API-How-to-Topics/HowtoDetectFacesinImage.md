---
title: Detectar rostos em uma imagem – Detecção Facial
titleSuffix: Azure Cognitive Services
description: Este guia demonstra como usar a detecção facial para extrair atributos como gênero, idade ou pose de uma determinada imagem.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 02/23/2021
ms.author: sbowles
ms.custom: devx-track-csharp
ms.openlocfilehash: 3a15cce45c527a92c99e0488661e0b67bb8e2371
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "101713058"
---
# <a name="get-face-detection-data"></a>Obter dados de detecção facial

Este guia demonstra como usar a detecção facial para extrair atributos como gênero, idade ou pose de uma determinada imagem. As os snippets de código deste guia foram escritos em C# com a biblioteca de clientes da Detecção Facial dos Serviços Cognitivos do Azure. O mesmo recurso está disponível por meio da [API REST](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

Este guia mostra como:

- Obter os locais e as dimensões de rostos em uma imagem.
- Obter em uma imagem os locais de vários pontos de referência do rosto, como pupilas, nariz e boca.
- Supor o gênero, a idade, a emoção e outros atributos de um rosto detectado.

## <a name="setup"></a>Instalação

Este guia pressupõe que você já construiu um objeto [FaceClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient), chamado `faceClient`, com uma chave de assinatura da Detecção Facial e uma URL de ponto de extremidade. Deste ponto em diante, você pode usar o recurso de detecção facial chamando [DetectWithUrlAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync), que é usado neste guia ou [DetectWithStreamAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync). Para obter instruções sobre como configurar esse recurso, siga um dos guias de início rápido.

Este guia concentra-se nas especificidades da chamada da Detecção, como os argumentos que você pode passar e o que pode fazer com os dados retornados. Recomendamos que você consulte apenas os recursos necessários. Cada operação leva mais tempo para ser concluída.

## <a name="get-basic-face-data"></a>Obter dados básicos de rosto

Para localizar rostos e obter suas localizações em uma imagem, chame o método [DetectWithUrlAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync) ou [DetectWithStreamAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync) com o parâmetro _returnFaceId_ definido como **true**. Essa é a configuração padrão.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="basic1":::

Você pode consultar os objetos [DetectedFace](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface) retornados quanto às IDs exclusivas e um retângulo que fornece as coordenadas de pixel do rosto.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="basic2":::

Para obter informações sobre como analisar a localização e as dimensões do rosto, confira [FaceRectangle](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.facerectangle). Normalmente, esse retângulo contém os olhos, as sobrancelhas, o nariz e a boca. A parte superior da cabeça, os ouvidos e o queixo não estão necessariamente incluídos. Para usar o retângulo de rosto para recortar totalmente uma cabeça ou obter um retrato de média distância, talvez para uma foto do tipo utilizado em documentos, você pode expandir o retângulo em cada direção.

## <a name="get-face-landmarks"></a>Obter pontos de referência facial

[Os pontos de referência faciais](../concepts/face-detection.md#face-landmarks) são um conjunto de pontos fáceis de encontrar em um rosto, como as pupilas ou a ponta do nariz. Para obter dados de referência do rosto, defina o parâmetro _detectionModel_ como **DetectionModel.Detection01** e o parâmetro _returnFaceLandmarks_ como **true**.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="landmarks1":::

O seguinte código demonstra como você pode recuperar as localizações do nariz e das pupilas:

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="landmarks2":::

Você também pode usar dados de pontos de referência faciais para calcular com precisão a direção do rosto. Por exemplo, você pode definir a rotação do rosto como um vetor partindo do centro da boca para o centro dos olhos. O seguinte código calcula esse vetor:

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="direction":::

Quando você sabe a direção do rosto, pode girar o quadro de rosto retangular para alinhá-lo mais adequadamente. Para recortar rostos em uma imagem, você pode girar a imagem de maneira programática para que os rostos sempre apareçam na vertical.

## <a name="get-face-attributes"></a>Obter atributos faciais

Além dos retângulos e pontos de referência faciais, a API de Detecção Facial pode analisar vários atributos conceituais de um rosto. Para obter uma lista completa, confira a seção conceitual de [Atributos de rosto](../concepts/face-detection.md#attributes).

Para analisar os atributos de rosto, defina o parâmetro _detectionModel_ como **DetectionModel.Detection01** e o parâmetro _returnFaceAttributes_ para uma lista de valores da [Enumeração FaceAttributeType](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype).

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="attributes1":::

Em seguida, obtenha referências aos dados retornados e realize mais operações de acordo com suas necessidades.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="attributes2":::

Para saber mais sobre cada um dos atributos, confira o guia conceitual de [Detecção e atributos de rosto](../concepts/face-detection.md).

## <a name="next-steps"></a>Próximas etapas

Neste guia, você aprendeu a usar as várias funcionalidades da detecção facial. Em seguida, integre esses recursos ao seu aplicativo seguindo um tutorial detalhado.

- [Tutorial: Criar um aplicativo do WPF para exibir dados faciais em uma imagem](../Tutorials/FaceAPIinCSharpTutorial.md)

## <a name="related-topics"></a>Tópicos relacionados

- [Documentação de referência (REST)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
- [Documentação de referência (SDK do .NET)](/dotnet/api/overview/azure/cognitiveservices/client/faceapi)
