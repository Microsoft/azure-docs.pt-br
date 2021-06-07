---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/19/2020
ms.openlocfilehash: f4eb312aff200389f59a3e342305b8eda98f213e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104582744"
---
## <a name="model-id-announcement"></a>Comunicado de ID do modelo

Para anunciar a ID do modelo, o dispositivo deve incluí-lo nas informações de conexão:

```python
device_client = IoTHubDeviceClient.create_from_symmetric_key(
    symmetric_key=symmetric_key,
    hostname=registration_result.registration_state.assigned_hub,
    device_id=registration_result.registration_state.device_id,
    product_info=model_id,
)
```

> [!TIP]
> Para módulos e IoT Edge, use `IoTHubModuleClient` no lugar de `IoTHubDeviceClient` .

> [!TIP]
> Essa é a única vez que um dispositivo pode definir a ID do modelo, ele não pode ser atualizado depois que o dispositivo se conecta.

## <a name="dps-payload"></a>Payload de DPS

Os dispositivos que usam o [DPS (serviço de provisionamento de dispositivos)](../articles/iot-dps/about-iot-dps.md) podem incluir o `modelId` a ser usado durante o processo de provisionamento usando a carga JSON a seguir.

```json
{
    "modelId" : "dtmi:com:example:Thermostat;1"
}
```

## <a name="implement-telemetry-properties-and-commands"></a>Implementar telemetria, propriedades e comandos

Conforme descrito em [entender os componentes nos modelos de plug and Play IOT](../articles/iot-pnp/concepts-modeling-guide.md), os criadores de dispositivos devem decidir se desejam usar componentes para descrever seus dispositivos. Ao usar componentes, os dispositivos devem seguir as regras descritas nesta seção.

### <a name="telemetry"></a>Telemetria

Um componente padrão não requer nenhuma propriedade especial.

Ao usar componentes aninhados, os dispositivos devem definir uma propriedade de mensagem com o nome do componente:

```python
async def send_telemetry_from_temp_controller(device_client, telemetry_msg, component_name=None):
    msg = Message(json.dumps(telemetry_msg))
    msg.content_encoding = "utf-8"
    msg.content_type = "application/json"
    if component_name:
        msg.custom_properties["$.sub"] = component_name
    await device_client.send_message(msg)
}
```

### <a name="read-only-properties"></a>Propriedades somente leitura

Relatar uma propriedade do componente padrão não requer qualquer construção especial:

```python
await device_client.patch_twin_reported_properties({"maxTempSinceLastReboot": 38.7})
```

O dispositivo de atualização é atualizado com a próxima Propriedade relatada:

```json
{
  "reported": {
      "maxTempSinceLastReboot" : 38.7
  }
}
```

Ao usar componentes aninhados, as propriedades devem ser criadas dentro do nome do componente:

```python
inner_dict = {}
inner_dict["targetTemperature"] = 38.7
inner_dict["__t"] = "c"
prop_dict = {}
prop_dict["thermostat1"] = inner_dict

await device_client.patch_twin_reported_properties(prop_dict)
```

O dispositivo de atualização é atualizado com a próxima Propriedade relatada:

```json
{
  "reported": {
    "thermostat1" : {  
      "__t" : "c",  
      "maxTempSinceLastReboot" : 38.7
     }
  }
}
```

### <a name="writable-properties"></a>Propriedades graváveis

Essas propriedades podem ser definidas pelo dispositivo ou atualizadas pela solução. Se a solução atualizar uma propriedade, o cliente receberá uma notificação como um retorno de chamada no `IoTHubDeviceClient` ou no `IoTHubModuleClient` . Para seguir as convenções de Plug and Play IoT, o dispositivo deve informar ao serviço que a propriedade foi recebida com êxito.

#### <a name="report-a-writable-property"></a>Relatar uma propriedade gravável

Quando um dispositivo relata uma propriedade gravável, ele deve incluir os `ack` valores definidos nas convenções.

Para relatar uma propriedade gravável do componente padrão:

```python
prop_dict = {}
prop_dict["targetTemperature"] = {
    "ac": 200,
    "ad": "reported default value",
    "av": 0,
    "value": 23.2
}

await device_client.patch_twin_reported_properties(prop_dict)
```

O dispositivo de atualização é atualizado com a próxima Propriedade relatada:

```json
{
  "reported": {
    "targetTemperature": {
      "value": 23.2,
      "ac": 200,
      "av": 0,
      "ad": "reported default value"
    }
  }
}
```

Para relatar uma propriedade gravável de um componente aninhado, o "My" deve incluir um marcador:

```python
inner_dict = {}
inner_dict["targetTemperature"] = {
    "ac": 200,
    "ad": "reported default value",
    "av": 0,
    "value": 23.2
}
inner_dict["__t"] = "c"
prop_dict = {}
prop_dict["thermostat1"] = inner_dict

await device_client.patch_twin_reported_properties(prop_dict)
```

O dispositivo de atualização é atualizado com a próxima Propriedade relatada:

```json
{
  "reported": {
    "thermostat1": {
      "__t" : "c",
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 0,
          "ad": "complete"
      }
    }
  }
}
```

#### <a name="subscribe-to-desired-property-updates"></a>Assinar as atualizações da propriedade desejada

Os serviços podem atualizar as propriedades desejadas que disparam uma notificação nos dispositivos conectados. Essa notificação inclui as propriedades desejadas atualizadas, incluindo o número de versão que identifica a atualização. Os dispositivos devem responder com a mesma `ack` mensagem que as propriedades relatadas.

Um componente padrão vê a propriedade única e cria o relatório `ack` com a versão recebida:

```python
async def execute_property_listener(device_client):
    ignore_keys = ["__t", "$version"]
    while True:
        patch = await device_client.receive_twin_desired_properties_patch()  # blocking call

        version = patch["$version"]
        prop_dict = {}

        for prop_name, prop_value in patch.items():
            if prop_name in ignore_keys:
                continue
            else:
                prop_dict[prop_name] = {
                    "ac": 200,
                    "ad": "Successfully executed patch",
                    "av": version,
                    "value": prop_value,
                }

        await device_client.patch_twin_reported_properties(prop_dict)
```

O dispositivo "up" mostra a propriedade nas seções desejadas e relatadas:

```json
{
  "desired" : {
    "targetTemperature": 23.2,
    "$version" : 3
  },
  "reported": {
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
  }
}
```

Um componente aninhado recebe as propriedades desejadas encapsuladas com o nome do componente e deve relatar novamente a `ack` Propriedade relatada:

```python
def create_reported_properties_from_desired(patch):
    ignore_keys = ["__t", "$version"]
    component_prefix = list(patch.keys())[0]
    values = patch[component_prefix]

    version = patch["$version"]
    inner_dict = {}

    for prop_name, prop_value in values.items():
        if prop_name in ignore_keys:
            continue
        else:
            inner_dict["ac"] = 200
            inner_dict["ad"] = "Successfully executed patch"
            inner_dict["av"] = version
            inner_dict["value"] = prop_value
            values[prop_name] = inner_dict

    properties_dict = dict()
    if component_prefix:
        properties_dict[component_prefix] = values
    else:
        properties_dict = values

    return properties_dict

async def execute_property_listener(device_client):
    while True:
        patch = await device_client.receive_twin_desired_properties_patch()  # blocking call
        properties_dict = create_reported_properties_from_desired(patch)

        await device_client.patch_twin_reported_properties(properties_dict)
```

O dispositivo "up para componentes" mostra as seções desejadas e relatadas da seguinte maneira:

```json
{
  "desired" : {
    "thermostat1" : {
        "__t" : "c",
        "targetTemperature": 23.2,
    }
    "$version" : 3
  },
  "reported": {
    "thermostat1" : {
        "__t" : "c",
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
    }
  }
}
```

### <a name="commands"></a>Comandos

Um componente padrão recebe o nome do comando conforme ele foi invocado pelo serviço.

Um componente aninhado recebe o nome do comando prefixado com o nome do componente e o `*` separador.

```python
command_request = await device_client.receive_method_request("thermostat1*reboot")
```

#### <a name="request-and-response-payloads"></a>Cargas de solicitação e resposta

Os comandos usam tipos para definir suas cargas de solicitação e resposta. Um dispositivo deve desserializar o parâmetro de entrada de entrada e serializar a resposta. O exemplo a seguir mostra como implementar um comando com tipos complexos definidos nas cargas:

```json
{
  "@type": "Command",
  "name": "getMaxMinReport",
  "displayName": "Get Max-Min report.",
  "description": "This command returns the max, min and average temperature from the specified time to the current time.",
  "request": {
    "name": "since",
    "displayName": "Since",
    "description": "Period to return the max-min report.",
    "schema": "dateTime"
  },
  "response": {
    "name" : "tempReport",
    "displayName": "Temperature Report",
    "schema": {
      "@type": "Object",
      "fields": [
        {
          "name": "maxTemp",
          "displayName": "Max temperature",
          "schema": "double"
        },
        {
          "name": "minTemp",
          "displayName": "Min temperature",
          "schema": "double"
        },
        {
          "name" : "avgTemp",
          "displayName": "Average Temperature",
          "schema": "double"
        },
        {
          "name" : "startTime",
          "displayName": "Start Time",
          "schema": "dateTime"
        },
        {
          "name" : "endTime",
          "displayName": "End Time",
          "schema": "dateTime"
        }
      ]
    }
  }
}
```

Os trechos de código a seguir mostram como um dispositivo implementa essa definição de comando, incluindo os tipos usados para habilitar a serialização e desserialização:

```python
def create_max_min_report_response(values):
    response_dict = {
        "maxTemp": max_temp,
        "minTemp": min_temp,
        "avgTemp": sum(avg_temp_list) / moving_window_size,
        "startTime": (datetime.now() - timedelta(0, moving_window_size * 8)).isoformat(),
        "endTime": datetime.now().isoformat(),
    }
    # serialize response dictionary into a JSON formatted str
    response_payload = json.dumps(response_dict, default=lambda o: o.__dict__, sort_keys=True)
    return response_payload
```

> [!Tip]
> Os nomes de solicitação e resposta não estão presentes nas cargas serializadas transmitidas pela conexão.
