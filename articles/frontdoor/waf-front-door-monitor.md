---
title: Monitoramento e registro em log do firewall do aplicativo Web do Azure
description: Saiba mais sobre o WAF (firewall do aplicativo Web) com o monitoramento e registro em log do FrontDoor
services: frontdoor
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/31/2019
ms.author: kumud
ms.reviewer: tyao
ms.openlocfilehash: 675d06f3d2071022da3867a4c45137efb818980d
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849136"
---
# <a name="azure-web-application-firewall-monitoring-and-logging"></a>Monitoramento e registro em log do firewall do aplicativo Web do Azure 

O monitoramento e o registro em log do WAF (firewall do aplicativo Web) do Azure são fornecidos por meio de log e integração com logs de Azure Monitor e Azure Monitor.

## <a name="azure-monitor"></a>Azure Monitor

O WAF com o log do FrontDoor é integrado ao [Azure monitor](../azure-monitor/overview.md). Azure Monitor permite que você acompanhe informações de diagnóstico, incluindo alertas e logs do WAF. Você pode configurar o monitoramento de WAF dentro do recurso de porta frontal no portal na guia **diagnóstico** ou por meio do serviço de Azure monitor diretamente.

Em portal do Azure, vá para tipo de recurso da porta frontal. Na guia**métricas** de **monitoramento**/à esquerda, você pode adicionar **WebApplicationFirewallRequestCount** para rastrear o número de solicitações que correspondem às regras de WAF. Os filtros personalizados podem ser criados com base em tipos de ação e nomes de regra.

![WAFMetrics](./media//waf-front-door-monitor/waf-frontdoor-metrics.png)

## <a name="logs-and-diagnostics"></a>Logs e diagnósticos

WAF com a porta frontal fornece relatórios detalhados sobre cada ameaça detectada. O registro em log é integrado aos Logs de diagnóstico do Azure e os alertas são registrados em um formato json. Esses logs podem ser integrados aos [logs do Azure Monitor](../azure-monitor/insights/azure-networking-analytics.md).

![WAFDiag](./media/waf-front-door-monitor/waf-frontdoor-diagnostics.png)

FrontdoorAccessLog registra todas as solicitações que são encaminhadas para back-ends do cliente. FrontdoorWebApplicationFirewallLog registra qualquer solicitação que corresponda a uma regra de WAF.

A consulta de exemplo a seguir obtém logs do WAF em solicitações bloqueadas:

``` WAFlogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorWebApplicationFirewallLog"
| where action_s == "Block"

```

A consulta de exemplo a seguir obtém entradas AccessLogs:

``` AccessLogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorAccessLog"


```

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre a [porta da frente](front-door-overview.md).

