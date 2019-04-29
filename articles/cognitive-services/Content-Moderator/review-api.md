---
title: Conceitos de trabalhos - moderador de conteúdo e revisões, fluxos de trabalho,
titlesuffix: Azure Cognitive Services
description: Saiba mais sobre as revisões, fluxos de trabalho e trabalhos
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: sajagtap
ms.openlocfilehash: c1d4ef640e2ae072dacba7a665b6689e3224c55c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60607290"
---
# <a name="content-moderation-reviews-workflows-and-jobs"></a>Revisões de moderação de conteúdo, os fluxos de trabalho e trabalhos

O Content Moderator combina a moderação auxiliada por computador com recursos humanos no loop para criar um processo de moderação ideal para cenários do mundo real. Ele faz isso por meio de baseado em nuvem [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com). Neste guia, você aprenderá sobre os conceitos básicos da ferramenta de revisão: revisões, fluxos de trabalho e trabalhos.

## <a name="reviews"></a>Análises

Em uma revisão, o conteúdo é carregado para a ferramenta de revisão e aparece sob o **examine** guia. A partir daqui, os usuários podem alterar as marcas aplicadas e aplicar suas próprias marcas personalizadas conforme apropriado. Quando um usuário envia uma revisão, os resultados são enviados para um ponto de extremidade de retorno de chamada especificado e o conteúdo é removido do site.

![Abra o site da ferramenta de revisão em um navegador, na guia revisão](./Review-Tool-user-Guide/images/image-workflow-review.png)

Consulte a [guia da ferramenta de revisão](./review-tool-user-guide/review-moderated-images.md) para começar a criar análises, ou consulte o [guia da API REST](./try-review-api-review.md) para aprender a fazer isso programaticamente.

## <a name="workflows"></a>Fluxos de trabalho

Um fluxo de trabalho é um filtro personalizado baseado em nuvem para o conteúdo. Fluxos de trabalho podem se conectar a uma variedade de serviços para filtrar o conteúdo de maneiras diferentes e, em seguida, execute a ação apropriada. Com o conector do Content Moderator, um fluxo de trabalho pode aplicar marcas de moderação e automaticamente criar revisões com conteúdo enviado.

### <a name="view-workflows"></a>Exibir fluxos de trabalho

Para exibir seus fluxos de trabalho existentes, vá para o [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com/) e selecione **configurações** > **fluxos de trabalho**.

![Fluxo de trabalho padrão](images/default-workflow-listed.PNG)

Fluxos de trabalho podem ser completamente descritos como cadeias de caracteres JSON, que torna acessível por meio de programação. Se você selecionar o **edite** opção para seu fluxo de trabalho e, em seguida, selecione o **JSON** guia, você verá uma expressão JSON semelhante ao seguinte:

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

Consulte a [guia da ferramenta de revisão](./review-tool-user-guide/workflows.md) para começar a criar e usar fluxos de trabalho, ou consulte o [guia da API REST](./try-review-api-workflow.md) para aprender a fazer isso programaticamente.

## <a name="jobs"></a>Trabalhos

Um trabalho de moderação serve como um tipo de wrapper para a funcionalidade de moderação de conteúdo, os fluxos de trabalho e revisões. O trabalho examina o conteúdo usando a moderação de imagem o Content Moderator, API ou a API de moderação de texto e, em seguida, compara-o fluxo de trabalho designado. De acordo com os resultados do fluxo de trabalho, ele pode ou não pode criar uma revisão para o conteúdo a [ferramenta de revisão](./review-tool-user-guide/human-in-the-loop.md). Enquanto as revisões e fluxos de trabalho podem ser criados e configurados com seus respectivos APIs, o trabalho de API permite obter um relatório detalhado de todo o processo (que pode ser enviado para um ponto de extremidade de retorno de chamada especificados).

Consulte a [guia da API REST](./try-review-api-job.md) para começar a usar os trabalhos.

## <a name="next-steps"></a>Próximos passos

* Teste o [Console de API de Trabalho](try-review-api-job.md) e use os exemplos de código de API REST. Se você estiver familiarizado com Visual Studio e C#, consulte também o [Início rápido do .NET de Trabalhos](moderation-jobs-quickstart-dotnet.md). 
* Para revisões, comece com o [console de API de revisão](try-review-api-review.md) e use os exemplos de código da API REST. Em seguida, consulte o [início rápido do .NET de Análises](moderation-reviews-quickstart-dotnet.md).
* Para análises de vídeos, use o [Início rápido de análise de vídeo](video-reviews-quickstart-dotnet.md)e saiba como [adicionar transcrições para a análise de vídeo](video-transcript-reviews-quickstart-dotnet.md).
