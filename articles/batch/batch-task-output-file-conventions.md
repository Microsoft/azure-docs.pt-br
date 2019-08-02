---
title: Manter saída de trabalhos e tarefas no armazenamento do Azure com a biblioteca de Convenções de Arquivo para .NET – Lote do Azure | Microsoft Docs
description: Saiba como usar a biblioteca de Convenções de Arquivo em Lote do Azure para .NET para manter a saída de tarefa e trabalho em Lote no Armazenamento do Azure e exibir a saída mantida no portal do Azure.
services: batch
documentationcenter: .net
author: laurenhughes
manager: gwallace
editor: ''
ms.assetid: 16e12d0e-958c-46c2-a6b8-7843835d830e
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 11/14/2018
ms.author: lahugh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8be42399d9919d0ed410f1503353568c86a75f5a
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68322889"
---
# <a name="persist-job-and-task-data-to-azure-storage-with-the-batch-file-conventions-library-for-net"></a>Persistir dados de tarefa e trabalho no Armazenamento do Microsoft Azure com a biblioteca de Convenções de Arquivo em Lotes para .NET

[!INCLUDE [batch-task-output-include](../../includes/batch-task-output-include.md)]

Uma maneira de manter os dados da tarefa é usar a [biblioteca de convenções de arquivo do lote do Azure para .net][nuget_package]. A biblioteca de Convenções de Arquivo simplifica o processo de armazenar dados de saída de tarefa no Armazenamento do Azure e recuperá-los. Você pode usar a biblioteca de Convenções de Arquivo em código tanto do cliente quanto de &mdash; no código de tarefa para manter arquivos e no código do cliente para listá-los e recuperá-los. Seu código de tarefa também pode usar a biblioteca para recuperar as saídas de tarefas upstream, como em um cenário de [dependências de tarefa](batch-task-dependencies.md).

Para recuperar arquivos de saída com a biblioteca de Convenções de Arquivo, você pode localizar os arquivos para um determinado trabalho ou tarefa listando-os por ID e finalidade. Você não precisa saber os nomes nem os locais dos arquivos. Por exemplo, você pode usar a biblioteca de Convenções de Arquivo para listar todos os arquivos intermediários para uma determinada tarefa ou obter um arquivo de versão prévia para um determinado trabalho.

> [!TIP]
> Começando com a versão de 01/05/2017, a API de serviço de Lote dá suporte a manter dados de saída para o Armazenamento do Azure para tarefas e tarefas do gerenciador de trabalho que são executadas em pools criados com a configuração de máquina virtual. A API do serviço de Lote oferece uma maneira simples de manter a saída de dentro do código que cria uma tarefa e serve como uma alternativa à biblioteca de Convenções de Arquivo. Você pode modificar seus aplicativos cliente em Lotes para manter a saída sem necessidade de atualizar o aplicativo que sua tarefa está executando. Para obter mais informações, consulte [Manter dados de tarefa no Armazenamento do Azure com a API de serviço em Lote](batch-task-output-files.md).

## <a name="when-do-i-use-the-file-conventions-library-to-persist-task-output"></a>Quando usar a biblioteca Convenções de Arquivo para manter a saída da tarefa?

O Lote do Azure fornece mais de uma maneira de manter a saída da tarefa. A biblioteca Convenções de Arquivo é ideal para estes cenários:

- Você pode facilmente modificar o código para o aplicativo que sua tarefa está executando para manter os arquivos usando a biblioteca Convenções de Arquivo.
- Você desejar transmitir dados para o Armazenamento do Azure enquanto a tarefa ainda está em execução.
- Você desejar manter os dados dos pools criados com a configuração do serviço de nuvem ou a configuração de máquina virtual.
- O aplicativo cliente ou outras tarefas no trabalho precisar localizar e baixar arquivos de saída de tarefas por ID ou por finalidade.
- Você desejar exibir a saída da tarefa no portal do Azure.

Se seu cenário for diferente daqueles listados acima, poderá ser necessário considerar uma abordagem diferente. Para obter mais informações sobre outras opções para manter a saída da tarefa, consulte [Manter saída de trabalho e tarefa no Armazenamento do Azure](batch-task-output.md).

## <a name="what-is-the-batch-file-conventions-standard"></a>O que é o padrão Convenções do Arquivo em Lote?

O [padrão Convenções de Arquivo em Lote](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/Batch/Support/FileConventions#conventions) fornece um esquema de nomenclatura para os contêineres de destino e os caminhos de blob nos quais os arquivos de saída são gravados. Os arquivos mantidos no Armazenamento do Azure que segue o padrão Convenções de Arquivo ficam automaticamente disponíveis para exibição no portal do Azure. O portal está ciente da convenção de nomenclatura e, portanto, pode exibir arquivos que aderem a ela.

A biblioteca Convenções de Arquivo para .NET nomeia automaticamente seus contêineres de armazenamento e os arquivos de saída de tarefa acordo com o padrão de Convenções de Arquivo. A biblioteca Convenções de Arquivo também fornece métodos para consultar arquivos de saída no Armazenamento do Azure acordo com a ID do trabalho, a ID da tarefa ou a finalidade.

Se você estiver desenvolvendo com uma linguagem que não .NET, poderá implementar o padrão de Convenções de Arquivo você mesmo em seu aplicativo. Para obter mais informações, consulte [Implementar o padrão de convenções de arquivo de lote](batch-task-output.md#implement-the-batch-file-conventions-standard).

## <a name="link-an-azure-storage-account-to-your-batch-account"></a>Vincular uma conta de Armazenamento do Azure à sua conta do Lote

Para manter os dados de saída no Armazenamento do Azure usando a biblioteca Convenções de Arquivo, primeiro vincule uma conta de Armazenamento do Azure à sua conta do Lote. Se ainda não tiver fez isso, vincule uma conta de Armazenamento à sua conta do Lote usando o [portal do Azure](https://portal.azure.com):

1. Navegue até sua conta do Lote no portal do Azure.
1. Em **Configurações**, selecione **Conta de Armazenamento**.
1. Se você ainda não tiver uma conta de Armazenamento associada à sua conta do Lote, clique em **Conta de Armazenamento (Nenhuma)** .
1. Selecione uma conta de Armazenamento na lista para sua assinatura. Para melhor desempenho, use uma conta de Armazenamento do Azure que esteja na mesma região que a conta do Lote em que as tarefas estão em execução.

## <a name="persist-output-data"></a>Manter os dados de saída

Para manter os dados de saída de trabalho e tarefa com a biblioteca Convenções de Arquivo, crie um contêiner no Armazenamento do Azure e salve a saída no contêiner. Use a [biblioteca de cliente do Armazenamento do Azure para .NET](https://www.nuget.org/packages/WindowsAzure.Storage) em seu código de tarefa para carregar a saída da tarefa para o contêiner. 

Para obter mais informações sobre como trabalhar com contêineres e blobs no Armazenamento do Azure, consulte [Introdução ao armazenamento de Blobs do Azure usando o .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md).

> [!WARNING]
> Todas as saídas de trabalho e tarefa mantidas com a biblioteca Convenções de Arquivo são armazenadas no mesmo contêiner. Se um grande número de tarefas tentar manter arquivos ao mesmo tempo, poderão ser impostos [limites de limitação de armazenamento](../storage/common/storage-performance-checklist.md#blobs).

### <a name="create-storage-container"></a>Criar um contêiner de armazenamento

Para manter a saída da tarefa no armazenamento do Azure, primeiro crie um contêiner chamando [CloudJob][net_cloudjob].[PrepareOutputStorageAsync][net_prepareoutputasync]. Esse método de extensão usa um objeto [CloudStorageAccount][net_cloudstorageaccount] como um parâmetro. Ele cria um contêiner chamado de acordo com o padrão File Conventions, para que seu conteúdo possa ser descoberto pelo portal do Azure e pelos métodos de recuperação discutidos posteriormente no artigo.

Normalmente, você coloca o código para criar um contêiner no seu aplicativo cliente &mdash; o aplicativo que cria os pools, os trabalhos e as tarefas.

```csharp
CloudJob job = batchClient.JobOperations.CreateJob(
    "myJob",
    new PoolInformation { PoolId = "myPool" });

// Create reference to the linked Azure Storage account
CloudStorageAccount linkedStorageAccount =
    new CloudStorageAccount(myCredentials, true);

// Create the blob storage container for the outputs
await job.PrepareOutputStorageAsync(linkedStorageAccount);
```

### <a name="store-task-outputs"></a>Armazenar saídas de tarefas

Agora que você preparou um contêiner no armazenamento do Azure, as tarefas podem salvar a saída no contêiner usando a classe [TaskOutputStorage][net_taskoutputstorage] encontrada na biblioteca de convenções de arquivo.

No código da tarefa, primeiro crie um objeto [TaskOutputStorage][net_taskoutputstorage] e, depois que a tarefa tiver concluído seu trabalho, chame o método [TaskOutputStorage][net_taskoutputstorage] .[SaveAsync][net_saveasync] para salvar sua saída no armazenamento do Azure.

```csharp
CloudStorageAccount linkedStorageAccount = new CloudStorageAccount(myCredentials);
string jobId = Environment.GetEnvironmentVariable("AZ_BATCH_JOB_ID");
string taskId = Environment.GetEnvironmentVariable("AZ_BATCH_TASK_ID");

TaskOutputStorage taskOutputStorage = new TaskOutputStorage(
    linkedStorageAccount, jobId, taskId);

/* Code to process data and produce output file(s) */

await taskOutputStorage.SaveAsync(TaskOutputKind.TaskOutput, "frame_full_res.jpg");
await taskOutputStorage.SaveAsync(TaskOutputKind.TaskPreview, "frame_low_res.jpg");
```

O parâmetro `kind` do método [TaskOutputStorage](/dotnet/api/microsoft.azure.batch.conventions.files.taskoutputstorage).[SaveAsync](/dotnet/api/microsoft.azure.batch.conventions.files.taskoutputstorage.saveasync#overloads) categoriza os arquivos mantidos. Há quatro tipos de [TaskOutputKind][net_taskoutputkind] predefinidos `TaskPreview`: `TaskLog` `TaskOutput`,, `TaskIntermediate.` e você também pode definir categorias personalizadas de saída.

Esses tipos de saída permitem que você especifique qual tipo de saídas listar ao consultar posteriormente o Lote para obter as saídas persistentes de determinada tarefa. Em outras palavras, ao listar as saídas de uma tarefa, você pode filtrar a lista em um dos tipos de saída. Por exemplo, "Forneça a saída de *visualização* da tarefa *109*". Mais informações sobre listagem e recuperação de saídas estão disponíveis em Recuperar saída mais adiante no artigo.

> [!TIP]
> O tipo de saída também determina o local no portal do Azure em que um arquivo específico é exibido: Arquivos categorizados por *TaskOutput* aparecem sob **Arquivos de saída da tarefa**, e arquivos *TaskLog* aparecem sob **Logs de tarefa**.

### <a name="store-job-outputs"></a>Armazenar saídas de trabalhos

Além de armazenar as saídas de tarefas, você pode armazenar as saídas associadas a um trabalho inteiro. Por exemplo, na tarefa de mesclagem de um trabalho de renderização de filme, você pode persistir o filme totalmente renderizado como uma saída de trabalho. Quando seu trabalho estiver concluído, o aplicativo cliente poderá simplesmente listar e recuperar as saídas para o trabalho e não precisará consultar as tarefas individuais.

Armazene a saída do trabalho chamando o método [JobOutputStorage][net_joboutputstorage] .[SaveAsync][net_joboutputstorage_saveasync] e especifique [JobOutputKind][net_joboutputkind] e filename:

```csharp
CloudJob job = new JobOutputStorage(acct, jobId);
JobOutputStorage jobOutputStorage = job.OutputStorage(linkedStorageAccount);

await jobOutputStorage.SaveAsync(JobOutputKind.JobOutput, "mymovie.mp4");
await jobOutputStorage.SaveAsync(JobOutputKind.JobPreview, "mymovie_preview.mp4");
```

Assim como com o tipo **TaskOutputKind** para saídas de tarefa, você usa o tipo [JobOutputKind][net_joboutputkind] para categorizar os arquivos persistentes de um trabalho. Esse parâmetro permite que você consulte posteriormente para (listar) um tipo específico de saída. O tipo **JobOutputKind** inclui as categorias de saída e versão prévia e dá suporte à criação de categorias personalizadas.

### <a name="store-task-logs"></a>Armazenar logs de tarefas

Além de manter um arquivo no armazenamento durável quando uma tarefa ou trabalho é concluído, talvez seja necessário manter arquivos atualizados durante a execução de uma tarefa: arquivos de log &mdash; ou `stdout.txt` e `stderr.txt`, por exemplo. Para essa finalidade, a biblioteca de convenções de arquivo do lote do Azure fornece o método [TaskOutputStorage][net_taskoutputstorage] .[SaveTrackedAsync][net_savetrackedasync] . Com [SaveTrackedAsync][net_savetrackedasync], você pode acompanhar alterações em um arquivo no nó (em um intervalo que você especifica) e persistir essas atualizações no Armazenamento do Azure.

No trecho de código a seguir, usamos [SaveTrackedAsync][net_savetrackedasync] para atualizar `stdout.txt` no armazenamento do Azure a cada 15 segundos durante a execução da tarefa:

```csharp
TimeSpan stdoutFlushDelay = TimeSpan.FromSeconds(3);
string logFilePath = Path.Combine(
    Environment.GetEnvironmentVariable("AZ_BATCH_TASK_DIR"), "stdout.txt");

// The primary task logic is wrapped in a using statement that sends updates to
// the stdout.txt blob in Storage every 15 seconds while the task code runs.
using (ITrackedSaveOperation stdout =
        await taskStorage.SaveTrackedAsync(
        TaskOutputKind.TaskLog,
        logFilePath,
        "stdout.txt",
        TimeSpan.FromSeconds(15)))
{
    /* Code to process data and produce output file(s) */

    // We are tracking the disk file to save our standard output, but the
    // node agent may take up to 3 seconds to flush the stdout stream to
    // disk. So give the file a moment to catch up.
     await Task.Delay(stdoutFlushDelay);
}
```

A seção comentada `Code to process data and produce output file(s)` é um espaço reservado para o código que sua tarefa normalmente executaria. Por exemplo, você pode ter código que baixa dados do Armazenamento do Azure e executa uma transformação ou um cálculo neles. A parte importante desse trecho de código é demonstrar como você pode encapsular esse códigos em `using` um bloco para atualizar periodicamente um arquivo com [SaveTrackedAsync][net_savetrackedasync].

O agente do nó é um programa executado em cada nó no pool e fornece a interface de comando e controle entre o nó e o serviço do Lote. A chamada `Task.Delay` é necessária no final deste bloco `using` para garantir que o agente do nó tenha tempo para liberar o conteúdo do padrão que sai para o arquivo stdout.txt no nó. Sem esse atraso, é possível ignorar os últimos segundos de saída. Esse atraso pode não ser necessário para todos os arquivos.

> [!NOTE]
> Quando você habilita o acompanhamento de arquivo com **SaveTrackedAsync**, apenas *acréscimos* ao arquivo rastreado acompanhado são mantidos no Armazenamento do Azure. Use esse método somente para acompanhar arquivos de log não rotativos ou outros arquivos que são gravados com operações de acréscimo ao final do arquivo.

## <a name="retrieve-output-data"></a>Recuperar dados de saída

Quando você recupera sua saída persistente usando a biblioteca de Convenções de Arquivo do Lote do Azure, pode fazer isso de maneira voltada para tarefas e trabalhos. Você pode solicitar a saída para determinada tarefa ou trabalho sem precisar saber o caminho no Armazenamento do Azure nem mesmo seu nome de arquivo. Em vez disso, você pode solicitar os arquivos de saída pela ID de tarefa ou trabalho.

O snippet de código a seguir itera em todas as tarefas de um trabalho, imprime algumas informações sobre os arquivos de saída para a tarefa e então baixa os arquivos do Armazenamento.

```csharp
foreach (CloudTask task in myJob.ListTasks())
{
    foreach (OutputFileReference output in
        task.OutputStorage(storageAccount).ListOutputs(
            TaskOutputKind.TaskOutput))
    {
        Console.WriteLine($"output file: {output.FilePath}");

        output.DownloadToFileAsync(
            $"{jobId}-{output.FilePath}",
            System.IO.FileMode.Create).Wait();
    }
}
```

## <a name="view-output-files-in-the-azure-portal"></a>Exibir arquivos de saída no portal do Azure

O portal do Azure exibe os arquivos de saída de tarefa e os logs que são mantidos para uma conta de Armazenamento do Azure vinculado usando o [padrão de Convenções de Arquivo em Lote](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/Batch/Support/FileConventions#conventions). Você mesmo pode implementar essas convenções em uma linguagem de sua escolha ou pode usar a biblioteca de Convenções de Arquivo em seus aplicativos .NET.

Para habilitar a exibição de seus arquivos de saída no portal, você deve atender aos seguintes requisitos:

1. Vincular uma conta do Armazenamento do Azure à sua conta do Lote.
1. Seguir as convenções de nomenclatura predefinidas para os arquivos e contêineres de Armazenamento ao persistir saídas. Você pode encontrar a definição dessas convenções na biblioteca de convenções de arquivo [Leiame][github_file_conventions_readme] . If you use the [Azure Batch File Conventions][nuget_package] Library para persistir sua saída, os arquivos são persistidos de acordo com o padrão de convenções de arquivo.

Para exibir logs e arquivos de saída de tarefa no portal do Azure, navegue até a tarefa em cuja saída você está interessado e clique em **Arquivos de saída salvos** ou **Logs salvos**. Esta imagem mostra os **Arquivos de saída salvos** para a tarefa com a ID "007":

![Folha de saídas de tarefa no portal do Azure][2]

## <a name="code-sample"></a>Exemplo de código

O [PersistOutputs][github_persistoutputs] sample project is one of the [Azure Batch code samples][github_samples] no github. Essa solução do Visual Studio demonstra como usar a biblioteca de Convenções de Arquivo de Lote do Azure para persistir a saída da tarefa para o armazenamento durável. Para executar o exemplo, siga estas etapas:

1. Abra o projeto no **Visual Studio 2019**.
2. Adicione suas **credenciais de conta** do Lote e do Armazenamento a **AccountSettings.settings** no projeto Microsoft.Azure.Batch.Samples.Common.
3. **Compile** (mas não execute) a solução. Restaure todos os pacotes NuGet, se solicitado.
4. Use o portal do Azure para carregar um [pacote de aplicativos](batch-application-packages.md) para **PersistOutputsTask**. Inclua o `PersistOutputsTask.exe` e seus assemblies dependentes no pacote .zip, defina a ID do aplicativo como "PersistOutputsTask" e a versão do pacote de aplicativos como "1.0".
5. **Inicie** (execute) o projeto **PersistOutputs**.
6. Quando solicitado a escolher a tecnologia de persistência a usar para executar o exemplo, digite **1** para executar o exemplo usando a biblioteca Convenções de Arquivo para manter a saída da tarefa. 

## <a name="next-steps"></a>Próximas etapas

### <a name="get-the-batch-file-conventions-library-for-net"></a>Obter a biblioteca Convenções de Arquivo em Lote para .NET

A biblioteca de convenções de arquivo em lotes para .net está disponível nas classes [NuGet][nuget_package] . The library extends the [CloudJob][net_cloudjob] e [CloudTask][net_cloudtask] com novos métodos. Consulte também a [documentação de referência](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.conventions.files) para a biblioteca Convenções de Arquivo.

O [código-fonte][github_file_conventions] para a biblioteca de convenções de arquivo está disponível no GitHub no repositório Microsoft Azure SDK para .net. 

### <a name="explore-other-approaches-for-persisting-output-data"></a>Explorar outras abordagens para manter dados de saída

- Consulte [Manter saída de trabalho e tarefa para Armazenamento do Azure](batch-task-output.md) para obter uma visão geral de persistir dados de tarefa e trabalho.
- Consulte [Manter dados de tarefa para o Armazenamento do Azure com a API de serviço de Lote](batch-task-output-files.md) para aprender a usar a API de serviço de Lote para manter os dados de saída.

[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[github_file_conventions]: https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/Batch/FileConventions
[github_file_conventions_readme]: https://github.com/Azure/azure-sdk-for-net/blob/AutoRest/src/Batch/FileConventions/README.md
[github_persistoutputs]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs
[github_samples]: https://github.com/Azure/azure-batch-samples
[net_batchclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_cloudstorageaccount]: https://docs.microsoft.com/java/api/com.microsoft.azure.storage._cloud_storage_account
[net_cloudtask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_fileconventions_readme]: https://github.com/Azure/azure-sdk-for-net/blob/AutoRest/src/Batch/FileConventions/README.md
[net_joboutputkind]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.joboutputkind.aspx
[net_joboutputstorage]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.joboutputstorage.aspx
[net_joboutputstorage_saveasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.joboutputstorage.saveasync.aspx
[net_msdn]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[net_prepareoutputasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.cloudjobextensions.prepareoutputstorageasync.aspx
[net_saveasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputstorage.saveasync.aspx
[net_savetrackedasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputstorage.savetrackedasync.aspx
[net_taskoutputkind]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputkind.aspx
[net_taskoutputstorage]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputstorage.aspx
[nuget_manager]: https://docs.nuget.org/consume/installing-nuget
[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[portal]: https://portal.azure.com
[storage_explorer]: https://storageexplorer.com/

[1]: ./media/batch-task-output/task-output-01.png "Arquivos de saída salvos e Seletores de logs salvos no portal"
[2]: ./media/batch-task-output/task-output-02.png "Folha de saídas de tarefa no portal do Azure"
