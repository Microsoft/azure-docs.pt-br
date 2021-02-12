---
title: Como conectar um exemplo de ponte IoT Plug and Play em execução no Linux ou Windows a um hub IoT | Microsoft Docs
description: Compile e execute o IoT Plug and Play Bridge no Linux ou no Windows que se conecta a um hub IoT. Use a ferramenta Azure IoT Explorer para exibir as informações enviadas pelo dispositivo ao hub.
author: usivagna
ms.author: ugans
ms.date: 12/11/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 9bcf256b6144702254bbff4a57e5ff402abaa962
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/08/2021
ms.locfileid: "99834095"
---
# <a name="how-to-connect-an--iot-plug-and-play-bridge-sample-running-on-linux-or-windows-to-iot-hub"></a>Como conectar um exemplo de ponte IoT Plug and Play em execução no Linux ou Windows para o Hub IoT

Este artigo mostra como criar o adaptador ambiental de exemplo do IoT Plug and Play Bridge, conectá-lo ao Hub IoT e usar a ferramenta Azure IoT Explorer para exibir a telemetria que ele envia. A ponte de Plug and Play IoT é escrita em C e inclui o SDK do dispositivo IoT do Azure para C. Ao final deste tutorial, você deverá ser capaz de executar a ponte de Plug and Play IoT e ver a telemetria de relatório no Azure IoT Explorer:

:::image type="content" source="media/concepts-iot-pnp-bridge/iot-pnp-bridge-explorer-telemetry.png" alt-text="Uma captura de tela mostrando o Azure IoT Explorer com uma tabela de telemetria relatada (umidade, temperatura) da ponte de Plug and Play IOT.":::

## <a name="prerequisites"></a>Pré-requisitos

Você pode executar o exemplo no artigo no Windows ou Linux. Os comandos do Shell neste guia de instruções seguem a Convenção do Windows para separadores de caminho ' `\` ', se você estiver acompanhando o Linux, certifique-se de trocar esses separadores por ' `/` '.

### <a name="azure-iot-explorer"></a>Azure IoT Explorer

Para interagir com o dispositivo de exemplo na segunda parte deste artigo, você usa a ferramenta do **Azure IOT Explorer** . [Baixe e instale a versão mais recente do Azure IoT Explorer](./howto-use-iot-explorer.md) para o seu sistema operacional.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Execute o comando a seguir para obter a _cadeia de conexão do Hub IOT_ para o Hub. Anote essa cadeia de conexão, você a usará posteriormente neste artigo:

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

Execute o comando a seguir para obter a _cadeia de conexão de dispositivo_ do dispositivo que você adicionou ao hub. Anote essa cadeia de conexão, você a usará posteriormente neste artigo:

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```

## <a name="download-and-run-the-bridge"></a>Baixar e executar a ponte

Neste artigo, você tem duas opções para executar a ponte. Você pode:

- Baixe um executável predefinido e execute-o conforme descrito nesta seção.
- Baixe o código-fonte e, em seguida, [compile e execute a ponte](#build-and-run-the-bridge) conforme descrito na seção a seguir.

Para baixar e executar a ponte:

1. Vá para a página de [versões](https://github.com/Azure/iot-plug-and-play-bridge/releases)de plug and Play de IOT.
1. Baixe o executável predefinido para seu sistema operacional: **pnpbridge_bin.exe** para Windows ou **pnpbridge_bin** para Linux.
1. Baixe oconfig.jsde exemplo [ no](https://raw.githubusercontent.com/Azure/iot-plug-and-play-bridge/master/pnpbridge/src/adapters/samples/environmental_sensor/config.json) arquivo de configuração para o exemplo de sensor ambiental. Verifique se o arquivo de configuração está na mesma pasta que o executável.
1. Edite o *config.jsno* arquivo:

    - Adicione o `connection-string` valor que é a _cadeia de conexão do dispositivo_ anotada anteriormente.
    - Adicione o valor de `symmetric_key` chave de acesso compartilhado da cadeia de _conexão do dispositivo_.
    - Substitua o `root_interface_model_id` valor por `dtmi:com:example:PnpBridgeEnvironmentalSensor;1` .

    A primeira seção do *config.jsno* arquivo agora é semelhante ao seguinte trecho:

    ```json
    {
      "$schema": "../../../pnpbridge/src/pnpbridge_config_schema.json",
      "pnp_bridge_connection_parameters": {
        "connection_type" : "connection_string",
        "connection_string" : "HostName=youriothub.azure-devices.net;DeviceId=yourdevice;SharedAccessKey=TTrz8fR7ylHKt7DC/e/e2xocCa5VIcq5x9iQKxKFVa8=",
        "root_interface_model_id": "dtmi:com:example:PnpBridgeEnvironmentalSensor;1",
        "auth_parameters": {
            "auth_type": "symmetric_key",
            "symmetric_key": "TTrz8fR7ylHKt7DC/e/e2xocCa5VIcq5x9iQKxKFVa8="
        },
    ```

1. Execute o executável no ambiente de linha de comando. A ponte gera uma saída semelhante a:

    ```output
    c:\temp\temp-bridge>dir
     Volume in drive C is OSDisk
     Volume Serial Number is 38F7-DA4A
    
     Directory of c:\temp\temp-bridge
    
    10/12/2020  12:24    <DIR>          .
    10/12/2020  12:24    <DIR>          ..
    08/12/2020  15:26             1,216 config.json
    10/12/2020  12:21         3,617,280 pnpbridge_bin.exe
                   2 File(s)      3,618,496 bytes
                   2 Dir(s)  12,999,147,520 bytes free
    
    c:\temp\temp-bridge>pnpbridge_bin.exe
    Info:
     -- Press Ctrl+C to stop PnpBridge
    
    Info: Using default configuration location
    Info: Starting Azure PnpBridge
    Info: Pnp Bridge is running as am IoT egde device.
    Info: Pnp Bridge creation succeeded.
    Info: Connection_type is [connection_string]
    Info: Tracing is disabled
    Info: WARNING: SharedAccessKey is included in connection string. Ignoring symmetric_key in config file.
    Info: IoT Edge Device configuration initialized successfully
    Info: Building Pnp Bridge Adapter Manager, Adapters & Components
    Info: Adapter with identity environment-sensor-sample-pnp-adapter does not have any associated global parameters. Proceeding with adapter creation.
    Info: Pnp Adapter with adapter ID environment-sensor-sample-pnp-adapter has been created.
    Info: Pnp Adapter Manager created successfully.
    Info: Pnp components created successfully.
    Info: Pnp components built in model successfully.
    Info: Connected to Azure IoT Hub
    Info: Environmental Sensor: Starting Pnp Component
    Info: IoTHub client call to _SendReportedState succeeded
    Info: Environmental Sensor Adapter:: Sending device information property to IoTHub. propertyName=state, propertyValue=true
    Info: Pnp components started successfully.
    ```

## <a name="build-and-run-the-bridge"></a>Compilar e executar a ponte

Se preferir criar o executável por conta própria, você pode baixar o código-fonte e criar scripts.

Abra um prompt de comando em uma pasta de sua escolha. Execute o comando a seguir para clonar o repositório GitHub do [IoT plug and Play Bridge](https://github.com/Azure/iot-plug-and-play-bridge) neste local:

```cmd
git clone https://github.com/Azure/iot-plug-and-play-bridge.git
```

Depois de clonar o repositório, atualize os submódulos. Os submódulos incluem o SDK do IoT do Azure para C:

```cmd
cd iot-plug-and-play-bridge
git submodule update --init --recursive
```

Essa operação deve demorar alguns minutos.

> [!TIP]
> Se você tiver problemas com a falha da atualização do submódulo git clone, esse é um problema conhecido com os caminhos de arquivo do Windows. Você pode tentar o seguinte comando para resolver o problema: `git config --system core.longpaths true`

Os pré-requisitos para a criação da ponte diferem pelo sistema operacional:

### <a name="windows"></a>Windows

Para criar a ponte de Plug and Play IoT no Windows, instale o seguinte software:

* [Visual Studio (Community, Professional ou Enterprise)](https://visualstudio.microsoft.com/downloads/) – inclua a carga de trabalho **Desenvolvimento para Desktop com C++** ao [instalar](/cpp/build/vscpp-step-0-installation?preserve-view=true&view=vs-2019) o Visual Studio.
* [Git](https://git-scm.com/download/).
* [CMake](https://cmake.org/download/).

### <a name="linux"></a>Linux

Este artigo pressupõe que você esteja usando Ubuntu Linux. As etapas neste artigo foram testadas usando o Ubuntu 18, 4.

Para criar a ponte de Plug and Play IoT no Linux, instale o **gcc**, **git**, **CMake** e todas as dependências necessárias usando o `apt-get` comando:

```sh
sudo apt-get update
sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
```

Verifique se a versão do `cmake` é superior a **2.8.12** e se a versão do **GCC** é superior a **4.4.7**.

```sh
cmake --version
gcc --version
```

### <a name="build-the-iot-plug-and-play-bridge"></a>Compilar a ponte de Plug and Play IoT

Navegue até a pasta *pnpbridge* no diretório do repositório.

Para Windows, execute o seguinte em um [prompt de comando do desenvolvedor para o Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs):

```cmd
cd scripts\windows
build.cmd
```

Da mesma forma, para Linux, execute o seguinte:

```bash
cd scripts/linux
./setup.sh
./build.sh
```

>[!TIP]
>No Windows, você pode abrir a solução gerada pelo comando CMake no Visual Studio 2019. Abra o arquivo de projeto *azure_iot_pnp_bridge. sln* no diretório CMake e defina o projeto *pnpbridge_bin* como o projeto de inicialização na solução. Agora você pode compilar o exemplo no Visual Studio e executá-lo no modo de depuração.

### <a name="edit-the-configuration-file"></a>Editar o arquivo de configuração

Você pode saber mais sobre os arquivos de configuração no [documento conceitos de ponte do IoT plug and Play](concepts-iot-pnp-bridge.md).

Abra o *iot-plug-and-play-bridge\pnpbridge\src\adapters\samples\environmental_sensor\config.jsno* arquivo em um editor de texto.

- Adicione o `connection-string` valor que é a _cadeia de conexão do dispositivo_ anotada anteriormente.
- Adicione o valor de `symmetric_key` chave de acesso compartilhado da cadeia de _conexão do dispositivo_.
- Substitua o `root_interface_model_id` valor por `dtmi:com:example:PnpBridgeEnvironmentalSensor;1` .

A primeira seção do *config.jsno* arquivo agora é semelhante ao seguinte trecho:

```json
{
  "$schema": "../../../pnpbridge/src/pnpbridge_config_schema.json",
  "pnp_bridge_connection_parameters": {
    "connection_type" : "connection_string",
    "connection_string" : "HostName=youriothub.azure-devices.net;DeviceId=yourdevice;SharedAccessKey=TTrz8fR7ylHKt7DC/e/e2xocCa5VIcq5x9iQKxKFVa8=",
    "root_interface_model_id": "dtmi:com:example:PnpBridgeEnvironmentalSensor;1",
    "auth_parameters": {
        "auth_type": "symmetric_key",
        "symmetric_key": "TTrz8fR7ylHKt7DC/e/e2xocCa5VIcq5x9iQKxKFVa8="
    },
```

### <a name="run-the-iot-plug-and-play-bridge"></a>Executar a ponte de Plug and Play IoT

Inicie o exemplo de sensor ambiental de ponte de Plug and Play de IoT. O parâmetro é o caminho para o `config.json` arquivo que você editou na seção anterior:

```cmd
REM Windows
cd iot-plug-and-play-bridge\pnpbridge\cmake\pnpbridge_x86\src\pnpbridge\samples\console
Debug\pnpbridge_bin.exe ..\..\..\..\..\..\src\adapters\samples\environmental_sensor\config.json
```

A ponte gera uma saída semelhante a:

```output
c:\temp>cd iot-plug-and-play-bridge\pnpbridge\cmake\pnpbridge_x86\src\pnpbridge\samples\console

c:\temp\iot-plug-and-play-bridge\pnpbridge\cmake\pnpbridge_x86\src\pnpbridge\samples\console>Debug\pnpbridge_bin.exe ..\..\..\..\..\..\src\adapters\samples\environmental_sensor\config.json
Info:
 -- Press Ctrl+C to stop PnpBridge

Info: Using configuration from specified file path: ..\..\..\..\..\..\src\adapters\samples\environmental_sensor\config.json
Info: Starting Azure PnpBridge
Info: Pnp Bridge is running as am IoT egde device.
Info: Pnp Bridge creation succeeded.
Info: Connection_type is [connection_string]
Info: Tracing is disabled
Info: WARNING: SharedAccessKey is included in connection string. Ignoring symmetric_key in config file.
Info: IoT Edge Device configuration initialized successfully
Info: Building Pnp Bridge Adapter Manager, Adapters & Components
Info: Adapter with identity environment-sensor-sample-pnp-adapter does not have any associated global parameters. Proceeding with adapter creation.
Info: Pnp Adapter with adapter ID environment-sensor-sample-pnp-adapter has been created.
Info: Pnp Adapter Manager created successfully.
Info: Pnp components created successfully.
Info: Pnp components built in model successfully.
Info: Connected to Azure IoT Hub
Info: Environmental Sensor: Starting Pnp Component
Info: IoTHub client call to _SendReportedState succeeded
Info: Environmental Sensor Adapter:: Sending device information property to IoTHub. propertyName=state, propertyValue=true
Info: Pnp components started successfully.
Info: IoTHub client call to _SendEventAsync succeeded
Info: PnpBridge_PnpBridgeStateTelemetryCallback called, result=0, telemetry=PnpBridge configuration complete
Info: Processing property update for the device or module twin
Info: Environmental Sensor Adapter:: Successfully delivered telemetry message for <environmentalSensor>
```

Use os seguintes comandos para executar a ponte no Linux:

```bash
cd iot-plug-and-play-bridge/pnpbridge/cmake/pnpbridge_x86/src/pnpbridge/samples/console
./pnpbridge_bin ../../../../../../src/adapters/samples/environmental_sensor/config.json
```

## <a name="download-the-model-files"></a>Baixar os arquivos de modelo

Você usará o Azure IoT Explorer posteriormente para exibir o dispositivo quando ele se conectar ao Hub IoT. O Azure IoT Explorer precisa de uma cópia local do arquivo de modelo que corresponda à **ID de modelo** enviada pelo seu dispositivo. O arquivo de modelo permite que o IoT Explorer exiba a telemetria, as propriedades e os comandos que seu dispositivo implementa.

Para baixar os modelos do Azure IoT Explorer:

1. Crie uma pasta chamada *models* no computador local.
1. Salve [EnvironmentalSensor.jsna](https://raw.githubusercontent.com/Azure/iot-plug-and-play-bridge/master/pnpbridge/docs/schemas/EnvironmentalSensor.json) pasta *modelos* que você criou na etapa anterior.
1. Se você abrir esse arquivo de modelo em um editor de texto, poderá ver o modelo que define um componente com `dtmi:com:example:PnpBridgeEnvironmentalSensor;1` como sua ID. Essa é a mesma ID de modelo usada na *config.jsno* arquivo.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Usar o Azure IoT Explorer para validar o código

Depois que a ponte for iniciada, use a ferramenta do Azure IoT Explorer para verificar se ela está funcionando. Você pode ver a telemetria, as propriedades e os comandos definidos no `dtmi:com:example:PnpBridgeEnvironmentalSensor;1` modelo.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu como conectar um dispositivo de Plug and Play de IoT a um hub IoT. Para saber mais sobre como criar uma solução que interage com os dispositivos IoT Plug and Play, confira:

* [O que é o IoT Plug and Play Bridge](./concepts-iot-pnp-bridge.md)
* [Criar, implantar e estender a ponte de Plug and Play IoT](howto-build-deploy-extend-pnp-bridge.md)
* [Ponte de Plug and Play IoT no GitHub](https://github.com/Azure/iot-plug-and-play-bridge)
