---
title: Início rápido do C para fluxos de dispositivos do Hub IoT do Azure para o SSH e o RDP (versão prévia) | Microsoft Docs
description: Neste início rápido, você executará um aplicativo C de exemplo que funciona como um proxy para habilitar cenários de SSH e RDP em fluxos de dispositivos do Hub IoT.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: 23a005ebb16f4786c7dde9ec5b2a7ae7c5685cb8
ms.sourcegitcommit: b49431b29a53efaa5b82f9be0f8a714f668c38ab
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68377241"
---
# <a name="quickstart-enable-ssh-and-rdp-over-an-iot-hub-device-stream-by-using-a-c-proxy-application-preview"></a>Início Rápido: Habilitar o SSH e o RDP em fluxos de dispositivos do Hub IoT usando o aplicativo proxy do C (versão prévia)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

Atualmente, o Hub IoT do Azure dá suporte a fluxos de dispositivos como uma [versão prévia do recurso](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Os [fluxos de dispositivos do Hub IoT](./iot-hub-device-streams-overview.md) permitem que aplicativos de serviço e dispositivo se comuniquem de maneira segura e simples para o firewall. Para obter uma visão geral da configuração, confira [a página Amostra de proxy local](./iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp).

Este início rápido descreve a configuração para passar o tráfego SSH por um túnel (usando a porta 22) por meio de fluxos de dispositivos. A configuração do tráfego RDP (protocolo RDP) é semelhante e exige uma simples alteração na configuração. Como os fluxos de dispositivos são independentes de protocolo e de aplicativo, você pode modificar este início rápido para acomodar outros tipos de tráfego do aplicativo.

## <a name="how-it-works"></a>Como ele funciona

A figura a seguir ilustra como os programas proxy locais do dispositivo e do serviço permitem a conectividade de ponta a ponta entre os processos do cliente SSH e do daemon SSH. Durante a versão prévia pública, o SDK do C dá suporte somente a fluxos de dispositivos no lado do dispositivo. Consequentemente, este início rápido contém instruções para executar somente o aplicativo proxy local do dispositivo. Você deve executar um dos seguintes inícios rápidos do lado do servidor:

* [SSH/RDP em fluxos de dispositivos do Hub IoT usando o proxy C#](./quickstart-device-streams-proxy-csharp.md)
* [SSH/RDP em fluxos de dispositivos do Hub IoT usando o proxy NodeJS](./quickstart-device-streams-proxy-nodejs.md).

![Configuração de proxy local](./media/quickstart-device-streams-proxy-csharp/device-stream-proxy-diagram.svg)

1. O proxy local do serviço se conecta ao hub IoT e inicia um fluxo de dispositivos para o dispositivo de destino.

2. O proxy local do dispositivo conclui o handshake de inicialização do fluxo e estabelece um túnel de streaming de ponta a ponta pelo ponto de extremidade de streaming do hub IoT até o lado do serviço.

3. O proxy local do dispositivo conecta-se ao daemon SSH que escuta na porta 22 do dispositivo. Essa configuração é definível, conforme descrito na seção "Executar o aplicativo proxy local do dispositivo".

4. O proxy local do serviço aguarda novas conexões SSH de um usuário por meio da escuta em uma porta designada que, nesse caso, é a porta 2222. Essa configuração é definível, conforme descrito na seção "Executar o aplicativo proxy local do dispositivo". Quando o usuário se conecta por meio do cliente SSH, o túnel permite que o tráfego de aplicativo SSH seja transferido entre os programas de cliente e servidor SSH.

> [!NOTE]
> O tráfego SSH enviado por um fluxo de dispositivos é passado por um túnel pelo ponto de extremidade de streaming do hub IoT, em vez de ser enviado diretamente entre o serviço e o dispositivo. Para obter mais informações, confira os [benefícios do uso de fluxos de dispositivos do Hub IoT](iot-hub-device-streams-overview.md#benefits). Além disso, a figura ilustra o daemon SSH em execução no mesmo dispositivo (ou computador) do proxy local do dispositivo. Neste início rápido, o fornecimento do endereço IP do daemon SSH permite que o daemon e o proxy local do dispositivo também sejam executados em computadores diferentes.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Atualmente, a versão prévia dos fluxos de dispositivos só é compatível com hubs IoT criados nas seguintes regiões:

  * Centro dos EUA
  * EUA Central EUAP

* Instale o [Visual Studio 2019](https://www.visualstudio.com/vs/) com a carga de trabalho [Desenvolvimento para desktop com C++](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) habilitada.
* Instale a versão mais recente do [Git](https://git-scm.com/download/).

* Execute o comando a seguir para adicionar a Extensão do Azure IoT para a CLI do Azure à instância do Cloud Shell. A Extensão de IoT adiciona comandos específicos do Hub IoT, do IoT Edge e do DPS (Serviço de Provisionamento de Dispositivos IoT) à CLI do Azure.

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```

## <a name="prepare-the-development-environment"></a>Preparar o ambiente de desenvolvimento

Neste início rápido, você usará o [SDK do dispositivo IoT do Azure para C](iot-hub-device-sdk-c-intro.md). Você preparará um ambiente de desenvolvimento usado para clonar e criar o [SDK do C do IoT do Azure](https://github.com/Azure/azure-iot-sdk-c) no GitHub. O SDK no GitHub inclui o código de exemplo usado neste início rápido.

1. Baixe o [sistema de build CMake](https://cmake.org/download/).

    É importante que os pré-requisitos do Visual Studio (Visual Studio e a carga de trabalho *Desenvolvimento para desktop com C++* ) estejam instalados no computador, *antes* de iniciar a instalação do CMake. Após a implementação dos pré-requisitos e a verificação do download, instale o sistema de build CMake.

1. Abra um prompt de comando ou o shell Bash do Git. Execute o seguinte comando para clonar o repositório do GitHub [SDK de C do IoT do Azure](https://github.com/Azure/azure-iot-sdk-c):

    ```
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive -b public-preview
    ```

    Você deve esperar que essa operação leve alguns minutos.

1. Crie um subdiretório *cmake* no diretório raiz do repositório Git, conforme mostrado no comando a seguir e acesse essa pasta.

    ```
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. Execute os comandos a seguir no diretório *cmake* para compilar uma versão do SDK específica da plataforma cliente de desenvolvimento.

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

Um dispositivo deve ser registrado no hub IoT antes de poder se conectar. Nesta seção, você usará o Azure Cloud Shell com a [extensão de IoT](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) para registrar um dispositivo simulado.

1. Para criar a identidade do dispositivo, execute o seguinte comando no Cloud Shell:

   > [!NOTE]
   > * Substitua o espaço reservado *YourIoTHubName* com o nome escolhido para o hub IoT.
   > * Use *MyDevice*, conforme mostrado. Esse é o nome fornecido para o dispositivo registrado. Se você escolher um nome diferente para seu dispositivo, use esse nome ao longo deste artigo e atualize o nome do dispositivo nos aplicativos de exemplo antes de executá-los.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyDevice
    ```

1. Para obter a *cadeia de conexão de dispositivo* referente ao dispositivo que você acabou de registrar, execute os seguintes comandos no Cloud Shell:

   > [!NOTE]
   > Substitua o espaço reservado *YourIoTHubName* com o nome escolhido para o hub IoT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyDevice --output table
    ```

    Anote a cadeia de conexão de dispositivo para uso posterior neste início rápido. Ela se parece com o seguinte exemplo:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

## <a name="ssh-to-a-device-via-device-streams"></a>SSH para um dispositivo por fluxos de dispositivos

Nesta seção, você estabelecerá um fluxo de ponta a ponta para encapsular o tráfego SSH.

### <a name="run-the-device-local-proxy-application"></a>Executar o aplicativo de proxy no local do dispositivo

1. Edite o arquivo de origem *iothub_client_c2d_streaming_proxy_sample.c* na pasta *iothub_client/samples/iothub_client_c2d_streaming_proxy_sample* e forneça a cadeia de conexão de dispositivo, IP/nome do host do dispositivo de destino e a porta SSH 22:

   ```C
   /* Paste in your iothub connection string  */
   static const char* connectionString = "[Connection string of IoT Hub]";
   static const char* localHost = "[IP/Host of your target machine]"; // Address of the local server to connect to.
   static const size_t localPort = 22; // Port of the local server to connect to.
   ```

1. Compilar o exemplo:

   ```bash
   # In Linux
   # Go to the sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_proxy_sample
   make -j
   ```

   ```cmd
   rem In Windows
   rem Go to cmake at root of repository
   cmake --build . -- /m /p:Configuration=Release
   ```

1. Execute o programa compilado no dispositivo:

   ```bash
   # In Linux
   # Go to the sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_proxy_sample
   ./iothub_client_c2d_streaming_proxy_sample
   ```

   ```cmd
   rem In Windows
   rem Go to the sample's release folder cmake\iothub_client\samples\iothub_client_c2d_streaming_proxy_sample\Release
   iothub_client_c2d_streaming_proxy_sample.exe
   ```

### <a name="run-the-service-local-proxy-application"></a>Executar o aplicativo de proxy no local do serviço

Conforme abordado na seção "Como funciona", o estabelecimento de um fluxo de ponta a ponta para passar o tráfego SSH por um túnel exige um proxy local em cada extremidade (nos lados do serviço e do dispositivo). Durante a versão prévia pública, o SDK do C do Hub IoT dá suporte somente a fluxos de dispositivos no lado do dispositivo. Para compilar e executar o proxy local do serviço, siga as instruções em um dos seguintes inícios rápidos:

   * [SSH/RDP em fluxos de dispositivos do Hub IoT usando aplicativos do proxy C#](./quickstart-device-streams-proxy-csharp.md)
   * [SSH/RDP em fluxos de dispositivos do Hub IoT usando aplicativos proxy do Node.js](./quickstart-device-streams-proxy-nodejs.md)

### <a name="establish-an-ssh-session"></a>Estabelecer uma sessão SSH

Após ambos os proxies no local do dispositivo e no local do serviço estarem em execução, use o programa de cliente SSH e se conecte ao proxy no local de serviço na porta 2222 (em vez de diretamente ao daemon SSH).

```cmd/sh
ssh <username>@localhost -p 2222
```

Neste ponto, a janela de entrada do SSH solicitará que você insira suas credenciais.

A seguinte imagem mostra a saída do console no proxy local do dispositivo, que se conecta ao daemon SSH em `IP_address:22`:

![Saída de proxy local do dispositivo](./media/quickstart-device-streams-proxy-c/device-console-output.png)

A imagem a seguir mostra a saída do console do programa cliente SSH. O cliente SSH se comunica com o daemon SSH conectando-se à porta 22, na qual o proxy local do serviço escuta:

![Saída do cliente SSH](./media/quickstart-device-streams-proxy-csharp/ssh-console-output.png)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você configurou um hub IoT, registrou um dispositivo, implantou um programa proxy local do dispositivo e do serviço para estabelecer um fluxo de dispositivos pelo Hub IoT e usou os proxies para passar o tráfego SSH por um túnel.

Para saber mais sobre fluxos de dispositivos, confira:

> [!div class="nextstepaction"]
> [Visão geral dos fluxos de dispositivos](./iot-hub-device-streams-overview.md)
