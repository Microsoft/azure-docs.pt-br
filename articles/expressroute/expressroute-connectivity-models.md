---
title: 'Azure ExpressRoute: modelos de conectividade'
description: Examine a conectividade entre os serviços de rede, Microsoft Azure e Microsoft 365 do cliente. Os clientes podem usar provedores de MPLS, trocas de nuvem e Ethernet.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: duau
ms.openlocfilehash: fb35a03b5dd8780445eb27f485966e3c3452feea
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91978740"
---
# <a name="expressroute-connectivity-models"></a>Modelos de conectividade do ExpressRoute
Você pode criar uma conexão entre sua rede local e a nuvem da Microsoft de quatro maneiras diferentes, [colocalização CloudExchange](#CloudExchange), [conexão Ethernet ponto a ponto](#Ethernet), [conexão de qualquer para qualquer (IPVPN)](#IPVPN)e [rota direta do ExpressRoute](#Direct). Os provedores de conectividade podem oferecer um ou mais modelos de conectividade. Converse com seu provedor de conectividade a fim de escolher o modelo mais adequado a você.
<br><br>

:::image type="content" source="./media/expressroute-connectivity-models/expressroute-connectivity-models-diagram.png" alt-text="Diagrama de modelo de conectividade do ExpressRoute":::

## <a name="co-located-at-a-cloud-exchange"></a><a name="CloudExchange"></a>Colocalizada em uma troca de nuvem
Se você estiver colocalizado em uma instalação com uma troca de nuvem, poderá solicitar conexões cruzadas virtuais com a nuvem da Microsoft por meio da troca de Ethernet do provedor de colocalização. Os provedores da colocalização podem oferecer conexões cruzadas de Camada 2 ou conexões cruzadas gerenciadas de Camada 3 entre sua infraestrutura na instalação de colocalização e a nuvem da Microsoft.

## <a name="point-to-point-ethernet-connections"></a><a name="Ethernet"></a>Conexões Ethernet ponto a ponto
Você pode conectar seus data centers/escritórios locais à nuvem da Microsoft por meio de links de Ethernet ponto a ponto. Os provedores de Ethernet ponto a ponto podem oferecer conexões de Camada 2 ou conexões gerenciadas de Camada 3 entre seu site e a nuvem da Microsoft.

## <a name="any-to-any-ipvpn-networks"></a><a name="IPVPN"></a>Redes de qualquer ponto a qualquer ponto (IPVPN)
É possível integrar sua WAN à nuvem da Microsoft. Os Provedores de IPVPN (normalmente, VPN MPLS) oferecem conectividade “qualquer para qualquer” entre suas filiais e datacenters. A nuvem da Microsoft pode ser interconectada à sua WAN para que ela fique exatamente igual a qualquer outra filial. Normalmente, os provedores de rede WAN oferecem conectividade gerenciada de Camada 3. Os recursos do ExpressRoute são idênticos em todos os modelos de conectividade mencionados acima.

## <a name="direct-from-expressroute-sites"></a><a name="Direct"></a>Direto de sites do ExpressRoute
Você pode se conectar diretamente à rede global da Microsoft em um local de emparelhamento distribuído estrategicamente em todo o mundo. O [ExpressRoute Direct](expressroute-erdirect-about.md) fornece conectividade dual de 100 Gbps ou 10 Gbps, que dá suporte à conectividade ativa/ativa em escala.

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre conexões e domínios de roteamento do ExpressRoute. Consulte [Circuitos e domínios de roteamento do ExpressRoute](expressroute-circuit-peerings.md).
* Saiba mais sobre recursos do ExpressRoute. Veja a [Visão geral técnica do ExpressRoute](expressroute-introduction.md)
* Encontrar um provedor de serviços. Consulte [Parceiros e locais de emparelhamento do ExpressRoute](expressroute-locations.md).
* Certifique-se que todos os pré-requisitos foram atendidos. Consulte [Pré-requisitos do ExpressRoute](expressroute-prerequisites.md).
* Consulte os requisitos para [Roteamento](expressroute-routing.md), [NAT](expressroute-nat.md) e [QoS](expressroute-qos.md).
* Configurar sua conexão do ExpressRoute.
  * [Criar um circuito do ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md)
  * [Configurar o roteamento](expressroute-howto-routing-portal-resource-manager.md)
  * [Vincular uma rede virtual a um circuito do ExpressRoute](expressroute-howto-linkvnet-portal-resource-manager.md)