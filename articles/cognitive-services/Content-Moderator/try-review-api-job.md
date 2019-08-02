---
title: Usar trabalhos de moderação com o console da API REST-Content Moderator
titleSuffix: Azure Cognitive Services
description: Use as operações de trabalho da API de Análise para iniciar trabalhos de moderação de conteúdo de ponta a ponta para conteúdo de imagem ou texto no Content Moderator do Azure.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 03/18/2019
ms.author: sajagtap
ms.openlocfilehash: c6f3d9c1605dc97b315550d8b7e3fdf08144c1bc
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561240"
---
# <a name="define-and-use-moderation-jobs-rest"></a>Definir e usar trabalhos de moderação (REST)

Um trabalho de moderação serve como um tipo de wrapper para a funcionalidade de moderação de conteúdo, fluxos de trabalho e revisões. Este guia mostra como usar as APIs REST de trabalho para iniciar e verificar trabalhos de moderação de conteúdo. Depois de entender a estrutura das APIs, você pode facilmente portar essas chamadas para qualquer plataforma compatível com REST.

## <a name="prerequisites"></a>Pré-requisitos

- Entre ou crie uma conta no site da [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com/) de Content Moderator.
- Adicional [Definir um fluxo](./Review-Tool-User-Guide/Workflows.md) de trabalho personalizado para usar com o seu cargo; Você também pode usar o fluxo de trabalho padrão.

## <a name="create-a-job"></a>Criar um trabalho

Para criar um trabalho de moderação, vá para a página [trabalho – criar](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5) referência de API e selecione o botão para a região de chave (você pode encontrá-la na URL do ponto de extremidade na página **credenciais** da [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com/)). Isso inicia o console de API, no qual você pode facilmente construir e executar chamadas à API REST.

![Trabalho – criar seleção de região de página](images/test-drive-job-1.png)

### <a name="enter-rest-call-parameters"></a>Inserir parâmetros de chamada REST

Insira os seguintes valores para construir a chamada REST:

- **teamName**: A ID da equipe que você criou quando configurou sua conta de [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com/) (encontrada no campo **ID** na tela de credenciais da ferramenta de revisão).
- **ContentType**: Pode ser "Image", "text" ou "Video".
- **ContentId**: Uma cadeia de caracteres de identificador personalizado. essa cadeia de caracteres é passada para a API e retornada pelo retorno de chamada. É útil para associar identificadores internos ou metadados com os resultados de um trabalho de moderação.
- **Fluxo de trabalho**: O nome do fluxo de trabalho que você criou anteriormente (ou "padrão" para o fluxo de trabalho padrão).
- **CallbackEndpoint**: Adicional A URL para receber informações de retorno de chamada quando a revisão for concluída.
- **Ocp-Apim-Subscription-Key**: Sua chave de Content Moderator. Você pode encontrá-lo na guia **configurações** da [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com).

### <a name="fill-in-the-request-body"></a>Preencha o corpo da solicitação

O corpo da sua chamada REST contém um campo, **contentvalue**. Cole o conteúdo de texto bruto se você estiver moderar texto ou insira uma imagem ou uma URL de vídeo se você estiver moderar imagem/vídeo. Você pode usar a seguinte URL de imagem de exemplo:[https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg](https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg)

![Trabalho - Criar parâmetros de consulta de console, cabeçalhos e caixa do corpo da solicitação](images/job-api-console-inputs.PNG)

### <a name="submit-your-request"></a>Enviar a solicitação

Selecione **Enviar**. Se a operação for concluída com sucesso, o status `200 OK`da **resposta** será e a caixa **conteúdo da resposta** exibirá uma ID para o trabalho. Copie esta ID para usar nas etapas a seguir.

![Análise - Criar console Caixa de conteúdo de resposta exibe a ID de análise](images/test-drive-job-3.PNG)

## <a name="get-job-status"></a>Obter status do trabalho

Para obter o status e os detalhes de um trabalho em execução ou concluído, vá para a página de referência do [trabalho-obter](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c3) API e selecione o botão para a sua região (a região na qual sua chave é administrada).

![Trabalho-obter seleção de região](images/test-drive-region.png)

Insira os parâmetros de chamada REST como na seção acima. Para esta etapa, **JobID** é a cadeia de caracteres de ID exclusiva que você recebeu quando criou o trabalho. Selecione **Enviar**. Se a operação for concluída com sucesso, o status `200 OK`da **resposta** será e a caixa **conteúdo da resposta** exibirá o trabalho no formato JSON, como o seguinte:

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

### <a name="examine-the-new-reviews"></a>Examinar as novas revisões

Se o seu trabalho de conteúdo resultou na criação de uma revisão, você poderá exibi-lo na [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com). Selecione  > /**vídeo** de**texto**daimagemderevisão(dependendodoconteúdoquevocêusou/). O conteúdo deve aparecer, pronto para revisão humana. Depois que um moderador humano revisa as marcas e os dados de previsão atribuídos automaticamente e envia uma decisão de moderação final, a API de trabalhos envia todas essas informações para o ponto de extremidade do ponto de extremidade do retorno de chamada designado.

## <a name="next-steps"></a>Próximas etapas

Neste guia, você aprendeu a criar e consultar trabalhos de moderação de conteúdo usando a API REST. Em seguida, integre os trabalhos em um cenário de moderação de ponta a ponta, como o tutorial de moderação de [comércio eletrônico](./ecommerce-retail-catalog-moderation.md) .