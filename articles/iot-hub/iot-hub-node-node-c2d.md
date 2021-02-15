---
title: Mensagens da nuvem para o dispositivo com o Hub IoT do Azure (Nó)| Microsoft Docs
description: Como enviar mensagens da nuvem para o dispositivo para um dispositivo de um Hub IoT do Azure usando os SDKs do IoT do Azure para Node.js. Modifique um aplicativo de dispositivo simulado para receber mensagens da nuvem para o dispositivo e modificar um aplicativo de back-end para enviá-las.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: javascript
ms.topic: conceptual
ms.date: 06/16/2017
ms.custom:
- amqp
- mqtt
- devx-track-js
ms.openlocfilehash: e398138f12c38e5235a0004679d9574dbde607db
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91446885"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-nodejs"></a>Enviar mensagens de nuvem para dispositivo com o Hub IoT (Node.js)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

O Hub IoT do Azure é um serviço totalmente gerenciado que ajuda a permitir comunicações bidirecionais confiáveis e seguras entre milhões de dispositivos e um back-end de solução. O início rápido [Enviar telemetria de um dispositivo para um hub IoT](quickstart-send-telemetry-node.md) mostra como criar um hub IoT, como provisionar uma identidade do dispositivo nele e como codificar um aplicativo de dispositivo simulado que envia mensagens de dispositivo para nuvem.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Este tutorial se baseia no [Enviar telemetria de um dispositivo para um hub IoT](quickstart-send-telemetry-node.md). Ele mostra como:

* Da sua solução de back-end, envie mensagens da nuvem para o dispositivo em um único dispositivo por meio do Hub IoT.
* Receber mensagens da nuvem para o dispositivo em um dispositivo.
* Do back-end da sua solução, solicite confirmação de entrega (*comentários*) para as mensagens enviadas a um dispositivo do Hub IoT.

Encontre mais informações sobre mensagens de nuvem para dispositivo no [guia do desenvolvedor do Hub IoT](iot-hub-devguide-messaging.md).

Ao fim deste tutorial, você executará dois aplicativos de console do Node.js:

* **SimulatedDevice**, uma versão modificada do aplicativo criado em [Enviar telemetria de um dispositivo para um hub IoT](quickstart-send-telemetry-node.md), que se conecta ao hub IoT e recebe mensagens de nuvem para dispositivo.

* **SendCloudToDeviceMessage**, que envia uma mensagem de nuvem para dispositivo ao aplicativo de dispositivo simulado por meio do Hub IoT e recebe a confirmação de entrega.

> [!NOTE]
> O Hub IoT tem suporte a SDK para várias plataformas de dispositivo e linguagens (incluindo C, Java, Python e Javascript) por meio dos SDKs do dispositivo IoT do Azure. Para obter instruções passo a passo sobre como conectar seu dispositivo ao código deste tutorial e, em geral, ao Hub IoT do Azure, veja o [Centro de Desenvolvedores do IoT do Azure](https://azure.microsoft.com/develop/iot).
>

## <a name="prerequisites"></a>Pré-requisitos

* Node.js versão 10.0.x ou posterior. [Preparar o ambiente de desenvolvimento](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md) descreve como instalar o Node.js para este tutorial no Windows ou no Linux.

* Uma conta ativa do Azure. (Se você não tiver uma conta, poderá criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial) em apenas alguns minutos.)

* Verifique se a porta 8883 está aberta no firewall. O exemplo de dispositivo deste artigo usa o protocolo MQTT, que se comunica pela porta 8883. Essa porta poderá ser bloqueada em alguns ambientes de rede corporativos e educacionais. Para obter mais informações e maneiras de resolver esse problema, confira [Como se conectar ao Hub IoT (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="receive-messages-in-the-simulated-device-app"></a>Receber mensagens no aplicativo do dispositivo simulado

Nesta seção, você modificará o aplicativo de dispositivo simulado criado em [Enviar telemetria de um dispositivo para um hub IoT](quickstart-send-telemetry-node.md) para receber mensagens de nuvem para dispositivo, provenientes do hub IoT.

1. Usando um editor de texto, abra o arquivo **SimulatedDevice.js**. Esse arquivo está localizado na pasta **iot-hub\Quickstarts\simulated-device** da pasta raiz do código de exemplo do Node.js que você baixou no início rápido [Enviar telemetria de um dispositivo para um hub IoT](quickstart-send-telemetry-node.md).

2. Registre um manipulador com o cliente do dispositivo para receber mensagens enviadas do Hub IoT. Adicione a chamada para `client.on` logo após a linha que cria o cliente do dispositivo, como no snippet a seguir:

    ```javascript
    var client = DeviceClient.fromConnectionString(connectionString, Mqtt);

    client.on('message', function (msg) {
      console.log('Id: ' + msg.messageId + ' Body: ' + msg.data);
      client.complete(msg, function (err) {
        if (err) {
          console.error('complete error: ' + err.toString());
        } else {
          console.log('complete sent');
        }
      });
    });
    ```

Neste exemplo, o dispositivo chama a função **Complete** para notificar o Hub IOT de que ele processou a mensagem e que pode ser removido com segurança da fila do dispositivo. A chamada à função **complete** não será necessária se você estiver usando o transporte MQTT e puder ser omitida. Ele é necessário para AMQP e HTTPS.

Com AMQP e HTTPS, mas não MQTT, o dispositivo também pode:

* Abandone uma mensagem, que resulta no Hub IoT reter a mensagem na fila do dispositivo para consumo futuro.
* Rejeite uma mensagem, que remove permanentemente a mensagem da fila do dispositivo.

Se algo acontecer que impede que o dispositivo conclua, abandone ou rejeite a mensagem, o Hub IoT irá, após um período de tempo limite fixo, enfileirar a mensagem para entrega novamente. Por esse motivo, a lógica de processamento de mensagens no aplicativo do dispositivo deve ser *idempotente*, de modo que o recebimento da mesma mensagem várias vezes produz o mesmo resultado.

Para obter informações mais detalhadas sobre como o Hub IoT processa mensagens da nuvem para o dispositivo, incluindo detalhes do ciclo de vida da mensagem da nuvem para o dispositivo, consulte [enviar mensagens da nuvem para o dispositivo de um hub IOT](iot-hub-devguide-messages-c2d.md).
  
> [!NOTE]
> Se você usar HTTPS em vez de MQTT ou AMQP como o transporte, a instância **DeviceClient** verificará mensagens do Hub IOT com pouca frequência (no mínimo a cada 25 minutos). Para obter mais informações sobre as diferenças entre o suporte a MQTT, AMQP e HTTPS, consulte [diretrizes de comunicação da nuvem para o dispositivo](iot-hub-devguide-c2d-guidance.md) e [escolha um protocolo de comunicação](iot-hub-devguide-protocols.md).
>

## <a name="get-the-iot-hub-connection-string"></a>Obter a cadeia de conexão do hub IoT

Neste artigo, você cria um serviço de back-end para enviar mensagens de nuvem para dispositivo por meio do Hub IoT criado no [Enviar telemetria de um dispositivo para um hub IoT](quickstart-send-telemetry-node.md). Para enviar mensagens de nuvem para dispositivo, o serviço precisa da permissão **conexão de serviço**. Por padrão, todo Hub IoT é criado com uma política de acesso compartilhado chamada **serviço** que concede essa permissão.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Envie uma mensagem da nuvem para o dispositivo

Nesta seção, você criará um aplicativo do console do Node.js que envia mensagens da nuvem ao dispositivo para o aplicativo do dispositivo simulado. Você precisa da ID do dispositivo que você adicionou no início rápido [Enviar telemetria de um dispositivo para um hub IoT](quickstart-send-telemetry-node.md). Você também precisa da cadeia de conexão do hub IoT que você copiou anteriormente em [Obter a cadeia de conexão do hub IoT](#get-the-iot-hub-connection-string).

1. Crie uma pasta vazia denominada **sendcloudtodevicemessage**. Na pasta **sendcloudtodevicemessage**, crie um arquivo package.json usando o comando a seguir no prompt de comando. Aceite todos os padrões:

    ```shell
    npm init
    ```

2. No prompt de comando, na pasta **sendcloudtodevicemessage**, execute o seguinte comando para instalar o pacote **azure-iothub**:

    ```shell
    npm install azure-iothub --save
    ```

3. Usando um editor de texto, crie um arquivo **SendCloudToDeviceMessage.js** na pasta **sendcloudtodevicemessage**.

4. Adicione as seguintes instruções `require` no início do arquivo **SendCloudToDeviceMessage.js** :

    ```javascript
    'use strict';

    var Client = require('azure-iothub').Client;
    var Message = require('azure-iot-common').Message;
    ```

5. Adicione o seguinte código ao arquivo **SendCloudToDeviceMessage.js** . Substitua os valores de espaço reservado "{iot hub connection string}" e "{device id}" pela cadeia de conexão do hub IoT e pela ID do dispositivo anotadas anteriormente:

    ```javascript
    var connectionString = '{iot hub connection string}';
    var targetDevice = '{device id}';

    var serviceClient = Client.fromConnectionString(connectionString);
    ```

6. Adicione a seguinte função para imprimir os resultados da operação no console:

    ```javascript
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
      };
    }
    ```

7. Adicione a seguinte função para imprimir mensagens de comentários de entrega para o console:

    ```javascript
    function receiveFeedback(err, receiver){
      receiver.on('message', function (msg) {
        console.log('Feedback message:')
        console.log(msg.getData().toString('utf-8'));
      });
    }
    ```

8. Adicione o seguinte código para enviar uma mensagem ao dispositivo e lidar com a mensagem de comentário quando o dispositivo reconhecer a mensagem da nuvem para o dispositivo:

    ```javascript
    serviceClient.open(function (err) {
      if (err) {
        console.error('Could not connect: ' + err.message);
      } else {
        console.log('Service client connected');
        serviceClient.getFeedbackReceiver(receiveFeedback);
        var message = new Message('Cloud to device message.');
        message.ack = 'full';
        message.messageId = "My Message ID";
        console.log('Sending message: ' + message.getData());
        serviceClient.send(targetDevice, message, printResultFor('send'));
      }
    });
    ```

9. Salve e feche o arquivo **SendCloudToDeviceMessage.js** .

## <a name="run-the-applications"></a>Executar os aplicativos

Agora você está pronto para executar os aplicativos.

1. No prompt de comando na pasta **simulated-device**, execute o seguinte comando para enviar telemetria ao Hub IoT e escutar mensagens de nuvem para dispositivo:

    ```shell
    node SimulatedDevice.js
    ```

    ![Executar um aplicativo de dispositivo simulado](./media/iot-hub-node-node-c2d/receivec2d.png)

2. No prompt de comando na pasta **sendcloudtodevicemessage**, execute o comando a seguir para enviar uma mensagem da nuvem para o dispositivo e esperar os comentários de confirmação:

    ```shell
    node SendCloudToDeviceMessage.js
    ```

    ![Execute o aplicativo para enviar o comando da nuvem para o dispositivo](./media/iot-hub-node-node-c2d/sendc2d.png)

   > [!NOTE]
   > Para simplificar, este tutorial não implementa nenhuma política de repetição. No código de produção, implemente políticas de repetição (como uma retirada exponencial), conforme sugerido no artigo [Tratamento de falhas transitórias](/azure/architecture/best-practices/transient-faults).
   >

## <a name="next-steps"></a>Próximas etapas

Neste tutorial você aprendeu a enviar e receber mensagens da nuvem para o dispositivo.

Para ver exemplos de soluções completas que usam o Hub IoT, consulte [Acelerador de solução de Monitoramento Remoto do Azure IoT](https://azure.microsoft.com/documentation/suites/iot-suite/).

Para saber mais sobre como desenvolver soluções com o Hub IoT, consulte o [Guia do desenvolvedor do Hub IoT](iot-hub-devguide.md).
