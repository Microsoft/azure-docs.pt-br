---
title: Criar um aplicativo Web usando a API do Azure Cosmos DB para MongoDB e o SDK do .NET
description: Apresenta um exemplo de código .NET que você pode usar para conectar-se e consultar usando a API do Azure Cosmos DB para MongoDB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 10/15/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 65ceca7ee0a6482e489ca768b9b33981d313c3db
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101658022"
---
# <a name="quickstart-build-a-net-web-app-using-azure-cosmos-dbs-api-for-mongodb"></a>Início Rápido: Criar um aplicativo Web do .NET usando a API para MongoDB do Azure Cosmos DB 
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](./mongodb-introduction.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-go.md)
>  

O Azure Cosmos DB é o banco de dados NoSQL rápido da Microsoft com APIs abertas para qualquer escala. É possível criar e consultar rapidamente bancos de dados de documentos, de chave/valor e de grafo, que se beneficiem das funcionalidades de escala horizontal e de distribuição global no núcleo do Cosmos DB. 

Este início rápido demonstra como criar uma conta do Cosmos com a [API do Azure Cosmos DB para MongoDB](mongodb-introduction.md). Em seguida, você criará e implantará um aplicativo Web de lista de tarefas criado usando o [driver .NET do MongoDB](https://docs.mongodb.com/ecosystem/drivers/csharp/).

## <a name="prerequisites-to-run-the-sample-app"></a>Pré-requisitos para executar o aplicativo de exemplo

Para executar o exemplo, você precisará do [Visual Studio](https://www.visualstudio.com/downloads/) e de uma conta válida do Azure Cosmos DB.

Caso ainda não tenha o Visual Studio, baixe o [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/) com a carga de trabalho de **desenvolvimento para a Web e ASP.NET** inserida na instalação.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 

<a id="create-account"></a>
## <a name="create-a-database-account"></a>Criar uma conta de banco de dados

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

O exemplo descrito neste artigo é compatível com MongoDB.Driver versão 2.6.1.

## <a name="clone-the-sample-app"></a>Clonar o aplicativo de exemplo

Primeiro, baixe o aplicativo de exemplo do GitHub. 

1. Abra um prompt de comando, crie uma nova pasta chamada exemplos de git e feche o prompt de comando.

    ```bash
    mkdir "C:\git-samples"
    ```

2. Abra uma janela de terminal de git, como git bash, e use o comando `cd` para alterar para a nova pasta para instalar o aplicativo de exemplo.

    ```bash
    cd "C:\git-samples"
    ```

3. Execute o comando a seguir para clonar o repositório de exemplo. Este comando cria uma cópia do aplicativo de exemplo no seu computador. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-getting-started.git
    ```

Se não quiser usar o git, você também pode [baixar o projeto como um arquivo ZIP](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-getting-started/archive/master.zip).

## <a name="review-the-code"></a>Examine o código

Esta etapa é opcional. Se você estiver interessado em aprender como os recursos de banco de dados são criados no código, poderá examinar os snippets de código a seguir. Caso contrário, você poderá pular para [Atualizar sua cadeia de conexão](#update-your-connection-string). 

Todos os snippets de código a seguir são retirados do arquivo Dal.cs no diretório DAL.

* Inicialize o cliente.

    ```cs
        MongoClientSettings settings = new MongoClientSettings();
        settings.Server = new MongoServerAddress(host, 10255);
        settings.UseSsl = true;
        settings.SslSettings = new SslSettings();
        settings.SslSettings.EnabledSslProtocols = SslProtocols.Tls12;

        MongoIdentity identity = new MongoInternalIdentity(dbName, userName);
        MongoIdentityEvidence evidence = new PasswordEvidence(password);

        settings.Credential = new MongoCredential("SCRAM-SHA-1", identity, evidence);

        MongoClient client = new MongoClient(settings);
    ```

* Recupere o banco de dados e a coleção.

    ```cs
    private string dbName = "Tasks";
    private string collectionName = "TasksList";

    var database = client.GetDatabase(dbName);
    var todoTaskCollection = database.GetCollection<MyTask>(collectionName);
    ```

* Recupere todos os documentos.

    ```cs
    collection.Find(new BsonDocument()).ToList();
    ```

Criar uma tarefa e inseri-la na coleção

   ```csharp
    public void CreateTask(MyTask task)
    {
        var collection = GetTasksCollectionForEdit();
        try
        {
            collection.InsertOne(task);
        }
        catch (MongoCommandException ex)
        {
            string msg = ex.Message;
        }
    }
   ```
   Da mesma forma, é possível atualizar e excluir documentos usando os métodos de [coleção. UpdateOne()](https://docs.mongodb.com/stitch/mongodb/actions/collection.updateOne/index.html) e [coleção. DeleteOne()](https://docs.mongodb.com/stitch/mongodb/actions/collection.deleteOne/index.html). 

## <a name="update-your-connection-string"></a>Atualizar sua cadeia de conexão

Agora, volte ao portal do Azure para obter informações sobre a cadeia de conexão e copiá-las para o aplicativo.

1. No [portal do Azure](https://portal.azure.com/), na sua conta do Cosmos, no painel de navegação esquerdo, clique em **Cadeia de Conexão** e, em seguida, clique em **Chaves de leitura/gravação**. Você usará os botões de cópia no lado direito da tela para copiar o Nome de usuário, Senha e Host para o arquivo Dal.cs na próxima etapa.

2. Abra o arquivo **Dal.cs** no diretório **DAL**. 

3. Copie o valor do **nome de usuário** do portal (usando o botão de cópia) e transforme-o no valor do **nome de usuário** no arquivo **Dal.cs**. 

4. Em seguida, copie o valor de **host** do portal e transforme-o no valor do **host** no arquivo **Dal.cs**. 

5. Em seguida, copie sua **senha** do portal e transforme-o no valor da **senha** no arquivo **Dal.cs**. 

Agora, você atualizou o aplicativo com todas as informações necessárias para comunicar-se com o Cosmos DB. 
    
## <a name="run-the-web-app"></a>Executar o aplicativo Web

1. No Visual Studio, clique com o botão direito do mouse no projeto no **Gerenciador de Soluções** e clique em **Gerenciar Pacotes NuGet**. 

2. Na caixa **Procurar** do NuGet, digite *MongoDB.Driver*.

3. Nos resultados, instale a biblioteca **MongoDB.Driver**. Isso instala o pacote do MongoDB.Driver, bem como todas as dependências.

4. Clique em CTRL + F5 para executar o aplicativo. Seu aplicativo é exibido no navegador. 

5. Clique em **Criar** no navegador e crie algumas novas tarefas em seu aplicativo de lista de tarefas.

## <a name="review-slas-in-the-azure-portal"></a>Examinar SLAs no Portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu como criar uma conta do Cosmos, criar uma coleção e executar um aplicativo de console. Agora você pode importar dados adicionais para o banco de dados Cosmos. 

> [!div class="nextstepaction"]
> [Importar dados do MongoDB no Azure Cosmos DB](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json)