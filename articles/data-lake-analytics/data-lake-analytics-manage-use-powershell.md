---
title: Gerenciar o Azure Data Lake Analytics usando o Azure PowerShell | Microsoft Docs
description: "Saiba como gerenciar contas, fontes de dados, trabalhos e itens de catálogo do Azure Data Lake Analytics. "
services: data-lake-analytics
documentationcenter: 
author: matt1883
manager: jhubbard
editor: cgronlun
ms.assetid: ad14d53c-fed4-478d-ab4b-6d2e14ff2097
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/23/2017
ms.author: mahi
ms.openlocfilehash: 65bf5928428b21e98c893a9de8ca596329329411
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="manage-azure-data-lake-analytics-using-azure-powershell"></a>Gerenciar a Análise Azure Data Lake usando o Azure PowerShell
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Saiba como gerenciar contas, fontes de dados, usuários, trabalhos e itens de catálogo do Azure Data Lake Analytics usando o Azure PowerShell. 

## <a name="prerequisites"></a>Pré-requisitos

Ao criar uma conta do Data Lake Analytics, você precisa conhecer as seguintes informações:

* **ID da assinatura**: ID da assinatura do Azure na qual sua conta do Data Lake Analytics se encontra.
* **Grupo de recursos**: o nome do grupo de recursos do Azure que contém sua conta do Data Lake Analytics.
* **Nome da conta do Data Lake Analytics**: o nome da conta deve conter apenas letras minúsculas e números.
* **Conta padrão do Data Lake Store**: cada conta do Data Lake Analytics tem uma conta padrão do Data Lake Store. Essas contas devem estar no mesmo local.
* **Local**: a localização da sua conta do Data Lake Analytics, como "Leste dos EUA 2" ou outros locais com suporte. Os locais com suporte podem ser vistos em nossa [página de preços](https://azure.microsoft.com/pricing/details/data-lake-analytics/).

Os trechos do PowerShell neste tutorial usam essas variáveis para armazenar essas informações

```powershell
$subId = "<SubscriptionId>"
$rg = "<ResourceGroupName>"
$adla = "<DataLakeAnalyticsAccountName>"
$adls = "<DataLakeStoreAccountName>"
$location = "<Location>"
```

## <a name="log-in"></a>Fazer logon

Faça logon usando uma ID de assinatura.

```powershell
Login-AzureRmAccount -SubscriptionId $subId
```

Faça logon usando um nome de assinatura.

```
Login-AzureRmAccount -SubscriptionName $subname 
```

O cmdlet `Login-AzureRmAccount` sempre solicitará as credenciais. Você pode evitar o recebimento de avisos usando os cmdlets a seguir:

```powershell
# Save login session information
Save-AzureRmProfile -Path D:\profile.json  

# Load login session information
Select-AzureRmProfile -Path D:\profile.json 
```

## <a name="managing-accounts"></a>Gerenciamento de contas

### <a name="create-a-data-lake-analytics-account"></a>Criar uma conta da Análise Data Lake

Se você ainda não tiver um [grupo de recursos](../azure-resource-manager/resource-group-overview.md#resource-groups) para usar, crie um. 

```powershell
New-AzureRmResourceGroup -Name  $rg -Location $location
```

Toda conta do Data Lake Analytics requer uma conta padrão do Data Lake Store usada para o armazenamento de logs. Você pode reutilizar uma conta existente ou criar uma. 

```powershell
New-AdlStore -ResourceGroupName $rg -Name $adls -Location $location
```

Quando um Grupo de Recursos e uma conta do Data Lake Store estiverem disponíveis, crie uma conta do Data Lake Analytics.

```powershell
New-AdlAnalyticsAccount -ResourceGroupName $rg -Name $adla -Location $location -DefaultDataLake $adls
```

### <a name="get-information-about-an-account"></a>Obter informações sobre uma conta

Obtenha detalhes sobre uma conta.

```powershell
Get-AdlAnalyticsAccount -Name $adla
```

Verifique a existência de uma conta específica do Data Lake Analytics. O cmdlet retorna `True` ou `False`.

```powershell
Test-AdlAnalyticsAccount -Name $adla
```

Verifique a existência de uma conta específica do Data Lake Store. O cmdlet retorna `True` ou `False`.

```powershell
Test-AdlStoreAccount -Name $adls
```

### <a name="listing-accounts"></a>Listar contas

Liste contas do Data Lake Analytics na assinatura atual.

```powershell
Get-AdlAnalyticsAccount
```

Liste contas do Data Lake Analytics em um grupo de recursos específico.

```powershell
Get-AdlAnalyticsAccount -ResourceGroupName $rg
```

## <a name="managing-firewall-rules"></a>Gerenciar regras de firewall

Liste regras de firewall.

```powershell
Get-AdlAnalyticsFirewallRule -Account $adla
```

Adicione uma regra de firewall.

```powershell
$ruleName = "Allow access from on-prem server"
$startIpAddress = "<start IP address>"
$endIpAddress = "<end IP address>"

Add-AdlAnalyticsFirewallRule -Account $adla -Name $ruleName -StartIpAddress $startIpAddress -EndIpAddress $endIpAddress
```

Altere uma regra de firewall.

```powershell
Set-AdlAnalyticsFirewallRule -Account $adla -Name $ruleName -StartIpAddress $startIpAddress -EndIpAddress $endIpAddress
```

Remova uma regra de firewall.

```powershell
Remove-AdlAnalyticsFirewallRule -Account $adla -Name $ruleName
```



Permita endereços IP do Azure.

```powershell
Set-AdlAnalyticsAccount -Name $adla -AllowAzureIpState Enabled
```

```powershell
Set-AdlAnalyticsAccount -Name $adla -FirewallState Enabled
Set-AdlAnalyticsAccount -Name $adla -FirewallState Disabled
```

## <a name="managing-data-sources"></a>Gerenciar fontes de dados
No momento, o Azure Data Lake Analytics dá suporte às seguintes fontes de dados:

* [Repositório Azure Data Lake](../data-lake-store/data-lake-store-overview.md)
* [Armazenamento do Azure](../storage/common/storage-introduction.md)

Ao criar uma conta do Analytics, você deve indicar uma conta do Data Lake Store como a fonte de dados padrão. A conta padrão do Repositório Data Lake é usada para armazenar metadados de trabalho e logs de auditoria de trabalho. Após criar uma conta do Data Lake Analytics, você pode adicionar outras contas do Data Lake Store e/ou contas de Armazenamento. 

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
Get-AdlAnalyticsDataSource -Name $adla

# List attached Data Lake Store accounts
Get-AdlAnalyticsDataSource -Name $adla | where -Property Type -EQ "DataLakeStore"

# List attached Storage accounts
Get-AdlAnalyticsDataSource -Name $adla | where -Property Type -EQ "Blob"
```

## <a name="submit-u-sql-jobs"></a>Enviar trabalhos de U-SQL

### <a name="submit-a-string-as-a-u-sql-script"></a>Enviar uma cadeia de caracteres como um script U-SQL

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


### <a name="submit-a-file-as-a-u-sql-script"></a>Enviar um arquivo como um script U-SQL

```powershell
$scriptpath = "d:\test.usql"
$script | Out-File $scriptpath 
Submit-AdlJob -AccountName $adla –ScriptPath $scriptpath -Name "Demo"
```

## <a name="list-jobs-in-an-account"></a>Listar trabalhos em uma conta

### <a name="list-all-the-jobs-in-the-account"></a>Listar todos os trabalhos na conta. 

A saída inclui os trabalhos em execução no momento e os trabalhos que foram concluídos recentemente.

```powershell
Get-AdlJob -Account $adla
```


### <a name="list-a-specific-number-of-jobs"></a>Listar um número específico de trabalhos

Por padrão, a lista de trabalhos é classificada na hora do envio. Portanto, os trabalhos enviados mais recentemente aparecem primeiro. Por padrão, a conta do ADLA recorda os trabalhos por 180 dias, mas o cmdlet Ge-AdlJob retorna apenas os primeiros 500. Use o parâmetro -Top para listar um número específico de trabalhos.

```powershell
$jobs = Get-AdlJob -Account $adla -Top 10
```


### <a name="list-jobs-based-on-the-value-of-job-property"></a>Listar trabalhos com base no valor da propriedade do trabalho

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

Utilize o parâmetro `-Result` para detectar se os trabalhos finalizados foram concluídos com êxito. O parâmetro possui esses valores:

* Cancelado
* Com falha
* Nenhum
* Bem-sucedido

``` powershell
# List Successful jobs.
Get-AdlJob -Account $adla -State Ended -Result Succeeded

# List Failed jobs.
Get-AdlJob -Account $adla -State Ended -Result Failed
```


O parâmetro `-Submitter` ajuda a identificar quem enviou um trabalho.

```powershell
Get-AdlJob -Account $adla -Submitter "joe@contoso.com"
```

O `-SubmittedAfter` é útil na filtragem para um intervalo de tempo.


```powershell
# List  jobs submitted in the last day.
$d = [DateTime]::Now.AddDays(-1)
Get-AdlJob -Account $adla -SubmittedAfter $d

# List  jobs submitted in the last seven day.
$d = [DateTime]::Now.AddDays(-7)
Get-AdlJob -Account $adla -SubmittedAfter $d
```

### <a name="analyzing-job-history"></a>Analisar o histórico do trabalho

Usar o Azure PowerShell para analisar o histórico de trabalhos que foram executados no Data Lake Analytics é uma técnica poderosa. Você pode usá-lo para obter informações sobre o uso e o custo. Você pode saber mais examinando o [repositório de exemplo de análise do histórico do trabalho](https://github.com/Azure-Samples/data-lake-analytics-powershell-job-history-analysis)  

## <a name="get-information-about-pipelines-and-recurrences"></a>Obter informações sobre pipelines e recorrências

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

## <a name="get-information-about-a-job"></a>Obter informações sobre um trabalho

### <a name="get-job-status"></a>Obter status do trabalho

Obter o status de um trabalho específico.

```powershell
Get-AdlJob -AccountName $adla -JobId $job.JobId
```

### <a name="examine-the-job-outputs"></a>Examinar as saídas do trabalho

Depois que o trabalho for encerrado, verifique se o arquivo de saída existe listando os arquivos em uma pasta.

```powershell
Get-AdlStoreChildItem -Account $adls -Path "/"
```

## <a name="manage-running-jobs"></a>Gerenciar trabalhos em execução

### <a name="cancel-a-job"></a>Cancelar um trabalho

```powershell
Stop-AdlJob -Account $adls -JobID $jobID
```

### <a name="wait-for-a-job-to-finish"></a>Aguardar até que um trabalho seja concluído

Em vez de repetir `Get-AdlAnalyticsJob` até que um trabalho seja concluído, você pode usar o cmdlet `Wait-AdlJob` para aguardar o trabalho ser encerrado.

```powershell
Wait-AdlJob -Account $adla -JobId $job.JobId
```

## <a name="manage-compute-policies"></a>Gerenciar políticas de computação

### <a name="list-existing-compute-policies"></a>Listar as políticas de computação existentes

O cmdlet `Get-AdlAnalyticsComputePolicy` recupera informações sobre políticas de computação para uma conta do Data Lake Analytics.

```powershell
$policies = Get-AdlAnalyticsComputePolicy -Account $adla
```

### <a name="create-a-compute-policy"></a>Criar uma política de computação

O cmdlet `New-AdlAnalyticsComputePolicy` cria uma nova política de computação para uma conta do Data Lake Analytics. Esse exemplo define as AUs máximas disponíveis ao usuário especificado para 50 e a prioridade de trabalho mínima para 250.

```powershell
$userObjectId = (Get-AzureRmAdUser -SearchString "garymcdaniel@contoso.com").Id

New-AdlAnalyticsComputePolicy -Account $adla -Name "GaryMcDaniel" -ObjectId $objectId -ObjectType User -MaxDegreeOfParallelismPerJob 50 -MinPriorityPerJob 250
```

## <a name="check-for-the-existence-of-a-file"></a>Verificar a existência de um arquivo.

```powershell
Test-AdlStoreItem -Account $adls -Path "/data.csv"
```

## <a name="uploading-and-downloading"></a>Carregando e baixando arquivos

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

## <a name="manage-catalog-items"></a>Gerenciar itens do catálogo

O catálogo do U-SQL é usado para estruturar dados e código para que eles possam ser compartilhados por scripts U-SQL. O catálogo possibilita o melhor desempenho possível com dados no Azure Data Lake. Para saber mais, consulte [Usar o Catálogo do U-SQL](data-lake-analytics-use-u-sql-catalog.md).

### <a name="list-items-in-the-u-sql-catalog"></a>Itens de lista no catálogo do U-SQL

```powershell
# List U-SQL databases
Get-AdlCatalogItem -Account $adla -ItemType Database 

# List tables within a database
Get-AdlCatalogItem -Account $adla -ItemType Table -Path "database"

# List tables within a schema.
Get-AdlCatalogItem -Account $adla -ItemType Table -Path "database.schema"
```

Listar todos os assemblies em todos os bancos de dados em uma conta do ADLA.

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

### <a name="create-credentials-in-a-catalog"></a>Criar credenciais em um catálogo

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

### <a name="get-basic-information-about-an-adla-account"></a>Obter informações básicas sobre uma conta do ADLA

Dado um nome de conta, o código a seguir procura informações básicas sobre a conta

```
$adla_acct = Get-AdlAnalyticsAccount -Name "saveenrdemoadla"
$adla_name = $adla_acct.Name
$adla_subid = $adla_acct.Id.Split("/")[2]
$adla_sub = Get-AzureRmSubscription -SubscriptionId $adla_subid
$adla_subname = $adla_sub.Name
$adla_defadls_datasource = Get-AdlAnalyticsDataSource -Account $adla_name  | ? { $_.IsDefault } 
$adla_defadlsname = $adla_defadls_datasource.Name

Write-Host "ADLA Account Name" $adla_name
Write-Host "Subscription Id" $adla_subid
Write-Host "Subscription Name" $adla_subname
Write-Host "Defautl ADLS Store" $adla_defadlsname
Write-Host 

Write-Host '$subname' " = ""$adla_subname"" "
Write-Host '$subid' " = ""$adla_subid"" "
Write-Host '$adla' " = ""$adla_name"" "
Write-Host '$adls' " = ""$adla_defadlsname"" "
```

## <a name="working-with-azure"></a>Trabalhando com o Azure

### <a name="get-details-of-azurerm-errors"></a>Obter os detalhes de erros do AzureRm

```powershell
Resolve-AzureRmError -Last
```

### <a name="verify-if-you-are-running-as-an-administrator"></a>Verifique se você está executando como um administrador

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
function Get-TenantIdFromSubcriptionName( [string] $subname )
{
    $sub = (Get-AzureRmSubscription -SubscriptionName $subname)
    $sub.TenantId
}

Get-TenantIdFromSubcriptionName "ADLTrainingMS"
```

De uma ID de assinatura:

```powershell
function Get-TenantIdFromSubcriptionId( [string] $subid )
{
    $sub = (Get-AzureRmSubscription -SubscriptionId $subid)
    $sub.TenantId
}

$subid = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
Get-TenantIdFromSubcriptionId $subid
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
$subs = Get-AzureRmSubscription
foreach ($sub in $subs)
{
    Write-Host $sub.Name "("  $sub.Id ")"
    Write-Host "`tTenant Id" $sub.TenantId
}
```

## <a name="create-a-data-lake-analytics-account-using-a-template"></a>Criar uma conta do Data Lake Analytics usando um modelo

Você também pode utilizar um modelo do grupo de recursos do Azure usando o seguinte script do PowerShell:

```powershell
$subId = "<Your Azure Subscription ID>"

$rg = "<New Azure Resource Group Name>"
$location = "<Location (such as East US 2)>"
$adls = "<New Data Lake Store Account Name>"
$adla = "<New Data Lake Analytics Account Name>"

$deploymentName = "MyDataLakeAnalyticsDeployment"
$armTemplateFile = "<LocalFolderPath>\azuredeploy.json"  # update the JSON template path 

# Log in to Azure
Login-AzureRmAccount -SubscriptionId $subId

# Create the resource group
New-AzureRmResourceGroup -Name $rg -Location $location

# Create the Data Lake Analytics account with the default Data Lake Store account.
$parameters = @{"adlAnalyticsName"=$adla; "adlStoreName"=$adls}
New-AzureRmResourceGroupDeployment -Name $deploymentName -ResourceGroupName $rg -TemplateFile $armTemplateFile -TemplateParameterObject $parameters 
```

Para saber mais, confira [Implantar um aplicativo com um modelo do Gerenciador de Recursos do Azure](../azure-resource-manager/resource-group-template-deploy.md) e [Criando modelos do Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

**Modelo de exemplo**

Salve o texto a seguir como um arquivo `.json` e, em seguida, utilize o script anterior do PowerShell para usar o modelo. 

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "adlAnalyticsName": {
      "type": "string",
      "metadata": {
        "description": "The name of the Data Lake Analytics account to create."
      }
    },
    "adlStoreName": {
      "type": "string",
      "metadata": {
        "description": "The name of the Data Lake Store account to create."
      }
    }
  },
  "resources": [
    {
      "name": "[parameters('adlStoreName')]",
      "type": "Microsoft.DataLakeStore/accounts",
      "location": "East US 2",
      "apiVersion": "2015-10-01-preview",
      "dependsOn": [ ],
      "tags": { }
    },
    {
      "name": "[parameters('adlAnalyticsName')]",
      "type": "Microsoft.DataLakeAnalytics/accounts",
      "location": "East US 2",
      "apiVersion": "2015-10-01-preview",
      "dependsOn": [ "[concat('Microsoft.DataLakeStore/accounts/',parameters('adlStoreName'))]" ],
      "tags": { },
      "properties": {
        "defaultDataLakeStoreAccount": "[parameters('adlStoreName')]",
        "dataLakeStoreAccounts": [
          { "name": "[parameters('adlStoreName')]" }
        ]
      }
    }
  ],
  "outputs": {
    "adlAnalyticsAccount": {
      "type": "object",
      "value": "[reference(resourceId('Microsoft.DataLakeAnalytics/accounts',parameters('adlAnalyticsName')))]"
    },
    "adlStoreAccount": {
      "type": "object",
      "value": "[reference(resourceId('Microsoft.DataLakeStore/accounts',parameters('adlStoreName')))]"
    }
  }
}
```

## <a name="next-steps"></a>Próximas etapas
* [Visão geral da Análise do Microsoft Azure Data Lake](data-lake-analytics-overview.md)
* Introdução ao Data Lake Analytics usando o [Portal do Azure](data-lake-analytics-get-started-portal.md) | [Azure PowerShell](data-lake-analytics-get-started-powershell.md) | [CLI 2.0](data-lake-analytics-get-started-cli2.md)
* Gerenciar o Azure Data Lake Analytics usando o [Portal do Azure](data-lake-analytics-manage-use-portal.md) | [Azure PowerShell](data-lake-analytics-manage-use-powershell.md) | [CLI](data-lake-analytics-manage-use-cli.md) 
