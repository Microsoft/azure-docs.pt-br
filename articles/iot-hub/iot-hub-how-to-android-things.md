---
title: Desenvolva para a plataforma Android Things usando SDKs de IoT do Azure | Microsoft Docs
description: Guia do desenvolvedor – saiba mais sobre como desenvolver no Android Things usando os SDKs do Hub IoT do Azure.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: robinsh
ms.openlocfilehash: 6008ca1549629bcbb582f38de2ab1b453cd73a99
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/18/2020
ms.locfileid: "94843750"
---
# <a name="develop-for-android-things-platform-using-azure-iot-sdks"></a>Desenvolva para a plataforma Android Things usando SDKs de IoT do Azure

Os [SDKs do Hub IoT do Azure](./iot-hub-devguide-sdks.md) fornecem suporte de primeiro nível a plataformas populares como Windows, Linux, OSX, MBED e plataformas móveis, como Android e iOS.  Como parte do nosso compromisso para proporcionar maior escolha e flexibilidade em implantações de IoT, o SDK do Java também é compatível com a plataforma [Android Things](https://developer.android.com/things/).  Os desenvolvedores podem aproveitar os benefícios do sistema operacional Android Things no lado do dispositivo enquanto usam o [Hub IoT do Azure](about-iot-hub.md) como o hub de mensagens central que é dimensionado para milhões de dispositivos conectados ao mesmo tempo.

Este tutorial descreve as etapas para criar um aplicativo do lado do dispositivo no Android Things usando o SDK do Java do Azure IoT.

## <a name="prerequisites"></a>Pré-requisitos

* Um hardware compatível com Android Things com o SO Android Things em execução.  Você pode seguir a [Documentação do Android Things](https://developer.android.com/things/get-started/kits#flash-at) sobre como iniciar o sistema operacional do Android Things.  Verifique se seu dispositivo Android Things está conectado à Internet com periféricos essenciais, como teclado, vídeo e mouse conectados.  Este tutorial usa o Raspberry Pi 3.

* Versão mais recente do [Android Studio](https://developer.android.com/studio/)

* Versão mais recente do [Git](https://git-scm.com/)

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="create-an-iot-hub"></a>Crie um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrar um dispositivo

Um dispositivo deve ser registrado no hub IoT antes de poder se conectar. Neste início rápido, você usa o Azure Cloud Shell para registrar um dispositivo simulado.

1. Execute os seguintes comandos no Azure Cloud Shell para adicionar a extensão da CLI do Hub IoT e criar a identidade do dispositivo.

   **YourIoTHubName**: substitua o espaço reservado abaixo pelo nome escolhido para o Hub IoT.

   **MyAndroidThingsDevice** : esse é o nome fornecido para o dispositivo registrado. Use MyAndroidThingsDevice conforme mostrado. Se você escolher um nome diferente para seu dispositivo, você também precisará usar esse nome ao longo deste artigo e atualizar o nome de dispositivo nos aplicativos de exemplo antes de executá-los.

    ```azurecli-interactive
    az extension add --name azure-iot
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyAndroidThingsDevice
    ```

2. Execute os seguintes comandos no Azure Cloud Shell para obter a *cadeia de conexão do dispositivo* para o dispositivo que você acabou de registrar. Substitua `YourIoTHubName` abaixo pelo nome que você escolher para o Hub IOT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyAndroidThingsDevice --output table
    ```

    Tome nota da cadeia de conexão do dispositivo, que se parece com:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidThingsDevice;SharedAccessKey={YourSharedAccessKey}`

    Você usará esse valor posteriormente no início rápido.

## <a name="building-an-android-things-application"></a>Criando um aplicativo do Android Things

1. A primeira etapa para criar um aplicativo do Android Things é conectar-se a seus dispositivos Android Things. Conecte seu dispositivo Android Things a uma exibição e conecte-se à Internet. O Android Things fornece [documentação](https://developer.android.com/things/get-started/kits) sobre como se conectar ao WiFi. Depois de se conectar à Internet, anote o endereço IP listado em Redes.

2. Use a ferramenta [adb](https://developer.android.com/studio/command-line/adb) para conectar-se ao seu dispositivo Android Things com o endereço IP observado acima. Verifique novamente a conexão usando este comando no seu terminal. Você deve ver seus dispositivos listados como "conectado".

   ```
   adb devices
   ```

3. Baixe nossa amostra para Android/Android Things deste [repositório](https://github.com/Azure-Samples/azure-iot-samples-java) ou use o Git.

   ```
   git clone https://github.com/Azure-Samples/azure-iot-samples-java.git
   ```

4. No Android Studio, abra o Android Project localizado em "\azure-iot-samples-java\iot-hub\Samples\device\AndroidSample".

5. Abra o arquivo gradle.properties e substitua "Device_connection_string" pela cadeia de conexão do dispositivo observado anteriormente.
 
6. Clique em Executar – Depurar e selecione seu dispositivo para implantar esse código em seus dispositivos Android Things.

7. Quando o aplicativo é iniciado com êxito, você pode ver um aplicativo em execução em seu dispositivo Android Things. Esse aplicativo de exemplo envia leituras de temperatura geradas aleatoriamente.

## <a name="read-the-telemetry-from-your-hub"></a>Ler a telemetria do hub

Você pode exibir os dados por meio do hub IoT conforme vão sendo recebidos. A extensão de CLI do Hub IoT pode se conectar ao ponto de extremidade **Eventos** do lado do serviço em seu Hub IoT. A extensão recebe as mensagens do dispositivo para a nuvem, enviadas de seu dispositivo simulado. Um aplicativo de back-end do Hub IoT normalmente é executado na nuvem para receber e processar as mensagens do dispositivo para a nuvem.

Execute os seguintes comandos no Azure Cloud Shell, substituindo `YourIoTHubName` pelo nome do Hub IoT:

```azurecli-interactive
az iot hub monitor-events --device-id MyAndroidThingsDevice --hub-name YourIoTHubName
```

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Próximas etapas

* Consulte [como gerenciar a conectividade e mensagens confiáveis](iot-hub-reliability-features-in-sdks.md) usando SDKs do Hub IoT.
* Saiba mais sobre como [desenvolver para plataformas móveis](iot-hub-how-to-develop-for-mobile-devices.md) como iOS e Android.
* [Suporte de plataforma do SDK do IoT do Azure](iot-hub-device-sdk-platform-support.md)