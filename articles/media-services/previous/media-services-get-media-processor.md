---
title: Como criar um processador de mídia usando o SDK de Serviços de Mídia do Azure para .NET | Microsoft Docs
description: Saiba como criar um componente de processador de mídia para codificar, converter o formato, criptografar ou descriptografar conteúdo de mídia dos Serviços de Mídia do Azure. Os exemplos de código são escritos em C# e usam a SDK dos Serviços de Mídia para .NET.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: dbf9496f-c6f0-42a7-aa36-70f89dcb8ea2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: af6badda426f1bb81d8528cfda9b8c02d55712b3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61463818"
---
# <a name="how-to-get-a-media-processor-instance"></a>Como: Obter uma instância do Processador de Mídia
> [!div class="op_single_selector"]
> * [.NET](media-services-get-media-processor.md)
> * [REST](media-services-rest-get-media-processor.md)
> 
> 

## <a name="overview"></a>Visão geral
Nos Serviços de Mídia, um processador de mídia é um componente que manipula uma tarefa de processamento específica, como codificação, conversão de formato, criptografia ou descriptografia de conteúdo de mídia. Normalmente, você cria um processador de mídia quando está criando uma tarefa para codificar, criptografar ou converter o formato do conteúdo de mídia.

## <a name="azure-media-processors"></a>Processadores de mídia do Azure 

O tópico a seguir fornece listas de processadores de mídia:

* [Codificação de processadores de mídia](scenarios-and-availability.md#encoding-media-processors)
* [Processadores de mídia do Analytics](scenarios-and-availability.md#analytics-media-processors)

## <a name="get-media-processor"></a>Obter processador de mídia

O método a seguir mostra como obter uma instância do processador de mídia. O exemplo de código pressupõe o uso de uma variável no nível do módulo chamada **_context** para referenciar o contexto do servidor, como descrito na seção [Como: Conectar Programaticamente os Serviços de Mídia](media-services-use-aad-auth-to-access-ams-api.md).

    private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
        ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

        if (processor == null)
        throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

        return processor;
    }


## <a name="media-services-learning-paths"></a>Roteiros de aprendizagem dos Serviços de Mídia
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Próximas etapas
Agora que você já sabe como obter uma instância do processador de mídia, vá para o tópico [Como Codificar um Ativo](media-services-dotnet-encode-with-media-encoder-standard.md) , que mostrará como usar o Codificador de Mídia Standard para codificar um ativo.

