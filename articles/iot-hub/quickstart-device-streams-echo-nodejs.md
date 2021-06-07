---
title: Guia de Início Rápido – Comunicar-se com o aplicativo do dispositivo no Node.js com os fluxos de dispositivos do Hub IoT do Azure
description: Neste início rápido, você executará aplicativos do lado do serviço do Node.js que se comunica com um dispositivo IoT por meio de um fluxo de dispositivos.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom: mvc, devx-track-js, devx-track-azurecli
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: 335014f032162866e4780bf1294ddcd108b4fd03
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98624381"
---
# <a name="quickstart-communicate-to-a-device-application-in-nodejs-via-iot-hub-device-streams-preview"></a>Início Rápido: comunicar-se com um aplicativo de dispositivo no Node.js por meio de fluxos de dispositivos do Hub IoT (versão prévia)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

Neste guia de início rápido, você executa um aplicativo do lado do serviço e, usando fluxos de dispositivos, configura a comunicação entre um dispositivo e um serviço. Os fluxos de dispositivos do Hub IoT do Azure permitem que aplicativos de serviço e de dispositivo se comuniquem de maneira segura e consoante com o firewall. Durante a versão prévia pública, o SDK do Node.js dá suporte somente a fluxos de dispositivos no lado do serviço. Consequentemente, este início rápido contém apenas instruções para executar o aplicativo no lado do serviço.

## <a name="prerequisites"></a>Pré-requisitos

* A conclusão de [Comunicação com aplicativos de dispositivo no C por meio de fluxos de dispositivos do Hub IoT](./quickstart-device-streams-echo-c.md) ou [Comunicação com aplicativos de dispositivo em C# por meio de fluxos de dispositivos do Hub IoT](./quickstart-device-streams-echo-csharp.md).

* Uma conta do Azure com uma assinatura ativa. [Crie um gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* [Node.js 10+](https://nodejs.org).

    Você pode verificar a versão atual do Node.js no computador de desenvolvimento usando o seguinte comando:

    ```cmd/sh
    node --version
    ```

* [Um projeto de exemplo do Node.js](https://github.com/Azure-Samples/azure-iot-samples-node/archive/streams-preview.zip).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

Atualmente, o Hub IoT do Microsoft Azure dá suporte a fluxos de dispositivos como uma [versão prévia do recurso](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

> [!IMPORTANT]
> Atualmente, a versão prévia dos fluxos de dispositivos só é compatível com os Hubs IoT criados nas seguintes regiões:
>
> * Centro dos EUA
> * EUA Central EUAP
> * Norte da Europa
> * Sudeste Asiático

## <a name="create-an-iot-hub"></a>Crie um hub IoT

Se você tiver concluído o [Início Rápido: Enviar telemetria de um dispositivo para um Hub IoT](quickstart-send-telemetry-node.md) anterior, ignore esta etapa.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrar um dispositivo

Se você tiver concluído o [Início Rápido: Enviar telemetria de um dispositivo para um Hub IoT](quickstart-send-telemetry-node.md) anterior, ignore esta etapa.

Um dispositivo deve ser registrado no hub IoT antes de poder se conectar. Neste início rápido, você usa o Azure Cloud Shell para registrar um dispositivo simulado.

1. Execute o comando a seguir no Azure Cloud Shell para criar a identidade do dispositivo.

   **YourIoTHubName**: substitua o espaço reservado abaixo pelo nome escolhido para o hub IoT.

   **MyDevice**: esse é o nome do dispositivo que está sendo registrado. É recomendável usar **MyDevice** conforme mostrado. Se escolher um nome diferente para seu dispositivo, você também precisará usar esse nome ao longo deste artigo, bem como atualizar o nome do dispositivo nos aplicativos de exemplo antes de executá-los.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

2. Você também precisa de uma *cadeia de conexão de serviço* para permitir que aplicativos de back-end se conectem ao seu hub IoT e recuperem mensagens. O comando abaixo recupera a cadeia de conexão de serviço para o hub IoT:

    **YourIoTHubName**: substitua o espaço reservado abaixo pelo nome escolhido para o hub IoT.

    ```azurecli-interactive
    az iot hub connection-string show --policy-name service --name {YourIoTHubName} --output table
    ```

    Anote a cadeia de conexão de serviço retornada para uso posterior neste início rápido. Ela se parece com o seguinte exemplo:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="communicate-between-device-and-service-via-device-streams"></a>Comunicação entre o dispositivo e serviço por meio de fluxos de dispositivos

Nesta seção, você executará o aplicativo do lado do dispositivo e o aplicativo do lado do serviço, e estabelecerá comunicação entre os dois.

### <a name="run-the-device-side-application"></a>Executar o aplicativo do lado do dispositivo

Como mencionado anteriormente, o SDK do Node.js do Hub IoT dá suporte somente a fluxos de dispositivos no lado do serviço. Para um aplicativo do lado do dispositivo, use os programas de dispositivo complementares disponíveis nestes inícios rápidos:

* [Comunicação com aplicativos de dispositivo no C por meio de fluxos de dispositivos do Hub IoT](./quickstart-device-streams-echo-c.md)

* [Comunicação com aplicativos de dispositivo no C# por meio de fluxos de dispositivos do Hub IoT](./quickstart-device-streams-echo-csharp.md)

Certifique-se de que o aplicativo do lado do dispositivo está em execução antes de prosseguir para a próxima etapa.

### <a name="run-the-service-side-application"></a>Executar o aplicativo do lado do serviço

Neste início rápido, o aplicativo Node.js no lado do serviço tem as funcionalidades a seguir:

* Cria um fluxo de dispositivos para um dispositivo IoT.
* Lê a entrada da linha de comando e a envia para o aplicativo de dispositivo, que vai reproduzi-la novamente.

O código demonstrará o processo de inicialização de um fluxo de dispositivo, assim como usá-lo para enviar e receber dados.

Supondo que o aplicativo do lado do dispositivo está em execução, siga as etapas abaixo em uma janela de terminal local para executar o aplicativo do lado do serviço em Node.js:

* Forneça suas credenciais de serviço e a identificação do dispositivo como variáveis de ambiente.
 
   ```cmd/sh
   # In Linux
   export IOTHUB_CONNECTION_STRING="{ServiceConnectionString}"
   export STREAMING_TARGET_DEVICE="MyDevice"

   # In Windows
   SET IOTHUB_CONNECTION_STRING={ServiceConnectionString}
   SET STREAMING_TARGET_DEVICE=MyDevice
   ```
  
   Altere o espaço reservado de ServiceConnectionString para corresponder à cadeia de conexão de serviço, bem como **MyDevice** para corresponder à ID do dispositivo se você tiver atribuído a ele um nome diferente.

* Navegue até `Quickstarts/device-streams-service` na pasta de projeto descompactada e execute o exemplo usando o nó.

   ```cmd/sh
   cd azure-iot-samples-node-streams-preview/iot-hub/Quickstarts/device-streams-service
    
   # Install the preview service SDK, and other dependencies
   npm install azure-iothub@streams-preview
   npm install

   node echo.js
   ```

No final da última etapa, o programa do lado do serviço iniciará um fluxo para seu dispositivo e, após o estabelecimento, enviará um buffer de cadeia de caracteres para o serviço pelo fluxo. Neste exemplo, o programa do lado do serviço simplesmente lê o `stdin` no terminal e o envia para o dispositivo, que vai reproduzi-lo novamente. Isso demonstra a comunicação bidirecional bem-sucedida entre os dois aplicativos.

![Saída do console no lado do serviço](./media/quickstart-device-streams-echo-nodejs/service-console-output.png)

Em seguida, você pode encerrar o programa pressionando Enter novamente.

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [iot-hub-quickstarts-clean-up-resources-device-streams](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você configurou um hub IoT, registrou um dispositivo, estabeleceu um fluxo de dispositivos entre aplicativos no lado do dispositivo e do serviço e usou o fluxo para enviar dados entre os aplicativos.

Use os links abaixo para saber mais sobre fluxos de dispositivos:

> [!div class="nextstepaction"]
> [Visão geral dos fluxos de dispositivos](./iot-hub-device-streams-overview.md)
