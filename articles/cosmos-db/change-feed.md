---
title: "Trabalhando com o suporte ao feed de alterações no Azure Cosmos DB | Microsoft Docs"
description: "Use o suporte ao feed de alterações do Azure Cosmos DB para controlar as alterações nos documentos e executar o processamento baseado em eventos como gatilhos e manter os caches e sistemas de análise atualizados."
keywords: "feed de alteração"
services: cosmos-db
author: arramac
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: 2d7798db-857f-431a-b10f-3ccbc7d93b50
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: rest-api
ms.topic: article
ms.date: 08/15/2017
ms.author: arramac
ms.openlocfilehash: 16bd85065f77612ac342ae4a8b500e0c7fa2a078
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="working-with-the-change-feed-support-in-azure-cosmos-db"></a>Trabalhando com o suporte ao feed de alterações no Azure Cosmos DB
O [Azure Cosmos DB](../cosmos-db/introduction.md) é um serviço de banco de dados rápido, flexível e replicado globalmente, usado para armazenar grandes volumes de dados transacionais e operacionais com latência previsível de milissegundos de dígito único para leituras e gravações. Isso o torna adequado para IoT, jogos, varejo e aplicativos de log operacional. Um padrão de design comum nesses aplicativos é controlar as alterações feitas nos dados do Azure Cosmos DB e atualizar exibições materializadas, executar análise em tempo real, arquivar dados em armazenamento frio e disparar notificações em determinados eventos de acordo com essas alterações. O **suporte ao feed de alterações** do Azure Cosmos DB permite que você crie soluções eficientes e escalonáveis para cada um desses padrões.

Com suporte ao feed de alterações, o Azure Cosmos DB fornece uma lista classificada de documentos em uma coleção do Azure Cosmos DB na ordem em que eles foram modificados. Este feed pode ser usado para ouvir as modificações de dados dentro da coleção e executar ações como:

* Disparar uma chamada a uma API quando um documento é inserido ou modificado
* Executar o processamento em tempo real (fluxo) em atualizações
* Sincronizar dados com um cache, o mecanismo de pesquisa ou o data warehouse

As alterações no Azure Cosmos DB são persistentes e podem ser processadas de forma assíncrona e distribuídas entre um ou mais consumidores para processamento paralelo. Vamos examinar as APIs do feed de alterações e como usá-las para criar aplicativos escalonáveis em tempo real. Este artigo mostra como trabalhar com o feed de alterações do Azure Cosmos DB e a API do DocumentDB. 

![Usando o feed de alterações do Azure Cosmos DB para capacitar a análise em tempo real e cenários de computação orientada a eventos](./media/change-feed/changefeedoverview.png)

> [!NOTE]
> O suporte ao feed de alterações é fornecido apenas para a API do DocumentDB neste momento. A API do Graph e a API de Tabela não têm suporte no momento.

## <a name="use-cases-and-scenarios"></a>Cenários e casos de uso
O feed de alterações permite o processamento eficiente de grandes conjuntos de dados com um alto volume de gravações e oferece uma alternativa à consulta de conjuntos de dados inteiros para identificar o que foi alterado. Por exemplo, você pode executar as seguintes etapas de forma eficiente:

* Atualize um cache, índice de pesquisa ou data warehouse com os dados armazenados no Azure Cosmos DB.
* Implementar camadas e arquivamento de dados em nível do aplicativo, ou seja, armazenar “dados quentes” no Azure Cosmos DB e arquivar “dados frios” no [Armazenamento de Blobs do Azure](../storage/common/storage-introduction.md) ou no [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md).
* Implementar a análise em lote nos dados usando o [Apache Hadoop](run-hadoop-with-hdinsight.md).
* Implementar [pipelines lambda no Azure](https://blogs.technet.microsoft.com/msuspartner/2016/01/27/azure-partner-community-big-data-advanced-analytics-and-lambda-architecture/) com o Azure Cosmos DB. O Azure Cosmos DB fornece uma solução de banco de dados escalonável que pode manipular a ingestão e a consulta, além de implementar arquiteturas lambda com baixo TCO. 
* Realize migrações com zero tempo de inatividade para outra conta do Azure Cosmos DB com um esquema de particionamento diferente.

**Pipelines lambda com o Azure Cosmos DB para ingestão e consulta:**

![Pipeline lambda baseado no Azure Cosmos DB para ingestão e consulta](./media/change-feed/lambda.png)

Use o Azure Cosmos DB para receber e armazenar dados de evento de dispositivos, sensores, infraestrutura e aplicativos, além de processar esses eventos em tempo real com o [Stream Analytics do Azure](../stream-analytics/stream-analytics-documentdb-output.md), [Apache Storm](../hdinsight/hdinsight-storm-overview.md) ou [Apache Spark](../hdinsight/hdinsight-apache-spark-overview.md). 

Em aplicativos Web e móveis [sem servidor](http://azure.com/serverless), você pode acompanhar eventos como alterações no perfil, nas preferências ou no local do cliente para disparar determinadas ações, como enviar notificações por push para seus dispositivos usando o [Azure Functions](../azure-functions/functions-bindings-documentdb.md) ou os [Serviços de Aplicativos](https://azure.microsoft.com/services/app-service/). Se você estiver usando o Azure Cosmos DB para criar um jogo, poderá, por exemplo, usar o feed de alterações para implementar placares em tempo real de acordo com as pontuações dos jogos concluídos.

## <a name="how-change-feed-works-in-azure-cosmos-db"></a>Como funciona o feed de alterações no Azure Cosmos DB
O Azure Cosmos DB fornece a capacidade de ler de forma incremental as atualizações feitas em uma coleção do Azure Cosmos DB. Este feed de alteração tem as seguintes propriedades:

* As alterações são persistentes no Azure Cosmos DB e podem ser processadas de forma assíncrona.
* As alterações em documentos em uma coleção ficam imediatamente disponíveis no feed de alteração.
* Cada alteração em um documento aparece exatamente uma vez no feed de alterações, e os clientes gerenciam a respectiva lógica do ponto de verificação. A biblioteca do processador do feed de alterações fornece ponto de verificação automático e semântica "pelo menos uma vez".
* Somente a alteração mais recente de um determinado documento é incluída no log de alterações. As alterações intermediárias podem não estar disponíveis.
* O feed de alteração é classificado por ordem de modificação em cada valor de chave de partição. Não há nenhuma garantia de ordem entre os valores de chave de partição.
* As alterações podem ser sincronizadas de qualquer ponto no tempo, ou seja, não há nenhum período de retenção de dados fixo para o qual haja alterações disponíveis.
* As alterações estão disponíveis em blocos de intervalos de chaves de partição. Esse recurso permite que as alterações de coleções grandes sejam processadas em paralelo por vários consumidores/servidores.
* Os aplicativos podem solicitar vários feeds de alteração simultaneamente na mesma coleção.

O feed de alterações do Azure Cosmos DB é habilitado por padrão para todas as contas. Use a [produtividade provisionada](request-units.md) em sua região de gravação ou em qualquer [região de leitura](distribute-data-globally.md) para ler o feed de alterações, assim como qualquer outra operação do Azure Cosmos DB. O feed de alteração inclui inserções e as operações de atualização feitas em documentos na coleção. Você pode capturar exclusões ao definir um sinalizador de "exclusão reversível" em seus documentos em vez de exclusões. Como alternativa, você pode definir um período de validade finito para seus documentos por meio do [recurso TTL](time-to-live.md), por exemplo, de 24 horas, e usar o valor dessa propriedade para capturar as exclusões. Com essa solução, você precisa processar alterações em um intervalo de tempo menor do que o período de expiração do TTL. O feed de alteração está disponível para cada intervalo de chaves de partição dentro da coleção de documentos e, portanto, pode ser distribuído entre um ou mais consumidores para processamento paralelo. 

![Processamento distribuído do feed de alterações do Azure Cosmos DB](./media/change-feed/changefeedvisual.png)

Você tem algumas opções para implementar um feed de alterações em seu código de cliente. As seções a seguir descrevem como implementar o feed de alterações usando a API REST do Azure Cosmos DB e os SDKs do DocumentDB. No entanto, para aplicativos .NET, é recomendável usar a nova [Biblioteca do processador de feed de alterações](#change-feed-processor) para processar eventos do feed de alterações, pois ela simplifica a leitura de alterações em partições e habilita vários threads trabalhando em paralelo. 

## <a id="rest-apis"></a>Trabalhando com a API REST e os SDKs do DocumentDB
O Azure Cosmos DB fornece contêineres elásticos de armazenamento e produtividade chamados **coleções**. Os dados em coleções são logicamente agrupados usando [chaves de partição](partition-data.md) para obtenção de escalabilidade e desempenho. O Azure Cosmos DB fornece várias APIs para acessar esses dados, incluindo pesquisa por ID (Read/Get), consulta e feeds de leitura (verificações). O feed de alterações pode ser obtido populando dois novos cabeçalhos de solicitação na API `ReadDocumentFeed` do DocumentDB e pode ser processado em paralelo em intervalos de chaves de partição.

### <a name="readdocumentfeed-api"></a>API do ReadDocumentFeed
Vamos examinar o funcionamento do ReadDocumentFeed. O Azure Cosmos DB dá suporte à leitura de um feed de documentos em uma coleção por meio da API `ReadDocumentFeed`. Por exemplo, a solicitação a seguir retorna uma página de documentos na coleção `serverlogs`. 

    GET https://mydocumentdb.documents.azure.com/dbs/smalldb/colls/serverlogs HTTP/1.1
    x-ms-date: Tue, 22 Nov 2016 17:05:14 GMT
    authorization: type%3dmaster%26ver%3d1.0%26sig%3dgo7JEogZDn6ritWhwc5hX%2fNTV4wwM1u9V2Is1H4%2bDRg%3d
    Cache-Control: no-cache
    x-ms-consistency-level: Strong
    User-Agent: Microsoft.Azure.Documents.Client/1.10.27.5
    x-ms-version: 2016-07-11
    Accept: application/json
    Host: mydocumentdb.documents.azure.com

Os resultados podem ser limitados por meio do cabeçalho `x-ms-max-item-count` e as leituras podem ser retomadas reenviando a solicitação com um cabeçalho `x-ms-continuation` retornado na resposta anterior. Quando executado de um único cliente, `ReadDocumentFeed` itera resultados entre partições em série. 

**Leitura serial de feed de documento**

Você também pode recuperar o feed de documentos usando um dos [SDKs do Azure Cosmos DB](documentdb-sdk-dotnet.md) compatíveis. Por exemplo, o trecho a seguir mostra como usar o [método ReadDocumentFeedAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readdocumentfeedasync?view=azure-dotnet) no .NET.

```csharp
FeedResponse<dynamic> feedResponse = null;
do
{
    feedResponse = await client.ReadDocumentFeedAsync(collection, new FeedOptions { MaxItemCount = -1 });
}
while (feedResponse.ResponseContinuation != null);
```

### <a name="distributed-execution-of-readdocumentfeed"></a>Execução distribuída do ReadDocumentFeed
Para coleções que contêm terabytes de dados ou mais, ou um grande volume de atualizações de ingestão, a execução serial de feed de leitura de um único computador cliente pode não ser prática. Para dar suporte a esses cenários de Big Data, o Azure Cosmos DB fornece APIs para distribuir chamadas `ReadDocumentFeed` de forma transparente entre vários leitores/consumidores cliente. 

**Feed de Documento de Leitura Distribuída**

Para fornecer o processamento escalonável das alterações incrementais, o Azure Cosmos DB dá suporte a um modelo de expansão para a API do feed de alterações de acordo com intervalos de chaves de partição.

* Você pode obter uma lista de intervalos de chaves de partição para uma coleção que esteja executando uma chamada `ReadPartitionKeyRanges`. 
* Para cada intervalo de chaves de partição, você pode realizar uma `ReadDocumentFeed` para ler documentos com chaves de partição dentro do intervalo.

### <a name="retrieving-partition-key-ranges-for-a-collection"></a>Recuperação de intervalos de chaves de partição para uma coleção
Você pode recuperar os intervalos de chaves de partição solicitando o recurso `pkranges` em uma coleção. Por exemplo, a solicitação a seguir recupera a lista de intervalos de chaves de partição para a coleção `serverlogs`:

    GET https://querydemo.documents.azure.com/dbs/bigdb/colls/serverlogs/pkranges HTTP/1.1
    x-ms-date: Tue, 15 Nov 2016 07:26:51 GMT
    authorization: type%3dmaster%26ver%3d1.0%26sig%3dEConYmRgDExu6q%2bZ8GjfUGOH0AcOx%2behkancw3LsGQ8%3d
    x-ms-consistency-level: Session
    x-ms-version: 2016-07-11
    Accept: application/json
    Host: querydemo.documents.azure.com

Essa solicitação retorna a seguinte resposta com metadados sobre os intervalos de chaves de partição:

    HTTP/1.1 200 Ok
    Content-Type: application/json
    x-ms-item-count: 25
    x-ms-schemaversion: 1.1
    Date: Tue, 15 Nov 2016 07:26:51 GMT

    {
       "_rid":"qYcAAPEvJBQ=",
       "PartitionKeyRanges":[
          {
             "_rid":"qYcAAPEvJBQCAAAAAAAAUA==",
             "id":"0",
             "_etag":"\"00002800-0000-0000-0000-580ac4ea0000\"",
             "minInclusive":"",
             "maxExclusive":"05C1CFFFFFFFF8",
             "_self":"dbs\/qYcAAA==\/colls\/qYcAAPEvJBQ=\/pkranges\/qYcAAPEvJBQCAAAAAAAAUA==\/",
             "_ts":1477100776
          },
          ...
       ],
       "_count": 25
    }


**Propriedades do intervalo de chaves de partição**: cada intervalo de chaves de partição inclui as propriedades de metadados da seguinte tabela:

<table>
    <tr>
        <th>Nome do cabeçalho</th>
        <th>Descrição</th>
    </tr>
    <tr>
        <td>ID</td>
        <td>
            <p>A ID para o intervalo de chaves de partição. É uma ID estável e exclusiva dentro de cada coleção.</p>
            <p>Deve ser usada na seguinte chamada para ler as alterações por intervalo de chaves de partição.</p>
        </td>
    </tr>
    <tr>
        <td>maxExclusive</td>
        <td>O valor de hash de chave de partição máxima para o intervalo de chaves de partição. Para uso interno.</td>
    </tr>
    <tr>
        <td>minInclusive</td>
        <td>O valor de hash da chave de partição mínimo para o intervalo de chaves de partição. Para uso interno.</td>
    </tr>       
</table>

Faça isso usando um dos [SDKs do Azure Cosmos DB](documentdb-sdk-dotnet.md) com suporte. Por exemplo, o trecho a seguir mostra como recuperar intervalos de chaves de partição no .NET, usando o método [ReadPartitionKeyRangeFeedAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readpartitionkeyrangefeedasync?view=azure-dotnet).

```csharp
string pkRangesResponseContinuation = null;
List<PartitionKeyRange> partitionKeyRanges = new List<PartitionKeyRange>();

do
{
    FeedResponse<PartitionKeyRange> pkRangesResponse = await client.ReadPartitionKeyRangeFeedAsync(
        collectionUri, 
        new FeedOptions { RequestContinuation = pkRangesResponseContinuation });

    partitionKeyRanges.AddRange(pkRangesResponse);
    pkRangesResponseContinuation = pkRangesResponse.ResponseContinuation;
}
while (pkRangesResponseContinuation != null);
```

O Azure Cosmos DB dá suporte à recuperação de documentos por intervalo de chaves de partição definindo o cabeçalho `x-ms-documentdb-partitionkeyrangeid` opcional. 

### <a name="performing-an-incremental-readdocumentfeed"></a>Execução de um ReadDocumentFeed incremental
O ReadDocumentFeed dá suporte aos seguintes cenários/tarefas de processamento incremental de alterações em coleções do Azure Cosmos DB:

* Leia todas as alterações para documentos desde o início, ou seja, desde a criação da coleção.
* Leia todas as alterações para atualizações futuras nos documentos a partir da hora atual ou todas as alterações desde a hora especificada por um usuário.
* Leia todas as alterações feitas em documentos de uma versão lógica da coleção (ETag). É possível criar pontos de verificação de seus clientes com base em ETag retornado de solicitações de feed de leitura incrementais.

As alterações incluem inserções e atualizações em documentos. Para capturar as exclusões, você deve usar uma propriedade de "exclusão reversível" em documentos ou usar a [propriedade TTL interna](time-to-live.md) para sinalizar uma exclusão pendente no feed de alteração.

A tabela a seguir lista os [cabeçalhos de solicitação](/rest/api/documentdb/common-documentdb-rest-request-headers.md) e de [resposta](/rest/api/documentdb/common-documentdb-rest-response-headers.md) para operações do ReadDocumentFeed.

**Cabeçalhos de solicitação para ReadDocumentFeed incremental**:

<table>
    <tr>
        <th>Nome do cabeçalho</th>
        <th>Descrição</th>
    </tr>
    <tr>
        <td>A-IM</td>
        <td>Deve ser definido como "feed Incremental" ou omitido</td>
    </tr>
    <tr>
        <td>If-None-Match</td>
        <td>
            <p>Nenhum cabeçalho: retorna todas as alterações desde o início (criação de coleção)</p>
            <p>"*": retorna todas as novas alterações para dados na coleção</p>           
            <p>&lt;etag&gt;: se definida como uma ETag de coleção, retorna todas as alterações feitas desde o carimbo de data/hora lógico</p>
        </td>
    </tr>
    <tr>    
        <td>If-Modified-Since</td> 
        <td>Formato de hora RFC 1123; ignorado se If-None-Match for especificado</td> 
    </tr> 
    <tr>
        <td>x-ms-documentdb-partitionkeyrangeid</td>
        <td>A ID de intervalo de chaves de partição de leitura de dados.</td>
    </tr>
</table>

**Cabeçalhos de resposta para ReadDocumentFeed incremental**:

<table> <tr>
        <th>Nome do cabeçalho</th>
        <th>Descrição</th>
    </tr>
    <tr>
        <td>etag</td>
        <td>
            <p>O número de sequência lógica (LSN) do último documento retornado na resposta.</p>
            <p>O ReadDocumentFeed incremental pode ser retomado por meio do reenvio desse valor em If-None-Match.</p>
        </td>
    </tr>
</table>

Aqui está um exemplo de solicitação para retornar todas as alterações incrementais na coleção da versão/ETag lógica `28535` e intervalo de chaves de partição = `16`:

    GET https://mydocumentdb.documents.azure.com/dbs/bigdb/colls/bigcoll/docs HTTP/1.1
    x-ms-max-item-count: 1
    If-None-Match: "28535"
    A-IM: Incremental feed
    x-ms-documentdb-partitionkeyrangeid: 16
    x-ms-date: Tue, 22 Nov 2016 20:43:01 GMT
    authorization: type%3dmaster%26ver%3d1.0%26sig%3dzdpL2QQ8TCfiNbW%2fEcT88JHNvWeCgDA8gWeRZ%2btfN5o%3d
    x-ms-version: 2016-07-11
    Accept: application/json
    Host: mydocumentdb.documents.azure.com

As alterações são ordenadas por tempo em cada valor de chave de partição dentro do intervalo de chaves de partição. Não há nenhuma garantia de ordem entre os valores de chave de partição. Se houver mais resultados do que pode caber em uma única página, você poderá ler a próxima página de resultados reenviando a solicitação com o cabeçalho `If-None-Match` com valor igual a `etag` da resposta anterior. Se vários documentos tiverem sido inseridos ou atualizados transacionalmente dentro de um procedimento armazenado ou gatilho, serão todos retornados na mesma página de resposta.

> [!NOTE]
> Com o feed de alterações, você poderá obter mais itens retornados em uma página que o especificado em `x-ms-max-item-count`, no caso de vários documentos inseridos ou atualizados dentro de procedimentos armazenados ou gatilhos. 

Ao usar o SDK do .NET (1.17.0), defina o campo `StartTime` em `ChangeFeedOptions` para retornar diretamente documentos alterados desde `StartTime` ao chamar `CreateDocumentChangeFeedQuery`. Ao especificar `If-Modified-Since` usando a API REST, sua solicitação não retornará os documentos em si, mas o token de continuação ou `etag` no cabeçalho da resposta. Para retornar os documentos modificados na hora especificada, o token de continuação `etag` deve ser usado na próxima solicitação com `If-None-Match` para retornar os documentos reais. 

O SDK do .NET fornece as classes auxiliares [CreateDocumentChangeFeedQuery](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentchangefeedquery?view=azure-dotnet) e [ChangeFeedOptions](/dotnet/api/microsoft.azure.documents.client.changefeedoptions?view=azure-dotnet) para acessar as alterações feitas em uma coleção. O trecho a seguir mostra como recuperar todas as alterações desde o início usando o SDK do .NET de um único cliente.

```csharp
private async Task<Dictionary<string, string>> GetChanges(
    DocumentClient client,
    string collection,
    Dictionary<string, string> checkpoints)
{
    string pkRangesResponseContinuation = null;
    List<PartitionKeyRange> partitionKeyRanges = new List<PartitionKeyRange>();

    do
    {
        FeedResponse<PartitionKeyRange> pkRangesResponse = await client.ReadPartitionKeyRangeFeedAsync(
            collectionUri, 
            new FeedOptions { RequestContinuation = pkRangesResponseContinuation });

        partitionKeyRanges.AddRange(pkRangesResponse);
        pkRangesResponseContinuation = pkRangesResponse.ResponseContinuation;
    }
    while (pkRangesResponseContinuation != null);

    foreach (PartitionKeyRange pkRange in partitionKeyRanges)
    {
        string continuation = null;
        checkpoints.TryGetValue(pkRange.Id, out continuation);

        IDocumentQuery<Document> query = client.CreateDocumentChangeFeedQuery(
            collection,
            new ChangeFeedOptions
            {
                PartitionKeyRangeId = pkRange.Id,
                StartFromBeginning = true,
                RequestContinuation = continuation,
                MaxItemCount = 1
            });

        while (query.HasMoreResults)
        {
            FeedResponse<DeviceReading> readChangesResponse = await query.ExecuteNextAsync<DeviceReading>();

            foreach (DeviceReading changedDocument in readChangesResponse)
            {
                Console.WriteLine(changedDocument.Id);
            }

            checkpoints[pkRange.Id] = readChangesResponse.ResponseContinuation;
        }
    }

    return checkpoints;
}
```
E o trecho a seguir mostra como processar alterações em tempo real com o Azure Cosmos DB usando o suporte ao feed de alterações e a função anterior. A primeira chamada retorna todos os documentos na coleção, e o segundo retorna apenas os dois documentos criados desde o último ponto de verificação.

```csharp
// Returns all documents in the collection.
Dictionary<string, string> checkpoints = await GetChanges(client, collection, new Dictionary<string, string>());

await client.CreateDocumentAsync(collection, new DeviceReading { DeviceId = "xsensr-201", MetricType = "Temperature", Unit = "Celsius", MetricValue = 1000 });
await client.CreateDocumentAsync(collection, new DeviceReading { DeviceId = "xsensr-212", MetricType = "Pressure", Unit = "psi", MetricValue = 1000 });

// Returns only the two documents created above.
checkpoints = await GetChanges(client, collection, checkpoints);
```

Você também pode filtrar o feed de alteração usando lógica de cliente para processar eventos seletivamente. Por exemplo, aqui está um trecho que usa o LINQ no lado do cliente para processar somente os eventos de alteração de temperatura de sensores de dispositivo.

```csharp
FeedResponse<DeviceReading> readChangesResponse = await query.ExecuteNextAsync<DeviceReading>;

foreach (DeviceReading changedDocument in 
    readChangesResponse.AsEnumerable().Where(d => d.MetricType == "Temperature" && d.MetricValue > 1000L))
{
    // trigger an action, like call an API
}
```

## <a id="change-feed-processor"></a>Biblioteca do processador do feed de alterações
Outra opção é usar a [Biblioteca do processador do feed de alterações do Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/documentdb-sdk-dotnet-changefeed), que pode ajudá-lo a distribuir facilmente o processamento de eventos de um feed de alterações entre vários consumidores. A biblioteca é excelente para a criação de leitores de feed de alterações na plataforma .NET. Alguns fluxos de trabalho que seriam simplificados usando a Biblioteca do processador do feed de alterações comparados com métodos incluídos em outros SDKs do Cosmos DB incluem: 

* Efetuar pull de atualizações de feed de alterações quando os dados são armazenados em várias partições
* Mover ou replicar dados de uma coleção para outra
* Execução paralela de ações disparadas por atualizações aos dados e ao feed de alterações 

Embora o uso das APIs nos SDKs do Cosmos forneça acesso preciso às atualizações do feed de alterações em cada partição, o uso da Biblioteca do processador do feed de alterações simplifica a leitura de alterações em partições e em vários threads trabalhando em paralelo. Em vez de ler manualmente as alterações de cada contêiner e salvar um token de continuação para cada partição, o Processador do feed de alterações gerencia automaticamente a leitura de alterações em partições usando um mecanismo de concessão.

A biblioteca está disponível como um Pacote NuGet: [Microsoft.Azure.Documents.ChangeFeedProcessor](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/) e do código-fonte como um [exemplo](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/ChangeFeedProcessor) do Github. 

### <a name="understanding-change-feed-processor-library"></a>Noções básicas sobre a biblioteca do processador do feed de alterações 

Há quatro componentes principais da implementação do Processador do feed de alterações: a coleção monitorada, a coleção de concessão, o host de processador e os consumidores. 

**Coleção monitorada:** a coleção monitorada são os dados dos quais o feed de alterações é gerado. Todas as inserções e alterações à coleção monitorada são refletidas no feed de alterações da coleção. 

**Coleção de concessão:** a coleção de concessão coordena o processamento do feed de alterações em vários trabalhos. Uma coleção separada é usada para armazenar as concessões com uma concessão por partição. É vantajoso armazenar essa coleção de concessão em uma conta diferente, com a região de gravação mais próxima do local em que o Processador do feed de alterações está em execução. Um objeto de concessão contém os seguintes atributos: 
* Proprietário: especifica o host que é proprietário da concessão
* Continuação: especifica a posição (token de continuação) de uma determinada partição no feed de alterações
* Carimbo de data/hora: última vez em que a concessão foi atualizada; o carimbo de data/hora pode ser usado para verificar se a concessão está expirada 

**Host de processador:** cada host determina quantas partições devem ser processadas com base no número de instâncias de hosts com concessões ativas. 
1.  Quando um host é iniciado, ele adquire concessões para balancear a carga de trabalho entre todos os hosts. Um host renova concessões periodicamente para que as concessões permaneçam ativas. 
2.  Um host realiza pontos de verificação do último token de continuação em relação à respectiva concessão para cada leitura. Para garantir a segurança de simultaneidade, um host verifica o Etag de cada atualização de concessão. Também há suporte para outras estratégias de ponto de verificação.  
3.  Durante o desligamento, um host libera todas as concessões, mas mantém as informações de continuação, para que possa retomar a leitura do ponto de verificação armazenado mais tarde. 

Neste momento, o número de hosts não pode ser maior que o número de partições (concessões).

**Consumidores:** consumidores ou trabalhos, são os threads que realizam o processamento do feed de alterações iniciado por cada host. Cada host de processador pode ter vários consumidores. Cada consumidor lê o feed de alterações da partição à qual ele é atribuído e notifica o respectivo host sobre as alterações e as concessões expiradas.

Para compreender melhor como esses quatro elementos do Processador do feed de alterações funcionam em conjunto, vamos examinar um exemplo no diagrama a seguir. A coleção monitorada armazena documentos e usa a "cidade" como a chave de partição. Podemos ver que a partição azul contém documentos com o campo "cidade" de "A a E" e assim por diante. Há dois hosts, cada um com dois consumidores lendo das quatro partições em paralelo. As setas mostram os consumidores lendo de um ponto específico no feed de alterações. Na primeira partição, o azul mais escuro representa as alterações não lidas enquanto que o azul claro representa as alterações já lidas no feed de alterações. Os hosts de usam a coleção de concessão para armazenar um valor de "continuação" a fim de manter um registro da posição atual de leitura para cada consumidor. 

![Usando o host do processador do feed de alterações do Azure Cosmos DB](./media/change-feed/changefeedprocessornew.png)

### <a name="using-change-feed-processor-library"></a>Usando a Biblioteca do processador do feed de alterações 
A seção a seguir explica como usar a Biblioteca do processador do feed de alterações no contexto de replicar alterações de uma coleção de origem para uma coleção de destino. Aqui, a coleção de origem é a coleção monitorada no Processador do feed de alterações. 

**Instalar e incluir o Pacote NuGet do Processador do feed de alterações** 

Antes de instalar o Pacote NuGet do Processador do feed de alterações, instale: 
* Microsoft.Azure.DocumentDB, versão 1.13.1 ou superior 
* Newtonsoft.Json, versão 9.0.1 ou superior. Instale `Microsoft.Azure.DocumentDB.ChangeFeedProcessor` e o inclua como uma referência.

**Criar uma coleção monitorada, uma coleção de destino e uma coleção de concessão** 

Para usar a Biblioteca do processador do feed de alterações, a coleção de concessão precisa ser criada antes de executar o(s) host(s) do processador. Novamente, é recomendável armazenar uma coleção de concessão em uma conta diferente, com a região de gravação mais próxima do local em que o Processador do feed de alterações está em execução. Neste exemplo de movimentação de dados, precisamos criar a coleção de destino antes de executar o host do Processador do feed de alterações. No código de exemplo, chamamos um método auxiliar para criar a coleção monitorada, a coleção de concessão e a de destino, caso ainda não existam. 

> [!WARNING]
> Criar uma coleção tem implicações de preços, pois você está reservando a taxa de transferência para o aplicativo se comunicar com o Azure Cosmos DB. Para obter mais detalhes, visite a [página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/)
> 
> 

*Criando um host de processador*

A classe `ChangeFeedProcessorHost` fornece um ambiente de tempo de execução seguro, thread-safe e de vários processos para implementações do processador de eventos, que também fornece gerenciamento de concessão de ponto de verificação e partição. Para usar a classe `ChangeFeedProcessorHost`, você pode implementar `IChangeFeedObserver`. Essa interface contém três métodos:

* `OpenAsync`: essa função é chamada quando o observador do feed de alterações é aberto. Ela pode ser modificada para realizar uma ação específica quando o consumidor/observador for aberto.  
* `CloseAsync`: essa função é chamada quando o observador do feed de alterações é terminado. Ela pode ser modificada para realizar uma ação específica quando o consumidor/observador for fechado.  
* `ProcessChangesAsync`: essa função é chamada quando novas alterações de documento estão disponíveis no feed de alterações. Ela pode ser modificada para realizar uma ação específica após cada atualização do feed de alterações.  

Em nosso exemplo, implementamos a interface `IChangeFeedObserver` por meio da classe `DocumentFeedObserver`. Aqui, a função `ProcessChangesAsync` faz upsert (atualiza) de um documento do feed de alterações na coleção de destino. Este exemplo é útil para mover dados de uma coleção para outra, a fim de alterar a chave de partição de um conjunto de dados. 

*Executando o Host de processador*

Antes de começar o processamento de eventos, as opções do feed de alterações e as opções de host do feed de alterações podem ser personalizadas. 
```csharp
    // Customizable change feed option and host options 
    ChangeFeedOptions feedOptions = new ChangeFeedOptions();

    // ie customize StartFromBeginning so change feed reads from beginning
    // can customize MaxItemCount, PartitonKeyRangeId, RequestContinuation, SessionToken and StartFromBeginning
    feedOptions.StartFromBeginning = true;

    ChangeFeedHostOptions feedHostOptions = new ChangeFeedHostOptions();

    // ie. customizing lease renewal interval to 15 seconds
    // can customize LeaseRenewInterval, LeaseAcquireInterval, LeaseExpirationInterval, FeedPollDelay 
    feedHostOptions.LeaseRenewInterval = TimeSpan.FromSeconds(15);

```
Os campos específicos que podem ser personalizados estão resumidos nas tabelas a seguir. 

**Opções do feed de alterações**:
<table>
    <tr>
        <th>Nome da Propriedade</th>
        <th>Descrição</th>
    </tr>
    <tr>
        <td>MaxItemCount</td>
        <td>Obtém ou define o número máximo de itens a serem retornados na operação de enumeração no serviço de banco de dados do Azure Cosmos DB.</td>
    </tr>
    <tr>
        <td>PartitionKeyRangeId</td>
        <td>Obtém ou define a ID do intervalo de chave de partição da solicitação atual no serviço de banco de dados do Azure Cosmos DB.</td>
    </tr>
    <tr>
        <td>RequestContinuation</td>
        <td>Obtém ou define o token de continuação de solicitação no serviço de banco de dados do Azure Cosmos DB.</td>
    </tr>
        <tr>
        <td>SessionToken</td>
        <td>Obtém ou define o token de sessão para ser usado com a consistência de sessão no serviço de banco de dados do Azure Cosmos DB.</td>
    </tr>
        <tr>
        <td>StartFromBeginning</td>
        <td>Obtém ou define se o feed de alterações no serviço de banco de dados do Azure Cosmos DB deve começar do início (true) ou do local atual (false). Por padrão, ele inicia do local atual (false).</td>
    </tr>
</table>

**Opções do host do feed de alterações**:
<table>
    <tr>
        <th>Nome da Propriedade</th>
        <th>Tipo</th>
        <th>Descrição</th>
    </tr>
    <tr>
        <td>LeaseRenewInterval</td>
        <td>TimeSpan</td>
        <td>O intervalo de todas as concessões para partições mantidas atualmente pela instância ChangeFeedEventHost.</td>
    </tr>
    <tr>
        <td>LeaseAcquireInterval</td>
        <td>TimeSpan</td>
        <td>O intervalo para disparar uma tarefa para computar se as partições são distribuídas uniformemente entre as instâncias de host conhecidas.</td>
    </tr>
    <tr>
        <td>LeaseExpirationInterval</td>
        <td>TimeSpan</td>
        <td>O intervalo para o qual a concessão é tomada em uma concessão que representa uma partição. Se a concessão não for renovada dentro deste intervalo, ela será expirada e a propriedade da partição será movida para outra instância de ChangeFeedEventHost.</td>
    </tr>
    <tr>
        <td>FeedPollDelay</td>
        <td>TimeSpan</td>
        <td>O atraso entre a sondagem de uma partição quanto a novas alterações no feed, depois que todas as alterações atuais forem descarregadas.</td>
    </tr>
    <tr>
        <td>CheckpointFrequency</td>
        <td>CheckpointFrequency</td>
        <td>A frequência das concessões de ponto de verificação.</td>
    </tr>
    <tr>
        <td>MinPartitionCount</td>
        <td>int</td>
        <td>A contagem de partição mínima para o host.</td>
    </tr>
    <tr>
        <td>MaxPartitionCount</td>
        <td>int</td>
        <td>O número máximo de partições a que o host pode servir.</td>
    </tr>
    <tr>
        <td>DiscardExistingLeases</td>
        <td>Bool</td>
        <td>Se todas as concessões existentes no início do host devem ser excluídas e se o host deve começar do zero.</td>
    </tr>
</table>


Para iniciar o processamento de eventos, crie uma instância do `ChangeFeedProcessorHost`, fornecendo os parâmetros apropriados para a sua coleção do Azure Cosmos DB. Em seguida, chame `RegisterObserverAsync` para registrar sua implementação `IChangeFeedObserver` (DocumentFeedObserver neste exemplo) com o tempo de execução. Neste ponto, o host tenta adquirir uma concessão em todos os intervalos de chaves de partição na coleção do Azure Cosmos DB usando um algoritmo "greedy". Essas concessões duram por determinado período e, em seguida, devem ser renovadas. Como novos nós, as instâncias de trabalho, nesse caso, ficam online, colocam reservas de concessão e, com o tempo, a carga é trocada entre os nós, à medida que cada host tenta adquirir mais concessões. 

No código de exemplo, usamos uma classe factory (DocumentFeedObserverFactory.cs) para criar um observador e a `RegistObserverFactoryAsync` para registrar o observador. 

```csharp
using (DocumentClient destClient = new DocumentClient(destCollInfo.Uri, destCollInfo.MasterKey))
    {
        DocumentFeedObserverFactory docObserverFactory = new DocumentFeedObserverFactory(destClient, destCollInfo);
        ChangeFeedEventHost host = new ChangeFeedEventHost(hostName, documentCollectionLocation, leaseCollectionLocation, feedOptions, feedHostOptions);

        await host.RegisterObserverFactoryAsync(docObserverFactory);

        Console.WriteLine("Running... Press enter to stop.");
        Console.ReadLine();

        await host.UnregisterObserversAsync();
    }
```
Ao longo do tempo, é estabelecido um equilíbrio. Essa funcionalidade dinâmica permite que o dimensionamento automático baseado em CPU seja aplicado aos consumidores tanto para escalar quanto para reduzir verticalmente. Se as alterações estiverem disponíveis no Azure Cosmos DB a uma taxa mais rápida do que os consumidores podem processar, o aumento de CPU nos consumidores poderá ser usado para causar uma escala automática na contagem de instâncias de trabalho.

## <a name="next-steps"></a>Próximas etapas
* Experimente as [amostras de código do Feed de alterações do Azure Cosmos DB no GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/ChangeFeed)
* Introdução à codificação com os [SDKs do Azure Cosmos DB](documentdb-sdk-dotnet.md) ou a [API REST](/rest/api/documentdb/).
