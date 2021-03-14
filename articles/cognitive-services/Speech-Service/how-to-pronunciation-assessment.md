---
title: Como usar o SDK de fala para avaliação de pronúncia
titleSuffix: Azure Cognitive Services
description: O SDK de fala dá suporte à avaliação de pronúncia, que avalia a qualidade de pronúncia da entrada de fala, com indicadores de precisão, fluência, integridade, etc.
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/12/2021
ms.author: yulili
ms.custom: references_regions
zone_pivot_groups: programming-languages-speech-services-nomore-variant
ms.openlocfilehash: dc1ab8bd1a851f7fafd5c001ac73e66973e1b64c
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102051881"
---
# <a name="pronunciation-assessment"></a>Avaliação de pronúncia

A avaliação de pronúncia avalia a pronúncia de fala e dá aos palestrantes os comentários sobre a precisão e a fluência de áudio falado.
Com a avaliação de pronúncia, os aprendizes de linguagem podem praticar, obter comentários instantâneos e melhorar sua pronúncia para que eles possam falar e apresentar confiança.
Os educadores podem usar a capacidade de avaliar a pronúncia de vários alto-falantes em tempo real.

Neste artigo, você aprenderá a configurar `PronunciationAssessmentConfig` e recuperar o `PronunciationAssessmentResult` usando o SDK de fala.

> [!NOTE]
> O recurso de avaliação de pronúncia só dá suporte ao idioma `en-US` no momento.

## <a name="pronunciation-assessment-with-the-speech-sdk"></a>Avaliação de pronúncia com o SDK de fala

Nos exemplos abaixo, você criará um `PronunciationAssessmentConfig` e o aplicará a um `SpeechRecognizer` .

Os trechos de código a seguir ilustram como usar a detecção automática de idioma em seus aplicativos:

::: zone pivot="programming-language-csharp"

```csharp
var pronunciationAssessmentConfig = new PronunciationAssessmentConfig(
    "reference text", GradingSystem.HundredMark, Granularity.Phoneme);

using (var recognizer = new SpeechRecognizer(
    speechConfig,
    audioConfig))
{
    // apply the pronunciation assessment configuration to the speech recognizer
    pronunciationAssessmentConfig.ApplyTo(recognizer);
    var speechRecognitionResult = await recognizer.RecognizeOnceAsync();
    var pronunciationAssessmentResult =
        PronunciationAssessmentResult.FromResult(speechRecognitionResult);
    var pronunciationScore = pronunciationAssessmentResult.PronunciationScore;
}
```

::: zone-end

::: zone pivot="programming-language-cpp"

```cpp
auto pronunciationAssessmentConfig =
    PronunciationAssessmentConfig::Create("reference text",
        PronunciationAssessmentGradingSystem::HundredMark,
        PronunciationAssessmentGranularity::Phoneme);

auto recognizer = SpeechRecognizer::FromConfig(
    speechConfig,
    audioConfig);

// apply the pronunciation assessment configuration to the speech recognizer
pronunciationAssessmentConfig->ApplyTo(recognizer);
speechRecognitionResult = recognizer->RecognizeOnceAsync().get();
auto pronunciationAssessmentResult =
    PronunciationAssessmentResult::FromResult(speechRecognitionResult);
auto pronunciationScore = pronunciationAssessmentResult->PronunciationScore;
```

::: zone-end

::: zone pivot="programming-language-java"

```java
PronunciationAssessmentConfig pronunciationAssessmentConfig =
    new PronunciationAssessmentConfig("reference text", 
        PronunciationAssessmentGradingSystem.HundredMark,
        PronunciationAssessmentGranularity.Phoneme);

SpeechRecognizer recognizer = new SpeechRecognizer(
    speechConfig,
    audioConfig);

// apply the pronunciation assessment configuration to the speech recognizer
pronunciationAssessmentConfig.applyTo(recognizer);
Future<SpeechRecognitionResult> future = recognizer.recognizeOnceAsync();
SpeechRecognitionResult result = future.get(30, TimeUnit.SECONDS);
PronunciationAssessmentResult pronunciationAssessmentResult =
    PronunciationAssessmentResult.fromResult(result);
Double pronunciationScore = pronunciationAssessmentResult.getPronunciationScore();

recognizer.close();
speechConfig.close();
audioConfig.close();
pronunciationAssessmentConfig.close();
result.close();
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
pronunciation_assessment_config = \
        speechsdk.PronunciationAssessmentConfig(reference_text='reference text',
                grading_system=msspeech.PronunciationAssessmentGradingSystem.HundredMark,
                granularity=msspeech.PronunciationAssessmentGranularity.Phoneme)
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, \
        audio_config=audio_config)

# apply the pronunciation assessment configuration to the speech recognizer
pronunciation_assessment_config.apply_to(speech_recognizer)
result = speech_recognizer.recognize_once()
pronunciation_assessment_result = speechsdk.PronunciationAssessmentResult(result)
pronunciation_score = pronunciation_assessment_result.pronunciation_score
```

::: zone-end

::: zone pivot="programming-language-javascript"

```Javascript
var pronunciationAssessmentConfig = new SpeechSDK.PronunciationAssessmentConfig("reference text",
    PronunciationAssessmentGradingSystem.HundredMark,
    PronunciationAssessmentGranularity.Word, true);
var speechRecognizer = SpeechSDK.SpeechRecognizer.FromConfig(speechConfig, audioConfig);
// apply the pronunciation assessment configuration to the speech recognizer
pronunciationAssessmentConfig.applyTo(speechRecognizer);

speechRecognizer.recognizeOnceAsync((result: SpeechSDK.SpeechRecognitionResult) => {
        var pronunciationAssessmentResult = SpeechSDK.PronunciationAssessmentResult.fromResult(result);
        var pronunciationScore = pronunciationAssessmentResult.pronunciationScore;
        var wordLevelResult = pronunciationAssessmentResult.detailResult.Words;
},
{});
```

::: zone-end

::: zone pivot="programming-language-objectivec"

```Objective-C
SPXPronunciationAssessmentConfiguration* pronunciationAssessmentConfig =
    [[SPXPronunciationAssessmentConfiguration alloc]init:@"reference text"
                                           gradingSystem:SPXPronunciationAssessmentGradingSystem_HundredMark
                                             granularity:SPXPronunciationAssessmentGranularity_Phoneme];

SPXSpeechRecognizer* speechRecognizer = \
        [[SPXSpeechRecognizer alloc] initWithSpeechConfiguration:speechConfig
                                              audioConfiguration:audioConfig];

// apply the pronunciation assessment configuration to the speech recognizer
[pronunciationAssessmentConfig applyToRecognizer:speechRecognizer];

SPXSpeechRecognitionResult *result = [speechRecognizer recognizeOnce];
SPXPronunciationAssessmentResult* pronunciationAssessmentResult = [[SPXPronunciationAssessmentResult alloc] init:result];
double pronunciationScore = pronunciationAssessmentResult.pronunciationScore;
```

::: zone-end

### <a name="pronunciation-assessment-configuration-parameters"></a>Parâmetros de configuração de avaliação de pronúncia

Esta tabela lista os parâmetros de configuração para avaliação de pronúncia.

| Parâmetro | Descrição | Necessário? |
|-----------|-------------|---------------------|
| ReferenceText | O texto em relação ao qual a pronúncia será avaliada. | Obrigatório |
| GradingSystem | O sistema de ponto para a calibragem de pontuação. O `FivePoint` sistema fornece uma pontuação de ponto flutuante de 0-5 e `HundredMark` fornece uma pontuação de ponto flutuante de 0-100. Padrão: `FivePoint`. | Opcional |
| Granularidade | A granularidade da avaliação. Os valores aceitos são `Phoneme` , que mostra a pontuação no nível de texto completo, Word e fonema, `Word` , que mostra a pontuação no texto completo e no nível de palavra, `FullText` , que mostra a pontuação somente no nível de texto completo. Padrão: `Phoneme`. | Opcional |
| EnableMiscue | Habilita o cálculo de miscue. Com isso habilitado, as palavras pronunciadas serão comparadas ao texto de referência e serão marcadas com omissão/inserção com base na comparação. Os valores aceitos são `False` e `True`. Padrão: `False`. | Opcional |
| Scenarioid | Um GUID que indica um sistema de ponto personalizado. | Opcional |

### <a name="pronunciation-assessment-result-parameters"></a>Parâmetros de resultado da avaliação de pronúncia

Esta tabela lista os parâmetros de resultado da avaliação de pronúncia.

| Parâmetro | Descrição |
|-----------|-------------|
| `AccuracyScore` | Precisão da pronúncia da fala. A precisão indica o quão próximo os fonemas correspondem à pronúncia de um orador nativo. A pontuação de precisão de nível de texto completo e de palavra é agregada da Pontuação de precisão de nível de fonema. |
| `FluencyScore` | Fluência da fala determinada. Fluência indica a precisão com que a fala corresponde ao uso silencioso de quebras silenciosas entre palavras do orador nativo. |
| `CompletenessScore` | Integridade da fala, determinada pelo cálculo da proporção de palavras pronunciadas para fazer referência à entrada de texto. |
| `PronunciationScore` | Pontuação geral que indica a qualidade da pronúncia da fala determinada. Isso é agregado de `AccuracyScore` `FluencyScore` e `CompletenessScore` com peso. |
| `ErrorType` | Esse valor indica se uma palavra é omitida, inserida ou pronunciada incorretamente, em comparação com `ReferenceText` . Os valores possíveis são `None` (ou seja, nenhum erro nesta palavra), `Omission` `Insertion` e `Mispronunciation` . |

## <a name="next-steps"></a>Próximas etapas

<!-- TODO: update JavaScript sample links after release -->

::: zone pivot="programming-language-csharp"
* Consulte o [código de exemplo](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_recognition_samples.cs#L949) no GitHub para avaliação de pronúncia.
::: zone-end

::: zone pivot="programming-language-cpp"
* Consulte o [código de exemplo](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/cpp/windows/console/samples/speech_recognition_samples.cpp#L633) no GitHub para avaliação de pronúncia.
::: zone-end

::: zone pivot="programming-language-java"
* Consulte o [código de exemplo](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/SpeechRecognitionSamples.java#L697) no GitHub para avaliação de pronúncia.
::: zone-end

::: zone pivot="programming-language-python"
* Consulte o [código de exemplo](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/python/console/speech_sample.py#L576) no GitHub para avaliação de pronúncia.
::: zone-end

::: zone pivot="programming-language-objectivec"
* Consulte o [código de exemplo](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/objective-c/ios/speech-samples/speech-samples/ViewController.m#L642) no GitHub para avaliação de pronúncia.
::: zone-end

* [Documentação de referência do SDK de fala](speech-sdk.md)
