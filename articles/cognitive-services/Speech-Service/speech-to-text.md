---
title: Visão geral de fala a texto-serviço de fala
titleSuffix: Azure Cognitive Services
description: O software de fala em texto permite a transcrição em tempo real de fluxos de áudio em texto. Seus aplicativos, ferramentas ou dispositivos podem consumir, exibir e tomar medidas nessa entrada de texto. Este artigo é uma visão geral dos benefícios e funcionalidades do serviço de conversão de fala em texto.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: trbye
ms.custom: cog-serv-seo-aug-2020
keywords: conversão de fala em texto, software de conversão de fala em texto
ms.openlocfilehash: 5e593202a8cd68dfc4a92f991d573b4b06051b36
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102434298"
---
# <a name="what-is-speech-to-text"></a>O que é conversão de fala em texto?

Nesta visão geral, você aprenderá sobre os benefícios e capacidades do serviço de conversão de fala em texto.
A conversão de fala em texto, também conhecida como reconhecimento de fala, permite a transcrição em tempo real de fluxos de áudio em texto. Seus aplicativos, ferramentas ou dispositivos podem consumir, exibir e tomar medidas sobre esse texto como entrada de comando. Esse serviço é fornecido pela mesma tecnologia de reconhecimento usada pela Microsoft para os produtos Cortana e Office. Ele funciona diretamente com as ofertas de tradução e serviço de <a href="./speech-translation.md" target="_blank">conversão </a> de <a href="./text-to-speech.md" target="_blank">texto em fala </a> . Para obter uma lista completa de idiomas de fala para texto disponíveis, consulte [idiomas com suporte](language-support.md#speech-to-text).

O serviço de conversão de voz em texto usa o modelo de linguagem universal. Esse modelo foi treinado usando dados de propriedade da Microsoft e é implantado na nuvem. É ideal para cenários de conversação e de ditado. Ao usar a conversão de fala em texto para reconhecimento e transcrição em um ambiente exclusivo, você pode criar e treinar modelos acústicos, de linguagem e de pronúncia personalizados. A personalização é útil para lidar com ruídos de ambiente ou vocabulário específico do setor.

Com o texto de referência adicional como entrada, o serviço de conversão de fala também habilita a capacidade de [avaliação de pronúncia](rest-speech-to-text.md#pronunciation-assessment-parameters) para avaliar a pronúncia da fala e dá aos palestrantes os comentários sobre a precisão e a fluência de áudio falado. Com a avaliação de pronúncia, os aprendizes de linguagem podem praticar, obter comentários instantâneos e melhorar sua pronúncia para que eles possam falar e apresentar confiança. Os educadores podem usar a capacidade de avaliar a pronúncia de vários alto-falantes em tempo real. Atualmente, o recurso dá suporte a inglês-EUA e correlaciona altamente com as avaliações de fala conduzidas por especialistas.

> [!NOTE]
> Fala do Bing foi encerrado em 15 de outubro de 2019. Se seus aplicativos, ferramentas ou produtos estiverem usando as APIs de Fala do Bing, criamos guias para ajudá-lo a migrar para o serviço de fala.
> - [Migrar do Fala do Bing para o serviço de fala](how-to-migrate-from-bing-speech.md)

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

## <a name="get-started"></a>Introdução

Consulte o guia de [início rápido](get-started-speech-to-text.md) para começar a usar a fala em texto. O serviço está disponível por meio do [SDK de fala](speech-sdk.md), da [API REST](rest-speech-to-text.md#pronunciation-assessment-parameters)e da [CLI de fala](spx-overview.md).

## <a name="sample-code"></a>Código de exemplo

O código de exemplo para o SDK de fala está disponível no GitHub. Esses exemplos abordam cenários comuns, como ler áudio de um arquivo ou fluxo, reconhecimento contínuo e único e trabalho com modelos personalizados.

- [Exemplos de conversão de fala em texto (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Exemplos de transcrição em lote (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
- [Exemplos de avaliação de pronúncia (REST)](rest-speech-to-text.md#pronunciation-assessment-parameters)

## <a name="customization"></a>Personalização

Além do modelo de serviço de fala padrão, você pode criar modelos personalizados. A personalização ajuda a superar as barreiras de reconhecimento de fala, como estilo de fala, vocabulário e ruído de fundo, consulte [fala personalizada](./custom-speech-overview.md). As opções de personalização variam por idioma/localidade, consulte [idiomas com suporte](./language-support.md) para verificar o suporte.

## <a name="batch-transcription"></a>Transcrição em lote

A transcrição do lote é um conjunto de operações da API REST que permitem transcrever uma grande quantidade de áudio no armazenamento. Será possível transmitir arquivos de áudio com um URI de SAS (assinatura de acesso compartilhado) e receber resultados de transcrição de maneira assíncrona. Consulte o [instruções](batch-transcription.md) para obter mais informações sobre como usar a API de transcrição do lote.

[!INCLUDE [speech-reference-doc-links](includes/speech-reference-doc-links.md)]

## <a name="next-steps"></a>Próximas etapas

- [Obter gratuitamente uma chave de assinatura dos Serviços de Fala](overview.md#try-the-speech-service-for-free)
- [Obter o SDK de Fala](speech-sdk.md)