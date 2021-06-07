---
title: Visão geral da conversão de texto em fala – Serviço de Fala
titleSuffix: Azure Cognitive Services
description: O recurso da conversão de texto em fala do serviço de Fala permite que aplicativos, ferramentas ou dispositivos convertam texto em fala sintetizada similar à humana. Este artigo é uma visão geral dos benefícios e das funcionalidades do serviço de conversão de texto em fala.
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
ms.openlocfilehash: 1a7ff7befc1a5e82c5518cf103a81b35ec38c7db
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449351"
---
# <a name="what-is-text-to-speech"></a>O que é conversão de texto em fala?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Nesta visão geral, você conhecerá os benefícios e as funcionalidades do serviço de conversão de texto em fala, que permite que aplicativos, ferramentas ou dispositivos convertam texto em fala sintetizada similar à humana. Escolha entre vozes padrão e neurais ou crie uma voz personalizada exclusiva para seu produto ou marca. Há 75 vozes padrão disponíveis em mais de 45 idiomas e localidades e cinco vozes neurais estão disponíveis em um número selecionado de idiomas e localidades. Para ver uma lista completa de vozes, idiomas e localidades compatíveis, confira [idiomas compatíveis](language-support.md#text-to-speech).

Esta documentação contém os seguintes tipos de artigos:

* Os **guias de início rápido** são instruções de introdução que orientam sobre como fazer solicitações ao serviço.
* Os **guias de instruções** contêm instruções de uso do serviço de maneiras mais específicas ou personalizadas.
* Os **conceitos** fornecem explicações detalhadas sobre a funcionalidade e os recursos do serviço.
* Os **tutoriais** são guias mais longos que mostram como usar o serviço como um componente de soluções de negócios mais amplas.

> [!NOTE]
> A Fala do Bing foi desativada em 15 de outubro de 2019. Caso seus aplicativos, ferramentas ou produtos estiverem usando as APIs de Fala do Bing ou a Fala Personalizada, criamos guias para ajudar você a migrar para o serviço de Fala.
> - [Migrar da Fala do Bing para o Serviço de Fala](how-to-migrate-from-bing-speech.md)

## <a name="core-features"></a>Principais recursos

* Síntese de Fala – Use o [SDK de Fala](./get-started-text-to-speech.md) ou a [API REST](rest-text-to-speech.md) para a conversão de texto em fala usando vozes padrão, neurais ou personalizadas.

* Síntese assíncrona de áudio longo – Use a [API de Áudio Longo](long-audio-api.md) para sintetizar de maneira assíncrona arquivos de conversão de texto em fala com mais de dez minutos (por exemplo, audiolivros ou palestras). Ao contrário da síntese executada com o SDK de Fala ou a API REST de reconhecimento de fala, as respostas não são retornadas em tempo real. A expectativa é que as solicitações sejam enviadas de maneira assíncrona, que as respostas sejam sondadas e que o áudio sintetizado seja baixado quando disponibilizado no serviço. Há suporte somente para vozes neurais personalizadas.

* Vozes padrão – Criadas usando as técnicas de Síntese Paramétrica Estatística e/ou Síntese de Concatenação. Essas vozes são altamente inteligíveis e soam naturais. É fácil habilitar seus aplicativos para falar em mais de 45 idiomas, com uma ampla variedade de opções de voz. Essas vozes têm alta precisão de pronúncia, incluindo suporte para abreviações, expansões de acrônimos, interpretações de data/hora, polifonia e muito mais. Para ver uma lista completa das vozes padrão, confira [idiomas compatíveis](language-support.md#text-to-speech).

* Vozes neurais – As redes neurais profundas são usadas para superar os limites da síntese de fala tradicional com relação à ênfase e à entonação na linguagem falada. A previsão de prosódia e a síntese de voz são executadas simultaneamente, o que resulta em saídas mais suaves e de som natural. As vozes neurais podem ser usadas para interagir com chatbots e assistentes de voz de maneira mais natural e participativa, converter textos digitais, como livros eletrônicos em audiolivros, e aprimorar sistemas de navegação automotiva. Com a prosódia natural semelhante à humana e a articulação clara das palavras, as vozes neurais reduzem significativamente a fadiga de escuta quando você interage com sistemas de IA. Para ver uma lista completa das vozes neurais, confira [idiomas compatíveis](language-support.md#text-to-speech).

* Ajustar estilos de fala com SSML – A SSML (Linguagem de Marcação de Síntese de Fala) é uma linguagem de marcação baseada em XML usada para personalizar saídas de reconhecimento de fala. Com a SSML, é possível ajustar o timbre, adicionar pausas, aprimorar a pronúncia, acelerar ou diminuir a taxa de fala, aumentar ou diminuir o volume e atribuir várias vozes a um documento. Confira as [instruções](speech-synthesis-markup.md) para ajustar os estilos de fala.

* Visemes – Os [visemes](how-to-speech-synthesis-viseme.md) são as principais poses da fala observada, como a posição dos lábios, da mandíbula e da língua ao produzir um fonema específico. Os visemes têm uma forte correlação com vozes e fonemas. Ao usar eventos viseme no SDK de Fala, você pode gerar dados de animação facial, que podem ser usados para animar rostos em comunicação, educação, entretenimento e serviço de atendimento ao cliente por leitura labial.

> [!NOTE]
> O viseme só funciona para voz `en-US-AriaNeural` no momento.

## <a name="get-started"></a>Introdução

Confira o guia de [início rápido](get-started-text-to-speech.md) para começar a usar a conversão de texto em fala. O serviço de conversão de texto em fala está disponível por meio do [SDK de Fala](speech-sdk.md), da [API REST](rest-text-to-speech.md) e da [CLI de Fala](spx-overview.md)

## <a name="sample-code"></a>Código de exemplo

Há um código de exemplo disponível no GitHub para a conversão de texto em fala. Esses exemplos abordam a conversão de texto em fala nas linguagens de programação mais populares.

- [Exemplos de conversão de texto em fala (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Exemplos de conversão de texto em fala (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="customization"></a>Personalização

Além das vozes padrão e neural, é possível criar e ajustar vozes personalizadas exclusivas para um produto ou marca. Para começar, basta alguns arquivos de áudio e as transcrições deles. Para saber mais, confira [Introdução à Voz Personalizada](how-to-custom-voice.md)

## <a name="pricing-note"></a>Observação sobre os preços

Ao usar o serviço de conversão de texto em fala, cada caractere convertido em fala será cobrado, incluindo a pontuação. Embora o documento SSML em si não seja faturável, elementos opcionais usados para ajustar como o texto é convertido em fala, como fonemas e timbre, são contados como caracteres faturáveis. Veja uma lista de itens faturáveis:

- Texto passado para o serviço de conversão de texto em fala no corpo SSML da solicitação
- Todas as marcações dentro do campo de texto do corpo da solicitação no formato SSML, exceto as marcas `<speak>` e `<voice>`
- Letras, pontuação, espaços, tabulações, marcação e todos os caracteres de espaço em branco
- Cada ponto de código definido no Unicode

Para obter informações mais detalhadas, confira [Preços](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

> [!IMPORTANT]
> Cada caractere de idioma chinês, japonês e coreano é contado como dois caracteres na cobrança.

## <a name="reference-docs"></a>Documentos de Referência

- [SDK da fala](speech-sdk.md)
- [API REST: conversão de texto em fala](rest-text-to-speech.md)

## <a name="next-steps"></a>Próximas etapas

- [Ganhe uma assinatura gratuita do serviço de Fala](overview.md#try-the-speech-service-for-free)
- [Obter o SDK de Fala](speech-sdk.md)
