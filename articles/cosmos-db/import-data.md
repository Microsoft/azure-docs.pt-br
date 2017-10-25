---
title: "Ferramenta de migração de banco de dados do Azure Cosmos DB | Microsoft Docs"
description: "Saiba como usar as ferramentas de migração de dados de software livre do Azure Cosmos DB para importar dados para o Azure Cosmos DB de várias fontes, incluindo MongoDB, SQL Server, Armazenamento de tabelas, Amazon DynamoDB, CSV e arquivos JSON. Conversão de CSV para JSON."
keywords: "csv em json, ferramentas de migração de banco de dados, converter csv em json"
services: cosmos-db
author: andrewhoh
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: d173581d-782a-445c-98d9-5e3c49b00e25
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/06/2017
ms.author: anhoh
ms.custom: mvc
ms.openlocfilehash: a60c47814da2660f17456f5e662f420adbb9158e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-import-data-into-azure-cosmos-db-with-the-documentdb-api"></a>Como importar dados para o Azure Cosmos DB com a API do DocumentDB

Este tutorial fornece instruções sobre como usar a ferramenta BD Cosmos do Azure: Migração de Dados da API DocumentDB, que pode importar dados de várias fontes, incluindo arquivos JSON, arquivos CSV, SQL, MongoDB, Armazenamento de tabelas do Azure, Amazon DynamoDB e coleções da API DocumentDB do BD Cosmos do Azure para usar com o BD Cosmos do Azure e a API DocumentDB. A ferramenta de Migração de Dados também pode ser usada ao migrar de uma coleção de partição única para uma coleção de várias partições na API do DocumentDB.

A ferramenta de Migração de Dados só funciona durante a importação de dados para o Azure Cosmos DB para uso com a API do DocumentDB. No momento, não há suporte para a importação de dados para uso com a API de Tabela ou a API do Graph. 

Para importar dados para uso com a API do MongoDB, consulte [Azure Cosmos DB: Como migrar dados para a API do MongoDB?](mongodb-migrate.md).

Este tutorial cobre as seguintes tarefas:

> [!div class="checklist"]
> * Instalação da ferramenta de Migração de Dados
> * Importação de dados de diferentes fontes de dados
> * Exportação do Azure Cosmos DB para o JSON

## <a id="Prerequisites"></a>Pré-requisitos
Antes de seguir as instruções deste artigo, verifique se você tem os seguintes itens instalados:

* [Microsoft .NET Framework 4.51](https://www.microsoft.com/download/developer-tools.aspx) ou superior.

## <a id="Overviewl"></a>Visão geral da ferramenta de Migração de Dados
A ferramenta de Migração de Dados é uma solução de software livre que importa dados para o Azure Cosmos DB de uma variedade de fontes, incluindo:

* Arquivos JSON
* MongoDB
* SQL Server
* Arquivos CSV
* Armazenamento da tabela do Azure
* Amazon DynamoDB
* HBase
* Coleções do Azure Cosmos DB

Embora a ferramenta de importação inclua uma interface gráfica do usuário (dtui.exe), ela também pode ser controlada pela linha de comando (dt.exe). Na verdade, há uma opção de extrair o comando associado depois de configurar uma importação por meio da interface do usuário. Dados de origem em tabela (por exemplo, arquivos do SQL Server ou CSV) podem ser transformados, de forma que relações hierárquicas (subdocumentos) podem ser criadas durante a importação. Continue lendo para saber mais sobre as opções de origem, linhas de comando de exemplo para importar de cada origem, opções de destino e resultados de importação de visualização.

## <a id="Install"></a>Instalar a ferramenta de Migração de Dados
O código-fonte da ferramenta de migração está disponível no GitHub [nesse repositório](https://github.com/azure/azure-documentdb-datamigrationtool) e há uma versão compilada no [Centro de Download da Microsoft](http://www.microsoft.com/downloads/details.aspx?FamilyID=cda7703a-2774-4c07-adcc-ad02ddc1a44d). Você pode compilar a solução ou simplesmente baixar e extrair a versão compilada em um diretório de sua escolha. Em seguida, execute um:

* **Dtui.exe**: Versão da interface gráfica da ferramenta
* **Dt.exe**: Versão de linha de comando da ferramenta

## <a name="import-data"></a>Importar dados

Depois de instalar a ferramenta, é hora de importar os dados. Que tipo de dados você deseja importar?

* [Arquivos JSON](#JSON)
* [MongoDB](#MongoDB)
* [Arquivos de exportação do MongoDB](#MongoDBExport)
* [SQL Server](#SQL)
* [Arquivos CSV](#CSV)
* [Armazenamento de Tabelas do Azure](#AzureTableSource)
* [Amazon DynamoDB](#DynamoDBSource)
* [Blob](#BlobImport)
* [Coleções do Azure Cosmos DB](#DocumentDBSource)
* [HBase](#HBaseSource)
* [Importação em massa do Azure Cosmos DB](#DocumentDBBulkImport)
* [Importação de registro sequencial do Azure Cosmos DB](#DocumentDSeqTarget)


## <a id="JSON"></a>Para importar arquivos JSON
A opção de importador de origem de arquivo JSON permite importar um ou mais arquivos JSON de documento único ou arquivos JSON que contêm uma matriz de documentos JSON. Ao adicionar pastas que contêm arquivos JSON para importar, você tem a opção de pesquisar recursivamente arquivos em subpastas.

![Captura de tela das opções de origem do arquivo JSON — ferramentas de migração de banco de dados](./media/import-data/jsonsource.png)

Aqui estão alguns exemplos de linha de comando para importar os arquivos JSON:

    #Import a single JSON file
    dt.exe /s:JsonFile /s.Files:.\Sessions.json /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Sessions /t.CollectionThroughput:2500

    #Import a directory of JSON files
    dt.exe /s:JsonFile /s.Files:C:\TESessions\*.json /t:CosmosDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Sessions /t.CollectionThroughput:2500

    #Import a directory (including sub-directories) of JSON files
    dt.exe /s:JsonFile /s.Files:C:\LastFMMusic\**\*.json /t:CosmosDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Music /t.CollectionThroughput:2500

    #Import a directory (single), directory (recursive), and individual JSON files
    dt.exe /s:JsonFile /s.Files:C:\Tweets\*.*;C:\LargeDocs\**\*.*;C:\TESessions\Session48172.json;C:\TESessions\Session48173.json;C:\TESessions\Session48174.json;C:\TESessions\Session48175.json;C:\TESessions\Session48177.json /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:subs /t.CollectionThroughput:2500

    #Import a single JSON file and partition the data across 4 collections
    dt.exe /s:JsonFile /s.Files:D:\\CompanyData\\Companies.json /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:comp[1-4] /t.PartitionKey:name /t.CollectionThroughput:2500

## <a id="MongoDB"></a>Para importar do MongoDB

> [!IMPORTANT]
> Se você estiver importando para uma conta do Azure Cosmos DB com suporte para o MongoDB, siga estas [instruções](mongodb-migrate.md).
> 
> 

A opção de importador de origem do MongoDB permite importar de uma coleção do MongoDB individual e opcionalmente filtrar documentos usando uma consulta e/ou modificar a estrutura do documento usando uma projeção.  

![Captura de tela das opções de fonte do MongoDB](./media/import-data/mongodbsource.png)

A cadeia de conexão está no formato padrão do MongoDB:

    mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database>

> [!NOTE]
> Use o comando Verify para garantir que a instância do MongoDB especificada no campo de cadeia de conexão pode ser acessada.
> 
> 

Digite o nome da coleção por meio da qual os dados serão importados. Você pode opcionalmente especificar ou fornecer um arquivo para uma consulta (por exemplo, {pop: {$gt: 5000}}) e/ou uma projeção (por exemplo, {loc:0}) para filtrar e moldar os dados a serem importados.

Aqui estão alguns exemplos de linha de comando para importar por meio do MongoDB:

    #Import all documents from a MongoDB collection
    dt.exe /s:MongoDB /s.ConnectionString:mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database> /s.Collection:zips /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:BulkZips /t.IdField:_id /t.CollectionThroughput:2500

    #Import documents from a MongoDB collection which match the query and exclude the loc field
    dt.exe /s:MongoDB /s.ConnectionString:mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database> /s.Collection:zips /s.Query:{pop:{$gt:50000}} /s.Projection:{loc:0} /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:BulkZipsTransform /t.IdField:_id/t.CollectionThroughput:2500

## <a id="MongoDBExport"></a>Para importar arquivos de exportação do MongoDB

> [!IMPORTANT]
> Se você estiver importando para uma conta do Azure Cosmos DB com suporte para o MongoDB, siga estas [instruções](mongodb-migrate.md).
> 
> 

A opção do importador de origem de arquivo JSON de exportação do MongoDB permite que você importe um ou mais arquivos JSON produzidos por meio do utilitário mongoexport.  

![Captura de tela das opções de fonte de exportação do MongoDB](./media/import-data/mongodbexportsource.png)

Ao adicionar pastas que contêm arquivos JSON de exportação do MongoDB, você tem a opção de pesquisar recursivamente arquivos em subpastas.

Aqui está um exemplo de linha de comando para importar de arquivos de JSON de exportação do MongoDB:

    dt.exe /s:MongoDBExport /s.Files:D:\mongoemployees.json /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:employees /t.IdField:_id /t.Dates:Epoch /t.CollectionThroughput:2500

## <a id="SQL"></a>Para importar do SQL Server
A opção do importador de origem do SQL permite importar de um banco de dados do SQL Server individual e, opcionalmente, filtrar os registros a serem importados usando uma consulta. Além disso, você pode modificar a estrutura do documento, especificando um separador de aninhamento (falaremos mais sobre isso em instantes).  

![Captura de tela das opções de origem do SQL — ferramentas de migração de banco de dados](./media/import-data/sqlexportsource.png)

O formato da cadeia de conexão é o formato da cadeia de conexão SQL padrão.

> [!NOTE]
> Use o comando Verify para garantir que a instância do SQL Server especificada no campo Cadeia de conexão pode ser acessada.
> 
> 

A propriedade de separador de aninhamento é usada para criar relacionamentos hierárquicos (sub-documentos) durante a importação. Considere a seguinte consulta SQL:

*Selecione CAST (BusinessEntityID como varchar) como ID, Nome, AddressType como [Address.AddressType], AddressLine1 como [Address.AddressLine1], City como [Address.Location.City], StateProvinceName como [Address.Location.StateProvinceName], PostalCode como [Address.PostalCode], CountryRegionName como [Address.CountryRegionName] de Sales.vStoreWithAddresses WHERE AddressType='Main Office'*

Que retorna os seguintes resultados (parciais):

![Captura de tela dos resultados de consulta do SQL](./media/import-data/sqlqueryresults.png)

Observe os aliases como Address.AddressType e Address.Location.StateProvinceName. Especificando um separador de aninhamento de “.”, a ferramenta de importação cria os sub-documentos Address e Address.Location durante a importação. Este é um exemplo de um documento resultante no Azure Cosmos DB:

*{ "id": "956", "Name": "Finer Sales and Service", "Address": { "AddressType": "Main Office", "AddressLine1": "#500-75 O'Connor Street", "Location": { "City": "Ottawa", "StateProvinceName": "Ontario" }, "PostalCode": "K4B 1S2", "CountryRegionName": "Canada" } }*

Aqui estão alguns exemplos de linha de comando para importar do SQL Server:

    #Import records from SQL which match a query
    dt.exe /s:SQL /s.ConnectionString:"Data Source=<server>;Initial Catalog=AdventureWorks;User Id=advworks;Password=<password>;" /s.Query:"select CAST(BusinessEntityID AS varchar) as Id, * from Sales.vStoreWithAddresses WHERE AddressType='Main Office'" /t:CosmosDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Stores /t.IdField:Id /t.CollectionThroughput:2500

    #Import records from sql which match a query and create hierarchical relationships
    dt.exe /s:SQL /s.ConnectionString:"Data Source=<server>;Initial Catalog=AdventureWorks;User Id=advworks;Password=<password>;" /s.Query:"select CAST(BusinessEntityID AS varchar) as Id, Name, AddressType as [Address.AddressType], AddressLine1 as [Address.AddressLine1], City as [Address.Location.City], StateProvinceName as [Address.Location.StateProvinceName], PostalCode as [Address.PostalCode], CountryRegionName as [Address.CountryRegionName] from Sales.vStoreWithAddresses WHERE AddressType='Main Office'" /s.NestingSeparator:. /t:CosmosDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:StoresSub /t.IdField:Id /t.CollectionThroughput:2500

## <a id="CSV"></a>Para importar arquivos CSV e converter CSV em JSON
A opção de importador de origem de arquivo CSV permite que você importe um ou mais arquivos CSV. Ao adicionar pastas que contêm arquivos CSV para importar, você tem a opção de pesquisar recursivamente arquivos em subpastas.

![Captura de tela das opções de origem do CSV — CVS em JSON](media/import-data/csvsource.png)

De forma semelhante à origem de SQL, a propriedade de separador de aninhamento pode ser usada para criar relacionamentos hierárquicos (sub-documentos) durante a importação. Considere a seguinte linha de cabeçalho CSV e linhas de dados:

![Captura de tela dos registros de exemplo do CSV — CVS em JSON](./media/import-data/csvsample.png)

Observe os aliases como DomainInfo.Domain_Name e RedirectInfo.Redirecting. Especificando um separador de aninhamento de “.”, a ferramenta de importação cria os sub-documentos DomainInfo e RedirectInfo durante a importação. Este é um exemplo de um documento resultante no Azure Cosmos DB:

*{ "DomainInfo": { "Domain_Name": "ACUS.GOV", "Domain_Name_Address": "http://www.ACUS.GOV" }, "Federal Agency": "Administrative Conference of the United States", "RedirectInfo": { "Redirecting": "0", "Redirect_Destination": "" }, "id": "9cc565c5-ebcd-1c03-ebd3-cc3e2ecd814d" }*

A ferramenta de importação tentará deduzir informações de tipo para valores sem aspas em arquivos CSV (valores entre aspas são tratados sempre como cadeias de caracteres).  Os tipos são identificados na seguinte ordem: número, datetime, booliano.  

Há duas outras coisas a observar sobre a importação de CSV:

1. Por padrão, os valores sem aspas sempre são cortados para tabulações e espaços, enquanto os valores entre aspas são preservados como estão. Esse comportamento pode ser substituído por a caixa de seleção com valores entre aspas de corte ou pela opção de linha de comando /s.TrimQuoted.
2. Por padrão, um nulo sem aspas é tratado como um valor nulo. Esse comportamento pode ser substituído (isto é, trate um nulo sem aspas como uma cadeia de caracteres "null") com o NULL de tratamento sem aspas como caixa de seleção da cadeia de caracteres ou a opção de linha de comando /s.NoUnquotedNulls.

Aqui está um exemplo de linha de comando para importação de CSV:

    dt.exe /s:CsvFile /s.Files:.\Employees.csv /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Employees /t.IdField:EntityID /t.CollectionThroughput:2500

## <a id="AzureTableSource"></a>Para importar do Armazenamento de tabelas do Azure
A opção de importador de origem de armazenamento de tabela do Azure permite importar de uma tabela de armazenamento de uma tabela individual do Azure e filtrar opcionalmente as entidades da tabela a serem importadas. Observe que não é possível usar a ferramenta de Migração de Dados para importar dados do Armazenamento de tabelas do Azure para o Azure Cosmos DB para uso com a API de Tabela. No momento, há suporte apenas para a importação do Azure Cosmos DB para uso com a API do DocumentDB.

![Captura de tela das opções de origem de armazenamento da tabela do Azure](./media/import-data/azuretablesource.png)

O formato da cadeia de conexão de armazenamento de tabela do Azure é:

    DefaultEndpointsProtocol=<protocol>;AccountName=<Account Name>;AccountKey=<Account Key>;

> [!NOTE]
> Use o comando Verify para garantir que a instância de armazenamento da tabela do Azure especificada no campo de cadeia de conexão pode ser acessada.
> 
> 

Digite o nome da tabela do Azure por meio da qual os dados serão importados. Opcionalmente, você pode especificar um [filtro](https://msdn.microsoft.com/library/azure/ff683669.aspx).

A opção de importador de origem de armazenamento de tabela do Azure tem as seguintes opções adicionais:

1. Incluir campos internos
   1. Todos - incluir todos os campos internos (PartitionKey, RowKey e Timestamp)
   2. Nenhum - excluir todos os campos internos
   3. RowKey - incluir somente o campo RowKey
2. Selecionar colunas
   1. Filtros de armazenamento de tabela do Azure não dão suporte a projeções. Se você quiser importar somente propriedades específicas de entidade da tabela do Azure, adicione-as à lista Selecionar colunas. Todas as outras propriedades da entidade serão ignoradas.

Aqui está um exemplo de linha de comando para importar por meio do armazenamento de tabela do Azure:

    dt.exe /s:AzureTable /s.ConnectionString:"DefaultEndpointsProtocol=https;AccountName=<Account Name>;AccountKey=<Account Key>" /s.Table:metrics /s.InternalFields:All /s.Filter:"PartitionKey eq 'Partition1' and RowKey gt '00001'" /s.Projection:ObjectCount;ObjectSize  /t:CosmosDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:metrics /t.CollectionThroughput:2500

## <a id="DynamoDBSource"></a>Para importar do Amazon DynamoDB
A opção de importação de fonte do Amazon DynamoDB permite importar de uma tabela individual do Amazon DynamoDB e, opcionalmente, filtrar as entidades a serem importadas. Vários modelos são fornecidos para que a configuração de uma importação seja tão fácil quanto possível.

![Captura de tela das opções de origem do Amazon DynamoDB — ferramentas de migração de banco de dados](./media/import-data/dynamodbsource1.png)

![Captura de tela das opções de origem do Amazon DynamoDB — ferramentas de migração de banco de dados](./media/import-data/dynamodbsource2.png)

O formato da cadeia de conexão do Amazon DynamoDB é:

    ServiceURL=<Service Address>;AccessKey=<Access Key>;SecretKey=<Secret Key>;

> [!NOTE]
> Use o comando Verify para garantir que a instância do Amazon DynamoDB especificada no campo de cadeia de conexão possa ser acessada.
> 
> 

Aqui está um exemplo de linha de comando para importar do Amazon DynamoDB:

    dt.exe /s:DynamoDB /s.ConnectionString:ServiceURL=https://dynamodb.us-east-1.amazonaws.com;AccessKey=<accessKey>;SecretKey=<secretKey> /s.Request:"{   """TableName""": """ProductCatalog""" }" /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<Azure Cosmos DB Endpoint>;AccountKey=<Azure Cosmos DB Key>;Database=<Azure Cosmos DB Database>;" /t.Collection:catalogCollection /t.CollectionThroughput:2500

## <a id="BlobImport"></a>Para importar arquivos do Armazenamento de blobs do Azure
O arquivo JSON, arquivo de exportação do MongoDB e opções de importador de origem do arquivo CSV permitem que você importe um ou mais arquivos de Armazenamento de Blob do Azure. Depois de especificar uma URL do contêiner de Blob e a chave de conta, basta fornece uma expressão regular para selecionar os arquivos a serem importados.

![Captura de tela das opções de origem de arquivo de blob](./media/import-data/blobsource.png)

Eis um exemplo de linha de comando para importar arquivos JSON do Armazenamento de Blob do Azure:

    dt.exe /s:JsonFile /s.Files:"blobs://<account key>@account.blob.core.windows.net:443/importcontainer/.*" /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:doctest

## <a id="DocumentDBSource"></a>Para importar de uma coleção da API DocumentDB do BC Cosmos do Azure
A opção de importador de origem do Azure Cosmos DB permite importar dados de uma ou mais coleções do Azure Cosmos DB e, opcionalmente, filtrar documentos usando uma consulta.  

![Captura de tela das opções de fonte do Azure Cosmos DB](./media/import-data/documentdbsource.png)

O formato da cadeia de conexão do Azure Cosmos DB é:

    AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;

A cadeia de conexão da conta do Azure Cosmos DB pode ser recuperada na folha Chaves do portal do Azure, conforme descrito em [Como gerenciar uma conta do Azure Cosmos DB](manage-account.md); no entanto, o nome do banco de dados deve ser acrescentado à cadeia de conexão no seguinte formato:

    Database=<CosmosDB Database>;

> [!NOTE]
> Use o comando Verify para garantir que a instância do Azure Cosmos DB especificada no campo de cadeia de conexão pode ser acessada.
> 
> 

Para importar de uma única coleção do Azure Cosmos DB, insira o nome da coleção da qual os dados serão importados. Para importar de várias coleções do Azure Cosmos DB, forneça uma expressão regular para corresponder um ou mais nomes de coleção (por exemplo, collection01 | collection02 | collection03). Você pode opcionalmente especificar ou fornecer um arquivo para uma consulta para filtrar e moldar os dados a serem importados.

> [!NOTE]
> Uma vez que o campo de coleção aceita expressões regulares, se você estiver importando de uma única coleção cujo nome contém caracteres de expressão regular, esses caracteres devem ser substituídos adequadamente.
> 
> 

A opção de importador de origem do Azure Cosmos DB tem as seguintes opções avançadas:

1. Incluir Campos Internos: especifica se as propriedades do sistema de documentos do Azure Cosmos DB devem ser incluídas na exportação (por exemplo, _rid, _ts).
2. Número de Repetições em Caso de Falha: especifica o número de vezes para tentar a conexão novamente com o Azure Cosmos DB em caso de falhas transitórias (por exemplo, interrupção da conectividade de rede).
3. Intervalo de Repetição: especifica o tempo de espera para tentar a conexão novamente com o Azure Cosmos DB em caso de falhas transitórias (por exemplo, interrupção da conectividade de rede).
4. Modo de Conexão: especifica o modo de conexão a ser usado com o Azure Cosmos DB. As opções disponíveis são DirectTcp, DirectHttps e Gateway. Os modos de conexão direta são mais rápidos, enquanto que o modo de gateway é mais amigável ao firewall, uma vez que só usa a porta 443.

![Captura de tela das opções avançadas de fonte do Azure Cosmos DB](./media/import-data/documentdbsourceoptions.png)

> [!TIP]
> A ferramenta de importação usa como padrão o modo de conexão DirectTcp. Se você enfrentar problemas de firewall, alterne para o modo de conexão Gateway, uma vez que ele só requer a porta 443.
> 
> 

Estas são algumas amostras de linha de comando para importar do Azure Cosmos DB:

    #Migrate data from one Azure Cosmos DB collection to another Azure Cosmos DB collections
    dt.exe /s:CosmosDB /s.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /s.Collection:TEColl /t:CosmosDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:TESessions /t.CollectionThroughput:2500

    #Migrate data from multiple Azure Cosmos DB collections to a single Azure Cosmos DB collection
    dt.exe /s:CosmosDB /s.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /s.Collection:comp1|comp2|comp3|comp4 /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:singleCollection /t.CollectionThroughput:2500

    #Export an Azure Cosmos DB collection to a JSON file
    dt.exe /s:CosmosDB /s.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /s.Collection:StoresSub /t:JsonFile /t.File:StoresExport.json /t.Overwrite /t.CollectionThroughput:2500

> [!TIP]
> A ferramenta de Importação de Dados do Azure Cosmos DB também dá suporte à importação de dados do [Emulador do Azure Cosmos DB](local-emulator.md). Ao importar dados de um emulador local, defina o ponto de extremidade como `https://localhost:<port>`. 
> 
> 

## <a id="HBaseSource"></a>Para importar do HBase
A opção de importador de origem do HBase permite importar dados de uma tabela do HBase e, opcionalmente, filtrar os dados. Vários modelos são fornecidos para que a configuração de uma importação seja tão fácil quanto possível.

![Captura de tela das opções de origem do HBase](./media/import-data/hbasesource1.png)

![Captura de tela das opções de origem do HBase](./media/import-data/hbasesource2.png)

O formato da cadeia de conexão HBase Stargate é:

    ServiceURL=<server-address>;Username=<username>;Password=<password>

> [!NOTE]
> Use o comando Verify para garantir que a instância do HBase especificada no campo de cadeia de conexão possa ser acessada.
> 
> 

Aqui está um exemplo de linha de comando para importar do HBase:

    dt.exe /s:HBase /s.ConnectionString:ServiceURL=<server-address>;Username=<username>;Password=<password> /s.Table:Contacts /t:CosmosDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:hbaseimport

## <a id="DocumentDBBulkTarget"></a>Para importar para a API DocumentDB (importação em massa)
O importador em Massa do Azure Cosmos DB permite importar de qualquer uma das opções de origem disponíveis, usando um procedimento armazenado do Azure Cosmos DB para maior eficiência. A ferramenta dá suporte à importação para uma coleção de partição única do Azure Cosmos DB, bem como à importação fragmentada, por meio da qual os dados são particionados em várias coleções de partição única do Azure Cosmos DB. Para obter mais informações sobre o particionamento de dados, consulte [Particionamento e escala no Azure Cosmos DB](partition-data.md). A ferramenta vai criar, executar e, em seguida, excluir o procedimento armazenado da(s) coleção(ões) de destino.  

![Captura de tela das opções em massa do Azure Cosmos DB](./media/import-data/documentdbbulk.png)

O formato da cadeia de conexão do Azure Cosmos DB é:

    AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;

A cadeia de conexão da conta do Azure Cosmos DB pode ser recuperada na folha Chaves do portal do Azure, conforme descrito em [Como gerenciar uma conta do Azure Cosmos DB](manage-account.md); no entanto, o nome do banco de dados deve ser acrescentado à cadeia de conexão no seguinte formato:

    Database=<CosmosDB Database>;

> [!NOTE]
> Use o comando Verify para garantir que a instância do Azure Cosmos DB especificada no campo de cadeia de conexão pode ser acessada.
> 
> 

Para importar para uma única coleção, digite o nome da coleção à qual os dados serão importados e clique no botão Adicionar. Para importar para várias coleções, insira o nome de cada coleção individualmente ou use a seguinte sintaxe para especificar várias coleções: *collection_prefix*[start index - end index]. Ao especificar várias coleções por meio da sintaxe mencionada anteriormente, lembre-se do seguinte:

1. Somente padrões de nome de intervalo inteiro têm suporte. Por exemplo, a especificação de coleção [0-3] produzirá as seguintes coleções: collection0, collection1, collection2, collection3.
2. Você pode usar uma sintaxe abreviada: collection[3] emitirá o mesmo conjunto de coleções mencionado na etapa 1.
3. Mais de uma substituição pode ser fornecida. Por exemplo, a coleção [0-1] [0-9] gerará 20 nomes de coleção com zeros à esquerda (collection01... 02,... 03).

Depois de especificar o(s) nome(s) de coleção, escolha a taxa de transferência desejada da(s) coleção(ões) (de 400 a 10.000 RUs). Para uma importação com melhor desempenho, escolha uma taxa de transferência maior. Para obter mais informações sobre níveis de desempenho, consulte [Níveis de desempenho no Azure Cosmos DB](performance-levels.md).

> [!NOTE]
> A configuração de taxa de transferência de desempenho só se aplica à criação da coleção. Se a coleção especificada já existir, sua taxa de transferência não será modificada.
> 
> 

Ao importar para várias coleções, a ferramenta de importação dá suporte a hash baseado em fragmentação. Neste cenário, especifique a propriedade do documento que deseja usar como a Chave de partição (se a Chave de partição for deixada em branco, os documentos serão fragmentados aleatoriamente em coleções de destino).

Opcionalmente, você pode especificar qual campo na origem de importação deve ser usado como a propriedade de ID do documento do Azure Cosmos DB durante a importação (observe que se os documentos não contiverem essa propriedade, a ferramenta de importação gerará um GUID como o valor da propriedade de ID).

Há uma série de opções avançadas disponíveis durante a importação. Em primeiro lugar, embora a ferramenta inclua um procedimento armazenado de importação em massa padrão (BulkInsert.js), você pode optar por especificar seu próprio procedimento armazenado de importação:

 ![Captura de tela da opção de sproc de inserção em massa do Azure Cosmos DB](./media/import-data/bulkinsertsp.png)

Adicionalmente, ao importar tipos de dados (por exemplo, do SQL Server ou do MongoDB), você pode escolher entre três opções de importação:

 ![Captura de tela das opções de importação de data e hora do Azure Cosmos DB](./media/import-data/datetimeoptions.png)

* Cadeia de caracteres: Persistir como um valor de cadeia de caracteres
* Época: Persistir como um valor de número de época
* Ambos: Persistir com os valores de número de cadeia de caracteres e de época. Essa opção criará um sub-documento, por exemplo: "date_joined": {"Value": "2013-10-21T21:17:25.2410000Z", "Epoch": 1382390245}

O importador em Massa do Azure Cosmos DB tem as seguintes opções avançadas adicionais:

1. Tamanho do lote: A ferramenta usa como padrão um tamanho de lote de 50.  Se os documentos a serem importados forem grandes, considere reduzir o tamanho do lote. Da mesma forma, se os documentos a serem importados forem pequenos, considere aumentar o tamanho do lote.
2. Tamanho máximo de script (bytes): a ferramenta usa como padrão um tamanho máximo de script de 512KB
3. Desabilitar a geração automática de ID: Se todos os documentos a serem importados contiverem um campo de identificação, selecionar essa opção pode aumentar o desempenho. Documentos com um campo de ID exclusiva ausente não serão importados.
4. Atualizar documentos existentes: a ferramenta por padrão não substitui os documentos existentes com conflitos de ID. Esta opção permitirá a substituir documentos existentes por ids correspondentes. Esse recurso é útil para migrações de dados agendadas que atualizam documentos existentes.
5. Número de Repetições em Caso de Falha: especifica o número de vezes para tentar a conexão novamente com o Azure Cosmos DB em caso de falhas transitórias (por exemplo, interrupção da conectividade de rede).
6. Intervalo de Repetição: especifica o tempo de espera para tentar a conexão novamente com o Azure Cosmos DB em caso de falhas transitórias (por exemplo, interrupção da conectividade de rede).
7. Modo de Conexão: especifica o modo de conexão a ser usado com o Azure Cosmos DB. As opções disponíveis são DirectTcp, DirectHttps e Gateway. Os modos de conexão direta são mais rápidos, enquanto que o modo de gateway é mais amigável ao firewall, uma vez que só usa a porta 443.

![Captura de tela das opções avançadas de importação em massa do Azure Cosmos DB](./media/import-data/docdbbulkoptions.png)

> [!TIP]
> A ferramenta de importação usa como padrão o modo de conexão DirectTcp. Se você enfrentar problemas de firewall, alterne para o modo de conexão Gateway, uma vez que ele só requer a porta 443.
> 
> 

## <a id="DocumentDBSeqTarget"></a>Para importar para a API DocumentDB (Importação de Registro Sequencial)
O importador de registro sequencial do Azure Cosmos DB permite que você importe de qualquer uma das opções de origem disponíveis com base em cada registro. Você pode escolher esta opção se estiver importando para uma coleção existente que já atingiu a cota de procedimentos armazenados. A ferramenta dá suporte à importação para uma coleção (com partição única e com várias partições) do Azure Cosmos DB, bem como à importação fragmentada, por meio da qual os dados são particionados em várias coleções de partição única e/ou com várias partições do Azure Cosmos DB. Para obter mais informações sobre o particionamento de dados, consulte [Particionamento e escala no Azure Cosmos DB](partition-data.md).

![Captura de tela das opções de importação de registro sequencial do Azure Cosmos DB](./media/import-data/documentdbsequential.png)

O formato da cadeia de conexão do Azure Cosmos DB é:

    AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;

A cadeia de conexão da conta do Azure Cosmos DB pode ser recuperada na folha Chaves do portal do Azure, conforme descrito em [Como gerenciar uma conta do Azure Cosmos DB](manage-account.md); no entanto, o nome do banco de dados deve ser acrescentado à cadeia de conexão no seguinte formato:

    Database=<Azure Cosmos DB Database>;

> [!NOTE]
> Use o comando Verify para garantir que a instância do Azure Cosmos DB especificada no campo de cadeia de conexão pode ser acessada.
> 
> 

Para importar para uma única coleção, digite o nome da coleção à qual os dados serão importados e clique no botão Adicionar. Para importar para várias coleções, insira o nome de cada coleção individualmente ou use a seguinte sintaxe para especificar várias coleções: *collection_prefix*[start index - end index]. Ao especificar várias coleções por meio da sintaxe mencionada anteriormente, lembre-se do seguinte:

1. Somente padrões de nome de intervalo inteiro têm suporte. Por exemplo, a especificação de coleção [0-3] produzirá as seguintes coleções: collection0, collection1, collection2, collection3.
2. Você pode usar uma sintaxe abreviada: collection[3] emitirá o mesmo conjunto de coleções mencionado na etapa 1.
3. Mais de uma substituição pode ser fornecida. Por exemplo, a coleção [0-1] [0-9] gerará 20 nomes de coleção com zeros à esquerda (collection01... 02,... 03).

Depois de especificar o(s) nome(s) de coleção, escolha a taxa de transferência desejada da(s) coleção(ões) (de 400 a 250.000 RUs). Para uma importação com melhor desempenho, escolha uma taxa de transferência maior. Para obter mais informações sobre níveis de desempenho, consulte [Níveis de desempenho no Azure Cosmos DB](performance-levels.md). Qualquer importação para coleções com taxa de transferência acima de 10.000 RUs exigirá uma chave de partição. Se optar por ter mais de 250.000 RUs, você precisará fazer uma solicitação no portal para aumento da conta.

> [!NOTE]
> A configuração de taxa de transferência só se aplica à criação da coleção. Se a coleção especificada já existir, sua taxa de transferência não será modificada.
> 
> 

Ao importar para várias coleções, a ferramenta de importação dá suporte a hash baseado em fragmentação. Neste cenário, especifique a propriedade do documento que deseja usar como a Chave de partição (se a Chave de partição for deixada em branco, os documentos serão fragmentados aleatoriamente em coleções de destino).

Opcionalmente, você pode especificar qual campo na origem de importação deve ser usado como a propriedade de ID do documento do Azure Cosmos DB durante a importação (observe que se os documentos não contiverem essa propriedade, a ferramenta de importação gerará um GUID como o valor da propriedade de ID).

Há uma série de opções avançadas disponíveis durante a importação. Primeiro, ao importar tipos de dados (por exemplo, do SQL Server ou do MongoDB), você pode escolher entre três opções de importação:

 ![Captura de tela das opções de importação de data e hora do Azure Cosmos DB](./media/import-data/datetimeoptions.png)

* Cadeia de caracteres: Persistir como um valor de cadeia de caracteres
* Época: Persistir como um valor de número de época
* Ambos: Persistir com os valores de número de cadeia de caracteres e de época. Essa opção criará um sub-documento, por exemplo: "date_joined": {"Value": "2013-10-21T21:17:25.2410000Z", "Epoch": 1382390245}

O importador de Registro sequencial do Azure Cosmos DB tem as seguintes opções avançadas adicionais:

1. Número de solicitações paralelas: A ferramenta usa como padrão 2 solicitações paralelas. Se os documentos a serem importados forem pequenos, considere aumentar o número de solicitações paralelas. Observe que se esse número for muito elevado, a importação poderá sofrer limitação.
2. Desabilitar a geração automática de ID: Se todos os documentos a serem importados contiverem um campo de identificação, selecionar essa opção pode aumentar o desempenho. Documentos com um campo de ID exclusiva ausente não serão importados.
3. Atualizar documentos existentes: a ferramenta por padrão não substitui os documentos existentes com conflitos de ID. Esta opção permitirá a substituir documentos existentes por ids correspondentes. Esse recurso é útil para migrações de dados agendadas que atualizam documentos existentes.
4. Número de Repetições em Caso de Falha: especifica o número de vezes para tentar a conexão novamente com o Azure Cosmos DB em caso de falhas transitórias (por exemplo, interrupção da conectividade de rede).
5. Intervalo de Repetição: especifica o tempo de espera para tentar a conexão novamente com o Azure Cosmos DB em caso de falhas transitórias (por exemplo, interrupção da conectividade de rede).
6. Modo de Conexão: especifica o modo de conexão a ser usado com o Azure Cosmos DB. As opções disponíveis são DirectTcp, DirectHttps e Gateway. Os modos de conexão direta são mais rápidos, enquanto que o modo de gateway é mais amigável ao firewall, uma vez que só usa a porta 443.

![Captura de tela das opções avançadas de importação de registro sequencial do Azure Cosmos DB](./media/import-data/documentdbsequentialoptions.png)

> [!TIP]
> A ferramenta de importação usa como padrão o modo de conexão DirectTcp. Se você enfrentar problemas de firewall, alterne para o modo de conexão Gateway, uma vez que ele só requer a porta 443.
> 
> 

## <a id="IndexingPolicy"></a>Especificar uma política de indexação ao criar coleções do Azure Cosmos DB
Quando você permite que a ferramenta de migração crie coleções durante a importação, pode especificar a política de indexação das coleções. Na seção de opções avançadas das opções de Importação em massa e Registro sequencial do Azure Cosmos DB, navegue para a seção Política de indexação.

![Captura de tela das opções avançadas de Política de indexação do Azure Cosmos DB](./media/import-data/indexingpolicy1.png)

Usando a opção avançada de política de indexação, você pode selecionar um arquivo de política de indexação, inserir manualmente uma política de indexação ou selecionar a partir um conjunto de modelos padrão (clicando com o botão direito na caixa de texto da política indexação).

Os modelos de política que a ferramenta fornece são:

* Padrão. Essa política é mais útil quando você está executando consultas de igualdade em cadeias de caracteres e usando as consultas ORDER BY, intervalo e igualdade para números. Essa política tem uma sobrecarga de armazenamento de índice menor que Intervalo.
* Intervalo. Essa política é mais útil quando você está usando consultas ORDER BY, intervalo e igualdade em números e cadeias de caracteres. Essa política tem uma sobrecarga de armazenamento de índice maior do que Padrão ou Hash.

![Captura de tela das opções avançadas de Política de indexação do Azure Cosmos DB](./media/import-data/indexingpolicy2.png)

> [!NOTE]
> Se você não especificar uma política de indexação, a política padrão será aplicada. Para obter mais informações sobre políticas de indexação, consulte [Políticas de indexação do Azure Cosmos DB](indexing-policies.md).
> 
> 

## <a name="export-to-json-file"></a>Exportar para arquivo JSON
O exportador de JSON do Azure Cosmos DB permite exportar uma das opções de origem disponíveis para um arquivo JSON que contém uma matriz de documentos JSON. A ferramenta manipulará a exportação por você, ou você pode optar por exibir o comando de migração resultante e executar o comando por conta própria. O arquivo JSON resultante pode ser armazenado localmente ou no Armazenamento de Blob do Azure.

![Captura de tela da opção de exportação de arquivo local JSON do Azure Cosmos DB](./media/import-data/jsontarget.png)

![Captura de tela da opção de exportação de JSON do Armazenamento de blobs do Azure no Azure Cosmos DB](./media/import-data/jsontarget2.png)

Você pode optar por melhorar a aparência do JSON resultante, o que aumentará o tamanho do documento resultante, tornando o conteúdo mais legível.

    Standard JSON export
    [{"id":"Sample","Title":"About Paris","Language":{"Name":"English"},"Author":{"Name":"Don","Location":{"City":"Paris","Country":"France"}},"Content":"Don's document in Azure Cosmos DB is a valid JSON document as defined by the JSON spec.","PageViews":10000,"Topics":[{"Title":"History of Paris"},{"Title":"Places to see in Paris"}]}]

    Prettified JSON export
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

## <a name="advanced-configuration"></a>Configuração avançada
Na tela de Configuração avançada, especifique a localização do arquivo de log do qual você gostaria que os erros fossem gravados. As seguintes regras se aplicam a esta página:

1. Se não for fornecido um nome de arquivo, todos os erros serão retornados na página Resultados.
2. Se for fornecido um nome de arquivo sem um diretório, o arquivo será ser criado (ou substituído) no diretório atual do ambiente.
3. Se você selecionar um arquivo existente, o arquivo será substituído, não há nenhuma opção de acréscimo.

Em seguida, escolha se deseja registrar, todas as mensagens de erro, nenhuma mensagem ou as mensagens críticas. Finalmente, decida com que frequência a mensagem de transferência na tela será atualizada com seu progresso.

    ![Screenshot of Advanced configuration screen](./media/import-data/AdvancedConfiguration.png)

## <a name="confirm-import-settings-and-view-command-line"></a>Confirme as configurações de importação e de linha de comando de exibição
1. Depois de especificar as informações de origem, as informações de destino e a configuração avançada, examine a resumo de migração e, opcionalmente, examine/copie o comando resultante da migração (copiar o comando é útil para automatizar as operações de importação):
   
    ![Captura de tela da tela de resumo](./media/import-data/summary.png)
   
    ![Captura de tela da tela de resumo](./media/import-data/summarycommand.png)
2. Quando estiver satisfeito com suas opções de origem e de destino, clique em **Importar**. O tempo decorrido, a contagem transferida e as informações de falha (se você não fornecer um nome de arquivo na Configuração avançada) serão atualizados à medida que a importação está em andamento. Uma vez concluído, você pode exportar os resultados (por exemplo, para lidar com as falhas de importação).
   
    ![Captura de tela da opção de exportação de JSON do Azure Cosmos DB](./media/import-data/viewresults.png)
3. Você também pode iniciar uma nova importação, mantendo as configurações existentes (por exemplo, informações da cadeia de conexão, escolha de origem e destino, etc.) ou redefinindo todos os valores.
   
    ![Captura de tela da opção de exportação de JSON do Azure Cosmos DB](./media/import-data/newimport.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você fez o seguinte:

> [!div class="checklist"]
> * Instalou a ferramenta de Migração de Dados
> * Importou dados de diferentes fontes de dados
> * Exportou do Azure Cosmos DB para o JSON

Agora, você pode seguir para o próximo tutorial e saber como consultar dados usando o Azure Cosmos DB. 

> [!div class="nextstepaction"]
>[Como consultar os dados?](../cosmos-db/tutorial-query-documentdb.md)
