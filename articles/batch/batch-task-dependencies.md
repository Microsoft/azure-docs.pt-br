---
title: Usar dependências de tarefas para executar tarefas com base na conclusão de outras tarefas - Lote do Azure | Microsoft Docs
description: Crie tarefas que dependem da conclusão de outras tarefas para o processamento em estilo MapReduce e cargas de trabalho de big data semelhantes no Lote do Azure.
services: batch
documentationcenter: .net
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: b8d12db5-ca30-4c7d-993a-a05af9257210
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 05/22/2017
ms.author: lahugh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ca6918b809a9b4ede3fffb151c7fa5183ae03b47
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60550338"
---
# <a name="create-task-dependencies-to-run-tasks-that-depend-on-other-tasks"></a>Crie dependências de tarefas para executar tarefas que dependam de outras tarefas

É possível definir dependências entre tarefas para executar uma tarefa ou um conjunto de tarefas somente após a conclusão de uma tarefa pai. Alguns cenários em que as dependências entre tarefas são úteis incluem:

* Cargas de trabalho de estilo MapReduce na nuvem.
* Trabalhos cujas tarefas de processamento de dados podem ser expressas como um DAG (grafo direcionado acíclico).
* Processos de pré-renderização e pós-renderização, em que cada tarefa deve ser concluída antes do início da próxima.
* Qualquer outro trabalho no qual tarefas downstream dependem da saída das tarefas upstream.

Com dependências entre tarefas do Lote, é possível criar tarefas que estão agendadas para execução em nós de computação após a conclusão de uma ou mais tarefas pai. Por exemplo, você pode criar um trabalho que processa cada quadro de um filme 3D com tarefas paralelas separadas. A tarefa final, a "tarefa de mesclagem", mescla os quadros renderizados no filme completo somente depois que todos os quadros são gerados com êxito.

Por padrão, as tarefas dependentes estão agendadas para execução somente após a conclusão bem-sucedida da tarefa pai. É possível especificar uma ação de dependência para substituir o comportamento padrão e executar tarefas quando a tarefa pai falha. Consulte a seção [Ações de dependência](#dependency-actions) para obter detalhes.  

Você pode criar tarefas que dependem de outras tarefas em uma relação um para um ou um para muitos. Também é possível criar uma dependência entre intervalos, em que uma tarefa depende da conclusão de um grupo de tarefas em um intervalo especificado de identificações da tarefa. Você pode combinar esses três cenários básicos para criar relações muitos-para-muitos.

## <a name="task-dependencies-with-batch-net"></a>Dependências de tarefas com o .NET do Lote
Neste artigo, discutimos como configurar dependências de tarefas usando a biblioteca [.NET do Lote][net_msdn]. Primeiro mostramos como [habilitar a dependência de tarefa](#enable-task-dependencies) nos trabalhos. Em seguida, demonstramos brevemente como [configurar uma tarefa com dependências](#create-dependent-tasks). Também descrevemos como especificar uma ação de dependência para executar tarefas dependentes em caso de falha do pai. Finalmente, discutiremos os [cenários de dependência](#dependency-scenarios) aos quais o Lote dá suporte.

## <a name="enable-task-dependencies"></a>Habilitar dependências de tarefas
Para usar dependências entre tarefas no aplicativo do Lote, é necessário primeiro configurar o trabalho para usar dependências entre tarefas. No .NET do Lote, habilite-o no [CloudJob][net_cloudjob] configurando a propriedade [UsesTaskDependencies][net_usestaskdependencies] como `true`:

```csharp
CloudJob unboundJob = batchClient.JobOperations.CreateJob( "job001",
    new PoolInformation { PoolId = "pool001" });

// IMPORTANT: This is REQUIRED for using task dependencies.
unboundJob.UsesTaskDependencies = true;
```

No snippet de código anterior, "batchClient" é uma instância da classe [BatchClient][net_batchclient].

## <a name="create-dependent-tasks"></a>Criar tarefas dependentes
Para criar uma tarefa que depende da conclusão de uma ou mais tarefas pai, é possível especificar que a tarefa “depende” das outras tarefas. No .NET do Lote, configure a propriedade [CloudTask][net_cloudtask].[DependsOn][net_dependson] com uma instância da classe [TaskDependencies][net_taskdependencies]:

```csharp
// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
```

Este snippet de código cria uma tarefa dependente com a identificação da tarefa “Flowers”. A tarefa “Flowers” depende das tarefas “Rain” e “Sun”. A tarefa “Flowers” será agendada para execução em um nó de computação somente após a conclusão bem-sucedida das tarefas “Rain” e “Sun”.

> [!NOTE]
> Por padrão, uma tarefa é considerada concluída com êxito quando está no estado **concluído** e seu **código de saída** é `0`. No .NET do Lote, isso significa que o valor da propriedade [CloudTask][net_cloudtask].[State][net_taskstate] é `Completed` e o valor da propriedade [TaskExecutionInformation][net_taskexecutioninformation].[ExitCode][net_exitcode] de CloudTask é `0`. Para saber como alterar isso, confira a seção [Ações de dependência](#dependency-actions).
> 
> 

## <a name="dependency-scenarios"></a>Cenários de dependência
Há três cenários de dependência de tarefas básicos que você pode usar no Lote do Azure: um-para-um, um-para-muitos e dependência de intervalo de IDs de tarefas. Eles podem ser combinados para fornecer um quarto cenário, muitos-para-muitos.

| Cenário&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | Exemplo |  |
|:---:| --- | --- |
|  [Um-para-um](#one-to-one) |*tarefaB* depende de *tarefaA* <p/> A *tarefaB* não será agendada para execução até que a *tarefaA* seja concluída com êxito |![Diagrama: dependência de tarefa de um para um][1] |
|  [Um-para-muitos](#one-to-many) |A *tarefaC* depende da *tarefaA* e da *tarefaB* <p/> A *tarefaC* não será agendada para execução até que a *tarefaA* e a *tarefaB* sejam concluídas com êxito |![Diagrama: dependência de tarefa de um para muitos][2] |
|  [Intervalo de IDs de tarefa](#task-id-range) |A *taskD* depende de diversas tarefas <p/> A *tarefaD* não será agendada para execução até que as tarefas com as IDs *1* a *10* sejam concluídas com êxito |![Diagrama: dependência de intervalo de ids de tarefas][3] |

> [!TIP]
> Você pode criar relações **muitos para muitos**, como uma em que as tarefas C, D, E e F dependem das tarefas A e B. Isso é útil, por exemplo, em cenários de pré-processamento em paralelo em que as tarefas downstream dependem da saída de várias tarefas upstream.
> 
> Nos exemplos desta seção, uma tarefa dependente é executada somente após a conclusão bem-sucedida das tarefas pai. Esse comportamento é o comportamento padrão de uma tarefa dependente. É possível executar uma tarefa dependente após uma falha da tarefa pai especificando uma ação de dependência para substituir o comportamento padrão. Consulte a seção [Ações de dependência](#dependency-actions) para obter detalhes.

### <a name="one-to-one"></a>Um-para-um
Em uma relação um-para-um, uma tarefa depende da conclusão bem-sucedida de uma tarefa pai. Para criar a dependência, forneça uma única identificação da tarefa para o método estático [TaskDependencies][net_taskdependencies].[OnId][net_onid] ao popular a propriedade [DependsOn][net_dependson] de [CloudTask][net_cloudtask].

```csharp
// Task 'taskA' doesn't depend on any other tasks
new CloudTask("taskA", "cmd.exe /c echo taskA"),

// Task 'taskB' depends on completion of task 'taskA'
new CloudTask("taskB", "cmd.exe /c echo taskB")
{
    DependsOn = TaskDependencies.OnId("taskA")
},
```

### <a name="one-to-many"></a>Um-para-muitos
Em uma relação um-para-muitos, uma tarefa depende da conclusão de várias tarefas pai. Para criar a dependência, forneça uma coleção de identificações da tarefa para o método estático [TaskDependencies][net_taskdependencies].[OnIds][net_onids] ao popular a propriedade [DependsOn][net_dependson] de [CloudTask][net_cloudtask].

```csharp
// 'Rain' and 'Sun' don't depend on any other tasks
new CloudTask("Rain", "cmd.exe /c echo Rain"),
new CloudTask("Sun", "cmd.exe /c echo Sun"),

// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
``` 

### <a name="task-id-range"></a>Intervalo de IDs de tarefa
Em uma dependência em um intervalo de tarefas pai, uma tarefa depende da conclusão de tarefas cujas IDs estão em um intervalo.
Para criar a dependência, forneça a primeira e a última identificação da tarefa no intervalo para o método estático [TaskDependencies][net_taskdependencies].[OnIdRange][net_onidrange] ao popular a propriedade [DependsOn][net_dependson] de [CloudTask][net_cloudtask].

> [!IMPORTANT]
> Quando você usar os intervalos de ID de tarefa para suas dependências, somente tarefas com IDs que representam valores inteiros serão selecionadas por intervalo. Portanto, o intervalo `1..10` selecionará as tarefas `3` e `7`, mas não `5flamingoes`. 
> 
> Zeros à esquerda não são significativos ao avaliar as dependências do intervalo. Portanto, as tarefas com identificadores de cadeia de caracteres `4`, `04` e `004` estarão *dentro* do intervalo e serão todas tratadas como uma tarefa `4`. Assim, a primeira a ser concluída atenderá à dependência.
> 
> Cada tarefa no intervalo deve atender à dependência, concluindo com êxito ou concluindo com uma falha que é mapeada para uma ação de dependência definida como **Atender**. Consulte a seção [Ações de dependência](#dependency-actions) para obter detalhes.
>
>

```csharp
// Tasks 1, 2, and 3 don't depend on any other tasks. Because
// we will be using them for a task range dependency, we must
// specify string representations of integers as their ids.
new CloudTask("1", "cmd.exe /c echo 1"),
new CloudTask("2", "cmd.exe /c echo 2"),
new CloudTask("3", "cmd.exe /c echo 3"),

// Task 4 depends on a range of tasks, 1 through 3
new CloudTask("4", "cmd.exe /c echo 4")
{
    // To use a range of tasks, their ids must be integer values.
    // Note that we pass integers as parameters to TaskIdRange,
    // but their ids (above) are string representations of the ids.
    DependsOn = TaskDependencies.OnIdRange(1, 3)
},
```

## <a name="dependency-actions"></a>Ações de dependência

Por padrão, uma tarefa dependente ou um conjunto de tarefas é executado somente após a conclusão bem-sucedida de uma tarefa pai. Em alguns cenários, talvez você deseje executar tarefas dependentes mesmo se houver uma falha da tarefa pai. É possível substituir o comportamento padrão especificando uma ação de dependência. Uma ação de dependência especifica se uma tarefa dependente é qualificada para execução, de acordo com o sucesso ou a falha da tarefa pai. 

Por exemplo, suponha que uma tarefa dependente está aguardando dados da conclusão da tarefa upstream. Se a tarefa upstream falhar, a tarefa dependente ainda poderá ser executada usando dados mais antigos. Nesse caso, uma ação de dependência pode especificar que a tarefa dependente é qualificada para execução, apesar da falha da tarefa pai.

Uma ação de dependência baseia-se em uma condição de saída da tarefa pai. É possível especificar uma ação de dependência para qualquer uma das condições de saída a seguir; para o .NET, consulte a classe [ExitConditions][net_exitconditions] para obter detalhes:

- Quando ocorre um erro de pré-processamento.
- Quando ocorre um erro de carregamento de arquivo. Se a tarefa é encerrada com um código de saída especificado por meio de **exitCodes** ou **exitCodeRanges**, e, em seguida, encontra erro de carregamento de arquivo, a ação especificada pelo código de saída tem precedência.
- Quando a tarefa é encerrada com um código de saída definido pela propriedade **ExitCodes**.
- Quando a tarefa é encerrada com um código de saída que está dentro de um intervalo especificado pela propriedade **ExitCodeRanges**.
- No caso padrão, se a tarefa for encerrada com um código de saída não definido por **ExitCodes** ou **ExitCodeRanges**, ou se a tarefa for encerrada com um erro de pré-processamento e a propriedade **PreProcessingError** não for definida, ou se houver erro de carregamento de arquivo e a propriedade **FileUploadError** não estiver definida. 

Para especificar uma ação de dependência no .NET, defina a propriedade [ExitOptions][net_exitoptions].[DependencyAction][net_dependencyaction] da condição de saída. A propriedade **DependencyAction** usa um dos dois valores:

- A configuração da propriedade **DependencyAction** como **Atender** indica que as tarefas dependentes estão qualificadas para execução se a tarefa pai é encerrada com um erro especificado.
- A configuração da propriedade **DependencyAction** como **Bloquear** indica que as tarefas dependentes não estão qualificadas para execução.

A configuração padrão da propriedade **DependencyAction** é **Atender** para o código de saída 0 e **Bloquear** para todas as outras condições de saída.

O snippet de código a seguir define a propriedade **DependencyAction** de uma tarefa pai. Se a tarefa pai é encerrada com um erro de pré-processamento ou com os códigos de erro especificados, a tarefa dependente é bloqueada. Se a tarefa pai é encerrada com qualquer outro erro diferente de zero, a tarefa dependente é qualificada para execução.

```csharp
// Task A is the parent task.
new CloudTask("A", "cmd.exe /c echo A")
{
    // Specify exit conditions for task A and their dependency actions.
    ExitConditions = new ExitConditions
    {
        // If task A exits with a pre-processing error, block any downstream tasks (in this example, task B).
        PreProcessingError = new ExitOptions
        {
            DependencyAction = DependencyAction.Block
        },
        // If task A exits with the specified error codes, block any downstream tasks (in this example, task B).
        ExitCodes = new List<ExitCodeMapping>
        {
            new ExitCodeMapping(10, new ExitOptions() { DependencyAction = DependencyAction.Block }),
            new ExitCodeMapping(20, new ExitOptions() { DependencyAction = DependencyAction.Block })
        },
        // If task A succeeds or fails with any other error, any downstream tasks become eligible to run 
        // (in this example, task B).
        Default = new ExitOptions
        {
            DependencyAction = DependencyAction.Satisfy
        }
    }
},
// Task B depends on task A. Whether it becomes eligible to run depends on how task A exits.
new CloudTask("B", "cmd.exe /c echo B")
{
    DependsOn = TaskDependencies.OnId("A")
},
```

## <a name="code-sample"></a>Exemplo de código
O projeto de exemplo [TaskDependencies][github_taskdependencies] é um dos [exemplos de código do Lote do Azure][github_samples] no GitHub. Esta solução do Visual Studio demonstra:

- Como habilitar a dependência entre tarefas em um trabalho
- Como criar tarefas que dependem de outras tarefas
- Como executar essas tarefas em um pool de nós de computação.

## <a name="next-steps"></a>Próximas etapas
### <a name="application-deployment"></a>Implantação do aplicativo
O recurso de [pacotes de aplicativos](batch-application-packages.md) do lote fornece uma maneira fácil de implantar e controlar a versão dos aplicativos que as tarefas executam em nós de computação.

### <a name="installing-applications-and-staging-data"></a>Instalação de aplicativos e preparação de dados
Consulte [Instalando aplicativos e preparando dados em nós de computação do Lote][forum_post] no fórum do Lote do Azure para obter uma visão geral de métodos para preparar os nós para execução de tarefas. Escrita por um dos membros da equipe do Lote do Azure, essa postagem é um bom guia sobre as diferentes maneiras de copiar aplicativos, dados de entrada de tarefa e outros arquivos nos nós de computação.

[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[github_taskdependencies]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/TaskDependencies
[github_samples]: https://github.com/Azure/azure-batch-samples
[net_batchclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_cloudtask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_dependson]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.dependson.aspx
[net_exitcode]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskexecutioninformation.exitcode.aspx
[net_exitconditions]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.exitconditions
[net_exitoptions]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.exitoptions
[net_dependencyaction]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.exitoptions
[net_msdn]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[net_onid]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onid.aspx
[net_onids]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onids.aspx
[net_onidrange]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onidrange.aspx
[net_taskexecutioninformation]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskexecutioninformation.aspx
[net_taskstate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.common.taskstate.aspx
[net_usestaskdependencies]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.usestaskdependencies.aspx
[net_taskdependencies]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskdependencies.aspx

[1]: ./media/batch-task-dependency/01_one_to_one.png "Diagrama: dependência de um para um"
[2]: ./media/batch-task-dependency/02_one_to_many.png "Diagrama: dependência de um para muitos"
[3]: ./media/batch-task-dependency/03_task_id_range.png "Diagrama: dependência de intervalo de ids de tarefas"
