---
title: Guia de Início Rápido – Comunicar-se com o aplicativo do dispositivo no C com os fluxos de dispositivos do Hub IoT do Azure
description: Neste início rápido, você executará um aplicativo do lado do dispositivo do C que se comunica com um dispositivo IoT por meio de um fluxo de dispositivos.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: mvc, devx-track-azurecli
ms.date: 08/20/2019
ms.author: robinsh
ms.openlocfilehash: fd179bf17f481ae84f768d619c6ddc0b9afab280
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102202545"
---
# <a name="quickstart-communicate-to-a-device-application-in-c-via-iot-hub-device-streams-preview"></a>Início Rápido: comunicar-se com um aplicativo de dispositivo no C por meio de fluxos de dispositivos do Hub IoT (versão prévia)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

Atualmente, o Hub IoT do Azure dá suporte a fluxos de dispositivos como uma [versão prévia do recurso](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Os [fluxos de dispositivos do Hub IoT](iot-hub-device-streams-overview.md) permitem que aplicativos de serviço e dispositivo se comuniquem de maneira segura e simples para o firewall. Durante a versão prévia pública, o SDK do C dá suporte somente a fluxos de dispositivos no lado do dispositivo. Consequentemente, este início rápido contém instruções para executar somente o aplicativo no lado do dispositivo. Para executar um aplicativo correspondente no lado do serviço, confira estes artigos:

* [Comunicação com aplicativos de dispositivo no C# por meio de fluxos de dispositivos do Hub IoT](./quickstart-device-streams-echo-csharp.md)

* [Comunicação com aplicativos de dispositivo no Node.js por meio de fluxos de dispositivos do Hub IoT](./quickstart-device-streams-echo-nodejs.md)

Neste início rápido, o aplicativo C no lado do dispositivo tem a funcionalidade a seguir:

* Estabelecer um fluxo de dispositivos para um dispositivo IoT.

* Receba dados que são enviados do aplicativo do lado do serviço e ecoe-o novamente.

O código demonstra o processo de inicialização de um fluxo de dispositivos, assim como usá-lo para enviar e receber dados.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Você precisa dos pré-requisitos a seguir:

* Instale o [Visual Studio 2019](https://www.visualstudio.com/vs/) com a carga de trabalho **Desenvolvimento para desktop com C++** habilitada.

* Instale a versão mais recente do [Git](https://git-scm.com/download/).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

Atualmente, a versão prévia dos fluxos de dispositivos só é compatível com hubs IoT criados nas seguintes regiões:

  * Centro dos EUA
  * EUA Central EUAP
  * Norte da Europa
  * Sudeste Asiático

## <a name="prepare-the-development-environment"></a>Preparar o ambiente de desenvolvimento

Neste início rápido, você usará o [SDK do dispositivo IoT do Azure para C](iot-hub-device-sdk-c-intro.md). Você preparará um ambiente de desenvolvimento usado para clonar e criar o [SDK do C do IoT do Azure](https://github.com/Azure/azure-iot-sdk-c) no GitHub. O SDK no GitHub inclui o código de exemplo usado neste início rápido.

   > [!NOTE]
   > Antes de iniciar este procedimento, certifique-se de que o Visual Studio esteja instalado com a carga de trabalho **Desenvolvimento de desktop com C++**.

1. Instale o [Sistema de build CMake](https://cmake.org/download/) conforme descrito na página de download.

1. Abra um prompt de comando ou o shell Bash do Git. Execute o seguinte comando para clonar o repositório do GitHub do [SDK de C do IoT do Azure](https://github.com/Azure/azure-iot-sdk-c):

    ```cmd/sh
    git clone -b public-preview https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Essa operação pode levar alguns minutos.

1. Crie um subdiretório *cmake* no diretório raiz do repositório git e navegue até essa pasta. Execute os seguintes comandos no diretório *azure-iot-sdk-c*:

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

1. Execute os comandos a seguir no diretório *cmake* para compilar uma versão do SDK específica da plataforma cliente de desenvolvimento.

   * No Linux:

      ```bash
      cmake ..
      make -j
      ```

   * No Windows, abra um [Prompt de Comando do Desenvolvedor para Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs). Execute o comando para sua versão do Visual Studio. Este início rápido usa o Visual Studio 2019. Esses comandos criam uma solução do Visual Studio para o dispositivo simulado no diretório *cmake*.

      ```cmd
      rem For VS2015
      cmake .. -G "Visual Studio 14 2015"

      rem Or for VS2017
      cmake .. -G "Visual Studio 15 2017"

      rem Or for VS2019
      cmake .. -G "Visual Studio 16 2019"

      rem Then build the project
      cmake --build . -- /m /p:Configuration=Release
      ```

## <a name="create-an-iot-hub"></a>Crie um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrar um dispositivo

É necessário registrar um dispositivo no hub IoT antes de conectá-lo. Nesta seção, você usará o Azure Cloud Shell com a [Extensão de IoT](/cli/azure/ext/azure-iot/iot) para registrar um dispositivo simulado.

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

## <a name="communicate-between-the-device-and-the-service-via-device-streams"></a>Comunicação entre o dispositivo e serviço por meio de fluxos de dispositivos

Nesta seção, você executará o aplicativo do lado do dispositivo e o aplicativo do lado do serviço, e estabelecerá comunicação entre os dois.

### <a name="run-the-device-side-application"></a>Executar o aplicativo do lado do dispositivo

Para executar o aplicativo do lado do dispositivo, siga estas etapas:

1. Forneça as credenciais do dispositivo, editando o arquivo de origem **iothub_client_c2d_streaming_sample.c** na pasta `iothub_client/samples/iothub_client_c2d_streaming_sample` e adicionando a cadeia de conexão do dispositivo.

   ```C
   /* Paste in your iothub connection string  */
   static const char* connectionString = "{DeviceConnectionString}";
   ```

1. Compile o código com os seguintes comandos:

   ```bash
   # In Linux
   # Go to the sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_sample
   make -j
   ```

   ```cmd
   rem In Windows
   rem Go to the cmake folder at the root of repo
   cmake --build . -- /m /p:Configuration=Release
   ```

1. Execute o programa compilado:

   ```bash
   # In Linux
   # Go to the sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_sample
   ./iothub_client_c2d_streaming_sample
   ```

   ```cmd
   rem In Windows
   rem Go to the sample's release folder cmake\iothub_client\samples\iothub_client_c2d_streaming_sample\Release
   iothub_client_c2d_streaming_sample.exe
   ```

### <a name="run-the-service-side-application"></a>Executar o aplicativo do lado do serviço

Como mencionado anteriormente, o SDK do C do Hub IoT dá suporte apenas a fluxos de dispositivos no lado do dispositivo. Para criar e executar o aplicativo complementar do lado do serviço, siga as instruções em um dos seguintes inícios rápidos:

* [Comunicação com um aplicativo de dispositivo no C# por meio de fluxos de dispositivos do Hub IoT](./quickstart-device-streams-echo-csharp.md)

* [Comunicação com um aplicativo de dispositivo no Node.js por meio de fluxos de dispositivos do Hub IoT](./quickstart-device-streams-echo-nodejs.md)

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [iot-hub-quickstarts-clean-up-resources-device-streams](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você configurou um hub IoT, registrou um dispositivo, estabeleceu um fluxo de dispositivos entre um aplicativo C no dispositivo e outro aplicativo no lado do serviço e usou o fluxo para enviar dados entre os aplicativos.

Para saber mais sobre fluxos de dispositivos, confira:

> [!div class="nextstepaction"]
> [Visão geral dos fluxos de dispositivos](./iot-hub-device-streams-overview.md)
