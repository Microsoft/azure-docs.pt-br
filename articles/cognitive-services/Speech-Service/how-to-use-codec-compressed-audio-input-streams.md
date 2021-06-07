---
title: Áudio compactado do codec de fluxo com o SDK de fala-serviço de fala
titleSuffix: Azure Cognitive Services
description: Saiba como transmitir áudio compactado para o serviço de fala com o SDK de fala. Disponível para C++, C# e Java para Linux, Java no Android e Objective-C no iOS.
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: amishu
ms.custom: devx-track-csharp
zone_pivot_groups: programming-languages-set-twenty-two
ms.openlocfilehash: 1e08203076de2073e39c5b5f5eb40b66c88490d7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103417681"
---
# <a name="use-codec-compressed-audio-input-with-the-speech-sdk"></a>Usar a entrada de áudio compactada por codec com o SDK de fala

A API de **fluxo de entrada de áudio compactada** do SDK do serviço de fala fornece uma maneira de transmitir áudio compactado para o serviço de fala usando um `PullStream` ou o `PushStream` .

Plataforma | Idiomas | Versão do GStreamer com suporte
| :--- | ---: | :---:
Windows (exceto UWP)  | C++, C#, Java, Python | [1.18.3](https://gstreamer.freedesktop.org/data/pkg/windows/1.18.3/)
Linux  | C++, C#, Java, Python | [distribuições e arquiteturas de destino do Linux com suporte](~/articles/cognitive-services/speech-service/speech-sdk.md)
Android  | Java | [1.18.3](https://gstreamer.freedesktop.org/data/pkg/android/1.18.3/)

## <a name="speech-sdk-version-required-for-compressed-audio-input"></a>Versão do SDK de fala necessária para entrada de áudio compactado
* O Speech SDK versão 1.10.0 ou posterior é necessário para o RHEL 8 e o CentOS 8
* O Speech SDK versão 1.11.0 ou posterior é necessário para o para Windows.
* Speech SDK versão 1.16.0 ou posterior para o GStreamer mais recente no Windows e no Android.

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

## <a name="gstreamer-required-to-handle-compressed-audio"></a>GStreamer necessário para lidar com áudio compactado

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/cpp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/java/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/python/prerequisites.md)]
::: zone-end

## <a name="example-code-using-codec-compressed-audio-input"></a>Código de exemplo usando a entrada de áudio compactado por codec

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/csharp/examples.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/cpp/examples.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/java/examples.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/python/examples.md)]
::: zone-end

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba como reconhecer a fala](./get-started-speech-to-text.md)