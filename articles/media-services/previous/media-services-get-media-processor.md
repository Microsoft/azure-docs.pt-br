---
title: Como criar um processador de mídia usando o SDK de Serviços de Mídia do Azure para .NET | Microsoft Docs
description: Saiba como criar um componente de processador de mídia para codificar, converter o formato, criptografar ou descriptografar conteúdo de mídia dos Serviços de Mídia do Azure. Os exemplos de código são escritos em C# e usam a SDK dos Serviços de Mídia para .NET.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: dbf9496f-c6f0-42a7-aa36-70f89dcb8ea2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 6ce2a28ff9b34373cc716ea397927ef160bd1097
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103013339"
---
# <a name="how-to-get-a-media-processor-instance"></a>Como: obter uma instância do processador de mídia

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [.NET](media-services-get-media-processor.md)
> * [REST](media-services-rest-get-media-processor.md)

## <a name="overview"></a>Visão geral

Nos Serviços de Mídia, um processador de mídia é um componente que manipula uma tarefa de processamento específica, como codificação, conversão de formato, criptografia ou descriptografia de conteúdo de mídia. Normalmente, você cria um processador de mídia quando está criando uma tarefa para codificar, criptografar ou converter o formato do conteúdo de mídia.

## <a name="azure-media-processors"></a>Processadores de mídia do Azure

O tópico a seguir fornece listas de processadores de mídia:

* [Codificando processadores de mídia](scenarios-and-availability.md)
* [Processadores de mídia da Análise](scenarios-and-availability.md)

## <a name="get-media-processor"></a>Obter processador de mídia

O método a seguir mostra como obter uma instância do processador de mídia. O exemplo de código pressupõe o uso de uma variável em nível de módulo chamada **_context** para fazer referência ao contexto do servidor, conforme é descrito na seção [Como conectar-se aos Serviços de Mídia de forma programática](media-services-use-aad-auth-to-access-ams-api.md).

```csharp
private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
{
    var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
    ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

    if (processor == null)
    throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

    return processor;
}
```

## <a name="media-services-learning-paths"></a>Roteiros de aprendizagem dos Serviços de Mídia

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Próximas etapas

Agora que você já sabe como obter uma instância do processador de mídia, vá para o tópico [Como Codificar um Ativo](media-services-dotnet-encode-with-media-encoder-standard.md) , que mostrará como usar o Codificador de Mídia Standard para codificar um ativo.