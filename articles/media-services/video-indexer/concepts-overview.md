---
title: Conceitos de Video Indexer – Azure
titleSuffix: Azure Media Services Video Indexer
description: Este artigo fornece uma breve visão geral dos serviços de mídia do Azure Video Indexer terminologia e conceitos.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/19/2021
ms.author: juliako
ms.openlocfilehash: 41c9dfe9251da3bddb16ff507ebd512713c3b88a
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98633727"
---
# <a name="video-indexer-concepts"></a>Conceitos do Video Indexer

Este artigo fornece uma breve visão geral dos serviços de mídia do Azure Video Indexer terminologia e conceitos.

## <a name="audiovideocombined-insights"></a>Áudio/vídeo/informações combinadas

Ao carregar seus vídeos para o Video Indexer, ele executa modelos de IA diferentes para analisar os elementos visuais e de áudio. À medida que o Video Indexer analisa o vídeo, modelos de IA extraem os insights. Para obter mais informações, consulte [visão geral](video-indexer-overview.md).

## <a name="confidence-scores"></a>Pontuações de Confiança

A pontuação de confiança indica a confiança em um insight. É um número entre 0,0 e 1,0. Quanto maior a pontuação, maior a confiança na resposta. Por exemplo, 

```json
"transcript":[
{
  "id":1,
  "text":"Well, good morning everyone and welcome to",
  "confidence":0.8839,
  "speakerId":1,
  "language":"en-US",
  "instances":[
     {
    "adjustedStart":"0:00:10.21",
    "adjustedEnd":"0:00:12.81",
    "start":"0:00:10.21",
    "end":"0:00:12.81"
     }
  ]
},
```

## <a name="content-moderation"></a>Moderação de conteúdo

Use modelos de moderação de conteúdo visual e textual para manter seus usuários protegidos contra conteúdo inadequado e validar que o conteúdo publicado corresponde aos valores da sua organização. Você pode bloquear automaticamente determinados vídeos ou alertar os usuários sobre o conteúdo. Para obter mais informações, consulte [insights: Visual e moderação de conteúdo textual](video-indexer-output-json-v2.md#visualcontentmoderation). 

## <a name="blocks"></a>Blocos   

Os blocos destinam-se a tornar mais fácil percorrer os dados. Por exemplo, o bloco pode ser dividido com base em quando os locutores mudam ou quando há uma longa pausa.  

## <a name="project-and-editor"></a>Projeto e editor

O site [Video indexer](https://www.videoindexer.ai/) permite que você use as informações aprofundadas do vídeo para: localizar o conteúdo de mídia correto, localizar as partes das quais você está interessado e usar os resultados para criar um projeto totalmente novo. Depois de criado, o projeto pode ser renderizado e baixado de Video Indexer e ser usado em seus próprios aplicativos de edição ou fluxos de trabalho downstream.

Alguns cenários em que você pode achar esse recurso útil são: 

* Criando destaques de filmes para os trailers.
* Usando clipes antigos de vídeos em conversões de notícias.
* Criação de conteúdo mais curto para mídia social.

Para obter mais informações, consulte [usar o editor para criar projetos](use-editor-create-project.md).

## <a name="keyframes"></a>Quadros chave

Video Indexer seleciona os quadros que melhor representam cada imagem. Quadros-chave são os quadros representativos selecionados de todo o vídeo com base em Propriedades estética (por exemplo, contraste e capacidade de ativação). Para obter mais informações, consulte [Cenas, capturas e quadros-chave](scenes-shots-keyframes.md).

## <a name="time-range-vs-adjusted-time-range"></a>intervalo de tempo versus intervalo de tempo ajustado   

TimeRange é o intervalo de tempo no vídeo original. AdjustedTimeRange é o intervalo de tempo em relação à playlist atual. Como é possível criar uma playlist de diferentes linhas de diferentes vídeos, é possível usar um vídeo de 1 hora e usar apenas 1 linha dele, por exemplo, 10:00-10:15. Nesse caso, você terá uma playlist com 1 linha, em que o intervalo de tempo é 10:00-10:15, mas o adjustedTimeRange é 00:00-00:15. 

## <a name="widgets"></a>Widgets

O Video Indexer dá suporte a widgets de inserção em seus aplicativos. Para obter mais informações, consulte [inserir Video indexer widgets em seus aplicativos](video-indexer-embed-widgets.md).

## <a name="summarized-insights"></a>Informações resumidas  

Os insights resumidos contêm uma exibição agregada dos dados: rostos, tópicos, emoções. Por exemplo, em vez de percorrer cada um dos milhares de intervalos de tempo e verificar quais rostos estão nele, os insights resumidos contêm todos os rostos e, para cada um, os intervalos de tempo em que ele aparece e a porcentagem de tempo durante o qual ele é mostrado.  

## <a name="next-steps"></a>Próximas etapas

- [visão geral](video-indexer-overview.md)
- [Insights](video-indexer-output-json-v2.md)
