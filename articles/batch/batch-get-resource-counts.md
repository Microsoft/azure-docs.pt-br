---
title: Contar estados para tarefas e nós
description: Conte o estado das tarefas do Lote do Azure e nós de computação para ajudar a gerenciar e monitorar soluções do Lote.
ms.date: 06/18/2020
ms.topic: how-to
ms.custom: seodec18
ms.openlocfilehash: 90f741b9ec5e17da4fd0cc95ef921e116b0c27dc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85960581"
---
# <a name="monitor-batch-solutions-by-counting-tasks-and-nodes-by-state"></a>Monitorar soluções do Lote pela contagem de tarefas e nós por estado

Para monitorar e gerenciar soluções de lote do Azure em grande escala, talvez seja necessário determinar as contagens de recursos em vários Estados. O lote do Azure fornece operações eficientes para obter contagens de tarefas em lotes e nós de computação. Você pode usar essas operações em vez de consultas de lista potencialmente demoradas que retornam informações detalhadas sobre grandes coleções de tarefas ou nós.

- [Obter Contagens de Tarefas](/rest/api/batchservice/job/gettaskcounts) obtém uma contagem agregada de tarefas ativas, em execução e concluídas em um trabalho, bem como das tarefas que tiveram êxito ou falharam. 

  Contando as tarefas em cada estado, você pode exibir mais facilmente o andamento do trabalho para um usuário ou detectar atrasos inesperados ou falhas que podem afetar o trabalho. Obter Contagens de Tarefas está disponível desde a API do serviço de lote versão 2017-06-01.5.1 e SDKs e ferramentas relacionados.

- [Listar Contagens de Nó de Pool](/rest/api/batchservice/account/listpoolnodecounts) obtém o número de nós de computação dedicado e de baixa prioridade em cada pool que estão em vários estados: criando, ocioso, offline, que admitiu preempção, reinicializando, refazendo a imagem, iniciando e outros.

  Pela contagem de nós em cada estado, você pode determinar quando você tem recursos computacionais adequados para executar seus trabalhos e identificar possíveis problemas com seus pools. Listar Contagens de Nó de Pool está disponível desde a API do Serviço de Lote versão 2018-03-01.6.1 e SDKs e ferramentas relacionados.

Observe que, às vezes, os números retornados por essas operações podem não estar atualizados. Se você precisar ter certeza de que uma contagem é precisa, use uma consulta de lista para contar esses recursos. As consultas de lista também permitem que você obtenha informações sobre outros recursos do lote, como aplicativos. Para obter mais informações sobre a aplicação de filtros a consultas de lista, consulte [Criar consultas para listar recursos do Lote com eficiência](batch-efficient-list-queries.md).

## <a name="task-state-counts"></a>Contagens de estados de tarefa

A operação Obter Contagens de Tarefas conta tarefas pelos seguintes estados:

- **Ativa** – uma tarefa que está na fila e é capaz de executar, mas não está atualmente atribuída a um nó de computação. Uma tarefa também é `active` se é [dependente de uma tarefa pai](batch-task-dependencies.md) que ainda não foi concluída. 
- **Executando** – uma tarefa que foi atribuída a um nó de computação, mas ainda não foi concluída. Uma tarefa é contabilizada como `running` quando seu estado é `preparing` ou `running`, conforme indicado pela operação [Obter informações sobre uma tarefa](/rest/api/batchservice/task/get).
- **Concluída** – uma tarefa que não é mais elegível para ser executada porque foi concluída com êxito, ou então foi concluída com êxito e também esgotou seu limite de repetição. 
- **Bem-sucedida** – uma tarefa cujo resultado da execução da tarefa é `success`. O Lote determina se uma tarefa teve êxito ou não, verificando a propriedade `TaskExecutionResult` da propriedade [executionInfo](/rest/api/batchservice/task/get).
- **Bem-sucedida** – uma tarefa cuja execução tem resultado `failure`.

O exemplo de código .NET a seguir mostra como recuperar contagens de tarefas por estado:

```csharp
var taskCounts = await batchClient.JobOperations.GetJobTaskCountsAsync("job-1");

Console.WriteLine("Task count in active state: {0}", taskCounts.Active);
Console.WriteLine("Task count in preparing or running state: {0}", taskCounts.Running);
Console.WriteLine("Task count in completed state: {0}", taskCounts.Completed);
Console.WriteLine("Succeeded task count: {0}", taskCounts.Succeeded);
Console.WriteLine("Failed task count: {0}", taskCounts.Failed);
```

Você pode usar um padrão semelhante para REST e outras linguagens com suporte para obter as contagens de tarefa para um trabalho. 

> [!NOTE]
> As versões da API do Serviço do Lote anteriores a 2018-08-01.7.0 também retornam uma propriedade `validationStatus` a resposta Obter Contagens de Tarefas. Essa propriedade indica se o Lote verificou a consistência de contagens de estado com os estados relatados na API de Tarefas de Lista. Um valor de `validated` indica apenas que o Lote procurou consistência pelo menos uma vez para o trabalho. O valor da `validationStatus` propriedade não indica se as contagens que obtêm contagens de tarefas retornadas estão atualizadas no momento.

## <a name="node-state-counts"></a>Contagens de estados de nós

A operação Listar Contagens de Nós de Pool lista nós de computação pelos seguintes estados em cada pool. Contagens de agregação separadas são fornecidas para nós dedicados e nós de baixa prioridade em cada pool.

- **Criando** – uma VM alocada no Azure que ainda não iniciou o processo de ingresso em um pool.
- **Ociosa** – um nó de computação disponível que não está executando uma tarefa atualmente.
- **LeavingPool** – um nó que está saindo do pool, seja porque o usuário o removeu explicitamente ou porque o pool está redimensionando ou reduzindo verticalmente de modo automático.
- **Offline** – um nó que o Lote não pode usar para agendar novas tarefas.
- **Admitiu preempção** – um nó de baixa prioridade que foi removido do pool porque o Azure recuperou a VM. Um nó `preempted` pode ser reinicializado quando a capacidade da VM de baixa prioridade substituta está disponível.
- **Reinicializando** – um nó que está reinicializando.
- **Refazendo a imagem** – um nó no qual o sistema operacional está sendo reinstalado.
- **Em execução** – um nó que está executando uma ou mais tarefas (exceto a tarefa iniciar).
- **Iniciando** – um nó no qual o serviço de Lote está iniciando. 
- **StartTaskFailed** – um nó no qual a [tarefa iniciar](/rest/api/batchservice/pool/add#starttask) falhou e esgotou todas as novas tentativas e no qual `waitForSuccess` está definido na tarefa iniciar. O nó não é utilizável para execução de tarefas.
- **Desconhecido** – um nó que perdeu contato com o serviço de Lote e cujo estado não é conhecido.
- **Não utilizável** – um nó que não pode ser usado para execução da tarefa devido a erros.
- **WaitingForStartTask** – um nó no qual a tarefa iniciar começou a ser executada, mas `waitForSuccess` está definida e a tarefa iniciar não foi concluída.

O seguinte snippet de C# mostra como listar as contagens de nó para todos os pools na conta atual:

```csharp
foreach (var nodeCounts in batchClient.PoolOperations.ListPoolNodeCounts())
{
    Console.WriteLine("Pool Id: {0}", nodeCounts.PoolId);

    Console.WriteLine("Total dedicated node count: {0}", nodeCounts.Dedicated.Total);

    // Get dedicated node counts in Idle and Offline states; you can get additional states.
    Console.WriteLine("Dedicated node count in Idle state: {0}", nodeCounts.Dedicated.Idle);
    Console.WriteLine("Dedicated node count in Offline state: {0}", nodeCounts.Dedicated.Offline);

    Console.WriteLine("Total low priority node count: {0}", nodeCounts.LowPriority.Total);

    // Get low-priority node counts in Running and Preempted states; you can get additional states.
    Console.WriteLine("Low-priority node count in Running state: {0}", nodeCounts.LowPriority.Running);
    Console.WriteLine("Low-priority node count in Preempted state: {0}", nodeCounts.LowPriority.Preempted);
}
```

O snippet C# a seguir mostra como listar contagens de nós para um determinado pool na conta atual.

```csharp
foreach (var nodeCounts in batchClient.PoolOperations.ListPoolNodeCounts(new ODATADetailLevel(filterClause: "poolId eq 'testpool'")))
{
    Console.WriteLine("Pool Id: {0}", nodeCounts.PoolId);

    Console.WriteLine("Total dedicated node count: {0}", nodeCounts.Dedicated.Total);

    // Get dedicated node counts in Idle and Offline states; you can get additional states.
    Console.WriteLine("Dedicated node count in Idle state: {0}", nodeCounts.Dedicated.Idle);
    Console.WriteLine("Dedicated node count in Offline state: {0}", nodeCounts.Dedicated.Offline);

    Console.WriteLine("Total low priority node count: {0}", nodeCounts.LowPriority.Total);

    // Get low-priority node counts in Running and Preempted states; you can get additional states.
    Console.WriteLine("Low-priority node count in Running state: {0}", nodeCounts.LowPriority.Running);
    Console.WriteLine("Low-priority node count in Preempted state: {0}", nodeCounts.LowPriority.Preempted);
}
```

Você pode usar um padrão semelhante para REST e outras linguagens compatíveis para obter contagens de nó para pools.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [Fluxo de trabalho e recursos primários do serviço de lote](batch-service-workflow-features.md) como pools, nós, trabalhos e tarefas.
- Saiba mais sobre como aplicar filtros a consultas que listam recursos do lote, consulte [criar consultas para listar recursos do lote com eficiência](batch-efficient-list-queries.md).
