---
title: Modelo de pull do feed de alterações
description: Saiba como usar o modelo de pull do feed de alterações do Azure Cosmos DB para ler o feed de alterações e as diferenças entre o modelo de pull e o processador do feed de alterações
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 03/10/2021
ms.reviewer: sngun
ms.openlocfilehash: 9279dddc92629b17a2a73f3a41fe261d322d677e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103463673"
---
# <a name="change-feed-pull-model-in-azure-cosmos-db"></a>Alterar o modelo de pull do feed de alterações no Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Com o modelo de pull do feed de alterações, você pode consumir o feed de alterações do Azure Cosmos DB em seu próprio ritmo. Do mesmo modo já é possível fazer com o [processador do feed de alterações](change-feed-processor.md), você pode usar o modelo de pull do feed de alterações para paralelizar o processamento de alterações entre vários consumidores de feed de alterações.

> [!NOTE]
> O modelo de pull do feed de alterações está atualmente somente em [versão prévia no SDK do .NET do Azure Cosmos DB](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/3.17.0-preview). A versão prévia ainda não está disponível para outras versões do SDK.

## <a name="comparing-with-change-feed-processor"></a>Comparação com o processador do feed de alterações

Muitos cenários podem processar o feed de alterações usando o [processador do feed de alterações](change-feed-processor.md) ou o modelo de pull. Os tokens de continuação do modelo de pull e o contêiner de concessão do processador de feed de alterações são "indicadores" para o último item processado (ou lote de itens) no feed de alterações.

No entanto, não é possível converter tokens de continuação em um contêiner de concessão (ou vice-versa).

> [!NOTE]
> Na maioria dos casos, quando você precisa ler o feed de alterações, a opção mais simples é usar o [processador do feed de alterações](change-feed-processor.md).

Você deve considerar o uso do modelo de pull nestes cenários:

- Ler alterações de uma chave de partição específica
- Controlar o ritmo no qual seu cliente recebe alterações para processamento
- Executar uma única leitura dos dados existentes no feed de alterações (por exemplo, para fazer uma migração de dados)

Aqui estão algumas diferenças importantes entre o processador do feed de alterações e o modelo de pull:

|Recurso  | Alterar o processador de feed| Modelo de pull |
| --- | --- | --- |
| Acompanhar o ponto atual no processamento do feed de alterações | Concessão (armazenada em um contêiner do Azure Cosmos DB) | Token de continuação (armazenado na memória ou persistido manualmente) |
| Capacidade de reproduzir alterações passadas | Sim, com o modelo de push | Sim, com o modelo de pull|
| Sondagem para alterações futuras | Verifica automaticamente se há alterações com base no `WithPollInterval` especificado pelo usuário | Manual |
| Comportamento em que não há nenhuma nova alteração | Aguardar `WithPollInterval` e verificar automaticamente | É necessário capturar a exceção e verificar manualmente |
| Processar alterações de todo o contêiner | Sim, paralelizado automaticamente em vários threads/computador consumindo do mesmo contêiner| Sim, e paralelizado manualmente usando FeedTokens |
| Processar alterações de apenas uma chave de partição | Sem suporte | Sim|
| Nível de suporte | Disponível para o público geral | Visualização |

> [!NOTE]
> Ao contrário da leitura usando o processador do feed de alterações, você deve manipular explicitamente os casos em que não há nenhuma nova alteração. 

## <a name="consuming-an-entire-containers-changes"></a>Como consumir as alterações de um contêiner inteiro

Você pode criar um `FeedIterator` para processar o feed de alterações usando o modelo de pull. Ao criar inicialmente um `FeedIterator` , você deve especificar um valor necessário `ChangeFeedStartFrom` que consiste na posição inicial para ler as alterações, bem como o desejado `FeedRange` . O `FeedRange` é um intervalo de valores de chave de partição e especifica os itens que serão lidos do feed de alterações usando esse específico `FeedIterator` .

Opcionalmente, você pode especificar `ChangeFeedRequestOptions` para definir um `PageSizeHint` . O `PageSizeHint` é o número máximo de itens que serão retornados em uma única página.

O `FeedIterator` é fornecido em duas versões. Além dos exemplos abaixo que retornam objetos de entidade, você também pode obter a resposta com suporte a `Stream`. Os fluxos permitem que você leia os dados sem necessidade de desserializá-los primeiro, economizando recursos do cliente.

Aqui está um exemplo para obter um `FeedIterator` que retorna objetos de entidade, neste caso, um objeto `User`:

```csharp
FeedIterator<User> InteratorWithPOCOS = container.GetChangeFeedIterator<User>(ChangeFeedStartFrom.Beginning(), ChangeFeedMode.Incremental);
```

Aqui está um exemplo para obter um `FeedIterator` que retorna um `Stream`:

```csharp
FeedIterator iteratorWithStreams = container.GetChangeFeedStreamIterator<User>(ChangeFeedStartFrom.Beginning(), ChangeFeedMode.Incremental);
```

Se você não fornecer um `FeedRange` para um `FeedIterator` , poderá processar o feed de alterações de um contêiner inteiro em seu próprio ritmo. Aqui está um exemplo que inicia a leitura de todas as alterações, começando na hora atual:

```csharp
FeedIterator iteratorForTheEntireContainer = container.GetChangeFeedStreamIterator<User>(ChangeFeedStartFrom.Now(), ChangeFeedMode.Incremental);

while (iteratorForTheEntireContainer.HasMoreResults)
{
    try {
        FeedResponse<User> users = await iteratorForTheEntireContainer.ReadNextAsync();

        foreach (User user in users)
            {
                Console.WriteLine($"Detected change for user with id {user.id}");
            }
    }
    catch {
        Console.WriteLine($"No new changes");
        await Task.Delay(TimeSpan.FromSeconds(5));
    }
}
```

Como o feed de alterações é efetivamente uma lista infinita de itens que abrangem todas as futuras gravações e atualizações, o valor de `HasMoreResults` é sempre verdadeiro. Ao tentar ler o feed de alterações e não houver nenhuma nova alteração disponível, você receberá uma exceção. No exemplo acima, a exceção é tratada aguardando 5 segundos antes de verificar se há alterações.

## <a name="consuming-a-partition-keys-changes"></a>Consumindo as alterações de uma chave de partição

Em alguns casos, talvez você queira apenas processar as alterações de uma chave de partição específica. Você pode obter um `FeedIterator` para uma chave de partição específica e processar as alterações da mesma maneira que pode fazê-lo para um contêiner inteiro.

```csharp
FeedIterator<User> iteratorForPartitionKey = container.GetChangeFeedIterator<User>(
    ChangeFeedStartFrom.Beginning(FeedRange.FromPartitionKey(new PartitionKey("PartitionKeyValue")), ChangeFeedMode.Incremental));

while (iteratorForThePartitionKey.HasMoreResults)
{
    try {
        FeedResponse<User> users = await iteratorForThePartitionKey.ReadNextAsync();

        foreach (User user in users)
            {
                Console.WriteLine($"Detected change for user with id {user.id}");
            }
    }
    catch (CosmosException exception) when (exception.StatusCode == System.Net.HttpStatusCode.NotModified)
    {
        Console.WriteLine($"No new changes");
        await Task.Delay(TimeSpan.FromSeconds(5));
    }
}
```

## <a name="using-feedrange-for-parallelization"></a>Como usar FeedRange para paralelização

No [processador do feed de alterações](change-feed-processor.md), o trabalho é distribuído automaticamente entre vários consumidores. No modelo de pull do feed de alterações, você pode usar o `FeedRange` para paralelizar o processamento do feed de alterações. Um `FeedRange` representa um intervalo de valores de chave de partição.

Aqui está um exemplo que mostra como obter uma lista de intervalos para o contêiner:

```csharp
IReadOnlyList<FeedRange> ranges = await container.GetFeedRangesAsync();
```

Ao obter a lista de FeedRanges para seu contêiner, você obterá um `FeedRange` por [partição física](partitioning-overview.md#physical-partitions).

Usando um `FeedRange`, você pode criar um `FeedIterator` para paralelizar o processamento do feed de alterações em vários computadores ou threads. Ao contrário do exemplo anterior que mostrou como obter um `FeedIterator` para o contêiner inteiro ou uma única chave de partição, você pode usar FeedRanges para obter vários FeedIterators que podem processar o feed de alterações em paralelo.

No caso em que você deseja usar o FeedRanges, você precisa ter um processo orquestrador que obtém FeedRanges e os distribui para esses computadores. Essa distribuição pode ser:

* Usando `FeedRange.ToJsonString` e distribuindo esse valor de cadeia de caracteres. Os consumidores podem usar esse valor com `FeedRange.FromJsonString`
* Se a distribuição estiver em processo, passando a referência do objeto `FeedRange`.

Aqui está um exemplo que mostra como ler desde o início do feed de alterações do contêiner usando dois computadores hipotéticos separados que estão realizando a leitura em paralelo:

Computador 1:

```csharp
FeedIterator<User> iteratorA = container.GetChangeFeedIterator<User>(ChangeFeedStartFrom.Beginning(ranges[0]), ChangeFeedMode.Incremental);
while (iteratorA.HasMoreResults)
{
    try {
        FeedResponse<User> users = await iteratorA.ReadNextAsync();

        foreach (User user in users)
            {
                Console.WriteLine($"Detected change for user with id {user.id}");
            }
    }
    catch (CosmosException exception) when (exception.StatusCode == System.Net.HttpStatusCode.NotModified)
    {
        Console.WriteLine($"No new changes");
        await Task.Delay(TimeSpan.FromSeconds(5));
    }
}
```

Computador 2:

```csharp
FeedIterator<User> iteratorB = container.GetChangeFeedIterator<User>(ChangeFeedStartFrom.Beginning(ranges[1]), ChangeFeedMode.Incremental);
while (iteratorB.HasMoreResults)
{
    try {
        FeedResponse<User> users = await iteratorA.ReadNextAsync();

        foreach (User user in users)
            {
                Console.WriteLine($"Detected change for user with id {user.id}");
            }
    }
    catch (CosmosException exception) when (exception.StatusCode == System.Net.HttpStatusCode.NotModified)
    {
        Console.WriteLine($"No new changes");
        await Task.Delay(TimeSpan.FromSeconds(5));
    }
}
```

## <a name="saving-continuation-tokens"></a>Como salvar tokens de continuação

Você pode salvar a posição de seu `FeedIterator` criando um token de continuação. Um token de continuação é um valor de cadeia de caracteres que mantém o controle das últimas alterações processadas do FeedIterator. Isso permite que o `FeedIterator` retome desse ponto posteriormente. O código a seguir lerá o feed de alterações desde a criação do contêiner. Depois que não houver mais alterações disponíveis, ele manterá um token de continuação para que o consumo do feed de alterações possa ser retomado posteriormente.

```csharp
FeedIterator<User> iterator = container.GetChangeFeedIterator<User>(ChangeFeedStartFrom.Beginning(), ChangeFeedMode.Incremental);

string continuation = null;

while (iterator.HasMoreResults)
{
   try { 
        FeedResponse<User> users = await iterator.ReadNextAsync();
        continuation = users.ContinuationToken;

        foreach (User user in users)
            {
                Console.WriteLine($"Detected change for user with id {user.id}");
            }
   }
    catch (CosmosException exception) when (exception.StatusCode == System.Net.HttpStatusCode.NotModified)
    {
        Console.WriteLine($"No new changes");
        await Task.Delay(TimeSpan.FromSeconds(5));
    }   
}

// Some time later
FeedIterator<User> iteratorThatResumesFromLastPoint = container.GetChangeFeedIterator<User>(ChangeFeedStartFrom.ContinuationToken(continuation), ChangeFeedMode.Incremental);
```

Enquanto o contêiner do Cosmos existir, um token de continuação FeedIterator nunca expirará.

## <a name="next-steps"></a>Próximas etapas

* [Visão geral do feed de alterações](change-feed.md)
* [Usando o processador de feed de alterações](change-feed-processor.md)
* [Disparar o Azure Functions](change-feed-functions.md)
