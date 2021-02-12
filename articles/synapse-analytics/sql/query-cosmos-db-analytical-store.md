---
title: Consultar dados de Azure Cosmos DB usando um pool SQL sem servidor na visualização do link Synapse do Azure
description: Neste artigo, você aprenderá a consultar Azure Cosmos DB usando um pool SQL sem servidor na visualização do link Synapse do Azure.
services: synapse analytics
author: jovanpop-msft
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 12/04/2020
ms.author: jovanpop
ms.reviewer: jrasnick
ms.openlocfilehash: 2059608faa8ce148e5823e48eff6abf9e71c9b01
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98735426"
---
# <a name="query-azure-cosmos-db-data-with-a-serverless-sql-pool-in-azure-synapse-link-preview"></a>Consultar dados de Azure Cosmos DB com um pool SQL sem servidor na visualização do link Synapse do Azure

> [!IMPORTANT]
> O suporte do pool SQL sem servidor para o link Synapse do Azure para Azure Cosmos DB está atualmente em versão prévia. Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Para obter mais informações, consulte os [Termos de uso complementares de versões prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Um pool SQL sem servidor permite analisar dados em seus contêineres de Azure Cosmos DB que são habilitados com o [link Synapse do Azure](../../cosmos-db/synapse-link.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) quase em tempo real sem afetar o desempenho de suas cargas de trabalho transacionais. Ele oferece uma sintaxe T-SQL familiar para consultar dados do [armazenamento analítico](../../cosmos-db/analytical-store-introduction.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) e conectividade integrada a uma grande variedade de ferramentas de consulta de Business Intelligence (BI) e ad hoc por meio da interface T-SQL.

Para consultar Azure Cosmos DB, a área de superfície de [seleção](/sql/t-sql/queries/select-transact-sql?view=azure-sqldw-latest&preserve-view=true) completa é suportada por meio da função [OPENROWSET](develop-openrowset.md) , que inclui a maioria dos [operadores e funções SQL](overview-features.md). Você também pode armazenar os resultados da consulta que lê dados de Azure Cosmos DB juntamente com os dados no armazenamento de BLOBs do Azure ou Azure Data Lake Storage usando [criar tabela externa como SELECT](develop-tables-cetas.md#cetas-in-serverless-sql-pool) (CETAS). Atualmente, não é possível armazenar os resultados da consulta do pool SQL sem servidor para Azure Cosmos DB usando CETAS.

Neste artigo, você aprenderá a escrever uma consulta com um pool SQL sem servidor que consultará dados de contêineres Azure Cosmos DB que são habilitados com o link Synapse do Azure. Em seguida, você pode saber mais sobre como criar exibições de pool de SQL sem servidor em Azure Cosmos DB contêineres e conectá-las a modelos de Power BI neste [tutorial](./tutorial-data-analyst.md).

> [!IMPORTANT]
> Este tutorial usa um contêiner com um [Azure Cosmos DB esquema bem definido](../../cosmos-db/analytical-store-introduction.md#schema-representation). A experiência de consulta que o pool SQL sem servidor fornece para um [Azure Cosmos DB esquema de fidelidade total](#full-fidelity-schema) é um comportamento temporário que será alterado com base nos comentários de visualização. Não confie no esquema do conjunto de resultados da `OPENROWSET` função sem a `WITH` cláusula que lê dados de um contêiner com um esquema de fidelidade total, pois a experiência de consulta pode ser alinhada e alterada com base no esquema bem definido. Você pode postar seus comentários no [Fórum de comentários do Azure Synapse Analytics](https://feedback.azure.com/forums/307516-azure-synapse-analytics). Você também pode entrar em contato com a [equipe do produto Azure Synapse link](mailto:cosmosdbsynapselink@microsoft.com) para fornecer comentários.

## <a name="overview"></a>Visão geral

O pool SQL sem servidor permite consultar Azure Cosmos DB armazenamento analítico usando a `OPENROWSET` função. 
- `OPENROWSET` com chave embutida. Essa sintaxe pode ser usada para consultar coleções de Azure Cosmos DB sem a necessidade de preparar as credenciais.
- `OPENROWSET` Essa credencial referenciada que contém a chave de conta de Cosmos DB. Essa sintaxe pode ser usada para criar exibições em coleções de Azure Cosmos DB.

### <a name="openrowset-with-key"></a>[OPENROWSET com chave](#tab/openrowset-key)

Para dar suporte à consulta e análise de dados em um repositório analítico Azure Cosmos DB, um pool SQL sem servidor usa a seguinte `OPENROWSET` sintaxe:

```sql
OPENROWSET( 
       'CosmosDB',
       '<Azure Cosmos DB connection string>',
       <Container name>
    )  [ < with clause > ] AS alias
```

A cadeia de conexão Azure Cosmos DB especifica o nome da conta de Azure Cosmos DB, o nome do banco de dados, a chave mestra da conta do banco de dados e um nome de região opcional para a `OPENROWSET` função.

A cadeia de conexão tem o seguinte formato:
```sql
'account=<database account name>;database=<database name>;region=<region name>;key=<database account master key>'
```

O nome do contêiner de Azure Cosmos DB é especificado sem aspas na `OPENROWSET` sintaxe. Se o nome do contêiner tiver caracteres especiais, por exemplo, um traço (-), o nome deverá ser disposto dentro de colchetes ( `[]` ) na `OPENROWSET` sintaxe.

### <a name="openrowset-with-credential"></a>[OPENROWSET com credencial](#tab/openrowset-credential)

Você pode usar `OPENROWSET` a sintaxe que faz referência à credencial:

```sql
OPENROWSET( 
       PROVIDER = 'CosmosDB',
       CONNECTION = '<Azure Cosmos DB connection string without account key>',
       OBJECT = '<Container name>',
       [ CREDENTIAL | SERVER_CREDENTIAL ] = '<credential name>'
    )  [ < with clause > ] AS alias
```

A cadeia de conexão Azure Cosmos DB não contém a chave nesse caso. A cadeia de conexão tem o seguinte formato:
```sql
'account=<database account name>;database=<database name>;region=<region name>'
```

A chave mestra da conta de banco de dados é colocada em credencial no escopo do banco de dados ou credencial do servidor. 

---

> [!IMPORTANT]
> Verifique se você está usando algum agrupamento de banco de dados UTF-8, por exemplo, `Latin1_General_100_CI_AS_SC_UTF8` , porque os valores de cadeia de caracteres em um repositório analítico de Azure Cosmos DB são codificados como texto UTF-8.
> Uma incompatibilidade entre a codificação de texto no arquivo e o agrupamento pode causar erros de conversão de texto inesperado.
> Você pode alterar facilmente o agrupamento padrão do banco de dados atual usando a instrução T-SQL `alter database current collate Latin1_General_100_CI_AI_SC_UTF8` .

> [!NOTE]
> Um pool SQL sem servidor não dá suporte à consulta de um repositório transacional Azure Cosmos DB.

## <a name="sample-dataset"></a>Amostra do conjunto de dados

Os exemplos neste artigo baseiam-se nos dados do [Centro Europeu para prevenção de doenças e controle (ECDC) COVID-19 casos](https://azure.microsoft.com/services/open-datasets/catalog/ecdc-covid-19-cases/) e [COVID-19 Open Research DataSet (cabo-19), doi: 10.5281/zenodo. 3715505](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/).

Você pode ver a licença e a estrutura de dados nessas páginas. Você também pode baixar dados de exemplo para os conjuntos de [ECDC](https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.json) e de [cabo-19](https://azureopendatastorage.blob.core.windows.net/covid19temp/comm_use_subset/pdf_json/000b7d1517ceebb34e1e3e817695b6de03e2fa78.json) .

Para acompanhar este artigo mostrando como consultar dados de Azure Cosmos DB com um pool de SQL sem servidor, certifique-se de criar os seguintes recursos:

* Uma conta de banco de dados Azure Cosmos DB que é [habilitada para o link Synapse do Azure](../../cosmos-db/configure-synapse-link.md).
* Um banco de dados Azure Cosmos DB chamado `covid` .
* Dois contêineres Azure Cosmos DB nomeados `Ecdc` e `Cord19` carregados com os conjuntos de valores de exemplo anteriores.

Você pode usar a seguinte cadeia de conexão para fins de teste: `Account=synapselink-cosmosdb-sqlsample;Database=covid;Key=s5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==` . Observe que essa conexão não garantirá o desempenho, pois essa conta pode estar localizada em uma região remota em comparação com o ponto de extremidade SQL do Synapse.

## <a name="explore-azure-cosmos-db-data-with-automatic-schema-inference"></a>Explorar Azure Cosmos DB dados com inferência de esquema automática

A maneira mais fácil de explorar dados no Azure Cosmos DB é usando o recurso de inferência automática de esquema. Ao omitir a `WITH` cláusula da `OPENROWSET` instrução, você pode instruir o pool SQL sem servidor a detectar automaticamente (inferir) o esquema do repositório analítico do contêiner de Azure Cosmos DB.

### <a name="openrowset-with-key"></a>[OPENROWSET com chave](#tab/openrowset-key)

```sql
SELECT TOP 10 *
FROM OPENROWSET( 
       'CosmosDB',
       'Account=synapselink-cosmosdb-sqlsample;Database=covid;Key=s5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==',
       Ecdc) as documents
```

### <a name="openrowset-with-credential"></a>[OPENROWSET com credencial](#tab/openrowset-credential)

```sql
/*  Setup - create server-level or database scoped credential with Azure Cosmos DB account key:
    CREATE CREDENTIAL MyCosmosDbAccountCredential
    WITH IDENTITY = 'SHARED ACCESS SIGNATURE', SECRET = 's5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==';
*/
SELECT TOP 10 *
FROM OPENROWSET(
      PROVIDER = 'CosmosDB',
      CONNECTION = 'Account=synapselink-cosmosdb-sqlsample;Database=covid',
      OBJECT = 'Ecdc',
      SERVER_CREDENTIAL = 'MyCosmosDbAccountCredential'
    ) with ( date_rep varchar(20), cases bigint, geo_id varchar(6) ) as rows
```

---

No exemplo anterior, orientamos o pool SQL sem servidor para se conectar ao banco de `covid` dados na conta de Azure Cosmos DB `MyCosmosDbAccount` autenticada usando a chave Azure Cosmos dB (a cópia no exemplo anterior). Em seguida, acessamos o `Ecdc` armazenamento analítico do contêiner na `West US 2` região. Como não há projeção de propriedades específicas, a `OPENROWSET` função retornará todas as propriedades dos itens de Azure Cosmos DB.

Supondo que os itens no contêiner de Azure Cosmos DB `date_rep` tenham `cases` Propriedades, e, `geo_id` os resultados dessa consulta são mostrados na tabela a seguir:

| date_rep | cases | geo_id |
| --- | --- | --- |
| 2020-08-13 | 254 | RS |
| 2020-08-12 | 235 | RS |
| 2020-08-11 | 163 | RS |

Se você precisar explorar dados do outro contêiner no mesmo banco de Azure Cosmos DB, poderá usar a mesma cadeia de conexão e fazer referência ao contêiner necessário como o terceiro parâmetro:

```sql
SELECT TOP 10 *
FROM OPENROWSET( 
       'CosmosDB',
       'Account=synapselink-cosmosdb-sqlsample;Database=covid;Key=s5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==',
       Cord19) as cord19
```

## <a name="explicitly-specify-schema"></a>Especificar explicitamente o esquema

Embora a funcionalidade de inferência de esquema automática no `OPENROWSET` forneça um querience simples e fácil de usar, seus cenários de negócios podem exigir que você especifique explicitamente o esquema para propriedades relevantes somente leitura dos dados de Azure Cosmos DB.

A `OPENROWSET` função permite especificar explicitamente quais propriedades você deseja ler dos dados no contêiner e especificar seus tipos de dados.

Vamos imaginar que importamos alguns dados do [ECDC COVID DataSet](https://azure.microsoft.com/services/open-datasets/catalog/ecdc-covid-19-cases/) com a seguinte estrutura para Azure Cosmos DB:

```json
{"date_rep":"2020-08-13","cases":254,"countries_and_territories":"Serbia","geo_id":"RS"}
{"date_rep":"2020-08-12","cases":235,"countries_and_territories":"Serbia","geo_id":"RS"}
{"date_rep":"2020-08-11","cases":163,"countries_and_territories":"Serbia","geo_id":"RS"}
```

Esses documentos JSON simples no Azure Cosmos DB podem ser representados como um conjunto de linhas e colunas no SQL Synapse. A `OPENROWSET` função permite que você especifique um subconjunto de propriedades que você deseja ler e os tipos de coluna exatos na `WITH` cláusula:

### <a name="openrowset-with-key"></a>[OPENROWSET com chave](#tab/openrowset-key)
```sql
SELECT TOP 10 *
FROM OPENROWSET(
      'CosmosDB',
      'Account=synapselink-cosmosdb-sqlsample;Database=covid;Key=s5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==',
       Ecdc
    ) with ( date_rep varchar(20), cases bigint, geo_id varchar(6) ) as rows
```
### <a name="openrowset-with-credential"></a>[OPENROWSET com credencial](#tab/openrowset-credential)
```sql
/*  Setup - create server-level or database scoped credential with Azure Cosmos DB account key:
    CREATE CREDENTIAL MyCosmosDbAccountCredential
    WITH IDENTITY = 'SHARED ACCESS SIGNATURE', SECRET = 's5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==';
*/
SELECT TOP 10 *
FROM OPENROWSET(
      PROVIDER = 'CosmosDB',
      CONNECTION = 'Account=synapselink-cosmosdb-sqlsample;Database=covid',
      OBJECT = 'Ecdc',
      SERVER_CREDENTIAL = 'MyCosmosDbAccountCredential'
    ) with ( date_rep varchar(20), cases bigint, geo_id varchar(6) ) as rows
```
---
O resultado dessa consulta pode ser semelhante à seguinte tabela:

| date_rep | cases | geo_id |
| --- | --- | --- |
| 2020-08-13 | 254 | RS |
| 2020-08-12 | 235 | RS |
| 2020-08-11 | 163 | RS |

Para obter mais informações sobre os tipos SQL que devem ser usados para Azure Cosmos DB valores, consulte as [regras para mapeamentos de tipo SQL](#azure-cosmos-db-to-sql-type-mappings) no final do artigo.

## <a name="create-view"></a>Criar modo de exibição

A criação de exibições nos bancos de dados mestre ou padrão não é recomendada nem tem suporte. Portanto, você precisa criar um banco de dados de usuário para suas exibições.

Depois de identificar o esquema, você pode preparar uma exibição sobre seus dados de Azure Cosmos DB. Você deve posicionar a chave de conta do Azure Cosmos DB em uma credencial separada e fazer referência a essa credencial da `OPENROWSET` função. Não mantenha sua chave de conta na definição de exibição.

```sql
CREATE CREDENTIAL MyCosmosDbAccountCredential
WITH IDENTITY = 'SHARED ACCESS SIGNATURE', SECRET = 's5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==';
GO
CREATE OR ALTER VIEW Ecdc
AS SELECT *
FROM OPENROWSET(
      PROVIDER = 'CosmosDB',
      CONNECTION = 'Account=synapselink-cosmosdb-sqlsample;Database=covid',
      OBJECT = 'Ecdc',
      SERVER_CREDENTIAL = 'MyCosmosDbAccountCredential'
    ) with ( date_rep varchar(20), cases bigint, geo_id varchar(6) ) as rows
```

Não use `OPENROWSET` sem esquema explicitamente definido, pois isso pode afetar o desempenho. Certifique-se de usar os menores tamanhos possíveis para suas colunas (por exemplo VARCHAR (100) em vez de VARCHAR padrão (8000)). Você deve usar um agrupamento UTF-8 como agrupamento de banco de dados padrão ou defini-lo como agrupamento de coluna explícito para evitar o [problema de conversão de UTF-8](../troubleshoot/reading-utf8-text.md). O agrupamento `Latin1_General_100_BIN2_UTF8` fornece o melhor desempenho quando o Yu filtra dados usando algumas colunas de cadeia de caracteres.

## <a name="query-nested-objects-and-arrays"></a>Consultar objetos aninhados e matrizes

Com Azure Cosmos DB, você pode representar modelos de dados mais complexos, compondo-os como objetos ou matrizes aninhados. O recurso AutoSync do link Synapse do Azure para Azure Cosmos DB gerencia a representação do esquema no repositório analítico pronto, o que inclui o tratamento de tipos de dados aninhados que permitem a consulta avançada do pool SQL sem servidor.

Por exemplo, o conjunto de dados do [cabo-19](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/) tem documentos JSON que seguem esta estrutura:

```json
{
    "paper_id": <str>,                   # 40-character sha1 of the PDF
    "metadata": {
        "title": <str>,
        "authors": <array of objects>    # list of author dicts, in order
        ...
     }
     ...
}
```

Os objetos aninhados e as matrizes no Azure Cosmos DB são representados como cadeias de caracteres JSON no resultado da consulta quando a `OPENROWSET` função os lê. Você pode especificar os caminhos para valores aninhados nos objetos ao usar a `WITH` cláusula:

```sql
SELECT TOP 10 *
FROM OPENROWSET( 
       'CosmosDB',
       'Account=synapselink-cosmosdb-sqlsample;Database=covid;Key=s5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==',
       Cord19)
WITH (  paper_id    varchar(8000),
        title        varchar(1000) '$.metadata.title',
        metadata     varchar(max),
        authors      varchar(max) '$.metadata.authors'
) AS docs;
```

O resultado dessa consulta pode ser semelhante à seguinte tabela:

| paper_id | título | metadata | authors |
| --- | --- | --- |
| bb11206963e831f... | Informações complementares de um epidemi de eco... | `{"title":"Supplementary Informati…` | `[{"first":"Julien","last":"Mélade","suffix":"","af…`| 
| bb1206963e831f1... | O uso de convalescent sera na imune-E... | `{"title":"The Use of Convalescent…` | `[{"first":"Antonio","last":"Lavazza","suffix":"", …` |
| bb378eca9aac649... | Tylosema esculentum (Marama) tuber e B... | `{"title":"Tylosema esculentum (Ma…` | `[{"first":"Walter","last":"Chingwaru","suffix":"",…` | 

Saiba mais sobre a análise [de tipos de dados complexos no link Synapse do Azure](../how-to-analyze-complex-schema.md) e [estruturas aninhadas em um pool SQL sem servidor](query-parquet-nested-types.md).

> [!IMPORTANT]
> Se você vir caracteres inesperados em seu texto como `MÃƒÂ©lade` em vez de `Mélade` , o agrupamento de banco de dados não será definido como agrupamento [UTF-8](/sql/relational-databases/collations/collation-and-unicode-support#utf8) .
> [Altere o agrupamento do banco de dados](/sql/relational-databases/collations/set-or-change-the-database-collation#to-change-the-database-collation) para o agrupamento UTF-8 usando uma instrução SQL como `ALTER DATABASE MyLdw COLLATE LATIN1_GENERAL_100_CI_AS_SC_UTF8` .

## <a name="flatten-nested-arrays"></a>Mesclar matrizes aninhadas

Azure Cosmos DB dados podem ter subconjuntos aninhados como a matriz do autor de um conjunto de dados de [cabo 19](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/) :

```json
{
    "paper_id": <str>,                      # 40-character sha1 of the PDF
    "metadata": {
        "title": <str>,
        "authors": [                        # list of author dicts, in order
            {
                "first": <str>,
                "middle": <list of str>,
                "last": <str>,
                "suffix": <str>,
                "affiliation": <dict>,
                "email": <str>
            },
            ...
        ],
        ...
}
```

Em alguns casos, talvez seja necessário "unir" as propriedades do item superior (metadados) com todos os elementos da matriz (autores). Um pool SQL sem servidor permite mesclar estruturas aninhadas aplicando a `OPENJSON` função na matriz aninhada:

```sql
SELECT
    *
FROM
    OPENROWSET(
      'CosmosDB',
      'Account=synapselink-cosmosdb-sqlsample;Database=covid;Key=s5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==',
       Cord19
    ) WITH ( title varchar(1000) '$.metadata.title',
             authors varchar(max) '$.metadata.authors' ) AS docs
      CROSS APPLY OPENJSON ( authors )
                  WITH (
                       first varchar(50),
                       last varchar(50),
                       affiliation nvarchar(max) as json
                  ) AS a
```

O resultado dessa consulta pode ser semelhante à seguinte tabela:

| título | authors | first | last | afilia |
| --- | --- | --- | --- | --- |
| Informações complementares de um epidemi de eco... |   `[{"first":"Julien","last":"Mélade","suffix":"","affiliation":{"laboratory":"Centre de Recher…` | Julien | Mélade | `   {"laboratory":"Centre de Recher…` |
Informações complementares de um epidemi de eco... | `[{"first":"Nicolas","last":"4#","suffix":"","affiliation":{"laboratory":"","institution":"U…` | Nicolas | quatro # |`{"laboratory":"","institution":"U…` | 
| Informações complementares de um epidemi de eco... |   `[{"first":"Beza","last":"Ramazindrazana","suffix":"","affiliation":{"laboratory":"Centre de Recher…` | Beza | Ramazindrazana | `{"laboratory":"Centre de Recher…` |
| Informações complementares de um epidemi de eco... |   `[{"first":"Olivier","last":"Flores","suffix":"","affiliation":{"laboratory":"UMR C53 CIRAD, …` | Olivier | Flores |`{"laboratory":"UMR C53 CIRAD, …` |     

> [!IMPORTANT]
> Se você vir caracteres inesperados em seu texto como `MÃƒÂ©lade` em vez de `Mélade` , o agrupamento de banco de dados não será definido como agrupamento [UTF-8](/sql/relational-databases/collations/collation-and-unicode-support#utf8) . [Altere o agrupamento do banco de dados](/sql/relational-databases/collations/set-or-change-the-database-collation#to-change-the-database-collation) para o agrupamento UTF-8 usando uma instrução SQL como `ALTER DATABASE MyLdw COLLATE LATIN1_GENERAL_100_CI_AS_SC_UTF8` .

## <a name="azure-cosmos-db-to-sql-type-mappings"></a>Azure Cosmos DB para mapeamentos de tipo SQL

Embora Azure Cosmos DB repositório transacional seja independente de esquema, o armazenamento analítico é esquematizados para otimizar o desempenho de consultas analíticas. Com o recurso AutoSync do link Synapse do Azure, o Azure Cosmos DB gerencia a representação do esquema no armazenamento analítico pronto, o que inclui o tratamento de tipos de dados aninhados. Como um pool SQL sem servidor consulta o repositório analítico, é importante entender como mapear Azure Cosmos DB tipos de dados de entrada para tipos de dados SQL.

Azure Cosmos DB contas da API do SQL (Core) dão suporte a tipos de propriedade JSON de número, Cadeia de caracteres, booliano, nulo, objeto aninhado ou matriz. Você precisaria escolher tipos SQL que correspondam a esses tipos de JSON se estiver usando a `WITH` cláusula em `OPENROWSET` . A tabela a seguir mostra os tipos de coluna SQL que devem ser usados para diferentes tipos de propriedade no Azure Cosmos DB.

| Tipo de propriedade Azure Cosmos DB | Tipo de coluna SQL |
| --- | --- |
| Booliano | bit |
| Integer | BIGINT |
| Decimal | FLOAT |
| String | varchar (agrupamento de banco de dados UTF-8) |
| Data e hora (cadeia de caracteres formatada em ISO) | varchar (30) |
| Data e hora (carimbo de hora do UNIX) | BIGINT |
| Nulo | `any SQL type` 
| Objeto ou matriz aninhada | varchar (max) (agrupamento de banco de dados UTF-8), serializado como texto JSON |

## <a name="full-fidelity-schema"></a>Esquema de fidelidade total

Azure Cosmos DB esquema de fidelidade total registra os valores e seus melhores tipos de correspondência para cada propriedade em um contêiner. A `OPENROWSET` função em um contêiner com esquema de fidelidade total fornece o tipo e o valor real em cada célula. Vamos supor que a consulta a seguir leia os itens de um contêiner com esquema de fidelidade total:

```sql
SELECT *
FROM OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Ecdc
    ) as rows
```

O resultado dessa consulta retornará tipos e valores formatados como texto JSON:

| date_rep | cases | geo_id |
| --- | --- | --- |
| {"data": "2020-08-13"} | {"Int32": "254"} | {"String": "RS"} |
| {"data": "2020-08-12"} | {"Int32": "235"}| {"String": "RS"} |
| {"data": "2020-08-11"} | {"Int32": "316"} | {"String": "RS"} |
| {"data": "2020-08-10"} | {"Int32": "281"} | {"String": "RS"} |
| {"data": "2020-08-09"} | {"Int32": "295"} | {"String": "RS"} |
| {"String": "2020/08/08"} | {"Int32": "312"} | {"String": "RS"} |
| {"data": "2020-08-07"} | {"float64": "339.0"} | {"String": "RS"} |

Para cada valor, você pode ver o tipo identificado em um Azure Cosmos DB item de contêiner. A maioria dos valores da `date_rep` propriedade contém `date` valores, mas alguns deles são armazenados incorretamente como cadeias de caracteres no Azure Cosmos DB. O esquema de fidelidade completa retornará valores digitados corretamente `date` e valores formatados incorretamente `string` .
O número de casos são informações armazenadas como um `int32` valor, mas há um valor que é inserido como um número decimal. Esse valor tem o `float64` tipo. Se houver alguns valores que excedam o maior `int32` número, eles serão armazenados como o `int64` tipo. Todos os `geo_id` valores neste exemplo são armazenados como `string` tipos.

> [!IMPORTANT]
> A `OPENROWSET` função sem uma `WITH` cláusula expõe os valores com os tipos esperados e os valores com tipos inseridos incorretamente. Essa função foi projetada para exploração de dados e não para relatórios. Não analise valores JSON retornados dessa função para criar relatórios. Use uma [cláusula WITH](#query-items-with-full-fidelity-schema) explícita para criar seus relatórios. Você deve limpar os valores que têm tipos incorretos no contêiner de Azure Cosmos DB para aplicar correções no repositório analítico de fidelidade total.

Se precisar consultar Azure Cosmos DB contas do tipo de API do Mongo DB, você pode saber mais sobre a representação de esquema de fidelidade total no repositório analítico e os nomes de propriedade estendida a serem usados no [que é Azure Cosmos DB armazenamento analítico (versão prévia)?](../../cosmos-db/analytical-store-introduction.md#analytical-schema).

### <a name="query-items-with-full-fidelity-schema"></a>Itens de consulta com esquema de fidelidade total

Ao consultar o esquema de fidelidade completa, você precisa especificar explicitamente o tipo SQL e o tipo de propriedade de Azure Cosmos DB esperado na `WITH` cláusula. Não use `OPENROWSET` uma `WITH` cláusula nos relatórios porque o formato do conjunto de resultados pode ser alterado na visualização com base nos comentários.

No exemplo a seguir, vamos pressupor que `string` é o tipo correto para a `geo_id` propriedade e `int32` é o tipo correto para a `cases` Propriedade:

```sql
SELECT geo_id, cases = SUM(cases)
FROM OPENROWSET(
      'CosmosDB'
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Ecdc
    ) WITH ( geo_id VARCHAR(50) '$.geo_id.string',
             cases INT '$.cases.int32'
    ) as rows
GROUP BY geo_id
```

Os valores de `geo_id` e `cases` que têm outros tipos serão retornados como `NULL` valores. Essa consulta fará referência somente ao `cases` com o tipo especificado na expressão ( `cases.int32` ).

Se você tiver valores com outros tipos ( `cases.int64` , `cases.float64` ) que não podem ser limpos em um contêiner Azure Cosmos DB, você precisará referenciá-los explicitamente em uma `WITH` cláusula e combinar os resultados. A consulta a seguir agrega ambos `int32` , `int64` e `float64` armazenados na `cases` coluna:

```sql
SELECT geo_id, cases = SUM(cases_int) + SUM(cases_bigint) + SUM(cases_float)
FROM OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Ecdc
    ) WITH ( geo_id VARCHAR(50) '$.geo_id.string', 
             cases_int INT '$.cases.int32',
             cases_bigint BIGINT '$.cases.int64',
             cases_float FLOAT '$.cases.float64'
    ) as rows
GROUP BY geo_id
```

Neste exemplo, o número de casos é armazenado como `int32` `int64` valores, ou `float64` . Todos os valores devem ser extraídos para calcular o número de casos por país.

## <a name="known-issues"></a>Problemas conhecidos

- A experiência de consulta que o pool SQL sem servidor fornece para [Azure Cosmos DB esquema de fidelidade total](#full-fidelity-schema) é um comportamento temporário que será alterado com base nos comentários de visualização. Não confie no esquema que a `OPENROWSET` função sem a `WITH` cláusula fornece durante a visualização pública, pois a experiência de consulta pode estar alinhada ao esquema bem definido com base nos comentários dos clientes. Para fornecer comentários, entre em contato com a [equipe de produto do Azure Synapse link](mailto:cosmosdbsynapselink@microsoft.com).
- Um pool SQL sem servidor retornará um aviso de tempo de compilação se o `OPENROWSET` agrupamento de coluna não tiver codificação UTF-8. Você pode alterar facilmente o agrupamento padrão para todas as `OPENROWSET` funções em execução no banco de dados atual usando a instrução T-SQL `alter database current collate Latin1_General_100_CI_AS_SC_UTF8` .

Possíveis erros e ações de solução de problemas estão listados na tabela a seguir.

| Erro | Causa raiz |
| --- | --- |
| Erros de sintaxe:<br/> -Sintaxe incorreta próxima a `Openrowset`<br/> - `...` Não é uma `BULK OPENROWSET` opção de provedor reconhecida.<br/> -Sintaxe incorreta próxima a `...` | Possíveis causas raiz:<br/> -Não está usando CosmosDB como o primeiro parâmetro.<br/> -Usando um literal de cadeia de caracteres em vez de um identificador no terceiro parâmetro.<br/> -Não especificando o terceiro parâmetro (nome do contêiner). |
| Ocorreu um erro na cadeia de conexão CosmosDB. | -A conta, o banco de dados ou a chave não está especificado. <br/> -Há alguma opção em uma cadeia de conexão que não é reconhecida.<br/> -Um ponto e vírgula ( `;` ) é colocado no final de uma cadeia de conexão. |
| Falha ao resolver o caminho CosmosDB com o erro "nome de conta incorreto" ou "nome de banco de dados incorreto". | O nome da conta, o nome do banco de dados ou o contêiner especificado não pode ser encontrado ou o armazenamento analítico não foi habilitado para a coleção especificada.|
| Falha ao resolver o caminho CosmosDB com o erro "valor de segredo incorreto" ou "segredo é nulo ou vazio". | A chave de conta não é válida ou está ausente. |
| A coluna `column name` do tipo `type name` não é compatível com o tipo de dados externo `type name` . | O tipo de coluna especificado na `WITH` cláusula não corresponde ao tipo no contêiner de Azure Cosmos DB. Tente alterar o tipo de coluna conforme descrito na seção [Azure Cosmos DB para mapeamentos de tipo SQL](#azure-cosmos-db-to-sql-type-mappings)ou use o `VARCHAR` tipo. |
| A coluna contém `NULL` valores em todas as células. | Possivelmente um nome de coluna errado ou uma expressão de caminho na `WITH` cláusula. O nome da coluna (ou expressão de caminho após o tipo de coluna) na `WITH` cláusula deve corresponder a algum nome de propriedade na coleção de Azure Cosmos DB. A comparação diferencia *maiúsculas de minúsculas*. Por exemplo, `productCode` e `ProductCode` são propriedades diferentes. |

Você pode relatar sugestões e problemas na [página de comentários do Azure Synapse Analytics](https://feedback.azure.com/forums/307516-azure-synapse-analytics?category_id=387862).

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte os seguintes artigos:

- [Usar o Power BI e o pool de SQL sem servidor com o link Synapse do Azure](../../cosmos-db/synapse-link-power-bi.md)
- [Criar e usar modos de exibição em um pool SQL sem servidor](create-use-views.md)
- [Tutorial sobre como criar exibições de pool de SQL sem servidor em Azure Cosmos DB e conectá-las a modelos de Power BI por meio do DirectQuery](./tutorial-data-analyst.md)
