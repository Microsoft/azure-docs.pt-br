---
title: Métricas e alertas no Gerenciador de Tráfego do Azure
description: Neste artigo, Aprenda as métricas e os alertas disponíveis para o Gerenciador de tráfego no Azure.
services: traffic-manager
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/11/2018
ms.author: duau
ms.openlocfilehash: b18e0329aeb4e95e021c3326b6b428c10edc0c6e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100586425"
---
# <a name="traffic-manager-metrics-and-alerts"></a>Alertas e métricas do Gerenciador de Tráfego

O Gerenciador de Tráfego fornece balanceamento de carga baseado em DNS, que inclui vários métodos de roteamento e opções de monitoramento de ponto de extremidade. Este artigo descreve as métricas e os alertas associados disponíveis aos clientes. 

## <a name="metrics-available-in-traffic-manager"></a>Métricas disponíveis no Gerenciador de Tráfego 

O Gerenciador de Tráfego fornece as seguintes métricas de acordo com o perfil que os clientes podem usar para entender o uso do Gerenciador de Tráfego e o status de seus pontos de extremidade nesse perfil.  

### <a name="queries-by-endpoint-returned"></a>Consultas por ponto de extremidade retornado
Use [esta métrica](../azure-monitor/essentials/metrics-supported.md) para exibir o número de consultas que um perfil do Gerenciador de Tráfego processou durante um período especificado. Também é possível exibir as mesmas informações com uma granularidade de nível de ponto de extremidade que ajuda você a entender quantas vezes um ponto de extremidade retornou nas respostas da consulta do Gerenciador de Tráfego.

No exemplo a seguir, a Figura 1 exibe todas as respostas de consulta que o perfil do Gerenciador de Tráfego retorna. 

  
![Exibição agregada de todas as consultas](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-queries-aggregate-view.png)

*Figura 1: Exibição agregada com todas as consultas*
  
A Figura 2 exibe as mesmas informações, porém, elas são divididas por pontos de extremidade. Como resultado, você pode ver o volume de respostas de consulta no qual um ponto de extremidade específico retornou.

![Métricas do Gerenciador de Tráfego - exibição dividida do volume de consulta por ponto de extremidade](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-query-volume-per-endpoint.png)

*Figura 2: Exibição dividida com volume de consulta mostrado por ponto de extremidade retornado*

## <a name="endpoint-status-by-endpoint"></a>Status do ponto de extremidade por ponto de extremidade
Use [esta métrica](../azure-monitor/essentials/metrics-supported.md#microsoftnetworktrafficmanagerprofiles) para entender o status de integridade dos pontos de extremidade no perfil. Ela usa dois valores:
 - use **1** se o ponto de extremidade estiver ativo.
 - use **0**, se o ponto de extremidade estiver inativo.

Essa métrica pode ser exibida como um valor de agregação que representa o status de todas as métricas (Figura 3), ou pode ser dividida (veja a Figura 4) para mostrar o status de pontos de extremidade específicos. Se o nível de agregação anterior estiver selecionado como **Avg**, o valor dessa métrica será a média aritmética do status de todos os pontos de extremidade. Por exemplo, se um perfil tiver dois pontos de extremidade e apenas um deles estiver íntegro, essa métrica terá um valor de **0,50**, como mostra a Figura 3. 


![Métricas do Gerenciador de Tráfego - exibição composta do status do ponto de extremidade](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-endpoint-status-composite-view.png)

*Figura 3: Exibição composta da métrica de status do ponto de extremidade – agregação "Méd" selecionada*


![Métricas do Gerenciador de Tráfego - exibição dividida do status do ponto de extremidade](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-endpoint-status-split-view.png)

*Figura 4: Exibição dividida das métricas de status do ponto de extremidade*

Você pode consumir essas métricas no portal do [serviço do Azure Monitor](../azure-monitor/essentials/metrics-supported.md), na [API REST](/rest/api/monitor/), na [CLI do Azure](/cli/azure/monitor) e no [Azure PowerShell](/powershell/module/az.applicationinsights), ou por meio da seção de métricas da experiência do portal do Gerenciador de Tráfego.

## <a name="alerts-on-traffic-manager-metrics"></a>Alertas em métricas do Gerenciador de Tráfego
Além de processar e exibir métricas do Gerenciador de Tráfego, o Azure Monitor permite aos clientes configurar e receber alertas associados a essas métricas. Você pode escolher quais condições devem ser atendidas nessas métricas para que um alerta ocorra, a frequência com que essas condições precisam ser monitoradas e como os alertas devem ser enviados a você. Para saber mais, confira a [documentação sobre os alertas do Azure Monitor](../azure-monitor/alerts/alerts-metric.md).

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre o [serviço do Azure Monitor](../azure-monitor/essentials/metrics-supported.md)
- Saiba como [criar um gráfico usando o Azure Monitor](../azure-monitor/essentials/metrics-getting-started.md#create-your-first-metric-chart)