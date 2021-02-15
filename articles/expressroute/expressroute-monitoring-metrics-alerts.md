---
title: 'Azure ExpressRoute: monitoramento, métricas e alertas'
description: Saiba mais sobre monitoramento, métricas e alertas do Azure ExpressRoute usando o Azure Monitor, a única loja para todas as métricas, alertas e logs de diagnóstico no Azure.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 01/11/2020
ms.author: duau
ms.openlocfilehash: 7a5da35da35b2f447256bc742681ccd7a7d403da
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2021
ms.locfileid: "99091537"
---
# <a name="expressroute-monitoring-metrics-and-alerts"></a>Monitoramento, alertas e métricas do ExpressRoute

Este artigo ajuda você a entender o monitoramento do ExpressRoute, métricas e alertas usando o Azure Monitor. O Azure Monitor é uma opção completa para todas as métricas, alertas e logs de diagnóstico em todo o Azure.
 
>[!NOTE]
>Usar **Métricas Clássicas** não é recomendado.
>

## <a name="expressroute-metrics"></a>Métricas de ExpressRoute

Para exibir as **métricas**, navegue até a página *Azure monitor* e clique em *métricas*. Para exibir as métricas de **expressroute** , filtre por tipo de recurso *circuitos de expressroute*. Para exibir **alcance global** métricas, filtre por tipo de recurso *circuitos do expressroute* e selecione um recurso de circuito do expressroute que tenha alcance global habilitado. Para exibir as métricas **diretas do expressroute** , filtre tipo de recurso por *portas do expressroute*. 

Depois que uma métrica for selecionada, a agregação padrão será aplicada. Opcionalmente, você pode aplicar a divisão, que mostrará a métrica com dimensões diferentes.

### <a name="available-metrics"></a>Métricas disponíveis

|**Métrica**|**Categoria**|**Dimensão (ões)**|**Recurso (s)**|
| --- | --- | --- | --- |
|Disponibilidade do ARP|Disponibilidade|<ui><li>Par (roteador de ExpressRoute primário/secundário)</ui></li><ui><li> Tipo de emparelhamento (privado/público/Microsoft)</ui></li>|ExpressRoute|
|Disponibilidade de BGP|Disponibilidade|<ui><li> Par (roteador de ExpressRoute primário/secundário)</ui></li><ui><li> Tipo de emparelhamento</ui></li>|ExpressRoute|
|BitsInPerSecond|Tráfego|<ui><li> Tipo de emparelhamento (ExpressRoute)</ui></li><ui><li>Link (ExpressRoute direto)</ui></li>|<li>ExpressRoute</li><li>ExpressRoute Direct</li><ui><li>Conexão de gateway do ExpressRoute</ui></li>|
|BitsOutPerSecond|Tráfego| <ui><li>Tipo de emparelhamento (ExpressRoute)</ui></li><ui><li> Link (ExpressRoute direto) |<ui><li>ExpressRoute<ui><li>ExpressRoute direto</ui></li><ui><li>Conexão de gateway do ExpressRoute</ui></li>|
|Utilização da CPU|Desempenho| <ui><li>Instância</ui></li>|Gateway de rede virtual do ExpressRoute|
|Pacotes por segundo|Desempenho| <ui><li>Instância</ui></li>|Gateway de rede virtual do ExpressRoute|
|Contagem de rotas anunciadas para o par |Disponibilidade| <ui><li>Instância</ui></li>|Gateway de rede virtual do ExpressRoute|
|Contagem de rotas aprendidas do par |Disponibilidade| <ui><li>Instância</ui></li>|Gateway de rede virtual do ExpressRoute|
|Frequência de alteração de rotas |Disponibilidade| <ui><li>Instância</ui></li>|Gateway de rede virtual do ExpressRoute|
|Número de VMs na rede virtual |Disponibilidade| N/D |Gateway de rede virtual do ExpressRoute|
|GlobalReachBitsInPerSecond|Tráfego|<ui><li>Circuito emparelhado skey (chave de serviço)</ui></li>|Alcance Global|
|GlobalReachBitsOutPerSecond|Tráfego|<ui><li>Circuito emparelhado skey (chave de serviço)</ui></li>|Alcance Global|
|AdminState|Conectividade física|Link|ExpressRoute Direct|
|LineProtocol|Conectividade física|Link|ExpressRoute Direct|
|RxLightLevel|Conectividade física|<ui><li>Link</ui></li><ui><li>Estreita</ui></li>|ExpressRoute Direct|
|TxLightLevel|Conectividade física|<ui><li>Link</ui></li><ui><li>Estreita</ui></li>|ExpressRoute Direct|
>[!NOTE]
>Usar *GlobalGlobalReachBitsInPerSecond* e *GlobalGlobalReachBitsOutPerSecond* só será visível se pelo menos uma conexão alcance global for estabelecida.
>

## <a name="circuits-metrics"></a>Métricas de circuitos

### <a name="bits-in-and-out---metrics-across-all-peerings"></a>Bits de entrada e saída-métricas em todos os emparelhamentos

Você pode exibir as métricas em todos os emparelhamentos em um determinado circuito do ExpressRoute.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/ermetricspeering.jpg" alt-text="métricas de circuito":::

### <a name="bits-in-and-out---metrics-per-peering"></a>Métricas de entrada e saída de bits por emparelhamento

Você pode exibir métricas para emparelhamento privado, público e da Microsoft em bits por segundo.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erpeeringmetrics.jpg" alt-text="métricas por emparelhamento":::

### <a name="bgp-availability---split-by-peer"></a>Disponibilidade de BGP-divisão por par  

Você pode exibir quase a disponibilidade em tempo real do BGP entre emparelhamentos e pares (roteadores de ExpressRoute primários e secundários). Esse painel mostra a sessão BGP primária para emparelhamento privado e a segunda sessão BGP para emparelhamento privado. 

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erBgpAvailabilityMetrics.jpg" alt-text="Disponibilidade de BGP por ponto":::

### <a name="arp-availability---split-by-peering"></a>Disponibilidade ARP-divisão por emparelhamento  

Você pode exibir quase a disponibilidade em tempo real do [ARP](./expressroute-troubleshooting-arp-resource-manager.md) entre emparelhamentos e pares (roteadores de ExpressRoute primários e secundários). Este painel mostra a sessão ARP de emparelhamento privado em ambos os pares, mas é concluída para o emparelhamento da Microsoft entre emparelhamentos. A agregação padrão (média) foi utilizada em ambos os pares.  

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erArpAvailabilityMetrics.jpg" alt-text="Disponibilidade ARP por ponto":::

## <a name="expressroute-direct-metrics"></a>Métricas diretas do ExpressRoute

### <a name="admin-state---split-by-link"></a>Estado do administrador – dividir por link

Você pode exibir o estado do administrador para cada link do par de porta do ExpressRoute Direct.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/adminstate-per-link.jpg" alt-text="Estado de administrador direto ER":::

### <a name="bits-in-per-second---split-by-link"></a>Bits por segundo – divisão por link

Você pode exibir os bits em por segundo em ambos os links do par de portas diretas do ExpressRoute.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/bits-in-per-second-per-link.jpg" alt-text="Bits diretos ER em por segundo":::

### <a name="bits-out-per-second---split-by-link"></a>Bits de saída por segundo-divisão por link

Você também pode exibir os bits de saída por segundo em ambos os links do par de portas diretas do ExpressRoute.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/bits-out-per-second-per-link.jpg" alt-text="Bits diretos de ER de saída por segundo":::

### <a name="line-protocol---split-by-link"></a>Protocolo de linha – dividir por link

Você pode exibir o protocolo de linha em cada link do par de porta do ExpressRoute Direct.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/line-protocol-per-link.jpg" alt-text="Protocolo de linha direta ER":::

### <a name="rx-light-level---split-by-link"></a>Nível de luz Rx-divisão por link

Você pode exibir o nível de luz Rx (o nível de luz que a porta do ExpressRoute Direct está **recebendo**) para cada porta. Os níveis de luz Rx em boas condições geralmente se enquadram em um intervalo de-10 a 0 dBm

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/rxlight-level-per-link.jpg" alt-text="Nível de luz Rx de linha direta ER":::

### <a name="tx-light-level---split-by-link"></a>Nível de luz Tx – dividir por link

Você pode exibir o nível de luz Tx (o nível de luz que a porta do ExpressRoute Direct está **transmitindo**) para cada porta. Os níveis de luz Tx íntegros geralmente se enquadram dentro de um intervalo de-10 a 0 dBm

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/txlight-level-per-link.jpg" alt-text="Nível de luz Tx da linha direta ER":::

## <a name="expressroute-virtual-network-gateway-metrics"></a>Métricas do gateway de rede virtual ExpressRoute

### <a name="cpu-utilization---split-instance"></a>Utilização da CPU – dividir instância

Você pode exibir a utilização da CPU das instâncias de gateway.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/cpu-split.jpg" alt-text="Divisão de CPU":::

### <a name="packets-per-second---split-by-instance"></a>Pacotes por segundo-divisão por instância

Você pode exibir pacotes por segundo atravessando o gateway.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/pps-split.jpg" alt-text="Pacotes por segundo-divisão":::

### <a name="count-of-routes-advertised-to-peer---split-by-instance"></a>Contagem de rotas anunciadas para divisão por pares por instância

Você pode exibir o número de rotas anunciadas para o circuito do ExpressRoute.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/count-of-routes-advertised-to-peer.png" alt-text="Contagem de rotas anunciadas para o par":::

### <a name="count-of-routes-learned-from-peer---split-by-instance"></a>Contagem de rotas aprendidas da instância de divisão por pares

Você pode exibir o número de rotas recebidas do circuito do ExpressRoute.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/count-of-routes-learned-from-peer.png" alt-text="Contagem de rotas aprendidas do par":::

### <a name="frequency-of-routes-change---split-by-instance"></a>Frequência de rotas de alteração – divisão por instância

Você pode exibir a frequência da alteração da rota no gateway.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/frequency-of-routes-changed.png" alt-text="Frequência de rotas alteradas":::

### <a name="number-of-vms-in-the-virtual-network"></a>Número de VMs na rede virtual

Você pode exibir o número de máquinas virtuais na rede virtual.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/number-of-virtual-machines-virtual-network.png" alt-text="Número de máquinas virtuais na rede virtual":::

## <a name="expressroute-gateway-connections-in-bitsseconds"></a>Conexões de gateway do ExpressRoute em bits por segundos

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erconnections.jpg" alt-text="conexões de gateway":::

## <a name="alerts-for-expressroute-gateway-connections"></a>Alertas para conexões de gateway do ExpressRoute

1. Para configurar alertas, navegue até **Azure monitor** e, em seguida, selecione **alertas**.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/eralertshowto.jpg" alt-text="alertas":::
2. Clique em **+ Selecionar destino** e selecione o recurso de conexão de gateway do ExpressRoute.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alerthowto2.jpg" alt-text="destino":::
3. Defina os detalhes do alerta.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alerthowto3.jpg" alt-text="grupo de ação":::
4. Defina e adicione o grupo de ações.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/actiongroup.png" alt-text="Adicionar grupo de ação":::

## <a name="alerts-based-on-each-peering"></a>Alertas com base em cada emparelhamento

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/basedpeering.jpg" alt-text="cada emparelhamento":::

## <a name="configure-alerts-for-activity-logs-on-circuits"></a>Configurar alertas para os logs de atividade em circuitos

Nos **Critérios de Alerta**, você pode selecionar **Log de Atividades** para o tipo de sinal e selecione o sinal.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alertshowto6activitylog.jpg" alt-text="logs de atividade":::

## <a name="additional-metrics-in-log-analytics"></a>Métricas adicionais no Log Analytics

Você também pode exibir as métricas de ExpressRoute navegando até o recurso de circuito do ExpressRoute e selecionando a guia *logs* . Para qualquer métrica que você consultar, a saída conterá as colunas abaixo.

|**Coluna**|**Tipo**|**Descrição**|
| --- | --- | --- |
|TimeGrain|string|PT1M (os valores de métrica são enviados a cada minuto)|
|Contagem|real|Geralmente igual a 2 (cada MSEE envia um único valor de métrica a cada minuto)|
|Mínimo|real|O mínimo dos dois valores de métrica enviados por Push por dois MSEEs|
|Máximo|real|O máximo dos dois valores de métrica enviados por Push por dois MSEEs|
|Média|real|Igual a (mínimo + máximo)/2|
|Total|real|Soma dos dois valores de métrica de ambos os MSEEs (o valor principal para se concentrar para a métrica consultada)|
  
## <a name="next-steps"></a>Próximas etapas

Configurar sua conexão do ExpressRoute.
  
* [Criar e modificar um circuito](expressroute-howto-circuit-arm.md)
* [Criar e modificar a configuração de emparelhamento](expressroute-howto-routing-arm.md)
* [Vincular uma rede virtual a um circuito do ExpressRoute](expressroute-howto-linkvnet-arm.md)
