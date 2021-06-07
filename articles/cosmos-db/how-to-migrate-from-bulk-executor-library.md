---
title: Migrar da biblioteca de executores em massa para o suporte em massa em Azure Cosmos DB SDK do .NET v3
description: Saiba como migrar seu aplicativo do usando a biblioteca de executores em massa para o suporte em massa no Azure Cosmos DB SDK v3
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 04/24/2020
ms.author: maquaran
ms.custom: devx-track-dotnet
ms.openlocfilehash: 24d6b475964e4bf7745495e9c41d0e89bb76f7e9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93341273"
---
# <a name="migrate-from-the-bulk-executor-library-to-the-bulk-support-in-azure-cosmos-db-net-v3-sdk"></a>Migrar da biblioteca de executores em massa para o suporte em massa em Azure Cosmos DB SDK do .NET v3
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Este artigo descreve as etapas necessárias para migrar um código de aplicativo existente que usa a [biblioteca de executores em massa do .net](bulk-executor-dot-net.md) para o recurso de suporte em [massa](tutorial-sql-api-dotnet-bulk-import.md) na versão mais recente do SDK do .net.

## <a name="enable-bulk-support"></a>Habilitar suporte em massa

Habilite o suporte em massa na `CosmosClient` instância por meio da configuração [AllowBulkExecution](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.allowbulkexecution) :

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="Initialization":::

## <a name="create-tasks-for-each-operation"></a>Criar tarefas para cada operação

O suporte em massa no SDK do .NET funciona aproveitando a [biblioteca paralela de tarefas](/dotnet/standard/parallel-programming/task-parallel-library-tpl) e as operações de agrupamento que ocorrem simultaneamente. 

Não há nenhum método único no SDK que levará sua lista de documentos ou operações como um parâmetro de entrada, mas, em vez disso, você precisa criar uma tarefa para cada operação que deseja executar em massa e, em seguida, esperar que elas sejam concluídas.

Por exemplo, se a entrada inicial for uma lista de itens em que cada item tem o seguinte esquema:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="Model":::

Se desejar fazer a importação em massa (semelhante ao uso de BulkExecutor. BulkImportAsync), você precisará ter chamadas simultâneas para `CreateItemAsync` . Por exemplo:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkImport":::

Se desejar fazer uma *atualização* em massa (semelhante ao uso de [BulkExecutor. BulkUpdateAsync](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkupdateasync)), você precisará ter chamadas simultâneas para `ReplaceItemAsync` o método depois de atualizar o valor do item. Por exemplo:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkUpdate":::

E se desejar fazer a *exclusão* em massa (semelhante ao uso de [BulkExecutor. BulkDeleteAsync](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkdeleteasync)), você precisará ter chamadas simultâneas para `DeleteItemAsync` , com a `id` chave de partição e de cada item. Por exemplo:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkDelete":::

## <a name="capture-task-result-state"></a>Estado do resultado da tarefa de captura

Nos exemplos de código anteriores, criamos uma lista simultânea de tarefas e chamamos o `CaptureOperationResponse` método em cada uma dessas tarefas. Esse método é uma extensão que nos permite manter um *esquema de resposta semelhante* como BulkExecutor, capturando quaisquer erros e acompanhando o [uso de unidades de solicitação](request-units.md).

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="CaptureOperationResult":::

Onde o `OperationResponse` é declarado como:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="OperationResult":::

## <a name="execute-operations-concurrently"></a>Executar operações simultaneamente

Para acompanhar o escopo de toda a lista de tarefas, usamos essa classe auxiliar:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkOperationsHelper":::

O `ExecuteAsync` método aguardará até que todas as operações sejam concluídas e você poderá usá-las da seguinte forma:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="WhenAll":::

## <a name="capture-statistics"></a>Estatísticas de captura

O código anterior aguarda até que todas as operações sejam concluídas e calcula as estatísticas necessárias. Essas estatísticas são semelhantes às do [BulkImportResponse](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkimport.bulkimportresponse)da biblioteca de executor em massa.

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="ResponseType":::

O `BulkOperationResponse` contém:

1. O tempo total necessário para processar a lista de operações por meio do suporte em massa.
1. O número de operações bem-sucedidas.
1. O total de unidades de solicitação consumidas.
1. Se houver falhas, ele exibirá uma lista de tuplas que contêm a exceção e o item associado para fins de log e de identificação.

## <a name="retry-configuration"></a>Repetir configuração

A biblioteca de executores em massa tinha [diretrizes](bulk-executor-dot-net.md#bulk-import-data-to-an-azure-cosmos-account) que mencionaram para definir o `MaxRetryWaitTimeInSeconds` e o `MaxRetryAttemptsOnThrottledRequests` [retryoptions](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.retryoptions) para `0` delegar controle à biblioteca.

Para suporte em massa no SDK do .NET, não há nenhum comportamento oculto. Você pode configurar as opções de repetição diretamente por meio de [CosmosClientOptions. MaxRetryAttemptsOnRateLimitedRequests](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.maxretryattemptsonratelimitedrequests) e [CosmosClientOptions. MaxRetryWaitTimeOnRateLimitedRequests](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.maxretrywaittimeonratelimitedrequests).

> [!NOTE]
> Nos casos em que as unidades de solicitação provisionadas são muito menores do que o esperado com base na quantidade de dados, talvez você queira considerar defini-los como valores altos. A operação em massa levará mais tempo, mas terá uma chance maior de ser concluída com sucesso devido às mais altas tentativas.

## <a name="performance-improvements"></a>Aprimoramentos de desempenho

Assim como acontece com outras operações com o SDK do .NET, o uso das APIs de fluxo resulta em um melhor desempenho e evita qualquer serialização desnecessária. 

O uso de APIs de fluxo só é possível se a natureza dos dados que você usa corresponder à de um fluxo de bytes (por exemplo, fluxos de arquivo). Nesses casos, usar os `CreateItemStreamAsync` métodos, `ReplaceItemStreamAsync` , ou `DeleteItemStreamAsync` e trabalhar com `ResponseMessage` (em vez de `ItemResponse` ) aumenta a taxa de transferência que pode ser obtida.

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre as versões do SDK do .NET, consulte o artigo [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md) .
* Obtenha o [código-fonte](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration) completo de migração do github.
* [Exemplos em massa adicionais no GitHub](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/BulkSupport)