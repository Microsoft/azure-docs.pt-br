---
title: Visão geral do Resource Health do Gateway de Aplicativo do Azure
description: Este artigo é uma visão geral do recurso de integridade de recursos para Aplicativo Azure gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 7/9/2019
ms.author: victorh
ms.openlocfilehash: 7e30a93f8270cfaf8910130cc1e2633bb80c2b8e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93397165"
---
# <a name="azure-application-gateway-resource-health-overview"></a>Visão geral do Resource Health do Gateway de Aplicativo do Azure

O [Azure Resource Health](../service-health/resource-health-overview.md) ajuda a diagnosticar e obter suporte quando um problema de serviço do Azure afeta seus recursos. Ele informa sobre a integridade atual e anterior dos seus recursos. E fornece suporte técnico para ajudá-lo a mitigar os problemas.

Para o gateway de aplicativo, Resource Health se baseia em sinais emitidos pelo gateway para avaliar se ele está íntegro ou não. Se o gateway não estiver íntegro, Resource Health analisará informações adicionais para determinar a origem do problema. Ele também identifica ações que a Microsoft está assumindo ou o que você pode fazer para corrigir o problema.

Para obter detalhes adicionais sobre como a integridade é avaliada, revise a lista completa de tipos de recursos e verificações de integridade no [Azure Resource Health](../service-health/resource-health-checks-resource-types.md#microsoftnetworkapplicationgateways).


O status de integridade do gateway de aplicativo é exibido como um dos seguintes status:

## <a name="available"></a>Disponível

Um status disponível significa que o serviço **não** detectou nenhum evento que afete a integridade do recurso. Você verá a notificação **recentemente resolvida** em casos em que o gateway se recuperou do tempo de inatividade não planejado durante as últimas 24 horas.

![Status de integridade disponível](media/resource-health-overview/available-full.png)

## <a name="unavailable"></a>Indisponível

Um status **indisponível** significa que o serviço detectou uma plataforma contínua ou um evento de não plataforma que afeta a integridade do gateway.

### <a name="platform-events"></a>Eventos de plataforma

Os eventos da plataforma são disparados por vários componentes da infraestrutura do Azure. Eles incluem ações agendadas (por exemplo, manutenção planejada) e incidentes inesperados (por exemplo, uma reinicialização do host não planejada).

O Resource Health fornece detalhes adicionais sobre o evento e o processo de recuperação. Ele também permite que você entre em contato com o suporte mesmo se você não tiver um contrato de suporte ativo da Microsoft.

![Status indisponível](media/resource-health-overview/unavailable.png)

## <a name="unknown"></a>Unknown

O status de integridade **desconhecido** indica Resource Health não recebeu informações sobre o gateway por mais de 10 minutos. Esse status não é uma indicação definitiva do estado do gateway. Mas é um ponto de dados importante no processo de solução de problemas.

Se o gateway estiver sendo executado conforme o esperado, o status será alterado para **disponível** após alguns minutos.

Se você estiver enfrentando problemas, o status de integridade **desconhecido** poderá sugerir que um evento na plataforma esteja afetando o gateway.

![Status desconhecido](media/resource-health-overview/unknown.png)

## <a name="degraded"></a>Degradado

O status de integridade **degradado** indica que o gateway detectou uma perda no desempenho, embora ainda esteja disponível para uso.

![Status inparabéns](media/resource-health-overview/degraded.png)

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como solucionar problemas do firewall do aplicativo Web do gateway de aplicativo (WAF), consulte [solucionar problemas de WAF (firewall do aplicativo Web) para aplicativo Azure gateway](../web-application-firewall/ag/web-application-firewall-troubleshoot.md).