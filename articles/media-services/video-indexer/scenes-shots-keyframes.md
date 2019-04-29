---
title: Vídeo cenas indexador, capturas e quadros-chave - Azure
titlesuffix: Azure Media Services
description: Este tópico fornece uma visão geral do indexador de vídeo nos bastidores, capturas e quadros-chave.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 04/01/2019
ms.author: juliako
ms.openlocfilehash: dfa41dc695cf6ab357a9cd4cdbd32454b6dd107d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60560072"
---
# <a name="scenes-shots-and-keyframes"></a>Cenas, capturas e quadros-chave

Video Indexer dá suporte a vídeos do meio da segmentação em unidades temporais com base nas propriedades estruturais e semânticas. Esse recurso permite aos clientes procurar, gerenciar e editar seu conteúdo de vídeo com base em diferentes granularidades facilmente. Por exemplo, com base nos bastidores, capturas e quadros-chave, descritos neste tópico. O **detecção de cena** recurso está atualmente em versão prévia.   

![Cenas, capturas e quadros-chave](./media/scenes-shots-keyframes/scenes-shots-keyframes.png)

## <a name="scene-detection-preview"></a>Detecção de cena (visualização)

Indexador de vídeo determina quando as alterações de uma cena no vídeo com base em indicações visuais. Uma cena representa um único evento e ele é composto de uma série de capturas consecutivas, que são semanticamente relacionados. Uma miniatura de cena é o primeiro quadro-chave da sua captura subjacente. Indexador de vídeo segmenta um vídeo em segundo plano com base em coerência de cor em capturas consecutivas e recupera o início e a hora de término de cada cena. Detecção de cena é considerada uma tarefa desafiadora, pois envolve Quantificando os aspectos de semânticos de vídeos.

> [!NOTE]
> Se aplica aos vídeos que contêm pelo menos 3 cenas.

## <a name="shot-detection"></a>Detecção de captura

Indexador de vídeo determina quando uma captura alterações no vídeo com base em indicações visuais, acompanhando transições abruptas e graduais no esquema de cores de quadros adjacentes. Os metadados da captura incluem um início e hora de término, bem como a lista de quadros-chave incluídos no que captura. As capturas são quadros consecutivos tirados da câmera mesma ao mesmo tempo.

## <a name="keyframe-detection"></a>Detecção de quadro-chave

Seleciona o quadro (s) que melhor representa a captura. Quadros-chave são os quadros representativos, selecionados de todo o vídeo com base nas propriedades estéticas (por exemplo, contraste e stableness). Indexador de vídeo recupera uma lista de IDs de quadro-chave como parte dos metadados da captura, com base no qual os clientes podem extrair a miniatura do quadro-chave. 

Quadros-chave são associados com capturas da saída JSON. 

## <a name="next-steps"></a>Próximas etapas

[Examine a saída do indexador de vídeo produzida pela API](video-indexer-output-json-v2.md#scenes)