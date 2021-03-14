---
title: Copiar uma VM do Linux usando CLI do Azure
description: Saiba como criar uma cópia da sua VM Linux do Azure usando a CLI do Azure e Managed Disks.
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 10/17/2018
ms.author: cynthn
ms.custom: legacy, devx-track-azurecli
ms.openlocfilehash: 05b45745565dface6b29f5a05cb1b557f4a49f6e
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102558382"
---
# <a name="create-a-copy-of-a-linux-vm-by-using-azure-cli-and-managed-disks"></a>Criar uma cópia da sua VM Linux usando a CLI do Azure e Managed Disks

Este artigo mostra como criar uma cópia de sua VM (máquina virtual) do Azure executando o Linux usando o CLI do Azure. Para copiar, criar, armazenar e compartilhar imagens de VM em escala, consulte [galerias de imagens compartilhadas](../shared-images-cli.md).

Você também pode [carregar e criar uma VM com base em um VHD](upload-vhd.md).

## <a name="prerequisites"></a>Pré-requisitos

-   Instale a [CLI do Azure](/cli/azure/install-az-cli2).

-   Entre em uma conta do Azure com [az login](/cli/azure/reference-index#az-login).

-   Tenha uma VM do Azure para usar como origem para a cópia.

## <a name="stop-the-source-vm"></a>Pare a VM de origem

Desaloque a VM de origem usando [az vm deallocate](/cli/azure/vm#az-vm-deallocate).
O seguinte exemplo desaloca a VM *myVM* no grupo de recursos chamado *myResourceGroup*:

```azurecli
az vm deallocate \
    --resource-group myResourceGroup \
    --name myVM
```

## <a name="copy-the-source-vm"></a>Copiar a VM de origem

Para copiar uma máquina virtual, você deve criar uma cópia do disco rígido virtual subjacente. Esse processo cria um disco rígido virtual (VHD) especializado como disco gerenciado que contém a mesma configuração e configurações da VM de origem.

Para saber mais sobre Azure Managed Disks, veja [Visão geral dos Azure Managed Disks](../managed-disks-overview.md). 

1.  Lista cada VM e o nome do disco do sistema operacional com [az vm list](/cli/azure/vm#az-vm-list). O exemplo a seguir lista todas as VMs no grupo de recursos denominado *myResourceGroup*:
    
    ```azurecli
    az vm list -g myResourceGroup \
         --query '[].{Name:name,DiskName:storageProfile.osDisk.name}' \
         --output table
    ```

    A saída deverá ser semelhante ao seguinte exemplo:

    ```azurecli
    Name    DiskName
    ------  --------
    myVM    myDisk
    ```

1.  Copie o disco criando um novo disco gerenciado e usando [az disk create](/cli/azure/disk#az-disk-create). O exemplo a seguir cria um disco chamado *myCopiedDisk* do disco gerenciado chamado *myDisk*:

    ```azurecli
    az disk create --resource-group myResourceGroup \
         --name myCopiedDisk --source myDisk
    ``` 

1.  Verifique se os discos gerenciados agora em seu grupo de recursos usando [az disk list](/cli/azure/disk#az-disk-list). O exemplo a seguir lista os discos gerenciados no grupo de recursos denominado *myResourceGroup*:

    ```azurecli
    az disk list --resource-group myResourceGroup --output table
    ```


## <a name="set-up-a-virtual-network"></a>Configurar uma rede virtual

As etapas opcionais a seguir criam uma nova rede virtual, uma sub-rede, um endereço IP público e uma placa de adaptador de rede virtual (NIC).

Se você estiver copiando uma VM para fins ou implantações adicionais de solução de problemas, não convém usar uma máquina virtual em uma rede virtual existente.

Se quiser criar uma infraestrutura de rede virtual para as VMs copiadas, siga as próximas etapas. Se você não quiser criar uma rede virtual, vá para [Criar uma VM](#create-a-vm).

1.  Crie a rede virtual usando [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create). O exemplo a seguir cria uma rede virtual chamada *myVnet* e uma sub-rede chamada *mySubnet*:

    ```azurecli
    az network vnet create --resource-group myResourceGroup \
        --location eastus --name myVnet \
        --address-prefix 192.168.0.0/16 \
        --subnet-name mySubnet \
        --subnet-prefix 192.168.1.0/24
    ```

1.  Crie um IP público usando [az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create). O exemplo a seguir cria um IP público chamado *myPublicIP* com o nome DNS de *mypublicdns*. (Como o nome DNS deve ser exclusivo, forneça um nome exclusivo.)

    ```azurecli
    az network public-ip create --resource-group myResourceGroup \
        --location eastus --name myPublicIP --dns-name mypublicdns \
        --allocation-method static --idle-timeout 4
    ```

1.  Criar a NIC usando [az network nic create](/cli/azure/network/nic#az-network-nic-create).
    O exemplo a seguir cria uma NIC chamada *myNic* anexada à sub-rede *mySubnet*:

    ```azurecli
    az network nic create --resource-group myResourceGroup \
        --location eastus --name myNic \
        --vnet-name myVnet --subnet mySubnet \
        --public-ip-address myPublicIP
    ```

## <a name="create-a-vm"></a>Criar uma máquina virtual

Criar uma VM usando [az vm create](/cli/azure/vm#az-vm-create).

Especifique o disco copiado gerenciado para usar como o disco do sistema operacional (`--attach-os-disk`) da seguinte maneira:

```azurecli
az vm create --resource-group myResourceGroup \
    --name myCopiedVM --nics myNic \
    --size Standard_DS1_v2 --os-type Linux \
    --attach-os-disk myCopiedDisk
```

## <a name="next-steps"></a>Próximas etapas

Para saber como usar uma [Galeria de imagens compartilhadas](../shared-images-cli.md) para gerenciar imagens de VM.
