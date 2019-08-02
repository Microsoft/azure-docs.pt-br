---
title: Criar um pipeline de Machine Learning do Apache Spark - Microsoft Azure HDInsight
description: Usar a biblioteca de Machine Learning do Apache Spark para criar pipelines de dados.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 07/22/2019
ms.openlocfilehash: 4ad68ef33eb469c7949c3f3efcd55d6765da4158
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68441874"
---
# <a name="create-an-apache-spark-machine-learning-pipeline"></a>Criar um pipeline de machine learning do Apache Spark

A biblioteca de Machine Learning escalonável do Apache Spark (MLlib) oferece recursos de modelagem para um ambiente distribuído. O pacote Spark [`spark.ml`](https://spark.apache.org/docs/latest/ml-pipeline.html) é um conjunto de APIs de alto nível construído em DataFrames. Essas APIs ajudam a criar e ajustar pipelines práticos de Machine Learning.  *Machine Learning do Spark* refere-se a essa API baseada em DataFrame MLlib, e não à API de pipeline baseada em RDD mais antiga.

Um pipeline de Machine Learning (ML) é um fluxo de trabalho completo que combina vários algoritmos de Machine Learning em conjunto. Pode haver muitas etapas necessárias para processar e aprender com dados, exigindo uma sequência de algoritmos. Os pipelines definem os estágios e a ordenação de um processo de Machine Learning. No MLlib, os estágios de um pipeline são representados por uma sequência específica de PipelineStages, onde um Transformador e um Estimador executam cada uma das tarefas.

Um Transformador é um algoritmo que transforma um DataFrame para outro usando o método `transform()`. Por exemplo, um transformador de recursos pode ler uma coluna de um DataFrame, mapeá-la para outra coluna e produzir um novo DataFrame com a coluna mapeada anexada a ele.

Um Estimador é uma abstração de algoritmos de aprendizagem e é responsável pelo ajuste ou treinamento em um conjunto de dados para produzir um Transformador. Um Estimador implementa um método chamado `fit()`, que aceita um DataFrame e produz um DataFrame, que é um Transformador.

Cada instância sem estado de um Transformador ou um Estimador tem seu próprio identificador exclusivo, que é utilizado ao especificar parâmetros. Ambos usam uma API uniforme para especificar esses parâmetros.

## <a name="pipeline-example"></a>Exemplo de pipeline

Para demonstrar um uso prático de um pipeline de ML, este exemplo usa o arquivo de dados de exemplo `HVAC.csv` que é fornecido pré-carregado no armazenamento padrão do cluster do HDInsight, do Armazenamento do Azure ou Data Lake Storage. Para exibir o conteúdo do arquivo, navegue até o diretório `/HdiSamples/HdiSamples/SensorSampleData/hvac`. `HVAC.csv` contém um conjunto de vezes com as temperaturas real e de destino para os sistemas HVAC (*aquecimento, ventilação e ar condicionado*) em várias construções. A meta é treinar o modelo nos dados e produzir uma temperatura de previsão para uma determinada construção.

O seguinte código:

1. Define um `LabeledDocument`, que armazena `BuildingID`, `SystemInfo` (um identificador do sistema e duração), e `label` (1,0 se a construção estiver muito quente, 0,0 caso contrário).
2. Cria uma função de analisador personalizada `parseDocument` que leva uma linha (linha) de dados e determina se a construção está "quente" comparando a temperatura de destino com a temperatura real.
3. Aplica o analisador ao extrair os dados de origem.
4. Criar dados de treinamento.

```python
from pyspark.ml import Pipeline
from pyspark.ml.classification import LogisticRegression
from pyspark.ml.feature import HashingTF, Tokenizer
from pyspark.sql import Row

# The data structure (column meanings) of the data array:
# 0 Date
# 1 Time
# 2 TargetTemp
# 3 ActualTemp
# 4 System
# 5 SystemAge
# 6 BuildingID

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
data = sc.textFile(
    "wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

documents = data.filter(lambda s: "Date" not in s).map(parseDocument)
training = documents.toDF()
```

Esse exemplo de pipeline tem três etapa: `Tokenizer` e `HashingTF` (ambos Transformadores) e `Logistic Regression` (um Estimador).  Os dados extraídos e analisados no DataFrame `training`flui através do pipeline quando `pipeline.fit(training)` é chamado.

1. O primeiro estágio, `Tokenizer`, divide a coluna de entrada `SystemInfo` (consistindo no identificador do sistema e valores de duração) em uma coluna de saída `words`. Essa nova coluna `words` é adicionada ao DataFrame. 
2. O segundo estágio, `HashingTF`, converte a nova coluna `words` vetores de recursos. Essa nova coluna `features` é adicionada ao DataFrame. Essas duas primeiras etapas são Transformadores. 
3. O terceiro estágio, `LogisticRegression`, é um estimador e, portanto, o pipeline chama o método `LogisticRegression.fit()` para produzir um `LogisticRegressionModel`. 

```python
tokenizer = Tokenizer(inputCol="SystemInfo", outputCol="words")
hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
lr = LogisticRegression(maxIter=10, regParam=0.01)

# Build the pipeline with our tokenizer, hashingTF, and logistic regression stages
pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])

model = pipeline.fit(training)
```

Para consultar as novas colunas `words` e `features` adicionadas pelos transformadores `Tokenizer` e `HashingTF`, e um exemplo do Estimador `LogisticRegression`, execute um método `PipelineModel.transform()` no DataFrame original. No código de produção, a próxima etapa seria passar em um DataFrame de teste para validar o treinamento.

```python
peek = model.transform(training)
peek.show()

# Outputs the following:
+----------+----------+-----+--------+--------------------+--------------------+--------------------+----------+
|BuildingID|SystemInfo|label|   words|            features|       rawPrediction|         probability|prediction|
+----------+----------+-----+--------+--------------------+--------------------+--------------------+----------+
|         4|     13 20|  0.0|[13, 20]|(262144,[250802,2...|[0.11943986671420...|[0.52982451901740...|       0.0|
|        17|      3 20|  0.0| [3, 20]|(262144,[89074,25...|[0.17511205617446...|[0.54366648775222...|       0.0|
|        18|     17 20|  1.0|[17, 20]|(262144,[64358,25...|[0.14620993833623...|[0.53648750722548...|       0.0|
|        15|      2 23|  0.0| [2, 23]|(262144,[31351,21...|[-0.0361327091023...|[0.49096780538523...|       1.0|
|         3|      16 9|  1.0| [16, 9]|(262144,[153779,1...|[-0.0853679939336...|[0.47867095324139...|       1.0|
|         4|     13 28|  0.0|[13, 28]|(262144,[69821,25...|[0.14630166986618...|[0.53651031790592...|       0.0|
|         2|     12 24|  0.0|[12, 24]|(262144,[187043,2...|[-0.0509556393066...|[0.48726384581522...|       1.0|
|        16|     20 26|  1.0|[20, 26]|(262144,[128319,2...|[0.33829638728900...|[0.58377663577684...|       0.0|
|         9|      16 9|  1.0| [16, 9]|(262144,[153779,1...|[-0.0853679939336...|[0.47867095324139...|       1.0|
|        12|       6 5|  0.0|  [6, 5]|(262144,[18659,89...|[0.07513008136562...|[0.51877369045183...|       0.0|
|        15|     10 17|  1.0|[10, 17]|(262144,[64358,25...|[-0.0291988646553...|[0.49270080242078...|       1.0|
|         7|      2 11|  0.0| [2, 11]|(262144,[212053,2...|[0.03678030020834...|[0.50919403860812...|       0.0|
|        15|      14 2|  1.0| [14, 2]|(262144,[109681,2...|[0.06216423725633...|[0.51553605651806...|       0.0|
|         6|       3 2|  0.0|  [3, 2]|(262144,[89074,21...|[0.00565582077537...|[0.50141395142468...|       0.0|
|        20|     19 22|  0.0|[19, 22]|(262144,[139093,2...|[-0.0769288695989...|[0.48077726176073...|       1.0|
|         8|     19 11|  0.0|[19, 11]|(262144,[139093,2...|[0.04988910033929...|[0.51246968885151...|       0.0|
|         6|      15 7|  0.0| [15, 7]|(262144,[77099,20...|[0.14854929135994...|[0.53706918109610...|       0.0|
|        13|      12 5|  0.0| [12, 5]|(262144,[89689,25...|[-0.0519932532562...|[0.48700461408785...|       1.0|
|         4|      8 22|  0.0| [8, 22]|(262144,[98962,21...|[-0.0120753606650...|[0.49698119651572...|       1.0|
|         7|      17 5|  0.0| [17, 5]|(262144,[64358,89...|[-0.0721054054871...|[0.48198145477106...|       1.0|
+----------+----------+-----+--------+--------------------+--------------------+--------------------+----------+

only showing top 20 rows
```

O objeto `model` agora pode ser usado para fazer previsões. Para obter um exemplo completo desse aplicativo de Machine Learning e as instruções passo a passo para executá-lo, consulte [Criar aplicativos de Machine Learning do Apache Spark no Azure HDInsight](apache-spark-ipython-notebook-machine-learning.md).

## <a name="see-also"></a>Consulte também

* [Ciência de Dados usando o Scala e o Apache Spark no Azure](../../machine-learning/team-data-science-process/scala-walkthrough.md)
