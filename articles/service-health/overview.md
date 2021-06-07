---
title: O que é a Integridade do Serviço do Azure?
description: Informações personalizadas sobre como seus aplicativos do Azure são afetados pela manutenção e pelos problemas de serviço atuais e futuros do Azure.
ms.topic: overview
ms.date: 05/10/2019
ms.openlocfilehash: cb1eb8eb75a18a73e8f024f02217df37f45981fe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87499765"
---
# <a name="what-is-azure-service-health"></a>O que é a Integridade do Serviço do Azure?

O Azure oferece um conjunto de experiências para manter você informado sobre a integridade dos seus recursos de nuvem. Essas informações incluem problemas atuais e futuros, como eventos de impacto de serviço, manutenção planejada e outras alterações que podem afetar sua disponibilidade.

A Integridade do Serviço do Azure é uma combinação de três serviços menores separados.

O [status do Azure](azure-status-overview.md) informa sobre interrupções de serviço no Azure na **[página Status do Azure](https://status.azure.com)** . A página é uma exibição global da integridade de todos os serviços do Azure em todas as regiões do Azure. A página de status é uma boa referência para incidentes com impacto generalizado, mas é altamente recomendável que os usuários atuais do Azure aproveitem a integridade do serviço do Azure para se manterem informados sobre incidentes e manutenção do Azure.

A [integridade do serviço](service-health-overview.md) fornece uma exibição personalizada da integridade dos serviços e das regiões do Azure que você está usando. Esse é o melhor lugar onde procurar serviços que afetam as comunicações sobre interrupções, atividades de manutenção planejada e outros avisos de integridade, porque a experiência autenticada da Integridade do Serviço sabe quais serviços e recursos você usa atualmente. A melhor maneira de usar a Integridade do Serviço é configurar alertas de Integridade do Serviço para notificá-lo por meio de seus canais de comunicação preferenciais quando problemas de serviço, manutenção planejada ou outras alterações podem afetar os serviços do Azure e as regiões que você usa.

O [Resource Health](resource-health-overview.md) fornece informações sobre a integridade de seus recursos de nuvem individuais, como uma instância de máquina virtual específica. Usando o Azure Monitor, você também pode configurar alertas para notificá-lo de alterações de disponibilidade para seus recursos de nuvem. O Resource Health, em conjunto com as notificações do Azure Monitor, ajuda você a ficar mais bem informado sobre a disponibilidade de seus recursos de minuto a minuto e a avaliar rapidamente se um problema ocorreu devido a uma complicação no seu lado ou está relacionado a um evento da plataforma do Azure.

Juntas, essas experiências fornecem uma visão abrangente da integridade do Azure, na granularidade mais relevante para você.

**Assista a uma visão geral da página Status do Azure, Integridade do Serviço do Azure e Azure Resource Health**

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OgX6]

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]