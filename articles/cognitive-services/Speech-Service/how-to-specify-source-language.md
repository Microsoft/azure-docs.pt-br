---
title: Como especificar o idioma de origem de fala para texto
titleSuffix: Azure Cognitive Services
description: O SDK de fala permite que você especifique o idioma de origem ao converter a fala em texto. Este artigo descreve como usar os métodos FromConfig e SourceLanguageConfig para permitir que o serviço de fala saiba o idioma de origem e forneça um destino de modelo personalizado.
services: cognitive-services
author: susanhu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: qiohu
zone_pivot_groups: programming-languages-set-two
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 1b134fd3d09eeda340e7323638a36b68336242c2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91362020"
---
# <a name="specify-source-language-for-speech-to-text"></a>Especificar o idioma de origem de fala para texto

Neste artigo, você aprenderá a especificar o idioma de origem para uma entrada de áudio passada para o SDK de fala para reconhecimento de fala. Além disso, o código de exemplo é fornecido para especificar um modelo de fala personalizado para um reconhecimento aprimorado.

::: zone pivot="programming-language-csharp"

## <a name="how-to-specify-source-language-in-c"></a>Como especificar o idioma de origem em C #

No exemplo a seguir, o idioma de origem é fornecido explicitamente como um parâmetro usando `SpeechRecognizer` Construct.

```csharp
var recognizer = new SpeechRecognizer(speechConfig, "de-DE", audioConfig);
```

No exemplo a seguir, o idioma de origem é fornecido usando `SourceLanguageConfig` . Em seguida, o `sourceLanguageConfig` é passado como um parâmetro a ser `SpeechRecognizer` construído.

```csharp
var sourceLanguageConfig = SourceLanguageConfig.FromLanguage("de-DE");
var recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

No exemplo a seguir, o idioma de origem e o ponto de extremidade personalizado são fornecidos usando `SourceLanguageConfig` . Em seguida, o `sourceLanguageConfig` é passado como um parâmetro a ser `SpeechRecognizer` construído.

```csharp
var sourceLanguageConfig = SourceLanguageConfig.FromLanguage("de-DE", "The Endpoint ID for your custom model.");
var recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `SpeechRecognitionLanguage` e `EndpointId` os métodos set são preteridos da `SpeechConfig` classe em C#. O uso desses métodos não é recomendado e não deve ser usado durante a construção de um `SpeechRecognizer` .

::: zone-end

::: zone pivot="programming-language-cpp"


## <a name="how-to-specify-source-language-in-c"></a>Como especificar o idioma de origem em C++

No exemplo a seguir, o idioma de origem é fornecido explicitamente como um parâmetro usando o `FromConfig` método.

```C++
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, "de-DE", audioConfig);
```

No exemplo a seguir, o idioma de origem é fornecido usando `SourceLanguageConfig` . Em seguida, o `sourceLanguageConfig` é passado como um parâmetro para `FromConfig` ao criar o `recognizer` .

```C++
auto sourceLanguageConfig = SourceLanguageConfig::FromLanguage("de-DE");
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, sourceLanguageConfig, audioConfig);
```

No exemplo a seguir, o idioma de origem e o ponto de extremidade personalizado são fornecidos usando `SourceLanguageConfig` . O `sourceLanguageConfig` é passado como um parâmetro para `FromConfig` ao criar o `recognizer` .

```C++
auto sourceLanguageConfig = SourceLanguageConfig::FromLanguage("de-DE", "The Endpoint ID for your custom model.");
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `SetSpeechRecognitionLanguage` e `SetEndpointId` são métodos preteridos da `SpeechConfig` classe em C++ e Java. O uso desses métodos não é recomendado e não deve ser usado durante a construção de um `SpeechRecognizer` .

::: zone-end

::: zone pivot="programming-language-java"

## <a name="how-to-specify-source-language-in-java"></a>Como especificar o idioma de origem em Java

No exemplo a seguir, o idioma de origem é fornecido explicitamente ao criar um novo `SpeechRecognizer` .

```Java
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, "de-DE", audioConfig);
```

No exemplo a seguir, o idioma de origem é fornecido usando `SourceLanguageConfig` . Em seguida, o `sourceLanguageConfig` é passado como um parâmetro ao criar um novo `SpeechRecognizer` .

```Java
SourceLanguageConfig sourceLanguageConfig = SourceLanguageConfig.fromLanguage("de-DE");
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

No exemplo a seguir, o idioma de origem e o ponto de extremidade personalizado são fornecidos usando `SourceLanguageConfig` . Em seguida, o `sourceLanguageConfig` é passado como um parâmetro ao criar um novo `SpeechRecognizer` .

```Java
SourceLanguageConfig sourceLanguageConfig = SourceLanguageConfig.fromLanguage("de-DE", "The Endpoint ID for your custom model.");
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `setSpeechRecognitionLanguage` e `setEndpointId` são métodos preteridos da `SpeechConfig` classe em C++ e Java. O uso desses métodos não é recomendado e não deve ser usado durante a construção de um `SpeechRecognizer` .

::: zone-end

::: zone pivot="programming-language-python"

## <a name="how-to-specify-source-language-in-python"></a>Como especificar o idioma de origem no Python

No exemplo a seguir, o idioma de origem é fornecido explicitamente como um parâmetro usando `SpeechRecognizer` Construct.

```Python
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, language="de-DE", audio_config=audio_config)
```

No exemplo a seguir, o idioma de origem é fornecido usando `SourceLanguageConfig` . Em seguida, o `SourceLanguageConfig` é passado como um parâmetro a ser `SpeechRecognizer` construído.

```Python
source_language_config = speechsdk.languageconfig.SourceLanguageConfig("de-DE")
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, source_language_config=source_language_config, audio_config=audio_config)
```

No exemplo a seguir, o idioma de origem e o ponto de extremidade personalizado são fornecidos usando `SourceLanguageConfig` . Em seguida, o `SourceLanguageConfig` é passado como um parâmetro a ser `SpeechRecognizer` construído.

```Python
source_language_config = speechsdk.languageconfig.SourceLanguageConfig("de-DE", "The Endpoint ID for your custom model.")
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, source_language_config=source_language_config, audio_config=audio_config)
```

>[!Note]
> `speech_recognition_language``endpoint_id`as propriedades e são preteridas da `SpeechConfig` classe no Python. O uso dessas propriedades não é recomendado, e elas não devem ser usadas durante a construção de um `SpeechRecognizer` .

::: zone-end

::: zone pivot="programming-language-more"

## <a name="how-to-specify-source-language-in-javascript"></a>Como especificar o idioma de origem em JavaScript

A primeira etapa é criar um `SpeechConfig` :

```Javascript
var speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionkey", "YourRegion");
```

Em seguida, especifique o idioma de origem do seu áudio com `speechRecognitionLanguage` :

```Javascript
speechConfig.speechRecognitionLanguage = "de-DE";
```

Se você estiver usando um modelo personalizado para reconhecimento, poderá especificar o ponto de extremidade com `endpointId` :

```Javascript
speechConfig.endpointId = "The Endpoint ID for your custom model.";
```

## <a name="how-to-specify-source-language-in-objective-c"></a>Como especificar o idioma de origem no Objective-C

No exemplo a seguir, o idioma de origem é fornecido explicitamente como um parâmetro usando `SPXSpeechRecognizer` Construct.

```Objective-C
SPXSpeechRecognizer* speechRecognizer = \
    [[SPXSpeechRecognizer alloc] initWithSpeechConfiguration:speechConfig language:@"de-DE" audioConfiguration:audioConfig];
```

No exemplo a seguir, o idioma de origem é fornecido usando `SPXSourceLanguageConfiguration` . Em seguida, o `SPXSourceLanguageConfiguration` é passado como um parâmetro a ser `SPXSpeechRecognizer` construído.

```Objective-C
SPXSourceLanguageConfiguration* sourceLanguageConfig = [[SPXSourceLanguageConfiguration alloc]init:@"de-DE"];
SPXSpeechRecognizer* speechRecognizer = [[SPXSpeechRecognizer alloc] initWithSpeechConfiguration:speechConfig
                                                                     sourceLanguageConfiguration:sourceLanguageConfig
                                                                              audioConfiguration:audioConfig];
```

No exemplo a seguir, o idioma de origem e o ponto de extremidade personalizado são fornecidos usando `SPXSourceLanguageConfiguration` . Em seguida, o `SPXSourceLanguageConfiguration` é passado como um parâmetro a ser `SPXSpeechRecognizer` construído.

```Objective-C
SPXSourceLanguageConfiguration* sourceLanguageConfig = \
        [[SPXSourceLanguageConfiguration alloc]initWithLanguage:@"de-DE"
                                                     endpointId:@"The Endpoint ID for your custom model."];
SPXSpeechRecognizer* speechRecognizer = [[SPXSpeechRecognizer alloc] initWithSpeechConfiguration:speechConfig
                                                                     sourceLanguageConfiguration:sourceLanguageConfig
                                                                              audioConfiguration:audioConfig];
```

>[!Note]
> `speechRecognitionLanguage``endpointId`as propriedades e são preteridas da `SPXSpeechConfiguration` classe em Objective-C. O uso dessas propriedades não é recomendado, e elas não devem ser usadas durante a construção de um `SPXSpeechRecognizer` .

::: zone-end

## <a name="see-also"></a>Confira também

* Para obter uma lista de idiomas com suporte e localidades de fala para texto, consulte [suporte a idiomas](language-support.md).

## <a name="next-steps"></a>Próximas etapas

* [Documentação de referência do SDK de fala](speech-sdk.md)