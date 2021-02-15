---
title: Visão geral do HDInsight 4.0 – Azure
description: Compare o HDInsight 3.6 com os recursos, as limitações e as recomendações de atualização do HDInsight 4.0.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/21/2020
ms.openlocfilehash: 694acc0005e90d8299d46528f83ba68ea3fcf1c0
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98931183"
---
# <a name="azure-hdinsight-40-overview"></a>Visão geral do Azure HDInsight 4.0

O Azure HDInsight é um dos serviços mais populares entre os clientes corporativos para Apache Hadoop e Apache Spark. O HDInsight 4.0 é uma distribuição de nuvem dos componentes do Apache Hadoop. Este artigo fornece informações sobre a versão mais recente do Azure HDInsight e como atualizar.

## <a name="whats-new-in-hdinsight-40"></a>Novidades do HDInsight 4.0

### <a name="apache-hive-30-and-low-latency-analytical-processing"></a>Apache Hive 3.0 e processamento analítico de baixa latência

O LLAP (processamento analítico de baixa latência) do Apache Hive usa servidores de consulta persistentes e cache em memória. Esse processo fornece resultados rápidos de consulta SQL sobre dados no armazenamento em nuvem remota. O LLAP do Hive usa um conjunto de daemons persistentes que executam fragmentos de consultas do Hive. A execução da consulta em LLAP é semelhante ao Hive sem LLAP, com tarefas de trabalho em execução dentro de daemons de LLAP em vez de contêineres.

Os benefícios do LLAP do Hive incluem:

* Capacidade de fazer análises profundas de SQL sem sacrificar o desempenho e a capacidade de adaptação. Como junções complexas, subconsultas, funções de janela, classificação, funções definidas pelo usuário e agregações complexas.

* Consultas interativas em dados no mesmo armazenamento em que os dados são preparados, eliminando a necessidade de movê-los do armazenamento para outro mecanismo a fim de realizar o processamento analítico.

* Os resultados da consulta de cache permitem que os resultados de consulta calculados anteriormente sejam reutilizados. Esse cache economiza tempo e recursos gastos na execução das tarefas de cluster necessárias para a consulta.

### <a name="hive-dynamic-materialized-views"></a>Exibições materializadas dinâmicas do Hive

Agora o Hive é compatível com exibições materializadas dinâmicas ou pré-computação de resumos relevantes. As exibições aceleram o processamento de consultas em data warehouses. As exibições materializadas podem ser armazenadas nativamente no Hive e podem usar a aceleração de LLAP perfeitamente.

### <a name="hive-transactional-tables"></a>Tabelas transacionais do Hive

O HDI 4.0 inclui o Apache Hive 3. O Hive 3 requer conformidade de atomicidade, consistência, isolamento e durabilidade para tabelas transacionais que residem no warehouse do Hive. As tabelas e dados de tabela em conformidade com ACID são acessadas e gerenciadas pelo Hive. Os dados em tabelas CRUD (criar, recuperar, atualizar e excluir) devem estar no formato de arquivo ORC (coluna e linha otimizadas). As tabelas somente inserção dão suporte a todos os formatos de arquivo. 

> [!Note]
> O suporte a ACID/transacional só funciona para tabelas gerenciadas e não para tabelas externas. As tabelas externas do hive são projetadas de forma que as partes externas possam ler e gravar dados da tabela, sem que o hive perfoming qualquer alteração dos dados subjacentes. Para tabelas ACID, o hive pode alterar os dados subjacentes com compactação e transações.

Alguns benefícios das tabelas ACID são os seguintes:

* O ACID v2 tem melhorias de desempenho no formato de armazenamento e no mecanismo de execução.

* O ACID é habilitado por padrão para permitir o suporte completo para as atualizações de dados.

* Os recursos aprimorados de ACID permitem que você atualize e exclua no nível de linha.

* Sem sobrecarga de desempenho.

* Sem necessidade de Bucketing.

* O Spark pode ler e gravar em tabelas ACID do Hive por meio do Conector de Warehouse do Hive.

Saiba mais sobre o [Apache Hive 3](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/hive-overview/content/hive_whats_new_in_this_release_hive.html).

### <a name="apache-spark"></a>Apache Spark

O Apache Spark obtém tabelas atualizáveis e transações ACID com o Conector de Warehouse do Hive. O Conector de Warehouse do Hive permite que você registre tabelas transacionais do Hive como tabelas externas no Spark para acessar toda a funcionalidade transacional. As versões anteriores só eram compatíveis com manipulação de partição de tabela. O Hive Warehouse Connector também dá suporte a DataFrames de streaming.  Esse processo transmite leituras e gravações em tabelas de Hive transacionais e de streaming do Spark.

Os executores do Spark podem se conectar diretamente aos daemons de LLAP do Hive para recuperar e atualizar dados de forma transacional, permitindo que o Hive mantenha o controle dos dados.

O Apache Spark no HDInsight 4.0 é compatível com os seguintes cenários:

* Executar o treinamento do modelo de machine learning sobre a mesma tabela transacional usada para relatórios.
* Usar transações ACID para adicionar colunas de AM do Spark com segurança em uma tabela do Hive.
* Executar um trabalho de streaming do Spark no feed de alterações de uma tabela de streaming do Hive.
* Criar arquivos ORC diretamente de um trabalho de Streaming Estruturado do Spark.

Você não precisa mais se preocupar com a tentativa acidental de acessar tabelas transacionais do Hive diretamente no Spark. Recebendo resultados inconsistentes, dados duplicados ou dados corrompidos. No HDInsight 4.0, as tabelas do Spark e do Hive são mantidas em Metastores separados. Use o Conector de Data Warehouse do Hive para registrar explicitamente as tabelas transacionais do Hive como tabelas externas do Spark.

Saiba mais sobre o [Apache Spark](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/spark-overview/content/analyzing_data_with_apache_spark.html).

### <a name="apache-oozie"></a>Apache Oozie

O Apache Oozie 4.3.1 está incluído no HDI 4.0 com as seguintes alterações:

* O Oozie não executa mais ações do Hive. A CLI do Hive foi removida e substituída pela BeeLine.

* Você pode excluir dependências indesejáveis do compartilhamento lib por meio da inclusão de um padrão de exclusão no arquivo **job.properties**.

Saiba mais sobre o [Apache Oozie](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/release-notes/content/patch_oozie.html).

## <a name="how-to-upgrade-to-hdinsight-40"></a>Como atualizar para o HDInsight 4.0

Teste cuidadosamente seus componentes antes de implementar a versão mais recente em um ambiente de produção. O HDInsight 4.0 está disponível para que você comece o processo de atualização. O HDInsight 3.6 é a opção padrão para evitar incidentes acidentais.

Não há um caminho de atualização compatível das versões anteriores do HDInsight para o HDInsight 4.0. Como os formatos de dados de blob e do Metastore foram alterados, a versão 4.0 não é compatível com versões anteriores. É importante que você mantenha o novo ambiente do HDInsight 4.0 separado do ambiente de produção atual. Se você implantar o HDInsight 4.0 no ambiente atual, o Metastore será permanentemente atualizado.  

## <a name="limitations"></a>Limitações

* O HDInsight 4.0 não é compatível com o MapReduce para Apache Hive. Use o Apache Tez em vez disso. Saiba mais sobre o [Apache Tez](https://tez.apache.org/).
* O HDInsight 4.0 não é compatível com o Apache Storm.
* O HDInsight 4,0 não dá suporte ao tipo de cluster de serviços ML.
* A exibição do hive só está disponível em clusters HDInsight 4,0 com um número de versão igual ou maior que 4,1. Esse número de versão está disponível em versões do > admin – Ambari.
* O interpretador de shell no Apache Zeppelin não é compatível com clusters do Spark e do Interactive Query.
* Não é possível *desabilitar* o LLAP em um cluster Spark-LLAP. Você pode apenas desativar o LLAP.
* Azure Data Lake Storage Gen2 não pode salvar notebooks Jupyter em um cluster Spark.
* O Apache Pig é executado no Tez por padrão, no entanto, você pode alterá-lo para o MapReduce
* A integração do Spark SQL Ranger para segurança de linha e coluna foi preterida
* O Spark 2.4 e o Kafka 2.1 estão disponíveis no HDInsight 4.0, portanto, o Spark 2.3 e o Kafka 1.1 não são mais compatíveis. É recomendável usar o Spark 2.4 e o Kafka 2.1 e superior no HDInsight 4.0.

## <a name="next-steps"></a>Próximas etapas

* [Guia de migração do HBase](./hbase/apache-hbase-migrate-new-version.md)
* [Guia de migração do hive](./interactive-query/apache-hive-migrate-workloads.md)
* [Guia de migração do Kafka](./kafka/migrate-versions.md)
* [Guia de migração do Spark](./spark/migrate-versions.md)
* [Documentação do HDInsight do Azure](index.yml)
* [Notas de Versão](hdinsight-release-notes.md)
