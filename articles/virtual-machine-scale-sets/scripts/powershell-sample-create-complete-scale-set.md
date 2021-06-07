---
title: Exemplos do Azure PowerShell – Criar um conjunto de dimensionamento de máquinas virtuais completo
description: Esse script cria um conjunto de dimensionamento de máquinas virtuais executando o Windows Server 2016, em que os recursos individuais são configurados e criados.
author: mimckitt
ms.author: mimckitt
ms.topic: sample
ms.service: virtual-machine-scale-sets
ms.date: 05/29/2018
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurepowershell
ms.openlocfilehash: 31d779adc0ce08331486d615a6af2095323189bd
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105935459"
---
# <a name="create-a-complete-virtual-machine-scale-set-with-powershell"></a>Criar um conjunto de dimensionamento de máquinas virtuais completo com o PowerShell

Esse script cria um conjunto de dimensionamento de máquinas virtuais que executa o Windows Server 2016. Recursos individuais são configurados e criados, em vez de usar as [opções de criação de recursos internos disponíveis aqui em New-AzVmss](powershell-sample-create-simple-scale-set.md). Depois de executar o script, é possível acessar a VM via RDP.


[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="sample-script"></a>Exemplo de script

[!code-powershell[main](../../../powershell_scripts/virtual-machine-scale-sets/complete-scale-set/complete-scale-set.ps1 "Create a complete virtual machine scale set")]

## <a name="clean-up-deployment"></a>Limpar a implantação
Execute o comando a seguir para remover o grupo de recursos, o conjunto de dimensionamento e todos os recursos relacionados.

```powershell
Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="script-explanation"></a>Explicação sobre o script
Esse script usa os seguintes comandos para criar a implantação. Cada item em que a tabela contém links para a documentação específica do comando.

| Comando | Observações |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Cria uma configuração de sub-rede. Essa configuração é usada com o processo de criação de rede virtual. |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Cria uma rede virtual. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Cria um endereço IP público. |
| [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) | Cria uma configuração de IP front-end para um balanceador de carga. |
| [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) | Cria uma configuração de pool de endereços de back-end para um balanceador de carga. |
| [New-AzLoadBalancerInboundNatRuleConfig](/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig) | Cria uma configuração de regra NAT de entrada para um balanceador de carga. |
| [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer) | Cria um balanceador de carga. |
| [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/new-azloadbalancerprobeconfig) | Cria uma configuração de investigação para um balanceador de carga. |
| [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/new-azloadbalancerruleconfig) | Cria uma configuração de regra para um balanceador de carga. |
| [Set-AzLoadBalancer](/powershell/module/az.Network/Set-azLoadBalancer) | Atualize o balanceador de carga com as informações fornecidas. |
| [New-AzVmssIpConfig](/powershell/module/az.Compute/New-azVmssIpConfig) | Crie uma configuração de IP para as instâncias de VM do conjunto de dimensionamento. As instâncias de VM estão conectadas ao pool de back-end do balanceador de carga, ao pool NAT e à sub-rede da rede virtual. |
| [New-AzVmssConfig](/powershell/module/az.Compute/New-azVmssConfig) | Criar a configuração do conjunto de dimensionamento. Essa configuração inclui informações como o número de instâncias de VM a ser criado, o SKU da VM (tamanho) e o modo de política de atualização. A configuração é adicionada por outros cmdlets e é usada durante a criação do conjunto de dimensionamento. |
| [Set-AzVmssStorageProfile](/powershell/module/az.Compute/Set-azVmssStorageProfile) | Defina a imagem a ser usada para as instâncias de VM e adicione-a à configuração do conjunto de dimensionamento. |
| [Set-AzVmssOsProfile](/powershell/module/az.Compute/Set-azVmssStorageProfile) | Defina o nome de usuário administrativo e as credenciais de senha e o prefixo de nomenclatura da VM. Adicione esses valores à configuração do conjunto de dimensionamento. |
| [Add-AzVmssNetworkInterfaceConfiguration](/powershell/module/az.Compute/Add-azVmssNetworkInterfaceConfiguration) | Adicione uma interface de rede virtual às instâncias de VM, com base na configuração de IP. Adicione esses valores à configuração do conjunto de dimensionamento. |
| [New-AzVmss](/powershell/module/az.Compute/New-azVmss) | Crie o conjunto de dimensionamento com base nas informações fornecidas na configuração do conjunto de dimensionamento. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Remove um grupo de recursos e todos os recursos contidos nele. |

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre o módulo do Azure PowerShell, confira [Documentação do Azure PowerShell](/powershell/azure/).
