---
title: Consultar dados exportados de Azure Monitor usando o Data Explorer do Azure (versão prévia)
description: Use o Azure Data Explorer para consultar dados que foram exportados de seu espaço de trabalho do Log Analytics para uma conta de armazenamento do Azure.
author: osalzberg
ms.author: bwren
ms.reviewer: bwren
ms.topic: conceptual
ms.date: 10/13/2020
ms.openlocfilehash: 5eff593075db118b23d74147e33b40eb4402193c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102031150"
---
# <a name="query-exported-data-from-azure-monitor-using-azure-data-explorer-preview"></a>Consultar dados exportados de Azure Monitor usando o Data Explorer do Azure (versão prévia)
A exportação de dados de Azure Monitor para uma conta de armazenamento do Azure permite a retenção de baixo custo e a capacidade de realocar os logs para regiões diferentes. Use o Data Explorer do Azure para consultar dados que foram exportados de seus espaços de trabalho do Log Analytics. Uma vez configuradas, as tabelas com suporte enviadas de seus espaços de trabalho para uma conta de armazenamento do Azure estarão disponíveis como uma fonte de dados para o Azure Data Explorer.

O fluxo do processo é o seguinte: 

1.  Exportar dados do espaço de trabalho Log Analytics para a conta de armazenamento do Azure.
2.  Crie uma tabela externa no cluster de Data Explorer do Azure e o mapeamento para os tipos de dados.
3.  Consultar dados do Data Explorer do Azure.

:::image type="content" source="media/azure-data-explorer-query-storage/exported-data-query.png" alt-text="Fluxo de consulta de dados exportados do Azure Data Explorer.":::



## <a name="send-data-to-azure-storage"></a>Enviar dados para o armazenamento do Azure
Azure Monitor logs podem ser exportados para uma conta de armazenamento do Azure usando qualquer uma das opções a seguir.

- Para exportar todos os dados do seu espaço de trabalho do Log Analytics para uma conta de armazenamento do Azure ou Hub de eventos, use o recurso de exportação de dados do espaço de trabalho Log Analytics dos logs do Azure Monitor. Confira [log Analytics exportação de dados de espaço de trabalho no Azure monitor (versão prévia)](./logs-data-export.md)
- Exportação agendada de uma consulta de log usando um aplicativo lógico. Isso é semelhante ao recurso de exportação de dados, mas permite que você envie dados filtrados ou agregados para o armazenamento do Azure. Esse método está sujeito a [limites de consulta de log](../service-limits.md#log-analytics-workspaces)  , consulte [arquivar dados de log Analytics espaço de trabalho no armazenamento do Azure usando o aplicativo lógico](./logs-export-logic-app.md).
- Exportar uma vez usando um aplicativo lógico. Consulte [conector de logs de Azure monitor para aplicativos lógicos e automação de energia](./logicapp-flow-connector.md).
- Exportar uma vez para o computador local usando o script do PowerShell. Consulte [Invoke-AzOperationalInsightsQueryExport](https://www.powershellgallery.com/packages/Invoke-AzOperationalInsightsQueryExport).

> [!TIP]
> Você pode usar um cluster de Data Explorer do Azure existente ou criar um novo cluster dedicado com as configurações necessárias.

## <a name="create-an-external-table-located-in-azure-blob-storage"></a>Criar uma tabela externa localizada no armazenamento de BLOBs do Azure
Use [tabelas externas](/azure/data-explorer/kusto/query/schema-entities/externaltables) para vincular o Azure data Explorer a uma conta de armazenamento do Azure. Uma tabela externa é uma entidade de esquema Kusto que faz referência a dados armazenados fora de um banco de dados Kusto. Assim como as tabelas, uma tabela externa tem um esquema bem definido. Ao contrário das tabelas, os dados são armazenados e gerenciados fora de um cluster Kusto. Os dados exportados da seção anterior são salvos em linhas JSON.

Para criar uma referência, você precisa do esquema da tabela exportada. Use o operador [GetSchema](/azure/data-explorer/kusto/query/getschemaoperator) da log Analytics para recuperar essas informações que incluem as colunas da tabela e seus tipos de dados.

:::image type="content" source="media\azure-data-explorer-query-storage\exported-data-map-schema.jpg" alt-text="Log Analytics esquema de tabela.":::

Agora você pode usar a saída para criar a consulta Kusto para compilar a tabela externa.
Seguindo as orientações em [criar e alterar tabelas externas no armazenamento do Azure ou Azure data Lake](/azure/data-explorer/kusto/management/external-tables-azurestorage-azuredatalake), crie uma tabela externa em um formato JSON e, em seguida, execute a consulta de seu banco de dados de data Explorer do Azure.

>[!NOTE]
>A criação da tabela externa é criada a partir de dois processos. A primeira é criar a tabela externa, enquanto a segunda está criando o mapeamento.

O script do PowerShell a seguir criará os comandos [Create](/azure/data-explorer/kusto/management/external-tables-azurestorage-azuredatalake#create-external-table-mapping) para a tabela e o mapeamento.

```powershell
PARAM(
    $resourcegroupname, #The name of the Azure resource group
    $TableName, # The Log Analytics table you wish to convert to external table
    $MapName, # The name of the map
    $subscriptionId, #The ID of the subscription
    $WorkspaceId, # The Log Analytics WorkspaceId
    $WorkspaceName, # The Log Analytics workspace name
    $BlobURL, # The Blob URL where the data is saved
    $ContainerAccessKey, # The blob container Access Key (Option to add a SAS url)
    $ExternalTableName = $null # The External Table name, null to use the same name
)

if($null -eq $ExternalTableName) {
    $ExternalTableName = $TableName
}

$query = $TableName + ' | getschema | project ColumnName, DataType'

$output = (Invoke-AzOperationalInsightsQuery -WorkspaceId $WorkspaceId -Query $query).Results

$FirstCommand = @()
$SecondCommand = @()

foreach ($record in $output) {
    if ($record.DataType -eq 'System.DateTime') {
        $dataType = 'datetime'
    } else {
        $dataType = 'string'
    }
    $FirstCommand += $record.ColumnName + ":" + "$dataType" + ","
    $SecondCommand += "{`"column`":" + "`"" + $record.ColumnName + "`"," + "`"datatype`":`"$dataType`",`"path`":`"$." + $record.ColumnName + "`"},"
}
$schema = ($FirstCommand -join '') -replace ',$'
$mapping = ($SecondCommand -join '') -replace ',$'

$CreateExternal = @'
.create external table {0} ({1})
kind=blob
partition by (TimeGeneratedPartition:datetime = bin(TimeGenerated, 1min))
pathformat = (datetime_pattern("'y='yyyy'/m='MM'/d='dd'/h='HH'/m='mm", TimeGeneratedPartition))
dataformat=multijson
(
   h@'{2}/WorkspaceResourceId=/subscriptions/{4}/resourcegroups/{6}/providers/microsoft.operationalinsights/workspaces/{5};{3}'
)
with
(
   docstring = "Docs",
   folder = "ExternalTables"
)
'@ -f $TableName, $schema, $BlobURL, $ContainerAccessKey, $subscriptionId, $WorkspaceName, $resourcegroupname,$WorkspaceId

$createMapping = @'
.create external table {0} json mapping "{1}" '[{2}]'
'@ -f $ExternalTableName, $MapName, $mapping

Write-Host -ForegroundColor Red 'Copy and run the following commands (one by one), on your Azure Data Explorer cluster query window to create the external table and mappings:'
write-host -ForegroundColor Green $CreateExternal
Write-Host -ForegroundColor Green $createMapping
```

A imagem a seguir mostra e exemplo da saída.

:::image type="content" source="media/azure-data-explorer-query-storage/external-table-create-command-output.png" alt-text="Saída de comando de criação externa.":::

[![Exemplo de saída](media/azure-data-explorer-query-storage/external-table-create-command-output.png)](media/azure-data-explorer-query-storage/external-table-create-command-output.png#lightbox)

>[!TIP]
>* Copie, Cole e execute a saída do script em sua ferramenta de cliente do Azure Data Explorer para criar a tabela e o mapeamento.
>* Para usar todos os dados dentro do contêiner, altere o script e altere a URL para ' https://your.blob.core.windows.net/containername ; SecKey'

## <a name="query-the-exported-data-from-azure-data-explorer"></a>Consultar os dados exportados do Azure Data Explorer 

Depois de configurar o mapeamento, você pode consultar os dados exportados do Azure Data Explorer. Sua consulta requer a função [external_table](/azure/data-explorer/kusto/query/externaltablefunction) , como no exemplo a seguir.

```kusto
external_table("HBTest","map") | take 10000
```

[![Consultar Log Analytics dados exportados](media/azure-data-explorer-query-storage/external-table-query.png)](media/azure-data-explorer-query-storage/external-table-query.png#lightbox)

## <a name="next-steps"></a>Próximas etapas

- Aprenda a [escrever consultas no Azure data Explorer](/azure/data-explorer/write-queries)