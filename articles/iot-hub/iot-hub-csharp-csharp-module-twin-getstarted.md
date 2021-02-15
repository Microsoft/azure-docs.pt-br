---
title: Introdução c/identidade do módulo do Hub IoT do Azure & módulo d (.NET)
description: Saiba como criar a identidade do módulo e atualizar o módulo gêmeo usando SDKs de IoT para .NET.
author: chrissie926
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: menchi
ms.custom: amqp, devx-track-csharp
ms.openlocfilehash: daba400b9daadf464c4c125ad266745237e71367
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2020
ms.locfileid: "92142517"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-net"></a>Introdução à identidade e módulo do módulo do Hub IoT (.NET)

[!INCLUDE [iot-hub-selector-module-twin-getstarted](../../includes/iot-hub-selector-module-twin-getstarted.md)]

> [!NOTE]
> [As identidades do módulo e os módulos gêmeos](iot-hub-devguide-module-twins.md) são semelhantes à identidade do dispositivo e ao dispositivo gêmeo do Hub IoT do Azure, mas fornecem melhor granularidade. Embora a identidade do dispositivo do Hub IoT do Azure e o dispositivo de dispositivos de apoio habilitem o aplicativo de back-end para configurar um dispositivo e fornecer visibilidade sobre as condições do dispositivo, uma identidade de módulo e um módulo inserir fornecem esses recursos para componentes individuais de um dispositivo. Em dispositivos compatíveis com vários componentes, como dispositivos baseados no sistema operacional ou dispositivos de firmware, as identidades de módulo e o módulo gêmeos permitem a configuração isolada e as condições para cada componente.

No fim deste tutorial, você terá dois aplicativos de console .NET:

* **Createidentities**. Esse aplicativo cria uma identidade de dispositivo, uma identidade de módulo e uma chave de segurança associada para conectar seus clientes de módulo e de dispositivo.

* **UpdateModuleTwinReportedProperties**. Este aplicativo envia Propriedades relatadas do módulo atual para o Hub IoT.

> [!NOTE]
> Para obter informações sobre os SDKs de IoT do Azure que você pode usar para criar aplicativos executados em dispositivos e no back-end da solução, veja [SDKs de IoT do Azure](iot-hub-devguide-sdks.md).

## <a name="prerequisites"></a>Pré-requisitos

* Visual Studio.

* Uma conta ativa do Azure. Se você não tem uma conta, pode criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.

## <a name="create-a-hub"></a>Criar um hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="get-the-iot-hub-connection-string"></a>Obter a cadeia de conexão do hub IoT

[!INCLUDE [iot-hub-howto-module-twin-shared-access-policy-text](../../includes/iot-hub-howto-module-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

[!INCLUDE [iot-hub-get-started-create-module-identity-csharp](../../includes/iot-hub-get-started-create-module-identity-csharp.md)]

## <a name="update-the-module-twin-using-net-device-sdk"></a>Atualizar o módulo gêmeo usando o SDK do dispositivo .NET

Nesta seção, você criará um aplicativo de console .NET em seu dispositivo simulado que atualiza as propriedades relatadas do módulo gêmeo.

Antes de começar, obtenha a cadeia de conexão do módulo. Entre no [portal do Azure](https://portal.azure.com/). Navegue até o Hub e selecione **dispositivos IOT**. Localize **myFirstDevice**. Selecione **myFirstDevice** para abri-lo e, em seguida, selecione **myFirstModule** para abri-lo. Em **detalhes de identidade do módulo**, copie a **cadeia de conexão (chave primária)** quando necessário no procedimento a seguir.

   ![Detalhes do módulo do Portal do Azure](./media/iot-hub-csharp-csharp-module-twin-getstarted/module-identity-detail.png)

1. No Visual Studio, adicione um novo projeto à sua solução selecionando **arquivo**  >  **novo**  >  **projeto**. Em criar um novo projeto, selecione **aplicativo de console (.NET Framework)** e selecione **Avançar**.

1. Nomeie o projeto *UpdateModuleTwinReportedProperties*. Para **solução**, selecione **Adicionar à solução**. Verifique se a versão do .NET Framework é 4.6.1 ou posterior.

    ![Criar um projeto do Visual Studio](./media/iot-hub-csharp-csharp-module-twin-getstarted/configure-update-twins-csharp1.png)

1. Selecione **criar** para criar seu projeto.

1. No Visual Studio, abra **ferramentas**  >  **Gerenciador de pacotes NuGet**  >  **gerenciar pacotes NuGet para solução**. Selecione a guia **Procurar**.

1. Pesquise e selecione **Microsoft. Azure. Devices. Client**e, em seguida, selecione **instalar**.

    ![Captura de tela que mostra o "Microsoft. Azure. Devices. Client" selecionado e o botão "instalar" realçado.](./media/iot-hub-csharp-csharp-module-twin-getstarted/install-client-sdk.png)

1. Adicione as instruções `using` abaixo na parte superior do arquivo **Program.cs** :

    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using System.Threading.Tasks;
    using Newtonsoft.Json;
    ```

1. Adicione os seguintes campos à classe **Program** . Substitua o valor de espaço reservado pela cadeia de conexão do módulo.

    ```csharp
    private const string ModuleConnectionString = "<Your module connection string>";
    private static ModuleClient Client = null;
    static void ConnectionStatusChangeHandler(ConnectionStatus status, 
      ConnectionStatusChangeReason reason)
    {
        Console.WriteLine("Connection Status Changed to {0}; the reason is {1}", 
          status, reason);
    }
    ```

1. Adicione o seguinte método **OnDesiredPropertyChanged** à classe de **Programa**:

    ```csharp
    private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, 
      object userContext)
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

1. Adicione as seguintes linhas ao método **principal** :

    ```csharp
    static void Main(string[] args)
    {
        Microsoft.Azure.Devices.Client.TransportType transport = 
          Microsoft.Azure.Devices.Client.TransportType.Amqp;

        try
        {
            Client = 
              ModuleClient.CreateFromConnectionString(ModuleConnectionString, transport);
            Client.SetConnectionStatusChangesHandler(ConnectionStatusChangeHandler);
            Client.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertyChanged, null).Wait();

            Console.WriteLine("Retrieving twin");
            var twinTask = Client.GetTwinAsync();
            twinTask.Wait();
            var twin = twinTask.Result;
            Console.WriteLine(JsonConvert.SerializeObject(twin.Properties)); 

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
        Console.ReadLine();
        Client.CloseAsync().Wait();
    }
    ```

    Este exemplo de código mostra como recuperar o módulo gêmeo e atualizar as propriedades relatadas com o protocolo AMQP. Na versão prévia pública, só há suporte a AMQP para operações de módulo gêmeo.

1. Opcionalmente, você pode adicionar essas instruções ao método **Main** para enviar um evento ao Hub IOT do seu módulo. Coloque essas linhas abaixo do `try catch` bloco.

    ```csharp
    Byte[] bytes = new Byte[2];
    bytes[0] = 0;
    bytes[1] = 1;
    var sendEventsTask = Client.SendEventAsync(new Message(bytes));
    sendEventsTask.Wait();
    Console.WriteLine("Event sent to IoT Hub.");
    ```

## <a name="run-the-apps"></a>Executar os aplicativos

Agora você pode executar os aplicativos.

1. No Visual Studio, em **Gerenciador de soluções**, clique com o botão direito do mouse em sua solução e selecione **definir projetos de inicialização**.

1. Em **Propriedades comuns**, selecione **projeto de inicialização.**

1. Selecione **vários projetos de inicialização**e, em seguida, selecione **Iniciar** como a ação para os aplicativos e **OK** para aceitar as alterações.

1. Pressione **F5** para iniciar os aplicativos.

## <a name="next-steps"></a>Próximas etapas

Para continuar a introdução ao Hub IoT e explorar outros cenários de IoT, confira:

* [Introdução ao gerenciamento de dispositivo](iot-hub-node-node-device-management-get-started.md)

* [Introdução ao IoT Edge](../iot-edge/quickstart-linux.md)