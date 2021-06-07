---
title: Otimizar o processamento de dados para Apache Spark – Azure HDInsight
description: Saiba como escolher as operações mais eficientes para processar seus dados no Apache Spark com o Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/20/2020
ms.custom: contperf-fy21q1
ms.openlocfilehash: 98457456bc1c8f9fdb08c32fdcd319a3aa9ff14a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98944711"
---
# <a name="data-processing-optimization-for-apache-spark"></a>Otimização de processamento de dados para Apache Spark

Este artigo discute como otimizar a configuração do cluster do Apache Spark para obter um melhor desempenho no Azure HDInsight.

## <a name="overview"></a>Visão geral

Se houver trabalhos lentos em uma Junção ou Ordem Aleatória, provavelmente a causa será a *distorção de dados*. A distorção de dados é assimetria em seus dados de trabalho. Por exemplo, um trabalho de mapa pode levar 20 segundos. Mas executar um trabalho em que os dados são unidos ou colocados em ordem aleatória leva horas. Para corrigir a distorção de dados, será necessário usar salt da chave inteira ou um *salt isolado* para apenas um subconjunto de chaves. Se você estiver usando um sal isolado, deverá filtrar adicionalmente para isolar o subconjunto de chaves com sal nas junções de mapa. Outra opção é primeiro introduzir uma coluna de bucket e pré-agregado em buckets.

Outro fator que causa junções lentas pode ser o tipo de junção. Por padrão, o Spark usa o tipo de junção `SortMerge`. Esse tipo de junção é mais adequado para conjuntos de dados grandes. Mas, de outra forma, é computacionalmente caro porque primeiro deve classificar os lados esquerdo e direito dos dados antes de mesclá-los.

Uma junção `Broadcast` é mais adequada para conjuntos de dados menores ou, onde um lado da junção é muito menor do que o outro lado. Esse tipo de junção difunde um lado para todos os executores e, portanto, requer mais memória para difusões em geral.

É possível alterar o tipo de junção na configuração, definindo `spark.sql.autoBroadcastJoinThreshold` ou você pode definir uma dica de junção usando as APIs do DataFrame (`dataframe.join(broadcast(df2))`).

```scala
// Option 1
spark.conf.set("spark.sql.autoBroadcastJoinThreshold", 1*1024*1024*1024)

// Option 2
val df1 = spark.table("FactTableA")
val df2 = spark.table("dimMP")
df1.join(broadcast(df2), Seq("PK")).
    createOrReplaceTempView("V_JOIN")

sql("SELECT col1, col2 FROM V_JOIN")
```

Se você estiver usando tabelas em bucket, terá um terceiro tipo de junção, a junção `Merge`. Um conjunto de dados corretamente pré-particionado e pré-classificado ignorará a fase de classificação cara de uma junção `SortMerge`.

A ordem de questões de junção, particularmente em consultas mais complexas. Inicie com as junções mais seletivas. Além disso, mova junções que aumentam o número de linhas após as agregações, quando possível.

Para gerenciar o paralelismo em junções cartesianas, você pode adicionar estruturas aninhadas, janelas e talvez ignorar uma ou mais etapas do trabalho do Spark.

## <a name="optimize-job-execution"></a>Otimizar a execução do trabalho

* Armazene em cache conforme necessário, por exemplo, se você utilizar os dados duas vezes, armazene-os em cache.
* Difundir variáveis a todos os executores. As variáveis são serializadas apenas uma vez, resultando em pesquisas mais rápidas.
* Use o pool de threads no driver, o que resulta em operação mais rápida para muitas tarefas.

Monitore regularmente os trabalhos em execução verificar se há problemas de desempenho. Se precisar de mais informações sobre certos problemas, considere uma das seguintes ferramentas de criação de perfil de desempenho:

* A [Ferramenta Intel PAL](https://github.com/intel-hadoop/PAT) monitora a CPU, o armazenamento e o uso da largura de banda da rede.
* Perfis de [Controle de Missão Oracle Java 8](https://www.oracle.com/technetwork/java/javaseproducts/mission-control/java-mission-control-1998576.html) do Spark e código de executor.

A chave para desempenho de consultas do Spark 2.x é o mecanismo Tungsten, que depende da geração de código de estágio inteiro. Em alguns casos, a geração de código de estágio inteiro pode ser desabilitada. Por exemplo, se você utilizar um tipo não mutável (`string`) na expressão de agregação, `SortAggregate` aparecerá em vez de `HashAggregate`. Por exemplo, para um melhor desempenho, tente o seguinte e, em seguida, habilite novamente a geração de código:

```sql
MAX(AMOUNT) -> MAX(cast(AMOUNT as DOUBLE))
```

## <a name="next-steps"></a>Próximas etapas

* [Otimizar o armazenamento de dados para Apache Spark](optimize-data-storage.md)
* [Otimizar o uso da memória para Apache Spark](optimize-memory-usage.md)
* [Otimizar a configuração do cluster para Apache Spark](optimize-cluster-configuration.md)
