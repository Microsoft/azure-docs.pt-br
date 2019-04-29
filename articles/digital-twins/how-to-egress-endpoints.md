---
title: Saída e pontos de extremidade em gêmeos digitais do Azure | Microsoft Docs
description: Diretrizes sobre como criar pontos de extremidade de Gêmeos Digitais do Azure.
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/31/2018
ms.author: alinast
ms.openlocfilehash: e93811a56f934a95dde45633c4fb64312b3696df
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60924798"
---
# <a name="egress-and-endpoints"></a>Pontos de extremidade e de saída

Os *pontos de extremidade* dos Gêmeos Digitais do Azure representam um agente de mensagens ou eventos na assinatura de um usuário do Azure. Eventos e mensagens podem ser enviados para os tópicos Hubs de Eventos do Azure, Grade de Eventos do Azure e Barramento de Serviço do Azure.

Eventos são roteados para pontos de extremidade de acordo com as preferências de roteamentos predefinidos. Os usuários especificam quais *tipos de evento* cada ponto de extremidade pode receber.

Para saber mais sobre eventos, roteamento e tipos de eventos, consulte [Roteamento de eventos e as mensagens nos Gêmeos Digitais do Azure](./concepts-events-routing.md).

## <a name="events"></a>Eventos

Eventos são enviados por objetos de IoT (por exemplo, dispositivos e sensores) para processamento por agentes de mensagens e eventos do Azure. Eventos são definidos pela seguinte [Referência de esquema de evento da Grade de Eventos do Azure](../event-grid/event-schema.md).

```JSON
{
  "id": "00000000-0000-0000-0000-000000000000",
  "subject": "ExtendedPropertyKey",
  "data": {
    "SpacesToNotify": [
      "3a16d146-ca39-49ee-b803-17a18a12ba36"
    ],
    "Id": "00000000-0000-0000-0000-000000000000",
      "Type": "ExtendedPropertyKey",
    "AccessType": "Create"
  },
  "eventType": "TopologyOperation",
  "eventTime": "2018-04-17T17:41:54.9400177Z",
  "dataVersion": "1",
  "metadataVersion": "1",
  "topic": "/subscriptions/YOUR_TOPIC_NAME"
}
```

| Atributo | Type | DESCRIÇÃO |
| --- | --- | --- |
| ID | string | Identificador exclusivo do evento. |
| subject | string | Caminho definido pelo fornecedor para o assunto do evento. |
| data | objeto | Dados do evento específicos ao provedor de recursos. |
| eventType | string | Um dos tipos de evento registrados para a origem do evento. |
| eventTime | string | A hora em que o evento é gerado com base na hora UTC do provedor. |
| dataVersion | string | A versão do esquema do objeto de dados. O fornecedor define a versão do esquema. |
| metadataVersion | string | A versão do esquema do metadados de evento. Grade de Eventos define o esquema de propriedades de nível superior. Grade de Eventos fornece esse valor. |
| topic | string | Caminho de recurso completo para a origem do evento. Este campo não é gravável. Grade de Eventos fornece esse valor. |

Para obter mais informações sobre o esquema de evento da Grade de Eventos:

- Examine a [Referência de esquema de evento de Grade de Eventos do Azure](../event-grid/event-schema.md).
- Leia a [Referência do Azure EventGrid Node.js SDK EventGridEvent](https://docs.microsoft.com/javascript/api/azure-eventgrid/eventgridevent?view=azure-node-latest).

## <a name="event-types"></a>Tipos de evento

Os tipos de eventos classificam a natureza do evento e são definidos no campo **eventType**. Tipos de evento disponíveis são fornecidos pela lista a seguir:

- TopologyOperation
- UdfCustom
- SensorChange
- SpaceChange
- DeviceMessage

Formatos de eventos para cada um dos tipos de eventos são descritos nas seções a seguir.

### <a name="topologyoperation"></a>TopologyOperation

**TopologyOperation** aplica-se a alterações no gráfico. O **assunto** propriedade especifica o tipo de objeto afetado. Os seguintes tipos de objetos podem acionar este evento:

- Dispositivo
- DeviceBlobMetadata
- DeviceExtendedProperty
- ExtendedPropertyKey
- ExtendedType
- KeyStore
- Relatório
- RoleDefinition
- Sensor
- SensorBlobMetadata
- SensorExtendedProperty
- Espaço
- SpaceBlobMetadata
- SpaceExtendedProperty
- SpaceResource
- SpaceRoleAssignment
- Sistema
- Usuário
- UserBlobMetadata
- UserExtendedProperty

#### <a name="example"></a>Exemplo

```JSON
{
  "id": "00000000-0000-0000-0000-000000000000",
  "subject": "ExtendedPropertyKey",
  "data": {
    "SpacesToNotify": [
      "3a16d146-ca39-49ee-b803-17a18a12ba36"
    ],
    "Id": "00000000-0000-0000-0000-000000000000",
      "Type": "ExtendedPropertyKey",
    "AccessType": "Create"
  },
  "eventType": "TopologyOperation",
  "eventTime": "2018-04-17T17:41:54.9400177Z",
  "dataVersion": "1",
  "metadataVersion": "1",
  "topic": "/subscriptions/YOUR_TOPIC_NAME"
}
```

| Value | Substitua por |
| --- | --- |
| YOUR_TOPIC_NAME | O nome do seu tópico personalizado |

### <a name="udfcustom"></a>UdfCustom

**UdfCustom** é um evento enviado por uma função definida pelo usuário (UDF).
  
> [!IMPORTANT]  
> Este evento deve ser explicitamente enviado do próprio UDF.

#### <a name="example"></a>Exemplo

```JSON
{
  "id": "568fd394-380b-46fa-925a-ebb96f658cce",
  "subject": "UdfCustom",
  "data": {
    "TopologyObjectId": "7c799bfc-1bff-4b9e-b15a-669933969d20",
    "ResourceType": "Space",
    "Payload": "\"Room is not available or air quality is poor\"",
    "CorrelationId": "568fd394-380b-46fa-925a-ebb96f658cce"
  },
  "eventType": "UdfCustom",
  "eventTime": "2018-10-02T06:50:15.198Z",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "topic": "/subscriptions/YOUR_TOPIC_NAME"
}
```

| Value | Substitua por |
| --- | --- |
| YOUR_TOPIC_NAME | O nome do seu tópico personalizado |

### <a name="sensorchange"></a>SensorChange

**SensorChange** é uma atualização do estado de um sensor com base nas alterações de telemetria.

#### <a name="example"></a>Exemplo

```JSON
{
  "id": "60bf5336-2929-45b4-bb4c-b45699dfe95f",
  "subject": "SensorChange",
  "data": {
    "Type": "Classic",
    "DataType": "Motion",
    "Id": "60bf5336-2929-45b4-bb4c-b45699dfe95f",
    "Value": "False",
    "PreviousValue": "True",
    "EventTimestamp": "2018-04-17T17:46:15.4964262Z",
    "MessageType": "sensor",
    "Properties": {
      "ms-client-request-id": "c9e576b7-5eea-4f61-8617-92a57add5179",
      "ms-activity-id": "ct22YwXEGJ5u.605.0"
    }
  },
  "eventType": "SensorChange",
  "eventTime": "2018-04-17T17:46:18.5452993Z",
  "dataVersion": "1",
  "metadataVersion": "1",
  "topic": "/subscriptions/YOUR_TOPIC_NAME"
}
```

| Value | Substitua por |
| --- | --- |
| YOUR_TOPIC_NAME | O nome do seu tópico personalizado |

### <a name="spacechange"></a>SpaceChange

**SpaceChange** é uma atualização para o estado de um espaço com base nas alterações de telemetria.

#### <a name="example"></a>Exemplo

```JSON
{
  "id": "42522e10-b1aa-42ff-a5e7-7181788ffc4b",
  "subject": "SpaceChange",
  "data": {
    "Type": null,
    "DataType": "AvailableAndFresh",
    "Id": "7c799bfc-1bff-4b9e-b15a-669933969d20",
    "Value": "Room is not available or air quality is poor",
    "PreviousValue": null,
    "RawData": null,
    "transactionId": null,
    "EventTimestamp": null,
    "MessageType": null,
    "Properties": null,
    "CorrelationId": "42522e10-b1aa-42ff-a5e7-7181788ffc4b"
  },
  "eventType": "SpaceChange",
  "eventTime": "2018-10-02T06:50:20.128Z",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "topic": "/subscriptions/YOUR_TOPIC_NAME"
}
```

| Value | Substitua por |
| --- | --- |
| YOUR_TOPIC_NAME | O nome do seu tópico personalizado |

### <a name="devicemessage"></a>DeviceMessage

Ao usar **DeviceMessage**, você pode especificar uma conexão **EventHub** para a qual os eventos de telemetria brutos também podem ser roteados a partir do Gêmeos Digitais do Azure.

> [!NOTE]
> - **DeviceMessage** é combinável apenas com **EventHub**. Você não pode combinar **DeviceMessage** com nenhum outro tipo de evento.
> - Você pode especificar apenas um ponto de extremidade da combinação de tipo **EventHub** ou **DeviceMessage**.

## <a name="configure-endpoints"></a>Configurar pontos de extremidade

O gerenciamento de terminal é exercido por meio da API do Endpoints.

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

Os exemplos a seguir demonstram como configurar os pontos de extremidade suportados.

>[!IMPORTANT]
> Preste muita atenção ao atributo **eventTypes**. Ele define quais tipos de evento são manipulados pelo ponto de extremidade e assim, determinam seu roteamento.

Uma solicitação HTTP POST autenticada mediante

```plaintext
YOUR_MANAGEMENT_API_URL/endpoints
```

- Rotear para os tipos de evento do Barramento de Serviço do Microsoft Azure **SensorChange**, **SpaceChange** e **TopologyOperation**:

  ```JSON
  {
    "type": "ServiceBus",
    "eventTypes": [
      "SensorChange",
      "SpaceChange",
      "TopologyOperation"
    ],
    "connectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_PRIMARY_KEY",
    "secondaryConnectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SECONDARY_KEY",
    "path": "YOUR_TOPIC_NAME"
  }
  ```

    | Value | Substitua por |
    | --- | --- |
    | YOUR_NAMESPACE | O namespace do seu ponto de extremidade |
    | YOUR_PRIMARY_KEY | A cadeia de conexão primária usada para autenticar |
    | YOUR_SECONDARY_KEY | A cadeia de conexão secundária usada para autenticar |
    | YOUR_TOPIC_NAME | O nome do seu tópico personalizado |

- Rotear para os tipos de eventos da Grade de Eventos do Azure **SensorChange**, **SpaceChange** e **TopologyOperation**:

  ```JSON
  {
    "type": "EventGrid",
    "eventTypes": [
      "SensorChange",
      "SpaceChange",
      "TopologyOperation"
    ],
    "connectionString": "YOUR_PRIMARY_KEY",
    "secondaryConnectionString": "YOUR_SECONDARY_KEY",
    "path": "YOUR_TOPIC_NAME.westus-1.eventgrid.azure.net"
  }
  ```

    | Value | Substitua por |
    | --- | --- |
    | YOUR_PRIMARY_KEY | A cadeia de conexão primária usada para autenticar|
    | YOUR_SECONDARY_KEY | A cadeia de conexão secundária usada para autenticar |
    | YOUR_TOPIC_NAME | O nome do seu tópico personalizado |

- Rota para tipos de eventos de Hubs de Eventos do Azure **SensorChange**, **SpaceChange**, e **TopologyOperation**:

  ```JSON
  {
    "type": "EventHub",
    "eventTypes": [
      "SensorChange",
      "SpaceChange",
      "TopologyOperation"
    ],
    "connectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_PRIMARY_KEY",
    "secondaryConnectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SECONDARY_KEY",
    "path": "YOUR_EVENT_HUB_NAME"
  }
  ```

    | Value | Substitua por |
    | --- | --- |
    | YOUR_NAMESPACE | O namespace do seu ponto de extremidade |
    | YOUR_PRIMARY_KEY | A cadeia de conexão primária usada para autenticar |
    | YOUR_SECONDARY_KEY | A cadeia de conexão secundária usada para autenticar |
    | YOUR_EVENT_HUB_NAME | O nome do hub de eventos |

- Rotear para o tipo de evento Hubs de Eventos **DeviceMessage**. A inclusão de `EntityPath` no **connectionString** é obrigatório:

  ```JSON
  {
    "type": "EventHub",
    "eventTypes": [
      "DeviceMessage"
    ],
    "connectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_PRIMARY_KEY;EntityPath=YOUR_EVENT_HUB_NAME",
    "secondaryConnectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SECONDARY_KEY;EntityPath=YOUR_EVENT_HUB_NAME",
    "path": "YOUR_EVENT_HUB_NAME"
  }
  ```

    | Value | Substitua por |
    | --- | --- |
    | YOUR_NAMESPACE | O namespace do seu ponto de extremidade |
    | YOUR_PRIMARY_KEY | A cadeia de conexão primária usada para autenticar |
    | YOUR_SECONDARY_KEY | A cadeia de conexão secundária usada para autenticar |
    | YOUR_EVENT_HUB_NAME | O nome do hub de eventos |

> [!NOTE]  
> Após a criação de um novo endpoint, pode levar de 5 a 10 minutos para começar a receber eventos no endpoint.

## <a name="primary-and-secondary-connection-keys"></a>Chaves de conexão primária e secundária

Quando uma chave de conexão primária se torna não autorizada, o sistema tenta automaticamente a chave de conexão secundária. Isso fornece um backup e permite a possibilidade de autenticar e atualizar a chave primária através da API do Endpoints.

Se ambas as chaves de conexão primária e secundária não forem autorizadas, o sistema entrará em um tempo de espera de recuo exponencial de até 30 minutos. Os eventos são descartados em cada tempo de espera de recuo acionado.

Sempre que o sistema estiver em um estado de espera de retrocesso, a atualização das chaves de conexões por meio da API do Endpoints pode levar até 30 minutos para entrar em vigor.

## <a name="unreachable-endpoints"></a>Pontos de extremidade inacessíveis

Quando um endpoint se torna inacessível, o sistema entra em um tempo de espera de recuo exponencial de até 30 minutos. Os eventos são descartados em cada tempo de espera de recuo acionado.

## <a name="next-steps"></a>Próximas etapas

- Aprenda [como usar o Swagger de Gêmeos Digitais do Azure](how-to-use-swagger.md).

- Saiba mais sobre [roteamento de eventos e mensagens](concepts-events-routing.md) em Gêmeos Digitais do Azure.
