---
title: Módulo de IoT Edge C# para Azure Stack Edge pro com GPU | Microsoft Docs
description: Saiba como desenvolver um módulo de IoT Edge em C# que pode ser implantado em seu dispositivo de GPU pro Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/05/2021
ms.author: alkohli
ms.openlocfilehash: d2ab96fcfa33301f0bd1212b23f9418fa39d0134
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2021
ms.locfileid: "102637660"
---
# <a name="develop-a-c-iot-edge-module-to-move-files-on-azure-stack-edge-pro"></a>Desenvolver um módulo de IoT Edge em C# para mover arquivos no Azure Stack Edge pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Este artigo explica como criar um módulo IoT Edge para implantação com seu dispositivo Azure Stack Edge pro. O Azure Stack Edge pro é uma solução de armazenamento que permite processar dados e enviá-los pela rede para o Azure.

Você pode usar os módulos Azure IoT Edge com o Azure Stack Edge pro para transformar os dados conforme movidos para o Azure. O módulo usado neste artigo implementa a lógica para copiar um arquivo de um compartilhamento local para um compartilhamento de nuvem em seu dispositivo Azure Stack Edge pro.

Neste artigo, você aprenderá como:

> [!div class="checklist"]
> * Criar um registro de contêiner para armazenar e gerenciar seus módulos (imagens do Docker).
> * Crie um módulo IoT Edge para implantar em seu dispositivo do Azure Stack Edge pro.


## <a name="about-the-iot-edge-module"></a>Sobre o módulo do IoT Edge

O dispositivo pro Edge Azure Stack pode implantar e executar módulos IoT Edge. Os módulos do Edge são, essencialmente, contêineres do Docker que executam uma tarefa específica, por exemplo, ingerir uma mensagem de um dispositivo, transformar uma mensagem ou enviar uma mensagem para um Hub IoT. Neste artigo, você criará um módulo que copia arquivos de um compartilhamento local para um compartilhamento de nuvem em seu dispositivo Azure Stack Edge pro.

1. Os arquivos são gravados no compartilhamento local no dispositivo Azure Stack Edge pro.
2. O gerador de evento de arquivo cria um evento de arquivo para cada arquivo gravado no compartilhamento local. Os eventos de arquivo também são gerados quando um arquivo é modificado. Depois, os eventos de arquivo são enviados ao Hub IoT Edge (no runtime do IoT Edge).
3. O módulo personalizado do IoT Edge processa o evento de arquivo para criar um objeto de evento de arquivo que também contém um caminho relativo para o arquivo. O módulo gera um caminho absoluto usando o caminho relativo do arquivo e copia o arquivo do compartilhamento local para o compartilhamento na nuvem. Em seguida, o módulo exclui o arquivo do compartilhamento local.

![Como Azure IoT Edge módulo funciona no Azure Stack Edge pro](./media/azure-stack-edge-gpu-create-iot-edge-module/how-module-works-1.png)

Assim que o arquivo chega ao compartilhamento na nuvem, ele é carregado automaticamente em sua conta do Armazenamento do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, verifique se você tem:

- Um dispositivo Azure Stack Edge pro que está em execução.

    - O dispositivo também tem um recurso do Hub IoT associado.
    - O dispositivo tem a função Computação de borda configurada.
    Para obter mais informações, acesse [Configurar a computação](azure-stack-edge-j-series-deploy-configure-compute.md#configure-compute) para o Azure Stack Edge pro.<!--Update link?-->

- Os seguintes recursos de desenvolvimento:

    - [Visual Studio Code](https://code.visualstudio.com/).
    - [C# para extensão do Visual Studio Code (com OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).
    - [Extensão de Azure IOT Edge para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge).
    - [SDK do .NET Core 2.1](https://www.microsoft.com/net/download).
    - [Docker CE](https://store.docker.com/editions/community/docker-ce-desktop-windows). Talvez você precise criar uma conta para baixar e instalar o software.

## <a name="create-a-container-registry"></a>Criar um registro de contêiner

Um registro de contêiner do Azure é um registro particular do Docker no Azure no qual você pode armazenar e gerenciar suas imagens de contêiner particulares do Docker. Os dois serviços de registro populares do Docker disponíveis na nuvem são o Registro de Contêiner do Azure e o Hub do Docker. Este artigo usa o Registro de Contêiner.

1. Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).
2. Selecione **Criar um recurso > Contêineres > Registro de Contêiner**. Clique em **Criar**.
3. Forneça:

   1. Um **Nome do registro** exclusivo no Azure que contenha de 5 a 50 caracteres alfanuméricos.
   2. Escolha uma **assinatura**.
   3. Escolha um **Grupo de recursos** existente ou crie um novo.
   4. Selecione um **Local**. É recomendável que esse local seja o mesmo associado ao recurso de borda do Azure Stack.
   5. Alterne **Usuário administrador** para **Ativar**.
   6. Defina o SKU como **Básico**.

      ![Criar um registro de contêiner](./media/azure-stack-edge-gpu-create-iot-edge-module/create-container-registry-1.png)
 
4. Selecione **Criar**.
5. Depois que o registro de contêiner for criado, navegue até ele e selecione **Chaves de acesso**.

    ![Obter chaves de acesso](./media/azure-stack-edge-gpu-create-iot-edge-module/get-access-keys-1.png)
 
6. Copie os valores para **Servidor de logon**, **Nome de usuário** e **Senha**. Você usará esses valores mais tarde para publicar a imagem do Docker no registro e adicionar as credenciais de registro ao runtime do Azure IoT Edge.


## <a name="create-an-iot-edge-module-project"></a>Criar um projeto de módulo do IoT Edge

As etapas a seguir criam um projeto de módulo do IoT Edge com base no SDK do .NET Core 2.1. O projeto usa o Visual Studio Code e a extensão do Azure IoT Edge.

### <a name="create-a-new-solution"></a>Criar uma nova solução

Crie um modelo de solução de C# que possa ser personalizado com seu próprio código.

1. No Visual Studio Code, selecione **Exibir > Paleta de Comandos** para abrir a paleta de comandos do VS Code.
2. Na paleta de comandos, insira e execute o comando **Azure: Entrar** e siga as instruções para entrar na conta do Azure. Se já tiver entrado, pode ignorar esta etapa.
3. Na paleta de comandos, digite e execute o comando **Azure IoT Edge: Nova solução do IoT Edge**. Na paleta de comandos, forneça as seguintes informações para criar sua solução:

    1. Selecione a pasta na qual deseja criar a solução.
    2. Forneça um nome para a solução ou aceite o padrão **EdgeSolution**.
    
        ![Criar nova solução 1](./media/azure-stack-edge-gpu-create-iot-edge-module/create-new-solution-1.png)

    3. Escolha **módulo C#** como o modelo de módulo.
    4. Substitua o nome do módulo padrão pelo nome que você deseja atribuir, neste caso, é **FileCopyModule**.
    
        ![Criar nova solução 2](./media/azure-stack-edge-gpu-create-iot-edge-module/create-new-solution-2.png)

    5. Especifique o registro de contêiner que você criou na seção anterior como o repositório de imagens do primeiro módulo. Substitua **localhost:5000** pelo valor de servidor de logon que você copiou.

        A cadeia de caracteres final se parece com `<Login server name>/<Module name>`. Nesse exemplo, a cadeia de caracteres é: `mycontreg2.azurecr.io/filecopymodule`.

        ![Criar nova solução 3](./media/azure-stack-edge-gpu-create-iot-edge-module/create-new-solution-3.png)

4. Acesse **Arquivo > Abrir Pasta**.

    ![Criar nova solução 4](./media/azure-stack-edge-gpu-create-iot-edge-module/create-new-solution-4.png)

5. Procure e aponte para a pasta **EdgeSolution** que você criou anteriormente. A janela do VS Code carrega seu workspace da solução IoT Edge com seus cinco componentes de nível superior. Você não Editará a `.vscode` pasta, o arquivo **. gitignore** , o arquivo **. env** e o deployment.template.jsem * * neste artigo.
    
    O único componente que você modificará é a pasta de módulos. Essa pasta tem o código C# do módulo e os arquivos do Docker para compilar seu módulo como uma imagem de contêiner.

    ![Criar nova solução 5](./media/azure-stack-edge-gpu-create-iot-edge-module/create-new-solution-5.png)

### <a name="update-the-module-with-custom-code"></a>Atualizar o módulo com código personalizado

1. No VS Code Explorer, abra **modules > FileCopyModule > Program.cs**.
2. Na parte superior do **namespace FileCopyModule**, adicione as seguinte instruções using aos tipos usados posteriormente. **Microsoft.Azure.Devices.Client.Transport.Mqtt** é um protocolo para envio de mensagens para o Hub IoT Edge.

    ```
    namespace FileCopyModule
    {
        using Microsoft.Azure.Devices.Client.Transport.Mqtt;
        using Newtonsoft.Json;
    ```
3. Adicione a variável **InputFolderPath** e **OutputFolderPath** à classe Program.

    ```
    class Program
        {
            static int counter;
            private const string InputFolderPath = "/home/input";
            private const string OutputFolderPath = "/home/output";
    ```

4. Imediatamente após a etapa anterior, adicione a classe **fileevent** para definir o corpo da mensagem.

    ```
    /// <summary>
    /// The FileEvent class defines the body of incoming messages. 
    /// </summary>
    private class FileEvent
    {
        public string ChangeType { get; set; }

        public string ShareRelativeFilePath { get; set; }

        public string ShareName { get; set; }
    }
    ```

5. No **método Init**, o código cria e configura um objeto **ModuleClient** . Esse objeto permite que o módulo se conecte ao runtime do Azure IoT Edge local usando o protocolo MQTT para enviar e receber mensagens. A cadeia de caracteres de conexão utilizada no método Init é fornecida ao módulo pelo runtime do IoT Edge. O código registra um retorno de chamada FileCopy para receber mensagens de um hub IoT Edge por meio do ponto de extremidade **input1**. Substitua o **método Init** pelo código a seguir.

    ```
    /// <summary>
    /// Initializes the ModuleClient and sets up the callback to receive
    /// messages containing file event information
    /// </summary>
    static async Task Init()
    {
        MqttTransportSettings mqttSetting = new MqttTransportSettings(TransportType.Mqtt_Tcp_Only);
        ITransportSettings[] settings = { mqttSetting };

        // Open a connection to the IoT Edge runtime
        ModuleClient ioTHubModuleClient = await ModuleClient.CreateFromEnvironmentAsync(settings);
        await ioTHubModuleClient.OpenAsync();
        Console.WriteLine("IoT Hub module client initialized.");

        // Register callback to be called when a message is received by the module
        await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FileCopy, ioTHubModuleClient);
    }
    ```

6. Remova o código para o **método PipeMessage** e, em seu lugar, insira o código para **FileCopy**.

    ```
        /// <summary>
        /// This method is called whenever the module is sent a message from the IoT Edge Hub.
        /// This method deserializes the file event, extracts the corresponding relative file path, and creates the absolute input file path using the relative file path and the InputFolderPath.
        /// This method also forms the absolute output file path using the relative file path and the OutputFolderPath. It then copies the input file to output file and deletes the input file after the copy is complete.
        /// </summary>
        static async Task<MessageResponse> FileCopy(Message message, object userContext)
        {
            int counterValue = Interlocked.Increment(ref counter);

            try
            {
                byte[] messageBytes = message.GetBytes();
                string messageString = Encoding.UTF8.GetString(messageBytes);
                Console.WriteLine($"Received message: {counterValue}, Body: [{messageString}]");

                if (!string.IsNullOrEmpty(messageString))
                {
                    var fileEvent = JsonConvert.DeserializeObject<FileEvent>(messageString);

                    string relativeFileName = fileEvent.ShareRelativeFilePath.Replace("\\", "/");
                    string inputFilePath = InputFolderPath + relativeFileName;
                    string outputFilePath = OutputFolderPath + relativeFileName;

                    if (File.Exists(inputFilePath))                
                    {
                        Console.WriteLine($"Moving input file: {inputFilePath} to output file: {outputFilePath}");
                        var outputDir = Path.GetDirectoryName(outputFilePath);
                        if (!Directory.Exists(outputDir))
                        {
                            Directory.CreateDirectory(outputDir);
                        }

                        File.Copy(inputFilePath, outputFilePath, true);
                        Console.WriteLine($"Copied input file: {inputFilePath} to output file: {outputFilePath}");
                        File.Delete(inputFilePath);
                        Console.WriteLine($"Deleted input file: {inputFilePath}");
                    } 
                    else
                    {
                        Console.WriteLine($"Skipping this event as input file doesn't exist: {inputFilePath}");   
                    }
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine("Caught exception: {0}", ex.Message);
                Console.WriteLine(ex.StackTrace);
            }

            Console.WriteLine($"Processed event.");
            return MessageResponse.Completed;
        }
    ```

7. Salve o arquivo.
8. Você também pode [baixar um exemplo de código existente](https://azure.microsoft.com/resources/samples/data-box-edge-csharp-modules/?cdn=disable) para este projeto. Em seguida, você pode validar o arquivo que você salvou no arquivo **Program.cs** neste exemplo.

## <a name="build-your-iot-edge-solution"></a>Criar solução IoT Edge

Na seção anterior, você criou uma solução do IoT Edge e adicionou o código ao FileCopyModule para copiar arquivos do compartilhamento local para o compartilhamento na nuvem. Agora você precisa compilar a solução como uma imagem de contêiner e enviá-la por push para seu registro de contêiner.

1. Em VSCode, vá para terminal > novo terminal para abrir um novo terminal Visual Studio Code integrado.
2. Entre no Docker digitando o seguinte comando no terminal integrado.

    `docker login <ACR login server> -u <ACR username>`

    Use o nome de usuário e o servidor de logon que você copiou do seu registro de contêiner.

    ![Compilar e enviar por push a solução IoT Edge](./media/azure-stack-edge-gpu-create-iot-edge-module/build-iot-edge-solution-1.png)

2. Mediante solicitação, forneça a senha. Você também pode recuperar os valores do servidor de logon, do nome de usuário e da senha a partir das **Chaves de Acesso** em seu registro de contêiner no portal do Azure.
 
3. Após fornecer as credenciais, envie sua imagem de módulo por push para o registro de contêiner do Azure. No explorador do VS Code, clique com o botão direito do mouse no arquivo **module.json** e selecione **Compilar e enviar por push solução IoT Edge**.

    ![Criar e enviar por push IoT Edge solução 2](./media/azure-stack-edge-gpu-create-iot-edge-module/build-iot-edge-solution-2.png)
 
    Quando você pedir ao Visual Studio Code que desenvolva sua solução, ele executará dois comandos no terminal integrado: docker build e docker push. Esses dois comandos compilam seu código, conteinerizam o CSharpModule.dll e enviam o código por push para o registro de contêiner especificado ao inicializar a solução.

    Você será solicitado a escolher a plataforma de módulo. Selecione o *amd64* correspondente para o Linux.

    ![Selecionar plataforma](./media/azure-stack-edge-gpu-create-iot-edge-module/select-platform.png)

    > [!IMPORTANT] 
    > Somente os módulos do Linux têm suporte.

    Talvez você veja o seguinte aviso, mas pode ignorá-lo:

    *Program. cs (77, 44): aviso CS1998: esse método assíncrono não tem operadores ' Await ' e será executado de forma síncrona. Considere o uso do operador ' Await ' para aguardar chamadas de API que não sejam de bloqueio ou ' aguardar tarefa. executar (...) ' para fazer o trabalho associado à CPU em um thread em segundo plano.*

4. Você pode conferir o endereço de imagem de contêiner completo com marca no terminal integrado do VS Code. O endereço da imagem é criado a partir de informações que estão no arquivo module.json com o formato `<repository>:<version>-<platform>`. Para este artigo, ele deve se parecer com `mycontreg2.azurecr.io/filecopymodule:0.0.1-amd64`.

## <a name="next-steps"></a>Próximas etapas

Para implantar e executar esse módulo no Azure Stack Edge pro, consulte as etapas em [Adicionar um módulo](azure-stack-edge-j-series-deploy-configure-compute.md#add-a-module).<!--Update link?-->
