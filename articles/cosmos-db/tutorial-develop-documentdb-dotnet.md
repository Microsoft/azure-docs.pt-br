---
title: 'Azure Cosmos DB: Desenvolver com a API DocumentDB no .NET | Microsoft Docs'
description: Aprenda a desenvolver com a API DocumentDB do Azure Cosmos DB usando o .NET
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
tags: 
ms.assetid: 
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 05/10/2017
ms.author: mimig
ms.custom: mvc
ms.openlocfilehash: 871731adc849c575d97ed6735b87457811668ed5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cosmosdb-develop-with-the-documentdb-api-in-net"></a>Azure Cosmos DB: Desenvolver com a API DocumentDB no .NET

O Azure Cosmos DB é o serviço de banco de dados multimodelo distribuído globalmente da Microsoft. É possível criar e consultar rapidamente documentos, chave/valor e bancos de dados do gráfico. Todos se beneficiam de recursos de escala horizontal e distribuição global no núcleo do Azure Cosmos DB. 

Este tutorial mostra como criar uma conta do Azure Cosmos DB usando o portal do Azure e, em seguida, criar um banco de dados de documentos e uma coleção com uma [chave de partição](documentdb-partition-data.md#partition-keys) usando a [API .NET do DocumentDB](documentdb-introduction.md). Ao definindo uma chave de partição quando você cria uma coleção, seu aplicativo está preparado para dimensionar com facilidade à medida que seus dados crescem. 

Este tutorial cobre as seguintes tarefas usando [API .NET do DocumentDB](documentdb-sdk-dotnet.md):

> [!div class="checklist"]
> * Criar uma conta do Azure Cosmos DB
> * Criar um banco de dados e uma coleção com uma chave de partição
> * Criar documentos JSON
> * Atualizar um documento
> * Consultar coleções particionadas
> * Executar procedimentos armazenados
> * Excluir um documento
> * Excluir um banco de dados

## <a name="prerequisites"></a>Pré-requisitos
Certifique-se que você tem o seguinte:

* Uma conta ativa do Azure. Se não tiver uma, você poderá se inscrever em uma [conta gratuita](https://azure.microsoft.com/free/). 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* Se você ainda não tem o Visual 2017 Studio instalado, poderá baixar e usar o **Visual Studio 2017 Community Edition** [gratuito](https://www.visualstudio.com/downloads/). Verifique se você habilitou o **desenvolvimento do Azure** durante a instalação do Visual Studio.

## <a name="create-an-azure-cosmos-db-account"></a>Criar uma conta do Azure Cosmos DB

Vamos começar criando uma conta do Azure Cosmos DB no portal do Azure.

> [!TIP]
> * Já tem uma conta do Azure Cosmos DB? Nesse caso, pule para [Configurar sua solução do Visual Studio](#SetupVS)
> * Você tinha uma conta do Azure DocumentDB? Se sua conta agora é uma conta do Azure Cosmos DB, você pode pular para [Configurar sua solução do Visual Studio](#SetupVS).  
> * Se estiver usando o Emulador do Azure Cosmos DB, execute as etapas em [Emulador do Azure Cosmos DB](local-emulator.md) para configurar o emulador e pule para [Configurar sua solução do Visual Studio](#SetupVS). 
>
>

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="SetupVS"></a>Configurar sua solução do Visual Studio
1. Abra o **Visual Studio** no seu computador.
2. No menu **Arquivo**, selecione **Novo** e depois **Projeto**.
3. Na caixa de diálogo **Novo Projeto**, selecione **Modelos** / **Visual C#** / **Aplicativo de Console (.NET Framework)**, nomeie o projeto e clique em **OK**.
   ![Captura de tela da janela Novo Projeto](./media/tutorial-develop-documentdb-dotnet/nosql-tutorial-new-project-2.png)

4. No **Gerenciador de Soluções**, clique com o botão direito do mouse no seu novo aplicativo de console, que está em sua solução do Visual Studio e clique em **gerenciar pacotes NuGet...**
    
    ![Captura de tela do menu exibido pelo clique com o botão direito do mouse para o projeto](./media/tutorial-develop-documentdb-dotnet/nosql-tutorial-manage-nuget-pacakges.png)
5. Na guia **NuGet**, clique em **Procurar** e digite **documentdb** na caixa de pesquisa.
<!---stopped here--->
6. Nos resultados, encontre **Microsoft.Azure.DocumentDB** e clique em **Instalar**.
   A ID do pacote para a Biblioteca de Clientes do Azure Cosmos DB é [Microsoft.Azure.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB).
   ![Captura de tela do menu NuGet para localizar documentos do SDK do cliente do Azure Cosmos DB](./media/tutorial-develop-documentdb-dotnet/nosql-tutorial-manage-nuget-pacakges-2.png)

    Se receber uma mensagem sobre a análise das alterações para a solução, clique em **OK**. Se receber uma mensagem sobre a aceitação da licença, clique em **Aceito**.

## <a id="Connect"></a>Adicionar referências ao seu projeto
As etapas restantes neste tutorial fornecem os trechos de código da API do DocumentDB necessárias para criar e atualizar os recursos do Azure Cosmos DB em seu projeto.

Primeiro, adicione essas referências ao seu aplicativo.
<!---These aren't added by default when you install the pkg?--->

```csharp
using System.Net;
using Microsoft.Azure.Documents;
using Microsoft.Azure.Documents.Client;
using Newtonsoft.Json;
```

## <a id="add-references"></a>Conecte seu aplicativo

Em seguida, adicione essas duas constantes e sua variável de *cliente* no seu aplicativo.

```csharp
private const string EndpointUrl = "<your endpoint URL>";
private const string PrimaryKey = "<your primary key>";
private DocumentClient client;
```

Em seguida, volte ao [portal do Azure](https://portal.azure.com) para recuperar a URL do ponto de extremidade e a chave primária. A URL do ponto de extremidade e a chave primária são necessárias para que seu aplicativo reconheça onde deve se conectar e para que o Azure Cosmos DB confie na conexão do seu aplicativo.

No portal do Azure, navegue até a sua conta do Azure Cosmos DB, clique em **Chaves** e, em seguida, clique em **Chaves de leitura/gravação**.

Copie o URI do portal e cole-o em `<your endpoint URL>` no arquivo program.cs. Em seguida, copie a CHAVE PRIMÁRIA do portal e cole-a em `<your primary key>`. Certifique-se de remover o `<` e `>` de seus valores.

![Captura de tela do portal do Azure usado pelo tutorial do NoSQL para criar um aplicativo de console em C#. Mostra uma conta do Azure Cosmos DB, com CHAVES realçadas na folha da conta do Azure Cosmos DB e os valores de URI e CHAVE PRIMÁRIA realçados na folha Chaves](./media/tutorial-develop-documentdb-dotnet/nosql-tutorial-keys.png)

## <a id="instantiate"></a>Criar uma instância do DocumentClient

Agora, crie uma nova instância do **DocumentClient**.

```csharp
DocumentClient client = new DocumentClient(new Uri(EndpointUrl), PrimaryKey);
```

## <a id="create-database"></a>Criar um banco de dados

Em seguida, crie um [banco de dados](documentdb-resources.md#databases) do Azure Cosmos DB usando o método [CreateDatabaseAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdatabaseasync.aspx) ou o método [CreateDatabaseIfNotExistsAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdatabaseifnotexistsasync.aspx) da classe **DocumentClient** a partir do [SDK .NET do DocumentDB](documentdb-sdk-dotnet.md). Um banco de dados é o contêiner lógico de armazenamento de documentos JSON particionado em coleções.

```csharp
await client.CreateDatabaseAsync(new Database { Id = "db" });
```
## <a name="decide-on-a-partition-key"></a>Escolha uma chave de partição 

Coleções são contêineres para armazenamento de documentos. Elas são recursos lógicos e podem [abranger uma ou mais partições físicas](partition-data.md). Uma [chave de partição](documentdb-partition-data.md) é uma propriedade (ou caminho) nos documentos que é ser usada para distribuir dados entre vários servidores ou partições. Todos os documentos com a mesma chave de partição são armazenados na mesma partição. 

Determinar uma chave de partição é uma decisão importante que deve ser tomada antes de você criar uma coleção. As chaves de partição são uma propriedade (ou caminho) nos documentos que podem ser usadas pelo Azure Cosmos DB para distribuir dados entre vários servidores ou partições. O Cosmos DB faz o hash do valor da chave de partição e usa o resultado com hash para determinar em qual partição deve armazenar o item. Todos os documentos com a mesma chave de partição são armazenados na mesma partição e as chaves de partição não podem ser alteradas depois que uma coleção é criada. 

Para este tutorial, vamos definir a chave de partição para `/deviceId` para que todos os dados para um único dispositivo sejam armazenados em uma única partição. Você deseja escolher uma chave de partição que tem um grande número de valores, cada um dos quais são usados com quase a mesma frequência para garantir que o Cosmos DB pode balancear a carga conforme os dados aumentam e atingem a taxa de transferência total da coleção. 

Para obter mais informações sobre particionamento, consulte [Como particionar e dimensionar no Azure Cosmos DB?](partition-data.md) 

## <a id="CreateColl"></a>Criar uma coleção 

Agora que conhecemos a nossa chave de partição, `/deviceId`, permite criar uma [coleção](documentdb-resources.md#collections) usando o método [CreateDocumentCollectionAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdocumentcollectionasync.aspx) ou o método [CreateDocumentCollectionIfNotExistsAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentcollectionifnotexistsasync.aspx) da classe **DocumentClient**. Uma coleção é um contêiner de documentos JSON e qualquer lógica de aplicativo JavaScript associada. 

> [!WARNING]
> Criar uma coleção tem implicações de preços, pois você está reservando a taxa de transferência para o aplicativo se comunicar com o Azure Cosmos DB. Para obter mais detalhes, visite a nossa [página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/)
> 
> 

```csharp
// Collection for device telemetry. Here the JSON property deviceId is used  
// as the partition key to spread across partitions. Configured for 2500 RU/s  
// throughput and an indexing policy that supports sorting against any  
// number or string property. .
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "coll";
myCollection.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("db"),
    myCollection,
    new RequestOptions { OfferThroughput = 2500 });
```

Esse método faz uma chamada da API REST ao Azure Cosmos DB e o serviço provisiona várias partições com base na produtividade solicitada. Você pode alterar a taxa de transferência de uma coleção conforme suas necessidades de desempenho aumentarem usando o SDK ou o [portal do Azure](set-throughput.md).

## <a id="CreateDoc"></a>Criar documentos JSON
Vamos inserir alguns documentos JSON no Azure Cosmos DB. Um [documento](documentdb-resources.md#documents) pode ser criado usando o método [CreateDocumentAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentasync.aspx) da classe **DocumentClient**. Os documentos são conteúdo JSON (arbitrário) definido pelo usuário. Essa classe de exemplo contém uma leitura de dispositivo e uma chamada para CreateDocumentAsync para inserir uma nova leitura de dispositivo em uma coleção.

```csharp
public class DeviceReading
{
    [JsonProperty("id")]
    public string Id;

    [JsonProperty("deviceId")]
    public string DeviceId;

    [JsonConverter(typeof(IsoDateTimeConverter))]
    [JsonProperty("readingTime")]
    public DateTime ReadingTime;

    [JsonProperty("metricType")]
    public string MetricType;

    [JsonProperty("unit")]
    public string Unit;

    [JsonProperty("metricValue")]
    public double MetricValue;
  }

// Create a document. Here the partition key is extracted 
// as "XMS-0001" based on the collection definition
await client.CreateDocumentAsync(
    UriFactory.CreateDocumentCollectionUri("db", "coll"),
    new DeviceReading
    {
        Id = "XMS-001-FE24C",
        DeviceId = "XMS-0001",
        MetricType = "Temperature",
        MetricValue = 105.00,
        Unit = "Fahrenheit",
        ReadingTime = DateTime.UtcNow
    });
```
## <a name="read-data"></a>Ler dados

Vamos ler o documento pela sua chave de partição e Id usando o método ReadDocumentAsync. Observe que as leituras incluem um valor de PartitionKey (correspondente ao cabeçalho de solicitação `x-ms-documentdb-partitionkey` na API REST).

```csharp
// Read document. Needs the partition key and the Id to be specified
Document result = await client.ReadDocumentAsync(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
  new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });

DeviceReading reading = (DeviceReading)(dynamic)result;
```

## <a name="update-data"></a>Atualizar dados

Agora, vamos atualizar alguns dados usando o método ReplaceDocumentAsync.

```csharp
// Update the document. Partition key is not required, again extracted from the document
reading.MetricValue = 104;
reading.ReadingTime = DateTime.UtcNow;

await client.ReplaceDocumentAsync(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
  reading);
```

## <a name="delete-data"></a>Excluir dados

Agora vamos excluir um documento pela sua chave de partição e id usando o método DeleteDocumentAsync.

```csharp
// Delete a document. The partition key is required.
await client.DeleteDocumentAsync(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
  new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });
```
## <a name="query-partitioned-collections"></a>Consultar coleções particionadas

Quando você consulta dados em coleções particionadas, o Azure Cosmos DB roteia automaticamente a consulta para as partições que correspondem aos valores de chave de partição especificados no filtro (caso haja algum). Por exemplo, esta consulta é roteada para apenas a partição que contém a chave de partição "XMS-0001".

```csharp
// Query using partition key
IQueryable<DeviceReading> query = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("db", "coll"))
    .Where(m => m.MetricType == "Temperature" && m.DeviceId == "XMS-0001");
```
    
A consulta a seguir não tem um filtro na chave de partição (DeviceId) e é distribuída para todas as partições em que ela é executada no índice da partição. Observe que você precisa especificar o EnableCrossPartitionQuery (`x-ms-documentdb-query-enablecrosspartition` na API REST) para fazer com que o SDK execute uma consulta em partições.

```csharp
// Query across partition keys
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("db", "coll"), 
    new FeedOptions { EnableCrossPartitionQuery = true })
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100);
```

## <a name="parallel-query-execution"></a>Execução de consulta paralela
Os SDKs de DocumentDB do Azure Cosmos DB 1.9.0 e superiores são compatíveis com execução de consulta paralela, que permite realizar consultas de baixa latência em coleções particionadas, mesmo quando elas precisam tocar um grande número de partições. Por exemplo, a consulta a seguir é configurada para ser executada paralelamente entre partições.

```csharp
// Cross-partition Order By queries
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("db", "coll"), 
    new FeedOptions { EnableCrossPartitionQuery = true, MaxDegreeOfParallelism = 10, MaxBufferedItemCount = 100})
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100)
    .OrderBy(m => m.MetricValue);
```
    
Você pode gerenciar a execução de consulta paralela ajustando os seguintes parâmetros:

* Ao definir `MaxDegreeOfParallelism`, é possível controlar o grau de paralelismo, isto é, o número máximo de conexões de rede simultâneas com as partições da coleção. Se você definir esse valor como -1, o grau de paralelismo será gerenciado pelo SDK. Se o `MaxDegreeOfParallelism` não for especificado nem definido para 0, que é o valor padrão, haverá uma única conexão de rede para as partições da coleção.
* Definindo `MaxBufferedItemCount`, você pode compensar a latência da consulta e a utilização da memória no lado do cliente. Se você omitir esse parâmetro ou defini-lo como -1, o número de itens armazenados em buffer durante a execução da consulta paralela será gerenciado pelo SDK.

Dado o mesmo estado da coleção, uma consulta paralela retornará resultados na mesma ordem da execução serial. Ao executar uma consulta entre partições que inclui classificação (ORDER BY e/ou TOP), o SDK do DocumentDB emite a consulta paralelamente entre partições e mescla os resultados parcialmente classificados no lado do cliente para produzir resultados ordenados globalmente.

## <a name="execute-stored-procedures"></a>Executar procedimentos armazenados
Por último, você poderá executar transações atômicas em documentos com a mesma ID de dispositivo, por exemplo, se você estiver mantendo agregações ou o estado mais recente de um dispositivo em um único documento adicionado o código a seguir ao seu projeto.

```csharp
await client.ExecuteStoredProcedureAsync<DeviceReading>(
    UriFactory.CreateStoredProcedureUri("db", "coll", "SetLatestStateAcrossReadings"),
    new RequestOptions { PartitionKey = new PartitionKey("XMS-001") }, 
    "XMS-001-FE24C");
```

E isso é tudo! Esses são os principais componentes de um aplicativo do Azure Cosmos DB que usa uma chave de partição para dimensionar com eficiência a distribuição de dados em partições.  

## <a name="clean-up-resources"></a>Limpar recursos

Se você não continuar usando este aplicativo, exclua todos os recursos criados por esse tutorial no portal do Azure com as seguintes etapas:

1. No menu à esquerda no portal do Azure, clique em **Grupos de recursos** e depois clique no nome exclusivo do recurso criado. 
2. Em sua página de grupo de recursos, clique em **Excluir**, digite o nome do recurso para excluir na caixa de texto e depois clique em **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você fez o seguinte: 

> [!div class="checklist"]
> * Criou uma conta do Azure Cosmos DB
> * Criou um banco de dados e uma coleção com uma chave de partição
> * Criou documentos JSON
> * Atualizou um documento
> * Consultou coleções particionadas
> * Executou um procedimento armazenado
> * Excluiu um documento
> * Excluiu um banco de dados

Agora você pode prosseguir para o próximo tutorial e importar dados adicionais para a sua conta do Cosmos DB. 

> [!div class="nextstepaction"]
> [Importar dados para o Azure Cosmos DB](import-data.md)
