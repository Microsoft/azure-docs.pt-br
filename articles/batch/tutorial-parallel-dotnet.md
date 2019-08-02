---
title: Executar uma carga de trabalho paralela - .NET do Lote do Azure
description: 'Tutorial: transcodificar arquivos de mídia em paralelo com ffmpeg no Lote do Azure usando a biblioteca de cliente .NET do Lote'
services: batch
author: laurenhughes
manager: gwallace
ms.assetid: ''
ms.service: batch
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: lahugh
ms.custom: mvc
ms.openlocfilehash: 4350cc215c776317d3bde24c7561c317a31fb4c3
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68321864"
---
# <a name="tutorial-run-a-parallel-workload-with-azure-batch-using-the-net-api"></a>Tutorial: Executar uma carga de trabalho paralela com o Lote do Azure usando a API do .NET

Use o Lote do Azure para executar trabalhos em lote de HPC (computação de alto desempenho) e paralelos em larga escala com eficiência no Azure. Este tutorial percorre um exemplo de C# para executar uma carga de trabalho paralela usando o Lote. Você conhecerá um fluxo de trabalho de aplicativo comum no Lote e como interagir programaticamente com recursos do Armazenamento e do Lote. Você aprenderá como:

> [!div class="checklist"]
> * Adicionar um pacote de aplicativos à sua conta do Lote
> * Autenticar com as contas do Lote e do Armazenamento
> * Carregar arquivos de entrada no armazenamento
> * Criar um conjunto de nós de computação para executar um aplicativo
> * Criar um trabalho e tarefas para processar arquivos de entrada
> * Monitorar a execução de tarefas
> * Recuperar arquivos de saída

Neste tutorial, você converte os arquivos de mídia MP4 em paralelo para o formato MP3 usando a ferramenta de software livre [ffmpeg](https://ffmpeg.org/). 

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* [Visual Studio 2017 ou posterior](https://www.visualstudio.com/vs) ou [.NET Core 2.1](https://www.microsoft.com/net/download/dotnet-core/2.1) para Linux, macOS ou Windows.

* Uma conta do Lote e uma conta de Armazenamento do Azure vinculada. Para criar essas contas, consulte os guias de início rápido do Lote usando o [portal do Azure](quick-create-portal.md) ou a [CLI do Azure](quick-create-cli.md).

* [Versão de 64 bits do Windows do ffmpeg 3.4](https://ffmpeg.zeranoe.com/builds/win64/static/ffmpeg-3.4-win64-static.zip) (. zip). Baixe o arquivo zip em seu computador local. Para este tutorial, você só precisa do arquivo zip. Não é necessário descompactar o arquivo ou instalá-lo localmente.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="add-an-application-package"></a>Adicionar um pacote de aplicativos

Use o portal do Azure para adicionar o ffmpeg à sua conta do Lote como um [pacote de aplicativos](batch-application-packages.md). Os pacotes de aplicativos ajudam a gerenciar aplicativos de tarefas e sua implantação nos nós de computação em seu pool. 

1. No portal do Azure, clique em **Mais serviços** > **Contas do Lote** e clique no nome da sua conta do Lote.
3. Clique em **Aplicativos** > **Adicionar**.
4. Como **Id do aplicativo**, insira *ffmpeg*e uma versão do pacote de *3.4*. Selecione o arquivo zip de ffmpeg que você baixou anteriormente e clique em **OK**. O pacote de aplicativos ffmpeg é adicionado à sua conta do Lote.

![Adicionar pacote de aplicativos](./media/tutorial-parallel-dotnet/add-application.png)

[!INCLUDE [batch-common-credentials](../../includes/batch-common-credentials.md)]

## <a name="download-and-run-the-sample"></a>Baixar e executar o exemplo

### <a name="download-the-sample"></a>Baixar o exemplo

[Baixe ou clone o aplicativo de exemplo](https://github.com/Azure-Samples/batch-dotnet-ffmpeg-tutorial) do GitHub. Para clonar o repositório do aplicativo de exemplo com um cliente Git, use o seguinte comando:

```
git clone https://github.com/Azure-Samples/batch-dotnet-ffmpeg-tutorial.git
```

Navegue até o diretório que contém o arquivo da solução Visual Studio `BatchDotNetFfmpegTutorial.sln`.

Abra o arquivo da solução no Visual Studio e atualize as cadeias de caracteres da credencial `Program.cs` com os valores obtidos para suas contas. Por exemplo:

```csharp
// Batch account credentials
private const string BatchAccountName = "mybatchaccount";
private const string BatchAccountKey  = "xxxxxxxxxxxxxxxxE+yXrRvJAqT9BlXwwo1CwF+SwAYOxxxxxxxxxxxxxxxx43pXi/gdiATkvbpLRl3x14pcEQ==";
private const string BatchAccountUrl  = "https://mybatchaccount.mybatchregion.batch.azure.com";

// Storage account credentials
private const string StorageAccountName = "mystorageaccount";
private const string StorageAccountKey  = "xxxxxxxxxxxxxxxxy4/xxxxxxxxxxxxxxxxfwpbIC5aAWA8wDu+AFXZB827Mt9lybZB1nUcQbQiUrkPtilK5BQ==";
```

[!INCLUDE [batch-credentials-include](../../includes/batch-credentials-include.md)]

Além disso, verifique se a referência do pacote de aplicativos ffmpeg na solução corresponde à Id e à versão do pacote ffmpeg que você carregou na sua conta do Lote.

```csharp
const string appPackageId = "ffmpeg";
const string appPackageVersion = "3.4";
```

### <a name="build-and-run-the-sample-project"></a>Criar e executar o projeto de exemplo

Compile e execute o aplicativo no Visual Studio ou na linha de comando com os comandos `dotnet build` e `dotnet run`. Depois de executar o aplicativo, examine o código para saber o que cada parte do aplicativo faz. Por exemplo, no Visual Studio:

* Clique com botão direito do mouse na solução no Gerenciador de Soluções e clique em **Compilar Solução**. 

* Confirme a restauração de qualquer pacote NuGet, se solicitado. Se você precisar baixar pacotes ausentes, verifique se o [Gerenciador de Pacotes do NuGet](https://docs.nuget.org/consume/installing-nuget) está instalado.

Em seguida, execute-o. Quando você executa o aplicativo de exemplo, a saída do console fica mais ou menos assim. Durante a execução, você tem uma pausa em `Monitoring all tasks for 'Completed' state, timeout in 00:30:00...` enquanto os nós de computação do pool são iniciados. 

```
Sample start: 11/19/2018 3:20:21 PM

Container [input] created.
Container [output] created.
Uploading file LowPriVMs-1.mp4 to container [input]...
Uploading file LowPriVMs-2.mp4 to container [input]...
Uploading file LowPriVMs-3.mp4 to container [input]...
Uploading file LowPriVMs-4.mp4 to container [input]...
Uploading file LowPriVMs-5.mp4 to container [input]...
Creating pool [WinFFmpegPool]...
Creating job [WinFFmpegJob]...
Adding 5 tasks to job [WinFFmpegJob]...
Monitoring all tasks for 'Completed' state, timeout in 00:30:00...
Success! All tasks completed successfully within the specified timeout period.
Deleting container [input]...

Sample end: 11/19/2018 3:29:36 PM
Elapsed time: 00:09:14.3418742
```

Vá para sua conta do Lote no portal do Azure para monitorar o pool, os nós de computação, os trabalhos e as tarefas. Por exemplo, para ver um mapa de calor dos nós de computação em seu pool, clique em **Pools** > *WinFFmpegPool*.

Quando as tarefas são executadas, o mapa de calor fica mais ou menos assim:

![Mapa de calor de pool](./media/tutorial-parallel-dotnet/pool.png)

O tempo de execução típico é de aproximadamente **10 minutos** ao executar o aplicativo em sua configuração padrão. A criação de pool leva mais tempo.

[!INCLUDE [batch-common-tutorial-download](../../includes/batch-common-tutorial-download.md)]

## <a name="review-the-code"></a>Examine o código

As seções a seguir separa o aplicativo de exemplo nas etapas executadas para processar uma carga de trabalho no serviço Lote. Veja o arquivo `Program.cs` na solução ao ler o restante deste artigo, pois nem toda linha de código no exemplo é discutida.

### <a name="authenticate-blob-and-batch-clients"></a>Autenticar clientes de Blob e do Lote

Para interagir com a conta de armazenamento vinculada, o aplicativo usa a Biblioteca de Clientes de Armazenamento do Azure para .NET. Ele cria uma referência à conta com [CloudStorageAccount](/dotnet/api/microsoft.azure.cosmos.table.cloudstorageaccount), autenticando usando a autenticação de chave compartilhada. Em seguida, ele cria um [CloudBlobClient](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient).

```csharp
// Construct the Storage account connection string
string storageConnectionString = String.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}",
                                StorageAccountName, StorageAccountKey);

// Retrieve the storage account
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(storageConnectionString);

CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
```

O aplicativo cria um objeto [BatchClient](/dotnet/api/microsoft.azure.batch.batchclient) para criar e gerenciar pools, trabalhos e tarefas no serviço Lote. O cliente do Lote no exemplo usa a autenticação de chave compartilhada. O Lote também dá suporte à autenticação por meio do [Azure Active Directory](batch-aad-auth.md) para autenticar usuários individuais ou um aplicativo autônomo.

```csharp
BatchSharedKeyCredentials sharedKeyCredentials = new BatchSharedKeyCredentials(BatchAccountUrl, BatchAccountName, BatchAccountKey);

using (BatchClient batchClient = BatchClient.Open(sharedKeyCredentials))
...
```

### <a name="upload-input-files"></a>Carregar arquivos de entrada

A aplicativo passa o objeto `blobClient` para o método `CreateContainerIfNotExistAsync` a fim de criar um contêiner de armazenamento para os arquivos de entrada (formato MP4) e um contêiner para a saída da tarefa.

```csharp
CreateContainerIfNotExistAsync(blobClient, inputContainerName;
CreateContainerIfNotExistAsync(blobClient, outputContainerName);
```

Em seguida, os arquivos são carregados para o contêiner de entrada na pasta `InputFiles` local. Os arquivos no armazenamento são definidos como objetos [ResourceFile](/dotnet/api/microsoft.azure.batch.resourcefile) do Lote que ele pode baixar mais tarde para os nós de computação. 

Dois métodos no `Program.cs` são envolvidos no carregamento de arquivos:

* `UploadResourceFilesToContainerAsync`: Retorna uma coleção de objetos ResourceFile e chama internamente `UploadResourceFileToContainerAsync` para carregar cada arquivo que é passado no parâmetro `inputFilePaths`.
* `UploadResourceFileToContainerAsync`: Carrega cada arquivo como um blob no contêiner de entrada. Depois de carregar o arquivo, ele obtém uma assinatura de acesso compartilhado (SAS) para o blob e retorna um objeto ResourceFile para representá-la.

```csharp
string inputPath = Path.Combine(Environment.CurrentDirectory, "InputFiles");

List<string> inputFilePaths = new List<string>(Directory.GetFileSystemEntries(inputPath, "*.mp4",
    SearchOption.TopDirectoryOnly));

List<ResourceFile> inputFiles = await UploadResourceFilesToContainerAsync(
  blobClient,
  inputContainerName,
  inputFilePaths);
```

Para obter detalhes de como carregar arquivos como blobs em uma conta de armazenamento com o .NET, confira [Carregar, baixar e listar blobs usando o .NET](../storage/blobs/storage-quickstart-blobs-dotnet.md).

### <a name="create-a-pool-of-compute-nodes"></a>Criar um pool de nós de computação

Em seguida, o exemplo cria um pool de nós de computação na conta do Lote com uma chamada para `CreatePoolIfNotExistAsync`. Esse método definido usa o método [BatchClient.PoolOperations.CreatePool](/dotnet/api/microsoft.azure.batch.pooloperations.createpool) para definir o número de nós, o tamanho da VM e uma configuração de pool. Aqui, um objeto [VirtualMachineConfiguration](/dotnet/api/microsoft.azure.batch.virtualmachineconfiguration) especifica uma [ImageReference](/dotnet/api/microsoft.azure.batch.imagereference) para uma imagem do Windows Server publicada no Azure Marketplace. O Lote dá suporte a uma ampla gama de imagens de VM no Azure Marketplace, bem como imagens de VM personalizadas.

O número de nós e o tamanho da VM são definidos usando constantes definidas. O Lote dá suporte a nós dedicados e a [nós de baixa prioridade](batch-low-pri-vms.md), e você pode usar um ou ambos em seus pools. Nós dedicados são reservados para o pool. Nós de baixa prioridade são oferecidos a um preço menor do excedente de capacidade da VM no Azure. Nós de baixa prioridade ficam indisponíveis quando o Azure não tem capacidade suficiente. O exemplo, por padrão, cria um pool que contém apenas cinco nós de baixa prioridade em tamanho *Standard_A1_v2*.

O aplicativo ffmpeg for implantado para os nós de computação adicionando um [ApplicationPackageReference](/dotnet/api/microsoft.azure.batch.applicationpackagereference) à configuração do pool.

O método [CommitAsync](/dotnet/api/microsoft.azure.batch.cloudpool.commitasync) envia o pool para o serviço Lote.

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "MicrosoftWindowsServer",
    offer: "WindowsServer",
    sku: "2016-Datacenter-smalldisk",
    version: "latest");

VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration(
    imageReference: imageReference,
    nodeAgentSkuId: "batch.node.windows amd64");

pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: DedicatedNodeCount,
    targetLowPriorityComputeNodes: LowPriorityNodeCount,
    virtualMachineSize: PoolVMSize,
    virtualMachineConfiguration: virtualMachineConfiguration);

pool.ApplicationPackageReferences = new List<ApplicationPackageReference>
    {
    new ApplicationPackageReference {
    ApplicationId = appPackageId,
    Version = appPackageVersion}};

await pool.CommitAsync();  
```

### <a name="create-a-job"></a>Criar um trabalho

Um trabalho do Lote especifica um pool onde executar tarefas, e configurações opcionais, como uma prioridade e uma agenda para o trabalho. O exemplo cria um trabalho com uma chamada para `CreateJobAsync`. Esse método definido usa o método [BatchClient.JobOperations.CreateJob](/dotnet/api/microsoft.azure.batch.joboperations.createjob) para criar um trabalho em seu pool.

O método [CommitAsync](/dotnet/api/microsoft.azure.batch.cloudjob.commitasync) envia o trabalho para o serviço Lote. Inicialmente, o trabalho não tem nenhuma tarefa.

```csharp
CloudJob job = batchClient.JobOperations.CreateJob();
job.Id = JobId;
job.PoolInformation = new PoolInformation { PoolId = PoolId };

await job.CommitAsync();
```

### <a name="create-tasks"></a>Criar tarefas

O exemplo cria tarefas no trabalho com uma chamada para o método `AddTasksAsync`, que cria uma lista de objetos [CloudTask](/dotnet/api/microsoft.azure.batch.cloudtask). Cada `CloudTask` executa ffmpeg para processar um objeto `ResourceFile` de entrada usando uma propriedade [CommandLine](/dotnet/api/microsoft.azure.batch.cloudtask.commandline). O ffmpeg anteriormente foi instalado em cada nó quando o pool foi criado. Aqui, a linha de comando executa ffmpeg para converter cada arquivo MP4 (vídeo) de entrada em um arquivo MP3 (áudio).

O exemplo cria um objeto [OutputFile](/dotnet/api/microsoft.azure.batch.outputfile) para o arquivo MP3 depois de executar a linha de comando. Os arquivos de saída de cada tarefa (um, neste caso) são carregados em um contêiner na conta de armazenamento vinculada, usando a propriedade [OutputFiles](/dotnet/api/microsoft.azure.batch.cloudtask.outputfiles) da tarefa. Anteriormente no exemplo de código, uma URL de assinatura de acesso compartilhado (`outputContainerSasUrl`) foi obtida para fornecer acesso de gravação ao contêiner de saída. Observe as condições definidas no objeto `outputFile`. Um arquivo de saída de uma tarefa é carregado no contêiner somente depois que a tarefa é concluída com êxito (`OutputFileUploadCondition.TaskSuccess`). Confira o [exemplo de código](https://github.com/Azure-Samples/batch-dotnet-ffmpeg-tutorial) completo no GitHub para obter mais detalhes de implementação.

Em seguida, o exemplo adiciona tarefas ao trabalho com o método [AddTaskAsync](/dotnet/api/microsoft.azure.batch.joboperations.addtaskasync), que os enfileira para execução nos nós de computação.

```csharp
 // Create a collection to hold the tasks added to the job.
List<CloudTask> tasks = new List<CloudTask>();

for (int i = 0; i < inputFiles.Count; i++)
{
    string taskId = String.Format("Task{0}", i);

    // Define task command line to convert each input file.
    string appPath = String.Format("%AZ_BATCH_APP_PACKAGE_{0}#{1}%", appPackageId, appPackageVersion);
    string inputMediaFile = inputFiles[i].FilePath;
    string outputMediaFile = String.Format("{0}{1}",
        System.IO.Path.GetFileNameWithoutExtension(inputMediaFile),
        ".mp3");
    string taskCommandLine = String.Format("cmd /c {0}\\ffmpeg-3.4-win64-static\\bin\\ffmpeg.exe -i {1} {2}", appPath, inputMediaFile, outputMediaFile);

    // Create a cloud task (with the task ID and command line)
    CloudTask task = new CloudTask(taskId, taskCommandLine);
    task.ResourceFiles = new List<ResourceFile> { inputFiles[i] };

    // Task output file
    List<OutputFile> outputFileList = new List<OutputFile>();
    OutputFileBlobContainerDestination outputContainer = new OutputFileBlobContainerDestination(outputContainerSasUrl);
    OutputFile outputFile = new OutputFile(outputMediaFile,
       new OutputFileDestination(outputContainer),
       new OutputFileUploadOptions(OutputFileUploadCondition.TaskSuccess));
    outputFileList.Add(outputFile);
    task.OutputFiles = outputFileList;
    tasks.Add(task);
}

// Add tasks as a collection
await batchClient.JobOperations.AddTaskAsync(jobId, tasks);
return tasks
```

### <a name="monitor-tasks"></a>Monitorar tarefas

Quando o Lote adiciona tarefas a um trabalho, o serviço as enfileira e agenda para execução em nós de computação no pool associado automaticamente. Com base nas configurações especificadas, o Lote manipula o enfileiramento, o agendamento, a repetição de todas as tarefas e outras obrigações de administração de tarefas.

Há muitas abordagens para o monitoramento da execução da tarefa. Esse exemplo define um método `MonitorTasks` para relatar apenas sobre estados de falha ou sucesso de tarefas e sua conclusão. O código `MonitorTasks` especifica um [ODATADetailLevel](/dotnet/api/microsoft.azure.batch.odatadetaillevel) para selecionar com eficiência apenas algumas informações sobre as tarefas. Em seguida, ele cria um [TaskStateMonitor](/dotnet/api/microsoft.azure.batch.taskstatemonitor), que fornece utilitários auxiliares para monitorar os estados de tarefa. Em `MonitorTasks`, o exemplo aguarda até que todas as tarefas alcancem `TaskState.Completed` dentro de um limite de tempo. Em seguida, ele conclui o trabalho e relata as tarefas concluídas, mas com falhas de código de saída diferente de zero.

```csharp
TaskStateMonitor taskStateMonitor = batchClient.Utilities.CreateTaskStateMonitor();
try
{
    await taskStateMonitor.WhenAll(addedTasks, TaskState.Completed, timeout);
}
catch (TimeoutException)
{
    batchClient.JobOperations.TerminateJob(jobId);
    Console.WriteLine(incompleteMessage);
    return false;
}
batchClient.JobOperations.TerminateJob(jobId);
 Console.WriteLine(completeMessage);
...

```

## <a name="clean-up-resources"></a>Limpar recursos

Depois que ele executa as tarefas, o aplicativo exclui automaticamente o contêiner de armazenamento de entrada criado e oferece a opção de excluir o pool do Lote e o trabalho. As classes [JobOperations](/dotnet/api/microsoft.azure.batch.batchclient.joboperations) e [PoolOperations](/dotnet/api/microsoft.azure.batch.batchclient.pooloperations) do BatchClient têm métodos de exclusão correspondentes, chamados se você confirmar a exclusão. Embora você não seja cobrado pelos trabalhos e pelas tarefas, será cobrado pelos nós de computação. Portanto, recomendamos que você aloque os pools conforme a necessidade. Quando você excluir o pool, todas as saídas de tarefa nos nós são excluídas. No entanto, os arquivos de saída permanecerão na conta de armazenamento.

Quando não forem mais necessário, exclua o grupo de recursos, a conta do Lote e a conta de armazenamento. Para fazer isso no Portal do Azure, selecione o grupo de recursos para a conta do Lote e clique em **Excluir grupo de recursos**.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Adicionar um pacote de aplicativos à sua conta do Lote
> * Autenticar com as contas do Lote e do Armazenamento
> * Carregar arquivos de entrada no armazenamento
> * Criar um conjunto de nós de computação para executar um aplicativo
> * Criar um trabalho e tarefas para processar arquivos de entrada
> * Monitorar a execução de tarefas
> * Recuperar arquivos de saída

Para obter mais exemplos de como usar a API do .NET para agendar e processar cargas de trabalho do Lote, consulte os exemplos no GitHub.

> [!div class="nextstepaction"]
> [Exemplos em C# do Lote](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp)
