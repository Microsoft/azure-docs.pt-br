---
title: Visão geral do reconhecimento de fala – Serviço de Fala
titleSuffix: Azure Cognitive Services
description: O software de reconhecimento de fala permite a transcrição em tempo real de fluxos de áudio em texto. Aplicativos, ferramentas ou dispositivos podem usar, exibir e alterar essa entrada de texto. Este artigo é uma visão geral dos benefícios e das funcionalidades do serviço de reconhecimento de fala.
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
ms.openlocfilehash: 3450d39729096bfc3077f51e2069f8f102e571a5
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449385"
---
# <a name="what-is-speech-to-text"></a>O que é conversão de fala em texto?

Nesta visão geral, você conhecerá os benefícios e as funcionalidades do serviço de reconhecimento de fala.
A conversão de fala em texto, também conhecida como reconhecimento de fala, permite transcrever fluxos de áudio em texto em tempo real. Seus aplicativos, ferramentas ou dispositivos podem consumir, exibir e tomar medidas sobre esse texto como entrada de comando. Esse serviço é da mesma plataforma de tecnologia de reconhecimento que a Microsoft usa para o Cortana e os produtos do Office. Ele funciona perfeitamente com as ofertas de serviço de <a href="./speech-translation.md" target="_blank">tradução</a> e <a href="./text-to-speech.md" target="_blank">conversão de texto em fala</a>. Para ver uma lista completa de idiomas disponíveis para reconhecimento de fala, confira os [idiomas compatíveis](language-support.md#speech-to-text).

O comportamento padrão do serviço de conversão de fala em texto é usar o modelo de linguagem Universal. Esse modelo foi treinado usando dados de propriedade da Microsoft e está implantado na nuvem. Ele é ideal para cenários de conversa e de ditado. Ao usar a conversão de fala em texto para reconhecimento e transcrição em um ambiente exclusivo, você pode criar e treinar modelos acústicos, de linguagem e de pronúncia personalizados. A personalização é útil para lidar com ruído ambiente ou com vocabulário específico do setor.

Esta documentação contém os seguintes tipos de artigos:

* Os **guias de início rápido** são instruções de introdução que orientam sobre como fazer solicitações ao serviço.
* Os **guias de instruções** contêm instruções de uso do serviço de maneiras mais específicas ou personalizadas.
* Os **conceitos** fornecem explicações detalhadas sobre a funcionalidade e os recursos do serviço.
* Os **tutoriais** são guias mais longos que mostram como usar o serviço como um componente de soluções de negócios mais amplas.

> [!NOTE]
> A Fala do Bing foi desativada em 15 de outubro de 2019. Caso seus aplicativos, ferramentas ou produtos estiverem usando as APIs de Fala do Bing, criamos guias para ajudar você a migrar para o serviço de Fala.
> - [Migrar da Fala do Bing para o Serviço de Fala](how-to-migrate-from-bing-speech.md)

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

## <a name="get-started"></a>Introdução

Confira o guia de [início rápido](get-started-speech-to-text.md) para começar a usar o reconhecimento de fala. O serviço está disponível por meio do [SDK de Fala](speech-sdk.md), da [API REST](rest-speech-to-text.md#pronunciation-assessment-parameters) e da [CLI de Fala](spx-overview.md).

## <a name="sample-code"></a>Código de exemplo

Há um código de exemplo disponível no GitHub para o SDK de Fala. Esses exemplos abordam cenários comuns, como ler áudio de um arquivo ou fluxo, reconhecimento contínuo e único e trabalho com modelos personalizados.

- [Exemplos de reconhecimento de fala (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Exemplos de transcrição em lote (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
- [Exemplos de avaliação de pronúncia (REST)](rest-speech-to-text.md#pronunciation-assessment-parameters)

## <a name="customization"></a>Personalização

Além do modelo de serviço de Fala padrão, é possível criar modelos personalizados. A personalização ajuda a superar as barreiras de reconhecimento de fala, como estilo de fala, vocabulário e ruído de fundo. Confira [Fala Personalizada](./custom-speech-overview.md). As opções de personalização variam por idioma/localidade. Confira [idiomas compatíveis](./language-support.md) para verificar o suporte.

## <a name="batch-transcription"></a>Transcrição de lote

A transcrição de lote é um conjunto de operações da API REST que permitem transcrever uma grande quantidade de áudio no armazenamento. Será possível transmitir arquivos de áudio com um URI de SAS (assinatura de acesso compartilhado) e receber resultados de transcrição de maneira assíncrona. Confira as [instruções](batch-transcription.md) para saber mais sobre como usar a API de transcrição de lote.

[!INCLUDE [speech-reference-doc-links](includes/speech-reference-doc-links.md)]

## <a name="next-steps"></a>Próximas etapas

- [Obter gratuitamente uma chave de assinatura dos Serviços de Fala](overview.md#try-the-speech-service-for-free)
- [Obter o SDK de Fala](speech-sdk.md)