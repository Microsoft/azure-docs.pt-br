---
title: 'PowerShell: migrar SQL Server para o SQL Instância Gerenciada online'
titleSuffix: Azure Database Migration Service
description: Saiba como migrar online de SQL Server para o SQL Instância Gerenciada do Azure usando Azure PowerShell e o serviço de migração de banco de dados do Azure.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: ''
ms.topic: how-to
ms.date: 12/16/2020
ms.openlocfilehash: 7f09db2e1f98d48e91dfea2642969ff4ca360967
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98697765"
---
# <a name="migrate-sql-server-to-sql-managed-instance-online-with-powershell--azure-database-migration-service"></a>Migrar SQL Server para o SQL Instância Gerenciada online com o PowerShell & serviço de migração de banco de dados do Azure

Neste artigo, você migrará online o banco de dados **Adventureworks2016** restaurado para uma instância local do SQL Server 2005 ou superior para uma instância gerenciada SQL do Azure sql usando Microsoft Azure PowerShell. Você pode migrar bancos de dados de uma instância SQL Server para uma Instância Gerenciada SQL usando o `Az.DataMigration` módulo no Microsoft Azure PowerShell.

Neste artigo, você aprenderá como:
> [!div class="checklist"]
>
> * Crie um grupos de recursos.
> * Crie uma instância do Serviço de Migração de Banco de Dados do Azure.
> * Crie um projeto de migração em uma instância do serviço de migração de banco de dados do Azure.
> * Execute a migração online.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Este artigo fornece etapas para uma migração online, mas também é possível migrar [offline](howto-sql-server-to-azure-sql-managed-instance-powershell-offline.md).


## <a name="prerequisites"></a>Pré-requisitos

Para concluir essas etapas, você precisa:

* [SQL Server 2016 ou superior](https://www.microsoft.com/sql-server/sql-server-downloads) (qualquer edição).
* Uma cópia local do banco de dados **AdventureWorks2016** , que está disponível para download [aqui](/sql/samples/adventureworks-install-configure).
* Para habilitar o protocolo TCP/IP, que está desabilitado por padrão com a instalação do SQL Server Express. Habilite o protocolo TCP/IP seguindo o artigo [Habilitar ou desabilitar um protocolo de rede de servidor](/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).
* Configurar o [Firewall do Windows para acesso ao mecanismo de banco de dados](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Uma assinatura do Azure. Se você não tiver [uma conta gratuita](https://azure.microsoft.com/free/), crie uma antes de começar.
* Um Instância Gerenciada do SQL. Você pode criar um Instância Gerenciada do SQL seguindo os detalhes no artigo [criar um ASQL instância gerenciada](../azure-sql/managed-instance/instance-create-quickstart.md).
* Para baixar e instalar o [Assistente de migração de dados](https://www.microsoft.com/download/details.aspx?id=53595) v 3.3 ou posterior.
* Um Rede Virtual do Microsoft Azure criado usando o modelo de implantação Azure Resource Manager, que fornece o serviço de migração de banco de dados do Azure com conectividade site a site para seus servidores de origem locais usando o [ExpressRoute](../expressroute/expressroute-introduction.md) ou [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).
* Uma avaliação completa de seu banco de dados local e migração de esquema usando o Assistente de Migração de Dados, conforme descrito no artigo [executando uma avaliação de migração de SQL Server](/sql/dma/dma-assesssqlonprem).
* Para baixar e instalar o `Az.DataMigration` módulo (versão 0.7.2 ou posterior) do Galeria do PowerShell usando o [cmdlet Install-Module PowerShell](/powershell/module/powershellget/Install-Module).
* Para garantir que as credenciais usadas para se conectar à instância de SQL Server de origem tenham a permissão [Control Server](/sql/t-sql/statements/grant-server-permissions-transact-sql) .
* Para garantir que as credenciais usadas para se conectar ao SQL de destino Instância Gerenciada tenham a permissão CONTROL DATABASE nos bancos de dados SQL Instância Gerenciada de destino.

    > [!IMPORTANT]
    > Para migrações online, você já deve ter configurado suas credenciais de Azure Active Directory. Para obter mais informações, consulte o artigo [usar o portal para criar um aplicativo do Azure AD e uma entidade de serviço que pode acessar recursos](../active-directory/develop/howto-create-service-principal-portal.md).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados.

Crie um grupo de recursos usando o [`New-AzResourceGroup`](/powershell/module/az.resources/new-azresourcegroup) comando.

O exemplo a seguir cria um grupo de recursos chamado *MyResource* Group na região *leste dos EUA* .

```powershell
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

## <a name="create-an-instance-of-dms"></a>Criar uma instância do DMS

Crie uma nova instância do Serviço de Migração de Banco de Dados do Azure usando o cmdlet `New-AzDataMigrationService`.
Esse cmdlet espera os seguintes parâmetros obrigatórios:

* *Nome do grupo de recursos do Azure*. Você pode usar o [`New-AzResourceGroup`](/powershell/module/az.resources/new-azresourcegroup) comando para criar um grupo de recursos do Azure como mostrado anteriormente e fornecer seu nome como um parâmetro.
* *Nome do serviço*. Cadeia de caracteres que corresponde ao nome de serviço exclusivo desejado para o serviço de migração de banco de dados do Azure.
* *Localização*. Especifica o local do serviço. Especifique um local de data center do Azure, como oeste dos EUA ou sudeste asiático.
* *SKU*. Esse parâmetro corresponde ao nome do SKU do DMS. Atualmente, os nomes de SKU com suporte são *Basic_1vCore*, *Basic_2vCores* *GeneralPurpose_4vCores*.
* *Identificador de Sub-rede Virtual*. Você pode usar o cmdlet [`New-AzVirtualNetworkSubnetConfig`](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) para criar uma sub-rede.

O exemplo a seguir cria um serviço chamado *mydms* no grupo de recursos *MyDMSResourceGroup* localizado na região *leste dos EUA* usando uma rede virtual chamada *MyVNET* e uma sub-rede chamada *mysubnet*.


```powershell
$vNet = Get-AzVirtualNetwork -ResourceGroupName MyDMSResourceGroup -Name MyVNET

$vSubNet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vNet -Name MySubnet

$service = New-AzDms -ResourceGroupName myResourceGroup `
  -ServiceName MyDMS `
  -Location EastUS `
  -Sku Basic_2vCores `  
  -VirtualSubnetId $vSubNet.Id`
```

## <a name="create-a-migration-project"></a>Criar um projeto de migração

Depois de criar uma instância do Serviço de Migração de Banco de Dados do Azure, crie um projeto de migração. Um projeto do Serviço de Migração de Banco de Dados do Azure exige informações de conexão para as instâncias de origem e de destino, bem como uma lista de bancos de dados que você deseja migrar como parte do projeto.
Defina cadeias de conexão de conectividade de origem e de destino.

O script a seguir define os detalhes da conexão de SQL Server de origem: 

```powershell
# Source connection properties
$sourceDataSource = "<mysqlserver.domain.com/privateIP of source SQL>"
$sourceUserName = "domain\user"
$sourcePassword = "mypassword"
```

O script a seguir define os detalhes de conexão do SQL Instância Gerenciada de destino: 

```powershell
# Target MI connection properties
$targetMIResourceId = "/subscriptions/<subid>/resourceGroups/<rg>/providers/Microsoft.Sql/managedInstances/<myMI>"
$targetUserName = "<user>"
$targetPassword = "<password>"
```



### <a name="define-source-and-target-database-mapping"></a>Definir mapeamento de banco de dados de origem e destino

Fornecer bancos de dados a serem migrados neste projeto de migração

O script a seguir mapeia o banco de dados de origem para o respectivo novo banco de dados no SQL de destino Instância Gerenciada com o nome fornecido.

```powershell
# Selected databases (Source database name to target database name mapping)
$selectedDatabasesMap = New-Object System.Collections.Generic.Dictionary"[String,String]" 
$selectedDatabasesMap.Add("<source  database name>", "<target database name> ")
```

Para vários bancos de dados, adicione a lista de bancos de dados ao script acima usando o seguinte formato:

```powershell
$selectedDatabasesMap = New-Object System.Collections.Generic.Dictionary"[String,String]" 
$selectedDatabasesMap.Add("<source  database name1>", "<target database name1> ")
$selectedDatabasesMap.Add("<source  database name2>", "<target database name2> ")
```

### <a name="create-dms-project"></a>Criar projeto do DMS

Você pode criar um projeto de serviço de migração de banco de dados do Azure dentro da instância DMS.

```powershell
# Create DMS project
$project = New-AzDataMigrationProject `
  -ResourceGroupName $dmsResourceGroupName `
  -ServiceName $dmsServiceName `
  -ProjectName $dmsProjectName `
  -Location $dmsLocation `
  -SourceType SQL `
  -TargetType SQLMI `

# Create selected databases object
$selectedDatabases = @();
foreach ($sourceDbName in $selectedDatabasesMap.Keys){
    $targetDbName = $($selectedDatabasesMap[$sourceDbName])
    $selectedDatabases += New-AzDmsSelectedDB -MigrateSqlServerSqlDbMi `
      -Name $sourceDbName `
      -TargetDatabaseName $targetDbName `
      -BackupFileShare $backupFileShare `
}
```



### <a name="create-a-backup-fileshare-object"></a>Criar um objeto FileShare de backup

Agora crie um objeto FileShare que representa o compartilhamento de rede SMB local para o qual o serviço de migração de banco de dados do Azure pode pegar os backups de banco de dados de origem usando o cmdlet New-AzDmsFileShare

```powershell
# SMB Backup share properties
$smbBackupSharePath = "\\shareserver.domain.com\mybackup"
$smbBackupShareUserName = "domain\user"
$smbBackupSharePassword = "<password>"

# Create backup file share object
$smbBackupSharePasswordSecure = ConvertTo-SecureString -String $smbBackupSharePassword -AsPlainText -Force
$smbBackupShareCredentials = New-Object System.Management.Automation.PSCredential ($smbBackupShareUserName, $smbBackupSharePasswordSecure)
$backupFileShare = New-AzDmsFileShare -Path $smbBackupSharePath -Credential $smbBackupShareCredentials
```

### <a name="define-the-azure-storage"></a>Definir o armazenamento do Azure 

Selecione o contêiner de armazenamento do Azure a ser usado para a migração: 

```powershell
# Storage resource id
$storageAccountResourceId = "/subscriptions/<subscriptionname>/resourceGroups/<rg>/providers/Microsoft.Storage/storageAccounts/<mystorage>"
```


### <a name="configure-azure-active-directory-app"></a>Configurar Azure Active Directory aplicativo

Forneça os detalhes necessários para Azure Active Directory para uma migração de Instância Gerenciada do SQL online: 

```powershell
# AAD properties
$AADAppId = "<appid-guid>"
$AADAppKey = "<app-key>"

# Create AAD object
$AADAppKeySecure = ConvertTo-SecureString $AADAppKey -AsPlainText -Force
$AADApp = New-AzDmsAadApp -ApplicationId $AADAppId -AppKey $AADAppKeySecure
```


## <a name="create-and-start-a-migration-task"></a>Criar e iniciar uma tarefa de migração

Em seguida, crie e inicie uma tarefa do serviço de migração de banco de dados do Azure. Chame a origem e o destino usando variáveis e liste as tabelas de banco de dados a serem migradas: 


```powershell
# Managed Instance online migration properties
$dmsTaskName = "testmigration1"

# Create source connection info
$sourceConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource $sourceDataSource `
  -AuthType WindowsAuthentication `
  -TrustServerCertificate:$true
$sourcePasswordSecure = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCredentials = New-Object System.Management.Automation.PSCredential ($sourceUserName, $sourcePasswordSecure)

# Create target connection info
$targetConnInfo = New-AzDmsConnInfo -ServerType SQLMI `
    -MiResourceId $targetMIResourceId
$targetPasswordSecure = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCredentials = New-Object System.Management.Automation.PSCredential ($targetUserName, $targetPasswordSecure)
```

O exemplo a seguir cria e inicia uma tarefa de migração online: 

```powershell
# Create DMS migration task
$migTask = New-AzDataMigrationTask -TaskType MigrateSqlServerSqlDbMiSync `
  -ResourceGroupName $dmsResourceGroupName `
  -ServiceName $dmsServiceName `
  -ProjectName $dmsProjectName `
  -TaskName $dmsTaskName `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCredentials `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCredentials `
  -SelectedDatabase  $selectedDatabases `
  -BackupFileShare $backupFileShare `
  -AzureActiveDirectoryApp $AADApp `
  -StorageResourceId $storageAccountResourceId
```

Para obter mais informações, consulte [New-AzDataMigrationTask](/powershell/module/az.datamigration/new-azdatamigrationtask).

## <a name="monitor-the-migration"></a>Monitorar a migração

Para monitorar a migração, execute as seguintes tarefas.

### <a name="check-the-status-of-task"></a>Verificar o status da tarefa

```powershell
# Get migration task status details
$migTask = Get-AzDataMigrationTask `
                    -ResourceGroupName $dmsResourceGroupName `
                    -ServiceName $dmsServiceName `
                    -ProjectName $dmsProjectName `
                    -Name $dmsTaskName `
                    -ResultType DatabaseLevelOutput `
                    -Expand

# Task state will be either of 'Queued', 'Running', 'Succeeded', 'Failed', 'FailedInputValidation' or 'Faulted'
$taskState = $migTask.ProjectTask.Properties.State

# Display task state
$taskState | Format-List
```

Use o seguinte para obter a lista de erros:-

```powershell
# Get task errors
$taskErrors = $migTask.ProjectTask.Properties.Errors

# Display task errors
foreach($taskError in $taskErrors){
    $taskError |  Format-List
}


# Get database level details
$databaseLevelOutputs = $migTask.ProjectTask.Properties.Output

# Display database level details
foreach($databaseLevelOutput in $databaseLevelOutputs){

    # This is the source database name.
    $databaseName = $databaseLevelOutput.SourceDatabaseName;

    Write-Host "=========="
    Write-Host "Start migration details for database " $databaseName
    # This is the status for that database - It will be either of:
    # INITIAL, FULL_BACKUP_UPLOADING, FULL_BACKUP_UPLOADED, LOG_FILES_UPLOADING,
    # CUTOVER_IN_PROGRESS, CUTOVER_INITIATED, CUTOVER_COMPLETED, COMPLETED, CANCELLED, FAILED
    $databaseMigrationState = $databaseLevelOutput.MigrationState;

    # Details about last restored backup. This contains file names, LSN, backup date, etc 
    $databaseLastRestoredBackup = $databaseLevelOutput.LastRestoredBackupSetInfo
        
    # Details about last restored backup. This contains file names, LSN, backup date, etc 
    $databaseLastRestoredBackup = $databaseLevelOutput.LastRestoredBackupSetInfo

    # Details about last Currently active/most recent backups. This contains file names, LSN, backup date, etc 
    $databaseActiveBackpusets = $databaseLevelOutput.ActiveBackupSets

    # Display info
    $databaseLevelOutput | Format-List

    Write-Host "Currently active/most recent backupset details:"
    $databaseActiveBackpusets  | select BackupStartDate, BackupFinishedDate, FirstLsn, LastLsn -ExpandProperty ListOfBackupFiles | Format-List

    Write-Host "Last restored backupset details:"
    $databaseLastRestoredBackupFiles  | Format-List

    Write-Host "End migration details for database " $databaseName
    Write-Host "=========="
}
```

## <a name="performing-the-cutover"></a>Executando a transferência 

Com uma migração online, um backup completo e a restauração de bancos de dados são executados e, em seguida, o trabalho prossegue na restauração dos logs de transações armazenados no BackupFileShare.

Quando o banco de dados em um Instância Gerenciada SQL do Azure é atualizado com os mais recentes e está em sincronia com o banco de dado de origem, você pode executar uma transferência.

O exemplo a seguir concluirá o cutover\migration. Os usuários chamam esse comando a seu critério.

```powershell
$command = Invoke-AzDmsCommand -CommandType CompleteSqlMiSync `
                               -ResourceGroupName myResourceGroup `
                               -ServiceName $service.Name `
                               -ProjectName $project.Name `
                               -TaskName myDMSTask `
                               -DatabaseName "Source DB Name"
```

## <a name="deleting-the-instance-of-azure-database-migration-service"></a>Excluindo a instância do serviço de migração de banco de dados do Azure

Depois que a migração for concluída, você poderá excluir a instância do serviço de migração de banco de dados do Azure:

```powershell
Remove-AzDms -ResourceGroupName myResourceGroup -ServiceName MyDMS
```

## <a name="additional-resources"></a>Recursos adicionais

Para obter informações sobre cenários de migração adicionais (pares de origem/destino), consulte o [Guia de migração de banco de dados](https://datamigration.microsoft.com/)da Microsoft.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o serviço de migração de banco de dados do Azure no artigo o [que é o serviço de migração de banco de dados do Azure?](./dms-overview.md).