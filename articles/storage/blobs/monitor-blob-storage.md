---
title: Monitorando o armazenamento de BLOBs do Azure | Microsoft Docs
description: Saiba como monitorar o desempenho e a disponibilidade do armazenamento de BLOBs do Azure. Monitore os dados do armazenamento de BLOBs do Azure, saiba mais sobre a configuração e analise os dados de métrica e de log.
author: normesta
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 10/26/2020
ms.author: normesta
ms.reviewer: fryu
ms.custom: subject-monitoring, devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 3b497a8507fb82bfb69fbe7396e5a0d34006a7f1
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102502069"
---
# <a name="monitoring-azure-blob-storage"></a>Monitorando o armazenamento de BLOBs do Azure

Quando você tem aplicativos e processos de negócios críticos que dependem de recursos do Azure, recomendamos monitorar a disponibilidade, o desempenho e a operação desses recursos. Este artigo descreve os dados de monitoramento gerados pelo armazenamento de BLOBs do Azure e como você pode usar os recursos do Azure Monitor para analisar alertas sobre esses dados.

> [!NOTE]
> Os logs do Armazenamento do Microsoft Azure no Azure Monitor estão em versão preliminar pública e disponíveis para teste de versão preliminar em todas as regiões de nuvem pública. Essa visualização habilita logs para BLOBs (que inclui Azure Data Lake Storage Gen2), arquivos, filas e tabelas. Esse recurso está disponível para todas as contas de armazenamento criadas com o modelo de implantação Azure Resource Manager. Consulte [visão geral da conta de armazenamento](../common/storage-account-overview.md).

## <a name="monitor-overview"></a>Visão geral do Monitor

A página de **visão geral** no portal do Azure para cada recurso de armazenamento de BLOBs inclui uma breve exibição do uso de recursos, como solicitações e cobrança por hora. Essas informações são úteis, mas apenas uma pequena quantidade de dados de monitoramento está disponível. Alguns desses dados são coletados automaticamente e estão disponíveis para análise assim que você criar o recurso. Você pode habilitar tipos adicionais de coleta de dados com algumas configurações.

## <a name="what-is-azure-monitor"></a>O que é o Azure Monitor?
O armazenamento de BLOBs do Azure cria dados de monitoramento usando [Azure monitor](../../azure-monitor/overview.md), que é um serviço de monitoramento de pilha completo no Azure. O Azure Monitor oferece um conjunto completo de recursos para monitorar os recursos do Azure e recursos em outras nuvens e locais. 

Comece com o artigo [monitorando os recursos do Azure com Azure monitor](../../azure-monitor/essentials/monitor-azure-resource.md) que descreve o seguinte:

- O que é o Azure Monitor?
- Custos associados ao monitoramento
- Monitoramento de dados coletados no Azure
- Configuração de coleta de dados
- Ferramentas padrão no Azure para analisar e alertar sobre dados de monitoramento

As seções a seguir se baseiam neste artigo para descrever os dados específicos coletados do Armazenamento do Azure. Os exemplos mostram como configurar a coleta de dados e analisar esses dados com as ferramentas do Azure.

## <a name="monitoring-data"></a>Dados de monitoramento

O armazenamento de BLOBs do Azure coleta os mesmos tipos de dados de monitoramento que outros recursos do Azure, que são descritos em [monitoramento de dados de recursos do Azure](../../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data). 

Consulte [referência de dados de monitoramento do armazenamento de BLOBs do Azure](monitor-blob-storage-reference.md) para obter informações detalhadas sobre as métricas de métricas e logs criadas pelo armazenamento de BLOBs do Azure.

As métricas e os logs do Azure Monitor oferecem suporte apenas às contas de armazenamento do Azure Resource Manager. O Azure Monitor não tem suporte a contas de armazenamento clássicas. Se você quiser usar as métricas ou os logs nas contas de armazenamento clássicas, precisará migrar para a conta de armazenamento do Azure Resource Manager. Confira [Migrar para o Azure Resource Manager](../../virtual-machines/migration-classic-resource-manager-overview.md).

Você pode continuar usando logs e métricas clássicos se desejar. Na verdade, as métricas e os logs clássicos estão disponíveis em paralelo com as métricas e os logs no Azure Monitor. O suporte permanece disponível até que o Armazenamento do Microsoft Azure encerre o serviço nas métricas e nos logs herdados.

## <a name="collection-and-routing"></a>Coleta e roteamento

As métricas de plataforma e o log de atividades são coletados automaticamente, mas podem ser roteados para outros locais usando uma configuração de diagnóstico. 

Para coletar logs de recursos, você deve criar uma configuração de diagnóstico. Ao criar a configuração, escolha **blob** como o tipo de armazenamento para o qual você deseja habilitar os logs. Em seguida, especifique uma das seguintes categorias de operações para as quais você deseja coletar logs. 

| Categoria | Descrição |
|:---|:---|
| StorageRead | Operações de leitura em objetos. |
| StorageWrite | Operações de gravação em objetos. |
| StorageDelete | Excluir operações em objetos. |

> [!NOTE]
> Data Lake Storage Gen2 não aparece como um tipo de armazenamento. Isso ocorre porque Data Lake Storage Gen2 é um conjunto de recursos disponíveis para o armazenamento de BLOBs. 

## <a name="creating-a-diagnostic-setting"></a>Criando uma configuração de diagnóstico

Você pode criar uma configuração de diagnóstico usando o portal do Azure, o PowerShell, o CLI do Azure ou um modelo de Azure Resource Manager. 

Para obter diretrizes gerais, consulte [criar configuração de diagnóstico para coletar logs e métricas de plataforma no Azure](../../azure-monitor/essentials/diagnostic-settings.md).

> [!NOTE]
> Os logs do Armazenamento do Microsoft Azure no Azure Monitor estão em versão preliminar pública e disponíveis para teste de versão preliminar em todas as regiões de nuvem pública. Essa visualização habilita logs para BLOBs (que inclui Azure Data Lake Storage Gen2), arquivos, filas e tabelas. Esse recurso está disponível para todas as contas de armazenamento criadas com o modelo de implantação Azure Resource Manager. Consulte [visão geral da conta de armazenamento](../common/storage-account-overview.md).

### <a name="azure-portal"></a>[Azure portal](#tab/azure-portal)

1. Entre no portal do Azure.

2. Navegue para sua conta de armazenamento.

3. Na seção **monitoramento** , clique em **configurações de diagnóstico (versão prévia)**.

   > [!div class="mx-imgBorder"]
   > ![portal – Logs de diagnóstico](media/monitor-blob-storage/diagnostic-logs-settings-pane.png)   

4. Escolha **blob** como o tipo de armazenamento para o qual você deseja habilitar os logs.

5. Clique em **Adicionar configuração de diagnóstico**.

   > [!div class="mx-imgBorder"]
   > ![Portal-logs de recursos – adicionar configuração de diagnóstico](media/monitor-blob-storage/diagnostic-logs-settings-pane-2.png)

   A página **configurações de diagnóstico** é exibida.

   > [!div class="mx-imgBorder"]
   > ![Página logs de recursos](media/monitor-blob-storage/diagnostic-logs-page.png)

6. No campo **nome** da página, insira um nome para essa configuração de log de recursos. Em seguida, selecione as operações que você deseja registrar (operações de leitura, gravação e exclusão) e onde deseja que os logs sejam enviados.

#### <a name="archive-logs-to-a-storage-account"></a>Arquivar logs em uma conta de armazenamento

Se você optar por arquivar seus logs em uma conta de armazenamento, pagará pelo volume de logs que são enviados para a conta de armazenamento. Para obter preços específicos, consulte a seção de **logs da plataforma** da página de [preços do Azure monitor](https://azure.microsoft.com/pricing/details/monitor/#platform-logs) .

1. Marque a caixa de seleção **arquivar em uma conta de armazenamento** e, em seguida, clique no botão **Configurar** .

   > [!div class="mx-imgBorder"]   
   > ![Página Configurações de diagnóstico armazenamento de arquivos](media/monitor-blob-storage/diagnostic-logs-settings-pane-archive-storage.png)

2. Na lista suspensa **conta de armazenamento** , selecione a conta de armazenamento na qual você deseja arquivar os logs, clique no botão **OK** e, em seguida, clique no botão **salvar** .

   [!INCLUDE [no retention policy](../../../includes/azure-storage-logs-retention-policy.md)]

   > [!NOTE]
   > Antes de escolher uma conta de armazenamento como destino de exportação, consulte [arquivar logs de recursos do Azure](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage) para entender os pré-requisitos na conta de armazenamento.

#### <a name="stream-logs-to-azure-event-hubs"></a>Transmitir logs para os hubs de eventos do Azure

Se você optar por transmitir os logs para um hub de eventos, pagará pelo volume de logs que são enviados para o Hub de eventos. Para obter preços específicos, consulte a seção de **logs da plataforma** da página de [preços do Azure monitor](https://azure.microsoft.com/pricing/details/monitor/#platform-logs) .

1. Marque a caixa de seleção **transmitir para um hub de eventos** e, em seguida, clique no botão **Configurar** .

2. No painel **selecionar um hub de eventos** , escolha o namespace, o nome e o nome da política do hub de eventos para o qual você deseja transmitir os logs. 

   > [!div class="mx-imgBorder"]
   > ![Página de configurações de diagnóstico Hub de eventos](media/monitor-blob-storage/diagnostic-logs-settings-pane-event-hub.png)

3. Clique no botão **OK** e, em seguida, clique no botão **salvar** .

#### <a name="send-logs-to-azure-log-analytics"></a>Enviar logs para o Azure Log Analytics

1. Marque a caixa de seleção **Enviar para log Analytics** , selecione um espaço de trabalho do log Analytics e, em seguida, clique no botão **salvar** .

   > [!div class="mx-imgBorder"]   
   > ![Página Configurações de diagnóstico log Analytics](media/monitor-blob-storage/diagnostic-logs-settings-pane-log-analytics.png)

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

Substitua o `<storage-service-resource--id>` espaço reservado neste trecho pela ID de recurso do serviço BLOB. Para encontrar o ID do recurso no portal do Azure, você pode abrir a página **Propriedades** da sua conta de armazenamento.

Você pode usar `StorageRead` , `StorageWrite` e `StorageDelete` para o valor do parâmetro **Category** .

[!INCLUDE [no retention policy](../../../includes/azure-storage-logs-retention-policy.md)]

Veja um exemplo:

`Set-AzDiagnosticSetting -ResourceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/blobServices/default -StorageAccountId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount -Enabled $true -Category StorageWrite,StorageDelete`

Para obter uma descrição de cada parâmetro, consulte [arquivar logs de recursos do Azure por meio de Azure PowerShell](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage).

#### <a name="stream-logs-to-an-event-hub"></a>Transmitir logs para um hub de eventos

Se você optar por transmitir os logs para um hub de eventos, pagará pelo volume de logs que são enviados para o Hub de eventos. Para obter preços específicos, consulte a seção de **logs da plataforma** da página de [preços do Azure monitor](https://azure.microsoft.com/pricing/details/monitor/#platform-logs) .

Habilite logs usando o cmdlet do PowerShell [set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) com o `EventHubAuthorizationRuleId` parâmetro.

```powershell
Set-AzDiagnosticSetting -ResourceId <storage-service-resource-id> -EventHubAuthorizationRuleId <event-hub-namespace-and-key-name> -Enabled $true -Category <operations-to-log> -RetentionEnabled <retention-bool> -RetentionInDays <number-of-days>
```

Veja um exemplo:

`Set-AzDiagnosticSetting -ResourceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/blobServices/default -EventHubAuthorizationRuleId /subscriptions/20884142-a14v3-4234-5450-08b10c09f4/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhubnamespace/authorizationrules/RootManageSharedAccessKey -Enabled $true -Category StorageDelete`

Para obter uma descrição de cada parâmetro, consulte os [dados de fluxo para hubs de eventos por meio de cmdlets do PowerShell](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs).

#### <a name="send-logs-to-log-analytics"></a>Enviar logs ao Log Analytics

Habilite logs usando o cmdlet do PowerShell [set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) com o `WorkspaceId` parâmetro.

```powershell
Set-AzDiagnosticSetting -ResourceId <storage-service-resource-id> -WorkspaceId <log-analytics-workspace-resource-id> -Enabled $true -Category <operations-to-log> -RetentionEnabled <retention-bool> -RetentionInDays <number-of-days>
```

Veja um exemplo:

`Set-AzDiagnosticSetting -ResourceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/blobServices/default -WorkspaceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.OperationalInsights/workspaces/my-analytic-workspace -Enabled $true -Category StorageDelete`

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
az monitor diagnostic-settings create --name <setting-name> --storage-account <storage-account-name> --resource <storage-service-resource-id> --resource-group <resource-group> --logs '[{"category": <operations>, "enabled": true }]'
```

Substitua o `<storage-service-resource--id>` espaço reservado neste trecho pelo serviço de armazenamento de BLOB da ID de recurso. Para encontrar o ID do recurso no portal do Azure, você pode abrir a página **Propriedades** da sua conta de armazenamento.

Você pode usar `StorageRead` , `StorageWrite` e `StorageDelete` para o valor do parâmetro **Category** .

[!INCLUDE [no retention policy](../../../includes/azure-storage-logs-retention-policy.md)]

Veja um exemplo:

`az monitor diagnostic-settings create --name setting1 --storage-account mystorageaccount --resource /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount/blobServices/default --resource-group myresourcegroup --logs '[{"category": StorageWrite}]'`

Para obter uma descrição de cada parâmetro, consulte os [logs de recursos de arquivo por meio do CLI do Azure](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage).

#### <a name="stream-logs-to-an-event-hub"></a>Transmitir logs para um hub de eventos

Se você optar por transmitir os logs para um hub de eventos, pagará pelo volume de logs que são enviados para o Hub de eventos. Para obter preços específicos, consulte a seção de **logs da plataforma** da página de [preços do Azure monitor](https://azure.microsoft.com/pricing/details/monitor/#platform-logs) .

Habilite logs usando o comando [AZ monitor Diagnostics-Settings Create](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create) .

```azurecli-interactive
az monitor diagnostic-settings create --name <setting-name> --event-hub <event-hub-name> --event-hub-rule <event-hub-namespace-and-key-name> --resource <storage-account-resource-id> --logs '[{"category": <operations>, "enabled": true "retentionPolicy": {"days": <number-days>, "enabled": <retention-bool}}]'
```

Veja um exemplo:

`az monitor diagnostic-settings create --name setting1 --event-hub myeventhub --event-hub-rule /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhubnamespace/authorizationrules/RootManageSharedAccessKey --resource /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount/blobServices/default --logs '[{"category": StorageDelete, "enabled": true }]'`

Para obter uma descrição de cada parâmetro, consulte os [dados de fluxo para os hubs de eventos por meio de CLI do Azure](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs).

#### <a name="send-logs-to-log-analytics"></a>Enviar logs ao Log Analytics

Habilite logs usando o comando [AZ monitor Diagnostics-Settings Create](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create) .

```azurecli-interactive
az monitor diagnostic-settings create --name <setting-name> --workspace <log-analytics-workspace-resource-id> --resource <storage-account-resource-id> --logs '[{"category": <category name>, "enabled": true "retentionPolicy": {"days": <days>, "enabled": <retention-bool}}]'
```

Veja um exemplo:

`az monitor diagnostic-settings create --name setting1 --workspace /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.OperationalInsights/workspaces/my-analytic-workspace --resource /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount/blobServices/default --logs '[{"category": StorageDelete, "enabled": true ]'`

 Para obter mais informações, consulte [transmitir logs de recursos do Azure para log Analytics espaço de trabalho no Azure monitor](../../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace).

### <a name="template"></a>[Modelo](#tab/template)

Para exibir um modelo de Azure Resource Manager que cria uma configuração de diagnóstico, consulte [configuração de diagnóstico para o armazenamento do Azure](../../azure-monitor/essentials/resource-manager-diagnostic-settings.md#diagnostic-setting-for-azure-storage).

---

## <a name="analyzing-metrics"></a>Analisando as métricas

Você pode analisar métricas para o Armazenamento do Microsoft Azure com métricas de outros serviços do Azure usando o Metrics Explorer. Para abrir o Metrics Explorer, selecione **Métricas** no menu **Azure Monitor**. Para informações sobre o uso dessa ferramenta, consulte [Introdução ao Azure Metrics Explorer](../../azure-monitor/essentials/metrics-getting-started.md). 

Este exemplo mostra como exibir **Transações** no nível da conta.

![Captura de tela de acesso às métricas no portal do Azure](./media/monitor-blob-storage/access-metrics-portal.png)

Para métricas com suporte para dimensões, você pode filtrar a métrica com valor da dimensão desejado. Este exemplo mostra como exibir **transações** no nível de conta em uma operação específica, selecionando valores para a dimensão **Nome da API**.

![Captura de tela de acesso às métricas com dimensão no portal do Azure](./media/monitor-blob-storage/access-metrics-portal-with-dimension.png)

Para ver uma lista completa das dimensões compatíveis com o Armazenamento do Microsoft Azure, consulte [Dimensões de métricas](monitor-blob-storage-reference.md#metrics-dimensions).

As métricas para o armazenamento de BLOBs do Azure estão nestes namespaces: 

- Microsoft.Storage/storageAccounts
- Microsoft.Storage/storageAccounts/blobServices

Para obter uma lista de todas as métricas de suporte de Azure Monitor, que inclui o armazenamento de BLOBs do Azure, consulte [Azure monitor métricas com suporte](../../azure-monitor/essentials/metrics-supported.md).


### <a name="accessing-metrics"></a>Acessando métricas

> [!TIP]
> Para exibir os exemplos da CLI do Azure ou do .NET, escolha as guias correspondentes listadas aqui.

### <a name="net"></a>[.NET](#tab/azure-portal)

O Azure Monitor fornece o [SDK do .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/) para ler valores e definição de métricas. O [código de exemplo](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/) mostra como usar o SDK com parâmetros diferentes. Você precisa usar `0.18.0-preview` ou uma versão posterior para métricas de armazenamento.
 
Nestes exemplos, substitua o `<resource-ID>` espaço reservado pela ID de recurso de toda a conta de armazenamento ou o serviço de armazenamento de BLOBs. Você pode encontrar essas IDs de recurso nas página de **propriedades** da sua conta de armazenamento no portal do Azure.

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
        // Resource ID for blob storage
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/blobServices/default";
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

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

#### <a name="list-the-metric-definition"></a>Listar a definição de métrica

Você pode listar a definição de métrica de sua conta de armazenamento ou o serviço de armazenamento de BLOBs. Use o cmdlet [Get-AzMetricDefinition](/powershell/module/az.monitor/get-azmetricdefinition).

Neste exemplo, substitua o `<resource-ID>` espaço reservado pela ID de recurso de toda a conta de armazenamento ou a ID de recurso do serviço de armazenamento de BLOBs.  Você pode encontrar essas IDs de recurso nas página de **propriedades** da sua conta de armazenamento no portal do Azure.

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetricDefinition -ResourceId $resourceId
```

#### <a name="reading-metric-values"></a>Lendo valores de métrica

Você pode ler os valores de métrica de nível de conta da sua conta de armazenamento ou do serviço de armazenamento de BLOBs. Use o cmdlet [Get-AzMetric](/powershell/module/Az.Monitor/Get-AzMetric).

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetric -ResourceId $resourceId -MetricNames "UsedCapacity" -TimeGrain 01:00:00
```

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

#### <a name="list-the-account-level-metric-definition"></a>Listar a definição de métrica no nível de conta

Você pode listar a definição de métrica de sua conta de armazenamento ou o serviço de armazenamento de BLOBs. Use o comando [az monitor metrics list-definitions](/cli/azure/monitor/metrics#az-monitor-metrics-list-definitions).
 
Neste exemplo, substitua o `<resource-ID>` espaço reservado pela ID de recurso de toda a conta de armazenamento ou a ID de recurso do serviço de armazenamento de BLOBs. Você pode encontrar essas IDs de recurso nas página de **propriedades** da sua conta de armazenamento no portal do Azure.

```azurecli-interactive
   az monitor metrics list-definitions --resource <resource-ID>
```

#### <a name="read-account-level-metric-values"></a>Ler os valores de métrica no nível de conta

Você pode ler os valores de métrica da sua conta de armazenamento ou do serviço de armazenamento de BLOBs. Use o comando [az monitor metrics list](/cli/azure/monitor/metrics#az-monitor-metrics-list).

```azurecli-interactive
   az monitor metrics list --resource <resource-ID> --metric "UsedCapacity" --interval PT1H
```
### <a name="template"></a>[Modelo](#tab/template)

N/D

---

## <a name="analyzing-logs"></a>Análise de logs

Você pode acessar os logs de recursos como um blob em uma conta de armazenamento, como dados de evento, ou por meio de consultas do Log Analytics.

Para obter uma referência detalhada dos campos que aparecem nesses logs, consulte [referência de dados de monitoramento do armazenamento de BLOBs do Azure](monitor-blob-storage-reference.md).

> [!NOTE]
> Os logs do Armazenamento do Microsoft Azure no Azure Monitor estão em versão preliminar pública e disponíveis para teste de versão preliminar em todas as regiões de nuvem pública. Essa versão preliminar habilita logs para blobs (que incluem Azure Data Lake Storage Gen2), arquivos, filas, tabelas, contas de armazenamento Premium nas contas de armazenamento GPv1 e GPv2. Não há suporte para contas de armazenamento clássicas.

As entradas de log são criadas somente se há solicitações feitas no ponto de extremidade de serviço. Por exemplo, se uma conta de armazenamento tiver atividades no ponto de extremidade de blob, mas não em nos pontos de extremidade de tabela ou fila, somente os logs pertencentes ao serviço de armazenamento de blobs serão criados. Os logs do Armazenamento do Microsoft Azure contêm informações detalhadas sobre solicitações bem-sucedidas e com falha para um serviço de armazenamento. Essas informações podem ser usadas para monitorar solicitações individuais e diagnosticar problemas com um serviço de armazenamento. As solicitações são registradas em uma base de melhor esforço.

### <a name="log-authenticated-requests"></a>Registrar em log solicitações autenticadas

 Os seguintes tipos de solicitações autenticadas são registrados:

- Solicitações bem-sucedidas
- Solicitações com falha, incluindo tempo limite, limitação, rede, autorização e outros erros
- Solicitações que usam uma SAS (Assinatura de Acesso Compartilhado) ou OAuth, incluindo solicitações bem-sucedidas e com falha
- Solicitações para dados de análise (dados de log clássicos no contêiner **$logs** e dados de métrica clássicos nas tabelas **$metric**)

As solicitações feitas pelo serviço de armazenamento de BLOBs, como criação ou exclusão de log, não são registradas. Para uma lista completa de dados registrados, consulte [Mensagens de operações e status registradas em logs](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) e [Formato de log de armazenamento](monitor-blob-storage-reference.md).

### <a name="log-anonymous-requests"></a>Registrar em log solicitações anônimas

 Os seguintes tipos de solicitações anônimas são registrados:

- Solicitações bem-sucedidas
- Erros do servidor
- Erros de tempo limite para o cliente e para o servidor
- Solicitações GET com falha com o código de erro 304 (não modificado)

Nenhuma outra solicitação anônima com falha é registrada em log. Para uma lista completa de dados registrados, consulte [Mensagens de operações e status registradas em logs](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) e [Formato de log de armazenamento](monitor-blob-storage-reference.md).

### <a name="accessing-logs-in-a-storage-account"></a>Acessando logs em uma conta de armazenamento

Os logs aparecem como blobs armazenados em um contêiner na conta de armazenamento de destino. Os dados são coletados e armazenados dentro de um único blob como um conteúdo JSON delimitado por linha. O nome do blob segue a convenção de nomenclatura:

`https://<destination-storage-account>.blob.core.windows.net/insights-logs-<storage-operation>/resourceId=/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<source-storage-account>/blobServices/default/y=<year>/m=<month>/d=<day>/h=<hour>/m=<minute>/PT1H.json`

Aqui está um exemplo:

`https://mylogstorageaccount.blob.core.windows.net/insights-logs-storagewrite/resourceId=/subscriptions/`<br>`208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/blobServices/default/y=2019/m=07/d=30/h=23/m=12/PT1H.json`

### <a name="accessing-logs-in-an-event-hub"></a>Acessando logs em um hub de eventos

Os logs enviados para um hub de eventos não são armazenados como um arquivo, mas você pode verificar se o hub de eventos recebeu as informações de log. No portal do Azure, navegue até seu hub de eventos e verifique se a contagem de **mensagens de entrada** é maior que zero. 

![Logs de auditoria](media/monitor-blob-storage/event-hub-log.png)

Você pode acessar e ler dados de log enviados ao seu hub de eventos usando informações de segurança e ferramentas de monitoramento e gerenciamento de eventos. Para mais informações, consulte [O que posso fazer com os dados de monitoramento que estão sendo enviados ao meu hub de eventos?](../../azure-monitor/essentials/stream-monitoring-data-event-hubs.md).

### <a name="accessing-logs-in-a-log-analytics-workspace"></a>Acessando logs em um espaço de trabalho Log Analytics

Você pode acessar os logs enviados a um workspace do Log Analytics usando consultas de log do Azure Monitor.

Para obter mais informações, confira [Introdução ao Log Analytics no Azure Monitor](../../azure-monitor/logs/log-analytics-tutorial.md).

Os dados são armazenados na tabela **StorageBlobLog** . Os logs para Data Lake Storage Gen2 não aparecem em uma tabela dedicada. Isso ocorre porque Data Lake Storage Gen2 não é serviço. É um conjunto de recursos que você pode habilitar em sua conta de armazenamento. Se você habilitou esses recursos, os logs continuarão a aparecer na tabela StorageBlobLogs. 

#### <a name="sample-kusto-queries"></a>Consultas de exemplo do Kusto

Aqui estão algumas consultas que você pode inserir na barra de **pesquisa de log** para ajudá-lo a monitorar seu armazenamento de BLOBs. Essas consultas funcionam com a [nova linguagem](../../azure-monitor/logs/log-query-overview.md).

> [!IMPORTANT]
> Quando você seleciona **logs** no menu do grupo de recursos da conta de armazenamento, log Analytics é aberta com o escopo de consulta definido como o grupo de recursos atual. Isso significa que as consultas de log incluirão apenas os dados desse grupo de recursos. Se você quiser executar uma consulta que inclui dados de outros recursos ou dados de outros serviços do Azure, selecione **logs** no menu **Azure monitor** . Confira [Escopo da consulta de log e intervalo de tempo no Log Analytics do Azure Monitor](../../azure-monitor/logs/scope.md) para obter detalhes.

Use essas consultas para ajudar a monitorar suas contas do Armazenamento do Microsoft Azure:

* Para listar os 10 erros mais comuns nos últimos três dias.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and StatusText !contains "Success"
    | summarize count() by StatusText
    | top 10 by count_ desc
    ```
* Para listar as 10 operações que causaram mais erros nos últimos três dias.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and StatusText !contains "Success"
    | summarize count() by OperationName
    | top 10 by count_ desc
    ```
* Para listar as 10 operações com a latência de ponta a ponta mais longa nos últimos três dias.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d)
    | top 10 by DurationMs desc
    | project TimeGenerated, OperationName, DurationMs, ServerLatencyMs, ClientLatencyMs = DurationMs - ServerLatencyMs
    ```
* Para listar todas as operações que causaram erros de limitação da largura de banda do lado do servidor nos últimos três dias.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and StatusText contains "ServerBusy"
    | project TimeGenerated, OperationName, StatusCode, StatusText
    ```
* Para listar todas as solicitações com acesso anônimo nos últimos três dias.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and AuthenticationType == "Anonymous"
    | project TimeGenerated, OperationName, AuthenticationType, Uri
    ```
* Para criar um gráfico de pizza das operações usadas nos últimos três dias.
    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d)
    | summarize count() by OperationName
    | sort by count_ desc 
    | render piechart
    ```
## <a name="faq"></a>Perguntas frequentes

**Armazenamento do Azure dá suporte a métricas para gerenciados discos ou discos não gerenciado?**

Não. A Computação do Azure suporta as métricas em discos. Para obter mais informações, consulte [Métricas por dicso para discos gerenciados e não gerenciados](https://azure.microsoft.com/blog/per-disk-metrics-managed-disks/).

## <a name="next-steps"></a>Próximas etapas

- Para obter uma referência dos logs e métricas criados pelo armazenamento de BLOBs do Azure, consulte [referência de dados de monitoramento do armazenamento de BLOBs do Azure](monitor-blob-storage-reference.md).
- Para ver informações detalhadas sobre o monitoramento dos recursos do Azure, consulte [Monitorar recursos do Azure com o Azure Monitor](../../azure-monitor/essentials/monitor-azure-resource.md).
- Para obter mais informações sobre a migração de métricas, consulte [Migração de métricas do Armazenamento do Microsoft Azure](../common/storage-metrics-migration.md).
