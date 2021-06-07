---
title: incluir arquivo
description: incluir arquivo
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: include
ms.date: 01/12/2021
ms.author: duau
ms.custom: include file
ms.openlocfilehash: 6f8ed3381f056238bdbb24fe52c5f859afef7d03
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "98147682"
---
| Recurso | Limite |
| --- | --- |
| Circuitos do ExpressRoute por assinatura |10 |
| Circuitos do ExpressRoute por região por assinatura, com o Azure Resource Manager |10 |
| Número máximo de rotas anunciadas para o emparelhamento privado do Azure com o ExpressRoute Standard |4.000 |
| Número máximo de rotas anunciadas para o emparelhamento privado do Azure com o complemento ExpressRoute Premium |10.000 |
| Número máximo de rotas anunciadas do emparelhamento privado do Azure do espaço de endereço da VNet para uma conexão do ExpressRoute |200 |
| Número máximo de rotas anunciadas para emparelhamento do Microsoft com o Standard do ExpressRoute |200 |
| Número máximo de rotas anunciadas para o emparelhamento da Microsoft com o complemento ExpressRoute Premium |200 |
| Número máximo de circuitos do ExpressRoute vinculados à mesma rede virtual no mesmo local de emparelhamento |4 |
| Número máximo de circuitos do ExpressRoute vinculados à mesma rede virtual em diferentes locais de emparelhamento |4 |
| Número de links de rede virtual permitido por circuito do ExpressRoute |Confira a tabela [Número de redes virtuais por circuito do ExpressRoute](#vnetpercircuit).  |

#### <a name="number-of-virtual-networks-per-expressroute-circuit"></a><a name="vnetpercircuit"></a> Número de redes virtuais por circuito do ExpressRoute
| **Tamanho do circuito** | **Número de links de rede virtual para Standard** | **Número de links de rede virtual com complemento Premium** |
| --- | --- | --- |
| 50 Mbps |10 |20 |
| 100 Mbps |10 |25 |
| 200 Mbps |10 |25 |
| 500 Mbps |10 |40 |
| 1 Gbps |10 |50 |
| 2 Gbps |10 |60 |
| 5 Gbps |10 |75 |
| 10 Gbps |10 |100 |
| 40 Gbps* |10 |100 |
| 100 Gbps* |10 |100 |

**Somente o ExpressRoute Direct de 100 Gbps*

> [!NOTE]
> Conexões de Alcance Global são contadas para o limite de conexões de rede virtual por circuito do ExpressRoute. Por exemplo, um Circuito Premium de 10 Gbps permitiria 5 conexões de Alcance Global e 95 conexões com os Gateways do ExpressRoute ou 95 conexões de Alcance Global e 5 conexões com os Gateways do ExpressRoute ou qualquer outra combinação até o limite de 100 conexões para o circuito.
