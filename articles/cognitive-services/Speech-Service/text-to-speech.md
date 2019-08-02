---
title: Conversão de texto em fala-serviço de fala
titleSuffix: Azure Cognitive Services
description: O recurso de conversão de texto em fala no serviço de fala é um recurso que permite que seus aplicativos, ferramentas ou dispositivos convertam texto em fala sintetizada semelhante à humana. Escolha entre as vozes padrão e neural ou crie sua própria voz personalizada exclusiva para seu produto ou marca. 75 vozes padrão estão disponíveis em mais de 45 idiomas e localidades, e 5 vozes neurais estão disponíveis em 4 idiomas e localidades.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: erhopf
ms.openlocfilehash: 3d2c3e2bf693f763db13d90961a31e871aa25235
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68558869"
---
# <a name="what-is-text-to-speech"></a>O que é conversão de texto em fala?

Conversão de texto em fala dos serviços de fala do Azure é um serviço que permite que seus aplicativos, ferramentas ou dispositivos convertam texto em fala sintetizada semelhante à humana. Escolha entre as vozes padrão e neural ou crie sua própria voz personalizada exclusiva para seu produto ou marca. 75 vozes padrão estão disponíveis em mais de 45 idiomas e localidades, e 5 vozes neurais estão disponíveis em 4 idiomas e localidades. Para obter uma lista completa, consulte [idiomas com suporte](language-support.md#text-to-speech).

A tecnologia de conversão de texto em fala permite que os criadores de conteúdo interajam com seus usuários de maneiras diferentes. A conversão de texto em fala pode melhorar a acessibilidade, fornecendo aos usuários uma opção para interagir com o conteúdo forma audível. Se o usuário tem um deficiência visual, uma deficiência de aprendizado ou requer informações de navegação enquanto conduz, a conversão de texto em fala pode melhorar uma experiência existente. Conversão de texto em fala também é um complemento valioso para bots de voz e assistentes virtuais.


Ao aproveitar a linguagem de marcação de síntese de fala (SSML), uma linguagem de marcação baseada em XML, os desenvolvedores que usam o serviço de conversão de texto em fala podem especificar como o texto de entrada é convertido em fala sintetizada. Com a SSML, você pode ajustar a densidade, a pronúncia, a taxa de fala, o volume e muito mais. Para obter mais informações, consulte [SSML](#speech-synthesis-markup-language-ssml).

### <a name="standard-voices"></a>Vozes padrão

As vozes padrão são criadas usando as técnicas de síntese paramétricas estatística e/ou síntese de concatenação. Essas vozes são altamente inteligível e sólidas. Você pode facilmente permitir que seus aplicativos falem em mais de 45 idiomas, com uma ampla gama de opções de voz. Essas vozes fornecem alta precisão de pronúncia, incluindo suporte para abreviações, expansões de acrônimo, interpretações de data/hora, telefones e muito mais. Use voz padrão para aprimorar a acessibilidade para seus aplicativos e serviços, permitindo que os usuários interajam com o conteúdo forma audível.

### <a name="neural-voices"></a>Vozes neurais

As vozes neurais usam redes neurais profundas para superar os limites de sistemas de conversão de texto em fala tradicionais na correspondência dos padrões de estresse e intonation na linguagem falada e no sintetizamento das unidades de fala em uma voz de computador. A conversão de texto em fala padrão prosody em análise linguística separada e etapas de previsão acústica que são governadas por modelos independentes, o que pode resultar em síntese de voz muffled. Nosso recurso neural Prosody a previsão e a síntese de voz simultaneamente, o que resulta em uma voz mais fluida e de som natural.

Vozes neurais podem ser usadas para interagir com chatbots e assistentes virtuais de forma mais natural e participativa para converter textos digitais, como livros eletrônicos, em audiolivros, e aprimorar sistemas de navegação de carros. Com a Prosody natural humana e a clara Articulation de palavras, as vozes neurais reduzem significativamente a escuta de fadiga quando você interage com sistemas de ia.

As vozes neurais dão suporte a diferentes estilos, como neutral e cheerful. Por exemplo, a voz Jessa (en-US) pode falar cheerfully, que é otimizado para uma conversa boa e feliz. Você pode ajustar a saída de voz, como Tom, densidade e velocidade usando a [linguagem de marcação de síntese de fala](speech-synthesis-markup.md). Para obter uma lista completa de vozes disponíveis, consulte [idiomas com suporte](language-support.md#text-to-speech).

Para saber mais sobre os benefícios das vozes neurais, consulte [o novo serviço de conversão de texto em fala do neural da Microsoft ajuda as máquinas a falarem como pessoas](https://azure.microsoft.com/blog/microsoft-s-new-neural-text-to-speech-service-helps-machines-speak-like-people/).

### <a name="custom-voices"></a>Vozes personalizadas

A personalização de voz permite que você crie uma voz de um tipo reconhecível para sua marca. Para criar sua fonte de voz personalizada, você faz uma gravação no estúdio e carrega os scripts associados como os dados de treinamento. Em seguida, o serviço cria um modelo de voz exclusivo ajustado para a gravação. Você pode usar essa fonte de voz personalizada para sintetizar a fala. Para obter mais informações, consulte [vozes personalizadas](how-to-customize-voice-font.md).

## <a name="speech-synthesis-markup-language-ssml"></a>Linguagem de marcação de síntese de fala (SSML)

A linguagem de marcação de síntese de fala (SSML) é uma linguagem de marcação baseada em XML que permite aos desenvolvedores especificar como o texto de entrada é convertido em fala sintetizada usando o serviço de conversão de texto em fala. Em comparação com o texto sem formatação, o SSML permite que os desenvolvedores ajustem a densidade, a pronúncia, a taxa de fala, o volume e muito mais da saída de conversão de texto em fala. Pontuação normal, como pausar após um período ou usar o intonation correto quando uma frase termina com um ponto de interrogação é manipulada automaticamente.

Todas as entradas de texto enviadas ao serviço de conversão de texto em fala devem ser estruturadas como SSML. Para obter mais informações, consulte [linguagem de marcação de síntese de fala](speech-synthesis-markup.md).

### <a name="pricing-note"></a>Nota de preços

Ao usar o serviço de conversão de texto em fala, você será cobrado por cada caractere convertido em fala, incluindo pontuação. Embora o documento SSML em si não seja Faturável, elementos opcionais usados para ajustar como o texto é convertido em fala, como fonemas e pitch, são contados como caracteres faturáveis. Aqui está uma lista do que é Faturável:

* Texto passado para o serviço de conversão de texto em fala no corpo de SSML da solicitação
* Todas as marcações dentro do campo de texto do corpo da solicitação no formato SSML, `<speak>` exceto `<voice>` para marcas e
* Letras, pontuação, espaços, tabulações, marcação e todos os caracteres de espaço em branco
* Cada ponto de código definido no Unicode

Para obter informações detalhadas, consulte [preços](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

> [!IMPORTANT]
> Cada caractere de idioma chinês, japonês e coreano é contado como dois caracteres para cobrança.

## <a name="core-features"></a>Principais recursos

Esta tabela lista os principais recursos para conversão de texto em fala:

| Caso de uso | SDK | REST |
|----------|-----|------|
| Converter texto em fala. | Sim | Sim |
| Carregar conjuntos de valores para adaptação de voz. | Não | Sim\* |
| Crie e gerencie modelos de fonte de voz. | Não | Sim\* |
| Crie e gerencie implantações de fontes de voz. | Não | Sim\* |
| Criar e gerenciar testes de fonte de voz. | Não | Sim\* |
| Gerenciar assinaturas. | Não | Sim\* |

\**Esses serviços estão disponíveis usando o ponto de extremidade Cris.ai. Consulte [referência do Swagger](https://westus.cris.ai/swagger/ui/index). Essas APIs de treinamento e gerenciamento de voz personalizadas implementam a limitação que limita as solicitações a 25 por 5 segundos, enquanto a própria API de síntese de fala implementa a limitação que permite 200 solicitações por segundo como a mais alta. Quando a limitação ocorrer, você será notificado por meio de cabeçalhos de mensagem.*

## <a name="get-started-with-text-to-speech"></a>Introdução ao texto para fala

Oferecemos guias de início rápido projetados para que você execute códigos em menos de 10 minutos. Esta tabela inclui uma lista de guias de início rápido de conversão de texto em fala organizadas por idioma.

### <a name="sdk-quickstarts"></a>Guias de início rápido do SDK

| Início rápido (SDK) | Plataforma | Referência da API |
|------------|----------|---------------|
| [C#, .NET Core](quickstart-text-to-speech-dotnetcore.md) | Windows | [Procurar](https://aka.ms/csspeech/csharpref) |
| [C#, .NET Framework](quickstart-text-to-speech-dotnet-windows.md) | Windows | [Procurar](https://aka.ms/csspeech/csharpref) |
| [C#, UWP](quickstart-text-to-speech-csharp-uwp.md) | Windows | [Procurar](https://aka.ms/csspeech/csharpref) |
| [C#, Unity](quickstart-text-to-speech-csharp-unity.md) | Windows, Android | [Procurar](https://aka.ms/csspeech/csharpref) |
| [C++](quickstart-text-to-speech-cpp-windows.md) | Windows | [Procurar](https://aka.ms/csspeech/cppref) |
| [C++](quickstart-text-to-speech-cpp-linux.md) | Linux | [Procurar](https://aka.ms/csspeech/cppref) |

### <a name="rest-quickstarts"></a>Guias de início rápido do REST

| Início rápido (REST) | Plataforma | Referência da API |
|------------|----------|---------------|
| [C#, .NET Core](quickstart-dotnet-text-to-speech.md) | Windows, macOS, Linux | [Procurar](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Node.js](quickstart-nodejs-text-to-speech.md) | Windows, macOS, Linux | [Procurar](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Python](quickstart-python-text-to-speech.md) | Windows, macOS, Linux | [Procurar](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |

## <a name="sample-code"></a>Código de exemplo

O código de exemplo para conversão de texto em fala está disponível no GitHub. Esses exemplos abordam a conversão de texto em fala nas linguagens de programação mais populares.

* [Exemplos de conversão de texto em fala (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
* [Exemplos de conversão de texto em fala (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="reference-docs"></a>Documentos de Referência

* [SDK da fala](speech-sdk-reference.md)
* [SDK de Dispositivos de Fala](speech-devices-sdk.md)
* [API REST: conversão de fala em texto](rest-speech-to-text.md)
* [API REST: conversão de texto em fala](rest-text-to-speech.md)
* [API REST: transcrição e personalização em lote](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Próximas etapas

* [Obter uma assinatura gratuita dos Serviços de Fala](get-started.md)
* [Criar fontes de voz personalizadas](how-to-customize-voice-font.md)
