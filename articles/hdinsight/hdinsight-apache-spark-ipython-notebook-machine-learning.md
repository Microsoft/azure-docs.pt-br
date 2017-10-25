---
title: "Criar aplicativos de aprendizado de máquina do Apache Spark no Azure HDInsight | Microsoft Docs"
description: "Instruções passo a passo sobre como criar aplicativos de aprendizado de máquina do Apache Spark em clusters do HDInsight Spark usando o bloco de anotações do Jupyter"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: f584ca5e-abee-4b7c-ae58-2e45dfc56bf4
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: nitinme
ms.openlocfilehash: 928c452869d27c7f40982d8b5d400c3a611008a3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="build-apache-spark-machine-learning-applications-on-azure-hdinsight"></a>Criar aplicativos de aprendizado de máquina do Apache Spark no Azure HDInsight

Aprenda a criar um aplicativo de aprendizado de máquina do Apache Spark usando um cluster do Spark no HDInsight. Este artigo mostra como usar o bloco de anotações do Jupyter disponível com o cluster para criar e testar esse aplicativo. O aplicativo usa os dados de HVAC.csv de exemplo que estão disponíveis em todos os clusters por padrão.

**Pré-requisitos:**

Você deve ter o seguinte:

* Um cluster do Apache Spark no HDInsight. Para obter instruções, consulte o artigo sobre como [Criar clusters do Apache Spark no Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md). 

## <a name="data"></a>Entender o conjunto de dados
Antes de começarmos a criação do aplicativo, vamos entender a estrutura dos dados para os quais criaremos o aplicativo e o tipo de análise que vamos fazer nos dados. 

Neste artigo, usamos o exemplo arquivo de dados de exemplo **HVAC.csv** que está disponível na conta de Armazenamento do Azure que você associou ao cluster HDInsight. Na conta de armazenamento, o arquivo está em **\HdiSamples\HdiSamples\SensorSampleData\hvac**. Baixe e abra o arquivo CSV para obter um instantâneo dos dados.  

![Exemplo de instantâneo dos dados usados para aprendizado de máquina do Spark](./media/hdinsight-apache-spark-ipython-notebook-machine-learning/spark-machine-learning-understand-data.png "Exemplo de instantâneo dos dados usados para aprendizado de máquina do Spark")

Os dados mostram a temperatura de destino e a temperatura real de um prédio com sistemas de HVAC instalados. Vamos supor que a coluna **System** representa a ID do sistema e a coluna **SystemAge** representa o número de anos que o sistema HVAC foi instalado no prédio.

Podemos usar esses dados para prever se um prédio será mais quente ou frio com base na temperatura de destino, uma ID de sistema e a idade do sistema.

## <a name="app"></a>Escrever um aplicativo de aprendizado de máquina do Spark usando o MLlib Spark
Neste aplicativo, usamos um pipeline ML do Spark para executar uma classificação de documento. No pipeline, vamos dividir o documento em palavras, converter as palavras em um vetor de recurso numérico e, finalmente, criar um modelo de previsão usando as etiquetas e vetores de recurso. Execute as seguintes etapas para criar o aplicativo.

1. No [Portal do Azure](https://portal.azure.com/), no quadro inicial, clique no bloco do cluster Spark (se você o tiver fixado no quadro inicial). Você também pode navegar até o cluster em **Procurar Tudo** > **Clusters HDInsight**.   
2. Na folha do cluster Spark, clique em **Painel do Cluster** e em **Notebook Jupyter**. Se você receber uma solicitação, insira as credenciais de administrador para o cluster.
   
   > [!NOTE]
   > Você também pode acessar o Bloco de Notas Jupyter de seu cluster abrindo a seguinte URL no navegador. Substitua **CLUSTERNAME** pelo nome do cluster:
   > 
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   > 
   > 
3. Crie um novo bloco de anotações. Clique em **Novo** e em **PySpark**.
   
    ![Criar um exemplo de bloco de anotações do Jupyter para aprendizado de máquina do Spark](./media/hdinsight-apache-spark-ipython-notebook-machine-learning/spark-machine-learning-create-notebook.png "Criar um exemplo de bloco de anotações do Jupyter para aprendizado de máquina Spark")
4. Um novo bloco de anotações é criado e aberto com o nome Untitled.pynb. Clique no nome do bloco de anotações na parte superior e digite um nome amigável.
   
    ![Fornecer um exemplo de nome de bloco de anotações do Jupyter para aprendizado de máquina Spark](./media/hdinsight-apache-spark-ipython-notebook-machine-learning/spark-machine-learning-notebook-name.png "Fornecer um exemplo de nome de bloco de anotações do Jupyter para aprendizado de máquina Spark")
5. Por ter criado um notebook usando o kernel PySpark, não será necessário criar nenhum contexto explicitamente. Os contextos do Spark e do Hive serão criados automaticamente para você ao executar a primeira célula do código. Você pode começar importando os tipos que são obrigatórios para este cenário. Cole o trecho a seguir em uma célula vazia e pressione **SHIFT+ENTER**. 
   
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
6. Agora você deve carregar os dados (hvac.csv), analisá-los e usá-los para treinar o modelo. Para isso, você define uma função que verifica se a temperatura real do prédio é maior que a temperatura de destino. Se a temperatura real é maior, o prédio está quente, indicado pelo valor **1,0**. Se a temperatura real é menor, o prédio está frio, indicado pelo valor **0,0**. 
   
    Cole o trecho a seguir em uma célula vazia e pressione **SHIFT+ENTER**.

        # List the structure of data for better understanding. Because the data will be
        # loaded as an array, this structure makes it easy to understand what each element
        # in the array corresponds to

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
        data = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

        documents = data.filter(lambda s: "Date" not in s).map(parseDocument)
        training = documents.toDF()


1. Configure o pipeline de aprendizado de máquina Spark que consiste de três estágios: tokenizer, hashingTF e lr. Para obter mais informações sobre o que é um pipeline e como ele funciona, confira <a href="http://spark.apache.org/docs/latest/ml-guide.html#how-it-works" target="_blank">Pipeline de aprendizado de máquina Spark</a>.
   
    Cole o trecho a seguir em uma célula vazia e pressione **SHIFT+ENTER**.
   
        tokenizer = Tokenizer(inputCol="SystemInfo", outputCol="words")
        hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
        lr = LogisticRegression(maxIter=10, regParam=0.01)
        pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])
2. Ajuste o pipeline para o documento de treinamento. Cole o trecho a seguir em uma célula vazia e pressione **SHIFT+ENTER**.
   
        model = pipeline.fit(training)
3. Verifique o documento de treinamento para o ponto de verificação de seu progresso com o aplicativo. Cole o trecho a seguir em uma célula vazia e pressione **SHIFT+ENTER**.
   
        training.show()
   
    Isso deve fornecer um resultado semelhante ao seguinte:
   
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

    Volte e verifique se a saída em relação ao arquivo CSV bruto. Por exemplo, a primeira linha do arquivo CSV tem esses dados:

    ![Exemplo de instantâneo dos dados de saída para aprendizado de máquina do Spark](./media/hdinsight-apache-spark-ipython-notebook-machine-learning/spark-machine-learning-output-data.png "Exemplo de instantâneo dos dados de saída para aprendizado de máquina do Spark")

    Observe como a temperatura real é menor que a temperatura de destino sugerindo que o prédio está frio. Portanto, no resultado do treinamento, o valor para o **rótulo** na primeira linha é **0,0**, o que significa que o prédio não está quente.

1. Prepare um conjunto de dados para executar o modelo treinado. Para fazer isso, passamos uma ID de sistema e a idade do sistema (denotado como **SystemInfo** no resultado de treinamento), e o modelo deve prever se o prédio com essa ID de sistema e idade de sistema seria mais quente (indicado por 1,0) ou mais frio (indicado pelo 0,0).
   
   Cole o trecho a seguir em uma célula vazia e pressione **SHIFT+ENTER**.
   
       # SystemInfo here is a combination of system ID followed by system age
       Document = Row("id", "SystemInfo")
       test = sc.parallelize([(1L, "20 25"),
                     (2L, "4 15"),
                     (3L, "16 9"),
                     (4L, "9 22"),
                     (5L, "17 10"),
                     (6L, "7 22")]) \
           .map(lambda x: Document(*x)).toDF() 
2. Por fim, faça as previsões nos dados de teste. Cole o trecho a seguir em uma célula vazia e pressione **SHIFT+ENTER**.
   
        # Make predictions on test documents and print columns of interest
        prediction = model.transform(test)
        selected = prediction.select("SystemInfo", "prediction", "probability")
        for row in selected.collect():
            print row
3. Você deverá ver um resultado semelhante ao seguinte:
   
       Row(SystemInfo=u'20 25', prediction=1.0, probability=DenseVector([0.4999, 0.5001]))
       Row(SystemInfo=u'4 15', prediction=0.0, probability=DenseVector([0.5016, 0.4984]))
       Row(SystemInfo=u'16 9', prediction=1.0, probability=DenseVector([0.4785, 0.5215]))
       Row(SystemInfo=u'9 22', prediction=1.0, probability=DenseVector([0.4549, 0.5451]))
       Row(SystemInfo=u'17 10', prediction=1.0, probability=DenseVector([0.4925, 0.5075]))
       Row(SystemInfo=u'7 22', prediction=0.0, probability=DenseVector([0.5015, 0.4985]))
   
   Na primeira linha na previsão, você pode ver que para um sistema HVAC com ID 20 e sistema de 25 anos, o prédio estará quente (**previsão = 1,0**). O primeiro valor de DenseVector (0,49999) corresponde à previsão 0,0 e o segundo valor (0,5001) corresponde à previsão 1,0. Na saída, mesmo que o segundo valor seja apenas um pouco mais alto, o modelo mostra **previsão = 1,0**.
4. Depois de concluir a execução do aplicativo, você deve encerrar o notebook para liberar os recursos. Para isso, no menu **Arquivo** do bloco de anotações, clique em **Fechar e Interromper**. Isso desligará e fechará o bloco de anotações.

## <a name="anaconda"></a>Use a biblioteca Anaconda scikit-learn para aprendizado de máquina do Spark
Os clusters Apache Spark no HDInsight incluem bibliotecas Anaconda. Isso também inclui a biblioteca **scikit-learn** para aprendizado de máquina. A biblioteca também inclui vários conjuntos de dados que você pode usar para criar aplicativos de exemplo diretamente de um bloco de anotações do Jupyter. Para obter exemplos sobre como usar a biblioteca scikit-learn, confira [http://scikit-learn.org/stable/auto_examples/index.html](http://scikit-learn.org/stable/auto_examples/index.html).

## <a name="seealso"></a>Consulte também
* [Visão geral: Apache Spark no Azure HDInsight](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>Cenários
* [Spark com BI: executar análise de dados interativa usando o Spark no HDInsight com ferramentas de BI](hdinsight-apache-spark-use-bi-tools.md)
* [Spark com Machine Learning: usar o Spark no HDInsight para prever resultados da inspeção de alimentos](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Streaming Spark: usar o Spark no HDInsight para a criação de aplicativos de streaming em tempo real](hdinsight-apache-spark-eventhub-streaming.md)
* [Análise de log do site usando o Spark no HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Criar e executar aplicativos
* [Criar um aplicativo autônomo usando Scala](hdinsight-apache-spark-create-standalone-application.md)
* [Executar trabalhos remotamente em um cluster do Spark usando Livy](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões
* [Usar o plug-in de Ferramentas do HDInsight para IntelliJ IDEA para criar e enviar aplicativos Spark Scala](hdinsight-apache-spark-intellij-tool-plugin.md)
* [Usar o plug-in de Ferramentas do HDInsight para depurar aplicativos Spark remotamente](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Usar blocos de anotações do Zeppelin com um cluster Spark no HDInsight](hdinsight-apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para o bloco de anotações Jupyter no cluster do Spark para HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Usar pacotes externos com blocos de notas Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter em seu computador e conectar-se a um cluster Spark do HDInsight](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gerenciar recursos
* [Gerenciar os recursos de cluster do Apache Spark no Azure HDInsight](hdinsight-apache-spark-resource-manager.md)
* [Rastrear e depurar trabalhos em execução em um cluster do Apache Spark no HDInsight](hdinsight-apache-spark-job-debugging.md)

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-weblogs-sample]: hdinsight-hive-analyze-website-log.md
[hdinsight-sensor-data-sample]: hdinsight-hive-analyze-sensor-data.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-create-storageaccount]:../storage/common/storage-create-storage-account.md
