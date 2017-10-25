---
title: Tutorial - Use a biblioteca de cliente do Lote do Azure para .NET | Microsoft Docs
description: "Aprenda os conceitos básicos do Lote do Azure e crie uma solução simples usando o .NET."
services: batch
documentationcenter: .net
author: tamram
manager: timlt
editor: 
ms.assetid: 76cb9807-cbc1-405a-8136-d1e53e66e82b
ms.service: batch
ms.devlang: dotnet
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-compute
ms.date: 06/28/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cf8fdca51a6a4ad1b7cd4fe6980543199f6b36e0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-building-solutions-with-the-batch-client-library-for-net"></a>Comece a criar soluções com a biblioteca de cliente do Lote para .NET

> [!div class="op_single_selector"]
> * [.NET](batch-dotnet-get-started.md)
> * [Python](batch-python-tutorial.md)
> * [Node.js](batch-nodejs-get-started.md)
>
>

Conheça os fundamentos do [Lote do Azure][azure_batch] e da biblioteca [.NET do Lote][net_api] neste artigo, em que mostramos o passo a passo de um aplicativo de exemplo em C#. Veremos como o aplicativo de exemplo aproveita o serviço Lote para processar uma carga de trabalho paralela na nuvem e como ele interage com o [Armazenamento do Azure](../storage/common/storage-introduction.md) para a preparação e a recuperação de arquivos. Você verá um fluxo de trabalho comum do aplicativo Lote e obterá uma compreensão básica dos principais componentes do Lote, como trabalhos, tarefas, pools e nós de computação.

![Fluxo de trabalho da solução do Lote (básico)][11]<br/>

## <a name="prerequisites"></a>Pré-requisitos
Este artigo pressupõe que você tenha um conhecimento prático do C# e do Visual Studio. Ele também pressupõe que você é capaz de satisfazer os requisitos de criação de conta especificados abaixo para o Azure e os serviços Lote e Armazenamento.

### <a name="accounts"></a>Contas
* **Conta do Azure**: se você ainda não tiver uma assinatura do Azure, [crie uma conta gratuita do Azure][azure_free_account].
* **Conta do Lote**: quando você tiver uma assinatura do Azure, [crie uma conta do Lote do Azure](batch-account-create-portal.md).
* **Conta de armazenamento**: veja [Criar uma conta de armazenamento](../storage/common/storage-create-storage-account.md#create-a-storage-account) em [Sobre as contas de armazenamento do Azure](../storage/common/storage-create-storage-account.md).

> [!IMPORTANT]
> No momento, o Lote dá suporte *somente* ao tipo de conta de armazenamento de **uso geral**, conforme descrito na etapa 5 [Criar uma conta de armazenamento](../storage/common/storage-create-storage-account.md#create-a-storage-account) em [Sobre as contas de armazenamento do Azure](../storage/common/storage-create-storage-account.md).
>
>

### <a name="visual-studio"></a>Visual Studio
Você deve ter o **Visual Studio 2015 ou mais recente** para compilar o projeto de exemplo. Você pode encontrar versões gratuitas e de avaliação do Visual Studio na [visão geral dos produtos do Visual Studio][visual_studio].

### <a name="dotnettutorial-code-sample"></a>*DotNetTutorial* 
O exemplo [DotNetTutorial][github_dotnettutorial] é um dos vários exemplos de código de Lote encontrados no repositório [azure-batch-samples][github_samples] no GitHub. Você pode baixar todos os exemplos clicando no botão **Clonar ou baixar > Baixar ZIP** na home page do repositório ou clicando no link de download direto de [azure-batch-samples-master.zip][github_samples_zip]. Depois de extrair o conteúdo do arquivo ZIP, você poderá encontrar a solução na seguinte pasta:

`\azure-batch-samples\CSharp\ArticleProjects\DotNetTutorial`

### <a name="azure-batch-explorer-optional"></a>Gerenciador do Lote do Azure (opcional)
O [Gerenciador do Lote do Azure][github_batchexplorer] é um utilitário gratuito incluído no repositório [azure-batch-samples][github_samples] no GitHub. Embora não seja necessário para concluir este tutorial, pode ser útil ao desenvolver e depurar suas soluções do Lote.

## <a name="dotnettutorial-sample-project-overview"></a>Visão geral do projeto de exemplo DotNetTutorial
O exemplo de código *DotNetTutorial* é uma solução do Visual Studio que consiste em dois projetos: **DotNetTutorial** e **TaskApplication**.

* **DotNetTutorial** é o aplicativo cliente que interage com os serviços Lote e Armazenamento para executar uma carga de trabalho paralela em nós de computação (máquinas virtuais). O DotNetTutorial é executado em sua estação de trabalho local.
* **TaskApplication** é o programa executado em nós de computação no Azure para executar o trabalho real. No exemplo, `TaskApplication.exe` analisa o texto em um arquivo baixado do Armazenamento do Azure (o arquivo de entrada). Em seguida, ele produz um arquivo de texto (o arquivo de saída) que contém uma lista das três palavras principais que aparecem no arquivo de entrada. Após criar o arquivo de saída, TaskApplication carrega o arquivo no Armazenamento do Azure. Isso o disponibiliza para download para o aplicativo cliente. O TaskApplication é executado em paralelo em vários nós de computação no serviço Lote.

O diagrama a seguir ilustra as principais operações executadas pelo aplicativo cliente, *DotNetTutorial*, e o aplicativo executado pelas tarefas, *TaskApplication*. Esse fluxo de trabalho básico é típico de muitas soluções de computação criadas com o Lote. Embora ele não demonstre todos os recursos disponíveis no serviço Lote, praticamente todos os cenários do Lote incluem partes desse fluxo de trabalho.

![Fluxo de trabalho de exemplo do Lote][8]<br/>

[**Etapa 1.**](#step-1-create-storage-containers) Crie **contêineres** no Armazenamento de Blobs do Azure.<br/>
[**Etapa 2.**](#step-2-upload-task-application-and-data-files) Carregue o aplicativo de tarefas e os arquivos de entrada nos contêineres.<br/>
[**Etapa 3.**](#step-3-create-batch-pool) Criar um **pool** do Lote.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**3a.** O pool **StartTask** baixa os arquivos binários da tarefa (TaskApplication) para os nós quando eles ingressam no pool.<br/>
[**Etapa 4.**](#step-4-create-batch-job) Crie um **trabalho** do Lote.<br/>
[**Etapa 5.**](#step-5-add-tasks-to-job) Adicione **tarefas** ao trabalho.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**5a.** As tarefas serão agendadas para a execução em nós.<br/>
    &nbsp;&nbsp;&nbsp;&nbsp;**5b.** Cada tarefa baixa seus dados de entrada do Armazenamento do Azure e então inicia a execução.<br/>
[**Etapa 6.**](#step-6-monitor-tasks) Monitore as tarefas.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**6a.** À medida que as tarefas são concluídas, elas carregam os dados de saída no Armazenamento do Azure.<br/>
[**Etapa 7.**](#step-7-download-task-output) Baixe a saída da tarefa do Armazenamento.

Como mencionado, nem todas as soluções do Lote executam essas etapas exatas e podem incluir muitas outras, mas o aplicativo de exemplo *DotNetTutorial* demonstra os processos comuns encontrados em uma solução do Lote.

## <a name="build-the-dotnettutorial-sample-project"></a>Criar o projeto de exemplo *DotNetTutorial*
Antes de poder executar o exemplo com êxito, você deverá especificar as credenciais de conta do Lote e do Armazenamento no arquivo `Program.cs` do projeto *DotNetTutorial*. Se você ainda não tiver feito isso, abra a solução no Visual Studio clicando duas vezes no arquivo de solução `DotNetTutorial.sln` . Ou abra-a no Visual Studio usando o menu **Arquivo > Abrir > Projeto/Solução**.

Abra `Program.cs` no projeto *DotNetTutorial* . Em seguida, adicione as credenciais como especificado próximo à parte superior do arquivo:

```csharp
// Update the Batch and Storage account credential strings below with the values
// unique to your accounts. These are used when constructing connection strings
// for the Batch and Storage client objects.

// Batch account credentials
private const string BatchAccountName = "";
private const string BatchAccountKey  = "";
private const string BatchAccountUrl  = "";

// Storage account credentials
private const string StorageAccountName = "";
private const string StorageAccountKey  = "";
```

> [!IMPORTANT]
> Como mencionado acima, no momento você deve especificar as credenciais para uma conta de armazenamento de **uso geral** no Armazenamento do Azure. Os aplicativos do Lote usa o armazenamento de blobs na conta de armazenamento de **uso geral**. Não especifique as credenciais de uma conta do Armazenamento criada por meio da seleção do tipo de conta do *armazenamento de Blobs* .
>
>

Você pode encontrar suas credenciais de conta do Lote e de Armazenamento na folha da conta de cada serviço no [Portal do Azure][azure_portal]:

![Credenciais de Lote no portal][9]
![Credenciais de armazenamento no portal][10]<br/>

Agora que você atualizou o projeto com suas credenciais, clique com botão direito do mouse na solução no Gerenciador de Soluções e clique em **Compilar Solução**. Confirme a restauração de qualquer pacote NuGet, se solicitado.

> [!TIP]
> Se os pacotes NuGet não forem automaticamente restaurados ou se você encontrar erros sobre uma falha ao restaurar os pacotes, verifique se você tem o [Gerenciador de Pacotes NuGet][nuget_packagemgr] instalado. Em seguida, habilite o download de pacotes ausentes. Confira [Habilitando a Restauração de Pacotes Durante o Build][nuget_restore] para habilitar o download de pacotes.
>
>

Nas seções a seguir, dividimos o aplicativo de exemplo nas etapas executadas para processar uma carga de trabalho no serviço Lote e discutimos essas etapas detalhadamente. Você deve fazer referência à solução aberta no Visual Studio enquanto estiver trabalhando até o restante deste artigo já que nem todas as linhas de código do exemplo serão discutidas.

Navegue até a parte superior do método `MainAsync` no arquivo `Program.cs` do projeto *DotNetTutorial* para começar pela Etapa 1. Cada etapa abaixo segue aproximadamente a progressão de chamadas de método em `MainAsync`.

## <a name="step-1-create-storage-containers"></a>Etapa 1: Criar contêineres do Armazenamento
![Criar contêineres no Armazenamento do Azure][1]
<br/>

O Lote inclui suporte interno para a interação com o Armazenamento do Azure. Os contêineres em sua conta do Armazenamento fornecerão os arquivos necessários às tarefas que serão executadas em sua conta do Lote. Os contêineres também fornecem um local para armazenar os dados de saída produzidos pelas tarefas. A primeira coisa que o aplicativo cliente *DotNetTutorial* faz é criar três contêineres no [Armazenamento de Blobs do Azure](../storage/common/storage-introduction.md):

* **application**: esse contêiner hospedará o aplicativo executado pelas tarefas, além de qualquer uma de suas dependências, como DLLs.
* **input**: as tarefas baixarão os arquivos de dados a serem processados do contêiner *input* .
* **output**: quando as tarefas concluírem o processamento dos arquivos de entrada, carregarão os resultados no contêiner *output* .

Para interagir com uma conta de Armazenamento e criar contêineres, usamos a [Biblioteca de Cliente do Armazenamento do Azure para .NET][net_api_storage]. Podemos criar uma referência à conta com [CloudStorageAccount][net_cloudstorageaccount] e dela criar um [CloudBlobClient][net_cloudblobclient]:

```csharp
// Construct the Storage account connection string
string storageConnectionString = String.Format(
    "DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}",
    StorageAccountName,
    StorageAccountKey);

// Retrieve the storage account
CloudStorageAccount storageAccount =
    CloudStorageAccount.Parse(storageConnectionString);

// Create the blob client, for use in obtaining references to
// blob storage containers
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
```

Usamos a referência `blobClient` em todo o aplicativo e o passamos como um parâmetro para diversos métodos. Um exemplo disso é o bloco de código logo após o trecho mostrado acima, onde chamamos `CreateContainerIfNotExistAsync` para realmente criarmos os contêineres.

```csharp
// Use the blob client to create the containers in Azure Storage if they don't
// yet exist
const string appContainerName    = "application";
const string inputContainerName  = "input";
const string outputContainerName = "output";
await CreateContainerIfNotExistAsync(blobClient, appContainerName);
await CreateContainerIfNotExistAsync(blobClient, inputContainerName);
await CreateContainerIfNotExistAsync(blobClient, outputContainerName);
```

```csharp
private static async Task CreateContainerIfNotExistAsync(
    CloudBlobClient blobClient,
    string containerName)
{
        CloudBlobContainer container =
            blobClient.GetContainerReference(containerName);

        if (await container.CreateIfNotExistsAsync())
        {
                Console.WriteLine("Container [{0}] created.", containerName);
        }
        else
        {
                Console.WriteLine("Container [{0}] exists, skipping creation.",
                    containerName);
        }
}
```

Depois que os contêineres tiverem sido criados, o aplicativo poderá carregar os arquivos que serão usados pelas tarefas.

> [!TIP]
> [Como usar o Armazenamento de Blobs do .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md) fornece uma boa visão geral de como trabalhar com blobs e contêineres do Armazenamento do Azure. Ele deverá estar próximo à parte superior da lista de leitura quando você começar a trabalhar com o Lote.
>
>

## <a name="step-2-upload-task-application-and-data-files"></a>Etapa 2: carregar aplicativos e de tarefa e arquivos de dados 
![Carregar arquivos de aplicativo e de entrada (dados) da tarefa nos contêineres][2]
<br/>

Na operação de upload do arquivo, *DotNetTutorial* primeiro define as coleções de caminhos de arquivo **application** e **input** como eles existem no computador local. Em seguida, ele carrega esses arquivos nos contêineres que você criou na etapa anterior.

```csharp
// Paths to the executable and its dependencies that will be executed by the tasks
List<string> applicationFilePaths = new List<string>
{
    // The DotNetTutorial project includes a project reference to TaskApplication,
    // allowing us to determine the path of the task application binary dynamically
    typeof(TaskApplication.Program).Assembly.Location,
    "Microsoft.WindowsAzure.Storage.dll"
};

// The collection of data files that are to be processed by the tasks
List<string> inputFilePaths = new List<string>
{
    @"..\..\taskdata1.txt",
    @"..\..\taskdata2.txt",
    @"..\..\taskdata3.txt"
};

// Upload the application and its dependencies to Azure Storage. This is the
// application that will process the data files, and will be executed by each
// of the tasks on the compute nodes.
List<ResourceFile> applicationFiles = await UploadFilesToContainerAsync(
    blobClient,
    appContainerName,
    applicationFilePaths);

// Upload the data files. This is the data that will be processed by each of
// the tasks that are executed on the compute nodes within the pool.
List<ResourceFile> inputFiles = await UploadFilesToContainerAsync(
    blobClient,
    inputContainerName,
    inputFilePaths);
```

Há dois métodos no `Program.cs` que estão envolvidos no processo de carregamento:

* `UploadFilesToContainerAsync`: esse método retorna uma coleção de objetos [ResourceFile][net_resourcefile] (discutidos abaixo) e internamente chama `UploadFileToContainerAsync` para carregar cada arquivo passado no parâmetro *filePaths*.
* `UploadFileToContainerAsync`: é o método que realmente executa o upload do arquivo e cria os objetos [ResourceFile][net_resourcefile]. Depois de carregar o arquivo, ele obtém uma assinatura de acesso compartilhado (SAS) para o arquivo e retorna um objeto ResourceFile que a representa. As assinaturas de acesso compartilhado também são discutidas abaixo.

```csharp
private static async Task<ResourceFile> UploadFileToContainerAsync(
    CloudBlobClient blobClient,
    string containerName,
    string filePath)
{
        Console.WriteLine(
            "Uploading file {0} to container [{1}]...", filePath, containerName);

        string blobName = Path.GetFileName(filePath);

        CloudBlobContainer container = blobClient.GetContainerReference(containerName);
        CloudBlockBlob blobData = container.GetBlockBlobReference(blobName);
        await blobData.UploadFromFileAsync(filePath);

        // Set the expiry time and permissions for the blob shared access signature.
        // In this case, no start time is specified, so the shared access signature
        // becomes valid immediately
        SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
        {
                SharedAccessExpiryTime = DateTime.UtcNow.AddHours(2),
                Permissions = SharedAccessBlobPermissions.Read
        };

        // Construct the SAS URL for blob
        string sasBlobToken = blobData.GetSharedAccessSignature(sasConstraints);
        string blobSasUri = String.Format("{0}{1}", blobData.Uri, sasBlobToken);

        return new ResourceFile(blobSasUri, blobName);
}
```

### <a name="resourcefiles"></a>ResourceFiles
O [ResourceFile][net_resourcefile] fornece tarefas no Lote com a URL para um arquivo no Armazenamento do Azure que é baixado para um nó de computação antes da execução da tarefa. A propriedade [ResourceFile.BlobSource][net_resourcefile_blobsource] especifica a URL completa do arquivo como ela existe no Armazenamento do Azure. A URL também pode incluir uma assinatura de acesso compartilhado (SAS) que fornece acesso seguro ao arquivo. A maioria dos tipos de tarefas no .NET do Lote inclui uma propriedade *ResourceFiles* , incluindo:

* [CloudTask][net_task]
* [StartTask][net_pool_starttask]
* [JobPreparationTask][net_jobpreptask]
* [JobReleaseTask][net_jobreltask]

O aplicativo de exemplo DotNetTutorial não usa os tipos de tarefa JobPreparationTask ou a JobReleaseTask, mas você pode ler mais sobre isso em [Executar tarefas de preparação e de conclusão de trabalhos em nós de computação do Lote do Azure](batch-job-prep-release.md).

### <a name="shared-access-signature-sas"></a>Assinatura de acesso compartilhado (SAS)
As assinaturas de acesso compartilhado são cadeias de caracteres que, quando incluídas como parte de uma URL, oferecem acesso seguro a contêineres e a blobs no Armazenamento do Azure. O aplicativo DotNetTutorial usa URLs de assinatura de acesso compartilhado de blob e de contêiner e demonstra como obter essas cadeias de caracteres de assinatura de acesso compartilhado do serviço de Armazenamento.

* **Assinaturas de acesso compartilhado do Blob**: a StartTask do pool em DotNetTutorial usa assinaturas de acesso compartilhado de blob ao baixar os binários do aplicativo e os dados de entrada de arquivos de Armazenamento (confira a Etapa 3 abaixo). O método `UploadFileToContainerAsync` no `Program.cs` do DotNetTutorial contém o código que obtém a assinatura de acesso compartilhado de cada blob. Isso é feito chamando [CloudBlob.GetSharedAccessSignature][net_sas_blob].
* **Assinaturas de acesso compartilhado do contêiner**: como cada tarefa conclui seu trabalho em nós de computação, ele carrega o arquivo de saída no contêiner *output* no Armazenamento do Azure. Para fazer isso, o TaskApplication usa uma assinatura de acesso compartilhado de contêiner que fornece acesso de gravação ao contêiner como parte do caminho ao carregar o arquivo. Você obtém a assinatura de acesso compartilhado do contêiner da mesma forma como obtém a assinatura de acesso compartilhado do blob. No DotNetTutorial, você verá que o método auxiliar `GetContainerSasUrl` chama [CloudBlobContainer.GetSharedAccessSignature][net_sas_container] para fazer isso. Leia mais sobre como o TaskApplication usa a assinatura de acesso compartilhado do contêiner na “Etapa 6: Monitorar tarefas".

> [!TIP]
> Confira a série de duas partes sobre as assinaturas de acesso compartilhado, [Parte 1: Compreendendo o modelo de assinatura de acesso compartilhado (SAS)](../storage/common/storage-dotnet-shared-access-signature-part-1.md) e [Parte 2: Criar e usar uma assinatura de acesso compartilhado (SAS) com o armazenamento de Blobs](../storage/blobs/storage-dotnet-shared-access-signature-part-2.md), para saber mais sobre como fornecer acesso seguro aos dados em sua conta do Armazenamento.
>
>

## <a name="step-3-create-batch-pool"></a>Etapa 3: Criar pool do Lote
![Criar um pool do Lote][3]
<br/>

Um **pool** do Lote é uma coleção de nós de computação (máquinas virtuais) nos quais o Lote executa as tarefas de um trabalho.

Depois de carregar o aplicativo e os arquivos de dados para a conta de armazenamento com APIs do Armazenamento do Azure, o *DotNetTutorial* começa a fazer chamadas para o serviço Lote com APIs fornecidas pela biblioteca .NET do Lote. O código cria primeiro um [BatchClient][net_batchclient]:

```csharp
BatchSharedKeyCredentials cred = new BatchSharedKeyCredentials(
    BatchAccountUrl,
    BatchAccountName,
    BatchAccountKey);

using (BatchClient batchClient = BatchClient.Open(cred))
{
    ...
```

Em seguida, o exemplo cria um pool de nós de computação na conta do Lote com uma chamada para `CreatePoolIfNotExistsAsync`. `CreatePoolIfNotExistsAsync` usa o método [BatchClient.PoolOperations.CreatePool][net_pool_create] para criar um novo pool no serviço Lote:

```csharp
private static async Task CreatePoolIfNotExistAsync(BatchClient batchClient, string poolId, IList<ResourceFile> resourceFiles)
{
    CloudPool pool = null;
    try
    {
        Console.WriteLine("Creating pool [{0}]...", poolId);

        // Create the unbound pool. Until we call CloudPool.Commit() or CommitAsync(), no pool is actually created in the
        // Batch service. This CloudPool instance is therefore considered "unbound," and we can modify its properties.
        pool = batchClient.PoolOperations.CreatePool(
            poolId: poolId,
            targetDedicatedComputeNodes: 3,                                             // 3 compute nodes
            virtualMachineSize: "small",                                                // single-core, 1.75 GB memory, 225 GB disk
            cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "4"));   // Windows Server 2012 R2

        // Create and assign the StartTask that will be executed when compute nodes join the pool.
        // In this case, we copy the StartTask's resource files (that will be automatically downloaded
        // to the node by the StartTask) into the shared directory that all tasks will have access to.
        pool.StartTask = new StartTask
        {
            // Specify a command line for the StartTask that copies the task application files to the
            // node's shared directory. Every compute node in a Batch pool is configured with a number
            // of pre-defined environment variables that can be referenced by commands or applications
            // run by tasks.

            // Since a successful execution of robocopy can return a non-zero exit code (e.g. 1 when one or
            // more files were successfully copied) we need to manually exit with a 0 for Batch to recognize
            // StartTask execution success.
            CommandLine = "cmd /c (robocopy %AZ_BATCH_TASK_WORKING_DIR% %AZ_BATCH_NODE_SHARED_DIR%) ^& IF %ERRORLEVEL% LEQ 1 exit 0",
            ResourceFiles = resourceFiles,
            WaitForSuccess = true
        };

        await pool.CommitAsync();
    }
    catch (BatchException be)
    {
        // Swallow the specific error code PoolExists since that is expected if the pool already exists
        if (be.RequestInformation?.BatchError != null && be.RequestInformation.BatchError.Code == BatchErrorCodeStrings.PoolExists)
        {
            Console.WriteLine("The pool {0} already existed when we tried to create it", poolId);
        }
        else
        {
            throw; // Any other exception is unexpected
        }
    }
}
```

Ao criar um pool com [CreatePool][net_pool_create], você especificará diversos parâmetros, como o número de nós de computação, o [tamanho dos nós](../cloud-services/cloud-services-sizes-specs.md) e o sistema operacional dos nós. Em *DotNetTutorial*, usamos [CloudServiceConfiguration][net_cloudserviceconfiguration] para especificar o Windows Server 2012 R2 do [Serviços de Nuvem](../cloud-services/cloud-services-guestos-update-matrix.md). 

Você também pode criar pools de nós de computação que são VMs (máquinas virtuais) do Azure especificando a [VirtualMachineConfiguration][net_virtualmachineconfiguration] para o pool. Você pode criar um conjunto de nós de computação de VM de [imagens Linux](batch-linux-nodes.md) ou Windows. A fonte para as imagens de VM pode ser:

- O [Marketplace de Máquinas Virtuais do Azure][vm_marketplace], que fornece imagens de Windows e Linux prontas para uso. 
- Uma imagem personalizada preparada e fornecida por você. Para obter mais detalhes sobre imagens personalizadas, confira [Desenvolver soluções de computação paralela em grande escala com o Lote](batch-api-basics.md#pool).

> [!IMPORTANT]
> Você é cobrado pelos recursos de computação no Lote. Para minimizar os custos, você poderá diminuir `targetDedicatedComputeNodes` para 1 antes de executar o exemplo.
>
>

Junto com essas propriedades de nó físico, você também poderá especificar uma [StartTask][net_pool_starttask] para o pool. StartTask é executado em cada nó quando o nó ingressa no pool e sempre que um nó é reiniciado. A StartTask é especialmente útil para instalar aplicativos em nós de computação antes da execução de tarefas. Por exemplo, se suas tarefas processassem dados usando scripts Python, você poderia usar uma StartTask para instalar o Python nos nós de computação.

Neste aplicativo de exemplo, a StartTask copia os arquivos baixados do Armazenamento (especificados usando a propriedade [StartTask][net_starttask].[ResourceFiles][net_starttask_resourcefiles]) do diretório de trabalho StartTask para o diretório compartilhado que *todas* as tarefas em execução no nó podem acessar. Essencialmente, isso copia `TaskApplication.exe` e suas dependências para um diretório compartilhado em cada nó à medida que o nó se une o pool para que qualquer tarefa executada no nó possa acessá-lo.

> [!TIP]
> O recurso **pacotes de aplicativos** do Lote do Azure fornece outra maneira de colocar seu aplicativo nos nós de computação em seu pool. Veja [Implantar aplicativos em nós de computação com pacotes de aplicativos do Lote](batch-application-packages.md) para obter detalhes.
>
>

Também podemos notar no trecho de código acima o uso de duas variáveis de ambiente na propriedade *CommandLine* da StartTask: `%AZ_BATCH_TASK_WORKING_DIR%` e `%AZ_BATCH_NODE_SHARED_DIR%`. Cada nó de computação em um pool do Lote é configurado automaticamente com diversas variáveis de ambiente específicas para o Lote. Qualquer processo executado por uma tarefa tem acesso a essas variáveis de ambiente.

> [!TIP]
> Para saber mais sobre as variáveis de ambiente disponíveis em nós de computação em um pool do Lote, além de informações sobre os diretórios de trabalho da tarefa, confira as seções [Configurações de ambiente para tarefas](batch-api-basics.md#environment-settings-for-tasks) e [Arquivos e diretórios](batch-api-basics.md#files-and-directories) na [Visão geral do recurso Lote para desenvolvedores](batch-api-basics.md).
>
>

## <a name="step-4-create-batch-job"></a>Etapa 4: Criar o trabalho do Lote
![Criar trabalho do Lote][4]<br/>

Um **trabalho** do Lote é uma coleção de tarefas associadas a um pool de nós de computação. As tarefas em um trabalho são executadas nos nós de computação do pool associado.

Você pode usar um trabalho não apenas para organizar e acompanhar tarefas relacionadas de cargas de trabalho, mas também pode impor certas restrições, como o tempo máximo de execução do trabalho (e, por extensão, de suas tarefas), além da prioridade do trabalho em relação a outros trabalhos na conta do Lote. No entanto, neste exemplo, o trabalho só estará associado ao pool criado na etapa 3. Nenhuma propriedade adicional será configurada.

Todos os trabalhos do Lotes estão associados a um pool específico. Essa associação indica em quais nós as tarefas do trabalho serão executadas. Especifique isso usando a propriedade [CloudJob.PoolInformation][net_job_poolinfo], como mostrado no trecho de código abaixo.

```csharp
private static async Task CreateJobAsync(
    BatchClient batchClient,
    string jobId,
    string poolId)
{
    Console.WriteLine("Creating job [{0}]...", jobId);

    CloudJob job = batchClient.JobOperations.CreateJob();
    job.Id = jobId;
    job.PoolInformation = new PoolInformation { PoolId = poolId };

    await job.CommitAsync();
}
```

Agora que um trabalho foi criado, as tarefas serão adicionadas para a execução do trabalho.

## <a name="step-5-add-tasks-to-job"></a>Etapa 5: Adicionar tarefas ao trabalho
![Adicionar tarefas ao trabalho][5]<br/>
*(1) As tarefas são adicionadas ao trabalho, (2) as tarefas são agendadas para execução em nós e (3) as tarefas baixam os arquivos de dados para processamento*

As **tarefas** do Lote são as unidades individuais de trabalho executadas nos nós de computação. Uma tarefa tem uma linha de comando e executa os scripts ou os executáveis especificados na linha de comando.

Para realmente executar o trabalho, as tarefas devem ser adicionadas a um trabalho. Cada [CloudTask][net_task] é configurada usando uma propriedade de linha de comando e [ResourceFiles][net_task_resourcefiles] (assim como acontece com a StartTask do pool) que a tarefa baixa para o nó antes de a linha de comando ser executada automaticamente. No projeto de exemplo *DotNetTutorial* , cada tarefa processa apenas um arquivo. Portanto, sua coleção ResourceFiles contém um único elemento.

```csharp
private static async Task<List<CloudTask>> AddTasksAsync(
    BatchClient batchClient,
    string jobId,
    List<ResourceFile> inputFiles,
    string outputContainerSasUrl)
{
    Console.WriteLine("Adding {0} tasks to job [{1}]...", inputFiles.Count, jobId);

    // Create a collection to hold the tasks that we'll be adding to the job
    List<CloudTask> tasks = new List<CloudTask>();

    // Create each of the tasks. Because we copied the task application to the
    // node's shared directory with the pool's StartTask, we can access it via
    // the shared directory on the node that the task runs on.
    foreach (ResourceFile inputFile in inputFiles)
    {
        string taskId = "topNtask" + inputFiles.IndexOf(inputFile);
        string taskCommandLine = String.Format(
            "cmd /c %AZ_BATCH_NODE_SHARED_DIR%\\TaskApplication.exe {0} 3 \"{1}\"",
            inputFile.FilePath,
            outputContainerSasUrl);

        CloudTask task = new CloudTask(taskId, taskCommandLine);
        task.ResourceFiles = new List<ResourceFile> { inputFile };
        tasks.Add(task);
    }

    // Add the tasks as a collection, as opposed to issuing a separate AddTask call
    // for each. Bulk task submission helps to ensure efficient underlying API calls
    // to the Batch service.
    await batchClient.JobOperations.AddTaskAsync(jobId, tasks);

    return tasks;
}
```

> [!IMPORTANT]
> Quando acessarem variáveis de ambiente como `%AZ_BATCH_NODE_SHARED_DIR%` ou executam um aplicativo não encontrado no `PATH` do nó, as linhas de comando da tarefa deverão ser prefixadas com `cmd /c`. Isso executará explicitamente o interpretador de comandos e o instruirá a terminar após a execução do comando. Esse requisito será desnecessário se suas tarefas executarem um aplicativo no `PATH` do nó (como *robocopy.exe* ou *powershell.exe*) e se nenhuma variável de ambiente for usada.
>
>

No loop `foreach` no trecho de código acima, você verá que a linha de comando da tarefa é construída de forma que três argumentos de linha de comando sejam passados para *TaskApplication.exe*:

1. O **primeiro argumento** é o caminho do arquivo a ser processado. Esse é o caminho local para o arquivo conforme ele existe no nó. Quando o objeto ResourceFile em `UploadFileToContainerAsync` foi criado acima, o nome do arquivo foi usado para essa propriedade (como um parâmetro para o construtor ResourceFile). Isso indica que o arquivo pode ser encontrado no mesmo diretório que *TaskApplication.exe*.
2. O **segundo argumento** especifica que as *N* palavras principais devem ser gravadas no arquivo de saída. No exemplo, isso é codificado para que as três palavras principais sejam gravadas no arquivo de saída.
3. O **terceiro argumento** é a assinatura de acesso compartilhado (SAS) que fornece acesso de gravação ao contêiner **output** no Armazenamento do Azure. *TaskApplication.exe* usa essa URL de assinatura de acesso compartilhado quando carrega o arquivo de saída no Armazenamento do Azure. Você pode encontrar o código para isso no método `UploadFileToContainer` do arquivo `Program.cs` do projeto TaskApplication:

```csharp
// NOTE: From project TaskApplication Program.cs

private static void UploadFileToContainer(string filePath, string containerSas)
{
        string blobName = Path.GetFileName(filePath);

        // Obtain a reference to the container using the SAS URI.
        CloudBlobContainer container = new CloudBlobContainer(new Uri(containerSas));

        // Upload the file (as a new blob) to the container
        try
        {
                CloudBlockBlob blob = container.GetBlockBlobReference(blobName);
                blob.UploadFromFile(filePath);

                Console.WriteLine("Write operation succeeded for SAS URL " + containerSas);
                Console.WriteLine();
        }
        catch (StorageException e)
        {

                Console.WriteLine("Write operation failed for SAS URL " + containerSas);
                Console.WriteLine("Additional error information: " + e.Message);
                Console.WriteLine();

                // Indicate that a failure has occurred so that when the Batch service
                // sets the CloudTask.ExecutionInformation.ExitCode for the task that
                // executed this application, it properly indicates that there was a
                // problem with the task.
                Environment.ExitCode = -1;
        }
}
```

## <a name="step-6-monitor-tasks"></a>Etapa 6: Monitorar tarefas
![Monitorar tarefas][6]<br/>
*O aplicativo cliente (1) monitora as tarefas para a conclusão e o status de êxito e (2) os dados resultantes do upload de tarefas para o Armazenamento do Azure*

Quando as tarefas são adicionadas a um trabalho, são automaticamente enfileiradas e agendadas para execução em nós de computação no pool associado ao trabalho. Com base nas configurações especificadas, o Lote manipula o enfileiramento, o agendamento, a repetição de todas as tarefas e outras obrigações de administração de tarefas para você.

Há muitas abordagens para o monitoramento da execução da tarefa. O DotNetTutorial mostra um exemplo simples que relata apenas a conclusão e a falha de tarefas ou os estados de êxito. No método `MonitorTasks` no `Program.cs` do DotNetTutorial, há três conceitos do .NET do Lote que garantem a discussão. Eles são listados na ordem em que aparecem:

1. **ODATADetailLevel**: a especificação de um [ODATADetailLevel][net_odatadetaillevel] na lista de operações (como a obtenção de uma lista de tarefas do trabalho) é essencial para garantir o desempenho do aplicativo do Lote. Adicione [Consultar o serviço Lote do Azure com eficiência](batch-efficient-list-queries.md) à sua lista de leitura se você planejar fazer qualquer tipo de monitoramento de status em seus aplicativos do Lote.
2. **TaskStateMonitor**: [TaskStateMonitor][net_taskstatemonitor] fornece aplicativos .NET do Lote com utilitários auxiliares para monitorar estados da tarefa. Em `MonitorTasks`, *DotNetTutorial* aguarda que todas as tarefas alcancem [TaskState.Completed][net_taskstate] dentro de um limite de tempo. Em seguida, ele conclui o trabalho.
3. **TerminateJobAsync**: o encerramento de um trabalho com [JobOperations.TerminateJobAsync][net_joboperations_terminatejob] (ou o bloqueio de JobOperations.TerminateJob) marca esse trabalho como concluído. Será essencial fazer isso se sua solução do Lote usar um [JobReleaseTask][net_jobreltask]. Esse é um tipo especial de tarefa, descrita em [Tarefas de preparação e de conclusão do trabalho](batch-job-prep-release.md).

O método `MonitorTasks` no `Program.cs` do *DotNetTutorial* aparece abaixo:

```csharp
private static async Task<bool> MonitorTasks(
    BatchClient batchClient,
    string jobId,
    TimeSpan timeout)
{
    bool allTasksSuccessful = true;
    const string successMessage = "All tasks reached state Completed.";
    const string failureMessage = "One or more tasks failed to reach the Completed state within the timeout period.";

    // Obtain the collection of tasks currently managed by the job. Note that we use
    // a detail level to  specify that only the "id" property of each task should be
    // populated. Using a detail level for all list operations helps to lower
    // response time from the Batch service.
    ODATADetailLevel detail = new ODATADetailLevel(selectClause: "id");
    List<CloudTask> tasks =
        await batchClient.JobOperations.ListTasks(JobId, detail).ToListAsync();

    Console.WriteLine("Awaiting task completion, timeout in {0}...",
        timeout.ToString());

    // We use a TaskStateMonitor to monitor the state of our tasks. In this case, we
    // will wait for all tasks to reach the Completed state.
    TaskStateMonitor taskStateMonitor
        = batchClient.Utilities.CreateTaskStateMonitor();

    try
    {
        await taskStateMonitor.WhenAll(tasks, TaskState.Completed, timeout);
    }
    catch (TimeoutException)
    {
        await batchClient.JobOperations.TerminateJobAsync(jobId, failureMessage);
        Console.WriteLine(failureMessage);
        return false;
    }

    await batchClient.JobOperations.TerminateJobAsync(jobId, successMessage);

    // All tasks have reached the "Completed" state, however, this does not
    // guarantee all tasks completed successfully. Here we further check each task's
    // ExecutionInfo property to ensure that it did not encounter a failure
    // or return a non-zero exit code.

    // Update the detail level to populate only the task id and executionInfo
    // properties. We refresh the tasks below, and need only this information for
    // each task.
    detail.SelectClause = "id, executionInfo";

    foreach (CloudTask task in tasks)
    {
        // Populate the task's properties with the latest info from the Batch service
        await task.RefreshAsync(detail);

        if (task.ExecutionInformation.Result == TaskExecutionResult.Failure)
        {
            // A task with failure information set indicates there was a problem with the task. It is important to note that
            // the task's state can be "Completed," yet still have encountered a failure.

            allTasksSuccessful = false;

            Console.WriteLine("WARNING: Task [{0}] encountered a failure: {1}", task.Id, task.ExecutionInformation.FailureInformation.Message);
            if (task.ExecutionInformation.ExitCode != 0)
            {
                // A non-zero exit code may indicate that the application executed by the task encountered an error
                // during execution. As not every application returns non-zero on failure by default (e.g. robocopy),
                // your implementation of error checking may differ from this example.

                Console.WriteLine("WARNING: Task [{0}] returned a non-zero exit code - this may indicate task execution or completion failure.", task.Id);
            }
        }
    }

    if (allTasksSuccessful)
    {
        Console.WriteLine("Success! All tasks completed successfully within the specified timeout period.");
    }

    return allTasksSuccessful;
}
```

## <a name="step-7-download-task-output"></a>Etapa 7: Baixar a saída da tarefa
![Baixar a saída da tarefa do Armazenamento][7]<br/>

Agora que o trabalho foi concluído, a saída das tarefas pode ser baixada do Armazenamento do Azure. Isso é feito com uma chamada a `DownloadBlobsFromContainerAsync` no `Program.cs` do *DotNetTutorial*:

```csharp
private static async Task DownloadBlobsFromContainerAsync(
    CloudBlobClient blobClient,
    string containerName,
    string directoryPath)
{
        Console.WriteLine("Downloading all files from container [{0}]...", containerName);

        // Retrieve a reference to a previously created container
        CloudBlobContainer container = blobClient.GetContainerReference(containerName);

        // Get a flat listing of all the block blobs in the specified container
        foreach (IListBlobItem item in container.ListBlobs(
                    prefix: null,
                    useFlatBlobListing: true))
        {
                // Retrieve reference to the current blob
                CloudBlob blob = (CloudBlob)item;

                // Save blob contents to a file in the specified folder
                string localOutputFile = Path.Combine(directoryPath, blob.Name);
                await blob.DownloadToFileAsync(localOutputFile, FileMode.Create);
        }

        Console.WriteLine("All files downloaded to {0}", directoryPath);
}
```

> [!NOTE]
> A chamada a `DownloadBlobsFromContainerAsync` no aplicativo *DotNetTutorial* especifica que os arquivos devem ser baixados para a pasta `%TEMP%`. Fique à vontade para modificar esse local de saída.
>
>

## <a name="step-8-delete-containers"></a>Etapa 8: Excluir contêineres
Como você é cobrado pelos dados que residem no Armazenamento do Azure, sempre será uma boa ideia remover os blobs que não sejam mais necessários para seus trabalhos em lotes. No `Program.cs` do DotNetTutorial, isso é feito com três chamadas para o método auxiliar `DeleteContainerAsync`:

```csharp
// Clean up Storage resources
await DeleteContainerAsync(blobClient, appContainerName);
await DeleteContainerAsync(blobClient, inputContainerName);
await DeleteContainerAsync(blobClient, outputContainerName);
```

O próprio método simplesmente obtém uma referência para o contêiner e, em seguida, chama [CloudBlobContainer.DeleteIfExistsAsync][net_container_delete]:

```csharp
private static async Task DeleteContainerAsync(
    CloudBlobClient blobClient,
    string containerName)
{
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);

    if (await container.DeleteIfExistsAsync())
    {
        Console.WriteLine("Container [{0}] deleted.", containerName);
    }
    else
    {
        Console.WriteLine("Container [{0}] does not exist, skipping deletion.",
            containerName);
    }
}
```

## <a name="step-9-delete-the-job-and-the-pool"></a>Etapa 9: excluir o trabalho e o pool
Na etapa final, é solicitado que você exclua o trabalho e o pool criados pelo aplicativo DotNetTutorial. Embora você não seja cobrado pelos trabalhos e pelas tarefas, *será* cobrado pelos nós de computação. Portanto, recomendamos que você aloque os nós conforme necessário. A exclusão de pools não utilizados pode fazer parte de seu processo de manutenção.

As [JobOperations][net_joboperations] e as [PoolOperations][net_pooloperations] do BatchClient têm métodos de exclusão correspondentes, chamados se o usuário confirmar a exclusão:

```csharp
// Clean up the resources we've created in the Batch account if the user so chooses
Console.WriteLine();
Console.WriteLine("Delete job? [yes] no");
string response = Console.ReadLine().ToLower();
if (response != "n" && response != "no")
{
    await batchClient.JobOperations.DeleteJobAsync(JobId);
}

Console.WriteLine("Delete pool? [yes] no");
response = Console.ReadLine();
if (response != "n" && response != "no")
{
    await batchClient.PoolOperations.DeletePoolAsync(PoolId);
}
```

> [!IMPORTANT]
> Tenha em mente que você será cobrado pelos recursos de computação, a exclusão dos pools não utilizados reduzirá o custo. Além disso, lembre-se de que a exclusão de um pool exclui todos os nós de computação no pool e que os dados em nós não poderão ser recuperados depois que o pool for excluído.
>
>

## <a name="run-the-dotnettutorial-sample"></a>Executar o exemplo *DotNetTutorial*
Quando você executar o aplicativo de exemplo, a saída do console será semelhante à seguinte. Durante a execução, você terá uma pausa em `Awaiting task completion, timeout in 00:30:00...` enquanto os nós de computação do pool são iniciados. Use o [Portal do Azure][azure_portal] para monitorar o pool, os nós de computação, o trabalho e as tarefas durante e após a execução. Use o [Portal do Azure][azure_portal] ou o [Gerenciador de Armazenamento do Azure][storage_explorers] para exibir os recursos do Armazenamento (contêineres e blobs) criados pelo aplicativo.

O tempo de execução típico é de **aproximadamente 5 minutos** ao executar o aplicativo em sua configuração padrão.

```
Sample start: 1/8/2016 09:42:58 AM

Container [application] created.
Container [input] created.
Container [output] created.
Uploading file C:\repos\azure-batch-samples\CSharp\ArticleProjects\DotNetTutorial\bin\Debug\TaskApplication.exe to container [application]...
Uploading file Microsoft.WindowsAzure.Storage.dll to container [application]...
Uploading file ..\..\taskdata1.txt to container [input]...
Uploading file ..\..\taskdata2.txt to container [input]...
Uploading file ..\..\taskdata3.txt to container [input]...
Creating pool [DotNetTutorialPool]...
Creating job [DotNetTutorialJob]...
Adding 3 tasks to job [DotNetTutorialJob]...
Awaiting task completion, timeout in 00:30:00...
Success! All tasks completed successfully within the specified timeout period.
Downloading all files from container [output]...
All files downloaded to C:\Users\USERNAME\AppData\Local\Temp
Container [application] deleted.
Container [input] deleted.
Container [output] deleted.

Sample end: 1/8/2016 09:47:47 AM
Elapsed time: 00:04:48.5358142

Delete job? [yes] no: yes
Delete pool? [yes] no: yes

Sample complete, hit ENTER to exit...
```

## <a name="next-steps"></a>Próximas etapas
Fique à vontade para fazer alterações em *DotNetTutorial* e em *TaskApplication* para fazer experiências com cenários de computação diferentes. Por exemplo, tente adicionar um atraso de execução para *TaskApplication*, como com [Thread.Sleep][net_thread_sleep], para simular tarefas demoradas e para monitorá-las no portal. Tente adicionar mais tarefas ou ajustar o número de nós de computação. Adicione lógica para verificar e permitir o uso de um pool existente para acelerar o tempo de execução (*dica*: confira `ArticleHelpers.cs` no projeto [Microsoft.Azure.Batch.Samples.Common][github_samples_common] em [azure-batch-samples][github_samples]).

Agora que você está familiarizado com o fluxo de trabalho básico de uma solução do Lote, é hora de se aprofundar nos recursos adicionais do serviço Lote.

* Examine o artigo [Visão geral dos recursos do Lote do Azure](batch-api-basics.md) , que é recomendável se ainda não estiver familiarizado com o serviço.
* Comece pelos outros artigos de desenvolvimento do Lote em **Desenvolvimento detalhado** no [Roteiro de aprendizagem do Lote][batch_learning_path].
* Confira uma implementação diferente do processamento da carga de trabalho "N palavras principais" usando o Lote no exemplo [TopNWords][github_topnwords].
* Confira as [notas de versão](https://github.com/Azure/azure-sdk-for-net/blob/psSdkJson6/src/SDKs/Batch/DataPlane/changelog.md#azurebatch-release-notes) do Batch .NET para conhecer as alterações mais recentes na biblioteca.

[azure_batch]: https://azure.microsoft.com/services/batch/
[azure_free_account]: https://azure.microsoft.com/free/
[azure_portal]: https://portal.azure.com
[batch_learning_path]: https://azure.microsoft.com/documentation/learning-paths/batch/
[github_batchexplorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[github_dotnettutorial]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/DotNetTutorial
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_common]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/Common
[github_samples_zip]: https://github.com/Azure/azure-batch-samples/archive/master.zip
[github_topnwords]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/TopNWords
[net_api]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[net_api_storage]: https://msdn.microsoft.com/library/azure/mt347887.aspx
[net_batchclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudblobclient]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobclient.aspx
[net_cloudblobcontainer]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.aspx
[net_cloudstorageaccount]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.cloudstorageaccount.aspx
[net_cloudserviceconfiguration]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudserviceconfiguration.aspx
[net_container_delete]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.deleteifexistsasync.aspx
[net_job]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_job_poolinfo]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.protocol.models.cloudjob.poolinformation.aspx
[net_joboperations]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.joboperations
[net_joboperations_terminatejob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.terminatejobasync.aspx
[net_jobpreptask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobpreparationtask.aspx
[net_jobreltask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobreleasetask.aspx
[net_node]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.aspx
[net_odatadetaillevel]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.aspx
[net_pool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_pool_create]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx
[net_pool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx
[net_pooloperations]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.pooloperations
[net_resourcefile]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.resourcefile.aspx
[net_resourcefile_blobsource]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.resourcefile.blobsource.aspx
[net_sas_blob]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblob.getsharedaccesssignature.aspx
[net_sas_container]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblobcontainer.getsharedaccesssignature.aspx
[net_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.starttask.aspx
[net_starttask_resourcefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.starttask.resourcefiles.aspx
[net_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_task_resourcefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.resourcefiles.aspx
[net_taskstate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.common.taskstate.aspx
[net_taskstatemonitor]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskstatemonitor.aspx
[net_thread_sleep]: https://msdn.microsoft.com/library/274eh01d(v=vs.110).aspx
[net_virtualmachineconfiguration]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.virtualmachineconfiguration.aspx
[nuget_packagemgr]: https://docs.nuget.org/consume/installing-nuget
[nuget_restore]: https://docs.nuget.org/consume/package-restore/msbuild-integrated#enabling-package-restore-during-build
[storage_explorers]: http://storageexplorer.com/
[visual_studio]: https://www.visualstudio.com/vs/
[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/

[1]: ./media/batch-dotnet-get-started/batch_workflow_01_sm.png "Criar contêineres no Armazenamento do Azure"
[2]: ./media/batch-dotnet-get-started/batch_workflow_02_sm.png "Carregar arquivos de aplicativo e de entrada (dados) da tarefa nos contêineres"
[3]: ./media/batch-dotnet-get-started/batch_workflow_03_sm.png "Criar pool do Lote"
[4]: ./media/batch-dotnet-get-started/batch_workflow_04_sm.png "Criar trabalho em lotes"
[5]: ./media/batch-dotnet-get-started/batch_workflow_05_sm.png "Adicionar tarefas ao trabalho"
[6]: ./media/batch-dotnet-get-started/batch_workflow_06_sm.png "Monitorar tarefas"
[7]: ./media/batch-dotnet-get-started/batch_workflow_07_sm.png "Baixar a saída da tarefa do Armazenamento"
[8]: ./media/batch-dotnet-get-started/batch_workflow_sm.png "Fluxo de trabalho da solução do Lote (diagrama completo)"
[9]: ./media/batch-dotnet-get-started/credentials_batch_sm.png "Credenciais do Lote no Portal"
[10]: ./media/batch-dotnet-get-started/credentials_storage_sm.png "Credenciais do Armazenamento no Portal"
[11]: ./media/batch-dotnet-get-started/batch_workflow_minimal_sm.png "Fluxo de trabalho da solução do Lote (diagrama mínimo)"
