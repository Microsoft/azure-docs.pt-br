---
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/09/2018
ms.author: cherylmc
ms.openlocfilehash: 622a2f69c2e7b82f2df10d6120ee2dd466961915
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "77211416"
---
As SKUs herdadas (antigas) do gateway de VPN são:

* Padrão (básico)
* Standard
* HighPerformance

O gateway de VPN não usa a SKU do gateway UltraPerformance. Para saber mais sobre a SKU do UltraPerformance, veja a documentação do [ExpressRoute](../articles/expressroute/expressroute-about-virtual-network-gateways.md).

Ao trabalhar com os SKUs herdados, considere o seguinte:

* Se quiser usar um tipo PolicyBased VPN, você deverá usar a SKU Básica. VPNs PolicyBased (anteriormente chamadas de Roteamento Estático) não têm suporte em qualquer outro tipo de SKU.
* BGP não tem suporte na SKU Básica.
* Configurações de coexistência de ExpressRoute-Gateway de VPN não têm suporte na SKU Básica.
* As conexões de Gateway de VPN S2S ativa-ativa só podem ser configuradas na SKU HighPerformance.