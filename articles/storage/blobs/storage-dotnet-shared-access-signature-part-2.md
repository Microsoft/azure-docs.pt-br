---
title: Criar e usar uma SAS (Assinatura de Acesso Compartilhado) com o Armazenamento de Blobs do Azure | Microsoft Docs
description: Este tutorial mostra como criar assinaturas de acesso compartilhado para uso com o Armazenamento de Blobs e como consumi-las em seus aplicativos cliente.
services: storage
documentationcenter: 
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 491e0b3c-76d4-4149-9a80-bbbd683b1f3e
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/15/2017
ms.author: tamram
ms.openlocfilehash: 9dde12acde748c48b56f9f96ee772fca49954358
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="shared-access-signatures-part-2-create-and-use-a-sas-with-blob-storage"></a>Assinaturas de Acesso Compartilhado, Parte 2: criar e usar uma SAS com o Armazenamento de Blobs

[Parte 1](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) deste tutorial explorou as assinaturas de acesso compartilhado (SAS) e explicou as práticas recomendadas para sua utilização. A Parte 2 mostra como gerar e usar assinaturas de acesso compartilhado com o Armazenamento de Blobs. Os exemplos são escritos em C# e usam a Biblioteca de Cliente do Armazenamento do Azure para .NET. Os exemplos neste tutorial:

* Geram uma assinatura de acesso compartilhado em um contêiner
* Geram uma assinatura de acesso compartilhado em um blob
* Criam uma política de acesso armazenado para gerenciar assinaturas nos recursos de um contêiner
* Testam as assinaturas de acesso compartilhado em um aplicativo cliente

## <a name="about-this-tutorial"></a>Sobre este tutorial
Neste tutorial, criamos dois aplicativos de console que demonstram a criação e uso de assinaturas de acesso compartilhado para contêineres e blobs:

**Aplicativo 1**: o aplicativo de gerenciamento. Gera uma assinatura de acesso compartilhado para um contêiner e um blob. Inclui a chave de acesso da conta de armazenamento no código-fonte.

**Aplicativo 2**: o aplicativo cliente. Acessa os recursos do contêiner e do blob, usando as assinaturas de acesso compartilhado criadas com o primeiro aplicativo. Usa as assinaturas de acesso compartilhado apenas para acessar o contêiner e os recursos de blob-- ele *não* inclui a chave de acesso da conta de armazenamento.

## <a name="part-1-create-a-console-application-to-generate-shared-access-signatures"></a>Parte 1: Criar um aplicativo de console para gerar assinaturas de acesso compartilhado
Primeiro, verifique se você tem a Biblioteca de Cliente do Armazenamento do Azure para .NET instalada. Você pode instalar o [pacote do NuGet](http://nuget.org/packages/WindowsAzure.Storage/ "pacote do NuGet") que contém os assemblies mais atualizados para a biblioteca de cliente. Esse é o método recomendado para garantir que você tenha as correções mais recentes. Você também pode baixar a biblioteca de cliente como parte da versão mais recente do [SDK do Azure para .NET](https://azure.microsoft.com/downloads/).

No Visual Studio, crie um novo aplicativo de console do Windows e dê a ele o nome **GenerateSharedAccessSignatures**. Adicione referências a [Microsoft.WindowsAzure.ConfigurationManager](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager) e [WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage/) usando uma das seguintes abordagens:

* Use o [Gerenciador de pacotes do NuGet](https://docs.nuget.org/consume/installing-nuget) no Visual Studio. Selecione **Projeto** > **Gerenciar Pacotes do NuGet**, pesquise online por cada pacote (Microsoft.WindowsAzure.ConfigurationManager e WindowsAzure.Storage) e instale-os.
* Como alternativa, localize esses assemblies na sua instalação do SDK do Azure e adicione referências a eles:
  * Microsoft.WindowsAzure.Configuration.dll
  * Microsoft.WindowsAzure.Storage.dll

Na parte superior do arquivo Program.cs, adicione as seguintes diretivas **using** :

```csharp
using System.IO;
using Microsoft.Azure;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
```

Edite o arquivo app.config para que ele contenha um parâmetro de configuração com uma cadeia de conexão que aponte para a sua conta de armazenamento. O arquivo app.config deve ser semelhante a este:

```xml
<configuration>
  <startup>
    <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2" />
  </startup>
  <appSettings>
    <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey"/>
  </appSettings>
</configuration>
```

### <a name="generate-a-shared-access-signature-uri-for-a-container"></a>Gerar um URI de assinatura de acesso compartilhado para um contêiner
Em primeiro lugar, adicionaremos um método para gerar uma assinatura de acesso compartilhado em um novo contêiner. Nesse caso, a assinatura não está associada a uma política de acesso armazenado, portanto, ela transporta no URI as informações que indicam sua hora de expiração e as permissões concedidas por ela.

Primeiro, adicione código ao método **Main()** para autenticar o acesso à sua conta de armazenamento e criar um novo contêiner:

```csharp
static void Main(string[] args)
{
    //Parse the connection string and return a reference to the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));

    //Create the blob client object.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    //Get a reference to a container to use for the sample code, and create it if it does not exist.
    CloudBlobContainer container = blobClient.GetContainerReference("sascontainer");
    container.CreateIfNotExists();

    //Insert calls to the methods created below here...

    //Require user input before closing the console window.
    Console.ReadLine();
}
```

Em seguida, adicione um método que gere a assinatura de acesso compartilhado para o contêiner e retorne o URI de assinatura:

```csharp
static string GetContainerSasUri(CloudBlobContainer container)
{
    //Set the expiry time and permissions for the container.
    //In this case no start time is specified, so the shared access signature becomes valid immediately.
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();
    sasConstraints.SharedAccessExpiryTime = DateTimeOffset.UtcNow.AddHours(24);
    sasConstraints.Permissions = SharedAccessBlobPermissions.List | SharedAccessBlobPermissions.Write;

    //Generate the shared access signature on the container, setting the constraints directly on the signature.
    string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);

    //Return the URI string for the container, including the SAS token.
    return container.Uri + sasContainerToken;
}
```

Adicione as linhas a seguir na parte inferior do método **Main()**, antes da chamada de **Console.ReadLine()**, para chamar **GetContainerSasUri()** e grave o URI da assinatura na janela do console:

```csharp
//Generate a SAS URI for the container, without a stored access policy.
Console.WriteLine("Container SAS URI: " + GetContainerSasUri(container));
Console.WriteLine();
```

Compile e execute para gerar a saída do URI de assinatura de acesso compartilhado para o novo contêiner. O URI será semelhante a este:

```
https://storageaccount.blob.core.windows.net/sascontainer?sv=2012-02-12&se=2013-04-13T00%3A12%3A08Z&sr=c&sp=wl&sig=t%2BbzU9%2B7ry4okULN9S0wst%2F8MCUhTjrHyV9rDNLSe8g%3D
```

Depois de executar o código, a assinatura de acesso compartilhado criada no contêiner terá validade durante as próximas 24 horas. A assinatura concede uma permissão de cliente para listar os blobs no contêiner e gravar novos blobs no contêiner.

### <a name="generate-a-shared-access-signature-uri-for-a-blob"></a>Gerar um URI de assinatura de acesso compartilhado para um blob
Em seguida, escreveremos um código semelhante para criar um novo blob dentro do contêiner e gerar uma assinatura de acesso compartilhado para ele. Essa assinatura de acesso compartilhado não está associada a uma política de acesso armazenado, portanto, ela inclui as informações de hora de início, hora de expiração e permissão no URI.

Adicione um novo método que cria um novo blob e escreve um texto nele; em seguida, uma assinatura de acesso compartilhado será gerada e retornará o URI de assinatura:

```csharp
static string GetBlobSasUri(CloudBlobContainer container)
{
    //Get a reference to a blob within the container.
    CloudBlockBlob blob = container.GetBlockBlobReference("sasblob.txt");

    //Upload text to the blob. If the blob does not yet exist, it will be created.
    //If the blob does exist, its existing content will be overwritten.
    string blobContent = "This blob will be accessible to clients via a shared access signature (SAS).";
    blob.UploadText(blobContent);

    //Set the expiry time and permissions for the blob.
    //In this case, the start time is specified as a few minutes in the past, to mitigate clock skew.
    //The shared access signature will be valid immediately.
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();
    sasConstraints.SharedAccessStartTime = DateTimeOffset.UtcNow.AddMinutes(-5);
    sasConstraints.SharedAccessExpiryTime = DateTimeOffset.UtcNow.AddHours(24);
    sasConstraints.Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Write;

    //Generate the shared access signature on the blob, setting the constraints directly on the signature.
    string sasBlobToken = blob.GetSharedAccessSignature(sasConstraints);

    //Return the URI string for the container, including the SAS token.
    return blob.Uri + sasBlobToken;
}
```

Na parte inferior do método **Main()**, adicione as linhas a seguir para chamar **GetBlobSasUri()**, antes da chamada para **Console.ReadLine()**, e grave o URI de assinatura de acesso compartilhado na janela de console:

```csharp
//Generate a SAS URI for a blob within the container, without a stored access policy.
Console.WriteLine("Blob SAS URI: " + GetBlobSasUri(container));
Console.WriteLine();
```

Compile e execute para gerar a saída do URI de assinatura de acesso compartilhado para o novo blob. O URI será semelhante a este:

```
https://storageaccount.blob.core.windows.net/sascontainer/sasblob.txt?sv=2012-02-12&st=2013-04-12T23%3A37%3A08Z&se=2013-04-13T00%3A12%3A08Z&sr=b&sp=rw&sig=dF2064yHtc8RusQLvkQFPItYdeOz3zR8zHsDMBi4S30%3D
```

### <a name="create-a-stored-access-policy-on-the-container"></a>Criar uma política de acesso armazenado no contêiner
Agora, vamos criar uma política de acesso armazenado no contêiner, que definirá as restrições de qualquer assinatura de acesso compartilhado associada a ela.

Nos exemplos anteriores, especificamos a hora de início (implícita ou explicitamente), a hora de expiração e as permissões no próprio URI de assinatura de acesso compartilhado. Nos exemplos a seguir, especificaremos isso na política de acesso armazenado, não na assinatura de acesso compartilhado. Isso nos permitirá alterar essas restrições sem reemitir a assinatura de acesso compartilhado.

É possível ter uma ou mais restrições na assinatura de acesso compartilhado e o restante na política de acesso armazenado. No entanto, você pode especificar apenas a hora de início, a hora de expiração e as permissões em um local ou no outro. Por exemplo, não é possível especificar permissões na assinatura de acesso compartilhado e especificá-las também na política de acesso armazenada.

Ao adicionar uma política de acesso armazenada a um contêiner, você deve obter as permissões do contêiner existente, adicionar a nova política de acesso e, em seguida, definir as permissões do contêiner.

Adicione um novo método que cria uma nova política de acesso armazenada em um contêiner e retorna o nome da política:

```csharp
static void CreateSharedAccessPolicy(CloudBlobClient blobClient, CloudBlobContainer container,
    string policyName)
{
    //Get the container's existing permissions.
    BlobContainerPermissions permissions = container.GetPermissions();

    //Create a new shared access policy and define its constraints.
    SharedAccessBlobPolicy sharedPolicy = new SharedAccessBlobPolicy()
    {
        SharedAccessExpiryTime = DateTimeOffset.UtcNow.AddHours(24),
        Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List | SharedAccessBlobPermissions.Read
    };

    //Add the new policy to the container's permissions, and set the container's permissions.
    permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
    container.SetPermissions(permissions);
}
```

Na parte inferior do método **Main()**, antes da chamada ao **Console.ReadLine()**, adicione as seguintes linhas para limpar primeiro todas as políticas de acesso existentes e, em seguida, chame o método **CreateSharedAccessPolicy()**:

```csharp
//Clear any existing access policies on container.
BlobContainerPermissions perms = container.GetPermissions();
perms.SharedAccessPolicies.Clear();
container.SetPermissions(perms);

//Create a new access policy on the container, which may be optionally used to provide constraints for
//shared access signatures on the container and the blob.
string sharedAccessPolicyName = "tutorialpolicy";
CreateSharedAccessPolicy(blobClient, container, sharedAccessPolicyName);
```

Ao limpar as políticas de acesso em um contêiner, você deve primeiro obter as permissões do contêiner existente e, em seguida, desmarcar as permissões e defini-las novamente.

### <a name="generate-a-shared-access-signature-uri-on-the-container-that-uses-an-access-policy"></a>Gerar um URI de assinatura de acesso compartilhado no contêiner que usa uma política de acesso
Em seguida, criaremos outra assinatura de acesso compartilhado no contêiner criado anteriormente, mas, desta vez, associaremos a assinatura à política de acesso armazenada criada no exemplo anterior.

Adicione um novo método para gerar outra assinatura de acesso compartilhado para o contêiner:

```csharp
static string GetContainerSasUriWithPolicy(CloudBlobContainer container, string policyName)
{
    //Generate the shared access signature on the container. In this case, all of the constraints for the
    //shared access signature are specified on the stored access policy.
    string sasContainerToken = container.GetSharedAccessSignature(null, policyName);

    //Return the URI string for the container, including the SAS token.
    return container.Uri + sasContainerToken;
}
```

Na parte inferior do método **Main()**, antes da chamada para **Console.ReadLine()**, adicione as linhas a seguir para chamar o método **GetContainerSasUriWithPolicy**:

```csharp
//Generate a SAS URI for the container, using a stored access policy to set constraints on the SAS.
Console.WriteLine("Container SAS URI using stored access policy: " + GetContainerSasUriWithPolicy(container, sharedAccessPolicyName));
Console.WriteLine();
```

### <a name="generate-a-shared-access-signature-uri-on-the-blob-that-uses-an-access-policy"></a>Gerar um URI de assinatura de acesso compartilhado no blob que usa uma política de acesso
Finalmente, adicionaremos um método semelhante para criar outro blob e gerar uma assinatura de acesso compartilhado associada a uma política de acesso armazenada.

Adicione um novo método para criar um blob e gerar uma assinatura de acesso compartilhado:

```csharp
static string GetBlobSasUriWithPolicy(CloudBlobContainer container, string policyName)
{
    //Get a reference to a blob within the container.
    CloudBlockBlob blob = container.GetBlockBlobReference("sasblobpolicy.txt");

    //Upload text to the blob. If the blob does not yet exist, it will be created.
    //If the blob does exist, its existing content will be overwritten.
    string blobContent = "This blob will be accessible to clients via a shared access signature. " +
    "A stored access policy defines the constraints for the signature.";
    MemoryStream ms = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
    ms.Position = 0;
    using (ms)
    {
        blob.UploadFromStream(ms);
    }

    //Generate the shared access signature on the blob.
    string sasBlobToken = blob.GetSharedAccessSignature(null, policyName);

    //Return the URI string for the container, including the SAS token.
    return blob.Uri + sasBlobToken;
}
```

Na parte inferior do método **Main()**, antes da chamada para **Console.ReadLine()**, adicione as linhas a seguir para chamar o método **GetBlobSasUriWithPolicy**:

```csharp
//Generate a SAS URI for a blob within the container, using a stored access policy to set constraints on the SAS.
Console.WriteLine("Blob SAS URI using stored access policy: " + GetBlobSasUriWithPolicy(container, sharedAccessPolicyName));
Console.WriteLine();
```

Agora, o método **Main()** deverá ser semelhante a este em sua totalidade. Execute-o para gravar os URIs de assinatura de acesso compartilhado na janela de console e, em seguida, copiá-los e colá-los em um arquivo de texto a ser usado na segunda parte deste tutorial.

```csharp
static void Main(string[] args)
{
    //Parse the connection string and return a reference to the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));

    //Create the blob client object.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    //Get a reference to a container to use for the sample code, and create it if it does not exist.
    CloudBlobContainer container = blobClient.GetContainerReference("sascontainer");
    container.CreateIfNotExists();

    //Generate a SAS URI for the container, without a stored access policy.
    Console.WriteLine("Container SAS URI: " + GetContainerSasUri(container));
    Console.WriteLine();

    //Generate a SAS URI for a blob within the container, without a stored access policy.
    Console.WriteLine("Blob SAS URI: " + GetBlobSasUri(container));
    Console.WriteLine();

    //Clear any existing access policies on container.
    BlobContainerPermissions perms = container.GetPermissions();
    perms.SharedAccessPolicies.Clear();
    container.SetPermissions(perms);

    //Create a new access policy on the container, which may be optionally used to provide constraints for
    //shared access signatures on the container and the blob.
    string sharedAccessPolicyName = "tutorialpolicy";
    CreateSharedAccessPolicy(blobClient, container, sharedAccessPolicyName);

    //Generate a SAS URI for the container, using a stored access policy to set constraints on the SAS.
    Console.WriteLine("Container SAS URI using stored access policy: " + GetContainerSasUriWithPolicy(container, sharedAccessPolicyName));
    Console.WriteLine();

    //Generate a SAS URI for a blob within the container, using a stored access policy to set constraints on the SAS.
    Console.WriteLine("Blob SAS URI using stored access policy: " + GetBlobSasUriWithPolicy(container, sharedAccessPolicyName));
    Console.WriteLine();

    Console.ReadLine();
}
```

Ao executar o aplicativo de console GenerateSharedAccessSignatures, você verá uma saída semelhante à seguinte. Essas são as assinaturas de acesso compartilhado usadas na Parte 2 do tutorial.

```
Container SAS URI: https://storagesample.blob.core.windows.net/sascontainer?sv=2016-05-31&sr=c&sig=pFlEZD%2F6sJTNLxD%2FQ26Hh85j%2FzYPxZav6mP1KJwnvJE%3D&se=2017-05-16T16%3A16%3A47Z&sp=wl

Blob SAS URI: https://storagesample.blob.core.windows.net/sascontainer/sasblob.txt?sv=2016-05-31&sr=b&sig=%2FiBWAZbXESzCMvRcm7JwJBK0gT0BtPSWEq4pRwmlBRI%3D&st=2017-05-15T16%3A11%3A48Z&se=2017-05-16T16%3A16%3A48Z&sp=rw

Container SAS URI using stored access policy: https://storagesample.blob.core.windows.net/sascontainer?sv=2016-05-31&sr=c&si=tutorialpolicy&sig=aMb6rKDvvpfiGVsZI2rCmyUra6ZPpq%2BZ%2FLyTgAeec%2Bk%3D

Blob SAS URI using stored access policy: https://storagesample.blob.core.windows.net/sascontainer/sasblobpolicy.txt?sv=2016-05-31&sr=b&si=tutorialpolicy&sig=%2FkTWkT23SS45%2FoF4bK2mqXkN%2BPKs%2FyHuzkfQ4GFoZVU%3D
```

## <a name="part-2-create-a-console-application-to-test-the-shared-access-signatures"></a>Parte 2: Criar um aplicativo de console para testar as assinaturas de acesso compartilhado
Para testar as assinaturas de acesso compartilhado criadas nos exemplos anteriores, criaremos um segundo aplicativo de console que utiliza as assinaturas para executar operações no contêiner e em um blob.

> [!NOTE]
> Se mais de 24 horas tiverem decorrido desde que você concluiu a primeira parte do tutorial, as assinaturas geradas deixarão de ser válidas. Nesse caso, você deve executar o código no primeiro aplicativo de console para gerar assinaturas de acesso compartilhado atualizadas para uso na segunda parte do tutorial.
>

No Visual Studio, crie um novo aplicativo de console do Windows e dê a ele o nome **ConsumeSharedAccessSignatures**. Adicione referências a [Microsoft.WindowsAzure.ConfigurationManager](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager) e [WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage/), como fez anteriormente.

Na parte superior do arquivo Program.cs, adicione as seguintes diretivas **using** :

```csharp
using System.IO;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
```

No corpo do método **Main()** , adicione as seguintes constantes de cadeia de caracteres, alterando seus valores nas assinaturas de acesso compartilhado geradas na parte 1 do tutorial.

```csharp
static void Main(string[] args)
{
    const string containerSAS = "<your container SAS>";
    const string blobSAS = "<your blob SAS>";
    const string containerSASWithAccessPolicy = "<your container SAS with access policy>";
    const string blobSASWithAccessPolicy = "<your blob SAS with access policy>";
}
```

### <a name="add-a-method-to-try-container-operations-using-a-shared-access-signature"></a>Adicionar um método para testar operações de contêiner usando uma assinatura de acesso compartilhado
Em seguida, adicionaremos um método que testará algumas operações de contêiner usando uma assinatura de acesso compartilhado no contêiner. A assinatura de acesso compartilhado é usada para retornar uma referência ao contêiner, autenticando o acesso ao contêiner com base na assinatura sozinha.

Adicione o seguinte método ao Program.cs:

```csharp
static void UseContainerSAS(string sas)
{
    //Try performing container operations with the SAS provided.

    //Return a reference to the container using the SAS URI.
    CloudBlobContainer container = new CloudBlobContainer(new Uri(sas));

    //Create a list to store blob URIs returned by a listing operation on the container.
    List<ICloudBlob> blobList = new List<ICloudBlob>();

    //Write operation: write a new blob to the container.
    try
    {
        CloudBlockBlob blob = container.GetBlockBlobReference("blobCreatedViaSAS.txt");
        string blobContent = "This blob was created with a shared access signature granting write permissions to the container. ";
        blob.UploadText(blobContent);

        Console.WriteLine("Write operation succeeded for SAS " + sas);
        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine("Write operation failed for SAS " + sas);
        Console.WriteLine("Additional error information: " + e.Message);
        Console.WriteLine();
    }

    //List operation: List the blobs in the container.
    try
    {
        foreach (ICloudBlob blob in container.ListBlobs())
        {
            blobList.Add(blob);
        }
        Console.WriteLine("List operation succeeded for SAS " + sas);
        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine("List operation failed for SAS " + sas);
        Console.WriteLine("Additional error information: " + e.Message);
        Console.WriteLine();
    }

    //Read operation: Get a reference to one of the blobs in the container and read it.
    try
    {
        CloudBlockBlob blob = container.GetBlockBlobReference(blobList[0].Name);
        MemoryStream msRead = new MemoryStream();
        msRead.Position = 0;
        using (msRead)
        {
            blob.DownloadToStream(msRead);
            Console.WriteLine(msRead.Length);
        }
        Console.WriteLine("Read operation succeeded for SAS " + sas);
        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine("Read operation failed for SAS " + sas);
        Console.WriteLine("Additional error information: " + e.Message);
        Console.WriteLine();
    }
    Console.WriteLine();

    //Delete operation: Delete a blob in the container.
    try
    {
        CloudBlockBlob blob = container.GetBlockBlobReference(blobList[0].Name);
        blob.Delete();
        Console.WriteLine("Delete operation succeeded for SAS " + sas);
        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine("Delete operation failed for SAS " + sas);
        Console.WriteLine("Additional error information: " + e.Message);
        Console.WriteLine();
    }
}
```

Atualize o método **Main()** para chamar **UseContainerSAS()** com as duas assinaturas de acesso compartilhado que você criou no contêiner:

```csharp
static void Main(string[] args)
{
    string containerSAS = "<your container SAS>";
    string blobSAS = "<your blob SAS>";
    string containerSASWithAccessPolicy = "<your container SAS with access policy>";
    string blobSASWithAccessPolicy = "<your blob SAS with access policy>";

    //Call the test methods with the shared access signatures created on the container, with and without the access policy.
    UseContainerSAS(containerSAS);
    UseContainerSAS(containerSASWithAccessPolicy);

    Console.ReadLine();
}
```

### <a name="add-a-method-to-try-blob-operations-using-a-shared-access-signature"></a>Adicionar um método para testar operações de blob usando uma assinatura de acesso compartilhado
Finalmente, adicionaremos um método que testará algumas operações de blob usando uma assinatura de acesso compartilhado no blob. Nesse caso, usamos o construtor **CloudBlockBlob(String)**passando a assinatura de acesso compartilhado para retornar uma referência ao blob. Nenhuma outra autenticação é obrigatória; ela se baseia na assinatura sozinha.

Adicione o seguinte método ao Program.cs:

```csharp
static void UseBlobSAS(string sas)
{
    //Try performing blob operations using the SAS provided.

    //Return a reference to the blob using the SAS URI.
    CloudBlockBlob blob = new CloudBlockBlob(new Uri(sas));

    //Write operation: Write a new blob to the container.
    try
    {
        string blobContent = "This blob was created with a shared access signature granting write permissions to the blob. ";
        MemoryStream msWrite = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
        msWrite.Position = 0;
        using (msWrite)
        {
            blob.UploadFromStream(msWrite);
        }
        Console.WriteLine("Write operation succeeded for SAS " + sas);
        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine("Write operation failed for SAS " + sas);
        Console.WriteLine("Additional error information: " + e.Message);
        Console.WriteLine();
    }

    //Read operation: Read the contents of the blob.
    try
    {
        MemoryStream msRead = new MemoryStream();
        using (msRead)
        {
            blob.DownloadToStream(msRead);
            msRead.Position = 0;
            using (StreamReader reader = new StreamReader(msRead, true))
            {
                string line;
                while ((line = reader.ReadLine()) != null)
                {
                    Console.WriteLine(line);
                }
            }
        }
        Console.WriteLine("Read operation succeeded for SAS " + sas);
        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine("Read operation failed for SAS " + sas);
        Console.WriteLine("Additional error information: " + e.Message);
        Console.WriteLine();
    }

    //Delete operation: Delete the blob.
    try
    {
        blob.Delete();
        Console.WriteLine("Delete operation succeeded for SAS " + sas);
        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine("Delete operation failed for SAS " + sas);
        Console.WriteLine("Additional error information: " + e.Message);
        Console.WriteLine();
    }
}
```

Atualize o método **Main()** para chamar **UseBlobSAS()** com as duas assinaturas de acesso compartilhado que você criou no blob:

```csharp
static void Main(string[] args)
{
    string containerSAS = "<your container SAS>";
    string blobSAS = "<your blob SAS>";
    string containerSASWithAccessPolicy = "<your container SAS with access policy>";
    string blobSASWithAccessPolicy = "<your blob SAS with access policy>";

    //Call the test methods with the shared access signatures created on the container, with and without the access policy.
    UseContainerSAS(containerSAS);
    UseContainerSAS(containerSASWithAccessPolicy);

    //Call the test methods with the shared access signatures created on the blob, with and without the access policy.
    UseBlobSAS(blobSAS);
    UseBlobSAS(blobSASWithAccessPolicy);

    Console.ReadLine();
}
```

Execute o aplicativo de console e observe a saída para ver quais operações são permitidas para quais assinaturas. A saída na janela de console será semelhante a esta:

```
Write operation succeeded for SAS https://storagesample.blob.core.windows.net/sascontainer?sv=2016-05-31&sr=c&sig=32EaQGuFyDMb3yOAey3wq%2B%2FLwgPQxAgSo7UhzLdyIDU%3D&se=2017-05-16T15%3A41%3A20Z&sp=wl

List operation succeeded for SAS https://storagesample.blob.core.windows.net/sascontainer?sv=2016-05-31&sr=c&sig=32EaQGuFyDMb3yOAey3wq%2B%2FLwgPQxAgSo7UhzLdyIDU%3D&se=2017-05-16T15%3A41%3A20Z&sp=wl

Read operation failed for SAS https://storagesample.blob.core.windows.net/sascontainer?sv=2016-05-31&sr=c&sig=32EaQGuFyDMb3yOAey3wq%2B%2FLwgPQxAgSo7UhzLdyIDU%3D&se=2017-05-16T15%3A41%3A20Z&sp=wl
Additional error information: The remote server returned an error: (403) Forbidden.

Delete operation failed for SAS https://storagesample.blob.core.windows.net/sascontainer?sv=2016-05-31&sr=c&sig=32EaQGuFyDMb3yOAey3wq%2B%2FLwgPQxAgSo7UhzLdyIDU%3D&se=2017-05-16T15%3A41%3A20Z&sp=wl
Additional error information: The remote server returned an error: (403) Forbidden.

...
```

## <a name="next-steps"></a>Próximas etapas

* [Assinaturas de acesso compartilhado, Parte 1: Noções básicas sobre o modelo SAS](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Gerenciar o acesso de leitura anônimo aos contêineres e blobs](storage-manage-access-to-resources.md)
* [Delegar o acesso com uma assinatura de acesso compartilhado (API REST)](http://msdn.microsoft.com/library/azure/ee395415.aspx)
* [Introdução à Tabela e à Fila SAS](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx)
