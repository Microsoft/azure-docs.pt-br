---
title: Restaurar SQL Data Warehouse do Azure (Portal do Azure) | Microsoft Docs
description: Tarefas do Portal do Azure para a restauração de um SQL Data Warehouse do Azure.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 2d59235b067d9571bc8b64c33799431be6489502
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61421258"
---
# <a name="restore-azure-sql-data-warehouse-portal"></a>Restaurar SQL Data Warehouse do Azure (Portal)
> [!div class="op_single_selector"]
> * [Visão geral][Overview]
> * [Portal][Portal]
> * [PowerShell][PowerShell]
> * [REST][REST]
> 
> 
> Neste artigo, você aprenderá como restaurar o SQL Data Warehouse do Azure usando o Portal do Azure.

## <a name="before-you-begin"></a>Antes de começar
**Verifique sua capacidade de DTU.** Cada instância do SQL Data Warehouse é hospedada por um SQL Server (por exemplo, myserver.database.windows.net) que tem uma cota DTU (Unidade de Transmissão de Dados) padrão. Antes de restaurar o SQL Data Warehouse, verifique se o SQL Server tem cota de DTU suficiente restante para o banco de dados que você está restaurando. Para saber como calcular a cota de DTU ou para solicitar mais DTUs, veja [Solicitar uma alteração de cota de DTU][Request a DTU quota change].

## <a name="restore-an-active-or-paused-database"></a>Restaurar um banco de dados ativo ou pausado
Para restaurar um banco de dados:

1. Entre no [Portal do Azure][Azure portal].
2. No painel esquerdo, selecione **Procurar** e, em seguida, selecione **Servidores SQL**.

    ![Selecione Procurar > servidores SQL](./media/sql-data-warehouse-restore-database-portal/01-browse-for-sql-server.png)
3. Localize o servidor e, em seguida, selecione-o.

    ![Selecione seu servidor](./media/sql-data-warehouse-restore-database-portal/01-select-server.png)
4. Localize a instância do SQL Data Warehouse da qual você quer restaurar e selecione-a.

    ![Selecione a instância do SQL Data Warehouse para restaurar](./media/sql-data-warehouse-restore-database-portal/01-select-active-dw.png)
5. Na parte superior da folha Data Warehouse, selecione **Restaurar**.

    ![Selecione Restaurar](./media/sql-data-warehouse-restore-database-portal/01-select-restore-from-active.png)
6. Especifique um novo **Nome de banco de dados**.
7. Selecione o **Ponto de restauração** mais recente.

   Certifique-se de escolher o ponto de restauração mais recente. Como os pontos de restauração são mostrados em Hora Universal Coordenada (UTC), a opção padrão poderá não ser o ponto de restauração mais recente.

      ![Selecione um ponto de restauração](./media/sql-data-warehouse-restore-database-portal/01-restore-blade-from-active.png)
8. Selecione **OK**.
9. O processo de restauração de banco de dados será iniciado e você poderá usar as **NOTIFICAÇÕES** para monitorar o processo.

> [!NOTE]
> Depois que a restauração estiver concluída, você poderá configurar o banco de dados recuperado seguindo [Configurar o banco de dados após a recuperação][Configure your database after recovery].
>
>

## <a name="restore-a-deleted-database"></a>Restaurar um banco de dados excluído
Para restaurar um banco de dados excluído:

1. Entre no [Portal do Azure][Azure portal].
2. No painel esquerdo, selecione **Procurar** e, em seguida, selecione **Servidores SQL**.

    ![Selecione Procurar > servidores SQL](./media/sql-data-warehouse-restore-database-portal/01-browse-for-sql-server.png)
3. Localize o servidor e, em seguida, selecione-o.

    ![Selecione seu servidor](./media/sql-data-warehouse-restore-database-portal/02-select-server.png)
4. Role para baixo até a seção **Operações** na folha do seu servidor.
5. Selecione o bloco **Bancos de Dados excluídos**.

    ![Selecione o bloco Bancos de Dados excluídos](./media/sql-data-warehouse-restore-database-portal/02-select-deleted-dws.png)
6. Selecione o banco de dados excluído que você deseja restaurar.

    ![Selecionar um banco de dados para ser restaurado](./media/sql-data-warehouse-restore-database-portal/02-select-deleted-dw.png)
7. Especifique um novo **Nome de banco de dados**.

    ![Adicione um nome para o banco de dados](./media/sql-data-warehouse-restore-database-portal/02-restore-blade-from-deleted.png)
8. Selecione **OK**.
9. O processo de restauração de banco de dados será iniciado e você poderá usar as **NOTIFICAÇÕES** para monitorar o processo.

> [!NOTE]
> Para configurar o banco de dados após a conclusão da restauração, consulte [Configurar o banco de dados após a recuperação][Configure your database after recovery].
>
>

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre os recursos de continuidade dos negócios das edições do Banco de Dados SQL do Azure, leia a [Visão geral da continuidade dos negócios do Banco de Dados SQL do Azure][Azure SQL Database business continuity overview].

<!--Image references-->

<!--Article references-->
[Azure SQL Database business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Overview]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[Request a DTU quota change]: ./sql-data-warehouse-get-started-create-support-ticket.md

<!--MSDN references-->

<!--Blog references-->

<!--Other Web references-->
[Azure portal]: https://portal.azure.com/
