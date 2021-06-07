---
title: 'Tutorial: Ferramenta de migração de banco de dados do Azure Cosmos DB'
description: 'Tutorial: Saiba como usar as ferramentas de migração de dados de software livre do Azure Cosmos DB para importar dados para o Azure Cosmos DB de várias fontes, incluindo MongoDB, SQL Server, Armazenamento de tabelas, Amazon DynamoDB, CSV e arquivos JSON. Conversão de CSV para JSON.'
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: tutorial
ms.date: 10/23/2020
ms.author: dech
ms.openlocfilehash: 1cee4d2ad1bc7f362a045a5991624ec43521b8d2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96341641"
---
# <a name="tutorial-use-data-migration-tool-to-migrate-your-data-to-azure-cosmos-db"></a>Tutorial: Usar a ferramenta de migração de dados para migrar seus dados para o Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Este tutorial fornece instruções sobre como usar a ferramenta Migração de Dados do Azure Cosmos DB, que pode importar dados de várias fontes para contêineres e tabelas do Azure Cosmos. Você pode importar de arquivos JSON, arquivos CSV, SQL, MongoDB, Armazenamento de Tabelas do Azure, Amazon DynamoDB e até mesmo coleções da API de SQL do Azure Cosmos DB. Migre dados para coleções e tabelas para uso com o Azure Cosmos DB. A ferramenta de Migração de Dados também pode ser usada ao migrar de uma coleção de partição única para uma coleção de várias partições na API do SQL.

> [!NOTE]
> A ferramenta de Migração de Dados do Azure Cosmos DB é uma ferramenta de software livre criada para pequenas migrações. Para migrações maiores, veja nosso [guia para ingerir dados](cosmosdb-migrationchoices.md).

* **[API do SQL](./introduction.md)** – você pode usar qualquer uma das opções de fonte fornecidas na ferramenta de Migração de Dados para importar dados em pequena escala. [Conheça as opções de migração para importar dados em grande escala](cosmosdb-migrationchoices.md).
* **[API de Tabela](table-introduction.md)** – você pode usar a ferramenta de Migração de dados ou [AzCopy](table-import.md#migrate-data-by-using-azcopy) para importar dados. Para obter mais informações, consulte [Importar dados para uso com a API de tabela do Azure Cosmos DB](table-import.md).
* **[API para MongoDB do Azure Cosmos DB](mongodb-introduction.md)** – a ferramenta de Migração de Dados não dá suporte à API do MongoDB do Azure Cosmos DB, seja como origem ou como destino. Se você desejar migrar os dados de ou para coleções no Azure Cosmos DB, veja [Como migrar dados do MongoDB para um banco de dados do Cosmos com uma API do MongoDB do Azure Cosmos DB](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json) para obter instruções. Você ainda pode usar a ferramenta de migração de dados para exportar dados do MongoDB para coleções de API do SQL do Azure Cosmos DB para uso com a API do SQL.
* **[API do Cassandra](graph-introduction.md)** – a ferramenta de Migração de dados não é uma ferramenta de importação com suporte para contas de API do Cassandra. [Conheça as opções de migração para importar dados para a API do Cassandra](cosmosdb-migrationchoices.md#azure-cosmos-db-cassandra-api)
* **[API do Gremlin](graph-introduction.md)** : a ferramenta de Migração de dados não é uma ferramenta de importação com suporte para contas de API do Gremlin atualmente. [Conheça as opções de migração para importar dados para a API do Gremlin](cosmosdb-migrationchoices.md#other-apis) 

Este tutorial cobre as seguintes tarefas:

> [!div class="checklist"]
> * Instalação da ferramenta de Migração de Dados
> * Importação de dados de diferentes fontes de dados
> * Exportação do Azure Cosmos DB para o JSON

## <a name="prerequisites"></a><a id="Prerequisites"></a>Pré-requisitos

Antes de seguir as instruções deste artigo, realize as seguintes etapas:

* **Instale** o [Microsoft .NET Framework 4.51](https://www.microsoft.com/download/developer-tools.aspx) ou superior.

* **Aumentar a taxa de transferência:** A duração da sua migração de dados depende da taxa de transferência que você configurar para uma coleção individual ou um conjunto de coleções. Certifique-se de aumentar a taxa de transferência para migrações de dados maiores. Depois de concluir a migração, diminua a taxa de transferência para economizar custos. Para obter mais informações sobre como aumentar a produtividade no portal do Azure, confira [níveis de desempenho](performance-levels.md) e [tipos de preço](https://azure.microsoft.com/pricing/details/cosmos-db/) no Azure Cosmos DB.

* **Criar recursos do Azure Cosmos DB:** Antes de começar a migrar dados, crie previamente todas as suas coleções no portal do Azure. Para migrar para uma conta do Azure Cosmos DB que tenha produtividade de nível de banco de dados, forneça uma chave de partição ao criar os contêineres do Azure Cosmos.

> [!IMPORTANT]
> Para garantir que a ferramenta de Migração de dados use o protocolo TLS 1.2 ao se conectar às contas do Azure Cosmos, use a versão 4.7 do .NET Framework ou siga as instruções [neste artigo](/dotnet/framework/network-programming/tls).

## <a name="overview"></a><a id="Overviewl"></a>Visão geral

A ferramenta de Migração de Dados é uma solução de software livre que importa dados para o Azure Cosmos DB de uma variedade de fontes, incluindo:

* Arquivos JSON
* MongoDB
* SQL Server
* Arquivos CSV
* Armazenamento da tabela do Azure
* Amazon DynamoDB
* HBase
* Contêineres do Azure Cosmos

Embora a ferramenta de importação inclua uma interface gráfica do usuário (dtui.exe), ela também pode ser controlada pela linha de comando (dt.exe). Na verdade, há uma opção de extrair o comando associado depois de configurar uma importação por meio da interface do usuário. É possível transformar os dados de origem tabulares, como SQL Server ou arquivos CSV para criar relações hierárquicas (subdocumentos) durante a importação. Continue lendo para saber mais sobre as opções de origem, comandos de exemplo para importar de cada origem, opções de destino e resultados de importação de visualização.

> [!NOTE]
> Você só deve usar a ferramenta de migração do Azure Cosmos DB para pequenas migrações. Para migrações grandes, veja nosso [guia para ingerir dados](cosmosdb-migrationchoices.md).

## <a name="installation"></a><a id="Install"></a>Instalação

O código-fonte da ferramenta de migração está disponível no GitHub [neste repositório](https://github.com/azure/azure-documentdb-datamigrationtool). É possível baixar e compilar a solução localmente ou [baixar um binário pré-compilado](https://aka.ms/csdmtool) e, em seguida, executar:

* **Dtui.exe**: Versão da interface gráfica da ferramenta
* **Dt.exe**: Versão de linha de comando da ferramenta

## <a name="select-data-source"></a>Selecione uma fonte de dados

Depois de instalar a ferramenta, é hora de importar os dados. Que tipo de dados você deseja importar?

* [Arquivos JSON](#JSON)
* [MongoDB](#MongoDB)
* [Arquivos de exportação do MongoDB](#MongoDBExport)
* [SQL Server](#SQL)
* [Arquivos CSV](#CSV)
* [Armazenamento de Tabelas do Azure](#AzureTableSource)
* [Amazon DynamoDB](#DynamoDBSource)
* [Blob](#BlobImport)
* [Contêineres do Azure Cosmos](#SQLSource)
* [HBase](#HBaseSource)
* [Importação em massa do Azure Cosmos DB](#SQLBulkTarget)
* [Importação de registro sequencial do Azure Cosmos DB](#SQLSeqTarget)

## <a name="import-json-files"></a><a id="JSON"></a>Importar arquivos JSON

A opção de importador de origem de arquivo JSON permite importar um ou mais arquivos JSON de documento único ou arquivos JSON que tenham uma matriz de documentos JSON. Ao adicionar pastas que tenham arquivos JSON para importar, você tem a opção de pesquisar recursivamente arquivos em subpastas.

:::image type="content" source="./media/import-data/jsonsource.png" alt-text="Captura de tela das opções de origem do arquivo JSON – ferramentas de migração de banco de dados":::

A cadeia de conexão está no seguinte formato:

`AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>`

* `<CosmosDB Endpoint>` é o URI do ponto de extremidade. Você pode obter esse valor no portal do Azure. Navegue até a conta do Azure Cosmos. Abra o painel **Visão geral** e copie o valor do **URI**.
* `<AccountKey>` é a "Senha" ou a **CHAVE PRIMÁRIA**. Você pode obter esse valor no portal do Azure. Navegue até a conta do Azure Cosmos. Abra o painel **Cadeias de Conexão** ou **Chaves** e copie o valor da "Senha" ou da **CHAVE PRIMÁRIA**.
* `<CosmosDB Database>` é o nome do banco de dados do Cosmos DB.

Exemplo: `AccountEndpoint=https://myCosmosDBName.documents.azure.com:443/;AccountKey=wJmFRYna6ttQ79ATmrTMKql8vPri84QBiHTt6oinFkZRvoe7Vv81x9sn6zlVlBY10bEPMgGM982wfYXpWXWB9w==;Database=myDatabaseName`

> [!NOTE]
> Use o comando Verify para garantir que a conta do Cosmos DB especificada no campo de cadeia de conexão pode ser acessada.

Aqui estão alguns exemplos de linha de comando para importar os arquivos JSON:

```console
#Import a single JSON file
dt.exe /s:JsonFile /s.Files:.\Sessions.json /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Sessions /t.CollectionThroughput:2500

#Import a directory of JSON files
dt.exe /s:JsonFile /s.Files:C:\TESessions\*.json /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Sessions /t.CollectionThroughput:2500

#Import a directory (including sub-directories) of JSON files
dt.exe /s:JsonFile /s.Files:C:\LastFMMusic\**\*.json /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Music /t.CollectionThroughput:2500

#Import a directory (single), directory (recursive), and individual JSON files
dt.exe /s:JsonFile /s.Files:C:\Tweets\*.*;C:\LargeDocs\**\*.*;C:\TESessions\Session48172.json;C:\TESessions\Session48173.json;C:\TESessions\Session48174.json;C:\TESessions\Session48175.json;C:\TESessions\Session48177.json /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:subs /t.CollectionThroughput:2500

#Import a single JSON file and partition the data across 4 collections
dt.exe /s:JsonFile /s.Files:D:\\CompanyData\\Companies.json /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:comp[1-4] /t.PartitionKey:name /t.CollectionThroughput:2500
```

## <a name="import-from-mongodb"></a><a id="MongoDB"></a>Importar do MongoDB

> [!IMPORTANT]
> Caso esteja importando para uma conta do Cosmos configurada com a API do MongoDB do Azure Cosmos DB, siga estas [instruções](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json).

Com a opção de importador de origem do MongoDB, é possível importar de uma coleção do MongoDB individual e, como opção, filtrar documentos usando uma consulta e modificar a estrutura do documento usando uma projeção.  

:::image type="content" source="./media/import-data/mongodbsource.png" alt-text="Captura de tela das opções de origem do MongoDB":::

A cadeia de conexão está no formato padrão do MongoDB:

`mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database>`

> [!NOTE]
> Use o comando Verify para garantir que a instância do MongoDB especificada no campo de cadeia de conexão pode ser acessada.

Digite o nome da coleção por meio da qual os dados serão importados. Como opção, é possível especificar ou fornecer um arquivo para uma consulta, como `{pop: {$gt:5000}}`, ou uma projeção, como `{loc:0}`, para o filtro e a forma dos dados sendo importados.

Aqui estão alguns exemplos de linha de comando para importar por meio do MongoDB:

```console
#Import all documents from a MongoDB collection
dt.exe /s:MongoDB /s.ConnectionString:mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database> /s.Collection:zips /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:BulkZips /t.IdField:_id /t.CollectionThroughput:2500

#Import documents from a MongoDB collection which match the query and exclude the loc field
dt.exe /s:MongoDB /s.ConnectionString:mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database> /s.Collection:zips /s.Query:{pop:{$gt:50000}} /s.Projection:{loc:0} /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:BulkZipsTransform /t.IdField:_id/t.CollectionThroughput:2500
```

## <a name="import-mongodb-export-files"></a><a id="MongoDBExport"></a>Importar arquivos de exportação do MongoDB

> [!IMPORTANT]
> Caso esteja importando para uma conta do Azure Cosmos DB com suporte para o MongoDB, siga estas [instruções](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json).

A opção do importador de origem de arquivo JSON de exportação do MongoDB permite que você importe um ou mais arquivos JSON produzidos por meio do utilitário mongoexport.  

:::image type="content" source="./media/import-data/mongodbexportsource.png" alt-text="Captura de tela das opções de origem de exportação do MongoDB":::

Ao adicionar pastas que tenham arquivos JSON de exportação do MongoDB, você tem a opção de pesquisar recursivamente arquivos em subpastas.

Aqui está um exemplo de linha de comando para importar de arquivos de JSON de exportação do MongoDB:

```console
dt.exe /s:MongoDBExport /s.Files:D:\mongoemployees.json /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:employees /t.IdField:_id /t.Dates:Epoch /t.CollectionThroughput:2500
```

## <a name="import-from-sql-server"></a><a id="SQL"></a>Importar do SQL Server

A opção do importador de origem do SQL permite importar de um banco de dados do SQL Server individual e, opcionalmente, filtrar os registros a serem importados usando uma consulta. Além disso, você pode modificar a estrutura do documento, especificando um separador de aninhamento (falaremos mais sobre isso em instantes).  

:::image type="content" source="./media/import-data/sqlexportsource.png" alt-text="Captura de tela das opções de origem do SQL – ferramentas de migração de banco de dados":::

O formato da cadeia de conexão é o formato da cadeia de conexão SQL padrão.

> [!NOTE]
> Use o comando Verify para garantir que a instância do SQL Server especificada no campo Cadeia de conexão pode ser acessada.

A propriedade de separador de aninhamento é usada para criar relacionamentos hierárquicos (sub-documentos) durante a importação. Considere a seguinte consulta SQL:

`select CAST(BusinessEntityID AS varchar) as Id, Name, AddressType as [Address.AddressType], AddressLine1 as [Address.AddressLine1], City as [Address.Location.City], StateProvinceName as [Address.Location.StateProvinceName], PostalCode as [Address.PostalCode], CountryRegionName as [Address.CountryRegionName] from Sales.vStoreWithAddresses WHERE AddressType='Main Office'`

Que retorna os seguintes resultados (parciais):

:::image type="content" source="./media/import-data/sqlqueryresults.png" alt-text="Captura de tela dos resultados de consulta SQL":::

Observe os aliases como Address.AddressType e Address.Location.StateProvinceName. Especificando um separador de aninhamento de '.', a ferramenta de importação cria os subdocumentos Address e Address.Location durante a importação. Este é um exemplo de um documento resultante no Azure Cosmos DB:

*{ "id": "956", "Name": "Finer Sales and Service", "Address": { "AddressType": "Main Office", "AddressLine1": "#500-75 O'Connor Street", "Location": { "City": "Ottawa", "StateProvinceName": "Ontario" }, "PostalCode": "K4B 1S2", "CountryRegionName": "Canada" } }*

Aqui estão alguns exemplos de linha de comando para importar do SQL Server:

```console
#Import records from SQL which match a query
dt.exe /s:SQL /s.ConnectionString:"Data Source=<server>;Initial Catalog=AdventureWorks;User Id=advworks;Password=<password>;" /s.Query:"select CAST(BusinessEntityID AS varchar) as Id, * from Sales.vStoreWithAddresses WHERE AddressType='Main Office'" /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Stores /t.IdField:Id /t.CollectionThroughput:2500

#Import records from sql which match a query and create hierarchical relationships
dt.exe /s:SQL /s.ConnectionString:"Data Source=<server>;Initial Catalog=AdventureWorks;User Id=advworks;Password=<password>;" /s.Query:"select CAST(BusinessEntityID AS varchar) as Id, Name, AddressType as [Address.AddressType], AddressLine1 as [Address.AddressLine1], City as [Address.Location.City], StateProvinceName as [Address.Location.StateProvinceName], PostalCode as [Address.PostalCode], CountryRegionName as [Address.CountryRegionName] from Sales.vStoreWithAddresses WHERE AddressType='Main Office'" /s.NestingSeparator:. /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:StoresSub /t.IdField:Id /t.CollectionThroughput:2500
```

## <a name="import-csv-files-and-convert-csv-to-json"></a><a id="CSV"></a>Importar arquivos CSV e converter CSV em JSON

A opção de importador de origem de arquivo CSV permite que você importe um ou mais arquivos CSV. Ao adicionar pastas que tenham arquivos CSV para importar, você tem a opção de pesquisar recursivamente arquivos em subpastas.

:::image type="content" source="media/import-data/csvsource.png" alt-text="Captura de tela das opções de origem de CSV – CSV em JSON":::

De forma semelhante à origem de SQL, a propriedade de separador de aninhamento pode ser usada para criar relacionamentos hierárquicos (sub-documentos) durante a importação. Considere a seguinte linha de cabeçalho CSV e linhas de dados:

:::image type="content" source="./media/import-data/csvsample.png" alt-text="Captura de tela dos registros de exemplo de CSV – CSV em JSON":::

Observe os aliases como DomainInfo.Domain_Name e RedirectInfo.Redirecting. Especificando um separador de aninhamento de '.', a ferramenta de importação cria os sub-documentos DomainInfo e RedirectInfo durante a importação. Este é um exemplo de um documento resultante no Azure Cosmos DB:

*{ "DomainInfo": { "Domain_Name": "ACUS.GOV", "Domain_Name_Address": "https:\//www.ACUS.GOV" }, "Federal Agency": "Administrative Conference of the United States", "RedirectInfo": { "Redirecting": "0", "Redirect_Destination": "" }, "id": "9cc565c5-ebcd-1c03-ebd3-cc3e2ecd814d" }*

A ferramenta de importação tenta deduzir informações de tipo para valores sem aspas em arquivos CSV (valores entre aspas são tratados sempre como cadeias de caracteres).  Os tipos são identificados na seguinte ordem: número, datetime, booliano.  

Há duas outras coisas a observar sobre a importação de CSV:

1. Por padrão, os valores sem aspas sempre são cortados para tabulações e espaços, enquanto os valores entre aspas são preservados como estão. Esse comportamento pode ser substituído pela caixa de seleção com valores entre aspas de corte ou pela opção de linha de comando /s.TrimQuoted.
2. Por padrão, um nulo sem aspas é tratado como um valor nulo. Esse comportamento pode ser substituído (ou seja, tratar um nulo sem aspas como uma cadeia de caracteres "null") com o NULL de tratamento sem aspas como caixa de seleção da cadeia de caracteres ou a opção de linha de comando /s.NoUnquotedNulls.

Aqui está um exemplo de linha de comando para importação de CSV:

```console
dt.exe /s:CsvFile /s.Files:.\Employees.csv /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Employees /t.IdField:EntityID /t.CollectionThroughput:2500
```

## <a name="import-from-azure-table-storage"></a><a id="AzureTableSource"></a>Importar por meio do armazenamento de tabela do Azure

A opção de importador de origem de armazenamento de tabela do Azure permite importar de uma tabela de armazenamento de uma tabela individual do Azure. Como outra opção, você pode filtrar as entidades da tabela a serem importadas.

Você pode gerar dados que foram importados do Armazenamento de Tabelas do Azure para tabelas do Azure Cosmos DB e entidades para uso com a API de Tabela. Dados importados também podem ser a saída para coleções e documentos para uso com a API do SQL. No entanto, a API de Tabela só está disponível como um destino no utilitário de linha de comando. É possível exportar para API de Tabela usando a interface de usuário da ferramenta de Migração de Dados. Para obter mais informações, consulte [Importar dados para uso com a API de tabela do Azure Cosmos DB](table-import.md).

:::image type="content" source="./media/import-data/azuretablesource.png" alt-text="Captura de tela das opções de origem de armazenamento da Tabela do Azure":::

O formato da cadeia de conexão de armazenamento de tabela do Azure é:

`DefaultEndpointsProtocol=<protocol>;AccountName=<Account Name>;AccountKey=<Account Key>;`

> [!NOTE]
> Use o comando Verify para garantir que a instância de armazenamento da tabela do Azure especificada no campo de cadeia de conexão pode ser acessada.

Digite o nome da tabela do Azure da qual os dados serão importados. Opcionalmente, você pode especificar um [filtro](/visualstudio/azure/vs-azure-tools-table-designer-construct-filter-strings).

A opção de importador de origem de armazenamento de tabela do Azure tem as seguintes opções adicionais:

1. Incluir campos internos
   1. Todos - incluir todos os campos internos (PartitionKey, RowKey e Timestamp)
   2. Nenhum - excluir todos os campos internos
   3. RowKey - incluir somente o campo RowKey
2. Selecionar colunas
   1. Filtros de armazenamento de tabela do Azure não dão suporte a projeções. Se você quiser importar somente propriedades específicas de entidade da tabela do Azure, adicione-as à lista Selecionar colunas. Todas as outras propriedades da entidade são ignoradas.

Aqui está um exemplo de linha de comando para importar por meio do armazenamento de tabela do Azure:

```console
dt.exe /s:AzureTable /s.ConnectionString:"DefaultEndpointsProtocol=https;AccountName=<Account Name>;AccountKey=<Account Key>" /s.Table:metrics /s.InternalFields:All /s.Filter:"PartitionKey eq 'Partition1' and RowKey gt '00001'" /s.Projection:ObjectCount;ObjectSize  /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:metrics /t.CollectionThroughput:2500
```

## <a name="import-from-amazon-dynamodb"></a><a id="DynamoDBSource"></a>Importar do Amazon DynamoDB

A opção do importador de origem do Amazon DynamoDB permite a importação de uma única tabela do Amazon DynamoDB. Como opção, é possível filtrar as entidades a serem importadas. Vários modelos são fornecidos para que a configuração de uma importação seja tão fácil quanto possível.

:::image type="content" source="./media/import-data/dynamodbsource1.png" alt-text="Captura de tela das opções de origem do Amazon DynamoDB – ferramentas de migração de bancos de dados.":::

:::image type="content" source="./media/import-data/dynamodbsource2.png" alt-text="Captura de tela das opções de origem do Amazon DynamoDB com modelo – ferramentas de migração de bancos de dados.":::

O formato da cadeia de conexão do Amazon DynamoDB é:

`ServiceURL=<Service Address>;AccessKey=<Access Key>;SecretKey=<Secret Key>;`

> [!NOTE]
> Use o comando Verify para garantir que a instância do Amazon DynamoDB especificada no campo de cadeia de conexão possa ser acessada.

Aqui está um exemplo de linha de comando para importar do Amazon DynamoDB:

```console
dt.exe /s:DynamoDB /s.ConnectionString:ServiceURL=https://dynamodb.us-east-1.amazonaws.com;AccessKey=<accessKey>;SecretKey=<secretKey> /s.Request:"{   """TableName""": """ProductCatalog""" }" /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<Azure Cosmos DB Endpoint>;AccountKey=<Azure Cosmos DB Key>;Database=<Azure Cosmos database>;" /t.Collection:catalogCollection /t.CollectionThroughput:2500
```

## <a name="import-from-azure-blob-storage"></a><a id="BlobImport"></a>Importar do armazenamento de Blob do Azure

O arquivo JSON, arquivo de exportação do MongoDB e opções de importador de origem do arquivo CSV permitem que você importe um ou mais arquivos de Armazenamento de Blob do Azure. Depois de especificar uma URL do contêiner de Blob e a chave de conta, forneça uma expressão regular para selecionar os arquivos a serem importados.

:::image type="content" source="./media/import-data/blobsource.png" alt-text="Captura de tela das opções de origem de arquivo de blob":::

Eis um exemplo de linha de comando para importar arquivos JSON do Armazenamento de Blob do Azure:

```console
dt.exe /s:JsonFile /s.Files:"blobs://<account key>@account.blob.core.windows.net:443/importcontainer/.*" /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:doctest
```

## <a name="import-from-a-sql-api-collection"></a><a id="SQLSource"></a>Importar de uma coleção de API do SQL

A opção de importador de origem do Azure Cosmos DB permite importar dados de um ou mais contêineres do Azure Cosmos e, opcionalmente, filtrar documentos usando uma consulta.  

:::image type="content" source="./media/import-data/documentdbsource.png" alt-text="Captura de tela das opções de origem do Azure Cosmos DB":::

O formato da cadeia de conexão do Azure Cosmos DB é:

`AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;`

É possível recuperar a cadeia de conexão da conta do Azure Cosmos DB na página Chaves do portal do Azure, conforme descrito em [Como gerenciar uma conta do Azure Cosmos DB](./how-to-manage-database-account.md). No entanto, o nome do banco de dados deve ser acrescentado à cadeia de conexão no seguinte formato:

`Database=<CosmosDB Database>;`

> [!NOTE]
> Use o comando Verify para garantir que a instância do Azure Cosmos DB especificada no campo de cadeia de conexão pode ser acessada.

Para importar de um único contêiner do Azure Cosmos, insira o nome da coleção da qual os dados serão importados. Para importar de mais de um contêiner do Azure Cosmos, forneça uma expressão regular para encontrar a correspondência de um ou mais nomes de coleções (por exemplo, collection01 | collection02 | collection03). Outra opção é especificar ou fornecer um arquivo para uma consulta para filtrar e moldar os dados sendo importados.

> [!NOTE]
> Como o campo de coleção aceita expressões regulares, se você estiver importando de uma única coleção cujo nome tenha caracteres de expressão regular, esses caracteres devem ser substituídos adequadamente.

A opção de importador de origem do Azure Cosmos DB tem as seguintes opções avançadas:

1. Incluir Campos Internos: Especifica se as propriedades do sistema de documentos do Azure Cosmos DB devem ou não ser incluídas na exportação (por exemplo, _rid, _ts).
2. Número de repetições em caso de falha: Especifica o número de vezes para tentar a conexão novamente com o Azure Cosmos DB em caso de falhas transitórias (por exemplo, interrupção da conectividade de rede).
3. Intervalo de Repetição: Especifica o tempo de espera para tentar a conexão novamente com o Azure Cosmos DB em caso de falhas transitórias (por exemplo, interrupção da conectividade de rede).
4. Modo de Conexão: especifica o modo de conexão a ser usado com o Azure Cosmos DB. As opções disponíveis são DirectTcp, DirectHttps e Gateway. Os modos de conexão direta são mais rápidos, enquanto que o modo de gateway é mais amigável ao firewall, uma vez que só usa a porta 443.

:::image type="content" source="./media/import-data/documentdbsourceoptions.png" alt-text="Captura de tela das opções avançadas de origem do Azure Cosmos DB":::

> [!TIP]
> A ferramenta de importação usa como padrão o modo de conexão DirectTcp. Se você enfrentar problemas de firewall, alterne para o modo de conexão Gateway, uma vez que ele só requer a porta 443.

Estes são alguns exemplos de linha de comando para importar do Azure Cosmos DB:

```console
#Migrate data from one Azure Cosmos container to another Azure Cosmos containers
dt.exe /s:DocumentDB /s.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /s.Collection:TEColl /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:TESessions /t.CollectionThroughput:2500

#Migrate data from more than one Azure Cosmos container to a single Azure Cosmos container
dt.exe /s:DocumentDB /s.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /s.Collection:comp1|comp2|comp3|comp4 /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:singleCollection /t.CollectionThroughput:2500

#Export an Azure Cosmos container to a JSON file
dt.exe /s:DocumentDB /s.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /s.Collection:StoresSub /t:JsonFile /t.File:StoresExport.json /t.Overwrite
```

> [!TIP]
> A ferramenta de Importação de Dados do Azure Cosmos DB também dá suporte à importação de dados do [Emulador do Azure Cosmos DB](local-emulator.md). Ao importar dados de um emulador local, defina o ponto de extremidade como `https://localhost:<port>`.

## <a name="import-from-hbase"></a><a id="HBaseSource"></a>Importar do HBase

A opção de importador de origem do HBase permite importar dados de uma tabela do HBase e, opcionalmente, filtrar os dados. Vários modelos são fornecidos para que a configuração de uma importação seja tão fácil quanto possível.

:::image type="content" source="./media/import-data/hbasesource1.png" alt-text="Captura de tela das opções de origem do HBase.":::

:::image type="content" source="./media/import-data/hbasesource2.png" alt-text="Captura de tela das opções de origem do HBase com o menu contextual Filtro expandido.":::

O formato da cadeia de conexão HBase Stargate é:

`ServiceURL=<server-address>;Username=<username>;Password=<password>`

> [!NOTE]
> Use o comando Verify para garantir que a instância do HBase especificada no campo de cadeia de conexão possa ser acessada.

Aqui está um exemplo de linha de comando para importar do HBase:

```console
dt.exe /s:HBase /s.ConnectionString:ServiceURL=<server-address>;Username=<username>;Password=<password> /s.Table:Contacts /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:hbaseimport
```

## <a name="import-to-the-sql-api-bulk-import"></a><a id="SQLBulkTarget"></a>Importar para a API do SQL (importação em massa)

O importador em Massa do Azure Cosmos DB permite importar de qualquer uma das opções de origem disponíveis, usando um procedimento armazenado do Azure Cosmos DB para maior eficiência. A ferramenta dá suporte à importação para um contêiner de partição única do Azure Cosmos. Ela também dá suporte à importação fragmentada, na qual os dados são particionados em mais de um contêiner de partição única do Azure Cosmos. Para obter mais informações sobre o particionamento de dados, consulte [Particionamento e escala no Azure Cosmos DB](partitioning-overview.md). A ferramenta cria, executa e depois exclui o procedimento armazenado das coleções de destino.  

:::image type="content" source="./media/import-data/documentdbbulk.png" alt-text="Captura de tela das opções em massa do Azure Cosmos DB":::

O formato da cadeia de conexão do Azure Cosmos DB é:

`AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;`

A cadeia de conexão da conta do Azure Cosmos DB pode ser recuperada na página Chaves do portal do Azure, conforme descrito em [Como gerenciar uma conta do Azure Cosmos DB](./how-to-manage-database-account.md); no entanto, o nome do banco de dados deve ser acrescentado à cadeia de conexão no seguinte formato:

`Database=<CosmosDB Database>;`

> [!NOTE]
> Use o comando Verify para garantir que a instância do Azure Cosmos DB especificada no campo de cadeia de conexão pode ser acessada.

Para importar para uma única coleção, digite o nome da coleção da qual importar os dados e clique no botão Adicionar. Para importar para mais de uma coleção, insira o nome de cada coleção individualmente ou use a seguinte sintaxe para especificar mais de uma coleção: *collection_prefix*[start index - end index]. Ao especificar mais de uma coleção usando a sintaxe mencionada anteriormente, mantenha as seguintes diretrizes em mente:

1. Somente padrões de nome de intervalo inteiro têm suporte. Por exemplo, a especificação de coleção [0-3] cria as seguintes coleções: collection0, collection1, collection2, collection3.
2. Você pode usar uma sintaxe abreviada: collection[3] cria o mesmo conjunto de coleções mencionado na etapa 1.
3. Mais de uma substituição pode ser fornecida. Por exemplo, a coleção [0-1] [0-9] gera 20 nomes de coleção com zeros à esquerda (collection01... 02,... 03).

Depois de especificar o(s) nome(s) de coleção, escolha a taxa de transferência desejada da(s) coleção(ões) (de 400 a 10.000 RUs). Para uma importação com melhor desempenho, escolha uma taxa de transferência maior. Para obter mais informações sobre níveis de desempenho, consulte [Níveis de desempenho no Azure Cosmos DB](performance-levels.md).

> [!NOTE]
> A configuração de taxa de transferência de desempenho só se aplica à criação da coleção. Se a coleção especificada já existir, a taxa de transferência não será modificada.

Ao importar para mais de uma coleção, a ferramenta de importação oferece suporte à fragmentação baseada em hash. Nesse cenário, especifique a propriedade de documento que você deseja usar como a Chave de Partição. (Se a Chave de Partição for deixada em branco, os documentos são fragmentados aleatoriamente em coleções de destino.)

Como opção, é possível especificar qual campo na fonte de importação deve ser usado como a propriedade de ID do documento do Azure Cosmos DB durante a importação. Se documentos não tiverem essa propriedade, a ferramenta de importação gera um GUID como o valor da propriedade de ID.

Há uma série de opções avançadas disponíveis durante a importação. Em primeiro lugar, embora a ferramenta inclua um procedimento armazenado de importação em massa padrão (BulkInsert.js), você pode optar por especificar seu próprio procedimento armazenado de importação:

 :::image type="content" source="./media/import-data/bulkinsertsp.png" alt-text="Captura de tela da opção de sproc BULK INSERT do Azure Cosmos DB":::

Além disso, ao importar tipos de dados (por exemplo, do SQL Server ou do MongoDB), você pode escolher entre três opções de importação:

 :::image type="content" source="./media/import-data/datetimeoptions.png" alt-text="Captura de tela das opções de importação de data e hora do Azure Cosmos DB":::

* Cadeia de caracteres: persistir como um valor de cadeia de caracteres
* Época: persistir como um valor de número de época
* Ambos: persistir com os valores de número de cadeia de caracteres e de época. Esta opção cria um subdocumento, por exemplo: "date_joined": { "Value": "2013-10-21T21:17:25.2410000Z", "Epoch": 1382390245 }

O importador em Massa do Azure Cosmos DB tem as seguintes opções avançadas adicionais:

1. Tamanho do Lote: A ferramenta usa como padrão um tamanho de lote de 50.  Se os documentos a serem importados forem grandes, considere reduzir o tamanho do lote. Da mesma forma, se os documentos a serem importados forem pequenos, considere aumentar o tamanho do lote.
2. Tamanho Máximo de Script (bytes): A ferramenta usa como padrão um tamanho máximo de script de 512 KB.
3. Desabilitar a Geração Automática de ID: Se todos os documentos a serem importados tiverem um campo de ID, selecionar essa opção poderá aumentar o desempenho. Documentos que não tenham um campo de ID exclusiva não são importados.
4. Atualizar documentos existentes: a ferramenta usa como padrão não substituir os documentos existentes com conflitos de ID. Essa opção permite substituir documentos existentes por IDs correspondentes. Esse recurso é útil para migrações de dados agendadas que atualizam documentos existentes.
5. Número de repetições em caso de falha: Especifica quantas vezes se tenta refazer a conexão com o Azure Cosmos DB durante falhas transitórias (por exemplo, interrupção da conectividade de rede).
6. Intervalo de Repetição: Especifica o tempo de espera para tentar a conexão novamente com o Azure Cosmos DB em caso de falhas transitórias (por exemplo, interrupção da conectividade de rede).
7. Modo de Conexão: especifica o modo de conexão a ser usado com o Azure Cosmos DB. As opções disponíveis são DirectTcp, DirectHttps e Gateway. Os modos de conexão direta são mais rápidos, enquanto que o modo de gateway é mais amigável ao firewall, uma vez que só usa a porta 443.

:::image type="content" source="./media/import-data/docdbbulkoptions.png" alt-text="Captura de tela das opções avançadas de importação em massa do Azure Cosmos DB":::

> [!TIP]
> A ferramenta de importação usa como padrão o modo de conexão DirectTcp. Se você enfrentar problemas de firewall, alterne para o modo de conexão Gateway, uma vez que ele só requer a porta 443.

## <a name="import-to-the-sql-api-sequential-record-import"></a><a id="SQLSeqTarget"></a>Importar para a API do SQL (Importação de Registro Sequencial)

O importador de registro sequencial do Azure Cosmos DB permite a importação a partir de uma opção de origem disponível com base em cada registro. Você pode escolher esta opção se estiver importando para uma coleção existente que já atingiu a cota de procedimentos armazenados. A ferramenta dá suporte à importação para um único contêiner (partição única e várias partições) do Azure Cosmos. Ela também dá suporte à importação fragmentada, pela qual os dados são particionados em mais de um contêiner de partição única ou de várias partições do Azure Cosmos. Para obter mais informações sobre o particionamento de dados, consulte [Particionamento e escala no Azure Cosmos DB](partitioning-overview.md).

:::image type="content" source="./media/import-data/documentdbsequential.png" alt-text="Captura de tela das opções de importação de registro sequencial do Azure Cosmos DB":::

O formato da cadeia de conexão do Azure Cosmos DB é:

`AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;`

É possível recuperar a cadeia de conexão da conta do Azure Cosmos DB na página Chaves do portal do Azure, conforme descrito em [Como gerenciar uma conta do Azure Cosmos DB](./how-to-manage-database-account.md). No entanto, o nome do banco de dados deve ser acrescentado à cadeia de conexão no seguinte formato:

`Database=<Azure Cosmos database>;`

> [!NOTE]
> Use o comando Verify para garantir que a instância do Azure Cosmos DB especificada no campo de cadeia de conexão pode ser acessada.

Para importar para uma única coleção, digite o nome da coleção para a qual os dados serão importados, depois clique no botão Adicionar. Para importar para mais de uma coleção, insira o nome de cada coleção individualmente. Também é possível usar a sintaxe a seguir para especificar mais de uma coleção: *collection_prefix*[start index - end index]. Ao especificar mais de uma coleção por meio da sintaxe mencionada anteriormente, mantenha as seguintes diretrizes em mente:

1. Somente padrões de nome de intervalo inteiro têm suporte. Por exemplo, a especificação de coleção [0-3] cria as seguintes coleções: collection0, collection1, collection2, collection3.
2. Você pode usar uma sintaxe abreviada: collection[3] cria o mesmo conjunto de coleções mencionado na etapa 1.
3. Mais de uma substituição pode ser fornecida. Por exemplo, a coleção [0-1] [0-9] cria 20 nomes de coleção com zeros à esquerda (collection01... 02,... 03).

Depois de especificar o(s) nome(s) de coleção, escolha a taxa de transferência desejada da(s) coleção(ões) (de 400 a 250.000 RUs). Para uma importação com melhor desempenho, escolha uma taxa de transferência maior. Para obter mais informações sobre níveis de desempenho, consulte [Níveis de desempenho no Azure Cosmos DB](performance-levels.md). Qualquer importação para coleções com taxa de transferência acima de 10.000 RUs exige uma chave de partição. Se optar por ter mais de 250.000 RUs, você precisa fazer uma solicitação no portal para aumento da conta.

> [!NOTE]
> A configuração de taxa de transferência só se aplica à criação da coleção ou do banco de dados. Se a coleção especificada já existir, a taxa de transferência não será modificada.

Ao importar para mais de uma coleção, a ferramenta de importação oferece suporte à fragmentação baseada em hash. Nesse cenário, especifique a propriedade de documento que você deseja usar como a Chave de Partição. (Se a Chave de Partição for deixada em branco, os documentos são fragmentados aleatoriamente em coleções de destino.)

Como opção, é possível especificar qual campo na fonte de importação deve ser usado como a propriedade de ID do documento do Azure Cosmos DB durante a importação. (Se documentos não tiverem essa propriedade, a ferramenta de importação gera um GUID como o valor da propriedade de ID.)

Há uma série de opções avançadas disponíveis durante a importação. Primeiro, ao importar tipos de dados (por exemplo, do SQL Server ou do MongoDB), você pode escolher entre três opções de importação:

 :::image type="content" source="./media/import-data/datetimeoptions.png" alt-text="Captura de tela das opções de importação de data e hora do Azure Cosmos DB":::

* Cadeia de caracteres: persistir como um valor de cadeia de caracteres
* Época: persistir como um valor de número de época
* Ambos: persistir com os valores de número de cadeia de caracteres e de época. Esta opção cria um subdocumento, por exemplo: "date_joined": { "Value": "2013-10-21T21:17:25.2410000Z", "Epoch": 1382390245 }

O importador de Registro sequencial do Azure Cosmos DB tem as seguintes opções avançadas adicionais:

1. Número de Solicitações Paralelas: A ferramenta usa como padrão duas solicitações paralelas. Se os documentos a serem importados forem pequenos, considere aumentar o número de solicitações paralelas. Se este número for muito elevado, a importação poderá sofrer limitação.
2. Desabilitar a Geração Automática de ID: Se todos os documentos a serem importados tiverem um campo de ID, selecionar essa opção poderá aumentar o desempenho. Documentos que não tenham um campo de ID exclusiva não são importados.
3. Atualizar documentos existentes: a ferramenta usa como padrão não substituir os documentos existentes com conflitos de ID. Essa opção permite substituir documentos existentes por IDs correspondentes. Esse recurso é útil para migrações de dados agendadas que atualizam documentos existentes.
4. Número de repetições em caso de falha: Especifica quantas vezes se tenta refazer a conexão com o Azure Cosmos DB durante falhas transitórias (por exemplo, interrupção da conectividade de rede).
5. Intervalo de Repetição: Especifica o tempo de espera para tentar a conexão novamente com o Azure Cosmos DB durante falhas transitórias (por exemplo, interrupção da conectividade de rede).
6. Modo de Conexão: especifica o modo de conexão a ser usado com o Azure Cosmos DB. As opções disponíveis são DirectTcp, DirectHttps e Gateway. Os modos de conexão direta são mais rápidos, enquanto que o modo de gateway é mais amigável ao firewall, uma vez que só usa a porta 443.

:::image type="content" source="./media/import-data/documentdbsequentialoptions.png" alt-text="Captura de tela das opções avançadas de importação de registro sequencial do Azure Cosmos DB":::

> [!TIP]
> A ferramenta de importação usa como padrão o modo de conexão DirectTcp. Se você enfrentar problemas de firewall, alterne para o modo de conexão Gateway, uma vez que ele só requer a porta 443.

## <a name="specify-an-indexing-policy"></a><a id="IndexingPolicy"></a>Especificar uma política de indexação

Quando você permite que a ferramenta de migração crie coleções da API do SQL do Azure Cosmos DB durante a importação, é possível especificar a política de indexação das coleções. Na seção de opções avançadas das opções de Importação em massa e Registro sequencial do Azure Cosmos DB, navegue para a seção Política de indexação.

:::image type="content" source="./media/import-data/indexingpolicy1.png" alt-text="Captura de tela das opções avançadas da Política de Indexação do Azure Cosmos DB.":::

Usando a opção avançada de política de indexação, você pode selecionar um arquivo de política de indexação, inserir manualmente uma política de indexação ou selecionar a partir um conjunto de modelos padrão (clicando com o botão direito do mouse na caixa de texto da política indexação).

Os modelos de política que a ferramenta fornece são:

* Padrão. Essa política é a melhor ao executar consultas de igualdade em cadeias de caracteres. Ela também funciona se você usar consultas de ORDER BY, intervalo e igualdade para números. Essa política tem uma sobrecarga de armazenamento de índice menor que Intervalo.
* Intervalo. Essa política será mais útil ao usar consultas de ORDER BY, intervalo e igualdade em números e cadeias de caracteres. Essa política tem uma sobrecarga de armazenamento de índice maior do que Padrão ou Hash.

:::image type="content" source="./media/import-data/indexingpolicy2.png" alt-text="Captura de tela das opções avançadas da Política de Indexação do Azure Cosmos DB especificando informações de destino.":::

> [!NOTE]
> Se você não especificar uma política de indexação, a política padrão é aplicada. Para obter mais informações sobre políticas de indexação, consulte [Políticas de indexação do Azure Cosmos DB](index-policy.md).

## <a name="export-to-json-file"></a>Exportar para arquivo JSON

O exportador de JSON do Azure Cosmos DB permite exportar uma das opções de origem disponíveis para um arquivo JSON que tenha uma matriz de documentos JSON. A ferramenta lida com a exportação para você. Como alternativa, é possível optar por exibir o comando de migração resultante e executar o comando por conta própria. O arquivo JSON resultante pode ser armazenado localmente ou no Armazenamento de Blob do Azure.

:::image type="content" source="./media/import-data/jsontarget.png" alt-text="Captura de tela da opção de exportação de arquivo local JSON do Azure Cosmos DB":::

:::image type="content" source="./media/import-data/jsontarget2.png" alt-text="Captura de tela da opção de exportação de JSON do Armazenamento de Blobs do Azure no Azure Cosmos DB":::

Como opção, é possível escolher melhorar a aparência do JSON resultante. Essa ação aumentará o tamanho do documento resultante e deixará o conteúdo mais legível.

* Exportação JSON padrão

  ```JSON
  [{"id":"Sample","Title":"About Paris","Language":{"Name":"English"},"Author":{"Name":"Don","Location":{"City":"Paris","Country":"France"}},"Content":"Don's document in Azure Cosmos DB is a valid JSON document as defined by the JSON spec.","PageViews":10000,"Topics":[{"Title":"History of Paris"},{"Title":"Places to see in Paris"}]}]
  ```

* Exportação JSON com aparência melhor

  ```JSON
    [
     {
    "id": "Sample",
    "Title": "About Paris",
    "Language": {
      "Name": "English"
    },
    "Author": {
      "Name": "Don",
      "Location": {
        "City": "Paris",
        "Country": "France"
      }
    },
    "Content": "Don's document in Azure Cosmos DB is a valid JSON document as defined by the JSON spec.",
    "PageViews": 10000,
    "Topics": [
      {
        "Title": "History of Paris"
      },
      {
        "Title": "Places to see in Paris"
      }
    ]
    }]
  ```

Eis um exemplo de linha de comando para exportar o arquivo JSON para o Armazenamento de Blobs do Azure:

```console
dt.exe /ErrorDetails:All /s:DocumentDB /s.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB database_name>" /s.Collection:<CosmosDB collection_name>
/t:JsonFile /t.File:"blobs://<Storage account key>@<Storage account name>.blob.core.windows.net:443/<Container_name>/<Blob_name>"
/t.Overwrite
```

## <a name="advanced-configuration"></a>Configuração avançada

Na tela de Configuração avançada, especifique a localização do arquivo de log do qual você gostaria que os erros fossem gravados. As seguintes regras se aplicam a esta página:

1. Se não for fornecido um nome de arquivo, todos os erros são retornados na página Resultados.
2. Se for fornecido um nome de arquivo sem um diretório, o arquivo é criado (ou substituído) no diretório atual do ambiente.
3. Se você selecionar um arquivo existente, o arquivo é substituído; não há nenhuma opção de acréscimo.
4. Em seguida, escolha se deseja registrar, todas as mensagens de erro, nenhuma mensagem ou as mensagens críticas. Finalmente, decida com que frequência a mensagem de transferência na tela é atualizada com seu progresso.

   :::image type="content" source="./media/import-data/AdvancedConfiguration.png" alt-text="Captura da tela Configuração avançada":::

## <a name="confirm-import-settings-and-view-command-line"></a>Confirme as configurações de importação e de linha de comando de exibição

1. Depois de especificar as informações de origem, de destino e as configurações avançadas, analise o resumo da migração e exiba ou copie o comando de migração resultante se preferir. (Copiar o comando é útil para automatizar as operações de importação.)

    :::image type="content" source="./media/import-data/summary.png" alt-text="Captura de tela da tela Resumo.":::

    :::image type="content" source="./media/import-data/summarycommand.png" alt-text="Captura de tela da tela Resumo com a Visualização da Linha de Comando.":::

2. Quando estiver satisfeito com suas opções de origem e de destino, clique em **Importar**. O tempo decorrido, a contagem transferida e as informações de falha (se você não tiver fornecido um nome de arquivo na Configuração avançada) são atualizados enquanto a importação está em andamento. Uma vez concluída, você pode exportar os resultados (por exemplo, para lidar com as falhas de importação).

    :::image type="content" source="./media/import-data/viewresults.png" alt-text="Captura de tela das opções de exportação JSON do Azure Cosmos DB.":::

3. Também é possível iniciar uma nova importação por meio da redefinição de todos os valores ou mantendo as configurações existentes. (Por exemplo, você pode optar por manter as informações da cadeia de conexão, a escolha de origem e destino e muito mais.)

    :::image type="content" source="./media/import-data/newimport.png" alt-text="Captura de tela das opções de exportação JSON do Azure Cosmos DB com a caixa de diálogo de confirmação Nova Importação.":::

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você executou as seguintes tarefas:

> [!div class="checklist"]
> * Instalou a ferramenta de Migração de Dados
> * Importou dados de diferentes fontes de dados
> * Exportou do Azure Cosmos DB para o JSON

Agora, você pode seguir para o próximo tutorial e saber como consultar dados usando o Azure Cosmos DB.

> [!div class="nextstepaction"]
>[Como consultar os dados?](../cosmos-db/tutorial-query-sql-api.md)
