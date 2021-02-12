---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/19/2020
ms.openlocfilehash: f731e07de4936bc8de396c4f9bcd3a52913cb502
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99616509"
---
## <a name="model-id-announcement"></a>Comunicado de ID do modelo

Para anunciar a ID do modelo, o dispositivo deve incluí-lo nas informações de conexão:

```csharp
DeviceClient.CreateFromConnectionString(
  connectionString,
  TransportType.Mqtt,
  new ClientOptions() { ModelId = modelId })
```

A nova `ClientOptions` sobrecarga está disponível em todos os `DeviceClient` métodos usados para inicializar uma conexão.

> [!TIP]
> Para módulos e IoT Edge, use `ModuleClient` no lugar de `DeviceClient` .

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

Conforme descrito em [entender os componentes nos modelos de plug and Play IOT](../articles/iot-pnp/concepts-components.md), os criadores de dispositivos devem decidir se desejam usar componentes para descrever seus dispositivos. Ao usar componentes, os dispositivos devem seguir as regras descritas nesta seção.

### <a name="telemetry"></a>Telemetria

Um componente padrão não requer nenhuma propriedade especial.

Ao usar componentes aninhados, os dispositivos devem definir uma propriedade de mensagem com o nome do componente:

```c#
public async Task SendComponentTelemetryValueAsync(string componentName, string serializedTelemetry)
{
  var message = new Message(Encoding.UTF8.GetBytes(serializedTelemetry));
  message.ComponentName = componentName;
  message.ContentType = "application/json";
  message.ContentEncoding = "utf-8";
  await client.SendEventAsync(message);
}
```

### <a name="read-only-properties"></a>Propriedades somente leitura

Relatar uma propriedade do componente padrão não requer qualquer construção especial:

```csharp
TwinCollection reportedProperties = new TwinCollection();
reportedProperties["maxTemperature"] = 38.7;
await client.UpdateReportedPropertiesAsync(reportedProperties);
```

O dispositivo de atualização é atualizado com a próxima Propriedade relatada:

```json
{
  "reported": {
      "maxTemperature" : 38.7
  }
}
```

Ao usar componentes aninhados, as propriedades devem ser criadas dentro do nome do componente:

```csharp
TwinCollection reportedProperties = new TwinCollection();
TwinCollection component = new TwinCollection();
component["maxTemperature"] = 38.7;
component["__t"] = "c"; // marker to identify a component
reportedProperties["thermostat1"] = component;
await client.UpdateReportedPropertiesAsync(reportedProperties);
```

O dispositivo de atualização é atualizado com a próxima Propriedade relatada:

```json
{
  "reported": {
    "thermostat1" : {  
      "__t" : "c",  
      "maxTemperature" : 38.7
     } 
  }
}
```

### <a name="writable-properties"></a>Propriedades graváveis

Essas propriedades podem ser definidas pelo dispositivo ou atualizadas pela solução. Se a solução atualizar uma propriedade, o cliente receberá uma notificação como um retorno de chamada no `DeviceClient` ou no `ModuleClient` . Para seguir as convenções de Plug and Play IoT, o dispositivo deve informar ao serviço que a propriedade foi recebida com êxito.

#### <a name="report-a-writable-property"></a>Relatar uma propriedade gravável

Quando um dispositivo relata uma propriedade gravável, ele deve incluir os `ack` valores definidos nas convenções.

Para relatar uma propriedade gravável do componente padrão:

```csharp
TwinCollection reportedProperties = new TwinCollection();
TwinCollection ackProps = new TwinCollection();
ackProps["value"] = 23.2;
ackProps["ac"] = 200; // using HTTP status codes
ackProps["av"] = 0; // not readed from a desired property
ackProps["ad"] = "reported default value";
reportedProperties["targetTemperature"] = ackProps;
await client.UpdateReportedPropertiesAsync(reportedProperties);
```

O dispositivo de atualização é atualizado com a próxima Propriedade relatada:

```json
{
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

Para relatar uma propriedade gravável de um componente aninhado, o "My" deve incluir um marcador:

```csharp
TwinCollection reportedProperties = new TwinCollection();
TwinCollection component = new TwinCollection();
TwinCollection ackProps = new TwinCollection();
component["__t"] = "c"; // marker to identify a component
ackProps["value"] = 23.2;
ackProps["ac"] = 200; // using HTTP status codes
ackProps["av"] = 0; // not read from a desired property
ackProps["ad"] = "reported default value";
component["targetTemperature"] = ackProps;
reportedProperties["thermostat1"] = component;
await client.UpdateReportedPropertiesAsync(reportedProperties);
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
          "av": 3,
          "ad": "complete"
      }
    }
  }
}
```

#### <a name="subscribe-to-desired-property-updates"></a>Assinar as atualizações da propriedade desejada

Os serviços podem atualizar as propriedades desejadas que disparam uma notificação nos dispositivos conectados. Essa notificação inclui as propriedades desejadas atualizadas, incluindo o número de versão que identifica a atualização. Os dispositivos devem responder com a mesma `ack` mensagem que as propriedades relatadas.

Um componente padrão vê a propriedade única e cria o relatório `ack` com a versão recebida:

```csharp
await client.SetDesiredPropertyUpdateCallbackAsync(async (desired, ctx) => 
{
  JValue targetTempJson = desired["targetTemperature"];
  double targetTemperature = targetTempJson.Value<double>();

  TwinCollection reportedProperties = new TwinCollection();
  TwinCollection ackProps = new TwinCollection();
  ackProps["value"] = targetTemperature;
  ackProps["ac"] = 200;
  ackProps["av"] = desired.Version; 
  ackProps["ad"] = "desired property received";
  reportedProperties["targetTemperature"] = ackProps;

  await client.UpdateReportedPropertiesAsync(reportedProperties);
}, null);
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

```csharp
await client.SetDesiredPropertyUpdateCallbackAsync(async (desired, ctx) =>
{
  JObject thermostatComponent = desired["thermostat1"];
  JToken targetTempProp = thermostatComponent["targetTemperature"];
  double targetTemperature = targetTempProp.Value<double>();

  TwinCollection reportedProperties = new TwinCollection();
  TwinCollection component = new TwinCollection();
  TwinCollection ackProps = new TwinCollection();
  component["__t"] = "c"; // marker to identify a component
  ackProps["value"] = targetTemperature;
  ackProps["ac"] = 200; // using HTTP status codes
  ackProps["av"] = desired.Version; // not readed from a desired property
  ackProps["ad"] = "desired property received";
  component["targetTemperature"] = ackProps;
  reportedProperties["thermostat1"] = component;

  await client.UpdateReportedPropertiesAsync(reportedProperties);
}, null);
```

O dispositivo para um componente aninhado mostra as seções desejadas e relatadas da seguinte maneira:

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

```csharp
await client.SetMethodHandlerAsync("themostat*reboot", (MethodRequest req, object ctx) =>
{
  Console.WriteLine("REBOOT");
  return Task.FromResult(new MethodResponse(200));
},
null);
```

#### <a name="request-and-response-payloads"></a>Cargas de solicitação e resposta

Os comandos usam tipos para definir suas cargas de solicitação e resposta. Um dispositivo deve desserializar o parâmetro de entrada de entrada e serializar a resposta. O exemplo a seguir mostra como implementar um comando com tipos complexos definidos nas cargas:

```json
{
  "@type": "Command",
  "name": "start",
  "request": {
    "name": "startRequest",
    "schema": {
      "@type": "Object",
      "fields": [
        {
          "name": "startPriority",
          "schema": "integer"
        },
        {
          "name": "startMessage",
          "schema" : "string"
        }
      ]
    }
  },
  "response": {
    "name": "startReponse",
    "schema": {
      "@type": "Object",
      "fields": [
        {
            "name": "startupTime",
            "schema": "integer" 
        },
        {
          "name": "startupMessage",
          "schema": "string"
        }
      ]
    }
  }
}
```

Os trechos de código a seguir mostram como um dispositivo implementa essa definição de comando, incluindo os tipos usados para habilitar a serialização e desserialização:

```csharp
class startRequest
{
  public int startPriority { get; set; }
  public string startMessage { get; set; }
}

class startResponse
{
  public int startupTime { get; set; }
  public string startupMessage { get; set; }
}

// ... 

await client.SetMethodHandlerAsync("start", (MethodRequest req, object ctx) =>
{
  var startRequest = JsonConvert.DeserializeObject<startRequest>(req.DataAsJson);
  Console.WriteLine($"Received start command with priority ${startRequest.startPriority} and ${startRequest.startMessage}");

  var startResponse = new startResponse
  {
    startupTime = 123,
    startupMessage = "device started with message " + startRequest.startMessage
  };

  string responsePayload = JsonConvert.SerializeObject(startResponse);
  MethodResponse response = new MethodResponse(Encoding.UTF8.GetBytes(responsePayload), 200);
  return Task.FromResult(response);
},null);
```

> [!Tip]
> Os nomes de solicitação e resposta não estão presentes nas cargas serializadas transmitidas pela conexão.
