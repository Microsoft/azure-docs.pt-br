---
title: Provedores de recursos pelos serviços do Azure
description: Lista todos os namespaces do provedor de recursos para Azure Resource Manager e mostra o serviço do Azure para esse namespace.
ms.topic: conceptual
ms.date: 12/01/2020
ms.openlocfilehash: 65fa6a690f05a61e54bae2d22f4889c3193bcb1a
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103008698"
---
# <a name="resource-providers-for-azure-services"></a>Provedores de recursos para serviços do Azure

Este artigo mostra como os namespaces do provedor de recursos são mapeados para os serviços do Azure.

## <a name="match-resource-provider-to-service"></a>Corresponder provedor de recursos ao serviço

Os provedores de recursos marcados com **-Registered** são registrados por padrão para sua assinatura. Para obter mais informações, consulte [Registration](#registration).

| Namespace do provedor de recursos | Serviço do Azure |
| --------------------------- | ------------- |
| Microsoft.AAD | [Serviços de Domínio do Active Directory do Azure](../../active-directory-domain-services/index.yml) |
| Microsoft.Addons | core |
| Microsoft. ADHybridHealthService- [registrado](#registration) | [Azure Active Directory](../../active-directory/index.yml) |
| Microsoft.Advisor | [Assistente do Azure](../../advisor/index.yml) |
| Microsoft.AlertsManagement | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft.AnalysisServices | [Azure Analysis Services](../../analysis-services/index.yml) |
| Microsoft.ApiManagement | [Gerenciamento da API](../../api-management/index.yml) |
| Microsoft.AppConfiguration | [Configuração de Aplicativos do Azure](../../azure-app-configuration/index.yml) |
| Microsoft.AppPlatform | [Azure Spring Cloud](../../spring-cloud/spring-cloud-overview.md) |
| Microsoft.Attestation | Serviço de atestado do Azure |
| Microsoft. Authorization- [registrado](#registration) | [Azure Resource Manager](../index.yml) |
| Microsoft.Automation | [Automação](../../automation/index.yml) |
| Microsoft. AutonomousSystems | [Sistemas autônomos](https://www.microsoft.com/ai/autonomous-systems) |
| Microsoft.AVS | [Solução VMware no Azure](../../azure-vmware/index.yml) |
| Microsoft.AzureActiveDirectory | [Azure Active Directory B2C](../../active-directory-b2c/index.yml) |
| Microsoft. AzureArcData | Serviços de Dados habilitados para Azure Arc |
| Microsoft.AzureData | Registro de SQL Server |
| Microsoft.AzureStack | core |
| Microsoft.AzureStackHCI | [Azure Stack HCI](/azure-stack/hci/overview) |
| Microsoft.Batch | [Batch](../../batch/index.yml) |
| Microsoft. billing- [registrado](#registration) | [Gerenciamento de Custos e Cobrança](/azure/billing/) |
| Microsoft.BingMaps | [Bing Mapas](/BingMaps/#pivot=main&panel=BingMapsAPI) |
| Microsoft.Blockchain | [Azure Blockchain Service](../../blockchain/workbench/index.yml) |
| Microsoft.BlockchainTokens | [Tokens do Azure Blockchain](https://azure.microsoft.com/services/blockchain-tokens/) |
| Microsoft.Blueprint | [Azure Blueprints](../../governance/blueprints/index.yml) |
| Microsoft.BotService | [Serviço de Bot do Azure](/azure/bot-service/) |
| Microsoft.Cache | [Cache Redis do Azure](../../azure-cache-for-redis/index.yml) |
| Microsoft.Capacity | core |
| Microsoft.Cdn | [Rede de Distribuição de Conteúdo](../../cdn/index.yml) |
| Microsoft.CertificateRegistration | [Certificados do serviço de aplicativo](../../app-service/configure-ssl-certificate.md#import-an-app-service-certificate) |
| Microsoft.ChangeAnalysis | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft.ClassicCompute | Máquina virtual do modelo de implantação clássico |
| Microsoft.ClassicInfrastructureMigrate | Migração do modelo de implantação clássico |
| Microsoft.ClassicNetwork | Rede virtual do modelo de implantação clássico |
| Microsoft.ClassicStorage | Armazenamento de modelo de implantação clássico |
| Microsoft. ClassicSubscription- [registrado](#registration) | Modelo de implantação clássica |
| Microsoft.CognitiveServices | [Serviços Cognitivos](../../cognitive-services/index.yml) |
| Microsoft. Commerce- [registrado](#registration) | core |
| Microsoft.Compute | [Máquinas virtuais](../../virtual-machines/index.yml)<br />[Conjuntos de Escala de Máquina Virtual](../../virtual-machine-scale-sets/index.yml) |
| Microsoft. consumo- [registrado](#registration) | [Gerenciamento de Custos](/azure/cost-management/) |
| Microsoft.ContainerInstance | [Instâncias de Contêiner](../../container-instances/index.yml) |
| Microsoft.ContainerRegistry | [Registro de Contêiner](../../container-registry/index.yml) |
| Microsoft.ContainerService | [AKS (Serviço de Kubernetes do Azure)](../../aks/index.yml) |
| Microsoft. CostManagement- [registrado](#registration) | [Gerenciamento de Custos](/azure/cost-management/) |
| Microsoft.CostManagementExports | [Gerenciamento de Custos](/azure/cost-management/) |
| Microsoft.CustomerLockbox | [Sistema de Proteção de Dados do Cliente para Microsoft Azure](../../security/fundamentals/customer-lockbox-overview.md) |
| Microsoft.CustomProviders | [Provedores Personalizados do Azure](../custom-providers/overview.md) |
| Microsoft.DataBox | [Azure Data Box](../../databox/index.yml) |
| Microsoft.DataBoxEdge | [Azure Stack Edge](../../databox-online/azure-stack-edge-overview.md) |
| Microsoft.Databricks | [Azure Databricks](/azure/azure-databricks/) |
| Microsoft.DataCatalog | [Catálogo de Dados](../../data-catalog/index.yml) |
| Microsoft.DataFactory | [Data Factory](../../data-factory/index.yml) |
| Microsoft.DataLakeAnalytics | [Data Lake Analytics](../../data-lake-analytics/index.yml) |
| Microsoft.DataLakeStore | [Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-introduction.md) |
| Microsoft.DataMigration | [Serviço de Migração de Banco de Dados do Azure](../../dms/index.yml) |
| Microsoft.DataProtection | Proteção de dados |
| Microsoft.DataShare | [Azure Data Share](../../data-share/index.yml) |
| Microsoft.DBforMariaDB | [Banco de Dados do Azure para MariaDB](../../mariadb/index.yml) |
| Microsoft.DBforMySQL | [Banco de Dados do Azure para MySQL](../../mysql/index.yml) |
| Microsoft.DBforPostgreSQL | [Banco de Dados do Azure para PostgreSQL](../../postgresql/index.yml) |
| Microsoft.DeploymentManager | [Gerenciador de Implantação do Azure](../templates/deployment-manager-overview.md) |
| Microsoft.DesktopVirtualization | [Área de Trabalho Virtual do Windows](../../virtual-desktop/index.yml) |
| Microsoft.Devices | [Hub IoT do Azure](../../iot-hub/index.yml)<br />[Serviço de Provisionamento de Dispositivos no Hub IoT do Azure](../../iot-dps/index.yml) |
| Microsoft.DevOps | [Azure DevOps](/azure/devops/) |
| Microsoft.DevSpaces | [Azure Dev Spaces](../../dev-spaces/index.yml) |
| Microsoft.DevTestLab | [Azure Lab Services](../../lab-services/index.yml) |
| Microsoft.DigitalTwins | [Gêmeos Digitais do Azure](../../digital-twins/overview.md) |
| Microsoft.DocumentDB | [Azure Cosmos DB](../../cosmos-db/index.yml) |
| Microsoft.DomainRegistration | [Serviço de Aplicativo](../../app-service/index.yml) |
| Microsoft.DynamicsLcs | [Serviços de ciclo de vida](https://lcs.dynamics.com/Logon/Index ) |
| Microsoft.EnterpriseKnowledgeGraph | Grafo de conhecimento corporativo |
| Microsoft.EventGrid | [Grade de Eventos](../../event-grid/index.yml) |
| Microsoft.EventHub | [Hubs de Evento](../../event-hubs/index.yml) |
| Microsoft. Features – [registrado](#registration) | [Azure Resource Manager](../index.yml) |
| Microsoft.GuestConfiguration | [Azure Policy](../../governance/policy/index.yml) |
| Microsoft.HanaOnAzure | [SAP HANA em Instâncias Grandes do Azure](../../virtual-machines/workloads/sap/hana-overview-architecture.md) |
| Microsoft.HardwareSecurityModules | [HSM Dedicado do Azure](../../dedicated-hsm/index.yml) |
| Microsoft.HDInsight | [HDInsight](../../hdinsight/index.yml) |
| Microsoft.HealthcareApis | [API do Azure para FHIR](../../healthcare-apis/fhir/index.yml) |
| Microsoft.HybridCompute | [Azure Arc](../../azure-arc/index.yml) |
| Microsoft.HybridData | [StorSimple](../../storsimple/index.yml) |
| Microsoft. HybridNetwork  | [Zonas de borda privada](../../networking/edge-zones-overview.md) |
| Microsoft.ImportExport | [Importação/Exportação do Azure](../../import-export/storage-import-export-service.md) |
| microsoft.insights | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft.IoTCentral | [Azure IoT Central](../../iot-central/index.yml) |
| Microsoft.IoTSpaces | [Gêmeos Digitais do Azure](../../digital-twins/index.yml) |
| Microsoft.Intune | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft.KeyVault | [Key Vault](../../key-vault/index.yml) |
| Microsoft.Kubernetes | [AKS (Serviço de Kubernetes do Azure)](../../aks/index.yml) |
| Microsoft.KubernetesConfiguration | [AKS (Serviço de Kubernetes do Azure)](../../aks/index.yml) |
| Microsoft.Kusto | [Azure Data Explorer](/azure/data-explorer/) |
| Microsoft.LabServices | [Azure Lab Services](../../lab-services/index.yml) |
| Microsoft.Logic | [Aplicativos Lógicos](../../logic-apps/index.yml) |
| Microsoft.MachineLearning | [Machine Learning Studio](../../machine-learning/classic/index.yml) |
| Microsoft.MachineLearningServices | [Azure Machine Learning](../../machine-learning/index.yml) |
| Microsoft.Maintenance | [Manutenção do Azure](../../virtual-machines/maintenance-control-cli.md) |
| Microsoft.ManagedIdentity | [Identidades gerenciadas para os recursos do Azure](../../active-directory/managed-identities-azure-resources/index.yml) |
| Microsoft.ManagedNetwork | Redes virtuais gerenciadas pelos serviços de PaaS |
| Microsoft.ManagedServices | [Azure Lighthouse](../../lighthouse/index.yml) |
| Microsoft.Management | [Grupos de Gerenciamento](../../governance/management-groups/index.yml) |
| Microsoft.Maps | [Mapas do Azure](../../azure-maps/index.yml) |
| Microsoft.Marketplace | core |
| Microsoft.MarketplaceApps | core |
| Microsoft. MarketplaceOrdering- [registrado](#registration) | core |
| Microsoft.Media | [Serviços de Mídia](../../media-services/index.yml) |
| Microsoft.Microservices4Spring | [Azure Spring Cloud](../../spring-cloud/spring-cloud-overview.md) |
| Microsoft.Migrate | [Migrações para Azure](../../migrate/migrate-services-overview.md) |
| Microsoft.MixedReality | [Âncoras Espaciais do Azure](../../spatial-anchors/index.yml) |
| Microsoft.NetApp | [Azure NetApp Files](../../azure-netapp-files/index.yml) |
| Microsoft.Network | [Gateway de Aplicativo](../../application-gateway/index.yml)<br />[Azure Bastion](../../bastion/index.yml)<br />[Proteção contra DDoS do Azure](../../ddos-protection/ddos-protection-overview.md)<br />[DNS do Azure](../../dns/index.yml)<br />[Azure ExpressRoute](../../expressroute/index.yml)<br />[Firewall do Azure](../../firewall/index.yml)<br />[Azure Front Door Service](../../frontdoor/index.yml)<br />[Link Privado do Azure](../../private-link/index.yml)<br />[Balanceador de Carga](../../load-balancer/index.yml)<br />[Observador de Rede](../../network-watcher/index.yml)<br />[Gerenciador de Tráfego](../../traffic-manager/index.yml)<br />[Rede Virtual](../../virtual-network/index.yml)<br />[WAN Virtual](../../virtual-wan/index.yml)<br />[Gateway de VPN](../../vpn-gateway/index.yml)<br /> |
| Microsoft. notebooks | [Azure Notebooks](https://notebooks.azure.com/help/introduction) |
| Microsoft.NotificationHubs | [Hubs de Notificação](../../notification-hubs/index.yml) |
| Microsoft.ObjectStore | Repositório de objetos |
| Microsoft.OffAzure | [Migrações para Azure](../../migrate/migrate-services-overview.md) |
| Microsoft.OperationalInsights | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft.OperationsManagement | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft.Peering | [Serviço de Emparelhamento do Azure](../../peering-service/index.yml) |
| Microsoft.PolicyInsights | [Azure Policy](../../governance/policy/index.yml) |
| Microsoft. Portal- [registrado](#registration) | [Portal do Azure](../../azure-portal/index.yml) |
| Microsoft.PowerBI | [Power BI](/power-bi/power-bi-overview) |
| Microsoft.PowerBIDedicated | [Power BI Embedded](/azure/power-bi-embedded/) |
| Microsoft. PowerPlatform | [Power Platform](/power-platform/) |
| Microsoft.ProjectBabylon | [Catálogo de Dados do Azure](../../data-catalog/overview.md) |
| Microsoft.Quantum | [Quantum do Azure](https://azure.microsoft.com/services/quantum/) |
| Microsoft.RecoveryServices | [Azure Site Recovery](../../site-recovery/index.yml) |
| Microsoft.RedHatOpenShift | [Red Hat OpenShift no Azure](../../virtual-machines/linux/openshift-get-started.md) |
| Microsoft.Relay | [Retransmissão do Azure](../../azure-relay/relay-what-is-it.md) |
| Microsoft. ResourceGraph- [registrado](#registration) | [Gráfico de Recursos do Azure](../../governance/resource-graph/index.yml) |
| Microsoft.ResourceHealth | [Integridade do Serviço do Azure](../../service-health/index.yml) |
| Microsoft. Resources- [registrado](#registration) | [Azure Resource Manager](../index.yml) |
| Microsoft.SaaS | core |
| Microsoft.Scheduler | [Agendador](../../scheduler/index.yml) |
| Microsoft.Search | [Azure Cognitive Search](../../search/index.yml) |
| Microsoft.Security | [Central de segurança](../../security-center/index.yml) |
| Microsoft.SecurityInsights | [Azure Sentinel](../../sentinel/index.yml) |
| Microsoft. SerialConsole- [registrado](#registration) | [Console serial do Azure para Windows](../../virtual-machines/troubleshooting/serial-console-windows.md) |
| Microsoft.ServiceBus | [Barramento de Serviço](/azure/service-bus/) |
| Microsoft.ServiceFabric | [Service Fabric](../../service-fabric/index.yml) |
| Microsoft.ServiceFabricMesh | [Malha do Service Fabric](../../service-fabric-mesh/index.yml) |
| Microsoft.Services | core |
| Microsoft.SignalRService | [Serviço Azure SignalR](../../azure-signalr/index.yml) |
| Microsoft.SoftwarePlan | Licença |
| Microsoft.Solutions | [Aplicativos Gerenciados do Azure](../managed-applications/index.yml) |
| Microsoft.Sql | [Banco de Dados SQL do Azure](../../azure-sql/database/index.yml)<br /> [Instância Gerenciada do SQL do Azure](../../azure-sql/managed-instance/index.yml) <br />[Azure Synapse Analytics](/azure/sql-data-warehouse/) |
| Microsoft.SqlVirtualMachine | [SQL Server em Máquinas Virtuais do Azure](../../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) |
| Microsoft.Storage | [Storage](../../storage/index.yml) |
| Microsoft.StorageCache | [Azure HPC Cache](../../hpc-cache/index.yml) |
| Microsoft.StorageSync | [Storage](../../storage/index.yml) |
| Microsoft.StorSimple | [StorSimple](../../storsimple/index.yml) |
| Microsoft.StreamAnalytics | [Azure Stream Analytics](../../stream-analytics/index.yml) |
| Microsoft.Subscription | core |
| Microsoft. support- [registrado](#registration) | core |
| Microsoft.Synapse | [Azure Synapse Analytics](/azure/sql-data-warehouse/) |
| Microsoft.TimeSeriesInsights | [Azure Time Series Insights](../../time-series-insights/index.yml) |
| Microsoft.Token | Token |
| Microsoft.VirtualMachineImages | [Construtor de Imagens do Azure](../../virtual-machines/image-builder-overview.md) |
| microsoft.visualstudio | [Azure DevOps](/azure/devops/) |
| Microsoft.VMware | [Solução VMware no Azure](../../azure-vmware/index.yml) |
| Microsoft.VMwareCloudSimple | [Solução VMware no Azure pela CloudSimple](../../vmware-cloudsimple/index.md) |
| Microsoft.VSOnline | [Azure DevOps](/azure/devops/) |
| Microsoft.Web | [Serviço de Aplicativo](../../app-service/index.yml)<br />[Azure Functions](../../azure-functions/index.yml) |
| Microsoft.WindowsDefenderATP | [Proteção Avançada contra Ameaças do Microsoft Defender](../../security-center/security-center-wdatp.md) |
| Microsoft.WindowsESU | Atualizações de segurança estendidas |
| Microsoft.WindowsIoT | [Serviços do Windows 10 IoT Core](/windows-hardware/manufacture/iot/iotcoreservicesoverview) |
| Microsoft.WorkloadMonitor | [Azure Monitor](../../azure-monitor/index.yml) |

## <a name="registration"></a>Registro

Os provedores de recursos acima que são marcados com **-registrado** são registrados por padrão para sua assinatura. Para usar os outros provedores de recursos, você deve [registrá-los](resource-providers-and-types.md). No entanto, muitos provedores de recursos são registrados para você quando você executar determinadas ações. Por exemplo, se você criar um recurso por meio do portal, o portal registrará automaticamente todos os provedores de recursos não registrados que forem necessários. Ao implantar recursos por meio de um [modelo de Azure Resource Manager](../templates/overview.md), todos os provedores de recursos necessários também são registrados.

> [!IMPORTANT]
> Somente registre um provedor de recursos quando estiver pronto para usá-lo. A etapa de registro permite que você mantenha os privilégios mínimos em sua assinatura. Um usuário mal-intencionado não pode usar provedores de recursos que não estão registrados.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre provedores de recursos, incluindo como registrar um provedor de recursos, consulte [tipos e provedores de recursos do Azure](resource-providers-and-types.md).