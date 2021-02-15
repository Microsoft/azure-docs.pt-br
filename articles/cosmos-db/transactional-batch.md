---
title: Operações de lote transacionais no Azure Cosmos DB usando o SDK do .NET
description: Saiba como usar o TransactionalBatch no SDK do .NET Azure Cosmos DB para executar um grupo de operações de ponto que tenham êxito ou falha.
author: stefArroyo
ms.author: esarroyo
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 10/27/2020
ms.openlocfilehash: 9f6692db2da3722507136a468d1dcbdc2985e73f
ms.sourcegitcommit: fa807e40d729bf066b9b81c76a0e8c5b1c03b536
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/11/2020
ms.locfileid: "97347550"
---
# <a name="transactional-batch-operations-in-azure-cosmos-db-using-the-net-sdk"></a>Operações de lote transacionais no Azure Cosmos DB usando o SDK do .NET
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

O lote transacional descreve um grupo de operações de ponto que precisam ter êxito ou falha junto com a mesma chave de partição em um contêiner. No SDK do .NET, a `TransactionalBatch` classe é usada para definir esse lote de operações. Se todas as operações forem realizadas com sucesso na ordem em que são descritas na operação de lote transacional, a transação será confirmada. No entanto, se qualquer operação falhar, toda a transação será revertida.

## <a name="whats-a-transaction-in-azure-cosmos-db"></a>O que é uma transação no Azure Cosmos DB

Uma transação em um banco de dados típico pode ser definida como uma sequência de operações executadas como uma única unidade lógica de trabalho. Cada transação fornece garantias de propriedade ACID (atomicidade, consistência, isolamento, durabilidade).

* A **atomicidade** garante que todas as operações realizadas dentro de uma transação sejam tratadas como uma única unidade e que todas elas sejam confirmadas ou nenhuma delas.
* A **consistência** garante que os dados estejam sempre em um estado válido entre as transações.
* O **isolamento** garante que duas transações não interfiram umas com as outras – muitos sistemas comerciais fornecem vários níveis de isolamento que podem ser usados com base nas necessidades do aplicativo.
* A **durabilidade** garante que qualquer alteração confirmada em um banco de dados sempre estará presente.
O Azure Cosmos DB dá suporte a [transações em conformidade com Acid completo com isolamento de instantâneo](database-transactions-optimistic-concurrency.md) para operações na mesma [chave de partição lógica](partitioning-overview.md).

## <a name="transactional-batch-operations-vs-stored-procedures"></a>Operações em lote transacionais vs. procedimentos armazenados

O Azure Cosmos DB atualmente dá suporte a procedimentos armazenados, que também fornecem o escopo transacional em operações. No entanto, as operações de lote transacionais oferecem os seguintes benefícios:

* **Opção de idioma** – o lote transacional tem suporte no SDK e no idioma com o qual você trabalha já, enquanto os procedimentos armazenados precisam ser escritos em JavaScript.
* **Controle de versão de código** – o controle de versão do código do aplicativo e sua integração ao pipeline de CI/CD é muito mais natural do que orquestrar a atualização de um procedimento armazenado e verificar se a substituição ocorre no momento certo. Ele também facilita a reversão das alterações.
* **Desempenho** – latência reduzida em operações equivalentes em até 30% em comparação com a execução do procedimento armazenado.
* **Serialização de conteúdo** – cada operação em um lote transacional pode aproveitar as opções de serialização personalizadas para sua carga.

## <a name="how-to-create-a-transactional-batch-operation"></a>Como criar uma operação de lote transacional

Ao criar uma operação de lote transacional, você começa em uma instância de contêiner e chama `CreateTransactionalBatch` :

```csharp
string partitionKey = "The Family";
ParentClass parent = new ParentClass(){ Id = "The Parent", PartitionKey = partitionKey, Name = "John", Age = 30 };
ChildClass child = new ChildClass(){ Id = "The Child", ParentId = parent.Id, PartitionKey = partitionKey };
TransactionalBatch batch = container.CreateTransactionalBatch(new PartitionKey(parent.PartitionKey)) 
  .CreateItem<ParentClass>(parent)
  .CreateItem<ChildClass>(child);
```

Em seguida, você precisará chamar `ExecuteAsync` no lote:

```csharp
TransactionalBatchResponse batchResponse = await batch.ExecuteAsync();
```

Depois que a resposta for recebida, examine se ela foi bem-sucedida ou não e extraia os resultados:

```csharp
using (batchResponse)
{
  if (batchResponse.IsSuccessStatusCode)
  {
    TransactionalBatchOperationResult<ParentClass> parentResult = batchResponse.GetOperationResultAtIndex<ParentClass>(0);
    ParentClass parentClassResult = parentResult.Resource;
    TransactionalBatchOperationResult<ChildClass> childResult = batchResponse.GetOperationResultAtIndex<ChildClass>(1);
    ChildClass childClassResult = childResult.Resource;
  }
}
```

Se houver uma falha, a operação com falha terá um código de status de seu erro correspondente. Todas as outras operações terão um código de status 424 (dependência com falha). No exemplo a seguir, a operação falha porque tenta criar um item que já existe (409 HttpStatusCode. Conflict). O código de status permite que um identifique a causa da falha da transação.

```csharp
// Parent's birthday!
parent.Age = 31;
// Naming two children with the same name, should abort the transaction
ChildClass anotherChild = new ChildClass(){ Id = "The Child", ParentId = parent.Id, PartitionKey = partitionKey };
TransactionalBatchResponse failedBatchResponse = await container.CreateTransactionalBatch(new PartitionKey(partitionKey))
  .ReplaceItem<ParentClass>(parent.Id, parent)
  .CreateItem<ChildClass>(anotherChild)
  .ExecuteAsync();

using (failedBatchResponse)
{
  if (!failedBatchResponse.IsSuccessStatusCode)
  {
    TransactionalBatchOperationResult<ParentClass> parentResult = failedBatchResponse.GetOperationResultAtIndex<ParentClass>(0);
    // parentResult.StatusCode is 424
    TransactionalBatchOperationResult<ChildClass> childResult = failedBatchResponse.GetOperationResultAtIndex<ChildClass>(1);
    // childResult.StatusCode is 409
  }
}
```

## <a name="how-are-transactional-batch-operations-executed"></a>Como as operações de lote transacionais são executadas

Quando o `ExecuteAsync` método é chamado, todas as operações no `TransactionalBatch` objeto são agrupadas, serializadas em uma única carga e enviadas como uma única solicitação para o serviço de Azure Cosmos DB.

O serviço recebe a solicitação e executa todas as operações em um escopo transacional e retorna uma resposta usando o mesmo protocolo de serialização. Essa resposta é um êxito ou uma falha e fornece respostas de operações individuais por operação.

O SDK expõe a resposta para que você verifique o resultado e, opcionalmente, extraia cada um dos resultados da operação interna.

## <a name="limitations"></a>Limitações

Atualmente, há dois limites conhecidos:

* O limite de tamanho de solicitação Azure Cosmos DB restringe o tamanho da `TransactionalBatch` carga para não exceder 2 MB, e o tempo de execução máximo é de 5 segundos.
* Há um limite atual de 100 operações por `TransactionalBatch` para garantir que o desempenho seja conforme o esperado e dentro dos SLAs.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [o que você pode fazer com o TransactionalBatch](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/TransactionalBatch)

* Visite nossos [exemplos](sql-api-dotnet-v3sdk-samples.md) para obter mais maneiras de usar nosso SDK do .net Cosmos DB
