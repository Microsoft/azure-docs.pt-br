---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 9734859c0bf22201c146e5d8a220f3146f6051c4
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67171812"
---
A tabela a seguir mostra os tipos de gateway e a produtividade agregada estimada pela SKU do gateway. Esta tabela aplica-se a ambos os modelos de implantação do Gerenciador de Recursos e clássico. 

Os preços diferem entre os SKUs de gateway. Para saber mais, veja [Preços do Gateway de VPN](https://azure.microsoft.com/pricing/details/vpn-gateway).

Observe que a SKU do gateway UltraPerformance não é representada nesta tabela. Para saber mais sobre a SKU do UltraPerformance, veja a documentação do [ExpressRoute](../articles/expressroute/expressroute-about-virtual-network-gateways.md).

|  | **Taxa de transferência de Gateway de VPN (1)** | **Túneis IPsec máximo de Gateway de VPN (2)** | **Taxa de transferência de Gateway de ExpressRoute** | **Coexistência de Gateway de VPN e o ExpressRoute** |
| --- | --- | --- | --- | --- |
| **SKU Básica (3)(5)(6)** |100 Mbps |10 |500 Mbps (6) |Não |
| **SKU padrão (4)(5)** |100 Mbps |10 |1000 Mbps |Sim |
| **SKU de Alto Desempenho (4)** |200 Mbps |30 |2000 Mbps |Sim |


(1) A taxa de transferência da VPN é uma estimativa aproximada baseada nas medidas entre redes virtuais na mesma região do Azure. Não é uma taxa de transferência garantida para conexões entre locais na Internet. É a medida de taxa de transferência máxima possível.

(2) O número de túneis refere-se às VPNs baseadas em rota. Uma VPN PolicyBased só pode dar suporte a um túnel de VPN de Site a Site.

(3) Não há suporte a BGP para a SKU básica.

(4) não há suporte para VPNs PolicyBased nesta SKU. Eles têm suporte apenas na SKU básica.

(5) Não há suporte para conexões de Gateway de VPN S2S ativa-ativa para essa SKU. Ativo-ativo tem suporte apenas com a SKU HighPerformance.

(6) A SKU básica é preterida para uso com ExpressRoute.
