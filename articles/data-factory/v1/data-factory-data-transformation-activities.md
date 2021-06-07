---
title: 'Transformação de dados: processar & transformar dados '
description: Saiba como transformar dados ou processar dados em Azure Data Factory usando o Hadoop, Azure Machine Learning Studio (clássico) ou Azure Data Lake Analytics.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 422acbaab097182b800e3bad35b0121284db9cd8
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104782921"
---
# <a name="transform-data-in-azure-data-factory-version-1"></a>Transformar dados no Azure Data Factory versão 1
> [!div class="op_single_selector"]
> * [Hive](data-factory-hive-activity.md)  
> * [Pig](data-factory-pig-activity.md)  
> * [MapReduce](data-factory-map-reduce.md)  
> * [Streaming do Hadoop](data-factory-hadoop-streaming-activity.md)
> * [Azure Machine Learning Studio (clássico)](data-factory-azure-ml-batch-execution-activity.md) 
> * [Procedimento armazenado](data-factory-stored-proc-activity.md)
> * [U-SQL da Análise Data Lake](data-factory-usql-activity.md)
> * [Personalizado do .NET](data-factory-use-custom-activities.md)

## <a name="overview"></a>Visão geral
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço Data Factory, consulte [atividades de transformação de dados no Data Factory ](../transform-data.md).

Este artigo explica as atividades de transformação de dados no Azure Data Factory que você pode usar para transformar e processar dados brutos em previsões e ideias. Uma atividade de transformação é executada em um ambiente de cálculo, como um cluster do Azure HDInsight ou um Lote do Azure. Ela fornece links para artigos com informações detalhadas sobre cada atividade de transformação.

O Data Factory dá suporte às seguintes atividades de transformação de dados, que podem ser adicionadas aos [pipelines](data-factory-create-pipelines.md) individualmente ou encadeadas a outra atividade.

> [!NOTE]
> Para uma explicação com instruções passo a passo, confira o artigo [Criar um pipeline com a transformação do Hive](data-factory-build-your-first-pipeline.md) .  
> 
> 

## <a name="hdinsight-hive-activity"></a>Atividade de Hive do HDInsight
A atividade de Hive do HDInsight em um pipeline de Data Factory executa consultas de Hive em seu próprio cluster ou no cluster sob demanda do HDInsight baseado em Windows/Linux. Consulte o artigo [atividade do hive](data-factory-hive-activity.md) para obter detalhes sobre essa atividade. 

## <a name="hdinsight-pig-activity"></a>Atividade de Pig do HDInsight
A atividade de Pig do HDInsight em um pipeline de Data Factory executa consultas de Pig em seu próprio cluster ou no cluster sob demanda do HDInsight baseado em Windows/Linux. Consulte o artigo [atividade de Pig](data-factory-pig-activity.md) para obter detalhes sobre essa atividade. 

## <a name="hdinsight-mapreduce-activity"></a>Atividade de MapReduce do HDInsight
A atividade de MapReduce do HDInsight em um pipeline do Data Factory executa programas MapReduce no seu próprio cluster HDInsight baseado em Windows/Linux, ou em um sob demanda. Consulte o artigo [atividade do MapReduce](data-factory-map-reduce.md) para obter detalhes sobre essa atividade.

## <a name="hdinsight-streaming-activity"></a>Atividade de Streaming do HDInsight
A Atividade de Streaming do HDInsight em um pipeline do Data Factory executa programas de Transmissão do Hadoop em seu próprio ou cluster HDInsight sob demanda baseado no Windows/Linux. Confira [Atividade de HDInsight Streaming](data-factory-hadoop-streaming-activity.md) para obter detalhes sobre essa atividade.

## <a name="hdinsight-spark-activity"></a>Atividade do HDInsight Spark
A atividade do HDInsight Spark em um pipeline do Data Factory executa programas do Spark em seu próprio cluster HDInsight. Para obter detalhes, consulte [Invocar programas Spark do Azure Data Factory](data-factory-spark.md) para obter detalhes. 

## <a name="azure-machine-learning-studio-classic-activities"></a>Atividades Azure Machine Learning Studio (clássicas)
Azure Data Factory permite que você crie facilmente pipelines que usam um serviço Web publicado Azure Machine Learning Studio (clássico) para análise preditiva. Usando a [atividade de execução em lote](data-factory-azure-ml-batch-execution-activity.md#invoking-a-web-service-using-batch-execution-activity) em um pipeline Azure data Factory, você pode invocar um serviço Web Studio (clássico) para fazer previsões sobre os dados no lote.

Ao longo do tempo, os modelos de previsão nos experimentos de Pontuação do estúdio (clássico) precisam ser retreinados usando novos conjuntos de dados de entrada. Depois de concluir o retreinamento, você deseja atualizar o serviço Web de pontuação com o modelo de aprendizado de máquina retreinado. Você pode usar a [atividade atualizar recurso](data-factory-azure-ml-batch-execution-activity.md#updating-models-using-update-resource-activity) para atualizar o serviço Web com o modelo treinado recentemente.  

Confira [usar atividades Azure Machine Learning Studio (clássicas)](data-factory-azure-ml-batch-execution-activity.md) para obter detalhes sobre essas atividades do estúdio (clássico). 

## <a name="stored-procedure-activity"></a>Atividade de procedimento armazenado
Você pode usar a atividade de procedimento armazenado SQL Server em um pipeline de Data Factory para invocar um procedimento armazenado em um dos seguintes repositórios de dados: banco de dados SQL do Azure, análise de Synapse do Azure, banco de dados SQL Server em sua empresa ou uma VM do Azure. Consulte o artigo [atividade de procedimento armazenado](data-factory-stored-proc-activity.md) para obter detalhes.  

## <a name="data-lake-analytics-u-sql-activity"></a>Atividade do U-SQL do Data Lake Analytics
A atividade de U-SQL do Data Lake Analytics executa um script U-SQL em um cluster do Azure Data Lake Analytics. Consulte o artigo [atividade do U-SQL da análise de dados](data-factory-usql-activity.md) para obter detalhes. 

## <a name="net-custom-activity"></a>Atividade personalizada do .NET
Se precisar transformar dados de uma maneira que não tenha suporte do Data Factory, você poderá criar uma atividade personalizada com sua própria lógica de processamento de dados e usar a atividade no pipeline. Você pode configurar a atividade personalizada do .NET para que seja executada usando um serviço de Lote do Azure ou um cluster do Azure HDInsight. Confira o artigo [Usar atividades personalizadas](data-factory-use-custom-activities.md) para obter detalhes. 

Você pode criar uma atividade personalizada para executar scripts R em seu cluster HDInsight com R instalado. Veja [Executar scripts R usando o Azure Data Factory](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample). 

## <a name="compute-environments"></a>Ambientes de computação
Crie um serviço vinculado para o ambiente de computação e, em seguida, usar o serviço vinculado ao definir uma atividade de transformação. Há dois tipos de ambientes de computação com suporte do Data Factory. 

1. **Sob demanda**: nesse caso, o ambiente de computação é totalmente gerenciado pelo Data Factory. Ele é automaticamente criado pelo serviço Data Factory antes de um trabalho ser enviado a fim de processar os dados e é removido após a conclusão do trabalho. Você pode configurar e controlar as configurações granulares do ambiente de computação sob demanda para execução do trabalho, gerenciamento de cluster e ações de inicialização. 
2. **Traga seu próprio**: nesse caso, você pode registrar no Data Factory seu próprio ambiente de computação (por exemplo, o cluster HDInsight) como um serviço vinculado. O ambiente de computação é gerenciado por você e o serviço Data Factory o utiliza para executar as atividades. 

Veja o artigo [Serviços vinculados de computação](data-factory-compute-linked-services.md) para saber mais sobre os serviços de computação com suporte do Data Factory. 

## <a name="summary"></a>Resumo
O Azure Data Factory dá suporte às atividades de transformação de dados e ambientes de computação para as atividades a seguir. As atividades de transformação podem ser adicionadas a pipelines tanto individualmente quanto encadeadas a outra atividade.

| Atividades de transformação de dados | Ambiente de computação |
|:--- |:--- |
| [Hive](data-factory-hive-activity.md) |HDInsight [Hadoop] |
| [Pig](data-factory-pig-activity.md) |HDInsight [Hadoop] |
| [MapReduce](data-factory-map-reduce.md) |HDInsight [Hadoop] |
| [Streaming do Hadoop](data-factory-hadoop-streaming-activity.md) |HDInsight [Hadoop] |
| [Atividades do Azure Machine Learning Studio (clássico): Execução de lote e Recurso de atualização](data-factory-azure-ml-batch-execution-activity.md) |VM do Azure |
| [Procedimento armazenado](data-factory-stored-proc-activity.md) |SQL do Azure, Azure Synapse Analytics ou SQL Server |
| [U-SQL da Análise Data Lake](data-factory-usql-activity.md) |Análise Azure Data Lake |
| [DotNet](data-factory-use-custom-activities.md) |HDInsight [Hadoop] ou Lote do Azure |

