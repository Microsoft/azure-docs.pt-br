---
title: Comunicar-se com um aplicativo de dispositivo em C por meio de fluxos de dispositivos do Hub IoT do Azure (versão prévia) | Microsoft Docs
description: Neste início rápido, você executará um aplicativo do lado do dispositivo do C que se comunica com um dispositivo IoT por meio de um fluxo de dispositivos.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: 4b6f987c68f9fe3ef95c82017b7d8be1d83083ea
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446122"
---
# <a name="quickstart-communicate-to-a-device-application-in-c-via-iot-hub-device-streams-preview"></a>Início Rápido: comunicar-se com um aplicativo de dispositivo no C por meio de fluxos de dispositivos do Hub IoT (versão prévia)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

Atualmente, o Hub IoT do Azure dá suporte a fluxos de dispositivos como uma [versão prévia do recurso](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Os [fluxos de dispositivos do Hub IoT](iot-hub-device-streams-overview.md) permitem que aplicativos de serviço e dispositivo se comuniquem de maneira segura e simples para o firewall. Durante a versão prévia pública, o SDK do C dá suporte somente a fluxos de dispositivos no lado do dispositivo. Consequentemente, este início rápido contém instruções para executar somente o aplicativo no lado do dispositivo. Para executar um aplicativo do lado do serviço complementar, confira:
 
   * [Comunicação com aplicativos de dispositivo no C# por meio de fluxos de dispositivos do Hub IoT](./quickstart-device-streams-echo-csharp.md)
   * [Comunicação com aplicativos de dispositivo no Node.js por meio de fluxos de dispositivos do Hub IoT](./quickstart-device-streams-echo-nodejs.md)

Neste início rápido, o aplicativo C no lado do dispositivo tem a funcionalidade a seguir:

* Estabelecer um fluxo de dispositivos para um dispositivo IoT.
* Receba dados que são enviados do aplicativo do lado do serviço e ecoe-o novamente.

O código demonstra o processo de inicialização de um fluxo de dispositivos, assim como usá-lo para enviar e receber dados.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Atualmente, a versão prévia dos fluxos de dispositivos só é compatível com hubs IoT criados nas seguintes regiões:

  * Centro dos EUA
  * EUA Central EUAP

* Instale o [Visual Studio 2017](https://www.visualstudio.com/vs/) com a carga de trabalho [Desenvolvimento para desktop com C++](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) habilitada.

* Instale a versão mais recente do [Git](https://git-scm.com/download/).

* Execute o comando a seguir para adicionar a Extensão do Azure IoT para a CLI do Azure à instância do Cloud Shell. A Extensão de IoT adiciona comandos específicos do Hub IoT, do IoT Edge e do DPS (Serviço de Provisionamento de Dispositivos IoT) à CLI do Azure.

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```

## <a name="prepare-the-development-environment"></a>Preparar o ambiente de desenvolvimento

Neste início rápido, você usará o [SDK do dispositivo IoT do Azure para C](iot-hub-device-sdk-c-intro.md). Você preparará um ambiente de desenvolvimento usado para clonar e criar o [SDK do C do IoT do Azure](https://github.com/Azure/azure-iot-sdk-c) no GitHub. O SDK no GitHub inclui o código de exemplo usado neste início rápido.

1. Baixe o [sistema de build CMake](https://cmake.org/download/).

    Antes de iniciar a instalação do CMake, é importante que os pré-requisitos do Visual Studio (Visual Studio e a carga de trabalho *Desenvolvimento para desktop com C++* ) estejam instalados no computador. Após a implementação dos pré-requisitos e a verificação do download, instale o sistema de build CMake.

2. Abra um prompt de comando ou o shell Bash do Git. Execute o seguinte comando para clonar o repositório do GitHub [SDK de C do IoT do Azure](https://github.com/Azure/azure-iot-sdk-c):

    ```
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive -b public-preview
    ```

    Essa operação pode levar alguns minutos.

3. Crie um subdiretório *cmake* no diretório raiz do repositório Git, conforme mostrado no comando a seguir e, em seguida, acesse essa pasta.

    ```
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

4. Execute os comandos a seguir no diretório *cmake* para compilar uma versão do SDK específica da plataforma cliente de desenvolvimento.

   * No Linux:

      ```bash
      cmake ..
      make -j
      ```

   * No Windows, execute os comandos a seguir no Prompt de Comando do Desenvolvedor para o Visual Studio 2015 ou 2017. Uma solução do Visual Studio para o dispositivo simulado será gerada no diretório *cmake*.

      ```cmd
      rem For VS2015
      cmake .. -G "Visual Studio 14 2015"

      rem Or for VS2017
      cmake .. -G "Visual Studio 15 2017"

      rem Then build the project
      cmake --build . -- /m /p:Configuration=Release
      ```

## <a name="create-an-iot-hub"></a>Crie um hub IoT

[!INCLUDE [iot-hub-include-create-hub-device-streams](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>Registrar um dispositivo

É necessário registrar um dispositivo no hub IoT antes de conectá-lo. Nesta seção, você usará o Azure Cloud Shell com a [Extensão de IoT](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) para registrar um dispositivo simulado.

1. Para criar a identidade do dispositivo, execute o seguinte comando no Cloud Shell:

   > [!NOTE]
   > * Substitua o espaço reservado *YourIoTHubName* com o nome escolhido para o hub IoT.
   > * Use *MyDevice*, conforme mostrado. Esse é o nome fornecido para o dispositivo registrado. Se você escolher um nome diferente para seu dispositivo, use esse nome ao longo deste artigo e atualize o nome do dispositivo nos aplicativos de exemplo antes de executá-los.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyDevice
    ```

2. Para obter a *cadeia de conexão de dispositivo* referente ao dispositivo que você acabou de registrar, execute os seguintes comandos no Cloud Shell:

   > [!NOTE]
   > Substitua o espaço reservado *YourIoTHubName* com o nome escolhido para o hub IoT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyDevice --output table
    ```

    Anote a cadeia de conexão de dispositivo para uso posterior neste início rápido. Ela se parece com o seguinte exemplo:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

## <a name="communicate-between-the-device-and-the-service-via-device-streams"></a>Comunicação entre o dispositivo e serviço por meio de fluxos de dispositivos

Nesta seção, você executará o aplicativo do lado do dispositivo e o aplicativo do lado do serviço, e estabelecerá comunicação entre os dois.

### <a name="run-the-device-side-application"></a>Executar o aplicativo do lado do dispositivo

Para executar o aplicativo do lado do dispositivo, faça o seguinte:

1. Forneça suas credenciais do dispositivo editando o arquivo de origem *iothub_client_c2d_streaming_sample.c* na pasta *iothub_client/samples/iothub_client_c2d_streaming_sample* e, em seguida, fornecendo a cadeia de conexão do dispositivo.

   ```C
   /* Paste in your iothub connection string  */
   static const char* connectionString = "[device connection string]";
   ```

2. Compile o código conforme segue:

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

3. Execute o programa compilado:

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

Como mencionado anteriormente, o SDK do C do Hub IoT dá suporte apenas a fluxos de dispositivos no lado do dispositivo. Para compilar e executar o aplicativo do lado do serviço, siga as instruções em um dos seguintes inícios rápidos:

* [Comunicação com um aplicativo de dispositivo no C# por meio de fluxos de dispositivos do Hub IoT](./quickstart-device-streams-echo-csharp.md)
* [Comunicação com um aplicativo de dispositivo no Node.js por meio de fluxos de dispositivos do Hub IoT](./quickstart-device-streams-echo-nodejs.md)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [iot-hub-quickstarts-clean-up-resources-device-streams](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você configurou um hub IoT, registrou um dispositivo, estabeleceu um fluxo de dispositivos entre um aplicativo C no dispositivo e outro aplicativo no lado do serviço e usou o fluxo para enviar dados entre os aplicativos.

Para saber mais sobre fluxos de dispositivos, confira:

> [!div class="nextstepaction"]
> [Visão geral dos fluxos de dispositivos](./iot-hub-device-streams-overview.md)
