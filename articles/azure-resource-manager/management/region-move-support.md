---
title: Suporte para mover recursos do Azure entre regiões
description: Lista os tipos de recursos do Azure que podem ser movidos entre regiões do Azure
author: rayne-wiselman
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 08/25/2020
ms.author: raynew
ms.openlocfilehash: 49c5828e02bf96a536ff14f6b84e81f7adbe3090
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98806894"
---
# <a name="support-for-moving-azure-resources-across-regions"></a>Suporte para mover recursos do Azure entre regiões

Este artigo confirma se há suporte para um tipo de recurso do Azure para a mudança para outra região do Azure. 

Pule para um namespace de provedor de recursos:
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [microsoft.aadiam](#microsoftaadiam)
> - [Microsoft.Addons](#microsoftaddons)
> - [Microsoft.ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft.Advisor](#microsoftadvisor)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft.AppPlatform](#microsoftappplatform)
> - [Microsoft.AppService](#microsoftappservice)
> - [Microsoft.Attestation](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.AVS](#microsoftavs)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.AzureStackHCI](#microsoftazurestackhci)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.Billing](#microsoftbilling)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.BizTalkServices](#microsoftbiztalkservices)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.BlockchainTokens](#microsoftblockchaintokens)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Capacity](#microsoftcapacity)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft.ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.ClassicSubscription](#microsoftclassicsubscription)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Commerce](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft.Consumption](#microsoftconsumption)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.ContentModerator](#microsoftcontentmoderator)
> - [Microsoft.CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CustomerInsights](#microsoftcustomerinsights)
> - [Microsoft.CustomerLockbox](#microsoftcustomerlockbox)
> - [Microsoft.CustomProviders](#microsoftcustomproviders)
> - [Microsoft.DataBox](#microsoftdatabox)
> - [Microsoft.DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft.Databricks](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [Microsoft.DataConnect](#microsoftdataconnect)
> - [Microsoft.DataExchange](#microsoftdataexchange)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [Microsoft.DataLake](#microsoftdatalake)
> - [Microsoft.DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft.DataLakeStore](#microsoftdatalakestore)
> - [Microsoft.DataMigration](#microsoftdatamigration)
> - [Microsoft.DataProtection](#microsoftdataprotection)
> - [Microsoft.DataShare](#microsoftdatashare)
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft.DesktopVirtualization](#microsoftdesktopvirtualization)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft.DevOps](#microsoftdevops)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DigitalTwins](#microsoftdigitaltwins)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Experimentation](#microsoftexperimentation)
> - [Microsoft.Falcon](#microsoftfalcon)
> - [Microsoft.Features](#microsoftfeatures)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Microsoft.GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HardwareSecurityModules](#microsofthardwaresecuritymodules)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [Microsoft. HybridNetwork](#microsofthybridnetwork)
> - [Microsoft.Hydra](#microsofthydra)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [microsoft.insights](#microsoftinsights)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kubernetes](#microsoftkubernetes)
> - [Microsoft.KubernetesConfiguration](#microsoftkubernetesconfiguration)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.LocationBasedServices](#microsoftlocationbasedservices)
> - [Microsoft.LocationServices](#microsoftlocationservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningCompute](#microsoftmachinelearningcompute)
> - [Microsoft.MachineLearningExperimentation](#microsoftmachinelearningexperimentation)
> - [Microsoft.MachineLearningModelManagement](#microsoftmachinelearningmodelmanagement)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.Maintenance](#microsoftmaintenance)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft.ManagedNetwork](#microsoftmanagednetwork)
> - [Microsoft.ManagedServices](#microsoftmanagedservices)
> - [Microsoft.Management](#microsoftmanagement)
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft.Marketplace](#microsoftmarketplace)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.MarketplaceOrdering](#microsoftmarketplaceordering)
> - [Microsoft.Media](#microsoftmedia)
> - [Microsoft.Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft.Migrate](#microsoftmigrate)
> - [Microsoft.MixedReality](#microsoftmixedreality)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft.ObjectStore](#microsoftobjectstore)
> - [Microsoft.OffAzure](#microsoftoffazure)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft.Peering](#microsoftpeering)
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.ProjectBabylon](#microsoftprojectbabylon)
> - [Microsoft.ProviderHub](#microsoftproviderhub)
> - [Microsoft.Quantum](#microsoftquantum)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.RedHatOpenShift](#microsoftredhatopenshift)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.ResourceHealth](#microsoftresourcehealth)
> - [Microsoft.Resources](#microsoftresources)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft.SerialConsole](#microsoftserialconsole)
> - [Microsoft.ServerManagement](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.Services](#microsoftservices)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft.Sql](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft.StorageCache](#microsoftstoragecache)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.StreamAnalyticsExplorer](#microsoftstreamanalyticsexplorer)
> - [Microsoft.Subscription](#microsoftsubscription)
> - [microsoft.support](#microsoftsupport)
> - [Microsoft.Synapse](#microsoftsynapse)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.Token](#microsofttoken)
> - [Microsoft.VirtualMachineImages](#microsoftvirtualmachineimages)
> - [microsoft.visualstudio](#microsoftvisualstudio)
> - [Microsoft.VMware](#microsoftvmware)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.VnfManager](#microsoftvnfmanager)
> - [Microsoft.VSOnline](#microsoftvsonline)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsESU](#microsoftwindowsesu)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft. WorkloadBuilder](#microsoftworkloadbuilder)
> - [Microsoft.WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | domainservices | Não | 


## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | diagnosticsettings | Não |
> | diagnosticsettingscategories | Não |
> | privatelinkforazuread | Não |
> | tenants |  Não |

## <a name="microsoftaddons"></a>o. Complementos

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | supportproviders | Não |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- | 
> | aadsupportcases | Não |
> | addsservices | Não | 
> | agentes | Não | 
> | anonymousapiusers | Não |
> | configuração | Não | 
> | logs | Não | 
> | relatórios | Não | 
> | servicehealthmetrics | Não | 
> | services | Não | 

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- | 
> | configurações | Não | 
> | generaterecommendations | Não |
> | metadata | Não |
> | filmes | Não |
> | suppressions | Não | 

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | actionrules | Não | 
> | alertas | Não | 
> | alertslist | Não | 
> | alertsmetadata | Não | 
> | alertssummary | Não | 
> | alertssummarylist | Não | 
> | smartdetectoralertrules | Não | 
> | smartgroups | Não | 

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | servers | Não |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | reportfeedback | Não |
> | serviço |  Sim (usando o modelo) <br/><br/> [Mova o gerenciamento de API entre regiões](../../api-management/api-management-howto-migrate.md). | 

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | configurationstores | Não | 
> | configurationstores/eventgridfilters | Não |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- | 
> | spring | Não | 

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | apiapps | Sim (usando o modelo)<br/><br/> [Mover um aplicativo do serviço de aplicativo para outra região](../../app-service/manage-move-across-regions.md) | 
> | appidentities | Não | 
> | gateways | Não | 

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- | 
> | attestationproviders | Não | 

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | classicadministrators | Não | 
> | dataaliases | Não | 
> | denyassignments | Não | 
> | elevateaccess | Não | 
> | findorphanroleassignments | Não | 
> | locks | Não | 
> | permissões | Não | 
> | policyassignments | Não | 
> | policydefinitions | Não | 
> | policysetdefinitions | Não | 
> | privatelinkassociations | Não | 
> | resourcemanagementprivatelinks | Não | 
> | roleassignments | Não | 
> | roleassignmentsusagemetrics | Não | 
> | roledefinitions | Não | 

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | automationaccounts | Sim (usando o modelo) <br/><br/> [Usando a replicação geográfica](../../automation/automation-managing-data.md#geo-replication-in-azure-automation) |  
> | automationaccounts/configurations | Não | 
> | automationaccounts/runbooks | Não | 

## <a name="microsoftavs"></a>Microsoft.AVS

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | Assinatura |
> | ------------- | ----------- | 
> | privateclouds | Não | 


## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | b2cdirectories | Não | 
> | b2ctenants | Não | 

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | datacontrollers | Não | 
> | hybriddatamanagers | Não | 
> | postgresinstances | Não | 
> | sqlinstances | Não | 
> | sqlmanagedinstances | Não |
> | sqlserverinstances | Não | 
> | sqlserverregistrations | Não |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | cloudmanifestfiles | Não |
> | registrations | Não | 

## <a name="microsoftazurestackhci"></a>Microsoft.AzureStackHCI

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- | 
> | clusters | Não | 

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | batchaccounts |  As contas do lote não podem ser movidas diretamente de uma região para outra, mas você pode usar um modelo para exportar um modelo, modificá-lo e implantar o modelo na nova região. <br/><br/> Saiba mais sobre como [mover uma conta do lote entre regiões](../../batch/best-practices.md#moving-batch-accounts-across-regions) |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- | 
> | billingaccounts | Não | 
> | billingperiods | Não | 
> | billingpermissions | Não | 
> | billingproperty | Não | 
> | billingroleassignments | Não | 
> | billingroledefinitions | Não | 
> | departments | Não | 
> | enrollmentaccounts | Não | 
> | invoices | Não | 
> | transfers | Não | 

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | mapapis | Não | 

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | biztalk | Não | 

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | blockchainmembers | Não <br/><br/> A rede blockchain não pode ter nós em regiões diferentes. 
> | cordamembers | Não |
> | watchers | Não | 

## <a name="microsoftblockchaintokens"></a>Microsoft.BlockchainTokens

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | tokenservices | Não |


## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | blueprintassignments | Não | 
> | blueprints | Não |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | botservices | Não | 

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | redis | Não | 
> | redisenterprise | Não | 

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- | 
> | appliedreservations | Não | 
> | calculateexchange | Não | 
> | calculateprice | Não | 
> | calculatepurchaseprice | Não | 
> | catalogs | Não | 
> | commercialreservationorders | Não | 
> | câmbio | Não |
> | reservationorders | Não | 
> | reservations | Não | 
> | recursos | Não | 
> | validatereservationorder | Não | 

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | cdnwebapplicationfirewallpolicies | Não |
> | edgenodes | Não
> | perfis | Não | 
> | profiles/endpoints | Não | 

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | certificateorders | Não | 


## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | funcionalidades | Não | 
> | domainnames | Sim | Não |
> | quotas | Não | 
> | resourcetypes | Não |
> | validatesubscriptionmoveavailability | Não | 
> | virtualmachines | Não 

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- | 
> | classicinfrastructureresources | Não | 

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | funcionalidades | Não | 
> | expressroutecrossconnections | Não | 
> | expressroutecrossconnections/peerings | Não | 
> | gatewaysupporteddevices | Não | 
> | networksecuritygroups | Não |
> | quotas | Não |
> | reservedips | Não | 
> | virtualnetworks | Não | 

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | disks | Não | 
> | images | Não | 
> | osimages | Não | 
> | osplatformimages | Não | 
> | publicimages | Não | 
> | quotas | Não | 
> | storageaccounts | Sim |  
> | vmimages | Não |

## <a name="microsoftclassicsubscription"></a>Microsoft.ClassicSubscription

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- | 
> | operações | Não | 

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | accounts | Não | 
> | Pesquisa Cognitiva | Com suporte com etapas manuais.<br/><br/> Saiba mais sobre como [mover o serviço de pesquisa cognitiva do Azure para outra região](../../search/search-howto-move-across-regions.md)

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- | 
> | ratecard | Não | 
> | usageaggregates | Não | 

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | availabilitysets | Sim <br/><br/> Use o [Azure Resource mover](../../resource-mover/tutorial-move-region-virtual-machines.md) para mover conjuntos de disponibilidade. | 
> | diskaccesses | Não |
> | diskencryptionsets | Não | 
> | disks | Sim <br/><br/> Use o [Azure Resource mover](../../resource-mover/tutorial-move-region-virtual-machines.md) para mover VMs do Azure e discos relacionados. | 
> | galleries | Não | 
> | galleries/images | Não | 
> | galleries/images/versions | Não | 
> | hostgroups | Não | 
> | hostgroups/hosts | Não | 
> | images | Não | 
> | proximityplacementgroups | Não | 
> | restorepointcollections | Não | 
> | sharedvmimages | Não | 
> | sharedvmimages/versions | Não | 
> | snapshots | Não | 
> | sshpublickeys | Não |
> | virtualmachines | Sim <br/><br/> Use o [Azure Resource mover](../../resource-mover/tutorial-move-region-virtual-machines.md) para mover VMs do Azure. | 
> | virtualmachines/extensions | Não | 
> | virtualmachinescalesets | Não | 

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- | 
> | aggregatedcost | Não | 
> | balances | Não | 
> | budgets | Não | 
> | charges | Não | 
> | costtags | Não | 
> | credits | Não | 
> | events | Não | 
> | forecasts | Não | 
> | lots | Não | 
> | marketplaces | Não | 
> | pricesheets | Não | 
> | products | Não | 
> | reservationdetails | Não | 
> | reservationrecommendationdetails | Não | 
> | reservationrecommendations | Não | 
> | reservationsummaries | Não | 
> | reservationtransactions | Não | 
> | marcas | Não | 
> | tenants | Não | 
> | terms | Não | 
> | usagedetails | Não | 


## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | containergroups | Não | 
> | serviceassociationlinks | Não |


## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | registries | Não |  
> | registries/agentpools | Não | 
> | registries/buildtasks | Não |  
> | registries/replications | Não | 
> | registries/tasks | Não |  
> | registries/webhooks | Não | 

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | containerservices | Não |
> | managedclusters | Não | 
> | openshiftmanagedclusters | Não | 

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | de dimensionamento da Web | Não | 

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | accounts | Não | 

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | alertas | Não | 
> | billingaccounts | Não | 
> | budgets | Não | 
> | cloudconnectors | Não | 
> | conectores | Não | 
> | departments | Não | 
> | dimensions | Não | 
> | enrollmentaccounts | Não | 
> | exports | Não | 
> | externalbillingaccounts | Não | 
> | previsão | Não | 
> | Consulta | Não | 
> | register | Não | 
> | reportconfigs | Não | 
> | relatórios | Não | 
> | configurações | Não | 
> | showbackrules | Não | 
> | Modos de exibição | Não | 

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | hubs | Não |  

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- | 
> | solicitações | Não | 

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | associations | Não |
> | resourceproviders | Não | 

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | jobs | Não | 

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | availableskus | Não |
> | databoxedgedevices | Não | 

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | workspaces | Não | 

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | catalogs | Não | 
> | datacatalogs | Não | 

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | connectionmanagers | Não | 

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | pacotes | Não | 
> | planos | Não | 

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | datafactories | Não | 
> | factories | Não |  

## <a name="microsoftdatalake"></a>Microsoft.DataLake

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | datalakeaccounts | Não | 

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | accounts | Não | 

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | accounts | Não | 

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | services | Não | 
> | services/projects | Não | 
> | slots | Não | 

## <a name="microsoftdataprotection"></a>Microsoft.DataProtection

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- | ---------- |
> | backupvaults | Não | 

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | accounts | Não | 

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | servers | Você pode usar uma réplica de leitura entre regiões para mover um servidor existente. [Saiba mais](../../postgresql/howto-move-regions-portal.md).<br/><br/> Se o serviço for provisionado com o armazenamento de backup com redundância geográfica, você poderá usar a restauração geográfica para restaurar em outras regiões. [Saiba mais](../../mariadb/concepts-business-continuity.md#recover-from-an-azure-regional-data-center-outage).

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | servers | Você pode usar uma réplica de leitura entre regiões para mover um servidor existente. [Saiba mais](../../mysql/howto-move-regions-portal.md).

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | servergroups | Não | 
> | servers | Você pode usar uma réplica de leitura entre regiões para mover um servidor existente. [Saiba mais](../../postgresql/howto-move-regions-portal.md).
> | serversv2 | Não | 

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | artifactsources | Não | 
> | rollouts | Não |  
> | servicetopologies | Não | 
> | servicetopologies/services | Não |  
> | servicetopologies/services/serviceunits | Não | 
> | etapas | Não | 


## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- | 
> | applicationgroups | Não | 
> | workspaces | Não | 

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | elasticpools | Não. O recurso não está exposto.
> | elasticpools/iothubtenants | Não. O recurso não está exposto.
> | iothubs | Sim. [Saiba mais](../../iot-hub/iot-hub-how-to-clone.md)
> | provisioningservices | Não | 

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | controladores | Não | 


## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | controladores | Não | 
> | Cluster AKS | Não<br/><br/> [Saiba mais](../../dev-spaces/faq.md#can-i-migrate-my-aks-cluster-with-azure-dev-spaces-to-another-region) sobre como mudar para outra região.

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | labcenters | Não | 
> | labs | Não | 
> | labs/environments | Não |  
> | labs/servicerunners | Não | 
> | labs / virtualmachines | Não |  
> | schedules | Não |  

## <a name="microsoftdigitaltwins"></a>Microsoft.DigitalTwins

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- | 
> | digitaltwinsinstances | Sim, recriando recursos em uma nova região. [Saiba mais](../../digital-twins/how-to-move-regions.md) |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | databaseaccounts | Não | 
> | databaseaccounts | Não | 

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | domains | Não | 
> | generatessorequest | Não | 
> | topleveldomains | Não | 
> | validatedomainregistrationinformation | Não |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | services | Não |  

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | domains | Não | 
> | eventsubscriptions | Não |
> | extensiontopics | Não | 
> | partnernamespaces | Não | 
> | partnerregistrations | Não | 
> | partnertopics | Não | 
> | systemtopics | Não | 
> | topics | Não | 
> | topictypes | Não | 

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | clusters | Não |  
> | namespaces | Sim (com modelo)<br/><br/> [Mover um namespace do hub de eventos para outra região](../../event-hubs/move-across-regions.md) | 
> | sku | Não |  

## <a name="microsoftexperimentation"></a>Microsoft.Experimentation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- | 
> | experimentworkspaces | Não | 

## <a name="microsoftfalcon"></a>Microsoft.Falcon

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- | 
> | namespaces | Não | 

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- | 
> | featureproviders | Não | 
> | recursos | Não | 
> | providers | Não | 
> | subscriptionfeatureregistrations | Não | 

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | accounts | Não | 

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- | 
> | automanagedaccounts | Não | 
> | automanagedvmconfigurationprofiles | Não | 
> | guestconfigurationassignments | Não | 
> | software | Não | 
> | softwareupdateprofile | Não | 
> | softwareupdates | Não | 

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | hanainstances | Não | 
> | sapmonitors | Não |  

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- | 
> | dedicatedhsms | Não | 


## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | clusters | Não | 

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | services | Não |  

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | machines | Não | 
> | machines/extensions | Não |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | datamanagers |  Não | 

## <a name="microsofthybridnetwork"></a>Microsoft. HybridNetwork

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- | 
> | dispositivos | Não | 
> | vnfs | Não | 

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- | 
> | components | Não | 
> | networkscopes | Não | 

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | jobs |  Não | 

## <a name="microsoftinsights"></a>microsoft.insights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | accounts | Não. [Saiba mais](../../azure-monitor/faq.md#how-do-i-move-an-application-insights-resource-to-a-new-region).
> | actiongroups |  Não | 
> | activitylogalerts | Não | 
> | alertrules |  Não | 
> | autoscalesettings |  Não | 
> | baseline | Não |
> | components |  Não |  
> | datacollectionrules | Não | 
> | diagnosticsettings | Não | 
> | diagnosticsettingscategories | Não | 
> | eventcategories | Não | 
> | eventtypes | Não | 
> | extendeddiagnosticsettings | Não | |
> | guestdiagnosticsettings | Não | 
> | listmigrationdate | Não | 
> | logdefinitions | Não | 
> | logprofiles | Não | 
> | logs | Não | Não |
> | metricalerts | Não | 
> | metricbaselines | Não | 
> | metricbatch | Não | 
> | metricdefinitions | Não | 
> | metricnamespaces | Não | 
> | Métricas | Não | 
> | migratealertrules | Não |
> | migratetonewpricingmodel | Não | 
> | myworkbooks | Não |
> | notificationgroups | Não | 
> | privatelinkscopes | Não |
> | rollbacktolegacypricingmodel | Não |
> | scheduledqueryrules |  Não | 
> | Topologia | Não |
> | transações | Não |
> | vminsightsonboardingstatuses | Não |
> | webtests |  Não | 
> | webtesters/testresultfile | Não |
> | workbooks |  Não |  
> | workbooktemplates | Não |


## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | apptemplates | Não | 
> | iotapps | Não | 



## <a name="microsoftiothub"></a>Microsoft.IoTHub

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> |  iothub |  Sim (clonar Hub) <br/><br/> [Clonar um hub IoT para outra região](../../iot-hub/iot-hub-how-to-clone.md)

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região |
> | ------------- | ----------- | 
> | grafo | Não | 

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | deletedvaults | Não |
> | hsmpools | Não | 
> | managedhsms | Não |
> | vaults |  Não | 

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- | 
> | connectedclusters | Não | 
> | registeredsubscriptions | Não | 

## <a name="microsoftkubernetesconfiguration"></a>Microsoft.KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- | 
> | sourcecontrolconfigurations | Não | 

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | clusters |  Não |  

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | labaccounts | Não | 
> | users | Não | 

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | accounts | Não | 

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | accounts | Não, é um serviço global.

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | hostingenvironments | Não | 
> | integrationaccounts |  Não |  
> | integrationserviceenvironments | Não | 
> | integrationserviceenvironments/managedapis | Não |
> | isolatedenvironments | Não | 
> | workflows |  Não |  

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | commitmentplans |  Não | 
> | webservices |  Não | 
> | workspaces |  Não | 

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | operationalizationclusters |  Não | 

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | accounts | Não | 
> | teamaccounts | Não | 


## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | accounts | Não | 


## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | workspaces | Não | 

## <a name="microsoftmaintenance"></a>Microsoft.Maintenance

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | configurationassignments | Sim. [Saiba mais](../../virtual-machines/move-region-maintenance-configuration.md) | 
> | maintenanceconfigurations | Sim. [Saiba mais](../../virtual-machines/move-region-maintenance-configuration-resources.md) |
> | updates | Não | 

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | identidades | Não | 
> | userassignedidentities | Não | 

## <a name="microsoftmanagednetwork"></a>Microsoft.ManagedNetwork

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- | 
> | managednetworks | Não | 
> | managednetworks/managednetworkgroups | Não |
> | managednetworks/managednetworkpeeringpolicies | Não | 
> | notificação | Não | 

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- | 
> | marketplaceregistrationdefinitions | Não | 
> | registrationassignments | Não |
> | registrationdefinitions | Não | 

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- | 
> | getentities | Não | 
> | managementgroups | Não | 
> | managementgroups/settings | Não | 
> | recursos | Não | 
> | starttenantbackfill | Não | 
> | tenantbackfillstatus | Não | 

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | accounts |  Não, o Azure Maps é um serviço geoespacial. 
> | accounts/privateatlases | Não

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | offers | Não | 
> | offertypes | Não | 
> | privategalleryitems | Não | 
> | privatestoreclient | Não | 
> | privatestores | Não | 
> | products | Não | 
> | publishers | Não | 
> | register | Não | 

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- | 
> | classicdevservices | Não | 

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- | 
> | Contratos | Não | 
> | offertypes | Não | 

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | mediaservices |  Não | 
> | mediaservices/liveevents |  Não | 
> | mediaservices/streamingendpoints |  Não | 

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | appclusters | Não | 

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | assessmentprojects | Não | 
> | migrateprojects | Não | 
> | movecollections | Não
> | projects | Não | 

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- | ---------- |
> | holographicsbroadcastaccounts | Não | 
> | objectunderstandingaccounts | Não | 
> | remoterenderingaccounts | Não | 
> | spatialanchorsaccounts | Não | 

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | netappaccounts | Não | 
> | netappaccounts/capacitypools | Não | 
> | netappaccounts/capacitypools/volumes | Não | 
> | netappaccounts/capacitypools/volumes/mounttargets | Não | 
> | netappaccounts/capacitypools/volumes/snapshots | Não | 

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | applicationgateways | Não |
> | applicationgatewaywebapplicationfirewallpolicies | Não | 
> | applicationsecuritygroups |  Não |  
> | azurefirewalls |  Não |  
> | bastionhosts | Não | 
> | bgpservicecommunities | Não |
> | connections |  Não | 
> | ddoscustompolicies |  Não | 
> | ddosprotectionplans | Não | 
> | dnszones |  Não | 
> | expressroutecircuits | Não | 
> | expressroutegateways | Não | 
> | expressrouteserviceproviders | Não | 
> | firewallpolicies | Não |
> | frontdoors | Não | 
> | ipallocations | Não |
> | ipgroups | Não |
> | loadbalancers | Sim <br/><br/> Use o [Azure Resource mover](../../resource-mover/tutorial-move-region-virtual-machines.md) para mover balanceadores de carga internos e externos. |
> | localnetworkgateways |  Não | 
> | natgateways |  Não | 
> | networkexperimentprofiles | Não |
> | networkintentpolicies |  Não | 
> | networkinterfaces | Sim <br/><br/> Use o [Azure Resource mover](../../resource-mover/tutorial-move-region-virtual-machines.md) para mover NICs. | 
> | networkprofiles | Não | 
> | networksecuritygroups | Sim <br/><br/> Use o [Azure Resource mover](../../resource-mover/tutorial-move-region-virtual-machines.md) para mover NGSs (grupos de segurança de rede). | 
> | networkwatchers |  Não |  
> | networkwatchers/connectionmonitors |  Não | 
> | networkwatchers/flowlogs |  Não | 
> | networkwatchers/pingmeshes |  Não | 
> | p2svpngateways | Não | 
> | privatednszones |  Não |  
> | privatednszones/virtualnetworklinks | Não |> | privatednszonesinternal | Não |
> | privateendpointredirectmaps | Não |
> | privateendpoints | Não | 
> | privatelinkservices | Não | 
> | publicipaddresses | Sim<br/><br/> Use o [Azure Resource mover](../../resource-mover/tutorial-move-region-virtual-machines.md) para mover endereços IP públicos. |
> | publicipprefixes | Não | 
> | routefilters | Não | 
> | routetables |  Não | 
> | securitypartnerproviders | Não |
> | serviceendpointpolicies |  Não | 
> | trafficmanagergeographichierarchies | Não | 
> | trafficmanagerprofiles |  Não | 
> | trafficmanagerusermetricskeys | Não |
> | virtualhubs | Não | 
> | virtualnetworkgateways |  Não |  
> | virtualnetworks |  Não | 
> | virtualnetworktaps | Não | 
> | virtualwans | Não | 
> | vpngateways (WAN Virtual) | Não | 
> | vpnsites (WAN Virtual) | Não | 
> | vpnsites (WAN Virtual) | Não |


## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | namespaces |  Não | 
> | namespaces/notificationhubs |  Não |  

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- | 
> | osnamespaces | Não | 

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- | 
> | hypervsites | Não | 
> | importsites | Não | 
> | serversites | Não | 
> | vmwaresites | Não | 

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | clusters | Não | 
> | deletedworkspaces | Não | 
> | linktargets | Não | 
> | storageinsightconfigs | Não |
> | workspaces | Não |



## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | managementassociations | Não |
> | managementconfigurations |  Não | 
> | solutions | Não |
> | Modos de exibição |  Não | 

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | legacypeerings | Não | 
> | peerasns | Não | 
> | peeringlocations | Não | 
> | peerings | Não | 
> | peeringservicecountries | Não | 
> | peeringservicelocations | Não | 
> | peeringserviceproviders | Não | 
> | peeringservices | Não | 

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- | 
> | policyevents | Não | 
> | policystates | Não | 
> | policytrackedresources | Não | 
> | remediations | Não | 

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | consoles | Não |
> | dashboards | Não | 
> | usersettings | Não | 


## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | workspacecollections |  Não | 

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | capacities |  Não | 

## <a name="microsoftprojectbabylon"></a>Microsoft.ProjectBabylon

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | accounts | Não | 

## <a name="microsoftproviderhub"></a>Microsoft.ProviderHub

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- | 
> | availableaccounts | Não | 
> | providerregistrations | Não | 
> | rollouts | Não | 

## <a name="microsoftquantum"></a>Microsoft.Quantum

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- | 
> | workspaces | Não | 

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | replicationeligibilityresults | Não |
> | vaults | Não.<br/><br/> Não há suporte para a movimentação de cofres dos serviços de recuperação para o backup do Azure nas regiões do Azure.<br/><br/> Nos cofres dos serviços de recuperação para Azure Site Recovery, você pode [desabilitar e recriar o cofre](../../site-recovery/move-vaults-across-regions.md) na região de destino. | 

## <a name="microsoftredhatopenshift"></a>Microsoft.RedHatOpenShift

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- | 
> | openshiftclusters | Não | 

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | namespaces |  Não | 

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | consultas |  Não |  
> | resourcechangedetails | Não | 
> | resourcechanges | Não | 
> | recursos | Não | 
> | resourceshistory | Não | 
> | subscriptionsstatus | Não | 

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- | 
> | childresources | Não | 
> | emergingissues | Não | 
> | events | Não | 
> | metadata | Não | 
> | Notificações | Não | 

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região |
> | ------------- | ----------- |
> | deploymentScripts |  Sim<br/><br/>[Mover recursos do Microsoft. Resources para uma nova região](microsoft-resources-move-regions.md) |
> | templateSpecs |  Sim<br/><br/>[Mover recursos do Microsoft. Resources para uma nova região](microsoft-resources-move-regions.md) |  

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | de dimensionamento da Web |  Não | 
> | saasresources | Não | 


## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | resourcehealthmetadata | Não |
> | searchservices |  Não | 


## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | adaptivenetworkhardenings | Não | 
> | advancedthreatprotectionsettings | Não | 
> | alertas | Não | 
> | allowedconnections | Não | 
> | applicationwhitelistings | Não | 
> | assessmentmetadata | Não | 
> | assessments | Não | 
> | autodismissalertsrules | Não | 
> | automations | Não | 
> | autoprovisioningsettings | Não |
> | complianceresults | Não | 
> | compliances | Não | 
> | datacollectionagents | Não | 
> | devicesecuritygroups | Não | 
> | discoveredsecuritysolutions | Não | 
> | externalsecuritysolutions | Não | 
> | informationprotectionpolicies | Não | 
> | iotsecuritysolutions | Não | 
> | iotsecuritysolutions/analyticsmodels | Não | 
> | iotsecuritysolutions/analyticsmodels/aggregatedalerts | Não | 
> | iotsecuritysolutions/analyticsmodels/aggregatedrecommendations | Não | 
> | jitnetworkaccesspolicies | Não | 
> | políticas | Não | 
> | pricings | Não | 
> | regulatorycompliancestandards | Não | 
> | regulatorycompliancestandards/regulatorycompliancecontrols | Não | 
> | regulatorycompliancestandards/regulatorycompliancecontrols/regulatorycomplianceassessments | Não | 
> | securitycontacts | Não | 
> | securitysolutions | Não | 
> | securitysolutionsreferencedata | Não | 
> | securitystatuses | Não | 
> | securitystatusessummaries | Não | 
> | servervulnerabilityassessments | Não | 
> | configurações | Não | 
> | subassessments | Não |
> | tarefas | Não | 
> | topologies | Não | 
> | workspacesettings | Não | 

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- | 
> | aggregations | Não | 
> | alertrules | Não | 
> | alertruletemplates | Não | 
> | automationrules | Não |
> | cases | Não | 
> | dataconnectors | Não | 
> | entidades | Não | 
> | entityqueries | Não |
> | incidents | Não | 
> | officeconsents | Não | 
> | configurações | Não | 
> | threatintelligence | Não | 

## <a name="microsoftserialconsole"></a>Microsoft.SerialConsole

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- | 
> | consoleservices | Não | 

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | gateways | Não | 
> | nós | Não | 

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | namespaces |  Não | 
> | premiummessagingregions | Não | 
> | sku | Não | 

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | de dimensionamento da Web | Não | 
> | clusters |  Não |  
> | containergroups | Não | 
> | containergroupsets | Não | 
> | edgeclusters | Não | 
> | managedclusters | Não |
> | networks | Não | 
> | secretstores | Não | 
> | volumes | Não | 

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | de dimensionamento da Web |  Não | 
> | containergroups | Não | 
> | gateways |  Não | 
> | networks |  Não | 
> | segredos |  Não | 
> | volumes |  Não |  

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- | 
> | rollouts | Não | 

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | signalr |  Não |  

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- | 
> | hybridusebenefits | Não | 

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | appliancedefinitions | Não | 
> | appliances | Não | 
> | jitrequests | Não | 

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | instancepools | Não | 
> | Locais | Não |
> | managedinstances | Sim <br/><br/> [Saiba mais](../../azure-sql/database/move-resources-across-regions.md) sobre como mover instâncias gerenciadas entre regiões. | 
> | managedinstances/databases | Sim | 
> | servers | Sim | 
> | servers/databases | Sim <br/><br/> [Saiba mais](../../azure-sql/database/move-resources-across-regions.md) sobre como mover bancos de dados entre regiões.<br/><br/> [Saiba mais](../../resource-mover/tutorial-move-region-sql.md) sobre como usar o Azure Resource mover para mover bancos de dados SQL do Azure.  | 
> | servers/elasticpools | Sim <br/><br/> [Saiba mais](../../azure-sql/database/move-resources-across-regions.md) sobre como mover pools elásticos entre regiões.<br/><br/> [Saiba mais](../../resource-mover/tutorial-move-region-sql.md) sobre como usar o Azure Resource mover para mover os pools elásticos do SQL Azure.  | 
> | virtualclusters | Sim | 

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | sqlvirtualmachinegroups |  Não |  
> | sqlvirtualmachines |  Não |  


## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | storageaccounts | Sim<br/><br/> [Mover uma conta de armazenamento do Azure para outra região](../../storage/common/storage-account-move.md) | 

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | caches | Não | 

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | storagesyncservices |  Não | 

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | storagesyncservices | Não | 

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | storagesyncservices | Não | 

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | managers | Não | 

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | clusters | Não |
> | streamingjobs |  Não |  


## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | environments | Não | 
> | instances | Não | 

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- | 
> | subscriptions | Não | 

## <a name="microsoftsupport"></a>microsoft.support

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- | 
> | services | Não | 
> | supporttickets | Não | 

## <a name="microsoftsynapse"></a>Microsoft.Synapse

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- | 
> | workspaces | Não | 
> | workspaces/bigdatapools | Não | 
> | workspaces/sqlpools | Não | 


## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | environments |  Não | 
> | environments/eventsources |  Não |  
> | environments/referencedatasets |  Não | 

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | stores | Não | 

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | imagetemplates | Não | 

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | account |  Não | 
> | account/extension |  Não | 
> | account/project |  Não | 

## <a name="microsoftvmware"></a>Microsoft.VMware

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- | 
> | arczones | Não | 
> | resourcepools | Não | 
> | vcenters | Não | 
> | virtualmachines | Não | 
> | virtualmachinetemplates | Não | 
> | virtualnetworks | Não | 

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | dedicatedcloudnodes | Não | 
> | dedicatedcloudservices | Não | 
> | virtualmachines | Não | 

## <a name="microsoftvnfmanager"></a>Microsoft.VnfManager

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- | 
> | dispositivos | Não | 
> | vnfs | Não | 

## <a name="microsoftvsonline"></a>Microsoft.VSOnline

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- | 
> | accounts | Não | 
> | planos | Não | 
> | registeredsubscriptions | Não |


## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | availablestacks | Não | 
> | billingmeters | Não | 
> | certificates | Não | 
> | connectiongateways |  Não |  
> | connections |  Não |  
> | customapis |  Não | 
> | deletedsites | Não | 
> | deploymentlocations | Não | 
> | georegions | Não | 
> | hostingenvironments | Não | 
> | kubeenvironments | Não | 
> | publishingusers | Não |
> | filmes | Não | 
> | resourcehealthmetadata | Não | 
> | runtimes | Não | 
> | serverfarms | Não |  
> | serverfarms/eventgridfilters | N
> | sites |  Não | 
> | sites/premieraddons |  Não |  
> | sites / slots |  Não |  
> | sourcecontrols | Não |
> | staticsites | Não | 

## <a name="microsoftwindowsesu"></a>Microsoft.WindowsESU

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- | 
> | multipleactivationkeys | Não |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- |
> | deviceservices | Não | 

## <a name="microsoftworkloadbuilder"></a>Microsoft. WorkloadBuilder

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- | 
> | cargas de trabalho | Não | 

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimentação de região | 
> | ------------- | ----------- | 
> | components | Não |
> | componentssummary | Não | 
> | monitorinstances | Não | 
> | monitorinstancessummary | Não | 
> | monitors | Não | 
## <a name="third-party-services"></a>Serviços de terceiros

No momento, serviços de terceiros não dão suporte à operação de movimentação.

## <a name="next-steps"></a>Próximas etapas

[Saiba mais](../../resource-mover/overview.md) sobre o serviço de movimentação de recursos.

