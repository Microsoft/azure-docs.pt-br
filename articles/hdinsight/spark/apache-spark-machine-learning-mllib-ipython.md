---
title: Exemplo de aprendizado de máquina com MLlib do Spark no HDInsight – Azure
description: Saiba como usar o Spark MLlib para criar um aplicativo de aprendizado de máquina que analisa um conjunto de dados usando a classificação por meio de regressão logística.
keywords: aprendizado de máquina do spark, exemplo de aprendizado de máquina do spark
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 06/17/2019
ms.author: hrasheed
ms.openlocfilehash: bdc645bf8de95265158c3bb7ebf71952369e4ab2
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190904"
---
# <a name="use-apache-spark-mllib-to-build-a-machine-learning-application-and-analyze-a-dataset"></a>Use o Apache Spark MLlib para criar um aplicativo de aprendizado de máquina e analisar um conjunto de dados

Aprenda como usar o Apache Spark [MLlib](https://spark.apache.org/mllib/) para criar um aplicativo de aprendizado de máquina para fazer uma análise preditiva simples em um conjunto de dados aberto. Das bibliotecas aprendizado de máquina internas do Spark, este exemplo usa *classificação* por meio de regressão logística. 

MLlib é uma biblioteca Spark principal que fornece vários utilitários úteis para tarefas de aprendizado de máquina, incluindo utilitários adequados para:

* classificação
* Regressão
* Clustering
* Modelagem de tópico
* Decomposição de valor singular (SVD) e análise de componente principal (PCA)
* Teste de hipótese e cálculo de estatísticas de exemplo

## <a name="understand-classification-and-logistic-regression"></a>Compreender a classificação e regressão logística
*Classificação*, uma tarefa popular de aprendizado de máquina, é o processo de classificação de dados de entrada em categorias. É o trabalho de um algoritmo de classificação para descobrir como atribuir "rótulos" a dados de entrada que você fornece. Por exemplo, você pode pensar em um algoritmo de aprendizado de máquina que aceite informações sobre estoque como entrada e divida o estoque em duas categorias: estoque que você deve vender e estoque que deve ser mantido.

Regressão logística é o algoritmo usado para classificação. A API de regressão logística do Spark é útil para *classificação binária*ou para classificação de dados de entrada em um dos dois grupos. Para obter mais informações sobre a regressão logística, consulte [Wikipédia](https://en.wikipedia.org/wiki/Logistic_regression).

Em resumo, o processo de regressão logística produz uma *função logística* que pode ser usada para prever a probabilidade de um vetor de entrada pertencer a um grupo ou outro.  

## <a name="predictive-analysis-example-on-food-inspection-data"></a>Exemplo de análise preditiva em dados de inspeção de alimentos
Neste exemplo, você usa o Spark para executar uma análise preditiva sobre os dados de inspeção de alimentos (**Food_Inspections1.csv**) que foi adquirido por meio do [portal de dados da cidade de Chicago](https://data.cityofchicago.org/). Esse conjunto de dados contém informações sobre inspeções de estabelecimentos de alimentos realizadas em Chicago, incluindo informações sobre cada estabelecimento de alimentos inspecionado, as violações encontradas (se houver) e os resultados da inspeção. O arquivo de dados CSV já está disponível na conta de armazenamento associada ao cluster em **/HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections1.csv**.

Nas etapas a seguir, você desenvolverá um modelo para ver o que é necessário para ser aprovado ou reprovado em uma inspeção de alimentos.

## <a name="create-an-apache-spark-mllib-machine-learning-app"></a>Crie um aplicativo de aprendizado de máquina Apache Spark MLlib

1. Crie um bloco de notas do Jupyter usando o kernel PySpark. Para obter instruções, consulte [Criar um bloco de notas do Jupyter](./apache-spark-jupyter-spark-sql.md#create-a-jupyter-notebook).

2. Importe os tipos obrigatórios necessários para este aplicativo. Copie e cole o código a seguir em uma célula vazia e, em seguida, pressione **SHIFT + ENTER**.

    ```PySpark
    from pyspark.ml import Pipeline
    from pyspark.ml.classification import LogisticRegression
    from pyspark.ml.feature import HashingTF, Tokenizer
    from pyspark.sql import Row
    from pyspark.sql.functions import UserDefinedFunction
    from pyspark.sql.types import *
    ```
    Por causa do kernel PySpark, não será necessário criar nenhum contexto explicitamente. Os contextos do Spark e do Hive são criados automaticamente para você ao executar a primeira célula do código. 

## <a name="construct-the-input-dataframe"></a>Construir o dataframe de entrada

Como os dados brutos estão em formato CSV, você pode usar o contexto do Spark para efetuar pull do arquivo na memória como texto não estruturado; em seguida, use a biblioteca CSV do Python para analisar cada linha dos dados.

1. Execute as seguintes linhas para criar um RDD (Conjunto de Dados distribuídos resiliente), importando e analisando os dados de entrada.

    ```PySpark
    def csvParse(s):
        import csv
        from StringIO import StringIO
        sio = StringIO(s)
        value = csv.reader(sio).next()
        sio.close()
        return value
    
    inspections = sc.textFile('/HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections1.csv')\
                    .map(csvParse)
    ```

2. Execute o seguinte código para recuperar uma linha de RDD, portanto você pode dar uma olhada do esquema de dados:

    ```PySpark
    inspections.take(1)
    ```

    A saída é:

    ```
    [['413707',
        'LUNA PARK INC',
        'LUNA PARK  DAY CARE',
        '2049789',
        "Children's Services Facility",
        'Risk 1 (High)',
        '3250 W FOSTER AVE ',
        'CHICAGO',
        'IL',
        '60625',
        '09/21/2010',
        'License-Task Force',
        'Fail',
        '24. DISH WASHING FACILITIES: PROPERLY DESIGNED, CONSTRUCTED, MAINTAINED, INSTALLED, LOCATED AND OPERATED - Comments: All dishwashing machines must be of a type that complies with all requirements of the plumbing section of the Municipal Code of Chicago and Rules and Regulation of the Board of Health. OBSEVERD THE 3 COMPARTMENT SINK BACKING UP INTO THE 1ST AND 2ND COMPARTMENT WITH CLEAR WATER AND SLOWLY DRAINING OUT. INST NEED HAVE IT REPAIR. CITATION ISSUED, SERIOUS VIOLATION 7-38-030 H000062369-10 COURT DATE 10-28-10 TIME 1 P.M. ROOM 107 400 W. SURPERIOR. | 36. LIGHTING: REQUIRED MINIMUM FOOT-CANDLES OF LIGHT PROVIDED, FIXTURES SHIELDED - Comments: Shielding to protect against broken glass falling into food shall be provided for all artificial lighting sources in preparation, service, and display facilities. LIGHT SHIELD ARE MISSING UNDER HOOD OF  COOKING EQUIPMENT AND NEED TO REPLACE LIGHT UNDER UNIT. 4 LIGHTS ARE OUT IN THE REAR CHILDREN AREA,IN THE KINDERGARDEN CLASS ROOM. 2 LIGHT ARE OUT EAST REAR, LIGHT FRONT WEST ROOM. NEED TO REPLACE ALL LIGHT THAT ARE NOT WORKING. | 35. WALLS, CEILINGS, ATTACHED EQUIPMENT CONSTRUCTED PER CODE: GOOD REPAIR, SURFACES CLEAN AND DUST-LESS CLEANING METHODS - Comments: The walls and ceilings shall be in good repair and easily cleaned. MISSING CEILING TILES WITH STAINS IN WEST,EAST, IN FRONT AREA WEST, AND BY THE 15MOS AREA. NEED TO BE REPLACED. | 32. FOOD AND NON-FOOD CONTACT SURFACES PROPERLY DESIGNED, CONSTRUCTED AND MAINTAINED - Comments: All food and non-food contact equipment and utensils shall be smooth, easily cleanable, and durable, and shall be in good repair. SPLASH GUARDED ARE NEEDED BY THE EXPOSED HAND SINK IN THE KITCHEN AREA | 34. FLOORS: CONSTRUCTED PER CODE, CLEANED, GOOD REPAIR, COVING INSTALLED, DUST-LESS CLEANING METHODS USED - Comments: The floors shall be constructed per code, be smooth and easily cleaned, and be kept clean and in good repair. INST NEED TO ELEVATE ALL FOOD ITEMS 6INCH OFF THE FLOOR 6 INCH AWAY FORM WALL.  ',
        '41.97583445690982',
        '-87.7107455232781',
        '(41.97583445690982, -87.7107455232781)']]
    ```

    A saída anterior lhe dá uma ideia do esquema do arquivo de entrada. Ele inclui o nome de cada estabelecimento, o tipo de estabelecimento, o endereço, os dados da inspeção e a localização, entre outras informações. 

3. Execute o seguinte código para criar um dataframe (*df*) e uma tabela temporária (*CountResults*) com algumas colunas que são úteis para a análise de previsão. `sqlContext` é usado para executar transformações de dados estruturados. 

    ```PySpark
    schema = StructType([
    StructField("id", IntegerType(), False),
    StructField("name", StringType(), False),
    StructField("results", StringType(), False),
    StructField("violations", StringType(), True)])
    
    df = spark.createDataFrame(inspections.map(lambda l: (int(l[0]), l[1], l[12], l[13])) , schema)
    df.registerTempTable('CountResults')
    ```

    As quatro colunas de interesse no dataframe são **id**, **nome**, **resultados** e **violações**.

4. Execute o código a seguir para obter uma pequena amostra dos dados:

    ```PySpark
    df.show(5)
    ```

    A saída é:

    ```
    +------+--------------------+-------+--------------------+
    |    id|                name|results|          violations|
    +------+--------------------+-------+--------------------+
    |413707|       LUNA PARK INC|   Fail|24. DISH WASHING ...|
    |391234|       CAFE SELMARIE|   Fail|2. FACILITIES TO ...|
    |413751|          MANCHU WOK|   Pass|33. FOOD AND NON-...|
    |413708|BENCHMARK HOSPITA...|   Pass|                    |
    |413722|           JJ BURGER|   Pass|                    |
    +------+--------------------+-------+--------------------+
    ```

## <a name="understand-the-data"></a>Entender os dados

Vamos começar a ter uma ideia do que o nosso conjunto de dados contém. 

1. Execute o seguinte código para mostrar os valores distintos na coluna **resultados**:

    ```PySpark
    df.select('results').distinct().show()
    ```

    A saída é:

    ```
    +--------------------+
    |             results|
    +--------------------+
    |                Fail|
    |Business Not Located|
    |                Pass|
    |  Pass w/ Conditions|
    |     Out of Business|
    +--------------------+
    ```

2. Execute o seguinte código para visualizar a distribuição desses resultados:

    ```PySpark
    %%sql -o countResultsdf
    SELECT COUNT(results) AS cnt, results FROM CountResults GROUP BY results
    ```

    A mágica do `%%sql` seguido pelo `-o countResultsdf` assegura que a saída da consulta seja mantida localmente no servidor Jupyter (normalmente o nó principal do cluster). A saída é mantida como uma estrutura de dados [Pandas](https://pandas.pydata.org/) com o nome especificado **countResultsdf**. Para obter mais informações sobre a mágica `%%sql` e outras magias disponíveis com o kernel do PySpark, consulte [Kernels disponíveis em notebooks Jupyter com clusters do Apache Spark HDInsight](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).

    A saída é:

    ![Saída da consulta SQL](./media/apache-spark-machine-learning-mllib-ipython/spark-machine-learning-query-output.png "Saída da consulta SQL")


3. Você também pode usar [Matplotlib](https://en.wikipedia.org/wiki/Matplotlib), uma biblioteca usada para construir a visualização de dados para criar um gráfico. Como o gráfico deve ser criado a partir do dataframe **countResultsdf** mantido localmente, o snippet de código deve começar com a mágica `%%local`. Isso garante que o código seja executado localmente no servidor do Jupyter.

    ```PySpark
    %%local
    %matplotlib inline
    import matplotlib.pyplot as plt

    labels = countResultsdf['results']
    sizes = countResultsdf['cnt']
    colors = ['turquoise', 'seagreen', 'mediumslateblue', 'palegreen', 'coral']
    plt.pie(sizes, labels=labels, autopct='%1.1f%%', colors=colors)
    plt.axis('equal')
    ```

    A saída é:

    ![Saída do aplicativo de aprendizado de máquina do Spark – gráfico de pizza com cinco conjuntos de resultados de inspeção distintos](./media/apache-spark-machine-learning-mllib-ipython/spark-machine-learning-result-output-1.png "Saída do resultado de aprendizado de máquina de Spark")

    Para prever um resultado de inspeção de alimentos, você precisa desenvolver um modelo com base nas violações. Como a regressão logística é um método de classificação binária, é recomendável agrupar os dados do resultado em duas categorias: **Reprovado** e **Aprovado**:

   - Aprovado
       - Aprovado
       - Aprovado c/ condições
   - Reprovado
       - Reprovado
   - Descartar
       - Negócios não localizados
       - Fora de negócio

     Dados com os outros resultados ("Negócios não localizados" ou "Cessação de atividades") não são úteis, e eles formam uma porcentagem muito pequena dos resultados de qualquer forma.

4. Execute o código a seguir para converter nosso dataframe existente (`df`) em um novo dataframe em que cada inspeção é representada como um par de violações de rótulo. Nesse caso, um rótulo de `0.0` representa uma falha, um rótulo de `1.0` representa um sucesso e um rótulo de `-1.0` representa alguns resultados diferentes desses dois. 

    ```PySpark
    def labelForResults(s):
        if s == 'Fail':
            return 0.0
        elif s == 'Pass w/ Conditions' or s == 'Pass':
            return 1.0
        else:
            return -1.0
    label = UserDefinedFunction(labelForResults, DoubleType())
    labeledData = df.select(label(df.results).alias('label'), df.violations).where('label >= 0')
    ```

5. Execute o seguinte código para mostrar uma linha de dados rotulados:

    ```PySpark
    labeledData.take(1)
    ```

    A saída é:

    ```
    [Row(label=0.0, violations=u"41. PREMISES MAINTAINED FREE OF LITTER, UNNECESSARY ARTICLES, CLEANING  EQUIPMENT PROPERLY STORED - Comments: All parts of the food establishment and all parts of the property used in connection with the operation of the establishment shall be kept neat and clean and should not produce any offensive odors.  REMOVE MATTRESS FROM SMALL DUMPSTER. | 35. WALLS, CEILINGS, ATTACHED EQUIPMENT CONSTRUCTED PER CODE: GOOD REPAIR, SURFACES CLEAN AND DUST-LESS CLEANING METHODS - Comments: The walls and ceilings shall be in good repair and easily cleaned.  REPAIR MISALIGNED DOORS AND DOOR NEAR ELEVATOR.  DETAIL CLEAN BLACK MOLD LIKE SUBSTANCE FROM WALLS BY BOTH DISH MACHINES.  REPAIR OR REMOVE BASEBOARD UNDER DISH MACHINE (LEFT REAR KITCHEN). SEAL ALL GAPS.  REPLACE MILK CRATES USED IN WALK IN COOLERS AND STORAGE AREAS WITH PROPER SHELVING AT LEAST 6' OFF THE FLOOR.  | 38. VENTILATION: ROOMS AND EQUIPMENT VENTED AS REQUIRED: PLUMBING: INSTALLED AND MAINTAINED - Comments: The flow of air discharged from kitchen fans shall always be through a duct to a point above the roofline.  REPAIR BROKEN VENTILATION IN MEN'S AND WOMEN'S WASHROOMS NEXT TO DINING AREA. | 32. FOOD AND NON-FOOD CONTACT SURFACES PROPERLY DESIGNED, CONSTRUCTED AND MAINTAINED - Comments: All food and non-food contact equipment and utensils shall be smooth, easily cleanable, and durable, and shall be in good repair.  REPAIR DAMAGED PLUG ON LEFT SIDE OF 2 COMPARTMENT SINK.  REPAIR SELF CLOSER ON BOTTOM LEFT DOOR OF 4 DOOR PREP UNIT NEXT TO OFFICE.")]
    ```

## <a name="create-a-logistic-regression-model-from-the-input-dataframe"></a>Criar um modelo de regressão logística do dataframe de entrada

A tarefa final é converter os dados rotulados para um formato que possa ser analisado pela regressão logística. A entrada para um algoritmo de regressão logística precisa ser um conjunto de *pares de vetor de recurso de rótulo*, em que o “vetor de recurso” é um vetor de números que representa o ponto de entrada. Portanto, você precisa converter a coluna "violações", que é semiestruturada e contém muitos comentários em texto livre, para uma matriz de números reais que um computador facilmente entenderia.

Uma abordagem de aprendizado de máquina padrão para processamento de linguagem natural é atribuir a cada palavra distinta um “índice” e então passar um vetor para o algoritmo de aprendizado de máquina, de modo que o valor de cada índice contenha a frequência relativa da palavra na cadeia de texto.

O MLlib oferece uma maneira fácil de executar essa operação. Primeiro, crie tokens de cada cadeia de caracteres de violações para obter as palavras individuais em cada cadeia de caracteres. Em seguida, use um `HashingTF` para converter cada conjunto de tokens em um vetor de recurso que pode ser passado para o algoritmo de regressão logística para construir um modelo. Você realiza todas essas etapas em sequência, usando um "pipeline".

```PySpark
tokenizer = Tokenizer(inputCol="violations", outputCol="words")
hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
lr = LogisticRegression(maxIter=10, regParam=0.01)
pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])

model = pipeline.fit(labeledData)
```

## <a name="evaluate-the-model-using-another-dataset"></a>Avaliar o modelo usando outro conjunto de dados

Você pode usar o modelo criado anteriormente para *prever* quais serão os resultados das novas inspeções com base nas violações observadas. Você treinou esse modelo no conjunto de dados **Food_Inspections1.csv**. Você pode usar um segundo conjunto de dados, **Food_Inspections2.csv**, para *avaliar* a força deste modelo nos dados novos. Esse segundo conjunto de dados (**Food_Inspections2.csv**) está no contêiner de armazenamento padrão associado ao cluster.

1. Execute o código a seguir para criar um novo dataframe, **predictionsDf** que contém a previsão gerada pelo modelo. O snippet de código também cria uma tabela temporária **Previsões** com base no dataframe.

    ```PySpark
    testData = sc.textFile('wasbs:///HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections2.csv')\
                .map(csvParse) \
                .map(lambda l: (int(l[0]), l[1], l[12], l[13]))
    testDf = spark.createDataFrame(testData, schema).where("results = 'Fail' OR results = 'Pass' OR results = 'Pass w/ Conditions'")
    predictionsDf = model.transform(testDf)
    predictionsDf.registerTempTable('Predictions')
    predictionsDf.columns
    ```

    Você verá algo semelhante ao mostrado a seguir:

    ```
    ['id',
        'name',
        'results',
        'violations',
        'words',
        'features',
        'rawPrediction',
        'probability',
        'prediction']
    ```

1. Examine uma das previsões. Execute este snippet de código:

    ```PySpark
    predictionsDf.take(1)
    ```

   Há uma previsão para a primeira entrada no conjunto de dados de teste.
1. O método `model.transform()` aplica a mesma transformação para novos dados com o mesmo esquema e chega a uma previsão de como classificar os dados. Você pode fazer algumas estatísticas simples para ter uma ideia de quão precisas foram nossas previsões:

    ```PySpark
    numSuccesses = predictionsDf.where("""(prediction = 0 AND results = 'Fail') OR
                                            (prediction = 1 AND (results = 'Pass' OR
                                                                results = 'Pass w/ Conditions'))""").count()
    numInspections = predictionsDf.count()

    print "There were", numInspections, "inspections and there were", numSuccesses, "successful predictions"
    print "This is a", str((float(numSuccesses) / float(numInspections)) * 100) + "%", "success rate"
    ```

    A saída se parece com o seguinte:

    ```
    There were 9315 inspections and there were 8087 successful predictions
    This is a 86.8169618894% success rate
    ```

    Usar regressão logística com o Spark fornece um modelo preciso da relação entre as descrições de violações em inglês e se um determinado negócio seria aprovado ou reprovado uma inspeção de alimentos.

## <a name="create-a-visual-representation-of-the-prediction"></a>Criar uma representação visual da previsão
Agora você pode construir uma visualização final para ajudar a justificar os resultados deste teste.

1. Você começa extraindo as diferentes previsões e os resultados da tabela temporária **Predictions** criada anteriormente. As consultas a seguir separam a saída como *true_positive*, *false_positive*, *true_negative* e *false_negative*. Nas consultas a seguir, você vai desligar as visualização usando `-q` e também salvar a saída (usando `-o`) como quadros de dados que podem ser usados com a mágica `%%local`.

    ```PySpark
    %%sql -q -o true_positive
    SELECT count(*) AS cnt FROM Predictions WHERE prediction = 0 AND results = 'Fail'
    ```

    ```PySpark
    %%sql -q -o false_positive
    SELECT count(*) AS cnt FROM Predictions WHERE prediction = 0 AND (results = 'Pass' OR results = 'Pass w/ Conditions')
    ```

    ```PySpark
    %%sql -q -o true_negative
    SELECT count(*) AS cnt FROM Predictions WHERE prediction = 1 AND results = 'Fail'
    ```

    ```PySpark
    %%sql -q -o false_negative
    SELECT count(*) AS cnt FROM Predictions WHERE prediction = 1 AND (results = 'Pass' OR results = 'Pass w/ Conditions')
    ```

1. Por fim, use o snippet de código a seguir para gerar o gráfico usando o **Matplotlib**.

    ```PySpark
    %%local
    %matplotlib inline
    import matplotlib.pyplot as plt

    labels = ['True positive', 'False positive', 'True negative', 'False negative']
    sizes = [true_positive['cnt'], false_positive['cnt'], false_negative['cnt'], true_negative['cnt']]
    colors = ['turquoise', 'seagreen', 'mediumslateblue', 'palegreen', 'coral']
    plt.pie(sizes, labels=labels, autopct='%1.1f%%', colors=colors)
    plt.axis('equal')
    ```

    Você deve ver o seguinte resultado:

    ![Saída do aplicativo de aprendizado de máquina do Spark – percentuais de gráfico de pizza de inspeções em alimentos com falha.](./media/apache-spark-machine-learning-mllib-ipython/spark-machine-learning-result-output-2.png "Saída do resultado de aprendizado de máquina do Spark")

    Neste gráfico, um resultado "positivo" refere-se a uma reprovação na inspeção de alimentos, enquanto um resultado negativo refere-se a uma aprovação na inspeção.

## <a name="shut-down-the-notebook"></a>Fechar o notebook
Depois de concluir a execução do aplicativo, você deve encerrar o bloco de anotações para liberar os recursos. Para fazer isso, no menu **Arquivo** do notebook, selecione **Fechar e Interromper**. Isso desliga e fecha o bloco de anotações.

## <a name="seealso"></a>Consulte também
* [Visão geral: Apache Spark no Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Cenários
* [Apache Spark com BI: Executar análise de dados interativa usando o Spark no HDInsight com ferramentas de BI](apache-spark-use-bi-tools.md)
* [Apache Spark com Machine Learning: Usar o Spark no HDInsight para analisar a temperatura de prédios usando dados do sistema de HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Análise de log do site usando o Apache Spark no HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Criar e executar aplicativos
* [Criar um aplicativo autônomo usando Scala](apache-spark-create-standalone-application.md)
* [Execute trabalhos remotamente em um cluster do Apache Spark usando o Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões
* [Use o Plug-in de Ferramentas do HDInsight para IntelliJ IDEA para criar e enviar aplicativos Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Use o Plugin do HDInsight Tools para o IntelliJ IDEA para depurar os aplicativos do Apache Spark remotamente](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Use os blocos de anotações do Apache Zeppelin com um cluster do Apache Spark no HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para o notebook Jupyter no cluster do Apache Spark para HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Usar pacotes externos com blocos de notas Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter em seu computador e conectar-se a um cluster Spark do HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gerenciar recursos
* [Gerenciar os recursos de cluster do Apache Spark no Azure HDInsight](apache-spark-resource-manager.md)
* [Rastrear e depurar trabalhos em execução em um cluster do Apache Spark no HDInsight](apache-spark-job-debugging.md)
