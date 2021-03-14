---
title: Monitorando arquivos do Azure | Microsoft Docs
description: Saiba como monitorar o desempenho e a disponibilidade dos arquivos do Azure. Monitore os dados dos arquivos do Azure, saiba mais sobre a configuração e analise os dados de métrica e de log.
author: normesta
services: storage
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 3/02/2021
ms.author: normesta
ms.reviewer: fryu
ms.custom: monitoring, devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 16a899b7f0e40c2eee91d1dd445f0992572a9dda
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/13/2021
ms.locfileid: "103418157"
---
# <a name="monitoring-azure-files"></a>Monitorando arquivos do Azure

Quando você tem aplicativos e processos de negócios críticos que dependem de recursos do Azure, recomendamos monitorar a disponibilidade, o desempenho e a operação desses recursos. Este artigo descreve os dados de monitoramento gerados pelos arquivos do Azure e como você pode usar os recursos do Azure Monitor para analisar alertas sobre esses dados.

## <a name="monitor-overview"></a>Visão geral do Monitor

A página de **visão geral** no portal do Azure para cada recurso de arquivos do Azure inclui uma breve exibição do uso de recursos, como solicitações e cobrança por hora. Essas informações são úteis, mas apenas uma pequena quantidade de dados de monitoramento está disponível. Alguns desses dados são coletados automaticamente e estão disponíveis para análise assim que você criar o recurso. Você pode habilitar tipos adicionais de coleta de dados com algumas configurações.

## <a name="what-is-azure-monitor"></a>O que é o Azure Monitor?
Os arquivos do Azure criam dados de monitoramento usando [Azure monitor](../../azure-monitor/overview.md), que é um serviço de monitoramento de pilha completo no Azure. O Azure Monitor oferece um conjunto completo de recursos para monitorar os recursos do Azure e recursos em outras nuvens e locais. 

Comece com o artigo [monitorando os recursos do Azure com o Azure monitor](../../azure-monitor/essentials/monitor-azure-resource.md), que descreve o seguinte:

- O que é o Azure Monitor?
- Custos associados ao monitoramento
- Monitoramento de dados coletados no Azure
- Configuração de coleta de dados
- Ferramentas padrão no Azure para analisar e alertar sobre dados de monitoramento

As seções a seguir se baseiam neste artigo descrevendo os dados específicos coletados dos arquivos do Azure. Os exemplos mostram como configurar a coleta de dados e analisar esses dados com as ferramentas do Azure.

## <a name="monitoring-data"></a>Dados de monitoramento

Os arquivos do Azure coletam os mesmos tipos de dados de monitoramento que outros recursos do Azure, que são descritos em [monitoramento de dados de recursos do Azure](../../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data). 

Consulte [referência de dados de monitoramento de arquivo do Azure](storage-files-monitoring-reference.md) para obter informações detalhadas sobre as métricas de métricas e logs criadas pelos arquivos do Azure.

As métricas e os logs do Azure Monitor oferecem suporte apenas às contas de armazenamento do Azure Resource Manager. O Azure Monitor não tem suporte a contas de armazenamento clássicas. Se você quiser usar as métricas ou os logs nas contas de armazenamento clássicas, precisará migrar para a conta de armazenamento do Azure Resource Manager. Confira [Migrar para o Azure Resource Manager](../../virtual-machines/migration-classic-resource-manager-overview.md).

## <a name="collection-and-routing"></a>Coleta e roteamento

As métricas de plataforma e o log de atividades são coletados automaticamente, mas podem ser roteados para outros locais usando uma configuração de diagnóstico. 

Para coletar logs de recursos, você deve criar uma configuração de diagnóstico. Ao criar a configuração, escolha **arquivo** como o tipo de armazenamento para o qual você deseja habilitar os logs. Em seguida, especifique uma das seguintes categorias de operações para as quais você deseja coletar logs. 

| Categoria | Descrição |
|:---|:---|
| StorageRead | Operações de leitura em objetos. |
| StorageWrite | Operações de gravação em objetos. |
| StorageDelete | Excluir operações em objetos. |

Para obter a lista de operações de SMB e REST registradas, consulte [operações registradas de armazenamento e mensagens de status](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) e [referência de dados de monitoramento de arquivos do Azure](storage-files-monitoring-reference.md).

## <a name="creating-a-diagnostic-setting"></a>Criando uma configuração de diagnóstico

Você pode criar uma configuração de diagnóstico usando o portal do Azure, o PowerShell, o CLI do Azure ou um modelo de Azure Resource Manager.

> [!NOTE]
> Os logs do Armazenamento do Microsoft Azure no Azure Monitor estão em versão preliminar pública e disponíveis para teste de versão preliminar em todas as regiões de nuvem pública. Essa visualização habilita logs para BLOBs (que inclui Azure Data Lake Storage Gen2), arquivos, filas e tabelas. Esse recurso está disponível para todas as contas de armazenamento criadas com o modelo de implantação Azure Resource Manager. Consulte [visão geral da conta de armazenamento](../common/storage-account-overview.md).

Para obter diretrizes gerais, consulte [criar configuração de diagnóstico para coletar logs e métricas de plataforma no Azure](../../azure-monitor/essentials/diagnostic-settings.md).

### <a name="azure-portal"></a>[Portal do Azure](#tab/azure-portal)

1. Entre no portal do Azure.

2. Navegue para sua conta de armazenamento.

3. Na seção **monitoramento** , clique em **configurações de diagnóstico (versão prévia)**.

   > [!div class="mx-imgBorder"]
   > ![portal – Logs de diagnóstico](media/storage-files-monitoring/diagnostic-logs-settings-pane.png)   

4. Escolha **arquivo** como o tipo de armazenamento para o qual você deseja habilitar os logs.

5. Clique em **Adicionar configuração de diagnóstico**.

   > [!div class="mx-imgBorder"]
   > ![Portal-logs de recursos – adicionar configuração de diagnóstico](media/storage-files-monitoring/diagnostic-logs-settings-pane-2.png)

   A página **configurações de diagnóstico** é exibida.

   > [!div class="mx-imgBorder"]
   > ![Página logs de recursos](media/storage-files-monitoring/diagnostic-logs-page.png)

6. No campo **nome** da página, insira um nome para essa configuração de log de recursos. Em seguida, selecione as operações que você deseja registrar (operações de leitura, gravação e exclusão) e onde deseja que os logs sejam enviados.

#### <a name="archive-logs-to-a-storage-account"></a>Arquivar logs em uma conta de armazenamento

Se você optar por arquivar seus logs em uma conta de armazenamento, pagará pelo volume de logs que são enviados para a conta de armazenamento. Para obter preços específicos, consulte a seção de **logs da plataforma** da página de [preços do Azure monitor](https://azure.microsoft.com/pricing/details/monitor/#platform-logs) .

1. Marque a caixa de seleção **arquivar em uma conta de armazenamento** e, em seguida, clique no botão **Configurar** .

   > [!div class="mx-imgBorder"]   
   > ![Página Configurações de diagnóstico armazenamento de arquivos](media/storage-files-monitoring/diagnostic-logs-settings-pane-archive-storage.png)

2. Na lista suspensa **conta de armazenamento** , selecione a conta de armazenamento na qual você deseja arquivar os logs, clique no botão **OK** e, em seguida, clique no botão **salvar** .

   [!INCLUDE [no retention policy](../../../includes/azure-storage-logs-retention-policy.md)]

   > [!NOTE]
   > Antes de escolher uma conta de armazenamento como destino de exportação, consulte [arquivar logs de recursos do Azure](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage) para entender os pré-requisitos na conta de armazenamento.

#### <a name="stream-logs-to-azure-event-hubs"></a>Transmitir logs para os hubs de eventos do Azure

Se você optar por transmitir os logs para um hub de eventos, pagará pelo volume de logs que são enviados para o Hub de eventos. Para obter preços específicos, consulte a seção de **logs da plataforma** da página de [preços do Azure monitor](https://azure.microsoft.com/pricing/details/monitor/#platform-logs) .

1. Marque a caixa de seleção **transmitir para um hub de eventos** e, em seguida, clique no botão **Configurar** .

2. No painel **selecionar um hub de eventos** , escolha o namespace, o nome e o nome da política do hub de eventos para o qual você deseja transmitir os logs. 

   > [!div class="mx-imgBorder"]
   > ![Página de configurações de diagnóstico Hub de eventos](media/storage-files-monitoring/diagnostic-logs-settings-pane-event-hub.png)

3. Clique no botão **OK** e, em seguida, clique no botão **salvar** .

#### <a name="send-logs-to-azure-log-analytics"></a>Enviar logs para o Azure Log Analytics

1. Marque a caixa de seleção **Enviar para log Analytics** , selecione um espaço de trabalho do log Analytics e, em seguida, clique no botão **salvar** .

   > [!div class="mx-imgBorder"]   
   > ![Página Configurações de diagnóstico log Analytics](media/storage-files-monitoring/diagnostic-logs-settings-pane-log-analytics.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Abra uma janela de comando do Windows PowerShell e entre na sua assinatura do Azure usando o `Connect-AzAccount` comando. Em seguida, siga as instruções na tela.

   ```powershell
   Connect-AzAccount
   ```

2. Defina sua assinatura ativa para a assinatura da conta de armazenamento para a qual você deseja habilitar o registro em log.

   ```powershell
   Set-AzContext -SubscriptionId <subscription-id>
   ```

#### <a name="archive-logs-to-a-storage-account"></a>Arquivar logs em uma conta de armazenamento

Se você optar por arquivar seus logs em uma conta de armazenamento, pagará pelo volume de logs que são enviados para a conta de armazenamento. Para obter preços específicos, consulte a seção de **logs da plataforma** da página de [preços do Azure monitor](https://azure.microsoft.com/pricing/details/monitor/#platform-logs) .

Habilite logs usando o cmdlet [set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) do PowerShell junto com o `StorageAccountId` parâmetro.

```powershell
Set-AzDiagnosticSetting -ResourceId <storage-service-resource-id> -StorageAccountId <storage-account-resource-id> -Enabled $true -Category <operations-to-log> 
```

Substitua o `<storage-service-resource--id>` espaço reservado neste trecho pela ID de recurso do serviço de arquivo do Azure. Para encontrar o ID do recurso no portal do Azure, você pode abrir a página **Propriedades** da sua conta de armazenamento.

Você pode usar `StorageRead` , `StorageWrite` e `StorageDelete` para o valor do parâmetro **Category** .

[!INCLUDE [no retention policy](../../../includes/azure-storage-logs-retention-policy.md)]

Aqui está um exemplo:

`Set-AzDiagnosticSetting -ResourceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/fileServices/default -StorageAccountId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount -Enabled $true -Category StorageWrite,StorageDelete`

Para obter uma descrição de cada parâmetro, consulte [arquivar logs de recursos do Azure por meio de Azure PowerShell](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage).

#### <a name="stream-logs-to-an-event-hub"></a>Transmitir logs para um hub de eventos

Se você optar por transmitir os logs para um hub de eventos, pagará pelo volume de logs que são enviados para o Hub de eventos. Para obter preços específicos, consulte a seção de **logs da plataforma** da página de [preços do Azure monitor](https://azure.microsoft.com/pricing/details/monitor/#platform-logs) .

Habilite logs usando o cmdlet do PowerShell [set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) com o `EventHubAuthorizationRuleId` parâmetro.

```powershell
Set-AzDiagnosticSetting -ResourceId <storage-service-resource-id> -EventHubAuthorizationRuleId <event-hub-namespace-and-key-name> -Enabled $true -Category <operations-to-log> -RetentionEnabled <retention-bool> -RetentionInDays <number-of-days>
```

Aqui está um exemplo:

`Set-AzDiagnosticSetting -ResourceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/fileServices/default -EventHubAuthorizationRuleId /subscriptions/20884142-a14v3-4234-5450-08b10c09f4/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhubnamespace/authorizationrules/RootManageSharedAccessKey -Enabled $true -Category StorageDelete`

Para obter uma descrição de cada parâmetro, consulte os [dados de fluxo para hubs de eventos por meio de cmdlets do PowerShell](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs).

#### <a name="send-logs-to-log-analytics"></a>Enviar logs ao Log Analytics

Habilite logs usando o cmdlet do PowerShell [set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) com o `WorkspaceId` parâmetro.

```powershell
Set-AzDiagnosticSetting -ResourceId <storage-service-resource-id> -WorkspaceId <log-analytics-workspace-resource-id> -Enabled $true -Category <operations-to-log> -RetentionEnabled <retention-bool> -RetentionInDays <number-of-days>
```

Aqui está um exemplo:

`Set-AzDiagnosticSetting -ResourceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/fileServices/default -WorkspaceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.OperationalInsights/workspaces/my-analytic-workspace -Enabled $true -Category StorageDelete`

Para obter mais informações, consulte [transmitir logs de recursos do Azure para log Analytics espaço de trabalho no Azure monitor](../../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace).

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

1. Primeiro, abra o [Azure Cloud Shell](../../cloud-shell/overview.md)ou, se você [instalou](/cli/azure/install-azure-cli) a CLI do Azure localmente, abra um aplicativo de console de comando, como o Windows PowerShell.

2. Se sua identidade estiver associada a mais de uma assinatura, defina sua assinatura ativa para a assinatura da conta de armazenamento para a qual você deseja habilitar os logs.

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   Substitua o valor de espaço reservado `<subscription-id>` pela ID da sua assinatura.

#### <a name="archive-logs-to-a-storage-account"></a>Arquivar logs em uma conta de armazenamento

Se você optar por arquivar seus logs em uma conta de armazenamento, pagará pelo volume de logs que são enviados para a conta de armazenamento. Para obter preços específicos, consulte a seção de **logs da plataforma** da página de [preços do Azure monitor](https://azure.microsoft.com/pricing/details/monitor/#platform-logs) .

Habilite logs usando o comando [AZ monitor Diagnostics-Settings Create](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create) .

```azurecli-interactive
az monitor diagnostic-settings create --name <setting-name> --storage-account <storage-account-name> --resource <storage-service-resource-id> --resource-group <resource-group> --logs '[{"category": <operations>, "enabled": true}]'
```

Substitua o `<storage-service-resource--id>` espaço reservado neste trecho pelo serviço de armazenamento de BLOB da ID de recurso. Para encontrar o ID do recurso no portal do Azure, você pode abrir a página **Propriedades** da sua conta de armazenamento.

Você pode usar `StorageRead` , `StorageWrite` e `StorageDelete` para o valor do parâmetro **Category** .

[!INCLUDE [no retention policy](../../../includes/azure-storage-logs-retention-policy.md)]

Aqui está um exemplo:

`az monitor diagnostic-settings create --name setting1 --storage-account mystorageaccount --resource /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount/fileServices/default --resource-group myresourcegroup --logs '[{"category": StorageWrite, "enabled": true}]'`

Para obter uma descrição de cada parâmetro, consulte os [logs de recursos de arquivo por meio do CLI do Azure](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage).

#### <a name="stream-logs-to-an-event-hub"></a>Transmitir logs para um hub de eventos

Se você optar por transmitir os logs para um hub de eventos, pagará pelo volume de logs que são enviados para o Hub de eventos. Para obter preços específicos, consulte a seção de **logs da plataforma** da página de [preços do Azure monitor](https://azure.microsoft.com/pricing/details/monitor/#platform-logs) .

Habilite logs usando o comando [AZ monitor Diagnostics-Settings Create](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create) .

```azurecli-interactive
az monitor diagnostic-settings create --name <setting-name> --event-hub <event-hub-name> --event-hub-rule <event-hub-namespace-and-key-name> --resource <storage-account-resource-id> --logs '[{"category": <operations>, "enabled": true "retentionPolicy": {"days": <number-days>, "enabled": <retention-bool}}]'
```

Aqui está um exemplo:

`az monitor diagnostic-settings create --name setting1 --event-hub myeventhub --event-hub-rule /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhubnamespace/authorizationrules/RootManageSharedAccessKey --resource /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount/fileServices/default --logs '[{"category": StorageDelete, "enabled": true }]'`

Para obter uma descrição de cada parâmetro, consulte os [dados de fluxo para os hubs de eventos por meio de CLI do Azure](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs).

#### <a name="send-logs-to-log-analytics"></a>Enviar logs ao Log Analytics

Habilite logs usando o comando [AZ monitor Diagnostics-Settings Create](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create) .

```azurecli-interactive
az monitor diagnostic-settings create --name <setting-name> --workspace <log-analytics-workspace-resource-id> --resource <storage-account-resource-id> --logs '[{"category": <category name>, "enabled": true "retentionPolicy": {"days": <days>, "enabled": <retention-bool}}]'
```

Aqui está um exemplo:

`az monitor diagnostic-settings create --name setting1 --workspace /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.OperationalInsights/workspaces/my-analytic-workspace --resource /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount/fileServices/default --logs '[{"category": StorageDelete, "enabled": true ]'`

 Para obter mais informações, consulte [transmitir logs de recursos do Azure para log Analytics espaço de trabalho no Azure monitor](../../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace).

### <a name="template"></a>[Modelo](#tab/template)

Para exibir um modelo de Azure Resource Manager que cria uma configuração de diagnóstico, consulte [configuração de diagnóstico para o armazenamento do Azure](../../azure-monitor/essentials/resource-manager-diagnostic-settings.md#diagnostic-setting-for-azure-storage).

---

## <a name="analyzing-metrics"></a>Analisando as métricas

Você pode analisar métricas para o Armazenamento do Microsoft Azure com métricas de outros serviços do Azure usando o Metrics Explorer. Para abrir o Metrics Explorer, selecione **Métricas** no menu **Azure Monitor**. Para informações sobre o uso dessa ferramenta, consulte [Introdução ao Azure Metrics Explorer](../../azure-monitor/essentials/metrics-getting-started.md). 

Para métricas com suporte para dimensões, você pode filtrar a métrica com valor da dimensão desejado.  Para ver uma lista completa das dimensões compatíveis com o Armazenamento do Microsoft Azure, consulte [Dimensões de métricas](storage-files-monitoring-reference.md#metrics-dimensions). As métricas para os arquivos do Azure estão nestes namespaces: 

- Microsoft.Storage/storageAccounts
- Microsoft.Storage/storageAccounts/fileServices

Para obter uma lista de todas as métricas de suporte de Azure Monitor, que inclui arquivos do Azure, consulte [Azure monitor métricas com suporte](../../azure-monitor/essentials/metrics-supported.md#microsoftstoragestorageaccountsfileservices).

### <a name="accessing-metrics"></a>Acessando métricas

> [!TIP]
> Para exibir os exemplos da CLI do Azure ou do .NET, escolha as guias correspondentes listadas aqui.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

#### <a name="list-the-metric-definition"></a>Listar a definição de métrica

Você pode listar a definição de métrica de sua conta de armazenamento ou o serviço de arquivos do Azure. Use o cmdlet [Get-AzMetricDefinition](/powershell/module/az.monitor/get-azmetricdefinition).

Neste exemplo, substitua o `<resource-ID>` espaço reservado pela ID de recurso de toda a conta de armazenamento ou a ID de recurso do serviço arquivos do Azure.  Você pode encontrar essas IDs de recurso nas página de **propriedades** da sua conta de armazenamento no portal do Azure.

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetricDefinition -ResourceId $resourceId
```

#### <a name="reading-metric-values"></a>Lendo valores de métrica

Você pode ler os valores de métrica de nível de conta da sua conta de armazenamento ou do serviço arquivos do Azure. Use o cmdlet [Get-AzMetric](/powershell/module/Az.Monitor/Get-AzMetric).

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetric -ResourceId $resourceId -MetricNames "UsedCapacity" -TimeGrain 01:00:00
```

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

#### <a name="list-the-account-level-metric-definition"></a>Listar a definição de métrica no nível de conta

Você pode listar a definição de métrica de sua conta de armazenamento ou o serviço de arquivos do Azure. Use o comando [az monitor metrics list-definitions](/cli/azure/monitor/metrics#az-monitor-metrics-list-definitions).
 
Neste exemplo, substitua o `<resource-ID>` espaço reservado pela ID de recurso de toda a conta de armazenamento ou a ID de recurso do serviço arquivos do Azure. Você pode encontrar essas IDs de recurso nas página de **propriedades** da sua conta de armazenamento no portal do Azure.

```azurecli-interactive
   az monitor metrics list-definitions --resource <resource-ID>
```

#### <a name="read-account-level-metric-values"></a>Ler os valores de métrica no nível de conta

Você pode ler os valores de métrica da sua conta de armazenamento ou do serviço arquivos do Azure. Use o comando [az monitor metrics list](/cli/azure/monitor/metrics#az-monitor-metrics-list).

```azurecli-interactive
   az monitor metrics list --resource <resource-ID> --metric "UsedCapacity" --interval PT1H
```

### <a name="net"></a>[.NET](#tab/azure-portal)

O Azure Monitor fornece o [SDK do .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/) para ler valores e definição de métricas. O [código de exemplo](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/) mostra como usar o SDK com parâmetros diferentes. Você precisa usar `0.18.0-preview` ou uma versão posterior para métricas de armazenamento.
 
Nestes exemplos, substitua o `<resource-ID>` espaço reservado pela ID de recurso de toda a conta de armazenamento ou do serviço arquivos do Azure. Você pode encontrar essas IDs de recurso nas página de **propriedades** da sua conta de armazenamento no portal do Azure.

Substitua a variável `<subscription-ID>` pela ID da sua assinatura. Para saber como obter valores para `<tenant-ID>`, `<application-ID>` e `<AccessKey>`, consulte [Usar o portal para criar um aplicativo e uma entidade de serviço do Azure Active Directory que possa acessar recursos](../../active-directory/develop/howto-create-service-principal-portal.md). 

#### <a name="list-the-account-level-metric-definition"></a>Listar a definição de métrica no nível de conta

Os exemplos a seguir mostram como listar a definição de uma métrica no nível da conta:

```csharp
    public static async Task ListStorageMetricDefinition()
    {
        var resourceId = "<resource-ID>";
        var subscriptionId = "<subscription-ID>";
        var tenantId = "<tenant-ID>";
        var applicationId = "<application-ID>";
        var accessKey = "<AccessKey>";


        MonitorManagementClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;
        IEnumerable<MetricDefinition> metricDefinitions = await readOnlyClient.MetricDefinitions.ListAsync(resourceUri: resourceId, cancellationToken: new CancellationToken());

        foreach (var metricDefinition in metricDefinitions)
        {
            // Enumrate metric definition:
            //    Id
            //    ResourceId
            //    Name
            //    Unit
            //    MetricAvailabilities
            //    PrimaryAggregationType
            //    Dimensions
            //    IsDimensionRequired
        }
    }

```

#### <a name="reading-account-level-metric-values"></a>Lendo valores de métrica no nível da conta

O exemplo a seguir mostra como ler os dados `UsedCapacity` no nível da conta:

```csharp
    public static async Task ReadStorageMetricValue()
    {
        var resourceId = "<resource-ID>";
        var subscriptionId = "<subscription-ID>";
        var tenantId = "<tenant-ID>";
        var applicationId = "<application-ID>";
        var accessKey = "<AccessKey>";

        MonitorClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;

        Microsoft.Azure.Management.Monitor.Models.Response Response;

        string startDate = DateTime.Now.AddHours(-3).ToUniversalTime().ToString("o");
        string endDate = DateTime.Now.ToUniversalTime().ToString("o");
        string timeSpan = startDate + "/" + endDate;

        Response = await readOnlyClient.Metrics.ListAsync(
            resourceUri: resourceId,
            timespan: timeSpan,
            interval: System.TimeSpan.FromHours(1),
            metricnames: "UsedCapacity",

            aggregation: "Average",
            resultType: ResultType.Data,
            cancellationToken: CancellationToken.None);

        foreach (var metric in Response.Value)
        {
            // Enumrate metric value
            //    Id
            //    Name
            //    Type
            //    Unit
            //    Timeseries
            //        - Data
            //        - Metadatavalues
        }
    }

```

#### <a name="reading-multidimensional-metric-values"></a>Lendo valores de métrica multidimensional

Para métricas multidimensionais, você precisa definir os filtros de metadados se você quiser ler dados da métrica nos valores de dimensão específica.

O exemplo a seguir mostra como ler dados em uma métrica que suporta várias dimensões:

```csharp
    public static async Task ReadStorageMetricValueTest()
    {
        // Resource ID for Azure Files
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/fileServices/default";
        var subscriptionId = "<subscription-ID}";
        // How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "<tenant-ID>";
        var applicationId = "<application-ID>";
        var accessKey = "<AccessKey>";

        MonitorManagementClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;

        Microsoft.Azure.Management.Monitor.Models.Response Response;

        string startDate = DateTime.Now.AddHours(-3).ToUniversalTime().ToString("o");
        string endDate = DateTime.Now.ToUniversalTime().ToString("o");
        string timeSpan = startDate + "/" + endDate;
        // It's applicable to define meta data filter when a metric support dimension
        // More conditions can be added with the 'or' and 'and' operators, example: BlobType eq 'BlockBlob' or BlobType eq 'PageBlob'
        ODataQuery<MetadataValue> odataFilterMetrics = new ODataQuery<MetadataValue>(
            string.Format("BlobType eq '{0}'", "BlockBlob"));

        Response = readOnlyClient.Metrics.List(
                        resourceUri: resourceId,
                        timespan: timeSpan,
                        interval: System.TimeSpan.FromHours(1),
                        metricnames: "BlobCapacity",
                        odataQuery: odataFilterMetrics,
                        aggregation: "Average",
                        resultType: ResultType.Data);

        foreach (var metric in Response.Value)
        {
            //Enumrate metric value
            //    Id
            //    Name
            //    Type
            //    Unit
            //    Timeseries
            //        - Data
            //        - Metadatavalues
        }
    }

```

# <a name="template"></a>[Modelo](#tab/template)

N/D

---

## <a name="analyzing-logs"></a>Análise de logs

Você pode acessar os logs de recursos como um blob em uma conta de armazenamento, como dados de evento, ou por meio de consultas do Log Analytics.

Para obter a lista de operações de SMB e REST registradas, consulte [operações registradas de armazenamento e mensagens de status](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) e [referência de dados de monitoramento de arquivos do Azure](storage-files-monitoring-reference.md).

> [!NOTE]
> Os logs do Armazenamento do Microsoft Azure no Azure Monitor estão em versão preliminar pública e disponíveis para teste de versão preliminar em todas as regiões de nuvem pública. Essa versão preliminar habilita logs para blobs (que incluem Azure Data Lake Storage Gen2), arquivos, filas, tabelas, contas de armazenamento Premium nas contas de armazenamento GPv1 e GPv2. Não há suporte para contas de armazenamento clássicas.

As entradas de log são criadas somente se há solicitações feitas no ponto de extremidade de serviço. Por exemplo, se uma conta de armazenamento tiver atividade em seu ponto de extremidade de arquivo, mas não em seus pontos de extremidades de tabela ou fila, somente os logs pertencentes ao serviço de arquivos do Azure serão criados. Os logs do Armazenamento do Microsoft Azure contêm informações detalhadas sobre solicitações bem-sucedidas e com falha para um serviço de armazenamento. Essas informações podem ser usadas para monitorar solicitações individuais e diagnosticar problemas com um serviço de armazenamento. As solicitações são registradas em uma base de melhor esforço.

### <a name="log-authenticated-requests"></a>Registrar em log solicitações autenticadas

 Os seguintes tipos de solicitações autenticadas são registrados:

- Solicitações bem-sucedidas
- Solicitações com falha, incluindo tempo limite, limitação, rede, autorização e outros erros
- Solicitações que usam Kerberos, NTLM ou SAS (assinatura de acesso compartilhado), incluindo solicitações com falha e bem-sucedidas
- Solicitações para dados de análise (dados de log clássicos no contêiner de **$logs** e dados de métrica clássicos nas tabelas de **$Metric** )

As solicitações feitas pelo próprio serviço de arquivos do Azure, como criação ou exclusão de log, não são registradas. Para obter uma lista completa das solicitações SMB e REST registradas, consulte [operações registradas de armazenamento e mensagens de status](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) e [referência de dados de monitoramento de arquivos do Azure](storage-files-monitoring-reference.md).

### <a name="accessing-logs-in-a-storage-account"></a>Acessando logs em uma conta de armazenamento

Os logs aparecem como blobs armazenados em um contêiner na conta de armazenamento de destino. Os dados são coletados e armazenados dentro de um único blob como um conteúdo JSON delimitado por linha. O nome do blob segue a convenção de nomenclatura:

`https://<destination-storage-account>.blob.core.windows.net/insights-logs-<storage-operation>/resourceId=/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<source-storage-account>/fileServices/default/y=<year>/m=<month>/d=<day>/h=<hour>/m=<minute>/PT1H.json`

Aqui está um exemplo:

`https://mylogstorageaccount.blob.core.windows.net/insights-logs-storagewrite/resourceId=/subscriptions/`<br>`208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/fileServices/default/y=2019/m=07/d=30/h=23/m=12/PT1H.json`

### <a name="accessing-logs-in-an-event-hub"></a>Acessando logs em um hub de eventos

Os logs enviados para um hub de eventos não são armazenados como um arquivo, mas você pode verificar se o hub de eventos recebeu as informações de log. No portal do Azure, navegue até seu hub de eventos e verifique se a contagem de **mensagens de entrada** é maior que zero. 

![Logs de auditoria](media/storage-files-monitoring/event-hub-log.png)

Você pode acessar e ler dados de log enviados ao seu hub de eventos usando informações de segurança e ferramentas de monitoramento e gerenciamento de eventos. Para mais informações, consulte [O que posso fazer com os dados de monitoramento que estão sendo enviados ao meu hub de eventos?](../../azure-monitor/essentials/stream-monitoring-data-event-hubs.md).

### <a name="accessing-logs-in-a-log-analytics-workspace"></a>Acessando logs em um espaço de trabalho Log Analytics

Você pode acessar os logs enviados a um workspace do Log Analytics usando consultas de log do Azure Monitor. Os dados são armazenados na tabela **StorageFileLogs** . 

Para obter mais informações, consulte [log Analytics tutorial](../../azure-monitor/logs/log-analytics-tutorial.md).

#### <a name="sample-kusto-queries"></a>Consultas de exemplo do Kusto

Aqui estão algumas consultas que você pode inserir na barra de **pesquisa de log** para ajudá-lo a monitorar seus arquivos do Azure. Essas consultas funcionam com a [nova linguagem](../../azure-monitor/logs/log-query-overview.md).

> [!IMPORTANT]
> Quando você seleciona **logs** no menu do grupo de recursos da conta de armazenamento, log Analytics é aberta com o escopo de consulta definido como o grupo de recursos atual. Isso significa que as consultas de log incluirão apenas os dados desse grupo de recursos. Se você quiser executar uma consulta que inclui dados de outros recursos ou dados de outros serviços do Azure, selecione **logs** no menu **Azure monitor** . Confira [Escopo da consulta de log e intervalo de tempo no Log Analytics do Azure Monitor](../../azure-monitor/logs/scope.md) para obter detalhes.

Use essas consultas para ajudá-lo a monitorar seus compartilhamentos de arquivos do Azure:

- Exibir erros de SMB na última semana

```Kusto
StorageFileLogs
| where Protocol == "SMB" and TimeGenerated >= ago(7d) and StatusCode contains "-"
| sort by StatusCode
```
- Criar um gráfico de pizza de operações SMB na última semana

```Kusto
StorageFileLogs
| where Protocol == "SMB" and TimeGenerated >= ago(7d) 
| summarize count() by OperationName
| sort by count_ desc
| render piechart
```

- Exibir erros REST na última semana

```Kusto
StorageFileLogs
| where Protocol == "HTTPS" and TimeGenerated >= ago(7d) and StatusText !contains "Success"
| sort by StatusText asc
```

- Criar um gráfico de pizza de operações REST na última semana

```Kusto
StorageFileLogs
| where Protocol == "HTTPS" and TimeGenerated >= ago(7d) 
| summarize count() by OperationName
| sort by count_ desc
| render piechart
```

Para exibir a lista de nomes de coluna e descrições de arquivos do Azure, consulte [StorageFileLogs](/azure/azure-monitor/reference/tables/storagefilelogs).

Para obter mais informações sobre como escrever consultas, consulte [log Analytics tutorial](../../azure-monitor/logs/log-analytics-tutorial.md).

## <a name="alerts"></a>Alertas

Os alertas do Azure Monitor notificam você proativamente quando condições importantes são encontradas nos dados de monitoramento. Eles permitem que você identifique e resolva problemas no seu sistema antes que os clientes os percebam. Você pode definir alertas em [métricas](../../azure-monitor/alerts/alerts-metric-overview.md), [logs](../../azure-monitor/alerts/alerts-unified-log.md) e [log de atividades](../../azure-monitor/alerts/activity-log-alerts.md). 

A tabela a seguir lista alguns cenários de exemplo para monitorar e a métrica apropriada a ser usada para o alerta:

| Cenário | Métrica a ser usada para alerta |
|-|-|
| O compartilhamento de arquivos está limitado. | Métrica: transações<br>Nome da dimensão: tipo de resposta <br>Nome da dimensão: FileShare (somente compartilhamento de arquivos Premium) |
| O tamanho do compartilhamento de arquivos é de 80% da capacidade. | Métrica: capacidade de arquivo<br>Nome da dimensão: FileShare (somente compartilhamento de arquivos Premium) |
| A saída do compartilhamento de arquivos excedeu 500 GiB em um dia. | Métrica: saída<br>Nome da dimensão: FileShare (somente compartilhamento de arquivos Premium) |

### <a name="how-to-create-alerts-for-azure-files"></a>Como criar alertas para arquivos do Azure

1. Vá para sua **conta de armazenamento** no **portal do Azure**. 

2. Clique em **alertas** e, em seguida, clique em **+ nova regra de alerta**.

3. Clique em **Editar recurso**, selecione o **tipo de recurso arquivo** e, em seguida, clique em **concluído**. 

4. Clique em **Adicionar condição** e forneça as seguintes informações para o alerta: 

    - **Métrica**
    - **Nome da dimensão**
    - **Lógica de alerta**

5. Clique em **Adicionar grupos de ações** e adicione um grupo de ações (email, SMS, etc.) ao alerta selecionando um grupo de ações existente ou criando um novo grupo de ação.

6. Preencha os **detalhes do alerta** , como nome da **regra de alerta**, **Descrição** e **severidade**.

7. Clique em **criar regra de alerta** para criar o alerta.

> [!NOTE]  
> Se você criar um alerta e ele estiver muito ruidosa, ajuste o valor de limite e a lógica de alerta.

### <a name="how-to-create-an-alert-if-a-file-share-is-throttled"></a>Como criar um alerta se um compartilhamento de arquivos for limitado

1. Vá para sua **conta de armazenamento** no **portal do Azure**.
2. Na seção **monitoramento** , clique em **alertas** e, em seguida, clique em **+ nova regra de alerta**.
3. Clique em **Editar recurso**, selecione o **tipo de recurso arquivo** para a conta de armazenamento e clique em **concluído**. Por exemplo, se o nome da conta de armazenamento for `contoso` , selecione o `contoso/file` recurso.
4. Clique em **Adicionar condição** para adicionar uma condição.
5. Você verá uma lista de sinais com suporte para a conta de armazenamento, selecione a métrica **Transações** .
6. Na folha **Configurar lógica de sinal** , clique na lista suspensa **nome da dimensão** e selecione **tipo de resposta**.
7. Clique na lista suspensa **valores de dimensão** e selecione os tipos de resposta apropriados para o compartilhamento de arquivos.

    Para compartilhamentos de arquivos padrão, selecione os seguintes tipos de resposta:

    - SuccessWithShareIopsThrottling
    - SuccessWithThrottling
    - ClientShareIopsThrottlingError

    Para compartilhamentos de arquivos premium, selecione os seguintes tipos de resposta:

    - SuccessWithShareEgressThrottling
    - SuccessWithShareIngressThrottling
    - SuccessWithShareIopsThrottling
    - ClientShareEgressThrottlingError
    - ClientShareIngressThrottlingError
    - ClientShareIopsThrottlingError

   > [!NOTE]
   > Se os tipos de resposta não estiverem listados na lista suspensa **valores de dimensão** , isso significa que o recurso não foi limitado. Para adicionar os valores de dimensão, ao lado da lista suspensa **valores de dimensão** , selecione **adicionar valor personalizado**, insira o tipo resposta (por exemplo, **SuccessWithThrottling**), selecione **OK** e repita essas etapas para adicionar todos os tipos de resposta aplicáveis ao compartilhamento de arquivos.

8. Para **compartilhamentos de arquivos Premium**, clique na lista suspensa **nome da dimensão** e selecione **compartilhamento de arquivos**. Para **compartilhamentos de arquivos padrão**, pule para a **etapa #10**.

   > [!NOTE]
   > Se o compartilhamento de arquivos for um compartilhamento de arquivos padrão, a dimensão de **compartilhamento de arquivos** não listará os compartilhamentos de arquivos porque as métricas por compartilhamento não estão disponíveis para compartilhamentos de arquivos padrão. Os alertas de limitação para compartilhamentos de arquivos padrão serão disparados se algum compartilhamento de arquivos dentro da conta de armazenamento for limitado e o alerta não identificar qual compartilhamento de arquivos foi limitado. Como as métricas por compartilhamento não estão disponíveis para compartilhamentos de arquivos padrão, a recomendação é ter um compartilhamento de arquivos por conta de armazenamento.

9. Clique na lista suspensa **valores de dimensão** e selecione os compartilhamentos de arquivos que você deseja alertar.
10. Defina os **parâmetros de alerta** (valor de limite, operador, granularidade de agregação e frequência de avaliação) e clique em **concluído**.

    > [!TIP]
    > Se você estiver usando um limite estático, o gráfico de métrica poderá ajudar a determinar um valor de limite razoável se o compartilhamento de arquivos estiver sendo limitado no momento. Se você estiver usando um limite dinâmico, o gráfico de métrica exibirá os limites calculados com base nos dados recentes.

11. Clique em **Adicionar grupos de ações** para adicionar um **grupo de ações** (email, SMS, etc.) ao alerta selecionando um grupo de ações existente ou criando um novo grupo de ação.
12. Preencha os **detalhes do alerta** , como nome da **regra de alerta**, **Descrição** e **severidade**.
13. Clique em **criar regra de alerta** para criar o alerta.

### <a name="how-to-create-an-alert-if-the-azure-file-share-size-is-80-of-capacity"></a>Como criar um alerta se o tamanho do compartilhamento de arquivos do Azure for de 80% da capacidade

1. Vá para sua **conta de armazenamento** no **portal do Azure**.
2. Na seção **monitoramento** , clique em **alertas** e, em seguida, clique em **+ nova regra de alerta**.
3. Clique em **Editar recurso**, selecione o **tipo de recurso arquivo** para a conta de armazenamento e clique em **concluído**. Por exemplo, se o nome da conta de armazenamento for `contoso` , selecione o `contoso/file` recurso.
4. Clique em **Adicionar condição** para adicionar uma condição.
5. Você verá uma lista de sinais com suporte para a conta de armazenamento, selecione a métrica de **capacidade de arquivo** .
6. Para **compartilhamentos de arquivos Premium**, clique na lista suspensa **nome da dimensão** e selecione **compartilhamento de arquivos**. Para **compartilhamentos de arquivos padrão**, pule para a **etapa #8**.

   > [!NOTE]
   > Se o compartilhamento de arquivos for um compartilhamento de arquivos padrão, a dimensão de **compartilhamento de arquivos** não listará os compartilhamentos de arquivos porque as métricas por compartilhamento não estão disponíveis para compartilhamentos de arquivos padrão. Os alertas para compartilhamentos de arquivos padrão são baseados em todos os compartilhamentos de arquivos na conta de armazenamento. Como as métricas por compartilhamento não estão disponíveis para compartilhamentos de arquivos padrão, a recomendação é ter um compartilhamento de arquivos por conta de armazenamento.

7. Clique na lista suspensa **valores de dimensão** e selecione os compartilhamentos de arquivos que você deseja alertar.
8. Insira o **valor do limite** em bytes. Por exemplo, se o tamanho do compartilhamento de arquivos for 100 TiB e você quiser receber um alerta quando o tamanho do compartilhamento de arquivos for 80% da capacidade, o valor do limite em bytes será 87960930222080.
9. Defina o restante dos **parâmetros de alerta** (granularidade de agregação e frequência de avaliação) e clique em **concluído**.
10. Clique em **Adicionar grupos de ações** para adicionar um **grupo de ações** (email, SMS, etc.) ao alerta selecionando um grupo de ações existente ou criando um novo grupo de ação.
11. Preencha os **detalhes do alerta** , como nome da **regra de alerta**, **Descrição** e **severidade**.
12. Clique em **criar regra de alerta** para criar o alerta.

### <a name="how-to-create-an-alert-if-the-azure-file-share-egress-has-exceeded-500-gib-in-a-day"></a>Como criar um alerta se a saída do compartilhamento de arquivos do Azure excedeu 500 GiB em um dia

1. Vá para sua **conta de armazenamento** no **portal do Azure**.
2. Na seção monitoramento, clique em **alertas** e, em seguida, clique em **+ nova regra de alerta**.
3. Clique em **Editar recurso**, selecione o **tipo de recurso arquivo** para a conta de armazenamento e clique em **concluído**. Por exemplo, se o nome da conta de armazenamento for contoso, selecione o recurso contoso/File.
4. Clique em **Adicionar condição** para adicionar uma condição.
5. Você verá uma lista de sinais com suporte para a conta de armazenamento, selecione a métrica de **saída** .
6. Para **compartilhamentos de arquivos Premium**, clique na lista suspensa **nome da dimensão** e selecione **compartilhamento de arquivos**. Para **compartilhamentos de arquivos padrão**, pule para a **etapa #8**.

   > [!NOTE]
   > Se o compartilhamento de arquivos for um compartilhamento de arquivos padrão, a dimensão de **compartilhamento de arquivos** não listará os compartilhamentos de arquivos porque as métricas por compartilhamento não estão disponíveis para compartilhamentos de arquivos padrão. Os alertas para compartilhamentos de arquivos padrão são baseados em todos os compartilhamentos de arquivos na conta de armazenamento. Como as métricas por compartilhamento não estão disponíveis para compartilhamentos de arquivos padrão, a recomendação é ter um compartilhamento de arquivos por conta de armazenamento.

7. Clique na lista suspensa **valores de dimensão** e selecione os compartilhamentos de arquivos que você deseja alertar.
8. Insira **536870912000** bytes para o valor de limite. 
9. Clique na lista suspensa **agregação de granularidade** e selecione **24 horas**.
10. Selecione a **frequência de avaliação** e **clique em concluído**.
11. Clique em **Adicionar grupos de ações** para adicionar um **grupo de ações** (email, SMS, etc.) ao alerta selecionando um grupo de ações existente ou criando um novo grupo de ação.
12. Preencha os **detalhes do alerta** , como nome da **regra de alerta**, **Descrição** e **severidade**.
13. Clique em **criar regra de alerta** para criar o alerta.

## <a name="next-steps"></a>Próximas etapas

- [Referência de dados de monitoramento de arquivos do Azure](storage-files-monitoring-reference.md)
- [Monitorar recursos do Azure com o Azure Monitor](../../azure-monitor/essentials/monitor-azure-resource.md)
- [Migração de métricas do armazenamento do Azure](../common/storage-metrics-migration.md)
- [Como planejar uma implantação de Arquivos do Azure](./storage-files-planning.md)
- [Como implantar Arquivos do Azure](./storage-how-to-create-file-share.md)
- [Solucionar Problemas dos Arquivos do Azure no Windows](./storage-troubleshoot-windows-file-connection-problems.md)
- [Solucionar Problemas dos Arquivos do Azure no Linux](./storage-troubleshoot-linux-file-connection-problems.md)
