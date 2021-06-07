---
title: Como usar metadados de arquivo em consultas
description: A função OPENROWSET fornece informações de arquivo e caminho sobre cada arquivo usado na consulta de modo a filtrar ou analisar dados com base no nome do arquivo e/ou caminho da pasta.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: d24ae1f42c685589309506b2d5e0eab157b2bc42
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96299608"
---
# <a name="use-file-metadata-in-serverless-sql-pool-queries"></a>Usar metadados de arquivo em consultas de pool de SQL sem servidor

O pool SQL sem servidor pode endereçar vários arquivos e pastas conforme descrito no artigo [pastas de consulta e vários arquivos](query-folders-multiple-csv-files.md) . Neste artigo, você aprenderá a usar informações de metadados sobre nomes de arquivos e pastas nas consultas.

Em alguns casos, pode ser necessário saber qual fonte do arquivo ou pasta se correlaciona com uma linha específica no conjunto de resultados.

Você pode usar as funções `filepath` e `filename` para retornar nomes de arquivo e/ou caminho no conjunto de resultados. Ou você pode usá-los para filtrar dados com base no nome do arquivo e/ou caminho da pasta. Essas funções são descritas na seção de sintaxe de [função filename](query-data-storage.md#filename-function) e [função filepath](query-data-storage.md#filepath-function). Nas seções a seguir, você encontrará descrições breves ao longo de exemplos.

## <a name="prerequisites"></a>Pré-requisitos

A primeira etapa é **criar um banco de dados** com uma fonte de dados que faça referência à conta de armazenamento. Em seguida, inicialize os objetos executando o [script de instalação](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) nesse banco de dados. Esse script de instalação criará as fontes de dados, as credenciais no escopo do banco de dados e os formatos de arquivo externos que são usados nessas amostras.

## <a name="functions"></a>Funções

### <a name="filename"></a>Nome de arquivo

Esta função retorna o nome do arquivo de onde a linha se origina.

O exemplo a seguir lê os arquivos de dados de Táxis amarelos em NYC relativos aos últimos três meses de 2017 e retorna o número de corridas por arquivo. A parte OPENROWSET da consulta especifica quais arquivos serão lidos.

```sql
SELECT
    nyc.filename() AS [filename]
    ,COUNT_BIG(*) AS [rows]
FROM  
    OPENROWSET(
        BULK 'parquet/taxi/year=2017/month=9/*.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) nyc
GROUP BY nyc.filename();
```

O exemplo a seguir mostra como *filename()* pode ser usado na cláusula WHERE para filtrar os arquivos a serem lidos. Ele acessa toda a pasta na parte OPENROWSET da consulta e filtra os arquivos na cláusula WHERE.

Os resultados serão os mesmos do exemplo anterior.

```sql
SELECT
    r.filename() AS [filename]
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
    BULK 'csv/taxi/yellow_tripdata_2017-*.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV',
        PARSER_VERSION = '2.0',
        FIRSTROW = 2) 
        WITH (C1 varchar(200) ) AS [r]
WHERE
    r.filename() IN ('yellow_tripdata_2017-10.csv', 'yellow_tripdata_2017-11.csv', 'yellow_tripdata_2017-12.csv')
GROUP BY
    r.filename()
ORDER BY
    [filename];
```

### <a name="filepath"></a>Filepath

A função filepath retorna um caminho completo ou parcial:

- Quando chamada sem um parâmetro, ela retorna o caminho completo do arquivo de onde a linha se origina. Quando DATA_SOURCE é usado em OPENROWSET, ele retorna o caminho relativo a DATA_SOURCE. 
- Quando chamada com um parâmetro, ela retorna parte do caminho correspondente ao caractere curinga na posição especificada no parâmetro. Por exemplo, o valor de parâmetro 1 retornaria a parte do caminho correspondente ao primeiro caractere curinga.

O exemplo a seguir lê arquivos de dados de Táxis amarelos em NYC relativos aos últimos três meses de 2017. Ele retorna o número de corridas por caminho de arquivo. A parte OPENROWSET da consulta especifica quais arquivos serão lidos.

```sql
SELECT
    r.filepath() AS filepath
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
        BULK 'csv/taxi/yellow_tripdata_2017-1*.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV',
        PARSER_VERSION = '2.0',
        FIRSTROW = 2
    )
    WITH (
        vendor_id INT
    ) AS [r]
GROUP BY
    r.filepath()
ORDER BY
    filepath;
```

O exemplo a seguir mostra como *filepath()* pode ser usado na cláusula WHERE para filtrar os arquivos a serem lidos.

Você pode usar os curingas na parte OPENROWSET da consulta e filtrar os arquivos na cláusula WHERE. Os resultados serão os mesmos do exemplo anterior.

```sql
SELECT
    r.filepath() AS filepath
    ,r.filepath(1) AS [year]
    ,r.filepath(2) AS [month]
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
        BULK 'csv/taxi/yellow_tripdata_*-*.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV',
        PARSER_VERSION = '2.0',        
        FIRSTROW = 2
    )
WITH (
    vendor_id INT
) AS [r]
WHERE
    r.filepath(1) IN ('2017')
    AND r.filepath(2) IN ('10', '11', '12')
GROUP BY
    r.filepath()
    ,r.filepath(1)
    ,r.filepath(2)
ORDER BY
    filepath;
```

## <a name="next-steps"></a>Próximas etapas

No próximo artigo, você aprenderá como [consultar arquivos Parquet](query-parquet-files.md).
