---
title: Criar vários gatilhos de Azure Functions independentes para Cosmos DB
description: Saiba como configurar vários gatilhos independentes do Azure Functions para o Cosmos DB a fim de criar arquiteturas orientadas a eventos.
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 07/17/2019
ms.author: maquaran
ms.custom: devx-track-csharp
ms.openlocfilehash: 78fff48a97965f0b80456cd3e56ed1507bc784fc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93336666"
---
# <a name="create-multiple-azure-functions-triggers-for-cosmos-db"></a>Criar vários gatilhos do Azure Functions para o Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Este artigo descreve como você pode configurar vários gatilhos do Azure Functions para o Cosmos DB a fim de funcionarem em paralelo e reagirem de modo independente a alterações.

:::image type="content" source="./media/change-feed-functions/multi-trigger.png" alt-text="Funções baseadas em evento sem servidor operando com o gatilho do Azure Functions para o Cosmos DB e compartilhando um contêiner de concessões" border="false":::

## <a name="event-based-architecture-requirements"></a>Requisitos de arquitetura baseada em evento

Ao criar arquiteturas sem servidor com o [Azure Functions](../azure-functions/functions-overview.md), é [recomendado](../azure-functions/functions-best-practices.md#avoid-long-running-functions) criar pequenos conjuntos de função que funcionam juntos, em vez de funções grandes de execução prolongada.

Conforme criar fluxos sem servidor com base em eventos usando o [Gatilho do Azure Functions para o Cosmos DB](./change-feed-functions.md), você executará o cenário em que deseja realizar várias ações sempre que houver um novo evento em um [contêiner do Azure Cosmos](./account-databases-containers-items.md#azure-cosmos-containers) em particular. Se as ações que você deseja disparar são independentes umas das outras, a solução ideal é **criar um gatilho do Azure Functions para o Cosmos DB por ação** que você deseja realizar, tudo isso escutando as alterações no mesmo contêiner do Azure Cosmos.

## <a name="optimizing-containers-for-multiple-triggers"></a>Otimizando contêineres para vários gatilhos

Dados os *requisitos* do gatilho do Azure Functions para o Cosmos DB, precisamos de um segundo contêiner para armazenar o estado, também chamado de *contêiner de concessões*. Isso significa que você precisa de um contêiner de concessões separado para cada Azure Function?

Aqui você tem duas opções:

* Criar **um contêiner de concessões por função**: essa abordagem pode ser traduzida em custos adicionais, a menos que você esteja usando um [banco de dados de produtividade compartilhado](./set-throughput.md#set-throughput-on-a-database). A taxa de transferência mínima no nível do contêiner é de 400 [unidades de solicitação](./request-units.md) e, no caso do contêiner de concessões, só está sendo usada como ponto de verificação do progresso e para manter o estado.
* Ter **um contêiner de concessão e compartilhá-lo** para todas as suas funções: essa segunda opção faz uso melhor das unidades de solicitação provisionadas no contêiner, pois permite que vários Azure Functions compartilhem e usem a mesma taxa de transferência provisionada.

A meta deste artigo é orientá-lo para realizar a segunda opção.

## <a name="configuring-a-shared-leases-container"></a>Configurando um contêiner de concessões compartilhado

Para configurar o contêiner de concessões compartilhado, a única configuração adicional que você precisa fazer a seus gatilhos é adicionar o  [atributo](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#attributes-and-annotations)`LeaseCollectionPrefix` se você está usando C# ou o `leaseCollectionPrefix` [atributo](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md) se você está usando JavaScript. O valor do atributo deve ser um descritor lógico daquele gatilho específico.

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

* Veja a configuração completa para o [Gatilho do Azure Functions para o Cosmos DB](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration)
* Verifique a [lista de exemplos](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md) estendida para todos os idiomas.
* Visite as receitas Sem Servidor com o [repositório do GitHub](https://github.com/ealsur/serverless-recipes/tree/master/cosmosdbtriggerscenarios) do Azure Cosmos DB e do Azure Functions para obter mais exemplos.
