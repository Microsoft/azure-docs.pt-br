---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/24/2020
ms.openlocfilehash: 6a6baa14d7521f4a85350af7b08b5fcbe82ddf6b
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2020
ms.locfileid: "97033809"
---
## <a name="prerequisites"></a>Pré-requisitos

Para concluir as etapas neste artigo, você precisa do seguinte:

* Um aplicativo do Azure IoT Central criado usando o modelo de **Aplicativo personalizado**. Para obter mais informações, consulte o [Criar um início rápido de aplicativo](../articles/iot-central/core/quick-deploy-iot-central.md). O aplicativo precisa ter sido criado em 14 de julho de 2020 ou após essa data.
* Um computador de desenvolvimento com o [Node.js](https://nodejs.org/) versão 6 ou posterior instalado. É possível executar `node --version` na linha de comando para verificar a versão. As instruções deste tutorial pressupõem que você esteja executando o comando **node** no prompt de comando do Windows. Contudo, você pode usar o Node.js em muitos outros sistemas operacionais.
* Uma cópia local do repositório GitHub do [SDK do Microsoft Azure IoT para Node.js](https://github.com/Azure/azure-iot-sdk-node) que contém o código de exemplo. Use este link para baixar uma cópia do repositório: [Baixar ZIP](https://github.com/Azure/azure-iot-sdk-node/archive/master.zip). Em seguida, descompacte o arquivo em uma localização adequada no computador local.

## <a name="review-the-code"></a>Examine o código

Na cópia do SDK do Microsoft Azure IoT para Node.js baixado anteriormente, abra o arquivo *azure-iot-sdk-node/device/samples/pnp/simple_thermostat.js* em um editor de texto.

Quando você executa o exemplo para se conectar ao IoT Central, ele usa o DPS (Serviço de Provisionamento de Dispositivos) para registrar o dispositivo e gerar uma cadeia de conexão. O exemplo recupera as informações de conexão do DPS necessárias por meio do ambiente de linha de comando.

O método `main`:

* Cria um objeto `client` e define a ID do modelo `dtmi:com:example:Thermostat;1` antes de abrir a conexão. O IoT Central usa a ID do modelo para identificar ou gerar o modelo de dispositivo para este dispositivo. Para saber mais, confira [Associar um dispositivo a um modelo de dispositivo automaticamente](../articles/iot-central/core/concepts-get-connected.md#associate-a-device-with-a-device-template).
* Cria um manipulador de comando.
* Inicia um loop para enviar a telemetria de temperatura a cada 10 segundos.
* Envia a propriedade `maxTempSinceLastReboot` ao IoT Central. O IoT Central ignora a propriedade `serialNumber` porque ela não faz parte do modelo de dispositivo.
* Cria um manipulador de propriedade gravável.

```javascript
async function main() {

  // ...

  // fromConnectionString must specify a transport, coming from any transport package.
  const client = Client.fromConnectionString(deviceConnectionString, Protocol);

  let resultTwin;
  try {
    // Add the modelId here
    await client.setOptions(modelIdObject);
    await client.open();

    client.onDeviceMethod(commandMaxMinReport, commandHandler);

    // Send Telemetry every 10 secs
    let index = 0;
    intervalToken = setInterval(() => {
      sendTelemetry(client, index).catch((err) => console.log('error', err.toString()));
      index += 1;
    }, telemetrySendInterval);

    // attach a standard input exit listener
    attachExitHandler(client);

    // Deal with twin
    try {
      resultTwin = await client.getTwin();
      const patchRoot = createReportPropPatch({ serialNumber: deviceSerialNum });
      const patchThermostat = createReportPropPatch({
        maxTempSinceLastReboot: deviceTemperatureSensor.getMaxTemperatureValue()
      });

      // the below things can only happen once the twin is there
      updateComponentReportedProperties(resultTwin, patchRoot);
      updateComponentReportedProperties(resultTwin, patchThermostat);

      // Setup the handler for desired properties
      desiredPropertyPatchHandler(resultTwin);

    } catch (err) {
      console.error('could not retrieve twin or report twin properties\n' + err.toString());
    }
  } catch (err) {
    console.error('could not connect Plug and Play client or could not attach interval function for telemetry\n' + err.toString());
  }
}
```

A função `provisionDevice` mostra como o dispositivo usa o DPS para se registrar no IoT Central e se conectar a ele. O conteúdo inclui a ID do modelo que o IoT Central usa para [associar o dispositivo a um modelo de dispositivo](../articles/iot-central/core/concepts-get-connected.md#associate-a-device-with-a-device-template):

```javascript
async function provisionDevice(payload) {
  var provSecurityClient = new SymmetricKeySecurityClient(registrationId, symmetricKey);
  var provisioningClient = ProvisioningDeviceClient.create(provisioningHost, idScope, new ProvProtocol(), provSecurityClient);

  if (!!(payload)) {
    provisioningClient.setProvisioningPayload(payload);
  }

  try {
    let result = await provisioningClient.register();
    deviceConnectionString = 'HostName=' + result.assignedHub + ';DeviceId=' + result.deviceId + ';SharedAccessKey=' + symmetricKey;
  } catch (err) {
    console.error("error registering device: " + err.toString());
  }
}
```

A função `sendTelemetry` mostra como o dispositivo envia a telemetria de temperatura para o IoT Central. O método `getCurrentTemperatureObject` retorna um objeto semelhante a `{ temperature: 45.6 }`:

```javascript
async function sendTelemetry(deviceClient, index) {
  console.log('Sending telemetry message %d...', index);
  const msg = new Message(
    JSON.stringify(
      deviceTemperatureSensor.updateSensor().getCurrentTemperatureObject()
    )
  );
  msg.contentType = 'application/json';
  msg.contentEncoding = 'utf-8';
  await deviceClient.sendEvent(msg);
}
```

O método `main` usa os dois métodos a seguir para enviar a propriedade `maxTempSinceLastReboot` ao IoT Central. O método `main` chama `createReportPropPatch` com um objeto semelhante a `{maxTempSinceLastReboot: 80.9}`:

```javascript
const createReportPropPatch = (propertiesToReport) => {
  let patch;
  patch = { };
  patch = propertiesToReport;
  return patch;
};

const updateComponentReportedProperties = (deviceTwin, patch) => {
  deviceTwin.properties.reported.update(patch, function (err) {
    if (err) throw err;
    console.log('Properties have been reported for component');
  });
};
```

O método `main` usa os dois métodos a seguir para processar as atualizações na propriedade gravável _temperatura de destino_ por meio do IoT Central. Observe como `propertyUpdateHandle` compila a resposta com a versão e o código de status:

```javascript
const desiredPropertyPatchHandler = (deviceTwin) => {
  deviceTwin.on('properties.desired', (delta) => {
    const versionProperty = delta.$version;

    Object.entries(delta).forEach(([propertyName, propertyValue]) => {
      if (propertyName !== '$version') {
        propertyUpdateHandler(deviceTwin, propertyName, null, propertyValue, versionProperty);
      }
    });
  });
};

const propertyUpdateHandler = (deviceTwin, propertyName, reportedValue, desiredValue, version) => {
  console.log('Received an update for property: ' + propertyName + ' with value: ' + JSON.stringify(desiredValue));
  const patch = createReportPropPatch(
    { [propertyName]:
      {
        'value': desiredValue,
        'ac': 200,
        'ad': 'Successfully executed patch for ' + propertyName,
        'av': version
      }
    });
  updateComponentReportedProperties(deviceTwin, patch);
  console.log('updated the property');
};
```

O método `main` usa os dois métodos a seguir para processar chamadas ao comando `getMaxMinReport`. O método `getMaxMinReportObject` gera o relatório como um objeto JSON:

```javascript
const commandHandler = async (request, response) => {
  switch (request.methodName) {
  case commandMaxMinReport: {
    console.log('MaxMinReport ' + request.payload);
    await sendCommandResponse(request, response, 200, deviceTemperatureSensor.getMaxMinReportObject());
    break;
  }
  default:
    await sendCommandResponse(request, response, 404, 'unknown method');
    break;
  }
};

const sendCommandResponse = async (request, response, status, payload) => {
  try {
    await response.send(status, payload);
    console.log('Response to method \'' + request.methodName +
              '\' sent successfully.' );
  } catch (err) {
    console.error('An error ocurred when sending a method response:\n' +
              err.toString());
  }
};
```

## <a name="get-connection-information"></a>Obter informações de conexão

[!INCLUDE [iot-central-connection-configuration](iot-central-connection-configuration.md)]

## <a name="run-the-code"></a>Executar o código

Para executar o aplicativo de exemplo, abra um ambiente de linha de comando e acesse a pasta *azure-iot-sdk-node/device/samples/pnp* que contém o arquivo de exemplo *simple_thermostat.js*.

[!INCLUDE [iot-central-connection-environment](iot-central-connection-environment.md)]

Instale os pacotes necessários:

```cmd/sh
npm install
```

Execute o exemplo:

```cmd/sh
node simple_thermostat.js
```

A saída a seguir mostra o dispositivo se registrando e se conectando ao IoT Central. Em seguida, o exemplo envia a propriedade `maxTempSinceLastReboot` antes de começar a enviar a telemetria:

```output
registration succeeded
assigned hub=iotc-.......azure-devices.net
deviceId=sample-device-01
payload=undefined
Connecting using connection string HostName=iotc-........azure-devices.net;DeviceId=sample-device-01;SharedAccessKey=Ci....=
Enabling the commands on the client
Please enter q or Q to exit sample.
The following properties will be updated for root interface:
{ maxTempSinceLastReboot: 55.20309427428496 }
Properties have been reported for component
Sending telemetry message 0...
Sending telemetry message 1...
Sending telemetry message 2...
Sending telemetry message 3...
```

[!INCLUDE [iot-central-monitor-thermostat](iot-central-monitor-thermostat.md)]

Você pode ver como o dispositivo responde a comandos e a atualizações de propriedade:

```output
MaxMinReport 2020-10-15T12:00:00.000Z
Response to method 'getMaxMinReport' sent successfully.

...

Received an update for property: targetTemperature with value: {"value":86.3}
The following properties will be updated for root interface:
{
  targetTemperature: {
    value: { value: 86.3 },
    ac: 200,
    ad: 'Successfully executed patch for targetTemperature',
    av: 2
  }
}
```
