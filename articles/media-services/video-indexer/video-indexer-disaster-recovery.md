---
title: Failover e recuperação de desastre do Video Indexer
titleSuffix: Azure Media Services
description: Saiba como fazer failover para uma conta de Video Indexer secundária se ocorrer uma falha de datacenter regional ou um desastre.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 07/29/2019
ms.author: juliako
ms.openlocfilehash: eab376c44065979de86e5c70b796be952fccffaa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87065408"
---
# <a name="video-indexer-failover-and-disaster-recovery"></a>Failover e recuperação de desastre do Video Indexer

O Video Indexer dos Serviços de Mídia do Azure não fornece failover instantâneo do serviço se houver uma interrupção ou falha de datacenter regional. Este artigo explica como configurar seu ambiente para um failover a fim de garantir a disponibilidade ideal para aplicativos e o tempo de recuperação minimizado se ocorrer um desastre.

Recomendamos configurar a recuperação de desastre de continuidade de negócios (BCDR) entre pares regionais para se beneficiar das políticas de isolamento e disponibilidade do Azure. Para obter mais informações, consulte [Regiões emparelhadas do Azure](../../best-practices-availability-paired-regions.md).

## <a name="prerequisites"></a>Pré-requisitos

Uma assinatura do Azure. Se você ainda não tiver uma assinatura do Azure, Inscreva-se para a [avaliação gratuita do Azure](https://azure.microsoft.com/free/).

## <a name="failover-to-a-secondary-account"></a>Failover para uma conta secundária

Para implementar o BCDR, você precisa ter duas contas de Video Indexer para lidar com a redundância.

1. Crie duas contas de Video Indexer conectadas ao Azure (consulte [criar uma conta de video indexer](connect-to-azure.md)). Crie uma conta para sua região primária e a outra para a região emparelhada do Azure.
1. Se houver uma falha em sua região primária, alterne para indexação usando a conta secundária.

> [!TIP]
> Você pode automatizar o BCDR Configurando alertas do log de atividades para notificações de integridade do serviço de acordo com a [criação de alertas do log de atividades em notificações de serviço](../../service-health/alerts-activity-log-service-notifications-portal.md).

Para obter informações sobre como usar vários locatários, consulte [gerenciar vários locatários](manage-multiple-tenants.md). Para implementar o BCDR, escolha uma destas duas opções: [Video indexer conta por locatário](manage-multiple-tenants.md#video-indexer-account-per-tenant) ou [assinatura do Azure por locatário](manage-multiple-tenants.md#azure-subscription-per-tenant).

## <a name="next-steps"></a>Próximas etapas

[Gerenciar uma conta de video indexer conectada ao Azure](manage-account-connected-to-azure.md).
