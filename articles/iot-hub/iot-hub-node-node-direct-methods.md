---
title: "Métodos diretos do Hub IoT do Azure (Node) | Microsoft Docs"
description: "Como usar os métodos diretos do Hub IoT do Azure. Use os SDKs do IoT do Azure para Node.js para implementar um aplicativo de dispositivo simulado que inclui um método direto e um aplicativo de serviço que invoca o método direto."
services: iot-hub
documentationcenter: 
author: nberdy
manager: timlt
editor: 
ms.assetid: ea9c73ca-7778-4e38-a8f1-0bee9d142f04
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/25/2017
ms.author: nberdy
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9a73c25724a239e56c3ea62a8452bb7c3a2b51be
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-direct-methods-on-your-iot-device-with-nodejs"></a>Usar métodos diretos no dispositivo IoT com o Node.js
[!INCLUDE [iot-hub-selector-c2d-methods](../../includes/iot-hub-selector-c2d-methods.md)]

Ao fim deste tutorial, você terá dois aplicativos de console do Node.js:

* **CallMethodOnDevice.js**, que chama um método no aplicativo do dispositivo simulado e exibe a resposta.
* **SimulatedDevice.js**, que se conecta ao Hub IoT com a identidade do dispositivo criada anteriormente e responde ao método chamado pela nuvem.

> [!NOTE]
> O artigo [SDKs de IoT do Azure][lnk-hub-sdks] fornece informações sobre os SDKs de IoT do Azure que você pode usar para criar aplicativos executados em dispositivos e no back-end da solução.
> 
> 

Para concluir este tutorial, você precisará do seguinte:

* Node.js versão 4.0.x ou posterior.
* Uma conta ativa do Azure. (Se você não tem uma conta, pode criar uma [conta gratuita][lnk-free-trial] em apenas alguns minutos.)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Criar um aplicativo de dispositivo simulado
Nesta seção, você cria um aplicativo de console do Node.js que responde a um método chamado pela nuvem.

1. Crie uma nova pasta vazia denominada **simulateddevice**. Na pasta **simulateddevice** , crie um arquivo package.json usando o comando a seguir no prompt de comando. Aceite todos os padrões:
   
    ```
    npm init
    ```
2. No prompt de comando, na pasta **simulateddevice**, execute o seguinte comando para instalar o pacote **azure-iot-device** do SDK do Dispositivo e o pacote **azure-iot-device-mqtt**:
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. Usando um editor de texto, crie um novo arquivo **SimulatedDevice.js** na pasta **simulateddevice**.
4. Adicione as seguintes instruções `require` no início do arquivo **SimulatedDevice.js** :
   
    ```
    'use strict';
   
    var Mqtt = require('azure-iot-device-mqtt').Mqtt;
    var DeviceClient = require('azure-iot-device').Client;
    ```
5. Adicione uma variável **connectionString** e use-a para criar uma instância **DeviceClient**. Substitua **{cadeia de conexão do dispositivo}** pela cadeia de conexão de dispositivo gerada na seção *Criar uma identidade de dispositivo*:
   
    ```
    var connectionString = '{device connection string}';
    var client = DeviceClient.fromConnectionString(connectionString, Mqtt);
    ```
6. Adicione a seguinte função para implementar o método no dispositivo:
   
    ```
    function onWriteLine(request, response) {
        console.log(request.payload);
   
        response.send(200, 'Input was written to log.', function(err) {
            if(err) {
                console.error('An error ocurred when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.' );
            }
        });
    }
    ```
7. Abra a conexão com o Hub IoT e inicialize o ouvinte do método:
   
    ```
    client.open(function(err) {
        if (err) {
            console.error('could not open IotHub client');
        }  else {
            console.log('client opened');
            client.onDeviceMethod('writeLine', onWriteLine);
        }
    });
    ```
8. Salve e feche o arquivo **SimulatedDevice.js** .

> [!NOTE]
> Para simplificar, este tutorial não implementa nenhuma política de repetição. No código de produção, você deve implementar políticas de repetição (tais como tentar conexão novamente), conforme sugerido no artigo do MSDN [Tratamento de Falhas Transitórias][lnk-transient-faults].
> 
> 

## <a name="call-a-method-on-a-device"></a>Chamar um método em um dispositivo
Nesta seção, você cria um aplicativo de console do Node.js que chama um método no aplicativo do dispositivo simulado e, em seguida, exibe a resposta.

1. Crie uma nova pasta vazia denominada **callmethodondevice**. Na pasta **callmethodondevice**, crie um arquivo package.json usando o comando a seguir no prompt de comando. Aceite todos os padrões:
   
    ```
    npm init
    ```
2. No prompt de comando, na pasta **callmethodondevice**, execute o seguinte comando para instalar o pacote **azure-iothub**:
   
    ```
    npm install azure-iothub --save
    ```
3. Usando um editor de texto, crie um arquivo **CallMethodOnDevice.js** na pasta **callmethodondevice**.
4. Adicione as seguintes instruções `require` no início do arquivo **SimulatedDevice.js**:
   
    ```
    'use strict';
   
    var Client = require('azure-iothub').Client;
    ```
5. Adicione a seguinte declaração de variável e substitua o valor de espaço reservado pela cadeia de conexão do Hub IoT para o seu hub:
   
    ```
    var connectionString = '{iothub connection string}';
    var methodName = 'writeLine';
    var deviceId = 'myDeviceId';
    ```
6. Crie o cliente para abrir a conexão a seu Hub IoT.
   
    ```
    var client = Client.fromConnectionString(connectionString);
    ```
7. Adicione a seguinte função para invocar o método de dispositivo e imprimir a resposta do dispositivo no console:
   
    ```
    var methodParams = {
        methodName: methodName,
        payload: 'hello world',
        timeoutInSeconds: 30
    };
   
    client.invokeDeviceMethod(deviceId, methodParams, function (err, result) {
        if (err) {
            console.error('Failed to invoke method \'' + methodName + '\': ' + err.message);
        } else {
            console.log(methodName + ' on ' + deviceId + ':');
            console.log(JSON.stringify(result, null, 2));
        }
    });
    ```
8. Salve e feche o arquivo **CallMethodOnDevice.js**.

## <a name="run-the-apps"></a>Executar os aplicativos
Agora você está pronto para executar os aplicativos.

1. Em um prompt de comando, na pasta **simulateddevice**, execute o seguinte comando para iniciar a escuta de chamadas de método de seu Hub IoT:
   
    ```
    node SimulatedDevice.js
    ```
   
    ![][7]
2. Em um prompt de comando na pasta **callmethodondevice**, execute o seguinte comando para iniciar o monitoramento de seu Hub IoT:
   
    ```
    node CallMethodOnDevice.js 
    ```
   
    ![][8]
3. Você verá o dispositivo reagir ao método imprimindo a mensagem e o aplicativo que chamou o método exibindo a resposta do dispositivo:
   
    ![][9]

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você configurou um novo hub IoT no portal do Azure e depois criou uma identidade do dispositivo no Registro de identidade do Hub IoT. Você usou essa identidade do dispositivo para habilitar o aplicativo do dispositivo simulado para reagir aos métodos invocados pela nuvem. Você também criou um aplicativo que invoca métodos no dispositivo e exibe a resposta do dispositivo. 

Para continuar a introdução ao Hub IoT e explorar outros cenários de IoT, confira:

* [Introdução ao Hub IoT]
* [Agendar trabalhos em vários dispositivos][lnk-devguide-jobs]

Para saber como estender sua solução de IoT e agendar chamadas de método em vários dispositivos, confira o tutorial [Agendar e difundir trabalhos][lnk-tutorial-jobs].

<!-- Images. -->
[7]: ./media/iot-hub-node-node-direct-methods/run-simulated-device.png
[8]: ./media/iot-hub-node-node-direct-methods/run-callmethodondevice.png
[9]: ./media/iot-hub-node-node-direct-methods/methods-output.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-devguide-methods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[Send Cloud-to-Device messages with IoT Hub]: iot-hub-csharp-csharp-c2d.md
[Process Device-to-Cloud messages]: iot-hub-csharp-csharp-process-d2c.md
[Introdução ao Hub IoT]: iot-hub-node-node-getstarted.md
