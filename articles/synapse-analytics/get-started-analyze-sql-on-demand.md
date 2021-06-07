---
title: 'Tutorial: introdução à análise de dados com um pool de SQL sem servidor'
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
ms.openlocfilehash: 0ef76be2c083d6185b8b919d174de28aa3b65446
ms.sourcegitcommit: af6eba1485e6fd99eed39e507896472fa930df4d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2021
ms.locfileid: "106293763"
---
# <a name="analyze-data-with-a-serverless-sql-pool"></a>Analisar dados com um pool de SQL sem servidor

Neste tutorial, você aprenderá a analisar dados com o pool de SQL sem servidor. 

## <a name="the-built-in-serverless-sql-pool"></a>O pool de SQL sem servidor interno

Os pools de SQL sem servidor permitem que você use o SQL sem precisar reservar a capacidade. A cobrança de um pool de SQL sem servidor se baseia no volume de dados processados para executar a consulta, não no número de nós usados para executá-la.

Todo workspace é fornecido com um pool de SQL sem servidor pré-configurado chamado **Interno**. 

## <a name="analyze-nyc-taxi-data-in-blob-storage-using-serverless-sql-pool"></a>Analisar dados de táxis de Nova York no armazenamento de blobs usando o pool de SQL sem servidor

Nesta seção, você usará um pool de SQL sem servidor para analisar dados de táxi de NYC em uma conta do Armazenamento de Blobs do Azure.

1. No Synapse Studio, acesse o hub **Desenvolver**
1. Crie um script SQL.
1. Cole o código a seguir no script.

    ```
    SELECT
        TOP 100 *
    FROM
        OPENROWSET(
            BULK     'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
            FORMAT = 'parquet'
        ) AS [result];
    ```
1. Clique em **Executar**

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Analisar dados com um Pool do Spark sem servidor](get-started-analyze-spark.md)
