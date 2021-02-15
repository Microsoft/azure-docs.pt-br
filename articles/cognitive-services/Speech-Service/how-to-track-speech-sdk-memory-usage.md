---
title: Como controlar o uso de memória do SDK de fala-serviço de fala
titleSuffix: Azure Cognitive Services
description: O SDK do serviço de fala dá suporte a várias linguagens de programação para conversão de fala em texto e de texto em fala, juntamente com a tradução de fala. Este artigo aborda as ferramentas de gerenciamento de memória integradas ao SDK.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: rhurey
ms.custom: devx-track-csharp
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: c7e74ce8f4d0b7889d2e5bcd117eaa43e2b6991b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88934133"
---
# <a name="how-to-track-speech-sdk-memory-usage"></a>Como controlar o uso de memória do SDK de fala

O SDK de fala é baseado em uma base de código nativa projetada em várias linguagens de programação por meio de uma série de camadas de interoperabilidade. Cada projeção específica de idioma tem recursos idiomatically corretos para gerenciar o ciclo de vida do objeto. Além disso, o SDK de fala inclui ferramentas de gerenciamento de memória para acompanhar o uso de recursos com o log de objetos e limites de objetos. 

## <a name="how-to-read-object-logs"></a>Como ler logs de objetos

Se o [log do SDK de fala estiver habilitado](how-to-use-logging.md), as marcas de rastreamento serão emitidas para habilitar a observação histórica do objeto. Essas marcas incluem: 

* `TrackHandle` ou `StopTracking` 
* O tipo de objeto
* O número atual de objetos que são rastreados no tipo do objeto e o número atual que está sendo acompanhado.

Aqui está um log de exemplo: 

```terminal
(284): 8604ms SPX_DBG_TRACE_VERBOSE:  handle_table.h:90 TrackHandle type=Microsoft::CognitiveServices::Speech::Impl::ISpxRecognitionResult handle=0x0x7f688401e1a0, ptr=0x0x7f688401e1a0, total=19
```

## <a name="set-a-warning-threshold"></a>Definir um limite de aviso

Você tem a opção de criar um limite de aviso e, se esse limite for excedido (supondo que o log esteja habilitado), uma mensagem de aviso será registrada. A mensagem de aviso contém um despejo de todos os objetos em existência junto com sua contagem. Essas informações podem ser usadas para entender melhor os problemas. 

Para habilitar um limite de aviso, ele deve ser especificado em um `SpeechConfig` objeto. Esse objeto é verificado quando um novo reconhecedor é criado. Nos exemplos a seguir, vamos supor que você criou uma instância do `SpeechConfig` chamada `config` :

::: zone pivot="programming-language-csharp"

```csharp
config.SetProperty("SPEECH-ObjectCountWarnThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
config->SetProperty("SPEECH-ObjectCountWarnThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-java"

```java
config.setProperty("SPEECH-ObjectCountWarnThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
speech_config.set_property_by_name("SPEECH-ObjectCountWarnThreshold", "10000")?
```

::: zone-end

::: zone pivot="programming-language-more"

```ObjectiveC
[config setPropertyTo:@"10000" byName:"SPEECH-ObjectCountWarnThreshold"];
```

::: zone-end

> [!TIP]
> O valor padrão dessa propriedade é 10.000.

## <a name="set-an-error-threshold"></a>Definir um limite de erro 

Usando o SDK de fala, você pode definir o número máximo de objetos permitidos em um determinado momento. Se essa configuração estiver habilitada, quando o número máximo for atingido, haverá falha nas tentativas de criar novos objetos Recognizer. Os objetos existentes continuarão a funcionar.

Veja um erro de exemplo:

```terminal
Runtime error: The maximum object count of 500 has been exceeded.
The threshold can be adjusted by setting the SPEECH-ObjectCountErrorThreshold property on the SpeechConfig object.
See http://https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-object-tracking-speech-sdk for more detailed information.
Handle table dump by ojbect type:
class Microsoft::CognitiveServices::Speech::Impl::ISpxRecognitionResult 0
class Microsoft::CognitiveServices::Speech::Impl::ISpxRecognizer 0
class Microsoft::CognitiveServices::Speech::Impl::ISpxAudioConfig 0
class Microsoft::CognitiveServices::Speech::Impl::ISpxSpeechConfig 0
```

Para habilitar um limite de erro, ele deve ser especificado em um `SpeechConfig` objeto. Esse objeto é verificado quando um novo reconhecedor é criado. Nos exemplos a seguir, vamos supor que você criou uma instância do `SpeechConfig` chamada `config` :

::: zone pivot="programming-language-csharp"

```csharp
config.SetProperty("SPEECH-ObjectCountErrorThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
config->SetProperty("SPEECH-ObjectCountErrorThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-java"

```java
config.setProperty("SPEECH-ObjectCountErrorThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
speech_config.set_property_by_name("SPEECH-ObjectCountErrorThreshold", "10000")?
```

::: zone-end

::: zone pivot="programming-language-more"

```objc
[config setPropertyTo:@"10000" byName:"SPEECH-ObjectCountErrorThreshold"];
```

::: zone-end

> [!TIP]
> O valor padrão dessa propriedade é o valor máximo específico da plataforma para um `size_t` tipo de dados. Um reconhecimento típico consumirá entre 7 e 10 objetos internos.

## <a name="next-steps"></a>Próximas etapas

* [Saiba mais sobre o SDK de fala](speech-sdk.md)