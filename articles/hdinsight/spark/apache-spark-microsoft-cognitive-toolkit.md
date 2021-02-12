---
title: Microsoft Cognitive Toolkit com Apache Spark-HDInsight do Azure
description: Saiba como o modelo de aprendizado aprofundado treinado das Ferramentas Cognitivas da Microsoft pode ser aplicado a um conjunto de dados usando a API Python Spark em um cluster do Azure HDInsight Spark.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/14/2020
ms.openlocfilehash: cddbc4b6a5c7a2c787c8305fdf703e34543746f8
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98929962"
---
# <a name="use-microsoft-cognitive-toolkit-deep-learning-model-with-azure-hdinsight-spark-cluster"></a>Use o modelo de aprendizado profundo treinado das Ferramentas Cognitivas da Microsoft com o cluster do Azure HDInsight Spark

Neste artigo, você executa as seguintes etapas.

1. Execute um script personalizado para instalar [Microsoft cognitive Toolkit](/cognitive-toolkit/) em um cluster Azure HDInsight Spark.

2. Faça upload de um [Jupyter Notebook](https://jupyter.org/) no cluster do [Apache Spark](https://spark.apache.org/) para ver como aplicar um modelo de aprendizado profundo treinado do Microsoft Cognitive Toolkit a arquivos em uma conta do Armazenamento de Blobs do Azure usando a [API Spark Python (PySpark)](https://spark.apache.org/docs/latest/api/python/index.html)

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster do Apache Spark no HDInsight. Veja [Criar um cluster do Apache Spark](./apache-spark-jupyter-spark-sql-use-portal.md).

* Familiaridade com o uso de anotações do Jupyter com Spark no HDInsight. Para obter mais informações, confira [Carregar dados e executar consultas com o Apache Spark no HDInsight](./apache-spark-load-data-run-query.md).

## <a name="how-does-this-solution-flow"></a>Como é o fluxo dessa solução?

Esta solução é dividida entre este artigo e um Jupyter Notebook que você carrega como parte deste artigo. Neste artigo, você realiza as seguintes etapas:

* Executar uma ação de script em um cluster HDInsight Spark para instalar pacotes do Python e do Kit de Ferramentas Cognitivas da Microsoft.
* Carregue o Jupyter Notebook que executa a solução para o cluster HDInsight Spark.

As etapas restantes a seguir são abordadas na Jupyter Notebook.

* Carregue imagens de exemplo em um conjunto de RDDs distribuído e resiliente do Spark.
  * Carregar módulos e definir predefinições.
  * Baixar o conjunto de dados localmente no cluster do Spark.
  * Converter o conjunto de dados em um RDD.
* Classificar as imagens usando um modelo treinado do Cognitive Toolkit.
  * Baixar o modelo treinado do Cognitive Toolkit no cluster do Spark.
  * Definir funções a serem usadas por nós de trabalho.
  * Classificar as imagens em nós de trabalho.
  * Avaliar a precisão do modelo.

## <a name="install-microsoft-cognitive-toolkit"></a>Instalar o Kit de Ferramentas Cognitivas da Microsoft

Você pode instalar o Kit de Ferramentas Cognitivas da Microsoft em um cluster do Spark usando ação de script. A ação de script usa scripts personalizados para instalar componentes no cluster que não estão disponíveis por padrão. Você pode usar o script personalizado do portal do Azure, usando o SDK do .NET do HDInsight ou usando Azure PowerShell. Você também pode usar o script para instalar o kit de ferramentas como parte da criação do cluster ou depois que o cluster estiver em funcionamento.

Neste artigo, usamos o portal para instalar o kit de ferramentas após o cluster ter sido criado. Para ver outras maneiras de executar o script personalizado, consulte [Personalizar os clusters HDInsight usando a Ação de Script](../hdinsight-hadoop-customize-cluster-linux.md).

### <a name="using-the-azure-portal"></a>Usando o portal do Azure

Para obter instruções sobre como usar a portal do Azure para executar a ação de script, consulte [Personalizar clusters HDInsight usando a ação de script](../hdinsight-hadoop-customize-cluster-linux.md#script-action-during-cluster-creation). Certifique-se de fornecer as entradas a seguir para instalar o Kit de Ferramentas Cognitivas da Microsoft. Use os seguintes valores para a ação de script:

|Propriedade |Valor |
|---|---|
|Tipo de script|- Personalizado|
|Nome| Instalar o MCT|
|URI do script Bash|`https://raw.githubusercontent.com/Azure-Samples/hdinsight-pyspark-cntk-integration/master/cntk-install.sh`|
|Tipos de nó:|Cabeçalho, trabalhador|
|Parâmetros|Nenhum|

## <a name="upload-the-jupyter-notebook-to-azure-hdinsight-spark-cluster"></a>Carregar o Jupyter Notebook para Azure HDInsight Spark cluster

Para usar o Microsoft Cognitive Toolkit com o cluster Azure HDInsight Spark, você deve carregar o Jupyter Notebook **CNTK_model_scoring_on_Spark_walkthrough. ipynb** no cluster Azure HDInsight Spark. Este notebook está disponível no GitHub em [https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration](https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration) .

1. Baixar e descompactar [https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration](https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration) .

1. Em um navegador da Web, navegue até `https://CLUSTERNAME.azurehdinsight.net/jupyter`, em que `CLUSTERNAME` é o nome do cluster.

1. No Jupyter Notebook, selecione **carregar** no canto superior direito e, em seguida, navegue até o download e selecione arquivo `CNTK_model_scoring_on_Spark_walkthrough.ipynb` .

    ![Carregar Jupyter Notebook para Azure HDInsight Spark cluster](./media/apache-spark-microsoft-cognitive-toolkit/hdinsight-microsoft-cognitive-toolkit-load-jupyter-notebook.png "Carregar Jupyter Notebook para Azure HDInsight Spark cluster")

1. Selecione **carregar** novamente.

1. Depois que o bloco de anotações for carregado, clique no nome do bloco de anotações e siga as instruções no próprio bloco de anotações sobre como carregar o conjunto de dados e executar o artigo.

## <a name="see-also"></a>Veja também

* [Visão geral: Apache Spark no Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Cenários

* [Apache Spark com BI: execute análise de dados interativa usando o Spark no HDInsight com ferramentas de BI](apache-spark-use-bi-tools.md)
* [Apache Spark com Machine Learning: use o Spark no HDInsight para analisar a temperatura do edifício usando dados de HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark com Machine Learning: use o Spark no HDInsight para prever os resultados da inspeção de alimentos](apache-spark-machine-learning-mllib-ipython.md)
* [Análise de log do site usando o Apache Spark no HDInsight](apache-spark-custom-library-website-log-analysis.md)
* [Análise de dados do Application Insight telemetria usando o Apache Spark no HDInsight](apache-spark-analyze-application-insight-logs.md)

### <a name="create-and-run-applications"></a>Criar e executar aplicativos

* [Criar um aplicativo autônomo usando Scala](apache-spark-create-standalone-application.md)
* [Execute trabalhos remotamente em um cluster do Apache Spark usando o Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões

* [Use o Plug-in de Ferramentas do HDInsight para IntelliJ IDEA para criar e enviar aplicativos Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Use o Plugin do HDInsight Tools para o IntelliJ IDEA para depurar os aplicativos do Apache Spark remotamente](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Use os blocos de anotações do Apache Zeppelin com um cluster do Apache Spark no HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para Jupyter Notebook no cluster Apache Spark para HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Usar pacotes externos com notebooks Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter em seu computador e conectar-se a um cluster Spark do HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gerenciar recursos

* [Gerenciar os recursos de cluster do Apache Spark no Azure HDInsight](apache-spark-resource-manager.md)
* [Rastrear e depurar trabalhos em execução em um cluster do Apache Spark no HDInsight](apache-spark-job-debugging.md)
