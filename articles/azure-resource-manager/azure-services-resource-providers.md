---
title: Provedores de recursos do Azure Resource Manager por serviços do Azure
description: Lista todos os namespaces do provedor de recursos do Azure Resource Manager e mostra o serviço do Azure para esse namespace.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 06/25/2019
ms.author: tomfitz
ms.openlocfilehash: 5f5ffbd9b1f2bde08023d1758792fab54c2885f7
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442600"
---
# <a name="resource-providers-for-azure-services"></a>Provedores de recursos para serviços do Azure

Este artigo mostra como os namespaces do provedor de recursos são mapeados para os serviços do Azure.

## <a name="match-resource-provider-to-service"></a>Provedor de recursos de correspondência ao serviço

| Namespace do provedor de recursos | Serviço do Azure |
| --------------------------- | ------------- |
| Microsoft.AAD | [Serviços de domínio do Active Directory do Azure](../active-directory-domain-services/index.yml) |
| microsoft.aadiam | [O Azure Active Directory](/azure/active-directory/) |
| Microsoft.Addons | core |
| Microsoft.ADHybridHealthService | [O Azure Active Directory](/azure/active-directory/) |
| Microsoft.Advisor | [Assistente do Azure](../advisor/index.yml) |
| Microsoft.AlertsManagement | [Azure Monitor](../azure-monitor/index.yml) |
| Microsoft.AnalysisServices | [Azure Analysis Services](/azure/analysis-services/) |
| Microsoft.ApiManagement | [Gerenciamento de API](../api-management/index.yml) |
| Microsoft.AppConfiguration | core |
| Microsoft.Authorization | [Azure Resource Manager](index.yml) |
| Microsoft.Automation | [Automação](../automation/index.yml) |
| Microsoft.AzureActiveDirectory | [O Azure Active Directory B2C](../active-directory-b2c/index.yml) |
| Microsoft.AzureStack | core |
| Microsoft.Batch | [Batch](../batch/index.yml) |
| Microsoft.Billing | [Billing](/azure/billing/) |
| Microsoft.BingMaps | [Bing Mapas](https://docs.microsoft.com/BingMaps/#pivot=main&panel=BingMapsAPI) |
| Microsoft.BizTalkServices | [Serviços do BizTalk](../logic-apps/logic-apps-move-from-mabs.md) |
| Microsoft.Blockchain | [Serviço do Azure Blockchain](/azure/blockchain/workbench/) |
| Microsoft.Blueprint | [Especificações técnicas do Azure](/azure/governance/blueprints/) |
| Microsoft.BotService | [Serviço de Bot do Azure](/azure/bot-service/) |
| Microsoft.Cache | [Cache Redis do Azure](/azure/azure-cache-for-redis/) |
| Microsoft.Capacity | core |
| Microsoft.Cdn | [Rede de distribuição de conteúdo](../cdn/index.yml) |
| Microsoft.CertificateRegistration | [Certificados de serviço de aplicativo](../app-service/web-sites-purchase-ssl-web-site.md) |
| Microsoft.ClassicCompute | Máquina de virtual do modelo de implantação clássico |
| Microsoft.ClassicInfrastructureMigrate | Migração de modelo de implantação clássico |
| Microsoft.ClassicNetwork | Rede virtual do modelo de implantação clássico |
| Microsoft.ClassicStorage | Armazenamento de modelo de implantação clássico |
| Microsoft.ClassicSubscription | Modelo de implantação clássica |
| Microsoft.CognitiveServices | [Serviços cognitivos](/azure/cognitive-services/) |
| Microsoft.Commerce | core |
| Microsoft.Compute | [Máquinas virtuais](/azure/virtual-machines/) |
| Microsoft.Consumption | [Gerenciamento de custos](/azure/cost-management/) |
| Microsoft.ContainerInstance | [Instâncias de contêiner](/azure/container-instances/) |
| Microsoft.ContainerRegistry | [Registro de contêiner](/azure/container-registry/) |
| Microsoft.ContainerService | [AKS (Serviço de Kubernetes do Azure)](/azure/aks/) |
| Microsoft.ContentModerator | [Azure Content Moderator](../cognitive-services/content-moderator/index.yml) |
| Microsoft.CostManagement | [Gerenciamento de custos](/azure/cost-management/) |
| Microsoft.CustomerInsights | Customer Insights |
| Microsoft.CustomerLockbox | Cofre do cliente para o Microsoft Azure |
| Microsoft.CustomProviders | [Provedores personalizados do Azure](../managed-applications/custom-providers-overview.md) |
| Microsoft.DataBox | [Azure Data Box](/azure/databox-family/) |
| Microsoft.DataBoxEdge | [Borda da caixa de dados do Azure](../databox-online/data-box-edge-overview.md) |
| Microsoft.Databricks | [O Azure Databricks](/azure/azure-databricks/) |
| Microsoft.DataCatalog | [Catálogo de dados](/azure/data-catalog/) |
| Microsoft.DataFactory | [Data Factory](/azure/data-factory/) |
| Microsoft.DataLakeAnalytics | [Data Lake Analytics](/azure/data-lake-analytics/) |
| Microsoft.DataLakeStore | [Azure Data Lake Store](../storage/blobs/data-lake-storage-introduction.md) |
| Microsoft.DataMigration | [Serviço de migração de banco de dados do Azure](/azure/dms/) |
| Microsoft.DBforMariaDB | [Banco de dados do Azure para MariaDB](/azure/mariadb/) |
| Microsoft.DBforMySQL | [Banco de dados do Azure para MySQL](/azure/mysql/) |
| Microsoft.DBforPostgreSQL | [Azure Database for PostgreSQL](/azure/postgresql/) |
| Microsoft.DeploymentManager | [Azure Deployment Manager](deployment-manager-overview.md) |
| Microsoft.Devices | [Hub IoT](/azure/iot-hub/)<br />[Serviço de Provisionamento de Dispositivos no Hub IoT](/azure/iot-dps/) |
| Microsoft.DevSpaces | [Espaços de desenvolvimento do Azure](/azure/dev-spaces/) |
| Microsoft.DevTestLab | [Azure Lab Services](../lab-services/index.yml) |
| Microsoft.DocumentDB | [Azure Cosmos DB](../cosmos-db/index.yml) |
| Microsoft.DomainRegistration | [Serviço de aplicativo](/azure/app-service/) |
| Microsoft.EnterpriseKnowledgeGraph | Gráfico de Conhecimento da empresa |
| Microsoft.EventGrid | [Grade de eventos](/azure/event-grid/) |
| Microsoft.EventHub | [Hubs de Evento](../event-hubs/index.yml) |
| Microsoft.Features | [O Azure Resource Manager](index.yml) |
| Microsoft.Genomics | [Microsoft Genomics](/azure/genomics/) |
| Microsoft.GuestConfiguration | [Política do Azure](../governance/policy/index.yml) |
| Microsoft.HanaOnAzure | [SAP HANA no Azure](../virtual-machines/workloads/sap/hana-overview-architecture.md) |
| Microsoft.HardwareSecurityModules | [HSM Dedicado do Azure](../dedicated-hsm/index.yml) |
| Microsoft.HDInsight | [HDInsight](../hdinsight/index.yml) |
| Microsoft.HealthcareApis | [API do Azure para FHIR](../healthcare-apis/index.yml) |
| Microsoft.ImportExport | [Importação/exportação do Azure](../storage/common/storage-import-export-service.md) |
| microsoft.insights | [Azure Monitor](../azure-monitor/index.yml) |
| Microsoft.Intune | [Intune](/intune/) |
| Microsoft.IoTCentral | [IoT Central](/azure/iot-central/) |
| Microsoft.IoTSpaces | [Gêmeos Digital do Azure](../digital-twins/index.yml) |
| Microsoft.KeyVault | [Key Vault](../key-vault/index.yml) |
| Microsoft.Kusto | [Gerenciador de dados do Azure](../data-explorer/index.yml) |
| Microsoft.LabServices | [Azure Lab Services](../lab-services/index.yml) |
| Microsoft.LocationBasedServices | [Mapas do Azure](../azure-maps/index.yml) |
| Microsoft.LocationServices | core |
| Microsoft.LogAnalytics | [Azure Monitor](../azure-monitor/index.yml) |
| Microsoft.Logic | [Aplicativos Lógicos](../logic-apps/index.yml) |
| Microsoft.MachineLearning | [Machine Learning Studio](../machine-learning/studio/index.yml) |
| Microsoft.MachineLearningCompute | [Serviço de Machine Learning](../machine-learning/service/index.yml) |
| Microsoft.MachineLearningModelManagement | [Serviço de Machine Learning](../machine-learning/service/index.yml) |
| Microsoft.MachineLearningServices | [Serviço de Machine Learning](../machine-learning/service/index.yml) |
| Microsoft.ManagedIdentity | [Identidades gerenciadas para os recursos do Azure](../active-directory/managed-identities-azure-resources/index.yml) |
| Microsoft.ManagedLab | [Azure Lab Services](../lab-services/index.yml) |
| Microsoft.Management | [Grupos de gerenciamento](/azure/governance/management-groups/) |
| Microsoft.Maps | [Mapas do Azure](../azure-maps/index.yml) |
| Microsoft.Marketplace | core |
| Microsoft.MarketplaceApps | core |
| Microsoft.MarketplaceOrdering | core |
| Microsoft.Media | [Serviços de Mídia](../media-services/index.yml) |
| Microsoft.Migrate | [Migrações para Azure](../migrate/migrate-overview.md) |
| Microsoft.MixedReality | [Âncoras Espaciais do Azure](/azure/spatial-anchors/) |
| Microsoft.NetApp | [Azure NetApp Files](../azure-netapp-files/index.yml) |
| Microsoft.Network | [Rede Virtual](../virtual-network/index.yml)<br />[Balanceador de Carga](../load-balancer/index.yml)<br />[Gateway de Aplicativo](../application-gateway/index.yml)<br />[Azure DNS](../dns/index.yml)<br />[ExpressRoute](../expressroute/index.yml)<br />[Gateway de VPN](../vpn-gateway/index.yml)<br />[Gerenciador de Tráfego](../traffic-manager/index.yml)<br />[Observador de Rede](../network-watcher/index.yml)<br />[Firewall do Azure](../firewall/index.yml)<br />[Serviço do Azure da frente](../frontdoor/index.yml)<br />[Bastiões do Azure](/azure/bastion/) |
| Microsoft.NotificationHubs | [Hubs de Notificação](../notification-hubs/index.yml) |
| Microsoft.OffAzure | [Migrações para Azure](../migrate/migrate-overview.md) |
| Microsoft.OperationalInsights | [Azure Monitor](../azure-monitor/index.yml) |
| Microsoft.OperationsManagement | [Azure Monitor](../azure-monitor/index.yml) |
| Microsoft.PolicyInsights | [Política do Azure](../governance/policy/index.yml) |
| Microsoft.Portal | [Portal do Azure](/azure/azure-portal/) |
| Microsoft.PowerBI | [Power BI](/power-bi/power-bi-overview) |
| Microsoft.PowerBIDedicated | [Power BI Embedded](/azure/power-bi-embedded/) |
| Microsoft.RecoveryServices | [Recuperação de Site](../site-recovery/index.yml) |
| Microsoft.Relay | [Retransmissão do Azure](../service-bus-relay/relay-what-is-it.md) |
| Microsoft.ResourceGraph | [Gráfico de recursos do Azure](/azure/governance/resource-graph/) |
| Microsoft.ResourceHealth | core |
| Microsoft.Resources | [Azure Resource Manager](index.yml) |
| Microsoft.SaaS | core |
| Microsoft.Scheduler | [Agendador](/azure/scheduler/) |
| Microsoft.Search | [Azure Search](../search/index.yml) |
| Microsoft.Security | [A Central de segurança](../security-center/index.yml) |
| Microsoft.ServiceBus | [Barramento de Serviço](/azure/service-bus/) |
| Microsoft.ServiceFabric | [Service Fabric](../service-fabric/index.yml) |
| Microsoft.ServiceFabricMesh | [Malha do Service Fabric](../service-fabric-mesh/index.yml) |
| Microsoft.SignalRService | [Serviço Azure SignalR](../azure-signalr/index.yml) |
| Microsoft.SiteRecovery | [Recuperação de Site](../site-recovery/index.yml) |
| Microsoft.Solutions | [Aplicativos gerenciados do Azure](../managed-applications/index.yml) |
| Microsoft.Sql | [Banco de Dados SQL do Azure](../sql-database/index.yml) |
| Microsoft.SqlVirtualMachine | [SQL Server nas Máquinas Virtuais do Azure](../virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md) |
| Microsoft.Storage | [Armazenamento](../storage/index.yml) |
| Microsoft.StorageSync | [Armazenamento](../storage/index.yml) |
| Microsoft.StorSimple | [StorSimple](/azure/storsimple/) |
| Microsoft.StreamAnalytics | [Stream Analytics](../stream-analytics/index.yml) |
| Microsoft.Subscription | core |
| microsoft.support | core |
| Microsoft.TimeSeriesInsights | [Time Series Insights](../time-series-insights/index.yml) |
| microsoft.visualstudio | [DevOps do Azure](/azure/devops/?view=azure-devops) |
| Microsoft.VMwareCloudSimple | [Solução de VMware do Azure por CloudSimple](/azure/vmware-cloudsimple/) |
| Microsoft.Web | [Serviço de Aplicativo](../app-service/index.yml)<br />[Funções](../azure-functions/index.yml) |
| Microsoft.WindowsDefenderATP | [Proteção Avançada contra Ameaças do Windows Defender](/windows/security/threat-protection/windows-defender-atp/windows-defender-advanced-threat-protection) |
| Microsoft.WindowsIoT | [Serviços do Windows 10 IoT Core](https://docs.microsoft.com/windows-hardware/manufacture/iot/iotcoreservicesoverview) |
| Microsoft.WorkloadMonitor | [Azure Monitor](../azure-monitor/index.yml) |

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre provedores de recursos, consulte [tipos e provedores de recursos do Azure](resource-manager-supported-services.md)
