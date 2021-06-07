---
title: Início Rápido do Azure - Criar um blob no armazenamento de objeto usando o Go | Microsoft Docs
description: Neste início rápido, você criará uma conta de armazenamento e um contêiner no armazenamento de objeto (Blob). Em seguida, você deve usar a biblioteca de clientes de armazenamento para Go a fim de carregar um blob no Armazenamento do Azure, baixar um blob e listar os blobs em um contêiner.
author: twooley
ms.author: twooley
ms.date: 11/14/2018
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: aa1c4aea2966e70728fbde22fdcbe1e8d78cb166
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106280608"
---
# <a name="quickstart-upload-download-and-list-blobs-using-go"></a>Início rápido: Carregar, baixar e listar blobs usando Go

Neste início rápido, você aprenderá a usar a linguagem de programação Go para carregar, baixar e listar blobs de blocos em um contêiner no Armazenamento de Blobs do Azure. 

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

Verifique se você tem os pré-requisitos adicionais a seguir instalados:
 
* [Go 1.8 ou superior](https://golang.org/dl/)
* [SDK do Azure Storage Blob para Go](https://github.com/azure/azure-storage-blob-go/) usando comando a seguir:

    ```
    go get -u github.com/Azure/azure-storage-blob-go/azblob
    ``` 

    > [!NOTE]
    > Coloque em maiúscula `Azure` na URL para evitar problemas de importação relacionadas à capitalização ao trabalhar com o SDK. Coloque `Azure` em maiúscula também em suas instruções de importação.
    
## <a name="download-the-sample-application"></a>Baixar o aplicativo de exemplo
O [aplicativo de exemplo](https://github.com/Azure-Samples/storage-blobs-go-quickstart.git) usado neste início rápido é um aplicativo Go básico.  

Use o [git](https://git-scm.com/) para baixar uma cópia do aplicativo para seu ambiente de desenvolvimento. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-go-quickstart 
```

Este comando clona o repositório para sua pasta do git local. Para abrir o exemplo do Go para armazenamento de Blob, procure o arquivo storage-quickstart.go.  

[!INCLUDE [storage-copy-account-key-portal](../../../includes/storage-copy-account-key-portal.md)]

## <a name="configure-your-storage-connection-string"></a>Configurar a cadeia de conexão de armazenamento
Essa solução requer que o nome e a chave da conta de armazenamento sejam armazenados com segurança em variáveis de ambiente locais no computador que está executando o exemplo. Siga um dos exemplos abaixo, dependendo do seu sistema operacional para criar as variáveis de ambiente.

# <a name="linux"></a>[Linux](#tab/linux)

```
export AZURE_STORAGE_ACCOUNT="<youraccountname>"
export AZURE_STORAGE_ACCESS_KEY="<youraccountkey>"
```

# <a name="windows"></a>[Windows](#tab/windows)

```
setx AZURE_STORAGE_ACCOUNT "<youraccountname>"
setx AZURE_STORAGE_ACCESS_KEY "<youraccountkey>"
```

---

## <a name="run-the-sample"></a>Execute o exemplo
Esse exemplo cria um arquivo de teste na pasta atual, carrega o arquivo de teste no Armazenamento de blobs, lista os blobs no contêiner e baixa o arquivo em um buffer. 

Para executar o exemplo, emita o seguinte comando: 

```go run storage-quickstart.go```

A saída a seguir é um exemplo da saída retornada ao executar o aplicativo:
  
```
Azure Blob storage quick start sample
Creating a container named quickstart-5568059279520899415
Creating a dummy file to test the upload and download
Uploading the file with blob name: 630910657703031215
Blob name: 630910657703031215
Downloaded the blob: hello world
this is a blob
Press the enter key to delete the sample files, example container, and exit the application.
```
Quando você pressiona a tecla para continuar, o programa de exemplo exclui o contêiner de armazenamento e os arquivos. 

> [!TIP]
> Você também pode usar uma ferramenta como o [Gerenciador de Armazenamento do Azure](https://storageexplorer.com) para exibir os arquivos no Armazenamento de Blobs. O Gerenciador de Armazenamento do Azure é uma ferramenta gratuita de multiplataforma que permite que você acesse as informações da sua conta de armazenamento. 
>

## <a name="understand-the-sample-code"></a>Entender o código de exemplo

Em seguida, vamos percorrer o código de exemplo para que você possa entender como ele funciona.

### <a name="create-containerurl-and-bloburl-objects"></a>Criar objetos ContainerURL e BlobURL
A primeira coisa a fazer é criar as referências aos objetos ContainerURL e BlobURL usados para acessar e gerenciar o Armazenamento de blobs. Esses objetos oferecem APIs de baixo nível, como Criar, Carregar e Baixar para emitir APIs REST.

* Use o struct [**SharedKeyCredential**](https://godoc.org/github.com/Azure/azure-storage-blob-go/azblob#SharedKeyCredential) para armazenar suas credenciais. 

* Crie um [**Pipeline**](https://godoc.org/github.com/Azure/azure-storage-blob-go/azblob#NewPipeline) usando as credenciais e as opções. O pipeline especifica itens como políticas de repetição, registro em log, desserialização de payloads de resposta HTTP e muito mais.  

* Crie uma instância de um novo [**ContainerURL**](https://godoc.org/github.com/Azure/azure-storage-blob-go/azblob#ContainerURL) e um novo objeto [**BlobURL**](https://godoc.org/github.com/Azure/azure-storage-blob-go/azblob#BlobURL) para executar operações em contêiner (Criar) e blobs (Upload e Download).


Depois que tiver o ContainerURL, você pode criar uma instância do objeto **BlobURL**, que aponta para um blob, e executar operações como carregar, baixar e copiar.

> [!IMPORTANT]
> Os nomes de contêiner devem estar em minúsculas. Consulte [Naming and Referencing Containers, Blobs, and Metadata](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) (Nomenclatura e referência de contêineres, blobs e metadados) para obter mais informações sobre nomes de contêiner e de blobs.

Nesta seção, você criará um novo contêiner. O contêiner é chamado de **quickstartblobs-[random string]** . 

```go 
// From the Azure portal, get your storage account name and key and set environment variables.
accountName, accountKey := os.Getenv("AZURE_STORAGE_ACCOUNT"), os.Getenv("AZURE_STORAGE_ACCESS_KEY")
if len(accountName) == 0 || len(accountKey) == 0 {
    log.Fatal("Either the AZURE_STORAGE_ACCOUNT or AZURE_STORAGE_ACCESS_KEY environment variable is not set")
}

// Create a default request pipeline using your storage account name and account key.
credential, err := azblob.NewSharedKeyCredential(accountName, accountKey)
if err != nil {
    log.Fatal("Invalid credentials with error: " + err.Error())
}
p := azblob.NewPipeline(credential, azblob.PipelineOptions{})

// Create a random string for the quick start container
containerName := fmt.Sprintf("quickstart-%s", randomString())

// From the Azure portal, get your storage account blob service URL endpoint.
URL, _ := url.Parse(
    fmt.Sprintf("https://%s.blob.core.windows.net/%s", accountName, containerName))

// Create a ContainerURL object that wraps the container URL and a request
// pipeline to make requests.
containerURL := azblob.NewContainerURL(*URL, p)

// Create the container
fmt.Printf("Creating a container named %s\n", containerName)
ctx := context.Background() // This example uses a never-expiring context
_, err = containerURL.Create(ctx, azblob.Metadata{}, azblob.PublicAccessNone)
handleErrors(err)
```
### <a name="upload-blobs-to-the-container"></a>Carregar blobs para o contêiner

O Armazenamento de Blobs dá suporte a blobs de blocos, blobs de acréscimo e blobs de páginas. Os blobs de blocos são utilizados com mais frequência e são usados nesse guia de início rápido.  

Para carregar um arquivo em um blob, abra o arquivo usando **os.Open**. Depois, você pode carregar o arquivo no caminho especificado usando uma das APIs REST: Upload (PutBlob), StageBlock/CommitBlockList (PutBlock/PutBlockList). 

Como alternativa, o SDK oferece [APIs de alto nível](https://github.com/Azure/azure-storage-blob-go/blob/master/azblob/highlevel.go) que são criadas sobre as APIs REST de nível inferior. Por exemplo, a função ***UploadFileToBlockBlob*** usa operações StageBlock (PutBlock) para carregar um arquivo em partes, simultaneamente, para otimizar a taxa de transferência. Se o arquivo for menor que 256 MB, ele usa o Upload (PutBlob) para concluir a transferência em uma única transação.

O exemplo a seguir carrega o arquivo para seu contêiner chamado **quickstartblobs-[randomstring]** .

```go
// Create a file to test the upload and download.
fmt.Printf("Creating a dummy file to test the upload and download\n")
data := []byte("hello world this is a blob\n")
fileName := randomString()
err = ioutil.WriteFile(fileName, data, 0700)
handleErrors(err)

// Here's how to upload a blob.
blobURL := containerURL.NewBlockBlobURL(fileName)
file, err := os.Open(fileName)
handleErrors(err)

// You can use the low-level Upload (PutBlob) API to upload files. Low-level APIs are simple wrappers for the Azure Storage REST APIs.
// Note that Upload can upload up to 256MB data in one shot. Details: https://docs.microsoft.com/rest/api/storageservices/put-blob
// To upload more than 256MB, use StageBlock (PutBlock) and CommitBlockList (PutBlockList) functions. 
// Following is commented out intentionally because we will instead use UploadFileToBlockBlob API to upload the blob
// _, err = blobURL.Upload(ctx, file, azblob.BlobHTTPHeaders{ContentType: "text/plain"}, azblob.Metadata{}, azblob.BlobAccessConditions{})
// handleErrors(err)

// The high-level API UploadFileToBlockBlob function uploads blocks in parallel for optimal performance, and can handle large files as well.
// This function calls StageBlock/CommitBlockList for files larger 256 MBs, and calls Upload for any file smaller
fmt.Printf("Uploading the file with blob name: %s\n", fileName)
_, err = azblob.UploadFileToBlockBlob(ctx, file, blobURL, azblob.UploadToBlockBlobOptions{
    BlockSize:   4 * 1024 * 1024,
    Parallelism: 16})
handleErrors(err)
```

### <a name="list-the-blobs-in-a-container"></a>Listar os blobs em um contêiner

Obtenha uma lista de arquivos no contêiner usando o método **ListBlobs** em um **ContainerURL**. ListBlobs retorna um único segmento de blobs (até 5.000), começando pelo **Marcador** especificado. Use um Marcador vazio para começar a enumeração desde o início. Nomes de blob são retornados em ordem lexicográfica. Depois de obter um segmento, processe-o e depois chame o ListBlobs novamente passando o Marcador retornado anteriormente.  

```go
// List the container that we have created above
fmt.Println("Listing the blobs in the container:")
for marker := (azblob.Marker{}); marker.NotDone(); {
    // Get a result segment starting with the blob indicated by the current Marker.
    listBlob, err := containerURL.ListBlobsFlatSegment(ctx, marker, azblob.ListBlobsSegmentOptions{})
    handleErrors(err)

    // ListBlobs returns the start of the next segment; you MUST use this to get
    // the next segment (after processing the current result segment).
    marker = listBlob.NextMarker

    // Process the blobs returned in this result segment (if the segment is empty, the loop body won't execute)
    for _, blobInfo := range listBlob.Segment.BlobItems {
        fmt.Print(" Blob name: " + blobInfo.Name + "\n")
    }
}
```

### <a name="download-the-blob"></a>Baixar o blob

Baixe blobs usando a função **Download** de nível inferior em um BlobURL. Isso retornará um struct **DownloadResponse**. Execute a função **Body** sobre a struct para obter um fluxo **RetryReader** para a leitura de dados. Se uma conexão falhar durante a leitura, solicitações adicionais serão feitas para restabelecer uma conexão e continuar a leitura. Especificar um RetryReaderOption com o conjunto MaxRetryRequests como 0 (o padrão) retorna o corpo da resposta original e nenhuma nova tentativa será realizada. Como alternativa, use as APIs de alto nível **DownloadBlobToBuffer** ou **DownloadBlobToFile** para simplificar seu código.

O código a seguir baixa o blob usando a função **Download**. O conteúdo do blob é gravado em um buffer e mostrado no console.

```go
// Here's how to download the blob
downloadResponse, err := blobURL.Download(ctx, 0, azblob.CountToEnd, azblob.BlobAccessConditions{}, false)

// NOTE: automatically retries are performed if the connection fails
bodyStream := downloadResponse.Body(azblob.RetryReaderOptions{MaxRetryRequests: 20})

// read the body into a buffer
downloadedData := bytes.Buffer{}
_, err = downloadedData.ReadFrom(bodyStream)
handleErrors(err)
```

### <a name="clean-up-resources"></a>Limpar os recursos
Se você não precisar mais dos blobs carregados neste início rápido, poderá excluir todo o contêiner usando o método **Delete**. 

```go
// Cleaning up the quick start by deleting the container and the file created locally
fmt.Printf("Press enter key to delete the sample files, example container, and exit the application.\n")
bufio.NewReader(os.Stdin).ReadBytes('\n')
fmt.Printf("Cleaning up.\n")
containerURL.Delete(ctx, azblob.ContainerAccessConditions{})
file.Close()
os.Remove(fileName)
```

## <a name="resources-for-developing-go-applications-with-blobs"></a>Recursos para desenvolvimento de aplicativos Go com blobs

Consulte estes recursos adicionais para o desenvolvimento de Go com armazenamento de Blobs:

- Exiba e instale o [código-fonte da biblioteca de clientes Go](https://github.com/Azure/azure-storage-blob-go) para o Armazenamento do Azure no GitHub.
- Explore [exemplos de armazenamento de Blobs](https://godoc.org/github.com/Azure/azure-storage-blob-go/azblob#pkg-examples) gravados usando a biblioteca de clientes Go.

## <a name="next-steps"></a>Próximas etapas
 
Neste início rápido, você aprendeu a transferir arquivos entre o disco local e o armazenamento de blobs do Azure usando o Go. Para obter mais informações sobre o SDK do Azure Storage Blob, consulte o [Código-Fonte](https://github.com/Azure/azure-storage-blob-go/) e a [Referência da API](https://godoc.org/github.com/Azure/azure-storage-blob-go/azblob).