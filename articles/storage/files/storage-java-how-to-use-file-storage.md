---
title: Desenvolvimento para o Arquivos do Azure com Java | Microsoft Docs
description: Saiba como desenvolver aplicativos e serviços Java que usam o Arquivos do Azure para armazenar dados de arquivo.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 11/18/2020
ms.custom: devx-track-java
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 25baa278961b93b04e60f2e997b98753cb6cf3ab
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/21/2020
ms.locfileid: "95024102"
---
# <a name="develop-for-azure-files-with-java"></a>Desenvolvimento para o Arquivos do Azure com Java

[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

Aprenda as noções básicas de desenvolvimento de aplicativos Java que usam os arquivos do Azure para armazenar dados. Crie um aplicativo de console e Aprenda as ações básicas usando as APIs de arquivos do Azure:

- Criar e excluir Compartilhamentos de Arquivos do Azure
- Criar e excluir diretórios
- Enumerar arquivos e diretórios em um Compartilhamento de Arquivos do Azure
- Carregar, baixar e excluir um arquivo

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

## <a name="create-a-java-application"></a>Criar um aplicativo Java

Para criar os exemplos, você precisará do JDK (Java Development Kit) e do [SDK do armazenamento do Azure para Java](https://github.com/azure/azure-sdk-for-java). Você também deverá ter criado uma conta de armazenamento do Azure.

## <a name="set-up-your-application-to-use-azure-files"></a>Configurar seu aplicativo para usar os Arquivos do Azure

Para usar as APIs de arquivos do Azure, adicione o seguinte código à parte superior do arquivo Java de onde você pretende acessar os arquivos do Azure.

# <a name="java-v12"></a>[Java V12](#tab/java)

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_ImportStatements":::

# <a name="java-v11"></a>[V11 Java](#tab/java11)

```java
// Include the following imports to use Azure Files APIs v11
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.file.*;
```

---

## <a name="set-up-an-azure-storage-connection-string"></a>Configurar uma cadeia de conexão de armazenamento do Azure

Para usar o Arquivos do Azure, será necessário se conectar à sua conta de armazenamento do Azure. Configure uma cadeia de conexão e use-a para se conectar à sua conta de armazenamento. Defina uma variável estática para conter a cadeia de conexão.

# <a name="java-v12"></a>[Java V12](#tab/java)

Substitua *\<storage_account_name\>* e *\<storage_account_key\>* pelos valores reais da sua conta de armazenamento.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_ConnectionString":::

# <a name="java-v11"></a>[V11 Java](#tab/java11)

Substitua *your_storage_account_name* e *your_storage_account_key* pelos valores reais da sua conta de armazenamento.

```java
// Configure the connection-string with your values
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account_name;" +
    "AccountKey=your_storage_account_key";
```

---

## <a name="access-azure-files-storage"></a>Acessar o armazenamento de arquivos do Azure

# <a name="java-v12"></a>[Java V12](#tab/java)

Para acessar os arquivos do Azure, crie um objeto [ShareClient](/java/api/com.azure.storage.file.share.shareclient) . Use a classe [ShareClientBuilder](/java/api/com.azure.storage.file.share.shareclientbuilder) para criar um novo objeto **ShareClient** .

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_createClient":::

# <a name="java-v11"></a>[V11 Java](#tab/java11)

Para acessar sua conta de armazenamento, use o objeto **CloudStorageAccount** , passando a cadeia de conexão para o método **Parse** .

```java
// Use the CloudStorageAccount object to connect to your storage account
try {
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);
} catch (InvalidKeyException invalidKey) {
    // Handle the exception
}
```

**CloudStorageAccount.parse** gera uma InvalidKeyException; portanto, você precisará colocá-lo dentro de um bloco try/catch.

---

## <a name="create-a-file-share"></a>Criar um compartilhamento de arquivo

Todos os arquivos e diretórios nos arquivos do Azure são armazenados em um contêiner chamado compartilhamento.

# <a name="java-v12"></a>[Java V12](#tab/java)

O método [ShareClient. Create](/java/api/com.azure.storage.file.share.shareclient.create) gera uma exceção se o compartilhamento já existe. Coloque a chamada para **criar** em um `try/catch` bloco e manipule a exceção.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_createFileShare":::

# <a name="java-v11"></a>[V11 Java](#tab/java11)

Para obter acesso a um compartilhamento e seu conteúdo, crie um cliente de arquivos do Azure.

```java
// Create the Azure Files client.
CloudFileClient fileClient = storageAccount.createCloudFileClient();
```

Usando o cliente do Arquivos do Azure, será possível obter uma referência a um compartilhamento.

```java
// Get a reference to the file share
CloudFileShare share = fileClient.getShareReference("sampleshare");
```

Para realmente criar o compartilhamento, use o método **createIfNotExists** do objeto **CloudFileShare** .

```java
if (share.createIfNotExists()) {
    System.out.println("New share created");
}
```

Neste ponto, o **compartilhamento** mantém uma referência a um compartilhamento chamado **compartilhamento de exemplo**.

---

## <a name="delete-a-file-share"></a>Excluir um compartilhamento de arquivos

O código de exemplo a seguir exclui um compartilhamento de arquivos.

# <a name="java-v12"></a>[Java V12](#tab/java)

Exclua um compartilhamento chamando o método [ShareClient. Delete](/java/api/com.azure.storage.file.share.shareclient.delete) .

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_deleteFileShare":::

# <a name="java-v11"></a>[V11 Java](#tab/java11)

Exclua um compartilhamento chamando o método **deleteIfExists** em um objeto **CloudFileShare** .

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

---

## <a name="create-a-directory"></a>Criar um diretório

Organize o armazenamento colocando arquivos dentro de subdiretórios, em vez de ter todos eles no diretório raiz.

# <a name="java-v12"></a>[Java V12](#tab/java)

O código a seguir cria um diretório chamando [ShareDirectoryClient. Create](/java/api/com.azure.storage.file.share.sharedirectoryclient.create). O método de exemplo retorna um `Boolean` valor que indica se ele criou com êxito o diretório.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_createDirectory":::

# <a name="java-v11"></a>[V11 Java](#tab/java11)

O código a seguir cria um subdiretório chamado **SampleDir** no diretório raiz.

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

---

## <a name="delete-a-directory"></a>Excluir um diretório

A exclusão de um diretório é uma tarefa simples. Não é possível excluir um diretório que ainda contenha arquivos ou subdiretórios.

# <a name="java-v12"></a>[Java V12](#tab/java)

O método [ShareDirectoryClient. Delete](/java/api/com.azure.storage.file.share.sharedirectoryclient.delete) gera uma exceção se o diretório não existe ou não está vazio. Coloque a chamada para **excluir** em um `try/catch` bloco e manipule a exceção.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_deleteDirectory":::

# <a name="java-v11"></a>[V11 Java](#tab/java11)

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

---

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Enumerar arquivos e diretórios em um Compartilhamento de Arquivos do Azure

# <a name="java-v12"></a>[Java V12](#tab/java)

Obtenha uma lista de arquivos e diretórios chamando [ShareDirectoryClient. listFilesAndDirectories](/java/api/com.azure.storage.file.share.sharedirectoryclient.listfilesanddirectories). O método retorna uma lista de objetos [ShareFileItem](/java/api/com.azure.storage.file.share.models.sharefileitem) nos quais você pode iterar. O código a seguir lista os arquivos e diretórios dentro do diretório especificado pelo parâmetro *dirname* .

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_enumerateFilesAndDirs":::

# <a name="java-v11"></a>[V11 Java](#tab/java11)

Obtenha uma lista de arquivos e diretórios chamando **listFilesAndDirectories** em uma referência de **CloudFileDirectory** . O método retorna uma lista de objetos **ListFileItem** nos quais você pode iterar. O código a seguir lista os arquivos e diretórios dentro do diretório raiz.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

for ( ListFileItem fileItem : rootDir.listFilesAndDirectories() ) {
    System.out.println(fileItem.getUri());
}
```

---

## <a name="upload-a-file"></a>Carregar um arquivo

Saiba como carregar um arquivo do armazenamento local.

# <a name="java-v12"></a>[Java V12](#tab/java)

O código a seguir carrega um arquivo local no armazenamento de arquivos do Azure chamando o método [ShareFileClient. Uploadfromfile](/java/api/com.azure.storage.file.share.sharefileclient.uploadfromfile) . O método de exemplo a seguir retorna um `Boolean` valor que indica se ele carregou com êxito o arquivo especificado.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_uploadFile":::

# <a name="java-v11"></a>[V11 Java](#tab/java11)

Obtenha uma referência para o diretório em que o arquivo será carregado chamando o método **getRootDirectoryReference** no objeto de compartilhamento.

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

---

## <a name="download-a-file"></a>Baixar um arquivo

Uma das operações mais frequentes é baixar arquivos do armazenamento de arquivos do Azure.

# <a name="java-v12"></a>[Java V12](#tab/java)

O exemplo a seguir baixa o arquivo especificado para o diretório local especificado no parâmetro *destDir* . O método de exemplo torna o nome de arquivo baixado exclusivo, predependendo da data e hora.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_downloadFile":::

# <a name="java-v11"></a>[V11 Java](#tab/java11)

O exemplo a seguir baixa SampleFile.txt e exibe seu conteúdo.

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

---

## <a name="delete-a-file"></a>Excluir um arquivo

Outra operação comum do Arquivos do Azure é a exclusão de arquivos.

# <a name="java-v12"></a>[Java V12](#tab/java)

O código a seguir exclui o arquivo especificado especificado. Primeiro, o exemplo cria um [ShareDirectoryClient](/java/api/com.azure.storage.file.share.sharedirectoryclient) com base no parâmetro *dirname* . Em seguida, o código obtém um [ShareFileClient](/java/api/com.azure.storage.file.share.sharefileclient) do cliente de diretório, com base no parâmetro *filename* . Por fim, o método de exemplo chama [ShareFileClient. Delete](/java/api/com.azure.storage.file.share.sharefileclient.delete) para excluir o arquivo.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_deleteFile":::

# <a name="java-v11"></a>[V11 Java](#tab/java11)

O código a seguir exclui um arquivo chamado SampleFile.txt armazenado em um diretório chamado **sampledir**.

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

---

## <a name="next-steps"></a>Próximas etapas

Se você quiser saber mais sobre outras APIs de armazenamento do Azure, siga estes links.

- [Azure para desenvolvedores Java](/azure/developer/java)
- [SDK do Azure para Java](https://github.com/azure/azure-sdk-for-java)
- [SDK do Azure para Android](https://github.com/azure/azure-sdk-for-android)
- [Referência da biblioteca de cliente do compartilhamento de arquivos do Azure para Java SDK](/java/api/overview/azure/storage-file-share-readme)
- [API REST de serviços de armazenamento do Azure](/rest/api/storageservices/)
- [Blog da equipe de Armazenamento do Azure](https://azure.microsoft.com/blog/topics/storage-backup-and-recovery/)
- [Transferir dados com o Utilitário da Linha de Comando AzCopy](../common/storage-use-azcopy-v10.md)
- [Solução de problemas de Arquivos do Azure – Windows](storage-troubleshoot-windows-file-connection-problems.md)
