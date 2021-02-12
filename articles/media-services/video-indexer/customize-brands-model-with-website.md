---
title: Personalizar um modelo de marcas com o site do Video Indexer
titleSuffix: Azure Media Services
description: Saiba como personalizar um modelo de marcas com o site Video Indexer.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 12/15/2019
ms.author: kumud
ms.openlocfilehash: a2de9dbb479f43d6b646cd9f6cf604d6a08c8b6a
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/16/2020
ms.locfileid: "97586085"
---
# <a name="customize-a-brands-model-with-the-video-indexer-website"></a>Personalizar um modelo de marcas com o site do Video Indexer

O Video Indexer dá suporte à detecção de marca de fala e texto visual durante a indexação e a reindexação de conteúdo de áudio e de vídeo. O recurso de detecção de marca identifica menções a produtos, serviços e empresas sugeridos pelo banco de dados de marcas do Bing. Por exemplo, se a Microsoft for mencionada em conteúdo de vídeo ou áudio ou se aparecer no texto visual em um vídeo, Video Indexer o detectará como uma marca no conteúdo.

Um modelo de marcas personalizadas permite que você:

- Selecione se você deseja que Video Indexer detecte marcas do banco de dados das marcas do Bing.
- Selecione se você deseja que Video Indexer exclua a detecção de determinadas marcas (essencialmente criando uma lista de negações de marcas).
- Selecione se você deseja que Video Indexer inclua marcas que devem ser parte do seu modelo que podem não estar no banco de dados de marcas do Bing (essencialmente criando uma lista de aceitação de marcas).

Para obter uma visão geral detalhada, consulte esta [visão geral](customize-brands-model-overview.md).

Você pode usar o site do Video Indexer para criar, usar e editar modelos personalizados de marcas detectados em um vídeo, conforme descrito neste tópico. Você também pode usar a API, conforme descrito em [Personalizar modelo de marcas usando APIs](customize-brands-model-with-api.md).

> [!NOTE]
> Se o vídeo tiver sido indexado antes de adicionar uma marca, você precisará reindexá-la. Você encontrará o item de **reindexação** no menu suspenso associado ao vídeo. Selecione **Opções avançadas**  ->  **categorias de marca** e marque **todas as marcas**.

## <a name="edit-brands-model-settings"></a>Editar configurações de modelo de marcas

Você tem a opção de definir se deseja que as marcas do banco de dados de marcas do Bing sejam detectadas ou não. Para definir essa opção, você precisa editar as configurações do modelo de marcas. Execute estas etapas:

1. Acesse o site [Video indexer](https://www.videoindexer.ai/) e entre.
1. Para personalizar um modelo em sua conta, selecione o botão **personalização do modelo de conteúdo** à esquerda da página.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/content-model-customization/content-model-customization.png" alt-text="Personalizar o modelo de conteúdo no Video Indexer":::
1. Para editar marcas, selecione a guia **Marcas**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/customize-brand-model/customize-brand-model.png" alt-text="Captura de tela mostra a guia marcas da caixa de diálogo personalização do modelo de conteúdo":::
1. Marque a opção **Mostrar marcas sugeridas pelo Bing** se desejar que Video indexer detectar marcas sugeridas pelo Bing — deixe a opção desmarcada se você não fizer isso.

## <a name="include-brands-in-the-model"></a>Incluir marcas no modelo

A seção **incluir marcas** representa as marcas personalizadas que você deseja Video indexer para detectar, mesmo que não sejam sugeridas pelo Bing.  

### <a name="add-a-brand-to-include-list"></a>Adicionar uma marca à lista de inclusão

1. Selecione **+ criar nova marca**.

    Forneça um nome (obrigatório), uma categoria (opcional), uma descrição (opcional) e uma URL de referência (opcional).
    A função do campo de categoria é ajudá-lo na marcação das marcas comerciais. Esse campo aparece como as *marcas* da marca comercial ao usar as APIs do Video Indexer. Por exemplo, a marca "Azure" pode ser marcada ou categorizada como "Nuvem".

    O campo URL de referência pode ser qualquer site de referência para a marca (como um link para sua página da Wikipédia).

2. Selecione **salvar** e você verá que a marca foi adicionada à lista de **marcas de inclusão** .

### <a name="edit-a-brand-on-the-include-list"></a>Editar uma marca na lista de inclusões

1. Selecione o ícone de lápis ao lado da marca que você deseja editar.

    Você pode atualizar a categoria, a descrição ou a URL de referência de uma marca. Você não pode alterar o nome de uma marca porque os nomes de marcas são exclusivos. Se você precisar alterar o nome da marca, exclua a marca inteira (consulte a próxima seção) e crie uma nova marca com o novo nome.

2. Selecione o botão **Atualizar** para atualizar a marca com as novas informações.

### <a name="delete-a-brand-on-the-include-list"></a>Excluir uma marca na lista de inclusões

1. Selecione o ícone de Lixeira ao lado da marca que você deseja excluir.
2. Selecione **excluir** e a marca não será mais exibida na lista *incluir marcas* .

## <a name="exclude-brands-from-the-model"></a>Excluir marcas do modelo

A seção **Excluir marcas** representa as marcas que você não deseja que Video indexer detecte.

### <a name="add-a-brand-to-exclude-list"></a>Adicionar uma marca à lista de exclusões

1. Selecione **+ criar nova marca.**

    Forneça um nome (obrigatório) e uma categoria (opcional).

2. Selecione **salvar** e você verá que a marca foi adicionada à lista *Excluir marcas* .

### <a name="edit-a-brand-on-the-exclude-list"></a>Editar uma marca na lista de exclusões

1. Selecione o ícone de lápis ao lado da marca que você deseja editar.

    Só é possível atualizar a categoria de uma marca. Você não pode alterar o nome de uma marca porque os nomes de marcas são exclusivos. Se você precisar alterar o nome da marca, exclua a marca inteira (consulte a próxima seção) e crie uma nova marca com o novo nome.

2. Selecione o botão **Atualizar** para atualizar a marca com as novas informações.

### <a name="delete-a-brand-on-the-exclude-list"></a>Excluir uma marca na lista de exclusões

1. Selecione o ícone de Lixeira ao lado da marca que você deseja excluir.
2. Selecione **excluir** e a marca não será mais exibida na sua lista de *marcas de exclusão* .

## <a name="next-steps"></a>Próximas etapas

[Personalizar o modelo de Marcas usando APIs](customize-brands-model-with-api.md)
