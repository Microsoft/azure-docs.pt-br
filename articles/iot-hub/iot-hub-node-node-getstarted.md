---
title: "Introdução ao Hub IoT do Azure | Microsoft Docs"
description: "Saiba como enviar mensagens de dispositivo para nuvem para o Hub IoT do Azure SDKs da IoT para Node.js. Crie um dispositivo simulado e aplicativos de serviço para registrar seu dispositivo, envie mensagens e leia mensagens no hub IoT."
services: iot-hub
documentationcenter: nodejs
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 56618522-9a31-42c6-94bf-55e2233b39ac
ms.service: iot-hub
ms.devlang: javascript
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/31/2017
ms.author: dobett
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f3398e38cf7d3d28d9ca4edef5a9bca96aeaf2ab
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="connect-your-simulated-device-to-your-iot-hub-using-node"></a>Conectar o dispositivo simulado ao hub IoT usando o Node

[!INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

No fim deste tutorial, você terá três aplicativos de console do Node.js:

* **CreateDeviceIdentity.js**, que cria uma identidade do dispositivo e uma chave de segurança associada para conectar o aplicativo de dispositivo simulado.
* **ReadDeviceToCloudMessages.js**, que exibe a telemetria enviada pelo aplicativo de dispositivo simulado.
* **SimulatedDevice.js**, que se conecta ao Hub IoT com a identidade do dispositivo criada anteriormente e envia uma mensagem de telemetria a cada segundo usando o protocolo MQTT.

> [!NOTE]
> O artigo [SDKs de IoT do Azure][lnk-hub-sdks] fornece informações sobre os SDKs de IoT do Azure que você pode usar para criar aplicativos executados em dispositivos e no back-end da solução.

Para concluir este tutorial, você precisará do seguinte:

* Node.js versão 4.0.x ou posterior.
* Uma conta ativa do Azure. (Se você não tem uma conta, pode criar uma [conta gratuita][lnk-free-trial] em apenas alguns minutos.)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

Você criou seu Hub IoT. Você tem o nome de host do Hub IoT e a cadeia de conexão do Hub IoT de que precisa para concluir o restante deste tutorial.

## <a name="create-a-device-identity"></a>Criar uma identidade do dispositivo

Nesta seção, você cria um aplicativo de console do Node.js que cria uma identidade do dispositivo no registro de identidade em seu hub IoT. Um dispositivo só pode se conectar ao hub IoT se tiver uma entrada no registro de identidade. Para obter mais informações, consulte a seção **"Registro de identidade"** do [Guia do Desenvolvedor do Hub IoT][lnk-devguide-identity]. Execute esse aplicativo para gerar a ID de dispositivo exclusiva e a chave usada pelo dispositivo para se identificar ao enviar mensagens de dispositivo para nuvem.

1. Crie uma nova pasta vazia denominada `createdeviceidentity`. Na pasta `createdeviceidentity`, crie um arquivo package.json usando o comando a seguir no seu prompt de comando. Aceite todos os padrões:

    ```cmd/sh
    npm init
    ```

2. No prompt de comando na pasta `createdeviceidentity`, execute o seguinte comando para instalar o pacote do SDK de Serviço `azure-iothub`:

    ```cmd/sh
    npm install azure-iothub --save
    ```

3. Usando um editor de texto, crie um arquivo **CreateDeviceIdentity.js** na pasta `createdeviceidentity`.

4. Adicione a seguinte instrução `require` no início do arquivo **CreateDeviceIdentity.js** :

    ```nodejs
    'use strict';

    var iothub = require('azure-iothub');
    ```

5. Adicione o código a seguir ao arquivo **CreateDeviceIdentity.js**. Substitua o valor do espaço reservado pela cadeia de conexão do Hub IoT criado na seção anterior:

    ```nodejs
    var connectionString = '{iothub connection string}';

    var registry = iothub.Registry.fromConnectionString(connectionString);
    ```

6. Adicione o código a seguir para criar uma definição do dispositivo no Registro de identidade no hub IoT. Esse código criará um dispositivo se a id do dispositivo não existir no registro de identidade. Caso contrário, retornará a chave do dispositivo existente:

    ```nodejs
    var device = {
      deviceId: 'myFirstNodeDevice'
    }
    registry.create(device, function(err, deviceInfo, res) {
      if (err) {
        registry.get(device.deviceId, printDeviceInfo);
      }
      if (deviceInfo) {
        printDeviceInfo(err, deviceInfo, res)
      }
    });

    function printDeviceInfo(err, deviceInfo, res) {
      if (deviceInfo) {
        console.log('Device ID: ' + deviceInfo.deviceId);
        console.log('Device key: ' + deviceInfo.authentication.symmetricKey.primaryKey);
      }
    }
    ```

   [!INCLUDE [iot-hub-pii-note-naming-device](../../includes/iot-hub-pii-note-naming-device.md)]

7. Salve e feche o arquivo **CreateDeviceIdentity.js** .

8. Para executar o aplicativo `createdeviceidentity`, execute o seguinte comando no prompt de comando na pasta `createdeviceidentity`:

    ```cmd/sh
    node CreateDeviceIdentity.js 
    ```

9. Anote a **Id do dispositivo** e a **Chave do dispositivo**. Você precisará desses valores mais tarde quando criar um aplicativo que conecta o Hub IoT como um dispositivo.

> [!NOTE]
> O Registro de identidade do Hub IoT armazena apenas as identidades de dispositivo para habilitar o acesso seguro ao Hub IoT. Ele armazena as IDs e as chaves do dispositivo a usar como credenciais de segurança e um sinalizador habilitado/desabilitado que você poderá usar para desabilitar o acesso de um dispositivo individual. Se seu aplicativo precisar armazenar outros metadados específicos do dispositivo, ele deverá usar um repositório específico do aplicativo. Para saber mais, confira o [Guia de Desenvolvedor do Hub IoT][lnk-devguide-identity].

<a id="D2C_node"></a>
## <a name="receive-device-to-cloud-messages"></a>Receber mensagens do dispositivo para a nuvem

Nesta seção, você cria um aplicativo de console do Node.js que lê as mensagens do dispositivo para a nuvem a partir do Hub IoT. Um Hub IoT expõe um ponto de extremidade compatível com os [Hubs de Eventos][lnk-event-hubs-overview] para permitir que você leia mensagens do dispositivo para a nuvem. Para simplificar, este tutorial cria um leitor básico que não é adequado para uma implantação de alta taxa de transferência. O tutorial [Processar mensagens do dispositivo para a nuvem][lnk-process-d2c-tutorial] mostra como processar as mensagens do dispositivo para a nuvem em escala. O tutorial [Introdução aos Hubs de Eventos][lnk-eventhubs-tutorial] oferece informações adicionais aplicáveis aos pontos de extremidade compatíveis com o Hub de Eventos do Hub IoT.

> [!NOTE]
> O ponto de extremidade compatível com o Hub de Eventos para ler mensagens do dispositivo para a nuvem sempre usa o protocolo AMQP.

1. Crie uma pasta vazia chamada `readdevicetocloudmessages`. Na pasta `readdevicetocloudmessages`, crie um arquivo package.json usando o comando a seguir no seu prompt de comando. Aceite todos os padrões:

    ```cmd/sh
    npm init
    ```

2. No prompt de comando na pasta `readdevicetocloudmessages`, execute o seguinte comando para instalar o pacote **azure-event-hubs**:

    ```cmd/sh
    npm install azure-event-hubs --save
    ```

3. Usando um editor de texto, crie um arquivo **ReadDeviceToCloudMessages.js** na pasta `readdevicetocloudmessages`.

4. Adicione as seguintes instruções `require` no início do arquivo **ReadDeviceToCloudMessages.js** :

    ```nodejs
    'use strict';

    var EventHubClient = require('azure-event-hubs').Client;
    ```

5. Adicione a seguinte declaração de variável e substitua o valor de espaço reservado pela cadeia de conexão do Hub IoT para o seu hub:

    ```nodejs
    var connectionString = '{iothub connection string}';
    ```

6. Adicione as duas seguintes funções que imprimem a saída no console:

    ```nodejs
    var printError = function (err) {
      console.log(err.message);
    };

    var printMessage = function (message) {
      console.log('Message received: ');
      console.log(JSON.stringify(message.body));
      console.log('');
    };
    ```

7. Adicione o seguinte código para criar o **EventHubClient**, abra a conexão com o Hub IoT e crie um receptor para cada partição. Esse aplicativo usa um filtro ao criar um receptor para que o receptor leia apenas as mensagens enviadas para o Hub IoT após o início da execução do receptor. Esse filtro é útil em um ambiente de teste para ver apenas o conjunto atual de mensagens. Em um ambiente de produção, o código deve poder processar todas as mensagens. Para obter mais informações, confira o tutorial [Como processar as mensagens entre o dispositivo e a nuvem do Hub IoT][lnk-process-d2c-tutorial]:

    ```nodejs
    var client = EventHubClient.fromConnectionString(connectionString);
    client.open()
        .then(client.getPartitionIds.bind(client))
        .then(function (partitionIds) {
            return partitionIds.map(function (partitionId) {
                return client.createReceiver('$Default', partitionId, { 'startAfterTime' : Date.now()}).then(function(receiver) {
                    console.log('Created partition receiver: ' + partitionId)
                    receiver.on('errorReceived', printError);
                    receiver.on('message', printMessage);
                });
            });
        })
        .catch(printError);
    ```

8. Salve e feche o arquivo **ReadDeviceToCloudMessages.js** .

## <a name="create-a-simulated-device-app"></a>Criar um aplicativo de dispositivo simulado

Nesta seção, você cria um aplicativo de console do Node.js que simula um dispositivo que envia mensagens do dispositivo para a nuvem para um hub IoT.

1. Crie uma pasta vazia chamada `simulateddevice`. Na pasta `simulateddevice`, crie um arquivo package.json usando o comando a seguir no seu prompt de comando. Aceite todos os padrões:

    ```cmd/sh
    npm init
    ```

2. No prompt de comando, na pasta `simulateddevice`, execute o seguinte comando para instalar o pacote **azure-iot-device** do SDK do Dispositivo e o pacote **azure-iot-device-mqtt**:

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

3. Usando um editor de texto, crie um arquivo **SimulatedDevice.js** na pasta `simulateddevice`.

4. Adicione as seguintes instruções `require` no início do arquivo **SimulatedDevice.js** :

    ```nodejs
    'use strict';

    var clientFromConnectionString = require('azure-iot-device-mqtt').clientFromConnectionString;
    var Message = require('azure-iot-device').Message;
    ```

5. Adicione uma variável `connectionString` e use-a para criar uma instância de **cliente**. Substitua `{youriothostname}` pelo nome do Hub IoT criado na seção *Criar um Hub IoT*. Substitua `{yourdevicekey}` pelo valor da chave do dispositivo gerado na seção *Criar uma identidade de dispositivo*:

    ```nodejs
    var connectionString = 'HostName={youriothostname};DeviceId=myFirstNodeDevice;SharedAccessKey={yourdevicekey}';

    var client = clientFromConnectionString(connectionString);
    ```

6. Adicione a seguinte função para exibir a saída do aplicativo:

    ```nodejs
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
      };
    }
    ```

7. Crie um retorno de chamada e use a função **setInterval** para enviar uma mensagem ao hub IoT a cada segundo:

    ```nodejs
    var connectCallback = function (err) {
      if (err) {
        console.log('Could not connect: ' + err);
      } else {
        console.log('Client connected');

        // Create a message and send it to the IoT Hub every second
        setInterval(function(){
            var temperature = 20 + (Math.random() * 15);
            var humidity = 60 + (Math.random() * 20);            
            var data = JSON.stringify({ deviceId: 'myFirstNodeDevice', temperature: temperature, humidity: humidity });
            var message = new Message(data);
            message.properties.add('temperatureAlert', (temperature > 30) ? 'true' : 'false');
            console.log("Sending message: " + message.getData());
            client.sendEvent(message, printResultFor('send'));
        }, 1000);
      }
    };
    ```

8. Abra a conexão com o Hub IoT e comece a enviar mensagens:

    ```nodejs
    client.open(connectCallback);
    ```

9. Salve e feche o arquivo **SimulatedDevice.js** .

> [!NOTE]
> Para simplificar, este tutorial não implementa nenhuma política de repetição. No código de produção, implemente políticas de repetição (como uma retirada exponencial), como sugerido no artigo [Tratamento de falhas transitórias][lnk-transient-faults] do MSDN.

## <a name="run-the-apps"></a>Executar os aplicativos

Agora você está pronto para executar os aplicativos.

1. Em um prompt de comando na pasta `readdevicetocloudmessages`, execute o seguinte comando para iniciar o monitoramento de seu Hub IoT:

    ```cmd/sh
    node ReadDeviceToCloudMessages.js 
    ```

    ![Aplicativo de serviço do Hub IoT do Node.js para monitorar mensagens do dispositivo para a nuvem][7]

2. Em um prompt de comando na pasta `simulateddevice`, execute o seguinte comando para iniciar o envio de dados de telemetria para o Hub IoT:

    ```cmd/sh
    node SimulatedDevice.js
    ```

    ![Aplicativo de dispositivo do Hub IoT do Node.js para enviar mensagens do dispositivo para a nuvem][8]

3. O bloco **Uso** no [portal do Azure][lnk-portal] mostra o número de mensagens enviadas para o Hub IoT:

    ![Bloco de Uso do Portal do Azure mostrando o número de mensagens enviadas ao Hub IoT][43]

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você configurou um novo hub IoT no portal do Azure e depois criou uma identidade do dispositivo no Registro de identidade do Hub IoT. Você usou essa identidade do dispositivo para habilitar o aplicativo de dispositivo simulado para enviar as mensagens entre o dispositivo e a nuvem para o Hub IoT. Você também criou um aplicativo que exibe as mensagens recebidas pelo Hub IoT.

Para continuar a introdução ao Hub IoT e explorar outros cenários de IoT, confira:

* [Conectando o dispositivo][lnk-connect-device]
* [Introdução ao gerenciamento de dispositivo][lnk-device-management]
* [Introdução ao Azure IoT Edge][lnk-iot-edge]

Para saber como estender sua solução IoT e processar as mensagens entre o dispositivo e a nuvem em escala, consulte o tutorial [Processar as mensagens entre o dispositivo e a nuvem][lnk-process-d2c-tutorial].
[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

<!-- Images. -->
[7]: ./media/iot-hub-node-node-getstarted/runapp1.png
[8]: ./media/iot-hub-node-node-getstarted/runapp2.png
[43]: ./media/iot-hub-csharp-csharp-getstarted/usage.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide-identity-registry.md
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iot-edge]: iot-hub-linux-iot-edge-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
