---
title: Introdução à API de Tabela do Azure Cosmos DB usando SDK do .NET Standard
description: Armazene dados estruturados na nuvem usando a API de Tabela do Azure Cosmos DB.
author: wmengmsft
ms.author: wmeng
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: sample
ms.date: 05/20/2019
ms.openlocfilehash: 75f1554f7522723d71666633a03761d07e797e33
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443515"
---
# <a name="get-started-with-azure-cosmos-db-table-api-and-azure-table-storage-using-the-net-sdk"></a>Introdução à API de Tabela do Azure Cosmos DB e armazenamento de Tabelas do Azure usando SDK do .NET

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]

[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

É possível usar a API de Tabela do Azure Cosmos DB ou o armazenamento de Tabelas do Azure para armazenar dados NoSQL estruturados na nuvem, fornecendo um repositório de chave/atributo com um esquema sem design. Como a API de Tabela do Azure Cosmos DB e o armazenamento de Tabelas são sem esquema, é fácil adaptar seus dados à medida que as necessidades do aplicativo evoluem. É possível usar a API de Tabela do Azure Cosmos DB e o armazenamento de Tabelas para armazenar conjuntos de dados flexíveis como dados de usuário para aplicativos Web, catálogos de endereços, informações de dispositivo ou outros tipos de metadados que o serviço exigir. 

Este tutorial descreve um exemplo que mostra como usar a [Biblioteca de Tabelas do Microsoft Azure Cosmos DB para .NET](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) com cenários de API de Tabela do Azure Cosmos DB e armazenamento de Tabelas do Azure. Você deverá usar a conexão específica para o serviço do Azure. Esses cenários são explorados usando exemplos em C# que ilustram como criar tabelas, inserir/atualizar dados, consultar dados e excluir tabelas.

## <a name="prerequisites"></a>Pré-requisitos

Você precisará do seguinte para concluir este exemplo com sucesso:

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)

* [Biblioteca de Tabelas do Microsoft Azure Cosmos DB para .NET](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) - Essa biblioteca atualmente está disponível para estrutura .NET e .NET Standard. 

* [Conta de API de Tabela do Azure Cosmos DB](create-table-dotnet.md#create-a-database-account).

## <a name="create-an-azure-cosmos-db-table-api-account"></a>Criar uma conta de API de Tabele do Azure Cosmos DB

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

## <a name="create-a-net-console-project"></a>Criar um projeto de console do .NET

No Visual Studio, crie um novo aplicativo de console do .NET. As etapas a seguir mostram como criar um aplicativo de console no Visual Studio 2019. É possível usar a Biblioteca de Tabelas do Azure Cosmos DB em qualquer tipo de aplicativo .NET, incluindo um serviço de nuvem do Azure ou um aplicativo Web, além de aplicativos para área de trabalho e dispositivos móveis. Neste guia, usamos um aplicativo de console para simplificar.

1. Selecione **Arquivo** > **Novo** > **Projeto**.

1. Escolha **Aplicativo de Console (.NET Core)** e, em seguida, selecione **Avançar**.

1. No campo **Nome do projeto**, insira um nome para seu aplicativo, por exemplo, **CosmosTableSamples**. (Você pode fornecer um nome diferente conforme necessário.)

1. Selecione **Criar**.

Todos os exemplos de código neste exemplo podem ser adicionados ao método Main() do arquivo **Program.cs** do aplicativo do console.

## <a name="install-the-required-nuget-package"></a>Instalar o pacote NuGet necessário

Para obter o pacote NuGet, siga estas etapas:

1. Clique com o botão direito do mouse no seu projeto no **Gerenciador de Soluções** e escolha **Gerenciar Pacotes NuGet**.

1. Pesquise online por `Microsoft.Azure.Cosmos.Table`, `Microsoft.Extensions.Configuration`, `Microsoft.Extensions.Configuration.Json`, `Microsoft.Extensions.Configuration.Binder` e selecione **Instalar** para instalar a Biblioteca de Tabelas do Microsoft Azure Cosmos DB.

## <a name="configure-your-storage-connection-string"></a>Configurar a cadeia de conexão de armazenamento

1. Do [portal do Azure](https://portal.azure.com/), navegue até sua conta do Azure Cosmos ou conta do Armazenamento de Tabelas. 

1. Abra o painel **Cadeia de Conexão** ou **Chaves de acesso** painel. Use o botão de cópia no lado direito da tela para copiar a **CADEIA DE CONEXÃO PRIMÁRIA**.

   ![Exiba e copie a CADEIA DE CONEXÃO PRIMÁRIA no painel Cadeia de Conexão](./media/create-table-dotnet/connection-string.png)
   
1. Para configurar a cadeia de conexão, no Visual Studio clique com o botão direito do mouse no projeto **CosmosTableSamples**.

1. Selecione **Adicionar** e, em seguida, **Novo Item**. Crie um novo arquivo **Settings.json** com o tipo de arquivo como **Arquivo de Configuração JSON do TypeScript**. 

1. Substitua o código no arquivo Settings.json pelo seguinte código e atribua a cadeia de conexão primária:

   ```csharp
   {
   "StorageConnectionString": <Primary connection string of your Azure Cosmos DB account>
   }
   ```

1. Clique com o botão direito do mouse no projeto **CosmosTableSamples**. Selecione **Adicionar**, **Novo Item** e adicione uma classe nomeada **AppSettings.cs**.

1. Adicione o seguinte código ao arquivo AppSettings.cs. Esse arquivo lê a cadeia de conexão do arquivo Settings.json e a atribui ao parâmetro de configuração:

   ```csharp
   namespace CosmosTableSamples
   {
    using Microsoft.Extensions.Configuration;
    public class AppSettings
    {
        public string StorageConnectionString { get; set; }
        public static AppSettings LoadAppSettings()
        {
            IConfigurationRoot configRoot = new ConfigurationBuilder()
                .AddJsonFile("Settings.json")
                .Build();
            AppSettings appSettings = configRoot.Get<AppSettings>();
            return appSettings;
        }
    }
   }
   ```

## <a name="parse-and-validate-the-connection-details"></a>Analisar e validar os detalhes da conexão 

1. Clique com o botão direito do mouse no projeto **CosmosTableSamples**. Selecione **Adicionar**, **Novo Item** e adicione uma classe nomeada **Common.cs**. Você gravará código para validar os detalhes da conexão e criar uma tabela dentro dessa classe.

1. Defina um método `CreateStorageAccountFromConnectionString`, conforme mostrado abaixo. Esse método analisará os detalhes da cadeia de conexão e validará se os detalhes da chave de conta e do nome da conta fornecidos no arquivo "Settings.json" são válidos. 

 ```csharp
using System;

namespace CosmosTableSamples
{
    using System.Threading.Tasks;
    using Microsoft.Azure.Cosmos.Table;
    using Microsoft.Azure.Documents;

    public class Common
    {
        public static CloudStorageAccount CreateStorageAccountFromConnectionString(string storageConnectionString)
        {
            CloudStorageAccount storageAccount;
            try
            {
                storageAccount = CloudStorageAccount.Parse(storageConnectionString);
            }
            catch (FormatException)
            {
                Console.WriteLine("Invalid storage account information provided. Please confirm the AccountName and AccountKey are valid in the app.config file - then restart the application.");
                throw;
            }
            catch (ArgumentException)
            {
                Console.WriteLine("Invalid storage account information provided. Please confirm the AccountName and AccountKey are valid in the app.config file - then restart the sample.");
                Console.ReadLine();
                throw;
            }

            return storageAccount;
        }
    }
}
   ```


## <a name="create-a-table"></a>Criar uma tabela 

A classe [CloudTableClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.cloudtableclient) permite que você recupere as tabelas e entidades armazenadas no Armazenamento de tabelas. Como não temos tabelas na conta de API de Tabela do Cosmos DB, adicionaremos o método `CreateTableAsync` para a classe **Common.cs** para criar uma tabela:

```csharp
public static async Task<CloudTable> CreateTableAsync(string tableName)
  {
    string storageConnectionString = AppSettings.LoadAppSettings().StorageConnectionString;

    // Retrieve storage account information from connection string.
    CloudStorageAccount storageAccount = CreateStorageAccountFromConnectionString(storageConnectionString);

    // Create a table client for interacting with the table service
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient(new TableClientConfiguration());

    Console.WriteLine("Create a Table for the demo");

    // Create a table client for interacting with the table service 
    CloudTable table = tableClient.GetTableReference(tableName);
    if (await table.CreateIfNotExistsAsync())
    {
      Console.WriteLine("Created Table named: {0}", tableName);
    }
    else
    {
      Console.WriteLine("Table {0} already exists", tableName);
    }

    Console.WriteLine();
    return table;
}
```

## <a name="define-the-entity"></a>Definir a entidade 

As entidades são mapeadas para objetos C# usando uma classe personalizada derivada de [TableEntity](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableentity). Para adicionar uma entidade a uma tabela, crie uma classe que defina as propriedades da sua entidade.

Clique com o botão direito do mouse no projeto **CosmosTableSamples**. Selecione **Adicionar**, **Nova Pasta** e nomeie-a como **Modelo**. Na pasta Modelo, adicione uma classe nomeada **CustomerEntity.cs** e adicione o código a seguir.

```csharp
namespace CosmosTableSamples.Model
{
    using Microsoft.Azure.Cosmos.Table;
    public class CustomerEntity : TableEntity
    {
        public CustomerEntity()
        {
        }

        public CustomerEntity(string lastName, string firstName)
        {
            PartitionKey = lastName;
            RowKey = firstName;
        }

        public string Email { get; set; }
        public string PhoneNumber { get; set; }
    }
}
```

Esse código define uma classe de entidade que usa o nome do cliente como a chave de linha e o sobrenome como a chave de partição. Juntas, uma chave de partição e uma chave de linha identificam exclusivamente a entidade na tabela. As entidades com a mesma chave de partição podem ser consultadas mais rápido do que aquelas com chaves de partição diferentes, mas usar chaves de partição diferentes permite uma escalabilidade maior de operações paralelas. As entidades que serão armazenadas em tabelas deverão ser de um tipo com suporte, por exemplo, derivado da classe [TableEntity](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableentity). As propriedades de entidade que você gostaria de armazenar em uma tabela precisam ser propriedades públicas do tipo e dar suporte à obtenção e à definição dos valores. Além disso, o tipo de entidade deverá expor um construtor sem parâmetros.

## <a name="insert-or-merge-an-entity"></a>Inserir ou mesclar uma entidade

O exemplo de código a seguir cria um objeto de entidade e o adiciona à tabela. O método InsertOrMerge dentro da classe [TableOperation](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableoperation) é usado para inserir ou mesclar uma entidade. O método [CloudTable.ExecuteAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.cloudtable.executeasync?view=azure-dotnet) é chamado para executar a operação. 

Clique com o botão direito do mouse no projeto **CosmosTableSamples**. Selecione **Adicionar**, **Novo Item** e adicione uma classe nomeada **SamplesUtils.cs**. Essa classe armazena todo o código necessário para executar operações CRUD nas entidades. 

```csharp
public static async Task<CustomerEntity> InsertOrMergeEntityAsync(CloudTable table, CustomerEntity entity)
    {
      if (entity == null)
    {
       throw new ArgumentNullException("entity");
    }
    try
    {
       // Create the InsertOrReplace table operation
       TableOperation insertOrMergeOperation = TableOperation.InsertOrMerge(entity);

       // Execute the operation.
       TableResult result = await table.ExecuteAsync(insertOrMergeOperation);
       CustomerEntity insertedCustomer = result.Result as CustomerEntity;
        
        // Get the request units consumed by the current operation. RequestCharge of a TableResult is only applied to Azure CosmoS DB 
        if (result.RequestCharge.HasValue)
          {
            Console.WriteLine("Request Charge of InsertOrMerge Operation: " + result.RequestCharge);
          }

        return insertedCustomer;
        }
        catch (StorageException e)
        {
          Console.WriteLine(e.Message);
          Console.ReadLine();
          throw;
        }
    }
```

### <a name="get-an-entity-from-a-partition"></a>Obter uma entidade de uma partição

É possível obter entidade de uma partição usando o método Recuperar na classe [TableOperation](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableoperation). O exemplo de código a seguir obtém a chave de linha da chave de partição, o email e o número de telefone de uma entidade do cliente. Este exemplo também imprime as unidades de solicitação consumidas para consultar a entidade. Para consultar uma entidade, acrescente o seguinte código ao arquivo **SamplesUtils.cs**: 

```csharp
public static async Task<CustomerEntity> RetrieveEntityUsingPointQueryAsync(CloudTable table, string partitionKey, string rowKey)
    {
      try
      {
        TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>(partitionKey, rowKey);
        TableResult result = await table.ExecuteAsync(retrieveOperation);
        CustomerEntity customer = result.Result as CustomerEntity;
        if (customer != null)
        {
          Console.WriteLine("\t{0}\t{1}\t{2}\t{3}", customer.PartitionKey, customer.RowKey, customer.Email, customer.PhoneNumber);
        }

        // Get the request units consumed by the current operation. RequestCharge of a TableResult is only applied to Azure CosmoS DB 
        if (result.RequestCharge.HasValue)
        {
           Console.WriteLine("Request Charge of Retrieve Operation: " + result.RequestCharge);
        }

        return customer;
        }
        catch (StorageException e)
        {
           Console.WriteLine(e.Message);
           Console.ReadLine();
           throw;
        }
    }
```

## <a name="delete-an-entity"></a>Excluir uma entidade

Você pode excluir facilmente uma entidade após a recuperação usando o mesmo padrão mostrado para a atualização de uma entidade. O código a seguir recupera e exclui uma entidade de cliente. Para excluir uma entidade, anexe o seguinte código ao arquivo **SamplesUtils.cs**: 

```csharp
public static async Task DeleteEntityAsync(CloudTable table, CustomerEntity deleteEntity)
   {
     try
     {
        if (deleteEntity == null)
     {
        throw new ArgumentNullException("deleteEntity");
     }

    TableOperation deleteOperation = TableOperation.Delete(deleteEntity);
    TableResult result = await table.ExecuteAsync(deleteOperation);

    // Get the request units consumed by the current operation. RequestCharge of a TableResult is only applied to Azure CosmoS DB 
    if (result.RequestCharge.HasValue)
    {
       Console.WriteLine("Request Charge of Delete Operation: " + result.RequestCharge);
    }

    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

## <a name="execute-the-crud-operations-on-sample-data"></a>Executar as operações CRUD em dados de exemplo

Após definir os métodos para criar tabela, inserir ou mesclar entidades, execute esses métodos nos dados de exemplo. Para fazer isso, clique com o botão direito do mouse no projeto **CosmosTableSamples**. Selecione **Adicionar**, **Novo Item** e adicione uma classe nomeada **BasicSamples.cs**, em seguida, adicione o seguinte código. Esse código cria uma tabela e adiciona entidades a ela. Se você quiser excluir a entidade e a tabela no término do projeto, remova os comentários dos métodos `table.DeleteIfExistsAsync()` e `SamplesUtils.DeleteEntityAsync(table, customer)` do seguinte código:

```csharp
using System;
namespace CosmosTableSamples
{
    using System.Threading.Tasks;
    using Microsoft.Azure.Cosmos.Table;
    using Model;

    class BasicSamples
    {
        public async Task RunSamples()
        {
            Console.WriteLine("Azure Cosmos DB Table - Basic Samples\n");
            Console.WriteLine();

            string tableName = "demo" + Guid.NewGuid().ToString().Substring(0, 5);

            // Create or reference an existing table
            CloudTable table = await Common.CreateTableAsync(tableName);

            try
            {
                // Demonstrate basic CRUD functionality 
                await BasicDataOperationsAsync(table);
            }
            finally
            {
                // Delete the table
                // await table.DeleteIfExistsAsync();
            }
        }

        private static async Task BasicDataOperationsAsync(CloudTable table)
        {
            // Create an instance of a customer entity. See the Model\CustomerEntity.cs for a description of the entity.
            CustomerEntity customer = new CustomerEntity("Harp", "Walter")
            {
                Email = "Walter@contoso.com",
                PhoneNumber = "425-555-0101"
            };

            // Demonstrate how to insert the entity
            Console.WriteLine("Insert an Entity.");
            customer = await SamplesUtils.InsertOrMergeEntityAsync(table, customer);

            // Demonstrate how to Update the entity by changing the phone number
            Console.WriteLine("Update an existing Entity using the InsertOrMerge Upsert Operation.");
            customer.PhoneNumber = "425-555-0105";
            await SamplesUtils.InsertOrMergeEntityAsync(table, customer);
            Console.WriteLine();

            // Demonstrate how to Read the updated entity using a point query 
            Console.WriteLine("Reading the updated Entity.");
            customer = await SamplesUtils.RetrieveEntityUsingPointQueryAsync(table, "Harp", "Walter");
            Console.WriteLine();

            // Demonstrate how to Delete an entity
            //Console.WriteLine("Delete the entity. ");
            //await SamplesUtils.DeleteEntityAsync(table, customer);
            //Console.WriteLine();
        }
    }
}
```

O código anterior cria uma tabela que começa com “demo” e o GUID gerado é anexado ao nome da tabela. Em seguida, adiciona uma entidade de cliente com nome e sobrenome como "Valter Lima" e, posteriormente atualiza o número de telefone desse usuário. 

Neste tutorial, você compilou um código para executar operações CRUD básicas nos dados armazenados na conta de API de Tabela. Também é possível executar operações avançadas como – inserir dados em lotes, consultar todos os dados em uma partição, consultar um intervalo de dados em uma partição, listar tabelas na conta cujos nomes iniciam com o prefixo especificado. Você pode baixar o formulário de exemplo completo [azure-cosmos-table-dotnet-core-getting-started](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started) do repositório GitHub. A classe [AdvancedSamples.cs](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started/blob/master/CosmosTableSamples/AdvancedSamples.cs) tem mais operações que podem ser executadas nos dados.  

## <a name="run-the-project"></a>Executar o projeto

No projeto **CosmosTableSamples**. Abra a classe chamada **Program.cs** e adicione o código a seguir a ela para chamar BasicSamples quando o projeto for executado.

```csharp
using System;

namespace CosmosTableSamples
{
    class Program
    {
        public static void Main(string[] args)
        {
            Console.WriteLine("Azure Cosmos Table Samples");
            BasicSamples basicSamples = new BasicSamples();
            basicSamples.RunSamples().Wait();
           
            Console.WriteLine();
            Console.WriteLine("Press any key to exit");
            Console.Read();
        }
    }
}
```

Agora, compile a solução e pressione F5 para executar o projeto. Quando o projeto for executado, a seguinte saída no prompt de comando será exibida:

![Saída do prompt de comando](./media/tutorial-develop-table-standard/output-from-sample.png)

Se você receber um erro informando que o arquivo Settings.json não pode ser encontrado ao executar o projeto, será possível resolver isso adicionando a seguinte entrada XML às configurações do projeto. Clique com o botão direito em CosmosTableSamples, selecione Editar CosmosTableSamples.csproj e adicione o seguinte itemGroup: 

```csharp
  <ItemGroup>
    <None Update="Settings.json">
      <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
    </None>
  </ItemGroup>
```
Agora, você pode entrar no portal do Azure e verificar se os dados existem na tabela. 

![Resultados no portal](./media/tutorial-develop-table-standard/results-in-portal.png)

## <a name="next-steps"></a>Próximas etapas

Você pode avançar para o próximo tutorial e saber como migrar dados para conta de API de Tabela do Azure Cosmos DB. 

> [!div class="nextstepaction"]
>[Como consultar dados](../cosmos-db/table-import.md)
