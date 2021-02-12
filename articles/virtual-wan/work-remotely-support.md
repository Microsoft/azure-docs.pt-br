---
title: WAN virtual do Azure e trabalhando remotamente
description: Esta página descreve como você pode aproveitar a WAN virtual do Azure para habilitar o trabalho remotamente devido ao pandemia COVID-19.
services: virtual-wan
author: reyandap
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/22/2020
ms.author: cherylmc
ms.openlocfilehash: fc048f3da3156f5e17cfa32479b834b7320a60a2
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96023476"
---
# <a name="azure-virtual-wan-and-supporting-remote-work"></a>WAN virtual do Azure e suporte ao trabalho remoto

>[!NOTE]
>Este artigo descreve como você pode aproveitar a WAN virtual do Azure, o Azure, a rede da Microsoft e o ecossistema de parceiros do Azure para trabalhar remotamente e atenuar os problemas de rede que você está enfrentando devido a crises COVID-19.
>

Você está embaralhando para fornecer conectividade para usuários remotos?
Você vê repentinamente uma necessidade de dar suporte a um surto de usuários além do que você planejou?
Você precisa que o usuário se conecte de casa e não só acesse a nuvem, mas também pode alcançar localmente?
Você precisa que seus usuários remotos possam acessar recursos por trás de uma WAN privada?
Você tem a necessidade de os usuários acessarem recursos intra Cloud sem a necessidade de configurar a conectividade entre regiões?
Como esse pandemia global cria alterações sem precedentes em nosso lugar, a equipe de WAN virtual do Azure está aqui para que você atenda às suas necessidades de conectividade.

A WAN Virtual do Azure é um serviço de rede que reúne muitas funcionalidades de rede, segurança e roteamento para fornecer uma interface operacional. Essas funcionalidades incluem conectividade de ramificação (por meio da automação de conectividade de dispositivos de parceiros de WAN virtuais, como SD-WAN ou VPN CPE), conectividade VPN site a site, conectividade VPN de usuário remoto (ponto a site), conexão privada (ExpressRoute), conectividade de nuvem interna (conectividade transitiva para redes virtuais), VPN ExpressRoute interconectividade, roteamento, firewall do Azure, criptografia para conectividade Você não precisa ter todos esses casos de uso para começar a usar a WAN virtual. Você pode começar com apenas um caso de uso e ajustar sua rede à medida que ela evolui.

![Diagrama de WAN virtual](./media/virtual-wan-about/virtualwan1.png)

Agora, falando sobre usuários remotos, vamos examinar o que você precisa para colocar sua rede em funcionamento:

## <a name="set-up-remote-user-connectivity"></a><a name="connectivity"></a>Configurar a conectividade de usuário remoto

Você pode conectar seus recursos no Azure por meio de uma conexão de IPsec/IKE (IKEv2) ou OpenVPN. Esse tipo de conexão requer que um cliente VPN seja configurado para o usuário remoto. Esse cliente pode ser o cliente de [VPN do Azure](https://go.microsoft.com/fwlink/?linkid=2117554) ou cliente OpenVPN ou qualquer cliente que ofereça suporte a IKEv2. Para obter mais informações, consulte [Criar uma conexão ponto a site](virtual-wan-point-to-site-portal.md).

## <a name="connectivity-from-the-remote-user-to-on-premises"></a><a name="remote user connectivity"></a>Conectividade do usuário remoto para o local

Você tem duas opções aqui:

* Configure a conectividade site a site com qualquer dispositivo VPN existente. Quando você conecta o dispositivo VPN IPsec ao Hub de WAN virtual do Azure, a conectividade entre a VPN de usuário ponto a site (usuário remoto) e a VPN site a site é automática. Para obter mais informações sobre como configurar a VPN site a site do dispositivo VPN local para a WAN virtual do Azure, consulte [criar uma conexão site a site usando a WAN virtual](virtual-wan-site-to-site-portal.md).

* Conecte o circuito do ExpressRoute ao Hub WAN virtual. A conexão de um circuito do ExpressRoute requer a implantação de um gateway do ExpressRoute na WAN virtual. Assim que você tiver implantado um, a interconectividade entre a VPN de usuário ponto a site e o usuário do ExpressRoute será automático. Para criar a conexão do ExpressRoute, consulte [criar uma conexão de expressroute usando a WAN virtual](virtual-wan-expressroute-portal.md). Você pode usar um circuito de ExpressRoute existente para se conectar à WAN virtual do Azure.

## <a name="existing-basic-virtual-wan-customer"></a><a name="basic vWAN"></a>Cliente de WAN virtual básico existente

A WAN virtual básica fornece somente VPN site a site. Para que os usuários remotos se conectem, será necessário atualizar a WAN virtual para a WAN virtual padrão. Para obter as etapas para atualizar uma WAN virtual, consulte [atualizar uma WAN virtual de básico para Standard](upgrade-virtual-wan.md)

## <a name="additional-information"></a><a name="other considerations"></a>Informações adicionais

A WAN virtual dá suporte a um hub por região/local. Para obter informações de localização, consulte o artigo [Parceiros e localizações de WAN Virtual](virtual-wan-locations-partners.md). Cada Hub dá suporte a até 10.000 conexões de usuário remoto, 1.000 conexão de ramificação, quatro circuitos de ExpressRoute e até 500 conexões de rede virtual. Ao escalar verticalmente os usuários remotos, se você tiver dúvidas, não hesite em buscar ajuda enviando um email para azurevirtualwan@microsoft.com . Se você precisar de suporte técnico, não se esqueça de abrir um tíquete de suporte na portal do Azure e a ajuda estará a caminho.

## <a name="faq"></a><a name="faq"></a>Perguntas frequentes

[!INCLUDE [Virtual WAN FAQ](../../includes/virtual-wan-faq-include.md)]

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre WAN virtual, consulte [visão geral da WAN virtual](virtual-wan-about.md)