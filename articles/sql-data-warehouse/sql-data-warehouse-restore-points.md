---
title: Pontos de restauração definidos pelo usuário | Microsoft Docs
description: Como criar um ponto de restauração SQL Data Warehouse do Azure.
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 07/03/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.openlocfilehash: bb5a267d1c7058193f1d7c8ed4087f5c0ebab267
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68426584"
---
# <a name="user-defined-restore-points"></a>Pontos de restauração definidos pelo usuário

Neste artigo, você aprenderá a criar um novo ponto de restauração definido pelo usuário para o Azure SQL Data Warehouse usando o PowerShell e o portal do Azure.

## <a name="create-user-defined-restore-points-through-powershell"></a>Criar pontos de restauração definidos pelo usuário por meio do PowerShell

Para criar um ponto de restauração definido pelo usuário, use o cmdlet [New-AzSqlDatabaseRestorePoint][New-AzSqlDatabaseRestorePoint] do PowerShell.

1. Antes de começar, certifique-se de [instalar Azure PowerShell][Install Azure PowerShell].
2. Abra o PowerShell.
3. Conecte-se à sua conta do Azure e liste todas as assinaturas associadas à sua conta.
4. Selecione a assinatura que contém o banco de dados a ser restaurado.
5. Crie um ponto de restauração para uma cópia imediata do seu data warehouse.

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$Label = "<YourRestorePointLabel>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName

# Create a restore point of the original database
New-AzSqlDatabaseRestorePoint -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -RestorePointLabel $Label

```

6. Consulte a lista de todos os pontos de restauração existentes.

```Powershell
# List all restore points
Get-AzSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName
```

## <a name="create-user-defined-restore-points-through-the-azure-portal"></a>Criar pontos de restauração definidos pelo usuário por meio do portal do Azure

Os pontos de restauração definidos pelo usuário também podem ser criados por meio de portal do Azure.

1. Entre em sua conta do [portal do Azure][Azure portal] .

2. Navegue até o SQL Data Warehouse para o qual você deseja criar um ponto de restauração.

3. Selecione **visão geral** no painel esquerdo, selecione **+ novo ponto de restauração**. Se o botão novo ponto de restauração não estiver habilitado, verifique se o data warehouse não está em pausa.

    ![Novo Ponto de Restauração](./media/sql-data-warehouse-restore-points/creating-restore-point-01.png)

4. Especifique um nome para o ponto de restauração definido pelo usuário e clique em **aplicar**. Os pontos de restauração definidos pelo usuário têm um período de retenção padrão de sete dias.

    ![Nome do ponto de restauração](./media/sql-data-warehouse-restore-points/creating-restore-point-11.png)

## <a name="next-steps"></a>Próximas etapas

- [Restaurar um data warehouse existente][Restore an existing data warehouse]
- [Restaurar um data warehouse excluído][Restore a deleted data warehouse]
- [Restaurar de um data warehouse de backup geográfico][Restore from a geo-backup data warehouse]

<!--Image references-->

<!--Article references-->
[Azure SQL Database business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Request a DTU quota change]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[Install Azure PowerShell]: https://docs.microsoft.com/powershell/azure/overview
[Overview]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[PowerShelldoc]:./sql-data-warehouse-restore-points.md#create-user-defined-restore-points-through-powershell
[Restore an existing data warehouse]:./sql-data-warehouse-restore-active-paused-dw.md
[Restore a deleted data warehouse]:./sql-data-warehouse-restore-deleted-dw.md
[Restore from a geo-backup data warehouse]:./sql-data-warehouse-restore-from-geo-backup.md
<!--MSDN references-->
[New-AzSqlDatabaseRestorePoint]: https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabaserestorepoint?view=azps-2.4.0

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
