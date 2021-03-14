---
title: Detectar faces em um rosto de imagem
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
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101713058"
---
# <a name="get-face-detection-data"></a>Obter dados de detecção facial

Este guia demonstra como usar a detecção facial para extrair atributos como gênero, idade ou pose de uma determinada imagem. Os trechos de código neste guia são escritos em C# usando a biblioteca de cliente facial dos serviços cognitivas do Azure. A mesma funcionalidade está disponível por meio da [API REST](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

Este guia mostra como:

- Obter os locais e as dimensões de faces em uma imagem.
- Obtenha os locais de vários pontos de referência de face, como Pupils, nariz e boca, em uma imagem.
- Adivinhe o gênero, a idade, a emoção e outros atributos de uma face detectada.

## <a name="setup"></a>Instalação

Este guia pressupõe que você já construiu um objeto [FaceClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient) , chamado `faceClient` , com uma chave de assinatura facial e uma URL de ponto de extremidade. A partir daqui, você pode usar o recurso de detecção facial chamando o [DetectWithUrlAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync), que é usado neste guia ou [DetectWithStreamAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync). Para obter instruções sobre como configurar esse recurso, siga um dos guias de início rápido.

Este guia concentra-se nas especificidades da chamada de detecção, como os argumentos que você pode passar e o que pode fazer com os dados retornados. Recomendamos que você consulte apenas os recursos necessários. Cada operação leva mais tempo para ser concluída.

## <a name="get-basic-face-data"></a>Obter dados básicos de face

Para localizar faces e obter seus locais em uma imagem, chame o método [DetectWithUrlAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync) ou [DetectWithStreamAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync) com o parâmetro _returnFaceId_ definido como **true**. Esta é a configuração padrão.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="basic1":::

Você pode consultar os objetos [DetectedFace](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface) retornados para suas IDs exclusivas e um retângulo que fornece as coordenadas de pixel da face.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="basic2":::

Para obter informações sobre como analisar o local e as dimensões da face, consulte [FaceRectangle](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.facerectangle). Normalmente, esse retângulo contém os olhos, os eyebrow, o nariz e a boca. A parte superior de Head, ouvidos e Chin não estão necessariamente incluídas. Para usar o retângulo de face para cortar uma cabeça completa ou obter um retrato de média, talvez para uma imagem de tipo ID de foto, você pode expandir o retângulo em cada direção.

## <a name="get-face-landmarks"></a>Obter pontos de referência facial

As dicas de [referência](../concepts/face-detection.md#face-landmarks) são um conjunto de pontos fáceis de encontrar em uma face, como o Pupils ou a gorjeta do nariz. Para obter dados de referência de face, defina o parâmetro _detectionModel_ como **detectionModel. Detection01** e o parâmetro _returnFaceLandmarks_ como **true**.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="landmarks1":::

O código a seguir demonstra como você pode recuperar os locais do nariz e do Pupils:

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="landmarks2":::

Você também pode usar dados de pontos de referência de face para calcular com precisão a direção da face. Por exemplo, você pode definir a rotação da face como um vetor do centro da boca para o centro dos olhos. O código a seguir calcula esse vetor:

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="direction":::

Quando você sabe a direção da face, pode girar o quadro de rosto Retangular para alinhá-lo mais adequadamente. Para cortar faces em uma imagem, você pode girar a imagem de forma programática para que as faces sempre apareçam na vertical.

## <a name="get-face-attributes"></a>Obter atributos faciais

Além de retângulos de face e pontos de referência, a API de detecção facial pode analisar vários atributos conceituais de uma face. Para obter uma lista completa, consulte a seção conceitual de [atributos de rosto](../concepts/face-detection.md#attributes) .

Para analisar os atributos de face, defina o parâmetro _detectionModel_ como **detectionModel. Detection01** e o parâmetro _returnFaceAttributes_ para uma lista de valores de [Enumeração FaceAttributeType](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype) .

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="attributes1":::

Em seguida, obtenha referências aos dados retornados e realize mais operações de acordo com suas necessidades.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="attributes2":::

Para saber mais sobre cada um dos atributos, consulte o guia conceitual de [detecção e atributos de rosto](../concepts/face-detection.md) .

## <a name="next-steps"></a>Próximas etapas

Neste guia, você aprendeu a usar as várias funcionalidades de detecção facial. Em seguida, integre esses recursos ao seu aplicativo seguindo um tutorial detalhado.

- [Tutorial: Criar um aplicativo do WPF para exibir dados faciais em uma imagem](../Tutorials/FaceAPIinCSharpTutorial.md)

## <a name="related-topics"></a>Tópicos relacionados

- [Documentação de referência (REST)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
- [Documentação de referência (SDK do .NET)](/dotnet/api/overview/azure/cognitiveservices/client/faceapi)
