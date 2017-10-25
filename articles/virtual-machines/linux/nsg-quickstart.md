---
title: Abrir portas para uma VM do Linux com a CLI 2.0 do Azure | Microsoft Docs
description: "Saiba como abrir uma porta e criar um ponto de extremidade para a VM Linux usando o modelo de implantação do Azure Resource Manager e a CLI 2.0 do Azure"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: eef9842b-495a-46cf-99a6-74e49807e74e
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/21/2017
ms.author: iainfou
ms.openlocfilehash: d176187fe465264b5f433260de5178b48ca9dd4a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="open-ports-and-endpoints-to-a-linux-vm-with-the-azure-cli"></a>Abrir portas e pontos de extremidade para uma VM Linux com a CLI do Azure
No Azure, você abre uma porta, ou cria um ponto de extremidade, para uma VM (máquina virtual) criando um filtro de rede ou uma sub-rede ou interface de rede de VM. Coloque os filtros, que controlam o tráfego de entrada e saída, em um Grupo de Segurança de Rede anexado ao recurso que recebe o tráfego. Vamos usar um exemplo comum de tráfego da Web na porta 80. Este artigo mostra como abrir uma porta para uma VM usando a CLI 2.0 do Azure. Você também pode executar essas etapas com a [CLI do Azure 1.0](nsg-quickstart-nodejs.md).


## <a name="quick-commands"></a>Comandos rápidos
Para criar regras e um Grupo de Segurança de Rede, é necessário ter a última [CLI 2.0 do Azure](/cli/azure/install-az-cli2) instalada e conectada a uma conta do Azure usando [az login](/cli/azure/#login).

Nos exemplos a seguir, substitua os nomes de parâmetro de exemplo com seus próprios valores. Os nomes de parâmetro de exemplo incluem *myResourceGroup*, *myNetworkSecurityGroup* e *myVnet*.

Crie o grupo de segurança de rede com [az network nsg create](/cli/azure/network/nsg#create). O exemplo a seguir cria um grupo de segurança de rede chamado *myNetworkSecurityGroup* na localização *eastus*:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNetworkSecurityGroup
```

Adicione uma regra com [az network nsg rule create](/cli/azure/network/nsg/rule#create) para permitir o tráfego HTTP para o servidor Web (ou faça ajustes para seu próprio cenário, como acesso ao SSH ou conectividade de banco de dados). O exemplo a seguir cria uma regra chamada *myNetworkSecurityGroupRule* para permitir o tráfego TCP na porta 80:

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRule \
    --protocol tcp \
    --priority 1000 \
    --destination-port-range 80
```

Associe o Grupo de Segurança de Rede à NIC (adaptador de rede) da VM com [az network nic update](/cli/azure/network/nic#update). O exemplo a seguir associa uma NIC existente chamada *myNic* ao Grupo de Segurança de Rede chamado *myNetworkSecurityGroup*:

```azurecli
az network nic update \
    --resource-group myResourceGroup \
    --name myNic \
    --network-security-group myNetworkSecurityGroup
```

Como alternativa, você pode associar o Grupo de Segurança de Rede a uma sub-rede de uma rede virtual com [az network vnet subnet update](/cli/azure/network/vnet/subnet#update) em vez de apenas ao adaptador de rede em uma única VM. O exemplo a seguir associa uma sub-rede existente chamada *mySubnet* na rede virtual *myVnet* ao Grupo de Segurança de Rede chamado *myNetworkSecurityGroup*:

```azurecli
az network vnet subnet update \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnet \
    --network-security-group myNetworkSecurityGroup
```

## <a name="more-information-on-network-security-groups"></a>Mais informações sobre os Grupos de Segurança de Rede
Os comandos rápidos aqui permitem que você coloque tudo em funcionamento com o tráfego que flui para sua VM. Os Grupos de Segurança de Rede fornecem muitos recursos excelentes e granularidade para controlar o acesso aos recursos. Você pode ler mais sobre a [criação de um Grupo de Segurança de Rede e as regras ACL aqui](tutorial-virtual-network.md#secure-network-traffic).

Para aplicativos Web altamente disponíveis, você deve colocar suas VMs atrás de um balanceador de carga do Azure. O balanceador de carga distribui o tráfego para VMs, com um Grupo de Segurança de rede que fornece filtragem. Para saber mais, veja [Como balancear a carga de máquinas virtuais Linux no Azure para criar um aplicativo altamente disponível](tutorial-load-balancer.md).

## <a name="next-steps"></a>Próximas etapas
Neste exemplo, você criou uma regra simples para permitir o tráfego HTTP. Você pode encontrar informações sobre a criação de ambientes mais detalhados nos seguintes artigos:

* [Visão geral do Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md)
* [O que é um NSG (grupo de segurança de rede)?](../../virtual-network/virtual-networks-nsg.md)
