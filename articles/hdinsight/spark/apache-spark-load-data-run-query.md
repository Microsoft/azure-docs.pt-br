---
title: 'Tutorial: Carregar dados & executar consultas com Apache Spark – Azure HDInsight'
description: Tutorial - Aprenda a carregar dados e executar consultas interativas em clusters do Spark no Azure HDInsight.
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive,mvc
ms.date: 02/12/2020
ms.openlocfilehash: eee93d338a106414235b04380e5a67c772951188
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106062736"
---
# <a name="tutorial-load-data-and-run-queries-on-an-apache-spark-cluster-in-azure-hdinsight"></a>Tutorial: Carregar dados e executar consultas em um cluster Apache Spark no Azure HDInsight

Neste tutorial, você aprenderá a criar um dataframe de um arquivo csv e a executar consultas de SQL Spark interativas em um cluster do [Apache Spark](https://spark.apache.org/) no Azure HDInsight. No Spark, um dataframe é uma coleção distribuída de dados organizados em colunas nomeadas. O dataframe é conceitualmente equivalente a uma tabela em um banco de dados relacional ou uma estrutura de dados em R/Python.

Neste tutorial, você aprenderá como:
> [!div class="checklist"]
> * Criar um dataframe usando um arquivo CSV
> * Executar consultas no dataframe

## <a name="prerequisites"></a>Pré-requisitos

Um cluster do Apache Spark no HDInsight. Veja [Criar um cluster do Apache Spark](./apache-spark-jupyter-spark-sql-use-portal.md).

## <a name="create-a-jupyter-notebook"></a>Criará um Jupyter Notebook

O Jupyter Notebook é um ambiente de notebook interativo que oferece suporte a várias linguagens de programação. O notebook permite que você interaja com seus dados, combine código com texto markdown e execute visualizações simples.

1. Edite a URL `https://SPARKCLUSTER.azurehdinsight.net/jupyter` substituindo `SPARKCLUSTER` com pelo nome do seu cluster do Spark. Em seguida, insira a URL editada em um navegador da Web. Em caso de solicitação, insira as credenciais de logon do cluster.

2. Na página da Web do Jupyter, selecione **Novo** > **PySpark** para criar um notebook.

   :::image type="content" source="./media/apache-spark-load-data-run-query/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png " alt-text="Criar um Jupyter Notebook para executar consultas SQL interativas do Spark" border="true":::

   Um notebook é criado e aberto com o nome Untitled(`Untitled.ipynb`).

    > [!NOTE]  
    > Usando o kernel PySpark para criar um notebook, a sessão `spark` é criada automaticamente quando você executar a primeira célula de código. Você não precisa criar a sessão explicitamente.

## <a name="create-a-dataframe-from-a-csv-file"></a>Criar um dataframe usando um arquivo CSV

Os aplicativos podem criar dataframes diretamente de arquivos ou pastas no armazenamento remoto, como o Armazenamento do Azure ou Azure Data Lake Storage; de uma tabela de Hive; ou de outras fontes de dados compatíveis com o Spark, como o Cosmos DB, BD SQL do Azure, DW, etc. A captura de tela a seguir mostra um instantâneo do arquivo HVAC.csv usado neste tutorial. O arquivo CSV vem com todos os clusters do Spark no HDInsight. Os dados capturam as variações de temperatura de algumas construções.

:::image type="content" source="./media/apache-spark-load-data-run-query/hdinsight-spark-sample-data-interactive-spark-sql-query.png " alt-text="Instantâneo de dados para consulta SQL interativa do Spark" border="true":::

1. Cole o código a seguir em uma célula vazia do Jupyter Notebook e pressione **SHIFT + ENTER** para executar o código. O código importa os tipos obrigatórios necessários para este cenário:

    ```python
    from pyspark.sql import *
    from pyspark.sql.types import *
    ```

    Ao executar uma consulta interativa no Jupyter, a legenda da guia ou da janela do navegador da Web mostra um status **(Ocupado)** ao lado do título do notebook. Você também verá um círculo sólido ao lado do texto **PySpark** no canto superior direito. Após a conclusão do trabalho, isso será alterado para um círculo vazio.

    :::image type="content" source="./media/apache-spark-load-data-run-query/hdinsight-spark-interactive-spark-query-status.png " alt-text="Status de consulta SQL interativa do Spark" border="true":::

1. Anote a ID da sessão retornada. Na imagem acima, a ID da sessão é 0. Se desejar, você poderá recuperar os detalhes da sessão navegando até `https://CLUSTERNAME.azurehdinsight.net/livy/sessions/ID/statements`, em que CLUSTERNAME é o nome do cluster Spark e ID é o número da ID da sessão.

1. Execute o seguinte código para criar um dataframe e uma tabela temporária (**hvac**) executando o código a seguir.

    ```python
    # Create a dataframe and table from sample data
    csvFile = spark.read.csv('/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv', header=True, inferSchema=True)
    csvFile.write.saveAsTable("hvac")
    ```

## <a name="run-queries-on-the-dataframe"></a>Executar consultas no dataframe

Depois que a tabela for criada, você poderá executar uma consulta interativa nos dados.

1. Execute o seguinte código em uma célula vazia do notebook:

    ```sql
    %%sql
    SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"
    ```

   A saída tabular a seguir é exibida.

     :::image type="content" source="./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result.png " alt-text="Saída de tabela de resultado de consulta interativa do Spark" border="true":::

2. Você também pode ver os resultados em outras visualizações. Para ver um gráfico de área para a mesma saída, selecione **Área** e, em seguida, defina outros valores conforme mostrado.

    :::image type="content" source="./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result-area-chart.png " alt-text="Grafo de área do resultado da consulta interativa do Spark" border="true":::

3. Na barra de menus do notebook, navegue até **Arquivo** > **Salvar e Ponto de Verificação**.

4. Se você estiver começando o [próximo tutorial](apache-spark-use-bi-tools.md) agora, deixe o bloco de anotações aberto. Se não estiver, desligue o notebook para lançar os recursos de cluster: na barra de menus do notebook, navegue até **Arquivo** >  **Fechar e Interromper**.

## <a name="clean-up-resources"></a>Limpar os recursos

Com o HDInsight, seus dados e seus Jupyter Notebooks são armazenados no Armazenamento do Azure ou no Azure Data Lake Storage, de modo que você possa excluir com segurança um cluster quando ele não estiver em uso. Você também é cobrado por um cluster HDInsight, mesmo quando ele não está em uso. Como os encargos para o cluster são muitas vezes maiores do que os encargos para armazenamento, faz sentido, do ponto de vista econômico, excluir os clusters quando não estiverem em uso. Se você planeja trabalhar no próximo tutorial imediatamente, convém manter o cluster.

Abra o cluster no portal do Azure e selecione **Excluir**.

:::image type="content" source="./media/apache-spark-load-data-run-query/hdinsight-azure-portal-delete-cluster.png " alt-text="Excluir cluster HDInsight" border="true":::

Também é possível selecionar o nome do grupo de recursos para abrir a página do grupo de recursos, e depois selecionar **Excluir grupo de recursos**. Ao excluir o grupo de recursos, você exclui o cluster Spark do HDInsight e a conta de armazenamento padrão.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a criar um dataframe de um arquivo csv e a executar consultas de SQL Spark interativas em um cluster do Apache Spark no Azure HDInsight. Avance para o próximo artigo para ver como os dados que você registrou no Apache Spark podem ser removidos em uma ferramenta de análise de BI, assim como Power BI.

> [!div class="nextstepaction"]
> [Analisar dados usando ferramentas de BI](apache-spark-use-bi-tools.md)
