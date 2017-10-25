---
title: Crie uma VM do Linux zoneada com a CLI do Azure | Microsoft Docs
description: Crie uma VM do Linux em uma zona de disponibilidade com a CLI do Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: dlepow
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/19/2017
ms.author: danlep
ms.custom: 
ms.openlocfilehash: 232c2cf1ba0a7de23da10357de9a6e6ad9a0d41d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-linux-virtual-machine-in-an-availability-zone-with-the-azure-cli"></a>Crie uma máquina virtual do Linux em uma zona de disponibilidade com a CLI do Azure

Este artigo aborda usando o CLI do Azure para criar uma máquina virtual do Linux em uma região de disponibilidade do Azure. Uma [zona de disponibilidade](../../availability-zones/az-overview.md) é uma zona fisicamente separada em uma região do Azure. Use zonas de disponibilidade para proteger seus aplicativos e dados de uma improvável falha ou perda de um datacenter inteiro.

[!INCLUDE [availability-zones-preview-statement.md](../../../includes/availability-zones-preview-statement.md)]

Certifique-se de que você instalou a versão mais recente do [CLI do Azure 2.0](/cli/azure/install-az-cli2) e entrou em uma conta do Azure com [az login](/cli/azure/#login).

## <a name="create-resource-group"></a>Criar grupo de recursos

Crie um grupo de recursos com o comando [az group create](/cli/azure/group#az_group_create).  

Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. Você deve criar um grupo de recursos antes de criar uma máquina virtual. Neste exemplo, criaremos um grupo de recursos chamado *myResourceGroupVM* na região *eastus2*. Leste dos EUA 2 é uma das regiões do Azure que dá suporte a zonas de disponibilidade em versão prévia.

```azurecli-interactive 
az group create --name myResourceGroupVM --location eastus2
```

O grupo de recursos é especificado ao criar ou modificar uma VM, que pode ser visto durante este tutorial.

## <a name="create-virtual-machine"></a>Criar máquina virtual

Crie uma máquina virtual com o comando [az vm create](/cli/azure/vm#az_vm_create). 

Há várias opções disponíveis ao criar uma máquina virtual, como a imagem do sistema operacional, as credenciais administrativas e o dimensionamento do disco. Neste exemplo, criaremos uma máquina virtual chamada *myVM* no Ubuntu. A VM é criada na zona de disponibilidade *1*. Por padrão, a VM é criada no tamanho *Standard_DS1_v2*. Esse tamanho é suportado para a visualização de zonas de disponibilidade.

```azurecli-interactive 
az vm create --resource-group myResourceGroupVM --name myVM --image UbuntuLTS --generate-ssh-keys --zone 1
```

A criação da VM pode levar alguns minutos. Depois que a VM tiver sido criada, a CLI do Azure envia informações sobre a VM. Anote o valor `zones` que indica a zona de disponibilidade no qual a máquina virtual está em execução. 

```azurecli-interactive 
{
  "fqdns": "",
  "id": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroupVM/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus2",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroupVM",
  "zones": "1"
}
```

## <a name="zone-for-ip-address-and-managed-disk"></a>Zona de endereço IP e de disco gerenciado

Quando a VM é implantada em uma zona de disponibilidade, o endereço IP e os recursos de disco gerenciado são implantados na mesma região de disponibilidade. Para obter informações sobre esses tópicos, consulte os seguintes recursos.

Primeiro use o comando [az vm list-ip-adresses](/cli/azure/vm#az_vm_list_ip_addresses) para retornar o nome do recurso de endereço IP público no *myVM*. Neste exemplo, o nome é armazenado em uma variável usada em uma etapa posterior.

```azurecli-interactive
ipaddressname=$(az vm list-ip-addresses -g myResourceGroupVM -n myVM --query "[].virtualMachine.network.publicIpAddresses[].name" -o tsv)
```

Agora você pode obter informações sobre o endereço IP:

```azurecli-interactive
az network public-ip show --resource-group myResourceGroupVM --name $ipaddressname
```

A saída mostra que o endereço IP está na mesma região da disponibilidade da VM:

```azurecli-interactive
{
  "dnsSettings": null,
  "etag": "W/\"b7ad25eb-3191-4c8f-9cec-c5e4a3a37d35\"",
  "id": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroupVM/providers/Microsoft.Network/publicIPAddresses/myVMPublicIP",
  "idleTimeoutInMinutes": 4,
  "ipAddress": "52.174.34.95",
  "ipConfiguration": {
    "etag": null,
    "id": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroupVM/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM",
    "name": null,
    "privateIpAddress": null,
    "privateIpAllocationMethod": null,
    "provisioningState": null,
    "publicIpAddress": null,
    "resourceGroup": "myResourceGroupVM",
    "subnet": null
  },
  "location": "eastUS2",
  "name": "myVMPublicIP",
  "provisioningState": "Succeeded",
  "publicIpAddressVersion": "IPv4",
  "publicIpAllocationMethod": "Dynamic",
  "resourceGroup": "myResourceGroupVM",
  "resourceGuid": "8c70a073-09be-4504-0000-000000000000",
  "tags": {},
  "type": "Microsoft.Network/publicIPAddresses",
  "zones": [
    "1"
  ]
}
```

Da mesma forma, verifique se o disco gerenciado da VM está na zona de disponibilidade. Utilize o comando [az vm show](/cli/azure/vm#az_vm_show) para obter a Id do disco. Neste exemplo, o Id do disco é armazenado em uma variável usada em uma etapa posterior. 

```azurecli-interactive
osdiskname=$(az vm show -g myResourceGroupVM -n myVM --query "storageProfile.osDisk.name" -o tsv)
```
Agora você pode obter informações sobre o disco gerenciado:

```azurecli-interactive
az disk show --resource-group myResourceGroupVM --name $osdiskname
```

A saída mostra que o disco gerenciado está na mesma região da disponibilidade da VM:

```azurecli-interactive
{
  "creationData": {
    "createOption": "FromImage",
    "imageReference": {
      "id": "/Subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/Providers/Microsoft.Compute/Locations/westeurope/Publishers/Canonical/ArtifactTypes/VMImage/Offers/UbuntuServer/Skus/16.04-LTS/Versions/latest",
      "lun": null
    },
    "sourceResourceId": null,
    "sourceUri": null,
    "storageAccountId": null
  },
  "diskSizeGb": 30,
  "encryptionSettings": null,
  "id": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroupVM/providers/Microsoft.Compute/disks/osdisk_761c570dab",
  "location": "eastus2",
  "managedBy": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroupVM/providers/Microsoft.Compute/virtualMachines/myVM",
  "name": "osdisk_761c570dab",
  "osType": "Linux",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroupVM",
  "sku": {
    "name": "Premium_LRS",
    "tier": "Premium"
  },
  "tags": {},
  "timeCreated": "2017-09-05T22:16:06.892752+00:00",
  "type": "Microsoft.Compute/disks",
  "zones": [
    "1"
  ]
}
```
 


## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a criar uma VM em uma região de disponibilidade. Saiba mais sobre [regiões e disponibilidade](regions-and-availability.md) para máquinas virtuais do Azure.




