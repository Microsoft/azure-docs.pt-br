---
title: Persistir a saída de tarefa e de trabalho no Armazenamento do Azure com a API do serviço de Lote do Azure - Lote do Azure | Microsoft Docs
description: Saiba como usar a API do serviço de lote para manter a saída de tarefa e o trabalho em lotes no Armazenamento do Azure.
services: batch
author: laurenhughes
manager: gwallace
editor: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 03/05/2019
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: c113521a1828a27ce9454ea142cb1708b7c5a1b6
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68322835"
---
# <a name="persist-task-data-to-azure-storage-with-the-batch-service-api"></a>Manter os dados de tarefa para o Armazenamento do Azure com a API de serviço de lote

[!INCLUDE [batch-task-output-include](../../includes/batch-task-output-include.md)]

A API de serviço do Lote fornece suporte para manter dados de saída para o Armazenamento do Microsoft Azure para tarefas e tarefas do gerenciador de trabalho, que são executadas em pools com a configuração de máquina virtual. Quando você adiciona uma tarefa, pode especificar um contêiner no Armazenamento do Azure como o destino para a saída da tarefa. O serviço de lote grava os dados de saída nesse contêiner quando a tarefa é concluída.

Uma vantagem de usar a API do serviço de lote para manter a saída da tarefa é que você não precisa modificar o aplicativo que a tarefa está sendo executada. Em vez disso, com algumas modificações no seu aplicativo cliente, você pode manter a saída da tarefa de dentro do mesmo código que cria a tarefa.

## <a name="when-do-i-use-the-batch-service-api-to-persist-task-output"></a>Quando uso a API do serviço de Lote para manter a saída da tarefa?

O Lote do Azure fornece mais de uma maneira de manter a saída da tarefa. Usar a API de serviço de lote é uma abordagem conveniente que é mais adequada para esses cenários:

- Você deseja escrever o código para manter a saída da tarefa de dentro de seu aplicativo cliente, sem modificar o aplicativo que sua tarefa está executando.
- Você deseja manter a saída das tarefas em Lote e as tarefas do gerenciador de trabalho em pools criados com a configuração de máquina virtual.
- Você deseja manter a saída a um contêiner de Armazenamento do Azure com um nome arbitrário.
- Você deseja manter a saída a um contêiner de Armazenamento do Azure nomeado de acordo com o [padrão de Convenções de Arquivo em Lotes](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/Batch/Support/FileConventions#conventions). 

Se seu cenário for diferente daqueles listados acima, poderá ser necessário considerar uma abordagem diferente. Por exemplo, a API de serviço de lote atualmente não suporta saída de transmissão para o armazenamento do Azure durante a execução da tarefa. Para a saída de fluxo, considere usar a biblioteca de Convenções de Arquivo em Lotes, disponível para .NET. Para outros idiomas, você precisará implantar sua própria solução. Para obter mais informações sobre outras opções para manter a saída da tarefa, consulte [Manter saída de trabalho e tarefa no Armazenamento do Azure](batch-task-output.md).

## <a name="create-a-container-in-azure-storage"></a>Criar um contêiner no Armazenamento do Azure

Para manter a saída da tarefa no Armazenamento do Azure, você precisará criar um contêiner que serve como o destino para seus arquivos de saída. Crie o contêiner antes de executar a tarefa, preferencialmente antes de enviar seu trabalho. Para criar o contêiner, use a biblioteca de cliente de Armazenamento do Azure apropriada ou o SDK. Para obter mais informações sobre APIs de Armazenamento do Azure, consulte a [documentação do Armazenamento do Azure](https://docs.microsoft.com/azure/storage/).

Por exemplo, se você estiver escrevendo seu aplicativo em C#, use a [biblioteca de cliente de Armazenamento do Azure para .NET](https://www.nuget.org/packages/WindowsAzure.Storage/). O exemplo a seguir mostra como criar um contêiner:

```csharp
CloudBlobContainer container = storageAccount.CreateCloudBlobClient().GetContainerReference(containerName);
await container.CreateIfNotExists();
```

## <a name="get-a-shared-access-signature-for-the-container"></a>Obtenha uma assinatura de acesso compartilhado para o contêiner

Depois de criar o contêiner, obtenha uma assinatura de acesso compartilhado (SAS) com acesso de gravação ao contêiner. Uma SAS fornece acesso delegado para o contêiner. A SAS concede acesso com um conjunto especificado de permissões e em um intervalo de tempo especificado. O serviço de lote precisa de uma SAS com permissões de gravação para gravar a saída da tarefa no contêiner. Para saber mais sobre SAS, confira [Uso de assinaturas de acesso compartilhado \(SAS\) no Armazenamento do Azure](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

Quando você receber uma SAS usando as APIs de Armazenamento do Azure, a API retorna uma cadeia de caracteres de token SAS. Essa cadeia de caracteres de token inclui todos os parâmetros de SAS, incluindo as permissões e o intervalo no qual a SAS é válida. Para usar a SAS para acessar um contêiner no Armazenamento do Azure, você precisa anexar a cadeia de caracteres de token SAS para o URI do recurso. O URI de recurso, junto com o token SAS anexado, fornece acesso autenticado ao Armazenamento do Azure.

O exemplo a seguir mostra como obter uma cadeia de token de SAS somente gravação para o contêiner e anexa a SAS para o URI do contêiner:

```csharp
string containerSasToken = container.GetSharedAccessSignature(new SharedAccessBlobPolicy()
{
    SharedAccessExpiryTime = DateTimeOffset.UtcNow.AddDays(1),
    Permissions = SharedAccessBlobPermissions.Write
});

string containerSasUrl = container.Uri.AbsoluteUri + containerSasToken;
```

## <a name="specify-output-files-for-task-output"></a>Especificar arquivos de saída para a saída da tarefa

Para especificar os arquivos de saída para uma tarefa, crie uma coleção de objetos [OutputFile](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfile) e a atribua para a propriedade de [CloudTask.OutputFiles](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudtask.outputfiles#Microsoft_Azure_Batch_CloudTask_OutputFiles) ao criar a tarefa.

O exemplo de código C# a seguir cria uma tarefa que grava números aleatórios para um arquivo chamado `output.txt`. O exemplo cria um arquivo de saída para `output.txt` a ser gravado para o contêiner. O exemplo também cria arquivos de saída para os arquivos de log que correspondem ao padrão de arquivo `std*.txt` ( _, por exemplo,_ , `stdout.txt` e `stderr.txt`). A URL do contêiner requer o SAS que foi criado anteriormente para o contêiner. O serviço de lote usa as SAS para autenticar o acesso ao contêiner:

```csharp
new CloudTask(taskId, "cmd /v:ON /c \"echo off && set && (FOR /L %i IN (1,1,100000) DO (ECHO !RANDOM!)) > output.txt\"")
{
    OutputFiles = new List<OutputFile>
    {
        new OutputFile(
            filePattern: @"..\std*.txt",
            destination: new OutputFileDestination(
         new OutputFileBlobContainerDestination(
                    containerUrl: containerSasUrl,
                    path: taskId)),
            uploadOptions: new OutputFileUploadOptions(
            uploadCondition: OutputFileUploadCondition.TaskCompletion)),
        new OutputFile(
            filePattern: @"output.txt",
            destination: 
         new OutputFileDestination(new OutputFileBlobContainerDestination(
                    containerUrl: containerSasUrl,
                    path: taskId + @"\output.txt")),
            uploadOptions: new OutputFileUploadOptions(
            uploadCondition: OutputFileUploadCondition.TaskCompletion)),
}
```

### <a name="specify-a-file-pattern-for-matching"></a>Especifique um padrão de arquivo para correspondência

Quando você especifica um arquivo de saída, você pode usar a propriedade [OutputFile.FilePattern](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfile.filepattern#Microsoft_Azure_Batch_OutputFile_FilePattern) para especificar um padrão de arquivo para correspondência. O padrão de arquivo pode corresponder a zero arquivos, um único arquivo ou um conjunto de arquivos que são criados pela tarefa.

A propriedade **FilePattern** oferece suporte a caracteres curinga do sistema de arquivos padrão como `*` (para não-recursivo corresponde) e `**` (para corresponde recursivo). Por exemplo, o exemplo de código acima especifica o padrão de arquivo para corresponder `std*.txt` não recursiva:

`filePattern: @"..\std*.txt"`

Para carregar um único arquivo, especifique um padrão de arquivo sem curingas. Por exemplo, o exemplo de código acima especifica o padrão de arquivo para corresponder `output.txt`:

`filePattern: @"output.txt"`

### <a name="specify-an-upload-condition"></a>Especificar uma condição de upload

A propriedade [OutputFileUploadOptions.UploadCondition](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfileuploadoptions.uploadcondition#Microsoft_Azure_Batch_OutputFileUploadOptions_UploadCondition) permite o upload condicional de arquivos de saída. Um cenário comum é carregar um conjunto de arquivos se a tarefa for bem-sucedida, e um conjunto diferente de arquivos se ele falhar. Por exemplo, você talvez queira carregar arquivos de log detalhados somente quando a tarefa falhar e for encerrada com um código de saída diferente de zero. Da mesma forma, você talvez queira carregar arquivos de resultado somente se a tarefa for bem-sucedida, pois esses arquivos podem estar ausentes ou incompletos se a tarefa falhar.

O exemplo de código acima define a propriedade **UploadCondition** para **TaskCompletion**. Essa configuração especifica que o arquivo será carregado após a conclusão de tarefas, independentemente do valor do código de saída.

`uploadCondition: OutputFileUploadCondition.TaskCompletion`

Para outras configurações, consulte o enum [OutputFileUploadCondition](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.common.outputfileuploadcondition).

### <a name="disambiguate-files-with-the-same-name"></a>Desfazer a ambiguidade de arquivos com o mesmo nome

As tarefas em um trabalho podem produzir os arquivos que têm o mesmo nome. Por exemplo, `stdout.txt` e `stderr.txt` são criados para cada tarefa que executa em um trabalho. Como cada tarefa é executada em seu próprio contexto, esses arquivos não estão em conflito no sistema de arquivos do nó. No entanto, quando você carrega arquivos de várias tarefas para um contêiner compartilhado, você precisará desfazer a ambiguidade de arquivos com o mesmo nome.

A propriedade [OutputFileBlobContainerDestination.Path](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfileblobcontainerdestination.path#Microsoft_Azure_Batch_OutputFileBlobContainerDestination_Path) especifica o blob de destino ou o diretório virtual para arquivos de saída. Você pode usar a propriedade de **caminho** para nomear o blob ou diretório virtual de forma que os arquivos de saída com o mesmo nome são nomeados exclusivamente no Armazenamento do Azure. Usando a ID da tarefa no caminho é uma boa maneira de garantir nomes exclusivos e identificar facilmente os arquivos.

Se a propriedade **FilePattern** é definida como uma expressão curinga, em seguida, todos os arquivos que correspondem ao padrão são carregados para o diretório virtual especificado pela propriedade **caminho**. Por exemplo, se o contêiner for `mycontainer`, a tarefa de ID é `mytask`, e o padrão de arquivo é `..\std*.txt`, em seguida, os URIs absolutos para arquivos de saída no Armazenamento do Azure serão semelhantes a:

```
https://myaccount.blob.core.windows.net/mycontainer/mytask/stderr.txt
https://myaccount.blob.core.windows.net/mycontainer/mytask/stdout.txt
```

Se a propriedade **FilePattern** está definida para corresponder a um nome de arquivo único, que significa que ele não contém caracteres curinga, o valor da propriedade **caminho** especifica o nome totalmente qualificado do blob. Se você antecipar conflitos de nomes com um único arquivo de várias tarefas, em seguida, inclua o nome do diretório virtual como parte do nome do arquivo para resolver a ambiguidade desses arquivos. Por exemplo, definir a propriedade **caminho** para incluir a ID da tarefa, o caractere delimitador (normalmente uma barra invertida) e o nome do arquivo:

`path: taskId + @"/output.txt"`

O URIs absolutos para arquivos de saída para um conjunto de tarefas será semelhante a:

```
https://myaccount.blob.core.windows.net/mycontainer/task1/output.txt
https://myaccount.blob.core.windows.net/mycontainer/task2/output.txt
```

Para obter mais informações sobre diretórios virtuais no Armazenamento do Azure, consulte [Listar os blobs em um contêiner](../storage/blobs/storage-quickstart-blobs-dotnet.md#list-the-blobs-in-a-container).

## <a name="diagnose-file-upload-errors"></a>Diagnosticar erros de carregamento de arquivo

Se o carregamento de arquivos de saída no Armazenamento do Azure falhar, então a tarefa será movida para o estado **concluído** e a propriedade [TaskExecutionInformation.FailureInformation](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.taskexecutioninformation.failureinformation#Microsoft_Azure_Batch_TaskExecutionInformation_FailureInformation) é definida. Analise a propriedade **FailureInformation** para determinar o erro. Por exemplo, aqui está um erro que ocorre no upload do arquivo se o contêiner não pode ser encontrado:

```
Category: UserError
Code: FileUploadContainerNotFound
Message: One of the specified Azure container(s) was not found while attempting to upload an output file
```

Em cada upload de arquivo, o lote grava dois arquivos de log para o nó de computação, `fileuploadout.txt` e `fileuploaderr.txt`. Você pode analisar esses arquivos de log para saber mais sobre uma falha específica. Em casos em que o upload do arquivo nunca foi tentado, por exemplo porque não foi possível executar a própria tarefa, então esses arquivos de log não existirão.

## <a name="diagnose-file-upload-performance"></a>Diagnosticar desempenho de carregamento de arquivo

O arquivo `fileuploadout.txt` registra em log o progresso de upload. Você pode analisar este arquivo para obter mais informações sobre o quanto os carregamentos de arquivo estão demorando. Tenha em mente que há muitos fatores que contribuem para o desempenho de upload, incluindo o tamanho do nó, outra atividade no nó no momento do upload, se o contêiner de destino está na mesma região que o pool de lote, quantos nós estão carregando a conta de armazenamento ao mesmo tempo, e assim por diante.

## <a name="use-the-batch-service-api-with-the-batch-file-conventions-standard"></a>Usar a API de serviço de lote com o padrão de convenções de arquivo em lotes

Quando você mantém a saída da tarefa com a API de serviço de lote, você pode nomear o contêiner de destino e os blobs como desejar. Você também pode optar por nomeá-los de acordo com o [padrão de Convenções de Arquivo em Lotes](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/Batch/Support/FileConventions#conventions). O padrão de Convenções de Arquivo determina os nomes do contêiner de destino e do blob no Armazenamento do Azure para um determinado arquivo de saída com base nos nomes do trabalho e da tarefa. Se você usar o padrão de Convenções de Arquivo para nomear arquivos de saída, os arquivos de saída estarão disponíveis para exibição no [portal do Azure](https://portal.azure.com).

Se você estiver desenvolvendo em C#, poderá usar os métodos incorporados à [biblioteca Convenções de Arquivo em Lote do Azure para .NET](https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files). Essa biblioteca cria os contêineres corretamente nomeados e os caminhos de blob para você. Por exemplo, você pode chamar a API para obter o nome correto para o contêiner, com base no nome do trabalho:

```csharp
string containerName = job.OutputStorageContainerName();
```

Você pode usar o método [CloudJobExtensions.GetOutputStorageContainerUrl](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.conventions.files.cloudjobextensions.getoutputstoragecontainerurl) para retornar uma URL de assinatura de acesso compartilhado (SAS) que é usada para gravar no contêiner. Você pode passar esse SAS para o construtor [OutputFileBlobContainerDestination](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfileblobcontainerdestination).

Se você estiver desenvolvendo com uma linguagem que não C#, precisará implantar o padrão de Convenções de Arquivo você mesmo.

## <a name="code-sample"></a>Exemplo de código

O [PersistOutputs][github_persistoutputs] sample project is one of the [Azure Batch code samples][github_samples] no github. Essa solução do Visual Studio demonstra como usar a biblioteca de cliente em lotes para .NET para manter a saída da tarefa para o armazenamento durável. Para executar o exemplo, siga estas etapas:

1. Abra o projeto no **Visual Studio 2019**.
2. Adicione suas **credenciais de conta** do Lote e do Armazenamento a **AccountSettings.settings** no projeto Microsoft.Azure.Batch.Samples.Common.
3. **Compile** (mas não execute) a solução. Restaure todos os pacotes NuGet, se solicitado.
4. Use o portal do Azure para carregar um [pacote de aplicativos](batch-application-packages.md) para **PersistOutputsTask**. Inclua o `PersistOutputsTask.exe` e seus assemblies dependentes no pacote .zip, defina a ID do aplicativo como "PersistOutputsTask" e a versão do pacote de aplicativos como "1.0".
5. **Inicie** (execute) o projeto **PersistOutputs**.
6. Quando solicitado a escolher a tecnologia de persistência a usar para executar o exemplo, digite **2** para executar o exemplo usando a API do serviço de lote para manter a saída da tarefa.
7. Se desejado, execute o exemplo novamente, inserindo **3** para manter a saída com a API de serviço de lote e também para nomear o contêiner de destino e o caminho de blob de acordo com o padrão de Convenções de Arquivo.

## <a name="next-steps"></a>Próximas etapas

- Para obter mais informações sobre persistência de saída de tarefa com a biblioteca de Convenções de Arquivo para .NET, consulte [Persistir dados de tarefa e trabalho no Armazenamento do Microsoft Azure com a biblioteca de Convenções de Arquivo em Lotes para .NET](batch-task-output-file-conventions.md).
- Para obter mais informações sobre outras abordagens para manter os dados de saída em lote do Azure, consulte [Manter saída de trabalho e tarefa no Armazenamento do Azure](batch-task-output.md).

[github_persistoutputs]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs
[github_samples]: https://github.com/Azure/azure-batch-samples
