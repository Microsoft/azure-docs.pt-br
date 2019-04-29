---
title: Texto em fala com os serviços de fala do Azure
titleSuffix: Azure Cognitive Services
description: Texto para fala a fala dos serviços do Azure é um serviço que permite que seus aplicativos, ferramentas ou dispositivos converter texto em fala natural de sintetizada humana. Escolher entre standard e neurais vozes ou criar sua própria voz personalizada exclusivos ao seu produto ou marca. 75 vozes padrão estão disponíveis em mais de 45 idiomas e localidades e vozes neurais 5 estão disponíveis em 4 de idiomas e localidades.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 2c5c2bc2b8dd1930efef9833bd442fcad5566e2a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61460262"
---
# <a name="what-is-text-to-speech"></a>O que é o texto em fala?

Texto para fala a fala dos serviços do Azure é um serviço que permite que seus aplicativos, ferramentas ou dispositivos converter texto em fala natural de sintetizada humana. Escolher entre standard e neurais vozes ou criar sua própria voz personalizada exclusivos ao seu produto ou marca. 75 vozes padrão estão disponíveis em mais de 45 idiomas e localidades e vozes neurais 5 estão disponíveis em 4 de idiomas e localidades. Para obter uma lista completa, consulte [idiomas com suporte](language-support.md#text-to-speech).

Tecnologia de texto para fala permite que os criadores de conteúdo interagir com seus usuários de diferentes maneiras. Texto para fala pode melhorar a acessibilidade, fornecendo aos usuários uma opção para interagir com o conteúdo de forma audível. Se o usuário tem uma deficiência visual, uma deficiência de aprendizagem, ou exige informações de navegação enquanto dirige, uma experiência existente pode melhorar o texto para fala. Texto em fala também é um complemento valioso para bots de voz e assistentes virtuais.

### <a name="standard-voices"></a>Vozes padrão

Vozes padrão são criadas usando técnicas de síntese paramétrica estatísticos e/ou síntese de concatenação. Esses vozes são altamente inteligível e parecer bastante naturais. Você pode habilitar facilmente seus aplicativos falar em mais de 45 idiomas, com uma ampla variedade de opções de voz. Esses vozes fornecem precisão pronúncia alta, incluindo suporte para abreviações, expansões de acrônimos, interpretações de data/hora, polyphones e muito mais. Use voz padrão para melhorar a acessibilidade de seus aplicativos e serviços, permitindo que os usuários interajam com seu conteúdo de forma audível.

### <a name="neural-voices"></a>Vozes neurais

Vozes neurais usam redes neurais profundas para superar os limites dos sistemas tradicionais de texto em fala na correspondência de padrões de estresse e entonação no idioma falado e resumir as unidades de conversão de fala em uma voz de computador. Texto em fala padrão divide-se a métrica em análise linguística separado e etapas de previsão acústico controladas por modelos independentes. Isso pode resultar na síntese de voz muffled, buzzy. Nossa funcionalidade neural faz síntese de voz e previsão de métrica simultaneamente, o que resulta em uma voz mais fluida e alarme natural.

Vozes neurais podem ser usadas para interagir com chatbots e assistentes virtuais de forma mais natural e participativa para converter textos digitais, como livros eletrônicos, em audiolivros, e aprimorar sistemas de navegação de carros. Com a métrica de natural humana e clara Articulação das palavras, vozes Neural reduzem significativamente fadiga escuta ao interagir com sistemas de inteligência Artificial. Para obter mais informações sobre as vozes neurais, consulte [idiomas com suporte](language-support.md#text-to-speech).

Para saber mais sobre os benefícios de vozes neurais, consulte [novo serviço texto em fala neural da Microsoft ajuda a máquinas falar como pessoas](https://azure.microsoft.com/blog/microsoft-s-new-neural-text-to-speech-service-helps-machines-speak-like-people/).

### <a name="custom-voices"></a>Vozes personalizadas

Personalização de voz lhe permite criar uma voz reconhecível, um de tipo para sua marca. Para criar sua fonte de voz personalizada, você fazer uma gravação studio e carrega os scripts associados como os dados de treinamento. Em seguida, o serviço cria um modelo de voz exclusivo ajustado para a gravação. Você pode usar essa fonte de voz personalizadas para sintetizador de fala. Para obter mais informações, consulte [vozes personalizadas](how-to-customize-voice-font.md).

## <a name="core-features"></a>Principais recursos

Esta tabela lista os principais recursos de texto em fala:

| Caso de uso | . | REST |
|----------|-----|------|
| Converta texto em fala. | Sim | Sim |
| Carrega conjuntos de dados para a adaptação de voz. | Não  | Sim\* |
| Criar e gerenciar modelos de fonte de voz. | Não  | Sim\* |
| Criar e gerenciar implantações de fonte de voz. | Não  | Sim\* |
| Criar e gerenciar testes de fonte de voz. | Não  | Sim\* |
| Gerencie assinaturas. | Não  | Sim\* |

\* *Esses serviços estão disponíveis usando o ponto de extremidade cris.ai. Ver [Swagger referência](https://westus.cris.ai/swagger/ui/index). Esses treinamento de voz personalizadas e as APIs de gerenciamento de implementam a limitação que limitar as solicitações a 25 por 5 segundos, enquanto a síntese de fala, API em si implementa a limitação que permite a 200 solicitações por segundo, o mais alto. Quando a limitação ocorre, você será notificado por meio de cabeçalhos de mensagem.*

## <a name="get-started-with-text-to-speech"></a>Introdução ao texto em fala

Nós oferecemos guias de início rápido foi projetados para ter está executando código em menos de 10 minutos. Essa tabela inclui uma lista de texto em fala guias de início rápido organizados por idioma.

### <a name="sdk-quickstarts"></a>Guias de início rápido do SDK

| Guia de início rápido (SDK) | Plataforma | Referência de API |
|------------|----------|---------------|
| [C#, .NET Framework](quickstart-text-to-speech-dotnet-windows.md) |  Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| [C++](quickstart-text-to-speech-cpp-windows.md) |  Windows | [Browse](https://aka.ms/csspeech/cppref) |
| [C++](quickstart-text-to-speech-cpp-linux.md) | Linux | [Browse](https://aka.ms/csspeech/cppref) |

### <a name="rest-quickstarts"></a>Guias de início rápido REST

| Guia de início rápido (REST) | Plataforma | Referência de API |
|------------|----------|---------------|
| [C#, .NET Core](quickstart-dotnet-text-to-speech.md) | Windows, macOS, Linux | [Browse](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Node.js](quickstart-nodejs-text-to-speech.md) | Janela, macOS, Linux | [Browse](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Python](quickstart-python-text-to-speech.md) | Janela, macOS, Linux | [Browse](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |

## <a name="sample-code"></a>Exemplo de código

Código de exemplo de texto em fala está disponível no GitHub. Esses exemplos abrangem a conversão de texto em fala em linguagens de programação mais populares.

* [Exemplos de texto em fala (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
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
