---
title: Início rápido de conversão de texto em fala – serviço de fala
titleSuffix: Azure Cognitive Services
description: Saiba como usar o SDK de Fala para converter texto em fala. Neste guia de início rápido, você aprenderá sobre construção de objetos e padrões de design, formatos de saída de áudio compatíveis, a CLI de Fala e opções de configuração personalizadas para síntese de fala.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/01/2020
ms.author: trbye
ms.custom: devx-track-python, devx-track-js, devx-track-csharp, cog-serv-seo-aug-2020
zone_pivot_groups: programming-languages-set-twenty-four
keywords: conversão de texto em fala
ms.openlocfilehash: 7a41c4d9c1074b376da3de556caf63ced0bc84ec
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102428161"
---
# <a name="get-started-with-text-to-speech"></a>Introdução à conversão de texto em fala

::: zone pivot="programming-language-csharp"
[!INCLUDE [C# Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-csharp.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [C++ Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-cpp.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-java.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-javascript.md)]
::: zone-end

::: zone pivot="programming-languages-objectivec-swift"
[!INCLUDE [Objective-C and Swift Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-objectivec-swift.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-python.md)]
::: zone-end

::: zone pivot="programming-language-curl"
[!INCLUDE [REST include](includes/how-to/text-to-speech-basics/text-to-speech-basics-curl.md)]
::: zone-end

::: zone pivot="programmer-tool-spx"
[!INCLUDE [CLI Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-cli.md)]
::: zone-end

## <a name="get-position-information"></a>Obter informações de posição

Seu projeto pode precisar saber quando uma palavra é falada por conversão de fala em texto para que ele possa executar uma ação específica com base no tempo em que isso ocorre. Por exemplo, se você quisesse realçar palavras conforme elas fossem faladas, precisaria saber o que realçar, quando realçar essas palavras e por quanto tempo realçá-las.

Você pode fazer isso usando o evento `WordBoundary` disponível em `SpeechSynthesizer`. Esse evento é gerado no início de cada nova palavra falada e fornecerá uma diferença de tempo dentro do fluxo falado, bem como um deslocamento de texto dentro do prompt de entrada.

* `AudioOffset` relata o tempo decorrido entre o início da síntese e o início da próxima palavra na saída de áudio. Isso é medido na unidade HNS (100 nanossegundos), com 10.000 HNS sendo equivalentes a um milissegundo.
* `WordOffset` relata a posição do caractere na cadeia de caracteres de entrada (texto original ou [SSML](speech-synthesis-markup.md)) imediatamente antes da palavra que está prestes a ser falada.

> [!NOTE]
> Os eventos `WordBoundary` são gerados à medida que os dados de áudio de saída ficam disponíveis, o que será mais rápido do que a reprodução em um dispositivo de saída. A sincronização apropriada da temporização de fluxo para "tempo real" deve ser realizada pelo chamador.

Você pode encontrar exemplos de uso de `WordBoundary` nos [exemplos de conversão de texto em fala](https://aka.ms/csspeech/samples) no GitHub.

## <a name="next-steps"></a>Próximas etapas

* [Introdução à Voz Personalizada](how-to-custom-voice.md)
* [Aprimorar a sintetização com SSML](speech-synthesis-markup.md)
* Saiba como usar a [API de áudio longo](long-audio-api.md) para amostras de texto grande, como livros e artigos de notícias
* Consulte as [amostras do guia de início rápido](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart) no GitHub
