---
title: "Habilitar o acesso de leitura público para contêineres e blobs no Armazenamento de Blobs do Azure | Microsoft Docs"
description: "Saiba como disponibilizar os contêineres e blobs para acesso anônimo e como acessá-los programaticamente."
services: storage
documentationcenter: 
author: tamram
manager: timlt
editor: tysonn
ms.assetid: a2cffee6-3224-4f2a-8183-66ca23b2d2d7
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2017
ms.author: tamram
ms.openlocfilehash: 3b41e25a6a4fb919f268166500bc248b81d8c70f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="manage-anonymous-read-access-to-containers-and-blobs"></a>Gerenciar o acesso de leitura anônimo aos contêineres e blobs
Você pode habilitar o acesso de leitura anônimo, público para um contêiner e seus blobs no Armazenamento de Blobs do Azure. Ao fazer isso, você pode conceder acesso somente leitura a esses recursos sem compartilhar a chave da conta e sem exigir uma SAS (assinatura de acesso compartilhado).

O acesso de leitura público é ideal para cenários em que você quer que determinados blobs sempre estejam disponíveis para acesso de leitura anônimo. Para ter um controle mais refinado, você pode criar uma assinatura de acesso compartilhado. As assinaturas de acesso compartilhado permitem fornecer acesso restrito usando permissões diferentes, por um período específico. Para saber mais sobre como criar assinaturas de acesso compartilhado, veja [Usando SAS (Assinaturas de Acesso Compartilhado) no Armazenamento do Azure](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="grant-anonymous-users-permissions-to-containers-and-blobs"></a>Conceder permissões de usuários anônimos a contêineres e blobs
Por padrão, um contêiner e todos os blobs dentro dele podem ser acessados somente pelo proprietário da conta de armazenamento. Para dar aos usuários anônimos permissões de leitura a um contêiner e seus blobs, é possível definir as permissões de contêiner para permitir acesso público. Usuários anônimos podem ler blobs dentro de um contêiner publicamente acessível sem autenticar a solicitação.

Você pode configurar um contêiner com as seguintes permissões:

* **Sem acesso de leitura público:** o contêiner e seus blobs podem ser acessados somente pelo proprietário da conta de armazenamento. Esse é o padrão para todos os novos contêineres.
* **Acesso de leitura público somente para blobs:** os blobs nesse contêiner podem ser lidos por solicitação anônima, mas os dados do contêiner não estão disponíveis. Os clientes não podem enumerar os blobs no contêiner.
* **Acesso de leitura público completo:** todos os dados de contêiner e blob podem ser lidos por solicitação anônima. Os clientes podem enumerar os blobs no contêiner por meio solicitação anônima, mas não podem enumerar os contêineres na conta de armazenamento.

Você pode usar os seguintes recursos para definir permissões de contêiner:

* [Portal do Azure](https://portal.azure.com)
* [Azure PowerShell](../common/storage-powershell-guide-full.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#how-to-manage-azure-blobs)
* [CLI 2.0 do Azure](../common/storage-azure-cli.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#create-and-manage-blobs)
* De modo programático, usando uma das bibliotecas de cliente de armazenamento ou a API REST

### <a name="set-container-permissions-in-the-azure-portal"></a>Definir permissões de contêiner no Portal do Azure
Para definir permissões de contêiner no [Portal do Azure](https://portal.azure.com), siga estas etapas:

1. Abra a folha **Conta de armazenamento** no portal. Você pode localizar sua conta de armazenamento selecionando **Contas de armazenamento** na folha de menu do portal principal.
1. Em **SERVIÇO BLOB** na folha de menu, selecione **Contêineres**.
1. Clique com o botão direito do mouse na linha do contêiner ou selecione as reticências para abrir o **Menu de contexto** do contêiner.
1. Selecione **Política de acesso** no menu de contexto.
1. Selecione um **Tipo de acesso** no menu suspenso.

    ![Caixa de diálogo Editar Metadados do Contêiner](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

### <a name="set-container-permissions-with-net"></a>Definir permissões de contêiner com .NET
A fim de definir permissões para um contêiner usando C# e a Biblioteca de Cliente de Armazenamento para .NET, primeiro recupere as permissões existentes do contêiner chamando o método **GetPermissions**. Em seguida, defina a propriedade **PublicAccess** para o objeto **BlobContainerPermissions** que é retornado pelo método **GetPermissions**. Por fim, chame o método **SetPermissions** com as permissões atualizadas.

O exemplo a seguir define as permissões do contêiner para acesso de leitura público completo. Para definir as permissões como acesso de leitura público somente para os blobs, defina a propriedade **PublicAccess** como **BlobContainerPublicAccessType.Blob**. Para remover todas as permissões para usuários anônimos, defina a propriedade como **BlobContainerPublicAccessType.Off**.

```csharp
public static void SetPublicContainerPermissions(CloudBlobContainer container)
{
    BlobContainerPermissions permissions = container.GetPermissions();
    permissions.PublicAccess = BlobContainerPublicAccessType.Container;
    container.SetPermissions(permissions);
}
```

## <a name="access-containers-and-blobs-anonymously"></a>Acessar contêineres e blobs anonimamente
Um cliente que acessa contêineres e blobs anonimamente pode usar construtores que não necessitam de credenciais. Os exemplos a seguir mostram algumas maneiras diferentes de fazer referência a recursos do serviço Blob anonimamente.

### <a name="create-an-anonymous-client-object"></a>Criar um objeto de cliente anônimo
Você pode criar um novo objeto de cliente de serviço para acesso anônimo fornecendo o ponto de extremidade do serviço Blob para a conta. No entanto, você também precisa saber o nome de um contêiner dessa conta que esteja disponível para acesso anônimo.

```csharp
public static void CreateAnonymousBlobClient()
{
    // Create the client object using the Blob service endpoint.
    CloudBlobClient blobClient = new CloudBlobClient(new Uri(@"https://storagesample.blob.core.windows.net"));

    // Get a reference to a container that's available for anonymous access.
    CloudBlobContainer container = blobClient.GetContainerReference("sample-container");

    // Read the container's properties. Note this is only possible when the container supports full public read access.
    container.FetchAttributes();
    Console.WriteLine(container.Properties.LastModified);
    Console.WriteLine(container.Properties.ETag);
}
```

### <a name="reference-a-container-anonymously"></a>Fazer referência a um contêiner anonimamente
Se tiver a URL para um contêiner que está disponível de forma anônima, você pode usá-lo para fazer referência diretamente ao contêiner.

```csharp
public static void ListBlobsAnonymously()
{
    // Get a reference to a container that's available for anonymous access.
    CloudBlobContainer container = new CloudBlobContainer(new Uri(@"https://storagesample.blob.core.windows.net/sample-container"));

    // List blobs in the container.
    foreach (IListBlobItem blobItem in container.ListBlobs())
    {
        Console.WriteLine(blobItem.Uri);
    }
}
```

### <a name="reference-a-blob-anonymously"></a>Fazer referência a um blob anonimamente
Se tiver a URL para um blob que está disponível para acesso anônimo, você pode fazer referência ao blob diretamente usando esta URL:

```csharp
public static void DownloadBlobAnonymously()
{
    CloudBlockBlob blob = new CloudBlockBlob(new Uri(@"https://storagesample.blob.core.windows.net/sample-container/logfile.txt"));
    blob.DownloadToFile(@"C:\Temp\logfile.txt", System.IO.FileMode.Create);
}
```

## <a name="features-available-to-anonymous-users"></a>Recursos disponíveis para usuários anônimos
A tabela a seguir mostra quais operações podem ser chamadas por usuários anônimos quando a ACL do contêiner é definida para permitir acesso público.

| Operação REST | Permissão com acesso de leitura público completo | Permissão de acesso de leitura público apenas para blobs |
| --- | --- | --- |
| Listar contêineres |Somente proprietário |Somente proprietário |
| Create Container |Somente proprietário |Somente proprietário |
| Get Container Properties |Todos |Somente proprietário |
| Get Container Metadata |Todos |Somente proprietário |
| Set Container Metadata |Somente proprietário |Somente proprietário |
| Get Container ACL |Somente proprietário |Somente proprietário |
| Set Container ACL |Somente proprietário |Somente proprietário |
| Delete Container |Somente proprietário |Somente proprietário |
| Listar Blobs |Todos |Somente proprietário |
| Put Blob |Somente proprietário |Somente proprietário |
| Get Blob |Todos |Todos |
| Get Blob Properties |Todos |Todos |
| Set Blob Properties |Somente proprietário |Somente proprietário |
| Get Blob Metadata |Todos |Todos |
| Set Blob Metadata |Somente proprietário |Somente proprietário |
| Put Block |Somente proprietário |Somente proprietário |
| Obter lista de blocos (somente blocos confirmados) |Todos |Todos |
| Obter lista de blocos (somente blocos não confirmados ou todos os blocos) |Somente proprietário |Somente proprietário |
| Put Block List |Somente proprietário |Somente proprietário |
| Delete Blob |Somente proprietário |Somente proprietário |
| Copiar blob |Somente proprietário |Somente proprietário |
| Blob de instantâneo |Somente proprietário |Somente proprietário |
| Lease Blob |Somente proprietário |Somente proprietário |
| Put Page |Somente proprietário |Somente proprietário |
| Get Page Ranges |Todos |Todos |
| Acrescentar blob |Somente proprietário |Somente proprietário |

## <a name="next-steps"></a>Próximas etapas

* [Autenticação para os Serviços de Armazenamento do Azure](https://msdn.microsoft.com/library/azure/dd179428.aspx)
* [Uso de SAS (Assinaturas de Acesso Compartilhado)](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Delegando acesso com uma assinatura de acesso compartilhado](https://msdn.microsoft.com/library/azure/ee395415.aspx)
