---
title: Tópicos e filas do Barramento de Serviço como manipuladores de eventos para eventos da Grade de Eventos do Azure
description: Descreve como você pode usar tópicos e filas do Barramento de Serviço como manipuladores de eventos para eventos da Grade de Eventos do Azure.
ms.topic: conceptual
ms.date: 09/03/2020
ms.openlocfilehash: 12b72420e3475b46a4cd61ce5032b478af740dde
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97399852"
---
# <a name="service-bus-queues-and-topics-as-event-handlers-for-azure-event-grid-events"></a>Tópicos e filas do Barramento de Serviço como manipuladores de eventos para eventos da Grade de Eventos do Azure
Um manipulador de eventos é o local para o qual o evento é enviado. O manipulador usa alguma ação adicional para processar o evento. Vários serviços do Azure são automaticamente configurados para lidar com os eventos, e o **Barramento de Serviço do Azure** é um deles. 

Você pode usar uma fila ou um tópico de serviço como um manipulador de eventos para eventos da Grade de Eventos. 

## <a name="service-bus-queues"></a>Filas do Barramento de Serviço
Você pode encaminhar eventos na Grade de Eventos diretamente para filas do Barramento de Serviço para uso em buffer ou cenários de comando e controle em aplicativos empresariais.

No portal do Azure, ao criar uma assinatura de evento, selecione a **Fila do Barramento de Serviço** como o tipo de ponto de extremidade e clique em **Selecionar um ponto de extremidade** para escolher uma fila do Barramento de Serviço.

### <a name="using-cli-to-add-a-service-bus-queue-handler"></a>Como usar a CLI para adicionar um manipulador de fila do Barramento de Serviço

Para a CLI do Azure, o seguinte exemplo assina e conecta um tópico da grade de eventos a uma fila do Barramento de Serviço:

```azurecli-interactive
az eventgrid event-subscription create \
    --name <my-event-subscription> \
    --source-resource-id /subscriptions/{SubID}/resourceGroups/{RG}/providers/Microsoft.EventGrid/topics/topic1 \
    --endpoint-type servicebusqueue \
    --endpoint /subscriptions/{SubID}/resourceGroups/TestRG/providers/Microsoft.ServiceBus/namespaces/ns1/queues/queue1
```

## <a name="service-bus-topics"></a>Tópicos do Barramento de Serviço

Você pode encaminhar eventos na Grade de Eventos diretamente para tópicos do Barramento de Serviço para manipular eventos do sistema do Azure com tópicos de Barramento de Serviço ou para cenários de mensagens de comando e controle.

No portal do Azure, ao criar uma assinatura de evento, selecione o **Tópico do Barramento de Serviço** como o tipo de ponto de extremidade e clique em **Selecionar um ponto de extremidade** para escolher um tópico do Barramento de Serviço.

### <a name="using-cli-to-add-a-service-bus-topic-handler"></a>Como usar a CLI para adicionar um manipulador de tópico do Barramento de Serviço

Por CLI do Azure, o exemplo a seguir assina e conecta um tópico da grade de eventos a um tópico do barramento de serviço:

```azurecli-interactive
az eventgrid event-subscription create \
    --name <my-event-subscription> \
    --source-resource-id /subscriptions/{SubID}/resourceGroups/{RG}/providers/Microsoft.EventGrid/topics/topic1 \
    --endpoint-type servicebustopic \
    --endpoint /subscriptions/{SubID}/resourceGroups/TestRG/providers/Microsoft.ServiceBus/namespaces/ns1/topics/topic1
```

[!INCLUDE [event-grid-message-headers](../../includes/event-grid-message-headers.md)]

Ao enviar um evento para uma fila ou um tópico do barramento de serviço como uma mensagem orientada, o `messageid` da mensagem orientada é uma ID interna do sistema.

A ID do sistema interno da mensagem será mantida na entrega do evento para que você possa evitar entregas duplicadas ativando a detecção de **duplicidades** na entidade do barramento de serviço. Recomendamos que você habilite a duração da detecção de duplicidades na entidade do Barramento de Serviço como TTL (tempo de vida) do evento ou duração máxima de repetição, o que for maior.

## <a name="rest-examples-for-put"></a>Exemplos de REST (para PUT)

### <a name="service-bus-queue"></a>Fila do Barramento de Serviço

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "ServiceBusQueue",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.ServiceBus/namespaces/<SERVICE BUS NAMESPACE NAME>/queues/<SERVICE BUS QUEUE NAME>"
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

### <a name="service-bus-queue---delivery-with-managed-identity"></a>Fila do Barramento de Serviço – entrega com identidade gerenciada

```json
{
    "properties": {
        "deliveryWithResourceIdentity": 
        {
            "identity": 
            {
                "type": "SystemAssigned"
            },
            "destination": 
            {
                "endpointType": "ServiceBusQueue",
                "properties": 
                {
                    "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.ServiceBus/namespaces/<SERVICE BUS NAMESPACE NAME>/queues/<SERVICE BUS QUEUE NAME>"
                }
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

### <a name="service-bus-topic"></a>Tópico do Barramento de Serviço

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "ServiceBusTopic",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.ServiceBus/namespaces/<SERVICE BUS NAMESPACE NAME>/topics/<SERVICE BUS TOPIC NAME>"
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

### <a name="service-bus-topic---delivery-with-managed-identity"></a>Tópico do Barramento de Serviço – entrega com identidade gerenciada

```json
{
    "properties": 
    {
        "deliveryWithResourceIdentity": 
        {
            "identity": 
            {
                "type": "SystemAssigned"
            },
            "destination": 
            {
                "endpointType": "ServiceBusTopic",
                "properties": 
                {
                    "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.ServiceBus/namespaces/<SERVICE BUS NAMESPACE NAME>/topics/<SERVICE BUS TOPIC NAME>"
                }
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

## <a name="next-steps"></a>Próximas etapas
Consulte o artigo [Manipuladores de eventos](event-handlers.md) para obter uma lista dos manipuladores de eventos compatíveis. 
