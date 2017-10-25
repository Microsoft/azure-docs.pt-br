---
title: "Introdução ao Armazenamento de Filas do Azure e aos Serviços Conectados do Visual Studio (ASP.NET) | Microsoft Docs"
description: "Como começar a usar o Armazenamento de Filas do Azure em um projeto do ASP.NET no Visual Studio após a conexão a uma conta de armazenamento usando os Serviços Conectados do Visual Studio"
services: storage
documentationcenter: 
author: kraigb
manager: ghogen
editor: 
ms.assetid: 94ca3413-5497-433f-abbe-836f83a9de72
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 12/23/2016
ms.author: kraigb
ms.openlocfilehash: 4687e5dfce72583728068c176d86d100313badf6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-queue-storage-and-visual-studio-connected-services-aspnet"></a>Introdução ao Armazenamento de Filas do Azure e aos Serviços Conectados do Visual Studio (ASP.NET)
[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Visão geral

O Armazenamento de Filas do Azure fornece mensagens na nuvem entre os componentes do aplicativo. Na criação de aplicativos para escala, os componentes do aplicativo geralmente são desassociados, para que possam ser redimensionados independentemente. O armazenamento de filas fornece mensagens assíncronas para a comunicação entre os componentes do aplicativo, estando eles em execução na nuvem, na área de trabalho, em um servidor local ou em um dispositivo móvel. O armazenamento de Fila também dá suporte ao gerenciamento de tarefas assíncronas e à criação de fluxos de trabalho do processo.

Este tutorial mostra como gravar código ASP.NET para alguns cenários comuns usando entidades do Armazenamento de Filas do Azure. Esses cenários incluem tarefas comuns como criar uma fila do Azure e adicionar, modificar, ler e remover mensagens da fila.

##<a name="prerequisites"></a>Pré-requisitos

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Conta de armazenamento do Azure](../storage/common/storage-create-storage-account.md#create-a-storage-account)

[!INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/vs-storage-aspnet-getting-started-create-azure-account.md)]

[!INCLUDE [storage-development-environment-include](../../includes/vs-storage-aspnet-getting-started-setup-dev-env.md)]

### <a name="create-an-mvc-controller"></a>Criar um controlador MVC 

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse em **Controladores** e, no menu de contexto, selecione **Adicionar-> Controlador**.

    ![Adicionar um controlador a um aplicativo ASP.NET MVC](./media/vs-storage-aspnet-getting-started-queues/add-controller-menu.png)

1. Na caixa de diálogo **Adicionar Scaffold**, clique em **Controlador MVC 5 – Vazio** e selecione **Adicionar**.

    ![Especificar o tipo de controlador MVC](./media/vs-storage-aspnet-getting-started-queues/add-controller.png)

1. Na caixa de diálogo **Adicionar Controlador**, nomeie o controlador como *QueuesController* e selecione **Adicionar**.

    ![Dê um nome ao controlador MVC](./media/vs-storage-aspnet-getting-started-queues/add-controller-name.png)

1. Adicione as seguintes diretivas *using* ao arquivo `QueuesController.cs`:

    ```csharp
    using Microsoft.Azure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Queue;
    ```
## <a name="create-a-queue"></a>Criar uma fila

As etapas a seguir ilustram como criar uma fila:

> [!NOTE]
> 
> Esta seção pressupõe que você concluiu as etapas [Configurar o ambiente de desenvolvimento](#set-up-the-development-environment). 

1. Abra o arquivo `QueuesController.cs` . 

1. Adicione um método chamado **CreateQueue** que retorna um **ActionResult**.

    ```csharp
    public ActionResult CreateQueue()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. Dentro do método **CreateQueue**, obtenha um objeto **CloudStorageAccount** que representa as informações da sua conta de armazenamento. Use o seguinte código para obter as informações da cadeia de conexão e conta de armazenamento da configuração de serviço do Azure: (Altere *&lt;nome-da-conta-de-armazenamento>* para o nome da conta de armazenamento do Azure que você está acessando.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Obtenha um objeto **CloudQueueClient** que representa um cliente do serviço de fila.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```
1. Obtenha um objeto **CloudQueue** que representa uma referência ao nome da fila desejada. O método **CloudQueueClient.GetQueueReference** não faz uma solicitação para o Armazenamento de Filas. A referência é retornada mesmo se as filas não existirem. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Chame o método **CloudQueue.CreateIfNotExists** para criar a fila se ela ainda não existir. O método **CloudQueue.CreateIfNotExists** retorna **true** se a fila não existir e for criada com êxito. Caso contrário, **false** será retornado.    

    ```csharp
    ViewBag.Success = queue.CreateIfNotExists();
    ```

1. Atualize o **ViewBag** com o nome da fila.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ```

1. No **Gerenciador de Soluções**, expanda a pasta **Exibições**, clique com o botão direito do mouse em **Filas** e, no menu de contexto, selecione **Adicionar->Exibição**.

1. Na caixa de diálogo **Adicionar Exibição**, insira **CreateQueue** para o nome de exibição e selecione **Adicionar**.

1. Abra `CreateQueue.cshtml` e modifique-o para que se pareça com o seguinte trecho de código:

    ```csharp
    @{
        ViewBag.Title = "Create Queue";
    }
    
    <h2>Create Queue results</h2>

    Creation of @ViewBag.QueueName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. No **Gerenciador de Soluções**, expanda a pasta **Exibições->Compartilhadas** e abra `_Layout.cshtml`.

1. Após o último **Html.ActionLink**, adicione o seguinte **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Create queue", "CreateQueue", "Queues")</li>
    ```

1. Execute o aplicativo e selecione **Criar fila** para ver resultados semelhantes à seguinte captura de tela:
  
    ![Criar fila](./media/vs-storage-aspnet-getting-started-queues/create-queue-results.png)

    Conforme mencionado anteriormente, o método **CloudQueue.CreateIfNotExists** retornará **true** apenas quando a fila não existir e for criada. Portanto, se você executar o aplicativo quando a fila existir, o método retornará **false**. Para executar o aplicativo várias vezes, você deverá excluir a fila antes de executar o aplicativo novamente. É possível excluir a fila por meio do método **CloudQueue.Delete**. Também é possível excluir a fila usando o [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040) ou o [Gerenciador de Armazenamento do Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md).  

## <a name="add-a-message-to-a-queue"></a>Adicionar uma mensagem a uma fila

Depois que você tiver [criado uma fila](#create-a-queue), poderá adicionar mensagens a ela. Esta seção orientará você pela adição de uma mensagem para uma fila *test-queue*. 

> [!NOTE]
> 
> Esta seção pressupõe que você concluiu as etapas [Configurar o ambiente de desenvolvimento](#set-up-the-development-environment). 

1. Abra o arquivo `QueuesController.cs` .

1. Adicione um método chamado **AddMessage** que retorna um **ActionResult**.

    ```csharp
    public ActionResult AddMessage()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Dentro do método **AddMessage**, obtenha um objeto **CloudStorageAccount** que representa as informações da sua conta de armazenamento. Use o seguinte código para obter as informações da cadeia de conexão e conta de armazenamento da configuração de serviço do Azure: (Altere *&lt;nome-da-conta-de-armazenamento>* para o nome da conta de armazenamento do Azure que você está acessando.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Obtenha um objeto **CloudQueueClient** que representa um cliente do serviço de fila.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Obtenha um objeto **CloudQueueContainer** que representa uma referência à fila. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Crie o objeto **CloudQueueMessage** que representa a mensagem que você deseja adicionar à fila. Um objeto **CloudQueueMessage** pode ser criado por meio de uma cadeia de caracteres (em formato UTF-8) ou de uma matriz de bytes.

    ```csharp
    CloudQueueMessage message = new CloudQueueMessage("Hello, Azure Queue Storage");
    ```

1. Chame o método **CloudQueue.AddMessage** para adicionar a mensagem à fila.

    ```csharp
    queue.AddMessage(message);
    ```

1. Crie e configure algumas propriedades **ViewBag** para serem mostradas na exibição.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ViewBag.Message = message.AsString;
    ```

1. No **Gerenciador de Soluções**, expanda a pasta **Exibições**, clique com o botão direito do mouse em **Filas** e, no menu de contexto, selecione **Adicionar->Exibição**.

1. Na caixa de diálogo **Adicionar Exibição**, digite **AddMessage** para o nome da exibição e selecione **Adicionar**.

1. Abra `AddMessage.cshtml` e modifique-o para que se pareça com o seguinte trecho de código:

    ```csharp
    @{
        ViewBag.Title = "Add Message";
    }
    
    <h2>Add Message results</h2>
    
    The message '@ViewBag.Message' was added to the queue '@ViewBag.QueueName'.
    ```

1. No **Gerenciador de Soluções**, expanda a pasta **Exibições->Compartilhadas** e abra `_Layout.cshtml`.

1. Após o último **Html.ActionLink**, adicione o seguinte **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Add message", "AddMessage", "Queues")</li>
    ```

1. Execute o aplicativo e selecione **Adicionar mensagem** para ver resultados semelhantes à seguinte captura de tela:
  
    ![Adicionar mensagem](./media/vs-storage-aspnet-getting-started-queues/add-message-results.png)

As duas seções, [Ler uma mensagem de uma fila sem removê-la](#read-a-message-from-a-queue-without-removing-it) e [Ler e remover uma mensagem de uma fila](#read-and-remove-a-message-from-a-queue), ilustram como ler mensagens de uma fila.    

## <a name="read-a-message-from-a-queue-without-removing-it"></a>Ler uma mensagem de uma fila sem removê-la

As etapas a seguir ilustram como espiar uma mensagem enfileirada (ler a primeira mensagem sem removê-la).  

> [!NOTE]
> 
> Esta seção pressupõe que você concluiu as etapas [Configurar o ambiente de desenvolvimento](#set-up-the-development-environment). 

1. Abra o arquivo `QueuesController.cs` .

1. Adicione um método chamado **PeekMessage** que retorna um **ActionResult**.

    ```csharp
    public ActionResult PeekMessage()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Dentro do método **PeekMessage**, obtenha um objeto **CloudStorageAccount** que representa as informações da sua conta de armazenamento. Use o seguinte código para obter as informações da cadeia de conexão e conta de armazenamento da configuração de serviço do Azure: (Altere *&lt;nome-da-conta-de-armazenamento>* para o nome da conta de armazenamento do Azure que você está acessando.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Obtenha um objeto **CloudQueueClient** que representa um cliente do serviço de fila.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Obtenha um objeto **CloudQueueContainer** que representa uma referência à fila. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Chame o método **CloudQueue.PeekMessage** para ler a primeira mensagem na fila sem removê-la da fila. 

    ```csharp
    CloudQueueMessage message = queue.PeekMessage();
    ```

1. Atualize **ViewBag** com dois valores: o nome da fila e a mensagem que foi lida. O objeto **CloudQueueMessage** expõe duas propriedades para obter o valor do objeto: **CloudQueueMessage.AsBytes** ou **CloudQueueMessage.AsString**. **AsString** (usado neste exemplo) retorna uma cadeia de caracteres, enquanto **AsBytes** retorna uma matriz de bytes.

    ```csharp
    ViewBag.QueueName = queue.Name; 
    ViewBag.Message = (message != null ? message.AsString : "");
    ```

1. No **Gerenciador de Soluções**, expanda a pasta **Exibições**, clique com o botão direito do mouse em **Filas** e, no menu de contexto, selecione **Adicionar->Exibição**.

1. Na caixa de diálogo **Adicionar Exibição**, digite **PeekMessage** para o nome da exibição e selecione **Adicionar**.

1. Abra `PeekMessage.cshtml` e modifique-o para que se pareça com o seguinte trecho de código:

    ```csharp
    @{
        ViewBag.Title = "PeekMessage";
    }
    
    <h2>Peek Message results</h2>
    
    <table border="1">
        <tr><th>Queue</th><th>Peeked Message</th></tr>
        <tr><td>@ViewBag.QueueName</td><td>@ViewBag.Message</td></tr>
    </table>    
    ```

1. No **Gerenciador de Soluções**, expanda a pasta **Exibições->Compartilhadas** e abra `_Layout.cshtml`.

1. Após o último **Html.ActionLink**, adicione o seguinte **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Peek message", "PeekMessage", "Queues")</li>
    ```

1. Execute o aplicativo e selecione **Espiar Mensagem** para ver resultados semelhantes à seguinte captura de tela:
  
    ![Espiar mensagem](./media/vs-storage-aspnet-getting-started-queues/peek-message-results.png)

## <a name="read-and-remove-a-message-from-a-queue"></a>Ler e remover uma mensagem de uma fila

Nesta seção, você aprenderá como ler e remover uma mensagem de uma fila.   

> [!NOTE]
> 
> Esta seção pressupõe que você concluiu as etapas [Configurar o ambiente de desenvolvimento](#set-up-the-development-environment). 

1. Abra o arquivo `QueuesController.cs` .

1. Adicione um método chamado **ReadMessage** que retorna um **ActionResult**.

    ```csharp
    public ActionResult ReadMessage()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Dentro do método **ReadMessage**, obtenha um objeto **CloudStorageAccount** que representa as informações da sua conta de armazenamento. Use o seguinte código para obter as informações da cadeia de conexão e conta de armazenamento da configuração de serviço do Azure: (Altere *&lt;nome-da-conta-de-armazenamento>* para o nome da conta de armazenamento do Azure que você está acessando.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Obtenha um objeto **CloudQueueClient** que representa um cliente do serviço de fila.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Obtenha um objeto **CloudQueueContainer** que representa uma referência à fila. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Chame o método **CloudQueue.GetMessage** para ler a primeira mensagem na fila. O método **CloudQueue.GetMessage** torna a mensagem invisível por 30 segundos (por padrão) para qualquer outro código que esteja lendo as mensagens, para que nenhum outro código possa modificar ou excluir a mensagem durante o seu processamento. Para alterar o tempo durante o qual a mensagem fica invisível, modifique o parâmetro **visibilityTimeout** que está sendo passado para o método **CloudQueue.GetMessage**.

    ```csharp
    // This message will be invisible to other code for 30 seconds.
    CloudQueueMessage message = queue.GetMessage();     
    ```

1. Chame o método **CloudQueueMessage.Delete** para excluir a mensagem da fila.

    ```csharp
    queue.DeleteMessage(message);
    ```

1. Atualize o **ViewBag** com a mensagem excluída e o nome da fila.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ViewBag.Message = message.AsString;
    ```
 
1. No **Gerenciador de Soluções**, expanda a pasta **Exibições**, clique com o botão direito do mouse em **Filas** e, no menu de contexto, selecione **Adicionar->Exibição**.

1. Na caixa de diálogo **Adicionar Exibição**, digite **ReadMessage** para o nome da exibição e selecione **Adicionar**.

1. Abra `ReadMessage.cshtml` e modifique-o para que se pareça com o seguinte trecho de código:

    ```csharp
    @{
        ViewBag.Title = "ReadMessage";
    }
    
    <h2>Read Message results</h2>
    
    <table border="1">
        <tr><th>Queue</th><th>Read (and Deleted) Message</th></tr>
        <tr><td>@ViewBag.QueueName</td><td>@ViewBag.Message</td></tr>
    </table>
    ```

1. No **Gerenciador de Soluções**, expanda a pasta **Exibições->Compartilhadas** e abra `_Layout.cshtml`.

1. Após o último **Html.ActionLink**, adicione o seguinte **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Read/Delete message", "ReadMessage", "Queues")</li>
    ```

1. Execute o aplicativo e selecione **Ler/Excluir Mensagem** para ver resultados semelhantes à seguinte captura de tela:
  
    ![Ler e excluir a mensagem](./media/vs-storage-aspnet-getting-started-queues/read-message-results.png)

## <a name="get-the-queue-length"></a>Obter o tamanho da fila

Esta seção ilustra como obter o tamanho da fila (número de mensagens). 

> [!NOTE]
> 
> Esta seção pressupõe que você concluiu as etapas [Configurar o ambiente de desenvolvimento](#set-up-the-development-environment). 

1. Abra o arquivo `QueuesController.cs` .

1. Adicione um método chamado **GetQueueLength** que retorna um **ActionResult**.

    ```csharp
    public ActionResult GetQueueLength()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Dentro do método **ReadMessage**, obtenha um objeto **CloudStorageAccount** que representa as informações da sua conta de armazenamento. Use o seguinte código para obter as informações da cadeia de conexão e conta de armazenamento da configuração de serviço do Azure: (Altere *&lt;nome-da-conta-de-armazenamento>* para o nome da conta de armazenamento do Azure que você está acessando.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Obtenha um objeto **CloudQueueClient** que representa um cliente do serviço de fila.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Obtenha um objeto **CloudQueueContainer** que representa uma referência à fila. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Chame o método **CloudQueue.FetchAttributes** para recuperar os atributos da fila (incluindo seu tamanho). 

    ```csharp
    queue.FetchAttributes();
    ```

6. Acesse a propriedade **CloudQueue.ApproximateMessageCount** para alterar o tamanho da fila.
 
    ```csharp
    int? nMessages = queue.ApproximateMessageCount;
    ```

1. Atualize o **ViewBag** com o nome da fila e seu tamanho.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ViewBag.Length = nMessages;
    ```
 
1. No **Gerenciador de Soluções**, expanda a pasta **Exibições**, clique com o botão direito do mouse em **Filas** e, no menu de contexto, selecione **Adicionar->Exibição**.

1. Na caixa de diálogo **Adicionar Exibição**, insira **GetQueueLength** para o nome da exibição e selecione **Adicionar**.

1. Abra `GetQueueLengthMessage.cshtml` e modifique-o para que se pareça com o seguinte trecho de código:

    ```csharp
    @{
        ViewBag.Title = "GetQueueLength";
    }
    
    <h2>Get Queue Length results</h2>
    
    The queue '@ViewBag.QueueName' has a length of (number of messages): @ViewBag.Length
    ```

1. No **Gerenciador de Soluções**, expanda a pasta **Exibições->Compartilhadas** e abra `_Layout.cshtml`.

1. Após o último **Html.ActionLink**, adicione o seguinte **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Get queue length", "GetQueueLength", "Queues")</li>
    ```

1. Execute o aplicativo e selecione **Obter tamanho da fila** para ver resultados semelhantes à captura de tela a seguir:
  
    ![Obter o tamanho da fila](./media/vs-storage-aspnet-getting-started-queues/get-queue-length-results.png)


## <a name="delete-a-queue"></a>Excluir uma fila
Esta seção ilustra como excluir uma fila. 

> [!NOTE]
> 
> Esta seção pressupõe que você concluiu as etapas [Configurar o ambiente de desenvolvimento](#set-up-the-development-environment). 

1. Abra o arquivo `QueuesController.cs` .

1. Adicione um método chamado **DeleteQueue** que retorna um **ActionResult**.

    ```csharp
    public ActionResult DeleteQueue()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Dentro do método **DeleteQueue**, obtenha um objeto **CloudStorageAccount** que representa as informações da sua conta de armazenamento. Use o seguinte código para obter as informações da cadeia de conexão e conta de armazenamento da configuração de serviço do Azure: (Altere *&lt;nome-da-conta-de-armazenamento>* para o nome da conta de armazenamento do Azure que você está acessando.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Obtenha um objeto **CloudQueueClient** que representa um cliente do serviço de fila.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Obtenha um objeto **CloudQueueContainer** que representa uma referência à fila. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Chame o método **CloudQueue.Delete** para excluir a fila representada pelo objeto **CloudQueue**.

    ```csharp
    queue.Delete();
    ```

1. Atualize o **ViewBag** com o nome da fila e seu tamanho.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ```
 
1. No **Gerenciador de Soluções**, expanda a pasta **Exibições**, clique com o botão direito do mouse em **Filas** e, no menu de contexto, selecione **Adicionar->Exibição**.

1. Na caixa de diálogo **Adicionar Exibição**, insira **DeleteQueue** para o nome de exibição e selecione **Adicionar**.

1. Abra `DeleteQueue.cshtml` e modifique-o para que se pareça com o seguinte trecho de código:

    ```csharp
    @{
        ViewBag.Title = "DeleteQueue";
    }
    
    <h2>Delete Queue results</h2>
    
    @ViewBag.QueueName deleted.
    ```

1. No **Gerenciador de Soluções**, expanda a pasta **Exibições->Compartilhadas** e abra `_Layout.cshtml`.

1. Após o último **Html.ActionLink**, adicione o seguinte **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Delete queue", "DeleteQueue", "Queues")</li>
    ```

1. Execute o aplicativo e selecione **Obter tamanho da fila** para ver resultados semelhantes à captura de tela a seguir:
  
    ![Excluir fila](./media/vs-storage-aspnet-getting-started-queues/delete-queue-results.png)

## <a name="next-steps"></a>Próximas etapas
Consulte outros guias de recursos para obter informações sobre opções adicionais para armazenar dados no Azure.

  * [Introdução ao Armazenamento de Blobs do Azure e aos Serviços Conectados do Visual Studio (ASP.NET)](../storage/vs-storage-aspnet-getting-started-blobs.md)
  * [Introdução ao Armazenamento de Tabelas do Azure e aos Serviços Conectados do Visual Studio (ASP.NET)](vs-storage-aspnet-getting-started-tables.md)
