---
title: Kit de Ferramentas Cognitivas da Microsoft com Azure HDInsight Spark para aprendizado aprofundado | Microsoft Docs
description: Saiba como o modelo de aprendizado aprofundado treinado das Ferramentas Cognitivas da Microsoft pode ser aplicado a um conjunto de dados usando a API Python Spark em um cluster do Azure HDInsight Spark.
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: nitinme
ms.openlocfilehash: 5364a8fabe3ca5d0fdf9aabe296fef085587604e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-microsoft-cognitive-toolkit-deep-learning-model-with-azure-hdinsight-spark-cluster"></a>Use o modelo de aprendizado profundo treinado das Ferramentas Cognitivas da Microsoft com o cluster do Azure HDInsight Spark

Neste artigo, você executa as seguintes etapas.

1. Executar um script personalizado para instalar o Kit de Ferramentas Cognitivas da Microsoft em um cluster do Azure HDInsight Spark.

2. Carregar um Notebook Jupyter no cluster do Spark para ver como aplicar um modelo de aprendizado profundo treinado do Kit de Ferramentas Cognitivas da Microsoft a arquivos em uma Conta de Armazenamento de Blobs do Azure usando a [API Python Spark (PySpark)](https://spark.apache.org/docs/0.9.0/python-programming-guide.html)

## <a name="prerequisites"></a>Pré-requisitos

* **Uma assinatura do Azure**. Antes de começar este tutorial, você deverá ter uma assinatura do Azure. Consulte [Criar sua conta gratuita do Azure hoje](https://azure.microsoft.com/free).

* **Cluster do Azure HDInsight Spark**. Para este artigo, crie um cluster Spark 2.0. Para obter instruções, consulte [Criar um cluster do Apache Spark no Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).

## <a name="how-does-this-solution-flow"></a>Como é o fluxo dessa solução?

Essa solução é dividida entre este artigo e um Notebook Jupyter que é carregado como parte deste tutorial. Neste artigo, você realiza as seguintes etapas:

* Executar uma ação de script em um cluster HDInsight Spark para instalar pacotes do Python e do Kit de Ferramentas Cognitivas da Microsoft.
* Carregar o Notebook Jupyter que executa a solução no cluster HDInsight Spark.

As seguintes etapas restantes são abordadas no Notebook Jupyter.

- Carregar imagens de exemplo em um Conjunto de dados distribuído resiliente, ou RDD, do Spark
   - Carregar módulos e definir predefinições
   - Baixar o conjunto de dados localmente no cluster do Spark
   - Converter o conjunto de dados em um RDD
- Classificar as imagens usando um modelo treinado do Kit de Ferramentas Cognitivas
   - Baixar o modelo treinado do Kit de Ferramentas Cognitivas no cluster do Spark
   - Definir funções a serem usadas por nós de trabalho
   - Classificar as imagens em nós de trabalho
   - Avaliar a precisão do modelo


## <a name="install-microsoft-cognitive-toolkit"></a>Instalar o Kit de Ferramentas Cognitivas da Microsoft

Você pode instalar o Kit de Ferramentas Cognitivas da Microsoft em um cluster do Spark usando ação de script. A ação de script usa scripts personalizados para instalar componentes no cluster que não estão disponíveis por padrão. Você pode usar o script personalizado do Portal do Azure, usando o SDK .NET do HDInsight ou o Azure PowerShell. Você também pode usar o script para instalar o kit de ferramentas como parte da criação do cluster ou depois que o cluster estiver em funcionamento. 

Neste artigo, usamos o portal para instalar o kit de ferramentas após o cluster ter sido criado. Para ver outras maneiras de executar o script personalizado, consulte [Personalizar os clusters HDInsight usando a Ação de Script](hdinsight-hadoop-customize-cluster-linux.md).

### <a name="using-the-azure-portal"></a>Usando o Portal do Azure

Para obter instruções sobre como usar o Portal do Azure para executar a ação de script, consulte [Personalizar os clusters HDInsight usando a Ação de Script](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation). Certifique-se de fornecer as entradas a seguir para instalar o Kit de Ferramentas Cognitivas da Microsoft.

* Forneça um valor para o nome da ação de script.

* Para uma **URI do script Bash**, insira `https://raw.githubusercontent.com/Azure-Samples/hdinsight-pyspark-cntk-integration/master/cntk-install.sh`.

* Verifique se você executou o script apenas em nós de cabeçalho e de trabalho e desmarque todas as outras caixas de seleção.

* Clique em **Criar**.

## <a name="upload-the-jupyter-notebook-to-azure-hdinsight-spark-cluster"></a>Carregue o Notebook Jupyter para o cluster do Azure HDInsight Spark

Para usar o Kit de Ferramentas Cognitivas da Microsoft com o cluster do Azure HDInsight Spark, você precisa carregar o Notebook Jupyter **CNTK_model_scoring_on_Spark_walkthrough.ipynb** no cluster do Azure HDInsight Spark. Esse bloco de anotações está disponível no GitHub em [https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration](https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration).

1. Clone o repositório do GitHub [https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration](https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration). Para obter instruções para clonar, consulte [Clonando um repositório](https://help.github.com/articles/cloning-a-repository/).

2. No Portal do Azure, abra a folha do cluster do Spark que você já provisionado, clique em **Painel do Cluster** e, em seguida, clique em **Notebook Jupyter**.

    Você também pode iniciar o Notebook Jupyter indo para a URL `https://<clustername>.azurehdinsight.net/jupyter/`. Substitua \<clustername> pelo nome do seu cluster do HDInsight.

3. No Notebook Jupyter, clique em **Carregar** no canto superior direito e, em seguida, navegue até o local no qual você clonou o repositório do GitHub.

    ![Carregar o Notebook Jupyter para o cluster do Azure HDInsight Spark](./media/hdinsight-apache-spark-microsoft-cognitive-toolkit/hdinsight-microsoft-cognitive-toolkit-load-jupyter-notebook.png "Carregar o bloco de anotações do Jupyter para o cluster do Azure HDInsight Spark")

4. Clique em **Carregar** novamente.

5. Após o bloco de anotações ser carregado, clique em seu nome e, em seguida, siga as instruções contidas nele sobre como carregar o conjunto de dados e executar o tutorial.

## <a name="see-also"></a>Consulte também
* [Visão geral: Apache Spark no Azure HDInsight](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>Cenários
* [Spark com BI: executar análise de dados interativa usando o Spark no HDInsight com ferramentas de BI](hdinsight-apache-spark-use-bi-tools.md)
* [Spark com Aprendizado de Máquina: usar o Spark no HDInsight para analisar a temperatura de prédios usando dados do sistema HVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark com Aprendizado de Máquina: usar o Spark no HDInsight para prever resultados da inspeção de alimentos](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Streaming Spark: usar o Spark no HDInsight para a criação de aplicativos de streaming em tempo real](hdinsight-apache-spark-eventhub-streaming.md)
* [Análise de log do site usando o Spark no HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)
* [Análise da telemetria de dados do Application Insight usando o Spark no HDInsight](hdinsight-spark-analyze-application-insight-logs.md)

### <a name="create-and-run-applications"></a>Criar e executar aplicativos
* [Criar um aplicativo autônomo usando Scala](hdinsight-apache-spark-create-standalone-application.md)
* [Executar trabalhos remotamente em um cluster do Spark usando Livy](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões
* [Use o Plug-in de Ferramentas do HDInsight para IntelliJ IDEA para criar e enviar aplicativos Spark Scala](hdinsight-apache-spark-intellij-tool-plugin.md)
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

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-create-storageaccount]:../storage/common/storage-create-storage-account.md
