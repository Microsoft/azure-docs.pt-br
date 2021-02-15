---
title: Políticas de streaming nos serviços de mídia do Azure
description: Este artigo fornece uma explicação sobre o que são as políticas de streaming e como são usados pelos Serviços de Mídia do Azure.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: inhenkel
ms.openlocfilehash: 2873486b95f0bb4a9a27e2e050cc6f6d5473a44e
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98898165"
---
# <a name="streaming-policies"></a>Políticas de Streaming

Nos Serviços de Mídia do Azure v3, as [Políticas de Streaming](/rest/api/media/streamingpolicies) permitem definir protocolos de streaming e opções de criptografia para seus [Localizadores de Streaming](streaming-locators-concept.md). Os serviços de mídia v3 fornecem algumas políticas de streaming predefinidas para que você possa usá-las diretamente para avaliação ou produção. 

As políticas de streaming predefinidas atualmente disponíveis:<br/>
* ' Predefined_DownloadOnly '
* ' Predefined_ClearStreamingOnly '
* ' Predefined_DownloadAndClearStreaming '
* ' Predefined_ClearKey '
* ' Predefined_MultiDrmCencStreaming ' 
* ' Predefined_MultiDrmStreaming '

A "árvore de decisão" a seguir ajuda você a escolher uma política de streaming predefinida para seu cenário.

> [!IMPORTANT]
> * As propriedades de **Políticas de Streaming** que são do tipo Datetime estão sempre no formato UTC.
> * Você deve criar um conjunto limitado de políticas para sua conta de serviço de mídia e reutilizá-los para os Localizadores de Streaming sempre que as mesmas opções forem necessárias. Para obter mais informações, consulte [cotas e limites](limits-quotas-constraints.md).

## <a name="decision-tree"></a>Árvore de decisão

Clique na imagem para exibi-la em tamanho normal.  

[![Diagrama que mostra uma árvore de decisão projetada para ajudá-lo a escolher uma política de streaming predefinida para seu cenário.](./media/streaming-policy/large.png)](./media/streaming-policy/large.png#lightbox)

Ao criptografar seu conteúdo, você precisa criar uma [política de chave de conteúdo](content-key-policy-concept.md), a política de chave de **conteúdo** não é necessária para streaming ou download claro. 

Se você tiver requisitos especiais (por exemplo, se quiser especificar protocolos diferentes, precisar usar um serviço de entrega de chaves personalizado ou precisar usar uma faixa de áudio clara), poderá [criar](/rest/api/media/streamingpolicies/create) uma política de streaming personalizada. 

## <a name="get-a-streaming-policy-definition"></a>Obter uma definição de política de streaming  

Se você quiser ver a definição de uma política de streaming, use [Get](/rest/api/media/streamingpolicies/get) e especifique o nome da política. Por exemplo:

### <a name="rest"></a>REST

Solicitação:

```
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Media/mediaServices/contosomedia/streamingPolicies/clearStreamingPolicy?api-version=2018-07-01
```

Resposta:

```
{
  "name": "clearStreamingPolicy",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Media/mediaservices/contosomedia/streamingPolicies/clearStreamingPolicy",
  "type": "Microsoft.Media/mediaservices/streamingPolicies",
  "properties": {
    "created": "2018-08-08T18:29:30.8501486Z",
    "noEncryption": {
      "enabledProtocols": {
        "download": true,
        "dash": true,
        "hls": true,
        "smoothStreaming": true
      }
    }
  }
}
```

## <a name="filtering-ordering-paging"></a>Filtragem, classificação, paginação

Confira [Filtragem, classificação, paginação de entidades dos Serviços de Mídia](entities-overview.md).

## <a name="next-steps"></a>Próximas etapas

* [Transmitir um arquivo por streaming](stream-files-dotnet-quickstart.md)
* [Usar criptografia dinâmica AES-128 e o serviço de entrega de chave](protect-with-aes128.md)
* [Usar o serviço de entrega de licença e criptografia dinâmica do DRM](protect-with-drm.md)
