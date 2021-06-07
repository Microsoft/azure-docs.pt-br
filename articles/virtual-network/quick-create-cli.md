---
title: Criar uma rede virtual – início rápido – CLI do Azure
titlesuffix: Azure Virtual Network
description: Neste início rápido, aprenda a criar uma rede virtual usando a CLI do Azure. A rede virtual permite que os recursos do Azure se comuniquem entre si e com a Internet.
author: KumudD
ms.service: virtual-network
ms.topic: quickstart
ms.date: 03/06/2021
ms.author: kumud
ms.custom: devx-track-azurecli
ms.openlocfilehash: 407207c0dcb6270f08fb511a01e6e4e835b9fab9
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776745"
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-cli"></a>Início Rápido: Criar uma rede virtual usando a CLI do Azure

Uma rede virtual permite que recursos do Azure, como VMs (máquinas virtuais), comuniquem-se em modo privado e com a Internet. 

Neste início rápido, você aprende como criar uma rede virtual. Após criar uma rede virtual, você implantará duas VMs na rede virtual. Você fará a conexão com as VMs usando a Internet e se comunicará de modo privado na nova rede virtual.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Este guia de início rápido requer a versão 2.0.28 ou posterior da CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="create-a-resource-group-and-a-virtual-network"></a>Crie um grupo de recursos e uma rede virtual

Antes de poder criar uma rede virtual, você deverá criar um grupo de recursos para hospedá-la. Crie um grupo de recursos com [az group create](/cli/azure/group#az_group_create). Este exemplo cria um grupo de recursos chamado **CreateVNetQS-rg** na localização **Eastus**:

```azurecli-interactive
az group create \
    --name CreateVNetQS-rg \
    --location eastus
```

Crie a rede virtual com [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create). O exemplo cria uma rede virtual padrão chamada **myVNet** com uma sub-rede chamada **default**:

```azurecli-interactive
az network vnet create \
  --name myVNet \
  --resource-group CreateVNetQS-rg \
  --subnet-name default
```

## <a name="create-virtual-machines"></a>Criar máquinas virtuais

Crie duas VMs na rede virtual.

### <a name="create-the-first-vm"></a>Criar a primeira VM

Crie uma VM com [az vm create](/cli/azure/vm#az_vm_create). 

Se as chaves SSH ainda não existirem em uma localização de chave padrão, o comando as criará. Para usar um conjunto específico de chaves, use a opção `--ssh-key-value`. 

A opção `--no-wait` cria a VM em segundo plano. Você pode continuar para a próxima etapa. 

Esse exemplo cria uma VM chamada **myVM1**:

```azurecli-interactive
az vm create \
  --resource-group CreateVNetQS-rg \
  --name myVM1 \
  --image UbuntuLTS \
  --generate-ssh-keys \
  --public-ip-address myPublicIP-myVM1 \
  --no-wait
```

### <a name="create-the-second-vm"></a>Criar a segunda VM

Você usou a opção `--no-wait` na etapa anterior. Continue e crie a segunda VM chamada **myVM2**.

```azurecli-interactive
az vm create \
  --resource-group CreateVNetQS-rg \
  --name myVM2 \
  --image UbuntuLTS \
  --public-ip-address myPublicIP-myVM2 \
  --generate-ssh-keys
```

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

### <a name="azure-cli-output-message"></a>Mensagem de saída da CLI do Azure

As VMs podem levar alguns minutos para serem criadas. Depois que o Azure cria as VMs, a CLI do Azure retorna uma saída como esta:

```output
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/CreateVNetQS-rg/providers/Microsoft.Compute/virtualMachines/myVM2",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.5",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "CreateVNetQS-rg"
  "zones": ""
}
```

## <a name="vm-public-ip"></a>IP da VM

Para obter o endereço IP **myVM2**, use [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show):

```azurecli-interactive
az network public-ip show \
  --resource-group CreateVNetQS-rg  \
  --name myPublicIP-myVM2 \
  --query ipAddress \
  --output tsv
```

## <a name="connect-to-a-vm-from-the-internet"></a>Conecte uma VM a partir da Internet

Nesse comando, substitua `<publicIpAddress>` pelo endereço IP da VM **myVM2**:

```bash
ssh <publicIpAddress>
```

## <a name="communicate-between-vms"></a>Comunicação entre VMs

Para confirmar a comunicação privada entre as VMs **myVM2** e **myVM1**, insira este comando:

```bash
ping myVM1 -c 4
```

Quatro respostas serão recebidas de *10.0.0.4*.

Encerre a sessão SSH com a VM **myVM2**.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não for mais necessário, você poderá usar [az group delete](/cli/azure/group#az_group_delete) para remover o grupo de recursos e todos os recursos que ele contém:

```azurecli-interactive
az group delete \
    --name CreateVNetQS-rg \
    --yes
```

## <a name="next-steps"></a>Próximas etapas

Neste início rápido: 

* Você criou uma rede virtual padrão e duas VMs. 
* Você se conectou a uma VM pela Internet e executou uma comunicação entre duas VMs em modo privado.

A comunicação privada entre as VMs é irrestrita em uma rede virtual. 

Prossiga para o próximo artigo para aprender a configurar diferentes tipos de comunicações de rede de VMs:
> [!div class="nextstepaction"]
> [Filtrar tráfego de rede](tutorial-filter-network-traffic.md)
