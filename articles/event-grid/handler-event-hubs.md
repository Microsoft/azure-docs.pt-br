---
title: Hub de eventos como um manipulador de eventos para os eventos da Grade de Eventos do Azure
description: Descreve como você pode usar os hubs de eventos como manipuladores de eventos para os eventos da Grade de Eventos do Azure.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 446fef6df65f59206519e282c74d59c2ed1bfa9d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96005615"
---
# <a name="event-hub-as-an-event-handler-for-azure-event-grid-events"></a>Hub de eventos como um manipulador de eventos para os eventos da Grade de Eventos do Azure
Um manipulador de eventos é o local para o qual o evento é enviado. O manipulador usa uma ação para processar o evento. Vários serviços do Azure são automaticamente configurados para lidar com os eventos, e o **Hubs de Eventos do Azure** é um deles. 

Use o **Hubs de Eventos** quando sua solução receber eventos da Grade de Eventos mais rápido do que é capaz de processar. Quando os eventos estiverem em um hub de eventos, seu aplicativo poderá processar a partir do hub de eventos em seu próprio agendamento. Você pode dimensionar o processamento dos eventos para manipular os eventos de entrada.

## <a name="tutorials"></a>Tutoriais
Veja os exemplos a seguir: 

|Title  |Descrição  |
|---------|---------|
| [Início Rápido: Encaminhar eventos personalizados para os Hubs de Eventos do Azure com a CLI do Azure](custom-event-to-eventhub.md) | Envia um evento personalizado para um hub de eventos para processamento por um aplicativo. |
| [Modelo do Resource Manager: Criar um tópico personalizado de Grade de Eventos e enviar eventos para um hub de eventos](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| Um modelo do Gerenciador de Recursos que cria uma assinatura para um tópico personalizado. Envia eventos para os Hubs de Eventos do Azure. |

[!INCLUDE [event-grid-message-headers](../../includes/event-grid-message-headers.md)]


## <a name="rest-examples-for-put"></a>Exemplos de REST (para PUT)


### <a name="event-hub"></a>Hub de Eventos

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "EventHub",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUBS NAMESPACE NAME>/eventhubs/<EVENT HUB NAME>"
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

### <a name="event-hub---delivery-with-managed-identity"></a>Hub de eventos – entrega com identidade gerenciada

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
                "endpointType": "EventHub",
                "properties": 
                {
                    "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUBS NAMESPACE NAME>/eventhubs/<EVENT HUB NAME>"
                }
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

## <a name="next-steps"></a>Próximas etapas
Consulte o artigo [Manipuladores de eventos](event-handlers.md) para obter uma lista dos manipuladores de eventos compatíveis. 
