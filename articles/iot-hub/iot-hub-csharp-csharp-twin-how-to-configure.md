---
title: "Usar as propriedades do dispositivo gêmeo do Hub IoT do Azure (.NET/Node) | Microsoft Docs"
description: "Como usar dispositivos gêmeos do Hub IoT do Azure para configurar dispositivos. Use o SDK do dispositivo IoT do Azure para .NET para implementar um aplicativo de dispositivo simulado e o SDK do serviço IoT do Azure para .NET para implementar um aplicativo de serviço que modifique uma configuração de dispositivo usando um dispositivo gêmeo."
services: iot-hub
documentationcenter: .net
author: dsk-2015
manager: timlt
editor: 
ms.assetid: 3c627476-f982-43c9-bd17-e0698c5d236d
ms.service: iot-hub
ms.devlang: csharp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/10/2017
ms.author: dkshir
ms.translationtype: HT
ms.sourcegitcommit: 2ad539c85e01bc132a8171490a27fd807c8823a4
ms.openlocfilehash: 679cda28bf3ce9fb207fe3693a3453b355f1de15
ms.contentlocale: pt-br
ms.lasthandoff: 07/12/2017

---
# <a name="use-desired-properties-to-configure-devices"></a>Usar as propriedades desejadas para configurar os dispositivos
[!INCLUDE [iot-hub-selector-twin-how-to-configure](../../includes/iot-hub-selector-twin-how-to-configure.md)]

No fim deste tutorial, você terá dois aplicativos de console .NET:

* **SimulateDeviceConfiguration.js**, um aplicativo de dispositivo simulado que aguarda uma atualização da configuração desejada e reporta o status de um processo simulado de atualização de configuração.
* **SetDesiredConfigurationAndQuery**, um aplicativo de back-end que define a configuração desejada em um dispositivo e consulta o processo de atualização de configuração.

> [!NOTE]
> O artigo [SDKs do IoT do Azure][lnk-hub-sdks] fornece informações sobre os SDKs do IoT do Azure que você pode usar para compilar dispositivos e aplicativos back-end.
> 
> 

Para concluir este tutorial, você precisará do seguinte:

* Visual Studio 2015 ou Visual Studio 2017.
* Uma conta ativa do Azure. Se não tiver uma conta, você poderá criar uma [conta gratuita][lnk-free-trial] em apenas alguns minutos.

Caso tenha seguido o tutorial [Introdução aos dispositivos gêmeos][lnk-twin-tutorial], você já terá um Hub IoT e uma identidade de dispositivo chamada **myDeviceId**. Nesse caso, você pode pular para a seção [Criar o aplicativo do dispositivo simulado][lnk-how-to-configure-createapp].

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

<a id="#create-the-simulated-device-app"></a>
## <a name="create-the-simulated-device-app"></a>Criar o aplicativo de dispositivo simulado
Nesta seção, você cria um aplicativo de console .NET que se conecta ao seu hub como **myDeviceId**, aguarda uma atualização de configuração desejada e, em seguida, reporta atualizações sobre o processo simulado de atualização de configuração.

1. No Visual Studio, crie um projeto da Área de Trabalho Clássica do Windows no Visual C# usando o modelo de projeto **Aplicativo de Console**. Dê ao projeto o nome de **SimulateDeviceConfiguration**.
   
    ![Novo aplicativo de dispositivo Visual C# Windows clássico][img-createdeviceapp]

1. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **SimulateDeviceConfiguration** e, em seguida, clique em **Gerenciar Pacotes NuGet…**.
1. Na janela **Gerenciador de Pacotes NuGet**, selecione **Procurar** e pesquise por **microsoft.azure.devices.client**. Selecione **Instalar** para instalar o pacote **Microsoft.Azure.Devices.Client** e aceite os termos de uso. O procedimento baixa, instala e adiciona uma referência ao [pacote NuGet do SDK do Dispositivo IoT do Azure][lnk-nuget-client-sdk] e suas dependências.
   
    ![Aplicativo de cliente de janela do Gerenciador de Pacotes NuGet][img-clientnuget]
1. Adicione as instruções `using` abaixo na parte superior do arquivo **Program.cs** :
   
        using Microsoft.Azure.Devices.Client;
        using Microsoft.Azure.Devices.Shared;
        using Newtonsoft.Json;

1. Adicione os seguintes campos à classe **Program** . Substitua o valor de espaço reservado pela cadeia de conexão do dispositivo que você anotou na seção anterior.
   
        static string DeviceConnectionString = "HostName=<yourIotHubName>.azure-devices.net;DeviceId=<yourIotDeviceName>;SharedAccessKey=<yourIotDeviceAccessKey>";
        static DeviceClient Client = null;
        static TwinCollection reportedProperties = new TwinCollection();

1. Adicione o seguinte método à classe **Programa** :
 
        public static void InitClient()
        {
            try
            {
                Console.WriteLine("Connecting to hub");
                Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, TransportType.Mqtt);
            }
            catch (Exception ex)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", ex.Message);
            }
        }
    O objeto **Client** expõe todos os métodos necessários para você interagir com dispositivos gêmeos a partir do dispositivo. O código mostrado acima inicializa o objeto **Cliente** e recupera o dispositivo gêmeo para **myDeviceId**.

1. Adicione o seguinte método à classe **Programa**. Esse método define os valores iniciais de telemetria no dispositivo local e, em seguida, atualiza o dispositivo gêmeo.

        public static async void InitTelemetry()
        {
            try
            {
                Console.WriteLine("Report initial telemetry config:");
                TwinCollection telemetryConfig = new TwinCollection();
                
                telemetryConfig["configId"] = "0";
                telemetryConfig["sendFrequency"] = "24h";
                reportedProperties["telemetryConfig"] = telemetryConfig;
                Console.WriteLine(JsonConvert.SerializeObject(reportedProperties));

                await Client.UpdateReportedPropertiesAsync(reportedProperties);
            }
            catch (AggregateException ex)
            {
                foreach (Exception exception in ex.InnerExceptions)
                {
                    Console.WriteLine();
                    Console.WriteLine("Error in sample: {0}", exception);
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", ex.Message);
            }
        }

1. Adicione o seguinte método à classe **Programa**. Isso é um retorno de chamada que detectará uma alteração nas *propriedades desejadas* no dispositivo gêmeo.

        private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, object userContext)
        {
            try
            {
                Console.WriteLine("Desired property change:");
                Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));

                var currentTelemetryConfig = reportedProperties["telemetryConfig"];
                var desiredTelemetryConfig = desiredProperties["telemetryConfig"];

                if ((desiredTelemetryConfig != null) && (desiredTelemetryConfig["configId"] != currentTelemetryConfig["configId"]))
                {
                    Console.WriteLine("\nInitiating config change");
                    currentTelemetryConfig["status"] = "Pending";
                    currentTelemetryConfig["pendingConfig"] = desiredTelemetryConfig;

                    await Client.UpdateReportedPropertiesAsync(reportedProperties);

                    CompleteConfigChange();
                }
            }
            catch (AggregateException ex)
            {
                foreach (Exception exception in ex.InnerExceptions)
                {
                    Console.WriteLine();
                    Console.WriteLine("Error in sample: {0}", exception);
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", ex.Message);
            }
        }

    Esse método atualiza as propriedades relatadas no objeto do dispositivo gêmeo local com a solicitação de atualização de configuração e define o status como **Pendente**, então atualiza o dispositivo gêmeo no serviço. Depois de atualizar com êxito o dispositivo gêmeo, ele conclui a mudança de configuração chamando o método `CompleteConfigChange` descrito no próximo ponto.

1. Adicione o seguinte método à classe **Programa**. Esse método simula uma redefinição do dispositivo, em seguida, atualiza as propriedades relatadas do local configurando o status como **Êxito** e remove o elemento **pendingConfig**. Em seguida, ele atualiza o dispositivo gêmeo no serviço. 

        public static async void CompleteConfigChange()
        {
            try
            {
                var currentTelemetryConfig = reportedProperties["telemetryConfig"];

                Console.WriteLine("\nSimulating device reset");
                await Task.Delay(30000); 

                Console.WriteLine("\nCompleting config change");
                currentTelemetryConfig["configId"] = currentTelemetryConfig["pendingConfig"]["configId"];
                currentTelemetryConfig["sendFrequency"] = currentTelemetryConfig["pendingConfig"]["sendFrequency"];
                currentTelemetryConfig["status"] = "Success";
                currentTelemetryConfig["pendingConfig"] = null;

                await Client.UpdateReportedPropertiesAsync(reportedProperties);
                Console.WriteLine("Config change complete \nPress any key to exit.");
            }
            catch (AggregateException ex)
            {
                foreach (Exception exception in ex.InnerExceptions)
                {
                    Console.WriteLine();
                    Console.WriteLine("Error in sample: {0}", exception);
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", ex.Message);
            }
        }

1. Por fim, adicione as seguintes linhas ao método **Principal**:

        try
        {
            InitClient();
            InitTelemetry();

            Console.WriteLine("Wait for desired telemetry...");
            Client.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertyChanged, null).Wait();
            Console.ReadKey();
        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", exception);
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
        }

   > [!NOTE]
   > Este tutorial não simula nenhum comportamento para atualizações de configuração simultâneas. Alguns processos de atualização de configuração podem ser capazes de acomodar as alterações de configuração de destino enquanto a atualização está em execução, outros podem colocá-las em fila e outros poderiam ainda rejeitá-las com uma condição de erro. Certifique-se de considerar o comportamento desejado para o seu processo de configuração específico e adicione a lógica apropriada antes de iniciar a alteração de configuração.
   > 
   > 
1. Compile a solução e, em seguida, execute o aplicativo de dispositivo do Visual Studio clicando em **F5**. No console de saída, você deve ver as mensagens indicando que o seu dispositivo simulado está recuperando o dispositivo gêmeo, configurando a telemetria e aguardando a alteração da propriedade desejada. Mantenha o aplicativo em execução.

## <a name="create-the-service-app"></a>Criar o aplicativo do serviço
Nesta seção, você criará um aplicativo de console .NET que atualiza as *propriedades desejadas* no dispositivo gêmeo associado à **myDeviceId** com um novo objeto de configuração de telemetria. Ele então consulta os dispositivos gêmeos armazenados no Hub IoT e mostra a diferença entre as configurações desejadas e reportadas do dispositivo.

1. No Visual Studio, adicione um projeto da Área de Trabalho Clássica do Windows no Visual C# à solução atual usando o modelo de projeto **Aplicativo do Console** . Nomeie o projeto **SetDesiredConfigurationAndQuery**.
   
    ![Novo projeto da Área de Trabalho Clássica do Windows no Visual C#][img-createapp]
1. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **SetDesiredConfigurationAndQuery** e clique em **Gerenciar Pacotes NuGet...**.
1. Na janela **Gerenciador de Pacotes Nuget**, selecione **Procurar**, procure **microsoft.azure.devices**, selecione **Instalar** para instalar o pacote **Microsoft.Azure.Devices** e aceite os termos de uso. O procedimento baixa, instala e adiciona uma referência ao [pacote Nuget do SDK do Dispositivo IoT do Azure][lnk-nuget-service-sdk] e suas dependências.
   
    ![Janela do Gerenciador de Pacotes NuGet][img-servicenuget]
1. Adicione as instruções `using` abaixo na parte superior do arquivo **Program.cs** :
   
        using Microsoft.Azure.Devices;
        using System.Threading;
        using Newtonsoft.Json;
1. Adicione os seguintes campos à classe **Program** . Substitua o valor do espaço reservado pela cadeia de conexão do Hub IoT criado na seção anterior.
   
        static RegistryManager registryManager;
        static string connectionString = "{iot hub connection string}";
1. Adicione o seguinte método à classe **Programa** :
   
        static private async Task SetDesiredConfigurationAndQuery()
        {
            var twin = await registryManager.GetTwinAsync("myDeviceId");
            var patch = new {
                    properties = new {
                        desired = new {
                            telemetryConfig = new {
                                configId = Guid.NewGuid().ToString(),
                                sendFrequency = "5m"
                            }
                        }
                    }
                };
   
            await registryManager.UpdateTwinAsync(twin.DeviceId, JsonConvert.SerializeObject(patch), twin.ETag);
            Console.WriteLine("Updated desired configuration");
   
            while (true)
            {
                var query = registryManager.CreateQuery("SELECT * FROM devices WHERE deviceId = 'myDeviceId'");
                var results = await query.GetNextAsTwinAsync();
                foreach (var result in results)
                {
                    Console.WriteLine("Config report for: {0}", result.DeviceId);
                    Console.WriteLine("Desired telemetryConfig: {0}", JsonConvert.SerializeObject(result.Properties.Desired["telemetryConfig"], Formatting.Indented));
                    Console.WriteLine("Reported telemetryConfig: {0}", JsonConvert.SerializeObject(result.Properties.Reported["telemetryConfig"], Formatting.Indented));
                    Console.WriteLine();
                }
                Thread.Sleep(10000);
            }
        }
   
    O objeto **Registry** expõe todos os métodos necessários para interagir com gêmeos de dispositivo do serviço. Esse código inicializa o objeto **Registry**, recupera o dispositivo gêmeo para **myDeviceId** e então atualiza as propriedades desejadas com um novo objeto de configuração de telemetria.
    Depois disso, a cada 10 segundos, ele consulta os dispositivos gêmeos armazenados no Hub IoT e imprime as configurações de telemetria desejadas e relatadas. Consulte a [Linguagem de consulta de Hub IoT][lnk-query] para saber como gerar relatórios em todos os seus dispositivos.
   
   > [!IMPORTANT]
   > Esse aplicativo consulta o Hub IoT a cada 10 segundos para fins ilustrativos. Use consultas para gerar relatórios voltados para o usuário em vários dispositivos, não para detectar alterações. Se sua solução exigir notificações em tempo real de eventos de dispositivo, use [notificações gêmeas][lnk-twin-notifications].
   > 
   > 
1. Por fim, adicione as seguintes linhas ao método **Main** :
   
        registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        SetDesiredConfigurationAndQuery();
        Console.WriteLine("Press any key to quit.");
        Console.ReadLine();
1. No Gerenciador de Soluções, abra **Definir projetos de StartUp...** e certifique-se de que a **Ação** para o projeto **SetDesiredConfigurationAndQuery** é **Iniciar**. Compilar a solução.
1. Com o aplicativo de dispositivo **SimulateDeviceConfiguration** em execução, execute o aplicativo de serviço do Visual Studio usando **F5**. Você deve ver a configuração reportada mudar de **Pendente** para **Êxito** com a nova frequência de envio ativo de cinco minutos, em vez de 24 horas.

 ![Dispositivo configurado com êxito][img-deviceconfigured]
   
   > [!IMPORTANT]
   > Há um atraso de até um minuto entre a operação de relatório de dispositivo e o resultado da consulta. Isso é para habilitar a infraestrutura de consulta a trabalhar em escala muito alta. Para recuperar os modos de exibição consistentes de um único dispositivo gêmeo, use o método **getDeviceTwin** na classe **Registry**.
   > 
   > 

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você definiu uma configuração desejada como *propriedades desejadas* no back-end da solução e criou um aplicativo de dispositivo para detectar essa alteração e simular um processo de atualização de várias etapas, relatando seu status por meio das propriedades relatadas.

Veja os recursos a seguir para saber como:

* Enviar telemetria de dispositivos com o tutorial [Introdução ao Hub IoT][lnk-iothub-getstarted].
* Agendar ou executar operações em grandes conjuntos de dispositivos. Veja o tutorial [Schedule and broadcast jobs][lnk-schedule-jobs] (Agendar e transmitir trabalhos).
* Controlar dispositivos interativamente (como ativar uma ventoinha de um aplicativo controlado pelo usuário), com o tutorial [Uso de métodos diretos][lnk-methods-tutorial].

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-csharp-twin-how-to-configure/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-csharp-twin-how-to-configure/createnetapp.png
[img-deviceconfigured]: media/iot-hub-csharp-csharp-twin-how-to-configure/deviceconfigured.png
[img-createdeviceapp]: media/iot-hub-csharp-csharp-twin-how-to-configure/createdeviceapp.png
[img-clientnuget]: media/iot-hub-csharp-csharp-twin-how-to-configure/devicesdknuget.png
[img-deviceconfigured]: media/iot-hub-csharp-csharp-twin-how-to-configure/deviceconfigured.png


<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-nuget-client-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/1.1.0/

[lnk-query]: iot-hub-devguide-query-language.md
[lnk-twin-notifications]: iot-hub-devguide-device-twins.md#back-end-operations
[lnk-twin-tutorial]: iot-hub-csharp-csharp-twin-getstarted.md
[lnk-schedule-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-iothub-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md
[lnk-how-to-configure-createapp]: iot-hub-csharp-csharp-twin-how-to-configure.md#create-the-simulated-device-app

