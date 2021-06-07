---
title: Usar o conector do Spark com Microsoft Azure SQL e SQL Server
description: Saiba como usar o conector do Spark com o banco de dados SQL do Azure, o SQL Instância Gerenciada do Azure e o SQL Server.
services: sql-database
ms.service: sql-db-mi
ms.subservice: development
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: denzilribeiro
ms.author: denzilr
ms.reviewer: sstein
ms.date: 09/02/2020
ms.openlocfilehash: 2e01e74f5086f7f1eb7e85661fbd35f452d8dae8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92790195"
---
# <a name="accelerate-real-time-big-data-analytics-using-the-spark-connector"></a>Acelere a análise de Big Data em tempo real usando o conector do Spark
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

> [!NOTE]
> A partir de setembro de 2020, esse conector não é mantido ativamente. No entanto, o [conector de Apache Spark para SQL Server e o Azure SQL](/sql/connect/spark/connector) agora estão disponíveis, com suporte para as associações de Python e R, uma interface mais fácil de usar para inserir dados em massa e muitas outras melhorias. É altamente recomendável que você avalie e use o novo conector, em vez deste. As informações sobre o conector antigo (esta página) só são mantidas para fins de arquivamento.

O conector do Spark permite que os bancos de dados do Azure SQL Database, Azure SQL Instância Gerenciada e SQL Server atuem como o coletor de dados de entrada ou de saída para trabalhos do Spark. Ele permite que você utilize dados transacionais em tempo real na análise de Big Data e persiste resultados para consultas ad hoc ou relatórios. Em comparação com o conector JDBC interno, esse conector fornece a capacidade de inserir dados em massa em seu banco de dado. Ele pode superar a inserção de linha por linha com 10 vezes para 20x um desempenho mais rápido. O conector do Spark dá suporte à autenticação Azure Active Directory (AD do Azure) para se conectar ao banco de dados SQL do Azure e ao SQL do Azure Instância Gerenciada, permitindo que você conecte seu banco de dados do Azure Databricks usando sua conta do Azure AD. Ele fornece interfaces semelhantes com o conector interno do JDBC. É muito fácil migrar trabalhos do Spark existentes para usar esse novo conector.

## <a name="download-and-build-a-spark-connector"></a>Baixar e compilar um conector do Spark

O repositório GitHub para o conector antigo anteriormente vinculado a partir dessa página não é mantido ativamente. Em vez disso, é altamente recomendável avaliar e usar o [novo conector](https://github.com/microsoft/sql-spark-connector).

### <a name="official-supported-versions"></a>Versões oficiais com suporte

| Componente                             | Versão                  |
| :-----------------------------------  | :----------------------- |
| Apache Spark                          | 2.0.2 ou posterior           |
| Scala                                 | 2.10 ou posterior            |
| Microsoft JDBC Driver para SQL Server  | 6.2 ou posterior             |
| Microsoft SQL Server                  | SQL Server 2008 ou posterior |
| Banco de Dados SQL do Azure                    | Com suporte                |
| Instância Gerenciada do Azure SQL            | Com suporte                |

O conector do Spark utiliza o Microsoft JDBC Driver para SQL Server para mover dados entre os nós de trabalho do Spark e os do:

O fluxo de dados é o seguinte:

1. O nó mestre do Spark conecta-se aos bancos de dados no banco de dados SQL ou SQL Server e carrega o dado de uma tabela específica ou usando uma consulta SQL específica.
2. O nó mestre Spark distribui os dados para nós de trabalho para transformação.
3. O nó de trabalho conecta-se a bancos de dados que se conectam ao banco de dados SQL e SQL Server e gravam os dados no banco de dado. O usuário pode optar por usar a inserção de linha por linha ou em massa.

O diagrama a seguir ilustra o fluxo de dados.

   ![O diagrama mostra o fluxo descrito, com um nó mestre conectando-se diretamente ao banco de dados e se conectando a três nós de trabalho, que se conectam ao banco de dados.](./media/spark-connector/architecture.png)

### <a name="build-the-spark-connector"></a>Criar o conector do Spark

Atualmente, o projeto do conector usa maven. Para compilar o conector sem dependências, execute:

- mvn clean package
- Baixe as versões mais recentes do JAR da pasta release
- Incluir o banco de dados SQL Spark JAR

## <a name="connect-and-read-data-using-the-spark-connector"></a>Conectar e ler dados usando o conector do Spark

Você pode conectar-se a bancos de dados no banco de dados SQL e SQL Server de um trabalho do Spark para ler ou gravar. Você também pode executar uma consulta DML ou DDL em bancos de dados no banco de dados SQL e SQL Server.

### <a name="read-data-from-azure-sql-and-sql-server"></a>Ler dados do Azure SQL e SQL Server

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"            -> "mysqlserver.database.windows.net",
  "databaseName"   -> "MyDatabase",
  "dbTable"        -> "dbo.Clients",
  "user"           -> "username",
  "password"       -> "*********",
  "connectTimeout" -> "5", //seconds
  "queryTimeout"   -> "5"  //seconds
))

val collection = sqlContext.read.sqlDB(config)
collection.show()
```

### <a name="read-data-from-azure-sql-and-sql-server-with-specified-sql-query"></a>Ler dados do Azure SQL e SQL Server com a consulta SQL especificada

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
val collection = sqlContext.read.sqlDB(config)
collection.show()
```

### <a name="write-data-to-azure-sql-and-sql-server"></a>Gravar dados no Azure SQL e SQL Server

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

// Aquire a DataFrame collection (val collection)

val config = Config(Map(
  "url"          -> "mysqlserver.database.windows.net",
  "databaseName" -> "MyDatabase",
  "dbTable"      -> "dbo.Clients",
  "user"         -> "username",
  "password"     -> "*********"
))

import org.apache.spark.sql.SaveMode
collection.write.mode(SaveMode.Append).sqlDB(config)
```

### <a name="run-dml-or-ddl-query-in-azure-sql-and-sql-server"></a>Executar a consulta DML ou DDL no SQL do Azure e SQL Server

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.query._
val query = """
              |UPDATE Customers
              |SET ContactName = 'Alfred Schmidt', City = 'Frankfurt'
              |WHERE CustomerID = 1;
            """.stripMargin

val config = Config(Map(
  "url"          -> "mysqlserver.database.windows.net",
  "databaseName" -> "MyDatabase",
  "user"         -> "username",
  "password"     -> "*********",
  "queryCustom"  -> query
))

sqlContext.sqlDBQuery(config)
```

## <a name="connect-from-spark-using-azure-ad-authentication"></a>Conectar do Spark usando a autenticação do Azure AD

Você pode se conectar ao banco de dados SQL do Azure e ao SQL Instância Gerenciada usando a autenticação do Azure AD. Use a autenticação do Azure AD para gerenciar de forma centralizada as identidades de usuários do banco de dados e como uma alternativa à autenticação do SQL Server.

### <a name="connecting-using-activedirectorypassword-authentication-mode"></a>Conectando-se usando o modo de autenticação ActiveDirectoryPassword

#### <a name="setup-requirement"></a>Requisito de instalação

Se você estiver usando o modo de autenticação ActiveDirectoryPassword, você precisará baixar [azure-activedirectory-library-for-java](https://github.com/AzureAD/azure-activedirectory-library-for-java) e suas dependências, e incluí-las no caminho de build de Java.

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"            -> "mysqlserver.database.windows.net",
  "databaseName"   -> "MyDatabase",
  "user"           -> "username",
  "password"       -> "*********",
  "authentication" -> "ActiveDirectoryPassword",
  "encrypt"        -> "true"
))

val collection = sqlContext.read.sqlDB(config)
collection.show()
```

### <a name="connecting-using-an-access-token"></a>Conectando usando um token de acesso

#### <a name="setup-requirement"></a>Requisito de instalação

Se você estiver usando o modo de autenticação baseado em token de acesso, será necessário baixar [azure-activedirectory-library-for-java](https://github.com/AzureAD/azure-activedirectory-library-for-java) e suas dependências, e incluí-las no caminho de build de Java.

Consulte [usar a autenticação Azure Active Directory para autenticação](authentication-aad-overview.md) para saber como obter um token de acesso para seu banco de dados no banco de dados SQL do azure ou instância gerenciada SQL do Azure.

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"                   -> "mysqlserver.database.windows.net",
  "databaseName"          -> "MyDatabase",
  "accessToken"           -> "access_token",
  "hostNameInCertificate" -> "*.database.windows.net",
  "encrypt"               -> "true"
))

val collection = sqlContext.read.sqlDB(config)
collection.show()
```

## <a name="write-data-using-bulk-insert"></a>Gravar dados usando a inserção em massa

O conector JDBC tradicional grava dados em seu banco de dado usando a inserção linha por linha. Você pode usar o conector do Spark para gravar dados no SQL do Azure e SQL Server usando a inserção em massa. Ele melhora significativamente o desempenho de gravação durante o carregamento de grandes conjuntos de dados ou ao carregar dados em tabelas em que um índice de repositório de coluna é usado.

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

Se você ainda não fez isso, baixe o conector do Spark do [repositório GitHub do Azure-SQLDB-Spark](https://github.com/Azure/azure-sqldb-spark) e explore os recursos adicionais no repositório:

- [Blocos de anotações de exemplo do Azure Databricks](https://github.com/Azure/azure-sqldb-spark/tree/master/samples/notebooks)
- [Scripts de exemplo (Scala)](https://github.com/Azure/azure-sqldb-spark/tree/master/samples/scripts)

Pode ser útil também ler o [Guia do Apache Spark SQL, DataFrames e Conjuntos de dados](https://spark.apache.org/docs/latest/sql-programming-guide.html) e a [documentação do Azure Databricks](/azure/azure-databricks/).