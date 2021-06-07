---
title: incluir arquivo
description: incluir arquivo
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 02/03/2021
ms.author: fauhse
ms.custom: include file
ms.openlocfilehash: a086aae35c9a800c6a4cfc3e872a34438bc84095
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99569452"
---
Por padrão, os compartilhamentos de arquivo padrão podem abranger até 5 TiB, mas você pode aumentar o limite de compartilhamento para 100 TiB. Para aumentar o limite de compartilhamento, habilite **Compartilhamento de arquivo grandes** na sua conta de armazenamento. As contas de armazenamento Premium (contas de armazenamento *FileStorage*) não têm o sinalizador de recurso de compartilhamento de arquivo grande, pois todos os compartilhamentos de arquivo Premium já estão habilitados para provisionar até a capacidade total de 100 TiB.

Você só pode habilitar compartilhamentos de arquivo grande em contas de armazenamento padrão com redundância local ou de zona. Depois de habilitar o sinalizador de recurso de compartilhamento de arquivo grande, não será possível alterar o nível de redundância para armazenamento com redundância geográfica ou com redundância de zona geográfica.

Para habilitar compartilhamentos de arquivo grande em uma conta de armazenamento existente, navegue até **Compartilhamentos de arquivo** no sumário da conta de armazenamento.
Nessa folha, selecione **Compartilhar capacidade**, altere a capacidade de compartilhamento para **100 TiB** e selecione **Salvar**.

:::image type="content" source="media/storage-files-tiers-enable-large-shares/enable-lfs.png" alt-text="Uma captura de tela da configuração de aceitação habilitar o botão de alternância do compartilhamento de arquivo grande no portal do Azure." lightbox="media/storage-files-tiers-enable-large-shares/increase-share-capacity.png":::

Você também pode habilitar compartilhamentos de arquivo de 100 TiB por meio do cmdlet [`Set-AzStorageAccount`](/powershell/module/az.storage/set-azstorageaccount) do PowerShell e do comando [`az storage account update`](/cli/azure/storage/account#az-storage-account-update) da CLI do Azure. Para obter instruções detalhadas sobre como habilitar compartilhamentos de arquivo grande, confira [habilitar e criar compartilhamentos de arquivo grande](../articles/storage/files/storage-files-how-to-create-large-file-share.md).

Para saber mais sobre como criar compartilhamentos de arquivo em novas contas de armazenamento, confira [criar um compartilhamento de arquivo do Azure](../articles/storage/files/storage-how-to-create-file-share.md).