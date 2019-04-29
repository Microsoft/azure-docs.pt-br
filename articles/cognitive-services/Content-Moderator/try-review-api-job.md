---
title: Trabalhos de moderação de uso com o console de API REST - Content Moderator
titlesuffix: Azure Cognitive Services
description: Use as operações de trabalho da API de Análise para iniciar trabalhos de moderação de conteúdo de ponta a ponta para conteúdo de imagem ou texto no Content Moderator do Azure.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 03/18/2019
ms.author: sajagtap
ms.openlocfilehash: 7827cee2af2dfc0c1fddc407c1d146dc9a66c514
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60607631"
---
# <a name="define-and-use-moderation-jobs-rest"></a>Definir e usar trabalhos de moderação (REST)

Um trabalho de moderação serve como um tipo de wrapper para a funcionalidade de moderação de conteúdo, os fluxos de trabalho e revisões. Este guia mostra como usar o APIs REST de trabalho para iniciar e verifique se os trabalhos de moderação de conteúdo. Depois de compreender a estrutura das APIs, você poderá transportar facilmente essas chamadas para qualquer plataforma compatível com REST.

## <a name="prerequisites"></a>Pré-requisitos

- Entrar ou criar uma conta no Content Moderator [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com/) site.
- (Opcional) [Definir um fluxo de trabalho personalizado](./Review-Tool-User-Guide/Workflows.md) para usar com o seu trabalho; você também pode usar o fluxo de trabalho padrão.

## <a name="create-a-job"></a>Criar um trabalho

Para criar um trabalho de moderação, vá para o [do trabalho – criar](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5) API de referência a página e selecione o botão para a sua região de chave (você pode encontrá-lo na URL do ponto de extremidade na **credenciais** página do [revisão ferramenta](https://contentmoderator.cognitive.microsoft.com/)). Isso inicia o console de API, onde você pode facilmente construir e executar chamadas à API REST.

![Trabalho – Criar página seleção de região](images/test-drive-job-1.png)

### <a name="enter-rest-call-parameters"></a>Insira os parâmetros da chamada REST

Insira os seguintes valores para construir a chamada REST:

- **teamName**: A ID da equipe que você criou quando você configurar seu [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com/) conta (encontrada na **Id** campo na tela de credenciais da sua ferramenta de revisão).
- **ContentType**: Isso pode ser "Image", "Text" ou "Vídeo".
- **ContentId**: Uma cadeia de caracteres de identificador personalizado. essa cadeia de caracteres é passada para a API e retornada pelo retorno de chamada. É útil para associar identificadores internos ou metadados com os resultados de um trabalho de moderação.
- **WorkflowName**: O nome do fluxo de trabalho que você criou anteriormente (ou "padrão" para o fluxo de trabalho padrão).
- **CallbackEndpoint**: (Opcional) A URL para receber informações de retorno de chamada quando a análise for concluída.
- **Ocp-Apim-Subscription-Key**: Sua chave de Content Moderator. Você pode encontrá-lo na **as configurações** guia da [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com).

### <a name="fill-in-the-request-body"></a>Preencha o corpo da solicitação

O corpo da sua chamada REST contém um campo, **ContentValue**. Cole o conteúdo de texto bruto se são moderar o texto ou inserir uma imagem ou URL do vídeo, se você estiver moderar imagem/vídeo. Você pode usar a URL de imagem de exemplo a seguir: [https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg](https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg)

![Trabalho - Criar parâmetros de consulta de console, cabeçalhos e caixa do corpo da solicitação](images/job-api-console-inputs.PNG)

### <a name="submit-your-request"></a>Enviar a solicitação

Selecione **Enviar**. Se a operação for bem-sucedida, o **status de resposta** é `200 OK`e o **conteúdo da resposta** caixa exibe uma ID para o trabalho. Copie esta ID para usar nas etapas a seguir.

![Análise - Criar console Caixa de conteúdo de resposta exibe a ID de análise](images/test-drive-job-3.PNG)

## <a name="get-job-status"></a>Obter status do trabalho

Para obter o status e detalhes de um trabalho em execução ou concluído, vá para o [do trabalho – obter](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c3) página de referência de API e selecione o botão para a sua região (a região na qual a chave é administrada).

![Trabalho - seleção de região de Get](images/test-drive-region.png)

Insira os parâmetros da chamada REST como na seção acima. Para esta etapa **JobId** é a cadeia de caracteres de identificação exclusiva que você recebeu quando criou o trabalho. Selecione **Enviar**. Se a operação for bem-sucedida, o **status de resposta** é `200 OK`e o **conteúdo da resposta** caixa exibe o trabalho no formato JSON, semelhante ao seguinte:

```json
{  
  "Id":"2018014caceddebfe9446fab29056fd8d31ffe",
  "TeamName":"some team name",
  "Status":"Complete",
  "WorkflowId":"OCR",
  "Type":"Image",
  "CallBackEndpoint":"",
  "ReviewId":"201801i28fc0f7cbf424447846e509af853ea54",
  "ResultMetaData":[  
    {  
      "Key":"hasText",
      "Value":"True"
    },
    {  
      "Key":"ocrText",
      "Value":"IF WE DID \r\nALL \r\nTHE THINGS \r\nWE ARE \r\nCAPABLE \r\nOF DOING, \r\nWE WOULD \r\nLITERALLY \r\nASTOUND \r\nOURSELVE \r\n"
    }
  ],
  "JobExecutionReport":[  
    {  
      "Ts":"2018-01-07T00:38:29.3238715",
      "Msg":"Posted results to the Callbackendpoint: https://requestb.in/vxke1mvx"
    },
    {  
      "Ts":"2018-01-07T00:38:29.2928416",
      "Msg":"Job marked completed and job content has been removed"
    },
    {  
      "Ts":"2018-01-07T00:38:29.0856472",
      "Msg":"Execution Complete"
    },
    {  
      "Ts":"2018-01-07T00:38:26.7714671",
      "Msg":"Successfully got hasText response from Moderator"
    },
    {  
      "Ts":"2018-01-07T00:38:26.4181346",
      "Msg":"Getting hasText from Moderator"
    },
    {  
      "Ts":"2018-01-07T00:38:25.5122828",
      "Msg":"Starting Execution - Try 1"
    }
  ]
}
```

![Trabalho – obter resposta de chamada REST](images/test-drive-job-5.png)

### <a name="examine-the-new-reviews"></a>Examinar o novo review(s)

Se o trabalho de conteúdo resultou na criação de uma revisão, você pode exibi-lo na [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com). Selecione **revisão** > **imagem**/**texto**/**vídeo** (dependendo de qual conteúdo é usado). O conteúdo deve aparecer, prontos para revisão humana. Depois que um humano moderador revisa as marcas atribuída automaticamente e os dados de previsão e envia uma decisão final moderação, a API de trabalhos envia todas essas informações para o ponto de extremidade do ponto de extremidade de retorno de chamada designado.

## <a name="next-steps"></a>Próximas etapas

Neste guia, você aprendeu como criar e consultar usando a API REST de trabalhos de moderação de conteúdo. Em seguida, integrar trabalhos em um cenário de moderação de ponta a ponta, como o [moderação de comércio eletrônico](./ecommerce-retail-catalog-moderation.md) tutorial.