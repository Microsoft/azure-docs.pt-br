---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: ad32204739d728006362ef55657a2f433be7aefc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97978881"
---
Para configurar o SDK de fala para aceitar a entrada de áudio compactado, crie `PullAudioInputStream` ou `PushAudioInputStream` . Em seguida, crie um `AudioConfig` de uma instância de sua classe de fluxo, especificando o formato de compactação do fluxo.

Vamos supor que você tenha uma classe de fluxo de entrada chamada `pushStream` e esteja usando Opus/OGG. Seu código pode ter a seguinte aparência:

```cpp
using namespace Microsoft::CognitiveServices::Speech;
using namespace Microsoft::CognitiveServices::Speech::Audio;

// ... omitted for brevity

 auto config =
    SpeechConfig::FromSubscription(
        "YourSubscriptionKey",
        "YourServiceRegion"
    );

auto audioFormat =
    AudioStreamFormat::GetCompressedFormat(
        AudioStreamContainerFormat::OGG_OPUS
    );
auto audioConfig =
    AudioConfig::FromStreamInput(
        pushStream,
        audioFormat
    );

auto recognizer = SpeechRecognizer::FromConfig(config, audioConfig);
auto result = recognizer->RecognizeOnceAsync().get();

auto text = result->Text;
```
