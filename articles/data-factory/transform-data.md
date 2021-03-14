---
title: Transformar dados
description: Transforme dados ou processe dados em Azure Data Factory usando Hadoop, Azure Machine Learning Studio (clássico) ou Azure Data Lake Analytics.
ms.service: data-factory
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
ms.custom: seo-lt-2019
ms.date: 07/31/2018
ms.openlocfilehash: 0a1eb593e9f9f15f88aefb2fe06706153a4b74a4
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100361393"
---
# <a name="transform-data-in-azure-data-factory"></a>Transformar dados no Azure Data Factory

> [!div class="op_single_selector"]
> * [Fluxo de dados de mapeamento](data-flow-create.md)
> * [Hive](transform-data-using-hadoop-hive.md)  
> * [Pig](transform-data-using-hadoop-pig.md)  
> * [MapReduce](transform-data-using-hadoop-map-reduce.md)  
> * [HDInsight Streaming](transform-data-using-hadoop-streaming.md)
> * [HDInsight Spark](transform-data-using-spark.md)
> * [Azure Machine Learning Studio (clássico)](transform-data-using-machine-learning.md) 
> * [Procedimento armazenado](transform-data-using-stored-procedure.md)
> * [U-SQL da Análise Data Lake](transform-data-using-data-lake-analytics.md)
> * [Bloco de anotações do databricks](transform-data-databricks-notebook.md)
> * [Databricks Jar](transform-data-databricks-jar.md)
> * [Databricks Python](transform-data-databricks-python.md)
> * [Personalizado do .NET](transform-data-using-dotnet-custom-activity.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

## <a name="overview"></a>Visão geral
Este artigo explica as atividades de transformação de dados no Azure Data Factory que você pode usar para transformar e processar seus dados brutos em previsões e ideias em escala. Uma atividade de transformação é executada em um ambiente de computação, como o Azure Databricks ou o Azure HDInsight. Ela fornece links para artigos com informações detalhadas sobre cada atividade de transformação.

O Data Factory dá suporte às seguintes atividades de transformação de dados, que podem ser adicionadas aos [pipelines](concepts-pipelines-activities.md) individualmente ou encadeadas a outra atividade.

## <a name="transform-natively-in-azure-data-factory-with-data-flows"></a>Transformar nativamente em Azure Data Factory com fluxos de dados

### <a name="mapping-data-flows"></a>Fluxos de dados de mapeamento

O mapeamento de fluxos de dados são transformações de dados visualmente projetadas em Azure Data Factory. Os fluxos de dados permitem que os engenheiros de dados desenvolvam a lógica de transformação de dados gráficos sem escrever código. Os fluxos de dados resultantes são executados como atividades dentro de Azure Data Factory pipelines que usam clusters Spark expandidos. As atividades de fluxo de dados podem ser operadas por meio de recursos de agendamento, controle, fluxo e monitoramento existentes de Data Factory. Para obter mais informações, consulte [mapeando fluxos de dados](concepts-data-flow-overview.md).

### <a name="data-wrangling"></a>Wrangling de dados

Power Query no Azure Data Factory permite a Wrangling de dados em escala de nuvem, o que permite que você faça a preparação de dados sem código em escala de nuvem iterativamente. O data Wrangling integra-se com o [Power Query online](/power-query/) e torna Power Query funções M disponíveis para os dados Wrangling em escala de nuvem por meio da execução do Spark. Para obter mais informações, consulte [Data Wrangling in ADF](wrangling-overview.md).

## <a name="external-transformations"></a>Transformações externas

Opcionalmente, você pode codificar manualmente as transformações e gerenciar o ambiente de computação externo.

### <a name="hdinsight-hive-activity"></a>Atividade de Hive do HDInsight
A atividade de Hive do HDInsight em um pipeline de Data Factory executa consultas de Hive em seu próprio cluster ou no cluster sob demanda do HDInsight baseado em Windows/Linux. Consulte o artigo [Hive activity](transform-data-using-hadoop-hive.md) (Atividade do Hive) para obter detalhes sobre essa atividade. 

### <a name="hdinsight-pig-activity"></a>Atividade de Pig do HDInsight
A atividade de Pig do HDInsight em um pipeline de Data Factory executa consultas de Pig em seu próprio cluster ou no cluster sob demanda do HDInsight baseado em Windows/Linux. Consulte o artigo [Pig activity](transform-data-using-hadoop-pig.md) (Atividade do Pig) para obter detalhes sobre essa atividade. 

### <a name="hdinsight-mapreduce-activity"></a>Atividade de MapReduce do HDInsight
A atividade de MapReduce do HDInsight em um pipeline do Data Factory executa programas MapReduce no seu próprio cluster HDInsight baseado em Windows/Linux, ou em um sob demanda. Consulte o artigo [MapReduce activity](transform-data-using-hadoop-map-reduce.md) (Atividade do MapReduce) para obter detalhes sobre essa atividade.

### <a name="hdinsight-streaming-activity"></a>Atividade de Streaming do HDInsight
A atividade de streaming no HDInsight em um pipeline do Data Factory executa programas de streaming do Hadoop em um cluster do HDInsight baseado em Windows/Linux de sua propriedade ou sob demanda. Confira [Atividade de HDInsight Streaming](transform-data-using-hadoop-streaming.md) para obter detalhes sobre essa atividade.

### <a name="hdinsight-spark-activity"></a>Atividade do Spark no HDInsight
A atividade do HDInsight Spark em um pipeline do Data Factory executa programas do Spark em seu próprio cluster HDInsight. Para obter detalhes, consulte [Invocar programas Spark do Azure Data Factory](transform-data-using-spark.md) para obter detalhes. 

### <a name="azure-machine-learning-studio-classic-activities"></a>Atividades Azure Machine Learning Studio (clássicas)
Azure Data Factory permite que você crie facilmente pipelines que usam um serviço Web publicado Azure Machine Learning Studio (clássico) para análise preditiva. Usando a [atividade de execução em lote](transform-data-using-machine-learning.md) em um pipeline Azure data Factory, você pode invocar um serviço Web Studio (clássico) para fazer previsões sobre os dados no lote.

Ao longo do tempo, os modelos de previsão nos experimentos de Pontuação do estúdio (clássico) precisam ser retreinados usando novos conjuntos de dados de entrada. Depois de concluir o retreinamento, você deseja atualizar o serviço Web de pontuação com o modelo de aprendizado de máquina retreinado. Você pode usar a [Atividade de Recurso de Atualização](update-machine-learning-models.md) para atualizar o serviço Web com o modelo recém-treinado.  

Confira [usar atividades Azure Machine Learning Studio (clássicas)](transform-data-using-machine-learning.md) para obter detalhes sobre essas atividades do estúdio (clássico). 

### <a name="stored-procedure-activity"></a>Atividade de procedimento armazenado
Você pode usar a atividade de procedimento armazenado SQL Server em um pipeline de Data Factory para invocar um procedimento armazenado em um dos seguintes repositórios de dados: banco de dados SQL do Azure, análise de Synapse do Azure, banco de dados SQL Server em sua empresa ou uma VM do Azure. Consulte o artigo [Stored Procedure activity](transform-data-using-stored-procedure.md) (Atividade de Procedimento Armazenado) para obter detalhes.  

### <a name="data-lake-analytics-u-sql-activity"></a>Atividade do U-SQL do Data Lake Analytics
A atividade de U-SQL do Data Lake Analytics executa um script U-SQL em um cluster do Azure Data Lake Analytics. Consulte o artigo [Data Analytics U-SQL activity](transform-data-using-data-lake-analytics.md) (atividade de U-SQL do Data Analytics) para obter detalhes. 

### <a name="databricks-notebook-activity"></a>Atividade de Notebook do Databricks

A atividade de Azure Databricks notebook em um pipeline de Data Factory executa um notebook do databricks em seu espaço de trabalho do Azure Databricks. O Azure Databricks é uma plataforma gerenciada para executar o Apache Spark. Veja [Transformar dados executando um bloco de notas do Databricks](transform-data-databricks-notebook.md).

### <a name="databricks-jar-activity"></a>Atividade do Databricks Jar

A atividade de Jar do Azure Databricks em um pipeline do Data Factory executa um Jar de Spark no cluster do Azure Databricks. O Azure Databricks é uma plataforma gerenciada para executar o Apache Spark. Veja [Transformar dados executando uma atividade Jar no Azure Databricks](transform-data-databricks-jar.md).

### <a name="databricks-python-activity"></a>Atividade do Databricks Python

A atividade de Python do Azure Databricks em um pipeline do Data Factory executa um arquivo de Python em seu cluster do Azure Databricks. O Azure Databricks é uma plataforma gerenciada para executar o Apache Spark. Ver [transformar dados executando uma atividade de Python no Azure Databricks](transform-data-databricks-python.md).

### <a name="custom-activity"></a>Atividade personalizada
Se precisar transformar dados de uma maneira que não tenha suporte do Data Factory, você poderá criar uma atividade personalizada com sua própria lógica de processamento de dados e usar a atividade no pipeline. Você pode configurar a atividade personalizada do .NET para que seja executada usando um serviço de Lote do Azure ou um cluster do Azure HDInsight. Confira o artigo [Usar atividades personalizadas](transform-data-using-dotnet-custom-activity.md) para obter detalhes. 

Você pode criar uma atividade personalizada para executar scripts R em seu cluster HDInsight com R instalado. Veja [Executar scripts R usando o Azure Data Factory](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample). 

### <a name="compute-environments"></a>Ambientes de computação
Crie um serviço vinculado para o ambiente de computação e, em seguida, usar o serviço vinculado ao definir uma atividade de transformação. Há dois tipos de ambientes de computação com suporte do Data Factory. 

- **Sob demanda**: nesse caso, o ambiente de computação é totalmente gerenciado pelo Data Factory. Ele é automaticamente criado pelo serviço Data Factory antes de um trabalho ser enviado a fim de processar os dados e é removido após a conclusão do trabalho. Você pode configurar e controlar as configurações granulares do ambiente de computação sob demanda para execução do trabalho, gerenciamento de cluster e ações de inicialização. 
- **Traga seu próprio**: nesse caso, você pode registrar no Data Factory seu próprio ambiente de computação (por exemplo, o cluster HDInsight) como um serviço vinculado. O ambiente de computação é gerenciado por você e o serviço Data Factory o utiliza para executar as atividades. 

Veja o artigo [Serviços vinculados de computação](compute-linked-services.md) para saber mais sobre os serviços de computação com suporte do Data Factory. 

## <a name="next-steps"></a>Próximas etapas
Consulte o tutorial a seguir para obter um exemplo de como usar uma atividade de transformação: [Tutorial: transformar dados usando o Spark](tutorial-transform-data-spark-powershell.md)
