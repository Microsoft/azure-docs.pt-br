---
title: Extrair, transformar e carregar (ETL) em escala - Azure HDInsight
description: Saiba como o ETL é usado no HDInsight com o Apache Hadoop.
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/13/2019
ms.author: ashishth
ms.openlocfilehash: d3ce2a19e195174cd302b25005b3b063d028b6cf
ms.sourcegitcommit: e5dcf12763af358f24e73b9f89ff4088ac63c6cb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/14/2019
ms.locfileid: "67137167"
---
# <a name="extract-transform-and-load-etl-at-scale"></a>Extrair, transformar e carregar (ETL) em escala

Extração, transformação e carregamento (ETL) é o processo pelo qual os dados são adquiridos de várias fontes, coletados em um local padrão, limpos e processados e, por fim, carregados em um repositório de dados do qual podem ser consultados. Processos ETL herdados importam dados, os limpam no local e, em seguida, os armazenam em um mecanismo de dados relacionais. Com o HDInsight, uma grande variedade de componentes do ecossistema Apache Hadoop suporta a execução de ETL em escala. 

O uso do HDInsight no processo de ETL pode ser resumido por este pipeline:

![Visão geral de ETL de HDInsight](./media/apache-hadoop-etl-at-scale/hdinsight-etl-at-scale-overview.png)

As seções a seguir exploram cada uma das fases de ETL e seus componentes associados.

## <a name="orchestration"></a>Orquestração

Orquestração se estende por todas as fases do pipeline de ETL. Trabalhos ETL no HDInsight geralmente envolvem vários produtos trabalhando em conjunto com o outro.  Você pode usar o Hive para limpar uma parte dos dados, enquanto o Pig limpa outra parte.  Você pode usar o Azure Data Factory para carregar dados no Banco de Dados SQL do Azure Data Lake Store.

A orquestração é necessária para executar o trabalho apropriado no momento apropriado.

### <a name="apache-oozie"></a>Apache Oozie

Apache Oozie é um sistema de coordenação do fluxo de trabalho que gerencia trabalhos do Hadoop. Oozie é executado em um cluster HDInsight e está integrado com a pilha do Hadoop. O Oozie dá suporte a trabalhos do Hadoop para Apache Hadoop MapReduce, Apache Pig, Apache Hive e Apache Sqoop. Oozie também pode ser usado para agendar trabalhos específicos para um sistema, como programas Java ou scripts de shell.

Para obter mais informações, confira [Usar o Apache Oozie com o Apache Hadoop para definir e executar um fluxo de trabalho no HDInsight](../hdinsight-use-oozie-linux-mac.md). Para obter um aprofundamento que mostra como usar o Oozie para impulsionar um pipeline de ponta a ponta, confira [Operacionalizar o pipeline de dados](../hdinsight-operationalize-data-pipeline.md). 

### <a name="azure-data-factory"></a>Fábrica de dados do Azure

O Azure Data Factory fornece recursos de orquestração na forma de plataforma como serviço. É um serviço de integração de dados baseado em nuvem que permite que você crie fluxos de trabalho orientados a dados na nuvem para orquestrar e automatizar a movimentação de dados e a transformação de dados. 

Usando o Azure Data Factory, você pode:

1. Criar e agendar fluxos de trabalhos orientados a dados (chamados pipelines) que ingerem dados provenientes de repositórios de dados diferentes.
2. Processar e transformar dados usando serviços de computação como o Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics, Azure Batch e Azure Machine Learning.
3. Publicar dados de saída em repositórios de dados como o SQL Data Warehouse do Azure para que estes sejam consumidos por aplicativos de business intelligence (BI).

Para obter mais informações sobre o Azure Data Factory, consulte a [documentação](../../data-factory/introduction.md).

## <a name="ingest-file-storage-and-result-storage"></a>Armazenamento de arquivo e armazenamento de resultados de ingestão

Os arquivos de dados de origem geralmente são carregados em um local no Armazenamento do Azure ou no Azure Data Lake Storage. Os arquivos podem estar em qualquer formato, mas geralmente são arquivos simples como CSVs. 

### <a name="azure-storage"></a>Armazenamento do Azure 

[Armazenamento do Azure](https://azure.microsoft.com/services/storage/blobs/) tem [metas específicas de escalabilidade](../../storage/common/storage-scalability-targets.md).  Para nós mais analíticos, o Armazenamento do Azure é dimensionado melhor ao lidar com vários arquivos menores.  O Armazenamento do Azure garante o mesmo desempenho, independentemente da quantidade ou tamanho dos arquivos (desde que estejam nos seus limites).  Isso significa que você pode armazenar terabytes de dados e ainda obter um desempenho consistente, se você estiver usando um subconjunto de dados ou todos os dados.

O Armazenamento do Azure tem vários tipos diferentes de blobs.  Um *append blob* é uma ótima opção para armazenar logs da web ou dados do sensor.  

Vários blobs podem ser distribuídos em vários servidores para escalar horizontalmente o acesso a eles, mas um único blob só pode ser atendido por um único servidor. Embora os blobs possam ser agrupados logicamente em contêineres de blob, o particionamento não é afetado de forma alguma por esse agrupamento.

O Armazenamento do Azure também tem uma camada de API WebHDFS para o armazenamento de blob.  Todos os serviços no HDInsight podem acessar arquivos no Armazenamento de Blobs do Azure para limpeza de dados e processamento de dados, da mesma forma que esses serviços usariam o Sistema de Arquivos Distribuído da Hadoop (HDFS).

Os dados são normalmente incluídos no Armazenamento do Azure usando o PowerShell, o SDK do Armazenamento do Azure ou AZCopy.

### <a name="azure-data-lake-storage"></a>Armazenamento do Azure Data Lake

O ADLS (Azure Data Lake Storage) é um repositório de hiperescala gerenciado para dados de análise que são compatíveis com o HDFS.  O ADLS usa um paradigma de design que é semelhante ao HDFS e oferece escalabilidade ilimitada em termos de capacidade total e o tamanho dos arquivos individuais. O ADLS é muito bom ao trabalhar com arquivos grandes, uma vez que um arquivo grande pode ser armazenado em vários nós.  O particionamento de dados em ADLS é feito em segundo plano.  Você obtém uma enorme taxa de transferência para executar trabalhos analíticos com milhares de executores simultâneos que leem e gravam centenas de terabytes de dados com eficiência.

Os dados são normalmente incluídos em ADLS usando o Azure Data Factory, SDKs do ADLS, AdlCopy Service, Apache DistCp ou Apache Sqoop.  Qual desses serviços depende em grande parte de onde os dados são.  Se a dados estão atualmente em um cluster de Hadoop existente, você pode usar o Apache DistCp, AdlCopy Service ou o Azure Data Factory.  Se ele estiver no Armazenamento de Blobs do Azure, você poderá usar o SDK do .NET do Azure Data Lake Storage, o Azure PowerShell ou o Azure Data Factory.

O ADLS também é otimizado para inclusão de eventos usando o Hub de Eventos do Azure ou Apache Storm.

#### <a name="considerations-for-both-storage-options"></a>Considerações para ambas as opções de armazenamento

Para carregar conjuntos de dados no intervalo terabyte, a latência de rede pode ser um problema grave, especialmente se os dados estão vindo de uma localização no local.  Nesses casos, você pode usar as opções a seguir:

* Azure ExpressRoute:  O Azure ExpressRoute permite criar conexões privadas entre os datacenters do Azure e a infraestrutura local. Essas conexões proporcionam uma opção confiável para transferir grandes quantidades de dados. Para obter mais informações, confira a [documentação do ExpressRoute do Azure](../../expressroute/expressroute-introduction.md).

* Carregamento de dados "offline". Você poderá usar o [serviço de Importação/Exportação do Azure](../../storage/common/storage-import-export-service.md) para enviar unidades de disco rígido com seus dados para um datacenter do Azure. Seus dados são carregados pela primeira vez nos Blobs de Armazenamento do Azure. Em seguida, você poderá usar o [Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md) ou a ferramenta [AdlCopy](../../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md) para copiar os dados dos blobs do Armazenamento do Azure para o Data Lake Storage.

### <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse

O SQL DW do Azure é uma ótima opção para armazenar resultados limpos e preparados para análise futura.  O HDInsight do Azure pode ser usado para executar esses serviços para o SQL DW do Azure.

O SQL Data Warehouse do Azure (SQL DW) é um repositório de banco de dados relacional otimizado para cargas de trabalho analíticas.  O SQL DW do Azure é dimensionado com base em tabelas particionadas.  Tabelas podem ser particionadas em vários nós.  Nós do SQL DW do Azure são selecionados no momento da criação.  Eles podem ser dimensionados após o fato, mas isso é um processo ativo que pode exigir a movimentação de dados. Consulte [SQL Data Warehouse - gerenciar computação](../../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md) para obter mais informações.

### <a name="apache-hbase"></a>HBase no Apache

O Apache HBase é um repositório de chave-valor disponível no Azure HDInsight.  O Apache HBase é um banco de dados NoSQL de código-fonte aberto, que é compilado no Hadoop e modelado com base em Google BigTable. O HBase fornece acesso aleatório de desempenho e uma coerência forte para grandes quantidades de dados não estruturados e semiestruturados em um banco de dados sem esquema, organizado por famílias de colunas.

Os dados são armazenados nas linhas de uma tabela e os dados contidos nas linhas são agrupados por famílias de colunas. O HBase é um banco de dados sem esquema, no aspecto de que nem as colunas nem os tipos de dados armazenados nelas precisam ser definidos antes de serem utilizados. O código-fonte aberto é dimensionado linearmente para lidar com petabytes de dados em milhares de nós. O HBase pode fazer uso da redundância de dados, do processamento em lote e de outros recursos que são fornecidos por aplicativos distribuídos no ecossistema do Hadoop.   

O HBase é um excelente destino para dados de sensor e de log para análise futura.

A escalabilidade do HBase é determinada pelo número de nós no cluster HDInsight.

### <a name="azure-sql-database-and-azure-database"></a>Banco de Dados SQL do Azure e Azure Database

O Azure oferece três bancos de dados relacionais diferentes como plataforma-como-um-serviço (PAAS).

* [Banco de Dados SQL do Azure](../../sql-database/sql-database-technical-overview.md) é uma implementação do Microsoft SQL Server. Para obter mais informações sobre o desempenho, consulte [Ajustando o desempenho no Banco de Dados SQL do Azure](../../sql-database/sql-database-performance-guidance.md).
* [Banco de Dados do Azure para MySQL](../../mysql/overview.md) é uma implementação do Oracle MySQL.
* [Banco de Dados do Azure para PostgreSQL](../../postgresql/quickstart-create-server-database-portal.md) é uma implementação do Oracle PostgreSQL.

Esses produtos escalam verticalmente, o que significa que eles são dimensionados com a adição de mais CPU e memória.  Você também pode optar por usar discos premium com os produtos para melhorar o desempenho de I/O.

## <a name="azure-analysis-services"></a>Azure Analysis Services 

Azure Analysis Services (AAS) é um mecanismo de dados analíticos usado em suporte a decisões e análise de negócios, fornecendo os dados analíticos para relatórios de negócios e aplicativos cliente, como o Power BI, Excel, relatórios do Reporting Services e outras ferramentas de visualização de dados.

Cubos de análise podem ser dimensionados alterando camadas para cada cubo individual.  Para obter mais informações, consulte [Preços do Azure Analysis Services](https://azure.microsoft.com/pricing/details/analysis-services/).

## <a name="extract-and-load"></a>Extrair e carregar

Depois que os dados estiverem no Azure, você pode usar vários serviços para extraí-los e carregá-los em outros produtos.  O HDInsight oferece suporte a Sqoop e Flume. 

### <a name="apache-sqoop"></a>Apache Sqoop

O Apache Sqoop é uma ferramenta projetada para transferir com eficiência os dados entre fontes de dados estruturados, semi-estruturados e não estruturados. 

Sqoop usa MapReduce para importar e exportar os dados, para fornecer tolerância a falhas e a operação em paralelo.

### <a name="apache-flume"></a>Apache Flume

O Apache Flume é um serviço distribuído, confiável e disponível para coletar com eficiência, agregar e mover grandes quantidades de dados de log. O Flume tem uma arquitetura simples e flexível, com base em fluxos de dados de streaming. O Flume é robusto e tolerante a falhas com mecanismos de confiabilidade ajustáveis e vários mecanismos de failover e recuperação. O Flume usa um modelo simples de dados extensíveis que permite a aplicação analítica on-line.

O Apache Flume não pode ser usado com o Azure HDInsight.  Uma instalação local do Hadoop pode usar o Flume para enviar os dados para Azure Storage Blobs ou o Azure Data Lake Storage.  Para obter mais informações, consulte [Usando o Apache Flume com HDInsight](https://web.archive.org/web/20190217104751/ https://blogs.msdn.microsoft.com/bigdatasupport/2014/03/18/using-apache-flume-with-hdinsight/).

## <a name="transform"></a>Transformar

Uma vez que dados estiverem no local escolhido, você precisa limpá-los, combiná-os ou prepará-los para um padrão de uso específico.  Hive, Pig e Spark SQL são boas opções para esse tipo de trabalho.  Todos eles são suportados no HDInsight. 

## <a name="next-steps"></a>Próximas etapas

* [Use o Apache Pig com o Apache Hadoop no HDInsight](hdinsight-use-pig.md)
* [Usando o Apache Hive como uma ferramenta de ETL](apache-hadoop-using-apache-hive-as-an-etl-tool.md) 
* [Usar Gen2 de armazenamento do Azure Data Lake com clusters de HDInsight do Azure](../hdinsight-hadoop-use-data-lake-storage-gen2.md)
