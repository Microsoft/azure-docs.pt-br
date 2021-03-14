---
title: 'Banco de dados SQL do Azure: gerenciar retenção de backup de longo prazo'
description: Saiba como armazenar e restaurar backups automatizados para o banco de dados SQL do Azure no armazenamento do Azure (por até 10 anos) usando o portal do Azure e o PowerShell
services: sql-database
ms.service: sql-db-mi
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 12/16/2020
ms.openlocfilehash: fad19d360f7c476ba71a9bbe00b58387b92f8ac4
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101690542"
---
# <a name="manage-azure-sql-database-long-term-backup-retention"></a>Gerenciar a retenção de backup de longo prazo do Banco de Dados SQL do Azure
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Com o banco de dados SQL do Azure, você pode definir uma EPD (política de retenção de backup) de [longo prazo](long-term-retention-overview.md) para reter automaticamente os backups em contêineres de armazenamento de BLOBs do Azure separados por até 10 anos. Em seguida, você pode recuperar um banco de dados usando esses backups pelo Portal do Azure ou o PowerShell. As políticas de retenção de longo prazo também têm suporte para o [Azure SQL instância gerenciada](../managed-instance/long-term-backup-retention-configure.md).

## <a name="using-the-azure-portal"></a>Usando o portal do Azure

As seções a seguir mostram como usar o portal do Azure para definir políticas de retenção de longo prazo, gerenciar backups de retenção de longo prazo disponíveis e restaurar de um backup disponível.

### <a name="configure-long-term-retention-policies"></a>Configurar políticas de retenção de longo prazo

Você pode configurar o Banco de Dados SQL para [reter backups automatizados](long-term-retention-overview.md) por um período maior que o período de retenção da camada de serviço.

1. No portal do Azure, navegue até o servidor e, em seguida, selecione **backups**. Selecione a guia **políticas de retenção** para modificar as configurações de retenção de backup.

   ![experiência de políticas de retenção](./media/long-term-backup-retention-configure/ltr-policies-tab.png)

2. Na guia políticas de retenção, selecione os bancos de dados nos quais você deseja definir ou modificar políticas de retenção de backup de longo prazo. Os bancos de dados não selecionados não serão afetados.

   ![Selecionar Banco de dados para configurar políticas de retenção de backup](./media/long-term-backup-retention-configure/ltr-policies-tab-configure.png)

3. No painel **Configurar políticas** , especifique o período de retenção desejado para backups semanais, mensais ou anuais. Escolha um período de retenção de ' 0 ' para indicar que nenhuma retenção de backup de longo prazo deve ser definida.

   ![painel configurar políticas](./media/long-term-backup-retention-configure/ltr-configure-policies.png)

4. Selecione **aplicar** para aplicar as configurações de retenção escolhidas a todos os bancos de dados selecionados.

> [!IMPORTANT]
> Quando você habilita uma política de retenção de backup de longo prazo, pode levar até sete dias para o primeiro backup ficar visível e disponível para restauração. Para obter detalhes sobre o ritmo de backup de LTR, confira [Retenção de backup de longo prazo](long-term-retention-overview.md).

### <a name="view-backups-and-restore-from-a-backup"></a>Exibir os backups e restaurar de um backup

Exiba os backups que são retidos para um banco de dados específico com uma política EPD e restaure desses backups.

1. No portal do Azure, navegue até o servidor e, em seguida, selecione **backups**. Para exibir os backups EPD disponíveis para um banco de dados específico, selecione **gerenciar** na coluna backups LTR disponíveis. Um painel será exibido com uma lista dos backups LTR disponíveis para o banco de dados selecionado.

   ![experiência de backups disponíveis](./media/long-term-backup-retention-configure/ltr-available-backups-tab.png)

1. No painel **backups EPD disponíveis** que aparece, examine os backups disponíveis. Você pode selecionar um backup a ser restaurado ou excluído.

   ![Exibir backups EPD disponíveis](./media/long-term-backup-retention-configure/ltr-available-backups-manage.png)

1. Para restaurar de um backup EPD disponível, selecione o backup do qual você deseja restaurar e, em seguida, selecione **restaurar**.

   ![restaurar do backup EPD disponível](./media/long-term-backup-retention-configure/ltr-available-backups-restore.png)

1. Escolha um nome para o novo banco de dados e, em seguida, selecione **revisar + criar** para revisar os detalhes da sua restauração. Selecione **criar** para restaurar o banco de dados do backup escolhido.

   ![configurar detalhes da restauração](./media/long-term-backup-retention-configure/restore-ltr.png)

1. Na barra de ferramentas, selecione o ícone de notificação para exibir o status do trabalho de restauração.

   ![progresso do trabalho de restauração](./media/long-term-backup-retention-configure/restore-job-progress-long-term.png)

1. Quando o trabalho de restauração for concluído, abra a página **Bancos de dados SQL** para exibir o banco de dados recém-restaurado.

> [!NOTE]
> A partir daqui, você pode conectar o banco de dados restaurado usando o SQL Server Management Studio para executar as tarefas necessárias, tais como, [extrair um pouco de dados do banco de dados restaurado para copiar para o banco de dados existente ou excluir o banco de dados existente e renomear o banco de dados restaurado com o nome do banco de dados existente](recovery-using-backups.md#point-in-time-restore).

## <a name="using-powershell"></a>Usando o PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> O módulo Azure Resource Manager do PowerShell ainda tem suporte do Banco de Dados SQL do Azure, mas todo o desenvolvimento futuro é para o módulo Az.Sql. Para esses cmdlets, confira [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos.

As seções a seguir mostram como usar o PowerShell para configurar a retenção de backup de longo prazo, Exibir backups no armazenamento do Azure e restaurar de um backup no armazenamento do Azure.

### <a name="azure-roles-to-manage-long-term-retention"></a>Funções do Azure para gerenciar a retenção de longo prazo

Para **Get-AzSqlDatabaseLongTermRetentionBackup** e **Restore-AzSqlDatabase**, você precisará de uma das seguintes regras:

- Função Proprietário da assinatura ou
- Função Colaborador do SQL Server ou
- Função personalizada com as seguintes permissões:

   Microsoft.Sql/locations/longTermRetentionBackups/read  Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionBackups/read  Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/read

Para **Remove-AzSqlDatabaseLongTermRetentionBackup**, você precisará ter uma das seguintes funções:

- Função Proprietário da assinatura ou
- Função personalizada com a seguinte permissão:

   Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/delete

> [!NOTE]
> A função Colaborador do SQL Server não tem permissão para excluir backups de LTR.

As permissões do RBAC do Azure podem ser concedidas em escopo de *assinatura* ou *grupo de recursos* . No entanto, para acessar backups de LTR que pertencem a um servidor descartado, a permissão deve ser concedida no escopo de *assinatura* desse servidor.

- Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/delete

### <a name="create-an-ltr-policy"></a>Criar uma política LTR

```powershell
# get the SQL server
$subId = "<subscriptionId>"
$serverName = "<serverName>"
$resourceGroup = "<resourceGroupName>"
$dbName = "<databaseName>"

Connect-AzAccount
Select-AzSubscription -SubscriptionId $subId

$server = Get-AzSqlServer -ServerName $serverName -ResourceGroupName $resourceGroup

# create LTR policy with WeeklyRetention = 12 weeks. MonthlyRetention and YearlyRetention = 0 by default.
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup -WeeklyRetention P12W

# create LTR policy with WeeklyRetention = 12 weeks, YearlyRetention = 5 years and WeekOfYear = 16 (week of April 15). MonthlyRetention = 0 by default.
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup -WeeklyRetention P12W -YearlyRetention P5Y -WeekOfYear 16
```

### <a name="view-ltr-policies"></a>Exibir políticas LTR

Este exemplo mostra como listar as políticas LTR dentro de um servidor

```powershell
# get all LTR policies within a server
$ltrPolicies = Get-AzSqlDatabase -ResourceGroupName $resourceGroup -ServerName $serverName | `
    Get-AzSqlDatabaseLongTermRetentionPolicy

# get the LTR policy of a specific database
$ltrPolicies = Get-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup
```

### <a name="clear-an-ltr-policy"></a>Limpar uma política LTR

Este exemplo mostra como limpar uma política LTR de um banco de dados

```powershell
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup -RemovePolicy
```

### <a name="view-ltr-backups"></a>Exibir backups LTR

Este exemplo mostra como listar os backups LTR dentro de um servidor.

```powershell
# get the list of all LTR backups in a specific Azure region
# backups are grouped by the logical database id, within each group they are ordered by the timestamp, the earliest backup first
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location

# get the list of LTR backups from the Azure region under the named server
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName

# get the LTR backups for a specific database from the Azure region under the named server
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName -DatabaseName $dbName

# list LTR backups only from live databases (you have option to choose All/Live/Deleted)
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -DatabaseState Live

# only list the latest LTR backup for each database
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName -OnlyLatestPerDatabase
```

### <a name="delete-ltr-backups"></a>Excluir backups LTR

Este exemplo mostra como excluir um backup LTR da lista de backups.

```powershell
# remove the earliest backup
$ltrBackup = $ltrBackups[0]
Remove-AzSqlDatabaseLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId
```

> [!IMPORTANT]
> A exclusão do backup LTR é irreversível. Para excluir um backup de LTR depois que o servidor tiver sido excluído, você deverá ter a permissão de escopo de Assinatura. Você pode configurar notificações sobre cada exclusão no Azure Monitor filtrando a operação "Exclui um backup de retenção de longo prazo". O log de atividades contém informações sobre quem fez a solicitação e quando. Confira [Criar alertas do log de atividades](../../azure-monitor/alerts/alerts-activity-log.md) para obter instruções detalhadas.

### <a name="restore-from-ltr-backups"></a>Restaurar a partir de backups LTR

Este exemplo mostra como restaurar a partir de um backup LTR. Observe que essa interface não foi alterada, mas o parâmetro de ID de recurso agora requer a ID de recurso de backup EPD.

```powershell
# restore a specific LTR backup as an P1 database on the server $serverName of the resource group $resourceGroup
Restore-AzSqlDatabase -FromLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId -ServerName $serverName -ResourceGroupName $resourceGroup `
    -TargetDatabaseName $dbName -ServiceObjectiveName P1
```

> [!IMPORTANT]
> Para restaurar a partir de um backup EPD após a exclusão do servidor ou do grupo de recursos, você deve ter permissões com escopo para a assinatura do servidor e essa assinatura deve estar ativa. Você também deve omitir o parâmetro -ResourceGroupName opcional.

> [!NOTE]
> A partir daqui, você pode conectar o banco de dados restaurado usando o SQL Server Management Studio para executar as tarefas necessárias, tais como, extrair um pouco de dados do banco de dados restaurado para copiar para o banco de dados existente ou excluir o banco de dados existente e renomear o banco de dados restaurado com o nome do banco de dados existente. Confira [recuperação pontual](recovery-using-backups.md#point-in-time-restore).

## <a name="limitations"></a>Limitações
- Ao restaurar de um backup EPD, a propriedade de escala de leitura é desabilitada. Para habilitar, leia escala no banco de dados restaurado, atualize o banco de dados depois que ele tiver sido criado.
- Você precisa especificar o objetivo de nível de serviço de destino ao restaurar de um backup EPD, que foi criado quando o banco de dados estava em um pool elástico. 

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre backups automáticos gerados pelo serviço, veja [backups automáticos](automated-backups-overview.md)
- Para saber mais sobre a retenção de backup de longo prazo, consulte [retenção de backup de longo prazo](long-term-retention-overview.md)
