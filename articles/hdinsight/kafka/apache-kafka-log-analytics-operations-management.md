---
title: Logs de Azure Monitor para Apache Kafka-Azure HDInsight
description: Saiba como usar os logs de Azure Monitor para analisar logs de Apache Kafka cluster no Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 02/17/2020
ms.openlocfilehash: 51301bd38bf0700ce42ef33a47b9e763da8d4ed6
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100595286"
---
# <a name="analyze-logs-for-apache-kafka-on-hdinsight"></a>Analise logs do Apache Kafka no HDInsight

Saiba como usar os logs de Azure Monitor para analisar os logs gerados pelo Apache Kafka no HDInsight.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="logs-location"></a>Local dos logs

Os logs de Apache Kafka no cluster estão localizados em `/var/log/kafka` . Os logs do Kafka não são salvos ou persistidos entre os ciclos de vida do cluster, independentemente de os discos gerenciados serem usados. A tabela a seguir mostra os logs disponíveis.

|Log |Descrição |
|---|---|
|Kafka. out|stdout e stderr do processo Kafka. Você encontrará logs de inicialização e desligamento do Kafka neste arquivo.|
|Server. log|O log principal do servidor do Kafka. Todos os logs do agente do Kafka terminam aqui.|
|Controller. log|Os logs do controlador se o agente estiver agindo como controlador.|
|StateChange. log|Todos os eventos de alteração de estado para os agentes são registrados nesse arquivo.|
|Kafka-GC. log|Estatísticas de coleta de lixo Kafka.|

## <a name="enable-azure-monitor-logs-for-apache-kafka"></a>Habilitar logs de Azure Monitor para Apache Kafka

As etapas para habilitar os logs de Azure Monitor para o HDInsight são as mesmas para todos os clusters HDInsight. Use os links a seguir para entender como criar e configurar os serviços necessários:

1. Criar um espaço de trabalho do Log Analytics. Para obter mais informações, consulte o documento [logs no Azure monitor](../../azure-monitor/logs/data-platform-logs.md) .

2. Criar um Kafka no cluster HDInsight. Para obter mais informações, consulte o documento [Introdução ao Apache Kafka no HDInsight](apache-kafka-get-started.md).

3. Configure o cluster Kafka para usar logs de Azure Monitor. Para obter mais informações, consulte o documento [usar Azure monitor logs para monitorar o HDInsight](../hdinsight-hadoop-oms-log-analytics-tutorial.md) .

> [!IMPORTANT]  
> Pode levar cerca de 20 minutos antes que os dados estejam disponíveis para logs de Azure Monitor.

## <a name="query-logs"></a>Logs de consulta

1. No [portal do Azure](https://portal.azure.com), selecione seu espaço de trabalho do log Analytics.

2. No menu à esquerda, em **geral**, selecione **logs**. A partir desse ponto, é possível pesquisar os dados coletados do Kafka. Insira uma consulta na janela de consulta e, em seguida, selecione **executar**. Veja a seguir alguns exemplos de pesquisas:

* Uso do disco:

    ```kusto
    Perf
    | where ObjectName == "Logical Disk" and CounterName == "Free Megabytes" and InstanceName == "_Total" and ((Computer startswith_cs "hn" and Computer contains_cs "-") or (Computer startswith_cs "wn" and Computer contains_cs "-")) 
    | summarize AggregatedValue = avg(CounterValue) by Computer, bin(TimeGenerated, 1h)
    ```

* Uso da CPU:

    ```kusto
    Perf 
    | where CounterName == "% Processor Time" and InstanceName == "_Total" and ((Computer startswith_cs "hn" and Computer contains_cs "-") or (Computer startswith_cs "wn" and Computer contains_cs "-")) 
    | summarize AggregatedValue = avg(CounterValue) by Computer, bin(TimeGenerated, 1h)
    ```

* Mensagens de entrada por segundo: (substitua `your_kafka_cluster_name` pelo nome do cluster.)

    ```kusto
    metrics_kafka_CL 
    | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-MessagesInPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_MessagesInPerSec_Count_value_d) by HostName_s, bin(TimeGenerated, 1h)
    ```

* Bytes de entrada por segundo: (substitua `wn0-kafka` por um nome de host do nó de trabalho.)

    ```kusto
    metrics_kafka_CL 
    | where HostName_s == "wn0-kafka" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesInPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesInPerSec_Count_value_d) by bin(TimeGenerated, 1h)
    ```

* Bytes de saída por segundo: (substitua `your_kafka_cluster_name` pelo nome do cluster.)

    ```kusto
    metrics_kafka_CL 
    | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesOutPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesOutPerSec_Count_value_d) by bin(TimeGenerated, 1h)
    ```

    Também é possível inserir `*` para pesquisas todos os tipos registrados. Atualmente, os logs a seguir estão disponíveis para consultas:

    | Tipo de log | Description |
    | ---- | ---- |
    | log\_kafkaserver\_CL | Kafka broker server.log |
    | log\_kafkacontroller\_CL | Kafka broker controller.log |
    | metrics\_kafka\_CL | Métricas do Kafka JMX |

    ![Uso da CPU do Apache Kafka log Analytics](./media/apache-kafka-log-analytics-operations-management/apache-kafka-cpu-usage.png)

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre Azure Monitor, consulte [Azure monitor visão geral](../../azure-monitor/overview.md)e [consultar logs de Azure monitor para monitorar clusters HDInsight](../hdinsight-hadoop-oms-log-analytics-use-queries.md).

Para saber mais sobre como trabalhar com o Apache Kafka, confira os seguintes documentos:

* [Espelhamento do Apache Kafka entre clusters HDInsight](apache-kafka-mirroring.md)
* [Aumentar a escala de Apache Kafka no HDInsight](apache-kafka-scalability.md)
* [Use o fluxo do Apache Spark (DStreams) com Apache Kafka](../hdinsight-apache-spark-with-kafka.md)
* [Use o fluxo estruturado do Apache Spark com o Apache Kafka](../hdinsight-apache-kafka-spark-structured-streaming.md)