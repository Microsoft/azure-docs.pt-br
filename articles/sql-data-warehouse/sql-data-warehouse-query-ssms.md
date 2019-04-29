---
title: Conectar-se ao SQL Data Warehouse do Azure – SSMS | Microsoft Docs
description: Use o SQL Server Management Studio (SSMS) para conectar e consultar o SQL Data Warehouse.
services: sql-data-warehouse
author: KavithaJonnakuti
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: consume
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: 809802bc34a6cdc45f4b018d35895939e4b8f667
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61476450"
---
# <a name="connect-to-sql-data-warehouse-with-sql-server-management-studio-ssms"></a>Conectar-se ao SQL Data Warehouse com o SSMS (SQL Server Management Studio)
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

Use o SQL Server Management Studio (SSMS) para conectar e consultar o SQL Data Warehouse. 

## <a name="prerequisites"></a>Pré-requisitos
Para usar este tutorial, você precisará de:

* Um SQL Data Warehouse existente. Para criar um, confira [Criar um SQL Data Warehouse][Create a SQL Data Warehouse].
* SSMS (SQL Server Management Studio) instalado. [Instale o SSMS][Install SSMS] gratuitamente se você ainda não o tiver.
* O nome de servidor SQL totalmente qualificado. Para encontrar isso, confira [Conectar-se ao SQL Data Warehouse][Connect to SQL Data Warehouse].

## <a name="1-connect-to-your-sql-data-warehouse"></a>1. Conectar-se ao SQL Data Warehouse
1. Abra o SSMS.
2. Abrir Pesquisador de Objetos. Para fazer isso, selecione **Arquivo** > **Conectar Pesquisador de Objetos**.
   
    ![Pesquisador de Objetos do SQL Server][1]
3. Preencha os campos na janela Conectar ao Servidor.
   
    ![Conectar-se ao servidor][2]
   
   * **Nome do servidor**. Insira o **nome do servidor** identificado anteriormente.
   * **Autenticação**. Selecione **Autenticação do SQL Server** ou **Autenticação Integrada do Active Directory**.
   * **Nome de Usuário** e **Senha**. Insira o nome de usuário e senha se a Autenticação do SQL Server foi selecionada acima.
   * Clique em **Conectar**.
4. Para explorar, expanda seu servidor do SQL Azure. Você pode exibir os bancos de dados associados ao servidor. Expanda o AdventureWorksDW para ver as tabelas no banco de dados de exemplo.
   
    ![Explorar o AdventureWorksWeb][3]

## <a name="2-run-a-sample-query"></a>2. Executar uma consulta de exemplo
Agora que uma conexão foi estabelecida com o banco de dados, escreveremos uma consulta.

1. Clique com o botão direito do mouse em seu banco de dados no Gerenciador de Objetos do SQL Server.
2. Selecione **Nova Consulta**. Uma nova janela de consulta é aberta.
   
    ![Nova Consulta][4]
3. Copie esta consulta TSQL na janela de consulta:
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. Execute a consulta. Para fazer isso, clique em `Execute` ou use o atalho a seguir: `F5`.
   
    ![Executar consulta][5]
5. Examine os resultados da consulta. Neste exemplo, a tabela FactInternetSales tem 60398 linhas.
   
    ![Resultados da consulta][6]

## <a name="next-steps"></a>Próximas etapas
Agora que você pode se conectar e consultar, tente [visualizar os dados com o PowerBI][visualizing the data with PowerBI].

Para configurar seu ambiente para a autenticação do Azure Active Directory, confira [Autenticar no SQL Data Warehouse][Authenticate to SQL Data Warehouse].

<!--Arcticles-->
[Connect to SQL Data Warehouse]: sql-data-warehouse-connect-overview.md
[Create a SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md
[Authenticate to SQL Data Warehouse]: sql-data-warehouse-authentication.md
[visualizing the data with PowerBI]: sql-data-warehouse-get-started-visualize-with-power-bi.md 

<!--Other-->
[Azure portal]: https://portal.azure.com
[Install SSMS]: https://msdn.microsoft.com/library/hh213248.aspx


<!--Image references-->

[1]: media/sql-data-warehouse-query-ssms/connect-object-explorer.png
[2]: media/sql-data-warehouse-query-ssms/connect-object-explorer1.png
[3]: media/sql-data-warehouse-query-ssms/explore-tables.png
[4]: media/sql-data-warehouse-query-ssms/new-query.png
[5]: media/sql-data-warehouse-query-ssms/execute-query.png
[6]: media/sql-data-warehouse-query-ssms/results.png
