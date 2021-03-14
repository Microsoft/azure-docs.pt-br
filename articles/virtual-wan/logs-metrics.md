---
title: Logs e métricas
titleSuffix: Azure Virtual WAN
description: Saiba mais sobre os logs e as métricas da WAN virtual do Azure
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 06/05/2020
ms.author: cherylmc
ms.openlocfilehash: a5bbcc4c07826a5093da3f51c709f49ceb231753
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100596074"
---
# <a name="azure-virtual-wan-logs-and-metrics"></a>Métricas e logs de WAN virtual do Azure

Você pode monitorar a WAN virtual do Azure usando o Azure Monitor. A WAN virtual é um serviço de rede que reúne muitas funcionalidades de rede, segurança e roteamento para fornecer uma única interface operacional. Gateways de VPN de WAN virtual, gateways de ExpressRoute e firewall do Azure têm registro em log e métricas disponíveis por meio de Azure Monitor.

Este artigo discute métricas e diagnósticos que estão disponíveis por meio do Portal. As métricas são leves e podem dar suporte a cenários quase em tempo real, tornando-as úteis para alertas e detecção rápida de problemas.

## <a name="monitoring-secured-hub-azure-firewall"></a>Monitorando o Hub protegido (firewall do Azure) 

Você pode monitorar o Hub protegido usando os logs de firewall do Azure. Você também pode usar os logs de atividades para auditar operações nos recursos do Firewall do Azure.

Se você optou por proteger seu hub virtual usando o Firewall do Azure, os logs e as métricas relevantes estão disponíveis aqui: [logs e métricas de firewall do Azure](../firewall/logs-and-metrics.md).

## <a name="metrics"></a>Métricas

As métricas em Azure Monitor são valores numéricos que descrevem algum aspecto de um sistema em um determinado momento. As métricas são coletadas a cada minuto e são úteis para alertas porque podem ser amostradas com frequência. Um alerta pode ser acionado rapidamente com lógica relativamente simples.

### <a name="site-to-site-vpn-gateways"></a>Gateways de VPN site a site

As métricas a seguir estão disponíveis para gateways de VPN site a site do Azure:

* **Largura de banda do gateway** – a média de largura de banda agregada site a site de um gateway em bytes por segundo.
* **Largura de banda de túnel** – largura de banda média de um túnel em bytes por segundo.
* **Bytes de saída de túnel** – bytes de saída de um túnel. 
* **Pacotes de saída de túnel** – contagem de pacotes de saída de um túnel. 
* **Descarte de pacotes incompatíveis TS de saída de túnel** – contagem de eliminação de pacotes de saída da incompatibilidade de seletor de tráfego de um túnel. 
* Bytes de entrada de **encapsulamento** – bytes recebidos de um túnel. 
* **Pacote de entrada de túnel** – contagem de pacotes de entrada de um túnel. 
* **Descarte de pacotes incompatíveis TS** de entrada de túnel – a contagem de eliminação de pacotes de entrada da incompatibilidade de seletor de tráfego de um túnel. 

### <a name="point-to-site-vpn-gateways"></a>Gateways de VPN ponto a site

As métricas a seguir estão disponíveis para gateways de VPN de ponto a site do Azure:

* **Largura de banda do gateway P2S** – largura de banda agregada ponto a site média de um gateway em bytes por segundo.
* **Contagem de conexões P2S** – contagem de conexões ponto a site de um gateway.

### <a name="azure-expressroute-gateways"></a>Gateways de ExpressRoute do Azure

As métricas a seguir estão disponíveis para gateways do Azure ExpressRoute:

* **BitsInPerSecond** – bits de ingresso no Azure por segundo.
* **BitsOutPerSecond** – bits de saída do Azure por segundo.

### <a name="view-gateway-metrics"></a><a name="metrics-steps"></a>Exibir métricas do gateway

As etapas a seguir ajudam a localizar e exibir as métricas:

1. No portal, navegue até o Hub virtual que tem o gateway.

2. Selecione **VPN (site a site)** para localizar um gateway site a site, **ExpressRoute** para localizar um gateway de ExpressRoute ou VPN de **usuário (ponto a site)** para localizar um gateway de ponto a site. Na página, você pode ver as informações do gateway. Copie essas informações. Você o usará posteriormente para exibir o diagnóstico usando Azure Monitor.

3. Selecione **Métricas**.

   :::image type="content" source="./media/logs-metrics/metrics.png" alt-text="Captura de tela mostra um painel V P N do site a site com o modo de exibição Azure Monitor selecionado.":::

4. Na página **métricas** , você pode exibir as métricas nas quais está interessado.

   :::image type="content" source="./media/logs-metrics/metrics-page.png" alt-text="Captura de tela que mostra a página ' métricas ' com as categorias realçadas.":::

## <a name="diagnostic-logs"></a><a name="diagnostic"></a>Logs de diagnóstico

### <a name="site-to-site-vpn-gateways"></a>Gateways de VPN site a site

Os seguintes diagnósticos estão disponíveis para gateways de VPN site a site do Azure:

* **Logs de diagnóstico de gateway** – diagnósticos específicos de gateway, como integridade, configuração, atualizações de serviço, bem como diagnósticos adicionais.
* **Logs de diagnóstico de túnel** – são logs relacionados ao túnel IPSec, como eventos de conexão e desconexão para um túnel IPSec site a site, SAS negociada, motivos de desconexão, bem como diagnósticos adicionais.
* **Rotear logs de diagnóstico** – esses são logs relacionados a eventos para rotas estáticas, BGP, atualizações de rota, bem como diagnósticos adicionais.
* **Logs de diagnóstico Ike** – diagnósticos específicos de Ike para conexões IPSec.

### <a name="point-to-site-vpn-gateways"></a>Gateways de VPN ponto a site

Os seguintes diagnósticos estão disponíveis para gateways de VPN de ponto a site do Azure:

* **Logs de diagnóstico de gateway** – diagnósticos específicos de gateway, como integridade, configuração, atualizações de serviço, bem como outros diagnósticos.
* **Logs de diagnóstico Ike** – diagnósticos específicos de Ike para conexões IPSec.
* **Logs de diagnóstico do P2S** – são os eventos de cliente e a configuração de P2S de usuário (ponto a site). Eles incluem conexão/desconexão de cliente, alocação de endereço de cliente VPN, bem como outros diagnósticos.

### <a name="view-diagnostic-logs"></a><a name="diagnostic-steps"></a>Exibir logs de diagnóstico

As etapas a seguir ajudam a localizar e exibir o diagnóstico:

1. No portal, navegue até o recurso de WAN virtual. Na seção **visão geral** da página Wan virtual no portal, selecione **Essentials** para expandir a exibição e obter informações do grupo de recursos. Copie as informações do grupo de recursos.

   :::image type="content" source="./media/logs-metrics/3.png" alt-text="Captura de tela que mostra a seção ' visão geral ' com uma seta apontando para o botão ' copiar '.":::

2. Na seção monitoramento, navegue até o grupo de recursos. Selecione **configurações de diagnóstico** e, em seguida, insira as informações do recurso. Essas são as informações de recurso que você copiou na etapa 2 da seção [Exibir métricas de gateway](#metrics-steps) , anteriormente neste artigo.

   :::image type="content" source="./media/logs-metrics/4.png" alt-text="Captura de tela que mostra a seção &quot;monitoramento&quot; com uma seta apontando para a lista suspensa &quot;recurso&quot;.":::

3. Na página resultados, selecione **+ Adicionar configuração de diagnóstico** e, em seguida, selecione uma opção. Você pode optar por enviar para Log Analytics, transmitir para um hub de eventos ou simplesmente arquivar em uma conta de armazenamento.

   :::image type="content" source="./media/logs-metrics/5.png" alt-text="página de métricas":::

### <a name="log-analytics-sample-query"></a><a name="sample-query"></a>Log Analytics consulta de exemplo

Os logs estão localizados no **espaço de trabalho log Analytics do Azure**. Você pode configurar uma consulta no Log Analytics. O exemplo a seguir contém uma consulta para obter o diagnóstico de rota site a site.

```AzureDiagnostics | where Category == "RouteDiagnosticLog"```

Substitua os valores abaixo, após o **= =**, conforme necessário.

* "GatewayDiagnosticLog"
* "IKEDiagnosticLog"
* "P2SDiagnosticLog"
* "TunnelDiagnosticLog"
* "RouteDiagnosticLog"

## <a name="activity-logs"></a><a name="activity-logs"></a>Logs de atividade

As entradas do **log de atividades** são coletadas por padrão e podem ser exibidas no portal do Azure. Você pode usar os logs de atividades do Azure (anteriormente conhecidos como logs *operacionais* e *logs de auditoria*) para exibir todas as operações enviadas à sua assinatura do Azure.

## <a name="next-steps"></a>Próximas etapas

* Para saber como monitorar as métricas e logs do Firewall do Azure, veja [Tutorial: Monitorar os logs de Firewall do Azure](../firewall/firewall-diagnostics.md).
* Para saber mais sobre as métricas em Azure Monitor, consulte [métricas em Azure monitor](../azure-monitor/essentials/data-platform-metrics.md).
