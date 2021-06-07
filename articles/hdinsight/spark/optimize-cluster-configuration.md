---
title: Otimização da configuração de cluster do Apache Spark – Azure HDInsight
description: Saiba como configurar o cluster do Apache Spark para maximizar a taxa de transferência no Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/21/2020
ms.custom: contperf-fy21q1
ms.openlocfilehash: f70e9f242b0f12abf58e72554e7cb6819ce8f7a9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98944731"
---
# <a name="cluster-configuration-optimization-for-apache-spark"></a>Otimização de configuração de cluster para Apache Spark

Este artigo discute como otimizar a configuração do cluster do Apache Spark para obter um melhor desempenho no Azure HDInsight.

## <a name="overview"></a>Visão geral

Dependendo da carga de trabalho do cluster Spark, você poderá determinar que uma configuração Spark não padrão resulte em execução de trabalho do Spark mais otimizada.  Execute testes de comparação com cargas de trabalho de exemplo para validar quaisquer configurações não padrão de cluster.

Aqui, são apresentado alguns parâmetros comuns que podem ser ajustados:

|Parâmetro |Descrição |
|---|---|
|--num-executors|Define o número apropriado de executores.|
|--executor-cores|Define o número de núcleos para cada executor. Normalmente, você deverá ter executores de tamanho médio, pois outros processos consomem parte da memória disponível.|
|--executor-memory|Define o tamanho da memória para cada executor, que controla o tamanho de heap no YARN. Deixe alguma memória para sobrecarga de execução.|

## <a name="select-the-correct-executor-size"></a>Selecionar o tamanho correto de executor

Ao decidir a configuração de executor, considere a sobrecarga de coleta de lixo Java (GC).

* Fatores para reduzir o tamanho do executor:
    1. Reduzir o tamanho do heap abaixo de 32 GB para manter a sobrecarga de GC < 10%.
    2. Reduzir o número de núcleos para manter a sobrecarga de GC < 10%.

* Fatores para aumentar o tamanho do executor:
    1. Reduzir a sobrecarga de comunicação entre os executores.
    2. Reduzir o número de conexões abertas entre executores (N2) em clusters maiores (> 100 executores).
    3. Aumentar o tamanho do heap para acomodar tarefas intensivas de memória.
    4. Opcional: Reduzir a sobrecarga da memória por executor.
    5. Opcional: Aumentar o uso e a simultaneidade ao sobrecarregar a CPU.

Como regra geral, quando selecionar o tamanho do executor:

1. Inicie com 30 GB por executor e distribua núcleos de computadores disponíveis.
2. Aumente o número de núcleos de executor para clusters maiores (> 100 executores).
3. Modificar o tamanho com base em execuções de avaliação e nos fatores anteriores, como a sobrecarga do GC.

Quando executar consultas simultâneas, considere o seguinte:

1. Inicie com 30 GB por executor e todos os núcleos de computadores.
2. Crie várias aplicações do Spark paralelas com subscrição excessiva de CPU (cerca de 30% de melhoria de latência).
3. Distribua consultas em aplicativos paralelos.
4. Modificar o tamanho com base em execuções de avaliação e nos fatores anteriores, como a sobrecarga do GC.

Para obter mais informações sobre como usar o Ambari para configurar executores, veja [Configurações do Apache Spark – Executores do Spark](apache-spark-settings.md#configuring-spark-executors).

Monitore o desempenho da consulta para verificar exceções ou outros problemas de desempenho, observando a exibição da linha do tempo. Observe também o grafo de SQL, as estatísticas de trabalho e, assim por diante. Para obter informações sobre como depurar trabalhos do Spark usando o YARN e o servidor de histórico do Spark, veja [Depuração dos trabalhos do Apache Spark em execução no Azure HDInsight](apache-spark-job-debugging.md). Para obter dicas sobre como usar o servidor de linha do tempo do YARN, veja [Acesso aos logs do aplicativo do Apache Hadoop YARN](../hdinsight-hadoop-access-yarn-app-logs-linux.md).

Às vezes, um ou alguns dos executores são mais lentos que os outros e as tarefas demoram muito mais tempo para serem executadas. Essa lentidão geralmente ocorre em clusters maiores (mais de 30 nós). Nesse caso, divida o trabalho em um número maior de tarefas para que o agendador possa compensar tarefas lentas. Por exemplo, tenha pelo menos duas vezes mais tarefas que o número de núcleos de executor no aplicativo. Você também pode habilitar a execução especulativa de tarefas com `conf: spark.speculation = true`.

## <a name="next-steps"></a>Próximas etapas

* [Otimização do processamento de dados para Apache Spark](optimize-cluster-configuration.md)
* [Otimização do armazenamento de dados para Apache Spark](optimize-data-storage.md)
* [Otimização do uso da memória para Apache Spark](optimize-memory-usage.md)
