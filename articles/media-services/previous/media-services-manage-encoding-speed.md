---
title: Gerenciar a velocidade e a simultaneidade de sua codificação com os Serviços de Mídia do Azure | Microsoft Docs
description: Este artigo fornece uma visão geral de como você pode gerenciar a velocidade e a simultaneidade de seus trabalhos/tarefas de codificação com os Serviços de Mídia do Azure.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 676313f8-a158-4e3a-a99b-2c29a341ecc9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: f01235e5b02bf2a61a359fa5571c6e6d575c1232
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103014801"
---
#  <a name="manage-speed-and-concurrency-of-your-encoding"></a>Gerenciar a velocidade e a simultaneidade de sua codificação

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)] 

Este artigo fornece uma visão geral de como você pode gerenciar a velocidade e a simultaneidade de seus trabalhos/tarefas de codificação.

## <a name="overview"></a>Visão geral

Nos Serviços de Mídia, um **Tipo de Unidade Reservada** que determina a velocidade com que as suas tarefas de processamento de mídia são processadas. Você pode escolher entre os seguintes tipos de unidade reservada: **S1**, **S2** ou **S3**. Por exemplo, o mesmo trabalho de codificação é executado mais rapidamente quando você usa o tipo de unidade reservada **S2** em comparação ao tipo **S1**. O tópico [dimensionamento de unidades de codificação](media-services-scale-media-processing-overview.md) mostra uma tabela que o ajuda a tomar uma decisão ao escolher entre diferentes velocidades de codificação.

Além de especificar o tipo de unidade reservada, você pode especificar o provisionamento da sua conta com **Unidades Reservadas**. O número de unidades reservadas provisionadas determina o número de tarefas de mídia que podem ser processadas simultaneamente em uma determinada conta. Por exemplo, se sua conta tiver cinco unidades reservadas, as cinco tarefas de mídia serão executadas simultaneamente enquanto houver tarefas a serem processadas. As tarefas restantes irão aguardar na fila e serão selecionadas para processamento sequencialmente quando uma tarefa em execução for concluída. Se uma conta não tiver nenhuma unidade reservada provisionada, as tarefas serão selecionadas sequencialmente. Nesse caso, o tempo de espera entre a conclusão de uma tarefa e o início da próxima dependerá da disponibilidade dos recursos do sistema.

Para obter informações detalhadas e exemplos que mostram como dimensionar unidades de codificação, consulte [este](media-services-scale-media-processing-overview.md) tópico.

## <a name="next-step"></a>Próxima etapa

[Dimensionamento de unidades de codificação](media-services-scale-media-processing-overview.md)

## <a name="media-services-learning-paths"></a>Roteiros de aprendizagem dos Serviços de Mídia
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

