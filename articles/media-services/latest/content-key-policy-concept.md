---
title: Políticas de Chave de Conteúdo nos Serviços de Mídia – Azure | Microsoft Docs
description: Este artigo fornece uma explicação de quais são as Políticas de Chave de Conteúdo e como elas são usadas pelos Serviços de Mídia do Azure.
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
ms.custom: seodec18
ms.openlocfilehash: d9e86c45d535862e0c3d02b3f331bc40ebb7f6c7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60733038"
---
# <a name="content-key-policies"></a>Políticas da Chave de Conteúdo

Com os Serviços de Mídia, é possível entregar o conteúdo ao vivo e sob demanda criptografado dinamicamente com a criptografia AES (AES-128) ou qualquer um dos três principais sistemas DRM (gerenciamento de direitos digitais): Microsoft PlayReady, Google Widevine e Apple FairPlay. Os serviços de mídia também fornecem um serviço de distribuição de chaves AES e licenças DRM (PlayReady, Widevine e FairPlay) para os clientes autorizados.

Para especificar opções de criptografia em seu fluxo, você precisará criar a [Política de Chave de Conteúdo](https://docs.microsoft.com/rest/api/media/contentkeypolicies) e associá-la ao **Localizador de Streaming**. A **Política de Chave de Conteúdo** configura como a chave de conteúdo é entregue aos clientes finais por meio do componente Distribuição de Chave dos Serviços de Mídia. Você pode permitir que os Serviços de Mídia gerem automaticamente a chave de conteúdo. Normalmente, você usaria uma chave de vida útil longa e verificaria a existência de políticas com Get. Para ter acesso à chave, você precisa chamar um método de ação separado para obter segredos ou as credenciais. Confira o exemplo a seguir.

As **Políticas de Chave de Conteúdo** podem ser atualizadas. Por exemplo, você poderá querer atualizar a política se precisar fazer uma rotação de chaves. Você pode atualizar a chave de verificação principal e a lista de chaves de verificação alternativas na política existente. Pode levar até 15 minutos para que os caches de entrega de Chaves atualizem e selecionem a política atualizada. 

> [!IMPORTANT]
> * As propriedades de **Políticas de Conteúdo de Chave** que são do tipo Datetime estão sempre no formato UTC.
> * Você deve criar um conjunto limitado de políticas para sua conta de serviço de mídia e reutilizá-lo para os Localizadores de Streaming sempre que as mesmas opções forem necessárias. 

## <a name="example"></a>Exemplo

Para obter a chave, use **GetPolicyPropertiesWithSecretsAsync**, conforme mostrado no exemplo a seguir.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="filtering-ordering-paging"></a>Filtragem, classificação, paginação

Confira [Filtragem, classificação, paginação de entidades dos Serviços de Mídia](entities-overview.md).

## <a name="next-steps"></a>Próximas etapas

* [Usar criptografia dinâmica AES-128 e o serviço de entrega de chave](protect-with-aes128.md)
* [Use criptografia dinâmica DRM e serviço de entrega de licenças](protect-with-drm.md)
* [EncodeHTTPAndPublishAESEncrypted](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/EncodeHTTPAndPublishAESEncrypted)
