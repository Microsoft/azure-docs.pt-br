---
title: Métricas no Azure Monitor | Microsoft Docs
description: Descreve as métricas no Azure Monitor que são leves de dados capazes de dar suporte a quase em tempo real cenários de monitoramento.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: 684491b546a0456d936ae199cdfb93180aa05043
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67607037"
---
# <a name="metrics-in-azure-monitor"></a>Métricas no Azure Monitor

> [!NOTE]
> A plataforma de dados do Azure Monitor se baseia em dois tipos de dados fundamentais: Métricas e Logs. Este artigo descreve as métricas. Consulte a [registra em log no Azure Monitor](data-platform-logs.md) para obter uma descrição detalhada de logs e a [plataforma de dados do Azure Monitor](data-platform.md) para obter uma comparação dos dois.

Métricas no Azure Monitor são leves e capaz de dar suporte a cenários em tempo real, tornando-os particularmente útil para quase detecção de alerta e rápida de problemas. Este artigo descreve como as métricas são estruturadas, o que você pode fazer com eles e identifica diferentes fontes de dados que armazenam dados em métricas.

## <a name="what-are-metrics"></a>O que são métricas?
Métricas são valores numéricos que descrevem algum aspecto de um sistema em um momento específico. As métricas são coletadas em intervalos regulares e são úteis para alertas, porque podem ser utilizados com frequência, e um alerta pode ser acionado rapidamente com a lógica relativamente simple.

## <a name="what-can-you-do-with-azure-monitor-metrics"></a>O que você pode fazer com métricas do Azure Monitor?
A tabela a seguir lista as diferentes maneiras que você pode usar dados de métrica no Azure Monitor.

|  |  |
|:---|:---|
| Analisar | Use [do metrics explorer](metrics-charts.md) para analisar as métricas coletadas em um gráfico e comparar as métricas de recursos diferentes. |
| Visualizar | Fixar um gráfico do Gerenciador de métricas para um [painel do Azure](../learn/tutorial-app-dashboards.md).<br>Criar uma [pasta de trabalho](../app/usage-workbooks.md) para combinar com vários conjuntos de dados em um relatório interativo. Exportar os resultados de uma consulta para [Grafana](grafana-plugin.md) aproveitar seus painéis e combine com outras fontes de dados. |
| Alerta | Configurar uma [regra de alerta de métrica](alerts-metric.md) que envia uma notificação ou leva [ação automatizada](action-groups.md) quando o valor da métrica ultrapassa um limite. |
| Automatizar |  Use [AutoEscala](autoscale-overview.md) para aumentar ou diminuir os recursos com base em um valor de métrica cruzar um limite. |
| Exportação | [Rotear as métricas para os Logs de](diagnostic-logs-stream-log-store.md) para analisar dados do Azure Monitor Metrics junto com dados em Logs do Azure Monitor e para armazenar valores de métrica por mais de 93 dias.<br>Stream métricas para um [Hub de eventos](stream-monitoring-data-event-hubs.md) roteá-las a sistemas externos. |
| Recuperar | Acessar valores de métrica de uma linha de comando usando [cmdlets do PowerShell](https://docs.microsoft.com/powershell/module/az.applicationinsights)<br>Acessar valores de métrica de aplicativo personalizado usando [API REST](rest-api-walkthrough.md).<br>Acessar valores de métrica de uma linha de comando usando [CLI](/cli/azure/monitor/metrics). |
| Arquivo | [Arquive](..//learn/tutorial-archive-data.md) o histórico de desempenho ou integridade do recurso para fins de conformidade, auditoria ou geração de relatórios offline. |

## <a name="how-is-data-in-azure-monitor-metrics-structured"></a>É como os dados do Azure Monitor Metrics estruturada?
Dados coletados pelo Azure Monitor métricas são armazenados em um banco de dados de série temporal que é otimizado para análise de dados de carimbo de data / hora. Cada conjunto de valores de métrica é uma série de tempo com as seguintes propriedades:

* A hora em que o valor foi coletado
* O valor do recurso está associado.
* Um namespace que atua como uma categoria para a métrica
* Um nome de métrica
* O próprio valor
* Algumas métricas podem ter várias dimensões, conforme descrito em [métricas multidimensionais](#multi-dimensional-metrics). As métricas personalizadas podem ter até 10 dimensões.

## <a name="multi-dimensional-metrics"></a>Métricas multidimensionais
Um dos desafios para os dados de métrica é que ela geralmente tem informações limitadas para fornecer contexto para valores coletados. O Azure Monitor resolve esse desafio com métricas multidimensionais. Dimensões de uma métrica são pares nome-valor que contêm dados adicionais para descrever o valor da métrica. Por exemplo, uma métrica _espaço em disco disponível_ poderia ter uma dimensão chamada _unidade_ com valores _c:_ , _unidade d:_ , que permitiria a exibição o espaço em disco disponível em todas as unidades ou para cada unidade individualmente.

O exemplo abaixo ilustra dois conjuntos de dados de uma métrica hipotética chamada _Taxa de Transferência de Rede_. O primeiro conjunto de dados não tem nenhuma dimensão. O segundo conjunto de dados mostra os valores com duas dimensões, _Endereço IP_ e _Direção_:

### <a name="network-throughput"></a>Taxa de Transferência de Rede

| Timestamp     | Valor da Métrica |
| ------------- |:-------------|
| 9/8/2017 8h14 | 1\.331,8 Kbps |
| 9/8/2017 8h15 | 1\.141,4 Kbps |
| 9/8/2017 8h16 | 1\.110,2 Kbps |

Essa métrica não dimensional pode responder apenas a uma pergunta básica, como “qual era minha taxa de transferência de rede em determinado horário?”

### <a name="network-throughput--two-dimensions-ip-and-direction"></a>Taxa de Transferência de Rede + duas dimensões (“IP” e “Direção”)

| Timestamp     | Dimensão “IP”   | Dimensão “Direção” | Valor da Métrica|
| ------------- |:-----------------|:------------------- |:-----------|
| 9/8/2017 8h14 | IP="192.168.5.2" | Direction="Send"    | 646,5 Kbps |
| 9/8/2017 8h14 | IP="192.168.5.2" | Direction="Receive" | 420,1 Kbps |
| 9/8/2017 8h14 | IP="10.24.2.15"  | Direction="Send"    | 150,0 Kbps |
| 9/8/2017 8h14 | IP="10.24.2.15"  | Direction="Receive" | 115,2 Kbps |
| 9/8/2017 8h15 | IP="192.168.5.2" | Direction="Send"    | 515,2 Kbps |
| 9/8/2017 8h15 | IP="192.168.5.2" | Direction="Receive" | 371,1 Kbps |
| 9/8/2017 8h15 | IP="10.24.2.15"  | Direction="Send"    | 155,0 Kbps |
| 9/8/2017 8h15 | IP="10.24.2.15"  | Direction="Receive" | 100,1 Kbps |

Essa métrica pode responder a perguntas como “qual era a taxa de transferência de rede para cada endereço IP?” e “quantos dados foram enviados vs. recebidos?” As métricas multidimensionais trazem um valor analítico e de diagnóstico adicional em comparação às métricas não dimensionais.

## <a name="interacting-with-azure-monitor-metrics"></a>Interagindo com as métricas do Azure Monitor
Use [Metrics Explorer](metrics-charts.md) interativamente para analisar os dados no seu banco de dados de métrica e os valores de várias métricas do gráfico ao longo do tempo. Você pode fixar os gráficos em um painel para exibi-los com outras visualizações. Também é possível recuperar métricas usando a [API REST de monitoramento do Azure](rest-api-walkthrough.md).

![Metrics Explorer](media/data-platform/metrics-explorer.png)

## <a name="sources-of-azure-monitor-metrics"></a>Fontes de métricas do Azure Monitor
Há três fontes fundamentais de métricas coletadas pelo Azure Monitor. Depois que essas métricas são coletadas no banco de dados de métrica do Azure Monitor, eles podem ser avaliados em conjunto, independentemente de sua origem.

As **métricas de plataforma** são criadas pelos recursos do Azure e esclarecem a integridade e o desempenho. Cada tipo de recurso cria um [conjunto distinto de métricas](metrics-supported.md) sem a necessidade de configuração. Métricas de plataforma são coletadas dos recursos do Azure com uma frequência de um minuto, a menos que especificado de outra forma na definição da métrica. 

**Métricas de SO convidado** são coletadas do sistema operacional convidado de uma máquina virtual. Habilitar as métricas de sistema operacional convidado para máquinas de virtuais do Windows com [extensão de diagnóstico do Windows (WAD)](../platform/diagnostics-extension-overview.md) e para máquinas virtuais Linux com [InfluxData Telegraf agente](https://www.influxdata.com/time-series-platform/telegraf/).

As **métricas de aplicativo** são criadas pelo Application Insights para seus aplicativos monitorados e ajudam você a detectar problemas de desempenho e acompanhar as tendências de como o aplicativo está sendo usado. Isso inclui valores como _Tempo de resposta do servidor_ e _Exceções de navegador_.

**Métricas personalizadas** são métricas que você define além das métricas padrão que estão disponíveis automaticamente. Você pode [definir métricas personalizadas em seu aplicativo](../app/api-custom-events-metrics.md) que é monitorado pelo Application Insights ou criar métricas personalizadas para um serviço do Azure usando o [métricas personalizadas API](metrics-store-custom-rest-api.md).

## <a name="retention-of-metrics"></a>Retenção de métricas
Para a maioria dos recursos no Azure, as métricas são armazenadas para 93 dias. Há algumas exceções:
  * **Métricas de SO convidado clássicas**. Métricas do SO convidado clássica são retidas por 14 dias. Para uma retenção maior, é recomendável usar novas métricas de sistema operacional convidado que são coletadas com [extensão de diagnóstico do Windows (WAD)](../platform/diagnostics-extension-overview.md) e para máquinas virtuais Linux com [InfluxData Telegraf agente](https://www.influxdata.com/time-series-platform/telegraf/).
  * **Métricas com base em log do aplicativo Insights**. Por trás da cena [métricas baseadas em log](../app/pre-aggregated-metrics-log-metrics.md) traduzir consultas de log. Sua retenção corresponde a retenção de eventos nos logs subjacentes. Para recursos do Application Insights, os logs são armazenados por 90 dias. 

> [!NOTE]
> Você pode [enviar métricas de plataforma para recursos do Azure Monitor para um espaço de trabalho do Log Analytics](diagnostic-logs-stream-log-store.md) para análise de tendências de longo prazo.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [plataforma de dados do Azure Monitor](data-platform.md).
- Saiba mais sobre [dados de log no Azure Monitor](data-platform-logs.md).
- Saiba mais sobre os [dados de monitoramento disponíveis](data-sources.md) para diferentes recursos no Azure.
