---
title: "Notas de versão dos componentes do Hadoop no Azure HDInsight | Microsoft Docs"
description: "Últimas notas de versão e versões de componentes do Hadoop para Azure HDInsight. Obtenha detalhes e dicas de desenvolvimento para Spark, Servidor R, Hive e muito mais."
services: hdinsight
documentationcenter: 
editor: cgronlun
manager: jhubbard
author: nitinme
tags: azure-portal
ms.assetid: a363e5f6-dd75-476a-87fa-46beb480c1fe
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/04/2017
ms.author: nitinme
ms.openlocfilehash: 96b2b4976729da5b7d8b75909dbe099090240c08
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="release-notes-for-hadoop-components-on-azure-hdinsight"></a>Notas de versão dos componentes do Hadoop no Azure HDInsight

Este artigo fornece informações sobre as atualizações de versão **mais recentes** do Azure HDInsight. Para obter informações sobre versões anteriores, consulte o [Arquivo morto de notas de versão do HDInsight](hdinsight-release-notes-archive.md).

> [!IMPORTANT]
> O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou superior. Para saber mais, confira o [artigo sobre controle de versão do HDInsight](hdinsight-component-versioning.md).


## <a name="notes-for-08012017-release-of-hdinsight"></a>Notas da versão de 01/08/2017 do HDInsight

| Title | Descrição | Área Afetada  | Tipo de cluster  | 
| --- | --- | --- | --- | --- |
| Lançamento do Microsoft R Server 9.1 no HDInsight |O HDInsight agora dá suporte ao provisionamento de clusters do R Server 9.1 no HDInsight. Para obter mais informações sobre a versão do Microsoft R Server 9.1, consulte [este blog](https://blogs.technet.microsoft.com/dataplatforminsider/2017/04/19/introducing-microsoft-r-server-9-1-release/). |O Barramento de |Servidor R |
| O HDInsight 3.6 agora inclui versões mais recentes da pilha do Hadoop|<ul><li>Para obter uma lista detalhada de versões atualizadas, consulte [Versões dos componentes do Hadoop disponíveis no HDInsight](hdinsight-component-versioning.md#hadoop-components-available-with-different-hdinsight-versions).</li><li>Para obter uma lista de bugs corrigidos nas versões mais recentes da pilha do Hadoop, consulte [Informações de Patch do Apache](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.1/bk_release-notes/content/patch_parent.html).</li><li>Para obter uma lista das últimas alterações entre HDP 2.6.1 (que agora está disponível no HDInsight 3.6), consulte [https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.1/bk_release-notes/content/behavior_changes.html](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.1/bk_release-notes/content/behavior_changes.html).</li><li>Para obter uma lista dos problemas conhecidos no HDP 2.6.1, consulte [Problemas conhecidos](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.1/bk_release-notes/content/known_issues.html).</li></ul> |O Barramento de |Todos |N/D |
| Atualizações para os clusters de Hive Interativo (versão prévia) |<ul><li><b>Aperfeiçoamento de recurso.</b> Implementação de metastore em cache que reduz a carga no SQL de back-end armazenando em cache os metadados e que melhora o desempenho de todas as operações de metadados.  Essa melhoria agora é um padrão em todos os clusters do Hive Interativo. Para obter mais informações, consulte [https://issues.apache.org/jira/browse/HIVE-16520](https://issues.apache.org/jira/browse/HIVE-16520).</li><li><b>Aperfeiçoamento de recurso.</b> O carregamento dinâmico de partição é otimizado. Para obter mais informações, consulte [https://issues.apache.org/jira/browse/HIVE-14204] (https://issues.apache.org/jira/browse/HIVE-14204).</li><li><b>Aperfeiçoamento de recurso.</b> Otimizações de configuração para HDInsight no Linux.</li><li><b>Correção de bug.</b> `CredentialProviderFactory$getProviders` não é thread-safe. Esse problema está corrigido. Para obter mais informações, consulte [https://issues.apache.org/jira/browse/HADOOP-14195](https://issues.apache.org/jira/browse/HADOOP-14195).</li><li><b>Correção de bug.</b> Alto uso da CPU com API `liststatus` de driver WASB resultando em um desempenho de ATS ruim. Esse problema está corrigido. Para obter mais informações, consulte [https://github.com/Azure/azure-storage-java/pull/154](https://github.com/Azure/azure-storage-java/pull/154).</li></ul> |O Barramento de |Hive Interativo (versão prévia) |
| Atualiza para clusters Hadoop |A confiabilidade da operação de trabalho do Templeton foi aprimorada. Para obter mais informações, consulte [https://issues.apache.org/jira/browse/HIVE-15947](https://issues.apache.org/jira/browse/HIVE-15947) |O Barramento de |O Hadoop |
| Atualizações do YARN | Agora, o HDInsight cria um banco de dados do Ambari de 250 GB (sem aumentar os custos), o que resulta em uma melhor experiência para os clientes. Essa alteração deverá impedir que ATS seja preenchido e provavelmente terá um desempenho melhor. |O Barramento de |Todos |
| Atualizações do Spark | Versão do Spark 2.1.1. Para obter mais informações, confira [Spark Versão 2.1.1](https://spark.apache.org/releases/spark-release-2-1-1.html). | O Barramento de | Spark |

  



## <a name="04062017---general-availability-of-hdinsight-36"></a>06/04/2017 – Disponibilidade geral do HDInsight 3.6

* Com esta versão, o Azure HDInsight adiciona a versão 3.6, que se baseia no HDP 2.6. As notas de versão do HDP 2.6 estão disponíveis [aqui](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.0/bk_release-notes/content/ch_relnotes.html) e mais informações sobre versões do HDInsight podem ser encontradas [aqui](hdinsight-component-versioning.md). O HDInsight 3.6 está disponível para as cargas de trabalho a seguir:

    * Hadoop v2.7.3
    * HBase v1.1.2
    * Storm v1.1.0
    * Spark v2.1.0
    * Hive Interativo v2.1.0

* **Suporte à Exibição do Hive 2.0**. Isso deve melhorar a experiência do usuário para o Hive Interativo. Para obter mais informações, consulte a [documentação da Hortonworks](http://docs.hortonworks.com/HDPDocuments/Ambari-2.5.0.3/bk_ambari-views/content/ch_using_hive_view.html).

* **Aprimoramentos de desempenho com o Hive LLAP**. Para obter mais informações, consulte a [documentação da Hortonworks](https://hortonworks.com/blog/top-5-performance-boosters-with-apache-hive-llap/).

* **Novos recursos no Hive**. Consulte a [documentação da Hortonworks](https://hortonworks.com/apache/hive/#section_4).

* **Substituição da CLI do hive**: a CLI do Hive está sendo preterida e os clientes são incentivados a usar o Beeline em seu lugar. Para obter mais informações, veja a [documentação do Apache](https://cwiki.apache.org/confluence/display/Hive/Replacing+the+Implementation+of+Hive+CLI+Using+Beeline). Para obter instruções sobre como usar o Beeline com o HDInsight, consulte [Usar clusters Beeline com o Hadoop HDInsight](hdinsight-hadoop-use-hive-beeline.md).

* **Novos recursos no Apache Phoenix e HBase**.
    * Suporte a cota de armazenamento: comumente usadas em ambientes multilocatário, permitindo espaço de armazenamento limitado por tabela por nível de namespace.
    * Melhorias de indexação do Phoenix: criar índice incremental e recompilar/retomar a indexação após falhas anteriores.
    * Ferramenta de integridade de dados do Phoenix: dá suporte à validação de esquema, índice e outros metadados.


* **Problema com o HBase**: durante a execução de um trabalho de upload de CSV em massa do MapReduce, a sintaxe a seguir pode resultar em um erro.

        HADOOP_CLASSPATH=$(hbase mapredcp):/path/to/hbase/conf hadoop jar phoenix-<version>-client.jar org.apache.phoenix.mapreduce.CsvBulkLoadTool --table EXAMPLE --input /data/example.csv

    Use a sintaxe a seguir, em vez disso:

        HADOOP_CLASSPATH=/path/to/hbase-protocol.jar:/path/to/hbase/conf hadoop jar phoenix-<version>-client.jar org.apache.phoenix.mapreduce.CsvBulkLoadTool --table EXAMPLE --input /data/example.csv


## <a name="02282017---release-of-spark-21-on-hdinsight-36-preview"></a>28/02/2017 – Lançamento do Spark 2.1 no HDInsight 3.6 (Visualização)
* O [Spark 2.1](http://spark.apache.org/releases/spark-release-2-1-0.html) melhora muitos problemas de estabilidade e usabilidade com versões anteriores. Ele também traz novos recursos em todas as cargas de trabalho Spark, como Spark Core, SQL, ML e Streaming.
* O fluxo estruturado obtém escalabilidade aprimorada com suporte para marcas d'água de hora do evento e conector Kafka 0.10.
* O particionamento SQL do Spark agora é tratado usando o novo mecanismo de Tratamento de Partição Escalonável. Veja mais detalhes [aqui](http://spark.apache.org/releases/spark-release-2-1-0.html) sobre como atualizar.
* Atualmente, o Spark 2.1 no Azure HDInsight 3.6 Preview não oferece suporte à conectividade da Ferramenta de BI usando o driver ODBC.
* Não há suporte para acesso do Azure Data Lake Store usando clusters do Spark 2.1 nesta versão Preview.


## <a name="11182016---release-of-spark-201-on-hdinsight-35"></a>18/11/2016 – Lançamento do Spark 2.0.1 no HDInsight 3.5
O Spark 2.0.1 agora está disponível em clusters do Spark (HDInsight versão 3.5).

## <a name="11162016---release-of-r-server-90-on-hdinsight-35-spark-20"></a>16/11/2016 – Lançamento do R Server 9.0 no HDInsight 3.5 (Spark 2.0)
*   Os clusters de Servidor R agora incluem a opção para duas versões: Servidor R 9.0 HDI 3.5 (Spark 2.0) e Servidor R 8.0 em HDI 3.4 (Spark 1.6).
*   O Servidor R 9.0 no HDI 3.5 (Spark 2.0) está incluído no R 3.3.2 e inclui novas funções de fonte de dados ScaleR chamadas RxHiveData e RxParquetData para carregar dados do Hive e do Parquet diretamente para DataFrames Spark para análise do ScaleR. Para mais informações, veja a ajuda embutida dessas funções no R por meio do uso dos comandos **?RxHiveData** e **?RxParquetData**.
*   A edição RStudio Server community está instalada por padrão (com uma opção de recusa) na folha Configuração de Cluster, como parte do fluxo de provisionamento.

## <a name="11092016---release-of-spark-20-on-hdinsight"></a>11/09/2016 – Lançamento do Spark 2.0 no HDInsight
* Os clusters Spark 2.0 no HDInsight 3.5 agora oferecem suporte aos serviços Livy e Jupyter.

## <a name="10262016---release-of-r-server-on-hdinsight"></a>26/10/2016 – Versão do R Server no HDInsight
* O URI para o acesso ao nó de borda foi alterado para **nomedocluster**-ed-ssh.azurehdinsight.net
* O provisionamento de cluster do Servidor R no HDInsight foi simplificado.
* O Servidor R no HDInsight agora está disponível como um tipo de cluster regular “Servidor R” do HDInsight e não é mais instalado como um aplicativo HDInsight separado. O nó de borda e os binários do Servidor R agora são provisionados como parte da implantação de cluster do Servidor R. Isso melhora a velocidade e a confiabilidade do provisionamento. O modelo de preço para o Servidor R é atualizado adequadamente.
* O preço do tipo de cluster Servidor R agora se baseia na camada de preço Standard mais o preço da sobretaxa do Servidor R. A camada Premium é reservada para recursos Premium disponíveis em diferentes tipos de cluster e não será usada para o tipo de cluster do Servidor R. Essa alteração não afeta o preço efetivo do Servidor R; ela altera apenas como as cobranças são apresentadas na fatura. Todos os clusters do Servidor R existentes continuarão funcionando e os modelos do Resource Manager continuarão funcionando até o aviso de substituição. **De qualquer forma, é recomendável atualizar as implantações com script para usar o novo modelo do Resource Manager.**






