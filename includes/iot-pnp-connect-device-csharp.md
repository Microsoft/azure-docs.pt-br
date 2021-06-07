---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 30ea75a2df63fa935314fc103fe1e7e092f655b2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104612096"
---
Este guia de início rápido mostra como criar um aplicativo de exemplo de dispositivo IoT Plug and Play, conectá-lo ao hub IoT e usar a ferramenta Azure IoT Explorer para exibir a telemetria que ele envia. O aplicativo de exemplo é escrito em C# e está incluído nos Exemplos de IoT do Azure para C#. Um construtor de soluções pode usar a ferramenta Azure IoT Explorer para compreender as funcionalidades de um dispositivo IoT Plug and Play sem a necessidade de ver nenhum código de dispositivo.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

Para concluir este início rápido no Windows, você precisará do seguinte software instalado no computador de desenvolvimento:

* [Visual Studio (Community, Professional ou Enterprise)](https://visualstudio.microsoft.com/downloads/).
* [Git](https://git-scm.com/download/).

## <a name="download-the-code"></a>Baixar o código

Neste início rápido, você vai preparar um ambiente de desenvolvimento que pode ser usado para clonar e criar os Exemplos de IoT do Azure para o repositório C#.

Abra um prompt de comando em uma pasta de sua escolha. Execute o seguinte comando para clonar o repositório GitHub de [Exemplos do Microsoft Azure IoT para C# (.NET)](https://github.com/Azure-Samples/azure-iot-samples-csharp) nesta localização:

```cmd
git clone  https://github.com/Azure-Samples/azure-iot-samples-csharp.git
```

## <a name="build-the-code"></a>Compilar o código

Agora você pode compilar o exemplo no Visual Studio e executá-lo no modo de depuração.

1. Abra o arquivo de projeto *azure-iot-samples-csharp\iot-hub\Samples\device\PnpDeviceSamples\Thermostat\Thermostat.csproj* no Visual Studio 2019.

1. No Visual Studio, navegue até **Projeto > Propriedades do Termostato > Depurar**. Em seguida, adicione as seguintes variáveis de ambiente ao projeto:

    | Nome | Valor |
    | ---- | ----- |
    | IOTHUB_DEVICE_SECURITY_TYPE | DPS |
    | IOTHUB_DEVICE_DPS_ENDPOINT | global.azure-devices-provisioning.net |
    | IOTHUB_DEVICE_DPS_ID_SCOPE | O valor anotado quando você concluiu [Configurar seu ambiente](../articles/iot-pnp/set-up-environment.md) |
    | IOTHUB_DEVICE_DPS_DEVICE_ID | my-pnp-device |
    | IOTHUB_DEVICE_DPS_DEVICE_KEY | O valor anotado quando você concluiu [Configurar seu ambiente](../articles/iot-pnp/set-up-environment.md) |

Agora você pode compilar o exemplo no Visual Studio e executá-lo no modo de depuração.

## <a name="run-the-device-sample"></a>Executar o exemplo de dispositivo

Para rastrear a execução de código no Visual Studio no Windows, adicione um ponto de interrupção à função `main` no arquivo program.cs.

Agora, o dispositivo está pronto para receber comandos e atualizações de propriedade e começou a enviar dados telemétricos para o hub. Mantenha o exemplo em execução enquanto você conclui as próximas etapas.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Usar o Azure IoT Explorer para validar o código

Após iniciar o exemplo de cliente do dispositivo, use a ferramenta Azure IoT Explorer para verificar se ele está funcionando.

[!INCLUDE [iot-pnp-iot-explorer.md](iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>Examine o código

Este exemplo implementa um dispositivo termostato simples do IoT Plug and Play. O modelo que esse exemplo implementa não usa os [componentes](../articles/iot-pnp/concepts-modeling-guide.md) do IoT Plug and Play. O [arquivo de modelo de DTDL (linguagem de definição de Gêmeos Digitais) do dispositivo termostato](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) define a telemetria, as propriedades e os comandos que o dispositivo implementa.

O código do dispositivo se conecta ao hub IoT usando o método `CreateFromConnectionString` padrão. O dispositivo envia a ID de modelo do modelo de DTDL que ele implementa na solicitação de conexão. Um dispositivo que envia uma ID de modelo é um dispositivo IoT Plug and Play:

```csharp
private static void InitializeDeviceClientAsync()
{
  var options = new ClientOptions
  {
    ModelId = ModelId,
  };
  s_deviceClient = DeviceClient.CreateFromConnectionString(s_deviceConnectionString, TransportType.Mqtt, options);
  s_deviceClient.SetConnectionStatusChangesHandler((status, reason) =>
  {
     s_logger.LogDebug($"Connection status change registered - status={status}, reason={reason}.");
  });
}
```

A ID do modelo é armazenada no código, conforme mostrado no seguinte snippet:

```csharp
private const string ModelId = "dtmi:com:example:Thermostat;1";
```

O código que atualiza propriedades, manipula comandos e envia telemetria é idêntico ao código de um dispositivo que não usa as convenções do IoT Plug and Play.

O exemplo usa uma biblioteca JSON para analisar objetos JSON nos conteúdos enviados do seu hub IoT:

```csharp
using Newtonsoft.Json;

...

DateTime since = JsonConvert.DeserializeObject<DateTime>(request.DataAsJson);
```
