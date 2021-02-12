---
title: Usar o editor de Video Indexer para criar projetos e adicionar clipes de vídeo
titleSuffix: Azure Media Services
description: Este tópico demonstra como usar o editor de Video Indexer para criar projetos e adicionar clipes de vídeo.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/28/2020
ms.author: juliako
ms.openlocfilehash: 07e4b05e12a5994c707a171c5736aea04a9c9723
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632919"
---
# <a name="add-video-clips-to-your-projects"></a>Adicionar clipes de vídeo aos seus projetos

O site [Video indexer](https://www.videoindexer.ai/) permite que você use as informações aprofundadas do vídeo para: localizar o conteúdo de mídia correto, localizar as partes das quais você está interessado e usar os resultados para criar um projeto totalmente novo. 

Depois de criado, o projeto pode ser renderizado e baixado de Video Indexer e ser usado em seus próprios aplicativos de edição ou fluxos de trabalho downstream.

Alguns cenários em que você pode achar esse recurso útil são: 

* Criando destaques de filmes para os trailers.
* Usando clipes antigos de vídeos em conversões de notícias.
* Criação de conteúdo mais curto para mídia social.

Este artigo mostra como criar um projeto e adicionar os clipes selecionados dos vídeos ao projeto. 

## <a name="create-new-project-and-manage-videos"></a>Criar novo projeto e gerenciar vídeos

1. Navegue até o site do [Video Indexer](https://www.videoindexer.ai/) e entre.
1. Selecione a guia **projetos** . Se você tiver criado projetos antes, verá todos os outros projetos aqui.
1. Clique em **criar novo projeto**.  

    :::image type="content" source="./media/video-indexer-view-edit/new-project.png" alt-text="Criar um novo projeto":::
1. Dê um nome ao seu projeto clicando no ícone de lápis. Substitua o texto que diz "projeto sem título" pelo nome do projeto e clique na marca de seleção.

    :::image type="content" source="./media/video-indexer-view-edit/new-project-edit-name.png" alt-text="Um novo projeto":::
    
### <a name="add-videos-to-the-project"></a>Adicionar vídeos ao projeto

> [!NOTE]
> Atualmente, os projetos podem conter apenas vídeos indexados no mesmo idioma. </br>Depois de selecionar um vídeo em um idioma, você não poderá adicionar os vídeos em sua conta que estejam em um idioma diferente, os vídeos que têm outros idiomas ficarão esmaecidos/desabilitados.

1. Adicione vídeos com os quais você deseja trabalhar neste projeto selecionando **adicionar vídeos**.

    Você verá todos os vídeos em sua conta e uma caixa de pesquisa que diz "Pesquisar texto, palavras-chave ou conteúdo visual". Você pode pesquisar vídeos que tenham uma pessoa, rótulo, marca, palavra-chave ou ocorrência especificada na transcrição e no OCR.
    
    Por exemplo, na imagem abaixo, estávamos procurando vídeos que mencionam "visão personalizada" apenas em transcrição (use **filtro** se você quiser filtrar os resultados da pesquisa).
    
    :::image type="content" source="./media/video-indexer-view-edit/custom-vision.png" alt-text="Captura de tela mostra a pesquisa de vídeos que mencionam a visão personalizada":::
1. Clique em **Adicionar** para adicionar vídeos ao projeto.
1. Agora, você verá todos os vídeos que escolheu. Esses são os vídeos dos quais você vai selecionar os clipes para seu projeto.

    Você pode reorganizar a ordem dos vídeos arrastando e soltando ou selecionando o botão de menu lista e selecionando **mover para baixo** ou **mover para cima**. No menu lista, você também poderá remover o vídeo deste projeto. 
    
    Você pode adicionar mais vídeos a este projeto a qualquer momento selecionando **adicionar vídeos**. Você também pode adicionar várias ocorrências do mesmo vídeo ao seu projeto. Talvez você queira fazer isso se quiser mostrar um clipe de um vídeo e, em seguida, um clipe de outro e, em seguida, outro clipe do primeiro vídeo. 

### <a name="select-clips-to-use-in-your-project"></a>Selecione os clipes para usar em seu projeto

Se você clicar na seta para baixo no lado direito de cada vídeo, abrirá as informações no vídeo com base nos carimbos de data/hora (clipes do vídeo). 

1. Para criar consultas para clipes específicos, use a caixa de pesquisa que diz "Pesquisar em transcrição, texto visual, pessoas e rótulos".
1. Selecione **Exibir informações** para personalizar quais percepções você deseja ver e quais não deseja ver. 

    :::image type="content" source="./media/video-indexer-view-edit/search-try-cognitive-services.png" alt-text="A captura de tela mostra a pesquisa de vídeos que dizem tentar serviços cognitivas":::
1. Adicione filtros para especificar ainda mais detalhes sobre as cenas que você está procurando selecionando **Opções de filtro**.

    Você pode adicionar vários filtros. 
1. Quando estiver satisfeito com os resultados, selecione os clipes que deseja adicionar a este projeto selecionando o segmento que deseja adicionar. Você pode cancelar a seleção deste clipe clicando no segmento novamente.
    
    Adicione todos os segmentos de um vídeo (ou todos os que foram retornados após a pesquisa) clicando na opção de menu lista ao lado do vídeo e selecionando **selecionar tudo**. 

Ao selecionar e ordenar seus clipes, você pode visualizar o vídeo no Player no lado direito da página. 

> [!IMPORTANT]
> Lembre-se de salvar seu projeto ao fazer alterações selecionando **salvar projeto** na parte superior da página. 

### <a name="render-and-download-the-project"></a>Renderizar e baixar o projeto

> [!NOTE]
> Para contas pagas Video Indexer, a renderização do seu projeto tem custos de codificação. Video Indexer contas de avaliação são limitadas a 5 horas de renderização.

1. Quando terminar, verifique se o projeto foi salvo. Agora você pode renderizar este projeto. Clique em **renderizar**, uma caixa de diálogo pop-up aparece informando que o indexador de vídeo processará um arquivo e, em seguida, o link de download será enviado para seu email. Selecione Continuar. 

    :::image type="content" source="./media/video-indexer-view-edit/render-download.png" alt-text="Captura de tela mostra Video Indexer com a opção de processar e baixar seu projeto":::
    
    Você também verá uma notificação informando que o projeto está sendo renderizado na parte superior da página. Depois de fazer a renderização, você verá uma nova notificação informando que o projeto foi renderizado com êxito. Clique na notificação para baixar o projeto. O projeto será baixado no formato MP4.
1. Você pode acessar projetos salvos na guia **projetos** . 

    Se você selecionar este projeto, verá todas as informações e a linha do tempo deste projeto. Se você selecionar o **Editor de vídeo**, poderá continuar fazendo edições neste projeto. As edições incluem adicionar ou remover vídeos e clipes ou renomear o projeto.
    
## <a name="create-a-project-from-your-video"></a>Criar um projeto do seu vídeo

Você pode criar um novo projeto diretamente de um vídeo em sua conta. 

1. Vá para a guia **biblioteca** do site video indexer.
1. Abra o vídeo que você deseja usar para criar seu projeto. Na página informações e linha do tempo, selecione o botão **Editor de vídeo** .

    Isso levará você para a mesma página que você usou para criar um novo projeto. Ao contrário do novo projeto, você vê os segmentos de informações de carimbo de data/hora do vídeo, que começou a editar anteriormente.

## <a name="see-also"></a>Confira também

[Visão geral do indexador vídeo](video-indexer-overview.md)

