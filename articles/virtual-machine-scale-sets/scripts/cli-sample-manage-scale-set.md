---
title: Exemplo da CLI do Azure para gerenciamento de conjuntos de dimensionamento de máquinas virtuais
description: Este exemplo mostra como adicionar discos a um conjunto de dimensionamento de máquinas virtuais. Você pode atualizar os discos e adicionar suas máquinas virtuais à autenticação do Azure AD.
author: mimckitt
ms.author: mimckitt
ms.date: 02/04/2021
ms.topic: sample
ms.service: virtual-machine-scale-sets
ms.devlang: azurecli
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1abdf7ae15753d78ac8728f57e9b0cd5dcd9165e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101672594"
---
# <a name="create-and-manage-virtual-machine-scale-set"></a>Criar e gerenciar um conjunto de dimensionamento de máquinas virtuais

Use esses comandos de exemplo para criar um protótipo de um conjunto de dimensionamento de máquinas virtuais usando a CLI do Azure.

Esses comandos de exemplo demonstram as seguintes operações:

* Crie um conjunto de dimensionamento de máquina virtual.
* Adicionar e atualizar discos novos ou existentes para um conjunto de dimensionamento ou para uma instância do conjunto.
* Adicionar conjunto de dimensionamento à autenticação do Azure AD (Azure Active Directory).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="sample-commands"></a>Comandos de exemplo

```azurecli
# Create a resource group
az group create --name MyResourceGroup --location eastus

# Create virtual machine scale set
az vmss create --resource-group MyResourceGroup --name myScaleSet --instance-count 2 \
    --image UbuntuLTS --upgrade-policy-mode automatic --admin-username azureuser \
    --generate-ssh-keys

# Attach a new managed disk to your scale set
az vmss disk attach --resource-group MyResourceGroup --vmss-name myScaleSet --size-gb 50
```

Depois de adicionar um novo disco de dados, formate e monte o disco. Para máquinas virtuais do Windows, confira [Anexar um disco de dados gerenciado a uma VM do Windows usando o portal do Azure](../../virtual-machines/windows/attach-managed-disk-portal.md). Para máquinas virtuais do Linux, confira [Adicionar um disco a uma VM do Linux](../../virtual-machines/linux/add-disk.md).

```azurecli
# Attach an existing managed disk to a virtual machine instance in your scale set
az vmss disk attach --resource-group MyResourceGroup --disk myDataDisk \
    --vmss-name myScaleSet --instance-id 0

# See the instances in your virtual machine scale set
az vmss list-instances --resource-group MyResourceGroup --name myScaleSet --output table

# See the disks for your virtual machine
az disk list --resource-group MyResourceGroup \
    --query "[*].{Name:name,Gb:diskSizeGb,Tier:accountType}" --output table

# Deallocate the virtual machine
az vmss deallocate --resource-group MyResourceGroup --name myScaleSet --instance-ids 0 

# Resize the disk
az disk update --resource-group MyResourceGroup --name myDataDisk --size-gb 200

# Restart the disk
az vmss restart --resource-group MyResourceGroup --name myScaleSet --instance-ids 0
```

Para usar o disco expandido, expanda a partição subjacente. Para obter mais informações, confira [Expandir uma partição de disco e o sistema de arquivos](../../virtual-machines/linux/expand-disks.md#expand-a-disk-partition-and-filesystem).

Este exemplo redimensionou um disco de dados. Você pode usar o mesmo procedimento para atualizar um disco do SO. Para obter mais informações sobre uma máquina virtual do Windows, confira [Como expandir a unidade do sistema operacional de uma máquina virtual](../../virtual-machines/windows/expand-os-disk.md). Para obter mais informações sobre máquinas virtuais do Linux, confira [Expandir discos rígidos virtuais em uma VM do Linux com a CLI do Azure](../../virtual-machines/linux/expand-disks.md).

```azurecli
# Enable managed service identity on your scale set. This is required to authenticate and interact with other Azure services using bearer tokens.
az vmss identity assign --resource-group MyResourceGroup --name myScaleSet --role Owner \
    --scope /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyResourceGroup

# Connect to Azure AD authentication
az vmss extension set --resource-group MyResourceGroup --name AADLoginForWindows \
    --publisher Microsoft.Azure.ActiveDirectory --vmss-name myScaleSet

# Upgrade one instance of a scale set virtual machine
az vmss update-instances --resource-group MyResourceGroup --name myScaleSet --instance-ids 0 

# Remove a managed disk from the scale set
az vmss disk detach --resource-group MyResourceGroup --vmss-name myScaleSet --lun 0

# Remove a managed disk from an instance
az vmss disk detach --resource-group MyResourceGroup --vmss-name myScaleSet --instance-id 1 --lun 0

# Delete the pre-existing disk
az disk delete --resource-group MyResourceGroup --disk myDataDisk
```

## <a name="clean-up-resources"></a>Limpar os recursos

Após usar esses comandos, execute o comando a seguir para remover o grupo de recursos e todos os recursos associados a ele.

```azurecli
az group delete --name MyResourceGroup
```

## <a name="azure-cli-references-used-in-this-article"></a>Referências da CLI do Azure usadas neste artigo

* [az disk delete](/cli/azure/disk#az_disk_delete)
* [az disk list](/cli/azure/disk#az_disk_list)
* [az disk update](/cli/azure/disk#az_disk_update)
* [az group create](/cli/azure/group#az_group_create)
* [az vmss create](/cli/azure/vmss#az_vmss_create)
* [az virtual machine scale set deallocate](/cli/azure/vmss#az_vmss_deallocate)
* [az vmss disk attach](/cli/azure/vmss/disk#az_vmss_disk_attach)
* [az vmss disk detach](/cli/azure/vmss/disk#az_vmss_disk_detach)
* [az vmss extension set](/cli/azure/vmss/extension#az_vmss_extension_set)
* [az vmss identity assign](/cli/azure/vmss/identity#az_vmss_identity_assign)
* [az vmss list-instances](/cli/azure/vmss#az_vmss_list_instances)
* [az vmss restart](/cli/azure/vmss#az_vmss_restart)
* [az vmss update-instances](/cli/azure/vmss#az_vmss_update_instances)