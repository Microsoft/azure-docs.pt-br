---
title: Comandos comuns do PowerShell para Redes Virtuais do Azure | Microsoft Docs
description: Comandos comuns do PowerShell para ajudar você a criar uma rede virtual e seus recursos associados para VMs.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 56e1a73c-8299-4996-bd03-f74585caa1dc
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: cynthn
ms.openlocfilehash: db6afa2900cc67d971ba06d393a936da9b55b574
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67719921"
---
# <a name="common-powershell-commands-for-azure-virtual-networks"></a>Comandos comuns do PowerShell para Redes Virtuais do Azure

Se você quiser criar uma máquina virtual, será necessário criar uma [rede virtual](../../virtual-network/virtual-networks-overview.md) ou saber sobre uma rede virtual existente na qual a VM possa ser adicionada. Normalmente, quando você cria uma VM, você também precisa considerar a criação dos recursos descritos neste artigo.

Confira [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview) para saber mais sobre como instalar a versão mais recente do Azure PowerShell, selecionar a assinatura e entrar em sua conta.

Algumas variáveis poderão ser úteis para você se estiver executando mais de um dos comandos descritos neste artigo:

- $location – o local dos recursos de rede. Você pode usar [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation) para encontrar uma [região geográfica](https://azure.microsoft.com/regions/) que funciona para você.
- $myResourceGroup – o nome do grupo de recursos em que os recursos de rede estão localizados.

## <a name="create-network-resources"></a>Criar recursos da rede

| Tarefa | Comando |
| ---- | ------- |
| Criar configurações da sub-rede |$subnet1 = [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig) -Name "mySubnet1" -AddressPrefix XX.X.X.X/XX<BR>$subnet2 = New-AzVirtualNetworkSubnetConfig -Name "mySubnet2" -AddressPrefix XX.X.X.X/XX<BR><BR>Uma rede típica pode ter uma sub-rede para um [balanceador de carga para a internet](../../load-balancer/load-balancer-internet-overview.md) e uma sub-rede separada para um [balanceador de carga interno](../../load-balancer/load-balancer-internal-overview.md). |
| Criar uma rede virtual |$vnet = [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork) -Name "myVNet" -ResourceGroupName $myResourceGroup -Location $location -AddressPrefix XX.X.X.X/XX -Subnet $subnet1, $subnet2 |
| Testar um nome de domínio exclusivo |[Test-AzDnsAvailability](https://docs.microsoft.com/powershell/module/az.network/test-azdnsavailability) -DomainNameLabel "myDNS" -Location $location<BR><BR>Você pode especificar um nome de domínio DNS para um [recurso IP público](../../virtual-network/virtual-network-ip-addresses-overview-arm.md), que cria um mapeamento de domainname.location.cloudapp.azure.com para o endereço IP público nos servidores DNS gerenciados pelo Azure. O campo pode conter apenas letras, números e hifens. O primeiro e o último caractere devem ser uma letra ou um número e o nome de domínio deve ser exclusivo no local do seu Azure. Se **True** retornar, o nome proposto será globalmente exclusivo. |
| Criar um endereço IP público |$pip = [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress) -Name "myPublicIp" -ResourceGroupName $myResourceGroup -DomainNameLabel "myDNS" -Location $location -AllocationMethod Dynamic<BR><BR>O endereço IP público usa o nome de domínio criado anteriormente e é usado pela configuração de front-end do balanceador de carga. |
| Criar uma configuração de IP de front-end |$frontendIP = [New-AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig) -Name "myFrontendIP" -PublicIpAddress $pip<BR><BR>A configuração de front-end inclui o endereço IP público criado anteriormente para o tráfego de rede de entrada. |
| Criar um pool de endereços de back-end |$beAddressPool = [New-AzLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) -Name "myBackendAddressPool"<BR><BR>Fornece endereços internos para o back-end do balanceador de carga que são acessados por meio de uma interface de rede. |
| Criar uma investigação |$healthProbe = [New-AzLoadBalancerProbeConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerprobeconfig) -Name "myProbe" -RequestPath 'HealthProbe.aspx' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2<BR><BR>Contém as investigações de integridade usadas para verificar a disponibilidade das instâncias de máquina virtual no pool de endereços back-end. |
| Criar uma regra de balanceamento de carga |$lbRule = [New-AzLoadBalancerRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerruleconfig) -Name HTTP -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80<BR><BR>Contém regras que atribuem uma porta pública no balanceador de carga a uma porta no pool de endereços de back-end. |
| Criar uma regra NAT de entrada |$inboundNATRule = [New-AzLoadBalancerInboundNatRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig) -Name "myInboundRule1" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389<BR><BR>Contém regras que mapeiam uma porta pública no balanceador de carga para uma porta de uma máquina virtual específica no pool de endereços back-end. |
| Criar um balanceador de carga |$loadBalancer = [New-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancer) -ResourceGroupName $myResourceGroup -Name "myLoadBalancer" -Location $location -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule -LoadBalancingRule $lbRule -BackendAddressPool $beAddressPool -Probe $healthProbe |
| Criar um adaptador de rede |$nic1= [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface) -ResourceGroupName $myResourceGroup -Name "myNIC" -Location $location -PrivateIpAddress XX.X.X.X -Subnet $subnet2 -LoadBalancerBackendAddressPool $loadBalancer.BackendAddressPools[0] -LoadBalancerInboundNatRule $loadBalancer.InboundNatRules[0]<BR><BR>Crie uma interface de rede usando o endereço IP público e a sub-rede de rede virtual criada anteriormente. |

## <a name="get-information-about-network-resources"></a>Obter informações sobre recursos de rede

| Tarefa | Comando |
| ---- | ------- |
| Listar redes virtuais |[Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork) -ResourceGroupName $myResourceGroup<BR><BR>Lista todas as redes virtuais no grupo de recursos. |
| Obter informações sobre uma rede virtual |Get-AzVirtualNetwork -Name "myVNet" -ResourceGroupName $myResourceGroup |
| Listar sub-redes em uma rede virtual |Get-AzVirtualNetwork -Name "myVNet" -ResourceGroupName $myResourceGroup &#124; Select Subnets |
| Obter informações sobre uma sub-rede |[Get-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetworksubnetconfig) -Name "mySubnet1" -VirtualNetwork $vnet<BR><BR>Obtém informações sobre a sub-rede na rede virtual especificada. O valor $vnet representa o objeto retornado por Get-AzVirtualNetwork. |
| Listar endereços IP |[Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress) -ResourceGroupName $myResourceGroup<BR><BR>Lista os endereços IP públicos no grupo de recursos. |
| Listar balanceadores de carga |[Get-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/get-azloadbalancer) -ResourceGroupName $myResourceGroup<BR><BR>Lista todos os balanceadores de carga no grupo de recursos. |
| Listar adaptadores de rede |[Get-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/get-aznetworkinterface) -ResourceGroupName $myResourceGroup<BR><BR>Lista todos os adaptadores de rede no grupo de recursos. |
| Obter informações sobre um adaptador de rede |Get-AzNetworkInterface -Name "myNIC" -ResourceGroupName $myResourceGroup<BR><BR>Obtém informações sobre um adaptador de rede específico. |
| Obter a configuração de IP de um adaptador de rede |[Get-AzNetworkInterfaceIPConfig](https://docs.microsoft.com/powershell/module/az.network/get-aznetworkinterfaceipconfig) -Name "myNICIP" -NetworkInterface $nic<BR><BR>Obtém informações sobre a configuração de IP do adaptador de rede especificado. O valor $nic representa o objeto retornado por Get-AzNetworkInterface. |

## <a name="manage-network-resources"></a>Gerenciar recursos de rede

| Tarefa | Comando |
| ---- | ------- |
| Adicionar uma sub-rede a uma rede virtual |[Add-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/add-azvirtualnetworksubnetconfig) -AddressPrefix XX.X.X.X/XX -Name "mySubnet1" -VirtualNetwork $vnet<BR><BR>Adiciona uma sub-rede a uma rede virtual existente. O valor $vnet representa o objeto retornado por Get-AzVirtualNetwork. |
| Excluir uma rede virtual |[Remove-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/remove-azvirtualnetwork) -Name "myVNet" -ResourceGroupName $myResourceGroup<BR><BR>Remove a rede virtual especificada do grupo de recursos. |
| Excluir um adaptador de rede |[Remove-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/remove-aznetworkinterface) -Name "myNIC" -ResourceGroupName $myResourceGroup<BR><BR>Remove o adaptador de rede especificado do grupo de recursos. |
| Excluir um balanceador de carga |[Remove-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/remove-azloadbalancer) -Name "myLoadBalancer" -ResourceGroupName $myResourceGroup<BR><BR>Remove o balanceador de carga especificado do grupo de recursos. |
| Excluir um endereço IP público |[Remove-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/remove-azpublicipaddress)-Name "myIPAddress" -ResourceGroupName $myResourceGroup<BR><BR>Remove o endereço IP público especificado do grupo de recursos. |

## <a name="next-steps"></a>Próximas etapas
* Usar o adaptador de rede que você cria quando [cria uma VM](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Saiba mais sobre como você pode [criar uma VM com várias interfaces de rede](../../virtual-network/virtual-network-deploy-multinic-classic-ps.md).

