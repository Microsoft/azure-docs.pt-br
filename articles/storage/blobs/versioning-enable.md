---
title: Habilitar e gerenciar o controle de versão de blob
titleSuffix: Azure Storage
description: Saiba como habilitar o controle de versão de blob no portal do Azure ou usando um modelo de Azure Resource Manager.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/09/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: 5b6bd16eacf4b1bbb7b93f5500813e7fa9dc7eef
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100095827"
---
# <a name="enable-and-manage-blob-versioning"></a>Habilitar e gerenciar o controle de versão de blob

Você pode habilitar o controle de versão do armazenamento de BLOBs para manter automaticamente as versões anteriores de um blob quando ele é modificado ou excluído. Quando o controle de versão do blob está habilitado, você pode restaurar uma versão anterior de um blob para recuperar seus dados se eles forem modificados ou excluídos erroneamente.

Este artigo mostra como habilitar ou desabilitar o controle de versão de BLOB para a conta de armazenamento usando o portal do Azure ou um modelo de Azure Resource Manager. Para saber mais sobre o controle de versão de BLOB, consulte [controle de versão de blob](versioning-overview.md).

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="enable-blob-versioning"></a>Habilitar controle de versão de blob

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Para habilitar o controle de versão de BLOB para uma conta de armazenamento no portal do Azure:

1. Navegue até sua conta de armazenamento no Portal.
1. Em **serviço blob**, escolha **proteção de dados**.
1. Na seção **controle de versão** , selecione **habilitado**.

:::image type="content" source="media/versioning-enable/portal-enable-versioning.png" alt-text="Captura de tela mostrando como habilitar o controle de versão de blob no portal do Azure":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para habilitar o controle de versão de BLOB para uma conta de armazenamento com o PowerShell, primeiro instale o módulo [AZ. Storage](https://www.powershellgallery.com/packages/Az.Storage) versão 2.3.0 ou posterior. Em seguida, chame o comando [Update-AzStorageBlobServiceProperty](/powershell/module/az.storage/update-azstorageblobserviceproperty) para habilitar o controle de versão, conforme mostrado no exemplo a seguir. Lembre-se de substituir os valores entre colchetes angulares com seus próprios valores:

```powershell
# Set resource group and account variables.
$rgName = "<resource-group>"
$accountName = "<storage-account>"

# Enable versioning.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -IsVersioningEnabled $true
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para habilitar o controle de versão de BLOB para uma conta de armazenamento com CLI do Azure, primeiro instale o CLI do Azure versão 2.2.0 ou posterior. Em seguida, chame o comando [AZ Storage Account blob-Service-Properties Update](/cli/azure/ext/storage-blob-preview/storage/account/blob-service-properties#ext_storage_blob_preview_az_storage_account_blob_service_properties_update) para habilitar o controle de versão, conforme mostrado no exemplo a seguir. Lembre-se de substituir os valores entre colchetes angulares com seus próprios valores:

```azurecli
az storage account blob-service-properties update \
    --resource-group <resource_group> \
    --account-name <storage-account> \
    --enable-versioning true
```

# <a name="template"></a>[Modelo](#tab/template)

Para habilitar o controle de versão de blob com um modelo, crie um modelo com a propriedade **IsVersioningEnabled** como **true**. As etapas a seguir descrevem como criar um modelo no portal do Azure.

1. Na portal do Azure, escolha **criar um recurso**.
1. Em **Pesquisar no Marketplace**, digite **implantação de modelo** e pressione **Enter**.
1. Escolha **implantação de modelo**, escolha **criar** e, em seguida, escolha **criar seu próprio modelo no editor**.
1. No editor de modelo, Cole o JSON a seguir. Substitua o espaço reservado `<accountName>` pelo nome da sua conta de armazenamento.
1. Salve o modelo.
1. Especifique o grupo de recursos da conta e, em seguida, escolha o botão **comprar** para implantar o modelo e habilitar o controle de versão de BLOB.

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.Storage/storageAccounts/blobServices",
                "apiVersion": "2019-06-01",
                "name": "<accountName>/default",
                "properties": {
                    "IsVersioningEnabled": true
                }
            }
        ]
    }
    ```

Para obter mais informações sobre como implantar recursos com modelos no portal do Azure, consulte [implantar recursos com portal do Azure](../../azure-resource-manager/templates/deploy-portal.md).

---

## <a name="modify-a-blob-to-trigger-a-new-version"></a>Modificar um blob para disparar uma nova versão

O exemplo de código a seguir mostra como disparar a criação de uma nova versão com a biblioteca de cliente de armazenamento do Azure para .NET, versão [12.5.1](https://www.nuget.org/packages/Azure.Storage.Blobs/12.5.1) ou posterior. Antes de executar este exemplo, verifique se você habilitou o controle de versão para sua conta de armazenamento.

O exemplo cria um blob de blocos e, em seguida, atualiza os metadados do blob. Atualizar os metadados do blob dispara a criação de uma nova versão. O exemplo recupera a versão inicial e a versão atual e mostra que apenas a versão atual inclui os metadados.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_UpdateVersionedBlobMetadata":::

## <a name="list-blob-versions"></a>Listar versões de BLOB

Para listar versões de BLOB ou instantâneos com a biblioteca de cliente .NET V12, especifique o parâmetro [BlobStates](/dotnet/api/azure.storage.blobs.models.blobstates) com o campo **version** .

O exemplo de código a seguir mostra como listar a versão de BLOBs com a biblioteca de cliente de armazenamento do Azure para .NET, versão [12.5.1](https://www.nuget.org/packages/Azure.Storage.Blobs/12.5.1) ou posterior. Antes de executar este exemplo, verifique se você habilitou o controle de versão para sua conta de armazenamento.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_ListBlobVersions":::

## <a name="next-steps"></a>Próximas etapas

- [Controle de versão de BLOB](versioning-overview.md)
- [Exclusão reversível para blobs do Armazenamento do Azure ](./soft-delete-blob-overview.md)