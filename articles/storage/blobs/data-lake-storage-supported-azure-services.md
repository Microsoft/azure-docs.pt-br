---
title: Serviços do Azure que dão suporte a Azure Data Lake Storage Gen2 | Microsoft Docs
description: Saiba mais sobre quais serviços do Azure se integram com o Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 08/05/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 0dd011d6c1ff631d537dbcadf9a77bbe6d89aa45
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100518792"
---
# <a name="azure-services-that-support-azure-data-lake-storage-gen2"></a>Serviços do Azure compatíveis com o Azure Data Lake Storage Gen2

Você pode usar os serviços do Azure para ingerir dados, executar análises e criar representações visuais. Este artigo fornece uma lista de serviços do Azure com suporte, divulga seu nível de suporte e fornece links para artigos que ajudam você a usar esses serviços com Azure Data Lake Storage Gen2.

## <a name="supported-azure-services"></a>Serviços do Azure compatíveis

Esta tabela lista os serviços do Azure que você pode usar com Azure Data Lake Storage Gen2. Os itens destas tabelas serão alterados ao longo do tempo, pois o suporte continuará a ser expandido.

> [!NOTE]
> O nível de suporte refere-se apenas a como o serviço tem suporte com o Data Lake Storage Gen 2.

|Serviço do Azure |Nível de suporte |Azure AD |Chave compartilhada| Artigos relacionados |
|---------------|-------------------|---|---|---|
|Azure Data Factory|Disponível para o público geral|Sim|Sim|[Carregar dados no Azure Data Lake Storage Gen2 com o Azure Data Factory](../../data-factory/load-azure-data-lake-storage-gen2.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Azure Databricks|Disponível para o público geral|Sim|Sim|[Usar com o Azure Databricks](/azure/databricks/data/data-sources/azure/azure-datalake-gen2) <br> [Início rápido: analisar dados em Azure Data Lake Storage Gen2 usando Azure Databricks](data-lake-storage-quickstart-create-databricks-account.md) <br>[Tutorial: Extrair, transformar e carregar dados usando o Azure Databricks](/azure/databricks/scenarios/databricks-extract-load-sql-data-warehouse) <br>[Tutorial: acessar dados de Data Lake Storage Gen2 com Azure Databricks usando o Spark](data-lake-storage-use-databricks-spark.md)|
|Hub de Eventos do Azure|Disponível para o público geral|Não|Sim|[Capture eventos por meio dos Hubs de Eventos do Azure no Armazenamento de Blobs do Azure ou no Azure Data Lake Storage](../../event-hubs/event-hubs-capture-overview.md)|
|Grade de Eventos do Azure|Disponível para o público geral|Sim|Sim|[Tutorial: Implementar o padrão de captura do data lake para atualizar uma tabela do Databricks Delta](data-lake-storage-events.md)|
|Aplicativos Lógicos do Azure|Disponível para o público geral|Não|Sim|[Visão geral – O que são Aplicativos Lógicos do Azure?](../../logic-apps/logic-apps-overview.md)|
|Azure Machine Learning|Disponível para o público geral|Sim|Sim|[Acessar dados nos serviços de armazenamento do Azure](../../machine-learning/how-to-access-data.md)|
|Stream Analytics do Azure|Disponível para o público geral|Sim|Sim|[Início Rápido: Criar um trabalho do Stream Analytics usando o portal do Azure](../../stream-analytics/stream-analytics-quick-create-portal.md) <br> [Saída para Azure Data Lake Gen2](../../stream-analytics/stream-analytics-define-outputs.md)|
|Data Box|Disponível para o público geral|Não|Sim|[Usar Azure Data Box para migrar dados de um repositório HDFS local para o armazenamento do Azure](data-lake-storage-migrate-on-premises-hdfs-cluster.md)|
|HDInsight |Disponível para o público geral|Sim|Sim|[Usar Gen2 de armazenamento do Azure Data Lake com clusters de HDInsight do Azure](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)<br>[Usando a CLI do HDFS com o Data Lake Storage Gen2](data-lake-storage-use-hdfs-data-lake-storage.md) <br>[Tutorial: extrair, transformar e carregar dados usando Apache Hive no Azure HDInsight](data-lake-storage-tutorial-extract-transform-load-hive.md)|
|Hub IoT |Disponível para o público geral|Sim|Sim|[Usar o roteamento de mensagens do Hub IoT para enviar mensagens de dispositivo para nuvem para diferentes pontos de extremidade](../../iot-hub/iot-hub-devguide-messages-d2c.md)|
|Power BI|Disponível para o público geral|Sim|Sim|[Analisar dados em Data Lake Storage Gen2 usando Power BI](/power-query/connectors/datalakestorage)|
|Azure Synapse Analytics (antigo SQL Data Warehouse)|Disponível para o público geral|Sim|Sim|[Analisar dados em uma conta de armazenamento](../../synapse-analytics/get-started-analyze-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|O SQL Server Integration Services (SSIS)|Disponível para o público geral|Sim|Sim|[Gerenciador de conexões do armazenamento do Azure](/sql/integration-services/connection-manager/azure-storage-connection-manager)|
|Azure Data Explorer|Disponível para o público geral|Sim|Sim|[Consultar dados no Azure Data Lake usando o Azure Data Explorer](/azure/data-explorer/data-lake-query-data)|
|Pesquisa Cognitiva do Azure|Visualização|Sim|Sim|[Indexar e Pesquisar Azure Data Lake Storage Gen2 documentos (visualização)](../../search/search-howto-index-azure-data-lake-storage.md)|
|Rede de Distribuição de Conteúdo do Azure|Ainda não compatível|Não aplicável|Não aplicável|[Indexar e Pesquisar Azure Data Lake Storage Gen2 documentos (visualização)](../../cdn/cdn-overview.md)|
|Banco de Dados SQL do Azure|Ainda não compatível|Não aplicável|Não aplicável|[O que é o Banco de Dados SQL do Azure?](../../azure-sql/database/sql-database-paas-overview.md)|

## <a name="see-also"></a>Confira também

- [Problemas conhecidos com o Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)
- [Recursos de armazenamento de blobs no Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md)
- [Plataformas de software livre compatíveis com o Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md)
- [Acesso de vários protocolos no Azure Data Lake Storage](data-lake-storage-multi-protocol-access.md)