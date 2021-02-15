---
title: Otimizar os trabalhos do Spark para desempenho – Microsoft Azure HDInsight
description: Mostre estratégias comuns para o melhor desempenho de clusters do Apache Spark no Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/21/2020
ms.custom: contperf-fy21q1
ms.openlocfilehash: 567fc2637538408d9727d07d3185a5b0e3cf20c5
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98929936"
---
# <a name="optimize-apache-spark-jobs-in-hdinsight"></a>Otimizar trabalhos do Apache Spark no HDInsight

Este artigo fornece uma visão geral das estratégias para otimizar trabalhos do Apache Spark no Azure HDInsight.

## <a name="overview"></a>Visão geral

O desempenho dos seus trabalhos do Apache Spark depende de vários fatores. Esses fatores de desempenho incluem: como os dados são armazenados, como o cluster é configurado e as operações que são usadas durante o processamento dos dados.

Os desafios comuns que você pode enfrentar incluem: restrições de memória devido a executores de tamanho inadequado, operações de longa execução e tarefas que resultam em operações cartesianas.

Também há muitas otimizações que podem ajudá-lo a superar esses desafios, como o Caching, e permitir a distorção de dados.

Em cada um dos artigos a seguir, você pode encontrar informações sobre diferentes aspectos da otimização do Spark.

* [Otimização do armazenamento de dados para Apache Spark](optimize-data-storage.md)
* [Otimização do processamento de dados para Apache Spark](optimize-data-processing.md)
* [Otimização do uso de memória para Apache Spark](optimize-memory-usage.md)
* [Otimizar a configuração do cluster HDInsight para Apache Spark](optimize-cluster-configuration.md)

## <a name="next-steps"></a>Próximas etapas

* [Depurar trabalhos do Apache Spark em execução no Azure HDInsight](apache-spark-job-debugging.md)
* [Gerenciar recursos para um cluster do Apache Spark no HDInsight](apache-spark-resource-manager.md)
* [Definir as configurações do Apache Spark](apache-spark-settings.md)
