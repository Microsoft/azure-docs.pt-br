---
title: Machine Learning com Apache Spark
description: Este artigo fornece uma visão geral conceitual dos recursos de aprendizado de máquina e ciência de dados disponíveis por meio de Apache Spark no Azure Synapse Analytics.
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: machine-learning
ms.date: 11/13/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.openlocfilehash: 0485f697b9360b0f2dfe94fdf07629978b5127c1
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98116918"
---
# <a name="machine-learning-with-apache-spark"></a>Aprendizado de máquina com Apache Spark

Apache Spark no Azure Synapse Analytics permite o aprendizado de máquina com Big Data, fornecendo a capacidade de obter informações valiosas de grandes quantidades de dados estruturados, não estruturados e de movimentação rápida. 

Esta seção inclui uma visão geral e tutoriais para fluxos de trabalho do Machine Learning, incluindo análise de dados exploratório, engenharia de recursos, treinamento de modelo, pontuação de modelo e implantação.  

## <a name="synapse-runtime"></a>Tempo de execução Synapse 
O tempo de execução do Synapse é um ambiente organizado e otimizado para ciência de dados e aprendizado de máquina. O tempo de execução do Synapse oferece uma variedade de bibliotecas populares de software livre e cria no SDK do Azure Machine Learning por padrão. O tempo de execução do Synapse também inclui muitas bibliotecas externas, incluindo PyTorch, Scikit-learn, XGBoost e muito mais.

Saiba mais sobre as bibliotecas disponíveis e as versões relacionadas exibindo o [tempo de execução do Azure Synapse Analytics](../spark/apache-spark-version-support.md)publicado.

## <a name="exploratory-data-analysis"></a>Análise de dados exploratórios
Ao usar Apache Spark no Azure Synapse Analytics, há várias opções internas para ajudá-lo a visualizar seus dados, incluindo opções de gráfico do Synapse notebook, acesso a bibliotecas populares de software livre como o manasceu e o matplotlib, bem como a integração com o Synapse SQL e Power BI.

Saiba mais sobre as opções de visualização de dados e análise de dados exibindo o artigo sobre como [Visualizar dados usando o Azure Synapse notebooks](../spark/apache-spark-data-visualization.md).

## <a name="feature-engineering"></a>Engenharia de recursos
Por padrão, o tempo de execução do Synapse inclui um conjunto de bibliotecas que são comumente usadas para a engenharia de recursos. Para grandes conjuntos de altos, você pode usar Spark SQL, MLlib e koalas para engenharia de recursos. Para conjuntos de valores menores, bibliotecas de terceiros como numpy, pandas e Scikit-Learn também fornecem métodos úteis para esses cenários.

## <a name="train-models"></a>Treinar modelos
Há várias opções ao treinar modelos de aprendizado de máquina usando o Azure Spark na análise de Synapse do Azure: Apache Spark MLlib, Azure Machine Learning e várias outras bibliotecas de código-fonte aberto. 

Saiba mais sobre os recursos de aprendizado de máquina exibindo o artigo sobre como [treinar modelos no Azure Synapse Analytics](../spark/apache-spark-machine-learning-training.md).

### <a name="sparkml-and-mllib"></a>SparkML e MLlib
As funcionalidades de computação distribuídas na memória do Spark fazem dele uma boa escolha para algoritmos iterativos usados em cálculos de grafo e aprendizado de máquina. ```spark.ml``` fornece um conjunto uniforme de APIs de alto nível que ajudam os usuários a criar e ajustar pipelines de aprendizado de máquina. Para saber mais sobre ```spark.ml``` o, você pode visitar o [Guia de programação do Apache Spark ml](https://spark.apache.org/docs/1.2.2/ml-guide.html).

### <a name="azure-machine-learning-automated-ml"></a>Azure Machine Learning ML automatizado
[Azure Machine Learning ml automatizado](../../machine-learning/concept-automated-ml.md) (aprendizado automatizado de máquina) ajuda a automatizar o processo de desenvolvimento de modelos de aprendizado de máquina. Com ele, cientistas de dados, analistas e desenvolvedores podem criar modelos de ML com alta escala, eficiência e produtividade, ao mesmo tempo em que dão suporte à qualidade do modelo. Os componentes para executar o SDK do ML automatizado Azure Machine Learning são criados diretamente no tempo de execução do Synapse.

### <a name="open-source-libraries"></a>Bibliotecas de código-fonte aberto
Cada pool de Apache Spark no Azure Synapse Analytics é fornecido com um conjunto de bibliotecas de aprendizado de máquina precarregados e populares.  Algumas das bibliotecas de aprendizado de máquina relevantes que são incluídas por padrão incluem:

- O [Scikit-Learn](https://scikit-learn.org/stable/index.html) é uma das bibliotecas mais populares de aprendizado de máquina de nó único para algoritmos de ml clássicos. O Scikit-Learn oferece suporte à maioria dos algoritmos de aprendizado supervisionados e não supervisionados e também pode ser usado para mineração de dados e análise de dados.
  
- O [XGBoost](https://xgboost.readthedocs.io/en/latest/) é uma biblioteca de aprendizado de máquina popular que contém algoritmos otimizados para árvores de decisão de treinamento e florestas aleatórias. 
  
- [PyTorch](https://pytorch.org/)  &  [Tensorflow](https://www.tensorflow.org/) são bibliotecas poderosas de aprendizado profundo do Python. Em um pool de Apache Spark no Azure Synapse Analytics, você pode usar essas bibliotecas para criar modelos de máquina única definindo o número de executores em seu pool como zero. Embora Apache Spark não seja funcional nessa configuração, é uma maneira simples e econômica de criar modelos de máquina única.

## <a name="track-model-development"></a>Acompanhar o desenvolvimento de modelos
O [MLFlow](https://www.mlflow.org/) é uma biblioteca de software livre para gerenciar o ciclo de vida de seus experimentos de aprendizado de máquina. O acompanhamento de MLFlow é um componente de MLflow que registra e rastreia suas métricas de execução de treinamento e artefatos de modelo. Para saber mais sobre como você pode usar o acompanhamento de MLFlow por meio do Azure Synapse Analytics e Azure Machine Learning, visite este tutorial sobre [como usar o MLFlow](../../machine-learning/how-to-use-mlflow.md).

## <a name="model-scoring"></a>Pontuação do modelo
A Pontuação do modelo, ou inferência, é a fase em que um modelo é usado para fazer previsões. Para Pontuação de modelo com SparkML ou MLLib, você pode aproveitar os métodos nativos do Spark para executar o inferência diretamente em um dataframe do Spark. Para outras bibliotecas de software livre e tipos de modelo, você também pode criar um UDF do Spark para escalar horizontalmente a inferência em grandes conjuntos de altos. Para conjuntos de valores menores, você também pode usar os métodos de inferência de modelo nativo fornecidos pela biblioteca.

## <a name="register-and-serve-models"></a>Registrar e servir modelos
O registro de um modelo permite que você armazene, versione e rastreie metadados sobre modelos em seu espaço de trabalho. Depois de concluir o treinamento do modelo, você poderá registrar seu modelo no [registro do modelo de Azure Machine Learning](../../machine-learning/concept-model-management-and-deployment.md#register-package-and-deploy-models-from-anywhere). Depois de registrados, os modelos de ONNX também podem ser usados para [enriquecer os dados](../machine-learning/tutorial-sql-pool-model-scoring-wizard.md) armazenados em pools SQL dedicados.

## <a name="next-steps"></a>Próximas etapas
Para começar a usar o Machine Learning no Azure Synapse Analytics, não deixe de conferir os seguintes tutoriais:
- [Analisar dados com o Azure Synapse notebooks](../spark/apache-spark-data-visualization-tutorial.md)

- [Treinar um modelo de aprendizado de máquina com ML automatizado](../spark/apache-spark-azure-machine-learning-tutorial.md)

- [Treinar um modelo de aprendizado de máquina com o Apache Spark MLlib](../spark/apache-spark-machine-learning-mllib-notebook.md)
