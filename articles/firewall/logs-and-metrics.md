---
title: Visão geral dos logs do Firewall do Azure
description: Este artigo é uma visão geral dos logs de diagnóstico do Firewall do Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 9/24/2018
ms.author: victorh
ms.openlocfilehash: c129c394f3d694b832722287027c1f9e58028a33
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61065845"
---
# <a name="azure-firewall-logs"></a>Logs do Firewall do Azure

Você pode monitorar o Firewall do Azure usando os logs de firewall. Você também pode usar os logs de atividades para auditar operações nos recursos do Firewall do Azure.

Você pode acessar alguns desses logs por meio do portal. Os logs podem ser enviados para os [logs do Azure Monitor](../azure-monitor/insights/azure-networking-analytics.md), o Armazenamento e os Hubs de Eventos e analisados nos logs do Azure Monitor ou por ferramentas diferentes, como Excel e Power BI.

## <a name="diagnostic-logs"></a>Logs de diagnóstico

 Os seguintes logs de diagnóstico estão disponíveis para o Firewall do Azure:

* **Log de regra de aplicativo**

   O log de regra de aplicativo é salvo em uma conta de armazenamento, transmitidos para hubs de eventos e/ou enviados para os logs do Azure Monitor somente se você tiver habilitado ele para cada Firewall do Azure. Cada nova conexão que corresponda a uma de suas regras de aplicativo configurado gera um log para a conexão aceita/negada. Os dados são registrados no formato JSON, conforme mostrado no seguinte exemplo:

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

* **Log de regra de rede**

   O log de regra de rede é salvo em uma conta de armazenamento, transmitidos para hubs de eventos e/ou enviados para os logs do Azure Monitor somente se você tiver habilitado ele para cada Firewall do Azure. Cada nova conexão que corresponda a uma de suas regras de rede configuradas gera um log para a conexão aceita/negada. Os dados são registrados no formato JSON, conforme mostrado no seguinte exemplo:

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

Você tem três opções para armazenar os logs:

* **Conta de armazenamento**: As contas de armazenamento são mais adequadas para os logs quando eles são armazenados por mais tempo e examinados quando necessário.
* **Hubs de Evento**: Os hubs de eventos são uma ótima opção para integração a outras ferramentas SEIM (informações de segurança e gerenciamento de evento) para receber alertas sobre os recursos.
* **Logs do Azure Monitor**: Os logs do Azure Monitor são mais adequados para o monitoramento geral em tempo real do aplicativo ou para a observação de tendências.

## <a name="activity-logs"></a>Logs de atividade

   As entradas do log de atividades são coletadas por padrão e podem ser exibidas no portal do Azure.

   Você pode usar os [logs de atividades do Azure](../azure-resource-manager/resource-group-audit.md) (anteriormente conhecidos como logs operacionais e logs de auditoria) para exibir todas as operações que estão sendo enviadas à sua assinatura do Azure.


## <a name="next-steps"></a>Próximas etapas

Para saber como monitorar as métricas e logs de Firewall do Azure, consulte [Tutorial: Monitorar os logs do Firewall do Azure](tutorial-diagnostics.md).