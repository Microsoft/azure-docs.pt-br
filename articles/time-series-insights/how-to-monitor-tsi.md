---
title: Time Series Insights de monitoramento | Microsoft Docs
description: Monitore Time Series Insights para disponibilidade, desempenho e operação.
author: deepakpalled
ms.author: lyhughes
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/10/2020
ms.custom: lyrana
ms.openlocfilehash: 3fc880e238c1c9f45c663975470a6ab57267a648
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98879537"
---
# <a name="monitoring-time-series-insights"></a>Time Series Insights de monitoramento

Quando você tem aplicativos e processos de negócios críticos que dependem de recursos do Azure, recomendamos monitorar a disponibilidade, o desempenho e a operação desses recursos. Este artigo descreve os dados de monitoramento gerados pelo Time Series Insights e como você pode usar os recursos do Azure Monitor para analisar e alertar sobre esses dados.

## <a name="monitor-overview"></a>Visão geral do Monitor

A página de **visão geral** no portal do Azure para cada ambiente de time Series insights inclui uma breve exibição do uso de recursos, como o número de mensagens recebidas e o número de bytes armazenados. Essas informações são úteis, no entanto, apenas uma pequena quantidade de dados de monitoramento está disponível neste painel. Alguns desses dados são coletados automaticamente e estão disponíveis para análise assim que você criar o recurso. Você pode habilitar tipos adicionais de coleta de dados com algumas configurações.

## <a name="what-is-azure-monitor"></a>O que é Azure Monitor

Time Series Insights cria dados de monitoramento usando [Azure monitor](../azure-monitor/overview.md), que é um serviço de monitoramento de pilha completo no Azure que fornece um conjunto completo de recursos para monitorar os recursos do Azure, além de recursos em outras nuvens e locais.

Comece com o artigo [monitorando os recursos do Azure com Azure monitor](../azure-monitor/insights/monitor-azure-resource.md), que descreve os seguintes conceitos:

- O que é o Azure Monitor?
- Custos associados ao monitoramento
- Monitoramento de dados coletados no Azure
- Configuração de coleta de dados
- Ferramentas padrão no Azure para analisar e alertar sobre dados de monitoramento

As seções a seguir se baseiam neste artigo descrevendo os dados específicos coletados para Azure Time Series Insights. Essas seções também fornecem exemplos para configurar a coleta de dados e analisar esses dados com as ferramentas do Azure.

> [!TIP]
> Para entender os custos associados a Azure Monitor, confira [uso e custos estimados](../azure-monitor/platform/usage-estimated-costs.md). Para entender o tempo que leva para que seus dados apareçam em Azure Monitor, consulte [tempo de ingestão de dados de log](../azure-monitor/platform/data-ingestion-time.md).

## <a name="monitoring-data-from-azure-time-series-insights"></a>Monitorando dados de Azure Time Series Insights

Azure Time Series Insights coleta os mesmos tipos de dados de monitoramento que outros recursos do Azure descritos em [monitoramento de dados de recursos do Azure](../azure-monitor/insights/monitor-azure-resource.md#monitoring-data). 

Veja [Azure Time Series insights referência de dados de monitoramento](how-to-monitor-tsi-reference.md) para obter uma referência detalhada dos logs e das métricas que você pode coletar.

## <a name="collection-and-routing"></a>Coleta e roteamento

As métricas de plataforma são coletadas e armazenadas automaticamente, mas podem ser roteadas para outros locais usando uma configuração de diagnóstico.

Os logs de recursos não são coletados e armazenados até você criar uma configuração de diagnóstico e roteá-los para um ou mais locais.
Consulte [criar configuração de diagnóstico para coletar logs e métricas de plataforma no Azure](../azure-monitor/platform/diagnostic-settings.md) para o processo detalhado para criar uma configuração de diagnóstico usando o portal do Azure, a CLI ou o PowerShell. Ao criar uma configuração de diagnóstico, você especifica quais categorias de logs coletar.

Você pode coletar logs das seguintes categorias para Azure Time Series Insights:

   | Categoria | Descrição |
   |---|---|
   | Entrada  | A categoria de entrada rastreia os erros que ocorrem no pipeline de entrada. Essa categoria inclui erros que ocorrem durante o recebimento de eventos (como falhas para se conectar a uma fonte de evento) e eventos de processamento (como erros ao analisar uma carga de evento). |

## <a name="analyzing-metrics"></a>Analisando métricas

Você pode analisar as métricas para Azure Time Series Insights, juntamente com as métricas de outros serviços do Azure, abrindo as métricas no menu Azure Monitor. Consulte [Introdução ao Azure Metrics Explorer](../azure-monitor/platform/metrics-getting-started.md) para obter detalhes sobre como usar essa ferramenta.

Para obter uma lista das métricas de plataforma coletadas, consulte [monitoramento Azure Time Series insights referência de dados](how-to-monitor-tsi-reference.md#metrics)

Este exemplo mostra a contagem de bytes recebidos de todas as origens de evento em seu ambiente de Azure Time Series Insights.

**Entrada de bytes recebidos** da [ ![ série temporal de entrada bytes recebidos do Azure](media/how-to-monitor-tsi/ingress-received-bytes.png)](media/how-to-monitor-tsi/ingress-received-bytes.png#lightbox)

O exemplo mostra a contagem de bytes processados com êxito e disponíveis para consulta em seu ambiente de Azure Time Series Insights.

**Bytes armazenados de entrada** de [ ![ sequência de tempo do Azure bytes armazenados de entrada de série temporal](media/how-to-monitor-tsi/ingress-stored-bytes.png)](media/how-to-monitor-tsi/ingress-stored-bytes.png#lightbox)

## <a name="analyzing-logs"></a>Análise de logs
Você pode acessar os logs de recursos como um blob em uma conta de armazenamento, como dados de evento, ou por meio de consultas do Log Analytics.

Os dados em Logs do Azure Monitor são armazenados em tabelas, cada uma com o próprio conjunto de propriedades exclusivas.

Todos os logs de recursos no Azure Monitor têm os mesmos campos seguidos por campos específicos do serviço. O esquema comum é descrito em [Azure monitor esquema de log de recursos](../azure-monitor/platform/resource-logs-schema.md#top-level-common-schema). Para obter uma lista dos tipos de logs de recursos coletados para Azure Time Series Insights, consulte [referência de dados de monitoramento de Azure Time Series insights](how-to-monitor-tsi-reference.md#resource-logs).

Azure Time Series Insights armazena dados nas tabelas a seguir.

| Tabela | Descrição |
|:---|:---|
| TSIIngress | A tabela que armazena dados da categoria de entrada. A categoria de entrada rastreia os erros que ocorrem no pipeline de entrada. Essa categoria inclui erros que ocorrem durante o recebimento de eventos (como falhas para se conectar a uma fonte de evento) e eventos de processamento (como erros ao analisar uma carga de evento).

Para rotear dados para logs de Azure Monitor, você deve criar uma configuração de diagnóstico para enviar logs de recursos ou métricas de plataforma para um espaço de trabalho Log Analytics. Para saber mais, consulte [coleta e roteamento](../iot-hub/monitor-iot-hub.md#collection-and-routing).

## <a name="sample-queries"></a>Consultas de exemplo

Veja a seguir as consultas que você pode usar para ajudá-lo a monitorar seu ambiente de Azure Time Series Insights:

+ Obter detalhes sobre falhas de conexão de origem de evento nos últimos cinco dias:

    ```Kusto
   TSIIngress
   | where OperationName == "Microsoft.TimeSeriesInsights/environments/eventsources/ingress/connect"
   | where _ResourceId contains "<your environment name, event source name, or the full event source resource URL>"
   | where TimeGenerated > ago(5d)

    ```
+ Obter detalhes sobre mensagens inválidas recebidas nos últimos cinco dias:

    ```Kusto
   TSIIngress
   | where OperationName == "Microsoft.TimeSeriesInsights/environments/eventsources/ingress/deserialize"
   | where _ResourceId contains "<your environment name, event source name, or the full event source resource URL>"
   | where TimeGenerated > ago(5d)

    ```

## <a name="alerts"></a>Alertas

Azure Monitor Alertas proativamente notificam você quando condições importantes são encontradas nos dados de monitoramento. Eles permitem que você identifique e resolva problemas em seu sistema antes que os clientes os percebam. Você pode definir alertas em [métricas](../azure-monitor/platform/alerts-metric-overview.md), [logs](../azure-monitor/platform/alerts-unified-log.md)e no [log de atividades](../azure-monitor/platform/activity-log-alerts.md). Tipos diferentes de alertas têm benefícios e desvantagens.

Ao criar uma regra de alerta com base nas métricas de plataforma, lembre-se de que para Time Series Insights métricas de plataforma que são coletadas em unidades de contagem, algumas agregações podem não estar disponíveis ou utilizáveis.

## <a name="next-steps"></a>Próximas etapas

* Veja [Azure Time Series insights referência de dados de monitoramento](how-to-monitor-tsi-reference.md) para obter uma referência dos logs e métricas criados por Azure Time Series insights.
* Confira [Como monitorar os recursos do Azure com o Azure Monitor](../azure-monitor/insights/monitor-azure-resource.md) para obter detalhes sobre o monitoramento de recursos do Azure.