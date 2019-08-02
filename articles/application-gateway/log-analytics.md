---
title: Usar o Azure Log Analytics para examinar os logs do Firewall do aplicativo de Web de Gateway de aplicativo
description: Este artigo mostra como você pode usar o Azure Log Analytics para examinar os logs do Firewall do aplicativo de Web de Gateway de aplicativo
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 7/10/2019
ms.author: victorh
ms.openlocfilehash: aa867e33ef0faa96b6a66a9075a3a5b8b0b0bca4
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67712170"
---
# <a name="use-log-analytics-to-examine-application-gateway-web-application-firewall-logs"></a>Usar o Log Analytics para examinar os Logs de Firewall do aplicativo de Web de Gateway de aplicativo

Depois que o WAF do Gateway de aplicativo está funcionando, você pode habilitar os logs inspecionar o que está acontecendo com cada solicitação. Firewall logs fornecem insight para os quais o WAF está avaliando, correspondência e bloqueio. Com o Log Analytics, você pode examinar os dados dentro dos logs de firewall para fornecer informações ainda mais. Para obter mais informações sobre como criar um espaço de trabalho do Log Analytics, consulte [criar um espaço de trabalho do Log Analytics no portal do Azure](../azure-monitor/learn/quick-create-workspace.md). Para obter mais informações sobre consultas de log, consulte [visão geral do log de consultas no Azure Monitor](../azure-monitor/log-query/log-query-overview.md).

## <a name="import-waf-logs"></a>Importar logs de WAF

Para importar seus logs de firewall no Log Analytics, consulte [integridade do Back-end, logs de diagnóstico e métricas para o Gateway de aplicativo](application-gateway-diagnostics.md#diagnostic-logging). Quando você tem os logs de firewall em seu espaço de trabalho do Log Analytics, você pode exibir dados, escrever consultas, criar visualizações e adicioná-los ao seu painel do portal.

## <a name="explore-data-with-examples"></a>Explorar dados com exemplos

Para exibir os dados brutos no log de firewall, você pode executar a consulta a seguir:

```
AzureDiagnostics 
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
```

Isso será semelhante à seguinte consulta:

![Consulta do log Analytics](media/log-analytics/log-query.png)

Você pode fazer uma busca detalhada nos dados e gerar gráficos ou criar visualizações a partir daqui. Consulte as consultas a seguir como ponto de partida:

### <a name="matchedblocked-requests-by-ip"></a>Solicitações de correspondência/bloqueados por IP

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by clientIp_s, bin(TimeGenerated, 1m)
| render timechart
```

### <a name="matchedblocked-requests-by-uri"></a>Solicitações de correspondência/bloqueados por URI

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by requestUri_s, bin(TimeGenerated, 1m)
| render timechart
```

### <a name="top-matched-rules"></a>Principais regras correspondentes

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by ruleId_s, bin(TimeGenerated, 1m)
| where count_ > 10
| render timechart
```

### <a name="top-five-matched-rule-groups"></a>Cinco principais grupos de regra correspondente

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize Count=count() by details_file_s, action_s
| top 5 by Count desc
| render piechart
```

## <a name="add-to-your-dashboard"></a>Adicionar ao seu dashboard

Depois de criar uma consulta, você pode adicioná-lo ao seu painel.  Selecione o **fixar no painel** na parte superior direita do espaço de trabalho do log analytics. Com quatro consultas anteriores fixadas a um painel de exemplo, esses são os dados que você pode ver rapidamente:

![painel](media/log-analytics/dashboard.png)

## <a name="next-steps"></a>Próximas etapas

[Integridade do back-end, logs de diagnóstico e métricas para o Gateway de aplicativo](application-gateway-diagnostics.md)
