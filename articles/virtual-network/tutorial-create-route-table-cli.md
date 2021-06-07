---
title: Rotear tráfego - CLI do Azure | Microsoft Docs
description: Neste artigo, saiba como rotear tráfego com uma tabela de rotas utilizando a CLI do Azure.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: kumud
ms.custom: devx-track-azurecli
ms.openlocfilehash: 2ff643c39820fa529c8678c7a36881dd25da354c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762489"
---
# <a name="route-network-traffic-with-a-route-table-using-the-azure-cli"></a>Rotear tráfego com uma tabela de rotas utilizando a CLI do Azure

Por padrão, o Azure roteia automaticamente o tráfego entre todas as sub-redes dentro de uma rede virtual. É possível criar as próprias rotas para substituir o roteamento padrão do Azure. A capacidade de criar rotas personalizadas será útil, por exemplo, se você quiser rotear o tráfego entre sub-redes por meio de uma NVA (solução de virtualização de rede). Neste artigo, você aprenderá como:

* Criar uma tabela de rotas
* Criar uma rota
* Criar uma rede virtual com várias sub-redes
* Associar uma tabela de rotas a uma sub-rede
* Criar uma NVA que roteia o tráfego
* Implantar VMs (máquinas virtuais) em diferentes sub-redes
* Rotear o tráfego de uma sub-rede para outra por meio de uma NVA

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Este artigo exige a versão 2.0.28 ou posterior da CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="create-a-route-table"></a>Criar uma tabela de rotas

Antes que você possa criar uma tabela de rotas, crie um grupo de recursos com [az group create](/cli/azure/group) para todos os recursos criados neste artigo. 

```azurecli-interactive
# Create a resource group.
az group create \
  --name myResourceGroup \
  --location eastus
```

Crie uma tabela de rotas com [az network route-table create](/cli/azure/network/route-table#az_network_route_table_create). O exemplo a seguir cria uma tabela de rotas nomeada *myRouteTablePublic*. 

```azurecli-interactive
# Create a route table
az network route-table create \
  --resource-group myResourceGroup \
  --name myRouteTablePublic
```

## <a name="create-a-route"></a>Criar uma rota

Crie uma rota na tabela de rotas com [az network route-table route create](/cli/azure/network/route-table/route#az_network_route_table_route_create). 

```azurecli-interactive
az network route-table route create \
  --name ToPrivateSubnet \
  --resource-group myResourceGroup \
  --route-table-name myRouteTablePublic \
  --address-prefix 10.0.1.0/24 \
  --next-hop-type VirtualAppliance \
  --next-hop-ip-address 10.0.2.4
```

## <a name="associate-a-route-table-to-a-subnet"></a>Associar uma tabela de rotas a uma sub-rede

Antes de poder associar uma tabela de rotas a uma sub-rede, será necessário criar uma rede virtual e uma sub-rede. Crie uma rede virtual com uma sub-rede com [az network vnet create](/cli/azure/network/vnet).

```azurecli-interactive
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefix 10.0.0.0/16 \
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24
```

Crie duas sub-redes adicionais com [az network vnet subnet create](/cli/azure/network/vnet/subnet).

```azurecli-interactive
# Create a private subnet.
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --address-prefix 10.0.1.0/24

# Create a DMZ subnet.
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name DMZ \
  --address-prefix 10.0.2.0/24
```

Associe a tabela de rotas *myRouteTablePublic* à sub-rede *Pública* com [az network vnet subnet update](/cli/azure/network/vnet/subnet).

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --name Public \
  --resource-group myResourceGroup \
  --route-table myRouteTablePublic
```

## <a name="create-an-nva"></a>Criar uma NVA

Uma NVA é uma VM que executa uma função de rede, como roteamento, firewall ou otimização de WAN.

Criar uma NVA na sub-rede *DMZ* com [az vm create](/cli/azure/vm). Quando você cria uma VM, o Azure cria e atribui um endereço IP público para a VM, por padrão. O parâmetro `--public-ip-address ""` instrui o Azure para não criar e atribuir um endereço IP público para a VM, pois a VM não precisa estar conectada à internet. Se as chaves SSH ainda não existirem em uma localização de chave padrão, o comando criará. Para usar um conjunto específico de chaves, use a opção `--ssh-key-value`.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmNva \
  --image UbuntuLTS \
  --public-ip-address "" \
  --subnet DMZ \
  --vnet-name myVirtualNetwork \
  --generate-ssh-keys
```

A VM demora alguns minutos para criar. Não prossiga para a próxima etapa até que o Azure termine de criar a VM e retorne a saída sobre a VM. 

Para uma interface de rede poder encaminhar o tráfego de rede enviado a ela, que não seja destinado a seu próprio endereço IP, o encaminhamento de IP deve ser habilitado para a interface de rede. Ative o encaminhamento de IP para a interface de rede com [az network nic update](/cli/azure/network/nic).

```azurecli-interactive
az network nic update \
  --name myVmNvaVMNic \
  --resource-group myResourceGroup \
  --ip-forwarding true
```

Dentro da VM, o sistema operacional, ou um aplicativo em execução na VM, também pode ser capaz de encaminhar o tráfego. Habilitar o encaminhamento de IP no sistema de operacional da VM com [az vm extension set](/cli/azure/vm/extension):

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVmNva \
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings '{"commandToExecute":"sudo sysctl -w net.ipv4.ip_forward=1"}'
```

O comando pode demorar até um minuto para executar.

## <a name="create-virtual-machines"></a>Criar máquinas virtuais

Crie duas VMs na rede virtual para que seja possível validar esse tráfego da sub-rede *Pública* roteada para a sub-rede *Privada* por meio da NVA em uma etapa posterior. 

Crie uma VM na sub-rede *Pública* com [az vm create](/cli/azure/vm). O parâmetro `--no-wait` permite que o Azure execute o comando em segundo plano para que você possa continuar com o próximo comando. Para simplificar este artigo, uma senha é utilizada. Normalmente, chaves são usadas em implantações de produção. Se você utilizar chaves, também deverá configurar o encaminhamento de agente SSH. Para obter mais informações, consulte a documentação do cliente SSH. Substitua `<replace-with-your-password>` no comando a seguir com uma senha de sua escolha.

```azurecli-interactive
adminPassword="<replace-with-your-password>"

az vm create \
  --resource-group myResourceGroup \
  --name myVmPublic \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Public \
  --admin-username azureuser \
  --admin-password $adminPassword \
  --no-wait
```

Crie uma VM na sub-rede *Privada*.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmPrivate \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Private \
  --admin-username azureuser \
  --admin-password $adminPassword
```

A VM demora alguns minutos para criar. Quando a VM estiver criada, a CLI do Azure mostra informações semelhantes ao exemplo a seguir: 

```output
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVmPrivate",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.1.4",
  "publicIpAddress": "13.90.242.231",
  "resourceGroup": "myResourceGroup"
}
```

Anote o **publicIpAddress**. Esse endereço será usado para acessar a VM da internet em uma etapa posterior.

## <a name="route-traffic-through-an-nva"></a>Rotear o tráfego por meio de uma NVA

Use o comando a seguir para criar uma sessão SSH com a VM *myVmPrivate*. Substitua *\<publicIpAddress>* pelo endereço IP público de sua VM. No exemplo acima, o endereço IP é *13.90.242.231*.

```bash
ssh azureuser@<publicIpAddress>
```

Quando uma senha for solicitada, insira a senha que você selecionou em [Criar máquinas virtuais](#create-virtual-machines).

Utilize o comando a seguir para instalar o rastreamento de rotas na VM *myVmPrivate*:

```bash
sudo apt-get install traceroute
```

Utilize o comando a seguir para testar o roteamento do tráfego para a VM *myVmPublic* da VM *myVmPrivate*.

```bash
traceroute myVmPublic
```

A resposta é semelhante ao seguinte exemplo:

```output
traceroute to myVmPublic (10.0.0.4), 30 hops max, 60 byte packets
1  10.0.0.4 (10.0.0.4)  1.404 ms  1.403 ms  1.398 ms
```

Você pode ver que o tráfego é roteado diretamente da VM *myVmPrivate* para a VM *myVmPublic*. As rotas padrão do Azure roteiam o tráfego diretamente entre sub-redes. 

Utilize o comando a seguir para SSH para a VM *myVmPublic* da VM *myVmPrivate*:

```bash
ssh azureuser@myVmPublic
```

Utilize o comando a seguir para instalar o rastreamento de rotas na VM *myVmPublic*:

```bash
sudo apt-get install traceroute
```

Utilize o comando a seguir para testar o roteamento do tráfego para a VM *myVmPrivate* da VM *myVmPublic*.

```bash
traceroute myVmPrivate
```

A resposta é semelhante ao seguinte exemplo:

```output
traceroute to myVmPrivate (10.0.1.4), 30 hops max, 60 byte packets
1  10.0.2.4 (10.0.2.4)  0.781 ms  0.780 ms  0.775 ms
2  10.0.1.4 (10.0.0.4)  1.404 ms  1.403 ms  1.398 ms
```

Você pode ver que o primeiro salto é 10.0.2.4, que é o endereço IP privado da NVA. O segundo salto é 10.0.1.4, o endereço IP privado da VM *myVmPrivate*. A rota adicionada à tabela de rotas *myRouteTablePublic* e associada à sub-rede *Pública* fez o Azure encaminhar o tráfego através de NVA, em vez de diretamente para a sub-rede *Privada*.

Feche as sessões SSH para as VMs *myVmPublic* e *myVmPrivate*.

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, use [az group delete](/cli/azure/group) para remover o grupo de recursos e todos os recursos que ele contém.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você criou uma tabela de rotas e associou-a a uma sub-rede. Você criou uma NVA simples que roteou o tráfego de uma sub-rede pública para uma sub-rede privada. Implante uma variedade de NVAs pré-configuradas que executem funções de rede, como firewall e otimização de WAN pelo [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking). Para saber mais sobre roteamento, consulte [Visão geral de roteamento](virtual-networks-udr-overview.md) e [Gerenciar uma tabela de rotas](manage-route-table.md).

Embora seja possível implantar muitos recursos do Azure dentro de uma rede virtual, os recursos para alguns serviços PaaS do Azure não podem ser implantados em uma rede virtual. Ainda assim, é possível restringir acesso aos recursos de alguns serviços PaaS do Azure somente para tráfego de uma sub-rede de rede virtual. Para saber como, veja [Restringir o acesso à rede para recursos PaaS](tutorial-restrict-network-access-to-resources-cli.md).
