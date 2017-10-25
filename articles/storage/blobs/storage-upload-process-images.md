---
title: Carregar dados de imagem na nuvem com o Armazenamento do Azure | Microsoft Docs
description: Use o armazenamento de Blobs do Azure com um aplicativo Web para armazenar dados de aplicativo
services: storage
documentationcenter: 
author: georgewallace
manager: timlt
editor: 
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: csharp
ms.topic: tutorial
ms.date: 09/19/2017
ms.author: gwallace
ms.custom: mvc
ms.openlocfilehash: a204498016ff837c5247009eaaffbd4f79285d0b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="upload-image-data-in-the-cloud-with-azure-storage"></a>Carregar dados de imagem na nuvem com o Armazenamento do Azure

Este tutorial é a primeira parte de uma série. Este tutorial mostra como implantar um aplicativo Web que usa a Biblioteca de Clientes do Armazenamento do Azure para carregar imagens para uma conta de armazenamento. Ao terminar, você tem um aplicativo Web armazenando e exibindo imagens do Armazenamento do Azure.

![Exibição do contêiner de imagens](media/storage-upload-process-images/figure2.png)

Na primeira parte da série, você aprenderá a:

> [!div class="checklist"]
> * Criar uma conta de armazenamento
> * Criar um contêiner e definir permissões
> * Recuperar uma chave de acesso
> * Definir as configurações do aplicativo
> * Implantar um aplicativo Web no Azure
> * Interagir com o aplicativo Web

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este tutorial exigirá que você execute a CLI do Azure versão 2.0.4 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure]( /cli/azure/install-azure-cli). 

## <a name="create-a-resource-group"></a>Criar um grupo de recursos 

Crie um grupo de recursos com o comando [az group create](/cli/azure/group#create). Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados.
 
O seguinte exemplo cria um grupo de recursos chamado `myResourceGroup`.
 
```azurecli-interactive 
az group create --name myResourceGroup --location westcentralus 
``` 

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento
 
O exemplo carrega imagens em um contêiner de blob em uma conta de armazenamento do Azure. Uma conta de armazenamento fornece um namespace exclusivo para armazenar e acessar os objetos de dados do Armazenamento do Azure. Crie uma conta de armazenamento no grupo de recursos que você criou ao utilizar o comando [az storage account create](/cli/azure/storage/account#create). 

> [!IMPORTANT] 
> Na parte 2 do tutorial você usa assinaturas de evento para o armazenamento de blobs. As assinaturas de evento atualmente têm suporte apenas para contas de armazenamento de blobs no Centro-oeste dos EUA e Oeste dos EUA 2. Por causa dessa restrição, você deve criar uma conta de armazenamento de blobs que é usada pelo aplicativo de exemplo para armazenar imagens e miniaturas.   

No comando a seguir, substitua seu próprio nome global exclusivo da conta de armazenamento de blobs quando o espaço reservado `<blob_storage_account>` for exibido.  

```azurecli-interactive 
az storage account create --name <blob_storage_account> \
--location westcentralus --resource-group myResourceGroup \
--sku Standard_LRS --kind blobstorage --access-tier hot 
``` 
 
## <a name="create-blob-storage-containers"></a>Criar contêineres de armazenamento de blobs
 
O aplicativo usa dois contêineres na conta de armazenamento de blobs. Os contêineres são semelhantes às pastas e são usados para armazenar blobs. O contêiner de _imagens_ é onde o aplicativo carrega as imagens de alta resolução. Em uma parte posterior da série, um aplicativo de função do Azure carrega miniaturas de imagem redimensionada para o contêiner _miniaturas_. 

Obtenha a chave de conta de armazenamento usando o comando [az storage account keys list](/cli/azure/storage/account/keys#list). Use essa chave para criar dois contêineres usando o comando [az storage container create](/cli/azure/storage/container#create).  
 
Nesse caso, `<blob_storage_account>` é o nome da conta de armazenamento de blobs criada. O acesso público aos contêineres de _imagens_ está definido como `off` e o acesso público aos contêineres _miniaturas_ está definido como `container`. As configurações de acesso público de `container` permitem que as miniaturas estejam visíveis para as pessoas que visitam a página da Web.
 
```azurecli-interactive 
blobStorageAccount=<blob_storage_account>

blobStorageAccountKey=$(az storage account keys list -g myResourceGroup \
-n $blobStorageAccount --query [0].value --output tsv) 

az storage container create -n images --account-name $blobStorageAccount \
--account-key $blobStorageAccountKey --public-access off 

az storage container create -n thumbs --account-name $blobStorageAccount \
--account-key $blobStorageAccountKey --public-access container

echo "Make a note of your blob storage account key..." 
echo $blobStorageAccountKey 
``` 

Anote o nome e a chave da sua conta de armazenamento de blobs. O aplicativo de exemplo usa essas configurações para se conectar à conta de armazenamento para carregar as imagens. 

## <a name="create-an-app-service-plan"></a>Criar um plano de Serviço de Aplicativo 

Um [plano do Serviço de Aplicativo](../../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) especifica o local, tamanho e recursos do farm de servidores Web que hospeda o aplicativo. 

Criar um plano do Serviço de Aplicativo com o comando [az appservice plan create](/cli/azure/appservice/plan#create). 

O exemplo a seguir cria um plano do Serviço de Aplicativo denominado `myAppServicePlan` usando o tipo de preço **Gratuita**: 

```azurecli-interactive 
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku FREE 
``` 

## <a name="create-a-web-app"></a>Criar um aplicativo Web 

O aplicativo Web fornece um espaço de hospedagem para o código do aplicativo de exemplo que é implantado do repositório de exemplo do GitHub. Crie um [aplicativo Web](../../app-service/app-service-web-overview.md) no plano do Serviço de Aplicativo do `myAppServicePlan` com o comando [az webapp create](/cli/azure/webapp#create).  
 
No comando a seguir, substitua `<web_app>` por um nome exclusivo (os caracteres válidos são `a-z`, `0-9` e `-`). Se `<web_app>` não for exclusivo, você receberá a mensagem de erro: _O site com o nome `<web_app>` fornecido já existe._ A URL padrão do aplicativo Web é `https://<web_app>.azurewebsites.net`.  

```azurecli-interactive 
az webapp create --name <web_app> --resource-group myResourceGroup --plan myAppServicePlan 
``` 

## <a name="deploy-the-sample-app-from-the-github-repository"></a>Implantar o aplicativo de exemplo do repositório do GitHub 

Serviço de Aplicativo dá suporte a várias maneiras de implantar o conteúdo em um aplicativo Web. Neste tutorial, você implanta o aplicativo Web de um repositório de exemplo do GitHub público: [https://github.com/Azure-Samples/storage-blob-upload-from-webapp](https://github.com/Azure-Samples/storage-blob-upload-from-webapp). Configure a implantação do GitHub para o aplicativo Web com o comando [az webapp deployment source config](/cli/azure/webapp/deployment/source#config). Substitua `<web_app>` pelo nome do aplicativo Web criado na etapa anterior.

O projeto de exemplo contém um aplicativo [ASP.NET MVC](https://www.asp.net/mvc) que aceita uma imagem, salva-a em uma conta de armazenamento e exibe imagens de um contêiner de miniaturas. O aplicativo Web usa os namespaces [Microsoft.WindowsAzure.Storage](/dotnet/api/microsoft.windowsazure.storage?view=azure-dotnet), [Microsoft.WindowsAzure.Storage.Blob](/dotnet/api/microsoft.windowsazure.storage.blob?view=azure-dotnet) e [Microsoft.WindowsAzure.Storage.Auth](/dotnet/api/microsoft.windowsazure.storage.auth?view=azure-dotnet) da Biblioteca de Clientes do Armazenamento do Azure para interagir com o Armazenamento do Azure. 

```azurecli-interactive 
az webapp deployment source config --name <web_app> \
--resource-group myResourceGroup --branch master --manual-integration \
--repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
``` 

## <a name="configure-web-app-settings"></a>Definir configurações do aplicativo Web 

O aplicativo Web de exemplo usa a [Biblioteca de Clientes do Armazenamento do Azure](/dotnet/api/overview/azure/storage?view=azure-dotnet) para solicitar tokens de acesso, que são usados para carregar imagens. As credenciais da conta de armazenamento usadas pelo SDK do Armazenamento são definidas nas configurações do aplicativo para o aplicativo Web. Adicione configurações de aplicativo ao aplicativo implantado com o comando [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#set). 

No comando a seguir, `<blob_storage_account>` é o nome da sua conta de armazenamento de blobs e `<blob_storage_key>` é a chave associada. Substitua `<web_app>` pelo nome do aplicativo Web criado na etapa anterior.     

```azurecli-interactive 
az webapp config appsettings set --name <web_app> --resource-group myResourceGroup \
--settings AzureStorageConfig__AccountName=<blob_storage_account> \
AzureStorageConfig__ImageContainer=images  \
AzureStorageConfig__ThumbnailContainer=thumbs \
AzureStorageConfig__AccountKey=<blob_storage_key>  
``` 

Depois que o aplicativo Web é implantado e configurado, você pode testar a funcionalidade de upload de imagem no aplicativo.   

## <a name="upload-an-image"></a>Carregar uma imagem 

Para testar o aplicativo Web, navegue para a URL do aplicativo publicado. A URL padrão do aplicativo Web é `https://<web_app>.azurewebsites.net`. Selecione a região **Carregar fotos** para selecionar e carregar um arquivo ou arraste e solte um arquivo na região. A imagem desaparece se for carregada com êxito.

![Aplicativo ImageResizer](media/storage-upload-process-images/figure1.png)

No código de exemplo, a tarefa `UploadFiletoStorage` no arquivo `Storagehelper.cs` é usada para carregar as imagens no contêiner `images` dentro da conta de armazenamento usando o método [UploadFromStreamAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfromstreamasync?view=azure-dotnet). O exemplo de código a seguir contém a tarefa `UploadFiletoStorage`. 

```csharp
public static async Task<bool> UploadFileToStorage(Stream fileStream, string fileName, AzureStorageConfig _storageConfig)
{
    // Create storagecredentials object by reading the values from the configuration (appsettings.json)
    StorageCredentials storageCredentials = new StorageCredentials(_storageConfig.AccountName, _storageConfig.AccountKey);

    // Create cloudstorage account by passing the storagecredentials
    CloudStorageAccount storageAccount = new CloudStorageAccount(storageCredentials, true);

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get reference to the blob container by passing the name by reading the value from the configuration (appsettings.json)
    CloudBlobContainer container = blobClient.GetContainerReference(_storageConfig.ImageContainer);

    // Get the reference to the block blob from the container
    CloudBlockBlob blockBlob = container.GetBlockBlobReference(fileName);

    // Upload the file
    await blockBlob.UploadFromStreamAsync(fileStream);

    return await Task.FromResult(true);
}
```

As seguintes classes e métodos são usados na tarefa anterior:

|Classe  |Método  |
|---------|---------|
|[StorageCredentials](/dotnet/api/microsoft.windowsazure.storage.auth.storagecredentials?view=azure-dotnet)     |         |
|[CloudStorageAccount](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount?view=azure-dotnet)    |  [CreateCloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount.createcloudblobclient?view=azure-dotnet#Microsoft_WindowsAzure_Storage_CloudStorageAccount_CreateCloudBlobClient)       |
|[CloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient?view=azure-dotnet)     |[GetContainerReference](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient.getcontainerreference?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlobClient_GetContainerReference_System_String_)         |
|[CloudBlobContainer](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer?view=azure-dotnet)    | [GetBlockBlobReference](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.getblockblobreference?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlobContainer_GetBlockBlobReference_System_String_)        |
|[CloudBlockBlob](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob?view=azure-dotnet)     | [UploadFromStreamAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfromstreamasync?view=azure-dotnet)        |

## <a name="verify-the-image-is-shown-in-the-storage-account"></a>Verifique se a imagem é mostrada na conta de armazenamento

Entre em https://portal.azure.com. No menu à esquerda, selecione **Contas de armazenamento**, em seguida, selecione o nome da sua conta de armazenamento. Em **visão geral**, selecione o contêiner **Imagens**.

Verifique se a imagem é mostrada no contêiner.

![Exibição do contêiner de imagens](media/storage-upload-process-images/figure13.png)

## <a name="test-thumbnail-viewing"></a>Exibição de miniaturas de teste

Para testar a exibição de miniaturas, você carregará uma imagem para o contêiner de miniatura para garantir que o aplicativo pode ler o contêiner de miniatura.

Entre em https://portal.azure.com. No menu à esquerda, selecione **Contas de armazenamento**, em seguida, selecione o nome da sua conta de armazenamento. Selecione **Contêineres** em **Serviço Blob** e selecione o contêiner **miniaturas**. Selecione **Carregar** para abrir o painel **Carregar blob**.

Escolha um arquivo usando o seletor de arquivos e selecione **Carregar**.

Navegue de volta para seu aplicativo para verificar se a imagem carregada para o contêiner **miniaturas** está visível.

![Exibição do contêiner de imagens](media/storage-upload-process-images/figure2.png)

No contêiner **miniaturas** no Portal do Azure, selecione a imagem carregada e selecione **Excluir** para excluir a imagem. Na parte dois da série, você automatizará a criação de imagens em miniatura, portanto, essa imagem de teste não será necessária.

A CDN pode ser habilitada para armazenar em cache o conteúdo da sua conta de armazenamento do Azure. Embora não esteja descrito nesse tutorial, para saber como habilitar a CDN com sua conta de armazenamento do Azure, você pode visitar: [Integrar uma conta de armazenamento do Azure com a CDN do Azure](../../cdn/cdn-create-a-storage-account-with-cdn.md).

## <a name="next-steps"></a>Próximas etapas

Na primeira parte da série, você aprendeu sobre como configurar um aplicativo Web interagindo com o armazenamento, por exemplo, como:

> [!div class="checklist"]
> * Criar uma conta de armazenamento
> * Criar um contêiner e definir permissões
> * Recuperar uma chave de acesso
> * Definir as configurações do aplicativo
> * Implantar um aplicativo Web no Azure
> * Interagir com o aplicativo Web

Prossiga para a parte dois da série para saber mais sobre como usar a Grade de Eventos para disparar uma função do Azure para redimensionar uma imagem.

> [!div class="nextstepaction"]
> [Usar a Grade de Eventos para disparar uma função do Azure para redimensionar uma imagem carregada](../../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
