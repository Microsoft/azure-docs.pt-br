---
title: Módulo do IoT Edge em C# para Azure Data Box Edge | Microsoft Docs
description: Saiba como desenvolver um módulo do IoT Edge em C# que pode ser implantado em seu Data Box Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/19/2019
ms.author: alkohli
ms.openlocfilehash: c2803ba598895834bb197f4a06ff0635354fcaca
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63759992"
---
# <a name="develop-a-c-iot-edge-module-to-move-files-on-data-box-edge"></a>Desenvolver um C# módulo do IoT Edge para mover arquivos de dados de caixa de borda

Este artigo instrui sobre a criação de um módulo do IoT Edge para implantação com seu dispositivo Data Box Edge. O Azure Data Box Edge é uma solução de armazenamento que permite processar dados e enviá-los pela rede para o Azure.

Você pode usar os módulos do Azure IoT Edge com seu Data Box Edge para transformar os dados conforme eles são transferidos para o Azure. O módulo usado neste artigo implementa a lógica para copiar um arquivo de um compartilhamento local para um compartilhamento na nuvem em seu dispositivo Data Box Edge.

Neste artigo, você aprenderá a:

> [!div class="checklist"]
> * Criar um registro de contêiner para armazenar e gerenciar seus módulos (imagens do Docker).
> * Criar um módulo do IoT Edge para implantar em seu dispositivo Data Box Edge.


## <a name="about-the-iot-edge-module"></a>Sobre o módulo do IoT Edge

Seu dispositivo Data Box Edge pode implantar e executar módulos do IoT Edge. Os módulos do Edge são, essencialmente, contêineres do Docker que executam uma tarefa específica, por exemplo, ingerir uma mensagem de um dispositivo, transformar uma mensagem ou enviar uma mensagem para um Hub IoT. Neste artigo, você criará um módulo que copia arquivos de um compartilhamento local para um compartilhamento na nuvem em seu dispositivo do Data Box Edge.

1. Os arquivos são gravados no compartilhamento local em seu dispositivo Data Box Edge.
2. O gerador de evento de arquivo cria um evento de arquivo para cada arquivo gravado no compartilhamento local. Os eventos de arquivo também são gerados quando um arquivo é modificado. Depois, os eventos de arquivo são enviados ao Hub IoT Edge (no tempo de execução do IoT Edge).
3. O módulo personalizado do IoT Edge processa o evento de arquivo para criar um objeto de evento de arquivo que também contém um caminho relativo para o arquivo. O módulo gera um caminho absoluto usando o caminho relativo do arquivo e copia o arquivo do compartilhamento local para o compartilhamento na nuvem. Em seguida, o módulo exclui o arquivo do compartilhamento local.

![Como o módulo do Azure IoT Edge funciona no Data Box Edge](./media/data-box-edge-create-iot-edge-module/how-module-works-1.png)

Assim que o arquivo chega ao compartilhamento na nuvem, ele é carregado automaticamente em sua conta do Armazenamento do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, verifique se você tem:

- Um dispositivo Data Box Edge em execução.

    - O dispositivo também tem um recurso do Hub IoT associado.
    - O dispositivo tem a função Computação de borda configurada.
    Para obter mais informações, acesse [configurar computação](data-box-edge-deploy-configure-compute.md#configure-compute) para a borda da caixa de dados.

- Os seguintes recursos de desenvolvimento:

    - [Visual Studio Code](https://code.visualstudio.com/).
    - [C# para extensão do Visual Studio Code (com OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).
    - [Extensão do Azure IoT Edge para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge).
    - [SDK do .NET Core 2.1](https://www.microsoft.com/net/download).
    - [CE do Docker](https://store.docker.com/editions/community/docker-ce-desktop-windows). Talvez você precise criar uma conta para baixar e instalar o software.

## <a name="create-a-container-registry"></a>Criar um registro de contêiner

Um registro de contêiner do Azure é um registro particular do Docker no Azure no qual você pode armazenar e gerenciar suas imagens de contêiner particulares do Docker. Os dois serviços de registro populares do Docker disponíveis na nuvem são o Registro de Contêiner do Azure e o Hub do Docker. Este artigo usa o Registro de Contêiner.

1. Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).
2. Selecione **Criar um recurso > Contêineres > Registro de Contêiner**. Clique em **Criar**.
3. Forneça:

   1. Um **Nome do registro** exclusivo no Azure que contenha de 5 a 50 caracteres alfanuméricos.
   2. Escolha uma **Assinatura**.
   3. Escolha um **Grupo de recursos** existente ou crie um novo.
   4. Selecione um **Local**. Recomendamos que esse local seja o mesmo que está associado ao recurso do Data Box Edge.
   5. Alterne **Usuário administrador** para **Ativar**.
   6. Defina o SKU como **Básico**.

      ![Criar um registro de contêiner](./media/data-box-edge-create-iot-edge-module/create-container-registry-1.png)
 
4. Selecione **Criar**.
5. Depois que o registro de contêiner for criado, navegue até ele e selecione **Chaves de acesso**.

    ![Obter chaves de acesso](./media/data-box-edge-create-iot-edge-module/get-access-keys-1.png)
 
6. Copie os valores para **Servidor de logon**, **Nome de usuário** e **Senha**. Você usará esses valores mais tarde para publicar a imagem do Docker no registro e adicionar as credenciais de registro ao tempo de execução do Azure IoT Edge.


## <a name="create-an-iot-edge-module-project"></a>Criar um projeto de módulo do IoT Edge

As etapas a seguir criam um projeto de módulo do IoT Edge com base no SDK do .NET Core 2.1. O projeto usa o Visual Studio Code e a extensão do Azure IoT Edge.

### <a name="create-a-new-solution"></a>Criar uma nova solução

Crie um modelo de solução de C# que possa ser personalizado com seu próprio código.

1. No Visual Studio Code, selecione **Exibir > Paleta de Comandos** para abrir a paleta de comandos do VS Code.
2. Na paleta de comandos, insira e execute o comando **Azure: Entrar** e siga as instruções para entrar na conta do Azure. Se já tiver entrado, pode ignorar esta etapa.
3. Na paleta de comandos, digite e execute o comando **Azure IoT Edge: nova solução do IoT Edge**. Na paleta de comandos, forneça as seguintes informações para criar sua solução:

    1. Selecione a pasta na qual deseja criar a solução.
    2. Forneça um nome para a solução ou aceite o padrão **EdgeSolution**.
    
        ![Criar nova solução 1](./media/data-box-edge-create-iot-edge-module/create-new-solution-1.png)

    3. Escolha **Módulo C#** como o modelo de módulo.
    4. Substitua o nome do módulo padrão pelo nome que você deseja atribuir, neste caso, é **FileCopyModule**.
    
        ![Criar nova solução 2](./media/data-box-edge-create-iot-edge-module/create-new-solution-2.png)

    5. Especifique o registro de contêiner que você criou na seção anterior como o repositório de imagens do primeiro módulo. Substitua **localhost:5000** pelo valor de servidor de logon que você copiou.

        A cadeia de caracteres final se parece com `<Login server name>/<Module name>`. Nesse exemplo, a cadeia de caracteres é: `mycontreg2.azurecr.io/filecopymodule`.

        ![Criar nova solução 3](./media/data-box-edge-create-iot-edge-module/create-new-solution-3.png)

4. Acesse **Arquivo > Abrir Pasta**.

    ![Criar nova solução 4](./media/data-box-edge-create-iot-edge-module/create-new-solution-4.png)

5. Procure e aponte para a pasta **EdgeSolution** que você criou anteriormente. A janela do VS Code carrega seu espaço de trabalho da solução IoT Edge com seus cinco componentes de nível superior. Você não editará a pasta **.vscode**, o arquivo **.gitignore**, o arquivo **.env** e o **deployment.template.json** neste artigo.
    
    O único componente que você modificará é a pasta de módulos. Essa pasta tem o código C# do módulo e os arquivos do Docker para compilar seu módulo como uma imagem de contêiner.

    ![Criar nova solução 5](./media/data-box-edge-create-iot-edge-module/create-new-solution-5.png)

### <a name="update-the-module-with-custom-code"></a>Atualizar o módulo com código personalizado

1. No VS Code explorer, abra **módulos > FileCopyModule > Program.cs**.
2. Na parte superior do **namespace FileCopyModule**, adicione as seguinte instruções using aos tipos usados posteriormente. **Microsoft.Azure.Devices.Client.Transport.Mqtt** é um protocolo para envio de mensagens para o Hub IoT Edge.

    ```
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

4. Adicione a classe **MessageBody** à classe Program. Essas classes definem o esquema esperado para o corpo de mensagens de entrada.

    ```
    /// <summary>
    /// The MessageBody class defines the expected schema for the body of incoming messages. 
    /// </summary>
    private class FileEvent
    {
        public string ChangeType { get; set; }

        public string ShareRelativeFilePath { get; set; }

        public string ShareName { get; set; }
    }
    ```

5. No método **Init**, o código cria e configura um objeto **ModuleClient**. Esse objeto permite que o módulo se conecte ao tempo de execução do Azure IoT Edge local usando o protocolo MQTT para enviar e receber mensagens. A cadeia de caracteres de conexão utilizada no método Init é fornecida ao módulo pelo tempo de execução do IoT Edge. O código registra um retorno de chamada FileCopy para receber mensagens de um hub IoT Edge por meio do ponto de extremidade **input1**.

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

6. Insira o código para **FileCopy**.

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

## <a name="build-your-iot-edge-solution"></a>Criar solução IoT Edge

Na seção anterior, você criou uma solução do IoT Edge e adicionou o código ao FileCopyModule para copiar arquivos do compartilhamento local para o compartilhamento na nuvem. Agora você precisa compilar a solução como uma imagem de contêiner e enviá-la por push para seu registro de contêiner.

1. No VSCode, vá para o Terminal > Novo Terminal para abrir um novo terminal integrado do Visual Studio Code.
2. Entrar no Docker, inserindo o seguinte comando no terminal integrado.

    `docker login <ACR login server> -u <ACR username>`

    Use o nome de usuário e o servidor de logon que você copiou do seu registro de contêiner. 

    ![Compilar e enviar por push a solução IoT Edge](./media/data-box-edge-create-iot-edge-module/build-iot-edge-solution-1.png)

2. Mediante solicitação, forneça a senha. Você também pode recuperar os valores do servidor de logon, do nome de usuário e da senha a partir das **Chaves de Acesso** em seu registro de contêiner no portal do Azure.
 
3. Após fornecer as credenciais, envie sua imagem de módulo por push para o registro de contêiner do Azure. No explorador do VS Code, clique com o botão direito do mouse no arquivo **module.json** e selecione **Compilar e enviar por push solução IoT Edge**.

    ![Compilar e enviar por push a solução IoT Edge](./media/data-box-edge-create-iot-edge-module/build-iot-edge-solution-2.png)
 
    Quando você pedir ao Visual Studio Code que desenvolva sua solução, ele executará dois comandos no terminal integrado: docker build e docker push. Esses dois comandos compilam seu código, conteinerizam o CSharpModule.dll e enviam o código por push para o registro de contêiner especificado ao inicializar a solução.

    Você será solicitado a escolher a plataforma de módulo. Selecione o *amd64* correspondente para o Linux.

    ![Selecionar plataforma](./media/data-box-edge-create-iot-edge-module/select-platform.png)

    > [!IMPORTANT] 
    > Somente os módulos do Linux têm suporte.

    Talvez você veja o seguinte aviso, mas pode ignorá-lo:

    *Program.cs(77,44): aviso CS1998: O método assíncrono não possui operadores 'await' e será executado de forma síncrona. É recomendável o uso do operador "await" para aguardar chamadas à API desbloqueadas ou do operador "await Task.Run(...)" para realizar um trabalho associado à CPU em um thread em segundo plano.*

4. Você pode conferir o endereço de imagem de contêiner completo com marca no terminal integrado do VS Code. O endereço da imagem é criado a partir de informações que estão no arquivo module.json com o formato `<repository>:<version>-<platform>`. Para este artigo, ele deve se parecer com `mycontreg2.azurecr.io/filecopymodule:0.0.1-amd64`.

## <a name="next-steps"></a>Próximos passos

Para implantar e executar esse módulo na borda da caixa de dados, consulte as etapas em [adicionar um módulo](data-box-edge-deploy-configure-compute.md#add-a-module).
