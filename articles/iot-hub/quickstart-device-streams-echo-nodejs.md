---
title: Comunicar-se com um aplicativo de dispositivo em Node.js por meio de fluxos de dispositivos do Hub IoT (versão prévia) | Microsoft Docs
description: Neste início rápido, você executará aplicativos do lado do serviço do Node.js que se comunica com um dispositivo IoT por meio de um fluxo de dispositivos.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: 74a9e5e6be326bf9cef3dc6d26594b80491deb3e
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446055"
---
# <a name="quickstart-communicate-to-a-device-application-in-nodejs-via-iot-hub-device-streams-preview"></a>Início Rápido: comunicar-se com um aplicativo de dispositivo no Node.js por meio de fluxos de dispositivos do Hub IoT (versão prévia)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

Atualmente, o Hub IoT do Microsoft Azure dá suporte a fluxos de dispositivos como uma [versão prévia do recurso](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Os [fluxos de dispositivos do Hub IoT](./iot-hub-device-streams-overview.md) permitem que aplicativos de serviço e dispositivo se comuniquem de maneira segura e simples para o firewall. Durante a versão prévia pública, o SDK do Node.js dá suporte somente a fluxos de dispositivos no lado do serviço. Consequentemente, este início rápido contém apenas instruções para executar o aplicativo no lado do serviço. Você deve executar um aplicativo no lado do dispositivo que acompanha de um dos seguintes inícios rápidos:

* [Comunicação com aplicativos de dispositivo no C por meio de fluxos de dispositivos do Hub IoT](./quickstart-device-streams-echo-c.md)

* [Comunicação com aplicativos de dispositivo no C# por meio de fluxos de dispositivos do Hub IoT](./quickstart-device-streams-echo-csharp.md).

Neste início rápido, o aplicativo Node.js no lado do serviço tem as funcionalidades a seguir:

* Cria um fluxo de dispositivos para um dispositivo IoT.

* Lê a entrada da linha de comando e a envia para o aplicativo de dispositivo, que vai reproduzi-la novamente.

O código demonstrará o processo de inicialização de um fluxo de dispositivo, assim como usá-lo para enviar e receber dados.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Atualmente, a versão prévia dos fluxos de dispositivos só é compatível com os Hubs IoT criados nas seguintes regiões:

*  **Centro dos EUA**

*  **EUA Central EUAP**

Para executar o aplicativo do lado do serviço neste início rápido, você precisa do Node.js v10.x.x ou posterior no computador de desenvolvimento.

Você pode baixar o Node.js para várias plataformas do [Nodejs.org](https://nodejs.org).

Você pode verificar a versão atual do Node.js no computador de desenvolvimento usando o seguinte comando:

```
node --version
```

Execute o comando a seguir para adicionar a Extensão do Microsoft Azure IoT para a CLI do Azure à instância do Cloud Shell. A Extensão de IoT adiciona comandos específicos do Hub IoT, do IoT Edge e do DPS (Serviço de Provisionamento de Dispositivos) no IoT à CLI do Azure.

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Caso ainda não tenha feito isso, faça o download do projeto de exemplo do Node.js do https://github.com/Azure-Samples/azure-iot-samples-node/archive/streams-preview.zip e extraia o arquivo ZIP.

## <a name="create-an-iot-hub"></a>Crie um hub IoT

Se tiver concluído o [Início Rápido: enviar telemetria de um dispositivo para um hub IoT](quickstart-send-telemetry-node.md), pode ignorar esta etapa.

[!INCLUDE [iot-hub-include-create-hub-device-streams](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>Registrar um dispositivo

Se tiver concluído o [Início Rápido: enviar telemetria de um dispositivo para um hub IoT](quickstart-send-telemetry-node.md), pode ignorar esta etapa.

Um dispositivo deve ser registrado no hub IoT antes de poder se conectar. Neste início rápido, você usa o Azure Cloud Shell para registrar um dispositivo simulado.

1. Execute o comando a seguir no Azure Cloud Shell para criar a identidade do dispositivo.

   **YourIoTHubName**: substitua o espaço reservado abaixo pelo nome escolhido para o hub IoT.

   **MyDevice**: Esse é o nome fornecido para o dispositivo registrado. Use MyDevice, conforme mostrado. Se você escolher um nome diferente para seu dispositivo, você também precisará usar esse nome ao longo deste artigo e atualizar o nome de dispositivo nos aplicativos de exemplo antes de executá-los.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyDevice
    ```

2. Você também precisa de uma *cadeia de conexão de serviço* para permitir que aplicativos de back-end se conectem ao seu hub IoT e recuperem mensagens. O comando abaixo recupera a cadeia de conexão de serviço para o hub IoT:

    **YourIoTHubName**: substitua o espaço reservado abaixo pelo nome escolhido para o hub IoT.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name YourIoTHubName
    ```

    Anote o valor retornado, que tem esta aparência:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="communicate-between-device-and-service-via-device-streams"></a>Comunicação entre o dispositivo e serviço por meio de fluxos de dispositivos

Nesta seção, você executará o aplicativo do lado do dispositivo e o aplicativo do lado do serviço, e estabelecerá comunicação entre os dois.

### <a name="run-the-device-side-application"></a>Executar o aplicativo do lado do dispositivo

Como mencionado anteriormente, o SDK do Node.js do Hub IoT dá suporte somente a fluxos de dispositivos no lado do serviço. Para o aplicativo no lado do dispositivo, use o programa de dispositivo que acompanha disponível em um destes inícios rápidos:

   * [Comunicação com aplicativos de dispositivo no C por meio de fluxos de dispositivos do Hub IoT](./quickstart-device-streams-echo-c.md)

   * [Comunicação com aplicativos de dispositivo no C# por meio de fluxos de dispositivos do Hub IoT](./quickstart-device-streams-echo-csharp.md)

Certifique-se de que o aplicativo do lado do dispositivo está em execução antes de prosseguir para a próxima etapa.

### <a name="run-the-service-side-application"></a>Executar o aplicativo do lado do serviço

Supondo que o aplicativo do lado do dispositivo está em execução, siga as etapas abaixo para executar o aplicativo do lado do serviço em Node.js:

* Forneça suas credenciais de serviço e a identificação do dispositivo como variáveis de ambiente.
 
   ```
   # In Linux
   export IOTHUB_CONNECTION_STRING="<provide_your_service_connection_string>"
   export STREAMING_TARGET_DEVICE="MyDevice"

   # In Windows
   SET IOTHUB_CONNECTION_STRING=<provide_your_service_connection_string>
   SET STREAMING_TARGET_DEVICE=MyDevice
   ```
  
   Altere `MyDevice` para a identificação do dispositivo que você escolheu para seu dispositivo.

* Navegue até `Quickstarts/device-streams-service` na pasta de projeto descompactada e execute o exemplo usando o nó.

   ```
   cd azure-iot-samples-node-streams-preview/iot-hub/Quickstarts/device-streams-service
    
   # Install the preview service SDK, and other dependencies
   npm install azure-iothub@streams-preview
   npm install

   node echo.js
   ```

No final da última etapa, o programa do lado do serviço iniciará um fluxo para seu dispositivo e, após o estabelecimento, enviará um buffer de cadeia de caracteres para o serviço pelo fluxo. Neste exemplo, o programa do lado do serviço simplesmente lê o `stdin` no terminal e o envia para o dispositivo, que vai reproduzi-lo novamente. Isso demonstra a comunicação bidirecional bem-sucedida entre os dois aplicativos.

![Saída do console no lado do serviço](./media/quickstart-device-streams-echo-nodejs/service-console-output.png)

Em seguida, você pode encerrar o programa pressionando Enter novamente.

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [iot-hub-quickstarts-clean-up-resources-device-streams](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você configurou um hub IoT, registrou um dispositivo, estabeleceu um fluxo de dispositivos entre aplicativos no lado do dispositivo e do serviço e usou o fluxo para enviar dados entre os aplicativos.

Use os links abaixo para saber mais sobre fluxos de dispositivos:

> [!div class="nextstepaction"]
> [Visão geral dos fluxos de dispositivos](./iot-hub-device-streams-overview.md) 