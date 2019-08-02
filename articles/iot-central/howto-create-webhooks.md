---
title: Criar webhooks em regras no Microsoft Azure IoT Central | Microsoft Docs
description: Crie webhooks no Microsoft Azure IoT Central para notificar automaticamente outros aplicativos ao acionar regras.
author: viv-liu
ms.author: viviali
ms.date: 06/16/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: fdeefdc1ec5372d6ac17f0f985ee0c50ce902e56
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165316"
---
# <a name="create-webhook-actions-on-rules-in-azure-iot-central"></a>Criar ações de webhook em regras no Microsoft Azure IoT Central

*Este tópico aplica-se a construtores e administradores.*

Webhooks permitem que você conecte o aplicativo IoT Central a outros aplicativos e serviços de monitoramento remoto e notificações. Webhooks notificam automaticamente os outros aplicativos e serviços conectados sempre que uma regra é acionada no aplicativo IoT Central. O aplicativo de Central da IoT envia uma solicitação POST ao ponto de extremidade HTTP do outro aplicativo sempre que uma regra é disparada. A carga contém detalhes do dispositivo e os detalhes de gatilho de regra.

## <a name="set-up-the-webhook"></a>Configurar o webhook

Neste exemplo, conecte-se para o RequestBin para ser notificado quando as regras de acionam usando webhooks.

1. Abra [RequestBin](https://requestbin.net/).

1. Crie um novo RequestBin e copie a **URL do Compartimento**.

1. Crie uma [regra de telemetria](howto-create-telemetry-rules.md) ou uma [regra de evento](howto-create-event-rules.md). Salve a regra e adicione uma nova ação.

    ![Tela de criação do Webhook](media/howto-create-webhooks/webhookcreate.png)

1. Escolha a ação do webhook, forneça um nome de exibição e cole a URL do Compartimento como URL de Retorno de Chamada.

1. Salve a regra.

Agora quando a regra é disparada, você verá uma nova solicitação aparecem no RequestBin.

## <a name="payload"></a>Carga útil

Quando uma regra é acionada, uma solicitação HTTP POST é feita para a URL de retorno de chamada que contém uma carga json com as medidas, dispositivo, regra e detalhes do aplicativo. Para uma regra de telemetria, a carga é semelhante ao seguinte:

```json
{
    "id": "ID",
    "timestamp": "date-time",
    "device" : {
        "id":"ID",
        "name":  "Refrigerator1",
        "simulated" : true,
        "deviceId": "deviceID",
        "deviceTemplate":{
            "id": "ID",
            "version":"1.0.0"
        },
        "properties":{
            "device":{
                "firmwareversion":"1.0"
            },
            "cloud":{
                "location":"One Microsoft Way"
            }
        },
        "measurements":{
            "telemetry":{
                "temperature":20,
                "pressure":10
            }
        }

    },
    "rule": {
        "id": "ID",
        "name": "High temperature alert",
        "enabled": true,
        "deviceTemplate": {
            "id":"GUID",
            "version":"1.0.0"
        }
    },
    "application": {
        "id": "ID",
        "name": "Contoso app",
        "subdomain":"contoso-app"
    }
}
```

## <a name="known-limitations"></a>Limitações conhecidas

Atualmente não há nenhuma maneira programática de assinar/cancelar a assinatura desses webhooks por meio de uma API.

Se você tiver algumas ideias para melhorar esse recurso, poste suas sugestões em nosso [fórum Uservoice](https://feedback.azure.com/forums/911455-azure-iot-central).

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como configurar e usar webhooks, a próxima etapa sugerida é explorar [criação de fluxos de trabalho no Microsoft Flow](howto-add-microsoft-flow.md).
