---
title: Criar um aplicativo de console .NET para gerenciar dados nos recursos da API do SQL do Azure Cosmos DB
description: Saiba como criar um aplicativo de console .NET para gerenciar recursos da conta da API do SQL do Azure Cosmos DB.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 07/12/2019
ms.openlocfilehash: c738b2d44c5faca1ef95b2da8fd1f90a1b3af919
ms.sourcegitcommit: c71306fb197b433f7b7d23662d013eaae269dc9c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68371016"
---
# <a name="quickstart-build-a-net-console-app-to-manage-azure-cosmos-db-sql-api-resources"></a>Início Rápido: Criar um aplicativo de console .NET para gerenciar dados nos recursos da API do SQL do Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](create-sql-api-dotnet.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)
>  

Introdução à biblioteca de clientes da API do SQL do Azure Cosmos DB para .NET. Siga as etapas neste documento para instalar o pacote .NET, criar um aplicativo e experimentar o código de exemplo para operações CRUD básicas nos dados armazenados no Azure Cosmos DB. 

O Azure Cosmos DB é o serviço de banco de dados multimodelo distribuído globalmente da Microsoft. É possível usar o Azure Cosmos DB para criar e consultar rapidamente chaves/valores, documentos e bancos de dados de grafos. Use a biblioteca de clientes da API do SQL do Azure Cosmos DB para .NET para:

* Criar um banco de dados e um contêiner do Azure Cosmos
* Adicionar dados de exemplo ao contêiner
* Consultar os dados 
* Excluir o banco de dados

[Documentação de referência da API](/dotnet/api/microsoft.azure.cosmos?view=azure-dotnet) | [Código-fonte da biblioteca](https://github.com/Azure/azure-cosmos-dotnet-v3) | [Pacote (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Cosmos)

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure – [crie uma gratuitamente](https://azure.microsoft.com/free/) ou você pode [Experimentar o Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/) sem uma assinatura do Azure, sem ônus e sem compromisso. 
* [SDK do .NET Core 2.1 ou posterior](https://dotnet.microsoft.com/download/dotnet-core/2.1).
* [CLI do Azure](/cli/azure/install-azure-cli?view=azure-cli-latest)

## <a name="setting-up"></a>Configurando

Esta seção orienta a criação de uma conta do Azure Cosmos e a configuração de um projeto que usa a biblioteca de cliente da API do SQL do Azure Cosmos DB para .NET a fim de gerenciar recursos. O código de exemplo descrito neste artigo cria o banco de dados `FamilyDatabase` e os membros da família (cada membro da família é um item) dentro desse banco de dados. Cada membro da família tem propriedades como `Id, FamilyName, FirstName, LastName, Parents, Children, Address,`. A propriedade `LastName` é usada como a chave de partição para o contêiner. 

### <a id="create-account"></a>Criar uma conta do Azure Cosmos

O código a seguir criará uma conta do Azure Cosmos com consistência de sessão. A conta é replicada no `South Central US` e no `North Central US`. Selecione o botão **Experimentar** e cole o código para executá-lo no Azure Cloud Shell. 

```azurecli-interactive

# Set variables for the new SQL API account, database, and container
resourceGroupName='myResourceGroup'
location='southcentralus'
accountName='mysqlapicosmosdb' 
databaseName='FamilyDatabase'
containerName='FamilyContainer'

# Create a resource group
az group create \
    --name $resourceGroupName \
    --location $location

# Create a SQL API Cosmos DB account with session consistency and multi-master enabled
az cosmosdb create \
    --resource-group $resourceGroupName \
    --name $accountName \
    --kind GlobalDocumentDB \
    --locations regionName="South Central US" failoverPriority=0 --locations regionName="North Central US" failoverPriority=1 \
    --default-consistency-level "Session" \
    --enable-multiple-write-locations true

```

### <a id="create-dotnet-core-app"></a>Criar um novo aplicativo .NET

Crie um novo aplicativo .NET em seu IDE ou editor preferido. Em uma janela de console, execute o novo comando dotnet a seguir para criar um novo aplicativo com o nome `todo`.

```console
dotnet new console --langVersion 7.1 -n todo
```

Altere o diretório para a pasta do aplicativo recém-criado. É possível criar o aplicativo com:

```console
cd todo
dotnet build
```

A saída esperada da compilação deve ser algo como:

```console
  Restore completed in 100.37 ms for C:\Users\user1\Downloads\CosmosDB_Samples\todo\todo.csproj.
  todo -> C:\Users\user1\Downloads\CosmosDB_Samples\todo\bin\Debug\netcoreapp2.2\todo.dll
  todo -> C:\Users\user1\Downloads\CosmosDB_Samples\todo\bin\Debug\netcoreapp2.2\todo.Views.dll

Build succeeded.
    0 Warning(s)
    0 Error(s)

Time Elapsed 00:00:34.17
```

### <a id="install-package"></a>Instalar pacote Azure Cosmos DB

Ainda no diretório do aplicativo, instale a biblioteca de clientes do Azure Cosmos DB para .NET Core usando o comando para adicionar pacote do dotnet.

```console
dotnet add package Microsoft.Azure.Cosmos
```

### <a name="copy-your-azure-cosmos-account-credentials-from-the-azure-portal"></a>Copiar suas credenciais da conta do Azure Cosmos no portal do Azure

O aplicativo de exemplo precisa autenticar sua conta do Azure Cosmos. Para autenticar, você deve passar as credenciais da conta do Azure Cosmos para o aplicativo. Obtenha suas credenciais da conta do Azure Cosmos seguindo estas etapas:

1. Entre no [Portal do Azure](https://portal.azure.com/).

1. Navegue até a conta do Azure Cosmos. 

1. Abra o painel **Chaves** e copie o **URI** e a **CHAVE PRIMÁRIA** da sua conta. Você adicionará os valores do URI e das chaves em uma variável de ambiente da próxima etapa.

### <a name="set-the-environment-variables"></a>Definir as variáveis de ambiente

Depois de copiar o **URI** e a **CHAVE PRIMÁRIA** da sua conta, salve-os em uma nova variável de ambiente no computador local que executa o aplicativo. Para definir a variável de ambiente, abra uma janela de console e execute o comando a seguir. Certifique-se de substituir os valores `<Your_Azure_Cosmos_account_URI>` e `<Your_Azure_Cosmos_account_PRIMARY_KEY>`.

**Windows**

```console
setx EndpointUrl "<Your_Azure_Cosmos_account_URI>"
setx PrimaryKey "<Your_Azure_Cosmos_account_PRIMARY_KEY>"
```

**Linux**

```bash
export EndpointUrl "<Your_Azure_Cosmos_account_URI>"
export PrimaryKey "<Your_Azure_Cosmos_account_PRIMARY_KEY>"
```

**MacOS**

```bash
export EndpointUrl "<Your_Azure_Cosmos_account_URI>"
export PrimaryKey "<Your_Azure_Cosmos_account_PRIMARY_KEY>"
```

 ## <a id="object-model"></a>Modelo de objeto

Antes de começar a criar o aplicativo, vamos examinar a hierarquia de recursos no Azure Cosmos DB e o modelo de objeto usado para criar e acessar esses recursos. O Azure Cosmos DB cria recursos na seguinte ordem:

* Conta do Azure Cosmos 
* Bancos de dados 
* Contêineres 
* Itens

Para saber mais sobre a hierarquia de diferentes entidades, confira o artigo [Trabalhando com bancos de dados, contêineres e itens no Azure Cosmos DB](databases-containers-items.md). Use as seguintes classes .NET para interagir com esses recursos:

* [CosmosClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclient?view=azure-dotnet) – fornece a representação lógica do lado do cliente para o serviço do Azure Cosmos DB. Esse objeto do cliente é usado para configurar e executar solicitações no serviço.

* [CreateDatabaseIfNotExistsAsync](/dotnet/api/microsoft.azure.cosmos.cosmosclient.createdatabaseifnotexistsasync?view=azure-dotnet) – esse método cria (se não existir) ou obtém (se já existir) um recurso de banco de dados como uma operação assíncrona. 

* [CreateContainerIfNotExistsAsync](/dotnet/api/microsoft.azure.cosmos.database.createcontainerifnotexistsasync?view=azure-dotnet) – esse método cria (se não existir) ou obtém (se já existir) um contêiner como uma operação assíncrona. Você pode verificar o código de status da resposta para determinar se o contêiner foi criado recentemente (201) ou se um contêiner existente foi retornado (200). 
* [CreateItemAsync](/dotnet/api/microsoft.azure.cosmos.container.createitemasync?view=azure-dotnet) – esse método cria um item dentro do contêiner. 

* [GetItemQueryIterator](/dotnet/api/microsoft.azure.cosmos.container.GetItemQueryIterator?view=azure-dotnet
) – esse método cria uma consulta para itens em contêiner em um banco de dados do Azure Cosmos usando uma instrução SQL com valores parametrizados. 

* [DeleteAsync](/dotnet/api/microsoft.azure.cosmos.database.deleteasync?view=azure-dotnet) – exclui o banco de dados especificado de sua conta Azure Cosmos. O método `DeleteAsync` exclui somente o banco de dados. O descarte da instância `Cosmosclient` deve ocorrer separadamente (o que é feito no método DeleteDatabaseAndCleanupAsync). 

 ## <a id="code-examples"></a>Exemplos de código

O código de exemplo descrito neste artigo cria um banco de dados de família em Azure Cosmos DB. O banco de dados da família contém detalhes da família, como nome, endereço, local, pais associados, filhos e animais de estimação. Antes de preencher os dados de sua conta do Azure Cosmos, defina as propriedades de um item da família. Crie uma nova classe chamada `Family.cs` no nível raiz do seu aplicativo de exemplo e adicione o seguinte código a ela:

```csharp
using Newtonsoft.Json;

namespace todo
{
    public class Family
    {
        [JsonProperty(PropertyName = "id")]
        public string Id { get; set; }
        public string LastName { get; set; }
        public Parent[] Parents { get; set; }
        public Child[] Children { get; set; }
        public Address Address { get; set; }
        public bool IsRegistered { get; set; }
        public override string ToString()
        {
            return JsonConvert.SerializeObject(this);
        }
    }

    public class Parent
    {
        public string FamilyName { get; set; }
        public string FirstName { get; set; }
    }

    public class Child
    {
        public string FamilyName { get; set; }
        public string FirstName { get; set; }
        public string Gender { get; set; }
        public int Grade { get; set; }
        public Pet[] Pets { get; set; }
    }

    public class Pet
    {
        public string GivenName { get; set; }
    }

    public class Address
    {
        public string State { get; set; }
        public string County { get; set; }
        public string City { get; set; }
    }
}
```

### <a name="add-the-using-directives--define-the-client-object"></a>Adicionar usando as diretivas e definir o objeto de cliente

No diretório do projeto, abra o arquivo `Program.cs` em seu editor e adicione o seguinte usando as diretivas na parte superior do seu aplicativo:

```csharp

using System;
using System.Threading.Tasks;
using System.Configuration;
using System.Collections.Generic;
using System.Net;
using Microsoft.Azure.Cosmos;
```

Para `program.cs file`, adicione o código para ler as variáveis de ambiente que você definiu na etapa anterior. Defina os objetos `CosmosClient`, `Database` e `Container`. Em seguida, adicione o código ao método principal que chama o método `GetStartedDemoAsync` com o qual você gerencia os recursos da conta do Azure Cosmos. 

```csharp
namespace todo
{
public class Program
{

    /// The Azure Cosmos DB endpoint for running this GetStarted sample.
    private string EndpointUrl = Environment.GetEnvironmentVariable("EndpointUrl");

    /// The primary key for the Azure DocumentDB account.
    private string PrimaryKey = Environment.GetEnvironmentVariable("PrimaryKey");

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
           Console.WriteLine("Beginning operations...\n");
           Program p = new Program();
           await p.GetStartedDemoAsync();

        }
        catch (CosmosException de)
        {
           Exception baseException = de.GetBaseException();
           Console.WriteLine("{0} error occurred: {1}", de.StatusCode, de);
        }
        catch (Exception e)
        {
            Console.WriteLine("Error: {0}", e);
        }
        finally
        {
            Console.WriteLine("End of demo, press any key to exit.");
            Console.ReadKey();
        }
    }
}
}
```

### <a name="create-a-database"></a>Criar um banco de dados 

Defina o método `CreateDatabaseAsync` dentro da classe `program.cs`. Esse método cria o `FamilyDatabase`, caso ainda não exista. 

```csharp
private async Task CreateDatabaseAsync()
{
    // Create a new database
    this.database = await this.cosmosClient.CreateDatabaseIfNotExistsAsync(databaseId);
    Console.WriteLine("Created Database: {0}\n", this.database.Id);
}
```

### <a name="create-a-container"></a>Criar um contêiner

Defina o método `CreateContainerAsync` dentro da classe `program.cs`. Esse método cria o `FamilyContainer`, caso ainda não exista. 

```csharp
/// Create the container if it does not exist. 
/// Specifiy "/LastName" as the partition key since we're storing family information, to ensure good distribution of requests and storage.
private async Task CreateContainerAsync()
{
    // Create a new container
    this.container = await this.database.CreateContainerIfNotExistsAsync(containerId, "/LastName");
    Console.WriteLine("Created Container: {0}\n", this.container.Id);
}
```

### <a name="create-an-item"></a>Criar um item

Crie um item de família adicionando o método `AddItemsToContainerAsync` com o seguinte código:

```csharp
private async Task AddItemsToContainerAsync()
{
    // Create a family object for the Andersen family
    Family andersenFamily = new Family
    {
        Id = "Andersen.1",
        LastName = "Andersen",
        Parents = new Parent[]
        {
           new Parent { FirstName = "Thomas" },
           new Parent { FirstName = "Mary Kay" }
        },
        Children = new Child[]
        {
           new Child
            {
                FirstName = "Henriette Thaulow",
                Gender = "female",
                Grade = 5,
                Pets = new Pet[]
                {
                    new Pet { GivenName = "Fluffy" }
                }
            }
        },
        Address = new Address { State = "WA", County = "King", City = "Seattle" },
        IsRegistered = false
 };

try
{
    // Read the item to see if it exists. ReadItemAsync will throw an exception if the item does not exist and return status code 404 (Not found).
    ItemResponse<Family> andersenFamilyResponse = await this.container.ReadItemAsync<Family>(andersenFamily.Id, new PartitionKey(andersenFamily.LastName));
    Console.WriteLine("Item in database with id: {0} already exists\n", andersenFamilyResponse.Resource.Id);
}
catch(CosmosException ex) when (ex.StatusCode == HttpStatusCode.NotFound)
{
    // Create an item in the container representing the Andersen family. Note we provide the value of the partition key for this item, which is "Andersen"
    ItemResponse<Family> andersenFamilyResponse = await this.container.CreateItemAsync<Family>(andersenFamily, new PartitionKey(andersenFamily.LastName));

    // Note that after creating the item, we can access the body of the item with the Resource property off the ItemResponse. We can also access the RequestCharge property to see the amount of RUs consumed on this request.
    Console.WriteLine("Created item in database with id: {0} Operation consumed {1} RUs.\n", andersenFamilyResponse.Resource.Id, andersenFamilyResponse.RequestCharge);
}

```

### <a name="query-the-items"></a>Consultar os itens

Depois de inserir um item, você pode executar uma consulta para obter os detalhes da família "Martins". O código a seguir mostra como executar a consulta usando diretamente a consulta SQL. A consulta SQL para obter os detalhes da família "Martins" é: `SELECT * FROM c WHERE c.LastName = 'Andersen'`. Defina o método `QueryItemsAsync` dentro da classe `program.cs` e adicione o seguinte código:


```csharp
private async Task QueryItemsAsync()
{
    var sqlQueryText = "SELECT * FROM c WHERE c.LastName = 'Andersen'";

    Console.WriteLine("Running query: {0}\n", sqlQueryText);

    QueryDefinition queryDefinition = new QueryDefinition(sqlQueryText);
    FeedIterator<Family> queryResultSetIterator = this.container.GetItemQueryIterator<Family>(queryDefinition);

    List<Family> families = new List<Family>();

    while (queryResultSetIterator.HasMoreResults)
    {
        FeedResponse<Family> currentResultSet = await queryResultSetIterator.ReadNextAsync();
        foreach (Family family in currentResultSet)
        {
            families.Add(family);
            Console.WriteLine("\tRead {0}\n", family);
        }
    }
}

```

### <a name="delete-the-database"></a>Excluir o banco de dados 

Por fim, você pode excluir o banco de dados adicionando o método `DeleteDatabaseAndCleanupAsync` com o seguinte código:

```csharp
private async Task DeleteDatabaseAndCleanupAsync()
{
   DatabaseResponse databaseResourceResponse = await this.database.DeleteAsync();
   // Also valid: await this.cosmosClient.Databases["FamilyDatabase"].DeleteAsync();

   Console.WriteLine("Deleted Database: {0}\n", this.databaseId);

   //Dispose of CosmosClient
    this.cosmosClient.Dispose();
}
```

### <a name="execute-the-crud-operations"></a>Executar as operações CRUD

Depois de definir todos os métodos necessários, execute-os com o método `GetStartedDemoAsync`. O método `DeleteDatabaseAndCleanupAsync` foi retirado do comentário deste código porque você não verá nenhum recurso se esse método for executado. Você pode remover os comentários após validar que seus recursos do Azure Cosmos DB foram criados no portal do Azure. 

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
    await this.DeleteFamilyItemAsync();
    //await this.DeleteDatabaseAndCleanupAsync();
}
```

Depois de adicionar todos os métodos necessários, salve o arquivo `Program.cs`. 

## <a name="run-the-code"></a>Executar o código

Em seguida, compile e execute o aplicativo para criar os recursos do Azure Cosmos DB. Abra uma nova janela de prompt de comando, não utilize a mesma instância usada para definir as variáveis de ambiente, porque as variáveis de ambiente não estão definidas na janela aberta no momento. Será necessário abrir um novo prompt de comando para ver as atualizações. 

```console
dotnet build
```

```console
dotnet run
```

A saída a seguir é gerada quando você executa o aplicativo. Você também pode entrar no portal do Azure e validar que os recursos foram criados:

```console
Created Database: FamilyDatabase

Created Container: FamilyContainer

Created item in database with id: Andersen.1 Operation consumed 11.62 RUs.

Running query: SELECT * FROM c WHERE c.LastName = 'Andersen'

        Read {"id":"Andersen.1","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":false}

End of demo, press any key to exit.
```

Você pode validar que os dados foram criados entrando no portal do Azure e olhando os itens necessários em sua conta do Azure Cosmos. 

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessária, você poderá usar o CLI do Azure ou o Azure PowerShell para remover a conta do Azure Cosmos e o grupo de recursos correspondente. O comando a seguir mostra como excluir o grupo de recursos usando o CLI do Azure:

```azurecli
az group delete -g "myResourceGroup" -l "southcentralus"
```

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu a criar uma conta do Azure Cosmos, como criar um banco de dados e um contêiner usando o .NET Core. Agora você pode importar dados adicionais para sua conta do Azure Cosmos com as instruções no artigo a seguir. 

> [!div class="nextstepaction"]
> [Importar dados no Azure Cosmos DB](import-data.md)
