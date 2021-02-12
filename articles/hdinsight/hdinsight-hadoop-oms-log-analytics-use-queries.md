---
title: Consultar logs de Azure Monitor para monitorar os clusters do Azure HDInsight
description: Saiba como executar consultas em logs de Azure Monitor para monitorar trabalhos em execução em um cluster HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/02/2019
ms.openlocfilehash: f9213f36ec33939c3df3b56d21822aa3b6a17c03
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98945609"
---
# <a name="query-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>Consulta logs do Azure Monitor para monitorar clusters do HDInsight

Conheça alguns cenários básicos sobre como usar os logs de Azure Monitor para monitorar os clusters do Azure HDInsight:

* [Analisar métricas de cluster HDInsight](#analyze-hdinsight-cluster-metrics)
* [Criar alertas de eventos](#create-alerts-for-tracking-events)

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Pré-requisitos

Você deve ter configurado um cluster HDInsight para usar logs de Azure Monitor e adicionar soluções de monitoramento de logs de Azure Monitor específicas do cluster HDInsight ao espaço de trabalho. Para obter instruções, consulte [usar logs de Azure monitor com clusters HDInsight](hdinsight-hadoop-oms-log-analytics-tutorial.md).

## <a name="analyze-hdinsight-cluster-metrics"></a>Analisar métricas de cluster HDInsight

Saiba como procurar métricas específicas para seu cluster HDInsight.

1. Abra o espaço de trabalho do Log Analytics que está associado ao seu cluster HDInsight do portal do Azure.
1. Em **Geral**, selecione **Logs**.
1. Digite a consulta a seguir na caixa de pesquisa para procurar todas as métricas de todas as métricas disponíveis para todos os clusters HDInsight configurados para usar logs de Azure Monitor e, em seguida, selecione **executar**. Examine os resultados.

    ```kusto
    search *
    ```

    ![O Apache Ambari Analytics pesquisa todas as métricas](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics.png "Pesquisar todas as métricas")

1. No menu à esquerda, selecione a guia **filtro** .

1. Em **tipo**, selecione **pulsação**. Em seguida, selecione **aplicar & executar**.

    ![métricas específicas de pesquisa do log Analytics](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-metrics.png "Pesquisar métricas específicas")

1. Observe que a consulta na caixa de texto muda para:

    ```kusto
    search *
    | where Type == "Heartbeat"
    ```

1. Você pode se aprofundar usando as opções disponíveis no menu à esquerda. Por exemplo:

    - Para ver os logs de um nó específico:

        ![Pesquisar erros específicos Saída1](./media/hdinsight-hadoop-oms-log-analytics-use-queries/log-analytics-specific-node.png "Pesquisar erros específicos Saída1")

    - Para ver os logs em determinados momentos:

        ![Pesquisar erros específicos output2](./media/hdinsight-hadoop-oms-log-analytics-use-queries/log-analytics-specific-time.png "Pesquisar erros específicos output2")

1. Selecione **aplicar & executar** e revisar os resultados. Observe também que a consulta foi atualizada para:

    ```kusto
    search *
    | where Type == "Heartbeat"
    | where (Computer == "zk2-myhado") and (TimeGenerated == "2019-12-02T23:15:02.69Z" or TimeGenerated == "2019-12-02T23:15:08.07Z" or TimeGenerated == "2019-12-02T21:09:34.787Z")
    ```

### <a name="additional-sample-queries"></a>Consultas de exemplo adicionais

Uma consulta de exemplo com base na média de recursos usados em um intervalo de 10 minutos, Categorizado pelo nome do cluster:

```kusto
search in (metrics_resourcemanager_queue_root_default_CL) * 
| summarize AggregatedValue = avg(UsedAMResourceMB_d) by ClusterName_s, bin(TimeGenerated, 10m)
```

Em vez de refinar com base na média de recursos usados, é possível usar a seguinte consulta para refinar os resultados com base em quando os recursos máximos foram usados (assim como o 90º e o 95º percentil) em uma janela de 10 minutos:

```kusto
search in (metrics_resourcemanager_queue_root_default_CL) * 
| summarize ["max(UsedAMResourceMB_d)"] = max(UsedAMResourceMB_d), ["pct95(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 95), ["pct90(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 90) by ClusterName_s, bin(TimeGenerated, 10m)
```

## <a name="create-alerts-for-tracking-events"></a>Criar alertas para eventos de rastreamento

A primeira etapa para criar um alerta é chegar a uma consulta com base na qual o alerta é disparado. É possível usar qualquer consulta que você desejar para criar um alerta.

1. Abra o espaço de trabalho do Log Analytics que está associado ao seu cluster HDInsight do portal do Azure.
1. Em **Geral**, selecione **Logs**.
1. Execute a consulta a seguir na qual você deseja criar um alerta e, em seguida, selecione **executar**.

    ```kusto
    metrics_resourcemanager_queue_root_default_CL | where AppsFailed_d > 0
    ```

    A consulta fornece uma lista de aplicativos com falha em execução em clusters de HDInsight.

1. Selecione **nova regra de alerta** na parte superior da página.

    ![Inserir consulta para criar um alert1](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert-query.png "Inserir consulta para criar um alert1")

1. Na janela **Criar regra**, digite a consulta e outros detalhes para criar um alerta e, em seguida, selecione **Criar regra de alerta**.

    ![Inserir consulta para criar um alert2](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert.png "Inserir consulta para criar um alert2")

### <a name="edit-or-delete-an-existing-alert"></a>Editar ou excluir um alerta existente

1. Abra o espaço de trabalho do Log Analytics no portal do Azure.

1. No menu à esquerda, em **monitoramento**, selecione **alertas**.

1. Em direção à parte superior, selecione **gerenciar regras de alerta**.

1. Selecione o alerta que você deseja editar ou excluir.

1. Você tem as seguintes opções: **Salvar**, **Descartar**, **Desabilitar**, e **Excluir**.

    ![Log de Azure Monitor do HDInsight logs de exclusão de alertas](media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-edit-alert.png)

Para obter mais informações, confira [Criar, exibir e gerenciar alertas de métrica usando o Azure Monitor](../azure-monitor/platform/alerts-metric.md).

## <a name="see-also"></a>Veja também

* [Introdução às consultas de log no Azure Monitor](../azure-monitor/log-query/get-started-queries.md)
* [Criar exibições personalizadas usando o designer de exibição no Azure Monitor](../azure-monitor/platform/view-designer.md)
