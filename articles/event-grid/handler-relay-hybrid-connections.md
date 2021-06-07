---
title: Conexão híbrida de retransmissão como um manipulador de eventos para eventos da Grade de Eventos do Azure
description: Descreve como você pode usar as conexões híbridas da Retransmissão do Azure como manipuladores de eventos para os eventos da Grade de Eventos do Azure.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 38233a2e103600f07837ce9a1ad8d63fe7e4fb99
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91270195"
---
# <a name="relay-hybrid-connection-as-an-event-handler-for-azure-event-grid-events"></a>Conexão híbrida de retransmissão como um manipulador de eventos para eventos da Grade de Eventos do Azure
Um manipulador de eventos é o local para o qual o evento é enviado. O manipulador usa alguma ação adicional para processar o evento. Vários serviços do Azure são automaticamente configurados para lidar com os eventos, e a **Retransmissão do Azure** é um deles. 

Use as **Conexões Híbridas de Retransmissão** do Azure para enviar eventos para aplicativos que estão em uma rede corporativa e não têm um ponto de extremidade publicamente acessível.

## <a name="tutorials"></a>Tutoriais
Consulte o tutorial a seguir para ter um exemplo de uso da conexão híbrida da Retransmissão do Azure como um manipulador de eventos. 

|Title  |Descrição  |
|---------|---------|
| [Tutorial: enviar eventos para conexão híbrida](custom-event-to-hybrid-connection.md) | Envia um evento personalizado para uma conexão híbrida existente para processamento por um aplicativo de escuta. |

## <a name="rest-example-for-put"></a>Exemplo de REST (para PUT)

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "HybridConnection",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Relay/namespaces/<RELAY NAMESPACE NAME>/hybridconnections/<HYBRID CONNECTION NAME>"
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

## <a name="next-steps"></a>Próximas etapas
Consulte o artigo [Manipuladores de eventos](event-handlers.md) para obter uma lista dos manipuladores de eventos compatíveis. 