---
title: Filtrar tráfego - CLI do Azure | Microsoft Docs
description: Neste artigo, você aprenderá como filtrar o tráfego para uma sub-rede, com um grupo de segurança de rede, usando o CLI do Azure.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/30/2018
ms.author: kumud
ms.custom: devx-track-azurecli
ms.openlocfilehash: 57657637afd9e3e527942fb262a085fa4b8b053e
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106066561"
---
# <a name="filter-network-traffic-with-a-network-security-group-using-the-azure-cli"></a>Filtrar tráfego com um grupo de segurança de rede utilizando o CLI do Azure

É possível filtrar o tráfego para entrada e saída de uma sub-rede de rede virtual com um grupo de segurança de rede. Grupos de segurança de rede contêm regras de segurança que filtram o tráfego por endereço IP, porta e protocolo. As regras de segurança são aplicadas aos recursos implantados em uma sub-rede. Neste artigo, você aprenderá como:

* Criar um grupo de segurança de rede e regras de segurança
* Criar uma rede virtual e associar um grupo de segurança de rede a uma sub-rede
* Implantar VMs (máquinas virtuais) em uma sub-rede
* Testar filtros de tráfego

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Este artigo exige a versão 2.0.28 ou posterior da CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="create-a-network-security-group"></a>Criar um grupo de segurança de rede

Um grupo de segurança de rede contém regras de segurança. As regras de segurança especificam uma origem e um destino. Origens e destinos podem ser grupos de segurança de aplicativos.

### <a name="create-application-security-groups"></a>Criar grupos de segurança de aplicativos

Primeiro, crie um grupo de recursos para todos os recursos criados neste artigo com [az group create](/cli/azure/group). O exemplo a seguir cria um grupo de recursos no local *eastus*: 

```azurecli-interactive
az group create \
  --name myResourceGroup \
  --location eastus
```

Crie um grupo de segurança de rede com [az network nsg create](/cli/azure/network/asg). Um grupo de segurança de aplicativos permite agrupar servidores com requisitos de filtragem de porta semelhantes. O exemplo a seguir cria dois grupos de segurança de aplicativos.

```azurecli-interactive
az network asg create \
  --resource-group myResourceGroup \
  --name myAsgWebServers \
  --location eastus

az network asg create \
  --resource-group myResourceGroup \
  --name myAsgMgmtServers \
  --location eastus
```

### <a name="create-a-network-security-group"></a>Criar um grupo de segurança de rede

Crie um grupo de segurança de rede com [az network nsg create](/cli/azure/network/nsg). O exemplo a seguir cria um grupo de segurança de rede nomeado *myNsg*: 

```azurecli-interactive 
# Create a network security group
az network nsg create \
  --resource-group myResourceGroup \
  --name myNsg
```

### <a name="create-security-rules"></a>Criar regras de segurança

Crie uma regra de segurança com [az network nsg rule create](/cli/azure/network/nsg/rule). O exemplo a seguir cria uma regra que permite a entrada de tráfego da Internet para o grupo de segurança de aplicativo *myWebServers* pelas portas 80 e 443:

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsg \
  --name Allow-Web-All \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 100 \
  --source-address-prefix Internet \
  --source-port-range "*" \
  --destination-asgs "myAsgWebServers" \
  --destination-port-range 80 443
```

O exemplo a seguir cria uma regra que permite a entrada de tráfego da Internet para o grupo de segurança de aplicativo *myMgmtServers* pela porta 22:

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsg \
  --name Allow-SSH-All \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 110 \
  --source-address-prefix Internet \
  --source-port-range "*" \
  --destination-asgs "myAsgMgmtServers" \
  --destination-port-range 22
```

Neste artigo, o SSH (porta 22) é exposto à Internet para a VM *myAsgMgmtServers*. Para ambientes de produção, em vez de expor a porta 22 à Internet, é recomendável conectar os recursos do Azure que você quer gerenciar utilizando uma conexão de rede [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [privada](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

Crie a rede virtual com [az network vnet create](/cli/azure/network/vnet). O exemplo a seguir cria uma virtual nomeada *myVirtualNetwork*:

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefixes 10.0.0.0/16
```

Crie uma rede virtual com uma sub-rede com [az network vnet subnet create](/cli/azure/network/vnet/subnet). O exemplo a seguir adiciona uma sub-rede nomeada *mySubnet* à rede virtual e associa o grupo de segurança de rede *myNsg* a ela:

```azurecli-interactive
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name mySubnet \
  --address-prefix 10.0.0.0/24 \
  --network-security-group myNsg
```

## <a name="create-virtual-machines"></a>Criar máquinas virtuais

Crie duas VMs na rede virtual para poder validar a filtragem de tráfego em uma etapa posterior. 

Crie uma VM com [az vm create](/cli/azure/vm). O exemplo a seguir cria uma VM que servirá como um servidor Web. A opção `--asgs myAsgWebServers` faz com que o Azure torne a interface de rede que ele cria para a VM um membro do grupo de segurança do aplicativo *myAsgWebServers*.

A opção `--nsg ""` é especificada para impedir que o Azure crie um grupo de segurança de rede padrão para a interface de rede que ele cria quando cria a VM. Para simplificar este artigo, uma senha é utilizada. Normalmente, chaves são usadas em implantações de produção. Se você usar chaves, será necessário configurar também o encaminhamento de agente SSH para as etapas restantes. Para obter mais informações, consulte a documentação do cliente SSH. Substitua `<replace-with-your-password>` no comando a seguir com uma senha de sua escolha.

```azurecli-interactive
adminPassword="<replace-with-your-password>"

az vm create \
  --resource-group myResourceGroup \
  --name myVmWeb \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet mySubnet \
  --nsg "" \
  --asgs myAsgWebServers \
  --admin-username azureuser \
  --admin-password $adminPassword
```

A VM demora alguns minutos para criar. Depois que a VM é criada, é retornada uma saída semelhante ao exemplo a seguir: 

```output
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVmWeb",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.90.242.231",
  "resourceGroup": "myResourceGroup"
}
```

Anote o **publicIpAddress**. Esse endereço será usado para acessar a VM da internet em uma etapa posterior.  Crie uma VM para servir como um servidor de gerenciamento:

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmMgmt \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet mySubnet \
  --nsg "" \
  --asgs myAsgMgmtServers \
  --admin-username azureuser \
  --admin-password $adminPassword
```

A VM demora alguns minutos para criar. Depois que a VM é criada, anote o **publicIpAddress** na saída retornada. Esse endereço é usado para acessar a VM na próxima etapa. Não prossiga para a próxima etapa até que o Azure tenha concluído a criação da VM.

## <a name="test-traffic-filters"></a>Testar filtros de tráfego

Use o comando a seguir para criar uma sessão SSH com a VM *myVmMgmt*. Substitua *\<publicIpAddress>* pelo endereço IP público de sua VM. No exemplo acima, o endereço IP é *13.90.242.231*.

```bash 
ssh azureuser@<publicIpAddress>
```

Quando uma senha for solicitada, insira a senha inserida em [Criar VMs](#create-virtual-machines).

É uma conexão com êxito porque a porta 22 tem permissão de entrada da Internet para o grupo de segurança de aplicativo *myAsgMgmtServers* em que o adaptador de rede conectado à VM *myVmMgmt* está.

Utilize o comando a seguir para SSH para a VM *myVmWeb* da VM *myVmMgmt*:

```bash 
ssh azureuser@myVmWeb
```

É uma conexão com êxito porque uma regra de segurança padrão dentro de cada grupo de segurança de rede permite o tráfego em todas as portas entre todos os endereços IP em uma rede virtual. Não é possível criar um SSH para a VM *myVmWeb* da Internet porque a regra de segurança para *myAsgWebServers* não permite a entrada da porta 22 da Internet.

Use os comandos a seguir para instalar o servidor Web nginx na VM *myVmWeb*:

```bash 
# Update package source
sudo apt-get -y update

# Install NGINX
sudo apt-get -y install nginx
```

A VM *myVmWeb* tem permissão de saída para a Internet para recuperar nginx, pois uma regra de segurança padrão permite todo o tráfego de saída para a Internet. Saída da sessão SSH *myVmWeb*, o que deixa você no prompt `username@myVmMgmt:~$` da VM *myVmMgmt*. Para recuperar a tela de boas-vindas do nginx da VM *myVmWeb*, insira o comando a seguir:

```bash
curl myVmWeb
```

Faça logoff da VM *myVmMgmt*. Para confirmar que você pode acessar o servidor da Web *myVmWeb* de fora do Azure, digite `curl <publicIpAddress>` de seu próprio computador. É uma conexão com êxito porque a porta 80 tem permissão de entrada da Internet para o grupo de segurança de aplicativo *myAsgWebServers* em que o adaptador de rede conectado à VM *myVmWeb* está.

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, use [az group delete](/cli/azure/group) para remover o grupo de recursos e todos os recursos que ele contém.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você criou um grupo de segurança de rede e o associou a uma sub-rede de rede virtual. Para saber mais sobre grupos de segurança de rede, consulte [Visão geral do grupo de segurança de rede](./network-security-groups-overview.md) e [Gerenciar um grupo de segurança de rede](manage-network-security-group.md).

O Azure roteia o tráfego entre sub-redes por padrão. Em vez disso, é possível escolher rotear o tráfego entre sub-redes por meio de uma VM, servindo como um firewall, por exemplo. Para saber mais, consulte [Criar uma tabela de roteamentos](tutorial-create-route-table-cli.md).