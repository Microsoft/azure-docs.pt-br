---
title: Introdução aos dispositivos gêmeos do Hub IoT do Azure (Node) | Microsoft Docs
description: Como usar dispositivos gêmeos do Hub IoT do Azure para adicionar marcas e usar uma consulta do Hub IoT. Use os SDKs do IoT do Azure para Node.js para implementar um aplicativo de dispositivo simulado e um aplicativo de serviço que adiciona as marcas e executa a consulta do Hub IoT.
author: fsautomata
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 08/25/2017
ms.author: elioda
ms.openlocfilehash: f93abac563d47f6505f42d29e882698ef31174bf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61444701"
---
# <a name="get-started-with-device-twins-node"></a>Introdução aos dispositivos gêmeos (Node)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

No fim deste tutorial, você terá dois aplicativos de console do Node.js:

* **AddTagsAndQuery.js**, um aplicativo de back-end Node.js que adiciona marcas e consultas aos dispositivos gêmeos.

* **TwinSimulatedDevice.js**, um aplicativo Node.js que simula um dispositivo que se conecta ao seu Hub IoT com a identidade do dispositivo criada anteriormente e reporta sua condição de conectividade.

> [!NOTE]
> O artigo [SDKs de IoT do Azure](iot-hub-devguide-sdks.md) apresenta informações sobre os SDKs de IoT do Azure que você pode usar para criar dispositivos e aplicativos de back-end.
>

Para concluir este tutorial, você precisará do seguinte:

* Node.js versão 4.0.x ou posterior.

* Uma conta ativa do Azure. (Se você não tiver uma conta, poderá criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.)

## <a name="create-an-iot-hub"></a>Crie um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>Recuperar a cadeia conexão para o hub IoT

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-the-service-app"></a>Criar o aplicativo do serviço

Nesta seção, você cria um aplicativo de console do Node.js que adiciona metadados de local ao dispositivo gêmeo associado a **myDeviceId**. Em seguida, ele consulta os dispositivos gêmeos armazenados no hub IoT selecionando os dispositivos localizados nos EUA e, depois, aqueles que relatam uma conexão celular.

1. Crie uma nova pasta vazia denominada **addtagsandqueryapp**. Na pasta **addtagsandqueryapp**, crie um novo arquivo package.json usando o comando a seguir no prompt de comando. Aceite todos os padrões:

    ```
    npm init
    ```

2. No prompt de comando, na pasta **addtagsandqueryapp**, execute o seguinte comando para instalar o pacote **azure-iothub**:
   
    ```
    npm install azure-iothub --save
    ```

3. Usando um editor de texto, crie um novo arquivo **AddTagsAndQuery.js** na pasta **addtagsandqueryapp**.

4. Adicione o seguinte código ao arquivo **AddTagsAndQuery.js** e substitua o espaço reservado **{iot hub connection string}** pela cadeia de conexão do Hub IoT que você copiou quando criou seu hub:

   ``` javascript
        'use strict';
        var iothub = require('azure-iothub');
        var connectionString = '{iot hub connection string}';
        var registry = iothub.Registry.fromConnectionString(connectionString);
   
        registry.getTwin('myDeviceId', function(err, twin){
            if (err) {
                console.error(err.constructor.name + ': ' + err.message);
            } else {
                var patch = {
                    tags: {
                        location: {
                            region: 'US',
                            plant: 'Redmond43'
                      }
                    }
                };
   
                twin.update(patch, function(err) {
                  if (err) {
                    console.error('Could not update twin: ' + err.constructor.name + ': ' + err.message);
                  } else {
                    console.log(twin.deviceId + ' twin updated successfully');
                    queryTwins();
                  }
                });
            }
        });
   ```

    O objeto **Registry** expõe todos os métodos necessários para interagir com gêmeos de dispositivo do serviço. O código anterior inicializa primeiro o objeto **Registry**, depois, recupera o dispositivo gêmeo para **myDeviceId** e finalmente atualiza as marcas com as informações do local desejado.

    Depois de atualizar as marcas, ele chama a função **queryTwins**.

5. Adicione o seguinte código ao final do **AddTagsAndQuery.js** para implementar a função **queryTwins**:

   ```javascript
        var queryTwins = function() {
            var query = registry.createQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'", 100);
            query.nextAsTwin(function(err, results) {
                if (err) {
                    console.error('Failed to fetch the results: ' + err.message);
                } else {
                    console.log("Devices in Redmond43: " + results.map(function(twin) {return twin.deviceId}).join(','));
                }
            });
   
            query = registry.createQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity.type = 'cellular'", 100);
            query.nextAsTwin(function(err, results) {
                if (err) {
                    console.error('Failed to fetch the results: ' + err.message);
                } else {
                    console.log("Devices in Redmond43 using cellular network: " + results.map(function(twin) {return twin.deviceId}).join(','));
                }
            });
        };
   ```

    O código anterior executa duas consultas: a primeira seleciona somente os dispositivos gêmeos de dispositivos localizados na fábrica de **Redmond43**, e o segundo aperfeiçoa a consulta para selecionar somente os dispositivos que também estão conectados por meio de rede celular.

    O código anterior, quando ele cria o objeto **query**, especifica um número máximo de documentos retornados. O objeto **query** contém uma propriedade booliana **hasMoreResults** que você pode usar para invocar os métodos **nextAsTwin** várias vezes para recuperar todos os resultados. Um método chamado **next** está disponível para os resultados que não são de dispositivos gêmeos, por exemplo, resultados de consultas de agregação.

6. Execute o aplicativo com:

    ```
        node AddTagsAndQuery.js
    ```

   Você deve ver um dispositivo nos resultados da consulta que pergunta sobre todos os dispositivos localizados em **Redmond43**, e nenhum para a consulta que restringe os resultados para dispositivos que usam uma rede de celular.
   
    ![Ver um dispositivo nos resultados da consulta](media/iot-hub-node-node-twin-getstarted/service1.png)

Na seção seguinte, você cria um aplicativo de dispositivo que reporta as informações de conectividade e altera o resultado da consulta na seção anterior.

## <a name="create-the-device-app"></a>Criar o aplicativo do dispositivo

Nesta seção, você cria um aplicativo de console do Node.js que se conecta ao seu hub como **myDeviceId**, e depois atualiza seu as propriedades reportadas de seu dispositivo gêmeo a fim de conter as informações indicando que ele está conectado usando uma rede de celular.

1. Crie uma nova pasta vazia denominada **reportconnectivity**. Na pasta **reportconnectivity**, crie um novo arquivo package.json usando o comando a seguir no prompt de comando. Aceite todos os padrões:
   
    ```
    npm init
    ```

2. No prompt de comando, na pasta **reportconnectivity**, execute o seguinte comando para instalar os pacotes **azure-iot-device** e **azure-iot-device-mqtt**:
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

3. Usando um editor de texto, crie um novo arquivo **ReportConnectivity.js** na pasta **reportconnectivity**.

4. Adicione o seguinte código ao arquivo **ReportConnectivity.js** e substitua o espaço reservado **{cadeia de conexão do dispositivo}** pela cadeia de conexão do dispositivo copiada quando você criou a identidade do dispositivo **myDeviceId**:

    ```
        'use strict';
        var Client = require('azure-iot-device').Client;
        var Protocol = require('azure-iot-device-mqtt').Mqtt;
   
        var connectionString = '{device connection string}';
        var client = Client.fromConnectionString(connectionString, Protocol);
   
        client.open(function(err) {
        if (err) {
            console.error('could not open IotHub client');
        }  else {
            console.log('client opened');
   
            client.getTwin(function(err, twin) {
            if (err) {
                console.error('could not get twin');
            } else {
                var patch = {
                    connectivity: {
                        type: 'cellular'
                    }
                };
   
                twin.properties.reported.update(patch, function(err) {
                    if (err) {
                        console.error('could not update twin');
                    } else {
                        console.log('twin state reported');
                        process.exit();
                    }
                });
            }
            });
        }
        });
    ```

    O objeto **Client** expõe todos os métodos necessários para você interagir com dispositivos gêmeos a partir do dispositivo. O código anterior, depois de inicializar o objeto **Client**, recupera o dispositivo gêmeo para **myDeviceId** e atualiza suas propriedades reportadas com as informações de conectividade.

5. Executar o aplicativo do dispositivo

    ```   
        node ReportConnectivity.js
    ```

    Você deve ver a mensagem `twin state reported`.

6. Agora que o dispositivo divulgou suas informações de conectividade, ele deve aparecer em ambas as consultas. Acesse novamente a pasta **addtagsandqueryapp** e execute as consultas novamente:

    ```   
        node AddTagsAndQuery.js
    ```

    Desta vez, **myDeviceId** deve aparecer em ambos os resultados da consulta.

    ![Mostrar myDeviceId em ambos os resultados da consulta](media/iot-hub-node-node-twin-getstarted/service2.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você configurou um novo hub IoT no portal do Azure e depois criou uma identidade do dispositivo no Registro de identidade do Hub IoT. Você adicionou os metadados do dispositivo como marcações de um aplicativo de back-end e criou um aplicativo de dispositivo simulado para relatar informações de conectividade no dispositivo gêmeo. Você também aprendeu a consultar essas informações usando a linguagem de consulta do Hub IoT semelhante ao SQL.

Veja os recursos a seguir para saber como:

* Enviar telemetria de dispositivos com o [Introdução ao IoT Hub](quickstart-send-telemetry-node.md) tutorial,

* configurar dispositivos usando as propriedades desejadas do dispositivo gêmeo com o tutorial [Usar propriedades desejadas para configurar dispositivos](tutorial-device-twins.md),

* Controlar dispositivos interativamente (como ativar uma ventoinha de um aplicativo controlado pelo usuário), com o [usar métodos diretos](quickstart-control-device-node.md) tutorial.