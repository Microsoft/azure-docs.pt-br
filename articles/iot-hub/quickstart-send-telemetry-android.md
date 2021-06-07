---
title: Guia de Início Rápido – Enviar a telemetria ao Hub IoT do Azure (Android) | Microsoft Docs
description: Neste início rápido, você executa um aplicativo Android de exemplo para enviar telemetria simulada a um hub IoT e ler a telemetria do hub IoT para processamento na nuvem.
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: quickstart
ms.custom:
- mvc
- mqtt
- devx-track-java
- devx-track-azurecli
ms.date: 03/15/2019
ms.author: wesmc
ms.openlocfilehash: c9e81786f9bd45961b38dbd9336ea98be1ab2010
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106059762"
---
# <a name="quickstart-send-iot-telemetry-from-an-android-device"></a>Início Rápido: Enviar telemetria de IoT de um dispositivo Android

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

Neste início rápido, você envia telemetria a um Hub IoT do Azure de um aplicativo Android em execução em um dispositivo simulado ou físico. O Hub IoT é um serviço do Azure que permite a ingestão de grandes volumes de telemetria de seus dispositivos IoT pela nuvem para processamento ou armazenamento. Este início rápido usa um aplicativo do Android previamente escrito para enviar a telemetria. A telemetria será lida no Hub IoT usando o Azure Cloud Shell. Antes de executar o aplicativo, crie um hub IoT e registre um dispositivo com o hub.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta do Azure com uma assinatura ativa. [Crie um gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* [Android Studio com o SDK do Android 27](https://developer.android.com/studio/). Para obter mais informações, confira [Instalação do Android](https://developer.android.com/studio/install). O SDK do Android 27 é usado pelo exemplo deste artigo.

* [Um aplicativo Android de exemplo](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Samples/device/AndroidSample). Este exemplo faz parte do repositório [azure-iot-samples-java](https://github.com/Azure-Samples/azure-iot-samples-java).

* Porta 8883 aberta no firewall. A amostra de dispositivo deste início rápido usa o protocolo MQTT, que se comunica pela porta 8883. Essa porta poderá ser bloqueada em alguns ambientes de rede corporativos e educacionais. Para obter mais informações e maneiras de resolver esse problema, confira [Como se conectar ao Hub IoT (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Crie um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrar um dispositivo

Um dispositivo deve ser registrado no hub IoT antes de poder se conectar. Neste início rápido, você usa o Azure Cloud Shell para registrar um dispositivo simulado.

1. Execute o comando a seguir no Azure Cloud Shell para criar a identidade do dispositivo.

   **YourIoTHubName**: substitua o espaço reservado abaixo pelo nome escolhido para o hub IoT.

   **MyAndroidDevice**: esse é o nome do dispositivo que está sendo registrado. É recomendável usar **MyAndroidDevice** conforme mostrado. Se você escolher um nome diferente para seu dispositivo, você também precisará usar esse nome ao longo deste artigo e atualizar o nome de dispositivo nos aplicativos de exemplo antes de executá-los.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyAndroidDevice
    ```

2. Execute o seguinte comando no Azure Cloud Shell para obter a _cadeia de conexão de dispositivo_ referente ao dispositivo que você acabou de registrar:

    **YourIoTHubName**: substitua o espaço reservado abaixo pelo nome escolhido para o hub IoT.

    ```azurecli-interactive
    az iot hub device-identity connection-string show --hub-name {YourIoTHubName} --device-id MyAndroidDevice --output table
    ```

    Tome nota da cadeia de conexão do dispositivo, que se parece com:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}`

    Você usará esse valor posteriormente neste início rápido para enviar a telemetria.

## <a name="send-simulated-telemetry"></a>Enviar telemetria simulada

1. Abra o projeto Android de exemplo do GitHub no Android Studio. O projeto está localizado no seguinte diretório da cópia clonada ou baixada do repositório [azure-iot-sample-java](https://github.com/Azure-Samples/azure-iot-samples-java): *\azure-iot-samples-java\iot-hub\Samples\device\AndroidSample*.

2. No Android Studio, abra *gradle.properties* do projeto de exemplo e substitua o espaço reservado **Device_Connection_String** pela cadeia de conexão de dispositivo que você anotou anteriormente.

    ```
    DeviceConnectionString=HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}
    ```

3. No Android Studio, clique em **Arquivo** > **Sincronizar Projeto com Arquivos do Gradle**. Verifique se que a compilação foi concluída.

   > [!NOTE]
   > Se a sincronização de projeto falhar, poderá ser por um dos seguintes motivos:
   >
   > * As versões do plug-in Android Gradle e do Gradle referenciadas no projeto estão desatualizadas para a sua versão do Android Studio. Siga [estas instruções](https://developer.android.com/studio/releases/gradle-plugin) para referenciar e instalar as versões corretas do plug-in e do Gradle para sua instalação.
   > * O contrato de licença para o SDK do Android não foi assinado. Siga as instruções na saída do build para assinar o contrato de licença e baixar o SDK.

4. Depois que a compilação for concluída, clique em **Executar** > **Executar “aplicativo”** . Configure o aplicativo para ser executado em um dispositivo Android físico ou em um emulador do Android. Para obter mais informações sobre como executar um aplicativo Android em um dispositivo físico ou emulador, confira [Executar seu aplicativo](https://developer.android.com/training/basics/firstapp/running-app).

5. Depois que o aplicativo for carregado, clique no botão **Iniciar** para começar a enviar dados telemétricos ao Hub IoT:

    ![Aplicativo](media/quickstart-send-telemetry-android/sample-screenshot.png)


## <a name="read-the-telemetry-from-your-hub"></a>Ler a telemetria do hub

Nesta seção, você usará o Azure Cloud Shell com a [extensão de IoT](/cli/azure/ext/azure-iot/iot) para monitorar as mensagens de dispositivo enviadas pelo dispositivo Android.

1. Usando o Azure Cloud Shell, execute o seguinte comando para se conectar e ler mensagens do hub IoT:

   **YourIoTHubName**: substitua o espaço reservado abaixo pelo nome escolhido para o hub IoT.

    ```azurecli-interactive
    az iot hub monitor-events --hub-name {YourIoTHubName} --output table
    ```

    A captura de tela abaixo mostra a saída à medida que o Hub IoT recebe a telemetria enviada pelo dispositivo Android:

      ![Leia as mensagens do dispositivo usando a CLI do Azure](media/quickstart-send-telemetry-android/read-data.png)
## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você configurou um hub IoT, registrou um dispositivo, enviou telemetria simulada para o hub usando um aplicativo Android e leu a telemetria do hub usando o Azure Cloud Shell.

Para saber como controlar o dispositivo simulado em um aplicativo de back-end, continue no próximo início rápido.

> [!div class="nextstepaction"]
> [Início Rápido: Controlar um dispositivo conectado a um hub IoT](quickstart-control-device-android.md)
