---
title: Gerenciar recursos que são criados durante o processo de movimentação da VM no Azure Resource mover
description: Saiba como gerenciar recursos que são criados durante o processo de movimentação da VM no Azure Resource mover
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 09/10/2020
ms.author: raynew
ms.openlocfilehash: 7ad0e73a90e733af0dd752100ebc71908f68181a
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100388474"
---
# <a name="manage-resources-created-for-the-vm-move"></a>Gerenciar recursos criados para a movimentação da VM

Este artigo descreve como gerenciar recursos que são criados explicitamente pelo [Azure Resource mover](overview.md) para facilitar o processo de movimentação da VM. 

Depois de mover as VMs entre regiões, há vários recursos criados pelo movimentador de recursos que devem ser limpos manualmente.

## <a name="delete-resources-created-for-vm-move"></a>Excluir recursos criados para movimentação de VM

Exclua manualmente a coleção de movimentação e Site Recovery recursos criados para a movimentação da VM.

1. Examine os recursos no grupo de recursos ```ResourceMoverRG-<sourceregion>-<target-region>``` .
2. Verifique se a VM e todos os outros recursos de origem na coleção de movimentação foram movidos/excluídos. Isso verifica se não há recursos pendentes usando-os.
2. Exclua esses recursos.

    - O nome da coleção da movimentação é ```movecollection-<sourceregion>-<target-region>-<metadata-region>```.
    - O nome da conta de armazenamento em cache é ```resmovecache<guid>```
    - O nome do cofre é ```ResourceMove-<sourceregion>-<target-region>-GUID```.

## <a name="next-steps"></a>Próximas etapas

Tente [mover uma VM](tutorial-move-region-virtual-machines.md) para outra região com o Resource mover.
