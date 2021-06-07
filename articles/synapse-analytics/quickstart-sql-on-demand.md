---
title: 'Início Rápido: Usar o pool de SQL sem servidor'
description: Neste início rápido, você verá e aprenderá como é fácil consultar vários tipos de arquivos usando o pool de SQL sem servidor.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 8607355069bbae5983239ddbd3e8752143f31497
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101676343"
---
# <a name="quickstart-use-serverless-sql-pool"></a>Início Rápido: Usar o pool de SQL sem servidor

O pool de SQL sem servidor do Azure Synapse é um serviço de consulta sem servidor que permite executar as consultas SQL em arquivos colocados no Armazenamento do Azure. Neste início rápido, você aprenderá a consultar vários tipos de arquivos usando o pool de SQL sem servidor. Os formatos compatíveis são listados em [OPENROWSET](sql/develop-openrowset.md).

Este início rápido mostra a consulta de: arquivos CSV, Apache Parquet e JSON.

## <a name="prerequisites"></a>Pré-requisitos

Escolha um cliente SQL para emitir consultas:

- O [Azure Synapse Studio](./get-started-create-workspace.md) é uma ferramenta da Web que pode ser usada para procurar arquivos no armazenamento e criar consultas SQL.
- O [Azure Data Studio](sql/get-started-azure-data-studio.md) é uma ferramenta de cliente que permite executar consultas SQL e notebooks no banco de dados sob demanda.
- O [SQL Server Management Studio](sql/get-started-ssms.md) é uma ferramenta de cliente que permite executar consultas SQL no banco de dados sob demanda.

Parâmetros para o início rápido:

| Parâmetro                                 | Descrição                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| Endereço do ponto de extremidade de serviço do pool de SQL sem servidor    | Usado como o nome do servidor                                   |
| Região do ponto de extremidade de serviço do pool de SQL sem servidor     | Usada para determinar qual armazenamento será usado nas amostras |
| Nome de usuário e senha para acesso de ponto de extremidade | Usado para acessar o ponto de extremidade                               |
| O banco de dados usado para criar exibições         | Banco de dados usado como ponto de partida nas amostras       |

## <a name="first-time-setup"></a>Configuração inicial

Antes de usar os exemplos:

- Criar um banco de dados para as exibições (caso você queira usar exibições)
- Criar credenciais a serem usadas pelo pool de SQL sem servidor para acessar arquivos no armazenamento

### <a name="create-database"></a>Criar banco de dados

Crie um banco de dados próprio para fins de demonstração. Você usará esse banco de dados para criar suas exibições e para as consultas de exemplo no artigo.

> [!NOTE]
> Os bancos de dados são usados apenas para os metadados de exibição, não para os dados reais.
>Anote o nome do banco de dados usado para uso posterior no Início Rápido.

Use a seguinte consulta, alterando `mydbname` para um nome de sua escolha:

```sql
CREATE DATABASE mydbname
```

### <a name="create-data-source"></a>Criar a fonte de dados

Para executar consultas usando o pool de SQL sem servidor, crie uma fonte de dados que o pool de SQL sem servidor possa usar para acessar os arquivos no armazenamento.
Execute o seguinte snippet de código para criar a fonte de dados usada nos exemplos nesta seção:

```sql
-- create master key that will protect the credentials:
CREATE MASTER KEY ENCRYPTION BY PASSWORD = <enter very strong password here>

-- create credentials for containers in our demo storage account
CREATE DATABASE SCOPED CREDENTIAL sqlondemand
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D'
GO
CREATE EXTERNAL DATA SOURCE SqlOnDemandDemo WITH (
    LOCATION = 'https://sqlondemandstorage.blob.core.windows.net',
    CREDENTIAL = sqlondemand
);
```

## <a name="query-csv-files"></a>Consultar arquivos CSV

A seguinte imagem é uma visualização do arquivo a ser consultado:

![Dez primeiras linhas do arquivo CSV sem cabeçalho e uma nova linha no estilo do Windows.](./sql/media/query-single-csv-file/population.png)

A seguinte consulta mostra como ler um arquivo CSV que não contém uma linha de cabeçalho, com uma nova linha no estilo do Windows e colunas delimitadas por vírgula:

```sql
SELECT TOP 10 *
FROM OPENROWSET
  (
      BULK 'csv/population/*.csv',
      DATA_SOURCE = 'SqlOnDemandDemo',
      FORMAT = 'CSV', PARSER_VERSION = '2.0'
  )
WITH
  (
      country_code VARCHAR (5)
    , country_name VARCHAR (100)
    , year smallint
    , population bigint
  ) AS r
WHERE
  country_name = 'Luxembourg' AND year = 2017
```

Você pode especificar o esquema no momento da compilação da consulta.
Para obter mais exemplos, confira como [consultar um arquivo CSV](sql/query-single-csv-file.md).

## <a name="query-parquet-files"></a>Consultar arquivos Parquet

A amostra a seguir apresenta as funcionalidades de inferência automática de esquemas para consulta de arquivos Parquet. Ela retorna o número de linhas em setembro de 2017 sem especificar o esquema.

> [!NOTE]
> Você não precisa especificar colunas na cláusula `OPENROWSET WITH` ao ler arquivos Parquet. Nesse caso, o pool de SQL sem servidor utiliza os metadados do arquivo Parquet e associa as colunas por nome.

```sql
SELECT COUNT_BIG(*)
FROM OPENROWSET
  (
      BULK 'parquet/taxi/year=2017/month=9/*.parquet',
      DATA_SOURCE = 'SqlOnDemandDemo',
      FORMAT='PARQUET'
  ) AS nyc
```

Encontre mais informações sobre [como consultar arquivos parquet](sql/query-parquet-files.md).

## <a name="query-json-files"></a>Consultar arquivos JSON

### <a name="json-sample-file"></a>Arquivo de exemplo JSON

Os arquivos são armazenados no contêiner *json* e na pasta *books* e contêm uma só entrada de livro com a seguinte estrutura:

```json
{  
   "_id":"ahokw88",
   "type":"Book",
   "title":"The AWK Programming Language",
   "year":"1988",
   "publisher":"Addison-Wesley",
   "authors":[  
      "Alfred V. Aho",
      "Brian W. Kernighan",
      "Peter J. Weinberger"
   ],
   "source":"DBLP"
}
```

### <a name="query-json-files"></a>Consultar arquivos JSON

A seguinte consulta mostra como usar [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?view=azure-sqldw-latest&preserve-view=true) para recuperar valores escalares (título, editor) de um livro com o título *Probabilística e métodos estatísticos em criptologia, uma introdução com artigos selecionados*:

```sql
SELECT
    JSON_VALUE(jsonContent, '$.title') AS title
  , JSON_VALUE(jsonContent, '$.publisher') as publisher
  , jsonContent
FROM OPENROWSET
  (
      BULK 'json/books/*.json',
      DATA_SOURCE = 'SqlOnDemandDemo'
    , FORMAT='CSV'
    , FIELDTERMINATOR ='0x0b'
    , FIELDQUOTE = '0x0b'
    , ROWTERMINATOR = '0x0b'
  )
WITH
  ( jsonContent varchar(8000) ) AS [r]
WHERE
  JSON_VALUE(jsonContent, '$.title') = 'Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected Topics'
```

> [!IMPORTANT]
> Estamos lendo o arquivo JSON inteiro como uma só linha/coluna. Sendo assim, FIELDTERMINATOR, FIELDQUOTE e ROWTERMINATOR estão definidos como 0x0b porque não esperamos encontrá-los no arquivo.

## <a name="next-steps"></a>Próximas etapas

Agora, você está pronto para continuar com os seguintes artigos:

- [Consultar um arquivo CSV individual](sql/query-single-csv-file.md)
- [Consultar pastas e vários arquivos CSV](sql/query-folders-multiple-csv-files.md)
- [Consultar arquivos específicos](sql/query-specific-files.md)
- [Consultar arquivos Parquet](sql/query-parquet-files.md)
- [Consultar tipos aninhados Parquet](sql/query-parquet-nested-types.md)
- [Consultar arquivos JSON](sql/query-json-files.md)
- [Como criar e usar exibições](sql/create-use-views.md)
- [Como criar e usar tabelas externas](sql/create-use-external-tables.md)
- [Persistir o resultado da consulta no Armazenamento do Azure](sql/create-external-table-as-select.md)
- [Consultar um arquivo CSV individual](sql/query-single-csv-file.md)