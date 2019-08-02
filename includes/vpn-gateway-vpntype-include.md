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
ms.openlocfilehash: b3907882df09bfae1d6453fbffbd3e7562554f7c
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67171804"
---
* **PolicyBased:** VPNs PolicyBased eram chamadas anteriormente de gateways de roteamento estático no modelo de implantação clássico. As VPNs baseadas em política criptografam e direcionam pacotes por meio de túneis IPsec com base nas políticas IPsec configuradas com as combinações de prefixos de endereço entre sua rede local e a VNet do Azure. A política (ou o seletor de tráfego) normalmente é definida como uma lista de acesso na configuração de dispositivo VPN. O valor para um tipo de VPN PolicyBased é *PolicyBased*. Ao usar uma VPN PolicyBased, lembre-se das seguintes limitações:
  
  * VPNs PolicyBased **só** podem ser usadas no SKU de gateway básico. Esse tipo de VPN não é compatível com outros SKUs de gateway.
  * Você pode ter apenas um túnel ao usar uma VPN PolicyBased.
  * Você só pode usar VPNs PolicyBased para conexões S2S, e apenas para determinadas configurações. A maioria das configurações de Gateway de VPN exige uma VPN RouteBased.
* **RouteBased**: As VPNs RouteBased eram chamadas anteriormente de gateways de roteamento dinâmico no modelo de implantação clássico. As VPNs RouteBased usam "rotas" da tabela de roteamento ou de encaminhamento de IP para direcionar pacotes para as interfaces de túnel correspondentes. As interfaces de túnel criptografam ou descriptografam então os pacotes para dentro e para fora dos túneis. A política (ou seletor de tráfego) para as VPNs RouteBased são configurados como qualquer para qualquer (ou curingas). O valor para um tipo de VPN RouteBased é *RouteBased*.
