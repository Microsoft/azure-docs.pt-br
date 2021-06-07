---
title: Criar e usar exibições no pool de SQL sem servidor
description: Nesta seção, você aprenderá a criar e usar exibições para encapsular consultas do pool de SQL sem servidor. As exibições permitirão que você reutilize essas consultas. As exibições também serão necessárias se você quiser usar ferramentas, como o Power BI, em conjunto com o pool de SQL sem servidor.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 05/20/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 0948c7c82d7577bae07057bff9d1be4d7e09f978
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96462286"
---
# <a name="create-and-use-views-using-serverless-sql-pool-in-azure-synapse-analytics"></a>Criar e usar exibições usando o pool de SQL sem servidor no Azure Synapse Analytics

Nesta seção, você aprenderá a criar e usar exibições para encapsular consultas do pool de SQL sem servidor. As exibições permitirão que você reutilize essas consultas. As exibições também serão necessárias se você quiser usar ferramentas, como o Power BI, em conjunto com o pool de SQL sem servidor.

## <a name="prerequisites"></a>Pré-requisitos

A primeira etapa é criar um banco de dados em que a exibição será criada e inicializar os objetos necessários para autenticar no armazenamento do Azure executando o [script de instalação](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) nesse banco de dados. Todas as consultas neste artigo serão executadas no banco de dados de exemplo.

## <a name="create-a-view"></a>Criar uma exibição

Você pode criar exibições da mesma maneira que cria exibições convencionais do SQL Server. A consulta a seguir cria uma exibição que lê o arquivo *population.csv*.

> [!NOTE]
> Altere a primeira linha da consulta, ou seja, [mydbname], de modo que você esteja usando o banco de dados criado.

```sql
USE [mydbname];
GO

DROP VIEW IF EXISTS populationView;
GO

CREATE VIEW populationView AS
SELECT * 
FROM OPENROWSET(
        BULK 'csv/population/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', 
        FIELDTERMINATOR =',', 
        ROWTERMINATOR = '\n'
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
) AS [r];
```

A exibição neste exemplo usa a função `OPENROWSET`, que usa o caminho absoluto para os arquivos subjacentes. Se tiver `EXTERNAL DATA SOURCE` com uma URL raiz de seu armazenamento, você poderá usar `OPENROWSET` com `DATA_SOURCE` e o caminho de arquivo relativo:

```sql
CREATE VIEW TaxiView
AS SELECT *, nyc.filepath(1) AS [year], nyc.filepath(2) AS [month]
FROM
    OPENROWSET(
        BULK 'parquet/taxi/year=*/month=*/*.parquet',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT='PARQUET'
    ) AS nyc
```

## <a name="use-a-view"></a>Usar uma exibição

Você pode usar exibições em suas consultas da mesma maneira que usa exibições em consultas do SQL Server.

A consulta a seguir demonstra o uso da exibição *population_csv* que criamos em [Criar uma exibição](#create-a-view). Ela retorna nomes de países/regiões com o respectivo número de habitantes em 2019 em ordem decrescente.

> [!NOTE]
> Altere a primeira linha da consulta, ou seja, [mydbname], de modo que você esteja usando o banco de dados criado.

```sql
USE [mydbname];
GO

SELECT
    country_name, population
FROM populationView
WHERE
    [year] = 2019
ORDER BY
    [population] DESC;
```

## <a name="next-steps"></a>Próximas etapas

Para obter informações sobre como consultar diferentes tipos de arquivo, veja os artigos [Consultar um arquivo CSV individual](query-single-csv-file.md), [Consultar arquivos Parquet](query-parquet-files.md) e [Consultar arquivos JSON](query-json-files.md).
