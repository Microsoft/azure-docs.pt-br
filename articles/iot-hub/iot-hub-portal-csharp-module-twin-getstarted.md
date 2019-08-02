---
title: Introdução à identidade do módulo e ao módulo gêmeo do Hub IoT do Azure (portal e .NET) | Microsoft Docs
description: Saiba como criar a identidade do módulo e atualizar o módulo gêmeo usando o portal e .NET.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 04/26/2018
ms.openlocfilehash: ce71c64aff66ea94282a82c1f1b1ee564e74f192
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68403899"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-using-the-portal-and-net-device"></a>Introdução à identidade do módulo e ao módulo gêmeo do Hub IoT usando o portal e o dispositivo .NET

> [!NOTE]
> [As identidades do módulo e os módulos gêmeos](iot-hub-devguide-module-twins.md) são semelhantes à identidade do dispositivo e ao dispositivo gêmeo do Hub IoT do Azure, mas fornecem melhor granularidade. Enquanto a identidade do dispositivo e o dispositivo gêmeo do Hub IoT do Azure permitem que o aplicativo de back-end configure um dispositivo e forneça visibilidade às condições do dispositivo, uma identidade de módulo e módulo gêmeo fornecem esses recursos para componentes individuais de um dispositivo. Em dispositivos habilitados com vários componentes, como dispositivos baseados em sistema operacional ou dispositivos de firmware, permite condições e configuração isolada para cada componente.
>

Neste tutorial, você irá aprender:

1. Como criar uma identidade de módulo no portal.

2. Como usar um SDK de dispositivo .NET atualize o módulo de atualização do seu dispositivo.

> [!NOTE]
> Para obter informações sobre os SDKs de IoT do Azure que você pode usar para criar aplicativos executados em dispositivos e no back-end da solução, veja [SDKs de IoT do Azure](iot-hub-devguide-sdks.md).
>

Para concluir este tutorial, você precisará do seguinte:

* Visual Studio.
* Uma conta ativa do Azure. (Se você não tiver uma conta, poderá criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.)

## <a name="create-an-iot-hub"></a>Crie um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrar um novo dispositivo no hub IoT

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-module-identity-in-the-portal"></a>Criar uma identidade de módulo no portal

Em uma identidade de dispositivo, você pode criar até 20 identidades de módulo. Clique no botão **Adicionar Identidade de Módulo** na parte superior para criar sua primeira identidade de módulo chamada **myFirstModule**.

  ![Detalhes do dispositivo](./media/iot-hub-portal-csharp-module-twin-getstarted/create-module-id.png)

Salve e clique na identidade do módulo que você acabou de criar. Você pode ver os detalhes de identidade do módulo. Salve a cadeia de conexão - chave primária. Ele será usado na próxima seção, na qual você configurará seu módulo no dispositivo.

  ![Detalhes do dispositivo](./media/iot-hub-portal-csharp-module-twin-getstarted/module-details.png)

## <a name="update-the-module-twin-using-net-device-sdk"></a>Atualizar o módulo gêmeo usando o SDK do dispositivo .NET

Você criou com êxito a identidade do módulo em seu Hub IoT. Vamos tentar se comunicar com a nuvem pelo dispositivo simulado. Depois de criar uma identidade de módulo, um módulo gêmeo é implicitamente criado no Hub IoT. Nesta seção, você criará um aplicativo de console .NET em seu dispositivo simulado que atualiza as propriedades relatadas do módulo gêmeo.

## <a name="create-a-visual-studio-project"></a>Criar um projeto do Visual Studio

No Visual Studio, adicione um projeto da Área de Trabalho Clássica do Windows no Visual C# à solução existente usando o modelo de projeto **Aplicativo do Console (.NET Framework)** . Verifique se a versão do .NET Framework é 4.6.1 ou posterior. Nomeie o projeto **UpdateModuleTwinReportedProperties**.

  ![Criar um projeto do Visual Studio](./media/iot-hub-csharp-csharp-module-twin-getstarted/update-twins-csharp1.png)

## <a name="install-the-latest-azure-iot-hub-net-device-sdk"></a>Instalar o SDK do dispositivo .NET do Hub IoT do Azure

A identidade do módulo e o módulo gêmeo estão em versão prévia pública. Ele só está disponível nos SDKs do dispositivo de pré-lançamento do Hub IoT. No Visual Studio, abra ferramentas > gerenciamento de pacotes Nuget > gerenciar pacotes Nuget para solução. Pesquise Microsoft.Azure.Devices.Client. Verifique se você marcou a caixa de seleção incluir pré-lançamento. Selecione a versão mais recente e instale. Agora você tem acesso a todos os recursos do módulo.

  ![Instalar o SDK V1.16.0-preview-005 do serviço .NET do Hub IoT](./media/iot-hub-csharp-csharp-module-twin-getstarted/install-sdk.png)

## <a name="get-your-module-connection-string"></a>Obter a cadeia de conexão do módulo

Entre no [Portal do Azure](https://portal.azure.com/). Navegue até seu Hub IoT e clique em Dispositivos IoT. Localize myFirstDevice e abra-o. Você verá que myFirstModule foi criado com êxito. Copie a cadeia de conexão do módulo. Ela será necessária na próxima etapa.

  ![Detalhes do módulo do Portal do Azure](./media/iot-hub-csharp-csharp-module-twin-getstarted/module-detail.png)

## <a name="create-updatemoduletwinreportedproperties-console-app"></a>Criar um aplicativo de console UpdateModuleTwinReportedProperties

Adicione as instruções `using` abaixo na parte superior do arquivo **Program.cs** :

```csharp
using Microsoft.Azure.Devices.Client;
using Microsoft.Azure.Devices.Shared;
```

Adicione os seguintes campos à classe **Program** . Substitua o valor de espaço reservado pela cadeia de conexão do módulo.

```csharp
private const string ModuleConnectionString = "<Your module connection string>";
private static ModuleClient Client = null;
```

Adicione o seguinte método **OnDesiredPropertyChanged** à classe de **Programa**:

```csharp
private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, object userContext)
    {
        Console.WriteLine("desired property change:");
        Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));
        Console.WriteLine("Sending current time as reported property");
        TwinCollection reportedProperties = new TwinCollection
        {
            ["DateTimeLastDesiredPropertyChangeReceived"] = DateTime.Now
        };

        await Client.UpdateReportedPropertiesAsync(reportedProperties).ConfigureAwait(false);
    }
```

Por fim, adicione as seguintes linhas ao método **Principal** :

```csharp
static void Main(string[] args)
{
    Microsoft.Azure.Devices.Client.TransportType transport = Microsoft.Azure.Devices.Client.TransportType.Amqp;

    try
    {
        Client = ModuleClient.CreateFromConnectionString(ModuleConnectionString, transport);
        Client.SetConnectionStatusChangesHandler(ConnectionStatusChangeHandler);
        Client.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertyChanged, null).Wait();

        Console.WriteLine("Retrieving twin");
        var twinTask = Client.GetTwinAsync();
        twinTask.Wait();
        var twin = twinTask.Result;
        Console.WriteLine(JsonConvert.SerializeObject(twin));

        Console.WriteLine("Sending app start time as reported property");
        TwinCollection reportedProperties = new TwinCollection();
        reportedProperties["DateTimeLastAppLaunch"] = DateTime.Now;

        Client.UpdateReportedPropertiesAsync(reportedProperties);
    }
    catch (AggregateException ex)
    {
        Console.WriteLine("Error in sample: {0}", ex);
    }

    Console.WriteLine("Waiting for Events.  Press enter to exit...");
    Console.ReadKey();
    Client.CloseAsync().Wait();
}

private static void ConnectionStatusChangeHandler(ConnectionStatus status, ConnectionStatusChangeReason reason)
{
    Console.WriteLine($"Status {status} changed: {reason}");
}
```

Este exemplo de código mostra como recuperar o módulo gêmeo e atualizar as propriedades relatadas com o protocolo AMQP. Na versão prévia pública, só há suporte a AMQP para operações de módulo gêmeo.

## <a name="run-the-apps"></a>Executar os aplicativos

Agora você está pronto para executar os aplicativos. No Visual Studio, no Gerenciador de Soluções, clique com o botão direito na solução e clique em **Definir Projetos de Inicialização**. Selecione **Vários projetos de inicialização**e, em seguida, selecione **Iniciar** como ação para o aplicativo do console. Em seguida, pressione F5 para iniciar a execução de ambos os aplicativos.

## <a name="next-steps"></a>Próximas etapas

Para continuar a introdução ao Hub IoT e explorar outros cenários de IoT, confira:

* [Introdução à identidade do módulo do Hub IoT e ao módulo "cópia" usando o backup do .NET e o dispositivo .NET](iot-hub-csharp-csharp-module-twin-getstarted.md)

* [Introdução ao IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
