---
title: Entender os gêmeos digitais de IoT Plug and Play
description: Entenda como o Plug and Play IoT usa o gêmeos digital
author: prashmo
ms.author: prashmo
ms.date: 12/14/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 99c957e5bf6ffe69c94e109796590f5ab975c3cf
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97656879"
---
# <a name="understand-iot-plug-and-play-digital-twins"></a>Entender os gêmeos digitais de IoT Plug and Play

Um dispositivo de Plug and Play IoT implementa um modelo descrito pelo esquema [DTDL (digital gêmeos Definition Language)](https://github.com/Azure/opendigitaltwins-dtdl) . Um modelo descreve o conjunto de componentes, propriedades, comandos e mensagens de telemetria que um dispositivo específico pode ter.

O IoT Plug and Play usa a versão 2 do DTDL. Para obter mais informações sobre esta versão, consulte a especificação do [DTDL (digital gêmeos Definition Language)-versão 2](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) no github.

> [!NOTE]
> DTDL não é exclusivo para Plug and Play de IoT. Outros serviços de IoT, como o [Azure digital gêmeos](../digital-twins/overview.md), o usam para representar ambientes inteiros, como edifícios e redes de energia.

Os SDKs do serviço IoT do Azure incluem APIs que permitem que um serviço interaja com o digital de um dispositivo. Por exemplo, um serviço pode ler as propriedades do dispositivo a partir de um email digital ou usar o FileUp digital para chamar um comando em um dispositivo. Para saber mais, confira exemplos de número de informações [digitais do Hub IOT](concepts-developer-guide-service.md#iot-hub-digital-twin-examples).

O dispositivo de Plug and Play de IoT de exemplo neste artigo implementa um [modelo de controlador de temperatura](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) que tem componentes [termostato](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) .

## <a name="device-twins-and-digital-twins"></a>Dispositivo gêmeos e gêmeos digital

Bem como uma conexão digital, o Hub IoT do Azure também mantém um *dispositivo* para cada dispositivo conectado. Um dispositivo "r" é semelhante a um "digital", pois é uma representação das propriedades de um dispositivo. Os SDKs do serviço IoT do Azure incluem APIs para interagir com dispositivos gêmeos.

Um hub IoT Inicializa uma conexão digital e um dispositivo se conecta na primeira vez que um dispositivo de Plug and Play IoT é conectado.

Dispositivos gêmeos são documentos JSON que armazenam informações de estado do dispositivo, incluindo metadados, configurações e condições. Para saber mais, consulte [exemplos de cliente de serviço do Hub IOT](concepts-developer-guide-service.md#iot-hub-service-client-examples). Os integradores de dispositivo e de solução podem continuar a usar o mesmo conjunto de APIs e SDKs do dispositivo para implementar dispositivos e soluções usando as convenções de Plug and Play de IoT.

As APIs de alta do digital operam em construções DTDL de alto nível, como componentes, propriedades e comandos. As APIs de teledigital fotodigitais facilitam para os criadores de solução criar soluções de Plug and Play de IoT.

Em um dispositivo, o estado de uma propriedade gravável é dividido nas seções *propriedades desejadas* e *Propriedades relatadas* . Todas as propriedades somente leitura estão disponíveis na seção Propriedades relatadas.

Em uma folha de erro digital, há uma exibição unificada do estado atual e desejado da propriedade. O estado de sincronização de uma determinada propriedade é armazenado na seção componente padrão correspondente `$metadata` .

### <a name="device-twin-json-example"></a>Exemplo de JSON de dispositivo

O trecho de código a seguir mostra um dispositivo de Plug and Play IoT em formato "r" formatado como um objeto JSON:

```json
{
  "deviceId": "sample-device",
  "modelId": "dtmi:com:example:TemperatureController;1",
  "version": 15,
  "properties": {
    "desired": {
      "thermostat1": {
        "__t": "c",
        "targetTemperature": 21.8
      },
      "$metadata": {...},
      "$version": 4
    },
    "reported": {
      "serialNumber": "alwinexlepaho8329",
      "thermostat1": {
        "maxTempSinceLastReboot": 25.3,
        "__t": "c",
        "targetTemperature": {
          "value": 21.8,
          "ac": 200,
          "ad": "Successfully executed patch",
        }
      },
      "$metadata": {...},
      "$version": 11
    }
  }
}
```

### <a name="digital-twin-example"></a>Exemplo de "mydigital]

O trecho de código a seguir mostra o formato digital de entrelaçamento como um objeto JSON:

```json
{
  "$dtId": "sample-device",
  "serialNumber": "alwinexlepaho8329",
  "thermostat1": {
    "maxTempSinceLastReboot": 25.3,
    "targetTemperature": 21.8,
    "$metadata": {
      "targetTemperature": {
        "desiredValue": 21.8,
        "desiredVersion": 4,
        "ackVersion": 4,
        "ackCode": 200,
        "ackDescription": "Successfully executed patch",
        "lastUpdateTime": "2020-07-17T06:11:04.9309159Z"
      },
      "maxTempSinceLastReboot": {
         "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
      }
    }
  },
  "$metadata": {
    "$model": "dtmi:com:example:TemperatureController;1",
    "serialNumber": {
      "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
    }
  }
}
```

A tabela a seguir descreve os campos no objeto JSON digital.

| Nome do campo | Descrição |
| --- | --- |
| `$dtId` | Uma cadeia de caracteres fornecida pelo usuário que representa a ID do dispositivo digital de Altova |
| `{propertyName}` | O valor de uma propriedade em JSON |
| `$metadata.$model` | Adicional A ID da interface do modelo que caracteriza este teledigital |
| `$metadata.{propertyName}.desiredValue` | [Somente para propriedades graváveis] O valor desejado da propriedade especificada |
| `$metadata.{propertyName}.desiredVersion` | [Somente para propriedades graváveis] A versão do valor desejado mantido pelo Hub IoT|
| `$metadata.{propertyName}.ackVersion` | [Necessário, somente para propriedades graváveis] A versão reconhecida pelo dispositivo que está implementando a teledigital, ela deve ser maior ou igual à versão desejada |
| `$metadata.{propertyName}.ackCode` | [Necessário, somente para propriedades graváveis] O `ack` código retornado pelo aplicativo de dispositivo que implementa o teledigital |
| `$metadata.{propertyName}.ackDescription` | [Opcional, somente para propriedades graváveis] A `ack` Descrição retornada pelo aplicativo de dispositivo que implementa o teledigital |
| `$metadata.{propertyName}.lastUpdateTime` | O Hub IoT mantém o carimbo de data/hora da última atualização da propriedade pelo dispositivo. Os carimbos de data/hora estão em UTC e são codificados no formato ISO8601 AAAA-MM-DDTHH: MM: SS. mmmZ |
| `{componentName}` | Um objeto JSON que contém os valores de propriedade e metadados do componente. |
| `{componentName}.{propertyName}` | O valor da Propriedade do componente em JSON |
| `{componentName}.$metadata` | As informações de metadados para o componente. |

### <a name="properties"></a>Propriedades

As propriedades são campos de dados que representam o estado de uma entidade (como as propriedades em muitas linguagens de programação orientada a objeto).

#### <a name="read-only-property"></a>Propriedade somente leitura

Esquema DTDL:

```json
{
    "@type": "Property",
    "name": "serialNumber",
    "displayName": "Serial Number",
    "description": "Serial number of the device.",
    "schema": "string"
}
```

Neste exemplo, `alwinexlepaho8329` é o valor atual da `serialNumber` propriedade somente leitura relatada pelo dispositivo.
Os trechos de código a seguir mostram a representação JSON lado a lado da `serialNumber` Propriedade:

:::row:::
   :::column span="":::
      **Dispositivo gêmeo**

```json
"properties": {
  "reported": {
    "serialNumber": "alwinexlepaho8329"
  }
}
```

   :::column-end:::
   :::column span="":::
      **Gêmeo digital**

```json
"serialNumber": "alwinexlepaho8329"
```

   :::column-end:::
:::row-end:::

#### <a name="writable-property"></a>Propriedade gravável

Os exemplos a seguir mostram uma propriedade gravável no componente padrão.

DTDL:

```json
{
  "@type": "Property",
  "name": "fanSpeed",
  "displayName": "Fan Speed",
  "writable": true,
  "schema": "double"
}
```

:::row:::
   :::column span="":::
      **Dispositivo gêmeo**

```json
{
  "properties": {
    "desired": {
      "fanSpeed": 2.0,
    },
    "reported": {
      "fanSpeed": {
        "value": 3.0,
        "ac": 200,
        "av": 1,
        "ad": "Successfully executed patch version 1"
      }
    }
  },
}
```

   :::column-end:::
   :::column span="":::
      **Gêmeo digital**

```json
{
  "fanSpeed": 3.0,
  "$metadata": {
    "fanSpeed": {
      "desiredValue": 2.0,
      "desiredVersion": 2,
      "ackVersion": 1,
      "ackCode": 200,
      "ackDescription": "Successfully executed patch version 1",
      "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
    }
  }
}
```

   :::column-end:::
:::row-end:::

Neste exemplo, `3.0` é o valor atual da `fanSpeed` Propriedade relatada pelo dispositivo. `2.0` é o valor desejado definido pela solução. O valor desejado e o estado de sincronização de uma propriedade no nível da raiz são definidos no nível da raiz para um conjunto de bits `$metadata` digital. Quando o dispositivo fica online, ele pode aplicar essa atualização e reportar o valor atualizado.

### <a name="components"></a>Componentes

Os componentes permitem a criação de interface de modelo como um assembly de outras interfaces.
Por exemplo, a interface [termostato](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) pode ser incorporada como componentes `thermostat1` e  `thermostat2` no modelo de modelo de controlador de [temperatura](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) .

Em um dispositivo, um componente é identificado pelo `{ "__t": "c"}` marcador. Em uma teledigital, a presença de `$metadata` marca um componente.

Neste exemplo, `thermostat1` é um componente com duas propriedades:

- `maxTempSinceLastReboot` é uma propriedade somente leitura.
- `targetTemperature` é uma propriedade gravável que foi sincronizada com êxito pelo dispositivo. O valor desejado e o estado de sincronização dessas propriedades estão no componente `$metadata` .

Os trechos de código a seguir mostram a representação JSON lado a lado do `thermostat1` componente:

:::row:::
   :::column span="":::
      **Dispositivo gêmeo**

```json
"properties": {
  "desired": {
    "thermostat1": {
      "__t": "c",
      "targetTemperature": 21.8
    },
    "$metadata": {
    },
    "$version": 4
  },
  "reported": {
    "thermostat1": {
      "maxTempSinceLastReboot": 25.3,
      "__t": "c",
      "targetTemperature": {
        "value": 21.8,
        "ac": 200,
        "ad": "Successfully executed patch",
        "av": 4
      }
    },
    "$metadata": {
    },
    "$version": 11
  }
}
```

   :::column-end:::
   :::column span="":::
      **Gêmeo digital**

```json
"thermostat1": {
  "maxTempSinceLastReboot": 25.3,
  "targetTemperature": 21.8,
  "$metadata": {
    "targetTemperature": {
      "desiredValue": 21.8,
      "desiredVersion": 4,
      "ackVersion": 4,
      "ackCode": 200,
      "ackDescription": "Successfully executed patch",
      "lastUpdateTime": "2020-07-17T06:11:04.9309159Z"
    },
    "maxTempSinceLastReboot": {
       "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
    }
  }
}
```

   :::column-end:::
:::row-end:::

## <a name="digital-twin-apis"></a>APIs de entrelaçamento digital

As APIs de atualização digitais incluem **Get digital** up, **Update digital** up, **Invoke Component Command** e **Invoke Command** Operations, mais gerenciando uma atualização digital. Você pode usar as [APIs REST](/rest/api/iothub/service/digitaltwin) diretamente ou por meio de um [SDK de serviço](../iot-pnp/libraries-sdks.md).

## <a name="digital-twin-change-events"></a>Eventos de alteração de gêmeo digital

Quando os eventos de alteração do gêmeo digital são habilitados, um evento é disparado sempre que o valor atual ou o desejado do componente ou da propriedade é alterado. Os eventos de alteração de troca digital são gerados no formato de [patch JSON](http://jsonpatch.com/) . Os eventos correspondentes serão gerados no formato de dispositivo de troca se os eventos de alteração de entrelaçamento estiverem habilitados.

Para saber como habilitar o roteamento para eventos de dispositivo e de mensagens digitais, consulte [usar o roteamento de mensagem do Hub IOT para enviar mensagens do dispositivo para a nuvem a diferentes pontos de extremidade](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events). Para entender o formato da mensagem, consulte [criar e ler mensagens do Hub IOT](../iot-hub/iot-hub-devguide-messages-construct.md).

Por exemplo, o seguinte evento de alteração de troca digital é disparado quando `targetTemperature` é definido pela solução:

```json
iothub-connection-device-id:sample-device
iothub-enqueuedtime:7/17/2020 6:11:04 AM
iothub-message-source:digitalTwinChangeEvents
correlation-id:275d463fa034
content-type:application/json-patch+json
content-encoding:utf-8
[
  {
    "op": "add",
    "path": "/thermostat1/$metadata/targetTemperature",
    "value": {
      "desiredValue": 21.8,
      "desiredVersion": 4
    }
  }
]
```

O seguinte evento de alteração de troca digital é disparado quando o dispositivo relata que a alteração desejada acima foi aplicada:

```json
iothub-connection-device-id:sample-device
iothub-enqueuedtime:7/17/2020 6:11:05 AM
iothub-message-source:digitalTwinChangeEvents
correlation-id:275d464a2c80
content-type:application/json-patch+json
content-encoding:utf-8
[
  {
    "op": "add",
    "path": "/thermostat1/$metadata/targetTemperature/ackCode",
    "value": 200
  },
  {
    "op": "add",
    "path": "/thermostat1/$metadata/targetTemperature/ackDescription",
    "value": "Successfully executed patch"
  },
  {
    "op": "add",
    "path": "/thermostat1/$metadata/targetTemperature/ackVersion",
    "value": 4
  },
  {
    "op": "add",
    "path": "/thermostat1/$metadata/targetTemperature/lastUpdateTime",
    "value": "2020-07-17T06:11:04.9309159Z"
  },
  {
    "op": "add",
    "path": "/thermostat1/targetTemperature",
    "value": 21.8
  }
]
```

> [!NOTE]
> As mensagens de notificação de alteração de troca são duplas quando ativadas tanto no dispositivo quanto na notificação de alteração de mensagens digitais.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu sobre o digital gêmeos, aqui estão alguns recursos adicionais:

- [Como usar as APIs de Plug and Play do IoT digital](howto-manage-digital-twin.md)
- [Interagir com um dispositivo da sua solução](quickstart-service.md)
- [API REST de monodigital de IoT](/rest/api/iothub/service/digitaltwin)
- [Azure IoT Explorer](howto-use-iot-explorer.md)