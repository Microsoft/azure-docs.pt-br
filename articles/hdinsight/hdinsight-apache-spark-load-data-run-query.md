---
title: Executar consultas interativas em um cluster HDInsight Spark do Azure | Microsoft Docs
description: "Guia de início rápido sobre como criar um cluster do Apache Spark no HDInsight."
keywords: "início rápido do spark,spark interativo,consulta interativa,hdinsight spark,azure spark"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: nitinme
ms.openlocfilehash: 7853988f91372f2ab563481e441c0d6db4a7fd96
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="run-interactive-queries-on-an-hdinsight-spark-cluster"></a>Executar consultas interativas em um cluster HDInsight Spark

Neste artigo, você pode usar um bloco de anotações do Jupyter para executar consultas interativas do Spark SQL com relação a um cluster Spark. O bloco de anotações do Jupyter é um aplicativo baseado em navegador que estende a experiência interativa baseada em console para a Web. Para obter mais informações, consulte [O bloco de anotações do Jupyter](http://jupyter-notebook.readthedocs.io/en/latest/notebook.html).

Para este tutorial, você deve usar o kernel **PySpark** do bloco de anotações do Jupyter para executar uma consulta SQL Spark interativa. Blocos de anotações do Jupyter em de clusters HDInsight também dão suporte a dois outros kernels – **PySpark3** e **Spark**. Para saber mais sobre os três kernels e os benefícios de usar o **PySpark**, consulte [Usar kernels de blocos de anotações do Jupyter com clusters do Apache Spark no HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md).

## <a name="prerequisites"></a>Pré-requisitos

* **Um cluster HDInsight Spark do Azure**. Para obter instruções, consulte [Criar um cluster do Apache Spark no Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).

## <a name="create-a-jupyter-notebook-to-run-interactive-queries"></a>Criar um bloco de anotações do Jupyter para executar consultas interativas

Para executar consultas, usamos dados de exemplo que estão disponíveis por padrão no armazenamento associado ao cluster. No entanto, você deve primeiro carregar esses dados no Spark como um dataframe. Quando tiver o dataframe, você poderá executar consultas nele usando o bloco de anotações do Jupyter. Nesta seção, você vê como:

* Registrar um conjunto de dados de exemplo como um dataframe do Spark.
* Executar consultas no dataframe.

1. Abra o [Portal do Azure](https://portal.azure.com/). Se você tiver optado por fixar o cluster ao painel, clique no bloco do cluster no painel para iniciar a folha de cluster.

    Se você não fixar o cluster ao painel, no painel esquerdo, clique em **Clusters HDInsight** e clique no cluster que você criou.

3. Em **Links rápidos**, clique em **Painéis de cluster**e, em seguida, clique em **Anotações do Jupyter**. Se você receber uma solicitação, insira as credenciais de administrador para o cluster.

   ![Abrir um bloco de anotações Jupyter para executar uma consulta interativa SQL do Spark](./media/hdinsight-apache-spark-load-data-run-query/hdinsight-spark-start-jupyter-interactive-spark-sql-query.png "Abrir um bloco de anotações Jupyter para executar uma consulta interativa SQL do Spark")

   > [!NOTE]
   > Você também pode acessar o bloco de anotações Jupyter de seu cluster abrindo a seguinte URL no navegador. Substitua **CLUSTERNAME** pelo nome do cluster:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   >
   >
3. Crie um notebook. Clique em **Novo** e em **PySpark**.

   ![Criar um bloco de anotações Jupyter para executar consulta interativa SQL do Spark](./media/hdinsight-apache-spark-load-data-run-query/hdinsight-spark-create-jupyter-interactive-Spark-SQL-query.png "Criar um bloco de anotações Jupyter para executar uma consulta interativa SQL do Spark")

   Um novo bloco de anotações é criado e aberto com o nome Untitled(Untitled.pynb).

4. Clique no nome do bloco de anotações na parte superior e, se desejar, digite um nome amigável.

    ![Forneça um nome para o bloco de anotações Jupyter para executar a consulta interativa Spark](./media/hdinsight-apache-spark-load-data-run-query/hdinsight-spark-jupyter-notebook-name.png "Forneça um nome para o bloco de anotações Jupyter para executar a consulta interativa Spark")

5. Cole o código a seguir em uma célula vazia e pressione **SHIFT + ENTER** para executar o código. O código importa os tipos obrigatórios necessários para este cenário:

        from pyspark.sql import *
        from pyspark.sql.types import *

    Por ter criado um notebook usando o kernel PySpark, não será necessário criar nenhum contexto explicitamente. Os contextos do Spark e do Hive são criados automaticamente para você ao executar a primeira célula do código.

    ![Status de consulta interativa SQL Spark](./media/hdinsight-apache-spark-load-data-run-query/hdinsight-spark-interactive-spark-query-status.png "Status de consulta interativa SQL Spark")

    Toda vez que você executar uma consulta interativa no Jupyter, o título da janela do navegador da Web mostrará um status **(Ocupado)** com o título do bloco de anotações. Você também verá um círculo sólido ao lado do texto **PySpark** no canto superior direito. Após a conclusão do trabalho, isso será alterado para um círculo vazio.

6. Antes de você carregar os dados em um cluster Spark, examinaremos um instantâneo dele. Os dados de exemplo usados neste tutorial estão disponíveis como um arquivo CSV em todos os clusters HDInsight Spark em **\HdiSamples\HdiSamples\SensorSampleData\hvac\hvac.csv**. Os dados capturam as variações de temperatura de um prédio. Aqui estão as primeiras linhas dos dados.

    ![Instantâneo de dados para consulta SQL interativa do Spark](./media/hdinsight-apache-spark-load-data-run-query/hdinsight-spark-sample-data-interactive-spark-sql-query.png "Instantâneo de dados para consulta SQL interativa do Spark")

6. Crie um dataframe e uma tabela temporária (**hvac**) executando o código a seguir. Para este tutorial, nós não criamos todas as colunas na tabela temporária em comparação com as colunas nos dados brutos de CSV. 

        # Create an RDD from sample data
        hvacText = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

        # Create a schema for our data
        Entry = Row('Date', 'Time', 'TargetTemp', 'ActualTemp', 'BuildingID')

        # Parse the data and create a schema
        hvacParts = hvacText.map(lambda s: s.split(',')).filter(lambda s: s[0] != 'Date')
        hvac = hvacParts.map(lambda p: Entry(str(p[0]), str(p[1]), int(p[2]), int(p[3]), int(p[6])))
        
        # Infer the schema and create a table       
        hvacTable = sqlContext.createDataFrame(hvac)
        hvacTable.registerTempTable('hvactemptable')
        dfw = DataFrameWriter(hvacTable)
        dfw.saveAsTable('hvac')

7. Uma vez criada a tabela, execute a consulta interativa nos dados, use o código a seguir.

        %%sql
        SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"

   Como está usando um kernel PySpark, agora você pode executar diretamente uma consulta interativa SQL na tabela temporária **hvac** que você criou usando a palavra mágica `%%sql`. Para saber mais sobre a palavra mágica `%%sql`, e outras palavras mágicas disponíveis com o kernel PySpark, confira [Kernels disponíveis em notebooks Jupyter com clusters HDInsight Spark](hdinsight-apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).

   A saída tabular a seguir é exibida por padrão.

     ![Tabela de saída do resultado da consulta interativa Spark](./media/hdinsight-apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result.png "Tabela de saída do resultado da consulta interativa Spark")

    Você também pode ver os resultados em outras visualizações. Por exemplo, um gráfico de área para a mesma saída seria semelhante ao seguinte.

    ![Gráfico de área de resultado de consulta interativa do Spark](./media/hdinsight-apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result-area-chart.png "Gráfico de área de resultado de consulta interativa do Spark")

9. Depois de concluir a execução do aplicativo, encerre o notebook para liberar os recursos. Para isso, no menu **Arquivo** do bloco de anotações, clique em **Fechar e Interromper**.

## <a name="next-step"></a>Próxima etapa

Neste artigo, você aprendeu como executar consultas interativas no Spark usando o bloco de anotações do Jupyter. Avance para o próximo artigo para ver como os dados que você registrou no Spark podem ser removidos em uma ferramenta de análise de BI, assim como Power BI e Tableau. 

> [!div class="nextstepaction"]
>[Spark BI usando ferramentas de visualização de dados com o Azure HDInsight](hdinsight-apache-spark-use-bi-tools.md)




