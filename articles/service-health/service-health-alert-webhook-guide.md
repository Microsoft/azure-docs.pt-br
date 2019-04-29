---
title: Configurar notificações de integridade para sistemas de gerenciamento de problemas existentes usando um webhook | Microsoft Docs
description: Obtenha notificações personalizadas sobre eventos de integridade do serviço para seu sistema de gerenciamento de problemas existente.
author: stephbaron
ms.author: stbaron
ms.topic: conceptual
ms.service: service-health
ms.workload: Supportability
ms.date: 3/27/2018
ms.openlocfilehash: 69b142cd46c006e562218c949fb450864589a661
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60622151"
---
# <a name="configure-health-notifications-for-existing-problem-management-systems-using-a-webhook"></a>Configurar notificações de integridade para sistemas de gerenciamento de problemas existentes usando um webhook

Este artigo mostra como configurar os alertas de integridade do serviço para enviar dados por meio de Webhooks para seu sistema de notificação existente.

Atualmente, você pode configurar alertas de integridade do serviço para que, quando um Incidente de Serviço do Azure afetá-lo, você seja notificado por email ou mensagem de texto.
No entanto, talvez você já tenha sistema de notificação externa existente que deseja usar.
Este documento mostra as partes mais importantes do conteúdo do webhook e como você pode criar alertas personalizados para ser notificado quando problemas de serviço o afetarem.

Caso deseje usar uma integração pré-configurada, saiba como:
* [Configurar alertas com o ServiceNow](service-health-alert-webhook-servicenow.md)
* [Configurar alertas com o PagerDuty](service-health-alert-webhook-pagerduty.md)
* [Configurar alertas com o OpsGenie](service-health-alert-webhook-opsgenie.md)

## <a name="configuring-a-custom-notification-using-the-service-health-webhook-payload"></a>Configurar uma notificação personalizada usando o conteúdo do webhook de integridade do serviço
Caso deseje configurar sua própria integração de webhook personalizada, você precisa analisar o conteúdo do JSON que é enviado durante as notificações de integridade do serviço.

Acesse [aqui para ver um exemplo](../azure-monitor/platform/activity-log-alerts-webhook.md) da aparência do conteúdo do webhook `ServiceHealth`.

Detecte se esse é um alerta de integridade do serviço examinando `context.eventSource == "ServiceHealth"`. Nesse local, as propriedades que são mais relevantes para ingestão são:
 * `data.context.activityLog.status`
 * `data.context.activityLog.level`
 * `data.context.activityLog.subscriptionId`
 * `data.context.activityLog.properties.title`
 * `data.context.activityLog.properties.impactStartTime`
 * `data.context.activityLog.properties.communication`
 * `data.context.activityLog.properties.impactedServices`
 * `data.context.activityLog.properties.trackingId`

## <a name="creating-a-direct-link-to-the-service-health-dashboard-for-an-incident"></a>Criando um link direto ao painel de integridade do serviço do Azure para um incidente
Crie um link direto ao painel de Integridade do Serviço na área de trabalho ou no dispositivo móvel gerando uma URL especializada. Use a `trackingId`, bem como o primeiro e os últimos três dígitos de sua `subscriptionId`, para formar:
```
https://app.azure.com/h/<trackingId>/<first and last three digits of subscriptionId>
```

Por exemplo, se sua `subscriptionId` for `bba14129-e895-429b-8809-278e836ecdb3` e sua `trackingId` for `0DET-URB`, a URL de Integridade do Serviço será:

```
https://app.azure.com/h/0DET-URB/bbadb3
```

## <a name="using-the-level-to-detect-the-severity-of-the-issue"></a>Usando o nível para detectar a gravidade do problema
Da gravidade mais baixa até a mais alta, a propriedade `level` no conteúdo pode ser `Informational`, `Warning`, `Error` e `Critical`.

## <a name="parsing-the-impacted-services-to-understand-the-full-scope-of-the-incident"></a>Analisando os serviços afetados para entender o escopo completo do incidente
Os alertas de integridade do serviço podem informá-lo sobre problemas em várias Regiões e serviços. Para obter os detalhes completos, você precisa analisar o valor de `impactedServices`.
O conteúdo é uma cadeia de caracteres [JSON com escape](https://json.org/); quando sem escape, contém outro objeto JSON que pode ser analisado normalmente.

```json
{"data.context.activityLog.properties.impactedServices": "[{\"ImpactedRegions\":[{\"RegionName\":\"Australia East\"},{\"RegionName\":\"Australia Southeast\"}],\"ServiceName\":\"Alerts & Metrics\"},{\"ImpactedRegions\":[{\"RegionName\":\"Australia Southeast\"}],\"ServiceName\":\"App Service\"}]"}
```

Torna-se:

```json
[
   {
      "ImpactedRegions":[
         {
            "RegionName":"Australia East"
         },
         {
            "RegionName":"Australia Southeast"
         }
      ],
      "ServiceName":"Alerts & Metrics"
   },
   {
      "ImpactedRegions":[
         {
            "RegionName":"Australia Southeast"
         }
      ],
      "ServiceName":"App Service"
   }
]
```

Isso mostra que há problemas com “Alertas e Métricas” no Leste e Sudeste da Austrália, bem como problemas com o “Serviço de Aplicativo” no Sudeste da Austrália.


## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>Testando a integração de webhook por meio de uma solicitação HTTP POST
1. Crie o conteúdo de integridade do serviço que você deseja enviar. Encontre um conteúdo de webhook de integridade do serviço de exemplo em [Webhooks para alertas do log de atividades do Azure](../azure-monitor/platform/activity-log-alerts-webhook.md).

2. Crie uma solicitação HTTP POST, da seguinte maneira:

    ```
    POST        https://your.webhook.endpoint

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
3. Você deverá receber uma resposta `2XX - Successful`.

4. Acesse o [PagerDuty](https://www.pagerduty.com/) para confirmar se a integração foi configurada com êxito.

## <a name="next-steps"></a>Próximas etapas
- Examine o [esquema do webhook de alertas de log de atividades](../azure-monitor/platform/activity-log-alerts-webhook.md). 
- Saiba mais sobre as [notificações de integridade do serviço](../azure-monitor/platform/service-notifications.md).
- Saiba mais sobre [grupos de ação](../azure-monitor/platform/action-groups.md).