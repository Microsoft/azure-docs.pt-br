---
title: Criar um agente de Trabalho Elástico usando o PowerShell (versão prévia)
description: Saiba como criar e gerenciar um agente de Trabalho Elástico usando o PowerShell.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: seo-lt-2019, devx-track-azurepowershell
ms.devlang: ''
ms.topic: tutorial
author: johnpaulkee
ms.author: joke
ms.reviwer: sstein
ms.date: 10/21/2020
ms.openlocfilehash: 95e9ef340328bb4c1835e966cc9c3019bca88c09
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100368822"
---
# <a name="create-an-elastic-job-agent-using-powershell-preview"></a>Criar um agente de Trabalho Elástico usando o PowerShell (versão prévia)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Os [trabalhos elásticos (versão prévia)](job-automation-overview.md) habilitam a execução de um ou mais scripts T-SQL (Transact-SQL) em paralelo entre vários bancos de dados.

Neste tutorial, você aprenderá as etapas necessárias para executar uma consulta em vários bancos de dados:

> [!div class="checklist"]
> * Criar um agente de Trabalho Elástico
> * Criar credenciais de trabalho para que os trabalhos possam executar scripts em seus destinos
> * Definir os destinos (servidores, pools elásticos, bancos de dados, mapas de fragmento) nos quais você deseja executar o trabalho
> * Criar credenciais no escopo do banco de dados nos bancos de dados de destino para que o agente se conecte e execute trabalhos
> * Criar um trabalho
> * Adicionar etapas de trabalho a um trabalho
> * Iniciar a execução de um trabalho
> * Monitorar um trabalho

## <a name="prerequisites"></a>Pré-requisitos

A versão atualizada dos trabalhos de Banco de Dados Elástico tem um novo conjunto de cmdlets do PowerShell para uso durante a migração. Esses novos cmdlets transferem todas as suas credenciais de trabalho existentes, destinos (incluindo bancos de dados, servidores, coleções personalizadas), gatilhos de trabalho, agendas de trabalho, conteúdos de trabalhos e trabalhos de failover para um novo agente de Trabalho Elástico.

### <a name="install-the-latest-elastic-jobs-cmdlets"></a>Instalar os cmdlets de Trabalhos Elásticos mais recentes

Se você ainda não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

Instale o módulo **Az.Sql** mais recente para obter os cmdlets de Trabalho Elástico. Execute os comandos a seguir no PowerShell com acesso administrativo.

```powershell
# installs the latest PackageManagement and PowerShellGet packages
Find-Package PackageManagement | Install-Package -Force
Find-Package PowerShellGet | Install-Package -Force

# Restart your powershell session with administrative access

# Install and import the Az.Sql module, then confirm
Install-Module -Name Az.Sql
Import-Module Az.Sql

Get-Module Az.Sql
```

Além do módulo **Az.Sql**, este tutorial também exige o módulo *SqlServer* do PowerShell. Para obter detalhes, confira [Instalar o módulo do SQL Server PowerShell](/sql/powershell/download-sql-server-ps-module).

## <a name="create-required-resources"></a>Criar recursos necessários

A criação de um agente de Trabalho Elástico requer um banco de dados (S0 ou superior) para uso como o [banco de dados de trabalhos](job-automation-overview.md#elastic-job-database).

O script a seguir cria um novo grupo de recursos, servidor e banco de dados para uso como o banco de dados de trabalhos. O segundo script cria um segundo servidor com dois bancos de dados em branco nos quais os trabalhos serão executados.

Os Trabalhos Elásticos não têm nenhum requisito de nomenclatura específico. Você pode usar as convenções de nomenclatura que desejar, desde que estejam em conformidade com os [requisitos do Azure](/azure/architecture/best-practices/resource-naming).

```powershell
# sign in to Azure account
Connect-AzAccount

# create a resource group
Write-Output "Creating a resource group..."
$resourceGroupName = Read-Host "Please enter a resource group name"
$location = Read-Host "Please enter an Azure Region"
$rg = New-AzResourceGroup -Name $resourceGroupName -Location $location
$rg

# create a server
Write-Output "Creating a server..."
$agentServerName = Read-Host "Please enter an agent server name"
$agentServerName = $agentServerName + "-" + [guid]::NewGuid()
$adminLogin = Read-Host "Please enter the server admin name"
$adminPassword = Read-Host "Please enter the server admin password"
$adminPasswordSecure = ConvertTo-SecureString -String $AdminPassword -AsPlainText -Force
$adminCred = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList $adminLogin, $adminPasswordSecure
$agentServer = New-AzSqlServer -ResourceGroupName $resourceGroupName -Location $location `
    -ServerName $agentServerName -ServerVersion "12.0" -SqlAdministratorCredentials ($adminCred)

# set server firewall rules to allow all Azure IPs
Write-Output "Creating a server firewall rule..."
$agentServer | New-AzSqlServerFirewallRule -AllowAllAzureIPs
$agentServer

# create the job database
Write-Output "Creating a blank database to be used as the Job Database..."
$jobDatabaseName = "JobDatabase"
$jobDatabase = New-AzSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $agentServerName -DatabaseName $jobDatabaseName -RequestedServiceObjectiveName "S0"
$jobDatabase
```

```powershell
# create a target server and sample databases - uses the same credentials
Write-Output "Creating target server..."
$targetServerName = Read-Host "Please enter a target server name"
$targetServerName = $targetServerName + "-" + [guid]::NewGuid()
$targetServer = New-AzSqlServer -ResourceGroupName $resourceGroupName -Location $location `
    -ServerName $targetServerName -ServerVersion "12.0" -SqlAdministratorCredentials ($adminCred)

# set target server firewall rules to allow all Azure IPs
$targetServer | New-AzSqlServerFirewallRule -AllowAllAzureIPs
$targetServer | New-AzSqlServerFirewallRule -StartIpAddress 0.0.0.0 -EndIpAddress 255.255.255.255 -FirewallRuleName AllowAll
$targetServer

# create sample databases to execute jobs against
$db1 = New-AzSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $targetServerName -DatabaseName "database1"
$db1
$db2 = New-AzSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $targetServerName -DatabaseName "database2"
$db2
```

### <a name="create-the-elastic-job-agent"></a>Criar o agente de Trabalho Elástico

Um agente de Trabalho Elástico é um recurso do Azure para criar, executar e gerenciar trabalhos. O agente executa trabalhos com base em uma agenda ou como um único trabalho.

O cmdlet **New-AzSqlElasticJobAgent** exige que um banco de dados no Banco de Dados SQL do Azure já exista, de modo que todos os parâmetros *resourceGroupName*, *serverName* e *databaseName* apontem para recursos existentes.

```powershell
Write-Output "Creating job agent..."
$agentName = Read-Host "Please enter a name for your new Elastic Job agent"
$jobAgent = $jobDatabase | New-AzSqlElasticJobAgent -Name $agentName
$jobAgent
```

### <a name="create-the-job-credentials"></a>Criar as credenciais do trabalho

Os trabalhos usam credenciais no escopo do banco de dados para se conectar aos bancos de dados de destino especificados pelo grupo de destino após a execução e executar os scripts. Essas credenciais no escopo do banco de dados também são usadas para se conectar ao banco de dados mestre e enumerar todos os bancos de dados em um servidor ou um pool elástico, quando um destes é usado como o tipo de membro do grupo de destino.

As credenciais no escopo do banco de dados devem ser criadas no banco de dados de trabalhos. Todos os bancos de dados de destino devem ter um logon com permissões suficientes para que o trabalho seja concluído com êxito.

![Credenciais de trabalhos elásticos](./media/elastic-jobs-powershell-create/job-credentials.png)

Além das credenciais na imagem, observe a adição dos comandos **GRANT** no script a seguir. Essas permissões são necessárias para o script que escolhemos como exemplo para o trabalho. O exemplo cria uma nova tabela nos bancos de dados de destino, cada banco de dados de destino precisa ter as permissões apropriadas para ser executado com êxito.

Para criar as credenciais de trabalho necessárias (no banco de dados de trabalho), execute o seguinte script:

```powershell
# in the master database (target server)
# create the master user login, master user, and job user login
$params = @{
  'database' = 'master'
  'serverInstance' =  $targetServer.ServerName + '.database.windows.net'
  'username' = $adminLogin
  'password' = $adminPassword
  'outputSqlErrors' = $true
  'query' = 'CREATE LOGIN masteruser WITH PASSWORD=''password!123'''
}
Invoke-SqlCmd @params
$params.query = "CREATE USER masteruser FROM LOGIN masteruser"
Invoke-SqlCmd @params
$params.query = 'CREATE LOGIN jobuser WITH PASSWORD=''password!123'''
Invoke-SqlCmd @params

# for each target database
# create the jobuser from jobuser login and check permission for script execution
$targetDatabases = @( $db1.DatabaseName, $Db2.DatabaseName )
$createJobUserScript =  "CREATE USER jobuser FROM LOGIN jobuser"
$grantAlterSchemaScript = "GRANT ALTER ON SCHEMA::dbo TO jobuser"
$grantCreateScript = "GRANT CREATE TABLE TO jobuser"

$targetDatabases | % {
  $params.database = $_
  $params.query = $createJobUserScript
  Invoke-SqlCmd @params
  $params.query = $grantAlterSchemaScript
  Invoke-SqlCmd @params
  $params.query = $grantCreateScript
  Invoke-SqlCmd @params
}

# create job credential in Job database for master user
Write-Output "Creating job credentials..."
$loginPasswordSecure = (ConvertTo-SecureString -String 'password!123' -AsPlainText -Force)

$masterCred = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList "masteruser", $loginPasswordSecure
$masterCred = $jobAgent | New-AzSqlElasticJobCredential -Name "masteruser" -Credential $masterCred

$jobCred = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList "jobuser", $loginPasswordSecure
$jobCred = $jobAgent | New-AzSqlElasticJobCredential -Name "jobuser" -Credential $jobCred
```

### <a name="define-the-target-databases-to-run-the-job-against"></a>Definir os bancos de dados de destino nos quais executar os trabalhos

Um [grupo de destino](job-automation-overview.md#target-group) define o conjunto de um ou mais bancos de dados em que uma etapa de trabalho será executada.

O seguinte snippet de código cria dois grupos de destino: *serverGroup* e *serverGroupExcludingDb2*. *serverGroup* tem como alvo todos os bancos de dados existentes no momento da execução e *serverGroupExcludingDb2* tem como alvo todos os bancos de dados no servidor, exceto *targetDb2*:

```powershell
Write-Output "Creating test target groups..."
# create ServerGroup target group
$serverGroup = $jobAgent | New-AzSqlElasticJobTargetGroup -Name 'ServerGroup'
$serverGroup | Add-AzSqlElasticJobTarget -ServerName $targetServerName -RefreshCredentialName $masterCred.CredentialName

# create ServerGroup with an exclusion of db2
$serverGroupExcludingDb2 = $jobAgent | New-AzSqlElasticJobTargetGroup -Name 'ServerGroupExcludingDb2'
$serverGroupExcludingDb2 | Add-AzSqlElasticJobTarget -ServerName $targetServerName -RefreshCredentialName $masterCred.CredentialName
$serverGroupExcludingDb2 | Add-AzSqlElasticJobTarget -ServerName $targetServerName -Database $db2.DatabaseName -Exclude
```

### <a name="create-a-job-and-steps"></a>Criar um trabalho e etapas

Este exemplo define um trabalho e duas etapas de trabalho para que o trabalho seja executado. A primeira etapa de trabalho (*etapa 1*) cria uma nova tabela (*Step1Table*) em cada banco de dados no grupo de destino *ServerGroup*. A segunda etapa de trabalho (*step2*) cria uma nova tabela (*Step2Table*) em cada banco de dados, exceto para *TargetDb2*, pois o grupo de destino definido anteriormente está especificado para excluí-lo.

```powershell
Write-Output "Creating a new job..."
$jobName = "Job1"
$job = $jobAgent | New-AzSqlElasticJob -Name $jobName -RunOnce
$job

Write-Output "Creating job steps..."
$sqlText1 = "IF NOT EXISTS (SELECT * FROM sys.tables WHERE object_id = object_id('Step1Table')) CREATE TABLE [dbo].[Step1Table]([TestId] [int] NOT NULL);"
$sqlText2 = "IF NOT EXISTS (SELECT * FROM sys.tables WHERE object_id = object_id('Step2Table')) CREATE TABLE [dbo].[Step2Table]([TestId] [int] NOT NULL);"

$job | Add-AzSqlElasticJobStep -Name "step1" -TargetGroupName $serverGroup.TargetGroupName -CredentialName $jobCred.CredentialName -CommandText $sqlText1
$job | Add-AzSqlElasticJobStep -Name "step2" -TargetGroupName $serverGroupExcludingDb2.TargetGroupName -CredentialName $jobCred.CredentialName -CommandText $sqlText2
```

### <a name="run-the-job"></a>Executar o trabalho

Para iniciar o trabalho imediatamente, execute o seguinte comando:

```powershell
Write-Output "Start a new execution of the job..."
$jobExecution = $job | Start-AzSqlElasticJob
$jobExecution
```

Após a conclusão bem-sucedida, você deverá ver duas tabelas novas no TargetDb1 e apenas uma tabela nova no TargetDb2:

   ![verificação de novas tabelas no SSMS](./media/elastic-jobs-powershell-create/job-execution-verification.png)

Você também pode agendar o trabalho para ser executado posteriormente. Para agendar um trabalho para execução em um momento específico, execute o seguinte comando:

```powershell
# run every hour starting from now
$job | Set-AzSqlElasticJob -IntervalType Hour -IntervalCount 1 -StartTime (Get-Date) -Enable
```

### <a name="monitor-status-of-job-executions"></a>Monitorar status de execuções de trabalho

Os seguintes snippets de código obtêm os detalhes de execução do trabalho:

```powershell
# get the latest 10 executions run
$jobAgent | Get-AzSqlElasticJobExecution -Count 10

# get the job step execution details
$jobExecution | Get-AzSqlElasticJobStepExecution

# get the job target execution details
$jobExecution | Get-AzSqlElasticJobTargetExecution -Count 2
```

A tabela a seguir lista os estados possíveis de execução de trabalho:

|Estado|Descrição|
|:---|:---|
|**Criado** | A execução do trabalho acabou de ser criada e não ainda está em andamento.|
|**InProgress** | A execução do trabalho está atualmente em andamento.|
|**WaitingForRetry** | A execução do trabalho não conseguiu concluir a ação e está aguardando para tentar novamente.|
|**Êxito** | A execução do trabalho foi concluída com êxito.|
|**SucceededWithSkipped** | A execução do trabalho foi concluída com êxito, mas alguns de seus filhos foram ignorados.|
|**Com falha** | A execução do trabalho falhou e esgotou suas novas tentativas.|
|**TimedOut** | A execução do trabalho atingiu o tempo limite.|
|**Cancelado** | A execução do trabalho foi cancelada.|
|**Ignorado** | A execução do trabalho foi ignorada porque outra execução da mesma etapa de trabalho já estava em execução no mesmo destino.|
|**WaitingForChildJobExecutions** | A execução do trabalho está esperando a conclusão das execuções filhas.|

## <a name="clean-up-resources"></a>Limpar os recursos

Exclua os recursos do Azure criados neste tutorial excluindo o grupo de recursos.

> [!TIP]
> Se planejar continuar a usar esses trabalhos, não limpe os recursos criados neste artigo.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você executou um script Transact-SQL em um conjunto de bancos de dados. Você aprendeu a realizar as seguintes tarefas:

> [!div class="checklist"]
> * Criar um agente de Trabalho Elástico
> * Criar credenciais de trabalho para que os trabalhos possam executar scripts em seus destinos
> * Definir os destinos (servidores, pools elásticos, bancos de dados, mapas de fragmento) nos quais você deseja executar o trabalho
> * Criar credenciais no escopo do banco de dados nos bancos de dados de destino para que o agente se conecte e execute trabalhos
> * Criar um trabalho
> * Adicionar uma etapa de trabalho ao trabalho
> * Iniciar a execução do trabalho
> * Monitorar trabalho

> [!div class="nextstepaction"]
> [Gerenciar Trabalhos Elásticos usando Transact-SQL](elastic-jobs-tsql-create-manage.md)
