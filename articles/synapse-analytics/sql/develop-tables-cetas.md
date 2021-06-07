---
title: CETAS (CREATE EXTERNAL TABLE AS SELECT) no SQL do Synapse
description: Como usar CETAS (CREATE EXTERNAL TABLE AS SELECT) com o SQL do Synapse
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 09/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 23b74c06ebd45092b9efe36a870eeac7f6fb828a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101677562"
---
# <a name="cetas-with-synapse-sql"></a>CETAS com Synapse SQL

Você pode usar CETAS (CREATE EXTERNAL TABLE AS SELECT) no pool de SQL dedicado ou no pool de SQL sem servidor para realizar as seguintes tarefas:  

- Criar uma tabela externa
- Exportar, em paralelo, os resultados de uma instrução SELECT do Transact-SQL para:

  - O Hadoop
  - Blob de Armazenamento do Azure
  - Azure Data Lake Storage Gen2

## <a name="cetas-in-dedicated-sql-pool"></a>CETAS no pool de SQL dedicado

Para o uso e a sintaxe do CETAS do pool de SQL dedicado, veja o artigo [CREATE EXTERNAL TABLE AS SELECT](/sql/t-sql/statements/create-external-table-as-select-transact-sql?view=azure-sqldw-latest&preserve-view=true). Além disso, para obter diretrizes sobre o CTAS usando o pool de SQL dedicado, confira o artigo [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true).

## <a name="cetas-in-serverless-sql-pool"></a>CETAS no pool de SQL sem servidor

Ao usar o pool de SQL sem servidor, o CETAS é usado para criar uma tabela externa e exportar os resultados da consulta para o Azure Storage Blob ou o Azure Data Lake Storage Gen2.

## <a name="syntax"></a>Sintaxe

```syntaxsql
CREATE EXTERNAL TABLE [ [database_name  . [ schema_name ] . ] | schema_name . ] table_name
    WITH (
        LOCATION = 'path_to_folder',  
        DATA_SOURCE = external_data_source_name,  
        FILE_FORMAT = external_file_format_name  
)
    AS <select_statement>  
[;]

<select_statement> ::=  
    [ WITH <common_table_expression> [ ,...n ] ]  
    SELECT <select_criteria>
```

## <a name="arguments"></a>Argumentos

*[ [ *database_name* . [ *schema_name* ] . ] | *schema_name* . ] *table_name**

O nome de uma a três partes da tabela a ser criada. Para uma tabela externa, o pool de SQL sem servidor armazena apenas os metadados da tabela. Nenhum dado real é movido ou armazenado no pool de SQL sem servidor.

LOCATION = *'path_to_folder'*

Especifica o local em que gravar os resultados da instrução SELECT na fonte de dados externa. A pasta raiz é o local de dados especificado na fonte de dados externa. LOCATION deve apontar para uma pasta e ter uma / à direita. Exemplo: aggregated_data/

DATA_SOURCE = *external_data_source_name*

Especifica o nome do objeto de fonte de dados externa que contém a localização em que os dados externos serão armazenados. Para criar uma fonte de dados externa, use [CREATE EXTERNAL DATA SOURCE (Transact-SQL)](develop-tables-external-tables.md#create-external-data-source).

FILE_FORMAT = *external_file_format_name*

Especifica o nome do objeto de formato de arquivo externo que contém o formato do arquivo de dados externo. Para criar um formato de arquivo externo, use [CREATE EXTERNAL FILE FORMAT (Transact-SQL)](develop-tables-external-tables.md#create-external-file-format). No momento, só há suporte para formatos de arquivo externo com FORMAT_TYPE=PARQUET e FORMAT_TYPE=DELIMITEDTEXT. A compactação GZip para o formato DELIMITEDTEXT não é compatível.

WITH *<common_table_expression>*

Especifica um conjunto de resultados nomeado temporário, conhecido como uma CTE (expressão de tabela comum). Para obter mais informações, confira [WITH common_table_expression (Transact-SQL)](/sql/t-sql/queries/with-common-table-expression-transact-sql?view=azure-sqldw-latest&preserve-view=true).

SELECT <select_criteria>

Popula a nova tabela com os resultados de uma instrução SELECT. *select_criteria* é o corpo da instrução SELECT que determina quais dados serão copiados para a nova tabela. Para obter informações sobre as instruções SELECT, confira [SELECT (Transact-SQL)](/sql/t-sql/queries/select-transact-sql?view=azure-sqldw-latest&preserve-view=true).

> [!NOTE]
> A cláusula ORDER BY em SELECT não tem suporte para CETAS.

## <a name="permissions"></a>Permissões

Você precisa de permissões para listar o conteúdo da pasta e gravar na pasta LOCATION para que o CETAS funcione.

## <a name="examples"></a>Exemplos

Esses exemplos usam CETAS para salvar a população total agregada por ano e estado em uma pasta aggregated_data que está localizada na fonte de dados population_ds.

Este exemplo conta com a credencial, a fonte de dados e o formato de arquivo externo criados anteriormente. Veja o documento [tabelas externas](develop-tables-external-tables.md). Para salvar os resultados da consulta em uma pasta diferente na mesma fonte de dados, altere o argumento LOCATION. 

Para salvar os resultados em uma conta de armazenamento diferente, crie e use uma fonte de dados diferente para o argumento DATA_SOURCE.

> [!NOTE]
> Os exemplos a seguir usam uma conta de armazenamento do Open Data do Azure pública. Ela é somente leitura. Para executar essas consultas, você precisa fornecer a fonte de dados para a qual você tem permissões de gravação.

```sql
-- use CETAS to export select statement with OPENROWSET result to  storage
CREATE EXTERNAL TABLE population_by_year_state
WITH (
    LOCATION = 'aggregated_data/',
    DATA_SOURCE = population_ds,  
    FILE_FORMAT = census_file_format
)  
AS
SELECT decennialTime, stateName, SUM(population) AS population
FROM
    OPENROWSET(BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=*/*.parquet',
    FORMAT='PARQUET') AS [r]
GROUP BY decennialTime, stateName
GO

-- you can query the newly created external table
SELECT * FROM population_by_year_state
```

O exemplo a seguir usa uma tabela externa como fonte para o CETAS. Ele conta com a credencial, a fonte de dados, o formato de arquivo externo e a tabela externa criados anteriormente. Veja o documento [tabelas externas](develop-tables-external-tables.md).

```sql
-- use CETAS with select from external table
CREATE EXTERNAL TABLE population_by_year_state
WITH (
    LOCATION = 'aggregated_data/',
    DATA_SOURCE = population_ds,  
    FILE_FORMAT = census_file_format
)  
AS
SELECT decennialTime, stateName, SUM(population) AS population
FROM census_external_table
GROUP BY decennialTime, stateName
GO

-- you can query the newly created external table
SELECT * FROM population_by_year_state
```

## <a name="supported-data-types"></a>Tipos de dados com suporte

O CETAS pode ser usado para armazenar conjuntos de resultados com os seguintes tipos de dados SQL:

- binary
- varbinary
- char
- varchar
- NCHAR
- NVARCHAR
- smalldate
- date
- DATETIME
- datetime2
- datetimeoffset
- time
- decimal
- numeric
- FLOAT
- real
- BIGINT
- TINYINT
- SMALLINT
- INT
- BIGINT
- bit
- money
- SMALLMONEY
- UNIQUEIDENTIFIER

> [!NOTE]
> LOBs maiores que 1 MB não podem ser usados com CETAS.

## <a name="next-steps"></a>Próximas etapas

Tente consultar as [Tabelas externas do Apache Spark para o Azure Synapse](develop-storage-files-spark-tables.md).
