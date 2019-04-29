---
title: Visão geral de BGP e gateways de VPN do Azure | Microsoft Docs
description: Este artigo fornece uma visão geral do BGP com Gateways de VPN do Azure.
services: vpn-gateway
author: WenJason
manager: digimobile
ms.service: vpn-gateway
ms.topic: article
origin.date: 01/12/2017
ms.date: 03/04/2019
ms.author: v-jay
ms.openlocfilehash: 91e9fe1eb6b3df0b64d05f2b1e300403a9e01db9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60762248"
---
# <a name="about-bgp-with-azure-vpn-gateway"></a>Sobre o BGP com o Gateway de VPN do Azure
Este artigo fornece uma visão geral do suporte a BGP (Border Gateway Protocol) no Gateway de VPN do Azure.

O BGP é o protocolo de roteamento padrão usado na Internet para a troca de informações de roteamento e acessibilidade entre duas ou mais redes. Quando usado no contexto de Redes Virtuais do Azure, o BGP habilita os Gateways de VPN do Azure e os dispositivos de VPN local, chamados de pares de BGP ou vizinhos, a trocar "rotas" que informarão ambos os gateways da disponibilidade e da acessibilidade para que esses prefixos percorram os gateways ou os roteadores envolvidos. O BGP também pode habilitar o roteamento de trânsito entre várias redes propagando rotas que um gateway BGP obtém de um par no nível de protocolo BGP para todos os outros pares no nível de protocolo BGP. 

## <a name="why"></a>Por que usar o BGP?
O BGP é um recurso opcional que você pode usar com gateways de VPN Baseados em Rota do Azure. Você também deve verificar se os dispositivos de VPN locais dão suporte a BGP antes de habilitar o recurso. Você pode continuar a usar gateways de VPN do Azure e seus dispositivos de VPN locais sem BGP. É o equivalente ao uso de rotas estáticas (sem BGP) *versus* o uso de roteamento dinâmico com BGP entre suas redes e o Azure.

Há várias vantagens e novos recursos com o BGP:

### <a name="prefix"></a>Suporte a atualizações prefixo flexíveis e automáticas
Com o BGP, você só precisa declarar um prefixo mínimo para um par de BGP específico através do túnel de VPN S2S IPsec. Ele pode ser tão pequeno quanto um prefixo de host (/32) do endereço IP do par de BGP do dispositivo VPN local. Você pode controlar quais prefixos de rede locais deseja anunciar ao Azure para permitir que sua Rede Virtual do Azure os acesse.

Você também pode anunciar prefixos maiores que podem incluir alguns de seus prefixos de endereço de VNet, como um espaço de endereço IP privado grande (por exemplo, 10.0.0.0/8). Observe que os prefixos não podem ser idênticos a nenhum de seus prefixos de VNet. As rotas idênticas aos prefixos de VNet serão rejeitadas.

### <a name="multitunnel"></a>Suporte a vários túneis entre uma VNet e um site local com failover automático com base em BGP
Você pode estabelecer várias conexões entre sua VNet do Azure e os dispositivos de VPN locais no mesmo local. Esse recurso fornece vários túneis (caminhos) entre as duas redes em uma configuração ativo-ativo. Se um dos túneis for desconectado, as rotas correspondentes serão retiradas por meio do BGP, e o tráfego mudará automaticamente para os túneis restantes.

O seguinte diagrama mostra um exemplo simples dessa configuração altamente disponível:

![Vários caminhos ativos](./media/vpn-gateway-bgp-overview/multiple-active-tunnels.png)

### <a name="transitrouting"></a>Suporte ao roteamento de trânsito entre as redes locais e múltiplas VNets do Azure
O BGP habilita vários gateways a aprender e propagar prefixos de redes diferentes, quer elas estejam conectadas direta ou indiretamente. Isso pode habilitar o roteamento de tráfego com gateways de VPN do Azure entre os sites locais ou em várias Redes Virtuais do Azure.

O seguinte diagrama mostra um exemplo de uma topologia de vários saltos com vários caminhos que podem transportar o tráfego entre as duas redes locais por meio de gateways de VPN do Azure dentro das Redes da Microsoft:

![Trânsito com vários saltos](./media/vpn-gateway-bgp-overview/full-mesh-transit.png)

## <a name="faq"></a>PERGUNTAS FREQUENTES SOBRE O BGP
[!INCLUDE [vpn-gateway-faq-bgp-include](../../includes/vpn-gateway-faq-bgp-include.md)]

## <a name="next-steps"></a>Próximas etapas
Confira [Introdução ao BGP em gateways de VPN do Azure](vpn-gateway-bgp-resource-manager-ps.md) para obter as etapas de configuração do BGP para suas conexões entre locais e VNet para VNet.

