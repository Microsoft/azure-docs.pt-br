---
title: Criar webhooks em regras no Microsoft Azure IoT Central | Microsoft Docs
description: Crie webhooks no Microsoft Azure IoT Central para notificar automaticamente outros aplicativos ao acionar regras.
author: viv-liu
ms.author: viviali
ms.date: 04/03/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: b2ac4bbf1457144d23a91c4e83b554b3ee806119
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "87337221"
---
# <a name="create-webhook-actions-on-rules-in-azure-iot-central"></a>Criar ações de webhook em regras no Microsoft Azure IoT Central

*Este tópico aplica-se a construtores e administradores.*

Webhooks permitem que você conecte o aplicativo IoT Central a outros aplicativos e serviços de monitoramento remoto e notificações. Webhooks notificam automaticamente os outros aplicativos e serviços conectados sempre que uma regra é acionada no aplicativo IoT Central. Seu aplicativo IoT Central envia uma solicitação POST para o ponto de extremidade HTTP do outro aplicativo sempre que uma regra é disparada. A carga contém detalhes do dispositivo e detalhes do gatilho da regra.

## <a name="set-up-the-webhook"></a>Configurar o webhook

Neste exemplo, você se conecta ao RequestBin para ser notificado quando as regras forem acionadas usando WebHooks.

1. Abra [RequestBin](https://requestbin.net/).

1. Crie um novo RequestBin e copie a **URL do Compartimento**.

1. Crie uma [regra de telemetria](tutorial-create-telemetry-rules.md). Salve a regra e adicione uma nova ação.

    ![Tela de criação do webhook](media/howto-create-webhooks/webhookcreate.png)

1. Escolha a ação do webhook, forneça um nome de exibição e cole a URL do Compartimento como URL de Retorno de Chamada.

1. Salve a regra.

Agora, quando a regra for disparada, você verá que uma nova solicitação aparecerá em RequestBin.

## <a name="payload"></a>Carga útil

Quando uma regra é disparada, uma solicitação HTTP POST é feita à URL de retorno de chamada que contém uma carga JSON com os detalhes de telemetria, dispositivo, regra e aplicativo. O conteúdo pode ser semelhante ao seguinte:

```json
{
    "timestamp": "2020-04-06T00:20:15.06Z",
    "action": {
        "id": "<id>",
        "type": "WebhookAction",
        "rules": [
            "<rule_id>"
        ],
        "displayName": "Webhook 1",
        "url": "<callback_url>"
    },
    "application": {
        "id": "<application_id>",
        "displayName": "Contoso",
        "subdomain": "contoso",
        "host": "contoso.azureiotcentral.com"
    },
    "device": {
        "id": "<device_id>",
        "etag": "<etag>",
        "displayName": "MXChip IoT DevKit - 1yl6vvhax6c",
        "instanceOf": "<device_template_id>",
        "simulated": true,
        "provisioned": true,
        "approved": true,
        "cloudProperties": {
            "City": {
                "value": "Seattle"
            }
        },
        "properties": {
            "deviceinfo": {
                "firmwareVersion": {
                    "value": "1.0.0"
                }
            }
        },
        "telemetry": {
            "<interface_instance_name>": {
                "humidity": {
                    "value": 47.33228889360127
                }
            }
        }
    },
    "rule": {
        "id": "<rule_id>",
        "displayName": "Humidity monitor"
    }
}
```
Se a regra monitorar a telemetria agregada durante um período de tempo, a carga conterá uma seção de telemetria diferente.

```json
{
    "telemetry": {
        "<interface_instance_name>": {
            "Humidity": {
                "avg": 39.5
            }
        }
    }
}
```

## <a name="data-format-change-notice"></a>Aviso de alteração do formato de dados

Se você tiver um ou mais WebHooks criados e salvos antes de **3 de abril de 2020**, será necessário excluir o webhook e criar um novo webhook. Isso ocorre porque WebHooks mais antigos usam um formato de carga mais antigo que será preterido no futuro.

### <a name="webhook-payload-format-deprecated-as-of-3-april-2020"></a>Carga de webhook (formato preterido a partir de 3 de abril de 2020)

```json
{
    "id": "<id>",
    "displayName": "Webhook 1",
    "timestamp": "2019-10-24T18:27:13.538Z",
    "rule": {
        "id": "<id>",
        "displayName": "High temp alert",
        "enabled": true
    },
    "device": {
        "id": "mx1",
        "displayName": "MXChip IoT DevKit - mx1",
        "instanceOf": "<device-template-id>",
        "simulated": true,
        "provisioned": true,
        "approved": true
    },
    "data": [{
        "@id": "<id>",
        "@type": ["Telemetry"],
        "name": "temperature",
        "displayName": "Temperature",
        "value": 66.27310467496761,
        "interfaceInstanceName": "sensors"
    }],
    "application": {
        "id": "<id>",
        "displayName": "x - Store Analytics Checkout",
        "subdomain": "<subdomain>",
        "host": "<host>"
    }
}
```

## <a name="known-limitations"></a>Limitações conhecidas

Atualmente não há nenhuma maneira programática de assinar/cancelar a assinatura desses webhooks por meio de uma API.

Se você tiver ideias sobre como melhorar esse recurso, poste suas sugestões em nosso [Fórum de voz do usuário](https://feedback.azure.com/forums/911455-azure-iot-central).

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como configurar e usar WebHooks, a próxima etapa sugerida é explorar a [configuração Azure monitor grupos de ação](howto-use-action-groups.md).
