---
title: O Spark Connector com o Banco de Dados SQL do Azure e SQL Server| Microsoft Docs
description: Saiba como usar o Spark Connector para Banco de Dados SQL do Azure e SQL Server
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: carlrab
manager: craigg
ms.date: 09/25/2018
ms.openlocfilehash: 192387958785e4032a1cb549d0ba071fa406a60e
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "68228216"
---
# <a name="accelerate-real-time-big-data-analytics-with-spark-connector-for-azure-sql-database-and-sql-server"></a>Acelerar a análise de Big Data em tempo real com o conector do Spark para Banco de Dados SQL do Azure e SQL Server

O conector do Spark para Banco de Dados SQL do Azure e SQL Server permite que os bancos de dados SQL, incluindo o Banco de Dados SQL do Azure e SQL Server, para atuar como fonte de dados de entrada ou coletor de dados de saída para trabalhos do Spark. Ele permite que você utilize dados transacionais em tempo real na análise de Big Data e manter os resultados para consultas ou relatórios ad hoc. Em comparação com o conector interno do JDBC, este conector fornece a capacidade de inserir dados em massa em bancos de dados SQL. Ele pode superar a inserção de linha por linha com desempenho de 10 a 20 vezes mais rápido. O conector do Spark para Banco de Dados SQL do Azure e SQL Server também há compatibilidade com a autenticação do AAD. Ele permite conectar uma segurança ao seu Banco de Dados SQL do Azure do Azure Databricks usando sua conta do AAD. Ele fornece interfaces semelhantes com o conector interno do JDBC. É muito fácil migrar trabalhos do Spark existentes para usar esse novo conector.

## <a name="download"></a>Baixar
Para começar, baixe conector Spark para BD SQL do [repositório azure-sqldb-spark](https://github.com/Azure/azure-sqldb-spark) do GitHub.

## <a name="official-supported-versions"></a>Versões oficiais compatíveis

| Componente                            |Versão                  |
| :----------------------------------- | :---------------------- |
| Apache Spark                         |2.0.2 ou posterior           |
| Scala                                |2.10 ou posterior            |
| Microsoft JDBC Driver para SQL Server |6.2 ou posterior             |
| Microsoft SQL Server                 |SQL Server 2008 ou posterior |
| Banco de Dados SQL do Azure                   |Com suporte                |

O conector Spark para Banco de Dados SQL do Azure e o SQL Server utilizam o Microsoft JDBC Driver para SQL Server para mover dados entre nós de trabalho Spark e Bancos de Dados SQL:
 
O fluxo de dados é o seguinte:
1. O nó mestre Spark se conecta ao SQL Server ou ao Banco de Dados SQL do Azure e carrega os dados de uma tabela específica ou usando uma consulta SQL específica
2. O nó mestre Spark distribui os dados para nós de trabalho para transformação. 
3. O nó de Trabalho se conecta ao SQL Server ou ao Banco de Dados SQL do Azure e grava dados no banco de dados. O usuário pode optar por usar a inserção de linha por linha ou em massa.

O diagrama a seguir ilustra o fluxo de dados.

   ![Arquitetura](./media/sql-database-spark-connector/architecture.png)

### <a name="build-the-spark-to-sql-db-connector"></a>Criar o conector Spark para BD SQL
Atualmente, o projeto do conector usa maven. Para compilar o conector sem dependências, execute:

- mvn clean package
- Baixe as versões mais recentes do JAR da pasta release
- Incluir o BD SQL Spark JAR

## <a name="connect-spark-to-sql-db-using-the-connector"></a>Conectar o Spark ao BD SQL usando o conector
Você pode se conectar ao Banco de Dados SQL do Azure ou o SQL Server de trabalhos do Spark, ler ou gravar dados. Você também pode executar uma consulta DML ou DDL em um Banco de Dados SQL do Azure ou o banco de dados do SQL Server.

### <a name="read-data-from-azure-sql-database-or-sql-server"></a>Ler dados do Banco de Dados SQL do Azure ou SQL Server

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"            -> "mysqlserver.database.windows.net",
  "databaseName"   -> "MyDatabase",
  "dbTable"        -> "dbo.Clients"
  "user"           -> "username",
  "password"       -> "*********",
  "connectTimeout" -> "5", //seconds
  "queryTimeout"   -> "5"  //seconds
))

val collection = sqlContext.read.sqlDB(config)
collection.show()
```
### <a name="reading-data-from-azure-sql-database-or-sql-server-with-specified-sql-query"></a>Lendo dados do Banco de Dados SQL do Azure ou do SQL Server com uma consulta SQL especificada
```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"          -> "mysqlserver.database.windows.net",
  "databaseName" -> "MyDatabase",
  "queryCustom"  -> "SELECT TOP 100 * FROM dbo.Clients WHERE PostalCode = 98074" //Sql query
  "user"         -> "username",
  "password"     -> "*********",
))

//Read all data in table dbo.Clients
val collection = sqlContext.read.sqlDb(config)
collection.show()
```

### <a name="write-data-to-azure-sql-database-or-sql-server"></a>Gravar dados do Banco de Dados SQL do Azure ou SQL Server
```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._
 
// Aquire a DataFrame collection (val collection)

val config = Config(Map(
  "url"          -> "mysqlserver.database.windows.net",
  "databaseName" -> "MyDatabase",
  "dbTable"      -> "dbo.Clients"
  "user"         -> "username",
  "password"     -> "*********"
))

import org.apache.spark.sql.SaveMode
collection.write.mode(SaveMode.Append).sqlDB(config)
```

### <a name="run-dml-or-ddl-query-in-azure-sql-database-or-sql-server"></a>Executar a consulta DML ou DDL no Banco de Dados SQL do Azure ou SQL Server
```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.query._
val query = """
              |UPDATE Customers
              |SET ContactName = 'Alfred Schmidt', City= 'Frankfurt'
              |WHERE CustomerID = 1;
            """.stripMargin

val config = Config(Map(
  "url"          -> "mysqlserver.database.windows.net",
  "databaseName" -> "MyDatabase",
  "user"         -> "username",
  "password"     -> "*********",
  "queryCustom"  -> query
))

sqlContext.SqlDBQuery(config)
```

## <a name="connect-spark-to-azure-sql-database-using-aad-authentication"></a>Conectar o Spark ao Banco de Dados SQL do Azure usando a autenticação do AAD
Você pode se conectar ao Banco de Dados SQL do Azure usando a autenticação do AAD (Azure Active Directory). Use a autenticação do AAD para gerenciar centralmente as identidades de usuários de banco de dados e como uma alternativa para autenticação do SQL Server.
### <a name="connecting-using-activedirectorypassword-authentication-mode"></a>Conectando-se usando o modo de autenticação ActiveDirectoryPassword
#### <a name="setup-requirement"></a>Requisitos da instalação
Se você estiver usando o modo de autenticação ActiveDirectoryPassword, você precisará baixar [azure-activedirectory-library-for-java](https://github.com/AzureAD/azure-activedirectory-library-for-java) e suas dependências, e incluí-las no caminho de build de Java.

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"            -> "mysqlserver.database.windows.net",
  "databaseName"   -> "MyDatabase",
  "user"           -> "username ",
  "password"       -> "*********",
  "authentication" -> "ActiveDirectoryPassword",
  "encrypt"        -> "true"
))

val collection = sqlContext.read.SqlDB(config)
collection.show()
```

### <a name="connecting-using-access-token"></a>Conectando-se usando um token de acesso
#### <a name="setup-requirement"></a>Requisitos da instalação
Se você estiver usando o modo de autenticação baseado em token de acesso, será necessário baixar [azure-activedirectory-library-for-java](https://github.com/AzureAD/azure-activedirectory-library-for-java) e suas dependências, e incluí-las no caminho de build de Java.

Consulte [Usar a autenticação do Azure Active Directory para autenticação com o Banco de Dados SQL](sql-database-aad-authentication.md) para saber como obter o token de acesso para o Banco de Dados SQL do Azure.

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"                   -> "mysqlserver.database.windows.net",
  "databaseName"          -> "MyDatabase",
  "accessToken"           -> "access_token ",
  "hostNameInCertificate" -> "*.database.windows.net",
  "encrypt"               -> "true"
))

val collection = sqlContext.read.SqlDB(config)
collection.show()
```

## <a name="write-data-to-azure-sql-database-or-sql-server-using-bulk-insert"></a>Gravar dados do Banco de Dados SQL do Azure ou SQL Server usando inserção em massa
O conector do jdbc tradicional grava dados no Banco de Dados SQL do Azure ou o SQL Server usando a inserção linha por linha. Você pode usar o conector Spark para BD SQL para gravar dados no Banco de Dados SQL usando inserção em massa. Ele melhora significativamente o desempenho de gravação durante o carregamento de grandes conjuntos de dados ou ao carregar dados em tabelas em que um índice de repositório de coluna é usado.

```scala
import com.microsoft.azure.sqldb.spark.bulkcopy.BulkCopyMetadata
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

/** 
  Add column Metadata.
  If not specified, metadata is automatically added
  from the destination table, which may suffer performance.
*/
var bulkCopyMetadata = new BulkCopyMetadata
bulkCopyMetadata.addColumnMetadata(1, "Title", java.sql.Types.NVARCHAR, 128, 0)
bulkCopyMetadata.addColumnMetadata(2, "FirstName", java.sql.Types.NVARCHAR, 50, 0)
bulkCopyMetadata.addColumnMetadata(3, "LastName", java.sql.Types.NVARCHAR, 50, 0)

val bulkCopyConfig = Config(Map(
  "url"               -> "mysqlserver.database.windows.net",
  "databaseName"      -> "MyDatabase",
  "user"              -> "username",
  "password"          -> "*********",
  "dbTable"           -> "dbo.Clients",
  "bulkCopyBatchSize" -> "2500",
  "bulkCopyTableLock" -> "true",
  "bulkCopyTimeout"   -> "600"
))

df.bulkCopyToSqlDB(bulkCopyConfig, bulkCopyMetadata)
//df.bulkCopyToSqlDB(bulkCopyConfig) if no metadata is specified.
```

## <a name="next-steps"></a>Próximas etapas
Se ainda não tiver feito isso, baixe o conector do Spark para o Banco de Dados SQL do Azure e o SQL Server do [repositório do GitHub azure-sqldb-spark](https://github.com/Azure/azure-sqldb-spark) e explore os recursos adicionais no repositório:

-   [Blocos de anotações de exemplo do Azure Databricks](https://github.com/Azure/azure-sqldb-spark/tree/master/samples/notebooks)
- [Scripts de exemplo (Scala)](https://github.com/Azure/azure-sqldb-spark/tree/master/samples/scripts)

Pode ser útil também ler o [Guia do Apache Spark SQL, DataFrames e Conjuntos de dados](https://spark.apache.org/docs/latest/sql-programming-guide.html) e a [documentação do Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/).

