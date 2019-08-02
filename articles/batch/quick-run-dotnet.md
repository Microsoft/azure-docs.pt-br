---
title: Início rápido do Azure - Executar trabalho do Lote - .NET
description: Execute rapidamente um trabalho do Lote e tarefas com a biblioteca de clientes .NET do Lote.
services: batch
author: laurenhughes
manager: gwallace
ms.service: batch
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 11/29/2018
ms.author: lahugh
ms.custom: mvc
ms.openlocfilehash: 37cd6fdd2f82af581e27f9341292c484b1cc601e
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68322336"
---
# <a name="quickstart-run-your-first-azure-batch-job-with-the-net-api"></a>Início Rápido: Executar o primeiro trabalho em Lote do Azure com a API do .NET

Este guia de início rápido executa um trabalho do Lote do Azure de um aplicativo em C# criado na API do .NET do Lote do Azure. O aplicativo carrega vários arquivos de dados de entrada no armazenamento do Azure e, em seguida, cria um *pool* de nós de computação (máquinas virtuais) do Lote. Em seguida, ele cria um exemplo de *trabalho* que executa *tarefas* para processar cada arquivo de entrada no pool usando um comando básico. Depois de concluir este guia de início rápido, você entenderá os conceitos principais do serviço Lote e estará pronto para experimentar o Lote com cargas de trabalho mais realistas em maior escala.

![Fluxo de trabalho de aplicativo do início rápido](./media/quick-run-dotnet/sampleapp.png)

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* [Visual Studio 2017 ou posterior](https://www.visualstudio.com/vs) ou [.NET Core 2.1](https://www.microsoft.com/net/download/dotnet-core/2.1) para Linux, macOS ou Windows. 

* Uma conta do Lote e uma conta de Armazenamento do Azure vinculada. Para criar essas contas, consulte os guias de início rápido do Lote usando o [portal do Azure](quick-create-portal.md) ou a [CLI do Azure](quick-create-cli.md). 

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

[!INCLUDE [batch-common-credentials](../../includes/batch-common-credentials.md)]

## <a name="download-the-sample"></a>Baixar o exemplo

[Baixe ou clone o aplicativo de exemplo](https://github.com/Azure-Samples/batch-dotnet-quickstart) do GitHub. Para clonar o repositório do aplicativo de exemplo com um cliente Git, use o seguinte comando:

```
git clone https://github.com/Azure-Samples/batch-dotnet-quickstart.git
```

Navegue até o diretório que contém o arquivo da solução Visual Studio `BatchDotNetQuickstart.sln`.

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

## <a name="build-and-run-the-app"></a>Compilar e executar o aplicativo

Para ver o fluxo de trabalho do Lote em ação, compile e execute o aplicativo no Visual Studio ou na linha de comando com os comandos `dotnet build` e `dotnet run`. Depois de executar o aplicativo, examine o código para saber o que cada parte do aplicativo faz. Por exemplo, no Visual Studio:

* Clique com botão direito do mouse na solução no Gerenciador de Soluções e clique em **Compilar Solução**. 

* Confirme a restauração de qualquer pacote NuGet, se solicitado. Se você precisar baixar pacotes ausentes, verifique se o [Gerenciador de Pacotes do NuGet](https://docs.nuget.org/consume/installing-nuget) está instalado.

Em seguida, execute-o. Quando você executa o aplicativo de exemplo, a saída do console fica mais ou menos assim. Durante a execução, você tem uma pausa em `Monitoring all tasks for 'Completed' state, timeout in 00:30:00...` enquanto os nós de computação do pool são iniciados. As tarefas são enfileiradas para execução assim que o primeiro nó de computação estiver em execução. Vá para sua conta do Lote no [portal do Azure](https://portal.azure.com) para monitorar o pool, os nós de computação, os trabalhos e as tarefas.

```
Sample start: 11/16/2018 4:02:54 PM

Container [input] created.
Uploading file taskdata0.txt to container [input]...
Uploading file taskdata1.txt to container [input]...
Uploading file taskdata2.txt to container [input]...
Creating pool [DotNetQuickstartPool]...
Creating job [DotNetQuickstartJob]...
Adding 3 tasks to job [DotNetQuickstartJob]...
Monitoring all tasks for 'Completed' state, timeout in 00:30:00...
```

Após a conclusão das tarefas, você verá uma saída semelhante à seguinte para cada tarefa:

```
Printing task output.
Task: Task0
Node: tvm-2850684224_3-20171205t000401z
Standard out:
Batch processing began with mainframe computers and punch cards. Today it still plays a central role in business, engineering, science, and other pursuits that require running lots of automated tasks....
stderr:
...
```

O tempo de execução típico é de aproximadamente cinco minutos ao executar o aplicativo em sua configuração padrão. A instalação inicial do pool leva mais tempo. Para executar o trabalho novamente, exclua o trabalho da execução anterior e não exclua o pool. Em um pool pré-configurado, o trabalho é concluído em alguns segundos.


## <a name="review-the-code"></a>Examine o código

O aplicativo .NET neste guia de início rápido faz o seguinte:

* Carrega três arquivos de texto pequenos em um contêiner de blob em sua conta de Armazenamento do Azure. Esses arquivos são entradas para o processamento pelo Lote.
* Cria um conjunto de nós de computação executando o Windows Server.
* Cria um trabalho e três tarefas a serem executadas em nós. Cada tarefa processa um dos arquivos de entrada usando uma linha de comando do Windows. 
* Exibe os arquivos retornados pelas tarefas.

Confira o arquivo `Program.cs` e as seções a seguir para obter mais detalhes. 

### <a name="preliminaries"></a>Etapas preliminares

Para interagir com a conta de armazenamento, o aplicativo usa a Biblioteca de Clientes de Armazenamento do Azure para .NET. Podemos criar uma referência à conta com [CloudStorageAccount](/dotnet/api/microsoft.azure.cosmos.table.cloudstorageaccount) e dela criar um [CloudBlobClient](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient).

```csharp
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
```

O aplicativo usa a referência `blobClient` para criar um contêiner na conta de armazenamento e carregar os arquivos de dados no contêiner. Os arquivos no armazenamento são definidos como objetos [ResourceFile](/dotnet/api/microsoft.azure.batch.resourcefile) do Lote que ele pode baixar mais tarde para os nós de computação.

```csharp
List<string> inputFilePaths = new List<string>
{
    "taskdata0.txt",
    "taskdata1.txt",
    "taskdata2.txt"
};

List<ResourceFile> inputFiles = new List<ResourceFile>();

foreach (string filePath in inputFilePaths)
{
    inputFiles.Add(UploadFileToContainer(blobClient, inputContainerName, filePath));
}
```

O aplicativo cria um objeto [BatchClient](/dotnet/api/microsoft.azure.batch.batchclient) para criar e gerenciar pools, trabalhos e tarefas no serviço Lote. O cliente do Lote no exemplo usa a autenticação de chave compartilhada. (O Lote também dá suporte à autenticação do Azure Active Directory.)

```csharp
BatchSharedKeyCredentials cred = new BatchSharedKeyCredentials(BatchAccountUrl, BatchAccountName, BatchAccountKey);

using (BatchClient batchClient = BatchClient.Open(cred))
...
```

### <a name="create-a-pool-of-compute-nodes"></a>Criar um pool de nós de computação

Para criar um pool do Lote, o aplicativo usa o método [BatchClient.PoolOperations.CreatePool](/dotnet/api/microsoft.azure.batch.pooloperations.createpool) para definir o número de nós, o tamanho da VM e uma configuração de pool. Aqui, um objeto [VirtualMachineConfiguration](/dotnet/api/microsoft.azure.batch.virtualmachineconfiguration) especifica uma [ImageReference](/dotnet/api/microsoft.azure.batch.imagereference) para uma imagem do Windows Server publicada no Azure Marketplace. O Lote dá suporte a uma ampla gama de imagens do Linux e do Windows Server no Azure Marketplace, bem como imagens de VM personalizadas.

O número de nós (`PoolNodeCount`) e o tamanho da VM (`PoolVMSize`) são constantes definidas. O exemplo cria por padrão um pool com dois nós de tamanho *Standard_A1_v2*. O tamanho sugerido oferece um bom equilíbrio entre desempenho e custo para este exemplo rápido.

O método [Commit](/dotnet/api/microsoft.azure.batch.cloudpool.commit) envia o pool para o serviço Lote.

```csharp

private static VirtualMachineConfiguration CreateVirtualMachineConfiguration(ImageReference imageReference)
{
    return new VirtualMachineConfiguration(
        imageReference: imageReference,
        nodeAgentSkuId: "batch.node.windows amd64");
}

private static ImageReference CreateImageReference()
{
    return new ImageReference(
        publisher: "MicrosoftWindowsServer",
        offer: "WindowsServer",
        sku: "2016-datacenter-smalldisk",
        version: "latest");
}

private static void CreateBatchPool(BatchClient batchClient, VirtualMachineConfiguration vmConfiguration)
{
    try
    {
        CloudPool pool = batchClient.PoolOperations.CreatePool(
            poolId: PoolId,
            targetDedicatedComputeNodes: PoolNodeCount,
            virtualMachineSize: PoolVMSize,
            virtualMachineConfiguration: vmConfiguration);

        pool.Commit();
    }
...

```

### <a name="create-a-batch-job"></a>Criar um trabalho do Lote

Um trabalho do Lote é um agrupamento lógico de uma ou mais tarefas. Um trabalho inclui configurações comuns às tarefas, como prioridade e o pool onde elas devem ser executadas. O aplicativo usa o método [BatchClient.JobOperations.CreateJob](/dotnet/api/microsoft.azure.batch.joboperations.createjob) para criar um trabalho em seu pool.

O método [Commit](/dotnet/api/microsoft.azure.batch.cloudjob.commit) envia o trabalho para o serviço Lote. Inicialmente, o trabalho não tem nenhuma tarefa.

```csharp
try
{
    CloudJob job = batchClient.JobOperations.CreateJob();
    job.Id = JobId;
    job.PoolInformation = new PoolInformation { PoolId = PoolId };

    job.Commit();
}
...
```

### <a name="create-tasks"></a>Criar tarefas

O aplicativo cria uma lista de objetos [CloudTask](/dotnet/api/microsoft.azure.batch.cloudtask). Cada tarefa processa um objeto `ResourceFile` de entrada usando uma propriedade [CommandLine](/dotnet/api/microsoft.azure.batch.cloudtask.commandline). No exemplo, a linha de comando executa o comando Windows `type` para exibir o arquivo de entrada. Esse comando é um exemplo simples para fins de demonstração. Quando você usa o Lote, a linha de comando é onde você especifica seu aplicativo ou script. O Lote fornece várias maneiras para implantar aplicativos e scripts em nós de computação.

Em seguida, o aplicativo adiciona tarefas ao trabalho com o método [AddTask](/dotnet/api/microsoft.azure.batch.joboperations.addtask), que os enfileira para execução nos nós de computação.

```csharp
for (int i = 0; i < inputFiles.Count; i++)
{
    string taskId = String.Format("Task{0}", i);
    string inputFilename = inputFiles[i].FilePath;
    string taskCommandLine = String.Format("cmd /c type {0}", inputFilename);

    CloudTask task = new CloudTask(taskId, taskCommandLine);
    task.ResourceFiles = new List<ResourceFile> { inputFiles[i] };
    tasks.Add(task);
}

batchClient.JobOperations.AddTask(JobId, tasks);
```

### <a name="view-task-output"></a>Exibir saída da tarefa

O aplicativo cria um [TaskStateMonitor](/dotnet/api/microsoft.azure.batch.taskstatemonitor) para monitorar as tarefas e fazer com que sejam concluídas. Em seguida, o aplicativo usa a propriedade [CloudTask.ComputeNodeInformation](/dotnet/api/microsoft.azure.batch.cloudtask.computenodeinformation) para exibir o arquivo `stdout.txt` gerado por cada tarefa concluída. Quando a tarefa é executada com êxito, a saída do comando de tarefa é gravada em `stdout.txt`:

```csharp
foreach (CloudTask task in completedtasks)
{
    string nodeId = String.Format(task.ComputeNodeInformation.ComputeNodeId);
    Console.WriteLine("Task: {0}", task.Id);
    Console.WriteLine("Node: {0}", nodeId);
    Console.WriteLine("Standard out:");
    Console.WriteLine(task.GetNodeFile(Constants.StandardOutFileName).ReadAsString());
}
```

## <a name="clean-up-resources"></a>Limpar recursos

O aplicativo exclui automaticamente o contêiner de armazenamento que cria e oferece a opção para excluir o pool e o trabalho do Lote. Você é cobrado pelo pool enquanto os nós estão em execução, mesmo se não há trabalhos agendados. Quando você não precisa mais do pool, exclua-o. Quando você excluir o pool, todas as saídas de tarefa nos nós são excluídas.

Quando não forem mais necessário, exclua o grupo de recursos, a conta do Lote e a conta de armazenamento. Para fazer isso no Portal do Azure, selecione o grupo de recursos para a conta do Lote e clique em **Excluir grupo de recursos**.

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você executou um pequeno aplicativo compilado com a API de .NET do Lote para criar um pool do Lote e um trabalho do Lote. O trabalho executou os exemplos de tarefas e baixou a saída criada nos nós. Agora que você conhece os conceitos principais do serviço Lote, está pronto para experimentar o Lote com cargas de trabalho mais realistas em maior escala. Para saber mais sobre o Lote do Azure e percorrer uma carga de trabalho paralela com um aplicativo real, continue com o tutorial de .NET do Lote.


> [!div class="nextstepaction"]
> [Processar uma carga de trabalho paralela com o .NET](tutorial-parallel-dotnet.md)
