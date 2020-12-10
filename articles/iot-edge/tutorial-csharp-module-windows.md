---
title: Tutorial – Desenvolver um módulo C# para o Windows usando o Azure IoT Edge
description: Este tutorial mostra como criar um módulo do IoT Edge com código em C# e implantá-lo em um dispositivo Windows IoT Edge.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/03/2020
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, amqp, devx-track-csharp
ms.openlocfilehash: 46d3a9e961be0717aba75c1f5845b97b52092510
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/09/2020
ms.locfileid: "96931955"
---
# <a name="tutorial-develop-a-c-iot-edge-module-for-windows-devices"></a>Tutorial: Desenvolver um módulo do IoT Edge em C# para dispositivos Windows

Use o Visual Studio para desenvolver código em C# e implantá-lo em um dispositivo Windows que está executando o Azure IoT Edge.

Use os módulos do Azure IoT Edge para implantar um código que implementa a lógica de negócios diretamente em seus dispositivos IoT Edge. Este tutorial o orienta através da criação e implantação de um módulo IoT Edge que filtra os dados do sensor. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
>
> * Use o Visual Studio para criar um módulo do IoT Edge baseado no SDK do C#.
> * Use o Visual Studio e o Docker para criar uma imagem do Docker e publicá-la no registro.
> * Implantar o módulo no dispositivo IoT Edge.
> * Exibir os dados gerados.

O módulo IoT Edge que criado neste tutorial filtra os dados de temperatura gerados pelo seu dispositivo. Ele somente envia mensagens upstream se a temperatura estiver acima de um limite especificado. Esse tipo de análise na borda é útil para reduzir a quantidade de dados que é comunicada e armazenada na nuvem.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial demonstra como desenvolver um módulo em **C#** usando o **Visual Studio 2019** e implantá-lo em um **dispositivo Windows**. Se você estiver desenvolvendo módulos para dispositivos Linux, acesse [Desenvolver um módulo do IoT Edge em C# para dispositivos Linux](tutorial-csharp-module.md).

Use a tabela a seguir para entender as opções para desenvolver e implantar módulos do C# em dispositivos Windows:

| C# | Visual Studio Code | Visual Studio 2017/2019 |
| -- | ------------------ | ------------------ |
| **Desenvolvimento do Windows AMD64** | ![Desenvolver módulos em C# para WinAMD64 no VS Code](./media/tutorial-c-module/green-check.png) | ![Desenvolver módulos em C# para WinAMD64 no Visual Studio](./media/tutorial-c-module/green-check.png) |
| **Depuração do Windows AMD64** |   | ![Depurar módulos em C# para WinAMD64 no Visual Studio](./media/tutorial-c-module/green-check.png) |

Antes de iniciar este tutorial, você deve ter passado pelo tutorial anterior para configurar seu ambiente de desenvolvimento, [Desenvolver um módulo do IoT Edge para um dispositivo Windows](tutorial-develop-for-windows.md). Após concluir esse tutorial, você já deverá ter os seguintes pré-requisitos:

* Um [Hub IoT](../iot-hub/iot-hub-create-through-portal.md) na camada padrão ou gratuito no Azure.
* Um [dispositivo Windows que executa o Azure IoT Edge](quickstart.md).
* Um registro de contêiner, como o [Registro de Contêiner do Azure](../container-registry/index.yml).
* [Visual Studio 2019](/visualstudio/install/install-visual-studio) configurado com a extensão [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools).
* [Docker Desktop](https://docs.docker.com/docker-for-windows/install/) configurado para executar contêineres do Windows.

> [!TIP]
> Se estiver usando o Visual Studio 2017 (versão 15.7 ou superior), baixe e instale o [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) para VS 2017 no Visual Studio Marketplace

## <a name="create-a-module-project"></a>Criar um projeto de módulo

As etapas a seguir criam um projeto de módulo do IoT Edge usando o Visual Studio e a extensão Ferramentas do Azure IoT Edge. Quando o modelo de projeto for criado, adicione o novo código para que o módulo filtre as mensagens com base nas propriedades relatadas.

### <a name="create-a-new-project"></a>Criar um novo projeto

O Azure IoT Edge Tools fornece modelos de projeto para todas as linguagens de módulo do IoT Edge compatíveis com o Visual Studio. Todos esses modelos têm os arquivos e o código nos quais você precisa implantar um módulo de trabalho para testar o IoT Edge ou oferecem a você um ponto de partida para personalizar o modelo com sua própria lógica de negócios.

1. Inicie o Visual Studio 2019 e selecione **Criar Projeto**.

2. Pesquise **IoT Edge** e escolha o projeto **Azure IoT Edge (Windows amd64)** . Clique em **Próximo**.

   ![Criar um projeto do Azure IoT Edge](./media/tutorial-csharp-module-windows/new-project.png)

3. Renomeie o projeto e a solução para algo descritivo como **CSharpTutorialApp**. Clique em **Criar** para criar o projeto.

   ![Configurar um novo projeto do Azure IoT Edge](./media/tutorial-csharp-module-windows/configure-project.png)

4. Configure o projeto com os seguintes valores:

   | Campo | Valor |
   | ----- | ----- |
   | Selecione um modelo | Selecione **Módulo em C#** . |
   | Nome do projeto de módulo | Nomeie o módulo **CSharpModule**. |
   | Repositório de imagens do Docker | Um repositório de imagem inclui o nome do registro de contêiner e o nome da imagem de contêiner. Sua imagem de contêiner é populada previamente com base no valor de nome do projeto de módulo. Substitua **localhost:5000** pelo valor do **Servidor de logon** do seu Registro de Contêiner do Azure. Você pode recuperar o servidor de Logon da página Visão Geral do seu registro de contêiner no portal do Azure. <br><br> O repositório de imagens final se parece com \<registry name\>.azurecr.io/csharpmodule. |

   ![Configure seu projeto para o dispositivo de destino, tipo de módulo e registro de contêiner](./media/tutorial-csharp-module-windows/add-application-and-module.png)

5. Selecione **Adicionar** para criar o projeto.

### <a name="add-your-registry-credentials"></a>Adicionar suas credenciais de registro

O manifesto de implantação compartilha as credenciais para seu registro de contêiner com o runtime do IoT Edge. O runtime precisa dessas credenciais para efetuar pull de imagens privadas para o dispositivo IoT Edge. Use as credenciais da seção **Chaves de acesso** do Registro de Contêiner do Azure.

1. No gerenciador de soluções do Visual Studio, abra o arquivo **deployment.template.json**.

2. Localize a propriedade **registryCredentials** nas propriedades desejadas do $edgeAgent. Ele deve ter seu endereço de registro preenchido automaticamente com base nas informações fornecidas durante a criação do projeto e os campos de nome de usuário e senha devem conter nomes de variáveis. Por exemplo:

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "$CONTAINER_REGISTRY_USERNAME_<registry name>",
       "password": "$CONTAINER_REGISTRY_PASSWORD_<registry name>",
       "address": "<registry name>.azurecr.io"
     }
   }
   ```

3. Abra o arquivo **.env** em sua solução de módulo. (Está oculto por padrão no Gerenciador de Soluções; portanto, pode ser necessário selecionar o botão **Mostrar Todos os Arquivos** para exibi-lo.) O arquivo .env deve conter as mesmas variáveis de nome de usuário e senha que você viu no arquivo deployment.template.json.

4. Adicione os valores de **Nome de usuário** e **Senha** de seu Registro de Contêiner do Azure.

5. Salve suas alterações no arquivo .env.

### <a name="update-the-module-with-custom-code"></a>Atualizar o módulo com código personalizado

O código padrão do módulo recebe mensagens em uma fila de entrada e as passa adiante por meio de sua fila de saída. Vamos adicionar algum código adicional para que o módulo processe as mensagens na borda antes de encaminhá-las ao Hub IoT. Atualize o módulo para que ele analise os dados de temperatura em cada mensagem e envie apenas a mensagem ao Hub IoT se a temperatura exceder determinado limite.

1. No Visual Studio, abra **CSharpModule** > **Program.cs**.

2. Na parte superior do namespace **CSharpModule**, adicione três instruções **using** aos tipos usados posteriormente:

    ```csharp
    using System.Collections.Generic;     // For KeyValuePair<>
    using Microsoft.Azure.Devices.Shared; // For TwinCollection
    using Newtonsoft.Json;                // For JsonConvert
    ```

3. Adicione a variável **temperatureThreshold** à classe **Program** após a variável de contador. A variável temperatureThreshold define o valor que a temperatura medida deve exceder para os dados a serem enviados para o Hub IoT.

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

4. Adicione as classes **MessageBody**, **Machine** e **Ambient** à classe **Program** após as declarações de variável. Essas classes definem o esquema esperado para o corpo de mensagens de entrada.

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

5. Localize o método **Init**. Esse método cria e configura um objeto **ModuleClient**, que permite que o módulo se conecte ao runtime do Azure IoT Edge para enviar e receber mensagens. O código também registra um retorno de chamada para receber mensagens de um hub do IoT Edge por meio do ponto de extremidade **input1**.

   Substitua todo o método Init pelo seguinte código:

   ```csharp
   static async Task Init()
   {
       AmqpTransportSettings amqpSetting = new AmqpTransportSettings(TransportType.Amqp_Tcp_Only);
       ITransportSettings[] settings = { amqpSetting };

       // Open a connection to the Edge runtime
       ModuleClient ioTHubModuleClient = await ModuleClient.CreateFromEnvironmentAsync(settings);
       await ioTHubModuleClient.OpenAsync();
       Console.WriteLine("IoT Hub module client initialized.");

       // Read the TemperatureThreshold value from the module twin's desired properties
       var moduleTwin = await ioTHubModuleClient.GetTwinAsync();
       await OnDesiredPropertiesUpdate(moduleTwin.Properties.Desired, ioTHubModuleClient);

       // Attach a callback for updates to the module twin's desired properties.
       await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertiesUpdate, null);

       // Register a callback for messages that are received by the module.
       await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
   }
   ```

   Esse método Init atualizado ainda configura a conexão com o runtime do IoT Edge com o ModuleClient, mas também adiciona uma nova funcionalidade. Ele lê as propriedades desejadas do módulo gêmeo para recuperar o valor **temperatureThreshold**. Em seguida, cria um retorno de chamada que escuta, para as atualizações futuras, as propriedades desejadas do módulo gêmeo. Com esse retorno de chamada, é possível atualizar o limite de temperatura no módulo gêmeo remotamente e as alterações serão incorporadas ao módulo.

   O método Init atualizado também altera o método **SetInputMessageHandlerAsync** existente. No código de exemplo, as mensagens de entrada em *input1* são processadas com a função *PipeMessage*, mas podemos alterar isso para usar a função *FilterMessages* que criaremos nas etapas a seguir.

6. Adicione um novo método **onDesiredPropertiesUpdate** à classe **Program**. Este método recebe atualizações sobre as propriedades desejadas do módulo gêmeo e atualiza a variável **temperatureThreshold** para corresponder. Todos os módulos possuem seu próprio módulo gêmeo, o que permite configurar o código em execução dentro de um módulo diretamente da nuvem.

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

7. Remova o método de exemplo **PipeMessage** e substitua-o por um novo método **FilterMessages**. Esse método é chamado sempre que o módulo recebe uma mensagem do hub do IoT Edge. Ele filtra as mensagens que reportam temperaturas abaixo do limite de temperatura definido através do módulo gêmeo. Ele também adiciona a propriedade **MessageType** à mensagem com o valor definido para **Alerta**.

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
                using(var filteredMessage = new Message(messageBytes))
                {
                    foreach (KeyValuePair<string, string> prop in message.Properties)
                    {
                        filteredMessage.Properties.Add(prop.Key, prop.Value);
                    }

                    filteredMessage.Properties.Add("MessageType", "Alert");
                    await moduleClient.SendEventAsync("output1", filteredMessage);
                }
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

9. Abra o arquivo **deployment.template.json** em sua solução do IoT Edge. Esse arquivo informa ao agente do IoT Edge quais módulos implantar, nesse caso, **SimulatedTemperatureSensor** e **CSharpModule**, e informa ao hub do IoT Edge como rotear mensagens entre eles.

10. Adicione o módulo gêmeo **CSharpModule** no manifesto de implantação. Insira o seguinte conteúdo JSON na parte inferior da seção **modulesContent**, após o módulo gêmeo do **$edgeHub**:

    ```json
       "CSharpModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
    ```

    ![Adicionar gêmeo de módulo ao modelo de implantação](./media/tutorial-csharp-module-windows/module-twin.png)

11. Salve o arquivo deployment.template.json.

## <a name="build-and-push-your-module"></a>Criar e enviar seu módulo por push

Na seção anterior, você criou uma solução IoT Edge e adicionou um código a **CSharpModule** para filtrar mensagens em que a temperatura relatada da máquina estiver abaixo do limite aceitável. Agora você precisa compilar a solução como uma imagem de contêiner e enviá-la por push para seu registro de contêiner.

1. Use o seguinte comando para entrar no Docker em seu computador de desenvolvimento. Use o nome de usuário, senha e servidor de logon do seu Registro de Contêiner do Azure. É possível recuperar esses valores na seção **Chaves de acesso** no registro do portal do Azure.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Talvez você receba um aviso de segurança recomendando usar `--password-stdin`. Embora essa prática seja recomendada para cenários de produção, ela não serve para este tutorial. Para saber mais, confira a referência de [logon do docker](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin).

2. No gerenciador de soluções do Visual Studio, clique com o botão direito do mouse no nome do projeto que você deseja criar. O nome padrão é **AzureIotEdgeApp1**, e, uma vez que você está criando um módulo do Windows, a extensão deve ser **Windows.Amd64**.

3. Selecione **Compilar e Efetuar Push de Módulos do IoT Edge**.

   O comando de criação e de envio por push inicia três operações. Primeiro, é criada uma pasta na solução denominada **config** que contém o manifesto de implantação completo, criado com base nas informações do modelo de implantação e em outros arquivos da solução. Depois, ele executa `docker build` para montar a imagem de contêiner com base no dockerfile apropriado para sua arquitetura de destino. Por fim, ele executa `docker push` para enviar por push o repositório de imagens para seu registro de contêiner.

   Esse processo pode levar vários minutos na primeira vez, mas será mais rápido na próxima vez em que você executar os comandos.

## <a name="deploy-modules-to-device"></a>Implantar módulos no dispositivo

Use o Cloud Explorer do Visual Studio e a extensão Azure IoT Edge Tools para implantar o projeto de módulo em seu dispositivo IoT Edge. Você já tem um manifesto de implantação preparado para seu cenário, o arquivo **deployment.windows-amd64.json** na pasta config. Agora, tudo o que você precisa fazer é selecionar um dispositivo para receber a implantação.

Verifique se seu dispositivo IoT Edge está em funcionamento.

1. No Cloud Explorer do Visual Studio, expanda os recursos para conferir sua lista de dispositivos IoT.

2. Clique com o botão direito do mouse no nome do dispositivo IoT Edge no qual você deseja receber a implantação.

3. Selecione **Criar Implantação**.

4. No explorador de arquivos, selecione o arquivo **deployment.windows-amd64.json** na pasta config de sua solução.

5. Atualize o Cloud Explorer para conferir os módulos implantados e listados em seu dispositivo.

## <a name="view-generated-data"></a>Exibir os dados gerados

Depois que você aplica o manifesto de implantação no seu dispositivo IoT Edge, o runtime do IoT Edge no dispositivo coleta as novas informações de implantação e inicia a execução nele. Todos os módulos em execução no dispositivo que não estão incluídos no manifesto de implantação são interrompidos. Todos os módulos ausentes do dispositivo são iniciados.

É possível usar a extensão Ferramentas do IoT Edge para exibir as mensagens conforme elas chegam ao Hub IoT.

1. No Cloud Explorer do Visual Studio, selecione o nome do seu dispositivo IoT Edge.

2. Na lista **Ações**, selecione **Iniciar o Monitoramento do Ponto de Extremidade de Evento Interno**.

3. Exiba as mensagens que chegam ao seu Hub IoT. Pode levar um tempo para as mensagens chegarem, porque as alterações que fizemos no código CSharpModule aguardam até que a temperatura do computador atinja 25 graus antes de enviar mensagens. Também é adicionado o tipo de mensagem **Alerta** às mensagens que atingem esse limite de temperatura.

   ![Exibir as mensagens que chegam ao Hub IoT](./media/tutorial-csharp-module-windows/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>Editar o módulo gêmeo

Usamos o módulo gêmeo CSharpModule para definir o limite de temperatura em 25 graus. É possível usar o módulo gêmeo para alterar a funcionalidade sem precisar atualizar o código do módulo.

1. No Visual Studio, abra o arquivo **deployment.windows-amd64.json**. (Não o arquivo deployment.template. Se você não vir o manifesto de implantação no arquivo de configuração no gerenciador de soluções, selecione o ícone **Mostrar todos os arquivos** na barra de ferramentas do gerenciador.)

2. Localize o CSharpModule gêmeo e altere o valor do parâmetro **temperatureThreshold** para uma nova temperatura de 5 a 10 graus mais alta do que a última temperatura relatada.

3. Salve o arquivo **deployment.windows-amd64.json**.

4. Siga as etapas de implantação novamente para aplicar o manifesto de implantação atualizado ao seu dispositivo.

5. Monitore as mensagens do dispositivo para nuvem recebidas. Você deve ver uma interrupção das mensagens até que o novo limite de temperatura seja atingido.

## <a name="clean-up-resources"></a>Limpar os recursos

Se você pretende continuar no próximo artigo recomendado, pode manter os recursos e as configurações já criados e reutilizá-los. Você também pode continuar usando o mesmo dispositivo IoT Edge como um dispositivo de teste.

Caso contrário, é possível excluir as configurações locais e os recursos do Azure usados neste artigo para evitar encargos.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou um módulo do IoT Edge com código para filtrar os dados brutos gerados pelo seu dispositivo IoT Edge.

É possível passar para os próximos tutoriais para saber como o Azure IoT Edge pode ajudar você a implantar os serviços de nuvem do Azure para processar e analisar dados na borda.

> [!div class="nextstepaction"]
> [Funções](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [Serviço de Visão Personalizada](tutorial-deploy-custom-vision.md)
