---
title: Usar pacotes personalizados do Maven com o Jupyter no Spark no Azure HDInsight
description: Instruções passo a passo sobre como configurar os blocos de notas do Jupyter disponíveis com clusters Spark HDInsight para usar pacotes personalizados do Maven.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: hrasheed
ms.openlocfilehash: 02b5b7a3673b3df3ba27e7814851e3519e473633
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448720"
---
# <a name="use-external-packages-with-jupyter-notebooks-in-apache-spark-clusters-on-hdinsight"></a>Usar pacotes externos com blocos de anotações Jupyter em clusters Apache Spark no HDInsight
> [!div class="op_single_selector"]
> * [Usando a mágica da célula](apache-spark-jupyter-notebook-use-external-packages.md)
> * [Usando a ação de script](apache-spark-python-package-installation.md)

Saiba como configurar uma [Jupyter Notebook](https://jupyter.org/) no cluster do Apache Spark no HDInsight para usar externos enviados pela comunidade Apache **maven** pacotes que não estão incluídos o out-of-the-box no cluster. 

Você pode pesquisar o [Repositório do Maven](https://search.maven.org/) para obter uma lista de pacotes que estão disponíveis. Você também pode obter uma lista de pacotes disponíveis de outras fontes. Por exemplo, uma lista completa dos pacotes enviados pela comunidade está disponível em [Pacotes do Spark](https://spark-packages.org/).

Neste artigo, você aprenderá a usar o pacote [spark csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) com o bloco de notas Jupyter.

## <a name="prerequisites"></a>Pré-requisitos
Você deve ter o seguinte:

* Um cluster do Apache Spark no HDInsight. Para obter instruções, consulte o artigo sobre como [Criar clusters do Apache Spark no Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="use-external-packages-with-jupyter-notebooks"></a>Usar pacotes externos com blocos de notas Jupyter
1. No [Portal do Azure](https://portal.azure.com/), no quadro inicial, clique no bloco do cluster Spark (se você o tiver fixado no quadro inicial). Você também pode navegar até o cluster em **Procurar Tudo** > **Clusters HDInsight**.   

1. Na folha do cluster Spark, clique em **Links Rápidos** e, na folha **Painel do Cluster**, clique em **Notebook do Jupyter**. Se você receber uma solicitação, insira as credenciais de administrador para o cluster.

    > [!NOTE]  
    > Você também pode acessar o Bloco de Notas Jupyter de seu cluster abrindo a seguinte URL no navegador. Substitua **CLUSTERNAME** pelo nome do cluster:
    > 
    > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

1. Crie um novo bloco de anotações. Clique em **Novo** e em **Spark**.
   
    ![Criar um novo bloco de anotações do Jupyter](./media/apache-spark-jupyter-notebook-use-external-packages/hdinsight-spark-create-notebook.png "Criar um novo bloco de anotações do Jupyter")

1. Um novo bloco de anotações é criado e aberto com o nome Untitled.pynb. Clique no nome do bloco de anotações na parte superior e digite um nome amigável.
   
    ![Fornecer um nome para o bloco de anotações](./media/apache-spark-jupyter-notebook-use-external-packages/hdinsight-spark-name-notebook.png "Fornecer um nome para o bloco de anotações")

1. Você usará a mágica `%%configure` a fim de configurar o bloco de notas para usar um pacote externo. Em blocos de notas que usam pacotes externos, não deixe de chamar a mágica `%%configure` na primeira célula de código. Isso garante que o kernel está configurado para usar o pacote antes de iniciar a sessão.

    >[!IMPORTANT]  
    >Se esquecer de configurar o kernel na primeira célula, você poderá usar `%%configure` com o parâmetro `-f`, mas isso reiniciará a sessão e todo o progresso será perdido.

    | Versão do HDInsight | Comando |
    |-------------------|---------|
    |Para HDInsight 3.3 e HDInsight 3.4 | `%%configure` <br>`{ "packages":["com.databricks:spark-csv_2.10:1.4.0"] }`|
    | Para HDInsight 3.5 e HDInsight 3.6 | `%%configure`<br>`{ "conf": {"spark.jars.packages": "com.databricks:spark-csv_2.10:1.4.0" }}`|

1. O snippet de código acima espera as coordenadas do maven para o pacote externo no Repositório Central do Maven. Nesse snippet de código, `com.databricks:spark-csv_2.10:1.4.0` é a coordenada do maven para o pacote **spark-csv**. Veja como você constrói as coordenadas de um pacote.
   
    a. Localize o pacote no Repositório Maven. Neste artigo, usamos [spark-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar).
   
    b. No repositório, colete os valores para **GroupId**, **ArtifactId** e **Version**. Certifique-se de que os valores que você coletar correspondam ao cluster. Nesse caso, estamos usando um pacote de Spark 1.4.0 e Scala 2.10, mas talvez seja necessário selecionar versões diferentes para a versão apropriada do Spark ou do Scala no cluster. Você pode encontrar a versão do Scala no cluster executando `scala.util.Properties.versionString` no kernel Jupyter do Spark ou em Spark submit. Você pode encontrar a versão do Spark no cluster executando `sc.version` em Notebooks Jupyter.
   
    ![Usar pacotes externos com blocos de anotações do Jupyter](./media/apache-spark-jupyter-notebook-use-external-packages/use-external-packages-with-jupyter.png "Usar pacotes externos com blocos de anotações do Jupyter")
   
    c. Concatene os três valores, separados por dois pontos ( **:** ).
   
        com.databricks:spark-csv_2.10:1.4.0

1. Execute a célula de código com a mágica `%%configure` . Isso irá configurar a sessão Livy subjacente para usar o pacote fornecido. Nas células subsequentes no bloco de notas, você agora pode usar o pacote conforme mostrado abaixo.
   
        val df = sqlContext.read.format("com.databricks.spark.csv").
        option("header", "true").
        option("inferSchema", "true").
        load("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

    Para o HDInsight 3.6, você deve usar o snippet de código a seguir.

        val df = spark.read.format("com.databricks.spark.csv").
        option("header", "true").
        option("inferSchema", "true").
        load("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

1. Você pode executar os snippets de código, como mostrado abaixo, para exibir os dados do dataframe que você criou na etapa anterior.
   
        df.show()
   
        df.select("Time").count()

## <a name="seealso"></a>Consulte também
* [Visão geral: Apache Spark no Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Cenários
* [Apache Spark com BI: Executar análise de dados interativa usando o Spark no HDInsight com ferramentas de BI](apache-spark-use-bi-tools.md)
* [Apache Spark com Machine Learning: Usar o Spark no HDInsight para analisar a temperatura de prédios usando dados do sistema de HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark com Machine Learning: Usar o Spark no HDInsight para prever resultados da inspeção de alimentos](apache-spark-machine-learning-mllib-ipython.md)
* [Análise de log do site usando o Apache Spark no HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Criar e executar aplicativos
* [Criar um aplicativo autônomo usando Scala](apache-spark-create-standalone-application.md)
* [Execute trabalhos remotamente em um cluster do Apache Spark usando o Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões

* [Usar pacotes Python externos com notebooks Jupyter em clusters do Apache Spark no HDInsight Linux](apache-spark-python-package-installation.md)
* [Use o Plug-in de Ferramentas do HDInsight para IntelliJ IDEA para criar e enviar aplicativos Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Use o Plugin do HDInsight Tools para o IntelliJ IDEA para depurar os aplicativos do Apache Spark remotamente](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Use os blocos de anotações do Apache Zeppelin com um cluster do Apache Spark no HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para o notebook Jupyter no cluster do Apache Spark para HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Instalar o Jupyter em seu computador e conectar-se a um cluster Spark do HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gerenciar recursos
* [Gerenciar os recursos de cluster do Apache Spark no Azure HDInsight](apache-spark-resource-manager.md)
* [Rastrear e depurar trabalhos em execução em um cluster do Apache Spark no HDInsight](apache-spark-job-debugging.md)
