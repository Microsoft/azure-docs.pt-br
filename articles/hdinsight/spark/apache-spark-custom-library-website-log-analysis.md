---
title: Analisar logs de sites com bibliotecas de Python no Spark – Azure
description: Este notebook demonstra como analisar dados de log usando uma biblioteca personalizada com o Spark no Azure HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/28/2017
ms.author: hrasheed
ms.openlocfilehash: 5492f4865e464cf8bedaee6e9b0ab25532e21459
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448751"
---
# <a name="analyze-website-logs-using-a-custom-python-library-with-apache-spark-cluster-on-hdinsight"></a>Analise logs do site usando uma biblioteca Python personalizada com cluster do Apache Spark no HDInsight

Este notebook demonstra como analisar dados de log usando uma biblioteca personalizada com o Apache Spark no HDInsight. A biblioteca personalizada que usamos é uma biblioteca Python chamada **iislogparser.py**.

> [!TIP]  
> Este artigo também está disponível como um notebook Jupyter em um cluster Spark (Linux) que você criar no HDInsight. A experiência de bloco de anotações permite executar os snippets de código Python no próprio bloco de anotações. Para executar o artigo de dentro de um bloco de anotações, crie um cluster Spark, inicie um bloco de anotações do Jupyter (`https://CLUSTERNAME.azurehdinsight.net/jupyter`), e, em seguida, execute o notebook **analisar logs com o Spark usando um personalizado ipynb** sob o **PySpark** pasta.
>
>

**Pré-requisitos:**

Você deve ter o seguinte:

* Uma assinatura do Azure. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* Um cluster do Apache Spark no HDInsight. Para obter instruções, consulte o artigo sobre como [Criar clusters do Apache Spark no Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="save-raw-data-as-an-rdd"></a>Salvar dados brutos como RDD
Nesta seção, usamos o notebook [Jupyter](https://jupyter.org) associado a um cluster do Apache Spark no HDInsight para executar trabalhos que processam dados brutos de exemplo e os salvam como uma tabela Hive. Os dados de exemplo são um arquivo .csv (hvac.csv) disponível em todos os clusters por padrão.

Depois que seus dados forem salvos como uma tabela do Apache Hive, na próxima seção, nos conectaremos à tabela Hive usando ferramentas de BI, como o Power BI e o Tableau.

1. No [portal do Azure](https://portal.azure.com/), no quadro inicial, clique no bloco do cluster Spark (se você o tiver fixado no quadro inicial). Você também pode navegar até o cluster em **Procurar Tudo** > **Clusters HDInsight**.   
2. Na folha do cluster Spark, clique em **Painel do Cluster** e em **Notebook Jupyter**. Se você receber uma solicitação, insira as credenciais de administrador para o cluster.

   > [!NOTE]
   > Você também pode acessar o Bloco de Notas Jupyter de seu cluster abrindo a seguinte URL no navegador. Substitua **CLUSTERNAME** pelo nome do cluster:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   >
   >
3. Crie um novo bloco de anotações. Clique em **Novo** e em **PySpark**.

    ![Criar um novo bloco de anotações do Jupyter](./media/apache-spark-custom-library-website-log-analysis/hdinsight-create-jupyter-notebook.png "Criar um novo bloco de anotações do Jupyter")
4. Um novo bloco de anotações é criado e aberto com o nome Untitled.pynb. Clique no nome do bloco de anotações na parte superior e digite um nome amigável.

    ![Fornecer um nome para o bloco de anotações](./media/apache-spark-custom-library-website-log-analysis/hdinsight-name-jupyter-notebook.png "Fornecer um nome para o bloco de anotações")
5. Por ter criado um notebook usando o kernel PySpark, não será necessário criar nenhum contexto explicitamente. Os contextos do Spark e do Hive serão criados automaticamente para você ao executar a primeira célula do código. Você pode começar importando os tipos que são obrigatórios para este cenário. Cole o snippet a seguir em uma célula vazia e pressione **SHIFT+ENTER**.

        from pyspark.sql import Row
        from pyspark.sql.types import *


1. Crie um RDD usando os dados de log de exemplo já disponíveis no cluster. Você pode acessar os dados na conta de armazenamento padrão associada ao cluster em **\HdiSamples\HdiSamples\WebsiteLogSampleData\SampleLog\909f2b.log**.

        logs = sc.textFile('wasb:///HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b.log')


1. Recupere um log de exemplo definido para verificar se a etapa anterior foi concluída com êxito.

        logs.take(5)

    Você deverá ver um resultado semelhante ao seguinte:

        # -----------------
        # THIS IS AN OUTPUT
        # -----------------

        [u'#Software: Microsoft Internet Information Services 8.0',
         u'#Fields: date time s-sitename cs-method cs-uri-stem cs-uri-query s-port cs-username c-ip cs(User-Agent) cs(Cookie) cs(Referer) cs-host sc-status sc-substatus sc-win32-status sc-bytes cs-bytes time-taken',
         u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step2.png X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 53175 871 46',
         u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step3.png X-ARR-LOG-ID=9eace870-2f49-4efd-b204-0d170da46b4a 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 51237 871 32',
         u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step4.png X-ARR-LOG-ID=4bea5b3d-8ac9-46c9-9b8c-ec3e9500cbea 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 72177 871 47']

## <a name="analyze-log-data-using-a-custom-python-library"></a>Analise os dados de log usando uma biblioteca Python personalizada
1. Na saída acima, as primeiras linhas incluem as informações de cabeçalho, e cada linha restante coincide com o esquema descrito naquele cabeçalho. Analisar esses logs pode ser complicado. Então, usamos uma biblioteca personalizada do Python (**iislogparser.py**) que torna a análise desses logs muito mais fácil. Por padrão, essa biblioteca é incluída com o cluster do Spark no HDInsight em **/HdiSamples/HdiSamples/WebsiteLogSampleData/iislogparser.py**.

    No entanto, essa biblioteca não está no `PYTHONPATH`, então não podemos usá-la com uma instrução de importação como `import iislogparser`. Para usar essa biblioteca, devemos distribuí-la para todos os nós de trabalho. Execute o snippet de código a seguir.

        sc.addPyFile('wasb:///HdiSamples/HdiSamples/WebsiteLogSampleData/iislogparser.py')


1. O `iislogparser` fornece uma função `parse_log_line` que retorna `None` se uma linha do log for uma linha de cabeçalho, e retorna uma instância da classe `LogLine` se encontrar uma linha do log. Use a classe `LogLine` para extrair somente as linhas do log do RDD:

        def parse_line(l):
            import iislogparser
            return iislogparser.parse_log_line(l)
        logLines = logs.map(parse_line).filter(lambda p: p is not None).cache()
2. Recupere algumas linhas de log extraídas para verificar se a etapa foi concluída com êxito.

       logLines.take(2)

   A saída deverá ser semelhante a esta:

       # -----------------
       # THIS IS AN OUTPUT
       # -----------------

       [2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step2.png X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 53175 871 46,
        2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step3.png X-ARR-LOG-ID=9eace870-2f49-4efd-b204-0d170da46b4a 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 51237 871 32]
3. A classe `LogLine`, por sua vez, tem alguns métodos úteis, como `is_error()`, que retorna se uma entrada de log tem um código de erro. Use isso para calcular o número de erros nas linhas de log extraídas e então registre todos os erros em um arquivo diferente.

       errors = logLines.filter(lambda p: p.is_error())
       numLines = logLines.count()
       numErrors = errors.count()
       print 'There are', numErrors, 'errors and', numLines, 'log entries'
       errors.map(lambda p: str(p)).saveAsTextFile('wasb:///HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b-2.log')

   Você verá algo semelhante ao mostrado a seguir:

       # -----------------
       # THIS IS AN OUTPUT
       # -----------------

       There are 30 errors and 646 log entries
4. Você também pode usar **Matplotlib** para construir uma visualização dos dados. Por exemplo, se você quiser isolar a causa de solicitações executadas por um longo tempo, talvez queira localizar os arquivos que, em média, levam mais tempo para atender.
   O snippet a seguir recupera os 25 principais recursos que levaram mais tempo para atender uma solicitação.

       def avgTimeTakenByKey(rdd):
           return rdd.combineByKey(lambda line: (line.time_taken, 1),
                                   lambda x, line: (x[0] + line.time_taken, x[1] + 1),
                                   lambda x, y: (x[0] + y[0], x[1] + y[1]))\
                     .map(lambda x: (x[0], float(x[1][0]) / float(x[1][1])))

       avgTimeTakenByKey(logLines.map(lambda p: (p.cs_uri_stem, p))).top(25, lambda x: x[1])

   Você verá algo semelhante ao mostrado a seguir:

       # -----------------
       # THIS IS AN OUTPUT
       # -----------------

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
5. Você também pode apresentar essas informações na forma do gráfico. Como uma primeira etapa para criar um gráfico, criaremos uma tabela **AverageTime**temporária. A tabela agrupa os logs por hora para ver se houve quaisquer picos incomuns de latência em um determinado momento.

       avgTimeTakenByMinute = avgTimeTakenByKey(logLines.map(lambda p: (p.datetime.minute, p))).sortByKey()
       schema = StructType([StructField('Minutes', IntegerType(), True),
                            StructField('Time', FloatType(), True)])

       avgTimeTakenByMinuteDF = sqlContext.createDataFrame(avgTimeTakenByMinute, schema)
       avgTimeTakenByMinuteDF.registerTempTable('AverageTime')
6. Você pode então executar a seguinte consulta SQL para obter todos os registros da tabela **AverageTime** .

       %%sql -o averagetime
       SELECT * FROM AverageTime

   A mágica do `%%sql` seguido pelo `-o averagetime` assegura que a saída da consulta seja mantida localmente no servidor Jupyter (normalmente o nó principal do cluster). A saída é mantida como uma estrutura de dados [Pandas](https://pandas.pydata.org/) com o nome especificado **averagetime**.

   Você verá algo semelhante ao mostrado a seguir:

   ![Saída da consulta SQL](./media/apache-spark-custom-library-website-log-analysis/hdinsight-jupyter-sql-qyery-output.png "Saída da consulta SQL")

   Para obter mais informações sobre a palavra mágica `%%sql`, consulte [Parâmetros com suporte na palavra mágica %%sql](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).
7. Agora você pode usar Matplotlib, uma biblioteca usada para construir a visualização de dados para criar um gráfico. Como o gráfico deve ser criado da estrutura de dados **averagetime** mantida localmente, o snippet de código deve começar com a mágica `%%local`. Isso garante que o código seja executado localmente no servidor do Jupyter.

       %%local
       %matplotlib inline
       import matplotlib.pyplot as plt

       plt.plot(averagetime['Minutes'], averagetime['Time'], marker='o', linestyle='--')
       plt.xlabel('Time (min)')
       plt.ylabel('Average time taken for request (ms)')

   Você verá algo semelhante ao mostrado a seguir:

   ![Saída de Matplotlib](./media/apache-spark-custom-library-website-log-analysis/hdinsight-apache-spark-web-log-analysis-plot.png "Saída de Matplotlib")
8. Depois de concluir a execução do aplicativo, você deve encerrar o notebook para liberar os recursos. Para isso, no menu **Arquivo** do bloco de anotações, clique em **Fechar e Interromper**. Isso desligará e fechará o bloco de anotações.

## <a name="seealso"></a>Consulte também
* [Visão geral: Apache Spark no Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Cenários
* [Apache Spark com BI: Executar análise de dados interativa usando o Spark no HDInsight com ferramentas de BI](apache-spark-use-bi-tools.md)
* [Apache Spark com Machine Learning: Usar o Spark no HDInsight para analisar a temperatura de prédios usando dados do sistema de HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark com Machine Learning: Usar o Spark no HDInsight para prever resultados da inspeção de alimentos](apache-spark-machine-learning-mllib-ipython.md)

### <a name="create-and-run-applications"></a>Criar e executar aplicativos
* [Criar um aplicativo autônomo usando Scala](apache-spark-create-standalone-application.md)
* [Execute trabalhos remotamente em um cluster do Apache Spark usando o Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões
* [Use o Plugin do HDInsight Tools para o IntelliJ IDEA criar e enviar os aplicativos Scala do Apache Spark](apache-spark-intellij-tool-plugin.md)
* [Use o Plugin do HDInsight Tools para o IntelliJ IDEA para depurar os aplicativos do Apache Spark remotamente](../hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Use os blocos de anotações do Apache Zeppelin com um cluster do Apache Spark no HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para o notebook Jupyter no cluster do Apache Spark para HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Usar pacotes externos com blocos de notas Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter em seu computador e conectar-se a um cluster Spark do HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gerenciar recursos
* [Gerenciar os recursos de cluster do Apache Spark no Azure HDInsight](apache-spark-resource-manager.md)
* [Rastrear e depurar trabalhos em execução em um cluster do Apache Spark no HDInsight](apache-spark-job-debugging.md)
