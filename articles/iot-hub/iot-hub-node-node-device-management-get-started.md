---
title: Introdução ao gerenciamento de dispositivo do Hub IoT do Azure (Node) | Microsoft Docs
description: Como usar o gerenciamento de dispositivos do Hub IoT para iniciar uma reinicialização do dispositivo remoto. Use o SDK do IoT do Azure para Node.js para implementar um aplicativo de dispositivo simulado que inclui um método direto e um aplicativo de serviço que invoca o método direto.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/25/2017
ms.openlocfilehash: 868df2c8d1e14000f743686dcb6d4174d851be86
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68403252"
---
# <a name="get-started-with-device-management-node"></a>Introdução ao gerenciamento de dispositivos (Node)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

Este tutorial mostra como:

* Use o [portal do Azure](https://portal.azure.com) para criar um hub IOT e criar uma identidade de dispositivo no Hub IOT.

* Crie um aplicativo de dispositivo simulado contendo um método direto que reinicia o dispositivo. Métodos diretos são invocados da nuvem.

* Criar um aplicativo de console Node.js que chama um método direto de reinicialização no aplicativo de dispositivo simulado por meio do Hub IoT.

Ao fim deste tutorial, você terá dois aplicativos de console do Node.js:

* **dmpatterns_getstarted_device.js**, que conecta seu hub IoT com a identidade do dispositivo criada anteriormente, recebe um método direto de reinicialização, simula uma reinicialização física e informa a hora da última reinicialização.

* **dmpatterns_getstarted_service.js**, que chama um método direto no aplicativo do dispositivo simulado, exibe a resposta e exibe as propriedades relatadas atualizadas.

Para concluir este tutorial, você precisará do seguinte:

* Node. js versão 10.0. x ou posterior. [Preparar seu ambiente de desenvolvimento](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md) descreve como instalar o Node. js para este tutorial no Windows ou no Linux.

* Uma conta ativa do Azure. (Se você não tiver uma conta, poderá criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.)

## <a name="create-an-iot-hub"></a>Crie um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Criar um aplicativo de dispositivo simulado

Nesta seção, você executará as seguintes etapas:

* Criar um aplicativo de console do Node.js que responde a um método direto chamado pela nuvem

* Disparar uma reinicialização do dispositivo simulado

* Usar as propriedades relatadas para habilitar consultas de dispositivo gêmeo para identificar dispositivos e a última reinicialização

1. Crie uma pasta vazia denominada **manageddevice**.  Na pasta **manageddevice**, crie um arquivo package.json usando o comando a seguir no prompt de comando.  Aceite todos os padrões:
      
    ```
    npm init
    ```

2. No prompt de comando na pasta **manageddevice**, execute o seguinte comando para instalar o pacote **azure-iot-device** do SDK do Dispositivo e o pacote **azure-iot-device-mqtt**:
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

3. Usando um editor de texto, crie um arquivo **dmpatterns_getstarted_device.js** na pasta **manageddevice**.

4. Adicione as seguintes instruções ‘require’ no início do arquivo **dmpatterns_getstarted_device.js**:
   
    ```
    'use strict';
   
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```

5. Adicione uma variável **connectionString** e use-a para criar um **Cliente** do dispositivo.  Substitua a cadeia de conexão de dispositivo pela cadeia de conexão do seu dispositivo.  
   
    ```
    var connectionString = 'HostName={youriothostname};DeviceId=myDeviceId;SharedAccessKey={yourdevicekey}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

6. Adicione a seguinte função para implementar o método direto no dispositivo
   
    ```
    var onReboot = function(request, response) {
   
        // Respond the cloud app for the direct method
        response.send(200, 'Reboot started', function(err) {
            if (err) {
                console.error('An error occurred when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.');
            }
        });
   
        // Report the reboot before the physical restart
        var date = new Date();
        var patch = {
            iothubDM : {
                reboot : {
                    lastReboot : date.toISOString(),
                }
            }
        };
   
        // Get device Twin
        client.getTwin(function(err, twin) {
            if (err) {
                console.error('could not get twin');
            } else {
                console.log('twin acquired');
                twin.properties.reported.update(patch, function(err) {
                    if (err) throw err;
                    console.log('Device reboot twin state reported')
                });  
            }
        });
   
        // Add your device's reboot API for physical restart.
        console.log('Rebooting!');
    };
    ```

7. Abra a conexão com o Hub IoT e inicie o ouvinte do método direto:

   
    ```
    client.open(function(err) {
        if (err) {
            console.error('Could not open IotHub client');
        }  else {
            console.log('Client opened.  Waiting for reboot method.');
            client.onDeviceMethod('reboot', onReboot);
        }
    });
    ```

8. Salve e feche o arquivo **dmpatterns_getstarted_device.js**.

> [!NOTE]
> Para simplificar, este tutorial não implementa nenhuma política de repetição. No código de produção, implemente políticas de repetição (como uma retirada exponencial), conforme sugerido no artigo [Tratamento de falhas transitórias](/azure/architecture/best-practices/transient-faults).

## <a name="get-the-iot-hub-connection-string"></a>Obter a cadeia de conexão do Hub IoT

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Disparar uma reinicialização remota no dispositivo usando um método direto

Nesta seção, você criará um aplicativo do console Node.js que inicia uma reinicialização remota em um dispositivo usando um método direto. O aplicativo usa consultas de dispositivo gêmeo para descobrir o último horário de reinicialização para esse dispositivo.

1. Crie uma pasta vazia denominada **triggerrebootondevice**. Na pasta **triggerrebootondevice**, crie um arquivo package.json usando o comando a seguir no prompt de comando. Aceite todos os padrões:
   
    ```
    npm init
    ```

2. No prompt de comando, na pasta **triggerrebootondevice**, execute o seguinte comando para instalar o pacote **azure-iothub** do SDK do Dispositivo e o pacote **azure-iot-device-mqtt**:
   
    ```
    npm install azure-iothub --save
    ```

3. Usando um editor de texto, crie um arquivo **dmpatterns_getstarted_service.js** na pasta **triggerrebootondevice**.

4. Adicione as seguintes instruções "require" no início do arquivo **dmpatterns_getstarted_service.js**:

  
    ```
    'use strict';
   
    var Registry = require('azure-iothub').Registry;
    var Client = require('azure-iothub').Client;
    ```

5. Adicione as seguintes declarações de variável e substitua os valores de espaço reservado:

   
    ```
    var connectionString = '{iothubconnectionstring}';
    var registry = Registry.fromConnectionString(connectionString);
    var client = Client.fromConnectionString(connectionString);
    var deviceToReboot = 'myDeviceId';
    ```

6. Adicione a seguinte função para invocar o método do dispositivo para reiniciar o dispositivo de destino:
   
    ```
    var startRebootDevice = function(twin) {
   
        var methodName = "reboot";
   
        var methodParams = {
            methodName: methodName,
            payload: null,
            timeoutInSeconds: 30
        };
   
        client.invokeDeviceMethod(deviceToReboot, methodParams, function(err, result) {
            if (err) {
                console.error("Direct method error: "+err.message);
            } else {
                console.log("Successfully invoked the device to reboot.");  
            }
        });
    };
    ```

7. Adicione a seguinte função para consultar o dispositivo e obter a hora da última reinicialização:
   
    ```
    var queryTwinLastReboot = function() {
   
        registry.getTwin(deviceToReboot, function(err, twin){
   
            if (twin.properties.reported.iothubDM != null)
            {
                if (err) {
                    console.error('Could not query twins: ' + err.constructor.name + ': ' + err.message);
                } else {
                    var lastRebootTime = twin.properties.reported.iothubDM.reboot.lastReboot;
                    console.log('Last reboot time: ' + JSON.stringify(lastRebootTime, null, 2));
                }
            } else 
                console.log('Waiting for device to report last reboot time.');
        });
    };
    ```

8. Adicione o seguinte código para chamar as funções que dispararão o método direto de reinicialização e consultarão a hora da última reinicialização:

   
    ```
    startRebootDevice();
    setInterval(queryTwinLastReboot, 2000);
    ```

9. Salve e feche o arquivo **dmpatterns_getstarted_service.js**.

## <a name="run-the-apps"></a>Executar os aplicativos

Agora você está pronto para executar os aplicativos.

1. No prompt de comando da pasta **manageddevice**, execute o seguinte comando para iniciar a escutar o método direto de reinicialização.

   
    ```
    node dmpatterns_getstarted_device.js
    ```

2. No prompt de comando da pasta **triggerrebootondevice**, execute o seguinte comando para disparar a reinicialização e a consulta remota para o dispositivo gêmeo localizar o tempo de reinicialização mais recente.

   
    ```
    node dmpatterns_getstarted_service.js
    ```

3. Você verá a resposta do dispositivo para o método direto no console.

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]