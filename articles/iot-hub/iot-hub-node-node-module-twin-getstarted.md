---
title: Introdução ao módulo gêmeo e à identidade do módulo do Hub IoT (Node.js) | Microsoft Docs
description: Saiba como criar a identidade do módulo e atualizar o módulo gêmeo usando SDKs de IoT para Node.js.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: node
ms.topic: conceptual
ms.date: 04/26/2018
ms.openlocfilehash: 4d11455914a285fa9557ae34565940f600db23e4
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68404154"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-using-nodejs-back-end-and-nodejs-device"></a>Introdução à identidade do módulo e ao módulo gêmeo do Hub IoT usando back-end Node.js e dispositivo Node.js

> [!NOTE]
> [As identidades do módulo e os módulos gêmeos](iot-hub-devguide-module-twins.md) são semelhantes à identidade do dispositivo e ao dispositivo gêmeo do Hub IoT do Azure, mas fornecem melhor granularidade. Enquanto a identidade do dispositivo e o dispositivo gêmeo do Hub IoT do Azure permitem que o aplicativo de back-end configure um dispositivo e forneça visibilidade às condições do dispositivo, uma identidade de módulo e módulo gêmeo fornecem esses recursos para componentes individuais de um dispositivo. Em dispositivos habilitados com vários componentes, como dispositivos baseados em sistema operacional ou dispositivos de firmware, permite condições e configuração isolada para cada componente.

Ao fim deste tutorial, você terá dois aplicativos do Node.js:

* **CreateIdentities**, que cria uma identidade de dispositivo, uma identidade de módulo e uma chave de segurança associada para conectar seus clientes de dispositivo e módulo.

* **UpdateModuleTwinReportedProperties**, que envia propriedades relatadas atualizadas de módulo gêmeo ao Hub IoT.

> [!NOTE]
> Para obter informações sobre os SDKs de IoT do Azure que você pode usar para criar aplicativos executados em dispositivos e no back-end da solução, veja [SDKs de IoT do Azure](iot-hub-devguide-sdks.md).

Para concluir este tutorial, você precisará do seguinte:

* Uma conta ativa do Azure. (Se você não tiver uma conta, poderá criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.)

* Instale o último [SDK do Node.js](https://github.com/Azure/azure-iot-sdk-node).

## <a name="create-an-iot-hub"></a>Crie um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="get-the-iot-hub-connection-string"></a>Obter a cadeia de conexão do Hub IoT

[!INCLUDE [iot-hub-howto-module-twin-shared-access-policy-text](../../includes/iot-hub-howto-module-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

## <a name="create-a-device-identity-and-a-module-identity-in-iot-hub"></a>Criar uma identidade do dispositivo e uma identidade do módulo no Hub IoT

Nesta seção, você criará um aplicativo Node.js que cria uma identidade do dispositivo e uma identidade do módulo no registro de identidade no Hub IoT. Um dispositivo ou módulo não pode se conectar ao Hub IoT, a menos que ele tenha uma entrada no Registro de identidade. Para obter mais informações, consulte a seção "registro de identidade" do [Guia do desenvolvedor do Hub IOT](iot-hub-devguide-identity-registry.md). Quando você executa esse aplicativo de console, ele gera ID e chave exclusivas para o dispositivo e o módulo. O dispositivo e o módulo usam esses valores para se identificar ao enviar mensagens de dispositivo para nuvem para o Hub IoT. As IDs diferenciam minúsculas e maiúsculas.

1. Crie um diretório para manter o código.

2. Dentro desse diretório, primeiro execute  **npm init -y**  para criar um package.json vazio com os padrões. Esse é o arquivo de projeto do código.

3. Execute **NPM install-S Azure-iothub\@modules-Preview** para instalar o SDK do serviço dentro do subdiretório **node_modules** .

    > [!NOTE]
    > O nome do subdiretório node_modules usa o módulo word para significar "uma biblioteca de nós". Este termo não está relacionado com módulos do Hub IoT.

4. Crie o seguinte arquivo .js no diretório. Chame-o de **add.js**. Copie e cole a cadeia de conexão do hub e o nome do hub.

    ```javascript
    var Registry = require('azure-iothub').Registry;
    var uuid = require('uuid');
    // Copy/paste your connection string and hub name here
    var serviceConnectionString = '<hub connection string from portal>';
    var hubName = '<hub name>.azure-devices.net';
    // Create an instance of the IoTHub registry
    var registry = Registry.fromConnectionString(serviceConnectionString);
    // Insert your device ID and moduleId here.
    var deviceId = 'myFirstDevice';
    var moduleId = 'myFirstModule';
    // Create your device as a SAS authentication device
    var primaryKey = new Buffer(uuid.v4()).toString('base64');
    var secondaryKey = new Buffer(uuid.v4()).toString('base64');
    var deviceDescription = {
      deviceId: deviceId,
      status: 'enabled',
      authentication: {
        type: 'sas',
        symmetricKey: {
          primaryKey: primaryKey,
          secondaryKey: secondaryKey
        }
      }
    };

    // First, create a device identity
    registry.create(deviceDescription, function(err) {
      if (err) {
        console.log('Error creating device identity: ' + err);
        process.exit(1);
      }
      console.log('device connection string = "HostName=' + hubName + ';DeviceId=' + deviceId + ';SharedAccessKey=' + primaryKey + '"');

      // Then add a module to that device
      registry.addModule({ deviceId: deviceId, moduleId: moduleId }, function(err) {
        if (err) {
          console.log('Error creating module identity: ' + err);
          process.exit(1);
        }

        // Finally, retrieve the module details from the hub so we can construct the connection string
        registry.getModule(deviceId, moduleId, function(err, foundModule) {
          if (err) {
            console.log('Error getting module back from hub: ' + err);
            process.exit(1);
          }
          console.log('module connection string = "HostName=' + hubName + ';DeviceId=' + foundModule.deviceId + ';ModuleId='+foundModule.moduleId+';SharedAccessKey=' + foundModule.authentication.symmetricKey.primaryKey + '"');
          process.exit(0);
        });
      });
    });

    ```

Esse aplicativo cria uma identidade do dispositivo com a ID **myFirstDevice** e uma identidade do módulo com a ID **myFirstModule** no dispositivo **myFirstDevice**. (se essa ID de módulo já existir no registro de identidade, o código simplesmente irá recuperar as informações do módulo existentes.) Em seguida, o aplicativo exibe a chave primária dessa identidade. Você usa essa chave no aplicativo de módulo simulado para se conectar ao Hub IoT.

Execute isso usando o nó add.js. Ele fornecerá uma cadeia de conexão para a identidade do dispositivo e outra para a identidade do módulo.

> [!NOTE]
> O Registro de identidade do Hub IoT armazena apenas as identidades de dispositivo e módulo para habilitar o acesso seguro ao Hub IoT. O registro de identidade armazena IDs de dispositivo e chaves para usar como credenciais de segurança. O registro de identidade também armazena um sinalizador de habilitado/desabilitado para cada dispositivo que você pode usar para desabilitar o acesso ao dispositivo. Se seu aplicativo precisar armazenar outros metadados específicos do dispositivo, ele deverá usar um repositório específico do aplicativo. Não há nenhum sinalizador habilitado/desabilitado para as identidades do módulo. Para saber mais, confira [Guia de Desenvolvedor do Hub IoT](iot-hub-devguide-identity-registry.md).

## <a name="update-the-module-twin-using-nodejs-device-sdk"></a>Atualizar o módulo gêmeo usando o SDK do dispositivo Node.js

Nesta seção, você criará um aplicativo Node.js no dispositivo simulado que atualiza as propriedades relatadas do módulo gêmeo.

1. **Obtenha a cadeia de conexão do módulo** --entre no [portal do Azure](https://portal.azure.com/). Navegue até seu Hub IoT e clique em Dispositivos IoT. Localize myFirstDevice e abra-o. Você verá que myFirstModule foi criado com êxito. Copie a cadeia de conexão do módulo. Ela será necessária na próxima etapa.

   ![Detalhes do módulo do Portal do Azure](./media/iot-hub-node-node-module-twin-getstarted/module-detail.png)

2. Da mesma forma que você fez na etapa anterior, crie um diretório para o código do dispositivo e use NPM para inicializá-lo e instalar o SDK do dispositivo (**NPM install-S Azure-IOT\@-Device-AMQP modules-Preview**).

   > [!NOTE]
   > O comando npm install pode parecer lento. Seja paciente, pois está baixando muitos códigos do repositório de pacotes.

   > [!NOTE]
   > Se você visualizar um erro informando npm ERR! erro de registro de análise de json, é seguro ignorar. Se você visualizar um erro informando npm ERR! erro de registro de análise de json, é seguro ignorar.

3. Crie um arquivo chamado twin.js. Copie e cole a cadeia de caracteres de identidade do módulo.

    ```javascript
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-amqp').Amqp;
    // Copy/paste your module connection string here.
    var connectionString = '<insert module connection string here>';
    // Create a client using the Amqp protocol.
    var client = Client.fromConnectionString(connectionString, Protocol);
    client.on('error', function (err) {
      console.error(err.message);
    });
    // connect to the hub
    client.open(function(err) {
      if (err) {
        console.error('error connecting to hub: ' + err);
        process.exit(1);
      }
      console.log('client opened');
    // Create device Twin
      client.getTwin(function(err, twin) {
        if (err) {
          console.error('error getting twin: ' + err);
          process.exit(1);
        }
        // Output the current properties
        console.log('twin contents:');
        console.log(twin.properties);
        // Add a handler for desired property changes
        twin.on('properties.desired', function(delta) {
            console.log('new desired properties received:');
            console.log(JSON.stringify(delta));
        });
        // create a patch to send to the hub
        var patch = {
          updateTime: new Date().toString(),
          firmwareVersion:'1.2.1',
          weather:{
            temperature: 72,
            humidity: 17
          }
        };
        // send the patch
        twin.properties.reported.update(patch, function(err) {
          if (err) throw err;
          console.log('twin state reported');
        });
      });
    });
    ```

4. Agora, execute isso usando o comando **node twin.js**.

    ```
    F:\temp\module_twin>node twin.js
    client opened
    twin contents:
    { reported: { update: [Function: update], '$version': 1 },
      desired: { '$version': 1 } }
    new desired properties received:
    {"$version":1}
    twin state reported
    ```

## <a name="next-steps"></a>Próximas etapas

Para continuar a introdução ao Hub IoT e explorar outros cenários de IoT, confira:

* [Introdução ao gerenciamento de dispositivo](iot-hub-node-node-device-management-get-started.md)

* [Introdução ao IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)