---
title: O que é o Azure HDInsight
description: Uma introdução ao HDInsight, à pilha e aos componentes da tecnologia Apache Hadoop e Apache Spark, incluindo Kafka, Hive, Storm e HBase para análise de Big Data.
ms.service: hdinsight
ms.topic: overview
ms.custom: contperf-fy21q1
ms.date: 08/21/2020
ms.openlocfilehash: cbaa84c73a5e332dca60286bc35200761e546dd3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104864220"
---
# <a name="what-is-azure-hdinsight"></a>O que é o Azure HDInsight?

O Azure HDInsight é um serviço de análise totalmente gerenciado, completo e open-source na nuvem para empresas. Você pode usar estruturas de software livre como Hadoop, Apache Spark, Apache Hive, LLAP, Apache Kafka, Apache Storm, R e outros.

## <a name="what-is-hdinsight-and-the-hadoop-technology-stack"></a>O que é o HDInsight e a pilha de tecnologias do Hadoop?

O Azure HDInsight é uma distribuição de nuvem dos componentes do Hadoop. O Azure HDInsight torna mais fácil, rápido e econômico processar grandes quantidades de dados. Você pode usar as estruturas de código aberto mais populares, como Hadoop, Spark, Hive, LLAP, Kafka, Storm, R e muito mais. Com essas estruturas, você pode habilitar uma grande variedade de cenários, como extração, transformação e carregamento (ETL), data warehouse, aprendizado de máquina e IoT.

Para ver os componentes disponíveis da pilha de tecnologia do Hadoop no HDInsight, confira [Componentes e versões disponíveis com o HDInsight](./hdinsight-component-versioning.md). Para ler mais sobre o Hadoop no HDInsight, consulte a [Página de recursos do Azure para HDInsight](https://azure.microsoft.com/services/hdinsight/).

## <a name="what-is-big-data"></a>O que é big data?

Mais do que nunca, o Big Data está sendo coletado em volumes crescentes, em velocidades mais altas e em uma maior variedade de formatos. Ele pode ser histórico (referente a dados armazenados) ou em tempo real (o que significa que é transmitido da fonte). Consulte [Cenários de uso do HDInsight](#scenarios-for-using-hdinsight) para saber mais sobre os casos de uso mais comuns de Big Data.

## <a name="why-should-i-use-azure-hdinsight"></a>Por que devo usar o Azure HDInsight?

Esta seção lista os recursos do Azure HDInsight.

|Recurso  |Descrição  |
|---------|---------|
|Nativo de nuvem     |     O Azure HDInsight permite que você crie clusters otimizados para Hadoop, Spark,  [Interactive Query (LLAP)](./interactive-query/apache-interactive-query-get-started.md), Kafka, Storm, HBase e ML Services no Azure. O HDInsight também oferece um SLA de ponta a ponta em todas as suas cargas de trabalho de produção.  |
|De baixo custo e escalonável     | O HDInsight permite aumentar ou reduzir as cargas de trabalho. É possível reduzir os custos criando clusters sob demanda e pagando apenas pelo que for usado. Você também pode compilar pipelines de dados para operacionalizar seus trabalhos. A computação e o armazenamento desacoplados fornecem melhor desempenho e flexibilidade. |
|Seguro e em conformidade    | O HDInsight permite a proteção dos ativos de dados corporativos com a Rede Virtual do Microsoft Azure, criptografia e integração com o Azure Active Directory. O HDInsight também atende aos padrões de conformidade mais populares do setor e do governo.        |
|Monitoramento    | O Azure HDInsight é integrado aos logs do Azure Monitor para fornecer uma interface única com a qual você pode monitorar todos os seus clusters.        |
|Disponibilidade global | O HDInsight está disponível em mais regiões do que qualquer outra oferta de análise de Big Data. O Azure HDInsight também está disponível no Azure Governamental, na China e na Alemanha, o que permite atender às necessidades da sua empresa nas principais áreas soberanas. |  
|Produtividade     |  O Microsoft Azure HDInsight permite que você use ferramentas produtivas avançadas para o Hadoop e o Spark com seus ambientes de desenvolvimento preferidos. Esses ambientes de desenvolvimento incluem Visual Studio, VSCode, Eclipse e IntelliJ para dar suporte ao Scala, Python, R, Java e .NET. Os cientistas de dados também podem colaborar usando notebooks populares como o Jupyter e o Zeppelin.    |
|Extensibilidade     |  Você pode estender os clusters do HDInsight com componentes instalados (Hue, Presto, etc.) usando ações de script, adicionando nós de borda ou integrando outros aplicativos de Big Data certificados. O HDInsight permite a integração perfeita com as soluções de Big Data mais populares com uma implantação com um clique.|

## <a name="scenarios-for-using-hdinsight"></a>Cenários de uso do HDInsight

O Azure HDInsight pode ser usado para uma variedade de cenários no processamento de Big Data. Podem ser dados históricos (dados que já estão coletados e armazenados) ou dados em tempo real (dados que são transmitidos diretamente da fonte). Os cenários para processar esses dados podem ser resumidos nas seguintes categorias:

### <a name="batch-processing-etl"></a>Processamento em lotes (ETL)

Extração, transformação e carregamento (ETL) é um processo em que os dados estruturados ou não estruturados são extraídos de fontes de dados heterogêneas. Em seguida, ele é transformado em um formato estruturado e carregado no repositório de dados. Você pode usar os dados transformados para ciência de dados ou data warehousing.

### <a name="data-warehousing"></a>Data warehousing

É possível usar o HDInsight para executar consultas interativas em escalas petabyte sobre dados estruturados ou não estruturados em qualquer formato. Também é possível criar modelos conectando-os a ferramentas de BI.

Arquitetura do HDInsight: data warehousing

### <a name="internet-of-things-iot"></a>Internet das coisas (IoT)

Você pode usar o HDInsight para processar os dados de streaming recebidos em tempo real de diferentes tipos de dispositivos. Para obter mais informações, [leia esta postagem de blog do Azure que informa a visualização pública do Apache Kafka no HDInsight com Azure Managed Disks](https://azure.microsoft.com/blog/announcing-public-preview-of-apache-kafka-on-hdinsight-with-azure-managed-disks/).

Arquitetura do HDInsight: Internet das Coisas

### <a name="data-science"></a>Ciência de dados

Você pode usar o HDInsight para compilar aplicativos que extraem informações críticas dos dados. Você também pode usar o Azure Machine Learning antes disso para prever tendências futuras para seu negócio. Para obter mais informações, [leia esta história de cliente](https://customers.microsoft.com/story/pros).

Arquitetura do HDInsight: ciência de dados

### <a name="hybrid"></a>Híbrido

Você pode usar o HDInsight para estender sua infraestrutura de Big Data local existente ao Azure para aproveitar os recursos de análise avançada da nuvem.

Arquitetura do HDInsight: híbrido

## <a name="cluster-types-in-hdinsight"></a>Tipos de cluster no HDInsight

O HDInsight inclui tipos específicos de cluster e recursos de personalização do cluster, como a capacidade de adicionar componentes, utilitários e idiomas. O HDInsight oferece os seguintes tipos de cluster:

|Tipo de cluster | Descrição |
|---|---|
|[Apache Hadoop](./hadoop/apache-hadoop-introduction.md)|uma estrutura que usa HDFS, gerenciamento de recursos YARN e um modelo de programação MapReduce simples para processar e analisar dados em lote em paralelo.|
|[Apache Spark](./spark/apache-spark-overview.md)|uma estrutura de processamento paralelo de software livre que dá suporte ao processamento na memória para melhorar o desempenho dos aplicativos de análise de Big Data. Confira [O que é o Apache Spark no HDInsight?](./spark/apache-spark-overview.md).|
|[HBase no Apache](./hbase/apache-hbase-overview.md)|um banco de dados NoSQL baseado em Hadoop que fornece acesso aleatório e coerência forte para big data não estruturado e semiestruturado (potencialmente, bilhões de linhas vezes milhões de colunas). Confira [O que é o HBase em HDInsight?](./hbase/apache-hbase-overview.md)|
|[ML Services](./r-server/r-server-overview.md)|um servidor para hospedagem e gerenciamento paralelo, processos R distribuídos. Ele fornece aos cientistas de dados, estatísticos e programadores de R o acesso sob demanda a métodos escalonáveis e distribuídos de análise no HDInsight. Consulte a [Visão geral de ML Services no HDInsight](./r-server/r-server-overview.md).|
|[Apache Storm](./storm/apache-storm-overview.md)|um sistema de computação distribuído e em tempo real para processar rapidamente grandes fluxos de dados. O Storm é oferecido como um cluster gerenciado no HDInsight. Consulte [Analisar dados do sensor em tempo real usando o Storm e o Hadoop](./storm/apache-storm-overview.md).|
|[Consulta Interativa do Apache](./interactive-query/apache-interactive-query-get-started.md)|Caching na memória para consultas de Hive interativas e mais rápidas. Veja [Usar a consulta interativa no HDInsight](./interactive-query/apache-interactive-query-get-started.md).|
|[Apache Kafka](./kafka/apache-kafka-introduction.md)|uma plataforma de código-fonte aberto usada para criar aplicativos e pipelines de dados de transmissão. O Kafka também fornece funcionalidade de fila de mensagens, o que permite que você publique e assine fluxos de dados. Consulte [Uma introdução ao Apache Kafka no HDInsight](./kafka/apache-kafka-introduction.md).|

## <a name="open-source-components-in-hdinsight"></a>Componentes de código aberto no HDInsight

O Azure HDInsight permite que você crie clusters com estruturas de software livre como Hadoop, Spark, Hive, LLAP, Kafka, Storm, HBase e R. Por padrão, esses clusters vêm com outros componentes de software livre incluídos no cluster como Apache Ambari5, Avro5, Apache Hive3, HCatalog2, Apache Mahout2, Apache Hadoop MapReduce3, Apache Hadoop YARN2, Apache Phoenix3, Apache Pig3, Apache Sqoop3, Apache Tez3, Apache Oozie2 e Apache ZooKeeper5.  

## <a name="programming-languages-in-hdinsight"></a>Linguagens de programação no HDInsight

Os clusters do HDInsight, incluindo Spark, HBase, Kafka, Hadoop e outros, dão suporte a várias linguagens de programação. Algumas linguagens de programação não são instaladas por padrão. No caso de bibliotecas, módulos ou pacotes que não são instalados por padrão, use uma ação de script para instalar o componente.

|Linguagem de programação  |Informações  |
|---------|---------|
|Suporte padrão à linguagem de programação     | Por padrão, os clusters HDInsight são compatíveis com:<ul><li>Java</li><li>Python</li><li>.NET</li><li>Go</li></ul>  |
|Linguagens JVM (máquina virtual Java)     | Muitas linguagens diferentes do Java podem ser executadas em uma máquina virtual do Java (JVM). No entanto, se você executar algumas dessas linguagens, você talvez precise instalar componentes adicionais no cluster. As seguintes linguagens baseadas em JVM são permitidas nos clusters HDInsight: <ul><li>Clojure</li><li>Jython (Python para Java)</li><li>Scala</li></ul>     |
|Linguagens específicas do Hadoop     | Os clusters HDInsight dão suporte às seguintes linguagens que são específicas ao ecossistema da pilha de tecnologias do Hadoop: <ul><li>Pig Latin para trabalhos do Pig</li><li>HiveQL para trabalhos do Hive e SparkSQL</li></ul>        |

## <a name="development-tools-for-hdinsight"></a>Ferramentas de desenvolvimento para HDInsight

Você pode usar ferramentas de desenvolvimento do HDInsight, incluindo IntelliJ, Eclipse, Visual Studio Code e Visual Studio, para criar e enviar a consulta de dados do HDInsight e o trabalho com integração perfeita com o Azure.

* Azure Toolkit for IntelliJ10
* Azure Toolkit for Eclipse6
* Ferramentas do Azure HDInsight para VS Code13
* Ferramentas do Azure Data Lake para Visual Studio9

## <a name="business-intelligence-on-hdinsight"></a>Business intelligence no HDInsight

As ferramentas familiares de BI (business intelligence) recuperam, analisam e relatam os dados que estão integrados ao HDInsight usando o suplemento Power Query ou o Driver ODBC do Microsoft Hive:

* [Apache Spark BI usando ferramentas de visualização de dados com o Azure HDInsight](./spark/apache-spark-use-bi-tools.md)

* [Visualize dados do Apache Hive com o Microsoft Power BI no Azure HDInsight](./hadoop/apache-hadoop-connect-hive-power-bi.md)

* [Visualizar dados da consulta interativa do Hive com o Power BI no Azure HDInsight](./interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md)

* [Conectar o Excel ao Apache Hadoop com o Power Query](./hadoop/apache-hadoop-connect-excel-power-query.md) (requer Windows)

* [Conectar o Excel ao Apache Hadoop com o Driver ODBC do Microsoft Hive](./hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md) (requer Windows)


## <a name="in-region-data-residency"></a>Residência de dados na região 

O Spark, o Hadoop, o LLAP, o Storm e o MLService não armazenam dados do cliente; portanto, esses serviços atendem automaticamente aos requisitos de residência de dados na região, incluindo aqueles especificados na [Central de confiabilidade](https://azuredatacentermap.azurewebsites.net/). 

O Kafka e o HBase armazenam dados do cliente. Esses dados são armazenados automaticamente pelo Kafka e pelo HBase em uma única região; portanto, esse serviço atende aos requisitos de residência de dados na região, incluindo aqueles especificados na [Central de Confiabilidade](https://azuredatacentermap.azurewebsites.net/). 


As ferramentas conhecidas de BI (business intelligence) recuperam, analisam e relatam os dados integrados ao HDInsight usando o suplemento Power Query ou o Driver ODBC do Microsoft Hive.

## <a name="next-steps"></a>Próximas etapas

* [Criar cluster do Apache Hadoop no HDInsight](./hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md)
* Criar o cluster do Apache Spark – Portal
* [Segurança empresarial no Azure HDInsight](./domain-joined/hdinsight-security-overview.md)
