---
title: Usar o SparkCruise no Azure HDInsight para acelerar Apache Spark consultas
description: Saiba como usar a plataforma de otimização SparkCruise para melhorar a eficiência das consultas de Apache Spark.
ms.service: hdinsight
ms.topic: how-to
ms.date: 07/27/2020
ms.openlocfilehash: f07ea3007d2e72c179abe38cca56178b564bc231
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98928278"
---
# <a name="sparkcruise-on-azure-hdinsight"></a>SparkCruise no Azure HDInsight

Este documento discute o recurso *SparkCruise* do Azure HDInsight, que reutiliza automaticamente Apache Spark computações para aumentar a eficiência da consulta.

## <a name="overview"></a>Visão geral

As consultas que você executa em uma plataforma de análise, como Apache Spark, são decompostas em um plano de consulta que contém subconsultas menores. Essas subconsultas podem aparecer repetidamente nos planos de consulta para várias consultas. Cada vez que elas ocorrem, elas são executadas novamente para retornar os resultados. No entanto, executar novamente a mesma consulta pode ser ineficiente e levar a custos de computação desnecessários.

O *SparkCruise* é uma plataforma de otimização de carga de trabalho que pode reutilizar cálculos comuns, diminuindo o tempo de execução da consulta geral e os custos de transferência de dados. A plataforma usa o conceito de uma *exibição materializada*, que é uma consulta cujos resultados são armazenados em formato previamente computado. Esses resultados podem ser reutilizados quando a consulta em si aparece novamente mais tarde, em vez de recalcular os resultados novamente.

## <a name="setup-and-installation"></a>Instalação e configuração

O SparkCruise está disponível em todos os clusters HDInsight 4,0 com Spark 2,3 ou 2,4. Os arquivos de biblioteca SparkCruise são instalados no `/opt/peregrine/` diretório em seu cluster HDInsight. Para funcionar corretamente, o *SparkCruise* requer as seguintes propriedades de configuração, que são definidas por padrão.

* `spark.sql.queryExecutionListeners` é definido como `com.microsoft.peregrine.spark.listeners.PlanLogListener` , que habilita o log de planos de consulta.
* `spark.sql.extensions` é definido como `com.microsoft.peregrine.spark.extensions.SparkExtensionsHdi` , que habilita as regras do otimizador para materialização online e reutilização.

## <a name="computation-reuse-in-spark-sql"></a>Reutilização de computação no Spark SQL

O cenário de exemplo a seguir ilustra como usar o *SparkCruise* para otimizar Apache Spark consultas. 

1. SSH no nó principal do cluster Spark.
1. Digite `spark-shell`.
1. Execute o trecho de código a seguir, que executa algumas consultas básicas usando dados de exemplo no cluster.

    ```scala
    spark.sql("select count(*) from hivesampletable").collect
    spark.sql("select count(*) from hivesampletable").collect
    spark.sql("select distinct market from hivesampletable where querytime like '11%'").show
    spark.sql("select distinct state, country from hivesampletable where querytime like '11%'").show
    :quit
    ```
1. Use a ferramenta de análise de consulta do *SparkCruise* para analisar os planos de consulta das consultas anteriores, que são armazenadas nos logs de aplicativo do Spark. 

    ```
    sudo /opt/peregrine/analyze/peregrine.sh analyze views
    ```

1. Exiba a saída do processo de análise, que é um arquivo de comentários. Este arquivo de comentários contém anotações para consultas SQL do Spark futuras. 

    ```
    sudo /opt/peregrine/analyze/peregrine.sh show
    ```

O `analyze` comando analisa os planos de consulta e cria uma representação tabular da carga de trabalho. Esta tabela de carga de trabalho pode ser consultada usando o notebook *WorkloadInsights* incluído no repositório de [exemplos do HDInsight SparkCruise](https://github.com/Azure-Samples/azure-sparkcruise-samples) . Em seguida, o `views` comando identifica expressões de subplano comuns e seleciona expressões de subplano interessantes para materialização e reutilização futura. A saída é um arquivo de comentários que contém anotações para consultas SQL do Spark futuras. 

O `show` comando exibe uma saída como o texto a seguir:

```bash
Feedback file -->

1593761760087311271 Materialize /peregrine/views/1593761760087311271
1593761760087311271 Reuse /peregrine/views/1593761760087311271
18446744073621796959 Materialize /peregrine/views/18446744073621796959
18446744073621796959 Reuse /peregrine/views/18446744073621796959
11259615723090744908 Materialize /peregrine/views/11259615723090744908
11259615723090744908 Reuse /peregrine/views/11259615723090744908
9409467400931056980 Materialize /peregrine/views/9409467400931056980
9409467400931056980 Reuse /peregrine/views/9409467400931056980

Materialized subexpressions -->

Found 4 items
-rw-r--r--   1 sshuser sshuser     113445 2020-07-24 16:46 /peregrine/views/logical_ir.csv
-rw-r--r--   1 sshuser sshuser     169458 2020-07-24 16:46 /peregrine/views/physical_ir.csv
-rw-r--r--   1 sshuser sshuser      25730 2020-07-24 16:46 /peregrine/views/views.csv
-rw-r--r--   1 sshuser sshuser        536 2020-07-24 16:46 /peregrine/views/views.stp
```

O arquivo de comentários contém entradas no seguinte formato: `subplan-identifier [Materialize|Reuse] input/path/to/action` . Neste exemplo, há duas assinaturas exclusivas: uma representando as duas primeiras consultas repetidas e a segunda que representa o predicado de filtro nas duas últimas consultas. Com este arquivo de comentários, as consultas a seguir quando forem enviadas novamente agora irão materializar e reutilizar automaticamente subplanos comuns. 

Para testar as otimizações, execute outro conjunto de consultas de exemplo.

1. Digite `spark-shell`.
1. Execute o seguinte trecho de código

    ```scala
    spark.sql("select count(*) from hivesampletable").collect
    spark.sql("select count(*) from hivesampletable").collect
    spark.sql("select distinct state, country from hivesampletable where querytime like '12%'").show
    spark.sql("select distinct market from hivesampletable where querytime like '12%'").show
    :quit
    ```

1. Exiba o arquivo de comentários novamente para ver as assinaturas das consultas que foram reutilizadas.

    ```
    sudo /opt/peregrine/analyze/peregrine.sh show
    ```

O `show` comando fornece uma saída semelhante ao seguinte texto:

```bash
Feedback file -->

1593761760087311271 Materialize /peregrine/views/1593761760087311271
1593761760087311271 Reuse /peregrine/views/1593761760087311271
18446744073621796959 Materialize /peregrine/views/18446744073621796959
18446744073621796959 Reuse /peregrine/views/18446744073621796959
11259615723090744908 Materialize /peregrine/views/11259615723090744908
11259615723090744908 Reuse /peregrine/views/11259615723090744908
9409467400931056980 Materialize /peregrine/views/9409467400931056980
9409467400931056980 Reuse /peregrine/views/9409467400931056980

Materialized subexpressions -->

Found 8 items
drwxr-xr-x   - root root          0 2020-07-24 17:21 /peregrine/views/11259615723090744908
drwxr-xr-x   - root root          0 2020-07-24 17:21 /peregrine/views/1593761760087311271
drwxr-xr-x   - root root          0 2020-07-24 17:22 /peregrine/views/18446744073621796959
drwxr-xr-x   - root root          0 2020-07-24 17:21 /peregrine/views/9409467400931056980
-rw-r--r--   1 root root     113445 2020-07-24 16:46 /peregrine/views/logical_ir.csv
-rw-r--r--   1 root root     169458 2020-07-24 16:46 /peregrine/views/physical_ir.csv
-rw-r--r--   1 root root      25730 2020-07-24 16:46 /peregrine/views/views.csv
-rw-r--r--   1 root root        536 2020-07-24 16:46 /peregrine/views/views.stp

```

Embora o valor literal na consulta tenha sido alterado de `'11%'` para `'12%'` , o *SparkCruise* ainda pode corresponder consultas anteriores a novas consultas com pequenas variações, como a evolução de valores literais e versões de conjuntos de de conjunto de pesquisa. Se houver alterações importantes em uma consulta, você poderá executar novamente a ferramenta de análise para identificar novas consultas que podem ser reutilizadas.

Nos bastidores, *SparkCruise* dispara uma subconsulta para materializar o subplano selecionado da primeira consulta que o contém. As consultas posteriores podem ler diretamente os subplanos materializados em vez de Recomputa-los. Nessa carga de trabalho, os subplanos serão materializados de maneira online pela primeira e terceira consultas. Podemos ver a alteração do plano de consultas depois que os subplanos comuns são materializados.

Você pode ver que agora há quatro novas subexpressões materializadas disponíveis para serem reutilizadas em consultas subsequentes.

## <a name="clean-up"></a>Limpeza

Os arquivos de comentários, subplanos materializados e logs de consulta são persistidos entre as sessões do Spark. Para remover esses arquivos, execute o seguinte comando:

```bash
sudo /opt/peregrine/analyze/peregrine.sh clean
```

## <a name="next-steps"></a>Próximas etapas

* [Use o bloco de anotações de informações de carga de trabalho para determinar os benefícios do SparkCruise](https://github.com/Azure-Samples/azure-sparkcruise-samples/tree/main/SparkCruise)
* [Melhorar o desempenho de cargas de trabalho de Apache Spark usando o cache de e/s do Azure HDInsight](apache-spark-improve-performance-iocache.md)
* [Otimizar trabalhos do Apache Spark no HDInsight](./apache-spark-perf.md)
* [SparkCruise: reutilização de computação Handsfree no Spark](https://people.cs.umass.edu/~aroy/sparkcruise-vldb19.pdf)
* [Diretrizes de Apache Spark no Azure HDInsight](./spark-best-practices.md)
