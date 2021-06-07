---
title: 'Início Rápido: API do Cassandra com .NET Core – Azure Cosmos DB'
description: Este guia de início rápido mostra como usar a API do Cassandra do Azure Cosmos DB para criar um aplicativo de perfil com o portal do Azure e o .NET Core
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
author: TheovanKraay
ms.author: thvankra
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 10/01/2020
ms.custom: devx-track-dotnet
ms.openlocfilehash: f228386d8cf0e708080b7f6c5f6cef7258b2eafb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93099973"
---
# <a name="quickstart-build-a-cassandra-app-with-net-core-and-azure-cosmos-db"></a>Início Rápido: Criar um aplicativo do Cassandra com o .NET Core e o Azure Cosmos DB
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-cassandra-dotnet.md)
> * [.NET Core](create-cassandra-dotnet-core.md)
> * [Java v3](create-cassandra-java.md)
> * [Java v4](create-cassandra-java-v4.md)
> * [Node.js](create-cassandra-nodejs.md)
> * [Python](create-cassandra-python.md)
>  

Este guia de início rápido mostra como usar o .NET Core e a [API do Cassandra](cassandra-introduction.md) do Azure Cosmos DB para criar um aplicativo de perfil clonando um exemplo do GitHub. Este Início Rápido também mostra como usar o portal do Azure baseado na Web para criar uma conta do Azure Cosmos DB.

O Azure Cosmos DB é o serviço de banco de dados multimodelo distribuído globalmente da Microsoft. É possível criar e consultar rapidamente documentos, tabelas, valores-chave e bancos de dados de gráfico, todos os quais se beneficiam de recursos de distribuição global e escala horizontal e no núcleo do Azure Cosmos DB. 

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] Como alternativa, você pode [Experimentar o Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/) sem uma assinatura do Azure, sem ônus e sem compromisso.

Além disso, você precisa de: 
* Se ainda não tiver o Visual Studio 2019 instalado, faça o download e use o **Visual Studio 2019 Community Edition** [gratuito](https://www.visualstudio.com/downloads/). Verifique se você habilitou o **desenvolvimento do Azure** durante a instalação do Visual Studio.
* Instale o [Git](https://www.git-scm.com/) para clonar o exemplo.

<a id="create-account"></a>
## <a name="create-a-database-account"></a>Criar uma conta de banco de dados

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../../includes/cosmos-db-create-dbaccount-cassandra.md)]


## <a name="clone-the-sample-application"></a>Clonar o aplicativo de exemplo

Agora, vamos trabalhar com o código. Vamos clonar um aplicativo de API Cassandra do GitHub, definir a cadeia de conexão e executá-lo. Você verá como é fácil trabalhar usando dados de forma programática. 

1. Abra um prompt de comando. Crie uma pasta chamada `git-samples`. Em seguida, feche o prompt de comando.

    ```bash
    md "C:\git-samples"
    ```

2. Abra uma janela de terminal de git, como git bash, e use o comando `cd` para alterar para a nova pasta para instalar o aplicativo de exemplo.

    ```bash
    cd "C:\git-samples"
    ```

3. Execute o comando a seguir para clonar o repositório de exemplo. Este comando cria uma cópia do aplicativo de exemplo no seu computador.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-cassandra-dotnet-core-getting-started.git
    ```

4. Depois, abra o arquivo de solução CassandraQuickStartSample no Visual Studio. 

## <a name="review-the-code"></a>Examine o código

Esta etapa é opcional. Se você estiver interessado em aprender como o código cria os recursos de banco de dados,examine os snippets a seguir. Os snippets são todos obtidos do arquivo `Program.cs` no método `async Task ProcessAsync()` instalado na pasta `C:\git-samples\azure-cosmos-db-cassandra-dotnet-core-getting-started\CassandraQuickStart`. Caso contrário, você poderá pular para [Atualizar sua cadeia de conexão](#update-your-connection-string).

* Inicialize a sessão conectando-se a um ponto de extremidade do cluster Cassandra. A API Cassandra no Azure Cosmos DB tem suporte apenas para TLSv1.2. 

  ```csharp
      var options = new Cassandra.SSLOptions(SslProtocols.Tls12, true, ValidateServerCertificate);
      options.SetHostNameResolver((ipAddress) => CASSANDRACONTACTPOINT);
      Cluster cluster = Cluster
          .Builder()
          .WithCredentials(USERNAME, PASSWORD)
          .WithPort(CASSANDRAPORT)
          .AddContactPoint(CASSANDRACONTACTPOINT)
          .WithSSL(options)
          .Build()
      ;
      ISession session = await cluster.ConnectAsync();
   ```

* Remova o keyspace existente se ele já existir.

    ```csharp
    await session.ExecuteAsync(new SimpleStatement("DROP KEYSPACE IF EXISTS uprofile")); 
    ```

* Crie um novo keyspace.

    ```csharp
    await session.ExecuteAsync(new SimpleStatement("CREATE KEYSPACE uprofile WITH REPLICATION = { 'class' : 'NetworkTopologyStrategy', 'datacenter1' : 1 };"));
    ```

* Crie uma nova tabela.

   ```csharp
  await session.ExecuteAsync(new SimpleStatement("CREATE TABLE IF NOT EXISTS uprofile.user (user_id int PRIMARY KEY, user_name text, user_bcity text)"));
   ```

* Insira entidades de usuário usando o objeto IMapper com uma nova sessão que se conecta ao keyspace do uprofile.

    ```csharp
    await mapper.InsertAsync<User>(new User(1, "LyubovK", "Dubai"));
    ```

* Consulte para obter todas as informações de usuário.

    ```csharp
    foreach (User user in await mapper.FetchAsync<User>("Select * from user"))
    {
        Console.WriteLine(user);
    }
    ```

* Consulte para obter uma informação única de usuário.

    ```csharp
    mapper.FirstOrDefault<User>("Select * from user where user_id = ?", 3);
    ```

## <a name="update-your-connection-string"></a>Atualizar sua cadeia de conexão

Agora, volte ao portal do Azure para obter informações sobre a cadeia de conexão e copiá-las para o aplicativo. As informações da cadeia de conexão permitem que seu aplicativo se comunique com o banco de dados hospedado.

1. No [portal do Azure](https://portal.azure.com/), selecione **Cadeia de Conexão**.

1. Use o botão :::image type="icon" source="./media/create-cassandra-dotnet/copy.png"::: no lado direito da tela para copiar o valor do NOME DE USUÁRIO.

   :::image type="content" source="./media/create-cassandra-dotnet/keys.png" alt-text="Exibir e copiar uma chave de acesso no portal do Azure, página Cadeia de Conexão":::

1. No Visual Studio, abra o arquivo Program.cs. 

1. Cole o valor do NOME DE USUÁRIO do portal sobre `<PROVIDE>` na linha 13.

    A linha 13 do Program.cs agora deve ser semelhante a 

    `private const string UserName = "cosmos-db-quickstart";`

    Você também pode colar o mesmo valor em `<PROVIDE>` na linha 15 para o valor CONTACT POINT:

    `private const string CassandraContactPoint = "cosmos-db-quickstarts.cassandra.cosmosdb.azure.com"; //  DnsName`

1. Volte ao portal e copie o valor da SENHA. Cole o valor da SENHA do portal sobre `<PROVIDE>` na linha 14.

    A linha 14 do Program.cs agora deve ser semelhante a 

    `private const string Password = "2Ggkr662ifxz2Mg...==";`

1. Volte ao portal e copie o valor do PONTO DE CONTATO. Cole o valor do PONTO DE CONTATO do portal sobre `<PROVIDE>` na linha 16.

    A linha 16 de Program.cs agora será semelhante a 

    `private const string CASSANDRACONTACTPOINT = "quickstart-cassandra-api.cassandra.cosmos.azure.com";`

1. Salve o arquivo Program.cs.
    
## <a name="run-the-net-core-app"></a>Execute o aplicativo .NET Core

1. No Visual Studio, selecione **Ferramentas** > **Gerenciador de Pacotes do NuGet** > **Console do Gerenciador de Pacotes**.

2. No prompt de comando, use o comando a seguir para instalar o pacote NuGet do driver .NET. 

    ```cmd
    Install-Package CassandraCSharpDriver
    ```
3. Aperte CTRL + F5 para executar o aplicativo. Seu aplicativo é exibido na janela do seu console. 

    :::image type="content" source="./media/create-cassandra-dotnet/output.png" alt-text="Exibir e verificar a saída":::

    Pressione CTRL + C para interromper a execução do programa e feche a janela do console. 
    
4. No portal do Azure, abra **Data Explorer** para consultar, modificar e trabalhar com esses novos dados.

    :::image type="content" source="./media/create-cassandra-dotnet/data-explorer.png" alt-text="Exibir os dados no Data Explorer":::

## <a name="review-slas-in-the-azure-portal"></a>Examinar SLAs no Portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu como criar uma conta do Azure Cosmos DB, como criar um contêiner usando o Data Explorer e como executar um aplicativo Web. Agora, é possível importar outros dados para sua conta do Cosmos DB. 

> [!div class="nextstepaction"]
> [Importar dados do Cassandra para o Azure Cosmos DB](cassandra-import-data.md)
