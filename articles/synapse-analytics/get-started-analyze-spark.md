---
title: 'Início rápido: introdução à análise com o Spark'
description: Neste tutorial, você aprenderá a analisar dados com o Apache Spark.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: spark
ms.topic: tutorial
ms.date: 12/31/2020
ms.openlocfilehash: 2feabda5ea3f0c0748b92de9fcb7ef05abbdcf4c
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98209433"
---
# <a name="analyze-with-apache-spark"></a>Análise com o Apache Spark

Neste tutorial, você conhecerá as etapas básicas usadas para carregar e analisar dados com o Apache Spark para o Azure Synapse.

## <a name="analyze-nyc-taxi-data-in-blob-storage-using-spark"></a>Analisar os dados de Táxi de Nova York no armazenamento de blobs usando o Spark

1. No hub **Dados**, clique no botão **+** para **Adicionar um novo recurso** e clique em >> **Procurar na galeria**. 
1. Encontre **Comissão de Táxi e Limusines de Nova York – registros de viagem de táxi amarelo** e clique nele. 
1. Na parte inferior da página, clique em **Continuar** e **Adicionar conjunto de dados**. 
1. Após alguns instantes, no hub **Dados** em **Vinculado**, clique com o botão direito do mouse em **Armazenamento de Blobs do Azure >> Conjuntos de Dados de Exemplo >> nyc_tlc_yellow** e selecione **Novo notebook** e **Carregar no Quadro de Dados**.
1. Isso criará um Notebook com o seguinte código:
    ```

    from azureml.opendatasets import NycTlcYellow

    data = NycTlcYellow()
    data_df = data.to_spark_dataframe()
    # Display 10 rows
    display(data_df.limit(10))
    ```
1. No notebook, no menu **Anexar a**, escolha o Pool do Spark sem servidor **Spark1** que criamos anteriormente.
1. Selecione **Executar** na célula
1. Se você quiser ver apenas o esquema do dataframe, execute uma célula com o seguinte código:
    ```

    data_df.printSchema()
    ```

## <a name="load-the-nyc-taxi-data-into-the-spark-nyctaxi-database"></a>Carregar os dados de táxi de Nova York no banco de dados nyctaxi do Spark

Os dados estão disponíveis em uma tabela no **SQLPOOL1**. Carregue-os em um banco de dados do Spark chamado **nyctaxi**.

1. No Synapse Studio, acesse o hub **Desenvolver**.
1. Selecione **+**  > **Notebook**.
1. Na parte superior do notebook, defina o valor **Anexar a** como **Spark1**.
1. Na primeira célula de código do novo notebook, insira o seguinte código:


    ```scala
    %%spark
    spark.sql("CREATE DATABASE IF NOT EXISTS nyctaxi")
    val df = spark.read.sqlanalytics("SQLPOOL1.dbo.Trip") 
    df.write.mode("overwrite").saveAsTable("nyctaxi.trip")
    ```


1. Execute o script. Isso poderá levar de 2 a 3 minutos.
1. No hub **Dados**, na guia **Workspace**, clique com o botão direito do mouse em **Bancos de Dados** e selecione **Atualizar**. Agora você verá o banco de dados **nyctaxi (Spark)** na lista.


## <a name="analyze-the-nyc-taxi-data-using-spark-and-notebooks"></a>Analisar os dados de táxi de NYC usando Spark e notebooks

1. Retorne ao notebook.
1. Crie uma célula de código e insira o código a seguir. 


   ```py
   %%pyspark
   df = spark.sql("SELECT * FROM nyctaxi.trip") 
   display(df)
   ```

1. Execute a célula para mostrar os dados de Táxis de Nova York que carregamos no banco de dados **nyctaxi** do Spark.
1. Crie uma célula de código e insira o código a seguir. Depois, execute a célula para realizar a mesma análise que fizemos anteriormente com o pool de SQL dedicado **SQLPOOL1**. Esse código salva e exibe os resultados da análise em uma tabela chamada **nyctaxi.passengercountstats**.


   ```py
   %%pyspark
   df = spark.sql("""
      SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
      FROM nyctaxi.trip
      WHERE TripDistanceMiles > 0 AND PassengerCount > 0
      GROUP BY PassengerCount
      ORDER BY PassengerCount
   """) 
   display(df)
   df.write.saveAsTable("nyctaxi.passengercountstats")
   ```

1. Nos resultados da célula, selecione **Gráfico** para visualizar os dados exibidos.

## <a name="load-data-from-a-spark-table-into-a-dedicated-sql-pool-table"></a>Carregar dados de uma tabela do Spark em uma tabela do pool de SQL dedicado

Anteriormente, copiamos dados da tabela do pool de SQL dedicado **SQLPOOL1.dbo.Trip** na tabela do Spark **nyctaxi.trip**. Em seguida, você agregou os dados na tabela do Spark **nyctaxi.passengercountstats**. Agora, você copiará os dados de **nyctaxi.passengercountstats** para uma tabela do pool de SQL dedicado chamada **SQLPOOL1.dbo.PassengerCountStats**.

1. Crie uma célula de código e insira o código a seguir. Execute a célula no notebook. Ela copia a tabela do Spark agregada de volta na tabela do pool de SQL dedicado.

```scala
%%spark
val df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df.write.sqlanalytics("SQLPOOL1.dbo.PassengerCountStats", Constants.INTERNAL )
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Analisar dados com o pool de SQL sem servidor](get-started-analyze-sql-on-demand.md)
