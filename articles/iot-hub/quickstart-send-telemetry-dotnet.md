---
title: Guia de Início Rápido – Enviar a telemetria ao Hub IoT do Azure (C#) | Microsoft Docs
description: Neste início rápido, você executa dois aplicativos C# de exemplo para enviar telemetria simulada a um Hub IoT e ler a telemetria do Hub IoT para processamento na nuvem.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom:
- mvc
- mqtt
- 'Role: Cloud Development'
- devx-track-azurecli
ms.date: 06/01/2020
ms.openlocfilehash: 1a681cd3f1c624268fdc9bc62cf9841e9ac28827
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106057500"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-it-with-a-service-application-net"></a>Início Rápido: Enviar a telemetria de um dispositivo para um hub IoT e lê-la com um aplicativo de serviço (.NET)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

O Hub IoT é um serviço do Azure que permite a ingestão de grandes volumes de telemetria de seus dispositivos IoT pela nuvem para processamento ou armazenamento. Neste início rápido, você envia telemetria de um aplicativo de dispositivo simulado, por meio do hub IoT, para um aplicativo de serviço para processamento.

O início rápido usa dois aplicativos C# previamente escritos, um para enviar a telemetria e outro para ler a telemetria do hub. Antes de executar esses dois aplicativos, você cria um hub IoT e registra um dispositivo com o hub.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Os dois exemplos de aplicativo executados neste início rápido são escritos usando o C#. É necessário ter o SDK do .NET Core 3.1 ou versão mais recente em seu computador de desenvolvimento.

    Você pode fazer o download do SDK do .NET Core para várias plataformas a partir do [.NET](https://www.microsoft.com/net/download/all).

    Verifique a versão atual do C# no computador de desenvolvimento usando o seguinte comando:

    ```cmd/sh
    dotnet --version
    ```

    > [!NOTE]
    > Recomenda-se o SDK do .NET Core 3.1 (ou versão mais recente) para compilar o código do serviço de Hubs de Eventos usado para ler a telemetria neste início rápido.


* Baixe as amostras de C# do Azure IoT em [https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) e extraia o arquivo zip.

* Verifique se a porta 8883 está aberta no firewall. A amostra de dispositivo deste início rápido usa o protocolo MQTT, que se comunica pela porta 8883. Essa porta poderá ser bloqueada em alguns ambientes de rede corporativos e educacionais. Para obter mais informações e maneiras de resolver esse problema, confira [Como se conectar ao Hub IoT (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Crie um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrar um dispositivo

Um dispositivo deve ser registrado no hub IoT antes de poder se conectar. Neste início rápido, você usa o Azure Cloud Shell para registrar um dispositivo simulado.

1. Execute o comando a seguir no Azure Cloud Shell para criar a identidade do dispositivo.

   **YourIoTHubName**: substitua o espaço reservado abaixo pelo nome escolhido para o hub IoT.

   **MyDotnetDevice**: esse é o nome do dispositivo que está sendo registrado. É recomendável usar **MyDotnetDevice** conforme mostrado. Se você escolher um nome diferente para seu dispositivo, você também precisará usar esse nome ao longo deste artigo e atualizar o nome de dispositivo nos aplicativos de exemplo antes de executá-los.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDotnetDevice
    ```

2. Execute o seguinte comando no Azure Cloud Shell para obter a _cadeia de conexão de dispositivo_ referente ao dispositivo que você acabou de registrar:

   **YourIoTHubName**: substitua o espaço reservado abaixo pelo nome escolhido para o hub IoT.

    ```azurecli-interactive
    az iot hub device-identity connection-string show --hub-name {YourIoTHubName} --device-id MyDotnetDevice --output table
    ```

    Tome nota da cadeia de conexão do dispositivo, que se parece com:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDotnetDevice;SharedAccessKey={YourSharedAccessKey}`

    Você usará esse valor posteriormente no início rápido.

3. Você também precisa do _ponto de extremidade compatível com Hubs de Eventos_, do _caminho compatível com Hubs de Eventos_ e da _chave primária de serviço_ de seu hub IoT para habilitar o aplicativo de serviço a fim de se conectar ao seu hub IoT e recuperar as mensagens. Os comandos a seguir recuperam esses valores para o seu hub IoT:

   **YourIoTHubName**: substitua o espaço reservado abaixo pelo nome escolhido para o hub IoT.

    ```azurecli-interactive
    az iot hub show --query properties.eventHubEndpoints.events.endpoint --name {YourIoTHubName}

    az iot hub show --query properties.eventHubEndpoints.events.path --name {YourIoTHubName}

    az iot hub policy show --name service --query primaryKey --hub-name {YourIoTHubName}
    ```

    Anote esses três valores, que você usará posteriormente no início rápido.

## <a name="send-simulated-telemetry"></a>Enviar telemetria simulada

O aplicativo de dispositivo simulado se conecta a um ponto de extremidade específico do dispositivo em seu hub IoT e envia telemetria simulada de temperatura e umidade.

1. Em uma janela de terminal local, navegue até a pasta raiz do projeto C# de exemplo. Em seguida, navegue até a pasta **iot-hub\Quickstarts\SimulatedDevice**.

2. Na janela de terminal local, execute os seguintes comandos para instalar os pacotes necessários para o aplicativo de dispositivo simulado:

    ```cmd/sh
    dotnet restore
    ```

3. Na janela de terminal local, execute o seguinte comando para criar e executar o aplicativo de dispositivo simulado com a cadeia de conexão do dispositivo que você anotou anteriormente:

    ```cmd/sh
    dotnet run -- {DeviceConnectionString}
    ```

    A captura de tela a seguir mostra o resultado à medida que o aplicativo de dispositivo simulado envia telemetria para o seu hub IoT:

    ![Executar o dispositivo simulado](media/quickstart-send-telemetry-dotnet/simulated-device.png)

## <a name="read-the-telemetry-from-your-hub"></a>Ler a telemetria do hub

O aplicativo de serviço se conecta ao ponto de extremidade **Eventos** do lado do serviço em seu hub IoT. O aplicativo recebe as mensagens do dispositivo para a nuvem, enviadas de seu dispositivo simulado. Um aplicativo de serviço do hub IoT normalmente é executado na nuvem para receber e processar as mensagens do dispositivo para nuvem.

1. Em outra janela de terminal local, navegue até a pasta raiz do projeto C# de exemplo. Em seguida, navegue até a pasta **iot-hub\Quickstarts\ReadD2cMessages**.

2. Na janela de terminal local, execute o seguinte comando para instalar as bibliotecas necessárias para o aplicativo:

    ```cmd/sh
    dotnet restore
    ```

3. Na janela de terminal local, execute o comando a seguir para ver as opções de parâmetro.

    ```cmd/sh
    dotnet run
    ```

4. Na janela de terminal local, execute um dos seguintes comandos para criar e executar o aplicativo com:

    ```cmd/sh
    dotnet run -- -c {EventHubConnectionString}
    ```

    ou

    ```cmd/sh
    dotnet run -- -e {EventHubCompatibleEndpoint} -n {EventHubName} -s {SharedAccessKey}
    ```

    A seguinte captura de tela mostra a saída à medida que o aplicativo de serviço recebe a telemetria enviada pelo dispositivo simulado para o hub:

    ![Executar o aplicativo de serviço](media/quickstart-send-telemetry-dotnet/read-device-to-cloud.png)

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você configurou um hub IoT, registrou um dispositivo, enviou telemetria simulada para o hub usando um aplicativo C# e leu a telemetria do hub usando um aplicativo de serviço simples.

Para saber como controlar o dispositivo simulado em um aplicativo de serviço, prossiga para o próximo guia de início rápido.

> [!div class="nextstepaction"]
> [Início Rápido: Controlar um dispositivo conectado a um hub IoT](quickstart-control-device-dotnet.md)
