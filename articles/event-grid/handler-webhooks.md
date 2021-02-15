---
title: Webhooks como manipuladores de eventos para eventos da Grade de Eventos do Azure
description: Descreve como você pode usar os webhooks como manipuladores de eventos para os eventos da Grade de Eventos do Azure. Os runbooks e os aplicativos lógicos da Automação do Azure têm suporte como manipuladores de eventos por meio de webhooks.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: aa20c75789a18b93f787134dffe165e60ff1ab6f
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91875794"
---
# <a name="webhooks-automation-runbooks-logic-apps-as-event-handlers-for-azure-event-grid-events"></a>WebHooks, runbooks de Automação, Aplicativos Lógicos como manipuladores de eventos para eventos da Grade de Eventos do Azure
Um manipulador de eventos é o local para o qual o evento é enviado. O manipulador usa alguma ação adicional para processar o evento. Vários serviços do Azure são automaticamente configurados para manipular eventos. Você também pode usar qualquer WebHook para manipular eventos. O WebHook não precisa ser hospedado no Azure para manipular eventos. A Grade de Eventos dá suporte apenas a pontos de extremidade do Webhook HTTPS.

> [!NOTE]
> - Os runbooks e os aplicativos lógicos da Automação do Azure têm suporte como manipuladores de eventos por meio de webhooks. 
> - Embora você possa usar **webhook** como um **tipo de ponto de extremidade** para configurar uma função do Azure como um manipulador de eventos, use o **Azure function** como um tipo de ponto de extremidade. Para obter mais informações, consulte [Azure function como um manipulador de eventos](handler-functions.md).

## <a name="webhooks"></a>Webhooks
Consulte os artigos a seguir para obter uma visão geral e exemplos de como usar webhooks como manipuladores de eventos. 

|Title  |Descrição  |
|---------|---------|
| Início Rápido: criar e encaminhar eventos personalizados com - [CLI do Azure](custom-event-quickstart.md), [PowerShell](custom-event-quickstart-powershell.md), e [portal](custom-event-quickstart-portal.md). | Mostra como enviar eventos personalizados para um WebHook. |
| Início Rápido: encaminhe eventos de armazenamento de Blob para um ponto de extremidade com - [CLI do Azure](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json), [PowerShell](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json) e [portal](blob-event-quickstart-portal.md). | Mostra como enviar eventos de armazenamento de blob para um WebHook. |
| [Início Rápido: enviar eventos de registro de contêiner](../container-registry/container-registry-event-grid-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Mostra como usar a CLI do Azure para enviar eventos de Registro de Contêiner. |
| [Visão geral: receber eventos em um ponto de extremidade HTTP](receive-events.md) | Descreve como validar um ponto de extremidade HTTP para receber eventos de uma Assinatura de Evento e depois receber e desserializar os eventos. |


## <a name="azure-automation"></a>Automação do Azure
Você pode processar eventos usando runbooks de Automação do Azure. O processamento de eventos usando runbooks automatizados tem suporte por meio de webhooks. Você cria um webhook para o runbook e, em seguida, usa o manipulador de webhook. Veja o seguinte tutorial para obter um exemplo: 

|Title  |Descrição  |
|---------|---------|
|[Tutorial: Automação do Azure com Grade de Eventos e Microsoft Teams](ensure-tags-exists-on-new-virtual-machines.md) |Crie uma máquina virtual, que envia um evento. O evento dispara um runbook de Automação que marca a máquina virtual e dispara uma mensagem que é enviada para um canal do Microsoft Teams. |


## <a name="logic-apps"></a>Aplicativos Lógicos
Use **Aplicativos Lógicos** para implementar processos empresariais para processar eventos da Grade de Eventos. Você não cria um webhook explicitamente neste cenário. O webhook é criado para você automaticamente quando você configura o aplicativo lógico para manipular eventos da Grade de Eventos. Consulte os seguintes tutoriais para exemplos: 

|Title  |Descrição  |
|---------|---------|
| [Tutorial: Monitorar alterações de máquina virtual com a Grade de Eventos do Azure e os Aplicativos Lógicos](monitor-virtual-machine-changes-event-grid-logic-app.md) | Um aplicativo lógico monitora as alterações feitas em uma máquina virtual e envia emails sobre essas alterações. |
| [Tutorial: Enviar notificações por email sobre os eventos do Hub IoT do Azure usando os Aplicativos Lógicos](publish-iot-hub-events-to-logic-apps.md) | Um aplicativo lógico envia um email de notificação sempre que um dispositivo é adicionado ao seu hub de IoT. |
| [Tutorial: Responder a eventos do Barramento de Serviço do Azure recebidos por meio da Grade de Eventos do Azure usando o Azure Functions e os Aplicativos Lógicos do Azure](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | A Grade de Eventos envia mensagens do tópico do Barramento de Serviço para o aplicativo de função e o aplicativo lógico. |

## <a name="rest-example-for-put"></a>Exemplo REST (para PUT)

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "WebHook",
            "properties": 
            {
                "endpointUrl": "<WEB HOOK URL>",
                "maxEventsPerBatch": 1,
                "preferredBatchSizeInKilobytes": 64
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

## <a name="next-steps"></a>Próximas etapas
Consulte o artigo [Manipuladores de eventos](event-handlers.md) para obter uma lista dos manipuladores de eventos compatíveis. 
