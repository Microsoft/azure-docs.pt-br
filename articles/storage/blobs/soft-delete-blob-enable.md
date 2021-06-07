---
title: Habilitar e gerenciar exclusão reversível para blobs
titleSuffix: Azure Storage
description: Habilite a exclusão reversível para BLOBs para recuperar seus dados com mais facilidade quando eles forem modificados ou excluídos erroneamente.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/15/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: a74860b7adf4dade5aedc71a4960595cbe55eaf0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "95995294"
---
# <a name="enable-and-manage-soft-delete-for-blobs"></a>Habilitar e gerenciar exclusão reversível para blobs

A exclusão reversível de blob protege seus dados de serem modificados ou excluídos acidentalmente ou erroneamente. Quando a exclusão reversível de blob está habilitada para uma conta de armazenamento, BLOBs, versões de BLOB e instantâneos nessa conta de armazenamento podem ser recuperados depois de serem excluídos, dentro de um período de retenção especificado por você.

Se houver uma possibilidade de que seus dados possam ser acidentalmente modificados ou excluídos por um aplicativo ou outro usuário da conta de armazenamento, a Microsoft recomenda ativar a exclusão reversível de BLOB. Este artigo mostra como habilitar a exclusão reversível para BLOBs. Para obter mais detalhes sobre a exclusão reversível de BLOB, consulte [exclusão reversível para BLOBs](soft-delete-blob-overview.md).

Para saber como habilitar também a exclusão reversível para contêineres, consulte [habilitar e gerenciar a exclusão reversível para contêineres](soft-delete-container-enable.md).

## <a name="enable-blob-soft-delete"></a>Habilitar exclusão reversível de BLOB

# <a name="portal"></a>[Portal](#tab/azure-portal)

Habilite a exclusão reversível para blobs em sua conta de armazenamento usando o portal do Azure:

1. No [Portal do Azure](https://portal.azure.com/), navegue até sua conta de armazenamento.
1. Localize a opção **proteção de dados** em **serviço blob**.
1. Defina a propriedade **exclusão reversível do blob** como *habilitada*.
1. Em **políticas de retenção**, especifique por quanto tempo os blobs de exclusão reversível são retidos pelo armazenamento do Azure.
1. Salve suas alterações.

![Captura de tela do portal do Azure com o serviço blob de proteção de dados escolhido.](media/soft-delete-blob-enable/storage-blob-soft-delete-portal-configuration.png)

Para exibir blobs com exclusão reversível, marque a caixa de seleção **Mostrar blobs excluídos**.

![Captura de tela da página de serviço blob de proteção de dados com a opção Mostrar BLOBs excluídos realçada.](media/soft-delete-blob-enable/storage-blob-soft-delete-portal-view-soft-deleted.png)

Para exibir instantâneos com exclusão reversível para um determinado blob, selecione o blob e clique em **Exibir instantâneos**.

![Captura de tela da página de serviço blob de proteção de dados com a opção Exibir instantâneos realçada.](media/soft-delete-blob-enable/storage-blob-soft-delete-portal-view-soft-deleted-snapshots.png)

Verifique se a caixa de seleção **Mostrar instantâneos excluídos** está selecionada.

![Captura de tela da página Exibir instantâneos com a opção Mostrar BLOBs excluídos realçada.](media/soft-delete-blob-enable/storage-blob-soft-delete-portal-view-soft-deleted-snapshots-check.png)

Quando você clica em um blob com exclusão reversível ou um instantâneo, observe as novas propriedades do blob. Elas indicam quando o objeto foi excluído e o número de dias até que o blob ou o instantâneo de blob expire permanentemente. Se o objeto com exclusão reversível não for um instantâneo, você também terá a opção de restaurá-lo.

![Captura de tela dos detalhes de um objeto com exclusão reversível.](media/soft-delete-blob-enable/storage-blob-soft-delete-portal-properties.png)

Lembre-se de que restaurar um blob também restaurará todos os instantâneos associados. Para restaurar instantâneos com exclusão reversível para um blob ativo, clique no blob e selecione **Restaurar todos os instantâneos**.

![Captura de tela dos detalhes de um blob com exclusão reversível.](media/soft-delete-blob-enable/storage-blob-soft-delete-portal-undelete-all-snapshots.png)

Depois que restaurar os instantâneos de um blob, você poderá clicar em **Promover** para copiar um instantâneo sobre o blob raiz, restaurando assim o blob para o instantâneo.

![Captura de tela da página Exibir instantâneos com a opção promover realçada.](media/soft-delete-blob-enable/storage-blob-soft-delete-portal-promote-snapshot.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Para habilitar a exclusão reversível, atualize as propriedades do serviço do cliente de um blob. O exemplo a seguir habilita a exclusão reversível para um subconjunto de contas em uma assinatura:

```powershell
Set-AzContext -Subscription "<subscription-name>"
$MatchingAccounts = Get-AzStorageAccount | where-object{$_.StorageAccountName -match "<matching-regex>"}
$MatchingAccounts | Enable-AzStorageDeleteRetentionPolicy -RetentionDays 7
```

É possível verificar se a exclusão reversível foi ativada usando o comando a seguir:

```powershell
$MatchingAccounts | $account = Get-AzStorageAccount -ResourceGroupName myresourcegroup -Name storageaccount
   Get-AzStorageServiceProperty -ServiceType Blob -Context $account.Context | Select-Object -ExpandProperty DeleteRetentionPolicy
```

Para recuperar os blobs que foram excluídos acidentalmente, chame **undelete blob** nesses BLOBs. Lembre-se de que chamar **Restaurar Blob** em blobs ativos e com exclusão reversível restaurará todos os instantâneos com exclusão reversível associados como ativos. O exemplo a seguir chama **undelete blob** em todos os BLOBs com exclusão reversível e ativos em um contêiner:

```powershell
# Create a context by specifying storage account name and key
$ctx = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

# Get the blobs in a given container and show their properties
$Blobs = Get-AzStorageBlob -Container $StorageContainerName -Context $ctx -IncludeDeleted
$Blobs.ICloudBlob.Properties

# Undelete the blobs
$Blobs.ICloudBlob.Undelete()
```
Para localizar a política de retenção de exclusão reversível atual, use o comando a seguir:

```azurepowershell-interactive
   $account = Get-AzStorageAccount -ResourceGroupName myresourcegroup -Name storageaccount
   Get-AzStorageServiceProperty -ServiceType Blob -Context $account.Context
```

# <a name="cli"></a>[CLI](#tab/azure-CLI)

Para habilitar a exclusão reversível, atualize as propriedades do serviço do cliente de um blob:

```azurecli-interactive
az storage blob service-properties delete-policy update --days-retained 7  --account-name mystorageaccount --enable true
```

Para verificar se a exclusão reversível está ativada, use o seguinte comando: 

```azurecli-interactive
az storage blob service-properties delete-policy show --account-name mystorageaccount 
```

# <a name="python"></a>[Python](#tab/python)

Para habilitar a exclusão reversível, atualize as propriedades do serviço do cliente de um blob:

```python
# Make the requisite imports
from azure.storage.blob import BlockBlobService
from azure.storage.common.models import DeleteRetentionPolicy

# Initialize a block blob service
block_blob_service = BlockBlobService(
    account_name='<enter your storage account name>', account_key='<enter your storage account key>')

# Set the blob client's service property settings to enable soft delete
block_blob_service.set_blob_service_properties(
    delete_retention_policy=DeleteRetentionPolicy(enabled=True, days=7))
```

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

Para habilitar a exclusão reversível, atualize as propriedades do serviço do cliente de um blob:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_EnableSoftDelete":::

Para recuperar os blobs que foram excluídos acidentalmente, chame Restaurar nesses blobs. Lembre-se de que selecionar **Desfazer exclusão** em blobs ativos e com exclusão reversível restaurará todos os instantâneos com exclusão reversível associados como ativos. O exemplo a seguir chama Restaurar em todos os blobs com exclusão reversível e ativos em um contêiner:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_RecoverDeletedBlobs":::

Para recuperar para uma versão específica do blob, primeiro chame Restaurar em um blob e depois copie o instantâneo desejado sobre o blob. O exemplo a seguir recupera um blob de blocos para o seu instantâneo gerado mais recentemente:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_RecoverSpecificBlobVersion":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Para habilitar a exclusão reversível, atualize as propriedades do serviço do cliente de um blob:

```csharp
// Get the blob client's service property settings
ServiceProperties serviceProperties = blobClient.GetServiceProperties();

// Configure soft delete
serviceProperties.DeleteRetentionPolicy.Enabled = true;
serviceProperties.DeleteRetentionPolicy.RetentionDays = RetentionDays;

// Set the blob client's service property settings
blobClient.SetServiceProperties(serviceProperties);
```

Para recuperar os blobs que foram excluídos acidentalmente, chame **undelete blob** nesses BLOBs. Lembre-se de que chamar **Restaurar Blob** em blobs ativos e com exclusão reversível restaurará todos os instantâneos com exclusão reversível associados como ativos. O exemplo a seguir chama **undelete blob** em todos os BLOBs ativos e excluídos de forma reversível em um contêiner:

```csharp
// Recover all blobs in a container
foreach (CloudBlob blob in container.ListBlobs(useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Deleted))
{
       await blob.UndeleteAsync();
}
```

Para recuperar para uma versão específica do blob, primeiro chame a operação **restaurar blob** e, em seguida, copie o instantâneo desejado sobre o blob. O exemplo a seguir recupera um blob de blocos para o seu instantâneo gerado mais recentemente:

```csharp
// Undelete
await blockBlob.UndeleteAsync();

// List all blobs and snapshots in the container prefixed by the blob name
IEnumerable<IListBlobItem> allBlobVersions = container.ListBlobs(
    prefix: blockBlob.Name, useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Snapshots);

// Restore the most recently generated snapshot to the active blob
CloudBlockBlob copySource = allBlobVersions.First(version => ((CloudBlockBlob)version).IsSnapshot &&
    ((CloudBlockBlob)version).Name == blockBlob.Name) as CloudBlockBlob;
blockBlob.StartCopy(copySource);
```  

---

## <a name="next-steps"></a>Próximas etapas

- [Exclusão reversível do armazenamento de blobs](./soft-delete-blob-overview.md)
- [Controle de versão de BLOB](versioning-overview.md)