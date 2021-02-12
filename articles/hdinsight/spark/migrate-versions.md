---
title: Migrar cargas de trabalho do Apache Spark 2.1 ou 2.2 para 2.3 ou 2.4 - Azure HDInsight
description: Aprenda como migrar Apache Spark 2.1 ou 2.2 para 2.3 ou 2.4.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: af1894d2f63357006e87fa8e4533f135ecc02f21
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944752"
---
# <a name="migrate-apache-spark-21-and-22-workloads-to-23-and-24"></a>Migrar cargas de trabalho Apache Spark 2.1 e 2.2 para 2.3 e 2.4

Este documento explica como migrar cargas de trabalho do Apache Spark 2.1 e 2.2 para 2.3 e 2.4.

Como discutimos nas [notas sobre a versão](../hdinsight-release-notes.md#upcoming-changes), a partir de 1º de julho de 2020, as configurações de cluster a seguir não terão mais suporte e os clientes não poderão criar novos clusters com essas configurações:
 - Spark 2.1 e 2.2 em um cluster Spark do HDInsight 3.6
 - Spark 2.3 em um cluster Spark do HDInsight 4.0

Os clusters existentes nessas configurações serão executados como estão, sem suporte da Microsoft. Se estiver no Spark 2.1 ou 2.2 no HDInsight 3.6, passe para o Spark 2.3 no HDInsight 3.6 até 30 de junho de 2020 para evitar a interrupção potencial do sistema/suporte. Se estiver no Spark 2.3 em um cluster do HDInsight 4.0, passe para o Spark 2.4 no HDInsight 4.0 até 30 de junho de 2020 para evitar a interrupção potencial do sistema/suporte.

Para obter informações gerais sobre como migrar um cluster do HDInsight do 3.6 para 4.0, consulte [Migrar o cluster do HDInsight para uma versão mais recente](../hdinsight-upgrade-cluster.md). Para informações gerais para migrar para uma versão mais recente do Apache Spark, consulte [Apache Spark: Política de versão](https://spark.apache.org/versioning-policy.html).

## <a name="guidance-on-spark-version-upgrades-on-hdinsight"></a>Diretrizes sobre atualizações de versão do Spark no HDInsight

| Cenário de atualização | Mecanismo | Itens a serem considerados | Integração do Spark/Hive |
|------------------|-----------|--------------------|------------------------|
|Spark 2.1 do HDInsight 3.6 para Spark 2.3 do HDInsight 3.6| Recriar clusters com Spark 2.3 do HDInsight | Examine os seguintes artigos: <br> [Apache Spark: Atualização do Spark SQL 2.2 para 2.3](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-22-to-23) <br><br> [Apache Spark: Atualização do SQL 2.1 para 2.2](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-21-to-22) | Nenhuma alteração |
|Spark 2.2 do HDInsight 3.6 para Spark 2.3 do HDInsight 3.6 | Recriar clusters com Spark 2.3 do HDInsight | Examine os seguintes artigos: <br> [Apache Spark: Atualização do Spark SQL 2.2 para 2.3](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-22-to-23) | Nenhuma alteração |
| Spark 2.1 do HDInsight 3.6 para Spark 2.4 do HDInsight 4.0 | Recriar clusters com o Spark 2.4 do HDInsight 4.0 | Examine os seguintes artigos: <br> [Apache Spark: Atualização do SQL do Spark 2.3 para 2.4](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-23-to-24) <br><br> [Apache Spark: Atualização do Spark SQL 2.2 para 2.3](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-22-to-23) <br><br> [Apache Spark: Atualização do SQL 2.1 para 2.2](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-21-to-22) | A integração do Spark e do hive foi alterada no HDInsight 4,0. <br><br> No HDInsight 4.0, o Spark e o Hive usam catálogos independentes para acessar tabelas SparkSQL ou Hive. Uma tabela criada pelo Spark reside no catálogo do Spark. Uma tabela criada pelo Hive reside no catálogo do Hive. Esse comportamento é diferente no HDInsight 3.6, onde o Hive e o Spark compartilham o catálogo comum. A integração do Hive e do Spark no HDInsight 4.0 depende do Hive Warehouse Connector (HWC). O HWC funciona como uma ponte entre o Spark e o Hive. Saiba mais sobre o Hive Warehouse Connector. <br> No HDInsight 4.0, se quiser compartilhar o metastore entre o Hive e o Spark, você poderá alterar a propriedade metastore.catalog.default para hive no cluster do Spark. Você pode encontrar essa propriedade no spark2-hive-site-override do Ambari Advanced. É importante entender que o compartilhamento do metastore só funciona para tabelas externas do hive, isso não funcionará se você tiver tabelas Hive internas/gerenciadas ou tabelas ACID. <br><br>Leia [Migrar cargas de trabalho do Hive do Azure HDInsight 3.6 para o HDInsight 4.0](../interactive-query/apache-hive-migrate-workloads.md) para mais informações.<br><br> |
| Spark 2.2 do HDInsight 3.6 para Spark 2.4 do HDInsight 4.0 | Recriar clusters com o Spark 2.4 do HDInsight 4.0 | Examine os seguintes artigos: <br> [Apache Spark: Atualização do SQL do Spark 2.3 para 2.4](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-23-to-24) <br><br> [Apache Spark: Atualização do Spark SQL 2.2 para 2.3](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-22-to-23) | A integração do Spark e do hive foi alterada no HDInsight 4,0. <br><br> No HDInsight 4.0, o Spark e o Hive usam catálogos independentes para acessar tabelas SparkSQL ou Hive. Uma tabela criada pelo Spark reside no catálogo do Spark. Uma tabela criada pelo Hive reside no catálogo do Hive. Esse comportamento é diferente no HDInsight 3.6, onde o Hive e o Spark compartilham o catálogo comum. A integração do Hive e do Spark no HDInsight 4.0 depende do Hive Warehouse Connector (HWC). O HWC funciona como uma ponte entre o Spark e o Hive. Saiba mais sobre o Hive Warehouse Connector. <br> No HDInsight 4.0, se quiser compartilhar o metastore entre o Hive e o Spark, você poderá alterar a propriedade metastore.catalog.default para hive no cluster do Spark. Você pode encontrar essa propriedade no spark2-hive-site-override do Ambari Advanced. É importante entender que o compartilhamento do metastore só funciona para tabelas externas do hive, isso não funcionará se você tiver tabelas Hive internas/gerenciadas ou tabelas ACID. <br><br>Leia [Migrar cargas de trabalho do Hive do Azure HDInsight 3.6 para o HDInsight 4.0](../interactive-query/apache-hive-migrate-workloads.md) para mais informações.|

## <a name="next-steps"></a>Próximas etapas

* [Migrar cluster do HDInsight para uma nova versão](../hdinsight-upgrade-cluster.md)
* [Migrar cargas de trabalho do Hive do Azure HDInsight 3.6 para o HDInsight 4.0](../interactive-query/apache-hive-migrate-workloads.md)
