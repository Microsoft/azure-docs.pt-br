---
title: Alterar o caminho de blob padrão | Microsoft Docs
description: Saiba como configurar uma função do Azure para renomear um caminho de arquivo de blob
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: cdaf991c25c23dee4f87b44142c1482bf892bcf2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60723535"
---
# <a name="change-a-blob-path-from-the-default-path"></a>Alterar o caminho de blob padrão

Quando o serviço do Gerenciador de Dados do StorSimple transforma os dados, por padrão, ele coloca os blobs transformados em um contêiner de armazenamento, conforme especificado durante a criação do repositório de destino. Como os blobs chegam nesse local, você pode querer mover esses blobs para um local alternativo. Este artigo descreve como configurar uma função do Azure para renomear um caminho de arquivo de blob padrão e, assim, mover os blobs para um local diferente.

## <a name="prerequisites"></a>Pré-requisitos

Certifique-se de que você tenha uma definição de trabalho configurada corretamente no seu serviço de Gerenciador de Dados do StorSimple.

## <a name="create-an-azure-function"></a>Criar uma função do Azure

Para criar uma função do Azure, realize as etapas a seguir:

1. Vá para o [Portal do Azure](https://portal.azure.com/).

2. Clique em **+ Criar um recurso**. Na caixa **Pesquisa**, digite **Aplicativo de funções** e pressione **Enter**. Selecione e clique em **Aplicativo de Funções** na lista de aplicativos exibidos.

    ![Digite “Aplicativo de Funções” na caixa Pesquisa](./media/storsimple-data-manager-change-default-blob-path/search-function-app.png)

3. Clique em **Criar**.

    ![O botão “Criar” da janela Aplicativo de Funções](./media/storsimple-data-manager-change-default-blob-path/create-function-app.png)

4. Na folha de configuração **Aplicativo de funções**, realize as seguintes etapas:

    1. Forneça um **Nome de Aplicativo** exclusivo.
    2. Na lista suspensa, selecione **Assinatura**. Essa assinatura deve ser a mesma associada ao seu serviço do Gerenciador de Dados do StorSimple.
    3. Selecione **Criar novo** grupo de recursos.
    4. Para a lista suspensa do **Plano de hospedagem**, selecione **Plano de Consumo**.
    5. Especifique um local onde sua função seja executada. Você deseja a mesma região onde o serviço do Gerenciador de Dados do StorSimple e a conta de armazenamento associada à definição de trabalho estão localizados.
    6. Selecione uma conta de armazenamento existente ou crie uma nova. Uma conta de armazenamento é usada internamente para a função.

        ![Inserir dados de configuração do novo Aplicativo de Funções](./media/storsimple-data-manager-change-default-blob-path/function-app-parameters.png)

    7. Clique em **Criar**. O aplicativo de funções é criado.
     
        ![Aplicativo de funções criado](./media/storsimple-data-manager-change-default-blob-path/function-app-created.png)

5. Selecione **Funções** e clique em **+ Nova função**.

    ![Clique em + Nova Função](./media/storsimple-data-manager-change-default-blob-path/create-new-function.png)

6. Selecione **C#** para o idioma. Na matriz de blocos de modelo, selecione **C#** no bloco **QueueTrigger-CSharp**.

7. No **Gatilho de fila**:

    1. Insira um **Nome** para a sua função.
    2. Na caixa **Nome da fila**, digite o nome da definição de trabalho de transformação de dados.
    3. Em **Conexão da conta de armazenamento**, clique em **nova**. Na lista de contas de armazenamento, selecione a conta associada à sua definição de trabalho. Anote o nome da conexão (destacada). Esse nome será necessário mais tarde na função do Azure.

        ![Criar uma nova função do C#](./media/storsimple-data-manager-change-default-blob-path/new-function-parameters.png)

    4. Clique em **Criar**. A **Função** é criada.

     
10. Na janela Função, execute o arquivo _.csx_.

    ![Criar uma nova função do C#](./media/storsimple-data-manager-change-default-blob-path/new-function-run-csx.png)
    
    Execute as seguintes etapas:

    1. Cole o seguinte código:

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

    2. Substitua **STORAGE_CONNECTIONNAME** na linha 11 pela conexão da conta de armazenamento (consulte a etapa 7c).

        ![Copie o nome de conexão de armazenamento](./media/storsimple-data-manager-change-default-blob-path/new-function-storage-connection-name.png)

    3. **Salve** a função.

        ![Salvar função](./media/storsimple-data-manager-change-default-blob-path/save-function.png)

12. Para concluir a função, adicione mais um arquivo realizando as seguintes etapas:

    1. Clique em **Exibir arquivos**.

       ![O link “Exibir arquivos”](./media/storsimple-data-manager-change-default-blob-path/view-files.png)

    2. Clique em **+ Adicionar**.
        
        ![O link “Exibir arquivos”](./media/storsimple-data-manager-change-default-blob-path/new-function-add-file.png)
    
    3. Digite **project.json** e pressione **Enter**. No arquivo **project.json**, cole o seguinte código:

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

    
    4. Clique em **Salvar**.

        ![O link “Exibir arquivos”](./media/storsimple-data-manager-change-default-blob-path/new-function-project-json.png)

Você criou uma função do Azure. Essa função é disparada sempre que um novo blob é gerado pelo trabalho de transformação de dados.

## <a name="next-steps"></a>Próximas etapas

[Usar a interface do usuário do Gerenciador de Dados StorSimple para transformar seus dados](storsimple-data-manager-ui.md)
