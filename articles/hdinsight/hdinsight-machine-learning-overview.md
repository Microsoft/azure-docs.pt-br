---
title: Visão geral do aprendizado de máquina - Azure HDInsight
description: Descreve as opções de aprendizado de máquina no HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/19/2018
ms.author: hrasheed
ms.openlocfilehash: ca69424ecbddc068fdaf8bb5839a647701ee27fe
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63766718"
---
# <a name="machine-learning-on-hdinsight"></a>Aprendizado de máquina no HDInsight

O HDInsight permite o aprendizado de máquina com Big Data, proporcionando a capacidade de obter informações valiosas de grandes quantidades (petabytes, ou mesmo exabytes) de dados estruturados, não estruturados e em movimento rápido. Há várias opções de aprendizado de máquina no HDInsight:  SparkML e Apache Spark MLlib, R, Apache Hive e o Microsoft Cognitive Toolkit.

## <a name="sparkml-and-mllib"></a>SparkML e MLlib

O [HDInsight Spark](spark/apache-spark-overview.md) é uma oferta do [Apache Spark](https://spark.apache.org/) hospedada no Azure, uma estrutura de processamento de dados paralelo unificada, de software livre, que fornece suporte ao processamento em memória para aumentar a análise de Big Data. O mecanismo de processamento do Spark foi desenvolvido para velocidade, facilidade de uso e análise sofisticada. As funcionalidades de computação distribuídas na memória do Spark fazem dele uma boa escolha para algoritmos iterativos usados em cálculos de grafo e aprendizado de máquina. Há duas bibliotecas de aprendizado de máquina escalonáveis que oferecem recursos de modelagem de algoritmo para esse ambiente distribuído: MLlib e SparkML. O MLlib contém a API original criada com base em RDDs. O SparkML é um pacote mais recente que fornece uma API de nível superior criada com base em DataFrames para a construção de pipelines do ML. O SparkML ainda não fornece suporte a todos os recursos do MLlib, mas está substituindo o MLlib como biblioteca de aprendizado de máquina padrão do Spark.

A biblioteca do Microsoft Azure Machine Learning para Apache Spark é [MMLSpark](https://github.com/Azure/mmlspark). Essa biblioteca é projetada para tornar os cientistas de dados mais produtivos no Spark, aumentar a taxa de experimentação e alavancar técnicas de aprendizado de máquina avançadas, incluindo aprendizado profundo, em conjuntos de dados muito grandes. O MMLSpark fornece uma camada com base em APIs de baixo nível do SparkML ao construir modelos de ML escalonáveis, como indexação de cadeias de caracteres, forçar dados em um layout esperado por algoritmos de aprendizado de máquina e montagem de vetores de recursos. A biblioteca do MMLSpark simplifica essas e outras tarefas comuns para criação de modelos no PySpark.

## <a name="r"></a>R

[R](https://www.r-project.org/) atualmente é a linguagem de programação estatística mais popular do mundo. É uma ferramenta de visualização de dados de software livre com uma comunidade de mais de 2,5 milhões de usuários e em crescimento. Com sua base de usuários próspera e mais de 8.000 pacotes contribuídos, o R é uma opção provável para muitas empresas que precisam de aprendizado de máquina. Você pode criar um Cluster HDInsight com o ML Services pronto para ser usado com conjuntos de dados maciços e modelos. Essa capacidade fornece aos cientistas de dados e estatísticos uma interface R familiar que pode der dimensionada sob demanda por meio do HDInsight, sem a sobrecarga de configuração e manutenção do cluster.

![Treinamento para previsão com Microsoft R Server](./media/hdinsight-machine-learning-overview/r-training.png)

O nó de borda de um cluster fornece um local conveniente para se conectar ao cluster e executar os scripts de R.  Você também tem a opção de executar scripts R em todos os nós do cluster, usando o Hadoop Map Reduce do ScaleR ou contextos de computação do Spark.

Com o ML Services no HDInsight com Spark, é possível paralelizar o treinamento em todos os nós de um cluster, usando um contexto de computação do Spark. Você pode executar scripts R diretamente no nó de borda, usando todos os núcleos disponíveis em paralelo, conforme necessário. Alternativamente, você poderá executar seu código do nó de borda para iniciar o processamento distribuído em todos os nós no cluster. O ML Services no HDInsight com Spark também permite a paralelização de funções de pacotes R de software livre, se desejado.

## <a name="azure-machine-learning-and-apache-hive"></a>Azure Machine Learning e Apache Hive

O Microsoft Azure Machine Learning fornece ferramentas para modelar análises preditivas, bem como um serviço totalmente gerenciado que você pode usar para implantar seus modelos preditivos como serviços Web prontos para uso. O Microsoft Azure Machine Learning é uma solução de análise preditiva completa na nuvem que você pode usar para criar, testar, operacionalizar e gerenciar modelos preditivos. Selecione de uma grande biblioteca de algoritmos, use um estúdio baseado na Web para a criação de modelos e implante facilmente seu modelo como um serviço Web.

![Criar análises avançadas acessíveis ao Hadoop com Microsoft Azure Machine Learning](./media/hdinsight-machine-learning-overview/hadoop-azure-ml.png)

Criar recursos para dados em um cluster do HDInsight Hadoop usando [consultas do Hive](../machine-learning/team-data-science-process/create-features-hive.md). *A engenharia de recursos* tenta aumentar o poder preditivo dos algoritmos de aprendizado, criando recursos a partir de dados brutos que facilitam o processo de aprendizado. É possível executar consultas do HiveQL no Azure Machine Learning Studio e acessar dados processados no Hive e armazenados no armazenamento de blobs, usando o [módulo Importação de Dados](../machine-learning/studio/import-data.md).

## <a name="microsoft-cognitive-toolkit"></a>Kit de Ferramentas Cognitivas da Microsoft

[Aprendizado profundo](https://www.microsoft.com/en-us/research/group/dltc/) é um branch de aprendizado de máquina que usa redes neurais, inspirado nos processos biológicos do cérebro humano. Muitos pesquisadores veem o aprendizado profundo como uma abordagem promissora para aprimorar a inteligência artificial. Exemplos de aprendizado profundo são os tradutores de idiomas falados, sistemas de reconhecimento de imagens e raciocínio de máquina.

Para ajudar a avançar seu próprio trabalho no aprendizado profundo, a Microsoft desenvolveu o [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/). Este kit de ferramentas está sendo usado por uma grande variedade de produtos da Microsoft, por empresas em todo o mundo com a necessidade de implantar aprendizado profundo em escala e por estudantes interessados nos algoritmos e técnicas mais recentes.

## <a name="see-also"></a>Consulte também

### <a name="scenarios"></a>Cenários

* [Apache Spark com Machine Learning: Usar o Spark no HDInsight para analisar a temperatura de prédios usando dados do sistema de HVAC](spark/apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark com Machine Learning: Usar o Spark no HDInsight para prever resultados da inspeção de alimentos](spark/apache-spark-machine-learning-mllib-ipython.md)
* [Gerar recomendações de filmes com Apache Mahout](hadoop/apache-hadoop-mahout-linux-mac.md)
* [Apache Hive e Azure Machine Learning](../machine-learning/team-data-science-process/create-features-hive.md)
* [Apache Hive e Azure Machine Learning de ponta a ponta](../machine-learning/team-data-science-process/hive-walkthrough.md)
* [Machine Learning com Spark em HDInsight no HDInsight](../machine-learning/team-data-science-process/spark-overview.md)

### <a name="deep-learning-resources"></a>Recursos de aprendizado profundo

* [Usar o Microsoft Cognitive Toolkit modelo com o cluster Azure HDInsight Spark de aprendizado profundo](spark/apache-spark-microsoft-cognitive-toolkit.md)
* [Usar o Caffe no Azure HDInsight Spark para aprendizado aprofundado distribuído](spark/apache-spark-deep-learning-caffe.md)
* [Estruturas de aprendizagem profunda e IA na máquina Virtual de ciência de dados (DSVM)](../machine-learning/data-science-virtual-machine/dsvm-deep-learning-ai-frameworks.md)
