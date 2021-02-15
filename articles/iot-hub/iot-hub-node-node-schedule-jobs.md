---
title: Agendar trabalhos com o Hub IoT do Azure (Node) | Microsoft Docs
description: Como agendar um trabalho do Hub IoT do Azure para invocar um método direto em vários dispositivos. Você pode usar os SDKs do IoT do Azure para Node.js para implementar os aplicativos do dispositivo simulado e um aplicativo de serviço para executar o trabalho.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 08/16/2019
ms.custom: mqtt, devx-track-js
ms.openlocfilehash: e1992c806619154fa7b3c33500b2e54fbc919f20
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2020
ms.locfileid: "92151442"
---
# <a name="schedule-and-broadcast-jobs-nodejs"></a>Agendar e difundir trabalhos (Node.js)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

O Hub IoT do Azure é um serviço completamente gerenciado que permite que um aplicativo back-end crie e controle trabalhos que agendam e atualizam milhões de dispositivos.  Os trabalhos podem ser usados para as seguintes ações:

* Atualizar as propriedades desejadas
* Marcas de atualização
* Chamar métodos diretos

Conceitualmente, um trabalho encapsula uma dessas ações e rastreia o progresso da execução com relação a um conjunto de dispositivos, definido por uma consulta do dispositivo gêmeo.  Por exemplo, um aplicativo de back-end pode usar um trabalho para invocar um método de reinicialização em 10.000 dispositivos, especificado por uma consulta do dispositivo gêmeo e agendado no futuro. Esse aplicativo pode acompanhar o progresso à medida que cada um desses dispositivos recebem e executam o método de reinicialização.

Saiba mais sobre cada um desses recursos nestes artigos:

* Dispositivo gêmeo e propriedades: [Introdução aos dispositivos gêmeos](iot-hub-node-node-twin-getstarted.md) e [Tutorial: Como usar as propriedades do dispositivo gêmeo](tutorial-device-twins.md)

* Métodos diretos: [Guia do desenvolvedor do Hub IoT – métodos diretos](iot-hub-devguide-direct-methods.md) e [Tutorial: métodos diretos](quickstart-control-device-node.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Este tutorial mostra como:

* Crie um aplicativo do dispositivo simulado Node.js que tem um método direto que habilita o **lockDoor** que pode ser chamado pelo back-end da solução.

* Crie um aplicativo de console Node.js que chama o método direto **lockDoor** no aplicativo de dispositivo simulado usando um trabalho e atualiza as propriedades desejadas usando um trabalho de dispositivo.

Ao fim deste tutorial, você terá dois aplicativos do Node.js:

* **simDevice.js**, que se conecta ao seu hub IOT com a identidade do dispositivo e recebe um método direto **lockDoor** .

* **scheduleJobService.js**, que chama um método direto no aplicativo do dispositivo simulado e atualiza as propriedades desejadas do dispositivo gêmeo usando um trabalho.

## <a name="prerequisites"></a>Pré-requisitos

* Node.js versão 10.0.x ou posterior. [Preparar o ambiente de desenvolvimento](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md) descreve como instalar o Node.js para este tutorial no Windows ou no Linux.

* Uma conta ativa do Azure. (Se você não tiver uma conta, poderá criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.)

* Verifique se a porta 8883 está aberta no firewall. O exemplo de dispositivo deste artigo usa o protocolo MQTT, que se comunica pela porta 8883. Essa porta poderá ser bloqueada em alguns ambientes de rede corporativos e educacionais. Para obter mais informações e maneiras de resolver esse problema, confira [Como se conectar ao Hub IoT (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Crie um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrar um novo dispositivo no hub IoT

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Criar um aplicativo de dispositivo simulado

Nesta seção, você cria um aplicativo de console do Node.js que responde a um método direto chamado pela nuvem, que aciona um método **lockDoor** simulado.

1. Crie uma nova pasta vazia denominada **simDevice**.  Na pasta **simDevice**, crie um arquivo package.json usando o comando a seguir no seu prompt de comando.  Aceite todos os padrões:

   ```console
   npm init
   ```

2. No prompt de comando, na pasta **simDevice**, execute o seguinte comando para instalar o pacote **azure-iot-device** do SDK do Dispositivo e o pacote **azure-iot-device-mqtt**:

   ```console
   npm install azure-iot-device azure-iot-device-mqtt --save
   ```

3. Usando um editor de texto, crie um novo arquivo **SimDevice.js** na pasta **simDevice**.

4. Adicione as seguintes instruções "require" no início do arquivo **simDevice.js**:

    ```javascript
    'use strict';

    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```

5. Adicione uma variável **connectionString** e use-a para criar um **Cliente** do dispositivo. Substitua o `{yourDeviceConnectionString}` valor do espaço reservado pela cadeia de conexão do dispositivo que você copiou anteriormente.

    ```javascript
    var connectionString = '{yourDeviceConnectionString}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

6. Adicione a seguinte função para manipular o método **lockDoor** .

    ```javascript
    var onLockDoor = function(request, response) {

        // Respond the cloud app for the direct method
        response.send(200, function(err) {
            if (err) {
                console.error('An error occurred when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.');
            }
        });

        console.log('Locking Door!');
    };
    ```

7. Adicione o código a seguir para registrar o manipulador para o método **lockDoor** .

   ```javascript
   client.open(function(err) {
        if (err) {
            console.error('Could not connect to IotHub client.');
        }  else {
            console.log('Client connected to IoT Hub. Register handler for lockDoor direct method.');
            client.onDeviceMethod('lockDoor', onLockDoor);
        }
   });
   ```

8. Salve e feche o arquivo **simDevice.js**.

> [!NOTE]
> Para simplificar, este tutorial não implementa nenhuma política de repetição. No código de produção, implemente políticas de repetição (como uma retirada exponencial), conforme sugerido no artigo [Tratamento de falhas transitórias](/azure/architecture/best-practices/transient-faults).
>

## <a name="get-the-iot-hub-connection-string"></a>Obter a cadeia de conexão do hub IoT

[!INCLUDE [iot-hub-howto-schedule-jobs-shared-access-policy-text](../../includes/iot-hub-howto-schedule-jobs-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

## <a name="schedule-jobs-for-calling-a-direct-method-and-updating-a-device-twins-properties"></a>Agendar trabalhos para chamar um método direto e atualizar as propriedades do dispositivo gêmeo

Nesta seção, você cria um aplicativo de console Node.js que inicia um **lockDoor** remoto em um dispositivo usando um método direto e atualiza as propriedades do dispositivo.

1. Crie uma nova pasta vazia denominada **scheduleJobService**.  Na pasta **scheduleJobService**, crie um arquivo package.json usando o comando a seguir no prompt de comando.  Aceite todos os padrões:

    ```console
    npm init
    ```

2. No prompt de comando, na pasta **scheduleJobService**, execute o seguinte comando para instalar o pacote **azure-iothub** do SDK do Dispositivo e o pacote **azure-iot-device-mqtt**:

    ```console
    npm install azure-iothub uuid --save
    ```

3. Usando um editor de texto, crie um novo arquivo **scheduleJobService.js** na pasta **scheduleJobService**.

4. Adicione as seguintes instruções ' require ' no início do arquivo de **scheduleJobService.js** :

    ```javascript
    'use strict';

    var uuid = require('uuid');
    var JobClient = require('azure-iothub').JobClient;
    ```

5. Adicione as declarações de variável a seguir. Substitua o `{iothubconnectionstring}` valor do espaço reservado pelo valor copiado em [obter a cadeia de conexão do Hub IOT](#get-the-iot-hub-connection-string). Se você registrou um dispositivo diferente de **DeviceID**, certifique-se de alterá-lo na condição de consulta.

    ```javascript
    var connectionString = '{iothubconnectionstring}';
    var queryCondition = "deviceId IN ['myDeviceId']";
    var startTime = new Date();
    var maxExecutionTimeInSeconds =  300;
    var jobClient = JobClient.fromConnectionString(connectionString);
    ```

6. Adicione a seguinte função que é usada para monitorar a execução do trabalho:

    ```javascript
    function monitorJob (jobId, callback) {
        var jobMonitorInterval = setInterval(function() {
            jobClient.getJob(jobId, function(err, result) {
            if (err) {
                console.error('Could not get job status: ' + err.message);
            } else {
                console.log('Job: ' + jobId + ' - status: ' + result.status);
                if (result.status === 'completed' || result.status === 'failed' || result.status === 'cancelled') {
                clearInterval(jobMonitorInterval);
                callback(null, result);
                }
            }
            });
        }, 5000);
    }
    ```

7. Adicione o seguinte código para agendar o trabalho que chama o método de dispositivo:
  
    ```javascript
    var methodParams = {
        methodName: 'lockDoor',
        payload: null,
        responseTimeoutInSeconds: 15 // Timeout after 15 seconds if device is unable to process method
    };

    var methodJobId = uuid.v4();
    console.log('scheduling Device Method job with id: ' + methodJobId);
    jobClient.scheduleDeviceMethod(methodJobId,
                                queryCondition,
                                methodParams,
                                startTime,
                                maxExecutionTimeInSeconds,
                                function(err) {
        if (err) {
            console.error('Could not schedule device method job: ' + err.message);
        } else {
            monitorJob(methodJobId, function(err, result) {
                if (err) {
                    console.error('Could not monitor device method job: ' + err.message);
                } else {
                    console.log(JSON.stringify(result, null, 2));
                }
            });
        }
    });
    ```

8. Adicione o seguinte código para agendar o trabalho para atualizar o dispositivo gêmeo:

    ```javascript
    var twinPatch = {
       etag: '*',
       properties: {
           desired: {
               building: '43',
               floor: 3
           }
       }
    };

    var twinJobId = uuid.v4();

    console.log('scheduling Twin Update job with id: ' + twinJobId);
    jobClient.scheduleTwinUpdate(twinJobId,
                                queryCondition,
                                twinPatch,
                                startTime,
                                maxExecutionTimeInSeconds,
                                function(err) {
        if (err) {
            console.error('Could not schedule twin update job: ' + err.message);
        } else {
            monitorJob(twinJobId, function(err, result) {
                if (err) {
                    console.error('Could not monitor twin update job: ' + err.message);
                } else {
                    console.log(JSON.stringify(result, null, 2));
                }
            });
        }
    });
    ```

9. Salve e feche o arquivo **scheduleJobService.js**.

## <a name="run-the-applications"></a>Executar os aplicativos

Agora você está pronto para executar os aplicativos.

1. No prompt de comando, na pasta **simDevice**, execute o seguinte comando para começar a escutar o método direto de reinicialização.

    ```console
    node simDevice.js
    ```

2. No prompt de comando da pasta **scheduleJobService**, execute o seguinte comando para disparar os trabalhos para bloquear a porta e atualizar o twin

    ```console
    node scheduleJobService.js
    ```

3. Você vê a resposta do dispositivo para o método direto e o status do trabalho no console do.

   O seguinte mostra a resposta do dispositivo para o método direto:

   ![Saída do aplicativo de dispositivo simulado](./media/iot-hub-node-node-schedule-jobs/sim-device.png)

   O seguinte mostra os trabalhos de agendamento de serviço para o método direto e a atualização do dispositivo e os trabalhos em execução para a conclusão:

   ![Executar um aplicativo de dispositivo simulado](./media/iot-hub-node-node-schedule-jobs/schedule-job-service.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você usou um trabalho para agendar um método direto para um dispositivo e a atualização das propriedades do twin do dispositivo.

Para continuar a introdução aos padrões do Hub IoT e do gerenciamento de dispositivos, como remoto pela atualização do firmware do ar, consulte [tutorial: como fazer uma atualização de firmware](tutorial-firmware-update.md).

Para continuar a introdução ao Hub IoT, consulte [Introdução ao Azure IoT Edge](../iot-edge/quickstart-linux.md).