---
title: Integrar Data Lake Storage Gen1 com outros serviço do Azure
description: Entenda como você pode integrar Azure Data Lake Storage Gen1 com outros serviços do Azure.
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 929853f4dbedca7034c8e2a51e6231651a2dd08f
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96461653"
---
# <a name="integrating-azure-data-lake-storage-gen1-with-other-azure-services"></a>Integrando o Azure Data Lake Storage Gen1 com outros serviços do Azure
O Azure Data Lake Storage Gen1 pode ser usado em conjunto com outros serviços do Azure para permitir uma gama mais ampla de cenários. O artigo a seguir lista os serviços com os quais o Data Lake Storage Gen1 pode ser integrado.

## <a name="use-data-lake-storage-gen1-with-azure-hdinsight"></a>Use o Data Lake Storage Gen1 com o Azure HDInsight
Você pode provisionar um cluster do [Azure HDInsight](https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/) que usa o Data Lake Storage Gen1 como o armazenamento compatível com HDFS. Para esta versão, para clusters de Hadoop e Storm no Windows e Linux, você pode usar Gen1 de armazenamento do Data Lake somente como um armazenamento adicional. Esses clusters ainda usam o Armazenamento do Azure (WASB) como o armazenamento padrão. No entanto, para clusters de HBase no Windows e Linux, você pode usar Gen1 de armazenamento do Data Lake como armazenamento padrão, ou armazenamento adicional ou ambos.

Para obter instruções sobre como provisionar um cluster do HDInsight com o Data Lake Storage Gen1, consulte:

* [Provisionar um cluster HDInsight com Data Lake Storage Gen1 usando portal do Azure](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Provisione um cluster do HDInsight com o Data Lake Storage Gen1 como armazenamento padrão usando o Azure PowerShell](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
* [Provisione um cluster do HDInsight com o Data Lake Storage Gen1 como armazenamento adicional usando o Azure PowerShell](data-lake-store-hdinsight-hadoop-use-powershell.md)

## <a name="use-data-lake-storage-gen1-with-azure-data-lake-analytics"></a>Use o Data Lake Storage Gen1 com o Azure Data Lake Analytics
[Análise Azure Data Lake](../data-lake-analytics/data-lake-analytics-overview.md) permite que você trabalhe com Big Data em escala na nuvem. Ele provisiona dinamicamente os recursos e permite fazer análises em terabytes ou mesmo exabytes de dados que podem ser armazenados em diversas fontes de dados suportadas, sendo uma delas o Data Lake Storage Gen1. O Data Lake Analytics é especialmente otimizado para trabalhar com o Data Lake Storage Gen1 - fornecendo o mais alto nível de desempenho, rendimento e paralelização para cargas de trabalho de big data.

Para obter instruções sobre como usar o Data Lake Analytics com o Data Lake Storage Gen1, consulte [Primeiros passos no Data Lake Analytics usando o Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md).

## <a name="use-data-lake-storage-gen1-with-azure-data-factory"></a>Use o Data Lake Storage Gen1 com o Azure Data Factory
É possível usar o [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) para receber dados de tabelas do Azure, Banco de Dados SQL do Azure, SQL Data Warehouse do Azure, Blobs de Armazenamento do Azure e bancos de dados locais. Sendo um cidadão de primeira classe no ecossistema do Azure, o Azure Data Factory pode ser usado para orquestrar a ingestão de dados dessas fontes para o Data Lake Storage Gen1.

Para obter instruções sobre como usar o Azure Data Factory com o Data Lake Storage Gen1, consulte [Mover dados de e para o Data Lake Storage Gen1 usando o Data Factory](../data-factory/connector-azure-data-lake-store.md).

## <a name="copy-data-from-azure-storage-blobs-into-data-lake-storage-gen1"></a>Copiar dados dos Blobs de Armazenamento do Azure para o Data Lake Storage Gen1
O Data Storage do Azure Data Lake fornece uma ferramenta de linha de comando, AdlCopy, que permite copiar dados do Armazenamento de Blocos do Azure para uma conta do Data Lake Storage Gen1. Para obter mais informações, consulte [Copiar dados dos Blobs de Armazenamento do Azure para o Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md).

## <a name="copy-data-between-azure-sql-database-and-data-lake-storage-gen1"></a>Copiar dados entre o Banco de Dados SQL do Azure e o Data Lake Storage Gen1
Você pode usar o Apache Sqoop para importar e exportar dados entre o Banco de Dados SQL do Azure e o Data Lake Storage Gen1. Para obter mais informações, consulte [copiar dados entre o data Lake Storage Gen1 e o Azure SQL Database usando o Sqoop](data-lake-store-data-transfer-sql-sqoop.md).

## <a name="use-data-lake-storage-gen1-with-stream-analytics"></a>Use o Data Lake Storage Gen1 com o Stream Analytics
Você pode usar o Data Lake Storage Gen1 como uma das saídas para armazenar dados transmitidos usando o Azure Stream Analytics. Para obter mais informações, consulte [Transmitir dados do Blob de armazenamento do Azure para o Data Lake Storage Gen1 usando o Azure Stream Analytics](data-lake-store-stream-analytics.md).

## <a name="use-data-lake-storage-gen1-with-power-bi"></a>Use o Data Lake Storage Gen1 com o Power BI
Você pode usar o Power BI para importar dados de uma conta do Data Lake Storage Gen1 para analisar e visualizar os dados. Para obter mais informações, consulte [Analisar dados no Data Lake Storage Gen1 usando o Power BI](data-lake-store-power-bi.md).

## <a name="use-data-lake-storage-gen1-with-data-catalog"></a>Use o Data Lake Storage Gen1 com o Catálogo de Dados
Você pode registrar dados do Data Lake Storage Gen1 no Catálogo de Dados do Azure para tornar os dados detectáveis em toda a organização. Para obter mais informações, consulte [Registrar dados do Data Lake Storage Gen1 no Catálogo de Dados do Azure](data-lake-store-with-data-catalog.md).

## <a name="use-data-lake-storage-gen1-with-sql-server-integration-services-ssis"></a>Use o Data Lake Storage Gen1 com o SSIS (SQL Server Integration Services)
Você pode usar o gerenciador de conexões do Data Lake Storage Gen1 no SSIS para conectar um pacote do SSIS com o Data Lake Storage Gen1. Para obter mais informações, consulte [Use o Data Lake Storage Gen1 com o SSIS](/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager).

## <a name="use-data-lake-storage-gen1-with-azure-synapse-analytics"></a>Usar Data Lake Storage Gen1 com o Azure Synapse Analytics
Você pode usar o polybase para carregar dados de Data Lake Storage Gen1 para o Azure Synapse Analytics. Para obter mais informações, consulte [usar data Lake Storage Gen1 com o Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store.md).

## <a name="use-data-lake-storage-gen1-with-azure-event-hubs"></a>Use o Data Lake Storage Gen1 com os Hubs de Eventos do Azure
Você pode usar o Armazenamento de Data Lake do Azure Gen1 para arquivar e capturar dados recebidos pelos Hubs de Eventos do Azure. Para obter mais informações, consulte [Usar o Data Lake Storage Gen1 com os Azure Event Hubs](data-lake-store-archive-eventhub-capture.md).

## <a name="see-also"></a>Confira também
* [Visão Geral do Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Comece a usar o Data Lake Storage Gen1 usando o Portal](data-lake-store-get-started-portal.md)
* [Introdução ao Data Lake Storage Gen1 usando o PowerShell](data-lake-store-get-started-powershell.md)