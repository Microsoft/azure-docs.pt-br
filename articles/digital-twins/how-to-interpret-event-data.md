---
title: Interpretar dados de eventos
titleSuffix: Azure Digital Twins
description: Veja como interpretar diferentes tipos de evento e suas diferentes mensagens de notificação.
author: baanders
ms.author: baanders
ms.date: 6/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: a0f2b971eae5d37e8fb0771e213075289af6c519
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98045250"
---
# <a name="understand-event-data"></a>Entender os dados do evento

Eventos diferentes no Azure digital gêmeos produzem **notificações**, que permitem que o back-end da solução esteja ciente quando diferentes ações estão ocorrendo. Em seguida, eles são [roteados](concepts-route-events.md) para diferentes locais dentro e fora do Azure digital gêmeos que podem usar essas informações para tomar medidas.

Há vários tipos de notificações que podem ser geradas e mensagens de notificação podem parecer diferentes dependendo do tipo de evento gerado. Este artigo fornece detalhes sobre os diferentes tipos de mensagens e o que eles podem ter.

Este gráfico mostra os diferentes tipos de notificação:

[!INCLUDE [digital-twins-notifications.md](../../includes/digital-twins-notifications.md)]

Em geral, as notificações são feitas de duas partes: o cabeçalho e o corpo. 

### <a name="event-notification-headers"></a>Cabeçalhos de notificação de eventos

Os cabeçalhos de mensagens de notificação são representados com pares chave-valor. Dependendo do protocolo usado (MQTT, AMQP ou HTTP), os cabeçalhos de mensagem serão serializados de forma diferente. Esta seção discute informações gerais de cabeçalho para mensagens de notificação, independentemente do protocolo e da serialização específicos escolhidos.

Algumas notificações estão em conformidade com o padrão [CloudEvents](https://cloudevents.io/) . A conformidade do CloudEvents é a seguinte:
* As notificações emitidas de dispositivos continuam a seguir as especificações existentes para notificações
* As notificações processadas e emitidas pelo Hub IoT continuam a seguir as especificações existentes para notificação, exceto quando o Hub IoT opta por oferecer suporte a CloudEvents, como por meio da grade de eventos
* Notificações emitidas do [gêmeos digital](concepts-twins-graph.md) com um [modelo](concepts-models.md) em conformidade com o CloudEvents
* Notificações processadas e emitidas pelo Azure digital gêmeos estão em conformidade com o CloudEvents

Os serviços precisam adicionar um número de sequência em todas as notificações para indicar sua ordem ou manter sua própria ordenação de alguma outra maneira. 

As notificações emitidas pelo Azure digital gêmeos para a grade de eventos serão formatadas automaticamente para o esquema CloudEvents ou o esquema EventGridEvent, dependendo do tipo de esquema definido no tópico da grade de eventos. 

Os atributos de extensão nos cabeçalhos serão adicionados como propriedades no esquema da grade de eventos dentro da carga. 

### <a name="event-notification-bodies"></a>Corpos de notificação de eventos

Os corpos de mensagens de notificação são descritos aqui no JSON. Dependendo da serialização desejada para o corpo da mensagem (como com JSON, CBOR, Protobuf, etc.), o corpo da mensagem pode ser serializado de forma diferente.

O conjunto de campos que o corpo contém varia de acordo com diferentes tipos de notificação. Aqui estão dois corpos de mensagens de exemplo, para ter uma ideia do que eles geralmente parecem e podem incluir.

Mensagem de telemetria:

```json
{
  "specversion": "1.0",
  "id": "df5a5992-817b-4e8a-b12c-e0b18d4bf8fb",
  "type": "microsoft.iot.telemetry",
  "source": "contoso-adt.api.wus2.digitaltwins.azure.net/digitaltwins/room1",
  "data": {
    "Temperature": 10
  },
  "dataschema": "dtmi:example:com:floor4;2",
  "datacontenttype": "application/json",
  "traceparent": "00-7e3081c6d3edfb4eaf7d3244b2036baa-23d762f4d9f81741-01"
}
```

Mensagem de notificação do ciclo de vida:

```json
{
  "specversion": "1.0",
  "id": "d047e992-dddc-4a5a-b0af-fa79832235f8",
  "type": "Microsoft.DigitalTwins.Twin.Create",
  "source": "contoso-adt.api.wus2.digitaltwins.azure.net",
  "data": {
    "$dtId": "floor1",
    "$etag": "W/\"e398dbf4-8214-4483-9d52-880b61e491ec\"",
    "$metadata": {
      "$model": "dtmi:example:Floor;1"
    }
  },
  "subject": "floor1",
  "time": "2020-06-23T19:03:48.9700792Z",
  "datacontenttype": "application/json",
  "traceparent": "00-18f4e34b3e4a784aadf5913917537e7d-691a71e0a220d642-01"
}
```

## <a name="message-format-detail-for-different-event-types"></a>Detalhes de formato de mensagem para diferentes tipos de evento

Esta seção apresenta mais detalhes sobre os diferentes tipos de notificações emitidos pelo Hub IoT e o gêmeos digital do Azure (ou outros serviços de IoT do Azure). Você lerá sobre as coisas que disparam cada tipo de notificação e o conjunto de campos incluídos em cada tipo de corpo de notificação.

### <a name="digital-twin-life-cycle-notifications"></a>Notificações do ciclo de vida de troca digital

Todas as notificações de emissão de [gêmeos digital](concepts-twins-graph.md) , independentemente de elas representarem [dispositivos de Hub IOT no Azure digital gêmeos](how-to-ingest-iot-hub-data.md) ou não. Isso ocorre devido a **notificações do ciclo de vida**, que são sobre o próprio digital.

As notificações do ciclo de vida são disparadas quando:
* Um teledigital é criado
* Uma teledigitais digital é excluída

#### <a name="properties"></a>Propriedades

Aqui estão os campos no corpo de uma notificação de ciclo de vida.

| Nome | Valor |
| --- | --- |
| `id` | Identificador da notificação, como um UUID ou um contador mantido pelo serviço. `source` + `id` é exclusivo para cada evento distinto. |
| `source` | Nome do Hub IoT ou instância do gêmeos digital do Azure, como *MyHub.Azure-Devices.net* ou *mydigitaltwins.westus2.azuredigitaltwins.net* |
| `specversion` | *1,0*<br>A mensagem está de acordo com esta versão da [especificação CloudEvents](https://github.com/cloudevents/spec). |
| `type` | `Microsoft.DigitalTwins.Twin.Create`<br>`Microsoft.DigitalTwins.Twin.Delete` |
| `datacontenttype` | `application/json` |
| `subject` | ID do teledigital |
| `time` | Carimbo de data/hora para quando a operação ocorreu no entrelaçamento |
| `traceparent` | Um contexto de rastreamento W3C para o evento |

#### <a name="body-details"></a>Detalhes do corpo

O corpo é o texto digital afetado, representado no formato JSON. O esquema para isso é o *recurso de gêmeos Digital 7,1*.

Para eventos de criação, a carga reflete o estado de uma cruz após a criação do recurso, portanto, ele deve incluir todos os elementos gerados pelo sistema, assim como uma `GET` chamada.

Aqui está um exemplo de um corpo para um dispositivo de [plug and Play IOT (PnP)](../iot-pnp/overview-iot-plug-and-play.md) , com componentes e nenhuma propriedade de nível superior. As propriedades que não fazem sentido para dispositivos (como propriedades relatadas) devem ser omitidas.

```json
{
  "$dtId": "device-digitaltwin-01",
  "$etag": "W/\"e59ce8f5-03c0-4356-aea9-249ecbdc07f9\"",
  "thermostat": {
    "temperature": 80,
    "humidity": 45,
    "$metadata": {
      "$model": "dtmi:com:contoso:Thermostat;1",
      "temperature": {
        "desiredValue": 85,
        "desiredVersion": 3,
        "ackVersion": 2,
        "ackCode": 200,
        "ackDescription": "OK"
      },
      "humidity": {
        "desiredValue": 40,
        "desiredVersion": 1,
        "ackVersion": 1,
        "ackCode": 200,
        "ackDescription": "OK"
      }
    }
  },
  "$metadata": {
    "$model": "dtmi:com:contoso:Thermostat_X500;1",
  }
}
```

Aqui está outro exemplo de uma página digital. Este é baseado em um [modelo](concepts-models.md)e não oferece suporte a componentes:

```json
{
  "$dtId": "logical-digitaltwin-01",
  "$etag": "W/\"e59ce8f5-03c0-4356-aea9-249ecbdc07f9\"",
  "avgTemperature": 70,
  "comfortIndex": 85,
  "$metadata": {
    "$model": "dtmi:com:contoso:Building;1",
    "avgTemperature": {
      "desiredValue": 72,
      "desiredVersion": 5,
      "ackVersion": 4,
      "ackCode": 200,
      "ackDescription": "OK"
    },
    "comfortIndex": {
      "desiredValue": 90,
      "desiredVersion": 1,
      "ackVersion": 3,
      "ackCode": 200,
      "ackDescription": "OK"
    }
  }
}
```

### <a name="digital-twin-relationship-change-notifications"></a>Notificações de alteração de relacionamento de troca digital

As **notificações de alteração de relação** são disparadas quando qualquer relação de uma troca digital é criada, atualizada ou excluída. 

#### <a name="properties"></a>Propriedades

Aqui estão os campos no corpo de uma notificação de alteração de borda.

| Nome    | Valor |
| --- | --- |
| `id` | Identificador da notificação, como um UUID ou um contador mantido pelo serviço. `source` + `id` é exclusivo para cada evento distinto |
| `source` | Nome da instância de gêmeos digital do Azure, como *mydigitaltwins.westus2.azuredigitaltwins.net* |
| `specversion` | *1,0*<br>A mensagem está de acordo com esta versão da [especificação CloudEvents](https://github.com/cloudevents/spec). |
| `type` | `Microsoft.DigitalTwins.Relationship.Create`<br>`Microsoft.DigitalTwins.Relationship.Update`<br>`Microsoft.DigitalTwins.Relationship.Delete`
|`datacontenttype`| `application/json` |
| `subject` | ID da relação, como `<twinID>/relationships/<relationshipID>` |
| `time` | Carimbo de data/hora para quando a operação ocorreu na relação |
| `traceparent` | Um contexto de rastreamento W3C para o evento |

#### <a name="body-details"></a>Detalhes do corpo

O corpo é a carga de uma relação, também no formato JSON. Ele usa o mesmo formato que uma `GET` solicitação para uma relação por meio da [API DigitalTwins](/rest/api/digital-twins/dataplane/twins). 

"Atualizar uma relação" significa que as propriedades da relação foram alteradas. 

Veja um exemplo de uma notificação de relação de atualização para atualizar uma propriedade:

```json
{
    "modelId": "dtmi:example:Floor;1",
    "patch": [
      {
        "value": "user3",
        "path": "/ownershipUser",
        "op": "replace"
      }
    ]
  }
```

Para `Relationship.Delete` , o corpo é o mesmo que a `GET` solicitação e Obtém o estado mais recente antes da exclusão.

Aqui está um exemplo de uma notificação criar ou excluir relação:

```json
{
    "$relationshipId": "device_to_device",
    "$etag": "W/\"72479873-0083-41a8-83e2-caedb932d881\"",
    "$relationshipName": "Connected",
    "$targetId": "device2",
    "connectionType": "WIFI"
}
```

### <a name="digital-twin-change-notifications"></a>Notificações de alteração de troca digital

As **notificações de alteração de troca digital** são disparadas quando um intercâmbio digital está sendo atualizado, como:
* Quando valores de propriedade ou metadados são alterados.
* Quando as alterações de metadados digitais ou de componentes forem alteradas. Um exemplo desse cenário é alterar o modelo de uma troca digital.

#### <a name="properties"></a>Propriedades

Aqui estão os campos no corpo de uma notificação de alteração de troca digital.

| Nome    | Valor |
| --- | --- |
| `id` | Identificador da notificação, como um UUID ou um contador mantido pelo serviço. `source` + `id` é exclusivo para cada evento distinto |
| `source` | Nome do Hub IoT ou instância do gêmeos digital do Azure, como *MyHub.Azure-Devices.net* ou *mydigitaltwins.westus2.azuredigitaltwins.net*
| `specversion` | *1,0*<br>A mensagem está de acordo com esta versão da [especificação CloudEvents](https://github.com/cloudevents/spec). |
| `type` | `Microsoft.DigitalTwins.Twin.Update` |
| `datacontenttype` | `application/json` |
| `subject` | ID do teledigital |
| `time` | Carimbo de data/hora para quando a operação ocorreu no teledigital |
| `traceparent` | Um contexto de rastreamento W3C para o evento |

#### <a name="body-details"></a>Detalhes do corpo

O corpo da `Twin.Update` notificação é um documento de patch JSON que contém a atualização para o digital.

Por exemplo, digamos que uma atualização digital tenha sido atualizada usando o patch a seguir.

:::code language="json" source="~/digital-twins-docs-samples/models/patch-component-2.json":::

A notificação correspondente (se executada de forma síncrona pelo serviço, como o Azure digital gêmeos atualizando um texto digital) teria um corpo como:

```json
{
    "modelId": "dtmi:example:com:floor4;2",
    "patch": [
      {
        "value": 40,
        "path": "/Temperature",
        "op": "replace"
      },
      {
        "value": 30,
        "path": "/comp1/prop1",
        "op": "add"
      }
    ]
  }
```

## <a name="next-steps"></a>Próximas etapas

Consulte como criar pontos de extremidade e rotas para entregar eventos:
* [*Como: gerenciar pontos de extremidade e rotas*](how-to-manage-routes-apis-cli.md)

Ou então, saiba mais sobre as APIs do Azure digital gêmeos e as opções do SDK:
* [*Como: usar as APIs e SDKs do gêmeos digital do Azure*](how-to-use-apis-sdks.md)