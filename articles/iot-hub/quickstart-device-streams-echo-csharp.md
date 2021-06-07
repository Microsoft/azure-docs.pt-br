---
title: Guia de Início Rápido – Comunicar-se com o aplicativo do dispositivo no C# com os fluxos de dispositivos do Hub IoT do Azure
description: Neste início rápido, você executará dois aplicativos C# de exemplo que se comunicam por um fluxo de dispositivos estabelecido pelo Hub IoT.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc, devx-track-azurecli
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: 3eb65db27e5b96f4b12973154bc860a2ab3df020
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98624601"
---
# <a name="quickstart-communicate-to-a-device-application-in-c-via-iot-hub-device-streams-preview"></a>Início Rápido: Comunicar-se com um aplicativo de dispositivo em C# por meio de fluxos de dispositivos do Hub IoT (versão prévia)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

Atualmente, o Hub IoT do Azure dá suporte a fluxos de dispositivos como uma [versão prévia do recurso](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Os [fluxos de dispositivos do Hub IoT](./iot-hub-device-streams-overview.md) permitem que aplicativos de serviço e dispositivo se comuniquem de maneira segura e simples para o firewall. Este início rápido envolve dois aplicativos C# que aproveitam os fluxos de dispositivos para enviar dados bidirecionalmente (eco).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Atualmente, a versão prévia dos fluxos de dispositivos só é compatível com hubs IoT criados nas seguintes regiões:
  * Centro dos EUA
  * EUA Central EUAP
  * Norte da Europa
  * Sudeste Asiático

* Os dois aplicativos de exemplo executados neste início rápido foram escritos em C#. É necessário ter o SDK do .NET Core 2.1.0 ou posterior no computador de desenvolvimento.

    Baixe o [SDK do .NET Core para várias plataformas do .NET](https://www.microsoft.com/net/download/all).

    Verifique a versão atual do C# no computador de desenvolvimento usando o seguinte comando:

    ```
    dotnet --version
    ```

* [Baixe os exemplos de C# do Azure IoT](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) e extraia o arquivo ZIP. Você precisará dele no lado do dispositivo e no lado do serviço.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Crie um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrar um dispositivo

Um dispositivo deve ser registrado no hub IoT antes de poder se conectar. Nesta seção, você usará o Azure Cloud Shell para registrar um dispositivo simulado.

1. Para criar a identidade do dispositivo, execute o seguinte comando no Cloud Shell:

   > [!NOTE]
   > * Substitua o espaço reservado *YourIoTHubName* com o nome escolhido para o hub IoT.
   > * Para o nome do dispositivo que você está registrando, é recomendável usar *MyDevice* conforme mostrado. Se você escolher um nome diferente para seu dispositivo, use esse nome ao longo deste artigo e atualize o nome do dispositivo nos aplicativos de exemplo antes de executá-los.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

1. Para obter a *cadeia de conexão de dispositivo* referente ao dispositivo que você acabou de registrar, execute o seguinte comando no Cloud Shell:

   > [!NOTE]
   > Substitua o espaço reservado *YourIoTHubName* com o nome escolhido para o hub IoT.

    ```azurecli-interactive
    az iot hub device-identity connection-string show --hub-name {YourIoTHubName} --device-id MyDevice --output table
    ```

    Anote a cadeia de conexão de dispositivo retornada para uso posterior neste início rápido. Ela se parece com o seguinte exemplo:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

3. Você também precisa de *cadeia de conexão de serviço* do Hub IoT para habilitar o aplicativo do lado do serviço para se conectar ao Hub IoT e estabelecer um fluxo de dispositivos. O comando a seguir recupera esse valor para o Hub IoT:

   > [!NOTE]
   > Substitua o espaço reservado *YourIoTHubName* com o nome escolhido para o hub IoT.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name {YourIoTHubName} --output table
    ```

    Anote a cadeia de conexão de serviço retornada para uso posterior neste início rápido. Ela se parece com o seguinte exemplo:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="communicate-between-the-device-and-the-service-via-device-streams"></a>Comunicação entre o dispositivo e serviço por meio de fluxos de dispositivos

Nesta seção, você executará o aplicativo do lado do dispositivo e o aplicativo do lado do serviço, e estabelecerá comunicação entre os dois.

### <a name="run-the-service-side-application"></a>Executar o aplicativo do lado do serviço

Em uma janela do terminal local, navegue até o diretório `iot-hub/Quickstarts/device-streams-echo/service` na pasta descompactada do projeto. Mantenha as seguintes informações acessíveis:

| Nome do parâmetro | Valor do parâmetro |
|----------------|-----------------|
| `ServiceConnectionString` | A cadeia de conexão de serviço do seu hub IoT. |
| `MyDevice` | O identificador do dispositivo que você criou anteriormente. |

Compile e execute o código com os seguintes comandos:

```
cd ./iot-hub/Quickstarts/device-streams-echo/service/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run "{ServiceConnectionString}" "MyDevice"

# In Windows
dotnet run {ServiceConnectionString} MyDevice
```
O aplicativo aguardará até que o aplicativo do dispositivo fique disponível.

> [!NOTE]
> Um tempo limite ocorre se o aplicativo do lado do dispositivo não responde a tempo.

### <a name="run-the-device-side-application"></a>Executar o aplicativo do lado do dispositivo

Em outra janela de terminal local, navegue até o diretório `iot-hub/Quickstarts/device-streams-echo/device` na pasta descompactada do projeto. Mantenha as seguintes informações acessíveis:

| Nome do parâmetro | Valor do parâmetro |
|----------------|-----------------|
| `DeviceConnectionString` | A cadeia de conexão do dispositivo de seu Hub IoT. |

Compile e execute o código com os seguintes comandos:

```
cd ./iot-hub/Quickstarts/device-streams-echo/device/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run "{DeviceConnectionString}"

# In Windows
dotnet run {DeviceConnectionString}
```

No final da última etapa, o aplicativo do lado do serviço inicia um fluxo para o dispositivo. Depois que o fluxo é estabelecido, o aplicativo envia um buffer de cadeia de caracteres ao serviço pelo fluxo. Nesta amostra, o aplicativo no lado do serviço apenas ecoa os mesmos dados para o dispositivo, o que demonstra uma comunicação bidirecional bem-sucedida entre os dois aplicativos.

Saída do console no lado do dispositivo:

![Saída do console no lado do dispositivo](./media/quickstart-device-streams-echo-csharp/device-console-output.png)

Saída do console no lado do serviço:

![Saída do console no lado do serviço](./media/quickstart-device-streams-echo-csharp/service-console-output.png)

O tráfego enviado pelo fluxo é passado por um túnel por meio do hub IoT, em vez de ser enviado diretamente. Confira os benefícios fornecidos com detalhes no tópico [Benefícios dos fluxos de dispositivos](./iot-hub-device-streams-overview.md#benefits).

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [iot-hub-quickstarts-clean-up-resources-device-streams](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você configurou um hub IoT, registrou um dispositivo, estabeleceu um fluxo de dispositivos entre aplicativos C# nos lados do dispositivo e do serviço e usou o fluxo para enviar dados entre os aplicativos.

Para saber mais sobre fluxos de dispositivos, confira:

> [!div class="nextstepaction"]
> [Visão geral dos fluxos de dispositivos](./iot-hub-device-streams-overview.md)
