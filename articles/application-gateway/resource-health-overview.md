---
title: Visão geral de integridade do recurso de Gateway de aplicativo do Azure
description: Este artigo é uma visão geral do recurso de integridade do recurso do Gateway de aplicativo do Azure
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 7/9/2019
ms.author: victorh
ms.openlocfilehash: db29551a8150b70e797d45fe659482470c8aca2a
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67659496"
---
# <a name="azure-application-gateway-resource-health-overview"></a>Visão geral de integridade do recurso de Gateway de aplicativo do Azure

[O Azure Resource Health](../service-health/resource-health-overview.md) ajuda você a diagnosticar e obter suporte quando um problema de serviço do Azure afeta seus recursos. Ele informa sobre a integridade atual e anterior dos seus recursos. E fornece suporte técnico para ajudá-lo a mitigar os problemas.

Para o Gateway de aplicativo, o Resource Health se baseia em sinais emitidos pelo gateway para avaliar se ele está íntegro ou não. Se o gateway não está íntegro, o Resource Health analisa informações adicionais para determinar a origem do problema. Ele também identifica ações que a Microsoft está tomando ou que você pode fazer para corrigir o problema.

Para obter detalhes adicionais sobre como a integridade é avaliada, revise a lista completa de tipos de recursos e verificações de integridade no [Azure Resource Health](../service-health/resource-health-checks-resource-types.md#microsoftnetworkapplicationgateways).


O status de integridade do Gateway de aplicativo é exibido como um dos seguintes status:

## <a name="available"></a>Disponível

Uma **disponível** status significa que o serviço não detectou todos os eventos que afetam a integridade do recurso. Você verá a **recentemente resolvidos** notificação nos casos em que o gateway tem recuperado de tempo de inatividade não planejado nas últimas 24 horas.

![Status de integridade disponíveis](media/resource-health-overview/available-full.png)

## <a name="unavailable"></a>Indisponível

Uma **indisponível** status significa que o serviço detectou uma plataforma em andamento ou evento de não plataforma que afeta a integridade do gateway.

### <a name="platform-events"></a>Eventos de plataforma

Os eventos da plataforma são disparados por vários componentes da infraestrutura do Azure. Eles incluem ações agendadas (por exemplo, manutenção planejada) e incidentes inesperados (por exemplo, uma reinicialização do host não planejada).

O Resource Health fornece detalhes adicionais sobre o evento e o processo de recuperação. Ele também permite que você entre em contato com o suporte mesmo se você não tiver um contrato de suporte ativo da Microsoft.

![Status indisponível](media/resource-health-overview/unavailable.png)

## <a name="unknown"></a>Unknown

O **desconhecido** status de integridade indica que o Resource Health não recebeu informações sobre o gateway por mais de 10 minutos. Esse status não é uma indicação definitiva do estado do gateway. Mas é um ponto de dados importantes no processo de solução de problemas.

Se o gateway está em execução conforme o esperado, o status muda para **disponível** depois de alguns minutos.

Se você estiver tendo problemas, o **desconhecido** status de integridade pode sugerir que um evento na plataforma está afetando o gateway.

![Status desconhecido](media/resource-health-overview/unknown.png)

## <a name="degraded"></a>Degradado

O **degradado** status de integridade indica seu gateway detectou uma queda no desempenho, embora ele ainda está disponível para uso.

![Status degrated](media/resource-health-overview/degraded.png)

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como solucionar problemas de Gateway de aplicativo Web Application Firewall (WAF), consulte [solucionar problemas de aplicativo WAF (Firewall Web) para o Gateway de aplicativo do Azure](web-application-firewall-troubleshoot.md).