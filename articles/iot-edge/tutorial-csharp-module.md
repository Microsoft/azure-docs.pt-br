---
title: Tutorial para desenvolver um módulo do C# para Linux – Azure IoT Edge | Microsoft Docs
description: Este tutorial mostra como criar um módulo do IoT Edge com código em C# e implantá-lo em um dispositivo Linux IoT Edge.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/23/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 305804682ede9833619837807d6f174ec670560e
ms.sourcegitcommit: c556477e031f8f82022a8638ca2aec32e79f6fd9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68414250"
---
# <a name="tutorial-develop-a-c-iot-edge-module-for-linux-devices"></a>Tutorial: Desenvolver um módulo do IoT Edge do C# para dispositivos Linux

Use o Visual Studio Code para desenvolver código do C# e implantá-lo em um dispositivo Linux que executando o Azure IoT Edge. 

Use os módulos do Azure IoT Edge para implantar um código que implementa a lógica de negócios diretamente em seus dispositivos IoT Edge. Este tutorial o orienta através da criação e implantação de um módulo IoT Edge que filtra os dados do sensor. Você utilizará o dispositivo IoT Edge simulado que foi criado em Implantar Azure IoT Edge em um dispositivo simulado nos inícios rápidos de [Windows](quickstart.md) ou [Linux](quickstart-linux.md). Neste tutorial, você aprenderá como:    

> [!div class="checklist"]
> * Use o Visual Studio Code para criar um módulo do IoT Edge baseado no SDK .NET Core 2.1.
> * Usar o Visual Studio Code e o Docker para criar uma imagem do Docker e publicá-la no registro.
> * Implantar o módulo no dispositivo IoT Edge.
> * Exibir os dados gerados.

O módulo IoT Edge que criado neste tutorial filtra os dados de temperatura gerados pelo seu dispositivo. Ele somente envia mensagens upstream se a temperatura estiver acima de um limite especificado. Esse tipo de análise na borda é útil para reduzir a quantidade de dados que é comunicada e armazenada na nuvem. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Escopo da solução

Este tutorial demonstra como desenvolver um módulo em **C#** usando o **Visual Studio Code** e como implantá-lo em um **dispositivo Linux**. Se você estiver desenvolvendo módulos para dispositivos Windows, acesse [Desenvolver um módulo do IoT Edge em C# para dispositivos Windows](tutorial-csharp-module-windows.md).

Use a tabela a seguir para entender as opções para desenvolver e implantar módulos do C em dispositivos Linux: 

| C# | Visual Studio Code | Visual Studio | 
| -- | ------------------ | ------------- |
| **Linux AMD64** | ![Módulos em C# para LinuxAMD64 no VS Code](./media/tutorial-c-module/green-check.png) | ![Módulos em C# para LinuxAMD64 no Visual Studio](./media/tutorial-c-module/green-check.png) |
| **Linux ARM32** | ![Módulos em C# para LinuxARM32 no VS Code](./media/tutorial-c-module/green-check.png) | ![Módulos em C# para LinuxARM64 no Visual Studio](./media/tutorial-c-module/green-check.png) |

>[!NOTE]
>O suporte para dispositivos Linux ARM64 está disponível na [versão prévia pública](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Para obter mais informações, confira [Desenvolver e depurar módulos do IoT Edge ARM64 no Visual Studio Code (versão prévia)](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview).

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este tutorial, você deve ter passado pelo tutorial anterior para configurar seu ambiente de desenvolvimento, [Desenvolver um módulo do IoT Edge para um dispositivo Linux](tutorial-develop-for-linux.md). Após concluir esse tutorial, você já deverá ter os seguintes pré-requisitos: 

* Um [Hub IoT](../iot-hub/iot-hub-create-through-portal.md) na camada padrão ou gratuito no Azure.
* Um [dispositivo Linux que executa o Azure IoT Edge](quickstart-linux.md).
* Um registro de contêiner, como o [Registro de Contêiner do Azure](https://docs.microsoft.com/azure/container-registry/).
* O [Visual Studio Code](https://code.visualstudio.com/) configurado com o [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* O [Docker CE](https://docs.docker.com/install/) configurado para executar contêineres do Linux.

Para concluir estes tutoriais, prepare os seguintes pré-requisitos adicionais no computador de desenvolvimento: 

* [C# para extensão do Visual Studio Code (com OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).
* [SDK do .NET Core 2.1](https://www.microsoft.com/net/download).

## <a name="create-a-module-project"></a>Criar um projeto de módulo

As etapas a seguir criam um projeto de módulo do IoT Edge para C# usando o Visual Studio Code e a extensão Azure IoT Tools. Quando o modelo de projeto for criado, adicione o novo código para que o módulo filtre as mensagens com base nas propriedades relatadas. 

### <a name="create-a-new-project"></a>Criar um novo projeto

Crie um modelo de solução de C# que possa ser personalizado com seu próprio código. 

1. No Visual Studio Code, selecione **Exibir** > **Paleta de comandos** para abrir a paleta de comandos do VS Code. 

2. Na paleta de comandos, insira e execute o comando **Azure: Entrar** e siga as instruções para entrar na conta do Azure. Se já tiver entrado, pode ignorar esta etapa.

3. Na paleta de comandos, digite e execute o comando **Azure IoT Edge: nova solução do IoT Edge**. Siga os prompts na paleta de comandos para criar sua solução.

   | Campo | Valor |
   | ----- | ----- |
   | Selecionar pasta | Escolha o local no computador de desenvolvimento em que o VS Code criará os arquivos de solução. |
   | Fornecer um nome para a solução | Insira um nome descritivo para a solução ou aceite o padrão **EdgeSolution**. |
   | Selecionar modelo do módulo | Escolha **Módulo C#** . |
   | Fornecer um nome de módulo | Nomeie o módulo **CSharpModule**. |
   | Fornecer o repositório de imagem do Docker para o módulo | Um repositório de imagem inclui o nome do registro de contêiner e o nome da imagem de contêiner. Sua imagem de contêiner é preenchida previamente o nome fornecido na última etapa. Substitua **localhost:5000** pelo valor do servidor de logon do seu registro de contêiner do Azure. Você pode recuperar o servidor de logon da página Visão Geral do seu registro de contêiner no portal do Azure. <br><br>O repositório de imagem final se parece com \<nome_do_registro\>.azurecr.io/csharpmodule. |
 
   ![Fornecer o repositório de imagem do Docker](./media/tutorial-csharp-module/repository.png)


### <a name="add-your-registry-credentials"></a>Adicionar suas credenciais de registro

O arquivo do ambiente armazena as credenciais para o registro de contêiner e as compartilha com o tempo de execução do IoT Edge. O tempo de execução precisa dessas credenciais para efetuar pull de imagens privadas para o dispositivo IoT Edge. Use as credenciais da seção **Chaves de acesso** do Registro de Contêiner do Azure. 

1. No explorador do VS Code, abra o arquivo **.env**. 
2. Atualize os campos com os valores de **nome de usuário** e **senha** do Registro de Contêiner do Azure. 
3. Salve o arquivo. 

### <a name="select-your-target-architecture"></a>Selecione sua arquitetura de destino

Atualmente, o Visual Studio Code pode desenvolver módulos C# para dispositivos Linux AMD64 e Linux ARM32v7. É necessário selecionar qual arquitetura você deseja usar para cada solução, porque o contêiner é criado e executado de maneira diferente para cada tipo de arquitetura. O padrão é o Linux AMD64. 

1. Abra a paleta de comandos e pesquise **Azure IoT Edge: definir a plataforma de destino padrão para a solução Edge** ou selecione o ícone de atalho na barra lateral na parte inferior da janela. 

2. Na paleta de comandos, selecione a arquitetura de destino na lista de opções. Para este tutorial, estamos usando uma máquina virtual Ubuntu como o dispositivo IoT Edge, portanto, manteremos o padrão **amd64**. 

### <a name="update-the-module-with-custom-code"></a>Atualizar o módulo com código personalizado

1. No explorador do VS Code, abra **módulos** > **CSharpModule** > **Program.cs**.

2. Na parte superior do namespace **CSharpModule**, adicione três instruções **using** aos tipos usados posteriormente:

    ```csharp
    using System.Collections.Generic;     // For KeyValuePair<>
    using Microsoft.Azure.Devices.Shared; // For TwinCollection
    using Newtonsoft.Json;                // For JsonConvert
    ```

3. Adicione a variável **temperatureThreshold** à classe **Program**. Essa variável define o valor que a temperatura medida deve exceder para que os dados sejam enviados ao hub IoT. 

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

4. Adicione as classes **MessageBody**, **Machine** e **Ambient** à classe **Program**. Essas classes definem o esquema esperado para o corpo de mensagens de entrada.

    ```csharp
    class MessageBody
    {
        public Machine machine {get;set;}
        public Ambient ambient {get; set;}
        public string timeCreated {get; set;}
    }
    class Machine
    {
        public double temperature {get; set;}
        public double pressure {get; set;}         
    }
    class Ambient
    {
        public double temperature {get; set;}
        public int humidity {get; set;}         
    }
    ```

5. Localize a função **Init**. Essa função cria e configura um objeto **ModuleClient**, que permite que o módulo conecte-se ao tempo de execução do IoT Edge do Azure para enviar e receber mensagens. Depois de criar o **ModuleClient**, o código lê o valor **temperatureThreshold** das propriedades desejadas do módulo gêmeo. O código registra um retorno de chamada para receber mensagens de um hub IoT Edge por meio do ponto de extremidade **input1**. Substitua o método **SetInputMessageHandlerAsync** por um novo e adicione um método **SetDesiredPropertyUpdateCallbackAsync** para atualizações das propriedades desejadas. Para fazer essa alteração, substitua a última linha do método **Init** pelo seguinte código:

    ```csharp
    // Register a callback for messages that are received by the module.
    // await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", PipeMessage, iotHubModuleClient);

    // Read the TemperatureThreshold value from the module twin's desired properties
    var moduleTwin = await ioTHubModuleClient.GetTwinAsync();
    await OnDesiredPropertiesUpdate(moduleTwin.Properties.Desired, ioTHubModuleClient);
    
    // Attach a callback for updates to the module twin's desired properties.
    await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertiesUpdate, null);

    // Register a callback for messages that are received by the module.
    await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
    ```

6. Adicione o método **onDesiredPropertiesUpdate** à classe **Program**. Este método recebe atualizações sobre as propriedades desejadas do módulo gêmeo e atualiza a variável **temperatureThreshold** para corresponder. Todos os módulos possuem seu próprio módulo gêmeo, o que permite configurar o código em execução dentro de um módulo diretamente da nuvem.

    ```csharp
    static Task OnDesiredPropertiesUpdate(TwinCollection desiredProperties, object userContext)
    {
        try
        {
            Console.WriteLine("Desired property change:");
            Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));

            if (desiredProperties["TemperatureThreshold"]!=null)
                temperatureThreshold = desiredProperties["TemperatureThreshold"];

        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error when receiving desired property: {0}", exception);
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error when receiving desired property: {0}", ex.Message);
        }
        return Task.CompletedTask;
    }
    ```

7. Substitua o método **PipeMessage** pelo método **FilterMessages**. Esse método é chamado sempre que o módulo recebe uma mensagem do hub do IoT Edge. Ele filtra as mensagens que reportam temperaturas abaixo do limite de temperatura definido através do módulo gêmeo. Ele também adiciona a propriedade **MessageType** à mensagem com o valor definido para **Alerta**. 

    ```csharp
    static async Task<MessageResponse> FilterMessages(Message message, object userContext)
    {
        var counterValue = Interlocked.Increment(ref counter);
        try
        {
            ModuleClient moduleClient = (ModuleClient)userContext;
            var messageBytes = message.GetBytes();
            var messageString = Encoding.UTF8.GetString(messageBytes);
            Console.WriteLine($"Received message {counterValue}: [{messageString}]");

            // Get the message body.
            var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

            if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
            {
                Console.WriteLine($"Machine temperature {messageBody.machine.temperature} " +
                    $"exceeds threshold {temperatureThreshold}");
                var filteredMessage = new Message(messageBytes);
                foreach (KeyValuePair<string, string> prop in message.Properties)
                {
                    filteredMessage.Properties.Add(prop.Key, prop.Value);
                }

                filteredMessage.Properties.Add("MessageType", "Alert");
                await moduleClient.SendEventAsync("output1", filteredMessage);
            }

            // Indicate that the message treatment is completed.
            return MessageResponse.Completed;
        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", exception);
            }
            // Indicate that the message treatment is not completed.
            var moduleClient = (ModuleClient)userContext;
            return MessageResponse.Abandoned;
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
            // Indicate that the message treatment is not completed.
            ModuleClient moduleClient = (ModuleClient)userContext;
            return MessageResponse.Abandoned;
        }
    }
    ```

8. Salve o arquivo Program.cs.

9. No gerenciador do VS Code, abra o arquivo **deployment.template.json** no workspace da solução IoT Edge. 

10. Adicione o módulo gêmeo **CSharpModule** no manifesto de implantação. Insira o seguinte conteúdo JSON na parte inferior da seção **modulesContent**, após o módulo gêmeo do **$edgeHub**: 

    ```json
       "CSharpModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
    ```

    ![Adicionar gêmeo de módulo ao modelo de implantação](./media/tutorial-csharp-module/module-twin.png)

11. Salve o arquivo deployment.template.json.


## <a name="build-and-push-your-module"></a>Criar e enviar seu módulo por push

Na seção anterior, você criou uma solução IoT Edge e adicionou um código a CSharpModule que filtrará mensagens em que a temperatura relatada do computador estiver dentro dos limites aceitáveis. Agora você precisa compilar a solução como uma imagem de contêiner e enviá-la por push para seu registro de contêiner.

1. Abra o terminal integrado do VS Code selecionando **Exibir** > **Terminal**.

1. Entre no Docker inserindo o seguinte comando no terminal. Entre com o nome de usuário, a senha e o servidor de logon do seu Registro de Contêiner do Azure. É possível recuperar esses valores na seção **Chaves de acesso** no registro do portal do Azure.
     
   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Talvez você receba um aviso de segurança recomendando usar `--password-stdin`. Embora essa prática seja recomendada para cenários de produção, ela não serve para este tutorial. Para saber mais, confira a referência de [logon do docker](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin).

2. No explorador do VS Code, clique com o botão direito do mouse no arquivo **deployment.template.json** e selecione **Compilar e enviar por push solução IoT Edge**.

   O comando de criação e de envio por push inicia três operações. Primeiro, é criada uma pasta na solução denominada **config** que contém o manifesto de implantação completo, criado com base nas informações do modelo de implantação e em outros arquivos da solução. Depois, ele executa `docker build` para montar a imagem de contêiner com base no dockerfile apropriado para sua arquitetura de destino. Em seguida, ele executa `docker push` para enviar o repositório de imagens por push para seu registro de contêiner.

## <a name="deploy-and-run-the-solution"></a>Implantar e executar a solução

Use o gerenciador do Visual Studio Code e a extensão Azure IoT Tools para implantar o projeto de módulo em seu dispositivo IoT Edge. Você já tem um manifesto de implantação preparado para o seu cenário, o arquivo **deployment.json** na pasta config. Agora, tudo o que você precisa fazer é selecionar um dispositivo para receber a implantação.

Verifique se seu dispositivo IoT Edge está em funcionamento.

1. No gerenciador do Visual Studio Code, expanda a seção **Dispositivos do Hub IoT do Azure** para ver sua lista de dispositivos IoT.

2. Clique com o botão direito no nome do seu dispositivo IoT Edge e selecione **Criar Implantação para Dispositivo Único**. 

5. Selecione o arquivo **deployment.json** na pasta **config** e clique em **Selecionar Manifesto de Implantação do Edge**. Não use o arquivo deployment.template.json. 

6. Clique no botão Atualizar. Você deve ver o novo **CSharpModule** sendo executado com o módulo **TempSensor** em **$edgeAgent** e **$edgeHub**.  

## <a name="view-generated-data"></a>Exibir os dados gerados

Depois que você aplica o manifesto de implantação no seu dispositivo IoT Edge, o tempo de execução do IoT Edge no dispositivo coleta as novas informações de implantação e inicia a execução nele. Todos os módulos em execução no dispositivo que não estão incluídos no manifesto de implantação são interrompidos. Todos os módulos ausentes do dispositivo são iniciados.

Você pode ver o status do seu dispositivo IoT Edge usando a seção **Dispositivos de Hub IoT do Azure** do explorer do Visual Studio Code. Expanda os detalhes do seu dispositivo para ver uma lista de módulos implantados e em execução.

1. No gerenciador do Visual Studio Code, clique com o botão direito do mouse no nome do dispositivo IoT Edge e escolha **Iniciar o monitoramento de pontos de extremidade internos de eventos**.

2. Exiba as mensagens que chegam ao seu Hub IoT. Pode levar um tempo para as mensagens chegarem, porque o dispositivo IoT Edge precisa receber sua nova implantação e iniciar todos os módulos. Em seguida, as alterações que fizemos no código CModule aguardam até que a temperatura do computador atinja 25 graus antes de enviar mensagens. Ele também adiciona o tipo de mensagem **Alerta** às mensagens que atingem esse limite de temperatura. 

   ![Exibir as mensagens que chegam ao Hub IoT](./media/tutorial-csharp-module/view-d2c-message.png)
 
## <a name="edit-the-module-twin"></a>Editar o módulo gêmeo

Usamos o módulo gêmeo CSharpModule no manifesto de implantação para definir o limite de temperatura em 25 graus. É possível usar o módulo gêmeo para alterar a funcionalidade sem precisar atualizar o código do módulo.

1. No Visual Studio Code, expanda os detalhes em seu dispositivo IoT Edge para ver os módulos em execução. 

2. Clique com o botão direito do mouse em **CSharpModule** e selecione **Editar módulo gêmeo**. 

3. Encontre **TemperatureThreshold** nas propriedades desejadas. Altere o valor para uma temperatura 5 a 10 graus maior do que a temperatura relatada mais recente. 

4. Salve o arquivo de módulo gêmeo.

5. Clique com o botão direito do mouse em qualquer lugar no painel de edição do módulo gêmeo e selecione **Atualizar módulo gêmeo**. 

5. Monitore as mensagens do dispositivo para nuvem recebidas. Você deve ver uma interrupção das mensagens até que o novo limite de temperatura seja atingido. 

## <a name="clean-up-resources"></a>Limpar recursos 

Se você pretende continuar no próximo artigo recomendado, pode manter os recursos e as configurações já criados e reutilizá-los. Você também pode continuar usando o mesmo dispositivo IoT Edge como um dispositivo de teste. 

Caso contrário, é possível excluir as configurações locais e os recursos do Azure usados neste artigo para evitar encargos. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou um módulo do IoT Edge que contém código para filtrar os dados brutos gerados pelo seu dispositivo IoT Edge. Quando estiver pronto para criar seus próprios módulos, você poderá saber mais sobre como [desenvolver seus próprios módulos do IoT Edge](module-development.md) ou como [desenvolver módulos com o Visual Studio Code](how-to-vs-code-develop-module.md). É possível passar para os próximos tutoriais para saber como o Azure IoT Edge pode ajudar você a implantar os serviços de nuvem do Azure para processar e analisar dados na borda.

> [!div class="nextstepaction"]
> [Funções](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [Serviço de Visão Personalizada](tutorial-deploy-custom-vision.md)
