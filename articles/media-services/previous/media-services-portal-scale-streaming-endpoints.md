---
title: Dimensionar pontos de extremidade de streaming com o portal do Azure | Microsoft Docs
description: Este tutorial orienta você pelas etapas de dimensionar pontos de extremidade de streaming usando o portal do Azure.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 1008b3a3-2fa1-4146-85bd-2cf43cd1e00e
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 4db53d64131e6fb43ce425cf579d79e62775754a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103009737"
---
# <a name="scale-streaming-endpoints-with-the-azure-portal"></a>Dimensionar pontos de extremidade de streaming com o portal do Azure

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

## <a name="overview"></a>Visão geral

> [!NOTE]
> Para concluir este tutorial, você precisa de uma conta do Azure. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/). 
> 
> 

Os pontos de extremidade do streaming **Premium** são adequados para as cargas de trabalho avançadas, fornecendo uma capacidade de largura de banda dimensionável e dedicada. Os clientes que têm um ponto de extremidade de streaming **Premium**, por padrão, obtêm uma US (Unidade de Streaming). O ponto de extremidade de streaming pode ser dimensionado adicionando USs. Cada US fornece uma capacidade de largura de banda adicional para o aplicativo. Para saber mais sobre tipos de ponto de extremidade de streaming e configuração de CDN, confira o tópico [Visão geral do Ponto de Extremidade do Streaming](media-services-streaming-endpoints-overview.md).
 
Este tópico mostra como dimensionar um ponto de extremidade de streaming.

Para saber mais sobre os detalhes de preços, consulte [Detalhes de preços dos Serviços de Mídia](https://azure.microsoft.com/pricing/details/media-services/).

## <a name="scale-streaming-endpoints"></a>Dimensionar pontos de extremidade de streaming

Para alterar o número de unidades de streaming, faça o seguinte:

1. No [Portal do Azure](https://portal.azure.com/), selecione sua conta dos Serviços de Mídia do Azure.
2. Na janela **Configurações**, selecione **Pontos de extremidade de streaming**.
3. Clique no ponto de extremidade de streaming que você deseja dimensionar. 

    > [!NOTE] 
    > É possível dimensionar apenas pontos de extremidade de streaming **Premium**.

4. Mova o controle deslizante para especificar o número de unidades de streaming.

    ![ponto de extremidade de streaming](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints3.png)

## <a name="next-steps"></a>Próximas etapas
Examine os roteiros de aprendizagem dos Serviços de Mídia.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

