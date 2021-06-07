---
title: incluir arquivo
description: incluir arquivo
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 12/17/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: b283add6cff1400cc3141f4fba3f0f3939ee34aa
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "97665091"
---
|  | **Ponto a Site** | **Site a site** | **ExpressRoute** |
| --- | --- | --- | --- |
| **Serviços com Suporte no Azure** |Serviços de Nuvem e Máquinas Virtuais |Serviços de Nuvem e Máquinas Virtuais |[Lista de serviços](../articles/expressroute/expressroute-faqs.md#supported-services) |
| **Larguras de Banda Típicas** |Com base na SKU do gateway |Normalmente < 1 Gbps agregado |50 Mbps, 100 Mbps, 200 Mbps, 500 Mbps, 1 Gbps, 2 Gbps, 5 Gbps e 10 Gbps |
| **Protocolos com Suporte** |SSTP (Secure Sockets Tunneling Protocol), OpenVPN e IPsec |IPsec |Conexão direta pelas tecnologias VLANs e VPN do NSP (MPLS, VPLS...) |
| **Roteamento** |RouteBased (dinâmico) |Damos suporte a PolicyBased (roteamento estático) e RouteBased (VPN de roteamento dinâmico) |BGP |
| **Resiliência de conexão** |ativo-passivo |ativo-passivo ou ativo-ativo |ativo-ativo |
| **Caso de uso típico** |Proteger o acesso às redes virtuais do Azure para usuários remotos |Cenários de desenvolvimento/teste/laboratório e cargas de trabalho de produção em pequena e média escala para serviços de nuvem e máquinas virtuais |Acesso a todos os serviços do Azure (lista validada), cargas de trabalho essenciais e em nível Empresarial, Backup, Big Data e Azure como um site de DR |
| **SLA** |[SLA](https://azure.microsoft.com/support/legal/sla/) |[SLA](https://azure.microsoft.com/support/legal/sla/) |[SLA](https://azure.microsoft.com/support/legal/sla/) |
| **Preços** |[Preços](https://azure.microsoft.com/pricing/details/vpn-gateway/) |[Preços](https://azure.microsoft.com/pricing/details/vpn-gateway/) |[Preços](https://azure.microsoft.com/pricing/details/expressroute/) |
| **Documentação Técnica** |[Documentação de Gateway de VPN](https://azure.microsoft.com/documentation/services/vpn-gateway/) |[Documentação de Gateway de VPN](https://azure.microsoft.com/documentation/services/vpn-gateway/) |[Documentação do ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/) |
| **perguntas frequentes** |[Perguntas frequentes de gateway de VPN](../articles/vpn-gateway/vpn-gateway-vpn-faq.md) |[Perguntas frequentes de gateway de VPN](../articles/vpn-gateway/vpn-gateway-vpn-faq.md) |[Perguntas Frequentes sobre ExpressRoute](../articles/expressroute/expressroute-faqs.md) |
