---
title: Reagir aos eventos do serviço de Signaler do Azure
description: Use a grade de eventos do Azure para assinar eventos do serviço Signaler do Azure. Outros serviços downstream podem ser disparados por esses eventos.
services: azure-signalr,event-grid
author: chenyl
ms.author: chenyl
ms.reviewer: zhshang
ms.date: 11/13/2019
ms.topic: conceptual
ms.service: signalr
ms.openlocfilehash: 77c8887ac19c6ce4c7d83734bdd2b44d9213914d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92151107"
---
# <a name="reacting-to-azure-signalr-service-events"></a>Reagindo aos eventos do Serviço do Azure SignalR

Os eventos do serviço de Signaler do Azure permitem que os aplicativos reajam a conexões de cliente conectadas ou desconectadas usando arquiteturas modernas sem servidor. Isso é feito sem a necessidade de código complicado ou serviços de sondagem caros e ineficientes.  Em vez disso, os eventos são enviados por Push por meio da [grade de eventos do Azure](https://azure.microsoft.com/services/event-grid/) para assinantes como [Azure Functions](https://azure.microsoft.com/services/functions/), [aplicativos lógicos do Azure](https://azure.microsoft.com/services/logic-apps/)ou até mesmo para seu próprio ouvinte HTTP personalizado. Com o Azure Signalr, você paga apenas pelo que consumir.

Os eventos do serviço de Signaler do Azure são enviados de forma confiável para o serviço de grade de eventos, que fornece serviços de entrega confiáveis para seus aplicativos por meio de políticas de repetição avançadas e entrega de mensagens mortas. Para saber mais, confira [entrega e repetição de mensagem da grade de eventos](../event-grid/delivery-and-retry.md).

![Modelo da Grade de Eventos](/azure/event-grid/media/overview/functional-model.png)

## <a name="serverless-state"></a>Estado sem servidor
Os eventos do serviço de Signaler do Azure estão ativos apenas quando as conexões do cliente estão em um estado sem servidor. Se um cliente não rotear para um servidor de Hub, ele entrará no estado sem servidor. O modo clássico só funciona quando o Hub ao qual as conexões de cliente se conectam não tem um servidor de Hub. O modo sem servidor é recomendado como uma prática recomendada. Para saber mais detalhes sobre o modo de serviço, consulte [como escolher o modo de serviço](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).

## <a name="available-azure-signalr-service-events"></a>Eventos do serviço de Signaler do Azure disponíveis
A Grade de eventos usa [assinaturas de evento](../event-grid/concepts.md#event-subscriptions) para rotear mensagens de evento para os assinantes. As assinaturas de evento do serviço de Signaler do Azure dão suporte a dois tipos de eventos:  

|Nome do evento|Descrição|
|----------|-----------|
|`Microsoft.SignalRService.ClientConnectionConnected`|Gerado quando uma conexão de cliente é conectada.|
|`Microsoft.SignalRService.ClientConnectionDisconnected`|Gerado quando uma conexão de cliente é desconectada.|

## <a name="event-schema"></a>Esquema do evento
Os eventos do serviço de Signaler do Azure contêm todas as informações necessárias para responder às alterações em seus dados. Você pode identificar um evento do serviço de Signaler do Azure com a propriedade eventType começa com "Microsoft. SignalRService". Informações adicionais sobre o uso de propriedades de evento da grade de eventos estão documentadas no [esquema de eventos da grade de eventos](../event-grid/event-schema.md).  

Aqui está um exemplo de um evento conectado de conexão de cliente:
```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/signalr-rg/providers/Microsoft.SignalRService/SignalR/signalr-resource",
  "subject": "/hub/chat",
  "eventType": "Microsoft.SignalRService.ClientConnectionConnected",
  "eventTime": "2019-06-10T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "timestamp": "2019-06-10T18:41:00.9584103Z",
    "hubName": "chat",
    "connectionId": "crH0uxVSvP61p5wkFY1x1A",
    "userId": "user-eymwyo23"
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

Para obter mais informações, consulte [esquema de eventos do serviço signalr](../event-grid/event-schema-azure-signalr.md).

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a grade de eventos e dê uma tentativa aos eventos do serviço Signaler do Azure:

> [!div class="nextstepaction"]
> [Experimente uma integração da grade de eventos de exemplo com o serviço](./signalr-howto-event-grid-integration.md) 
>  de Signaler do Azure [Sobre a grade de eventos](../event-grid/overview.md)