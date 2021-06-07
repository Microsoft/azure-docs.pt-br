---
title: Conectando o Azure a nuvens públicas | Microsoft Docs
description: Descrever várias maneiras de conectar o Azure a outras nuvens públicas
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 07/24/2019
ms.author: duau
ms.openlocfilehash: fb4c34345ab902e243f22eae06fd5ea174fd2992
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92202184"
---
# <a name="connecting-azure-with-public-clouds"></a>Conectando o Azure com nuvens públicas

Muitas empresas estão buscando uma estratégia de várias nuvens devido a objetivos comerciais e técnicos. Isso inclui custo, flexibilidade, disponibilidade de recursos, redundância, soberania de dados, etc. Essa estratégia ajuda a aproveitar melhor as duas nuvens. 

Essa abordagem também apresenta os desafios para a empresa em termos de arquitetura de rede e de aplicativos. Alguns desses desafios são latência e taxa de transferência de dados. Para resolver esses desafios, os clientes estão procurando se conectar a várias nuvens diretamente. Alguns provedores de serviços fornecem uma solução para conectar vários provedores de nuvem para os clientes. Em outros casos, o cliente pode implantar seu próprio roteador para conectar várias nuvens públicas.
## <a name="connectivity-via-expressroute"></a>Conectividade via ExpressRoute
O ExpressRoute permite que os clientes estendam suas redes locais para a nuvem da Microsoft por meio de uma conexão privada, facilitada por um provedor de conectividade. Com o ExpressRoute, os clientes podem estabelecer conexões com os serviços de nuvem da Microsoft.

Há três maneiras de se conectar por meio do ExpressRoute.

1. Provedor de Layer3
2. Provedor de Layer2
3. Conexão direta

### <a name="layer3-provider"></a>Provedor de Layer3

Provedores de Layer3 são comumente conhecidos como provedores IP VPN ou MPLS VPN. Os clientes aproveitam esses provedores para conectividade do MultiPoint entre seus data centers, branches e a nuvem. Os clientes se conectam ao provedor L3 via BGP ou via rota padrão estática. O provedor de serviços anuncia as rotas entre os sites do cliente, os data centers e a nuvem pública. 
 
Ao conectar-se por meio do provedor Layer3, a Microsoft anunciará as rotas de VNET do cliente para o provedor de serviços por BGP. O provedor pode ter duas implementações diferentes.

![Diagrama que mostra um provedor Layer3.](media/expressroute-connect-azure-to-public-cloud/azure-to-public-clouds-l3.png)

O provedor poderá ser a aterrissagem de cada provedor de nuvem em um VRF separado, se o tráfego de todos os provedores de nuvem for alcançado no roteador do cliente. Se o cliente estiver executando o BGP com o provedor de serviços, essas rotas serão anunciadas novamente para outros provedores de nuvem por padrão. 

Se o provedor de serviços for aterrissar todos os provedores de nuvem no mesmo VRF, as rotas serão anunciadas para outros provedores de nuvem diretamente do provedor de serviços. Isso está assumindo uma operação BGP padrão em que as rotas eBGP são anunciadas para outros vizinhos eBGP por padrão.

Cada nuvem pública tem um limite de prefixo diferente, assim, enquanto a distribuição do provedor de serviços de rotas deve ter cuidado ao distribuir as rotas.

### <a name="layer2-provider-and-direct-connection"></a>Provedor Layer2 e conexão direta

Embora a conectividade física em ambos os modelos seja diferente, mas em Layer3 BGP é estabelecida diretamente entre MSEE e o roteador do cliente. Para o cliente do ExpressRoute Direct, conecta-se diretamente ao MSEE. No caso do Layer2, o provedor de serviços estende a VLAN do local do cliente para a nuvem. Os clientes executam o BGP no topo da rede layer2 para conectar seus DCs à nuvem.
![Diagrama que mostra um provedor Layer2 e uma conexão direta.](media/expressroute-connect-azure-to-public-cloud/azure-to-public-clouds-l2.png)
Em ambos os casos, o cliente terá conexões ponto a ponto para cada uma das nuvens públicas. O cliente estabelecerá uma conexão BGP separada para cada nuvem pública. As rotas recebidas por um provedor de nuvem serão anunciadas para outro provedor de nuvem por padrão. Cada provedor de nuvem tem um limite de prefixo diferente, portanto, ao anunciar as rotas, o cliente deve cuidar desses limites. O cliente pode usar os botões de BGP usuais com a Microsoft ao anunciar rotas de outras nuvens públicas.

## <a name="direct-connection-with-expressroute"></a>Conexão direta com o ExpressRoute

Os clientes podem optar por conectar o ExpressRoute diretamente à oferta de conectividade direta do provedor de nuvem. Dois provedores de nuvem serão conectados de volta para trás e o BGP será estabelecido diretamente entre seus roteadores. Esse tipo de conexão está disponível com o Oracle hoje.

## <a name="site-to-site-vpn"></a>VPN site a site

Os clientes podem aproveitar a Internet para conectar suas instâncias no Azure com outras nuvens públicas. Quase todos os provedores de nuvem oferecem recursos de VPN site a site. No entanto, pode haver incompatibilidades devido à falta de determinadas variantes. Por exemplo, alguns provedores de nuvem dão suporte apenas a IKEv1, portanto, há um ponto de extremidade de terminação de VPN necessário nessa nuvem. Para os provedores de nuvem que dão suporte a IKEv2, um túnel direto pode ser estabelecido entre gateways de VPN em ambos os provedores de nuvem.

A VPN site a site não é considerada uma solução de alta taxa de transferência e baixa latência. No entanto, ele pode ser usado como um backup para conectividade física.

## <a name="next-steps"></a>Próximas etapas
Consulte [perguntas frequentes sobre o expressroute][ER-FAQ] para obter mais perguntas sobre o expressroute e a conectividade de rede virtual.

Consulte [Configurar a conexão direta entre o Azure e o Oracle Cloud][ER-OCI] para conectividade entre o Azure e o Oracle

<!--Link References-->
[ER-FAQ]: ./expressroute-faqs.md
[ER-OCI]: ../virtual-machines/workloads/oracle/configure-azure-oci-networking.md