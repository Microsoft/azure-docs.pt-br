---
title: Rede Virtual do Azure | Microsoft Docs
description: Saiba mais sobre os conceitos e recursos da Rede Virtual do Azure.
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 9633de4b-a867-4ddf-be3c-a332edf02e24
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/23/2017
ms.author: jdial
ms.openlocfilehash: 6d6afd2b9b956138ed400fbd6cabd3b480fde0f0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-virtual-network"></a>Rede Virtual do Azure

O serviço de Rede Virtual do Azure permite que você conecte com segurança os recursos do Azure usando redes virtuais (VNets). Uma VNet é uma representação da sua própria rede na nuvem. Uma VNet é um isolamento lógico da nuvem do Azure dedicada à sua assinatura. Você também pode conectar VNets à sua rede local. A figura a seguir mostra algumas funcionalidades do serviço de Rede Virtual do Azure:

![Diagrama de rede](./media/virtual-networks-overview/virtual-network-overview.png)

Para saber mais sobre as seguintes funcionalidades da Rede Virtual do Azure, clique na funcionalidade:
- **[Isolamento:](#isolation)** as VNets são isoladas umas das outras. Crie VNets separadas para desenvolvimento, teste e produção que usem os mesmos blocos de endereços CIDR. Por outro lado, você pode criar várias VNets que usam blocos de endereço CIDR diferentes e conectam as redes. Você pode segmentar uma VNet em várias sub-redes. O Azure fornece resolução de nome interna para instâncias de função de VMs e Serviços de Nuvem conectadas a uma rede virtual. Opcionalmente, configure uma VNet para usar seus próprios servidores DNS, em vez de usar a resolução de nome interna do Azure.
- **[Conectividade com a Internet:](#internet)** Por padrão, todas as instâncias de função de VM (máquina virtual) e Serviços de Nuvem do Azure conectadas a uma VNet têm acesso à Internet. Você também pode habilitar o acesso de entrada a recursos específicos, conforme o necessário.
- **[Conectividade de recursos do Azure:](#within-vnet)** Recursos do Azure como Serviços de Nuvem e VMs podem ser conectados à mesma VNet. Os recursos podem se conectar usando endereços IP privados, mesmo se estiverem em sub-redes diferentes. O Azure fornece roteamento padrão entre sub-redes, VNets e redes locais, portanto você não precisa configurar e gerenciar rotas.
- **[Conectividade de VNet:](#connect-vnets)** as VNets podem ser conectadas entre si, permitindo que os recursos conectados a qualquer VNet se comuniquem com recursos de outra VNet.
- **[Conectividade local:](#connect-on-premises)** as VNets podem ser conectadas a redes locais por meio de conexões de rede privada entre sua rede e o Azure, ou por uma conexão de VPN site a site pela Internet.
- **[Filtragem de tráfego:](#filtering)** o tráfego de rede de instâncias de função de VMs e do Serviços de Nuvem pode ser filtrado na entrada e na saída pelo endereço IP e porta de origem, endereço IP e porta de destino e protocolo.
- **[Roteamento:](#routing)** opcionalmente, você pode substituir o roteamento padrão do Azure configurando suas próprias rotas ou usando rotas BGP por meio de um gateway de rede.

## <a name = "isolation"></a>Isolamento e segmentação de rede

É possível implementar várias VNets dentro de cada [assinatura](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) e [região](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#region) do Azure. Cada VNet é isolada de outras VNets. Para cada VNet, você pode:
- Especificar um espaço de endereço IP privado personalizado usando endereços públicos e privados (RFC 1918). O Azure atribui aos recursos conectados à VNet um endereço IP privado do espaço de endereço que você atribuiu.
- Segmentar a VNet em uma ou mais sub-redes e alocar uma parte do espaço de endereço da VNet a cada sub-rede.
- Usar a resolução de nomes fornecida pelo Azure ou especificar seu próprio servidor DNS que será usado pelos recursos conectados a uma VNet. Para saber mais sobre a resolução de nome em uma VNet, leia o artigo [Resolução de nomes para VMs e Serviços de Nuvem](virtual-networks-name-resolution-for-vms-and-role-instances.md).

## <a name = "internet"></a>Conectar-se à Internet
Por padrão, todos os recursos conectados a uma VNet têm conectividade de saída para a Internet. O endereço IP privado do recurso passa por SNAT (conversão do endereço de rede de origem) para um endereço IP público pela infraestrutura do Azure. Para saber mais sobre conectividade de saída na Internet, leia o artigo [Noções básicas das conexões de saída no Azure](..\load-balancer\load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json#standalone-vm-with-no-instance-level-public-ip-address). Altere a conectividade padrão implementando o roteamento e a filtragem de tráfego personalizado.

Para comunicar-se internamente com os recursos do Azure da Internet, ou para comunicar-se externamente com a Internet sem SNAT, é necessário atribuir um endereço IP público ao recurso. Para saber mais sobre endereços IP públicos, leia o artigo [Endereços IP públicos](virtual-network-public-ip-address.md).

## <a name="within-vnet"></a>Conectar recursos do Azure
Você pode conectar vários recursos do Azure a uma VNet, como VMs (máquinas virtuais), Serviços de Nuvem, Ambientes de Serviço de Aplicativo e Conjuntos de dimensionamento de máquinas virtuais. As VMs se conectam a uma sub-rede dentro de uma VNet por meio de uma NIC (interface de rede). Para saber mais sobre NICs, leia o artigo [Interfaces de rede](virtual-network-network-interface.md).

## <a name="connect-vnets"></a>Conectar redes virtuais

Você pode conectar as VNets entre si, permitindo que os recursos conectados a qualquer VNet se comuniquem entre si através das VNets. Use uma ou as duas opções a seguir para conectar VNets entre si:
- **Emparelhamento:** permite que os recursos conectados a diferentes VNets do Azure no mesmo local do Azure se comuniquem entre si. A largura de banda e a latência entre as VNets são as mesmas se os recursos estivessem conectados à mesma VNet. Para saber mais sobre emparelhamento, leia o artigo [Emparelhamento de rede virtual](virtual-network-peering-overview.md).
- **Conexão de VNet para VNet:** permite recursos conectados a uma VNet do Azure diferente dentro dos mesmos locais ou em locais diferentes do Azure. Ao contrário do emparelhamento, a largura de banda é limitada entre as VNets, pois o tráfego deve fluir por um Gateway de VPN do Azure. Para saber mais sobre como conectar VNets a uma conexão VNet para VNet, leia o artigo [Configurar uma conexão de VNet para VNet](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="connect-on-premises"></a>Conectar-se a uma rede local

Você pode conectar sua rede local a uma VNet usando qualquer combinação das seguintes opções:
- **VPN (rede virtual privada) de ponto a site:** estabelecida entre um único PC conectado à sua rede e a VNet. Esse tipo de conexão é ótimo se você estiver começando a usar o Azure, ou para os desenvolvedores, pois exige pouca ou nenhuma alteração em sua rede existente. A conexão usa o protocolo SSTP para fornecer comunicação criptografada pela Internet entre o PC e a VNet. A latência de uma VPN ponto a site é imprevisível, pois o tráfego atravessa a Internet.
- **VPN site a site:** estabelecida entre o dispositivo VPN e um Gateway de VPN do Azure. Esse tipo de conexão permite que qualquer recurso local autorizado acesse uma VNet. A conexão é uma VPN IPSec/IKE que fornece comunicação criptografada pela Internet entre o dispositivo local e o Gateway de VPN do Azure. A latência de uma conexão site a site é imprevisível, pois o tráfego atravessa a Internet.
- **Azure ExpressRoute:** estabelecida entre sua rede e o Azure, por meio de um parceiro de ExpressRoute. Essa conexão é privada. O tráfego não atravessa a Internet. A latência de uma conexão de ExpressRoute é previsível, pois o tráfego não atravessa a Internet.

Para saber mais sobre todas as opções de conexão anteriores, leia o artigo [Diagramas de topologia de conexão](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#diagrams).

## <a name="filtering"></a>Filtrar o tráfego de rede
Filtre o tráfego de rede entre sub-redes usando uma ou as duas opções a seguir:
- **NSG (Grupos de segurança de rede):** cada NSG pode conter várias regras de segurança de entrada e saída que permitem a filtragem do tráfego por endereço IP de origem e de destino, porta e protocolo. Aplique um NSG a cada NIC em uma VM. Você também pode aplicar um NSG à sub-rede a qual uma NIC, ou outros recursos do Azure, está conectada. Leia o artigo [Grupos de segurança de rede](virtual-networks-nsg.md) para saber mais sobre NSGs.
- **NVA (Solução de virtualização de rede):** NVA um é uma VM que executa um software responsável por uma função de rede, como um firewall. Veja uma lista de NVAs disponíveis no [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances). Também há NVAs que fornecem otimização de WAN e outras funções de tráfego de rede. As NVAs são usadas normalmente com rotas BGP ou definidas pelo usuário. Você também pode usar uma NVA para filtrar o tráfego entre VNets.

## <a name="routing"></a>Rotear o tráfego de rede

Por padrão, o Azure cria tabelas de rotas que permitem aos recursos conectados a qualquer sub-rede e em qualquer VNet a comunicação mútua. Você pode implementar uma ou as duas opções a seguir para substituir as rotas padrão criadas pelo Azure:
- **Rotas definidas pelo usuário:** você pode criar tabelas de rotas personalizadas com rotas que controlam para onde o tráfego será roteado em cada sub-rede. Para saber mais sobre as rotas definidas pelo usuário, leia o artigo [Rotas definidas pelo usuário](virtual-networks-udr-overview.md).
- **Rotas BGP:** se você conectar sua VNet à rede local usando um Gateway de VPN do Azure ou conexão de ExpressRoute, poderá propagar as rotas BGP às suas VNets.

## <a name="pricing"></a>Preços

Não há cobrança pelas redes virtuais, sub-redes, tabelas de rotas ou grupos de segurança de rede. Uso de largura de banda de Internet de saída, endereços IP públicos, emparelhamento de rede virtual, Gateways de VPN e ExpressRoute têm suas próprias estruturas de preços. Veja as páginas de preço de [Rede virtual](https://azure.microsoft.com/pricing/details/virtual-network), [Gateway de VPN](https://azure.microsoft.com/pricing/details/vpn-gateway) e [ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute) para saber mais.

## <a name="faq"></a>Perguntas frequentes

Para revisar as perguntas frequentes sobre Rede Virtual, consulte o artigo [Perguntas frequentes sobre a Rede Virtual](virtual-networks-faq.md).


## <a name="next-steps"></a>Próximas etapas

- Criar sua primeira VNet e conecte algumas VMs a ela executando as etapas no artigo [Criar sua primeira rede virtual](virtual-network-get-started-vnet-subnet.md).
- Criar uma conexão ponto a site a uma VNet executando as etapas no artigo [Configurar uma conexão ponto a site](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Saiba mais sobre alguns dos outros principais [recursos de rede](../networking/networking-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) do Azure.
