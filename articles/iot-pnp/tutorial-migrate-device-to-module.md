---
title: 'Tutorial: conectar um módulo genérico do IoT Plug and Play do Azure | Microsoft Docs'
description: 'Tutorial: usar um código de dispositivo C# de exemplo do IoT Plug and Play em um módulo genérico.'
author: ericmitt
ms.author: ericmitt
ms.date: 9/22/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: b981352dcfc5d3d2de2d773e2936feb27b6ba3d4
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106064691"
---
# <a name="tutorial-connect-an-iot-plug-and-play-module-c"></a>Tutorial: Conectar um módulo IoT Plug and Play (C#)

Este tutorial mostra como conectar um [módulo](../iot-hub/iot-hub-devguide-module-twins.md) genérico IoT Plug and Play.

Um dispositivo é IoT Plug and Play se ele publica a ID de modelo quando se conecta a um hub IoT e implementa as propriedades e os métodos descritos no modelo DTDL (Linguagem de Definição de Gêmeos Digitais) identificado pela ID do modelo. Para saber mais sobre como os dispositivos usam uma DTDL e uma ID de modelo, confira o [Guia do desenvolvedor do IoT Plug and Play](./concepts-developer-guide-device.md). Os módulos usam IDs de modelo e modelos de DTDL da mesma maneira.

Para demonstrar como implementar um módulo de IoT Plug and Play, este tutorial mostra como:

> [!div class="checklist"]
> * Adicionar um dispositivo com um módulo ao seu hub IoT.
> * Converter a amostra de dispositivo de termostato em C# em um módulo genérico.
> * Usar o SDK do serviço para interagir com o módulo.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

Para realizar este tutorial no Windows, instale o seguinte software em um ambiente Windows local:

* [Visual Studio (Community, Professional ou Enterprise)](https://visualstudio.microsoft.com/downloads/).
* [Git](https://git-scm.com/download/).

Use a ferramenta Explorer de IoT do Azure para adicionar um novo dispositivo chamado **my-module-device** ao hub IoT.

Adicione um módulo chamado **my-module** ao **my-module-device**:

1. Na ferramenta Explorer de IoT do Azure, navegue até o dispositivo **my-module-device**.

1. Selecione **Identidade do módulo** e escolha **+ Adicionar**.

1. Insira **my-module** como o nome da identidade do módulo e selecione **Salvar**.

1. Na lista de identidades de módulo, selecione **my-module**. Em seguida, copie a cadeia de conexão primária. Você usará a cadeia de conexão do módulo posteriormente neste tutorial.

1. Selecione a guia **Módulo gêmeo** e observe que não há propriedades desejadas ou relatadas:

    ```json
    {
      "deviceId": "my-module-device",
      "moduleId": "my-module",
      "etag": "AAAAAAAAAAE=",
      "deviceEtag": "NjgzMzQ1MzQ1",
      "status": "enabled",
      "statusUpdateTime": "0001-01-01T00:00:00Z",
      "connectionState": "Disconnected",
      "lastActivityTime": "0001-01-01T00:00:00Z",
      "cloudToDeviceMessageCount": 0,
      "authenticationType": "sas",
      "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
      },
      "modelId": "",
      "version": 2,
      "properties": {
        "desired": {
          "$metadata": {
            "$lastUpdated": "0001-01-01T00:00:00Z"
          },
          "$version": 1
          },
          "reported": {
            "$metadata": {
              "$lastUpdated": "0001-01-01T00:00:00Z"
          },
          "$version": 1
        }
      }
    }
    ```

## <a name="download-the-code"></a>Baixar o código

Se ainda não tiver feito isso, clone o repositório GitHub do SDK do C# do Dispositivo do Hub IoT do Azure em seu computador local:

Abra um prompt de comando em uma pasta de sua escolha. Use o seguinte comando para clonar o repositório GitHub do [Exemplos do C# de IoT do Azure](https://github.com/Azure-Samples/azure-iot-samples-csharp) nesta localização:

```cmd
git clone https://github.com/Azure-Samples/azure-iot-samples-csharp.git
```

## <a name="prepare-the-project"></a>Preparar o projeto

Para abrir e preparar o projeto de exemplo:

1. Abra o arquivo de projeto *azure-iot-sdk-csharp\iot-hub\Samples\device\PnpDeviceSamples\Thermostat\Thermostat.csproj* no Visual Studio 2019.

1. No Visual Studio, navegue até **Projeto > Propriedades do Termostato > Depurar**. Em seguida, adicione as seguintes variáveis de ambiente ao projeto:

    | Nome | Valor |
    | ---- | ----- |
    | IOTHUB_DEVICE_SECURITY_TYPE | connectionString |
    | IOTHUB_MODULE_CONNECTION_STRING | A cadeia de conexão do módulo anotada anteriormente |

    Para saber mais sobre a configuração do exemplo, confira o [leiame de exemplo](https://github.com/Azure-Samples/azure-iot-samples-csharp/blob/master/iot-hub/Samples/device/PnpDeviceSamples/readme.md).

## <a name="modify-the-code"></a>Modificar o código

Para modificar o código para funcionar como um módulo em vez de um dispositivo:

1. No Visual Studio, abra o *Parameter.cs* e modifique a linha que define a variável **PrimaryConnectionString** da seguinte maneira:

    ```csharp
    public string PrimaryConnectionString { get; set; } = Environment.GetEnvironmentVariable("IOTHUB_MODULE_CONNECTION_STRING");
    ```

1. No Visual Studio, abra o *Program.cs* e substitua as sete instâncias da classe `DeviceClient` pela classe `ModuleClient`.

    > [!TIP]
    > Use o recurso pesquisar e substituir do Visual Studio, com as opções **Diferenciar maiúsculas de minúsculas** e **Coincidir palavra inteira** habilitadas, para substituir `DeviceClient` por `ModuleClient`.

1. No Visual Studio, abra o *Thermostat.cs* e substitua ambas as instâncias da classe `DeviceClient` pela classe `ModuleClient` conforme a seguir.

1. Salve as alterações nos arquivos que você modificou.

Se você executar o código e usar o gerenciador do Azure IoT para ver o módulo gêmeo atualizado, verá o dispositivo gêmeo atualizado com a ID do modelo e a propriedade relatada do módulo:

```json
{
  "deviceId": "my-module-device",
  "moduleId": "my-mod",
  "etag": "AAAAAAAAAAE=",
  "deviceEtag": "NjgzMzQ1MzQ1",
  "status": "enabled",
  "statusUpdateTime": "0001-01-01T00:00:00Z",
  "connectionState": "Connected",
  "lastActivityTime": "0001-01-01T00:00:00Z",
  "cloudToDeviceMessageCount": 0,
  "authenticationType": "sas",
  "x509Thumbprint": {
    "primaryThumbprint": null,
    "secondaryThumbprint": null
  },
  "modelId": "dtmi:com:example:Thermostat;1",
  "version": 3,
  "properties": {
    "desired": {
      "$metadata": {
        "$lastUpdated": "0001-01-01T00:00:00Z"
      },
      "$version": 1
    },
    "reported": {
      "maxTempSinceLastReboot": 5,
      "$metadata": {
        "$lastUpdated": "2020-09-28T08:53:45.9956637Z",
        "maxTempSinceLastReboot": {
          "$lastUpdated": "2020-09-28T08:53:45.9956637Z"
        }
      },
      "$version": 2
    }
  }
}
```

## <a name="interact-with-a-device-module"></a>Interagir com um módulo de dispositivo

Os SDKs de serviço permitem recuperar a ID do modelo dos dispositivos e módulos de IoT Plug and Play conectados. Use os SDKs de serviço para definir propriedades graváveis e chamar comandos:

1. Em outra instância do Visual Studio, abra o projeto *azure-iot-sdk-csharp\iot-hub\Samples\service\PnpServiceSamples\Thermostat\Thermostat.csproj*.

1. No Visual Studio, navegue até **Projeto > Propriedades do Termostato > Depurar**. Em seguida, adicione as seguintes variáveis de ambiente ao projeto:

    | Nome | Valor |
    | ---- | ----- |
    | IOTHUB_DEVICE_ID | my-module-device |
    | IOTHUB_CONNECTION_STRING | O valor anotado quando você concluiu [Configurar seu ambiente](set-up-environment.md) |

    > [!TIP]
    > Você também pode localizar a cadeia de conexão do hub IoT na ferramenta Explorer de IoT do Azure.

1. Abra o arquivo *Program.cs* e modifique a linha que chama um comando da seguinte maneira:

    ```csharp
    CloudToDeviceMethodResult result = await s_serviceClient.InvokeDeviceMethodAsync(s_deviceId, "my-module", commandInvocation);
    ```

1. No arquivo *Program.cs*, modifique a linha que recupera o dispositivo gêmeo da seguinte maneira:

    ```csharp
    Twin twin = await s_registryManager.GetTwinAsync(s_deviceId, "my-module");
    ```

1. Confirme se o exemplo de cliente do módulo ainda está em execução e execute este exemplo de serviço. A saída do exemplo de serviço mostra a ID do modelo do dispositivo gêmeo e a chamada de comando:

    ```cmd
    [09/28/2020 10:52:55]dbug: Thermostat.Program[0]
      Initialize the service client.
    [09/28/2020 10:52:55]dbug: Thermostat.Program[0]
      Get Twin model Id and Update Twin
    [09/28/2020 10:52:59]dbug: Thermostat.Program[0]
      Model Id of this Twin is: dtmi:com:example:Thermostat;1
    [09/28/2020 10:52:59]dbug: Thermostat.Program[0]
      Invoke a command
    [09/28/2020 10:53:00]dbug: Thermostat.Program[0]
      Command getMaxMinReport invocation result status is: 200
    ```

    A saída do cliente do módulo mostra a resposta do manipulador de comandos:

    ```cmd
    [09/28/2020 10:53:00]dbug: Thermostat.ThermostatSample[0]
      Command: Received - Generating max, min and avg temperature report since 28/09/2020 10:52:55.
    [09/28/2020 10:53:00]dbug: Thermostat.ThermostatSample[0]
      Command: MaxMinReport since 28/09/2020 10:52:55: maxTemp=25.4, minTemp=25.4, avgTemp=25.4, startTime=28/09/2020 10:52:56, endTime=28/09/2020 10:52:56
    ```

## <a name="convert-to-an-iot-edge-module"></a>Converter em um módulo do IoT Edge

Para converter este exemplo para funcionar como um módulo IoT Plug and Play do IoT Edge, você deve colocar o aplicativo em contêiner. Não é necessário fazer mais nenhuma alteração no código. A variável de ambiente da cadeia de conexão é injetada pelo runtime do IoT Edge na inicialização. Para saber mais, confira [Usar o Visual Studio 2019 para desenvolver e depurar módulos para o Azure IoT Edge](../iot-edge/how-to-visual-studio-develop-module.md).

Para saber como implantar o módulo em contêineres, confira:

* [Executar o Azure IoT Edge em Máquinas Virtuais do Ubuntu](../iot-edge/how-to-install-iot-edge-ubuntuvm.md).
* [Instalar o runtime do Azure IoT Edge em sistemas Linux baseados em Debian](../iot-edge/how-to-install-iot-edge.md).

Você pode usar a ferramenta Explorer de IoT do Azure para ver:

* A ID do modelo do dispositivo do IoT Edge no módulo gêmeo.
* Telemetria enviada pelo dispositivo do IoT Edge.
* Atualizações de propriedade do módulo gêmeo do IoT Edge que disparam notificações do IoT Plug and Play.
* A reação do módulo do IoT Edge aos seus comandos do IoT Plug and Play.

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [iot-pnp-clean-resources](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a conectar um dispositivo IoT Plug and Play com módulos a um hub IoT. Para saber mais sobre os modelos de dispositivos IoT Plug and Play, confira:

> [!div class="nextstepaction"]
> [Guia do desenvolvedor de modelagem do IoT Plug and Play](./concepts-developer-guide-device.md)