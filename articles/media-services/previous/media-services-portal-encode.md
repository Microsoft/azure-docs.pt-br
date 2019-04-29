---
title: Codificar um ativo usando o Media Encoder Standard no Portal do Azure | Microsoft Docs
description: Este tutorial guia você pelas etapas de codificação de um ativo usando o Media Encoder Standard no Portal do Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 107d9e9a-71e9-43e5-b17c-6e00983aceab
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 90190f426419e65bd580b9004ae76a2c6b0c12e2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61463134"
---
# <a name="encode-an-asset-by-using-media-encoder-standard-in-the-azure-portal"></a>Codificar um ativo usando o Media Encoder Standard no Portal do Azure

> [!NOTE]
> Para concluir este tutorial, você precisa de uma conta do Azure. Para obter detalhes, confira [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/). 
> 
> 

Ao trabalhar com os Serviços de Mídia do Azure, um dos cenários mais comuns é fornecer streaming de taxa de bits adaptável aos clientes dos Serviços de Mídia do Azure. Os Serviços de Mídia oferecem suporte para as seguintes tecnologias de streaming de taxa de bits adaptável: Apple HTTP Live Streaming (HLS), Microsoft Smooth Streaming e Dynamic Adaptive Streaming no HTTP (DASH, também chamado de MPEG-DASH). Para preparar os vídeos para streaming de taxa de bits adaptável, primeiro codifique o vídeo de origem como arquivos de múltiplas taxas de bits. Você pode usar o Azure Media Encoder Standard para codificar seus vídeos.  

Os Serviços de Mídia oferecem empacotamento dinâmico. Com o empacotamento dinâmico, você pode fornecer seus MP4s de múltiplas taxas de bits em HLS, Smooth Streaming e MPEG-DASH, sem reempacotar nesses formatos de fluxo contínuo. Quando você usa o empacotamento dinâmico, você pode armazenar e pagar por arquivos em um único formato de armazenamento. Os Serviços de Mídia criam e fornecem a resposta apropriada com base nas solicitações de um cliente.

Para aproveitar o empacotamento dinâmico, você deve codificar o arquivo de origem em um conjunto de arquivos MP4 de múltiplas taxas de bits. As etapas de codificação são demonstradas posteriormente neste artigo.

Para saber como dimensionar o processamento de mídia, consulte [Processamento de mídia de escala usando o Portal do Azure](media-services-portal-scale-media-processing.md).

## <a name="encode-in-the-azure-portal"></a>Codificar no Portal do Azure

Como codificar o conteúdo usando o Media Encoder Standard:

1. No [Portal do Azure](https://portal.azure.com/), selecione sua conta dos Serviços de Mídia do Azure.
2. Selecione **Configurações** > **Ativos**. Selecione o ativo que você deseja codificar.
3. Selecione o botão **Codificar**.
4. No painel **Codificar um ativo** , selecione o processador **Media Encoder Standard** e uma predefinição. Para saber mais sobre as predefinições, confira [Gerar automaticamente uma escada de taxa de bits](media-services-autogen-bitrate-ladder-with-mes.md) e [Predefinições de tarefa para Media Encoder Standard](media-services-mes-presets-overview.md). É importante escolher a predefinição que funcione melhor para o vídeo de entrada. Por exemplo, se você souber que o vídeo de entrada tem uma resolução de 1920 x 1080 pixels, poderá usar a predefinição **H264 Taxas de Bits Múltiplas 1080p**. Se você tiver um vídeo de resolução baixa (640 x 360), você não deverá usar a predefinição **H264 Taxas de Bits Múltiplas 1080p**.
   
   Para ajudá-lo a gerenciar seus recursos, você pode editar o nome do ativo de saída e o nome do trabalho.
   
   ![Codificar ativos](./media/media-services-portal-vod-get-started/media-services-encode1.png)
5. Selecione **Criar**.

## <a name="media-services-learning-paths"></a>Roteiros de aprendizagem dos Serviços de Mídia
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Próximas etapas
* [Monitore o andamento do trabalho de codificação](media-services-portal-check-job-progress.md) no Portal do Azure.  

