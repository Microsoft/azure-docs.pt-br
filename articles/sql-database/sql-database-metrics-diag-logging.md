---
title: "Métricas de banco de dados SQL do Azure e o log de diagnóstico | Microsoft Docs"
description: "Saiba mais sobre como configurar recursos de banco de dados SQL do Azure para armazenar as estatísticas de execução de consulta, conectividade e uso de recursos."
services: sql-database
documentationcenter: 
author: vvasic
manager: jhubbard
editor: 
ms.assetid: 89c2a155-c2fb-4b67-bc19-9b4e03c6d3bc
ms.service: sql-database
ms.custom: monitor & tune
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/16/2017
ms.author: vvasic
ms.openlocfilehash: a56d48eaf335d9e78eeba99162cea7c61d96b7cb
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-sql-database-metrics-and-diagnostics-logging"></a>Métricas de banco de dados SQL do Azure e o log de diagnóstico 
O Banco de Dados SQL do Azure pode emitir métrica e logs de diagnóstico para facilitar o monitoramento. Você pode configurar o Banco de Dados SQL do Azure para armazenar o uso de recursos, trabalhos, sessões e conectividade em um destes recursos do Azure:
- **Armazenamento do Azure**: para o arquivamento de grandes quantidades de telemetria por um pequeno baixo
- **Hub de Eventos do Azure**: para a integração de telemetria de Banco de Dados SQL do Azure com a sua solução de monitoramento personalizada ou pipelines ativos
- **Análise de logs do Azure**: Para solução de monitoramento fora da caixa com relatórios, alertas e recursos de mitigação 

    ![Arquitetura](./media/sql-database-metrics-diag-logging/architecture.png)

## <a name="enable-logging"></a>Habilitar o registro em log

Métricas e log de diagnóstico não está habilitado por padrão. Você pode habilitar e gerenciar as métricas e diagnóstico de log usando um dos seguintes métodos:
- Portal do Azure
- PowerShell
- CLI do Azure
- API REST 
- Modelo do Resource Manager

Quando você habilitar o log de diagnóstico e métricas, você precisa especificar os recursos do Azure, onde os dados selecionados são coletados. Opções disponíveis:
- Log Analytics
- Hub de evento
- Armazenamento do Azure 

Você pode provisionar um novo recurso do Azure ou selecionar um recurso existente. Depois de selecionar o recurso de armazenamento, você precisa especificar quais dados coletar. As opções disponíveis incluem:

- **[métricas de 1 minuto](sql-database-metrics-diag-logging.md#1-minute-metrics)**  - contém o percentual DTU, o limite DTU, o percentual de CPU, porcentagem de leitura de dados físicos, porcentagem de gravação de log, êxito/falha/bloqueados por conexões de firewall, porcentagem de sessões, porcentagem de funcionários, armazenamento, porcentagem de armazenamento, porcentagem de armazenamento XTP
- **[QueryStoreRuntimeStatistics](sql-database-metrics-diag-logging.md#query-store-runtime-statistics)** – contém informações sobre as estatísticas de tempo de execução da consulta, como uso de cpu, duração da consulta, etc.
- **[QueryStoreWaitStatistics](sql-database-metrics-diag-logging.md#query-store-wait-statistics)**  – contém informações sobre as estatísticas de espera da consulta que informa você sobre o que suas consultas aguardaram, como CPU, LOG, LOCKING...
- **[Erros](sql-database-metrics-diag-logging.md#errors-dataset)** – contém informações sobre erros de SQL que ocorreram neste banco de dados.
- **[DatabaseWaitStatistics](sql-database-metrics-diag-logging.md#database-waits-dataset)** – contém informações sobre a quantidade de tempo de espera de um banco de dados em diferentes tipos de espera.
- **[Tempos limite](sql-database-metrics-diag-logging.md#timeouts-dataset)** – contém informações sobre a quantidade de tempo de espera de um banco de dados em diferentes tipos de espera.
- **[Bloqueios](sql-database-metrics-diag-logging.md#blockings-dataset)**  – contém informações sobre eventos de bloqueio ocorridos em um banco de dados.
- **[SQLInsights](sql-database-metrics-diag-logging.md#intelligent-insights-dataset)** –contém o recurso Insights inteligentes. [Saiba mais sobre Insights inteligentes](sql-database-intelligent-insights.md)

Se você especificar uma conta de AzureStorage ou Hub de eventos, você pode especificar uma política de retenção para especificar que os dados mais antigos do que um período de tempo selecionado é excluído. Se você especificar a análise de Log, a política de retenção depende do tipo de preço selecionado. Saiba mais sobre [Preço do Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/). 

Recomendamos que você leia os artigos [Visão geral de métricas no Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md) e [Visão geral dos Logs de diagnóstico do Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) para entender não apenas como habilitar o registro em log, mas também as métricas e categorias de log com suporte dos vários serviços do Azure.

### <a name="azure-portal"></a>Portal do Azure

Para habilitar as métricas e coleta de logs de diagnóstico no portal do Azure, navegue até o banco de dados SQL Azure ou página de pool elástico e, em seguida, clique em **Configurações de diagnóstico**.

   ![habilitar no portal do Azure](./media/sql-database-metrics-diag-logging/enable-portal.png)

Crie uma nova configuração ou edite configurações de diagnóstico existentes selecionando o destino e a telemetria.

   ![configurar definições de diagnóstico](./media/sql-database-metrics-diag-logging/diagnostics-portal.png)

### <a name="powershell"></a>PowerShell

Para habilitar as métricas e o log de diagnóstico usando o PowerShell, use os seguintes comandos:

- Para habilitar o armazenamento dos Logs de Diagnóstico em uma Conta de Armazenamento, use este comando:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   A ID da Conta de Armazenamento é a ID de recurso da conta de armazenamento para a qual você deseja enviar os logs.

- Para habilitar o streaming dos Logs de Diagnóstico para um Hub de Eventos, use este comando:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
   ```

   A ID da Regra do Barramento de Serviço é uma cadeia de caracteres com este formato:

   ```powershell
   {service bus resource ID}/authorizationrules/{key name}
   ``` 

- Para habilitar o envio dos Logs de Diagnóstico para um espaço de trabalho do Log Analytics, use este comando:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
   ```

- É possível obter a ID de recurso do seu espaço de trabalho do Log Analytics usando o seguinte comando:

   ```powershell
   (Get-AzureRmOperationalInsightsWorkspace).ResourceId
   ```

Você pode combinar esses parâmetros para permitir várias opções de saída.

### <a name="to-configure-multiple-azure-subscriptions"></a>Para configurar várias assinaturas do Azure

Para dar suporte a várias assinaturas, use o script do PowerShell de [Habilitar log de métricas de recursos do Azure usando o PowerShell](https://blogs.technet.microsoft.com/msoms/2017/01/17/enable-azure-resource-metrics-logging-using-powershell/). Forneça a ID de recurso do espaço de trabalho como um parâmetro ao executar o script para enviar dados de diagnóstico de recursos em uma assinatura do Azure a um espaço de trabalho em outra assinatura do Azure.

- Para configurar várias assinaturas do Azure, use os seguintes comandos:

    ```powershell
    PS C:\> $WSID = "/subscriptions/<subID>/resourcegroups/oms/providers/microsoft.operationalinsights/workspaces/omsws"
    PS C:\> .\Enable-AzureRMDiagnostics.ps1 -WSID $WSID
    ```

### <a name="azure-cli"></a>CLI do Azure

Para habilitar as métricas e o log de diagnóstico usando o CLI do Azure, use os seguintes comandos:

- Para habilitar o armazenamento dos Logs de Diagnóstico em uma Conta de Armazenamento, use este comando:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
   ```

   A ID da Conta de Armazenamento é a ID de recurso da conta de armazenamento para a qual você deseja enviar os logs.

- Para habilitar o streaming dos Logs de Diagnóstico para um Hub de Eventos, use este comando:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
   ```

   A ID da Regra do Barramento de Serviço é uma cadeia de caracteres com este formato:

   ```azurecli-interactive
   {service bus resource ID}/authorizationrules/{key name}
   ```

- Para habilitar o envio dos Logs de Diagnóstico para um espaço de trabalho do Log Analytics, use este comando:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
   ```

Você pode combinar esses parâmetros para permitir várias opções de saída.

### <a name="rest-api"></a>API REST

Saiba sobre como [alterar as Configurações de Diagnóstico usando a API REST do Azure Monitor](https://msdn.microsoft.com/library/azure/dn931931.aspx). 

### <a name="resource-manager-template"></a>Modelo do Resource Manager

Leia sobre como [habilitar as Configurações de Diagnóstico na criação do recurso usando o modelo do Resource Manager](../monitoring-and-diagnostics/monitoring-enable-diagnostic-logs-using-template.md). 

## <a name="stream-into-log-analytics"></a>Fluxo em Log Analytics 
Métricas de banco de dados SQL do Azure e logs de diagnóstico podem ser transmitidos em Log Analytics usando a opção "Enviar para análise de Log" incorporada no portal ou, habilitando o Log Analytics em uma configuração de diagnóstico por meio de cmdlets do PowerShell do Azure, CLI do Azure ou API REST do Azure Monitor.

### <a name="installation-overview"></a>Visão geral da instalação

Monitorar fleet de banco de dados SQL do Azure é simples com Log Analytics. Três etapas são necessárias:

1. Criar recursos de Log Analytics
2. Configurar bancos de dados para gravar logs de diagnóstico e métricas para o Log Analytics criado
3. Instalar a solução de **Análise de SQL do Azure** na galeria do Log Analytics

### <a name="create-log-analytics-resource"></a>Criar recursos de Log Analytics

1. Clique em **Novo** no menu da esquerda.
2. Clique em **Monitoramento + Gerenciamento**
3. Clique em **Log Analytics**
4. Preencha o formulário de Log Analytics com as informações adicionais necessárias: nome do espaço de trabalho, assinatura, grupo de recursos, local e tipo de preço.

   ![log analytics](./media/sql-database-metrics-diag-logging/log-analytics.png)

### <a name="configure-databases-to-record-metrics-and-diagnostic-logs"></a>Configurar bancos de dados para gravar logs de diagnóstico e métricas

A maneira mais fácil de configurar onde os bancos de dados registram suas métricas é por meio do portal do Azure. No portal do Azure, navegue até o recurso de banco de dados SQL do Azure e clique em **Configurações de diagnóstico**. 

### <a name="install-the-azure-sql-analytics-solution-from-gallery"></a>Instalar a solução de Análise de SQL do Azure na galeria  

1. Depois que o recurso de Log Analytics é criado e seus dados estão fluindo nele, instale a solução de análise de SQL do Azure. Isso pode ser feito por meio da **Galeria de soluções** que você pode encontrar na home page do OMS e no menu lateral. Na galeria, localize e clique em solução de **Análise de SQL do Azure** e clique em **Adicionar**.

   ![solução de monitoramento](./media/sql-database-metrics-diag-logging/monitoring-solution.png)

2. Na home page do seu OMS, um novo bloco chamado **Análise de SQL do Azure** é exibido. Selecionar este bloco abre o painel de Análise de SQL do Azure.

### <a name="using-azure-sql-analytics-solution"></a>Usando a solução de Análise de SQL do Azure

Análise de SQL do Azure é um painel hierárquico que permite navegar pela hierarquia de recursos de banco de dados SQL do Azure. [Clique aqui para saber como usar a solução de Análise de SQL do Azure.](../log-analytics/log-analytics-azure-sql.md)

## <a name="stream-into-azure-event-hub"></a>Fluxo no Hub de eventos do Azure

Métricas de banco de dados SQL do Azure e logs de diagnóstico podem ser transmitidos em Hub de eventos usando a opção "Enviar para um hub de eventos" incorporada no portal ou, habilitando a Regra de Barramento de Serviço em uma configuração de diagnóstico por meio de Cmdlets do PowerShell do Azure, CLI do Azure ou API REST do Azure Monitor. 

### <a name="what-to-do-with-metrics-and-diagnostic-logs-in-event-hub"></a>O que fazer com as métricas e os logs de diagnóstico no Hub de eventos?
Depois que os dados selecionados são transmitidos para o Hub de eventos, você está mais próximo de habilitar cenários de monitoramento avançado. Os Hub de Eventos agem como a "porta de entrada” para um pipeline de eventos e depois que os dados são coletados em um Hub de Eventos, eles podem ser transformados e armazenados usando qualquer provedor de análise em tempo real ou adaptadores de envio em lote/armazenamento. Hub de Eventos separa a produção de um fluxo de eventos do consumo desses eventos, para que os consumidores de eventos possam acessar os eventos em seu próprio cronograma. Para obter mais informações sobre o Hub de eventos, consulte:

- [O que são Hubs de Eventos do Azure](../event-hubs/event-hubs-what-is-event-hubs.md)?
- [Introdução aos Hubs de Evento](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)


Veja algumas maneiras de usar o recurso de streaming:

-             Exibir a integridade do serviço transmitindo dados de "afunilamento" para o Power BI - Usando os Hubs de Eventos, Stream Analytics e o Power BI, é fácil transformar suas métricas e dados de diagnóstico em informações quase em tempo real nos serviços do Azure. Para uma visão geral de como configurar um Hubs de Eventos, processar dados com o Stream Analytics e usar o Power BI como uma saída, consulte [Stream Analytics e Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md).
-             Logs de fluxo para fluxos de log e telemetria de terceiros – Usando a transmissão de Hubs de eventos, você pode obter suas métricas e logs de diagnóstico para soluções de log analytics e monitoramento de terceiros diferentes. 
-             Criar uma plataforma de registro em log e telemetria personalizada – Se você já tiver uma plataforma de telemetria personalizada ou estiver pensando em criar uma, a natureza altamente escalonável de publicação-assinatura dos Hubs de Eventos permite a flexibilidade de ingestão de logs de diagnóstico. Consulte [o guia de Dan Rosanova sobre como usar os Hubs de Eventos em uma plataforma de telemetria de escala global](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/).

## <a name="stream-into-azure-storage"></a>Fluxo no Armazenamento do Azure

Métricas de banco de dados SQL do Azure e logs de diagnóstico podem ser armazenados no Armazenamento do Azure usando a opção "Arquivar para uma conta de armazenamento" incorporada no portal do Azure, ou habilitando o Armazenamento do Azure em uma configuração de diagnóstico por meio de Cmdlets do PowerShell do Azure, CLI do Azure ou API REST do Azure Monitor.

### <a name="schema-of-metrics-and-diagnostic-logs-in-the-storage-account"></a>Esquema de métricas e logs de diagnóstico na conta de armazenamento

Depois que você configurar as métricas e coleta de logs de diagnóstico, um contêiner de armazenamento é criado na conta de armazenamento que você selecionou quando as primeiras linhas de dados estão disponíveis. A estrutura desses blobs é:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ databases/{database_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```
    
Ou, simplesmente:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/{resource Id}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Por exemplo, um nome de blob para métricas de 1 minuto pode ser:

```powershell
insights-metrics-minute/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.SQL/ servers/Server1/databases/database1/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

No caso de você desejar registrar os dados do Pool Elástico, o nome do blob é um pouco diferente:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ elasticPools/{elastic_pool_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

### <a name="download-metrics-and-logs-from-azure-storage"></a>Baixar logs e métricas do Armazenamento do Azure

Consulte [Baixar métricas e logs de diagnósticos do Armazenamento do Azure](../storage/blobs/storage-dotnet-how-to-use-blobs.md#download-blobs)

## <a name="metrics-and-logs-available"></a>Métricas e logs disponíveis

### <a name="1-minute-metrics"></a>métricas de 1 minuto

|**Recurso**|**Métricas**|
|---|---|
|Banco de dados|porcentagem de DTU, DTU usado, o limite DTU, o porcentagem de CPU, porcentagem de leitura de dados físicos, porcentagem de gravação de log, êxito/falha/bloqueados por conexões de firewall, porcentagem de sessões, porcentagem de funcionários, armazenamento, porcentagem de armazenamento, porcentagem de armazenamento XTP, deadlocks |
|Pool elástico|porcentagem de eDTU, eDTU usado, o limite eDTU, porcentagem de CPU, porcentagem de leitura de dados físicos, porcentagem de gravação de log, porcentagem de sessões, porcentagem de funcionários, armazenamento, porcentagem de armazenamento, limite de armazenamento, porcentagem de armazenamento XTP |
|||

### <a name="query-store-runtime-statistics"></a>Estatísticas de tempo de execução do Repositório de consultas

|Propriedade|Descrição|
|---|---|
|TenantId|Sua id de locatário.|
|SourceSystem|Sempre: Azure|
|TimeGenerated [UTC]|Carimbo de data/hora de quando o log foi gravado.|
|Tipo|Sempre: AzureDiagnostics|
|ResourceProvider|Nome do provedor de recursos. Sempre: MICROSOFT.SQL|
|Categoria|Nome da categoria. Sempre: QueryStoreRuntimeStatistics|
|OperationName|Nome da operação. Sempre: QueryStoreRuntimeStatisticsEvent|
|Recurso|Nome do recurso|
|ResourceType|Nome do tipo de recurso. Sempre: SERVIDORES/BANCOS DE DADOS|
|SubscriptionId|GUID de assinatura ao qual o banco de dados pertence.|
|ResourceGroup|Nome do grupo de recursos ao qual o banco de dados pertence.|
|LogicalServerName_s|Nome do servidor ao qual o banco de dados pertence.|
|ElasticPoolName_s|Nome do pool elástico ao qual o banco de dados pertence, se houver.|
|DatabaseName_s|Nome do banco de dados|
|ResourceId|URI de recurso|
|query_hash_s|Hash de consulta|
|query_plan_hash_s|Hash do plano de consulta|
|statement_sql_handle_s|Identificador de sql da instrução|
|interval_start_time_d|Inicie o datetimeoffset do intervalo em número de tiques de 1900-1-1.|
|interval_end_time_d|Encerre o datetimeoffset do intervalo em número de tiques de 1900-1-1.|
|logical_io_writes_d|Número total de gravações lógicas de E/S.|
|max_logical_io_writes_d|Número máximo de gravações lógicas de E/S por execução.|
|physical_io_reads_d|Número total de leituras físicas de E/S.|
|max_physical_io_reads_d|Número máximo de leituras lógicas de E/S por execução.|
|logical_io_reads_d|Número total de leituras lógicas de E/S.|
|max_logical_io_reads_d|Número máximo de leituras lógicas de E/S por execução.|
|execution_type_d|Tipo de execução|
|count_executions_d|Número de execuções da consulta.|
|cpu_time_d|Tempo total de CPU consumido pela consulta em microssegundos.|
|max_cpu_time_d|Tempo máximo de CPU do consumidor por uma execução única em microssegundos.|
|dop_d|Soma de graus de paralelismo.|
|max_dop_d|Grau máximo de paralelismo usado para execução única.|
|rowcount_d|Número total de linhas retornadas.|
|max_rowcount_d|Número máximo de linhas retornadas em uma única execução.|
|query_max_used_memory_d|Quantidade total de memória usada em KB.|
|max_query_max_used_memory_d|Quantidade máxima de memória usada por uma única execução em KB.|
|duration_d|Tempo total de execução em microssegundos.|
|max_duration_d|Tempo máximo de execução de uma única execução.|
|num_physical_io_reads_d|Número total de leituras físicas.|
|max_num_physical_io_reads_d|Número máximo de leituras físicas por execução.|
|log_bytes_used_d|Quantidade total de bytes de log usados.|
|max_log_bytes_used_d|Quantidade máxima de bytes de log usados por execução.|
|query_id_d|ID da consulta no Repositório de consultas|
|plan_id_d|ID do plano no Repositório de consultas|

[Saiba mais sobre os dados de estatísticas de tempo de execução do Repositório de consultas.](https://docs.microsoft.com/en-us/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql)

### <a name="query-store-wait-statistics"></a>Estatísticas de espera do Repositório de consultas

|Propriedade|Descrição|
|---|---|
|TenantId|Sua id de locatário.|
|SourceSystem|Sempre: Azure|
|TimeGenerated [UTC]|Carimbo de data/hora de quando o log foi gravado.|
|Tipo|Sempre: AzureDiagnostics|
|ResourceProvider|Nome do provedor de recursos. Sempre: MICROSOFT.SQL|
|Categoria|Nome da categoria. Sempre: QueryStoreRuntimeStatistics|
|OperationName|Nome da operação. Sempre: QueryStoreRuntimeStatisticsEvent|
|Recurso|Nome do recurso|
|ResourceType|Nome do tipo de recurso. Sempre: SERVIDORES/BANCOS DE DADOS|
|SubscriptionId|GUID de assinatura ao qual o banco de dados pertence.|
|ResourceGroup|Nome do grupo de recursos ao qual o banco de dados pertence.|
|LogicalServerName_s|Nome do servidor ao qual o banco de dados pertence.|
|ElasticPoolName_s|Nome do pool elástico ao qual o banco de dados pertence, se houver.|
|DatabaseName_s|Nome do banco de dados|
|ResourceId|URI de recurso|
|wait_category_s|Categoria da espera.|
|is_parameterizable_s|É a consulta parametrizável.|
|statement_type_s|Tipo da instrução.|
|statement_key_hash_s|Hash de chave de instrução.|
|exec_type_d|Tipo de execução|
|total_query_wait_time_ms_d|Tempo total de espera da consulta na categoria de espera específica.|
|max_query_wait_time_ms_d|Tempo máximo de espera da consulta em execução individual na categoria de espera específica|
|query_param_type_d|0|
|query_hash_s|Hash de consulta no Repositório de consultas.|
|query_plan_hash_s|Hash de plano de consulta no Repositório de consultas|
|statement_sql_handle_s|Identificador de instrução no Repositório de consultas|
|interval_start_time_d|Inicie o datetimeoffset do intervalo em número de tiques de 1900-1-1.|
|interval_end_time_d|Encerre o datetimeoffset do intervalo em número de tiques de 1900-1-1.|
|count_executions_d|Contagem de execuções da consulta|
|query_id_d|ID da consulta no Repositório de consultas|
|plan_id_d|ID do plano no Repositório de consultas|

[Clique aqui para saber mais sobre dados de estatísticas de espera no Repositório de consultas.](https://docs.microsoft.com/en-us/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql)

### <a name="errors-dataset"></a>Conjunto de dados de erros

|Propriedade|Descrição|
|---|---|
|TenantId|Sua id de locatário.|
|SourceSystem|Sempre: Azure|
|TimeGenerated [UTC]|Carimbo de data/hora de quando o log foi gravado.|
|Tipo|Sempre: AzureDiagnostics|
|ResourceProvider|Nome do provedor de recursos. Sempre: MICROSOFT.SQL|
|Categoria|Nome da categoria. Sempre: QueryStoreRuntimeStatistics|
|OperationName|Nome da operação. Sempre: QueryStoreRuntimeStatisticsEvent|
|Recurso|Nome do recurso|
|ResourceType|Nome do tipo de recurso. Sempre: SERVIDORES/BANCOS DE DADOS|
|SubscriptionId|GUID de assinatura ao qual o banco de dados pertence.|
|ResourceGroup|Nome do grupo de recursos ao qual o banco de dados pertence.|
|LogicalServerName_s|Nome do servidor ao qual o banco de dados pertence.|
|ElasticPoolName_s|Nome do pool elástico ao qual o banco de dados pertence, se houver.|
|DatabaseName_s|Nome do banco de dados|
|ResourceId|URI de recurso|
|Mensagem|Mensagem de erro em texto sem formatação|
|user_defined_b|É o bit de erro definido pelo usuário|
|error_number_d|Código do erro|
|Severity|Gravidade do erro|
|state_d|Estado do erro|
|query_hash_s|Hash de consulta da consulta com falha, se disponível|
|query_plan_hash_s|Hash do plano de consulta da consulta com falha, se disponível|

[Mensagens de erro do SQL Server](https://msdn.microsoft.com/en-us/library/cc645603.aspx)

### <a name="database-waits-dataset"></a>O banco de dados aguarda o conjunto de dados

|Propriedade|Descrição|
|---|---|
|TenantId|Sua id de locatário.|
|SourceSystem|Sempre: Azure|
|TimeGenerated [UTC]|Carimbo de data/hora de quando o log foi gravado.|
|Tipo|Sempre: AzureDiagnostics|
|ResourceProvider|Nome do provedor de recursos. Sempre: MICROSOFT.SQL|
|Categoria|Nome da categoria. Sempre: QueryStoreRuntimeStatistics|
|OperationName|Nome da operação. Sempre: QueryStoreRuntimeStatisticsEvent|
|Recurso|Nome do recurso|
|ResourceType|Nome do tipo de recurso. Sempre: SERVIDORES/BANCOS DE DADOS|
|SubscriptionId|GUID de assinatura ao qual o banco de dados pertence.|
|ResourceGroup|Nome do grupo de recursos ao qual o banco de dados pertence.|
|LogicalServerName_s|Nome do servidor ao qual o banco de dados pertence.|
|ElasticPoolName_s|Nome do pool elástico ao qual o banco de dados pertence, se houver.|
|DatabaseName_s|Nome do banco de dados|
|ResourceId|URI de recurso|
|wait_type_s|Nome do tipo de espera|
|start_utc_date_t [UTC]|Hora de início do período de medida.|
|end_utc_date_t [UTC]|Hora de término do período de medida.|
|delta_max_wait_time_ms_d|Tempo máximo de espera por execução|
|delta_signal_wait_time_ms_d|Tempo total de espera do sinal|
|delta_wait_time_ms_d|Tempo total de espera no período|
|delta_waiting_tasks_count_d|Número de tarefas em espera|

[Clique aqui para saber mais sobre estatísticas de espera no banco de dados.](https://docs.microsoft.com/en-us/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql)

### <a name="timeouts-dataset"></a>Conjunto de dados de tempo limite

|Propriedade|Descrição|
|---|---|
|TenantId|Sua id de locatário.|
|SourceSystem|Sempre: Azure|
|TimeGenerated [UTC]|Carimbo de data/hora de quando o log foi gravado.|
|Tipo|Sempre: AzureDiagnostics|
|ResourceProvider|Nome do provedor de recursos. Sempre: MICROSOFT.SQL|
|Categoria|Nome da categoria. Sempre: QueryStoreRuntimeStatistics|
|OperationName|Nome da operação. Sempre: QueryStoreRuntimeStatisticsEvent|
|Recurso|Nome do recurso|
|ResourceType|Nome do tipo de recurso. Sempre: SERVIDORES/BANCOS DE DADOS|
|SubscriptionId|GUID de assinatura ao qual o banco de dados pertence.|
|ResourceGroup|Nome do grupo de recursos ao qual o banco de dados pertence.|
|LogicalServerName_s|Nome do servidor ao qual o banco de dados pertence.|
|ElasticPoolName_s|Nome do pool elástico ao qual o banco de dados pertence, se houver.|
|DatabaseName_s|Nome do banco de dados|
|ResourceId|URI de recurso|
|error_state_d|Código do estado de erro|
|query_hash_s|Hash de consulta, se disponível|
|query_plan_hash_s|Hash do plano de consulta, se disponível|

### <a name="blockings-dataset"></a>Conjunto de dados de boqueios

|Propriedade|Descrição|
|---|---|
|TenantId|Sua id de locatário.|
|SourceSystem|Sempre: Azure|
|TimeGenerated [UTC]|Carimbo de data/hora de quando o log foi gravado.|
|Tipo|Sempre: AzureDiagnostics|
|ResourceProvider|Nome do provedor de recursos. Sempre: MICROSOFT.SQL|
|Categoria|Nome da categoria. Sempre: QueryStoreRuntimeStatistics|
|OperationName|Nome da operação. Sempre: QueryStoreRuntimeStatisticsEvent|
|Recurso|Nome do recurso|
|ResourceType|Nome do tipo de recurso. Sempre: SERVIDORES/BANCOS DE DADOS|
|SubscriptionId|GUID de assinatura ao qual o banco de dados pertence.|
|ResourceGroup|Nome do grupo de recursos ao qual o banco de dados pertence.|
|LogicalServerName_s|Nome do servidor ao qual o banco de dados pertence.|
|ElasticPoolName_s|Nome do pool elástico ao qual o banco de dados pertence, se houver.|
|DatabaseName_s|Nome do banco de dados|
|ResourceId|URI de recurso|
|lock_mode_s|Modo de bloqueio usado pela consulta|
|resource_owner_type_s|Proprietário do bloqueio.|
|blocked_process_filtered_s|XML de relatório de processo bloqueado.|
|duration_d|Duração do bloqueio em milissegundos.|

### <a name="intelligent-insights-dataset"></a>Conjunto de dados do Insights inteligentes
[Clique aqui para saber mais sobre formato de log do Insights inteligentes](sql-database-intelligent-insights-use-diagnostics-log.md)

## <a name="next-steps"></a>Próximas etapas

- Leia os artigos [Visão geral de métricas no Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md) e [Visão geral dos Logs de diagnóstico do Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) para entender não apenas como habilitar o registro em log, mas também as métricas e categorias de log com suporte dos vários serviços do Azure.
- Leia estes artigos para saber mais sobre os hubs de eventos:
   - [O que são Hubs de Eventos do Azure](../event-hubs/event-hubs-what-is-event-hubs.md)?
   - [Introdução aos Hubs de Evento](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
- Consulte [Baixar métricas e logs de diagnósticos do Armazenamento do Azure](../storage/blobs/storage-dotnet-how-to-use-blobs.md#download-blobs)
