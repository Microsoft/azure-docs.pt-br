---
title: incluir arquivo
description: incluir arquivo
services: iot-hub
author: chrissie926
ms.service: iot-hub
ms.topic: include
ms.date: 08/07/2019
ms.author: menchi
ms.custom: include file
ms.openlocfilehash: a5c1ddd085ae65b9920d73f50f993f4646785a69
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "68883793"
---
## <a name="create-a-module-identity"></a>Criar uma identidade de módulo

Nesta seção, você cria um aplicativo de console .NET que cria uma identidade de dispositivo e uma identidade de módulo no registro de identidade em seu hub. Um dispositivo ou módulo não pode se conectar ao Hub, a menos que ele tenha uma entrada no registro de identidade. Para obter mais informações, consulte a [seção registro de identidade do guia do desenvolvedor do Hub IOT](../articles/iot-hub/iot-hub-devguide-identity-registry.md).

Quando você executa esse aplicativo de console, ele gera ID e chave exclusivas para o dispositivo e o módulo. Seu dispositivo e módulo usam esses valores para se identificar quando enviam mensagens do dispositivo para a nuvem para o Hub IoT. As IDs diferenciam minúsculas e maiúsculas.

1. Abra o Visual Studio e selecione **Criar um projeto**.

1. Em **criar um novo projeto**, selecione **aplicativo de console (.NET Framework)**.

1. Selecione **Avançar** para abrir **configurar seu novo projeto**. Nomeie o projeto *CreateIdentities* e a solução *IoTHubGetStarted*. Verifique se a versão do .NET Framework é 4.6.1 ou posterior.

    ![Insira o nome e a estrutura da sua solução do Visual Studio](./media/iot-hub-get-started-create-module-identity-csharp/configure-createidentities-project.png)

1. No Visual Studio, abra **ferramentas**  >  **Gerenciador de pacotes NuGet**  >  **gerenciar pacotes NuGet para solução**. Selecione a guia **Procurar**.

1. Pesquise **Microsoft. Azure. Devices**. Selecione-o e, em seguida, selecione **instalar**.

    ![Instalar a versão atual do SDK do serviço .NET do Hub IoT do Azure](./media/iot-hub-get-started-create-module-identity-csharp/install-service-sdk.png)

1. Adicione as instruções `using` abaixo na parte superior do arquivo **Program.cs** :

   ```csharp
   using Microsoft.Azure.Devices;
   using Microsoft.Azure.Devices.Common.Exceptions;
   ```

1. Adicione os seguintes campos à classe **Program** . Substitua o valor do espaço reservado pela cadeia de conexão do Hub IoT criado na seção anterior.

   ```csharp
   const string connectionString = "<replace_with_iothub_connection_string>";
   const string deviceID = "myFirstDevice";
   const string moduleID = "myFirstModule";
   ```

1. Adicione o seguinte código para a classe **Principal**.

   ```csharp
   static void Main(string[] args)
   {
       AddDeviceAsync().Wait();
       AddModuleAsync().Wait();
   }
   ```

1. Adicione os seguintes métodos à classe **Programa**:

    ```csharp
    private static async Task AddDeviceAsync()
    {
       RegistryManager registryManager = 
         RegistryManager.CreateFromConnectionString(connectionString);
       Device device;

       try
       {
           device = await registryManager.AddDeviceAsync(new Device(deviceID));
       }
       catch (DeviceAlreadyExistsException)
        {
            device = await registryManager.GetDeviceAsync(deviceID);
        }

        Console.WriteLine("Generated device key: {0}", 
          device.Authentication.SymmetricKey.PrimaryKey);
    }

    private static async Task AddModuleAsync()
    {
        RegistryManager registryManager = 
          RegistryManager.CreateFromConnectionString(connectionString);
        Module module;

        try
        {
            module = 
              await registryManager.AddModuleAsync(new Module(deviceID, moduleID));
        }
        catch (ModuleAlreadyExistsException)
        {
            module = await registryManager.GetModuleAsync(deviceID, moduleID);
        }

        Console.WriteLine("Generated module key: {0}", module.Authentication.SymmetricKey.PrimaryKey);
    }
    ```

    O `AddDeviceAsync` método cria uma identidade de dispositivo com a ID **myFirstDevice**. Se essa ID de dispositivo já existir no registro de identidade, o código simplesmente recuperará as informações do dispositivo existente. Em seguida, o aplicativo exibe a chave primária dessa identidade. Você usa essa chave no aplicativo de dispositivo simulado para se conectar ao seu hub.

    O `AddModuleAsync` método cria uma identidade de módulo com a ID **myFirstModule** em Device **myFirstDevice**. Se essa ID de módulo já existir no registro de identidade, o código simplesmente recuperará as informações de módulo existentes. Em seguida, o aplicativo exibe a chave primária dessa identidade. Você usa essa chave no aplicativo de módulo simulado para se conectar ao seu hub.

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

1. Execute este aplicativo e anote a chave do dispositivo e a chave do módulo.

> [!NOTE]
> O registro de identidade do Hub IoT armazena somente identidades de dispositivo e de módulo para habilitar o acesso seguro ao Hub. O registro de identidade armazena IDs de dispositivo e chaves para usar como credenciais de segurança. O registro de identidade também armazena um sinalizador de habilitado/desabilitado para cada dispositivo que você pode usar para desabilitar o acesso ao dispositivo. Se seu aplicativo precisar armazenar outros metadados específicos do dispositivo, ele deverá usar um repositório específico do aplicativo. Não há nenhum sinalizador habilitado/desabilitado para as identidades do módulo. Para saber mais, confira [Guia de Desenvolvedor do Hub IoT](../articles/iot-hub/iot-hub-devguide-identity-registry.md).
