---
title: Conectar-se ao Synapse SQL com sqlcmd
description: Use o utilitário de linha de comando sqlcmd para se conectar ao pool de SQL sem servidor e ao pool de SQL dedicado e consultá-los.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 3abdd44f0684282e92da147dff996ff54f0ef23f
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107565467"
---
# <a name="connect-to-synapse-sql-with-sqlcmd"></a>Conectar-se ao Synapse SQL com sqlcmd

> [!div class="op_single_selector"]
> * [Azure Data Studio](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
> * [sqlcmd](../sql/get-started-connect-sqlcmd.md)
> * [SSMS](get-started-ssms.md)

Você pode usar o utilitário de linha de comando [sqlcmd](/sql/tools/sqlcmd-utility?view=azure-sqldw-latest&preserve-view=true) para se conectar ao pool de SQL sem servidor e ao pool de SQL dedicado no SQL do Synapse e consultá-los.  

## <a name="1-connect"></a>1. Conectar
Para começar com o [sqlcmd](/sql/tools/sqlcmd-utility?view=azure-sqldw-latest&preserve-view=true), abra o prompt de comando e digite **sqlcmd** seguido da cadeia de conexão do seu banco de dados do Synapse SQL. A cadeia de conexão precisará dos seguintes parâmetros:

* **Servidor (-S):** servidor no formato `<`Nome do Servidor`>`.database.windows.net
* **Banco de dados (-d):** Nome do banco de dados
* **Habilitar identificadores entre aspas (-I):** Os identificadores entre aspas devem ser habilitados para conectar-se a uma instância do Synapse SQL

Para usar a Autenticação do SQL Server, você precisa adicionar os parâmetros do nome de usuário e senha:

* **Usuário (-U):** usuário do servidor no formato `<`Usuário`>`
* **Senha (-P):** Senha associada ao usuário

Sua cadeia de conexão deverá ficar como o seguinte exemplo:

**Pool de SQL sem servidor**

```sql
C:\>sqlcmd -S partyeunrt.database.windows.net -d demo -U Enter_Your_Username_Here -P Enter_Your_Password_Here -I
```

**Pool de SQL dedicado**

```
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
```

Para usar a autenticação Integrada do Azure Active Directory, você precisa adicionar os parâmetros do Azure Active Directory:

* **Autenticação do Azure Active Directory (-G):** usar o Azure Active Directory para a autenticação

Sua cadeia de conexão deverá ficar como um dos seguintes exemplos:

**Pool de SQL sem servidor**

```
C:\>sqlcmd -S partyeunrt.database.windows.net -d demo -G -I
```

**Pool de SQL dedicado**

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -G -I
```

> [!NOTE]
> Você precisa [habilitar a Autenticação do Azure Active Directory](../sql-data-warehouse/sql-data-warehouse-authentication.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) para autenticar usando o Active Directory.

## <a name="2-query"></a>2. Consulta

### <a name="use-dedicated-sql-pool"></a>Use o pool de SQL dedicado

Após a conexão, você pode executar instruções [T-SQL](/sql/t-sql/language-reference?view=azure-sqldw-latest&preserve-view=true) (Transact-SQL) compatíveis na instância. Neste exemplo, as consultas são enviadas no modo interativo:

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
1> SELECT name FROM sys.tables;
2> GO
3> QUIT
```

Para o pool de SQL dedicado, os seguintes exemplos mostram como executar consultas no modo de lote usando a opção -Q ou canalizando o seu SQL para sqlcmd:

```sql
sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I -Q "SELECT name FROM sys.tables;"
```

```sql
"SELECT name FROM sys.tables;" | sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I > .\tables.out
```

### <a name="use-serverless-sql-pool"></a>Usar o pool de SQL sem servidor

Após a conexão, você pode emitir instruções [T-SQL](/sql/t-sql/language-reference?view=azure-sqldw-latest&preserve-view=true) (Transact-SQL) compatíveis com a instância.  No seguinte exemplo, as consultas são enviadas no modo interativo:

```sql
C:\>sqlcmd -S partyeunrt.database.windows.net -d demo -U Enter_Your_Username_Here -P Enter_Your_Password_Here -I
1> SELECT COUNT(*) FROM  OPENROWSET(BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet', FORMAT='PARQUET')
2> GO
3> QUIT
```

Para o pool de SQL sem servidor, os seguintes exemplos mostram como executar consultas no modo de lote usando a opção -Q ou canalizando o seu SQL para sqlcmd:

```sql
sqlcmd -S partyeunrt.database.windows.net -d demo -U Enter_Your_Username_Here -P 'Enter_Your_Password_Here' -I -Q "SELECT COUNT(*) FROM  OPENROWSET(BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet', FORMAT='PARQUET')"
```

```sql
"SELECT COUNT(*) FROM  OPENROWSET(BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet', FORMAT='PARQUET')" | sqlcmd -S partyeunrt.database.windows.net -d demo -U Enter_Your_Username_Here -P 'Enter_Your_Password_Here' -I > ./tables.out
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre as opções do sqlcmd, confira a [documentação do sqlcmd](/sql/tools/sqlcmd-utility?view=azure-sqldw-latest&preserve-view=true).
