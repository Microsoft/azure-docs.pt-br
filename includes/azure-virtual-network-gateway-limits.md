---
title: incluir arquivo
description: incluir arquivo
services: networking
author: anzaman
ms.service: VPN Gateway
ms.topic: include
ms.date: 08/25/2020
ms.author: alzam
ms.custom: include file
ms.openlocfilehash: 9fe9ef5549ced3b73d18d553fa0b62ec019684fe
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95561389"
---
| Recurso                                | Limite        |
|-----------------------------------------|------------------------------|
| Prefixos de endereço de VNet                   | 600 por gateway de VPN          |
| Rotas BGP de agregação                    | 4.000 por gateway de VPN        |
| Prefixos de endereço de gateway de rede local  | 1000 por gateway de rede local               |
| Conexões S2S                         | [Depende do SKU do gateway](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku)|
| Conexões P2S                         | [Depende do SKU do gateway](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku) |
| Limite de rotas P2S-IKEv2                 | 256 para não Windows **/** 25 para Windows           |
| Limite de rotas P2S-OpenVPN               | 1000                         |
| Máx. flows                              | 100 mil para VpnGw1/AZ  **/**  512K para VpnGw2-4/AZ|