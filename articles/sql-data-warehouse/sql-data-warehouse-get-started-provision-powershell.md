---
title: Criar SQL Data Warehouse usando o PowerShell | Microsoft Docs
description: Criar SQL Data Warehouse usando o PowerShell
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: jhubbard
editor: 
ms.assetid: 97434863-7938-4129-8949-5a119f5949e3
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: create
ms.date: 10/31/2016
ms.author: elbutter;barbkess
ms.openlocfilehash: a763f1c600c1a3f37cb565a8eb7db3c3f27dcf75
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-sql-data-warehouse-using-powershell"></a>Criar um SQL Data Warehouse usando o PowerShell
> [!div class="op_single_selector"]
> * [Portal do Azure](sql-data-warehouse-get-started-provision.md)
> * [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
> * [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)
>
>

Este artigo mostra como criar um SQL Data Warehouse usando o PowerShell.

## <a name="prerequisites"></a>Pré-requisitos
Para começar, você precisa do seguinte:

* **Conta do Azure**: visite [Avaliação gratuita do Azure][Azure Free Trial] ou [Créditos do Azure no MSDN][MSDN Azure Credits] para criar uma conta.
* **Servidor SQL do Azure**: veja [Criar um banco de dados SQL do Azure no portal do Azure][Create an Azure SQL database in the Azure Portal] ou [Criar um banco de dados SQL do Azure com o PowerShell][Create an Azure SQL database with PowerShell] para obter mais detalhes.
* **Grupo de recursos**: use o mesmo grupo de recursos do servidor SQL do Azure ou veja [como criar um grupo de recursos](../azure-resource-manager/resource-group-portal.md).
* **PowerShell versão 1.0.3 ou superior**: você pode verificar a versão executando **Get-Module -ListAvailable -Name Azure**.  A versão mais recente pode ser instalada pelo [Microsoft Web Platform Installer][Microsoft Web Platform Installer].  Para obter mais informações sobre como instalar a versão mais recente, consulte [Como instalar e configurar o Azure PowerShell][How to install and configure Azure PowerShell].

> [!NOTE]
> A criação de um SQL Data Warehouse pode resultar em um novo serviço faturável.  Confira [Preços do SQL Data Warehouse][SQL Data Warehouse pricing] para obter mais detalhes sobre preços.
>
>

## <a name="create-a-sql-data-warehouse"></a>Criar um SQL Data Warehouse
1. Abra o Windows PowerShell.
2. Execute este cmdlet para fazer logon no Gerenciador de Recursos do Azure.

    ```Powershell
    Login-AzureRmAccount
    ```
3. Selecione a assinatura que você deseja usar para a sessão atual.

    ```Powershell
    Get-AzureRmSubscription    -SubscriptionName "MySubscription" | Select-AzureRmSubscription
    ```
4. Criar banco de dados. Este exemplo cria um banco de dados denominado "mynewsqldw", com um nível de objetivo de serviço "DW400", no servidor denominado "sqldwserver1", que está no grupo de recursos denominado "mywesteuroperesgp1".

   ```Powershell
   New-AzureRmSqlDatabase -RequestedServiceObjectiveName "DW400" -DatabaseName "mynewsqldw" -ServerName "sqldwserver1" -ResourceGroupName "mywesteuroperesgp1" -Edition "DataWarehouse" -CollationName "SQL_Latin1_General_CP1_CI_AS" -MaxSizeBytes 10995116277760
   ```

Os parâmetros requeridos são:

* **RequestedServiceObjectiveName**: a quantidade de [DWU][DWU] solicitada.  Os valores com suporte são: DW100, DW200, DW300, DW400, DW500, DW600, DW1000, DW1200, DW1500, DW2000, DW3000 e DW6000.
* **DatabaseName**: o nome do SQL Data Warehouse que você está criando.
* **ServerName**: o nome do servidor que você está usando para a criação (deve ser V12).
* **ResourceGroupName**: o grupo de recursos que você está usando.  Para encontrar os grupos de recursos na sua assinatura, use Get-AzureResource.
* **Edition**: deve ser "DataWarehouse" para criar um SQL Data Warehouse.

Os parâmetros opcionais são:

* **CollationName**: o agrupamento padrão, se não especificado, é SQL_Latin1_General_CP1_CI_AS.  O agrupamento não pode ser alterado em um banco de dados.
* **MaxSizeBytes**: o tamanho máximo padrão de um banco de dados é de 10 GB.

Para obter mais detalhes sobre as opções do parâmetro, consulte [New-AzureRmSqlDatabase][New-AzureRmSqlDatabase] e [Criar Banco de Dados (SQL Data Warehouse do Azure)][Create Database (Azure SQL Data Warehouse)].

## <a name="next-steps"></a>Próximas etapas
Após o provisionamento do SQL Data Warehouse ter terminado, você pode tentar [carregar os dados de amostra][loading sample data] ou verificar como [desenvolver][develop], [carregar][load] ou [migrar][migrate].

Se você estiver interessado em saber mais sobre como gerenciar o SQL Data Warehouse de forma programática, confira nosso artigo sobre como usar os [Cmdlets do PowerShell e APIs REST][PowerShell cmdlets and REST APIs].

<!--Image references-->

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md
[migrate]: ./sql-data-warehouse-overview-migrate.md
[develop]: ./sql-data-warehouse-overview-develop.md
[load]: ./sql-data-warehouse-load-with-bcp.md
[loading sample data]: ./sql-data-warehouse-load-sample-databases.md
[PowerShell cmdlets and REST APIs]: ./sql-data-warehouse-reference-powershell-cmdlets.md
[firewall rules]: ../sql-database-configure-firewall-settings.md

[How to install and configure Azure PowerShell]: /powershell/azureps-cmdlets-docs
[how to create a SQL Data Warehouse from the Azure Portal]: ./sql-data-warehouse-get-started-provision.md
[Create an Azure SQL database in the Azure Portal]: ../sql-database/sql-database-get-started.md
[Create an Azure SQL database with PowerShell]: ../sql-database/sql-database-get-started-powershell.md
[how to create a resource group]: ../azure-resource-manager/resource-group-template-deploy-portal.md#create-resource-group

<!--MSDN references-->
[MSDN]: https://msdn.microsoft.com/library/azure/dn546722.aspx
[New-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619339.aspx
[Create Database (Azure SQL Data Warehouse)]: https://msdn.microsoft.com/library/mt204021.aspx

<!--Other Web references-->
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps
[SQL Data Warehouse pricing]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Azure Free Trial]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[MSDN Azure Credits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F
