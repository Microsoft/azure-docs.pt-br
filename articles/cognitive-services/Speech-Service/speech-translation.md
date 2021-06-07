---
title: Visão geral da tradução de fala – Serviço de Fala
titleSuffix: Azure Cognitive Services
description: É um programa que permite adicionar a tradução de fala de ponta a ponta, em tempo real e de vários idiomas a aplicativos, ferramentas e dispositivos. A mesma API pode ser usada para a tradução com conversão de fala em fala e de fala em texto. Este artigo é uma visão geral dos benefícios e das funcionalidades do serviço de Tradução de Fala.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: erhopf
ms.custom: devx-track-csharp, cog-serv-seo-aug-2020
keywords: tradução de fala
ms.openlocfilehash: 99541d7fe9eaa867860af93bc1423d476ce8bf4a
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449929"
---
# <a name="what-is-speech-translation"></a>O que é tradução de fala?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Nesta visão geral, você conhece os benefícios e as funcionalidades do serviço de Tradução de Fala, que traduz em tempo real [para vários idiomas](language-support.md#speech-translation) e faz o reconhecimento de fala de fluxos de áudio. Com o SDK de Fala, os seus aplicativos, ferramentas e dispositivos têm acesso a transcrições de origem e a saídas de tradução para o áudio fornecido. Os resultados provisórios da transcrição e da tradução são retornados conforme a fala é detectada e os resultados finais podem ser convertidos em fala sintetizada.

Esta documentação contém os seguintes tipos de artigos:

* Os **guias de início rápido** são instruções de introdução que orientam sobre como fazer solicitações ao serviço.
* Os **guias de instruções** contêm instruções de uso do serviço de maneiras mais específicas ou personalizadas.
* Os **conceitos** fornecem explicações detalhadas sobre a funcionalidade e os recursos do serviço.
* Os **tutoriais** são guias mais longos que mostram como usar o serviço como um componente de soluções de negócios mais amplas.

## <a name="core-features"></a>Principais recursos

* Tradução de reconhecimento de fala com resultados de reconhecimento.
* Tradução de fala em fala.
* Compatível com a tradução para vários idiomas de destino.
* Resultados de tradução e de reconhecimento provisórios.

## <a name="get-started"></a>Introdução 

Confira o guia de [início rápido](get-started-speech-translation.md) para começar a usar a tradução de fala. O serviço de Tradução de Fala está disponível por meio do [SDK de Fala](speech-sdk.md) e da [CLI de Fala](spx-overview.md).

## <a name="sample-code"></a>Código de exemplo

Há um código de exemplo disponível no GitHub para o SDK de Fala. Esses exemplos abordam cenários comuns, como ler áudio de um arquivo ou fluxo, reconhecimento/tradução contínua e única e trabalho com modelos personalizados.

* [Exemplos de reconhecimento de fala e tradução (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)

## <a name="migration-guides"></a>Guias de migração

Caso seus aplicativos, ferramentas ou produtos estiverem usando a [API de Tradução de Fala](./how-to-migrate-from-translator-speech-api.md), criamos guias para ajudar você a migrar para o serviço de Fala.

* [Migrar da API de Tradução de Fala para o Serviço de Fala](how-to-migrate-from-translator-speech-api.md)

## <a name="reference-docs"></a>Documentos de Referência

* [SDK da fala](./speech-sdk.md)
* [SDK de Dispositivos de Fala](speech-devices-sdk.md)
* [API REST: conversão de fala em texto](rest-speech-to-text.md)
* [API REST: conversão de texto em fala](rest-text-to-speech.md)
* [API REST: transcrição e personalização em lote](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)

## <a name="next-steps"></a>Próximas etapas

* Concluir o [início rápido](get-started-speech-translation.md) da Tradução de Fala
* [Obter gratuitamente uma chave de assinatura dos Serviços de Fala](overview.md#try-the-speech-service-for-free)
* [Obter o SDK de Fala](speech-sdk.md)