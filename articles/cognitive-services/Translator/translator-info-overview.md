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
ms.date: 02/15/2021
ms.author: lajanuar
ms.custom: cog-serv-seo-aug-2020
keywords: tradutor, tradução de texto, tradução automática, serviço de tradução
ms.openlocfilehash: 72df700d38cad3ee31ef438d7df1c890acde9e6b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101727967"
---
# <a name="what-is-the-translator-service"></a>O que é o serviço Tradutor?

O Tradutor é um serviço de tradução automática baseado em nuvem e faz parte da família de APIs cognitivas dos [Serviços Cognitivos do Azure](../../index.yml?panel=ai&pivot=products) usadas para a criação de aplicativos inteligentes. O Tradutor é fácil de integrar em seus aplicativos, sites, ferramentas e soluções. Ela permite que você adicione experiências de usuário multilíngue em [90 idiomas e dialetos](./language-support.md). E pode ser usada em qualquer plataforma de hardware, com qualquer sistema operacional para realizar a tradução de texto.

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

 O Tradutor Personalizado, uma extensão do serviço de Tradução da Microsoft, pode ser usado em conjunto com a Tradução para personalizar o sistema de tradução neural e melhorar a tradução com base na sua terminologia e estilo específicos.

Com o Tradutor Personalizado, é possível criar sistemas de tradução a fim de controlar a terminologia usada na sua empresa ou no seu setor. O sistema de tradução personalizada consegue se integrar com facilidade aos diversos aplicativos, fluxos de trabalho, sites e dispositivos existentes, por meio da Tradução normal, usando o parâmetro de categoria.

Saiba mais sobre o [Tradutor Personalizado](customization.md).

## <a name="next-steps"></a>Próximas etapas

- [Crie um serviço de Tradução](./translator-how-to-signup.md) a fim de obter as chaves de acesso e o ponto de extremidade.
- Experimente nosso [Início Rápido](quickstart-translator.md) para chamar rapidamente o serviço Tradutor.
- A [referência de API](./reference/v3-0-reference.md) fornece a documentação técnica para as APIs.
- [Detalhes de preço](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)
