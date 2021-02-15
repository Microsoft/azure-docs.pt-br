---
title: Análise de fluxo ao vivo usando Video Indexer
titleSuffix: Azure Media Services
description: Este artigo mostra como executar uma análise de fluxo ao vivo usando Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/13/2019
ms.author: juliako
ms.openlocfilehash: 89d0254fc758834c437f347e6ecb7bcafc1fe467
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "74185991"
---
# <a name="live-stream-analysis-with-video-indexer"></a>Análise de fluxo ao vivo com Video Indexer

Os serviços de mídia do Azure Video Indexer é um serviço do Azure projetado para extrair informações detalhadas de arquivos de áudio e vídeo offline. Isso é para analisar um determinado arquivo de mídia já criado com antecedência. No entanto, para alguns casos de uso, é importante obter as informações de mídia de um feed ao vivo o mais rápido possível para desbloquear o funcionamento e outros casos de uso pressionados no tempo. Por exemplo, esses metadados ricos em um fluxo ao vivo podem ser usados por produtores de conteúdo para automatizar a produção da TV.

Uma solução descrita neste artigo permite que os clientes usem Video Indexer em resoluções quase em tempo real em feeds dinâmicos. O atraso na indexação pode ser tão baixo quanto quatro minutos usando essa solução, dependendo das partes dos dados que estão sendo indexados, da resolução de entrada, do tipo de conteúdo e da computação usada para esse processo.

![Os metadados de Video Indexer na transmissão ao vivo](./media/live-stream-analysis/live-stream-analysis01.png)

*Figura 1 – Player de exemplo exibindo os metadados de Video Indexer na transmissão ao vivo*

A [solução de análise de fluxo](https://aka.ms/livestreamanalysis) em questão, usa Azure Functions e dois aplicativos lógicos para processar um programa ao vivo de um canal ao vivo nos serviços de mídia do Azure com Video indexer e exibe o resultado com player de mídia do Azure mostrando o fluxo resultante quase em tempo real.

Em alto nível, ele é composto de duas etapas principais. A primeira etapa é executada a cada 60 segundos e leva um subclipe dos últimos 60 segundos reproduzidos, cria um ativo a partir dele e o indexa por meio de Video Indexer. Em seguida, a segunda etapa é chamada após a conclusão da indexação. As informações capturadas são processadas, enviadas para Azure Cosmos DB e o subclipe indexado é excluído.

O player de exemplo reproduz a transmissão ao vivo e obtém as informações de Azure Cosmos DB, usando uma função dedicada do Azure. Ele exibe os metadados e as miniaturas em sincronia com o vídeo ao vivo.

![Os dois aplicativos lógicos processam a transmissão ao vivo a cada minuto na nuvem](./media/live-stream-analysis/live-stream-analysis02.png)

*Figura 2 – os dois aplicativos lógicos processando a transmissão ao vivo a cada minuto na nuvem.*

## <a name="step-by-step-guide"></a>Guia passo a passo 

O código completo e um guia passo a passo para implantar os resultados podem ser encontrados no [projeto do GitHub para análise de mídia ao vivo com Video indexer](https://aka.ms/livestreamanalysis). 

## <a name="next-steps"></a>Próximas etapas

[Visão geral do indexador vídeo](video-indexer-overview.md)
