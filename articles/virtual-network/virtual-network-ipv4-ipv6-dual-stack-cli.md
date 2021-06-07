---
title: Implantar aplicativo IPv6 dual stack-CLI básica Load Balancer
titlesuffix: Azure Virtual Network
description: Saiba como implantar um aplicativo de pilha dupla (IPv4 + IPv6) com o Load Balancer básico usando CLI do Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: e9bb0f499e8df712107a6fcdbff14ee367a02bf4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98934166"
---
# <a name="deploy-an-ipv6-dual-stack-application-using-basic-load-balancer---cli"></a>Implantar um aplicativo de pilha dupla IPv6 usando a CLI do Load Balancer básica

Este artigo mostra como implantar um aplicativo de pilha dupla (IPv4 + IPv6) com o Load Balancer básico usando CLI do Azure que inclui uma rede virtual de pilha dupla com uma sub-rede de pilha dupla, um Load Balancer básico com configurações de front-end dual (IPv4 + IPv6), VMs com NICs que têm uma configuração de IP dupla, regras de grupo de segurança de rede duplas e IPs públicos duplos.

Para implantar um aplicativo de pilha dupla (IPV4 + IPv6) usando Standard Load Balancer, consulte [implantar um aplicativo de pilha dupla IPv6 com Standard Load Balancer usando CLI do Azure](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-cli.md).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Este artigo requer a versão 2.0.49 ou posterior do CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Antes de criar sua rede virtual de pilha dupla, você deve criar um grupo de recursos com [AZ Group Create](/cli/azure/group). O exemplo a seguir cria um grupo de recursos chamado *DsResourceGroup01* no local *eastus* :

```azurecli-interactive
az group create \
--name DsResourceGroup01 \
--location eastus
```

## <a name="create-ipv4-and-ipv6-public-ip-addresses-for-load-balancer"></a>Criar endereços IP públicos IPv4 e IPv6 para o balanceador de carga
Para acessar seus pontos de extremidade IPv4 e IPv6 na Internet, você precisa de endereços IP públicos IPv4 e IPv6 para o balanceador de carga. Crie um endereço IP público com [az network public-ip create](/cli/azure/network/public-ip). O exemplo a seguir cria o endereço IP público IPv4 e IPv6 chamado *dsPublicIP_v4* e *dsPublicIP_v6* no grupo de recursos *DsResourceGroup01* :

```azurecli-interactive
# Create an IPV4 IP address
az network public-ip create \
--name dsPublicIP_v4  \
--resource-group DsResourceGroup01  \
--location eastus  \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv4

# Create an IPV6 IP address
az network public-ip create \
--name dsPublicIP_v6  \
--resource-group DsResourceGroup01  \
--location eastus \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv6

```

## <a name="create-public-ip-addresses-for-vms"></a>Criar endereços IP públicos para VMs

Para acessar remotamente suas VMs na Internet, você precisa de endereços IP públicos IPv4 para as VMs. Crie um endereço IP público com [az network public-ip create](/cli/azure/network/public-ip).

```azurecli-interactive
az network public-ip create \
--name dsVM0_remote_access  \
--resource-group DsResourceGroup01 \
--location eastus  \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv4

az network public-ip create \
--name dsVM1_remote_access  \
--resource-group DsResourceGroup01  \
--location eastus  \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv4
```

## <a name="create-basic-load-balancer"></a>Criar o balanceador de carga básico

Nesta seção, você configurará o IP de front-end duplo (IPv4 e IPv6) e o pool de endereços de back-ends para o balanceador de carga e, em seguida, criará um Load Balancer básico.

### <a name="create-load-balancer"></a>Criar um balanceador de carga

Crie o Load Balancer básico com [AZ Network lb Create](/cli/azure/network/lb) chamado **dsLB** , que inclui um pool de front-end chamado **dsLbFrontEnd_v4**, um pool de back-end chamado **dsLbBackEndPool_v4** que está associado ao endereço IP público IPv4 **dsPublicIP_v4** que você criou na etapa anterior. 

```azurecli-interactive
az network lb create \
--name dsLB  \
--resource-group DsResourceGroup01 \
--sku Basic \
--location eastus \
--frontend-ip-name dsLbFrontEnd_v4  \
--public-ip-address dsPublicIP_v4  \
--backend-pool-name dsLbBackEndPool_v4
```

### <a name="create-ipv6-frontend"></a>Criar front-end IPv6

Crie um IP de front-end IPV6 com [AZ Network lb frontend-IP Create](/cli/azure/network/lb/frontend-ip#az-network-lb-frontend-ip-create). O exemplo a seguir cria uma configuração de IP de front-end chamada *dsLbFrontEnd_v6* e anexa o endereço *dsPublicIP_v6* :

```azurecli-interactive
az network lb frontend-ip create \
--lb-name dsLB  \
--name dsLbFrontEnd_v6  \
--resource-group DsResourceGroup01  \
--public-ip-address dsPublicIP_v6

```

### <a name="configure-ipv6-back-end-address-pool"></a>Configurar o pool de endereços de back-end IPv6

Crie um pool de endereços de back-end IPv6 com [AZ Network lb address-pool Create](/cli/azure/network/lb/address-pool#az-network-lb-address-pool-create). O exemplo a seguir cria um pool de endereços de back-end chamado *dsLbBackEndPool_v6*  para incluir VMs com configurações de NIC IPv6:

```azurecli-interactive
az network lb address-pool create \
--lb-name dsLB  \
--name dsLbBackEndPool_v6  \
--resource-group DsResourceGroup01
```

### <a name="create-a-health-probe"></a>Criar uma investigação de integridade
Crie uma investigação de integridade com [az network lb probe create](/cli/azure/network/lb/probe) para monitorar a integridade das máquinas virtuais. 

```azurecli-interactive
az network lb probe create -g DsResourceGroup01  --lb-name dsLB -n dsProbe --protocol tcp --port 3389
```

### <a name="create-a-load-balancer-rule"></a>Criar uma regra de balanceador de carga

Uma regra de balanceador de carga é usada para definir como o tráfego é distribuído para as VMs. Defina a configuração de IP de front-end para o tráfego de entrada e o pool de IPs de back-end para receber o tráfego, junto com as portas de origem e de destino necessárias. 

Crie uma regra de balanceador de carga com [az network lb rule create](/cli/azure/network/lb/rule#az-network-lb-rule-create). O exemplo a seguir cria regras de balanceador de carga chamadas *dsLBrule_v4* e *dsLBrule_v6* e equilibra o tráfego na porta *TCP* *80* para as configurações de IP de front-end IPv4 e IPv6:

```azurecli-interactive
az network lb rule create \
--lb-name dsLB  \
--name dsLBrule_v4  \
--resource-group DsResourceGroup01  \
--frontend-ip-name dsLbFrontEnd_v4  \
--protocol Tcp  \
--frontend-port 80  \
--backend-port 80  \
--probe-name dsProbe \
--backend-pool-name dsLbBackEndPool_v4


az network lb rule create \
--lb-name dsLB  \
--name dsLBrule_v6  \
--resource-group DsResourceGroup01 \
--frontend-ip-name dsLbFrontEnd_v6  \
--protocol Tcp  \
--frontend-port 80 \
--backend-port 80  \
--probe-name dsProbe \
--backend-pool-name dsLbBackEndPool_v6

```

## <a name="create-network-resources"></a>Criar recursos da rede
Antes de implantar algumas VMs, você deve criar recursos de rede de suporte – conjunto de disponibilidade, grupo de segurança de rede, rede virtual e NICs virtuais. 
### <a name="create-an-availability-set"></a>Criar um conjunto de disponibilidade
Para melhorar a disponibilidade do seu aplicativo, coloque suas VMs em um conjunto de disponibilidade.

Crie um conjunto de disponibilidade com [az vm availability-set create](/cli/azure/vm/availability-set). O exemplo a seguir cria um conjunto de disponibilidade chamado *dsAVset*:

```azurecli-interactive
az vm availability-set create \
--name dsAVset  \
--resource-group DsResourceGroup01  \
--location eastus \
--platform-fault-domain-count 2  \
--platform-update-domain-count 2  
```

### <a name="create-network-security-group"></a>Criar um grupo de segurança de rede

Crie um grupo de segurança de rede para as regras que irão controlar a comunicação de entrada e saída em sua VNET.

#### <a name="create-a-network-security-group"></a>Criar um grupo de segurança de rede

Criar um grupo de segurança de rede com [AZ Network NSG Create](/cli/azure/network/nsg#az-network-nsg-create)


```azurecli-interactive
az network nsg create \
--name dsNSG1  \
--resource-group DsResourceGroup01  \
--location eastus

```

#### <a name="create-a-network-security-group-rule-for-inbound-and-outbound-connections"></a>Criar uma regra de grupo de segurança de rede para conexões de entrada e saída

Crie uma regra de grupo de segurança de rede para permitir conexões RDP por meio da porta 3389, conexão com a Internet pela porta 80 e para conexões de saída com [AZ Network NSG Rule Create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create).

```azurecli-interactive
# Create inbound rule for port 3389
az network nsg rule create \
--name allowRdpIn  \
--nsg-name dsNSG1  \
--resource-group DsResourceGroup01  \
--priority 100  \
--description "Allow Remote Desktop In"  \
--access Allow  \
--protocol "*"  \
--direction Inbound  \
--source-address-prefixes "*"  \
--source-port-ranges "*"  \
--destination-address-prefixes "*"  \
--destination-port-ranges 3389

# Create inbound rule for port 80
az network nsg rule create \
--name allowHTTPIn  \
--nsg-name dsNSG1  \
--resource-group DsResourceGroup01  \
--priority 200  \
--description "Allow HTTP In"  \
--access Allow  \
--protocol "*"  \
--direction Inbound  \
--source-address-prefixes "*"  \
--source-port-ranges 80  \
--destination-address-prefixes "*"  \
--destination-port-ranges 80

# Create outbound rule

az network nsg rule create \
--name allowAllOut  \
--nsg-name dsNSG1  \
--resource-group DsResourceGroup01  \
--priority 300  \
--description "Allow All Out"  \
--access Allow  \
--protocol "*"  \
--direction Outbound  \
--source-address-prefixes "*"  \
--source-port-ranges "*"  \
--destination-address-prefixes "*"  \
--destination-port-ranges "*"
```


### <a name="create-a-virtual-network"></a>Criar uma rede virtual

Crie a rede virtual com [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create). O exemplo a seguir cria uma rede virtual chamada *dsVNET* com sub-redes *dsSubNET_v4* e *dsSubNET_v6*:

```azurecli-interactive
# Create the virtual network
az network vnet create \
--name dsVNET \
--resource-group DsResourceGroup01 \
--location eastus  \
--address-prefixes "10.0.0.0/16" "fd00:db8:deca::/48"

# Create a single dual stack subnet

az network vnet subnet create \
--name dsSubNET \
--resource-group DsResourceGroup01 \
--vnet-name dsVNET \
--address-prefixes "10.0.0.0/24" "fd00:db8:deca:deed::/64" \
--network-security-group dsNSG1
```

### <a name="create-nics"></a>Criar NICs

Crie NICs virtuais para cada VM com [AZ Network NIC Create](/cli/azure/network/nic#az-network-nic-create). O exemplo a seguir cria uma NIC virtual para cada VM. Cada NIC tem duas configurações de IP (1 configuração de IPv4, 1 configuração de IPv6). Você cria a configuração do IPV6 com [AZ Network NIC IP-config Create](/cli/azure/network/nic/ip-config#az-network-nic-ip-config-create).

```azurecli-interactive
# Create NICs
az network nic create \
--name dsNIC0  \
--resource-group DsResourceGroup01 \
--network-security-group dsNSG1  \
--vnet-name dsVNET  \
--subnet dsSubNet  \
--private-ip-address-version IPv4 \
--lb-address-pools dsLbBackEndPool_v4  \
--lb-name dsLB  \
--public-ip-address dsVM0_remote_access

az network nic create \
--name dsNIC1 \
--resource-group DsResourceGroup01 \
--network-security-group dsNSG1 \
--vnet-name dsVNET \
--subnet dsSubNet \
--private-ip-address-version IPv4 \
--lb-address-pools dsLbBackEndPool_v4 \
--lb-name dsLB \
--public-ip-address dsVM1_remote_access

# Create IPV6 configurations for each NIC

az network nic ip-config create \
--name dsIp6Config_NIC0  \
--nic-name dsNIC0  \
--resource-group DsResourceGroup01 \
--vnet-name dsVNET \
--subnet dsSubNet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name dsLB

az network nic ip-config create \
--name dsIp6Config_NIC1 \
--nic-name dsNIC1 \
--resource-group DsResourceGroup01 \
--vnet-name dsVNET \
--subnet dsSubNet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name dsLB
```

### <a name="create-virtual-machines"></a>Criar máquinas virtuais

Crie as VM com [az vm create](/cli/azure/vm#az-vm-create). O exemplo a seguir cria duas VMs e os componentes de rede virtual necessários, caso ainda não existam. 

Crie a máquina virtual *dsVM0* da seguinte maneira:

```azurecli-interactive
 az vm create \
--name dsVM0 \
--resource-group DsResourceGroup01 \
--nics dsNIC0 \
--size Standard_A2 \
--availability-set dsAVset \
--image MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest  
```

Crie a máquina virtual *dsVM1* da seguinte maneira:

```azurecli-interactive
az vm create \
--name dsVM1 \
--resource-group DsResourceGroup01 \
--nics dsNIC1 \
--size Standard_A2 \
--availability-set dsAVset \
--image MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest 
```

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Exibir rede virtual de pilha dupla IPv6 no portal do Azure
Você pode exibir a rede virtual de pilha dupla IPv6 em portal do Azure da seguinte maneira:
1. Na barra de pesquisa do portal, insira *dsVnet*.
2. Quando **myVirtualNetwork** aparecer nos resultados da pesquisa, selecione-o. Isso inicia a página **visão geral** da rede virtual de pilha dupla chamada *dsVnet*. A rede virtual de pilha dupla mostra as duas NICs com as configurações de IPv4 e IPv6 localizadas na sub-rede de pilha dupla chamada *dsSubnet*.

  ![Rede virtual de pilha dupla IPv6 no Azure](./media/virtual-network-ipv4-ipv6-dual-stack-powershell/dual-stack-vnet.png)



## <a name="clean-up-resources"></a>Limpar os recursos

Quando não for mais necessário, você pode usar o comando [az group delete](/cli/azure/group#az-group-delete) para remover o grupo de recursos, a VM e todos os recursos relacionados.

```azurecli-interactive
 az group delete --name DsResourceGroup01
```

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você criou um Load Balancer básico com uma configuração de IP de front-end (IPv4 e IPv6). Você também criou duas máquinas virtuais que incluíam NICs com configurações de IP duplo (IPV4 + IPv6) que foram adicionadas ao pool de back-end do balanceador de carga. Para saber mais sobre o suporte a IPv6 em redes virtuais do Azure, consulte [o que é IPv6 para a rede virtual do Azure?](ipv6-overview.md)
