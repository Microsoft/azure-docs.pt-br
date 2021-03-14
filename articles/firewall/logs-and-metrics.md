---
title: Visão geral dos logs e das métricas do firewall do Azure
description: Você pode monitorar o Firewall do Azure usando os logs de firewall. Você também pode usar os logs de atividades para auditar operações nos recursos do Firewall do Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 02/16/2021
ms.author: victorh
ms.openlocfilehash: 8922e6e0d5137a3a900e0f57f685d449c08b3f47
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100596967"
---
# <a name="azure-firewall-logs-and-metrics"></a>Logs e métricas do Firewall do Azure

Você pode monitorar o Firewall do Azure usando os logs de firewall. Você também pode usar os logs de atividades para auditar operações nos recursos do Firewall do Azure.

Você pode acessar alguns desses logs por meio do portal. Os logs podem ser enviados para [Azure monitor logs](../azure-monitor/insights/azure-networking-analytics.md), armazenamento e hubs de eventos e analisados em logs de Azure monitor ou ferramentas diferentes, como Excel e Power bi.

As métricas são leves e podem dar suporte a cenários quase em tempo real, tornando-as úteis para alertas e detecção rápida de problemas.

## <a name="diagnostic-logs"></a>Logs de diagnóstico

 Os seguintes logs de diagnóstico estão disponíveis para o Firewall do Azure:

* **Log de regra de aplicativo**

   O log de regras de aplicativo é salvo em uma conta de armazenamento, transmitida para os hubs de eventos e/ou enviados para Azure Monitor logs somente se você o tiver habilitado para cada Firewall do Azure. Cada nova conexão que corresponda a uma de suas regras de aplicativo configurado gera um log para a conexão aceita/negada. Os dados são registrados no formato JSON, conforme mostrado nos exemplos a seguir:

   ```
   Category: application rule logs.
   Time: log timestamp.
   Properties: currently contains the full message.
   note: this field will be parsed to specific fields in the future, while maintaining backward compatibility with the existing properties field.
   ```

   ```json
   {
    "category": "AzureFirewallApplicationRule",
    "time": "2018-04-16T23:45:04.8295030Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallApplicationRuleLog",
    "properties": {
        "msg": "HTTPS request from 10.1.0.5:55640 to mydestination.com:443. Action: Allow. Rule Collection: collection1000. Rule: rule1002"
    }
   }
   ```

   ```json
   {
     "category": "AzureFirewallApplicationRule",
     "time": "2018-04-16T23:45:04.8295030Z",
     "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
     "operationName": "AzureFirewallApplicationRuleLog",
     "properties": {
         "msg": "HTTPS request from 10.11.2.4:53344 to www.bing.com:443. Action: Allow. Rule Collection: ExampleRuleCollection. Rule: ExampleRule. Web Category: SearchEnginesAndPortals"
     }
   }
   ```

* **Log de regra de rede**

   O log de regras de rede é salvo em uma conta de armazenamento, transmitida para os hubs de eventos e/ou enviados para Azure Monitor logs somente se você o tiver habilitado para cada Firewall do Azure. Cada nova conexão que corresponda a uma de suas regras de rede configuradas gera um log para a conexão aceita/negada. Os dados são registrados no formato JSON, conforme mostrado no seguinte exemplo:

   ```
   Category: network rule logs.
   Time: log timestamp.
   Properties: currently contains the full message.
   note: this field will be parsed to specific fields in the future, while maintaining backward compatibility with the existing properties field.
   ```

   ```json
  {
    "category": "AzureFirewallNetworkRule",
    "time": "2018-06-14T23:44:11.0590400Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallNetworkRuleLog",
    "properties": {
        "msg": "TCP request from 111.35.136.173:12518 to 13.78.143.217:2323. Action: Deny"
    }
   }

   ```

* **Log de proxy DNS**

   O log de proxy DNS será salvo em uma conta de armazenamento, transmitida para os hubs de eventos e/ou enviado para Azure Monitor logs somente se você o tiver habilitado para cada Firewall do Azure. Esse log rastreia mensagens DNS para um servidor DNS configurado usando o proxy DNS. Os dados são registrados no formato JSON, conforme mostrado nos exemplos a seguir:


   ```
   Category: DNS proxy logs.
   Time: log timestamp.
   Properties: currently contains the full message.
   note: this field will be parsed to specific fields in the future, while maintaining backward compatibility with the existing properties field.
   ```

   Sucesso:
   ```json
   {
     "category": "AzureFirewallDnsProxy",
     "time": "2020-09-02T19:12:33.751Z",
     "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
     "operationName": "AzureFirewallDnsProxyLog",
     "properties": {
         "msg": "DNS Request: 11.5.0.7:48197 – 15676 AAA IN md-l1l1pg5lcmkq.blob.core.windows.net. udp 55 false 512 NOERROR - 0 2.000301956s"
     }
   }
   ```

   Falha:

   ```json
   {
     "category": "AzureFirewallDnsProxy",
     "time": "2020-09-02T19:12:33.751Z",
     "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
     "operationName": "AzureFirewallDnsProxyLog",
     "properties": {
         "msg": " Error: 2 time.windows.com.reddog.microsoft.com. A: read udp 10.0.1.5:49126->168.63.129.160:53: i/o timeout”
     }
   }
   ```

   formato da msg:

   `[client’s IP address]:[client’s port] – [query ID] [type of the request] [class of the request] [name of the request] [protocol used] [request size in bytes] [EDNS0 DO (DNSSEC OK) bit set in the query] [EDNS0 buffer size advertised in the query] [response CODE] [response flags] [response size] [response duration]`

Você tem três opções para armazenar os logs:

* **Conta de armazenamento**: as contas de armazenamento são mais adequadas para os logs quando eles são armazenados por mais tempo e examinados quando necessário.
* **Hubs de eventos**: os hubs de eventos são uma ótima opção para integração a outras ferramentas SEIM (informações de segurança e gerenciamento de evento) para receber alertas sobre os recursos.
* **Logs de Azure monitor**: os logs de Azure monitor são mais bem usados para o monitoramento geral em tempo real de seu aplicativo ou a análise de tendências.

## <a name="activity-logs"></a>Logs de atividades

   As entradas do log de atividades são coletadas por padrão e podem ser exibidas no portal do Azure.

   Você pode usar [os logs de atividades do Azure](../azure-resource-manager/management/view-activity-logs.md) (anteriormente conhecidos como logs operacionais e logs de auditoria) para exibir todas as operações enviadas à sua assinatura do Azure.

## <a name="metrics"></a>Métricas

As métricas em Azure Monitor são valores numéricos que descrevem algum aspecto de um sistema em um determinado momento. As métricas são coletadas a cada minuto e são úteis para alertas porque podem ser amostradas com frequência. Um alerta pode ser acionado rapidamente com lógica relativamente simples.

As seguintes métricas estão disponíveis para o Firewall do Azure:

- **Contagem de ocorrências de regras de aplicativo** -o número de vezes que uma regra de aplicativo foi atingida.

    Unidade: contagem

- **Contagem de ocorrências de regras de rede** -o número de vezes que uma regra de rede foi atingida.

    Unidade: contagem

- **Dados processados** -soma de dados que atravessam o firewall em uma determinada janela de tempo.

    Unidade: bytes

- **Produtividade** -taxa de dados atravessando o firewall por segundo.

    Unidade: bits por segundo

- **Estado de integridade do firewall** – indica a integridade do firewall com base na disponibilidade da porta SNAT.

    Unidade: porcentagem

   Essa métrica tem duas dimensões:
  - Status: os valores possíveis estão *íntegros*, *degradados*, não *íntegros*.
  - Motivo: indica o motivo do status correspondente do firewall. 

     Se as portas SNAT forem usadas > 95%, elas serão consideradas esgotadas e a integridade será 50% com status =**degradado** e motivo =**porta SNAT**. O firewall mantém o tráfego de processamento, e as conexões existentes não são afetadas. No entanto, novas conexões podem não ser estabelecidas de forma intermitente.

     Se as portas SNAT forem usadas < 95%, o firewall será considerado íntegro e a integridade será mostrada como 100%.

     Se nenhum uso de portas SNAT for relatado, a integridade será mostrada como 0%. 

- **Utilização da porta SNAT** -a porcentagem de portas SNAT que foram utilizadas pelo firewall.

    Unidade: porcentagem

   Quando você adiciona mais endereços IP públicos ao firewall, mais portas SNAT ficam disponíveis, reduzindo a utilização de portas SNAT. Além disso, quando o firewall é dimensionado por diferentes motivos (por exemplo, CPU ou taxa de transferência), portas SNAT adicionais também ficam disponíveis. De modo eficaz, um determinado percentual de utilização de portas SNAT pode ficar inativo sem a adição de endereços IP públicos, apenas porque o serviço foi expandido. Você pode controlar diretamente o número de endereços IP públicos disponíveis para aumentar as portas disponíveis no firewall. Mas, você não pode controlar diretamente o dimensionamento de firewall.


## <a name="next-steps"></a>Próximas etapas

- Para saber como monitorar as métricas e logs do Firewall do Azure, veja [Tutorial: Monitorar os logs de Firewall do Azure](./firewall-diagnostics.md).

- Para saber mais sobre as métricas em Azure Monitor, consulte [métricas em Azure monitor](../azure-monitor/essentials/data-platform-metrics.md).