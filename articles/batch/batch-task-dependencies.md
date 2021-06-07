---
title: Criar dependências de tarefas para executar tarefas
description: Crie tarefas que dependem da conclusão de outras tarefas para o processamento em estilo MapReduce e cargas de trabalho de big data semelhantes no Lote do Azure.
ms.topic: how-to
ms.date: 12/28/2020
ms.custom: H1Hack27Feb2017, devx-track-csharp
ms.openlocfilehash: ef05a98fffc3c0684ad0fa29f2f9f039b388f5ad
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97803928"
---
# <a name="create-task-dependencies-to-run-tasks-that-depend-on-other-tasks"></a>Crie dependências de tarefas para executar tarefas que dependam de outras tarefas

Com as dependências de tarefas do lote, você cria tarefas que são agendadas para execução em nós de computação após a conclusão de uma ou mais tarefas pai. Por exemplo, você pode criar um trabalho que processa cada quadro de um filme 3D com tarefas paralelas separadas. A tarefa final, a "tarefa de mesclagem", mescla os quadros renderizados no filme completo somente depois que todos os quadros são gerados com êxito.

Alguns cenários em que as dependências entre tarefas são úteis incluem:

- Cargas de trabalho de estilo MapReduce na nuvem.
- Trabalhos cujas tarefas de processamento de dados podem ser expressas como um DAG (grafo direcionado acíclico).
- Processos de pré-renderização e pós-renderização, em que cada tarefa deve ser concluída antes do início da próxima.
- Qualquer outro trabalho no qual tarefas downstream dependem da saída das tarefas upstream.

Por padrão, as tarefas dependentes estão agendadas para execução somente após a conclusão bem-sucedida da tarefa pai. Opcionalmente, você pode especificar uma [ação de dependência](#dependency-actions)  para substituir o comportamento padrão e executar tarefas quando a tarefa pai falhar.

## <a name="task-dependencies-with-batch-net"></a>Dependências de tarefas com o .NET do Lote

Neste artigo, discutimos como configurar dependências de tarefas usando a biblioteca [.NET do Lote](/dotnet/api/microsoft.azure.batch). Primeiro mostramos como [habilitar a dependência de tarefa](#enable-task-dependencies) nos trabalhos. Em seguida, demonstramos brevemente como [configurar uma tarefa com dependências](#create-dependent-tasks). Também descrevemos como especificar uma ação de dependência para executar tarefas dependentes em caso de falha do pai. Finalmente, discutiremos os [cenários de dependência](#dependency-scenarios) aos quais o Lote dá suporte.

## <a name="enable-task-dependencies"></a>Habilitar dependências de tarefas

Para usar dependências entre tarefas no aplicativo do Lote, é necessário primeiro configurar o trabalho para usar dependências entre tarefas. No .NET do Lote, habilite-o no [CloudJob](/dotnet/api/microsoft.azure.batch.cloudjob) configurando a propriedade [UsesTaskDependencies](/dotnet/api/microsoft.azure.batch.cloudjob.usestaskdependencies) como `true`:

```csharp
CloudJob unboundJob = batchClient.JobOperations.CreateJob( "job001",
    new PoolInformation { PoolId = "pool001" });

// IMPORTANT: This is REQUIRED for using task dependencies.
unboundJob.UsesTaskDependencies = true;
```

No snippet de código anterior, "batchClient" é uma instância da classe [BatchClient](/dotnet/api/microsoft.azure.batch.batchclient).

## <a name="create-dependent-tasks"></a>Criar tarefas dependentes

Para criar uma tarefa que depende da conclusão de uma ou mais tarefas pai, é possível especificar que a tarefa “depende” das outras tarefas. No .NET do lote, configure a propriedade [CloudTask. depends](/dotnet/api/microsoft.azure.batch.cloudtask.dependson) com uma instância da classe [TaskDependencies](/dotnet/api/microsoft.azure.batch.taskdependencies) :

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
> Por padrão, uma tarefa é considerada concluída com êxito quando está no estado concluído e seu código de saída é `0`. No .NET do lote, isso significa que o valor da propriedade [CloudTask. State](/dotnet/api/microsoft.azure.batch.cloudtask.state) é `Completed` e o valor da propriedade [TaskExecutionInformation. ExitCode](/dotnet/api/microsoft.azure.batch.taskexecutioninformation.exitcode) do CloudTask é `0` . Para saber como alterar isso, consulte a seção [ações de dependência](#dependency-actions) .

## <a name="dependency-scenarios"></a>Cenários de dependência

Há três cenários de dependência de tarefas básicos que você pode usar no Lote do Azure: um-para-um, um-para-muitos e dependência de intervalo de IDs de tarefas. Esses três cenários podem ser combinados para fornecer um quarto cenário: muitos para muitos.

| Cenário&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | Exemplo | Ilustração |
|:---:| --- | --- |
|  [Um-para-um](#one-to-one) |*tarefaB* depende de *tarefaA* <p/> A *tarefaB* não será agendada para execução até que a *tarefaA* seja concluída com êxito |:::image type="content" source="media/batch-task-dependency/01_one_to_one.png" alt-text="Diagrama mostrando o cenário de dependência de tarefa um-para-um."::: |
|  [Um para muitos](#one-to-many) |A *tarefaC* depende da *tarefaA* e da *tarefaB* <p/> A *tarefaC* não será agendada para execução até que a *tarefaA* e a *tarefaB* sejam concluídas com êxito |:::image type="content" source="media/batch-task-dependency/02_one_to_many.png" alt-text="Diagrama mostrando o cenário de dependência de tarefa um-para-muitos."::: |
|  [Intervalo de IDs de tarefa](#task-id-range) |A *taskD* depende de diversas tarefas <p/> A *tarefaD* não será agendada para execução até que as tarefas com as IDs *1* a *10* sejam concluídas com êxito |:::image type="content" source="media/batch-task-dependency/03_task_id_range.png" alt-text="Diagrama mostrando o cenário de dependência de tarefa do intervalo de ID da tarefa."::: |

> [!TIP]
> Você pode criar relações **muitos para muitos**, como uma em que as tarefas C, D, E e F dependem das tarefas A e B. Isso é útil, por exemplo, em cenários de pré-processamento em paralelo em que as tarefas downstream dependem da saída de várias tarefas upstream.
> 
> Nos exemplos desta seção, uma tarefa dependente é executada somente após a conclusão bem-sucedida das tarefas pai. Esse comportamento é o comportamento padrão de uma tarefa dependente. Você pode executar uma tarefa dependente após a falha de uma tarefa pai, especificando uma [ação de dependência](#dependency-actions)  para substituir o comportamento padrão.

### <a name="one-to-one"></a>Um para um

Em uma relação um-para-um, uma tarefa depende da conclusão bem-sucedida de uma tarefa pai. Para criar a dependência, forneça uma ID de tarefa única para o método estático [TaskDependencies. OnId](/dotnet/api/microsoft.azure.batch.taskdependencies.onid) ao preencher a propriedade [CloudTask. depends](/dotnet/api/microsoft.azure.batch.cloudtask.dependson) .

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

Em uma relação um-para-muitos, uma tarefa depende da conclusão de várias tarefas pai. Para criar a dependência, forneça uma coleção de IDs de tarefa para o método estático [TaskDependencies. OnIds](/dotnet/api/microsoft.azure.batch.taskdependencies.onids) ao preencher a propriedade [CloudTask. depends](/dotnet/api/microsoft.azure.batch.cloudtask.dependson) .

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
Para criar a dependência, forneça as IDs da primeira e da última tarefa no intervalo para o método estático [TaskDependencies](/dotnet/api/microsoft.azure.batch.taskdependencies.onidrange) ao preencher a propriedade [CloudTask. depends](/dotnet/api/microsoft.azure.batch.cloudtask.dependson) .

> [!IMPORTANT]
> Quando você usar os intervalos de ID de tarefa para suas dependências, somente tarefas com IDs que representam valores inteiros serão selecionadas por intervalo. Por exemplo, o intervalo `1..10` selecionará tarefas `3` e `7` , mas não `5flamingoes` .
>
> Zeros à esquerda não são significativos ao avaliar as dependências do intervalo. Portanto, as tarefas com identificadores de cadeia de caracteres `4`, `04` e `004` estarão *dentro* do intervalo e serão todas tratadas como uma tarefa `4`. Assim, a primeira a ser concluída atenderá à dependência.
>
> Cada tarefa no intervalo deve satisfazer a dependência, seja concluída com êxito ou concluindo com uma falha que é mapeada para uma ação de [dependência](#dependency-actions) definida como **satisfazer**.

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

Por padrão, uma tarefa dependente ou um conjunto de tarefas é executado somente após a conclusão bem-sucedida de uma tarefa pai. Em alguns cenários, talvez você deseje executar tarefas dependentes mesmo se houver uma falha da tarefa pai. É possível substituir o comportamento padrão especificando uma ação de dependência.

Uma ação de dependência especifica se uma tarefa dependente é qualificada para execução, de acordo com o sucesso ou a falha da tarefa pai. Por exemplo, suponha que uma tarefa dependente está aguardando dados da conclusão da tarefa upstream. Se a tarefa upstream falhar, a tarefa dependente ainda poderá ser executada usando dados mais antigos. Nesse caso, uma ação de dependência pode especificar que a tarefa dependente é qualificada para execução, apesar da falha da tarefa pai.

Uma ação de dependência baseia-se em uma condição de saída da tarefa pai. Você pode especificar uma ação de dependência para qualquer uma das seguintes condições de saída:

- Quando ocorre um erro de pré-processamento.
- Quando ocorre um erro de carregamento de arquivo. Se a tarefa é encerrada com um código de saída especificado por meio de **exitCodes** ou **exitCodeRanges**, e, em seguida, encontra erro de carregamento de arquivo, a ação especificada pelo código de saída tem precedência.
- Quando a tarefa é encerrada com um código de saída definido pela propriedade **ExitCodes**.
- Quando a tarefa é encerrada com um código de saída que está dentro de um intervalo especificado pela propriedade **ExitCodeRanges**.
- No caso padrão, se a tarefa for encerrada com um código de saída não definido por **ExitCodes** ou **ExitCodeRanges**, ou se a tarefa for encerrada com um erro de pré-processamento e a propriedade **PreProcessingError** não for definida, ou se houver erro de carregamento de arquivo e a propriedade **FileUploadError** não estiver definida. 

Para .NET, consulte a classe [ExitConditions](/dotnet/api/microsoft.azure.batch.exitconditions) para obter mais detalhes sobre essas condições.

Para especificar uma ação de dependência no .NET, defina a propriedade [exitoptions. dependencyaction](/dotnet/api/microsoft.azure.batch.exitoptions.dependencyaction) para a condição de saída como um dos seguintes:

- **Satisfazer**: indica que as tarefas dependentes podem ser executadas se a tarefa pai for encerrada com um erro especificado.
- **Bloquear**: indica que as tarefas dependentes não estão qualificadas para execução.

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

O projeto de exemplo [TaskDependencies](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/TaskDependencies) no GitHub demonstra:

- Como habilitar a dependência de tarefas em um trabalho.
- Como criar tarefas que dependem de outras tarefas.
- Como executar essas tarefas em um pool de nós de computação.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o recurso de [pacotes de aplicativos](batch-application-packages.md) do lote, que fornece uma maneira fácil de implantar e executar a versão dos aplicativos que suas tarefas executam em nós de computação.
- Saiba mais sobre [a verificação de erros para trabalhos e tarefas](batch-job-task-error-checking.md).
