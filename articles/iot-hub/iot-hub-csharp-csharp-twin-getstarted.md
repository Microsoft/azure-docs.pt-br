---
title: Introdução aos dispositivos gêmeos do Hub IoT do Azure (.NET/.NET) | Microsoft Docs
description: Como usar dispositivos gêmeos do Hub IoT do Azure para adicionar marcas e usar uma consulta do Hub IoT. Use o SDK do dispositivo IoT do Azure para .NET para implementar o aplicativo de dispositivo simulado e o SDK do serviço IoT do Azure para .NET para implementar um aplicativo de serviço que adiciona as marcas e executa a consulta do Hub IoT.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 05/15/2017
ms.author: robinsh
ms.openlocfilehash: c1b8f60fd155cf9bce0b999da7459299b6f3c7aa
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68404533"
---
# <a name="get-started-with-device-twins-netnet"></a>Introdução aos dispositivos gêmeos (.NET/.NET)
[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

Ao fim deste tutorial, você terá estes aplicativos de console .NET:

* **CreateDeviceIdentity**, um aplicativo do .NET que cria uma identidade do dispositivo e uma chave de segurança associada para conectar o aplicativo de dispositivo simulado.

* **AddTagsAndQuery**, um aplicativo de back-end .NET que adiciona marcas e consultas aos dispositivos gêmeos.

* **ReportConnectivity**, um aplicativo de dispositivo do .NET que simula um dispositivo que se conecta ao seu Hub IoT com a identidade do dispositivo criada anteriormente e reporta sua condição de conectividade.

> [!NOTE]
> O artigo [SDKs de IoT do Azure](iot-hub-devguide-sdks.md) apresenta informações sobre os SDKs de IoT do Azure que você pode usar para criar dispositivos e aplicativos de back-end.
> 

Para concluir este tutorial, você precisará do seguinte:

* Visual Studio.
* Uma conta ativa do Azure. (Se você não tiver uma conta, poderá criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.)

## <a name="create-an-iot-hub"></a>Crie um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrar um novo dispositivo no hub IoT

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>Obter a cadeia de conexão do Hub IoT

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>Criar o aplicativo do serviço

Nesta seção, você cria um aplicativo de console do .NET (usando C#) que adiciona metadados de local ao dispositivo gêmeo associado a **myDeviceId**. Ele então consulta os dispositivos gêmeos armazenados no Hub IoT selecionando os dispositivos localizados nos EUA e depois aqueles que relataram a existência de uma conexão celular.

1. No Visual Studio, adicione um projeto da Área de Trabalho Clássica do Windows no Visual C# à solução atual usando o modelo de projeto **Aplicativo do Console** . Nomeie o projeto **AddTagsAndQuery**.
   
    ![Novo projeto da Área de Trabalho Clássica do Windows no Visual C#](./media/iot-hub-csharp-csharp-twin-getstarted/createnetapp.png)

2. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **AddTagsAndQuery** e, em seguida, clique em **Gerenciar Pacotes NuGet...** .

3. Na janela **Gerenciador de Pacotes NuGet**, selecione **Procurar** e pesquise por **Microsoft.Azure.Devices**. Selecione **Instalar** para instalar o pacote **Microsoft.Azure.Devices** e aceite os termos de uso. Este procedimento baixa, instala e adiciona uma referência ao pacote NuGet do [SDK do Serviço IoT do Azure](https://www.nuget.org/packages/Microsoft.Azure.Devices/) e suas dependências.
   
    ![Janela do Gerenciador de Pacotes NuGet](./media/iot-hub-csharp-csharp-twin-getstarted/servicesdknuget.png)

4. Adicione as instruções `using` abaixo na parte superior do arquivo **Program.cs** :

    ```csharp  
    using Microsoft.Azure.Devices;
    ```

5. Adicione os seguintes campos à classe **Program** . Substitua o valor do espaço reservado pela cadeia de conexão do Hub IoT que você copiou anteriormente em [obter a cadeia de conexão do Hub IOT](#get-the-iot-hub-connection-string).

    ```csharp  
    static RegistryManager registryManager;
    static string connectionString = "{iot hub connection string}";
    ```

6. Adicione o seguinte método à classe **Programa** :

    ```csharp  
    public static async Task AddTagsAndQuery()
    {
        var twin = await registryManager.GetTwinAsync("myDeviceId");
        var patch =
            @"{
                tags: {
                    location: {
                        region: 'US',
                        plant: 'Redmond43'
                    }
                }
            }";
        await registryManager.UpdateTwinAsync(twin.DeviceId, patch, twin.ETag);
   
        var query = registryManager.CreateQuery(
          "SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'", 100);
        var twinsInRedmond43 = await query.GetNextAsTwinAsync();
        Console.WriteLine("Devices in Redmond43: {0}", 
          string.Join(", ", twinsInRedmond43.Select(t => t.DeviceId)));
   
        query = registryManager.CreateQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity.type = 'cellular'", 100);
        var twinsInRedmond43UsingCellular = await query.GetNextAsTwinAsync();
        Console.WriteLine("Devices in Redmond43 using cellular network: {0}", 
          string.Join(", ", twinsInRedmond43UsingCellular.Select(t => t.DeviceId)));
    }
    ```
   
    A classe **RegistryManager** expõe todos os métodos necessários para interagir com gêmeos de dispositivo do serviço. O código anterior inicializa primeiro o objeto **registryManager**, depois recupera o dispositivo gêmeo para **myDeviceId** e, finalmente, atualiza as marcações com as informações do local desejado.
   
    Após a atualização, ele executa duas consultas: a primeira seleciona somente os dispositivos gêmeos de dispositivos localizados na fábrica de **Redmond43**, enquanto o segundo aperfeiçoa a consulta para selecionar somente os dispositivos que também estão conectados por meio de rede celular.
   
    Observe que o código anterior, quando ele cria o objeto **query**, especifica um número máximo de documentos retornados. O objeto **query** contém uma propriedade booliana **HasMoreResults** que você pode usar para invocar os métodos **GetNextAsTwinAsync** várias vezes para recuperar todos os resultados. Um método chamado **GetNextAsJson** está disponível para os resultados que não são de dispositivos gêmeos, por exemplo, resultados de consultas de agregação.

7. Por fim, adicione as seguintes linhas ao método **Principal** :

    ```csharp  
    registryManager = RegistryManager.CreateFromConnectionString(connectionString);
    AddTagsAndQuery().Wait();
    Console.WriteLine("Press Enter to exit.");
    Console.ReadLine();
    ```

8. No Gerenciador de Soluções, abra **Definir projetos de StartUp...** e certifique-se de que a **Ação** para o projeto **AddTagsAndQuery** é **Iniciar**. Compile a solução.

9. Execute esse aplicativo clicando com o botão direito do mouse no projeto **AddTagsAndQuery** e selecionando **Depurar**, seguido de **Iniciar nova instância**. Você deve ver um dispositivo nos resultados da consulta que pergunta sobre todos os dispositivos localizados em **Redmond43**, e nenhum para a consulta que restringe os resultados para dispositivos que usam uma rede de celular.
   
    ![Resultados da consulta na janela](./media/iot-hub-csharp-csharp-twin-getstarted/addtagapp.png)

Na seção seguinte, você cria um aplicativo de dispositivo que reporta as informações de conectividade e altera o resultado da consulta na seção anterior.

## <a name="create-the-device-app"></a>Criar o aplicativo do dispositivo

Nesta seção, você cria um aplicativo de console do .NET que se conecta ao seu hub como **myDeviceId** e depois atualiza suas propriedades reportadas para conter as informações indicando que ele está conectado usando uma rede celular.

1. No Visual Studio, adicione um projeto da Área de Trabalho Clássica do Windows no Visual C# à solução atual usando o modelo de projeto **Aplicativo do Console** . Nomeie o projeto como **ReportConnectivity**.
   
    ![Novo aplicativo de dispositivo Visual C# Windows clássico](./media/iot-hub-csharp-csharp-twin-getstarted/createdeviceapp.png)
    
2. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **ReportConnectivity** e clique em **Gerenciar Pacotes NuGet...** .

3. Na janela **Gerenciador de Pacotes NuGet**, selecione **Procurar** e pesquise por **Microsoft.Azure.Devices.Client**. Selecione **Instalar** para instalar o pacote **Microsoft.Azure.Devices.Client** e aceite os termos de uso. O procedimento baixa, instala e adiciona uma referência ao pacote NuGet do [SDK do Dispositivo IoT do Azure](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) e suas dependências.
   
    ![Aplicativo de cliente de janela do Gerenciador de Pacotes NuGet](./media/iot-hub-csharp-csharp-twin-getstarted/clientsdknuget.png)

4. Adicione as instruções `using` abaixo na parte superior do arquivo **Program.cs** :

    ```csharp  
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using Newtonsoft.Json;
    ```

5. Adicione os seguintes campos à classe **Program** . Substitua o valor de espaço reservado pela cadeia de conexão do dispositivo que você anotou na seção anterior.

    ```csharp  
    static string DeviceConnectionString = "HostName=<yourIotHubName>.azure-devices.net;
      DeviceId=<yourIotDeviceName>;SharedAccessKey=<yourIotDeviceAccessKey>";
    static DeviceClient Client = null;
    ```

6. Adicione o seguinte método à classe **Programa** :

    ```csharp
    public static async void InitClient()
    {
        try
        {
            Console.WriteLine("Connecting to hub");
            Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, 
              TransportType.Mqtt);
            Console.WriteLine("Retrieving twin");
            await Client.GetTwinAsync();
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
        }
    }
    ```

    O objeto **Client** expõe todos os métodos necessários para você interagir com dispositivos gêmeos a partir do dispositivo. O código mostrado acima inicializa o objeto **Cliente** e recupera o dispositivo gêmeo para **myDeviceId**.

7. Adicione o seguinte método à classe **Programa** :

    ```csharp  
    public static async void ReportConnectivity()
    {
        try
        {
            Console.WriteLine("Sending connectivity data as reported property");
            
            TwinCollection reportedProperties, connectivity;
            reportedProperties = new TwinCollection();
            connectivity = new TwinCollection();
            connectivity["type"] = "cellular";
            reportedProperties["connectivity"] = connectivity;
            await Client.UpdateReportedPropertiesAsync(reportedProperties);
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
        }
    }
    ```

   O código acima atualiza a propriedade relatada de **myDeviceId** com as informações de conectividade.

8. Por fim, adicione as seguintes linhas ao método **Principal** :

    ```csharp
    try
    {
        InitClient();
        ReportConnectivity();
    }
    catch (Exception ex)
    {
        Console.WriteLine();
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    Console.WriteLine("Press Enter to exit.");
    Console.ReadLine();
    ```

9. No Gerenciador de Soluções, abra **Definir projetos de StartUp...** e certifique-se de que a **Ação** para o projeto **ReportConnectivity** é **Iniciar**. Compile a solução.

10. Execute esse aplicativo clicando com o botão direito do mouse no projeto **ReportConnectivity** e selecionando **Depurar**, seguido de **Iniciar nova instância**. Você deve vê-lo obter as informações de gêmeo e enviar a conectividade como uma *propriedade relatada*.
   
    ![Execute o aplicativo de dispositivo para conectividade de relatório](./media/iot-hub-csharp-csharp-twin-getstarted/rundeviceapp.png)
       
11. Agora que o dispositivo divulgou suas informações de conectividade, ele deve aparecer em ambas as consultas. Execute o aplicativo .NET **AddTagsAndQuery** para executar as consultas novamente. Desta vez, **myDeviceId** deve aparecer em ambos os resultados da consulta.
   
    ![Conectividade do dispositivo relatada com êxito](./media/iot-hub-csharp-csharp-twin-getstarted/tagappsuccess.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você configurou um novo hub IoT no portal do Azure e depois criou uma identidade do dispositivo no Registro de identidade do Hub IoT. Você adicionou os metadados do dispositivo como marcações de um aplicativo de back-end e criou um aplicativo de dispositivo simulado para relatar informações de conectividade no dispositivo gêmeo. Você também aprendeu a consultar essas informações usando a linguagem de consulta do Hub IoT semelhante ao SQL.

Veja os recursos a seguir para saber como:

* enviar telemetria de dispositivos com tutorial [Enviar telemetria de um dispositivo para um Hub IoT](quickstart-send-telemetry-dotnet.md),

* configurar dispositivos usando as propriedades desejadas do dispositivo gêmeo com o tutorial [Usar propriedades desejadas para configurar dispositivos](tutorial-device-twins.md),

* controlar dispositivos interativamente (assim como ativar uma ventoinha de um aplicativo controlado pelo usuário) com o tutorial [Uso de métodos diretos](quickstart-control-device-dotnet.md).
