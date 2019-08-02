---
title: Configurar alertas em métricas do Gateway de VPN do Azure
description: Etapas para configurar alertas em métricas de Gateway de VPN
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: alzam
ms.openlocfilehash: d57663f683ba4e2107ec6813a19fac7b2dcdd26a
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67605222"
---
# <a name="set-up-alerts-on-vpn-gateway-metrics"></a>Configurar alertas em métricas do Gateway de VPN

Este artigo ajuda você a configurar alertas em métricas do Gateway de VPN do Azure. O Azure Monitor fornece a capacidade de configurar alertas para recursos do Azure. Você pode configurar alertas para gateways de rede virtual do tipo "VPN".


|**Métrica**   | **Unidade** | **granularidade** | **Descrição** | 
|---       | ---        | ---       | ---            | ---       |
|**AverageBandwidth**| Bytes/s  | 5 minutos| Média de utilização de largura de banda combinada de todas as conexões site a site no gateway.     |
|**P2SBandwidth**| Bytes/s  | 1 minuto  | Média de utilização de largura de banda combinada de todas as conexões ponto a site no gateway.    |
|**P2SConnectionCount**| Contagem  | 1 minuto  | Contagem de conexões ponto a site no gateway.   |
|**TunnelAverageBandwidth** | Bytes/s    | 5 minutos  | Média de utilização de largura de banda de túneis criada no gateway. |
|**TunnelEgressBytes** | Bytes | 5 minutos | Tráfego de saída em túneis criados no gateway.   |
|**TunnelEgressPackets** | Count | 5 minutos | Contagem de pacotes de saída nos túneis criados no gateway.   |
|**TunnelEgressPacketDropTSMismatch** | Count | 5 minutos | Contagem de pacotes de saída descartados em túneis causados por incompatibilidade do seletor de tráfego. |
|**TunnelIngressBytes** | Bytes | 5 minutos | Tráfego de entrada nos túneis criados no gateway.   |
|**TunnelIngressPackets** | Count | 5 minutos | Contagem de pacotes de entrada nos túneis criados no gateway.   |
|**TunnelIngressPacketDropTSMismatch** | Count | 5 minutos | Contagem de pacotes de entrada descartados em túneis causados por incompatibilidade do seletor de tráfego. |


## <a name="setup"></a>Configurar alertas do Azure Monitor com base nas métricas usando o portal do Azure

As etapas de exemplo a seguir criará um alerta em um gateway para:

- **Métrica:** TunnelAverageBandwidth
- **Condição:** Largura de banda > 10 bytes / segundo
- **Janela:** 5 minutos
- **Ação de alerta:** Email



1. Vá para o recurso de gateway de rede virtual e selecione **alertas** da **monitoramento** guia. Em seguida, crie uma nova regra de alerta ou editar uma regra de alerta existente.

   ![Seleções para criar uma regra de alerta](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert1.png "criar")

2. Selecione seu gateway de VPN como o recurso.

   ![O botão de seleção e o gateway VPN na lista de recursos](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert2.png "selecione")

3. Selecione uma métrica para configurar o alerta.

   ![Selecionado métrica na lista de métricas](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert3.png "selecione")
4. Configure a lógica de sinal. Há três componentes:

    a. **Dimensões**: Se a métrica tem dimensões, você pode selecionar valores de dimensão específicos para que o alerta é avaliado somente os dados da dimensão. Estes são opcionais.

    b. **Condição**: Esta é a operação para avaliar o valor da métrica.

    c. **Tempo**: Especifica a granularidade dos dados de métrica e o período de tempo para avaliar o alerta.

   ![Detalhes para configurar sinalizam lógica](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert4.png "selecione")

5. Para exibir as regras configuradas, selecione **gerenciar regras de alerta**.

   ![Botão para gerenciar regras de alerta](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert8.png "selecione")

## <a name="next-steps"></a>Próximas etapas

Para configurar alertas em logs de diagnóstico de túnel, consulte [configurar alertas em logs de diagnóstico do Gateway de VPN](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md).
