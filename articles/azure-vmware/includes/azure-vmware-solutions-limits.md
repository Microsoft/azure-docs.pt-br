---
title: Limites da solução VMware do Azure
description: Limitações da solução VMware do Azure.
ms.topic: include
ms.date: 03/24/2021
ms.openlocfilehash: 997a5ae96ff30226d055b7b966b128d7ec0ae5bd
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105582288"
---
<!-- Used in /azure/azure-resource-manager/management/azure-subscription-service-limits.md -->

A tabela a seguir descreve os limites máximos para a solução Azure VMware.

| **Recurso** | **Limite** |
| :-- | :-- |
| Clusters por nuvem privada | 12 |
| Número mínimo de nós por cluster | 3 |
| Número máximo de nós por cluster | 16 |
| Nós por nuvem privada | 96 |
| vCenter por nuvem privada | 1  |
| Emparelhamentos do site HCX | 3 com a Advanced Edition, 10 com Enterprise Edition |
| Número máximo de SDDCs vinculados para AVS ExpressRoute | 4 |
| AVS de ExpressRoute portspeed | 10 Gbps<br />O gateway de rede virtual usado determina a largura de banda real. Para obter mais detalhes, consulte [sobre gateways de rede virtual do ExpressRoute](../../expressroute/expressroute-about-virtual-network-gateways.md) | 
| IPs públicos expostos por meio de vWAN | 100 |
| limites de capacidade do vSAN | 75% do total utilizável (manter 25% disponível para SLA)  |

Para outros limites específicos do VMware, use a [ferramenta de configuração máxima do VMware!](https://configmax.vmware.com/).
