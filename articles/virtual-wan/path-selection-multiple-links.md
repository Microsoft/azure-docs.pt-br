---
title: Seleção de caminho do Azure entre vários links de ISP
titleSuffix: Azure Virtual WAN
description: Saiba mais sobre a seleção de caminhos do Azure e a WAN virtual
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: f24696c0db3155a59106e1361b01454b9ac16a20
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91267747"
---
# <a name="azure-path-selection-across-multiple-isp-links"></a>Seleção de caminho do Azure entre vários links de ISP

A WAN virtual do Azure fornece ao usuário a capacidade de incluir informações de link em um site de VPN, permitindo cenários em que a solução de dispositivo VPN/SD-WAN pode programar políticas específicas de ramificação para direcionar o tráfego entre vários links no Azure. Isso é chamado de **seleção de caminho do Azure**.

## <a name="architecture"></a>Arquitetura

Para entender como funciona a seleção de caminhos do Azure, vamos usar o exemplo de um site VPN de WAN virtual e uma conexão site a site.

Um site de VPN representa o dispositivo SD-WAN/VPN local com informações como IP público, modelo de dispositivo e nome, etc. O site VPN local real pode ter vários links de ISP que também podem ser incluídos em informações do site VPN de WAN virtual. Isso permite que você exiba as informações de link no Azure.

Uma conexão IPsec site a site entrando em uma VPN de WAN virtual é encerrada nas instâncias de gateway de VPN dentro de um hub virtual. Uma conexão site a site representa a conectividade entre o site VPN e o gateway de VPN do Azure. Ele consiste em uma ou mais conexões de link. Cada conexão de link consiste em dois túneis com cada túnel terminando em uma instância exclusiva do gateway de VPN de WAN virtual do Azure. Até quatro conexões de link podem ser configuradas na conexão site a site, o que torna possível ter até oito túneis em uma conexão site a site. O Azure dá suporte a até 2000 túneis sendo encerrados dentro de um único gateway de VPN de WAN virtual.

:::image type="content" source="./media/path-selection-multiple-links/multi-link-site.png" alt-text="Diagrama de vários links":::

Esta figura mostra o vínculo múltiplo em um site conectando-se à WAN virtual do Azure. Neste diagrama:

* Há dois links de ISP no Branch local (VPN/SD-dispositivo de WAN). Cada link do ISP corresponde a uma conexão de link.

* Supõe-se que o dispositivo de VPN/SD-WAN do Gerenciador de clientes local oferece suporte a IPsec IKEv1 ou IKEv2.

* Cada conexão de WAN virtual site a site do Azure é composta por conexões de link dentro dela própria. Uma conexão oferece suporte a até quatro conexões de link. O Azure cobra uma taxa de unidade de conexão para a conexão WAN virtual. Não há nenhum encargo para as conexões de link.

* Cada conexão de link, por sua vez, consiste em dois túneis IPsec que podem terminar em duas instâncias diferentes do gateway de VPN de WAN virtual. Os gateways são configurados como gateways ativos-ativos para resiliência. Cada conexão de link é necessária para ter um endereço IP exclusivo e o IP de emparelhamento via protocolo BGP. No diagrama, o túnel 0 pode terminar na instância 0 e o túnel 1 pode terminar na instância 1.

* Os dispositivos de ramificação que fornecem seleção de caminho podem habilitar a política apropriada na solução de gerenciamento de Branch para direcionar o tráfego entre vários links para o Azure. Por exemplo, o link do ISP 1 pode ser usado para tráfego de prioridade mais alta e o link do ISP 2 pode ser usado como backup.

* É importante observar que a VPN do HUB virtual usa ECMP (roteamento de vários caminhos de custo igual) em todos os túneis de terminação.

## <a name="next-steps"></a>Próximas etapas

Consulte as [perguntas frequentes do Azure](virtual-wan-faq.md).