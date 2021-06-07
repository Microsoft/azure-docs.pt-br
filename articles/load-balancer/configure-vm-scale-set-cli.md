---
title: Configurar conjunto de dimensionamento de máquinas virtuais com um Azure Load Balancer existente-CLI do Azure
description: Saiba como configurar um conjunto de dimensionamento de máquinas virtuais com um Azure Load Balancer existente usando o CLI do Azure.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: how-to
ms.date: 03/25/2020
ms.openlocfilehash: a60a6889217ce6ca8dccd5ebf5ee74b8f67a7757
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94518202"
---
# <a name="configure-a-virtual-machine-scale-set-with-an-existing-azure-load-balancer-using-the-azure-cli"></a>Configurar um conjunto de dimensionamento de máquinas virtuais com um Azure Load Balancer existente usando o CLI do Azure

Neste artigo, você aprenderá a configurar um conjunto de dimensionamento de máquinas virtuais com um Azure Load Balancer existente.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos 

- Você precisa de um balanceador de carga de SKU padrão existente na assinatura em que o conjunto de dimensionamento de máquinas virtuais será implantado.

- Você precisa de uma rede virtual do Azure para o conjunto de dimensionamento de máquinas virtuais.
 
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Este artigo exige a versão 2.0.28 ou posterior da CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="deploy-a-virtual-machine-scale-set-with-existing-load-balancer"></a>Implantar um conjunto de dimensionamento de máquinas virtuais com o balanceador de carga existente

Substitua os valores entre colchetes pelos nomes dos recursos em sua configuração.

```azurecli-interactive
az vmss create \
    --resource-group <resource-group> \
    --name <vmss-name>\
    --image <your-image> \
    --admin-username <admin-username> \
    --generate-ssh-keys  \
    --upgrade-policy-mode Automatic \
    --instance-count 3 \
    --vnet-name <virtual-network-name> \
    --subnet <subnet-name> \
    --lb <load-balancer-name> \
    --backend-pool-name <backend-pool-name>
```

O exemplo abaixo implanta um conjunto de dimensionamento de máquinas virtuais com:

- Conjunto de dimensionamento de máquinas virtuais chamado **myVMSS**
- Azure Load Balancer chamado **myLoadBalancer**
- Pool de back-end do balanceador de carga chamado **myBackendPool**
- Rede virtual do Azure chamada **myVnet**
- Sub-rede chamada **Mysubnet**
- Grupo de recursos chamado **MyResource** Group
- Imagem do servidor Ubuntu para o conjunto de dimensionamento de máquinas virtuais

```azurecli-interactive
az vmss create \
    --resource-group myResourceGroup \
    --name myVMSS \
    --image Canonical:UbuntuServer:18.04-LTS:latest \
    --admin-username adminuser \
    --generate-ssh-keys \
    --upgrade-policy-mode Automatic \
    --instance-count 3 \
    --vnet-name myVnet\
    --subnet mySubnet \
    --lb myLoadBalancer \
    --backend-pool-name myBackendPool
```
> [!NOTE]
> Depois que o conjunto de dimensionamento tiver sido criado, a porta de back-end não poderá ser modificada para uma regra de balanceamento de carga usada por uma investigação de integridade do balanceador de carga. Para alterar a porta, você pode remover a investigação de integridade atualizando o conjunto de dimensionamento de máquinas virtuais do Azure, atualizar a porta e, em seguida, configurar a investigação de integridade novamente.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você implantou um conjunto de dimensionamento de máquinas virtuais com um Azure Load Balancer existente.  Para saber mais sobre conjuntos de dimensionamento de máquinas virtuais e balanceador de carga, confira:

- [O que é o Azure Load Balancer?](load-balancer-overview.md)
- [O que são conjuntos de escala de máquina virtual?](../virtual-machine-scale-sets/overview.md)
                                