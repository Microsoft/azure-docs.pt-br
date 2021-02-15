---
title: Azure Monitor de serviços e categorias com suporte de logs de recursos
description: Referência de Azure Monitor entender os serviços com suporte e o esquema de eventos para logs de recursos do Azure.
ms.subservice: logs
ms.topic: reference
ms.date: 01/29/2021
ms.openlocfilehash: 02488e1a3ff26acf9ff318a2d5c09115aaba8df9
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99070739"
---
# <a name="supported-categories-for-azure-resource-logs"></a>Categorias com suporte para logs de recursos do Azure

> [!NOTE]
> Os logs de recursos eram anteriormente conhecidos como logs de diagnóstico. O nome foi alterado em outubro de 2019, pois os tipos de logs coletados por Azure Monitor foram deslocados para incluir mais do que apenas o recurso do Azure.

[Azure monitor logs de recursos](./platform-logs-overview.md) são logs emitidos pelos serviços do Azure que descrevem a operação desses serviços ou recursos. Todos os logs de recursos disponíveis por meio de Azure Monitor compartilham um esquema comum de nível superior, com flexibilidade para cada serviço para emitir propriedades exclusivas para seus próprios eventos.

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
|AccountLogon|AccountLogon|No|
|AccountManagement|AccountManagement|No|
|DetailTracking|DetailTracking|No|
|DirectoryServiceAccess|DirectoryServiceAccess|No|
|LogonLogoff|LogonLogoff|No|
|Objectaccess|Objectaccess|No|
|PolicyChange|PolicyChange|No|
|PrivilegeUse|PrivilegeUse|No|
|SystemSecurity|SystemSecurity|Não|


## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|Mecanismo|Mecanismo|No|
|Serviço|Serviço|Não|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|GatewayLogs|Logs relacionados ao Gateway ApiManagement|No|


## <a name="microsoftappconfigurationconfigurationstores"></a>Microsoft.AppConfiguration/configurationStores

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|HttpRequest|Solicitações HTTP|Yes|


## <a name="microsoftappplatformspring"></a>Microsoft.AppPlatform/Spring

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|ApplicationConsole|Console de aplicativo|No|
|SystemLogs|Logs do sistema|No|


## <a name="microsoftattestationattestationproviders"></a>Microsoft.Attestation/attestationProviders

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|AuditEvent|Categoria do log de mensagens AuditEvent.|No|
|ERRA|Categoria do log de mensagens de erro.|No|
|INF|Categoria de log de mensagens informativa.|No|
|AVI|Categoria do log de mensagens de aviso.|Não|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|DscNodeStatus|Status do nó DSC|No|
|JobLogs|Logs de trabalho|No|
|JobStreams|Transmissões de trabalho|Não|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|ServiceLog|Logs de serviço|Não|


## <a name="microsoftbatchaiworkspaces"></a>Microsoft.BatchAI/workspaces

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|BaiClusterEvent|BaiClusterEvent|No|
|BaiClusterNodeEvent|BaiClusterNodeEvent|No|
|BaiJobEvent|BaiJobEvent|Não|


## <a name="microsoftblockchainblockchainmembers"></a>Microsoft.Blockchain/blockchainMembers

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|BlockchainApplication|Aplicativo Blockchain|No|
|FabricOrderer|Solicitante de malha|No|
|FabricPeer|Ponto de malha|No|
|Proxy|Proxy|No|


## <a name="microsoftblockchaincordamembers"></a>Microsoft. Blockchain/cordaMembers

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|BlockchainApplication|Aplicativo Blockchain|No|


## <a name="microsoftbotservicebotservices"></a>microsoft.botservice/botservices

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|BotRequest|Solicitações dos canais para o bot|No|
|DependencyRequest|Solicitações para dependências|No|


## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Microsoft. CDN/cdnwebapplicationfirewallpolicies

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|WebApplicationFirewallLogs|Logs de firewall de aplicação Web|No|


## <a name="microsoftcdnprofiles"></a>Microsoft.Cdn/profiles

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|AzureCdnAccessLog|Log de acesso da CDN do Azure|No|
|FrontDoorAccessLog|Log de acesso do FrontDoor|Yes|
|FrontDoorHealthProbeLog|Log de investigação de integridade do FrontDoor|Yes|
|FrontDoorWebApplicationFirewallLog|Log do FrontDoor WebApplicationFirewall|Yes|


## <a name="microsoftcdnprofilesendpoints"></a>Microsoft.Cdn/profiles/endpoints

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|CoreAnalytics|Obtém as métricas do ponto de extremidade, como largura de banda, saída etc.|No|


## <a name="microsoftclassicnetworknetworksecuritygroups"></a>Microsoft.ClassicNetwork/networksecuritygroups

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|Evento de fluxo de regra de grupo de segurança de rede|Evento de fluxo de regra de grupo de segurança de rede|Não|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|Audit|Logs de Auditoria|No|
|RequestResponse|Logs de Solicitação e Resposta|No|
|Trace|Logs de rastreamento|No|


## <a name="microsoftcommunicationcommunicationservices"></a>Microsoft. Communication/Communications

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|ChatOperational|Logs de chat operacionais|No|
|SMSOperational|Logs de SMS operacionais|No|
|Uso|Registros de uso|Não|


## <a name="microsoftcontainerregistryregistries"></a>Microsoft.ContainerRegistry/registries

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|ContainerRegistryLoginEvents|Eventos de logon|No|
|ContainerRegistryRepositoryEvents|Logs do RepositoryEvent|Não|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|cluster-autoscaler|Dimensionador automático de cluster do Kubernetes|No|
|guard|guard|No|
|kube-apiserver|Servidor de API do Kubernetes|No|
|Kube-auditoria|Kubernetes auditoria|No|
|Kube-Audit-admin|Logs do administrador de auditoria do kubernetes|No|
|kube-controller-manager|Gerenciador do Controlador do Kubernetes|No|
|kube-scheduler|Agendador do Kubernetes|No|


## <a name="microsoftcustomprovidersresourceproviders"></a>Microsoft. CustomProviders/resourceproviders

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|AuditLogs|Logs de auditoria para chamadas MiniRP|No|


## <a name="microsoftd365customerinsightsinstances"></a>Microsoft. D365CustomerInsights/instâncias

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|Audit|Eventos de auditoria|No|
|Operacional|Eventos operacionais|No|


## <a name="microsoftdatabricksworkspaces"></a>Microsoft. databricks/espaços de trabalho

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|accounts|Contas do databricks|Não|
|clusters|Clusters do databricks|No|
|dBFS|Sistema de arquivos do Databricks|No|
|instancePools|Pools de instância|Não|
|jobs|Trabalhos do databricks|No|
|notebook|Databricks Notebook|Não|
|segredos|Segredos do databricks|No|
|sqlpermissions|Sqlpermissões do databricks|No|
|ssh|SSH do databricks|No|
|workspace|Espaço de trabalho do databricks|No|


## <a name="microsoftdatacollaborationworkspaces"></a>Microsoft. datacollaboration/Workspaces

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|CollaborationAudit|Auditoria de colaboração|Yes|
|Dataassets|Ativos de dados|No|
|Pipelines|Pipelines|No|
|Propostas|Propostas|No|
|Scripts|Scripts|Não|


## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/factories

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|ActivityRuns|Registro de execuções de atividade de pipeline|No|
|PipelineRuns|Registro de execuções de pipeline|No|
|SSISIntegrationRuntimeLogs|Logs do SSIS Integration Runtime|No|
|SSISPackageEventMessageContext|Contexto de mensagem de evento do pacote SSIS|No|
|SSISPackageEventMessages|Mensagens de evento de pacote SSIS|No|
|SSISPackageExecutableStatistics|Estatísticas de executável do pacote SSIS|No|
|SSISPackageExecutionComponentPhases|Fases do componente de execução de pacote SSIS|No|
|SSISPackageExecutionDataStatistics|Estatísticas de dados de exeution do pacote SSIS|No|
|TriggerRuns|Registro de execuções de gatilho|Não|


## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/accounts

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|Audit|Logs de Auditoria|No|
|Requests|Logs de solicitação|Não|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|Audit|Logs de Auditoria|No|
|Requests|Logs de solicitação|No|


## <a name="microsoftdatashareaccounts"></a>Microsoft.DataShare/accounts

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|ReceivedShareSnapshots|Instantâneos de compartilhamento recebidos|No|
|SentShareSnapshots|Instantâneos de compartilhamento enviados|No|
|Compartilhamentos|Compartilhamentos|No|
|ShareSubscriptions|Compartilhar assinaturas|No|


## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|MySqlAuditLogs|Logs de auditoria do MariaDB|No|
|MySqlSlowLogs|Logs do servidor MariaDB|No|


## <a name="microsoftdbformysqlflexibleservers"></a>Microsoft.DBforMySQL/flexibleServers

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|MySqlAuditLogs|Logs de auditoria do MySQL|No|
|MySqlSlowLogs|Logs lentos do MySQL|No|


## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|MySqlAuditLogs|Logs de auditoria do MySQL|No|
|MySqlSlowLogs|Logs do MySQL Server|No|


## <a name="microsoftdbforpostgresqlflexibleservers"></a>Microsoft.DBforPostgreSQL/flexibleServers

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|PostgreSQLLogs|Logs do PostgreSQL Server|Não|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|PostgreSQLLogs|Logs do PostgreSQL Server|No|
|QueryStoreRuntimeStatistics|As estatísticas de tempo de execução Repositório de Consultas PostgreSQL|No|
|QueryStoreWaitStatistics|Estatísticas de espera Repositório de Consultas PostgreSQL|Não|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft.DBforPostgreSQL/serversv2

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|PostgreSQLLogs|Logs do PostgreSQL Server|No|


## <a name="microsoftdesktopvirtualizationapplicationgroups"></a>Microsoft. DesktopVirtualization/applicationgroups

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|Ponto de verificação|Ponto de verificação|No|
|Erro|Erro|No|
|Gerenciamento|Gerenciamento|No|


## <a name="microsoftdesktopvirtualizationhostpools"></a>Microsoft. DesktopVirtualization/hostpools

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|AgentHealthStatus|AgentHealthStatus|No|
|Ponto de verificação|Ponto de verificação|No|
|Conexão|Conexão|No|
|Erro|Erro|No|
|HostRegistration|HostRegistration|No|
|Gerenciamento|Gerenciamento|No|


## <a name="microsoftdesktopvirtualizationworkspaces"></a>Microsoft. DesktopVirtualization/Workspaces

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|Ponto de verificação|Ponto de verificação|No|
|Erro|Erro|No|
|Feed|Feed|No|
|Gerenciamento|Gerenciamento|No|


## <a name="microsoftdeviceselasticpoolsiothubtenants"></a>Microsoft.Devices/ElasticPools/IotHubTenants

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|C2DCommands|Comandos C2C|No|
|C2CTwinOperations|Operações de Gêmeos C2D|No|
|Configurações|Configurações|No|
|conexões|conexões|No|
|D2CTwinOperations|D2CTwinOperations|No|
|DeviceIdentityOperations|Operações de Identidade do Dispositivo|No|
|DeviceStreams|Fluxos de dispositivo (visualização)|No|
|DeviceTelemetry|Telemetria de Dispositivo|No|
|DirectMethods|Métodos diretos|No|
|DistributedTracing|Rastreamento Distribuído (Versão Prévia)|No|
|FileUploadOperations|Operações de Upload de Arquivo|No|
|JobsOperations|Operações de Trabalhos|No|
|Rotas|Rotas|No|
|TwinQueries|Consultas de Gêmeos|Não|


## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|C2DCommands|Comandos C2C|No|
|C2CTwinOperations|Operações de Gêmeos C2D|No|
|Configurações|Configurações|No|
|conexões|conexões|No|
|D2CTwinOperations|D2CTwinOperations|No|
|DeviceIdentityOperations|Operações de Identidade do Dispositivo|No|
|DeviceStreams|Fluxos de dispositivo (visualização)|No|
|DeviceTelemetry|Telemetria de Dispositivo|No|
|DirectMethods|Métodos diretos|No|
|DistributedTracing|Rastreamento Distribuído (Versão Prévia)|No|
|FileUploadOperations|Operações de Upload de Arquivo|No|
|JobsOperations|Operações de Trabalhos|No|
|Rotas|Rotas|No|
|TwinQueries|Consultas de Gêmeos|No|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|DeviceOperations|Operações do Dispositivo|No|
|ServiceOperations|Operações de serviço|No|


## <a name="microsoftdigitaltwinsdigitaltwinsinstances"></a>Microsoft. DigitalTwins/digitalTwinsInstances

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|DigitalTwinsOperation|DigitalTwinsOperation|No|
|EventRoutesOperation|EventRoutesOperation|No|
|ModelsOperation|ModelsOperation|No|
|QueryOperation|QueryOperation|Não|


## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|CassandraRequests|CassandraRequests|No|
|ControlPlaneRequests|ControlPlaneRequests|No|
|DataPlaneRequests|DataPlaneRequests|No|
|GremlinRequests|GremlinRequests|No|
|MongoRequests|MongoRequests|No|
|PartitionKeyRUConsumption|PartitionKeyRUConsumption|No|
|PartitionKeyStatistics|PartitionKeyStatistics|No|
|QueryRuntimeStatistics|QueryRuntimeStatistics|Não|


## <a name="microsofteventgriddomains"></a>Microsoft.EventGrid/domains

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|DeliveryFailures|Logs de falha de entrega|No|
|PublishFailures|Publicar logs de falha|No|


## <a name="microsofteventgridpartnernamespaces"></a>Microsoft. EventGrid/partnerNamespaces

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|DeliveryFailures|Logs de falha de entrega|No|
|PublishFailures|Publicar logs de falha|No|


## <a name="microsofteventgridpartnertopics"></a>Microsoft. EventGrid/partnerTopics

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|DeliveryFailures|Logs de falha de entrega|No|


## <a name="microsofteventgridsystemtopics"></a>Microsoft. EventGrid/systemTopics

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|DeliveryFailures|Logs de falha de entrega|No|


## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|DeliveryFailures|Logs de falha de entrega|No|
|PublishFailures|Publicar logs de falha|Não|


## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|ArchiveLogs|Logs de arquivo|No|
|AutoScaleLogs|Logs de Escala Automática|No|
|CustomerManagedKeyUserLogs|Logs de chave gerenciado pelo cliente|No|
|EventHubVNetConnectionEvent|Logs de conexão de filtragem de VNet/IP|No|
|KafkaCoordinatorLogs|Logs do coordenador de Kafka|No|
|KafkaUserErrorLogs|Logs de erros do usuário Kafka|No|
|OperationalLogs|Logs operacionais|No|


## <a name="microsoftexperimentationexperimentworkspaces"></a>Microsoft. Experimentation/experimentWorkspaces

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|Solicitação|Solicitação|No|


## <a name="microsofthealthcareapisservices"></a>Microsoft.HealthcareApis/services

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|AuditLogs|Logs de auditoria|No|


## <a name="microsoftinsightsautoscalesettings"></a>microsoft.insights/autoscalesettings

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|AutoscaleEvaluations|Avaliações de Dimensionamento Automático|No|
|AutoscaleScaleActions|Ações de Dimensionamento Automático|Não|


## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Components

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|AppAvailabilityResults|Resultados da disponibilidade|No|
|AppBrowserTimings|Tempos do navegador|No|
|AppDependencies|Dependências|No|
|AppEvents|Eventos|No|
|AppExceptions|Exceções|No|
|AppMetrics|Métricas|No|
|AppPageViews|Exibições de página|No|
|AppPerformanceCounters|Contadores de desempenho|No|
|AppRequests|Requests|No|
|AppSystemEvents|Eventos do sistema|No|
|AppTraces|Rastreamentos|No|


## <a name="microsoftiotspacesgraph"></a>Microsoft.IoTSpaces/Graph

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|Audit|Audit|No|
|Saída|Saída|No|
|Entrada|Entrada|No|
|Operacional|Operacional|No|
|Trace|Trace|No|
|UserDefinedFunction|UserDefinedFunction|No|


## <a name="microsoftkeyvaultmanagedhsms"></a>Microsoft. keyvault/managedhsms

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|AuditEvent|Auditar evento|Não|


## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|AuditEvent|Logs de Auditoria|No|


## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Clusters

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|Comando|Comando|No|
|FailedIngestion|Operações de ingestão com falha|No|
|IngestionBatching|Envio em lote de ingestão|No|
|Consulta|Consulta|No|
|SucceededIngestion|Operações de ingestão com êxito|No|
|TableDetails|Detalhes da tabela|No|
|TableUsageStatistics|Estatísticas de uso de tabela|No|


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
|AmlComputeClusterEvent|AmlComputeClusterEvent|No|
|AmlComputeClusterNodeEvent|AmlComputeClusterNodeEvent|No|
|AmlComputeCpuGpuUtilization|AmlComputeCpuGpuUtilization|No|
|AmlComputeJobEvent|AmlComputeJobEvent|No|
|AmlRunStatusChangedEvent|AmlRunStatusChangedEvent|Não|


## <a name="microsoftmediamediaservices"></a>Microsoft.Media/mediaservices

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|KeyDeliveryRequests|Solicitações de entrega de chave|Não|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|ApplicationGatewayAccessLog|Log de acesso do Gateway de Aplicativo|No|
|ApplicationGatewayFirewallLog|Log de firewall do Gateway de Aplicativo|No|
|ApplicationGatewayPerformanceLog|Log de desempenho do Gateway de Aplicativo|Não|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|AzureFirewallApplicationRule|Regra de Aplicativo de Firewall do Azure|No|
|AzureFirewallDnsProxy|Proxy de DNS do firewall do Azure|No|
|AzureFirewallNetworkRule|Regra de Rede de Firewall do Azure|No|


## <a name="microsoftnetworkbastionhosts"></a>Microsoft.Network/bastionHosts

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|BastionAuditLogs|Logs de auditoria de bastiões|Não|


## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|PeeringRouteLog|Logs de Tabela de Rota de Emparelhamento|No|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/frontdoors

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|FrontdoorAccessLog|Log de acesso do Frontdoor|No|
|FrontdoorWebApplicationFirewallLog|Log de firewall do aplicativo Web Frontdoor|Não|


## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|LoadBalancerAlertEvent|Eventos de alerta do Load Balancer|No|
|LoadBalancerProbeHealthStatus|Status de integridade da investigação do Load Balancer|No|


## <a name="microsoftnetworknetworksecuritygroups"></a>Microsoft.Network/networksecuritygroups

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|NetworkSecurityGroupEvent|Network Security Group Event|No|
|NetworkSecurityGroupFlowEvent|Evento de fluxo de regra de grupo de segurança de rede|No|
|NetworkSecurityGroupRuleCounter|Contador de regras de grupo de segurança de rede|No|


## <a name="microsoftnetworkp2svpngateways"></a>Microsoft. Network/p2sVpnGateways

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|GatewayDiagnosticLog|Logs de Diagnóstico de Gateway|No|
|IKEDiagnosticLog|Logs de Diagnóstico IKE|No|
|P2SDiagnosticLog|Logs de Diagnóstico P2S|Não|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|DDoSMitigationFlowLogs|Logs de fluxo de decisões de mitigação de DDoS|No|
|DDoSMitigationReports|Relatórios de mitigações de DDoS|No|
|DDoSProtectionNotifications|Notificações de proteção contra DDoS|Não|


## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|ProbeHealthStatusEvents|Evento de Resultados de Integridade de Investigação do Gerenciador de Tráfego|Não|


## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|GatewayDiagnosticLog|Logs de Diagnóstico de Gateway|No|
|IKEDiagnosticLog|Logs de Diagnóstico IKE|No|
|P2SDiagnosticLog|Logs de Diagnóstico P2S|No|
|RouteDiagnosticLog|Logs de Diagnóstico de Rota|No|
|TunnelDiagnosticLog|Logs de Diagnóstico de Túnel|Não|


## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft.Network/virtualNetworks

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|VMProtectionAlerts|Alertas de proteção da VM|No|


## <a name="microsoftnetworkvpngateways"></a>Microsoft. Network/vpnGateways

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|GatewayDiagnosticLog|Logs de Diagnóstico de Gateway|No|
|IKEDiagnosticLog|Logs de Diagnóstico IKE|No|
|RouteDiagnosticLog|Logs de Diagnóstico de Rota|No|
|TunnelDiagnosticLog|Logs de Diagnóstico de Túnel|No|


## <a name="microsoftnotificationhubsnamespaces"></a>Microsoft.NotificationHubs/namespaces

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|OperationalLogs|Logs operacionais|Não|


## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.OperationalInsights/workspaces

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|Audit|Logs de Auditoria|No|


## <a name="microsoftpowerbitenants"></a>Microsoft. PowerBI/locatários

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|Mecanismo|Mecanismo|No|


## <a name="microsoftpowerbitenantsworkspaces"></a>Microsoft. PowerBI/locatários/espaços de trabalho

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|Mecanismo|Mecanismo|Não|


## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|Mecanismo|Mecanismo|No|


## <a name="microsoftprojectbabylonaccounts"></a>Microsoft. ProjectBabylon/accounts

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|ScanStatusLogEvent|ScanStatus|No|


## <a name="microsoftpurviewaccounts"></a>Microsoft. alcance/accounts

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|ScanStatusLogEvent|ScanStatus|No|


## <a name="microsoftrecoveryservicesvaults"></a>Microsoft.RecoveryServices/Vaults

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|AddonAzureBackupAlerts|Dados de alerta de backup do Azure addon|No|
|AddonAzureBackupJobs|Dados do trabalho de backup do Azure addon|No|
|AddonAzureBackupPolicy|Dados de política de backup do Azure addon|No|
|AddonAzureBackupProtectedInstance|Dados de instância protegida do backup do Azure addon|No|
|AddonAzureBackupStorage|Dados de armazenamento de backup do Azure addon|No|
|AzureBackupReport|Dados de relatórios de backup do Azure|No|
|AzureSiteRecoveryEvents|Eventos do Azure Site Recovery|No|
|AzureSiteRecoveryJobs|Trabalhos do Azure Site Recovery|No|
|AzureSiteRecoveryProtectedDiskDataChurn|Rotatividade de Dados de Disco Protegido do Azure Site Recovery|No|
|AzureSiteRecoveryRecoveryPoints|Pontos de Recuperação do Azure Site Recovery|No|
|AzureSiteRecoveryReplicatedItems|Itens replicados do Azure Site Recovery|No|
|AzureSiteRecoveryReplicationDataUploadRate|Taxa de Carregamento de Dados de Replicação do Azure Site Recovery|No|
|AzureSiteRecoveryReplicationStats|Estatísticas de Replicação do Azure Site Recovery|No|
|CoreAzureBackup|Principais dados de backup do Azure|Não|


## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/namespaces

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|HybridConnectionsEvent|Eventos de HybridConnections|No|
|HybridConnectionsLogs|HybridConnectionsLogs|Não|


## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|OperationLogs|Logs de operação|Não|


## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|OperationalLogs|Logs operacionais|No|


## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/SignalR

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|AllLogs|Logs do serviço de Signaler do Azure.|Não|


## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|DevOpsOperationsAudit|Logs de auditoria de operações do DevOps|No|
|ResourceUsageStats|Estatísticas de uso de recursos|No|
|SQLSecurityAuditEvents|Evento de Auditoria de Segurança do SQL|No|


## <a name="microsoftsqlmanagedinstancesdatabases"></a>Microsoft.Sql/managedInstances/databases

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|Errors|Errors|No|
|QueryStoreRuntimeStatistics|Estatísticas de Runtime do Repositório de Consultas|No|
|QueryStoreWaitStatistics|Estatísticas de Espera do Repositório de Consultas|No|
|SQLInsights|Insights do SQL|Não|


## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|AutomaticTuning|Ajuste automático|No|
|Blocos|Blocos|No|
|DatabaseWaitStatistics|Estatísticas de Espera do Banco de Dados|No|
|Deadlocks|Deadlocks|No|
|DevOpsOperationsAudit|Logs de auditoria de operações do DevOps|No|
|DmsWorkers|Trabalhos DMS|No|
|Errors|Errors|No|
|ExecRequests|Solicitações de Exec|No|
|QueryStoreRuntimeStatistics|Estatísticas de Runtime do Repositório de Consultas|No|
|QueryStoreWaitStatistics|Estatísticas de Espera do Repositório de Consultas|No|
|RequestSteps|Etapas de solicitação|No|
|SQLInsights|Insights do SQL|No|
|SqlRequests|Solicitações de Sql|No|
|SQLSecurityAuditEvents|Evento de Auditoria de Segurança do SQL|No|
|Tempos limite|Tempos limite|No|
|Esperas|Esperas|Não|


## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|StorageDelete|StorageDelete|Yes|
|StorageRead|StorageRead|Yes|
|StorageWrite|StorageWrite|Yes|


## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|StorageDelete|StorageDelete|Yes|
|StorageRead|StorageRead|Yes|
|StorageWrite|StorageWrite|Yes|


## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|StorageDelete|StorageDelete|Yes|
|StorageRead|StorageRead|Yes|
|StorageWrite|StorageWrite|Yes|


## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|StorageDelete|StorageDelete|Yes|
|StorageRead|StorageRead|Yes|
|StorageWrite|StorageWrite|Yes|


## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|Criação|Criação|No|
|Execução|Execução|No|


## <a name="microsoftsynapseworkspaces"></a>Microsoft.Synapse/workspaces

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|BuiltinSqlReqsEnded|Solicitações de pool de SQL internas encerradas|No|
|GatewayApiRequests|Solicitações de API de gateway Synapse|No|
|SQLSecurityAuditEvents|Evento de Auditoria de Segurança do SQL|No|
|SynapseRbacOperations|Operações de RBAC Synapse|No|


## <a name="microsoftsynapseworkspacesbigdatapools"></a>Microsoft. Synapse/Workspaces/bigDataPools

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|BigDataPoolAppsEnded|Aplicativos de pool de Big data encerrados|No|


## <a name="microsoftsynapseworkspacessqlpools"></a>Microsoft. Synapse/espaços de trabalho/sqlpools

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|DmsWorkers|Trabalhos DMS|No|
|ExecRequests|Solicitações de Exec|No|
|RequestSteps|Etapas de solicitação|No|
|SqlRequests|Solicitações de Sql|No|
|SQLSecurityAuditEvents|Evento de auditoria de segurança do SQL|No|
|Esperas|Esperas|No|


## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft.TimeSeriesInsights/environments

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|Entrada|Entrada|No|
|Gerenciamento|Gerenciamento|No|


## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft.TimeSeriesInsights/environments/eventsources

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|Entrada|Entrada|No|
|Gerenciamento|Gerenciamento|No|


## <a name="microsoftwebhostingenvironments"></a>microsoft.web/hostingenvironments

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|AppServiceEnvironmentPlatformLogs|Ambiente do Serviço de Aplicativondo logs da plataforma|No|


## <a name="microsoftwebsites"></a>microsoft.web/sites

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|AppServiceAntivirusScanAuditLogs|Relatar logs de auditoria de antivírus|No|
|AppServiceAppLogs|Logs de aplicativo do serviço de aplicativo|No|
|AppServiceAuditLogs|Acessar logs de auditoria|No|
|AppServiceConsoleLogs|Logs do console do serviço de aplicativo|No|
|AppServiceFileAuditLogs|Logs de auditoria de alteração de conteúdo do site|No|
|AppServiceHTTPLogs|Logs de HTTP|No|
|AppServiceIPSecAuditLogs|Logs de auditoria do IPSecurity|No|
|AppServicePlatformLogs|Logs da plataforma do serviço de aplicativo|No|
|FunctionAppLogs|Logs do aplicativo de funções|No|


## <a name="microsoftwebsitesslots"></a>microsoft.web/sites/slots

|Categoria|Nome de exibição da categoria|Custos para exportar|
|---|---|---|
|AppServiceAntivirusScanAuditLogs|Relatar logs de auditoria de antivírus|No|
|AppServiceAppLogs|Logs de aplicativo do serviço de aplicativo|No|
|AppServiceAuditLogs|Acessar logs de auditoria|No|
|AppServiceConsoleLogs|Logs do console do serviço de aplicativo|No|
|AppServiceFileAuditLogs|Logs de auditoria de alteração de conteúdo do site|No|
|AppServiceHTTPLogs|Logs de HTTP|No|
|AppServiceIPSecAuditLogs|Logs de auditoria do IPSecurity|No|
|AppServicePlatformLogs|Logs da plataforma do serviço de aplicativo|No|
|FunctionAppLogs|Logs do aplicativo de funções|No|



## <a name="next-steps"></a>Próximas etapas

* [Saiba mais sobre os logs de recursos](./platform-logs-overview.md)
* [Transmitir logs de recurso de recurso para **hubs de eventos**](./resource-logs.md#send-to-azure-event-hubs)
* [Alterar as configurações de diagnóstico do log de recursos usando a API REST do Azure Monitor](/rest/api/monitor/diagnosticsettings)
* [Analisar logs do Armazenamento do Azure com o Log Analytics](./resource-logs.md#send-to-log-analytics-workspace)

