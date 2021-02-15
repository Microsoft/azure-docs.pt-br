---
title: Conceitos de análises, fluxos de trabalho e trabalhos – Content Moderator
titleSuffix: Azure Cognitive Services
description: Neste artigo, você aprenderá sobre os principais conceitos da ferramenta de revisão; análises, fluxos de trabalho e trabalhos.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: pafarley
ms.openlocfilehash: 43c39c40af6e02861211a8666fefa57c34072f32
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/09/2020
ms.locfileid: "96905189"
---
# <a name="content-moderation-reviews-workflows-and-jobs"></a>Análises, fluxos de trabalho e trabalhos de moderação de conteúdo

O Content Moderator combina moderação assistida por computador com recursos humanos no loop para criar um processo de moderação ideal para cenários do mundo real. Ele faz isso por meio da ferramenta de [revisão](https://contentmoderator.cognitive.microsoft.com)baseada em nuvem. Neste guia, você aprenderá sobre os principais conceitos da ferramenta de revisão: análises, fluxos de trabalho e trabalhos.

## <a name="reviews"></a>Análises

Em uma revisão, o conteúdo é carregado para a ferramenta de revisão. Você pode exibi-lo clicando em seu tipo de conteúdo na guia **revisão** no painel. Na tela de revisão, você pode alterar as marcas aplicadas e aplicar suas próprias marcas personalizadas conforme apropriado. Quando você envia uma revisão, os resultados são enviados para um ponto de extremidade de retorno de chamada especificado e o conteúdo é removido do site.

> [!div class="mx-imgBorder"]
> ![O menu suspenso revisão é realçado. Ele mostra esses tipos de conteúdo: imagem, texto e vídeo.](./Review-Tool-user-Guide/images/review-tab.png)

### <a name="manage-reviews"></a>Gerenciar revisões

No painel, navegue até **Administração**  ->  **gerenciar revisões** para exibir a tela administrador. Aqui, você pode ver uma lista de todas as revisões (pendentes e concluídas).

O botão de **ações** de três pontos em cada revisão permite que você vá para a tela de revisão ou Inspecione o histórico dessa revisão.

> [!div class="mx-imgBorder"]
> ![Examinar o site da ferramenta na tela de revisão](./Review-Tool-user-Guide/images/manage-reviews.png)

Use a barra de ferramentas de **pesquisa** para classificar as revisões por uma variedade de categorias, como estado de revisão, marcas, tipo de conteúdo, subequipes, usuários atribuídos e data de criação/modificação.

> [!div class="mx-imgBorder"]
> ![A barra de ferramentas de pesquisa é mostrada. Ele tem várias caixas de combinação para inserir critérios de pesquisa, como estado de revisão e marcas.](./Review-Tool-user-Guide/images/review-search.png)

Consulte o [Guia de ferramentas de revisão](./review-tool-user-guide/review-moderated-images.md) para começar a criar revisões ou consulte o [Guia do console de API](./try-review-api-review.md) para saber como fazer isso programaticamente.

## <a name="workflows"></a>Fluxos de Trabalho

Um fluxo de trabalho é um filtro personalizado baseado em nuvem para conteúdo. Os fluxos de trabalho podem se conectar a uma variedade de serviços para filtrar conteúdo de maneiras diferentes e, em seguida, executar a ação apropriada. Com o conector de Content Moderator, um fluxo de trabalho pode aplicar automaticamente marcas de moderação e criar revisões com conteúdo enviado.

### <a name="view-workflows"></a>Exibir fluxos de trabalho

Para exibir os fluxos de trabalho existentes, vá para a [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com/) e selecione fluxos de trabalho de **Administração**  >  **Workflows**.

> [!div class="mx-imgBorder"]
> ![Fluxo de trabalho padrão](images/default-workflow-list.png)

Os fluxos de trabalho são definidos como cadeias de caracteres JSON, o que os torna acessíveis programaticamente. Se você selecionar a opção **Editar** para seu fluxo de trabalho e, em seguida, selecionar a guia **JSON** , verá uma expressão JSON semelhante à seguinte:

```json
{
    "Type": "Logic",
    "If": {
        "ConnectorName": "moderator",
        "OutputName": "isAdult",
        "Operator": "eq",
        "Value": "true",
        "Type": "Condition"
        },
    "Then": {
    "Perform": [
    {
        "Name": "createreview",
        "CallbackEndpoint": null,
        "Tags": []
    }
    ],
    "Type": "Actions"
    }
}
```

Consulte o [Guia de ferramentas de revisão](./review-tool-user-guide/workflows.md) para começar a criar e usar fluxos de trabalho ou consulte o [Guia do console de API](./try-review-api-workflow.md) para saber como fazer isso programaticamente.

## <a name="jobs"></a>Trabalhos

Um trabalho de moderação serve como um tipo de wrapper para a funcionalidade de moderação de conteúdo, fluxos de trabalho e revisões. O trabalho examina seu conteúdo usando a API de moderação de imagem Content Moderator ou a API de moderação de texto e, em seguida, verifica-o no fluxo de trabalho designado. Com base nos resultados do fluxo de trabalho, pode ou não criar uma revisão para o conteúdo da [ferramenta de revisão](./review-tool-user-guide/human-in-the-loop.md). Embora as revisões e os fluxos de trabalho possam ser criados e configurados com suas respectivas APIs, a API de trabalhos permite que você obtenha um relatório detalhado de todo o processo (que pode ser enviado para um ponto de extremidade de retorno de chamada especificado).

Consulte o [Guia do console de API](./try-review-api-job.md) para começar a usar os trabalhos.

## <a name="next-steps"></a>Próximas etapas

* Teste o [Console de API de Trabalho](try-review-api-job.md) e use os exemplos de código de API REST. Se você estiver familiarizado com Visual Studio e C#, consulte também o [Início rápido do .NET de Trabalhos](moderation-jobs-quickstart-dotnet.md). 
* Para revisões, comece com o [console de API de revisão](try-review-api-review.md) e use os exemplos de código da API REST. Em seguida, consulte a seção de revisões do [início rápido do .net](./client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp).
* Para análises de vídeos, use o [Início rápido de análise de vídeo](video-reviews-quickstart-dotnet.md)e saiba como [adicionar transcrições para a análise de vídeo](video-transcript-reviews-quickstart-dotnet.md).