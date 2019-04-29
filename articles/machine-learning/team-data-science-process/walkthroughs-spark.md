---
title: Análise no HDInsight Spark com PySpark, Scala – Processo de Ciência de Dados da Equipe
description: Exemplos do Processo de Ciência de Dados de Equipe que fornecem orientação sobre o uso de PySpark e Scala em um Azure HDInsight Spark para fazer análise preditiva.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 09/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: ce57c37791f9fcf353d08e177eb243f4939b52a8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60805519"
---
# <a name="hdinsight-spark-data-science-walkthroughs-using-pyspark-and-scala-on-azure"></a>Passo a passo de ciência de dados do HDInsight Spark usando PySpark e Scala no Azure

Esses passo a passos usam PySpark e Scala em um cluster Azure Spark para executar a análise preditiva. Eles seguem as etapas descritas no Processo de Ciência de Dados de Equipe. Para obter uma visão geral do Processo de Ciência de Dados de Equipe, veja [Processo de Ciência de Dados](overview.md). Para obter uma visão geral do Spark no HDInsight, confira [Introdução ao Spark no HDInsight](../../hdinsight/spark/apache-spark-overview.md).

Outras instruções passo a passo de ciência de dados que executam o Processo de ciência de dados de equipe estão agrupadas pela **plataforma** que elas usam. Consulte [Instruções passo a passo que executam o Processo de ciência de dados de equipe](walkthroughs.md) para obter uma discriminação desses exemplos.

## <a name="predict-taxi-tips-using-pyspark-on-azure-spark"></a>Prever gorjetas de táxi usando PySpark no Azure Spark

O passo a passo [Usar Spark no Azure HDInsight](spark-overview.md) usa dados dos táxis de Nova York para prever se uma gorjeta será paga, e os valores esperados. Ele usa o Processo de Ciência de Dados de Equipe em um cenário que usa um [cluster Spark do Azure HDInsight](https://azure.microsoft.com/services/hdinsight/) para armazenar, explorar e apresentar dados de engenharia do conjunto de dados publicamente disponível de corridas de táxi e tarifas em NYC. Este tópico de visão geral ajuda você a preparar um cluster HDInsight Spark e o notebooks Jupyter PySpark usados no restante do passo a passo. Esses notebooks mostram como explorar os dados e como criar e consumir modelos. Onotebook de exploração e modelagem de dados avançadas mostra como incluir validação cruzada, limpeza de hiperparâmetro e avaliação de modelo.

### <a name="data-exploration-and-modeling-with-spark"></a>Modelagem e exploração de dados com Spark 
Explore o conjunto de dados, crie, pontue e avalie os modelos de aprendizado de máquina usando o tópico [Criar modelos de regressão e classificação binária para dados com o kit de ferramentas MLlib do Spark](spark-data-exploration-modeling.md).

### <a name="model-consumption"></a>Consumo do modelo
Para saber como pontuar os modelos de classificação e regressão criados neste tópico, confira [Pontuar modelos de aprendizado de máquina criados no Spark](spark-model-consumption.md).

### <a name="cross-validation-and-hyperparameter-sweeping"></a>Validação cruzada e limpeza de hiperparâmetro
Confira [Modelagem e exploração de dados avançados com o Spark](spark-advanced-data-exploration-modeling.md) para saber como os modelos podem ser treinados usando a validação cruzada e a limpeza de hiperparâmetro.


## <a name="predict-taxi-tips-using-scala-on-azure-spark"></a>Prever gorjetas de táxi usando Scala no Azure Spark

O passo a passo [Usar Scala com Spark no Azure](scala-walkthrough.md) usa dados dos táxis de Nova York para prever se uma gorjeta será paga, e os valores esperados. Ele mostra como usar o Scala para tarefas de Machine Learning supervisionado com a biblioteca de Machine Learning do Spark (MLlib) e pacotes SparkML em um cluster Spark do Azure HDInsight. Ele explica as tarefas que constituem o [Processo de ciência de dados](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/): ingestão e exploração de dados, visualização, engenharia de recursos, modelagem e consumo de modelo. Os modelos criados incluem a regressão logística e linear, florestas aleatórias e árvores aumentadas gradientes.


## <a name="next-steps"></a>Próximas etapas

Para uma discussão sobre os principais componentes do Processo de Ciência de Dados de Equipe, veja [Visão geral do Processo de Ciência de Dados de Equipe](overview.md).

Para uma discussão sobre o ciclo de vida do Processo de Ciência de Dados de Equipe que você pode usar para estruturar seus projetos de ciência de dados, veja [Ciclo de vida do Processo de Ciência de Dados de Equipe](lifecycle.md). O ciclo de vida descreve as etapas, do início ao fim, que os projetos normalmente seguem quando são executados. 

