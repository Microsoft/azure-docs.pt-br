---
title: Análise no HDInsight Spark com PySpark, Scala – Processo de Ciência de Dados da Equipe
description: Exemplos do processo de ciência de dados de equipe que percorrem o uso de PySpark e escalabilidade em um Azure HDInsight Spark.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 1662af6cd3499fdf851d4e1bd8a0db48da7635b4
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93320125"
---
# <a name="hdinsight-spark-data-science-walkthroughs-using-pyspark-and-scala-on-azure"></a>Passo a passo de ciência de dados do HDInsight Spark usando PySpark e Scala no Azure

Esses passo a passos usam PySpark e Scala em um cluster Azure Spark para executar a análise preditiva. Eles seguem as etapas descritas no Processo de Ciência de Dados de Equipe. Para obter uma visão geral do Processo de Ciência de Dados de Equipe, veja [Processo de Ciência de Dados](overview.md). Para obter uma visão geral do Spark no HDInsight, confira [Introdução ao Spark no HDInsight](../../hdinsight/spark/apache-spark-overview.md).

Outras instruções passo a passo de ciência de dados que executam o Processo de ciência de dados de equipe estão agrupadas pela **plataforma** que elas usam. Consulte [Instruções passo a passo que executam o Processo de ciência de dados de equipe](walkthroughs.md) para obter uma discriminação desses exemplos.

## <a name="predict-taxi-tips-using-pyspark-on-azure-spark"></a>Prever gorjetas de táxi usando PySpark no Azure Spark

Com o uso de dados de táxi de Nova York, a instrução [use Spark no Azure HDInsight](spark-overview.md) prevê se uma gorjeta é paga e o intervalo de valores esperados. Este exemplo usa o processo de ciência de dados de equipe em um cenário usando um [cluster Azure HDInsight Spark](https://azure.microsoft.com/services/hdinsight/) para armazenar, explorar e recursos de engenharia de recurso do conjunto de dados publicamente disponível de corridas e tarifas de táxi de NYC. Este tópico de visão geral usa um cluster HDInsight Spark e notebooks Jupyter PySpark. Esses notebooks mostram como explorar os dados e como criar e consumir modelos. Onotebook de exploração e modelagem de dados avançadas mostra como incluir validação cruzada, limpeza de hiperparâmetro e avaliação de modelo.

### <a name="data-exploration-and-modeling-with-spark"></a>Modelagem e exploração de dados com Spark 
Explore o conjunto de dados, crie, pontue e avalie os modelos de aprendizado de máquina usando o tópico [Criar modelos de regressão e classificação binária para dados com o kit de ferramentas MLlib do Spark](spark-data-exploration-modeling.md).

### <a name="model-consumption"></a>Consumo do modelo
Para saber como pontuar os modelos de classificação e regressão criados neste tópico, confira [Pontuar modelos de aprendizado de máquina criados no Spark](spark-model-consumption.md).

### <a name="cross-validation-and-hyperparameter-sweeping"></a>Validação cruzada e limpeza de hiperparâmetro
Consulte [exploração e modelagem de dados avançados com o Spark](spark-advanced-data-exploration-modeling.md) sobre como os modelos podem ser treinados usando a validação cruzada e a limpeza de hiperparâmetro.


## <a name="predict-taxi-tips-using-scala-on-azure-spark"></a>Prever gorjetas de táxi usando Scala no Azure Spark

A instrução [use escalar com Spark no Azure](scala-walkthrough.md) prevê se uma gorjeta é paga e o intervalo de valores esperado a ser pago. Ele mostra como usar o Scala para tarefas de Machine Learning supervisionado com a biblioteca de Machine Learning do Spark (MLlib) e pacotes SparkML em um cluster Spark do Azure HDInsight. Ele orienta você pelas tarefas que constituem o [processo de ciência de dados](./index.yml): ingestão de dados e exploração, visualização, engenharia de recursos, modelagem e consumo de modelo. Os modelos criados incluem a regressão logística e linear, florestas aleatórias e árvores aumentadas gradientes.


## <a name="next-steps"></a>Próximas etapas

Para obter uma visão geral do processo de ciência de dados de equipe, consulte [visão geral do processo de ciência de dados de equipe](overview.md).

Para obter uma discussão sobre o ciclo de vida do processo de ciência de dados de equipe, consulte [ciclo de vida do processo de ciência de dados](lifecycle.md) Esse ciclo de vida descreve as etapas, do início ao fim, que os projetos geralmente seguem quando são executados.