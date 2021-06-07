---
title: Restaurar um pool SQL dedicado existente (anteriormente conhecido como SQL DW)
description: Guia de instruções para restaurar um pool SQL dedicado existente no Azure Synapse Analytics.
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
ms.openlocfilehash: 2ce552a13592c9d26ef70575f98b0b76ecc454ff
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97591983"
---
# <a name="restore-an-existing-dedicated-sql-pool-formerly-sql-dw"></a>Restaurar um pool SQL dedicado existente (anteriormente conhecido como SQL DW)

Neste artigo, você aprenderá a restaurar um pool SQL dedicado existente (anteriormente conhecido como SQL DW) usando o portal do Azure e o PowerShell.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

**Verifique sua capacidade de DTU.** Cada pool é hospedado por um [SQL Server lógico](../../azure-sql/database/logical-servers.md) (por exemplo, MyServer.Database.Windows.net) que tem uma cota de DTU padrão. Verifique se o servidor tem a cota de DTU suficiente restante para o banco de dados que está sendo restaurado. Para saber como calcular a DTU necessária ou para solicitar mais DTU, veja [Solicitar uma alteração de cota de DTU](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="before-you-begin"></a>Antes de começar

1. Certifique-se de [instalar Azure PowerShell](/powershell/azure/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
2. Ter um ponto de restauração existente do qual você deseja restaurar. Se você quiser criar uma nova restauração, consulte [o tutorial para criar um novo ponto de restauração definido pelo usuário](sql-data-warehouse-restore-points.md).

## <a name="restore-an-existing-dedicated-sql-pool-formerly-sql-dw-through-powershell"></a>Restaurar um pool SQL dedicado existente (anteriormente conhecido como SQL DW) por meio do PowerShell

Para restaurar um pool SQL dedicado existente (anteriormente conhecido como SQL DW) de um ponto de restauração, use o cmdlet do PowerShell [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) .

1. Abra o PowerShell.

2. Conecte-se à sua conta do Azure e liste todas as assinaturas associadas à sua conta.

3. Selecione a assinatura que contém o banco de dados a ser restaurado.

4. Liste os pontos de restauração para o pool SQL dedicado (anteriormente conhecido como SQL DW).

5. Selecione o ponto de restauração desejado usando o RestorePointCreationDate.

6. Restaure o pool de SQL dedicado (anteriormente conhecido como SQL DW) para o ponto de restauração desejado usando o cmdlet [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) do PowerShell.

    1. Para restaurar o pool de SQL dedicado (anteriormente conhecido como SQL DW) para um servidor diferente, certifique-se de especificar o outro nome de servidor.  Esse servidor também pode estar em um grupo de recursos e região diferentes.
    2. Para restaurar para uma assinatura diferente, use o botão ' mover ' para mover o servidor para outra assinatura.

7. Verifique se o pool SQL dedicado restaurado (anteriormente conhecido como SQL DW) está online.

8. Depois que a restauração for concluída, você poderá configurar seu pool SQL dedicado recuperado (anteriormente conhecido como SQL DW) seguindo [configurar seu banco de dados após a recuperação](../../azure-sql/database/disaster-recovery-guidance.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#configure-your-database-after-recovery).

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
#$TargetResourceGroupName="<YourTargetResourceGroupName>" # uncomment to restore to a different server.
#$TargetServerName="<YourtargetServerNameWithoutURLSuffixSeeNote>"  
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName

# Or list all restore points
Get-AzSqlDatabaseRestorePoint -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Get the specific database to restore
$Database = Get-AzSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Pick desired restore point using RestorePointCreationDate "xx/xx/xxxx xx:xx:xx xx"
$PointInTime="<RestorePointCreationDate>"  

# Restore database from a restore point
$RestoredDatabase = Restore-AzSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Use the following command to restore to a different server
#$TargetResourceGroupName = $Database.ResourceGroupName # for restoring to different server in same resourcegroup 
#$RestoredDatabase = Restore-AzSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $TargetResourceGroupName -ServerName $TargetServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Verify the status of restored database
$RestoredDatabase.status

```

## <a name="restore-an-existing-dedicated-sql-pool-formerly-sql-dw-through-the-azure-portal"></a>Restaurar um pool SQL dedicado existente (anteriormente conhecido como SQL DW) por meio do portal do Azure

1. Entre no [portal do Azure](https://portal.azure.com/).
2. Navegue até o dedicado do qual você deseja restaurar.
3. Na parte superior da folha Visão Geral, selecione **Restaurar**.

    ![ Visão Geral de Restauração](./media/sql-data-warehouse-restore-active-paused-dw/restoring-01.png)

4. Selecione **Pontos de Restauração Automática** ou **Pontos de Restauração Definido Pelo Usuário**. Se o pool do SQL dedicado (anteriormente conhecido como SQL DW) não tiver pontos de restauração automáticos, aguarde algumas horas ou crie um ponto de restauração definido pelo usuário antes da restauração. Para User-Defined pontos de restauração, selecione um existente ou crie um novo. Para o **servidor**, você pode escolher um servidor em um grupo de recursos e uma região diferentes ou criar um novo. Depois de fornecer todos os parâmetros, clique em **revisar + restaurar**.

    ![Pontos de restauração automática](./media/sql-data-warehouse-restore-active-paused-dw/restoring-11.png)

## <a name="next-steps"></a>Próximas etapas

- [Restaurar um pool SQL dedicado excluído (antigo SQL DW)](sql-data-warehouse-restore-deleted-dw.md)
- [Restaurar de um pool SQL dedicado de backup geográfico (antigo SQL DW)](sql-data-warehouse-restore-from-geo-backup.md)
