---
title: Habilitar e gerenciar métricas de Análise de Armazenamento do Azure (clássico) | Microsoft Docs
description: Saiba como habilitar, editar e exibir métricas de Análise de Armazenamento do Azure.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 01/29/2021
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring
ms.openlocfilehash: 784929e50d25a07ae92cf388be5ac14f6fa820a6
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2021
ms.locfileid: "99221506"
---
# <a name="enable-and-manage-azure-storage-analytics-metrics-classic"></a>Habilitar e gerenciar métricas de Análise de Armazenamento do Azure (clássico)

[Análise de armazenamento do Azure](storage-analytics.md) fornece métricas para todos os serviços de armazenamento para BLOBs, filas e tabelas. Você pode usar o [portal do Azure](https://portal.azure.com) para configurar quais métricas são registradas para sua conta e configurar gráficos que fornecem representações visuais de seus dados de métricas. Este artigo mostra como habilitar e gerenciar métricas. Para saber como habilitar logs, consulte [habilitar e gerenciar logs de análise de armazenamento do Azure (clássico)](manage-storage-analytics-logs.md).

Recomendamos que você examine o [Azure Monitor for Storage](../../azure-monitor/insights/storage-insights-overview.md) (versão prévia). Trata-se de um recurso do Azure Monitor que oferece monitoramento abrangente das contas do Armazenamento do Azure, fornecendo uma exibição unificada de desempenho, capacidade e disponibilidade dos serviços de Armazenamento do Azure. Ele não exige nenhuma habilitação ou configuração e você pode exibir imediatamente essas métricas nos gráficos interativos predefinidos e em outras visualizações incluídas.

> [!NOTE]
> Há custos associados ao exame de dados de monitoramento no portal do Azure. Para obter mais informações, consulte [Análise de Armazenamento](storage-analytics.md).
>
> As contas de armazenamento de blob de bloco de desempenho Premium não dão suporte a métricas Análise de Armazenamento. Se você quiser exibir as métricas com contas de armazenamento de blobs de bloco de desempenho premium, considere usar as [métricas de armazenamento do Azure no Azure monitor](../blobs/monitor-blob-storage.md).
>
> Para um guia aprofundado sobre como usar a Análise de Armazenamento e outras ferramentas para identificar, diagnosticar e solucionar problemas relacionados ao Armazenamento do Azure, consulte [Monitorar, diagnosticar e solucionar problemas do Armazenamento do Microsoft Azure](storage-monitoring-diagnosing-troubleshooting.md).
>

<a id="Enable-metrics"></a>

## <a name="enable-metrics"></a>Habilitar a métrica

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. No [portal do Azure](https://portal.azure.com), selecione **Contas de armazenamento** e o nome de conta de armazenamento para abrir o painel de conta.

2. Selecione **configurações de diagnóstico (clássico)** na seção **monitoramento (clássico)** da folha do menu.
   
   ![Captura de tela que realça a opção configurações de diagnóstico (clássico) na seção monitoramento (clássico).](./media/manage-storage-analytics-metrics/storage-enable-metrics-00.png)

3. Selecione o **tipo** de dados de métrica para cada **serviço** que você deseja monitorar e a **política de retenção** para os dados. Você também pode desabilitar o monitoramento definindo **Status** como **Desativado**.

   > [!div class="mx-imgBorder"]
   > ![Configure os logs no portal do Azure.](./media/manage-storage-analytics-logs/enable-diagnostics.png) 

   Para definir a política de retenção de dados, mova o controle deslizante **Retenção (dias)** ou insira o número de dias de dados devem ser retidos, de 1 a 365. O padrão para novas contas de armazenamento é de sete dias. Se não desejar definir uma política de retenção, digite zero. Se não houver nenhuma política de retenção, cabe a você excluir os dados de monitoramento.

   > [!WARNING]
   > Métricas são armazenados como dados em sua conta. Os dados de métrica podem ser acumulados em sua conta ao longo do tempo, o que pode aumentar o custo de armazenamento. Se precisar de dados de métrica por apenas um pequeno período de tempo, você poderá reduzir os custos modificando a política de retenção de dados. Dados de métricas obsoletos (dados anteriores à sua política de retenção) são excluídos pelo sistema. É recomendável definir uma política de retenção com base em quanto tempo você deseja manter os dados de métricas para sua conta. Consulte [Cobrança de métricas de armazenamento](storage-analytics-metrics.md#billing-on-storage-metrics) para obter mais informações.
   >

4. Ao concluir a configuração de monitoramento, selecione **Salvar**.

Um conjunto padrão de métricas é exibido em gráficos na folha **visão geral** , bem como na folha **métricas (clássicas)** . Depois que você habilita métricas para um serviço, pode levar até uma hora para que os dados apareçam nos gráficos. Você pode selecionar **Editar** em qualquer gráfico de métricas para configurar quais métricas serão exibidas.

Você pode desabilitar a coleta de métricas e o registro em log definindo **Status** como **Desativado**.

> [!NOTE]
> O Armazenamento do Azure usa o [armazenamento de tabelas](storage-introduction.md#table-storage) para armazenar as métricas para sua conta de armazenamento e armazena as métricas em tabelas em sua conta. Para obter mais informações, consulte: [Como as métricas são armazenadas](storage-analytics-metrics.md#how-metrics-are-stored).

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Abra uma janela de comando do Windows PowerShell.

2. Entre na sua assinatura do Azure com o comando `Connect-AzAccount` e siga as instruções na tela.

   ```powershell
   Connect-AzAccount
   ```

3. Se sua identidade estiver associada a mais de uma assinatura, defina sua assinatura ativa.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   Substitua o valor de espaço reservado `<subscription-id>` pela ID da sua assinatura.

5. Obtenha o contexto da conta de armazenamento que define a conta de armazenamento que você deseja usar.

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   $ctx = $storageAccount.Context
   ```

   * Substitua o valor do espaço reservado `<resource-group-name>` pelo nome do grupo de recursos.

   * Substitua o valor de espaço reservado `<storage-account-name>` pelo nome da sua conta de armazenamento. 

6. Você pode usar o PowerShell em seu computador local para configurar as métricas de armazenamento em sua conta de armazenamento. Use o cmdlet Azure PowerShell **set-AzStorageServiceMetricsProperty** para alterar as configurações atuais. 

   O comando a seguir alterna as métricas de minuto para o serviço blob em sua conta de armazenamento com o período de retenção definido como cinco dias.

   ```powershell
   Set-AzStorageServiceMetricsProperty -MetricsType Minute -ServiceType Blob -MetricsLevel ServiceAndApi  -RetentionDays 5 -Context $ctx
   ```   

   Esse cmdlet usa os seguintes parâmetros:  

   - **ServiceType**: Os possíveis valores são **Blob**, **Fila**, **Tabela** e **Arquivo**.
   - **MetricsType**: Os valores possíveis são **Hora** e **Minuto**.  
   - **MetricsLevel**: Os valores possíveis são:
      - **Nenhum**: Desativa o monitoramento.
      - **Serviço**: Coleta métricas como entrada e saída, disponibilidade, latência e porcentagens de êxitos, que são agregadas aos serviços blob, fila, tabela e arquivo.
      - **ServiceAndApi**: Além das métricas de serviço, coleta o mesmo conjunto de métricas para cada operação de armazenamento na API do Serviço de Armazenamento do Microsoft Azure.

   O comando a seguir recupera o nível de métricas por hora atual e dias de retenção para o serviço blob na conta de armazenamento padrão:  

   ```powershell
   Get-AzStorageServiceMetricsProperty -MetricsType Hour -ServiceType Blob -Context $storagecontext.Context
   ```  

   Para saber mais sobre como configurar os cmdlets do PowerShell do Azure para funcionar com sua assinatura do Azure e como selecionar a conta de armazenamento padrão para usar, consulte [Instalar e configurar o Microsoft Azure PowerShell](/powershell/azure/).  

### <a name="net-v12"></a>[.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/Monitoring.cs" id="snippet_EnableDiagnosticLogs":::

Para obter mais informações sobre como usar uma linguagem .NET para configurar Métricas de Armazenamento, consulte [Bibliotecas de clientes do Armazenamento do Microsoft Azure para .NET](/dotnet/api/overview/azure/storage).  

Para obter informações gerais sobre a configuração de métricas de armazenamento usando a API REST, consulte [Habilitação e configuração da Análise de Armazenamento](/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics). 

### <a name="net-v11"></a>[.NET v11](#tab/dotnet11)  

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.HourMetrics.MetricsLevel = MetricsLevel.Service;  
serviceProperties.HourMetrics.RetentionDays = 10;  

queueClient.SetServiceProperties(serviceProperties);  
```  

Para obter mais informações sobre como usar uma linguagem .NET para configurar Métricas de Armazenamento, consulte [Bibliotecas de clientes do Armazenamento do Microsoft Azure para .NET](/dotnet/api/overview/azure/storage).  

Para obter informações gerais sobre a configuração de métricas de armazenamento usando a API REST, consulte [Habilitação e configuração da Análise de Armazenamento](/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics). 

---

<a id="view-metrics"></a>

## <a name="view-metrics-in-a-chart"></a>Exibir métricas em um gráfico

Após configurar as métricas Análise de Armazenamento para monitorar sua conta de armazenamento, a Análise de Armazenamento registra as métricas em um conjunto conhecido de tabelas na sua conta de armazenamento. Você pode configurar gráficos para exibir métricas por hora no [portal do Azure](https://portal.azure.com).

Use o procedimento a seguir para escolher quais métricas de armazenamento devem ser exibidas em um gráfico de métricas.

1. Comece exibindo um gráfico de métricas de armazenamento no portal do Azure. Você pode encontrar gráficos na **folha da conta de armazenamento** e na folha **métricas (clássicas)** .

   Neste exemplo, usa o gráfico a seguir, que aparece na **folha de conta de armazenamento**:

   ![Seleção de gráfico no portal do Azure](./media/manage-storage-analytics-metrics/stg-customize-chart-00.png)

2. Clique em qualquer lugar dentro do gráfico para editar o gráfico.

3. Em seguida, selecione **Editar Gráfico**, selecione o Intervalo de Tempo das métricas para exibir no gráfico e o **service** (blob, fila, tabela, arquivo) cujas métricas você deseja exibir. Aqui, as métricas da semana passada são selecionadas para exibir o serviço de blob:

   ![Intervalo de tempo e seleção de serviço na folha Editar Gráfico](./media/manage-storage-analytics-metrics/storage-edit-metric-time-range.png)

4. Selecione as **métricas** individuais que deseja exibir no gráfico e clique em **OK**.

   ![Seleção de métrica individual na folha Editar Gráfico](./media/manage-storage-analytics-metrics/storage-edit-metric-selections.png)

Suas configurações de gráfico não afetam a coleta, agregação ou armazenamento de dados de monitoramento na conta de armazenamento.

#### <a name="metrics-availability-in-charts"></a>Disponibilidade de métricas em gráficos

A lista de métricas disponíveis é alterada com base em qual serviço você escolheu na lista suspensa e qual tipo de unidade do gráfico você está editando. Por exemplo, você só poderá selecionar as métricas de percentual como *PercentNetworkError* e *PercentThrottlingError* se estiver editando um gráfico que exiba as unidades em percentual:

![Gráfico de percentual de erro de solicitação no portal do Azure](./media/manage-storage-analytics-metrics/stg-customize-chart-04.png)

#### <a name="metrics-resolution"></a>Resolução de métricas

As métricas que você selecionou em **Diagnósticos** determinam a resolução das métricas que estão disponíveis para sua conta:

* O monitoramento **Agregado** fornece métricas como percentuais de entrada/saída, disponibilidade, latência e sucesso. Essas métricas são agregadas dos serviços de blob, tabela, arquivo e fila.
* **Por API** fornece resolução mais detalhada, com métricas disponíveis para operações de armazenamento individuais, além de agregações de nível de serviço.

## <a name="download-metrics-to-archive-or-analyze-locally"></a>Baixar métricas para arquivar ou analisar localmente

Se você quiser baixar as métricas para armazenamento a longo prazo ou para analisá-las localmente, você deve usar uma ferramenta ou escrever um código para ler as tabelas. As tabelas não aparecem quando você lista todas as tabelas em sua conta de armazenamento, mas você pode acessá-las diretamente por nome. Muitas ferramentas de navegação de armazenamento reconhecem essas tabelas e permitem que você as exiba diretamente. Para obter uma lista das ferramentas disponíveis, confira as [ferramentas do cliente de Armazenamento do Microsoft Azure](./storage-explorers.md).

|Métricas|Nomes da tabela|Anotações| 
|-|-|-|  
|Métricas por hora|$MetricsHourPrimaryTransactionsBlob<br /><br /> $MetricsHourPrimaryTransactionsTable<br /><br /> $MetricsHourPrimaryTransactionsQueue<br /><br /> $MetricsHourPrimaryTransactionsFile|Nas versões anteriores a 15 de agosto de 2013, essas tabelas eram conhecidas como:<br /><br /> $MetricsTransactionsBlob<br /><br /> $MetricsTransactionsTable<br /><br /> $MetricsTransactionsQueue<br /><br /> As métricas do serviço de arquivo estão disponíveis a partir da versão de 5 de abril de 2015.|  
|Métricas por minuto|$MetricsMinutePrimaryTransactionsBlob<br /><br /> $MetricsMinutePrimaryTransactionsTable<br /><br /> $MetricsMinutePrimaryTransactionsQueue<br /><br /> $MetricsMinutePrimaryTransactionsFile|Só podem ser habilitadas usando o PowerShell ou programaticamente.<br /><br /> As métricas do serviço de arquivo estão disponíveis a partir da versão de 5 de abril de 2015.|  
|Capacity|$MetricsCapacityBlob|Somente para serviço blob.|  

Para obter detalhes completos dos esquemas dessas tabelas, confira [Esquema de tabelas das métricas da Análise de Armazenamento](/rest/api/storageservices/storage-analytics-metrics-table-schema). As linhas de exemplo a seguir mostram apenas um subconjunto das colunas disponíveis, mas ilustram alguns recursos importantes da maneira como as métricas de armazenamento salvam essas métricas:  

|PartitionKey|RowKey|Timestamp|TotalRequests|TotalBillableRequests|TotalIngress|TotalEgress|Disponibilidade|AverageE2ELatency|AverageServerLatency|PercentSuccess| 
|-|-|-|-|-|-|-|-|-|-|-|  
|20140522T1100|user;All|2014-05-22T11:01:16.7650250Z|7|7|4003|46801|100|104.4286|6.857143|100|  
|20140522T1100|usuário;QueryEntities|2014-05-22T11:01:16.7640250Z|5|5|2694|45951|100|143.8|7.8|100|  
|20140522T1100|user;QueryEntity|2014-05-22T11:01:16.7650250Z|1|1|538|633|100|3|3|100|  
|20140522T1100|user;UpdateEntity|2014-05-22T11:01:16.7650250Z|1|1|771|217|100|9|6|100|  

Neste exemplo dos dados de métrica de minutos, a chave de partição usa o tempo de resolução minuto. A tecla de registro identifica o tipo de informação que é armazenado na linha. As informações são compostas pelo tipo de acesso e pelo tipo de solicitação:  

-   O tipo de acesso é um **usuário** ou **sistema**, onde o **usuário** refere-se a todas as solicitações de usuário para o serviço de armazenamento e o **sistema** refere-se às solicitações feitas pela Análise de Armazenamento.  
-   O tipo de solicitação é **todos** que, nesse caso, é uma linha de resumo ou identifica a API específica, como **QueryEntity** ou **UpdateEntity**.  

Esses dados de amostra mostram todos os registros para um único minuto (a partir das 11:00). Portanto, o número de solicitações de **QueryEntities** mais o número de solicitações de **QueryEntity** mais o número de solicitações de **UpdateEntity** somam sete. Esse total é mostrado na linha **user:All**. Da mesma forma, você pode derivar a latência média de ponta a ponta 104.4286 na linha **user:All** ao calcular ((143,8 * 5) + 3 + 9)/7.  

## <a name="view-metrics-data-programmatically"></a>Exibir dados de métricas programaticamente

A listagem a seguir mostra o código C# de exemplo, que acessa a métrica de minutos para um intervalo de minutos e exibe os resultados em uma janela de console. O exemplo de código usa a biblioteca de clientes do Armazenamento do Microsoft Azure versão 4.x ou posterior, que inclui a classe **CloudAnalyticsClient** que simplifica o acesso às tabelas de métricas no armazenamento. 

> [!NOTE]
> A classe **CloudAnalyticsClient** não está incluída na biblioteca de cliente do armazenamento de BLOBs do Azure V12 para .net. Em **31 de agosto de 2023** análise de armazenamento métricas, também conhecidas como *métricas clássicas* serão desativadas. Para saber mais, confira o [anúncio oficial](https://azure.microsoft.com/updates/azure-storage-classic-metrics-will-be-retired-on-31-august-2023/). Se você usar métricas clássicas, recomendamos que você migre para métricas em Azure Monitor antes dessa data. 

```csharp
private static void PrintMinuteMetrics(CloudAnalyticsClient analyticsClient, DateTimeOffset startDateTime, DateTimeOffset endDateTime)  
{  
 // Convert the dates to the format used in the PartitionKey.  
 var start = startDateTime.ToUniversalTime().ToString("yyyyMMdd'T'HHmm");  
 var end = endDateTime.ToUniversalTime().ToString("yyyyMMdd'T'HHmm");  

 var services = Enum.GetValues(typeof(StorageService));  
 foreach (StorageService service in services)  
 {  
     Console.WriteLine("Minute Metrics for Service {0} from {1} to {2} UTC", service, start, end);  
     var metricsQuery = analyticsClient.CreateMinuteMetricsQuery(service, StorageLocation.Primary);  
     var t = analyticsClient.GetMinuteMetricsTable(service);  
     var opContext = new OperationContext();  
     var query =  
             from entity in metricsQuery  
             // Note, you can't filter using the entity properties Time, AccessType, or TransactionType  
             // because they are calculated fields in the MetricsEntity class.  
             // The PartitionKey identifies the DataTime of the metrics.  
             where entity.PartitionKey.CompareTo(start) >= 0 && entity.PartitionKey.CompareTo(end) <= 0   
             select entity;  

     // Filter on "user" transactions after fetching the metrics from Azure Table storage.  
     // (StartsWith is not supported using LINQ with Azure Table storage.)  
     var results = query.ToList().Where(m => m.RowKey.StartsWith("user"));  
     var resultString = results.Aggregate(new StringBuilder(), (builder, metrics) => builder.AppendLine(MetricsString(metrics, opContext))).ToString();  
     Console.WriteLine(resultString);  
 }  
}  

private static string MetricsString(MetricsEntity entity, OperationContext opContext)  
{  
 var entityProperties = entity.WriteEntity(opContext);  
 var entityString =  
         string.Format("Time: {0}, ", entity.Time) +  
         string.Format("AccessType: {0}, ", entity.AccessType) +  
         string.Format("TransactionType: {0}, ", entity.TransactionType) +  
         string.Join(",", entityProperties.Select(e => new KeyValuePair<string, string>(e.Key.ToString(), e.Value.PropertyAsObject.ToString())));  
 return entityString;  
}  
```  

<a id="add-metrics-to-dashboard"></a>

## <a name="add-metrics-charts-to-the-portal-dashboard"></a>Adicionar gráficos de métricas para o painel do portal

Você pode adicionar gráficos de métricas do Armazenamento do Azure para qualquer uma de suas contas de armazenamento no painel do portal.

1. Clique para selecionar **Editar painel** ao exibir o painel no [portal do Azure](https://portal.azure.com).
1. Na **Galeria de Blocos**, selecione **Localizar blocos por** > **Tipo**.
1. Selecione **Tipo** > **Contas de armazenamento**.
1. Em **Recursos**, selecione a conta de armazenamento cujas métricas deseja adicionar ao painel.
1. Selecione **Categorias** > **Monitoramento**.
1. Arraste e solte o bloco do gráfico no painel para a métrica que você deseja exibir. Repita para todas as métricas que deseja exibir no painel. Na imagem a seguir, o gráfico "Blobs - total de solicitações" está realçado como um exemplo, mas todos os gráficos estão disponíveis para posicionamento em seu painel.

   ![Galeria de blocos no portal do Azure](./media/manage-storage-analytics-metrics/storage-customize-dashboard.png)
1. Selecione **Personalização concluída** na parte superior do painel quando terminar a adição de gráficos.

Depois de adicionar gráficos ao painel, você pode personalizá-los conforme descrito em Personalizar gráficos de métricas.

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre Análise de Armazenamento, consulte [análise de armazenamento](storage-analytics.md) para análise de armazenamento.
* [Configurar Logs de análise de armazenamento](manage-storage-analytics-logs.md).
* Saiba mais sobre o esquema de métricas. Consulte [análise de armazenamento esquema da tabela de métricas](/rest/api/storageservices/storage-analytics-metrics-table-schema).