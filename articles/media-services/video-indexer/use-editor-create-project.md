---
title: Use o editor do indexador de vídeo para criar projetos
titlesuffix: Azure Media Services
description: Este tópico demonstra como usar o editor do indexador de vídeo para criar projetos.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 04/02/2019
ms.author: juliako
ms.openlocfilehash: a9d6396cab560a201b98497e787af4b6c7c2dabb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60560713"
---
# <a name="use-the-video-indexer-editor-to-create-projects"></a>Use o editor do indexador de vídeo para criar projetos

Site do indexador de vídeo, permite que você use o aprofundamento dos seus vídeos para: localizar o conteúdo de mídia à direita, localize as partes que você está interessado e usa os resultados para criar um projeto inteiramente novo. Depois de criado, o projeto pode ser processado e baixado do indexador de vídeo e ser usado em sua própria edição de aplicativos ou fluxos de trabalho downstream.

Alguns cenários onde você pode encontrar esse recurso útil são: 

* Destaques de criação de filmes para trailers.
* Usando o antigos clipes de vídeos em conversões de notícias.
* Criação de conteúdo mais curto de mídia social.

Este artigo mostra como criar um projeto a partir do zero e também como criar um projeto de um vídeo em sua conta.

## <a name="create-new-project-and-manage-videos"></a>Criar novo projeto e gerenciem vídeos

1. Navegue até o site do [Video Indexer](https://www.videoindexer.ai/) e entre.
1. Selecione o **projetos** guia. Se você tiver criado projetos antes, você verá todos os seus projetos outros aqui.
1. Clique em **criar novo projeto**.  

    ![Novo Projeto](./media/video-indexer-view-edit/new-project.png)
1. Atribua um nome ao projeto clicando no ícone de lápis. Substitua o texto que diz "Projeto sem título" pelo nome do seu projeto e clique no cheque.

    ![Novo Projeto](./media/video-indexer-view-edit/new-project3.png)
    
### <a name="add-videos-to-the-project"></a>Adicionar vídeos ao projeto

> [!NOTE]
> Atualmente, os projetos podem conter apenas vídeos indexados no mesmo idioma. Depois de selecionar um vídeo em um idioma, você não pode adicionar os vídeos em sua conta que estão em um idioma diferente.

1. Adicionar vídeos que você deseja trabalhar neste projeto, selecionando **Adicionar vídeos**.

    Você verá todos os vídeos em sua conta e uma caixa de pesquisa que diz "Pesquisar por texto, palavras-chave ou conteúdo visual". Para pesquisar vídeos que têm uma pessoa especificada, o rótulo, a marca, a palavra-chave ou o ocorrência na transcrição e OCR.
    
    Por exemplo, na imagem abaixo, estamos procurando vídeos que mencionam "GitHub".
    
    ![GitHub](./media/video-indexer-view-edit/github.png)

    Você pode filtrar ainda mais os resultados selecionando **filtrar resultados**. Você pode filtrar para mostrar os vídeos que têm uma determinada pessoa ou para especificar que você deseja ver os resultados de vídeos que são apenas um em um determinado idioma ou um proprietário específico. <br/> Você também pode especificar o escopo da consulta. Por exemplo, se você quiser pesquisar "GitHub" no OCR, selecione **texto Visual**.

    ![Filter](./media/video-indexer-view-edit/visual-text.png)

    Você pode dispor em camadas vários filtros à sua consulta. Use o **+** / **-** botões para adicionar ou remover filtros. Use **Limpar filtros** para remover todos os filtros.
1. Para adicionar vídeos, selecione-os e, em seguida, selecione **adicionar**.
1. Agora, você verá todos os vídeos que você escolheu. Esses são os vídeos do qual você pretende selecionar clipes para seu projeto.

    Você pode reorganizar a ordem dos vídeos arrastando e soltando ou selecionando o botão de menu de lista e selecionando **mover para baixo** ou **mover para cima**. No menu à lista, você também poderá remover o vídeo com base nesse projeto. 

    ![Reorganizar](./media/video-indexer-view-edit/rearrange.png)
    
    Você tem a opção de adicionar mais vídeos a este projeto a qualquer momento selecionando **Adicionar vídeos**. Você também pode adicionar várias ocorrências do mesmo vídeo ao seu projeto. Você talvez queira fazer isso, se você quiser mostrar um clipe de um vídeo e, em seguida, um clipe de outro e, em seguida, outro clipe de vídeo primeiro. 

### <a name="select-clips-to-use-in-your-project"></a>Selecione clipes para usar em seu projeto

Se você clicar na seta para baixo à direita de cada vídeo, você abrirá os insights de vídeo com base em carimbos de data / hora (clipes de vídeo). 

1. Selecione **exibir Insights** para personalizar quais informações você deseja ver, e que você não deseja ver. 

    ![Exibir insights](./media/video-indexer-view-edit/insights.png)
1. Para criar consultas para clipes específicos, use a caixa de pesquisa que diz "Pesquisar no texto visual, transcrição, pessoas e rótulos".
1. Adicionar filtros para especificar ainda mais detalhes sobre o que você está procurando, selecionando de cenas **opções de filtro**.

    ![Opções de filtro](./media/video-indexer-view-edit/filter-options.png)

    Por exemplo, você talvez queira ver onde o GitHub é mencionado enquanto Donovan Brown estiver na tela de clipes. Para isso, você precisará adicionar um filtro de "inclusão" que tem "Pessoas" como o tipo de informações. Em seguida, você precisa digitar "Donovan Brown" na caixa de pesquisa para o filtro.
    
    ![Incluir](./media/video-indexer-view-edit/include.png)
    
    Se você quiser clipes em que o GitHub é mencionado enquanto estiver Donovan Brown _não_ na tela, bastaria você alterar o filtro de "inclusão" em um filtro de "excluir" usando o menu suspenso. 

1. Adicione um clipe ao seu projeto, selecionando o segmento que você deseja adicionar. Você pode desmarcar o clipe clicando no segmento novamente.
    
    Adicionar todos os segmentos de um vídeo, clicando na opção de menu lista ao lado do vídeo e selecionando **selecionar todos os segmentos**. 

    ![Adicionar todos](./media/video-indexer-view-edit/add-all.png)

    Você pode limpar todos da sua seleção selecionando Limpar seleção.

> [!TIP]
> Conforme você seleciona e ordenação os clipes, você pode visualizar o vídeo no player no lado direito da página. 

![Visualização](./media/video-indexer-view-edit/preview.png)

Lembre-se de salvar seu projeto quando você fizer alterações, selecionando **Salvar projeto**. 

### <a name="render-and-download-the-project"></a>Renderizar e baixe o projeto

> [!NOTE]
> Indexador de vídeo pago contas, seu projeto de renderização tem custos de codificação. Vídeos contas de avaliação do indexador são limitadas a 5 horas de renderização.

1. Quando terminar, certifique-se de que seu projeto foi salvo. Agora você pode renderizar este projeto. Selecione **renderizar e baixar**. 

    ![Salvar](./media/video-indexer-view-edit/save.png)

    Haverá um pop-up que informa que o indexador de vídeo renderizará um arquivo e, em seguida, o link de download será enviado ao seu email. Selecione continuar. 
    
    Você também verá uma notificação de que o projeto está sendo renderizado na parte superior da página. Quando terminar sendo processado, você verá uma nova notificação de que o projeto tenha sido processado com êxito. Clique na notificação para baixar o projeto. Ele baixará o projeto no formato mp4.

    ![Processamento concluído](./media/video-indexer-view-edit/rendering-done.png)

1. Você pode acessar projetos salvos a partir de **projetos** guia. 

    Se você selecionar este projeto, você verá todos os insights e a linha do tempo desse projeto. Se você selecionar **editor de vídeo**, você pode continuar a fazer edições para este projeto. Edições incluem adicionar ou remover vídeos e clipes ou renomear o projeto.

    ![Editor de vídeo](./media/video-indexer-view-edit/video-editor.png)
     
## <a name="create-a-project-from-your-video"></a>Criar um projeto do seu vídeo

Você pode criar um novo projeto diretamente de um vídeo em sua conta. 

1. Vá para o **biblioteca** guia do site do indexador de vídeo.
1. Abra o vídeo que você deseja usar para criar seu projeto. Na página de linha do tempo e insights, selecione a **editor de vídeo** botão.

    Isso leva você para a mesma página que você usou para criar um novo projeto. Ao contrário do novo projeto, você verá os segmentos de insights de data/hora do vídeo, que você começou editando anteriormente.

## <a name="see-also"></a>Consulte também

[Visão geral do indexador vídeo](video-indexer-overview.md)

