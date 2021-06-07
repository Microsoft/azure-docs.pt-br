---
title: Guia de Início Rápido – Enviar a telemetria para o Hub IoT do Azure (iOS)
description: Neste início rápido, você executa um aplicativo iOS de exemplo para enviar telemetria simulada a um hub IoT e ler a telemetria do hub IoT para processamento na nuvem.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: quickstart
ms.custom:
- mvc
- mqtt
- devx-track-azurecli
ms.date: 04/03/2019
ms.openlocfilehash: 21d25bd461a1a7e0cd2718cbe0ea32be06cf9055
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106066986"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-ios"></a>Início Rápido: Enviar telemetria de um dispositivo para um Hub IoT (iOS)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

O Hub IoT é um serviço do Azure que permite a ingestão de grandes volumes de telemetria de seus dispositivos IoT pela nuvem para processamento ou armazenamento. Neste artigo, você envia telemetria de um aplicativo de dispositivo simulado para o Hub IoT. Em seguida, você exibe os dados em um aplicativo de back-end.

Este artigo usa um aplicativo Swift previamente codificado para enviar a telemetria e um utilitário CLI para ler a telemetria do Hub IoT.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Baixe o exemplo de código dos [exemplos do Azure](https://github.com/Azure-Samples/azure-iot-samples-ios/archive/master.zip)

- A última versão do [XCode](https://developer.apple.com/xcode/) executando a última versão do SDK do iOS. Este início rápido foi testado com o XCode 10.2 e o iOS 12.2.

- A última versão do [CocoaPods](https://guides.cocoapods.org/using/getting-started.html).

- Verifique se a porta 8883 está aberta no firewall. A amostra de dispositivo deste início rápido usa o protocolo MQTT, que se comunica pela porta 8883. Essa porta poderá ser bloqueada em alguns ambientes de rede corporativos e educacionais. Para obter mais informações e maneiras de resolver esse problema, confira [Como se conectar ao Hub IoT (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Crie um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrar um dispositivo

Um dispositivo deve ser registrado no hub IoT antes de poder se conectar. Neste início rápido, você usa o Azure Cloud Shell para registrar um dispositivo simulado.

1. Execute o comando a seguir no Azure Cloud Shell para criar a identidade do dispositivo.

   **YourIoTHubName**: substitua o espaço reservado abaixo pelo nome escolhido para o hub IoT.

   **myiOSdevice**: esse é o nome do dispositivo que está sendo registrado. É recomendável usar **myiOSdevice** conforme mostrado. Se você escolher um nome diferente para seu dispositivo, você também precisará usar esse nome ao longo deste artigo e atualizar o nome de dispositivo nos aplicativos de exemplo antes de executá-los.

   ```azurecli-interactive
   az iot hub device-identity create --hub-name {YourIoTHubName} --device-id myiOSdevice
   ```

1. Execute o seguinte comando no Azure Cloud Shell para obter a _cadeia de conexão de dispositivo_ referente ao dispositivo que você acabou de registrar:

   **YourIoTHubName**: substitua o espaço reservado abaixo pelo nome escolhido para o hub IoT.

   ```azurecli-interactive
   az iot hub device-identity connection-string show --hub-name {YourIoTHubName} --device-id myiOSdevice --output table
   ```

   Tome nota da cadeia de conexão do dispositivo, que se parece com:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=myiOSdevice;SharedAccessKey={YourSharedAccessKey}`

    Você usará esse valor posteriormente no início rápido.

## <a name="send-simulated-telemetry"></a>Enviar telemetria simulada

O aplicativo de exemplo é executado em um dispositivo iOS, que se conecta a um ponto de extremidade específico do dispositivo em seu hub IoT e envia telemetria simulada de temperatura e umidade. 

### <a name="install-cocoapods"></a>Instalar CocoaPods

Os CocoaPods gerenciam dependências para projetos do iOS que usam bibliotecas de terceiros.

Em uma janela de terminal local, navegue até a pasta Azure-IoT-Samples-iOS que você baixou nos pré-requisitos. Em seguida, navegue até o projeto de exemplo:

```sh
cd quickstart/sample-device
```

Verifique se o XCode está fechado e execute o comando abaixo para instalar os CocoaPods declarados no arquivo **podfile**:

```sh
pod install
```

Além de instalar os pods necessários para o projeto, o comando de instalação também criou um arquivo de workspace do XCode que já está configurado para usar os pods para dependências. 

### <a name="run-the-sample-application"></a>Executar o aplicativo de exemplo 

1. Abra o workspace de exemplo no XCode.

   ```sh
   open "MQTT Client Sample.xcworkspace"
   ```

2. Expanda o projeto **Exemplo do Cliente MQTT** e a pasta com o mesmo nome.  
3. Abra **ViewController.swift** para edição no XCode. 
4. Pesquise a variável **connectionString** e atualize o valor com a cadeia de conexão do dispositivo anotado anteriormente.
5. Salve suas alterações. 
6. Execute o projeto no emulador de dispositivo com o botão **Compilar e executar** ou a combinação de teclas **Command + R**. 

   ![Executar o projeto](media/quickstart-send-telemetry-ios/run-sample.png)

7. Quando o emulador for aberto, selecione **Iniciar** no aplicativo de exemplo.

A captura de tela a seguir mostra alguns exemplos de saída à medida que o aplicativo envia telemetria simulada para o hub IoT:

   ![Executar o dispositivo simulado](media/quickstart-send-telemetry-ios/view-d2c.png)

## <a name="read-the-telemetry-from-your-hub"></a>Ler a telemetria do hub

O aplicativo de exemplo que você executou no emulador do XCode mostra dados sobre as mensagens enviadas do dispositivo. Você também pode exibir os dados por meio do hub IoT conforme vão sendo recebidos. A extensão de CLI do Hub IoT pode se conectar ao ponto de extremidade **Eventos** do lado do serviço em seu Hub IoT. A extensão recebe as mensagens do dispositivo para a nuvem, enviadas de seu dispositivo simulado. Um aplicativo de back-end do Hub IoT normalmente é executado na nuvem para receber e processar as mensagens do dispositivo para a nuvem.

Execute os seguintes comandos no Azure Cloud Shell, substituindo `YourIoTHubName` pelo nome do Hub IoT:

```azurecli-interactive
az iot hub monitor-events --device-id myiOSdevice --hub-name {YourIoTHubName}
```

A captura de tela a seguir mostra a saída à medida que a extensão recebe a telemetria enviada pelo dispositivo simulado para o hub:

A captura de tela abaixo mostra o tipo de telemetria mostrada na janela de terminal local:

![Exibir telemetria](media/quickstart-send-telemetry-ios/view-telemetry.png)

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você configurou um hub IoT, registrou um dispositivo, enviou telemetria simulada para o hub de um dispositivo iOS e leu a telemetria do hub. 

Para saber como controlar o dispositivo simulado em um aplicativo de back-end, continue no próximo início rápido.

> [!div class="nextstepaction"]
> [Início Rápido: Controlar um dispositivo conectado a um hub IoT](quickstart-control-device-node.md)
