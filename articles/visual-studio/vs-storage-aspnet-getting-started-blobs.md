---
title: "Introdução ao Armazenamento de Blobs do Azure e aos Serviços Conectados do Visual Studio (ASP.NET) | Microsoft Docs"
description: "Como começar a usar o Armazenamento de Blobs do Azure em um projeto ASP.NET no Visual Studio após a conexão a uma conta de armazenamento usando os Serviços Conectados do Visual Studio"
services: storage
documentationcenter: 
author: kraigb
manager: ghogen
editor: 
ms.assetid: b3497055-bef8-4c95-8567-181556b50d95
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 12/21/2016
ms.author: kraig
ms.openlocfilehash: e953c7978705379a28581213e8f1c665473ddd60
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet"></a>Introdução ao Armazenamento de Blobs do Azure e aos Serviços Conectados do Visual Studio (ASP.NET)
[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Visão geral

O Armazenamento de Blobs do Azure é um serviço que armazena dados não estruturados na nuvem como objetos/blobs. O Armazenamento de Blobs pode conter qualquer tipo de texto ou de dados binários, como um documento, um arquivo de mídia ou um instalador de aplicativo. O Armazenamento de Blobs também é chamado de armazenamento de objetos.

Este tutorial mostra como gravar um código ASP.NET para alguns cenários comuns usando o Armazenamento de Blobs do Azure. Entre os cenários estão a criação de um contêiner de blobs, bem como carregamento, listagem, download e exclusão de blobs.

##<a name="prerequisites"></a>Pré-requisitos

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Conta de armazenamento do Azure](../storage/common/storage-create-storage-account.md#create-a-storage-account)

[!INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/vs-storage-aspnet-getting-started-create-azure-account.md)]

[!INCLUDE [storage-development-environment-include](../../includes/vs-storage-aspnet-getting-started-setup-dev-env.md)]

### <a name="create-an-mvc-controller"></a>Criar um controlador MVC 

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse em **Controladores** e, no menu de contexto, selecione **Adicionar-> Controlador**.

    ![Adicionar um controlador a um aplicativo ASP.NET MVC](./media/vs-storage-aspnet-getting-started-blobs/add-controller-menu.png)

1. Na caixa de diálogo **Adicionar Scaffold**, clique em **Controlador MVC 5 – Vazio** e selecione **Adicionar**.

    ![Especificar o tipo de controlador MVC](./media/vs-storage-aspnet-getting-started-blobs/add-controller.png)

1. Na caixa de diálogo **Adicionar Controlador**, nomeie o controlador *BlobsController* e selecione **Adicionar**.

    ![Dê um nome ao controlador MVC](./media/vs-storage-aspnet-getting-started-blobs/add-controller-name.png)

1. Adicione as seguintes diretivas *using* ao arquivo `BlobsController.cs`:

    ```csharp
    using Microsoft.Azure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

## <a name="create-a-blob-container"></a>Criar um contêiner de blob

Um contêiner de blobs é uma hierarquia aninhada de blobs e as pastas. As seguintes etapas ilustram como criar um contêiner de blobs:

> [!NOTE]
> 
> O código nesta seção pressupõe que você concluiu as etapas na seção, [Configurar o ambiente de desenvolvimento](#set-up-the-development-environment). 

1. Abra o arquivo `BlobsController.cs` .

1. Adicione um método chamado **CreateBlobContainer** que retorna um **ActionResult**.

    ```csharp
    public ActionResult CreateBlobContainer()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Dentro do método **CreateBlobContainer** obtenha um objeto **CloudStorageAccount** que represente as informações de sua conta de armazenamento. Use o seguinte código para obter a cadeia de conexão de armazenamento e informações de conta de armazenamento da configuração do serviço do Azure. (Mude *&lt;nome-da-conta-de-armazenamento>* para o nome da conta de armazenamento do Azure que você está acessando.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Obtenha um objeto **CloudBlobClient** que representa um cliente do serviço de blob.
   
    ```csharp
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    ```

1. Obtenha um objeto **CloudBlobContainer** que representa uma referência ao nome do contêiner de blobs desejado. O método **CloudBlobClient.GetContainerReference** não faz uma solicitação ao Armazenamento de Blobs. A referência retorna mesmo se o contêiner de blobs não existir. 
   
    ```csharp
    CloudBlobContainer container = blobClient.GetContainerReference("test-blob-container");
    ```

1. Chame o método **CloudBlobContainer.CreateIfNotExists** para criar o contêiner se ele ainda não existir. O método **CloudBlobContainer.CreateIfNotExists** retornará **true** se o contêiner não existir e for criado com êxito. Caso contrário, **false** será retornado.    

    ```csharp
    ViewBag.Success = container.CreateIfNotExists();
    ```

1. Atualize o **ViewBag** com o nome do contêiner de blobs.

    ```csharp
    ViewBag.BlobContainerName = container.Name;
    ```

1. No **Gerenciador de Soluções**, expanda a pasta **Exibições**, clique com o botão direito do mouse em **Blobs** e, no menu de contexto, selecione **Adicionar-> Exibir**.

1. Na caixa de diálogo **Adicionar Exibição**, digite **CreateBlobContainer** para o nome da exibição e selecione **Adicionar**.

1. Abra `CreateBlobContainer.cshtml` e modifique-o para que se pareça com o seguinte trecho de código:

    ```csharp
    @{
        ViewBag.Title = "Create Blob Container";
    }
    
    <h2>Create Blob Container results</h2>

    Creation of @ViewBag.BlobContainerName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. No **Gerenciador de Soluções**, expanda a pasta **Exibições->Compartilhadas** e abra `_Layout.cshtml`.

1. Após o último **Html.ActionLink**, adicione o seguinte **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Create blob container", "CreateBlobContainer", "Blobs")</li>
    ```

1. Execute o aplicativo e selecione **Criar Contêiner de Blobs** para ver resultados semelhantes à seguinte captura de tela:
  
    ![Criar contêiner de blobs](./media/vs-storage-aspnet-getting-started-blobs/create-blob-container-results.png)

    Conforme mencionado anteriormente, o método **CloudBlobContainer.CreateIfNotExists** retornará **true** apenas quando o contêiner não existir e for criado. Portanto, se você executar o aplicativo quando o contêiner existir, o método retornará **false**. Para executar o aplicativo várias vezes, exclua o contêiner antes de executar o aplicativo novamente. A exclusão do contêiner pode ser feita com o método **CloudBlobContainer.Delete**. Também é possível excluir o contêiner usando o [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040) ou o [Gerenciador do Armazenamento do Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md).  

## <a name="upload-a-blob-into-a-blob-container"></a>Carregar um blob em um contêiner de blobs

Depois de [criar um contêiner de blobs](#create-a-blob-container), você pode carregar arquivos nele. Esta seção explica como carregar um arquivo local em um contêiner de blobs. Estas etapas presumem que você criou um contêiner de blobs chamado *test-blob-container*. 

> [!NOTE]
> 
> O código nesta seção pressupõe que você concluiu as etapas na seção, [Configurar o ambiente de desenvolvimento](#set-up-the-development-environment). 

1. Abra o arquivo `BlobsController.cs` .

1. Adicione um método chamado **UploadBlob** que retorna um **EmptyResult**.

    ```csharp
    public EmptyResult UploadBlob()
    {
        // The code in this section goes here.

        return new EmptyResult();
    }
    ```
 
1. Dentro do método **UploadBlob** obtenha um objeto **CloudStorageAccount** que represente as informações de sua conta de armazenamento. Use o seguinte código para obter as informações da cadeia de conexão e da conta de armazenamento da configuração de serviço do Azure: (Altere *&lt;nome-da-conta-de-armazenamento>* para o nome da conta de armazenamento do Azure que você está acessando.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Obtenha um objeto **CloudBlobClient** que representa um cliente do serviço de blob.
   
    ```csharp
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    ```

1. Obtenha um objeto **CloudBlobContainer** que representa uma referência ao nome do contêiner de blobs. 
   
    ```csharp
    CloudBlobContainer container = blobClient.GetContainerReference("test-blob-container");
    ```

1. Conforme explicado anteriormente, o Armazenamento do Azure oferece suporte a tipos diferentes de blob. Para recuperar uma referência a um blob de páginas, chame o método **CloudBlobContainer.GetPageBlobReference**. Para recuperar uma referência a um blob de blocos, chame o método **CloudBlobContainer.GetBlockBlobReference**. Normalmente, o blob de blocos é o tipo recomendado. (Mude <nome-do-blob>* para o nome que você quer dar ao blob após o carregamento.)

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference(<blob-name>);
    ```

1. Quando tiver uma referência de blob, você poderá transferir qualquer fluxo de dados para ele, chamando o método **UploadFromStream** do objeto de referência do blob. O método **UploadFromStream** cria o blob, se ele não existir, ou o substitui, se ele já existir. (Mude *&lt;file-to-upload>* para um caminho totalmente qualificado até o arquivo que você deseja carregar.)

    ```csharp
    using (var fileStream = System.IO.File.OpenRead(<file-to-upload>))
    {
        blob.UploadFromStream(fileStream);
    }
    ```

1. No **Gerenciador de Soluções**, expanda a pasta **Exibições**, clique com o botão direito do mouse em **Blobs** e, no menu de contexto, selecione **Adicionar-> Exibir**.

1. No **Gerenciador de Soluções**, expanda a pasta **Exibições->Compartilhadas** e abra `_Layout.cshtml`.

1. Após o último **Html.ActionLink**, adicione o seguinte **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Upload blob", "UploadBlob", "Blobs")</li>
    ```

1. Execute o aplicativo e selecione **Carregar blob**.  
  
Esta seção — [Listar os blobs em um contêiner de blobs](#list-the-blobs-in-a-blob-container) — ilustra como listar os blobs em um contêiner de blobs.    

## <a name="list-the-blobs-in-a-blob-container"></a>Listar os blobs em um contêiner de blobs

Esta seção ilustra como listar os blobs em um contêiner de blobs. O código de exemplo faz referência ao *test-blob-container* criado na seção, [Criar um contêiner de blobs](#create-a-blob-container).

> [!NOTE]
> 
> O código nesta seção pressupõe que você concluiu as etapas na seção, [Configurar o ambiente de desenvolvimento](#set-up-the-development-environment). 

1. Abra o arquivo `BlobsController.cs` .

1. Adicione um método chamado **ListBlobs** que retorna um **ActionResult**.

    ```csharp
    public ActionResult ListBlobs()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Dentro do método **ListBlobs** obtenha um objeto **CloudStorageAccount** que represente as informações de sua conta de armazenamento. Use o seguinte código para obter as informações da cadeia de conexão e da conta de armazenamento da configuração de serviço do Azure: (Altere *&lt;nome-da-conta-de-armazenamento>* para o nome da conta de armazenamento do Azure que você está acessando.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Obtenha um objeto **CloudBlobClient** que representa um cliente do serviço de blob.
   
    ```csharp
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    ```

1. Obtenha um objeto **CloudBlobContainer** que representa uma referência ao nome do contêiner de blobs. 
   
    ```csharp
    CloudBlobContainer container = blobClient.GetContainerReference("test-blob-container");
    ```

1. Para listar os blobs em um contêiner de blobs, use o método **CloudBlobContainer.ListBlobs**. O método **CloudBlobContainer.ListBlobs** retorna um objeto **IListBlobItem** convertido em um objeto **CloudBlockBlob**, **CloudPageBlob** ou **CloudBlobDirectory**. O trecho de código a seguir enumera todos os blobs em um contêiner de blobs. Cada blob é convertido no objeto apropriado com base no seu tipo, e seu nome (ou URI no caso de um **CloudBlobDirectory**) é adicionado a uma lista.

    ```csharp
    List<string> blobs = new List<string>();

    foreach (IListBlobItem item in container.ListBlobs(null, false))
    {
        if (item.GetType() == typeof(CloudBlockBlob))
        {
            CloudBlockBlob blob = (CloudBlockBlob)item;
            blobs.Add(blob.Name);
        }
        else if (item.GetType() == typeof(CloudPageBlob))
        {
            CloudPageBlob blob = (CloudPageBlob)item;
            blobs.Add(blob.Name);
        }
        else if (item.GetType() == typeof(CloudBlobDirectory))
        {
            CloudBlobDirectory dir = (CloudBlobDirectory)item;
            blobs.Add(dir.Uri.ToString());
        }
    }

    return View(blobs);
    ```

    Além dos blobs, os contêineres de blob podem conter diretórios. Vamos supor que você tenha um contêiner de blobs chamado *test-blob-container* com a seguinte hierarquia:

        foo.png
        dir1/bar.png
        dir2/baz.png

    Usando o exemplo de código anterior, a lista de cadeias de caracteres de **blobs** contém valores parecidos com os seguintes:

        foo.png
        <storage-account-url>/test-blob-container/dir1
        <storage-account-url>/test-blob-container/dir2

    Como você pode ver, a lista inclui apenas as entidades de nível superior; não as aninhadas (*bar.png* e *baz.png*). Para listar todas as entidades em um contêiner de blobs, você deve chamar o método **CloudBlobContainer.ListBlobs** e passar **true** para o parâmetro **useFlatBlobListing**.    

    ```csharp
    ...
    foreach (IListBlobItem item in container.ListBlobs(useFlatBlobListing:true))
    ...
    ```

    A configuração do parâmetro **useFlatBlobListing** como **true** retorna uma lista simples de todas as entidades no contêiner de blobs e produz os seguintes resultados:

        foo.png
        dir1/bar.png
        dir2/baz.png

1. No **Gerenciador de Soluções**, expanda a pasta **Exibições**, clique com o botão direito do mouse em **Blobs** e, no menu de contexto, selecione **Adicionar-> Exibir**.

1. Na caixa de diálogo **Adicionar Exibição**, insira **ListBlobs** para o nome da exibição e selecione **Adicionar**.

1. Abra `ListBlobs.cshtml` e modifique-o para que se pareça com o seguinte trecho de código:

    ```html
    @model List<string>
    @{
        ViewBag.Title = "List blobs";
    }
    
    <h2>List blobs</h2>
    
    <ul>
        @foreach (var item in Model)
        {
        <li>@item</li>
        }
    </ul>
    ```

1. No **Gerenciador de Soluções**, expanda a pasta **Exibições->Compartilhadas** e abra `_Layout.cshtml`.

1. Após o último **Html.ActionLink**, adicione o seguinte **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("List blobs", "ListBlobs", "Blobs")</li>
    ```

1. Execute o aplicativo e selecione **Listar blobs** para ver resultados semelhantes à seguinte captura de tela:
  
    ![Listagem de blobs](./media/vs-storage-aspnet-getting-started-blobs/listblobs.png)

## <a name="download-blobs"></a>Baixar blobs

Esta seção ilustra como baixar um blob e mantê-lo no armazenamento local ou ler o conteúdo em uma cadeia de caracteres. O código de exemplo faz referência ao *test-blob-container* criado na seção, [Criar um contêiner de blobs](#create-a-blob-container).

1. Abra o arquivo `BlobsController.cs` .

1. Adicione um método chamado **DownloadBlob** que retorna um **ActionResult**.

    ```csharp
    public EmptyResult DownloadBlob()
    {
        // The code in this section goes here.

        return new EmptyResult();
    }
    ```
 
1. Dentro do método **DownloadBlob** obtenha um objeto **CloudStorageAccount** que represente as informações de sua conta de armazenamento. Use o seguinte código para obter as informações da cadeia de conexão e da conta de armazenamento da configuração de serviço do Azure: (Altere *&lt;nome-da-conta-de-armazenamento>* para o nome da conta de armazenamento do Azure que você está acessando.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Obtenha um objeto **CloudBlobClient** que representa um cliente do serviço de blob.
   
    ```csharp
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    ```

1. Obtenha um objeto **CloudBlobContainer** que representa uma referência ao nome do contêiner de blobs. 
   
    ```csharp
    CloudBlobContainer container = blobClient.GetContainerReference("test-blob-container");
    ```

1. Obtém um objeto de referência de blob chamando o método **CloudBlobContainer.GetBlockBlobReference** ou **CloudBlobContainer.GetPageBlobReference**. (Mude *&lt;blob-name>* para o nome do blob que você está baixando.)

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference(<blob-name>);
    ```

1. Para baixar um blob, use o método **CloudBlockBlob.DownloadToStream** ou **CloudPageBlob.DownloadToStream**, dependendo do tipo de blob. O trecho de código a seguir usa o método **CloudBlockBlob.DownloadToStream** para transferir o conteúdo de um blob para um objeto de fluxo que é persistido em um arquivo local: (Altere *&lt;nome-do-arquivo-local>* para o nome de arquivo totalmente qualificado que representa onde você quer que o blob seja baixado.) 

    ```csharp
    using (var fileStream = System.IO.File.OpenWrite(<local-file-name>))
    {
        blob.DownloadToStream(fileStream);
    }
    ```

1. No **Gerenciador de Soluções**, expanda a pasta **Exibições->Compartilhadas** e abra `_Layout.cshtml`.

1. Após o último **Html.ActionLink**, adicione o seguinte **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Download blob", "DownloadBlob", "Blobs")</li>
    ```

1. Execute o aplicativo e selecione **Baixar blob** para baixar o blob. O blob especificado na chamada ao método **CloudBlobContainer.GetBlockBlobReference** baixa no local especificado na chamada ao método **File.OpenWrite**. 

## <a name="delete-blobs"></a>Excluir blobs

As seguintes etapas ilustram como excluir um blob:

> [!NOTE]
> 
> O código nesta seção pressupõe que você concluiu as etapas na seção, [Configurar o ambiente de desenvolvimento](#set-up-the-development-environment). 

1. Abra o arquivo `BlobsController.cs` .

1. Adicione um método chamado **DeleteBlob** que retorna um **ActionResult**.

    ```csharp
    public EmptyResult DeleteBlob()
    {
        // The code in this section goes here.

        return new EmptyResult();
    }
    ```

1. Obtenha um objeto **CloudStorageAccount** que represente as informações da conta de armazenamento. Use o seguinte código para obter as informações da cadeia de conexão e da conta de armazenamento da configuração de serviço do Azure: (Altere *&lt;nome-da-conta-de-armazenamento>* para o nome da conta de armazenamento do Azure que você está acessando.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Obtenha um objeto **CloudBlobClient** que representa um cliente do serviço de blob.
   
    ```csharp
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    ```

1. Obtenha um objeto **CloudBlobContainer** que representa uma referência ao nome do contêiner de blobs. 
   
    ```csharp
    CloudBlobContainer container = blobClient.GetContainerReference("test-blob-container");
    ```

1. Obtém um objeto de referência de blob chamando o método **CloudBlobContainer.GetBlockBlobReference** ou **CloudBlobContainer.GetPageBlobReference**. (Mude *&lt;blob-name>* para o nome do blob que você está excluindo.)

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference(<blob-name>);
        ```

1. To delete a blob, use the **Delete** method.

    ```csharp
    blob.Delete();
    ```

1. No **Gerenciador de Soluções**, expanda a pasta **Exibições->Compartilhadas** e abra `_Layout.cshtml`.

1. Após o último **Html.ActionLink**, adicione o seguinte **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Delete blob", "DeleteBlob", "Blobs")</li>
    ```

1. Execute o aplicativo e selecione **Excluir blob** para excluir o blob especificado na chamada do método **CloudBlobContainer.GetBlockBlobReference**. 

## <a name="next-steps"></a>Próximas etapas
Consulte outros guias de recursos para obter informações sobre opções adicionais para armazenar dados no Azure.

  * [Introdução ao Armazenamento de Tabelas do Azure e aos Serviços Conectados do Visual Studio (ASP.NET)](vs-storage-aspnet-getting-started-tables.md)
  * [Introdução ao Armazenamento de Filas do Azure e aos Serviços Conectados do Visual Studio (ASP.NET)](vs-storage-aspnet-getting-started-queues.md)
