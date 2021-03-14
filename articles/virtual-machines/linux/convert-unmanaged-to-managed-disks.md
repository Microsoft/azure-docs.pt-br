---
title: Converter uma VM do Linux de discos não gerenciados em discos gerenciados
description: Como converter uma VM do Linux de discos não gerenciados em discos gerenciados usando CLI do Azure.
author: roygara
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 12/15/2017
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 1f3b62f8c05edffa1b55bf3d8cd24494b1c918bd
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102558484"
---
# <a name="convert-a-linux-virtual-machine-from-unmanaged-disks-to-managed-disks"></a>Converter uma máquina virtual Linux de discos não gerenciados em Managed Disks

Se você tiver máquinas virtuais (VMs) do Linux existentes que usam discos não gerenciados, é possível converter as VMs para usar o [Azure Managed Disks](../managed-disks-overview.md). Esse processo converte o disco do sistema operacional e os discos de dados anexados.

Este artigo mostra como converter VMs usando a CLI do Azure. Se você precisar instalar ou atualizá-lo, confira [instalar a CLI do Azure](/cli/azure/install-azure-cli). 

## <a name="before-you-begin"></a>Antes de começar
* Examine [as perguntas frequentes sobre migração para Managed Disks](../faq-for-disks.md#migrate-to-managed-disks).

[!INCLUDE [virtual-machines-common-convert-disks-considerations](../../../includes/virtual-machines-common-convert-disks-considerations.md)]

* Os VHDs originais e a conta de armazenamento usados pela VM antes da conversão não são excluídos. Eles continuam a incorrer em encargos. Para evitar ser cobrado por esses artefatos, exclua os blobs VHD originais depois de verificar que a conversão foi concluída. Se você precisar encontrar esses discos desanexados para excluí-los, consulte nosso artigo [Localizar e excluir discos gerenciados e não geridos do Azure desconectados](find-unattached-disks.md).

## <a name="convert-single-instance-vms"></a>Converter VMs de instância única
Esta seção aborda como converter suas VMs de instância única do Azure de discos não gerenciados em discos gerenciados. (Se suas VMs estiverem em um conjunto de disponibilidade, consulte a próxima seção.) Você pode usar esse processo para converter as VMs de discos não gerenciados Premium (SSD) em discos gerenciados Premium ou discos não gerenciados padrão (HDD) em discos gerenciados Standard.

1. Desaloque a VM usando [az vm deallocate](/cli/azure/vm). O seguinte exemplo desaloca a VM `myVM` no grupo de recursos chamado `myResourceGroup`:

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

2. Converta a VM em Managed Disks com [az vm convert](/cli/azure/vm). O processo a seguir converte a VM nomeada `myVM`, incluindo o disco do sistema operacional e quaisquer discos de dados:

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

3. Inicie a VM após a conversão em Managed Disks usando [az vm start](/cli/azure/vm). O exemplo a seguir inicia a VM `myVM` no grupo de recursos `myResourceGroup`.

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## <a name="convert-vms-in-an-availability-set"></a>Converter VMs em um conjunto de disponibilidade

Se as VMs que você deseja converter em discos gerenciados estão em um conjunto de disponibilidade, converta primeiro o conjunto de disponibilidade em um conjunto de disponibilidade gerenciado.

Todas as VMs no conjunto de disponibilidade devem ser desalocadas antes de converter o conjunto de disponibilidade. Planeje a conversão de todas as VMs em Managed Disks após conversão do próprio conjunto de disponibilidade em um conjunto de disponibilidade gerenciada. Então, inicie todas as VMs e continuar a operar normalmente.

1. Liste todas as VMs em um conjunto de disponibilidade usando [az vm availability-set list](/cli/azure/vm/availability-set). O seguinte exemplo lista todas as VMs em um conjunto de disponibilidade `myAvailabilitySet` no grupo de recursos `myResourceGroup`:

    ```azurecli
    az vm availability-set show \
        --resource-group myResourceGroup \
        --name myAvailabilitySet \
        --query [virtualMachines[*].id] \
        --output table
    ```

2. Desaloque todas as VMs usando [az vm deallocate](/cli/azure/vm). O seguinte exemplo desaloca a VM `myVM` no grupo de recursos chamado `myResourceGroup`:

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

3. Converta um conjunto de disponibilidade usando [az vm availability-set convert](/cli/azure/vm/availability-set). O exemplo a seguir converte o conjunto de disponibilidade `myAvailabilitySet` no grupo de recursos `myResourceGroup`:

    ```azurecli
    az vm availability-set convert \
        --resource-group myResourceGroup \
        --name myAvailabilitySet
    ```

4. Converta todas as VMs em Managed Disks usando [az vm convert](/cli/azure/vm). O processo a seguir converte a VM nomeada `myVM`, incluindo o disco do sistema operacional e quaisquer discos de dados:

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

5. Inicie todas as VMs após a conversão em Managed Disks usando [az vm start](/cli/azure/vm). O seguinte exemplo inicia a VM chamada `myVM` no grupo de recursos chamado `myResourceGroup`:

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## <a name="convert-using-the-azure-portal"></a>Converter usando o portal do Azure

Também é possível converter discos não gerenciados em discos gerenciados usando o portal do Azure.

1. Entre no [portal do Azure](https://portal.azure.com).
2. Selecione a VM na lista de VMs no portal.
3. Na folha da VM, selecione **Discos** no menu.
4. Na parte superior da folha **Discos**, selecione **Migrar para discos gerenciados**.
5. Se sua VM estiver em um conjunto de disponibilidade, haverá um aviso na folha **Migrar para discos gerenciados** informando que você precisa converter o conjunto de disponibilidade primeiro. O aviso deve ter um link em que você pode clicar para converter o conjunto de disponibilidade. Quando o conjunto de disponibilidade for convertido ou se sua VM não estiver em um conjunto de disponibilidade, clique em **Migrar** para iniciar o processo de migração de seus discos para discos gerenciados.

A VM será interrompida e reiniciada após a conclusão da migração.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as opções de armazenamento, confira a [Visão geral dos Azure Managed Disks](../managed-disks-overview.md).
