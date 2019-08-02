---
title: Conectar um aplicativo cliente Node.js genérico ao Azure IoT Central | Microsoft Docs
description: Como um desenvolvedor de dispositivo, como se conectar a um dispositivo genérico do Node. js ao seu aplicativo do Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 06/14/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 90e4a061e38fdd3a13a640363069fae3a18e0b49
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444236"
---
# <a name="connect-a-generic-client-application-to-your-azure-iot-central-application-nodejs"></a>Conectar um aplicativo cliente genérico ao aplicativo Azure IoT Central (Node.js)

Este artigo descreve, como um desenvolvedor de dispositivos, como se conectar a um aplicativo Node.js genérico representando um dispositivo real ao aplicativo Microsoft Azure IoT Central.

## <a name="before-you-begin"></a>Antes de começar

Para concluir as etapas neste artigo, você precisa do seguinte:

1. Um aplicativo Azure IoT Central. Para obter mais informações, consulte o [Criar um início rápido de aplicativo](quick-deploy-iot-central.md).
1. Um computador de desenvolvimento com [Node.js](https://nodejs.org/) versão 4.0.0 ou posterior instalado. É possível executar `node --version` na linha de comando para verificar a versão. O Node.js está disponível para uma ampla variedade de sistemas operacionais.

## <a name="create-a-device-template"></a>Criar um modelo de dispositivo

Em seu aplicativo do Azure IoT Central, você precisa de um modelo de dispositivo com as medidas, as propriedades do dispositivo, configurações e comandos seguintes:

### <a name="telemetry-measurements"></a>Medidas de telemetria

Adicionar a telemetria a seguir sobre o **medidas** página:

| Nome para exibição | Nome do campo  | Unidades | Min | max | Casas Decimais |
| ------------ | ----------- | ----- | --- | --- | -------------- |
| Temperatura  | temperatura | F     | 60  | 110 | 0              |
| Umidade     | umidade    | %     | 0   | 100 | 0              |
| Pressão     | pressão    | kPa   | 80  | 110 | 0              |

> [!NOTE]
> O tipo de dados da medição de telemetria é um número de ponto flutuante.

Insira os nomes dos campos exatamente como mostrado na tabela no modelo de dispositivo. Se os nomes de campo não corresponderem aos nomes de propriedade no código do dispositivo correspondente, a telemetria não pode ser exibida no aplicativo.

### <a name="state-measurements"></a>Medidas de estado

Adicione o seguinte o estado na **medidas** página:

| Nome para exibição | Nome do campo  | Valor 1 | Nome para exibição | Valor 2 | Nome para exibição |
| ------------ | ----------- | --------| ------------ | ------- | ------------ | 
| Modo ventilação     | fanmode     | 1       | Executando      | 0       | Parado      |

> [!NOTE]
> O tipo de dados da medida State é string.

Insira os nomes dos campos exatamente como mostrado na tabela no modelo de dispositivo. Se os nomes de campo não corresponderem aos nomes de propriedade no código do dispositivo correspondente, o estado não pode ser exibido no aplicativo.

### <a name="event-measurements"></a>Medidas de evento

Adicione o seguinte evento sobre o **medidas** página:

| Nome para exibição | Nome do campo  | severity |
| ------------ | ----------- | -------- |
| Superaquecimento  | overheat    | Erro    |

> [!NOTE]
> O tipo de dados da medida de evento é cadeia de caracteres.

### <a name="location-measurements"></a>Medidas de local

Adicionar a medida de local a seguir a **medidas** página:

| Nome para exibição | Nome do campo  |
| ------------ | ----------- |
| Location     | location    |

A medida de local, tipo de dados é composto de dois flutuante números de ponto de longitude e latitude e um número de ponto flutuante opcional para altitude.

Insira os nomes dos campos exatamente como mostrado na tabela no modelo de dispositivo. Se os nomes de campo não corresponderem aos nomes de propriedade no código do dispositivo correspondente, o local não pode ser exibido no aplicativo.

### <a name="device-properties"></a>Propriedades do dispositivo

Adicione as seguintes propriedades de dispositivo na **propriedades** página:

| Nome para exibição        | Nome do campo        | Tipo de dados |
| ------------------- | ----------------- | --------- |
| Número de série       | serialNumber      | text      |
| Fabricante do dispositivo | manufacturer      | text      |

Insira os nomes de campo exatamente conforme mostrado na tabela no modelo de dispositivo. Se os nomes de campo não corresponderem aos nomes de propriedade no código do dispositivo correspondente, as propriedades não podem ser exibidas no aplicativo.

### <a name="settings"></a>Configurações

Adicione o seguinte **número** configurações de **configurações** página:

| Nome para exibição    | Nome do campo     | Unidades | Decimais | Min | max  | Inicial |
| --------------- | -------------- | ----- | -------- | --- | ---- | ------- |
| Velocidade da ventoinha       | fanSpeed       | rpm   | 0        | 0   | 3000 | 0       |
| Temperatura definida | setTemperature | F     | 0        | 20  | 200  | 80      |

Insira o nome de campo exatamente conforme mostrado na tabela no modelo de dispositivo. Se os nomes de campo não corresponderem aos nomes de propriedade no código do dispositivo correspondente, o dispositivo não pode receber o valor da configuração.

### <a name="commands"></a>Comandos

Adicione o seguinte comando na **comandos** página:

| Nome para exibição    | Nome do campo     | Tempo limite padrão | Tipo de Dados |
| --------------- | -------------- | --------------- | --------- |
| Contagem regressiva       | Contagem regressiva      | 30              | número    |

Adicione o seguinte campo de entrada para o comando de contagem regressiva:

| Nome para exibição    | Nome do campo     | Tipo de Dados | Value |
| --------------- | -------------- | --------- | ----- |
| Contagem de      | countFrom      | número    | 10    |

Insira nomes de campo exatamente como mostrado nas tabelas no modelo de dispositivo. Se os nomes de campo não corresponderem aos nomes de propriedade no código do dispositivo correspondente, o dispositivo não pode processar o comando.

## <a name="add-a-real-device"></a>Adicionar um dispositivo real

Em seu aplicativo do Azure IoT Central, adicione um dispositivo real para o modelo de dispositivo que você criou na seção anterior.

Em seguida, siga as instruções no tutorial "Adicionar um dispositivo" para [gerar uma cadeia de caracteres de conexão para o dispositivo real](tutorial-add-device.md#generate-connection-string). Você pode usar essa cadeia de caracteres de conexão na seção a seguir:

### <a name="create-a-nodejs-application"></a>Criar um aplicativo do Node.js

As etapas a seguir mostram como criar um aplicativo cliente que implementa o dispositivo real que você adicionou ao aplicativo. Aqui, o aplicativo Node.js representa o dispositivo real. 

1. Crie uma pasta chamada `connected-air-conditioner-adv` no computador. Navegue até essa pasta no ambiente de linha de comando.

1. Para inicializar o projeto Node.js, execute os comandos a seguir:

    ```cmd/sh
    npm init
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

1. Crie um arquivo chamado **connectedAirConditionerAdv.js** na pasta `connected-air-conditioner-adv`.

1. Adicione as `require` instruções no início do arquivo **connectedAirConditionerAdv.js**:

    ```javascript
    "use strict";

    // Use the Azure IoT device SDK for devices that connect to Azure IoT Central.
    var clientFromConnectionString = require('azure-iot-device-mqtt').clientFromConnectionString;
    var Message = require('azure-iot-device').Message;
    var ConnectionString = require('azure-iot-device').ConnectionString;
    ```

1. Adicione as declarações de variáveis a seguir ao arquivo:

    ```javascript
    var connectionString = '{your device connection string}';
    var targetTemperature = 0;
    var locLong = -122.1215;
    var locLat = 47.6740;
    var client = clientFromConnectionString(connectionString);
    ```

    Atualizar o espaço reservado `{your device connection string}` com o [cadeia de conexão do dispositivo](tutorial-add-device.md#generate-connection-string). Neste exemplo, você deve inicializar `targetTemperature` como zero, você pode usar a leitura atual do dispositivo ou um valor do dispositivo gêmeo.

1. Para enviar telemetria, estado, evento e as medidas de local para seu aplicativo do Azure IoT Central, adicione a seguinte função para o arquivo:

    ```javascript
    // Send device measurements.
    function sendTelemetry() {
      var temperature = targetTemperature + (Math.random() * 15);
      var humidity = 70 + (Math.random() * 10);
      var pressure = 90 + (Math.random() * 5);
      var fanmode = 0;
      var locationLong = locLong - (Math.random() / 100);
      var locationLat = locLat - (Math.random() / 100);
      var data = JSON.stringify({
        temperature: temperature,
        humidity: humidity,
        pressure: pressure,
        fanmode: (temperature > 25) ? "1" : "0",
        overheat: (temperature > 35) ? "ER123" : undefined,
        location: {
            lon: locationLong,
            lat: locationLat }
        });
      var message = new Message(data);
      client.sendEvent(message, (err, res) => console.log(`Sent message: ${message.getData()}` +
        (err ? `; error: ${err.toString()}` : '') +
        (res ? `; status: ${res.constructor.name}` : '')));
    }
    ```

1. Para enviar as propriedades de dispositivo ao aplicativo Azure IoT Central, adicione a função a seguir ao arquivo:

    ```javascript
    // Send device reported properties.
    function sendDeviceProperties(twin, properties) {
      twin.properties.reported.update(properties, (err) => console.log(`Sent device properties: ${JSON.stringify(properties)}; ` +
        (err ? `error: ${err.toString()}` : `status: success`)));
    }
    ```

1. Para definir as configurações que o dispositivo responde, adicione a definição a seguir:

    ```javascript
    // Add any settings your device supports,
    // mapped to a function that is called when the setting is changed.
    var settings = {
      'fanSpeed': (newValue, callback) => {
          // Simulate it taking 1 second to set the fan speed.
          setTimeout(() => {
            callback(newValue, 'completed');
          }, 1000);
      },
      'setTemperature': (newValue, callback) => {
        // Simulate the temperature setting taking two steps.
        setTimeout(() => {
          targetTemperature = targetTemperature + (newValue - targetTemperature) / 2;
          callback(targetTemperature, 'pending');
          setTimeout(() => {
            targetTemperature = newValue;
            callback(targetTemperature, 'completed');
          }, 5000);
        }, 5000);
      }
    };
    ```

1. Para identificar configurações atualizadas do aplicativo Azure IoT Central, adicione o seguinte ao arquivo:

    ```javascript
    // Handle settings changes that come from Azure IoT Central via the device twin.
    function handleSettings(twin) {
      twin.on('properties.desired', function (desiredChange) {
        for (let setting in desiredChange) {
          if (settings[setting]) {
            console.log(`Received setting: ${setting}: ${desiredChange[setting].value}`);
            settings[setting](desiredChange[setting].value, (newValue, status, message) => {
              var patch = {
                [setting]: {
                  value: newValue,
                  status: status,
                  desiredVersion: desiredChange.$version,
                  message: message
                }
              }
              twin.properties.reported.update(patch, (err) => console.log(`Sent setting update for ${setting}; ` +
                (err ? `error: ${err.toString()}` : `status: success`)));
            });
          }
        }
      });
    }
    ```

1. Adicione o seguinte código para manipular um comando de contagem regressiva enviado do aplicativo IoT Central:

    ```javascript
    // Handle countdown command
    function onCountdown(request, response) {
      console.log('Received call to countdown');

      var countFrom = (typeof(request.payload.countFrom) === 'number' && request.payload.countFrom < 100) ? request.payload.countFrom : 10;

      response.send(200, (err) => {
        if (err) {
          console.error('Unable to send method response: ' + err.toString());
        } else {
          client.getTwin((err, twin) => {
            function doCountdown(){
              if ( countFrom >= 0 ) {
                var patch = {
                  countdown:{
                    value: countFrom
                  }
                };
                sendDeviceProperties(twin, patch);
                countFrom--;
                setTimeout(doCountdown, 2000 );
              }
            }

            doCountdown();
          });
        }
      });
    }
    ```

1. Adicione o seguinte código para concluir a conexão ao Azure IoT Central e associe as funções no código cliente:

    ```javascript
    // Handle device connection to Azure IoT Central.
    var connectCallback = (err) => {
      if (err) {
        console.log(`Device could not connect to Azure IoT Central: ${err.toString()}`);
      } else {
        console.log('Device successfully connected to Azure IoT Central');

        // Create handler for countdown command
        client.onDeviceMethod('countdown', onCountdown);

        // Send telemetry measurements to Azure IoT Central every 1 second.
        setInterval(sendTelemetry, 1000);

        // Get device twin from Azure IoT Central.
        client.getTwin((err, twin) => {
          if (err) {
            console.log(`Error getting device twin: ${err.toString()}`);
          } else {
            // Send device properties once on device start up.
            var properties = {
              serialNumber: '123-ABC',
              manufacturer: 'Contoso'
            };
            sendDeviceProperties(twin, properties);

            // Apply device settings and handle changes to device settings.
            handleSettings(twin);
          }
        });
      }
    };

    // Start the device (connect it to Azure IoT Central).
    client.open(connectCallback);
    ```

## <a name="run-your-nodejs-application"></a>Execute o aplicativo Node.js

Execute o comando a seguir no ambiente de linha de comando:

```cmd/sh
node connectedAirConditionerAdv.js
```

Como um operador no aplicativo Azure IoT Central, para o dispositivo real, é possível:

* Exibir a telemetria na página **Medidas**:

    ![Exibir telemetria](media/howto-connect-nodejs/viewtelemetry.png)

* Exiba a localização na **medidas** página:

    ![Medidas de local de exibição](media/howto-connect-nodejs/viewlocation.png)

* Exibir os valores da propriedade de dispositivo enviados pelo dispositivo na página **Propriedades**. A propriedade blocos atualização do dispositivo quando o dispositivo se conecta:

    ![Exibir propriedades do dispositivo](media/howto-connect-nodejs/viewproperties.png)

* Definir a temperatura do ventilador de velocidade e o destino do **configurações** página:

    ![Configurar velocidade da ventoinha](media/howto-connect-nodejs/setfanspeed.png)

* Chamar o comando de contagem regressiva do **comandos** página:

    ![Chamar o comando de contagem regressiva](media/howto-connect-nodejs/callcountdown.png)

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como se conectar a um cliente genérico do Node. js ao seu aplicativo do Azure IoT Central, a próxima etapa sugerida é saber como [configurar um modelo de dispositivo personalizado](howto-set-up-template.md) para seu próprio dispositivo de IoT.
