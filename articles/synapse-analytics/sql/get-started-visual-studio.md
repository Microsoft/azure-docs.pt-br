---
title: Conectar e consultar o SQL Synapse com o Visual Studio e o SSDT
description: Use o Visual Studio para consultar o pool dedicado do SQL usando o Azure Synapse Analytics.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: ef8e2a3d1a6b78e8f2b6b9a900ed2485c1a4a5d7
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96451599"
---
# <a name="connect-to-synapse-sql-with-visual-studio-and-ssdt"></a>Conectar-se ao SQL do Synapse com o Visual Studio e o SSDT

> [!div class="op_single_selector"]
> * [Azure Data Studio](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Visual Studio](get-started-visual-studio.md)
> * [sqlcmd](get-started-connect-sqlcmd.md) 
> * [SSMS](get-started-ssms.md)
> 
> 

Use o Visual Studio para consultar o pool dedicado do SQL usando o Azure Synapse Analytics. Esse método usa a extensão SSDT (SQL Server Data Tools) no Visual Studio 2019. 

> [!NOTE]
> O pool SQL sem servidor não tem suporte do SSDT.

## <a name="prerequisites"></a>Pré-requisitos

Para usar este tutorial, você precisa ter os seguintes componentes:

* Um pool SQL dedicado existente. Se você não tiver um, consulte [criar um pool de SQL dedicado](../sql-data-warehouse/create-data-warehouse-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) para concluir este pré-requisito.
* SSDT para Visual Studio. Se você tiver o Visual Studio, você provavelmente já tem esse componente. Para obter instruções e opções de instalação, consulte [Instalar o Visual Studio e o SSDT](../sql-data-warehouse/sql-data-warehouse-install-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
* O nome de servidor SQL totalmente qualificado. Para localizar esse nome de servidor, consulte [conectar-se a um pool SQL dedicado](connect-overview.md).

## <a name="1-connect-to-a-dedicated-sql-pool"></a>1. conectar-se a um pool SQL dedicado
1. Abra o Visual Studio 2019.
2. Abra o Pesquisador de Objetos do SQL Server selecionando **Exibir** no  > **Explorador de Objetos do SQL Server**.
   
    ![Pesquisador de Objetos do SQL Server](./media/get-started-visual-studio/open-ssdt.png)
3. Clique no ícone **Adicionar SQL Server** .
   
    ![Adicionar SQL Server](./media/get-started-visual-studio/add-server.png)
4. Preencha os campos na janela Conectar ao Servidor.
   
    ![Conectar-se ao servidor](./media/get-started-visual-studio/connection-dialog.png)
   
   * **Nome do servidor**: Insira o **nome do servidor** identificado anteriormente.
   * **Autenticação**: Selecione **Autenticação do SQL Server** ou **Autenticação Integrada do Active Directory**:
   * **Nome de Usuário** e **Senha**: Insira seu nome de usuário e senha se a Autenticação do SQL Server foi selecionada acima.
   * Clique em **Conectar**.
5. Para explorar, expanda seu servidor do SQL Azure. Você pode exibir os bancos de dados associados ao servidor. Expanda o AdventureWorksDW para ver as tabelas no banco de dados de exemplo.
   
    ![Explorar o AdventureWorksWeb](./media/get-started-visual-studio/explore-sample.png)

## <a name="2-run-a-sample-query"></a>2. Executar uma consulta de exemplo
Agora que uma conexão foi estabelecida com o banco de dados, você escreverá uma consulta.

1. Clique com o botão direito do mouse em seu banco de dados no Gerenciador de Objetos do SQL Server.
2. Selecione **Nova Consulta**. Uma janela de nova consulta é aberta.
   
    ![Nova Consulta](./media/get-started-visual-studio/new-query2.png)
3. Copie a seguinte consulta T-SQL na janela de consulta:
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. Execute a consulta clicando na seta verde ou use o seguinte atalho: `CTRL`+`SHIFT`+`E`.
   
    ![Executar consulta](./media/get-started-visual-studio/run-query.png)
5. Examine os resultados da consulta. Neste exemplo, a tabela FactInternetSales tem 60398 linhas.
   
    ![Resultados da consulta](./media/get-started-visual-studio/query-results.png)

## <a name="next-steps"></a>Próximas etapas
Agora que você pode se conectar e consultar, tente [visualizar os dados com o Power BI](get-started-power-bi-professional.md).
Para configurar seu ambiente para Azure Active Directory autenticação, consulte [autenticar no pool SQL dedicado](../sql-data-warehouse/sql-data-warehouse-authentication.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
 