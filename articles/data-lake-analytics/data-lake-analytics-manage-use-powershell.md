---
title: Gerenciar a Análise Azure Data Lake usando o Azure PowerShell
description: Este artigo descreve como usar o Azure PowerShell para gerenciar contas, fontes de dados, usuários e trabalhos do Data Lake Analytics.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/29/2018
ms.openlocfilehash: f1d5d732433a78b8e3e7ff94bd6aaa2eea8f4a87
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102051779"
---
# <a name="manage-azure-data-lake-analytics-using-azure-powershell"></a>Gerenciar a Análise Azure Data Lake usando o Azure PowerShell

[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Este artigo descreve como gerenciar contas, fontes de dados, usuários e trabalhos do Azure Data Lake Analytics usando o Azure PowerShell.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para usar o PowerShell com o Data Lake Analytics, colete as seguintes informações:

* **ID da assinatura**: ID da assinatura do Azure que contém sua conta do Data Lake Analytics.
* **Grupo de recursos**: o nome do grupo de recursos do Azure que contém sua conta do Data Lake Analytics.
* **Nome de conta do Data Lake Analytics**: o nome da sua conta do Data Lake Analytics.
* **Nome da conta padrão do Data Lake Store**: cada conta do Data Lake Analytics tem uma conta padrão do Data Lake Store.
* **Local**: a localização da sua conta do Data Lake Analytics, como "Leste dos EUA 2" ou outros locais com suporte.

Os snippets do PowerShell neste tutorial usam essas variáveis para armazenar essas informações

```powershell
$subId = "<SubscriptionId>"
$rg = "<ResourceGroupName>"
$adla = "<DataLakeAnalyticsAccountName>"
$adls = "<DataLakeStoreAccountName>"
$location = "<Location>"
```

## <a name="log-in-to-azure"></a>Fazer logon no Azure

### <a name="log-in-using-interactive-user-authentication"></a>Entrar usando autenticação de usuário interativo

Entrar usando uma ID de assinatura ou o nome da assinatura

```powershell
# Using subscription id
Connect-AzAccount -SubscriptionId $subId

# Using subscription name
Connect-AzAccount -SubscriptionName $subname
```

## <a name="saving-authentication-context"></a>Salvando o contexto de autenticação

O cmdlet `Connect-AzAccount` sempre solicita as credenciais. Você pode evitar o recebimento de avisos usando os cmdlets a seguir:

```powershell
# Save login session information
Save-AzAccounts -Path D:\profile.json  

# Load login session information
Select-AzAccounts -Path D:\profile.json
```

### <a name="log-in-using-a-service-principal-identity-spi"></a>Fazer logon usando uma SPI (Identidade de Entidade de Serviço)

```powershell
$tenantid = "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"  
$spi_appname = "appname"
$spi_appid = "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"
$spi_secret = "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"

$pscredential = New-Object System.Management.Automation.PSCredential ($spi_appid, (ConvertTo-SecureString $spi_secret -AsPlainText -Force))
Login-AzAccount -ServicePrincipal -TenantId $tenantid -Credential $pscredential -Subscription $subid
```

## <a name="manage-accounts"></a>Gerenciar Contas

### <a name="list-accounts"></a>Listar contas

```powershell
# List Data Lake Analytics accounts within the current subscription.
Get-AdlAnalyticsAccount

# List Data Lake Analytics accounts within a specific resource group.
Get-AdlAnalyticsAccount -ResourceGroupName $rg
```

### <a name="create-an-account"></a>Criar uma conta

Toda conta do Data Lake Analytics requer uma conta padrão do Data Lake Store usada para o armazenamento de logs. Você pode reutilizar uma conta existente ou criar uma.

```powershell
# Create a data lake store if needed, or you can re-use an existing one
New-AdlStore -ResourceGroupName $rg -Name $adls -Location $location
New-AdlAnalyticsAccount -ResourceGroupName $rg -Name $adla -Location $location -DefaultDataLake $adls
```

### <a name="get-account-information"></a>Obter Informações da conta

Obtenha detalhes sobre uma conta.

```powershell
Get-AdlAnalyticsAccount -Name $adla
```

### <a name="check-if-an-account-exists"></a>Verificar se a conta existe

```powershell
Test-AdlAnalyticsAccount -Name $adla
```

## <a name="manage-data-sources"></a>Gerenciar as fontes de dados

No momento, o Azure Data Lake Analytics dá suporte às seguintes fontes de dados:

* [Repositório Azure Data Lake](../data-lake-store/data-lake-store-overview.md)
* [Armazenamento do Azure](../storage/common/storage-introduction.md)

Toda conta do Data Lake Analytics requer uma conta padrão do Data Lake Store. A conta padrão do Repositório Data Lake é usada para armazenar metadados de trabalho e logs de auditoria de trabalho.

### <a name="find-the-default-data-lake-store-account"></a>Encontrar a conta padrão do Repositório Data Lake

```powershell
$adla_acct = Get-AdlAnalyticsAccount -Name $adla
$dataLakeStoreName = $adla_acct.DefaultDataLakeAccount
```

É possível localizar a conta Data Lake Store padrão filtrando a lista de fontes de dados pela propriedade `IsDefault`:

```powershell
Get-AdlAnalyticsDataSource -Account $adla  | ? { $_.IsDefault }
```

### <a name="add-a-data-source"></a>Adicionar uma fonte de dados

```powershell

# Add an additional Storage (Blob) account.
$AzureStorageAccountName = "<AzureStorageAccountName>"
$AzureStorageAccountKey = "<AzureStorageAccountKey>"
Add-AdlAnalyticsDataSource -Account $adla -Blob $AzureStorageAccountName -AccessKey $AzureStorageAccountKey

# Add an additional Data Lake Store account.
$AzureDataLakeStoreName = "<AzureDataLakeStoreAccountName"
Add-AdlAnalyticsDataSource -Account $adla -DataLakeStore $AzureDataLakeStoreName
```

### <a name="list-data-sources"></a>Listar fontes de dados

```powershell
# List all the data sources
Get-AdlAnalyticsDataSource -Account $adla

# List attached Data Lake Store accounts
Get-AdlAnalyticsDataSource -Account $adla | where -Property Type -EQ "DataLakeStore"

# List attached Storage accounts
Get-AdlAnalyticsDataSource -Account $adla | where -Property Type -EQ "Blob"
```

## <a name="submit-u-sql-jobs"></a>Enviar trabalhos de U-SQL

### <a name="submit-a-string-as-a-u-sql-job"></a>Enviar uma cadeia de caracteres como um script U-SQL

```powershell
$script = @"
@a  =
    SELECT * FROM
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
"@

$scriptpath = "d:\test.usql"
$script | Out-File $scriptpath

Submit-AdlJob -AccountName $adla -Script $script -Name "Demo"
```

### <a name="submit-a-file-as-a-u-sql-job"></a>Enviar um arquivo como um script U-SQL

```powershell
$scriptpath = "d:\test.usql"
$script | Out-File $scriptpath
Submit-AdlJob -AccountName $adla –ScriptPath $scriptpath -Name "Demo"
```

### <a name="list-jobs"></a>Listar trabalhos

A saída inclui os trabalhos em execução no momento e os trabalhos que foram concluídos recentemente.

```powershell
Get-AdlJob -Account $adla
```

### <a name="list-the-top-n-jobs"></a>Listar os melhores trabalhos N

Por padrão, a lista de trabalhos é classificada na hora do envio. Portanto, os trabalhos enviados mais recentemente aparecem primeiro. Por padrão, a conta do ADLA recorda trabalhos por 180 dias, mas o cmdlet Get-AdlJob por padrão retorna apenas os 500 primeiros. Use o parâmetro -Top para listar um número específico de trabalhos.

```powershell
$jobs = Get-AdlJob -Account $adla -Top 10
```

### <a name="list-jobs-by-job-state"></a>Listar trabalhos pelo estado do trabalho

Utilizar o parâmetro `-State`. É possível combinar qualquer um desses valores:

* `Accepted`
* `Compiling`
* `Ended`
* `New`
* `Paused`
* `Queued`
* `Running`
* `Scheduling`
* `Start`

```powershell
# List the running jobs
Get-AdlJob -Account $adla -State Running

# List the jobs that have completed
Get-AdlJob -Account $adla -State Ended

# List the jobs that have not started yet
Get-AdlJob -Account $adla -State Accepted,Compiling,New,Paused,Scheduling,Start
```

### <a name="list-jobs-by-job-result"></a>Listar trabalhos por resultado do trabalho

Utilize o parâmetro `-Result` para detectar se os trabalhos finalizados foram concluídos com êxito. O parâmetro possui esses valores:

* Cancelado
* Com falha
* Nenhum
* Com sucesso

``` powershell
# List Successful jobs.
Get-AdlJob -Account $adla -State Ended -Result Succeeded

# List Failed jobs.
Get-AdlJob -Account $adla -State Ended -Result Failed
```

### <a name="list-jobs-by-job-submitter"></a>Listar trabalhos por emissor do trabalho

O parâmetro `-Submitter` ajuda a identificar quem enviou um trabalho.

```powershell
Get-AdlJob -Account $adla -Submitter "joe@contoso.com"
```

### <a name="list-jobs-by-submission-time"></a>Listar trabalhos por hora de envio

O `-SubmittedAfter` é útil na filtragem para um intervalo de tempo.

```powershell
# List  jobs submitted in the last day.
$d = [DateTime]::Now.AddDays(-1)
Get-AdlJob -Account $adla -SubmittedAfter $d

# List  jobs submitted in the last seven day.
$d = [DateTime]::Now.AddDays(-7)
Get-AdlJob -Account $adla -SubmittedAfter $d
```

### <a name="get-job-status"></a>Obter status do trabalho

Obter o status de um trabalho específico.

```powershell
Get-AdlJob -AccountName $adla -JobId $job.JobId
```

### <a name="cancel-a-job"></a>Cancelar um trabalho

```powershell
Stop-AdlJob -Account $adla -JobID $jobID
```

### <a name="wait-for-a-job-to-finish"></a>Aguardar até que um trabalho seja concluído

Em vez de repetir `Get-AdlAnalyticsJob` até que um trabalho seja concluído, você pode usar o cmdlet `Wait-AdlJob` para aguardar o trabalho ser encerrado.

```powershell
Wait-AdlJob -Account $adla -JobId $job.JobId
```


## <a name="list-job-pipelines-and-recurrences"></a>Listar pipelines e recorrências do trabalho

Utilize o cmdlet `Get-AdlJobPipeline` para consultar as informações de pipeline para trabalhos enviados anteriormente.

```powershell
$pipelines = Get-AdlJobPipeline -Account $adla
$pipeline = Get-AdlJobPipeline -Account $adla -PipelineId "<pipeline ID>"
```

Utilize o cmdlet `Get-AdlJobRecurrence` para consultar as informações de recorrência para trabalhos enviados anteriormente.

```powershell
$recurrences = Get-AdlJobRecurrence -Account $adla

$recurrence = Get-AdlJobRecurrence -Account $adla -RecurrenceId "<recurrence ID>"
```

## <a name="manage-compute-policies"></a>Gerenciar políticas de computação

### <a name="list-existing-compute-policies"></a>Listar as políticas de computação existentes

O cmdlet `Get-AdlAnalyticsComputePolicy` recupera informações sobre políticas de computação para uma conta do Data Lake Analytics.

```powershell
$policies = Get-AdlAnalyticsComputePolicy -Account $adla
```

### <a name="create-a-compute-policy"></a>Criar uma política de computação

O cmdlet `New-AdlAnalyticsComputePolicy` cria uma nova política de computação para uma conta do Data Lake Analytics. Esse exemplo define como 50 as AUs máximas disponíveis ao usuário especificado e a prioridade de trabalho mínima como 250.

```powershell
$userObjectId = (Get-AzAdUser -SearchString "garymcdaniel@contoso.com").Id

New-AdlAnalyticsComputePolicy -Account $adla -Name "GaryMcDaniel" -ObjectId $objectId -ObjectType User -MaxDegreeOfParallelismPerJob 50 -MinPriorityPerJob 250
```

## <a name="manage-files"></a>Gerenciar arquivos

### <a name="check-for-the-existence-of-a-file"></a>Verificar a existência de um arquivo

```powershell
Test-AdlStoreItem -Account $adls -Path "/data.csv"
```

### <a name="uploading-and-downloading"></a>Carregando e baixando arquivos

Carregar um arquivo.

```powershell
Import-AdlStoreItem -AccountName $adls -Path "c:\data.tsv" -Destination "/data_copy.csv"
```

Carregar uma pasta inteira de forma recursiva.

```powershell
Import-AdlStoreItem -AccountName $adls -Path "c:\myData\" -Destination "/myData/" -Recurse
```

Baixar um arquivo.

```powershell
Export-AdlStoreItem -AccountName $adls -Path "/data.csv" -Destination "c:\data.csv"
```

Baixar uma pasta inteira de forma recursiva.

```powershell
Export-AdlStoreItem -AccountName $adls -Path "/" -Destination "c:\myData\" -Recurse
```

> [!NOTE]
> Se o processo de upload ou de download for interrompido, você poderá tentar retomá-lo executando o cmdlet novamente com o sinalizador ``-Resume``.

## <a name="manage-the-u-sql-catalog"></a>Gerenciar o catálogo do U-SQL

O catálogo do U-SQL é usado para estruturar dados e código para que eles possam ser compartilhados por scripts U-SQL. O catálogo possibilita o melhor desempenho possível com dados no Azure Data Lake. Para saber mais, consulte [Usar o Catálogo do U-SQL](./data-lake-analytics-u-sql-get-started.md).

### <a name="list-items-in-the-u-sql-catalog"></a>Itens de lista no catálogo do U-SQL

```powershell
# List U-SQL databases
Get-AdlCatalogItem -Account $adla -ItemType Database

# List tables within a database
Get-AdlCatalogItem -Account $adla -ItemType Table -Path "database"

# List tables within a schema.
Get-AdlCatalogItem -Account $adla -ItemType Table -Path "database.schema"
```

### <a name="list-all-the-assemblies-the-u-sql-catalog"></a>Lista todos os assemblies no catálogo do U-SQL

```powershell
$dbs = Get-AdlCatalogItem -Account $adla -ItemType Database

foreach ($db in $dbs)
{
    $asms = Get-AdlCatalogItem -Account $adla -ItemType Assembly -Path $db.Name

    foreach ($asm in $asms)
    {
        $asmname = "[" + $db.Name + "].[" + $asm.Name + "]"
        Write-Host $asmname
    }
}
```

### <a name="get-details-about-a-catalog-item"></a>Obter detalhes sobre um item de catálogo

```powershell
# Get details of a table
Get-AdlCatalogItem  -Account $adla -ItemType Table -Path "master.dbo.mytable"

# Test existence of a U-SQL database.
Test-AdlCatalogItem  -Account $adla -ItemType Database -Path "master"
```

### <a name="store-credentials-in-the-catalog"></a>Armazenar credenciais no catálogo

Em um banco de dados U-SQL, crie um objeto de credencial para um banco de dados hospedado no Azure. Atualmente, as credenciais do U-SQL são o único tipo de item de catálogo que você pode criar por meio do PowerShell.

```powershell
$dbName = "master"
$credentialName = "ContosoDbCreds"
$dbUri = "https://contoso.database.windows.net:8080"

New-AdlCatalogCredential -AccountName $adla `
          -DatabaseName $db `
          -CredentialName $credentialName `
          -Credential (Get-Credential) `
          -Uri $dbUri
```

## <a name="manage-firewall-rules"></a>Verificar regras de firewall

### <a name="list-firewall-rules"></a>Listar regras de firewall

```powershell
Get-AdlAnalyticsFirewallRule -Account $adla
```

### <a name="add-a-firewall-rule"></a>Adicione uma regra de firewall

```powershell
$ruleName = "Allow access from on-prem server"
$startIpAddress = "<start IP address>"
$endIpAddress = "<end IP address>"

Add-AdlAnalyticsFirewallRule -Account $adla -Name $ruleName -StartIpAddress $startIpAddress -EndIpAddress $endIpAddress
```

### <a name="modify-a-firewall-rule"></a>Modificar uma regra de firewall

```powershell
Set-AdlAnalyticsFirewallRule -Account $adla -Name $ruleName -StartIpAddress $startIpAddress -EndIpAddress $endIpAddress
```

### <a name="remove-a-firewall-rule"></a>Remova uma regra de firewall

```powershell
Remove-AdlAnalyticsFirewallRule -Account $adla -Name $ruleName
```

### <a name="allow-azure-ip-addresses"></a>Permitir endereços IP do Azure

```powershell
Set-AdlAnalyticsAccount -Name $adla -AllowAzureIpState Enabled
```

```powershell
Set-AdlAnalyticsAccount -Name $adla -FirewallState Enabled
Set-AdlAnalyticsAccount -Name $adla -FirewallState Disabled
```

## <a name="working-with-azure"></a>Trabalhando com o Azure

### <a name="get-error-details"></a>Obter detalhes do erro

```powershell
Resolve-AzError -Last
```

### <a name="verify-if-you-are-running-as-an-administrator-on-your-windows-machine"></a>Verifique se você está executando como um administrador no seu computador com Windows

```powershell
function Test-Administrator  
{  
    $user = [Security.Principal.WindowsIdentity]::GetCurrent();
    $p = New-Object Security.Principal.WindowsPrincipal $user
    $p.IsInRole([Security.Principal.WindowsBuiltinRole]::Administrator)  
}
```

### <a name="find-a-tenantid"></a>Localizar um TenantID

De um nome de assinatura:

```powershell
function Get-TenantIdFromSubscriptionName( [string] $subname )
{
    $sub = (Get-AzSubscription -SubscriptionName $subname)
    $sub.TenantId
}

Get-TenantIdFromSubscriptionName "ADLTrainingMS"
```

De uma ID de assinatura:

```powershell
function Get-TenantIdFromSubscriptionId( [string] $subid )
{
    $sub = (Get-AzSubscription -SubscriptionId $subid)
    $sub.TenantId
}

$subid = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
Get-TenantIdFromSubscriptionId $subid
```

De um endereço de domínio, como "contoso.com"

```powershell
function Get-TenantIdFromDomain( $domain )
{
    $url = "https://login.windows.net/" + $domain + "/.well-known/openid-configuration"
    return (Invoke-WebRequest $url|ConvertFrom-Json).token_endpoint.Split('/')[3]
}

$domain = "contoso.com"
Get-TenantIdFromDomain $domain
```

### <a name="list-all-your-subscriptions-and-tenant-ids"></a>Listar todas as IDs de assinaturas e de locatário

```powershell
$subs = Get-AzSubscription
foreach ($sub in $subs)
{
    Write-Host $sub.Name "("  $sub.Id ")"
    Write-Host "`tTenant Id" $sub.TenantId
}
```

## <a name="create-a-data-lake-analytics-account-using-a-template"></a>Criar uma conta do Data Lake Analytics usando um modelo

Você também pode usar um modelo de grupo de recursos do Azure usando o exemplo a seguir: [Criar uma conta no Data Lake Analytics usando um modelo](https://github.com/Azure-Samples/data-lake-analytics-create-account-with-arm-template)

## <a name="next-steps"></a>Próximas etapas
* [Visão geral da Análise Microsoft Azure Data Lake](data-lake-analytics-overview.md)
* Introdução ao data Lake Analytics usando o [portal do Azure](data-lake-analytics-get-started-portal.md)  |  [Azure PowerShell](data-lake-analytics-get-started-powershell.md)  |  [CLI do Azure](data-lake-analytics-get-started-cli.md)
* Gerenciar Azure data Lake Analytics usando [portal do Azure](data-lake-analytics-manage-use-portal.md)  |  [Azure PowerShell](data-lake-analytics-manage-use-powershell.md)  |  [CLI](data-lake-analytics-manage-use-cli.md)
