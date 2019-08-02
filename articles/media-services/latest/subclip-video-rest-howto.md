---
title: Subclipe um vídeo durante a codificação com a API de REST de serviços de mídia do Azure
description: Este tópico descreve como subclipe um vídeo quando estiver codificando com os serviços de mídia do Azure usando o REST
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 06/10/2019
ms.author: juliako
ms.openlocfilehash: df8c8a4040b4aae4379b4bfe0e9a16337588dd1b
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67305113"
---
# <a name="subclip-a-video-when-encoding-with-media-services---rest"></a>Um vídeo quando estiver codificando com os serviços de mídia - REST do subclipe

Você pode cortar ou subclipe um vídeo quando estiver codificando-o usando um [trabalho](https://docs.microsoft.com/rest/api/media/jobs). Essa funcionalidade funciona com qualquer [transformar](https://docs.microsoft.com/rest/api/media/transforms) que é criado usando o [BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) predefinições, ou o [StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) predefinições. 

O exemplo REST neste tópico cria um trabalho que corta um vídeo, ele envia um trabalho de codificação. 

## <a name="prerequisites"></a>Pré-requisitos

Para concluir as etapas descritas neste tópico, você precisa:

- [Criar uma conta de serviços de mídia do Azure](create-account-cli-how-to.md).
- [Configurar o Postman para chamadas à API REST de Serviços de Mídia do Azure](media-rest-apis-with-postman.md).
    
    Certifique-se de seguir a última etapa no tópico [Obter token do Azure AD](media-rest-apis-with-postman.md#get-azure-ad-token). 
- Criar uma transformação e uma saída de ativos. Você pode ver como criar uma transformação e uma saída de ativos na [um arquivo remoto com base na URL de codificar e transmitir vídeo - REST](stream-files-tutorial-with-rest.md) tutorial.
- Examine os [conceito de codificação](encoding-concept.md) tópico.

## <a name="create-a-subclipping-job"></a>Criar um trabalho de subclipping

1. Na coleção de Postman que você baixou, selecione **transforma e trabalhos** -> **criar o trabalho com o recorte Sub**.
    
    O **colocar** solicitação tem esta aparência:
    
    ```
    https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName/jobs/:jobName?api-version={{api-version}}
    ```
1. Atualize o valor da variável de ambiente "transformName" com seu nome de transformação. 
1. Selecione o **corpo** guia e atualizar "myOutputAsset" com a saída do nome do ativo.

    ```
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

    Você vê o **resposta** com as informações sobre um trabalho que foi criada e enviada e o status do trabalho. 

## <a name="next-steps"></a>Próximas etapas

[Como codificar com uma transformação personalizada](custom-preset-rest-howto.md) 
