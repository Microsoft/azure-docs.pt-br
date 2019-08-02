---
title: Serviços e esquemas com suporte para os logs de diagnóstico do Azure
description: Compreenda o esquema de serviços e eventos com suporte para Logs de Diagnóstico do Azure.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 10/11/2018
ms.author: robb
ms.subservice: logs
ms.openlocfilehash: 468bcdb6aa688157196bb9cba8added623a857d3
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/17/2019
ms.locfileid: "67155299"
---
# <a name="supported-services-schemas-and-categories-for-azure-diagnostic-logs"></a>Serviços, esquemas e categorias com suporte para os logs de diagnóstico do Azure

[Os logs de diagnóstico do Monitor do Azure](../../azure-monitor/platform/diagnostic-logs-overview.md) são registros emitidos pelos serviços do Azure que descrevem a operação desses serviços ou recursos. Todos os logs de diagnóstico disponíveis por meio do Azure Monitor compartilham um esquema comum de nível superior, com flexibilidade para cada serviço emitir propriedades exclusivas para seus próprios eventos.

Uma combinação do tipo de recurso (disponível na propriedade `resourceId`) e da `category` identifica exclusivamente um esquema. Este artigo descreve o esquema de nível superior para os logs de diagnóstico e os vínculos para o esquema de cada serviço.

## <a name="top-level-diagnostic-logs-schema"></a>Esquema de logs de diagnóstico de nível superior

| NOME | Obrigatório/Opcional | DESCRIÇÃO |
|---|---|---|
| tempo real | Obrigatório | O carimbo de data/hora (UTC) do evento. |
| ResourceId | Obrigatório | A ID do recurso que emitiu o evento. Para serviços de locatário, isso é o /tenants/tenant-id/providers/provider-name do formulário. |
| tenantId | Necessário para os logs de locatário | A ID de locatário do que esse evento está vinculado ao locatário do Active Directory. Essa propriedade só é usada para logs de nível de locatário, ele não aparece nos logs de nível do recurso. |
| operationName | Obrigatório | O nome da operação representada por esse evento. Se o evento representa uma operação RBAC, esse é o nome da operação RBAC (por exemplo, Microsoft.Storage/storageAccounts/blobServices/blobs/Read). Normalmente modeladas na forma de uma operação do Resource Manager, mesmo que não sejam as operações do Resource Manager documentadas reais (`Microsoft.<providerName>/<resourceType>/<subtype>/<Write/Read/Delete/Action>`) |
| operationVersion | Opcional | A api-version associada à operação, se a operationName foi executada usando uma API (por exemplo, `http://myservice.windowsazure.net/object?api-version=2016-06-01`). Se não houver nenhuma API que corresponde a essa operação, a versão representará a versão dessa operação, caso as propriedades associadas à operação sejam alteradas no futuro. |
| categoria | Obrigatório | A categoria de log do evento. Categoria é a granularidade na qual você pode habilitar ou desabilitar os logs em determinado recurso. As propriedades exibidas no blob de propriedades de um evento são as mesmas em uma categoria de log e um tipo de recurso específicos. As categorias de log típicas são “Auditoria”, “Operacional”, “Execução” e “Solicitação”. |
| resultType | Opcional | O status do evento. Os valores típicos incluem Iniciado, Em Andamento, Com Êxito, Com Falha, Ativo e Resolvido. |
| resultSignature | Opcional | O substatus do evento. Se essa operação corresponder a uma chamada à API REST, esse será o código de status HTTP da chamada REST correspondente. |
| resultDescription | Opcional | A descrição de texto estático dessa operação, por exemplo, “Obter arquivo de armazenamento”. |
| durationMs | Opcional | A duração da operação em milissegundos. |
| callerIpAddress | Opcional | O endereço IP do chamador, caso a operação corresponda a uma chamada à API proveniente de uma entidade com um endereço IP disponível publicamente. |
| correlationId | Opcional | Um GUID usado para agrupar um conjunto de eventos relacionados. Normalmente, se dois eventos têm o mesmo operationName, mas dois status diferentes (por exemplo, “Iniciado” e “Com Êxito”), eles compartilham a mesma ID de correlação. Isso também pode representar outras relações entre os eventos. |
| identidade | Opcional | Um blob JSON que descreve a identidade do usuário ou do aplicativo que realizou a operação. Normalmente, isso inclui a autorização e as declarações/token JWT do Active Directory. |
| Nível | Opcional | O nível de severidade do evento. Precisa ser Informativo, Aviso, Erro ou Crítico. |
| location | Opcional | A região do recurso que emite o evento, por exemplo, “Leste dos EUA” ou “Sul da França” |
| propriedades | Opcional | As propriedades estendidas relacionadas a essa categoria específica de eventos. Todas as propriedades personalizadas/exclusivas precisam ser colocadas dentro desta “Parte B” do esquema. |

## <a name="service-specific-schemas-for-resource-diagnostic-logs"></a>Esquemas específicos do serviço para logs de diagnóstico de recurso
O esquema para os logs de diagnóstico de recurso varia dependendo do recurso e da categoria do log. Esta lista mostra todos os serviços que disponibilizam os logs de diagnóstico e os vínculos para o esquema específico do serviço e da categoria, quando disponíveis.

| Serviço | Esquema e Documentos |
| --- | --- |
| Azure Active Directory | [Visão geral](../../active-directory/reports-monitoring/concept-activity-logs-azure-monitor.md), [esquema de log de auditoria](../../active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema.md) e [esquema entradas](../../active-directory/reports-monitoring/reference-azure-monitor-sign-ins-log-schema.md) |
| Serviços de análise | https://azure.microsoft.com/blog/azure-analysis-services-integration-with-azure-diagnostic-logs/ |
| Gerenciamento de API | [Logs de Diagnóstico de Gerenciamento de API](../../api-management/api-management-howto-use-azure-monitor.md#diagnostic-logs) |
| Gateways do Aplicativo |[Log de diagnóstico do Gateway de Aplicativo](../../application-gateway/application-gateway-diagnostics.md) |
| Automação do Azure |[Análise de log para automação do Azure](../../automation/automation-manage-send-joblogs-log-analytics.md) |
| Lote do Azure |[Logs de diagnóstico do Lote do Azure](../../batch/batch-diagnostics.md) |
| Banco de Dados do Azure para MySQL | [Banco de dados do Azure para logs de Diagnóstico do MySQL](../../mysql/concepts-server-logs.md#diagnostic-logs) |
| Banco de Dados do Azure para PostgreSQL | [Banco de dados do Azure para logs de Diagnóstico do MySQL](../../postgresql/concepts-server-logs.md#diagnostic-logs) |
| Serviços Cognitivos | [Log de diagnóstico para serviços Cognitivos do Azure](../../cognitive-services/diagnostic-logging.md) |
| Rede de Distribuição de Conteúdo | [Logs de diagnóstico do Azure para CDN](../../cdn/cdn-azure-diagnostic-logs.md) |
| CosmosDB | [Registro em log do Azure Cosmos DB](../../cosmos-db/logging.md) |
| Data Factory | [Monitorar data factories usando o Azure Monitor](../../data-factory/monitor-using-azure-monitor.md) |
| Data Lake Analytics |[Acessando os logs de diagnóstico do Azure Data Lake Analytics](../../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Data Lake Store |[Acessando os logs de diagnóstico do Azure Data Lake Store](../../data-lake-store/data-lake-store-diagnostic-logs.md) |
| Hubs de Eventos |[Logs de diagnóstico dos Hubs de Eventos do Azure](../../event-hubs/event-hubs-diagnostic-logs.md) |
| ExpressRoute | Esquema não disponível. |
| Firewall do Azure | Esquema não disponível. |
| Hub IoT | [Operações do Hub IoT](../../iot-hub/iot-hub-monitor-resource-health.md#use-azure-monitor) |
| Key Vault |[Logs do Cofre da Chave do Azure](../../key-vault/key-vault-logging.md) |
| Load Balancer |[Log Analytics para o Azure Load Balancer](../../load-balancer/load-balancer-monitor-log.md) |
| Aplicativos Lógicos |[Esquema de controle personalizado dos Aplicativos Lógicos B2B](../../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| Grupos de segurança de rede |[Análise de logs para NSGs (grupos de segurança de rede)](../../virtual-network/virtual-network-nsg-manage-log.md) |
| Proteção contra DDOS | [Gerenciar Proteção contra DDoS do Azure Standard](../../virtual-network/manage-ddos-protection.md) |
| PowerBI Dedicated | [Log de diagnósticos para o Power BI Inserido no Azure](https://docs.microsoft.com/power-bi/developer/azure-pbie-diag-logs) |
| Serviços de Recuperação | [Modelo de dados para os Backup do Azure](../../backup/backup-azure-reports-data-model.md)|
| Search |[Habilitação e uso da análise de tráfego de pesquisa](../../search/search-traffic-analytics.md) |
| Barramento de Serviço |[Logs de diagnóstico do Barramento de Serviço do Azure](../../service-bus-messaging/service-bus-diagnostic-logs.md) |
| Banco de dados SQL | [Log de diagnósticos do Banco de Dados SQL do Azure](../../sql-database/sql-database-metrics-diag-logging.md) |
| Stream Analytics |[Logs de diagnóstico do trabalho](../../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| Gerenciador de Tráfego | [Esquema de log do Gerenciador de Tráfego](../../traffic-manager/traffic-manager-diagnostic-logs.md) |
| Redes Virtuais | Esquema não disponível. |
| Gateways de Rede Virtual | Esquema não disponível. |

## <a name="supported-log-categories-per-resource-type"></a>Categorias de log com suporte por tipo de recurso
|Tipo de recurso|Categoria|Nome de exibição da categoria|
|---|---|---|
|Microsoft.AnalysisServices/servers|Motor|Motor|
|Microsoft.AnalysisServices/servers|Serviço|Serviço|
|Microsoft.ApiManagement/service|GatewayLogs|Logs relacionados ao Gateway ApiManagement|
|Microsoft.Automation/automationAccounts|JobLogs|Logs de trabalho|
|Microsoft.Automation/automationAccounts|JobStreams|Transmissões de trabalho|
|Microsoft.Automation/automationAccounts|DscNodeStatus|Status do nó DSC|
|Microsoft.Batch/batchAccounts|ServiceLog|Logs de serviço|
|Microsoft.Cdn/profiles/endpoints|CoreAnalytics|Obtém as métricas do ponto de extremidade, como largura de banda, saída etc.|
|Microsoft.ClassicNetwork/networksecuritygroups|Evento de fluxo de regra de grupo de segurança de rede|Evento de fluxo de regra de grupo de segurança de rede|
|Microsoft.CognitiveServices/accounts|Audit|Logs de Auditoria|
|Microsoft.CognitiveServices/accounts|RequestResponse|Logs de Solicitação e Resposta|
|Microsoft.ContainerService/managedClusters|kube-apiserver|Servidor de API do Kubernetes|
|Microsoft.ContainerService/managedClusters|kube-controller-manager|Gerenciador do Controlador do Kubernetes|
|Microsoft.ContainerService/managedClusters|cluster-autoscaler|Dimensionador automático de cluster do Kubernetes|
|Microsoft.ContainerService/managedClusters|kube-scheduler|Agendador do Kubernetes|
|Microsoft.ContainerService/managedClusters|guard|Webhook de Autenticação|
|Microsoft.CustomerInsights/hubs|AuditEvents|AuditEvents|
|Microsoft.DataFactory/factories|ActivityRuns|Registro de execuções de atividade de pipeline|
|Microsoft.DataFactory/factories|PipelineRuns|Registro de execuções de pipeline|
|Microsoft.DataFactory/factories|TriggerRuns|Registro de execuções de gatilho|
|Microsoft.DataLakeAnalytics/accounts|Audit|Logs de Auditoria|
|Microsoft.DataLakeAnalytics/accounts|Solicitações|Logs de solicitação|
|Microsoft.DataLakeStore/accounts|Audit|Logs de Auditoria|
|Microsoft.DataLakeStore/accounts|Requests|Logs de solicitação|
|Microsoft.DBforMySQL/servers|MySqlSlowLogs|Logs do MySQL Server|
|Microsoft.DBforPostgreSQL/servers|PostgreSQLLogs|Logs do PostgreSQL Server|
|Microsoft.Devices/IotHubs|conexões|conexões|
|Microsoft.Devices/IotHubs|DeviceTelemetry|Telemetria de Dispositivo|
|Microsoft.Devices/IotHubs|C2DCommands|Comandos C2C|
|Microsoft.Devices/IotHubs|DeviceIdentityOperations|Operações de Identidade do Dispositivo|
|Microsoft.Devices/IotHubs|FileUploadOperations|Operações de Upload de Arquivo|
|Microsoft.Devices/IotHubs|Rotas|Rotas|
|Microsoft.Devices/IotHubs|D2CTwinOperations|D2CTwinOperations|
|Microsoft.Devices/IotHubs|C2CTwinOperations|Operações de Gêmeos C2D|
|Microsoft.Devices/IotHubs|TwinQueries|Consultas de Gêmeos|
|Microsoft.Devices/IotHubs|JobsOperations|Operações de Trabalhos|
|Microsoft.Devices/IotHubs|DirectMethods|Métodos diretos|
|Microsoft.Devices/IotHubs|E2EDiagnostics|Diagnóstico E2E (versão prévia)|
|Microsoft.Devices/IotHubs|Configurações|Configurações|
|Microsoft.Devices/provisioningServices|DeviceOperations|Operações do Dispositivo|
|Microsoft.Devices/provisioningServices|ServiceOperations|Operações de serviço|
|Microsoft.DocumentDB/databaseAccounts|DataPlaneRequests|DataPlaneRequests|
|Microsoft.DocumentDB/databaseAccounts|MongoRequests|MongoRequests|
|Microsoft.DocumentDB/databaseAccounts|QueryRuntimeStatistics|QueryRuntimeStatistics|
|Microsoft.EventHub/namespaces|ArchiveLogs|Logs de arquivo|
|Microsoft.EventHub/namespaces|OperationalLogs|Logs operacionais|
|Microsoft.EventHub/namespaces|AutoScaleLogs|Logs de Escala Automática|
|Microsoft.Insights/AutoscaleSettings|AutoscaleEvaluations|Avaliações de Dimensionamento Automático|
|Microsoft.Insights/AutoscaleSettings|AutoscaleScaleActions|Ações de Dimensionamento Automático|
|Microsoft.IoTSpaces/Graph|Rastreamento|Rastreamento|
|Microsoft.IoTSpaces/Graph|Operacional|Operacional|
|Microsoft.IoTSpaces/Graph|Audit|Audit|
|Microsoft.IoTSpaces/Graph|UserDefinedFunction|UserDefinedFunction|
|Microsoft.IoTSpaces/Graph|Entrada|Entrada|
|Microsoft.IoTSpaces/Graph|Saída|Saída|
|Microsoft.KeyVault/vaults|AuditEvent|Logs de Auditoria|
|Microsoft.Logic/workflows|WorkflowRuntime|Eventos de diagnóstico de tempo de execução do fluxo de trabalho|
|Microsoft.Logic/integrationAccounts|IntegrationAccountTrackingEvents|Acompanhar os eventos da Conta de Integração|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent|Network Security Group Event|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupRuleCounter|Contador de regras de grupo de segurança de rede|
|Microsoft.Network/loadBalancers|LoadBalancerAlertEvent|Eventos de alerta do Load Balancer|
|Microsoft.Network/loadBalancers|LoadBalancerProbeHealthStatus|Status de integridade da investigação do Load Balancer|
|Microsoft.Network/publicIPAddresses|DDoSProtectionNotifications|Notificações de proteção contra DDoS|
|Microsoft.Network/publicIPAddresses|DDoSMitigationFlowLogs|Logs de fluxo de decisões de mitigação de DDoS|
|Microsoft.Network/publicIPAddresses|DDoSMitigationReports|Relatórios de mitigações de DDoS|
|Microsoft.Network/virtualNetworks|VMProtectionAlerts|Alertas de proteção da VM|
|Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog|Log de acesso do Gateway de Aplicativo|
|Microsoft.Network/applicationGateways|ApplicationGatewayPerformanceLog|Log de desempenho do Gateway de Aplicativo|
|Microsoft.Network/applicationGateways|ApplicationGatewayFirewallLog|Log de firewall do Gateway de Aplicativo|
|Microsoft.Network/securegateways|AzureFirewallApplicationRule|Regra de Aplicativo de Firewall do Azure|
|Microsoft.Network/securegateways|AzureFirewallNetworkRule|Regra de Rede de Firewall do Azure|
|Microsoft.Network/azurefirewalls|AzureFirewallApplicationRule|Regra de Aplicativo de Firewall do Azure|
|Microsoft.Network/azurefirewalls|AzureFirewallNetworkRule|Regra de Rede de Firewall do Azure|
|Microsoft.Network/virtualNetworkGateways|GatewayDiagnosticLog|Logs de Diagnóstico de Gateway|
|Microsoft.Network/virtualNetworkGateways|TunnelDiagnosticLog|Logs de Diagnóstico de Túnel|
|Microsoft.Network/virtualNetworkGateways|RouteDiagnosticLog|Logs de Diagnóstico de Rota|
|Microsoft.Network/virtualNetworkGateways|IKEDiagnosticLog|Logs de Diagnóstico IKE|
|Microsoft.Network/virtualNetworkGateways|P2SDiagnosticLog|Logs de Diagnóstico P2S|
|Microsoft.Network/trafficManagerProfiles|ProbeHealthStatusEvents|Evento de Resultados de Integridade de Investigação do Gerenciador de Tráfego|
|Microsoft.Network/expressRouteCircuits|PeeringRouteLog|Logs de Tabela de Rota de Emparelhamento|
|Microsoft.Network/frontdoors|FrontdoorAccessLog|Log de acesso do Frontdoor|
|Microsoft.Network/frontdoors|FrontdoorWebApplicationFirewallLog|Log de firewall do aplicativo Web Frontdoor|
|Microsoft.PowerBIDedicated/capacities|Motor|Motor|
|Microsoft.RecoveryServices/Vaults|AzureBackupReport|Dados de relatórios de backup do Azure|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryJobs|Trabalhos do Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryEvents|Eventos do Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicatedItems|Itens replicados do Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicationStats|Estatísticas de Replicação do Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryRecoveryPoints|Pontos de Recuperação do Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicationDataUploadRate|Taxa de Carregamento de Dados de Replicação do Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryProtectedDiskDataChurn|Rotatividade de Dados de Disco Protegido do Azure Site Recovery|
|Microsoft.Search/searchServices|OperationLogs|Logs de operação|
|Microsoft.ServiceBus/namespaces|OperationalLogs|Logs operacionais|
|Microsoft.Sql/servers/databases|SQLInsights|Insights do SQL|
|Microsoft.Sql/servers/databases|AutomaticTuning|Ajuste automático|
|Microsoft.Sql/servers/databases|QueryStoreRuntimeStatistics|Estatísticas de Tempo de Execução do Repositório de Consultas|
|Microsoft.Sql/servers/databases|QueryStoreWaitStatistics|Estatísticas de Espera do Repositório de Consultas|
|Microsoft.Sql/servers/databases|Errors|Errors|
|Microsoft.Sql/servers/databases|DatabaseWaitStatistics|Estatísticas de Espera do Banco de Dados|
|Microsoft.Sql/servers/databases|Tempos limite|Tempos limite|
|Microsoft.Sql/servers/databases|Bloqueios|Bloqueios|
|Microsoft.Sql/servers/databases|Deadlocks|Deadlocks|
|Microsoft.Sql/servers/databases|Audit|Logs de Auditoria|
|Microsoft.Sql/servers/databases|SQLSecurityAuditEvents|Evento de Auditoria de Segurança do SQL|
|Microsoft.Sql/servers/databases|DmsWorkers|Trabalhos DMS|
|Microsoft.Sql/servers/databases|ExecRequests|Solicitações de Exec|
|Microsoft.Sql/servers/databases|RequestSteps|Etapas de solicitação|
|Microsoft.Sql/servers/databases|SqlRequests|Solicitações de Sql|
|Microsoft.Sql/servers/databases|Esperas|Esperas|
|Microsoft.Sql/managedInstances|ResourceUsageStats|Estatísticas de uso de recursos|
|Microsoft.Sql/managedInstances|SQLSecurityAuditEvents|Evento de Auditoria de Segurança do SQL|
|Microsoft.Sql/managedInstances/databases|SQLInsights|Insights do SQL|
|Microsoft.Sql/managedInstances/databases|QueryStoreRuntimeStatistics|Estatísticas de Tempo de Execução do Repositório de Consultas|
|Microsoft.Sql/managedInstances/databases|QueryStoreWaitStatistics|Estatísticas de Espera do Repositório de Consultas|
|Microsoft.Sql/managedInstances/databases|Errors|Errors|
|Microsoft.StreamAnalytics/streamingjobs|Execução|Execução|
|Microsoft.StreamAnalytics/streamingjobs|Criação|Criação|
|microsoft.web/sites|FunctionExecutionLogs|Logs de Execução de Função|
|microsoft.web/sites/slots|FunctionExecutionLogs|Logs de Execução de Função|

## <a name="next-steps"></a>Próximas etapas

* [Saiba mais sobre logs de diagnóstico](../../azure-monitor/platform/diagnostic-logs-overview.md)
* [Transmitir logs de diagnóstico de recurso os **Hubs de Eventos**](../../azure-monitor/platform/diagnostic-logs-stream-event-hubs.md)
* [Alterar as configurações de diagnóstico do recurso usando a API REST do Azure Monitor](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)
* [Analisar logs do Armazenamento do Azure com o Log Analytics](../../azure-monitor/platform/collect-azure-metrics-logs.md)
