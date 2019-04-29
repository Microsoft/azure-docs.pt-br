---
title: Conectar-se a sua rede local para o Azure | Microsoft Docs
description: Explica e compara os métodos diferentes disponíveis para se conectar a serviços de nuvem da Microsoft, como Azure, Office 365 e Dynamics CRM Online.
services: ''
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
tags: ''
ms.assetid: 294d39ad-40e0-476a-a362-57911b1172b7
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/25/2016
ms.author: cherylmc
ms.openlocfilehash: 9361c0b2ee84d7459bd432f5a8ce13fc5fd85ef8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60583465"
---
# <a name="connecting-your-on-premises-network-to-azure"></a>Conectar-se a sua rede local para o Azure
A Microsoft fornece vários tipos de serviços de nuvem. Enquanto você pode se conectar a todos os serviços na Internet pública, você também pode se conectar a alguns dos serviços usando um túnel de rede virtual privada (VPN) pela Internet ou por uma conexão direta e privada para a Microsoft. Este artigo ajuda você a determinar qual opção de conectividade melhor atenderá às suas necessidades com base nos tipos de serviços de nuvem da Microsoft que você consumir. A maioria das organizações utilizam vários tipos de conexão descritos abaixo.

## <a name="connecting-over-the-public-internet"></a>Conectar-se pela Internet pública
Esse tipo de conexão fornece acesso aos serviços de nuvem da Microsoft diretamente pela Internet, conforme mostrado abaixo.

![Internet](./media/guidance-connecting-your-on-premises-network-to-azure/internet.png "Internet")

Normalmente, essa conexão é o primeiro tipo usado para conectar-se aos serviços de nuvem da Microsoft. A tabela a seguir lista os prós e contras desse tipo de conexão.

| **Benefícios** | **Considerações** |
| --- | --- |
| Não requer modificação à sua rede local desde que todos os dispositivos cliente têm acesso ilimitado a todos os endereços IP e portas na Internet. |Embora o tráfego geralmente é criptografado usando HTTPS, ele pode ser interceptado em trânsito, pois ele atravessa a Internet pública. |
| Pode se conectar a todos os serviços de nuvem da Microsoft expostos à Internet pública. |Latência imprevisível porque a conexão atravessa a Internet. |
| Usa sua conexão de Internet existente. | |
| Não requer gerenciamento de dispositivos de qualquer conectividade. | |

Essa conexão não tem conectividade ou custos de largura de banda desde que você use sua conexão de Internet existente.

## <a name="connecting-with-a-point-to-site-connection"></a>Conectar-se com uma conexão ponto a site
Esse tipo de conexão fornece acesso a alguns serviços de nuvem da Microsoft por meio de um encapsulamento SSTP Secure Socket Tunneling Protocol () pela Internet, conforme mostrado abaixo.

![P2S](./media/guidance-connecting-your-on-premises-network-to-azure/p2s.png "conexãoPoint-to-site")

A conexão é feita pela sua conexão de Internet existente, mas requer o uso de um Gateway de VPN do Azure. A tabela a seguir lista os prós e contras desse tipo de conexão.

| **Benefícios** | **Considerações** |
| --- | --- |
| Não requer modificação à sua rede local desde que todos os dispositivos cliente têm acesso ilimitado a todos os endereços IP e portas na Internet. |Embora o tráfego é criptografado usando o IPSec, ele pode ser interceptado em trânsito, pois ele atravessa a Internet pública. |
| Usa sua conexão de Internet existente. |Latência imprevisível porque a conexão atravessa a Internet. |
| Taxa de transferência de até 200 Mb/s por gateway. |Requer a criação e gerenciamento de conexões separadas entre cada dispositivo em sua rede local e cada gateway que precisa se conectar a cada dispositivo. |
| Pode ser usado para se conectar aos serviços do Azure que podem ser conectados a redes virtuais do Azure (VNet), como máquinas virtuais do Azure e serviços de nuvem do Azure. |Requer administração mínima de em andamento de um Gateway de VPN do Azure. |
| Não pode ser usado para se conectar ao Microsoft Office 365 ou Dynamics CRM Online. | |
| Não pode ser usado para se conectar aos serviços do Azure não podem ser conectados a uma rede virtual. | |

Saiba mais sobre o [Gateway de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) serviço, seu [preços](https://azure.microsoft.com/pricing/details/vpn-gateway)e a transferência de dados de saída [preços](https://azure.microsoft.com/pricing/details/data-transfers).

## <a name="connecting-with-a-site-to-site-connection"></a>Conectar-se com uma conexão site a site
Esse tipo de conexão fornece acesso a alguns serviços de nuvem da Microsoft por meio de um túnel IPSec pela Internet, conforme mostrado abaixo.

![S2S](./media/guidance-connecting-your-on-premises-network-to-azure/s2s.png "conexão Site a site")

A conexão é feita pela sua conexão de Internet existente, mas requer o uso de um Gateway de VPN do Azure com seu preço associado e o preço da transferência de dados de saída. A tabela a seguir lista os prós e contras desse tipo de conexão.

| **Benefícios** | **Considerações** |
| --- | --- |
| Todos os dispositivos em sua rede local podem se comunicar com os serviços do Azure conectados a uma rede virtual, portanto, não é necessário para configurar conexões individuais para cada dispositivo. |Embora o tráfego é criptografado usando o IPSec, ele pode ser interceptado em trânsito, pois ele atravessa a Internet pública. |
| Usa sua conexão de Internet existente. |Latência imprevisível porque a conexão atravessa a Internet. |
| Pode ser usado para se conectar a serviços do Azure que podem ser conectados a uma rede virtual, como máquinas virtuais e serviços de nuvem. |Deve configurar e gerenciar um validado dispositivo VPN * no local. |
| Taxa de transferência de até 200 Mb/s por gateway. |Requer administração mínima de em andamento de um Gateway de VPN do Azure. |
| Pode forçar o tráfego de saída iniciado a partir de máquinas virtuais de nuvem por meio da rede local para inspeção e registro em log usando as rotas definidas pelo usuário ou o Border Gateway Protocol (BGP) * *. |Não pode ser usado para se conectar ao Microsoft Office 365 ou Dynamics CRM Online. |
| Não pode ser usado para se conectar aos serviços do Azure não podem ser conectados a uma rede virtual. | |
| Se você usa os serviços que iniciam conexões para dispositivos locais e as políticas de segurança exigem-lo, talvez seja necessário um firewall entre a rede local e o Azure. | |

* * Exibir uma lista dos [dispositivos VPN validados](../vpn-gateway/vpn-gateway-about-vpn-devices.md#devicetable).
* * * Saiba mais sobre como usar [rotas definidas pelo usuário](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) ou [BGP](../vpn-gateway/vpn-gateway-bgp-overview.md) para forçar o roteamento de redes virtuais do Azure para um dispositivo local.

## <a name="connecting-with-a-dedicated-private-connection"></a>Conectar-se com uma conexão privada dedicada
Esse tipo de conexão fornece acesso a todos os serviços de nuvem da Microsoft ao longo de uma conexão privada dedicada para a Microsoft não atravessa a Internet, conforme mostrado abaixo.

![ER](./media/guidance-connecting-your-on-premises-network-to-azure/er.png "conexão de ExpressRoute")

A conexão requer o uso do serviço ExpressRoute e uma conexão para um provedor de conectividade. A tabela a seguir lista os prós e contras desse tipo de conexão.

| **Benefícios** | **Considerações** |
| --- | --- |
| O tráfego não pode ser interceptado em trânsito pela Internet pública, como uma conexão dedicada por meio de um provedor de serviço é usado. |Requer gerenciamento de roteador local. |
| Largura de banda de até 10 Gb/s por circuito do ExpressRoute e a taxa de transferência de até 2 Gb/s para cada gateway. |Requer uma conexão dedicada para um provedor de conectividade. |
| Latência previsível porque ele é uma conexão dedicada para a Microsoft não atravessa a Internet. |Pode exigir a administração contínua mínimo de um ou mais Gateways de VPN do Azure (se conectar ao circuito a redes virtuais). |
| Não requer comunicação criptografada, embora você pode criptografar o tráfego, se desejado. |Se você estiver usando serviços de nuvem que iniciam conexões para dispositivos locais, talvez seja necessário um firewall entre a rede local e o Azure. |
| Pode se conectar diretamente a todos os serviços de nuvem do Microsoft, com algumas exceções *. |Requer conversão de endereços de rede (NAT) de endereços IP local inserir data centers da Microsoft para serviços que não podem ser conectados a um VNet.* * |
| Pode forçar o tráfego de saída iniciado a partir de máquinas virtuais de nuvem por meio da rede local para inspeção e registro em log de uso do BGP. | |

* * Exibir uma [lista de serviços](../expressroute/expressroute-faqs.md#supported-services) que não pode ser usado com o ExpressRoute. Sua assinatura do Azure deve ser aprovada para se conectar ao Office 365.  Consulte a [do Azure ExpressRoute para Office 365](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd?ui=en-US&rs=en-US&ad=US&fromAR=1) artigo para obter detalhes.
* * * Saiba mais sobre o ExpressRoute [NAT](../expressroute/expressroute-nat.md) requisitos.

Saiba mais sobre [ExpressRoute](../expressroute/expressroute-introduction.md), seus associados [preços](https://azure.microsoft.com/pricing/details/expressroute), e [provedores de conectividade](../expressroute/expressroute-locations.md#locations).

## <a name="additional-considerations"></a>Considerações adicionais
* Várias das opções acima têm vários limites máximos que podem dar suporte para conexões de rede virtual, conexões de gateway e outros critérios. É recomendável que você examine o Azure [limites de rede](../azure-subscription-service-limits.md#networking-limits) para entender se qualquer um deles impacto sobre os tipos de conectividade que você optar por usar.
* Se você planeja se conectar a um gateway de uma conexão de VPN site a site para a mesma rede virtual como um gateway de ExpressRoute, você deve se familiarizar com as restrições importantes primeiro. Consulte a [conexões coexistentes Site a Site e de configurar o ExpressRoute](../expressroute/expressroute-howto-coexist-resource-manager.md#limits-and-limitations) artigo para obter mais detalhes.

## <a name="next-steps"></a>Próximas etapas
Os recursos a seguir explicam como implementar os tipos de conexão abordados neste artigo.

* [Implementar uma conexão ponto a site](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)
* [Implementar uma conexão site a site](guidance-hybrid-network-vpn.md)
* [Implementar uma conexão privada dedicada](guidance-hybrid-network-expressroute.md)
* [Implementar uma conexão privada dedicada com uma conexão site a site para alta disponibilidade](guidance-hybrid-network-expressroute-vpn-failover.md)
