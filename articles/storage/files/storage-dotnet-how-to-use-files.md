---
title: Desenvolver para os Arquivos do Azure com .NET | Microsoft Docs
description: Saiba como desenvolver aplicativos e serviços .NET que usam o Arquivos do Azure para armazenar dados de arquivo.
services: storage
author: roygara
ms.service: storage
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/22/2017
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 2b615bbe7ffdf2f709cd7d7b0add4f956bec6a84
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63763460"
---
# <a name="develop-for-azure-files-with-net"></a>Desenvolvimento para o Arquivos do Azure com .NET

[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

Este tutorial demonstra as noções básicas de usar o .NET para desenvolver aplicativos que usam os [Arquivos do Azure](storage-files-introduction.md) para armazenar dados de arquivo. Este tutorial cria um aplicativo de console simples para executar ações básicas com o .NET e os Arquivos do Azure:

* Obter o conteúdo de um arquivo
* Defina a cota (tamanho máximo) para o compartilhamento de arquivos.
* Crie uma assinatura de acesso compartilhado (chave SAS) para um arquivo que usa uma política de acesso compartilhado definida no compartilhamento.
* Copie um arquivo em outro arquivo na mesma conta de armazenamento.
* Copie um arquivo em um blob na mesma conta de armazenamento.
* Usar métricas de armazenamento do Azure para solucionar problemas

Para saber mais sobre os Arquivos do Azure, consulte [Introdução aos Arquivos do Azure](storage-files-introduction.md).

[!INCLUDE [storage-check-out-samples-dotnet](../../../includes/storage-check-out-samples-dotnet.md)]

## <a name="understanding-the-net-apis"></a>Compreendendo as APIs do .NET

Os Arquivos do Azure fornecem duas abordagens amplas para aplicativos clientes: SMB (Protocolo SMB) e REST. No .NET, essas abordagens são abstraídas pelas APIs do `System.IO` e do `WindowsAzure.Storage`.

API | Quando usar | Observações
----|-------------|------
[System.IO](https://docs.microsoft.com/dotnet/api/system.io) | Seu aplicativo: <ul><li>Precisa ler/gravar arquivos por meio do SMB</li><li>Está em execução em um dispositivo que tem acesso pela porta 445 à sua conta do Arquivos do Azure</li><li>Não precisa gerenciar nenhum das configurações administrativas do compartilhamento de arquivo</li></ul> | Codificar a E/S de arquivo com os Arquivos do Azure no protocolo SMB geralmente é igual a codificar a E/S com qualquer dispositivo de compartilhamento de arquivos de rede ou de armazenamento local. Consulte [este tutorial](https://docs.microsoft.com/dotnet/csharp/tutorials/console-teleprompter) para obter uma introdução a uma quantidade de recursos do .NET, incluindo de E/S de arquivo.
[WindowsAzure.Storage](https://docs.microsoft.com/dotnet/api/overview/azure/storage?view=azure-dotnet#client-library) | Seu aplicativo: <ul><li>Não consegue acessar os Arquivos do Azure via protocolo SMB na porta 445 devido a restrições de ISP ou firewall</li><li>Requer a funcionalidade administrativa, como a capacidade de definir uma cota de compartilhamento de arquivos ou criar uma assinatura de acesso compartilhado</li></ul> | Este artigo demonstra o uso de `WindowsAzure.Storage` para E/S de arquivo usando a REST (em vez do protocolo SMB) e o gerenciamento do compartilhamento de arquivos.

## <a name="create-the-console-application-and-obtain-the-assembly"></a>Criar o aplicativo do console e obter o assembly
No Visual Studio, crie um novo aplicativo de console do Windows. As etapas a seguir mostram como criar um aplicativo de console no Visual Studio 2017. No entanto, as etapas são semelhantes em outras versões do Visual Studio.

1. Selecione **Arquivo** > **Novo** > **Projeto**
2. Selecione **Instalado** > **Modelos** > **Visual C#** > **Área de trabalho clássica do Windows**
3. Selecione **Aplicativo do Console (.NET Framework)**
4. Insira um nome para seu aplicativo no campo **Nome:**
5. Selecione **OK**

Todos os exemplos de código neste tutorial podem ser adicionados ao método `Main()` no arquivo`Program.cs` do aplicativo de console.

Você pode usar a Biblioteca de Cliente da Armazenamento do Azure em qualquer tipo de aplicativo .NET, incluindo um serviço de nuvem do Azure, um aplicativo Web do Azure e aplicativos da área de trabalho ou móvel. Neste guia, usamos um aplicativo de console para simplificar.

## <a name="use-nuget-to-install-the-required-packages"></a>Use o NuGet para instalar os pacotes necessários
Há dois pacotes que você precisará referenciar em seu projeto para concluir este tutorial:

* [Biblioteca de cliente de Armazenamento do Microsoft Azure para .NET](https://www.nuget.org/packages/WindowsAzure.Storage/): este pacote fornece acesso programático aos recursos de dados em sua conta de armazenamento.
* [Biblioteca do Gerenciador de Configuração do Microsoft Azure para .NET](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/): este pacote fornece uma classe para analisar uma cadeia de conexão em um arquivo de configuração, independentemente do local em que o aplicativo está sendo executado.

Você pode usar NuGet para obter os dois pacotes. Siga estas etapas:

1. Clique com o botão direito do mouse no seu projeto no **Gerenciador de Soluções** e escolha **Gerenciar Pacotes NuGet**.
2. Pesquise online por "Windowsazure.Storage" e clique em **Instalar** para instalar a Biblioteca de Cliente de Armazenamento e suas dependências.
3. Pesquise online por "WindowsAzure.ConfigurationManager" e clique em **Instalar** para instalar o Gerenciador de Configuração do Azure.

## <a name="save-your-storage-account-credentials-to-the-appconfig-file"></a>Salvar suas credenciais da conta de armazenamento no arquivo app.config
Em seguida, salve suas credenciais no arquivo app.config do projeto. Edite o arquivo app.config para que ele se pareça com o exemplo a seguir, substituindo `myaccount` pelo nome da conta de armazenamento e `mykey` pela chave da conta de armazenamento.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <startup>
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
    </startup>
    <appSettings>
        <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=StorageAccountKeyEndingIn==" />
    </appSettings>
</configuration>
```

> [!NOTE]
> A versão mais recente do emulador de armazenamento do Azure não dá suporte aos Arquivos do Azure. Sua cadeia de conexão deve ter como destino uma Conta de Armazenamento do Azure na nuvem para funcionar com os Arquivos do Azure.

## <a name="add-using-directives"></a>Adicionar diretivas using
Abra o arquivo `Program.cs` no Gerenciador de Soluções e adicione as diretivas using a seguir à parte superior do arquivo.

```csharp
using Microsoft.Azure; // Namespace for Azure Configuration Manager
using Microsoft.WindowsAzure.Storage; // Namespace for Storage Client Library
using Microsoft.WindowsAzure.Storage.Blob; // Namespace for Azure Blobs
using Microsoft.WindowsAzure.Storage.File; // Namespace for Azure Files
```

[!INCLUDE [storage-cloud-configuration-manager-include](../../../includes/storage-cloud-configuration-manager-include.md)]

## <a name="access-the-file-share-programmatically"></a>Acessar o compartilhamento de arquivos programaticamente
Em seguida, adicione o código a seguir ao método `Main()` (após o código mostrado acima) para recuperar a cadeia de conexão. Esse código obtém uma referência para o arquivo que criamos anteriormente e exporta seu conteúdo para a janela do console.

```csharp
// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    // Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.GetRootDirectoryReference();

    // Get a reference to the directory we created previously.
    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");

    // Ensure that the directory exists.
    if (sampleDir.Exists())
    {
        // Get a reference to the file we created previously.
        CloudFile file = sampleDir.GetFileReference("Log1.txt");

        // Ensure that the file exists.
        if (file.Exists())
        {
            // Write the contents of the file to the console window.
            Console.WriteLine(file.DownloadTextAsync().Result);
        }
    }
}
```

Execute o aplicativo de console para ver a saída.

## <a name="set-the-maximum-size-for-a-file-share"></a>Definir o tamanho máximo de um compartilhamento de arquivos
A partir da versão 5.x da Biblioteca de Cliente do Armazenamento do Azure, você pode definir a cota (ou o tamanho máximo) de um compartilhamento de arquivos em gigabytes. Você também pode verificar a quantidade de dados atualmente armazenada no compartilhamento.

Ao definir a cota para um compartilhamento, você pode limitar o tamanho total dos arquivos armazenados no compartilhamento. Se o tamanho total dos arquivos no compartilhamento ultrapassar a cota definida no compartilhamento, os clientes não poderão aumentar o tamanho dos arquivos existentes ou criar novos arquivos, a menos que eles estejam vazios.

O exemplo a seguir mostra como verificar o uso atual de um compartilhamento e como definir a cota para o compartilhamento.

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    // Check current usage stats for the share.
    // Note that the ShareStats object is part of the protocol layer for the File service.
    Microsoft.WindowsAzure.Storage.File.Protocol.ShareStats stats = share.GetStats();
    Console.WriteLine("Current share usage: {0} GB", stats.Usage.ToString());

    // Specify the maximum size of the share, in GB.
    // This line sets the quota to be 10 GB greater than the current usage of the share.
    share.Properties.Quota = 10 + stats.Usage;
    share.SetProperties();

    // Now check the quota for the share. Call FetchAttributes() to populate the share's properties.
    share.FetchAttributes();
    Console.WriteLine("Current share quota: {0} GB", share.Properties.Quota);
}
```

### <a name="generate-a-shared-access-signature-for-a-file-or-file-share"></a>Gerar uma assinatura de acesso compartilhado para um arquivo ou compartilhamento de arquivos
A partir da versão 5.x da Biblioteca de Cliente do Armazenamento do Azure, você pode gerar uma SAS (assinatura de acesso compartilhado) para um compartilhamento de arquivos ou para um arquivo individual. Você também pode criar uma política de acesso compartilhado em um compartilhamento de arquivos para gerenciar assinaturas de acesso compartilhado. Recomendamos a criação de uma política de acesso compartilhado, pois ela fornece um meio de revogar o SAS se ele for comprometido.

O exemplo a seguir cria uma política de acesso compartilhado em um compartilhamento e, em seguida, usa essa política para fornecer as restrições a um SAS em um arquivo no compartilhamento.

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    string policyName = "sampleSharePolicy" + DateTime.UtcNow.Ticks;

    // Create a new shared access policy and define its constraints.
    SharedAccessFilePolicy sharedPolicy = new SharedAccessFilePolicy()
        {
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessFilePermissions.Read | SharedAccessFilePermissions.Write
        };

    // Get existing permissions for the share.
    FileSharePermissions permissions = share.GetPermissions();

    // Add the shared access policy to the share's policies. Note that each policy must have a unique name.
    permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
    share.SetPermissions(permissions);

    // Generate a SAS for a file in the share and associate this access policy with it.
    CloudFileDirectory rootDir = share.GetRootDirectoryReference();
    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");
    CloudFile file = sampleDir.GetFileReference("Log1.txt");
    string sasToken = file.GetSharedAccessSignature(null, policyName);
    Uri fileSasUri = new Uri(file.StorageUri.PrimaryUri.ToString() + sasToken);

    // Create a new CloudFile object from the SAS, and write some text to the file.
    CloudFile fileSas = new CloudFile(fileSasUri);
    fileSas.UploadText("This write operation is authorized via SAS.");
    Console.WriteLine(fileSas.DownloadText());
}
```

Para saber mais sobre como criar e usar as assinaturas de acesso compartilhado, confira [Como usar SAS (Assinaturas de Acesso Compartilhado)](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) e [Criar e usar uma SAS com os Blobs do Azure](../blobs/storage-dotnet-shared-access-signature-part-2.md).

## <a name="copy-files"></a>Copiar arquivos
A partir da versão 5.x da Biblioteca de Cliente do Armazenamento do Azure, você pode copiar um arquivo em outro arquivo, um arquivo em um blob ou um blob em um arquivo. Nas próximas seções, demonstramos como executar essas operações de cópia de modo programático.

Você também pode usar o AzCopy para copiar um arquivo para outro, ou para copiar um blob em um arquivo ou vice-versa. Veja [Transferir dados com o Utilitário da Linha de Comando AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

> [!NOTE]
> Se você estiver copiando um blob em um arquivo, ou um arquivo em um blob, use uma assinatura de acesso compartilhado (SAS) para autorizar o acesso ao objeto de origem, mesmo se estiver copiando dentro da mesma conta de armazenamento.
> 
> 

**Copiar um arquivo para outro arquivo** O exemplo a seguir copia um arquivo em outro arquivo no mesmo compartilhamento. Como essa operação de cópia realiza a cópia entre arquivos na mesma conta de armazenamento, você pode usar a autenticação de Chave compartilhada para executar a cópia.

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    // Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.GetRootDirectoryReference();

    // Get a reference to the directory we created previously.
    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");

    // Ensure that the directory exists.
    if (sampleDir.Exists())
    {
        // Get a reference to the file we created previously.
        CloudFile sourceFile = sampleDir.GetFileReference("Log1.txt");

        // Ensure that the source file exists.
        if (sourceFile.Exists())
        {
            // Get a reference to the destination file.
            CloudFile destFile = sampleDir.GetFileReference("Log1Copy.txt");

            // Start the copy operation.
            destFile.StartCopy(sourceFile);

            // Write the contents of the destination file to the console window.
            Console.WriteLine(destFile.DownloadText());
        }
    }
}
```

**Copiar um arquivo para um blob** O exemplo a seguir cria um arquivo e o copia em um blob dentro da mesma conta de armazenamento. O exemplo cria uma SAS para o arquivo de origem, que o serviço usa para autorizar o acesso ao arquivo de origem durante a operação de cópia.

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Create a new file share, if it does not already exist.
CloudFileShare share = fileClient.GetShareReference("sample-share");
share.CreateIfNotExists();

// Create a new file in the root directory.
CloudFile sourceFile = share.GetRootDirectoryReference().GetFileReference("sample-file.txt");
sourceFile.UploadText("A sample file in the root directory.");

// Get a reference to the blob to which the file will be copied.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
CloudBlobContainer container = blobClient.GetContainerReference("sample-container");
container.CreateIfNotExists();
CloudBlockBlob destBlob = container.GetBlockBlobReference("sample-blob.txt");

// Create a SAS for the file that's valid for 24 hours.
// Note that when you are copying a file to a blob, or a blob to a file, you must use a SAS
// to authorize access to the source object, even if you are copying within the same
// storage account.
string fileSas = sourceFile.GetSharedAccessSignature(new SharedAccessFilePolicy()
{
    // Only read permissions are required for the source file.
    Permissions = SharedAccessFilePermissions.Read,
    SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24)
});

// Construct the URI to the source file, including the SAS token.
Uri fileSasUri = new Uri(sourceFile.StorageUri.PrimaryUri.ToString() + fileSas);

// Copy the file to the blob.
destBlob.StartCopy(fileSasUri);

// Write the contents of the file to the console window.
Console.WriteLine("Source file contents: {0}", sourceFile.DownloadText());
Console.WriteLine("Destination blob contents: {0}", destBlob.DownloadText());
```

Você pode copiar um blob em um arquivo da mesma maneira. Se o objeto de origem for um blob, crie uma SAS para autorizar o acesso ao blob durante a operação de cópia.

## <a name="share-snapshots"></a>Instantâneos de compartilhamento
A partir da versão 8.5 da Biblioteca de Cliente do Armazenamento do Microsoft Azure, você pode criar um instantâneo de compartilhamento. Também pode listar ou procurar os instantâneos de compartilhamento e excluir os instantâneos de compartilhamento. Instantâneos de compartilhamentos são somente leitura, portanto, nenhuma operação de gravação é permitida em instantâneos de compartilhamento.

**Criar instantâneos de compartilhamento**

O exemplo a seguir cria um instantâneo de compartilhamento de arquivos.

```csharp
storageAccount = CloudStorageAccount.Parse(ConnectionString); 
fClient = storageAccount.CreateCloudFileClient(); 
string baseShareName = "myazurefileshare"; 
CloudFileShare myShare = fClient.GetShareReference(baseShareName); 
var snapshotShare = myShare.Snapshot();

```
**Listar instantâneos de compartilhamento**

O exemplo a seguir lista os instantâneos de compartilhamento em um compartilhamento.

```csharp
var shares = fClient.ListShares(baseShareName, ShareListingDetails.All);
```

**Procurar arquivos e diretórios nos instantâneos de compartilhamento**

O exemplo a seguir procura arquivos e diretórios nos instantâneos de compartilhamento.

```csharp
CloudFileShare mySnapshot = fClient.GetShareReference(baseShareName, snapshotTime); 
var rootDirectory = mySnapshot.GetRootDirectoryReference(); 
var items = rootDirectory.ListFilesAndDirectories();
```

**Listar compartilhamentos e instantâneos de compartilhamento e restaurar arquivos ou compartilhamentos de arquivos dos instantâneos de compartilhamento** 

Tirar um instantâneo de um compartilhamento de arquivos permite a recuperação de arquivos individuais ou de todo o compartilhamento de arquivo no futuro. 

Você pode restaurar um arquivo de um instantâneo de compartilhamento de arquivo consultando os instantâneos de compartilhamento de um compartilhamento de arquivos. Em seguida, pode recuperar um arquivo que pertence a um determinado instantâneo de compartilhamento e usar essa versão para ler e comparar diretamente ou para restaurar.

```csharp
CloudFileShare liveShare = fClient.GetShareReference(baseShareName);
var rootDirOfliveShare = liveShare.GetRootDirectoryReference();

       var dirInliveShare = rootDirOfliveShare.GetDirectoryReference(dirName);
var fileInliveShare = dirInliveShare.GetFileReference(fileName);

           
CloudFileShare snapshot = fClient.GetShareReference(baseShareName, snapshotTime);
var rootDirOfSnapshot = snapshot.GetRootDirectoryReference();

       var dirInSnapshot = rootDirOfSnapshot.GetDirectoryReference(dirName);
var fileInSnapshot = dir1InSnapshot.GetFileReference(fileName);

string sasContainerToken = string.Empty;
       SharedAccessFilePolicy sasConstraints = new SharedAccessFilePolicy();
       sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
       sasConstraints.Permissions = SharedAccessFilePermissions.Read;
       //Generate the shared access signature on the container, setting the constraints directly on the signature.
sasContainerToken = fileInSnapshot.GetSharedAccessSignature(sasConstraints);

string sourceUri = (fileInSnapshot.Uri.ToString() + sasContainerToken + "&" + fileInSnapshot.SnapshotTime.ToString()); ;
fileInliveShare.StartCopyAsync(new Uri(sourceUri));

```


**Excluir instantâneos de compartilhamento**

O exemplo a seguir exclui um instantâneo de compartilhamento de arquivos.

```csharp
CloudFileShare mySnapshot = fClient.GetShareReference(baseShareName, snapshotTime); mySnapshot.Delete(null, null, null);
```

## <a name="troubleshooting-azure-files-using-metrics"></a>Solução de problemas dos Arquivos do Azure usando métricas
A Análise de Armazenamento do Azure agora dá suporte a métricas para os Arquivos do Azure. Com dados de métricas, você pode rastrear solicitações e diagnosticar problemas.

É possível habilitar métricas para os Arquivos do Azure por meio do [Portal do Azure](https://portal.azure.com). Você também pode habilitar métricas programaticamente ao chamar a operação Definir Propriedades de Serviço do Arquivo pela API REST ou uma operação semelhante na Biblioteca do Cliente de Armazenamento.

O exemplo de código a seguir mostra como usar a Biblioteca de Cliente de Armazenamento para .NET para habilitar as métricas para os Arquivos do Azure.

Primeiro, adicione as seguintes diretivas `using` ao arquivo `Program.cs` além daquelas adicionadas acima:

```csharp
using Microsoft.WindowsAzure.Storage.File.Protocol;
using Microsoft.WindowsAzure.Storage.Shared.Protocol;
```

Observe que, enquanto os Blobs do Azure, a Tabela do Azure e as Filas do Azure usam o tipo `ServiceProperties` compartilhado no namespace `Microsoft.WindowsAzure.Storage.Shared.Protocol`, os Arquivos do Azure usa seu próprio tipo, o `FileServiceProperties`, no namespace `Microsoft.WindowsAzure.Storage.File.Protocol`. Os namespaces devem ser referenciados no seu código, no entanto, para que o código a seguir seja compilado.

```csharp
// Parse your storage connection string from your application's configuration file.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));
// Create the File service client.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Set metrics properties for File service.
// Note that the File service currently uses its own service properties type,
// available in the Microsoft.WindowsAzure.Storage.File.Protocol namespace.
fileClient.SetServiceProperties(new FileServiceProperties()
{
    // Set hour metrics
    HourMetrics = new MetricsProperties()
    {
        MetricsLevel = MetricsLevel.ServiceAndApi,
        RetentionDays = 14,
        Version = "1.0"
    },
    // Set minute metrics
    MinuteMetrics = new MetricsProperties()
    {
        MetricsLevel = MetricsLevel.ServiceAndApi,
        RetentionDays = 7,
        Version = "1.0"
    }
});

// Read the metrics properties we just set.
FileServiceProperties serviceProperties = fileClient.GetServiceProperties();
Console.WriteLine("Hour metrics:");
Console.WriteLine(serviceProperties.HourMetrics.MetricsLevel);
Console.WriteLine(serviceProperties.HourMetrics.RetentionDays);
Console.WriteLine(serviceProperties.HourMetrics.Version);
Console.WriteLine();
Console.WriteLine("Minute metrics:");
Console.WriteLine(serviceProperties.MinuteMetrics.MetricsLevel);
Console.WriteLine(serviceProperties.MinuteMetrics.RetentionDays);
Console.WriteLine(serviceProperties.MinuteMetrics.Version);
```

Além disso, você pode consultar o [artigo de Solução de problemas de arquivos do Azure](storage-troubleshoot-windows-file-connection-problems.md) para obter diretrizes completas de solução de problemas.

## <a name="next-steps"></a>Próximas etapas
Veja estes links para obter mais informações sobre o Arquivos do Azure.

### <a name="conceptual-articles-and-videos"></a>Artigos e vídeos conceituais
* [Arquivos do Azure: um sistema de arquivos SMB de nuvem ininterrupta para Windows e Linux](https://azure.microsoft.com/documentation/videos/azurecon-2015-azure-files-storage-a-frictionless-cloud-smb-file-system-for-windows-and-linux/)
* [Como usar os Arquivos do Azure com Linux](storage-how-to-use-files-linux.md)

### <a name="tooling-support-for-file-storage"></a>Suporte de ferramentas para o armazenamento de arquivos
* [Como usar o AzCopy com o Armazenamento do Microsoft Azure](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)
* [Usando a CLI do Azure com o Armazenamento do Azure](../common/storage-azure-cli.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#create-and-manage-file-shares)
* [Solução de problemas do Arquivos do Azure](https://docs.microsoft.com/azure/storage/storage-troubleshoot-file-connection-problems)

### <a name="reference"></a>Referência
* [Referência à Biblioteca de Cliente de Armazenamento para .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx)
* [Referência à API REST do serviço de arquivos](https://msdn.microsoft.com/library/azure/dn167006.aspx)

### <a name="blog-posts"></a>Postagens no blog
* [O Arquivos do Azure já está disponível ao público em geral](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/)
* [Por dentro do Arquivos do Azure](https://azure.microsoft.com/blog/inside-azure-file-storage/)
* [Apresentando o serviço de arquivo do Microsoft Azure](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
* [Persistindo conexões para arquivos do Microsoft Azure](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx)
