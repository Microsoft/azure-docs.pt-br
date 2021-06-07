---
title: 'Início Rápido: Controlar um dispositivo no Azure IoT (Node.js)'
description: Neste início rápido, você executa dois aplicativos de exemplo Node.js. Um deles é um aplicativo back-end que pode controlar remotamente os dispositivos conectados ao seu hub. O outro aplicativo simula um dispositivo conectado ao seu hub que pode ser controlado remotamente.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom:
- mvc
- seo-javascript-september2019
- seo-javascript-october2019
- mqtt
- 'Role: Cloud Development'
- devx-track-js
- devx-track-azurecli
ms.date: 06/21/2019
ms.openlocfilehash: 4854daca6985f016c84d2525aebe4fa6b6c82c6a
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106059765"
---
# <a name="quickstart-use-nodejs-to-control-a-device-connected-to-an-azure-iot-hub"></a>Início Rápido: use o Node.js para controlar um dispositivo conectado a um hub IoT do Azure

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

Neste início rápido, você usa um método direto para controlar um dispositivo simulado conectado ao Hub IoT do Azure. O Hub IoT é um serviço do Azure que habilita a ingestão de grandes volumes de telemetria de dispositivos na nuvem e o gerenciamento de seus dispositivos IoT pela nuvem para armazenamento e processamento. Você pode usar métodos diretos para alterar remotamente o comportamento de um dispositivo conectado ao seu hub IoT. Este guia de início rápido usa dois aplicativos Node.js: um aplicativo de dispositivo simulado que responde a métodos diretos chamados de um aplicativo de back-end e um aplicativo de back-end que chama os métodos diretos no dispositivo simulado.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta do Azure com uma assinatura ativa. [Crie um gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* [Node.js 10+](https://nodejs.org).

    Você pode verificar a versão atual do Node.js no computador de desenvolvimento usando o seguinte comando:

    ```cmd/sh
    node --version
    ```

* [Um projeto de exemplo do Node.js](https://github.com/Azure-Samples/azure-iot-samples-node/archive/master.zip).

* Porta 8883 aberta no firewall. A amostra de dispositivo deste início rápido usa o protocolo MQTT, que se comunica pela porta 8883. Essa porta poderá ser bloqueada em alguns ambientes de rede corporativos e educacionais. Para obter mais informações e maneiras de resolver esse problema, confira [Como se conectar ao Hub IoT (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Crie um hub IoT

Se você tiver concluído o [Início Rápido: Enviar telemetria de um dispositivo para um Hub IoT](quickstart-send-telemetry-node.md) anterior, ignore esta etapa.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrar um dispositivo

Se você tiver concluído o [Início Rápido: Enviar telemetria de um dispositivo para um Hub IoT](quickstart-send-telemetry-node.md) anterior, ignore esta etapa.

Um dispositivo deve ser registrado no hub IoT antes de poder se conectar. Neste início rápido, você usa o Azure Cloud Shell para registrar um dispositivo simulado.

1. Execute o comando a seguir no Azure Cloud Shell para criar a identidade do dispositivo.

   **YourIoTHubName**: substitua o espaço reservado abaixo pelo nome escolhido para o hub IoT.

   **MyNodeDevice**: esse é o nome do dispositivo que está sendo registrado. É recomendável usar **MyNodeDevice** conforme mostrado. Se escolher um nome diferente para seu dispositivo, você também precisará usar esse nome ao longo deste artigo, bem como atualizar o nome do dispositivo nos aplicativos de exemplo antes de executá-los.

    ```azurecli-interactive
    az iot hub device-identity create \
      --hub-name {YourIoTHubName} --device-id MyNodeDevice
    ```

2. Execute os seguintes comandos no Azure Cloud Shell para obter a _cadeia de conexão de dispositivo_ referente ao dispositivo que você acabou de registrar:

    **YourIoTHubName**: substitua o espaço reservado abaixo pelo nome escolhido para o hub IoT.

    ```azurecli-interactive
    az iot hub device-identity connection-string show \
      --hub-name {YourIoTHubName} \
      --device-id MyNodeDevice \
      --output table
    ```

    Tome nota da cadeia de conexão do dispositivo, que se parece com:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Você usará esse valor posteriormente no início rápido.

3. Você também precisa de uma _cadeia de conexão de serviço_ para permitir que aplicativos de back-end se conectem ao seu hub IoT e recuperem mensagens. O comando abaixo recupera a cadeia de conexão de serviço para o hub IoT:

    **YourIoTHubName**: substitua o espaço reservado abaixo pelo nome escolhido para o hub IoT.

    ```azurecli-interactive
    az iot hub connection-string show \
      --policy-name service --name {YourIoTHubName} --output table

    ```

    Tome nota da cadeia de conexão de serviço, que se parece com:

   `HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}`

    Você usará esse valor posteriormente no início rápido. A cadeia de conexão do serviço é diferente da cadeia de conexão do dispositivo que você anotou na etapa anterior.

## <a name="listen-for-direct-method-calls"></a>Escutar chamadas de método direto

O aplicativo de dispositivo simulado se conecta a um ponto de extremidade específico do dispositivo em seu hub IoT, envia telemetria simulada e escuta chamadas de método direto de seu hub. Neste início rápido, a chamada de método direto do hub informa ao dispositivo para alterar o intervalo de envio da telemetria. O dispositivo simulado envia uma confirmação para o hub depois de executar o método direto.

1. Em uma janela de terminal local, navegue até a pasta raiz do projeto Node.js de exemplo. Em seguida, navegue até a pasta **iot-hub\Quickstarts\simulated-device-2**.

2. Abra o arquivo **SimulatedDevice.js** em seu editor de texto preferido.

    Substitua o valor da variável `connectionString` pela cadeia de conexão do dispositivo que você anotou anteriormente. Salve suas alterações em **SimulatedDevice.js**.

3. Na janela do terminal local, execute os seguintes comandos para instalar as bibliotecas necessárias e executar o aplicativo de dispositivo simulado:

    ```cmd/sh
    npm install
    node SimulatedDevice.js
    ```

    A captura de tela a seguir mostra o resultado à medida que o aplicativo de dispositivo simulado envia telemetria para o seu hub IoT:

    ![Executar o dispositivo simulado](./media/quickstart-control-device-node/simulated-device-telemetry-iot-hub.png)

## <a name="call-the-direct-method"></a>Chamar o método direto

O aplicativo de back-end se conecta a um ponto de extremidade do lado do serviço em seu Hub IoT. O aplicativo faz chamadas de método direto para um dispositivo por meio do hub IoT e escuta as confirmações. Um aplicativo de back-end do Hub IoT normalmente é executado na nuvem.

1. Em outra janela de terminal local, navegue até a pasta raiz do projeto Node.js de exemplo. Em seguida, navegue até a pasta **iot-hub\Quickstarts\back-end-application**.

2. Abra o arquivo **BackEndApplication.js** em seu editor de texto preferido.

    Substitua o valor da variável `connectionString` pela cadeia de conexão do serviço que você anotou anteriormente. Salve suas alterações em **BackEndApplication.js**.

3. Na janela do terminal local, execute os seguintes comandos para instalar as bibliotecas necessárias e executar o aplicativo de back-end:

    ```cmd/sh
    npm install
    node BackEndApplication.js
    ```

    A seguinte captura de tela mostra a saída enquanto o aplicativo faz uma chamada de método direto ao dispositivo e recebe uma confirmação:

    ![Saída quando o aplicativo faz uma chamada de método direto para o dispositivo](./media/quickstart-control-device-node/direct-method-device-call.png)

    Após executar o aplicativo de back-end, você verá uma mensagem na janela do console com o dispositivo simulado em execução, e a taxa de mudança de envio das mensagens:

    ![Saída quando há uma alteração no cliente simulado](./media/quickstart-control-device-node/simulated-device-message-change.png)

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você já chamou um método direto em um dispositivo de um aplicativo de back-end e respondeu à chamada de método direto em um aplicativo de dispositivo simulado.

Para saber como rotear mensagens de dispositivo para nuvem para destinos diferentes na nuvem, continue n próximo tutorial.

> [!div class="nextstepaction"]
> [Tutorial: Encaminhar a telemetria para pontos de extremidade diferentes para processamento](tutorial-routing.md)
