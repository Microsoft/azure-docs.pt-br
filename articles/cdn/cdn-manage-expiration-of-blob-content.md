---
title: "Gerenciar a expiração dos blobs de Armazenamento do Azure na CDN do Azure | Microsoft Docs"
description: "Aprenda sobre as opções para controlar a vida útil de blobs no cache do Azure CDN."
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: ad4801e9-d09a-49bf-b35c-efdc4e6034e8
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: d4741921806e443d92c385a04b781cec296c2ae8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="manage-expiration-of-azure-storage-blobs-in-azure-cdn"></a>Gerenciar a expiração dos blobs de Armazenamento do Azure na CDN do Azure
> [!div class="op_single_selector"]
> * [Serviços de Nuvem/Aplicativos Web do Azure, ASP.NET ou IIS](cdn-manage-expiration-of-cloud-service-content.md)
> * [Serviço Blob do Armazenamento do Azure](cdn-manage-expiration-of-blob-content.md)
> 
> 

O [serviço blob](../storage/common/storage-introduction.md#blob-storage) no [Armazenamento do Azure](../storage/common/storage-introduction.md) é uma das várias origens baseadas no Azure integrada à CDN do Azure.  Qualquer conteúdo de blob publicamente acessível pode ser armazenado em cache no Azure CDN até que o tempo de vida (TTL) seja decorrido.  O TTL é determinado pelo [*Controle de Cache* ](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9) na resposta HTTP do Armazenamento do Azure.

> [!TIP]
> Você pode optar por não definir nenhum TTL em um blob.  Nesse caso, o Azure CDN aplica automaticamente um TTL padrão de sete dias.
> 
> Para obter mais informações sobre como a CDN do Azure trabalha para acelerar o acesso aos blobs e a outros arquivos, consulte a [Visão geral da CDN do Azure](cdn-overview.md).
> 
> Para obter mais detalhes sobre o serviço Azure Storage Blob, consulte [Conceitos do serviço Blob](https://msdn.microsoft.com/library/dd179376.aspx). 
> 
> 

Este tutorial demonstra várias maneiras que você pode definir o TTL em um blob no Armazenamento do Azure.  

## <a name="azure-powershell"></a>Azure PowerShell
[Azure PowerShell](/powershell/azure/overview) é uma das maneiras mais rápidas e eficientes de administrar os serviços do Azure.  Use o cmdlet `Get-AzureStorageBlob` para obter uma referência para o blob, em seguida, defina a propriedade `.ICloudBlob.Properties.CacheControl`. 

```powershell
# Create a storage context
$context = New-AzureStorageContext -StorageAccountName "<storage account name>" -StorageAccountKey "<storage account key>"

# Get a reference to the blob
$blob = Get-AzureStorageBlob -Context $context -Container "<container name>" -Blob "<blob name>"

# Set the CacheControl property to expire in 1 hour (3600 seconds)
$blob.ICloudBlob.Properties.CacheControl = "public, max-age=3600"

# Send the update to the cloud
$blob.ICloudBlob.SetProperties()
```

> [!TIP]
> Você também pode usar o PowerShell para [gerenciar os perfis e os pontos de extremidade da CDN](cdn-manage-powershell.md).
> 
> 

## <a name="azure-storage-client-library-for-net"></a>Biblioteca do Cliente de Armazenamento do Azure para .NET
Para definir o TTL de um blob usando o .NET, use a [Biblioteca do Cliente de Armazenamento do Azure para .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md) para definir a propriedade [CloudBlob.Properties.CacheControl](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.blobproperties.cachecontrol.aspx).

```csharp
class Program
{
    const string connectionString = "<storage connection string>";
    static void Main()
    {
        // Retrieve storage account information from connection string
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);

        // Create a blob client for interacting with the blob service.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        // Create a reference to the container
        CloudBlobContainer container = blobClient.GetContainerReference("<container name>");

        // Create a reference to the blob
        CloudBlob blob = container.GetBlobReference("<blob name>");

        // Set the CacheControl property to expire in 1 hour (3600 seconds)
        blob.Properties.CacheControl = "public, max-age=3600";

        // Update the blob's properties in the cloud
        blob.SetProperties();
    }
}
```

> [!TIP]
> Existem muitos exemplos de código .NET disponíveis nos [Exemplos de Armazenamento de Blobs do Azure para .NET](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/).
> 
> 

## <a name="other-methods"></a>Outros métodos
* [Interface de linha de comando do Azure](../cli-install-nodejs.md)
  
    Ao carregar o blob, defina a propriedade *cacheControl* usando o argumento `-p`.  Este exemplo define o TTL para uma hora (3.600 segundos).
  
    ```text
    azure storage blob upload -c <connectionstring> -p cacheControl="public, max-age=3600" .\test.txt myContainer test.txt
    ```
* [API REST de serviços de armazenamento do Azure](https://msdn.microsoft.com/library/azure/dd179355.aspx)
  
    Defina explicitamente a propriedade *x-ms-blob-cache-control* em uma solicitação [Put Block](https://msdn.microsoft.com/en-us/library/azure/dd179451.aspx), [Put Block List](https://msdn.microsoft.com/en-us/library/azure/dd179467.aspx), ou [Set Blob Properties](https://msdn.microsoft.com/library/azure/ee691966.aspx).
* Ferramentas de gerenciamento de armazenamento de terceiros
  
    Algumas ferramentas de gerenciamento de Armazenamento do Azure de terceiros permitem que você defina a propriedade *CacheControl* em blobs. 

## <a name="testing-the-cache-control-header"></a>Testando o cabeçalho de *Controle de Cache*
Você pode verificar facilmente a TTL de seus blobs.  Usando as [ferramentas de desenvolvedor](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/)do seu navegador, teste se o blob está incluso no cabeçalho de resposta de *Controle de Cache* .  Você também pode usar uma ferramenta como **wget**, [Postman](https://www.getpostman.com/) ou [Fiddler](http://www.telerik.com/fiddler) para examinar os cabeçalhos de resposta.

## <a name="next-steps"></a>Próximas etapas
* [Leia sobre o cabeçalho *Cache-Control*](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9)
* [Saiba como gerenciar a expiração do conteúdo do Serviço de Nuvem na CDN do Azure](cdn-manage-expiration-of-cloud-service-content.md)

