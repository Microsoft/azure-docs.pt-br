---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 4308dd2b63b33604af83b360e5c1c0f02a3dec27
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95487771"
---
O IoT Plug and Play simplifica a IoT, permitindo que você interaja com as funcionalidades de um dispositivo sem conhecer a implementação do dispositivo subjacente. Este início rápido mostra como usar o C# para se conectar a um dispositivo IoT Plug and Play conectado à sua solução e controlá-lo.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

Para concluir este início rápido no Windows, você precisará do seguinte software instalado no computador de desenvolvimento:

* [Visual Studio (Community, Professional ou Enterprise)](https://visualstudio.microsoft.com/downloads/).
* [Git](https://git-scm.com/download/).

### <a name="clone-the-sdk-repository-with-the-sample-code"></a>Clonar o repositório do SDK com o código de exemplo

Se você concluiu o [Início Rápido: Conecte um aplicativo de exemplo de dispositivo do IoT Plug and Play em execução no Windows ao Hub IoT (C#)](../articles/iot-pnp/quickstart-connect-device.md). Você já clonou o repositório.

Clone os exemplos dos Exemplos de IoT do Azure para o repositório GitHub em C#. Abra um prompt de comando em uma pasta de sua escolha. Execute o seguinte comando para clonar o repositório GitHub [Exemplos de IoT do Microsoft Azure para .NET](https://github.com/Azure-Samples/azure-iot-samples-csharp):

```cmd
git clone https://github.com/Azure-Samples/azure-iot-samples-csharp.git
```

## <a name="run-the-sample-device"></a>Executar o dispositivo de exemplo

Neste início rápido, você usa um dispositivo de termostato de exemplo escrito em C# como o dispositivo IoT Plug and Play. Para executar o dispositivo de exemplo:

1. Abra o arquivo de projeto *azure-iot-samples-csharp\iot-hub\Samples\device\PnpDeviceSamples\Thermostat\Thermostat.csproj* no Visual Studio 2019.

1. No Visual Studio, navegue até **Projeto > Propriedades do Termostato > Depurar**. Em seguida, adicione as seguintes variáveis de ambiente ao projeto:

    | Nome | Valor |
    | ---- | ----- |
    | IOTHUB_DEVICE_SECURITY_TYPE | DPS |
    | IOTHUB_DEVICE_DPS_ENDPOINT | global.azure-devices-provisioning.net |
    | IOTHUB_DEVICE_DPS_ID_SCOPE | O valor anotado quando você concluiu [Configurar seu ambiente](../articles/iot-pnp/set-up-environment.md) |
    | IOTHUB_DEVICE_DPS_DEVICE_ID | my-pnp-device |
    | IOTHUB_DEVICE_DPS_DEVICE_KEY | O valor anotado quando você concluiu [Configurar seu ambiente](../articles/iot-pnp/set-up-environment.md) |

1. Agora você pode compilar o exemplo no Visual Studio e executá-lo no modo de depuração.

1. Você vê mensagens dizendo que o dispositivo enviou algumas informações e se reportou online. Essas mensagens indicam que o dispositivo começou a enviar dados telemétricos para o hub e agora está pronto para receber comandos e atualizações de propriedade. Não feche esta instância do Visual Studio. Você precisa dela para confirmar se o exemplo de serviço está funcionando.

## <a name="run-the-sample-solution"></a>Executar a solução de exemplo

Em [Configurar o ambiente para os inícios rápidos e os tutoriais do IoT Plug and Play](../articles/iot-pnp/set-up-environment.md), você criou duas variáveis de ambiente para configurar o exemplo a ser conectado ao hub IoT e ao dispositivo:

* **IOTHUB_CONNECTION_STRING**: a cadeia de conexão do hub IoT anotada anteriormente.
* **IOTHUB_DEVICE_ID**: `"my-pnp-device"`.

Neste guia de início rápido, você usa uma solução de IoT de exemplo em C# para interagir com o dispositivo de exemplo que acabou de configurar.

1. Em outra instância do Visual Studio, abra o projeto *azure-iot-samples-csharp\iot-hub\Samples\service\PnpServiceSamples\Thermostat\Thermostat.csproj*.

1. No Visual Studio, navegue até **Projeto > Propriedades do Termostato > Depurar**. Em seguida, adicione as seguintes variáveis de ambiente ao projeto:

    | Nome | Valor |
    | ---- | ----- |
    | IOTHUB_DEVICE_ID | my-pnp-device |
    | IOTHUB_CONNECTION_STRING | O valor anotado quando você concluiu [Configurar seu ambiente](../articles/iot-pnp/set-up-environment.md) |

1. Agora você pode compilar o exemplo no Visual Studio e executá-lo no modo de depuração.

### <a name="get-device-twin"></a>Obter dispositivo gêmeo

O seguinte snippet de código mostra como o aplicativo de serviço recupera o dispositivo gêmeo:

```C#
// Get a Twin and retrieves model Id set by Device client
Twin twin = await s_registryManager.GetTwinAsync(s_deviceId);
s_logger.LogDebug($"Model Id of this Twin is: {twin.ModelId}");
```

> [!NOTE]
> Este exemplo usa o namespace **Microsoft.Azure.Devices.Client** do **cliente do serviço Hub IoT**. Para saber mais sobre as APIs, incluindo a API de gêmeos digitais, confira o [guia de desenvolvedor do serviço](../articles/iot-pnp/concepts-developer-guide-service.md).

Esse código gera a seguinte saída:

```cmd
[09/21/2020 11:26:04]dbug: Thermostat.Program[0]
      Model Id of this Twin is: dtmi:com:example:Thermostat;1
```

O seguinte snippet de código mostra como usar um *patch* para atualizar as propriedades por meio do dispositivo gêmeo:

```C#
// Update the twin
var twinPatch = new Twin();
twinPatch.Properties.Desired[PropertyName] = PropertyValue;
await s_registryManager.UpdateTwinAsync(s_deviceId, twinPatch, twin.ETag);
```

Este código gera a seguinte saída do dispositivo quando o serviço atualiza a propriedade `targetTemperature`:

```cmd
[09/21/2020 11:26:05]dbug: Thermostat.ThermostatSample[0]
      Property: Received - { "targetTemperature": 60°C }.
[09/21/2020 11:26:05]dbug: Thermostat.ThermostatSample[0]
      Property: Update - {"targetTemperature": 60°C } is InProgress.

...

[09/21/2020 11:26:17]dbug: Thermostat.ThermostatSample[0]
      Property: Update - {"targetTemperature": 60°C } is Completed.
```

### <a name="invoke-a-command"></a>Invocar um comando

O seguinte snippet de código mostra como chamar um comando:

```csharp
private static async Task InvokeCommandAsync()
{
    var commandInvocation = new CloudToDeviceMethod(CommandName) { ResponseTimeout = TimeSpan.FromSeconds(30) };

    // Set command payload
    string componentCommandPayload = JsonConvert.SerializeObject(s_dateTime);
    commandInvocation.SetPayloadJson(componentCommandPayload);

    CloudToDeviceMethodResult result = await s_serviceClient.InvokeDeviceMethodAsync(s_deviceId, commandInvocation);

    if (result == null)
    {
        throw new Exception($"Command {CommandName} invocation returned null");
    }

    s_logger.LogDebug($"Command {CommandName} invocation result status is: {result.Status}");
}
```

Este código gera a seguinte saída do dispositivo quando o serviço chama o comando `getMaxMinReport`:

```cmd
[09/21/2020 11:26:05]dbug: Thermostat.ThermostatSample[0]
      Command: Received - Generating max, min and avg temperature report since 21/09/2020 11:25:58.
[09/21/2020 11:26:05]dbug: Thermostat.ThermostatSample[0]
      Command: MaxMinReport since 21/09/2020 11:25:58: maxTemp=32, minTemp=32, avgTemp=32, startTime=21/09/2020 11:25:59, endTime=21/09/2020 11:26:04
```
