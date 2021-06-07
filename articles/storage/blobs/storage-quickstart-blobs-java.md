---
title: 'Início rápido: Biblioteca do Armazenamento de Blobs do Azure v12 – Java'
description: Neste guia de início rápido, você aprenderá a usar a biblioteca de clientes do Armazenamento de Blobs do Azure versão 12 para Java a fim de criar um contêiner e um blob no Armazenamento de Blobs (objeto). Em seguida, você aprenderá como baixar o blob para seu computador local e como listar todos os blobs em um contêiner.
author: twooley
ms.custom: devx-track-java
ms.author: twooley
ms.date: 12/01/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: b6d42cf26bc128257aa5d0cd7ef01e8fe8749a51
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106280625"
---
# <a name="quickstart-manage-blobs-with-java-v12-sdk"></a>Início Rápido: Gerenciar blobs com o SDK do Java v12

Neste início rápido, você aprenderá a gerenciar blobs usando o Java. Os blobs são objetos que podem conter grandes quantidades de texto ou dados binários, incluindo imagens, documentos, mídia de streaming e dados de arquivos. Você carregará, baixará e listará os blobs e criará e excluirá contêineres.

Recursos adicionais:

* [Documentação de referência da API](/java/api/overview/azure/storage-blob-readme)
* [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-blob)
* [Pacote (Maven)](https://mvnrepository.com/artifact/com.azure/azure-storage-blob)
* [Amostras](../common/storage-samples-java.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-samples)

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Uma conta de armazenamento do Azure. [Criar uma conta de armazenamento](../common/storage-account-create.md).
- [JDK (Java Development Kit)](/java/azure/jdk/) versão 8 ou superior.
- [Apache Maven](https://maven.apache.org/download.cgi).

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="setting-up"></a>Configurando

Esta seção explica como preparar um projeto para funcionar com a biblioteca de clientes do Armazenamento de Blobs do Azure v12 para Java.

### <a name="create-the-project"></a>Criar o projeto

Crie um aplicativo Java chamado *blob-quickstart-v12*.

1. Em uma janela do console (como cmd, PowerShell ou Bash), use o Maven para criar um novo aplicativo do console com o nome *blob-quickstart-v12*. Digite o comando **mvn** a seguir para criar um "Olá, Mundo!" Projeto Java.

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    mvn archetype:generate `
        --define interactiveMode=n `
        --define groupId=com.blobs.quickstart `
        --define artifactId=blob-quickstart-v12 `
        --define archetypeArtifactId=maven-archetype-quickstart `
        --define archetypeVersion=1.4
    ```

    # <a name="bash"></a>[Bash](#tab/bash)

    ```bash
    mvn archetype:generate \
        --define interactiveMode=n \
        --define groupId=com.blobs.quickstart \
        --define artifactId=blob-quickstart-v12 \
        --define archetypeArtifactId=maven-archetype-quickstart \
        --define archetypeVersion=1.4
    ```

    ---

1. O resultado da geração do projeto deve ser algo similar a:

    ```console
    [INFO] Scanning for projects...
    [INFO]
    [INFO] ------------------< org.apache.maven:standalone-pom >-------------------
    [INFO] Building Maven Stub Project (No POM) 1
    [INFO] --------------------------------[ pom ]---------------------------------
    [INFO]
    [INFO] >>> maven-archetype-plugin:3.1.2:generate (default-cli) > generate-sources @ standalone-pom >>>
    [INFO]
    [INFO] <<< maven-archetype-plugin:3.1.2:generate (default-cli) < generate-sources @ standalone-pom <<<
    [INFO]
    [INFO]
    [INFO] --- maven-archetype-plugin:3.1.2:generate (default-cli) @ standalone-pom ---
    [INFO] Generating project in Batch mode
    [INFO] ----------------------------------------------------------------------------
    [INFO] Using following parameters for creating project from Archetype: maven-archetype-quickstart:1.4
    [INFO] ----------------------------------------------------------------------------
    [INFO] Parameter: groupId, Value: com.blobs.quickstart
    [INFO] Parameter: artifactId, Value: blob-quickstart-v12
    [INFO] Parameter: version, Value: 1.0-SNAPSHOT
    [INFO] Parameter: package, Value: com.blobs.quickstart
    [INFO] Parameter: packageInPathFormat, Value: com/blobs/quickstart
    [INFO] Parameter: version, Value: 1.0-SNAPSHOT
    [INFO] Parameter: package, Value: com.blobs.quickstart
    [INFO] Parameter: groupId, Value: com.blobs.quickstart
    [INFO] Parameter: artifactId, Value: blob-quickstart-v12
    [INFO] Project created from Archetype in dir: C:\QuickStarts\blob-quickstart-v12
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time:  7.056 s
    [INFO] Finished at: 2019-10-23T11:09:21-07:00
    [INFO] ------------------------------------------------------------------------
        ```

1. Switch to the newly created *blob-quickstart-v12* folder.

   ```console
   cd blob-quickstart-v12
   ```

1. No diretório *blob-quickstart-v12*, crie outro diretório chamado *dados*. É nele que os arquivos de dados de blob serão criados e armazenados.

    ```console
    mkdir data
    ```

### <a name="install-the-package"></a>Instalar o pacote

Abra o arquivo *pom.xml* no seu editor de texto. Adicione o seguinte elemento de dependência ao grupo de dependências.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-storage-blob</artifactId>
    <version>12.6.0</version>
</dependency>
```

### <a name="set-up-the-app-framework"></a>Configurar o framework de aplicativos

No diretório do projeto:

1. Navegue até o diretório */src/main/java/com/blobs/quickstart*
1. Abra o arquivo *App.java* em seu editor
1. Exclua a instrução `System.out.println("Hello world!");`
1. Adicione diretivas `import`

O código é o seguinte:

```java
package com.blobs.quickstart;

/**
 * Azure blob storage v12 SDK quickstart
 */
import com.azure.storage.blob.*;
import com.azure.storage.blob.models.*;
import java.io.*;

public class App
{
    public static void main( String[] args ) throws IOException
    {
    }
}
```

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Modelo de objeto

O Armazenamento de Blobs do Azure é otimizado para armazenar grandes quantidades de dados não estruturados. Dados não estruturados são dados que não estão de acordo com uma definição ou um modelo de dados específico, como texto ou dados binários. O Armazenamento de Blobs oferece três tipos de recursos:

* A conta de armazenamento
* Um contêiner na conta de armazenamento
* Um blob no contêiner

O diagrama a seguir mostra a relação entre esses recursos.

![Diagrama da arquitetura de Armazenamento de Blobs](./media/storage-blobs-introduction/blob1.png)

Use as seguintes classes Java para interagir com esses recursos:

* [BlobServiceClient](/java/api/com.azure.storage.blob.blobserviceclient): a classe `BlobServiceClient` permite manipular os recursos do Armazenamento do Azure e os contêineres do blob. A conta de armazenamento fornece o namespace de nível superior para o serviço Blob.
* a classe [ fornece uma API de construtor influente para ajudar na configuração e instanciação de objetos ](/java/api/com.azure.storage.blob.blobserviceclientbuilder).
* [BlobContainerClient](/java/api/com.azure.storage.blob.blobcontainerclient): a classe `BlobContainerClient` permite manipular os contêineres do Armazenamento do Azure e seus blobs.
* a classe [ permite manipular os blobs do Armazenamento do Azure.
* [BlobItem](/java/api/com.azure.storage.blob.models.blobitem): a classe `BlobItem` representa blobs individuais retornados de uma chamada para [listBlobs](/java/api/com.azure.storage.blob.blobcontainerclient.listblobs).

## <a name="code-examples"></a>Exemplos de código

Estes exemplos de snippets de código mostram como executar o seguinte com a biblioteca de clientes do Armazenamento de Blobs do Azure para Java:

* [Obter a cadeia de conexão](#get-the-connection-string)
* [Criar um contêiner](#create-a-container)
* [Carregar blobs em um contêiner](#upload-blobs-to-a-container)
* [Listar os blobs em um contêiner](#list-the-blobs-in-a-container)
* [Baixar blobs](#download-blobs)
* [Excluir um contêiner](#delete-a-container)

### <a name="get-the-connection-string"></a>Obtenha a cadeia de conexão

O código a seguir recupera a cadeia de conexão da conta de armazenamento a partir da variável de ambiente criada na seção [Configurar a cadeia de conexão do armazenamento](#configure-your-storage-connection-string).

Adicione esse código dentro do método `Main`:

```java
System.out.println("Azure Blob Storage v12 - Java quickstart sample\n");

// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable on the machine
// running the application called AZURE_STORAGE_CONNECTION_STRING. If the environment variable
// is created after the application is launched in a console or with
// Visual Studio, the shell or application needs to be closed and reloaded
// to take the environment variable into account.
String connectStr = System.getenv("AZURE_STORAGE_CONNECTION_STRING");
```

### <a name="create-a-container"></a>Criar um contêiner

Escolha um nome para o novo contêiner. O código abaixo anexa um valor de UUID ao nome do contêiner para garantir que ele seja exclusivo.

> [!IMPORTANT]
> Os nomes de contêiner devem estar em minúsculas. Para saber mais sobre como nomear contêineres e blobs, veja [Nomenclatura e referência de contêineres, blobs e metadados](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Em seguida, crie uma instância da classe [BlobContainerClient](/java/api/com.azure.storage.blob.blobcontainerclient) e chame o método [create](/java/api/com.azure.storage.blob.blobcontainerclient.create) para criar de fato o contêiner em sua conta de armazenamento.

Adicione este código ao final do método `Main`:

```java
// Create a BlobServiceClient object which will be used to create a container client
BlobServiceClient blobServiceClient = new BlobServiceClientBuilder().connectionString(connectStr).buildClient();

//Create a unique name for the container
String containerName = "quickstartblobs" + java.util.UUID.randomUUID();

// Create the container and return a container client object
BlobContainerClient containerClient = blobServiceClient.createBlobContainer(containerName);
```

### <a name="upload-blobs-to-a-container"></a>Carregar blobs em um contêiner

O trecho de código a seguir:

1. Cria um arquivo de texto no diretório de *dados* local.
1. Obtém uma referência para um objeto [BlobClient](/java/api/com.azure.storage.blob.blobclient) chamando o método [getBlobClient](/java/api/com.azure.storage.blob.blobcontainerclient.getblobclient) no contêiner da seção [Criar um contêiner](#create-a-container).
1. Carrega o arquivo de texto local para o blob chamando o método [uploadFromFile](/java/api/com.azure.storage.blob.blobclient.uploadfromfile). Esse método criará o blob se ele ainda não existir, mas não o substituirá se ele já existir.

Adicione este código ao final do método `Main`:

```java
// Create a local file in the ./data/ directory for uploading and downloading
String localPath = "./data/";
String fileName = "quickstart" + java.util.UUID.randomUUID() + ".txt";
File localFile = new File(localPath + fileName);

// Write text to the file
FileWriter writer = new FileWriter(localPath + fileName, true);
writer.write("Hello, World!");
writer.close();

// Get a reference to a blob
BlobClient blobClient = containerClient.getBlobClient(fileName);

System.out.println("\nUploading to Blob storage as blob:\n\t" + blobClient.getBlobUrl());

// Upload the blob
blobClient.uploadFromFile(localPath + fileName);
```

### <a name="list-the-blobs-in-a-container"></a>Listar os blobs em um contêiner

Lista os blobs no contêiner chamando o método [listBlobs](/java/api/com.azure.storage.blob.blobcontainerclient.listblobs). Nesse caso, apenas um blob foi adicionado ao contêiner, portanto, a operação de listagem retorna apenas esse blob.

Adicione este código ao final do método `Main`:

```java
System.out.println("\nListing blobs...");

// List the blob(s) in the container.
for (BlobItem blobItem : containerClient.listBlobs()) {
    System.out.println("\t" + blobItem.getName());
}
```

### <a name="download-blobs"></a>Baixar blobs

Baixe o blob criado anteriormente chamando o método [downloadToFile](/java/api/com.azure.storage.blob.specialized.blobclientbase.downloadtofile). O código de exemplo adiciona o sufixo "DOWNLOAD" ao nome do blob para que você possa ver os dois arquivos no sistema de arquivos local.

Adicione este código ao final do método `Main`:

```java
// Download the blob to a local file
// Append the string "DOWNLOAD" before the .txt extension so that you can see both files.
String downloadFileName = fileName.replace(".txt", "DOWNLOAD.txt");
File downloadedFile = new File(localPath + downloadFileName);

System.out.println("\nDownloading blob to\n\t " + localPath + downloadFileName);

blobClient.downloadToFile(localPath + downloadFileName);
```

### <a name="delete-a-container"></a>Excluir um contêiner

O código a seguir limpa os recursos que o aplicativo criou ao remover todo o contêiner usando o método [delete](/java/api/com.azure.storage.blob.blobcontainerclient.delete). Ele também exclui os arquivos locais criados pelo aplicativo.

O aplicativo pausa a entrada do usuário chamando `System.console().readLine()` antes de excluir o blob, o contêiner e os arquivos locais. Essa é uma boa oportunidade de verificar se os recursos foram criados corretamente antes de serem excluídos.

Adicione este código ao final do método `Main`:

```java
// Clean up
System.out.println("\nPress the Enter key to begin clean up");
System.console().readLine();

System.out.println("Deleting blob container...");
containerClient.delete();

System.out.println("Deleting the local source and downloaded files...");
localFile.delete();
downloadedFile.delete();

System.out.println("Done");
```

## <a name="run-the-code"></a>Executar o código

Este aplicativo cria um arquivo de teste na pasta local e o carrega no armazenamento de blobs. Em seguida, o exemplo lista os blobs no contêiner e baixa o arquivo com um novo nome para que você possa comparar os arquivos novos e antigos.

Navegue até o diretório que contém o arquivo *pom.xml* e compile o projeto usando o comando `mvn` a seguir.

```console
mvn compile
```

Em seguida, compile o pacote.

```console
mvn package
```

Execute o comando `mvn` a seguir para executar o aplicativo.

```console
mvn exec:java -Dexec.mainClass="com.blobs.quickstart.App" -Dexec.cleanupDaemonThreads=false
```

A saída do aplicativo é semelhante ao seguinte exemplo:

```output
Azure Blob Storage v12 - Java quickstart sample

Uploading to Blob storage as blob:
        https://mystorageacct.blob.core.windows.net/quickstartblobsf9aa68a5-260e-47e6-bea2-2dcfcfa1fd9a/quickstarta9c3a53e-ae9d-4863-8b34-f3d807992d65.txt

Listing blobs...
        quickstarta9c3a53e-ae9d-4863-8b34-f3d807992d65.txt

Downloading blob to
        ./data/quickstarta9c3a53e-ae9d-4863-8b34-f3d807992d65DOWNLOAD.txt

Press the Enter key to begin clean up

Deleting blob container...
Deleting the local source and downloaded files...
Done
```

Antes de iniciar o processo de limpeza, verifique se os dois arquivos estão na pasta *dados*. Você pode abri-los e ver se eles são idênticos.

Depois de verificar os arquivos, pressione a tecla **Enter** para excluir os arquivos de teste e concluir a demonstração.

## <a name="next-steps"></a>Próximas etapas

Neste início rápido você aprendeu a carregar, baixar e listar blobs usando Java.

Para ver os aplicativos de exemplo de armazenamento de blobs, acesse:

> [!div class="nextstepaction"]
> [Exemplos do SDK do Armazenamento de Blobs do Azure v12 para Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-blob/src/samples/java/com/azure/storage/blob)

* Para saber mais, confira o [SDK do Azure para Java](https://github.com/Azure/azure-sdk-for-java/blob/master/README.md).
* Para acessar tutoriais, exemplos, inícios rápidos e outras documentações, acesse o [Azure para Desenvolvedores de nuvem de Java](/azure/developer/java/).
