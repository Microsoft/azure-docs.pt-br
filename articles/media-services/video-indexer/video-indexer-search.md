---
title: Localizar momentos exatos em vídeos – Video Indexer
titlesuffix: Azure Media Services
description: Este tópico demonstra como localizar momentos exatos em vídeos usando o Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 02/25/2019
ms.author: juliako
ms.openlocfilehash: 07b3c806dc5df5f93bee3206cbca53485675e7dd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60560312"
---
# <a name="find-exact-moments-within-videos"></a>Localizar os minutos exatos nos vídeos

Este tópico mostra as opções de pesquisa que permitem que você localize os minutos exatos nos vídeos.

1. Navegue até o site do [Video Indexer](https://www.videoindexer.ai/) e entre.
2. Pesquise entre todos os vídeos da sua conta.

    No exemplo a seguir, podemos pesquisar todos os vídeos que falam sobre a segurança e na qual Satya é exibida,

    ![Search](./media/video-indexer-search/video-indexer-search01.png)
3. Pesquise os insights resumidos do vídeo.

    Em seguida, é possível pesquisar dentro de um vídeo clicando em **Reproduzir** no vídeo. Em seguida, é possível pesquisar no vídeo selecionando a guia **Pesquisar**. 

    No exemplo a seguir, vamos procurar "segura" dentro do vídeo selecionado.

    ![Search](./media/video-indexer-search/video-indexer-search02.png)

    Se você clicar em um dos resultados, o player direcionará você até esse momento no vídeo. É possível obter a exibição do player/insights e a sincronização no seu aplicativo. Para obter mais informações, consulte [Embed Video Indexer widgets into your application](video-indexer-embed-widgets.md) (Inserir widgets do Video Indexer em seu aplicativo). 
4. Pesquise a análise detalhada do vídeo.
    
    Se você quiser criar seus próprios clipes com base no vídeo que você encontrou, pressione a **editar** botão. Esta página mostra vídeo junto com seus insights como filtros. Para obter mais informações, consulte [Exibir e editar insights do Video Indexer](video-indexer-view-edit.md). 

    Você pode pesquisar no vídeo para mostrar apenas as linhas que você está interessado e usa os insights de lado para filtrar as partes que você deseja ver. Quando você terminar, você pode visualizar o clipe e pressione **publicar** para criar o novo clipe que aparece na Galeria de.
    
    O exemplo a seguir, procuramos o texto "realidade misturada". Também aplicamos filtros adicionais, conforme mostrado na tela abaixo.
    
    ![Search](./media/video-indexer-search/video-indexer-search03.png)

## <a name="next-steps"></a>Próximas etapas 

Depois de encontrar o vídeo com o qual você deseja trabalhar, será possível continuar processando-o, conforme descrito em um destes tópicos: 

- [Criar novos insights de vídeo com base no vídeo existente](video-indexer-create-new.md)
- [Processar conteúdo com a API REST do Video Indexer](video-indexer-use-apis.md)
- [Inserir widgets visuais em seu aplicativo](video-indexer-embed-widgets.md)

## <a name="see-also"></a>Consulte também

[Visão geral do indexador vídeo](video-indexer-overview.md)
