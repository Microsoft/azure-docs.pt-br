---
title: Criar as revisões de moderação com o console de API REST - Content Moderator
titlesuffix: Azure Cognitive Services
description: Use as APIs de revisão do moderador do conteúdo do Azure para criar as análises de imagem ou texto de moderação humanas.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 03/18/2019
ms.author: sajagtap
ms.openlocfilehash: 254269ccedc92b9dfc164cc4665a8a8513682773
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60607520"
---
# <a name="create-human-reviews-rest"></a>Criar análises humanas (REST)

[Revisões](./review-api.md#reviews) armazenar e exibir o conteúdo para moderadores humanos avaliar. Quando um usuário conclui uma revisão, os resultados são enviados para um ponto de extremidade de retorno de chamada especificados. Neste guia, você aprenderá como configurar revisões usando as APIs de REST de análise por meio do console de API. Depois de compreender a estrutura das APIs, você poderá transportar facilmente essas chamadas para qualquer plataforma compatível com REST.

## <a name="prerequisites"></a>Pré-requisitos

- Entrar ou criar uma conta no Content Moderator [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com/) site.

## <a name="create-a-review"></a>Criar uma revisão

Para criar uma revisão, vá para o **[examine - criar](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4)** API página de referência e selecione o botão para a sua região de chave (você pode encontrá-lo na URL do ponto de extremidade no **credenciais** página dos [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com/)). Isso inicia o console de API, onde você pode facilmente construir e executar chamadas à API REST.

![Examine - seleção de região de Get](images/test-drive-region.png)

### <a name="enter-rest-call-parameters"></a>Insira os parâmetros da chamada REST

Insira valores para **teamName**, e **Ocp-Apim-Subscription-Key**:

- **teamName**: A ID da equipe que você criou quando você configurar seu [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com/) conta (encontrada na **Id** campo na tela de credenciais da sua ferramenta de revisão).
- **Ocp-Apim-Subscription-Key**: Sua chave de Content Moderator. Você pode encontrá-lo na **as configurações** guia da [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com).

### <a name="enter-a-review-definition"></a>Insira uma definição de revisão

Editar o **corpo da solicitação** caixa para inserir a solicitação JSON com os seguintes campos:

- **Metadados**: Pares chave-valor personalizados a ser retornado ao seu ponto de extremidade de retorno de chamada. Se a chave é um código curto definido na [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com), ele aparece como uma marca.
- **Conteúdo**: No caso de conteúdo de imagem e vídeo, isso é uma cadeia de caracteres de URL que aponta para o conteúdo. Para o conteúdo de texto, essa é a cadeia de texto real.
- **ContentId**: Uma cadeia de caracteres de identificador personalizado. essa cadeia de caracteres é passada para a API e retornada pelo retorno de chamada. É útil para associar identificadores internos ou metadados com os resultados de um trabalho de moderação.
- **CallbackEndpoint**: (Opcional) A URL para receber informações de retorno de chamada quando a análise for concluída.

O corpo da solicitação de padrão mostra exemplos de diferentes tipos de revisões, que você pode criar:

```json
[Image]
[
  {
    "Metadata": [
      {
        "Key": "string",
        "Value": "string"
      }
    ],
    "Type": "Image",
    "Content": "<Content Url>",
    "ContentId": "<Your identifier for this content>",
    "CallbackEndpoint": "<Url where you would receive callbacks>"
  }
]
[Text]
[
  {
    "Metadata": [
      {
        "Key": "string",
        "Value": "string"
      }
    ],
    "Type": "Text",
    "Content": "<Your Text Content>",
    "ContentId": "<Your identifier for this content>",
    "CallbackEndpoint": "<Url where you would receive callbacks>"
  }
]
[Video]
[
  {
    "VideoFrames":[
      {
          "Id": "<Frame Id>",
          "Timestamp": "<Frame Timestamp",
          "FrameImage":"<Frame Image URL",
          "Metadata": [
            {
              "Key": "<Key>",
              "Value": "<Value"
            }
          ],
          "ReviewerResultTags": [
          ]
    ], 
    "Metadata": [
      {
        "Key": "string",
        "Value": "string"
      },
      //For encrypted Videos
        {
          "Key": "protectedType",
          "Value": "AES or FairPlay or Widevine or Playready"
        },
        {
          "Key": "authenticationToken",
          "Value": "your viewtoken(In case of Video Indexer AES encryption type, this value is viewtoken from breakdown json)"
        },
      //For FairPlay encrypted type video include certificateUrl as well
        {
          "Key": "certificateUrl",
          "Value": "your certificate url"
        }
    ],
    "Type": "Video",
    "Content": "<Stream Url>",
    "ContentId": "<Your identifier for this content>",
    "CallbackEndpoint": "<Url where you would receive callbacks>",
    [Optional]
    "Timescale": "<Timescale of the video>
  }
]
```

### <a name="submit-your-request"></a>Enviar a solicitação
  
Selecione **Enviar**. Se a operação for bem-sucedida, o **status de resposta** é `200 OK`e o **conteúdo da resposta** caixa exibe uma ID para a revisão. Copie esta ID para usar nas etapas a seguir.

![Análise - Criar console Caixa de conteúdo de resposta exibe a ID de análise](images/test-drive-review-2.PNG)

### <a name="examine-the-new-review"></a>Examine a nova revisão

No [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com), selecione **revisão** > **imagem**/**texto** / **Vídeo** (dependendo de qual conteúdo usado). O conteúdo que você carregou deve aparecer, pronta para revisão humana.

![Imagem da ferramenta de análise de uma bola de futebol](images/test-drive-review-5.PNG)

## <a name="get-review-details"></a>Obter detalhes de revisão

Para recuperar detalhes sobre uma revisão existente, vá para o [examine - obter](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2) página de referência de API e selecione o botão para a sua região (a região na qual a chave é administrada).

![Seleção de região de Fluxo de trabalho - Get](images/test-drive-region.png)

Insira os parâmetros da chamada REST como na seção acima. Para esta etapa **reviewId** é a cadeia de caracteres de identificação exclusiva que você recebeu quando você criou a revisão.

![Análise - Criar console Obter resultados](images/test-drive-review-3.PNG)
  
Selecione **Enviar**. Se a operação for bem-sucedida, o **status de resposta** é `200 OK`e o **conteúdo da resposta** caixa exibe os detalhes de revisão no formato JSON, semelhante ao seguinte:

```json
{  
  "reviewId":"201712i46950138c61a4740b118a43cac33f434",
  "subTeam":"public",
  "status":"Complete",
  "reviewerResultTags":[  
    {  
      "key":"a",
      "value":"False"
    },
    {  
      "key":"r",
      "value":"True"
    },
    {  
      "key":"sc",
      "value":"True"
    }
  ],
  "createdBy":"<teamname>",
  "metadata":[  
    {  
      "key":"sc",
      "value":"true"
    }
  ],
  "type":"Image",
  "content":"https://reviewcontentprod.blob.core.windows.net/<teamname>/IMG_201712i46950138c61a4740b118a43cac33f434",
  "contentId":"0",
  "callbackEndpoint":"<callbackUrl>"
}
```

Observe os seguintes campos na resposta:

- **status**
- **reviewerResultTags**: Isso será exibida se todas as marcas foram adicionadas manualmente pela equipe de análise humana (mostrado o **createdBy** campo).
- **metadados**: Isso mostra as marcas que foram adicionadas inicialmente na revisão, antes das alterações da equipe feita de análise humana.

## <a name="next-steps"></a>Próximos passos

Neste guia, você aprendeu a criar as revisões de moderação de conteúdo usando a API REST. Em seguida, integre revisões em um cenário de moderação de ponta a ponta, como o [moderação de comércio eletrônico](./ecommerce-retail-catalog-moderation.md) tutorial.