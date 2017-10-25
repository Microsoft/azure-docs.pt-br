---
title: "O que é o HDInsight, a pilha de tecnologia do Hadoop e os clusters? - Azure | Microsoft Docs"
description: "Uma introdução ao HDInsight e à pilha e aos componentes de tecnologia Hadoop, incluindo Spark, Kafka, Hive, HBase para análise de big data."
keywords: "hadoop do azure, azure hadoop, introdução ao hadoop, pilha de tecnologia do hadoop, introdução hadoop, hadoop e introdução, o que é um cluster hadoop, o que é o cluster hadoop, para que serve o hadoop"
services: hdinsight
documentationcenter: 
author: cjgronlund
manager: jhubbard
editor: cgronlun
ms.assetid: e56a396a-1b39-43e0-b543-f2dee5b8dd3a
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/20/2017
ms.author: cgronlun
ms.openlocfilehash: e5ed09ddb1556e6c76813e71bcb31cf4f792b616
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-azure-hdinsight-the-hadoop-technology-stack-and-hadoop-clusters"></a>Introdução ao Azure HDInsight, à pilha de tecnologia do Hadoop e aos clusters Hadoop
 Este artigo fornece uma introdução ao Azure HDInsight, uma distribuição em nuvem da pilha de tecnologia do Hadoop. Ele também aborda o que é um cluster Hadoop e quando usá-lo. 

## <a name="what-is-hdinsight-and-the-hadoop-technology-stack"></a>O que é o HDInsight e a pilha de tecnologias do Hadoop? 
O Azure HDInsight é uma distribuição em nuvem dos componentes do Hadoop da [HDP (Hortonworks Data Platform)](https://hortonworks.com/products/data-center/hdp/). O [Apache Hadoop](http://hadoop.apache.org/) era a estrutura de código-fonte original para processamento distribuído e análise de grandes conjuntos de dados em clusters de computadores. 


A pilha de tecnologias do Hadoop inclui software e utilitários relacionados, inclusive Apache Hive, HBase, Spark, Kafka e muitos outros. Para ver os componentes disponíveis da pilha de tecnologia do Hadoop no HDInsight, confira [Componentes e versões disponíveis com o HDInsight][component-versioning]. Para ler mais sobre o Hadoop no HDInsight, consulte a [Página de recursos do Azure para HDInsight](https://azure.microsoft.com/services/hdinsight/).

## <a name="what-is-a-hadoop-cluster-and-when-do-you-use-it"></a>O que é um cluster Hadoop e quando usá-lo?
*Hadoop* também é um tipo de cluster que tem:

* YARN para gerenciamento de recursos e agendamento de trabalho
* MapReduce para processamento paralelo
* O HDFS (Sistema de Arquivos Distribuído Hadoop)
  
Os clusters Hadoop geralmente são usados para processamento de dados armazenados em lotes. Outros tipos de clusters no HDInsight têm recursos adicionais: o Spark ganhou popularidade devido ao seu processamento mais rápido na memória. Consulte [Tipos de Cluster no HDInsight](#overview) para obter detalhes.

## <a name="what-is-big-data"></a>O que é big data?
Big data descreve qualquer grande quantidade de informações digitais, como:

* Dados do sensor de equipamentos industriais
* Atividade de cliente coletada de um site
* Um feed de notícias do Twitter

O Big Data está sendo coletado em volumes crescentes, em velocidades mais altas e em uma maior variedade de formatos. Ele pode ser histórico (referente a dados armazenados) ou em tempo real (o que significa que é transmitido da fonte). 

## <a name="overview"></a>Tipos de cluster no HDInsight
O HDInsight inclui tipos específicos de cluster e recursos de personalização do cluster, como a adição de componentes, utilitários e idiomas.

### <a name="spark-kafka-interactive-query-hbase-customized-and-other-cluster-types"></a>Spark, Kafka, Consulta Interativa, HBase, personalizado e outros tipos de cluster
O HDInsight oferece os seguintes tipos de cluster:

* **[Apache Hadoop](https://wiki.apache.org/hadoop)**: usa gerenciamento de recursos [HDFS](#hdfs), [YARN](#yarn) e um modelo de programação [MapReduce](#mapreduce) simples para processar e analisar dados em lote em paralelo.
* **[Apache Spark](http://spark.apache.org/)**: uma estrutura de processamento paralelo que dá suporte ao processamento na memória para melhorar o desempenho de aplicativos de análises de Big Data. O Spark funciona para SQL, dados de transmissão e aprendizado de máquina. Confira [O que é o Apache Spark no HDInsight?](hdinsight-apache-spark-overview.md)
* **[Apache HBase](http://hbase.apache.org/)**: um banco de dados NoSQL baseado em Hadoop que fornece acesso aleatório e coerência forte para big data não estruturado e semiestruturado (potencialmente, bilhões de linhas vezes milhões de colunas). Confira [O que é o HBase em HDInsight?](hdinsight-hbase-overview.md)
* **[Microsoft R Server](https://msdn.microsoft.com/microsoft-r/rserver)**: um servidor para hospedagem e gerenciamento paralelo, processos R distribuídos. Ele fornece aos cientistas de dados, estatísticos e programadores de R o acesso sob demanda a métodos escalonáveis e distribuídos de análise no HDInsight. Consulte a [Visão geral do Servidor R no HDInsight](hdinsight-hadoop-r-server-overview.md).
* **[Apache Storm](https://storm.incubator.apache.org/)**: um sistema de computação distribuído e em tempo real para processar rapidamente grandes fluxos de dados. O Storm é oferecido como um cluster gerenciado no HDInsight. Consulte [Analisar dados do sensor em tempo real usando o Storm e o Hadoop](hdinsight-storm-sensor-data-analysis.md).
* **[Visualização de consulta do Apache Interactive Hive (ou: vida longa e processo)](https://cwiki.apache.org/confluence/display/Hive/LLAP)**: cache na memória para consultas Hive interativas e mais rápidas. Veja [Usar a consulta interativa no HDInsight](hdinsight-hadoop-use-interactive-hive.md).
* **[Apache Kafka](https://kafka.apache.org/)**: uma plataforma de código-fonte aberto usada para criar aplicativos e pipelines de dados de transmissão. O Kafka também fornece funcionalidade de fila de mensagens, o que permite que você publique e assine fluxos de dados. Consulte [Uma introdução ao Apache Kafka no HDInsight](hdinsight-apache-kafka-introduction.md).

Você também pode configurar clusters usando os seguintes métodos:
* **[Pré-visualização de clusters de domínio associado](hdinsight-domain-joined-introduction.md)**: um cluster associado a um domínio do Active Directory para que você possa controlar o acesso e fornecer governança de dados.
* **[Clusters personalizados com ações de script](hdinsight-hadoop-customize-cluster-linux.md)**: clusters com scripts que são executados durante o provisionamento instalam componentes adicionais.

### <a name="example-cluster-customization-scripts"></a>Scripts de personalização de cluster de exemplo
As ações de Script são scripts Bash no Linux executados durante o provisionamento do cluster e que podem ser usados para instalar componentes adicionais no cluster. 

Estes são exemplos de script fornecidos pela equipe do HDInsight:

* **[Hue](hdinsight-hadoop-hue-linux.md)**: um conjunto de aplicativos Web usado para interagir com um cluster. Somente clusters do Linux.
* **[Giraph](hdinsight-hadoop-giraph-install-linux.md)**: processamento de Graph para modelar relacionamentos entre coisas ou pessoas.
* **[Solr](hdinsight-hadoop-solr-install-linux.md)**: uma plataforma de pesquisa de escala empresarial que permite a pesquisa de texto completo em dados.

Para saber mais sobre como desenvolver suas próprias ações de script, consulte [Desenvolvimento de ação de script com o HDInsight](hdinsight-hadoop-script-actions-linux.md).

## <a name="components-and-utilities-on-hdinsight-clusters"></a>Componentes e utilitários em clusters HDInsight
Os componentes e utilitários abaixo estão incluídos nos clusters HDInsight:

* **[Ambari](#ambari)**: provisionamento, gerenciamento e monitoramento e utilitários de clusters.
* **[Avro](#avro)** (Biblioteca de Microsoft .NET para Avro): serialização de dados para o ambiente Microsoft .NET. 
* **[Hive e HCatalog](#hive)**: consulta similar a SQL e uma camada de gerenciamento de armazenamento e tabela.
* **[Mahout](#mahout)**: aplicativos de aprendizado de máquina escalonáveis.
* **[MapReduce](#mapreduce)**: estrutura herdada para processamento e gerenciamento de recursos distribuídos do Hadoop. Confira [YARN](#yarn).
* **[Oozie](#oozie)**: gerenciamento de fluxo de trabalho.
* **[Phoenix](#phoenix)**: uma camada de banco de dados relacional em HBase.
* **[Pig](#pig)**: script mais simples para transformações de MapReduce.
* **[Sqoop](#sqoop)**: importação e exportação de dados.
* **[Tez](#tez)**: permite que os processos de uso intensivo de dados sejam executados com eficiência em grande escala.
* **[YARN](#yarn)**: gerenciamento de recursos que faz parte da biblioteca principal do Hadoop.
* **[ZooKeeper](#zookeeper)**: coordenação de processos em sistemas distribuídos.

> [!NOTE]
> Para obter informações sobre os componentes específicos e informações de versão, confira [Componentes do Hadoop e versões no HDInsight][component-versioning]
>
>

### <a name="ambari"></a>Ambari
O Apache Ambari serve para provisionar, gerenciar e monitorar clusters do Apache Hadoop. Inclui uma coleção de ferramentas intuitivas para operador e um conjunto abrangente de APIs que ocultam a complexidade do Hadoop, simplificando a operação de clusters. Os clusters HDInsight em Linux fornecem tanto a interface do usuário Web Ambari quanto a API REST Ambari. As exibições da Ambari em clusters HDInsight permitem recursos de plug-in de interface de usuário.
Consulte [Gerenciar clusters HDInsight usando o Ambari](hdinsight-hadoop-manage-ambari.md) e <a target="_blank" href="https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md">Referência de API do Apache Ambari</a>.

### <a name="avro"></a>Avro (Biblioteca do Microsoft .NET para Avro)
A Biblioteca do Microsoft .NET para Avro implementa o formato de intercâmbio de dados binário compacto do Apache Avro para serialização para o ambiente Microsoft.NET. Ele define um esquema com neutralidade de idioma para que os dados serializados em uma linguagem possam ser lidos em outra diferente. Informações detalhadas sobre o formato podem ser encontradas na <a target=_"blank" href="http://avro.apache.org/docs/current/spec.html">especificação do Apache Avro</a>. O formato dos arquivos do Avro dá suporte ao modelo de programação distribuído do MapReduce: os arquivos são "divisíveis", o que significa que você pode buscar qualquer ponto em um arquivo e iniciar a leitura por meio de um bloco específico. Para saber como fazer isso, consulte [Serializar dados com a Biblioteca do Microsoft .NET para Avro](hdinsight-dotnet-avro-serialization.md). Suporte a cluster baseado em Linux em breve.

### <a name="hdfs"></a>HDFS
O HDFS (Sistema de Arquivos Distribuído Hadoop) é um sistema de arquivos que, com YARN e MapReduce, representa o núcleo da tecnologia Hadoop. Ele é o sistema de arquivo padrão para clusters Hadoop no HDInsight. Confira [Consultar dados de armazenamento compatível com HDFS](hdinsight-hadoop-use-blob-storage.md).

### <a name="hive"></a>Hive & HCatalog
<a target="_blank" href="http://hive.apache.org/">Apache Hive</a> é um software de data warehouse baseado no Hadoop que permite consultar e gerenciar grandes conjuntos de dados no armazenamento distribuído usando uma linguagem semelhante ao SQL, chamada HiveQL. O Hive, como o Pig, é uma abstração sobre o MapReduce e converte consultas em uma série de trabalhos MapReduce. O Hive se parece mais com um sistema de gerenciamento de banco de dados relacional do que o Pig e é usado com dados mais estruturados. Para dados não estruturados, o Pig é a melhor opção. Consulte [Usar o Hive com Hadoop no HDInsight](hdinsight-use-hive.md).

O <a target="_blank" href="https://cwiki.apache.org/confluence/display/Hive/HCatalog/">Apache HCatalog</a> é uma camada de gerenciamento de armazenamento e de tabela para Hadoop que apresenta a você uma exibição de dados relacional. No HCatalog, você pode ler e gravar arquivos em qualquer formato que funcione para um Hive SerDe (serializador desserializador).

### <a name="mahout"></a>Mahout
<a target="_blank" href="https://mahout.apache.org/">Apache Mahout</a> é uma biblioteca de algoritmos de aprendizado de máquina que são executados no Hadoop. Usando princípios de estatísticas, os aplicativos de aprendizado de máquina ensinam aos sistemas a aprender com os dados e a usar os resultados passados para determinar um comportamento futuro. Consulte [Gerar recomendações de vídeo usando o Mahout no Hadoop](hdinsight-mahout.md).

### <a name="mapreduce"></a>MapReduce
O MapReduce é uma estrutura de software herdada para Hadoop para escrever aplicativos que processam conjuntos de Big Data paralelamente. Um trabalho do MapReduce divide grandes conjuntos de dados e organiza os dados em pares de valores chave para processamento. Os trabalhos MapReduce são executados em [YARN](#yarn). Consulte <a target="_blank" href="http://wiki.apache.org/hadoop/MapReduce">MapReduce</a> no Wiki do Hadoop.

### <a name="oozie"></a>Oozie
<a target="_blank" href="http://oozie.apache.org/">Apache Oozie</a> é um sistema de coordenação do fluxo de trabalho que gerencia trabalhos do Hadoop. É integrado com a pilha do Hadoop e oferece suporte a trabalhos do Hadoop para MapReduce, Pig, Hive e Sqoop. Também pode ser usado para agendar trabalhos específicos para um sistema, como programas Java ou scripts de shell. Confira [Usar o Oozie com o Hadoop](hdinsight-use-oozie-linux-mac.md).

### <a name="phoenix"></a>Phoenix
O <a  target="_blank" href="http://phoenix.apache.org/">Apache Phoenix</a> é uma camada de banco de dados relacional em HBase. O Phoenix inclui um driver JDBC que permite a você consultar e gerenciar tabelas SQL diretamente. O Phoenix converte consultas e outras instruções em chamadas de API NoSQL nativas, em vez de usar o MapReduce, permitindo, assim, aplicativos mais rápidos sobre repositórios NoSQL. Consulte [Usar Apache Phoenix e SQuirreL com clusters do HBase](hdinsight-hbase-phoenix-squirrel.md).

### <a name="pig"></a>Pig
<a  target="_blank" href="http://pig.apache.org/">Apache Pig</a> é uma plataforma de alto nível que permite executar transformações complexas de MapReduce em grandes conjuntos de dados usando uma linguagem de script simples, chamada Pig Latin. O Pig traduz os scripts em Pig Latin para que sejam executados dentro do Hadoop. Você pode criar Funções Definidas pelo Usuário (UDFs) para estender a Pig Latin. Confira [Usar o Pig com o Hadoop](hdinsight-use-pig.md).

### <a name="sqoop"></a>Sqoop
<a  target="_blank" href="http://sqoop.apache.org/">Apache Sqoop</a> é uma ferramenta que transferências dados em massa entre o Hadoop e bancos de dados relacionais, como um SQL, ou outros repositórios de dados estruturados, da forma mais eficiente possível. Consulte [Usar o Sqoop com o Hadoop](hdinsight-use-sqoop.md).

### <a name="tez"></a>Tez
O <a  target="_blank" href="http://tez.apache.org/">Apache Tez</a> é uma estrutura de aplicativo baseada no Hadoop YARN que executa gráficos complexos, gráficos acíclicos de processamento geral de dados. Ele é um sucessor mais flexível e poderoso para a estrutura do MapReduce que permite que processos de uso intensivo de dados, como o Hive, sejam executados com mais eficiência em grande escala. Consulte ["Usar o Apache Tez para melhorar o desempenho" em Usar o Hive e HiveQL](hdinsight-use-hive.md#usetez).

### <a name="yarn"></a>YARN
O Apache YARN é a última geração do MapReduce (MapReduce 2.0, ou MRv2) e permite cenários de processamento de dados além do processamento em lotes do MapReduce com maior escalabilidade e processamento em tempo real. O YARN fornece gerenciamento de recursos e uma estrutura de aplicativo distribuída. Trabalhos MapReduce executados em YARN. Consulte <a target="_blank" href="http://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html">Apache Hadoop NextGen MapReduce (YARN)</a>.

### <a name="zookeeper"></a>ZooKeeper
O <a  target="_blank" href="http://zookeeper.apache.org/">Apache ZooKeeper</a> coordena processos em grandes sistemas distribuídos usando um namespace hierárquico compartilhado de registros de dados (znodes). Os Znodes contêm pequenas quantidades de metainformações necessárias para coordenar processos: status, local, configuração e assim por diante. Veja um exemplo de [ZooKeeper com um cluster HBase e Apache Phoenix](hdinsight-hbase-phoenix-squirrel-linux.md). 

## <a name="programming-languages-on-hdinsight"></a>Linguagens de programação no HDInsight
Os clusters de HDInsight - Spark, HBase, Kafka, Hadoop e outros clusters - dão suporte a várias linguagens de programação, mas alguns não estão instalados por padrão. No caso de bibliotecas, módulos ou pacotes não instalados por padrão, [use uma ação de script para instalar o componente](hdinsight-hadoop-script-actions-linux.md). 

### <a name="default-programming-language-support"></a>Suporte padrão à linguagem de programação
Por padrão, os clusters HDInsight são compatíveis com:

* Java
* Python

Idiomas adicionais podem ser instalados usando [ações de script](hdinsight-hadoop-script-actions-linux.md).

### <a name="java-virtual-machine-jvm-languages"></a>Linguagens JVM (máquina virtual Java)
Muitas linguagens diferentes de Java podem ser executadas em uma JVM (máquina virtual Java); no entanto, a execução de algumas dessas linguagens pode exigir mais componentes instalados no cluster.

Essas linguagens baseadas em JVM são permitidas nos clusters HDInsight:

* Clojure
* Jython (Python para Java)
* Scala

### <a name="hadoop-specific-languages"></a>Linguagens específicas do Hadoop
Os clusters HDInsight dão suporte às seguintes linguagens que são específicas ao ecossistema da pilha de tecnologias do Hadoop:

* Pig Latin para trabalhos do Pig
* HiveQL para trabalhos do Hive e SparkSQL

## <a name="hdinsight-standard-and-hdinsight-premium"></a>HDInsight Standard e HDInsight Premium
O HDInsight fornece ofertas de nuvem de big data em duas categorias, Standard e Premium. O HDInsight Standard fornece um cluster de porte empresarial que as organizações podem usar para executar suas cargas de trabalho de big data. O HDInsight Premium baseia-se nos recursos padrão e fornece recursos avançados de análise e segurança para um cluster HDInsight. Para obter mais informações, confira [Azure HDInsight Premium](hdinsight-component-versioning.md#hdinsight-standard-and-hdinsight-premium)

## <a name="microsoft-business-intelligence-and-hdinsight"></a>Microsoft Business Intelligence e HDInsight
As ferramentas familiares de BI (business intelligence) recuperam, analisam e relatam dados integrados ao HDInsight usando o suplemento Power Query ou o Driver ODBC do Microsoft Hive:

* [Conectar o Excel ao Hadoop com o Power Query](hdinsight-connect-excel-power-query.md): aprenda a conectar o Excel à conta de Armazenamento do Azure que armazena os dados de seu cluster HDInsight usando o Microsoft Power Query para Excel. Estação de trabalho do Windows necessária. 
* [Conectar o Excel ao Hadoop com o Driver ODBC do Microsoft Hive](hdinsight-connect-excel-hive-odbc-driver.md): aprenda a importar dados do HDInsight com o Driver ODBC do Microsoft Hive. Estação de trabalho do Windows necessária. 
* [Plataforma de nuvem da Microsoft](http://www.microsoft.com/server-cloud/solutions/business-intelligence/default.aspx): saiba mais sobre o Power BI para Office 365, baixe a avaliação do SQL Server e configure o SharePoint Server 2013 e o SQL Server BI.
* [SQL Server Analysis Services](http://msdn.microsoft.com/library/hh231701.aspx)
* [Serviços de Relatório do SQL Server](http://msdn.microsoft.com/library/ms159106.aspx)


## <a name="next-steps"></a>Próximas etapas

* [Introdução ao Hadoop no HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md): um tutorial de início rápido para provisionamento de clusters do Hadoop no HDInsight e execução de consultas Hive de exemplo.
* [Introdução ao Spark no HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md): um tutorial de início rápido para criar um cluster Spark e executar consultas interativas do Spark SQL.
* [Usar Servidor R no HDInsight](hdinsight-hadoop-r-server-get-started.md): comece a usar o Servidor R no HDInsight Premium.
* [Provisionar clusters HDInsight](hdinsight-hadoop-provision-linux-clusters.md): saiba como provisionar um cluster Hadoop no HDInsight por meio do portal do Azure, da CLI do Azure ou do Azure PowerShell.


[component-versioning]: hdinsight-component-versioning.md
[zookeeper]: http://zookeeper.apache.org/