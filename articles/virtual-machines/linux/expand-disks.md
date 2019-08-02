---
title: Expandir discos rígidos virtuais em uma VM Linux no Azure | Microsoft Docs
description: Saiba como expandir discos rígidos virtuais em uma VM Linux com a CLI do Azure
services: virtual-machines-linux
documentationcenter: ''
author: roygara
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/15/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: cc61ad7fb6fb741c03fe8395b4cd460c3bd33c53
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67667834"
---
# <a name="expand-virtual-hard-disks-on-a-linux-vm-with-the-azure-cli"></a>Expandir discos rígidos virtuais em uma VM do Linux com a CLI do Azure

Este artigo descreve como expandir discos gerenciados para uma máquina virtual (VM) do Linux com a CLI do Azure. Você pode [adicionar discos de dados](add-disk.md) para fornecer espaço de armazenamento adicional e também expandir um disco de dados existente. O tamanho padrão do disco rígido virtual do sistema operacional (SO) é geralmente de 30 GB em uma VM do Linux no Azure. 

> [!WARNING]
> Sempre certifique-se de que seu sistema de arquivos está em um estado íntegro, o tipo de tabela de partição de disco compatíveis com o novo tamanho e garantir que seus dados são feitos backup antes de executar operações de redimensionamento do disco. Para saber mais, confira [Fazer backup de máquinas virtuais do Linux no Azure](tutorial-backup-vms.md). 

## <a name="expand-an-azure-managed-disk"></a>Expandir um disco gerenciado do Azure
Verifique se você tem o [CLI do Azure](/cli/azure/install-az-cli2) mais recente do Azure instalada e está conectada a uma conta do Azure usando [az login](/cli/azure/reference-index#az-login).

Este artigo requer uma VM existente no Azure com, pelo menos, um disco de dados anexado e preparado. Caso ainda não tenha uma VM que possa ser usada, confira [Criar e preparar uma VM com discos de dados](tutorial-manage-disks.md#create-and-attach-disks).

Nos exemplos a seguir, substitua os nomes de parâmetro de exemplo, como *myResourceGroup* e *myVM* com seus próprios valores.

1. Operações em discos rígidos virtuais não podem ser executadas com a VM em execução. Desaloque a VM com [az vm deallocate](/cli/azure/vm#az-vm-deallocate). O exemplo a seguir desaloca a VM chamada *myVM* no grupo de recursos chamado *myResourceGroup*:

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

    > [!NOTE]
    > A VM deve ser desalocada para que o disco rígido virtual seja expandido. Parar a VM com `az vm stop` não libera os recursos de computação. Para liberar os recursos de computação, use `az vm deallocate`.

1. Veja uma lista de discos gerenciados em um grupo de recursos com [az disk list](/cli/azure/disk#az-disk-list). O exemplo a seguir exibe uma lista de discos gerenciados no grupo de recursos chamado *myResourceGroup*:

    ```azurecli
    az disk list \
        --resource-group myResourceGroup \
        --query '[*].{Name:name,Gb:diskSizeGb,Tier:accountType}' \
        --output table
    ```

    Expanda o disco necessário com [az disk update](/cli/azure/disk#az-disk-update). O exemplo a seguir expande o disco gerenciado denominado *myDataDisk* para *200* GB:

    ```azurecli
    az disk update \
        --resource-group myResourceGroup \
        --name myDataDisk \
        --size-gb 200
    ```

    > [!NOTE]
    > Quando você expande um disco gerenciado, o tamanho atualizado é arredondado para o tamanho de disco gerenciado mais próximo. Para obter uma tabela dos tamanhos de disco gerenciado e as camadas disponíveis, consulte [Visão geral do Azure Managed Disks – Preço e cobrança](../windows/managed-disks-overview.md).

1. Inicie a VM com [az vm start](/cli/azure/vm#az-vm-start). O exemplo a seguir inicia a VM chamada *myVM* no grupo de recursos chamado *myResourceGroup*:

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```


## <a name="expand-a-disk-partition-and-filesystem"></a>Expanda uma partição de disco e o sistema de arquivos
Para usar um disco expandido, expanda a partição subjacente e o sistema de arquivos.

1. SSH da VM com as credenciais apropriadas. Você pode ver o endereço IP público de sua VM com [az vm show](/cli/azure/vm#az-vm-show):

    ```azurecli
    az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --o tsv
    ```

1. Expanda a partição subjacente e o sistema de arquivos.

    a. Se o disco já estiver montado, desmonte-o:

    ```bash
    sudo umount /dev/sdc1
    ```

    b. Use `parted` para exibir informações de disco e redimensionar a partição:

    ```bash
    sudo parted /dev/sdc
    ```

    Exiba informações sobre o layout da partição existente com `print`. A saída é semelhante ao exemplo a seguir, que mostra que o disco subjacente é de 215 GB:

    ```bash
    GNU Parted 3.2
    Using /dev/sdc1
    Welcome to GNU Parted! Type 'help' to view a list of commands.
    (parted) print
    Model: Unknown Msft Virtual Disk (scsi)
    Disk /dev/sdc1: 215GB
    Sector size (logical/physical): 512B/4096B
    Partition Table: loop
    Disk Flags:
    
    Number  Start  End    Size   File system  Flags
        1      0.00B  107GB  107GB  ext4
    ```

    c. Expanda a partição com `resizepart`. Insira o número de partição, *1*, e um tamanho para a nova partição:

    ```bash
    (parted) resizepart
    Partition number? 1
    End?  [107GB]? 215GB
    ```

    d. Para sair, digite `quit`.

1. Com a partição redimensionada, verifique a consistência da partição com `e2fsck`:

    ```bash
    sudo e2fsck -f /dev/sdc1
    ```

1. Redimensione o sistema de arquivos com `resize2fs`:

    ```bash
    sudo resize2fs /dev/sdc1
    ```

1. Monte a partição no local desejado, como `/datadrive`:

    ```bash
    sudo mount /dev/sdc1 /datadrive
    ```

1. Para verificar o disco de dados foi redimensionado, use `df -h`. A seguinte saída de exemplo mostra a unidade de dados */dev/sdc1* agora é de 200 GB:

    ```bash
    Filesystem      Size   Used  Avail Use% Mounted on
    /dev/sdc1        197G   60M   187G   1% /datadrive
    ```

## <a name="next-steps"></a>Próximas etapas
* Se precisar de armazenamento adicional, você também pode [adicionar discos de dados a uma VM Linux](add-disk.md). 
* Para obter mais informações sobre a criptografia de disco, consulte [Criptografar discos em uma VM do Linux usando a CLI do Azure](encrypt-disks.md).
