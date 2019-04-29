---
title: Personalizar um modelo de marcas no Video Indexer – Azure
titlesuffix: Azure Media Services
description: Este artigo fornece uma visão geral do que é um modelo de marcas no Video Indexer e como personalizá-lo.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 02/10/2019
ms.author: anzaman
ms.openlocfilehash: 15f4b8b6d4a27e721dfcaed8b9dd700e87e3a257
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61458988"
---
# <a name="customize-a-brands-model-in-video-indexer"></a>Personalizar um modelo de marcas no Video Indexer

O Video Indexer dá suporte à detecção de marca de fala e texto visual durante a indexação e a reindexação de conteúdo de áudio e de vídeo. O recurso de detecção de marca identifica menções a produtos, serviços e empresas sugeridos pelo banco de dados de marcas do Bing. Por exemplo, se a Microsoft é mencionada em um conteúdo de áudio ou de vídeo ou se ela aparece no texto visual em um vídeo, o Video Indexer detecta-a como uma marca presente no conteúdo. Marcas são diferenciadas de outros termos usando o contexto.

Detecção de marca é útil em uma ampla variedade de cenários empresariais, como arquivos de conteúdo e descoberta, anúncios contextuais, análise de mídia social, análise de concorrência no varejo e muito mais. A detecção de marca do Video Indexer permite a você indexar menções de marcas em fala e em texto visual usando o banco de dados de marcas do Bing, bem como com a personalização, criando um modelo de marcas personalizado para cada conta do Video Indexer. O recurso de modelo de marcas personalizado permite que você selecione se deseja ou não que o Video Indexer detecte marcas do banco de dados de marcas do Bing, exclua determinadas marcas da lista daquelas que serão detectadas (essencialmente criando uma lista de bloqueio de marcas) e inclua marcas que devem ser parte de seu modelo e que podem não estar no banco de dados de marcas do Bing (essencialmente criando uma lista de permissões de marcas). O modelo de Marcas personalizado criado só estará disponível na conta na qual você criou o modelo.

## <a name="out-of-the-box-detection-example"></a>Exemplo de detecção pronto para uso

No [2º dia da apresentação do Microsoft Build 2017](https://www.videoindexer.ai/media/ed6ede78ad/), a marca "Microsoft Windows" aparece várias vezes. Às vezes na transcrição, às vezes como texto visual e nunca de modo textual. O Video Indexer detecta com alta precisão que um termo é realmente uma marca com base no contexto, que abrange mais de 90 mil marcas fora da caixa e atualiza constantemente. Em 02:25, o Video Indexer detecta a marca na fala e depois novamente em 02:40 no texto visual, que é parte do logotipo do windows.

![Visão geral de marcas](./media/content-model-customization/brands-overview.png)

Falar sobre janelas no contexto de construção, em inglês, não detectará a palavra "Windows" como uma marca. O mesmo vale para Box, Apple, Fox, etc., com base em algoritmos avançados do Machine Learning que sabem como diferenciar de acordo com o contexto. A detecção de marca funciona para todos os idiomas compatíveis. Clique aqui para obter [vídeo e índice da palestra do 2º dia do Microsoft Build 2017](https://www.videoindexer.ai/media/ed6ede78ad/).

Para trazer suas próprias marcas, confira Próximas etapas.

## <a name="next-steps"></a>Próximas etapas

[Personalizar o modelo de Marcas usando APIs](customize-brands-model-with-api.md)

[Personalizar o modelo de Marcas usando o site](customize-brands-model-with-website.md)
