---
title: Perguntas frequentes sobre a Rede Virtual do Azure | Microsoft Docs
description: Respostas para as perguntas mais frequentes sobre as redes virtuais do Microsoft Azure.
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
ms.assetid: 54bee086-a8a5-4312-9866-19a1fba913d0
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/18/2017
ms.author: jdial
ms.openlocfilehash: c71e188b74ebfd9420f840957e83190cf476b584
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-virtual-network-frequently-asked-questions-faq"></a>Perguntas frequentes sobre a rede virtual do Azure (FAQ)

## <a name="virtual-network-basics"></a>Noções básicas sobre redes virtuais

### <a name="what-is-an-azure-virtual-network-vnet"></a>O que é uma Rede Virtual (VNet) do Azure?
Uma Rede Virtual do Azure (VNet) é uma representação da sua própria rede na nuvem. É um isolamento lógico da nuvem do Azure dedicada à sua assinatura. Você pode usar redes virtuais para provisionar e gerenciar VPNs (redes virtuais privadas) no Azure e, se desejar, vincular as redes virtuais no Azure a outras redes virtuais no Azure ou à sua infraestrutura de TI local para criar soluções híbridas e entre locais. Cada VNet que você cria tem seu próprio bloco CIDR e pode ser vinculada a outras redes virtuais e redes locais, desde que os blocos CIDR não se sobreponham. Você também controla as configurações do servidor DNS para redes virtuais e a segmentação da VNet em sub-redes.

Use redes virtuais para:

* Crie uma VNet dedicada apenas para nuvem privada Às vezes, você não precisa de uma configuração entre locais para sua solução. Quando você cria uma redes virtuais, seus serviços e VMs na rede virtual podem se comunicar de forma direta e segura entre si na nuvem. Isso mantém o tráfego seguramente na rede virtual, mas ainda permite que você configure conexões de ponto de extremidade para as VMs e os serviços que exigem comunicação com a Internet como parte de sua solução.
* Expanda seu data center com segurança Com as redes virtuais, você pode criar VPNs site a site (S2S) tradicionais para escalar com segurança a capacidade do data center. As VPNs S2S usam IPSEC para fornecer uma conexão segura entre o gateway de VPN corporativo e o Azure.
* Habilite cenários de nuvem híbrida As VNets proporcionam flexibilidade para dar suporte a uma variedade de cenários de nuvem híbrida. É possível conectar com segurança aplicativos baseados em nuvem a qualquer tipo de sistema local, como mainframes e sistemas Unix.

### <a name="how-do-i-know-if-i-need-a-vnet"></a>Como saber se eu preciso de uma VNet?
O artigo [Visão geral da Rede Virtual](virtual-networks-overview.md) fornece uma tabela de decisão que ajudará a escolher a melhor opção de design de rede para você.

### <a name="how-do-i-get-started"></a>Como começar?
Acesse a [documentação da Rede Virtual](https://docs.microsoft.com/azure/virtual-network/) para saber como começar a usar. Este conteúdo fornece uma visão geral e informações de implantação para todos os recursos de rede virtual.

### <a name="can-i-use-vnets-without-cross-premises-connectivity"></a>Posso usar redes virtuais sem conectividade entre locais?
Sim. É possível usar uma rede virtual sem usar conectividade híbrida. Isso será especialmente útil se você quiser executar controladores de domínio do Active Directory do Microsoft Windows Server.

### <a name="can-i-perform-wan-optimization-between-vnets-or-a-vnet-and-my-on-premises-data-center"></a>Posso realizar uma otimização de WAN entre redes virtuais ou entre uma rede virtual e meu data center local?

Sim. Você pode implantar um [dispositivo virtual de rede de otimização de WAN](https://azure.microsoft.com/marketplace/?term=wan+optimization) de vários fornecedores por meio do Marketplace do Azure.

## <a name="configuration"></a>Configuração

### <a name="what-tools-do-i-use-to-create-a-vnet"></a>Quais ferramentas eu uso para criar uma redes virtuais?
Você pode usar as seguintes ferramentas para criar ou configurar uma rede virtual:

* Portal do Azure (para Redes Virtuais clássicas e do Gerenciador de Recursos).
* Um arquivo de configuração de rede (netcfg - somente para Redes Virtuais clássicas). Consulte o artigo [Configurar uma VNet usando um arquivo de configuração de rede](virtual-networks-using-network-configuration-file.md).
* PowerShell (para Redes Virtuais clássicas e do Gerenciador de Recursos).
* CLI do Azure (para Redes Virtuais clássicas e do Gerenciador de Recursos).

### <a name="what-address-ranges-can-i-use-in-my-vnets"></a>Quais intervalos de endereço posso usar em minhas redes virtuais?
Qualquer intervalo de endereços IP definido na [RFC 1918](http://tools.ietf.org/html/rfc1918). Por exemplo, 10.0.0.0/16.

### <a name="can-i-have-public-ip-addresses-in-my-vnets"></a>Posso ter endereços IP públicos em minhas redes virtuais?
Sim. Para saber mais sobre intervalos de endereços IP públicos, consulte o artigo [Espaço de endereço IP público em uma rede virtual](virtual-networks-public-ip-within-vnet.md). Seus endereços IP públicos não estarão diretamente acessíveis na Internet.

### <a name="is-there-a-limit-to-the-number-of-subnets-in-my-vnet"></a>Há um limite para o número de sub-redes na minha VNet?
Sim. Leia o artigo [Limites do Azure](../azure-subscription-service-limits.md#networking-limits) para obter detalhes. Espaços de endereço de sub-rede não podem se sobrepor.

### <a name="are-there-any-restrictions-on-using-ip-addresses-within-these-subnets"></a>Existem restrições quanto ao uso de endereços IP dentro dessas sub-redes?
Sim. O Azure reserva alguns endereços IP em cada sub-rede. O primeiro e o último endereço IP das sub-redes são reservados para conformidade de protocolo, juntamente com três ou mais endereços usados para os serviços do Azure.

### <a name="how-small-and-how-large-can-vnets-and-subnets-be"></a>Que tamanho, máximo e mínimo, as redes virtuais e sub-redes podem ter?
A menor sub-rede à qual oferecemos suporte é de /29 e a maior é de /8 (usando definições da sub-rede CIDR).

### <a name="can-i-bring-my-vlans-to-azure-using-vnets"></a>Posso levar minhas VLANs para o Azure usando redes virtuais?
Não. As v são sobreposições da Camada 3. O Azure não oferece suporte a nenhuma semântica da Camada 2.

### <a name="can-i-specify-custom-routing-policies-on-my-vnets-and-subnets"></a>Posso especificar políticas de roteamento personalizadas nas minhas redes virtuais e sub-redes?
Sim. Você pode usar o UDR (Roteamento Definido pelo Usuário). Para saber mais sobre UDR, visite [Rotas definidas pelo usuário e Encaminhamento IP](virtual-networks-udr-overview.md).

### <a name="do-vnets-support-multicast-or-broadcast"></a>As redes virtuais oferecem suporte ao multicast ou à difusão?
Não. Não há suporte para nenhum dos dois.

### <a name="what-protocols-can-i-use-within-vnets"></a>Quais protocolos posso usar nas redes virtuais?
Você pode usar protocolos TCP, UDP e ICMP TCP/IP em redes virtuais. Pacotes encapsulados de IP em IP, multicast, difusão e pacotes de Encapsulamento de Roteamento Genérico (GRE) são bloqueados nas redes virtuais. 

### <a name="can-i-ping-my-default-routers-within-a-vnet"></a>Posso executar ping em meus roteadores padrão em uma rede virtual?
Não.

### <a name="can-i-use-tracert-to-diagnose-connectivity"></a>Posso usar tracert para diagnosticar a conectividade?
Não.

### <a name="can-i-add-subnets-after-the-vnet-is-created"></a>Posso adicionar sub-redes depois que a rede virtual é criada?
Sim. As sub-redes podem ser adicionadas às redes virtuais a qualquer momento, desde que o endereço da sub-rede não faça parte de outra sub-rede na rede virtual.

### <a name="can-i-modify-the-size-of-my-subnet-after-i-create-it"></a>Posso modificar o tamanho da minha sub-rede depois de criá-la?
Sim. Você poderá adicionar, remover, expandir ou reduzir uma sub-rede se não houver VMs ou serviços implantados nela.

### <a name="can-i-modify-subnets-after-i-created-them"></a>Posso modificar sub-redes depois de criá-las?
Sim. Você pode adicionar, remover e modificar os blocos CIDR usados por uma rede virtual.

### <a name="can-i-connect-to-the-internet-if-i-am-running-my-services-in-a-vnet"></a>Posso me conectar à Internet se eu estiver executando meus serviços em uma rede virtual?
Sim. Todos os serviços implantados em uma rede virtual podem se conectar à internet. Cada serviço de nuvem implantado no Azure tem um VIP publicamente endereçável atribuído a ele. Você precisará definir pontos de extremidade de entrada para funções de PaaS e pontos de extremidade de máquinas virtuais a fim de habilitar esses serviços para aceitar conexões da internet.

### <a name="do-vnets-support-ipv6"></a>As redes virtuais oferecem suporte ao IPv6?
Não. Não é possível usar o IPv6 com redes virtuais neste momento.

### <a name="can-a-vnet-span-regions"></a>Uma rede virtual pode abranger regiões?
Não. A rede virtual é limitada a uma única região.

### <a name="can-i-connect-a-vnet-to-another-vnet-in-azure"></a>Posso conectar uma rede virtual a outra rede virtual no Azure?
Sim. Você pode conectar uma rede virtual a outra usando:
- um Gateway de VPN do Azure. Leia o artigo [Configurar uma conexão de rede virtual a rede virtual ](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) para obter detalhes. 
- Emparelhamento VNet. Leia o artigo [Visão geral do emparelhamento VNet](virtual-network-peering-overview.md) para obter detalhes.

## <a name="name-resolution-dns"></a>Resolução de nomes (DNS)

### <a name="what-are-my-dns-options-for-vnets"></a>Quais são minhas opções DNS para redes virtuais?
Use a tabela de decisão na página [Resolução de nome para VMs e instâncias de função](virtual-networks-name-resolution-for-vms-and-role-instances.md) , que orientará você por todas as opções DNS disponíveis.

### <a name="can-i-specify-dns-servers-for-a-vnet"></a>Posso especificar servidores DNS para uma rede virtual?
Sim. Você pode especificar endereços IP do servidor DNS nas configurações da rede virtual. Isso será aplicado como o servidor DNS padrão a todas as VMs na rede virtual.

### <a name="how-many-dns-servers-can-i-specify"></a>Quantos servidores DNS posso especificar?
Consulte o artigo [Limites do Azure](../azure-subscription-service-limits.md#networking-limits) para obter detalhes.

### <a name="can-i-modify-my-dns-servers-after-i-have-created-the-network"></a>Posso modificar meus servidores DNS depois de ter criado a rede?
Sim. Você pode alterar a lista de servidores DNS para sua rede virtual a qualquer momento. Se você alterar sua lista de servidores DNS, será preciso reiniciar cada uma das VMs em sua rede virtual para que elas selecionem o novo servidor DNS.

### <a name="what-is-azure-provided-dns-and-does-it-work-with-vnets"></a>O que é o DNS fornecido pelo Azure? Ele funciona com redes virtuais?
O DNS fornecido pelo Azure é um serviço DNS multilocatário oferecido pela Microsoft. O Azure registra todas as VMs e instâncias de função do serviço de nuvem nesse serviço. Esse serviço fornece resolução de nome por nome de host para VMs e instâncias de função contidas no mesmo serviço de nuvem e por FQDN para VMs e instâncias de função na mesma rede virtual. Leia o artigo [Resolução de nomes para VMs e instâncias de função](virtual-networks-name-resolution-for-vms-and-role-instances.md) para saber mais sobre DNS.

> [!NOTE]
> No momento, existe uma limitação para os primeiros 100 serviços de nuvem em uma rede virtual para resolução de nome entre locatários usando DNS fornecido pelo Azure. Se você estiver usando seu próprio servidor DNS, essa limitação não se aplicará.
> 
> 

### <a name="can-i-override-my-dns-settings-on-a-per-vm--service-basis"></a>Posso substituir minhas configurações DNS por VM/serviço?
Sim. Você pode definir servidores DNS por serviço de nuvem para substituir as configurações de rede padrão. No entanto, é recomendável usar DNS para toda a rede, tanto quanto possível.

### <a name="can-i-bring-my-own-dns-suffix"></a>Posso colocar meu próprio sufixo DNS?
Não. Você não pode especificar um sufixo DNS personalizado para suas redes virtuais.

## <a name="connecting-virtual-machines"></a>Conexão de máquinas virtuais

### <a name="can-i-deploy-vms-to-a-vnet"></a>Posso implantar VMs em uma rede virtual?
Sim. Todas as interfaces de rede (NIC) anexadas a uma VM implantada por meio do modelo de implantação do Gerenciador de Recursos devem estar conectadas a uma rede virtual. VMs implantadas por meio do modelo de implantação clássico podem ser conectadas a uma rede virtual.

### <a name="what-are-the-different-types-of-ip-addresses-i-can-assign-to-vms"></a>Quais são os diferentes tipos de endereços IP que posso atribuir a VMs?
* **Privado:** atribuído a cada NIC em cada VM. O endereço é atribuído usando o método estático ou dinâmico. Os endereços IP privados são atribuídos do intervalo que você especificou nas configurações de sub-rede da VNet. Recursos implantados por meio do modelo de implantação clássico recebem endereços IP privados mesmo que não estejam conectados a uma VNet. Um endereço IP privado atribuído com o método dinâmico permanece atribuído a um recurso até que este seja excluído (slots de implantação de Serviço de Nuvem ou VMs). Um endereço IP privado atribuído com o método dinâmico pode mudar quando uma VM for reiniciada depois de ter ficado no estado parado (desalocado). Um endereço IP privado atribuído com o método estático permanece atribuído a um recurso até que o recurso seja excluído. Se você precisar garantir que o endereço IP privado de um recurso nunca mude até o recurso ser excluído, atribua um endereço IP privado com o método estático.
* **Público:** opcionalmente atribuído a NICs anexadas a VMs implantadas por meio do modelo de implantação do Azure Resource Manager. O endereço pode ser atribuído com o método de alocação estática ou dinâmica. Todas as instâncias de função de VMs e Serviços de Nuvem implantadas por meio do modelo de implantação clássico existem dentro de um serviço de nuvem, ao qual é atribuído um endereço IP endereço virtual IP (VIP) *dinâmico* e público. Um endereço IP público e *estático*, chamado de [endereço IP Reservado](virtual-networks-reserved-public-ip.md), pode ser atribuído como VIP. Você pode atribuir endereços IP públicos a instâncias de função de VMs ou serviços de nuvem individuais implantados por meio do modelo de implantação clássico. Eles são chamados de [endereços IP públicos em nível de instância (ILPIP)](virtual-networks-instance-level-public-ip.md) e podem ser atribuídos dinamicamente.

### <a name="can-i-reserve-a-private-ip-address-for-a-vm-that-i-will-create-at-a-later-time"></a>Posso reservar um endereço IP privado para uma VM que vou criar mais tarde?
Não. Não é possível reservar um endereço IP privado. Se um endereço IP privado estiver disponível, ele será atribuído a uma VM ou instância de função pelo servidor DHCP. Essa VM pode ou não ser aquela à qual você deseja que o endereço IP privado seja atribuído. No entanto, você pode alterar o endereço IP privado de uma VM já criada para qualquer endereço IP privado disponível.

### <a name="do-private-ip-addresses-change-for-vms-in-a-vnet"></a>Os endereços IP privado mudam para VMs em uma rede virtual?
Isso depende. Endereços IP privados dinâmicos permanecem com uma máquina virtual que ela seja interrompida (desalocada) ou excluída. Endereços IP privados estáticos só são liberados de uma VM quando esta é excluída.

### <a name="can-i-manually-assign-ip-addresses-to-nics-within-the-vm-operating-system"></a>Posso atribuir endereços IP a NICs manualmente dentro do sistema operacional da VM?
Sim, mas não é recomendável. Alterar manualmente o endereço IP de uma NIC no sistema operacional de uma VM pode levar à perda de conectividade para a VM se o endereço IP atribuído a uma NIC na VM do Azure for alterado.

### <a name="what-happens-to-my-ip-addresses-if-i-stop-a-cloud-service-deployment-slot-or-shutdown-a-vm-from-within-the-operating-system"></a>O que acontece aos meus endereços IP se eu interromper o slot de implantação de um Serviço de Nuvem ou desligar uma VM de dentro do sistema operacional?
Nada. Os endereços IP (VIP público, público e privado) permanecem atribuídos ao slot de implantação do Serviço de Nuvem ou à VM. Os endereços dinâmicos só são liberados se uma VM for interrompida (desalocada) ou excluída ou se um slot de implantação do serviço de nuvem for excluído. Clicar no botão **Interromper** para uma VM no portal do Azure define o estado desta como interrompido (desalocado). Nesse caso, a VM perderá os endereços IP.

### <a name="can-i-move-vms-from-one-subnet-to-another-subnet-in-a-vnet-without-re-deploying"></a>Posso mover VMs de uma sub-rede para outra em uma rede virtual sem reimplantação?
Sim. Você pode encontrar mais informações no artigo [Como mover uma VM ou instância de função para uma sub-rede diferente](virtual-networks-move-vm-role-to-subnet.md).

### <a name="can-i-configure-a-static-mac-address-for-my-vm"></a>Posso configurar um endereço MAC estático para minha VM?
Não. Um endereço MAC não pode ser configurado estaticamente.

### <a name="will-the-mac-address-remain-the-same-for-my-vm-once-it-has-been-created"></a>O endereço MAC permanecerá o mesmo para minha VM depois de criado?
Sim, o endereço MAC continua o mesmo para uma VM implantada por meio do Gerenciador de Recursos e dos modelos de implantação clássicos até que esta seja excluída. Anteriormente, o endereço MAC era lançado se a máquina virtual fosse interrompida (desalocada), mas agora o endereço MAC é mantido mesmo quando a VM está no estado desalocado.

### <a name="can-i-connect-to-the-internet-from-a-vm-in-a-vnet"></a>Posso me conectar à internet de uma VM em uma rede virtual?
Sim. Todas as instâncias de função de VMs e Serviços de Nuvem implantados em uma rede virtual podem se conectar à internet.

## <a name="azure-services-that-connect-to-vnets"></a>Serviços do Azure que se conectam a redes virtuais

### <a name="can-i-use-azure-app-service-web-apps-with-a-vnet"></a>Posso usar os Aplicativos Web do Serviço de Aplicativo do Azure em uma rede virtual?
Sim. Você pode implantar Aplicativos Web em uma rede virtual usando o ASE (Ambiente do Serviço de Aplicativo). Todos os Aplicativos Web poderão se conectar e acessar os recursos na Rede Virtual do Azure se você tiver configurado uma conexão ponto a site para sua rede virtual. Para obter mais informações, consulte os seguintes artigos:

* [Criando Aplicativos Web em um Ambiente do Serviço de Aplicativo](../app-service/environment/app-service-web-how-to-create-a-web-app-in-an-ase.md)
* [Integrar seu aplicativo a uma Rede Virtual do Azure](../app-service/web-sites-integrate-with-vnet.md)
* [Usando conexões híbridas e integração de rede virtual com Aplicativos Web](../app-service/web-sites-integrate-with-vnet.md#hybrid-connections-and-app-service-environments)

### <a name="can-i-deploy-cloud-services-with-web-and-worker-roles-paas-in-a-vnet"></a>Posso implantar Serviços de Nuvem com funções web e de trabalho (PaaS) em uma rede virtual?
Sim. Você pode implantar instâncias de função de Serviços de Nuvem em redes virtuais. Para fazer isso, especifique o nome e os mapeamentos de função/sub-rede na seção de configuração de rede da sua configuração de serviço. Não é preciso atualizar nenhum dos binários.

### <a name="can-i-connect-a-virtual-machine-scale-set-vmss-to-a-vnet"></a>Posso conectar um Conjunto de Dimensionamento de Máquinas Virtuais (VMSS) a uma rede virtual?
Sim. Você precisa conectar um VMSS a uma rede virtual.

### <a name="can-i-move-my-services-in-and-out-of-vnets"></a>Posso mover meus serviços para dentro e fora das redes virtuais?
Não. Não é possível mover serviços para dentro e fora das redes virtuais. Será preciso excluir e reimplantar o serviço para movê-lo para outra rede virtual.

## <a name="security"></a>Segurança

### <a name="what-is-the-security-model-for-vnets"></a>Qual é o modelo de segurança para redes virtuais?
As redes virtuais são completamente isoladas umas das outras e de outros serviços hospedados na infraestrutura do Azure. Uma rede virtual é um limite de confiança.

### <a name="can-i-restrict-inbound-or-outbound-traffic-flow-to-vnet-connected-resources"></a>Posso restringir o fluxo de tráfego de entrada ou saída para recursos conectados à rede virtual?
Sim. Você pode aplicar [Grupos de Segurança de Rede](virtual-networks-nsg.md) a sub-redes individuais em uma rede virtual, NICs conectados a uma rede virtual ou ambos.

### <a name="can-i-implement-a-firewall-between-vnet-connected-resources"></a>Posso implementar um firewall entre os recursos conectados à rede virtual?
Sim. Você pode implantar [dispositivos virtuais de rede de firewall](https://azure.microsoft.com/en-us/marketplace/?term=firewall) de vários fornecedores por meio do Marketplace do Azure.

### <a name="is-there-information-available-about-securing-vnets"></a>Existem informações disponíveis sobre como proteger redes virtuais?
Sim. Consulte o artigo [Visão geral da segurança de rede do Azure](../security/security-network-overview.md) para obter detalhes.

## <a name="apis-schemas-and-tools"></a>APIs, esquemas e ferramentas

### <a name="can-i-manage-vnets-from-code"></a>Posso gerenciar redes virtuais usando código?
Sim. Você pode usar APIs REST para redes virtuais nos modelos de implantação do [Azure Resource Manager](https://msdn.microsoft.com/library/mt163658.aspx) e [clássico (Gerenciamento de Serviço)](http://go.microsoft.com/fwlink/?LinkId=296833)).

### <a name="is-there-tooling-support-for-vnets"></a>Há suporte a ferramentas para redes virtuais?
Sim. Saiba mais sobre como usar:
- O Portal do Azure para implantar redes virtuais por meio dos modelos de implantação do [Azure Resource Manager](virtual-networks-create-vnet-arm-pportal.md) e [clássico](virtual-networks-create-vnet-classic-pportal.md).
- O PowerShell para gerenciar redes virtuais implantadas por meio dos modelos de implantação do [Gerenciador de Recursos](/powershell/resourcemanager/azurerm.network/v3.1.0/azurerm.network.md) e [clássico](/powershell/module/azure/?view=azuresmps-3.7.0).
- A [interface de linha de comando (CLI) do Azure](../virtual-machines/azure-cli-arm-commands.md#azure-network-commands-to-manage-network-resources) para gerenciar redes virtuais implantadas por meio de ambos os modelos de implantação.  
