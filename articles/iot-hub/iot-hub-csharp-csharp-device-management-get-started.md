---
title: Introdução ao gerenciamento de dispositivo de Hub IoT do Azure (.NET/.NET) | Microsoft Docs
description: Como usar o gerenciamento de dispositivos do Hub IoT do Azure para iniciar uma reinicialização do dispositivo remoto. Use o SDK do dispositivo IoT do Azure para .NET para implementar um aplicativo de dispositivo simulado que inclua um método direto e o SDK do serviço do Azure IoT para .NET para implementar um aplicativo de serviço que invoque o método direto.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: robinsh
ms.custom: mqtt, devx-track-csharp
ms.openlocfilehash: 5760c574e64a3b3b4a1df12092cff44374790a90
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "89018392"
---
# <a name="get-started-with-device-management-net"></a>Introdução ao gerenciamento de dispositivos (.NET)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

Este tutorial mostra como:

* Usar o portal do Azure para criar um Hub IoT e criar uma identidade de dispositivo em seu Hub IoT.

* Crie um aplicativo de dispositivo simulado contendo um método direto que reinicia o dispositivo. Métodos diretos são invocados da nuvem.

* Criar um aplicativo de console .NET que chama um método direto de reinicialização no aplicativo de dispositivo simulado por meio do Hub IoT.

No fim deste tutorial, você terá dois aplicativos de console .NET:

* **SimulateManagedDevice**. Esse aplicativo se conecta ao seu hub IoT com a identidade do dispositivo criada anteriormente, recebe um método direto de reinicialização, simula uma reinicialização física e informa a hora da última reinicialização.

* **TriggerReboot**. Esse aplicativo chama um método direto no aplicativo de dispositivo simulado e exibe a resposta e as propriedades reportadas atualizadas.

## <a name="prerequisites"></a>Pré-requisitos

* Visual Studio.

* Uma conta ativa do Azure. Se você não tem uma conta, pode criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.

* Verifique se a porta 8883 está aberta no firewall. O exemplo de dispositivo deste artigo usa o protocolo MQTT, que se comunica pela porta 8883. Essa porta poderá ser bloqueada em alguns ambientes de rede corporativos e educacionais. Para obter mais informações e maneiras de resolver esse problema, confira [Como se conectar ao Hub IoT (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Crie um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrar um novo dispositivo no hub IoT

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>Obter a cadeia de conexão do hub IoT

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Disparar uma reinicialização remota no dispositivo usando um método direto

Nesta seção, você criará um aplicativo do console .NET, usando C#, que inicia uma reinicialização remota em um dispositivo usando um método direto. O aplicativo usa consultas de dispositivo gêmeo para descobrir o último horário de reinicialização para esse dispositivo.

1. No Visual Studio, selecione **Criar um projeto**.

1. Em **Criar um projeto**, localize e selecione o modelo de projeto **Aplicativo de Console (.NET Framework)** e selecione **Próximo**.

1. Em **Configurar seu novo projeto**, dê ao projeto o nome *TriggerReboot* e selecione .NET Framework versão 4.5.1 ou posterior. Selecione **Criar**.

    ![Novo projeto da Área de Trabalho Clássica do Windows no Visual C#](./media/iot-hub-csharp-csharp-device-management-get-started/create-trigger-reboot-configure.png)

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto **TriggerReboot** e selecione **Gerenciar Pacotes NuGet**.

1. Selecione **Procurar** para pesquisar e selecione **Microsoft.Azure.Devices**. Selecione **Instalar** para instalar o pacote **Microsoft.Azure.Devices**.

    ![Janela do Gerenciador de Pacotes NuGet](./media/iot-hub-csharp-csharp-device-management-get-started/create-trigger-reboot-nuget-devices.png)

   Esta etapa baixa, instala e adiciona uma referência ao pacote NuGet do [SDK do serviço IoT do Azure](https://www.nuget.org/packages/Microsoft.Azure.Devices/) e suas dependências.

1. Adicione as instruções `using` abaixo na parte superior do arquivo **Program.cs** :

   ```csharp
   using Microsoft.Azure.Devices;
   using Microsoft.Azure.Devices.Shared;
   ```

1. Adicione os seguintes campos à classe **Program** . Substitua o valor de espaço reservado `{iot hub connection string}` pela cadeia de conexão do Hub IoT que você copiou anteriormente em [Obter a cadeia de conexão do hub IoT](#get-the-iot-hub-connection-string).

   ```csharp
   static RegistryManager registryManager;
   static string connString = "{iot hub connection string}";
   static ServiceClient client;
   static string targetDevice = "myDeviceId";
   ```

1. Adicione o seguinte método à classe **Programa**.  Esse código obtém o dispositivo gêmeo para o dispositivo de reinicialização e gera as propriedades relatadas.

   ```csharp
   public static async Task QueryTwinRebootReported()
   {
       Twin twin = await registryManager.GetTwinAsync(targetDevice);
       Console.WriteLine(twin.Properties.Reported.ToJson());
   }
   ```

1. Adicione o seguinte método à classe **Programa**.  Esse código inicia uma reinicialização no dispositivo usando um método direto.

   ```csharp
   public static async Task StartReboot()
   {
       client = ServiceClient.CreateFromConnectionString(connString);
       CloudToDeviceMethod method = new CloudToDeviceMethod("reboot");
       method.ResponseTimeout = TimeSpan.FromSeconds(30);

       CloudToDeviceMethodResult result = await 
         client.InvokeDeviceMethodAsync(targetDevice, method);

       Console.WriteLine("Invoked firmware update on device.");
   }
   ```

1. Por fim, adicione as seguintes linhas ao método **Principal** :

   ```csharp
   registryManager = RegistryManager.CreateFromConnectionString(connString);
   StartReboot().Wait();
   QueryTwinRebootReported().Wait();
   Console.WriteLine("Press ENTER to exit.");
   Console.ReadLine();
   ```

1. Selecione **Compilar** > **Compilar Solução**.

> [!NOTE]
> Este tutorial executa somente uma única consulta para propriedades relatadas do dispositivo. No código de produção, recomendamos a sondagem para detectar alterações nas propriedades relatadas.

## <a name="create-a-simulated-device-app"></a>Criar um aplicativo de dispositivo simulado

Nesta seção, você:

* Crie um aplicativo de console do .NET que responde a um método direto chamado pela nuvem.

* Dispare uma reinicialização do dispositivo simulado.

* Use as propriedades relatadas para habilitar consultas de dispositivo gêmeo para identificar os dispositivos e quando foi sua última reinicialização.

Para criar o aplicativo de dispositivo simulado, siga estas etapas:

1. No Visual Studio, na solução TriggerReboot que você já criou, selecione **Arquivo** > **Novo** > **Projeto**. Em **Criar um projeto**, localize e selecione o modelo de projeto **Aplicativo de Console (.NET Framework)** e selecione **Próximo**.

1. Em **Configurar seu novo projeto**, dê ao projeto o nome *SimulateManagedDevice* e, para **solução**, selecione **Adicionar à solução**. Selecione **Criar**.

    ![Dar um nome ao seu projeto e adicioná-lo à solução](./media/iot-hub-csharp-csharp-device-management-get-started/configure-device-app.png)

1. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **SimulateManagedDevice** e selecione em **Gerenciar Pacotes NuGet**.

1. Selecione **Procurar** para pesquisar e selecione **Microsoft.Azure.Devices.Client**. Selecione **Instalar**.

    ![Aplicativo de cliente de janela do Gerenciador de Pacotes NuGet](./media/iot-hub-csharp-csharp-device-management-get-started/create-device-nuget-devices-client.png)

   Esta etapa baixa, instala e adiciona uma referência ao pacote NuGet do [SDK do dispositivo IoT do Azure](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) e as dependências dele.

1. Adicione as instruções `using` abaixo na parte superior do arquivo **Program.cs** :

    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    ```

1. Adicione os seguintes campos à classe **Program** . Substitua o valor de espaço reservado `{device connection string}` pela cadeia de conexão de dispositivo que você anotou anteriormente em [Registrar um novo dispositivo no hub IoT](#register-a-new-device-in-the-iot-hub).

    ```csharp
    static string DeviceConnectionString = "{device connection string}";
    static DeviceClient Client = null;
    ```

1. Adicione o seguinte para implementar o método direto no dispositivo:

   ```csharp
   static Task<MethodResponse> onReboot(MethodRequest methodRequest, object userContext)
   {
       // In a production device, you would trigger a reboot 
       //   scheduled to start after this method returns.
       // For this sample, we simulate the reboot by writing to the console
       //   and updating the reported properties.
       try
       {
           Console.WriteLine("Rebooting!");

           // Update device twin with reboot time. 
           TwinCollection reportedProperties, reboot, lastReboot;
           lastReboot = new TwinCollection();
           reboot = new TwinCollection();
           reportedProperties = new TwinCollection();
           lastReboot["lastReboot"] = DateTime.Now;
           reboot["reboot"] = lastReboot;
           reportedProperties["iothubDM"] = reboot;
           Client.UpdateReportedPropertiesAsync(reportedProperties).Wait();
       }
       catch (Exception ex)
       {
           Console.WriteLine();
           Console.WriteLine("Error in sample: {0}", ex.Message);
       }

       string result = @"{""result"":""Reboot started.""}";
       return Task.FromResult(new MethodResponse(Encoding.UTF8.GetBytes(result), 200));
   }
   ```

1. Por fim, adicione o seguinte código ao método **Principal** para abrir a conexão para o hub IoT e inicializar o ouvinte do método:

   ```csharp
   try
   {
       Console.WriteLine("Connecting to hub");
       Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, 
         TransportType.Mqtt);

       // setup callback for "reboot" method
       Client.SetMethodHandlerAsync("reboot", onReboot, null).Wait();
       Console.WriteLine("Waiting for reboot method\n Press enter to exit.");
       Console.ReadLine();

       Console.WriteLine("Exiting...");

       // as a good practice, remove the "reboot" handler
       Client.SetMethodHandlerAsync("reboot", null, null).Wait();
       Client.CloseAsync().Wait();
   }
   catch (Exception ex)
   {
       Console.WriteLine();
       Console.WriteLine("Error in sample: {0}", ex.Message);
   }
   ```

1. No Gerenciador de Soluções, clique com o botão direito do mouse na solução e selecione **Definir Projetos de Inicialização**.

1. Para **Propriedades Comuns** > **Projeto de Inicialização**, selecione **Projeto de inicialização único** e, em seguida, o projeto **SimulateManagedDevice**. Selecione **OK** para salvar suas alterações.

1. Selecione **Compilar** > **Compilar Solução**.

> [!NOTE]
> Para simplificar, este tutorial não implementa nenhuma política de repetição. No código de produção, você deve implementar políticas de repetição (como uma retirada exponencial), conforme sugerido em [Tratamento de falhas transitórias](/azure/architecture/best-practices/transient-faults).

## <a name="run-the-apps"></a>Executar os aplicativos

Agora você está pronto para executar os aplicativos.

1. Para executar o aplicativo do dispositivo .NET **SimulateManagedDevice**, no Gerenciador de Soluções, clique com o botão direito do mouse no projeto **SimulateManagedDevice**, selecione **Depurar** e, em seguida, selecione **Iniciar nova instância**. O aplicativo deve começar a escutar chamadas de método do seu Hub IoT.

1. Depois que o dispositivo estiver conectado e aguardando invocações de método, clique com o botão direito do mouse no projeto **TriggerReboot**, selecione **Depurar** e, em seguida, selecione **Iniciar nova instância**.

   Você deverá ver “Reiniciando!” escrito no console **SimulatedManagedDevice** e as propriedades relatadas do dispositivo, que incluem a hora da última reinicialização, escritas no console **TriggerReboot**.

    ![Execução de aplicativo de serviço e dispositivo](./media/iot-hub-csharp-csharp-device-management-get-started/combinedrun.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]
