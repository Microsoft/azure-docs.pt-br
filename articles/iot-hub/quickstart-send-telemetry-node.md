---
title: 'Início Rápido: Enviar a telemetria para o Azure IoT (Node.js)'
description: Neste início rápido, você executará dois aplicativos Node.js de exemplo para enviar telemetria simulada para um Hub IoT e ler telemetria do Hub IoT para processamento na nuvem.
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
- mqtt
- 'Role: Cloud Development'
- devx-track-js
- devx-track-azurecli
ms.date: 06/21/2019
ms.openlocfilehash: 77d3ac2c1e06851a13b4424d855fa9f9b12b6c32
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106064947"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-it-with-a-back-end-application-nodejs"></a>Início Rápido: Enviar a telemetria de um dispositivo para um hub IoT e lê-la com um back-end (Node.js)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

 Neste início rápido, você envia telemetria de um aplicativo de dispositivo simulado, por meio do Hub IoT do Azure, para um aplicativo de back-end para processamento. O Hub IoT é um serviço do Azure que permite a ingestão de grandes volumes de telemetria de seus dispositivos IoT pela nuvem para processamento ou armazenamento. Este início rápido usa dois aplicativos Node.js previamente escritos, um para enviar a telemetria e outro para ler a telemetria do hub. Antes de executar esses dois aplicativos, você cria um hub IoT e registra um dispositivo com o hub.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* [Node.js 10+](https://nodejs.org). Se você estiver usando o Azure Cloud Shell, não atualize a versão instalada do Node.js. O Azure Cloud Shell já tem a versão mais recente do Node.js.

    Você pode verificar a versão atual do Node.js no computador de desenvolvimento usando o seguinte comando:

    ```cmd/sh
    node --version
    ```

* [Um projeto de exemplo do Node.js](https://github.com/Azure-Samples/azure-iot-samples-node/archive/master.zip).

* Porta 8883 aberta no firewall. A amostra de dispositivo deste início rápido usa o protocolo MQTT, que se comunica pela porta 8883. Essa porta poderá ser bloqueada em alguns ambientes de rede corporativos e educacionais. Para obter mais informações e maneiras de resolver esse problema, confira [Como se conectar ao Hub IoT (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Crie um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrar um dispositivo

Um dispositivo deve ser registrado no hub IoT antes de poder se conectar. Neste início rápido, você usa o Azure Cloud Shell para registrar um dispositivo simulado.

1. Execute o comando a seguir no Azure Cloud Shell para criar a identidade do dispositivo.

   **YourIoTHubName**: substitua o espaço reservado abaixo pelo nome escolhido para o hub IoT.

   **MyNodeDevice**: esse é o nome do dispositivo que está sendo registrado. É recomendável usar **MyNodeDevice** conforme mostrado. Se você escolher um nome diferente para seu dispositivo, você também precisará usar esse nome ao longo deste artigo e atualizar o nome de dispositivo nos aplicativos de exemplo antes de executá-los.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyNodeDevice
    ```

1. Execute o seguinte comando no Azure Cloud Shell para obter a _cadeia de conexão de dispositivo_ referente ao dispositivo que você acabou de registrar:

   **YourIoTHubName**: substitua o espaço reservado abaixo pelo nome escolhido para o hub IoT.

    ```azurecli-interactive
    az iot hub device-identity connection-string show --hub-name {YourIoTHubName} --device-id MyNodeDevice --output table
    ```

    Tome nota da cadeia de conexão do dispositivo, que se parece com:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Você usará esse valor posteriormente no início rápido.

1. Você também precisa do _ponto de extremidade compatível com Hubs de Evento_, do _caminho compatível dos Hubs de Eventos_ e da _chave primária de serviço_ de seu hub IoT para ativar o aplicativo back-end a fim de se conectar ao seu hub IoT e recuperar as mensagens. Os comandos a seguir recuperam esses valores para o seu hub IoT:

   **YourIoTHubName**: substitua o espaço reservado abaixo pelo nome escolhido para o hub IoT.

    ```azurecli-interactive
    az iot hub show --query properties.eventHubEndpoints.events.endpoint --name {YourIoTHubName}

    az iot hub show --query properties.eventHubEndpoints.events.path --name {YourIoTHubName}

    az iot hub policy show --name service --query primaryKey --hub-name {YourIoTHubName}
    ```

    Anote esses três valores, que você usará posteriormente no início rápido.

## <a name="send-simulated-telemetry"></a>Enviar telemetria simulada

O aplicativo de dispositivo simulado se conecta a um ponto de extremidade específico do dispositivo em seu hub IoT e envia telemetria simulada de temperatura e umidade.

1. Abra a janela do terminal local e navegue até a pasta raiz do projeto Node.js de exemplo. Em seguida, navegue até a pasta **iot-hub\Quickstarts\simulated-device**.

1. Abra o arquivo **SimulatedDevice.js** em seu editor de texto preferido.

    Substitua o valor da variável `connectionString` pela cadeia de conexão do dispositivo que você anotou anteriormente. Salve suas alterações em **SimulatedDevice.js**.

1. Na janela do terminal local, execute os seguintes comandos para instalar as bibliotecas necessárias e executar o aplicativo de dispositivo simulado:

    ```cmd/sh
    npm install
    node SimulatedDevice.js
    ```

    A captura de tela a seguir mostra o resultado à medida que o aplicativo de dispositivo simulado envia telemetria para o seu hub IoT:

    ![Executar o dispositivo simulado](media/quickstart-send-telemetry-node/SimulatedDevice.png)

## <a name="read-the-telemetry-from-your-hub"></a>Ler a telemetria do hub

O aplicativo de back-end se conecta ao ponto de extremidade **Eventos** do lado do serviço em seu Hub IoT. O aplicativo recebe as mensagens do dispositivo para a nuvem, enviadas de seu dispositivo simulado. Um aplicativo de back-end do Hub IoT normalmente é executado na nuvem para receber e processar as mensagens do dispositivo para a nuvem.

1. Abra outra janela do terminal local e navegue até a pasta raiz do projeto Node.js de exemplo. Em seguida, navegue até a pasta **iot-hub\Quickstarts\read-d2c-messages**.

1. Abra o arquivo **ReadDeviceToCloudMessages.js** em seu editor de texto preferido. Atualize as variáveis a seguir e salve suas alterações no arquivo.

    | Variável | Valor |
    | -------- | ----------- |
    | `eventHubsCompatibleEndpoint` | Substitua o valor da variável pelo ponto de extremidade compatível com os Hubs de Eventos que você anotou anteriormente. |
    | `eventHubsCompatiblePath`     | Substitua o valor da variável pelo caminho compatível com os Hubs de Eventos que você anotou anteriormente. |
    | `iotHubSasKey`                | Substitua o valor da variável pela chave primária de serviço que você anotou anteriormente. |

1. Na janela do terminal local, execute os seguintes comandos para instalar as bibliotecas necessárias e executar o aplicativo de back-end:

    ```cmd/sh
    npm install
    node ReadDeviceToCloudMessages.js
    ```

    A captura de tela a seguir mostra a saída à medida que o aplicativo de back-end recebe a telemetria enviada pelo dispositivo simulado para o hub:

    ![Executar o aplicativo de back-end](media/quickstart-send-telemetry-node/ReadDeviceToCloud.png)

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você configurou um hub IoT, registrou um dispositivo, enviou telemetria simulada para o hub usando um aplicativo Node.js e leu a telemetria do hub usando um aplicativo de back-end simples.

Para saber como controlar o dispositivo simulado em um aplicativo de back-end, continue no próximo início rápido.

> [!div class="nextstepaction"]
> [Início Rápido: Controlar um dispositivo conectado a um hub IoT](quickstart-control-device-node.md)
