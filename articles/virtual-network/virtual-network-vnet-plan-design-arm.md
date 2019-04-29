---
title: Planejamento das Redes Virtuais do Microsoft Azure | Microsoft Docs
description: Aprenda a planejar redes virtuais com base no seu isolamento, conectividade e requisitos de local.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: 3a4a9aea-7608-4d2e-bb3c-40de2e537200
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/16/2018
ms.author: kumud
ms.openlocfilehash: acd7a88acb31b9d3bd3ba714387561e91b3524a6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61034732"
---
# <a name="plan-virtual-networks"></a>Planejar redes virtuais

Criar uma rede virtual para fazer experiências com ela é bastante simples, mas as chances são: você implantará múltiplas redes virtuais ao longo do tempo para dar suporte às necessidades de produção da sua organização. Com algum planejamento, você poderá implantar redes virtuais e conectar-se aos recursos de que precisa com mais eficiência. As informações neste artigo serão mais úteis se você já estiver familiarizado com as redes virtuais e tiver alguma experiência de trabalhar com elas. Se você não estiver familiarizado com redes virtuais, é recomendável que você leia [visão geral da rede Virtual](virtual-networks-overview.md).

## <a name="naming"></a>Nomenclatura

Todos os recursos do Azure têm um nome. O nome deve ser exclusivo dentro de um escopo, o que pode variar para cada tipo de recurso. Por exemplo, o nome de uma rede virtual deve ser exclusivo dentro de um [grupo de recursos](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group), mas pode ser duplicado em uma [assinatura](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) ou região do [Azure](https://azure.microsoft.com/regions/#services). Definir uma convenção de nomenclatura que você pode usar consistentemente ao nomear recursos é útil ao gerenciar vários recursos de rede ao longo do tempo. Para sugestões, consulte [Convenções de nomenclatura](/azure/architecture/best-practices/naming-conventions?toc=%2fazure%2fvirtual-network%2ftoc.json#networking).

## <a name="regions"></a>Regiões

Todos os recursos do Microsoft Azure são criados na região do Azure e assinatura. Um recurso só poderá ser criado a uma rede virtual que existir na mesma região e assinatura em que o recurso está. No entanto, você pode se conectar a redes virtuais que existem em diferentes assinaturas e regiões. Para obter mais informações, consulte [conectividade](#connectivity). Ao decidir quais regiões implantar recursos, considere onde estão localizados fisicamente os consumidores de recursos:

- Os consumidores de recursos normalmente desejam a menor latência de rede para seus recursos. Para determinar as latências relativas entre um local especificado e regiões do Azure, consulte [Exibir latências relativas](../network-watcher/view-relative-latencies.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Você tem requisitos de residência, soberania, conformidade ou resiliência de dados? Nesse caso, escolher a região que atenda aos requisitos é fundamental. Para obter mais informações, consulte [Geografias do Microsoft Azure](https://azure.microsoft.com/global-infrastructure/geographies/).
- Você precisa de resiliência em Zonas de Disponibilidade do Azure na mesma zona do Azure para os recursos que você implantar? Você pode implantar os recursos, como máquinas virtuais (VM) para zonas de disponibilidade diferente na mesma rede virtual. No entanto, nem todas as regiões do Azure oferecem suporte zonas de disponibilidade. Para saber mais sobre as zonas de disponibilidade e as regiões que a suportam, consulte [zonas de disponibilidade](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="subscriptions"></a>Assinaturas

Você pode implantar tantas redes virtuais, quanto for necessário em cada assinatura, até o [limite](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits). Algumas organizações têm assinaturas diferentes para diferentes departamentos, por exemplo. Para obter mais informações e considerações de assinaturas, consulte [Governança de assinatura](/azure/architecture/cloud-adoption-guide/subscription-governance#define-your-hierarchy).

## <a name="segmentation"></a>Segmentação

Você pode criar várias redes virtuais por assinatura e por região. Você pode criar várias sub-redes em cada rede virtual. As considerações a seguiro ajudarão a determinar quantas redes virtuais e sub-redes que você precisa:

### <a name="virtual-networks"></a>Redes virtuais

Uma rede virtual é uma parte virtual, isolada da rede pública do Azure. Cada rede virtual é dedicado à sua assinatura. Coisas a serem consideradas ao decidir se deseja criar uma rede virtual, ou várias redes virtuais em uma assinatura:

- Os requisitos de segurança organizacional existem para isolar o tráfego em redes virtuais separadas? Você pode optar por conectar redes virtuais ou não. Se você se conectar a redes virtuais, será possível implementar um dispositivo virtual de rede, como um firewall, para controlar o fluxo de tráfego entre as redes virtuais. Para obter mais informações, confira [segurança](#security) e [conectividade](#connectivity).
- Há algum requisito organizacional para as redes virtuais de isolamento em [assinaturas](#subscriptions) ou [regiões](#regions) separadas?
- Uma [interface de rede](virtual-network-network-interface.md) permite que uma máquina virtual se comunique com outros recursos. Cada interface pode ter um ou mais endereços IP públicos ou privados atribuídos a ela. Quantas interfaces de rede e [endereços IP privados](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) são necessários em uma rede virtual? Há [limites](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) para o número de endereços IP públicos e privados que você pode ter dentro de uma rede virtual.
- Você deseja conectar a rede virtual à outra rede virtual na rede local? Você pode optar por conectar a algumas redes virtuais entre si ou redes locais, mas não outras. Para obter mais informações, consulte [conectividade](#connectivity). Cada rede virtual que você se conectar a outra rede virtual ou a rede local, deve ter um espaço de endereço exclusivo. Cada rede virtual tem um ou mais intervalos de endereço público ou privado atribuídos ao seu espaço de endereço. Um intervalo de endereços é especificado em roteamento do domínio de internet de classes (CIDR), como 10.0.0.0/16. Saiba mais sobre [intervalos de endereços](manage-virtual-network.md#add-or-remove-an-address-range) para redes virtuais.
- Você tem os requisitos de administração organizacional de recursos em diferentes redes virtuais? Em caso positivo, você pode separar os recursos em uma rede virtual separado para simplificar [atribuição de permissão](#permissions) a indivíduos na sua organização ou atribuir diferentes políticas para diferentes redes virtuais.
- Quando você implanta alguns recursos de serviço do Azure em uma rede virtual, eles criam sua própria rede virtual. Para determinar se um serviço do Azure cria sua própria rede virtual, consulte as informações para cada [serviço do Azure que pode ser implantado em uma rede virtual](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network).

### <a name="subnets"></a>Sub-redes

Uma rede virtual pode ser segmentada em uma ou mais sub-redes até o [limites](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits). Coisas a serem consideradas ao decidir se deseja criar uma sub-rede, ou várias redes virtuais em uma assinatura:

- Cada sub-rede deve ter um intervalo de endereço exclusivo, especificado no formato CIDR, dentro no espaço de endereço da rede virtual. O intervalo não pode sobrepor-se com outros intervalos de endereços de sub-rede dentro da rede virtual.
- Se você planeja implantar alguns recursos de serviço do Azure em uma rede virtual, podem exigir, ou criar suas próprias sub-redes, assim deve haver espaço não alocado para fazerem isso. Para determinar se um serviço do Azure cria sua própria sub-rede, consulte as informações para cada [serviço do Azure que pode ser implantado em uma rede virtual](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network). Por exemplo, se você se conectar a uma rede virtual a uma rede local usando um Gateway de VPN do Microsoft Azure, a rede virtual deve ter uma sub-rede dedicada para o gateway. Saiba mais sobre [as sub-redes de gateway](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub).
- Por padrão, o Azure roteia o tráfego entre todas as sub-redes em uma rede virtual. Você pode substituir o padrão de roteamento do Azure para evitar que o Azure roteie entre as sub-redes ou para rotear o tráfego entre as sub-redes através de uma solução de virtualização de rede, por exemplo. Se você precisar que o tráfego entre os recursos do mesmo fluxo de rede virtual por meio de um dispositivo de rede virtual (NVA), implante os recursos em sub-redes diferentes. Saiba mais em [segurança](#security).
- Você pode limitar o acesso a recursos do Microsoft Azure como uma conta de armazenamento do Azure ou banco de dados SQL do Azure, para especificar sub-redes com um ponto de extremidade de serviço de rede virtual. Além disso, você pode negar o acesso aos recursos da internet. Você pode criar várias sub-redes e habilitar um ponto de extremidade de serviço para algumas sub-redes, mas outros não. Saiba mais sobre [pontos de extremidade de serviço](virtual-network-service-endpoints-overview.md) e os recursos do Azure possibilitam que você os habilite.
- Você pode associar grupo de segurança a uma rede ou zero para cada sub-rede em uma rede virtual. Você pode associar o mesmo ou um grupo de segurança de rede para cada sub-rede. Cada grupo de segurança de rede contém regras que permitem ou negam o tráfego de e para origens e destinos. Saiba mais sobre [grupos de segurança de rede](#traffic-filtering).

## <a name="security"></a>Segurança

Você pode filtrar o tráfego de rede para e de recursos em uma rede virtual usando grupos de segurança de rede e dispositivos de rede virtual. Você pode controlar como o Azure direciona o tráfego de sub-redes. Você também pode limitar quem pode trabalhar com recursos em redes virtuais na sua organização.

### <a name="traffic-filtering"></a>Filtragem de tráfego

- Você pode filtrar o tráfego de rede entre os recursos em uma rede virtual usando um grupo de segurança de rede, uma NVA que filtra o tráfego de rede, ou ambos. Para implantar uma NVA, como um firewall, para filtrar o tráfego de rede, consulte o [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?subcategories=appliances&page=1). Ao usar uma NVA, você também criar rotas personalizadas para rotear o tráfego de sub-redes para a NVA. Saiba mais sobre [roteamento de tráfego](#traffic-routing).
- Um grupo de segurança de rede contém várias regras de segurança padrão que permitem ou negam o tráfego para ou de recursos. Um grupo de segurança de rede pode estar associado à interface de rede, a sub-rede, a interface de rede, ou ambos. Para simplificar o gerenciamento das regras de segurança, é recomendável que você associe um grupo de segurança de rede para as sub-redes individuais, em vez de interfaces de rede individuais dentro da sub-rede, sempre que possível.
- Se diferentes VMs dentro de uma sub-rede estiverem dentro das regras de segurança aplicadas a elas, você pode associar a interface de rede na VM a um ou mais grupos de segurança do aplicativo. Uma regra de segurança pode especificar um grupo de segurança do aplicativo em sua origem, destino ou ambos. Essa regra, em seguida, só se aplica às interfaces de rede que são membros do grupo de segurança de aplicativos. Saiba mais sobre [grupos de segurança de rede](security-overview.md) e [grupos de segurança de aplicativo](security-overview.md#application-security-groups).
- O Azure cria várias regras de segurança padrão dentro de cada grupo de segurança de rede. Uma regra padrão permite que todo o tráfego flua entre todos os recursos em uma rede virtual. Para substituir esse comportamento, use os grupos de segurança de rede, personalizar roteamento para rotear o tráfego a um NVA, ou ambos. É recomendável que você se familiarize com todas regras de segurança padrão do Azure [padrão de regras de segurança](security-overview.md#default-security-rules) e compreenda como as regras de grupo de segurança de rede são aplicadas a um recurso.

Você pode exibir projetos de exemplo para a implementação de um DMZ entre o Azure e a internet usando um [NVA](/azure/architecture/reference-architectures/dmz/secure-vnet-dmz?toc=%2Fazure%2Fvirtual-network%2Ftoc.json) ou [grupos de segurança de rede](virtual-networks-dmz-nsg.md).

### <a name="traffic-routing"></a>Roteamento de tráfego

O Azure cria várias rotas padrão para tráfego de saída de uma sub-rede. Você pode substituir o roteamento padrão do Azure criando uma tabela de roteamento e associando-a a uma sub-rede. Os motivos comuns para a substituição padrão do roteamento do Azure são:
- Porque você deseja que o tráfego entre as sub-redes flua por uma NVA. Para saber mais sobre como [configurar tabelas de rotas para forçar o tráfego por meio de uma NVA](tutorial-create-route-table-portal.md).
- Porque deseja forçar todo o tráfego direcionado à internet por meio de uma NVA, ou no local, por meio de um gateway de VPN do Azure. Forçar o tráfego de internet no local para inspeção e registro em log também é conhecido como túnel forçado. Saiba mais sobre como configurar o [túnel forçado](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2Fazure%2Fvirtual-network%2Ftoc.json).

Se você precisar implementar roteamento personalizado, é recomendável que você se familiarize com [roteamento no Azure](virtual-networks-udr-overview.md).

## <a name="connectivity"></a>Conectividade

Você pode conectar uma rede virtual para outras redes virtuais usando o emparelhamento de rede virtual, ou para sua rede local, usando um gateway de VPN do Azure.

### <a name="peering"></a>Emparelhamento

Ao usar [emparelhamento de rede virtual](virtual-network-peering-overview.md), as redes virtuais podem estar no mesmo, ou diferente, suporte para regiões do Azure. As redes virtuais podem estar nas mesmas ou diferentes assinaturas do Azure (até mesmo assinaturas pertencentes a diferentes locatários do Azure Active Directory). Antes de criar um emparelhamento, é recomendável que você se familiarize com os [requerimentos e as restrições de emparelhamento](virtual-network-manage-peering.md#requirements-and-constraints). A largura de banda entre recursos em redes virtuais emparelhada na mesma região é a mesma que se os recursos estivessem na mesma rede virtual.

### <a name="vpn-gateway"></a>gateway de VPN

Você pode usar um [Gateway de VPN](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) do Azure para se conectar a uma rede virtual para sua rede local usando um [VPN site a site](../vpn-gateway/vpn-gateway-tutorial-vpnconnection-powershell.md?toc=%2fazure%2fvirtual-network%2ftoc.json), ou usando uma conexão dedicada ao Azure [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

É possível combinar emparelhamento e um gateway de VPN para criar [redes de spoke e hub](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json), em que as redes virtuais spoke conectam-se a uma rede virtual do hub e o hub conecta-se a uma rede local, por exemplo.

### <a name="name-resolution"></a>Resolução de nomes

Recursos em uma rede virtual não podem resolver os nomes de recursos em uma rede virtual emparelhada usando o recurso [DNS interno](virtual-networks-name-resolution-for-vms-and-role-instances.md) do Azure. Para resolver nomes em redes virtuais emparelhadas, você precisa [implantar seu próprio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) ou usar [domínios privados do DNS do Azure](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Resolução de nomes entre redes locais e recursos em uma rede virtual também requer que você implante seu próprio servidor DNS.

## <a name="permissions"></a>Permissões

O Azure utiliza o [controle de acesso com base na regra](../role-based-access-control/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (RBAC) para recursos. As permissões são atribuídas a um [escopo](../role-based-access-control/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#scope) na seguinte hierarquia: Assinatura, grupo de gerenciamento, grupo de recursos e recursos individuais. Para saber mais sobre a hierarquia, consulte [Organizar os recursos](../azure-resource-manager/management-groups-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Para trabalhar com redes virtuais do Azure e todos os seus recursos relacionados como emparelhamento, grupos de segurança de rede, pontos de extremidade de serviço e tabelas de rotas, você pode atribuir membros de sua organização para o [proprietário](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#owner) interno, [Colaborador](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#contributor), ou funções de [Colaborador de rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) e, em seguida, atribua a função para o escopo apropriado. Se você deseja atribuir permissões específicas para um subconjunto dos recursos de rede virtual, crie uma [função personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e atribua as permissões específicas necessárias para [redes virtuais](manage-virtual-network.md#permissions), [ pontos de extremidade de serviço e sub-redes](virtual-network-manage-subnet.md#permissions), [interfaces de rede](virtual-network-network-interface.md#permissions), [emparelhamento](virtual-network-manage-peering.md#permissions), [grupos de segurança de rede e do aplicativo](manage-network-security-group.md#permissions), ou [tabelas de rota](manage-route-table.md#permissions) à função.

## <a name="policy"></a>Política

O Azure Policy habilita que você crie, atribua ou gerencie definições de política. As definições de políticas impõem regras diferentes sobre os recursos, portanto, os recursos permanecem em conformidade com os padrões organizacionais e contratos de nível de serviço. O Azure Policy executa uma avaliação dos recursos, verificando os recursos que não estão em conformidade com as definições de política que você tem. Por exemplo, é possível definir e aplicar uma política que permita a criação de redes virtuais em apenas um grupo ou região de recursos específicos. Outra política pode exigir que cada sub-rede tenha um grupo de segurança de rede associado a ela. As políticas então são avaliadas durante a criação e a atualização de recursos.

As políticas são aplicadas para a hierarquia a seguir: Assinatura, grupo de gerenciamento e grupo de recursos. Saiba mais sobre os modelos da [política do Azure](../governance/policy/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou implantar uma rede virtual [modelo de política](policy-samples.md).

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre todas as tarefas, configurações e opções para uma [rede virtual](manage-virtual-network.md), [sub-rede e ponto de extremidade de serviço](virtual-network-manage-subnet.md), [adaptador de rede](virtual-network-network-interface.md), [emparelhamento](virtual-network-manage-peering.md),  [grupo de segurança de aplicativo e rede](manage-network-security-group.md) ou [tabelas de rotas](manage-route-table.md).
