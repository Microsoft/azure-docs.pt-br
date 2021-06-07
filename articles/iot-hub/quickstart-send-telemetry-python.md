---
title: Guia de Início Rápido – Enviar a telemetria ao Hub IoT do Azure (Python) | Microsoft Docs
description: Neste início rápido, você executa um aplicativo Python de exemplo para enviar telemetria simulada a um hub IoT e usar um utilitário para ler a telemetria do Hub IoT.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: quickstart
ms.custom:
- mvc
- mqtt
- devx-track-python
- 'Role: Cloud Development'
- devx-track-azurecli
ms.date: 06/16/2020
ms.openlocfilehash: bb86ccafaa0b9eac5ef35e96bbe3031a40f350ee
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106067513"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-it-with-a-back-end-application-python"></a>Início Rápido: Enviar a telemetria de um dispositivo para um hub IoT e lê-la com um back-end (Python)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

Neste início rápido, você envia telemetria de um aplicativo de dispositivo simulado, por meio do Hub IoT do Azure, para um aplicativo de back-end para processamento. O Hub IoT é um serviço do Azure que permite a ingestão de grandes volumes de telemetria de seus dispositivos IoT pela nuvem para processamento ou armazenamento. Este início rápido usa dois aplicativos Python previamente escritos, um para enviar a telemetria e outro para ler a telemetria do hub. Antes de executar esses dois aplicativos, você cria um hub IoT e registra um dispositivo com o hub.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta do Azure com uma assinatura ativa. [Crie um gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* [Python 3.7+](https://www.python.org/downloads/). Para outras versões do Python com suporte, confira [Recursos do Dispositivo IoT do Azure](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device#azure-iot-device-features).

* [Um projeto Python de exemplo](https://github.com/Azure-Samples/azure-iot-samples-python/archive/master.zip).

* Porta 8883 aberta no firewall. A amostra de dispositivo deste início rápido usa o protocolo MQTT, que se comunica pela porta 8883. Essa porta poderá ser bloqueada em alguns ambientes de rede corporativos e educacionais. Para obter mais informações e maneiras de resolver esse problema, confira [Como se conectar ao Hub IoT (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Crie um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrar um dispositivo

Um dispositivo deve ser registrado no hub IoT antes de poder se conectar. Neste início rápido, você usa o Azure Cloud Shell para registrar um dispositivo simulado.

1. Execute o comando a seguir no Azure Cloud Shell para criar a identidade do dispositivo.

    **YourIoTHubName**: substitua o espaço reservado abaixo pelo nome escolhido para o hub IoT.

    **MyPythonDevice**: esse é o nome do dispositivo que está sendo registrado. É recomendável usar o **MyPythonDevice** conforme mostrado. Se você escolher um nome diferente para seu dispositivo, você também precisará usar esse nome ao longo deste artigo e atualizar o nome de dispositivo nos aplicativos de exemplo antes de executá-los.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyPythonDevice
    ```

1. Execute o seguinte comando no Azure Cloud Shell para obter a _cadeia de conexão de dispositivo_ referente ao dispositivo que você registrou:

    **YourIoTHubName**: substitua o espaço reservado abaixo pelo nome escolhido para o hub IoT.

    ```azurecli-interactive
    az iot hub device-identity connection-string show --hub-name {YourIoTHubName} --device-id MyPythonDevice --output table
    ```

    Tome nota da cadeia de conexão do dispositivo, que se parece com:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyPythonDevice;SharedAccessKey={YourSharedAccessKey}`

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

1. Em uma janela de terminal local, navegue até a pasta raiz do projeto Python de exemplo. Em seguida, navegue até a pasta **iot-hub\Quickstarts\simulated-device**.

1. Abra o arquivo **SimulatedDevice.py** em seu editor de texto preferido.

    Substitua o valor da variável `CONNECTION_STRING` pela cadeia de conexão do dispositivo que você anotou anteriormente. Salve suas alterações em **SimulatedDevice.py**.

1. Na janela de terminal local, execute os seguintes comandos para instalar as bibliotecas necessárias para o aplicativo de dispositivo simulado:

    ```cmd/sh
    pip install azure-iot-device
    ```

1. Na janela de terminal local, execute os seguintes comandos para executar o aplicativo de dispositivo simulado:

    ```cmd/sh
    python SimulatedDevice.py
    ```

    A captura de tela a seguir mostra o resultado à medida que o aplicativo de dispositivo simulado envia telemetria para o seu hub IoT:

    ![Executar o dispositivo simulado](media/quickstart-send-telemetry-python/simulated-device.png)

## <a name="read-the-telemetry-from-your-hub"></a>Ler a telemetria do hub

O aplicativo de back-end se conecta ao ponto de extremidade **Eventos** do lado do serviço em seu Hub IoT. O aplicativo recebe as mensagens do dispositivo para a nuvem, enviadas de seu dispositivo simulado. Um aplicativo de back-end do Hub IoT normalmente é executado na nuvem para receber e processar as mensagens do dispositivo para a nuvem.

> [!NOTE]
> As etapas a seguir usam o exemplo síncrono, **read_device_to_cloud_messages_sync.py**. Você pode executar as mesmas etapas com o exemplo assíncrono, **read_device_to_cloud_messages_async. py**.

1. Em outra janela de terminal local, navegue até a pasta raiz do projeto Python de exemplo. Em seguida, navegue até a pasta **iot-hub\Quickstarts\read-d2c-messages**.

2. Abra o arquivo **read_device_to_cloud_messages_sync. py** em um editor de texto de sua escolha. Atualize as variáveis a seguir e salve suas alterações no arquivo.

    | Variável | Valor |
    | -------- | ----------- |
    | `EVENTHUB_COMPATIBLE_ENDPOINT` | Substitua o valor da variável pelo ponto de extremidade compatível com os Hubs de Eventos que você anotou anteriormente. |
    | `EVENTHUB_COMPATIBLE_PATH`     | Substitua o valor da variável pelo caminho compatível com os Hubs de Eventos que você anotou anteriormente. |
    | `IOTHUB_SAS_KEY`                | Substitua o valor da variável pela chave primária de serviço que você anotou anteriormente. |

3. Na janela de terminal local, execute os seguintes comandos para instalar as bibliotecas necessárias para o aplicativo de back-end:

    ```cmd/sh
    pip install azure-eventhub
    ```

4. Na janela de terminal local, execute os comandos a seguir para compilar e executar o aplicativo back-end:

    ```cmd/sh
    python read_device_to_cloud_messages_sync.py
    ```

    A captura de tela a seguir mostra a saída à medida que o aplicativo de back-end recebe a telemetria enviada pelo dispositivo simulado para o hub:

    ![Executar o aplicativo de back-end](media/quickstart-send-telemetry-python/read-device-to-cloud.png)

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você configurou um hub IoT, registrou um dispositivo, enviou telemetria simulada para o hub usando um aplicativo Python e leu a telemetria do hub usando um aplicativo de back-end simples.

Para saber como controlar o dispositivo simulado em um aplicativo de back-end, continue no próximo início rápido.

> [!div class="nextstepaction"]
> [Início Rápido: Controlar um dispositivo conectado a um hub IoT](quickstart-control-device-python.md)
