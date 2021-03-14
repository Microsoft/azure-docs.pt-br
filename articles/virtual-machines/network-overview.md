---
title: Redes virtuais
description: Saiba mais sobre a rede, pois ela está relacionada à criação de máquinas virtuais do Linux no Azure.
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 07/17/2017
ms.author: cynthn
ms.openlocfilehash: 20cc67d5c6436d7c0f44071509e13af324a88eea
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100578859"
---
# <a name="virtual-networks-and-virtual-machines-in-azure"></a>Redes virtuais e máquinas virtuais no Azure 

Ao criar uma VM (máquina virtual) do Azure, você deve criar uma [VNet](../virtual-network/virtual-networks-overview.md) (rede virtual) ou usar uma VNet existente. Você também precisa decidir como suas VMs devem ser acessadas na VNet. É importante [planejar antes de criar recursos](../virtual-network/virtual-network-vnet-plan-design-arm.md) e compreender os [limites de recursos de rede](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).

Na figura a seguir, as VMs são representadas como servidores Web e servidores de banco de dados. Cada conjunto de VMs é atribuído a sub-redes separadas na VNet.

![rede virtual do Azure](./media/virtual-machines-common-network-overview/vnetoverview.png)

Você pode criar uma rede virtual antes de criar uma máquina virtual ou pode criar uma máquina virtual. Você cria esses recursos para dar suporte à comunicação com uma VM:

- Interfaces de rede
- Endereços IP
- Rede virtual e sub-redes

Além desses recursos básicos, você também deve considerar estes recursos opcionais:

- Grupos de segurança de rede
- Balanceadores de carga 

## <a name="network-interfaces"></a>Interfaces de rede

Uma [NIC (interface de rede)](../virtual-network/virtual-network-network-interface.md) é a interconexão entre uma VM e uma VNet (rede virtual). Uma VM deve ter pelo menos uma NIC, mas pode ter mais de uma, dependendo do tamanho da VM que você criar. Saiba mais sobre quantas NICs cada tamanho de VM dá suporte, consulte [tamanhos de VM](sizes.md).

Você pode criar uma VM com várias placas de rede e, em seguida, adicionar ou remover NICs por meio do ciclo de vida de uma VM. Várias NICs permitem que uma máquina virtual se conecte a várias sub-redes e envie ou receba tráfego na interface mais apropriada. VMs com qualquer número de interfaces de rede podem existir no mesmo conjunto de disponibilidade, até o número suportado pelo tamanho da VM. 

Cada NIC conectada a uma VM deve existir no mesmo local e assinatura que a VM. Cada NIC deve ser conectada a uma VNet que existe na mesma localização e assinatura do Azure que a NIC. Você pode alterar a sub-rede à qual uma VM está conectada depois de criá-la, mas não é possível alterar a rede virtual. Um endereço MAC é atribuído a cada NIC conectada a uma VM e ele não é alterado até que a VM seja excluída.

Esta tabela lista os métodos que você pode usar para criar uma interface de rede.

| Método | Descrição |
| ------ | ----------- |
| Portal do Azure | Quando você cria uma VM no portal do Azure, uma interface de rede é criada automaticamente para você (não é possível usar uma NIC que você cria separadamente). O portal cria uma VM com apenas uma NIC. Se quiser criar uma VM com mais de uma NIC, você deverá criá-la com um método diferente. |
| [PowerShell do Azure](./windows/multiple-nics.md) | Use [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) com o parâmetro **-PublicIpAddressId** para fornecer o identificador do endereço IP público que você criou anteriormente. |
| [CLI do Azure](./linux/multiple-nics.md) | Para fornecer o identificador do endereço IP público que você criou anteriormente, use [az network nic create](/cli/azure/network/nic) com o parâmetro **--public-ip-address**. |
| [Modelo](../virtual-network/template-samples.md) | Use [Interface de rede em uma rede Virtual com endereço IP público](https://github.com/Azure/azure-quickstart-templates/tree/master/101-nic-publicip-dns-vnet) como um guia para a implantação de uma interface de rede usando um modelo. |

## <a name="ip-addresses"></a>Endereços IP 

Você pode atribuir estes tipos de [endereços IP](../virtual-network/public-ip-addresses.md) a uma NIC no Azure:

- **Endereços IP públicos** - usados para comunicação de entrada e saída (sem NAT (conversão de endereços de rede)) com a Internet e outros recursos do Azure não conectados a uma VNet. Atribuir um endereço IP público a uma NIC é opcional. Os endereços IP públicos têm um custo nominal, e há um número máximo que pode ser usado por assinatura.
- **Endereços IP privados** - usados para comunicação em uma VNet, sua rede local e a Internet (com NAT). Você deve atribuir pelo menos um endereço IP privado a uma VM. Para saber mais sobre NAT no Azure, leia [Noções básicas sobre conexões de saída no Azure](../load-balancer/load-balancer-outbound-connections.md).

Você pode atribuir endereços IP públicos a VMs ou balanceadores de carga voltados para a Internet. Você pode atribuir endereços IP a VMs e balanceadores de carga internos. Você atribui endereços IP a uma VM usando uma interface de rede.

Há dois métodos de alocar um endereço IP para um recurso - dinâmico ou estático. O método de alocação padrão é dinâmico, em que um endereço IP não é alocado quando ele é criado. Em vez disso, o endereço IP é alocado quando você cria uma VM ou inicia uma VM parada. O endereço IP é liberado quando você para ou exclui a VM. 

Para garantir que o endereço IP para a VM permaneça o mesmo, você pode definir o método de alocação explicitamente como estático. Nesse caso, um endereço IP é atribuído imediatamente. Ele é liberado apenas quando você exclui a VM ou altera seu método de alocação para dinâmico.
    
Esta tabela lista os métodos que você pode usar para criar um endereço IP.

| Método | Descrição |
| ------ | ----------- |
| [Azure portal](../virtual-network/virtual-network-deploy-static-pip-arm-portal.md) | Por padrão, endereços IP públicos são dinâmicos, e o endereço associado a eles pode ser alterado quando a VM é parada ou excluída. Para assegurar que a VM sempre use o mesmo endereço IP público, crie um endereço IP público estático. Por padrão, o portal atribui um endereço IP privado dinâmico a uma NIC ao criar uma VM. Você pode alterar esse endereço IP para estático após a criação da VM.|
| [PowerShell do Azure](../virtual-network/virtual-network-deploy-static-pip-arm-ps.md) | Você usa [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) com o parâmetro **-AllocationMethod** como Dinâmico ou Estático. |
| [CLI do Azure](../virtual-network/virtual-network-deploy-static-pip-arm-cli.md) | Você usa [az network public-ip create](/cli/azure/network/public-ip) com o parâmetro **--allocation-method** como Dinâmico ou Estático. |
| [Modelo](../virtual-network/template-samples.md) | Use [Interface de rede em uma rede Virtual com endereço IP público](https://github.com/Azure/azure-quickstart-templates/tree/master/101-nic-publicip-dns-vnet) como um guia para implantar um IP público de endereço usando um modelo. |

Após criar um endereço IP público, você pode associá-lo a uma VM, atribuindo-o a uma NIC.

## <a name="virtual-network-and-subnets"></a>Rede virtual e sub-redes

Uma sub-rede é um intervalo de endereços IP na rede virtual. Você pode dividir a VNet em várias sub-redes para fins de organização e segurança. Cada NIC em uma VM está conectada a uma sub-rede em uma VNet. NICs conectadas às sub-redes (iguais ou diferentes) em uma VNet podem se comunicar entre si sem nenhuma configuração adicional.

Ao configurar uma rede virtual, você pode especificar a topologia, incluindo os espaços de endereço e sub-redes disponíveis. Se a VNet is precisar ser conectada a outras VNets ou redes locais, você deverá selecionar intervalos de endereços que não se sobreponham. Os endereços IP são privados e não podem ser acessados pela Internet, o que era verdadeiro apenas para os endereços IP não roteáveis, como 10.0.0.0/8, 172.16.0.0/12 ou 192.168.0.0/16. Agora, o Azure trata qualquer intervalo de endereços como parte do espaço de endereço IP VNet particular que está acessível apenas através da VNet, em VNets interconectadas e do seu local. 

Se trabalha em uma organização em que outra pessoa é responsável por redes internas, você deve conversar com essa pessoa antes de selecionar o espaço de endereço. Verifique se não há sobreposição e informe o espaço que deseja usar para que eles não tentem usar o mesmo intervalo de endereços IP. 

Por padrão, não há limite de segurança entre sub-redes. Portanto, as VMs em cada uma das sub-redes podem se comunicar entre si. No entanto, você pode configurar NSGs (Grupos de Segurança de Rede), que permitem controlar o fluxo de tráfego de e para sub-redes e de e para VMs. 

Esta tabela lista os métodos que você pode usar para criar uma VNet e sub-redes.    

| Método | Descrição |
| ------ | ----------- |
| [Azure portal](../virtual-network/quick-create-portal.md) | Se você permitir que o Azure crie uma VNet quando você criar uma VM, o nome será uma combinação do nome do grupo de recursos que contém a VNet e **-vnet**. O espaço de endereço é 10.0.0.0/24, o nome da sub-rede necessária é **default** e o intervalo de endereços de sub-rede é 10.0.0.0/24. |
| [PowerShell do Azure](../virtual-network/quick-create-powershell.md) | Você usa [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworkSubnetConfig) e [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) para criar uma sub-rede e uma VNet. Você também pode usar [Add-AzVirtualNetworkSubnetConfig](/powershell/module/Az.Network/Add-AzVirtualNetworkSubnetConfig) para adicionar uma sub-rede a uma rede virtual existente. |
| [CLI do Azure](../virtual-network/quick-create-cli.md) | A sub-rede e a VNet são criadas ao mesmo tempo. Forneça um parâmetro **--subnet-name** para [az network vnet create](/cli/azure/network/vnet) com o nome da sub-rede. |
| Modelo | A maneira mais fácil de criar uma VNet e sub-redes é fazer o download de um modelo existente, como [Rede Virtual com duas sub-redes](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vnet-two-subnets), e modificá-lo de acordo com suas necessidades. |

## <a name="network-security-groups"></a>Grupos de segurança de rede

Um [NSG (grupo de segurança de rede)](../virtual-network/virtual-network-vnet-plan-design-arm.md) contém uma lista de regras de ACL (Lista de Controle de Acesso) que permitem ou negam o tráfego de rede para sub-redes, NICs ou ambos. Os NSGs podem ser associados a sub-redes ou NICs individuais conectadas a uma sub-rede. Quando um NSG é associado a uma sub-rede, as regras de ACL se aplicam a todas as VMs na sub-rede. Além disso, o tráfego para uma NIC individual pode ser restringido associando um NSG diretamente a uma NIC.

Os NSGs contêm dois conjuntos de regras: entrada e saída. A prioridade de uma regra deve ser exclusiva em cada conjunto. Cada regra tem propriedades de protocolo, origem e intervalos de porta de destino, prefixos de endereço, direção de tráfego, prioridade e tipo de acesso. 

Todos os NSGs contêm um conjunto de regras padrão. As regras padrão não podem ser excluídas, mas como recebem a prioridade mais baixa, elas podem ser substituídas pelas regras que você criar. 

Quando um NSG é associado a uma NIC, as regras de acesso à rede no NSG são aplicadas somente a essa NIC. Se um NSG for aplicado a uma única NIC em uma VM com várias NICS, isso não afetará o tráfego para as outras NICs. É possível associar diferentes NSGs a um NIC (ou VM, dependendo do modelo de implantação) e à sub-rede a qual uma NIC ou VM está associada. A prioridade é fornecida com base na direção do tráfego.

Não se esqueça de [planejar](../virtual-network/virtual-network-vnet-plan-design-arm.md) seus NSGs ao planejar suas VMs e a VNet.

Esta tabela lista os métodos que você pode usar para criar um grupo de segurança de rede.

| Método | Descrição |
| ------ | ----------- |
| [Azure portal](../virtual-network/tutorial-filter-network-traffic.md) | Quando você cria uma VM no portal do Azure, um NSG é criado e associado automaticamente à NIC que o portal cria. O nome do NSG é uma combinação do nome da VM e **-nsg**. Este NSG contém uma regra de entrada com uma prioridade de 1000, o serviço definido como RDP, o protocolo definido como TCP, a porta configurada para 3389 e as ações definidas para Permitir. Se quiser permitir qualquer outro tráfego de entrada para a VM, você deverá incluir regras adicionais para o NSG. |
| [PowerShell do Azure](../virtual-network/tutorial-filter-network-traffic.md) | Use [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) e forneça as informações de regra necessárias. Use [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) para criar o NSG. Use [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) para configurar o NSG da sub-rede. Use [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) para adicionar o NSG à rede virtual. |
| [CLI do Azure](../virtual-network/tutorial-filter-network-traffic-cli.md) | Use [az network nsg create](/cli/azure/network/nsg) para criar inicialmente o NSG. Use [az network nsg rule create](/cli/azure/network/nsg/rule) para adicionar regras para o NSG. Use [az network vnet subnet update](/cli/azure/network/vnet/subnet) para adicionar o NSG à sub-rede. |
| [Modelo](../virtual-network/template-samples.md) | Use [Criar um grupo de segurança de rede](https://github.com/Azure/azure-quickstart-templates/tree/master/101-security-group-create) como um guia para a implantação de um grupo de segurança de rede usando um modelo. |

## <a name="load-balancers"></a>Balanceadores de carga

O [Azure Load Balancer](../load-balancer/load-balancer-overview.md) oferece alta disponibilidade e desempenho de rede para seus aplicativos. Um balanceador de carga pode ser configurado para [equilibrar o tráfego da Internet](../load-balancer/components.md#frontend-ip-configurations) para VMs ou [equilibrar o tráfego entre VMs em uma rede virtual](../load-balancer/components.md#frontend-ip-configurations). Um balanceador de carga também pode equilibrar o tráfego entre computadores locais e VMs em uma rede entre locais ou encaminhar tráfego externo para uma VM específica.

O balanceador de carga mapeia o tráfego de entrada e saída entre o endereço IP público e a porta do balanceador de carga e o endereço IP privado e a porta da VM.

Ao criar um balanceador de carga, você também deve considerar estes elementos de configuração:

- **Configuração de IP front-end** – um balanceador de carga pode incluir um ou mais endereços IP front-end. Esses endereços IP servem como entrada para o tráfego.
- **Pool de endereços de back-end** – endereços IP que estão associados à NIC para a qual a carga é distribuída.
- **[Encaminhamento de porta](../load-balancer/tutorial-load-balancer-port-forwarding-portal.md)** – define como o tráfego de entrada flui pelo IP de front-end e é distribuído para o IP de back-end usando regras NAT de entrada.
- **Regras do balanceador de carga** -mapeia determinada combinação de porta e IP front-end para um conjunto de endereços IP back-end e combinação de portas. Um balanceador de carga único pode ter várias regras de balanceamento de carga. Cada regra é uma combinação de um IP de front-end e IP de porta e back-end e porta associada a VMs.
- **[Sondas](../load-balancer/load-balancer-custom-probe-overview.md)** - monitora a integridade das VMs. Quando uma investigação não responde, o balanceador de carga interrompe o envio de novas conexões para a VM não íntegra. As conexões existentes não são afetadas e novas conexões são enviadas para VMs íntegras.
- **[Regras de saída](../load-balancer/load-balancer-outbound-connections.md#outboundrules)** – uma regra de saída configura a NAT (conversão de endereços de rede) de saída para todas as máquinas virtuais identificadas pelo pool de back-end do Standard Load Balancer a serem convertidas no front-end.

Esta tabela lista os métodos que você pode usar para criar um balanceador de carga voltado para a Internet.

| Método | Descrição |
| ------ | ----------- |
| Portal do Azure |  Você pode [balancear a carga de tráfego de Internet para VMs que estejam usando o portal do Azure](../load-balancer/quickstart-load-balancer-standard-public-portal.md). |
| [PowerShell do Azure](../load-balancer/quickstart-load-balancer-standard-public-powershell.md) | Para fornecer o identificador do endereço IP público que você criou anteriormente, use [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) com o parâmetro **-PublicIpAddress**. Use [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) para criar a configuração do pool de endereços de back-end. Use [New-AzLoadBalancerInboundNatRuleConfig](/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig) para criar regras NAT de entrada associadas à configuração de IP front-end que você criou. Use [New-AzLoadBalancerProbeConfig](/powershell/module/az.network/new-azloadbalancerprobeconfig) para criar os testes de que você precisa. Use [New-AzLoadBalancerRuleConfig](/powershell/module/az.network/new-azloadbalancerruleconfig) para criar a configuração de balanceador de carga. Use [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer) para criar o balanceador de carga.|
| [CLI do Azure](../load-balancer/quickstart-load-balancer-standard-public-cli.md) | Use [az network lb create](/cli/azure/network/lb) para criar a configuração de balanceador de carga inicial. Use [az network lb frontend-ip create](/cli/azure/network/lb/frontend-ip) para adicionar o endereço IP público que você criou anteriormente. Use [az network lb address-pool create](/cli/azure/network/lb/address-pool) para adicionar a configuração do pool de endereços de back-end. Use [az network lb inbound-nat-rule create](/cli/azure/network/lb/inbound-nat-rule) para adicionar regras de NAT. Use [az network lb rule create](/cli/azure/network/lb/rule) para adicionar as regras do balanceador de carga. Use [az network lb probe create](/cli/azure/network/lb/probe) para adicionar as sondas. |
| [Modelo](../load-balancer/quickstart-load-balancer-standard-public-template.md) | Use [3 VMs em um Load Balancer](https://github.com/Azure/azure-quickstart-templates/tree/master/101-load-balancer-standard-create) como um guia para implantar um balanceador de carga usando um modelo. |
    
Esta tabela lista os métodos que você pode usar para criar um balanceador de carga interno.

| Método | Descrição |
| ------ | ----------- |
| Portal do Azure | É possível [balancear carga de tráfego interna com um balanceador de carga no portal do Azure](../load-balancer/quickstart-load-balancer-standard-internal-portal.md). |
| [PowerShell do Azure](../load-balancer/quickstart-load-balancer-standard-internal-powershell.md) | Para fornecer um endereço IP privado na sub-rede, use [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) com o parâmetro **-PrivateIpAddress**. Use [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) para criar a configuração do pool de endereços de back-end. Use [New-AzLoadBalancerInboundNatRuleConfig](/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig) para criar regras NAT de entrada associadas à configuração de IP front-end que você criou. Use [New-AzLoadBalancerProbeConfig](/powershell/module/az.network/new-azloadbalancerprobeconfig) para criar os testes de que você precisa. Use [New-AzLoadBalancerRuleConfig](/powershell/module/az.network/new-azloadbalancerruleconfig) para criar a configuração de balanceador de carga. Use [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer) para criar o balanceador de carga.|
| [CLI do Azure](../load-balancer/quickstart-load-balancer-standard-internal-cli.md) | Use o comando [az network lb create](/cli/azure/network/lb) para criar a configuração de balanceador de carga inicial. Para definir o endereço IP privado, use [az network lb frontend-ip create](/cli/azure/network/lb/frontend-ip) com o parâmetro **--private-ip-address**. Use [az network lb address-pool create](/cli/azure/network/lb/address-pool) para adicionar a configuração do pool de endereços de back-end. Use [az network lb inbound-nat-rule create](/cli/azure/network/lb/inbound-nat-rule) para adicionar regras de NAT. Use [az network lb rule create](/cli/azure/network/lb/rule) para adicionar as regras do balanceador de carga. Use [az network lb probe create](/cli/azure/network/lb/probe) para adicionar as sondas.|
| [Modelo](../load-balancer/quickstart-load-balancer-standard-internal-template.md) | Use [duas VMs em um Load Balancer](https://github.com/Azure/azure-quickstart-templates/tree/master/201-2-vms-internal-load-balancer) como um guia para implantar um balanceador de carga usando um modelo. |

### <a name="virtual-machine-scale-sets"></a>conjuntos de escala de máquina virtual

Para obter mais informações sobre o balanceador de carga e conjuntos de dimensionamento de máquinas virtuais, confira [Rede para conjuntos de dimensionamento de máquinas virtuais do Azure](../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md).

## <a name="vms"></a>VMs

As VMs podem ser criadas na mesma VNet e podem se conectar entre si usando endereços IP privados. Eles podem se conectar mesmo que estejam em sub-redes diferentes, sem a necessidade de configurar um gateway ou usar endereços IP públicos. Para colocar as VMs em uma rede virtual, crie a rede virtual e, ao criar cada VM, atribua-a à VNet e à sub-rede. As VMs adquirem suas configurações de rede durante a implantação ou a inicialização.  

Um endereço IP é atribuído às VMs quando elas são implantadas. Se você implantar várias VMs em uma rede virtual ou sub-rede, endereços IP serão atribuídos quando elas forem inicializadas. Você também pode alocar um IP estático para uma VM. Se você alocar um IP estático, considere usar uma sub-rede específica para evitar a reutilização acidental de um IP estático para outra VM.  

Se você criar uma VM e, depois, quiser migrá-la para uma rede virtual, essa não será uma alteração simples na configuração. Você deve reimplantar a VM na VNet. A maneira mais fácil der reimplantar é excluir a VM, mas não os discos anexados a ela e, em seguida, recriar a VM usando os discos originais na VNet. 

Esta tabela lista os métodos que você pode usar para criar uma VM em uma VNet.

| Método | Descrição |
| ------ | ----------- |
| [Azure portal](./windows/quick-create-portal.md) | Usa as configurações de rede padrão mencionadas anteriormente para criar uma VM com uma única NIC. Para criar uma VM com várias NICs, você deve usar um método diferente. |
| [PowerShell do Azure](./windows/tutorial-manage-vm.md) | Inclui o uso de [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface) para adicionar o NIC que você criou anteriormente para a configuração da VM. |
| [CLI do Azure](./linux/create-cli-complete.md) | Crie e conecte a uma VM para uma rede virtual, sub-rede e NIC criar como etapas individuais. |
| [Modelo](./windows/ps-template.md) | Use [Implantação muito simples de uma VM do Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows) como um guia para implantar uma VM usando um modelo. |

## <a name="next-steps"></a>Próximas etapas
Para etapas de VM específicas sobre como gerenciar redes virtuais do Azure para máquinas virtuais, veja os tutoriais do [Windows](../virtual-machines/windows/tutorial-virtual-network.md) ou [Linux](../virtual-machines/linux/tutorial-virtual-network.md).

Também existem tutoriais sobre como balancear a carga de VMs e criar aplicativos altamente disponíveis para [Windows](../virtual-machines/windows/tutorial-load-balancer.md) ou [Linux](../virtual-machines/linux/tutorial-load-balancer.md).

- Saiba como configurar [rotas definidas pelo usuário e encaminhamento IP](../virtual-network/virtual-networks-udr-overview.md). 
- Saiba como configurar [conexões de VNet para VNet](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).
- Saiba como [Solucionar problemas de rotas](../virtual-network/diagnose-network-routing-problem.md).
- Saiba mais sobre [largura de banda de rede da máquina virtual](../virtual-network/virtual-machine-network-throughput.md).