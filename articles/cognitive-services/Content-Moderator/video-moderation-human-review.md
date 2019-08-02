---
title: Moderação de vídeo com revisão humana – Content Moderator
titleSuffix: Azure Cognitive Services
description: Usar a moderação vídeo assistido por computador e ferramentas de análise humana para moderar conteúdo inadequado
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 04/30/2019
ms.author: sajagtap
ms.openlocfilehash: 69075f6b36a0be7cd4d0dd4453ea9cdfc08d77dc
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561166"
---
# <a name="video-moderation-with-human-review"></a>Moderação de vídeo com revisão humana

Use a [ferramenta de análise humana](Review-Tool-User-Guide/human-in-the-loop.md) e moderação de [vídeo](video-moderation-api.md) assistida por computador Content moderator para vídeos moderados e transcrições para conteúdo adulto (explícito) e erótico (Sugestor) para obter os melhores resultados para seus negócios.

## <a name="video-trained-classifier-preview"></a>Classificador treinado para vídeo (versão prévia)

A classificação de vídeo assistido por computador é obtida com os modelos treinados de imagem ou modelos treinados de vídeo. Ao contrário de classificadores de vídeos treinados de imagem, o classificador de vídeos adultos e obscenos da Microsoft é treinado com vídeos. Esse método resulta em melhor qualidade da correspondência.

## <a name="shot-detection"></a>Detecção de captura

Ao emitir os detalhes de classificação, a inteligência adicional de vídeo ajuda com mais flexibilidade na análise de vídeos. Em vez de emitir apenas os quadros, o serviço de moderação de vídeo da Microsoft fornece informações de nível de captura também. Agora você tem a opção para analisar seus vídeos no nível de captura e o nível de quadro.

## <a name="key-frame-detection"></a>Detecção de quadro-chave

Em vez de emitir quadros em intervalos regulares, o serviço de moderação de vídeo identifica e emite apenas os quadros potencialmente completos (bons). O recurso permite a geração de quadro eficiente para análise de adultos e obscenos em nível de quadro.

A extração a seguir mostra uma resposta parcial com capturas potenciais, quadros-chave e pontuações de adultos e obscenos:

```json
"fragments":[  
  {  
    "start":0,
    "duration":18000
  },
  {  
    "start":18000,
    "duration":3600,
    "interval":3600,
    "events":[  
      [  
        {  
          "reviewRecommended":false,
          "adultScore":0.00001,
          "racyScore":0.03077,
          "index":5,
          "timestamp":18000,
          "shotIndex":0
        }
      ]
    ]
  },
  {  
    "start":18386372,
    "duration":119149,
    "interval":119149,
    "events":[  
      [  
        {  
          "reviewRecommended":true,
          "adultScore":0.00000,
          "racyScore":0.91902,
          "index":5085,
          "timestamp":18386372,
          "shotIndex":62
        }
      ]
    ]
```

## <a name="visualization-for-human-reviews"></a>Visualização para revisões humanas

Para obter casos mais sutis, as empresas precisam de uma solução de revisão humana para renderizar o vídeo, seus quadros e marcas atribuídas por máquinas. Os moderadores humanos que revisam vídeos e quadros obtêm uma visão completa das informações, alteram marcas e enviam suas decisões.

![modo de exibição padrão da ferramenta de análise de vídeo](images/video-review-default-view.png)

## <a name="player-view-for-video-level-review"></a>Modo de exibição de Player para análise de nível de vídeo

Decisões binárias de nível de vídeo são possíveis com um modo de exibição do player de vídeo que mostra os quadros potencialmente adultos e obscenos. Os revisores humanos navegam pelo vídeo com várias opções de velocidade para examinar as cenas. Eles confirmam suas decisões alternando as marcas.

![modo de exibição do player da ferramenta de análise de vídeo](images/video-review-player-view.PNG)

## <a name="frames-view-for-detailed-reviews"></a>Modo de exibição de quadros para análises detalhadas

Uma análise de vídeo detalhada para análise quadro-a-quadro é possibilitada por uma exibição com base no quadro. Os revisores humanos revisam e selecionam um ou mais quadros e alternam marcas para confirmar suas decisões. Uma próxima etapa opcional é redação dos quadros ou conteúdos ofensivos.

![exibição de quadros da ferramenta de análise de vídeo](images/video-review-frames-view-apply-tags.PNG)

## <a name="transcript-moderation"></a>Moderação da transcrição

Os vídeos normalmente têm narração que também precisa de moderação para fala ofensiva. Você pode usar o serviço do Azure Media Indexer para converter fala em texto e usar a API de análise do Content Moderator para enviar a transcrição de moderação de texto dentro da ferramenta de análise.

![exibição de transcrição da ferramenta de análise de vídeo](images/video-review-transcript-view.png)

## <a name="next-steps"></a>Próximas etapas

- Introdução com o [Início rápido de moderação de vídeo](video-moderation-api.md).
- Saiba como gerar [análises de vídeos](video-reviews-quickstart-dotnet.md) para seus revisores humanos do seu resultado moderado.
- Adicione [análises de transcrição de vídeo](video-transcript-reviews-quickstart-dotnet.md) às suas análises de vídeo.
- Confira o tutorial detalhado sobre como desenvolver uma [solução completa de moderação de vídeo](video-transcript-moderation-review-tutorial-dotnet.md).