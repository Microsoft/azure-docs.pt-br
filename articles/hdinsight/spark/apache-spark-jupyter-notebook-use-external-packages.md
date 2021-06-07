---
title: Usar pacotes Maven personalizados com Jupyter no Spark – Azure HDInsight
description: Instruções passo a passo sobre como configurar notebooks Jupyter disponíveis com clusters Spark do HDInsight para usar pacotes Maven personalizados.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 11/22/2019
ms.openlocfilehash: 3ddfdfdfe10d5b6ea7c2d5cd99d325564163c0dd
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104866005"
---
# <a name="use-external-packages-with-jupyter-notebooks-in-apache-spark-clusters-on-hdinsight"></a>Usar pacotes externos com blocos de anotações do Jupyter em clusters Apache Spark no HDInsight

Saiba como configurar um [Jupyter Notebook](https://jupyter.org/) no cluster apache Spark no HDInsight para usar pacotes Apache **Maven** externos, contribuídos pela Comunidade, que não estão incluídos de forma integrada no cluster.

Você pode pesquisar o [Repositório do Maven](https://search.maven.org/) para obter uma lista de pacotes que estão disponíveis. Você também pode obter uma lista de pacotes disponíveis de outras fontes. Por exemplo, uma lista completa dos pacotes enviados pela comunidade está disponível em [Pacotes do Spark](https://spark-packages.org/).

Neste artigo, você aprenderá a usar o pacote [Spark-CSV](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) com o Jupyter notebook.

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster do Apache Spark no HDInsight. Para obter instruções, consulte o artigo sobre como [Criar clusters do Apache Spark no Azure HDInsight](apache-spark-jupyter-spark-sql.md).

* Familiaridade com o uso de anotações do Jupyter com Spark no HDInsight. Para obter mais informações, confira [Carregar dados e executar consultas com o Apache Spark no HDInsight](./apache-spark-load-data-run-query.md).

* O [esquema de URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme) do seu armazenamento primário de clusters. Isso seria `wasb://` para o Armazenamento do Azure, `abfs://` para o Azure Data Lake Storage Gen2 ou `adl://` para o Azure Data Lake Storage Gen1. Se a transferência segura estiver habilitada para o Armazenamento do Azure ou para o Data Lake Storage Gen2, o URI será `wasbs://` ou `abfss://`, respectivamente. Confira também [transferência segura](../../storage/common/storage-require-secure-transfer.md).

## <a name="use-external-packages-with-jupyter-notebooks"></a>Usar pacotes externos com notebooks Jupyter

1. Navegue até `https://CLUSTERNAME.azurehdinsight.net/jupyter` onde `CLUSTERNAME` é o nome do seu cluster Spark.

1. Crie um novo bloco de anotações. Selecione **novo** e, em seguida, selecione **Spark**.

    :::image type="content" source="./media/apache-spark-jupyter-notebook-use-external-packages/hdinsight-spark-create-notebook.png " alt-text="Criar uma nova Jupyter Notebook do Spark" border="true":::

1. Um novo bloco de anotações é criado e aberto com o nome Untitled.pynb. Selecione o nome do bloco de anotações na parte superior e insira um nome amigável.

    :::image type="content" source="./media/apache-spark-jupyter-notebook-use-external-packages/hdinsight-spark-name-notebook.png " alt-text="Fornecer um nome para o bloco de anotações" border="true":::

1. Você usará a `%%configure` mágica para configurar o bloco de anotações para usar um pacote externo. Em blocos de notas que usam pacotes externos, não deixe de chamar a mágica `%%configure` na primeira célula de código. Isso garante que o kernel está configurado para usar o pacote antes de iniciar a sessão.

    >[!IMPORTANT]  
    >Se esquecer de configurar o kernel na primeira célula, você poderá usar `%%configure` com o parâmetro `-f`, mas isso reiniciará a sessão e todo o progresso será perdido.

    | Versão do HDInsight | Comando |
    |-------------------|---------|
    | Para HDInsight 3.5 e HDInsight 3.6 | `%%configure`<br>`{ "conf": {"spark.jars.packages": "com.databricks:spark-csv_2.11:1.5.0" }}`|
    |Para HDInsight 3.3 e HDInsight 3.4 | `%%configure` <br>`{ "packages":["com.databricks:spark-csv_2.10:1.4.0"] }`|

1. O snippet de código acima espera as coordenadas do maven para o pacote externo no Repositório Central do Maven. Nesse snippet de código, `com.databricks:spark-csv_2.11:1.5.0` é a coordenada do maven para o pacote **spark-csv**. Veja como você constrói as coordenadas de um pacote.

    a. Localize o pacote no Repositório Maven. Para este artigo, usamos o [Spark-CSV](https://mvnrepository.com/artifact/com.databricks/spark-csv).

    b. No repositório, colete os valores para **GroupId**, **ArtifactId** e **Version**. Certifique-se de que os valores que você coletar correspondam ao cluster. Nesse caso, estamos usando um pacote escalar 2,11 e Spark 1.5.0, mas talvez seja necessário selecionar versões diferentes para a versão escala ou Spark apropriada no seu cluster. Você pode encontrar a versão do Scala no cluster executando `scala.util.Properties.versionString` no kernel Jupyter do Spark ou em Spark submit. Você pode descobrir a versão do Spark no cluster executando `sc.version` em notebooks Jupyter.

    :::image type="content" source="./media/apache-spark-jupyter-notebook-use-external-packages/use-external-packages-with-jupyter.png " alt-text="Usar pacotes externos com Jupyter Notebook" border="true":::

    c. Concatene os três valores, separados por dois pontos (**:**).

    ```scala
    com.databricks:spark-csv_2.11:1.5.0
    ```

1. Execute a célula de código com a mágica `%%configure` . Isso irá configurar a sessão Livy subjacente para usar o pacote fornecido. Nas células subsequentes no bloco de notas, você agora pode usar o pacote conforme mostrado abaixo.

    ```scala
    val df = spark.read.format("com.databricks.spark.csv").
    option("header", "true").
    option("inferSchema", "true").
    load("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
    ```

    Para o HDInsight 3,4 e versões anteriores, você deve usar o trecho a seguir.

    ```scala
    val df = sqlContext.read.format("com.databricks.spark.csv").
    option("header", "true").
    option("inferSchema", "true").
    load("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
    ```

1. Você pode executar os snippets de código, como mostrado abaixo, para exibir os dados do dataframe que você criou na etapa anterior.

    ```scala
    df.show()
   
    df.select("Time").count()
    ```

## <a name="see-also"></a><a name="seealso"></a>Confira também

* [Visão geral: Apache Spark no Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Cenários

* [Apache Spark com BI: execute análise de dados interativa usando o Spark no HDInsight com ferramentas de BI](apache-spark-use-bi-tools.md)
* [Apache Spark com Machine Learning: use o Spark no HDInsight para analisar a temperatura do edifício usando dados de HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark com Machine Learning: use o Spark no HDInsight para prever os resultados da inspeção de alimentos](apache-spark-machine-learning-mllib-ipython.md)
* [Análise de log do site usando o Apache Spark no HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Criar e executar aplicativos

* [Criar um aplicativo autônomo usando Scala](apache-spark-create-standalone-application.md)
* [Execute trabalhos remotamente em um cluster do Apache Spark usando o Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões

* [Usar pacotes python externos com notebooks Jupyter em clusters de Apache Spark no HDInsight Linux](apache-spark-python-package-installation.md)
* [Use o Plug-in de Ferramentas do HDInsight para IntelliJ IDEA para criar e enviar aplicativos Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Use o Plugin do HDInsight Tools para o IntelliJ IDEA para depurar os aplicativos do Apache Spark remotamente](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Use os blocos de anotações do Apache Zeppelin com um cluster do Apache Spark no HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para Jupyter Notebook no cluster Apache Spark para HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Instalar o Jupyter em seu computador e conectar-se a um cluster Spark do HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gerenciar recursos

* [Gerenciar os recursos de cluster do Apache Spark no Azure HDInsight](apache-spark-resource-manager.md)
* [Rastrear e depurar trabalhos em execução em um cluster do Apache Spark no HDInsight](apache-spark-job-debugging.md)
