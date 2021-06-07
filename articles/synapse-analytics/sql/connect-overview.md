---
title: Conectar-se ao Synapse SQL
description: Conecte-se ao Synapse SQL.
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.custom: devx-track-csharp
ms.openlocfilehash: f5682302ea0fa83c04a8560ba3f0f98ea075e072
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107565535"
---
# <a name="connect-to-synapse-sql"></a>Conectar-se ao Synapse SQL
Conecte-se à funcionalidade Synapse SQL no Azure Synapse Analytics.

## <a name="supported-tools-for-serverless-sql-pool"></a>Ferramentas compatíveis com o pool de SQL sem servidor

O [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) tem total compatibilidade da versão 1.18.0 em diante. O SSMS tem compatibilidade parcial da versão 18.5 em diante. Você pode usá-lo somente para se conectar e consultar.

> [!NOTE]
> Se um logon do AAD tiver uma conexão aberta por mais de 1 hora no momento da execução da consulta, qualquer consulta que depender do AAD falhará. Isso inclui consultar o armazenamento usando a passagem do AAD e instruções que interagem com o AAD (como CREATE EXTERNAL PROVIDER). Isso afeta todas as ferramentas que mantêm conexões abertas, como no editor de consultas no SSMS e no ADS. As ferramentas que abrem novas conexões para executar uma consulta, como o Synapse Studio, não são afetadas.

> Você pode reiniciar o SSMS ou conectar-se e desconectar-se no ADS para atenuar esse problema. 

## <a name="find-your-server-name"></a>Localizar o nome do servidor

O nome do servidor para o pool de SQL dedicado no exemplo seguinte é: showdemoweu.sql.azuresynapse.net.
O nome do servidor para o pool de SQL sem servidor no exemplo seguinte é: showdemoweu-ondemand.sql.azuresynapse.net.

Para localizar o nome de servidor totalmente qualificado:

1. Vá para o [Portal do Azure](https://portal.azure.com).
2. Selecione **Workspaces do Azure Synapse**.
3. Selecione o workspace ao qual deseja se conectar.
4. Acesse a visão geral.
5. Localize o nome completo do servidor.

## <a name="sql-pool"></a>**Pool de SQL**

![Nome completo do servidor](./media/connect-overview/server-connect-example.png)

## <a name="serverless-sql-pool"></a>**Pool de SQL sem servidor**

![Nome completo do servidor do pool de SQL sem servidor](./media/connect-overview/server-connect-example-sqlod.png)

## <a name="supported-drivers-and-connection-strings"></a>Drivers suportados e cadeias de conexão
O Synapse SQL dá suporte a [ADO.NET](/dotnet/framework/data/adonet/), [ODBC](/sql/connect/odbc/windows/microsoft-odbc-driver-for-sql-server-on-windows), [PHP](/sql/connect/php/overview-of-the-php-sql-driver?f=255&MSPPError=-2147217396) e [JDBC](/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server). Para encontrar a versão e a documentação mais recentes, selecione um dos drivers anteriores. Para gerar automaticamente a cadeia de conexão para o driver que você está usando no Portal do Azure, selecione **Mostrar cadeias de conexão de banco de dados** no exemplo anterior. A seguir também há alguns exemplos da aparência de uma cadeia de conexão para cada driver.

> [!NOTE]
> Considere definir o tempo limite de conexão para 300 segundos a fim de permitir que a conexão perdure em curtos períodos de indisponibilidade.

### <a name="adonet-connection-string-example"></a>Exemplo de cadeia de conexão do ADO.NET

```csharp
Server=tcp:{your_server}.sql.azuresynapse.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

### <a name="odbc-connection-string-example"></a>Exemplo de cadeia de conexão do ODBC

```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.sql.azuresynapse.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

### <a name="php-connection-string-example"></a>Exemplo de cadeia de conexão do PHP

```PHP
Server: {your_server}.sql.azuresynapse.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.sql.azuresynapse.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.sql.azuresynapse.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

### <a name="jdbc-connection-string-example"></a>Exemplo de cadeia de conexão do JDBC

```Java
jdbc:sqlserver://yourserver.sql.azuresynapse.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.sql.azuresynapse.net;loginTimeout=30;
```

## <a name="connection-settings"></a>Configurações de conexão
O Synapse SQL padroniza algumas configurações durante a conexão e a criação do objeto. Essas configurações não podem ser substituídas e incluem:

| Configuração de banco de dados | Valor |
|:--- |:--- |
| [ANSI_NULLS](/sql/t-sql/statements/set-ansi-nulls-transact-sql?view=azure-sqldw-latest&preserve-view=true) |ATIVADO |
| [QUOTED_IDENTIFIERS](/sql/t-sql/statements/set-quoted-identifier-transact-sql?view=azure-sqldw-latest&preserve-view=true) |ATIVADO |
| [DATEFORMAT](/sql/t-sql/statements/set-dateformat-transact-sql?view=azure-sqldw-latest&preserve-view=true) |mda |
| [DATEFIRST](/sql/t-sql/statements/set-datefirst-transact-sql?view=azure-sqldw-latest&preserve-view=true) |7 |

## <a name="recommendations"></a>Recomendações

Para executar consultas no **Pool de SQL sem servidor**, as ferramentas recomendadas são o [Azure Data Studio](get-started-azure-data-studio.md) e o Azure Synapse Studio.

## <a name="next-steps"></a>Próximas etapas
Para se conectar e consultar com o Visual Studio, veja [Consultar com o Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json). Para saber mais sobre as opções de autenticação, confira [Autenticação no Synapse SQL](../sql-data-warehouse/sql-data-warehouse-authentication.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).