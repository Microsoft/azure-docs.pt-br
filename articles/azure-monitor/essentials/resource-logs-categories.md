---
title: Azure Monitor de serviços e categorias com suporte de logs de recursos
description: Referência de Azure Monitor entender os serviços com suporte e o esquema de eventos para logs de recursos do Azure.
ms.topic: reference
ms.date: 01/29/2021
ms.openlocfilehash: 9a04d0f470522dd4689d604756ffd25e70c5d456
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102033139"
---
# <a name="supported-categories-for-azure-resource-logs"></a>Categorias com suporte para logs de recursos do Azure

> [!NOTE]
> Os logs de recursos eram anteriormente conhecidos como logs de diagnóstico. O nome foi alterado em outubro de 2019, pois os tipos de logs coletados por Azure Monitor foram deslocados para incluir mais do que apenas o recurso do Azure.

[Azure monitor logs de recursos](../essentials/platform-logs-overview.md) são logs emitidos pelos serviços do Azure que descrevem a operação desses serviços ou recursos. Todos os logs de recursos disponíveis por meio de Azure Monitor compartilham um esquema comum de nível superior, com flexibilidade para cada serviço para emitir propriedades exclusivas para seus próprios eventos.

Uma combinação do tipo de recurso (disponível na propriedade `resourceId`) e da `category` identifica exclusivamente um esquema. Há um esquema comum para todos os logs de recursos com campos específicos do serviço, então adicionados para diferentes categorias de log. Para obter mais informações, consulte [esquema específico de serviço e comum para logs de recursos do Azure]()


## <a name="costs"></a>Custos

Há custos associados ao envio e armazenamento de dados em Log Analytics, armazenamento do Azure e/ou Hub de eventos. Você pode pagar pelo custo para colocar os dados nesses locais e mantê-los lá.  Os logs de recursos são um tipo de dados que você pode enviar para esses locais. 

Há um custo adicional para exportar algumas categorias de logs de recursos para esses locais. Esses logs com custos de exportação são listados na tabela a seguir. Para obter mais informações sobre esse preço, consulte a seção de logs da plataforma na [página de preços do Azure monitor](https://azure.microsoft.com/pricing/details/monitor/).

## <a name="supported-log-categories-per-resource-type"></a>Categorias de log com suporte por tipo de recurso

A seguir está uma lista dos tipos de logs disponíveis para cada tipo de recurso. 

Algumas categorias só podem ter suporte para tipos específicos de recursos. Consulte a documentação específica do recurso se você sentir que falta um recurso. Por exemplo, as categorias Microsoft. SQL/servidores/bancos de dados não estão disponíveis para todos os tipos de bancos de dados. Para obter mais informações, consulte [informações sobre o log de diagnóstico do banco de dados SQL](../../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md). 

Se você considerar que há algo ausente, poderá abrir um comentário do GitHub na parte inferior deste artigo.
## <a name="microsoftaaddomainservices"></a>Microsoft. AAD/DomainServices

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|AccountLogon|AccountLogon|Não|
|AccountManagement|AccountManagement|Não|
|DetailTracking|DetailTracking|Não|
|DirectoryServiceAccess|DirectoryServiceAccess|Não|
|LogonLogoff|LogonLogoff|Não|
|Objectaccess|Objectaccess|Não|
|PolicyChange|PolicyChange|Não|
|PrivilegeUse|PrivilegeUse|Não|
|SystemSecurity|SystemSecurity|Não|


## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|Mecanismo|Mecanismo|Não|
|Serviço|Serviço|Não|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|GatewayLogs|Logs relacionados ao Gateway ApiManagement|Não|


## <a name="microsoftappconfigurationconfigurationstores"></a>Microsoft.AppConfiguration/configurationStores

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|HttpRequest|Solicitações HTTP|Sim|


## <a name="microsoftappplatformspring"></a>Microsoft.AppPlatform/Spring

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|ApplicationConsole|Console de aplicativo|Não|
|SystemLogs|Logs do sistema|Não|


## <a name="microsoftattestationattestationproviders"></a>Microsoft.Attestation/attestationProviders

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|AuditEvent|Categoria do log de mensagens AuditEvent.|Não|
|ERRA|Categoria do log de mensagens de erro.|Não|
|INF|Categoria de log de mensagens informativa.|Não|
|AVI|Categoria do log de mensagens de aviso.|Não|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|DscNodeStatus|Status do nó DSC|Não|
|JobLogs|Logs de trabalho|Não|
|JobStreams|Transmissões de trabalho|Não|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|ServiceLog|Logs de serviço|Não|


## <a name="microsoftbatchaiworkspaces"></a>Microsoft.BatchAI/workspaces

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|BaiClusterEvent|BaiClusterEvent|Não|
|BaiClusterNodeEvent|BaiClusterNodeEvent|Não|
|BaiJobEvent|BaiJobEvent|Não|


## <a name="microsoftblockchainblockchainmembers"></a>Microsoft.Blockchain/blockchainMembers

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|BlockchainApplication|Aplicativo Blockchain|Não|
|FabricOrderer|Solicitante de malha|Não|
|FabricPeer|Ponto de malha|Não|
|Proxy|Proxy|Não|


## <a name="microsoftblockchaincordamembers"></a>Microsoft. Blockchain/cordaMembers

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|BlockchainApplication|Aplicativo Blockchain|Não|


## <a name="microsoftbotservicebotservices"></a>microsoft.botservice/botservices

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|BotRequest|Solicitações dos canais para o bot|Não|
|DependencyRequest|Solicitações para dependências|Não|


## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Microsoft. CDN/cdnwebapplicationfirewallpolicies

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|WebApplicationFirewallLogs|Logs de firewall de aplicação Web|Não|


## <a name="microsoftcdnprofiles"></a>Microsoft.Cdn/profiles

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|AzureCdnAccessLog|Log de acesso da CDN do Azure|Não|
|FrontDoorAccessLog|Log de acesso do FrontDoor|Sim|
|FrontDoorHealthProbeLog|Log de investigação de integridade do FrontDoor|Sim|
|FrontDoorWebApplicationFirewallLog|Log do FrontDoor WebApplicationFirewall|Sim|


## <a name="microsoftcdnprofilesendpoints"></a>Microsoft.Cdn/profiles/endpoints

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|CoreAnalytics|Obtém as métricas do ponto de extremidade, como largura de banda, saída etc.|Não|


## <a name="microsoftclassicnetworknetworksecuritygroups"></a>Microsoft.ClassicNetwork/networksecuritygroups

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|Evento de fluxo de regra de grupo de segurança de rede|Evento de fluxo de regra de grupo de segurança de rede|Não|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|Audit|Logs de Auditoria|Não|
|RequestResponse|Logs de Solicitação e Resposta|Não|
|Trace|Logs de rastreamento|Não|


## <a name="microsoftcommunicationcommunicationservices"></a>Microsoft. Communication/Communications

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|ChatOperational|Logs de chat operacionais|Não|
|SMSOperational|Logs de SMS operacionais|Não|
|Uso|Registros de uso|Não|


## <a name="microsoftcontainerregistryregistries"></a>Microsoft.ContainerRegistry/registries

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|ContainerRegistryLoginEvents|Eventos de logon|Não|
|ContainerRegistryRepositoryEvents|Logs do RepositoryEvent|Não|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|cluster-autoscaler|Dimensionador automático de cluster do Kubernetes|Não|
|guard|guard|Não|
|kube-apiserver|Servidor de API do Kubernetes|Não|
|Kube-auditoria|Kubernetes auditoria|Não|
|Kube-Audit-admin|Logs do administrador de auditoria do kubernetes|Não|
|kube-controller-manager|Gerenciador do Controlador do Kubernetes|Não|
|kube-scheduler|Agendador do Kubernetes|Não|


## <a name="microsoftcustomprovidersresourceproviders"></a>Microsoft. CustomProviders/resourceproviders

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|AuditLogs|Logs de auditoria para chamadas MiniRP|Não|


## <a name="microsoftd365customerinsightsinstances"></a>Microsoft. D365CustomerInsights/instâncias

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|Audit|Eventos de auditoria|Não|
|Operacional|Eventos operacionais|Não|


## <a name="microsoftdatabricksworkspaces"></a>Microsoft. databricks/espaços de trabalho

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|accounts|Contas do databricks|Não|
|clusters|Clusters do databricks|Não|
|dBFS|Sistema de arquivos do Databricks|Não|
|instancePools|Pools de instância|Não|
|jobs|Trabalhos do databricks|Não|
|notebook|Databricks Notebook|Não|
|segredos|Segredos do databricks|Não|
|sqlpermissions|Sqlpermissões do databricks|Não|
|ssh|SSH do databricks|Não|
|workspace|Espaço de trabalho do databricks|Não|


## <a name="microsoftdatacollaborationworkspaces"></a>Microsoft. datacollaboration/Workspaces

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|CollaborationAudit|Auditoria de colaboração|Sim|
|Dataassets|Ativos de dados|Não|
|Pipelines|Pipelines|Não|
|Propostas|Propostas|Não|
|Scripts|Scripts|Não|


## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/factories

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|ActivityRuns|Registro de execuções de atividade de pipeline|Não|
|PipelineRuns|Registro de execuções de pipeline|Não|
|SSISIntegrationRuntimeLogs|Logs do SSIS Integration Runtime|Não|
|SSISPackageEventMessageContext|Contexto de mensagem de evento do pacote SSIS|Não|
|SSISPackageEventMessages|Mensagens de evento de pacote SSIS|Não|
|SSISPackageExecutableStatistics|Estatísticas de executável do pacote SSIS|Não|
|SSISPackageExecutionComponentPhases|Fases do componente de execução de pacote SSIS|Não|
|SSISPackageExecutionDataStatistics|Estatísticas de dados de exeution do pacote SSIS|Não|
|TriggerRuns|Registro de execuções de gatilho|Não|


## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/accounts

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|Audit|Logs de Auditoria|Não|
|Requests|Logs de solicitação|Não|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|Audit|Logs de Auditoria|Não|
|Requests|Logs de solicitação|Não|


## <a name="microsoftdatashareaccounts"></a>Microsoft.DataShare/accounts

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|ReceivedShareSnapshots|Instantâneos de compartilhamento recebidos|Não|
|SentShareSnapshots|Instantâneos de compartilhamento enviados|Não|
|Compartilhamentos|Compartilhamentos|Não|
|ShareSubscriptions|Compartilhar assinaturas|Não|


## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|MySqlAuditLogs|Logs de auditoria do MariaDB|Não|
|MySqlSlowLogs|Logs do servidor MariaDB|Não|


## <a name="microsoftdbformysqlflexibleservers"></a>Microsoft.DBforMySQL/flexibleServers

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|MySqlAuditLogs|Logs de auditoria do MySQL|Não|
|MySqlSlowLogs|Logs lentos do MySQL|Não|


## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|MySqlAuditLogs|Logs de auditoria do MySQL|Não|
|MySqlSlowLogs|Logs do MySQL Server|Não|


## <a name="microsoftdbforpostgresqlflexibleservers"></a>Microsoft.DBforPostgreSQL/flexibleServers

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|PostgreSQLLogs|Logs do PostgreSQL Server|Não|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|PostgreSQLLogs|Logs do PostgreSQL Server|Não|
|QueryStoreRuntimeStatistics|As estatísticas de tempo de execução Repositório de Consultas PostgreSQL|Não|
|QueryStoreWaitStatistics|Estatísticas de espera Repositório de Consultas PostgreSQL|Não|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft.DBforPostgreSQL/serversv2

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|PostgreSQLLogs|Logs do PostgreSQL Server|Não|


## <a name="microsoftdesktopvirtualizationapplicationgroups"></a>Microsoft. DesktopVirtualization/applicationgroups

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|Ponto de verificação|Ponto de verificação|Não|
|Erro|Erro|Não|
|Gerenciamento|Gerenciamento|Não|


## <a name="microsoftdesktopvirtualizationhostpools"></a>Microsoft. DesktopVirtualization/hostpools

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|AgentHealthStatus|AgentHealthStatus|Não|
|Ponto de verificação|Ponto de verificação|Não|
|Conexão|Conexão|Não|
|Erro|Erro|Não|
|HostRegistration|HostRegistration|Não|
|Gerenciamento|Gerenciamento|Não|


## <a name="microsoftdesktopvirtualizationworkspaces"></a>Microsoft. DesktopVirtualization/Workspaces

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|Ponto de verificação|Ponto de verificação|Não|
|Erro|Erro|Não|
|Feed|Feed|Não|
|Gerenciamento|Gerenciamento|Não|


## <a name="microsoftdeviceselasticpoolsiothubtenants"></a>Microsoft.Devices/ElasticPools/IotHubTenants

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|C2DCommands|Comandos C2C|Não|
|C2CTwinOperations|Operações de Gêmeos C2D|Não|
|Configurações|Configurações|Não|
|conexões|conexões|Não|
|D2CTwinOperations|D2CTwinOperations|Não|
|DeviceIdentityOperations|Operações de Identidade do Dispositivo|Não|
|DeviceStreams|Fluxos de dispositivo (visualização)|Não|
|DeviceTelemetry|Telemetria de Dispositivo|Não|
|DirectMethods|Métodos diretos|Não|
|DistributedTracing|Rastreamento Distribuído (Versão Prévia)|Não|
|FileUploadOperations|Operações de Upload de Arquivo|Não|
|JobsOperations|Operações de Trabalhos|Não|
|Rotas|Rotas|Não|
|TwinQueries|Consultas de Gêmeos|Não|


## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|C2DCommands|Comandos C2C|Não|
|C2CTwinOperations|Operações de Gêmeos C2D|Não|
|Configurações|Configurações|Não|
|conexões|conexões|Não|
|D2CTwinOperations|D2CTwinOperations|Não|
|DeviceIdentityOperations|Operações de Identidade do Dispositivo|Não|
|DeviceStreams|Fluxos de dispositivo (visualização)|Não|
|DeviceTelemetry|Telemetria de Dispositivo|Não|
|DirectMethods|Métodos diretos|Não|
|DistributedTracing|Rastreamento Distribuído (Versão Prévia)|Não|
|FileUploadOperations|Operações de Upload de Arquivo|Não|
|JobsOperations|Operações de Trabalhos|Não|
|Rotas|Rotas|Não|
|TwinQueries|Consultas de Gêmeos|Não|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|DeviceOperations|Operações do Dispositivo|Não|
|ServiceOperations|Operações de serviço|Não|


## <a name="microsoftdigitaltwinsdigitaltwinsinstances"></a>Microsoft. DigitalTwins/digitalTwinsInstances

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|DigitalTwinsOperation|DigitalTwinsOperation|Não|
|EventRoutesOperation|EventRoutesOperation|Não|
|ModelsOperation|ModelsOperation|Não|
|QueryOperation|QueryOperation|Não|


## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|CassandraRequests|CassandraRequests|Não|
|ControlPlaneRequests|ControlPlaneRequests|Não|
|DataPlaneRequests|DataPlaneRequests|Não|
|GremlinRequests|GremlinRequests|Não|
|MongoRequests|MongoRequests|Não|
|PartitionKeyRUConsumption|PartitionKeyRUConsumption|Não|
|PartitionKeyStatistics|PartitionKeyStatistics|Não|
|QueryRuntimeStatistics|QueryRuntimeStatistics|Não|


## <a name="microsofteventgriddomains"></a>Microsoft.EventGrid/domains

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|DeliveryFailures|Logs de falha de entrega|Não|
|PublishFailures|Publicar logs de falha|Não|


## <a name="microsofteventgridpartnernamespaces"></a>Microsoft. EventGrid/partnerNamespaces

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|DeliveryFailures|Logs de falha de entrega|Não|
|PublishFailures|Publicar logs de falha|Não|


## <a name="microsofteventgridpartnertopics"></a>Microsoft. EventGrid/partnerTopics

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|DeliveryFailures|Logs de falha de entrega|Não|


## <a name="microsofteventgridsystemtopics"></a>Microsoft. EventGrid/systemTopics

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|DeliveryFailures|Logs de falha de entrega|Não|


## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|DeliveryFailures|Logs de falha de entrega|Não|
|PublishFailures|Publicar logs de falha|Não|


## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|ArchiveLogs|Logs de arquivo|Não|
|AutoScaleLogs|Logs de Escala Automática|Não|
|CustomerManagedKeyUserLogs|Logs de chave gerenciado pelo cliente|Não|
|EventHubVNetConnectionEvent|Logs de conexão de filtragem de VNet/IP|Não|
|KafkaCoordinatorLogs|Logs do coordenador de Kafka|Não|
|KafkaUserErrorLogs|Logs de erros do usuário Kafka|Não|
|OperationalLogs|Logs operacionais|Não|


## <a name="microsoftexperimentationexperimentworkspaces"></a>Microsoft. Experimentation/experimentWorkspaces

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|Solicitação|Solicitação|Não|


## <a name="microsofthealthcareapisservices"></a>Microsoft.HealthcareApis/services

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|AuditLogs|Logs de auditoria|Não|


## <a name="microsoftinsightsautoscalesettings"></a>microsoft.insights/autoscalesettings

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|AutoscaleEvaluations|Avaliações de Dimensionamento Automático|Não|
|AutoscaleScaleActions|Ações de Dimensionamento Automático|Não|


## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Components

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|AppAvailabilityResults|Resultados da disponibilidade|Não|
|AppBrowserTimings|Tempos do navegador|Não|
|AppDependencies|Dependências|Não|
|AppEvents|Eventos|Não|
|AppExceptions|Exceções|Não|
|AppMetrics|Métricas|Não|
|AppPageViews|Exibições de página|Não|
|AppPerformanceCounters|Contadores de desempenho|Não|
|AppRequests|Requests|Não|
|AppSystemEvents|Eventos do sistema|Não|
|AppTraces|Rastreamentos|Não|


## <a name="microsoftiotspacesgraph"></a>Microsoft.IoTSpaces/Graph

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|Audit|Audit|Não|
|Saída|Saída|Não|
|Entrada|Entrada|Não|
|Operacional|Operacional|Não|
|Trace|Trace|Não|
|UserDefinedFunction|UserDefinedFunction|Não|


## <a name="microsoftkeyvaultmanagedhsms"></a>Microsoft. keyvault/managedhsms

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|AuditEvent|Auditar evento|Não|


## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|AuditEvent|Logs de Auditoria|Não|


## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Clusters

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|Comando|Comando|Não|
|FailedIngestion|Operações de ingestão com falha|Não|
|IngestionBatching|Envio em lote de ingestão|Não|
|Consulta|Consulta|Não|
|SucceededIngestion|Operações de ingestão com êxito|Não|
|TableDetails|Detalhes da tabela|Não|
|TableUsageStatistics|Estatísticas de uso de tabela|Não|


## <a name="microsoftlogicintegrationaccounts"></a>Microsoft.Logic/integrationAccounts

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|IntegrationAccountTrackingEvents|Acompanhar os eventos da Conta de Integração|Não|


## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|WorkflowRuntime|Eventos de diagnóstico de runtime de fluxo de trabalho|Não|


## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/workspaces

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|AmlComputeClusterEvent|AmlComputeClusterEvent|Não|
|AmlComputeClusterNodeEvent|AmlComputeClusterNodeEvent|Não|
|AmlComputeCpuGpuUtilization|AmlComputeCpuGpuUtilization|Não|
|AmlComputeJobEvent|AmlComputeJobEvent|Não|
|AmlRunStatusChangedEvent|AmlRunStatusChangedEvent|Não|


## <a name="microsoftmediamediaservices"></a>Microsoft.Media/mediaservices

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|KeyDeliveryRequests|Solicitações de entrega de chave|Não|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|ApplicationGatewayAccessLog|Log de acesso do Gateway de Aplicativo|Não|
|ApplicationGatewayFirewallLog|Log de firewall do Gateway de Aplicativo|Não|
|ApplicationGatewayPerformanceLog|Log de desempenho do Gateway de Aplicativo|Não|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|AzureFirewallApplicationRule|Regra de Aplicativo de Firewall do Azure|Não|
|AzureFirewallDnsProxy|Proxy de DNS do firewall do Azure|Não|
|AzureFirewallNetworkRule|Regra de Rede de Firewall do Azure|Não|


## <a name="microsoftnetworkbastionhosts"></a>Microsoft.Network/bastionHosts

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|BastionAuditLogs|Logs de auditoria de bastiões|Não|


## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|PeeringRouteLog|Logs de Tabela de Rota de Emparelhamento|Não|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/frontdoors

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|FrontdoorAccessLog|Log de acesso do Frontdoor|Não|
|FrontdoorWebApplicationFirewallLog|Log de firewall do aplicativo Web Frontdoor|Não|


## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|LoadBalancerAlertEvent|Eventos de alerta do Load Balancer|Não|
|LoadBalancerProbeHealthStatus|Status de integridade da investigação do Load Balancer|Não|


## <a name="microsoftnetworknetworksecuritygroups"></a>Microsoft.Network/networksecuritygroups

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|NetworkSecurityGroupEvent|Network Security Group Event|Não|
|NetworkSecurityGroupFlowEvent|Evento de fluxo de regra de grupo de segurança de rede|Não|
|NetworkSecurityGroupRuleCounter|Contador de regras de grupo de segurança de rede|Não|


## <a name="microsoftnetworkp2svpngateways"></a>Microsoft. Network/p2sVpnGateways

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|GatewayDiagnosticLog|Logs de Diagnóstico de Gateway|Não|
|IKEDiagnosticLog|Logs de Diagnóstico IKE|Não|
|P2SDiagnosticLog|Logs de Diagnóstico P2S|Não|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|DDoSMitigationFlowLogs|Logs de fluxo de decisões de mitigação de DDoS|Não|
|DDoSMitigationReports|Relatórios de mitigações de DDoS|Não|
|DDoSProtectionNotifications|Notificações de proteção contra DDoS|Não|


## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|ProbeHealthStatusEvents|Evento de Resultados de Integridade de Investigação do Gerenciador de Tráfego|Não|


## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|GatewayDiagnosticLog|Logs de Diagnóstico de Gateway|Não|
|IKEDiagnosticLog|Logs de Diagnóstico IKE|Não|
|P2SDiagnosticLog|Logs de Diagnóstico P2S|Não|
|RouteDiagnosticLog|Logs de Diagnóstico de Rota|Não|
|TunnelDiagnosticLog|Logs de Diagnóstico de Túnel|Não|


## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft.Network/virtualNetworks

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|VMProtectionAlerts|Alertas de proteção da VM|Não|


## <a name="microsoftnetworkvpngateways"></a>Microsoft. Network/vpnGateways

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|GatewayDiagnosticLog|Logs de Diagnóstico de Gateway|Não|
|IKEDiagnosticLog|Logs de Diagnóstico IKE|Não|
|RouteDiagnosticLog|Logs de Diagnóstico de Rota|Não|
|TunnelDiagnosticLog|Logs de Diagnóstico de Túnel|Não|


## <a name="microsoftnotificationhubsnamespaces"></a>Microsoft.NotificationHubs/namespaces

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|OperationalLogs|Logs operacionais|Não|


## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.OperationalInsights/workspaces

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|Audit|Logs de Auditoria|Não|


## <a name="microsoftpowerbitenants"></a>Microsoft. PowerBI/locatários

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|Mecanismo|Mecanismo|Não|


## <a name="microsoftpowerbitenantsworkspaces"></a>Microsoft. PowerBI/locatários/espaços de trabalho

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|Mecanismo|Mecanismo|Não|


## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|Mecanismo|Mecanismo|Não|


## <a name="microsoftprojectbabylonaccounts"></a>Microsoft. ProjectBabylon/accounts

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|ScanStatusLogEvent|ScanStatus|Não|


## <a name="microsoftpurviewaccounts"></a>Microsoft. alcance/accounts

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|ScanStatusLogEvent|ScanStatus|Não|


## <a name="microsoftrecoveryservicesvaults"></a>Microsoft.RecoveryServices/Vaults

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|AddonAzureBackupAlerts|Dados de alerta de backup do Azure addon|Não|
|AddonAzureBackupJobs|Dados do trabalho de backup do Azure addon|Não|
|AddonAzureBackupPolicy|Dados de política de backup do Azure addon|Não|
|AddonAzureBackupProtectedInstance|Dados de instância protegida do backup do Azure addon|Não|
|AddonAzureBackupStorage|Dados de armazenamento de backup do Azure addon|Não|
|AzureBackupReport|Dados de relatórios de backup do Azure|Não|
|AzureSiteRecoveryEvents|Eventos do Azure Site Recovery|Não|
|AzureSiteRecoveryJobs|Trabalhos do Azure Site Recovery|Não|
|AzureSiteRecoveryProtectedDiskDataChurn|Rotatividade de Dados de Disco Protegido do Azure Site Recovery|Não|
|AzureSiteRecoveryRecoveryPoints|Pontos de Recuperação do Azure Site Recovery|Não|
|AzureSiteRecoveryReplicatedItems|Itens replicados do Azure Site Recovery|Não|
|AzureSiteRecoveryReplicationDataUploadRate|Taxa de Carregamento de Dados de Replicação do Azure Site Recovery|Não|
|AzureSiteRecoveryReplicationStats|Estatísticas de Replicação do Azure Site Recovery|Não|
|CoreAzureBackup|Principais dados de backup do Azure|Não|


## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/namespaces

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|HybridConnectionsEvent|Eventos de HybridConnections|Não|
|HybridConnectionsLogs|HybridConnectionsLogs|Não|


## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|OperationLogs|Logs de operação|Não|


## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|OperationalLogs|Logs operacionais|Não|


## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/SignalR

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|AllLogs|Logs do serviço de Signaler do Azure.|Não|


## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|DevOpsOperationsAudit|Logs de auditoria de operações do DevOps|Não|
|ResourceUsageStats|Estatísticas de uso de recursos|Não|
|SQLSecurityAuditEvents|Evento de Auditoria de Segurança do SQL|Não|


## <a name="microsoftsqlmanagedinstancesdatabases"></a>Microsoft.Sql/managedInstances/databases

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|Errors|Errors|Não|
|QueryStoreRuntimeStatistics|Estatísticas de Runtime do Repositório de Consultas|Não|
|QueryStoreWaitStatistics|Estatísticas de Espera do Repositório de Consultas|Não|
|SQLInsights|Insights do SQL|Não|


## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|AutomaticTuning|Ajuste automático|Não|
|Blocos|Blocos|Não|
|DatabaseWaitStatistics|Estatísticas de Espera do Banco de Dados|Não|
|Deadlocks|Deadlocks|Não|
|DevOpsOperationsAudit|Logs de auditoria de operações do DevOps|Não|
|DmsWorkers|Trabalhos DMS|Não|
|Errors|Errors|Não|
|ExecRequests|Solicitações de Exec|Não|
|QueryStoreRuntimeStatistics|Estatísticas de Runtime do Repositório de Consultas|Não|
|QueryStoreWaitStatistics|Estatísticas de Espera do Repositório de Consultas|Não|
|RequestSteps|Etapas de solicitação|Não|
|SQLInsights|Insights do SQL|Não|
|SqlRequests|Solicitações de Sql|Não|
|SQLSecurityAuditEvents|Evento de Auditoria de Segurança do SQL|Não|
|Tempos limite|Tempos limite|Não|
|Esperas|Esperas|Não|


## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|StorageDelete|StorageDelete|Sim|
|StorageRead|StorageRead|Sim|
|StorageWrite|StorageWrite|Sim|


## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|StorageDelete|StorageDelete|Sim|
|StorageRead|StorageRead|Sim|
|StorageWrite|StorageWrite|Sim|


## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|StorageDelete|StorageDelete|Sim|
|StorageRead|StorageRead|Sim|
|StorageWrite|StorageWrite|Sim|


## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|StorageDelete|StorageDelete|Sim|
|StorageRead|StorageRead|Sim|
|StorageWrite|StorageWrite|Sim|


## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|Criação|Criação|Não|
|Execução|Execução|Não|


## <a name="microsoftsynapseworkspaces"></a>Microsoft.Synapse/workspaces

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|BuiltinSqlReqsEnded|Solicitações de pool de SQL internas encerradas|Não|
|GatewayApiRequests|Solicitações de API de gateway Synapse|Não|
|SQLSecurityAuditEvents|Evento de Auditoria de Segurança do SQL|Não|
|SynapseRbacOperations|Operações de RBAC Synapse|Não|


## <a name="microsoftsynapseworkspacesbigdatapools"></a>Microsoft. Synapse/Workspaces/bigDataPools

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|BigDataPoolAppsEnded|Aplicativos de pool de Big data encerrados|Não|


## <a name="microsoftsynapseworkspacessqlpools"></a>Microsoft. Synapse/espaços de trabalho/sqlpools

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|DmsWorkers|Trabalhos DMS|Não|
|ExecRequests|Solicitações de Exec|Não|
|RequestSteps|Etapas de solicitação|Não|
|SqlRequests|Solicitações de Sql|Não|
|SQLSecurityAuditEvents|Evento de auditoria de segurança do SQL|Não|
|Esperas|Esperas|Não|


## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft.TimeSeriesInsights/environments

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|Entrada|Entrada|Não|
|Gerenciamento|Gerenciamento|Não|


## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft.TimeSeriesInsights/environments/eventsources

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|Entrada|Entrada|Não|
|Gerenciamento|Gerenciamento|Não|


## <a name="microsoftwebhostingenvironments"></a>microsoft.web/hostingenvironments

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|AppServiceEnvironmentPlatformLogs|Ambiente do Serviço de Aplicativondo logs da plataforma|Não|


## <a name="microsoftwebsites"></a>microsoft.web/sites

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|AppServiceAntivirusScanAuditLogs|Relatar logs de auditoria de antivírus|Não|
|AppServiceAppLogs|Logs de aplicativo do serviço de aplicativo|Não|
|AppServiceAuditLogs|Acessar logs de auditoria|Não|
|AppServiceConsoleLogs|Logs do console do serviço de aplicativo|Não|
|AppServiceFileAuditLogs|Logs de auditoria de alteração de conteúdo do site|Não|
|AppServiceHTTPLogs|Logs de HTTP|Não|
|AppServiceIPSecAuditLogs|Logs de auditoria do IPSecurity|Não|
|AppServicePlatformLogs|Logs da plataforma do serviço de aplicativo|Não|
|FunctionAppLogs|Logs do aplicativo de funções|Não|


## <a name="microsoftwebsitesslots"></a>microsoft.web/sites/slots

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|AppServiceAntivirusScanAuditLogs|Relatar logs de auditoria de antivírus|Não|
|AppServiceAppLogs|Logs de aplicativo do serviço de aplicativo|Não|
|AppServiceAuditLogs|Acessar logs de auditoria|Não|
|AppServiceConsoleLogs|Logs do console do serviço de aplicativo|Não|
|AppServiceFileAuditLogs|Logs de auditoria de alteração de conteúdo do site|Não|
|AppServiceHTTPLogs|Logs de HTTP|Não|
|AppServiceIPSecAuditLogs|Logs de auditoria do IPSecurity|Não|
|AppServicePlatformLogs|Logs da plataforma do serviço de aplicativo|Não|
|FunctionAppLogs|Logs do aplicativo de funções|Não|



## <a name="next-steps"></a>Próximas etapas

* [Saiba mais sobre os logs de recursos](../essentials/platform-logs-overview.md)
* [Transmitir logs de recurso de recurso para **hubs de eventos**](./resource-logs.md#send-to-azure-event-hubs)
* [Alterar as configurações de diagnóstico do log de recursos usando a API REST do Azure Monitor](/rest/api/monitor/diagnosticsettings)
* [Analisar logs do Armazenamento do Azure com o Log Analytics](./resource-logs.md#send-to-log-analytics-workspace)

