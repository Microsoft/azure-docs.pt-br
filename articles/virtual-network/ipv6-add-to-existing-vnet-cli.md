---
title: Adicionar IPv6 a um aplicativo IPv4 na rede virtual do Azure-CLI do Azure
titlesuffix: Azure Virtual Network
description: Este artigo mostra como implantar endereços IPv6 em um aplicativo existente na rede virtual do Azure usando CLI do Azure.
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
ms.openlocfilehash: 9a321687a755f8a3d6e6d9139138d61c58764ef4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98932610"
---
# <a name="add-ipv6-to-an-ipv4-application-in-azure-virtual-network---azure-cli"></a>Adicionar IPv6 a um aplicativo IPv4 na rede virtual do Azure-CLI do Azure

Este artigo mostra como adicionar endereços IPv6 a um aplicativo que está usando o endereço IP público IPv4 em uma rede virtual do Azure para um Standard Load Balancer usando CLI do Azure. A atualização in-loco inclui uma rede virtual e uma sub-rede, uma Standard Load Balancer com configurações de front-end IPv4 + IPV6, VMs com NICs que têm configurações IPv4 + IPv6, grupo de segurança de rede e IPs públicos.

## <a name="prerequisites"></a>Pré-requisitos

- Este artigo pressupõe que você implantou um Standard Load Balancer conforme descrito em [início rápido: criar um Standard Load Balancer-CLI do Azure](../load-balancer/quickstart-load-balancer-standard-public-cli.md).

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Este artigo exige a versão 2.0.28 ou posterior da CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="create-ipv6-addresses"></a>Criar endereços IPv6

Crie um endereço IPv6 público com [AZ Network Public-IP Create](/cli/azure/network/public-ip) para seu Standard Load Balancer. O exemplo a seguir cria um endereço IP público IPv6 chamado *PublicIP_v6* no grupo de recursos *myResourceGroupSLB* :

```azurecli-interactive
az network public-ip create \
--name PublicIP_v6 \
--resource-group MyResourceGroupSLB \
--location EastUS \
--sku Standard \
--allocation-method static \
--version IPv6
```

## <a name="configure-ipv6-load-balancer-frontend"></a>Configurar front-end do balanceador de carga IPv6

Configure o balanceador de carga com o novo endereço IP IPv6 usando [AZ Network lb frontend-IP Create](/cli/azure/network/lb/frontend-ip#az-network-lb-frontend-ip-create) da seguinte maneira:

```azurecli-interactive
az network lb frontend-ip create \
--lb-name myLoadBalancer \
--name dsLbFrontEnd_v6 \
--resource-group MyResourceGroupSLB \
--public-ip-address PublicIP_v6
```

## <a name="configure-ipv6-load-balancer-backend-pool"></a>Configurar pool de back-end do balanceador de carga IPv6

Crie o pool de back-end para NICs com endereços IPv6 usando [AZ Network lb address-pool Create](/cli/azure/network/lb/address-pool#az-network-lb-address-pool-create) da seguinte maneira:

```azurecli-interactive
az network lb address-pool create \
--lb-name myLoadBalancer \
--name dsLbBackEndPool_v6 \
--resource-group MyResourceGroupSLB
```

## <a name="configure-ipv6-load-balancer-rules"></a>Configurar regras do balanceador de carga IPv6

Crie regras de balanceador de carga IPv6 com [AZ Network lb Rule Create](/cli/azure/network/lb/rule#az-network-lb-rule-create).

```azurecli-interactive
az network lb rule create \
--lb-name myLoadBalancer \
--name dsLBrule_v6 \
--resource-group MyResourceGroupSLB \
--frontend-ip-name dsLbFrontEnd_v6 \
--protocol Tcp \
--frontend-port 80 \
--backend-port 80 \
--backend-pool-name dsLbBackEndPool_v6
```

## <a name="add-ipv6-address-ranges"></a>Adicionar intervalos de endereços IPv6

Adicione intervalos de endereços IPv6 à rede virtual e à sub-rede que hospeda o balanceador de carga da seguinte maneira:

```azurecli-interactive
az network vnet update \
--name myVnet  \
--resource-group MyResourceGroupSLB \
--address-prefixes  "10.0.0.0/16"  "fd00:db8:deca::/48"

az network vnet subnet update \
--vnet-name myVnet \
--name mySubnet \
--resource-group MyResourceGroupSLB \
--address-prefixes  "10.0.0.0/24"  "fd00:db8:deca:deed::/64"  
```

## <a name="add-ipv6-configuration-to-nics"></a>Adicionar configuração IPv6 a NICs

Configure as NICs da VM com um endereço IPv6 usando [AZ Network NIC IP-config Create](/cli/azure/network/nic/ip-config#az-network-nic-ip-config-create) da seguinte maneira:

```azurecli-interactive
az network nic ip-config create \
--name dsIp6Config_NIC1 \
--nic-name myNicVM1 \
--resource-group MyResourceGroupSLB \
--vnet-name myVnet \
--subnet mySubnet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name dsLB

az network nic ip-config create \
--name dsIp6Config_NIC2 \
--nic-name myNicVM2 \
--resource-group MyResourceGroupSLB \
--vnet-name myVnet \
--subnet mySubnet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name myLoadBalancer

az network nic ip-config create \
--name dsIp6Config_NIC3 \
--nic-name myNicVM3 \
--resource-group MyResourceGroupSLB \
--vnet-name myVnet \
--subnet mySubnet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name myLoadBalancer
```

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Exibir rede virtual de pilha dupla IPv6 no portal do Azure

Você pode exibir a rede virtual de pilha dupla IPv6 em portal do Azure da seguinte maneira:
1. Na barra de pesquisa do portal, insira *myVnet*.
2. Quando **myVnet** aparecer nos resultados da pesquisa, selecione-o. Isso inicia a página **visão geral** da rede virtual de pilha dupla chamada *myVNet*. A rede virtual de pilha dupla mostra as três NICs com as configurações de IPv4 e IPv6 localizadas na sub-rede de pilha dupla chamada *mysubnet*.

  ![Rede virtual de pilha dupla IPv6 no Azure](./media/ipv6-add-to-existing-vnet-powershell/ipv6-dual-stack-vnet.png)


## <a name="clean-up-resources"></a>Limpar os recursos

Quando não forem mais necessários, você poderá usar o comando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos, a VM e todos os recursos relacionados.

```azurecli-interactive
az group delete --name MyAzureResourceGroupSLB
```

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você atualizou um Standard Load Balancer existente com uma configuração de IP de front-end IPv4 para uma configuração de pilha dupla (IPv4 e IPv6). Você também adicionou configurações de IPv6 às NICs das VMs no pool de back-end. Para saber mais sobre o suporte a IPv6 em redes virtuais do Azure, consulte [o que é IPv6 para a rede virtual do Azure?](ipv6-overview.md)
