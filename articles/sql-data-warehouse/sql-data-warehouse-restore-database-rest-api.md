---
title: Restaurar um SQL Data Warehouse do Azure - API REST | Microsoft Docs
description: Restaurar um SQL Data Warehouse do Azure usando APIs REST.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: daf013472e5fa533912920e4c14a552905b5d333
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60935680"
---
# <a name="restore-an-azure-sql-data-warehouse-with-rest-apis"></a>Restaurar um SQL Data Warehouse do Azure com APIs REST
Restaurar um SQL Data Warehouse do Azure usando APIs REST.

## <a name="before-you-begin"></a>Antes de começar
**Verifique sua capacidade de DTU.** Cada SQL Data Warehouse é hospedado por um servidor SQL lógico (por exemplo, myserver.database.windows.net) que tem uma [cota de DTU](../sql-database/sql-database-what-is-a-dtu.md) padrão.  Antes de restaurar um SQL Data Warehouse, verifique se o SQL Server tem cota de DTU suficiente restante para o banco de dados que está sendo restaurado. Para solicitar mais DTU, você pode [Criar um tíquete de suporte](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="restore-an-active-or-paused-data-warehouse"></a>Restaurar um data warehouse ativo ou pausado
Para restaurar um data warehouse:

1. Obtenha a lista de pontos de restauração do banco de dados usando a operação de obtenção de pontos de restauração de banco de dados.
2. Comece sua restauração usando a operação [Criar solicitação de restauração do banco de dados](https://msdn.microsoft.com/library/azure/dn509571.aspx) .
3. Acompanhe o status de sua restauração usando a operação [Status de operações do banco de dados](https://msdn.microsoft.com/library/azure/dn720371.aspx) .

> [!NOTE]
> Depois que a restauração estiver concluída, você poderá configurar o data warehouse recuperado seguindo [Configurar o banco de dados após a recuperação](../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery).
> 
> 

## <a name="restore-a-deleted-data-warehouse"></a>Restaurar um data warehouse excluído
Para restaurar um data warehouse excluído:

1. Liste todos os data warehouse excluídos restauráveis usando a operação [Listar bancos de dados descartados restauráveis](https://msdn.microsoft.com/library/azure/dn509562.aspx).
2. Obtenha os detalhes do data warehouse excluído que deseja restaurar usando a operação [Obter banco de dados descartados restauráveis][Obter banco de dados descartados restauráveis].
3. Comece sua restauração usando a operação [Criar solicitação de restauração do banco de dados](https://msdn.microsoft.com/library/azure/dn509571.aspx) .
4. Acompanhe o status de sua restauração usando a operação [Status de operações do banco de dados](https://msdn.microsoft.com/library/azure/dn720371.aspx) .

> [!NOTE]
> Para configurar o data warehouse após a conclusão da restauração, veja [Configurar o banco de dados após a recuperação](../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery).
> 
> 

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre os recursos de continuidade dos negócios das edições do Banco de Dados SQL do Azure, leia a [Visão geral da continuidade dos negócios do Banco de Dados SQL do Azure](../sql-database/sql-database-business-continuity.md).
