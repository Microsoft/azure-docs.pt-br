---
title: 'Tutorial: Introdução à análise de dados em contas de armazenamento'
description: Neste tutorial, você aprenderá a analisar os dados localizados em uma conta de armazenamento.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: tutorial
ms.date: 12/31/2020
ms.openlocfilehash: f18977bb92b37546d5980134cba858b1f76b464c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104720008"
---
# <a name="analyze-data-in-a-storage-account"></a>Analisar dados em uma conta de armazenamento

Neste tutorial, você aprenderá a analisar os dados localizados em uma conta de armazenamento.

## <a name="overview"></a>Visão geral

Até agora nós abordamos cenários em que os dados residem em bancos de dados no workspace. Agora, mostraremos a você como trabalhar com arquivos em contas de armazenamento. Nesse cenário, usaremos a conta de armazenamento primária do workspace e do contêiner que especificamos ao criar o workspace.

* O nome da conta de armazenamento: **contosolake**
* O nome do contêiner na conta de armazenamento: **usuários**

### <a name="create-csv-and-parquet-files-in-your-storage-account"></a>Criar arquivos CSV e Parquet na conta de armazenamento

Execute o código a seguir em um notebook em uma nova célula de código. Ele cria um arquivo CSV e um arquivo Parquet na conta de armazenamento.

```py
%%pyspark
df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df = df.repartition(1) # This ensure we'll get a single file during write()
df.write.mode("overwrite").csv("/NYCTaxi/PassengerCountStats_csvformat")
df.write.mode("overwrite").parquet("/NYCTaxi/PassengerCountStats_parquetformat")
```

### <a name="analyze-data-in-a-storage-account"></a>Analisar dados em uma conta de armazenamento

Você pode analisar os dados na conta do ADLS Gen2 padrão do seu workspace ou vincular uma conta de armazenamento do ADLS Gen2 ou de Blobs ao seu workspace por meio de "**Gerenciar**" > "**Serviços Vinculados**" > "**Novo**" (As etapas abaixo se referem à conta do ADLS Gen2 primária).

1. No Synapse Studio, acesse o hub **Dados** e, em seguida, selecione **Vinculado**.
1. Acesse **Azure Data Lake Storage Gen2** > **myworkspace (Primário – contosolake)** .
1. Selecione **usuários (Primário)** . Você deve ver a pasta **NYCTaxi**. Nela, você verá duas pastas chamadas **PassengerCountStats_csvformat** e **PassengerCountStats_parquetformat**.
1. Abra a pasta **PassengerCountStats_parquetformat**. Dentro dela, você verá um arquivo Parquet com um nome como `part-00000-2638e00c-0790-496b-a523-578da9a15019-c000.snappy.parquet`.
1. Clique com o botão direito do mouse em **.parquet**, selecione **Novo notebook** e **Carregar no DataFrame**. Um notebook será criado com uma célula desta forma:

    ```py
    %%pyspark
    abspath = 'abfss://users@contosolake.dfs.core.windows.net/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet'
    df = spark.read.load(abspath, format='parquet')
    display(df.limit(10))
    ```

1. Anexe-o ao Pool do Spark chamado **Spark1**. Execute a célula.
1. Clique para voltar à pasta **users**. Clique com o botão direito do mouse no arquivo **.parquet** novamente e escolha **Novo script SQL** > **SELECIONAR AS 100 PRIMEIRAS LINHAS**. Ele cria um script SQL como este:

    ```sql
    SELECT 
        TOP 100 *
    FROM OPENROWSET(
        BULK 'https://contosolake.dfs.core.windows.net/users/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet',
        FORMAT='PARQUET'
    ) AS [result]
    ```

    Na janela de script, verifique se o campo **Conectar-se a** está definido como o pool de SQL sem servidor **Interno**.

1. Execute o script.



## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Orquestrar atividades com pipelines](get-started-pipelines.md)
