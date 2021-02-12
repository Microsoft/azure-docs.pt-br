---
title: Pesquisar minutos exatos em vídeos com Video Indexer
titleSuffix: Azure Media Services
description: Saiba como procurar minutos exatos em vídeos usando Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/23/2019
ms.author: juliako
ms.openlocfilehash: a1ca7c677e5f1eb55d74cca45e757676674f303c
ms.sourcegitcommit: 2e9643d74eb9e1357bc7c6b2bca14dbdd9faa436
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96030486"
---
# <a name="search-for-exact-moments-in-videos-with-video-indexer"></a>Pesquisar minutos exatos em vídeos com Video Indexer

Este tópico mostra como usar o site Video Indexer para procurar um tempo exato em vídeos.

1. Acesse o site [Video indexer](https://www.videoindexer.ai/) e entre.
1. Especifique as palavras-chave de pesquisa e a pesquisa será executada entre todos os vídeos na biblioteca da sua conta. 

    Você pode filtrar sua pesquisa selecionando **filtros**. No exemplo abaixo, pesquisamos "Microsoft" que aparece como somente texto na tela (OCR).

    :::image type="content" source="./media/video-indexer-search/filter.png" alt-text="Filtrar, somente texto":::
1. Pressione **Pesquisar** para ver o resultado.

    :::image type="content" source="./media/video-indexer-search/results.png" alt-text="Resultado da pesquisa de vídeo":::

    Se você selecionar um dos resultados, o jogador o levará a esse momento exato no vídeo.
1. Exiba e pesquise as informações resumidas do vídeo clicando em **reproduzir** no vídeo ou selecionando um dos resultados da pesquisa original. 

    Você pode exibir, Pesquisar e editar as **informações**. Quando você seleciona uma das informações, o jogador o leva a esse momento exato no vídeo.  

    :::image type="content" source="./media/video-indexer-search/insights.png" alt-text="Exibir, Pesquisar e editar as informações do vídeo":::

    Se você inserir o vídeo por meio de Video Indexer widgets, poderá obter a exibição e a sincronização do Player/insights em seu aplicativo. Para obter mais informações, consulte [Inserir widgets de video indexer em seu aplicativo](video-indexer-embed-widgets.md).
1. Você pode exibir, Pesquisar e editar as transcrições clicando na guia **linha do tempo** . 

    :::image type="content" source="./media/video-indexer-search/timeline.png" alt-text="Exibir, Pesquisar e editar as transcrições do vídeo":::

    Para editar o texto, selecione **Editar** no canto superior direito e altere o texto conforme necessário. 

    Você também pode converter e baixar as transcrições selecionando a opção apropriada no canto superior direito. 

## <a name="embed-download-create-projects"></a>Inserir, baixar, criar projetos

Você pode inserir seu vídeo selecionando **</>inserir** em seu vídeo. Para obter detalhes, consulte [Inserir widgets visuais em seu aplicativo](video-indexer-embed-widgets.md).

Você pode baixar o vídeo de origem, informações do vídeo, transcrições clicando em **baixar** em seu vídeo.

Você pode criar um clipe com base no vídeo de linhas específicas e momentos clicando em **abrir no editor**. Em seguida, editando o vídeo e salvando o projeto. Para obter detalhes, consulte [usar o aprofundamento dos seus vídeos](use-editor-create-project.md).

:::image type="content" source="./media/video-indexer-search/embed-download-create-projects.png" alt-text="Inserir, baixar, criar projetos do vídeo":::

## <a name="next-steps"></a>Próximas etapas

[Processar conteúdo com Video Indexer API REST](video-indexer-use-apis.md)
