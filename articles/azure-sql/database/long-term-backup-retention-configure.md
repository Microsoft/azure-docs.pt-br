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
ms.openlocfilehash: a0653f24eeb0a96c28714d00f1d943dfc7d336db
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2021
ms.locfileid: "99979684"
---
# <a name="manage-azure-sql-database-long-term-backup-retention"></a>Gerenciar a retenção de backup de longo prazo do Banco de Dados SQL do Azure
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

No banco de dados SQL do Azure, você pode configurar um banco de dados com uma EPD (política de retenção de backup) de [longo prazo](long-term-retention-overview.md) para reter automaticamente os backups de banco de dados em contêineres de armazenamento de BLOBs do Azure separados por até 10 anos. Em seguida, você pode recuperar um banco de dados usando esses backups pelo Portal do Azure ou o PowerShell. Você também pode configurar a retenção de longo prazo para um [SQL do Azure instância gerenciada](../managed-instance/long-term-backup-retention-configure.md) , mas ela está atualmente em visualização pública limitada.

## <a name="using-the-azure-portal"></a>Usando o portal do Azure

As seções a seguir mostram como usar o Portal do Azure para configurar a retenção de longo prazo, exibir os backups em retenção de longo prazo e restaurar backups da retenção de longo prazo.

### <a name="configure-long-term-retention-policies"></a>Configurar políticas de retenção de longo prazo

Você pode configurar o Banco de Dados SQL para [reter backups automatizados](long-term-retention-overview.md) por um período maior que o período de retenção da camada de serviço.

1. No portal do Azure, selecione sua instância do SQL Server e clique em **gerenciar backups**. Na guia **Configurar políticas**, marque a caixa de seleção do banco de dados no qual deseja definir ou modificar políticas de retenção de backup de longo prazo. Se a caixa de seleção ao lado do banco de dados não estiver marcada, as alterações da política não se aplicarão ao banco de dados.  

   ![link gerenciar backups](./media/long-term-backup-retention-configure/ltr-configure-ltr.png)

2. No painel **Configurar políticas**, selecione se deseja reter backups semanais, mensais ou anuais e especifique o período de retenção para cada um.

   ![configurar políticas](./media/long-term-backup-retention-configure/ltr-configure-policies.png)

3. Quando concluir, clique em **Aplicar**.

> [!IMPORTANT]
> Quando você habilita uma política de retenção de backup de longo prazo, pode levar até sete dias para o primeiro backup ficar visível e disponível para restauração. Para obter detalhes sobre o ritmo de backup de LTR, confira [Retenção de backup de longo prazo](long-term-retention-overview.md).

### <a name="view-backups-and-restore-from-a-backup"></a>Exibir os backups e restaurar de um backup

Exiba os backups que são retidos para um banco de dados específico com uma política EPD e restaure desses backups.

1. No portal do Azure, selecione o servidor e clique em **gerenciar backups**. Na guia **Backups disponíveis**, selecione o banco de dados para o qual você deseja ver os backups disponíveis.

   ![selecionar banco de dados](./media/long-term-backup-retention-configure/ltr-available-backups-select-database.png)

1. No painel **Backups disponíveis**, examine os backups disponíveis.

   ![exibir backups](./media/long-term-backup-retention-configure/ltr-available-backups.png)

1. Selecione o backup do qual você deseja restaurar e, em seguida, especifique o novo nome do banco de dados.

   ![restaurar](./media/long-term-backup-retention-configure/ltr-restore.png)

1. Clique em **OK** para restaurar o banco de dados do backup no armazenamento do Azure para o novo banco de dados.

1. Na barra de ferramentas, clique no ícone de notificação para exibir o status do trabalho de restauração.

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
> A exclusão do backup LTR é irreversível. Para excluir um backup EPD após a exclusão do servidor ou do grupo de recursos, você deve ter a permissão de escopo da assinatura. Você pode configurar notificações sobre cada exclusão no Azure Monitor filtrando a operação "Exclui um backup de retenção de longo prazo". O log de atividades contém informações sobre quem fez a solicitação e quando. Confira [Criar alertas do log de atividades](../../azure-monitor/platform/alerts-activity-log.md) para obter instruções detalhadas.

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
