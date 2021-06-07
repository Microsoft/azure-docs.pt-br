---
title: Consultar arquivos CSV usando o pool SQL sem servidor
description: Neste artigo, você aprenderá a consultar arquivos CSV únicos com formatos de arquivo diferentes usando o pool SQL sem servidor.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: f2f0cdf307e91fb40c55d4a98139bad1a5eca886
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96462593"
---
# <a name="query-csv-files"></a>Consultar arquivos CSV

Neste artigo, você aprenderá a consultar um único arquivo CSV usando o pool SQL sem servidor no Azure Synapse Analytics. Arquivos CSV podem ter diferentes formatos: 

- Com e sem linha de cabeçalho
- Com valores delimitados por tabulação e vírgula
- Com terminações de linha de estilo do Windows e Unix
- Com valores citados e não citados e caracteres de escape

Todas essas variações serão abordadas abaixo.

## <a name="quickstart-example"></a>Exemplo de início rápido

`OPENROWSET` a função permite que você leia o conteúdo do arquivo CSV fornecendo a URL para o arquivo.

### <a name="read-a-csv-file"></a>Ler um arquivo CSV

A maneira mais fácil de ver o conteúdo do `CSV` arquivo é fornecer a URL do arquivo para `OPENROWSET` funcionar, especificar csv `FORMAT` e 2,0 `PARSER_VERSION` . Se o arquivo estiver publicamente disponível ou se sua identidade do Azure AD puder acessar esse arquivo, você poderá ver o conteúdo do arquivo usando a consulta como a mostrada no exemplo a seguir:

```sql
select top 10 *
from openrowset(
    bulk 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.csv',
    format = 'csv',
    parser_version = '2.0',
    firstrow = 2 ) as rows
```

A opção `firstrow` é usada para ignorar a primeira linha no arquivo CSV que representa o cabeçalho nesse caso. Verifique se você pode acessar esse arquivo. Se o arquivo estiver protegido com chave SAS ou identidade personalizada, você precisará configurar a [credencial de nível de servidor para logon do SQL](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#server-scoped-credential).

> [!IMPORTANT]
> Se o arquivo CSV contiver caracteres UTF-8, verifique se você está usando um agrupamento de banco de dados UTF-8 (por exemplo, `Latin1_General_100_CI_AS_SC_UTF8` ).
> Uma incompatibilidade entre a codificação de texto no arquivo e o agrupamento pode causar erros de conversão inesperados.
> Você pode alterar facilmente o agrupamento padrão do banco de dados atual usando a seguinte instrução T-SQL: `alter database current collate Latin1_General_100_CI_AI_SC_UTF8`

### <a name="data-source-usage"></a>Uso da fonte de dados

O exemplo anterior usa o caminho completo para o arquivo. Como alternativa, você pode criar uma fonte de dados externa com o local que aponta para a pasta raiz do armazenamento:

```sql
create external data source covid
with ( location = 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases' );
```

Depois de criar uma fonte de dados, você pode usar essa fonte de dados e o caminho relativo para o arquivo na `OPENROWSET` função:

```sql
select top 10 *
from openrowset(
        bulk 'latest/ecdc_cases.csv',
        data_source = 'covid',
        format = 'csv',
        parser_version ='2.0',
        firstrow = 2
    ) as rows
```

Se uma fonte de dados estiver protegida com chave SAS ou identidade personalizada, você poderá configurar [a fonte de dados com a credencial no escopo do banco de dado](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#database-scoped-credential).

### <a name="explicitly-specify-schema"></a>Especificar explicitamente o esquema

`OPENROWSET` permite especificar explicitamente quais colunas você deseja ler na cláusula File using `WITH` :

```sql
select top 10 *
from openrowset(
        bulk 'latest/ecdc_cases.csv',
        data_source = 'covid',
        format = 'csv',
        parser_version ='2.0',
        firstrow = 2
    ) with (
        date_rep date 1,
        cases int 5,
        geo_id varchar(6) 8
    ) as rows
```

Os números após um tipo de dados na `WITH` cláusula representam o índice de coluna no arquivo CSV.

> [!IMPORTANT]
> Se o arquivo CSV contiver caracteres UTF-8, certifique-se de que você está explicilty especificando algum agrupamento UTF-8 (por exemplo `Latin1_General_100_CI_AS_SC_UTF8` ,) para todas as colunas na `WITH` cláusula ou defina algum agrupamento UTF-8 no nível do banco de dados.
> A incompatibilidade entre a codificação de texto no arquivo e o agrupamento pode causar erros de conversão inesperados.
> Você pode alterar facilmente o agrupamento padrão do banco de dados atual usando a seguinte instrução T-SQL: `alter database current collate Latin1_General_100_CI_AI_SC_UTF8`
> Você pode definir facilmente o agrupamento nos tipos de coluna usando a seguinte definição: `geo_id varchar(6) collate Latin1_General_100_CI_AI_SC_UTF8 8`

Nas seções a seguir, você pode ver como consultar vários tipos de arquivos CSV.

## <a name="prerequisites"></a>Pré-requisitos

A primeira etapa é criar um **banco de dados** no qual as tabelas serão criadas. Em seguida, inicialize os objetos executando o [script de instalação](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) nesse banco de dados. Esse script de instalação criará as fontes de dados, as credenciais no escopo do banco de dados e os formatos de arquivo externos que são usados nessas amostras.

## <a name="windows-style-new-line"></a>Nova linha de estilo Windows

A consulta a seguir mostra como ler um arquivo CSV sem uma linha de cabeçalho, com uma nova linha de estilo do Windows e colunas delimitadas por vírgula.

Visualização do arquivo:

![Dez primeiras linhas do arquivo CSV sem cabeçalho e uma nova linha no estilo do Windows.](./media/query-single-csv-file/population.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '\n'
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017;
```

## <a name="unix-style-new-line"></a>Nova linha de estilo UNIX

A consulta a seguir mostra como ler um arquivo CSV sem uma linha de cabeçalho, com uma nova linha de estilo Unix e colunas delimitadas por vírgula. Observe que o arquivo está em um local diferente, quando comparado a outros exemplos.

Visualização do arquivo:

![Primeiras 10 linhas do arquivo CSV sem linha de cabeçalho e com a nova linha de estilo UNIX.](./media/query-single-csv-file/population-unix.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '0x0a'
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017;
```

## <a name="header-row"></a>Linha de cabeçalho

A consulta a seguir mostra como ler um arquivo CSV com uma linha de cabeçalho, com uma nova linha de estilo Unix e colunas delimitadas por vírgula. Observe que o arquivo está em um local diferente, quando comparado a outros exemplos.

Visualização do arquivo:

![Primeiras 10 linhas do arquivo CSV com linha de cabeçalho e com a nova linha de estilo UNIX.](./media/query-single-csv-file/population-unix-hdr.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        FIRSTROW = 2
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017;
```

## <a name="custom-quote-character"></a>Caractere de aspas personalizado

A consulta a seguir mostra como ler um arquivo com uma linha de cabeçalho, com uma nova linha de estilo UNIX, com colunas delimitadas por vírgula e valores entre aspas. Observe que o arquivo está em um local diferente, quando comparado a outros exemplos.

Visualização do arquivo:

![Primeiras 10 linhas do arquivo CSV com a linha de cabeçalho e com os valores entre aspas e a nova linha de estilo Unix.](./media/query-single-csv-file/population-unix-hdr-quoted.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr-quoted/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '0x0a',
        FIRSTROW = 2,
        FIELDQUOTE = '"'
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017;
```

> [!NOTE]
> Essa consulta retornaria os mesmos resultados se o parâmetro FIELDQUOTE fosse omitido, pois FIELDQUOTE tem como valor padrão as aspas duplas.

## <a name="escape-characters"></a>Caracteres de escape

A consulta a seguir mostra como ler um arquivo com uma linha de cabeçalho, com uma nova linha de estilo UNIX, colunas delimitadas por vírgula e um caractere de escape usado pelo delimitador de campo (vírgula) dentro dos valores. Observe que o arquivo está em um local diferente, quando comparado a outros exemplos.

Visualização do arquivo:

![Primeiras 10 linhas do arquivo CSV com a linha de cabeçalho, com o caractere de escape e com a nova linha de estilo UNIX usados pelo delimitador de campo.](./media/query-single-csv-file/population-unix-hdr-escape.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr-escape/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '0x0a',
        FIRSTROW = 2,
        ESCAPECHAR = '\\'
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Slovenia';
```

> [!NOTE]
> Essa consulta falhará se o parâmetro ESCAPECHAR não for especificado, pois a vírgula em "Slov,enia" será tratada como delimitador de campo, em vez de parte do nome do país/região. Nesse caso, "Slov,enia" seria tratada como duas colunas. Ou seja, a linha específica teria uma coluna a mais que as outras linhas, e uma coluna a mais que o definido na cláusula WITH.

### <a name="escape-quoting-characters"></a>Caracteres de aspas de escape

A consulta a seguir mostra como ler um arquivo com uma linha de cabeçalho, com uma nova linha em estilo UNIX, colunas delimitadas por vírgula e um caractere de aspas duplas de escape dentro dos valores. Observe que o arquivo está em um local diferente, quando comparado a outros exemplos.

Visualização do arquivo:

![A consulta a seguir mostra como ler um arquivo com uma linha de cabeçalho, com uma nova linha em estilo UNIX, colunas delimitadas por vírgula e um caractere de aspas duplas de escape dentro dos valores.](./media/query-single-csv-file/population-unix-hdr-escape-quoted.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr-escape-quoted/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '0x0a',
        FIRSTROW = 2
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Slovenia';
```

> [!NOTE]
> O caractere de aspas deve ter escape com outro caractere de aspas. O caractere de aspas poderá aparecer no valor da coluna somente se o valor for encapsulado com caracteres de aspas.

## <a name="tab-delimited-files"></a>Arquivos delimitados por tabulação

A consulta a seguir mostra como ler um arquivo CSV com uma linha de cabeçalho, com uma nova linha de estilo Unix e colunas delimitadas por tabulação. Observe que o arquivo está em um local diferente, quando comparado a outros exemplos.

Visualização do arquivo:

![Primeiras 10 linhas do arquivo CSV com a linha de cabeçalho, com o delimitador por tabulação e a nova linha de estilo Unix.](./media/query-single-csv-file/population-unix-hdr-tsv.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr-tsv/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR ='\t',
        ROWTERMINATOR = '0x0a',
        FIRSTROW = 2
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017
```

## <a name="return-a-subset-of-columns"></a>Retornar um subconjunto de colunas

Até agora, você especificou um esquema de arquivo CSV usando a cláusula WITH e listando todas as colunas. Você só pode especificar as colunas que realmente precisa em sua consulta usando um número ordinal para cada coluna necessária. Colunas não relevantes também deverão ser omitidas.

A consulta a seguir retorna o número de nomes de país/região distintos em um arquivo, especificando apenas as colunas necessárias:

> [!NOTE]
> Dê uma olhada na cláusula WITH na consulta abaixo e observe que há um valor "2" (sem aspas) no final da linha em que coluna *[country_name]* é definida. Isso significa que a coluna *[country_name]* é a segunda coluna no arquivo. A consulta ignorará todas as colunas no arquivo, exceto a segunda.

```sql
SELECT
    COUNT(DISTINCT country_name) AS countries
FROM OPENROWSET(
        BULK 'csv/population/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '\n'
    )
WITH (
    --[country_code] VARCHAR (5),
    [country_name] VARCHAR (100) 2
    --[year] smallint,
    --[population] bigint
) AS [r]
```

## <a name="next-steps"></a>Próximas etapas

Os próximos artigos mostrarão como:

- [Consultar arquivos Parquet](query-parquet-files.md)
- [Consultar pastas e arquivos múltiplos](query-folders-multiple-csv-files.md)
