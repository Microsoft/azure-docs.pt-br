---
title: Usar a biblioteca .NET do executor em massa de grafo com a API do Gremlin no Azure Cosmos DB
description: Saiba como usar a biblioteca bulk executor para importar dados gráficos em massa para um contêiner da API Gremlin do Azure Cosmos DB.
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 05/28/2019
ms.author: chrande
ms.reviewer: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: b31cb33e09158de5912132d0fb7bd31a62131181
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93360506"
---
# <a name="using-the-graph-bulk-executor-net-library-to-perform-bulk-operations-in-azure-cosmos-db-gremlin-api"></a>Usar a biblioteca .NET bulk executor do grafo para executar operações em massa na API Gremlin do Azure Cosmos DB
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

Este tutorial fornece instruções sobre como usar a biblioteca .NET bulk executor do Azure Cosmos DB para importar e atualizar objetos gráficos para o contêiner da API Gremlin do Azure Cosmos DB. Esse processo usa a classe Graph na [biblioteca bulk executor](./bulk-executor-overview.md) para criar programaticamente objetos de vértice e de borda para, em seguida, inserir vários deles quando houver solicitações de rede. Esse comportamento pode ser configurado pela biblioteca bulk executor para otimizar o uso de recursos do banco de dados e da memória local.

Em vez de enviar consultas do Gremlin para um banco de dados, onde o comando é avaliado e, em seguida, executado um a um, o uso da biblioteca bulk executor exigirá a criação e a validação dos objetos localmente. Depois de criar os objetos, a biblioteca permitirá que você envie objetos gráficos para o serviço de banco de dados em sequência. Com esse método, as velocidades de ingestão de dados podem ser aumentadas em até 100 vezes, tornando-o o método ideal para migrações de dados iniciais ou operações periódicas de movimentação de dados. Saiba mais visitando a página do GitHub do [aplicativo de exemplo do Graph bulk executor do Azure Cosmos DB](https://github.com/Azure-Samples/azure-cosmosdb-graph-bulkexecutor-dotnet-getting-started).

## <a name="bulk-operations-with-graph-data"></a>Operações em massa com dados do gráfico

A [biblioteca bulk executor](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.graph?preserve-view=true&view=azure-dotnet) contém um namespace `Microsoft.Azure.CosmosDB.BulkExecutor.Graph` para fornecer recursos para criar e importar objetos gráficos. 

O processo a seguir descreve como a migração de dados pode ser usada para um contêiner da API Gremlin:
1. Recupere registros na fonte de dados.
2. Crie objetos `GremlinVertex` e `GremlinEdge` a partir dos registros obtidos e adicione-os a uma estrutura de dados `IEnumerable`. Nesta parte do aplicativo, a lógica para detectar e adicionar relacionamentos deve ser implementada, caso a fonte de dados não seja um banco de dados gráfico.
3. Use o [método Graph BulkImportAsync](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.graph.graphbulkexecutor.bulkimportasync?preserve-view=true&view=azure-dotnet) para inserir os objetos gráficos na coleção.

Esse mecanismo melhorará a eficiência da migração de dados, em comparação com o uso de um cliente Gremlin. Essa melhoria é percebida porque a inserção de dados no Gremlin exigirá que o aplicativo envie uma consulta por vez e essa consulta precisará ser validada, avaliada e, em seguida, executada para criar os dados. A biblioteca bulk executor tratará da validação no aplicativo e enviará múltiplos objetos gráficos por vez para cada solicitação de rede.

### <a name="creating-vertices-and-edges"></a>Criar vértices e bordas

`GraphBulkExecutor` disponibiliza o método `BulkImportAsync` que exige uma lista `IEnumerable` de objetos `GremlinVertex` ou `GremlinEdge`, definidos no namespace `Microsoft.Azure.CosmosDB.BulkExecutor.Graph.Element`. No exemplo, separamos as bordas e vértices em duas tarefas de importação do BulkExecutor. Veja o exemplo abaixo:

```csharp

IBulkExecutor graphbulkExecutor = new GraphBulkExecutor(documentClient, targetCollection);

BulkImportResponse vResponse = null;
BulkImportResponse eResponse = null;

try
{
    // Import a list of GremlinVertex objects
    vResponse = await graphbulkExecutor.BulkImportAsync(
            Utils.GenerateVertices(numberOfDocumentsToGenerate),
            enableUpsert: true,
            disableAutomaticIdGeneration: true,
            maxConcurrencyPerPartitionKeyRange: null,
            maxInMemorySortingBatchSize: null,
            cancellationToken: token);

    // Import a list of GremlinEdge objects
    eResponse = await graphbulkExecutor.BulkImportAsync(
            Utils.GenerateEdges(numberOfDocumentsToGenerate),
            enableUpsert: true,
            disableAutomaticIdGeneration: true,
            maxConcurrencyPerPartitionKeyRange: null,
            maxInMemorySortingBatchSize: null,
            cancellationToken: token);
}
catch (DocumentClientException de)
{
    Trace.TraceError("Document client exception: {0}", de);
}
catch (Exception e)
{
    Trace.TraceError("Exception: {0}", e);
}
```

Para mais informações sobre os parâmetros da biblioteca bulk executor, confira o [tópico BulkImportData para o Azure Cosmos DB](bulk-executor-dot-net.md#bulk-import-data-to-an-azure-cosmos-account).

A carga deve ser instanciada nos objetos `GremlinVertex` e `GremlinEdge`. Veja como esses objetos podem ser criados:

**Vértices** :
```csharp
// Creating a vertex
GremlinVertex v = new GremlinVertex(
    "vertexId",
    "vertexLabel");

// Adding custom properties to the vertex
v.AddProperty("customProperty", "value");

// Partitioning keys must be specified for all vertices
v.AddProperty("partitioningKey", "value");
```

**Bordas** :
```csharp
// Creating an edge
GremlinEdge e = new GremlinEdge(
    "edgeId",
    "edgeLabel",
    "targetVertexId",
    "sourceVertexId",
    "targetVertexLabel",
    "sourceVertexLabel",
    "targetVertexPartitioningKey",
    "sourceVertexPartitioningKey");

// Adding custom properties to the edge
e.AddProperty("customProperty", "value");
```

> [!NOTE]
> O utilitário bulk executor não verifica automaticamente a existência de Vértices antes de adicionar Bordas. É preciso validar essa opção no aplicativo antes de executar as tarefas de BulkImport.

## <a name="sample-application"></a>Aplicativo de exemplo

### <a name="prerequisites"></a>Pré-requisitos
* Visual Studio 2019 com a carga de trabalho de desenvolvimento do Azure. Comece usando a versão gratuita do [Visual Studio 2019 Community Edition](https://visualstudio.microsoft.com/downloads/).
* Uma assinatura do Azure. Crie [uma conta gratuita do Azure aqui](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cosmos-db). Como alternativa, é possível criar uma conta de banco de dados do Cosmos sem uma assinatura do Azure com [Experimentar o Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/).
* Um banco de dados da API Gremlin do Azure Cosmos DB com uma **coleção ilimitada**. Esse guia apresenta a introdução à [API Gremlin do Azure Cosmos DB no .NET](./create-graph-dotnet.md).
* Git. Para mais informações, confira a [página Downloads do Git](https://git-scm.com/downloads).

### <a name="clone-the-sample-application"></a>Clonar o aplicativo de exemplo
Neste tutorial, seguiremos as etapas de introdução usando o [exemplo do Graph bulk executor do Azure Cosmos DB](https://github.com/Azure-Samples/azure-cosmosdb-graph-bulkexecutor-dotnet-getting-started) hospedado no GitHub. Este aplicativo consiste em uma solução .NET que gera aleatoriamente objetos de vértice e de borda e, em seguida, executa inserções em massa na conta especificada do banco de dados gráfico. Para obter o aplicativo, execute o comando `git clone` a seguir:

```bash
git clone https://github.com/Azure-Samples/azure-cosmosdb-graph-bulkexecutor-dotnet-getting-started.git
```

Esse repositório contém o exemplo GraphBulkExecutor com os seguintes arquivos:

Arquivo|Descrição
---|---
`App.config`|É aqui que o aplicativo e os parâmetros específicos do banco de dados são especificados. Primeiro, esse arquivo precisa ser modificado para se conectar ao banco de dados de destino e às coleções.
`Program.cs`| Esse arquivo contém a lógica por trás da criação da coleção `DocumentClient`, do tratamento das limpezas e do envio das solicitações do bulk executor.
`Util.cs`| Esse arquivo contém uma classe auxiliar que contém a lógica por trás da geração de dados de teste e da verificação da existência do banco de dados e das coleções.

No arquivo `App.config`, os itens a seguir são os valores de configuração que podem ser fornecidos:

Configuração|Descrição
---|---
`EndPointUrl`|Este é **seu ponto de extremidade do SDK do .NET** , localizado na folha Visão geral de sua conta do banco de dados da API Gremlin do Azure Cosmos DB. O identificador tem o formato: `https://your-graph-database-account.documents.azure.com:443/`
`AuthorizationKey`|Essa é a Chave Primária ou Secundária listada em sua conta do Azure Cosmos DB. Saiba mais sobre [Como proteger o acesso aos dados do Azure Cosmos DB](./secure-access-to-data.md#primary-keys)
`DatabaseName`, `CollectionName`|Esses são os **nomes da coleção e banco de dados de destino**. Quando `ShouldCleanupOnStart` for definido como `true`, esses valores, juntamente com `CollectionThroughput`, serão usados para eliminá-los e criar um novo banco de dados e coleção. Da mesma forma, se `ShouldCleanupOnFinish` estiver definido como `true`, eles serão usados para excluir o banco de dados assim que a ingestão terminar. Observe que a coleção de destino deve ser **uma coleção ilimitada**.
`CollectionThroughput`|Esse método é usado para criar uma nova coleção se a opção `ShouldCleanupOnStart` estiver configurada como `true`.
`ShouldCleanupOnStart`|Ele eliminará as coleções e a conta do banco de dados antes da execução do programa e, em seguida, criará novas com os valores `DatabaseName`, `CollectionName` e `CollectionThroughput`.
`ShouldCleanupOnFinish`|Após a execução do programa, o método removerá as coleções e a conta do banco de dados com os valores `DatabaseName` e `CollectionName` especificados.
`NumberOfDocumentsToImport`|Isso determinará o número de vértices e bordas de teste que serão gerados no exemplo. Esse número será aplicado aos vértices e bordas.
`NumberOfBatches`|Isso determinará o número de vértices e bordas de teste que serão gerados no exemplo. Esse número será aplicado aos vértices e bordas.
`CollectionPartitionKey`|Isso será usado para criar os vértices e bordas de teste, onde essa propriedade será atribuída automaticamente. Isso também será usado ao recriar o banco de dados e as coleções, se a opção `ShouldCleanupOnStart` estiver configurada como `true`.

### <a name="run-the-sample-application"></a>Executar o aplicativo de exemplo

1. Adicione os parâmetros de configuração específicos do banco de dados em `App.config`. Isso será usado para criar uma instância de DocumentClient. Se o banco de dados e o contêiner ainda não tiverem sido criados, eles serão criados automaticamente.
2. Execute o aplicativo. Isso irá chamar `BulkImportAsync` duas vezes, uma vez para importar os vértices e outra para importar as bordas. Se qualquer um dos objetos gerar um erro quando forem inseridos, eles serão adicionados a `.\BadVertices.txt` ou `.\BadEdges.txt`.
3. Avalie os resultados consultando o banco de dados do gráfico. Se a opção `ShouldCleanupOnFinish` estiver definida como true, o banco de dados será automaticamente excluído.

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre os detalhes do pacote NuGet e notas de versão da biblioteca .NET do executor em massa, consulte [detalhes do SDK do executor em massa](sql-api-sdk-bulk-executor-dot-net.md). 
* Confira as [Dicas de desempenho](./bulk-executor-dot-net.md#performance-tips) para otimizar ainda mais o uso do bulk executor.
* Examine o [artigo de referência BulkExecutor.Graph](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.graph?preserve-view=true&view=azure-dotnet) para ver mais detalhes sobre as classes e métodos definidos nesse namespace.