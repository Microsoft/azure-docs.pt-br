---
title: Hub IoT do Azure e a Grade de Eventos | Microsoft Docs
description: Use a Grade de Eventos do Azure para acionar processos com base nas ações que ocorrem no Hub IoT.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: robinsh
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
ms.openlocfilehash: 0e0ca8a787145fb40087a2d99be85607404eebfa
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92152125"
---
# <a name="react-to-iot-hub-events-by-using-event-grid-to-trigger-actions"></a>Reagir aos eventos do Hub IoT usando a Grade de Eventos para disparar ações

O Hub IoT do Azure se integra à Grade de Eventos do Azure para que você possa enviar notificações de eventos para outros serviços e disparar processos downstream. Configure seus aplicativos de negócios para escutar eventos do Hub IoT para que você possa reagir a eventos críticos de maneira confiável, escalonável e segura. Por exemplo, crie um aplicativo que atualiza um banco de dados, cria um tíquete de trabalho e entrega uma notificação por email toda vez que um novo dispositivo IoT é registrado no Hub IoT.

[Grade de Eventos do Azure](../event-grid/overview.md) é um serviço de roteamento de eventos totalmente gerenciado que usa um modelo de publicação/assinatura. Grade de eventos tem suporte interno para os serviços do Azure como [Azure Functions](../azure-functions/functions-overview.md) e [Aplicativos Lógicos do Azure](../logic-apps/logic-apps-overview.md)e pode fornecer alertas de eventos para os serviços do Azure usando webhooks. Para obter uma lista completa dos manipuladores de eventos que dá suporte a Grade de Eventos, consulte [Uma introdução à Grade de Eventos do Azure](../event-grid/overview.md).

![Arquitetura de Grade de Eventos do Azure](./media/iot-hub-event-grid/event-grid-functional-model.png)

## <a name="regional-availability"></a>Disponibilidade regional

A integração da Grade de Eventos está disponível para os hubs IoT localizados nas regiões onde há suporte para a Grade de Eventos. Para obter a lista mais recente de regiões, consulte [Uma introdução à Grade de Eventos do Azure](../event-grid/overview.md).

## <a name="event-types"></a>Tipos de evento

Hub IoT publica os seguintes tipos de evento:

| Tipo de evento | Descrição |
| ---------- | ----------- |
| Microsoft.Devices.DeviceCreated | Publicado quando um dispositivo é registrado para um Hub IoT. |
| Microsoft.Devices.DeviceDeleted | Publicado quando um dispositivo é excluído de um Hub IoT. |
| Microsoft.Devices.DeviceConnected | Publicado quando um dispositivo é conectado a um Hub IoT. |
| Microsoft.Devices.DeviceDisconnected | Publicado quando um dispositivo é desconectado de um Hub IoT. |
| Microsoft.Devices.DeviceTelemetry | Publicado quando uma mensagem de telemetria do dispositivo é enviada a um hub IoT |

Use o portal do Azure ou a CLI do Azure para configurar os eventos para publicar a partir de cada Hub IoT. Para um exemplo, experimente o tutorial [Enviar notificações por email sobre os eventos do Hub IoT usando Aplicativos Lógicos](../event-grid/publish-iot-hub-events-to-logic-apps.md).

## <a name="event-schema"></a>Esquema do evento

Eventos do Hub IoT contêm todas as informações que você precisa para responder às alterações no ciclo de vida do seu dispositivo. Identifique um evento do Hub IoT verificando se a propriedade eventType começa com **Microsoft.Devices**. Para obter mais informações sobre como usar propriedades de evento de Grade de Eventos, consulte o [esquema de evento da Grade de Eventos](../event-grid/event-schema.md).

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



### <a name="device-telemetry-schema"></a>Esquema de telemetria do dispositivo

A mensagem de telemetria do dispositivo deve estar em um formato JSON válido com o contentType definido como **Application/JSON** e contentEncoding definido como **UTF-8** nas [Propriedades do sistema](iot-hub-devguide-routing-query-syntax.md#system-properties)de mensagens. Essas duas propriedades não diferenciam maiúsculas de minúsculas. Se a codificação de conteúdo não estiver definida, o Hub IoT gravará as mensagens no formato codificado 64 base.

Você pode enriquecer os eventos de telemetria do dispositivo antes que eles sejam publicados na grade de eventos selecionando o ponto de extremidade como grade de eventos. Para obter mais informações, consulte [visão geral de aprimoramentos de mensagem](iot-hub-message-enrichments-overview.md).

O exemplo a seguir mostra o esquema de um evento de telemetria do dispositivo:

```json
[{  
  "id": "9af86784-8d40-fe2g-8b2a-bab65e106785",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
  "subject": "devices/LogicAppTestDevice",
  "eventType": "Microsoft.Devices.DeviceTelemetry",
  "eventTime": "2019-01-07T20:58:30.48Z",
  "data": {
      "body": {
          "Weather": {
              "Temperature": 900
            },
            "Location": "USA"
        },
        "properties": {
            "Status": "Active"
        },
        "systemProperties": {
          "iothub-content-type": "application/json",
          "iothub-content-encoding": "utf-8",
          "iothub-connection-device-id": "d1",
          "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
          "iothub-connection-auth-generation-id": "123455432199234570",
          "iothub-enqueuedtime": "2019-01-07T20:58:30.48Z",
          "iothub-message-source": "Telemetry"
        }
  },
  "dataVersion": "",
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


> [!WARNING]
> *Os dados de entrelaçamento* associados a um evento de criação de dispositivo são uma configuração padrão e não *devem* ser dependentes para `authenticationType` as propriedades real e outras do dispositivo em um dispositivo recém-criado. Para `authenticationType` o e outras propriedades de dispositivo em um dispositivo recém-criado, use a API do Gerenciador de registro fornecida nos SDKs do Azure IOT.

Para obter uma descrição detalhada de cada propriedade, consulte [esquema de evento da grade de eventos do Azure para o Hub IOT](../event-grid/event-schema-iot-hub.md).

## <a name="filter-events"></a>Filtrar eventos

As assinaturas de evento do Hub IoT podem filtrar eventos com base no tipo de evento, conteúdo de dados e assunto, que é o nome do dispositivo.

A grade de eventos permite [Filtrar](../event-grid/event-filtering.md) tipos de eventos, assuntos e conteúdo de dados. Ao criar a assinatura da grade de eventos, você pode optar por assinar os eventos de IoT selecionados. Filtros de assunto no trabalho da Grade de Eventos com base em correspondências de **Começa com** (prefixo) e **Termina com** (sufixo). O filtro usa um operador `AND`, para que eventos com o assunto que correspondam ao prefixo e ao sufixo sejam entregues ao assinante.

O assunto de Eventos IoT usa o formato:

```json
devices/{deviceId}
```

A grade de eventos também permite a filtragem de atributos de cada evento, incluindo o conteúdo de dados. Isso permite que você escolha quais eventos são entregues com base no conteúdo da mensagem de telemetria. Consulte [filtragem avançada](../event-grid/event-filtering.md#advanced-filtering) para exibir exemplos. Para filtrar o corpo da mensagem de telemetria, você deve definir o contentType como **Application/JSON** e ContentEncoding como **UTF-8** nas [Propriedades do sistema](./iot-hub-devguide-routing-query-syntax.md#system-properties)de mensagens. Essas duas propriedades não diferenciam maiúsculas de minúsculas.

Para eventos que não são de telemetria como DeviceConnected, DeviceDisconnected, DeviceCreated e DeviceDeleted, a filtragem da grade de eventos pode ser usada ao criar a assinatura. Para eventos de telemetria, além da filtragem na grade de eventos, os usuários também podem filtrar no dispositivo gêmeos, propriedades de mensagem e corpo por meio da consulta de roteamento de mensagens. 

Quando você assina eventos de telemetria por meio da grade de eventos, o Hub IoT cria uma rota de mensagem padrão para enviar mensagens de dispositivo do tipo fonte de dados para a grade de eventos. Para obter mais informações sobre o roteamento de mensagens, consulte [Roteamento de mensagens do Hub IOT](iot-hub-devguide-messages-d2c.md). Essa rota ficará visível no portal no Hub IoT > roteamento de mensagens. Somente uma rota para a grade de eventos é criada, independentemente do número de assinaturas de exemplo criadas para eventos de telemetria. Portanto, se você precisar de várias assinaturas com filtros diferentes, poderá usar o operador OR nessas consultas na mesma rota. A criação e a exclusão da rota são controladas por meio da assinatura de eventos de telemetria por meio da grade de eventos. Você não pode criar ou excluir uma rota para a grade de eventos usando o roteamento de mensagens do Hub IoT.

Para filtrar mensagens antes que os dados de telemetria sejam enviados, você pode atualizar sua [consulta de roteamento](iot-hub-devguide-routing-query-syntax.md). Observe que a consulta de roteamento pode ser aplicada ao corpo da mensagem somente se o corpo for JSON. Você também deve definir o contentType como **Application/JSON** e ContentEncoding como **UTF-8** nas propriedades do [sistema](./iot-hub-devguide-routing-query-syntax.md#system-properties)de mensagens.

## <a name="limitations-for-device-connected-and-device-disconnected-events"></a>Limitações para eventos de conexão e desconexão de dispositivo

Para receber eventos de estado de conexão do dispositivo, um dispositivo deve fazer uma operação "C2D Receive telemetria" ou "mensagem de recebimento" com o Hub IOT. No entanto, observe que, se um dispositivo estiver usando o protocolo AMQP para se conectar ao Hub IOT, é recomendável que eles façam uma operação "C2D receber mensagem", caso contrário, suas notificações de estado de conexão poderão ser atrasadas em alguns minutos. Se o dispositivo estiver usando o protocolo MQTT, o Hub IoT manterá o link C2D aberto. Para AMQP, você pode abrir o link C2D chamando a API assíncrona de recebimento para o SDK C# do Hub IoT ou [cliente do dispositivo para AMQP](iot-hub-amqp-support.md#device-client).

Se você está enviando telemetria, o link D2C está aberto. 

Se a conexão do dispositivo estiver piscando, o que significa que o dispositivo se conecta e desconecta com frequência, não enviaremos todos os Estados de conexão única, mas publicaremos o estado de conexão atual feito em um instantâneo periódico, até que a cintilação continue. O recebimento do mesmo evento de estado de conexão com números de sequência diferentes ou eventos de estado de conexão diferentes significam que houve uma alteração no estado de conexão do dispositivo.

## <a name="tips-for-consuming-events"></a>Dicas para o consumo de eventos

Os aplicativos que lidam com eventos do Hub IoT devem seguir estas práticas sugeridas:

* Várias assinaturas podem ser configuradas para rotear eventos para o mesmo manipulador de eventos, portanto, não presuma que os eventos são de uma fonte específica. Sempre verifique o tópico da mensagem para garantir que ela vem do Hub IoT esperado.

* Não presuma que todos os eventos recebidos são dos tipos esperados. Sempre verifique o eventType antes de processar a mensagem.

* As mensagens podem ser recebidas fora de ordem ou após um atraso. Use o campo ETag para entender se suas informações sobre objetos estão atualizadas para dispositivos criados pelo dispositivo ou eventos excluídos do dispositivo.

## <a name="next-steps"></a>Próximas etapas

* [Tente o tutorial de eventos do Hub IoT](../event-grid/publish-iot-hub-events-to-logic-apps.md)

* [Saiba como ordenar os eventos de conexão e desconexão de dispositivo](iot-hub-how-to-order-connection-state-events.md)

* [Saiba mais sobre a Grade de Eventos](../event-grid/overview.md)

* [Comparar as diferenças entre roteamento de eventos e mensagens do Hub IoT](iot-hub-event-grid-routing-comparison.md)

* [Saiba como usar eventos de telemetria de IoT para implementar análises espaciais de IoT usando o Azure Maps](../azure-maps/tutorial-iot-hub-maps.md)