---
title: Áudio compactado do codec de fluxo com o SDK de fala-serviço de fala
titleSuffix: Azure Cognitive Services
description: Saiba como transmitir áudio compactado para os serviços de fala do Azure com o SDK de fala. Disponível para C++o C#, o e o Java para Linux.
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: amishu
ms.openlocfilehash: b29b42dea9522526d49c1bda017a522855946def
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559542"
---
# <a name="using-codec-compressed-audio-input-with-the-speech-sdk"></a>Usando o codec de entrada de áudio compactado com o SDK de fala

A API de **fluxo de entrada de áudio compactada** do SDK de fala fornece uma maneira de transmitir áudio compactado para o serviço de fala usando PullStream ou PushStream.

> [!IMPORTANT]
> O streaming de áudio compactado só C++tem C#suporte para o, o e o Java no Linux (Ubuntu 16, 4, Ubuntu 18, 4, Debian 9).
> O Speech SDK versão 1.4.0 ou superior é necessário.

Para wav/PCM, consulte a documentação de fala principal.  Fora de wav/PCM, há suporte para os seguintes formatos de entrada compactados do Codec:

- MP3
- OPUS/OGG

## <a name="prerequisites-to-using-codec-compressed-audio-input"></a>Pré-requisitos para usar a entrada de áudio compactado por codec

Instale essas dependências adicionais para usar a entrada de áudio compactado com o SDK de fala para Linux:

```sh
sudo apt install libgstreamer1.0-0 gstreamer1.0-plugins-base gstreamer1.0-plugins-good gstreamer1.0-plugins-bad gstreamer1.0-plugins-ugly
```

## <a name="example-code-using-codec-compressed-audio-input"></a>Código de exemplo usando a entrada de áudio compactado por codec

Para transmitir em um formato de áudio compactado para os serviços de `PullAudioInputStream` fala `PushAudioInputStream`, crie ou. Em seguida, crie `AudioConfig` um de uma instância de sua classe de fluxo, especificando o formato de compactação do fluxo.

Vamos supor que você tenha uma classe de fluxo de entrada `myPushStream` chamada e esteja usando Opus/OGG. Seu código pode ter a seguinte aparência:

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

var speechConfig = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");

// Create an audio config specifying the compressed audio format and the instance of your input stream class.
var audioFormat = AudioStreamFormat.GetCompressedFormat(AudioStreamContainerFormat.OGG_OPUS);
var audioConfig = AudioConfig.FromStreamInput(myPushStream, audioFormat);

var recognizer = new SpeechRecognizer(speechConfig, audioConfig);

var result = await recognizer.RecognizeOnceAsync();

var text = result.GetText();
```

## <a name="next-steps"></a>Próximas etapas

- [Obtenha sua assinatura de avaliação de Fala](https://azure.microsoft.com/try/cognitive-services/)
- [Veja como reconhecer fala em C#](quickstart-csharp-dotnet-windows.md)
