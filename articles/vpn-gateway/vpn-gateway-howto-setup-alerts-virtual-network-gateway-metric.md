---
title: Como configurar alertas de métricas do Gateway de VPN do Azure
description: Etapas para configurar alertas em métricas de Gateway de VPN
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptional
ms.date: 04/22/2019
ms.author: alzam
ms.openlocfilehash: 890b096acba601ec20efaac21155da84e77a1f31
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63769460"
---
# <a name="setting-up-alerts-on-vpn-gateway-metrics"></a>Configurando alertas em métricas do Gateway de VPN

Este artigo ajuda você a configurar alertas para métricas do Gateway de VPN. O Azure monitor fornece a capacidade de configurar alertas para recursos do Azure. Alertas podem ser configurados para gateways de rede virtual do tipo "VPN".


|**Métrica**   | **Unidade** | **granularidade** | **Descrição** | 
|---       | ---        | ---       | ---            | ---       |
|**AverageBandwidth**| Bytes/s  | 5 minutos| Média de utilização de largura de banda combinada de todas as conexões site a site no gateway.     |
|**P2SBandwidth**| Bytes/s  | 1 minuto  | Média de utilização de largura de banda combinada de todas as conexões ponto a site no gateway.    |
|**P2SConnectionCount**| Contagem  | 1 minuto  | Contagem de P2S conexões no gateway.   |
|**TunnelAverageBandwidth** | Bytes/s    | 5 minutos  | Média de utilização de largura de banda de túneis criada no gateway. |
|**TunnelEgressBytes** | Bytes | 5 minutos | Tráfego de saída em túneis criados no gateway.   |
|**TunnelEgressPackets** | Contagem | 5 minutos | Contagem de pacotes de saída nos túneis criados no gateway.   |
|**TunnelEgressPacketDropTSMismatch** | Contagem | 5 minutos | Contagem de pacotes de saída descartados em túneis causados por incompatibilidade de TS. |
|**TunnelIngressBytes** | Bytes | 5 minutos | Tráfego de entrada nos túneis criados no gateway.   |
|**TunnelIngressPackets** | Contagem | 5 minutos | Contagem de pacotes de entrada nos túneis criados no gateway.   |
|**TunnelIngressPacketDropTSMismatch** | Contagem | 5 minutos | Contagem de pacotes de entrada descartados em túneis causados por incompatibilidade de TS. |


## <a name="setup"></a>Configurar alertas do Azure Monitor com base nas métricas usando o portal

As etapas de exemplo abaixo criará um alerta em um gateway para: <br>

**Métrica:** Túnel de largura de banda média <br>
**Condição:** largura de banda > 10 Bytes / segundo <br>
**Janela:** 5 minutos <br>
**Ação de alerta:** Email <br>



1. Navegue até o recurso de gateway de rede virtual e selecione "Alertas" na guia monitoramento, em seguida, criar uma nova regra de alerta ou editar uma regra de alerta existente.

![Point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert1.png "criar")

2. Selecione seu gateway de VPN como o recurso.

![Point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert2.png "selecione")

3. Selecione uma métrica para configurar o alerta ![ponto a site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert3.png "selecione")
4. Configure a lógica de sinal. Há três componentes para lógica de sinal:

     a. Dimensões: Se a métrica tem dimensões, os valores de dimensão específicos podem ser selecionados para que o alerta é avaliado somente a dados da dimensão. Estes são opcionais.<br>
    b. Condição: A operação para avaliar o valor da métrica.<br>
    c. Horário: Especifica a granularidade dos dados de métrica e o período de tempo para avaliar o alerta.<br>

![Point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert4.png "selecione")

5. Para exibir as regras configuradas, clique em "Gerenciar regras de alerta" ![ponto a site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert8.png "selecione")

## <a name="next-steps"></a>Próximas etapas

Para configurar alertas em logs de diagnóstico de túnel, consulte [como configurar alertas em logs de diagnóstico do Gateway de VPN](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md).
