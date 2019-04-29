---
title: Ciência de dados usando o Scala e o Spark no Azure – Processo de Ciência de Dados da Equipe
description: Como usar o Scala para tarefas de aprendizado de máquina supervisionadas com a MLlib escalonável do Spark e os pacotes de AM do Spark em um cluster Azure HDInsight Spark.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: cdc37ace4687fe978030f528dcd5cbc87da596f0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60589491"
---
# <a name="data-science-using-scala-and-spark-on-azure"></a>Ciência de Dados usando o Scala e o Spark no Azure
Este artigo mostra como usar o Scala para tarefas de aprendizado de máquina supervisionadas com a MLlib escalonável do Spark e os pacotes de AM do Spark em um cluster Azure HDInsight Spark. Ele explica as tarefas que constituem o [Processo Ciência de Dados](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/): ingestão e exploração de dados, visualização, engenharia de recursos, modelagem e consumo de modelo. Os modelos no artigo incluem regressão logística e linear, florestas aleatórias e GBTs (árvores com aumento de gradiente), além de duas tarefas comuns de aprendizado de máquina supervisionadas:

* Problema de regressão: Previsão do valor da gorjeta (US$) para uma corrida de táxi
* Classificação binária: Previsão do valor da gorjeta ou não (1/0) para uma corrida de táxi

O processo de modelagem requer treinamento e avaliação em um conjunto de dados de teste e métricas de precisão relevantes. Neste artigo, você pode aprender a armazenar esses modelos no armazenamento de Blobs do Azure e a pontuar e avaliar seu desempenho preditivo. Este artigo também aborda os tópicos mais avançados de como otimizar modelos usando a validação cruzada e limpeza de hiperparâmetro. Os dados usados são uma amostra do conjunto de dados de corridas e tarifas de táxi em Nova York de 2013, disponível no GitHub.

[Scala](https://www.scala-lang.org/), uma linguagem baseada na máquina virtual Java, integra conceitos de linguagem funcional e orientada a objetos. Trata-se de uma linguagem escalonável que é bastante adequada ao processamento distribuído na nuvem e executada em clusters Azure Spark.

[Spark](https://spark.apache.org/) é uma estrutura de processamento paralelo de software livre que dá suporte ao processamento na memória para melhorar o desempenho de aplicativos analíticos de Big Data. O mecanismo de processamento do Spark foi desenvolvido para velocidade, facilidade de uso e análise sofisticada. As funcionalidades de computação distribuídas na memória do Spark fazem dele uma boa escolha para algoritmos iterativos em cálculos de grafo e aprendizado de máquina. O pacote [spark.ml](https://spark.apache.org/docs/latest/ml-guide.html) fornece um conjunto uniforme de APIs de alto nível criadas com base em quadros de dados, que podem ajudar você a criar e ajustar pipelines práticos de aprendizado de máquina. [MLlib](https://spark.apache.org/mllib/) é a biblioteca de aprendizado de máquina escalonável do Spark, que oferece recursos de modelagem para esse ambiente distribuído.

[HDInsight Spark](../../hdinsight/spark/apache-spark-overview.md) é a oferta do Spark de software livre hospedada no Azure. Ele também inclui suporte para notebooks Scala do Jupyter no cluster Spark, e pode executar consultas interativas do Spark SQL para transformar, filtrar e visualizar dados armazenados no armazenamento de Blobs do Azure. Os snippets de código Scala neste artigo que fornecem as soluções e mostram as gráficos relevantes para visualizar os dados executados em notebooks Jupyter instalados nos clusters Spark. As etapas de modelagem nestes tópicos contêm código que mostra como treinar, avaliar, salvar e consumir cada tipo de modelo.

As etapas de configuração e o código neste artigo são para o Azure HDInsight 3.4 Spark 1.6. No entanto, os códigos neste artigo e no [Notebook Jupyter para Scala](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Scala/Exploration%20Modeling%20and%20Scoring%20using%20Scala.ipynb) são genéricos e devem funcionar em qualquer cluster Spark. As etapas de configuração e gerenciamento do cluster poderão ser ligeiramente diferentes do que é mostrado neste artigo se você não estiver usando o HDInsight Spark.

> [!NOTE]
> Para ver um tópico que mostre como usar o Python, em vez do Scala, para concluir as tarefas em um processo completo de Ciência de dados, confira [Visão geral da Ciência de dados usando Spark no Azure HDInsight](spark-overview.md).
> 
> 

## <a name="prerequisites"></a>Pré-requisitos
* Você precisa ter uma assinatura do Azure. Se ainda não tiver uma, [obtenha uma avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Você precisa de um cluster Azure HDInsight 3.4 Spark 1.6 para concluir os procedimentos a seguir. Para criar um cluster, confira as instruções em [Introdução: Criar o Apache Spark no Azure HDInsight](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md). Defina o tipo de cluster e a versão no menu **Selecionar Tipo de Cluster** .

![Configuração de tipo de cluster do HDInsight](./media/scala-walkthrough/spark-cluster-on-portal.png)

> [!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]
> 
> 

Para obter uma descrição dos dados da corrida de táxi na cidade de Nova York e obter instruções sobre como executar código em um notebook Jupyter no cluster Spark, confira as seções relevantes de [Visão geral da Ciência de dados usando Spark no Azure HDInsight](spark-overview.md).  

## <a name="execute-scala-code-from-a-jupyter-notebook-on-the-spark-cluster"></a>Executar código em um notebook Jupyter no cluster Spark
É possível iniciar o notebook Jupyter no Portal do Azure. Encontre o cluster Spark no painel e clique nele para entrar na página de gerenciamento de seu cluster. Em seguida, clique em **Painéis do Cluster** e clique em **Notebook Jupyter** para abrir o notebook associado ao cluster Spark.

![Painel do cluster e notebooks Jupyter](./media/scala-walkthrough/spark-jupyter-on-portal.png)

Você também pode acessar blocos de anotações do Jupyter em https://&lt;clustername&gt;.azurehdinsight.net/jupyter. Substitua *clustername* pelo nome do cluster. Você precisa da senha de sua conta de administrador para acessar os notebooks Jupyter.

![Acesse os notebooks Jupyter usando o nome do cluster](./media/scala-walkthrough/spark-jupyter-notebook.png)

Selecione **Scala** para ver um diretório com alguns exemplos de notebooks predefinidos que usam a API PySpark. O notebook Exploration Modeling and Scoring using Scala.ipynb, que contém os exemplos de código para este conjunto de tópicos Spark, está disponível no [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/Scala).

Você pode carregar o notebook diretamente do GitHub para o servidor do Notebook Jupyter em seu cluster Spark. Na home page do Jupyter, clique no botão **Carregar** . No gerenciador de arquivos, cole a URL do GitHub (conteúdo bruto) do notebook Scala e clique em **Abrir**. O notebook Scala está disponível na seguinte URL:

[Exploration-Modeling-and-Scoring-using-Scala.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Scala/Exploration-Modeling-and-Scoring-using-Scala.ipynb)

## <a name="setup-preset-spark-and-hive-contexts-spark-magics-and-spark-libraries"></a>Configuração: Contextos predefinidos de Spark e Hive, palavras mágicas Spark e bibliotecas Spark
### <a name="preset-spark-and-hive-contexts"></a>Contextos predefinidos de Spark e Hive
    # SET THE START TIME
    import java.util.Calendar
    val beginningTime = Calendar.getInstance().getTime()


Os kernels Spark fornecidos com os notebooks Jupyter têm contextos predefinidos. Não é necessário definir explicitamente os contextos Spark ou Hive antes de começar a trabalhar com o aplicativo que você está desenvolvendo. Os contextos de predefinição são:

* `sc` para SparkContext
* `sqlContext` para HiveContext

### <a name="spark-magics"></a>Palavras mágicas do Spark
O kernel do Spark fornece algumas "palavras mágicas" predefinidas, que são comandos especiais que você pode chamar com `%%`. Dois desses comandos são usados nos exemplos de código a seguir.

* `%%local` especifica que o código nas linhas posteriores será executado localmente. Deve ser um código Scala válido.
* `%%sql -o <variable name>` executa uma consulta do Hive no `sqlContext`. Se o parâmetro `-o` for transmitido, o resultado da consulta será persistido no contexto `%%local` do Scala como um quadro de dados do Spark.

Para saber mais sobre os kernels para notebooks Jupyter e as "palavras mágicas" predefinidas chamadas com `%%` (por exemplo, `%%local`) confira [Kernels disponíveis para notebooks Jupyter com clusters do Apache Spark no HDInsight Linux](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md).

### <a name="import-libraries"></a>Importar bibliotecas
Importe o Spark, a MLlib e outras bibliotecas necessárias usando o código a seguir.

    # IMPORT SPARK AND JAVA LIBRARIES
    import org.apache.spark.sql.SQLContext
    import org.apache.spark.sql.functions._
    import java.text.SimpleDateFormat
    import java.util.Calendar
    import sqlContext.implicits._
    import org.apache.spark.sql.Row

    # IMPORT SPARK SQL FUNCTIONS
    import org.apache.spark.sql.types.{StructType, StructField, StringType, IntegerType, FloatType, DoubleType}
    import org.apache.spark.sql.functions.rand

    # IMPORT SPARK ML FUNCTIONS
    import org.apache.spark.ml.Pipeline
    import org.apache.spark.ml.feature.{StringIndexer, VectorAssembler, OneHotEncoder, VectorIndexer, Binarizer}
    import org.apache.spark.ml.tuning.{ParamGridBuilder, TrainValidationSplit, CrossValidator}
    import org.apache.spark.ml.regression.{LinearRegression, LinearRegressionModel, RandomForestRegressor, RandomForestRegressionModel, GBTRegressor, GBTRegressionModel}
    import org.apache.spark.ml.classification.{LogisticRegression, LogisticRegressionModel, RandomForestClassifier, RandomForestClassificationModel, GBTClassifier, GBTClassificationModel}
    import org.apache.spark.ml.evaluation.{BinaryClassificationEvaluator, RegressionEvaluator, MulticlassClassificationEvaluator}

    # IMPORT SPARK MLLIB FUNCTIONS
    import org.apache.spark.mllib.linalg.{Vector, Vectors}
    import org.apache.spark.mllib.util.MLUtils
    import org.apache.spark.mllib.classification.{LogisticRegressionWithLBFGS, LogisticRegressionModel}
    import org.apache.spark.mllib.regression.{LabeledPoint, LinearRegressionWithSGD, LinearRegressionModel}
    import org.apache.spark.mllib.tree.{GradientBoostedTrees, RandomForest}
    import org.apache.spark.mllib.tree.configuration.BoostingStrategy
    import org.apache.spark.mllib.tree.model.{GradientBoostedTreesModel, RandomForestModel, Predict}
    import org.apache.spark.mllib.evaluation.{BinaryClassificationMetrics, MulticlassMetrics, RegressionMetrics}

    # SPECIFY SQLCONTEXT
    val sqlContext = new SQLContext(sc)


## <a name="data-ingestion"></a>Ingestão de dados
A primeira etapa no processo de Ciência de dados é ingerir os dados que você deseja analisar. Traga os dados de fontes ou sistemas externos onde eles residem para seu ambiente de modelagem e a exploração de dados. Neste artigo, os dados ingeridos são uma junção de 0,1% do exemplo arquivo sobre gorjeta e tarifa de táxi (armazenado como um arquivo .tsv). O ambiente de exploração e modelagem de dados é Spark. Esta seção contém o código para concluir a seguinte série de tarefas:

1. Definir os caminhos de diretório para o armazenamento de dados e de modelos.
2. Ler o conjunto de dados (armazenado como um arquivo .tsv).
3. Definir um esquema para os dados e limpar os dados.
4. Criar um quadro de dados limpo e armazená-lo em cache na memória.
5. Registrar os dados como uma tabela temporária no SQLContext.
6. Consultar a tabela e importar os resultados em um quadro de dados.

### <a name="set-directory-paths-for-storage-locations-in-azure-blob-storage"></a>Definir caminhos de diretório para locais de armazenamento no armazenamento de Blobs do Azure
O Spark pode ler e gravar no armazenamento de Blobs do Azure. Você pode usar o Spark para processar todos os dados existentes e, depois, armazenar os resultados novamente no armazenamento de Blobs.

Para salvar arquivos ou modelos no armazenamento de Blobs, você precisa especificar o caminho corretamente. Faça referência ao contêiner padrão anexado ao cluster Spark usando um caminho que começa com `wasb:///`. Faça referência a outros locais usando `wasb://`.

O exemplo de código a seguir especifica o local dos dados de entrada a serem lidos e o caminho para o armazenamento de blobs que é anexado ao cluster Spark no qual o modelo será salvo.

    # SET PATHS TO DATA AND MODEL FILE LOCATIONS
    # INGEST DATA AND SPECIFY HEADERS FOR COLUMNS
    val taxi_train_file = sc.textFile("wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Train.tsv")
    val header = taxi_train_file.first;

    # SET THE MODEL STORAGE DIRECTORY PATH
    # NOTE THAT THE FINAL BACKSLASH IN THE PATH IS REQUIRED.
    val modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/";


### <a name="import-data-create-an-rdd-and-define-a-data-frame-according-to-the-schema"></a>Importar dados, criar um RDD e definir um quadro de dados de acordo com o esquema
    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # DEFINE THE SCHEMA BASED ON THE HEADER OF THE FILE
    val sqlContext = new SQLContext(sc)
    val taxi_schema = StructType(
        Array(
            StructField("medallion", StringType, true),
            StructField("hack_license", StringType, true),
            StructField("vendor_id", StringType, true),
            StructField("rate_code", DoubleType, true),
            StructField("store_and_fwd_flag", StringType, true),
            StructField("pickup_datetime", StringType, true),
            StructField("dropoff_datetime", StringType, true),
            StructField("pickup_hour", DoubleType, true),
            StructField("pickup_week", DoubleType, true),
            StructField("weekday", DoubleType, true),
            StructField("passenger_count", DoubleType, true),
            StructField("trip_time_in_secs", DoubleType, true),
            StructField("trip_distance", DoubleType, true),
            StructField("pickup_longitude", DoubleType, true),
            StructField("pickup_latitude", DoubleType, true),
            StructField("dropoff_longitude", DoubleType, true),
            StructField("dropoff_latitude", DoubleType, true),
            StructField("direct_distance", StringType, true),
            StructField("payment_type", StringType, true),
            StructField("fare_amount", DoubleType, true),
            StructField("surcharge", DoubleType, true),
            StructField("mta_tax", DoubleType, true),
            StructField("tip_amount", DoubleType, true),
            StructField("tolls_amount", DoubleType, true),
            StructField("total_amount", DoubleType, true),
            StructField("tipped", DoubleType, true),
            StructField("tip_class", DoubleType, true)
            )
        )

    # CAST VARIABLES ACCORDING TO THE SCHEMA
    val taxi_temp = (taxi_train_file.map(_.split("\t"))
                            .filter((r) => r(0) != "medallion")
                            .map(p => Row(p(0), p(1), p(2),
                                p(3).toDouble, p(4), p(5), p(6), p(7).toDouble, p(8).toDouble, p(9).toDouble, p(10).toDouble,
                                p(11).toDouble, p(12).toDouble, p(13).toDouble, p(14).toDouble, p(15).toDouble, p(16).toDouble,
                                p(17), p(18), p(19).toDouble, p(20).toDouble, p(21).toDouble, p(22).toDouble,
                                p(23).toDouble, p(24).toDouble, p(25).toDouble, p(26).toDouble)))


    # CREATE AN INITIAL DATA FRAME AND DROP COLUMNS, AND THEN CREATE A CLEANED DATA FRAME BY FILTERING FOR UNWANTED VALUES OR OUTLIERS
    val taxi_train_df = sqlContext.createDataFrame(taxi_temp, taxi_schema)

    val taxi_df_train_cleaned = (taxi_train_df.drop(taxi_train_df.col("medallion"))
            .drop(taxi_train_df.col("hack_license")).drop(taxi_train_df.col("store_and_fwd_flag"))
            .drop(taxi_train_df.col("pickup_datetime")).drop(taxi_train_df.col("dropoff_datetime"))
            .drop(taxi_train_df.col("pickup_longitude")).drop(taxi_train_df.col("pickup_latitude"))
            .drop(taxi_train_df.col("dropoff_longitude")).drop(taxi_train_df.col("dropoff_latitude"))
            .drop(taxi_train_df.col("surcharge")).drop(taxi_train_df.col("mta_tax"))
            .drop(taxi_train_df.col("direct_distance")).drop(taxi_train_df.col("tolls_amount"))
            .drop(taxi_train_df.col("total_amount")).drop(taxi_train_df.col("tip_class"))
            .filter("passenger_count > 0 and passenger_count < 8 AND payment_type in ('CSH', 'CRD') AND tip_amount >= 0 AND tip_amount < 30 AND fare_amount >= 1 AND fare_amount < 150 AND trip_distance > 0 AND trip_distance < 100 AND trip_time_in_secs > 30 AND trip_time_in_secs < 7200"));

    # CACHE AND MATERIALIZE THE CLEANED DATA FRAME IN MEMORY
    taxi_df_train_cleaned.cache()
    taxi_df_train_cleaned.count()

    # REGISTER THE DATA FRAME AS A TEMPORARY TABLE IN SQLCONTEXT
    taxi_df_train_cleaned.registerTempTable("taxi_train")

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**Saída:**

Tempo de execução da célula: 8 segundos.

### <a name="query-the-table-and-import-results-in-a-data-frame"></a>Consultar a tabela e importar os resultados em um quadro de dados
Em seguida, consulte a tabela de tarifas, os dados do passageiro e da gorjeta; filtre dados corrompidos e externos e imprima várias linhas.

    # QUERY THE DATA
    val sqlStatement = """
        SELECT fare_amount, passenger_count, tip_amount, tipped
        FROM taxi_train
        WHERE passenger_count > 0 AND passenger_count < 7
        AND fare_amount > 0 AND fare_amount < 200
        AND payment_type in ('CSH', 'CRD')
        AND tip_amount > 0 AND tip_amount < 25
    """
    val sqlResultsDF = sqlContext.sql(sqlStatement)

    # SHOW ONLY THE TOP THREE ROWS
    sqlResultsDF.show(3)

**Saída:**

| fare_amount | passenger_count | tip_amount | tipped |
| --- | --- | --- | --- |
|        13,5 |1.0 |2,9 |1.0 |
|        16,0 |2,0 |3.4 |1.0 |
|        10,5 |2,0 |1.0 |1.0 |

## <a name="data-exploration-and-visualization"></a>Visualização e exploração de dados
Depois de trazer os dados para o Spark, a próxima etapa no processo de Ciência de dados será obter uma compreensão mais profunda dos dados por meio de exploração e visualização. Nesta seção, você examinará os dados de táxi usando consultas SQL. Depois, importará os resultados em um quadro de dados a fim de criar gráficos com as variáveis de destino e os recursos potenciais para inspeção visual usando o recurso de visualização automática do Jupyter.

### <a name="use-local-and-sql-magic-to-plot-data"></a>Usar local e palavra mágica do SQL para criar gráficos com dados
Por padrão, a saída de qualquer snippet de código executado em um notebook Jupyter é disponibilizada no contexto da sessão que é persistida nos nós de trabalho. Se desejar salvar uma corrida nos nós de trabalho para cada cálculo e, se todos os dados necessários para o cálculo estiverem disponíveis localmente no nó do servidor do Jupyter (que é o nó de cabeçalho), você poderá usar a palavra mágica `%%local` para executar o snippet de código no servidor do Jupyter.

* **Palavra mágica do SQL** (`%%sql`). O kernel HDInsight Spark dá suporte a consultas do HiveQL fáceis e embutidas no SQLContext. O argumento (`-o VARIABLE_NAME`) persiste a saída da consulta SQL como um quadro de dados do Pandas no servidor do Jupyter. Isso significa que ele estará disponível no modo local.
* `%%local` **palavra mágica**. A palavra mágica `%%local` executa o código localmente no servidor do Jupyter, que é o nó de cabeçalho do cluster HDInsight. Normalmente, você usa a palavra mágica `%%local` em conjunto com a palavra mágica `%%sql` com o parâmetro `-o`. O parâmetro `-o` persistiria a saída da consulta SQL localmente e, em seguida, as palavras mágicas `%%local` disparariam o próximo conjunto de snippets de código para serem executados localmente na saída das consultas SQL que é persistida localmente.

### <a name="query-the-data-by-using-sql"></a>Consultar os dados usando SQL
Essa consulta recupera as corridas de táxi por valor da tarifa, contagem de passageiros e valor da gorjeta.

    # RUN THE SQL QUERY
    %%sql -q -o sqlResults
    SELECT fare_amount, passenger_count, tip_amount, tipped FROM taxi_train WHERE passenger_count > 0 AND passenger_count < 7 AND fare_amount > 0 AND fare_amount < 200 AND payment_type in ('CSH', 'CRD') AND tip_amount > 0 AND tip_amount < 25

No código a seguir, a palavra mágica `%%local` cria um quadro de dados locais, sqlResults. Você pode usar sqlResults para criar gráficos usando matplotlib.

> [!TIP]
> A palavra mágica local é usada várias vezes neste artigo. Se o conjunto de dados for grande, obtenha a amostra para criar um quadro de dados que possa se ajustar na memória local.
> 
> 

### <a name="plot-the-data"></a>Plotar os dados
Você poderá criar gráficos usando o código Python quando o quadro de dados estiver no contexto local como um quadro de dados Panda.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local

    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES.
    # CLICK THE TYPE OF PLOT TO GENERATE (LINE, AREA, BAR, ETC.)
    sqlResults


 O kernel do Spark visualiza automaticamente a saída das consultas SQL (HiveQL) depois que você executa o código. Você pode escolher entre vários tipos de visualizações:

* Tabela
* Pizza
* Linha
* Área
* Barra

Este é o código para criar gráficos com os dados:

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    import matplotlib.pyplot as plt
    %matplotlib inline

    # PLOT TIP BY PAYMENT TYPE AND PASSENGER COUNT
    ax1 = sqlResults[['tip_amount']].plot(kind='hist', bins=25, facecolor='lightblue')
    ax1.set_title('Tip amount distribution')
    ax1.set_xlabel('Tip Amount ($)')
    ax1.set_ylabel('Counts')
    plt.suptitle('')
    plt.show()

    # PLOT TIP BY PASSENGER COUNT
    ax2 = sqlResults.boxplot(column=['tip_amount'], by=['passenger_count'])
    ax2.set_title('Tip amount by Passenger count')
    ax2.set_xlabel('Passenger count')
    ax2.set_ylabel('Tip Amount ($)')
    plt.suptitle('')
    plt.show()

    # PLOT TIP AMOUNT BY FARE AMOUNT; SCALE POINTS BY PASSENGER COUNT
    ax = sqlResults.plot(kind='scatter', x= 'fare_amount', y = 'tip_amount', c='blue', alpha = 0.10, s=5*(sqlResults.passenger_count))
    ax.set_title('Tip amount by Fare amount')
    ax.set_xlabel('Fare Amount ($)')
    ax.set_ylabel('Tip Amount ($)')
    plt.axis([-2, 80, -2, 20])
    plt.show()


**Saída:**

![Histograma do valor da gorjeta](./media/scala-walkthrough/plot-tip-amount-histogram.png)

![Valor de gorjeta por contagem de passageiros](./media/scala-walkthrough/plot-tip-amount-by-passenger-count.png)

![Valor de gorjeta por valor de tarifa](./media/scala-walkthrough/plot-tip-amount-by-fare-amount.png)

## <a name="create-features-and-transform-features-and-then-prep-data-for-input-into-modeling-functions"></a>Criar recursos, transformar recursos e depois preparar os dados para entrada em funções de modelagem
Para as funções de modelagem baseadas em árvore no SparkML e na MLlib, você precisa preparar o destino e os recursos usando várias técnicas, como agrupamento, indexação, codificação one-hot e vetorização. Veja os procedimentos desta seção:

1. Criar um novo recurso **reunindo** horários em blocos de tempo de tráfego.
2. Aplicar **indexação e codificação one-hot** em recursos categóricos.
3. **Criar amostras e dividir o conjunto de dados** em frações de teste e treinamento.
4. **Especificar os recursos e a variável de treinamento**e depois criar RDDs (conjuntos de dados distribuídos e resilientes) de ponto de sinalização de entrada para treinamentos de codificação indexada e one-hot, ou quadros de dados.
5. **Categorizar e vetorizar recursos e destinos** automaticamente para uso como entradas para modelos de aprendizado de máquina.

### <a name="create-a-new-feature-by-binning-hours-into-traffic-time-buckets"></a>Criar um novo recurso reunindo horários em blocos de tempo de tráfego
Este código mostra como criar um novo recurso reunindo horários em blocos de tempo de tráfego e como armazenar em cache o quadro de dados resultante na memória. Quando RDDs e quadros de dados são usados repetidamente, o armazenamento em cache leva a tempos de execução melhores. Da mesma forma, você armazenará os RDDs e os quadros de dados em cache em vários estágios nos procedimentos finais.

    # CREATE FOUR BUCKETS FOR TRAFFIC TIMES
    val sqlStatement = """
        SELECT *,
        CASE
         WHEN (pickup_hour <= 6 OR pickup_hour >= 20) THEN "Night"
         WHEN (pickup_hour >= 7 AND pickup_hour <= 10) THEN "AMRush"
         WHEN (pickup_hour >= 11 AND pickup_hour <= 15) THEN "Afternoon"
         WHEN (pickup_hour >= 16 AND pickup_hour <= 19) THEN "PMRush"
        END as TrafficTimeBins
        FROM taxi_train
    """
    val taxi_df_train_with_newFeatures = sqlContext.sql(sqlStatement)

    # CACHE THE DATA FRAME IN MEMORY AND MATERIALIZE THE DATA FRAME IN MEMORY
    taxi_df_train_with_newFeatures.cache()
    taxi_df_train_with_newFeatures.count()


### <a name="indexing-and-one-hot-encoding-of-categorical-features"></a>Indexação e codificação one-hot de recursos categóricos
As funções de modelagem e previsão de MLlib exigem que recursos com dados de entrada categóricos sejam indexados ou codificados antes do uso. Esta seção mostra a você como indexar ou codificar recursos categóricos para entrada nas funções de modelagem.

Dependendo do modelo, você precisa indexá-lo ou codificá-lo de maneiras diferentes. Por exemplo, modelos de regressão logística e lineares exigem codificação one-hot. Por exemplo, um recurso com três categorias pode ser expandido em três colunas de recurso. Cada coluna conteria 0 ou 1, dependendo da categoria de uma observação. A MLlib fornece a função [OneHotEncoder](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) para executar a codificação one-hot. Esse codificador mapeia uma coluna de índices de rótulo para uma coluna de vetores binários com, no máximo, um valor único. Com essa codificação, os algoritmos que esperam recursos numéricos valiosos, como a regressão logística, são aplicados em recursos categóricos.

Aqui, você transforma apenas quatro variáveis para mostrar exemplos, que são cadeias de caracteres. Também é possível indexar outras variáveis, como dia da semana, representadas por valores numéricos, como variáveis categóricas.

Para indexação, use `StringIndexer()` e para codificação one-hot, use as funções `OneHotEncoder()` da MLlib. Aqui está o código para indexar e codificar recursos categóricos:

    # CREATE INDEXES AND ONE-HOT ENCODED VECTORS FOR SEVERAL CATEGORICAL FEATURES

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # INDEX AND ENCODE VENDOR_ID
    val stringIndexer = new StringIndexer().setInputCol("vendor_id").setOutputCol("vendorIndex").fit(taxi_df_train_with_newFeatures)
    val indexed = stringIndexer.transform(taxi_df_train_with_newFeatures)
    val encoder = new OneHotEncoder().setInputCol("vendorIndex").setOutputCol("vendorVec")
    val encoded1 = encoder.transform(indexed)

    # INDEX AND ENCODE RATE_CODE
    val stringIndexer = new StringIndexer().setInputCol("rate_code").setOutputCol("rateIndex").fit(encoded1)
    val indexed = stringIndexer.transform(encoded1)
    val encoder = new OneHotEncoder().setInputCol("rateIndex").setOutputCol("rateVec")
    val encoded2 = encoder.transform(indexed)

    # INDEX AND ENCODE PAYMENT_TYPE
    val stringIndexer = new StringIndexer().setInputCol("payment_type").setOutputCol("paymentIndex").fit(encoded2)
    val indexed = stringIndexer.transform(encoded2)
    val encoder = new OneHotEncoder().setInputCol("paymentIndex").setOutputCol("paymentVec")
    val encoded3 = encoder.transform(indexed)

    # INDEX AND TRAFFIC TIME BINS
    val stringIndexer = new StringIndexer().setInputCol("TrafficTimeBins").setOutputCol("TrafficTimeBinsIndex").fit(encoded3)
    val indexed = stringIndexer.transform(encoded3)
    val encoder = new OneHotEncoder().setInputCol("TrafficTimeBinsIndex").setOutputCol("TrafficTimeBinsVec")
    val encodedFinal = encoder.transform(indexed)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**Saída:**

Tempo de execução da célula: 4 segundos.

### <a name="sample-and-split-the-data-set-into-training-and-test-fractions"></a>Criar amostras e dividir o conjunto de dados em frações de teste e treinamento
Esse código cria uma amostragem aleatória dos dados (25% neste exemplo). Embora a amostragem não seja necessário para este exemplo, devido ao tamanho do conjunto de dados, o artigo demonstra como é possível obter amostras para que você saiba como usá-lo para seu próprio problema, quando for necessário. Quando as amostras são grandes, isso pode economizar um tempo significativo durante o treinamento de modelos. Em seguida, dividimos o exemplo em uma parte de treinamento (75% neste exemplo) e uma parte de teste (25% neste exemplo) para usar na classificação e na modelagem de regressão.

Adicione um número aleatório (entre 0 e 1) à linha de alcance (em uma coluna "rand") que pode ser usado para selecionar partições de validação cruzada durante o treinamento.

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # SPECIFY SAMPLING AND SPLITTING FRACTIONS
    val samplingFraction = 0.25;
    val trainingFraction = 0.75;
    val testingFraction = (1-trainingFraction);
    val seed = 1234;
    val encodedFinalSampledTmp = encodedFinal.sample(withReplacement = false, fraction = samplingFraction, seed = seed)
    val sampledDFcount = encodedFinalSampledTmp.count().toInt

    val generateRandomDouble = udf(() => {
        scala.util.Random.nextDouble
    })

    # ADD A RANDOM NUMBER FOR CROSS-VALIDATION
    val encodedFinalSampled = encodedFinalSampledTmp.withColumn("rand", generateRandomDouble());

    # SPLIT THE SAMPLED DATA FRAME INTO TRAIN AND TEST, WITH A RANDOM COLUMN ADDED FOR DOING CROSS-VALIDATION (SHOWN LATER)
    # INCLUDE A RANDOM COLUMN FOR CREATING CROSS-VALIDATION FOLDS
    val splits = encodedFinalSampled.randomSplit(Array(trainingFraction, testingFraction), seed = seed)
    val trainData = splits(0)
    val testData = splits(1)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**Saída:**

Tempo de execução da célula: 2 segundos.

### <a name="specify-training-variable-and-features-and-then-create-indexed-or-one-hot-encoded-training-and-testing-input-labeled-point-rdds-or-data-frames"></a>Especificar os recursos e a variável de treinamento, bem como criar treinamentos de codificação indexada e one-hot e entrada de testes denominada RDDs pontuais ou quadros de dados
Esta seção contém código que mostra como indexar dados de texto categóricos como um tipo de dados de ponto rotulado e codificá-lo para que ele possa ser usado para treinar e testar a regressão logística de MLlib e outros modelos de classificação. Objetos de ponto rotulados são RDDs formatados de uma maneira para que sejam exigidos como dados de entrada pela maioria dos algoritmos de aprendizado de máquina na MLlib. Um [ponto rotulado](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) é um vetor local, denso ou esparso, associado a um rótulo/resposta.

Nesse código, você pode especificar a variável de destino (dependente) e os recursos a serem usados para treinar modelos. Depois, você cria treinamentos de codificação indexada e one-hot e RDDs de ponto de entrada de teste ou quadros de dados.

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # MAP NAMES OF FEATURES AND TARGETS FOR CLASSIFICATION AND REGRESSION PROBLEMS
    val featuresIndOneHot = List("paymentVec", "vendorVec", "rateVec", "TrafficTimeBinsVec", "pickup_hour", "weekday", "passenger_count", "trip_time_in_secs", "trip_distance", "fare_amount").map(encodedFinalSampled.columns.indexOf(_))
    val featuresIndIndex = List("paymentIndex", "vendorIndex", "rateIndex", "TrafficTimeBinsIndex", "pickup_hour", "weekday", "passenger_count", "trip_time_in_secs", "trip_distance", "fare_amount").map(encodedFinalSampled.columns.indexOf(_))

    # SPECIFY THE TARGET FOR CLASSIFICATION ('tipped') AND REGRESSION ('tip_amount') PROBLEMS
    val targetIndBinary = List("tipped").map(encodedFinalSampled.columns.indexOf(_))
    val targetIndRegression = List("tip_amount").map(encodedFinalSampled.columns.indexOf(_))

    # CREATE INDEXED LABELED POINT RDD OBJECTS
    val indexedTRAINbinary = trainData.rdd.map(r => LabeledPoint(r.getDouble(targetIndBinary(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
    val indexedTESTbinary = testData.rdd.map(r => LabeledPoint(r.getDouble(targetIndBinary(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
    val indexedTRAINreg = trainData.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
    val indexedTESTreg = testData.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))

    # CREATE INDEXED DATA FRAMES THAT YOU CAN USE TO TRAIN BY USING SPARK ML FUNCTIONS
    val indexedTRAINbinaryDF = indexedTRAINbinary.toDF()
    val indexedTESTbinaryDF = indexedTESTbinary.toDF()
    val indexedTRAINregDF = indexedTRAINreg.toDF()
    val indexedTESTregDF = indexedTESTreg.toDF()

    # CREATE ONE-HOT ENCODED (VECTORIZED) DATA FRAMES THAT YOU CAN USE TO TRAIN BY USING SPARK ML FUNCTIONS
    val assemblerOneHot = new VectorAssembler().setInputCols(Array("paymentVec", "vendorVec", "rateVec", "TrafficTimeBinsVec", "pickup_hour", "weekday", "passenger_count", "trip_time_in_secs", "trip_distance", "fare_amount")).setOutputCol("features")
    val OneHotTRAIN = assemblerOneHot.transform(trainData)
    val OneHotTEST = assemblerOneHot.transform(testData)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**Saída:**

Tempo de execução da célula: 4 segundos.

### <a name="automatically-categorize-and-vectorize-features-and-targets-to-use-as-inputs-for-machine-learning-models"></a>Categorizar e vetorizar recursos e destinos automaticamente para uso como entradas para modelos de aprendizado de máquina
Use o AM do Spark para categorize o destino e os recursos para uso em funções de modelagem baseadas em árvore. O código conclui duas tarefas:

* Cria um destino binário para classificação atribuindo um valor de 0 ou 1 para cada ponto de dados entre 0 e 1 usando um valor de limite de 0,5.
* Categoriza automaticamente os recursos. Se o número de valores numéricos distintos para qualquer um dos recursos for menor do que 32, esse recurso será categorizado.

Este é o código para essas duas tarefas.

    # CATEGORIZE FEATURES AND BINARIZE THE TARGET FOR THE BINARY CLASSIFICATION PROBLEM

    # TRAIN DATA
    val indexer = new VectorIndexer().setInputCol("features").setOutputCol("featuresCat").setMaxCategories(32)
    val indexerModel = indexer.fit(indexedTRAINbinaryDF)
    val indexedTrainwithCatFeat = indexerModel.transform(indexedTRAINbinaryDF)
    val binarizer: Binarizer = new Binarizer().setInputCol("label").setOutputCol("labelBin").setThreshold(0.5)
    val indexedTRAINwithCatFeatBinTarget = binarizer.transform(indexedTrainwithCatFeat)

    # TEST DATA
    val indexerModel = indexer.fit(indexedTESTbinaryDF)
    val indexedTrainwithCatFeat = indexerModel.transform(indexedTESTbinaryDF)
    val binarizer: Binarizer = new Binarizer().setInputCol("label").setOutputCol("labelBin").setThreshold(0.5)
    val indexedTESTwithCatFeatBinTarget = binarizer.transform(indexedTrainwithCatFeat)

    # CATEGORIZE FEATURES FOR THE REGRESSION PROBLEM
    # CREATE PROPERLY INDEXED AND CATEGORIZED DATA FRAMES FOR TREE-BASED MODELS

    # TRAIN DATA
    val indexer = new VectorIndexer().setInputCol("features").setOutputCol("featuresCat").setMaxCategories(32)
    val indexerModel = indexer.fit(indexedTRAINregDF)
    val indexedTRAINwithCatFeat = indexerModel.transform(indexedTRAINregDF)

    # TEST DATA
    val indexerModel = indexer.fit(indexedTESTbinaryDF)
    val indexedTESTwithCatFeat = indexerModel.transform(indexedTESTregDF)



## <a name="binary-classification-model-predict-whether-a-tip-should-be-paid"></a>Modelo de classificação binária: Prever se uma gorjeta deve ser paga
Nesta seção, você cria três tipos de modelo de classificação binária para prever se uma gorjeta será paga ou não:

* Um **modelo de regressão logística`LogisticRegression()` usando a função**  do ML do Spark
* Um **modelo de classificação de floresta aleatória** usando a função `RandomForestClassifier()` da AM do Spark
* Um **modelo de classificação de árvore de aumento gradiente** usando a função `GradientBoostedTrees()` da MLlib

### <a name="create-a-logistic-regression-model"></a>Criar um modelo de regressão logística
Em seguida, crie um modelo de regressão logística usando a função `LogisticRegression()` da AM do Spark. Crie o código de compilação de modelo usando uma série de etapas:

1. **Treinar os dados do modelo** com um conjunto de parâmetros.
2. **Avaliar o modelo** em um conjunto de dados de teste com métricas.
3. **Salvar modelo** no armazenamento de Blobs para consumo futuro.
4. **Pontuar o modelo** em relação aos dados de teste.
5. **Plotar os resultados** com curvas ROC (característica de operação de recepção).

Veja o código para esses procedimentos:

    # CREATE A LOGISTIC REGRESSION MODEL
    val lr = new LogisticRegression().setLabelCol("tipped").setFeaturesCol("features").setMaxIter(10).setRegParam(0.3).setElasticNetParam(0.8)
    val lrModel = lr.fit(OneHotTRAIN)

    # PREDICT ON THE TEST DATA SET
    val predictions = lrModel.transform(OneHotTEST)

    # SELECT `BinaryClassificationEvaluator()` TO COMPUTE THE TEST ERROR
    val evaluator = new BinaryClassificationEvaluator().setLabelCol("tipped").setRawPredictionCol("probability").setMetricName("areaUnderROC")
    val ROC = evaluator.evaluate(predictions)
    println("ROC on test data = " + ROC)

    # SAVE THE MODEL
    val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
    val modelName = "LogisticRegression__"
    val filename = modelDir.concat(modelName).concat(datestamp)
    lrModel.save(filename);

Carregue, pontue e salve os resultados.

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # LOAD THE SAVED MODEL AND SCORE THE TEST DATA SET
    val savedModel = org.apache.spark.ml.classification.LogisticRegressionModel.load(filename)
    println(s"Coefficients: ${savedModel.coefficients} Intercept: ${savedModel.intercept}")

    # SCORE THE MODEL ON THE TEST DATA
    val predictions = savedModel.transform(OneHotTEST).select("tipped","probability","rawPrediction")
    predictions.registerTempTable("testResults")

    # SELECT `BinaryClassificationEvaluator()` TO COMPUTE THE TEST ERROR
    val evaluator = new BinaryClassificationEvaluator().setLabelCol("tipped").setRawPredictionCol("probability").setMetricName("areaUnderROC")
    val ROC = evaluator.evaluate(predictions)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");

    # PRINT THE ROC RESULTS
    println("ROC on test data = " + ROC)


**Saída:**

ROC nos dados de teste = 0,9827381497557599

Use o Python em quadros de dados locais Panda para criar gráficos com a curva ROC.

    # QUERY THE RESULTS
    %%sql -q -o sqlResults
    SELECT tipped, probability from testResults


    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    %matplotlib inline
    from sklearn.metrics import roc_curve,auc

    sqlResults['probFloat'] = sqlResults.apply(lambda row: row['probability'].values()[0][1], axis=1)
    predictions_pddf = sqlResults[["tipped","probFloat"]]

    # PREDICT THE ROC CURVE
    # predictions_pddf = sqlResults.rename(columns={'_1': 'probability', 'tipped': 'label'})
    prob = predictions_pddf["probFloat"]
    fpr, tpr, thresholds = roc_curve(predictions_pddf['tipped'], prob, pos_label=1);
    roc_auc = auc(fpr, tpr)

    # PLOT THE ROC CURVE
    plt.figure(figsize=(5,5))
    plt.plot(fpr, tpr, label='ROC curve (area = %0.2f)' % roc_auc)
    plt.plot([0, 1], [0, 1], 'k--')
    plt.xlim([0.0, 1.0])
    plt.ylim([0.0, 1.05])
    plt.xlabel('False Positive Rate')
    plt.ylabel('True Positive Rate')
    plt.title('ROC Curve')
    plt.legend(loc="lower right")
    plt.show()


**Saída:**

![Curva ROC de gorjeta ou não](./media/scala-walkthrough/plot-roc-curve-tip-or-not.png)

### <a name="create-a-random-forest-classification-model"></a>Criar um modelo de classificação de floresta aleatória
Em seguida, criamos um modelo de classificação de floresta aleatória usando a função `RandomForestClassifier()` da AM do Spark e avaliamos o modelo nos dados de teste.

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # CREATE THE RANDOM FOREST CLASSIFIER MODEL
    val rf = new RandomForestClassifier().setLabelCol("labelBin").setFeaturesCol("featuresCat").setNumTrees(10).setSeed(1234)

    # FIT THE MODEL
    val rfModel = rf.fit(indexedTRAINwithCatFeatBinTarget)
    val predictions = rfModel.transform(indexedTESTwithCatFeatBinTarget)

    # EVALUATE THE MODEL
    val evaluator = new MulticlassClassificationEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("f1")
    val Test_f1Score = evaluator.evaluate(predictions)
    println("F1 score on test data: " + Test_f1Score);

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");

    # CALCULATE BINARY CLASSIFICATION EVALUATION METRICS
    val evaluator = new BinaryClassificationEvaluator().setLabelCol("label").setRawPredictionCol("probability").setMetricName("areaUnderROC")
    val ROC = evaluator.evaluate(predictions)
    println("ROC on test data = " + ROC)


**Saída:**

ROC nos dados de teste = 0,9847103571552683

### <a name="create-a-gbt-classification-model"></a>Criar um modelo de classificação GBT
Em seguida, criamos um modelo de classificação GBT usando a função `GradientBoostedTrees()` da MLlib e avaliamos o modelo nos dados de teste.

    # TRAIN A GBT CLASSIFICATION MODEL BY USING MLLIB AND A LABELED POINT

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # DEFINE THE GBT CLASSIFICATION MODEL
    val boostingStrategy = BoostingStrategy.defaultParams("Classification")
    boostingStrategy.numIterations = 20
    boostingStrategy.treeStrategy.numClasses = 2
    boostingStrategy.treeStrategy.maxDepth = 5
    boostingStrategy.treeStrategy.categoricalFeaturesInfo = Map[Int, Int]((0,2),(1,2),(2,6),(3,4))

    # TRAIN THE MODEL
    val gbtModel = GradientBoostedTrees.train(indexedTRAINbinary, boostingStrategy)

    # SAVE THE MODEL IN BLOB STORAGE
    val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
    val modelName = "GBT_Classification__"
    val filename = modelDir.concat(modelName).concat(datestamp)
    gbtModel.save(sc, filename);

    # EVALUATE THE MODEL ON TEST INSTANCES AND THE COMPUTE TEST ERROR
    val labelAndPreds = indexedTESTbinary.map { point =>
      val prediction = gbtModel.predict(point.features)
      (point.label, prediction)
    }
    val testErr = labelAndPreds.filter(r => r._1 != r._2).count.toDouble / indexedTRAINbinary.count()
    //println("Learned classification GBT model:\n" + gbtModel.toDebugString)
    println("Test Error = " + testErr)

    # USE BINARY AND MULTICLASS METRICS TO EVALUATE THE MODEL ON THE TEST DATA
    val metrics = new MulticlassMetrics(labelAndPreds)
    println(s"Precision: ${metrics.precision}")
    println(s"Recall: ${metrics.recall}")
    println(s"F1 Score: ${metrics.fMeasure}")

    val metrics = new BinaryClassificationMetrics(labelAndPreds)
    println(s"Area under PR curve: ${metrics.areaUnderPR}")
    println(s"Area under ROC curve: ${metrics.areaUnderROC}")

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");

    # PRINT THE ROC METRIC
    println(s"Area under ROC curve: ${metrics.areaUnderROC}")


**Saída:**

Área sob a curva ROC: 0,9846895479241554

## <a name="regression-model-predict-tip-amount"></a>Modelo de regressão: Prever o valor da gorjeta
Nesta seção, você criará dois tipos de modelo de regressão para prever o valor da gorjeta:

* Um **modelo de regressão linear regularizado** usando a função `LinearRegression()` da AM do Spark. Você salvará o modelo e avaliará o modelo nos dados de teste.
* Um **modelo de regressão de árvore de aumento gradiente** usando a função `GBTRegressor()` da AM do Spark.

### <a name="create-a-regularized-linear-regression-model"></a>Criar um modelo de regressão linear regularizada
    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # CREATE A REGULARIZED LINEAR REGRESSION MODEL BY USING THE SPARK ML FUNCTION AND DATA FRAMES
    val lr = new LinearRegression().setLabelCol("tip_amount").setFeaturesCol("features").setMaxIter(10).setRegParam(0.3).setElasticNetParam(0.8)

    # FIT THE MODEL BY USING DATA FRAMES
    val lrModel = lr.fit(OneHotTRAIN)
    println(s"Coefficients: ${lrModel.coefficients} Intercept: ${lrModel.intercept}")

    # SUMMARIZE THE MODEL OVER THE TRAINING SET AND PRINT METRICS
    val trainingSummary = lrModel.summary
    println(s"numIterations: ${trainingSummary.totalIterations}")
    println(s"objectiveHistory: ${trainingSummary.objectiveHistory.toList}")
    trainingSummary.residuals.show()
    println(s"RMSE: ${trainingSummary.rootMeanSquaredError}")
    println(s"r2: ${trainingSummary.r2}")

    # SAVE THE MODEL IN AZURE BLOB STORAGE
    val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
    val modelName = "LinearRegression__"
    val filename = modelDir.concat(modelName).concat(datestamp)
    lrModel.save(filename);

    # PRINT THE COEFFICIENTS
    println(s"Coefficients: ${lrModel.coefficients} Intercept: ${lrModel.intercept}")

    # SCORE THE MODEL ON TEST DATA
    val predictions = lrModel.transform(OneHotTEST)

    # EVALUATE THE MODEL ON TEST DATA
    val evaluator = new RegressionEvaluator().setLabelCol("tip_amount").setPredictionCol("prediction").setMetricName("r2")
    val r2 = evaluator.evaluate(predictions)
    println("R-sqr on test data = " + r2)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**Saída:**

Tempo de execução da célula: 13 segundos.

    # LOAD A SAVED LINEAR REGRESSION MODEL FROM BLOB STORAGE AND SCORE A TEST DATA SET

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # LOAD A SAVED LINEAR REGRESSION MODEL FROM AZURE BLOB STORAGE
    val savedModel = org.apache.spark.ml.regression.LinearRegressionModel.load(filename)
    println(s"Coefficients: ${savedModel.coefficients} Intercept: ${savedModel.intercept}")

    # SCORE THE MODEL ON TEST DATA
    val predictions = savedModel.transform(OneHotTEST).select("tip_amount","prediction")
    predictions.registerTempTable("testResults")

    # EVALUATE THE MODEL ON TEST DATA
    val evaluator = new RegressionEvaluator().setLabelCol("tip_amount").setPredictionCol("prediction").setMetricName("r2")
    val r2 = evaluator.evaluate(predictions)
    println("R-sqr on test data = " + r2)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");

    # PRINT THE RESULTS
    println("R-sqr on test data = " + r2)


**Saída:**

R-sqr nos dados de teste = 0,5960320470835743

Em seguida, consulte os resultados do teste como um quadro de dados e use AutoVizWidget e matplotlib para visualizá-los.

    # RUN A SQL QUERY
    %%sql -q -o sqlResults
    select * from testResults

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local

    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES
    # CLICK THE TYPE OF PLOT TO GENERATE (LINE, AREA, BAR, AND SO ON)
    sqlResults

O código cria um quadro de dados local da saída da consulta e cria gráficos com os dados. A palavra mágica `%%local` cria um quadro de dados local, `sqlResults`, que pode ser usado para criar gráficos com matplotlib.

> [!NOTE]
> Essa palavra mágica do Spark será usada várias vezes neste artigo. Se a quantidade de dados for grande, você deverá obter uma amostra para criar um quadro de dados que se ajuste à memória local.
> 
> 

Crie gráficos usando matplotlib do Python.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    sqlResults
    %matplotlib inline
    import numpy as np

    # PLOT THE RESULTS
    ax = sqlResults.plot(kind='scatter', figsize = (6,6), x='tip_amount', y='prediction', color='blue', alpha = 0.25, label='Actual vs. predicted');
    fit = np.polyfit(sqlResults['tip_amount'], sqlResults['prediction'], deg=1)
    ax.set_title('Actual vs. Predicted Tip Amounts ($)')
    ax.set_xlabel("Actual")
    ax.set_ylabel("Predicted")
    #ax.plot(sqlResults['tip_amount'], fit[0] * sqlResults['prediction'] + fit[1], color='magenta')
    plt.axis([-1, 15, -1, 8])
    plt.show(ax)

**Saída:**

![Valor da gorjeta: real vs. previsto](./media/scala-walkthrough/plot-actual-vs-predicted-tip-amount.png)

### <a name="create-a-gbt-regression-model"></a>Criar um modelo de regressão GBT
Crie um modelo de regressão GBT usando a função `GBTRegressor()` da AM do Spark e avalie o modelo nos dados de teste.

[GBTs (árvores com aumento gradiente)](https://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) são conjuntos de árvores de decisão. As GBTs treinam árvores de decisão iterativamente para minimizar uma função de perda. Você pode usar GBTs para regressão e classificação. Elas podem manipular recursos categóricos, não exigem o dimensionamento de recursos e são capazes de capturar não linearidades e interações de recursos. Use-as também em uma configuração de classificação multiclasse.

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # TRAIN A GBT REGRESSION MODEL
    val gbt = new GBTRegressor().setLabelCol("label").setFeaturesCol("featuresCat").setMaxIter(10)
    val gbtModel = gbt.fit(indexedTRAINwithCatFeat)

    # MAKE PREDICTIONS
    val predictions = gbtModel.transform(indexedTESTwithCatFeat)

    # COMPUTE TEST SET R2
    val evaluator = new RegressionEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("r2")
    val Test_R2 = evaluator.evaluate(predictions)


    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");

    # PRINT THE RESULTS
    println("Test R-sqr is: " + Test_R2);


**Saída:**

R-sqr do teste é: 0,7655383534596654

## <a name="advanced-modeling-utilities-for-optimization"></a>Utilitários de modelagem avançada para otimização
Nesta seção, use utilitários de aprendizado de máquina que os desenvolvedores usam frequentemente para otimização do modelo. Especificamente, você pode otimizar os modelos de AM de três maneiras diferentes usando a limpeza de parâmetro e a validação cruzada:

* Dividir os dados em conjuntos de treinamento e validação, otimizar o modelo usando a limpeza de hiperparâmetro em um conjunto de treinamento e avaliar um conjunto de validação (regressão linear)
* Otimizar o modelo usando a validação cruzada e a limpeza de hiperparâmetro com a função CrossValidator do SparkML (classificação binária)
* Otimizar o modelo usando o código personalizado de validação cruzada e limpeza de parâmetro para utilizar qualquer função de aprendizado de máquina e o conjunto de parâmetros (regressão linear)

**Validação cruzada** é uma técnica que avalia quão bem um modelo treinado em um conjunto de dados conhecido será generalizado para prever os recursos dos conjuntos de dados nos quais ele não foi treinado. A ideia geral por trás dessa técnica é que um modelo é treinado em um conjunto de dados conhecidos em e, em seguida, a precisão de suas previsões é testada em relação a um conjunto de dados independente. Uma implementação comum é dividir um conjunto de dados em partições *k*e, em seguida, treinar o modelo em um estilo round-robin em todas, exceto uma das partições.

**Otimização do hiperparâmetro** é o problema de escolher um conjunto de hiperparâmetros para um algoritmo de aprendizado, normalmente com o objetivo de otimizar uma medida de desempenho do algoritmo em um conjunto de dados independente. Um hiperparâmetro é um valor que você deve especificar fora do procedimento de treinamento do modelo. As suposições sobre esses valores de hiperparâmetro podem afetar a flexibilidade e a precisão do modelo. As árvores de decisão têm hiperparâmetros, por exemplo, como a profundidade desejada e o número de folhas na árvore. Configure de um termo de penalidade de classificação incorreta para uma SVM (máquina de vetor de suporte).

Uma maneira comum de executar a otimização de hiperparâmetro é uma pesquisa de grade, também chamada de **parâmetro de limpeza**. Em uma pesquisa de grade, uma pesquisa detalhada é executada nos valores de um subconjunto especificado do espaço do hiperparâmetro para um algoritmo de aprendizado. A validação cruzada pode fornecer uma métrica de desempenho para classificar os resultados ideais produzidos pelo algoritmo de pesquisa de grade. Se você usar a limpeza de hiperparâmetro para validação cruzada, poderá ajudar a limitar problemas como sobreajuste de um modelo aos dados de treinamento. Dessa forma, o modelo retém a capacidade de aplicar ao conjunto geral de dados do qual os dados de treinamento foram extraídos.

### <a name="optimize-a-linear-regression-model-with-hyper-parameter-sweeping"></a>Otimizar um modelo de regressão linear com a limpeza de hiperparâmetro
Em seguida, divida os dados em conjuntos de treinamento e validação, use a limpeza de hiperparâmetro em um conjunto de treinamento para otimizar o modelo e avalie um conjunto de validação (regressão linear).

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # RENAME `tip_amount` AS A LABEL
    val OneHotTRAINLabeled = OneHotTRAIN.select("tip_amount","features").withColumnRenamed(existingName="tip_amount",newName="label")
    val OneHotTESTLabeled = OneHotTEST.select("tip_amount","features").withColumnRenamed(existingName="tip_amount",newName="label")
    OneHotTRAINLabeled.cache()
    OneHotTESTLabeled.cache()

    # DEFINE THE ESTIMATOR FUNCTION: `THE LinearRegression()` FUNCTION
    val lr = new LinearRegression().setLabelCol("label").setFeaturesCol("features").setMaxIter(10)

    # DEFINE THE PARAMETER GRID
    val paramGrid = new ParamGridBuilder().addGrid(lr.regParam, Array(0.1, 0.01, 0.001)).addGrid(lr.fitIntercept).addGrid(lr.elasticNetParam, Array(0.1, 0.5, 0.9)).build()

    # DEFINE THE PIPELINE WITH A TRAIN/TEST VALIDATION SPLIT (75% IN THE TRAINING SET), AND THEN THE SPECIFY ESTIMATOR, EVALUATOR, AND PARAMETER GRID
    val trainPct = 0.75
    val trainValidationSplit = new TrainValidationSplit().setEstimator(lr).setEvaluator(new RegressionEvaluator).setEstimatorParamMaps(paramGrid).setTrainRatio(trainPct)

    # RUN THE TRAIN VALIDATION SPLIT AND CHOOSE THE BEST SET OF PARAMETERS
    val model = trainValidationSplit.fit(OneHotTRAINLabeled)

    # MAKE PREDICTIONS ON THE TEST DATA BY USING THE MODEL WITH THE COMBINATION OF PARAMETERS THAT PERFORMS THE BEST
    val testResults = model.transform(OneHotTESTLabeled).select("label", "prediction")

    # COMPUTE TEST SET R2
    val evaluator = new RegressionEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("r2")
    val Test_R2 = evaluator.evaluate(testResults)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");

    println("Test R-sqr is: " + Test_R2);


**Saída:**

R-sqr do teste é: 0,6226484708501209

### <a name="optimize-the-binary-classification-model-by-using-cross-validation-and-hyper-parameter-sweeping"></a>Otimizar o modelo de classificação binária usando a limpeza de hiperparâmetro e a validação cruzada
Esta seção mostra como otimizar um modelo de classificação binária usando a limpeza de hiperparâmetro e a validação cruzada. Isso usa a função `CrossValidator` de AM do Spark.

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # CREATE DATA FRAMES WITH PROPERLY LABELED COLUMNS TO USE WITH THE TRAIN AND TEST SPLIT
    val indexedTRAINwithCatFeatBinTargetRF = indexedTRAINwithCatFeatBinTarget.select("labelBin","featuresCat").withColumnRenamed(existingName="labelBin",newName="label").withColumnRenamed(existingName="featuresCat",newName="features")
    val indexedTESTwithCatFeatBinTargetRF = indexedTESTwithCatFeatBinTarget.select("labelBin","featuresCat").withColumnRenamed(existingName="labelBin",newName="label").withColumnRenamed(existingName="featuresCat",newName="features")
    indexedTRAINwithCatFeatBinTargetRF.cache()
    indexedTESTwithCatFeatBinTargetRF.cache()

    # DEFINE THE ESTIMATOR FUNCTION
    val rf = new RandomForestClassifier().setLabelCol("label").setFeaturesCol("features").setImpurity("gini").setSeed(1234).setFeatureSubsetStrategy("auto").setMaxBins(32)

    # DEFINE THE PARAMETER GRID
    val paramGrid = new ParamGridBuilder().addGrid(rf.maxDepth, Array(4,8)).addGrid(rf.numTrees, Array(5,10)).addGrid(rf.minInstancesPerNode, Array(100,300)).build()

    # SPECIFY THE NUMBER OF FOLDS
    val numFolds = 3

    # DEFINE THE TRAIN/TEST VALIDATION SPLIT (75% IN THE TRAINING SET)
    val CrossValidator = new CrossValidator().setEstimator(rf).setEvaluator(new BinaryClassificationEvaluator).setEstimatorParamMaps(paramGrid).setNumFolds(numFolds)

    # RUN THE TRAIN VALIDATION SPLIT AND CHOOSE THE BEST SET OF PARAMETERS
    val model = CrossValidator.fit(indexedTRAINwithCatFeatBinTargetRF)

    # MAKE PREDICTIONS ON THE TEST DATA BY USING THE MODEL WITH THE COMBINATION OF PARAMETERS THAT PERFORMS THE BEST
    val testResults = model.transform(indexedTESTwithCatFeatBinTargetRF).select("label", "prediction")

    # COMPUTE THE TEST F1 SCORE
    val evaluator = new MulticlassClassificationEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("f1")
    val Test_f1Score = evaluator.evaluate(testResults)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**Saída:**

Tempo de execução da célula: 33 segundos.

### <a name="optimize-the-linear-regression-model-by-using-custom-cross-validation-and-parameter-sweeping-code"></a>Otimizar o modelo de regressão linear usando código personalizado de validação cruzada e limpeza de parâmetro
Em seguida, otimize o modelo usando código personalizado e identifique os melhores parâmetros do modelo usando o critério de precisão mais alta. Em seguida, criamos o modelo final, avaliamos o modelo nos dados de teste e salvamos o modelo no armazenamento de Blobs. Por fim, carregue o modelo, pontue os dados de teste e avalie sua precisão.

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # DEFINE THE PARAMETER GRID AND THE NUMBER OF FOLDS
    val paramGrid = new ParamGridBuilder().addGrid(rf.maxDepth, Array(5,10)).addGrid(rf.numTrees, Array(10,25,50)).build()

    val nFolds = 3
    val numModels = paramGrid.size
    val numParamsinGrid = 2

    # SPECIFY THE NUMBER OF CATEGORIES FOR CATEGORICAL VARIABLES
    val categoricalFeaturesInfo = Map[Int, Int]((0,2),(1,2),(2,6),(3,4))

    var maxDepth = -1
    var numTrees = -1
    var param = ""
    var paramval = -1
    var validateLB = -1.0
    var validateUB = -1.0
    val h = 1.0 / nFolds;
    val RMSE  = Array.fill(numModels)(0.0)

    # CREATE K-FOLDS
    val splits = MLUtils.kFold(indexedTRAINbinary, numFolds = nFolds, seed=1234)


    # LOOP THROUGH K-FOLDS AND THE PARAMETER GRID TO GET AND IDENTIFY THE BEST PARAMETER SET BY LEVEL OF ACCURACY
    for (i <- 0 to (nFolds-1)) {
        validateLB = i * h
        validateUB = (i + 1) * h
        val validationCV = trainData.filter($"rand" >= validateLB  && $"rand" < validateUB)
        val trainCV = trainData.filter($"rand" < validateLB  || $"rand" >= validateUB)
        val validationLabPt = validationCV.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)));
        val trainCVLabPt = trainCV.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)));
        validationLabPt.cache()
        trainCVLabPt.cache()

        for (nParamSets <- 0 to (numModels-1)) {
            for (nParams <- 0 to (numParamsinGrid-1)) {
                param = paramGrid(nParamSets).toSeq(nParams).param.toString.split("__")(1)
                paramval = paramGrid(nParamSets).toSeq(nParams).value.toString.toInt
                if (param == "maxDepth") {maxDepth = paramval}
                if (param == "numTrees") {numTrees = paramval}
            }
            val rfModel = RandomForest.trainRegressor(trainCVLabPt, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                                      numTrees=numTrees, maxDepth=maxDepth,
                                                      featureSubsetStrategy="auto",impurity="variance", maxBins=32)
            val labelAndPreds = validationLabPt.map { point =>
                                                     val prediction = rfModel.predict(point.features)
                                                     ( prediction, point.label )
                                                    }
            val validMetrics = new RegressionMetrics(labelAndPreds)
            val rmse = validMetrics.rootMeanSquaredError
            RMSE(nParamSets) += rmse
        }
        validationLabPt.unpersist();
        trainCVLabPt.unpersist();
    }
    val minRMSEindex = RMSE.indexOf(RMSE.min)

    # GET THE BEST PARAMETERS FROM A CROSS-VALIDATION AND PARAMETER SWEEP
    var best_maxDepth = -1
    var best_numTrees = -1
    for (nParams <- 0 to (numParamsinGrid-1)) {
        param = paramGrid(minRMSEindex).toSeq(nParams).param.toString.split("__")(1)
        paramval = paramGrid(minRMSEindex).toSeq(nParams).value.toString.toInt
        if (param == "maxDepth") {best_maxDepth = paramval}
        if (param == "numTrees") {best_numTrees = paramval}
    }

    # CREATE THE BEST MODEL WITH THE BEST PARAMETERS AND A FULL TRAINING DATA SET
    val best_rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                                      numTrees=best_numTrees, maxDepth=best_maxDepth,
                                                      featureSubsetStrategy="auto",impurity="variance", maxBins=32)

    # SAVE THE BEST RANDOM FOREST MODEL IN BLOB STORAGE
    val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
    val modelName = "BestCV_RF_Regression__"
    val filename = modelDir.concat(modelName).concat(datestamp)
    best_rfModel.save(sc, filename);

    # PREDICT ON THE TRAINING SET WITH THE BEST MODEL AND THEN EVALUATE
    val labelAndPreds = indexedTESTreg.map { point =>
                                            val prediction = best_rfModel.predict(point.features)
                                            ( prediction, point.label )
                                           }

    val test_rmse = new RegressionMetrics(labelAndPreds).rootMeanSquaredError
    val test_rsqr = new RegressionMetrics(labelAndPreds).r2

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


    # LOAD THE MODEL
    val savedRFModel = RandomForestModel.load(sc, filename)

    val labelAndPreds = indexedTESTreg.map { point =>
                                            val prediction = savedRFModel.predict(point.features)
                                            ( prediction, point.label )
                                           }
    # TEST THE MODEL
    val test_rmse = new RegressionMetrics(labelAndPreds).rootMeanSquaredError
    val test_rsqr = new RegressionMetrics(labelAndPreds).r2


**Saída:**

Tempo de execução da célula: 61 segundos.

## <a name="consume-spark-built-machine-learning-models-automatically-with-scala"></a>Consumir automaticamente modelos da aprendizado de máquina compilados no Spark com o Scala
Para obter uma visão geral dos tópicos que explicam as tarefas que compõem o processo de Ciência de dados no Azure, confira [Processo de Ciência de Dados de Equipe](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

[Passo a passo do processo de ciência de dados de equipe](walkthroughs.md) descreve outras orientações de ponta a ponta que demonstram as etapas no Processo de ciência de dados de equipe para cenários específicos. As orientações também mostram como combinar ferramentas e serviços de nuvem e locais em um fluxo de trabalho ou pipeline para criar um aplicativo inteligente.

[Pontuar modelos de aprendizado de máquina criados no Spark](spark-model-consumption.md) mostra como usar o código Scala para carregar e pontuar automaticamente novos conjuntos de dados com modelos de aprendizado de máquina compilados no Spark e salvos no armazenamento de Blobs do Azure. Você pode seguir as instruções fornecidas nele e simplesmente substituir o código Python pelo código Scala neste artigo para o consumo automatizado.

