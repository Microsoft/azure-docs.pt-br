---
title: O que é o Apache Spark – Azure HDInsight
description: Este artigo fornece uma introdução ao Spark no HDInsight e aos diferentes cenários em que você pode usar o cluster Spark no HDInsight.
ms.service: hdinsight
ms.custom: contperf-fy21q1
ms.topic: overview
ms.date: 09/21/2020
ms.openlocfilehash: deaab53feed4d5402ff0a5dcf9b2bd208e85bb18
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106062685"
---
# <a name="what-is-apache-spark-in-azure-hdinsight"></a>O que é o Apache Spark no Azure HDInsight

O Apache Spark é uma estrutura de processamento paralelo que dá suporte ao processamento na memória para melhorar o desempenho de aplicativos de análise de Big Data. O Apache Spark no Azure HDInsight é a implementação do Apache Spark na nuvem, realizada pela Microsoft. O HDInsight facilita a criação e a configuração de um cluster Spark no Azure. Os clusters Spark no HDInsight são compatíveis com o [Armazenamento de blobs do Azure](../../storage/common/storage-introduction.md), [Azure Data Lake Storage Gen1](../../data-lake-store/data-lake-store-overview.md) ou [Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-introduction.md). Portanto, você pode usar clusters Spark do HDInsight para processar os dados armazenados no Azure. Para obter os componentes e informações de versão, confira [Componentes e versões do Apache Hadoop no Azure HDInsight](../hdinsight-component-versioning.md).

:::image type="content" source="./media/apache-spark-overview/hdinsight-spark-overview.png" alt-text="Spark: uma estrutura unificada" border="false":::

## <a name="what-is-apache-spark"></a>O que é o Apache Spark?

O Spark oferece primitivos para computação de cluster na memória. Um trabalho do Spark pode carregar e armazenar dados em cache na memória e consultá-los várias vezes. A computação na memória é muito mais rápida do que aplicativos baseados em disco, como o Hadoop, que compartilha dados por meio do HDFS (Sistema de Arquivos Distribuído do Hadoop). O Spark também se integra à linguagem de programação Scala para permitir a manipulação de conjuntos de dados distribuídos como coleções locais. Não é necessário para estruturar tudo como operações de mapeamento e redução.

:::image type="content" source="./media/apache-spark-overview/map-reduce-vs-spark1.png" alt-text="MapReduce tradicional X Spark" border="false":::

Os clusters Spark no HDInsight oferecem um serviço Spark totalmente gerenciado. Os benefícios da criação de um cluster Spark no HDInsight estão relacionados aqui.

| Recurso | Descrição |
| --- | --- |
| Criação facilitada |Você pode criar um novo cluster do Spark no HDInsight em minutos usando o portal do Azure, o Azure PowerShell ou o SDK .NET do HDInsight. Confira [Introdução ao cluster do Apache Spark no HDInsight](apache-spark-jupyter-spark-sql-use-portal.md). |
| Fácil de uso |O cluster Spark no HDInsight inclui Jupyter Notebooks e Apache Zeppelin Notebooks. Você pode usar esses blocos de anotações para processar e visualizar dados interativamente. Confira [Usar notebooks do Apache Zeppelin com o Apache Spark](apache-spark-zeppelin-notebook.md) e [Carregar dados e executar consultas em um cluster do Apache Spark](apache-spark-load-data-run-query.md).|
| APIs REST |Os clusters Spark no HDInsight incluem [Apache Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server), um servidor de trabalho do Spark baseado em API REST para enviar e monitorar trabalhos remotamente. Veja [Usar a API REST do Apache Spark para enviar trabalhos remotos para um cluster do HDInsight Spark](apache-spark-livy-rest-interface.md).|
| Suporte para o Armazenamento do Azure | Os clusters Spark no HDInsight podem usar o Azure Data Lake Storage Gen1/Gen2 como o armazenamento primário ou um armazenamento adicional. Para obter mais informações sobre o Data Lake Storage Gen1, confira [Azure Data Lake Storage Gen1](../../data-lake-store/data-lake-store-overview.md). Para obter mais informações sobre o Data Lake Storage Gen2, confira [Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-introduction.md).|
| Integração com serviços do Azure |O cluster Spark no HDInsight é fornecido com um conector para Hubs de Eventos do Azure. Você pode criar aplicativos de streaming usando os Hubs de Eventos. Isso inclui o Apache Kafka, que já está disponível como parte do Spark. |
| Suporte para ML Server | O suporte para ML Server no HDInsight é fornecido como o tipo de cluster **ML Services**. Você pode configurar um cluster de ML Services para executar cálculos R distribuídos com as velocidades prometidas com um cluster Spark. Para obter mais informações, consulte [O que são os Serviços de ML no Microsoft Azure HDInsight?](../r-server/r-server-overview.md). |
| Integração com IDEs de terceiros | O HDInsight fornece vários plug-ins IDE que são úteis para criar e enviar aplicativos a um cluster Spark do HDInsight. Para obter mais informações, confira [Usar o Azure Toolkit for IntelliJ IDEA](apache-spark-intellij-tool-plugin.md), [Usar as Ferramentas do Spark e Hive para VS Code](../hdinsight-for-vscode.md) e [Usar o Azure Toolkit for Eclipse](apache-spark-eclipse-tool-plugin.md).|
| Consultas simultâneas |Os clusters Spark no HDInsight dão suporte a consultas simultâneas. Essa funcionalidade permite que várias consultas de um usuário ou várias consultas de vários usuários e aplicativos compartilhem os mesmos recursos de cluster. |
| Armazenamento em cache no SSDs |Você pode escolher os dados em cache na memória ou em SSDs anexados a nós do cluster. O armazenamento em cache na memória proporciona o melhor desempenho das consultas, porém o custo pode ser alto. O armazenamento em cache oferece uma ótima opção para melhorar o desempenho das consultas, sem necessidade de criar um cluster que caiba todo o conjunto de dados na memória. Confira [Aprimorar o desempenho de cargas de trabalho do Apache Spark usando o Cache de E/S do Azure HDInsight](apache-spark-improve-performance-iocache.md). |
| Integração com ferramentas de BI |Os clusters Spark no HDInsight fornecem conectores para ferramentas de BI como o Power BI para análise de dados. |
| Bibliotecas Anaconda pré-carregadas |Os clusters Spark no HDInsight são fornecidos com bibliotecas Anaconda pré-instaladas. [Anaconda](https://docs.continuum.io/anaconda/) fornece quase 200 bibliotecas de machine learning, análise de dados, visualização, etc. |
| Adaptabilidade | O HDInsight permite alterar o número de nós de cluster dinamicamente com o recurso de dimensionamento automático. Confira [Escalar automaticamente os clusters do Azure HDInsight](../hdinsight-autoscale-clusters.md). Além disso, os clusters Spark podem ser removidos sem perda de dados, pois todos os dados ficam armazenados no Armazenamento de blobs do Azure, [Azure Data Lake Storage Gen1](../../data-lake-store/data-lake-store-overview.md) ou [Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-introduction.md). |
| Contrato de Nível de Serviço |Os clusters Spark no HDInsight vêm com suporte 24 horas por dia, 7 dias por semana, e um SLA de 99,9% de tempo de atividade. |

Os clusters do Apache Spark no HDInsight incluem os seguintes componentes que estão disponíveis nos clusters por padrão.

* [Núcleo do Spark](https://spark.apache.org/docs/latest/). Inclui Spark Core, Spark SQL, APIs de streaming do Spark, GraphX e MLlib.
* [Anaconda](https://docs.continuum.io/anaconda/)
* [Apache Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server)
* [Jupyter Notebook](https://jupyter.org)
* [Bloco de anotações do Apache Zeppelin](http://zeppelin-project.org/)

Os clusters Spark no HDInsight contam com um [driver ODBC](/sql/connect/odbc/download-odbc-driver-for-sql-server) para conectividade de ferramentas de BI, como o Microsoft Power BI.

## <a name="spark-cluster-architecture"></a>Arquitetura do cluster Spark

:::image type="content" source="./media/apache-spark-overview/hdi-spark-architecture.png" alt-text="A arquitetura do HDInsight Spark" border="false":::

É fácil de entender os componentes do Spark entendendo como o Spark funciona nos clusters HDInsight.

Os aplicativos Spark são executados como conjuntos independentes de processos em um cluster. Coordenado pelo objeto SparkContext em seu programa principal (chamado de programa de driver).

O SparkContext pode se conectar a vários tipos de gerenciadores de cluster, que fornecem recursos entre aplicativos. Esses gerenciadores de cluster incluem o Apache Mesos, o Apache Hadoop YARN ou o gerenciador de cluster do Spark. No HDInsight, o Spark é executado usando o gerenciador de cluster YARN. Após a conexão, o Spark adquire executores em nós de trabalhado no cluster, que são processos que executam cálculos e armazenam dados para o seu aplicativo. Em seguida, ele envia o código do seu aplicativo (definido pelos arquivos JAR ou Python passados ao SparkContext) para os executores. Por fim, o SparkContext envia tarefas para serem realizadas pelos executores.

O SparkContext executa a função principal do usuário e as várias operações paralelas nos nós de trabalho. Em seguida, o SparkContext coleta os resultados das operações. Os nós de trabalho leem e gravam dados do Sistema de Arquivos Distribuído do Hadoop. Os nós de trabalho também armazenam dados transformados na memória em cache como RDDs (Conjuntos de Dados Distribuído Resiliente).

O SparkContext se conecta ao mestre do Spark e é responsável por converter um aplicativo em um DAG (grafo direcionado acíclico) de tarefas individuais. Tarefas que são executadas dentro de um processo de executor nos nós de trabalho. Cada aplicativo obtém seus próprios processos de executor. Esses processos se mantêm pela duração do aplicativo e executam tarefas em vários threads.

## <a name="spark-in-hdinsight-use-cases"></a>Casos de uso do Spark no HDInsight

Os clusters Spark no HDInsight ativam os seguintes cenários principais:

### <a name="interactive-data-analysis-and-bi"></a>Análise de dados interativa e BI

O Apache Spark no HDInsight armazena dados no Armazenamento de Blobs do Azure, no Azure Data Lake Gen1 ou no Azure Data Lake Storage Gen2. Os especialistas de negócios e os principais tomadores de decisões podem analisar e criar relatórios sobre esses dados. Além disso, podem usar o Microsoft Power BI para criar relatórios interativos com base nos dados analisados. Os analistas podem começar com dados não estruturados/semiestruturados no armazenamento de cluster, definir um esquema para os dados usando blocos de anotações e, em seguida, criar modelos de dados usando o Microsoft Power BI. Os clusters do Spark no HDInsight também dão suporte a várias ferramentas de BI de terceiros. Um exemplo dessas ferramentas é o Tableau, e elas facilitam o trabalho de analistas de dados, especialistas de negócios e tomadores de decisões importantes.

* [Tutorial: Visualizar dados de Spark usando o Power BI](apache-spark-use-bi-tools.md)

### <a name="spark-machine-learning"></a>Machine Learning do Spark

O Apache Spark acompanha o [MLlib](https://spark.apache.org/mllib/). O MLlib é uma biblioteca de machine learning criada com base no Spark que pode ser usada em um cluster do Spark no HDInsight. O cluster Spark no HDInsight também inclui o Anaconda, uma distribuição do Python com diferentes tipos de pacotes para machine learning. Ele conta também com suporte interno para notebooks do Jupyter e do Zeppelin, fornecendo a você um ambiente para criar aplicativos de aprendizado de máquina.

* [Tutorial: Prever temperaturas de prédios usando dados do sistema de HVAC](apache-spark-ipython-notebook-machine-learning.md)  
* [Tutorial: Prever resultados de inspeção de alimentos](apache-spark-machine-learning-mllib-ipython.md)

### <a name="spark-streaming-and-real-time-data-analysis"></a>Análise de dados de streaming e em tempo real do Spark

Os clusters Spark no HDInsight dão suporte avançado para criar soluções de análise em tempo real. O Spark já tem conectores para ingerir dados de várias fontes, como Kafka, Flume, Twitter, ZeroMQ ou soquetes TCP. O Spark no HDInsight adiciona suporte de primeira classe para ingestão de dados dos Hubs de Eventos do Azure. Hubs de Eventos é o serviço de enfileiramento de mensagens mais usado no Azure. A compatibilidade total com os Hubs de Eventos torna os clusters Spark no HDInsight a plataforma ideal para a criação de pipeline de análise em tempo real.

* [Visão geral do Streaming do Apache Spark](apache-spark-streaming-overview.md)
* [Visão geral do Streaming Estruturado do Apache Spark](apache-spark-structured-streaming-overview.md)

## <a name="next-steps"></a>Próximas etapas

Nesta visão geral, você obtém algumas noções básicas do Apache Spark no Azure HDInsight.  Você pode usar os seguintes artigos para saber mais sobre o Apache Spark no HDInsight e pode criar um cluster HDInsight Spark e executar outras consultas do Spark de exemplo:

* [Início Rápido: Criar um cluster Apache Spark no HDInsight e executar uma consulta interativa usando o Jupyter](./apache-spark-jupyter-spark-sql-use-portal.md)
* [Tutorial: Carregar dados e executar consultas em um trabalho do Apache Spark usando o Jupyter](./apache-spark-load-data-run-query.md)
* [Tutorial: Visualizar dados de Spark usando o Power BI](apache-spark-use-bi-tools.md)
* [Tutorial: Prever temperaturas de prédios usando dados do sistema de HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Otimizar desempenho de trabalhos do Spark](apache-spark-perf.md)
