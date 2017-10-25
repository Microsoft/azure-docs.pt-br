---
title: Criar um SQL Data Warehouse no Portal do Azure | Microsoft Docs
description: Saiba como criar um Azure SQL Data Warehouse no Portal do Azure
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: jhubbard
editor: 
tags: azure-sql-data-warehouse
ms.assetid: 552e496e-d560-419c-9996-6bbc80c521cb
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: create
ms.date: 10/31/2016
ms.author: elbutter;barbkess
ms.openlocfilehash: 24ed2d8bad3090e378acf2a42fb909dee0a8517b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-azure-sql-data-warehouse"></a>Criar um Azure SQL Data Warehouse
> [!div class="op_single_selector"]
> * [Portal do Azure](sql-data-warehouse-get-started-provision.md)
> * [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
> * [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)
>
>

Este tutorial usa o Portal do Azure para criar um SQL Data Warehouse que contém um exemplo de banco de dados AdventureWorksDW.

## <a name="prerequisites"></a>Pré-requisitos
Para começar, você precisa do seguinte:

* **Conta do Azure**: visite [Avaliação gratuita do Azure][Azure Free Trial] ou [Créditos do Azure no MSDN][MSDN Azure Credits] para criar uma conta.
* **Servidor SQL do Azure**: veja [Criar um banco de dados SQL com o portal do Azure][Create an Azure SQL database in the Azure portal] para obter mais detalhes.

> [!NOTE]
> A criação de um SQL Data Warehouse pode resultar em um novo serviço faturável.  Confira [Preços do SQL Data Warehouse][SQL Data Warehouse pricing] para obter mais detalhes.
>
>

## <a name="create-a-sql-data-warehouse"></a>Criar um SQL Data Warehouse
1. Entre no [Portal do Azure](https://portal.azure.com).
2. Clique em **+ Novo** > **Bancos de dados** > **SQL Data Warehouse**.

    ![Criação](./media/sql-data-warehouse-get-started-provision/create-sample.gif)
3. Na folha **SQL Data Warehouse** , preencha as informações necessárias e, em seguida, pressione ‘Criar’ para criar.

    ![Criar banco de dados](./media/sql-data-warehouse-get-started-provision/create-database.png)

   * **Servidor**: recomendamos que você selecione seu servidor primeiro.  
   * **Nome do banco de dados**: o nome usado para referenciar o SQL Data Warehouse.  Esse nome tem que ser exclusivo do servidor.
   * **Desempenho**: é recomendável começar com 400 [DWUs][DWU]. Você pode mover o controle deslizante para a esquerda ou direita para ajustar o desempenho de seu data warehouse, ou ampliar ou reduzir após a criação.  Para saber mais sobre DWUs, veja nossa documentação sobre [dimensionamento](sql-data-warehouse-manage-compute-overview.md) ou nossa [página de preços][SQL Data Warehouse pricing].
   * **Assinatura**: selecione a [assinatura] na qual esse SQL Data Warehouse será cobrado.
   * **Grupo de recursos**: os [grupos de recursos][Resource group] são contêineres projetados para ajudar você a gerenciar uma coleção de recursos do Azure. Saiba mais sobre [grupos de recursos](../azure-resource-manager/resource-group-overview.md).
   * **Selecionar origem**: clique em **Selecionar origem** > **Exemplo**. O Azure preenche automaticamente a opção **Selecionar exemplo** com AdventureWorksDW.

   > [!NOTE]
   > O agrupamento padrão para um SQL Data Warehouse é SQL_Latin1_General_CP1_CI_AS. Se for necessário um agrupamento diferente, [T-SQL][T-SQL] poderá ser usado para criar o banco de dados com um agrupamento diferente.
   >
   >

1. Clique em **Criar** para criar seu SQL Data Warehouse.
2. Aguarde alguns minutos. Quando o data warehouse estiver pronto, retorne para o [Portal do Azure](https://portal.azure.com). Você pode encontrar o SQL Data Warehouse em seu painel, relacionado sob seus Bancos de Dados SQL ou no grupo de recursos que você usou para criá-lo.

    ![exibição de portal](./media/sql-data-warehouse-get-started-provision/database-portal-view.png)

[!INCLUDE [SQL Database create server](../../includes/sql-database-create-new-server-firewall-portal.md)]

## <a name="next-steps"></a>Próximas etapas
Agora que você criou um SQL Data Warehouse, está pronto para [Conectar](sql-data-warehouse-connect-overview.md) e começar a consultar.

Para carregar dados no SQL Data Warehouse, veja a [visão geral de carregamento](sql-data-warehouse-overview-load.md).

Se você estiver tentando migrar um banco de dados existente para o SQL Data Warehouse, confira a [Visão geral de migração](sql-data-warehouse-overview-migrate.md) ou use o [Utilitário de migração](sql-data-warehouse-migrate-migration-utility.md).

As regras de firewall também podem ser configuradas usando o Transact-SQL. Para saber mais, confira [sp_set_firewall_rule][sp_set_firewall_rule] e [sp_set_database_firewall_rule][sp_set_database_firewall_rule].

Também é uma ótima ideia dar uma olhada nas [práticas recomendadas][Best practices].

<!--Article references-->
[Create an Azure SQL database in the Azure portal]: ../sql-database/sql-database-get-started.md
[Create an Azure SQL database with PowerShell]: ../sql-database/sql-database-create-and-configure-database-powershell
[resource groups]: ../azure-resource-manager/resource-group-template-deploy-portal.md
[Best practices]: sql-data-warehouse-best-practices.md
[DWU]: sql-data-warehouse-overview-what-is.md
[assinatura]: ../azure-glossary-cloud-terminology.md#subscription
[resource group]: ../azure-glossary-cloud-terminology.md#resource-group
[T-SQL]: ./sql-data-warehouse-get-started-create-database-tsql.md

<!--MSDN references-->
[sp_set_firewall_rule]: https://msdn.microsoft.com/library/dn270017.aspx
[sp_set_database_firewall_rule]: https://msdn.microsoft.com/library/dn270010.aspx

<!--Other Web references-->
[SQL Data Warehouse pricing]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Azure Free Trial]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[MSDN Azure Credits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F
