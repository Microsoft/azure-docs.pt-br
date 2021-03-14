---
title: Usar o atributo HeadPose
titleSuffix: Azure Cognitive Services
description: Saiba como usar o atributo HeadPose para girar automaticamente o retângulo facial ou detectar gestos com a cabeça em um feed de vídeo.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 02/23/2021
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: cec466fc420e435159e2882d7b39ef6d03eeea7c
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101733475"
---
# <a name="use-the-headpose-attribute"></a>Usar o atributo HeadPose

Neste guia, você verá como usar o atributo HeadPose de um rosto detectado para habilitar alguns cenários importantes.

## <a name="rotate-the-face-rectangle"></a>Girar o retângulo facial

O retângulo facial, retornado com cada rosto detectado, marca a localização e o tamanho do rosto na imagem. Por padrão, o retângulo é sempre alinhado com a imagem (os lados são vertical e horizontal); isso pode ser ineficiente para o enquadramento de rostos angulares. Em situações em que você deseja cortar rostos de maneira programática em uma imagem, é melhor poder girar o retângulo para fazer o corte.

O aplicativo de exemplo do [WPF de Detecção Facial dos Serviços Cognitivos](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) usa o atributo HeadPose para girar os retângulos faciais detectados.

### <a name="explore-the-sample-code"></a>Explorar o código de exemplo

Você pode girar o retângulo facial de maneira programática usando o atributo HeadPose. Se você especificar esse atributo ao detectar rostos (confira [Como detectar rostos](HowtoDetectFacesinImage.md)), poderá consultá-lo mais tarde. O método a seguir do aplicativo [WPF de Detecção Facial dos Serviços Cognitivos](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) usa uma lista de objetos **DetectedFace** e retorna uma lista de objetos **[Face](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/app-samples/Cognitive-Services-Face-WPF/Sample-WPF/Controls/Face.cs)**. **Face** aqui é uma classe personalizada que armazena dados faciais, incluindo as coordenadas de retângulo atualizadas. Os novos valores são calculados para **parte superior**, **esquerda**, **largura** e **altura**, e um novo campo **FaceAngle** especifica a rotação.

```csharp
/// <summary>
/// Calculate the rendering face rectangle
/// </summary>
/// <param name="faces">Detected face from service</param>
/// <param name="maxSize">Image rendering size</param>
/// <param name="imageInfo">Image width and height</param>
/// <returns>Face structure for rendering</returns>
public static IEnumerable<Face> CalculateFaceRectangleForRendering(IList<DetectedFace> faces, int maxSize, Tuple<int, int> imageInfo)
{
    var imageWidth = imageInfo.Item1;
    var imageHeight = imageInfo.Item2;
    var ratio = (float)imageWidth / imageHeight;
    int uiWidth = 0;
    int uiHeight = 0;
    if (ratio > 1.0)
    {
        uiWidth = maxSize;
        uiHeight = (int)(maxSize / ratio);
    }
    else
    {
        uiHeight = maxSize;
        uiWidth = (int)(ratio * uiHeight);
    }

    var uiXOffset = (maxSize - uiWidth) / 2;
    var uiYOffset = (maxSize - uiHeight) / 2;
    var scale = (float)uiWidth / imageWidth;

    foreach (var face in faces)
    {
        var left = (int)(face.FaceRectangle.Left * scale + uiXOffset);
        var top = (int)(face.FaceRectangle.Top * scale + uiYOffset);

        // Angle of face rectangles, default value is 0 (not rotated).
        double faceAngle = 0;

        // If head pose attributes have been obtained, re-calculate the left & top (X & Y) positions.
        if (face.FaceAttributes?.HeadPose != null)
        {
            // Head pose's roll value acts directly as the face angle.
            faceAngle = face.FaceAttributes.HeadPose.Roll;
            var angleToPi = Math.Abs((faceAngle / 180) * Math.PI);

            // _____       | / \ |
            // |____|  =>  |/   /|
            //             | \ / |
            // Re-calculate the face rectangle's left & top (X & Y) positions.
            var newLeft = face.FaceRectangle.Left +
                face.FaceRectangle.Width / 2 -
                (face.FaceRectangle.Width * Math.Sin(angleToPi) + face.FaceRectangle.Height * Math.Cos(angleToPi)) / 2;

            var newTop = face.FaceRectangle.Top +
                face.FaceRectangle.Height / 2 -
                (face.FaceRectangle.Height * Math.Sin(angleToPi) + face.FaceRectangle.Width * Math.Cos(angleToPi)) / 2;

            left = (int)(newLeft * scale + uiXOffset);
            top = (int)(newTop * scale + uiYOffset);
        }

        yield return new Face()
        {
            FaceId = face.FaceId?.ToString(),
            Left = left,
            Top = top,
            OriginalLeft = (int)(face.FaceRectangle.Left * scale + uiXOffset),
            OriginalTop = (int)(face.FaceRectangle.Top * scale + uiYOffset),
            Height = (int)(face.FaceRectangle.Height * scale),
            Width = (int)(face.FaceRectangle.Width * scale),
            FaceAngle = faceAngle,
        };
    }
}
```

### <a name="display-the-updated-rectangle"></a>Exibir o retângulo atualizado

Aqui, você pode usar os objetos **Face** na exibição. As seguintes linhas de [FaceDetectionPage.xaml](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/app-samples/Cognitive-Services-Face-WPF/Sample-WPF/Controls/FaceDetectionPage.xaml) mostram como o novo retângulo é renderizado com base nesses dados:

```xaml
 <DataTemplate>
    <Rectangle Width="{Binding Width}" Height="{Binding Height}" Stroke="#FF26B8F4" StrokeThickness="1">
        <Rectangle.LayoutTransform>
            <RotateTransform Angle="{Binding FaceAngle}"/>
        </Rectangle.LayoutTransform>
    </Rectangle>
</DataTemplate>
```

## <a name="detect-head-gestures"></a>Detectar gestos com a cabeça

Você pode detectar gestos com a cabeça, como acenar e dizer não com a cabeça, acompanhando as alterações de HeadPose em tempo real. Use esse recurso como um detector de atividade personalizado.

A detecção de atividade é a tarefa de determinar que uma entidade é uma pessoa real, não uma representação de imagem ou de vídeo. Um detector de gestos com a cabeça pode servir como uma maneira de ajudar a verificar a atividade, especialmente em oposição a uma representação de imagem de uma pessoa.

> [!CAUTION]
> Para detectar gestos com a cabeça em tempo real, você precisará chamar a API de Detecção Facial a uma taxa alta (mais de uma vez por segundo). Caso você tenha uma assinatura da Camada gratuita (f0), isso não será possível. Caso você tenha uma assinatura da camada paga, verifique se calculou os custos de fazer chamadas rápidas à API para detecção de gestos com a cabeça.

Confira a [Amostra de HeadPose da Detecção Facial](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceAPIHeadPoseSample) no GitHub para obter um exemplo funcional da detecção de gestos com a cabeça.

## <a name="next-steps"></a>Próximas etapas

Confira o aplicativo [WPF de Detecção Facial dos Serviços Cognitivos](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) no GitHub para obter um exemplo funcional de retângulos faciais girados. Ou confira o aplicativo [Amostra de HeadPose da Detecção Facial](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples), que acompanha o atributo HeadPose em tempo real para detectar movimentos de cabeça.