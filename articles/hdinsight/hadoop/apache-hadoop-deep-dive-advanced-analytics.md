---
title: Mergulho profundo - Análise avançada - Azure HDInsight
description: Saiba como a análise avançada usa algoritmos para processar Big Data no Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/01/2020
ms.openlocfilehash: 4b57eddafbf9a5615dc42e9a3c5a49c5f90781e2
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946673"
---
# <a name="deep-dive---advanced-analytics"></a>Mergulho profundo – Análise avançada

## <a name="what-is-advanced-analytics-for-hdinsight"></a>O que é a análise avançada para o HDInsight?

O HDInsight fornece a capacidade de obter insights valiosos de grandes quantidades de dados estruturados, não estruturados e rápida movimentação. A análise avançada é o uso de arquiteturas altamente escalonáveis, modelos de aprendizado estatísticos e de máquina e painéis inteligentes para fornecer insights significativos. O aprendizado de máquina ou *análise preditiva*, usa algoritmos que identificam e aprendem com as relações em seus dados para fazer previsões e orientar suas decisões.

## <a name="advanced-analytics-process"></a>Processo de análise Avançada

![Fluxo de processo de análise avançada](./media/apache-hadoop-deep-dive-advanced-analytics/hdinsight-analytic-process.png)

Depois de identificar o problema de negócios e começar a coletar e processar seus dados, você precisa criar um modelo que representa a pergunta que deseja prever. O modelo usará um ou mais algoritmos de aprendizado de máquina para fazer o tipo de previsão que melhor atenda às suas necessidades de negócios.  A maioria dos dados deve ser usada para treinar seu modelo, e o restante usado para testá-lo ou avaliá-lo.

Depois de criar, carregar, testar e avaliar o seu modelo, a próxima etapa é implantá-lo para que ele comece a fornecer respostas para suas perguntas. A última etapa é monitorar o desempenho do seu modelo e ajustá-lo conforme necessário.

## <a name="common-types-of-algorithms"></a>Tipos comuns de algoritmos

Soluções de análise avançada fornecem um conjunto de algoritmos de aprendizado de máquina. Aqui está um resumo das categorias de algoritmos e casos de uso de negócios comuns associados.

![Resumos de categoria de Machine Learning](./media/apache-hadoop-deep-dive-advanced-analytics/machine-learning-use-cases.png)

Além de selecionar os algoritmos mais adequados, você precisa considerar se precisa ou não fornecer dados para treinamento. Algoritmos de aprendizado de máquina são categorizados da seguinte maneira:

* Supervisionado – o algoritmo precisa ser treinado em um conjunto de dados rotulados antes de poder fornecer resultados
* Semisupervisionado-o algoritmo pode ser aumentado por destinos extras por meio de consulta interativa por um instrutor, que não estava disponível durante o estágio inicial de treinamento
* Não supervisionado-o algoritmo não requer dados de treinamento
* Reforço – o algoritmo usa os agentes do software para determinar o comportamento ideal em um contexto específico (geralmente usado em robótica)

| Categoria de algoritmo| Uso | Tipo de aprendizado | Algoritmos |
| --- | --- | --- | -- |
| classificação | Classificar pessoas ou coisas em grupos | Supervisionado | Árvores de decisão, Regressão logística, redes neurais |
| Clustering | Dividir um conjunto de exemplos em grupos homogêneos | Não supervisionado | Modelo de cluster K-means |
| Detecção de padrões | Identificar associações frequentes nos dados | Não supervisionado | Regras da associação |
| Regressão | Prever resultados numéricos | Supervisionado | Regressão linear, redes neurais |
| Reforço | Determinar comportamento ideal para robôs | Reforço | Simulações Monte Carlo, DeepMind |

## <a name="machine-learning-on-hdinsight"></a>Aprendizado de máquina no HDInsight

O HDInsight tem várias opções de aprendizado de máquina para um fluxo de trabalho de análise avançada:

* Machine Learning e Apache Spark
* R e ML Services
* Azure Machine Learning e Apache Hive
* Apache Spark e Aprendizado profundo

### <a name="machine-learning-and-apache-spark"></a>Machine Learning e Apache Spark

O [HDInsight Spark](../spark/apache-spark-overview.md) é uma oferta do [Apache Spark](https://spark.apache.org/) hospedada no Azure, uma estrutura de processamento de dados unificada, livre e paralela que usa o processamento dentro da memória para melhorar a análise de Big Data. O mecanismo de processamento do Spark foi desenvolvido para velocidade, facilidade de uso e análise sofisticada. As funcionalidades de computação distribuídas na memória do Spark fazem dele uma boa escolha para algoritmos iterativos usados em cálculos de grafo e aprendizado de máquina.

Há três bibliotecas de aprendizado de máquina escalonáveis que oferecem recursos de modelagem de algoritmo para esse ambiente distribuído:

* [**MLlib**](https://spark.apache.org/docs/latest/ml-guide.html) – a MLlib contém a API original criada com base em RDDs Spark.
* [**SparkML**](https://spark.apache.org/docs/1.2.2/ml-guide.html) – SparkML é um pacote mais recente que fornece uma API de nível superior criada sobre o Spark DataFrames para construir pipelines do ML.
* [**MMLSpark**](https://github.com/Azure/mmlspark) – a biblioteca Microsoft Azure Machine Learning para Apache Spark (MMLSpark) é projetada para tornar cientistas de dados mais produtivos em Spark, para aumentar a taxa de experimentação e aproveitar técnicas de aprendizado de máquina de última geração, incluindo aprendizado profundo, em conjuntos de dados muito grandes. A biblioteca MMLSpark simplifica tarefas comuns de modelagem para a criação de modelos em PySpark.

### <a name="r-and-ml-services"></a>R e ML Services

Como parte do HDInsight, é possível criar um cluster do HDInsight com [ML Services](../r-server/r-server-overview.md) pronto para ser usado com modelos e conjuntos de dados em massa. Esse novo recurso fornece aos cientistas de dados e estatísticos uma interface familiar do R que pode ser dimensionada sob demanda por meio de HDInsight, sem a sobrecarga de configuração e manutenção do cluster.

### <a name="azure-machine-learning-and-apache-hive"></a>Azure Machine Learning e Apache Hive

[Azure Machine Learning Studio (clássico)](https://studio.azureml.net/) fornece ferramentas para modelar análises preditivas e um serviço totalmente gerenciado que você pode usar para implantar seus modelos de previsão como serviços Web prontos para consumo. O Azure Machine Learning fornece ferramentas para criar soluções de análise preditiva completas na nuvem para criar, testar, operacionalizar e gerenciar modelos preditivos rapidamente. Selecione de uma grande biblioteca de algoritmos, use um estúdio baseado na Web para a criação de modelos e implante facilmente seu modelo como um serviço Web.

### <a name="apache-spark-and-deep-learning"></a>Apache Spark e Aprendizado profundo

O [Aprendizado profundo](https://www.microsoft.com/research/group/dltc/) é uma ramificação de aprendizado de máquina que usa *redes neurais em profundidade* (DNNs), inspirada pelos processos biológicos cérebro humano. Muitos pesquisadores veem o aprendizado profundo como uma abordagem promissora de inteligência artificial. Alguns exemplos de aprendizado profundo são tradutores idiomas falados, sistemas de reconhecimento de imagens e raciocínio de máquina. Para ajudar a avançar seu próprio trabalho no aprendizado profundo, a Microsoft desenvolveu o [Kit de Ferramentas Cognitivas da Microsoft](https://www.microsoft.com/en-us/cognitive-toolkit/) gratuito, fácil de usar e livre. O kit de ferramentas está sendo usado extensivamente por uma grande variedade de produtos da Microsoft, por empresas em todo o mundo com uma necessidade de implantar o aprendizado profundo em grande escala e por alunos interessados nos algoritmos e técnicas mais recentes.

## <a name="scenario---score-images-to-identify-patterns-in-urban-development"></a>Cenário – imagens de pontuação para identificar padrões de desenvolvimento urbano

Vamos revisar um exemplo de um pipeline de aprendizado de máquina de análise avançada usando o HDInsight.

Nesse cenário, você verá como DNNs produzidos em uma estrutura de aprendizado profundo, o CNTK (Cognitive Toolkit da Microsoft), pode ser operado para a pontuação de coleções de imagens grandes armazenadas em uma conta de armazenamento de BLOBs do Azure usando o PySpark em um cluster HDInsight Spark. Essa abordagem é aplicada a um caso de uso DNN comum, classificação de imagens aéreas e pode ser usada para identificar padrões recentes de desenvolvimento urbano.  Você usará um modelo de classificação de imagem pré-treinado. O modelo é pré-treinado no [conjunto de dados de CIFAR-10](https://www.cs.toronto.edu/~kriz/cifar.html) e foi aplicado a 10.000 imagens retidas.

Há três tarefas-chave nesse cenário de análise avançada:

1. Criar um cluster de Azure HDInsight Hadoop com uma distribuição do Apache Spark 2.1.0.
2. Executar um script personalizado para instalar o Kit de Ferramentas Cognitivas da Microsoft em todos os nós de um cluster do Azure HDInsight Spark.
3. Carregue um Jupyter Notebook pré-criado para o seu cluster HDInsight Spark para aplicar um modelo de aprendizado profundo Microsoft Cognitive Toolkit treinado a arquivos em uma conta de armazenamento de BLOBs do Azure usando a API do Python do Spark (PySpark).

Este exemplo usa o conjunto de imagens CIFAR-10 compilado e distribuído por Alex Krizhevsky, Vinod Nair e Geoffrey Hinton. O conjunto de dados CIFAR-10 contém 60.000 imagens coloridas 32x32 que pertencem a 10 classes mutuamente exclusivas:

![Machine Learning imagens de exemplo](./media/apache-hadoop-deep-dive-advanced-analytics/machine-learning-images.png)

Para saber mais sobre o conjunto de dados, confira Alex Krizhevsky [aprendendo várias camadas de recursos de imagens pequenas](https://www.cs.toronto.edu/~kriz/learning-features-2009-TR.pdf).

O conjunto de dados foi particionado em um conjunto de treinamento de 50.000 imagens e um conjunto de teste de 10.000 imagens. O primeiro conjunto foi usado para treinar um modelo de rede residual (ResNet) convolucional com vinte camadas de profundidade usando o Kit de Ferramentas Cognitivas da Microsoft seguindo [este tutorial](https://github.com/Microsoft/CNTK/tree/master/Examples/Image/Classification/ResNet) do repositório GitHub do Kit de Ferramentas Cognitivas. As 10.000 imagens restantes foram usadas para testar a precisão do modelo. É aí que a computação distribuída entra em cena: a tarefa de pré-processamento e pontuação das imagens é altamente paralelizável. Com o modelo treinado salvo em mãos, usamos:

* PySpark para distribuir as imagens e o modelo treinado para nós de trabalho do cluster.
* Python para pré-processar as imagens em cada nó do cluster HDInsight Spark.
* Kit de Ferramentas Cognitivas para carregar o modelo e a pontuação de imagens pré-processadas em cada nó.
* Jupyter Notebooks para executar o script PySpark, agregar os resultados e usar [Matplotlib](https://matplotlib.org/) para visualizar o desempenho do modelo.

Todo o pré-processamento/pontuação das 10.000 imagens leva menos de um minuto em um cluster com 4 nós de trabalho. O modelo prevê com precisão os rótulos de ~ 9.100 (91%) imagens. Uma matriz de confusão ilustra os erros mais comuns de classificação. Por exemplo, a matriz mostra que a classificação errada de cachorros como gatos e vice-versa ocorre mais frequentemente que em outros pares de rótulos.

![Machine Learning gráfico de resultados](./media/apache-hadoop-deep-dive-advanced-analytics/machine-learning-results.png)

### <a name="try-it-out"></a>Experimente!

Siga [este tutorial](../spark/apache-spark-microsoft-cognitive-toolkit.md) para implementar essa solução de ponta a ponta: configurar um cluster HDInsight Spark, instalar cognitive Toolkit e executar o Jupyter notebook que pontua 10.000 imagens CIFAR.

## <a name="next-steps"></a>Próximas etapas

Apache Hive e Azure Machine Learning

* [Apache Hive e Azure Machine Learning de ponta a ponta](../../machine-learning/team-data-science-process/hive-walkthrough.md)
* [Usando um cluster Azure HDInsight Hadoop em um conjunto de um DataSet de 1 TB](../../machine-learning/team-data-science-process/hive-criteo-walkthrough.md)

Apache Spark e MLLib

* [Machine Learning com Spark em HDInsight no HDInsight](../../machine-learning/team-data-science-process/spark-overview.md)
* [Apache Spark com Machine Learning: Use Apache Spark no HDInsight para analisar a temperatura de edifício usando dados HVAC](../spark/apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark com Machine Learning: Use Apache Spark no HDInsight para prever os resultados da inspeção de alimentos](../spark/apache-spark-machine-learning-mllib-ipython.md)

Aprendizado Profundo, Kit de Ferramentas Cognitivas e outros

* [Máquina Virtual de Ciência de Dados do Azure](../../machine-learning/data-science-virtual-machine/overview.md)
* [Introdução ao H2O.ai no Azure HDInsight](https://azure.microsoft.com/blog/introducing-h2o-ai-with-on-azure-hdinsight-to-bring-the-most-robust-ai-platform-for-enterprises/)
