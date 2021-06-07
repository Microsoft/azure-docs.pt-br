---
title: 'Início Rápido: Biblioteca de clientes do Armazenamento de blobs do Azure v8 para Java'
description: Crie uma conta de armazenamento e um contêiner no armazenamento de objeto (Blob). Em seguida, use a biblioteca cliente do Armazenamento do Azure v8 para o Java para carregar um blob no Armazenamento do Azure, fazer o download de um blob e listar os blobs em um contêiner.
author: twooley
ms.custom: devx-track-java
ms.author: twooley
ms.date: 01/19/2021
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: 49c55a2b565100e370ce561537c96a96b896f355
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106280591"
---
# <a name="quickstart-manage-blobs-with-java-v8-sdk"></a>Início Rápido: Gerenciar blobs com o SDK do Java v8

Neste início rápido, você aprenderá a gerenciar blobs usando o Java. Os blobs são objetos que podem conter grandes quantidades de texto ou dados binários, incluindo imagens, documentos, mídia de streaming e dados de arquivos. Você vai carregar, baixar e listar blobs. Você também criará e excluirá os contêineres, além de definir permissões neles.

> [!NOTE]
> Este guia de início rápido usa uma versão herdada da biblioteca de clientes do Armazenamento de Blobs do Azure. Para começar a usar a última versão, confira [Guia de Início Rápido: Gerenciar blobs com o SDK do Java v12](storage-quickstart-blobs-java.md).

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Uma conta de armazenamento do Azure. [Criar uma conta de armazenamento](../common/storage-account-create.md).
- Um IDE que tenha integração com o Maven. Este tutorial usa [Eclipse](https://www.eclipse.org/downloads/) com a configuração "Eclipse IDE para desenvolvedores de Java".

## <a name="download-the-sample-application"></a>Baixar o aplicativo de exemplo

O [aplicativo de exemplo](https://github.com/Azure-Samples/storage-blobs-java-quickstart) é um aplicativo de console básico.

Use o [git](https://git-scm.com/) para baixar uma cópia do aplicativo para seu ambiente de desenvolvimento.

```bash
git clone https://github.com/Azure-Samples/storage-blobs-java-quickstart.git
```

Este comando clona o repositório para sua pasta do git local. Para abrir o projeto, inicialize o Eclipse e feche a tela de boas-vindas. Selecione **Arquivo** e **Abrir Projetos no Sistema de Arquivos**. Certifique-se de que **Detectar e configurar natureza do projeto** esteja marcado. Selecione **Directory** e navegue até onde você armazenou o repositório clonado. Dentro do repositório clonado, selecione a pasta **blobAzureApp**. Verifique se o projeto **javaBlobsQuickstarts** aparece como um projeto do Eclipse e selecione **Concluir**.

Depois que o projeto concluir a importação, abra **AzureApp.java** (localizado em **blobQuickstart.blobAzureApp** dentro de **src/main/java**) e substitua `accountname` e `accountkey` dentro da cadeia de caracteres `storageConnectionString`. Em seguida, execute o aplicativo. As instruções específicas para concluir essas tarefas estão descritas nas seções a seguir.

[!INCLUDE [storage-copy-connection-string-portal](../../../includes/storage-copy-connection-string-portal.md)]

## <a name="configure-your-storage-connection-string"></a>Configurar a cadeia de conexão de armazenamento

No aplicativo, você deve fornecer a cadeia de conexão para sua conta de armazenamento. Abra o arquivo **AzureApp.Java**. Localize a variável `storageConnectionString` e cole o valor da cadeia de conexão copiada na seção anterior. A variável `storageConnectionString` deve ser semelhante ao seguinte código de exemplo:

```java
public static final String storageConnectionString =
"DefaultEndpointsProtocol=https;" +
"AccountName=<account-name>;" +
"AccountKey=<account-key>";
```

## <a name="run-the-sample"></a>Execute o exemplo

Esse aplicativo de exemplo cria um arquivo de teste no seu diretório padrão (*C:\Users\<user>\AppData\Local\Temp*, para usuários do Windows), carrega-o para o Armazenamento de blobs, lista os blobs no contêiner e baixa o arquivo com um novo nome para que você possa comparar os arquivos novos com os antigos.

Execute o exemplo usando o Maven na linha de comando. Abra um shell e navegue até **blobAzureApp** dentro de seu diretório clonado. Em seguida, digite `mvn compile exec:java`.

O exemplo a seguir mostra a saída que seria gerada se você executasse o aplicativo no Windows.

```
Azure Blob storage quick start sample
Creating container: quickstartcontainer
Creating a sample file at: C:\Users\<user>\AppData\Local\Temp\sampleFile514658495642546986.txt
Uploading the sample file
URI of blob is: https://myexamplesacct.blob.core.windows.net/quickstartcontainer/sampleFile514658495642546986.txt
The program has completed successfully.
Press the 'Enter' key while in the console to delete the sample files, example container, and exit the application.

Deleting the container
Deleting the source, and downloaded files
```

Antes de continuar, procure o arquivo de exemplo no diretório padrão (*C:\Users\<user>\AppData\Local\Temp*, para usuários do Windows). Copie a URL para o blob fora da janela do console e cole-a em um navegador para exibir o conteúdo do arquivo no Armazenamento de Blobs. Se você comparar o arquivo de exemplo no diretório com o conteúdo armazenado no armazenamento de blobs, verá que eles são os mesmos.

  >[!NOTE]
  >Você também pode usar uma ferramenta como o [Gerenciador de Armazenamento do Azure](https://storageexplorer.com/?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) para exibir os arquivos no Armazenamento de Blobs. O Gerenciador de Armazenamento do Azure é uma ferramenta gratuita de multiplataforma que permite que você acesse as informações da sua conta de armazenamento.

Depois de verificar os arquivos, pressione a tecla **Enter** para concluir a demonstração e excluir os arquivos de teste. Agora que você sabe o que o exemplo faz, abra o arquivo **AzureApp.java** para examinar o código.

## <a name="understand-the-sample-code"></a>Entender o código de exemplo

Em seguida, vamos percorrer o código de exemplo para que você possa entender como ele funciona.

### <a name="get-references-to-the-storage-objects"></a>Obter referências a objetos de armazenamento

A primeira coisa a fazer é criar as referências aos objetos usados para acessar e gerenciar o Armazenamento de Blobs. Esses objetos dependem uns dos outros, cada um é usado pelo próximo na lista.

* Crie uma instância do objeto [CloudStorageAccount](/java/api/com.microsoft.azure.management.storage.storageaccount) apontando para a conta de armazenamento.

    O objeto **CloudStorageAccount** é uma representação de sua conta de armazenamento e permite definir e acessar propriedades da conta de armazenamento de forma programática. Usando o objeto **CloudStorageAccount**, você pode criar uma instância do **CloudBlobClient**, que é necessário para acessar o serviço blob.

* Crie uma instância do objeto **CloudBlobClient**, que aponta para o [serviço Blob](/java/api/com.microsoft.azure.storage.blob.cloudblobclient) na sua conta de armazenamento.

    O **CloudBlobClient** fornece um ponto de acesso ao serviço Blob, permitindo que você defina e acesse as propriedades do Armazenamento de Blobs de forma programática. Usando o **CloudBlobClient** você pode criar uma instância do objeto **CloudBlobContainer**, que é necessário para criar contêineres.

* Crie uma instância do objeto [CloudBlobContainer](/java/api/com.microsoft.azure.storage.blob.cloudblobcontainer) que representa o contêiner que você está acessando. Use contêineres para organizar seus blobs da mesma forma que você usa pastas no computador para organizar arquivos.

    Assim que tiver o **CloudBlobContainer**, será possível criar uma instância do objeto [CloudBlockBlob](/java/api/com.microsoft.azure.storage.blob.cloudblockblob), que aponta para o blob específico no qual você está interessado, e realizar a operação de upload, download, cópia ou outra.

> [!IMPORTANT]
> Os nomes de contêiner devem estar em minúsculas. Para saber mais sobre contêineres, veja [Nomenclatura e referência de contêineres, blobs e metadados](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

### <a name="create-a-container"></a>Criar um contêiner

Nesta seção, você cria uma instância de objetos, cria um novo contêiner e, em seguida, define as permissões no contêiner para que os blobs sejam públicos e possam ser acessados com apenas uma URL. O contêiner é chamado de **quickstartcontainer**.

Este exemplo usa [CreateIfNotExists](/java/api/com.microsoft.azure.storage.blob.cloudblobcontainer.createifnotexists) porque queremos criar um novo contêiner cada vez que a amostra for executada. Em um ambiente de produção em que você usa o mesmo contêiner em um aplicativo, é uma prática recomendada chamar **CreateIfNotExists** somente uma vez. Como alternativa, é possível criar o contêiner antecipadamente para não precisar criá-lo no código.

```java
// Parse the connection string and create a blob client to interact with Blob storage
storageAccount = CloudStorageAccount.parse(storageConnectionString);
blobClient = storageAccount.createCloudBlobClient();
container = blobClient.getContainerReference("quickstartcontainer");

// Create the container if it does not exist with public access.
System.out.println("Creating container: " + container.getName());
container.createIfNotExists(BlobContainerPublicAccessType.CONTAINER, new BlobRequestOptions(), new OperationContext());
```

### <a name="upload-blobs-to-the-container"></a>Carregar blobs para o contêiner

Para carregar um arquivo a um blob de blocos, obtenha a referência ao blob no contêiner de destino. Após obter a referência de blob, você pode carregar dados nele usando [CloudBlockBlob.Upload](/java/api/com.microsoft.azure.storage.blob.cloudblockblob.upload). Essa operação cria o blob, se ele ainda não existir, ou o substitui, se ele já existir.

O código de exemplo cria um arquivo local a ser usado para o upload e para o download, armazenando o arquivo a ser carregado como **origem** e o nome do blob no **blob**. O exemplo a seguir carrega o arquivo no seu contêiner chamado **quickstartcontainer**.

```java
//Creating a sample file
sourceFile = File.createTempFile("sampleFile", ".txt");
System.out.println("Creating a sample file at: " + sourceFile.toString());
Writer output = new BufferedWriter(new FileWriter(sourceFile));
output.write("Hello Azure!");
output.close();

//Getting a blob reference
CloudBlockBlob blob = container.getBlockBlobReference(sourceFile.getName());

//Creating blob and uploading file to it
System.out.println("Uploading the sample file ");
blob.uploadFromFile(sourceFile.getAbsolutePath());
```

Existem vários métodos `upload`, incluindo [upload](/java/api/com.microsoft.azure.storage.blob.cloudblockblob.upload), [uploadBlock](/java/api/com.microsoft.azure.storage.blob.cloudblockblob.uploadblock), [uploadFullBlob](/java/api/com.microsoft.azure.storage.blob.cloudblockblob.uploadfullblob), [uploadStandardBlobTier](/java/api/com.microsoft.azure.storage.blob.cloudblockblob.uploadstandardblobtier) e [uploadText](/java/api/com.microsoft.azure.storage.blob.cloudblockblob.uploadtext), que você pode usar com o Armazenamento de Blobs. Por exemplo, caso você tenha uma cadeia de caracteres, use o método `UploadText`, em vez do método `Upload`.

Blobs de bloco podem ser qualquer tipo de arquivo binário ou de texto. Os blobs de páginas são usados principalmente para os arquivos VHD que auxiliam as VMs IaaS. Use os blobs de acréscimo para registro em log, como quando você quer gravar em um arquivo e depois adicionar mais informações. A maioria dos objetos armazenados no Armazenamento de Blobs são blobs de blocos.

### <a name="list-the-blobs-in-a-container"></a>Listar os blobs em um contêiner

Você pode obter uma lista de arquivos no contêiner usando [CloudBlobContainer.ListBlobs](/java/api/com.microsoft.azure.storage.blob.cloudblobcontainer.listblobs). O código a seguir recupera a lista de blobs e a percorre, mostrando os URIs dos blobs encontrados. Você pode copiar o URI da janela de comando e colá-lo em um navegador para exibir o arquivo.

```java
//Listing contents of container
for (ListBlobItem blobItem : container.listBlobs()) {
    System.out.println("URI of blob is: " + blobItem.getUri());
}
```

### <a name="download-blobs"></a>Baixar blobs

Faça download dos blobs para o disco local usando [CloudBlob.DownloadToFile](/java/api/com.microsoft.azure.storage.blob.cloudblob.downloadtofile).

O código a seguir baixa o blob carregado em uma seção anterior, adicionando um sufixo de "_DOWNLOADED" ao nome do blob para que você possa ver ambos os arquivos no disco local.

```java
// Download blob. In most cases, you would have to retrieve the reference
// to cloudBlockBlob here. However, we created that reference earlier, and
// haven't changed the blob we're interested in, so we can reuse it.
// Here we are creating a new file to download to. Alternatively you can also pass in the path as a string into downloadToFile method: blob.downloadToFile("/path/to/new/file").
downloadedFile = new File(sourceFile.getParentFile(), "downloadedFile.txt");
blob.downloadToFile(downloadedFile.getAbsolutePath());
```

### <a name="clean-up-resources"></a>Limpar os recursos

Se você não precisar mais dos blobs carregados, você poderá excluir o contêiner inteiro usando [CloudBlobContainer.DeleteIfExists](/java/api/com.microsoft.azure.storage.blob.cloudblobcontainer.deleteifexists). Esse método também exclui os arquivos no contêiner.

```java
try {
if(container != null)
    container.deleteIfExists();
} catch (StorageException ex) {
System.out.println(String.format("Service error. Http code: %d and error code: %s", ex.getHttpStatusCode(), ex.getErrorCode()));
}

System.out.println("Deleting the source, and downloaded files");

if(downloadedFile != null)
downloadedFile.deleteOnExit();

if(sourceFile != null)
sourceFile.deleteOnExit();
```

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a transferir arquivos entre um disco local e o Armazenamento de Blobs do Azure usando Java. Para saber mais sobre como trabalhar com Java, prossiga para o nosso repositório de código de origem do GitHub.

> [!div class="nextstepaction"]
> [Referência de API do Java](/java/api/overview/azure/storage?view=azure-java-legacy&preserve-view=true)
> [Exemplos de código para Java](../common/storage-samples-java.md)