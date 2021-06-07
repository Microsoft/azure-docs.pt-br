---
title: Migre seu aplicativo do Amazon DynamoDB para o Azure Cosmos DB
description: Saiba como migrar seu aplicativo .NET do DynamoDB da Amazon para o Azure Cosmos DB
author: manishmsfte
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 04/29/2020
ms.author: mansha
ms.openlocfilehash: 9b4b5fca8017a906fa44b02edcf5f0bdcf6166b3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93334218"
---
# <a name="migrate-your-application-from-amazon-dynamodb-to-azure-cosmos-db"></a>Migre seu aplicativo do Amazon DynamoDB para o Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

O Azure Cosmos DB é um banco de dados escalonável, distribuído globalmente e totalmente gerenciado. Ele fornece acesso garantido de baixa latência aos seus dados. Para saber mais sobre a API do Azure Cosmos DB, veja o artigo de [visão geral](introduction.md). Este artigo descreve como migrar seu aplicativo .NET do DynamoDB para o Azure Cosmos DB com alterações mínimas de código.

## <a name="conceptual-differences"></a>Diferenças conceituais

Estas são as principais diferenças conceituais entre o Azure Cosmos DB e o DynamoDB:

|  DynamoDB | Azure Cosmos DB  |
|---|---|
|Não aplicável|  Banco de dados |
|Tabela      |  Coleção |
|  Item |  Document |
|Atributo|Campo|
|Índice secundário|Índice secundário|
|Chave primária – chave de partição|Chave de partição|
|Chave primária – chave de classificação| Não obrigatório |
|STREAM|ChangeFeed|
|Unidade de computação de gravação|Unidade de solicitação (flexível, pode ser usada para leituras ou gravações)|
|Unidade de computação de leitura    |Unidade de solicitação (flexível, pode ser usada para leituras ou gravações)|
|Tabelas globais| Não obrigatório. Você pode selecionar a região diretamente ao provisionar a conta do Azure Cosmos (você pode alterar a região mais tarde)|

## <a name="structural-differences"></a>Diferenças estruturais

O Azure Cosmos DB tem uma estrutura JSON mais simples em comparação à do DynamoDB. O exemplo a seguir mostra as diferenças

**DynamoDB**:

O objeto JSON a seguir representa o formato de dados no DynamoDB

```json
{
TableName: "Music",
KeySchema: [
{ 
  AttributeName: "Artist",
  KeyType: "HASH", //Partition key
},
{ 
  AttributeName: "SongTitle",
  KeyType: "RANGE" //Sort key
}
],
AttributeDefinitions: [
{ 
  AttributeName: "Artist",
  AttributeType: "S"
},
{ 
  AttributeName: "SongTitle",
  AttributeType: "S"
}
],
ProvisionedThroughput: {
  ReadCapacityUnits: 1,
  WriteCapacityUnits: 1
 }
}
 ```

**Azure Cosmos DB**:

O objeto JSON a seguir representa o formato de dados no Azure Cosmos DB

```json
{
"Artist": "",
"SongTitle": "",
"AlbumTitle": "",
"Year": 9999,
"Price": 0.0,
"Genre": "",
"Tags": ""
}
```

## <a name="migrate-your-data"></a>Migrar seus dados

Há várias opções disponíveis para migrar seus dados para o Azure Cosmos DB. Para saber mais, confira o artigo [Opções para migrar seus dados locais ou na nuvem para o Azure Cosmos DB](cosmosdb-migrationchoices.md).

## <a name="migrate-your-code"></a>Migrar seu código

Este artigo tem como escopo a migração do código de um aplicativo para o Azure Cosmos DB, que é o aspecto crítico da migração de banco de dados. Para ajudá-lo a reduzir a curva de aprendizado, as seções a seguir incluem uma comparação de código lado a lado entre o Amazon DynamoDB e o snippet de código equivalente do Azure Cosmos DB.

Para baixar o código-fonte, clone o seguinte repositório:

```bash
git clone https://github.com/Azure-Samples/DynamoDB-to-CosmosDB
```

### <a name="pre-requisites"></a>Pré-requisitos

- .NET Framework 4.7.2
- Visual Studio 2019
- Acesso a uma conta de API do SQL do Azure Cosmos DB
- Instalação local do Amazon DynamoDB
- Java 8
- Execute a versão para download do Amazon DynamoDB na porta 8000 (você pode alterar e configurar o código)

### <a name="set-up-your-code"></a>Configurar seu código

Adicione o seguinte "pacote NuGet" ao seu projeto:

```bash
Install-Package Microsoft.Azure.Cosmos 
```

### <a name="establish-connection"></a>estabelecer conexão

**DynamoDB**:

No Amazon DynamoDB, o seguinte código é usado para se conectar:

```csharp
    AmazonDynamoDBConfig addbConfig = new AmazonDynamoDBConfig() ;
        addbConfig.ServiceURL = "endpoint";
        try { aws_dynamodbclient = new AmazonDynamoDBClient( addbConfig ); }
```

**Azure Cosmos DB**:

Para conectar o Azure Cosmos DB, atualize seu código para:

```csharp
client_documentDB = new CosmosClient("your connectionstring from the Azure portal");
```

**Otimizar a conexão no Azure Cosmos DB**

Com o Azure Cosmos DB, você pode usar as seguintes opções para otimizar sua conexão:

* **ConnectionMode** – use o modo de conexão direta para se conectar aos nós de dados no serviço do Azure Cosmos DB. Use o modo somente gateway para inicializar e armazenar em cache os endereços lógicos e atualizações nas atualizações. Confira o artigo de [modos de conectividade](sql-sdk-connection-modes.md) para obter mais detalhes.

* **ApplicationRegion** – essa opção é usada para definir a região de replicação geográfica preferida que é usada para interagir com o Azure Cosmos DB. Para saber mais, confira o artigo de [distribuição global](distribute-data-globally.md).

* **ConsistencyLevel** – essa opção é usada para substituir o nível de consistência padrão. Para saber mais, confira o artigo [Níveis de consistência](consistency-levels.md).

* **BulkExecutionMode** – essa opção é usada para executar operações em massa definindo a propriedade *AllowBulkExecution* como true. Para saber mais, confira o artigo [Importação em massa](tutorial-sql-api-dotnet-bulk-import.md).

   ```csharp
   client_cosmosDB = new CosmosClient(" Your connection string ",new CosmosClientOptions()
   { 
    ConnectionMode=ConnectionMode.Direct,
    ApplicationRegion=Regions.EastUS2,
    ConsistencyLevel=ConsistencyLevel.Session,
    AllowBulkExecution=true  
   });
   ```

### <a name="provision-the-container"></a>Provisionar o contêiner

**DynamoDB**:

Para armazenar os dados no Amazon DynamoDB, você precisa criar a tabela primeiro. Nesse processo, você define o esquema, o tipo de chave e os atributos, conforme mostrado no seguinte código:

```csharp
// movies_key_schema
public static List<KeySchemaElement> movies_key_schema
  = new List<KeySchemaElement>
{
  new KeySchemaElement
  {
    AttributeName = partition_key_name,
    KeyType = "HASH"
  },
  new KeySchemaElement
  {
    AttributeName = sort_key_name,
    KeyType = "RANGE"
  }
};

// key names for the Movies table
public const string partition_key_name = "year";
public const string sort_key_name      = "title";
  public const int readUnits=1, writeUnits=1; 

    // movie_items_attributes
    public static List<AttributeDefinition> movie_items_attributes
  = new List<AttributeDefinition>
{
  new AttributeDefinition
  {
    AttributeName = partition_key_name,
    AttributeType = "N"
  },
  new AttributeDefinition
  {
    AttributeName = sort_key_name,
    AttributeType = "S"
  }

CreateTableRequest  request;
CreateTableResponse response;

// Build the 'CreateTableRequest' structure for the new table
request = new CreateTableRequest
{
  TableName             = table_name,
  AttributeDefinitions  = table_attributes,
  KeySchema             = table_key_schema,
  // Provisioned-throughput settings are always required,
  // although the local test version of DynamoDB ignores them.
  ProvisionedThroughput = new ProvisionedThroughput( readUnits, writeUnits );
};
```

**Azure Cosmos DB**:

No Amazon DynamoDB, você precisa provisionar as unidades de computação de leitura e gravar unidades de computação. No Azure Cosmos DB você especifica a taxa de transferência como [RU/s (Unidades de Solicitação)](request-units.md), que pode ser usada para qualquer operação dinamicamente. Os dados são organizados como banco de dados --> contêiner --> item. Você pode especificar a taxa de transferência no nível do banco de dados ou no nível de coleção ou em ambos.

Para criar um banco de dados:

```csharp
await client_cosmosDB.CreateDatabaseIfNotExistsAsync(movies_table_name);
```

Para criar o contêiner:

```csharp
await cosmosDatabase.CreateContainerIfNotExistsAsync(new ContainerProperties() { PartitionKeyPath = "/" + partitionKey, Id = new_collection_name }, provisionedThroughput);
```

### <a name="load-the-data"></a>Carregar os dados

**DynamoDB**:

O código a seguir mostra como carregar os dados no Amazon DynamoDB. O moviesArray consiste em uma lista de documentos JSON, então, você precisa iterar e carregar o documento JSON para o Amazon DynamoDB:

```csharp
int n = moviesArray.Count;
for( int i = 0, j = 99; i < n; i++ )
    {
  try
  {
    string itemJson = moviesArray[i].ToString();
    Document doc = Document.FromJson(itemJson);
    Task putItem = moviesTable.PutItemAsync(doc);
    if( i >= j )
    {
      j++;
      Console.Write( "{0,5:#,##0}, ", j );
      if( j % 1000 == 0 )
        Console.Write( "\n " );
      j += 99;
    }
    await putItem;
```

**Azure Cosmos DB**:

No Azure Cosmos DB, você pode optar por transmitir e gravar com `moviesContainer.CreateItemStreamAsync()`. No entanto, neste exemplo, o JSON será desserializado no tipo *MovieModel* para demonstrar o recurso de conversão de tipo. O código é multi-threaded, que usará a arquitetura distribuída do Azure Cosmos DB e acelerará o carregamento:

```csharp
List<Task> concurrentTasks = new List<Task>();
for (int i = 0, j = 99; i < n; i++)
{
  try
  {
      MovieModel doc= JsonConvert.DeserializeObject<MovieModel>(moviesArray[i].ToString());
      doc.Id = Guid.NewGuid().ToString();
      concurrentTasks.Add(moviesContainer.CreateItemAsync(doc,new PartitionKey(doc.Year)));
      {
          j++;
          Console.Write("{0,5:#,##0}, ", j);
          if (j % 1000 == 0)
              Console.Write("\n               ");
          j += 99;
      }
      
  }
  catch (Exception ex)
  {
      Console.WriteLine("\n     ERROR: Could not write the movie record #{0:#,##0}, because:\n       {1}",
                          i, ex.Message);
      operationFailed = true;
      break;
  }
}
await Task.WhenAll(concurrentTasks);
```

### <a name="create-a-document"></a>Criar um documento

**DynamoDB**:

Escrever um novo documento no Amazon DynamoDB não tem segurança de tipos, o seguinte exemplo usa newItem como tipo de documento:

```csharp
Task<Document> writeNew = moviesTable.PutItemAsync(newItem, token);
await writeNew;
```

**Azure Cosmos DB**:

O Azure Cosmos DB fornece segurança de tipos por meio do modelo de dados. Usamos o modelo de dados chamado 'MovieModel':

```csharp
public class MovieModel
{
    [JsonProperty("id")]
    public string Id { get; set; }
    [JsonProperty("title")]
    public string Title{ get; set; }
    [JsonProperty("year")]
    public int Year { get; set; }
    public MovieModel(string title, int year)
    {
        this.Title = title;
        this.Year = year;
    }
    public MovieModel()
    {

    }
    [JsonProperty("info")]
    public   MovieInfo MovieInfo { get; set; }

    internal string PrintInfo()
    {
        if(this.MovieInfo!=null)
        return            string.Format("\nMovie with title:{1}\n Year: {2}, Actors: {3}\n Directors:{4}\n Rating:{5}\n", this.Id, this.Title, this.Year, String.Join(",",this.MovieInfo.Actors), this.MovieInfo, this.MovieInfo.Rating);
        else
            return string.Format("\nMovie with  title:{0}\n Year: {1}\n",  this.Title, this.Year);
    }
}
```

No Azure Cosmos DB, newItem será MovieModel:

```csharp
 MovieModel movieModel = new MovieModel()
            {
                Id = Guid.NewGuid().ToString(),
                Title = "The Big New Movie",
                Year = 2018,
                MovieInfo = new MovieInfo() { Plot = "Nothing happens at all.", Rating = 0 }
            };
    var writeNew= moviesContainer.CreateItemAsync(movieModel, new Microsoft.Azure.Cosmos.PartitionKey(movieModel.Year));
    await writeNew;
```

### <a name="read-a-document"></a>Ler um documento

**DynamoDB**:

Para ler o Amazon DynamoDB, você precisa definir primitivos:

```csharp
// Create Primitives for the HASH and RANGE portions of the primary key
Primitive hash = new Primitive(year.ToString(), true);
Primitive range = new Primitive(title, false);

  Task<Document> readMovie = moviesTable.GetItemAsync(hash, range, token);
  movie_record = await readMovie;
```

**Azure Cosmos DB**:

No entanto, com o Azure Cosmos DB, a consulta é natural (linq):

```csharp
IQueryable<MovieModel> movieQuery = moviesContainer.GetItemLinqQueryable<MovieModel>(true)
                        .Where(f => f.Year == year && f.Title == title);
// The query is executed synchronously here, but can also be executed asynchronously via the IDocumentQuery<T> interface
    foreach (MovieModel movie in movieQuery)
    {
      movie_record_cosmosdb = movie;
    }
```

A coleção de documentos no exemplo acima será:

- segurança de tipos
- fornece uma opção de consulta natural.

### <a name="update-an-item"></a>Atualizar um item

**DynamoDB**: Para atualizar o item no Amazon DynamoDB:

```csharp
updateResponse = await client.UpdateItemAsync( updateRequest );
````

**Azure Cosmos DB**:

No Azure Cosmos DB, a atualização será tratada como operação Upsert, o que significa inserir o documento, caso ele não exista:

```csharp
await moviesContainer.UpsertItemAsync<MovieModel>(updatedMovieModel);
```

### <a name="delete-a-document"></a>Excluir um documento

**DynamoDB**:

Para excluir um item no Amazon DynamoDB, você precisará se enquadrar nos primitivos:

```csharp
Primitive hash = new Primitive(year.ToString(), true);
      Primitive range = new Primitive(title, false);
      DeleteItemOperationConfig deleteConfig = new DeleteItemOperationConfig( );
      deleteConfig.ConditionalExpression = condition;
      deleteConfig.ReturnValues = ReturnValues.AllOldAttributes;
      
  Task<Document> delItem = table.DeleteItemAsync( hash, range, deleteConfig );
        deletedItem = await delItem;
```

**Azure Cosmos DB**:

No Azure Cosmos DB, podemos obter o documento e excluí-los de modo assíncrono:

```csharp
var result= ReadingMovieItem_async_List_CosmosDB("select * from c where c.info.rating>7 AND c.year=2018 AND c.title='The Big New Movie'");
while (result.HasMoreResults)
{
  var resultModel = await result.ReadNextAsync();
  foreach (var movie in resultModel.ToList<MovieModel>())
  {
    await moviesContainer.DeleteItemAsync<MovieModel>(movie.Id, new PartitionKey(movie.Year));
  }
  }
```

### <a name="query-documents"></a>Consultar documentos

**DynamoDB**:

No Amazon DynamoDB, as funções de API são necessárias para consultar os dados:

```csharp
QueryOperationConfig config = new QueryOperationConfig( );
  config.Filter = new QueryFilter( );
  config.Filter.AddCondition( "year", QueryOperator.Equal, new DynamoDBEntry[ ] { 1992 } );
  config.Filter.AddCondition( "title", QueryOperator.Between, new DynamoDBEntry[ ] { "B", "Hzz" } );
  config.AttributesToGet = new List<string> { "year", "title", "info" };
  config.Select = SelectValues.SpecificAttributes;
  search = moviesTable.Query( config ); 
```

**Azure Cosmos DB**:

No Azure Cosmos DB, você pode fazer a projeção e o filtro dentro de uma consulta SQL simples:

```csharp
var result = moviesContainer.GetItemQueryIterator<MovieModel>( 
  "select c.Year, c.Title, c.info from c where Year=1998 AND (CONTAINS(Title,'B') OR CONTAINS(Title,'Hzz'))");
```

Para operações de intervalo, por exemplo, 'between', você precisa fazer uma verificação no Amazon DynamoDB:

```csharp
ScanRequest sRequest = new ScanRequest
{
  TableName = "Movies",
  ExpressionAttributeNames = new Dictionary<string, string>
  {
    { "#yr", "year" }
  },
  ExpressionAttributeValues = new Dictionary<string, AttributeValue>
  {
      { ":y_a", new AttributeValue { N = "1960" } },
      { ":y_z", new AttributeValue { N = "1969" } },
  },
  FilterExpression = "#yr between :y_a and :y_z",
  ProjectionExpression = "#yr, title, info.actors[0], info.directors, info.running_time_secs"
};

ClientScanning_async( sRequest ).Wait( );
```

No Azure Cosmos DB, você pode usar a consulta SQL e uma instrução de linha única:

```csharp
var result = moviesContainer.GetItemQueryIterator<MovieModel>( 
  "select c.title, c.info.actors[0], c.info.directors,c.info.running_time_secs from c where BETWEEN year 1960 AND 1969");
```

### <a name="delete-a-container"></a>Excluir um contêiner

**DynamoDB**:

Para excluir a tabela no Amazon DynamoDB, você pode especificar:

```csharp
client.DeleteTableAsync( tableName );
```

**Azure Cosmos DB**:

Para excluir a coleção no Azure Cosmos DB, você pode especificar:

```csharp
await moviesContainer.DeleteContainerAsync();
```
Em seguida, exclua o banco de dados também se precisar:

```csharp
await cosmosDatabase.DeleteAsync();
```

Como você pode ver, o Azure Cosmos DB dá suporte a consultas naturais (SQL), as operações são assíncronas e muito mais fáceis. Você pode facilmente migrar seu código complexo para p Azure Cosmos DB, o que se torna mais simples após a migração.

### <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [otimização de desempenho](performance-tips.md).
- Saiba mais sobre [otimizar leituras e gravações](key-value-store-cost.md)
- Saiba mais sobre [monitoramento no Cosmos DB](monitor-cosmos-db.md)

