---
title: Restaurar uma SQL Data Warehouse do Azure de um backup geográfico | Microsoft Docs
description: Guia de instruções para a restauração geográfica de uma SQL Data Warehouse do Azure.
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 07/12/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.openlocfilehash: 768646522c2589c302fd7a58031d4ebdb7fcdc12
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68426649"
---
# <a name="geo-restore-azure-sql-data-warehouse"></a>SQL Data Warehouse do Azure de restauração geográfica

Neste artigo, você aprende a restaurar seu data warehouse de um backup geográfico por meio do portal do Azure e do PowerShell.

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

**Verifique sua capacidade de DTU.** Cada SQL Data Warehouse é hospedada por um SQL Server (por exemplo, myserver.database.windows.net) que tem uma cota de DTU padrão. Verifique se o SQL Server tem uma cota de DTU suficiente restante para o banco de dados que está sendo restaurado. Para saber como calcular a DTU necessária ou para solicitar mais DTU, veja [Solicitar uma alteração de cota de DTU][Request a DTU quota change].

## <a name="restore-from-an-azure-geographical-region-through-powershell"></a>Restaurar de uma região geográfica do Azure por meio do PowerShell

Para restaurar de um backup geográfico, use o cmdlet [Get-AzSqlDatabaseGeoBackup][Get-AzSqlDatabaseGeoBackup] and [Restore-AzSqlDatabase][Restore-AzSqlDatabase] .

> [!NOTE]
> Você pode executar uma restauração geográfica para Gen2! Para fazer isso, especifique um ServiceObjectiveName de Gen2 (por exemplo, DW1000**c**) como parâmetro opcional.
>

1. Antes de começar, certifique-se de [instalar Azure PowerShell][Install Azure PowerShell].
2. Abra o PowerShell.
2. Conecte-se à sua conta do Azure e liste todas as assinaturas associadas à sua conta.
3. Selecione a assinatura que contém a data warehouse a ser restaurada.
4. Obtenha o data warehouse que você deseja recuperar.
5. Crie a solicitação de recuperação para o data warehouse.
6. Verifique o status do data warehouse restaurado geograficamente.
7. Para configurar o data warehouse após a conclusão da restauração, veja [Configurar o banco de dados após a recuperação][Configure your database after recovery].

```Powershell
$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$TargetResourceGroupName="<YourTargetResourceGroupName>" # Restore to a different logical server.
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

Siga as etapas descritas abaixo para restaurar uma SQL Data Warehouse do Azure de um backup geográfico:

1. Entre em sua conta do [portal do Azure][Azure portal] .
1. Clique em **+ criar um recurso** e pesquise SQL data warehouse e clique em **criar**.

    ![Novo DW](./media/sql-data-warehouse-restore-from-geo-backup/georestore-new.png)
1. Preencha as informações solicitadas na guia **noções básicas** e clique **em Avançar: Configurações**adicionais.

    ![Noções Básicas](./media/sql-data-warehouse-restore-from-geo-backup/georestore-dw-1.png)
1. Para usar o parâmetro de **dados existente** , selecione **backup** e selecione o backup apropriado nas opções de rolagem para baixo. Clique em revisar **+ criar**.
 
   ![backup](./media/sql-data-warehouse-restore-from-geo-backup/georestore-select.png)
2. Depois que o data warehouse tiver sido restaurado, verifique se o **status** está online.

## <a name="next-steps"></a>Próximas etapas
- [Restaurar um data warehouse existente][Restore an existing data warehouse]
- [Restaurar um data warehouse excluído][Restore a deleted data warehouse]

<!--Image references-->

<!--Article references-->
[Install Azure PowerShell]: https://docs.microsoft.com/powershell/azure/overview
[Azure SQL Database business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Request a DTU quota change]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[How to install and configure Azure PowerShell]: /powershell/azureps-cmdlets-docs
[Overview]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[Restore an existing data warehouse]:./sql-data-warehouse-restore-active-paused-dw.md
[Restore a deleted data warehouse]:./sql-data-warehouse-restore-deleted-dw.md
[Restore from a geo-backup data warehouse]:./sql-data-warehouse-restore-from-geo-backup.md


<!--MSDN references-->
[Restore-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase
[Get-AzSqlDatabaseGeoBackup]: https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasegeobackup

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
