---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: eabfca64a4bdf1f72375575161111ddaff55eba3
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172387"
---
O Azure Data Factory dá suporte às seguintes atividades de transformação, que podem ser adicionadas aos pipelines individualmente ou encadeadas a outra atividade.

| Atividades de transformação de dados | Ambiente de computação |
|:--- |:--- |
| [Hive](../articles/data-factory/v1/data-factory-hive-activity.md) |HDInsight [Hadoop] |
| [Pig](../articles/data-factory/v1/data-factory-pig-activity.md) |HDInsight [Hadoop] |
| [MapReduce](../articles/data-factory/v1/data-factory-map-reduce.md) |HDInsight [Hadoop] |
| [Streaming do Hadoop](../articles/data-factory/v1/data-factory-hadoop-streaming-activity.md) |HDInsight [Hadoop] |
| [Spark](../articles/data-factory/v1/data-factory-spark.md) | HDInsight [Hadoop] |
| [Atividades de Machine Learning: Execução de lote e Recurso de atualização](../articles/data-factory/v1/data-factory-azure-ml-batch-execution-activity.md) |VM do Azure |
| [Procedimento armazenado](../articles/data-factory/v1/data-factory-stored-proc-activity.md) |SQL Azure, Azure SQL Data Warehouse ou SQL Server |
| [U-SQL da Análise Data Lake](../articles/data-factory/v1/data-factory-usql-activity.md) |Análise Azure Data Lake |
| [DotNet](../articles/data-factory/v1/data-factory-use-custom-activities.md) |HDInsight [Hadoop] ou Lote do Azure |

> [!NOTE]
> Você pode usar a atividade MapReduce para executar programas Spark no cluster HDInsight Spark. Veja [Invocar programas Spark pelo Azure Data Factory](../articles/data-factory/v1/data-factory-spark.md) para obter detalhes.
> Você pode criar uma atividade personalizada para executar scripts R em seu cluster HDInsight com R instalado. Veja [Executar scripts R usando o Azure Data Factory](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample).
> 
> 

