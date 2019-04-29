---
title: Usar revisões de conteúdo por meio da ferramenta de revisão - Content Moderator
titlesuffix: Azure Cognitive Services
description: Saiba como a ferramenta de revisão permite que moderadores humanos revisem imagens em um portal da Web.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 03/15/2019
ms.author: sajagtap
ms.openlocfilehash: a482ecf4a0d321525ab7e392695d2c4c0eebeadc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60628963"
---
# <a name="create-human-reviews"></a>Criar análises humanas

Neste guia, você aprenderá como configurar [revisa](../review-api.md#reviews) no site da ferramenta de revisão. Revisões de armazenam e exibem o conteúdo para moderadores humanos avaliar. Os moderadores podem alterar as marcas aplicadas e aplicar suas próprias marcas personalizadas conforme apropriado. Quando um usuário conclui uma revisão, os resultados são enviados para um ponto de extremidade de retorno de chamada especificado e o conteúdo é removido do site.

## <a name="prerequisites"></a>Pré-requisitos

- Entrar ou criar uma conta no Content Moderator [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com/) site.

## <a name="image-reviews"></a>Análises de imagem

1. Vá para o [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com/), selecione o **tente** guia e, em seguida, carregar algumas imagens para examinar.
1. Depois de terminar processamento de imagens carregadas, vá para o **revisão** e selecione **imagem**.

    ![Navegador Chrome que mostra a ferramenta de análise com a opção de examinar imagem realçada](images/review-images-1.png)

    As imagens exibidas com os rótulos que foram atribuídos pelo processo de moderação automática. As imagens que você tiver enviado por meio da ferramenta de revisão não são visíveis para outros revisores.

1. Se preferir, mova o **revisões para exibir** controle deslizante (1) para ajustar o número de imagens que são exibidas na tela. Clique no **marcadas** ou **sem marcas** botões (2) para classificar as imagens de forma adequada. Clique em um painel de marcas (3) para ativá-lo ativado ou desativado.

    ![Navegador Chrome mostrando a ferramenta Revisar com imagens marcadas para revisão](images/review-images-2.png)

1. Para obter mais informações sobre uma imagem, clique nas reticências na miniatura e marque **exibir detalhes**. Você pode atribuir uma imagem para uma subequipe com o **mover para** opção (consulte a [equipes](./configure.md#manage-team-and-subteams) seção para saber mais sobre subequipes).

    ![Uma imagem com a opção de exibição de detalhes realçada](images/review-images-3.png)

1. Procure as informações de moderação de imagem na página de detalhes.

    ![Uma imagem com detalhes de moderação listados em um painel separado](images/review-images-4.png)

1. Depois de ter revisado e atualizado as atribuições de marca, conforme necessário, clique em **Próximo** para enviar suas revisões. Depois de enviar, você tem cerca de cinco segundos para clicar no botão **Prev** para retornar à tela anterior e examinar imagens novamente. Depois disso, as imagens não estarão mais na fila de envio e o botão **Prev** não estará mais disponível.

## <a name="text-reviews"></a>Análises de texto

Texto examina a função da mesma forma que as revisões de imagem. Em vez de carregar o conteúdo, você simplesmente escreve ou colar em texto (até 1.024 caracteres). Em seguida, o Content Moderator analisa o texto e aplica marcas (além de outras informações de moderação, como conteúdo ofensivo e dados pessoais). Análises de texto, você pode alternar as marcas aplicadas e/ou aplicar marcações personalizadas antes de enviar a revisão.

![Captura de tela da ferramenta de revisão mostrando o texto sinalizado em uma janela do navegador Chrome](../images/reviewresults_text.png)

## <a name="next-steps"></a>Próximas etapas

Neste guia, você aprendeu a configurar e usar as revisões do Content Moderator [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com). Em seguida, consulte a [guia da API REST](../try-review-api-review.md) ou o [guia de SDK do .NET](../moderation-reviews-quickstart-dotnet.md) para aprender a criar as revisões de forma programática.