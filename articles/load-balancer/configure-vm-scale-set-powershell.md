---
title: Configurar conjunto de dimensionamento de máquinas virtuais com um Azure Load Balancer existente-Azure PowerShell
description: Saiba como configurar um conjunto de dimensionamento de máquinas virtuais com um Azure Load Balancer existente.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: how-to
ms.date: 03/26/2020
ms.openlocfilehash: 919883025075ca38bcef6c23ac744528e7bd5502
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "88510055"
---
# <a name="configure-a-virtual-machine-scale-set-with-an-existing-azure-load-balancer-using-azure-powershell"></a>Configurar um conjunto de dimensionamento de máquinas virtuais com um Azure Load Balancer existente usando Azure PowerShell

Neste artigo, você aprenderá a configurar um conjunto de dimensionamento de máquinas virtuais com um Azure Load Balancer existente.

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Azure.
- Um balanceador de carga de SKU padrão existente na assinatura em que o conjunto de dimensionamento de máquinas virtuais será implantado.
- Uma rede virtual do Azure para o conjunto de dimensionamento de máquinas virtuais.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure-cli"></a>Entrar na CLI do Azure

Entre no Azure.

```azurepowershell-interactive
Connect-AzAccount
```

## <a name="deploy-a-virtual-machine-scale-set-with-existing-load-balancer"></a>Implantar um conjunto de dimensionamento de máquinas virtuais com o balanceador de carga existente

Substitua os valores entre colchetes pelos nomes dos recursos em sua configuração.

```azurepowershell-interactive

$rsg = <resource-group>
$loc = <location>
$vms = <vm-scale-set-name>
$vnt = <virtual-network>
$sub = <subnet-name>
$lbn = <load-balancer-name>
$pol = <upgrade-policy-mode>

$lb = Get-AzLoadBalancer -ResourceGroupName $rsg -Name $lbn

New-AzVmss -ResourceGroupName $rsg -Location $loc -VMScaleSetName $vms -VirtualNetworkName $vnt -SubnetName $sub -LoadBalancerName $lb -UpgradePolicyMode $pol

```

O exemplo abaixo implanta um conjunto de dimensionamento de máquinas virtuais com:

- Conjunto de dimensionamento de máquinas virtuais chamado **myVMSS**
- Azure Load Balancer chamado **myLoadBalancer**
- Pool de back-end do balanceador de carga chamado **myBackendPool**
- Rede virtual do Azure chamada **myVnet**
- Sub-rede chamada **Mysubnet**
- Grupo de recursos chamado **MyResource** Group

```azurepowershell-interactive

$rsg = "myResourceGroup"
$loc = "East US 2"
$vms = "myVMSS"
$vnt = "myVnet"
$sub = "mySubnet"
$pol = "Automatic"
$lbn = "myLoadBalancer"

$lb = Get-AzLoadBalancer -ResourceGroupName $rsg -Name $lbn

New-AzVmss -ResourceGroupName $rsg -Location $loc -VMScaleSetName $vms -VirtualNetworkName $vnt -SubnetName $sub -LoadBalancerName $lb -UpgradePolicyMode $pol
```
> [!NOTE]
> Depois que o conjunto de dimensionamento tiver sido criado, a porta de back-end não poderá ser modificada para uma regra de balanceamento de carga usada por uma investigação de integridade do balanceador de carga. Para alterar a porta, você pode remover a investigação de integridade atualizando o conjunto de dimensionamento de máquinas virtuais do Azure, atualizar a porta e, em seguida, configurar a investigação de integridade novamente.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você implantou um conjunto de dimensionamento de máquinas virtuais com um Azure Load Balancer existente.  Para saber mais sobre conjuntos de dimensionamento de máquinas virtuais e balanceador de carga, confira:

- [O que é o Azure Load Balancer?](load-balancer-overview.md)
- [O que são conjuntos de escala de máquina virtual?](../virtual-machine-scale-sets/overview.md)
