---
title: "Criar um Módulo do Azure IoT Edge com C# | Microsoft Docs"
description: "Este tutorial mostra como escrever um módulo de conversor de dados BLE usando os últimos pacotes NuGet do Azure IoT Edge, Visual Studio Code e C#."
services: iot-hub
author: jeffreyCline
manager: timlt
keywords: azure, iot, tutorial, module, nuget, vscode, csharp, edge
ms.service: iot-hub
ms.devlang: csharp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2017
ms.author: jcline
ms.openlocfilehash: 7175ffc8de2c043593d61143b402484d33e4a8cc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-azure-iot-edge-module-with-cx23"></a>Criar um módulo do Azure IoT Edge com o C&#x23;

Este tutorial mostra como criar um módulo para o `Azure IoT Edge` usando `Visual Studio Code` e `C#`.

Neste tutorial, explicamos a configuração do ambiente e como escrever um módulo do conversor de dados [BLE](https://en.wikipedia.org/wiki/Bluetooth_Low_Energy) usando os últimos pacotes `Azure IoT Edge NuGet`. 

>[!NOTE]
Este tutorial está usando o `.NET Core SDK`, que oferece suporte à compatibilidade de plataforma cruzada. O tutorial a seguir é gravado usando o sistema operacional `Windows 10`. Alguns dos comandos neste tutorial podem ser diferente dependendo de sua `development environment`. 

## <a name="prerequisites"></a>Pré-requisitos

Nesta seção, você configura o ambiente para o desenvolvimento de módulos do `Azure IoT Edge`. Ela se aplica aos sistemas operacionais **Windows de 64 bits** e **Linux de 64 bits (Ubuntu/Debian 8)**.

O seguinte software é necessário:

- [Cliente Git](https://git-scm.com/downloads)
- [SDK do .NET Core](https://www.microsoft.com/net/core#windowscmd)
- [Visual Studio Code](https://code.visualstudio.com/)

Você não precisa clonar o repositório para este exemplo, no entanto, todo o código de exemplo discutido neste tutorial está localizado no repositório a seguir:

- `git clone https://github.com/Azure-Samples/iot-edge-samples.git`.
- `cd iot-edge-samples/dotnetcore/simulated_ble`

## <a name="getting-started"></a>Introdução

1. Instalar `.NET Core SDK`.
2. Instale o `Visual Studio Code` e o `C# extension` do Marketplace do Visual Studio Code.

Veja este [rápido tutorial em vídeo](https://channel9.msdn.com/Blogs/dotnet/Get-started-VSCode-Csharp-NET-Core-Windows) sobre como começar a usar `Visual Studio Code` e `.NET Core SDK`.

## <a name="creating-the-azure-iot-edge-converter-module"></a>Criar o módulo de conversor do Azure IoT Edge

1. Inicializar um novo projeto C# da biblioteca de classes `.NET Core`:
    - Abra um prompt de comando (`Windows + R` -> `cmd` -> `enter`).
    - Navegue até a pasta onde você deseja criar o projeto `C#`.
    - Digite **dotnet new classlib -o IoTEdgeConverterModule -f netstandard1.3**. 
    - Este comando cria uma classe vazia chamada `Class1.cs` em seu diretório de projetos.
2. Navegue até a pasta onde acabamos de criar o projeto de biblioteca de classes digitando **cd IoTEdgeConverterModule**.
3. Abra o projeto no `Visual Studio Code` digitando **code** .
4. Se o projeto for aberto no `Visual Studio Code`, clique em **IoTEdgeConverterModule.csproj** para abrir o arquivo, como mostrado na imagem a seguir:

    ![Janela de edição do Visual Studio Code](media/iot-hub-iot-edge-create-module/vscode-edit-csproj.png)

5. Insira o blob de `XML` mostrado no trecho de código a seguir entre a marca `PropertyGroup` de fechamento e a marca `Project` de fechamento; linha seis na imagem anterior e salve o arquivo pressionando `Ctrl` + `S`.

   ```xml
     <ItemGroup>
       <PackageReference Include="Microsoft.Azure.Devices.Gateway.Module.NetStandard" Version="1.0.5" />
       <PackageReference Include="System.Threading.Thread" Version="4.3.0" />
       <PackageReference Include="Newtonsoft.Json" Version="10.0.2" />
     </ItemGroup> 
   ```

6. Depois de salvar o arquivo `.csproj`, `Visual Studio Code` exibirá uma caixa de diálogo `unresolved dependencies`, como mostrado na imagem a seguir: 

    ![Caixa de diálogo de dependências de restauração do Visual Studio Code](media/iot-hub-iot-edge-create-module/vscode-restore.png)

    a) Clique em `Restore` para restaurar todas as referências no arquivo `.csproj` dos projetos incluindo as `PackageReferences` que adicionamos. 

    b) `Visual Studio Code` cria automaticamente o arquivo `project.assets.json` em sua pasta `obj` dos projetos. Esse arquivo contém informações sobre dependências do projeto para tornar as restaurações posteriores mais rápidas.
 
    >[!NOTE]
    `.NET Core Tools` agora são baseadas em MSBuild. O que significa que um arquivo de projeto `.csproj` é criado, em vez de um `project.json`.

    - Se `Visual Studio Code` não solicita a você se está ok, podemos fazer isso manualmente. Abra janela do terminal integrado `Visual Studio Code` pressionando as teclas `Ctrl` + `backtick` ou usando os menus `View` -> `Integrated Terminal`.
    - Na janela `Integrated Terminal` digite **dotnet restore**.
    
7. Renomear o arquivo `Class1.cs` para `BleConverterModule.cs`. 

    a) Para renomear o arquivo primeiro clique no arquivo, em seguida, pressione a tecla `F2`.
    
    b) Digite o novo nome **BleConverterModule**, como mostrado na imagem a seguir:

    ![Visual Studio Code renomeando uma classe](media/iot-hub-iot-edge-create-module/vscode-rename.png)

8. Substitua o código existente no arquivo `BleConverterModule.cs` copiando e colando o seguinte trecho de código em seu arquivo `BleConverterModule.cs`.

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Globalization;
   using System.Linq;
   using System.Text;
   using System.Threading;
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Gateway;
   using Newtonsoft.Json;
   
   namespace IoTEdgeConverterModule
   {
       public class BleConverterModule : IGatewayModule, IGatewayModuleStart
       {
           private Broker broker;
           private string configuration;
           private int messageCount;

           public void Create(Broker broker, byte[] configuration)
           {
               this.broker = broker;
               this.configuration = System.Text.Encoding.UTF8.GetString(configuration);
           }
   
           public void Start()
           {
           }

           public void Destroy()
           {
           }

           public void Receive(Message received_message)
           {
               string recMsg = Encoding.UTF8.GetString(received_message.Content, 0, received_message.Content.Length);
               BleData receivedData = JsonConvert.DeserializeObject<BleData>(recMsg);

               float temperature = float.Parse(receivedData.Temperature, CultureInfo.InvariantCulture.NumberFormat); 
               Dictionary<string, string> receivedProperties = received_message.Properties;
            
               Dictionary<string, string> properties = new Dictionary<string, string>();
               properties.Add("source", receivedProperties["source"]);
               properties.Add("macAddress", receivedProperties["macAddress"]);
               properties.Add("temperatureAlert", temperature > 30 ? "true" : "false");
   
               String content = String.Format("{0} \"deviceId\": \"Intel NUC Gateway\", \"messageId\": {1}, \"temperature\": {2} {3}", "{", ++this.messageCount, temperature, "}");
               Message messageToPublish = new Message(content, properties);
   
               this.broker.Publish(messageToPublish);
           }
       }
   }
   ```

9. Salve o arquivo pressionando `Ctrl` + `S`.

10. Crie um novo arquivo chamado `Untitled-1` pressionando as teclas `Ctrl` + `N` como mostrado na imagem a seguir:

    ![Novo arquivo do Visual Studio Code](media/iot-hub-iot-edge-create-module/vscode-new-file.png)

11. Para desserializar o objeto `JSON` que recebemos do dispositivo `BLE` simulado, copie o seguinte código para a janela do editor de códigos de arquivo `Untitled-1`. 

   ```csharp
   using System;
   using Newtonsoft.Json;

   namespace IoTEdgeConverterModule
   {
       public class BleData
       {
           [JsonProperty(PropertyName = "temperature")]
           public string Temperature { get; set; }
       }
   }
   ```

12. Salve o arquivo como `BleData.cs` pressionando as teclas `Ctrl` + `Shift` + `S`.
    - Na caixa de diálogo Salvar como no menu suspenso `Save as Type`, selecione `C# (*.cs;*.csx)` como mostrado na imagem a seguir:

    ![Caixa de diálogo Salvar como do Visual Studio Code](media/iot-hub-iot-edge-create-module/vscode-save-as.png)

13. Crie um novo arquivo chamado `Untitled-1` pressionando as teclas `Ctrl` + `N`.

14. Copie e cole o seguinte trecho de código no arquivo `Untitled-1`. Essa classe é um módulo `Azure IoT Edge`, que usamos para exibir os dados recebidos do nosso `BleConverterModule`.

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Linq;
   using System.Text;
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Gateway;
   using Newtonsoft.Json;
   
   namespace PrinterModule
   {
       public class DotNetPrinterModule : IGatewayModule
       {
           private string configuration;
           public void Create(Broker broker, byte[] configuration)
           {
               this.configuration = System.Text.Encoding.UTF8.GetString(configuration);
           }
   
           public void Destroy()
           {
           }
   
           public void Receive(Message received_message)
           {
               string recMsg = System.Text.Encoding.UTF8.GetString(received_message.Content, 0, received_message.Content.Length);
               Dictionary<string, string> receivedProperties = received_message.Properties;
               
               BleConverterData receivedData = JsonConvert.DeserializeObject<BleConverterData>(recMsg);
   
               Console.WriteLine();
               Console.WriteLine("Module           : [DotNetPrinterModule]");
               Console.WriteLine("received_message : {0}", recMsg);
   
               if(received_message.Properties["source"] == "bleTelemetry")
               {
                   Console.WriteLine("Source           : {0}", receivedProperties["source"]);
                   Console.WriteLine("MAC address      : {0}", receivedProperties["macAddress"]);
                   Console.WriteLine("Temperature Alert: {0}", receivedProperties["temperatureAlert"]);
                   Console.WriteLine("Deserialized Obj : [BleConverterData]");
                   Console.WriteLine("  DeviceId       : {0}", receivedData.DeviceId);
                   Console.WriteLine("  MessageId      : {0}", receivedData.MessageId);
                   Console.WriteLine("  Temperature    : {0}", receivedData.Temperature);
               }
   
               Console.WriteLine();
           }
       }
   }
   ```

15. Salve o arquivo como `DotNetPrinterModule.cs` pressionando `Ctrl` + `Shift` + `S`.
    - Na caixa de diálogo Salvar como no menu suspenso `Save as Type`, selecione `C# (*.cs;*.csx)`.

16. Crie um novo arquivo chamado `Untitled-1` pressionando as teclas `Ctrl` + `N`.

17. Para desserializar o objeto `JSON` que recebemos de `BleConverterModule`, copie e cole o seguinte trecho de código para o arquivo `Untitled-1`. 

   ```csharp
   using System;
   using Newtonsoft.Json;

   namespace PrinterModule
   {
       public class BleConverterData
       {
           [JsonProperty(PropertyName = "deviceId")]
           public string DeviceId { get; set; }
   
           [JsonProperty(PropertyName = "messageId")]
           public string MessageId { get; set; }
   
           [JsonProperty(PropertyName = "temperature")]
           public string Temperature { get; set; }
       }
   }
   ```

18. Salve o arquivo como `BleConverterData.cs` pressionando `Ctrl` + `Shift` + `S`.
    - Na caixa de diálogo Salvar como no menu suspenso `Save as Type`, selecione `C# (*.cs;*.csx)`.

19. Crie um novo arquivo chamado `Untitled-1` pressionando as teclas `Ctrl` + `N`.

20. Copie e cole o seguinte trecho de código no arquivo `Untitled-1`.

   ```json
   {
       "loaders": [
           {
               "type": "dotnetcore",
               "name": "dotnetcore",
               "configuration": {
                   "binding.path": "dotnetcore.dll",
                   "binding.coreclrpath": "C:\\Program Files\\dotnet\\shared\\Microsoft.NETCore.App\\1.1.1\\coreclr.dll",
                   "binding.trustedplatformassemblieslocation": "C:\\Program Files\\dotnet\\shared\\Microsoft.NETCore.App\\1.1.1\\"
               }
           }
       ],
          "modules": [
           {
               "name": "simulated_device",
               "loader": {
                   "name": "native",
                   "entrypoint": {
                       "module.path": "simulated_device.dll"
                   }
               },
               "args": {
                   "macAddress": "01:02:03:03:02:01",
                   "messagePeriod": 500
               }
           },
           {
               "name": "ble_converter_module",
               "loader": {
                   "name": "dotnetcore",
                   "entrypoint": {
                       "assembly.name": "IoTEdgeConverterModule",
                       "entry.type": "IoTEdgeConverterModule.BleConverterModule"
                   }
               },
               "args": ""
           },
           {
               "name": "printer_module",
               "loader": {
                   "name": "dotnetcore",
                   "entrypoint": {
                       "assembly.name": "IoTEdgeConverterModule",
                       "entry.type": "PrinterModule.DotNetPrinterModule"
                   }
               },
               "args": ""
           }
       ],
       "links": [
           {
               "source": "simulated_device", "sink": "ble_converter_module"
           },
           {
               "source": "ble_converter_module", "sink": "printer_module"
           }
   ]
   }
   ```

21. Salve o arquivo como `gw-config.json` pressionando `Ctrl` + `Shift` + `S`.
    - Na caixa de diálogo Salvar como no menu suspenso `Save as Type`, selecione `JSON (*.json;*.bowerrc;*.jshintrc;*.jscsrc;*.eslintrc;*.babelrc;*webmanifest)`.

22. Para habilitar a cópia do arquivo de configuração para o diretório de saída, atualize o `IoTEdgeConverterModule.csproj` com o blob XML a seguir:

   ```xml
     <ItemGroup>
       <None Update="gw-config.json" CopyToOutputDirectory="PreserveNewest" />
     </ItemGroup>
   ```
    
   - O `IoTEdgeConverterModule.csproj` atualizado deverá ter a seguinte aparência:

    ![Arquivo .csproj atualizado do Visual Studio Code](media/iot-hub-iot-edge-create-module/vscode-update-csproj.png)

23. Crie um novo arquivo chamado `Untitled-1` pressionando as teclas `Ctrl` + `N`.

24. Copie e cole o seguinte trecho de código no arquivo `Untitled-1`.

   ```powershell
   Copy-Item -Path $env:userprofile\.nuget\packages\microsoft.azure.devices.gateway.native.windows.x64\1.1.3\runtimes\win-x64\native\* -Destination .\bin\Debug\netstandard1.3
   Copy-Item -Path $env:userprofile\.nuget\packages\system.runtime.serialization.formatters\4.3.0\lib\netstandard1.4\* -Destination .\bin\Debug\netstandard1.3
   Copy-Item -Path $env:userprofile\.nuget\packages\system.runtime.serialization.primitives\4.3.0\lib\netstandard1.3\* -Destination .\bin\Debug\netstandard1.3
   Copy-Item -Path $env:userprofile\.nuget\packages\newtonsoft.json\10.0.2\lib\netstandard1.3\* -Destination .\bin\Debug\netstandard1.3
   Copy-Item -Path $env:userprofile\.nuget\packages\system.componentmodel.typeconverter\4.3.0\lib\netstandard1.5\* -Destination .\bin\Debug\netstandard1.3
   Copy-Item -Path $env:userprofile\.nuget\packages\system.collections.nongeneric\4.3.0\lib\netstandard1.3\* -Destination .\bin\Debug\netstandard1.3
   Copy-Item -Path $env:userprofile\.nuget\packages\system.collections.specialized\4.3.0\lib\netstandard1.3\* -Destination .\bin\Debug\netstandard1.3
   ```

25. Salve o arquivo como `binplace.ps1` pressionando `Ctrl` + `Shift` + `S`.
    - Na caixa de diálogo Salvar como no menu suspenso `Save as Type`, selecione `PowerShell (*.ps1;*.psm1;*.psd1;*.pssc;*.psrc)`.

26. Compile o projeto pressionando as teclas `Ctrl` + `Shift` + `B`. Quando você compila o projeto pela primeira vez, `Visual Studio Code` exibe a caixa de diálogo `No build task defined.`, como mostrado na imagem a seguir:

    ![Caixa de diálogo de tarefa de compilação do Visual Studio Code](media/iot-hub-iot-edge-create-module/vscode-build-task.png)

    a) Clique no botão `Configure Build Task`.

    b) No menu suspenso `Select a Task Runner` da caixa de diálogo. Selecione `.NET Core` como mostrado na imagem a seguir: 

    ![Caixa de diálogo Selecionar uma tarefa do Visual Studio Code](media/iot-hub-iot-edge-create-module/vscode-build-task-runner.png)

    c) Clicar no item `.NET Core` cria o arquivo `tasks.json` no seu diretório `.vscode` e abre o arquivo na janela `code editor`. Não é necessário modificar esse arquivo, feche a guia.

27.  Abra a janela do terminal integrado `Visual Studio Code`, pressionando as teclas `Ctrl` + `backtick` ou usando os menus `View` -> `Integrated Terminal` e digite **.\binplace.ps1** no prompt de comando `PowerShell`. Este comando copia todos as nossas dependências para o diretório de saída.

28. Navegue até o diretório de saída de projetos na janela `Integrated Terminal` digitando **cd .\bin\Debug\netstandard1.3**.

29. Execute o projeto de exemplo digitando **.\gw.exe gw-config.json** no prompt da janela `Integrated Terminal`. 
    - Se você tiver seguido exatamente as etapas neste tutorial, você deve estar agora executando o projeto de exemplo `Azure IoT Edge BLE Data Converter Module`, como mostrado na imagem a seguir:
    
        ![Exemplo de dispositivo simulado em execução no Visual Studio Code](media/iot-hub-iot-edge-create-module/vscode-run.png)
    
    - Se você deseja encerrar o aplicativo, pressione a tecla `<Enter>`.

>[!IMPORTANT]
Não é recomendável usar `Ctrl` + `C` para encerrar o aplicativo de gateway do `IoT Edge` (isto é, **gw.exe**). Essa ação pode causar uma anomalia no encerramento do processo.

