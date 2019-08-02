---
title: Como criar vários gatilhos independentes do Azure Functions para o Cosmos DB
description: Saiba como configurar vários gatilhos independentes do Azure Functions para o Cosmos DB a fim de criar arquiteturas orientadas a eventos.
author: ealsur
ms.service: cosmos-db
ms.topic: sample
ms.date: 07/17/2019
ms.author: maquaran
ms.openlocfilehash: 315ac1025a2b05ec7b16f7f0b14b66f224905d92
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68335686"
---
# <a name="create-multiple-azure-functions-triggers-for-cosmos-db"></a>Criar vários gatilhos do Azure Functions para o Cosmos DB

Este artigo descreve como você pode configurar vários gatilhos do Azure Functions para o Cosmos DB a fim de funcionarem em paralelo e reagirem de modo independente a alterações.

![Funções baseadas em evento sem servidor operando com o gatilho do Azure Functions para o Cosmos DB e compartilhando um contêiner de concessões](./media/change-feed-functions/multi-trigger.png)

## <a name="event-based-architecture-requirements"></a>Requisitos de arquitetura baseada em evento

Ao criar arquiteturas sem servidor com o [Azure Functions](../azure-functions/functions-overview.md), é [recomendado](../azure-functions/functions-best-practices.md#avoid-long-running-functions) criar pequenos conjuntos de função que funcionam juntos, em vez de funções grandes de execução prolongada.

Conforme criar fluxos sem servidor com base em eventos usando o [Gatilho do Azure Functions para o Cosmos DB](./change-feed-functions.md), você executará o cenário em que deseja realizar várias ações sempre que houver um novo evento em um [contêiner do Azure Cosmos](./databases-containers-items.md#azure-cosmos-containers) em particular. Se as ações que você deseja disparar são independentes umas das outras, a solução ideal é **criar um gatilho do Azure Functions para o Cosmos DB por ação** que você deseja realizar, tudo isso escutando as alterações no mesmo contêiner do Azure Cosmos.

## <a name="optimizing-containers-for-multiple-triggers"></a>Otimizando contêineres para vários gatilhos

Dados os *requisitos* do gatilho do Azure Functions para o Cosmos DB, precisamos de um segundo contêiner para armazenar o estado, também chamado de *contêiner de concessões*. Isso significa que você precisa de um contêiner de concessões separado para cada Azure Function?

Aqui você tem duas opções:

* Criar **um contêiner de concessões por Função**: Essa abordagem pode se converter em custos adicionais, a menos que você esteja usando um [banco de dados de taxa de transferência compartilhado](./set-throughput.md#set-throughput-on-a-database). A taxa de transferência mínima no nível do contêiner é de 400 [unidades de solicitação](./request-units.md) e, no caso do contêiner de concessões, só está sendo usada como ponto de verificação do progresso e para manter o estado.
* Ter **um contêiner de concessão e compartilhá-lo** com todas as suas Funções. Essa segunda opção oferece um uso melhor das Unidades de Solicitação provisionadas no contêiner, pois permite que várias Azure Functions compartilhem e usem a mesma taxa de transferência provisionada.

A meta deste artigo é orientá-lo para realizar a segunda opção.

## <a name="configuring-a-shared-leases-container"></a>Configurando um contêiner de concessões compartilhado

Para configurar o contêiner de concessões compartilhado, a única configuração adicional que você precisa fazer a seus gatilhos é adicionar o [atributo](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---c-attributes) `LeaseCollectionPrefix` se você está usando C# ou o [atributo](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---javascript-example) `leaseCollectionPrefix` se você está usando JavaScript. O valor do atributo deve ser um descritor lógico daquele gatilho específico.

Por exemplo, se você tem três gatilhos: um que envia emails, um que faz uma agregação para criar uma exibição materializada e um que envia as alterações para outro armazenamento, para análise posterior, pode atribuir o `LeaseCollectionPrefix` de "emails" ao primeiro, "materializada" ao segundo e "análise" ao terceiro.

A parte importante é que todos os três Gatilhos **podem usar a mesma configuração de contêiner de concessões** (nome de contêiner, banco de dados e conta).

Um exemplo de código muito simples usando o atributo `LeaseCollectionPrefix` em C# teria esta aparência:

```cs
using Microsoft.Azure.Documents;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using Microsoft.Extensions.Logging;

[FunctionName("SendEmails")]
public static void SendEmails([CosmosDBTrigger(
    databaseName: "ToDoItems",
    collectionName: "Items",
    ConnectionStringSetting = "CosmosDBConnection",
    LeaseCollectionName = "leases",
    LeaseCollectionPrefix = "emails")]IReadOnlyList<Document> documents,
    ILogger log)
{
    ...
}

[FunctionName("MaterializedViews")]
public static void MaterializedViews([CosmosDBTrigger(
    databaseName: "ToDoItems",
    collectionName: "Items",
    ConnectionStringSetting = "CosmosDBConnection",
    LeaseCollectionName = "leases",
    LeaseCollectionPrefix = "materialized")]IReadOnlyList<Document> documents,
    ILogger log)
{
    ...
}
```

Para JavaScript, você pode aplicar a configuração no arquivo `function.json` com o atributo `leaseCollectionPrefix`:

```json
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "CosmosDBConnection",
    "databaseName": "ToDoItems",
    "collectionName": "Items",
    "leaseCollectionPrefix": "emails"
},
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "CosmosDBConnection",
    "databaseName": "ToDoItems",
    "collectionName": "Items",
    "leaseCollectionPrefix": "materialized"
}
```

> [!NOTE]
> Sempre monitore das unidades de solicitação provisionadas em seu contêiner de concessões compartilhado. Cada Gatilho que o compartilha aumentará o consumo médio de taxa de transferência, portanto, talvez seja necessário aumentar a produtividade provisionada conforme você aumenta o número de Azure Functions que o utilizam.

## <a name="next-steps"></a>Próximas etapas

* Veja a configuração completa para o [Gatilho do Azure Functions para o Cosmos DB](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration)
* Verifique a [lista de exemplos](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---example) estendida para todos os idiomas.
* Visite as receitas Sem Servidor com o [repositório do GitHub](https://github.com/ealsur/serverless-recipes/tree/master/cosmosdbtriggerscenarios) do Azure Cosmos DB e do Azure Functions para obter mais exemplos.