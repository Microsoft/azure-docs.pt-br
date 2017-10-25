---
title: "Alterar o caminho de blob padrão | Microsoft Docs"
description: "Saiba como configurar uma função do Azure para renomear um caminho de arquivo de blob (visualização particular)"
services: storsimple
documentationcenter: NA
author: vidarmsft
manager: syadav
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 03/16/2017
ms.author: vidarmsft
ms.openlocfilehash: 057d4d7370207859617eb63238bf425bfa6d3e16
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="change-a-blob-path-from-the-default-path-private-preview"></a>Alterar o caminho de blob padrão (visualização particular)

Este artigo descreve como configurar uma função do Azure para renomear um caminho de arquivo de blob padrão. 

## <a name="prerequisites"></a>Pré-requisitos

Verifique se você tem uma definição de trabalho que foi configurada corretamente em um recurso de dados híbridos dentro de um grupo de recursos.

## <a name="create-an-azure-function"></a>Criar uma função do Azure

Para criar uma função do Azure, faça o seguinte:

1. Vá para o [Portal do Azure](http://portal.azure.com/).

2. Na parte superior do painel esquerdo, clique em **Novo**. 

3. Na caixa **Pesquisa**, digite **Aplicativo de Funções** e pressione Enter.

    ![Digite “Aplicativo de Funções” na caixa Pesquisa](./media/storsimple-data-manager-change-default-blob-path/goto-function-app-resource.png)

4. Na lista **Resultados**, clique em **Aplicativo de Funções**.

    ![Selecione o recurso de aplicativo de funções na lista de resultados](./media/storsimple-data-manager-change-default-blob-path/select-function-app-resource.png)

    A janela **Aplicativo de Funções** é aberta.

5. Clique em **Criar**.

    ![O botão “Criar” da janela Aplicativo de Funções](./media/storsimple-data-manager-change-default-blob-path/create-new-function-app.png)

6. Na folha de configuração **Aplicativo de Funções**, faça o seguinte:

    a. Na caixa **Nome do aplicativo**, digite o nome do aplicativo.
    
    b. Na caixa **Assinatura**, digite o nome da assinatura.

    c. Em **Grupo de Recursos**, clique em **Criar novo** e digite o nome do novo grupo de recursos.

    d. Na caixa **Plano de Hospedagem**, digite **Plano de Consumo**.

    e. Na caixa **Local**, digite o local.

    f. Em **Conta de armazenamento**, selecione uma conta de armazenamento existente ou crie uma nova. Uma conta de armazenamento é usada internamente para a função.

    ![Inserir dados de configuração do novo Aplicativo de Funções](./media/storsimple-data-manager-change-default-blob-path/enter-new-funcion-app-data.png)

7. Clique em **Criar**.  
    O aplicativo de funções é criado.

8. No painel esquerdo, clique em **Mais serviços** e, em seguida, faça o seguinte:
    
    a. Na caixa **Filtro**, digite **Serviços de aplicativos**.
    
    b. Clique em **Serviços de Aplicativos**. 

    ![Link “Mais serviços” no painel esquerdo](./media/storsimple-data-manager-change-default-blob-path/more-services.png)

9. Na lista de serviços de aplicativos, clique no nome do aplicativo de funções.  
    A página do aplicativo de funções é aberta.

10. No painel esquerdo, clique em **Nova Função** e, em seguida, faça o seguinte: 

    a. Na lista **Linguagem**, selecione **C#**.
    
    b. Na matriz de blocos de modelo, selecione **QueueTrigger-CSharp**.

    c. Na caixa **Dê um nome à sua função**, digite um nome para a função.

    d. Na caixa **Nome da fila**, digite o nome da definição de trabalho de transformação de dados.

    e. Em **Conexão da conta de armazenamento**, clique em **Nova** e, em seguida, selecione a conta que corresponde ao trabalho de transformação de dados.  
        Anote o nome da conexão. Esse nome será necessário mais tarde na função do Azure.

       ![Criar uma nova função do C#](./media/storsimple-data-manager-change-default-blob-path/create-new-csharp-function.png)

    f. Clique em **Criar**.  
    A janela **Função** é aberta.

11. Na janela **Função**, execute o arquivo _.csx_ e, em seguida, faça o seguinte:

    a. Cole o seguinte código:

    ```
    using System;
    using System.Configuration;
    using Microsoft.WindowsAzure.Storage.Blob;
    using Microsoft.WindowsAzure.Storage.Queue;
    using Microsoft.WindowsAzure.Storage;
    using System.Collections.Generic;
    using System.Linq;

    public static void Run(QueueItem myQueueItem, TraceWriter log)
    {
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConfigurationManager.AppSettings["STORAGE_CONNECTIONNAME"]);

        string storageAccUriEndswith = "windows.net/";
        string uri = myQueueItem.TargetLocation.Replace("%20", " ");
        log.Info($"Blob Uri: {uri}");

        // Remove storage account uri string
        uri = uri.Substring(uri.IndexOf(storageAccUriEndswith) + storageAccUriEndswith.Length);

        string containerName = uri.Substring(0, uri.IndexOf("/")); 

        // Remove container name string
        uri = uri.Substring(containerName.Length + 1);

        // Current blob path
        string blobName = uri; 

        string volumeName = uri.Substring(containerName.Length + 1);
        volumeName = uri.Substring(0, uri.IndexOf("/"));

        // Remove volume name string
        uri = uri.Substring(volumeName.Length + 1);

        string newContainerName = uri.Substring(0, uri.IndexOf("/")).ToLower();
        string newBlobName = uri.Substring(newContainerName.Length + 1);

        log.Info($"Container name: {containerName}");
        log.Info($"Volume name: {volumeName}");
        log.Info($"New container name: {newContainerName}");

        log.Info($"Blob name: {blobName}");
        log.Info($"New blob name: {newBlobName}");

        // Create the blob client.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        // Container reference
        CloudBlobContainer container = blobClient.GetContainerReference(containerName);
        CloudBlobContainer newContainer = blobClient.GetContainerReference(newContainerName);
        newContainer.CreateIfNotExists();

        if(!container.Exists())
        {
            log.Info($"Container - {containerName} not exists");
            return;
        }

        if(!newContainer.Exists())
        {
            log.Info($"Container - {newContainerName} not exists");
            return;
        }

        CloudBlockBlob blob = container.GetBlockBlobReference(blobName);
        if (!blob.Exists())
        {
            // Skip to copy the blob to new container, if source blob doesn't exist
            log.Info($"The specified blob does not exist.");
            log.Info($"Blob Uri: {blob.Uri}");
            return;
        }

        CloudBlockBlob blobCopy = newContainer.GetBlockBlobReference(newBlobName);
        if (!blobCopy.Exists())
        {
            blobCopy.StartCopy(blob);
            // Delete old blob, after copy to new container
            blob.DeleteIfExists();
            log.Info($"Blob file path renamed completed successfully");
        }
        else
        {
            log.Info($"Blob file path renamed already done");
            // Delete old blob, if already exists.
            blob.DeleteIfExists();
        }
    }

    public class QueueItem
    {
        public string SourceLocation {get;set;}
        public long SizeInBytes {get;set;}
        public string Status {get;set;}
        public string JobID {get;set;}
        public string TargetLocation {get; set;}
    }

    ```

    b. Substitua **STORAGE_CONNECTIONNAME** na linha 11 pela conexão da conta de armazenamento (consulte o ponto 8c).

    c. Na parte superior esquerda, clique em **Salvar**.

    ![Salvar função](./media/storsimple-data-manager-change-default-blob-path/save-function.png)

12. Para concluir a função, adicione mais um arquivo fazendo o seguinte:

    a. Clique em **Exibir arquivos**.

       ![O link “Exibir arquivos”](./media/storsimple-data-manager-change-default-blob-path/view-files.png)

    b. Clique em **Adicionar**.
    
    c. Digite **project.json** e pressione Enter.
    
    d. No arquivo **project.json**, cole o seguinte código:

    ```
    {
    "frameworks": {
        "net46":{
        "dependencies": {
            "windowsazure.storage": "8.1.1"
        }
        }
    }
    }

    ```

    e. Clique em **Salvar**.

Você criou uma função do Azure. Essa função é disparada sempre que um novo blob é gerado pelo trabalho de transformação de dados.

## <a name="next-steps"></a>Próximas etapas

[Usar a interface do usuário do Gerenciador de Dados StorSimple para transformar seus dados](storsimple-data-manager-ui.md)
