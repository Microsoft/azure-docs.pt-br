---
title: "Atualização de firmware do dispositivo com o Hub IoT do Azure (Node) | Microsoft Docs"
description: "Como usar o gerenciamento de dispositivos no Hub IoT do Azure para iniciar uma atualização de firmware do dispositivo. Use os SDKs do IoT do Azure para Node.js para implementar um aplicativo de dispositivo simulado e um aplicativo de serviço que dispara a atualização do firmware."
services: iot-hub
documentationcenter: .net
author: juanjperez
manager: timlt
editor: 
ms.assetid: 70b84258-bc9f-43b1-b7cf-de1bb715f2cf
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/07/2017
ms.author: juanpere
ms.openlocfilehash: e169367592b25ea45c3d1017937316a3b3b538b8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-device-management-to-initiate-a-device-firmware-update-nodenode"></a>Usar o gerenciamento de dispositivos para iniciar uma atualização de firmware do dispositivo (Node/Node)
[!INCLUDE [iot-hub-selector-firmware-update](../../includes/iot-hub-selector-firmware-update.md)]

No tutorial [Introdução ao gerenciamento de dispositivo][lnk-dm-getstarted], você viu como usar os primitivos [dispositivo gêmeo][lnk-devtwin] e [métodos diretos][lnk-c2dmethod] para reiniciar remotamente um dispositivo. Este tutorial usa os mesmos primitivos do Hub IoT, fornece orientações e mostra como fazer uma atualização de firmware simulada de ponta a ponta.  Esse padrão é usado na implementação da atualização de firmware para o dispositivo de exemplo Intel Edison.

Este tutorial mostra como:

* Criar um aplicativo de console Node.js que chama o método direto firmwareUpdate no aplicativo de dispositivo simulado por meio do Hub IoT.
* Criar um aplicativo de dispositivo simulado que implementa um método direto **firmwareUpdate**. Esse método inicia um processo de várias etapas que aguarda o download, baixa e aplica a imagem do firmware. Durante cada etapa da atualização, o dispositivo usa as propriedades relatadas para informar sobre o progresso.

Ao fim deste tutorial, você terá dois aplicativos de console do Node.js:

**dmpatterns_fwupdate_service.js**, que chama um método direto no aplicativo do dispositivo simulado, exibe a resposta e periodicamente (a cada 500 ms) exibe as propriedades relatadas atualizadas.

**dmpatterns_fwupdate_device.js**, que se conecta ao seu Hub IoT com a identidade do dispositivo criada anteriormente, recebe um método direto firmwareUpdate, é executado por meio de um processo de vários estados para simular uma atualização de firmware, incluindo: aguardar o download da imagem, baixar a nova imagem e aplicar a imagem.

Para concluir este tutorial, você precisará do seguinte:

* Node.js versão 4.0.x ou posterior, <br/>  [Preparar o ambiente de desenvolvimento][lnk-dev-setup] descreve como instalar o Node.js para este tutorial no Windows ou no Linux.
* Uma conta ativa do Azure. (Se você não tem uma conta, pode criar uma [conta gratuita][lnk-free-trial] em apenas alguns minutos.)

Consulte o artigo [Introdução ao gerenciamento de dispositivo](iot-hub-node-node-device-management-get-started.md) para criar seu hub IoT e obter a cadeia de conexão dele.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>Disparar uma atualização de firmware remoto no dispositivo usando um método direto
Nesta seção, você criará um aplicativo do console Node.js que inicia uma atualização remota de firmware em um dispositivo. O aplicativo utiliza um método direto para iniciar a atualização e usa consultas em dispositivos gêmeos para receber periodicamente o status da atualização do firmware ativo.

1. Crie uma pasta vazia denominada **triggerfwupdateondevice**.  Na pasta **triggerfwupdateondevice**, crie um arquivo package.json usando o comando a seguir no prompt de comando.  Aceite todos os padrões:
   
    ```
    npm init
    ```
2. No prompt de comando, na pasta **triggerfwupdateondevice**, execute o seguinte comando para instalar o pacote **azure-iot-hub**:
   
    ```
    npm install azure-iothub --save
    ```
3. Usando um editor de texto, crie um arquivo **dmpatterns_getstarted_service.js** na pasta **triggerfwupdateondevice**.
4. Adicione as seguintes instruções "require" no início do arquivo **dmpatterns_getstarted_service.js**:
   
    ```
    'use strict';
   
    var Registry = require('azure-iothub').Registry;
    var Client = require('azure-iothub').Client;
    ```
5. Adicione as seguintes declarações de variável e substitua os valores de espaço reservado:
   
    ```
    var connectionString = '{device_connectionstring}';
    var registry = Registry.fromConnectionString(connectionString);
    var client = Client.fromConnectionString(connectionString);
    var deviceToUpdate = 'myDeviceId';
    ```
6. Adicione a seguinte função para localizar e exibir o valor da propriedade relatada firmwareUpdate.
   
    ```
    var queryTwinFWUpdateReported = function() {
        registry.getTwin(deviceToUpdate, function(err, twin){
            if (err) {
              console.error('Could not query twins: ' + err.constructor.name + ': ' + err.message);
            } else {
              console.log((JSON.stringify(twin.properties.reported.iothubDM.firmwareUpdate)) + "\n");
            }
        });
    };
    ```
7. Adicione a seguinte função para invocar o método firmwareUpdate para reiniciar o dispositivo de destino:
   
    ```
    var startFirmwareUpdateDevice = function() {
      var params = {
          fwPackageUri: 'https://secureurl'
      };
   
      var methodName = "firmwareUpdate";
      var payloadData =  JSON.stringify(params);
   
      var methodParams = {
        methodName: methodName,
        payload: payloadData,
        timeoutInSeconds: 30
      };
   
      client.invokeDeviceMethod(deviceToUpdate, methodParams, function(err, result) {
        if (err) {
          console.error('Could not start the firmware update on the device: ' + err.message)
        } 
      });
    };
    ```
8. Por fim, adicione a seguinte função ao código para iniciar a sequência de atualização do firmware e começar a mostrar periodicamente as propriedades relatadas:
   
    ```
    startFirmwareUpdateDevice();
    setInterval(queryTwinFWUpdateReported, 500);
    ```
9. Salve e feche o arquio **dmpatterns_fwupdate_service.js**.

[!INCLUDE [iot-hub-device-firmware-update](../../includes/iot-hub-device-firmware-update.md)]

## <a name="run-the-apps"></a>Executar os aplicativos
Agora você está pronto para executar os aplicativos.

1. No prompt de comando da pasta **manageddevice**, execute o seguinte comando para iniciar a escutar o método direto de reinicialização.
   
    ```
    node dmpatterns_fwupdate_device.js
    ```
2. No prompt de comando da pasta **triggerfwupdateondevice**, execute o seguinte comando para disparar a reinicialização e a consulta remota para o dispositivo gêmeo localizar o tempo de reinicialização mais recente.
   
    ```
    node dmpatterns_fwupdate_service.js
    ```
3. Você verá a resposta do dispositivo para o método direto no console.

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você usou um método direto para disparar uma atualização remota de firmware em um dispositivo e utilizou as propriedades relatadas para acompanhar o andamento da atualização do firmware.

Para saber como estender sua solução de IoT e agendar chamadas de método em vários dispositivos, confira o tutorial [Agendar e difundir trabalhos][lnk-tutorial-jobs].

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-dm-getstarted]: iot-hub-node-node-device-management-get-started.md
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
