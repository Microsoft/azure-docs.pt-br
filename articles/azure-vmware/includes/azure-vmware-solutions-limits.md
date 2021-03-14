---
title: Limites da solução VMware do Azure
description: Limitações da solução VMware do Azure.
ms.topic: include
ms.date: 03/04/2021
ms.openlocfilehash: 99c27694a300284cfd99b8411306c90ad4d8a12e
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102194065"
---
<!-- Used in /azure/azure-resource-manager/management/azure-subscription-service-limits.md -->

A tabela a seguir descreve os limites máximos para a solução Azure VMware.

| **Recurso** | **Limite** |
| :-- | :-- |
| Clusters por nuvem privada | 4 |
| Número mínimo de nós por cluster | 3 |
| Número máximo de nós por cluster | 16 |
| Nós por nuvem privada | 64 |
| vCenter por nuvem privada | 1  |
| Emparelhamentos do site HCX | 3 com a Advanced Edition, 10 com Enterprise Edition |
| Número máximo de SDDCs vinculados para AVS ExpressRoute | 4 |
| AVS de ExpressRoute portspeed | 10 Gbps | 
| IPs públicos expostos por meio de vWAN | 100 |
| limites de capacidade do vSAN | 75% do total utilizável (manter 25% disponível para SLA)  |

Para outros limites específicos do VMware, use a [ferramenta de configuração máxima do VMware!](https://configmax.vmware.com/).
