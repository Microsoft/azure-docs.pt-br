---
title: Extrair, transformar e carregar (ETL) em escala - Azure HDInsight
description: Saiba como extrair, transformar e carregar (ETL) é usado no HDInsight com o Apache Hadoop.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/28/2020
ms.openlocfilehash: f11b31ba698c193029834caada2e5c4cf3ef2902
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104866634"
---
# <a name="extract-transform-and-load-etl-at-scale"></a>Extrair, transformar e carregar (ETL) em escala

Extrair, transformar e carregar (ETL) é o processo pelo qual os dados são adquiridos de várias fontes. Os dados são coletados em um local padrão, limpos e processados. Por fim, os dados são carregados em um armazenamento de dados no qual podem ser consultados. Processos ETL herdados importam dados, os limpam no local e, em seguida, os armazenam em um mecanismo de dados relacionais. Com o Azure HDInsight, uma ampla variedade de componentes de ambiente do Apache Hadoop dá suporte a ETL em escala.

O uso do HDInsight no processo de ETL é resumido por este pipeline:

:::image type="content" source="./media/apache-hadoop-etl-at-scale/hdinsight-etl-at-scale-overview.png" alt-text="Visão geral do ETL do HDInsight em escala" border="false":::

As seções a seguir exploram cada uma das fases de ETL e seus componentes associados.

## <a name="orchestration"></a>Orquestração

Orquestração se estende por todas as fases do pipeline de ETL. Trabalhos ETL no HDInsight geralmente envolvem vários produtos trabalhando em conjunto com o outro. Por exemplo:

- Você pode usar o Apache Hive para limpar uma parte dos dados e o Apache Pig para limpar a outra.
- Você pode usar o Azure Data Factory para carregar dados no Banco de Dados SQL do Azure Data Lake Store.

A orquestração é necessária para executar o trabalho apropriado no momento apropriado.

### <a name="apache-oozie"></a>Apache Oozie

Apache Oozie é um sistema de coordenação do fluxo de trabalho que gerencia trabalhos do Hadoop. Oozie é executado em um cluster HDInsight e está integrado com a pilha do Hadoop. O Oozie dá suporte a trabalhos do Apache Hadoop para Apache MapReduce, Pig, Hive e outros. Você pode usar o Oozie para agendar trabalhos específicos para um sistema, como programas Java ou scripts de shell.

Para mais informações, consulte [Usar o Apache Oozie com o Apache Hadoop para definir e executar um fluxo de trabalho no Azure HDInsight](../hdinsight-use-oozie-linux-mac.md). Consulte também, [Operacionalizar o pipeline de dados](../hdinsight-operationalize-data-pipeline.md).

### <a name="azure-data-factory"></a>Fábrica de dados do Azure

O Azure Data Factory fornece recursos de orquestração na forma de plataforma como serviço (PaaS). O Azure Data Factory é um serviço de integração de dados baseado em nuvem. Ele permite criar fluxos de trabalho controlados por dados para orquestrar e automatizar a movimentação e transformação de dados.

Use o Azure Data Factory para:

1. Criar e agendar fluxos de trabalho conduzidos por dados. Esses pipelines ingerem dados de armazenamentos de dados diferentes.
1. Processar e transformar os dados usando serviços de computação, como HDInsight ou Hadoop. Você também pode usar o Spark, Azure Data Lake Analytics, Lote do Azure ou Azure Machine Learning para esta etapa.
1. Publicar dados de saída em armazenamentos de dados, como o Azure Synapse Analytics, para que os aplicativos de BI consumam.

Para obter mais informações sobre o Azure Data Factory, consulte a [documentação](../../data-factory/introduction.md).

## <a name="ingest-file-storage-and-result-storage"></a>Armazenamento de arquivo e armazenamento de resultados de ingestão

Os arquivos de dados de origem geralmente são carregados em um local no Armazenamento do Microsoft Azure ou no Azure Data Lake Storage. Os arquivos geralmente estão em um formato simples, como CSV. No entanto, eles podem estar em qualquer formato.

### <a name="azure-storage"></a>Armazenamento do Azure

O Armazenamento do Microsoft Azure tem metas específicas de adaptabilidade. Consulte [Escalabilidade e metas de desempenho do Armazenamento de Blobs](../../storage/blobs/scalability-targets.md) para mais informações. Para nós mais analíticos, o Armazenamento do Azure é dimensionado melhor ao lidar com vários arquivos menores. Desde que esteja dentro do limite da conta, o Armazenamento do Microsoft Azure garante o mesmo desempenho, apesar do tamanho dos arquivos. Você pode armazenar terabytes de dados e ainda obter um desempenho confiável. Isso é válido se você estiver usando um subconjunto ou todos os dados.

O Armazenamento do Microsoft Azure tem vários tipos de blobs. Um *append blob* é uma ótima opção para armazenar logs da web ou dados do sensor.

Vários blobs podem ser distribuídos em vários servidores para expandir o acesso a eles. Mas um único blob só é servido por um único servidor. Embora os blobs possam ser agrupados logicamente em contêineres de blob, o particionamento não é afetado de forma alguma por esse grupo.

O Armazenamento do Microsoft Azure tem uma camada de API WebHDFS para o armazenamento de blob. Todos os serviços do HDInsight podem acessar arquivos no armazenamento de blobs do Azure para limpeza de dados e processamento de dados. Isso é semelhante ao uso do Sistema de Arquivos Distribuído do Hadoop (HDFS) por esses serviços.

Os dados são normalmente incluídos no Armazenamento do Microsoft Azure por meio do PowerShell, do SDK do Armazenamento do Azure ou do AZCopy.

### <a name="azure-data-lake-storage"></a>Armazenamento do Azure Data Lake

O Azure Data Lake Storage é um repositório gerenciado e de hiperescala para dados de análise. Ele utiliza e é compatível com um paradigma de design semelhante ao HDFS. O Data Lake Storage oferece adaptabilidade ilimitada para a capacidade total e o tamanho de arquivos individuais. Ele é uma boa opção ao trabalhar com arquivos grandes, pois podem ser armazenados em vários nós. O particionamento de dados no Data Lake Storage é feito em segundo plano. Você obtém uma enorme taxa de transferência para executar trabalhos analíticos com milhares de executores simultâneos que leem e gravam centenas de terabytes de dados com eficiência.

Normalmente, os dados são ingeridos no Data Lake Storage por meio do Azure Data Factory. Você também pode usar os SDKs do Data Lake Storage, o serviço AdlCopy, o Apache DistCp ou o Apache Sqoop. O serviço escolhido depende da localização dos dados. Se estiverem em um cluster de Hadoop, você pode usar o Apache DistCp, o serviço AdlCopy ou o Azure Data Factory. Para dados no Armazenamento de Blobs do Azure, você poderá usar o SDK do .NET do Azure Data Lake Storage, o Azure PowerShell ou o Azure Data Factory.

O Data Lake Storage é otimizado para a ingestão de eventos por meio dos Hubs de Eventos do Azure ou Apache Storm.

### <a name="considerations-for-both-storage-options"></a>Considerações para ambas as opções de armazenamento

Para carregar conjuntos de valores no intervalo de terabytes, a latência de rede pode ser um grande problema. Em especial, isso é válido se os dados forem locais. Nesses casos, você pode usar estas opções:

- **Azure ExpressRoute:** Crie conexões privadas entre os datacenters do Azure e a infraestrutura local. Essas conexões proporcionam uma opção confiável para transferir grandes quantidades de dados. Para obter mais informações, confira a [documentação do ExpressRoute do Azure](../../expressroute/expressroute-introduction.md).

- **Upload de dados de unidades de disco rígido:** Você poderá usar o [serviço de Importação/Exportação do Azure](../../import-export/storage-import-export-service.md) para enviar unidades de disco rígido com seus dados para um datacenter do Azure. Seus dados são carregados pela primeira vez no armazenamento de blobs do Azure. Em seguida, você poderá usar o Azure Data Factory ou a ferramenta AdlCopy para copiar os dados do armazenamento de blobs do Azure para o Data Lake Storage.

### <a name="azure-synapse-analytics"></a>Azure Synapse Analytics

O Azure Synapse Analytics é uma opção apropriada para armazenar resultados preparados. Você pode usar o Azure HDInsight para executar esses serviços para o Azure Synapse Analytics.

O Azure Synapse Analytics é um repositório de banco de dados relacional otimizado para cargas de trabalho analíticas. Ele é escalado com base em tabelas particionadas. Tabelas podem ser particionadas em vários nós. Os nós são selecionados no momento da criação. Eles podem ser dimensionados após o fato, mas isso é um processo ativo que pode exigir a movimentação de dados. Para obter mais informações, consulte [gerenciar computação no Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md).

### <a name="apache-hbase"></a>HBase no Apache

O Apache HBase é um repositório de chave/valor disponível no Azure HDInsight. Ele é um banco de dados NoSQL de software livre, que se baseia no Hadoop e é modelado com base no Google BigTable. O HBase fornece acesso aleatório de alto desempenho e consistência forte para grandes quantidades de dados não estruturados e semiestruturados.

Como o HBase é um banco de dados sem esquema, você não precisa definir colunas e tipos de dado antes de usá-los. Os dados são armazenados nas linhas de uma tabela e agrupados por família de colunas.

O código-fonte aberto é dimensionado linearmente para lidar com petabytes de dados em milhares de nós. O HBase faz uso da redundância de dados, do processamento em lote e de outros recursos que são fornecidos por aplicativos distribuídos no ambiente do Hadoop.

O HBase é um bom destino para dados de sensor e de log para análise futura.

A adaptabilidade do HBase é determinada pelo número de nós no cluster HDInsight.

### <a name="azure-sql-databases"></a>Bancos de Dados SQL do Azure

O Azure oferece três bancos de dados relacionais de PaaS:

* [Banco de Dados SQL do Azure](../../azure-sql/database/sql-database-paas-overview.md) é uma implementação do Microsoft SQL Server. Para obter mais informações sobre o desempenho, consulte [Ajustando o desempenho no Banco de Dados SQL do Azure](../../azure-sql/database/performance-guidance.md).
* [Banco de Dados do Azure para MySQL](../../mysql/overview.md) é uma implementação do Oracle MySQL.
* [Banco de Dados do Azure para PostgreSQL](../../postgresql/quickstart-create-server-database-portal.md) é uma implementação do Oracle PostgreSQL.

Adicione mais CPU e memória para escalar verticalmente esses produtos.  Você também pode optar por usar discos premium com os produtos para melhorar o desempenho de I/O.

## <a name="azure-analysis-services"></a>Azure Analysis Services

O Azure Analysis Services é um mecanismo de dados analíticos usado para apoiar decisões e análises de negócios. Ele oferece dados analíticos para relatórios de negócios e aplicativos cliente, como o Power BI. Os dados analíticos também funcionam com Excel, relatórios do SQL Server Reporting Services e outras ferramentas de visualização de dados.

Cubos de análise podem ser dimensionados com a alteração das camadas para cada cubo individual. Para mais informações, consulte [Preços do Azure Analysis Services](https://azure.microsoft.com/pricing/details/analysis-services/).

## <a name="extract-and-load"></a>Extrair e carregar

Depois que os dados estiverem no Azure, você poderá usar vários serviços para extraí-los e carregá-los em outros produtos. O HDInsight oferece suporte a Sqoop e Flume.

### <a name="apache-sqoop"></a>Apache Sqoop

O Apache Sqoop é uma ferramenta projetada para transferir com eficiência os dados entre fontes de dados estruturados, semi-estruturados e não estruturados.

Sqoop usa MapReduce para importar e exportar os dados, para fornecer tolerância a falhas e a operação em paralelo.

### <a name="apache-flume"></a>Apache Flume

O Apache Flume é um serviço distribuído, confiável e disponível para coletar com eficiência, agregar e mover grandes quantidades de dados de log. Sua arquitetura flexível se baseia em fluxos de dados de streaming. O Flume é robusto e tolerante a falhas com mecanismos de confiabilidade ajustáveis. Ele tem muitos mecanismos de failover e recuperação. O Flume usa um modelo simples de dados extensíveis que permite a aplicação analítica online.

O Apache Flume não pode ser usado com o Azure HDInsight. Uma instalação local do Hadoop pode usar o Flume para enviar os dados para o armazenamento de blobs do Azure ou o Azure Data Lake Storage. Para obter mais informações, consulte [Usando o Apache Flume com HDInsight](https://web.archive.org/web/20190217104751/https://blogs.msdn.microsoft.com/bigdatasupport/2014/03/18/using-apache-flume-with-hdinsight/).

## <a name="transform"></a>Transformar

Após os dados ficarem no local escolhido, você precisará limpá-los, combiná-os ou prepará-los para um padrão de uso específico. Hive, Pig e Spark SQL são boas opções para esse tipo de trabalho. Todos eles dão suporte ao HDInsight.

## <a name="next-steps"></a>Próximas etapas

- [Usar o Apache Hive como uma ferramenta de ETL](apache-hadoop-using-apache-hive-as-an-etl-tool.md)
- [Usar Gen2 de armazenamento do Azure Data Lake com clusters de HDInsight do Azure](../hdinsight-hadoop-use-data-lake-storage-gen2.md)
- [Mover dados do Banco de Dados SQL do Azure para uma tabela do Apache Hive](./apache-hadoop-use-sqoop-mac-linux.md)
