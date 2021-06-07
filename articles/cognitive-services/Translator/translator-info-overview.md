---
title: Serviço Tradutor da Microsoft
titlesuffix: Azure Cognitive Services
description: Integre o Tradutor em seus aplicativos, sites, ferramentas e outras soluções para fornecer experiências do usuário em vários idiomas.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.topic: overview
ms.subservice: translator-text
ms.date: 03/15/2021
ms.author: lajanuar
ms.custom: cog-serv-seo-aug-2020
keywords: tradutor, tradução de texto, tradução automática, serviço de tradução
ms.openlocfilehash: ec76aa7554110b7440eb825f2d5e86ae2da6baa2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104657715"
---
# <a name="what-is-the-translator-service"></a>O que é o serviço Tradutor?

O Tradutor é um serviço de tradução automática baseado em nuvem e faz parte da família de APIs cognitivas dos [Serviços Cognitivos do Azure](../../index.yml?panel=ai&pivot=products) usadas para a criação de aplicativos inteligentes. O Tradutor é fácil de integrar em seus aplicativos, sites, ferramentas e soluções. Ele permite que você adicione experiências de usuário em vários idiomas em [90 idiomas e dialetos](./language-support.md) e pode ser usado para tradução de texto com qualquer sistema operacional.

Esta documentação contém os seguintes tipos de artigos:  

* Os [**guias de início rápido**](quickstart-translator.md) são instruções de introdução que orientam sobre como fazer solicitações ao serviço.  
* Os [**guias de instruções**](translator-how-to-signup.md) contêm instruções de uso do serviço de maneiras mais específicas ou personalizadas.  
* Os [**conceitos**](character-counts.md) fornecem explicações detalhadas sobre a funcionalidade e os recursos do serviço.  
* Os [**tutoriais**](tutorial-wpf-translation-csharp.md) são guias mais longos que mostram como usar o serviço como um componente de soluções de negócios mais amplas.  


## <a name="about-microsoft-translator"></a>Sobre o Microsoft Translator

A Tradução está incorporada em muitos produtos e serviços da Microsoft e é usada nos aplicativos e fluxos de trabalho de milhares de empresas em todo o mundo.

A tradução de fala, do Tradutor, também está disponível no [Serviço de Fala do Azure](../speech-service/index.yml). Ele combina a funcionalidade da API de Tradução de Fala e do Serviço de Fala Personalizado em um serviço unificado e totalmente personalizável. 

## <a name="language-support"></a>Suporte ao idioma

O Tradutor fornece suporte a vários idiomas para tradução de texto, transliteração, detecção de idioma e dicionários. Confira [Suporte a idiomas](language-support.md) para obter uma lista completa, ou acesse a lista de forma programática com a [API REST](./reference/v3-0-languages.md).  

## <a name="microsoft-translator-neural-machine-translation"></a>Tradução Automática Neural do Microsoft Translator

A NMT (Tradução automática neural) é o novo padrão para traduções automáticas de IA (inteligência artificial) de alta qualidade. Ela substitui a tecnologia herdada de SMT (tradução automática estatística), que atingiu um limite de qualidade em meados da década de 2010.

A NMT fornece traduções melhores que a SMT não apenas de um ponto de vista de pontuação de qualidade da tradução bruta, mas também porque ela soará mais fluente e humana. O motivo principal dessa fluidez é que a NMT usa o contexto completo de uma sentença para traduzir palavras. A SMT usava apenas o contexto imediato de algumas palavras presentes antes e depois de cada palavra.

Os modelos de NMT são a essência da API e não são visíveis aos usuários finais. A única diferença notável é uma melhor qualidade da tradução, especialmente para idiomas como o chinês, o japonês e o árabe.

Saiba mais sobre [como funciona a NMT](https://www.microsoft.com/en-us/translator/mt.aspx#nnt).

## <a name="improve-translations-with-custom-translator"></a>Aprimorar traduções com o Tradutor Personalizado

 O [Tradutor Personalizado](customization.md), uma extensão do serviço do Tradutor, pode ser usado para personalizar o sistema de tradução neural e aprimorar a tradução com base na sua terminologia e no seu estilo específicos.

Com o Tradutor Personalizado, é possível criar sistemas de tradução a fim de controlar a terminologia usada na sua empresa ou no seu setor. O sistema de tradução personalizada consegue se integrar com facilidade aos diversos aplicativos, fluxos de trabalho, sites e dispositivos existentes, por meio da Tradução normal, usando o parâmetro de categoria.

## <a name="next-steps"></a>Próximas etapas

- [Crie um serviço de Tradução](./translator-how-to-signup.md) a fim de obter as chaves de acesso e o ponto de extremidade.
- Experimente nosso [Início Rápido](quickstart-translator.md) para chamar rapidamente o serviço Tradutor.
- A [referência de API](./reference/v3-0-reference.md) fornece a documentação técnica para as APIs.
- [Detalhes de preço](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)
