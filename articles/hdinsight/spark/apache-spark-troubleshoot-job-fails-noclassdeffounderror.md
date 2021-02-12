---
title: NoClassDefFoundError-Apache Spark com dados de Apache Kafka no Azure HDInsight
description: Apache Spark trabalho de streaming que lê dados de um cluster de Apache Kafka falha com um NoClassDefFoundError no Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/29/2019
ms.openlocfilehash: 4d00cbcb0151da39feb0cb015660291af544d7f4
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946373"
---
# <a name="apache-spark-streaming-job-that-reads-apache-kafka-data-fails-with-noclassdeffounderror-in-hdinsight"></a>Apache Spark trabalho de streaming que lê Apache Kafka dados falha com NoClassDefFoundError no HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções de problemas ao usar os componentes do Apache Spark nos clusters do Azure HDInsight.

## <a name="issue"></a>Problema

O cluster de Apache Spark executa um trabalho de streaming do Spark que lê dados de um cluster Apache Kafka. O trabalho de streaming do Spark falhará se a compactação de fluxo Kafka estiver ativada. Nesse caso, o aplicativo yarn de streaming do Spark application_1525986016285_0193 falhou, devido ao erro:

```
18/05/17 20:01:33 WARN YarnAllocator: Container marked as failed: container_e25_1525986016285_0193_01_000032 on host: wn87-Scaled.2ajnsmlgqdsutaqydyzfzii3le.cx.internal.cloudapp.net. Exit status: 50. Diagnostics: Exception from container-launch.
Container id: container_e25_1525986016285_0193_01_000032
Exit code: 50
Stack trace: ExitCodeException exitCode=50: 
 at org.apache.hadoop.util.Shell.runCommand(Shell.java:944)
```

## <a name="cause"></a>Causa

Esse erro pode ser causado pela especificação de uma versão do `spark-streaming-kafka` arquivo JAR que seja diferente da versão do cluster Kafka que você está executando.

Por exemplo, se você estiver executando uma versão de cluster Kafka 0.10.1, o seguinte comando resultará em um erro:

```
spark-submit \
--packages org.apache.spark:spark-streaming-kafka-0-8_2.11:2.2.0
--conf spark.executor.instances=16 \
...
~/Kafka_Spark_SQL.py <bootstrap server details>
```

## <a name="resolution"></a>Resolução

Use o comando Spark-Submit com a `–packages` opção e verifique se a versão do arquivo JAR Spark-streaming-Kafka é a mesma que a versão do cluster Kafka que você está executando.

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]