---
title: Métricas e alertas para a NAT da Rede Virtual do Azure
titleSuffix: Azure Virtual Network
description: Entenda as métricas e os alertas do Azure Monitor disponíveis para a NAT da Rede Virtual.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: nat
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/04/2020
ms.author: allensu
ms.openlocfilehash: b22d7823ac961de53914325d7ebf2b5d53b7c5af
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106055817"
---
# <a name="azure-virtual-network-nat-metrics"></a>Métricas da NAT da Rede Virtual do Azure

Os recursos do Gateway da NAT da Rede Virtual do Azure fornecem métricas multidimensionais. Você pode usar essas métricas para observar a operação e para [solução de problemas](troubleshoot-nat.md).  Os alertas podem ser configurados para problemas críticos, como esgotamento de SNAT.

<p align="center">
  <img src="media/nat-overview/flow-direction1.svg" alt="Figure depicts a NAT gateway resource that consumes all IP addresses for a public IP prefix and directs that traffic to and from two subnets of virtual machines and a virtual machine scale set." width="256" title="NAT de Rede Virtual para saída à Internet">
</p>

*Figura: NAT de Rede Virtual para saída à Internet*

## <a name="metrics"></a>Métricas

Os recursos do Gateway da NAT fornecem as seguintes métricas multidimensionais no Azure Monitor:

| Métrica | Descrição | Agregação recomendada | Dimensões |
|---|---|---|---|
| Bytes | Bytes processados de entrada e de saída | Somar | Direção (entrada; saída), protocolo (6 TCP; 17 UDP) |
| Pacotes | Pacotes processados de entrada e de saída | Somar | Direção (entrada; saída), protocolo (6 TCP; 17 UDP) |
| Pacotes removidos | Pacotes removidos pelo Gateway da NAT | Somar | / |
| Contagem de Conexões SNAT | Transições de estado por intervalo | Somar | Estado da conexão, protocolo (6 TCP; 17 UDP) |
| Contagem total de conexões SNAT | Conexões SNAT ativas atuais (~ portas SNAT em uso) | Somar | Protocolo (6 TCP; 17 UDP) |


## <a name="alerts"></a>Alertas

Os alertas das métricas podem ser configurados no Azure Monitor para cada uma das [métricas](#metrics) anteriores.

## <a name="limitations"></a>Limitações

Não há suporte para o Resource Health.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre a [NAT de Rede Virtual](nat-overview.md)
* Saiba mais sobre o [recurso do Gateway da NAT](nat-gateway-resource.md)
* Saiba mais sobre o [Azure Monitor](../azure-monitor/overview.md)
* Saiba mais sobre a [solução de problemas de recursos do Gateway da NAT](troubleshoot-nat.md).
* [Diga-nos o que criar em seguida para a NAT de Rede Virtual no UserVoice](https://aka.ms/natuservoice).


