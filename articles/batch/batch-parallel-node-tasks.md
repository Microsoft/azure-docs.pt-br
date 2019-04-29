---
title: Executar tarefas em paralelo para usar recursos de computação com eficiência - Lote do Azure | Microsoft Docs
description: Aumente a eficiência e reduza os custos usando menos nós de computação e executando tarefas simultâneas em cada nó em um pool do Lote do Azure
services: batch
documentationcenter: .net
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: 538a067c-1f6e-44eb-a92b-8d51c33d3e1a
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/17/2019
ms.author: lahugh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 79b45bd423ed6715cdb7cc7c0e079c150eefede5
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63763703"
---
# <a name="run-tasks-concurrently-to-maximize-usage-of-batch-compute-nodes"></a>Execute tarefas simultaneamente para maximizar o uso dos nós de computação do Lote 

Executando simultaneamente mais de uma tarefa em cada nó de computação no pool de Lotes do Azure, você pode maximizar o uso dos recursos em um número menor de nós no pool. Para algumas cargas de trabalho, isso pode resultar em tempos mais curtos de trabalho e reduzir os custos.

Embora alguns cenários se beneficiem de dedicar todos os recursos de um nó para uma única tarefa, algumas situações se beneficiam de permitir que várias tarefas compartilhem esses recursos:

* **Minimização da transferência de dados** quando as tarefas puderem compartilhar dados. Nesse cenário, é possível reduzir drasticamente os encargos de transferência de dados copiando dados compartilhados em uma quantidade menor de nós e executando tarefas em paralelo em cada nó. Isso se aplicará especialmente se os dados que devem ser copiados em cada nó precisarem ser transferidos entre regiões geográficas.
* **Maximização do uso de memória** quando as tarefas exigirem uma grande quantidade de memória, mas somente durante curtos períodos de tempo, e em momentos variáveis durante a execução. Você pode empregar menos nós de computação, porém maiores, com mais memória para lidar de forma eficiente com esses picos. Esses nós teriam várias tarefas executadas em paralelo em cada nó, mas cada tarefa aproveitaria a grande quantidade de memória dos nós em momentos diferentes.
* **Redução dos limites do número de nós** quando a comunicação entre nós for necessária em um pool. Atualmente, os pools configurados para comunicação entre nós estão limitados a 50 nós de computação. Se cada nó desse pool for capaz de executar tarefas em paralelo, será possível executar um número maior de tarefas em paralelo.
* **Replicação de um cluster de computação local**, como na primeira movimentação de um ambiente de computação para o Azure. Se sua solução local atual executar várias tarefas por nó de computação, você poderá aumentar o número máximo de tarefas de nó para espelhar mais de perto a configuração.

## <a name="example-scenario"></a>Cenário de exemplo
Para ilustrar os benefícios de execução das tarefas paralelas, digamos que seu aplicativo de tarefa tenha requisitos de CPU e memória de forma que os nós [Standard\_D1](../cloud-services/cloud-services-sizes-specs.md) sejam suficientes. Mas, para concluir o trabalho no tempo necessário, 1.000 nós são necessários.

Em vez de usar os nós Standard\_D1, que têm um núcleo de CPU, você poderia utilizar os nós [Standard\_D14](../cloud-services/cloud-services-sizes-specs.md) com 16 núcleos cada e permitir a execução de tarefas paralelas. Portanto, *16 vezes menos nós* poderiam ser usados; em vez de 1.000 nós, somente 63 seriam necessários. Além disso, se arquivos de aplicativo grandes ou dados de referência forem necessários para cada nó, a eficiência e a duração do trabalho serão novamente aperfeiçoadas, uma vez que os dados são copiados para apenas 63 nós.

## <a name="enable-parallel-task-execution"></a>Habilitar a execução de tarefas paralelas
Você configura os nós de computação para a execução paralela das tarefas no nível do pool. Com a biblioteca .NET do Lote, defina a propriedade [CloudPool.MaxTasksPerComputeNode][maxtasks_net] quando criar um pool. Se você estiver usando a API REST do Lote, defina o elemento [maxTasksPerNode][rest_addpool] no corpo da solicitação durante a criação do pool.

O lote do Azure permite que você defina tarefas por nó até (4X) o número de nós principais. Por exemplo, se o pool estiver configurado com nós de tamanho "Grande" (quatro núcleos), será possível definir `maxTasksPerNode` como 16. No entanto, independentemente do nó tem o número de núcleos, você não pode ter mais de 256 tarefas por nó. Para obter detalhes sobre o número de núcleos para cada um dos tamanhos de nó, confira [Tamanhos para Serviços de Nuvem](../cloud-services/cloud-services-sizes-specs.md). Para saber mais sobre limites de serviço, confira [Cotas e limites para o serviço de Lote do Azure](batch-quota-limit.md).

> [!TIP]
> Leve em consideração o valor de `maxTasksPerNode` ao criar uma [fórmula de dimensionamento automático][enable_autoscaling] para o seu pool. Por exemplo, uma fórmula que avalia `$RunningTasks` poderia ser drasticamente afetada por um aumento nas tarefas por nó. Consulte [Dimensionar automaticamente nós de computação em um pool do Lote do Azure](batch-automatic-scaling.md) para saber mais.
>
>

## <a name="distribution-of-tasks"></a>Distribuição de tarefas
Quando os nós de computação em um pool puderem executar as tarefas simultaneamente, será importante especificar como você deseja distribuir suas tarefas nos nós no pool.

Ao usar a propriedade [CloudPool.TaskSchedulingPolicy][task_schedule], você pode especificar que as tarefas devam ser atribuídas uniformemente em todos os nós no pool ("difusão"). Ou você pode especificar que o máximo possível de tarefas deve ser atribuído a cada nó antes de as tarefas serem atribuídas a outro nó no pool ("remessa").

Como um exemplo de como esse recurso é útil, considere o pool de nós [Standard\_D14](../cloud-services/cloud-services-sizes-specs.md) (no exemplo acima) configurado com um valor de 16 para [CloudPool.MaxTasksPerComputeNode][maxtasks_net]. Se [CloudPool.TaskSchedulingPolicy][task_schedule] for configurada com um [ComputeNodeFillType][fill_type] igual a *Pack*, ela maximizaria o uso de todos os 16 núcleos de cada nó e permitiria que um [pool de dimensionamento automático](batch-automatic-scaling.md) removesse os nós não utilizados do pool (nós sem nenhuma tarefa atribuída). Isso minimiza o uso de recursos e economizando dinheiro.

## <a name="batch-net-example"></a>Exemplo de .NET do Lote
Este snippet de código da API [Batch .NET][api_net] mostra uma solicitação para criar um pool que contém quatro nós com um máximo de quatro tarefas por nó. Isso especifica uma política de agendamento de tarefas que preencherá cada nó com tarefas antes de atribuir tarefas a outro nó no pool. Para saber mais sobre como adicionar pools usando a API .NET do Lote, confira [BatchClient.PoolOperations.CreatePool][poolcreate_net].

```csharp
CloudPool pool =
    batchClient.PoolOperations.CreatePool(
        poolId: "mypool",
        targetDedicatedComputeNodes: 4
        virtualMachineSize: "standard_d1_v2",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));

pool.MaxTasksPerComputeNode = 4;
pool.TaskSchedulingPolicy = new TaskSchedulingPolicy(ComputeNodeFillType.Pack);
pool.Commit();
```

## <a name="batch-rest-example"></a>Exemplo REST do Lote
Esse snippet da API [REST do Lote][api_rest] mostra uma solicitação para criar um pool com dois nós grandes, com um máximo de quatro tarefas por nó. Para obter mais informações sobre como adicionar pools usando a API REST, consulte [Adicionar um pool a uma conta][rest_addpool].

```json
{
  "odata.metadata":"https://myaccount.myregion.batch.azure.com/$metadata#pools/@Element",
  "id":"mypool",
  "vmSize":"large",
  "cloudServiceConfiguration": {
    "osFamily":"4",
    "targetOSVersion":"*",
  }
  "targetDedicatedComputeNodes":2,
  "maxTasksPerNode":4,
  "enableInterNodeCommunication":true,
}
```

> [!NOTE]
> Você pode definir o elemento `maxTasksPerNode` e a propriedade [MaxTasksPerComputeNode][maxtasks_net] apenas no momento da criação do pool. Eles não poderão ser modificados após a criação do pool.
>
>

## <a name="code-sample"></a>Exemplo de código
O projeto [ParallelNodeTasks][parallel_tasks_sample] no GitHub ilustra o uso da propriedade [CloudPool.MaxTasksPerComputeNode][maxtasks_net].

Este aplicativo de console C# usa a biblioteca [.NET do Lote][api_net] para criar um pool com um ou mais nós de computação. Ele executa um número configurável de tarefas nesses nós para simular uma carga variável. A saída do aplicativo especifica os nós executados em cada tarefa. O aplicativo também fornece um resumo dos parâmetros do trabalho e a duração. A parte de resumo da saída de duas execuções diferentes do aplicativo de exemplo é exibida abaixo.

```
Nodes: 1
Node size: large
Max tasks per node: 1
Tasks: 32
Duration: 00:30:01.4638023
```

A primeira execução do aplicativo de exemplo mostra que com um único nó no pool, e a configuração padrão de uma tarefa por nó, a duração do trabalho será superior a 30 minutos.

```
Nodes: 1
Node size: large
Max tasks per node: 4
Tasks: 32
Duration: 00:08:48.2423500
```

A segunda execução do exemplo mostra uma redução significativa na duração do trabalho. Isso ocorre porque o pool foi configurado com quatro tarefas por nó, o que permite que a execução paralela de tarefas conclua o trabalho em aproximadamente um quarto do tempo.

> [!NOTE]
> As durações de trabalho nos resumos acima não incluem o tempo de criação do pool. Cada um dos trabalhos acima foi enviado para pools criados anteriormente e cujos nós de computação estavam no estado *Ocioso* na hora do envio.
>
>

## <a name="next-steps"></a>Próximos passos
### <a name="batch-explorer-heat-map"></a>Mapa de Calor do Explorador do Lote
[O Explorador de lotes][batch_labs] é uma ferramenta de cliente autônomo e gratuita para ajudar a criar, depurar e monitorar aplicativos em lote do Azure. O Batch Explorer contém um recurso de *Mapa de Calor* que fornece visualização da execução da tarefa. Ao executar o aplicativo de exemplo [ParallelTasks][parallel_tasks_sample], você pode usar o recurso Mapa de Calor para visualizar facilmente a execução das tarefas paralelas em cada nó.


[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: https://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_labs]: https://azure.github.io/BatchExplorer/
[cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[enable_autoscaling]: https://msdn.microsoft.com/library/azure/dn820173.aspx
[fill_type]: https://msdn.microsoft.com/library/microsoft.azure.batch.common.computenodefilltype.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[maxtasks_net]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.maxtaskspercomputenode.aspx
[rest_addpool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[parallel_tasks_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ParallelTasks
[poolcreate_net]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx
[task_schedule]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudpool.taskschedulingpolicy.aspx

