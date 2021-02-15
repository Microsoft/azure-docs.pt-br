---
title: Analisar logs de sites com bibliotecas de Python no Spark – Azure
description: Este notebook demonstra como analisar dados de log usando uma biblioteca personalizada com o Spark no Azure HDInsight.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 12/27/2019
ms.openlocfilehash: 591804a31c100e5c8211ec6d73fc483c9ac85c72
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98940297"
---
# <a name="analyze-website-logs-using-a-custom-python-library-with-apache-spark-cluster-on-hdinsight"></a>Analise logs do site usando uma biblioteca Python personalizada com cluster do Apache Spark no HDInsight

Este notebook demonstra como analisar dados de log usando uma biblioteca personalizada com o Apache Spark no HDInsight. A biblioteca personalizada que usamos é uma biblioteca Python chamada **iislogparser.py**.

## <a name="prerequisites"></a>Pré-requisitos

Um cluster do Apache Spark no HDInsight. Para obter instruções, consulte o artigo sobre como [Criar clusters do Apache Spark no Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="save-raw-data-as-an-rdd"></a>Salvar dados brutos como RDD

Nesta seção, usamos o notebook [Jupyter](https://jupyter.org) associado a um cluster apache Spark no HDInsight para executar trabalhos que processam seus dados brutos de exemplo e salvá-los como uma tabela do hive. Os dados de exemplo são um arquivo .csv (hvac.csv) disponível em todos os clusters por padrão.

Depois que os dados forem salvos como uma tabela Apache Hive, na próxima seção, vamos nos conectar à tabela Hive usando ferramentas de BI, como Power BI e tableau.

1. Em um navegador da Web, navegue até `https://CLUSTERNAME.azurehdinsight.net/jupyter`, em que `CLUSTERNAME` é o nome do cluster.

1. Crie um novo bloco de anotações. Selecione **novo** e, em seguida, **PySpark**.

    ![Criar um novo Apache Jupyter Notebook](./media/apache-spark-custom-library-website-log-analysis/hdinsight-create-jupyter-notebook.png "Criar um novo Jupyter Notebook")

1. Um novo bloco de anotações é criado e aberto com o nome Untitled.pynb. Selecione o nome do bloco de anotações na parte superior e insira um nome amigável.

    ![Fornecer um nome para o bloco de anotações](./media/apache-spark-custom-library-website-log-analysis/hdinsight-name-jupyter-notebook.png "Fornecer um nome para o bloco de anotações")

1. Como você criou um bloco de anotações usando o kernel PySpark, não precisa criar nenhum contexto explicitamente. Os contextos do Spark e do Hive serão criados automaticamente para você ao executar a primeira célula do código. Você pode começar importando os tipos que são obrigatórios para este cenário. Cole o trecho a seguir em uma célula vazia e pressione **Shift + Enter**.

    ```pyspark
    from pyspark.sql import Row
    from pyspark.sql.types import *
    ```

1. Crie um RDD usando os dados de log de exemplo já disponíveis no cluster. Você pode acessar os dados na conta de armazenamento padrão associada ao cluster em `\HdiSamples\HdiSamples\WebsiteLogSampleData\SampleLog\909f2b.log` . Execute o seguinte código:

    ```pyspark
    logs = sc.textFile('wasbs:///HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b.log')
    ```

1. Recupere um log de exemplo definido para verificar se a etapa anterior foi concluída com êxito.

    ```pyspark
    logs.take(5)
    ```

    Você deverá ver uma saída semelhante ao texto a seguir:

    ```output
    [u'#Software: Microsoft Internet Information Services 8.0',
    u'#Fields: date time s-sitename cs-method cs-uri-stem cs-uri-query s-port cs-username c-ip cs(User-Agent) cs(Cookie) cs(Referer) cs-host sc-status sc-substatus sc-win32-status sc-bytes cs-bytes time-taken',
    u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step2.png X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 53175 871 46',
    u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step3.png X-ARR-LOG-ID=9eace870-2f49-4efd-b204-0d170da46b4a 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 51237 871 32',
    u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step4.png X-ARR-LOG-ID=4bea5b3d-8ac9-46c9-9b8c-ec3e9500cbea 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 72177 871 47']
    ```

## <a name="analyze-log-data-using-a-custom-python-library"></a>Analise os dados de log usando uma biblioteca Python personalizada

1. Na saída acima, as primeiras linhas incluem as informações de cabeçalho, e cada linha restante coincide com o esquema descrito naquele cabeçalho. Analisar esses logs pode ser complicado. Então, usamos uma biblioteca personalizada do Python (**iislogparser.py**) que torna a análise desses logs muito mais fácil. Por padrão, essa biblioteca é incluída com o cluster Spark no HDInsight em `/HdiSamples/HdiSamples/WebsiteLogSampleData/iislogparser.py` .

    No entanto, essa biblioteca não está no `PYTHONPATH` , portanto, não podemos usá-la usando uma instrução de importação como `import iislogparser` . Para usar essa biblioteca, devemos distribuí-la para todos os nós de trabalho. Execute o snippet de código a seguir.

    ```pyspark
    sc.addPyFile('wasbs:///HdiSamples/HdiSamples/WebsiteLogSampleData/iislogparser.py')
    ```

1. O `iislogparser` fornece uma função `parse_log_line` que retorna `None` se uma linha do log for uma linha de cabeçalho, e retorna uma instância da classe `LogLine` se encontrar uma linha do log. Use a classe `LogLine` para extrair somente as linhas do log do RDD:

    ```pyspark
    def parse_line(l):
        import iislogparser
        return iislogparser.parse_log_line(l)
    logLines = logs.map(parse_line).filter(lambda p: p is not None).cache()
    ```

1. Recupere algumas linhas de log extraídas para verificar se a etapa foi concluída com êxito.

    ```pyspark
    logLines.take(2)
    ```

   A saída deve ser semelhante ao seguinte texto:

    ```output
    [2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step2.png X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 53175 871 46,
    2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step3.png X-ARR-LOG-ID=9eace870-2f49-4efd-b204-0d170da46b4a 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 51237 871 32]
    ```

1. A classe `LogLine`, por sua vez, tem alguns métodos úteis, como `is_error()`, que retorna se uma entrada de log tem um código de erro. Use essa classe para calcular o número de erros nas linhas de log extraídas e registrar em log todos os erros em um arquivo diferente.

    ```pyspark
    errors = logLines.filter(lambda p: p.is_error())
    numLines = logLines.count()
    numErrors = errors.count()
    print 'There are', numErrors, 'errors and', numLines, 'log entries'
    errors.map(lambda p: str(p)).saveAsTextFile('wasbs:///HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b-2.log')
    ```

    A saída deve ser State `There are 30 errors and 646 log entries` .

1. Você também pode usar **Matplotlib** para construir uma visualização dos dados. Por exemplo, se você quiser isolar a causa de solicitações executadas por um longo tempo, talvez queira localizar os arquivos que, em média, levam mais tempo para atender. O snippet a seguir recupera os 25 principais recursos que levaram mais tempo para atender uma solicitação.

    ```pyspark
    def avgTimeTakenByKey(rdd):
        return rdd.combineByKey(lambda line: (line.time_taken, 1),
                                lambda x, line: (x[0] + line.time_taken, x[1] + 1),
                                lambda x, y: (x[0] + y[0], x[1] + y[1]))\
                    .map(lambda x: (x[0], float(x[1][0]) / float(x[1][1])))

    avgTimeTakenByKey(logLines.map(lambda p: (p.cs_uri_stem, p))).top(25, lambda x: x[1])
    ```

   Você deverá ver uma saída semelhante ao texto a seguir:

    ```output
    [(u'/blogposts/mvc4/step13.png', 197.5),
    (u'/blogposts/mvc2/step10.jpg', 179.5),
    (u'/blogposts/extractusercontrol/step5.png', 170.0),
    (u'/blogposts/mvc4/step8.png', 159.0),
    (u'/blogposts/mvcrouting/step22.jpg', 155.0),
    (u'/blogposts/mvcrouting/step3.jpg', 152.0),
    (u'/blogposts/linqsproc1/step16.jpg', 138.75),
    (u'/blogposts/linqsproc1/step26.jpg', 137.33333333333334),
    (u'/blogposts/vs2008javascript/step10.jpg', 127.0),
    (u'/blogposts/nested/step2.jpg', 126.0),
    (u'/blogposts/adminpack/step1.png', 124.0),
    (u'/BlogPosts/datalistpaging/step2.png', 118.0),
    (u'/blogposts/mvc4/step35.png', 117.0),
    (u'/blogposts/mvcrouting/step2.jpg', 116.5),
    (u'/blogposts/aboutme/basketball.jpg', 109.0),
    (u'/blogposts/anonymoustypes/step11.jpg', 109.0),
    (u'/blogposts/mvc4/step12.png', 106.0),
    (u'/blogposts/linq8/step0.jpg', 105.5),
    (u'/blogposts/mvc2/step18.jpg', 104.0),
    (u'/blogposts/mvc2/step11.jpg', 104.0),
    (u'/blogposts/mvcrouting/step1.jpg', 104.0),
    (u'/blogposts/extractusercontrol/step1.png', 103.0),
    (u'/blogposts/sqlvideos/sqlvideos.jpg', 102.0),
    (u'/blogposts/mvcrouting/step21.jpg', 101.0),
    (u'/blogposts/mvc4/step1.png', 98.0)]
    ```

1. Você também pode apresentar essas informações na forma do gráfico. Como uma primeira etapa para criar um gráfico, criaremos uma tabela **AverageTime** temporária. A tabela agrupa os logs por hora para ver se houve quaisquer picos incomuns de latência em um determinado momento.

    ```pyspark
    avgTimeTakenByMinute = avgTimeTakenByKey(logLines.map(lambda p: (p.datetime.minute, p))).sortByKey()
    schema = StructType([StructField('Minutes', IntegerType(), True),
                        StructField('Time', FloatType(), True)])

    avgTimeTakenByMinuteDF = sqlContext.createDataFrame(avgTimeTakenByMinute, schema)
    avgTimeTakenByMinuteDF.registerTempTable('AverageTime')
    ```

1. Você pode então executar a seguinte consulta SQL para obter todos os registros da tabela **AverageTime** .

    ```pyspark
    %%sql -o averagetime
    SELECT * FROM AverageTime
    ```

   A mágica do `%%sql` seguido pelo `-o averagetime` assegura que a saída da consulta seja mantida localmente no servidor Jupyter (normalmente o nó principal do cluster). A saída é mantida como uma estrutura de dados [Pandas](https://pandas.pydata.org/) com o nome especificado **averagetime**.

   Você deverá ver uma saída semelhante à imagem a seguir:

   ![saída de consulta SQL do hdinsight jupyter](./media/apache-spark-custom-library-website-log-analysis/hdinsight-jupyter-sql-qyery-output.png "Saída da consulta SQL")

   Para obter mais informações sobre a palavra mágica `%%sql`, consulte [Parâmetros com suporte na palavra mágica %%sql](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).

1. Agora você pode usar Matplotlib, uma biblioteca usada para construir a visualização de dados para criar um gráfico. Como o gráfico deve ser criado da estrutura de dados **averagetime** mantida localmente, o snippet de código deve começar com a mágica `%%local`. Isso garante que o código seja executado localmente no servidor do Jupyter.

    ```pyspark
    %%local
    %matplotlib inline
    import matplotlib.pyplot as plt

    plt.plot(averagetime['Minutes'], averagetime['Time'], marker='o', linestyle='--')
    plt.xlabel('Time (min)')
    plt.ylabel('Average time taken for request (ms)')
    ```

   Você deverá ver uma saída semelhante à imagem a seguir:

   ![gráfico de análise de log da Web do Apache Spark](./media/apache-spark-custom-library-website-log-analysis/hdinsight-apache-spark-web-log-analysis-plot.png "Saída de Matplotlib")

1. Depois de concluir a execução do aplicativo, você deve encerrar o bloco de anotações para liberar os recursos. Para fazer isso, no menu **Arquivo** do notebook, selecione **Fechar e Interromper**. Esta ação será desligada e fechará o bloco de anotações.

## <a name="next-steps"></a>Próximas etapas

Explore os seguintes artigos:

* [Visão geral: Apache Spark no Azure HDInsight](apache-spark-overview.md)
* [Usar pacotes externos com notebooks Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter em seu computador e conectar-se a um cluster Spark do HDInsight](apache-spark-jupyter-notebook-install-locally.md)
