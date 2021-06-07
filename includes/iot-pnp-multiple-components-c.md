---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 5a8d270ffdef1f9ae68814fa023284c68216d3ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104612466"
---
Este tutorial mostra como criar um aplicativo de exemplo de dispositivo do IoT Plug and Play com componentes, como conectá-lo ao hub IoT e como usar a ferramenta Azure IoT Explorer para ver as informações que ele envia ao hub. O aplicativo de exemplo é gravado em C e está incluído no SDK do dispositivo IoT do Azure para C. Um construtor de soluções pode usar a ferramenta do Azure IoT Explorer para compreender as funcionalidades de um dispositivo IoT Plug and Play sem a necessidade de ver nenhum código de dispositivo.

Neste tutorial, você:

> [!div class="checklist"]
> * Baixe o código de exemplo.
> * Compilará o código de exemplo.
> * Executará o aplicativo de dispositivo de exemplo e validará se ele se conecta ao seu hub IoT.
> * Examinará o código-fonte.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

Você pode concluir este tutorial no Linux ou no Windows. Os comandos do shell neste tutorial seguem a convenção do Linux para os separadores de caminho '`/`', se você está acompanhando no Windows, não deixe de trocar esses separadores por '`\`'.

Os pré-requisitos diferem por sistema operacional:

### <a name="linux"></a>Linux

Este tutorial pressupõe que você esteja usando o Ubuntu Linux. As etapas deste tutorial foram testadas com o Ubuntu 18.04.

Para concluir este tutorial no Linux, instale o seguinte software em um ambiente Linux local:

Instale o **GCC**, o **Git**, o **CMake** e todas as dependências exigidas usando o comando `apt-get`:

```sh
sudo apt-get update
sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
```

Verifique se a versão do `cmake` é superior a **2.8.12** e se a versão do **GCC** é superior a **4.4.7**.

```sh
cmake --version
gcc --version
```

### <a name="windows"></a>Windows

Para realizar este tutorial no Windows, instale o seguinte software em um ambiente Windows local:

* [Visual Studio (Community, Professional ou Enterprise)](https://visualstudio.microsoft.com/downloads/) – inclua a carga de trabalho **Desenvolvimento para Desktop com C++** ao [instalar](/cpp/build/vscpp-step-0-installation?preserve-view=true&view=vs-2019) o Visual Studio.
* [Git](https://git-scm.com/download/).
* [CMake](https://cmake.org/download/).

## <a name="download-the-code"></a>Baixar o código

Se você concluiu o [Início Rápido: Conecte um aplicativo de exemplo do dispositivo do IoT Plug and Play em execução no Linux ou no Windows ao Hub IoT (C)](../articles/iot-pnp/quickstart-connect-device.md). Você já baixou o código.

Neste tutorial, você vai preparar um ambiente de desenvolvimento que pode ser usado para clonar e compilar o SDK do C do dispositivo do Hub IoT do Azure.

Abra um prompt de comando em uma pasta de sua escolha. Execute o seguinte comando para clonar o repositório GitHub dos [SDKs de C e Bibliotecas do IoT do Azure](https://github.com/Azure/azure-iot-sdk-c) nesta localização:

```cmd/bash
git clone https://github.com/Azure/azure-iot-sdk-c.git
cd azure-iot-sdk-c
git submodule update --init
```

Essa operação deve demorar alguns minutos.

## <a name="build-and-run-the-code"></a>Criar e executar o código

Você pode criar e executar o código usando o Visual Studio ou `cmake` na linha de comando.

### <a name="use-visual-studio"></a>Usar o Virtual Studio

1. Abra a pasta raiz do repositório clonado. Depois de alguns segundos, o suporte para **CMake** no Visual Studio cria tudo de que você precisa para executar e depurar o projeto.
1. Quando o Visual Studio estiver pronto, em **Gerenciador de Soluções**, navegue até o *iothub_client/samples/pnp/pnp_temperature_controller/* .
1. Clique com o botão direito do mouse no arquivo *pnp_temperature_controller.c* e selecione **Adicionar Configuração de Depuração**. Selecione **Padrão**.
1. O Visual Studio abre o arquivo *launch.vs.json*. Edite esse arquivo conforme mostrado no snippet a seguir para definir as variáveis de ambiente necessárias. Você anotou a ID de escopo e a chave primária de registro ao concluir ["Configurar um ambiente" nos inícios rápidos e nos tutoriais do IoT Plug and Play](../articles/iot-pnp/set-up-environment.md):

    ```json
    {
      "version": "0.2.1",
      "defaults": {},
      "configurations": [
        {
          "type": "default",
          "project": "iothub_client\\samples\\pnp\\pnp_temperature_controller\\pnp_temperature_controller.c",
          "projectTarget": "",
          "name": "pnp_temperature_controller.c",
          "env": {
            "IOTHUB_DEVICE_SECURITY_TYPE": "DPS",
            "IOTHUB_DEVICE_DPS_ID_SCOPE": "<Your ID scope>",
            "IOTHUB_DEVICE_DPS_DEVICE_ID": "my-pnp-device",
            "IOTHUB_DEVICE_DPS_DEVICE_KEY": "<Your enrollment primary key>"
          }
        }
      ]
    }
    ```

1. Clique com o botão direito do mouse no arquivo *pnp_temperature_controller.c* e selecione **Definir como Item de Inicialização**.
1. Para rastrear a execução de código no Visual Studio, adicione um ponto de interrupção à função `main` no arquivo *pnp_temperature_controller.c*.
1. Agora você pode executar e depurar o exemplo no menu **Depurar**.

Agora, o dispositivo está pronto para receber comandos e atualizações de propriedade e começou a enviar dados telemétricos para o hub. Mantenha o exemplo em execução enquanto você conclui as próximas etapas.

### <a name="use-cmake-at-the-command-line"></a>Use `cmake` na linha de comando

Para compilar a amostra:

1. Crie uma pasta _cmake_ na pasta raiz do SDK do dispositivo clonado e navegue até ela:

    ```cmd/bash
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. Execute os seguintes comandos para gerar e compilar os arquivos de projeto para SDK e exemplos:

    ```cmd/bash
    cmake ..
    cmake --build .
    ```

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

Para saber mais sobre a configuração do exemplo, confira o [leiame de exemplo](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/samples/pnp/readme.md).

Para executar o exemplo:

1. Na pasta _cmake_, navegue até a pasta que contém o arquivo executável e execute-o:

    ```bash
    # Bash
    cd iothub_client/samples/pnp/pnp_temperature_controller/
    ./pnp_temperature_controller
    ```

    ```cmd
    REM Windows
    cd iothub_client\samples\pnp\pnp_temperature_controller\Debug
    pnp_temperature_controller.exe
    ```

Agora, o dispositivo está pronto para receber comandos e atualizações de propriedade e começou a enviar dados telemétricos para o hub. Mantenha o exemplo em execução enquanto você conclui as próximas etapas.

### <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>Usar o Azure IoT Explorer para validar o código

Após iniciar o exemplo de cliente do dispositivo, use a ferramenta Azure IoT Explorer para verificar se ele está funcionando.

[!INCLUDE [iot-pnp-iot-explorer.md](iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>Examine o código

Este exemplo implementa um dispositivo controlador de temperatura do IoT Plug and Play. Este exemplo implementa um modelo com [vários componentes](../articles/iot-pnp/concepts-modeling-guide.md). O [arquivo de modelo de DTDL (linguagem de definição de Gêmeos Digitais) do dispositivo de temperatura](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) define a telemetria, as propriedades e os comandos que o dispositivo implementa.

### <a name="iot-plug-and-play-helper-functions"></a>Funções auxiliares de IoT Plug and Play

Para este exemplo, o código usa algumas funções auxiliares da pasta */common*:

*pnp_device_client_ll* contém o método connect para IoT Plug and Play de IoT com o `model-id` incluído como um parâmetro: `PnP_CreateDeviceClientLLHandle`.

*pnp_protocol*: contém as funções auxiliares de IoT Plug and Play:

* `PnP_CreateReportedProperty`
* `PnP_CreateReportedPropertyWithStatus`
* `PnP_ParseCommandName`
* `PnP_CreateTelemetryMessageHandle`
* `PnP_ProcessTwinData`
* `PnP_CopyPayloadToString`
* `PnP_CreateDeviceClientLLHandle_ViaDps`

essas funções auxiliares são genéricas o bastante para você usá-las no seu próprio projeto. Este exemplo os usa nos três arquivos que correspondem a cada componente no modelo:

* *pnp_deviceinfo_component*
* *pnp_temperature_controller*
* *pnp_thermostat_component*

Por exemplo, no arquivo *pnp_deviceinfo_component*, a função `SendReportedPropertyForDeviceInformation` usa duas das funções auxiliares:

```c
if ((jsonToSend = PnP_CreateReportedProperty(componentName, propertyName, propertyValue)) == NULL)
{
    LogError("Unable to build reported property response for propertyName=%s, propertyValue=%s", propertyName, propertyValue);
}
else
{
    const char* jsonToSendStr = STRING_c_str(jsonToSend);
    size_t jsonToSendStrLen = strlen(jsonToSendStr);

    if ((iothubClientResult = IoTHubDeviceClient_LL_SendReportedState(deviceClientLL, (const unsigned char*)jsonToSendStr, jsonToSendStrLen, NULL, NULL)) != IOTHUB_CLIENT_OK)
    {
        LogError("Unable to send reported state for property=%s, error=%d", propertyName, iothubClientResult);
    }
    else
    {
        LogInfo("Sending device information property to IoTHub.  propertyName=%s, propertyValue=%s", propertyName, propertyValue);
    }
}
```

cada componente no exemplo segue esse padrão.

### <a name="code-flow"></a>fluxo de código

A função `main` inicializa a conexão e envia a ID do modelo:

```c
deviceClient = CreateDeviceClientAndAllocateComponents();
```

O código usa `PnP_CreateDeviceClientLLHandle` para se conectar ao hub IoT, definir `modelId` como uma opção e configurar o método do dispositivo e os manipuladores de retorno de chamada do dispositivo gêmeo para métodos diretos e atualizações do dispositivo gêmeo:

```c
g_pnpDeviceConfiguration.deviceMethodCallback = PnP_TempControlComponent_DeviceMethodCallback;
g_pnpDeviceConfiguration.deviceTwinCallback = PnP_TempControlComponent_DeviceTwinCallback;
g_pnpDeviceConfiguration.modelId = g_temperatureControllerModelId;
...

deviceClient = PnP_CreateDeviceClientLLHandle(&g_pnpDeviceConfiguration);
```

`&g_pnpDeviceConfiguration` também contém as informações de conexão. A variável de ambiente **IOTHUB_DEVICE_SECURITY_TYPE** determina se o exemplo usa uma cadeia de conexão ou o serviço de provisionamento de dispositivos para se conectar ao Hub IoT.

Quando o dispositivo envia uma ID de modelo, ele se torna um dispositivo IoT Plug and Play.

Com os manipuladores de retorno de chamada em vigor, o dispositivo reage às atualizações do gêmeo e a chamadas do método direto:

* Para o retorno de chamada de dispositivo gêmeo, o `PnP_TempControlComponent_DeviceTwinCallback` chama a função `PnP_ProcessTwinData` para processar os dados. `PnP_ProcessTwinData` usa o *padrão de visitante* para analisar o JSON e visita cada propriedade, chamando `PnP_TempControlComponent_ApplicationPropertyCallback` em cada elemento.

* Para o retorno de chamada de comandos, a função `PnP_TempControlComponent_DeviceMethodCallback` usa a função auxiliar para analisar os nomes de comando e componente:

    ```c
    PnP_ParseCommandName(methodName, &componentName, &componentNameSize, &pnpCommandName);
    ```

    Em seguida, a função `PnP_TempControlComponent_DeviceMethodCallback` chama o comando no componente:

    ```c
    LogInfo("Received PnP command for component=%.*s, command=%s", (int)componentNameSize, componentName, pnpCommandName);
    if (strncmp((const char*)componentName, g_thermostatComponent1Name, g_thermostatComponent1Size) == 0)
    {
        result = PnP_ThermostatComponent_ProcessCommand(g_thermostatHandle1, pnpCommandName, rootValue, response, responseSize);
    }
    else if (strncmp((const char*)componentName, g_thermostatComponent2Name, g_thermostatComponent2Size) == 0)
    {
        result = PnP_ThermostatComponent_ProcessCommand(g_thermostatHandle2, pnpCommandName, rootValue, response, responseSize);
    }
    else
    {
        LogError("PnP component=%.*s is not supported by TemperatureController", (int)componentNameSize, componentName);
        result = PNP_STATUS_NOT_FOUND;
    }
    ```

A função `main` inicializa as propriedades somente leitura enviadas ao hub IoT:

```c
PnP_TempControlComponent_ReportSerialNumber_Property(deviceClient);
PnP_DeviceInfoComponent_Report_All_Properties(g_deviceInfoComponentName, deviceClient);
PnP_TempControlComponent_Report_MaxTempSinceLastReboot_Property(g_thermostatHandle1, deviceClient);
PnP_TempControlComponent_Report_MaxTempSinceLastReboot_Property(g_thermostatHandle2, deviceClient);
```

A função `main` entra em um loop para atualizar os dados de evento e telemetria para cada componente:

```c
while (true)
{
    PnP_TempControlComponent_SendWorkingSet(deviceClient);
    PnP_ThermostatComponent_SendTelemetry(g_thermostatHandle1, deviceClient);
    PnP_ThermostatComponent_SendTelemetry(g_thermostatHandle2, deviceClient);
}
```

A função `PnP_ThermostatComponent_SendTelemetry` mostra como usar o struct `PNP_THERMOSTAT_COMPONENT`. O exemplo usa esse struct para armazenar informações sobre os dois termostatos conectados no controlador de temperatura. O código usa a função `PnP_CreateTelemetryMessageHandle` para preparar a mensagem e enviá-la:

```c
messageHandle = PnP_CreateTelemetryMessageHandle(pnpThermostatComponent->componentName, temperatureStringBuffer);
...
iothubResult = IoTHubDeviceClient_LL_SendEventAsync(deviceClientLL, messageHandle, NULL, NULL);
```

A função `main` por fim destrói os diferentes componentes e fecha a conexão com o hub.
