---
title: O que é o Serviço de Fala do Azure?
titleSuffix: Azure Cognitive Services
description: O Serviço de Fala do Azure é a unificação da conversão de fala em texto, da conversão de texto em fala e da tradução de fala em uma única assinatura do Azure. É fácil adicionar serviços de fala a seus aplicativos, ferramentas e dispositivos usando o SDK de Fala, o SDK de Dispositivos de Fala ou APIs REST. Adicione funcionalidades de fala a um chatbot existente, converta texto em fala em um aplicativo de tradução ou transcreva grandes volumes de dados de call center.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: overview
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: d9664ac9fb72a5f094674856a20230199270f01d
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67603159"
---
# <a name="what-are-the-speech-services"></a>O que é o Serviço de Fala?

O Serviço de Fala do Azure é a unificação do reconhecimento de fala, da conversão de texto em fala e da tradução de fala em uma única assinatura do Azure. É fácil habilitar serviços de fala em seus aplicativos, ferramentas e dispositivos usando o [SDK de Fala](speech-sdk-reference.md), o [SDK de Dispositivos de Fala](https://aka.ms/sdsdk-quickstart) ou [APIs REST](rest-apis.md).

> [!IMPORTANT]
> O Serviço de Fala substituiu a API de Fala do Bing, a Tradução de Fala e a Fala Personalizada. Confira *Guias de instruções > Migração* para obter instruções de migração.

Esses recursos compõem o Serviço de Fala do Azure. Use os links nesta tabela para saber mais sobre os casos de uso comuns de cada recurso ou navegue pela referência de API.

| Serviço | Recurso | DESCRIÇÃO | . | REST |
|---------|---------|-------------|-----|------|
| [Conversão de fala em texto](speech-to-text.md) | Conversão de fala em texto | A conversão de fala em texto transcreve, em tempo real, fluxos de áudio em texto que seus aplicativos, ferramentas ou dispositivos podem consumir ou exibir. Use a conversão de fala em texto com o [LUIS (Reconhecimento vocal)](https://docs.microsoft.com/azure/cognitive-services/luis/) para derivar intenções do usuário de dados de fala transcrita e agir com base em comandos de voz. | [Sim](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [Sim](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [Transcrição em lote](batch-transcription.md) | A Transcrição em lote permite a transcrição de fala em texto assíncrona de grandes volumes de dados. Trata-se de um serviço baseado em REST que usa o mesmo ponto de extremidade que o gerenciamento de modelos e a personalização. | Não | [Sim](https://westus.cris.ai/swagger/ui/index) |
| | [Transcrição de Conversas](conversation-transcription-service.md) | Permite o reconhecimento de fala em tempo real, a identificação do locutor e a diarização. É perfeito para transcrição de reuniões presenciais com a capacidade de distinguir os locutores. | Sim | Não |
| | [Criar Modelos de Fala Personalizada](#customize-your-speech-experience) | Se estiver usando a conversão de fala em texto para funcionalidades de reconhecimento e transcrição em um ambiente exclusivo, você poderá criar e treinar modelos acústicos, de idioma e de pronúncia personalizados para lidar com o ruído ambiente ou vocabulário específico do setor. | Não | [Sim](https://westus.cris.ai/swagger/ui/index) |
| [Conversão de Texto em Fala](text-to-speech.md) | Conversão de texto em fala | A conversão de texto em fala converte o texto de entrada em uma fala sintetizada semelhante à humana usando [Linguagem de marcação de síntese de fala (SSML)](text-to-speech.md#speech-synthesis-markup-language-ssml). Escolha entre as vozes padrão e vozes neurais (consulte [Suporte para idiomas](language-support.md)). | [Sim](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [Sim](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [Criar Vozes Personalizadas](#customize-your-speech-experience) | Crie fontes de voz personalizadas exclusivas para sua marca ou produto. | Não | [Sim](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Tradução de Fala](speech-translation.md) | Tradução de fala | A tradução de fala possibilita a tradução em tempo real e em vários idiomas de fala para seus aplicativos, suas ferramentas e seus dispositivos. Use esse serviço para tradução de fala em fala e fala em texto. | [Sim](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | Não |
| [Assistentes virtuais que têm como prioridade o uso de voz](voice-first-virtual-assistants.md) | Assistentes virtuais que têm como prioridade o uso de voz | Assistentes virtuais personalizados usando os Serviços de Fala do Azure capacitam os desenvolvedores a criar interfaces de conversação natural, semelhante à humana, para seus aplicativos e suas experiências. O Canal de Fala de Direct Line do Bot Framework aprimora essas funcionalidades fornecendo um ponto de entrada coordenado e orquestrado para um bot compatível que permite a interação com entrada e saída de voz com baixa latência e alta confiabilidade. | [Sim](voice-first-virtual-assistants.md) | Não |

## <a name="news-and-updates"></a>Novidades e atualizações

Saiba o que há de novo no Serviço de Fala do Azure.

* Junho de 2019
    * Lançamento do SDK de Fala 1.6.0. Para obter uma lista completa de atualizações, melhorias e problemas conhecidos, confira [Notas sobre a versão](releasenotes.md).
* Maio de 2019 – A documentação já está disponível para [Transcrição de Conversas](conversation-transcription-service.md), [Transcrição de Call Center](call-center-transcription.md) e [Assistentes Virtuais que têm como prioridade o uso de voz](voice-first-virtual-assistants.md).
* Maio de 2019
    * Lançamento do SDK de Fala 1.5.1. Para obter uma lista completa de atualizações, melhorias e problemas conhecidos, confira [Notas sobre a versão](releasenotes.md).
    * Lançamento do SDK de Fala 1.5.0. Para obter uma lista completa de atualizações, melhorias e problemas conhecidos, confira [Notas sobre a versão](releasenotes.md).
* Abril de 2019 – lançamento do SDK de Fala 1.4.0 com suporte para conversão de texto em fala (beta) para C++, C# e Java no Windows e no Linux. Além disso, o SDK agora dá suporte aos formatos de áudio MP3 e Opus/Ogg para C++ e C# no Linux. Para obter uma lista completa de atualizações, melhorias e problemas conhecidos, confira [Notas sobre a versão](releasenotes.md).
* Março de 2019 – um novo ponto de extremidade de TTS (conversão de texto em fala) que retorna uma lista completa das vozes disponíveis em uma região específica está disponível. Além disso, agora há suporte para as novas regiões para TTS. Para obter mais informações, veja [Referência da API de conversão de texto em fala (REST)](rest-text-to-speech.md).

## <a name="try-speech-services"></a>Experimente o Serviço de Fala

Nós oferecemos guias de início rápido nas linguagens de programação mais populares, todos eles desenvolvidos para que você executar o código em menos de 10 minutos. Esta tabela contém os guias de início rápido mais populares de cada recurso. Use o painel de navegação esquerdo para explorar plataformas e linguagens adicionais.

| Conversão de fala em texto (SDK) | Conversão de texto em fala (SDK) | Tradução (SDK) |
|----------------------|----------------------|-------------------|
| [C#, .NET Core (Windows)](quickstart-csharp-dotnet-windows.md) | [C#, .NET Framework (Windows)](quickstart-text-to-speech-dotnet-windows.md) | [Java (Windows, Linux)](quickstart-translate-speech-java-jre.md) |
| [JavaScript (Navegador)](quickstart-js-browser.md) | [C++ (Windows)](quickstart-text-to-speech-cpp-windows.md) | [C#, .NET Core (Windows)](quickstart-translate-speech-dotnetcore-windows.md) |
| [Python (Windows, Linux, macOS)](quickstart-python.md) | [C++ (Linux)](quickstart-text-to-speech-cpp-linux.md) | [C#, .NET Framework (Windows)](quickstart-translate-speech-dotnetframework-windows.md) |
| [Java (Windows, Linux)](quickstart-java-jre.md) | | [C++ (Windows)](quickstart-translate-speech-cpp-windows.md) |

> [!NOTE]
> A conversão de fala em texto e de texto em fala também tem pontos de extremidade REST e inícios rápidos associados.

Depois que tiver a oportunidade de usar o Serviço de Fala, experimente nosso tutorial que ensina a reconhecer intenções de fala usando o SDK de Fala e o LUIS.

* [Tutorial: Reconhecer intenções de fala com o SDK de Fala e o LUIS, C#](how-to-recognize-intents-from-speech-csharp.md)
* [Tutorial: Criar um aplicativo Flask para traduzir texto, analisar o sentimento e sintetizar a conversão de texto traduzido em fala, REST](https://docs.microsoft.com/azure/cognitive-services/translator/tutorial-build-flask-app-translation-synthesis?toc=%2fazure%2fcognitive-services%2fspeech-service%2ftoc.json&bc=%2fazure%2fcognitive-services%2fspeech-service%2fbreadcrumb%2ftoc.json&toc=%2Fen-us%2Fazure%2Fcognitive-services%2Fspeech-service%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)

## <a name="get-sample-code"></a>Obter código de exemplo

Há um código de exemplo disponível no GitHub para cada um dos Serviços de Fala do Azure. Esses exemplos abordam cenários comuns, como ler áudio de um arquivo ou fluxo, reconhecimento contínuo e único e trabalho com modelos personalizados. Use estes links para exibir exemplos de SDK e REST:

* [Amostras de conversão de fala em texto, conversão de texto em fala e tradução de fala (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
* [Exemplos de transcrição em lote (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
* [Exemplos de conversão de texto em fala (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="customize-your-speech-experience"></a>Personalize sua experiência de fala

O Serviço de Fala do Azure funciona bem com modelos internos, no entanto, talvez você queira personalizar e ajustar ainda mais a experiência para seu ambiente ou produto. As opções de personalização vão do ajuste do modelo acústico a fontes de voz exclusivas para sua marca. Depois de criar um modelo personalizado, você pode usá-lo com qualquer um dos Serviços de Fala do Azure.

| Serviço de Fala | Modelo | DESCRIÇÃO |
|----------------|-------|-------------|
| Conversão de fala em texto | [Modelo acústico](how-to-customize-acoustic-models.md) | Crie um modelo acústico personalizado para aplicativos, ferramentas ou dispositivos usados em ambientes particulares, como um carro ou um chão de fábrica, cada um deles com condições específicas de gravação. Exemplos incluem fala com sotaque, ruídos de fundo específicos ou uso de um microfone específico para gravação. |
| | [Modelo de linguagem](how-to-customize-language-model.md) | Crie um modelo de linguagem personalizado para melhorar a transcrição do vocabulário e da gramática específicos de um campo, por exemplo, terminologia médica ou jargão de TI. |
| | [Modelo de pronúncia](how-to-customize-pronunciation.md) | Usando um modelo de pronúncia personalizado, você pode definir a forma fonética e a exibição de uma palavra ou termo. É útil para lidar com termos personalizados, como nomes de produtos ou acrônimos. Tudo o que você precisa para começar é de um arquivo de pronúncia (um arquivo .txt simples). |
| Conversão de texto em fala | [Fonte de voz](how-to-customize-voice-font.md) | Fontes de voz personalizadas permitem que você crie uma voz reconhecível exclusiva para sua marca. É necessária apenas uma pequena quantidade de dados para começar a usar. Quanto mais dados que você fornecer, mais natural e humana sua fonte de voz soará. |

## <a name="reference-docs"></a>Documentos de Referência

* [SDK da fala](speech-sdk-reference.md)
* [SDK de Dispositivos de Fala](speech-devices-sdk.md)
* [API REST: conversão de fala em texto](rest-speech-to-text.md)
* [API REST: conversão de texto em fala](rest-text-to-speech.md)
* [API REST: transcrição e personalização em lote](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Obter gratuitamente uma chave de assinatura do Serviço de Fala](get-started.md)
