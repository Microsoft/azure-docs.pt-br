---
title: Definir e usar fluxos de trabalho de conteúdo por meio da ferramenta de revisão-Content Moderator
titleSuffix: Azure Cognitive Services
description: Você pode usar o designer de fluxo de trabalho do Azure Content Moderator para definir fluxos de trabalho e limites personalizados com base em suas políticas de conteúdo.
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: pafarley
ms.openlocfilehash: 6eb2a2d2762b60a12bb9a24b92e2edae4b846cd1
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/09/2020
ms.locfileid: "96904135"
---
# <a name="define-and-use-moderation-workflows"></a>Definir e usar fluxos de trabalho de moderação

Neste guia, você aprenderá a configurar e usar [fluxos de trabalho](../review-api.md#workflows) no site da [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com) . Os fluxos de trabalho são filtros personalizados baseados em nuvem que você pode usar para lidar com o conteúdo com mais eficiência. Os fluxos de trabalho podem se conectar a uma variedade de serviços para filtrar conteúdo de maneiras diferentes e, em seguida, executar a ação apropriada. Este guia mostra como usar o conector de Content Moderator (que é incluído por padrão) para filtrar o conteúdo e configurar as revisões humanas em um cenário de moderação típico.

## <a name="create-a-new-workflow"></a>Criar um novo fluxo de trabalho

Vá para a [ferramenta de revisão de Content moderator](https://contentmoderator.cognitive.microsoft.com/) e entre. Na guia **Configurações**, selecione **Fluxos de trabalho**.

![Configuração de fluxos de trabalho](images/2-workflows-0.png)

Na próxima tela, selecione **Adicionar fluxo de trabalho**.

![Adicionar um fluxo de trabalho](images/2-workflows-1.png)

### <a name="assign-a-name-and-description"></a>Atribuir nome e descrição

Nomeie seu fluxo de trabalho, insira uma descrição e escolha se o fluxo de trabalho tratará imagens ou texto.

![Nome e descrição do fluxo de trabalho](images/image-workflow-create.PNG)

### <a name="define-evaluation-criteria"></a>Definir critérios de avaliação

Na próxima tela, vá para a seção **se** . No menu suspenso superior, escolha **condição**. Isso permitirá que você configure a condição na qual o fluxo de trabalho executará uma ação. Se você quiser usar várias condições, escolha **combinação** em vez disso. 

Em seguida, selecione um conector. Este exemplo usa **Content Moderator**. Dependendo do conector que você escolher, você obterá opções diferentes para a saída de dados. Consulte a seção [conectores](./configure.md#connectors) do guia de configurações da ferramenta de revisão para saber como configurar outros conectores.

![Selecionar conector do fluxo de trabalho](images/image-workflow-connect-to.PNG)

Escolha a saída desejada a ser usada e defina as condições para fazer check-in.

![Definir condição de fluxo de trabalho](images/image-workflow-condition.PNG)

### <a name="define-the-action"></a>Definir a ação

Vá para a seção **em seguida** , em que você seleciona uma ação. O exemplo a seguir cria uma revisão de imagem e atribui uma marca. Opcionalmente, você pode adicionar um caminho alternativo (senão) e definir uma ação para isso também.

![Definir ação do fluxo de trabalho](images/image-workflow-action.PNG)

### <a name="save-the-workflow"></a>Salvar o fluxo de trabalho

Anote o nome do fluxo de trabalho; Você precisa do nome para iniciar um trabalho de moderação com a API do Workflow (veja abaixo). Por fim, salve o fluxo de trabalho usando o botão **salvar** na parte superior da página.

## <a name="test-the-workflow"></a>Testar o fluxo de trabalho

Agora que você definiu um fluxo de trabalho personalizado, teste-o com o conteúdo de exemplo. Vá para **fluxos de trabalho** e selecione o botão **executar fluxo de trabalho** correspondente.

![Testar fluxo de trabalho](images/image-workflow-execute.PNG)

Salve esta [imagem de exemplo](https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg) em sua unidade local. Em seguida, selecione **escolher arquivo (s)** e carregue a imagem no fluxo de trabalho.

![Um executor com uma cotação sobreposta na imagem](images/sample-text.jpg)

### <a name="track-progress"></a>Controlar o progresso

Você pode exibir o progresso do fluxo de trabalho na próxima janela pop-up.

![Monitorar a execução do fluxo de trabalho](images/image-workflow-job.PNG)

### <a name="verify-workflow-action"></a>Verificar ação do fluxo de trabalho

Vá para a guia **imagem** em **revisão** e verifique se há uma revisão de imagem recém-criada.

![Analisar imagens](images/image-workflow-review.PNG)

## <a name="next-steps"></a>Próximas etapas

Neste guia, você aprendeu a configurar e usar fluxos de trabalho de moderação da ferramenta de [análise](https://contentmoderator.cognitive.microsoft.com)de Content Moderator. Em seguida, consulte o [Guia do console de API](../try-review-api-workflow.md) para saber como criar fluxos de trabalho programaticamente.
