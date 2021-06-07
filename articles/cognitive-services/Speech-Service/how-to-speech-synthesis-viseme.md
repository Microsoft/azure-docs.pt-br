---
title: Como obter eventos de pose facial para o Lip-Sync
titleSuffix: Azure Cognitive Services
description: O SDK de fala dá suporte ao evento viseme na síntese de fala, que são usados para representar a chave representa a fala observada, como a posição dos Lips, a Jaw e a língua ao produzir um fonema específico.
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/03/2021
ms.author: yulili
ms.custom: references_regions
zone_pivot_groups: programming-languages-speech-services-nomore-variant
ms.openlocfilehash: e97c48d4e42627d0fc2caaa4f66e81b9a0cafa86
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105643899"
---
# <a name="get-facial-pose-events"></a>Obter eventos de pose facial

> [!NOTE]
> O viseme só funciona para `en-US-AriaNeural` voz por enquanto.

Um viseme é a descrição visual de um fonema no idioma falado.
Ele define a posição da face e da boca quando fala uma palavra.
Cada viseme descreve as poses de chave facial para um conjunto específico de fonemas.
Não há nenhuma correspondência de um para um entre visemes e fonemas.
Geralmente, vários fonemas correspondem a um único viseme, pois vários fonemas têm a mesma aparência quando produzidos, como `s` e `z` .
Consulte a [tabela de mapeamento entre visemes e fonemas](#map-phonemes-to-visemes).

Usando o visemes, você pode criar um assistente de difusão de notícias mais natural e inteligente, jogos e caracteres de desenho mais interativos e vídeos mais intuitivos de ensino de linguagem. As pessoas com deficiência auditiva também podem pegar um conteúdo de fala visual e "Lip-Read" que mostra visemes em uma face animada.

## <a name="get-viseme-events-with-the-speech-sdk"></a>Obter eventos do viseme com o SDK de fala

Para fazer eventos viseme, convertemos o texto de entrada em um conjunto de sequências de fonema e suas sequências de viseme correspondentes. Estimamos a hora de início de cada viseme no áudio de fala. Os eventos viseme contêm uma sequência de IDs de viseme, cada uma com um deslocamento no áudio em que o viseme é exibido. Esses eventos podem gerar animações de boca que simulam uma pessoa falando sobre o texto de entrada.

| Parâmetro | Descrição |
|-----------|-------------|
| ID do viseme | Número inteiro que especifica um viseme. Em inglês (Estados Unidos), oferecemos 22 visemes diferentes para descrever as formas de boca de um conjunto específico de fonemas. Consulte a [tabela de mapeamento entre VISEME ID e fonemas](#map-phonemes-to-visemes).  |
| Deslocamento de áudio | A hora de início de cada viseme, em tiques (100 nanossegundos). |

Para obter eventos do viseme, assine o `VisemeReceived` evento no SDK de fala.
Os trechos de código a seguir mostram como assinar o evento viseme.

::: zone pivot="programming-language-csharp"

```csharp
using (var synthesizer = new SpeechSynthesizer(speechConfig, audioConfig))
{
    // Subscribes to viseme received event
    synthesizer.VisemeReceived += (s, e) =>
    {
        Console.WriteLine($"Viseme event received. Audio offset: " +
            $"{e.AudioOffset / 10000}ms, viseme id: {e.VisemeId}.");
    };

    var result = await synthesizer.SpeakSsmlAsync(ssml));
}

```

::: zone-end

::: zone pivot="programming-language-cpp"

```cpp
auto synthesizer = SpeechSynthesizer::FromConfig(speechConfig, audioConfig);

// Subscribes to viseme received event
synthesizer->VisemeReceived += [](const SpeechSynthesisVisemeEventArgs& e)
{
    cout << "viseme event received. "
        // The unit of e.AudioOffset is tick (1 tick = 100 nanoseconds), divide by 10,000 to convert to milliseconds.
        << "Audio offset: " << e.AudioOffset / 10000 << "ms, "
        << "viseme id: " << e.VisemeId << "." << endl;
};

auto result = synthesizer->SpeakSsmlAsync(ssml).get();
```

::: zone-end

::: zone pivot="programming-language-java"

```java
SpeechSynthesizer synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);

// Subscribes to viseme received event
synthesizer.VisemeReceived.addEventListener((o, e) -> {
    // The unit of e.AudioOffset is tick (1 tick = 100 nanoseconds), divide by 10,000 to convert to milliseconds.
    System.out.print("Viseme event received. Audio offset: " + e.getAudioOffset() / 10000 + "ms, ");
    System.out.println("viseme id: " + e.getVisemeId() + ".");
});

SpeechSynthesisResult result = synthesizer.SpeakSsmlAsync(ssml).get();
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
speech_synthesizer = speechsdk.SpeechSynthesizer(speech_config=speech_config, audio_config=audio_config)

# Subscribes to viseme received event
speech_synthesizer.viseme_received.connect(lambda evt: print(
    "Viseme event received: audio offset: {}ms, viseme id: {}.".format(evt.audio_offset / 10000, evt.viseme_id)))

result = speech_synthesizer.speak_ssml_async(ssml).get()
```

::: zone-end

::: zone pivot="programming-language-javascript"

```Javascript
var synthesizer = new SpeechSDK.SpeechSynthesizer(speechConfig, audioConfig);

// Subscribes to viseme received event
synthesizer.visemeReceived = function (s, e) {
    window.console.log("(Viseme), Audio offset: " + e.audioOffset / 10000 + "ms. Viseme ID: " + e.visemeId);
}

synthesizer.speakSsmlAsync(ssml);
```

::: zone-end

::: zone pivot="programming-language-objectivec"

```Objective-C
SPXSpeechSynthesizer *synthesizer =
    [[SPXSpeechSynthesizer alloc] initWithSpeechConfiguration:speechConfig
                                           audioConfiguration:audioConfig];

// Subscribes to viseme received event
[synthesizer addVisemeReceivedEventHandler: ^ (SPXSpeechSynthesizer *synthesizer, SPXSpeechSynthesisVisemeEventArgs *eventArgs) {
    NSLog(@"Viseme event received. Audio offset: %fms, viseme id: %lu.", eventArgs.audioOffset/10000., eventArgs.visemeId);
}];

[synthesizer speakSsml:ssml];
```

::: zone-end

## <a name="map-phonemes-to-visemes"></a>Mapear fonemas para visemes

Visemes variam de acordo com a linguagem. Cada idioma tem um conjunto de visemes que correspondem a seus fonemas específicos. A tabela a seguir mostra a correspondência entre os fonemas de IPA (alfabeto fonético internacional) e as IDs de viseme para inglês (Estados Unidos).

| IPA | Exemplo | ID do viseme |
|-----|---------|-----------|
| i   | **ea** t   | 6 |
| ɪ   | **i** f | 6 |
| eɪ  | **um** te | 4 |
| ɛ | **e** muito | 4 |
|æ  |   **um** tivas        |1|
|ɑ  |   **o** bstinate     |2|
|ɔ  |   c **au** se         |3|
|ʊ  |   b **OO** k          |4|
|oʊ |   **o** LD           |8|
|u  |   **C** ber          |7|
|ʌ  |   NCLE **u**         |1|
|aɪ |   **i** CE           |11|
|aʊ |   **ou** t           |9|
|ɔɪ |   **Oi** l           |10|
|ju |   **Yu** ma          |[6, 7]|
|ə  |   **um** go           |1|
|ɪɹ |   s **Ear**          |[6, 13]|
|ɛɹ |   plano de **ar**      |[4, 13]|
|ʊɹ |   c **Your** e          |[4, 13]|
|aɪ(ə)ɹ |   **Irar** Land   |[11, 13]|
|aʊ(ə)ɹ |   s de **hora**     |[9, 13]|
|ɔɹ |   **ou** Alo        |[3, 13]|
|ɑɹ |   tist **ar**        |[2, 13]|
|ɝ  |   **Ear** th         |[5, 13]|
|ɚ  |   todos os GY **er**       |[1, 13]|
|w  |   **w** i, s **UE** de   |7|
|j  |   **y** ARD, f **e** w     |6|
|p  |   **p** UT           |21|
|b  |   **b** IG           |21|
|t  |   **t** ALK          |19|
|d  |   IG **d**           |19|
|k  |   **c** UT           |20|
|g  |   **g** o            |20|
|m  |   **m** às, s **m** Ash    |21|
|n  |   **n** o, s **n** º      |19|
|ŋ  |   Li **n** k          |20|
|f  |   **f** ork          |18|
|v  |   **v** Alor         |18|
|θ  |   **em**          |17|
|ð  |   **en**          |17|
|s  |   **s** it           |15|
|z  |   **z** AP           |15|
|ʃ  |   **sh** e           |16|
|ʒ  |   Acques **J**       |16|
|h  |   do **h**          |12|
|tʃ |   **ch** em          |16|
|dʒ |   **j** Oy           |16|
|l  |   **l** ID, g **l** AD     |14|
|ɹ  |   **r** Ed, b **r** ing    |13|


## <a name="next-steps"></a>Próximas etapas

* [Documentação de referência do SDK de fala](speech-sdk.md)
