---
title: Balancear o tráfego de VMs para HA - Azure PowerShell
description: Exemplo de Script do Azure PowerShell – balancear carga do tráfego de VMs para alta disponibilidade
services: load-balancer
documentationcenter: load-balancer
author: asudbring
manager: KumudD
ms.service: load-balancer
ms.devlang: powershell
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 05/16/2017
ms.author: allensu
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: eff2a4a0dd0b1541639de0126d0dd7c5c8dddd10
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98234096"
---
# <a name="load-balance-traffic-to-vms-for-high-availability"></a>Balancear o tráfego de VMs para alta disponibilidade

Este exemplo de script cria todos os componentes necessários para executar várias máquinas virtuais do Windows com uma configuração de alta disponibilidade e com balanceamento de carga. Após a execução do script, você terá três máquinas virtuais, associadas a um Conjunto de Disponibilidade do Azure, e acessíveis por meio de um Azure Load Balancer.

Se necessário, instale o Azure PowerShell usando a instrução encontrada no [guia do Azure PowerShell](/powershell/azure/) e, em seguida, execute `Connect-AzAccount` para criar uma conexão com o Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemplo de script

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-vm-nlb/create-vm-nlb.ps1 "Quick Create VM")]

## <a name="clean-up-deployment"></a>Limpar a implantação 

Execute o comando a seguir para remover o grupo de recursos, a VM e todos os recursos relacionados.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Explicação sobre o script

Esse script usa os seguintes comandos para criar um grupo de recursos, uma máquina virtual, um conjunto de disponibilidade, um balanceador de carga e todos os recursos relacionados. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Cria uma configuração de sub-rede. Essa configuração é usada com o processo de criação de rede virtual. |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Cria uma sub-rede e uma rede virtual do Azure. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)  | Cria um endereço IP público com um endereço IP estático e um nome DNS associado. |
| [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer)  | Cria um Azure Load Balancer. |
| [New-AzLoadBalancerProbeConfig](/powershell/module/az.network/new-azloadbalancerprobeconfig) | Cria uma investigação do balanceador de carga. Uma investigação do balanceador de carga é usada para monitorar cada VM no conjunto de balanceadores de carga. Se qualquer VM ficar inacessível, o tráfego não é roteado para a máquina virtual. |
| [New-AzLoadBalancerRuleConfig](/powershell/module/az.network/new-azloadbalancerruleconfig) | Cria uma regra do balanceador de carga. Neste exemplo, uma regra é criada para a porta 80. Conforme o tráfego HTTP chega ao balanceador de carga, ele é roteado para a porta 80, uma das VMs no conjunto de balanceadores de carga. |
| [New-AzLoadBalancerInboundNatRuleConfig](/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig) | Cria uma regra de conversão de endereços de rede (NAT) do balanceador de carga.  Regras de NAT mapeiam uma porta do balanceador de carga para uma porta em uma VM. Neste exemplo, é criada uma regra de NAT para o tráfego SSH para cada VM no conjunto de balanceadores de carga.  |
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Cria um grupo de segurança de rede (NSG), que é um limite de segurança entre a Internet e a máquina virtual. |
| [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) | Cria uma regra NSG para permitir o tráfego de entrada. Neste exemplo, a porta 22 é aberta para o tráfego SSH. |
| [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) | Cria uma placa de rede virtual e a anexa à rede virtual, à sub-rede e ao NSG. |
| [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset) | Cria um conjunto de disponibilidade. Conjuntos de disponibilidade garantem o funcionamento de aplicativos, distribuindo as máquinas virtuais em recursos físicos, de modo que, se ocorrer uma falha, todo o conjunto não é afetado. |
| [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig) | Cria uma configuração de VM. Essa configuração inclui informações como nome da VM, sistema operacional e credenciais administrativas. A configuração é usada durante a criação da VM. |
| [New-AzVM](/powershell/module/az.compute/new-azvm)  | Cria a máquina virtual e a conecta a placa de rede, a rede virtual, a sub-rede e o NSG. Este comando também especifica a imagem da máquina virtual a ser usada e as credenciais administrativas.  |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o Azure PowerShell, confira a [Documentação do Azure PowerShell](/powershell/azure/).

Exemplos adicionais de script de PowerShell de rede podem ser encontrados na [Documentação de visão geral da rede do Azure](../powershell-samples.md?toc=%2fazure%2fnetworking%2ftoc.json).