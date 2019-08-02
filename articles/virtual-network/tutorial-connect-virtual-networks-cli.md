---
title: Conectar redes virtuais com o emparelhamento de rede virtual – CLI do Azure | Microsoft Docs
description: Neste artigo, você aprende a conectar redes virtuais com o emparelhamento de rede virtual, usando a CLI do Azure.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
Customer intent: I want to connect two virtual networks so that virtual machines in one virtual network can communicate with virtual machines in the other virtual network.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: 8e1cf2a1c5503f31a70bc654ae1a211d1ab64581
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67203865"
---
# <a name="connect-virtual-networks-with-virtual-network-peering-using-the-azure-cli"></a>Conectar redes virtuais com o emparelhamento de rede virtual usando a CLI do Azure

Você pode conectar redes virtuais entre si com o emparelhamento de rede virtual. Depois que as redes virtuais são emparelhadas, os recursos de ambas as redes virtuais podem se comunicar entre si, com a mesma latência e largura de banda como se os recursos estivessem na mesma rede virtual. Neste artigo, você aprenderá a:

* Criar duas redes virtuais
* Conectar duas redes virtuais a um emparelhamento de rede virtual
* Implementar uma VM (máquina virtual) em cada rede virtual
* Comunicação entre VMs

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Caso opte por instalar e usar a CLI localmente, este artigo exigirá que seja executada a CLI do Azure versão 2.0.28 ou posterior. Para saber qual é a versão, execute `az --version`. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli). 

## <a name="create-virtual-networks"></a>Criar redes virtuais

Antes de criar uma rede virtual, será necessário criar um grupo de recursos para a rede virtual e todos os outros recursos criados neste artigo. Crie um grupo de recursos com [az group create](/cli/azure/group). O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* no local *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Crie a rede virtual com [az network vnet create](/cli/azure/network/vnet). O exemplo a seguir cria uma rede virtual nomeada *myVirtualNetwork1* com o prefixo de endereço *10.0.0.0/16*.

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork1 \
  --resource-group myResourceGroup \
  --address-prefixes 10.0.0.0/16 \
  --subnet-name Subnet1 \
  --subnet-prefix 10.0.0.0/24
```

Crie uma rede virtual chamada *myVirtualNetwork2* com o prefixo de endereço *10.1.0.0/16*:

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --address-prefixes 10.1.0.0/16 \
  --subnet-name Subnet1 \
  --subnet-prefix 10.1.0.0/24
```

## <a name="peer-virtual-networks"></a>Emparelhar redes virtuais

Os emparelhamentos são estabelecidos entre IDs de rede virtual e, portanto, você precisa primeiro obter a ID de cada rede virtual com o comando [az network vnet show](/cli/azure/network/vnet) e armazenar a ID em uma variável.

```azurecli-interactive
# Get the id for myVirtualNetwork1.
vNet1Id=$(az network vnet show \
  --resource-group myResourceGroup \
  --name myVirtualNetwork1 \
  --query id --out tsv)

# Get the id for myVirtualNetwork2.
vNet2Id=$(az network vnet show \
  --resource-group myResourceGroup \
  --name myVirtualNetwork2 \
  --query id \
  --out tsv)
```

Crie um emparelhamento de *myVirtualNetwork1* para *myVirtualNetwork2* com [az network vnet peering create](/cli/azure/network/vnet/peering). Se o parâmetro `--allow-vnet-access` não for especificado, um emparelhamento será estabelecido, mas nenhuma comunicação poderá fluir nele.

```azurecli-interactive
az network vnet peering create \
  --name myVirtualNetwork1-myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork1 \
  --remote-vnet $vNet2Id \
  --allow-vnet-access
```

Na saída retornada após a execução do comando anterior, você vê que o **peeringState** é *Iniciado*. O emparelhamento permanece no estado *Iniciado* até que você crie o emparelhamento de *myVirtualNetwork2* para *myVirtualNetwork1*. Crie um emparelhamento de *myVirtualNetwork2* para *myVirtualNetwork1*. 

```azurecli-interactive
az network vnet peering create \
  --name myVirtualNetwork2-myVirtualNetwork1 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork2 \
  --remote-vnet $vNet1Id \
  --allow-vnet-access
```

Na saída retornada após a execução do comando anterior, você vê que o **peeringState** é *Conectado*. O Azure também alterou o estado de emparelhamento do emparelhamento *myVirtualNetwork1-myVirtualNetwork2* para *Conectado*. Confirme se o estado de emparelhamento do emparelhamento *myVirtualNetwork1-myVirtualNetwork2* foi alterado para *Conectado* com [az network vnet peering show](/cli/azure/network/vnet/peering).

```azurecli-interactive
az network vnet peering show \
  --name myVirtualNetwork1-myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork1 \
  --query peeringState
```

Os recursos de uma rede virtual não podem se comunicar com os recursos da outra rede virtual até que o **peeringState** dos emparelhamentos de ambas as redes virtuais seja *Conectado*. 

## <a name="create-virtual-machines"></a>Criar máquinas virtuais

Crie uma VM em cada rede virtual para que seja possível comunicar-se entre elas em uma etapa posterior.

### <a name="create-the-first-vm"></a>Criar a primeira VM

Crie uma VM com [az vm create](/cli/azure/vm). O exemplo a seguir cria uma VM nomeada *myVm1* na rede virtual *myVirtualNetwork1*. Se as chaves SSH ainda não existirem em uma localização de chave padrão, o comando criará. Para usar um conjunto específico de chaves, use a opção `--ssh-key-value`. A opção `--no-wait` cria a VM em segundo plano, para que você possa prosseguir para a próxima etapa.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm1 \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork1 \
  --subnet Subnet1 \
  --generate-ssh-keys \
  --no-wait
```

### <a name="create-the-second-vm"></a>Criar a segunda VM

Crie uma VM na rede virtual *myVirtualNetwork2*.

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name myVm2 \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork2 \
  --subnet Subnet1 \
  --generate-ssh-keys
```

A VM demora alguns minutos para criar. Quando a VM estiver criada, a CLI do Azure mostra informações semelhantes ao exemplo a seguir: 

```azurecli 
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVm2",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.1.0.4",
  "publicIpAddress": "13.90.242.231",
  "resourceGroup": "myResourceGroup"
}
```

Anote o **publicIpAddress**. Esse endereço será usado para acessar a VM da internet em uma etapa posterior.

## <a name="communicate-between-vms"></a>Comunicação entre VMs

Use o comando a seguir para criar uma sessão SSH com a VM *myVm2*. Substitua `<publicIpAddress>` pelo endereço IP público de sua VM. No exemplo anterior, o endereço IP público é *13.90.242.231*.

```bash 
ssh <publicIpAddress>
```

Execute o ping da VM em *myVirtualNetwork1*.

```bash 
ping 10.0.0.4 -c 4
```

Você receberá quatro respostas. 

Feche a sessão SSH da VM *myVm2*. 

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, use [az group delete](/cli/azure/group) para remover o grupo de recursos e todos os recursos que ele contém.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a conectar duas redes, na mesma região do Azure, com o emparelhamento de rede virtual. Você também pode emparelhar redes virtuais em diferentes [regiões com suporte](virtual-network-manage-peering.md#cross-region), em [diferentes assinaturas do Azure](create-peering-different-subscriptions.md#cli) e também criar [designs de rede de hub e spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) com emparelhamento. Para saber mais sobre o emparelhamento de rede virtual, consulte [Visão geral de emparelhamento de rede virtual](virtual-network-peering-overview.md) e [Gerenciamento de emparelhamentos de rede virtual](virtual-network-manage-peering.md).

Você pode [conectar seu próprio computador a uma rede virtual](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) por meio de uma VPN e interagir com os recursos de uma rede virtual ou de redes virtuais emparelhadas. Para obter amostras de scripts reutilizáveis para concluir muitas das tarefas abordadas nos artigos sobre redes virtuais, consulte [amostras de script](cli-samples.md).
