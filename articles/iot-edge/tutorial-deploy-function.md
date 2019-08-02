---
title: Tutorial sobre como implantar uma função do Azure em um dispositivo – Azure IoT Edge | Microsoft Docs
description: Neste tutorial, você desenvolve uma função do Azure como um módulo do IoT Edge e, em seguida, a implanta em um dispositivo de borda.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/25/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 2c2a2659b6b9c77b36001af1602c904e7d200b56
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67433049"
---
# <a name="tutorial-deploy-azure-functions-as-iot-edge-modules"></a>Tutorial: Implantar funções do Azure como módulos do IoT Edge

Use o Azure Functions para implantar um código que implementa a lógica de negócios diretamente em seus dispositivos Azure IoT Edge. Este tutorial o orienta através da criação e implantação de uma função do Azure que filtra dados do sensor em um dispositivo IoT Edge simulado. Use o dispositivo IoT Edge simulado que foi criado em Implantar Azure IoT Edge em um dispositivo simulado nos inícios rápidos do [Windows](quickstart.md) ou do [Linux](quickstart-linux.md). Neste tutorial, você aprenderá como:

> [!div class="checklist"]
>
> * Usar o Visual Studio Code para criar uma função do Azure.
> * Usar o VS Code e o Docker para criar uma imagem do Docker e publicá-la em um registro de contêiner.
> * Implantar o módulo do registro do contêiner para seu dispositivo IoT Edge.
> * Exibir dados filtrados.

<center>

![Diagrama - Tutorial sobre como arquitetar, preparar e implantar módulo de funções](./media/tutorial-deploy-function/functions-architecture.png)
</center>

>[!NOTE]
>Os módulos de funções do Azure no Azure IoT Edge são [públicos na versão prévia](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

A função do Azure criada neste tutorial filtra os dados de temperatura gerados pelo seu dispositivo. Ela só envia mensagens upstream para o Hub IoT do Azure quando a temperatura estiver acima de um limite especificado. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este tutorial, você deve ter passado pelo tutorial anterior para configurar seu ambiente de desenvolvimento para o desenvolvimento de contêiner do Linux: [Desenvolver módulos do IoT Edge para dispositivos Linux](tutorial-develop-for-linux.md). Ao concluir esse tutorial, você deve ter os seguintes pré-requisitos implementados: 

* Um [Hub IoT](../iot-hub/iot-hub-create-through-portal.md) na camada padrão ou gratuito no Azure.
* Um [dispositivo Linux que executa o Azure IoT Edge](quickstart-linux.md)
* Um registro de contêiner, como o [Registro de Contêiner do Azure](https://docs.microsoft.com/azure/container-registry/).
* O [Visual Studio Code](https://code.visualstudio.com/) configurado com o [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* O [Docker CE](https://docs.docker.com/install/) configurado para executar contêineres do Linux.

Para desenvolver um módulo do IoT Edge com o Azure Functions, instale os seguintes pré-requisitos adicionais em seu computador de desenvolvimento: 

* [C# para extensão do Visual Studio Code (com OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).
* [O SDK 2.1 do .NET Core](https://www.microsoft.com/net/download).

## <a name="create-a-function-project"></a>Criar um projeto de função

As Ferramentas de IoT do Azure para Visual Studio Code instaladas nos pré-requisitos fornecem funcionalidades de gerenciamento, bem como alguns modelos de código. Nesta seção, você pode usar o Visual Studio Code para criar uma solução de IoT Edge que contém uma função do Azure. 

### <a name="create-a-new-project"></a>Criar um novo projeto

Crie um modelo de solução de Função C# que possa ser personalizado com seu próprio código.

1. Abra o Visual Studio Code no seu computador de desenvolvimento.

2. Abra a paleta de comandos do VS Code selecionando **Exibir** > **Paleta de comandos**.

3. Na paleta de comandos, digite e execute o comando **Azure IoT Edge: nova solução do IoT Edge**. Siga os prompts na paleta de comandos para criar sua solução.

   | Campo | Valor |
   | ----- | ----- |
   | Selecionar pasta | Escolha o local no computador de desenvolvimento em que o VS Code criará os arquivos de solução. |
   | Fornecer um nome para a solução | Insira um nome descritivo para a solução, como **FunctionSolution** ou aceite o padrão. |
   | Selecionar modelo do módulo | Escolha **Azure Functions – C#** . |
   | Fornecer um nome de módulo | Nomeie seu módulo **CSharpFunction**. |
   | Fornecer o repositório de imagem do Docker para o módulo | Um repositório de imagem inclui o nome do registro de contêiner e o nome da imagem de contêiner. Sua imagem de contêiner foi preenchida automaticamente na última etapa. Substitua **localhost:5000** pelo valor do servidor de logon do seu registro de contêiner do Azure. Você pode recuperar o servidor de logon da página Visão Geral do seu registro de contêiner no portal do Azure. A cadeia de caracteres final se parece com \<nome do registro\>.azurecr.io/CSharpFunction. |

   ![Fornecer o repositório de imagem do Docker](./media/tutorial-deploy-function/repository.png)

### <a name="add-your-registry-credentials"></a>Adicionar suas credenciais de registro

O arquivo do ambiente armazena as credenciais para o registro de contêiner e as compartilha com o tempo de execução do IoT Edge. O tempo de execução precisa dessas credenciais para efetuar pull de imagens privadas para o dispositivo IoT Edge.

1. No explorador do VS Code, abra o arquivo .env.
2. Atualize os campos com os valores de **nome de usuário** e **senha** que você copiou do registro de contêiner do Azure.
3. Salve o arquivo.

### <a name="select-your-target-architecture"></a>Selecione sua arquitetura de destino

No momento, o Visual Studio Code pode desenvolver módulos em C para os dispositivos Linux AMD64 e Linux ARM32v7. É necessário selecionar qual arquitetura você deseja usar para cada solução, porque o contêiner é criado e executado de maneira diferente para cada tipo de arquitetura. O padrão é o Linux AMD64. 

1. Abra a paleta de comandos e pesquise **Azure IoT Edge: definir a plataforma de destino padrão para a solução Edge** ou selecione o ícone de atalho na barra lateral na parte inferior da janela. 

2. Na paleta de comandos, selecione a arquitetura de destino na lista de opções. Para este tutorial, estamos usando uma máquina virtual Ubuntu como o dispositivo IoT Edge, portanto, manteremos o padrão **amd64**. 

### <a name="update-the-module-with-custom-code"></a>Atualizar o módulo com código personalizado

Vamos adicionar algum código adicional para que o módulo processe as mensagens na borda antes de encaminhá-las ao Hub IoT.

1. No Visual Studio Code, abra **módulos** > **CSharpFunction** > **CSharpFunction.cs**.

1. Substitua o conteúdo do arquivo **CSharpFunction.cs** pelo código a seguir. Esse código recebe telemetria sobre o ambiente e a temperatura da máquina e apenas encaminha a mensagem de logon no Hub IoT se a temperatura da máquina estiver acima do limite definido.

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.IO;
   using System.Text;
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Client;
   using Microsoft.Azure.WebJobs;
   using Microsoft.Azure.WebJobs.Extensions.EdgeHub;
   using Microsoft.Azure.WebJobs.Host;
   using Microsoft.Extensions.Logging;
   using Newtonsoft.Json;

   namespace Functions.Samples
   {
       public static class CSharpFunction
       {
           [FunctionName("CSharpFunction")]
           public static async Task FilterMessageAndSendMessage(
               [EdgeHubTrigger("input1")] Message messageReceived,
               [EdgeHub(OutputName = "output1")] IAsyncCollector<Message> output,
               ILogger logger)
           {
               const int temperatureThreshold = 20;
               byte[] messageBytes = messageReceived.GetBytes();
               var messageString = System.Text.Encoding.UTF8.GetString(messageBytes);

               if (!string.IsNullOrEmpty(messageString))
               {
                   logger.LogInformation("Info: Received one non-empty message");
                   // Get the body of the message and deserialize it.
                   var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

                   if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
                   {
                       // Send the message to the output as the temperature value is greater than the threshold.
                       var filteredMessage = new Message(messageBytes);
                       // Copy the properties of the original message into the new Message object.
                       foreach (KeyValuePair<string, string> prop in messageReceived.Properties)
                       {filteredMessage.Properties.Add(prop.Key, prop.Value);}
                       // Add a new property to the message to indicate it is an alert.
                       filteredMessage.Properties.Add("MessageType", "Alert");
                       // Send the message.
                       await output.AddAsync(filteredMessage);
                       logger.LogInformation("Info: Received and transferred a message with temperature above the threshold");
                   }
               }
           }
       }
       //Define the expected schema for the body of incoming messages.
       class MessageBody
       {
           public Machine machine {get; set;}
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
   }
   ```

1. Salve o arquivo.

## <a name="build-your-iot-edge-solution"></a>Criar solução IoT Edge

Na seção anterior, você criou uma solução IoT Edge e adicionou um código a **CSharpFunction** para filtrar mensagens em que a temperatura relatada do computador for menor do que o limite aceitável. Agora você precisa compilar a solução como uma imagem de contêiner e enviá-la por push para seu registro de contêiner.

Nesta seção, forneça as credenciais do seu registro de contêiner pela segunda vez (a primeira foi no arquivo **.env** da solução IoT Edge) entrando localmente do seu computador de desenvolvimento para que o Visual Studio Code possa enviar imagens por push ao registro.

1. Abra o terminal integrado do VS Code selecionando **Exibir** > **Terminal**. 

2. Entre no seu registro de contêiner, inserindo o seguinte comando no terminal integrado. Use o nome de usuário e o servidor de logon que você copiou do seu Registro de Contêiner do Azure anteriormente.

    ```csh/sh
    docker login -u <ACR username> <ACR login server>
    ```

    Quando você for solicitado a inserir a senha, cole a senha (ela não ficará visível na janela do terminal) do seu registro de contêiner e pressione **Enter**.

    ```csh/sh
    Password: <paste in the ACR password and press enter>
    Login Succeeded
    ```

3. No explorador do VS Code, clique com o botão direito do mouse no arquivo deployment.template.json e selecione **Compilar e enviar por push solução IoT Edge**. 

Quando você solicitar ao Visual Studio Code para compilar sua solução, primeiro ele usará as informações no modelo de implantação e gerará um arquivo deployment.json em uma nova pasta chamada **config**. Em seguida, ele executará dois comandos no terminal integrado: `docker build` e `docker push`. Esses dois comandos compilam seu código, conteinerizam as funções e enviam o código por push para o registro de contêiner que você especificou ao inicializar a solução. 

## <a name="view-your-container-image"></a>Exibir sua imagem de contêiner

O Visual Studio Code gera uma mensagem de êxito quando sua imagem de contêiner é enviada por push para seu registro de contêiner. Caso deseje confirmar a operação com êxito por conta própria, é possível exibir a imagem no registro. 

1. No portal do Azure, navegue até seu registro de contêiner do Azure. 
2. Selecione **Repositórios**.
3. Você deve ver o repositório **csharpfunction** na lista. Escolhe este repositório para ver mais detalhes.
4. Na seção **Marcas**, você deve ver a marca **0.0.1-amd64**. Essa marca indica a versão e a plataforma da imagem que você compilou. Esses valores são definidos no arquivo module.json na pasta CSharpFunction. 

## <a name="deploy-and-run-the-solution"></a>Implantar e executar a solução

Você pode usar o portal do Azure para implantar o módulo da função em um dispositivo IoT Edge como foi feito no guia de início rápido. Você também pode implantar e monitorar os módulos no Visual Studio Code. As seções a seguir usam as Ferramentas de IoT do Azure para VS Code que estavam listadas nos pré-requisitos. Instale a extensão agora, caso ainda não tenha feito isso. 

1. No explorador do VS Code, expanda a seção **Dispositivos do Hub IoT do Azure**. 

2. Clique com o botão direito no nome do seu dispositivo do IoT Edge e selecione **Criar implantação para um único dispositivo**. 

3. Navegue até a pasta da solução que contém **CSharpFunction**. Abra a pasta de configuração, escolha o arquivo **deployment.json** e depois **Selecionar manifesto de implantação do Edge**.

4. Atualize a seção **Dispositivos Hub IoT do Azure**. Você deve ver o novo **CSharpFunction** sendo executado junto com o módulo **TempSensor** em **$edgeAgent** e **$edgeHub**. Pode demorar um pouco para que os novos módulos apareçam. Seu dispositivo IoT Edge tem de recuperar suas novas informações de implantação do IoT Hub, iniciar novos contêineres e depois retornar o status ao Hub IoT. 

   ![Exibir módulos implantados no VS Code](./media/tutorial-deploy-function/view-modules.png)

## <a name="view-generated-data"></a>Exibir os dados gerados

Você pode ver todas as mensagens que chegam em seu hub IoT executando **Hub IoT do Azure: Iniciar o Monitoramento do Ponto de Extremidade de Evento Interno** na paleta de comandos.

Você também pode filtrar o modo de exibição para ver todas as mensagens que chegam ao seu Hub IoT vindas de um dispositivo específico. Clique com o botão direito do mouse na seção **Dispositivos do Hub IoT do Azure** e selecione **Iniciar Monitoramento de Ponto de Extremidade Interno**.

Para interromper o monitoramento de mensagens, execute o comando **Hub IoT do Azure: Parar o Monitoramento do Ponto de Extremidade de Evento Interno** na paleta de comandos. 

## <a name="clean-up-resources"></a>Limpar recursos

Se você pretende continuar no próximo artigo recomendado, pode manter os recursos e as configurações já criados e reutilizá-los. Você também pode continuar usando o mesmo dispositivo IoT Edge como um dispositivo de teste. 

Caso contrário, você pode excluir as configurações locais e os recursos do Azure criados neste artigo para evitar encargos. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou um módulo de função do Azure com o código para filtrar os dados brutos gerados pelo seu dispositivo IoT Edge. Quando estiver pronto para criar seus próprios módulos, você poderá saber mais sobre como [Desenvolver com o Azure IoT Edge para Visual Studio Code](how-to-vs-code-develop-module.md). 

Siga para os próximos tutoriais para conhecer outras formas pelas quais o Azure IoT Edge pode ajudá-lo a transformar dados em informações de negócios na borda.

> [!div class="nextstepaction"]
> [Localizar médias usando uma janela flutuante no Azure Stream Analytics](tutorial-deploy-stream-analytics.md)
