---
title: Definir e usar fluxos de trabalho de conteúdo por meio da ferramenta de revisão - Content Moderator
titlesuffix: Azure Cognitive Services
description: Você pode usar o designer de fluxo de trabalho do Azure Content Moderator para definir limites com base em suas políticas de conteúdo e fluxos de trabalho personalizados.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 04/04/2019
ms.author: sajagtap
ms.openlocfilehash: 006f7d6691b8872aaa7ff8ccacff484585761d00
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61271036"
---
# <a name="define-and-use-moderation-workflows"></a>Definir e usar fluxos de trabalho de moderação

Neste guia, você aprenderá a configurar e usar [fluxos de trabalho](../review-api.md#workflows) sobre o [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com) site. Fluxos de trabalho são filtros personalizados baseados em nuvem que você pode usar para manipular o conteúdo com mais eficiência. Fluxos de trabalho podem se conectar a uma variedade de serviços para filtrar o conteúdo de maneiras diferentes e, em seguida, execute a ação apropriada. Este guia mostra como usar o conector do Content Moderator (que é incluído por padrão) para filtrar o conteúdo e configurar análises humanas em um cenário típico de moderação.

## <a name="create-a-new-workflow"></a>Criar um novo fluxo de trabalho

Vá para o [ferramenta de revisão do Content Moderator](https://contentmoderator.cognitive.microsoft.com/) e entre. Na guia **Configurações**, selecione **Fluxos de trabalho**.

![Configuração de fluxos de trabalho](images/2-workflows-0.png)

Na próxima tela, selecione **adicionar fluxo de trabalho**.

![Adicionar um fluxo de trabalho](images/2-workflows-1.png)

### <a name="assign-a-name-and-description"></a>Atribuir nome e descrição

Nomeie seu fluxo de trabalho, insira uma descrição e escolha se o fluxo de trabalho lidar com imagens ou texto.

![Nome e descrição do fluxo de trabalho](images/image-workflow-create.PNG)

### <a name="define-evaluation-criteria"></a>Definir critérios de avaliação

Na próxima tela, vá para o **se** seção. No menu suspenso superior, escolha **condição**. Isso permitirá que você configure a condição na qual o fluxo de trabalho executará uma ação. Se você quiser usar várias condições, escolha **combinação** em vez disso. 

Em seguida, selecione um conector. Este exemplo usa **Content Moderator**. Dependendo do conector que você escolher, você obterá as diferentes opções para saída de dados. Consulte a [conectores](./configure.md#connectors) seção do guia de configurações do ferramenta de revisão para aprender a configurar outros conectores.

![Selecione o conector de fluxo de trabalho](images/image-workflow-connect-to.PNG)

Escolha a saída desejada para usar e definir as condições a serem verificadas em relação ao.

![Definir condição de fluxo de trabalho](images/image-workflow-condition.PNG)

### <a name="define-the-action"></a>Definir a ação

Vá para o **, em seguida,** seção, em que você selecione uma ação. O exemplo a seguir cria uma revisão de imagem e atribui uma marca. Opcionalmente, você pode adicionar um caminho alternativo de (Else) e definir uma ação para fazer isso também.

![Definir ação do fluxo de trabalho](images/image-workflow-action.PNG)

### <a name="save-the-workflow"></a>Salvar o fluxo de trabalho

Anote o nome do fluxo de trabalho; Você precisa do nome para iniciar um trabalho de moderação com a API de fluxo de trabalho (veja abaixo). Por fim, salve o fluxo de trabalho usando o **salvar** botão na parte superior da página.

## <a name="test-the-workflow"></a>Testar o fluxo de trabalho

Agora que você definiu um fluxo de trabalho personalizado, você deve testá-lo com conteúdo de exemplo. Vá para **fluxos de trabalho** e selecione correspondente **fluxo de trabalho Execute** botão.

![Testar fluxo de trabalho](images/image-workflow-execute.PNG)

Salvar esta [imagem de exemplo](https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg) em sua unidade local. Em seguida, selecione **escolha o arquivo (s)** e carregar a imagem no fluxo de trabalho.

![Um executor com uma citação sobreposta na imagem](images/sample-text.jpg)

### <a name="track-progress"></a>Controlar o progresso

Você pode exibir o progresso do fluxo de trabalho na janela pop-up seguinte.

![Monitorar a execução do fluxo de trabalho](images/image-workflow-job.PNG)

### <a name="verify-workflow-action"></a>Verifique se a ação de fluxo de trabalho

Vá para o **imagem** guia sob **examine** e verifique se existe uma revisão de imagem recém-criada.

![Analisar imagens](images/image-workflow-review.PNG)

## <a name="next-steps"></a>Próximos passos

Neste guia, você aprendeu a configurar e usar fluxos de trabalho de moderação do Content Moderator [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com). Em seguida, consulte a [guia da API REST](../try-review-api-workflow.md) para aprender a criar fluxos de trabalho de forma programática.
