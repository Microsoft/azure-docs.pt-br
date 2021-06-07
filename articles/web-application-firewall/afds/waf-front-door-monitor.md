---
title: Monitoramento e registro em log do firewall do aplicativo Web do Azure
description: Saiba mais sobre o WAF (firewall do aplicativo Web) com o monitoramento e registro em log do FrontDoor
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 06/09/2020
ms.author: victorh
ms.openlocfilehash: 596374d4f3f188e08a10bd25b36b178cc79a6e57
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "84808945"
---
# <a name="azure-web-application-firewall-monitoring-and-logging"></a>Monitoramento e registro em log do firewall do aplicativo Web do Azure

O monitoramento e o registro em log do WAF (firewall do aplicativo Web) do Azure são fornecidos por meio de log e integração com logs de Azure Monitor e Azure Monitor.

## <a name="azure-monitor"></a>Azure Monitor

O WAF com o log do FrontDoor é integrado ao [Azure monitor](../../azure-monitor/overview.md). Azure Monitor permite que você acompanhe informações de diagnóstico, incluindo alertas e logs do WAF. Você pode configurar o monitoramento de WAF dentro do recurso de porta frontal no portal na guia **diagnóstico** ou por meio do serviço de Azure monitor diretamente.

Em portal do Azure, vá para tipo de recurso da porta frontal. Na  / guia **métricas** de monitoramento à esquerda, você pode adicionar **WebApplicationFirewallRequestCount** para rastrear o número de solicitações que correspondem às regras de WAF. Os filtros personalizados podem ser criados com base em tipos de ação e nomes de regra.

:::image type="content" source="../media/waf-frontdoor-monitor/waf-frontdoor-metrics.png" alt-text="WAFMetrics ":::

## <a name="logs-and-diagnostics"></a>Configurar logs e diagnósticos

WAF com a porta frontal fornece relatórios detalhados sobre cada ameaça detectada. O registro em log é integrado aos Logs de diagnóstico do Azure e os alertas são registrados em um formato json. Esses logs podem ser integrados aos [logs do Azure Monitor](../../azure-monitor/insights/azure-networking-analytics.md).

![WAFDiag](../media/waf-frontdoor-monitor/waf-frontdoor-diagnostics.png)

[FrontdoorAccessLog](../../frontdoor/front-door-diagnostics.md) registra todas as solicitações. O FrontdoorWebApplicationFirewallLog registra em log qualquer solicitação que corresponda a uma regra WAF que tenha o esquema abaixo:

| Propriedade  | Descrição |
| ------------- | ------------- |
|Ação|Ação executada na solicitação|
| ClientIp | Endereço IP do cliente que fez a solicitação. Se houver um cabeçalho X-Forwardd-for na solicitação, o IP do cliente será escolhido do campo de cabeçalho. |
| ClientPort | A porta IP do cliente que fez a solicitação. |
| Detalhes|Detalhes adicionais sobre a solicitação correspondente |
|| matchVariableName: nome do parâmetro http da solicitação correspondente, por exemplo, nomes de cabeçalho|
|| matchVariableValue: valores que acionaram a correspondência|
| Host | O cabeçalho de host da solicitação correspondente |
| Política | O nome da política de WAF que a solicitação correspondeu. |
| Política de | Modo de operações da política WAF. Os valores possíveis são "prevenção" e "detecção" |
| RequestUri | URI completo da solicitação correspondente. |
| RuleName | O nome da regra WAF que a solicitação correspondeu. |
| SocketIp | O endereço IP de origem visto por WAF. Esse endereço IP é baseado na sessão TCP, independentemente de quaisquer cabeçalhos de solicitação.|
| TrackingReference | A cadeia de caracteres de referência exclusiva que identifica uma solicitação atendida pela Front Door, também enviada como o cabeçalho X-Azure-Ref para o cliente. Necessário para pesquisar detalhes nos logs de acesso para uma solicitação específica. |

O exemplo de consulta a seguir retorna logs de WAF em solicitações bloqueadas:

``` WAFlogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorWebApplicationFirewallLog"
| where action_s == "Block"

```

Aqui está um exemplo de uma solicitação registrada no log do WAF:

``` WAFlogQuerySample
{
    "time":  "2020-06-09T22:32:17.8376810Z",
    "category": "FrontdoorWebApplicationFirewallLog",
    "operationName": "Microsoft.Network/FrontDoorWebApplicationFirewallLog/Write",
    "properties":
    {
        "clientIP":"xxx.xxx.xxx.xxx",
        "clientPort":"52097",
        "socketIP":"xxx.xxx.xxx.xxx",
        "requestUri":"https://wafdemofrontdoorwebapp.azurefd.net:443/?q=%27%20or%201=1",
        "ruleName":"Microsoft_DefaultRuleSet-1.1-SQLI-942100",
        "policy":"WafDemoCustomPolicy",
        "action":"Block",
        "host":"wafdemofrontdoorwebapp.azurefd.net",
        "trackingReference":"08Q3gXgAAAAAe0s71BET/QYwmqtpHO7uAU0pDRURHRTA1MDgANjMxNTAwZDAtOTRiNS00YzIwLTljY2YtNjFhNzMyOWQyYTgy",
        "policyMode":"prevention",
        "details":
            {
            "matches":
                [{
                "matchVariableName":"QueryParamValue:q",
                "matchVariableValue":"' or 1=1"
                }]
            }
     }
}
```

A consulta de exemplo a seguir retorna entradas de AccessLogs:

``` AccessLogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorAccessLog"

```

Aqui está um exemplo de uma solicitação registrada no log do Access:

``` AccessLogSample
{
"time": "2020-06-09T22:32:17.8383427Z",
"category": "FrontdoorAccessLog",
"operationName": "Microsoft.Network/FrontDoor/AccessLog/Write",
 "properties":
    {
    "trackingReference":"08Q3gXgAAAAAe0s71BET/QYwmqtpHO7uAU0pDRURHRTA1MDgANjMxNTAwZDAtOTRiNS00YzIwLTljY2YtNjFhNzMyOWQyYTgy",
    "httpMethod":"GET",
    "httpVersion":"2.0",
    "requestUri":"https://wafdemofrontdoorwebapp.azurefd.net:443/?q=%27%20or%201=1",
    "requestBytes":"715",
    "responseBytes":"380",
    "userAgent":"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/85.0.4157.0 Safari/537.36 Edg/85.0.531.1",
    "clientIp":"xxx.xxx.xxx.xxx",
    "socketIp":"xxx.xxx.xxx.xxx",
    "clientPort":"52097",
    "timeTaken":"0.003",
    "securityProtocol":"TLS 1.2",
    "routingRuleName":"WAFdemoWebAppRouting",
    "rulesEngineMatchNames":[],
    "backendHostname":"wafdemowebappuscentral.azurewebsites.net:443",
    "sentToOriginShield":false,
    "httpStatusCode":"403",
    "httpStatusDetails":"403",
    "pop":"SJC",
    "cacheStatus":"CONFIG_NOCACHE"
    }
}

```

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre a [porta da frente](../../frontdoor/front-door-overview.md).
