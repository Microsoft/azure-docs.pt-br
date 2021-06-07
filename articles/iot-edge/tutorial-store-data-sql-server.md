---
title: Tutorial – Armazenar dados com o módulo do SQL usando o Azure IoT Edge
description: Este tutorial mostra como armazenar dados localmente no seu dispositivo IoT Edge com um módulo do SQL Server
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/04/2020
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, devx-track-csharp
ms.openlocfilehash: 361a151d1f1835f82e440704105cf8b3844a4c54
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106221616"
---
# <a name="tutorial-store-data-at-the-edge-with-sql-server-databases"></a>Tutorial: Armazenar dados na borda com os bancos de dados do SQL Server

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Implante um módulo do SQL Server para armazenar dados em um dispositivo que executa o Azure IoT Edge com contêineres Linux.

Use o Azure IoT Edge e o SQL Server para armazenar e consultar dados na borda. O Azure IoT Edge tem recursos de armazenamento básicos que armazenam mensagens em cache caso um dispositivo fique offline e depois as encaminha quando a conexão for restabelecida. No entanto, talvez você queira recursos mais avançados de armazenamento, como a capacidade de consultar dados localmente. Seus dispositivos IoT Edge podem usar bancos de dados locais para realizar computações mais complexa sem necessidade de manter uma conexão ao Hub IoT.

Este artigo fornece instruções para a implantação de um banco de dados do SQL Server em um dispositivo IoT Edge. O Azure Functions, em execução no dispositivo IoT Edge, estruturam os dados de entrada e, em seguida, enviam para o banco de dados. As etapas neste artigo também podem ser aplicadas a outros bancos de dados que funcionam em contêineres, como o MySQL ou PostgreSQL.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
>
> * Usar o Visual Studio Code para criar uma função do Azure
> * Implantar um banco de dados SQL em seu dispositivo IoT Edge
> * Use o Visual Studio Code para criar módulos e implantá-los em seu dispositivo IoT Edge
> * Exibir os dados gerados

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este tutorial, você deve ter passado pelo tutorial anterior para configurar seu ambiente de desenvolvimento para o desenvolvimento de contêiner do Linux: [Desenvolver módulos do IoT Edge para dispositivos Linux](tutorial-develop-for-linux.md). Ao concluir esse tutorial, você deve ter os seguintes pré-requisitos implementados:

* Um [Hub IoT](../iot-hub/iot-hub-create-through-portal.md) na camada padrão ou gratuito no Azure.
* Um dispositivo do AMD64 que executa o Azure IoT Edge com contêineres Linux. Você pode usar os inícios rápidos para configurar um [dispositivo Linux](quickstart-linux.md) ou um [dispositivo Windows](quickstart.md).
  * Dispositivos ARM, como Raspberry PIS, não podem executar o SQL Server. Se quiser usar o SQL em um dispositivo ARM, poderá se inscrever para experimentar o [SQL do Azure no Edge](https://azure.microsoft.com/services/sql-edge/) na versão prévia.
* Um registro de contêiner, como o [Registro de Contêiner do Azure](../container-registry/index.yml).
* O [Visual Studio Code](https://code.visualstudio.com/) configurado com o [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* O [Docker CE](https://docs.docker.com/install/) configurado para executar contêineres do Linux.

Este tutorial usa um módulo do Azure Functions para enviar dados para o SQL Server. Para desenvolver um módulo do IoT Edge com o Azure Functions, instale os seguintes pré-requisitos adicionais em seu computador de desenvolvimento:

* Extensão [C# para Visual Studio Code (da plataforma OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) para o Visual Studio Code.
* [SDK do .NET Core 2.1](https://www.microsoft.com/net/download).

## <a name="create-a-function-project"></a>Criar um projeto de função

Para enviar dados para um banco de dados, você precisa de um módulo que possa estruturar os dados corretamente e, depois, armazená-los em uma tabela.

### <a name="create-a-new-project"></a>Criar um novo projeto

As etapas a seguir mostram como criar uma função do IoT Edge usando o Visual Studio Code e as Ferramentas de IoT do Azure.

1. Abra o Visual Studio Code.

2. Abra a paleta de comandos do VS Code selecionando **Exibir** > **Paleta de comandos**.

3. Na paleta de comandos, digite e execute o comando **Azure IoT Edge: Nova solução do IoT Edge**. Na paleta de comandos, forneça as seguintes informações para criar sua solução:

   | Campo | Valor |
   | ----- | ----- |
   | Selecionar pasta | Escolha o local no computador de desenvolvimento em que o VS Code criará os arquivos de solução. |
   | Fornecer um nome para a solução | Insira um nome descritivo para a solução, como **SqlSolution** ou aceite o padrão. |
   | Selecionar modelo do módulo | Escolha **Azure Functions – C#** . |
   | Fornecer um nome de módulo | Nomeie seu módulo como **sqlFunction**. |
   | Fornecer o repositório de imagem do Docker para o módulo | Um repositório de imagem inclui o nome do registro de contêiner e o nome da imagem de contêiner. Sua imagem de contêiner foi preenchida automaticamente na última etapa. Substitua **localhost:5000** pelo valor do **Servidor de logon** do seu Registro de Contêiner do Azure. Você pode recuperar o servidor de Logon da página Visão Geral do seu registro de contêiner no portal do Azure. <br><br>A cadeia de caracteres final se parece com \<registry name\>.azurecr.io/sqlfunction. |

   A janela do VS Code carregará seu workspace da solução IoT Edge.

### <a name="add-your-registry-credentials"></a>Adicionar suas credenciais de registro

O arquivo do ambiente armazena as credenciais para o registro de contêiner e as compartilha com o runtime do IoT Edge. O runtime precisa dessas credenciais para efetuar pull de imagens privadas para o dispositivo IoT Edge.

A extensão do IoT Edge tenta efetuar pull de suas credenciais de Registro de Contêiner do Azure e as popula no arquivo de ambiente. Verifique se suas credenciais já estão incluídas. Caso contrário, adicione-as agora:

1. No explorador do VS Code, abra o arquivo .env.
2. Atualize os campos com os valores de **nome de usuário** e **senha** que você copiou do registro de contêiner do Azure.
3. Salve o arquivo.

### <a name="select-your-target-architecture"></a>Selecione sua arquitetura de destino

É necessário selecionar qual arquitetura você deseja usar para cada solução, porque o contêiner é criado e executado de maneira diferente para cada tipo de arquitetura. O padrão é o Linux AMD64.

1. Abra a paleta de comandos e pesquise **Azure IoT Edge: definir a plataforma de destino padrão para a solução Edge** ou selecione o ícone de atalho na barra lateral na parte inferior da janela.

2. Na paleta de comandos, selecione a arquitetura de destino na lista de opções. Para este tutorial, estamos usando uma máquina virtual Ubuntu como o dispositivo IoT Edge, portanto, manteremos o padrão **amd64**.

### <a name="update-the-module-with-custom-code"></a>Atualizar o módulo com código personalizado

1. No gerenciador do VS Code, abra **modules** > **sqlFunction** > **sqlFunction.csproj**.

2. Localize o grupo de referências de pacote e adicione um novo um para incluir o SqlClient.

   ```csproj
   <PackageReference Include="System.Data.SqlClient" Version="4.5.1"/>
   ```

3. Salve o arquivo **sqlFunction.csproj**.

4. Abra o arquivo **sqlFunction.cs**.

5. Substitua todo o conteúdo do arquivo pelo código a seguir:

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
   using Sql = System.Data.SqlClient;

   namespace Functions.Samples
   {
       public static class sqlFunction
       {
           [FunctionName("sqlFunction")]
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

                   //Store the data in SQL db
                   const string str = "<sql connection string>";
                   using (Sql.SqlConnection conn = new Sql.SqlConnection(str))
                   {
                       conn.Open();
                       var insertMachineTemperature = "INSERT INTO MeasurementsDB.dbo.TemperatureMeasurements VALUES (CONVERT(DATETIME2,'" + messageBody.timeCreated + "', 127), 'machine', " + messageBody.machine.temperature + ");";
                       var insertAmbientTemperature = "INSERT INTO MeasurementsDB.dbo.TemperatureMeasurements VALUES (CONVERT(DATETIME2,'" + messageBody.timeCreated + "', 127), 'ambient', " + messageBody.ambient.temperature + ");";
                       using (Sql.SqlCommand cmd = new Sql.SqlCommand(insertMachineTemperature + "\n" + insertAmbientTemperature, conn))
                       {
                           //Execute the command and log the # rows affected.
                           var rows = await cmd.ExecuteNonQueryAsync();
                           logger.LogInformation($"{rows} rows were updated");
                       }
                   }

                   if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
                   {
                       // Send the message to the output as the temperature value is greater than the threashold.
                       using (var filteredMessage = new Message(messageBytes))
                       {
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

6. Na linha 35, substitua a cadeia de caracteres **\<sql connection string\>** pela cadeia de caracteres a seguir. A propriedade **Fonte de dados** referencia o contêiner do SQL Server, que ainda não existe. Você o criará com o nome **SQL** na próxima seção.

   ```csharp
   Data Source=tcp:sql,1433;Initial Catalog=MeasurementsDB;User Id=SA;Password=Strong!Passw0rd;TrustServerCertificate=False;Connection Timeout=30;
   ```

7. Salve o arquivo **sqlFunction.cs**.

## <a name="add-the-sql-server-container"></a>Adicionar o contêiner do SQL Server

Um [Manifesto de implantação](module-composition.md) declara quais módulos o runtime do IoT Edge instalará em seu dispositivo IoT Edge. Você forneceu o código para criar um módulo do Function personalizado na seção anterior, mas o módulo do SQL Server já foi criado e está disponível no Azure Marketplace. Você precisa solicitar que o runtime do IoT Edge o inclua. Depois, configure-o em seu dispositivo.

1. No Visual Studio Code, abra a paleta de comandos selecionando **Exibição** > **Paleta de Comandos**.

2. Na paleta de comandos, digite e execute o comando **Azure IoT Edge: Adicionar módulo do IoT Edge**. Na paleta de comandos, forneça as seguintes informações para adicionar um novo módulo:

   | Campo | Valor |
   | ----- | ----- |
   | Selecionar arquivo de modelo de implantação | A paleta de comandos realça o arquivo **deployment.template.json** na pasta da solução atual. Selecione o arquivo.  |
   | Selecionar modelo do módulo | Selecione **Módulo do Azure Marketplace**. |

3. No marketplace do módulo do Azure IoT Edge, pesquise e selecione **Módulo do SQL Server**.

4. Altere o nome do módulo **sql**, todas as letras minúsculas. Esse nome corresponde ao nome de contêiner declarado na cadeia de conexão do arquivo sqlFunction.cs.

5. Selecione **Importar** para adicionar o módulo à sua solução.

6. Na pasta da solução, abra o arquivo **deployment.template.json**.

7. Localize a seção de **módulos**. Você deverá ver três módulos. O módulo *SimulatedTemperatureSensor* está incluído por padrão em novas soluções e fornece dados de teste para usar com seus outros módulos. O módulo *sqlFunction* é o módulo que você criou inicialmente e que foi atualizado com o novo código. Por fim, o módulo *sql* foi importado do Azure Marketplace.

   >[!Tip]
   >O módulo do SQL Server vem com uma senha padrão definida nas variáveis de ambiente do manifesto de implantação. Sempre que você criar um contêiner do SQL Server em um ambiente de produção, será necessário [alterar a senha do administrador do sistema padrão](/sql/linux/quickstart-install-connect-docker).

8. Feche o arquivo **deployment.template.json**.

## <a name="build-your-iot-edge-solution"></a>Criar solução IoT Edge

Nas seções anteriores, você criou uma solução com um módulo e depois adicionou outra ao modelo de manifesto de implantação. O módulo do SQL Server é hospedado publicamente pela Microsoft, mas você precisa colocar o código no módulo do Functions em um contêiner. Nesta seção, você criará a solução e imagens de contêiner para o módulo sqlFunction e enviará a imagem por push para o registro de contêiner.

1. No Visual Studio Code, abra o terminal integrado selecionando **Exibir** > **Terminal**.  

1. Entre no seu Registro de contêiner no Visual Studio Code para que você possa efetuar o push das suas imagens para o Registro. Use as mesmas credenciais de ACR (Registro de Contêiner do Azure) que você adicionou ao arquivo .env. Digite o seguinte comando no terminal integrado:

    ```csh/sh
    docker login -u <ACR username> -p <ACR password> <ACR login server>
    ```

    Você pode ver um aviso de segurança recomendando o uso do parâmetro --password-stdin. Embora seu uso esteja fora do escopo deste artigo, é recomendável seguir essa prática recomendada. Para obter mais informações, consulte a referência do comando [docker login](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin).

1. No explorador do VS Code, clique com o botão direito do mouse no arquivo **deployment.template.json** e selecione **Compilar e enviar por push solução IoT Edge**.

   O comando de criação e de envio por push inicia três operações. Primeiro, é criada uma pasta na solução denominada **config** que contém o manifesto de implantação completo, criado com base nas informações do modelo de implantação e em outros arquivos da solução. Depois, ele executa `docker build` para montar a imagem de contêiner com base no dockerfile apropriado para sua arquitetura de destino. Por fim, ele executa `docker push` para enviar por push o repositório de imagens para seu registro de contêiner.

   Esse processo pode levar vários minutos na primeira vez, mas será mais rápido na próxima vez em que você executar os comandos.

   Você pode verificar que o módulo sqlFunction foi envido por push com êxito ao registro de contêiner. No Portal do Azure, navegue até o registro de contêiner. Selecione **repositórios** e pesquise **sqlFunction**. Os outros dois módulos, SimulatedTemperatureSensor e sql, não serão enviados por push ao Registro de Contêiner porque os repositórios deles já estão nos Registros da Microsoft.

## <a name="deploy-the-solution-to-a-device"></a>Implantar a solução a um dispositivo

Você pode definir os módulos em um dispositivo por meio do Hub IoT, mas também pode acessar o Hub IoT e dispositivos por meio do Visual Studio Code. Nesta seção, você configura o acesso ao seu Hub IoT, depois usa o VS Code para implantar a solução ao seu dispositivo IoT Edge.

1. No gerenciador do Visual Studio Code, na seção **Hub IoT do Azure**, expanda **Dispositivos** para ver sua lista de dispositivos IoT.

2. Clique com o botão direito no dispositivo que deseja que seja o alvo de sua implantação e selecione **Criar Implantação para Dispositivo Único**.

3. Selecione o arquivo **deployment.amd64.json** na pasta **config** e clique em **Selecionar Manifesto de Implantação do Edge**. Não use o arquivo deployment.template.json.

4. No dispositivo, expanda **Módulos** para ver uma lista de módulos implantados e em execução. Clique no botão Atualizar. Você deve ver os novos módulos **sql** e **sqlFunction** sendo executados em conjunto com o módulo **SimulatedTemperatureSensor** e com **$edgeAgent** e **$edgeHub**.

    Você também pode verificar se todos os módulos estão em execução no seu dispositivo. No seu dispositivo IoT Edge, execute o comando a seguir para ver o status dos módulos.

   ```cmd/sh
   iotedge list
   ```

    Pode levar alguns minutos para que os módulos sejam iniciados. O runtime do IoT Edge precisa receber seu novo manifesto de implantação, obter as imagens de módulo do runtime do contêiner e iniciar cada novo módulo.

## <a name="create-the-sql-database"></a>Criar o banco de dados SQL

Ao aplicar o manifesto de implantação para seu dispositivo, você obtém três módulos em execução. O módulo SimulatedTemperatureSensor gera dados de ambiente simulado. O módulo sqlFunction obtém os dados e os formata para um banco de dados. Esta seção orienta-o através da configuração do banco de dados SQL para armazenar os dados de temperatura.

Execute os seguintes comandos em seu dispositivo IoT Edge. Esses comandos fazem a conexão com o módulo **sql** em execução em seu dispositivo e criam um banco de dados e uma tabela para manter os dados de temperatura que estão sendo enviados a ele.

1. Em uma ferramenta de linha de comando no dispositivo IoT Edge, faça a conexão com seu banco de dados.

      ```bash
      sudo docker exec -it sql bash
      ```

2. Abra a ferramenta de comando SQL.

      ```bash
      /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P 'Strong!Passw0rd'
      ```

3. Crie seu banco de dados:

      ```sql
      CREATE DATABASE MeasurementsDB
      ON
      (NAME = MeasurementsDB, FILENAME = '/var/opt/mssql/measurementsdb.mdf')
      GO
      ```

4. Defina sua tabela.

   ```sql
   CREATE TABLE MeasurementsDB.dbo.TemperatureMeasurements (measurementTime DATETIME2, location NVARCHAR(50), temperature FLOAT)
   GO
   ```

Você pode personalizar seu arquivo docker do SQL Server para configurar automaticamente o SQL Server a ser implantado em vários dispositivos IoT Edge. Para obter mais informações, consulte o [projeto de demonstração do contêiner do Microsoft SQL Server](https://github.com/twright-msft/mssql-node-docker-demo-app).

## <a name="view-the-local-data"></a>Exibir os dados locais

Depois que a tabela é criada, o módulo sqlFunction começa a armazenar dados em um banco de dados do SQL Server 2017 local em seu dispositivo IoT Edge.

De dentro da ferramenta de comando do SQL, execute o seguinte comando para exibir os dados da tabela formatada:

   ```sql
   SELECT * FROM MeasurementsDB.dbo.TemperatureMeasurements
   GO
   ```

   ![Exibir o conteúdo do banco de dados local](./media/tutorial-store-data-sql-server/view-data.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Se você pretende continuar no próximo artigo recomendado, pode manter os recursos e as configurações já criados e reutilizá-los. Você também pode continuar usando o mesmo dispositivo IoT Edge como um dispositivo de teste.

Caso contrário, você pode excluir as configurações locais e os recursos do Azure criados neste artigo para evitar encargos.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

Neste tutorial, você criou um módulo do Azure Functions que contém código para filtrar os dados brutos gerados pelo seu dispositivo IoT Edge. Quando estiver pronto para criar seus próprios módulos, você pode saber mais sobre como [Desenvolver o Azure Functions com o Azure IoT Edge para Visual Studio Code](./how-to-vs-code-develop-module.md).

## <a name="next-steps"></a>Próximas etapas

Se você quiser tentar outro método de armazenamento na borda, leia sobre como usar o armazenamento de BLOBs do Azure no IoT Edge.

> [!div class="nextstepaction"]
> [Armazenar dados na borda com o Armazenamento de Blobs do Azure no IoT Edge](how-to-store-data-blob.md)