---
title: Políticas de streaming nos Serviços de Mídia do Azure | Microsoft Docs
description: Este artigo fornece uma explicação sobre o que são as políticas de streaming e como são usados pelos Serviços de Mídia do Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/03/2019
ms.author: juliako
ms.openlocfilehash: 10600d8f3ff4e08b8d90f28ec15d3cb0c56bcae0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61230889"
---
# <a name="streaming-policies"></a>Políticas de Streaming

Nos Serviços de Mídia do Azure v3, as [Políticas de Streaming](https://docs.microsoft.com/rest/api/media/streamingpolicies) permitem definir protocolos de streaming e opções de criptografia para seus [Localizadores de Streaming](streaming-locators-concept.md). Você pode usar uma das Políticas de Streaming predefinidas ou criar uma política personalizada. As Políticas de Streaming predefinidas disponíveis no momento são: 'Predefined_DownloadOnly', 'Predefined_ClearStreamingOnly', 'Predefined_DownloadAndClearStreaming', 'Predefined_ClearKey', 'Predefined_MultiDrmCencStreaming' e 'Predefined_MultiDrmStreaming'.

> [!IMPORTANT]
> * As propriedades de **Políticas de Streaming** que são do tipo Datetime estão sempre no formato UTC.
> * Você deve criar um conjunto limitado de políticas para sua conta de serviço de mídia e reutilizá-los para os Localizadores de Streaming sempre que as mesmas opções forem necessárias. 

## <a name="examples"></a>Exemplos

### <a name="not-encrypted"></a>Não criptografado

Se você deseja transmitir seu arquivo sem criptografia, defina a política de streaming, não criptografada, predefinida: para “Predefined_ClearStreamingOnly” (no .NET, você pode usar PredefinedStreamingPolicy.ClearStreamingOnly).

```csharp
StreamingLocator locator = await client.StreamingLocators.CreateAsync(
    resourceGroup,
    accountName,
    locatorName,
    new StreamingLocator
    {
        AssetName = assetName,
        StreamingPolicyName = PredefinedStreamingPolicy.ClearStreamingOnly
    });
```

### <a name="encrypted"></a>Criptografado 

Se você precisar criptografar seu conteúdo com criptografia de envelope e cenc, defina sua política para “Predefined_MultiDrmCencStreaming”. Essa política indica que você deseja que duas chaves de conteúdo (envelope e CENC) sejam geradas e definidas no localizador. Assim, as criptografias do envelope, PlayReady e Widevine são aplicadas (a chave é entregue ao cliente de reprodução com base nas licenças de DRM configuradas).

```csharp
StreamingLocator locator = await client.StreamingLocators.CreateAsync(
    resourceGroup,
    accountName,
    locatorName,
    new StreamingLocator
    {
        AssetName = assetName,
        StreamingPolicyName = "Predefined_MultiDrmCencStreaming",
        DefaultContentKeyPolicyName = contentPolicyName
    });
```

Se você também quiser criptografar seu fluxo com CBCS (FairPlay), use “Predefined_MultiDrmStreaming”.

## <a name="filtering-ordering-paging"></a>Filtragem, classificação, paginação

Confira [Filtragem, classificação, paginação de entidades dos Serviços de Mídia](entities-overview.md).

## <a name="next-steps"></a>Próximas etapas

* [Transmitir um arquivo por streaming](stream-files-dotnet-quickstart.md)
* [Usar criptografia dinâmica AES-128 e o serviço de entrega de chave](protect-with-aes128.md)
* [Use criptografia dinâmica DRM e serviço de entrega de licenças](protect-with-drm.md)
