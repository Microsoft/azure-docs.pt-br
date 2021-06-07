---
title: Criar e usar as tabelas externas no pool de SQL sem servidor
description: Nesta seção, você aprenderá a criar e usar tabelas externas no pool de SQL sem servidor.
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick
ms.openlocfilehash: 5dcd4b7b76752ca5396fc68afc8d4c8e4e1edca5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96462303"
---
# <a name="create-and-use-external-tables-using-serverless-sql-pool-in-azure-synapse-analytics"></a>Criar e usar tabelas externas usando o pool de SQL sem servidor no Azure Synapse Analytics

Nesta seção, você aprenderá a criar e usar [tabelas externas](develop-tables-external-tables.md) no pool de SQL sem servidor. As tabelas externas serão úteis quando você desejar controlar o acesso a dados externos no pool de SQL sem servidor e se desejar usar ferramentas, como o Power BI, em conjunto com o pool de SQL sem servidor. As tabelas externas podem acessar dois tipos de armazenamento:
- Armazenamento público, onde os usuários acessam os arquivos de armazenamento público.
- Armazenamento protegido, onde os usuários acessam os arquivos de armazenamento usando a credencial SAS, a identidade do Azure AD ou a Identidade Gerenciada do workspace do Synapse.

## <a name="prerequisites"></a>Pré-requisitos

Sua primeira etapa é criar um banco de dados no qual as tabelas serão criadas. Em seguida, inicialize os objetos executando o [script de instalação](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) nesse banco de dados. Esse script de instalação criará os seguintes objetos que são usados neste exemplo:
- A CREDENCIAL NO ESCOPO DO BANCO DE DADOS `sqlondemand` que permite o acesso a uma conta de armazenamento do Azure `https://sqlondemandstorage.blob.core.windows.net` protegida por SAS.

    ```sql
    CREATE DATABASE SCOPED CREDENTIAL [sqlondemand]
    WITH IDENTITY='SHARED ACCESS SIGNATURE',  
    SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D'
    ```

- A FONTE DE DADOS EXTERNA `sqlondemanddemo` que faz referência à conta de armazenamento de demonstração protegida com a chave SAS, e a FONTE DE DADOS EXTERNA `YellowTaxi` que faz referência à conta de armazenamento do Azure disponível publicamente no local `https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/`.

    ```sql
    CREATE EXTERNAL DATA SOURCE SqlOnDemandDemo WITH (
        LOCATION = 'https://sqlondemandstorage.blob.core.windows.net',
        CREDENTIAL = sqlondemand
    );
    GO
    CREATE EXTERNAL DATA SOURCE YellowTaxi
    WITH ( LOCATION = 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/')
    ```

- Os formatos de arquivo `QuotedCSVWithHeaderFormat` e `ParquetFormat` que descrevem os tipos de arquivo CSV e parquet.

    ```sql
    CREATE EXTERNAL FILE FORMAT QuotedCsvWithHeaderFormat
    WITH (  
        FORMAT_TYPE = DELIMITEDTEXT,
        FORMAT_OPTIONS ( FIELD_TERMINATOR = ',', STRING_DELIMITER = '"', FIRST_ROW = 2   )
    );
    GO
    CREATE EXTERNAL FILE FORMAT ParquetFormat WITH (  FORMAT_TYPE = PARQUET );
    ```

As consultas neste artigo serão executadas no banco de dados de exemplo e usam esses objetos. 

## <a name="create-an-external-table-on-protected-data"></a>Criar uma tabela externa em dados protegidos

Você pode criar tabelas externas que acessam dados em uma conta de armazenamento do Azure que permite o acesso a usuários com alguma identidade ou chave SAS do Azure AD. Você pode criar tabelas externas da mesma maneira que cria tabelas externas convencionais do SQL Server. 

A consulta a seguir cria uma tabela externa que lê o arquivo *population.csv* da conta de armazenamento do Azure da demonstração do SynapseSQL que é referenciada usando a fonte de dados `sqlondemanddemo` e protegida com a credencial no escopo do banco de dados chamada `sqlondemand`. 

A fonte de dados e a credencial no escopo do banco de dados são criadas no [script de instalação](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql).

> [!NOTE]
> Altere a primeira linha da consulta, ou seja, [mydbname], de modo que você esteja usando o banco de dados criado. 

```sql
USE [mydbname];
GO
CREATE EXTERNAL TABLE populationExternalTable
(
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
)
WITH (
    LOCATION = 'csv/population/population.csv',
    DATA_SOURCE = sqlondemanddemo,
    FILE_FORMAT = QuotedCSVWithHeaderFormat
);
```

## <a name="create-an-external-table-on-public-data"></a>Criar uma tabela externa em dados públicos

Você pode criar tabelas externas que leem dados dos arquivos colocados no armazenamento do Azure disponível publicamente. Este [script de instalação](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) criará uma fonte de dados externa pública e uma definição de formato de arquivo Parquet usada na seguinte consulta:

```sql
CREATE EXTERNAL TABLE Taxi (
     vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
     pickup_datetime DATETIME2, 
     dropoff_datetime DATETIME2,
     passenger_count INT,
     trip_distance FLOAT,
     fare_amount FLOAT,
     tip_amount FLOAT,
     tolls_amount FLOAT,
     total_amount FLOAT
) WITH (
         LOCATION = 'puYear=*/puMonth=*/*.parquet',
         DATA_SOURCE = YellowTaxi,
         FILE_FORMAT = ParquetFormat
);
```
## <a name="use-an-external-table"></a>Usar uma tabela externa

Você pode usar [tabelas externas](develop-tables-external-tables.md) nas suas consultas da mesma maneira que as usa em consultas do SQL Server.

A consulta a seguir demonstra isso usando a tabela externa *population* que criamos na seção anterior. Ela retorna nomes de países/regiões com o respectivo número de habitantes em 2019 em ordem decrescente.

> [!NOTE]
> Altere a primeira linha da consulta, ou seja, [mydbname], de modo que você esteja usando o banco de dados criado.

```sql
USE [mydbname];
GO

SELECT
    country_name, population
FROM populationExternalTable
WHERE
    [year] = 2019
ORDER BY
    [population] DESC;
```

## <a name="next-steps"></a>Próximas etapas

Para obter informações sobre como armazenar os resultados de uma consulta no armazenamento, confira o artigo [Armazenar resultados da consulta no armazenamento](../sql/create-external-table-as-select.md).
