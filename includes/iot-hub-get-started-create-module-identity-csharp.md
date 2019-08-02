---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: iot-hub
author: chrissie926
ms.service: iot-hub
ms.topic: include
ms.date: 04/26/2018
ms.author: menchi
ms.custom: include file
ms.openlocfilehash: e78c9a490d2ad02fb132d62b0ab0b55f15d3d4ed
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172575"
---
## <a name="create-a-module-identity"></a>Criar uma identidade de módulo

Nesta seção, você cria um aplicativo do console do .NET que cria uma identidade do dispositivo e uma identidade do módulo no registro de identidade em seu Hub IoT. Um dispositivo ou módulo não pode se conectar ao Hub IoT, a menos que ele tenha uma entrada no Registro de identidade. Para obter mais informações, consulte a seção [Registro de identidade do Guia do desenvolvedor do Hub IoT](../articles/iot-hub/iot-hub-devguide-identity-registry.md). Quando você executa esse aplicativo de console, ele gera ID e chave exclusivas para o dispositivo e o módulo. O dispositivo e o módulo usam esses valores para se identificar ao enviar mensagens de dispositivo para nuvem para o Hub IoT. As IDs diferenciam minúsculas e maiúsculas.


1. **Criar um projeto do Visual Studio** - No Visual Studio, adicione um projeto da Área de Trabalho Clássica do Windows no Visual C# a uma nova solução usando o modelo de projeto **Aplicativo do Console (.NET Framework)** . Verifique se a versão do .NET Framework é 4.6.1 ou posterior. Nomeie o projeto **CreateIdentities** e a solução **IoTHubGetStarted**.

    ![Criar uma solução do visual studio](./media/iot-hub-get-started-create-module-identity-csharp/create-identities-csharp1.JPG)

2. **Instalar SDK V1.16.0-preview-001 do serviço .NET do Hub IoT do Azure** - Identidade do módulo e módulo gêmeo estão em versão prévia pública. Ele está disponível apenas nos SDKs do serviço de pré-lançamento do Hub IoT. No Visual Studio, abra ferramentas > gerenciamento de pacotes Nuget > gerenciar pacotes Nuget para solução. Pesquise Microsoft.Azure.Devices. Verifique se você marcou a caixa de seleção de incluir pré-lançamento. Selecione a versão 1.16.0-preview-001 e instale. Agora você tem acesso a todos os recursos do módulo. 

    ![Instalar SDK V1.16.0-preview-001 do serviço .NET do Hub IoT do Azure](./media/iot-hub-get-started-create-module-identity-csharp/install-sdk.png)

3. Adicione as instruções `using` abaixo na parte superior do arquivo **Program.cs** :

   ```csharp
   using Microsoft.Azure.Devices;
   using Microsoft.Azure.Devices.Common.Exceptions;
   ```

4. Adicione os seguintes campos à classe **Program** . Substitua o valor do espaço reservado pela cadeia de conexão do Hub IoT criado na seção anterior.

   ```csharp
   const string connectionString = "<replace_with_iothub_connection_string>";
   const string deviceID = "myFirstDevice";
   const string moduleID = "myFirstModule";
   ```

5. Adicione o seguinte código para a classe **Principal**.
   
   ```csharp
   static void Main(string[] args)
   {
       AddDeviceAsync().Wait();
       AddModuleAsync().Wait();
   }
   ```

6. Adicione os seguintes métodos à classe **Programa**:

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

    O método AddDeviceAsync() cria uma identidade do dispositivo com a ID **myFirstDevice**. (se essa ID do dispositivo já existir no registro de identidade, o código simplesmente irá recuperar as informações do dispositivo existentes.) Em seguida, o aplicativo exibe a chave primária dessa identidade. Você usa essa chave no aplicativo de dispositivo simulado para se conectar ao Hub IoT.

    O método AddModuleAsync() cria uma identidade de módulo com a ID **myFirstModule** no dispositivo **myFirstDevice**. (se essa ID de módulo já existir no registro de identidade, o código simplesmente irá recuperar as informações do módulo existentes.) Em seguida, o aplicativo exibe a chave primária dessa identidade. Você usa essa chave no aplicativo de módulo simulado para se conectar ao Hub IoT.

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

7. Execute este aplicativo e anote a chave do dispositivo e a chave do módulo.

> [!NOTE]
> O Registro de identidade do Hub IoT armazena apenas as identidades de dispositivo e módulo para habilitar o acesso seguro ao Hub IoT. O registro de identidade armazena IDs de dispositivo e chaves para usar como credenciais de segurança. O registro de identidade também armazena um sinalizador de habilitado/desabilitado para cada dispositivo que você pode usar para desabilitar o acesso ao dispositivo. Se seu aplicativo precisar armazenar outros metadados específicos do dispositivo, ele deverá usar um repositório específico do aplicativo. Não há nenhum sinalizador habilitado/desabilitado para as identidades do módulo. Para saber mais, confira [Guia de Desenvolvedor do Hub IoT](../articles/iot-hub/iot-hub-devguide-identity-registry.md).
