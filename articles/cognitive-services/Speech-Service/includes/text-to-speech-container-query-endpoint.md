---
title: Consultar ponto de extremidade de contêiner de conversão de texto em fala
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/31/2020
ms.author: aahi
ms.openlocfilehash: 9a29244745b154aa81997813fcf4e1457f599270
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103622163"
---
O contêiner fornece [APIs de ponto de extremidade baseadas em REST](../rest-text-to-speech.md). Há muitos [projetos de código-fonte de exemplo](https://github.com/Azure-Samples/Cognitive-Speech-TTS) para a plataforma, estrutura e variações de linguagem disponíveis.

Com os contêineres de conversão de texto em fala padrão ou neural, você deve contar com a localidade e a voz da marca de imagem baixada. Por exemplo, se você baixou a `latest` marca, a localidade padrão é `en-US` e a `AriaNeural` voz. `{VOICE_NAME}`Em seguida, o argumento seria [`en-US-AriaNeural`](../language-support.md#neural-voices) . Consulte o exemplo SSML abaixo:

```xml
<speak version="1.0" xml:lang="en-US">
    <voice name="en-US-AriaNeural">
        This text will get converted into synthesized speech.
    </voice>
</speak>
```

No entanto, para *texto personalizado para fala* , você precisará obter a **voz/modelo** do [portal de voz personalizado](https://aka.ms/custom-voice-portal). O nome do modelo personalizado é sinônimo do nome da voz. Navegue até a página de **treinamento** e copie a **voz/modelo** para usar como o `{VOICE_NAME}` argumento.
<br><br>
:::image type="content" source="../media/custom-voice/custom-voice-model-voice-name.png" alt-text="Modelo de voz personalizado-nome da voz":::

Consulte o exemplo SSML abaixo:

```xml
<speak version="1.0" xml:lang="en-US">
    <voice name="custom-voice-model">
        This text will get converted into synthesized speech.
    </voice>
</speak>
```

Vamos construir uma solicitação HTTP POST, fornecendo alguns cabeçalhos e uma carga de dados. Substitua o `{VOICE_NAME}` espaço reservado pelo seu próprio valor.

```curl
curl -s -v -X POST http://localhost:5000/speech/synthesize/cognitiveservices/v1 \
 -H 'Accept: audio/*' \
 -H 'Content-Type: application/ssml+xml' \
 -H 'X-Microsoft-OutputFormat: riff-24khz-16bit-mono-pcm' \
 -d '<speak version="1.0" xml:lang="en-US"><voice name="{VOICE_NAME}">This is a test, only a test.</voice></speak>' > output.wav
```


Esse comando:

* Constrói uma solicitação HTTP POST para o `speech/synthesize/cognitiveservices/v1` ponto de extremidade.
* Especifica um `Accept` cabeçalho de `audio/*`
* Especifica um `Content-Type` cabeçalho de `application/ssml+xml` , para obter mais informações, consulte [corpo da solicitação](../rest-text-to-speech.md#request-body).
* Especifica um `X-Microsoft-OutputFormat` cabeçalho de `riff-16khz-16bit-mono-pcm` , para obter mais opções, consulte [saída de áudio](../rest-text-to-speech.md#audio-outputs).
* Envia a solicitação de [linguagem de marcação de síntese de fala (SSML)](../speech-synthesis-markup.md) de acordo `{VOICE_NAME}` com o ponto de extremidade.
