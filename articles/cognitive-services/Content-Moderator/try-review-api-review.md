---
title: Criar revisões de moderação com o console da API REST-Content Moderator
titleSuffix: Azure Cognitive Services
description: Use as APIs de revisão de Content Moderator do Azure para criar revisões de imagem ou de texto para moderação humana.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 03/18/2019
ms.author: sajagtap
ms.openlocfilehash: 581ab488337cfecae3f5dd97610c7f92c75af8b5
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68564342"
---
# <a name="create-human-reviews-rest"></a>Criar análises humanas (REST)

[Revisa](./review-api.md#reviews) o armazenamento e exibe o conteúdo de moderadores humanos para avaliar. Quando um usuário conclui uma revisão, os resultados são enviados para um ponto de extremidade de retorno de chamada especificado. Neste guia, você aprenderá a configurar as revisões usando as APIs REST de revisão por meio do console de API. Depois de entender a estrutura das APIs, você pode facilmente portar essas chamadas para qualquer plataforma compatível com REST.

## <a name="prerequisites"></a>Pré-requisitos

- Entre ou crie uma conta no site da [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com/) de Content Moderator.

## <a name="create-a-review"></a>Criar uma revisão

Para criar uma revisão, vá para a página **[análise-criar](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4)** referência de API e selecione o botão para a região de chave (você pode encontrá-la na URL do ponto de extremidade na página **credenciais** da [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com/)). Isso inicia o console de API, no qual você pode facilmente construir e executar chamadas à API REST.

![Examinar – obter seleção de região](images/test-drive-region.png)

### <a name="enter-rest-call-parameters"></a>Inserir parâmetros de chamada REST

Insira valores para **teamname**e **OCP-APIM-Subscription-Key**:

- **teamName**: A ID da equipe que você criou quando configurou sua conta de [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com/) (encontrada no campo **ID** na tela de credenciais da ferramenta de revisão).
- **Ocp-Apim-Subscription-Key**: Sua chave de Content Moderator. Você pode encontrá-lo na guia **configurações** da [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com).

### <a name="enter-a-review-definition"></a>Insira uma definição de revisão

Edite a caixa **corpo da solicitação** para inserir a solicitação JSON com os seguintes campos:

- **Metadados**: Pares chave-valor personalizados a serem retornados para o ponto de extremidade de retorno de chamada. Se a chave for um código curto definido na [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com), ela aparecerá como uma marca.
- **Conteúdo**: No caso de conteúdo de imagem e vídeo, essa é uma cadeia de caracteres de URL apontando para o conteúdo. Para conteúdo de texto, esta é a cadeia de texto real.
- **ContentId**: Uma cadeia de caracteres de identificador personalizado. essa cadeia de caracteres é passada para a API e retornada pelo retorno de chamada. É útil para associar identificadores internos ou metadados com os resultados de um trabalho de moderação.
- **CallbackEndpoint**: Adicional A URL para receber informações de retorno de chamada quando a revisão for concluída.

O corpo da solicitação padrão mostra exemplos dos diferentes tipos de revisões que você pode criar:

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
  
Selecione **Enviar**. Se a operação for concluída com sucesso, o status `200 OK`da **resposta** será e a caixa **conteúdo da resposta** exibirá uma ID para a revisão. Copie esta ID para usar nas etapas a seguir.

![Análise - Criar console Caixa de conteúdo de resposta exibe a ID de análise](images/test-drive-review-2.PNG)

### <a name="examine-the-new-review"></a>Examinar a nova revisão

Na [ferramenta revisão](https://contentmoderator.cognitive.microsoft.com), selecione**vídeo** de > /**texto**/da imagem de **revisão**(dependendo de qual conteúdo você usou). O conteúdo que você carregou deve aparecer, pronto para revisão humana.

![Imagem da ferramenta de análise de uma bola de futebol](images/test-drive-review-5.PNG)

## <a name="get-review-details"></a>Obter detalhes da revisão

Para recuperar detalhes sobre uma revisão existente, acesse a página de referência de API de [revisão](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2) e selecione o botão da sua região (a região em que sua chave é administrada).

![Seleção de região de Fluxo de trabalho - Get](images/test-drive-region.png)

Insira os parâmetros de chamada REST como na seção acima. Para esta etapa,  REVIEWID é a cadeia de caracteres de ID exclusiva que você recebeu quando criou a revisão.

![Análise - Criar console Obter resultados](images/test-drive-review-3.PNG)
  
Selecione **Enviar**. Se a operação for concluída com sucesso, o status `200 OK`da **resposta** será e a caixa **conteúdo da resposta** exibirá os detalhes da revisão no formato JSON, como o seguinte:

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

Anote os seguintes campos na resposta:

- **status**
- **reviewerResultTags**: Isso será exibido se todas as marcas tiverem sido adicionadas manualmente pela equipe de análise humana (mostrada no campo **createdBy** ).
- **metadados**: Isso mostra as marcas que foram adicionadas inicialmente na revisão, antes que a equipe de análise humana tenha feito alterações.

## <a name="next-steps"></a>Próximas etapas

Neste guia, você aprendeu a criar revisões de moderação de conteúdo usando a API REST. Em seguida, integre as revisões em um cenário de moderação de ponta a ponta, como o tutorial de moderação de [comércio eletrônico](./ecommerce-retail-catalog-moderation.md) .