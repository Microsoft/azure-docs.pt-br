---
title: 'Instância Gerenciada do SQL do Azure: retenção de backup de longo prazo'
description: Saiba como armazenar e restaurar backups automatizados em contêineres de armazenamento de BLOBs do Azure separados para um Azure SQL Instância Gerenciada usando o PowerShell.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: shkale-msft
ms.author: shkale
ms.reviewer: mathoma, sstein
ms.date: 02/25/2021
ms.openlocfilehash: f298f0f9d76750be932db79b5a08b6385e984f88
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102051983"
---
# <a name="manage-azure-sql-managed-instance-long-term-backup-retention-powershell"></a>Gerenciar o Azure SQL Instância Gerenciada retenção de backup de longo prazo (PowerShell)
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

No Azure SQL Instância Gerenciada, você pode configurar uma EPD (política de retenção de backup) de [longo prazo](../database/long-term-retention-overview.md) como um recurso de visualização pública. Isso permite que você mantenha automaticamente os backups de banco de dados em contêineres separados do armazenamento de BLOBs do Azure por até 10 anos. Em seguida, você pode recuperar um banco de dados usando os backups com o PowerShell.

   > [!IMPORTANT]
   > A EPD para instâncias gerenciadas está disponível atualmente em visualização pública nas regiões públicas do Azure. 

As seções a seguir mostram como usar o PowerShell para configurar a retenção de backup de longo prazo, exibir backups no armazenamento do SQL do Azure e restaurar a partir de um backup no armazenamento do SQL do Azure.


## <a name="using-the-azure-portal"></a>Usando o portal do Azure

As seções a seguir mostram como usar o portal do Azure para definir políticas de retenção de longo prazo, gerenciar backups de retenção de longo prazo disponíveis e restaurar de um backup disponível.

### <a name="configure-long-term-retention-policies"></a>Configurar políticas de retenção de longo prazo

Você pode configurar o SQL Instância Gerenciada para [reter backups automatizados](../database/long-term-retention-overview.md) por um período maior que o período de retenção para sua camada de serviço.

1. No portal do Azure, selecione sua instância gerenciada e, em seguida, clique em **backups**. Na guia **políticas de retenção** , selecione os bancos de dados nos quais você deseja definir ou modificar políticas de retenção de backup de longo prazo. As alterações não serão aplicadas a nenhum banco de dados deixado desmarcado. 

   ![link gerenciar backups](./media/long-term-backup-retention-configure/ltr-configure-ltr.png)

2. No painel **Configurar políticas** , especifique o período de retenção desejado para backups semanais, mensais ou anuais. Escolha um período de retenção de ' 0 ' para indicar que nenhuma retenção de backup de longo prazo deve ser definida.

   ![configurar políticas](./media/long-term-backup-retention-configure/ltr-configure-policies.png)

3. Quando concluir, clique em **Aplicar**.

> [!IMPORTANT]
> Quando você habilita uma política de retenção de backup de longo prazo, pode levar até sete dias para o primeiro backup ficar visível e disponível para restauração. Para obter detalhes sobre o ritmo de backup de LTR, confira [Retenção de backup de longo prazo](../database/long-term-retention-overview.md).

### <a name="view-backups-and-restore-from-a-backup"></a>Exibir os backups e restaurar de um backup

Exiba os backups que são retidos para um banco de dados específico com uma política EPD e restaure desses backups.

1. No portal do Azure, selecione sua instância gerenciada e, em seguida, clique em **backups**. Na guia **Backups disponíveis**, selecione o banco de dados para o qual você deseja ver os backups disponíveis. Clique em **Gerenciar**.

   ![selecionar banco de dados](./media/long-term-backup-retention-configure/ltr-available-backups-select-database.png)

1. No painel **gerenciar backups** , examine os backups disponíveis.

   ![exibir backups](./media/long-term-backup-retention-configure/ltr-available-backups.png)

1. Selecione o backup do qual deseja restaurar, clique em **restaurar** e, na página restaurar, especifique o novo nome do banco de dados. O backup e a fonte serão preenchidos previamente nesta página. 

   ![selecionar backup para restauração](./media/long-term-backup-retention-configure/ltr-available-backups-restore.png)
   
   ![restaurar](./media/long-term-backup-retention-configure/ltr-restore.png)

1. Clique em **examinar + criar** para revisar os detalhes da restauração. Em seguida, clique em **criar** para restaurar o banco de dados do backup escolhido.

1. Na barra de ferramentas, clique no ícone de notificação para exibir o status do trabalho de restauração.

   ![progresso do trabalho de restauração](./media/long-term-backup-retention-configure/restore-job-progress-long-term.png)

1. Quando o trabalho de restauração for concluído, abra a página de **visão geral do instância gerenciada** para exibir o banco de dados recém restaurado.

> [!NOTE]
> A partir daqui, você pode conectar o banco de dados restaurado usando o SQL Server Management Studio para executar as tarefas necessárias, tais como, [extrair um pouco de dados do banco de dados restaurado para copiar para o banco de dados existente ou excluir o banco de dados existente e renomear o banco de dados restaurado com o nome do banco de dados existente](../database/recovery-using-backups.md#point-in-time-restore).


## <a name="using-powershell"></a>Usando o PowerShell
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> O módulo Azure Resource Manager do PowerShell ainda é suportado pelo banco de dados SQL do Azure. no entanto, o desenvolvimento futuro será feito no módulo AZ. Sql. Para esses cmdlets, confira [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos.

As seções a seguir mostram como usar o PowerShell para configurar a retenção de backup de longo prazo, Exibir backups no armazenamento do Azure e restaurar de um backup no armazenamento do Azure.

### <a name="azure-rbac-roles-to-manage-long-term-retention"></a>Funções de RBAC do Azure para gerenciar a retenção de longo prazo

Para **Get-AzSqlInstanceDatabaseLongTermRetentionBackup** e **Restore-AzSqlInstanceDatabase**, você precisará de uma das regras a seguir:

- Função Proprietário da assinatura ou
- Função Colaborador da Instância Gerenciada ou
- Função personalizada com as seguintes permissões:
  - `Microsoft.Sql/locations/longTermRetentionManagedInstanceBackups/read`
  - `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionManagedInstanceBackups/read`
  - `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/read`

Para **Remove-AzSqlInstanceDatabaseLongTermRetentionBackup**, você precisará ter uma das seguintes funções:

- Função Proprietário da assinatura ou
- Função personalizada com a seguinte permissão:
  - `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/delete`

> [!NOTE]
> A função Colaborador de Instância Gerenciada não tem permissão para excluir backups de LTR.

As permissões do RBAC do Azure podem ser concedidas em escopo de *assinatura* ou *grupo de recursos* . No entanto, para acessar backups de LTR que pertencem a uma instância descartada, a permissão deve ser concedida no escopo de *assinatura* da instância.

- `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/delete`

### <a name="create-an-ltr-policy"></a>Criar uma política LTR

```powershell
# get the Managed Instance
$subId = "<subscriptionId>"
$instanceName = "<instanceName>"
$resourceGroup = "<resourceGroupName>"
$dbName = "<databaseName>"

Connect-AzAccount

Select-AzSubscription -SubscriptionId $subId

$instance = Get-AzSqlInstance -Name $instanceName -ResourceGroupName $resourceGroup

# create LTR policy with WeeklyRetention = 12 weeks. MonthlyRetention and YearlyRetention = 0 by default.
$LTRPolicy = @{
    InstanceName = $instanceName 
    DatabaseName = $dbName 
    ResourceGroupName = $resourceGroup 
    WeeklyRetention = 'P12W'
}
Set-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy @LTRPolicy

# create LTR policy with WeeklyRetention = 12 weeks, YearlyRetention = 5 years and WeekOfYear = 16 (week of April 15). MonthlyRetention = 0 by default.
$LTRPolicy = @{
    InstanceName = $instanceName 
    DatabaseName = $dbName 
    ResourceGroupName = $resourceGroup 
    WeeklyRetention = 'P12W' 
    YearlyRetention = 'P5Y' 
    WeekOfYear = '16'
}
Set-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy @LTRPolicy
```

### <a name="view-ltr-policies"></a>Exibir políticas LTR

Este exemplo mostra como listar as políticas EPD dentro de uma instância para um banco de dados individual

```powershell
# gets the current version of LTR policy for a database
$LTRPolicies = @{
    InstanceName = $instanceName 
    DatabaseName = $dbName 
    ResourceGroupName = $resourceGroup
}
Get-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy @LTRPolicy 
```

Este exemplo mostra como listar as políticas EPD para todos os bancos de dados em uma instância

```powershell
# gets the current version of LTR policy for all of the databases on an instance

$Databases = Get-AzSqlInstanceDatabase -ResourceGroupName $resourceGroup -InstanceName $instanceName

$LTRParams = @{
    InstanceName = $instanceName
    ResourceGroupName = $resourceGroup
}

foreach($database in $Databases.Name){
    Get-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy @LTRParams  -DatabaseName $database
 }
```

### <a name="clear-an-ltr-policy"></a>Limpar uma política LTR

Este exemplo mostra como limpar uma política LTR de um banco de dados

```powershell
# remove the LTR policy from a database
$LTRPolicy = @{
    InstanceName = $instanceName 
    DatabaseName = $dbName 
    ResourceGroupName = $resourceGroup 
    RemovePolicy = $true
}
Set-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy @LTRPolicy
```

### <a name="view-ltr-backups"></a>Exibir backups LTR

Este exemplo mostra como listar os backups de LTR dentro de uma instância.

```powershell

$instance = Get-AzSqlInstance -Name $instanceName -ResourceGroupName $resourceGroup

# get the list of all LTR backups in a specific Azure region
# backups are grouped by the logical database id, within each group they are ordered by the timestamp, the earliest backup first
Get-AzSqlInstanceDatabaseLongTermRetentionBackup -Location $instance.Location

# get the list of LTR backups from the Azure region under the given managed instance
$LTRBackupParam = @{
    Location = $instance.Location 
    InstanceName = $instanceName
}
Get-AzSqlInstanceDatabaseLongTermRetentionBackup @LTRBackupParam

# get the LTR backups for a specific database from the Azure region under the given managed instance
$LTRBackupParam = @{
    Location = $instance.Location 
    InstanceName = $instanceName
    DatabaseName = $dbName
}
Get-AzSqlInstanceDatabaseLongTermRetentionBackup @LTRBackupParam

# list LTR backups only from live databases (you have option to choose All/Live/Deleted)
$LTRBackupParam = @{
    Location = $instance.Location 
    DatabaseState = 'Live'
}
Get-AzSqlInstanceDatabaseLongTermRetentionBackup @LTRBackupParam

# only list the latest LTR backup for each database
$LTRBackupParam = @{
    Location = $instance.Location 
    InstanceName = $instanceName
    OnlyLatestPerDatabase = $true
}
Get-AzSqlInstanceDatabaseLongTermRetentionBackup @LTRBackupParam 
```

### <a name="delete-ltr-backups"></a>Excluir backups LTR

Este exemplo mostra como excluir um backup LTR da lista de backups.

```powershell
# remove the earliest backup
# get the LTR backups for a specific database from the Azure region under the given managed instance
$LTRBackupParam = @{
    Location = $instance.Location 
    InstanceName = $instanceName
    DatabaseName = $dbName
}
$ltrBackups = Get-AzSqlInstanceDatabaseLongTermRetentionBackup @LTRBackupParam
$ltrBackup = $ltrBackups[0]
Remove-AzSqlInstanceDatabaseLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId
```

> [!IMPORTANT]
> A exclusão do backup LTR é irreversível. Para excluir um backup de LTR depois que a instância tiver sido excluída, você deverá ter a permissão de escopo de Assinatura. Você pode configurar notificações sobre cada exclusão no Azure Monitor filtrando a operação "Exclui um backup de retenção de longo prazo". O log de atividades contém informações sobre quem fez a solicitação e quando. Confira [Criar alertas do log de atividades](../../azure-monitor/alerts/alerts-activity-log.md) para obter instruções detalhadas.

### <a name="restore-from-ltr-backups"></a>Restaurar a partir de backups LTR

Este exemplo mostra como restaurar a partir de um backup LTR. Observe que essa interface não foi alterada, mas o parâmetro de ID de recurso agora requer a ID de recurso de backup EPD.

```powershell
# restore a specific LTR backup as an P1 database on the instance $instanceName of the resource group $resourceGroup
$LTRBackupParam = @{
    Location = $instance.Location 
    InstanceName = $instanceName
    DatabaseName = $dbname
    OnlyLatestPerDatabase = $true
}
$ltrBackup = Get-AzSqlInstanceDatabaseLongTermRetentionBackup @LTRBackupParam 

$RestoreLTRParam = @{
    TargetInstanceName          = $instanceName 
    TargetResourceGroupName     = $resourceGroup 
    TargetInstanceDatabaseName  = $dbName
    FromLongTermRetentionBackup = $true
    ResourceId                  = $ltrBackup.ResourceId 
}
Restore-AzSqlInstanceDatabase @RestoreLTRParam
```

> [!IMPORTANT]
> Para restaurar de um backup de LTR após a instância ter sido excluída, você deverá ter permissões com escopo para a assinatura da instância e a assinatura deverá estar ativa. Você também deve omitir o parâmetro -ResourceGroupName opcional.

> [!NOTE]
> A partir daqui, você pode conectar o banco de dados restaurado usando o SQL Server Management Studio para executar as tarefas necessárias, tais como, extrair um pouco de dados do banco de dados restaurado para copiar para o banco de dados existente ou excluir o banco de dados existente e renomear o banco de dados restaurado com o nome do banco de dados existente. Confira [recuperação pontual](../database/recovery-using-backups.md#point-in-time-restore).

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre backups automáticos gerados pelo serviço, veja [backups automáticos](../database/automated-backups-overview.md)
- Para saber mais sobre a retenção de backup de longo prazo, consulte [retenção de backup de longo prazo](../database/long-term-retention-overview.md)
