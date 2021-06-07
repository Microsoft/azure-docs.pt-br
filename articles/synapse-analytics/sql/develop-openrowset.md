---
title: Como usar a OPENROWSET no pool de SQL sem servidor
description: Este artigo descreve a sintaxe da OPENROWSET no pool de SQL sem servidor e explica como usar argumentos.
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 05/07/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: a6af8f4600544007a9ecb4fcff5ac9016fd7dfbf
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105640341"
---
# <a name="how-to-use-openrowset-using-serverless-sql-pool-in-azure-synapse-analytics"></a>Como usar a OPENROWSET usando o pool de SQL sem servidor no Azure Synapse Analytics

A função `OPENROWSET(BULK...)` permite que você acesse arquivos no Armazenamento do Azure. A função `OPENROWSET` lê o conteúdo de uma fonte de dados remota (por exemplo, o arquivo) e retorna o conteúdo como um conjunto de linhas. No recurso do pool de SQL sem servidor, o provedor de conjuntos de linhas em massa da OPENROWSET é acessado por meio de uma chamada à função OPENROWSET e da especificação da opção BULK.  

A função `OPENROWSET` pode ser referenciada na cláusula `FROM` de uma consulta como se fosse um nome de tabela `OPENROWSET`. Ela também dá suporte a operações em massa por meio de um provedor BULK interno que permite que os dados de um arquivo sejam lidos e retornados como um conjunto de linhas.

## <a name="data-source"></a>Fonte de dados

A função OPENROWSET no SQL do Synapse lê o conteúdo dos arquivos de uma fonte de dados. A fonte de dados é uma conta de armazenamento do Azure e pode ser explicitamente referenciada na função `OPENROWSET` ou dinamicamente inferida com base na URL dos arquivos que você deseja ler.
A função `OPENROWSET` pode opcionalmente conter um parâmetro `DATA_SOURCE` para especificar a fonte de dados que contém arquivos.
- `OPENROWSET` sem `DATA_SOURCE` pode ser usado para ler diretamente o conteúdo dos arquivos do local da URL especificado como opção `BULK`:

    ```sql
    SELECT *
    FROM OPENROWSET(BULK 'http://<storage account>.dfs.core.windows.net/container/folder/*.parquet',
                    FORMAT = 'PARQUET') AS file
    ```

essa é uma forma rápida e fácil de ler o conteúdo dos arquivos sem pré-configuração. Essa opção permite usar a opção de autenticação básica para acessar o armazenamento (passagem do Azure AD para logons do Azure AD e token SAS para logons do SQL). 

- `OPENROWSET` com `DATA_SOURCE` pode ser usado para acessar arquivos na conta de armazenamento especificada:

    ```sql
    SELECT *
    FROM OPENROWSET(BULK '/folder/*.parquet',
                    DATA_SOURCE='storage', --> Root URL is in LOCATION of DATA SOURCE
                    FORMAT = 'PARQUET') AS file
    ```


    essa opção permite que você configure o local da conta de armazenamento na fonte de dados e especifique o método de autenticação que deve ser usado para acessar o armazenamento. 
    
    > [!IMPORTANT]
    > `OPENROWSET` sem `DATA_SOURCE` oferece uma forma rápida e fácil de acessar os arquivos de armazenamento, mas oferece opções de autenticação limitadas. Por exemplo, as entidades de segurança do Azure AD só podem acessar arquivos usando a respectiva [identidade do Azure AD](develop-storage-files-storage-access-control.md?tabs=user-identity) ou arquivos publicamente disponíveis. Se você precisar de opções de autenticação mais avançadas, use a opção `DATA_SOURCE` e defina a credencial que deseja usar para acessar o armazenamento.


## <a name="security"></a>Segurança

Um usuário de banco de dados deve ter a permissão `ADMINISTER BULK OPERATIONS` para usar a função `OPENROWSET`.

O administrador de armazenamento também deve permitir que um usuário acesse os arquivos fornecendo um token SAS válido ou habilitando a entidade de segurança do Azure AD para acessar os arquivos de armazenamento. Saiba mais sobre o controle de acesso de armazenamento [neste artigo](develop-storage-files-storage-access-control.md).

`OPENROWSET` usa as regras a seguir para determinar como se autenticar no armazenamento:
- No `OPENROWSET` sem `DATA_SOURCE`, o mecanismo de autenticação depende do tipo de chamador.
  - Qualquer usuário pode usar `OPENROWSET` sem `DATA_SOURCE` para ler arquivos publicamente disponíveis no Armazenamento do Azure.
  - Os logons do Azure AD podem acessar arquivos protegidos usando as próprias [identidades do Azure AD](develop-storage-files-storage-access-control.md?tabs=user-identity#supported-storage-authorization-types) se o Armazenamento do Azure permite que o usuário do Azure AD Acesse arquivos subjacentes (por exemplo, se o chamador tem a permissão `Storage Reader` no Armazenamento do Azure).
  - Os logons do SQL também podem usar `OPENROWSET` sem `DATA_SOURCE` para acessar arquivos publicamente disponíveis, arquivos protegidos usando o token SAS ou a Identidade Gerenciada do workspace do Synapse. Você precisaria [criar uma credencial com escopo de servidor](develop-storage-files-storage-access-control.md#examples) para permitir o acesso a arquivos de armazenamento. 
- Em `OPENROWSET` com `DATA_SOURCE`, o mecanismo de autenticação é definido na credencial no escopo do banco de dados atribuída à fonte de dados referenciada. Essa opção permite que você acesse o armazenamento disponível publicamente ou o armazenamento usando o token SAS, a Identidade Gerenciada do workspace ou a [identidade do Azure AD do chamador](develop-storage-files-storage-access-control.md?tabs=user-identity#supported-storage-authorization-types) (se o chamador for a entidade de segurança do Azure AD). Se `DATA_SOURCE` referenciar ao armazenamento do Azure que não é público, você precisará [criar uma credencial com escopo de banco de dados](develop-storage-files-storage-access-control.md#examples) e referenciá-la no `DATA SOURCE` para permitir o acesso aos arquivos de armazenamento.

O chamador deve ter a permissão `REFERENCES` na credencial para usá-la para autenticar-se no armazenamento.

## <a name="syntax"></a>Sintaxe

```syntaxsql
--OPENROWSET syntax for reading Parquet files
OPENROWSET  
( { BULK 'unstructured_data_path' , [DATA_SOURCE = <data source name>, ]
    FORMAT='PARQUET' }  
)  
[WITH ( {'column_name' 'column_type' }) ]
[AS] table_alias(column_alias,...n)

--OPENROWSET syntax for reading delimited text files
OPENROWSET  
( { BULK 'unstructured_data_path' , [DATA_SOURCE = <data source name>, ] 
    FORMAT = 'CSV'
    [ <bulk_options> ] }  
)  
WITH ( {'column_name' 'column_type' [ 'column_ordinal' | 'json_path'] })  
[AS] table_alias(column_alias,...n)
 
<bulk_options> ::=  
[ , FIELDTERMINATOR = 'char' ]    
[ , ROWTERMINATOR = 'char' ] 
[ , ESCAPE_CHAR = 'char' ] 
[ , FIRSTROW = 'first_row' ]     
[ , FIELDQUOTE = 'quote_characters' ]
[ , DATA_COMPRESSION = 'data_compression_method' ]
[ , PARSER_VERSION = 'parser_version' ]
[ , HEADER_ROW = { TRUE | FALSE } ]
[ , DATAFILETYPE = { 'char' | 'widechar' } ]
[ , CODEPAGE = { 'ACP' | 'OEM' | 'RAW' | 'code_page' } ]
```

## <a name="arguments"></a>Argumentos

Você tem duas opções para arquivos de entrada que contêm os dados de destino para consulta. Os valores válidos são:

- 'CSV': inclui qualquer arquivo de texto delimitado com separadores de linha/coluna. Qualquer caractere pode ser usado como um separador de campo, como TSV: FIELDTERMINATOR = guia.

- 'PARQUET': arquivo binário no formato Parquet 

**'unstructured_data_path'**

O unstructured_data_path que estabelece um caminho para os dados pode ser um caminho absoluto ou relativo:
- O caminho absoluto no formato '\<prefix>://\<storage_account_path>/\<storage_path>' permite que um usuário leia os arquivos diretamente.
- Caminho relativo no formato '<caminho_de_armazenamento>' que deve ser usado com o parâmetro `DATA_SOURCE` e descreve o padrão de arquivo dentro do local <caminho_da_conta_de_armazenamento> definido no `EXTERNAL DATA SOURCE`. 

Abaixo você encontrará os valores <storage account path> relevantes que serão vinculados à fonte de dados externa específica. 

| Fonte de dados externa       | Prefixo | Caminho da conta de armazenamento                                 |
| -------------------------- | ------ | ---------------------------------------------------- |
| Armazenamento do Blobs do Azure         | http[s]  | \<storage_account>.blob.core.windows.net/path/file   |
| Armazenamento do Blobs do Azure         | wasb[s]  | \<container>@\<storage_account>.blob.core.windows.net/path/file |
| Azure Data Lake Storage Gen1 | http[s]  | \<storage_account>.azuredatalakestore.net/webhdfs/v1 |
| Azure Data Lake Storage Gen2 | http[s]  | \<storage_account>.dfs.core.windows.net /path/file   |
| Azure Data Lake Storage Gen2 | abfs[s]  | [\<file_system>@\<account_name>.dfs.core.windows.net/path/file](../../storage/blobs/data-lake-storage-introduction-abfs-uri.md#uri-syntax)              |
||||

'\<storage_path>'

Especifica um caminho no armazenamento que aponta para a pasta ou o arquivo que você deseja ler. Se o caminho apontar para um contêiner ou uma pasta, todos os arquivos serão lidos nessa pasta ou nesse contêiner específico. Os arquivos nas subpastas não serão incluídos. 

Use curingas para direcionar vários arquivos ou várias pastas. O uso de vários curingas não consecutivos é permitido.
Veja abaixo um exemplo que lê todos os arquivos *csv* que começam com *population* de todas as pastas que começam com */csv/population*:  
`https://sqlondemandstorage.blob.core.windows.net/csv/population*/population*.csv`

Se você especificar o unstructured_data_path para ser uma pasta, uma consulta do pool de SQL sem servidor vai recuperar os arquivos dessa pasta. 

Você pode instruir o pool de SQL sem servidor a percorrer as pastas especificando /* ao final do caminho, como no exemplo: `https://sqlondemandstorage.blob.core.windows.net/csv/population/**`

> [!NOTE]
> Ao contrário do Hadoop e do PolyBase, o pool de SQL sem servidor não retorna subpastas, a menos que você especifique /** ao final do caminho. Além disso, ao contrário do Hadoop e do PolyBase, o pool de SQL sem servidor retorna os arquivos cujo nome começa com um sublinhado (_) ou um ponto final (.).

No exemplo abaixo, se o unstructured_data_path=`https://mystorageaccount.dfs.core.windows.net/webdata/`, uma consulta do pool de SQL sem servidor retornará linhas do mydata.txt e do _hidden.txt. Ele não retornará mydata2.txt e mydata3.txt porque eles estão localizados em uma subpasta.

![Dados recursivos para tabelas externas](./media/develop-openrowset/folder-traversal.png)

`[WITH ( {'column_name' 'column_type' [ 'column_ordinal'] }) ]`

A cláusula WITH permite que você especifique as colunas que deseja ler dos arquivos.

- Em arquivos de dados CSV, para ler todas as colunas, forneça nomes de colunas e os respectivos tipos de dados. Se você quiser um subconjunto de colunas, use números ordinais para escolher as colunas dos arquivos de dados de origem por ordinal. As colunas serão vinculadas à designação ordinal. Se HEADER_ROW = TRUE for usado, então a associação de coluna será feita pelo nome da coluna em vez da posição ordinal.
    > [!TIP]
    > Também é possível omitir a cláusula WITH para arquivos CSV. Os tipos de dados serão inferidos automaticamente do conteúdo do arquivo. Você pode usar o argumento HEADER_ROW para especificar a existência da linha de cabeçalho e, nesse caso, os nomes das colunas serão lidos dessa linha. Para obter detalhes, confira a [descoberta automática de esquema](#automatic-schema-discovery).
    
- Em arquivos de dados Parquet, forneça nomes de colunas que correspondam aos nomes de colunas nos arquivos de dados de origem. As colunas serão vinculadas ao nome e diferenciarão maiúsculas de minúsculas. Se a cláusula WITH for omitida, todas as colunas de arquivos Parquet serão retornadas.
    > [!IMPORTANT]
    > Os nomes de coluna nos arquivos Parquet diferenciam maiúsculas de minúsculas. Se você especificar o nome da coluna com as configurações de maiúsculas e minúsculas diferentes do nome da coluna no arquivo Parquet, valores nulos serão retornados para essa coluna.


column_name = nome da coluna de saída. Se fornecido, esse nome substitui o nome da coluna no arquivo de origem e o nome da coluna fornecido no caminho JSON, se houver um. Se json_path não for fornecido, ele será adicionado automaticamente como '$.column_name'. Verifique o argumento json_path para ver o comportamento.

column_type = tipo de dados da coluna de saída. A conversão de tipo de dados implícita ocorrerá aqui.

column_ordinal = número ordinal da coluna nos arquivos de origem. Esse argumento é ignorado em arquivos Parquet, pois a associação é feita por nome. O seguinte exemplo retornará uma segunda coluna somente de um arquivo CSV:

```sql
WITH (
    --[country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2 2
    --[year] smallint,
    --[population] bigint
)
```

json_path = [expressão do caminho JSON](/sql/relational-databases/json/json-path-expressions-sql-server?view=azure-sqldw-latest&preserve-view=true) para a coluna ou propriedade aninhada. O [modo de caminho](/sql/relational-databases/json/json-path-expressions-sql-server?view=azure-sqldw-latest&preserve-view=true#PATHMODE) padrão é incerto.

> [!NOTE]
> Em modo estrito, a consulta falhará com erro se o caminho fornecido não existir. No modo incerto, a consulta será bem-sucedida e a expressão de caminho JSON for avaliada como NULL.

**\<bulk_options>**

FIELDTERMINATOR ='field_terminator'

Especifica o terminador de campo a ser usado. O terminador de campo padrão é uma vírgula (" **,** ").

ROWTERMINATOR ='row_terminator'`

Especifica o terminador de linha a ser usado. Se o terminador de linha não for especificado, um dos terminadores padrão será usado. Os terminadores padrão para PARSER_VERSION = '1.0' são \r\n, \n e \r. Os terminadores padrão para PARSER_VERSION = '2.0' são \r\n e \n.

ESCAPE_CHAR = 'char'

Especifica o caractere no arquivo que é usado para fazer escape dele mesmo e de todos os valores delimitadores no arquivo. Se o caractere de escape for seguido por um valor diferente dele mesmo ou por um dos valores delimitadores, o caractere de escape será removido durante a leitura do valor. 

O parâmetro ESCAPE_CHAR será aplicado independentemente de FIELDQUOTE estar ou não habilitado. Ele não será usado para fazer escape do caractere de aspas. O caractere de aspas deve ter escape com outro caractere de aspas. O caractere de aspas poderá aparecer no valor da coluna somente se o valor for encapsulado com caracteres de aspas.

FIRSTROW = 'first_row' 

Especifica o número da primeira linha a carregar. O padrão é 1 e indica a primeira fila no arquivo de dados especificado. Os números de linhas são determinados pela contagem dos terminadores de linha. FIRSTROW tem base 1.

FIELDQUOTE = 'field_quote' 

Especifica um caractere que será usado como o caractere de aspas no arquivo CSV. Se não for especificado, o caractere de aspas (") será usado. 

DATA_COMPRESSION = 'data_compression_method'

Especifica o método de compactação. Com suporte somente em PARSER_VERSION='1.0'. Há suporte para o seguinte método de compactação:

- GZIP

PARSER_VERSION = 'parser_version'

Especifica a versão do analisador a ser usada ao ler arquivos. As versões do analisador CSV com suporte no momento são 1.0 e 2.0:

- PARSER_VERSION = '1.0'
- PARSER_VERSION = '2.0'

O analisador CSV versão 1.0 é o padrão e conta com muitos recursos. A versão 2.0 foi criada para ter um desempenho aprimorado e não dá suporte a todas as opções e codificações. 

Especificações do analisador CSV versão 1.0:

- As opções a seguir não têm suporte: HEADER_ROW.

Especificações do analisador CSV versão 2.0:

- não há suporte para todos os tipos de dados.
- O tamanho máximo da coluna de caracteres é 8.000.
- O limite de tamanho máximo de linha é de 8 MB.
- As opções a seguir não têm suporte: DATA_COMPRESSION.
- A cadeia de caracteres vazia entre aspas ("") é interpretada como cadeia de caracteres vazia.
- Formato com suporte para o tipo de dados DATE: AAAA-MM-DD
- Formato com suporte para o tipo de dados TIME: HH:MM:SS[.segundos fracionários]
- Formato com suporte para o tipo de dados DATETIME2: AAAA-MM-DD HH:MM:SS[.segundos fracionários]

HEADER_ROW = { TRUE | FALSE }

Especifica se o arquivo CSV contém a linha de cabeçalho. O padrão é FALSE. Com suporte em PARSER_VERSION='2.0'. Se for TRUE, os nomes de coluna serão lidos da primeira linha, de acordo com o argumento FIRSTROW. Se TRUE e esquema forem especificados usando WITH, a associação de nomes de colunas será feita por nome da coluna, não posições ordinais.

DATAFILETYPE = { 'char' | 'widechar' }

Especifica a codificação: char é usado para arquivos UTF8 e widechar é usado para arquivos UTF16.

CODEPAGE = { 'ACP' | 'OEM' | 'RAW' | 'code_page' }

Especifica a página de código dos dados no arquivo de dados. O valor padrão é 65001 (codificação UTF-8). Confira mais detalhes sobre essa opção [aqui](/sql/t-sql/functions/openrowset-transact-sql?view=sql-server-ver15&preserve-view=true#codepage).

## <a name="fast-delimited-text-parsing"></a>Análise rápida de texto delimitado

Há duas versões do analisador de texto delimitado que você pode usar. O analisador CSV versão 1.0 é padrão e repleto de recursos, enquanto o analisador versão 2.0 foi criado para desempenho. A melhoria de desempenho do analisador 2.0 vem de técnicas de análise avançadas e do multithreading. A diferença de velocidade será maior conforme aumentar o tamanho do arquivo.

## <a name="automatic-schema-discovery"></a>Descoberta automática de esquema

Você pode consultar com facilidade arquivos CSV e Parquet sem conhecer ou especificar o esquema omitindo a cláusula WITH. Os nomes de coluna e os tipos de dados serão inferidos dos arquivos.

Os arquivos Parquet contêm metadados de coluna que serão lidos, mapeamentos de tipo podem ser encontrados nos [mapeamentos de tipo do Parquet](#type-mapping-for-parquet). Confira [como ler os arquivos Parquet sem especificar o esquema](#read-parquet-files-without-specifying-schema) para obter exemplos.

Para arquivos CSV, os nomes das colunas podem ser lidos na linha de cabeçalho. Você pode especificar se a linha de cabeçalho existe usando o argumento HEADER_ROW. Se HEADER_ROW=FALSE, serão usados nomes de coluna genéricos: C1, C2, ... Cn, em que n corresponde ao número total de colunas contidas no arquivo. Os tipos de dados serão inferidos das primeiras 100 linhas de dados. Confira [como ler os arquivos CSV sem especificar o esquema](#read-csv-files-without-specifying-schema) para obter exemplos.

> [!IMPORTANT]
> Há casos em que o tipo de dados apropriado não pode ser inferido devido à falta de informações e um tipo de dados mais abrangente é usado. Isso causa uma sobrecarga de desempenho e é particularmente significativo para colunas de caracteres que serão inferidas como varchar (8000). Para obter um desempenho ideal, [confira os tipos de dados inferidos](best-practices-sql-on-demand.md#check-inferred-data-types) e [use os tipos de dados apropriados](best-practices-sql-on-demand.md#use-appropriate-data-types).

### <a name="type-mapping-for-parquet"></a>Mapeamento de tipos para Parquet

Os arquivos Parquet contêm descrições de tipo para cada coluna. A tabela a seguir descreve como os tipos Parquet são mapeados para tipos nativos do SQL.

| Tipo do Parquet | Tipo lógico do Parquet (anotação) | Tipo de dados SQL |
| --- | --- | --- |
| BOOLEAN | | bit |
| BINARY/BYTE_ARRAY | | varbinary |
| DOUBLE | | FLOAT |
| FLOAT | | real |
| INT32 | | INT |
| INT64 | | BIGINT |
| INT96 | |datetime2 |
| FIXED_LEN_BYTE_ARRAY | |binary |
| BINARY |UTF8 |varchar \*(ordenação UTF8) |
| BINARY |STRING |varchar \*(ordenação UTF8) |
| BINARY |ENUM|varchar \*(ordenação UTF8) |
| FIXED_LEN_BYTE_ARRAY |UUID |UNIQUEIDENTIFIER |
| BINARY |DECIMAL |decimal |
| BINARY |JSON |varchar(8000) \*(ordenação UTF8) |
| BINARY |BSON | Sem suporte |
| FIXED_LEN_BYTE_ARRAY |DECIMAL |decimal |
| BYTE_ARRAY |INTERVAL | Sem suporte |
| INT32 |INT(8, true) |SMALLINT |
| INT32 |INT(16, true) |SMALLINT |
| INT32 |INT(32, true) |INT |
| INT32 |INT(8, false) |TINYINT |
| INT32 |INT(16, false) |INT |
| INT32 |INT(32, false) |BIGINT |
| INT32 |DATE |date |
| INT32 |DECIMAL |decimal |
| INT32 |TIME (MILLIS)|time |
| INT64 |INT(64, true) |BIGINT |
| INT64 |INT(64, false) |decimal(20,0) |
| INT64 |DECIMAL |decimal |
| INT64 |TIME (MICROS) |time – TIME(NANOS) não compatível |
|INT64 |TIMESTAMP (MILLIS/MICROS/NANOS) |datetime2 – TIMESTAMP(NANOS) não compatível |
|[Tipo complexo](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#lists) |LISTA |varchar(8000), serializado em JSON |
|[Tipo complexo](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#maps)|MAP|varchar(8000), serializado em JSON |

## <a name="examples"></a>Exemplos

### <a name="read-csv-files-without-specifying-schema"></a>Ler arquivos CSV sem especificar o esquema

O seguinte exemplo lê o arquivo CSV que contém a linha de cabeçalho sem especificar os nomes das colunas ou os tipos de dados: 

```sql
SELECT 
    *
FROM OPENROWSET(
    BULK 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.csv',
    FORMAT = 'CSV',
    PARSER_VERSION = '2.0',
    HEADER_ROW = TRUE) as [r]
```

O seguinte exemplo lê o arquivo CSV que não contém a linha de cabeçalho sem especificar os nomes das colunas ou os tipos de dados: 

```sql
SELECT 
    *
FROM OPENROWSET(
    BULK 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.csv',
    FORMAT = 'CSV',
    PARSER_VERSION = '2.0') as [r]
```

### <a name="read-parquet-files-without-specifying-schema"></a>Ler arquivos Parquet sem especificar o esquema

O seguinte exemplo retorna todas as colunas da primeira linha do conjunto de dados de censo no formato Parquet, sem especificar nomes de colunas nem tipos de dados: 

```sql
SELECT 
    TOP 1 *
FROM  
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet',
        FORMAT='PARQUET'
    ) AS [r]
```

### <a name="read-specific-columns-from-csv-file"></a>Ler colunas específicas do arquivo CSV

O exemplo a seguir retorna apenas duas colunas com números ordinais 1 e 4 dos arquivos population*.csv. Como não há nenhuma linha de cabeçalho nos arquivos, ele inicia a leitura na primeira linha:

```sql
SELECT 
    * 
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population/population*.csv',
        FORMAT = 'CSV',
        FIRSTROW = 1
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2 1,
    [population] bigint 4
) AS [r]
```

### <a name="read-specific-columns-from-parquet-file"></a>Ler colunas específicas do arquivo Parquet

O exemplo seguinte retorna apenas duas colunas da primeira linha do conjunto de dados do censo em formato Parquet: 

```sql
SELECT 
    TOP 1 *
FROM  
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet',
        FORMAT='PARQUET'
    )
WITH (
    [stateName] VARCHAR (50),
    [population] bigint
) AS [r]
```

### <a name="specify-columns-using-json-paths"></a>Especificar colunas usando caminhos JSON

O seguinte exemplo mostra como você pode usar [expressões de caminho JSON](/sql/relational-databases/json/json-path-expressions-sql-server?view=azure-sqldw-latest&preserve-view=true) na cláusula WITH e demonstra a diferença entre os modos de caminho estrito e incerto: 

```sql
SELECT 
    TOP 1 *
FROM  
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet',
        FORMAT='PARQUET'
    )
WITH (
    --lax path mode samples
    [stateName] VARCHAR (50), -- this one works as column name casing is valid - it targets the same column as the next one
    [stateName_explicit_path] VARCHAR (50) '$.stateName', -- this one works as column name casing is valid
    [COUNTYNAME] VARCHAR (50), -- STATEname column will contain NULLs only because of wrong casing - it targets the same column as the next one
    [countyName_explicit_path] VARCHAR (50) '$.COUNTYNAME', -- STATEname column will contain NULLS only because of wrong casing and default path mode being lax

    --strict path mode samples
    [population] bigint 'strict $.population' -- this one works as column name casing is valid
    --,[population2] bigint 'strict $.POPULATION' -- this one fails because of wrong casing and strict path mode
)
AS [r]
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais amostras, confira o [Início Rápido: Consultar o armazenamento de dados](query-data-storage.md) para saber como usar `OPENROWSET` para ler formatos de arquivo [CSV](query-single-csv-file.md), [PARQUET](query-parquet-files.md) e [JSON](query-json-files.md). Confira as [melhores práticas](best-practices-sql-on-demand.md) para alcançar o desempenho ideal. Você também pode aprender a salvar os resultados de sua consulta no Armazenamento do Azure usando [CETAS](develop-tables-cetas.md).