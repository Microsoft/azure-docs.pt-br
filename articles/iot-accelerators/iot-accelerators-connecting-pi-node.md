---
title: Provisionar o Raspberry Pi para monitoramento remoto em Node.js – Azure | Microsoft Docs
description: Descreve como conectar um dispositivo Raspberry Pi ao acelerador de solução de Monitoramento Remoto usando um aplicativo escrito em Node.js.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 01/24/2018
ms.author: dobett
ms.openlocfilehash: 20d50ac4ac4a1919077ebe67bb529e2dc5abf187
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61449567"
---
# <a name="connect-your-raspberry-pi-device-to-the-remote-monitoring-solution-accelerator-nodejs"></a>Conectar seu dispositivo Raspberry Pi ao acelerador da solução de monitoramento remoto (Node.js)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Este tutorial mostra como conectar um dispositivo físico ao acelerador de solução de monitoramento remoto. Neste tutorial, você usará o Node.js, que é uma boa opção para ambientes com poucas restrições de recursos.

Se você preferir simular um dispositivo, consulte [Criar e testar um novo dispositivo simulado](iot-accelerators-remote-monitoring-create-simulated-device.md).

### <a name="required-hardware"></a>Requisitos de hardware

Um computador desktop para que você possa se conectar remotamente à linha de comando no Raspberry Pi.

[Kit de início de IoT da Microsoft para Raspberry Pi 3](https://azure.microsoft.com/develop/iot/starter-kits/) ou componentes equivalentes. Este tutorial usa os seguintes itens do kit:

- Raspberry Pi 3
- Cartão MicroSD (com NOOBS)
- Um cabo USB Mini
- Um cabo Ethernet

### <a name="required-desktop-software"></a>Software de área de trabalho necessário

É necessário um cliente SSH em seu computador desktop para que você possa acessar remotamente a linha de comando no Raspberry Pi.

- O Windows não inclui um cliente SSH. Recomendamos o uso de [PuTTY](https://www.putty.org/).
- A maioria das distribuições do Linux e Mac OS incluem o utilitário de linha de comando do SSH. Para obter mais informações, consulte [SSH usando o Linux ou Mac OS](https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md).

### <a name="required-raspberry-pi-software"></a>Software do Raspberry Pi necessário

Se você ainda não tiver feito isso, instale o Node.js versão 4.0.0 ou posterior em seu Raspberry Pi. As etapas a seguir mostram como instalar o Node.js v6 em seu Raspberry Pi:

1. Conecte-se ao seu Raspberry Pi usando `ssh`. Para obter mais informações, consulte [SSH (Secure Shell)](https://www.raspberrypi.org/documentation/remote-access/ssh/README.md) no [site do Raspberry Pi](https://www.raspberrypi.org/).

1. Use o seguinte comando para atualizar o Raspberry Pi:

    ```sh
    sudo apt-get update
    ```

1. Use os seguintes comandos para remover qualquer instalação existente do Node. js de seu Raspberry Pi:

    ```sh
    sudo apt-get remove nodered -y
    sudo apt-get remove nodejs nodejs-legacy -y
    sudo apt-get remove npm  -y
    ```

1. Use os seguintes comandos para realizar o download e instar o Node.js v6 no seu Raspberry Pi:

    ```sh
    curl -sL https://deb.nodesource.com/setup_6.x | sudo bash -
    sudo apt-get install nodejs npm
    ```

1. Use o seguinte comando para verificar se você instalou o Node.js v6.11.4 com êxito:

    ```sh
    node --version
    ```

## <a name="create-a-nodejs-solution"></a>Criar uma solução Node.js

Conclua as etapas a seguir usando a conexão `ssh` com seu Raspberry Pi:

1. Crie uma pasta chamada `remotemonitoring` na pasta base do Raspberry Pi. Navegue até essa pasta na linha de comando:

    ```sh
    cd ~
    mkdir remotemonitoring
    cd remotemonitoring
    ```

1. Para baixar e instalar os pacotes que você precisa para concluir o aplicativo de exemplo, execute os seguintes comandos:

    ```sh
    npm install async azure-iot-device azure-iot-device-mqtt
    ```

1. Na pasta `remotemonitoring`, crie um arquivo chamado **remote_monitoring.js**. Abra esse arquivo em um editor de texto. No Raspberry Pi, você pode usar os editores de texto `nano` ou `vi`.

1. No arquivo **remote_monitoring.js**, adicione as seguintes instruções `require`:

    ```javascript
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var Message = require('azure-iot-device').Message;
    var async = require('async');
    ```

1. Adicione as declarações de variável a seguir após as instruções `require` . Substitua o valor de espaço reservado `{device connection string}` pelo valor indicado para o dispositivo provisionado na solução de monitoramento remoto:

    ```javascript
    var connectionString = '{device connection string}';
    ```

1. Para definir alguns dados telemétricos base, adicione as seguintes variáveis:

    ```javascript
    var temperature = 50;
    var temperatureUnit = 'F';
    var humidity = 50;
    var humidityUnit = '%';
    var pressure = 55;
    var pressureUnit = 'psig';
    ```

1. Para definir alguns valores de propriedade, adicione as seguintes variáveis:

    ```javascript
    var schema = "real-chiller;v1";
    var deviceType = "RealChiller";
    var deviceFirmware = "1.0.0";
    var deviceFirmwareUpdateStatus = "";
    var deviceLocation = "Building 44";
    var deviceLatitude = 47.638928;
    var deviceLongitude = -122.13476;
    var deviceOnline = true;
    ```

1. Adicione a seguinte variável para definir as propriedades relatadas a serem enviadas para a solução. Essas propriedades incluem metadados para exibição na interface do usuário da Web:

    ```javascript
    var reportedProperties = {
      "SupportedMethods": "Reboot,FirmwareUpdate,EmergencyValveRelease,IncreasePressure",
      "Telemetry": {
        [schema]: ""
      },
      "Type": deviceType,
      "Firmware": deviceFirmware,
      "FirmwareUpdateStatus": deviceFirmwareUpdateStatus,
      "Location": deviceLocation,
      "Latitude": deviceLatitude,
      "Longitude": deviceLongitude,
      "Online": deviceOnline
    }
    ```

1. Para imprimir os resultados da operação, adicione a função auxiliar a seguir:

    ```javascript
    function printErrorFor(op) {
        return function printError(err) {
            if (err) console.log(op + ' error: ' + err.toString());
        };
    }
    ```

1. Adicione a seguinte função auxiliar para usar para tornar os valores de telemetria aleatórios:

     ```javascript
     function generateRandomIncrement() {
         return ((Math.random() * 2) - 1);
     }
     ```

1. Adicione a função genérica a seguir para lidar com chamadas de método diretas da solução. A função exibe informações sobre o método direto que foi chamado, mas neste exemplo não modifica o dispositivo de nenhuma maneira. A solução usa métodos diretos para agir nos dispositivos:

     ```javascript
     function onDirectMethod(request, response) {
       // Implement logic asynchronously here.
       console.log('Simulated ' + request.methodName);

       // Complete the response
       response.send(200, request.methodName + ' was called on the device', function (err) {
         if (err) console.error('Error sending method response :\n' + err.toString());
         else console.log('200 Response to method \'' + request.methodName + '\' sent successfully.');
       });
     }
     ```

1. Adicione a função a seguir para lidar com chamadas de método direto **FirmwareUpdate** da solução. A função verifica os parâmetros passados na carga do método direto e, em seguida, é executada assincronamente uma simulação de atualização de firmware:

     ```javascript
     function onFirmwareUpdate(request, response) {
       // Get the requested firmware version from the JSON request body
       var firmwareVersion = request.payload.Firmware;
       var firmwareUri = request.payload.FirmwareUri;
      
       // Ensure we got a firmware values
       if (!firmwareVersion || !firmwareUri) {
         response.send(400, 'Missing firmware value', function(err) {
           if (err) console.error('Error sending method response :\n' + err.toString());
           else console.log('400 Response to method \'' + request.methodName + '\' sent successfully.');
         });
       } else {
         // Respond the cloud app for the device method
         response.send(200, 'Firmware update started.', function(err) {
           if (err) console.error('Error sending method response :\n' + err.toString());
           else {
             console.log('200 Response to method \'' + request.methodName + '\' sent successfully.');

             // Run the simulated firmware update flow
             runFirmwareUpdateFlow(firmwareVersion, firmwareUri);
           }
         });
       }
     }
     ```

1. Adicione a função a seguir para simular um fluxo de atualização de firmware demorado que relate o progresso para a solução:

     ```javascript
     // Simulated firmwareUpdate flow
     function runFirmwareUpdateFlow(firmwareVersion, firmwareUri) {
       console.log('Simulating firmware update flow...');
       console.log('> Firmware version passed: ' + firmwareVersion);
       console.log('> Firmware URI passed: ' + firmwareUri);
       async.waterfall([
         function (callback) {
           console.log("Image downloading from " + firmwareUri);
           var patch = {
             FirmwareUpdateStatus: 'Downloading image..'
           };
           reportUpdateThroughTwin(patch, callback);
           sleep(10000, callback);
         },
         function (callback) {
           console.log("Downloaded, applying firmware " + firmwareVersion);
           deviceOnline = false;
           var patch = {
             FirmwareUpdateStatus: 'Applying firmware..',
             Online: false
           };
           reportUpdateThroughTwin(patch, callback);
           sleep(8000, callback);
         },
         function (callback) {
           console.log("Rebooting");
           var patch = {
             FirmwareUpdateStatus: 'Rebooting..'
           };
           reportUpdateThroughTwin(patch, callback);
           sleep(10000, callback);
         },
         function (callback) {
           console.log("Firmware updated to " + firmwareVersion);
           deviceOnline = true;
           var patch = {
             FirmwareUpdateStatus: 'Firmware updated',
             Online: true,
             Firmware: firmwareVersion
           };
           reportUpdateThroughTwin(patch, callback);
           callback(null);
         }
       ], function(err) {
         if (err) {
           console.error('Error in simulated firmware update flow: ' + err.message);
         } else {
           console.log("Completed simulated firmware update flow");
         }
       });

       // Helper function to update the twin reported properties.
       function reportUpdateThroughTwin(patch, callback) {
         console.log("Sending...");
         console.log(JSON.stringify(patch, null, 2));
         client.getTwin(function(err, twin) {
           if (!err) {
             twin.properties.reported.update(patch, function(err) {
               if (err) callback(err);
             });      
           } else {
             if (err) callback(err);
           }
         });
       }

       function sleep(milliseconds, callback) {
         console.log("Simulate a delay (milleseconds): " + milliseconds);
         setTimeout(function () {
           callback(null);
         }, milliseconds);
       }
     }
     ```

1. Adicione o seguinte código para enviar dados de telemetria para a solução. O aplicativo cliente adiciona propriedades à mensagem para identificar o esquema de mensagem:

     ```javascript
     function sendTelemetry(data, schema) {
       if (deviceOnline) {
         var d = new Date();
         var payload = JSON.stringify(data);
         var message = new Message(payload);
         message.properties.add('iothub-creation-time-utc', d.toISOString());
         message.properties.add('iothub-message-schema', schema);

         console.log('Sending device message data:\n' + payload);
         client.sendEvent(message, printErrorFor('send event'));
       } else {
         console.log('Offline, not sending telemetry');
       }
     }
     ```

1. Adicione o seguinte código para criar uma instância do cliente:

     ```javascript
     var client = Client.fromConnectionString(connectionString, Protocol);
     ```

1. Adicione o seguinte código a:

    * Abra a conexão.
    * Configure um manipulador para as propriedades desejadas.
    * Envie as propriedades relatadas.
    * Registre manipuladores para os métodos diretos. O exemplo usa um manipulador separado para o método direto de atualização de firmware.
    * Comece a enviar a telemetria.

      ```javascript
      client.open(function (err) {
      if (err) {
        printErrorFor('open')(err);
      } else {
        // Create device Twin
        client.getTwin(function (err, twin) {
          if (err) {
            console.error('Could not get device twin');
          } else {
            console.log('Device twin created');

            twin.on('properties.desired', function (delta) {
              // Handle desired properties set by solution
              console.log('Received new desired properties:');
              console.log(JSON.stringify(delta));
            });

            // Send reported properties
            twin.properties.reported.update(reportedProperties, function (err) {
              if (err) throw err;
              console.log('Twin state reported');
            });

            // Register handlers for all the method names we are interested in.
            // Consider separate handlers for each method.
            client.onDeviceMethod('Reboot', onDirectMethod);
            client.onDeviceMethod('FirmwareUpdate', onFirmwareUpdate);
            client.onDeviceMethod('EmergencyValveRelease', onDirectMethod);
            client.onDeviceMethod('IncreasePressure', onDirectMethod);
          }
        });

        // Start sending telemetry
        var sendDeviceTelemetry = setInterval(function () {
          temperature += generateRandomIncrement();
          pressure += generateRandomIncrement();
          humidity += generateRandomIncrement();
          var data = {
            'temperature': temperature,
            'temperature_unit': temperatureUnit,
            'humidity': humidity,
            'humidity_unit': humidityUnit,
            'pressure': pressure,
            'pressure_unit': pressureUnit
          };
          sendTelemetry(data, schema)
        }, 5000);

        client.on('error', function (err) {
          printErrorFor('client')(err);
          if (sendTemperatureInterval) clearInterval(sendTemperatureInterval);
          if (sendHumidityInterval) clearInterval(sendHumidityInterval);
          if (sendPressureInterval) clearInterval(sendPressureInterval);
          client.close(printErrorFor('client.close'));
        });
      }
      });
      ```

1. Salve as alterações no arquivo **remote_monitoring.js**.

1. Para inicializar o aplicativo de exemplo, execute o seguinte comando no prompt de comando no Raspberry Pi:

     ```sh
     node remote_monitoring.js
     ```

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
