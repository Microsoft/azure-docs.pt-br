---
title: Usar o site do Video Indexer para personalizar um modelo de Linguagem – Azure
titlesuffix: Azure Media Services
description: Este artigo mostra como personalizar um modelo de Linguagem com o site do Video Indexer.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 02/10/2019
ms.author: anzaman
ms.openlocfilehash: 7b81b53c03104023823bef75beb4ac6077feede7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60555157"
---
# <a name="customize-a-language-model-with-the-video-indexer-website"></a>Personalizar um modelo de Linguagem com o site do Video Indexer

O Video Indexer permite criar modelos de Linguagem personalizados para personalizar o reconhecimento de fala, carregando o texto de adaptação, ou seja, o texto do domínio a cujo vocabulário você deseja que o mecanismo se adapte. Depois de treinar seu modelo, novas palavras que apareçam no texto de adaptação serão reconhecidas. 

Para obter uma visão geral detalhada e as melhores práticas para modelos de linguagem personalizados, confira [Personalizar um modelo de Linguagem com o Video Indexer](customize-language-model-overview.md).

Você pode usar o site do Video Indexer para criar e editar modelos de linguagem personalizados em sua conta, conforme descrito neste tópico. Você também pode usar a API, conforme descrito em [Personalizar modelo de Linguagem usando APIs](customize-language-model-with-api.md).

## <a name="create-a-language-model"></a>Criar um modelo de Linguagem

1. Navegue até o site do [Video Indexer](https://www.videoindexer.ai/) e entre.
2. Para personalizar um modelo em sua conta, clique no botão **Personalização de modelo de conteúdo** no canto superior direito da página.

   ![Personalizar o modelo de conteúdo](./media/content-model-customization/content-model-customization.png)

3. Selecione a guia **Linguagem**.

    Obtenha uma lista de linguagens de programação compatíveis. 

    ![Personalizar um modelo de linguagem](./media/customize-language-model/customize-language-model.png)

4. Na linguagem de programação que você deseja, clique em **Adicionar modelo**.
5. Digite o nome para o modelo de Linguagem e pressione Enter.

    Isso cria o modelo e oferece a opção de carregar arquivos de texto no modelo.
6. Para adicionar um arquivo de texto, clique em **Adicionar arquivo**. Isso abre o Explorador de Arquivos.

7. Navegue até o arquivo de texto e selecione-o. Você pode adicionar vários arquivos de texto a um modelo de Linguagem.

    Você também pode adicionar um arquivo de texto, clicando no botão **...** no lado direito do modelo de Linguagem e selecionando **Adicionar arquivo**.
8. Depois que você terminar de carregar os arquivos de texto, clique na opção verde **Treinar**.

    ![Treinar modelo de linguagem](./media/customize-language-model/train-model.png)

O processo de treinamento pode levar alguns minutos. Depois que o treinamento for concluído, você verá o texto **Treinado** ao lado do modelo. Você pode visualizar, baixar e excluir o arquivo do modelo.

![Modelo de linguagem treinado](./media/customize-language-model/preview-model.png)

### <a name="using-a-language-model-on-a-new-video"></a>Usando um modelo de Linguagem em um novo vídeo

Para usar seu modelo de Linguagem em um novo vídeo, siga um destes procedimentos:

* Clique no botão **Carregar** na parte superior da página 

    ![Carregar](./media/customize-language-model/upload.png)
* Solte o arquivo de áudio ou vídeo no círculo ou procure o arquivo

    ![Carregar](./media/customize-language-model/upload2.png)

Isso lhe dará a opção de selecionar o **idioma de origem de vídeo**. Clique na lista suspensa e selecione um modelo de Linguagem que você criou na lista. Ele deve indicar o idioma do seu modelo de Linguagem e o nome que você deu a ele entre parênteses.

Clique na opção **Carregar** na parte inferior da página e seu novo vídeo será indexado usando seu modelo de Linguagem.

### <a name="using-a-language-model-to-reindex"></a>Usando um modelo de Linguagem para reindexação

Para usar seu modelo de Linguagem para reindexar um vídeo em sua coleção, vá para seu **Vídeos da conta** na página inicial do [Video Indexer](https://www.videoindexer.ai/) e passe o mouse sobre o nome do vídeo que você deseja reindexar.

Você verá opções para editar seu vídeo, excluí-lo e reindexá-lo. Clique na opção de reindexar o vídeo.

![Reindexar](./media/customize-language-model/reindex1.png)

Isso lhe dá a opção de selecionar o **Idioma de origem de vídeo** com o qual reindexar o vídeo em questão. Clique na lista suspensa e selecione um modelo de Linguagem que você criou na lista. Ele deve indicar o idioma do seu modelo de linguagem e o nome que você deu a ele entre parênteses.

![Reindexar](./media/customize-language-model/reindex.png)

Clique no botão **Reindexar** e o vídeo será reindexado usando o modelo de Linguagem.

## <a name="edit-a-language-model"></a>Editar um modelo de Linguagem

Você pode editar um modelo de Linguagem alterando seu nome, adicionando arquivos a ele e excluindo arquivos dele.

Se adicionar ou excluir arquivos de modelo de idioma, você terá que treinar o modelo novamente, clicando na opção verde **Treinar**.

### <a name="rename-the-language-model"></a>Renomear o modelo de Linguagem

Você pode alterar o nome do modelo de Linguagem clicando em **...**  no lado direito do modelo de Linguagem e selecionando **Renomear**. 

Digite o novo nome e clique em Enter.

### <a name="add-files"></a>Adicionar arquivos

Para adicionar um arquivo de texto, clique em **Adicionar arquivo**. Isso abre o Explorador de Arquivos.

Navegue até o arquivo de texto e selecione-o. Você pode adicionar vários arquivos de texto a um modelo de Linguagem.

Você também pode adicionar um arquivo de texto, clicando no botão **...** no lado direito do modelo de Linguagem e selecionando **Adicionar arquivo**.

### <a name="delete-files"></a>Excluir arquivos

Para excluir um arquivo do modelo de Linguagem, clique no botão **...** no lado direito do arquivo de texto e selecione **Excluir**. Isso abre uma nova janela, informando que a exclusão não pode ser desfeita. Clique na opção **Excluir** na nova janela.

Esta ação remove o arquivo completamente do modelo de Linguagem.

## <a name="delete-a-language-model"></a>Excluir um modelo de Linguagem

Para excluir um modelo de Linguagem de sua conta, clique no botão **...** no lado direito do modelo de Linguagem e selecione **Excluir**.

Isso abre uma nova janela, informando que a exclusão não pode ser desfeita. Clique na opção **Excluir** na nova janela.

Esta ação remove o modelo de Linguagem completamente de sua conta. Qualquer vídeo que estava usando o modelo de Linguagem excluído manterá o mesmo índice até que você reindexe o vídeo. Se você reindexar o vídeo, poderá atribuir um novo modelo de Linguagem a ele. Caso contrário, o Video Indexer usará seu modelo padrão para reindexar o vídeo. 

## <a name="customize-language-models-by-correcting-transcripts"></a>Personalizar modelos de linguagem, corrigindo transcrições

Video Indexer dá suporte à personalização automática da linguagem modelos com base nos usuários reais de correções feitas as transcrições de seus vídeos.

1. Para fazer correções em uma transcrição, abra o vídeo que você deseja editar de seus vídeos de conta. Selecione o **linha do tempo** guia.

    ![Personalizar um modelo de linguagem](./media/customize-language-model/timeline.png)
1. Clique no ícone de lápis para editar a transcrição de sua transcrição. 

    ![Personalizar um modelo de linguagem](./media/customize-language-model/edits.png)

    Indexador de vídeo captura todas as linhas que são corrigidas por você na transcrição do vídeo e adiciona-os automaticamente em um arquivo de texto chamado "de edições de transcrição". Essas edições são usadas para treinar novamente o modelo de linguagem específico que foi usado para indexar este vídeo. 
    
    Se você não especificar um modelo de idioma ao indexar este vídeo, todas as edições para este vídeo serão armazenadas em um modelo de idioma padrão chamado adaptações de conta em que o idioma detectado do vídeo. 
    
    No caso de várias alterações foram feitas para a mesma linha, a última versão da linha corrigida será usada para atualizar o modelo de linguagem.  
    
    > [!NOTE]
    > Somente as correções textuais são usadas para a personalização. Isso significa que as correções que não envolvem palavras reais (por exemplo, sinais de pontuação ou espaços) não são incluídas. 
    
1. Você verá as correções de transcrição aparecem na guia idioma da página de personalização do modelo de conteúdo.

    ![Personalizar um modelo de linguagem](./media/customize-language-model/customize.png)

   Para examinar o arquivo "de edições de transcrição" para cada um dos seus modelos de linguagem, clique para abri-lo. 

    ![De edições de transcrição](./media/customize-language-model/from-transcript-edits.png)

## <a name="next-steps"></a>Próximas etapas

[Personalizar o modelo de linguagem usando APIs](customize-language-model-with-api.md)
