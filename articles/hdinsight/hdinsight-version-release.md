---
title: Visão geral do HDInsight 4.0 – Azure
description: Compare o HDInsight 3.6 com os recursos, as limitações e as recomendações de atualização do HDInsight 4.0.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 77260eaa2e19135586e438576397b4647afe7ece
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67483970"
---
# <a name="hdinsight-40-overview"></a>Visão geral do HDInsight 4.0

O Azure HDInsight é um dos serviços mais populares entre os clientes corporativos para análise de software livre Apache Hadoop e Apache Spark no Azure. HDInsight 4.0 é uma distribuição em nuvem dos componentes do Apache Hadoop. Este artigo fornece informações sobre a versão mais recente do Azure HDInsight e como atualizar.

## <a name="whats-new-in-hdinsight-40"></a>Novidades do HDInsight 4.0

### <a name="apache-hive-30-and-llap"></a>Apache Hive 3.0 e LLAP

O LLAP (processamento analítico de baixa latência) do Apache Hive usa servidores de consulta persistentes e cache em memória para entregar resultados rápidos de Consulta SQL em dados armazenados remotamente na nuvem. O LLAP do Hive aproveita um conjunto de daemons persistentes que executam fragmentos de consultas do Hive. A execução da consulta em LLAP é semelhante ao Hive sem LLAP, com tarefas de trabalho em execução dentro de daemons de LLAP em vez de contêineres.

Os benefícios do LLAP do Hive incluem:

* Capacidade de execução de análises profundas de SQL, como junções complexas, subconsultas, funções de janelas, classificação, funções definidas pelo usuário e agregações complexas, sem sacrificar o desempenho e a escalabilidade.

* Consultas interativas em dados no mesmo armazenamento em que os dados são preparados, eliminando a necessidade de movê-los do armazenamento para outro mecanismo a fim de realizar o processamento analítico.

* O cache dos resultados de consulta permite que os resultados de consulta previamente computados sejam reutilizados, economizando tempo e recursos gastos na execução das tarefas de cluster necessárias para a consulta.

### <a name="hive-dynamic-materialized-views"></a>Exibições materializadas dinâmicas do Hive

Agora o Hive é compatível com exibições materializadas dinâmicas ou pré-computação de resumos relevantes, usados para acelerar o processamento de consulta em data warehouses. As exibições materializadas podem ser armazenadas nativamente no Hive e podem usar a aceleração de LLAP perfeitamente.

### <a name="hive-transactional-tables"></a>Tabelas transacionais do Hive

O HDI 4.0 inclui o Apache Hive 3, que requer conformidade ACID (atomicidade, consistência, isolamento e durabilidade) para tabelas transacionais que residem no warehouse do Hive. As tabelas e dados de tabela em conformidade com ACID são acessadas e gerenciadas pelo Hive. Os dados em tabelas CRUD (criar, recuperar, atualizar e excluir) devem estar no formato de arquivo ORC (coluna e linha otimizadas), mas as tabelas do tipo somente inserção são compatíveis com todos os formatos de arquivo.

* O ACID v2 tem melhorias de desempenho no formato de armazenamento e no mecanismo de execução. 

* O ACID é habilitado por padrão para permitir o suporte completo para as atualizações de dados.

* Os recursos aprimorados de ACID permitem que você atualize e exclua no nível de linha.

* Sem sobrecarga de desempenho.

* Sem necessidade de Bucketing.

* O Spark pode ler e gravar em tabelas ACID do Hive por meio do Conector de Warehouse do Hive.

Saiba mais sobre o [Apache Hive 3](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/hive-overview/content/hive_whats_new_in_this_release_hive.html).

### <a name="apache-spark"></a>Apache Spark

O Apache Spark obtém tabelas atualizáveis e transações ACID com o Conector de Warehouse do Hive. O Conector de Warehouse do Hive permite que você registre tabelas transacionais do Hive como tabelas externas no Spark para acessar toda a funcionalidade transacional. As versões anteriores só eram compatíveis com manipulação de partição de tabela. O Conector de Warehouse do Hive também é compatível com DataFrames de Streaming para leituras e gravações de streaming nas tabelas transacionais e de streaming do Hive provenientes do Spark.

Os executores do Spark podem se conectar diretamente aos daemons de LLAP do Hive para recuperar e atualizar dados de forma transacional, permitindo que o Hive mantenha o controle dos dados.

O Apache Spark no HDInsight 4.0 é compatível com os seguintes cenários:

* Executar o treinamento do modelo de machine learning sobre a mesma tabela transacional usada para relatórios.
* Usar transações ACID para adicionar colunas de AM do Spark com segurança em uma tabela do Hive.
* Executar um trabalho de streaming do Spark no feed de alterações de uma tabela de streaming do Hive.
* Criar arquivos ORC diretamente de um trabalho de Streaming Estruturado do Spark.

Você não precisa mais se preocupar com a tentativa acidental de acessar tabelas transacionais do Hive diretamente no Spark, causando resultados inconsistentes, dados duplicados ou dados corrompidos. No HDInsight 4.0, as tabelas do Spark e do Hive são mantidas em Metastores separados. Use o Conector de Data Warehouse do Hive para registrar explicitamente as tabelas transacionais do Hive como tabelas externas do Spark.

Saiba mais sobre o [Apache Spark](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/spark-overview/content/analyzing_data_with_apache_spark.html).


### <a name="apache-oozie"></a>Apache Oozie

O Apache Oozie 4.3.1 está incluído no HDI 4.0 com as seguintes alterações:

* O Oozie não executa mais ações do Hive. A CLI do Hive foi removida e substituída pela BeeLine.

* Você pode excluir dependências indesejáveis do compartilhamento lib por meio da inclusão de um padrão de exclusão no arquivo **job.properties**.

Saiba mais sobre o [Apache Oozie](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/release-notes/content/patch_oozie.html).

## <a name="how-to-upgrade-to-hdinsight-40"></a>Como atualizar para o HDInsight 4.0

Assim como em qualquer versão principal, é importante testar cuidadosamente seus componentes antes de implementar a versão mais recente em um ambiente de produção. O HDInsight 4.0 está disponível para você começar o processo de atualização, mas o HDInsight 3.6 é a opção padrão para evitar contratempos acidentais.

Não há um caminho de atualização compatível das versões anteriores do HDInsight para o HDInsight 4.0. Como os formatos de dados de blob e do Metastore foram alterados, o HDInsight 4.0 não é compatível com versões anteriores. É importante que você mantenha o novo ambiente do HDInsight 4.0 separado do ambiente de produção atual. Se você implantar o HDInsight 4.0 no ambiente atual, o Metastore será atualizado e não poderá ser revertido.  

## <a name="limitations"></a>Limitações

* O HDInsight 4.0 não é compatível com o MapReduce para Apache Hive. Use o Apache Tez em vez disso. Saiba mais sobre o [Apache Tez](https://tez.apache.org/).
* O HDInsight 4.0 não é compatível com o Apache Storm. 
* A exibição do Hive não está mais disponível no HDInsight 4.0. 
* O interpretador de shell no Apache Zeppelin não é compatível com clusters do Spark e de Consulta Interativa.
* Não é possível *desabilitar* o LLAP em um cluster Spark-LLAP. Você pode apenas desativar o LLAP.
* O Azure Data Lake Storage Gen2 não é capaz de salvar Jupyter Notebooks em um cluster Spark.

## <a name="next-steps"></a>Próximas etapas

* [Documentação do HDInsight do Azure](index.yml)
* [Notas de versão](hdinsight-release-notes.md)
