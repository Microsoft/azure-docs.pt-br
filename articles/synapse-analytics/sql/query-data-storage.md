---
title: Consultar o armazenamento de dados com o pool de SQL sem servidor
description: Este artigo descreve como consultar o armazenamento do Azure usando o recurso de pool de SQL sem servidor no Azure Synapse Analytics.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: d299afca0bd8070a1da738e02812b64c41a7101c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101675045"
---
# <a name="query-storage-files-with-serverless-sql-pool-in-azure-synapse-analytics"></a>Consultar os arquivos de armazenamento com o pool de SQL sem servidor no Azure Synapse Analytics

O pool de SQL sem servidor permite que você consulte dados no data lake. Ele oferece uma área de superfície de consulta T-SQL que acomoda consultas de dados semiestruturados e não estruturados. Para consulta, os seguintes aspectos do T-SQL são compatíveis:

- Área de superfície de [SELECT](/sql/t-sql/queries/select-transact-sql?view=azure-sqldw-latest&preserve-view=true) completa, incluindo a maioria das [funções e operadores SQL](overview-features.md).
- [CETAS](develop-tables-cetas.md) (CREATE EXTERNAL TABLE AS SELECT) cria uma [tabela externa](develop-tables-external-tables.md) e, em seguida, exporta em paralelo os resultados de uma instrução SELECT de Transact-SQL para o Armazenamento do Azure.

Para obter mais informações sobre o que é e o que não é compatível atualmente, leia o artigo [Visão geral do pool de SQL sem servidor](on-demand-workspace-overview.md) ou os seguintes artigos:
- [Desenvolver o acesso de armazenamento](develop-storage-files-overview.md), em que você pode aprender a usar [Tabela externa](develop-tables-external-tables.md) e a função [OPENROWSET](develop-openrowset.md) para ler dados do armazenamento.
- [Controle o acesso de armazenamento](develop-storage-files-storage-access-control.md), em que você pode aprender a habilitar o SQL do Synapse a acessar o armazenamento usando a autenticação SAS ou a identidade gerenciada do workspace.

## <a name="overview"></a>Visão geral

Para dar suporte a uma experiência tranquila para a consulta no local de dados localizados em arquivos do Armazenamento do Azure, o pool de SQL sem servidor usa a função [OPENROWSET](develop-openrowset.md) com funcionalidades adicionais:

- [Consultar vários arquivos ou pastas](#query-multiple-files-or-folders)
- [Formato de arquivo PARQUET](#query-parquet-files)
- [CSV de consulta e texto delimitado (terminador de campo, terminador de linha, caractere de escape)](#query-csv-files)
- [Ler um subconjunto escolhido de colunas](#read-a-chosen-subset-of-columns)
- [Inferência de esquema](#schema-inference)
- [Função filename](#filename-function)
- [Função filepath](#filepath-function)
- [Trabalhar com tipos complexos e estruturas de dados aninhadas ou repetidas](#work-with-complex-types-and-nested-or-repeated-data-structures)

## <a name="query-parquet-files"></a>Consultar arquivos PARQUET

Para consultar dados de origem Parquet, use FORMAT = 'PARQUET'

```syntaxsql
SELECT * FROM
OPENROWSET( BULK N'https://myaccount.dfs.core.windows.net//mycontainer/mysubfolder/data.parquet', FORMAT = 'PARQUET') 
WITH (C1 int, C2 varchar(20), C3 varchar(max)) as rows
```

Examine o artigo [Consultar arquivos Parquet](query-parquet-files.md) para obter exemplos de uso.

## <a name="query-csv-files"></a>Consultar arquivos CSV

Para consultar dados de origem CSV, use FORMAT = 'CSV'. Você pode especificar o esquema do arquivo CSV como parte da função `OPENROWSET` ao consultar arquivos CSV:

```sql
SELECT * FROM
OPENROWSET( BULK N'https://myaccount.dfs.core.windows.net/mycontainer/mysubfolder/data.csv', FORMAT = 'CSV', PARSER_VERSION='2.0') 
WITH (C1 int, C2 varchar(20), C3 varchar(max)) as rows
```

Há algumas outras opções que podem ser usadas para ajustar as regras de análise para o formato CSV personalizado:
- ESCAPE_CHAR = 'char' especifica o caractere que é usado no arquivo para escapar a si mesmo e todos os valores de delimitador no arquivo. Se o caractere de escape for seguido por um valor diferente dele mesmo ou por um dos valores delimitadores, o caractere de escape será removido durante a leitura do valor.
O parâmetro ESCAPE_CHAR será aplicado quer FIELDQUOTE esteja habilitado ou não. Ele não será usado para fazer escape do caractere de aspas. O caractere de aspas deve ter escape com outro caractere de aspas. O caractere de aspas poderá aparecer no valor da coluna somente se o valor for encapsulado com caracteres de aspas.
- FIELDTERMINATOR ='field_terminator' especifica o terminador de campo a ser usado. O terminador de campo padrão é uma vírgula (" **,** ")
- ROWTERMINATOR ='row_terminator' especifica o terminador de linha a ser usado. O terminador de linha padrão um caractere de nova linha: **\r\n**.

## <a name="file-schema"></a>Esquema de arquivo

A linguagem SQL no SQL do Synapse permite que você defina o esquema do arquivo como parte da função `OPENROWSET` e leia todas as colunas ou um subconjunto delas ou tente determinar automaticamente os tipos de coluna do arquivo usando a inferência de esquema.

### <a name="read-a-chosen-subset-of-columns"></a>Ler um subconjunto escolhido de colunas

Para especificar as colunas que você deseja ler, você pode fornecer uma cláusula WITH opcional dentro da instrução `OPENROWSET`.

- Se houver arquivos de dados CSV para ler todas as colunas, forneça nomes de coluna e seus tipos de dados. Se você quiser um subconjunto de colunas, use números ordinais para escolher as colunas dos arquivos de dados de origem por ordinal. As colunas serão vinculadas à designação ordinal.
- Se houver arquivos de dados Parquet, forneça nomes de coluna que correspondam aos nomes de coluna nos arquivos de dados de origem. As colunas serão vinculadas ao nome.

```sql
SELECT * FROM
OPENROWSET( BULK N'https://myaccount.dfs.core.windows.net/mycontainer/mysubfolder/data.parquet', FORMAT = 'PARQUET') 
WITH (
      C1 int, 
      C2 varchar(20),
      C3 varchar(max)
) as rows
```

Para cada coluna, você precisa especificar o nome da coluna e digitar a cláusula `WITH`.
Para obter exemplos, veja [Ler arquivos CSV sem especificar todas as colunas](query-single-csv-file.md#return-a-subset-of-columns).

## <a name="schema-inference"></a>Inferência de esquema

Ao omitir a cláusula WITH da instrução `OPENROWSET`, você pode instruir o serviço a detectar automaticamente (inferir) o esquema dos arquivos subjacentes.

> [!NOTE]
> Atualmente, isso funciona apenas para o formato de arquivo PARQUET.

```sql
SELECT * FROM
OPENROWSET( BULK N'https://myaccount.dfs.core.windows.net/mycontainer/mysubfolder/data.parquet', FORMAT = 'PARQUET') 
```

Verifique se os [tipos de dados inferidos apropriados](best-practices-sql-on-demand.md#check-inferred-data-types) são usados para um desempenho ideal. 

## <a name="query-multiple-files-or-folders"></a>Consultar vários arquivos ou pastas

Para executar uma consulta T-SQL em um conjunto de arquivos dentro de uma pasta ou conjunto de pastas e tratá-los como uma entidade ou um conjunto de linhas, forneça um caminho para uma pasta ou um padrão (usando curingas) em um conjunto de arquivos ou pastas.

As seguintes regras se aplicam:

- Os padrões podem aparecer em parte de um caminho de diretório ou em um nome de arquivo.
- Vários padrões podem aparecer na mesma etapa de diretório ou nome de arquivo.
- Se houver vários curingas, os arquivos em todos os caminhos correspondentes serão incluídos no conjunto de arquivos resultante.

```sql
SELECT * FROM
OPENROWSET( BULK N'https://myaccount.dfs.core.windows.net/myroot/*/mysubfolder/*.parquet', FORMAT = 'PARQUET' ) as rows
```

Veja [Consultar pastas e vários arquivos](query-folders-multiple-csv-files.md) para obter exemplos de uso.

## <a name="file-metadata-functions"></a>Funções de metadados de arquivo

### <a name="filename-function"></a>Função filename

Essa função retorna o nome do arquivo de origem da linha. 

Para consultar arquivos específicos, leia a seção Filename no artigo [Consultar arquivos específicos](query-specific-files.md#filename).

O tipo de dados retornado é nvarchar (1024). Para obter um desempenho ideal, sempre converta o resultado da função filename para o tipo de dados apropriado. Se você usar o tipo de dados de caractere, use o comprimento apropriado.

### <a name="filepath-function"></a>Função filepath

Essa função retorna um caminho completo ou uma parte do caminho:

- Quando chamada sem parâmetro, retorna o caminho de arquivo completo da origem de uma linha.
- Quando chamada com parâmetro, ela retorna parte do caminho que corresponda ao curinga na posição especificada no parâmetro. Por exemplo, o valor de parâmetro 1 retornaria a parte do caminho que corresponde ao primeiro caractere curinga.

Para obter informações adicionais, leia a seção Filepath do artigo [Consultar arquivos específicos](query-specific-files.md#filepath).

O tipo de dados retornado é nvarchar (1024). Para obter um desempenho ideal, sempre converta o resultado da função filepath para o tipo de dados apropriado. Se você usar o tipo de dados de caractere, use o comprimento apropriado.

## <a name="work-with-complex-types-and-nested-or-repeated-data-structures"></a>Trabalhar com tipos complexos e estruturas de dados aninhadas ou repetidas

Para permitir uma experiência tranquila com os dados armazenados em tipos de dados aninhados ou repetidos, como em arquivos [Parquet](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#nested-types), o pool de SQL sem servidor adicionou as extensões a seguir.

#### <a name="project-nested-or-repeated-data"></a>Dados de projeto aninhados ou repetidos

Para projetar dados, execute uma instrução SELECT no arquivo Parquet que contém colunas de tipos de dados aninhados. Na saída, os valores aninhados serão serializados em JSON e retornados como um tipo de dados SQL varchar (8000).

```sql
    SELECT * FROM
    OPENROWSET
    (   BULK 'unstructured_data_path' ,
        FORMAT = 'PARQUET' )
    [AS alias]
```

Para obter informações mais detalhadas, veja a seção Dados de projeto aninhados ou repetidos do artigo [Consulta de tipos aninhados de Parquet](query-parquet-nested-types.md#project-nested-or-repeated-data).

#### <a name="access-elements-from-nested-columns"></a>Elementos de acesso de colunas aninhadas

Para acessar elementos aninhados de uma coluna aninhada, como Struct, use "notação de ponto" para concatenar nomes de campo no caminho. Forneça o caminho como column_name na cláusula WITH da função `OPENROWSET`.

O exemplo de fragmento da sintaxe fica como segue:

```syntaxsql
    OPENROWSET
    (   BULK 'unstructured_data_path' ,
        FORMAT = 'PARQUET' )
    WITH ({'column_name' 'column_type',})
    [AS alias]
    'column_name' ::= '[field_name.] field_name'
```

Por padrão, a função `OPENROWSET` corresponde ao nome e ao caminho do campo de origem com os nomes de coluna fornecidos na cláusula WITH. Os elementos contidos em diferentes níveis de aninhamento dentro do mesmo arquivo Parquet de origem podem ser acessados por meio da cláusula WITH.

**Valores retornados**

- A função retorna um valor escalar como int, decimal e varchar, do elemento especificado e no caminho especificado, para todos os tipos de Parquet que não estão no grupo Tipo Aninhado.
- Se o caminho aponta para um elemento que é de um Tipo Aninhado, a função retorna um fragmento JSON começando do elemento superior no caminho especificado. O fragmento JSON é do tipo varchar (8000).
- Se a propriedade não puder ser encontrada na column_name especificada, a função retornará um erro.
- Se a propriedade não puder ser encontrada no column_path especificado, dependendo do [Modo de caminho](/sql/relational-databases/json/json-path-expressions-sql-server?view=azure-sqldw-latest&preserve-view=true#PATHMODE), a função retornará um erro quando estiver no modo estrito ou nulo quando estiver no modo lax.

Para obter exemplos de consulta, examine a seção Elementos de acesso de colunas aninhadas no artigo [Consultar tipos aninhados de Parquet](query-parquet-nested-types.md#read-properties-from-nested-object-columns).

#### <a name="access-elements-from-repeated-columns"></a>Acessar elementos de colunas repetidas

Para acessar elementos de uma coluna repetida, como um elemento de uma Matriz ou um Mapa, use a função [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?view=azure-sqldw-latest&preserve-view=true) para cada elemento escalar que você precisa projetar e fornecer:

- Coluna aninhada ou repetida, como o primeiro parâmetro
- Um [caminho JSON](/sql/relational-databases/json/json-path-expressions-sql-server?view=azure-sqldw-latest&preserve-view=true) que especifica o elemento ou a propriedade a ser acessada, como um segundo parâmetro

Para acessar elementos não escalares de uma coluna repetida, use a função [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?view=azure-sqldw-latest&preserve-view=true) para cada elemento não escalar que você precisa projetar e fornecer:

- Coluna aninhada ou repetida, como o primeiro parâmetro
- Um [caminho JSON](/sql/relational-databases/json/json-path-expressions-sql-server?view=azure-sqldw-latest&preserve-view=true) que especifica o elemento ou a propriedade a ser acessada, como um segundo parâmetro

Veja o fragmento de sintaxe abaixo:

```syntaxsql
    SELECT
       { JSON_VALUE (column_name, path_to_sub_element), }
       { JSON_QUERY (column_name [ , path_to_sub_element ]), )
    FROM
    OPENROWSET
    (   BULK 'unstructured_data_path' ,
        FORMAT = 'PARQUET' )
    [AS alias]
```

Você pode encontrar exemplos de consulta para acessar elementos de colunas repetidas no artigo [Consultar tipos aninhados Parquet](query-parquet-nested-types.md#access-elements-from-repeated-columns).

## <a name="query-samples"></a>Exemplos de consulta

Você pode aprender mais sobre como consultar vários tipos de dados usando as consultas de exemplo.

### <a name="tools"></a>Ferramentas

As ferramentas de que você precisa para emitir consultas:
    - Azure Synapse Studio 
    - Azure Data Studio
    - SQL Server Management Studio

### <a name="demo-setup"></a>Configuração de demonstração

A primeira etapa é **criar um banco de dados** no qual você executará as consultas. Em seguida, você vai inicializar os objetos executando o [script de instalação](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) nesse banco de dados. 

Esse script de instalação criará as fontes de dados, as credenciais no escopo do banco de dados e os formatos de arquivo externos que são usados para ler os dados nestas amostras.

> [!NOTE]
> Os bancos de dados são usados apenas para exibição de metadados, não para dados propriamente ditos.  Anote o nome do banco de dados que você usa, você precisará dele mais tarde.

```sql
CREATE DATABASE mydbname;
```

### <a name="provided-demo-data"></a>Dados de demonstração fornecidos

Os dados de demonstração contêm os seguintes conjuntos de dados:

- Táxi de NYC – registros de corrida de táxi amarelo – parte do conjunto de dados públicos de NYC no formato CSV e Parquet
- Conjunto de dados populacionais no formato CSV
- Arquivos Parquet de exemplo com colunas aninhadas
- Livros no formato JSON

| Caminho da pasta                                                  | Descrição                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| /csv/                                                        | Pasta pai para dados no formato CSV                         |
| /csv/population/<br />/csv/population-unix/<br />/csv/population-unix-hdr/<br />/csv/population-unix-hdr-escape<br />/csv/population-unix-hdr-quoted | Pastas com arquivos de dados de população em formatos CSV diferentes. |
| /csv/taxi/                                                   | Pasta com arquivos de dados públicos de Nova York no formato CSV              |
| /parquet/                                                    | Pasta pai para dados no formato Parquet                     |
| /parquet/taxi                                                | Arquivos de dados públicos de Nova York no formato Parquet, particionados por ano e mês, usando o esquema de particionamento do Hive/Hadoop. |
| /parquet/nested/                                             | Arquivos Parquet de exemplo com colunas aninhadas                     |
| /json/                                                       | Pasta pai para dados no formato JSON                        |
| /json/books/                                                 | Arquivos JSON com os dados de livros                                   |


## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre como consultar diferentes tipos de arquivo e para criar e usar exibições, confira os seguintes artigos:

- [Consultar arquivos CSV](query-single-csv-file.md)
- [Consultar arquivos Parquet](query-parquet-files.md)
- [Consultar arquivos JSON](query-json-files.md)
- [Consultar valores aninhados](query-parquet-nested-types.md)
- [Consultar pastas e vários arquivos CSV](query-folders-multiple-csv-files.md)
- [Usar metadados de arquivo em consultas](query-specific-files.md)
- [Criar e usar modos de exibição](create-use-views.md)
