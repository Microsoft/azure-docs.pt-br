---
title: Projetar consultas de lista eficientes
description: Aumente o desempenho filtrando suas consultas ao solicitar informações sobre os recursos do Lote, como pools, trabalhos, tarefas e nós de computação.
ms.topic: how-to
ms.date: 06/18/2020
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 3a767cc8ae3c8c48e1e40e0735c33fa807ba0015
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88933507"
---
# <a name="create-queries-to-list-batch-resources-efficiently"></a>Criar consultas para listar recursos do Lote com eficiência

Quase todos os aplicativos do Lote precisam executar algum tipo de monitoramento ou outra operação que consulta o serviço de Lote, geralmente em intervalos regulares. Por exemplo, para determinar se há qualquer tarefa em fila restante em um trabalho, você deve obter dados sobre cada tarefa no trabalho. Para determinar o status de nós em seu pool, você deve obter os dados em cada nó no pool. Este artigo explica como executar essas consultas da forma mais eficiente.

Você pode aumentar o desempenho do aplicativo do lote do Azure reduzindo a quantidade de dados retornados pelo serviço ao consultar trabalhos, tarefas, nós de computação e outros recursos com a biblioteca [.net do lote](/dotnet/api/microsoft.azure.batch) .

> [!NOTE]
> O serviço de lote fornece suporte de API para os cenários comuns de contagem de tarefas em um trabalho e a contagem de nós de computação no pool do lote. Em vez de usar uma consulta de lista para elas, você pode chamar as operações [Obter Contagens de Tarefas](/rest/api/batchservice/job/gettaskcounts) e [Listar Contagens de Nós do Pool](/rest/api/batchservice/account/listpoolnodecounts). Essas operações são mais eficientes do que uma consulta de lista, mas retornam informações mais limitadas que talvez não estejam sempre atualizadas. Para obter mais informações, consulte [contar tarefas e nós de computação por estado](batch-get-resource-counts.md).

## <a name="specify-a-detail-level"></a>Especificar um nível de detalhe

Em um aplicativo do Lote de produção, as entidades, como trabalhos, tarefas e nós de computação, podem chegar a milhares. Quando você solicita informações sobre esses recursos, uma quantidade de dados potencialmente grande deve "cruzar a transmissão" do serviço do Lote para seu aplicativo em cada consulta. Limitando o número de itens e o tipo de informação retornada por uma consulta, você pode aumentar a velocidade de suas consultas e, portanto, o desempenho de seu aplicativo.

Esse snippet de código da API [.NET do Lote](/dotnet/api/microsoft.azure.batch) lista *todas* as tarefas associadas a um trabalho, bem como *todas* as propriedades de cada tarefa:

```csharp
// Get a collection of all of the tasks and all of their properties for job-001
IPagedEnumerable<CloudTask> allTasks =
    batchClient.JobOperations.ListTasks("job-001");
```

Porém, você pode executar uma consulta de lista muito mais eficiente aplicando um "nível de detalhe" à sua consulta. Você faz isso fornecendo um objeto [ODATADetailLevel](/dotnet/api/microsoft.azure.batch.odatadetaillevel) ao método [JobOperations.ListTasks](/dotnet/api/microsoft.azure.batch.joboperations). Este snippet de código retorna apenas a ID, a linha de comando e as propriedades de informações do nó de computação das tarefas concluídas:

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
> Recomendamos que você sempre forneça um objeto ODATADetailLevel para as chamadas de lista da API .NET para garantir a máxima eficiência e desempenho de seu aplicativo. Ao especificar um nível de detalhe, você ajuda a reduzir os tempos de resposta do serviço Lote, a melhorar a utilização da rede e a minimizar o uso da memória por aplicativos clientes.

## <a name="filter-select-and-expand"></a>Filtrar, selecionar e expandir

As APIs [.NET do Lote](/dotnet/api/microsoft.azure.batch) e [REST do Lote](/rest/api/batchservice/) permitem reduzir o número de itens retornados em uma lista e a quantidade de informações retornadas para cada um. Você faz isso especificando **filter**, **select** e **expand strings** ao executar as consultas da lista.

### <a name="filter"></a>Filtrar

A cadeia de caracteres filter é uma expressão que reduz o número de itens retornados. Por exemplo, você pode listar apenas as tarefas em execução de um trabalho ou listar somente nós de computação que estão prontos para executar tarefas.

A cadeia de caracteres filter consiste em uma ou mais expressões, em que uma expressão consiste em um nome de propriedade, um operador e um valor. As propriedades que podem ser especificadas são específicas a cada tipo de entidade consultado, assim como os operadores com suporte para cada propriedade.  Várias expressões podem ser combinadas usando os operadores lógicos `and` e `or`.

Esta cadeia de caracteres filter de exemplo lista apenas as tarefas de “renderização” em execução: `(state eq 'running') and startswith(id, 'renderTask')`.

### <a name="select"></a>Selecionar

A cadeia de caracteres select limita os valores de propriedade retornados para cada item. Você especifica uma lista de nomes de propriedade separados por vírgulas e somente esses valores de propriedade são retornados para os itens nos resultados da consulta. Você pode especificar qualquer uma das propriedades para o tipo de entidade que você está consultando.

Esta cadeia de caracteres select de exemplo especifica que apenas três valores da propriedade devem ser retornados para cada tarefa: `id, state, stateTransitionTime`.

### <a name="expand"></a>Expanda

A cadeia de caracteres expand reduz o número de chamadas de API necessárias para obter determinadas informações. Quando você usa uma cadeia de caracteres expand, é possível obter mais informações sobre cada item com uma única chamada de API. Em vez de obter primeiro a lista de entidades e, em seguida, solicitar informações para cada item na lista, você usa uma cadeia de caracteres de expansão para obter as mesmas informações em uma única chamada à API, ajudando a melhorar o desempenho, reduzindo as chamadas à API.

Assim como a cadeia de caracteres de seleção, a cadeia de caracteres de expansão controla se determinados dados são incluídos nos resultados da consulta de lista. Quando todas as propriedades forem necessárias e nenhuma cadeia de seleção tiver sido especificada, a cadeia de expansão *terá* que ser usada para obter informações estatísticas. Se uma cadeia de caracteres select for usada para obter um subconjunto de propriedades, `stats` poderá ser especificado na cadeia select e a cadeia de caracteres expand não precisará ser especificada.

A cadeia de caracteres de expansão tem suporte apenas quando ela é usada na listagem de trabalhos, agendas de trabalho, tarefas e pools. Atualmente, dá suporte apenas a informações de estatísticas.

Esta cadeia de caracteres expand de exemplo especifica que as informações de estatística devem ser retornadas para cada item na lista: `stats`.

> [!NOTE]
> Ao criar qualquer um dos três tipos de cadeias de caracteres de consulta (filter, select e expand), você deve garantir que os nomes de propriedade e caso correspondam aos seus equivalentes de elemento da API REST. Por exemplo, ao trabalhar com a classe [CloudTask](/dotnet/api/microsoft.azure.batch.cloudtask) do .NET, você deve especificar **state** em vez de **State**, mesmo que a propriedade .NET seja [CloudTask.State](/dotnet/api/microsoft.azure.batch.cloudtask.state#Microsoft_Azure_Batch_CloudTask_State). Consulte as tabelas abaixo para ver mapeamentos de propriedade entre o .NET e APIs REST.

### <a name="rules-for-filter-select-and-expand-strings"></a>Especificações das cadeias de caracteres filter, select e expand

- Os nomes das propriedades nas cadeias de caracteres de filtro, seleção e expansão devem aparecer como na API [REST do lote](/rest/api/batchservice/) , mesmo quando você usa o [.net do lote](/dotnet/api/microsoft.azure.batch) ou um dos outros SDKs do lote.
- Todos os nomes de propriedade diferenciam maiúsculas de minúsculas, mas valores de propriedade não diferenciam maiúsculas de minúsculas.
- As cadeias de caracteres de data/hora podem ter um dos dois formatos e devem ser precedidas por `DateTime`.
  
  - Exemplo de formato W3C-DTF: `creationTime gt DateTime'2011-05-08T08:49:37Z'`
  - Exemplo de formato RFC 1123: `creationTime gt DateTime'Sun, 08 May 2011 08:49:37 GMT'`
- As cadeias de caracteres boolianas são `true` ou `false`.
- Se uma propriedade ou operador inválido for especificado, o resultado será um erro `400 (Bad Request)` .

## <a name="efficient-querying-in-batch-net"></a>Consulta eficiente no .NET de Lote

Na API [.NET do Lote](/dotnet/api/microsoft.azure.batch), a classe [ODATADetailLevel](/dotnet/api/microsoft.azure.batch.odatadetaillevel) é usada para fornecimento de cadeias de caracteres filter, select e expand para operações de lista. A classe ODataDetailLevel tem três propriedades de cadeia de caracteres públicas que podem ser especificadas no construtor ou definidas diretamente. Em seguida, você passa o objeto ODataDetailLevel como um parâmetro para as várias operações da lista, como [ListPools](/dotnet/api/microsoft.azure.batch.pooloperations), [ListJobs](/dotnet/api/microsoft.azure.batch.joboperations) e [ListTasks](/dotnet/api/microsoft.azure.batch.joboperations).

- [ODATADetailLevel. FilterClause](/dotnet/api/microsoft.azure.batch.odatadetaillevel.filterclause): limita o número de itens retornados.
- [ODATADetailLevel. SelectClause](/dotnet/api/microsoft.azure.batch.odatadetaillevel.selectclause): especifica quais valores de propriedade são retornados com cada item.
- [ODATADetailLevel. ExpandClause](/dotnet/api/microsoft.azure.batch.odatadetaillevel.expandclause): recupera dados para todos os itens em uma única chamada à API em vez de chamadas separadas para cada item.

O snippet de código abaixo usa a API de Lote .NET para consultar o serviço de Lote com eficiência a fim de obter as estatísticas de um conjunto específico de pools. Nesse cenário, o usuário de Lote tem grupos de teste e de produção. As IDs do pool de teste são prefixadas com "test", e as IDs do grupo de produção são prefixadas com "prod". No snippet de código, *myBatchClient* é uma instância devidamente inicializada da classe [BatchClient](/dotnet/api/microsoft.azure.batch.batchclient).

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
> Uma instância de [ODATADetailLevel](/dotnet/api/microsoft.azure.batch.odatadetaillevel) configurada com as cláusulas Select e Expand também pode ser passada para os devidos métodos Get, como [PoolOperations.GetPool](/dotnet/api/microsoft.azure.batch.pooloperations.getpool#Microsoft_Azure_Batch_PoolOperations_GetPool_System_String_Microsoft_Azure_Batch_DetailLevel_System_Collections_Generic_IEnumerable_Microsoft_Azure_Batch_BatchClientBehavior__), para limitar a quantidade de dados retornados.

## <a name="batch-rest-to-net-api-mappings"></a>Mapeamentos REST Batch para API .NET

Os nomes de propriedade nas cadeias de caracteres de filtro, seleção e expansão devem refletir os seus equivalentes da API REST, no nome e no caso. As tabelas a seguir fornecem os mapeamentos entre os equivalentes API .NET e REST.

### <a name="mappings-for-filter-strings"></a>Mapeamentos para cadeias de caracteres de filtro

- **Métodos da lista .NET**: cada um dos métodos da API .NET nesta coluna aceita um objeto [ODATADetailLevel](/dotnet/api/microsoft.azure.batch.odatadetaillevel) como parâmetro.
- **Solicitações da lista REST**: cada página da API REST associada a essa coluna contém uma tabela especificando as propriedades e as operações permitidas nas cadeias de caracteres *filter*. Você usa esses nomes de propriedade e operações ao construir uma cadeia de caracteres [ODATADetailLevel. FilterClause](/dotnet/api/microsoft.azure.batch.odatadetaillevel.filterclause) .

| Métodos da lista .NET | Solicitações da lista REST |
| --- | --- |
| [CertificateOperations.ListCertificates](/dotnet/api/microsoft.azure.batch.certificateoperations) |[Listar os certificados de uma conta](/rest/api/batchservice/certificate/list) |
| [CloudTask.ListNodeFiles](/dotnet/api/microsoft.azure.batch.cloudtask) |[Listar os arquivos associados a uma tarefa](/rest/api/batchservice/file/listfromtask) |
| [JobOperations.ListJobPreparationAndReleaseTaskStatus](/dotnet/api/microsoft.azure.batch.joboperations) |[Listar o status das tarefas de preparação e liberação de trabalho para um determinado trabalho](/rest/api/batchservice/job/listpreparationandreleasetaskstatus) |
| [JobOperations.ListJobs](/dotnet/api/microsoft.azure.batch.joboperations) |[Listar os trabalhos em uma conta](/rest/api/batchservice/job/list) |
| [JobOperations.ListNodeFiles](/dotnet/api/microsoft.azure.batch.joboperations) |[Listar os arquivos em um nó](/rest/api/batchservice/file/listfromcomputenode) |
| [JobOperations.ListTasks](/dotnet/api/microsoft.azure.batch.joboperations) |[Listar as tarefas associadas a um trabalho](/rest/api/batchservice/task/list) |
| [JobScheduleOperations.ListJobSchedules](/dotnet/api/microsoft.azure.batch.jobscheduleoperations) |[Listar os cronogramas de trabalho em uma conta](/rest/api/batchservice/jobschedule/list) |
| [JobScheduleOperations.ListJobs](/dotnet/api/microsoft.azure.batch.jobscheduleoperations) |[Listar os trabalhos associados a uma agenda de trabalho](/rest/api/batchservice/job/listfromjobschedule) |
| [PoolOperations.ListComputeNodes](/dotnet/api/microsoft.azure.batch.pooloperations) |[Listar os nós de computação em um pool](/rest/api/batchservice/computenode/list) |
| [PoolOperations.ListPools](/dotnet/api/microsoft.azure.batch.pooloperations) |[Listar os pools em uma conta](/rest/api/batchservice/pool/list) |

### <a name="mappings-for-select-strings"></a>Mapeamentos para cadeias de caracteres de seleção

- **Tipos .NET do Lote**: Tipos de API .NET do Lote.
- **Entidades da API REST**: cada página nesta coluna contém uma ou mais tabelas que listam os nomes de propriedade da API REST para o tipo. Esses nomes de propriedade são usados ao construir as cadeias de caracteres *select* . Você usa esses mesmos nomes de propriedade ao construir uma cadeia de caracteres [ODATADetailLevel. SelectClause](/dotnet/api/microsoft.azure.batch.odatadetaillevel.selectclause) .

| Tipos de Lote .NET | Entidades da API REST |
| --- | --- |
| [Certificado](/dotnet/api/microsoft.azure.batch.certificate) |[Obter informações sobre um certificado](/rest/api/batchservice/certificate/get) |
| [CloudJob](/dotnet/api/microsoft.azure.batch.cloudjob) |[Obter informações sobre um trabalho](/rest/api/batchservice/job/get) |
| [CloudJobSchedule](/dotnet/api/microsoft.azure.batch.cloudjobschedule) |[Obter informações sobre uma agenda de trabalho](/rest/api/batchservice/jobschedule/get) |
| [ComputeNode](/dotnet/api/microsoft.azure.batch.computenode) |[Obter informações sobre um nó](/rest/api/batchservice/computenode/get) |
| [CloudPool](/dotnet/api/microsoft.azure.batch.cloudpool) |[Obter informações sobre um pool](/rest/api/batchservice/pool/get) |
| [CloudTask](/dotnet/api/microsoft.azure.batch.cloudtask) |[Obter informações sobre uma tarefa](/rest/api/batchservice/task/get) |

## <a name="example-construct-a-filter-string"></a>Exemplo: construir uma cadeia de caracteres filter

Ao construir uma cadeia de caracteres filter para [ODATADetailLevel.FilterClause](/dotnet/api/microsoft.azure.batch.odatadetaillevel.filterclause), confira a tabela acima em "Mapeamentos para cadeias de caracteres filter" para localizar a página da documentação da API REST correspondente à operação de lista que você deseja executar. Você encontrará as propriedades e os operadores com suporte na primeira tabela com várias linhas nessa página. Se quiser recuperar todas as tarefas cujo código de saída era diferente de zero, por exemplo, essa linha em [Listar as tarefas associadas a um trabalho](/rest/api/batchservice/task/list) especificará a cadeia de caracteres da propriedade aplicável e os operadores permitidos:

| Propriedade | Operações permitidas | Type |
|:--- |:--- |:--- |
| `executionInfo/exitCode` |`eq, ge, gt, le , lt` |`Int` |

Assim, a cadeia de caracteres de filtro para listar todas as tarefas com um código de saída diferente de zero deve ser:

`(executionInfo/exitCode lt 0) or (executionInfo/exitCode gt 0)`

## <a name="example-construct-a-select-string"></a>Exemplo: construir uma cadeia de caracteres select

Para construir um [ODATADetailLevel.SelectClause](/dotnet/api/microsoft.azure.batch.odatadetaillevel.selectclause), confira a tabela acima em “Mapeamentos para as cadeias de caracteres select” e navegue até a página da API REST correspondente ao tipo de entidade listada. Você encontrará as propriedades selecionáveis e os operadores com suporte na primeira tabela de várias linha nessa página. Se quiser recuperar apenas a ID e a linha de comando de cada tarefa em uma lista, por exemplo, você encontrará essas linhas na tabela aplicável em [Obter informações sobre uma tarefa](/rest/api/batchservice/task/get):

| Propriedade | Type | Observações |
|:--- |:--- |:--- |
| `id` |`String` |`The ID of the task.` |
| `commandLine` |`String` |`The command line of the task.` |

A cadeia de caracteres de seleção para incluir somente a ID e a linha de comando com cada tarefa listada seria:

`id, commandLine`

## <a name="code-samples"></a>Exemplos de código

### <a name="efficient-list-queries-code-sample"></a>Exemplo de código de consultas de lista eficientes

O projeto de exemplo [EfficientListQueries](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp/ArticleProjects/EfficientListQueries) no GitHub mostra como uma consulta de lista eficiente pode afetar o desempenho em um aplicativo. Esse aplicativo de console em C# cria e adiciona um grande número de tarefas a um trabalho. Em seguida, ele faz várias chamadas para o método [JobOperations.ListTasks](/dotnet/api/microsoft.azure.batch.joboperations) e passa objetos [ODATADetailLevel](/dotnet/api/microsoft.azure.batch.odatadetaillevel) que são configurados com valores de propriedade diferentes para variar a quantidade de dados a serem retornados. Ele produz uma saída semelhante à seguinte:

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

Como mostrado nos tempos transcorridos, você pode diminuir muito os tempos de resposta da consulta limitando as propriedades e o número de itens retornados. Você pode encontrar esse e outros exemplos de projetos no repositório [azure-batch-samples](https://github.com/Azure-Samples/azure-batch-samples) no GitHub.

### <a name="batchmetrics-library-and-code-sample"></a>Biblioteca BatchMetrics e exemplo de código

Além do exemplo de código EfficientListQueries acima, o projeto de exemplo [BatchMetrics](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp/BatchMetrics) demonstra como monitorar com eficiência o progresso do trabalho do lote do Azure usando a API do lote.

O exemplo [BatchMetrics](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp/BatchMetrics) inclui um projeto da biblioteca de classes .NET que você pode incorporar a seus próprios projetos e um programa de linha de comando simples para treinar e demonstrar o uso da biblioteca.

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

- Saiba como [maximizar o uso de recursos de computação do lote do Azure com tarefas de nó simultâneas](batch-parallel-node-tasks.md). Alguns tipos de cargas de trabalho podem se beneficiar da execução de tarefas paralelas em nós de computação maiores (mas menos). Consulte o [cenário de exemplo](batch-parallel-node-tasks.md#example-scenario) no artigo para obter detalhes sobre esse cenário.
- Saiba como [monitorar soluções do lote contando tarefas e nós por estado](batch-get-resource-counts.md)


[api_net]: /dotnet/api/microsoft.azure.batch
[api_net_listjobs]: /dotnet/api/microsoft.azure.batch.joboperations
[api_rest]: /rest/api/batchservice/
[batch_metrics]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchMetrics
[efficient_query_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/EfficientListQueries
[github_samples]: https://github.com/Azure/azure-batch-samples
[odata]: /dotnet/api/microsoft.azure.batch.odatadetaillevel
[odata_ctor]: /dotnet/api/microsoft.azure.batch.odatadetaillevel
[odata_expand]: /dotnet/api/microsoft.azure.batch.odatadetaillevel
[odata_filter]: /dotnet/api/microsoft.azure.batch.odatadetaillevel
[odata_select]: /dotnet/api/microsoft.azure.batch.odatadetaillevel

[net_list_certs]: /dotnet/api/microsoft.azure.batch.certificateoperations
[net_list_compute_nodes]: /dotnet/api/microsoft.azure.batch.pooloperations
[net_list_job_schedules]: /dotnet/api/microsoft.azure.batch.jobscheduleoperations
[net_list_jobprep_status]: /dotnet/api/microsoft.azure.batch.joboperations
[net_list_jobs]: /dotnet/api/microsoft.azure.batch.joboperations
[net_list_nodefiles]: /dotnet/api/microsoft.azure.batch.joboperations
[net_list_pools]: /dotnet/api/microsoft.azure.batch.pooloperations
[net_list_schedule_jobs]: /dotnet/api/microsoft.azure.batch.jobscheduleoperations
[net_list_task_files]: /dotnet/api/microsoft.azure.batch.cloudtask
[net_list_tasks]: /dotnet/api/microsoft.azure.batch.joboperations

[rest_list_certs]: /rest/api/batchservice/certificate/list
[rest_list_compute_nodes]: /rest/api/batchservice/computenode/list
[rest_list_job_schedules]: /rest/api/batchservice/jobschedule/list
[rest_list_jobprep_status]: /rest/api/batchservice/job/listpreparationandreleasetaskstatus
[rest_list_jobs]: /rest/api/batchservice/job/list
[rest_list_nodefiles]: /rest/api/batchservice/file/listfromcomputenode
[rest_list_pools]: /rest/api/batchservice/pool/list
[rest_list_schedule_jobs]: /rest/api/batchservice/job/listfromjobschedule
[rest_list_task_files]: /rest/api/batchservice/file/listfromtask
[rest_list_tasks]: /rest/api/batchservice/task/list

[rest_get_cert]: /rest/api/batchservice/certificate/get
[rest_get_job]: /rest/api/batchservice/job/get
[rest_get_node]: /rest/api/batchservice/computenode/get
[rest_get_pool]: /rest/api/batchservice/pool/get
[rest_get_schedule]: /rest/api/batchservice/jobschedule/get
[rest_get_task]: /rest/api/batchservice/task/get

[net_cert]: /dotnet/api/microsoft.azure.batch.certificate
[net_job]: /dotnet/api/microsoft.azure.batch.cloudjob
[net_node]: /dotnet/api/microsoft.azure.batch.computenode
[net_pool]: /dotnet/api/microsoft.azure.batch.cloudpool
[net_schedule]: /dotnet/api/microsoft.azure.batch.cloudjobschedule
[net_task]: /dotnet/api/microsoft.azure.batch.cloudtask

[rest_get_task_counts]: /rest/api/batchservice/get-the-task-counts-for-a-job
[rest_get_node_counts]: /rest/api/batchservice/account/listpoolnodecounts
