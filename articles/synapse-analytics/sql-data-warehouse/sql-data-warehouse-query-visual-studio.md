---
title: Conectar-se ao pool do SQL dedicado (anteriormente conhecido como SQL DW) com VSTS
description: Consulte pool de SQL dedicado (anteriormente conhecido como SQL DW) no Azure Synapse Analytics com o Visual Studio.
services: synapse-analytics
author: gaursa
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 08/15/2019
ms.author: gaursa
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 0baf2396b7c5af103f0b3aa223d0bccf725babbe
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104584135"
---
# <a name="connect-to-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics-with-visual-studio-and-ssdt"></a>Conectar-se ao pool do SQL dedicado (anteriormente conhecido como SQL DW) no Azure Synapse Analytics com o Visual Studio e o SSDT

> [!div class="op_single_selector"]
> * [Azure Data Studio](../sql/get-started-azure-data-studio.md)
> * [Power BI](/power-bi/connect-data/service-azure-sql-data-warehouse-with-direct-connect)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](../sql/get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

Use o Visual Studio para consultar um pool de SQL dedicado (anteriormente conhecido como SQL DW) no Azure Synapse em apenas alguns minutos. Esse método usa a extensão SSDT (SQL Server Data Tools) no Visual Studio 2019. 

## <a name="prerequisites"></a>Pré-requisitos
Para usar este tutorial, você precisará de:

* Um pool SQL dedicado existente (anteriormente conhecido como SQL DW). Para criar um, consulte [criar um pool de SQL dedicado (anteriormente conhecido como SQL DW)](create-data-warehouse-portal.md).
* SSDT para Visual Studio. Se você tiver o Visual Studio, provavelmente já terá o SSDT para o Visual Studio. Para obter instruções e opções de instalação, consulte [Instalar o Visual Studio e o SSDT](sql-data-warehouse-install-visual-studio.md).
* O nome de servidor SQL totalmente qualificado. Para encontrar essas informações, consulte [conectar-se a um pool SQL dedicado (anteriormente conhecido como SQL DW)](sql-data-warehouse-connect-overview.md).

## <a name="1-connect-to-your-dedicated-sql-pool-formerly-sql-dw"></a>1. Conecte-se ao seu pool SQL dedicado (antigo SQL DW)
1. Abra o Visual Studio 2019.
2. Abra pesquisador de objetos do SQL Server selecionando **Exibir**  >  **pesquisador de objetos do SQL Server**.
   
    ![Pesquisador de Objetos do SQL Server](./media/sql-data-warehouse-query-visual-studio/open-ssdt.png)
3. Clique no ícone **Adicionar SQL Server** .
   
    ![Adicionar SQL Server](./media/sql-data-warehouse-query-visual-studio/add-server.png)
4. Preencha os campos na janela Conectar ao Servidor.
   
    ![Conectar-se ao servidor](./media/sql-data-warehouse-query-visual-studio/connection-dialog.png)
   
   * **Nome do servidor**. Insira o **nome do servidor** identificado anteriormente.
   * **Autenticação**. Selecione **Autenticação do SQL Server** ou **Autenticação Integrada do Active Directory**.
   * **Nome de usuário** e **senha**. Insira o nome de usuário e senha se a Autenticação do SQL Server foi selecionada acima.
   * Clique em **Conectar**.
5. Para explorar, expanda seu servidor do SQL Azure. Você pode exibir os bancos de dados associados ao servidor. Expanda o AdventureWorksDW para ver as tabelas no banco de dados de exemplo.
   
    ![Explorar o AdventureWorksWeb](./media/sql-data-warehouse-query-visual-studio/explore-sample.png)

## <a name="2-run-a-sample-query"></a>2. Executar uma consulta de exemplo
Agora que uma conexão foi estabelecida com o banco de dados, escreveremos uma consulta.

1. Clique com o botão direito do mouse em seu banco de dados no Gerenciador de Objetos do SQL Server.
2. Selecione **Nova Consulta**. Uma janela de nova consulta é aberta.
   
    ![Nova Consulta](./media/sql-data-warehouse-query-visual-studio/new-query2.png)
3. Copie a seguinte consulta T-SQL na janela de consulta:
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. Execute a consulta clicando na seta verde ou use o seguinte atalho: `CTRL`+`SHIFT`+`E`.
   
    ![Executar consulta](./media/sql-data-warehouse-query-visual-studio/run-query.png)
5. Examine os resultados da consulta. Neste exemplo, a tabela FactInternetSales tem 60398 linhas.
   
    ![Resultados da consulta](./media/sql-data-warehouse-query-visual-studio/query-results.png)

## <a name="next-steps"></a>Próximas etapas
Agora que você pode se conectar e consultar, tente [visualizar os dados com o Power BI](/power-bi/connect-data/service-azure-sql-data-warehouse-with-direct-connect).

Para configurar seu ambiente para Azure Active Directory autenticação, consulte [autenticar no pool SQL dedicado (anteriormente conhecido como SQL DW)](sql-data-warehouse-authentication.md).