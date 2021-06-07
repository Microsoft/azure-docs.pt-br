---
title: Execute tarefas simultaneamente para maximizar o uso dos nós de computação do Lote
description: Aumente a eficiência e reduza os custos usando menos nós de computação e executando tarefas em paralelo em cada nó em um pool do lote do Azure
ms.topic: how-to
ms.date: 03/25/2021
ms.custom: H1Hack27Feb2017, devx-track-csharp
ms.openlocfilehash: 2a8f2d6a040bee0e32359f4860d7b346ac08c48e
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105607976"
---
# <a name="run-tasks-concurrently-to-maximize-usage-of-batch-compute-nodes"></a>Execute tarefas simultaneamente para maximizar o uso dos nós de computação do Lote

Você pode maximizar o uso de recursos em um número menor de nós de computação em seu pool executando mais de uma tarefa simultaneamente em cada nó.

Embora alguns cenários funcionem melhor com todos os recursos de um nó dedicados a uma única tarefa, determinadas cargas de trabalho podem ver tempos de trabalhos menores e reduzir custos quando várias tarefas compartilham esses recursos. Considere os seguintes cenários:

- **Minimize a transferência de dados** para tarefas que são capazes de compartilhar dados. Você pode reduzir drasticamente os encargos de transferência de dados copiando dados compartilhados para um número menor de nós, executando tarefas em paralelo em cada nó. Isso se aplicará especialmente se os dados que devem ser copiados em cada nó precisarem ser transferidos entre regiões geográficas.
- **Maximize o uso de memória** para tarefas que exigem uma grande quantidade de memória, mas somente durante curtos períodos de tempo e em momentos variáveis durante a execução. Você pode empregar menos nós de computação, porém maiores, com mais memória para lidar de forma eficiente com esses picos. Esses nós terão várias tarefas em execução em paralelo em cada nó, mas cada tarefa pode aproveitar a memória numerosas dos nós em momentos diferentes.
- **Reduza os limites de número de nós** quando a comunicação entre nós é necessária em um pool. Atualmente, os pools configurados para comunicação entre nós estão limitados a 50 nós de computação. Se cada nó desse pool for capaz de executar tarefas em paralelo, será possível executar um número maior de tarefas em paralelo.
- **Replique um cluster de computação local**, como quando você move um ambiente de computação pela primeira vez para o Azure. Se sua solução local atual executar várias tarefas por nó de computação, você poderá aumentar o número máximo de tarefas de nó para espelhar mais de perto a configuração.

## <a name="example-scenario"></a>Cenário de exemplo

Como exemplo, imagine um aplicativo de tarefa com requisitos de CPU e memória, de modo que os nós [ \_ D1 padrão](../cloud-services/cloud-services-sizes-specs.md#d-series) sejam suficientes. No entanto, para concluir o trabalho no tempo necessário, 1.000 desses nós são necessários.

Em vez de usar os nós Standard\_D1, que têm um núcleo de CPU, você poderia utilizar os nós [Standard\_D14](../cloud-services/cloud-services-sizes-specs.md#d-series) com 16 núcleos cada e permitir a execução de tarefas paralelas. Isso significa que 16 vezes menos nós poderiam ser usados, em vez de 1.000 nós, apenas 63 seria necessário. Se arquivos de aplicativo grandes ou dados de referência forem necessários para cada nó, a duração e a eficiência do trabalho serão aprimoradas, pois os dados serão copiados para apenas nós 63.

## <a name="enable-parallel-task-execution"></a>Habilitar a execução de tarefas paralelas

Você configura os nós de computação para a execução paralela das tarefas no nível do pool. Com a biblioteca .NET do lote, defina a propriedade [CloudPool. TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool.taskslotspernode) ao criar um pool. Se você estiver usando a API REST do lote, defina o elemento [taskSlotsPerNode](/rest/api/batchservice/pool/add) no corpo da solicitação durante a criação do pool.

> [!NOTE]
> Você pode definir o `taskSlotsPerNode` elemento e a propriedade [TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool) somente no momento da criação do pool. Eles não podem ser modificados depois que um pool já foi criado.

O lote do Azure permite que você defina os slots de tarefa por nó até (4x) o número de núcleos de nó. Por exemplo, se o pool estiver configurado com nós de tamanho "Grande" (quatro núcleos), será possível definir `taskSlotsPerNode` como 16. No entanto, independentemente de quantos núcleos o nó tem, você não pode ter mais de 256 slots de tarefa por nó. Para obter detalhes sobre o número de núcleos para cada um dos tamanhos de nó, confira [Tamanhos para Serviços de Nuvem](../cloud-services/cloud-services-sizes-specs.md). Para saber mais sobre limites de serviço, confira [Cotas e limites para o serviço de Lote do Azure](batch-quota-limit.md).

> [!TIP]
> Leve em consideração o valor de `taskSlotsPerNode` ao criar uma [fórmula de dimensionamento automático](/rest/api/batchservice/pool/enableautoscale) para o seu pool. Por exemplo, uma fórmula que avalia `$RunningTasks` poderia ser drasticamente afetada por um aumento nas tarefas por nó. Para obter mais informações, consulte [dimensionar automaticamente nós de computação em um pool do lote do Azure](batch-automatic-scaling.md).

## <a name="specify-task-distribution"></a>Especificar distribuição de tarefas

Ao habilitar tarefas simultâneas, é importante especificar como você deseja que as tarefas sejam distribuídas entre os nós no pool.

Ao usar a propriedade [CloudPool.TaskSchedulingPolicy](/dotnet/api/microsoft.azure.batch.cloudpool.taskschedulingpolicy), você pode especificar que as tarefas devam ser atribuídas uniformemente em todos os nós no pool ("difusão"). Ou você pode especificar que o máximo possível de tarefas deve ser atribuído a cada nó antes de as tarefas serem atribuídas a outro nó no pool ("remessa").

Como exemplo, considere o pool de nós [padrão \_ D14](../cloud-services/cloud-services-sizes-specs.md#d-series) (no exemplo acima) que está configurado com um valor de 16 [CloudPool. TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool.taskslotspernode) . Se o [CloudPool. TaskSchedulingPolicy](/dotnet/api/microsoft.azure.batch.cloudpool.taskschedulingpolicy) for configurado com um [ComputeNodeFillType](/dotnet/api/microsoft.azure.batch.common.computenodefilltype) do *Pack*, ele maximizaria o uso de todos os 16 núcleos de cada nó e permitiria que um [pool de dimensionamento](batch-automatic-scaling.md) automático removesse nós não utilizados (nós sem nenhuma tarefa atribuída) do pool. Isso minimiza o uso de recursos e economizando dinheiro.

## <a name="define-variable-slots-per-task"></a>Definir Slots variáveis por tarefa

Uma tarefa pode ser definida com a propriedade [CloudTask. RequiredSlots](/dotnet/api/microsoft.azure.batch.cloudtask.requiredslots) , especificando quantos slots ele requer para ser executado em um nó de computação. O valor padrão é 1. Você pode definir os slots de tarefa variáveis se suas tarefas tiverem diferentes pesos em relação ao uso de recursos no nó de computação. Isso permite que cada nó de computação tenha um número razoável de tarefas simultâneas em execução sem sobrecarregar recursos do sistema, como CPU ou memória.

Por exemplo, para um pool com propriedade `taskSlotsPerNode = 8` , você pode enviar tarefas com uso intensivo de CPU de vários núcleos com o `requiredSlots = 8` , enquanto outras tarefas podem ser definidas como `requiredSlots = 1` . Quando essa carga de trabalho mista é agendada, as tarefas de uso intensivo de CPU são executadas exclusivamente em seus nós de computação, enquanto outras podem ser executadas simultaneamente (até oito tarefas ao mesmo tempo) em outros nós. Isso ajuda a balancear sua carga de trabalho entre nós de computação e melhorar a eficiência do uso de recursos.

Certifique-se de não especificar que uma tarefa `requiredSlots` seja maior que o do pool `taskSlotsPerNode` . Isso fará com que a tarefa nunca possa ser executada. No momento, o serviço de lote não valida esse conflito quando você envia tarefas porque um trabalho pode não ter um pool associado no tempo de envio ou pode ser alterado para um pool diferente desabilitando/reabilitando.

> [!TIP]
> Ao usar slots de tarefa de variável, é possível que tarefas grandes com slots mais necessários possam falhar temporariamente para serem agendadas porque não há slots suficientes disponíveis em nenhum nó de computação, mesmo quando ainda há Slots ociosos em alguns nós. Você pode aumentar a prioridade do trabalho para essas tarefas para aumentar sua chance de competir por slots disponíveis em nós.
>
> O serviço de lote emite o [TaskScheduleFailEvent](batch-task-schedule-fail-event.md) quando ele não consegue agendar uma tarefa para ser executado e continua repetindo o agendamento até que os slots necessários fiquem disponíveis. Você pode escutar esse evento para detectar possíveis problemas de agendamento de tarefas e atenuá-los adequadamente.

## <a name="batch-net-example"></a>Exemplo de .NET do Lote

Os seguintes trechos de código de API [.net do lote](/dotnet/api/microsoft.azure.batch) mostram como criar um pool com vários slots de tarefa por nó e como enviar uma tarefa com slots necessários.

### <a name="create-a-pool-with-multiple-task-slots-per-node"></a>Criar um pool com vários slots de tarefa por nó

Este trecho de código mostra uma solicitação para criar um pool que contém quatro nós, com quatro slots de tarefa permitidos por nó. Isso especifica uma política de agendamento de tarefas que preencherá cada nó com tarefas antes de atribuir tarefas a outro nó no pool.

Para saber mais sobre como adicionar pools usando a API .NET do Lote, consulte [BatchClient.PoolOperations.CreatePool](/dotnet/api/microsoft.azure.batch.pooloperations.createpool).

```csharp
CloudPool pool =
    batchClient.PoolOperations.CreatePool(
        poolId: "mypool",
        targetDedicatedComputeNodes: 4
        virtualMachineSize: "standard_d1_v2",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));

pool.TaskSlotsPerNode = 4;
pool.TaskSchedulingPolicy = new TaskSchedulingPolicy(ComputeNodeFillType.Pack);
pool.Commit();
```

### <a name="create-a-task-with-required-slots"></a>Criar uma tarefa com slots necessários

Este trecho de código cria uma tarefa com não padrão `requiredSlots` . Esta tarefa só será executada quando houver slots livres suficientes disponíveis em um nó de computação.

```csharp
CloudTask task = new CloudTask(taskId, taskCommandLine)
{
    RequiredSlots = 2
};
```

### <a name="list-compute-nodes-with-counts-for-running-tasks-and-slots"></a>Listar nós de computação com contagens para executar tarefas e slots

Esse trecho de código lista todos os nós de computação no pool e imprime as contagens para executar tarefas e slots de tarefa por nó.

```csharp
ODATADetailLevel nodeDetail = new ODATADetailLevel(selectClause: "id,runningTasksCount,runningTaskSlotsCount");
IPagedEnumerable<ComputeNode> nodes = batchClient.PoolOperations.ListComputeNodes(poolId, nodeDetail);

await nodes.ForEachAsync(node =>
{
    Console.WriteLine(node.Id + " :");
    Console.WriteLine($"RunningTasks = {node.RunningTasksCount}, RunningTaskSlots = {node.RunningTaskSlotsCount}");

}).ConfigureAwait(continueOnCapturedContext: false);
```

### <a name="list-task-counts-for-the-job"></a>Listar contagens de tarefas para o trabalho

Esse trecho de código obtém as contagens de tarefas para o trabalho, o que inclui tarefas e contagem de Slots de tarefa por estado de tarefa.

```csharp
TaskCountsResult result = await batchClient.JobOperations.GetJobTaskCountsAsync(jobId);

Console.WriteLine("\t\tActive\tRunning\tCompleted");
Console.WriteLine($"TaskCounts:\t{result.TaskCounts.Active}\t{result.TaskCounts.Running}\t{result.TaskCounts.Completed}");
Console.WriteLine($"TaskSlotCounts:\t{result.TaskSlotCounts.Active}\t{result.TaskSlotCounts.Running}\t{result.TaskSlotCounts.Completed}");
```

## <a name="batch-rest-example"></a>Exemplo REST do Lote

Os seguintes trechos de código de API [REST do lote](/rest/api/batchservice/) mostram como criar um pool com vários slots de tarefa por nó e como enviar uma tarefa com slots necessários.

### <a name="create-a-pool-with-multiple-task-slots-per-node"></a>Criar um pool com vários slots de tarefa por nó

Este trecho de código mostra uma solicitação para criar um pool que contém dois nós grandes com um máximo de quatro tarefas por nó.

Para obter mais informações sobre como adicionar pools usando a API REST, consulte [Adicionar um pool a uma conta](/rest/api/batchservice/pool/add).

```json
{
  "odata.metadata":"https://myaccount.myregion.batch.azure.com/$metadata#pools/@Element",
  "id":"mypool",
  "vmSize":"large",
  "cloudServiceConfiguration": {
    "osFamily":"4",
    "targetOSVersion":"*",
  },
  "targetDedicatedComputeNodes":2,
  "taskSlotsPerNode":4,
  "enableInterNodeCommunication":true,
}
```

### <a name="create-a-task-with-required-slots"></a>Criar uma tarefa com slots necessários

Este trecho de código mostra uma solicitação para adicionar uma tarefa com não padrão `requiredSlots` . Esta tarefa só será executada quando houver slots livres suficientes disponíveis no nó de computação.

```json
{
  "id": "taskId",
  "commandLine": "bash -c 'echo hello'",
  "userIdentity": {
    "autoUser": {
      "scope": "task",
      "elevationLevel": "nonadmin"
    }
  },
  "requiredSLots": 2
}
```

## <a name="code-sample-on-github"></a>Exemplo de código no GitHub

O projeto [ParallelTasks](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ParallelTasks) no GitHub ilustra o uso da propriedade [CloudPool. TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool.taskslotspernode) .

Este aplicativo de console C# usa a biblioteca [.NET do Lote](/dotnet/api/microsoft.azure.batch) para criar um pool com um ou mais nós de computação. Ele executa um número configurável de tarefas nesses nós para simular uma carga de variável. A saída do aplicativo mostra quais nós executaram cada tarefa. O aplicativo também fornece um resumo dos parâmetros do trabalho e a duração.

Por exemplo, abaixo está a parte de resumo da saída de duas execuções diferentes do aplicativo de exemplo ParallelTasks. As durações de trabalho mostradas aqui não incluem o tempo de criação do pool, pois cada trabalho foi enviado para um pool criado anteriormente cujos nós de computação estavam no estado *ocioso* no momento do envio.

A primeira execução do aplicativo de exemplo mostra que com um único nó no pool, e a configuração padrão de uma tarefa por nó, a duração do trabalho será superior a 30 minutos.

```
Nodes: 1
Node size: large
Task slots per node: 1
Max slots per task: 1
Tasks: 32
Duration: 00:30:01.4638023
```

A segunda execução do exemplo mostra uma redução significativa na duração do trabalho. Isso ocorre porque o pool foi configurado com quatro tarefas por nó, permitindo que a execução de tarefa paralela conclua o trabalho em quase um trimestre do tempo.

```
Nodes: 1
Node size: large
Task slots per node: 4
Max slots per task: 1
Tasks: 32
Duration: 00:08:48.2423500
```

## <a name="next-steps"></a>Próximas etapas

- Experimente o mapa de calor [batch Explorer](https://azure.github.io/BatchExplorer/) . O Batch Explorer é uma ferramenta cliente autônoma, rica e exclusiva para ajudar a criar, depurar e monitorar aplicativos em lote do Azure. Quando você estiver executando o aplicativo de exemplo [ParallelTasks](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ParallelTasks) , o recurso de mapa de calor batch Explorer permite visualizar facilmente a execução de tarefas paralelas em cada nó.
- Explore os [exemplos do lote do Azure no GitHub](https://github.com/Azure/azure-batch-samples).
- Saiba mais sobre [dependências de tarefas do lote](batch-task-dependencies.md).
