---
title: Logs de diagnóstico do Barramento de Serviço do Azure | Microsoft Docs
description: Saiba como configurar logs de diagnóstico para o Barramento de Serviço no Azure.
keywords: ''
documentationcenter: .net
services: service-bus-messaging
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 7d4cb8e55c5d1561c09cf85122550a66e3671f17
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60714094"
---
# <a name="service-bus-diagnostic-logs"></a>Logs de diagnóstico do Barramento de Serviço

É possível exibir dois tipos de logs para o Barramento de Serviço do Azure:
* **[Logs de atividades](../azure-monitor/platform/activity-logs-overview.md)**. Esses logs contém informações sobre as operações executadas em um trabalho. Os logs estão sempre habilitados.
* **[Logs de diagnóstico](../azure-monitor/platform/diagnostic-logs-overview.md)**. É possível configurar logs de diagnóstico para obter informações mais detalhadas sobre tudo o que acontece em um trabalho. Os logs de diagnóstico abrangem atividades desde o momento em que o trabalho é criado até sua exclusão, incluindo atualizações e atividades que ocorrem durante a execução do trabalho.

## <a name="turn-on-diagnostic-logs"></a>Ativar logs de diagnóstico

Os logs de diagnóstico estão desabilitados por padrão. Para habilitar os logs de diagnóstico, realize as seguintes etapas:

1.  No [Portal do Azure](https://portal.azure.com), em **Monitoramento + Gerenciamento**, clique em **Logs de diagnóstico**.

    ![navegação de folha para logs de diagnóstico](./media/service-bus-diagnostic-logs/image1.png)

2. Clique no recurso que você deseja monitorar.  

3.  Clique em **Ativar diagnóstico**.

    ![ativar logs de diagnóstico](./media/service-bus-diagnostic-logs/image2.png)

4.  Para **Status**, clique em **Ativar**.

    ![alterar logs de diagnóstico de status](./media/service-bus-diagnostic-logs/image3.png)

5.  Definir o destino de arquivamento desejado; Por exemplo, uma conta de armazenamento, um hub de eventos ou do Azure Monitor registra em log.

6.  Salve as novas configurações de diagnóstico.

As novas configurações terão efeito em aproximadamente 10 minutos. Depois disso, os logs aparecerão no destino de arquivamento configurado, na folha **Logs de diagnóstico**.

Para saber mais sobre como configurar um diagnóstico, confira a [visão geral dos logs de diagnóstico do Azure](../azure-monitor/platform/diagnostic-logs-overview.md).

## <a name="diagnostic-logs-schema"></a>Esquema de logs de diagnóstico

Todos os logs são armazenados no formato JSON (JavaScript Object Notation). Cada entrada tem campos de cadeia de caracteres que usam o formato descrito na seção a seguir.

## <a name="operational-logs-schema"></a>Esquema de logs operacionais

Faz logon na categoria **OperationalLogs** para capturar o que acontece durante a operação do Barramento de Serviço. Especificamente, esses logs capturam o tipo de operação, incluindo a criação da fila, os recursos usados e o status da operação.

As cadeias de caracteres JSON do log operacional incluem os elementos listados na seguinte tabela:

Nome | Descrição
------- | -------
ActivityId | ID interna, usada para acompanhamento
EventName | Nome da operação           
resourceId | ID de recurso do Azure Resource Manager
SubscriptionId | ID da assinatura
EventTimeString | Tempo de operação
EventProperties | Propriedades da operação
Status | Status da operação
Chamador | Chamador da operação (portal do Azure ou cliente de gerenciamento)
category | OperationalLogs

Este é um exemplo de uma cadeia de caracteres JSON do log operacional:

```json
{
  "ActivityId": "6aa994ac-b56e-4292-8448-0767a5657cc7",
  "EventName": "Create Queue",
  "resourceId": "/SUBSCRIPTIONS/1A2109E3-9DA0-455B-B937-E35E36C1163C/RESOURCEGROUPS/DEFAULT-SERVICEBUS-CENTRALUS/PROVIDERS/MICROSOFT.SERVICEBUS/NAMESPACES/SHOEBOXEHNS-CY4001",
  "SubscriptionId": "1a2109e3-9da0-455b-b937-e35e36c1163c",
  "EventTimeString": "9/28/2016 8:40:06 PM +00:00",
  "EventProperties": "{\"SubscriptionId\":\"1a2109e3-9da0-455b-b937-e35e36c1163c\",\"Namespace\":\"shoeboxehns-cy4001\",\"Via\":\"https://shoeboxehns-cy4001.servicebus.windows.net/f8096791adb448579ee83d30e006a13e/?api-version=2016-07\",\"TrackingId\":\"5ee74c9e-72b5-4e98-97c4-08a62e56e221_G1\"}",
  "Status": "Succeeded",
  "Caller": "ServiceBus Client",
  "category": "OperationalLogs"
}
```

## <a name="next-steps"></a>Próximas etapas

Consulte os seguintes links para saber mais sobre o Barramento de Serviço:

* [Introdução ao Barramento de Serviço](service-bus-messaging-overview.md)
* [Introdução ao Barramento de Serviço](service-bus-dotnet-get-started-with-queues.md)
