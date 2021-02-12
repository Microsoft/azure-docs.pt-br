---
title: 'Tutorial: introdução à análise de dados com o pool de SQL sem servidor'
description: Neste tutorial, você aprenderá a analisar dados com um pool de SQL sem servidor usando dados localizados em bancos de dados do Spark.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: tutorial
ms.date: 12/31/2020
ms.openlocfilehash: c9f8760bd1a7b5d3700f3fdf03331fe7013e116f
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98209399"
---
# <a name="analyze-data-with-serverless-sql-pool-in-azure-synapse-analytics"></a>Analisar dados com o pool de SQL sem servidor no Azure Synapse Analytics

Neste tutorial, você aprenderá a analisar dados com o pool de SQL sem servidor usando dados localizados em bancos de dados do Spark. 

## <a name="analyze-nyc-taxi-data-in-blob-storage-using-serverless-sql-pool"></a>Analisar dados de táxis de Nova York no armazenamento de blobs usando o pool de SQL sem servidor

1. No hub **Dados** em **Vinculado**, clique com o botão direito do mouse em **Armazenamento de Blobs do Azure > Conjuntos de Dados de Exemplo > nyc_tlc_yellow**, **Novo Script SQL** e escolha **SELECIONAR AS 100 PRIMEIRAS LINHAS**
1. Isso criará um script SQL com o seguinte código:

    ```
    -- This is auto-generated code
    SELECT
        TOP 100 *
    FROM
        OPENROWSET(
            BULK     'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
            FORMAT = 'parquet'
        ) AS [result];
    ```
1. Clique em **Executar**

## <a name="analyze-nyc-taxi-data-in-spark-databases-using-serverless-sql-pool"></a>Analisar dados de táxi de NYC em bancos de dados do Spark usando o pool de SQL sem servidor

As tabelas em bancos de dados do Spark são automaticamente visíveis e podem ser consultadas pelo pool de SQL sem servidor.

1. No Synapse Studio, acesse o hub **Desenvolver** e crie um script de SQL.
1. Defina **Conectar-se a** como o pool de SQL sem servidor **Interno**.
1. Cole o texto a seguir no script e execute-o.

    ```sql
    SELECT *
    FROM nyctaxi.dbo.passengercountstats
    ```

    > [!NOTE]
    > Na primeira vez que você executar uma consulta que usa o pool de SQL sem servidor, levará cerca de dez segundos para que o pool de SQL sem servidor reúna os recursos de SQL necessários para executar suas consultas. As consultas subsequentes serão muito mais rápidas.
  


## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Analisar dados no Armazenamento](get-started-analyze-storage.md)
