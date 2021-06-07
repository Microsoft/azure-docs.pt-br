---
title: Conectar-se ao pool do SQL dedicado (anteriormente conhecido como SQL DW) com o SSMS
description: Use o SQL Server Management Studio (SSMS) para se conectar e consultar um pool SQL dedicado (anteriormente conhecido como SQL DW) no Azure Synapse Analytics.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: b142c88b0003281237dad125080930c0dd4d3bee
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98673596"
---
# <a name="connect-to-a-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics-with-sql-server-management-studio-ssms"></a>Conectar-se a um pool SQL dedicado (anteriormente conhecido como SQL DW) no Azure Synapse Analytics com o SQL Server Management Studio (SSMS)

> [!div class="op_single_selector"]
>
> * [Power BI](/power-bi/connect-data/service-azure-sql-data-warehouse-with-direct-connect)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md)
> * [SSMS](sql-data-warehouse-query-ssms.md)

Use o SQL Server Management Studio (SSMS) para se conectar e consultar um pool SQL dedicado (anteriormente conhecido como SQL DW).

## <a name="prerequisites"></a>Pré-requisitos

Para usar este tutorial, você precisará de:

* Um pool SQL dedicado existente. Para criar um, consulte [criar um pool de SQL dedicado (anteriormente conhecido como SQL DW)](create-data-warehouse-portal.md).
* SSMS (SQL Server Management Studio) instalado. [Baixe o SSMS](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) gratuitamente se você ainda não o tiver.
* O nome de servidor SQL totalmente qualificado. Para encontrar essas informações, consulte [pool dedicado do SQL (antigo SQL DW)](sql-data-warehouse-connect-overview.md).

## <a name="1-connect-to-your-dedicated-sql-pool-formerly-sql-dw"></a>1. Conecte-se ao seu pool SQL dedicado (antigo SQL DW)

1. Abra o SSMS.
2. Abra o pesquisador de objetos selecionando **arquivo** conectar pesquisador de  >  **objetos**.

    ![Pesquisador de Objetos do SQL Server](./media/sql-data-warehouse-query-ssms/connect-object-explorer.png)
3. Preencha os campos na janela Conectar ao Servidor.

   ![Conectar-se ao servidor](./media/sql-data-warehouse-query-ssms/connect-object-explorer1.png)

   * **Nome do servidor**. Insira o **nome do servidor** identificado anteriormente.
   * **Autenticação**. Selecione **Autenticação do SQL Server** ou **Autenticação Integrada do Active Directory**.
   * **Nome de usuário** e **senha**. Insira o nome de usuário e senha se a Autenticação do SQL Server foi selecionada acima.
   * Clique em **Conectar**.
4. Para explorar, expanda seu servidor do SQL Azure. Você pode exibir os bancos de dados associados ao servidor. Expanda o AdventureWorksDW para ver as tabelas no banco de dados de exemplo.

   ![Explorar o AdventureWorksWeb](./media/sql-data-warehouse-query-ssms/explore-tables.png)

## <a name="2-run-a-sample-query"></a>2. Executar uma consulta de exemplo

Agora que uma conexão foi estabelecida com o banco de dados, escreveremos uma consulta.

1. Clique com o botão direito do mouse em seu banco de dados no Gerenciador de Objetos do SQL Server.
2. Selecione **Nova Consulta**. Uma janela de nova consulta é aberta.

   ![Nova Consulta](./media/sql-data-warehouse-query-ssms/new-query.png)
3. Copie a seguinte consulta T-SQL na janela de consulta:

   ```sql
   SELECT COUNT(*) FROM dbo.FactInternetSales;
   ```

4. Execute a consulta clicando `Execute` ou use o seguinte atalho: `F5` .

   ![Executar consulta](./media/sql-data-warehouse-query-ssms/execute-query.png)
5. Examine os resultados da consulta. Neste exemplo, a tabela FactInternetSales tem 60398 linhas.

   ![Resultados da consulta](./media/sql-data-warehouse-query-ssms/results.png)

## <a name="next-steps"></a>Próximas etapas

Agora que você pode se conectar e consultar, tente [visualizar os dados com o Power BI](/power-bi/connect-data/service-azure-sql-data-warehouse-with-direct-connect). Para configurar seu ambiente para Azure Active Directory autenticação, consulte [autenticar no pool SQL dedicado](sql-data-warehouse-authentication.md).