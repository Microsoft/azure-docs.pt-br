---
title: 'Tutorial: Criar um aplicativo de console .NET para gerenciar dados na conta da API do SQL do Azure Cosmos DB'
description: 'Tutorial: Saiba como criar recursos da API do SQL para o Azure Cosmos DB usando um aplicativo de console C#.'
author: kirankumarkolli
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 11/05/2019
ms.author: kirankk
ms.custom: devx-track-csharp
ms.openlocfilehash: 49fa928285b29eaff806b009cf327e84e17491c6
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93098715"
---
# <a name="tutorial-build-a-net-console-app-to-manage-data-in-azure-cosmos-db-sql-api-account"></a>Tutorial: Criar um aplicativo de console .NET para gerenciar dados na conta da API do SQL do Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [Java](./create-sql-api-java.md)
> * [Async Java](./create-sql-api-java.md)
> * [Node.js](sql-api-nodejs-get-started.md)
>

Bem-vindo ao tutorial de introdução da API do SQL do Azure Cosmos DB. Após seguir este tutorial, você terá um aplicativo de console que cria e consulta recursos do Azure Cosmos DB.

Este tutorial usa a versão 3.0 ou posterior do [SDK do .NET do Azure Cosmos DB](https://www.nuget.org/packages/Microsoft.Azure.Cosmos). É possível trabalhar com o [.NET Framework ou o .NET Core](https://dotnet.microsoft.com/download).

Este tutorial abrange:

> [!div class="checklist"]
>
> * Criar e conectar-se a uma conta do Azure Cosmos
> * Como configurar o projeto no Visual Studio
> * Criar um banco de dados e um contêiner
> * Adicionando itens ao contêiner
> * Consultar um contêiner
> * Executando operações CRUD (criar, ler, atualizar e excluir) nos item
> * Excluir o banco de dados

Você não tem tempo? Não se preocupe! A solução completa está disponível em [GitHub](https://github.com/Azure-Samples/cosmos-dotnet-getting-started). Vá para a [seção Obter a solução completa do tutorial](#GetSolution) para obter instruções rápidas.

Agora vamos começar!

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta ativa do Azure. Se não tiver uma, você poderá se inscrever em uma [conta gratuita](https://azure.microsoft.com/free/).

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]

## <a name="step-1-create-an-azure-cosmos-db-account"></a>Etapa 1: Criar uma conta do Azure Cosmos DB

Vamos criar uma conta do Azure Cosmos DB. Se já tiver uma conta que deseja usar, ignore esta seção. Para usar o Emulador do Azure Cosmos DB, siga as etapas em [Emulador do Azure Cosmos DB](local-emulator.md) para configurá-lo. Em seguida, passe para [Etapa 2: Configurar seu projeto do Visual Studio](#SetupVS).

[!INCLUDE [create-dbaccount-preview](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="step-2-set-up-your-visual-studio-project"></a><a id="SetupVS"></a>Etapa 2: Configurar seu projeto do Visual Studio

1. Abra o Visual Studio e selecione **Criar um projeto**.
1. Em **Criar um novo projeto** , escolha **Aplicativo de Console (.NET Framework)** para C# e, em seguida, selecione **Avançar**.
1. Nomeie seu projeto *CosmosGettingStartedTutorial* e, em seguida, selecione **Criar**.

    :::image type="content" source="./media/sql-api-get-started/configure-cosmos-getting-started-2019.png" alt-text="Configurar o projeto":::

1. No **Gerenciador de Soluções** , clique com o botão direito do mouse em seu novo aplicativo de console, que está na solução do Visual Studio, e selecione **Gerenciar pacotes NuGet**.
1. No **Gerenciador de Pacotes NuGet** , selecione **Procurar** e pesquise por *Microsoft.Azure.Cosmos*. Escolha **Microsoft. Azure. Cosmos** e selecione **Instalar**.

   :::image type="content" source="./media/sql-api-get-started/cosmos-getting-started-manage-nuget-2019.png" alt-text="Instalar o NuGet para o SDK de cliente do Azure Cosmos DB":::

   A ID do pacote para a Biblioteca de Clientes do API do SQL do Azure Cosmos DB é [Biblioteca de Clientes do Microsoft Azure Cosmos DB](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/).

Ótimo! Agora que a instalação está concluída, vamos começar a escrever algum código. Para ver o projeto completo deste tutorial, confira [Desenvolvendo um aplicativo de console do .NET usando o Azure Cosmos DB](https://github.com/Azure-Samples/cosmos-dotnet-getting-started).

## <a name="step-3-connect-to-an-azure-cosmos-db-account"></a><a id="Connect"></a>Etapa 3: Conectar-se a uma conta do Azure Cosmos DB

1. Substitua as referências no início de seu aplicativo C#, no arquivo *Program.cs* , por estas referências:

   ```csharp
   using System;
   using System.Threading.Tasks;
   using System.Configuration;
   using System.Collections.Generic;
   using System.Net;
   using Microsoft.Azure.Cosmos;
   ```

1. Adicione essas constantes e variáveis à classe `Program`.

    ```csharp
    public class Program
    {
        // ADD THIS PART TO YOUR CODE

        // The Azure Cosmos DB endpoint for running this sample.
        private static readonly string EndpointUri = "<your endpoint here>";
        // The primary key for the Azure Cosmos account.
        private static readonly string PrimaryKey = "<your primary key>";

        // The Cosmos client instance
        private CosmosClient cosmosClient;

        // The database we will create
        private Database database;

        // The container we will create.
        private Container container;

        // The name of the database and container we will create
        private string databaseId = "FamilyDatabase";
        private string containerId = "FamilyContainer";
    }
    ```

   > [!NOTE]
   > Se estiver familiarizado com a versão anterior do SDK do .NET, você poderá estar familiarizado com os termos *coleção* e *documento*. Como o Azure Cosmos DB é compatível com vários modelos de API, a versão 3.0 do SDK do .NET usa os termos genéricos *contêiner* e *item*. Um *contêiner* pode ser uma coleção, um grafo ou uma tabela. Um *item* pode ser um documento, borda/vértice ou linha e é o conteúdo do contêiner. Para obter mais informações, confira [Trabalhar com bancos de dados, contêineres e itens no Azure Cosmos DB](account-databases-containers-items.md).

1. Abra o [Portal do Azure](https://portal.azure.com). Encontre sua conta do Azure Cosmos DB e selecione **Chaves**.

   :::image type="content" source="./media/sql-api-get-started/cosmos-getting-started-portal-keys.png" alt-text="Obter chaves do Azure Cosmos DB no portal do Azure":::

1. Em *Program.cs* , substitua `<your endpoint URL>` pelo valor do **URI**. Substitua `<your primary key>` pelo valor da **CHAVE PRIMÁRIA**.

1. Abaixo do método **Main** , adicione uma nova tarefa assíncrona denominada **GetStartedDemoAsync** , que cria uma instância do novo `CosmosClient`.

    ```csharp
    public static async Task Main(string[] args)
    {
    }

    // ADD THIS PART TO YOUR CODE
    /*
        Entry point to call methods that operate on Azure Cosmos DB resources in this sample
    */
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
    }
    ```

    Usaremos **GetStartedDemoAsync** como o ponto de entrada que chama os métodos que operam em recursos do Azure Cosmos DB.

1. Adicione o código a seguir para executar a tarefa assíncrona **GetStartedDemoAsync** a partir do método **Main**. O método **Main** captura as exceções e as grava no console.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=Main)]

1. Selecione F5 para executar seu aplicativo.

    O console exibe a mensagem: **Fim da demonstração, pressione qualquer tecla para sair.** Essa mensagem confirma que seu aplicativo fez uma conexão com o Azure Cosmos DB. Em seguida, você pode fechar a janela do console.

Parabéns! Você se conectou com sucesso a uma conta do Azure Cosmos DB.

## <a name="step-4-create-a-database"></a>Etapa 4: Criar um banco de dados

Um banco de dados é o contêiner lógico de itens particionados em contêineres. O método `CreateDatabaseIfNotExistsAsync` ou `CreateDatabaseAsync` da classe [CosmosClient](/dotnet/api/microsoft.azure.cosmos.cosmosclient) pode criar um banco de dados.

1. Copie e cole o método `CreateDatabaseAsync` abaixo do método `GetStartedDemoAsync`.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=CreateDatabaseAsync&highlight=7)]

    `CreateDatabaseAsync` cria um banco de dados com a ID `FamilyDatabase`, caso ainda não exista, que tem a ID especificada no campo `databaseId`.

1. Copie e cole o código abaixo, no qual você cria uma instância do CosmosClient para chamar o método **CreateDatabaseAsync** recém-adicionado.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);

        //ADD THIS PART TO YOUR CODE
        await this.CreateDatabaseAsync();
    }
    ```

    Seu *Program.cs* deve se parecer com a imagem a seguir, com seu ponto de extremidade e a chave primária preenchidos.

    ```csharp
    using System;
    using System.Threading.Tasks;
    using System.Configuration;
    using System.Collections.Generic;
    using System.Net;
    using Microsoft.Azure.Cosmos;

    namespace CosmosGettingStartedTutorial
    {
        class Program
        {
            // The Azure Cosmos DB endpoint for running this sample.
            private static readonly string EndpointUri = "<your endpoint here>";
            // The primary key for the Azure Cosmos account.
            private static readonly string PrimaryKey = "<your primary key>";

            // The Cosmos client instance
            private CosmosClient cosmosClient;

            // The database we will create
            private Database database;

            // The container we will create.
            private Container container;

            // The name of the database and container we will create
            private string databaseId = "FamilyDatabase";
            private string containerId = "FamilyContainer";

            public static async Task Main(string[] args)
            {
                try
                {
                    Console.WriteLine("Beginning operations...");
                    Program p = new Program();
                    await p.GetStartedDemoAsync();
                }
                catch (CosmosException de)
                {
                    Exception baseException = de.GetBaseException();
                    Console.WriteLine("{0} error occurred: {1}\n", de.StatusCode, de);
                }
                catch (Exception e)
                {
                    Console.WriteLine("Error: {0}\n", e);
                }
                finally
                {
                    Console.WriteLine("End of demo, press any key to exit.");
                    Console.ReadKey();
                }
            }

            /// <summary>
            /// Entry point to call methods that operate on Azure Cosmos DB resources in this sample
            /// </summary>
            public async Task GetStartedDemoAsync()
            {
                // Create a new instance of the Cosmos Client
                this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
                await this.CreateDatabaseAsync();
            }

            /// <summary>
            /// Create the database if it does not exist
            /// </summary>
            private async Task CreateDatabaseAsync()
            {
                // Create a new database
                this.database = await this.cosmosClient.CreateDatabaseIfNotExistsAsync(databaseId);
                Console.WriteLine("Created Database: {0}\n", this.database.Id);
            }
        }
    }
    ```

1. Selecione F5 para executar seu aplicativo.

   > [!NOTE]
   > Caso você receba um erro "exceção 503 de serviço não disponível", é possível que as [portas](sql-sdk-connection-modes.md#service-port-ranges) exigidas para o modo de conectividade direto estejam bloqueadas por um firewall. Para corrigir esse problema, abra as portas necessárias ou use a conectividade do modo de gateway, conforme mostrado no código a seguir:
   ```csharp
     // Create a new instance of the Cosmos Client in Gateway mode
     this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey, new CosmosClientOptions()
            {
                ConnectionMode = ConnectionMode.Gateway
            });
   ```

Parabéns! Você criou um banco de dados do Azure Cosmos com êxito.  

## <a name="step-5-create-a-container"></a><a id="CreateColl"></a>Etapa 5: Criar um contêiner

> [!WARNING]
> O método `CreateContainerIfNotExistsAsync` cria um novo contêiner, o que tem implicações quanto ao preço. Para obter mais detalhes, visite a nossa [página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/).
>
>

Um contêiner pode ser criado usando o método [**CreateContainerIfNotExistsAsync**](/dotnet/api/microsoft.azure.cosmos.database.createcontainerifnotexistsasync?view=azure-dotnet&preserve-view=true#Microsoft_Azure_Cosmos_Database_CreateContainerIfNotExistsAsync_Microsoft_Azure_Cosmos_ContainerProperties_System_Nullable_System_Int32__Microsoft_Azure_Cosmos_RequestOptions_System_Threading_CancellationToken_) ou [**CreateContainerAsync**](/dotnet/api/microsoft.azure.cosmos.database.createcontainerasync?view=azure-dotnet&preserve-view=true#Microsoft_Azure_Cosmos_Database_CreateContainerAsync_Microsoft_Azure_Cosmos_ContainerProperties_System_Nullable_System_Int32__Microsoft_Azure_Cosmos_RequestOptions_System_Threading_CancellationToken_) na classe `CosmosDatabase`. Um contêiner é formado por itens (que, no caso da API do SQL, são documentos JSON) e a lógica do aplicativo do servidor associada em JavaScript, por exemplo, procedimentos armazenados, funções definidas pelo usuário e gatilhos.

1. Copie e cole o método `CreateContainerAsync` abaixo do método `CreateDatabaseAsync`. `CreateContainerAsync` cria um contêiner com a ID `FamilyContainer`, caso ainda não exista, usando a ID especificada no campo `containerId` particionado pela propriedade `LastName`.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=CreateContainerAsync&highlight=9)]

1. Copie e cole o código a seguir, no qual você criou uma instância do CosmosClient para chamar o método **CreateContainer** que você acabou de adicionar.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();

        //ADD THIS PART TO YOUR CODE
        await this.CreateContainerAsync();
    }
    ```

1. Selecione F5 para executar seu aplicativo.

Parabéns! Você criou um contêiner do Azure Cosmos com êxito.  

## <a name="step-6-add-items-to-the-container"></a><a id="CreateDoc"></a>Etapa 6: Adicionar itens ao contêiner

O método [**CreateItemAsync**](/dotnet/api/microsoft.azure.cosmos.container.createitemasync?view=azure-dotnet&preserve-view=true#Microsoft_Azure_Cosmos_Container_CreateItemAsync__1___0_System_Nullable_Microsoft_Azure_Cosmos_PartitionKey__Microsoft_Azure_Cosmos_ItemRequestOptions_System_Threading_CancellationToken_) da classe `CosmosContainer` pode criar um item. Ao usar a API do SQL, os itens são projetados como documentos, que são conteúdo JSON arbitrário definido pelo usuário. Agora você pode inserir um item no contêiner do Azure Cosmos.

Primeiro, vamos criar uma classe `Family` que representa os objetos armazenados no Azure Cosmos DB nesta amostra. Também criaremos as subclasses `Parent`, `Child`, `Pet`, `Address` que são usadas em `Family`. O item precisa ter uma propriedade `Id` serializada como `id` em JSON.

1. Selecione Ctrl + Shift + A para abrir **Adicionar Novo Item**. Adicione uma nova classe `Family.cs` ao seu projeto.

    :::image type="content" source="./media/sql-api-get-started/cosmos-getting-started-add-family-class-2019.png" alt-text="Captura de tela da adição de uma nova classe Family.cs no projeto":::

1. Copie e cole a classe `Family`, `Parent`, `Child`, `Pet` e `Address` em `Family.cs`.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Family.cs)]


1. De volta a *Program.cs* , adicione o método `AddItemsToContainerAsync` após o método `CreateContainerAsync`.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=AddItemsToContainerAsync)]


    O código faz uma verificação para garantir que não haja um item com a mesma ID. Vamos inserir dois itens, um para a *Família Martins* e um para a *Família Barros*.

1. Adicione uma chamada para `AddItemsToContainerAsync` no método `GetStartedDemoAsync`.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();
        await this.CreateContainerAsync();

        //ADD THIS PART TO YOUR CODE
        await this.AddItemsToContainerAsync();
    }
    ```

1. Selecione F5 para executar seu aplicativo.

Parabéns! Você criou dois itens do Azure Cosmos com êxito.  

## <a name="step-7-query-azure-cosmos-db-resources"></a><a id="Query"></a>Etapa 7: Consultar recursos do Azure Cosmos DB

O Azure Cosmos DB tem suporte para consultas avançadas de documentos JSON armazenados em cada contêiner. Para obter mais informações, confira [Introdução a consultas SQL](./sql-query-getting-started.md). O código de exemplo a seguir mostra como executar uma consulta em relação aos itens que inserimos na etapa anterior.

1. Copie e cole o método `QueryItemsAsync` após o método `AddItemsToContainerAsync`.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=QueryItemsAsync&highlight=10-11,17-18)]

1. Adicione uma chamada para ``QueryItemsAsync`` no método ``GetStartedDemoAsync``.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();
        await this.CreateContainerAsync();
        await this.AddItemsToContainerAsync();

        //ADD THIS PART TO YOUR CODE
        await this.QueryItemsAsync();
    }
    ```

1. Selecione F5 para executar seu aplicativo.

Parabéns! Você consultou um contêiner do Azure Cosmos com êxito.

## <a name="step-8-replace-a-json-item"></a><a id="ReplaceItem"></a>Etapa 8: Substituir um item JSON

Agora, vamos atualizar um item no Azure Cosmos DB. Nós alteraremos a propriedade `IsRegistered` do `Family` e o `Grade` de um dos filhos.

1. Copie e cole o método `ReplaceFamilyItemAsync` após o método `QueryItemsAsync`.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=ReplaceFamilyItemAsync&highlight=15)]

1. Adicione uma chamada para `ReplaceFamilyItemAsync` no método `GetStartedDemoAsync`.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();
        await this.CreateContainerAsync();
        await this.AddItemsToContainerAsync();
        await this.QueryItemsAsync();

        //ADD THIS PART TO YOUR CODE
        await this.ReplaceFamilyItemAsync();
    }
    ```

1. Selecione F5 para executar seu aplicativo.

Parabéns! Você substituiu um item do Azure Cosmos com êxito.

## <a name="step-9-delete-item"></a><a id="DeleteDocument"></a>Etapa 9: Excluir item

Agora, excluiremos um item no Azure Cosmos DB.

1. Copie e cole o método `DeleteFamilyItemAsync` após o método `ReplaceFamilyItemAsync`.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=DeleteFamilyItemAsync&highlight=10)]

1. Adicione uma chamada para `DeleteFamilyItemAsync` no método `GetStartedDemoAsync`.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();
        await this.CreateContainerAsync();
        await this.AddItemsToContainerAsync();
        await this.QueryItemsAsync();
        await this.ReplaceFamilyItemAsync();

        //ADD THIS PART TO YOUR CODE
        await this.DeleteFamilyItemAsync();
    }
    ```

1. Selecione F5 para executar seu aplicativo.

Parabéns! Você excluiu um item do Azure Cosmos com êxito.

## <a name="step-10-delete-the-database"></a><a id="DeleteDatabase"></a>Etapa 10: Excluir o banco de dados

Agora, vamos excluir nosso banco de dados. Excluir o banco de dados criado remove o banco de dados e todos os recursos filhos. Os recursos incluem contêineres, itens e procedimentos armazenados, funções definidas pelo usuário e gatilhos. Também descartamos a instância `CosmosClient`.

1. Copie e cole o método `DeleteDatabaseAndCleanupAsync` após o método `DeleteFamilyItemAsync`.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=DeleteDatabaseAndCleanupAsync)]

1. Adicione uma chamada para ``DeleteDatabaseAndCleanupAsync`` no método ``GetStartedDemoAsync``.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=GetStartedDemoAsync&highlight=14)]

1. Selecione F5 para executar seu aplicativo.

Parabéns! Você excluiu um banco de dados do Azure Cosmos com êxito.

## <a name="step-11-run-your-c-console-application-all-together"></a><a id="Run"></a>Etapa 11: Executar o aplicativo de console C# inteiro!

Escolha F5 no Visual Studio para compilar e executar o aplicativo no modo de depuração.

Você deverá ver a saída de todo o aplicativo em uma janela do console. A saída mostra os resultados das consultas que adicionamos. Ela deve corresponder ao texto de exemplo abaixo.

```cmd
Beginning operations...

Created Database: FamilyDatabase

Created Container: FamilyContainer

Created item in database with id: Andersen.1 Operation consumed 11.43 RUs.

Created item in database with id: Wakefield.7 Operation consumed 14.29 RUs.

Running query: SELECT * FROM c WHERE c.LastName = 'Andersen'

        Read {"id":"Andersen.1","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":false}

Updated Family [Wakefield,Wakefield.7].
        Body is now: {"id":"Wakefield.7","LastName":"Wakefield","Parents":[{"FamilyName":"Wakefield","FirstName":"Robin"},{"FamilyName":"Miller","FirstName":"Ben"}],"Children":[{"FamilyName":"Merriam","FirstName":"Jesse","Gender":"female","Grade":6,"Pets":[{"GivenName":"Goofy"},{"GivenName":"Shadow"}]},{"FamilyName":"Miller","FirstName":"Lisa","Gender":"female","Grade":1,"Pets":null}],"Address":{"State":"NY","County":"Manhattan","City":"NY"},"IsRegistered":true}

Deleted Family [Wakefield,Wakefield.7]

Deleted Database: FamilyDatabase

End of demo, press any key to exit.
```

Parabéns! Você concluiu este tutorial e tem um aplicativo de console em C# funcional!

## <a name="get-the-complete-tutorial-solution"></a><a id="GetSolution"></a> Obter a solução completa do tutorial

Se não teve tempo para concluir as etapas neste tutorial ou se deseja apenas baixar os exemplos de código, você poderá baixá-los.

Para compilar a solução `GetStarted`, você precisará dos pré-requisitos a seguir:

* Uma conta ativa do Azure. Se não tiver uma, você poderá se inscrever em uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma [conta do Azure Cosmos DB][cosmos-db-create-account].
* A solução [GetStarted](https://github.com/Azure-Samples/cosmos-dotnet-getting-started) disponível no GitHub.

Para restaurar as referências ao SDK do .NET do Azure Cosmos DB no Visual Studio, clique com o botão direito do mouse na solução no **Gerenciador de Soluções** e selecione **Restaurar pacotes do NuGet**. Em seguida, no arquivo *App.config* , atualize os valores `EndPointUri` e `PrimaryKey`, conforme descrito em [Etapa 3: Conectar-se a uma conta do Azure Cosmos DB](#Connect).

Pronto, compile-o e você pode continuar!

## <a name="next-steps"></a>Próximas etapas

* Quer um tutorial mais complexo do ASP.NET MVC? Confira o [Tutorial: Desenvolver um aplicativo Web MVC do ASP.NET Core com o Azure Cosmos DB usando o SDK do .NET](sql-api-dotnet-application.md).
* Deseja realizar testes de desempenho e escala com o Azure Cosmos DB? Confira [Testes de desempenho e escala com o Azure Cosmos DB](performance-testing.md).
* Para saber como monitorar as solicitações, o uso e o armazenamento do Azure Cosmos DB, confira [Monitorar métricas de desempenho e armazenamento no Azure Cosmos DB](./monitor-cosmos-db.md).
* Para executar consultas em nosso conjunto de dados de exemplo, confira o [Playground para Consultas](https://www.documentdb.com/sql/demo).
* Para saber mais sobre o Azure Cosmos DB, confira [Bem-vindo ao Azure Cosmos DB](./introduction.md).

[cosmos-db-create-account]: create-sql-api-java.md#create-a-database-account