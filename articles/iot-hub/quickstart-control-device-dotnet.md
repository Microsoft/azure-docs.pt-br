---
title: Guia de Início Rápido – Controlar um dispositivo no Hub IoT do Azure (.NET) | Microsoft Docs
description: Neste início rápido, você executa dois aplicativos C# de exemplo. Um deles é um aplicativo de serviço que pode controlar remotamente os dispositivos conectados ao seu hub. O outro aplicativo simula um dispositivo conectado ao seu hub que pode ser controlado remotamente.
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
ms.date: 03/04/2020
ms.openlocfilehash: b89cc9dfb0a98f61b74ecf42471d08129661fb22
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106059846"
---
# <a name="quickstart-control-a-device-connected-to-an-iot-hub-net"></a>Início Rápido: Controlar um dispositivo conectado a um hub IoT (.NET)

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

O Hub IoT é um serviço do Azure que habilita a ingestão de grandes volumes de telemetria de dispositivos na nuvem e o gerenciamento de seus dispositivos IoT pela nuvem para armazenamento e processamento. Neste início rápido, você usa um *método direto* para controlar um dispositivo simulado conectado ao seu hub IoT. Você pode usar métodos diretos para alterar remotamente o comportamento de um dispositivo conectado ao seu hub IoT.

O início rápido usa dois aplicativos previamente escritos em .NET:

* Um aplicativo de dispositivo simulado que responde aos métodos diretos chamados de um aplicativo de serviço. Para receber as chamadas de método direto, esse aplicativo se conecta a um ponto de extremidade específico do dispositivo em seu hub IoT.

* Um aplicativo de serviço que chama os métodos diretos no dispositivo simulado. Para chamar um método direto em um dispositivo, esse aplicativo se conecta a um ponto de extremidade do lado do serviço em seu hub IoT.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Os dois exemplos de aplicativo executados neste início rápido são escritos usando o C#. É necessário ter o SDK do .NET Core 3.1 ou versão mais recente em seu computador de desenvolvimento.

    Você pode fazer o download do SDK do .NET Core para várias plataformas a partir do [.NET](https://www.microsoft.com/net/download/all).

    Verifique a versão atual do C# no computador de desenvolvimento usando o seguinte comando:

    ```cmd/sh
    dotnet --version
    ```
* Caso ainda não tenha feito isso, baixe os exemplos do C# do Azure IoT do https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip e extraia o arquivo ZIP.

* Verifique se a porta 8883 está aberta no firewall. A amostra de dispositivo deste início rápido usa o protocolo MQTT, que se comunica pela porta 8883. Essa porta poderá ser bloqueada em alguns ambientes de rede corporativos e educacionais. Para obter mais informações e maneiras de resolver esse problema, confira [Como se conectar ao Hub IoT (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Crie um hub IoT

Se você tiver concluído o [Início Rápido: Enviar telemetria de um dispositivo para um Hub IoT](quickstart-send-telemetry-dotnet.md) anterior, ignore esta etapa.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrar um dispositivo

Se você tiver concluído o [Início Rápido: Enviar telemetria de um dispositivo para um Hub IoT](quickstart-send-telemetry-dotnet.md) anterior, ignore esta etapa.

Um dispositivo deve ser registrado no hub IoT antes de poder se conectar. Neste início rápido, você usa o Azure Cloud Shell para registrar um dispositivo simulado.

1. Execute o comando a seguir no Azure Cloud Shell para criar a identidade do dispositivo.

   **YourIoTHubName**: substitua o espaço reservado abaixo pelo nome escolhido para o hub IoT.

   **MyDotnetDevice**: esse é o nome do dispositivo que está sendo registrado. É recomendável usar **MyDotnetDevice** conforme mostrado. Se escolher um nome diferente para seu dispositivo, você também precisará usar esse nome ao longo deste artigo, bem como atualizar o nome do dispositivo nos aplicativos de exemplo antes de executá-los.

    ```azurecli-interactive
    az iot hub device-identity create \
      --hub-name {YourIoTHubName} --device-id MyDotnetDevice
    ```

2. Execute os seguintes comandos no Azure Cloud Shell para obter a _cadeia de conexão de dispositivo_ referente ao dispositivo que você acabou de registrar:

   **YourIoTHubName**: substitua o espaço reservado abaixo pelo nome escolhido para o hub IoT.

    ```azurecli-interactive
    az iot hub device-identity connection-string show \
      --hub-name {YourIoTHubName} \
      --device-id MyDotnetDevice \
      --output table
    ```

    Tome nota da cadeia de conexão do dispositivo, que se parece com:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Você usará esse valor posteriormente no início rápido.

## <a name="retrieve-the-service-connection-string"></a>Recuperar a cadeia de conexão de serviço

Você também precisa de uma _cadeia de conexão de serviço_ do seu hub IoT para permitir que aplicativos de serviço se conectem ao hub e recuperem mensagens. O comando abaixo recupera a cadeia de conexão de serviço para o hub IoT:

```azurecli-interactive
az iot hub connection-string show --policy-name service --name {YourIoTHubName} --output table
```

Tome nota da cadeia de conexão de serviço, que se parece com:

   `HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}`

Você usará esse valor posteriormente no início rápido. A cadeia de conexão do serviço é diferente da cadeia de conexão do dispositivo que você anotou na etapa anterior.

## <a name="listen-for-direct-method-calls"></a>Escutar chamadas de método direto

O aplicativo de dispositivo simulado se conecta a um ponto de extremidade específico do dispositivo em seu hub IoT, envia telemetria simulada e escuta chamadas de método direto de seu hub. Neste início rápido, a chamada de método direto do hub informa ao dispositivo para alterar o intervalo de envio da telemetria. O dispositivo simulado envia uma confirmação para o hub depois de executar o método direto.

1. Em uma janela de terminal local, navegue até a pasta raiz do projeto C# de exemplo. Em seguida, navegue até a pasta **iot-hub\Quickstarts\SimulatedDeviceWithCommand**.

2. Na janela de terminal local, execute os seguintes comandos para instalar os pacotes necessários para o aplicativo de dispositivo simulado:

    ```cmd/sh
    dotnet restore
    ```

3. Na janela de terminal local, execute os seguintes comandos para compilar e executar o aplicativo de dispositivo simulado, substituindo `{DeviceConnectionString}` pela cadeia de conexão do dispositivo que você anotou anteriormente:

    ```cmd/sh
    dotnet run -- {DeviceConnectionString}
    ```

    A captura de tela a seguir mostra o resultado à medida que o aplicativo de dispositivo simulado envia telemetria para o seu hub IoT:

    ![Executar o dispositivo simulado](./media/quickstart-control-device-dotnet/SimulatedDevice-1.png)

## <a name="call-the-direct-method"></a>Chamar o método direto

O aplicativo de serviço se conecta a um ponto de extremidade do lado do serviço em seu Hub IoT. O aplicativo faz chamadas de método direto para um dispositivo por meio do hub IoT e escuta as confirmações. Um aplicativo de serviço do hub IoT normalmente é executado na nuvem.

1. Em outra janela de terminal local, navegue até a pasta raiz do projeto C# de exemplo. Em seguida, navegue até a pasta **iot-hub\Quickstarts\InvokeDeviceMethod**.

2. Na janela de terminal local, execute os seguintes comandos para instalar as bibliotecas necessárias para o aplicativo de serviço:

    ```cmd/sh
    dotnet restore
    ```

3. Na janela de terminal local, execute os seguintes comandos para criar e executar o aplicativo de serviço, substituindo `{ServiceConnectionString}` pela cadeia de conexão de serviço que você anotou anteriormente:

    ```cmd/sh
    dotnet run -- {ServiceConnectionString}
    ```

    A seguinte captura de tela mostra a saída enquanto o aplicativo faz uma chamada de método direto ao dispositivo e recebe uma confirmação:

    ![Executar o aplicativo de serviço](./media/quickstart-control-device-dotnet/BackEndApplication.png)

    Após executar o aplicativo de serviço, você verá uma mensagem na janela do console com o dispositivo simulado em execução, e a taxa de mudança de envio das mensagens:

    ![Alteração no cliente simulado](./media/quickstart-control-device-dotnet/SimulatedDevice-2.png)

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você já chamou um método direto em um dispositivo de um aplicativo de serviço e respondeu à chamada de método direto em um aplicativo de dispositivo simulado.

Para saber como rotear mensagens de dispositivo para nuvem para destinos diferentes na nuvem, continue n próximo tutorial.

> [!div class="nextstepaction"]
> [Tutorial: Encaminhar a telemetria para pontos de extremidade diferentes para processamento](tutorial-routing.md)
