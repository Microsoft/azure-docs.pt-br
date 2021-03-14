---
title: Comportamento de exportação de métricas com valores nulos e zero
description: Discussão de valores nulos versus zeros ao exportar métricas e um ponteiro para uma lista de quais métricas não são exportáveis.
services: azure-monitor
ms.topic: reference
ms.date: 07/22/2020
ms.openlocfilehash: 47b98fe46ac1f2a3e2f3f1a8078ad9ca6f867554
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102048838"
---
# <a name="azure-monitor-platform-metrics-exportable-via-diagnostic-settings"></a>Métricas de plataforma do Azure Monitor exportáveis por meio das Configurações de Diagnóstico

O Azure Monitor fornece [métricas de plataforma](../essentials/data-platform-metrics.md) por padrão sem configuração. Ele fornece várias maneiras de interagir com as métricas da plataforma, incluindo a criação de gráficos dessas métricas no portal, acessá-las por meio da API REST ou consultá-las usando o PowerShell ou a CLI. Consulte [Compatibilidade com as métricas](./metrics-supported.md) para obter uma lista completa de métricas de plataforma disponíveis atualmente com o pipeline de métrica consolidado do Azure Monitor. Para consultar e acessar essas métricas, use [2018-01-01 api-version](/rest/api/monitor/metricdefinitions). Outras métricas podem estar disponíveis no portal ou usando as APIs herdadas.

## <a name="metrics-not-exportable-via-diagnostic-settings"></a>Métricas não exportáveis por meio de configurações de diagnóstico

O conteúdo que costumava estar nesse local foi movido para a [lista com suporte de métricas de Azure monitor](./metrics-supported.md#exporting-platform-metrics-to-other-locations).

Há limitações ao exportar métricas por meio de configurações de diagnóstico. Todas as métricas são exportáveis usando a API REST. 

## <a name="exported-zero-vs-null-values"></a>Exportou valores zero vs NULL 

As métricas têm comportamento diferente ao lidar com 0 ou valores nulos.  Algumas métricas relatam 0 quando nenhum dado é obtido, por exemplo, métricas em falhas de http. Outras métricas armazenam nulos quando nenhum dado é obtido, pois pode indicar que o recurso está offline. Você pode ver a diferença ao criar graficamente essas métricas com valores nulos aparecendo como [linhas tracejadas](metrics-troubleshoot.md#chart-shows-dashed-line). 

Quando as métricas de plataforma podem ser exportadas por meio de configurações de diagnóstico, elas correspondem ao comportamento da métrica. Ou seja, eles exportam nulos quando o recurso não envia dados.  Eles exportam ' 0 ' somente quando foram realmente emitidos pelo recurso subjacente. 

Se você excluir um grupo de recursos ou um recurso específico, os dados de métrica dos recursos afetados não serão mais enviados para os destinos de exportação da configuração de diagnóstico. Ou seja, ele não aparece mais em hubs de eventos, contas de armazenamento do Azure e espaços de trabalho do Log Analytics.

### <a name="metrics-that-used-to-export-zero-for-null"></a>Métricas que usaram para exportar zero para nulo

Antes de 1º de junho de 2020, as métricas abaixo **eram usadas para** emitir ' 0 ' s quando não havia dados. Esses zeros podem então ser exportados para sistemas downstream, como Log Analytics e armazenamento do Azure.  Esse comportamento causou alguma confusão entre "0s" reais (emitidos pelo recurso) e ' 0s ' interpretados (nulos) e, portanto, o comportamento foi alterado para corresponder ao da métrica subjacente, conforme mencionado na seção anterior. 

A alteração ocorreu em todas as nuvens públicas e privadas.

A alteração não afetou o comportamento de nenhuma das seguintes experiências: 
   - Logs de recursos de plataforma exportados por meio de configurações de diagnóstico
   - Criação de gráficos de métricas no Metrics Explorer
   - Alertas sobre métricas de plataforma
 
A seguir está uma lista das métricas cujo comportamento mudou. 

| ResourceType                    | Métrica               |  MetricDisplayName  | 
|---------------------------------|----------------------|---------------------|
| Microsoft.ApiManagement/service | UnauthorizedRequests |  Solicitações de Gateway Não Autorizadas (Preterido)  | 
| Microsoft.ApiManagement/service | TotalRequests |  Solicitações Totais de Gateway (Preterido)  | 
| Microsoft.ApiManagement/service | SuccessfulRequests |  Solicitações de Gateway com Êxito (Preterido)  | 
| Microsoft.ApiManagement/service | Requests |  Requests  | 
| Microsoft.ApiManagement/service | OtherRequests |  Outras Solicitações de Gateway (Preterido)  | 
| Microsoft.ApiManagement/service | FailedRequests |  Solicitações de Gateway com Falha (Preterido)  | 
| Microsoft.ApiManagement/service | EventHubTotalFailedEvents |  Eventos do EventHub com Falha  | 
| Microsoft.ApiManagement/service | EventHubTotalEvents |  Eventos Totais do EventHub  | 
| Microsoft.ApiManagement/service | EventHubTotalBytesSent |  Tamanho dos Eventos do EventHub  | 
| Microsoft.ApiManagement/service | EventHubTimedoutEvents |  Eventos do EventHub com Tempo Limite Excedido  | 
| Microsoft.ApiManagement/service | EventHubThrottledEvents |  Eventos do EventHub Restringidos  | 
| Microsoft.ApiManagement/service | EventHubSuccessfulEvents |  Eventos do EventHub Bem-sucedidos  | 
| Microsoft.ApiManagement/service | EventHubRejectedEvents |  Eventos do EventHub Rejeitados  | 
| Microsoft.ApiManagement/service | EventHubDroppedEvents |  Eventos do EventHub Ignorados  | 
| Microsoft.ApiManagement/service | Duration |  Duração total de solicitações de gateway  | 
| Microsoft.ApiManagement/service | BackendDuration |  Duração de Solicitações de Back-end  | 
| Microsoft.DBforMariaDB/servers | storage_used |  Armazenamento usado  | 
| Microsoft.DBforMariaDB/servers | storage_percent |  Porcentagem de armazenamento  | 
| Microsoft.DBforMariaDB/servers | storage_limit |  Limite de armazenamento  | 
| Microsoft.DBforMariaDB/servers | serverlog_storage_usage |  Armazenamento do Log do Servidor usado  | 
| Microsoft.DBforMariaDB/servers | serverlog_storage_percent |  Porcentagem de armazenamento do Log do Servidor  | 
| Microsoft.DBforMariaDB/servers | serverlog_storage_limit |  Limite de armazenamento do Log do Servidor  | 
| Microsoft.DBforMariaDB/servers | seconds_behind_master |  Retardo de replicação em segundos  | 
| Microsoft.DBforMariaDB/servers | network_bytes_ingress |  Entrada na rede  | 
| Microsoft.DBforMariaDB/servers | network_bytes_egress |  Saída da rede  | 
| Microsoft.DBforMariaDB/servers | memory_percent |  Porcentagem de memória  | 
| Microsoft.DBforMariaDB/servers | io_consumption_percent |  Porcentagem de E/S  | 
| Microsoft.DBforMariaDB/servers | cpu_percent |  Porcentagem de CPU  | 
| Microsoft.DBforMariaDB/servers | connections_failed |  Conexões com falha  | 
| Microsoft.DBforMariaDB/servers | backup_storage_used |  Armazenamento de Backup usado  | 
| Microsoft.DBforMariaDB/servers | active_connections |  Conexões ativas  | 
| Microsoft.DBforMySQL/servers | storage_used |  Armazenamento usado  | 
| Microsoft.DBforMySQL/servers | storage_percent |  Porcentagem de armazenamento  | 
| Microsoft.DBforMySQL/servers | storage_limit |  Limite de armazenamento  | 
| Microsoft.DBforMySQL/servers | serverlog_storage_usage |  Armazenamento do Log do Servidor usado  | 
| Microsoft.DBforMySQL/servers | serverlog_storage_percent |  Porcentagem de armazenamento do Log do Servidor  | 
| Microsoft.DBforMySQL/servers | serverlog_storage_limit |  Limite de armazenamento do Log do Servidor  | 
| Microsoft.DBforMySQL/servers | seconds_behind_master |  Retardo de replicação em segundos  | 
| Microsoft.DBforMySQL/servers | network_bytes_ingress |  Entrada na rede  | 
| Microsoft.DBforMySQL/servers | network_bytes_egress |  Saída da rede  | 
| Microsoft.DBforMySQL/servers | memory_percent |  Porcentagem de memória  | 
| Microsoft.DBforMySQL/servers | io_consumption_percent |  Porcentagem de E/S  | 
| Microsoft.DBforMySQL/servers | cpu_percent |  Porcentagem de CPU  | 
| Microsoft.DBforMySQL/servers | connections_failed |  Conexões com falha  | 
| Microsoft.DBforMySQL/servers | backup_storage_used |  Armazenamento de Backup usado  | 
| Microsoft.DBforMySQL/servers | active_connections |  Conexões ativas  | 
| Microsoft.Devices/Account | digitaltwins.telemetry.nodes |  Espaço Reservado para Telemetria do Nó dos Gêmeos Digitais  | 
| Microsoft.Devices/IotHubs | twinQueries.success |  Consultas de gêmeos bem-sucedidas  | 
| Microsoft.Devices/IotHubs | twinQueries.resultSize |  Tamanho do resultado das consultas de gêmeos  | 
| Microsoft.Devices/IotHubs | twinQueries.failure |  Consultas de gêmeos com falhas  | 
| Microsoft.Devices/IotHubs | jobs.queryJobs.success |  Consultas de trabalho bem-sucedidas  | 
| Microsoft.Devices/IotHubs | jobs.queryJobs.failure |  Consultas de trabalho com falhas  | 
| Microsoft.Devices/IotHubs | jobs.listJobs.success |  Chamadas para listar trabalhos bem-sucedidas  | 
| Microsoft.Devices/IotHubs | jobs.listJobs.failure |  Chamadas para listar trabalhos com falhas  | 
| Microsoft.Devices/IotHubs | jobs.failed |  Trabalhos com falha  | 
| Microsoft.Devices/IotHubs | jobs.createTwinUpdateJob.success |  Criações de trabalhos de atualização de gêmeos bem-sucedidas  | 
| Microsoft.Devices/IotHubs | jobs.createTwinUpdateJob.failure |  Criações de trabalhos de atualização de gêmeos com falhas  | 
| Microsoft.Devices/IotHubs | jobs.createDirectMethodJob.success |  Criações de trabalhos de invocação de método bem-sucedidas  | 
| Microsoft.Devices/IotHubs | jobs.createDirectMethodJob.failure |  Criações de trabalhos de invocação de método com falhas  | 
| Microsoft.Devices/IotHubs | jobs.completed |  Trabalhos concluídos  | 
| Microsoft.Devices/IotHubs | jobs.cancelJob.success |  Cancelamentos de trabalho bem-sucedidos  | 
| Microsoft.Devices/IotHubs | jobs.cancelJob.failure |  Cancelamentos de trabalho com falhas  | 
| Microsoft.Devices/IotHubs | EventGridLatency |  Latência da Grade de Eventos (versão prévia)  | 
| Microsoft.Devices/IotHubs | EventGridDeliveries |  Entregas da Grade de Eventos (versão prévia)  | 
| Microsoft.Devices/IotHubs | devices.totalDevices |  Total de dispositivos (preterido)  | 
| Microsoft.Devices/IotHubs | devices.connectedDevices.allProtocol |  Dispositivos conectados (preteridos)   | 
| Microsoft.Devices/IotHubs | deviceDataUsageV2 |  Uso total de dados de dispositivos (versão prévia)  | 
| Microsoft.Devices/IotHubs | deviceDataUsage |  Uso total de dados do dispositivo  | 
| Microsoft.Devices/IotHubs | dailyMessageQuotaUsed |  Número total de mensagens usadas  | 
| Microsoft.Devices/IotHubs | d2c.twin.update.success |  Atualizações de gêmeos dos dispositivos bem-sucedidas  | 
| Microsoft.Devices/IotHubs | d2c.twin.update.size |  Tamanho das atualizações de gêmeos dos dispositivos  | 
| Microsoft.Devices/IotHubs | d2c.twin.update.failure |  Atualizações de gêmeos dos dispositivos com falhas  | 
| Microsoft.Devices/IotHubs | d2c.twin.read.success |  Leituras de gêmeos dos dispositivos bem-sucedidas  | 
| Microsoft.Devices/IotHubs | d2c.twin.read.size |  Tamanho da resposta das leituras de gêmeos dos dispositivos  | 
| Microsoft.Devices/IotHubs | d2c.twin.read.failure |  Leituras de gêmeos dos dispositivos com falhas  | 
| Microsoft.Devices/IotHubs | d2c.telemetry.ingress.success |  Mensagens de telemetria enviadas  | 
| Microsoft.Devices/IotHubs | d2c.telemetry.ingress.sendThrottle |  Número de erros de limitação  | 
| Microsoft.Devices/IotHubs | d2c.telemetry.ingress.allProtocol |  Tentativas de envio de mensagem de telemetria  | 
| Microsoft.Devices/IotHubs | d2c.telemetry.egress.success |  Roteamento: mensagens de telemetria entregues  | 
| Microsoft.Devices/IotHubs | d2c.telemetry.egress.orphaned |  Roteamento: mensagens de telemetria órfãs   | 
| Microsoft.Devices/IotHubs | d2c.telemetry.egress.invalid |  Roteamento: mensagens de telemetria incompatíveis  | 
| Microsoft.Devices/IotHubs | d2c.telemetry.egress.fallback |  Roteamento: mensagens entregues ao fallback  | 
| Microsoft.Devices/IotHubs | d2c.telemetry.egress.dropped |  Roteamento: mensagens de telemetria removidas   | 
| Microsoft.Devices/IotHubs | d2c.endpoints.latency.storage |  Roteamento: latência de mensagem para armazenamento  | 
| Microsoft.Devices/IotHubs | d2c.endpoints.latency.serviceBusTopics |  Roteamento: latência de mensagem para o Tópico do Barramento de Serviço  | 
| Microsoft.Devices/IotHubs | d2c.endpoints.latency.serviceBusQueues |  Roteamento: latência de mensagem para a Fila do Barramento de Serviço  | 
| Microsoft.Devices/IotHubs | d2c.endpoints.latency.eventHubs |  Roteamento: latência de mensagem para o Hub de Eventos  | 
| Microsoft.Devices/IotHubs | d2c.Endpoints.Latency.builtIn.Events |  Roteamento: latência de mensagem para mensagens/eventos  | 
| Microsoft.Devices/IotHubs | d2c.endpoints.egress.storage.bytes |  Roteamento: dados entregues ao armazenamento  | 
| Microsoft.Devices/IotHubs | d2c.endpoints.egress.storage.blobs |  Roteamento: blobs entregues ao armazenamento  | 
| Microsoft.Devices/IotHubs | d2c.endpoints.egress.storage |  Roteamento: mensagens entregues ao armazenamento  | 
| Microsoft.Devices/IotHubs | d2c.endpoints.egress.serviceBusTopics |  Roteamento: mensagens entregues ao Tópico do Barramento de Serviço  | 
| Microsoft.Devices/IotHubs | d2c.endpoints.egress.serviceBusQueues |  Roteamento: mensagens entregues à Fila do Barramento de Serviço  | 
| Microsoft.Devices/IotHubs | d2c.endpoints.egress.eventHubs |  Roteamento: mensagens entregues ao Hub de Eventos  | 
| Microsoft.Devices/IotHubs | d2c.endpoints.egress.builtIn.events |  Roteamento: mensagens entregues a mensagens/eventos  | 
| Microsoft.Devices/IotHubs | configurações |  Métricas de configuração  | 
| Microsoft.Devices/IotHubs | C2DMessagesExpired |  Mensagens C2D expiradas (versão prévia)  | 
| Microsoft.Devices/IotHubs | c2d.twin.update.success |  Atualizações de gêmeos de back-end bem-sucedidas  | 
| Microsoft.Devices/IotHubs | c2d.twin.update.size |  Tamanho das atualizações de gêmeos de back-end  | 
| Microsoft.Devices/IotHubs | c2d.twin.update.failure |  Atualizações de gêmeos de back-end com falhas  | 
| Microsoft.Devices/IotHubs | c2d.twin.read.success |  Leituras de gêmeos de back-end bem-sucedidas  | 
| Microsoft.Devices/IotHubs | c2d.twin.read.size |  Tamanho da resposta das leituras de gêmeos de back-end  | 
| Microsoft.Devices/IotHubs | c2d.twin.read.failure |  Leituras de gêmeos de back-end com falhas  | 
| Microsoft.Devices/IotHubs | c2d.methods.success |  Invocações de método diretas bem-sucedidas  | 
| Microsoft.Devices/IotHubs | c2d.methods.responseSize |  Tamanho da resposta das invocações de método diretas  | 
| Microsoft.Devices/IotHubs | c2d.methods.requestSize |  Tamanho da solicitação das invocações de método diretas  | 
| Microsoft.Devices/IotHubs | c2d.methods.failure |  Invocações de método diretas com falhas  | 
| Microsoft.Devices/IotHubs | c2d.commands.egress.reject.success |  Mensagens C2D rejeitadas  | 
| Microsoft.Devices/IotHubs | c2d.commands.egress.complete.success |  Entregas de mensagem C2D concluídas  | 
| Microsoft.Devices/IotHubs | c2d.commands.egress.abandon.success |  Mensagens C2D abandonadas  | 
| Microsoft.Devices/provisioningServices | RegistrationAttempts |  Tentativas de Registro  | 
| Microsoft.Devices/provisioningServices | DeviceAssignments |  Dispositivos atribuídos  | 
| Microsoft.Devices/provisioningServices | AttestationAttempts |  Tentativas de atestado  | 
| Microsoft.DocumentDB/databaseAccounts | TotalRequestUnits |  Total de Unidades Solicitadas  | 
| Microsoft.DocumentDB/databaseAccounts | TotalRequests |  Total de Solicitações  | 
| Microsoft.DocumentDB/databaseAccounts | MongoRequests |  Solicitações do Mongo  | 
| Microsoft.DocumentDB/databaseAccounts | MongoRequestCharge |  MongoRequestCharge  | 
| Microsoft.EventGrid/domains | PublishSuccessLatencyInMs |  Publicar Latência de Êxito  | 
| Microsoft.EventGrid/domains | PublishSuccessCount |  Eventos publicados  | 
| Microsoft.EventGrid/domains | PublishFailCount |  Publicar Eventos com Falha  | 
| Microsoft.EventGrid/domains | MatchedEventCount |  Eventos correspondentes  | 
| Microsoft.EventGrid/domains | DroppedEventCount |  Eventos removidos  | 
| Microsoft.EventGrid/domains | DeliverySuccessCount |  Eventos entregues  | 
| Microsoft.EventGrid/domains | DeadLetteredCount |  Eventos de mensagens mortas  | 
| Microsoft.EventGrid/eventSubscriptions | MatchedEventCount |  Eventos correspondentes  | 
| Microsoft.EventGrid/eventSubscriptions | DroppedEventCount |  Eventos removidos  | 
| Microsoft.EventGrid/eventSubscriptions | DeliverySuccessCount |  Eventos entregues  | 
| Microsoft.EventGrid/eventSubscriptions | DeadLetteredCount |  Eventos de mensagens mortas  | 
| Microsoft.EventGrid/extensionTopics | UnmatchedEventCount |  Eventos sem correspondência  | 
| Microsoft.EventGrid/extensionTopics | PublishSuccessLatencyInMs |  Publicar Latência de Êxito  | 
| Microsoft.EventGrid/extensionTopics | PublishSuccessCount |  Eventos publicados  | 
| Microsoft.EventGrid/extensionTopics | PublishFailCount |  Publicar Eventos com Falha  | 
| Microsoft.EventGrid/topics | UnmatchedEventCount |  Eventos sem correspondência  | 
| Microsoft.EventGrid/topics | PublishSuccessLatencyInMs |  Publicar Latência de Êxito  | 
| Microsoft.EventGrid/topics | PublishSuccessCount |  Eventos publicados  | 
| Microsoft.EventGrid/topics | PublishFailCount |  Publicar Eventos com Falha  | 
| Microsoft.EventHub/clusters | OutgoingMessages |  Mensagens de saída  | 
| Microsoft.EventHub/clusters | OutgoingBytes |  Bytes de Saída.  | 
| Microsoft.EventHub/clusters | IncomingRequests |  Solicitações de entrada  | 
| Microsoft.EventHub/clusters | IncomingMessages |  Mensagens de entrada  | 
| Microsoft.EventHub/clusters | IncomingBytes |  Bytes de Entrada.  | 
| Microsoft.EventHub/namespaces | SVRBSY |  Erros de Servidor Ocupado (Preterido)  | 
| Microsoft.EventHub/namespaces | SUCCREQ |  Solicitações Bem-sucedidas (Preterido)  | 
| Microsoft.EventHub/namespaces | OUTMSGS |  Mensagens de Saída (obsoleto) (Preterido)  | 
| Microsoft.EventHub/namespaces | OutgoingMessages |  Mensagens de saída  | 
| Microsoft.EventHub/namespaces | OutgoingBytes |  Bytes de Saída.  | 
| Microsoft.EventHub/namespaces | MISCERR |  Outros Erros (Preterido)  | 
| Microsoft.EventHub/namespaces | INTERR |  Erros Internos do Servidor (Preterido)  | 
| Microsoft.EventHub/namespaces | INREQS |  Solicitações de Entrada (Preterido)  | 
| Microsoft.EventHub/namespaces | INMSGS |  Mensagens de entrada (obsoleto) (Preterido)  | 
| Microsoft.EventHub/namespaces | IncomingRequests |  Solicitações de entrada  | 
| Microsoft.EventHub/namespaces | IncomingMessages |  Mensagens de entrada  | 
| Microsoft.EventHub/namespaces | IncomingBytes |  Bytes de Entrada.  | 
| Microsoft.EventHub/namespaces | FAILREQ |  Solicitações com Falha (Preterido)  | 
| Microsoft.EventHub/namespaces | EHOUTMSGS |  Mensagens de Saída (Preterida)  | 
| Microsoft.EventHub/namespaces | EHOUTMBS |  Bytes de saída (obsoleto) (Preterido)  | 
| Microsoft.EventHub/namespaces | EHOUTBYTES |  Bytes de saída (Preterida)  | 
| Microsoft.EventHub/namespaces | EHINMSGS |  Mensagens de Entrada (Preterida)  | 
| Microsoft.EventHub/namespaces | EHINMBS |  Bytes de entrada (obsoleto) (Preterido)  | 
| Microsoft.EventHub/namespaces | EHINBYTES |  Bytes de entrada (Preterida)  | 
| Microsoft.EventHub/namespaces | EHAMSGS |  Arquivar mensagens (Preterido)  | 
| Microsoft.EventHub/namespaces | EHAMBS |  Arquivar taxa de transferência de mensagem (Preterido)  | 
| Microsoft.EventHub/namespaces | EHABL |  Arquivar mensagens da lista de pendências (Preterido)  | 
| Microsoft.HDInsight/clusters | NumActiveWorkers |  Número de Trabalhos Ativos  | 
| Microsoft.HDInsight/clusters | GatewayRequests |  Solicitações de gateway  | 
| Microsoft.HDInsight/clusters | CategorizedGatewayRequests |  Solicitações de gateway categorizadas  | 
| Microsoft.Insights/AutoscaleSettings | ScaleActionsInitiated |  Ações de Escala Iniciadas  | 
| Microsoft.Insights/Components | traces/count |  Rastreamentos  | 
| Microsoft.Insights/Components | performanceCounters/requestsPerSecond |  Taxa de solicitação HTTP  | 
| Microsoft.Insights/Components | performanceCounters/requestsInQueue |  Solicitações HTTP na fila do aplicativo  | 
| Microsoft.Insights/Components | performanceCounters/exceptionsPerSecond |  Taxa de exceção  | 
| Microsoft.Insights/Components | pageViews/count |  Visualizações de página  | 
| Microsoft.Insights/Components | exceptions/count |  Exceções  | 
| Microsoft.Kusto/Clusters | StreamingIngestResults |  Resultado da Ingestão de Streaming  | 
| Microsoft.Kusto/Clusters | StreamingIngestDuration |  Duração da Ingestão de Streaming  | 
| Microsoft.Kusto/Clusters | StreamingIngestDataRate |  Taxa de Dados de Ingestão de Streaming  | 
| Microsoft.Kusto/Clusters | SteamingIngestRequestRate |  Taxa de Solicitação de Ingestão de Streaming  | 
| Microsoft.Kusto/Clusters | QueryDuration |  Duração da consulta  | 
| Microsoft.Kusto/Clusters | KeepAlive |  Keep alive  | 
| Microsoft.Kusto/Clusters | IngestionVolumeInMB |  Volume de ingestão (em MB)  | 
| Microsoft.Kusto/Clusters | IngestionUtilization |  Utilização da ingestão  | 
| Microsoft.Kusto/Clusters | IngestionResult |  Resultados da ingestão  | 
| Microsoft.Kusto/Clusters | IngestionLatencyInSeconds |  Latência de ingestão (em segundos)  | 
| Microsoft.Kusto/Clusters | ExportUtilization |  Utilização da Exportação  | 
| Microsoft.Kusto/Clusters | EventsProcessedForEventHubs |  Eventos processados (para os Hubs IoT/de Eventos)  | 
| Microsoft.Kusto/Clusters | CPU |  CPU  | 
| Microsoft.Kusto/Clusters | ContinuousExportResult |  Resultado da Exportação Contínua  | 
| Microsoft.Kusto/Clusters | ContinuousExportPendingCount |  Contagem de Exportação Contínua Pendente  | 
| Microsoft.Kusto/Clusters | ContinuousExportNumOfRecordsExported |  Exportação Contínua – número de registros exportados  | 
| Microsoft.Kusto/Clusters | ContinuousExportMaxLatenessMinutes |  Minutos de Atraso Máximo da Exportação Contínua  | 
| Microsoft.Kusto/Clusters | CacheUtilization |  Utilização do cache  | 
| Microsoft.Logic/integrationServiceEnvironments | TriggerThrottledEvents |  Eventos de restrição do gatilho  | 
| Microsoft.Logic/integrationServiceEnvironments | TriggersSucceeded |  Gatilhos bem sucedidos   | 
| Microsoft.Logic/integrationServiceEnvironments | TriggersStarted |  Gatilhos iniciados   | 
| Microsoft.Logic/integrationServiceEnvironments | TriggersSkipped |  Gatilhos ignorados  | 
| Microsoft.Logic/integrationServiceEnvironments | TriggersFired |  Gatilhos acionados   | 
| Microsoft.Logic/integrationServiceEnvironments | TriggersFailed |  Gatilhos com falha   | 
| Microsoft.Logic/integrationServiceEnvironments | TriggersCompleted |  Gatilhos concluídos   | 
| Microsoft.Logic/integrationServiceEnvironments | RunThrottledEvents |  Eventos de restrição de execução  | 
| Microsoft.Logic/integrationServiceEnvironments | RunStartThrottledEvents |  Executar Eventos Limitados por Início  | 
| Microsoft.Logic/integrationServiceEnvironments | RunsSucceeded |  Execuções bem sucedidas  | 
| Microsoft.Logic/integrationServiceEnvironments | RunsStarted |  Execuções iniciadas  | 
| Microsoft.Logic/integrationServiceEnvironments | RunsFailed |  Execuções com falha  | 
| Microsoft.Logic/integrationServiceEnvironments | RunsCompleted |  Execuções concluídas  | 
| Microsoft.Logic/integrationServiceEnvironments | RunsCancelled |  Execuções Canceladas  | 
| Microsoft.Logic/integrationServiceEnvironments | RunFailurePercentage |  Porcentagem de falha de execução  | 
| Microsoft.Logic/integrationServiceEnvironments | ActionThrottledEvents |  Eventos de restrição de ações  | 
| Microsoft.Logic/integrationServiceEnvironments | ActionsSucceeded |  Ações bem sucedidas   | 
| Microsoft.Logic/integrationServiceEnvironments | ActionsStarted |  Ações iniciadas   | 
| Microsoft.Logic/integrationServiceEnvironments | ActionsSkipped |  Ações ignoradas   | 
| Microsoft.Logic/integrationServiceEnvironments | ActionsFailed |  Ações com falha   | 
| Microsoft.Logic/integrationServiceEnvironments | ActionsCompleted |  Ações concluídas   | 
| Microsoft.Logic/workflows | TriggerThrottledEvents |  Eventos de restrição do gatilho  | 
| Microsoft.Logic/workflows | TriggersSucceeded |  Gatilhos bem sucedidos   | 
| Microsoft.Logic/workflows | TriggersStarted |  Gatilhos iniciados   | 
| Microsoft.Logic/workflows | TriggersSkipped |  Gatilhos ignorados  | 
| Microsoft.Logic/workflows | TriggersFired |  Gatilhos acionados   | 
| Microsoft.Logic/workflows | TriggersFailed |  Gatilhos com falha   | 
| Microsoft.Logic/workflows | TriggersCompleted |  Gatilhos concluídos   | 
| Microsoft.Logic/workflows | TotalBillableExecutions |  Total de execuções faturáveis  | 
| Microsoft.Logic/workflows | RunThrottledEvents |  Eventos de restrição de execução  | 
| Microsoft.Logic/workflows | RunStartThrottledEvents |  Executar Eventos Limitados por Início  | 
| Microsoft.Logic/workflows | RunsSucceeded |  Execuções bem sucedidas  | 
| Microsoft.Logic/workflows | RunsStarted |  Execuções iniciadas  | 
| Microsoft.Logic/workflows | RunsFailed |  Execuções com falha  | 
| Microsoft.Logic/workflows | RunsCompleted |  Execuções concluídas  | 
| Microsoft.Logic/workflows | RunsCancelled |  Execuções Canceladas  | 
| Microsoft.Logic/workflows | RunFailurePercentage |  Porcentagem de falha de execução  | 
| Microsoft.Logic/workflows | BillingUsageStorageConsumption |  Uso de Cobrança para Execuções de Consumo de Armazenamento  | 
| Microsoft.Logic/workflows | BillingUsageStorageConsumption |  Uso de Cobrança para Execuções de Consumo de Armazenamento  | 
| Microsoft.Logic/workflows | BillingUsageStandardConnector |  Uso de Cobrança para Execuções de Conector Padrão  | 
| Microsoft.Logic/workflows | BillingUsageStandardConnector |  Uso de Cobrança para Execuções de Conector Padrão  | 
| Microsoft.Logic/workflows | BillingUsageNativeOperation |  Uso de Cobrança para Execuções de Operação Nativa  | 
| Microsoft.Logic/workflows | BillingUsageNativeOperation |  Uso de Cobrança para Execuções de Operação Nativa  | 
| Microsoft.Logic/workflows | BillableTriggerExecutions |  Execuções de gatilho faturáveis  | 
| Microsoft.Logic/workflows | BillableActionExecutions |  Execuções de ação faturáveis  | 
| Microsoft.Logic/workflows | ActionThrottledEvents |  Eventos de restrição de ações  | 
| Microsoft.Logic/workflows | ActionsSucceeded |  Ações bem sucedidas   | 
| Microsoft.Logic/workflows | ActionsStarted |  Ações iniciadas   | 
| Microsoft.Logic/workflows | ActionsSkipped |  Ações ignoradas   | 
| Microsoft.Logic/workflows | ActionsFailed |  Ações com falha   | 
| Microsoft.Logic/workflows | ActionsCompleted |  Ações concluídas   | 
| Microsoft.Network/frontdoors | WebApplicationFirewallRequestCount |  Contagem de solicitações do Firewall de Aplicativo Web  | 
| Microsoft.Network/frontdoors | TotalLatency |  Latência total  | 
| Microsoft.Network/frontdoors | ResponseSize |  Tamanho da resposta  | 
| Microsoft.Network/frontdoors | RequestSize |  Tamanho da solicitação  | 
| Microsoft.Network/frontdoors | RequestCount |  Contagem de solicitações  | 
| Microsoft.Network/frontdoors | BillableResponseSize |  Tamanho de Resposta Faturável  | 
| Microsoft.Network/frontdoors | BackendRequestLatency |  Latência de solicitação de back-end  | 
| Microsoft.Network/frontdoors | BackendRequestCount |  Contagem de solicitações de back-end  | 
| Microsoft.Network/frontdoors | BackendHealthPercentage |  Percentual de integridade do back-end  | 
| Microsoft.Network/trafficManagerProfiles | QpsByEndpoint |  Consultas pelo Ponto de Extremidade Retornado  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | scheduled.pending |  Notificações agendadas pendentes  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | registration.update |  Operação de atualização de registro  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | registration.get |  Operações de leitura do registro  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | registration.delete |  Operação de exclusão de registro  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | registration.create |  Operação de criação de registro  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | registration.all |  Operações de Registro  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.wrongtoken |  Erros de autorização do WNS (token incorreto)  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.tokenproviderunreachable |  Erros de autorização do WNS (inacessível)  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.throttled |  Notificações restritas do WNS  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.success |  Notificações do WNS bem-sucedidas  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.pnserror |  Erros do WNS  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.invalidtoken |  Erros de autorização do WNS (token inválido)  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.invalidnotificationsize |  Erro de tamanho de notificação inválido do WNS  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.invalidnotificationformat |  Formato de notificação inválido do WNS  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.invalidcredentials |  Erros de autorização do WNS (credenciais inválidas)  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.expiredchannel |  Erro de canal expirado do WNS  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.dropped |  Notificações do WNS descartadas  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.channelthrottled |  Canal do WNS restrito  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.channeldisconnected |  Canal do WNS desconectado  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.badchannel |  Erro de canal inválido do WNS  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.authenticationerror |  Erros de autenticação do WNS  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.throttled |  Notificações restritas do MPNS  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.success |  Notificações de sucesso do MPNS  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.pnserror |  Erros do MPNS  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.invalidnotificationformat |  Erro de tamanho de notificação inválido do MPNS  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.invalidcredentials |  Credenciais inválidas do MPNS  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.dropped |  Notificações descartadas do MPNS  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.channeldisconnected |  Canal do MPNS desconectado  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.badchannel |  Erro de canal inválido do MPNS  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.authenticationerror |  Erros de autenticação do MPNS  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.wrongchannel |  Erro de canal incorreto do GCM  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.throttled |  Notificações restritas do GCM  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.success |  Notificações do GCM bem-sucedidas  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.pnserror |  Erros do GCM  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.invalidnotificationsize |  Erro de tamanho de notificação inválido do GCM  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.invalidnotificationformat |  Formato de notificação inválido do GCM  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.invalidcredentials |  Erros de autorização do GCM (credenciais inválidas)  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.expiredchannel |  Erro de canal expirado do GCM  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.badchannel |  Erro de canal inválido do GCM  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.authenticationerror |  Erros de autenticação do GCM  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.success |  Notificações do APNS bem-sucedidas  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.pnserror |  Erros do APNS  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.invalidnotificationsize |  Erro de tamanho de notificação inválido do APNS  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.invalidcredentials |  Erros de autorização do APNS  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.expiredchannel |  Erro de canal expirado do APNS  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.badchannel |  Erro de canal inválido do APNS  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.allpns.success |  Notificações de sucesso  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.allpns.pnserror |  Erros de sistema de notificação externa  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.allpns.invalidpayload |  Erros de conteúdo  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.allpns.channelerror |  Erros de canal  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | notificationhub.pushes |  Todas as notificações de saída  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | installation.upsert |  Criar ou atualizar operações de instalação  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | installation.patch |  Operações de instalação de patch  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | installation.get |  Obter operações de instalação  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | installation.delete |  Excluir operações de instalação  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | installation.all |  Operações de gerenciamento de instalação  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | incoming.scheduled.cancel |  Notificações por push agendadas canceladas  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | incoming.scheduled |  Notificações por push agendadas enviadas  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | incoming.all.requests |  Todas as solicitações recebidas  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | incoming.all.failedrequests |  Todas as solicitações com falha recebidas  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | incoming |  Mensagens de entrada  | 
| Microsoft.OperationalInsights/workspaces | Pulsação |  Pulsação  | 
| Microsoft.Relay/namespaces | BytesTransferred |  BytesTransferred  | 
| Microsoft.ServiceBus/namespaces | OutgoingMessages |  Mensagens de saída  | 
| Microsoft.ServiceBus/namespaces | IncomingRequests |  Solicitações de entrada  | 
| Microsoft.ServiceBus/namespaces | IncomingMessages |  Mensagens de entrada  | 
| Microsoft.SignalRService/SignalR | UserErrors |  Erros do usuário  | 
| Microsoft.SignalRService/SignalR | SystemErrors |  Erros do sistema  | 
| Microsoft.SignalRService/SignalR | OutboundTraffic |  Tráfego de saída  | 
| Microsoft.SignalRService/SignalR | MessageCount |  Contagem de mensagens  | 
| Microsoft.SignalRService/SignalR | InboundTraffic |  Tráfego de entrada  | 
| Microsoft.SignalRService/SignalR | ConnectionCount |  Contagem de conexão  | 
| Microsoft.Sql/managedInstances | avg_cpu_percent |  Percentual médio de CPU  | 
| Microsoft.Sql/servers | dtu_used |  DTU usado  | 
| Microsoft.Sql/servers | dtu_consumption_percent |  Porcentagem de DTU  | 
| Microsoft.Sql/servers/databases | xtp_storage_percent |  Percentual de armazenamento do OLTP na memória  | 
| Microsoft.Sql/servers/databases | workers_percent |  Porcentagem de funcionários  | 
| Microsoft.Sql/servers/databases | sessions_percent |  Porcentagem de sessões  | 
| Microsoft.Sql/servers/databases | physical_data_read_percent |  Porcentagem de E/S de dados  | 
| Microsoft.Sql/servers/databases | log_write_percent |  Porcentagem de E/S de log  | 
| Microsoft.Sql/servers/databases | dwu_used |  DWU usado  | 
| Microsoft.Sql/servers/databases | dwu_consumption_percent |  Porcentagem de DWU  | 
| Microsoft.Sql/servers/databases | dtu_used |  DTU usado  | 
| Microsoft.Sql/servers/databases | dtu_consumption_percent |  Porcentagem de DTU  | 
| Microsoft.Sql/servers/databases | deadlock |  Deadlocks  | 
| Microsoft.Sql/servers/databases | cpu_used |  CPU usada  | 
| Microsoft.Sql/servers/databases | cpu_percent |  Percentual de CPU  | 
| Microsoft.Sql/servers/databases | connection_successful |  Conexões bem sucedidas  | 
| Microsoft.Sql/servers/databases | connection_failed |  Conexões com falha  | 
| Microsoft.Sql/servers/databases | cache_hit_percent |  Percentual de ocorrência no cache  | 
| Microsoft.Sql/servers/databases | blocked_by_firewall |  Bloqueado pelo firewall  | 
| Microsoft.Sql/servers/elasticPools | xtp_storage_percent |  Percentual de armazenamento do OLTP na memória  | 
| Microsoft.Sql/servers/elasticPools | workers_percent |  Porcentagem de funcionários  | 
| Microsoft.Sql/servers/elasticPools | sessions_percent |  Porcentagem de sessões  | 
| Microsoft.Sql/servers/elasticPools | physical_data_read_percent |  Porcentagem de E/S de dados  | 
| Microsoft.Sql/servers/elasticPools | log_write_percent |  Porcentagem de E/S de log  | 
| Microsoft.Sql/servers/elasticPools | eDTU_used |  eDTU usado  | 
| Microsoft.Sql/servers/elasticPools | dtu_consumption_percent |  Porcentagem de DTU  | 
| Microsoft.Sql/servers/elasticPools | cpu_percent |  Percentual de CPU  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | TotalFrontEnds |  Total de front-ends  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | SmallAppServicePlanInstances |  Funções de trabalho pequenas do Plano do Serviço de Aplicativo  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | Requests |  Requests  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | MemoryPercentage |  Porcentagem de Memória  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | MediumAppServicePlanInstances |  Funções de trabalho médias do Plano do Serviço de Aplicativo  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | LargeAppServicePlanInstances |  Funções de trabalho grandes do Plano do Serviço de Aplicativo  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | HttpQueueLength |  Tamanho da Fila de Http  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | Http5xx |  Erros do Servidor Http  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | Http4xx |  Http 4xx  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | Http406 |  Http 406  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | Http404 |  Http 404  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | Http403 |  Http 403  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | Http401 |  Http 401  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | Http3xx |  Http 3xx  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | Http2xx |  Http 2xx  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | Http101 |  Http 101  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | DiskQueueLength |  Tamanho da fila do disco  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | CpuPercentage |  Percentual de CPU  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | BytesSent |  Saída de dados  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | BytesReceived |  Entrada de Dados  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | AverageResponseTime |  Tempo Médio de Resposta  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | ActiveRequests |  Solicitações ativas  | 
| Microsoft.Web/hostingEnvironments/workerPools | WorkersUsed |  Funções de trabalho usadas  | 
| Microsoft.Web/hostingEnvironments/workerPools | WorkersTotal |  Total de funções de trabalho  | 
| Microsoft.Web/hostingEnvironments/workerPools | WorkersAvailable |  Funções de trabalho disponíveis  | 
| Microsoft.Web/hostingEnvironments/workerPools | MemoryPercentage |  Porcentagem de Memória  | 
| Microsoft.Web/hostingEnvironments/workerPools | CpuPercentage |  Percentual de CPU  | 
| Microsoft.Web/serverfarms | TcpTimeWait |  Espera de Tempo de TCP  | 
| Microsoft.Web/serverfarms | TcpSynSent |  Sincronização de TCP Enviada  | 
| Microsoft.Web/serverfarms | TcpSynReceived |  Sincronização de TCP Recebida  | 
| Microsoft.Web/serverfarms | TcpLastAck |  Última Confirmação de TCP  | 
| Microsoft.Web/serverfarms | TcpFinWait2 |  Espera 2 de Fin de TCP  | 
| Microsoft.Web/serverfarms | TcpFinWait1 |  Espera 1 de Fin de TCP  | 
| Microsoft.Web/serverfarms | TcpEstablished |  TCP Estabelecido  | 
| Microsoft.Web/serverfarms | TcpClosing |  Fechamento de TCP  | 
| Microsoft.Web/serverfarms | TcpCloseWait |  Espera de Fechamento de TCP  | 
| Microsoft.Web/serverfarms | MemoryPercentage |  Porcentagem de Memória  | 
| Microsoft.Web/serverfarms | HttpQueueLength |  Tamanho da Fila de Http  | 
| Microsoft.Web/serverfarms | DiskQueueLength |  Tamanho da fila do disco  | 
| Microsoft.Web/serverfarms | CpuPercentage |  Percentual de CPU  | 
| Microsoft.Web/serverfarms | BytesSent |  Saída de dados  | 
| Microsoft.Web/serverfarms | BytesReceived |  Entrada de Dados  | 
| Microsoft.Web/sites | TotalAppDomainsUnloaded |  Total de domínios de aplicativo descarregados  | 
| Microsoft.Web/sites | TotalAppDomains |  Total de domínios de aplicativo  | 
| Microsoft.Web/sites | Threads |  Contagem de Threads  | 
| Microsoft.Web/sites | RequestsInApplicationQueue |  Solicitações na fila do aplicativo  | 
| Microsoft.Web/sites | Requests |  Requests  | 
| Microsoft.Web/sites | PrivateBytes |  Bytes Particulares  | 
| Microsoft.Web/sites | MemoryWorkingSet |  Conjunto de trabalho de memória  | 
| Microsoft.Web/sites | IoWriteOperationsPerSecond |  E/S de operações de gravação por segundo  | 
| Microsoft.Web/sites | IoWriteBytesPerSecond |  E/S de bytes de gravação por segundo  | 
| Microsoft.Web/sites | IoReadOperationsPerSecond |  E/S de operações de leitura por segundo  | 
| Microsoft.Web/sites | IoReadBytesPerSecond |  E/S de bytes de leitura por segundo  | 
| Microsoft.Web/sites | IoOtherOperationsPerSecond |  E/S de outras operações por segundo  | 
| Microsoft.Web/sites | IoOtherBytesPerSecond |  E/S de outros bytes por segundo  | 
| Microsoft.Web/sites | HttpResponseTime |  Tempo de Resposta  | 
| Microsoft.Web/sites | Http5xx |  Erros do Servidor Http  | 
| Microsoft.Web/sites | Http4xx |  Http 4xx  | 
| Microsoft.Web/sites | Http406 |  Http 406  | 
| Microsoft.Web/sites | Http404 |  Http 404  | 
| Microsoft.Web/sites | Http403 |  Http 403  | 
| Microsoft.Web/sites | Http401 |  Http 401  | 
| Microsoft.Web/sites | Http3xx |  Http 3xx  | 
| Microsoft.Web/sites | Http2xx |  Http 2xx  | 
| Microsoft.Web/sites | Http101 |  Http 101  | 
| Microsoft.Web/sites | HealthCheckStatus |  Status da verificação de integridade  | 
| Microsoft.Web/sites | Alças |  Núm. de Identificadores  | 
| Microsoft.Web/sites | Gen2Collections |  Coletas de lixo da Ger 2  | 
| Microsoft.Web/sites | Gen1Collections |  Coletas de lixo da Ger 1  | 
| Microsoft.Web/sites | Gen0Collections |  Coletas de lixo da Ger 0  | 
| Microsoft.Web/sites | FunctionExecutionUnits |  Unidades de Execução de Função  | 
| Microsoft.Web/sites | FunctionExecutionCount |  Contagem de Execução de Função  | 
| Microsoft.Web/sites | CurrentAssemblies |  Assemblies Atuais  | 
| Microsoft.Web/sites | CpuTime |  Tempo de CPU  | 
| Microsoft.Web/sites | BytesSent |  Saída de dados  | 
| Microsoft.Web/sites | BytesReceived |  Entrada de Dados  | 
| Microsoft.Web/sites | AverageResponseTime |  Tempo Médio de Resposta  | 
| Microsoft.Web/sites | AverageMemoryWorkingSet |  Conjunto de trabalho de memória média  | 
| Microsoft.Web/sites | AppConnections |  conexões  | 
| Microsoft.Web/sites/slots | TotalAppDomainsUnloaded |  Total de domínios de aplicativo descarregados  | 
| Microsoft.Web/sites/slots | TotalAppDomains |  Total de domínios de aplicativo  | 
| Microsoft.Web/sites/slots | Threads |  Contagem de Threads  | 
| Microsoft.Web/sites/slots | RequestsInApplicationQueue |  Solicitações na fila do aplicativo  | 
| Microsoft.Web/sites/slots | Requests |  Requests  | 
| Microsoft.Web/sites/slots | PrivateBytes |  Bytes Particulares  | 
| Microsoft.Web/sites/slots | MemoryWorkingSet |  Conjunto de trabalho de memória  | 
| Microsoft.Web/sites/slots | IoWriteOperationsPerSecond |  E/S de operações de gravação por segundo  | 
| Microsoft.Web/sites/slots | IoWriteBytesPerSecond |  E/S de bytes de gravação por segundo  | 
| Microsoft.Web/sites/slots | IoReadOperationsPerSecond |  E/S de operações de leitura por segundo  | 
| Microsoft.Web/sites/slots | IoReadBytesPerSecond |  E/S de bytes de leitura por segundo  | 
| Microsoft.Web/sites/slots | IoOtherOperationsPerSecond |  E/S de outras operações por segundo  | 
| Microsoft.Web/sites/slots | IoOtherBytesPerSecond |  E/S de outros bytes por segundo  | 
| Microsoft.Web/sites/slots | HttpResponseTime |  Tempo de Resposta  | 
| Microsoft.Web/sites/slots | Http5xx |  Erros do Servidor Http  | 
| Microsoft.Web/sites/slots | Http4xx |  Http 4xx  | 
| Microsoft.Web/sites/slots | Http406 |  Http 406  | 
| Microsoft.Web/sites/slots | Http404 |  Http 404  | 
| Microsoft.Web/sites/slots | Http403 |  Http 403  | 
| Microsoft.Web/sites/slots | Http401 |  Http 401  | 
| Microsoft.Web/sites/slots | Http3xx |  Http 3xx  | 
| Microsoft.Web/sites/slots | Http2xx |  Http 2xx  | 
| Microsoft.Web/sites/slots | Http101 |  Http 101  | 
| Microsoft.Web/sites/slots | HealthCheckStatus |  Status da verificação de integridade  | 
| Microsoft.Web/sites/slots | Alças |  Núm. de Identificadores  | 
| Microsoft.Web/sites/slots | Gen2Collections |  Coletas de lixo da Ger 2  | 
| Microsoft.Web/sites/slots | Gen1Collections |  Coletas de lixo da Ger 1  | 
| Microsoft.Web/sites/slots | Gen0Collections |  Coletas de lixo da Ger 0  | 
| Microsoft.Web/sites/slots | FunctionExecutionUnits |  Unidades de Execução de Função  | 
| Microsoft.Web/sites/slots | FunctionExecutionCount |  Contagem de Execução de Função  | 
| Microsoft.Web/sites/slots | CurrentAssemblies |  Assemblies Atuais  | 
| Microsoft.Web/sites/slots | CpuTime |  Tempo de CPU  | 
| Microsoft.Web/sites/slots | BytesSent |  Saída de dados  | 
| Microsoft.Web/sites/slots | BytesReceived |  Entrada de Dados  | 
| Microsoft.Web/sites/slots | AverageResponseTime |  Tempo Médio de Resposta  | 
| Microsoft.Web/sites/slots | AverageMemoryWorkingSet |  Conjunto de trabalho de memória média  | 
| Microsoft.Web/sites/slots | AppConnections |  conexões  | 
| Microsoft.Sql/servers/databases | cpu_percent | Percentual de CPU | 
| Microsoft.Sql/servers/databases | physical_data_read_percent | Porcentagem de E/S de dados | 
| Microsoft.Sql/servers/databases | log_write_percent | Porcentagem de E/S de log | 
| Microsoft.Sql/servers/databases | dtu_consumption_percent | Porcentagem de DTU | 
| Microsoft.Sql/servers/databases | connection_successful | Conexões bem sucedidas | 
| Microsoft.Sql/servers/databases | connection_failed | Conexões com falha | 
| Microsoft.Sql/servers/databases | blocked_by_firewall | Bloqueado pelo firewall | 
| Microsoft.Sql/servers/databases | deadlock | Deadlocks | 
| Microsoft.Sql/servers/databases | xtp_storage_percent | Percentual de armazenamento do OLTP na memória | 
| Microsoft.Sql/servers/databases | workers_percent | Porcentagem de funcionários | 
| Microsoft.Sql/servers/databases | sessions_percent | Porcentagem de sessões | 
| Microsoft.Sql/servers/databases | dtu_used | DTU usado | 
| Microsoft.Sql/servers/databases | cpu_used | CPU usada | 
| Microsoft.Sql/servers/databases | dwu_consumption_percent | Porcentagem de DWU | 
| Microsoft.Sql/servers/databases | dwu_used | DWU usado | 
| Microsoft.Sql/servers/databases | cache_hit_percent | Percentual de ocorrência no cache | 
| Microsoft.Sql/servers/databases | wlg_allocation_relative_to_system_percent | Percentual de alocação do grupo de carga de trabalho por sistema | 
| Microsoft.Sql/servers/databases | wlg_allocation_relative_to_wlg_effective_cap_percent | Alocação do grupo de carga de trabalho por porcentagem máxima de recursos | 
| Microsoft.Sql/servers/databases | wlg_active_queries | Consultas ativas do grupo de carga de trabalho | 
| Microsoft.Sql/servers/databases | wlg_queued_queries | Consultas na fila do grupo de carga de trabalho | 
| Microsoft.Sql/servers/databases | active_queries | Consultas ativas | 
| Microsoft.Sql/servers/databases | queued_queries | Consultas em fila | 
| Microsoft.Sql/servers/databases | wlg_active_queries_timeouts | Tempos limite de consulta do grupo de carga de trabalho | 
| Microsoft.Sql/servers/databases | wlg_queued_queries_timeouts | Tempos limite de consulta em fila do grupo de cargas de trabalho | 
| Microsoft.Sql/servers/databases | wlg_effective_min_resource_percent | Percentual mínimo efetivo de recursos | 
| Microsoft.Sql/servers/databases | wlg_effective_cap_resource_percent | Percentual de limite efetivo de recursos | 
| Microsoft.Sql/servers/elasticPools | cpu_percent | Percentual de CPU | 
| Microsoft.Sql/servers/elasticPools | physical_data_read_percent | Porcentagem de E/S de dados | 
| Microsoft.Sql/servers/elasticPools | log_write_percent | Porcentagem de E/S de log | 
| Microsoft.Sql/servers/elasticPools | dtu_consumption_percent | Porcentagem de DTU | 
| Microsoft.Sql/servers/elasticPools | workers_percent | Porcentagem de funcionários | 
| Microsoft.Sql/servers/elasticPools | sessions_percent | Porcentagem de sessões | 
| Microsoft.Sql/servers/elasticPools | eDTU_used | eDTU usado | 
| Microsoft.Sql/servers/elasticPools | xtp_storage_percent | Percentual de armazenamento do OLTP na memória | 
| Microsoft.Sql/servers | dtu_consumption_percent | Porcentagem de DTU | 
| Microsoft.Sql/servers | dtu_used | DTU usado | 
| Microsoft.Sql/managedInstances | avg_cpu_percent | Percentual médio de CPU |