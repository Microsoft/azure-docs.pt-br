---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/20/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: b1a9d93d9fccf02ba1517e429625150736e539e9
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67171807"
---
Ao criar um gateway de rede virtual, você precisa especificar o SKU do gateway que você deseja usar. Selecione as SKUs que atendem às suas necessidades com base nos tipos de cargas de trabalho, taxas de transferência, recursos e SLAs. Para SKUs em zonas de disponibilidade do Azure de gateway de rede virtual, consulte [SKUs de Gateway de zonas de disponibilidade do Azure](../articles/vpn-gateway/about-zone-redundant-vnet-gateways.md).

###  <a name="benchmark"></a>SKUs de gateway pelo túnel, a conexão e a taxa de transferência

[!INCLUDE [Aggregated throughput by SKU](./vpn-gateway-table-gwtype-aggtput-include.md)]

[!INCLUDE [classic SKU](./vpn-gateway-classic-sku-support-include.md)]

###  <a name="feature"></a>SKUs de gateway pelo conjunto de recursos

As novas SKUs do gateway VPN simplificam os conjuntos de recursos oferecidos nos gateways:

| **SKU**| **Recursos**|
| ---    | ---         |
|**Básico** (\*\*)   | **VPN baseada em rota**: 10 túneis S2S/conexões; Nenhuma autenticação RADIUS para P2S; Nenhum IKEv2 para P2S<br>**VPN baseada em política**: (IKEv1): 1 túnel S2S/conexão; sem P2S|
| **VpnGw1, VpnGw2 e VpnGw3** | **VPN baseada em roteamento**: até 30 túneis ( * ), P2S, BGP, ativo-ativo, política IPsec/IKE personalizada, coexistência ExpressRoute/VPN |
|        |             |

( * ) Você pode configurar "PolicyBasedTrafficSelectors" para se conectar a um gateway de VPN baseado em rota (VpnGw1, VpnGw2, VpnGw3) para vários dispositivos de firewall baseados em políticas. Confira [Conectar gateways de VPN a vários dispositivos VPN baseados em políticas usando o PowerShell](../articles/vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md) para obter detalhes.

(\*\*) O SKU básico é considerado um SKU herdado. A SKU básica tem certas limitações de recurso. Você não pode redimensionar um gateway que usa uma SKU básica para novas SKUs de gateway, em vez disso, você deve alterar para uma nova SKU, que envolve excluir e recriar o gateway VPN.

###  <a name="workloads"></a>SKUs de gateway - Vs de produção. Cargas de Trabalho de Desenvolvimento e Teste

Devido a diferenças nos SLAs e conjuntos de recursos, é recomendável usar as SKUs a seguir para produção versus desenvolvimento e teste:

| **Carga de trabalho**                       | **SKUs**               |
| ---                                | ---                    |
| **Produção, cargas de trabalho críticas** | VpnGw1, VpnGw2, VpnGw3 |
| **Teste de desenvolvimento ou prova de conceito**   | Básico (\*\*)                 |
|                                    |                        |

(\*\*) O SKU básico é considerado um SKU herdado e tem limitações de recursos. Verifique se o recurso que você precisa é compatível antes de usar a SKU básica.

Se você estiver usando as SKUs antigas (herdadas), as recomendações de SKU de produção serão Standard e HighPerformance. Para saber mais sobre as SKUs antigas, confira [SKUs de Gateway (herdadas)](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md).
