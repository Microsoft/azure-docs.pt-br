---
title: 'Tutorial: Criar aplicativo de aprendizado de máquina do Spark – Azure HDInsight'
description: Tutorial – Instruções passo a passo sobre como criar um aplicativo de machine learning do Apache Spark em clusters do HDInsight Spark usando o Jupyter Notebook.
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive,mvc, devx-track-python
ms.date: 04/07/2020
ms.openlocfilehash: 30ac866aa42861299ed6bac0f0af7522824234e6
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106062855"
---
# <a name="tutorial-build-an-apache-spark-machine-learning-application-in-azure-hdinsight"></a>Tutorial: Compilar um aplicativo de aprendizado de máquina do Apache Spark no Azure HDInsight

Neste tutorial, você aprenderá a usar o [Jupyter Notebook](https://jupyter.org/) para compilar um aplicativo de aprendizado de máquina do [Apache Spark](./apache-spark-overview.md) para o Azure HDInsight.

A [MLlib](https://spark.apache.org/docs/latest/ml-guide.html) é a biblioteca de machine learning adaptável do Spark, que consiste em algoritmos e utilitários de aprendizado comuns. (Classificação, regressão, clustering, filtragem colaborativa e redução de dimensionalidade. Além disso, primitivos de otimização subjacentes).

Neste tutorial, você aprenderá como:
> [!div class="checklist"]
> * Desenvolver um aplicativo de aprendizado de máquina do Apache Spark

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster do Apache Spark no HDInsight. Veja [Criar um cluster do Apache Spark](./apache-spark-jupyter-spark-sql-use-portal.md).

* Familiaridade com o uso de anotações do Jupyter com Spark no HDInsight. Para obter mais informações, confira [Carregar dados e executar consultas com o Apache Spark no HDInsight](./apache-spark-load-data-run-query.md).

## <a name="understand-the-data-set"></a>Entender o conjunto de dados

O aplicativo usa os dados de **HVAC.csv** de exemplo que estão disponíveis em todos os clusters por padrão. O arquivo está localizado em `\HdiSamples\HdiSamples\SensorSampleData\hvac`. Os dados mostram a temperatura de destino e a temperatura real de algumas compilações com sistemas de HVAC instalados. A coluna **System** representa a ID do sistema e a coluna **SystemAge** representa o número de anos que o sistema HVAC foi instalado no prédio. Você pode prever se um prédio será mais quente ou mais frio com base na meta de temperatura, na ID do sistema específico e na idade do sistema.

:::image type="content" source="./media/apache-spark-ipython-notebook-machine-learning/spark-machine-learning-understand-data.png " alt-text="Instantâneo dos dados usados para o exemplo de aprendizado de máquina do Spark" border="true":::

## <a name="develop-a-spark-machine-learning-application-using-spark-mllib"></a>Desenvolver um aplicativo de aprendizado de máquina do Spark usando o MLlib Spark

Esse aplicativo usa um pipeline do [pipeline de ML](https://spark.apache.org/docs/2.2.0/ml-pipeline.html) do Spark para fazer uma classificação de documentos. Os pipelines de ML fornecem um conjunto uniforme de APIs de alto nível criadas com base em DataFrames. Os DataFrames ajudam os usuários a criar e ajustar pipelines práticos de machine learning. No pipeline, você divide o documento em palavras, converte as palavras em um vetor de recurso numérico e, finalmente, cria um modelo de previsão usando as etiquetas e vetores de recurso. Execute as etapas a seguir para criar o aplicativo.

1. Crie um Jupyter Notebook usando o kernel do PySpark. Para obter as instruções, confira [Criar um arquivo do Jupyter Notebook](./apache-spark-jupyter-spark-sql.md#create-a-jupyter-notebook-file).

1. Importe os tipos obrigatórios necessários para este cenário. Cole o snippet a seguir em uma célula vazia e pressione **SHIFT+ENTER**.

    ```PySpark
    from pyspark.ml import Pipeline
    from pyspark.ml.classification import LogisticRegression
    from pyspark.ml.feature import HashingTF, Tokenizer
    from pyspark.sql import Row

    import os
    import sys
    from pyspark.sql.types import *

    from pyspark.mllib.classification import LogisticRegressionWithSGD
    from pyspark.mllib.regression import LabeledPoint
    from numpy import array
    ```

1. Carregue os dados (hvac.csv), analisá-los e usá-los para treinar o modelo.

    ```PySpark
    # Define a type called LabelDocument
    LabeledDocument = Row("BuildingID", "SystemInfo", "label")

    # Define a function that parses the raw CSV file and returns an object of type LabeledDocument
    def parseDocument(line):
        values = [str(x) for x in line.split(',')]
        if (values[3] > values[2]):
            hot = 1.0
        else:
            hot = 0.0

        textValue = str(values[4]) + " " + str(values[5])

        return LabeledDocument((values[6]), textValue, hot)

    # Load the raw HVAC.csv file, parse it using the function
    data = sc.textFile("/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

    documents = data.filter(lambda s: "Date" not in s).map(parseDocument)
    training = documents.toDF()
    ```

    No snippet de código, você definirá uma função que compare a temperatura real com a temperatura de destino. Se a temperatura real é maior, o prédio está quente, indicado pelo valor **1,0**. Caso contrário, a compilação fica fria, como indicado pelo valor **0,0**.

1. Configure o pipeline de aprendizado de máquina Spark que consiste de três estágios: tokenizer, hashingTF e lr.

    ```PySpark
    tokenizer = Tokenizer(inputCol="SystemInfo", outputCol="words")
    hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
    lr = LogisticRegression(maxIter=10, regParam=0.01)
    pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])
    ```

    Para obter mais informações sobre o pipeline e como ele funciona, confira [Pipeline de aprendizado de máquina Apache Spark](https://spark.apache.org/docs/latest/ml-pipeline.html).

1. Ajuste o pipeline para o documento de treinamento.

    ```PySpark
    model = pipeline.fit(training)
    ```

1. Verifique o documento de treinamento para o ponto de verificação de seu progresso com o aplicativo.

    ```PySpark
    training.show()
    ```

    A saída deverá ser semelhante a:

    ```output
    +----------+----------+-----+
    |BuildingID|SystemInfo|label|
    +----------+----------+-----+
    |         4|     13 20|  0.0|
    |        17|      3 20|  0.0|
    |        18|     17 20|  1.0|
    |        15|      2 23|  0.0|
    |         3|      16 9|  1.0|
    |         4|     13 28|  0.0|
    |         2|     12 24|  0.0|
    |        16|     20 26|  1.0|
    |         9|      16 9|  1.0|
    |        12|       6 5|  0.0|
    |        15|     10 17|  1.0|
    |         7|      2 11|  0.0|
    |        15|      14 2|  1.0|
    |         6|       3 2|  0.0|
    |        20|     19 22|  0.0|
    |         8|     19 11|  0.0|
    |         6|      15 7|  0.0|
    |        13|      12 5|  0.0|
    |         4|      8 22|  0.0|
    |         7|      17 5|  0.0|
    +----------+----------+-----+
    ```

    Comparando a saída em relação ao arquivo CSV bruto. Por exemplo, a primeira linha do arquivo CSV tem esses dados:

    :::image type="content" source="./media/apache-spark-ipython-notebook-machine-learning/spark-machine-learning-output-data.png " alt-text="Instantâneo de dados de saída para o exemplo de aprendizado de máquina do Spark" border="true":::

    Observe como a temperatura real é menor que a temperatura de destino sugerindo que o prédio está frio. O valor para o **rótulo** na primeira linha é **0,0**, o que significa que o prédio não está quente.

1. Prepare um conjunto de dados para executar o modelo treinado. Para fazer isso, você passa uma ID do sistema e a idade do sistema (indicada como **SystemInfo** na saída de treinamento). O modelo prevê se o edifício com essa ID do sistema e idade do sistema será mais quente (indicado por 1,0) ou mais frio (indicado por 0,0).

    ```PySpark
    # SystemInfo here is a combination of system ID followed by system age
    Document = Row("id", "SystemInfo")
    test = sc.parallelize([(1L, "20 25"),
                    (2L, "4 15"),
                    (3L, "16 9"),
                    (4L, "9 22"),
                    (5L, "17 10"),
                    (6L, "7 22")]) \
        .map(lambda x: Document(*x)).toDF()
    ```

1. Por fim, faça as previsões nos dados de teste.

    ```PySpark
    # Make predictions on test documents and print columns of interest
    prediction = model.transform(test)
    selected = prediction.select("SystemInfo", "prediction", "probability")
    for row in selected.collect():
        print row
    ```

    A saída deverá ser semelhante a:

    ```output  
    Row(SystemInfo=u'20 25', prediction=1.0, probability=DenseVector([0.4999, 0.5001]))
    Row(SystemInfo=u'4 15', prediction=0.0, probability=DenseVector([0.5016, 0.4984]))
    Row(SystemInfo=u'16 9', prediction=1.0, probability=DenseVector([0.4785, 0.5215]))
    Row(SystemInfo=u'9 22', prediction=1.0, probability=DenseVector([0.4549, 0.5451]))
    Row(SystemInfo=u'17 10', prediction=1.0, probability=DenseVector([0.4925, 0.5075]))
    Row(SystemInfo=u'7 22', prediction=0.0, probability=DenseVector([0.5015, 0.4985]))
    ```

   Observe a primeira linha na previsão. Para um sistema HVAC com ID 20 e sistema de 25 anos, o prédio está quente (**previsão = 1,0**). O primeiro valor de DenseVector (0,49999) corresponde à previsão 0,0 e o segundo valor (0,5001) corresponde à previsão 1,0. Na saída, mesmo que o segundo valor seja apenas um pouco mais alto, o modelo mostra **previsão = 1,0**.

1. Feche o bloco de anotações para liberar os recursos. Para fazer isso, no menu **Arquivo** do notebook, selecione **Fechar e Interromper**. Essa ação desliga e fecha o bloco de anotações.

## <a name="use-anaconda-scikit-learn-library-for-spark-machine-learning"></a>Use a biblioteca Anaconda scikit-learn para aprendizado de máquina do Spark

Os clusters Apache Spark no HDInsight incluem bibliotecas Anaconda. Também inclui a biblioteca **scikit-learn** para aprendizado de máquina. A biblioteca também inclui vários conjuntos de dados que você pode usar para criar aplicativos de exemplo diretamente em um Jupyter Notebook. Para obter exemplos sobre como usar a biblioteca scikit-learn, consulte [https://scikit-learn.org/stable/auto_examples/index.html](https://scikit-learn.org/stable/auto_examples/index.html).

## <a name="clean-up-resources"></a>Limpar os recursos

Se não for continuar a usar este aplicativo, exclua o cluster que criou seguindo estas etapas:

1. Entre no [portal do Azure](https://portal.azure.com/).

1. Na caixa **Pesquisar** na parte superior, digite **HDInsight**.

1. Selecione **Clusters do HDInsight** em **Serviços**.

1. Na lista de clusters do HDInsight exibida, selecione **…** ao lado do cluster que você criou para este tutorial.

1. Selecione **Excluir**. Selecione **Sim** na barra superior.

:::image type="content" source="./media/apache-spark-ipython-notebook-machine-learning/hdinsight-azure-portal-delete-cluster.png " alt-text="O portal do Azure exclui um cluster do HDInsight" border="true":::

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a usar o Jupyter Notebook para criar um aplicativo de aprendizado de máquina do Apache Spark para o Azure HDInsight. Vá para o próximo tutorial para saber como usar IntelliJ IDEA para trabalhos do Spark.

> [!div class="nextstepaction"]
> [Criar um aplicativo Scala Maven usando IntelliJ](./apache-spark-create-standalone-application.md)
