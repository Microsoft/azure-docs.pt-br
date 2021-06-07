---
title: Inscrever-se no Video Indexer e carregar seu primeiro vídeo – Azure
titleSuffix: Azure Media Services
description: Saiba como se inscrever e carregar seu primeiro vídeo usando o portal do Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: quickstart
ms.date: 01/25/2021
ms.author: juliako
ms.openlocfilehash: 76862800e88b41a70449cff4a4ffad072c755272
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106108033"
---
# <a name="quickstart-how-to-sign-up-and-upload-your-first-video"></a>Início Rápido: Como inscrever-se e atualizar seu primeiro vídeo

Este guia de início rápido de introdução mostra como entrar no site do Video Indexer e como carregar seu primeiro vídeo.

Ao criar uma conta do Video Indexer, você pode escolher uma conta de avaliação gratuita (em que você obtém um determinado número de minutos de indexação gratuitos) ou uma opção paga (onde você não está limitado pela cota). Com o teste gratuito, o Video Indexer fornece até 600 minutos de indexação gratuita para usuários do site e até 2400 minutos de indexação gratuita para usuários da API. Com a opção paga, você cria uma conta do Video Indexer que está [conectada à sua assinatura do Azure e a uma conta dos Serviços de Mídia do Azure](connect-to-azure.md). Você paga pelos minutos indexados. Para obter mais informações, confira [Preços dos Serviços de Mídia](https://azure.microsoft.com/pricing/details/media-services/). 

## <a name="sign-up-for-video-indexer"></a>Inscrever-se no Video Indexer

Para começar a desenvolver com o Video Indexer, navegue para o site do [Video Indexer](https://www.videoindexer.ai/) e inscreva-se.

Depois de começar a usar o Video Indexer, todos os dados armazenados e o conteúdo carregado serão criptografados em repouso com uma chave gerenciada da Microsoft.

> [!NOTE]
> Examine [alterações de autenticação planejadas no site do Video Indexer](release-notes.md#planned-video-indexer-website-authenticatication-changes).

## <a name="upload-a-video-using-the-video-indexer-website"></a>Carregar um vídeo usando o site do Video Indexer

### <a name="supported-file-formats-for-video-indexer"></a>Formatos de arquivo compatíveis com o Video Indexer

Confira o artigo [contêiner de entrada/formatos de arquivo](../latest/encode-media-encoder-standard-formats-reference.md) para obter uma lista de formatos de arquivo que você pode usar com o Video Indexer.

### <a name="upload-a-video"></a>Carregar um vídeo

1. Conecte-se ao site do [Video Indexer](https://www.videoindexer.ai/).
1. Para carregar um vídeo, pressione o botão ou o link **Carregar**.

    > [!NOTE]
    > O nome do vídeo não deve ter mais de 80 caracteres.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/video-indexer-get-started/video-indexer-upload.png" alt-text="Upload":::
1. Depois que o seu vídeo tiver sido carregado, o Video Indexer iniciará a indexação e a análise do vídeo. Veja o progresso. 

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/video-indexer-get-started/progress.png" alt-text="Progresso do upload":::
1. Depois que o Video Indexer terminar de analisar, você receberá um email com um link para seu vídeo e uma breve descrição do que foi encontrado nele. Por exemplo: pessoas, palavras faladas e escritas, tópicos e entidades nomeadas.
1. Posteriormente, você poderá encontrar seu vídeo na lista de bibliotecas e executar operações diferentes. Por exemplo: pesquisar, reindexar, editar.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/video-indexer-get-started/uploaded.png" alt-text="Carregou o upload":::

## <a name="supported-browsers"></a>Navegadores com suporte

Para mais informações, confira [navegadores com suporte](video-indexer-overview.md#supported-browsers).

## <a name="see-also"></a>Veja também

Confira [Carregar e indexar vídeos](upload-index-videos.md) para obter mais detalhes.

Depois de carregar e indexar um vídeo, você poderá começar a usar o site do [Video Indexer](video-indexer-view-edit.md) ou o [Portal do Desenvolvedor do Video Indexer](video-indexer-use-apis.md) para ver os insights do vídeo. 

[Começar a usar APIs](video-indexer-use-apis.md)

## <a name="next-steps"></a>Próximas etapas

Para obter uma introdução detalhada, visite nosso [laboratório de introdução](https://github.com/Azure-Samples/media-services-video-indexer/blob/master/IntroToVideoIndexer.md). 

Ao final do workshop, você terá um bom entendimento dos tipos de informações que podem ser extraídas do conteúdo de vídeo e áudio, estará mais preparado para identificar oportunidades relacionadas à inteligência de conteúdo e fará uma demonstração da IA de vídeo no Azure e de vários cenários no Video Indexer.

