---
title: Usar o Apache Spark para analisar os dados no Azure Data Lake Store | Microsoft Docs
description: Usar o Power BI para analisar os dados armazenados no Azure Data Lake Store
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 1f174323-c17b-428c-903d-04f0e272784c
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/28/2017
ms.author: nitinme
ms.openlocfilehash: beafd2c65b0506844ff9ea152037ed6f2866e650
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-hdinsight-spark-cluster-to-analyze-data-in-data-lake-store"></a>Usar o cluster Spark HDInsight para analisar dados no Data Lake Store

Neste tutorial, você usará no notebook Jupyter disponível com os clusters Spark HDInsight para executar um trabalho que lê dados de uma conta do Data Lake Store.

## <a name="prerequisites"></a>Pré-requisitos

* Conta do Azure Data Lake Store. Siga as instruções em [Introdução ao Azure Data Lake Store usando o portal do Azure](../data-lake-store/data-lake-store-get-started-portal.md).

* Cluster Spark HDInsight do Azure com o Data Lake Store como armazenamento. Siga as instruções em [Criar um cluster HDInsight com o Data Lake Store usando o portal do Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

    
## <a name="prepare-the-data"></a>Preparar os dados

> [!NOTE]
> Não é necessário realizar essa etapa se você criou o cluster HDInsight com o Data Lake Store como armazenamento padrão. Os processos de criação de cluster adicionam alguns dados de exemplo à conta do Data Lake Store especificada durante a criação do cluster. Vá para a seção [Usar o cluster HDInsight Spark com o Data Lake Store](#use-an-hdinsight-spark-cluster-with-data-lake-store).
>
>

Se você criou um cluster HDInsight com o Data Lake Store como armazenamento adicional e o Azure Storage Blob como armazenamento padrão, copie primeiro alguns dados de exemplo para a conta do Data Lake Store. Use os dados de exemplo do Blob de Armazenamento do Azure associado ao cluster HDInsight. Você pode usar a [ferramenta ADLCopy](http://aka.ms/downloadadlcopy) para fazer isso. Baixe e instale a ferramenta a partir do link.

1. Abra um prompt de comando e navegue até o diretório onde AdlCopy está instalado, normalmente `%HOMEPATH%\Documents\adlcopy`.

2. Execute o seguinte comando para copiar um blob específico do contêiner de origem para um Repositório Data Lake:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>

    Copie o arquivo de exemplo de dados **HVAC.csv** em **/HdiSamples/HdiSamples/SensorSampleData/hvac/** para a conta do Azure Data Lake Store. O trecho de código deve parecer com:

        AdlCopy /Source https://mydatastore.blob.core.windows.net/mysparkcluster/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv /dest swebhdfs://mydatalakestore.azuredatalakestore.net/hvac/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

   > [!WARNING]
   > Verifique se os nomes de arquivo e caminho estão com a capitalização apropriada.
   >
   >
3. Você deverá inserir as credenciais da assinatura do Azure vinculadas à sua conta do Data Lake Store. Você verá um resultado semelhante ao seguinte trecho:

        Initializing Copy.
        Copy Started.
        100% data copied.
        Copy Completed. 1 file copied.

    O arquivo de dados (**HVAC.csv**) será copiado em uma pasta **/hvac** na conta do Data Lake Store.

## <a name="use-an-hdinsight-spark-cluster-with-data-lake-store"></a>Usar um cluster Spark HDInsight com o Data Lake Store

1. No [Portal do Azure](https://portal.azure.com/), no quadro inicial, clique no bloco do cluster Spark (se você o tiver fixado no quadro inicial). Você também pode navegar até o cluster em **Procurar Tudo** > **Clusters HDInsight**.

2. Na folha do cluster Spark, clique em **Links Rápidos** e, na folha **Painel do Cluster**, clique em **Notebook do Jupyter**. Se você receber uma solicitação, insira as credenciais de administrador para o cluster.

   > [!NOTE]
   > Você também pode acessar o Bloco de Notas Jupyter de seu cluster abrindo a seguinte URL no navegador. Substitua **CLUSTERNAME** pelo nome do cluster:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   >
   >

3. Crie um novo bloco de anotações. Clique em **Novo** e em **PySpark**.

    ![Criar um novo bloco de anotações do Jupyter](./media/hdinsight-apache-spark-use-with-data-lake-store/hdinsight-create-jupyter-notebook.png "Criar um novo bloco de anotações do Jupyter")

4. Por ter criado um notebook usando o kernel PySpark, não será necessário criar nenhum contexto explicitamente. Os contextos do Spark e do Hive serão criados automaticamente para você ao executar a primeira célula do código. Você pode começar importando os tipos obrigatórios para este cenário. Para fazer isso, cole o trecho de código a seguir em uma célula vazia e pressione **SHIFT + ENTER**.

        from pyspark.sql.types import *

    Toda vez que você executar um trabalho no Jupyter, o título da janela do navegador da Web mostrará um status **(Ocupado)** com o título do bloco de anotações. Você também verá um círculo sólido ao lado do texto **PySpark** no canto superior direito. Depois que o trabalho for concluído, isso será alterado para um círculo vazio.

     ![Status de um trabalho de bloco de anotações Jupyter](./media/hdinsight-apache-spark-use-with-data-lake-store/hdinsight-jupyter-job-status.png "Status de um trabalho de bloco de anotações Jupyter")

5. Carregue os dados de exemplo em uma tabela temporária usando o arquivo **HVAC.csv** copiado para a conta do Data Lake Store. Você pode acessar os dados na conta do Repositório Data Lake usando o seguinte padrão de URL.

    * Se o Data Lake Store for seu armazenamento padrão, HVAC.csv estará em um caminho semelhante à URL a seguir:

            adl://<data_lake_store_name>.azuredatalakestore.net/<cluster_root>/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv

        Ou, você também pode usar um formato reduzido como o seguinte:

            adl:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv

    * Se o Data Lake Store for seu armazenamento adicional, HVAC.csv estará no local onde você o copiou, por exemplo:

            adl://<data_lake_store_name>.azuredatalakestore.net/<path_to_file>

     Em uma célula vazia, cole o seguinte exemplo de código, substitua **MYDATALAKESTORE** pelo nome de sua conta do Data Lake Store e pressione **Shift+Enter**. Esse exemplo de código registra os dados em uma tabela temporária chamada **hvac**.

            # Load the data. The path below assumes Data Lake Store is default storage for the Spark cluster
            hvacText = sc.textFile("adl://MYDATALAKESTORE.azuredatalakestore.net/cluster/mysparkcluster/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

            # Create the schema
            hvacSchema = StructType([StructField("date", StringType(), False),StructField("time", StringType(), False),StructField("targettemp", IntegerType(), False),StructField("actualtemp", IntegerType(), False),StructField("buildingID", StringType(), False)])

            # Parse the data in hvacText
            hvac = hvacText.map(lambda s: s.split(",")).filter(lambda s: s[0] != "Date").map(lambda s:(str(s[0]), str(s[1]), int(s[2]), int(s[3]), str(s[6]) ))

            # Create a data frame
            hvacdf = sqlContext.createDataFrame(hvac,hvacSchema)

            # Register the data fram as a table to run queries against
            hvacdf.registerTempTable("hvac")

6. Como está usando um kernel PySpark, agora você pode executar diretamente uma consulta SQL na tabela temporária **hvac** que acabou de criar usando a mágica de `%%sql`. Para obter mais informações sobre a mágica de `%%sql` , bem como outras mágicas disponíveis com o kernel PySpark, confira [Kernels disponíveis em notebooks Jupyter com clusters HDInsight Spark](hdinsight-apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).

        %%sql
        SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"

7. Depois que o trabalho for concluído com êxito, a saída tabular a seguir será exibida por padrão.

      ![Saída do resultado de consulta de tabela](./media/hdinsight-apache-spark-use-with-data-lake-store/jupyter-tabular-output.png "Saída do resultado de consulta de tabela")

     Você também pode ver os resultados em outras visualizações. Por exemplo, um gráfico de área para a mesma saída seria semelhante ao seguinte.

     ![Gráfico de área de resultado da consulta](./media/hdinsight-apache-spark-use-with-data-lake-store/jupyter-area-output.png "Gráfico de área de resultado da consulta")

8. Depois de concluir a execução do aplicativo, você deve encerrar o notebook para liberar os recursos. Para isso, no menu **Arquivo** do bloco de anotações, clique em **Fechar e Interromper**. Isso desligará e fechará o bloco de anotações.


## <a name="next-steps"></a>Próximas etapas

* [Criar um aplicativo Scala autônomo para ser executado no cluster do Apache Spark](hdinsight-apache-spark-create-standalone-application.md)
* [Usar as Ferramentas do HDInsight no Kit de Ferramentas do Azure para IntelliJ a fim de criar aplicativos Spark para um cluster HDInsight Spark Linux](hdinsight-apache-spark-intellij-tool-plugin.md)
* [Usar as Ferramentas do HDInsight no Kit de Ferramentas do Azure para Eclipse a fim de criar aplicativos Spark para um cluster HDInsight Spark Linux](hdinsight-apache-spark-eclipse-tool-plugin.md)
