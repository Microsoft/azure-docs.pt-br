---
title: Cenários de dados envolvendo o Data Lake Storage Gen1 | Microsoft Docs
description: Entenda os diferentes cenários e as ferramentas usando quais dados podem ser ingeridos, processados, baixados e visualizados em um Data Lake Storage Gen1 (anteriormente conhecido como Azure Data Lake Store)
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: twooley
ms.openlocfilehash: 0b16154edbda4bedfd4e9b680ba4311e7a235212
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60878985"
---
# <a name="using-azure-data-lake-storage-gen1-for-big-data-requirements"></a>Usar o Azure Data Lake Storage Gen1 para exigências de big data

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Há quatro estágios principais no processamento de big data:

* Ingestão de grandes quantidades de dados em um repositório de dados, em tempo real ou em lotes
* Processamento dos dados
* Download dos dados
* Visualização dos dados

Neste artigo, analisamos esses estágios com relação ao Azure Data Lake Storage Gen1 para entender as opções e as ferramentas disponíveis para atender às suas necessidades de Big Data.

## <a name="ingest-data-into-data-lake-storage-gen1"></a>Ingerir dados no Data Lake Storage Gen1
Esta seção destaca as diferentes fontes de dados e as diferentes maneiras que os dados podem ser ingeridos em uma conta do Data Lake Storage Gen1.

![Ingestão de dados no Data Lake Storage Gen1](./media/data-lake-store-data-scenarios/ingest-data.png "Ingestão de dados no Data Lake Storage Gen1")

### <a name="ad-hoc-data"></a>Dados ad hoc
Representam conjuntos de dados menores que são usados para criar protótipos de um aplicativo de big data. Há diferentes maneiras de ingerir dados ad hoc, dependendo da fonte dos dados.

| Fonte de dados | Ingeri-la usando |
| --- | --- |
| Computador local |<ul> <li>[Portal do Azure](data-lake-store-get-started-portal.md)</li> <li>[PowerShell do Azure](data-lake-store-get-started-powershell.md)</li> <li>[CLI do Azure](data-lake-store-get-started-cli-2.0.md)</li> <li>[Usando as ferramentas do Data Lake para o Visual Studio](../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md) </li></ul> |
| Blob de Armazenamento do Azure |<ul> <li>[Azure Data Factory](../data-factory/connector-azure-data-lake-store.md)</li> <li>[ferramenta AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)</li><li>[DistCp em execução no cluster HDInsight](data-lake-store-copy-data-wasb-distcp.md)</li> </ul> |

### <a name="streamed-data"></a>Dados transmitidos
Representam os dados que podem ser gerados por várias fontes, como aplicativos, dispositivos, sensores, etc. Esses dados podem ser ingeridos em um Data Lake Storage Gen1 por uma variedade de ferramentas. Essas ferramentas geralmente capturam e processam os dados em um evento em tempo real e gravam os eventos em lotes no Data Lake Storage Gen1 para que depois eles possam ser processados.

Veja as ferramentas que você pode usar:

* [Azure Stream Analytics](../stream-analytics/stream-analytics-data-lake-output.md) – os eventos ingeridos nos Hubs de Eventos podem ser gravados no Azure Data Lake Storage Gen1 usando uma saída do Azure Data Lake Storage Gen1.
* [Azure HDInsight Storm](../hdinsight/storm/apache-storm-write-data-lake-store.md) – é possível gravar dados diretamente do cluster Storm no Data Lake Storage Gen1.
* [EventProcessorHost](../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md) – é possível receber eventos dos Hubs de Eventos e gravá-los no Data Lake Storage Gen1 usando o [SDK para .NET do Data Lake Storage Gen1](data-lake-store-get-started-net-sdk.md).

### <a name="relational-data"></a>Dados relacionais
Você também pode originar dados nos bancos de dados relacionais. Durante um período, os bancos de dados relacionais coletam grandes volumes de dados que podem fornecer informações importantes se processados por meio de um pipeline de big data. É possível usar as ferramentas a seguir para mover tais dados para o Data Lake Storage Gen1.

* [Apache Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
* [Azure Data Factory](../data-factory/copy-activity-overview.md)

### <a name="web-server-log-data-upload-using-custom-applications"></a>Dados de log do servidor Web (carregar usando aplicativos personalizados)
Esse tipo de conjunto de dados é especificamente chamado porque a análise dos dados do log do servidor Web é um caso de uso comum para aplicativos de Big Data e requer que grandes volumes de arquivos de log sejam carregados no Data Lake Storage Gen1. Você pode usar qualquer uma das ferramentas a seguir para escrever seus próprios scripts ou aplicativos para carregar esses dados.

* [CLI do Azure](data-lake-store-get-started-cli-2.0.md)
* [PowerShell do Azure](data-lake-store-get-started-powershell.md)
* [SDK do .NET do Azure Data Lake Storage Gen1](data-lake-store-get-started-net-sdk.md)
* [Azure Data Factory](../data-factory/copy-activity-overview.md)

Para carregar dados de log do servidor Web, e também para carregar outros tipos de dados (por exemplo, dados de sentimentos sociais), é uma boa abordagem escrever seus próprios scripts/aplicativos personalizados, pois eles proporcionam a flexibilidade para incluir seus dados carregando o componente como parte do aplicativo maior de big data. Em alguns casos, esse código pode assumir a forma de um script ou um utilitário simples de linha de comando. Em outros casos, o código pode ser usado para integrar o processamento de big data em um aplicativo ou uma solução de negócios.

### <a name="data-associated-with-azure-hdinsight-clusters"></a>Dados associados aos clusters Azure HDInsight
A maioria dos tipos de cluster HDInsight (Hadoop, HBase, Storm) é compatível com o Data Lake Storage Gen1 como um repositório de armazenamento de dados. Os clusters HDInsight acessam dados dos WASB (Blobs de Armazenamento do Azure). Para obter melhor desempenho, você pode copiar os dados do WASB em uma conta do Data Lake Storage Gen1 associada ao cluster. Você pode usar as ferramentas a seguir para copiar os dados.

* [Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)
* [Serviço AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
* [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md)

### <a name="data-stored-in-on-premises-or-iaas-hadoop-clusters"></a>Dados armazenados localmente ou em clusters Hadoop da IaaS
É possível armazenar grandes quantidades de dados em clusters de Hadoop existentes, localmente em computadores que usam o HDFS. Os clusters Hadoop podem estar em uma implantação local ou em um cluster da IaaS no Azure. Pode haver requisitos para copiar esses dados no Azure Data Lake Storage Gen1 para uma abordagem única ou de forma recorrente. Há várias opções que podem ser usadas para conseguir isso. Veja abaixo uma lista de alternativas e as compensações associadas.

| Abordagem | Detalhes | Vantagens | Considerações |
| --- | --- | --- | --- |
| Usar o ADF (Azure Data Factory) para copiar dados diretamente de clusters Hadoop para o Azure Data Lake Storage Gen1 |[O ADF oferece suporte ao HDFS como uma fonte de dados](../data-factory/connector-hdfs.md) |O ADF fornece suporte nativo para HDFS e gerenciamento e monitoramento de primeira classe completo |Ele exige que um gateway de gerenciamento de dados seja implantado localmente ou em um cluster da IaaS |
| Exporte os dados do Hadoop como arquivos. Em seguida, copie os arquivos no Azure Data Lake Storage Gen1 usando o mecanismo apropriado. |Você pode copiar os arquivos no Azure Data Lake Storage Gen1 usando: <ul><li>[Azure PowerShell para o sistema operacional Windows](data-lake-store-get-started-powershell.md)</li><li>[CLI do Azure](data-lake-store-get-started-cli-2.0.md)</li><li>Aplicativo personalizado usando qualquer SDK do Data Lake Storage Gen1</li></ul> |Início rápido. Pode realizar carregamentos personalizados |Processo de várias etapas que envolve várias tecnologias. O monitoramento e o gerenciamento aumentarão a serão um desafio ao longo do tempo devido à natureza personalizada das ferramentas |
| Use Distcp para copiar dados do Hadoop para o Armazenamento do Azure. Em seguida, copie os dados no Armazenamento do Azure para o Data Lake Storage Gen1 usando o mecanismo apropriado. |Você pode copiar dados do Armazenamento do Azure para o Data Lake Storage Gen1 usando: <ul><li>[Azure Data Factory](../data-factory/copy-activity-overview.md)</li><li>[ferramenta AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)</li><li>[Apache DistCp em execução em clusters do HDInsight](data-lake-store-copy-data-wasb-distcp.md)</li></ul> |Você pode usar ferramentas de software livre. |Processo de várias etapas que envolve várias tecnologias |

### <a name="really-large-datasets"></a>Conjuntos de dados realmente grandes
Carregar conjuntos de dados que incluem vários terabytes usando os métodos descritos acima, às vezes, pode ser uma tarefa lenta e onerosa. Nesses casos, você pode usar as opções a seguir.

* **Usando o ExpressRoute do Azure**. O Azure ExpressRoute permite criar conexões privadas entre os datacenters do Azure e a infraestrutura presente em seu local. Isso proporciona uma opção confiável para transferir grandes quantidades de dados. Para obter mais informações, confira a [documentação do ExpressRoute do Azure](../expressroute/expressroute-introduction.md).
* **Carregamento de dados "offline"**. Se o uso do ExpressRoute do Azure não for possível por qualquer motivo, você poderá usar o [serviço de Importação/Exportação do Azure](../storage/common/storage-import-export-service.md) para enviar unidades de disco rígido com seus dados para um datacenter do Azure. Seus dados são carregados pela primeira vez nos Blobs de Armazenamento do Azure. Em seguida, é possível usar o [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) ou a [ferramenta AdlCopy](data-lake-store-copy-data-azure-storage-blob.md) para copiar dados dos Blobs de Armazenamento do Azure para o Data Lake Storage Gen1.

  > [!NOTE]
  > Ao usar o serviço Importação/Exportação, os tamanhos dos arquivos nos discos que você envia ao datacenter do Azure não devem ultrapassar 195 GB.
  >
  >

## <a name="process-data-stored-in-data-lake-storage-gen1"></a>Processar dados armazenados no Data Lake Storage Gen1
Depois que os dados estiverem disponíveis no Data Lake Storage Gen1, você poderá executar uma análise nesses dados usando os aplicativos de Big Data compatíveis. Atualmente, é possível usar o Azure HDInsight e o Azure Data Lake Analytics para executar trabalhos de análise de dados nos dados armazenados no Data Lake Storage Gen1.

![Analisar dados no Data Lake Storage Gen1](./media/data-lake-store-data-scenarios/analyze-data.png "Analisar dados no Data Lake Storage Gen1")

Você pode analisar os exemplos a seguir.

* [Criar um cluster HDInsight com o Data Lake Storage Gen1 como armazenamento](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Usar o Azure Data Lake Analytics com o Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)

## <a name="download-data-from-data-lake-storage-gen1"></a>Baixar dados do Data Lake Storage Gen1
Você também pode querer baixar ou mover dados do Azure Data Lake Storage Gen1 para cenários como:

* Mover dados para outros repositórios para fazer interface com os pipelines de processamento de dados existentes. Por exemplo, você pode querer mover os dados do Data Lake Storage Gen1 para o Banco de Dados SQL do Azure ou SQL Server local.
* Baixar dados no computador local para processamento em ambientes IDE durante a criação de protótipos de aplicativo.

![Gerar dados no Data Lake Storage Gen1](./media/data-lake-store-data-scenarios/egress-data.png "gerar dados no Data Lake Storage Gen1")

Nesses casos, você pode usar qualquer uma das opções a seguir:

* [Apache Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
* [Azure Data Factory](../data-factory/copy-activity-overview.md)
* [Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)

Você também pode usar os métodos a seguir para escrever seu próprio script/aplicativo e baixar dados do Data Lake Storage Gen1.

* [CLI do Azure](data-lake-store-get-started-cli-2.0.md)
* [PowerShell do Azure](data-lake-store-get-started-powershell.md)
* [SDK do .NET do Azure Data Lake Storage Gen1](data-lake-store-get-started-net-sdk.md)

## <a name="visualize-data-in-data-lake-storage-gen1"></a>Visualizar dados no Data Lake Storage Gen1
Você pode usar uma combinação de serviços para criar representações visuais de dados armazenados no Data Lake Storage Gen1.

![Visualizar dados no Data Lake Storage Gen1](./media/data-lake-store-data-scenarios/visualize-data.png "visualizar dados no Data Lake Storage Gen1")

* É possível começar usando o [Azure Data Factory para mover dados do Data Lake Storage Gen1 para um SQL Data Warehouse do Azure](../data-factory/copy-activity-overview.md)
* Depois disso, você pode [integrar o Power BI ao SQL Data Warehouse do Azure](../sql-data-warehouse/sql-data-warehouse-get-started-visualize-with-power-bi.md) para criar a representação visual dos dados.
