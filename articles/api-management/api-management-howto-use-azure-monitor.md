---
title: Monitorar o Gerenciamento de API com o Azure Monitor| Microsoft Docs
description: "Saiba como monitorar o serviço de Gerenciamento de API do Azure usando o Azure Monitor."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 2fa193cd-ea71-4b33-a5ca-1f55e5351e23
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: apimpm
ms.openlocfilehash: 717e033aa4bbd4dd8ebcc727c3f551aee81770dc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-api-management-with-azure-monitor"></a>Monitorar o Gerenciamento de API com o Azure Monitor
O Azure Monitor é um serviço do Azure que fornece uma única fonte para monitorar todos os recursos do Azure. Com o Azure Monitor, é possível visualizar, consultar, rotear, arquivar e executar ações nas métricas e nos logs provenientes dos recursos do Azure como o Gerenciamento de API. 

O vídeo a seguir mostra como monitorar o Gerenciamento de API usando o Azure Monitor. Para obter mais informações sobre o Azure Monitor, consulte [Introdução ao Azure Monitor]. 


> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Monitor-API-Management-with-Azure-Monitor/player]
>
>
 
## <a name="metrics"></a>Métricas
No momento, o Gerenciamento de API emite cinco métricas e estamos planejando adicionar mais no futuro. Essas métricas são emitidas a cada minuto, fornecendo uma visibilidade quase em tempo real do estado e da integridade de suas APIs. A seguir, há um resumo das métricas:
* Total de solicitações de gateway: o número de solicitações de API no período. 
* Solicitações de gateway bem-sucedidas: o número de solicitações de API que recebeu códigos de resposta HTTP bem-sucedidos, incluindo 304, 307 e unidades menores que 301 (por exemplo, 200). 
* Solicitações de gateway com falha: o número de solicitações de API que recebeu códigos de resposta HTTP errôneos, incluindo 400 e qualquer coisa maior do que 500.
* Solicitações de gateway não autorizadas: o número de solicitações de API que recebeu códigos de resposta HTTP, incluindo 401, 403 e 429. 
* Outras solicitações de gateway: o número de solicitações de API que recebeu códigos de resposta HTTP que não pertencem a nenhuma das categorias acima (por exemplo, 418).

É possível acessar as métricas em seu serviço de Gerenciamento de API ou acessar as métricas de todos os seus recursos do Azure no Azure Monitor. Para exibir as métricas em seu serviço de Gerenciamento de API:
1. Abra o portal do Azure.
2. Acesse seu serviço de Gerenciamento de API.
3. Clique em **Métricas**.

![Folha de métricas][metrics-blade]

Para obter mais informações sobre como usar as métricas, consulte [Overview of Metrics (Visão geral das Métricas)].

## <a name="activity-logs"></a>Logs de atividade
Os logs de atividades fornecem informações sobre as operações que foram realizadas em seus serviços de Gerenciamento de API. Anteriormente eles eram conhecidos como "logs de auditoria" ou "logs operacionais". Usando logs de atividades, é possível determinar “o que, quem e quando” para quaisquer operações de gravação (PUT, POST, DELETE) realizadas em seus serviços de Gerenciamento de API. 

> [!NOTE]
> Os logs de atividades não incluem operações de leitura (GET) ou operações realizadas no Portal do Publicador clássico ou usando as APIs de gerenciamento original.

É possível acessar os logs de atividades em seu serviço de Gerenciamento de API ou acessar logs de todos os seus recursos do Azure no Azure Monitor. Para exibir os logs de atividades em seu serviço de Gerenciamento de API:
1. Abra o portal do Azure.
2. Acesse seu serviço de Gerenciamento de API.
3. Clique em **Log de atividades**.

![Folha Logs de atividade][activity-logs-blade]

Para obter mais informações sobre como usar métricas, consulte [Overview of Activity Logs (Visão geral dos Logs de Atividades)].

## <a name="alerts"></a>Alertas
É possível configurar para receber alertas com base em métricas e logs de atividades. O Azure Monitor permite configurar um alerta para que ele faça o seguinte quando for acionado:

* Enviar uma notificação por email
* Chamar um webhook
* Invocar um aplicativo lógico do Azure

É possível configurar regras de alerta em seu serviço de Gerenciamento de API ou no Azure Monitor. Para configurá-los no Gerenciamento de API: 
1. Abra o portal do Azure.
2. Acesse seu serviço de Gerenciamento de API.
3. Clique em **Regras de alerta**.

![Folha Regras de alerta][alert-rules-blade]

Para obter mais informações sobre o uso de alertas, consulte [Overview of Alerts (Visão geral dos Alertas)].

## <a name="diagnostic-logs"></a>Logs de Diagnóstico
Os logs de diagnóstico fornecem informações avançadas sobre operações e erros importantes para auditoria, bem como para fins de solução de problemas. Os logs de diagnóstico são diferentes dos logs de atividades. Os logs de atividades fornecem informações sobre as operações realizadas em seus recursos do Azure. Os Logs de Diagnóstico fornecem informações em operações que o recurso realizou por conta própria.

No momento, o Gerenciamento de API oferece logs de diagnóstico (agrupados por hora) sobre a solicitação de API individual em que cada entrada que tem a seguinte estrutura:

```
{
    "Tenant": "",
      "DeploymentName": "",
      "time": "",
      "resourceId": "",
      "category": "GatewayLogs",
      "operationName": "Microsoft.ApiManagement/GatewayLogs",
      "durationMs": ,
      "Level": ,
      "properties": "{
          "ApiId": "",
          "OperationId": "",
          "ProductId": "",
          "SubscriptionId": "",
          "Method": "",
          "Url": "",
          "RequestSize": ,
          "ServiceTime": "",
          "BackendMethod": "",
          "BackendUrl": "",
          "BackendResponseCode": ,
          "ResponseCode": ,
          "ResponseSize": ,
          "Cache": "",
          "UserId"
      }"
 }
```

É possível acessar os logs de diagnóstico em seu serviço de Gerenciamento de API ou acessar logs de todos os seus recursos do Azure no Azure Monitor. Para exibir os logs de diagnóstico em seu serviço de Gerenciamento de API:
1. Abra o portal do Azure.
2. Acesse seu serviço de Gerenciamento de API.
3. Clique em **Log de diagnóstico**.

![Folha Logs de Diagnóstico][diagnostic-logs-blade]

Para obter mais informações sobre como usar métricas, consulte [Overview of Diagnostic Logs (Visão geral dos Logs de Diagnóstico)].

## <a name="next-step"></a>Próxima etapa

* [Introdução ao Azure Monitor]
* [Overview of Metrics (Visão geral das Métricas)]
* [Overview of Activity Logs (Visão geral dos Logs de Atividades)]
* [Overview of Diagnostic Logs (Visão geral dos Logs de Diagnóstico)]
* [Overview of Alerts (Visão geral dos Alertas)]

[Introdução ao Azure Monitor]: ../monitoring-and-diagnostics/monitoring-get-started.md
[Overview of Metrics (Visão geral das Métricas)]: ../monitoring-and-diagnostics/monitoring-overview-metrics.md
[Overview of Activity Logs (Visão geral dos Logs de Atividades)]: ../monitoring-and-diagnostics/monitoring-overview-activity-logs.md
[Overview of Diagnostic Logs (Visão geral dos Logs de Diagnóstico)]: ../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md
[Overview of Alerts (Visão geral dos Alertas)]: ../monitoring-and-diagnostics/insights-alerts-portal.md



[metrics-blade]: ./media/api-management-azure-monitor/api-management-metrics-blade.png
[activity-logs-blade]: ./media/api-management-azure-monitor/api-management-activity-logs-blade.png
[alert-rules-blade]: ./media/api-management-azure-monitor/api-management-alert-rules-blade.png
[diagnostic-logs-blade]: ./media/api-management-azure-monitor/api-management-diagnostic-logs-blade.png
