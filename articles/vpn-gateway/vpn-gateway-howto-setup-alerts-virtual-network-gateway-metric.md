---
title: Configurar alertas em métricas de gateway de VPN do Azure
description: Saiba como usar o portal do Azure para configurar Azure Monitor alertas com base em métricas para gateways de VPN de rede virtual.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: alzam
ms.openlocfilehash: 05fbc5675d6ee3b6720d9db9e07e7010cf1d9172
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "89435650"
---
# <a name="set-up-alerts-on-vpn-gateway-metrics"></a>Configurar alertas em métricas de gateway de VPN

Este artigo ajuda você a configurar alertas em métricas de gateway de VPN do Azure. Azure Monitor fornece a capacidade de configurar alertas para recursos do Azure. Você pode configurar alertas para gateways de rede virtual do tipo "VPN".


|**Métrica**   | **Unidade** | **Granularidade** | **Descrição** | 
|---       | ---        | ---       | ---            | ---       |
|**AverageBandwidth**| Bytes/s  | 5 minutos| Utilização média da largura de banda combinada de todas as conexões site a site no gateway.     |
|**P2SBandwidth**| Bytes/s  | 1 minuto  | Utilização média da largura de banda combinada de todas as conexões ponto a site no gateway.    |
|**P2SConnectionCount**| Contagem  | 1 minuto  | Contagem de conexões ponto a site no gateway.   |
|**TunnelAverageBandwidth** | Bytes/s    | 5 minutos  | Utilização média de largura de banda de túneis criados no gateway. |
|**TunnelEgressBytes** | Bytes | 5 minutos | Tráfego de saída em túneis criados no gateway.   |
|**TunnelEgressPackets** | Contagem | 5 minutos | Contagem de pacotes de saída em túneis criados no gateway.   |
|**TunnelEgressPacketDropTSMismatch** | Contagem | 5 minutos | Contagem de pacotes de saída descartados em túneis causados por incompatibilidade de seletor de tráfego. |
|**TunnelIngressBytes** | Bytes | 5 minutos | Tráfego de entrada em túneis criados no gateway.   |
|**TunnelIngressPackets** | Contagem | 5 minutos | Contagem de pacotes de entrada em túneis criados no gateway.   |
|**TunnelIngressPacketDropTSMismatch** | Contagem | 5 minutos | Contagem de pacotes de entrada descartados em túneis causados por incompatibilidade de seletor de tráfego. |


## <a name="set-up-azure-monitor-alerts-based-on-metrics-by-using-the-azure-portal"></a><a name="setup"></a>Configurar alertas de Azure Monitor com base nas métricas usando o portal do Azure

As etapas de exemplo a seguir criarão um alerta em um gateway para:

- **Métrica:** TunnelAverageBandwidth
- **Condição:** Largura de banda > 10 bytes/segundo
- **Janela:** 5 minutos
- **Ação de alerta:** Email



1. Vá para o recurso de gateway de rede virtual e selecione **alertas** na guia **monitoramento** . Em seguida, crie uma nova regra de alerta ou edite uma regra de alerta existente.

   ![Seleções para criar uma regra de alerta](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert1.png "Criar")

2. Selecione o gateway de VPN como o recurso.

   ![O botão Selecionar e o gateway de VPN na lista de recursos](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert2.png "Selecionar")

3. Selecione uma métrica a ser configurada para o alerta.

   ![Métrica selecionada na lista de métricas](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert3.png "Selecionar")
4. Configure a lógica do sinal. Há três componentes para ele:

    a. **Dimensões**: se a métrica tiver dimensões, você poderá selecionar valores de dimensão específicos para que o alerta avalie apenas os dados dessa dimensão. Eles são opcionais.

    b. **Condição**: esta é a operação para avaliar o valor da métrica.

    c. **Hora**: Especifique a granularidade dos dados da métrica e o período de tempo para avaliar o alerta.

   ![Detalhes para configurar a lógica de sinal](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert4.png "Selecionar")

5. Para exibir as regras configuradas, selecione **gerenciar regras de alerta**.

   ![Botão para gerenciar regras de alerta](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert8.png "Selecionar")

## <a name="next-steps"></a>Próximas etapas

Para configurar alertas em logs de recursos de túnel, consulte [configurar alertas em logs de recursos do gateway de VPN](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md).
