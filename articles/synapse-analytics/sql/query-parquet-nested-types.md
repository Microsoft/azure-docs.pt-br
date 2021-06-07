---
title: Consultar tipos aninhados do parquet usando o pool SQL sem servidor
description: Neste artigo, você aprenderá a consultar os tipos aninhados do parquet usando o pool SQL sem servidor.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 2ef547bea9ba84665ec7187a5adf2f7a7d87e0a4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "101666324"
---
# <a name="query-nested-types-in-parquet-and-json-files-by-using-serverless-sql-pool-in-azure-synapse-analytics"></a>Consultar tipos aninhados em arquivos parquet e JSON usando o pool SQL sem servidor no Azure Synapse Analytics

Neste artigo, você aprenderá a escrever uma consulta usando o pool SQL sem servidor no Azure Synapse Analytics. A consulta lerá os tipos aninhados parquet.
Tipos aninhados são estruturas complexas que representam objetos ou matrizes. Tipos aninhados podem ser armazenados em: 
- [Parquet](query-parquet-files.md), onde você pode ter várias colunas complexas que contêm matrizes e objetos.
- [Arquivos JSON](query-json-files.md)hierárquicos, nos quais você pode ler um documento JSON complexo como uma única coluna.
- Azure Cosmos DB coleções (atualmente em visualização pública restrita), em que cada documento pode conter propriedades aninhadas complexas.

O pool SQL sem servidor formata todos os tipos aninhados como objetos JSON e matrizes. Portanto, você pode [extrair ou modificar objetos complexos usando funções JSON](/sql/relational-databases/json/validate-query-and-change-json-data-with-built-in-functions-sql-server) ou [analisar dados JSON usando a função OPENJSON](/sql/relational-databases/json/convert-json-data-to-rows-and-columns-with-openjson-sql-server). 

Veja um exemplo de uma consulta que extrai valores escalares e de objeto do arquivo JSON [COVID-19 Open Research DataSet](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/) , que contém objetos aninhados: 

```sql
SELECT
    title = JSON_VALUE(doc, '$.metadata.title'),
    first_author = JSON_QUERY(doc, '$.metadata.authors[0]'),
    first_author_name = JSON_VALUE(doc, '$.metadata.authors[0].first'),
    complex_object = doc
FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/covid19temp/comm_use_subset/pdf_json/000b7d1517ceebb34e1e3e817695b6de03e2fa78.json',
        FORMAT='CSV', FIELDTERMINATOR ='0x0b', FIELDQUOTE = '0x0b', ROWTERMINATOR = '0x0b'
    )
    WITH ( doc varchar(MAX) ) AS docs;
```

A `JSON_VALUE` função retorna um valor escalar do campo no caminho especificado. A `JSON_QUERY` função retorna um objeto formatado como JSON a partir do campo no caminho especificado.

> [!IMPORTANT]
> Este exemplo usa um arquivo do conjunto de COVID-19 Open Research. [Veja a licença e a estrutura dos dados aqui](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/).

## <a name="prerequisites"></a>Pré-requisitos

A primeira etapa é criar um banco de dados onde o DataSource será criado. Em seguida, você inicializará os objetos executando um [script de instalação](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) no banco de dados. O script de instalação criará as fontes de dados, as credenciais no escopo do banco de dado e os formatos de arquivo externo que são usados nos exemplos.

## <a name="project-nested-or-repeated-data"></a>Dados de projeto aninhados ou repetidos

Um arquivo parquet pode ter várias colunas com tipos complexos. Os valores dessas colunas são formatados como texto JSON e retornados como colunas VARCHAR. A consulta a seguir lê o arquivo structExample. parquet e mostra como ler os valores das colunas aninhadas: 

```sql
SELECT
    DateStruct, TimeStruct, TimestampStruct, DecimalStruct, FloatStruct
FROM
    OPENROWSET(
        BULK 'parquet/nested/structExample.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    )
    WITH (
        DateStruct VARCHAR(8000),
        TimeStruct VARCHAR(8000),
        TimestampStruct VARCHAR(8000),
        DecimalStruct VARCHAR(8000),
        FloatStruct VARCHAR(8000)
    ) AS [r];
```

Essa consulta retorna o resultado a seguir. O conteúdo de cada objeto aninhado é retornado como texto JSON.

| DateStruct    | Timestruct    | TimestampStruct   | DecimalStruct | FloatStruct |
| --- | --- | --- | --- | --- |
|{"Date": "2009-04-25"}| {"Hora": "20:51:54.3598000"}|    {"Timestamp": "5501-04-08 12:13:57.4821000"}|    {"Decimal": 11143412.25350}| {"Float": 0.5}|
|{"Date": "1916-04-29"}| {"Hora": "00:16:04.6778000"}|    {"Timestamp": "1990-06-30 20:50:52.6828000"}|    {"Decimal": 1963545.62800}|  {"Float":-2,125}|

A consulta a seguir lê o arquivo justSimpleArray.parquet. Ele projeta todas as colunas do arquivo parquet, incluindo dados aninhados e repetidos.

```sql
SELECT
    SimpleArray
FROM
    OPENROWSET(
        BULK 'parquet/nested/justSimpleArray.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS [r];
```

Essa consulta retornará o seguinte resultado:

|SimpleArray|
| --- |
|[11, 12, 13]|
|[21, 22, 23]|

## <a name="read-properties-from-nested-object-columns"></a>Ler propriedades de colunas de objeto aninhado

A `JSON_VALUE` função permite que você retorne valores de colunas formatadas como texto JSON:

```sql
SELECT
    title = JSON_VALUE(complex_column, '$.metadata.title'),
    first_author_name = JSON_VALUE(complex_column, '$.metadata.authors[0].first'),
    body_text = JSON_VALUE(complex_column, '$.body_text.text'),
    complex_column
FROM
    OPENROWSET( BULK 'https://azureopendatastorage.blob.core.windows.net/covid19temp/comm_use_subset/pdf_json/000b7d1517ceebb34e1e3e817695b6de03e2fa78.json',
                FORMAT='CSV', FIELDTERMINATOR ='0x0b', FIELDQUOTE = '0x0b', ROWTERMINATOR = '0x0b' ) WITH ( complex_column varchar(MAX) ) AS docs;
```

O resultado é mostrado na tabela a seguir:

|título  | first_author_name | body_text | complex_column |
| --- | --- | --- | --- |
| Informações complementares de um epidemiolo de eco... | Julien   | -Figura S1: Phylogeny de... | `{    "paper_id": "000b7d1517ceebb34e1e3e817695b6de03e2fa78",    "metadata": {        "title": "Supplementary Information An eco-epidemiological study of Morbilli-related paramyxovirus infection in Madagascar bats reveals host-switching as the dominant macro-evolutionary mechanism",        "authors": [            {                "first": "Julien"` |

Ao contrário dos arquivos JSON, que na maioria dos casos retornam uma única coluna que contém um objeto JSON complexo, os arquivos parquet podem ter várias colunas complexas. Você pode ler as propriedades de colunas aninhadas usando a `JSON_VALUE` função em cada coluna. `OPENROWSET` permite que você especifique diretamente os caminhos das propriedades aninhadas em uma `WITH` cláusula. Você pode definir os caminhos como o nome de uma coluna ou pode adicionar uma expressão de [caminho JSON](/sql/relational-databases/json/json-path-expressions-sql-server) após o tipo de coluna.

A consulta a seguir lê o arquivo structExample. parquet e mostra como Surface elementos de uma coluna aninhada. Há duas maneiras de fazer referência a um valor aninhado:
- Especificando a expressão de caminho de valor aninhado após a especificação de tipo.
- Ao Formatar o nome da coluna como um caminho aninhado usando "." para fazer referência aos campos.

```sql
SELECT
    *
FROM
    OPENROWSET(
        BULK 'parquet/nested/structExample.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    )
    WITH (
        [DateValue] DATE '$.DateStruct.Date',
        [TimeStruct.Time] TIME,
        [TimestampStruct.Timestamp] DATETIME2,
        DecimalValue DECIMAL(18, 5) '$.DecimalStruct.Decimal',
        [FloatStruct.Float] FLOAT
    ) AS [r];
```

## <a name="access-elements-from-repeated-columns"></a>Acessar elementos de colunas repetidas

A consulta a seguir lê o arquivo justSimpleArray. parquet e usa [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?view=azure-sqldw-latest&preserve-view=true) para recuperar um elemento escalar de dentro de uma coluna repetida, como uma matriz ou um mapa:

```sql
SELECT
    *,
    JSON_VALUE(SimpleArray, '$[0]') AS FirstElement,
    JSON_VALUE(SimpleArray, '$[1]') AS SecondElement,
    JSON_VALUE(SimpleArray, '$[2]') AS ThirdElement
FROM
    OPENROWSET(
        BULK 'parquet/nested/justSimpleArray.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS [r];
```

Eis o resultado:

|SimpleArray    | Nome do primeiro  | Segundoelement | Terceiroelement |
| --- | --- | --- | --- |
| [11, 12, 13] | 11   | 12 | 13 |
| [21, 22, 23] | 21   | 22 | 23 |

## <a name="access-sub-objects-from-complex-columns"></a>Acessar subobjetos de colunas complexas

A consulta a seguir lê o arquivo mapExample. parquet e usa [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?view=azure-sqldw-latest&preserve-view=true) para recuperar um elemento não escalar de dentro de uma coluna repetida, como uma matriz ou um mapa:

```sql
SELECT
    MapOfPersons,
    JSON_QUERY(MapOfPersons, '$."John Doe"') AS [John]
FROM
    OPENROWSET(
        BULK 'parquet/nested/mapExample.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS [r];
```

Você também pode referenciar explicitamente as colunas que deseja retornar em uma `WITH` cláusula:

```sql
SELECT DocId,
    MapOfPersons,
    JSON_QUERY(MapOfPersons, '$."John Doe"') AS [John]
FROM
    OPENROWSET(
        BULK 'parquet/nested/mapExample.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) 
    WITH (DocId bigint, MapOfPersons VARCHAR(max)) AS [r];
```

A estrutura `MapOfPersons` é retornada como uma coluna varchar e formatada como uma cadeia de caracteres JSON.

## <a name="project-values-from-repeated-columns"></a>Valores de projeto de colunas repetidas

Se você tiver uma matriz de valores escalares (por exemplo `[1,2,3]` ) em algumas colunas, poderá expandi-los facilmente e associá-los com a linha principal usando este script:

```sql
SELECT
    SimpleArray, Element
FROM
    OPENROWSET(
        BULK 'parquet/nested/justSimpleArray.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS arrays
    CROSS APPLY OPENJSON (SimpleArray) WITH (Element int '$') as array_values
```

## <a name="next-steps"></a>Próximas etapas

O próximo artigo mostrará como [Consultar arquivos JSON](query-json-files.md).