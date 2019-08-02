---
title: Crie uma VM do Linux zoneada com a CLI do Azure | Microsoft Docs
description: Crie uma VM do Linux em uma zona de disponibilidade com a CLI do Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: dlepow
manager: gwallace
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/05/2018
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: 6bdbc566215fb7e68109b523fb2af9bca16c328c
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849695"
---
# <a name="create-a-linux-virtual-machine-in-an-availability-zone-with-the-azure-cli"></a>Crie uma máquina virtual do Linux em uma zona de disponibilidade com a CLI do Azure

Este artigo aborda usando o CLI do Azure para criar uma máquina virtual do Linux em uma região de disponibilidade do Azure. Uma [zona de disponibilidade](../../availability-zones/az-overview.md) é uma zona fisicamente separada em uma região do Azure. Use zonas de disponibilidade para proteger seus aplicativos e dados de uma improvável falha ou perda de um datacenter inteiro.

Para usar uma zona de disponibilidade, crie a máquina virtual em uma [região do Azure com suporte](../../availability-zones/az-overview.md#services-support-by-region).

Verifique se você instalou a versão mais recente da [CLI do Azure](/cli/azure/install-az-cli2) e entrou em uma conta do Azure com [az login](/cli/azure/reference-index).


## <a name="check-vm-sku-availability"></a>Verificar a disponibilidade do SKU de VM
A disponibilidade de tamanhos de VM, ou de SKUs, pode variar por região e por zona. Para ajudá-lo a planejar o uso de Zonas de Disponibilidade, você poderá listar os SKUs de VM disponíveis por região e zona do Azure. Essa capacidade garante que você escolha um tamanho adequado de VM e obtenha a resiliência desejada entre as zonas. Para saber mais sobre os diferentes tipos e tamanhos de VM, confira [Visão geral de tamanhos de VM](sizes.md).

Você pode exibir os SKUs de VM disponíveis usando o comando [az vm list-skus](/cli/azure/vm). O exemplo a seguir lista os SKUs de VM disponíveis na região *eastus2*:

```azurecli
az vm list-skus --location eastus2 --output table
```

A saída é semelhante ao exemplo condensado a seguir, que mostra as Zonas de Disponibilidade nas quais cada tamanho de VM está disponível:

```azurecli
ResourceType      Locations  Name               [...]    Tier       Size     Zones
----------------  ---------  -----------------           ---------  -------  -------
virtualMachines   eastus2    Standard_DS1_v2             Standard   DS1_v2   1,2,3
virtualMachines   eastus2    Standard_DS2_v2             Standard   DS2_v2   1,2,3
[...]
virtualMachines   eastus2    Standard_F1s                Standard   F1s      1,2,3
virtualMachines   eastus2    Standard_F2s                Standard   F2s      1,2,3
[...]
virtualMachines   eastus2    Standard_D2s_v3             Standard   D2_v3    1,2,3
virtualMachines   eastus2    Standard_D4s_v3             Standard   D4_v3    1,2,3
[...]
virtualMachines   eastus2    Standard_E2_v3              Standard   E2_v3    1,2,3
virtualMachines   eastus2    Standard_E4_v3              Standard   E4_v3    1,2,3
```


## <a name="create-resource-group"></a>Criar grupo de recursos

Crie um grupo de recursos com o comando [az group create](/cli/azure/group).  

Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. Você deve criar um grupo de recursos antes de criar uma máquina virtual. Neste exemplo, criaremos um grupo de recursos chamado *myResourceGroupVM* na região *eastus2*. Leste dos EUA 2 é uma das regiões do Azure que dá suporte a zonas de disponibilidade.

```azurecli 
az group create --name myResourceGroupVM --location eastus2
```

O grupo de recursos é especificado ao criar ou modificar uma VM, que pode ser visto durante este tutorial.

## <a name="create-virtual-machine"></a>Criar máquina virtual

Crie uma máquina virtual com o comando [az vm create](/cli/azure/vm). 

Há várias opções disponíveis ao criar uma máquina virtual, como a imagem do sistema operacional, as credenciais administrativas e o dimensionamento do disco. Neste exemplo, criaremos uma máquina virtual chamada *myVM* no Ubuntu. A VM é criada na zona de disponibilidade *1*. Por padrão, a VM é criada no tamanho *Standard_DS1_v2*.

```azurecli-interactive 
az vm create --resource-group myResourceGroupVM --name myVM --location eastus2 --image UbuntuLTS --generate-ssh-keys --zone 1
```

A criação da VM pode levar alguns minutos. Depois que a VM tiver sido criada, a CLI do Azure envia informações sobre a VM. Anote o valor `zones` que indica a zona de disponibilidade no qual a máquina virtual está em execução. 

```azurecli 
{
  "fqdns": "",
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroupVM/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus2",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroupVM",
  "zones": "1"
}
```

## <a name="confirm-zone-for-managed-disk-and-ip-address"></a>Confirme a zona do disco gerenciado e endereço IP

Quando a VM é implantada em uma zona de disponibilidade, um disco gerenciado para a VM é criado na mesma zona de disponibilidade. Por padrão, um endereço IP público também é criado nessa região. Para obter informações sobre esses tópicos, consulte os seguintes recursos.

Para verificar se o disco gerenciado da VM está na zona de disponibilidade, use o comando [AZ VM show](/cli/azure/vm) para retornar a ID do disco. Neste exemplo, a ID do disco é armazenada em uma variável que é usada em uma etapa posterior. 

```azurecli-interactive
osdiskname=$(az vm show -g myResourceGroupVM -n myVM --query "storageProfile.osDisk.name" -o tsv)
```
Agora você pode obter informações sobre o disco gerenciado:

```azurecli-interactive
az disk show --resource-group myResourceGroupVM --name $osdiskname
```

A saída mostra que o disco gerenciado está na mesma região da disponibilidade da VM:

```azurecli
{
  "creationData": {
    "createOption": "FromImage",
    "imageReference": {
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westeurope/Publishers/Canonical/ArtifactTypes/VMImage/Offers/UbuntuServer/Skus/16.04-LTS/Versions/latest",
      "lun": null
    },
    "sourceResourceId": null,
    "sourceUri": null,
    "storageAccountId": null
  },
  "diskSizeGb": 30,
  "encryptionSettings": null,
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroupVM/providers/Microsoft.Compute/disks/osdisk_761c570dab",
  "location": "eastus2",
  "managedBy": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroupVM/providers/Microsoft.Compute/virtualMachines/myVM",
  "name": "myVM_osdisk_761c570dab",
  "osType": "Linux",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroupVM",
  "sku": {
    "name": "Premium_LRS",
    "tier": "Premium"
  },
  "tags": {},
  "timeCreated": "2018-03-05T22:16:06.892752+00:00",
  "type": "Microsoft.Compute/disks",
  "zones": [
    "1"
  ]
}
```

Use o comando [az vm list-ip-adresses](/cli/azure/vm) para retornar o nome do recurso de endereço IP público no *myVM*. Neste exemplo, o nome é armazenado em uma variável usada em uma etapa posterior.

```azurecli
ipaddressname=$(az vm list-ip-addresses -g myResourceGroupVM -n myVM --query "[].virtualMachine.network.publicIpAddresses[].name" -o tsv)
```

Agora você pode obter informações sobre o endereço IP:

```azurecli
az network public-ip show --resource-group myResourceGroupVM --name $ipaddressname
```

A saída mostra que o endereço IP está na mesma região da disponibilidade da VM:

```azurecli
{
  "dnsSettings": null,
  "etag": "W/\"b7ad25eb-3191-4c8f-9cec-c5e4a3a37d35\"",
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroupVM/providers/Microsoft.Network/publicIPAddresses/myVMPublicIP",
  "idleTimeoutInMinutes": 4,
  "ipAddress": "52.174.34.95",
  "ipConfiguration": {
    "etag": null,
    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroupVM/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM",
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

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a criar uma VM em uma zona de disponibilidade. Saiba mais sobre a [disponibilidade](availability.md) de VMs do Azure.




