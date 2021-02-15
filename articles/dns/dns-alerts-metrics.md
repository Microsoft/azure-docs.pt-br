---
title: Métricas e alertas-DNS do Azure
description: Com este roteiro de aprendizagem, comece com as métricas e os alertas de DNS do Azure.
services: dns
documentationcenter: na
author: rohinkoul
manager: kumudD
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/17/2018
ms.author: rohink
ms.openlocfilehash: 85b681cbeb1b06784529e321c9e907ab2bdecb73
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94954640"
---
# <a name="azure-dns-metrics-and-alerts"></a>Métricas e alertas DNS do Azure
O DNS do Azure é um serviço de hospedagem para domínios DNS que fornece a resolução de nomes usando a infraestrutura do Microsoft Azure. Este artigo descreve as métricas e alertas para o serviço DNS do Azure.

## <a name="azure-dns-metrics"></a>Métricas de DNS do Azure

O DNS do Azure fornece métricas para os clientes permitirem que monitorem aspectos específicos de suas zonas DNS hospedadas no serviço. Além disso, com as métricas de DNS do Azure, você pode configurar e receber alertas com base nas condições de interesse. As métricas são fornecidas por meio de [serviço do Azure Monitor](../azure-monitor/index.yml). O DNS do Azure fornece as seguintes métricas por meio do Azure Monitor para as zonas DNS:

-   QueryVolume
-   RecordSetCount
-   RecordSetCapacityUtilization

Você também pode ver a [definição dessas métricas](../azure-monitor/platform/metrics-supported.md#microsoftnetworkdnszones) na página de documentação do Azure Monitor.
>[!NOTE]
> Neste momento, essas métricas estão disponíveis somente para as zonas de DNS públicas hospedadas no DNS do Azure. Se você tiver zonas privadas hospedadas no DNS do Azure, essas métricas não fornecerão dados para essas zonas. Além disso, o recurso de métricas e alertas só tem suporte na nuvem pública do Azure. O suporte para nuvens soberanas seguirá em um momento posterior. 

O elemento mais granular para o qual você pode ver as métricas é uma zona DNS. No momento, não é possível ver métricas para registros de recursos individuais em uma zona.

### <a name="query-volume"></a>Volume de consulta

A métrica *Volume de consulta* no DNS do Azure mostra o volume de consultas DNS (tráfego de consulta) que é recebido pelo DNS do Azure para a zona DNS. A unidade de medida é a contagem e a agregação é o total de todas as consultas recebidas por um período de tempo. 

Para exibir essa métrica, selecione a experiência do Metrics Explorer (versão prévia) na guia Monitor no portal do Azure. Selecione a zona DNS da lista suspensa Recurso, selecione a métrica de Volume de Consulta e selecione Soma como a agregação. A captura de tela abaixo mostra um exemplo.  Para obter mais informações sobre os gráficos e a experiência do Metrics Explorer, consulte [Azure Monitor Metrics Explorer](../azure-monitor/platform/metrics-charts.md).

![Volume de consulta](./media/dns-alerts-metrics/dns-metrics-query-volume.png)

*Figura: Métricas de volume de consultas de DNS do Azure*

### <a name="record-set-count"></a>Contagem de Conjuntos de Registros
A métrica *Contagem de conjunto de registros* mostra o número de conjuntos de registros no DNS do Azure para a zona DNS. Todos os conjuntos de registros definidos na sua zona são contados. A unidade de medida é a contagem e a agregação é o número máximo de todos os conjuntos de registros. Para exibir essa métrica, selecione experiência do Gerenciador de **métricas (versão prévia)** na guia **monitorar** na portal do Azure. Selecione a zona DNS da lista suspensa **Recurso**, selecione a métrica de **Contagem de conjunto de registros** e selecione **Max** como a **agregação**. Para obter mais informações sobre os gráficos e a experiência do Metrics Explorer, consulte [Azure Monitor Metrics Explorer](../azure-monitor/platform/metrics-charts.md). 

![Contagem de Conjuntos de Registros](./media/dns-alerts-metrics/dns-metrics-record-set-count.png)

*Figura: Métricas de contagem de conjunto do registro de DNS do Azure*


### <a name="record-set-capacity-utilization"></a>Utilização de capacidade de Conjuntos de Registros
A métrica *Utilização da capacidade de definir registro* no DNS do Azure mostra a porcentagem de utilização da capacidade do conjunto de registros para uma zona DNS. Cada zona DNS no DNS do Azure está sujeita a um limite de conjunto de registros que define o número máximo de conjuntos de registros que são permitidos para a zona (consulte [limites DNS](dns-zones-records.md#limits)). Portanto, esta métrica mostra quão próximo você está de atingir o limite de conjunto de registros. Por exemplo, se você tem 500 conjuntos de registros configurados para a zona DNS e a zona tem o limite de conjunto de registros padrão de 5000, a métrica de RecordSetCapacityUtilization mostrará o valor de 10% (que é obtido pela divisão 500 a 5000). A unidade de medida é **Porcentagem** e o tipo de **agregação** é **Máximo**. Para exibir essa métrica, selecione a experiência do Metrics Explorer (versão prévia) na guia Monitor no portal do Azure. Selecione a zona DNS da lista suspensa Recurso, selecione a métrica de Utilização da capacidade de conjunto de registros e selecione Max como a Agregação. A captura de tela abaixo mostra um exemplo. Para obter mais informações sobre os gráficos e a experiência do Metrics Explorer, consulte [Azure Monitor Metrics Explorer](../azure-monitor/platform/metrics-charts.md). 

![Captura de tela que whows um exemplo de como exibir métricas.](./media/dns-alerts-metrics/dns-metrics-record-set-capacity-uitlization.png)

*Figura: métricas de utilização da capacidade de conjunto de registros de DNS do Azure*

## <a name="alerts-in-azure-dns"></a>Alertas no DNS do Azure
O Azure Monitor fornece a capacidade de alerta com base nos valores de métrica disponíveis. As métricas DNS estão disponíveis na nova experiência de configuração de alertas. Conforme descrito em detalhes na [documentação de alertas do Azure Monitor](../azure-monitor/platform/alerts-metric.md), você pode selecionar a zona DNS como recurso, escolhes o tipo de sinal de métrica e configurar a lógica de alerta e outros parâmetros, como **período** e **frequência**. Você pode definir também um [grupo de ações](../azure-monitor/platform/action-groups.md) para quando ocorrer a condição de alerta, pelo qual o alerta será feito por meio de ações escolhidas. Para obter mais informações sobre como configurar alertas para métricas de Azure Monitor, consulte [Criar, exibir e gerenciar alertas usando o Azure Monitor](../azure-monitor/platform/alerts-metric.md). 

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre o [DNS do Azure](dns-overview.md).