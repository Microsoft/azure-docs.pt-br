---
title: Conceitos de análises, fluxos de trabalho e trabalhos – Content Moderator
titleSuffix: Azure Cognitive Services
description: Saiba mais sobre análises, fluxos de trabalho e trabalhos
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: sajagtap
ms.openlocfilehash: a77b93c46c9989181cf4473e8b908571a3df2f20
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68565556"
---
# <a name="content-moderation-reviews-workflows-and-jobs"></a>Análises, fluxos de trabalho e trabalhos de moderação de conteúdo

O Content Moderator combina moderação assistida por computador com recursos humanos no loop para criar um processo de moderação ideal para cenários do mundo real. Ele faz isso por meio da ferramenta de [revisão](https://contentmoderator.cognitive.microsoft.com)baseada em nuvem. Neste guia, você aprenderá sobre os principais conceitos da ferramenta de revisão: análises, fluxos de trabalho e trabalhos.

## <a name="reviews"></a>Comentários

Em uma revisão, o conteúdo é carregado para a ferramenta de revisão e aparece na guia **revisão** . A partir daqui, os usuários podem alterar as marcas aplicadas e aplicar suas próprias marcas personalizadas, conforme apropriado. Quando um usuário envia uma revisão, os resultados são enviados para um ponto de extremidade de retorno de chamada especificado e o conteúdo é removido do site.

![Examinar o site da ferramenta aberto em um navegador, na guia revisão](./Review-Tool-user-Guide/images/image-workflow-review.png)

Consulte o [Guia de ferramentas de revisão](./review-tool-user-guide/review-moderated-images.md) para começar a criar revisões ou consulte o [guia da API REST](./try-review-api-review.md) para saber como fazer isso programaticamente.

## <a name="workflows"></a>Workflows

Um fluxo de trabalho é um filtro personalizado baseado em nuvem para conteúdo. Os fluxos de trabalho podem se conectar a uma variedade de serviços para filtrar conteúdo de maneiras diferentes e, em seguida, executar a ação apropriada. Com o conector de Content Moderator, um fluxo de trabalho pode aplicar automaticamente marcas de moderação e criar revisões com conteúdo enviado.

### <a name="view-workflows"></a>Exibir fluxos de trabalho

Para exibir os fluxos de trabalho existentes, vá para a [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com/) e selecione **configurações** > fluxos de**trabalho**.

![Fluxo de trabalho padrão](images/default-workflow-listed.PNG)

Os fluxos de trabalho podem ser completamente descritos como cadeias de caracteres JSON, o que os torna acessíveis programaticamente. Se você selecionar a opção **Editar** para seu fluxo de trabalho e, em seguida, selecionar a guia **JSON** , verá uma expressão JSON semelhante à seguinte:

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

Consulte o [Guia de ferramentas de revisão](./review-tool-user-guide/workflows.md) para começar a criar e usar fluxos de trabalho ou consulte o [guia da API REST](./try-review-api-workflow.md) para saber como fazer isso programaticamente.

## <a name="jobs"></a>Tarefas (Jobs)

Um trabalho de moderação serve como um tipo de wrapper para a funcionalidade de moderação de conteúdo, fluxos de trabalho e revisões. O trabalho examina seu conteúdo usando a API de moderação de imagem Content Moderator ou a API de moderação de texto e, em seguida, verifica-o no fluxo de trabalho designado. Com base nos resultados do fluxo de trabalho, pode ou não criar uma revisão para o conteúdo da [ferramenta de revisão](./review-tool-user-guide/human-in-the-loop.md). Embora as revisões e os fluxos de trabalho possam ser criados e configurados com suas respectivas APIs, a API de trabalhos permite que você obtenha um relatório detalhado de todo o processo (que pode ser enviado para um ponto de extremidade de retorno de chamada especificado).

Consulte o [guia da API REST](./try-review-api-job.md) para começar a usar os trabalhos.

## <a name="next-steps"></a>Próximas etapas

* Teste o [Console de API de Trabalho](try-review-api-job.md) e use os exemplos de código de API REST. Se você estiver familiarizado com Visual Studio e C#, consulte também o [Início rápido do .NET de Trabalhos](moderation-jobs-quickstart-dotnet.md). 
* Para revisões, comece com o [console de API de revisão](try-review-api-review.md) e use os exemplos de código da API REST. Em seguida, consulte o [início rápido do .NET de Análises](moderation-reviews-quickstart-dotnet.md).
* Para análises de vídeos, use o [Início rápido de análise de vídeo](video-reviews-quickstart-dotnet.md)e saiba como [adicionar transcrições para a análise de vídeo](video-transcript-reviews-quickstart-dotnet.md).
