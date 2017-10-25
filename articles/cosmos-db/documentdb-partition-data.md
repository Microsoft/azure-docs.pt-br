---
title: Particionamento e escala no Azure Cosmos DB | Microsoft Docs
description: "Saiba mais sobre como o particionamento funciona no DB Cosmos do Azure, como configurar o particionamento e as chaves de partição e como escolher a chave de partição correta para seu aplicativo."
services: cosmos-db
author: arramac
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: 702c39b4-1798-48dd-9993-4493a2f6df9e
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/24/2017
ms.author: arramac
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 81010d91ac7fe8fa7149c52ed56af304cf4e83d9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="partitioning-in-azure-cosmos-db-using-the-documentdb-api"></a>Particionamento no Azure Cosmos DB usando a API do DocumentDB

O [DB Cosmos do Microsoft Azure](../cosmos-db/introduction.md) é um serviço com vários modelos de banco de dados, distribuído globalmente, que foi criado para ajudá-lo a obter um desempenho rápido e previsível e a dimensionar continuamente conforme o crescimento de seu aplicativo. 

Este artigo fornece uma visão geral de como trabalhar com o particionamento de contêineres do Cosmos DB com a API do DocumentDB. Consulte [particionamento e escala horizontal](../cosmos-db/partition-data.md) para obter uma visão geral dos conceitos e das melhores práticas de particionamento com uma API do Azure Cosmos DB. 

Para começar a codificar, baixe o projeto no [GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/a2d61ddb53f8ab2a23d3ce323c77afcf5a608f52/samples/documentdb-benchmark). 

Depois de ler este artigo, você poderá responder as seguintes perguntas:   

* Como funciona o particionamento no Azure Cosmos DB?
* Como fazer para configurar o particionamento no Azure Cosmos DB?
* O que são chaves de partição e como escolher a chave de partição correta para meu aplicativo?

Para começar a codificar, baixe o projeto na [Amostra de Test Drive de Desempenho do Azure Cosmos DB](https://github.com/Azure/azure-documentdb-dotnet/tree/a2d61ddb53f8ab2a23d3ce323c77afcf5a608f52/samples/documentdb-benchmark). 

<!-- placeholder until we have a permanent solution-->
<a name="partition-keys"></a>
<a name="single-partition-and-partitioned-collections"></a>
<a name="migrating-from-single-partition"></a>

## <a name="partition-keys"></a>Chaves de partição

Na API do DocumentDB, você especifica a definição da chave de partição na forma de um caminho JSON. A tabela a seguir mostra exemplos de definições de chave de partição e os valores correspondentes a cada uma. A chave de partição é especificada como um caminho, por exemplo, `/department` representa o departamento de propriedade. 

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p><strong>Chave de partição</strong></p></td>
            <td valign="top"><p><strong>Descrição</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>/department</p></td>
            <td valign="top"><p>Corresponde ao valor de doc.department, em que doc é o item.</p></td>
        </tr>
        <tr>
            <td valign="top"><p>/properties/name</p></td>
            <td valign="top"><p>Corresponde ao valor de doc.properties.name, em que doc é o item (propriedade aninhada).</p></td>
        </tr>
        <tr>
            <td valign="top"><p>/id</p></td>
            <td valign="top"><p>Corresponde ao valor de doc.id (a ID e a chave de partição são a mesma propriedade).</p></td>
        </tr>
        <tr>
            <td valign="top"><p>/"nome do departamento"</p></td>
            <td valign="top"><p>Corresponde ao valor de doc[“nome do departamento”], em que doc é o item.</p></td>
        </tr>
    </tbody>
</table>

> [!NOTE]
> A sintaxe da chave de partição é semelhante à especificação de caminho para caminhos de política de indexação, com a diferença básica de que o caminho corresponde à propriedade em vez do valor, ou seja, não há nenhum caractere curinga no final. Por exemplo, você especificaria /department/? para indexar os valores de departamento, mas especificaria /department como a definição de chave de partição. A chave de partição é indexada implicitamente e não pode ser excluída da indexação com substituições de política de indexação.
> 
> 

Vamos analisar como a opção da chave de partição afeta o desempenho do aplicativo.

## <a name="working-with-the-azure-cosmos-db-sdks"></a>Trabalhando com SDKs do Azure Cosmos DB
O Azure Cosmos DB adicionou suporte ao particionamento automático na [API REST versão 2015-12-16](/rest/api/documentdb/). Para criar contêineres particionados, você deve baixar versões do SDK 1.6.0 ou mais novas em uma das plataformas do SDK com suporte (.NET, Node.js, Java, Python, MongoDB). 

### <a name="creating-containers"></a>Criando contêineres
O exemplo a seguir mostra um trecho do .NET para criação de um contêiner para armazenar dados telemétricos do dispositivo de 20.000 unidades de solicitação por segundo de produtividade. O SDK define o valor de OfferThroughput (que por sua vez define o cabeçalho de solicitação `x-ms-offer-throughput` na API REST). Aqui, definimos `/deviceId` como a chave de partição. A opção de chave de partição é salva com o restante dos metadados do contêiner, como nome e política de indexação.

Para este exemplo, escolhemos `deviceId` , pois sabemos que (a) uma vez que há um grande número de dispositivos, as gravações podem ser distribuídas uniformemente nas partições e nos permite dimensionar o banco de dados para incluir grandes volumes de dados e (b) muitas solicitações como buscar a última leitura para um dispositivo limitam-se a um único deviceId e podem ser recuperadas de uma única partição.

```csharp
DocumentClient client = new DocumentClient(new Uri(endpoint), authKey);
await client.CreateDatabaseAsync(new Database { Id = "db" });

// Container for device telemetry. Here the property deviceId will be used as the partition key to 
// spread across partitions. Configured for 10K RU/s throughput and an indexing policy that supports 
// sorting against any number or string property.
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "coll";
myCollection.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("db"),
    myCollection,
    new RequestOptions { OfferThroughput = 20000 });
```

Esse método faz uma chamada à API REST ao Cosmos DB e o serviço provisionará várias partições com base na produtividade solicitada. Você pode alterar a produtividade de um contêiner conforme suas necessidades de desempenho mudarem. 

### <a name="reading-and-writing-items"></a>Lendo e gravando itens
Agora, vamos inserir dados no Cosmos DB. Esta é uma classe de exemplo que contém uma leitura de dispositivo e uma chamada a CreateDocumentAsync para inserir uma nova leitura de dispositivo em um contêiner. Este é um exemplo que utiliza a API do DocumentDB:

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

// Create a document. Here the partition key is extracted as "XMS-0001" based on the collection definition
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

Vamos ler o item pela chave da partição e ID, atualizá-lo e, então, como uma etapa final, excluí-lo pela chave de partição e ID. Observe que as leituras incluem um valor de PartitionKey (correspondente ao cabeçalho de solicitação `x-ms-documentdb-partitionkey` na API REST).

```csharp
// Read document. Needs the partition key and the ID to be specified
Document result = await client.ReadDocumentAsync(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
  new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });

DeviceReading reading = (DeviceReading)(dynamic)result;

// Update the document. Partition key is not required, again extracted from the document
reading.MetricValue = 104;
reading.ReadingTime = DateTime.UtcNow;

await client.ReplaceDocumentAsync(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
  reading);

// Delete document. Needs partition key
await client.DeleteDocumentAsync(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
  new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });
```

### <a name="querying-partitioned-containers"></a>Consultando contêineres particionados
Quando você consulta dados em contêineres particionados, o Azure Cosmos DB encaminha automaticamente a consulta para as partições que correspondem aos valores de chave de partição especificados no filtro (se houver). Por exemplo, esta consulta é roteada para apenas a partição que contém a chave de partição "XMS-0001".

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

O Cosmos DB dá suporte a [funções de agregação](documentdb-sql-query.md#Aggregates) `COUNT`, `MIN`, `MAX`, `SUM` e `AVG` em contêineres particionados usando SQL, a partir dos SDKs 1.12.0 e posterior. As consultas devem incluir um único operador de agregação e um único valor na projeção.

### <a name="parallel-query-execution"></a>Execução de consulta paralela
Os SDKs 1.9.0 e posterior do Cosmos DB dão suporte a opções de execução de consulta paralela, que permitem realizar consultas de baixa latência em coleções particionadas, mesmo quando elas precisam acessar um grande número de partições. Por exemplo, a consulta a seguir é configurada para ser executada paralelamente entre partições.

```csharp
// Cross-partition Order By Queries
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("db", "coll"), 
    new FeedOptions { EnableCrossPartitionQuery = true, MaxDegreeOfParallelism = 10, MaxBufferedItemCount = 100})
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100)
    .OrderBy(m => m.MetricValue);
```
    
Você pode gerenciar a execução de consulta paralela ajustando os seguintes parâmetros:

* Ao definir `MaxDegreeOfParallelism`, é possível controlar o grau de paralelismo, ou seja, o número máximo de conexões de rede simultâneas às partições do contêiner. Se você definir esse valor como -1, o grau de paralelismo será gerenciado pelo SDK. Se o `MaxDegreeOfParallelism` não for especificado nem definido como 0, que é o valor padrão, haverá uma única conexão de rede às partições do contêiner.
* Definindo `MaxBufferedItemCount`, você pode compensar a latência da consulta e a utilização da memória no lado do cliente. Se você omitir esse parâmetro ou defini-lo como -1, o número de itens armazenados em buffer durante a execução da consulta paralela será gerenciado pelo SDK.

Dado o mesmo estado da coleção, uma consulta paralela retornará resultados na mesma ordem da execução serial. Ao executar uma consulta entre partições que inclui classificação (ORDER BY e/ou TOP), o SDK do Azure Cosmos DB emite a consulta paralelamente entre partições e mescla os resultados parcialmente classificados no lado do cliente para produzir resultados ordenados globalmente.

### <a name="executing-stored-procedures"></a>Executando procedimentos armazenados
Você também poderá executar transações atômicas em documentos com a mesma ID de dispositivo, por exemplo, se estiver mantendo agregações ou o último estado de um dispositivo em um único item. 

```csharp
await client.ExecuteStoredProcedureAsync<DeviceReading>(
    UriFactory.CreateStoredProcedureUri("db", "coll", "SetLatestStateAcrossReadings"),
    new RequestOptions { PartitionKey = new PartitionKey("XMS-001") }, 
    "XMS-001-FE24C");
```
   
Na próxima seção, examinaremos como é possível passar de contêineres de partição única para contêineres particionados.

## <a name="next-steps"></a>Próximas etapas
Neste artigo, apresentamos uma visão geral de como trabalhar com o particionamento de contêineres do Azure Cosmos DB com a API do DocumentDB. Consulte também [particionamento e escala horizontal](../cosmos-db/partition-data.md) para obter uma visão geral dos conceitos e das melhores práticas de particionamento com uma API do Azure Cosmos DB. 

* Executar testes de desempenho e escala com o BD Cosmos do Azure. Consulte [Teste de desempenho e escala com o BD Cosmos do Azure](performance-testing.md) para obter um exemplo.
* Introdução à codificação com os [SDKs](documentdb-sdk-dotnet.md) ou a [API REST](/rest/api/documentdb/)
* Saiba mais sobre a [produtividade provisionada no DB Cosmos do Azure](request-units.md)

