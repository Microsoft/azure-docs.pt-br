---
title: Conectar-se ao sqlcmd do SQL Data Warehouse do Azure | Microsoft Docs
description: Use o utilitário de linha de comando sqlcmd para conectar-se ao SQL Data Warehouse do Azure e fazer uma consulta.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: f3b93660fb9f8f3b0bfdddc37105b9e998ed9eee
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479498"
---
# <a name="connect-to-sql-data-warehouse-with-sqlcmd"></a>Conecte-se ao SQL Data Warehouse com sqlcmd
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

Use o utilitário de linha de comando [sqlcmd][sqlcmd] para se conectar e consultar um SQL data warehouse do Azure.  

## <a name="1-connect"></a>1. Conectar
Para começar com o [sqlcmd][sqlcmd], abra o prompt de comando e digite **sqlcmd** seguido da cadeia de conexão do banco de dados SQL Data Warehouse. A cadeia de conexão precisará dos seguintes parâmetros:

* **Servidor (-S):** servidor no formato `<`Nome do Servidor`>`.database.windows.net
* **Banco de dados (-d):** nome do banco de dados.
* **Habilitar identificadores entre aspas (-I):** os identificadores entre aspas devem ser habilitados para conectarem uma instância do SQL Data Warehouse.

Para usar a Autenticação do SQL Server, você precisa adicionar os parâmetros do nome de usuário/senha:

* **Usuário (-U):** usuário do servidor no formato `<`Usuário`>`
* **Senha (-P):** senha associada ao usuário.

Por exemplo, a cadeia de conexão pode parecer com o seguinte:

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
```

Para usar a autenticação Integrada do Azure Active Directory, você precisa adicionar os parâmetros do Azure Active Directory:

* **Autenticação do Azure Active Directory (-G):** usar o Azure Active Directory para a autenticação

Por exemplo, a cadeia de conexão pode parecer com o seguinte:

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -G -I
```

> [!NOTE]
> Você precisa [habilitar a Autenticação do Azure Active Directory](sql-data-warehouse-authentication.md) para autenticar usando o Active Directory.
> 
> 

## <a name="2-query"></a>2. Consulta
Após a conexão, você pode executar quaisquer instruções Transact-SQL compatíveis com a instância.  Neste exemplo, as consultas são enviadas no modo interativo.

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
1> SELECT name FROM sys.tables;
2> GO
3> QUIT
```

Os próximos exemplos mostram como você pode executar as consultas no modo de lote usando a opção -Q ou direcionando o SQL para sqlcmd.

```sql
sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I -Q "SELECT name FROM sys.tables;"
```

```sql
"SELECT name FROM sys.tables;" | sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I > .\tables.out
```

## <a name="next-steps"></a>Próximas etapas
Consulte a [documentação do sqlcmd][sqlcmd] para obter mais detalhes sobre as opções disponíveis no sqlcmd.

<!--Image references-->

<!--Article references-->

<!--MSDN references--> 
[sqlcmd]: https://msdn.microsoft.com/library/ms162773.aspx
[Azure portal]: https://portal.azure.com

<!--Other Web references-->
