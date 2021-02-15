---
title: Restaurar um pool SQL dedicado de um backup geográfico
description: Guia de instruções para a restauração geográfica de um pool SQL dedicado no Azure Synapse Analytics
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/13/2020
ms.author: joanpo
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 4683bd84873506483209f4a0eb3751a1b163ed48
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96449864"
---
# <a name="geo-restore-a-dedicated-sql-pool-in-azure-synapse-analytics"></a>Restauração geográfica de um pool SQL dedicado no Azure Synapse Analytics

Neste artigo, você aprende a restaurar seu pool SQL dedicado (anteriormente conhecido como SQL DW) de um backup geográfico por meio do portal do Azure e do PowerShell.

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

**Verifique sua capacidade de DTU.** Cada pool de SQL dedicado (anteriormente conhecido como SQL DW) é hospedado por um [SQL Server lógico](../../azure-sql/database/logical-servers.md) (por exemplo, MyServer.Database.Windows.net) que tem uma cota de DTU padrão. Verifique se o SQL Server tem uma cota de DTU suficiente restante para o banco de dados que está sendo restaurado. Para saber como calcular a DTU necessária ou para solicitar mais DTU, veja [Solicitar uma alteração de cota de DTU](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="restore-from-an-azure-geographical-region-through-powershell"></a>Restaurar de uma região geográfica do Azure por meio do PowerShell

Para restaurar de um backup geográfico, use o cmdlet [Get-AzSqlDatabaseGeoBackup](/powershell/module/az.sql/get-azsqldatabasegeobackup?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) e [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) .

> [!NOTE]
> Você pode executar uma restauração geográfica para Gen2! Para fazer isso, especifique um ServiceObjectiveName de Gen2 (por exemplo, DW1000 **c**) como parâmetro opcional.
>

1. Antes de começar, certifique-se de [instalar Azure PowerShell](/powershell/azure/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
2. Abra o PowerShell.
3. Conecte-se à sua conta do Azure e liste todas as assinaturas associadas à sua conta.
4. Selecione a assinatura que contém a data warehouse a ser restaurada.
5. Obtenha o data warehouse que você deseja recuperar.
6. Crie a solicitação de recuperação para o data warehouse.
7. Verifique o status do data warehouse restaurado geograficamente.
8. Para configurar o data warehouse após a conclusão da restauração, veja [Configurar o banco de dados após a recuperação]( ../../sql-database/sql-database-disaster-recovery.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#configure-your-database-after-recovery).

```Powershell
$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$TargetResourceGroupName="<YourTargetResourceGroupName>" # Restore to a different server.
$TargetServerName="<YourtargetServerNameWithoutURLSuffixSeeNote>"  
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"
$TargetServiceObjective="<YourTargetServiceObjective-DWXXXc>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName
Get-AzureSqlDatabase -ServerName $ServerName

# Get the data warehouse you want to recover
$GeoBackup = Get-AzSqlDatabaseGeoBackup -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Recover data warehouse
$GeoRestoredDatabase = Restore-AzSqlDatabase –FromGeoBackup -ResourceGroupName $TargetResourceGroupName -ServerName $TargetServerName -TargetDatabaseName $NewDatabaseName –ResourceId $GeoBackup.ResourceID -ServiceObjectiveName $TargetServiceObjective

# Verify that the geo-restored data warehouse is online
$GeoRestoredDatabase.status
```

O banco de dados recuperado será habilitado para TDE se o banco de dados de origem for habilitado para TDE.

## <a name="restore-from-an-azure-geographical-region-through-azure-portal"></a>Restaurar de uma região geográfica do Azure por meio de portal do Azure

Siga as etapas descritas abaixo para restaurar um pool SQL dedicado (anteriormente conhecido como SQL DW) de um backup geográfico:

1. Entre em sua conta do [portal do Azure](https://portal.azure.com/) .
1. Pesquise **pools de SQL dedicados (anteriormente conhecido como SQL DW)**.

   ![Novo DW 2](./media/sql-data-warehouse-restore-from-geo-backup/georestore-new.png)

1. Clique em Adicionar e preencha as informações solicitadas na guia **noções básicas** e clique em **Avançar: configurações adicionais**.

   ![Noções básicas](./media/sql-data-warehouse-restore-from-geo-backup/georestore-dw-1.png)

1. Para usar o parâmetro de **dados existente** , selecione **backup** e selecione o backup apropriado nas opções de rolagem para baixo. Clique em **Revisar + Criar**.

   ![backup](./media/sql-data-warehouse-restore-from-geo-backup/georestore-select.png)

1. Depois que o data warehouse tiver sido restaurado, verifique se o **status** está online.

## <a name="next-steps"></a>Próximas etapas

- [Restaurar um pool SQL dedicado existente (anteriormente conhecido como SQL DW)](sql-data-warehouse-restore-active-paused-dw.md)
- [Restaurar um pool SQL dedicado excluído (antigo SQL DW)](sql-data-warehouse-restore-deleted-dw.md)
