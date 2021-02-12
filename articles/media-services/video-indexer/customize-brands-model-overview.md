---
title: Personalizar um modelo de marcas no Video Indexer – Azure
titleSuffix: Azure Media Services
description: Este artigo fornece uma visão geral do que é um modelo de marcas no Video Indexer e como personalizá-lo.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 12/15/2019
ms.author: juliako
ms.openlocfilehash: 81d7dda854c6afcc9397289ff23ba45b02ed9fc4
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/16/2020
ms.locfileid: "97586067"
---
# <a name="customize-a-brands-model-in-video-indexer"></a>Personalizar um modelo de marcas no Video Indexer

O Video Indexer dá suporte à detecção de marca de fala e texto visual durante a indexação e a reindexação de conteúdo de áudio e de vídeo. O recurso de detecção de marca identifica menções a produtos, serviços e empresas sugeridos pelo banco de dados de marcas do Bing. Por exemplo, se a Microsoft é mencionada em um conteúdo de áudio ou de vídeo ou se ela aparece no texto visual em um vídeo, o Video Indexer detecta-a como uma marca presente no conteúdo. Marcas são diferenciadas de outros termos usando o contexto.

Detecção de marca é útil em uma ampla variedade de cenários empresariais, como arquivos de conteúdo e descoberta, anúncios contextuais, análise de mídia social, análise de concorrência no varejo e muito mais. A detecção de marca do Video Indexer permite a você indexar menções de marcas em fala e em texto visual usando o banco de dados de marcas do Bing, bem como com a personalização, criando um modelo de marcas personalizado para cada conta do Video Indexer. O recurso de modelo de marcas personalizadas permite que você selecione se Video Indexer detectará marcas do banco de dados de marcas do Bing, excluirá a detecção de determinadas marcas (essencialmente criando uma lista de marcas não aprovadas) e incluirá marcas que devem ser parte do seu modelo que podem não estar no banco de dados de marcas do Bing (essencialmente criando uma lista de marcas aprovadas). O modelo de Marcas personalizado criado só estará disponível na conta na qual você criou o modelo.

## <a name="out-of-the-box-detection-example"></a>Exemplo de detecção pronto para uso

Na apresentação "Microsoft Build 2017 Day 2", a marca "Microsoft Windows" aparece várias vezes. Às vezes na transcrição, às vezes como texto visual e nunca de modo textual. O Video Indexer detecta com alta precisão que um termo é realmente uma marca com base no contexto, que abrange mais de 90 mil marcas fora da caixa e atualiza constantemente. Em 02:25, o Video Indexer detecta a marca na fala e depois novamente em 02:40 no texto visual, que é parte do logotipo do windows.

![Visão geral de marcas](./media/content-model-customization/brands-overview.png)

Falar sobre janelas no contexto de construção, em inglês, não detectará a palavra "Windows" como uma marca. O mesmo vale para Box, Apple, Fox, etc., com base em algoritmos avançados do Machine Learning que sabem como diferenciar de acordo com o contexto. A detecção de marca funciona para todos os idiomas compatíveis.  

## <a name="next-steps"></a>Próximas etapas

Para trazer suas próprias marcas, confira estes tópicos:

[Personalizar o modelo de Marcas usando APIs](customize-brands-model-with-api.md)

[Personalizar o modelo de Marcas usando o site](customize-brands-model-with-website.md)
