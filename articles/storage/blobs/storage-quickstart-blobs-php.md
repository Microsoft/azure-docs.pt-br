---
title: Início Rápido do Azure - Criar um blob no armazenamento de objeto usando o PHP | Microsoft Docs
description: Aprenda rapidamente a transferir objetos de/para o Armazenamento de Blobs do Azure usando PHP. Carregue, baixe e liste blobs de blocos em um contêiner no armazenamento de Blobs do Azure.
author: twooley
ms.author: twooley
ms.date: 11/14/2018
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: 400982b1cacd6ccd409559679376edcfc886ed5c
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106280047"
---
#  <a name="transfer-objects-tofrom-azure-blob-storage-using-php"></a>Transferir objetos de/para o Armazenamento de Blobs do Azure usando PHP
Neste guia de início rápido, você aprenderá a usar PHP para carregar, baixar e listar blobs de blocos em um contêiner no Armazenamento de Blobs do Azure. 

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

Verifique se você tem os pré-requisitos adicionais a seguir instalados:

* [PHP](https://php.net/downloads.php)
* [SDK do Armazenamento do Azure para PHP](https://github.com/Azure/azure-storage-php)

## <a name="download-the-sample-application"></a>Baixar o aplicativo de exemplo
O [aplicativo de exemplo](https://github.com/Azure-Samples/storage-blobs-php-quickstart.git) usado neste início rápido é um aplicativo PHP básico.  

Use o [git](https://git-scm.com/) para baixar uma cópia do aplicativo para seu ambiente de desenvolvimento. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-php-quickstart.git
```

Este comando clona o repositório para sua pasta do git local. Para abrir o aplicativo PHP de exemplo, procure a pasta storage-blobs-php-quickstart e abra o arquivo phpqs.php.  

[!INCLUDE [storage-copy-account-key-portal](../../../includes/storage-copy-account-key-portal.md)]

## <a name="configure-your-storage-connection-string"></a>Configurar a cadeia de conexão de armazenamento
No aplicativo, você deve fornecer o nome da conta de armazenamento e a chave de conta para criar a instância **BlobRestProxy** do seu aplicativo. É recomendável armazenar esses identificadores dentro de uma variável de ambiente no computador local que está executando o aplicativo. Use um dos exemplos a seguir, dependendo do seu sistema operacional, para criar a variável de ambiente. Substitua os valores **youraccountname** e **youraccountkey** pelo nome e chave da conta.

# <a name="linux"></a>[Linux](#tab/linux)

```bash
export ACCOUNT_NAME=<youraccountname>
export ACCOUNT_KEY=<youraccountkey>
```

# <a name="windows"></a>[Windows](#tab/windows)

```cmd
setx ACCOUNT_NAME=<youraccountname>
setx ACCOUNT_KEY=<youraccountkey>
```
---

## <a name="configure-your-environment"></a>Configurar seu ambiente
Leve a pasta de sua pasta git local e coloque-a em um diretório servido pelo servidor PHP. Em seguida, abra um prompt de comando no escopo do mesmo diretório e digite: `php composer.phar install`

## <a name="run-the-sample"></a>Execute o exemplo
Este exemplo cria um arquivo de teste na pasta '.'. O programa de exemplo carrega o arquivo de teste no armazenamento de Blobs, lista os blobs no contêiner e baixa o arquivo com um novo nome. 

Execute o exemplo. A saída a seguir é um exemplo da saída retornada ao executar o aplicativo:
  
```
Uploading BlockBlob: HelloWorld.txt
These are the blobs present in the container: HelloWorld.txt: https://myexamplesacct.blob.core.windows.net/blockblobsleqvxd/HelloWorld.txt

This is the content of the blob uploaded: Hello Azure!
```
Quando você pressiona a tecla exibida, o programa de exemplo exclui o contêiner de armazenamento e os arquivos. Antes de continuar, verifique se os dois documentos estão na pasta do servidor. Você pode abri-los e ver que eles são idênticos.

Você também pode usar uma ferramenta como o [Gerenciador de Armazenamento do Azure](https://storageexplorer.com) para exibir os arquivos no Armazenamento de Blobs. O Gerenciador de Armazenamento do Azure é uma ferramenta gratuita de multiplataforma que permite que você acesse as informações da sua conta de armazenamento. 

Depois de verificar os arquivos, pressione qualquer tecla para concluir a demonstração e excluir os arquivos de teste. Agora que você sabe o que o exemplo faz, abra o arquivo example.rb para examinar o código. 

## <a name="understand-the-sample-code"></a>Entender o código de exemplo

Em seguida, vamos percorrer o código de exemplo para que você possa entender como ele funciona.

### <a name="get-references-to-the-storage-objects"></a>Obter referências a objetos de armazenamento
A primeira coisa a fazer é criar as referências aos objetos usados para acessar e gerenciar o Armazenamento de Blobs. Esses objetos dependem uns dos outros, e cada um é usado pelo próximo na lista.

* Crie uma instância do objeto do **BlobRestProxy** do Armazenamento do Azure para configurar as credenciais de conexão. 
* Crie o objeto **BlobService**, que aponta para o serviço Blob na sua conta de armazenamento. 
* Crie o objeto **Container**, que representa o contêiner que você está acessando. Os contêineres são usados para organizar seus blobs da mesma forma que você usa pastas no seu computador para organizar seus arquivos.

Quando tiver o objeto de contêiner **blobClient**, você poderá criar o objeto de blob **Bloco** que aponta para o blob específico no qual está interessado. Em seguida, você pode executar operações como cópia, upload e download.

> [!IMPORTANT]
> Os nomes de contêiner devem estar em minúsculas. Consulte [Naming and Referencing Containers, Blobs, and Metadata](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) (Nomenclatura e referência de contêineres, blobs e metadados) para obter mais informações sobre nomes de contêiner e de blobs.

Nesta seção, você configura uma instância do cliente do armazenamento do Azure, instancia o objeto do serviço Blobs, cria um novo contêiner e define as permissões no contêiner para que os blobs sejam públicos. O contêiner é chamado de **quickstartblobs**. 

```PHP
    # Setup a specific instance of an Azure::Storage::Client
    $connectionString = "DefaultEndpointsProtocol=https;AccountName=".getenv('account_name').";AccountKey=".getenv('account_key');
    
    // Create blob client.
    $blobClient = BlobRestProxy::createBlobService($connectionString);
    
    # Create the BlobService that represents the Blob service for the storage account
    $createContainerOptions = new CreateContainerOptions();
    
    $createContainerOptions->setPublicAccess(PublicAccessType::CONTAINER_AND_BLOBS);
    
    // Set container metadata.
    $createContainerOptions->addMetaData("key1", "value1");
    $createContainerOptions->addMetaData("key2", "value2");

    $containerName = "blockblobs".generateRandomString();

    try    {
        // Create container.
        $blobClient->createContainer($containerName, $createContainerOptions);
```

### <a name="upload-blobs-to-the-container"></a>Carregar blobs para o contêiner

O Armazenamento de Blobs dá suporte a blobs de blocos, blobs de acréscimo e blobs de páginas. Os blobs de blocos são utilizados com mais frequência e são usados nesse guia de início rápido.  

Para carregar um arquivo em um blob, obtenha o caminho completo do arquivo unindo o nome do diretório e o nome do arquivo na unidade local. Em seguida, você pode carregar o arquivo no caminho especificado usando o método **createBlockBlob()**. 

O código de exemplo usa um arquivo local e carrega-o no Azure. O arquivo é armazenado como **myfile** e o nome do blob como **fileToUpload** no código. O exemplo a seguir carrega o arquivo para seu contêiner chamado **quickstartblobs**.

```PHP
    $myfile = fopen("HelloWorld.txt", "w") or die("Unable to open file!");
    fclose($myfile);

    # Upload file as a block blob
    echo "Uploading BlockBlob: ".PHP_EOL;
    echo $fileToUpload;
    echo "<br />";
    
    $content = fopen($fileToUpload, "r");

    //Upload blob
    $blobClient->createBlockBlob($containerName, $fileToUpload, $content);
```

Para executar uma atualização parcial do conteúdo de um blob de blocos, use o método **createblocklist()**. Os blobs de bloco podem ter até 4,7 TB e podem ser qualquer coisa desde planilhas do Excel até arquivos de vídeo grandes. Os blobs de páginas são usados principalmente para os arquivos VHD usados auxiliar as VMs IaaS. Os blobs de acréscimo são usados para registro em log, como quando você quer gravar em um arquivo e depois adicionar mais informações. O acréscimo de blobs deve ser usado em um único modo de gravação. A maioria dos objetos armazenados no Armazenamento de Blobs são blobs de blocos.

### <a name="list-the-blobs-in-a-container"></a>Listar os blobs em um contêiner

É possível obter uma lista de arquivos no contêiner usando o método **listBlobs()**. O código a seguir recupera a lista de blobs e a percorre, mostrando os nomes dos blobs encontrados em um contêiner.  

```PHP
    $listBlobsOptions = new ListBlobsOptions();
    $listBlobsOptions->setPrefix("HelloWorld");
    
    echo "These are the blobs present in the container: ";
    
    do{
        $result = $blobClient->listBlobs($containerName, $listBlobsOptions);
        foreach ($result->getBlobs() as $blob)
        {
            echo $blob->getName().": ".$blob->getUrl()."<br />";
        }
        
        $listBlobsOptions->setContinuationToken($result->getContinuationToken());
    } while($result->getContinuationToken());
```

### <a name="get-the-content-of-your-blobs"></a>Obter o conteúdo dos blobs

Obter o conteúdo dos blobs usando o método **getBlob()**. O código a seguir exibe o conteúdo do blob carregado em uma seção anterior.

```PHP
    $blob = $blobClient->getBlob($containerName, $fileToUpload);
    fpassthru($blob->getContentStream());
```

### <a name="clean-up-resources"></a>Limpar os recursos
Se você não precisar mais dos blobs carregados neste guia de início rápido, poderá excluir o contêiner inteiro usando o método **deleteContainer()**. Se os arquivos criados não forem mais necessários, use o método **deleteBlob()** para excluí-los.

```PHP
    // Delete blob.
    echo "Deleting Blob".PHP_EOL;
    echo $fileToUpload;
    echo "<br />";
    $blobClient->deleteBlob($_GET["containerName"], $fileToUpload);

    // Delete container.
    echo "Deleting Container".PHP_EOL;
    echo $_GET["containerName"].PHP_EOL;
    echo "<br />";
    $blobClient->deleteContainer($_GET["containerName"]);

    //Deleting local file
    echo "Deleting file".PHP_EOL;
    echo "<br />";
    unlink($fileToUpload);   
```

## <a name="resources-for-developing-php-applications-with-blobs"></a>Recursos para desenvolvimento de aplicativos PHP com blobs

Consulte estes recursos adicionais para o desenvolvimento em PHP com armazenamento de blobs:

- Veja, baixe e instale o [código-fonte da biblioteca do cliente PHP](https://github.com/Azure/azure-storage-php) para o Armazenamento do Azure no GitHub.
- Explore [exemplos de armazenamento de Blobs](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=php&term=blob) gravados usando a biblioteca de clientes de PHP.

## <a name="next-steps"></a>Próximas etapas
 
Neste início rápido, você aprendeu a transferir arquivos entre o disco local e o armazenamento de blobs do Azure usando PHP. Para saber mais sobre como trabalhar com o PHP, vá até nossa Central de desenvolvedores do PHP.

> [!div class="nextstepaction"]
> [Central de Desenvolvedores do PHP](https://azure.microsoft.com/develop/php/)


Para obter mais informações sobre o Gerenciador de Armazenamento e os Blobs, consulte [Gerenciar os recursos de Armazenamento de Blobs do Azure com o Gerenciador de Armazenamento](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).