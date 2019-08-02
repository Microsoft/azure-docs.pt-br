---
title: 'Início Rápido: Experimentar o Content Moderator na Web – Content Moderator'
titleSuffix: Azure Cognitive Services
description: Neste início rápido, você usará a ferramenta de revisão online Content Moderator para testar a funcionalidade básica do Content Moderator sem precisar escrever nenhum código.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: sajagtap
ms.openlocfilehash: e5b20bb45c08330c59fc72712b9e65b488c8c68b
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68564452"
---
# <a name="quickstart-try-content-moderator-on-the-web"></a>Início Rápido: Experimentar o Content Moderator na Web

Neste início rápido, você usará a ferramenta de revisão online Content Moderator para testar a funcionalidade básica do Content Moderator sem precisar escrever nenhum código. Se você deseja integrar esse serviço ao seu aplicativo mais rapidamente, consulte as outras iniciações rápidas na seção [Próximas etapas](#next-steps).

## <a name="prerequisites"></a>Pré-requisitos

- Um navegador da Web

## <a name="set-up-the-review-tool"></a>Configurar a ferramenta de revisão
A Ferramenta de Revisão do Content Moderator é uma ferramenta baseada na Web que permite que revisores humanos auxiliem o serviço cognitivo na tomada de decisões. Neste guia, você passará pelo curto processo de configuração da ferramenta de revisão para poder ver como o serviço Moderador de Conteúdo funciona. Vá para o site da [Ferramenta de Revisão do Content Moderator](https://contentmoderator.cognitive.microsoft.com/) e inscreva-se.

![Home Page do Content Moderator](images/homepage.PNG)

## <a name="create-a-review-team"></a>Criar uma equipe de análise

Em seguida, crie uma equipe de revisão. Em um cenário de trabalho, esse será o grupo de pessoas que analisará manualmente as decisões de moderação do serviço. Por enquanto, você só precisa criar um nome de equipe. Se você deseja convidar colegas para a equipe, pode fazê-lo digitando seus endereços de e-mail aqui.

![Convidar membro da equipe](images/QuickStart-2-small.png)

## <a name="upload-sample-content"></a>Carregar o conteúdo de exemplo

Agora você está pronto para carregar o conteúdo de exemplo. Selecione **tente > imagem**, **tente > texto**, ou **tente > vídeo**.

![Experimentar Imagem ou Moderação de Texto](images/tryimagesortext.png)

Envie seu conteúdo para moderação. Internamente, a ferramenta de análise chama as APIs de moderação para verificar seu conteúdo. Quando a varredura estiver concluída, você verá uma mensagem informando que há resultados aguardando sua análise.

![Moderar arquivos](images/submitted.png)

## <a name="review-moderation-tags"></a>Revisar tags de moderação

Revise as tags de moderação aplicadas. Você pode ver quais tags foram aplicadas ao seu conteúdo e qual foi a pontuação em cada categoria. Consulte a [imagem](image-moderation-api.md), [texto](text-moderation-api.md), e [vídeo](video-moderation-api.md) indicam tópicos de moderação para saber mais sobre as marcas um conteúdo diferente.

![Analisar resultados](images/reviewresults_text.png)

Em um projeto, você ou sua equipe de revisão podem alterar essas tags ou adicionar mais tags, conforme necessário. Você enviará essas alterações com o botão **Próximo**. À medida que seu aplicativo de negócios chama as APIs do moderador, o conteúdo marcado ficará na fila aqui, pronto para ser analisado pelas equipes de revisão humanas. Você pode revisar rapidamente grandes volumes de conteúdo usando essa abordagem.

Neste ponto, você usou a Ferramenta de Revisão do Content Moderator para ver exemplos do que o serviço Content Moderator pode fazer. Em seguida, você pode aprender mais sobre a ferramenta de revisão e como integrá-la em um projeto de software usando as APIs de revisão ou pular para a seção [Próximas etapas](#next-steps) para aprender a usar as próprias APIs de moderação seu aplicativo.

## <a name="learn-more-about-the-review-tool"></a>Saiba mais sobre a ferramenta de revisão

Para saber mais sobre como usar a Ferramenta Revisão do Content Moderator, consulte o guia [Ferramenta de revisão](Review-Tool-User-Guide/human-in-the-loop.md) e veja as APIs da ferramenta de revisão para saber como ajustar a experiência de revisão humana:
- A [API de Trabalho](try-review-api-job.md) examina o conteúdo por meio de APIs de moderação e gera análises na ferramenta de análise. 
- A [API de Revisão](try-review-api-review.md) cria diretamente análises de imagem, texto ou vídeo para moderadores humanos sem primeiro examinar o conteúdo. 
- A [API do Fluxo de Trabalho](try-review-api-workflow.md) cria, atualiza e obtém detalhes sobre os fluxos de trabalho personalizados que sua equipe cria.

Ou então, continue com as próximas etapas para começar a usar as APIs de moderação em seu código.

## <a name="next-steps"></a>Próximas etapas

Aprenda a usar as próprias APIs de moderação no seu aplicativo.
- Implemente a moderação de imagem. Use o [console de API](try-image-api.md) ou o [C# quickstart](image-moderation-quickstart-dotnet.md) para varrer imagens e detectar conteúdo adulto e vigoroso usando tags, índices de confiança e outras informações extraídas.
- Implementar a moderação de texto. Use o [console de API](try-text-api.md) ou o [início rápido do C#](text-moderation-quickstart-dotnet.md) para examinar o conteúdo de texto para encontrar conteúdo ofensivo, classificação de textos indesejados assistida por computador (versão prévia) e dados pessoais.
- Implementar a moderação de vídeo. Siga as [guia de instruções de moderação de vídeo para C# ](video-moderation-api.md) para verificar a vídeos e detectar possível conteúdo adulto. 
