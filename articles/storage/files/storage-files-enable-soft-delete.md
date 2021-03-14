---
title: Habilitar exclusão reversível – Compartilhamentos de arquivo do Azure
description: Saiba como habilitar a exclusão reversível nos compartilhamentos de arquivo do Azure para recuperação de dados e impedir a exclusão acidental.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 12/01/2020
ms.author: rogarana
ms.subservice: files
services: storage
ms.openlocfilehash: 77381700f4257006b50e56ab7ffc037ef99d297c
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102218546"
---
# <a name="enable-soft-delete-on-azure-file-shares"></a>Habilitar exclusão reversível nos compartilhamentos de arquivo do Azure

O Armazenamento do Azure agora oferece a exclusão reversível para compartilhamentos de arquivo para que você possa recuperar os dados mais facilmente quando eles forem excluídos erroneamente por outro usuário de conta de armazenamento ou um aplicativo. Para saber mais sobre a exclusão reversível, confira [Como impedir a exclusão acidental de compartilhamentos de arquivo do Azure](storage-files-prevent-file-share-deletion.md).

As seguintes seções mostram como habilitar e usar a exclusão reversível para compartilhamentos de arquivo do Azure em uma conta de armazenamento existente:

# <a name="portal"></a>[Portal](#tab/azure-portal)

## <a name="getting-started"></a>Introdução

1. Faça logon no [Portal do Azure](https://portal.azure.com/).
1. Navegue até sua conta de armazenamento e selecione **Exclusão reversível** em **Serviço de arquivo**.
1. Selecione **Habilitado** para **exclusão reversível de compartilhamento de arquivo**.
1. Selecione **Período de retenção do compartilhamento de arquivo em dias** e insira um número de sua escolha.
1. Selecione **Salvar** para confirmar suas configurações de retenção de dados.

:::image type="content" source="media/storage-how-to-recover-deleted-account/enable-soft-delete-files.png" alt-text="Captura de tela do painel de configurações de exclusão reversível da conta de armazenamento. Realçando a seção compartilhamentos de arquivo, habilite a alternância, defina um período de retenção e salve. Isso habilitará a exclusão reversível para todos os compartilhamentos de arquivos em sua conta de armazenamento.":::

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Os cmdlets de exclusão reversível estão disponíveis na versão 2.1.3 e mais recentes do [módulo CLI do Azure](/cli/azure/install-azure-cli).

## <a name="getting-started-with-cli"></a>Introdução à CLI

Para habilitar a exclusão reversível, você precisa atualizar as propriedades do serviço do cliente de um arquivo. O seguinte exemplo habilita a exclusão reversível para todos os compartilhamentos de arquivo em uma conta de armazenamento:

```azurecli
az storage account file-service-properties update --enable-delete-retention true -n yourStorageaccount -g yourResourceGroup
```

Você pode verificar se a exclusão reversível está habilitada e ver a respectiva política de retenção com o seguinte comando:

```azurecli
az storage account file-service-properties show -n yourStorageaccount -g yourResourceGroup
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

## <a name="prerequisite"></a>Pré-requisito

Os cmdlets de exclusão reversível estão disponíveis nas versões 4.8.0 e mais recentes do módulo AZ. Storage. 

## <a name="getting-started-with-powershell"></a>Introdução ao PowerShell

Para habilitar a exclusão reversível, você precisa atualizar as propriedades do serviço do cliente de um arquivo. O seguinte exemplo habilita a exclusão reversível para todos os compartilhamentos de arquivo em uma conta de armazenamento:

```azurepowershell-interactive
$rgName = "yourResourceGroupName"
$accountName = "yourStorageAccountName"

Update-AzStorageFileServiceProperty -ResourceGroupName $rgName -StorageAccountName $accountName -EnableShareDeleteRetentionPolicy $true -ShareRetentionDays 7
```

Você pode verificar se a exclusão reversível está habilitada e ver a respectiva política de retenção com o seguinte comando:

```azurepowershell-interactive
Get-AzStorageFileServiceProperty -ResourceGroupName $rgName -StorageAccountName $accountName
```
---

## <a name="restore-soft-deleted-file-share"></a>Restaurar compartilhamento de arquivo com exclusão reversível

# <a name="portal"></a>[Portal](#tab/azure-portal)

Para restaurar um compartilhamento de arquivo com exclusão reversível:

1. Navegue até sua conta de armazenamento e selecione **Compartilhamentos de arquivo**.
1. Na folha do compartilhamento de arquivo, habilite **Mostrar compartilhamentos excluídos** para exibir os compartilhamentos que foram excluídos de modo reversível.

    Isso exibirá todos os compartilhamentos atualmente em um estado **Excluído**.

    :::image type="content" source="media/storage-how-to-recover-deleted-account/undelete-file-share.png" alt-text="Se a coluna status, a coluna ao lado da coluna nome, estiver definida como Excluído, o compartilhamento de arquivo estará em um estado excluído de modo reversível. Ele será excluído permanentemente após o período de retenção especificado por você.":::

1. Selecione o compartilhamento e selecione **cancelar exclusão**, o que vai restaurá-lo.

    Você pode confirmar se o compartilhamento foi restaurado, pois o status dele muda para **Ativo**.

    :::image type="content" source="media/storage-how-to-recover-deleted-account/restored-file-share.png" alt-text="Se a coluna status, a coluna ao lado da coluna nome, estiver definida como Ativo, o compartilhamento de arquivo terá sido restaurado.":::

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Os cmdlets de exclusão reversível estão disponíveis na versão 2.1.3 do CLI do Azure. Para restaurar um compartilhamento de arquivos com exclusão reversível, primeiro você deve obter o `--deleted-version` valor do compartilhamento. Para obter esse valor, use o seguinte comando para listar todos os compartilhamentos excluídos para sua conta de armazenamento:

```azurecli
az storage share-rm list --storage-account yourStorageaccount --include-deleted
```

Depois de identificar o compartilhamento que você deseja restaurar, você pode usá-lo com o seguinte comando para restaurá-lo:

```azurecli
az storage share-rm restore -n deletedshare --deleted-version 01D64EB9886F00C4 -g yourResourceGroup --storage-account yourStorageaccount
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Os cmdlets de exclusão reversível estão disponíveis nas versões 4.8.0 e mais recentes do módulo AZ. Storage. Para restaurar um compartilhamento de arquivos com exclusão reversível, primeiro você deve obter o `-DeletedShareVersion` valor do compartilhamento. Para obter esse valor, use o seguinte comando para listar todos os compartilhamentos excluídos para sua conta de armazenamento:

```azurepowershell-interactive
Get-AzRmStorageShare -ResourceGroupName $rgname -StorageAccountName $accountName -IncludeDeleted
```

Depois de identificar o compartilhamento que você deseja restaurar, você pode usá-lo com o seguinte comando para restaurá-lo:

```azurepowershell-interactive
Restore-AzRmStorageShare -ResourceGroupName $rgname -StorageAccountName $accountName -DeletedShareVersion 01D5E2783BDCDA97
```
---

## <a name="disable-soft-delete"></a>Desabilitar exclusão reversível

Se você quiser parar de usar a exclusão reversível ou excluir permanentemente um compartilhamento de arquivo, siga estas instruções:

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Navegue até sua conta de armazenamento e selecione **Exclusão reversível** em **Configurações**.
1. Em **Compartilhamentos de arquivo**, selecione **Desabilitado** para **Exclusão reversível para compartilhamentos de arquivo**.
1. Selecione **Salvar** para confirmar suas configurações de retenção de dados.

    :::image type="content" source="media/storage-how-to-recover-deleted-account/disable-soft-delete-files.png" alt-text="Desabilitar a exclusão reversível permitirá que você exclua imediatamente e permanentemente todos os compartilhamentos de arquivo em sua conta de armazenamento sempre que quiser.":::

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Os cmdlets de exclusão reversível estão disponíveis na versão 2.1.3 do CLI do Azure. Você pode usar o seguinte comando para desabilitar a exclusão reversível em sua conta de armazenamento:

```azurecli
az storage account file-service-properties update --enable-delete-retention false -n yourStorageaccount -g yourResourceGroup
```
# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Os cmdlets de exclusão reversível estão disponíveis nas versões 4.8.0 e mais recentes do módulo AZ. Storage. Você pode usar o seguinte comando para desabilitar a exclusão reversível em sua conta de armazenamento:

```azurepowershell-interactive
Update-AzStorageFileServiceProperty -ResourceGroupName $rgName -StorageAccountName $accountName -EnableShareDeleteRetentionPolicy $false
```
---

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre uma outra forma de proteção e recuperação de dados, consulte nosso artigo [visão geral de instantâneos de compartilhamento para arquivos do Azure](storage-snapshots-files.md).