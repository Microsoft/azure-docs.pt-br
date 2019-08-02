---
title: Executar tarefas em paralelo para usar recursos de computação com eficiência - Lote do Azure | Microsoft Docs
description: Aumente a eficiência e reduza os custos usando menos nós de computação e executando tarefas simultâneas em cada nó em um pool do Lote do Azure
services: batch
documentationcenter: .net
author: laurenhughes
manager: gwallace
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
ms.openlocfilehash: cc6a607da2227ecf9acd6209e31b7aa0ef1c62d8
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68323374"
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
Você configura os nós de computação para a execução paralela das tarefas no nível do pool. Com a biblioteca .net do lote, defina o elemento [CloudPool. MaxTasksPerComputeNode][maxtasks_net] property when you create a pool. If you are using the Batch REST API, set the [maxTasksPerNode][rest_addpool] no corpo da solicitação durante a criação do pool.

O lote do Azure permite que você defina tarefas por nó até (4x) o número de nós principais. Por exemplo, se o pool estiver configurado com nós de tamanho "Grande" (quatro núcleos), será possível definir `maxTasksPerNode` como 16. No entanto, independentemente de quantos núcleos o nó tem, você não pode ter mais de 256 tarefas por nó. Para obter detalhes sobre o número de núcleos para cada um dos tamanhos de nó, confira [Tamanhos para Serviços de Nuvem](../cloud-services/cloud-services-sizes-specs.md). Para saber mais sobre limites de serviço, confira [Cotas e limites para o serviço de Lote do Azure](batch-quota-limit.md).

> [!TIP]
> Certifique-se de levar em conta `maxTasksPerNode` o valor ao construir uma [fórmula de dimensionamento automático][enable_autoscaling] para o pool. Por exemplo, uma fórmula que avalia `$RunningTasks` poderia ser drasticamente afetada por um aumento nas tarefas por nó. Consulte [Dimensionar automaticamente nós de computação em um pool do Lote do Azure](batch-automatic-scaling.md) para saber mais.
>
>

## <a name="distribution-of-tasks"></a>Distribuição de tarefas
Quando os nós de computação em um pool puderem executar as tarefas simultaneamente, será importante especificar como você deseja distribuir suas tarefas nos nós no pool.

Usando a propriedade [CloudPool. TaskSchedulingPolicy][task_schedule] , você pode especificar que as tarefas devem ser atribuídas uniformemente em todos os nós no pool ("difusão"). Ou você pode especificar que o máximo possível de tarefas deve ser atribuído a cada nó antes de as tarefas serem atribuídas a outro nó no pool ("remessa").

Como um exemplo de como esse recurso é valioso, considere o pool de [nós\_padrão D14](../cloud-services/cloud-services-sizes-specs.md) (no exemplo acima) configurado com um [CloudPool. MaxTasksPerComputeNode][maxtasks_net] value of 16. If the [CloudPool.TaskSchedulingPolicy][task_schedule] é configurado com um [ ComputeNodeFillType][Fill_type] do *pacote*, ele maximizaria o uso de todos os 16 núcleos de cada nó e permitiria que um [pool](batch-automatic-scaling.md) de dimensionamento automático removesse nós não utilizados do pool (nós sem nenhuma tarefa atribuída). Isso minimiza o uso de recursos e economizando dinheiro.

## <a name="batch-net-example"></a>Exemplo de .NET do Lote
Este [.net][api_net] API code snippet shows a request to create a pool that contains four nodes with a maximum of four tasks per node. It specifies a task scheduling policy that will fill each node with tasks prior to assigning tasks to another node in the pool. For more information on adding pools by using the Batch .NET API, see [BatchClient.PoolOperations.CreatePool][poolcreate_net]do lote.

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
Este [lote restante][api_rest] API snippet shows a request to create a pool that contains two large nodes with a maximum of four tasks per node. For more information on adding pools by using the REST API, see [Add a pool to an account][rest_addpool].

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
> Você pode definir o `maxTasksPerNode` elemento e a propriedade [MaxTasksPerComputeNode][maxtasks_net] somente no momento da criação do pool. Eles não poderão ser modificados após a criação do pool.
>
>

## <a name="code-sample"></a>Exemplo de código
A propriedade [ParallelNodeTasks][parallel_tasks_sample] project on GitHub illustrates the use of the [CloudPool.MaxTasksPerComputeNode][maxtasks_net] .

Esse C# aplicativo de console usa a biblioteca [.net do lote][api_net] para criar um pool com um ou mais nós de computação. Ele executa um número configurável de tarefas nesses nós para simular uma carga variável. A saída do aplicativo especifica os nós executados em cada tarefa. O aplicativo também fornece um resumo dos parâmetros do trabalho e a duração. A parte de resumo da saída de duas execuções diferentes do aplicativo de exemplo é exibida abaixo.

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

## <a name="next-steps"></a>Próximas etapas
### <a name="batch-explorer-heat-map"></a>Mapa de Calor do Explorador do Lote
[O Batch Explorer][batch_labs] é uma ferramenta cliente autônoma, rica e exclusiva para ajudar a criar, depurar e monitorar aplicativos em lote do Azure. O Batch Explorer contém um recurso de *Mapa de Calor* que fornece visualização da execução da tarefa. Quando estiver executando o aplicativo de exemplo [ParallelTasks][parallel_tasks_sample] , você poderá usar o recurso de mapa de calor para visualizar facilmente a execução de tarefas paralelas em cada nó.


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

