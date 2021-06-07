---
title: Desabilitar os backups geográficos
description: Guia de instruções para desabilitar backups geográficos para um pool SQL dedicado (anteriormente conhecido como SQL DW) no Azure Synapse Analytics
services: synapse-analytics
author: joannapea
manager: igorstan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 01/06/2021
ms.author: joanpo
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 395d5f0697138155b0bb0c629461aada9e9c18c6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98739079"
---
# <a name="disable-geo-backups-for-a-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Desabilitar backups geográficos para um pool de SQL dedicado (anteriormente conhecido como SQL DW) no Azure Synapse Analytics

Neste artigo, você aprende a desabilitar os backups geográficos para seu pool SQL dedicado (anteriormente conhecido como SQL DW) portal do Azure.

## <a name="disable-geo-backups-through-azure-portal"></a>Desabilitar backups geográficos por meio de portal do Azure

Siga estas etapas para desabilitar os backups geográficos para seu pool SQL dedicado (anteriormente conhecido como SQL DW):

> [!NOTE]
> Se você desabilitar os backups geográficos, não será mais possível recuperar seu pool SQL dedicado (anteriormente conhecido como SQL DW) para outra região do Azure. 
>

1. Entre em sua conta do [portal do Azure](https://portal.azure.com/) .
1. Selecione o recurso pool de SQL dedicado (anteriormente conhecido como SQL DW) para o qual você gostaria de desabilitar backups geográficos. 
1. Em **configurações** no painel de navegação esquerdo, selecione **política de backup geográfico**.

   ![Desabilitar backup geográfico](./media/sql-data-warehouse-restore-from-geo-backup/disable-geo-backup-1.png)

1. Para desabilitar backups geográficos, selecione **desabilitado**. 

   ![Backup geográfico desabilitado](./media/sql-data-warehouse-restore-from-geo-backup/disable-geo-backup-2.png)

1. Selecione *salvar* para garantir que suas configurações sejam salvas. 

   ![Salvar configurações de backup geográfico](./media/sql-data-warehouse-restore-from-geo-backup/disable-geo-backup-3.png)

## <a name="next-steps"></a>Próximas etapas

- [Restaurar um pool SQL dedicado existente (anteriormente conhecido como SQL DW)](sql-data-warehouse-restore-active-paused-dw.md)
- [Restaurar um pool SQL dedicado excluído (antigo SQL DW)](sql-data-warehouse-restore-deleted-dw.md)
