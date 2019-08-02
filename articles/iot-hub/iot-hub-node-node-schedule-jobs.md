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
ms.date: 10/06/2017
ms.openlocfilehash: a3a07f54d77577085951b2bccfa1213adef5b20d
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68403922"
---
# <a name="schedule-and-broadcast-jobs-node"></a>Agendar e transmitir trabalhos (Node)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

O Hub IoT do Azure é um serviço completamente gerenciado que permite que um aplicativo back-end crie e controle trabalhos que agendam e atualizam milhões de dispositivos.  Os trabalhos podem ser usados para as seguintes ações:

* Atualizar as propriedades desejadas
* Marcas de atualização
* Chamar métodos diretos

Conceitualmente, um trabalho encapsula uma dessas ações e rastreia o progresso da execução com relação a um conjunto de dispositivos, definido por uma consulta do dispositivo gêmeo.  Por exemplo, um aplicativo de back-end pode usar um trabalho para invocar um método de reinicialização em 10.000 dispositivos, especificado por uma consulta do dispositivo gêmeo e agendado no futuro. Esse aplicativo pode acompanhar o progresso à medida que cada um desses dispositivos recebem e executam o método de reinicialização.

Saiba mais sobre cada um desses recursos nestes artigos:

* Dispositivo gêmeo e propriedades: [Introdução ao dispositivo gêmeos](iot-hub-node-node-twin-getstarted.md) e [tutorial: Como usar as propriedades de dispositivo.](tutorial-device-twins.md)

* Métodos diretos: [Guia do desenvolvedor do Hub IOT – métodos diretos](iot-hub-devguide-direct-methods.md) e [tutorial: métodos diretos](quickstart-control-device-node.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Este tutorial mostra como:

* Crie um aplicativo do dispositivo simulado Node.js que tem um método direto que habilita o **lockDoor** que pode ser chamado pelo back-end da solução.

* Crie um aplicativo de console Node.js que chama o método direto **lockDoor** no aplicativo de dispositivo simulado usando um trabalho e atualiza as propriedades desejadas usando um trabalho de dispositivo.

Ao fim deste tutorial, você terá dois aplicativos do Node.js:

* **simDevice.js**, que se conecta ao seu hub IoT com a identidade do dispositivo e recebe um método direto **lockDoor**.

* **scheduleJobService.js**, que chama um método direto no aplicativo do dispositivo simulado e atualiza as propriedades desejadas do dispositivo gêmeo usando um trabalho.

Para concluir este tutorial, você precisará do seguinte:

* O Node. js versão 10.0. x ou posterior [Prepare seu ambiente de desenvolvimento](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md) descreve como instalar o Node. js para este tutorial no Windows ou no Linux.

* Uma conta ativa do Azure. (Se você não tiver uma conta, poderá criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.)

## <a name="create-an-iot-hub"></a>Crie um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Criar um aplicativo de dispositivo simulado

Nesta seção, você cria um aplicativo de console do Node.js que responde a um método direto chamado pela nuvem, que aciona um método **lockDoor** simulado.

1. Crie uma nova pasta vazia denominada **simDevice**.  Na pasta **simDevice**, crie um arquivo package.json usando o comando a seguir no seu prompt de comando.  Aceite todos os padrões:

   ```
   npm init
   ```

2. No prompt de comando, na pasta **simDevice**, execute o seguinte comando para instalar o pacote **azure-iot-device** do SDK do Dispositivo e o pacote **azure-iot-device-mqtt**:
   
   ```
   npm install azure-iot-device azure-iot-device-mqtt --save
   ```

3. Usando um editor de texto, crie um novo arquivo **SimDevice.js** na pasta **simDevice**.

4. Adicione as seguintes instruções "require" no início do arquivo **simDevice.js**:
   
    ```
    'use strict';
   
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```

5. Adicione uma variável **connectionString** e use-a para criar um **Cliente** do dispositivo.  
   
    ```
    var connectionString = 'HostName={youriothostname};DeviceId={yourdeviceid};SharedAccessKey={yourdevicekey}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

6. Adicione a seguinte função para manipular o método **lockDoor**.
   
    ```
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

7. Adicione o seguinte código para registrar o manipulador do método **lockDoor**.

   ```
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

## <a name="get-the-iot-hub-connection-string"></a>Obter a cadeia de conexão do Hub IoT

[!INCLUDE [iot-hub-howto-schedule-jobs-shared-access-policy-text](../../includes/iot-hub-howto-schedule-jobs-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

## <a name="schedule-jobs-for-calling-a-direct-method-and-updating-a-device-twins-properties"></a>Agendar trabalhos para chamar um método direto e atualizar as propriedades do dispositivo gêmeo

Nesta seção, é possível criar um aplicativo de console do Node.js que inicia um **lockDoor** remoto em um dispositivo usando um método direto e atualizar as propriedades do dispositivo gêmeo.

1. Crie uma nova pasta vazia denominada **scheduleJobService**.  Na pasta **scheduleJobService**, crie um arquivo package.json usando o comando a seguir no prompt de comando.  Aceite todos os padrões:

    ```
    npm init
    ```

2. No prompt de comando, na pasta **scheduleJobService**, execute o seguinte comando para instalar o pacote **azure-iothub** do SDK do Dispositivo e o pacote **azure-iot-device-mqtt**:
   
    ```
    npm install azure-iothub uuid --save
    ```

3. Usando um editor de texto, crie um novo arquivo **scheduleJobService.js** na pasta **scheduleJobService**.

4. Adicione as seguintes instruções "require" no início do arquivo **dmpatterns_gscheduleJobServiceetstarted_service.js**:
   
    ```
    'use strict';
   
    var uuid = require('uuid');
    var JobClient = require('azure-iothub').JobClient;
    ```

5. Adicione as seguintes declarações de variável e substitua os valores de espaço reservado:
   
    ```
    var connectionString = '{iothubconnectionstring}';
    var queryCondition = "deviceId IN ['myDeviceId']";
    var startTime = new Date();
    var maxExecutionTimeInSeconds =  300;
    var jobClient = JobClient.fromConnectionString(connectionString);
    ```

6. Adicione a seguinte função que é usada para monitorar a execução do trabalho:
   
    ```
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
   
    ```
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
   
    ```
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
   
    ```
    node simDevice.js
    ```

2. No prompt de comando da pasta **scheduleJobService**, execute o seguinte comando para disparar os trabalhos para bloquear a porta e atualizar o twin
   
    ```
    node scheduleJobService.js
    ```

3. Você verá a resposta do dispositivo para o método direto no console.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você usou um trabalho para agendar um método direto para um dispositivo e a atualização das propriedades do twin do dispositivo.

Para continuar a introdução aos padrões do Hub IOT e do gerenciamento de dispositivos, como remoto pela atualização do firmware [do ar, consulte o tutorial: Como fazer uma atualização](tutorial-firmware-update.md)de firmware.

Para continuar a introdução ao Hub IoT, consulte [introdução ao Azure IOT Edge](../iot-edge/tutorial-simulate-device-linux.md).
