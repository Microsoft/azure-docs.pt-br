---
title: "Integrando o Data Lake Store com outros Serviços do Azure | Microsoft Docs"
description: "Noções básicas sobre como o Repositório Data Lake é integrado com outros serviços do Azure"
documentationcenter: 
services: data-lake-store
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 48a5d1f4-3850-4c22-bbc4-6d1d394fba8a
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/28/2017
ms.author: nitinme
ms.openlocfilehash: de7aff6b31d937576da65498c5fcce2ae9abdbf1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="integrating-data-lake-store-with-other-azure-services"></a>Integrando o Repositório Data Lake com outros Serviços do Azure
O Repositório Azure Data Lake pode ser usado em conjunto com outros serviços do Azure para habilitar uma maior variedade de cenários. O artigo a seguir lista os serviços com os quais o Repositório Data Lake pode ser integrado.

## <a name="use-data-lake-store-with-azure-hdinsight"></a>Usar o Repositório Data Lake com o Azure HDInsight
É possível provisionar um cluster [Azure HDInsight](https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/) que usa o Repositório do Data Lake como o armazenamento compatível com HDFS. Para esta versão, para clusters Hadoop e Storm no Windows e Linux, é possível usar o Repositório Data Lake somente como um armazenamento adicional. Esses clusters ainda usam o Armazenamento do Azure (WASB) como o armazenamento padrão. No entanto, para clusters HBase no Windows e Linux, você pode usar o Repositório Data Lake como o armazenamento padrão ou adicional, ou ambos.

Para obter instruções sobre como provisionar um cluster HDInsight com o Repositório Data Lake, veja:

* [Provisionar um cluster HDInsight com o Repositório do Data Lake usando o Portal do Azure](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Provisionar um cluster HDInsight com Data Lake Store como o armazenamento padrão usando o Azure PowerShell](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
* [Provisionar um cluster HDInsight com Data Lake Store como o armazenamento adicional usando o Azure PowerShell](data-lake-store-hdinsight-hadoop-use-powershell.md)

## <a name="use-data-lake-store-with-azure-data-lake-analytics"></a>Usar o Repositório Data Lake com a Análise Azure Data Lake
[Análise Azure Data Lake](../data-lake-analytics/data-lake-analytics-overview.md) permite que você trabalhe com Big Data em escala na nuvem. Ela provisiona recursos de maneira dinâmica e permite fazer a análise de terabytes ou até mesmo de exabytes de dados que podem ser armazenados em várias fontes de dados com suporte, sendo uma delas o Repositório Data Lake. A Análise Data Lake é especialmente otimizada para funcionar com o Repositório Azure Data Lake - fornecendo o mais alto nível de desempenho, taxa de transferência e paralelização para suas cargas de trabalho de Big Data.

Para obter instruções sobre como usar a Análise Data Lake com o Repositório Data Lake, veja [Introdução à Análise Data Lake usando o Repositório Data Lake](../data-lake-analytics/data-lake-analytics-get-started-portal.md).

## <a name="use-data-lake-store-with-azure-data-factory"></a>Usar o Repositório Data Lake com o Azure Data Factory
É possível usar o [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) para receber dados de tabelas do Azure, Banco de Dados SQL do Azure, SQL Data Warehouse do Azure, Blobs de Armazenamento do Azure e bancos de dados locais. Como cidadão de primeira classe no ecossistema do Azure, o Azure Data Factory pode ser usado para orquestrar a ingestão de dados dessas fontes no Repositório Azure Data Lake.

Para obter instruções sobre como usar o Azure Data Factory com o Repositório Data Lake , veja [Mover dados para e do Repositório Data Lake usando o Data Factory](../data-factory/connector-azure-data-lake-store.md).

## <a name="copy-data-from-azure-storage-blobs-into-data-lake-store"></a>Copiar dados de Blobs do Armazenamento do Azure para o Repositório Data Lake
O Repositório Data Lake do Azure fornece uma ferramenta de linha de comando, AdlCopy, que permite copiar dados do Armazenamento de Blobs do Azure para o Repositório Data Lake. Para saber mais, confira [Copiar dados de Blobs do Armazenamento do Azure para o Repositório Data Lake](data-lake-store-copy-data-azure-storage-blob.md).

## <a name="copy-data-between-azure-sql-database-and-data-lake-store"></a>Copiar dados entre o Banco de Dados SQL do Azure e o Repositório Data Lake
Você pode usar o Apache Sqoop para importar e exportar dados entre o Banco de Dados SQL do Azure e o Repositório Data Lake. Para saber mais, confira [Copiar dados entre o Repositório Data Lake e o Banco de Dados SQL do Azure usando o Sqoop](data-lake-store-data-transfer-sql-sqoop.md).

## <a name="use-data-lake-store-with-stream-analytics"></a>Usar o Repositório Data Lake com o Stream Analytics
Você pode usar o Repositório Data Lake como uma das saídas para armazenar dados transmitidos usando o Stream Analytics do Azure. Para saber mais, confira [Transmitir dados do Blob de Armazenamento do Azure para o Repositório Data Lake usando o Stream Analytics do Azure](data-lake-store-stream-analytics.md).

## <a name="use-data-lake-store-with-power-bi"></a>Usar o Repositório Data Lake com o Power BI
Você pode usar o Power BI para importar dados de uma conta do Repositório Data Lake para analisar e visualizar os dados. Para saber mais, confira [Analisar dados no Repositório Data Lake usando o Power BI](data-lake-store-power-bi.md).

## <a name="use-data-lake-store-with-data-catalog"></a>Usar o Repositório Data Lake com o Catálogo de Dados
Você pode registrar dados do Repositório Data Lake no Catálogo de Dados do Azure para tornar os dados detectáveis em toda a organização. Para saber mais, confira [Registrar dados do Repositório Data Lake no Catálogo de Dados do Azure](data-lake-store-with-data-catalog.md).

## <a name="use-data-lake-store-with-sql-server-integration-services-ssis"></a>Usar o Data Lake Store com o SSIS (SQL Server Integration Services)
Você pode usar o gerenciador de conexão do Azure Data Lake Store no SSIS para conectar um pacote do SSIS com o Azure Data Lake Store. Para obter mais informações, consulte [Usar o Data Lake Store com o SSIS](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager).

## <a name="use-data-lake-store-with-sql-data-warehouse"></a>Usar o Data Lake Store com o SQL Data Warehouse
Você pode usar o PolyBase para carregar dados do Azure Data Lake Store para o SQL Data Warehouse. Para obter mais informações, consulte [Usar o Data Lake Store com o SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store.md).

## <a name="use-data-lake-store-with-azure-event-hubs"></a>Usar o Data Lake Store com os Hubs de Eventos do Azure
Você pode usar o Azure Data Lake Store para arquivar e capturar dados recebidos pelos Hubs de Eventos do Azure. Para saber mais, confira [Usar o Data Lake Store com os Hubs de Eventos do Azure](data-lake-store-archive-eventhub-capture.md).

## <a name="see-also"></a>Consulte também
* [Visão geral do Repositório Azure Data Lake](data-lake-store-overview.md)
* [Introdução ao Repositório Data Lake usando o Portal](data-lake-store-get-started-portal.md)
* [Introdução ao Repositório Data Lake usando o PowerShell](data-lake-store-get-started-powershell.md)  

