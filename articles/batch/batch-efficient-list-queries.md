---
title: Projetar consultas de lista eficientes - Lote do Azure | Microsoft Docs
description: Aumente o desempenho filtrando suas consultas ao solicitar informações sobre os recursos do Lote, como pools, trabalhos, tarefas e nós de computação.
services: batch
documentationcenter: .net
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: 031fefeb-248e-4d5a-9bc2-f07e46ddd30d
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 12/07/2018
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: ff3e95a603b8f9a188c7839578cd12287935de90
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60778247"
---
# <a name="create-queries-to-list-batch-resources-efficiently"></a>Criar consultas para listar recursos do Lote com eficiência

Aqui, você aprenderá a melhorar o desempenho do aplicativo do Lote do Azure reduzindo a quantidade de dados retornados pelo serviço quando consulta trabalhos, tarefas, nós de computação e outros recursos com a biblioteca [.NET do Lote][api_net].

Quase todos os aplicativos do Lote precisam executar algum tipo de monitoramento ou outra operação que consulta o serviço de Lote, geralmente em intervalos regulares. Por exemplo, para determinar se há qualquer tarefa em fila restante em um trabalho, você deve obter dados sobre cada tarefa no trabalho. Para determinar o status de nós em seu pool, você deve obter os dados em cada nó no pool. Este artigo explica como executar essas consultas da forma mais eficiente.

> [!NOTE]
> O serviço de Lote fornece suporte de API especial para o cenário comum de tarefas de contagem em um trabalho e contagem de nós de computação no pool do Lote. Em vez de usar uma consulta de lista para elas, você pode chamar as operações [Obter Contagens de Tarefas][rest_get_task_counts] e [Listar Contagens de Nós do Pool][rest_get_node_counts]. Essas operações são mais eficientes do que uma consulta de lista, mas retornam informações mais limitadas. Consulte [Contar tarefas e nós de computação por estado](batch-get-resource-counts.md). 


## <a name="meet-the-detaillevel"></a>Atender DetailLevel
Em um aplicativo do Lote de produção, as entidades, como trabalhos, tarefas e nós de computação, podem chegar a milhares. Quando você solicita informações sobre esses recursos, uma quantidade de dados potencialmente grande deve "cruzar a transmissão" do serviço do Lote para seu aplicativo em cada consulta. Limitando o número de itens e o tipo de informação retornada por uma consulta, você pode aumentar a velocidade de suas consultas e, portanto, o desempenho de seu aplicativo.

Esse snippet de código da API do [.NET do Lote][api_net] lista *todas* as tarefas associadas a um trabalho, bem como *todas* as propriedades de cada tarefa:

```csharp
// Get a collection of all of the tasks and all of their properties for job-001
IPagedEnumerable<CloudTask> allTasks =
    batchClient.JobOperations.ListTasks("job-001");
```

Porém, você pode executar uma consulta de lista muito mais eficiente aplicando um "nível de detalhe" à sua consulta. Você faz isso fornecendo um objeto [ODATADetailLevel][odata] ao método [JobOperations.ListTasks][net_list_tasks]. Este snippet de código retorna apenas a ID, a linha de comando e as propriedades de informações do nó de computação das tarefas concluídas:

```csharp
// Configure an ODATADetailLevel specifying a subset of tasks and
// their properties to return
ODATADetailLevel detailLevel = new ODATADetailLevel();
detailLevel.FilterClause = "state eq 'completed'";
detailLevel.SelectClause = "id,commandLine,nodeInfo";

// Supply the ODATADetailLevel to the ListTasks method
IPagedEnumerable<CloudTask> completedTasks =
    batchClient.JobOperations.ListTasks("job-001", detailLevel);
```

Neste cenário do exemplo, se houver milhares de tarefas no trabalho, os resultados da segunda consulta serão retornados normalmente muito mais rapidamente do que os da primeira. Mais informações sobre como usar o ODATADetailLevel ao listar os itens com a API do Lote .NET são incluídas [abaixo](#efficient-querying-in-batch-net).

> [!IMPORTANT]
> Recomendamos que você *sempre* forneça um objeto ODATADetailLevel para as chamadas de lista da API .NET para garantir a máxima eficiência e desempenho de seu aplicativo. Ao especificar um nível de detalhe, você ajuda a reduzir os tempos de resposta do serviço Lote, a melhorar a utilização da rede e a minimizar o uso da memória por aplicativos clientes.
> 
> 

## <a name="filter-select-and-expand"></a>Filtrar, selecionar e expandir
As APIs [.NET do Lote][api_net] e [REST do Lote][api_rest] permitem reduzir o número de itens retornados em uma lista e a quantidade de informações retornadas para cada um. Você faz isso especificando **filter**, **select** e **expand strings** ao executar as consultas da lista.

### <a name="filter"></a>Filter
A cadeia de caracteres filter é uma expressão que reduz o número de itens retornados. Por exemplo, liste somente as tarefas em execução para um trabalho, ou liste apenas nós de computação que estejam prontos para executar tarefas.

* A cadeia de caracteres filter consiste em uma ou mais expressões, em que uma expressão consiste em um nome de propriedade, um operador e um valor. As propriedades que podem ser especificadas são específicas a cada tipo de entidade consultado, assim como os operadores com suporte para cada propriedade.
* Várias expressões podem ser combinadas usando os operadores lógicos `and` e `or`.
* Esta cadeia de caracteres filter de exemplo lista apenas as tarefas de “renderização” em execução: `(state eq 'running') and startswith(id, 'renderTask')`.

### <a name="select"></a>Selecionar
A cadeia de caracteres select limita os valores de propriedade retornados para cada item. Especifique uma lista de nomes de propriedade e apenas os valores dessas propriedades retornarão para os itens nos resultados da consulta.

* A cadeia de caracteres de seleção consiste em uma lista separada por vírgulas de nomes de propriedade. Você pode especificar qualquer uma das propriedades para o tipo de entidade que você está consultando.
* Esta cadeia de caracteres select de exemplo especifica que apenas três valores da propriedade devem ser retornados para cada tarefa: `id, state, stateTransitionTime`.

### <a name="expand"></a>Expanda
A cadeia de caracteres expand reduz o número de chamadas de API necessárias para obter determinadas informações. Quando você usa uma cadeia de caracteres expand, é possível obter mais informações sobre cada item com uma única chamada de API. Em vez de obter primeiro a lista de entidades para depois solicitar informações para cada item na lista, você pode usar uma cadeia de caracteres expand para obter as mesmas informações em uma única chamada de API. Menos chamadas de API significam um desempenho melhor.

* Assim como a cadeia de caracteres de seleção, a cadeia de caracteres de expansão controla se determinados dados são incluídos nos resultados da consulta de lista.
* A cadeia de caracteres de expansão tem suporte apenas quando ela é usada na listagem de trabalhos, agendas de trabalho, tarefas e pools. Atualmente, dá suporte apenas a informações de estatísticas.
* Quando todas as propriedades forem necessárias e nenhuma cadeia de seleção tiver sido especificada, a cadeia de expansão *terá* que ser usada para obter informações estatísticas. Se uma cadeia de caracteres select for usada para obter um subconjunto de propriedades, `stats` poderá ser especificado na cadeia select e a cadeia de caracteres expand não precisará ser especificada.
* Esta cadeia de caracteres expand de exemplo especifica que as informações de estatística devem ser retornadas para cada item na lista: `stats`.

> [!NOTE]
> Ao criar qualquer um dos três tipos de cadeias de caracteres de consulta (filter, select e expand), você deve garantir que os nomes de propriedade e caso correspondam aos seus equivalentes de elemento da API REST. Por exemplo, ao trabalhar com a classe [CloudTask](/dotnet/api/microsoft.azure.batch.cloudtask#microsoft_azure_batch_cloudtask) do .NET, você deve especificar **state** em vez de **State**, mesmo que a propriedade .NET seja [CloudTask.State](/dotnet/api/microsoft.azure.batch.cloudtask#microsoft_azure_batch_cloudtask.state). Consulte as tabelas abaixo para ver mapeamentos de propriedade entre o .NET e APIs REST.
> 
> 

### <a name="rules-for-filter-select-and-expand-strings"></a>Especificações das cadeias de caracteres filter, select e expand
* Os nomes das propriedades nas cadeias de caracteres filter, select e expand devem aparecer como na API [REST do Lote][api_rest] – mesmo quando você usa o [.NET do Lote][api_net] ou um dos outros SDKs do Lote.
* Todos os nomes de propriedade diferenciam maiúsculas de minúsculas, mas valores de propriedade não diferenciam maiúsculas de minúsculas.
* As cadeias de caracteres de data/hora podem ter um dos dois formatos e devem ser precedidas por `DateTime`.
  
  * Exemplo de formato W3C-DTF: `creationTime gt DateTime'2011-05-08T08:49:37Z'`
  * Exemplo de formato RFC 1123: `creationTime gt DateTime'Sun, 08 May 2011 08:49:37 GMT'`
* As cadeias de caracteres boolianas são `true` ou `false`.
* Se uma propriedade ou operador inválido for especificado, o resultado será um erro `400 (Bad Request)` .

## <a name="efficient-querying-in-batch-net"></a>Consulta eficiente no .NET de Lote
Na API [.NET do Lote][api_net], a classe [ODATADetailLevel][odata] é usada para fornecimento de cadeias de caracteres filter, select e expand para operações de lista. A classe ODataDetailLevel tem três propriedades de cadeia de caracteres públicas que podem ser especificadas no construtor ou definidas diretamente. Em seguida, você passa o objeto ODataDetailLevel como um parâmetro para as várias operações da lista, como [ListPools][net_list_pools], [ListJobs][net_list_jobs] e [ListTasks][net_list_tasks].

* [ODATADetailLevel][odata].[FilterClause][odata_filter]: Limite o número de itens retornados.
* [ODATADetailLevel][odata].[SelectClause][odata_select]: Especifique quais valores de propriedade retornam com cada item.
* [ODATADetailLevel][odata].[ExpandClause][odata_expand]: Recupere dados de todos os itens em uma única chamada à API, em vez de chamadas separadas para cada item.

O snippet de código abaixo usa a API de Lote .NET para consultar o serviço de Lote com eficiência a fim de obter as estatísticas de um conjunto específico de pools. Nesse cenário, o usuário de Lote tem grupos de teste e de produção. As IDs do pool de teste são prefixadas com "test", e as IDs do grupo de produção são prefixadas com "prod". No snippet de código, *myBatchClient* é uma instância devidamente inicializada da classe [BatchClient](/dotnet/api/microsoft.azure.batch.batchclient#microsoft_azure_batch_batchclient).

```csharp
// First we need an ODATADetailLevel instance on which to set the filter, select,
// and expand clause strings
ODATADetailLevel detailLevel = new ODATADetailLevel();

// We want to pull only the "test" pools, so we limit the number of items returned
// by using a FilterClause and specifying that the pool IDs must start with "test"
detailLevel.FilterClause = "startswith(id, 'test')";

// To further limit the data that crosses the wire, configure the SelectClause to
// limit the properties that are returned on each CloudPool object to only
// CloudPool.Id and CloudPool.Statistics
detailLevel.SelectClause = "id, stats";

// Specify the ExpandClause so that the .NET API pulls the statistics for the
// CloudPools in a single underlying REST API call. Note that we use the pool's
// REST API element name "stats" here as opposed to "Statistics" as it appears in
// the .NET API (CloudPool.Statistics)
detailLevel.ExpandClause = "stats";

// Now get our collection of pools, minimizing the amount of data that is returned
// by specifying the detail level that we configured above
List<CloudPool> testPools =
    await myBatchClient.PoolOperations.ListPools(detailLevel).ToListAsync();
```

> [!TIP]
> Uma instância de [ODATADetailLevel][odata] configurada com as cláusulas Select e Expand também pode ser passada para os devidos métodos Get, como [PoolOperations.GetPool](/dotnet/api/microsoft.azure.batch.pooloperations#Microsoft_Azure_Batch_PoolOperations_GetPool_System_String_Microsoft_Azure_Batch_DetailLevel_System_Collections_Generic_IEnumerable_Microsoft_Azure_Batch_BatchClientBehavior__), para limitar a quantidade de dados retornados.
> 
> 

## <a name="batch-rest-to-net-api-mappings"></a>Mapeamentos REST Batch para API .NET
Os nomes de propriedade nas cadeias de caracteres de filtro, seleção e expansão *devem* refletir os seus equivalentes da API REST, no nome e no caso. As tabelas a seguir fornecem os mapeamentos entre os equivalentes API .NET e REST.

### <a name="mappings-for-filter-strings"></a>Mapeamentos para cadeias de caracteres de filtro
* **Métodos da lista .NET**: cada um dos métodos da API .NET nesta coluna aceita um objeto [ODATADetailLevel][odata] como parâmetro.
* **Solicitações da lista REST**: cada página da API REST associada a essa coluna contém uma tabela especificando as propriedades e as operações permitidas nas cadeias de caracteres *filter*. Você usará esses nomes de propriedade e operações ao construir uma cadeia de caracteres [ODATADetailLevel.FilterClause][odata_filter].

| Métodos da lista .NET | Solicitações da lista REST |
| --- | --- |
| [CertificateOperations.ListCertificates][net_list_certs] |[Listar os certificados de uma conta][rest_list_certs] |
| [CloudTask.ListNodeFiles][net_list_task_files] |[Listar os arquivos associados a uma tarefa][rest_list_task_files] |
| [JobOperations.ListJobPreparationAndReleaseTaskStatus][net_list_jobprep_status] |[Listar o status das tarefas de preparação e liberação de trabalho para um determinado trabalho][rest_list_jobprep_status] |
| [JobOperations.ListJobs][net_list_jobs] |[Listar os trabalhos em uma conta][rest_list_jobs] |
| [JobOperations.ListNodeFiles][net_list_nodefiles] |[Listar os arquivos em um nó][rest_list_nodefiles] |
| [JobOperations.ListTasks][net_list_tasks] |[Listar as tarefas associadas a um trabalho][rest_list_tasks] |
| [JobScheduleOperations.ListJobSchedules][net_list_job_schedules] |[Listar os cronogramas de trabalho em uma conta][rest_list_job_schedules] |
| [JobScheduleOperations.ListJobs][net_list_schedule_jobs] |[Listar os trabalhos associados a um cronograma de trabalho][rest_list_schedule_jobs] |
| [PoolOperations.ListComputeNodes][net_list_compute_nodes] |[Listar os nós de computação em um pool][rest_list_compute_nodes] |
| [PoolOperations.ListPools][net_list_pools] |[Listar os pools em uma conta][rest_list_pools] |

### <a name="mappings-for-select-strings"></a>Mapeamentos para cadeias de caracteres de seleção
* **Tipos .NET do Lote**: Tipos de API .NET do Lote.
* **Entidades da API REST**: cada página nesta coluna contém uma ou mais tabelas que listam os nomes de propriedade da API REST para o tipo. Esses nomes de propriedade são usados ao construir as cadeias de caracteres *select* . Você usará esses mesmos nomes de propriedade ao construir uma cadeia de caracteres [ODATADetailLevel.SelectClause][odata_select].

| Tipos de Lote .NET | Entidades da API REST |
| --- | --- |
| [Certificate][net_cert] |[Obter informações sobre um certificado][rest_get_cert] |
| [CloudJob][net_job] |[Obter informações sobre um trabalho][rest_get_job] |
| [CloudJobSchedule][net_schedule] |[Obter informações sobre um cronograma de trabalho][rest_get_schedule] |
| [ComputeNode][net_node] |[Obter informações sobre um nó][rest_get_node] |
| [CloudPool][net_pool] |[Obter informações sobre um pool][rest_get_pool] |
| [CloudTask][net_task] |[Obter informações sobre uma tarefa][rest_get_task] |

## <a name="example-construct-a-filter-string"></a>Exemplo: construir uma cadeia de caracteres filter
Ao construir uma cadeia de caracteres filter para [ODATADetailLevel.FilterClause][odata_filter], consulte a tabela acima em “Mapeamentos para cadeias de caracteres filter” para localizar a página da documentação da API REST correspondente à operação de lista que você deseja executar. Você encontrará as propriedades e os operadores com suporte na primeira tabela com várias linhas nessa página. Se quiser recuperar todas as tarefas cujo código de saída era diferente de zero, por exemplo, essa linha em [Listar as tarefas associadas a um trabalho][rest_list_tasks] especificará a cadeia de caracteres da propriedade aplicável e os operadores permitidos:

| Propriedade | Operações permitidas | Type |
|:--- |:--- |:--- |
| `executionInfo/exitCode` |`eq, ge, gt, le , lt` |`Int` |

Assim, a cadeia de caracteres de filtro para listar todas as tarefas com um código de saída diferente de zero deve ser:

`(executionInfo/exitCode lt 0) or (executionInfo/exitCode gt 0)`

## <a name="example-construct-a-select-string"></a>Exemplo: construir uma cadeia de caracteres select
Para construir um [ODATADetailLevel.SelectClause][odata_select], consulte a tabela acima em “Mapeamentos para as cadeias de caracteres select” e navegue até a página da API REST correspondente ao tipo de entidade listada. Você encontrará as propriedades selecionáveis e os operadores com suporte na primeira tabela de várias linha nessa página. Se quiser recuperar apenas a ID e a linha de comando de cada tarefa em uma lista, por exemplo, você encontrará essas linhas na tabela aplicável em [Obter informações sobre uma tarefa][rest_get_task]:

| Propriedade | Type | Observações |
|:--- |:--- |:--- |
| `id` |`String` |`The ID of the task.` |
| `commandLine` |`String` |`The command line of the task.` |

A cadeia de caracteres de seleção para incluir somente a ID e a linha de comando com cada tarefa listada seria:

`id, commandLine`

## <a name="code-samples"></a>Exemplos de código
### <a name="efficient-list-queries-code-sample"></a>Exemplo de código de consultas de lista eficientes
Confira o projeto de exemplo [EfficientListQueries][efficient_query_sample] no GitHub para ver como uma consulta de lista eficaz pode afetar o desempenho de um aplicativo. Esse aplicativo de console em C# cria e adiciona um grande número de tarefas a um trabalho. Em seguida, ele faz várias chamadas para o método [JobOperations.ListTasks][net_list_tasks] e passa objetos [ODATADetailLevel][odata] que são configurados com valores de propriedade diferentes para variar a quantidade de dados a serem retornados. Ele produz uma saída semelhante à seguinte:

```
Adding 5000 tasks to job jobEffQuery...
5000 tasks added in 00:00:47.3467587, hit ENTER to query tasks...

4943 tasks retrieved in 00:00:04.3408081 (ExpandClause:  | FilterClause: state eq 'active' | SelectClause: id,state)
0 tasks retrieved in 00:00:00.2662920 (ExpandClause:  | FilterClause: state eq 'running' | SelectClause: id,state)
59 tasks retrieved in 00:00:00.3337760 (ExpandClause:  | FilterClause: state eq 'completed' | SelectClause: id,state)
5000 tasks retrieved in 00:00:04.1429881 (ExpandClause:  | FilterClause:  | SelectClause: id,state)
5000 tasks retrieved in 00:00:15.1016127 (ExpandClause:  | FilterClause:  | SelectClause: id,state,environmentSettings)
5000 tasks retrieved in 00:00:17.0548145 (ExpandClause: stats | FilterClause:  | SelectClause: )

Sample complete, hit ENTER to continue...
```

Como mostrado nos tempos transcorridos, você pode diminuir muito os tempos de resposta da consulta limitando as propriedades e o número de itens retornados. Você pode encontrar esse e outros exemplos de projetos no repositório [azure-batch-samples][github_samples] no GitHub.

### <a name="batchmetrics-library-and-code-sample"></a>Biblioteca BatchMetrics e exemplo de código
Além do exemplo de código EfficientListQueries acima, você pode encontrar o projeto [BatchMetrics][batch_metrics] no repositório GitHub [azure-batch-samples][github_samples]. O projeto de exemplo BatchMetrics demonstra como monitorar com eficiência o andamento do trabalho de Lote do Azure usando a API do Lote.

O exemplo [BatchMetrics][batch_metrics] inclui um projeto da biblioteca de classes .NET que você pode incorporar a seus próprios projetos e um programa de linha de comando simples para treinar e demonstrar o uso da biblioteca.

O aplicativo de exemplo no projeto demonstra as seguintes operações:

1. Selecionando atributos específicos para baixar somente as propriedades necessárias
2. Filtrando os tempos de transição do estado para baixar somente as alterações desde a última consulta

Por exemplo, o método a seguir aparece na biblioteca BatchMetrics. Ele retorna um ODATADetailLevel que especifica que somente as propriedades `id` e `state` devem ser obtidas para as entidades consultadas. Também especifica que apenas as entidades cujo estado foi alterado desde o parâmetro `DateTime` especificado devem ser retornadas.

```csharp
internal static ODATADetailLevel OnlyChangedAfter(DateTime time)
{
    return new ODATADetailLevel(
        selectClause: "id, state",
        filterClause: string.Format("stateTransitionTime gt DateTime'{0:o}'", time)
    );
}
```

## <a name="next-steps"></a>Próximas etapas
### <a name="parallel-node-tasks"></a>Tarefas paralelas do nó
[Maximizar o uso de recursos de computação do Lote do Azure com tarefas de nó simultâneas](batch-parallel-node-tasks.md) é outro artigo relacionado ao desempenho de aplicativos do Lote. Alguns tipos de cargas de trabalho podem aproveitar a execução de tarefas paralelas nos nós de computação maiores, porém em menor número. Consulte o [cenário de exemplo](batch-parallel-node-tasks.md#example-scenario) no artigo para obter detalhes sobre esse cenário.


[api_net]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch
[api_net_listjobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[api_rest]: https://docs.microsoft.com/rest/api/batchservice/
[batch_metrics]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchMetrics
[efficient_query_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/EfficientListQueries
[github_samples]: https://github.com/Azure/azure-batch-samples
[odata]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.aspx
[odata_ctor]: https://msdn.microsoft.com/library/azure/dn866178.aspx
[odata_expand]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.expandclause.aspx
[odata_filter]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.filterclause.aspx
[odata_select]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.selectclause.aspx

[net_list_certs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificateoperations.listcertificates.aspx
[net_list_compute_nodes]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listcomputenodes.aspx
[net_list_job_schedules]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobschedules.aspx
[net_list_jobprep_status]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobpreparationandreleasetaskstatus.aspx
[net_list_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[net_list_nodefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listnodefiles.aspx
[net_list_pools]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listpools.aspx
[net_list_schedule_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobs.aspx
[net_list_task_files]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listnodefiles.aspx
[net_list_tasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listtasks.aspx

[rest_list_certs]: https://msdn.microsoft.com/library/azure/dn820154.aspx
[rest_list_compute_nodes]: https://msdn.microsoft.com/library/azure/dn820159.aspx
[rest_list_job_schedules]: https://msdn.microsoft.com/library/azure/mt282174.aspx
[rest_list_jobprep_status]: https://msdn.microsoft.com/library/azure/mt282170.aspx
[rest_list_jobs]: https://msdn.microsoft.com/library/azure/dn820117.aspx
[rest_list_nodefiles]: https://msdn.microsoft.com/library/azure/dn820151.aspx
[rest_list_pools]: https://msdn.microsoft.com/library/azure/dn820101.aspx
[rest_list_schedule_jobs]: https://msdn.microsoft.com/library/azure/mt282169.aspx
[rest_list_task_files]: https://msdn.microsoft.com/library/azure/dn820142.aspx
[rest_list_tasks]: https://msdn.microsoft.com/library/azure/dn820187.aspx

[rest_get_cert]: https://msdn.microsoft.com/library/azure/dn820176.aspx
[rest_get_job]: https://msdn.microsoft.com/library/azure/dn820106.aspx
[rest_get_node]: https://msdn.microsoft.com/library/azure/dn820168.aspx
[rest_get_pool]: https://msdn.microsoft.com/library/azure/dn820165.aspx
[rest_get_schedule]: https://msdn.microsoft.com/library/azure/mt282171.aspx
[rest_get_task]: https://msdn.microsoft.com/library/azure/dn820133.aspx

[net_cert]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificate.aspx
[net_job]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_node]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.aspx
[net_pool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_schedule]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjobschedule.aspx
[net_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx

[rest_get_task_counts]: /rest/api/batchservice/get-the-task-counts-for-a-job
[rest_get_node_counts]: /rest/api/batchservice/account/listpoolnodecounts
