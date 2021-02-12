---
title: Visão geral de conversão de texto em fala-serviço de fala
titleSuffix: Azure Cognitive Services
description: O recurso de conversão de texto em fala no serviço de fala permite que seus aplicativos, ferramentas ou dispositivos convertam texto em fala sintetizada semelhante à humana. Este artigo é uma visão geral dos benefícios e capacidades do serviço de conversão de texto em fala.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: trbye
ms.custom: cog-serv-seo-aug-2020
keywords: conversão de texto em fala
ms.openlocfilehash: 54db794d4e9cf0cd2a049a865f2e7bb52698a4bd
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/20/2021
ms.locfileid: "98599535"
---
# <a name="what-is-text-to-speech"></a>O que é conversão de texto em fala?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Nesta visão geral, você aprenderá sobre os benefícios e capacidades do serviço de conversão de texto em fala, que permite que seus aplicativos, ferramentas ou dispositivos convertam texto em fala sintetizada pelo tipo humano. Escolha entre vozes padrão e neural ou crie uma voz personalizada exclusiva para seu produto ou marca. 75 vozes padrão estão disponíveis em mais de 45 idiomas e localidades, e 5 vozes neurais estão disponíveis em um número selecionado de idiomas e localidades. Para obter uma lista completa de vozes, idiomas e localidades com suporte, consulte [idiomas com suporte](language-support.md#text-to-speech).

> [!NOTE]
> Fala do Bing foi encerrado em 15 de outubro de 2019. Se seus aplicativos, ferramentas ou produtos estiverem usando o Fala do Bing APIs ou Fala Personalizada, criamos guias para ajudá-lo a migrar para o serviço de fala.
> - [Migrar do Fala do Bing para o serviço de fala](how-to-migrate-from-bing-speech.md)

## <a name="core-features"></a>Principais recursos

* Síntese de fala – use o [SDK de fala](./get-started-text-to-speech.md) ou a [API REST](rest-text-to-speech.md) para converter conversão de texto em fala usando vozes padrão, neural ou personalizadas.

* Síntese assíncrona de longo áudio – Use a [API de áudio longo](long-audio-api.md) para sintetizar de forma assíncrona arquivos de texto para fala por mais de 10 minutos (por exemplo, livros de áudio ou palestras). Ao contrário da síntese executada usando o SDK de fala ou a API REST de fala em texto, as respostas não são retornadas em tempo real. A expectativa é que as solicitações sejam enviadas de forma assíncrona, que as respostas sejam sondadas e que o áudio sintetizado seja baixado quando disponibilizado a partir do serviço. Há suporte apenas para vozes neurais personalizadas.

* Vozes padrão-criadas usando as técnicas de síntese paramétricas estatística e/ou síntese de concatenação. Essas vozes são altamente inteligível e sólidas. Você pode facilmente permitir que seus aplicativos falem em mais de 45 idiomas, com uma ampla gama de opções de voz. Essas vozes fornecem alta precisão de pronúncia, incluindo suporte para abreviações, expansões de acrônimo, interpretações de data/hora, telefones e muito mais. Para obter uma lista completa de vozes padrão, consulte [idiomas com suporte](language-support.md#text-to-speech).

* Vozes neurais-as redes neurais profundas são usadas para superar os limites da síntese de fala tradicional com relação a estresse e intonation na linguagem falada. A previsão de Prosody e a síntese de voz são executadas simultaneamente, o que resulta em saídas mais suaves e de som natural. As vozes neurais podem ser usadas para fazer interações com os assistentes de voz e chatbots mais naturais e envolventes, converter textos digitais, como livros eletrônicos em Audiobooks, e aprimorar os sistemas de navegação no carro. Com a Prosody natural humana e a clara Articulation de palavras, as vozes neurais reduzem significativamente a escuta de fadiga quando você interage com sistemas de ia. Para obter uma lista completa de vozes neurais, consulte [idiomas com suporte](language-support.md#text-to-speech).

* Ajustar estilos de fala com o SSML-linguagem de marcação de síntese de fala (SSML) é uma linguagem de marcação baseada em XML usada para personalizar saídas de fala para texto. Com o SSML, você pode ajustar o timbre, adicionar pausas, melhorar a pronúncia, acelerar ou diminuir a taxa de fala, aumentar ou diminuir o volume e atribuir várias vozes a um único documento. Consulte o " [como](speech-synthesis-markup.md) " para ajustar estilos de fala.

## <a name="get-started"></a>Introdução

Consulte o guia de [início rápido](get-started-text-to-speech.md) para começar a usar o texto em fala. O serviço de conversão de texto em fala está disponível por meio do [SDK de fala](speech-sdk.md), da [API REST](rest-text-to-speech.md)e da [CLI de fala](spx-overview.md)

## <a name="sample-code"></a>Código de exemplo

O código de exemplo para conversão de texto em fala está disponível no GitHub. Esses exemplos abordam a conversão de texto em fala nas linguagens de programação mais populares.

- [Exemplos de conversão de texto em fala (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Exemplos de conversão de texto em fala (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="customization"></a>Personalização

Além das vozes padrão e neural, você pode criar e ajustar vozes personalizadas exclusivas para seu produto ou marca. Tudo o que é necessário para começar são alguns arquivos de áudio e as transcrições associadas. Para obter mais informações, consulte Introdução [à voz personalizada](how-to-custom-voice.md)

## <a name="pricing-note"></a>Nota de preços

Ao usar o serviço de conversão de texto em fala, você será cobrado por cada caractere convertido em fala, incluindo pontuação. Embora o documento SSML em si não seja Faturável, elementos opcionais usados para ajustar como o texto é convertido em fala, como fonemas e pitch, são contados como caracteres faturáveis. Aqui está uma lista do que é Faturável:

- Texto passado para o serviço de conversão de texto em fala no corpo de SSML da solicitação
- Todas as marcações dentro do campo de texto do corpo da solicitação no formato SSML, exceto para `<speak>` `<voice>` marcas e
- Letras, pontuação, espaços, tabulações, marcação e todos os caracteres de espaço em branco
- Cada ponto de código definido no Unicode

Para obter informações detalhadas, consulte [preços](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

> [!IMPORTANT]
> Cada caractere de idioma chinês, japonês e coreano é contado como dois caracteres para cobrança.

## <a name="reference-docs"></a>Documentos de Referência

- [SDK da fala](speech-sdk.md)
- [API REST: conversão de texto em fala](rest-text-to-speech.md)

## <a name="next-steps"></a>Próximas etapas

- [Obtenha uma assinatura gratuita do serviço de fala](overview.md#try-the-speech-service-for-free)
- [Obter o SDK de Fala](speech-sdk.md)