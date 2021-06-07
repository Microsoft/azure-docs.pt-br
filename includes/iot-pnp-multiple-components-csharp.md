---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: cd87978f9ec34e103ede869360858c5633a8c6ec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104612820"
---
Este tutorial mostra como criar um aplicativo de exemplo de dispositivo do IoT Plug and Play com componentes, como conectá-lo ao hub IoT e como usar a ferramenta Azure IoT Explorer para ver as informações que ele envia ao hub. O aplicativo de exemplo é escrito em C# e está incluído no SDK do dispositivo IoT do Azure para C#. Um construtor de soluções pode usar a ferramenta Azure IoT Explorer para compreender as funcionalidades de um dispositivo IoT Plug and Play sem a necessidade de ver nenhum código de dispositivo.

Neste tutorial, você:

> [!div class="checklist"]
> * Baixe o código de exemplo.
> * Compilará o código de exemplo.
> * Executará o aplicativo de dispositivo de exemplo e validará se ele se conecta ao seu hub IoT.
> * Examinará o código-fonte.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

Para realizar este tutorial no Windows, instale o seguinte software em um ambiente Windows local:

* [Visual Studio (Community, Professional ou Enterprise)](https://visualstudio.microsoft.com/downloads/).
* [Git](https://git-scm.com/download/).

### <a name="clone-the-sdk-repository-with-the-sample-code"></a>Clonar o repositório do SDK com o código de exemplo

Se você concluiu o [Início Rápido: Conecte um aplicativo de exemplo de dispositivo do IoT Plug and Play em execução no Windows ao Hub IoT (C#)](../articles/iot-pnp/quickstart-connect-device.md). Você já clonou o repositório.

Clone os exemplos dos Exemplos de IoT do Azure para o repositório GitHub em C#. Abra um prompt de comando em uma pasta de sua escolha. Execute o seguinte comando para clonar o repositório GitHub [Exemplos de IoT do Microsoft Azure para .NET](https://github.com/Azure-Samples/azure-iot-samples-csharp):

```cmd
git clone https://github.com/Azure-Samples/azure-iot-samples-csharp.git
```

## <a name="run-the-sample-device"></a>Executar o dispositivo de exemplo

Neste início rápido, você usará um dispositivo controlador de temperatura de exemplo escrito em C# como o dispositivo IoT Plug and Play. Para executar o dispositivo de exemplo:

1. Abra o arquivo de projeto *azure-iot-samples-csharp\iot-hub\Samples\device\PnpDeviceSamples\TemperatureController\TemperatureController.csproj* no Visual Studio 2019.

1. No Visual Studio, acesse **Projeto > Propriedades de TemperatureController > Depurar**. Em seguida, adicione as seguintes variáveis de ambiente ao projeto:

    | Nome | Valor |
    | ---- | ----- |
    | IOTHUB_DEVICE_SECURITY_TYPE | DPS |
    | IOTHUB_DEVICE_DPS_ENDPOINT | global.azure-devices-provisioning.net |
    | IOTHUB_DEVICE_DPS_ID_SCOPE | O valor anotado quando você concluiu [Configurar seu ambiente](../articles/iot-pnp/set-up-environment.md) |
    | IOTHUB_DEVICE_DPS_DEVICE_ID | my-pnp-device |
    | IOTHUB_DEVICE_DPS_DEVICE_KEY | O valor anotado quando você concluiu [Configurar seu ambiente](../articles/iot-pnp/set-up-environment.md) |


1. Agora você pode compilar o exemplo no Visual Studio e executá-lo no modo de depuração.

1. Você vê mensagens dizendo que o dispositivo enviou algumas informações e se reportou online. Essas mensagens indicam que o dispositivo começou a enviar dados telemétricos para o hub e agora está pronto para receber comandos e atualizações de propriedade. Não feche esta instância do Visual Studio. Você precisa dela para confirmar se o exemplo de serviço está funcionando.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Usar o Azure IoT Explorer para validar o código

Após iniciar o exemplo de cliente do dispositivo, use a ferramenta Azure IoT Explorer para verificar se ele está funcionando.

[!INCLUDE [iot-pnp-iot-explorer.md](iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>Examine o código

Este exemplo implementa um dispositivo controlador de temperatura do IoT Plug and Play. O modelo que este exemplo implementa usa [vários componentes](../articles/iot-pnp/concepts-modeling-guide.md). O [arquivo de modelo de DTDL (linguagem de definição de Gêmeos Digitais) do dispositivo de temperatura](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) define a telemetria, as propriedades e os comandos que o dispositivo implementa.

O código do dispositivo se conecta ao hub IoT usando o método `CreateFromConnectionString` padrão. O dispositivo envia a ID de modelo do modelo de DTDL que ele implementa na solicitação de conexão. Um dispositivo que envia uma ID de modelo é um dispositivo IoT Plug and Play:

```csharp
private static DeviceClient InitializeDeviceClient(string hostname, IAuthenticationMethod authenticationMethod)
{
    var options = new ClientOptions
    {
        ModelId = ModelId,
    };

    var deviceClient = DeviceClient.Create(hostname, authenticationMethod, TransportType.Mqtt, options);
    deviceClient.SetConnectionStatusChangesHandler((status, reason) =>
    {
        s_logger.LogDebug($"Connection status change registered - status={status}, reason={reason}.");
    });

    return deviceClient;
}
```

A ID do modelo é armazenada no código, conforme mostrado no seguinte snippet:

```csharp
private const string ModelId = "dtmi:com:example:TemperatureController;1";
```

Depois que o dispositivo se conectar ao hub IoT, o código registrará os manipuladores de comando. O comando `reboot` é definido no componente padrão. O comando `getMaxMinReport` é definido em cada um dos dois componentes do termostato:

```csharp
await _deviceClient.SetMethodHandlerAsync("reboot", HandleRebootCommandAsync, _deviceClient, cancellationToken);
await _deviceClient.SetMethodHandlerAsync("thermostat1*getMaxMinReport", HandleMaxMinReportCommandAsync, Thermostat1, cancellationToken);
await _deviceClient.SetMethodHandlerAsync("thermostat2*getMaxMinReport", HandleMaxMinReportCommandAsync, Thermostat2, cancellationToken);

```

Há manipuladores separados para as atualizações de propriedade desejadas nos dois componentes de termostato:

```csharp
_desiredPropertyUpdateCallbacks.Add(Thermostat1, TargetTemperatureUpdateCallbackAsync);
_desiredPropertyUpdateCallbacks.Add(Thermostat2, TargetTemperatureUpdateCallbackAsync);

```

O código de exemplo envia telemetria de cada componente do termostato:

```csharp
await SendTemperatureAsync(Thermostat1, cancellationToken);
await SendTemperatureAsync(Thermostat2, cancellationToken);
```

O método `SendTemperatureTelemetryAsync` usa a classe `PnpHhelper` para criar mensagens para cada componente:

```csharp
using Message msg = PnpHelper.CreateIothubMessageUtf8(telemetryName, JsonConvert.SerializeObject(currentTemperature), componentName);
```

A classe `PnpHelper` contém outros métodos de exemplo que você pode usar com um modelo de vários componentes.

Use a ferramenta do Azure IoT Explorer para ver a telemetria e as propriedades dos dois componentes do termostato:

:::image type="content" source="media/iot-pnp-multiple-components-csharp/multiple-component.png" alt-text="Vários dispositivos de componente no Azure IoT Explorer":::

Também é possível usar a ferramenta Azure IoT Explorer para chamar comandos em um dos dois componentes do termostato ou no componente padrão.
