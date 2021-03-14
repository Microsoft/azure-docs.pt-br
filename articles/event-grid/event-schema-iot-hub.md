---
title: Hub IoT do Azure como origem da grade de eventos
description: Este artigo fornece as propriedades e o esquema para eventos do Hub IoT do Azure. Ele lista os tipos de evento disponíveis, um evento de exemplo e propriedades de evento.
ms.topic: conceptual
ms.date: 02/11/2021
ms.openlocfilehash: 5f43b9d0041fa5842bc2557a61c5145ce588758a
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100363519"
---
# <a name="azure-iot-hub-as-an-event-grid-source"></a>Hub IoT do Azure como uma fonte de grade de eventos
Este artigo fornece as propriedades e o esquema para eventos do Hub IoT do Azure. Para obter uma introdução a esquemas de evento, consulte [esquema de grade de eventos do Azure](event-schema.md). 

## <a name="available-event-types"></a>Tipos de evento disponíveis

Hub IoT do Azure emite os seguintes tipos de evento:

| Tipo de evento | Descrição |
| ---------- | ----------- |
| Microsoft.Devices.DeviceCreated | Publicado quando um dispositivo é registrado para um Hub IoT. |
| Microsoft.Devices.DeviceDeleted | Publicado quando um dispositivo é excluído de um Hub IoT. | 
| Microsoft.Devices.DeviceConnected | Publicado quando um dispositivo é conectado a um Hub IoT. |
| Microsoft.Devices.DeviceDisconnected | Publicado quando um dispositivo é desconectado de um Hub IoT. | 
| Microsoft.Devices.DeviceTelemetry | Publicado quando uma mensagem de telemetria é enviada a um hub IoT. |

## <a name="example-event"></a>Exemplo de evento

# <a name="event-grid-event-schema"></a>[Esquema de eventos da Grade de Eventos](#tab/event-grid-event-schema)

O esquema para eventos DeviceConnected e DeviceDisconnected têm a mesma estrutura. Este exemplo de evento mostra o esquema de um evento gerado quando um dispositivo é conectado a um Hub IoT:

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
    "moduleId" : "DeviceModuleID"
  }, 
  "dataVersion": "1", 
  "metadataVersion": "1" 
}]
```

O evento DeviceTelemetry é gerado quando um evento de telemetria é enviado para um hub IoT. Um esquema de exemplo para esse evento é mostrado abaixo.

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

O esquema para eventos DeviceCreated e DeviceDeleted têm a mesma estrutura. Este exemplo de evento mostra o esquema de um evento gerado quando um dispositivo é registrado em um Hub IoT:

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
      "deviceEtag": "null",
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

# <a name="cloud-event-schema"></a>[Esquema de evento de nuvem](#tab/cloud-event-schema)

O esquema para eventos DeviceConnected e DeviceDisconnected têm a mesma estrutura. Este exemplo de evento mostra o esquema de um evento gerado quando um dispositivo é conectado a um Hub IoT:

```json
[{
  "id": "f6bbf8f4-d365-520d-a878-17bf7238abd8", 
  "source": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>", 
  "subject": "devices/LogicAppTestDevice", 
  "type": "Microsoft.Devices.DeviceConnected", 
  "time": "2018-06-02T19:17:44.4383997Z", 
  "data": {
    "deviceConnectionStateEventInfo": {
      "sequenceNumber":
        "000000000000000001D4132452F67CE200000002000000000000000000000001"
    },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice",
    "moduleId" : "DeviceModuleID"
  }, 
  "specversion": "1.0"
}]
```

O evento DeviceTelemetry é gerado quando um evento de telemetria é enviado para um hub IoT. Um esquema de exemplo para esse evento é mostrado abaixo.

```json
[{
  "id": "9af86784-8d40-fe2g-8b2a-bab65e106785",
  "source": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>", 
  "subject": "devices/LogicAppTestDevice", 
  "type": "Microsoft.Devices.DeviceTelemetry",
  "time": "2019-01-07T20:58:30.48Z",
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
  "specversion": "1.0"
}]
```

O esquema para eventos DeviceCreated e DeviceDeleted têm a mesma estrutura. Este exemplo de evento mostra o esquema de um evento gerado quando um dispositivo é registrado em um Hub IoT:

```json
[{
  "id": "56afc886-767b-d359-d59e-0da7877166b2",
  "source": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
  "subject": "devices/LogicAppTestDevice",
  "type": "Microsoft.Devices.DeviceCreated",
  "time": "2018-01-02T19:17:44.4383997Z",
  "data": {
    "twin": {
      "deviceId": "LogicAppTestDevice",
      "etag": "AAAAAAAAAAE=",
      "deviceEtag": "null",
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
  "specversion": "1.0"
}]
```

---


### <a name="event-properties"></a>Propriedades do evento

# <a name="event-grid-event-schema"></a>[Esquema de eventos da Grade de Eventos](#tab/event-grid-event-schema)

Todos os eventos conterão os mesmos dados de nível superior: 

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| `id` | string | Identificador exclusivo do evento. |
| `topic` | string | Caminho de recurso completo para a origem do evento. Esse campo não é gravável. Grade de Eventos fornece esse valor. |
| `subject` | string | Caminho definido pelo publicador para o assunto do evento. |
| `eventType` | string | Um dos tipos de evento registrados para a origem do evento. |
| `eventTime` | string | A hora em que o evento é gerado com base na hora UTC do provedor. |
| `data` | object | Dados de evento de Hub IoT.  |
| `dataVersion` | string | A versão do esquema do objeto de dados. O publicador define a versão do esquema. |
| `metadataVersion` | string | A versão do esquema dos metadados do evento. Grade de Eventos define o esquema de propriedades de nível superior. A Grade de Eventos fornece esse valor. |

# <a name="cloud-event-schema"></a>[Esquema de evento de nuvem](#tab/cloud-event-schema)

Todos os eventos conterão os mesmos dados de nível superior: 


| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| `id` | string | Identificador exclusivo do evento. |
| `source` | string | Caminho de recurso completo para a origem do evento. Esse campo não é gravável. Grade de Eventos fornece esse valor. |
| `subject` | string | Caminho definido pelo publicador para o assunto do evento. |
| `type` | string | Um dos tipos de evento registrados para a origem do evento. |
| `time` | string | A hora em que o evento é gerado com base na hora UTC do provedor. |
| `data` | object | Dados de evento de Hub IoT.  |
| `specversion` | string | Versão de especificação de esquema CloudEvents. |

---

Para todos os eventos de Hub IoT, o objeto de dados contém as seguintes propriedades:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| `hubName` | string | Nome do Hub IoT em que o dispositivo foi criado ou excluído. |
| `deviceId` | string | O identificador exclusivo do dispositivo. Essa cadeia de caracteres que diferencia maiúsculas de minúsculas pode ter até 128 caracteres e suporta caracteres alfanuméricos ASCII de 7 bits, mais os caracteres especiais a seguir: `- : . + % _ # * ? ! ( ) , = @ ; $ '`. |

O conteúdo do objeto de dados é diferente para cada publicador do evento. 

Para os eventos de Hub IoT **Dispositivo Conectado** e **Dispositivo Desconectado**, o objeto de dados contém as seguintes propriedades:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| `moduleId` | string | O identificador exclusivo do módulo. Este campo é a saída somente para dispositivos de módulo. Essa cadeia de caracteres que diferencia maiúsculas de minúsculas pode ter até 128 caracteres e suporta caracteres alfanuméricos ASCII de 7 bits, mais os caracteres especiais a seguir: `- : . + % _ # * ? ! ( ) , = @ ; $ '`. |
| `deviceConnectionStateEventInfo` | object | Informações de evento de estado de conexão do dispositivo
| `sequenceNumber` | string | Um número que ajuda a indicar a ordem de eventos de dispositivo conectado ou dispositivo desconectado. O evento mais recente terá um número de sequência maior que o evento anterior. Esse número pode se alterar por mais de 1, mas está aumentando estritamente. Veja [como usar o número de sequência](../iot-hub/iot-hub-how-to-order-connection-state-events.md). |

Para o evento Hub IOT de **telemetria do dispositivo** , o objeto de dados contém a mensagem do dispositivo para a nuvem no [formato de mensagem do Hub IOT](../iot-hub/iot-hub-devguide-messages-construct.md) e tem as seguintes propriedades:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| `body` | string | O conteúdo da mensagem do dispositivo. |
| `properties` | string | Propriedades do aplicativo são cadeias de caracteres definidas pelo usuário que podem ser adicionadas à mensagem. Esses campos são opcionais. |
| `system properties` | string | [As propriedades do sistema](../iot-hub/iot-hub-devguide-routing-query-syntax.md#system-properties) ajudam a identificar o conteúdo e a origem das mensagens. A mensagem de telemetria do dispositivo deve estar em um formato JSON válido com o contentType definido como JSON e contentEncoding definido como UTF-8 nas propriedades do sistema de mensagens. Se isso não estiver definido, o Hub IoT gravará as mensagens no formato codificado 64 base.  |

Para os eventos de Hub IoT **Dispositivo Criado** e **Dispositivo Excluído**, o objeto de dados contém as seguintes propriedades:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| `twin` | object | Informações sobre o dispositivo "or", que é a representação em nuvem dos metadados do dispositivo de aplicativo. | 
| `deviceID` | string | O identificador exclusivo do dispositivo gêmeo. | 
| `etag` | string | Um validador para garantir a consistência das atualizações para um dispositivo gêmeo. Cada etag é garantida como sendo exclusivo por dispositivos gêmeos. |  
| `deviceEtag` | string | Um validador para garantir a consistência das atualizações para um registro de dispositivo. Cada deviceEtag é garantida como sendo exclusivo por registro de dispositivo. |
| `status` | string | Se os dispositivos gêmeos estão habilitados ou desabilitados. | 
| `statusUpdateTime` | string | Atualizar o carimbo de data/hora ISO 8601 da ultima atualização de status dos dispositivos gêmeos. |
| `connectionState` | string | Se o dispositivo está conectado ou desconectado. | 
| `lastActivityTime` | string | O carimbo de data/hora ISO8601 da última atividade. | 
| `cloudToDeviceMessageCount` | inteiro | Contagem de nuvem para mensagens de dispositivo enviadas para este dispositivo. | 
| `authenticationType` | string | Tipo de autenticação usado para este dispositivo: `SAS`, `SelfSigned`, ou `CertificateAuthority`. |
| `x509Thumbprint` | string | A impressão digital é um valor exclusivo para o certificado x509, comumente usado para localizar um certificado específico em um repositório de certificados. A impressão digital é gerada dinamicamente usando o algoritmo SHA1 e não existe fisicamente no certificado. | 
| `primaryThumbprint` | string | A impressão digital primária para o certificado x509. |
| `secondaryThumbprint` | string | A impressão digital secundária para o certificado x509. | 
| `version` | inteiro | Um inteiro que é incrementado em um cada vez que o dispositivo gêmeo é atualizado. |
| `desired` | object | Uma parte das propriedades que pode ser gravada apenas pelo back-end do aplicativo e lida pelo dispositivo. | 
| `reported` | object | Uma parte das propriedades que pode ser gravada somente pelo dispositivo e lida pelo back-end do aplicativo. |
| `lastUpdated` | string | Atualizar o carimbo de data/hora ISO 8601 da ultima atualização de propriedade dos dispositivos gêmeos. | 

## <a name="tutorials-and-how-tos"></a>Tutoriais e instruções
|Título  |Descrição  |
|---------|---------|
| [Enviar notificações por email sobre os eventos do Hub IoT usando Aplicativos Lógicos](publish-iot-hub-events-to-logic-apps.md) | Um aplicativo lógico envia um email de notificação sempre que um dispositivo é adicionado ao seu Hub IoT. |
| [Reagir aos eventos do Hub IoT usando a Grade de Eventos para disparar ações](../iot-hub/iot-hub-event-grid.md) | Visão geral da integração do Hub IoT com a Grade de Eventos. |
| [Ordenar eventos de conexão e desconexão de dispositivo](../iot-hub/iot-hub-how-to-order-connection-state-events.md) | Mostra como ordenar os eventos de estado de conexão do dispositivo. |

## <a name="next-steps"></a>Próximas etapas

* Para ver uma introdução à Grade de Eventos do Azure, confira [O que é uma Grade de eventos?](overview.md)
* Para saber mais sobre como o Hub IoT e a Grade de Eventos funcionam juntos, consulte [Reagir a eventos do Hub IoT usando a Grade de Eventos para disparar ações](../iot-hub/iot-hub-event-grid.md).