---
title: Criar um aplicativo de console .NET para gerenciar dados na conta da API do SQL do Azure Cosmos DB
description: Saiba como criar recursos da API do SQL para o Azure Cosmos DB usando um aplicativo de console C#.
author: kirankumarkolli
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 07/23/2019
ms.author: kirankk
ms.openlocfilehash: 37b6a9947d3cabe1f566f842e321229efe9d03b6
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68598523"
---
# <a name="build-a-net-console-app-to-manage-data-in-azure-cosmos-db-sql-api-account"></a>Criar um aplicativo de console .NET para gerenciar dados na conta da API do SQL do Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [Async Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
>

Bem-vindo ao tutorial de introdução da API do SQL do Azure Cosmos DB. Após seguir este tutorial, você terá um aplicativo de console que cria e consulta recursos do Azure Cosmos DB. Este tutorial usa a [Versão 3.0 e posterior](https://www.nuget.org/packages/Microsoft.Azure.Cosmos) do SDK do .NET do Azure Cosmos DB, que pode ser direcionada para o [.NET Framework](https://dotnet.microsoft.com/download) ou o [.NET Core](https://dotnet.microsoft.com/download).

Este tutorial abrange:

> [!div class="checklist"]
> * Criar e conectar-se a uma conta do Azure Cosmos
> * Como configurar o projeto no Visual Studio
> * Criar um banco de dados e um contêiner
> * Adicionando itens ao contêiner
> * Consultar um contêiner
> * Operações CRUD no item
> * Excluir o banco de dados

Você não tem tempo? Não se preocupe! A solução completa está disponível em [GitHub](https://github.com/Azure-Samples/cosmos-dotnet-getting-started). Vá para a [seção Obter a solução completa do tutorial](#GetSolution) para obter instruções rápidas.

Agora vamos começar!

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta ativa do Azure. Se não tiver uma, você poderá se inscrever em uma [conta gratuita](https://azure.microsoft.com/free/).

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]

## <a name="step-1-create-an-azure-cosmos-db-account"></a>Etapa 1: Criar uma conta do Azure Cosmos DB
Vamos criar uma conta do Azure Cosmos DB. Se você já tem uma conta que deseja usar, você pode pular para [Configurar sua solução do Visual Studio](#SetupVS). Se estiver usando o Emulador do Azure Cosmos DB, execute as etapas no [Emulador do Azure Cosmos DB](local-emulator.md) para configurar o emulador e pule para [Configurar seu projeto do Visual Studio](#SetupVS).

[!INCLUDE [create-dbaccount-preview](../../includes/cosmos-db-create-dbaccount-preview.md)]

## <a id="SetupVS"></a>Etapa 2: Configurar seu projeto do Visual Studio
1. Abra o **Visual Studio 2017** em seu computador.
1. No menu **Arquivo**, selecione **Novo** e depois **Projeto**.
1. Na caixa de diálogo **Novo Projeto**, selecione **Visual C#**  / **Aplicativo de Console (.NET Framework)** , nomeie o projeto e clique em **OK**.
    ![Captura de tela da janela Novo Projeto](./media/sql-api-get-started/dotnet-tutorial-visual-studio-new-project.png)

    > [!NOTE]
    > Para o destino .NET Core, na caixa de diálogo **Novo Projeto**, selecione **Visual C#**  / **Aplicativo de Console (.NET Core)** , nomeie o projeto e, em seguida, clique em **OK**

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no seu novo aplicativo de console, que está em sua solução do Visual Studio e clique em **gerenciar pacotes NuGet...**

    ![Captura de tela do menu exibido pelo clique com o botão direito do mouse para o projeto](./media/sql-api-get-started/dotnet-tutorial-visual-studio-manage-nuget.png)
1. Na guia **NuGet**, clique em **Procurar** e insira **Microsoft.Azure.Cosmos** na caixa de pesquisa.
1. Nos resultados, encontre **Microsoft.Azure.Cosmos** e clique em **Instalar**.
   A ID do pacote para a Biblioteca de Clientes do API do SQL do Azure Cosmos DB é [Biblioteca de Clientes do Microsoft Azure Cosmos DB](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/).
   ![Captura de tela do menu do NuGet para localizar o SDK do cliente do Azure Cosmos DB](./media/sql-api-get-started/dotnet-tutorial-visual-studio-manage-nuget-2.png)

    Se receber uma mensagem sobre a análise das alterações para a solução, clique em **OK**. Se receber uma mensagem sobre a aceitação da licença, clique em **Aceito**.

Ótimo! Agora que a instalação está concluída, vamos começar a escrever algum código. Você pode encontrar um projeto de código completo deste tutorial no [GitHub](https://github.com/Azure-Samples/cosmos-dotnet-getting-started).

## <a id="Connect"></a>Etapa 3: Conectar-se a uma conta do Azure Cosmos DB
1. Primeiro, substitua as referências no início de seu aplicativo C#, no arquivo **Program.cs**, por estas referências:

   ```csharp
   using System;
   using System.Threading.Tasks;
   using System.Configuration;
   using System.Collections.Generic;
   using System.Net;
   using Microsoft.Azure.Cosmos;
   ```

1. Agora, adicione essas constantes e variáveis à classe pública ``Program``.

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

    Observe que se você estiver familiarizado com a versão anterior do SDK do .NET, poderá estar acostumado a ver os termos “coleção” e “documento”. Como o Azure Cosmos DB é compatível com vários modelos de API, a versão 3.0 ou superior do SDK do .NET usa os termos genéricos “contêiner” e “item”. Um contêiner pode ser uma coleção, um gráfico ou uma tabela. Um item pode ser um documento, borda/vértice ou linha e descreve o conteúdo do contêiner. [Saiba mais sobre bancos de dados, contêineres e itens.](databases-containers-items.md)

1. Recupere a URL do ponto de extremidade e a chave primária no [portal do Azure](https://portal.azure.com).

    No Portal do Azure, navegue até sua conta do Azure Cosmos DB e clique em **Chaves**.

    Copie o URI do portal e cole-o em `<your endpoint URL>` no arquivo ```Program.cs```. Copie a CHAVE PRIMÁRIA do portal e cole-a em `<your primary key>`.

   ![Captura de tela para obter chaves do Azure Cosmos DB no portal do Azure](./media/sql-api-get-started/dotnet-tutorial-portal-keys.png)

1. Em seguida, vamos criar uma nova instância do ```CosmosClient``` e configurar uma estrutura para nosso programa.

    Abaixo do método **Main**, adicione esta nova tarefa assíncrona denominada **GetStartedDemoAsync**, que criará uma instância do novo ```CosmosClient```. Usaremos **GetStartedDemoAsync** como ponto de entrada que chama os métodos que operam em recursos do Azure Cosmos DB.

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

1. Adicione o código a seguir para executar a tarefa assíncrona **GetStartedDemoAsync** a partir do método **Main**. O método **Main** capturará as exceções e as gravará no console.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=Main)]

1. Selecione **F5** para executar seu aplicativo. A saída da janela do console exibe a mensagem `End of demo, press any key to exit.`, confirmando que a conexão com o Azure Cosmos DB foi feita. Em seguida, você pode fechar a janela do console.

Parabéns! Você se conectou com sucesso a uma conta do Azure Cosmos DB. 

## <a name="step-4-create-a-database"></a>Etapa 4: Criar um banco de dados
Um banco de dados pode ser criado usando a função [**CreateDatabaseIfNotExistsAsync**](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclient) ou [**CreateDatabaseAsync**](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclient) da classe ``CosmosClient``. Um banco de dados é o contêiner lógico de itens particionados em contêineres.

1. Copie e cole o método **CreateDatabaseAsync** abaixo do método **GetStartedDemoAsync**. **CreateDatabaseAsync** criará um banco de dados com a ID ``FamilyDatabase``, caso ele ainda não exista, com a ID especificada do campo ``databaseId``. 

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=CreateDatabaseAsync&highlight=7)]

1. Copie e cole o código abaixo, no qual você criou uma instância do CosmosClient para chamar o método **CreateDatabaseAsync** recém-adicionado.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);

        //ADD THIS PART TO YOUR CODE
        await this.CreateDatabaseAsync();
    }
    ```

    Neste ponto, seu código deve se parecer com a imagem a seguir, com seu ponto de extremidade e a chave primária preenchidos.

    ```csharp
    using System;
    using System.Threading.Tasks;
    using System.Configuration;
    using System.Collections.Generic;
    using System.Net;
    using Microsoft.Azure.Cosmos;

    namespace CosmosGettingStarted
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

Selecione **F5** para executar seu aplicativo.

Parabéns! Você criou um banco de dados do Azure Cosmos DB com êxito.  

## <a id="CreateColl"></a>Etapa 5: Criar um contêiner
> [!WARNING]
> Chamar o método **CreateContainerIfNotExistsAsync** criará um novo contêiner e isso implicará custos. Para obter mais detalhes, visite a nossa [página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/).
>
>

Um contêiner pode ser criado usando a função [**CreateContainerIfNotExistsAsync**](/dotnet/api/microsoft.azure.cosmos.database.createcontainerifnotexistsasync?view=azure-dotnet#Microsoft_Azure_Cosmos_Database_CreateContainerIfNotExistsAsync_Microsoft_Azure_Cosmos_ContainerProperties_System_Nullable_System_Int32__Microsoft_Azure_Cosmos_RequestOptions_System_Threading_CancellationToken_) ou [**CreateContainerAsync**](/dotnet/api/microsoft.azure.cosmos.database.createcontainerasync?view=azure-dotnet#Microsoft_Azure_Cosmos_Database_CreateContainerAsync_Microsoft_Azure_Cosmos_ContainerProperties_System_Nullable_System_Int32__Microsoft_Azure_Cosmos_RequestOptions_System_Threading_CancellationToken_) da classe **CosmosDatabase**. Um contêiner é formado por itens (que, no caso da API do SQL, são documentos JSON) e a lógica do aplicativo do servidor associada em JavaScript, por exemplo, procedimentos armazenados, funções definidas pelo usuário e gatilhos.

1. Copie e cole o método **CreateContainerAsync** abaixo do método **CreateDatabaseAsync**. **CreateContainerAsync** criará um contêiner com a ID ``FamilyContainer``, caso ele ainda não exista, com a ID especificada do campo ``containerId`` particionado pela propriedade ``LastName``.

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

   Selecione **F5** para executar seu aplicativo.

Parabéns! Você criou um contêiner do Azure Cosmos DB com êxito.  

## <a id="CreateDoc"></a>Etapa 6: Adicionar itens ao contêiner
Um item pode ser criado usando a função [**CreateItemAsync**](/dotnet/api/microsoft.azure.cosmos.container.createitemasync?view=azure-dotnet#Microsoft_Azure_Cosmos_Container_CreateItemAsync__1___0_System_Nullable_Microsoft_Azure_Cosmos_PartitionKey__Microsoft_Azure_Cosmos_ItemRequestOptions_System_Threading_CancellationToken_) da classe **CosmosContainer**. Ao usar a API do SQL, os itens são projetados como documentos, ou seja, o conteúdo JSON definido pelo usuário (arbitrário). Agora você pode inserir um item no contêiner do Azure Cosmos DB.

Primeiro, vamos criar uma classe **Family** que representará os objetos armazenados no Azure Cosmos DB nesta amostra. Também criaremos as subclasses **Parent**, **Child**, **Pet** e **Address** que são usadas em **Family**. Observe que o item precisa ter uma propriedade **Id** serializada como **id** em JSON.

1. Selecione **Ctrl + Shift + A** para abrir a caixa de diálogo **Adicionar Novo Item**. Adicione uma nova classe **Family.cs** ao seu projeto.

    ![Captura de tela da adição de uma nova classe Family.cs no projeto](./media/sql-api-get-started/dotnet-tutorial-visual-studio-add-family-class.png)

1. Copie e cole a classe **Family**, **Parent**, **Child**, **Pet** e **Address** em **Family.cs**.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Family.cs)]

1. Navegue de volta até **Program.cs** e adicione o método **AddItemsToContainerAsync** abaixo do método **CreateContainerAsync**.
Antes da criação, o código faz uma verificação para garantir que não haja um item com a mesma ID. Vamos inserir dois itens, um para a Família Martins e um para a Família Barros.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=AddItemsToContainerAsync)]

1. Adicione uma chamada para ``AddItemsToContainerAsync`` no método ``GetStartedDemoAsync``.

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

Selecione **F5** para executar seu aplicativo.

Parabéns! Você criou dois itens do Azure Cosmos DB com êxito.  

## <a id="Query"></a>Etapa 7: Consultar recursos do Azure Cosmos DB
O Azure Cosmos DB tem suporte para [consultas](sql-api-sql-query.md) avançadas de documentos JSON armazenados em cada coleção. O código de exemplo a seguir mostra como executar uma consulta em relação aos itens que inserimos na etapa anterior.

1. Copie e cole o método **QueryItemsAsync** abaixo do método **AddItemsToContainerAsync**.

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

Selecione **F5** para executar seu aplicativo.

Parabéns! Você consultou um contêiner do Azure Cosmos DB com êxito.

## <a id="ReplaceItem"></a>Etapa 8: Substituir um item JSON
Agora, vamos atualizar um item no Azure Cosmos DB.

1. Copie e cole o método **ReplaceFamilyItemAsync** abaixo do método **QueryItemsAsync**. Observe que estamos alterando a propriedade ``IsRegistered`` da família e a ``Grade`` de um dos filhos.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=ReplaceFamilyItemAsync&highlight=15)]

1. Adicione uma chamada para ``ReplaceFamilyItemAsync`` no método ``GetStartedDemoAsync``.

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

   Selecione **F5** para executar seu aplicativo.

Parabéns! Você substituiu um item do Azure Cosmos DB com êxito.

## <a id="DeleteDocument"></a>Etapa 9: Excluir item
Agora, excluiremos um item no Azure Cosmos DB.

1. Copie e cole o método **DeleteFamilyItemAsync** abaixo do método **ReplaceFamilyItemAsync**.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=DeleteFamilyItemAsync&highlight=10)]

1. Adicione uma chamada para ``DeleteFamilyItemAsync`` no método ``GetStartedDemoAsync``.

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

Selecione **F5** para executar seu aplicativo.

Parabéns! Você excluiu um item do Azure Cosmos DB com êxito.

## <a id="DeleteDatabase"></a>Etapa 10: Excluir o banco de dados
Agora, vamos excluir nosso banco de dados. Excluir o banco de dados criado removerá o banco de dados e todos os recursos filho (contêineres, itens e procedimentos armazenados, funções definidas pelo usuário e gatilhos). Também vamos descartar a instância do **CosmosClient**.

1. Copie e cole o método **DeleteDatabaseAndCleanupAsync** abaixo do método **DeleteFamilyItemAsync**.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=DeleteDatabaseAndCleanupAsync)]

1. Adicione uma chamada para ``DeleteDatabaseAndCleanupAsync`` no método ``GetStartedDemoAsync``.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=GetStartedDemoAsync&highlight=14)]

Selecione **F5** para executar seu aplicativo.

Parabéns! Você excluiu um banco de dados do Azure Cosmos DB com êxito.

## <a id="Run"></a>Etapa 11: Executar o aplicativo de console C# inteiro!
Escolha F5 no Visual Studio para compilar e executar o aplicativo no modo de depuração.

Você deverá ver a saída de todo o aplicativo em uma janela do console. A saída mostrará os resultados das consultas que adicionamos e deverá coincidir com o texto de exemplo abaixo.

```
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

## <a id="GetSolution"></a> Obter a solução completa do tutorial
Se você não teve tempo para concluir as etapas neste tutorial ou se deseja apenas baixar os exemplos de código, poderá obtê-los no [GitHub](https://github.com/Azure-Samples/cosmos-dotnet-getting-started). 

Para criar a solução de Introdução, você precisará do seguinte:

* Uma conta ativa do Azure. Se não tiver uma, você poderá se inscrever em uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma [conta do Azure Cosmos DB][cosmos-db-create-account].
* A solução [GetStarted](https://github.com/Azure-Samples/cosmos-dotnet-getting-started) disponível no GitHub.

Para restaurar as referências do SDK do .NET do Azure Cosmos DB no Visual Studio, clique com o botão direito do mouse na solução **GetStarted** no Gerenciador de Soluções e clique em **Restaurar Pacotes do NuGet**. Em seguida, no arquivo App.config, atualize os valores EndPointUri e PrimaryKey conforme descrito em [Conectar-se a uma conta do Azure Cosmos DB](#Connect).

Pronto, compile-o e você pode continuar!

## <a name="next-steps"></a>Próximas etapas
* Quer um tutorial mais complexo do ASP.NET MVC? Confira o [Tutorial do ASP.NET MVC: Desenvolvimento de aplicativo Web com o Azure Cosmos DB](sql-api-dotnet-application-preview.md).
* Quer executar testes de desempenho e escala com o Azure Cosmos DB? Confira [Teste de desempenho e escala com o Azure Cosmos DB](performance-testing.md)
* Saiba como [monitorar solicitações, o uso e o armazenamento do Azure Cosmos DB](monitor-accounts.md).
* Executar consultas em nosso conjunto de dados de exemplo no [Query Playground](https://www.documentdb.com/sql/demo).
* Para saber mais sobre o Azure Cosmos DB, confira [Bem-vindo ao Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/introduction).

[cosmos-db-create-account]: create-sql-api-java.md#create-a-database-account
