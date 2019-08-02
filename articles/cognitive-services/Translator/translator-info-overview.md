---
title: O que é a API de Tradução de Texto? - API de Tradução de Texto
titlesuffix: Azure Cognitive Services
description: Integre a API de Tradução de Texto a seus aplicativos, sites, ferramentas e outras soluções para fornecer experiências de usuário em vários idiomas.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: overview
ms.date: 06/04/2019
ms.author: swmachan
ms.custom: seodec18
ms.openlocfilehash: 8cdd52963f89041ea97b018fd3756c925308e641
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443272"
---
# <a name="what-is-the-translator-text-api"></a>O que é a API de Tradução de Texto?

A API de Tradução de Texto é fácil de integrar a seus aplicativos, sites, ferramentas e soluções. Ela permite que você adicione experiências de usuário com vários idiomas em [mais de 60 idiomas](languages.md) e pode ser usada em qualquer plataforma de hardware com qualquer sistema operacional para traduzir idioma de texto para texto.

A API de Tradução de Texto faz parte da coleção [API dos Serviços Cognitivos](https://docs.microsoft.com/azure/#pivot=products&panel=ai) do Azure de aprendizado de máquina e algoritmos de IA na nuvem, e está pronta para ser consumida em seus projetos de desenvolvimento.

## <a name="about-microsoft-translator"></a>Sobre o Microsoft Translator

O Microsoft Translator é um serviço de tradução automática baseado em nuvem. O serviço principal é a API de Tradução de Texto, que aciona uma quantidade de produtos e serviços da Microsoft e é usado por milhares de empresas no mundo todo em seus aplicativos e fluxos de trabalho, o que permite que seu conteúdo alcance um público global.

A tradução de fala, por meio da API de Tradução de Texto, também está disponível no [Serviço de Fala da Microsoft](https://docs.microsoft.com/azure/cognitive-services/speech-service/). Ele combina a funcionalidade da API de Tradução de Fala e do Serviço de Fala Personalizado em um serviço unificado e totalmente personalizável. O Serviço de Fala está substituindo a API de Tradução de Fala, que será encerrado em 15 de outubro de 2019.

## <a name="language-support"></a>Suporte ao idioma

O Microsoft Translator dá suporte a vários idiomas para tradução, transliteração, detecção de idioma e dicionários. Confira [Suporte a idiomas](language-support.md) para obter uma lista completa, ou acesse a lista de forma programática com a [API REST](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-languages).  

## <a name="microsoft-translator-neural-machine-translation"></a>Tradução Automática Neural do Microsoft Translator

A NMT (Tradução automática neural) é o novo padrão para traduções automáticas de IA (inteligência artificial) de alta qualidade. Ela substitui a tecnologia herdada de SMT (tradução automática estatística), que atingiu um limite de qualidade em meados da década de 2010.

A NMT fornece traduções melhores que a SMT não apenas de um ponto de vista de pontuação de qualidade da tradução bruta, mas também porque ela soará mais fluente e humana. O motivo principal dessa fluidez é que a NMT usa o contexto completo de uma sentença para traduzir palavras. A SMT usava apenas o contexto imediato de algumas palavras presentes antes e depois de cada palavra.

Os modelos de NMT são a essência da API e não são visíveis aos usuários finais. A única diferença notável é uma melhor qualidade da tradução, especialmente para idiomas como o chinês, o japonês e o árabe.

Saiba mais sobre [como funciona a NMT](https://www.microsoft.com/en-us/translator/mt.aspx#nnt)

## <a name="language-customization"></a>Personalização de linguagem

Uma extensão do núcleo do serviço Microsoft Translator, o Tradutor Personalizado pode ser usado em conjunto com a API de Tradução de Texto para ajudá-lo a personalizar o sistema de tradução neural e aprimorar a tradução conforme sua terminologia e estilo específicos.

Com o Tradutor Personalizado, é possível compilar sistemas de tradução que controlem a terminologia usada na sua própria empresa ou setor. Assim, seu sistema de tradução personalizada será integrado facilmente aos aplicativos, fluxos de trabalho e sites existentes, e em vários tipos de dispositivos, por meio da API de Tradução de Texto regular da Microsoft, usando o parâmetro de categoria.

Saiba mais sobre a [personalização de idioma](customization.md)

## <a name="next-steps"></a>Próximas etapas

- [Inscreva-se](translator-text-how-to-signup.md) para obter uma chave de acesso.
- A [referência de API](https://docs.microsoft.com/azure/cognitive-services/Translator/reference/v3-0-reference) fornece a documentação técnica para as APIs.
- [Detalhes de preço](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)
