---
title: 'Início Rápido: usar o SDK de Armazenamento do Java para criar um blob'
description: Neste início rápido, você vai criar um contêiner no armazenamento de objetos (Azure Blob), carregar um arquivo, listar objetos e baixá-los usando o SDK de Armazenamento de Java.
services: storage
author: mhopkins-msft
ms.custom: mvc, seo-java-july2019
ms.service: storage
ms.topic: quickstart
ms.date: 11/14/2018
ms.author: mhopkins
ms.reviewer: seguler
ms.openlocfilehash: 12507ba87148bef6378542feb4ebde3b1c291a72
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68565919"
---
# <a name="quickstart-upload-download-and-list-blobs-by-using-the-java-storage-sdk-v10"></a>Início Rápido: Carregar, baixar e listar blobs usando o SDK de Armazenamento de Java V10

Neste início rápido, você aprende a usar o novo SDK de Armazenamento de Java para carregar, baixar e listar blobs de blocos em um contêiner no Armazenamento de Blobs do Azure. O novo SDK de Java utiliza o modelo de programação reativa com RxJava, que oferece operações assíncronas. Saiba mais sobre [extensões reativas para a VM Java](https://github.com/ReactiveX/RxJava) de RxJava. 

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

Verifique se você tem os pré-requisitos adicionais a seguir instalados:

* [Maven](https://maven.apache.org/download.cgi) para trabalhar na linha de comando ou qualquer ambiente de desenvolvimento Java integrado que você preferir.
* [JDK](https://aka.ms/azure-jdks)

## <a name="download-the-sample-application"></a>Baixar o aplicativo de exemplo

O [aplicativo de exemplo](https://github.com/Azure-Samples/storage-blobs-java-v10-quickstart) usado neste guia de início rápido é um aplicativo de console básico. 

Use o [git](https://git-scm.com/) para baixar uma cópia do aplicativo para seu ambiente de desenvolvimento.

```bash
git clone https://github.com/Azure-Samples/storage-blobs-java-v10-quickstart.git
```

Este comando clona o repositório para sua pasta do git local.

Após o projeto ser importado, abra **Quickstart.java** localizado em **src/main/java/quickstart**.

[!INCLUDE [storage-copy-account-key-portal](../../../includes/storage-copy-account-key-portal.md)]

## <a name="configure-your-storage-connection-string"></a>Configurar a cadeia de conexão de armazenamento
Essa solução requer que você armazene o nome e a chave da conta de armazenamento com segurança. Armazene-as em variáveis de ambiente locais no computador que executa o exemplo. Siga o exemplo de Linux ou Windows, dependendo do sistema operacional, para criar as variáveis de ambiente.

### <a name="linux-example"></a>Exemplo do Linux

```bash
export AZURE_STORAGE_ACCOUNT="<youraccountname>"
export AZURE_STORAGE_ACCESS_KEY="<youraccountkey>"
```

### <a name="windows-example"></a>Exemplo do Windows

```CMD
setx AZURE_STORAGE_ACCOUNT "<youraccountname>"
setx AZURE_STORAGE_ACCESS_KEY "<youraccountkey>"
```

## <a name="run-the-sample"></a>Execute o exemplo

Este exemplo cria um arquivo de teste no diretório padrão **AppData\Local\Temp** para os usuários do Windows. Em seguida, ele solicita que você execute as seguintes etapas:

1. Digite os comandos para carregar o arquivo de teste no armazenamento de blobs do Azure.
2. Liste os blobs no contêiner.
3. Baixe o arquivo carregado com um novo nome para que você possa comparar os arquivos novos e antigos. 

Se você deseja executar o exemplo usando Maven na linha de comando, abra um shell e navegue até **storage-blobs-java-v10-quickstart** dentro do diretório clonado. Em seguida, digite `mvn compile exec:java`.

Este exemplo mostra a saída se você executa o aplicativo no Windows.

```Output
Created quickstart container
Enter a command
(P)utBlob | (L)istBlobs | (G)etBlob | (D)eleteBlobs | (E)xitSample
# Enter a command :
P
Uploading the sample file into the container: https://<storageaccount>.blob.core.windows.net/quickstart
# Enter a command :
L
Listing blobs in the container: https://<storageaccount>.blob.core.windows.net/quickstart
Blob name: SampleBlob.txt
# Enter a command :
G
Get the blob: https://<storageaccount>.blob.core.windows.net/quickstart/SampleBlob.txt
The blob was downloaded to C:\Users\<useraccount>\AppData\Local\Temp\downloadedFile13097087873115855761.txt
# Enter a command :
D
Delete the blob: https://<storageaccount>.blob.core.windows.net/quickstart/SampleBlob.txt

# Enter a command :
>> Blob deleted: https://<storageaccount>.blob.core.windows.net/quickstart/SampleBlob.txt
E
Cleaning up the sample and exiting!
```

Você controla o exemplo e, portanto, insira os comandos para executar o código. As entradas diferenciam minúsculas e maiúsculas.

Você também pode usar uma ferramenta como o [Gerenciador de Armazenamento do Azure](https://storageexplorer.com/?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) para exibir os arquivos no Armazenamento de Blobs. O Gerenciador de Armazenamento do Azure é uma ferramenta gratuita de multiplataforma que dá acesso às informações da conta de armazenamento. 

Verifique os arquivos. Em seguida, selecione **E** e selecione **Enter** para concluir a demonstração e excluir os arquivos de teste. Agora que você sabe o que o exemplo faz, abra o arquivo **Quickstart.java** para ver o código. 

## <a name="understand-the-sample-code"></a>Entender o código de exemplo

As seções a seguir percorrem o código de exemplo para que você possa entender como ele funciona.

### <a name="get-references-to-the-storage-objects"></a>Obter referências a objetos de armazenamento

A primeira coisa a fazer é criar as referências aos objetos usados para acessar e gerenciar o Armazenamento de Blobs. Esses objetos dependem uns dos outros. Cada um é usado pelo próximo na lista.

1. Crie uma instância do objeto **StorageURL** que aponta para a conta de armazenamento.

    * O objeto [StorageURL](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._storage_u_r_l?view=azure-java-stable) é uma representação da conta de armazenamento. Você pode usá-lo para gerar um novo pipeline. 
    * Um pipeline é um conjunto de políticas que são usadas para manipular solicitações e respostas com mecanismos de autorização, registro em log e repetição. Para obter mais informações, consulte [Pipeline HTTP](https://github.com/Azure/azure-storage-java/wiki/Azure-Storage-Java-V10-Overview#url-types--http-pipeline).  
    * Usando o pipeline, crie uma instância do objeto [ServiceURL](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._service_u_r_l?view=azure-java-stable).
    * Usando o objeto **ServiceURL**, crie uma instância de [ContainerURL](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._container_u_r_l?view=azure-java-stable).
    * O **ContainerURL** é necessário para executar operações em contêineres de blob.

2. Crie uma instância do objeto **ContainerURL** que representa o contêiner que você está acessando. Contêineres organizam seus blobs da mesma forma que um computador organiza arquivos.

    * O **ContainerURL** fornece um ponto de acesso ao serviço de contêiner. 
    * Você pode criar uma instância do objeto [BlobURL](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._blob_u_r_l?view=azure-java-stable) usando [ContainerURL](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._container_u_r_l?view=azure-java-stable).
    * O **BlobURL** é necessário para a criação de blobs.

3. Crie uma instância do objeto **BlobURL** que aponta para o blob específico no qual você está interessado. 

> [!IMPORTANT]
> Os nomes de contêiner devem estar em minúsculas. Para obter mais informações sobre nomes de contêiner e de blobs, consulte [Nomeando e referenciando contêineres, blobs e metadados](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

### <a name="create-a-container"></a>Criar um contêiner 

Nesta seção, você cria uma instância de **ContainerURL**. Com ela, você cria um novo contêiner. O contêiner do exemplo é chamado de **quickstart**. 

Este exemplo usa [ContainerURL.create](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._container_u_r_l.create?view=azure-java-stable#com_microsoft_azure_storage_blob__container_u_r_l_create_Metadata_PublicAccessType_Context_) para você poder criar um novo contêiner cada vez que o exemplo for executado. Você também pode criar o contêiner antecipadamente para não precisar criá-lo no código.

```java
// Create a ServiceURL to call the Blob service. We will also use this to construct the ContainerURL
SharedKeyCredentials creds = new SharedKeyCredentials(accountName, accountKey);
// We are using a default pipeline here, you can learn more about it at https://github.com/Azure/azure-storage-java/wiki/Azure-Storage-Java-V10-Overview
final ServiceURL serviceURL = new ServiceURL(new URL("http://" + accountName + ".blob.core.windows.net"), StorageURL.createPipeline(creds, new PipelineOptions()));

// Let's create a container using a blocking call to Azure Storage
// If container exists, we'll catch and continue
containerURL = serviceURL.createContainerURL("quickstart");

try {
    ContainersCreateResponse response = containerURL.create(null, null).blockingGet();
    System.out.println("Container Create Response was " + response.statusCode());
} catch (RestException e){
    if (e instanceof RestException && ((RestException)e).response().statusCode() != 409) {
        throw e;
    } else {
        System.out.println("quickstart container already exists, resuming...");
    }
}
```

### <a name="upload-blobs-to-the-container"></a>Carregar blobs para o contêiner

O Armazenamento de Blobs dá suporte a blobs de blocos, blobs de acréscimo e blobs de páginas. Blobs de bloco são os mais usados. Eles são usados neste início rápido. 

1. Para carregar um arquivo em um blob, obtenha uma referência para o blob no contêiner de destino. 
2. Depois de obter a referência de blob, você poderá carregar um arquivo para ele usando uma das seguintes APIs:

   * APIs de baixo nível. Dentre os exemplos estão [BlockBlobURL.upload](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._block_blob_u_r_l.upload?view=azure-java-stable#com_microsoft_azure_storage_blob__block_blob_u_r_l_upload_Flowable_ByteBuffer__long_BlobHTTPHeaders_Metadata_BlobAccessConditions_Context_), também chamado de PutBlob, e [BlockBlobURL.stageBlock](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._block_blob_u_r_l.stageblock?view=azure-java-stable), também chamado de PutBLock, na instância de **BlockBlobURL**. 

   * As APIs de alto nível fornecidas na [classe TransferManager](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._transfer_manager?view=azure-java-stable). Um exemplo é o método [TransferManager.uploadFileToBlockBlob](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._transfer_manager.uploadfiletoblockblob?view=azure-java-stable). 

     A operação criará o blob se ainda não existir. Ele substitui o blob, caso já exista.

O código de exemplo cria um arquivo local a ser usado para upload e download. Ele armazena o arquivo a ser carregado como **sourceFile** e armazena a URL do blob em **blob**. O exemplo a seguir carrega o arquivo no seu contêiner chamado **quickstart**.

```java
static void uploadFile(BlockBlobURL blob, File sourceFile) throws IOException {

    FileChannel fileChannel = FileChannel.open(sourceFile.toPath());

    // Uploading a file to the blobURL using the high-level methods available in TransferManager class
    // Alternatively call the Upload/StageBlock low-level methods from BlockBlobURL type
    TransferManager.uploadFileToBlockBlob(fileChannel, blob, 8*1024*1024, null)
        .subscribe(response-> {
            System.out.println("Completed upload request.");
            System.out.println(response.response().statusCode());
        });
}
```

Blobs de bloco podem ser qualquer tipo de arquivo binário ou de texto. Os blobs de páginas são usados principalmente para os arquivos VHD usados auxiliar as VMs IaaS. Blobs de acréscimo são usados para acrescentar dados ao final, e eles geralmente são usados para registro em log. A maioria dos objetos armazenados no Armazenamento de Blobs são blobs de blocos.

### <a name="list-the-blobs-in-a-container"></a>Listar os blobs em um contêiner

É possível obter uma lista de objetos em um contêiner usando [ContainerURL.listBlobsFlatSegment](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._container_u_r_l.listblobsflatsegment?view=azure-java-stable). Esse método retorna até 5 mil objetos de uma só vez junto com um marcador de continuação quando há mais objetos a serem listados no contêiner. Crie uma função auxiliar que se chama repetidamente quando há um marcador de continuação na resposta **listBlobsFlatSegment** anterior.

```java
static void listBlobs(ContainerURL containerURL) {
    // Each ContainerURL.listBlobsFlatSegment call return up to maxResults (maxResults=10 passed into ListBlobOptions below).
    // To list all Blobs, we are creating a helper static method called listAllBlobs,
    // and calling it after the initial listBlobsFlatSegment call
    ListBlobsOptions options = new ListBlobsOptions(null, null, 10);

    containerURL.listBlobsFlatSegment(null, options)
        .flatMap(containersListBlobFlatSegmentResponse ->
            listAllBlobs(containerURL, containersListBlobFlatSegmentResponse))
                .subscribe(response-> {
                    System.out.println("Completed list blobs request.");
                    System.out.println(response.statusCode());
                });
}

private static Single <ContainersListBlobFlatSegmentResponse> listAllBlobs(ContainerURL url, ContainersListBlobFlatSegmentResponse response) {
    // Process the blobs returned in this result segment (if the segment is empty, blobs() will be null.
    if (response.body().blobs() != null) {
        for (Blob b : response.body().blobs().blob()) {
            String output = "Blob name: " + b.name();
            if (b.snapshot() != null) {
                output += ", Snapshot: " + b.snapshot();
            }
            System.out.println(output);
        }
    }
    else {
        System.out.println("There are no more blobs to list off.");
    }

    // If there is not another segment, return this response as the final response.
    if (response.body().nextMarker() == null) {
        return Single.just(response);
    } else {
        /*
        IMPORTANT: ListBlobsFlatSegment returns the start of the next segment; you MUST use this to get the next
        segment (after processing the current result segment
        */

        String nextMarker = response.body().nextMarker();

        /*
        The presence of the marker indicates that there are more blobs to list, so we make another call to
        listBlobsFlatSegment and pass the result through this helper function.
        */

        return url.listBlobsFlatSegment(nextMarker, new ListBlobsOptions(null, null,1))
            .flatMap(containersListBlobFlatSegmentResponse ->
                listAllBlobs(url, containersListBlobFlatSegmentResponse));
    }
}
```

### <a name="download-blobs"></a>Baixar blobs

Baixe os blobs em seu disco local usando [BlobURL.download](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._blob_u_r_l.download?view=azure-java-stable).

O código a seguir baixa o blob carregado em uma seção anterior. Ele adiciona um sufixo **_DOWNLOADED** ao nome do blob para que você possa ver ambos os arquivos no disco local. 

```java
static void getBlob(BlockBlobURL blobURL, File sourceFile) {
    try {
        // Get the blob using the low-level download method in BlockBlobURL type
        // com.microsoft.rest.v2.util.FlowableUtil is a static class that contains helpers to work with Flowable
        blobURL.download(new BlobRange(0, Long.MAX_VALUE), null, false)
            .flatMapCompletable(response -> {
                AsynchronousFileChannel channel = AsynchronousFileChannel.open(Paths
                    .get(sourceFile.getPath()), StandardOpenOption.CREATE,  StandardOpenOption.WRITE);
                        return FlowableUtil.writeFile(response.body(), channel);
            }).doOnComplete(()-> System.out.println("The blob was downloaded to " + sourceFile.getAbsolutePath()))
            // To call it synchronously add .blockingAwait()
            .subscribe();
    } catch (Exception ex){
        System.out.println(ex.toString());
    }
}
```

### <a name="clean-up-resources"></a>Limpar recursos

Se você não precisa mais dos blobs carregados neste início rápido, é possível excluir o contêiner usando [ContainerURL.delete](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._container_u_r_l.delete?view=azure-java-stable). Esse método também exclui os arquivos no contêiner.

```java
containerURL.delete(null).blockingGet();
```

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu a transferir arquivos entre um disco local e o Armazenamento de Blobs do Azure usando Java. 

> [!div class="nextstepaction"]
> [SDK de Armazenamento V10 para código-fonte em Java](https://github.com/Azure/azure-storage-java/)
> [Referência de API](https://docs.microsoft.com/java/api/overview/azure/storage/client?view=azure-java-stable)
> [Saiba mais sobre o RxJava](https://github.com/ReactiveX/RxJava)
