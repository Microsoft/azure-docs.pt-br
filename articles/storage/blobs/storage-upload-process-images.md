---
title: Carregar dados de imagem na nuvem com o Armazenamento do Azure | Microsoft Docs
description: Use o armazenamento de Blobs do Azure com aplicativos Web para armazenar dados de aplicativo em uma conta de armazenamento. Este tutorial cria um aplicativo Web que armazena e exibe imagens do armazenamento do Azure.
author: twooley
ms.service: storage
ms.subservice: blobs
ms.topic: tutorial
ms.date: 06/24/2020
ms.author: twooley
ms.reviewer: dineshm
ms.custom: devx-track-js, devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: f751b93a7d298763da5a37a54f400d6fc56a58e3
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106277956"
---
# <a name="tutorial-upload-image-data-in-the-cloud-with-azure-storage"></a>Tutorial: Carregar dados de imagem na nuvem com o Armazenamento do Azure

Este tutorial é a primeira parte de uma série. Neste tutorial, você aprenderá a implantar um aplicativo Web. O aplicativo Web usa a biblioteca de clientes do Armazenamento de Blobs do Azure para carregar imagens para uma conta de armazenamento. Ao terminar, você terá um aplicativo Web que armazena e exibe imagens do Armazenamento do Azure.

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

![Aplicativo de redimensionador de imagem no .NET](media/storage-upload-process-images/figure2.png)

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

![Aplicativo de redimensionador de imagem no JavaScript](media/storage-upload-process-images/upload-app-nodejs-thumb.png)

---

Na primeira parte da série, você aprenderá a:

> [!div class="checklist"]

> * Criar uma conta de armazenamento
> * Criar um contêiner e definir permissões
> * Recuperar uma chave de acesso
> * Implantar um aplicativo Web no Azure
> * Definir configurações de aplicativo
> * Interagir com o aplicativo Web

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisa de uma assinatura do Azure. Crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Para instalar e usar a CLI localmente, execute a CLI do Azure versão 2.0.4 ou posterior. Execute `az --version` para encontrar a versão. Se você precisar instalar ou atualizar, confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli). 

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](/cli/azure/group). Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados.  

O seguinte exemplo cria um grupo de recursos chamado `myResourceGroup`.

```azurecli
az group create --name myResourceGroup --location southeastasia
```

```powershell
az group create --name myResourceGroup --location southeastasia
```

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

O exemplo carrega imagens em um contêiner de blobs em uma conta de armazenamento do Azure. Uma conta de armazenamento fornece um namespace exclusivo para armazenar e acessar os objetos de dados do Armazenamento do Azure. Crie uma conta de armazenamento no grupo de recursos que você criou ao utilizar o comando [az storage account create](/cli/azure/storage/account).

> [!IMPORTANT]
> Na parte 2 do tutorial, você usa o Grade de Eventos do Azure com o armazenamento de blobs. Crie sua conta de armazenamento em uma região do Azure que dá suporte à Grade de Eventos. Para obter uma lista das regiões com suporte, consulte [produtos Azure por região](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all).

No comando a seguir, substitua seu próprio nome global exclusivo da conta de armazenamento de blobs quando o espaço reservado `<blob_storage_account>` for exibido.

```azurecli
blobStorageAccount="<blob_storage_account>"

az storage account create --name $blobStorageAccount --location southeastasia \
  --resource-group myResourceGroup --sku Standard_LRS --kind StorageV2 --access-tier hot
```

```powershell
$blobStorageAccount="<blob_storage_account>"

az storage account create --name $blobStorageAccount --location southeastasia `
  --resource-group myResourceGroup --sku Standard_LRS --kind StorageV2 --access-tier hot
```

## <a name="create-blob-storage-containers"></a>Criar contêineres de armazenamento de blobs

O aplicativo usa dois contêineres na conta de armazenamento de blobs. Os contêineres são semelhantes às pastas e armazenam blobs. O contêiner de *imagens* é onde o aplicativo carrega as imagens de alta resolução. Em uma parte posterior da série, um aplicativo de funções do Azure carrega miniaturas de imagem redimensionada para o contêiner *miniaturas*.

Obtenha a chave de conta de armazenamento usando o comando [az storage account keys list](/cli/azure/storage/account/keys). Depois use essa chave para criar dois contêineres usando o comando [az storage container create](/cli/azure/storage/container).

O acesso público do contêiner de *imagens* é definido como `off`. O acesso público do contêiner de *miniaturas* é definido como `container`. A configuração de acesso público do `container` permite que os usuários que visitam a página da Web exibir as miniaturas.

```bash
blobStorageAccountKey=$(az storage account keys list -g myResourceGroup \
  -n $blobStorageAccount --query "[0].value" --output tsv)

az storage container create --name images \
  --account-name $blobStorageAccount \
  --account-key $blobStorageAccountKey

az storage container create --name thumbnails \
  --account-name $blobStorageAccount \
  --account-key $blobStorageAccountKey --public-access container
```

```powershell
$blobStorageAccountKey=$(az storage account keys list -g myResourceGroup `
  -n $blobStorageAccount --query "[0].value" --output tsv)

az storage container create --name images `
  --account-name $blobStorageAccount `
  --account-key $blobStorageAccountKey

az storage container create --name thumbnails `
  --account-name $blobStorageAccount `
  --account-key $blobStorageAccountKey --public-access container
```

Anote o nome e a chave da sua conta de armazenamento de blobs. O aplicativo de exemplo usa essas configurações para se conectar à conta de armazenamento para carregar as imagens. 

## <a name="create-an-app-service-plan"></a>Criar um plano de Serviço de Aplicativo

Um [plano do Serviço de Aplicativo](../../app-service/overview-hosting-plans.md) especifica o local, tamanho e recursos do farm de servidores Web que hospeda o aplicativo.

Criar um plano do Serviço de Aplicativo com o comando [az appservice plan create](/cli/azure/appservice/plan).

O exemplo a seguir cria um plano do Serviço de Aplicativo denominado `myAppServicePlan` usando o tipo de preço **Gratuita**:

```azurecli
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku Free
```

```powershell
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku Free
```

## <a name="create-a-web-app"></a>Criar um aplicativo Web

O aplicativo Web fornece um espaço de hospedagem para o código do aplicativo de exemplo que é implantado do repositório de exemplo do GitHub. Crie um [aplicativo Web](../../app-service/overview.md) no plano do Serviço de Aplicativo do `myAppServicePlan` com o comando [az webapp create](/cli/azure/webapp).  

No comando a seguir, substitua `<web_app>` por um nome exclusivo. Os caracteres válidos são `a-z`, `0-9` e `-`. Se `<web_app>` não for exclusivo, você receberá a mensagem de erro: *O site com o nome `<web_app>` atribuído já existe.* A URL padrão do aplicativo Web é `https://<web_app>.azurewebsites.net`.  

```azurecli
webapp="<web_app>"

az webapp create --name $webapp --resource-group myResourceGroup --plan myAppServicePlan
```

```powershell
$webapp="<web_app>"

az webapp create --name $webapp --resource-group myResourceGroup --plan myAppServicePlan
```

## <a name="deploy-the-sample-app-from-the-github-repository"></a>Implantar o aplicativo de exemplo do repositório do GitHub

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

Serviço de Aplicativo dá suporte a várias maneiras de implantar o conteúdo em um aplicativo Web. Neste tutorial, você implanta o aplicativo Web de um [repositório de exemplo do GitHub público](https://github.com/Azure-Samples/storage-blob-upload-from-webapp). Configure a implantação do GitHub para o aplicativo Web com o comando [az webapp deployment source config](/cli/azure/webapp/deployment/source).

O projeto de exemplo contém um aplicativo [ASP.NET MVC](https://www.asp.net/mvc). O aplicativo aceita uma imagem, salva-a em uma conta de armazenamento e exibe imagens de um contêiner de miniaturas. O aplicativo Web usa os namespaces [Azure.Storage](/dotnet/api/azure.storage), [Azure.Storage.Blobs](/dotnet/api/azure.storage.blobs) e [Azure.Storage.Blobs.Models](/dotnet/api/azure.storage.blobs.models) para interagir com o serviço de Armazenamento do Azure.

```azurecli
az webapp deployment source config --name $webapp --resource-group myResourceGroup \
  --branch master --manual-integration \
  --repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
```

```powershell
az webapp deployment source config --name $webapp --resource-group myResourceGroup `
  --branch master --manual-integration `
  --repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
```

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

Serviço de Aplicativo dá suporte a várias maneiras de implantar o conteúdo em um aplicativo Web. Neste tutorial, você implanta o aplicativo Web de um [repositório de exemplo do GitHub público](https://github.com/Azure-Samples/azure-sdk-for-js-storage-blob-stream-nodejs). Configure a implantação do GitHub para o aplicativo Web com o comando [az webapp deployment source config](/cli/azure/webapp/deployment/source).

```azurecli
az webapp deployment source config --name $webapp --resource-group myResourceGroup \
  --branch master --manual-integration \
  --repo-url https://github.com/Azure-Samples/azure-sdk-for-js-storage-blob-stream-nodejs
```

```powershell
az webapp deployment source config --name $webapp --resource-group myResourceGroup `
  --branch master --manual-integration `
  --repo-url https://github.com/Azure-Samples/azure-sdk-for-js-storage-blob-stream-nodejs
```

---

## <a name="configure-web-app-settings"></a>Definir configurações do aplicativo Web

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

O aplicativo Web de exemplo usa as [APIs do Armazenamento do Azure para .NET](/dotnet/api/overview/azure/storage) para carregar imagens. As credenciais da conta de armazenamento são definidas nas configurações do aplicativo para o aplicativo Web. Adicione configurações de aplicativo ao aplicativo implantado com o comando [az webapp config appsettings set](/cli/azure/webapp/config/appsettings).

```azurecli
az webapp config appsettings set --name $webapp --resource-group myResourceGroup \
  --settings AzureStorageConfig__AccountName=$blobStorageAccount \
    AzureStorageConfig__ImageContainer=images \
    AzureStorageConfig__ThumbnailContainer=thumbnails \
    AzureStorageConfig__AccountKey=$blobStorageAccountKey
```

```powershell
az webapp config appsettings set --name $webapp --resource-group myResourceGroup `
  --settings AzureStorageConfig__AccountName=$blobStorageAccount `
    AzureStorageConfig__ImageContainer=images `
    AzureStorageConfig__ThumbnailContainer=thumbnails `
    AzureStorageConfig__AccountKey=$blobStorageAccountKey
```

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

O aplicativo Web de exemplo usa a [biblioteca de clientes do Armazenamento do Azure para JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage) para carregar imagens. As credenciais da conta de armazenamento são definidas nas configurações do aplicativo para o aplicativo Web. Adicione configurações de aplicativo ao aplicativo implantado com o comando [az webapp config appsettings set](/cli/azure/webapp/config/appsettings).

```azurecli
az webapp config appsettings set --name $webapp --resource-group myResourceGroup \
  --settings AZURE_STORAGE_ACCOUNT_NAME=$blobStorageAccount \
    AZURE_STORAGE_ACCOUNT_ACCESS_KEY=$blobStorageAccountKey
```

```powershell
az webapp config appsettings set --name $webapp --resource-group myResourceGroup `
  --settings AZURE_STORAGE_ACCOUNT_NAME=$blobStorageAccount `
  AZURE_STORAGE_ACCOUNT_ACCESS_KEY=$blobStorageAccountKey
```

---

Depois de implantar e configurar o aplicativo Web, você pode testar a funcionalidade de upload de imagem no aplicativo.

## <a name="upload-an-image"></a>Carregar uma imagem

Para testar o aplicativo Web, navegue para a URL do aplicativo publicado. A URL padrão do aplicativo Web é `https://<web_app>.azurewebsites.net`.

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

Selecione a região **Carregar fotos** para especificar e carregar um arquivo ou arraste um arquivo para a região. A imagem desaparece se for carregada com êxito. A seção **Miniaturas Geradas** permanecerá vazia até a testarmos mais adiante neste tutorial.

![Carregar fotos no .NET](media/storage-upload-process-images/figure1.png)

No código de exemplo, a tarefa `UploadFileToStorage` no arquivo *Storagehelper.cs* é usada para carregar as imagens no contêiner de *imagens* dentro da conta de armazenamento usando o método [UploadAsync](/dotnet/api/azure.storage.blobs.blobclient.uploadasync). O exemplo de código a seguir contém a tarefa `UploadFileToStorage`.

```csharp
public static async Task<bool> UploadFileToStorage(Stream fileStream, string fileName,
                                                    AzureStorageConfig _storageConfig)
{
    // Create a URI to the blob
    Uri blobUri = new Uri("https://" +
                          _storageConfig.AccountName +
                          ".blob.core.windows.net/" +
                          _storageConfig.ImageContainer +
                          "/" + fileName);

    // Create StorageSharedKeyCredentials object by reading
    // the values from the configuration (appsettings.json)
    StorageSharedKeyCredential storageCredentials =
        new StorageSharedKeyCredential(_storageConfig.AccountName, _storageConfig.AccountKey);

    // Create the blob client.
    BlobClient blobClient = new BlobClient(blobUri, storageCredentials);

    // Upload the file
    await blobClient.UploadAsync(fileStream);

    return await Task.FromResult(true);
}
```

As seguintes classes e métodos são usados na tarefa anterior:

| Classe | Método |
|-------|--------|
| [Uri](/dotnet/api/system.uri) | [Construtor Uri](/dotnet/api/system.uri.-ctor) |
| [StorageSharedKeyCredential](/dotnet/api/azure.storage.storagesharedkeycredential) | [Construtor StorageSharedKeyCredential(String, String)](/dotnet/api/azure.storage.storagesharedkeycredential.-ctor) |
| [BlobClient](/dotnet/api/azure.storage.blobs.blobclient) | [UploadAsync](/dotnet/api/azure.storage.blobs.blobclient.uploadasync) |

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

Selecione **Escolher Arquivo** para selecionar um arquivo e, em seguida, clique em **Carregar Imagem**. A seção **Miniaturas Geradas** permanecerá vazia até a testarmos mais adiante neste tutorial.

![Carregar fotos no Node.js](media/storage-upload-process-images/upload-app-nodejs.png)

No código de exemplo, a rota `post` é responsável por carregar a imagem em um contêiner de blob. A rota usa os módulos para ajudar a processar o upload:

- O [multer](https://github.com/expressjs/multer) implementa a estratégia de upload para o manipulador de rotas.
- [into-stream](https://github.com/sindresorhus/into-stream) converte o buffer em um fluxo, conforme exigido por [uploadStream](/javascript/api/%40azure/storage-blob/blockblobclient#uploadstream-readable--number--number--blockblobuploadstreamoptions-).

Como o arquivo é enviado para a rota, o conteúdo do arquivo permanece na memória até o arquivo ser carregado no contêiner de blobs.

> [!IMPORTANT]
> Carregar arquivos grandes na memória pode ter um efeito negativo sobre o desempenho do seu aplicativo Web. Se você espera que os usuários publiquem arquivos grandes, convém considerar a preparação dos arquivos no sistema de arquivos do servidor Web e, em seguida, agendar uploads no armazenamento de blobs. Assim que os arquivos estiverem no armazenamento de blobs, é possível removê-los do sistema de arquivos do servidor.

```javascript
if (process.env.NODE_ENV !== 'production') {
  require('dotenv').config();
}

const {
  BlobServiceClient,
  StorageSharedKeyCredential,
  newPipeline
} = require('@azure/storage-blob');

const express = require('express');
const router = express.Router();
const containerName1 = 'thumbnails';
const multer = require('multer');
const inMemoryStorage = multer.memoryStorage();
const uploadStrategy = multer({ storage: inMemoryStorage }).single('image');
const getStream = require('into-stream');
const containerName2 = 'images';
const ONE_MEGABYTE = 1024 * 1024;
const uploadOptions = { bufferSize: 4 * ONE_MEGABYTE, maxBuffers: 20 };

const sharedKeyCredential = new StorageSharedKeyCredential(
  process.env.AZURE_STORAGE_ACCOUNT_NAME,
  process.env.AZURE_STORAGE_ACCOUNT_ACCESS_KEY);
const pipeline = newPipeline(sharedKeyCredential);

const blobServiceClient = new BlobServiceClient(
  `https://${process.env.AZURE_STORAGE_ACCOUNT_NAME}.blob.core.windows.net`,
  pipeline
);

const getBlobName = originalName => {
  // Use a random number to generate a unique file name, 
  // removing "0." from the start of the string.
  const identifier = Math.random().toString().replace(/0\./, '');
  return `${identifier}-${originalName}`;
};

router.get('/', async (req, res, next) => {

  let viewData;

  try {
    const containerClient = blobServiceClient.getContainerClient(containerName1);
    const listBlobsResponse = await containerClient.listBlobFlatSegment();

    for await (const blob of listBlobsResponse.segment.blobItems) {
      console.log(`Blob: ${blob.name}`);
    }

    viewData = {
      title: 'Home',
      viewName: 'index',
      accountName: process.env.AZURE_STORAGE_ACCOUNT_NAME,
      containerName: containerName1
    };

    if (listBlobsResponse.segment.blobItems.length) {
      viewData.thumbnails = listBlobsResponse.segment.blobItems;
    }
  } catch (err) {
    viewData = {
      title: 'Error',
      viewName: 'error',
      message: 'There was an error contacting the blob storage container.',
      error: err
    };
    res.status(500);
  } finally {
    res.render(viewData.viewName, viewData);
  }
});

router.post('/', uploadStrategy, async (req, res) => {
  const blobName = getBlobName(req.file.originalname);
  const stream = getStream(req.file.buffer);
  const containerClient = blobServiceClient.getContainerClient(containerName2);;
  const blockBlobClient = containerClient.getBlockBlobClient(blobName);

  try {
    await blockBlobClient.uploadStream(stream,
      uploadOptions.bufferSize, uploadOptions.maxBuffers,
      { blobHTTPHeaders: { blobContentType: "image/jpeg" } });
    res.render('success', { message: 'File uploaded to Azure Blob storage.' });
  } catch (err) {
    res.render('error', { message: err.message });
  }
});

module.exports = router;
```

---

## <a name="verify-the-image-is-shown-in-the-storage-account"></a>Verifique se a imagem é mostrada na conta de armazenamento

Entre no [portal do Azure](https://portal.azure.com). No menu à esquerda, selecione **Contas de armazenamento**, em seguida, selecione o nome da sua conta de armazenamento. Escolha **Contêineres** e, em seguida, escolha o contêiner **imagens**.

Verifique se a imagem é mostrada no contêiner.

![Listagem do portal do Azure do contêiner de imagens](media/storage-upload-process-images/figure13.png)

## <a name="test-thumbnail-viewing"></a>Exibição de miniaturas de teste

Para testar a exibição de miniaturas, você carregará uma imagem no contêiner de **miniaturas** para verificar se o aplicativo pode ler o contêiner de **miniaturas**.

Entre no [portal do Azure](https://portal.azure.com). No menu à esquerda, selecione **Contas de armazenamento**, em seguida, selecione o nome da sua conta de armazenamento. Escolha **Contêineres** e, em seguida, escolha o contêiner **miniaturas**. Selecione **Carregar** para abrir o painel **Carregar blob**.

Escolha um arquivo com o seletor de arquivos e selecione **Carregar**.

Navegue de volta para seu aplicativo para verificar se a imagem carregada para o contêiner **miniaturas** está visível.

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

![Aplicativo de redimensionamento de imagem do .NET com a nova imagem exibida](media/storage-upload-process-images/figure2.png)

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

![Aplicativo de redimensionamento de imagem do Node.js com a nova imagem exibida](media/storage-upload-process-images/upload-app-nodejs-thumb.png)

---

Na parte dois da série, você automatiza a criação de imagens de miniaturas, de modo que essa imagem não seja necessária. No contêiner **miniaturas**, selecione a imagem carregada e selecione **Excluir** para remover a imagem.

Você pode habilitar a CDN (Rede de Distribuição de Conteúdo) para armazenar em cache o conteúdo da conta de armazenamento do Azure. Para obter mais informações, confira [Integrar uma conta de armazenamento do Azure com a CDN do Azure](../../cdn/cdn-create-a-storage-account-with-cdn.md).

## <a name="next-steps"></a>Próximas etapas

Na primeira parte da série, você aprendeu a configurar um aplicativo Web para interagir com o armazenamento.

Prossiga para a parte dois da série para saber mais sobre como usar a Grade de Eventos para disparar uma função do Azure para redimensionar uma imagem.

> [!div class="nextstepaction"]
> [Usar a Grade de Eventos para disparar uma função do Azure para redimensionar uma imagem carregada](../../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
