---
title: Otimizar o uso de memória no Apache Spark - Azure HDInsight
description: Saiba como otimizar o uso de memória no Apache Spark no Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/20/2020
ms.custom: contperf-fy21q1
ms.openlocfilehash: 4e23c5977b2492d2ea8a7a8cc050c77c512c3e16
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104868368"
---
# <a name="memory-usage-optimization-for-apache-spark"></a>Otimização de uso de memória para Apache Spark

Este artigo discute como otimizar o gerenciamento de memória do Apache Spark para obter um melhor desempenho no Azure HDInsight.

## <a name="overview"></a>Visão geral

O Spark opera colocando dados na memória. Portanto, o gerenciamento de recursos de memória é um aspecto fundamental da otimização na execução de trabalhos do Spark.  Há várias técnicas que podem ser aplicadas para usar a memória do cluster de maneira eficiente.

* Preferir partições de dados menores e considerar o tamanho, os tipos e a distribuição de dados na sua estratégia de particionamento.
* Considere [`Kryo data serialization`](https://github.com/EsotericSoftware/kryo) mais recentes e eficientes em vez da serialização de Java padrão.
* Preferir utilizar o YARN, pois separa `spark-submit` em lotes.
* Monitorar e sintonizar as definições da configuração do Spark.

Para sua referência, a estrutura da memória do Spark e alguns parâmetros da memória do executor chave são mostrados na próxima imagem.

## <a name="spark-memory-considerations"></a>Considerações de memória do Spark

Se estiver usando o YARN do Apache Hadoop, o YARN controlará a memória usada por todos os contêineres em cada nó do Spark.  O diagrama a seguir mostra os objetos de chave e os respectivos relacionamentos.

:::image type="content" source="./media/apache-spark-perf/apache-yarn-spark-memory.png" alt-text="Gerenciamento de memória YARN do Spark" border="false":::

Para endereçar mensagens de "memória insuficiente", tente:

* Revisar as ordens aleatórias de armazenamento do DAG. Reduzir a redução do lado do mapa, os dados de origem de pré-partição (ou fazer bucket), maximizar as ordens aleatórias simples e reduzir a quantidade de dados enviados.
* Preferir `ReduceByKey` com o limite de memória fixa para `GroupByKey`, que fornece agregações, janelas e outras funções, mas tem limite de memória ilimitado.
* Preferir `TreeReduce`, que faz mais trabalho nos executores ou partições, para `Reduce`, que faz todo o trabalho no driver.
* Usar os Conjuntos de Dados em vez dos objetos RDD de nível inferior.
* Criar ComplexTypes que encapsulam ações, como "Top N", várias agregações ou operações de janelas.

Para obter etapas de solução de problemas adicionais, consulte [exceções OutOfMemoryError para Apache Spark no Azure HDInsight](apache-spark-troubleshoot-outofmemory.md).

## <a name="next-steps"></a>Próximas etapas

* [Otimização do processamento de dados para Apache Spark](optimize-cluster-configuration.md)
* [Otimização do armazenamento de dados para Apache Spark](optimize-data-storage.md)
* [Otimizar a configuração do cluster para Apache Spark](optimize-cluster-configuration.md)
