---
title: Gerenciar a camada de acesso de um blob em uma conta de armazenamento do Azure
description: Saiba como alterar a camada de um blob em uma conta de armazenamento de BLOBs ou de GPv2
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/11/2021
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.reviewer: klaasl
ms.openlocfilehash: 183593022c934eaf52ffe18649c23e8deced34d8
ms.sourcegitcommit: 16887168729120399e6ffb6f53a92fde17889451
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/13/2021
ms.locfileid: "98166470"
---
# <a name="manage-the-access-tier-of-a-blob-in-an-azure-storage-account"></a>Gerenciar a camada de acesso de um blob em uma conta de armazenamento do Azure

Cada blob tem uma camada de acesso padrão, quente, fria ou arquivo morto. Um blob assume a camada de acesso padrão da conta de armazenamento do Azure na qual ele é criado. As contas de armazenamento de BLOBs e GPv2 expõem o atributo **camada de acesso** no nível da conta. Esse atributo especifica a camada de acesso padrão para qualquer BLOB que não tenha definido explicitamente no nível do objeto. Para objetos com a camada definida no nível do objeto, a camada de conta não será aplicada. A camada de arquivo pode ser aplicada somente no nível do objeto. Você pode alternar entre as camadas de acesso a qualquer momento seguindo as etapas abaixo.

## <a name="change-the-tier-of-a-blob-in-a-gpv2-or-blob-storage-account"></a>Alterar a camada de um blob em uma conta de armazenamento de BLOBs ou de GPv2

Os cenários a seguir usam o portal do Azure ou o PowerShell:

# <a name="portal"></a>[Portal](#tab/portal)

1. Entre no [portal do Azure](https://portal.azure.com).

1. No portal do Azure, pesquise e selecione **Todos os recursos**.

1. Selecione sua conta de armazenamento.

1. Selecione o contêiner e clique no blob.

1. Nas **Propriedades de blob**, selecione **Alterar camada**.

1. Selecione a camada de acesso **quente**, **fria** ou de **arquivamento** . Se o blob estiver atualmente no arquivo morto e você quiser reidratar-lo em uma camada online, você também poderá selecionar uma prioridade reidratar de **padrão** ou **alta**.

1. Selecione **Salvar** na parte inferior.

![Alterar camada de blob no portal do Azure](media/storage-tiers/blob-access-tier.png)

# <a name="powershell"></a>[PowerShell](#tab/powershell)

O script do PowerShell a seguir pode ser usado para alterar a camada de BLOB. A variável `$rgName` deve ser inicializada com o nome do grupo de recursos. A variável `$accountName` deve ser inicializada com o nome da conta de armazenamento. A variável `$containerName` deve ser inicializada com o nome do contêiner. A variável `$blobName` deve ser inicializada com o nome do blob.

```powershell
#Initialize the following with your resource group, storage account, container, and blob names
$rgName = ""
$accountName = ""
$containerName = ""
$blobName == ""

#Select the storage account and get the context
$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

#Select the blob from a container
$blob = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $ctx

#Change the blob’s access tier to archive
$blob.ICloudBlob.SetStandardBlobTier("Archive")
```

---

## <a name="next-steps"></a>Próximas etapas

- [Como gerenciar a camada de acesso de conta padrão de uma conta de armazenamento do Azure](../common/manage-account-default-access-tier.md)
- [Saiba mais sobre os dados de blob reidratar da camada de arquivo](storage-blob-rehydration.md)
- [Verifique os preços frequentes, esporádicos e de arquivo em contas de armazenamento de BLOBs e GPv2 por região](https://azure.microsoft.com/pricing/details/storage/)
