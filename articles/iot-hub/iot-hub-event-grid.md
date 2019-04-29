---
title: Hub IoT do Azure e a Grade de Eventos | Microsoft Docs
description: Use a Grade de Eventos do Azure para acionar processos com base nas ações que ocorrem no Hub IoT.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: kgremban
ms.openlocfilehash: a2c49a6ba269321d1903565ace3ebaae3f3b917e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60779400"
---
# <a name="react-to-iot-hub-events-by-using-event-grid-to-trigger-actions"></a>Reagir aos eventos do Hub IoT usando a Grade de Eventos para disparar ações

O Hub IoT do Azure integra-se com a Grade de Eventos do Azure para que você possa enviar notificações de eventos para outros serviços e acionar processos de downstream. Configure seus aplicativos de negócios para escutar eventos do Hub IoT para que você possa reagir a eventos críticos de maneira segura, escalonável e confiável. Por exemplo, crie um aplicativo que atualiza um banco de dados, cria um tíquete de trabalho e fornece uma notificação por email sempre que um novo dispositivo IoT está registrado para o hub IoT. 

[Grade de Eventos do Azure](../event-grid/overview.md) é um serviço de roteamento de eventos totalmente gerenciado que usa um modelo de publicação/assinatura. Grade de eventos tem suporte interno para os serviços do Azure como [Azure Functions](../azure-functions/functions-overview.md) e [Aplicativos Lógicos do Azure](../logic-apps/logic-apps-what-are-logic-apps.md)e pode fornecer alertas de eventos para os serviços do Azure usando webhooks. Para obter uma lista completa dos manipuladores de eventos que dá suporte a Grade de Eventos, consulte [Uma introdução à Grade de Eventos do Azure](../event-grid/overview.md). 

![Arquitetura de Grade de Eventos do Azure](./media/iot-hub-event-grid/event-grid-functional-model.png)

## <a name="regional-availability"></a>Disponibilidade regional

A integração da Grade de Eventos está disponível para os hubs IoT localizados nas regiões onde há suporte para a Grade de Eventos. Para obter a lista mais recente de regiões, consulte [Uma introdução à Grade de Eventos do Azure](../event-grid/overview.md). 

## <a name="event-types"></a>Tipos de evento

Hub IoT publica os seguintes tipos de evento: 

| Tipo de evento | DESCRIÇÃO |
| ---------- | ----------- |
| Microsoft.Devices.DeviceCreated | Publicado quando um dispositivo é registrado para um Hub IoT. |
| Microsoft.Devices.DeviceDeleted | Publicado quando um dispositivo é excluído de um Hub IoT. |
| Microsoft.Devices.DeviceConnected | Publicado quando um dispositivo é conectado a um Hub IoT. |
| Microsoft.Devices.DeviceDisconnected | Publicado quando um dispositivo é desconectado de um Hub IoT. |

Use o portal do Azure ou a CLI do Azure para configurar os eventos para publicar a partir de cada Hub IoT. Para um exemplo, experimente o tutorial [Enviar notificações por email sobre os eventos do Hub IoT usando Aplicativos Lógicos](../event-grid/publish-iot-hub-events-to-logic-apps.md).

## <a name="event-schema"></a>Esquema do evento

Eventos do Hub IoT contêm todas as informações que você precisa para responder às alterações no ciclo de vida do seu dispositivo. Você pode identificar um evento de Hub IoT, verificando se a propriedade eventType começa com **Microsoft.Devices**. Para obter mais informações sobre como usar propriedades de evento de Grade de Eventos, consulte o [esquema de evento da Grade de Eventos](../event-grid/event-schema.md).

### <a name="device-connected-schema"></a>Esquema de dispositivo conectado

O exemplo a seguir mostra o esquema de um evento de dispositivo conectado: 

```json
[{  
  "id": "f6bbf8f4-d365-520d-a878-17bf7238abd8", 
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>", 
  "subject": "devices/LogicAppTestDevice", 
  "eventType": "Microsoft.Devices.DeviceConnected", 
  "eventTime": "2018-06-02T19:17:44.4383997Z", 
  "data": {
      "deviceConnectionStateEventInfo": {
        "sequenceNumber":
          "000000000000000001D4132452F67CE200000002000000000000000000000001"
      },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice",
    "moduleId" : "DeviceModuleID",
  }, 
  "dataVersion": "1", 
  "metadataVersion": "1" 
}]
```

### <a name="device-created-schema"></a>Esquema criado de dispositivo

O exemplo a seguir mostra o esquema de um evento criado de dispositivo: 

```json
[{
  "id": "56afc886-767b-d359-d59e-0da7877166b2",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
  "subject": "devices/LogicAppTestDevice",
  "eventType": "Microsoft.Devices.DeviceCreated",
  "eventTime": "2018-01-02T19:17:44.4383997Z",
  "data": {
    "twin": {
      "deviceId": "LogicAppTestDevice",
      "etag": "AAAAAAAAAAE=",
      "deviceEtag":"null",
      "status": "enabled",
      "statusUpdateTime": "0001-01-01T00:00:00",
      "connectionState": "Disconnected",
      "lastActivityTime": "0001-01-01T00:00:00",
      "cloudToDeviceMessageCount": 0,
      "authenticationType": "sas",
      "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
      },
      "version": 2,
      "properties": {
        "desired": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        },
        "reported": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        }
      }
    },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

Para obter uma descrição detalhada de cada propriedade, consulte [esquema de evento de grade de eventos do Azure para o IoT Hub](../event-grid/event-schema-iot-hub.md).

## <a name="filter-events"></a>Filtrar eventos

Assinaturas de evento de Hub IoT podem filtrar eventos com base no nome de dispositivo e tipo de evento. Filtros de assunto no trabalho da Grade de Eventos com base em correspondências de **Começa com** (prefixo) e **Termina com** (sufixo). O filtro usa um operador `AND`, para que eventos com o assunto que correspondam ao prefixo e ao sufixo sejam entregues ao assinante. 

O assunto de Eventos IoT usa o formato:

```json
devices/{deviceId}
```
## <a name="limitations-for-device-connected-and-device-disconnected-events"></a>Limitações para eventos de conexão e desconexão de dispositivo

Para receber eventos de conexão e desconexão de dispositivo, você precisa abrir o link D2C ou o link C2D para o dispositivo. Se o dispositivo estiver usando o protocolo MQTT, o Hub IoT manterá o link C2D aberto. Para AMQP, você pode abrir o link C2D chamando o [recebimento assíncrono API](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.receiveasync?view=azure-dotnet). 

Se você está enviando telemetria, o link D2C está aberto. Se a conexão do dispositivo é cintilação, que significa que o dispositivo se conecta e desconecta-se com frequência, nós não enviará todos os estados de conexão única, mas publicará o estado de conexão que é o instantâneo a cada minuto. Em caso de uma interrupção do Hub IoT, publicaremos o estado de conexão do dispositivo assim que a interrupção acabar. Se o dispositivo for desconectado durante essa interrupção, o evento de dispositivo desconectado será publicado em até dez minutos.

## <a name="tips-for-consuming-events"></a>Dicas para o consumo de eventos

Aplicativos que tratam os eventos de Hub IoT devem seguir essas práticas sugeridas:

* Várias assinaturas podem ser configuradas para eventos de rota para o mesmo manipulador de eventos, portanto, não presuma que os eventos são de uma fonte específica. Sempre verifique o tópico de mensagem para certificar-se de que eles vêm do hub IoT que você espera. 

* Não presuma que todos os eventos que você recebe são os tipos que você espera. Sempre verifique o eventType antes de processar a mensagem.

* As mensagens podem chegar fora de ordem ou após um atraso. Use o campo de etag para entender se as informações sobre objetos estão atualizadas.

## <a name="next-steps"></a>Próximas etapas

* [Tente o tutorial de eventos do Hub IoT](../event-grid/publish-iot-hub-events-to-logic-apps.md)
* [Saiba como ordenar os eventos de conexão e desconexão de dispositivo](iot-hub-how-to-order-connection-state-events.md)
* [Saiba mais sobre a Grade de Eventos](../event-grid/overview.md)
* [Comparar as diferenças entre roteamento de eventos e mensagens do Hub IoT](iot-hub-event-grid-routing-comparison.md)
