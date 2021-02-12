---
title: Treinar modelos de aprendizado de máquina com o Apache Spark
description: Use Apache Spark no Azure Synapse Analytics para treinar modelos de aprendizado de máquina
author: midesa
ms.author: midesa
ms.reviewer: euang
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: machine-learning
ms.date: 09/13/2020
ms.openlocfilehash: 56b9a98eb72b375aacfeb7cb147997028d3d9ba7
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98116799"
---
# <a name="train-machine-learning-models"></a>Treinar modelos de aprendizado de máquina
Apache Spark no Azure Synapse Analytics permite o aprendizado de máquina com Big Data, fornecendo a capacidade de obter informações valiosas de grandes quantidades de dados estruturados, não estruturados e de movimentação rápida. Há várias opções ao treinar modelos de aprendizado de máquina usando o Azure Spark na análise de Synapse do Azure: Apache Spark MLlib, Azure Machine Learning e várias outras bibliotecas de código-fonte aberto. 

## <a name="apache-sparkml-and-mllib"></a>Apache SparkML e MLlib
O Apache Spark no Azure Synapse Analytics é uma das implementações da Microsoft do Apache Spark na nuvem. Ele fornece uma estrutura de processamento de dados paralela, unificada e de código aberto que dá suporte ao processamento na memória para impulsionar Big Data análise. O mecanismo de processamento do Spark foi desenvolvido para velocidade, facilidade de uso e análise sofisticada. As funcionalidades de computação distribuídas na memória do Spark fazem dele uma boa escolha para algoritmos iterativos usados em cálculos de grafo e aprendizado de máquina. 

Há três bibliotecas de aprendizado de máquina escalonáveis que oferecem recursos de modelagem de algoritmo para esse ambiente distribuído: MLlib e SparkML. O MLlib contém a API original criada com base em RDDs. O SparkML é um pacote mais recente que fornece uma API de nível superior criada com base em DataFrames para a construção de pipelines do ML. O SparkML ainda não dá suporte a todos os recursos do MLlib, mas substitui o MLlib como a biblioteca de aprendizado de máquina padrão do Spark.

> [!NOTE]
> 
> Você pode saber mais sobre como criar um modelo SparkML seguindo este [tutorial](../spark/apache-spark-azure-machine-learning-tutorial.md).

## <a name="popular-libraries"></a>Bibliotecas populares
Cada pool de Apache Spark no Azure Synapse Analytics é fornecido com um conjunto de bibliotecas de aprendizado de máquina precarregados e populares. Essas bibliotecas fornecem código reutilizável que você talvez queira incluir em seus programas ou projetos. Algumas das bibliotecas de aprendizado de máquina relevantes que são incluídas por padrão incluem:
- O [Scikit-Learn](https://scikit-learn.org/stable/index.html) é uma das bibliotecas mais populares de aprendizado de máquina de nó único para algoritmos de ml clássicos. O Scikit-Learn oferece suporte à maioria dos algoritmos de aprendizado supervisionados e não supervisionados e também pode ser usado para mineração de dados e análise de dados.
  
- O [XGBoost](https://xgboost.readthedocs.io/en/latest/) é uma biblioteca de aprendizado de máquina popular que contém algoritmos otimizados para árvores de decisão de treinamento e florestas aleatórias. 
  
- [PyTorch](https://pytorch.org/)  &  [Tensorflow](https://www.tensorflow.org/) são bibliotecas poderosas de aprendizado profundo do Python. Em um pool de Apache Spark no Azure Synapse Analytics, você pode usar essas bibliotecas para criar modelos de máquina única definindo o número de executores em seu pool como zero. Embora Apache Spark não seja funcional nessa configuração, é uma maneira simples e econômica de criar modelos de máquina única.

Você pode saber mais sobre as bibliotecas disponíveis e as versões relacionadas exibindo o [tempo de execução do Azure Synapse Analytics](../spark/apache-spark-version-support.md)publicado.

## <a name="mmlspark"></a>MMLSpark
A biblioteca do Microsoft Azure Machine Learning para Apache Spark é [MMLSpark](https://github.com/Azure/mmlspark). Essa biblioteca foi projetada para tornar os cientistas de dados mais produtivos no Spark, aumentar a taxa de experimentação e aproveitar as técnicas de aprendizado de máquina de ponta, incluindo aprendizado profundo, em grandes conjuntos de dados. 

O MMLSpark fornece uma camada com base em APIs de baixo nível do SparkML ao construir modelos de ML escalonáveis, como indexação de cadeias de caracteres, forçar dados em um layout esperado por algoritmos de aprendizado de máquina e montagem de vetores de recursos. A biblioteca do MMLSpark simplifica essas e outras tarefas comuns para criação de modelos no PySpark.

## <a name="automated-ml-in-azure-machine-learning"></a>ML automatizado no Azure Machine Learning 
O Azure Machine Learning é um ambiente baseado em nuvem que permite treinar, implantar, automatizar, gerenciar e acompanhar modelos de machine learning. O ML automatizado no Azure Machine Learning aceita definições de configuração e dados de treinamento e itera automaticamente por meio de combinações de métodos diferentes de normalização/padronização de recursos, modelos e configurações de hiperparâmetro para chegar ao melhor modelo. 

Ao usar o ML automatizado no Azure Synapse Analytics, você pode aproveitar a profunda integração entre os diferentes serviços para simplificar a autenticação & treinamento do modelo. 

> [!NOTE]
> 
> Você pode saber mais sobre como criar um teste de ML Azure Machine Learning automatizado seguindo este [tutorial](./spark/../apache-spark-azure-machine-learning-tutorial.md).

## <a name="azure-cognitive-services"></a>Serviços Cognitivos do Azure
Os [Serviços cognitivas do Azure](../../cognitive-services/what-are-cognitive-services.md) fornecem recursos de aprendizado de máquina para resolver problemas gerais, como análise de texto de sentimentos emocional ou análise de imagens para reconhecer objetos ou rostos. Você não precisa de machine learning especial nem de conhecimento de ciência de dados para usar esses serviços. Um serviço cognitiva fornece parte ou todos os componentes em uma solução de Machine Learning: dados, algoritmo e modelo treinado. Esses serviços destinam-se a exigir conhecimento geral sobre seus dados sem precisar de experiência com aprendizado de máquina ou ciência de dados. Você pode aproveitar esses serviços cognitivas previamente treinados automaticamente no Azure Synapse Analytics.

## <a name="next-steps"></a>Próximas etapas
Este artigo fornece uma visão geral das várias opções para treinar modelos de aprendizado de máquina dentro de pools de Apache Spark no Azure Synapse Analytics. Você pode aprender mais sobre o treinamento de modelo seguindo o tutorial abaixo:

- Executar experimentos de ML automatizados usando o Azure Machine Learning e o Azure Synapse Analytics: [tutorial de ml automatizado](../spark/apache-spark-azure-machine-learning-tutorial.md) 
- Executar experimentos do SparkML: [tutorial do Apache SparkML](../spark/apache-spark-machine-learning-mllib-notebook.md)
- Exibir as bibliotecas padrão: [tempo de execução do Azure Synapse Analytics](../spark/apache-spark-version-support.md)