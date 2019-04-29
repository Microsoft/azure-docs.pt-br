---
title: Desenvolvimento para o Arquivos do Azure com Java | Microsoft Docs
description: Saiba como desenvolver aplicativos e serviços Java que usam o Arquivos do Azure para armazenar dados de arquivo.
services: storage
author: roygara
ms.service: storage
ms.devlang: Java
ms.topic: article
ms.date: 09/19/2017
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 238e5971e79b192e0ef422dcd452859ff7566580
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63763491"
---
# <a name="develop-for-azure-files-with-java"></a>Desenvolvimento para o Arquivos do Azure com Java
[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

## <a name="about-this-tutorial"></a>Sobre este tutorial
Este tutorial demonstrará as noções básicas de usar Java para desenvolver aplicativos ou serviços que usam o Arquivos do Azure para armazenar dados de arquivo. Neste tutorial, criaremos um aplicativo de console e mostraremos como executar ações básicas com arquivos Java e Azure:

* Criar e excluir Compartilhamentos de Arquivos do Azure
* Criar e excluir diretórios
* Enumerar arquivos e diretórios em um Compartilhamento de Arquivos do Azure
* Carregar, baixar e excluir um arquivo

> [!Note]  
> Como os Arquivos do Azure podem ser acessados por meio do SMB, é possível gravação de aplicativos que acessam o compartilhamento de arquivos do Azure usando as classes de E / S padrão do Java. Este artigo descreverá como criar aplicativos que usam o SDK do Java do Armazenamento do Azure, que usa a [API REST do Arquivos do Azure](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api) para se comunicar com o Arquivos do Azure.

## <a name="create-a-java-application"></a>Criar um aplicativo Java
Para construir as amostras, você precisará do Java Development Kit (JDK) e do [ Azure Storage SDK para Java ](https://github.com/Azure/azure-storage-java). Você também deverá ter criado uma conta de armazenamento do Azure.

## <a name="set-up-your-application-to-use-azure-files"></a>Configurar seu aplicativo para usar os Arquivos do Azure
Para usar as APIs de armazenamento do Azure, adicione a instrução a seguir à parte superior do arquivo Java do qual você pretende acessar o serviço de armazenamento.

```java
// Include the following imports to use blob APIs.
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.file.*;
```

## <a name="set-up-an-azure-storage-connection-string"></a>Configurar uma cadeia de conexão de armazenamento do Azure
Para usar o Arquivos do Azure, será necessário se conectar à sua conta de armazenamento do Azure. A primeira etapa é configurar uma cadeia de conexão que usaremos para nos conectar à sua conta de armazenamento. Vamos definir uma variável estática para fazer isso.

```java
// Configure the connection-string with your values
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account_name;" +
    "AccountKey=your_storage_account_key";
```

> [!NOTE]
> Substitua nome_da_sua_conta_de_armazenamento e chave_da_sua_conta_de_armazenamento pelos valores reais da sua conta de armazenamento.
> 
> 

## <a name="connecting-to-an-azure-storage-account"></a>Conectar-se a uma conta de armazenamento do Azure
Para se conectar à sua conta de armazenamento, você precisa usar o objeto **CloudStorageAccount**, passando uma cadeia de conexão para seu método de **análise**.

```java
// Use the CloudStorageAccount object to connect to your storage account
try {
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);
} catch (InvalidKeyException invalidKey) {
    // Handle the exception
}
```

**CloudStorageAccount.parse** gera uma InvalidKeyException; portanto, você precisará colocá-lo dentro de um bloco try/catch.

## <a name="create-an-azure-file-share"></a>Criar um compartilhamento de arquivos do Azure
Todos os arquivos e diretórios do Arquivos do Azure residem em um contêiner chamado **Compartilhamento**. Sua conta de armazenamento pode a quantidade de compartilhamentos que a capacidade da conta permitir. Para obter acesso a um compartilhamento e seu conteúdo, é necessário usar um cliente de Arquivos do Azure.

```java
// Create the Azure Files client.
CloudFileClient fileClient = storageAccount.createCloudFileClient();
```

Usando o cliente do Arquivos do Azure, será possível obter uma referência a um compartilhamento.

```java
// Get a reference to the file share
CloudFileShare share = fileClient.getShareReference("sampleshare");
```

Para realmente criar o compartilhamento, use o método **createIfNotExists** do objeto CloudFileShare.

```java
if (share.createIfNotExists()) {
    System.out.println("New share created");
}
```

Neste ponto, **compartilhar** contém uma referência a um compartilhamento chamado **sampleshare**.

## <a name="delete-an-azure-file-share"></a>Excluir um Compartilhamento de Arquivos do Azure
Para excluir um compartilhamento deve-se chamar o método **deleteIfExists** em um objeto CloudFileShare. O código fornecido a seguir é um exemplo de como fazer isso.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

    // Create the file client.
   CloudFileClient fileClient = storageAccount.createCloudFileClient();

   // Get a reference to the file share
   CloudFileShare share = fileClient.getShareReference("sampleshare");

   if (share.deleteIfExists()) {
       System.out.println("sampleshare deleted");
   }
} catch (Exception e) {
    e.printStackTrace();
}
```

## <a name="create-a-directory"></a>Criar um diretório
Você também pode organizar o armazenamento colocando arquivos em subdiretórios em vez de manter todos eles no diretório raiz. Os Arquivos do Azure permitem que você crie quantos diretórios a conta permitir. O código abaixo irá criar um subdiretório chamado **sampledir** sob o diretório raiz.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

//Get a reference to the sampledir directory
CloudFileDirectory sampleDir = rootDir.getDirectoryReference("sampledir");

if (sampleDir.createIfNotExists()) {
    System.out.println("sampledir created");
} else {
    System.out.println("sampledir already exists");
}
```

## <a name="delete-a-directory"></a>Excluir um diretório
A exclusão de um diretório é uma tarefa simples, embora deva ser observado que você não pode excluir um diretório que ainda contenha arquivos ou outros diretórios.

```java
// Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

// Get a reference to the directory you want to delete
CloudFileDirectory containerDir = rootDir.getDirectoryReference("sampledir");

// Delete the directory
if ( containerDir.deleteIfExists() ) {
    System.out.println("Directory deleted");
}
```

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Enumerar arquivos e diretórios em um Compartilhamento de Arquivos do Azure
A lista de arquivos e diretórios em um compartilhamento pode ser obtida facilmente chamando **listFilesAndDirectories** em uma referência CloudFileDirectory. O método retorna uma lista de objetos ListFileItem nos quais você pode iterar. Como exemplo, o código a seguir lista os arquivos e diretórios contidos no diretório raiz.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

for ( ListFileItem fileItem : rootDir.listFilesAndDirectories() ) {
    System.out.println(fileItem.getUri());
}
```

## <a name="upload-a-file"></a>Carregar um arquivo
Nesta seção, você aprenderá a carregar um arquivo do armazenamento local para o diretório raiz de um compartilhamento.

A primeira etapa do carregamento de um arquivo é obter uma referência para o diretório onde ele deve residir. Para isso, você deve chamar o método **getRootDirectoryReference** do objeto de compartilhamento.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();
```

Agora que você tem uma referência para o diretório raiz do compartilhamento, pode carregar um arquivo usando o código a seguir.

```java
        // Define the path to a local file.
        final String filePath = "C:\\temp\\Readme.txt";
    
        CloudFile cloudFile = rootDir.getFileReference("Readme.txt");
        cloudFile.uploadFromFile(filePath);
```

## <a name="download-a-file"></a>Baixar um arquivo
Uma das operações mais frequentes executadas no Arquivos do Azure é baixar arquivos. No exemplo a seguir, o código baixa o arquivo SampleFile.txt e exibe seu conteúdo.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

//Get a reference to the directory that contains the file
CloudFileDirectory sampleDir = rootDir.getDirectoryReference("sampledir");

//Get a reference to the file you want to download
CloudFile file = sampleDir.getFileReference("SampleFile.txt");

//Write the contents of the file to the console.
System.out.println(file.downloadText());
```

## <a name="delete-a-file"></a>Excluir um arquivo
Outra operação comum do Arquivos do Azure é a exclusão de arquivos. O código a seguir exclui um arquivo chamado SampleFile.txt armazenado em um diretório chamado **sampledir**.

```java
// Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

// Get a reference to the directory where the file to be deleted is in
CloudFileDirectory containerDir = rootDir.getDirectoryReference("sampledir");

String filename = "SampleFile.txt"
CloudFile file;

file = containerDir.getFileReference(filename)
if ( file.deleteIfExists() ) {
    System.out.println(filename + " was deleted");
}
```

## <a name="next-steps"></a>Próximas etapas
Se você quiser saber mais sobre outras APIs de armazenamento do Azure, siga estes links.

* [Azure para desenvolvedores Java](/java/azure)/)
* [SDK de Armazenamento do Azure para Java](https://github.com/azure/azure-storage-java)
* [SDK de Armazenamento do Azure para Android](https://github.com/azure/azure-storage-android)
* [Referência de SDK do Cliente de Armazenamento do Azure](http://dl.windowsazure.com/storage/javadoc/)
* [API REST de serviços de armazenamento do Azure](https://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Blog da equipe de Armazenamento do Azure](https://blogs.msdn.com/b/windowsazurestorage/)
* [Transferir dados com o Utilitário da Linha de Comando AzCopy](../common/storage-use-azcopy.md)
* [Solução de problemas de Arquivos do Azure – Windows](storage-troubleshoot-windows-file-connection-problems.md)
