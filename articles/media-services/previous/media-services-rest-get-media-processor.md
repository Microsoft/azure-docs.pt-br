---
title: Como obter uma instância do processador de mídia usando REST | Microsoft Docs
description: Saiba como criar um componente de processador de mídia para codificar, converter o formato, criptografar ou descriptografar conteúdo de mídia dos Serviços de Mídia do Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: f9ff1997-0da6-4528-aaed-792837e5be41
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: d342cff6d322195ee88a74215f814be7d702aa5e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60761949"
---
# <a name="how-to-get-a-media-processor-instance"></a>Como obter uma instância do Processador de Mídia
> [!div class="op_single_selector"]
> * [.NET](media-services-get-media-processor.md)
> * [REST](media-services-rest-get-media-processor.md)
> 
> 

## <a name="overview"></a>Visão geral
Processadores de Mídia são um componente que manipula uma tarefa de processamento de vídeo ou áudio específica, como codificação, conversão de formato, criptografia ou descriptografia de conteúdo de mídia. Todas as tarefas enviadas aos Serviços de Mídia exigem um processador de mídia para codificar, criptografar ou converter o conteúdo de áudio ou vídeo. 

## <a name="azure-media-processors"></a>Processadores de mídia do Azure 

O tópico a seguir fornece listas de processadores de mídia:

* [Codificação de processadores de mídia](scenarios-and-availability.md#encoding-media-processors)
* [Processadores de mídia do Analytics](scenarios-and-availability.md#analytics-media-processors)

>[!NOTE]
>Ao acessar entidades nos serviços de mídia, você deve definir valores e campos de cabeçalho específicos nas suas solicitações HTTP. Para obter mais informações, consulte [Configuração para desenvolvimento da API REST dos Serviços de Mídia](media-services-rest-how-to-use.md).

## <a name="connect-to-media-services"></a>Conectar-se aos Serviços de Mídia

Para saber mais sobre como conectar-se à API do AMS, veja [Acessar a API dos Serviços de Mídia do Azure com a autenticação do Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 


## <a name="get-a-media-processor"></a>Obter um processador de mídia

A chamada REST a seguir mostra como obter uma instância do processador de mídia por nome (neste caso, **Codificador de Mídia Padrão**). 

Solicitação:

    GET https://media.windows.net/api/MediaProcessors()?$filter=Name%20eq%20'Media%20Encoder%20Standard' HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <token>
    x-ms-version: 2.17
    Host: media.windows.net

Resposta:

    . . .

    {  
       "odata.metadata":"https://media.windows.net/api/$metadata#MediaProcessors",
       "value":[  
          {  
             "Id":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "Description":"Media Encoder Standard",
             "Name":"Media Encoder Standard",
             "Sku":"",
             "Vendor":"Microsoft",
             "Version":"1.1"
          }
       ]
    }


## <a name="media-services-learning-paths"></a>Roteiros de aprendizagem dos Serviços de Mídia
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Próximas etapas
Agora que você já sabe como obter uma instância do processador de mídia, vá para o artigo [Como Codificar um Ativo](media-services-rest-get-started.md) que demonstra como usar o Media Encoder Standard para codificar um ativo.

