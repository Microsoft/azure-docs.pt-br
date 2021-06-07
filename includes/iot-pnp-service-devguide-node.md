---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 10/20/2020
ms.openlocfilehash: dfeeb451912dd32770a6ae92d73de83851d9d8f6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102244859"
---
Os recursos a seguir também estão disponíveis:

- [ Documentação de referência do SDK doNode.js](/javascript/api/azure-iothub)
- [Exemplos de cliente de serviço](https://github.com/Azure/azure-iot-sdk-node/blob/master/service/samples/javascript/twin.js)
- [Exemplos de gêmeos digital](https://github.com/Azure/azure-iot-sdk-node/blob/master/service/samples/javascript/get_digital_twin.js)

## <a name="iot-hub-service-client-examples"></a>Exemplos de cliente de serviço do Hub IoT

Esta seção mostra exemplos de JavaScript usando o cliente de serviço do Hub IoT e as classes de **cliente** e **registro** . Você usa a classe de **registro** para interagir com o estado do dispositivo usando o dispositivo gêmeos. Você também pode usar a classe de **registro** para [consultar os registros de dispositivo](../articles/iot-hub/iot-hub-devguide-query-language.md) no Hub IOT. Você usa a classe de **cliente** para chamar comandos no dispositivo. O modelo [DTDL](../articles/iot-pnp/concepts-digital-twin.md) para o dispositivo define as propriedades e os comandos que o dispositivo implementa. Nos trechos de código, a `deviceId` variável contém a ID do dispositivo de plug and Play IOT registrada com o Hub IOT.

### <a name="get-the-device-twin-and-model-id"></a>Obter o dispositivo e a ID do modelo

Para obter o dispositivo de conexão e ID do modelo do dispositivo IoT Plug and Play que se conectou ao seu hub IoT:

```javascript
var Registry = require('azure-iothub').Registry;

// ...

var registry = Registry.fromConnectionString(connectionString);
registry.getTwin(deviceId, function(err, twin) {
  if (err) {
    console.error(err.message);
  } else {
    console.log('Model Id: ' + twin.modelId);
    console.log(JSON.stringify(twin, null, 2));
  }
}
```

### <a name="update-device-twin"></a>Atualizar dispositivo gêmeo

O trecho de código a seguir mostra como atualizar a `targetTemperature` propriedade em um dispositivo. O exemplo mostra como você precisa obter o "atualizar" antes de atualizá-lo. A propriedade é definida no componente padrão do dispositivo:

```javascript
var Registry = require('azure-iothub').Registry;
var registry = Registry.fromConnectionString(connectionString);

registry.getTwin(deviceId, function(err, twin) {
  if (err) {
    console.error(err.message);
  } else {
    var twinPatch = {
      properties: {
        desired: {
          targetTemperature: 42
        }
      }
    };
    twin.update(twinPatch, function(err, twin) {
      if (err) {
        console.error(err.message);
      } else {
        console.log(JSON.stringify(twin, null, 2));
      }
    }
  }
}
```

O trecho a seguir mostra como atualizar a `targetTemperature` propriedade em um componente. O exemplo mostra como você precisa obter o "atualizar" antes de atualizá-lo. A propriedade é definida no componente **thermostat1** :

```javascript
var Registry = require('azure-iothub').Registry;
var registry = Registry.fromConnectionString(connectionString);

registry.getTwin(deviceId, function(err, twin) {
  if (err) {
    console.error(err.message);
  } else {
    var twinPatch = {
      properties: {
        desired: {
          thermostat1:
          {
            __t: "c",
            targetTemperature: 45
          }
        }
      }
    };
    twin.update(twinPatch, function(err, twin) {
      if (err) {
        console.error(err.message);
      } else {
        console.log(JSON.stringify(twin, null, 2));
      }
    }
  }
}
```

Para uma propriedade em um componente, o patch da propriedade é semelhante ao exemplo a seguir:

```json
{
  "thermostat1":
  {
    "__t": "c",
    "targetTemperature": 20
  }
}
```

### <a name="call-command"></a>Chamar comando

O trecho a seguir mostra como invocar o `getMaxMinReport` comando definido em um componente padrão:

```javascript
var Client = require('azure-iothub').Client;

// ...

var client = Client.fromConnectionString(connectionString);

var methodParams = {
  methodName: "getMaxMinReport",
  payload: new Date().getMinutes -2,
  responseTimeoutInSeconds: 15
};

client.invokeDeviceMethod(deviceId, methodParams, function (err, result) {
  if (err) {
    console.error('Failed to invoke method \'' + methodParams.methodName + '\': ' + err.message);
  } else {
    console.log(methodParams.methodName + ' on ' + deviceId + ':');
    console.log(JSON.stringify(result, null, 2));
  }
});
```

O trecho a seguir mostra como chamar o `getMaxMinReport` comando em um componente. O comando é definido no componente **thermostat1** :

```javascript
var Client = require('azure-iothub').Client;

// ...

var client = Client.fromConnectionString(connectionString);

var methodParams = {
  methodName: "thermostat1*getMaxMinReport",
  payload: new Date().getMinutes -2,
  responseTimeoutInSeconds: 15
};

client.invokeDeviceMethod(deviceId, methodParams, function (err, result) {
  if (err) {
    console.error('Failed to invoke method \'' + methodParams.methodName + '\': ' + err.message);
  } else {
    console.log(methodParams.methodName + ' on ' + deviceId + ':');
    console.log(JSON.stringify(result, null, 2));
  }
});
```

## <a name="iot-hub-digital-twin-examples"></a>Exemplos de entrelaçar digitais do Hub IoT

Você usa a classe **DigitalTwinClient** para interagir com o estado do dispositivo usando o gêmeos digital. O modelo [DTDL](../articles/iot-pnp/concepts-digital-twin.md) para o dispositivo define as propriedades e os comandos que o dispositivo implementa.

Esta seção mostra exemplos de JavaScript usando a API digital gêmeos.

A `digitalTwinId` variável contém a ID do dispositivo de plug and Play IOT registrada com o Hub IOT.

### <a name="get-the-digital-twin-and-model-id"></a>Obter a ID de modelo e a. digital

Para obter a ID de modelo e conexão digital do dispositivo IoT Plug and Play que se conectou ao seu hub IoT:

```javascript
const IoTHubTokenCredentials = require('azure-iothub').IoTHubTokenCredentials;
const DigitalTwinClient = require('azure-iothub').DigitalTwinClient;
const { inspect } = require('util');

// ...

const credentials = new IoTHubTokenCredentials(connectionString);
const digitalTwinClient = new DigitalTwinClient(credentials);

const digitalTwin = await digitalTwinClient.getDigitalTwin(digitalTwinId);

console.log(inspect(digitalTwin));
console.log('Model Id: ' + inspect(digitalTwin.$metadata.$model));
```

### <a name="update-digital-twin"></a>Atualizar atualização digital

O trecho de código a seguir mostra como atualizar a `targetTemperature` propriedade em um dispositivo. A propriedade é definida no componente padrão do dispositivo:

```javascript
const IoTHubTokenCredentials = require('azure-iothub').IoTHubTokenCredentials;
const DigitalTwinClient = require('azure-iothub').DigitalTwinClient;

// ...

const credentials = new IoTHubTokenCredentials(connString);
const digitalTwinClient = new DigitalTwinClient(credentials);

const patch = [{
  op: 'add',
  path: '/targetTemperature',
  value: 42
}];
await digitalTwinClient.updateDigitalTwin(digitalTwinId, patch);
```

O trecho a seguir mostra como atualizar a `targetTemperature` propriedade em um componente. A propriedade é definida no componente **thermostat1** :

```javascript
const IoTHubTokenCredentials = require('azure-iothub').IoTHubTokenCredentials;
const DigitalTwinClient = require('azure-iothub').DigitalTwinClient;

// ...

const credentials = new IoTHubTokenCredentials(connString);
const digitalTwinClient = new DigitalTwinClient(credentials);

const patch = [{
  op: 'add',
  path: '/thermostat1/targetTemperature',
  value: 42
}];
await digitalTwinClient.updateDigitalTwin(digitalTwinId, patch);
```

### <a name="call-command"></a>Chamar comando

O trecho a seguir mostra como invocar o `getMaxMinReport` comando definido em um componente padrão:

```javascript
const IoTHubTokenCredentials = require('azure-iothub').IoTHubTokenCredentials;
const DigitalTwinClient = require('azure-iothub').DigitalTwinClient;
const { inspect } = require('util');

// ...

const commandPayload = new Date().getMinutes -2;

const credentials = new IoTHubTokenCredentials(connectionString);
const digitalTwinClient = new DigitalTwinClient(credentials);

const options = {
  connectTimeoutInSeconds: 30,
  responseTimeoutInSeconds: 40
};
const commandResponse = await digitalTwinClient.invokeCommand(digitalTwinId, "getMaxMinReport", commandPayload, options);

console.log(inspect(commandResponse));
```

O trecho a seguir mostra como chamar o `getMaxMinReport` comando em um componente. O comando é definido no componente **thermostat1** :

```javascript
const IoTHubTokenCredentials = require('azure-iothub').IoTHubTokenCredentials;
const DigitalTwinClient = require('azure-iothub').DigitalTwinClient;
const { inspect } = require('util');

// ...

const commandPayload = new Date().getMinutes -2;

const credentials = new IoTHubTokenCredentials(connectionString);
const digitalTwinClient = new DigitalTwinClient(credentials);

const options = {
  connectTimeoutInSeconds: 30,
  responseTimeoutInSeconds: 40
};
const commandResponse = await digitalTwinClient.invokeComponentCommand(digitalTwinId, "thermostat1", "getMaxMinReport", commandPayload, options);

console.log(inspect(commandResponse));
```

## <a name="read-device-telemetry"></a>Ler telemetria do dispositivo

Os dispositivos IoT Plug and Play enviam a telemetria definida no modelo DTDL para o Hub IoT. Por padrão, o Hub IoT roteia a telemetria para um ponto de extremidade de hubs de eventos, onde você pode consumi-la. Para saber mais, confira [usar o roteamento de mensagens do Hub IOT para enviar mensagens do dispositivo para a nuvem a diferentes pontos de extremidade](../articles/iot-hub/iot-hub-devguide-messages-d2c.md).

O trecho de código a seguir mostra como ler a telemetria do ponto de extremidade de hubs de eventos padrão. O código neste trecho é obtido do início rápido do Hub IoT [Enviar telemetria de um dispositivo para um hub IOT e lê-lo com um aplicativo de back-end](../articles/iot-hub/quickstart-send-telemetry-node.md):

```javascript
const { EventHubConsumerClient } = require("@azure/event-hubs");

var printError = function (err) {
  console.log(err.message);
};

var printMessages = function (messages) {
  for (const message of messages) {
    console.log("Telemetry received: ");
    console.log(JSON.stringify(message.body));
    console.log("Properties (set by device): ");
    console.log(JSON.stringify(message.properties));
    console.log("System properties (set by IoT Hub): ");
    console.log(JSON.stringify(message.systemProperties));
    console.log("");
  }
};

// ...

const clientOptions = {};

const consumerClient = new EventHubConsumerClient("$Default", connectionString, clientOptions);

consumerClient.subscribe({
  processEvents: printMessages,
  processError: printError,
});
```

A saída a seguir do código anterior mostra a telemetria de temperatura enviada pelo dispositivo de Plug and Play IOT **TemperatureController** de vários componentes. A `dt-subject` Propriedade System mostra o nome do componente que enviou a telemetria. Neste exemplo, os dois componentes são `thermostat1` e `thermostat2` , conforme definido no modelo DTDL. A `dt-dataschema` Propriedade System mostra a ID do modelo:

```cmd/sh
Telemetry received:
{"temperature":68.77370855171125}
Properties (set by device):
undefined
System properties (set by IoT Hub):
{"iothub-connection-device-id":"my-pnp-device","iothub-connection-auth-method":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","iothub-connection-auth-generation-id":"637388034455888246","iothub-enqueuedtime":1603206669320,"iothub-message-source":"Telemetry","dt-subject":"thermostat1","dt-dataschema":"dtmi:com:example:TemperatureController;1","contentType":"application/json","contentEncoding":"utf-8"}

Telemetry received:
{"temperature":30.833394506549226}
Properties (set by device):
undefined
System properties (set by IoT Hub):
{"iothub-connection-device-id":"my-pnp-device","iothub-connection-auth-method":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","iothub-connection-auth-generation-id":"637388034455888246","iothub-enqueuedtime":1603206665835,"iothub-message-source":"Telemetry","dt-subject":"thermostat2","dt-dataschema":"dtmi:com:example:TemperatureController;1","contentType":"application/json","contentEncoding":"utf-8"}
```

## <a name="read-device-twin-change-notifications"></a>Ler notificações de alteração de troca de dispositivo

Você pode configurar o Hub IoT para gerar notificações de alteração de troca de dispositivo para rotear para um ponto de extremidade com suporte. Para saber mais, confira [usar o roteamento de mensagens do Hub IOT para enviar mensagens do dispositivo para a nuvem a diferentes pontos de extremidade > eventos de não telemetria](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events).

O código mostrado no trecho de código JavaScript anterior gera a saída a seguir quando o Hub IoT gera notificações de alteração de dispositivo para um dispositivo termostato sem componente. As propriedades do aplicativo `iothub-message-schema` e `opType` fornecem informações sobre o tipo de notificação de alteração:

```cmd/sh
Telemetry received:
{"version":4,"properties":{"reported":{"maxTempSinceLastReboot":42.1415152639582,"$metadata":{"$lastUpdated":"2020-10-21T10:01:40.1281138Z","maxTempSinceLastReboot":{"$lastUpdated":"2020-10-21T10:01:40.1281138Z"}},"$version":3}}}
Properties (set by device):
{"hubName":"my-pnp-hub","deviceId":"my-pnp-device","operationTimestamp":"2020-10-21T10:01:40.1281138+00:00","iothub-message-schema":"twinChangeNotification","opType":"updateTwin"}
System properties (set by IoT Hub):
{"iothub-connection-device-id":"my-pnp-device","iothub-enqueuedtime":1603274500282,"iothub-message-source":"twinChangeEvents","userId":{"type":"Buffer","data":[109,121,45,112,110,112,45,104,117,98]},"correlationId":"11ed82d13f50","contentType":"application/json","contentEncoding":"utf-8"}
```

O código mostrado no trecho de código JavaScript anterior gera a saída a seguir quando o Hub IoT gera notificações de alteração de dispositivo de troca para um dispositivo com componentes. Este exemplo mostra a saída quando um dispositivo sensor de temperatura com um componente termostato gera notificações. As propriedades do aplicativo `iothub-message-schema` e `opType` fornecem informações sobre o tipo de notificação de alteração:

```cmd/sh
Telemetry received:
{"version":4,"properties":{"reported":{"thermostat1":{"maxTempSinceLastReboot":3.5592971602417913,"__t":"c"},"$metadata":{"$lastUpdated":"2020-10-21T10:07:51.8284866Z","thermostat1":{"$lastUpdated":"2020-10-21T10:07:51.8284866Z","maxTempSinceLastReboot":{"$lastUpdated":"2020-10-21T10:07:51.8284866Z"},"__t":{"$lastUpdated":"2020-10-21T10:07:51.8284866Z"}}},"$version":3}}}
Properties (set by device):
{"hubName":"my-pnp-hub","deviceId":"my-pnp-device","operationTimestamp":"2020-10-21T10:07:51.8284866+00:00","iothub-message-schema":"twinChangeNotification","opType":"updateTwin"}
System properties (set by IoT Hub):
{"iothub-connection-device-id":"my-pnp-device","iothub-enqueuedtime":1603274871951,"iothub-message-source":"twinChangeEvents","userId":{"type":"Buffer","data":[109,121,45,112,110,112,45,104,117,98]},"correlationId":"11ee605b195f","contentType":"application/json","contentEncoding":"utf-8"}
```

## <a name="read-digital-twin-change-notifications"></a>Ler notificações de alteração de troca digital

Você pode configurar o Hub IoT para gerar notificações de alteração de troca digital para rotear para um ponto de extremidade com suporte. Para saber mais, confira [usar o roteamento de mensagens do Hub IOT para enviar mensagens do dispositivo para a nuvem a diferentes pontos de extremidade > eventos de não telemetria](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events).

O código mostrado no trecho de código JavaScript anterior gera a saída a seguir quando o Hub IoT gera notificações de alteração de troca digital para um dispositivo termostato sem componente. As propriedades do aplicativo `iothub-message-schema` e `opType` fornecem informações sobre o tipo de notificação de alteração:

```cmd/sh
Telemetry received:
[{"op":"add","path":"/$metadata/maxTempSinceLastReboot","value":{"lastUpdateTime":"2020-10-21T10:01:40.1281138Z"}},{"op":"add","path":"/maxTempSinceLastReboot","value":42.1415152639582}]
Properties (set by device):
{"hubName":"my-pnp-hub","deviceId":"my-pnp-device","operationTimestamp":"2020-10-21T10:01:40.1281138+00:00","iothub-message-schema":"digitalTwinChangeNotification","opType":"updateTwin"}
System properties (set by IoT Hub):
{"iothub-connection-device-id":"my-pnp-device","iothub-enqueuedtime":1603274500282,"iothub-message-source":"digitalTwinChangeEvents","userId":{"type":"Buffer","data":[109,121,45,112,110,112,45,104,117,98]},"correlationId":"11ed82d13f50","contentType":"application/json-patch+json","contentEncoding":"utf-8"}
```

O código mostrado no trecho de código JavaScript anterior gera a saída a seguir quando o Hub IoT gera notificações de alteração de troca digital para um dispositivo com componentes. Este exemplo mostra a saída quando um dispositivo sensor de temperatura com um componente termostato gera notificações. As propriedades do aplicativo `iothub-message-schema` e `opType` fornecem informações sobre o tipo de notificação de alteração:

```cmd/sh
Telemetry received:
[{"op":"add","path":"/thermostat1","value":{"$metadata":{"maxTempSinceLastReboot":{"lastUpdateTime":"2020-10-21T10:07:51.8284866Z"}},"maxTempSinceLastReboot":3.5592971602417913}}]
Properties (set by device):
{"hubName":"my-pnp-hub","deviceId":"my-pnp-device","operationTimestamp":"2020-10-21T10:07:51.8284866+00:00","iothub-message-schema":"digitalTwinChangeNotification","opType":"updateTwin"}
System properties (set by IoT Hub):
{"iothub-connection-device-id":"my-pnp-device","iothub-enqueuedtime":1603274871951,"iothub-message-source":"digitalTwinChangeEvents","userId":{"type":"Buffer","data":[109,121,45,112,110,112,45,104,117,98]},"correlationId":"11ee605b195f","contentType":"application/json-patch+json","contentEncoding":"utf-8"}
```
