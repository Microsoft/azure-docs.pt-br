---
title: Subclipe um vídeo ao codificar com REST dos serviços de mídia
description: Este tópico descreve como subcortar um vídeo ao codificar com os serviços de mídia do Azure usando REST
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: how-to
ms.date: 06/10/2019
ms.author: inhenkel
ms.openlocfilehash: 6c287e168289082b2bd717a2706dd011c7115691
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98955659"
---
# <a name="subclip-a-video-when-encoding-with-media-services---rest"></a>Subclipe um vídeo ao codificar com os serviços de mídia-REST

Você pode cortar ou subcortar um vídeo ao codificá-lo usando um [trabalho](/rest/api/media/jobs). Essa funcionalidade funciona com qualquer [Transformação](/rest/api/media/transforms) criada usando as predefinições [BuiltInStandardEncoderPreset](/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) ou [StandardEncoderPreset](/rest/api/media/transforms/createorupdate#standardencoderpreset). 

O exemplo de REST neste tópico cria um trabalho que corta um vídeo à medida que envia um trabalho de codificação. 

## <a name="prerequisites"></a>Pré-requisitos

Para concluir as etapas descritas neste tópico, você precisa:

- [Crie uma conta dos serviços de mídia do Azure](./create-account-howto.md).
- [Configurar o Postman para chamadas à API REST de Serviços de Mídia do Azure](media-rest-apis-with-postman.md).
    
    Certifique-se de seguir a última etapa no tópico [Obter token do Azure AD](media-rest-apis-with-postman.md#get-azure-ad-token). 
- Crie uma transformação e um ativo de saída. Você pode ver como criar uma transformação e um ativo de saída no tutorial [codificar um arquivo remoto baseado em URL e transmitir o vídeo REST](stream-files-tutorial-with-rest.md) .
- Examine o tópico [conceito de codificação](encoding-concept.md) .

## <a name="create-a-subclipping-job"></a>Criar um trabalho de subcorte

1. Na coleção de postmaster que você baixou, selecione **transformações e trabalhos**  ->  **criar trabalho com o subcorte**.
    
    A solicitação **Put** é parecida com esta:
    
    ```
    https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName/jobs/:jobName?api-version={{api-version}}
    ```
1. Atualize o valor da variável de ambiente "transformName" com o nome da transformação. 
1. Selecione a guia **corpo** e atualize o "myOutputAsset" com o nome do ativo de saída.

    ```json
    {
      "properties": {
        "description": "A Job with transform cb9599fb-03b3-40eb-a2ff-7ea909f53735 and single clip.",
       
        "input": {
          "@odata.type": "#Microsoft.Media.JobInputHttp",
          "baseUri": "https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/",
          "files": [
            "Ignite-short.mp4"
          ],
          "start": {
            "@odata.type": "#Microsoft.Media.AbsoluteClipTime",
            "time": "PT10S"
          },
          "end": {
            "@odata.type": "#Microsoft.Media.AbsoluteClipTime",
            "time": "PT40S"
          }
        },
      
        "outputs": [
          {
            "@odata.type": "#Microsoft.Media.JobOutputAsset",
            "assetName": "myOutputAsset"
          }
        ],
        "priority": "Normal"
      }
    }
    ```
1. Pressione **Enviar**.

    Você verá a **resposta** com as informações sobre o trabalho que foi criado e enviado e o status do trabalho. 

## <a name="next-steps"></a>Próximas etapas

[Como codificar com uma transformação personalizada](custom-preset-rest-howto.md) 
