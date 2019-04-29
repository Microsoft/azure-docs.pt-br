---
title: Conversão de fala em texto com os serviços de fala do Azure
titleSuffix: Azure Cognitive Services
description: Conversão de fala em texto fala dos serviços do Azure, também conhecido como conversão de fala em texto, habilita a transcrição em tempo real de fluxos de áudio em texto que podem consumir seus aplicativos, ferramentas ou dispositivos, exibir e agir como entrada de comando. Esse serviço estiver usando a mesma tecnologia de reconhecimento que a Microsoft usa para produtos Cortana e do Office e funciona perfeitamente com o texto em fala e conversão.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 9e6bc1264e668ba5c6593ce36e721f54e685c391
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61461248"
---
# <a name="what-is-speech-to-text"></a>O que é a conversão de fala em texto?

Conversão de fala em texto fala dos serviços do Azure, também conhecido como conversão de fala em texto, habilita a transcrição em tempo real de fluxos de áudio em texto que podem consumir seus aplicativos, ferramentas ou dispositivos, exibir e agir como entrada de comando. Esse serviço estiver usando a mesma tecnologia de reconhecimento que a Microsoft usa para produtos Cortana e do Office e funciona perfeitamente com o texto em fala e conversão.  Para obter uma lista completa de idiomas de fala em texto disponíveis, consulte [idiomas com suporte](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/language-support#speech-to-text).

Por padrão, o serviço de fala em texto usa o modelo de linguagem Universal. Esse modelo foi treinado usando dados de propriedade da Microsoft e é implantado na nuvem. Ele é ideal para o formato de conversação e cenários de ditado. Se estiver usando a conversão de fala em texto para funcionalidades de reconhecimento e transcrição em um ambiente exclusivo, você poderá criar e treinar modelos acústicos, de idioma e de pronúncia personalizados para lidar com o ruído ambiente ou vocabulário específico do setor. 

Você pode facilmente capturar áudio do microfone, ler de um fluxo ou acessar arquivos de áudio de armazenamento com o SDK de fala e APIs REST. O Speech SDK dá suporte a WAV/PCM de 16 bits, 16 kHz, canal único áudio para reconhecimento de fala. Há suporte para formatos de áudio adicionais usando o [ponto de extremidade REST de fala em texto](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) ou o [transcrição de serviço de lote](https://docs.microsoft.com/azure/cognitive-services/speech-service/batch-transcription#supported-formats).

## <a name="core-features"></a>Principais recursos

Aqui estão os recursos disponíveis por meio do SDK de fala e APIs REST:

| Caso de uso | . | REST |
|----------|-----|------|
| Transcrever declarações curtas (< 15 segundos). Suporta apenas o resultado final de transcrição. | Sim | Sim |
| Transcrição contínua de declarações longo e fluxo de áudio (> 15 segundos). Oferece suporte a resultados de transcrição intermediárias e finais. | Sim | Não  |
| Derivar as intenções de resultados do reconhecimento com [LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/what-is-luis). | Sim | Não\* |
| Transcrição de arquivos de áudio do lote assincronamente. | Não  | Sim\** |
| Criar e gerenciar modelos de fala. | Não  | Sim\** |
| Criar e gerenciar implantações de modelo personalizado. | Não  | Sim\** |
| Crie testes de precisão para medir a precisão do modelo de linha de base versus modelos personalizados. | Não  | Sim\** |
| Gerencie assinaturas. | Não  | Sim\** |

\* *entidades e intenções de LUIS podem ser derivadas usando uma assinatura do LUIS separada. Com essa assinatura, o SDK pode chamar LUIS para você e fornecer a entidade e intenção de resultados. Com a API REST, você mesmo pode chamar o LUIS para derivar intenções e entidades com assinatura do LUIS.*

\** *Esses serviços estão disponíveis usando o ponto de extremidade cris.ai. Ver [Swagger referência](https://westus.cris.ai/swagger/ui/index).*

## <a name="get-started-with-speech-to-text"></a>Introdução a fala em texto

Nós oferecemos guias de início rápido nas linguagens de programação mais populares, todos eles desenvolvidos para que você executar o código em menos de 10 minutos. Essa tabela inclui uma lista completa dos guias de início rápido do SDK de fala organizados por idioma.

| Início rápido | Plataforma | Referência de API |
|------------|----------|---------------|
| [C#, .NET Core](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-csharp-dotnetcore-windows) |  Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| [C#, .NET Framework](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-csharp-dotnet-windows) |  Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| [C#, UWP](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-csharp-uwp) |  Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| [C++](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-cpp-windows) |  Windows | [Browse](https://aka.ms/csspeech/cppref)|
| [C++](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-cpp-linux) | Linux | [Browse](https://aka.ms/csspeech/cppref) |
| [Java](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-java-android) | Android | [Browse](https://aka.ms/csspeech/javaref) |
| [Java](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-java-jre) | Windows, Linux | [Browse](https://aka.ms/csspeech/javaref) |
| [JavaScript, o navegador](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-js-browser) | Browser, Windows, Linux, macOS | [Browse](https://aka.ms/AA434tv) |
| [JavaScript, Node.js](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-js-node) | Windows, Linux, macOS | [Browse](https://aka.ms/AA434tv) |
| [Objective-C](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-objectivec-ios) | iOS | [Browse](https://aka.ms/csspeech/objectivecref) |
| [Python](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-python) | Windows, Linux, macOS | [Browse](https://aka.ms/AA434tr)  |

Se você preferir usar o serviço REST de fala em texto, consulte [APIs REST](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

## <a name="tutorials-and-sample-code"></a>Tutoriais e código de exemplo

Depois que tiver a oportunidade de usar o Serviço de Fala, experimente nosso tutorial que ensina a reconhecer intenções de fala usando o SDK de Fala e o LUIS.

* [Tutorial: Reconhecer intenções de fala com o SDK de Fala e o LUIS, C#](how-to-recognize-intents-from-speech-csharp.md)

Código de exemplo para o Speech SDK está disponível no GitHub. Esses exemplos abordam cenários comuns, como ler áudio de um arquivo ou fluxo, reconhecimento contínuo e único e trabalho com modelos personalizados.

* [Exemplos de fala em texto (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
* [Exemplos de transcrição em lote (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)

## <a name="customization"></a>Personalização

Além do modelo Universal usado pelos serviços de fala, você pode criar modelos personalizados de acústicos, idioma e pronúncia específica à sua experiência. Aqui está uma lista de opções de personalização:

| Modelo | DESCRIÇÃO |
|-------|-------------|
| [Modelo acústico](how-to-customize-acoustic-models.md) | Criar um modelo acústico personalizado é útil se seu aplicativo, ferramentas ou dispositivos são usados em um ambiente específico, como em um carro ou fábrica com condições específicas de gravação. Os exemplos envolvem fala e ruídos de fundo específicos ou o uso de um microfone específico para gravação. |
| [Modelo de linguagem](how-to-customize-language-model.md) | Crie um modelo de linguagem personalizados para melhorar a transcrição do vocabulário específico do setor e a gramática, como a terminologia médica ou jargão IT. |
| [Modelo de pronúncia](how-to-customize-pronunciation.md) | Usando um modelo de pronúncia personalizado, você pode definir a forma fonética e a exibição de uma palavra ou termo. É útil para lidar com termos personalizados, como nomes de produtos ou acrônimos. Tudo o que você precisa para começar é de um arquivo de pronúncia (um arquivo .txt simples). |

> [!NOTE]
> Opções de personalização variam por idioma/localidade (consulte [idiomas com suporte](supported-languages.md)).

## <a name="migration-guides"></a>Guias de migração

> [!WARNING]
> Fala do Bing será desativada em 15 de outubro de 2019.

Se seus aplicativos, ferramentas ou produtos estiver usando as APIs de fala do Bing ou conversão de fala personalizado, criamos guias para ajudá-lo a migrar para os serviços de fala.

* [Migrar de fala do Bing para os serviços de fala](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-migrate-from-bing-speech)
* [Migrar de fala personalizado para os serviços de fala](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-migrate-from-custom-speech-service)

## <a name="reference-docs"></a>Documentos de Referência

* [SDK da fala](speech-sdk-reference.md)
* [SDK de Dispositivos de Fala](speech-devices-sdk.md)
* [API REST: conversão de fala em texto](rest-speech-to-text.md)
* [API REST: conversão de texto em fala](rest-text-to-speech.md)
* [API REST: transcrição e personalização em lote](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Próximos passos

* [Obter gratuitamente uma chave de assinatura do Serviço de Fala](get-started.md)
* [Obtenha o SDK da fala](speech-sdk.md)
