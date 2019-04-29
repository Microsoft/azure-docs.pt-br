---
title: Criar tarefas para preparar e concluir trabalhos em nós de computação - Lote do Azure | Microsoft Docs
description: Use tarefas de preparação de nível de trabalho para minimizar a transferência de dados para nós de computação do Lote do Azure e libere as tarefas para limpeza de nó na conclusão do trabalho.
services: batch
documentationcenter: .net
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: 63d9d4f1-8521-4bbb-b95a-c4cad73692d3
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
origin.date: 02/27/2017
ms.date: 06/29/2018
ms.author: v-junlch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 517ac0f612b9e5fc5909a7f0fe2ce088c9b367d9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60776180"
---
# <a name="run-job-preparation-and-job-release-tasks-on-batch-compute-nodes"></a>Executar tarefas de preparação e liberação do trabalho em nós de computação do Lote

 Um trabalho do Lote do Azure geralmente requer alguma forma de configuração antes da execução das tarefas e manutenção pós-trabalho quando as tarefas são concluídas. Talvez seja necessário baixar dados de entrada de tarefas comuns nos nós de computação ou carregar dados de saída da tarefa no Armazenamento do Azure depois que o trabalho for concluído. Você pode usar tarefas de **preparação de trabalho** e de **liberação de trabalho** para executar essas operações.

## <a name="what-are-job-preparation-and-release-tasks"></a>O que são as tarefas de preparação e liberação do trabalho?
Antes da execução de uma tarefa de trabalho, a tarefa de preparação de trabalho é executada em todos os nós computados e agendados para execução de pelo menos uma tarefa. Quando o trabalho é concluído, a tarefa de liberação do trabalho é executada em cada nó no pool que executou pelo menos uma tarefa. Assim como acontece com tarefas normais de lote, você pode especificar um comando da tarefa para ser invocada quando uma tarefa de preparação ou liberação de trabalho for executada.

As tarefas de preparação e liberação de trabalho oferecem recursos conhecidos de tarefas do Lote, como download de arquivo ([arquivos de recurso][net_job_prep_resourcefiles]), execução elevada, variáveis de ambiente personalizadas, duração máxima da execução, contagem de repetição e tempo de retenção de arquivo.

Nas seções a seguir, você aprenderá a usar as classes [JobPreparationTask][net_job_prep] e [JobReleaseTask][net_job_release] encontradas na biblioteca [.NET do Lote][api_net].

> [!TIP]
> As tarefas de preparação e de liberação são especialmente úteis em ambientes de "pool compartilhado", em que um pool de nós de computação persiste entre as execuções de trabalho e usado por vários trabalhos.
> 
> 

## <a name="when-to-use-job-preparation-and-release-tasks"></a>Quando usar tarefas de preparação e liberação do trabalho
As tarefas de preparação e de liberação de trabalho são uma boa opção nas seguintes situações:

**Baixar dados de tarefas comuns**

Os trabalhos do Lote geralmente exigem um conjunto comum de dados como entrada para as tarefas do trabalho. Por exemplo, em cálculos diários de análise de riscos, os dados de mercado são específico do trabalho, porém, comuns a todas as tarefas no trabalho. Esses dados de mercado, geralmente com vários gigabytes, devem ser baixados para cada nó de computação apenas uma vez, para que qualquer tarefa que seja executada no nó possa usá-los. Use uma **tarefa de preparação de trabalho** para baixar esses dados para cada nó antes da execução de outras tarefas do trabalho.

**Excluir saída de tarefa e de trabalho**

Em um ambiente de "pool compartilhado", em que nós de computação do pool não são encerrados entre trabalhos, talvez seja necessário excluir os dados entre as execuções do trabalho. Talvez seja necessário conservar espaço em disco nos nós ou atender a políticas de segurança da sua organização. Use uma **tarefa de liberação de trabalho** para excluir dados baixados por uma tarefa de preparação de trabalho ou gerados durante a execução da tarefa.

**Retenção de log**

Convém manter uma cópia dos arquivos de log gerados pelas tarefas ou talvez arquivos de despejo de memória que podem ser gerados pelos aplicativos que falharam. Use uma **tarefa de liberação de trabalho** nesses casos para compactar e carregar esses dados para uma conta de [Armazenamento do Azure][azure_storage].

> [!TIP]
> Outra maneira de manter os logs e outros trabalhos e tarefas de saída de dados é usar a biblioteca [Convenções de arquivo do Lote do Azure](batch-task-output.md) .
> 
> 

## <a name="job-preparation-task"></a>tarefa de preparação de trabalho
Antes da execução de tarefas de um trabalho, o Lote executa a tarefa de preparação de trabalho em cada nó de computação agendado para executar uma tarefa. Por padrão, o serviço Lote aguarda a conclusão da tarefa de preparação do trabalho antes de executar as tarefas agendadas para execução no nó. No entanto, você pode configurar o serviço para não aguardar. Se o nó for reiniciado, a tarefa de preparação de trabalho será executada novamente, mas você também poderá desativar esse comportamento.

A tarefa de preparação de trabalho é executada apenas em nós programados para executar uma tarefa. Isso impede a execução desnecessária de uma tarefa de preparação em um nó que não recebeu uma tarefa. Isso pode ocorrer quando o número de tarefas de um trabalho é menor do que o número de nós em um pool. Também se aplica quando a [execução de tarefas simultâneas](batch-parallel-node-tasks.md) é habilitada, o que deixará alguns nós ociosos se a contagem de tarefas for inferior ao total de possíveis tarefas simultâneas. A não execução da tarefa de preparação do trabalho em nós ociosos economiza encargos de transferência de dados.

> [!NOTE]
> [JobPreparationTask][net_job_prep_cloudjob] é diferente de [CloudPool.StartTask][pool_starttask], visto que JobPreparationTask é executado no início de cada trabalho, enquanto StartTask é executado apenas quando um nó de computação ingressa em um pool pela primeira vez ou é reiniciado.
> 
> 

## <a name="job-release-task"></a>tarefa de liberação de trabalho
Quando um trabalho é marcado como concluído, a tarefa de liberação do trabalho é executada em cada nó no pool que executou pelo menos uma tarefa. Marcar uma tarefa como concluída emitindo uma solicitação de encerramento. Em seguida, o serviço do Lote define o estado do trabalho como *encerrando*, encerra quaisquer tarefas em execução ou ativas associadas ao trabalho e executa a tarefa de liberação do trabalho. Em seguida, o trabalho é movido para o estado *concluído* .

> [!NOTE]
> A exclusão do trabalho também executa a tarefa de liberação do trabalho. No entanto, se um trabalho já tiver sido encerrado, a tarefa de liberação não será executada uma segunda vez se o trabalho for excluído posteriormente.
> 
> 

## <a name="job-prep-and-release-tasks-with-batch-net"></a>Tarefas de preparação e liberação de trabalho com o Lote .NET
Para usar uma tarefa de preparação de trabalho, atribua um objeto [JobPreparationTask][net_job_prep] à propriedade [CloudJob.JobPreparationTask][net_job_prep_cloudjob] do seu trabalho. Da mesma forma, inicialize um [JobReleaseTask][net_job_release] e atribua-o à propriedade [CloudJob.JobReleaseTask][net_job_prep_cloudjob] para definir a tarefa de liberação do trabalho.

Neste snippet de código, `myBatchClient` é uma instância de [BatchClient][net_batch_client] e `myPool` é um pool existente dentro da conta do Lote.

```csharp
// Create the CloudJob for CloudPool "myPool"
CloudJob myJob =
    myBatchClient.JobOperations.CreateJob(
        "JobPrepReleaseSampleJob",
        new PoolInformation() { PoolId = "myPool" });

// Specify the command lines for the job preparation and release tasks
string jobPrepCmdLine =
    "cmd /c echo %AZ_BATCH_NODE_ID% > %AZ_BATCH_NODE_SHARED_DIR%\\shared_file.txt";
string jobReleaseCmdLine =
    "cmd /c del %AZ_BATCH_NODE_SHARED_DIR%\\shared_file.txt";

// Assign the job preparation task to the job
myJob.JobPreparationTask =
    new JobPreparationTask { CommandLine = jobPrepCmdLine };

// Assign the job release task to the job
myJob.JobReleaseTask =
    new JobReleaseTask { CommandLine = jobReleaseCmdLine };

await myJob.CommitAsync();
```

Conforme mencionado anteriormente, a tarefa de liberação é executada quando um trabalho é encerrado ou excluído. Encerre um trabalho com [JobOperations.TerminateJobAsync][net_job_terminate]. Exclua um trabalho com [JobOperations.DeleteJobAsync][net_job_delete]. Normalmente você encerra ou exclui um trabalho quando as tarefas são concluídas ou quando um tempo limite definido foi atingido.

```csharp
// Terminate the job to mark it as Completed; this will initiate the
// Job Release Task on any node that executed job tasks. Note that the
// Job Release Task is also executed when a job is deleted, thus you
// need not call Terminate if you typically delete jobs after task completion.
await myBatchClient.JobOperations.TerminateJobAsync("JobPrepReleaseSampleJob");
```

## <a name="code-sample-on-github"></a>Exemplo de código no GitHub
Para ver as tarefas de preparação e liberação de trabalho em ação, confira o projeto de exemplo [JobPrepRelease][job_prep_release_sample] no GitHub. Esse aplicativo de console faz o seguinte:

1. Cria um pool com dois nós.
2. Cria um trabalho com tarefas de preparação, de liberação e padrão de trabalho.
3. Executa a tarefa de preparação de trabalho, que primeiro grava a ID do nó em um arquivo de texto no diretório "compartilhado" do nó.
4. Executa uma tarefa em cada nó que grava sua ID de tarefa no mesmo arquivo de texto.
5. Depois que todas as tarefas forem concluídas (ou que o tempo limite for atingido), imprime o conteúdo do arquivo de texto de cada nó no console.
6. Quando o trabalho é concluído, executa a tarefa de liberação de trabalho para excluir o arquivo do nó.
7. Imprime os códigos de saída das tarefas de preparação e liberação de trabalho para cada nó no qual elas são executadas.
8. Pausa a execução para permitir a confirmação da exclusão do trabalho e/ou pool.

A saída do aplicativo de exemplo é semelhante ao seguinte:

```
Attempting to create pool: JobPrepReleaseSamplePool
Created pool JobPrepReleaseSamplePool with 2 nodes
Checking for existing job JobPrepReleaseSampleJob...
Job JobPrepReleaseSampleJob not found, creating...
Submitting tasks and awaiting completion...
All tasks completed.

Contents of shared\job_prep_and_release.txt on tvm-2434664350_1-20160623t173951z:
-------------------------------------------
tvm-2434664350_1-20160623t173951z tasks:
  task001
  task004
  task005
  task006

Contents of shared\job_prep_and_release.txt on tvm-2434664350_2-20160623t173951z:
-------------------------------------------
tvm-2434664350_2-20160623t173951z tasks:
  task008
  task002
  task003
  task007

Waiting for job JobPrepReleaseSampleJob to reach state Completed
...

tvm-2434664350_1-20160623t173951z:
  Prep task exit code:    0
  Release task exit code: 0

tvm-2434664350_2-20160623t173951z:
  Prep task exit code:    0
  Release task exit code: 0

Delete job? [yes] no
yes
Delete pool? [yes] no
yes

Sample complete, hit ENTER to exit...
```

> [!NOTE]
> Devido ao tempo variável de criação e início dos nós em um novo pool (alguns nós ficam prontos para tarefas antes de outros), você pode ver uma saída diferente. Especificamente, como as tarefas são concluídas rapidamente, um dos nós do pool pode executar todas as tarefas do trabalho. Se isso ocorrer, você observará que as tarefas de preparação e liberação do trabalho não existem para o nó que não executou nenhuma tarefa.
> 
> 

### <a name="inspect-job-preparation-and-release-tasks-in-the-azure-portal"></a>Inspecionar as tarefas de preparação e liberação do trabalho no portal do Azure
Quando estiver executando o aplicativo de exemplo, você poderá usar o [portal do Azure][portal] para exibir as propriedades do trabalho e suas tarefas ou até mesmo baixar o arquivo de texto compartilhado que é modificado pelas tarefas do trabalho.

A captura de tela abaixo mostra a **Folha de tarefas de preparação** no portal do Azure após uma execução do aplicativo de exemplo. Navegue até as propriedades *JobPrepReleaseSampleJob* após a conclusão das tarefas (mas antes de excluir seu trabalho e pool) e clique em **Tarefas de preparação** ou **Tarefas de liberação** para exibir suas propriedades.

![Propriedades de preparação de trabalho no portal do Azure][1]

## <a name="next-steps"></a>Próximas etapas
### <a name="application-packages"></a>pacotes de aplicativos
Além da tarefa de preparação de trabalho, você também pode usar o recurso de [pacotes de aplicativos](batch-application-packages.md) do Lote para preparar nós de computação para execução da tarefa. Esse recurso é especialmente útil para implantação de aplicativos que não exigem a execução de um instalados, aplicativos que contêm muitos arquivos (mais de 100) ou aplicativos que exigem um controle de versão estrito.

### <a name="installing-applications-and-staging-data"></a>Instalação de aplicativos e preparação de dados
Essa postagem no fórum da MSDN fornece uma visão geral dos vários métodos de preparação de seus nós para a execução de tarefas:

[Instalando aplicativos e preparando dados em nós de computação do Lote][forum_post]

Escrito por um dos membros da equipe do Lote do Azure, ele aborda várias técnicas que você pode usar para implantar aplicativos e dados em nós de computação.

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_listjobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[azure_storage]: https://www.azure.cn/home/features/storage/
[portal]: https://portal.azure.cn
[job_prep_release_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/JobPrepRelease
[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[net_batch_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]:https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_job_prep]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobpreparationtask.aspx
[net_job_prep_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobpreparationtask.aspx
[net_job_prep_resourcefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobpreparationtask.resourcefiles.aspx
[net_job_delete]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.deletejobasync.aspx
[net_job_terminate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.terminatejobasync.aspx
[net_job_release]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobreleasetask.aspx
[net_job_release_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobreleasetask.aspx
[pool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx

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

[1]: ./media/batch-job-prep-release/portal-jobprep-01.png

<!-- Update_Description: wording update -->