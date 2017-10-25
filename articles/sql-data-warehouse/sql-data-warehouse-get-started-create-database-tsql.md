---
title: Criar um SQL Data Warehouse com o TSQL | Microsoft Docs
description: Saiba como criar um SQL Data Warehouse do Azure com o TSQL
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: jhubbard
editor: 
tags: azure-sql-data-warehouse
ms.assetid: a4e2e68e-aa9c-4dd3-abb0-f7df997d237a
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: create
ms.date: 10/31/2016
ms.author: elbutter;barbkess
ms.openlocfilehash: 10d8aa2b3ab8d7d8a9b91e95ffccf03faa89d237
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-sql-data-warehouse-database-by-using-transact-sql-tsql"></a>Criar um banco de dados do SQL Data Warehouse usando TSQL (Transact-SQL)
> [!div class="op_single_selector"]
> * [Portal do Azure](sql-data-warehouse-get-started-provision.md)
> * [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
> * [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)
>
>

Este artigo mostra como criar um SQL Data Warehouse usando o T-SQL.

## <a name="prerequisites"></a>Pré-requisitos
Para começar, você precisa do seguinte:

* **Conta do Azure**: visite [Avaliação gratuita do Azure][Azure Free Trial] ou [Créditos do Azure no MSDN][MSDN Azure Credits] para criar uma conta.
* **Servidor SQL do Azure**: veja [Criar um servidor lógico do Banco de Dados SQL do Azure com o portal do Azure][Criar um servidor lógico do Banco de Dados SQL do Azure com o portal do Azure] ou [Criar um servidor lógico do Banco de Dados SQL do Azure com o PowerShell][Criar um servidor lógico do Banco de Dados SQL do Azure com o PowerShell] para obter mais detalhes.
* **Grupo de recursos**: use o mesmo grupo de recursos do servidor SQL do Azure ou veja [como criar um grupo de recursos][how to create a resource group].
* **Ambiente para executar o T-SQL**: você pode usar o [Visual Studio][Installing Visual Studio and SSDT], [sqlcmd][sqlcmd] ou [SSMS][SSMS] para executar o T-SQL.

> [!NOTE]
> A criação de um SQL Data Warehouse pode resultar em um novo serviço faturável.  Confira [Preços do SQL Data Warehouse][SQL Data Warehouse pricing] para obter mais detalhes sobre preços.
>
>

## <a name="create-a-database-with-visual-studio"></a>Criar um banco de dados com o Visual Studio
Se você for novo no Visual Studio, confira o artigo [Consultar o Azure SQL Data Warehouse (Visual Studio)][Query Azure SQL Data Warehouse (Visual Studio)].  Para começar, abra o Pesquisador de Objetos do SQL Server no Visual Studio e conecte-se ao servidor que hospedará seu banco de dados do SQL Data Warehouse.  Quando estiver conectado, você poderá criar um SQL Data Warehouse executando o seguinte comando SQL no banco de dados **mestre** .  Este comando cria o banco de dados MySqlDwDb com um Objetivo de Serviço de DW400 e permite que o banco de dados aumente até um tamanho máximo de 10 TB.

```sql
CREATE DATABASE MySqlDwDb COLLATE SQL_Latin1_General_CP1_CI_AS (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB);
```

## <a name="create-a-database-with-sqlcmd"></a>Criar um banco de dados com sqlcmd
Como alternativa, você pode executar o mesmo comando com sqlcmd executando o seguinte em um prompt de comando.

```sql
sqlcmd -S <Server Name>.database.windows.net -I -U <User> -P <Password> -Q "CREATE DATABASE MySqlDwDb COLLATE SQL_Latin1_General_CP1_CI_AS (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB)"
```

O agrupamento padrão quando não especificado é COLLATE SQL_Latin1_General_CP1_CI_AS.  O `MAXSIZE` pode ter entre 250 GB e 240 TB.  O `SERVICE_OBJECTIVE` pode estar entre DW100 e DW2000 [DWU][DWU].  Para obter uma lista de todos os valores válidos, confira a documentação do MSDN para [CREATE DATABASE][CREATE DATABASE].  MAXSIZE e SERVICE_OBJECTIVE podem ser alterados com um comando T-SQL [ALTER DATABASE][ALTER DATABASE].  O agrupamento de um banco de dados não pode ser alterado após a criação.   É necessário ter cuidado ao alterar o SERVICE_OBJECTIVE, pois alterar um DWU causa uma reinicialização dos serviços, cancelando todas as consultas em trânsito.  A alteração de MAXSIZE não reinicia os serviços, pois é apenas uma operação de metadados.

## <a name="next-steps"></a>Próximas etapas
Após o provisionamento do SQL Data Warehouse, você pode [carregar dados de exemplo][load sample data] ou conferir como [desenvolvê-los][develop], [carregar][load] ou [migrar][migrate].

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md
[how to create a SQL Data Warehouse from the Azure portal]: sql-data-warehouse-get-started-provision.md
[Query Azure SQL Data Warehouse (Visual Studio)]: sql-data-warehouse-query-visual-studio.md
[migrate]: sql-data-warehouse-overview-migrate.md
[develop]: sql-data-warehouse-overview-develop.md
[load]: sql-data-warehouse-overview-load.md
[load sample data]: sql-data-warehouse-load-sample-databases.md
[Create an Azure SQL database with the Azure Portal]: ../sql-database/sql-database-get-started.md
[Create an Azure SQL database with PowerShell]: ../sql-database/sql-database-create-and-configure-database-powershell
[how to create a resource group]: ../azure-resource-manager/resource-group-template-deploy-portal.md#create-resource-group
[Installing Visual Studio and SSDT]: sql-data-warehouse-install-visual-studio.md
[sqlcmd]: sql-data-warehouse-get-started-connect-sqlcmd.md

<!--MSDN references-->
[CREATE DATABASE]: https://msdn.microsoft.com/library/mt204021.aspx
[ALTER DATABASE]: https://msdn.microsoft.com/library/mt204042.aspx
[SSMS]: https://msdn.microsoft.com/library/mt238290.aspx

<!--Other Web references-->
[SQL Data Warehouse pricing]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Azure Free Trial]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[MSDN Azure Credits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F
