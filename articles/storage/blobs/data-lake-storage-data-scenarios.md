---
title: Cenários de dados envolvendo o Azure Data Lake Storage Gen2 | Microsoft Docs
description: Entenda os diferentes cenários e as ferramentas usando quais dados podem ser ingeridos, processados, baixados e visualizados em um Azure Data Lake Storage Gen2 (anteriormente conhecido como Azure Data Lake Store)
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 1d22a992ac4ae69c8541b6efbaee58340f48caa4
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100517868"
---
# <a name="using-azure-data-lake-storage-gen2-for-big-data-requirements"></a>Usando o Azure Data Lake Storage Gen2 para exigências de big data

Há quatro estágios principais no processamento de big data:

> [!div class="checklist"]
> * Ingestão de grandes quantidades de dados em um repositório de dados, em tempo real ou em lotes
> * Processamento dos dados
> * Download dos dados
> * Visualização dos dados

Este artigo destaca as opções e ferramentas para cada fase de processamento.

Para obter uma lista completa dos serviços do Azure que você pode usar com Azure Data Lake Storage Gen2, consulte [integrar o Azure data Lake Storage com os serviços do Azure](./data-lake-storage-supported-azure-services.md)

## <a name="ingest-the-data-into-data-lake-storage-gen2"></a>Ingerir os dados em Data Lake Storage Gen2

Esta seção destaca as diferentes fontes de dados e as diferentes maneiras que os dados podem ser ingeridos em uma conta do Data Lake Storage Gen2.

![Ingerir dados no Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/ingest-data.png "Ingerir dados no Data Lake Storage Gen2")

### <a name="ad-hoc-data"></a>Dados ad hoc

Representam conjuntos de dados menores que são usados para criar protótipos de um aplicativo de big data. Há diferentes maneiras de ingerir dados ad hoc, dependendo da fonte dos dados. 

Aqui está uma lista de ferramentas que você pode usar para a ingestão de dados ad hoc.

| Fonte de dados | Ingeri-la usando |
| --- | --- |
| Computador local |[PowerShell do Azure](data-lake-storage-directory-file-acl-powershell.md)<br><br>[CLI do Azure](data-lake-storage-directory-file-acl-cli.md)<br><br>[Gerenciador de Armazenamento](https://azure.microsoft.com/features/storage-explorer/)<br><br>[Ferramenta AzCopy](../common/storage-use-azcopy-v10.md)|
| Blob de Armazenamento do Azure |[Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md)<br><br>[Ferramenta AzCopy](../common/storage-use-azcopy-v10.md)<br><br>[DistCp em execução no cluster HDInsight](data-lake-storage-use-distcp.md)|

### <a name="streamed-data"></a>Dados transmitidos

Isso representa os dados que podem ser gerados por várias fontes, como aplicativos, dispositivos, sensores, etc. Esses dados podem ser ingeridos em Data Lake Storage Gen2 por uma variedade de ferramentas. Essas ferramentas geralmente capturam e processam os dados em um evento em tempo real e gravam os eventos em lotes no Data Lake Storage Gen2 para que depois eles possam ser processados.

Aqui está uma lista de ferramentas que você pode usar para a ingestão de dados transmitidos.

|Ferramenta | Diretrizes |
|---|--|
|Stream Analytics do Azure|[Início Rápido: Criar um trabalho do Stream Analytics usando o portal do Azure](../../stream-analytics/stream-analytics-quick-create-portal.md) <br> [Saída para Azure Data Lake Gen2](../../stream-analytics/stream-analytics-define-outputs.md)|
|Azure HDInsight Storm | [Gravar no HDFS do Apache Hadoop usando o Apache Storm no HDInsight](../../hdinsight/storm/apache-storm-write-data-lake-store.md) |

### <a name="relational-data"></a>Dados relacionais

Você também pode originar dados nos bancos de dados relacionais. Durante um período, os bancos de dados relacionais coletam grandes volumes de dados que podem fornecer informações importantes se processados por meio de um pipeline de big data. É possível usar as ferramentas a seguir para mover tais dados para o Data Lake Storage Gen2.

Aqui está uma lista de ferramentas que você pode usar para a ingestão de dados relacionais.

|Ferramenta | Diretrizes |
|---|--|
|Azure Data Factory | [Atividade Copiar no Azure Data Factory](../../data-factory/copy-activity-overview.md) |

### <a name="web-server-log-data-upload-using-custom-applications"></a>Dados de log do servidor Web (carregar usando aplicativos personalizados)

Esse tipo de conjunto de dados é especificamente chamado porque a análise dos dados do log do servidor Web é um caso de uso comum para aplicativos de Big Data e requer que grandes volumes de arquivos de log sejam carregados no Azure Data Lake Storage Gen2. Você pode usar qualquer uma das ferramentas a seguir para escrever seus próprios scripts ou aplicativos para carregar esses dados.

Aqui está uma lista de ferramentas que você pode usar para a ingestão de dados de log de servidor Web.

|Ferramenta | Diretrizes |
|---|--|
|Azure Data Factory | [Atividade Copiar no Azure Data Factory](../../data-factory/copy-activity-overview.md)  |
|CLI do Azure|[CLI do Azure](data-lake-storage-directory-file-acl-cli.md)|
|Azure PowerShell|[Azure PowerShell](data-lake-storage-directory-file-acl-powershell.md)|

Para carregar dados de log do servidor Web, e também para carregar outros tipos de dados (por exemplo, dados de sentimentos sociais), é uma boa abordagem escrever seus próprios scripts/aplicativos personalizados, pois eles proporcionam a flexibilidade para incluir seus dados carregando o componente como parte do aplicativo maior de big data. Em alguns casos, esse código pode assumir a forma de um script ou um utilitário simples de linha de comando. Em outros casos, o código pode ser usado para integrar o processamento de big data em um aplicativo ou uma solução de negócios.

### <a name="data-associated-with-azure-hdinsight-clusters"></a>Dados associados aos clusters Azure HDInsight

A maioria dos tipos de cluster HDInsight (Hadoop, HBase, Storm) é compatível com o Data Lake Storage Gen2 como um repositório de armazenamento de dados. Os clusters HDInsight acessam dados dos WASB (Blobs de Armazenamento do Azure). Para obter melhor desempenho, você pode copiar os dados do WASB em uma conta do Azure Data Lake Storage Gen2 associada ao cluster. Você pode usar as ferramentas a seguir para copiar os dados.

Aqui está uma lista de ferramentas que você pode usar para a ingestão de dados associados a clusters do HDInsight.

|Ferramenta | Diretrizes |
|---|--|
|Apache DistCp | [Use o DistCp para copiar dados entre os Azure Storage Blobs e o Azure Data Lake Storage Gen2](./data-lake-storage-use-distcp.md) |
|Ferramenta AzCopy | [Transferir dados com AzCopy](../common/storage-use-azcopy-v10.md) |
|Azure Data Factory | [Copiar dados de ou para Azure Data Lake Storage Gen2 usando Azure Data Factory](../../data-factory/load-azure-data-lake-storage-gen2.md) |

### <a name="data-stored-in-on-premises-or-iaas-hadoop-clusters"></a>Dados armazenados localmente ou em clusters Hadoop da IaaS

É possível armazenar grandes quantidades de dados em clusters de Hadoop existentes, localmente em computadores que usam o HDFS. Os clusters Hadoop podem estar em uma implantação local ou em um cluster da IaaS no Azure. Pode haver requisitos para copiar esses dados no Azure Data Lake Storage Gen2 para uma abordagem única ou de forma recorrente. Há várias opções que podem ser usadas para conseguir isso. Veja abaixo uma lista de alternativas e as compensações associadas.

| Abordagem | Detalhes | Vantagens | Considerações |
| --- | --- | --- | --- |
| Usar o ADF (Azure Data Factory) para copiar dados diretamente de clusters Hadoop para o Azure Data Lake Storage Gen2 |[O ADF oferece suporte ao HDFS como uma fonte de dados](../../data-factory/connector-hdfs.md) |O ADF fornece suporte nativo para HDFS e gerenciamento e monitoramento de primeira classe completo |Ele exige que um gateway de gerenciamento de dados seja implantado localmente ou em um cluster da IaaS |
| Use Distcp para copiar dados do Hadoop para o Armazenamento do Azure. Em seguida, copie os dados no Armazenamento do Microsoft Azure para o Data Lake Storage Gen2 usando o mecanismo apropriado. |Você pode copiar dados do Armazenamento do Microsoft Azure para o Data Lake Storage Gen2 usando: <ul><li>[Azure Data Factory](../../data-factory/copy-activity-overview.md)</li><li>[Ferramenta AzCopy](../common/storage-use-azcopy-v10.md)</li><li>[Apache DistCp em execução em clusters do HDInsight](data-lake-storage-use-distcp.md)</li></ul> |Você pode usar ferramentas de software livre. |Processo de várias etapas que envolve várias tecnologias |

### <a name="really-large-datasets"></a>Conjuntos de dados realmente grandes

Carregar conjuntos de dados que incluem vários terabytes usando os métodos descritos acima, às vezes, pode ser uma tarefa lenta e onerosa. Nesses casos, você pode usar o Azure ExpressRoute.  

O Azure ExpressRoute permite criar conexões privadas entre os datacenters do Azure e a infraestrutura local presente. Isso proporciona uma opção confiável para transferir grandes quantidades de dados. Para saber mais, confira [documentação do Azure ExpressRoute](../../expressroute/expressroute-introduction.md).

## <a name="process-the-data"></a>Processar os dados

Depois que os dados estiverem disponíveis no Azure Data Lake Storage Gen2, você poderá executar uma análise nesses dados usando os aplicativos de Big Data compatíveis. 

![Analisar dados no Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/analyze-data.png "Analisar dados no Data Lake Storage Gen2")

Aqui está uma lista de ferramentas que você pode usar para executar trabalhos de análise de dados em dados armazenados no Data Lake Storage Gen2.

|Ferramenta | Diretrizes |
|---|--|
|Azure HDInsight | [Usar Gen2 de armazenamento do Azure Data Lake com clusters de HDInsight do Azure](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md) |
|Azure Databricks | [Azure Data Lake Storage Gen2](/azure/databricks/data/data-sources/azure/azure-datalake-gen2)<br><br>[Início rápido: analisar dados em Azure Data Lake Storage Gen2 usando Azure Databricks](./data-lake-storage-quickstart-create-databricks-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)<br><br>[Tutorial: Extrair, transformar e carregar dados usando o Azure Databricks](/azure/databricks/scenarios/databricks-extract-load-sql-data-warehouse?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|

## <a name="visualize-the-data"></a>Visualizar os dados

Use o conector de Power BI para criar representações visuais de dados armazenados no Data Lake Storage Gen2. Consulte [analisar dados em Azure data Lake Storage Gen2 usando Power bi](/power-query/connectors/datalakestorage).

## <a name="download-the-data"></a>Baixar os dados

Você também pode querer baixar ou mover dados do Azure Data Lake Storage Gen2 para cenários como:

* Mover dados para outros repositórios para fazer interface com os pipelines de processamento de dados existentes. Por exemplo, talvez você queira mover dados de Data Lake Storage Gen2 para o banco de dados SQL do Azure ou uma instância de SQL Server.

* Baixar dados no computador local para processamento em ambientes IDE durante a criação de protótipos de aplicativo.

![Dados de saída de Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/egress-data.png "Dados de saída de Data Lake Storage Gen2")

Aqui está uma lista de ferramentas que você pode usar para baixar dados do Data Lake Storage Gen2.

|Ferramenta | Diretrizes |
|---|--|
|Azure Data Factory | [Atividade Copiar no Azure Data Factory](../../data-factory/copy-activity-overview.md) |
|Apache DistCp | [Use o DistCp para copiar dados entre os Azure Storage Blobs e o Azure Data Lake Storage Gen2](./data-lake-storage-use-distcp.md) |
|Gerenciador de Armazenamento do Azure|[Use o Gerenciador de Armazenamento do Azure para gerenciar diretórios, arquivos e ACLs no Azure Data Lake Storage Gen2](data-lake-storage-explorer.md)|
|Ferramenta AzCopy|[Transferir dados com o armazenamento de BLOBs e AzCopy](../common/storage-use-azcopy-v10.md#transfer-data)|
