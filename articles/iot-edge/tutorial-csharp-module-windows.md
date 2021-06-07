---
title: Tutorial – Desenvolver módulos em C# para o Windows usando o Azure IoT Edge
description: Este tutorial mostra como criar módulos do IoT Edge com código C# e implantá-los em dispositivos Windows que executam o IoT Edge.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/03/2020
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, amqp, devx-track-csharp
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 84e5bbff11d0a5ff5d47ca43a3da54d1f1fb5555
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106218848"
---
# <a name="tutorial-develop-c-iot-edge-modules-using-windows-containers"></a>Tutorial: Desenvolver módulos do IoT Edge em C# usando contêineres do Windows

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Este artigo mostra como usar o Visual Studio para desenvolver código em C# e implantá-lo em um dispositivo Windows que executa o Azure IoT Edge.

>[!NOTE]
>O IoT Edge 1.1 LTS é o último canal de lançamento que dá suporte a contêineres do Windows. Começando na versão 1.2, os contêineres do Windows deixarão de ter suporte. Considere usar ou mudar para o [IoT Edge para Linux no Windows](iot-edge-for-linux-on-windows.md) para executar o IoT Edge em dispositivos Windows.

Use módulos do Azure IoT Edge para implantar um código que implementa sua lógica de negócios diretamente em seus dispositivos IoT Edge. Este tutorial o orienta através da criação e implantação de um módulo IoT Edge que filtra os dados do sensor.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
>
> * Use o Visual Studio para criar um módulo do IoT Edge baseado no SDK do C#.
> * Use o Visual Studio e o Docker para criar uma imagem do Docker e publicá-la no registro.
> * Implantar o módulo no dispositivo IoT Edge.
> * Exibir os dados gerados.

O módulo IoT Edge que criado neste tutorial filtra os dados de temperatura gerados pelo seu dispositivo. O módulo envia mensagens upstream somente quando a temperatura ultrapassa um limite especificado. Esse tipo de análise na borda é útil para reduzir a quantidade de dados que é comunicada e armazenada na nuvem.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial demonstra como desenvolver um módulo em C#usando o Visual Studio 2019 e implantá-lo em um dispositivo Windows. Se estiver desenvolvendo módulos usando contêineres Linux, acesse [Desenvolver módulos do IoT Edge em C# usando contêineres Linux](tutorial-csharp-module.md).

Para entender as opções para desenvolver e implantar módulos em C# usando contêineres Windows, confira esta tabela:

| C# | Visual&nbsp;Studio&nbsp;Code | Visual Studio 2017&nbsp;e&nbsp;2019 |
| -- | :------------------: | :------------------: |
| Desenvolvimento do Windows AMD64 | ![Desenvolver módulos em C# para WinAMD64 no Visual Studio Code](./media/tutorial-c-module/green-check.png) | ![Desenvolver módulos em C# para WinAMD64 no Visual Studio](./media/tutorial-c-module/green-check.png) |
| Depuração do Windows AMD64 |   | ![Depurar módulos em C# para WinAMD64 no Visual Studio](./media/tutorial-c-module/green-check.png) |

Antes de começar o tutorial, configure o ambiente de desenvolvimento seguindo as instruções no tutorial [Desenvolver módulos do IoT Edge usando contêineres Windows](tutorial-develop-for-windows.md). Quando você terminar, o ambiente terá os seguintes pré-requisitos:

* Um [hub IoT](../iot-hub/iot-hub-create-through-portal.md) gratuito ou de camada padrão no Azure.
* Um [dispositivo Windows que executa o Azure IoT Edge](how-to-install-iot-edge-windows-on-windows.md).
* Um registro de contêiner, como o [Registro de Contêiner do Azure](../container-registry/index.yml).
* O [Visual Studio 2019](/visualstudio/install/install-visual-studio) configurado com a extensão [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools).
* O [Docker Desktop](https://docs.docker.com/docker-for-windows/install/) configurado para executar contêineres do Windows.

> [!TIP]
> Se estiver usando o Visual Studio 2017 (versão 15.7 ou superior), baixe e instale o Azure IoT Edge Tools para o Visual Studio 2017 no [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools).

## <a name="create-a-module-project"></a>Criar um projeto de módulo

Nesta seção, você cria um projeto de módulo do IoT Edge usando o Visual Studio e a extensão Azure IoT Edge Tools. Depois de criar um modelo de projeto, adicione o novo código para que o módulo filtre as mensagens com base nas propriedades relatadas.

### <a name="create-a-new-project"></a>Criar um projeto

O Azure IoT Edge Tools fornece modelos de projeto para todas as linguagens de módulo do IoT Edge com suporte do Visual Studio. Esses modelos têm todos os arquivos e códigos de que você precisa para implantar um módulo funcional para testar o IoT Edge. Eles também podem fornecer um ponto de partida para personalização com sua lógica de negócios.

1. Abra o Visual Studio 2019 e selecione **Criar projeto**.

1. No painel **Criar um projeto**, pesquise **IoT Edge** e, na lista de resultados, selecione o projeto **Azure IoT Edge (Windows amd64)** .

   ![Captura de tela do painel "Criar um projeto" no IoT Edge.](./media/tutorial-csharp-module-windows/new-project.png)

1. Selecione **Avançar**.

    O painel **Configurar o novo projeto** é aberto.

   ![Captura de tela do painel "Configurar o novo projeto".](./media/tutorial-csharp-module-windows/configure-project.png)

1. No painel **Configurar o novo projeto**, renomeie o projeto e a solução para algo mais descritivo, como **CSharpTutorialApp**. 

1. Selecione **Criar** para criar o cluster.

   O painel **Adicionar módulo** é aberto.

   ![Captura de tela do painel "Adicionar módulo" para configurar o projeto.](./media/tutorial-csharp-module-windows/add-application-and-module.png)

1. Na página **Configurar o novo projeto**, faça o seguinte:

   a. No painel esquerdo, selecione o modelo **Módulo C#** .  
   b. Na caixa **Nome do módulo**, digite **CSharpModule**.  
   c. Na caixa **URL do Repositório**, substitua **localhost:5000** pelo valor do **Servidor de logon** do seu Registro de Contêiner do Azure no seguinte formato: `<registry name>.azurecr.io/csharpmodule`

    > [!NOTE]
    > Um repositório de imagem inclui o nome do registro de contêiner e o nome da imagem de contêiner. Sua imagem de contêiner é populada previamente com base no valor do nome do projeto do módulo.  Você pode recuperar o servidor de logon da página de visão geral do seu registro de contêiner no portal do Azure.

1. Selecione **Adicionar** para criar o projeto.

### <a name="add-your-registry-credentials"></a>Adicionar suas credenciais de registro

O manifesto de implantação compartilha as credenciais para seu registro de contêiner com o runtime do IoT Edge. O runtime precisa dessas credenciais para efetuar pull de imagens privadas para o dispositivo IoT Edge. Use as credenciais da seção **Chaves de acesso** do Registro de Contêiner do Azure.

1. No Gerenciador de Soluções do Visual Studio, abra o arquivo *deployment.template.json*.

1. Localize a propriedade **registryCredentials** nas propriedades desejadas do $edgeAgent. O endereço do registro da propriedade deve ser preenchido automaticamente com as informações fornecidas quando você criou o projeto. Os campos de nome de usuário e senha devem conter nomes de variáveis. Por exemplo:

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "$CONTAINER_REGISTRY_USERNAME_<registry name>",
       "password": "$CONTAINER_REGISTRY_PASSWORD_<registry name>",
       "address": "<registry name>.azurecr.io"
     }
   }
   ```

1. Abra o arquivo do ambiente (ENV) em sua solução de módulo. Por padrão, o arquivo fica oculto no Gerenciador de Soluções, de modo que pode ser necessário selecionar o botão **Mostrar Todos os Arquivos** para exibi-lo. O arquivo ENV deve conter as mesmas variáveis de nome de usuário e senha que você viu no arquivo *deployment.template.json*.

1. Adicione os valores de **Nome de usuário** e **Senha** de seu Registro de Contêiner do Azure.

1. Salve suas alterações no arquivo ENV.

### <a name="update-the-module-with-custom-code"></a>Atualizar o módulo com código personalizado

O código padrão do módulo recebe mensagens em uma fila de entrada e as passa adiante por meio de sua fila de saída. Vamos adicionar mais código para que o módulo processe as mensagens na borda antes de encaminhá-las ao seu hub IoT. Atualize o módulo para que ele analise os dados de temperatura em cada mensagem e envie a mensagem ao hub IoT apenas se a temperatura exceder um determinado limite.

1. No Visual Studio, selecione **CSharpModule** > **Program.cs**.

1. Na parte superior do namespace **CSharpModule**, adicione três instruções **using** aos tipos usados posteriormente:

    ```csharp
    using System.Collections.Generic;     // For KeyValuePair<>
    using Microsoft.Azure.Devices.Shared; // For TwinCollection
    using Newtonsoft.Json;                // For JsonConvert
    ```

1. Adicione a variável **temperatureThreshold** à classe **Program** após a variável de contador. A variável temperatureThreshold define o valor que a temperatura medida deve exceder para os dados a serem enviados para o Hub IoT.

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

1. Adicione as classes **MessageBody**, **Machine** e **Ambient** à classe **Program** após as declarações de variável. Essas classes definem o esquema esperado para o corpo de mensagens de entrada.

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

1. Procure o método **Init**. Esse método cria e configura um objeto **ModuleClient**, que permite que o módulo se conecte ao runtime do Azure IoT Edge para enviar e receber mensagens. O código também registra um retorno de chamada para receber mensagens de um hub do IoT Edge por meio do ponto de extremidade **input1**.

   Substitua todo o método Init pelo seguinte código:

   ```csharp
   static async Task Init()
   {
       AmqpTransportSettings amqpSetting = new AmqpTransportSettings(TransportType.Amqp_Tcp_Only);
       ITransportSettings[] settings = { amqpSetting };

       // Open a connection to the Edge runtime.
       ModuleClient ioTHubModuleClient = await ModuleClient.CreateFromEnvironmentAsync(settings);
       await ioTHubModuleClient.OpenAsync();
       Console.WriteLine("IoT Hub module client initialized.");

       // Read the TemperatureThreshold value from the module twin's desired properties.
       var moduleTwin = await ioTHubModuleClient.GetTwinAsync();
       await OnDesiredPropertiesUpdate(moduleTwin.Properties.Desired, ioTHubModuleClient);

       // Attach a callback for updates to the module twin's desired properties.
       await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertiesUpdate, null);

       // Register a callback for messages that are received by the module.
       await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
   }
   ```

   Esse método Init atualizado ainda configura a conexão com o runtime do IoT Edge com o ModuleClient, mas também adiciona uma nova funcionalidade. Ele lê as propriedades desejadas do módulo gêmeo para recuperar o valor **temperatureThreshold**. Depois, cria um retorno de chamada que escuta, para as atualizações futuras, as propriedades desejadas do módulo gêmeo. Com esse retorno de chamada, é possível atualizar o limite de temperatura no módulo gêmeo remotamente e as alterações serão incorporadas ao módulo.

   O método Init atualizado também altera o método **SetInputMessageHandlerAsync** existente. No código de exemplo, as mensagens de entrada em *input1* são processadas com a função *PipeMessage*, mas podemos alterar isso para usar a função *FilterMessages* que criaremos nas etapas a seguir.

1. Adicione um novo método **onDesiredPropertiesUpdate** à classe **Program**. Esse método recebe atualizações sobre as propriedades desejadas do módulo gêmeo e atualiza a variável **temperatureThreshold** para corresponder. Todos os módulos possuem seu próprio módulo gêmeo, o que permite configurar o código em execução dentro de um módulo diretamente da nuvem.

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

1. Remova o método de exemplo **PipeMessage** e substitua-o por um novo método **FilterMessages**. Esse método é chamado sempre que o módulo recebe uma mensagem do hub do IoT Edge. Ele filtra mensagens que relatam temperaturas abaixo do limite definido por meio do módulo gêmeo. O método também adiciona a propriedade **MessageType** à mensagem com o valor definido como **Alerta**.

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

1. Salve o arquivo *Program.cs*.

1. Abra o arquivo *deployment.template.json* em sua solução do IoT Edge. Esse arquivo informa ao agente do IoT Edge quais módulos implantar e informa o hub do IoT Edge como rotear mensagens entre eles. Aqui, os módulos a serem implantados são **SimulatedTemperatureSensor** e **CSharpModule**.

1. Adicione o módulo gêmeo **CSharpModule** no manifesto de implantação. Insira o seguinte conteúdo JSON na parte inferior da seção `modulesContent`, após o módulo gêmeo do **$edgeHub**:

    ```json
       "CSharpModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
    ```

    ![Captura de tela mostrando o módulo gêmeo sendo adicionado ao modelo de implantação.](./media/tutorial-csharp-module-windows/module-twin.png)

1. Salve o arquivo *deployment.template.json*.

## <a name="build-and-push-your-module"></a>Criar e enviar seu módulo por push

Na seção anterior, você criou uma solução do IoT Edge e adicionou um código a **CSharpModule** para filtrar mensagens em que a temperatura relatada do computador está abaixo do limite aceitável. Agora você precisa compilar a solução como uma imagem de contêiner e enviá-la por push para seu registro de contêiner.

### <a name="sign-in-to-docker"></a>Entrar no Docker

1. Use o seguinte comando para entrar no Docker em seu computador de desenvolvimento. Use o nome de usuário, senha e servidor de logon do seu Registro de Contêiner do Azure. É possível recuperar esses valores na seção **Chaves de acesso** no registro do portal do Azure.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Você poderá receber um aviso de segurança recomendando o uso de `--password-stdin`. Embora recomendemos fazer isso como uma melhor prática para cenários de produção, essa prática está fora do escopo deste tutorial. Para saber mais, confira a [referência de logon do docker](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin).

### <a name="build-and-push"></a>Compilar e efetuar push

1. No Gerenciador de Soluções do Visual Studio, clique com o botão direito do mouse no nome do projeto que deseja compilar. O nome padrão é **AzureIotEdgeApp1** e, uma vez que você está compilando um módulo do Windows, a extensão deve ser **Windows.Amd64**.

1. Selecione **Compilar e Efetuar Push de Módulos do IoT Edge**.

   O comando de build e push inicia três operações:
   * Primeiro, ele cria uma pasta na solução chamada *config*, que mantém o manifesto de implantação completo. Ele é criado com base nas informações do modelo de implantação e em outros arquivos da solução. 
   * Depois, ele executa `docker build` para compilar a imagem de contêiner com base no Dockerfile apropriado para sua arquitetura de destino. 
   * Por fim, ele executa `docker push` para efetuar push do repositório de imagens para seu registro de contêiner.

   Esse processo pode levar vários minutos na primeira vez, mas será mais rápido na próxima vez em que você executar os comandos.

## <a name="deploy-modules-to-the-device"></a>Implantar módulos no dispositivo

Use o Cloud Explorer do Visual Studio e a extensão Azure IoT Edge Tools para implantar o projeto de módulo em seu dispositivo IoT Edge. Você já preparou um manifesto de implantação para seu cenário, o arquivo *deployment.windows-amd64.json*, na pasta *config*. Agora, tudo o que você precisa fazer é selecionar um dispositivo para receber a implantação.

Verifique se seu dispositivo IoT Edge está em funcionamento.

1. No Cloud Explorer do Visual Studio, expanda os recursos para ver sua lista de dispositivos IoT.

1. Clique com o botão direito do mouse no nome do dispositivo IoT Edge no qual você deseja receber a implantação.

1. Selecione **Criar Implantação**.

1. No Explorador de Arquivos do Visual Studio, selecione o arquivo *deployment.windows-amd64.json* na pasta *config* de sua solução.

1. Atualize o Cloud Explorer para ver os módulos implantados listados em seu dispositivo.

## <a name="view-generated-data"></a>Exibir os dados gerados

Depois que você aplica o manifesto de implantação no dispositivo IoT Edge, o runtime do IoT Edge no dispositivo coleta as novas informações de implantação e inicia a execução nele. Todos os módulos em execução no dispositivo, mas não incluídos no manifesto de implantação, são interrompidos. Todos os módulos ausentes do dispositivo são iniciados.

É possível usar a extensão de Ferramentas do IoT Edge para ver as mensagens conforme elas chegam ao hub IoT.

1. No Cloud Explorer do Visual Studio, selecione o nome do seu dispositivo IoT Edge.

1. Na lista **Ações**, selecione **Iniciar o Monitoramento do Ponto de Extremidade de Evento Interno**.

1. Veja as mensagens que estão chegando em seu hub IoT. Pode levar algum tempo para as mensagens chegarem, porque o dispositivo IoT Edge precisa receber sua nova implantação e iniciar todos os módulos. As alterações no código CSharpModule precisam aguardar até que a temperatura do computador atinja 25 graus antes que as mensagens sejam enviadas. O código também adiciona o tipo de mensagem **Alerta** às mensagens que atingem esse limite de temperatura.

   ![Captura de tela da Janela de Saída exibindo as mensagens que estão chegando ao hub IoT.](./media/tutorial-csharp-module-windows/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>Editar o módulo gêmeo

Você usou o módulo gêmeo CSharpModule para definir o limite de temperatura em 25 graus. É possível usar o módulo gêmeo para alterar a funcionalidade sem precisar atualizar o código do módulo.

1. No Visual Studio, abra o arquivo *deployment.windows-amd64.json*. 

   *Não* abra o arquivo *deployment.template*. Se não vir o manifesto de implantação no arquivo *config* no Gerenciador de Soluções, selecione o ícone **Mostrar todos os arquivos** na barra de ferramentas do Gerenciador de Soluções.

1. Procure pelo CSharpModule gêmeo e altere o valor do parâmetro **temperatureThreshold** para uma nova temperatura de 5 a 10 graus mais alta do que a última temperatura relatada.

1. Salve o arquivo *deployment.windows-amd64.json*.

1. Siga as instruções de implantação novamente para aplicar o manifesto de implantação atualizado ao seu dispositivo.

1. Monitore as mensagens do dispositivo para nuvem recebidas. As mensagens devem parar até que o novo limite de temperatura seja atingido.

## <a name="clean-up-resources"></a>Limpar os recursos

Se você pretende continuar para o próximo artigo recomendado, pode manter e reutilizar os recursos e as configurações criados neste tutorial. Você também pode continuar usando o mesmo dispositivo IoT Edge como um dispositivo de teste.

Caso contrário, para evitar encargos, você pode excluir as configurações locais e os recursos do Azure usados aqui.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou um módulo do IoT Edge com código para filtrar os dados brutos gerados pelo seu dispositivo IoT Edge.

Para saber como o Azure IoT Edge pode ajudar você a implantar os Serviços de Nuvem do Azure para processar e analisar dados na borda, prossiga para os próximos tutoriais.

> [!div class="nextstepaction"]
> [Azure Functions](tutorial-deploy-function.md)
> [Azure Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Azure Machine Learning](tutorial-deploy-machine-learning.md)
> [Serviço de Visão Personalizada](tutorial-deploy-custom-vision.md)
