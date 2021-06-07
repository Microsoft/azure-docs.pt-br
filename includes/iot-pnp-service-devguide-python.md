---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 10/20/2020
ms.openlocfilehash: a925c3a17988ef6f4b95a1e3cf4dd5fb8baa4829
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102510960"
---
Os recursos a seguir também estão disponíveis:

- [Documentação de referência do SDK do Python](/python/api/azure-iot-hub/azure.iot.hub)
- [Exemplos de cliente de serviço](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-hub/samples/iothub_registry_manager_method_sample.py)
- [Exemplos de gêmeos digital](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-hub/samples/get_digital_twin_sample.py)

## <a name="iot-hub-service-client-examples"></a>Exemplos de cliente de serviço do Hub IoT

Esta seção mostra exemplos do Python usando o cliente de serviço do Hub IoT e as classes **IoTHubRegistryManager** e **CloudToDeviceMethod** . Você usa a classe **IoTHubRegistryManager** para interagir com o estado do dispositivo usando o dispositivo gêmeos. Você também pode usar a classe **IoTHubRegistryManager** para [consultar os registros do dispositivo](../articles/iot-hub/iot-hub-devguide-query-language.md) no Hub IOT. Você usa a classe **CloudToDeviceMethod** para chamar comandos no dispositivo. O modelo [DTDL](../articles/iot-pnp/concepts-digital-twin.md) para o dispositivo define as propriedades e os comandos que o dispositivo implementa. Nos trechos de código, a `device_id` variável contém a ID do dispositivo de plug and Play IOT registrada com o Hub IOT.

### <a name="get-the-device-twin-and-model-id"></a>Obter o dispositivo e a ID do modelo

Para obter o dispositivo de conexão e ID do modelo do dispositivo IoT Plug and Play que se conectou ao seu hub IoT:

```python
from azure.iot.hub import IoTHubRegistryManager
from azure.iot.hub.models import Twin, TwinProperties

iothub_registry_manager = IoTHubRegistryManager(iothub_connection_str)

# ...

twin = iothub_registry_manager.get_twin(device_id)
print("The device twin is: ")
print("")
print(twin)
print("")

additional_props = twin.additional_properties
if "modelId" in additional_props:
    print("The Model ID for this device is:")
    print(additional_props["modelId"])
    print("")
```

### <a name="update-device-twin"></a>Atualizar dispositivo gêmeo

O trecho de código a seguir mostra como atualizar a `targetTemperature` propriedade em um dispositivo. O exemplo mostra como você precisa obter o ' s ' `etag` antes de atualizá-lo. A propriedade é definida no componente padrão do dispositivo:

```python
iothub_registry_manager = IoTHubRegistryManager(iothub_connection_str)

twin = iothub_registry_manager.get_twin(device_id)

twin_patch = Twin()

twin_patch.properties = TwinProperties(
    desired={"targetTemperature": 42}
)
updated_twin = iothub_registry_manager.update_twin(device_id, twin_patch, twin.etag)
```

O trecho a seguir mostra como atualizar a `targetTemperature` propriedade em um componente. O exemplo mostra como você precisa obter o ' s ' `ETag` antes de atualizá-lo. A propriedade é definida no componente **thermostat1** :

```python
iothub_registry_manager = IoTHubRegistryManager(iothub_connection_str)

twin = iothub_registry_manager.get_twin(device_id)

twin_patch = Twin()

twin_patch.properties = TwinProperties(
    desired={ "thermostat1": {
        "__t": "c",
        "targetTemperature": 42}
    }
)
updated_twin = iothub_registry_manager.update_twin(device_id, twin_patch, twin.etag)
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

```python
from azure.iot.hub import IoTHubRegistryManager
from azure.iot.hub.models import CloudToDeviceMethod

# ...

iothub_registry_manager = IoTHubRegistryManager(iothub_connection_str)

method_payload = datetime.datetime.now() - datetime.timedelta(minutes=2)
device_method = CloudToDeviceMethod(method_name="getMaxMinReport", payload=method_payload)
result = iothub_registry_manager.invoke_device_method(device_id, device_method)
print(result.payload)
```

O trecho a seguir mostra como chamar o `getMaxMinReport` comando em um componente. O comando é definido no componente **thermostat1** :

```python
from azure.iot.hub import IoTHubRegistryManager
from azure.iot.hub.models import CloudToDeviceMethod

# ...

iothub_registry_manager = IoTHubRegistryManager(iothub_connection_str)

method_payload = datetime.datetime.now() - datetime.timedelta(minutes=2)
device_method = CloudToDeviceMethod(method_name="thermostat1*getMaxMinReport", payload=method_payload)
result = iothub_registry_manager.invoke_device_method(device_id, device_method)
print(result.payload)
```

## <a name="iot-hub-digital-twin-examples"></a>Exemplos de entrelaçar digitais do Hub IoT

Você usa a classe **DigitalTwinClient** para interagir com o estado do dispositivo usando o gêmeos digital. O modelo [DTDL](../articles/iot-pnp/concepts-digital-twin.md) para o dispositivo define as propriedades e os comandos que o dispositivo implementa.

A `device_id` variável contém a ID do dispositivo de plug and Play IOT registrada com o Hub IOT.

### <a name="get-the-digital-twin-and-model-id"></a>Obter a ID de modelo e a. digital

Para obter a ID de modelo e conexão digital do dispositivo IoT Plug and Play que se conectou ao seu hub IoT:

```python
from azure.iot.hub import DigitalTwinClient

digital_twin_client = DigitalTwinClient(iothub_connection_str)

digital_twin = digital_twin_client.get_digital_twin(device_id)
if digital_twin:
    print(digital_twin)
    print("Model Id: " + digital_twin["$metadata"]["$model"])
else:
    print("No digital_twin found")
```

### <a name="update-digital-twin"></a>Atualizar atualização digital

O trecho de código a seguir mostra como atualizar a `targetTemperature` propriedade em um dispositivo. A propriedade é definida no componente padrão do dispositivo:

```python
from azure.iot.hub import DigitalTwinClient

digital_twin_client = DigitalTwinClient(iothub_connection_str)

patch = [{"op": "add", "path": "/targetTemperature", "value": 42}]
digital_twin_client.update_digital_twin(device_id, patch)
```

O trecho a seguir mostra como atualizar a `targetTemperature` propriedade em um componente. A propriedade é definida no componente **thermostat1** :

```python
from azure.iot.hub import DigitalTwinClient

digital_twin_client = DigitalTwinClient(iothub_connection_str)

patch = [{"op": "add", "path": "/targetTemperature", "value": 42}]
digital_twin_client.update_digital_twin(device_id, patch)
```

### <a name="call-command"></a>Chamar comando

O trecho a seguir mostra como invocar o `getMaxMinReport` comando definido em um componente padrão:

```python
from azure.iot.hub import DigitalTwinClient

payload = datetime.datetime.now() - datetime.timedelta(minutes=2)

connect_timeout_in_seconds = 3
response_timeout_in_seconds = 7


digital_twin_client = DigitalTwinClient(iothub_connection_str)

invoke_command_result = digital_twin_client.invoke_command(
    device_id, "getMaxMinReport", payload, connect_timeout_in_seconds, response_timeout_in_seconds
)
if invoke_command_result:
    print(invoke_command_result)
else:
    print("No invoke_command_result found")
```

O trecho a seguir mostra como chamar o `getMaxMinReport` comando em um componente. O comando é definido no componente **thermostat1** :

```python
from azure.iot.hub import DigitalTwinClient

payload = datetime.datetime.now() - datetime.timedelta(minutes=2)

connect_timeout_in_seconds = 3
response_timeout_in_seconds = 7


digital_twin_client = DigitalTwinClient(iothub_connection_str)

invoke_command_result = digital_twin_client.invoke_component_command(
    device_id, "thermostat1", "getMaxMinReport", payload, connect_timeout_in_seconds, response_timeout_in_seconds
)
if invoke_command_result:
    print(invoke_command_result)
else:
    print("No invoke_command_result found")
```

## <a name="read-device-telemetry"></a>Ler telemetria do dispositivo

Os dispositivos IoT Plug and Play enviam a telemetria definida no modelo DTDL para o Hub IoT. Por padrão, o Hub IoT roteia a telemetria para um ponto de extremidade de hubs de eventos, onde você pode consumi-la. Para saber mais, confira [usar o roteamento de mensagens do Hub IOT para enviar mensagens do dispositivo para a nuvem a diferentes pontos de extremidade](../articles/iot-hub/iot-hub-devguide-messages-d2c.md).

O trecho de código a seguir mostra como ler a telemetria do ponto de extremidade de hubs de eventos padrão. O código neste trecho é obtido do início rápido do Hub IoT [Enviar telemetria de um dispositivo para um hub IOT e lê-lo com um aplicativo de back-end](../articles/iot-hub/quickstart-send-telemetry-python.md):

```python
import asyncio
from azure.eventhub import TransportType
from azure.eventhub.aio import EventHubConsumerClient

# Define callbacks to process events
async def on_event_batch(partition_context, events):
    for event in events:
        print("Received event from partition: {}.".format(partition_context.partition_id))
        print("Telemetry received: ", event.body_as_str())
        print("Properties (set by device): ", event.properties)
        print("System properties (set by IoT Hub): ", event.system_properties)
        print()
    await partition_context.update_checkpoint()

async def on_error(partition_context, error):
    # ...

loop = asyncio.get_event_loop()
client = EventHubConsumerClient.from_connection_string(
    conn_str=CONNECTION_STR,
    consumer_group="$default",
)

try:
    loop.run_until_complete(client.receive_batch(on_event_batch=on_event_batch, on_error=on_error))
except KeyboardInterrupt:
    print("Receiving has stopped.")
finally:
    loop.run_until_complete(client.close())
    loop.stop()
```

A saída a seguir do código anterior mostra a telemetria de temperatura enviada pelo dispositivo **termostato** plug and Play IOT de não componente que tem apenas o componente padrão. A `dt-dataschema` Propriedade System mostra a ID do modelo:

```cmd/sh
Received event from partition: 1.
Telemetry received:  {"temperature": 12}
Properties (set by device):  None
System properties (set by IoT Hub):  {b'content-type': b'application/json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-connection-auth-method': b'{"scope":"device","type":"sas","issuer":"iothub","acceptingIpFilterRule":null}', b'iothub-connection-auth-generation-id': b'637388855582764406', b'iothub-enqueuedtime': 1603288810715, b'iothub-message-source': b'Telemetry', b'dt-dataschema': b'dtmi:com:example:Thermostat;1', b'x-opt-sequence-number': 13280, b'x-opt-offset': b'12890070640', b'x-opt-enqueued-time': 1603288810824}
```

A saída a seguir do código anterior mostra a telemetria de temperatura enviada pelo dispositivo de Plug and Play IOT **TemperatureController** de vários componentes. A `dt-subject` Propriedade System mostra o nome do componente que enviou a telemetria. Neste exemplo, os dois componentes são `thermostat1` e `thermostat2` , conforme definido no modelo DTDL. A `dt-dataschema` Propriedade System mostra a ID do modelo:

```cmd/sh
Received event from partition: 1.
Telemetry received:  {"temperature": 45}
Properties (set by device):  None
System properties (set by IoT Hub):  {b'content-type': b'application/json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-connection-auth-method': b'{"scope":"device","type":"sas","issuer":"iothub","acceptingIpFilterRule":null}', b'iothub-connection-auth-generation-id': b'637388858939631652', b'iothub-enqueuedtime': 1603289127844, b'iothub-message-source': b'Telemetry', b'dt-subject': b'thermostat1', b'dt-dataschema': b'dtmi:com:example:TemperatureController;1', b'x-opt-sequence-number': 13328, b'x-opt-offset': b'12890095440', b'x-opt-enqueued-time': 1603289128001}

Received event from partition: 1.
Telemetry received:  {"temperature": 49}
Properties (set by device):  None
System properties (set by IoT Hub):  {b'content-type': b'application/json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-connection-auth-method': b'{"scope":"device","type":"sas","issuer":"iothub","acceptingIpFilterRule":null}', b'iothub-connection-auth-generation-id': b'637388858939631652', b'iothub-enqueuedtime': 1603289133017, b'iothub-message-source': b'Telemetry', b'dt-subject': b'thermostat2', b'dt-dataschema': b'dtmi:com:example:TemperatureController;1', b'x-opt-sequence-number': 13329, b'x-opt-offset': b'12890095928', b'x-opt-enqueued-time': 1603289133173}
```

## <a name="read-device-twin-change-notifications"></a>Ler notificações de alteração de troca de dispositivo

Você pode configurar o Hub IoT para gerar notificações de alteração de troca de dispositivo para rotear para um ponto de extremidade com suporte. Para saber mais, confira [usar o roteamento de mensagens do Hub IOT para enviar mensagens do dispositivo para a nuvem a diferentes pontos de extremidade > eventos de não telemetria](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events).

O código mostrado no trecho de código Python anterior gera a saída a seguir quando o Hub IoT gera notificações de alteração de dispositivo para um dispositivo termostato sem componente. As propriedades do aplicativo `iothub-message-schema` e `opType` fornecem informações sobre o tipo de notificação de alteração:

```cmd/sh
Received event from partition: 1.
Telemetry received:  {"version":3,"properties":{"reported":{"maxTempSinceLastReboot":10.96,"$metadata":{"$lastUpdated":"2020-10-21T14:10:42.4171263Z","maxTempSinceLastReboot":{"$lastUpdated":"2020-10-21T14:10:42.4171263Z"}},"$version":2}}}
Properties (set by device):  {b'hubName': b'my-pnp-hub', b'deviceId': b'my-pnp-device', b'operationTimestamp': b'2020-10-21T14:10:42.4171263+00:00', b'iothub-message-schema': b'twinChangeNotification', b'opType': b'updateTwin'}
System properties (set by IoT Hub):  {b'user-id': b'my-pnp-hub\x81\x0e\xa4\x7f', b'correlation-id': b'12104ced5402', b'content-type': b'application/json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-enqueuedtime': 1603289442519, b'iothub-message-source': b'twinChangeEvents', b'x-opt-sequence-number': 13332, b'x-opt-offset': b'12890097392', b'x-opt-enqueued-time': 1603289442738}
```

O código mostrado no trecho de código Python anterior gera a saída a seguir quando o Hub IoT gera notificações de alteração de dispositivo de troca para um dispositivo com componentes. Este exemplo mostra a saída quando um dispositivo sensor de temperatura com um componente termostato gera notificações. As propriedades do aplicativo `iothub-message-schema` e `opType` fornecem informações sobre o tipo de notificação de alteração:

```cmd/sh
Received event from partition: 1.
Telemetry received:  {"version":4,"properties":{"reported":{"thermostat1":{"maxTempSinceLastReboot":98.34,"__t":"c"},"$metadata":{"$lastUpdated":"2020-10-21T14:13:39.36491Z","thermostat1":{"$lastUpdated":"2020-10-21T14:13:39.36491Z","maxTempSinceLastReboot":{"$lastUpdated":"2020-10-21T14:13:39.36491Z"},"__t":{"$lastUpdated":"2020-10-21T14:13:39.36491Z"}}},"$version":3}}}
Properties (set by device):  {b'hubName': b'my-pnp-hub', b'deviceId': b'my-pnp-device', b'operationTimestamp': b'2020-10-21T14:13:39.3649100+00:00', b'iothub-message-schema': b'twinChangeNotification', b'opType': b'updateTwin'}
System properties (set by IoT Hub):  {b'user-id': b'my-pnp-hub', b'correlation-id': b'1210b664ab83', b'content-type': b'application/json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-enqueuedtime': 1603289619481, b'iothub-message-source': b'twinChangeEvents', b'x-opt-sequence-number': 13341, b'x-opt-offset': b'12890102216', b'x-opt-enqueued-time': 1603289619668}
```

## <a name="read-digital-twin-change-notifications"></a>Ler notificações de alteração de troca digital

Você pode configurar o Hub IoT para gerar notificações de alteração de troca digital para rotear para um ponto de extremidade com suporte. Para saber mais, confira [usar o roteamento de mensagens do Hub IOT para enviar mensagens do dispositivo para a nuvem a diferentes pontos de extremidade > eventos de não telemetria](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events).

O código mostrado no trecho de código Python anterior gera a seguinte saída quando o Hub IoT gera notificações de alteração de troca digital para um dispositivo termostato sem componente. As propriedades do aplicativo `iothub-message-schema` e `opType` fornecem informações sobre o tipo de notificação de alteração:

```cmd/sh
Received event from partition: 1.
Telemetry received:  [{"op":"add","path":"/$metadata/maxTempSinceLastReboot","value":{"lastUpdateTime":"2020-10-21T14:10:42.4171263Z"}},{"op":"add","path":"/maxTempSinceLastReboot","value":10.96}]
Properties (set by device):  {b'hubName': b'my-pnp-hub', b'deviceId': b'my-pnp-device', b'operationTimestamp': b'2020-10-21T14:10:42.4171263+00:00', b'iothub-message-schema': b'digitalTwinChangeNotification', b'opType': b'updateTwin'}
System properties (set by IoT Hub):  {b'user-id': b'my-pnp-hub\x81\x0e\xa4\x7f', b'correlation-id': b'12104ced5402', b'content-type': b'application/json-patch+json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-enqueuedtime': 1603289442519, b'iothub-message-source': b'digitalTwinChangeEvents', b'x-opt-sequence-number': 13333, b'x-opt-offset': b'12890098024', b'x-opt-enqueued-time': 1603289442738}
```

O código mostrado no trecho de código Python anterior gera a seguinte saída quando o Hub IoT gera notificações de alteração de troca digital para um dispositivo com componentes. Este exemplo mostra a saída quando um dispositivo sensor de temperatura com um componente termostato gera notificações. As propriedades do aplicativo `iothub-message-schema` e `opType` fornecem informações sobre o tipo de notificação de alteração:

```cmd/sh
Received event from partition: 1.
Telemetry received:  [{"op":"add","path":"/thermostat1","value":{"$metadata":{"maxTempSinceLastReboot":{"lastUpdateTime":"2020-10-21T14:13:39.36491Z"}},"maxTempSinceLastReboot":98.34}}]
Properties (set by device):  {b'hubName': b'my-pnp-hub', b'deviceId': b'my-pnp-device', b'operationTimestamp': b'2020-10-21T14:13:39.3649100+00:00', b'iothub-message-schema': b'digitalTwinChangeNotification', b'opType': b'updateTwin'}
System properties (set by IoT Hub):  {b'user-id': b'my-pnp-hub', b'correlation-id': b'1210b664ab83', b'content-type': b'application/json-patch+json', b'content-encoding': b'utf-8', b'iothub-connection-device-id': b'my-pnp-device', b'iothub-enqueuedtime': 1603289619481, b'iothub-message-source': b'digitalTwinChangeEvents', b'x-opt-sequence-number': 13342, b'x-opt-offset': b'12890102984', b'x-opt-enqueued-time': 1603289619668}
```
