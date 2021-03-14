---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 10/20/2020
ms.openlocfilehash: 33752c1ebb83c5d63e8e1cb396c52f01f07046cd
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102244612"
---
Os recursos a seguir também estão disponíveis:

- [Documentação de referência do SDK do Java](/java/api/com.microsoft.azure.sdk.iot.service.devicetwin.devicetwindevice)
- [Exemplos de cliente de serviço](https://github.com/Azure/azure-iot-sdk-java/blob/master/service/iot-service-samples/pnp-service-sample)
- [Exemplos de gêmeos digital](https://github.com/Azure/azure-iot-sdk-java/tree/master/service/iot-service-samples/digitaltwin-service-samples)

## <a name="iot-hub-service-client-examples"></a>Exemplos de cliente de serviço do Hub IoT

Esta seção mostra exemplos de Java usando o cliente de serviço do Hub IoT e as classes **DeviceTwin** e **DeviceMethod** do namespace **com. Microsoft. Azure. Sdk. IOT. Service. DeviceTwin** . Você usa a classe **DeviceTwin** para interagir com o estado do dispositivo usando o dispositivo gêmeos. Você também pode usar a classe **DeviceTwin** para [consultar os registros do dispositivo](../articles/iot-hub/iot-hub-devguide-query-language.md) no Hub IOT. Você usa a classe **DeviceMethod** para chamar comandos no dispositivo. O modelo [DTDL](../articles/iot-pnp/concepts-digital-twin.md) para o dispositivo define as propriedades e os comandos que o dispositivo implementa. Nos trechos de código, a `deviceId` variável contém a ID do dispositivo de plug and Play IOT registrada com o Hub IOT.

### <a name="get-the-device-twin-and-model-id"></a>Obter o dispositivo e a ID do modelo

Para obter o dispositivo de conexão e ID do modelo do dispositivo IoT Plug and Play que se conectou ao seu hub IoT:

```java
DeviceTwin twinClient = DeviceTwin.createFromConnectionString(iotHubConnectionString);

// ...

DeviceTwinDevice twin = new DeviceTwinDevice(deviceId);
twinClient.getTwin(twin);
System.out.println("Model Id of this Twin is: " + twin.getModelId());
```

### <a name="update-device-twin"></a>Atualizar dispositivo gêmeo

O trecho de código a seguir mostra como atualizar a `targetTemperature` propriedade em um dispositivo. Você deve obter o ' Atualizar ' antes de atualizá-lo. A propriedade é definida no componente padrão do dispositivo:

```java
DeviceTwin twinClient = DeviceTwin.createFromConnectionString(iotHubConnectionString);
DeviceTwinDevice twin = new DeviceTwinDevice(deviceId);
twinClient.getTwin(twin);

double propertyValue = 60.2;
twin.setDesiredProperties(Collections.singleton(new Pair("targetTemperature", propertyValue)));
twinClient.updateTwin(twin);
```

O trecho a seguir mostra como atualizar a `targetTemperature` propriedade em um componente. Você deve obter o ' Atualizar ' antes de atualizá-lo. A propriedade é definida no componente **thermostat1** :

```java
public static Set<Pair> CreateComponentPropertyPatch(@NonNull String propertyName, @NonNull double propertyValue, @NonNull String componentName)
{
    JsonObject patchJson = new JsonObject();
    patchJson.addProperty("__t", "c");
    patchJson.addProperty(propertyName, propertyValue);
    return singleton(new Pair(componentName, patchJson));
}

// ...

DeviceTwin twinClient = DeviceTwin.createFromConnectionString(iotHubConnectionString);
DeviceTwinDevice twin = new DeviceTwinDevice(deviceId);
twinClient.getTwin(twin);

double propertyValue = 60.2;
twin.setDesiredProperties(CreateComponentPropertyPatch("targetTemperature", propertyValue, "thermostat1"));
twinClient.updateTwin(twin);
```

Para uma propriedade em um componente, o patch da propriedade é semelhante ao exemplo a seguir:

```json
{
  "thermostat1":
  {
    "__t": "c",
    "targetTemperature": 60.2
  }
}
```

### <a name="call-command"></a>Chamar comando

O trecho a seguir mostra como invocar o `getMaxMinReport` comando definido em um componente padrão:

```java
DeviceMethod methodClient = DeviceMethod.createFromConnectionString(iotHubConnectionString);

Long responseTimeout = TimeUnit.SECONDS.toSeconds(200);
Long connectTimeout = TimeUnit.SECONDS.toSeconds(5);

String commandInput = ZonedDateTime.now(ZoneOffset.UTC).minusMinutes(5).format(DateTimeFormatter.ISO_DATE_TIME);
MethodResult result = methodClient.invoke(deviceId, "getMaxMinReport", responseTimeout, connectTimeout, commandInput);

System.out.println("Method result status is: " + result.getStatus());
```

O trecho a seguir mostra como chamar o `getMaxMinReport` comando em um componente. O comando é definido no componente **thermostat1** :

```java
DeviceMethod methodClient = DeviceMethod.createFromConnectionString(iotHubConnectionString);

Long responseTimeout = TimeUnit.SECONDS.toSeconds(200);
Long connectTimeout = TimeUnit.SECONDS.toSeconds(5);

String commandInput = ZonedDateTime.now(ZoneOffset.UTC).minusMinutes(5).format(DateTimeFormatter.ISO_DATE_TIME);
MethodResult result = methodClient.invoke(deviceId, "thermostat1*getMaxMinReport", responseTimeout, connectTimeout, commandInput);

System.out.println("Method result status is: " + result.getStatus());
```

## <a name="iot-hub-digital-twin-examples"></a>Exemplos de entrelaçar digitais do Hub IoT

Você usa a classe **DigitalTwinAsyncClient** no namespace **com. Microsoft. Azure. Sdk. IOT. Service. digitaltwin** para interagir com o estado do dispositivo usando o gêmeos digital. Os exemplos a seguir também usam as classes **UpdateOperationUtility** e **BasicDigitalTwin** do mesmo namespace. O modelo [DTDL](../articles/iot-pnp/concepts-digital-twin.md) para o dispositivo define as propriedades e os comandos que o dispositivo implementa.

A `digitalTwinid` variável contém a ID do dispositivo de plug and Play IOT registrada com o Hub IOT.

### <a name="get-the-digital-twin-and-model-id"></a>Obter a ID de modelo e a. digital

Para obter a ID de modelo e conexão digital do dispositivo IoT Plug and Play que se conectou ao seu hub IoT:

```java
DigitalTwinAsyncClient asyncClient = DigitalTwinAsyncClient.createFromConnectionString(iotHubConnectionString);

CountDownLatch latch = new CountDownLatch(1);
asyncClient.getDigitalTwin(digitalTwinid, BasicDigitalTwin.class)
    .subscribe(
        getResponse ->
        {
            System.out.println("Digital Twin Model Id: " + getResponse.getMetadata().getModelId());
            System.out.println("Digital Twin: " + prettyBasicDigitalTwin(getResponse));
            latch.countDown();
        },
        error ->
        {
            System.out.println("Get Digital Twin failed: " + error);
            latch.countDown();
        });

latch.await(10, TimeUnit.SECONDS);

// ...

private static String prettyBasicDigitalTwin(BasicDigitalTwin basicDigitalTwin)
{
    Gson gson = new GsonBuilder().setPrettyPrinting().create();
    return gson.toJson(basicDigitalTwin);
}
```

### <a name="update-digital-twin"></a>Atualizar atualização digital

O trecho de código a seguir mostra como atualizar a `targetTemperature` propriedade em um dispositivo. A propriedade é definida no componente padrão do dispositivo:

```java
DigitalTwinAsyncClient asyncClient = DigitalTwinAsyncClient.createFromConnectionString(iotHubConnectionString);

CountDownLatch latch1 = new CountDownLatch(1);

UpdateOperationUtility updateOperationUtility = new UpdateOperationUtility();

// Add a new property.
updateOperationUtility.appendAddPropertyOperation("/" + "targetTemperature", 35);
asyncClient.updateDigitalTwin(digitalTwinid, updateOperationUtility.getUpdateOperations())
    .subscribe(
        getResponse ->
        {
            System.out.println("Updated Digital Twin");
            latch1.countDown();
        },
        error ->
        {
            System.out.println("Update Digital Twin failed: " + error);
            latch1.countDown();
        });
latch1.await(10, TimeUnit.SECONDS);
GetDigitalTwin();

// Replace an existing property.
CountDownLatch latch2 = new CountDownLatch(1);
updateOperationUtility.appendReplacePropertyOperation("/targetTemperature", 50);
asyncClient.updateDigitalTwin(digitalTwinid, updateOperationUtility.getUpdateOperations())
    .subscribe(
        getResponse ->
        {
            System.out.println("Updated Digital Twin");
            latch2.countDown();
        },
        error ->
        {
            System.out.println("Update Digital Twin failed: " + error);
            latch2.countDown();
        });

latch2.await(10, TimeUnit.SECONDS);
GetDigitalTwin();
```

O trecho a seguir mostra como atualizar a `targetTemperature` propriedade em um componente. A propriedade é definida no componente **thermostat1** :

```java
DigitalTwinClient client = DigitalTwinClient.createFromConnectionString(iotHubConnectionString);

// Get digital twin.
ServiceResponseWithHeaders<String, DigitalTwinGetHeaders> getResponse = client.getDigitalTwinWithResponse(digitalTwinid, String.class);

// Construct the options for conditional update.
DigitalTwinUpdateRequestOptions options = new DigitalTwinUpdateRequestOptions();
options.setIfMatch(getResponse.headers().eTag());

UpdateOperationUtility updateOperationUtility = new UpdateOperationUtility();

Map<String, Object> t1properties = new HashMap<>();
t1properties.put("targetTemperature", 50);
updateOperationUtility.appendReplaceComponentOperation("/thermostat1", t1properties);

digitalTwinUpdateOperations = updateOperationUtility.getUpdateOperations();
updateResponse = client.updateDigitalTwinWithResponse(digitalTwinid, digitalTwinUpdateOperations, options);
System.out.println("Update Digital Twin response status: " + updateResponse.response().message());

getResponse = client.getDigitalTwinWithResponse(digitalTwinid, String.class);
```

### <a name="call-command"></a>Chamar comando

O trecho a seguir mostra como invocar o `getMaxMinReport` comando definido em um componente padrão:

```java
CountDownLatch latch = new CountDownLatch(1);

String commandInput = ZonedDateTime.now(ZoneOffset.UTC).minusMinutes(5).format(DateTimeFormatter.ISO_DATE_TIME);

// Invoke a method on root level.
asyncClient.invokeCommand(digitalTwinid, "getMaxMinReport", commandInput)
    .subscribe(
        response ->
        {
            System.out.println("Invoked Command getMaxMinReport response: " + prettyString(response.getPayload()));
            latch.countDown();
        },
        error ->
        {
            RestException ex = (RestException)error;
            if(ex.response().code() == 404) {
                System.out.println("Invoked Command getMaxMinReport failed: " + error);
            }
            else {
                System.out.println("Ensure the device sample is running for this sample to succeed");
            }
            latch.countDown();
        });

latch.await(10, TimeUnit.SECONDS);

// ...

private static String prettyString(String str)
{
    Gson gson = new Gson();
    Gson gsonBuilder = new GsonBuilder().setPrettyPrinting().create();
    return gsonBuilder.toJson(gson.fromJson(str, Object.class));
}
```

O trecho a seguir mostra como chamar o `getMaxMinReport` comando em um componente. O comando é definido no componente **thermostat1** :

```java
DigitalTwinClient client = DigitalTwinClient.createFromConnectionString(iotHubConnectionString);

String commandInput = ZonedDateTime.now(ZoneOffset.UTC).minusMinutes(5).format(DateTimeFormatter.ISO_DATE_TIME);

DigitalTwinInvokeCommandRequestOptions options = new DigitalTwinInvokeCommandRequestOptions();
try {
    ServiceResponseWithHeaders<DigitalTwinCommandResponse, DigitalTwinInvokeCommandHeaders> commandResponse = client.invokeComponentCommandWithResponse(digitalTwinid, "thermostat1", "getMaxMinReport", commandInput, options);
    System.out.println("Command getMaxMinReport, payload: " + prettyString(commandResponse.body().getPayload()));
    System.out.println("Command getMaxMinReport, status: " + commandResponse.body().getStatus());
} catch (RestException ex)
{
    if(ex.response().code() == 404)
    {
        System.out.println("Ensure the device sample is running for this sample to succeed.");
    }
    else
    {
        throw ex;
    }
}

// ...

private static String prettyString(String str)
{
    Gson gson = new Gson();
    Gson gsonBuilder = new GsonBuilder().setPrettyPrinting().create();
    return gsonBuilder.toJson(gson.fromJson(str, Object.class));
}
```

## <a name="read-device-telemetry"></a>Ler telemetria do dispositivo

Os dispositivos IoT Plug and Play enviam a telemetria definida no modelo DTDL para o Hub IoT. Por padrão, o Hub IoT roteia a telemetria para um ponto de extremidade de hubs de eventos, onde você pode consumi-la. Para saber mais, confira [usar o roteamento de mensagens do Hub IOT para enviar mensagens do dispositivo para a nuvem a diferentes pontos de extremidade](../articles/iot-hub/iot-hub-devguide-messages-d2c.md).

O trecho de código a seguir mostra como ler a telemetria do ponto de extremidade de hubs de eventos padrão. O código neste trecho é obtido do início rápido do Hub IoT [Enviar telemetria de um dispositivo para um hub IOT e lê-lo com um aplicativo de back-end](../articles/iot-hub/quickstart-send-telemetry-java.md):

```java
import com.azure.messaging.eventhubs.EventHubClientBuilder;
import com.azure.messaging.eventhubs.EventHubConsumerAsyncClient;

// ...

EventHubClientBuilder eventHubClientBuilder = new EventHubClientBuilder()
    .consumerGroup(EventHubClientBuilder.DEFAULT_CONSUMER_GROUP_NAME)
    .connectionString(eventHubCompatibleConnectionString);

try (EventHubConsumerAsyncClient eventHubConsumerAsyncClient = eventHubClientBuilder.buildAsyncConsumerClient()) {

    receiveFromAllPartitions(eventHubConsumerAsyncClient);

}

// ...

private static void receiveFromAllPartitions(EventHubConsumerAsyncClient eventHubConsumerAsyncClient) {

eventHubConsumerAsyncClient
    .receive(false) // set this to false to read only the newly available events
    .subscribe(partitionEvent -> {
        System.out.println();
        System.out.printf("%nTelemetry received from partition %s:%n%s",
            partitionEvent.getPartitionContext().getPartitionId(), partitionEvent.getData().getBodyAsString());
        System.out.printf("%nApplication properties (set by device):%n%s", partitionEvent.getData().getProperties());
        System.out.printf("%nSystem properties (set by IoT Hub):%n%s",
            partitionEvent.getData().getSystemProperties());
    }, ex -> {
        System.out.println("Error receiving events " + ex);
    }, () -> {
        System.out.println("Completed receiving events");
    });
}
```

A saída a seguir do código anterior mostra a telemetria de temperatura enviada pelo dispositivo **termostato** plug and Play IOT de não componente que tem apenas o componente padrão. A `dt-dataschema` Propriedade System mostra a ID do modelo:

```cmd/sh
Telemetry received from partition 1:
{"temperature": 10.700000}
Application properties (set by device):
{$.cdid=my-pnp-device}
System properties (set by IoT Hub):
{correlation-id=dd960185-6ddb-4b5f-89bb-e26b0b3c201e, content-encoding=UTF-8, iothub-connection-auth-method={"scope":"device","type":"sas","issuer":"iothub","acceptingIpFilterRule":null}, iothub-enqueuedtime=Tue Oct 20 12:28:10 BST 2020, dt-dataschema=dtmi:com:example:Thermostat;1, absolute-expiry-time=0, iothub-connection-device-id=my-pnp-device, iothub-connection-auth-generation-id=637375776990653481, group-sequence=0, iothub-message-source=Telemetry, creation-time=0, message-id=1c05cece-070b-4e2e-b2e8-e263858594a3, content-type=application/json}

Telemetry received from partition 1:
{"temperature": 10.700000}
Application properties (set by device):
{$.cdid=my-pnp-device}
System properties (set by IoT Hub):
{correlation-id=d10a7350-43ef-4cf6-9db5-a4b08684cd9d, content-encoding=UTF-8, iothub-connection-auth-method={"scope":"device","type":"sas","issuer":"iothub","acceptingIpFilterRule":null}, iothub-enqueuedtime=Tue Oct 20 12:28:15 BST 2020, dt-dataschema=dtmi:com:example:Thermostat;1, absolute-expiry-time=0, iothub-connection-device-id=my-pnp-device, iothub-connection-auth-generation-id=637375776990653481, group-sequence=0, iothub-message-source=Telemetry, creation-time=0, message-id=d3a80af4-1246-41a0-a09a-582a12c17a00, content-type=application/json}
```

A saída a seguir do código anterior mostra a telemetria de temperatura enviada pelo dispositivo de Plug and Play IOT **TemperatureController** de vários componentes. A `dt-subject` Propriedade System mostra o nome do componente que enviou a telemetria. Neste exemplo, os dois componentes são `thermostat1` e `thermostat2` , conforme definido no modelo DTDL. A `dt-dataschema` Propriedade System mostra a ID do modelo:

```cmd/sh
Telemetry received from partition 1:
null
Application properties (set by device):
{$.cdid=my-pnp-device}
System properties (set by IoT Hub):
{correlation-id=413002d0-2107-4c08-8f4a-995ae1f4047b, content-encoding=UTF-8, iothub-connection-auth-method={"scope":"device","type":"sas","issuer":"iothub","acceptingIpFilterRule":null}, iothub-enqueuedtime=Tue Oct 20 12:31:14 BST 2020, dt-dataschema=dtmi:com:example:TemperatureController;1, absolute-expiry-time=0, iothub-connection-device-id=my-pnp-device, iothub-connection-auth-generation-id=637387902591517456, group-sequence=0, iothub-message-source=Telemetry, creation-time=0, message-id=da8bd068-850e-43fb-862f-66080d5969e4, content-type=application/json, dt-subject=thermostat1}

Telemetry received from partition 1:
null
Application properties (set by device):
{$.cdid=my-pnp-device}
System properties (set by IoT Hub):
{correlation-id=2d9407e5-413f-4f8d-bd30-cd153e03c72f, content-encoding=UTF-8, iothub-connection-auth-method={"scope":"device","type":"sas","issuer":"iothub","acceptingIpFilterRule":null}, iothub-enqueuedtime=Tue Oct 20 12:31:14 BST 2020, dt-dataschema=dtmi:com:example:TemperatureController;1, absolute-expiry-time=0, iothub-connection-device-id=my-pnp-device, iothub-connection-auth-generation-id=637387902591517456, group-sequence=0, iothub-message-source=Telemetry, creation-time=0, message-id=ed419c4e-ef2c-4acf-8991-6245059c5fdc, content-type=application/json, dt-subject=thermostat2}
```

## <a name="read-device-twin-change-notifications"></a>Ler notificações de alteração de troca de dispositivo

Você pode configurar o Hub IoT para gerar notificações de alteração de troca de dispositivo para rotear para um ponto de extremidade com suporte. Para saber mais, confira [usar o roteamento de mensagens do Hub IOT para enviar mensagens do dispositivo para a nuvem a diferentes pontos de extremidade > eventos de não telemetria](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events).

O código mostrado no trecho de código Java anterior gera a saída a seguir quando o Hub IoT gera notificações de alteração de dispositivo para um dispositivo termostato sem componente. As propriedades do aplicativo `iothub-message-schema` e `opType` fornecem informações sobre o tipo de notificação de alteração:

```cmd/sh
Telemetry received from partition 1:
{"version":11,"properties":{"reported":{"maxTempSinceLastReboot":43.4,"$metadata":{"$lastUpdated":"2020-10-20T11:50:41.123127Z","maxTempSinceLastReboot":{"$lastUpdated":"2020-10-20T11:50:41.123127Z"}},"$version":10}}}
Application properties (set by device):
{operationTimestamp=2020-10-20T11:50:41.1231270+00:00, opType=updateTwin, hubName=my-pnp-hub, deviceId=my-pnp-device, iothub-message-schema=twinChangeNotification}
System properties (set by IoT Hub):
{user-id=[B@12fd5bb4, correlation-id=11339418426a, content-encoding=utf-8, iothub-enqueuedtime=Tue Oct 20 12:50:41 BST 2020, absolute-expiry-time=0, iothub-connection-device-id=my-pnp-device, group-sequence=0, iothub-message-source=twinChangeEvents, creation-time=0, content-type=application/json}
```

O código mostrado no trecho de código Java anterior gera a saída a seguir quando o Hub IoT gera notificações de alteração de dispositivo de troca para um dispositivo com componentes. Este exemplo mostra a saída quando um dispositivo sensor de temperatura com um componente termostato gera notificações. As propriedades do aplicativo `iothub-message-schema` e `opType` fornecem informações sobre o tipo de notificação de alteração:

```cmd/sh
Telemetry received from partition 1:
{"version":9,"properties":{"reported":{"thermostat1":{"__t":"c","maxTempSinceLastReboot":32.5},"$metadata":{"$lastUpdated":"2020-10-20T11:48:01.2960851Z","thermostat1":{"$lastUpdated":"2020-10-20T11:48:01.2960851Z","__t":{"$lastUpdated":"2020-10-20T11:48:01.2960851Z"},"maxTempSinceLastReboot":{"$lastUpdated":"2020-10-20T11:48:01.2960851Z"}}},"$version":8}}}
Application properties (set by device):
{operationTimestamp=2020-10-20T11:48:01.2960851+00:00, opType=updateTwin, hubName=my-pnp-hub, deviceId=my-pnp-device, iothub-message-schema=twinChangeNotification}
System properties (set by IoT Hub):
{user-id=[B@23949bae, correlation-id=113334d542e1, content-encoding=utf-8, iothub-enqueuedtime=Tue Oct 20 12:48:01 BST 2020, absolute-expiry-time=0, iothub-connection-device-id=my-pnp-device, group-sequence=0, iothub-message-source=twinChangeEvents, creation-time=0, content-type=application/json}
```

## <a name="read-digital-twin-change-notifications"></a>Ler notificações de alteração de troca digital

Você pode configurar o Hub IoT para gerar notificações de alteração de troca digital para rotear para um ponto de extremidade com suporte. Para saber mais, confira [usar o roteamento de mensagens do Hub IOT para enviar mensagens do dispositivo para a nuvem a diferentes pontos de extremidade > eventos de não telemetria](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events).

O código mostrado no trecho de código Java anterior gera a saída a seguir quando o Hub IoT gera notificações de alteração de troca digital para um dispositivo termostato sem componente. As propriedades do aplicativo `iothub-message-schema` e `opType` fornecem informações sobre o tipo de notificação de alteração:

```cmd/sh
Telemetry received from partition 1:
[{"op":"replace","path":"/$metadata/maxTempSinceLastReboot/lastUpdateTime","value":"2020-10-20T11:52:40.627628Z"},{"op":"replace","path":"/maxTempSinceLastReboot","value":16.9}]
Application properties (set by device):
{operationTimestamp=2020-10-20T11:52:40.6276280+00:00, opType=updateTwin, hubName=my-pnp-hub, deviceId=my-pnp-device, iothub-message-schema=digitalTwinChangeNotification}
System properties (set by IoT Hub):
{user-id=[B@4475ce2a, correlation-id=1133db52c0e0, content-encoding=utf-8, iothub-enqueuedtime=Tue Oct 20 12:52:40 BST 2020, absolute-expiry-time=0, iothub-connection-device-id=my-pnp-device, group-sequence=0, iothub-message-source=digitalTwinChangeEvents, creation-time=0, content-type=application/json-patch+json}
```

O código mostrado no trecho de código Java anterior gera a saída a seguir quando o Hub IoT gera notificações de alteração de troca digital para um dispositivo com componentes. Este exemplo mostra a saída quando um dispositivo sensor de temperatura com um componente termostato gera notificações. As propriedades do aplicativo `iothub-message-schema` e `opType` fornecem informações sobre o tipo de notificação de alteração:

```cmd/sh
Telemetry received from partition 1:
[{"op":"add","path":"/thermostat1","value":{"$metadata":{"maxTempSinceLastReboot":{"lastUpdateTime":"2020-10-20T11:31:04.7811405Z"}},"maxTempSinceLastReboot":27.2}}]
Application properties (set by device):
{operationTimestamp=2020-10-20T11:31:04.7811405+00:00, opType=updateTwin, hubName=my-pnp-hub, deviceId=my-pnp-device, iothub-message-schema=digitalTwinChangeNotification}
System properties (set by IoT Hub):
{user-id=[B@75981aa, correlation-id=1130d6f4d212, content-encoding=utf-8, iothub-enqueuedtime=Tue Oct 20 12:31:04 BST 2020, absolute-expiry-time=0, iothub-connection-device-id=my-pnp-device, group-sequence=0, iothub-message-source=digitalTwinChangeEvents, creation-time=0, content-type=application/json-patch+json}
```
