---
title: Consultar arquivos parquet usando o pool SQL sem servidor
description: Neste artigo, você aprenderá a consultar os arquivos do parquet usando o pool SQL sem servidor.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: cce4c6aff986c2e8c3d879d962714e13f6b2e7ae
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97694687"
---
# <a name="query-parquet-files-using-serverless-sql-pool-in-azure-synapse-analytics"></a>Consultar arquivos parquet usando o pool SQL sem servidor no Azure Synapse Analytics

Neste artigo, você aprenderá a escrever uma consulta usando o pool SQL sem servidor que lerá os arquivos parquet.

## <a name="quickstart-example"></a>Exemplo de início rápido

`OPENROWSET` a função permite que você leia o conteúdo do arquivo parquet fornecendo a URL para o arquivo.

### <a name="read-parquet-file"></a>Ler arquivo parquet

A maneira mais fácil de ver o conteúdo do `PARQUET` arquivo é fornecer a URL do arquivo para `OPENROWSET` funcionar e especificar parquet `FORMAT` . Se o arquivo estiver publicamente disponível ou se sua identidade do Azure AD puder acessar esse arquivo, você poderá ver o conteúdo do arquivo usando a consulta como a mostrada no exemplo a seguir:

```sql
select top 10 *
from openrowset(
    bulk 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.parquet',
    format = 'parquet') as rows
```

Verifique se você pode acessar esse arquivo. Se o arquivo estiver protegido com a chave SAS ou identidade personalizada do Azure, você precisará configurar a [credencial no nível do servidor para o logon do SQL](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#server-scoped-credential).

> [!IMPORTANT]
> Verifique se você está usando um agrupamento de banco de dados UTF-8 (por exemplo `Latin1_General_100_BIN2_UTF8` ) porque os valores de cadeia de caracteres em arquivos PARQUET são codificados usando a codificação UTF-8.
> Uma incompatibilidade entre a codificação de texto no arquivo PARQUET e o agrupamento pode causar erros de conversão inesperados.
> Você pode alterar facilmente o agrupamento padrão do banco de dados atual usando a seguinte instrução T-SQL: `alter database current collate Latin1_General_100_BIN2_UTF8`

### <a name="data-source-usage"></a>Uso da fonte de dados

O exemplo anterior usa o caminho completo para o arquivo. Como alternativa, você pode criar uma fonte de dados externa com o local que aponta para a pasta raiz do armazenamento e usar essa fonte de dados e o caminho relativo para o arquivo na `OPENROWSET` função:

```sql
create external data source covid
with ( location = 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases' );
go
select top 10 *
from openrowset(
        bulk 'latest/ecdc_cases.parquet',
        data_source = 'covid',
        format = 'parquet'
    ) as rows
```

Se uma fonte de dados estiver protegida com chave SAS ou identidade personalizada, você poderá configurar [a fonte de dados com a credencial no escopo do banco de dado](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#database-scoped-credential).

### <a name="explicitly-specify-schema"></a>Especificar explicitamente o esquema

`OPENROWSET` permite especificar explicitamente quais colunas você deseja ler na cláusula File using `WITH` :

```sql
select top 10 *
from openrowset(
        bulk 'latest/ecdc_cases.parquet',
        data_source = 'covid',
        format = 'parquet'
    ) with ( date_rep date, cases int, geo_id varchar(6) ) as rows
```

> [!IMPORTANT]
> Certifique-se de que você está explicilty especificando algum agrupamento UTF-8 (por exemplo `Latin1_General_100_BIN2_UTF8` ) para todas as colunas de cadeia de caracteres na `WITH` cláusula ou defina algum agrupamento UTF-8 no nível do banco de dados.
> A incompatibilidade entre a codificação de texto no agrupamento de coluna de arquivo e cadeia de caracteres pode causar erros de conversão inesperados.
> Você pode alterar facilmente o agrupamento padrão do banco de dados atual usando a seguinte instrução T-SQL: `alter database current collate Latin1_General_100_BIN2_UTF8`
> Você pode definir facilmente o agrupamento nos tipos de coluna usando a seguinte definição: `geo_id varchar(6) collate Latin1_General_100_BIN2_UTF8`

Nas seções a seguir, você pode ver como consultar vários tipos de arquivos PARQUET.

## <a name="prerequisites"></a>Pré-requisitos

A primeira etapa é criar um **banco de dados** com uma fonte de dados que faça referência à conta de armazenamento [Táxis amarelos em NYC](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/). Em seguida, inicialize os objetos executando o [script de instalação](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) nesse banco de dados. Esse script de instalação criará as fontes de dados, as credenciais no escopo do banco de dados e os formatos de arquivo externos que são usados nestas amostras.

## <a name="dataset"></a>Dataset

Neste exemplo, é usado o conjunto dados de [Táxis amarelos em NYC](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/). A consulta a arquivos Parquet pode ser feita da mesma maneira que os [arquivos CSV](query-parquet-files.md) são lidos. A única diferença é que o parâmetro `FILEFORMAT` deve ser definido como `PARQUET`. Os exemplos neste artigo mostram as especificidades de leitura de arquivos Parquet.

## <a name="query-set-of-parquet-files"></a>Conjunto de consultas de arquivos Parquet

É possível especificar somente as colunas desejadas ao consultar arquivos Parquet.

```sql
SELECT
        YEAR(tpepPickupDateTime),
        passengerCount,
        COUNT(*) AS cnt
FROM  
    OPENROWSET(
        BULK 'puYear=2018/puMonth=*/*.snappy.parquet',
        DATA_SOURCE = 'YellowTaxi',
        FORMAT='PARQUET'
    ) WITH (
        tpepPickupDateTime DATETIME2,
        passengerCount INT
    ) AS nyc
GROUP BY
    passengerCount,
    YEAR(tpepPickupDateTime)
ORDER BY
    YEAR(tpepPickupDateTime),
    passengerCount;
```

## <a name="automatic-schema-inference"></a>Inferência de esquema automática

Não é necessário usar a cláusula OPENROWSET WITH para realizar a leitura de arquivos Parquet. Os nomes de coluna e os tipos de dados são lidos automaticamente por meio dos arquivos Parquet.

O exemplo a seguir mostra os recursos de inferência de esquema automática para arquivos Parquet. Ele retorna o número de linhas em Setembro de 2017 sem especificar um esquema.

> [!NOTE]
> Não é necessário especificar colunas na cláusula OPENROWSET WITH ao realizar a leitura de arquivos Parquet. Nesse caso, o serviço de consulta do pool SQL sem servidor utilizará metadados no arquivo parquet e associará colunas por nome.

```sql
SELECT TOP 10 *
FROM  
    OPENROWSET(
        BULK 'puYear=2018/puMonth=*/*.snappy.parquet',
        DATA_SOURCE = 'YellowTaxi',
        FORMAT='PARQUET'
    ) AS nyc
```

### <a name="query-partitioned-data"></a>Consultar dados particionados

O conjunto de dados fornecido neste exemplo foi dividido (particionado) em subpastas separadas. Com a função de linguagem é possível destinar partições específicas. Este exemplo mostra os valores das tarifas por ano, mês e tipo de pagamento nos três primeiros meses de 2017.

> [!NOTE]
> A consulta do pool SQL sem servidor é compatível com o esquema de particionamento do hive/Hadoop.

```sql
SELECT
        YEAR(tpepPickupDateTime),
        passengerCount,
        COUNT(*) AS cnt
FROM  
    OPENROWSET(
        BULK 'puYear=*/puMonth=*/*.snappy.parquet',
        DATA_SOURCE = 'YellowTaxi',
        FORMAT='PARQUET'
    ) nyc
WHERE
    nyc.filepath(1) = 2017
    AND nyc.filepath(2) IN (1, 2, 3)
    AND tpepPickupDateTime BETWEEN CAST('1/1/2017' AS datetime) AND CAST('3/31/2017' AS datetime)
GROUP BY
    passengerCount,
    YEAR(tpepPickupDateTime)
ORDER BY
    YEAR(tpepPickupDateTime),
    passengerCount;
```

## <a name="type-mapping"></a>Mapeamento de tipo

Para o mapeamento de tipo parquet para o [mapeamento de tipo](develop-openrowset.md#type-mapping-for-parquet)de verificação de tipo nativo do SQL para parquet.

## <a name="next-steps"></a>Próximas etapas

Acesse o próximo artigo para saber como [Consultar tipos aninhados de Parquet](query-parquet-nested-types.md).
