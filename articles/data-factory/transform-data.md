---
title: Transformar dados usando o Azure Data Factory | Microsoft Docs
description: Aprenda a transformar ou processar dados no Azure Data Factory usando o Hadoop, o Machine Learning ou o Azure Data Lake Analytics.
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: shlo
ms.openlocfilehash: b224f5b32c9a46f797e78644c731a9cd9341da2b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="transform-data-in-azure-data-factory"></a>Transformar dados no Azure Data Factory
> [!div class="op_single_selector"]
> * [Hive](transform-data-using-hadoop-hive.md)  
> * [Pig](transform-data-using-hadoop-pig.md)  
> * [MapReduce](transform-data-using-hadoop-map-reduce.md)  
> * [Streaming do Hadoop](transform-data-using-hadoop-streaming.md)
> * [Spark](transform-data-using-spark.md)
> * [Machine Learning](transform-data-using-machine-learning.md) 
> * [Procedimento armazenado](transform-data-using-stored-procedure.md)
> * [U-SQL da Análise Data Lake](transform-data-using-data-lake-analytics.md)
> * [Personalizado do .NET](transform-data-using-dotnet-custom-activity.md)

## <a name="overview"></a>Visão geral
Este artigo explica as atividades de transformação de dados no Azure Data Factory que você pode usar para transformar e processar dados brutos em previsões e ideias. Uma atividade de transformação é executada em um ambiente de cálculo, como um cluster do Azure HDInsight ou um Lote do Azure. Ela fornece links para artigos com informações detalhadas sobre cada atividade de transformação.

O Data Factory dá suporte às seguintes atividades de transformação de dados, que podem ser adicionadas aos [pipelines](concepts-pipelines-activities.md) individualmente ou encadeadas a outra atividade.
 

## <a name="hdinsight-hive-activity"></a>Atividade de Hive do HDInsight
A atividade de Hive do HDInsight em um pipeline de Data Factory executa consultas de Hive em seu próprio cluster ou no cluster sob demanda do HDInsight baseado em Windows/Linux. Consulte o artigo [Hive activity](transform-data-using-hadoop-hive.md) (Atividade do Hive) para obter detalhes sobre essa atividade. 

## <a name="hdinsight-pig-activity"></a>Atividade de Pig do HDInsight
A atividade de Pig do HDInsight em um pipeline de Data Factory executa consultas de Pig em seu próprio cluster ou no cluster sob demanda do HDInsight baseado em Windows/Linux. Consulte o artigo [Pig activity](transform-data-using-hadoop-pig.md) (Atividade do Pig) para obter detalhes sobre essa atividade. 

## <a name="hdinsight-mapreduce-activity"></a>Atividade de MapReduce do HDInsight
A atividade de MapReduce do HDInsight em um pipeline do Data Factory executa programas MapReduce no seu próprio cluster HDInsight baseado em Windows/Linux, ou em um sob demanda. Consulte o artigo [MapReduce activity](transform-data-using-hadoop-map-reduce.md) (Atividade do MapReduce) para obter detalhes sobre essa atividade.

## <a name="hdinsight-streaming-activity"></a>Atividade de Streaming do HDInsight
A atividade de streaming no HDInsight em um pipeline do Data Factory executa programas de streaming do Hadoop em um cluster do HDInsight baseado em Windows/Linux de sua propriedade ou sob demanda. Confira [Atividade de HDInsight Streaming](transform-data-using-hadoop-streaming.md) para obter detalhes sobre essa atividade.

## <a name="hdinsight-spark-activity"></a>Atividade do Spark no HDInsight
A atividade do HDInsight Spark em um pipeline do Data Factory executa programas do Spark em seu próprio cluster HDInsight. Para obter detalhes, consulte [Invocar programas Spark do Azure Data Factory](transform-data-using-spark.md) para obter detalhes. 

## <a name="machine-learning-activities"></a>Atividades de Machine Learning
O Azure Data Factory permite que você crie facilmente pipelines que usam o serviço Web do Azure Machine Learning publicado para a análise preditiva. Usando a [Atividade de Execução em Lotes](transform-data-using-machine-learning.md) em um pipeline do Azure Data Factory, você pode invocar um serviço Web do Machine Learning para fazer previsões sobre dados em lote.

Ao longo do tempo, os modelos de previsão nos testes de pontuação do Machine Learning precisam ser readaptados usando novos conjuntos de dados de entrada. Depois de concluir o novo treinamento, você deseja atualizar o serviço Web de pontuação com o modelo do Machine Learning readaptado. Você pode usar a [Atividade de Recurso de Atualização](update-machine-learning-models.md) para atualizar o serviço Web com o modelo recém-treinado.  

Confira [Usar atividades de Machine Learning](transform-data-using-machine-learning.md) para obter detalhes sobre essas atividades de Machine Learning. 

## <a name="stored-procedure-activity"></a>Atividade de procedimento armazenado
Você pode usar a atividade de Procedimento Armazenado do SQL Server em um pipeline de Data Factory para invocar um procedimento armazenado em um dos seguintes repositórios de dados: Banco de Dados SQL, SQL Data Warehouse, Banco de Dados SQL Server na sua empresa ou uma VM do Azure. Consulte o artigo [Stored Procedure activity](transform-data-using-stored-procedure.md) (Atividade de Procedimento Armazenado) para obter detalhes.  

## <a name="data-lake-analytics-u-sql-activity"></a>Atividade do U-SQL do Data Lake Analytics
A atividade de U-SQL do Data Lake Analytics executa um script U-SQL em um cluster do Azure Data Lake Analytics. Consulte o artigo [Data Analytics U-SQL activity](transform-data-using-data-lake-analytics.md) (atividade de U-SQL do Data Analytics) para obter detalhes. 

## <a name="net-custom-activity"></a>Atividade personalizada do .NET
Se precisar transformar dados de uma maneira que não tenha suporte do Data Factory, você poderá criar uma atividade personalizada com sua própria lógica de processamento de dados e usar a atividade no pipeline. Você pode configurar a atividade personalizada do .NET para que seja executada usando um serviço de Lote do Azure ou um cluster do Azure HDInsight. Confira o artigo [Usar atividades personalizadas](transform-data-using-dotnet-custom-activity.md) para obter detalhes. 

Você pode criar uma atividade personalizada para executar scripts R em seu cluster HDInsight com R instalado. Veja [Executar scripts R usando o Azure Data Factory](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample). 

## <a name="compute-environments"></a>Ambientes de computação
Crie um serviço vinculado para o ambiente de computação e, em seguida, usar o serviço vinculado ao definir uma atividade de transformação. Há dois tipos de ambientes de computação com suporte do Data Factory. 

- **Sob demanda**: nesse caso, o ambiente de computação é totalmente gerenciado pelo Data Factory. Ele é automaticamente criado pelo serviço Data Factory antes de um trabalho ser enviado a fim de processar os dados e é removido após a conclusão do trabalho. Você pode configurar e controlar as configurações granulares do ambiente de computação sob demanda para execução do trabalho, gerenciamento de cluster e ações de inicialização. 
- **Traga seu próprio**: nesse caso, você pode registrar no Data Factory seu próprio ambiente de computação (por exemplo, o cluster HDInsight) como um serviço vinculado. O ambiente de computação é gerenciado por você e o serviço Data Factory o utiliza para executar as atividades. 

Veja o artigo [Serviços vinculados de computação](compute-linked-services.md) para saber mais sobre os serviços de computação com suporte do Data Factory. 

## <a name="next-steps"></a>Próximas etapas
Consulte o tutorial a seguir para obter um exemplo de como usar uma atividade de transformação: [Tutorial: transformar dados usando o Spark](tutorial-transform-data-spark-powershell.md)