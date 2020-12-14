---
title: 'Início rápido: introdução à análise com o Spark'
description: Neste tutorial, você aprenderá a analisar dados com o Apache Spark
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: spark
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: ee4dc945f63180fd06f13287b22949d0ac1e3873
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2020
ms.locfileid: "96862013"
---
# <a name="analyze-with-apache-spark"></a>Análise com o Apache Spark

## <a name="analyze-nyc-taxi-data-in-blob-storage-using-spark"></a>Analisar os dados de Táxi de Nova York no armazenamento de blobs usando o Spark

Neste tutorial, você conhecerá as etapas básicas usadas para carregar e analisar dados com o Apache Spark para o Azure Synapse.

1. No hub **Dados**, clique em **Adicionar um novo recurso** (botão de mais em cima de **Vinculado**) >> **Procurar na galeria**. 
1. Encontre **Comissão de Táxi e Limusines de Nova York – registros de viagem de táxi amarelo** e clique nele. 
1. Na parte inferior da página, pressione **Continuar** e, depois disso, **Adicionar conjunto de dados**. 
1. Agora, no hub **Dados**, em **Vinculado**, clique com o botão direito do mouse em **Armazenamento de Blobs do Azure >> Conjuntos de Dados de Exemplo >> nyc_tlc_yellow** e selecione **Novo notebook**
1. Isso criará um Notebook com o seguinte código:
    ```
    from azureml.opendatasets import NycTlcYellow

    data = NycTlcYellow()
    data_df = data.to_spark_dataframe()
    display(data_df.limit(10))
    ```
1. No notebook, escolha um pool do Spark sem servidor no menu **Anexar a**
1. Selecione **Executar** na célula
1. Se você quiser ver apenas o esquema do dataframe, execute uma célula com o seguinte código:
    ```
    data_df.printSchema()
    ```

## <a name="load-the-nyc-taxi-data-into-the-spark-nyctaxi-database"></a>Carregar os dados de táxi de Nova York no banco de dados nyctaxi do Spark

Temos dados disponíveis em uma tabela em **SQLPOOL1**. Carregue-os em um banco de dados do Spark chamado **nyctaxi**.

1. No Synapse Studio, acesse o hub **Desenvolver**.
1. Selecione **+**  > **Notebook**.
1. Na parte superior do notebook, defina o valor **Anexar a** como **Spark1**.
1. Selecione **Adicionar código** para adicionar uma célula de código do notebook e depois cole o seguinte texto:

    ```scala
    %%spark
    spark.sql("CREATE DATABASE IF NOT EXISTS nyctaxi")
    val df = spark.read.sqlanalytics("SQLPOOL1.dbo.Trip") 
    df.write.mode("overwrite").saveAsTable("nyctaxi.trip")
    ```

1. Acesse o hub **Dados**, clique com o botão direito do mouse em **Bancos de dados** e selecione **Atualizar**. Agora você deve ver estes bancos de dados:

    - **SQLPOOL1 (SQL)**
    - **nyctaxi (Spark)**

## <a name="analyze-the-nyc-taxi-data-using-spark-and-notebooks"></a>Analisar os dados de táxi de NYC usando Spark e notebooks

1. Retorne ao notebook.
1. Crie uma célula de código e insira o texto a seguir. Em seguida, execute a célula para mostrar os dados de táxi de Nova York que carregamos no banco de dados **nyctaxi** do Spark.

   ```py
   %%pyspark
   df = spark.sql("SELECT * FROM nyctaxi.trip") 
   display(df)
   ```

1. Execute o código a seguir para realizar a mesma análise que fizemos anteriormente com o pool de SQL dedicado **SQLPOOL1**. Esse código salva os resultados da análise em uma tabela chamada **nyctaxi.passengercountstats** e exibe os resultados.

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

Anteriormente, copiamos dados da tabela do pool de SQL dedicado **SQLPOOL1.dbo.Trip** na tabela do Spark **nyctaxi.trip**. Em seguida, usando o Spark, agregamos os dados na tabela do Spark **nyctaxi.passengercountstats**. Agora, copiaremos os dados de **nyctaxi.passengercountstats** em uma tabela de pools de SQL dedicado chamada **SQLPOOL1.dbo.PassengerCountStats**.

Execute a célula a seguir no seu notebook. Ela copia a tabela do Spark agregada de volta na tabela do pool de SQL dedicado.

```scala
%%spark
val df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df.write.sqlanalytics("SQLPOOL1.dbo.PassengerCountStats", Constants.INTERNAL )
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Analisar dados com o pool de SQL sem servidor](get-started-analyze-sql-on-demand.md)


