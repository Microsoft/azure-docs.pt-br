---
title: Conceitos do Video Indexer
titlesuffix: Azure Media Services
description: Este tópico descreve alguns conceitos do serviço do Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: c7bbe8c6b2ad51ed7272cd215552807c7cea3aee
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60559814"
---
# <a name="video-indexer-concepts"></a>Conceitos do Video Indexer
 
Este artigo descreve alguns conceitos do serviço do Video Indexer.
    
## <a name="summarized-insights"></a>Informações resumidas

Os insights resumidos contêm uma exibição agregada dos dados: rostos, tópicos, emoções. Por exemplo, em vez de percorrer cada um dos milhares de intervalos de tempo e verificar quais rostos estão nele, os insights resumidos contêm todos os rostos e, para cada um, os intervalos de tempo em que ele aparece e a porcentagem de tempo durante o qual ele é mostrado.

## <a name="time-range-vs-adjusted-time-range"></a>intervalo de tempo versus intervalo de tempo ajustado

TimeRange é o intervalo de tempo no vídeo original. AdjustedTimeRange é o intervalo de tempo em relação à playlist atual. Como é possível criar uma playlist de diferentes linhas de diferentes vídeos, é possível usar um vídeo de 1 hora e usar apenas 1 linha dele, por exemplo, 10:00-10:15. Nesse caso, você terá uma playlist com 1 linha, em que o intervalo de tempo é 10:00-10:15, mas o adjustedTimeRange é 00:00-00:15.
 
## <a name="blocks"></a>Bloqueios

Os blocos destinam-se a tornar mais fácil percorrer os dados. Por exemplo, o bloco pode ser dividido com base em quando os locutores mudam ou quando há uma longa pausa.

## <a name="next-steps"></a>Próximas etapas

Para obter informações sobre como começar, consulte [How to sign up and upload your first video](video-indexer-get-started.md) (Como inscrever-se e carregar seu primeiro vídeo).

## <a name="see-also"></a>Consulte também

[Visão geral do indexador vídeo](video-indexer-overview.md)
