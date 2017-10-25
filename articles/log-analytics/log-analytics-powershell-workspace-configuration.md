---
title: "Usar o PowerShell para criar e configurar um espaço de trabalho do Log Analytics | Microsoft Docs"
description: "O Log Analytics usa dados de servidores em sua infraestrutura local ou na nuvem. Você pode coletar dados da máquina do armazenamento do Azure quando gerados pelo diagnóstico do Azure."
services: log-analytics
documentationcenter: 
author: richrundmsft
manager: jochan
editor: 
ms.assetid: 3b9b7ade-3374-4596-afb1-51b695f481c2
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: powershell
ms.topic: article
ms.date: 11/21/2016
ms.author: richrund
ms.openlocfilehash: 6807ab67e3593da82c147669b29bfdae3b6c967c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="manage-log-analytics-using-powershell"></a>Gerenciar o Log Analytics usando o PowerShell
Você pode usar os [cmdlets do PowerShell do Log Analytics](https://msdn.microsoft.com/library/mt188224\(v=azure.300\).aspx) para executar várias funções no Log Analytics de uma linha de comando ou como parte de um script.  Os exemplos das tarefas que você pode executar com o PowerShell incluem:

* Criar um espaço de trabalho
* Adicionar ou remover uma solução
* Importar e exportar pesquisas salvas
* Criar um grupo de computadores
* Habilitar coleta de logs do IIS de computadores com o agente do Windows instalado
* Coletar contadores de desempenho de computadores Linux e Windows
* Coletar eventos de syslog em computadores Linux 
* Coletar eventos dos logs de eventos do Windows
* Coletar logs de eventos personalizados
* Adicionar o agente de análise de log a uma máquina virtual do Azure
* Configurar a análise de log para indexar os dados coletados usando o diagnóstico do Azure

Este artigo fornece dois exemplos de código que ilustram algumas das funções que podem ser executadas do PowerShell.  Você pode consultar a [referência do cmdlet do PowerShell do Log Analytics](https://msdn.microsoft.com/library/mt188224\(v=azure.300\).aspx) para outras funções.

> [!NOTE]
> O Log Analytics chamava-se Operational Insights, e é por isso que esse é o nome usado nos cmdlets.
> 
> 

## <a name="prerequisites"></a>Pré-requisitos
Esses exemplos funcionam com a versão 2.3.0 ou posterior do módulo AzureRm.OperationalInsights.


## <a name="create-and-configure-a-log-analytics-workspace"></a>Criar e configurar um espaço de trabalho do Log Analytics
O exemplo de script a seguir ilustra como:

1. Criar um espaço de trabalho
2. Listar as soluções disponíveis
3. Adicionar soluções ao espaço de trabalho
4. Importar pesquisas salvas
5. Exportar pesquisas salvas
6. Criar um grupo de computadores
7. Habilitar coleta de logs do IIS de computadores com o agente do Windows instalado
8. Coletar contadores de desempenho de disco lógico de computadores Linux (% Used Inodes; Free Megabytes; % Used Space; Disk Transfers/sec; Disk Reads/sec; Disk Writes/sec)
9. Coletar eventos de syslog em computadores Linux
10. Coletar eventos de Erro e Aviso no Log de Eventos do Aplicativo dos computadores Windows
11. Coletar o contador de desempenho de Mbytes Disponíveis de Memória de computadores Windows
12. Coletar um log personalizado 

```

$ResourceGroup = "oms-example"
$WorkspaceName = "log-analytics-" + (Get-Random -Maximum 99999) # workspace names need to be unique - Get-Random helps with this for the example code
$Location = "westeurope"

# List of solutions to enable
$Solutions = "Security", "Updates", "SQLAssessment"

# Saved Searches to import
$ExportedSearches = @"
[
    {
        "Category":  "My Saved Searches",
        "DisplayName":  "WAD Events (All)",
        "Query":  "Type=Event SourceSystem:AzureStorage ",
        "Version":  1
    },
    {        
        "Category":  "My Saved Searches",
        "DisplayName":  "Current Disk Queue Length",
        "Query":  "Type=Perf ObjectName=LogicalDisk InstanceName=\"C:\" CounterName=\"Current Disk Queue Length\"",
        "Version":  1
    }
]
"@ | ConvertFrom-Json

# Custom Log to collect
$CustomLog = @"
{
    "customLogName": "sampleCustomLog1", 
    "description": "Example custom log datasource", 
    "inputs": [
        { 
            "location": { 
            "fileSystemLocations": { 
                "windowsFileTypeLogPaths": [ "e:\\iis5\\*.log" ], 
                "linuxFileTypeLogPaths": [ "/var/logs" ] 
                } 
            }, 
        "recordDelimiter": { 
            "regexDelimiter": { 
                "pattern": "\\n", 
                "matchIndex": 0, 
                "matchIndexSpecified": true, 
                "numberedGroup": null 
                } 
            } 
        }
    ], 
    "extractions": [
        { 
            "extractionName": "TimeGenerated", 
            "extractionType": "DateTime", 
            "extractionProperties": { 
                "dateTimeExtraction": { 
                    "regex": null, 
                    "joinStringRegex": null 
                    } 
                } 
            }
        ] 
    }
"@

# Create the resource group if needed
try {
    Get-AzureRmResourceGroup -Name $ResourceGroup -ErrorAction Stop
} catch {
    New-AzureRmResourceGroup -Name $ResourceGroup -Location $Location
}

# Create the workspace
New-AzureRmOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku Standard -ResourceGroupName $ResourceGroup

# List all solutions and their installation status
Get-AzureRmOperationalInsightsIntelligencePacks -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Add solutions
foreach ($solution in $Solutions) {
    Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -IntelligencePackName $solution -Enabled $true
}

#List enabled solutions
(Get-AzureRmOperationalInsightsIntelligencePacks -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName).Where({($_.enabled -eq $true)})

# Import Saved Searches
foreach ($search in $ExportedSearches) {
    $id = $search.Category + "|" + $search.DisplayName
    New-AzureRmOperationalInsightsSavedSearch -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId $id -DisplayName $search.DisplayName -Category $search.Category -Query $search.Query -Version $search.Version
}

# Export Saved Searches
(Get-AzureRmOperationalInsightsSavedSearch -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName).Value.Properties | ConvertTo-Json 

# Create Computer Group based on a query
New-AzureRmOperationalInsightsComputerGroup -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId "My Web Servers" -DisplayName "Web Servers" -Category "My Saved Searches" -Query "Computer=""web*"" | distinct Computer" -Version 1

# Create a computer group based on names (up to 5000)
$computerGroup = """servername1.contoso.com"",""servername2.contoso.com"",""servername3.contoso.com"",""servername4.contoso.com"""
New-AzureRmOperationalInsightsComputerGroup -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId "My Named Servers" -DisplayName "Named Servers" -Category "My Saved Searches" -Query $computerGroup -Version 1

# Enable IIS Log Collection using agent
Enable-AzureRmOperationalInsightsIISLogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Linux Perf
New-AzureRmOperationalInsightsLinuxPerformanceObjectDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -ObjectName "Logical Disk" -InstanceName "*"  -CounterNames @("% Used Inodes", "Free Megabytes", "% Used Space", "Disk Transfers/sec", "Disk Reads/sec", "Disk Reads/sec", "Disk Writes/sec") -IntervalSeconds 20  -Name "Example Linux Disk Performance Counters"
Enable-AzureRmOperationalInsightsLinuxCustomLogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Linux Syslog
New-AzureRmOperationalInsightsLinuxSyslogDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -Facility "kern" -CollectEmergency -CollectAlert -CollectCritical -CollectError -CollectWarning -Name "Example kernal syslog collection"
Enable-AzureRmOperationalInsightsLinuxSyslogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Windows Event
New-AzureRmOperationalInsightsWindowsEventDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -EventLogName "Application" -CollectErrors -CollectWarnings -Name "Example Application Event Log"

# Windows Perf
New-AzureRmOperationalInsightsWindowsPerformanceCounterDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -ObjectName "Memory" -InstanceName "*" -CounterName "Available MBytes" -IntervalSeconds 20 -Name "Example Windows Performance Counter"

# Custom Logs
New-AzureRmOperationalInsightsCustomLogDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -CustomLogRawJson "$CustomLog" -Name "Example Custom Log Collection"

```

## <a name="configuring-log-analytics-to-index-azure-diagnostics"></a>Configuração do Log Analytics para indexar os diagnósticos do Azure
Para o monitoramento de recursos do Azure realizado sem o uso de agente, os recursos precisam ter o diagnóstico do Azure habilitado e configurado para gravar em um espaço de trabalho do Log Analytics. Essa abordagem envia dados diretamente para o Log Analytics e não exige que dados sejam gravados para uma conta de armazenamento. Os recursos com suporte incluem:

| Tipo de recurso | Logs | Métricas |
| --- | --- | --- |
| Gateways do Aplicativo    | Sim | Sim |
| Contas de automação     | Sim | |
| Contas do Lote          | Sim | Sim |
| Data Lake Analytics     | Sim | | 
| Data Lake Store         | Sim | |
| Pool SQL Elástico        |     | Sim |
| Namespace do Hub de Eventos     |     | Sim |
| Hubs IoT                |     | Sim |
| Cofre da Chave               | Sim | |
| Balanceadores de Carga          | Sim | |
| Aplicativos Lógicos              | Sim | Sim |
| Grupos de segurança de rede | Sim | |
| Cache Redis             |     | Sim |
| Serviços de pesquisa         | Sim | Sim |
| Namespace do Barramento de Serviço   |     | Sim |
| SQL (v12)               |     | Sim |
| Sites               |     | Sim |
| Farms do servidor Web        |     | Sim |

Para obter os detalhes das métricas disponíveis, consulte [métricas compatíveis com o Azure Monitor](../monitoring-and-diagnostics/monitoring-supported-metrics.md).

Para obter os detalhes dos logs disponíveis, consulte [serviços e esquema com suporte para logs de diagnóstico](../monitoring-and-diagnostics/monitoring-diagnostic-logs-schema.md).

```
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$resourceId = "/SUBSCRIPTIONS/ec11ca60-1234-491e-5678-0ea07feae25c/RESOURCEGROUPS/DEMO/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/DEMO" 

Set-AzureRmDiagnosticSetting -ResourceId $resourceId -WorkspaceId $workspaceId -Enabled $true
```

Você também pode usar o cmdlet anterior para coletar logs de recursos em assinaturas diferentes. O cmdlet é capaz de trabalhar em assinaturas, pois você está fornecendo a id do recurso que está criando os logs e do espaço de trabalho para o qual os logs são enviados.


## <a name="configuring-log-analytics-to-index-azure-diagnostics-from-storage"></a>Configuração do Log Analytics para indexar os diagnósticos do Azure a partir do armazenamento
Para coletar dados de log de dentro de uma instância em execução de um serviço de nuvem clássico ou em um cluster de service fabric, você precisa primeiro escrever os dados no armazenamento do Azure. O Log Analytics é, então, configurado para coletar os logs da conta de armazenamento. Os recursos com suporte incluem:

* Serviços de nuvem clássicos (funções de web e de trabalho)
* Clusters do Service Fabric

O exemplo a seguir mostra como:

1. Listar as contas de armazenamento existentes e os locais de onde o Log Analytics indexará os dados
2. Criar uma configuração para ler de uma conta de armazenamento
3. Atualizar a configuração criada recentemente para indexar dados de locais adicionais
4. Excluir a configuração recém-criada

```
# validTables = "WADWindowsEventLogsTable", "LinuxsyslogVer2v0", "WADServiceFabric*EventTable", "WADETWEventTable" 
$workspace = (Get-AzureRmOperationalInsightsWorkspace).Where({$_.Name -eq "your workspace name"})

# Update these two lines with the storage account resource ID and the storage account key for the storage account you want to Log Analytics to  
$storageId = "/subscriptions/ec11ca60-1234-491e-5678-0ea07feae25c/resourceGroups/demo/providers/Microsoft.Storage/storageAccounts/wadv2storage"
$key = "abcd=="

# List existing insights
Get-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name

# Create a new insight
New-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" -StorageAccountResourceId $storageId -StorageAccountKey $key -Tables @("WADWindowsEventLogsTable") -Containers @("wad-iis-logfiles")

# Update existing insight
Set-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" -Tables @("WADWindowsEventLogsTable", "WADETWEventTable") -Containers @("wad-iis-logfiles")

# Remove the insight
Remove-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" 

```

Você também pode usar o script anterior para coletar logs de contas de armazenamento em assinaturas diferentes. O script é capaz de trabalhar em assinaturas, pois você está fornecendo a id do recurso da conta de armazenamento e uma chave de acesso correspondente. Quando você altera a chave de acesso, você precisa atualizar as informações de armazenamento para a nova chave.


## <a name="next-steps"></a>Próximas etapas
* [Examinar cmdlets do PowerShell do Log Analytics](https://msdn.microsoft.com/library/mt188224\(v=azure.300\).aspx) para obter informações adicionais sobre como usar o PowerShell para a configuração do Log Analytics.

