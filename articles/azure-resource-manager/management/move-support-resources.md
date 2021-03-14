---
title: Mover suporte de operação por tipo de recurso
description: Lista os tipos de recursos do Azure que podem ser movidos para um novo grupo de recursos ou assinatura.
ms.topic: conceptual
ms.date: 01/11/2021
ms.openlocfilehash: ae59bcc65f296a10c68b88f5f6a1ecbc923f8856
ms.sourcegitcommit: ec39209c5cbef28ade0badfffe59665631611199
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2021
ms.locfileid: "103232589"
---
# <a name="move-operation-support-for-resources"></a>Mover o suporte de operação para recursos

Este artigo lista se um tipo de recurso do Azure é compatível com a operação de movimentação. Ele também fornece informações sobre condições especiais a serem consideradas ao mover um recurso.

> [!IMPORTANT]
> Na maioria dos casos, um recurso filho não pode ser movido independentemente de seu recurso pai. Os recursos filho têm um tipo de recurso no formato de `<resource-provider-namespace>/<parent-resource>/<child-resource>` . Por exemplo, `Microsoft.ServiceBus/namespaces/queues` é um recurso filho de `Microsoft.ServiceBus/namespaces` . Quando você move o recurso pai, o recurso filho é automaticamente movido com ele. Se você não vir um recurso filho neste artigo, você pode presumir que ele é movido com o recurso pai. Se o recurso pai não der suporte à movimentação, o recurso filho não poderá ser movido.

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
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | domainservices | Não | Não |

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | diagnosticsettings | Não | Não |
> | diagnosticsettingscategories | Não | Não |
> | privatelinkforazuread | Sim | Sim |
> | tenants | Sim | Sim |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | supportproviders | Não | Não |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | aadsupportcases | Não | Não |
> | addsservices | Não | Não |
> | agentes | Não | Não |
> | anonymousapiusers | Não | Não |
> | configuração | Não | Não |
> | logs | Não | Não |
> | relatórios | Não | Não |
> | servicehealthmetrics | Não | Não |
> | services | Não | Não |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | configurações | Não | Não |
> | generaterecommendations | Não | Não |
> | metadata | Não | Não |
> | filmes | Não | Não |
> | suppressions | Não | Não |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | actionrules | Sim | Sim |
> | alertas | Não | Não |
> | alertslist | Não | Não |
> | alertsmetadata | Não | Não |
> | alertssummary | Não | Não |
> | alertssummarylist | Não | Não |
> | smartdetectoralertrules | Sim | Sim |
> | smartgroups | Não | Não |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | servers | Sim | Sim |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!IMPORTANT]
> Um serviço de Gerenciamento de API definido para o SKU de consumo não pode ser movido.

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | reportfeedback | Não | Não |
> | serviço | Sim | Sim |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | configurationstores | Sim | Sim |
> | configurationstores/eventgridfilters | Não | Não |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | spring | Sim | Sim |

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!IMPORTANT]
> Consulte [Diretrizes de movimentação do Serviço de Aplicativo](./move-limitations/app-service-move-limitations.md).

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | apiapps | Não | Não |
> | appidentities | Não | Não |
> | gateways | Não | Não |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | attestationproviders | Sim | Sim |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | classicadministrators | Não | Não |
> | dataaliases | Não | Não |
> | denyassignments | Não | Não |
> | elevateaccess | Não | Não |
> | findorphanroleassignments | Não | Não |
> | locks | Não | Não |
> | permissões | Não | Não |
> | policyassignments | Não | Não |
> | policydefinitions | Não | Não |
> | policysetdefinitions | Não | Não |
> | privatelinkassociations | Não | Não |
> | resourcemanagementprivatelinks | Não | Não |
> | roleassignments | Não | Não |
> | roleassignmentsusagemetrics | Não | Não |
> | roledefinitions | Não | Não |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!IMPORTANT]
> Os runbooks devem existir no mesmo grupo de recursos que a conta de automação.
>
> Para obter informações, consulte [mover sua conta de automação do Azure para outra assinatura](../../automation/how-to/move-account.md?toc=/azure/azure-resource-manager/toc.json).

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | automationaccounts | Sim | Sim |
> | automationaccounts/configurations | Sim | Sim |
> | automationaccounts/runbooks | Sim | Sim |

## <a name="microsoftavs"></a>Microsoft.AVS

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | privateclouds | Sim | Sim |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | b2cdirectories | Sim | Sim |
> | b2ctenants | Não | Não |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | datacontrollers | Não | Não |
> | hybriddatamanagers | Não | Não |
> | postgresinstances | Não | Não |
> | sqlinstances | Não | Não |
> | sqlmanagedinstances | Não | Não |
> | sqlserverinstances | Não | Não |
> | sqlserverregistrations | Sim | Sim |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | cloudmanifestfiles | Não | Não |
> | registrations | Sim | Sim |

## <a name="microsoftazurestackhci"></a>Microsoft.AzureStackHCI

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | Não | Não |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | batchaccounts | Sim | Sim |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | billingaccounts | Não | Não |
> | billingperiods | Não | Não |
> | billingpermissions | Não | Não |
> | billingproperty | Não | Não |
> | billingroleassignments | Não | Não |
> | billingroledefinitions | Não | Não |
> | departments | Não | Não |
> | enrollmentaccounts | Não | Não |
> | invoices | Não | Não |
> | transfers | Não | Não |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | mapapis | Não | Não |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | biztalk | Não | Não |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | blockchainmembers | Não | Não |
> | cordamembers | Não | Não |
> | watchers | Não | Não |

## <a name="microsoftblockchaintokens"></a>Microsoft.BlockchainTokens

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | tokenservices | Não | Não |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | blueprintassignments | Não | Não |
> | blueprints | Não | Não |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | botservices | Sim | Sim |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!IMPORTANT]
> Se a instância do Cache do Azure para Redis estiver configurada com uma rede virtual, a instância não poderá ser movida para uma assinatura diferente. Consulte [Limitações de movimentação de rede](./move-limitations/networking-move-limitations.md).

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | redis | Sim | Sim |
> | redisenterprise | Não | Não |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | appliedreservations | Não | Não |
> | calculateexchange | Não | Não |
> | calculateprice | Não | Não |
> | calculatepurchaseprice | Não | Não |
> | catalogs | Não | Não |
> | commercialreservationorders | Não | Não |
> | câmbio | Não | Não |
> | reservationorders | Não | Não |
> | reservations | Não | Não |
> | recursos | Não | Não |
> | validatereservationorder | Não | Não |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | cdnwebapplicationfirewallmanagedrulesets | Não | Não |
> | cdnwebapplicationfirewallpolicies | Sim | Sim |
> | edgenodes | Não | Não |
> | perfis | Sim | Sim |
> | profiles/endpoints | Sim | Sim |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!IMPORTANT]
> Consulte [Diretrizes de movimentação do Serviço de Aplicativo](./move-limitations/app-service-move-limitations.md).

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | certificateorders | Sim | Sim |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!IMPORTANT]
> Consulte [Diretrizes de movimentação da implantação clássica](./move-limitations/classic-model-move-limitations.md). Os recursos da implantação clássica podem ser movidos entre assinaturas com uma operação específica para esse cenário.

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | funcionalidades | Não | Não |
> | domainnames | Sim | Não |
> | quotas | Não | Não |
> | resourcetypes | Não | Não |
> | validatesubscriptionmoveavailability | Não | Não |
> | virtualmachines | Sim | Sim |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!IMPORTANT]
> Consulte [Diretrizes de movimentação da implantação clássica](./move-limitations/classic-model-move-limitations.md). Os recursos da implantação clássica podem ser movidos entre assinaturas com uma operação específica para esse cenário.

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | classicinfrastructureresources | Não | Não |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!IMPORTANT]
> Consulte [Diretrizes de movimentação da implantação clássica](./move-limitations/classic-model-move-limitations.md). Os recursos da implantação clássica podem ser movidos entre assinaturas com uma operação específica para esse cenário.

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | funcionalidades | Não | Não |
> | expressroutecrossconnections | Não | Não |
> | expressroutecrossconnections/peerings | Não | Não |
> | gatewaysupporteddevices | Não | Não |
> | networksecuritygroups | Não | Não |
> | quotas | Não | Não |
> | reservedips | Não | Não |
> | virtualnetworks | Não | Não |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!IMPORTANT]
> Consulte [Diretrizes de movimentação da implantação clássica](./move-limitations/classic-model-move-limitations.md). Os recursos da implantação clássica podem ser movidos entre assinaturas com uma operação específica para esse cenário.

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | disks | Não | Não |
> | images | Não | Não |
> | osimages | Não | Não |
> | osplatformimages | Não | Não |
> | publicimages | Não | Não |
> | quotas | Não | Não |
> | storageaccounts | Sim | Não |
> | vmimages | Não | Não |

## <a name="microsoftclassicsubscription"></a>Microsoft.ClassicSubscription

> [!IMPORTANT]
> Consulte [Diretrizes de movimentação da implantação clássica](./move-limitations/classic-model-move-limitations.md). Os recursos da implantação clássica podem ser movidos entre assinaturas com uma operação específica para esse cenário.

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | operações | Não | Não |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Sim | Sim |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | ratecard | Não | Não |
> | usageaggregates | Não | Não |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!IMPORTANT]
> Consulte [Diretrizes de movimentação de Máquinas Virtuais](./move-limitations/virtual-machines-move-limitations.md).

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | availabilitysets | Sim | Sim |
> | diskaccesses | Não | Não |
> | diskencryptionsets | Não | Não |
> | disks | Sim | Sim |
> | galleries | Não | Não |
> | galleries/images | Não | Não |
> | galleries/images/versions | Não | Não |
> | hostgroups | Não | Não |
> | hostgroups/hosts | Não | Não |
> | images | Sim | Sim |
> | proximityplacementgroups | Sim | Sim |
> | restorepointcollections | Não | Não |
> | restorepointcollections/restorepoints | Não | Não |
> | sharedvmextensions | Não | Não |
> | sharedvmimages | Não | Não |
> | sharedvmimages/versions | Não | Não |
> | snapshots | Sim | Sim |
> | sshpublickeys | Não | Não |
> | virtualmachines | Sim | Sim |
> | virtualmachines/extensions | Sim | Sim |
> | virtualmachinescalesets | Sim | Sim |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | aggregatedcost | Não | Não |
> | balances | Não | Não |
> | budgets | Não | Não |
> | charges | Não | Não |
> | costtags | Não | Não |
> | credits | Não | Não |
> | events | Não | Não |
> | forecasts | Não | Não |
> | lots | Não | Não |
> | marketplaces | Não | Não |
> | pricesheets | Não | Não |
> | products | Não | Não |
> | reservationdetails | Não | Não |
> | reservationrecommendationdetails | Não | Não |
> | reservationrecommendations | Não | Não |
> | reservationsummaries | Não | Não |
> | reservationtransactions | Não | Não |
> | marcas | Não | Não |
> | tenants | Não | Não |
> | terms | Não | Não |
> | usagedetails | Não | Não |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | containergroups | Não | Não |
> | serviceassociationlinks | Não | Não |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | registries | Sim | Sim |
> | registries/agentpools | Sim | Sim |
> | registries/buildtasks | Sim | Sim |
> | registries/replications | Sim | Sim |
> | registries/tasks | Sim | Sim |
> | registries/webhooks | Sim | Sim |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | containerservices | Não | Não |
> | managedclusters | Não | Não |
> | openshiftmanagedclusters | Não | Não |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | de dimensionamento da Web | Não | Não |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Não | Não |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | alertas | Não | Não |
> | billingaccounts | Não | Não |
> | budgets | Não | Não |
> | cloudconnectors | Não | Não |
> | conectores | Sim | Sim |
> | departments | Não | Não |
> | dimensions | Não | Não |
> | enrollmentaccounts | Não | Não |
> | exports | Não | Não |
> | externalbillingaccounts | Não | Não |
> | previsão | Não | Não |
> | Consulta | Não | Não |
> | register | Não | Não |
> | reportconfigs | Não | Não |
> | relatórios | Não | Não |
> | configurações | Não | Não |
> | showbackrules | Não | Não |
> | Modos de exibição | Não | Não |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hubs | Não | Não |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | solicitações | Não | Não |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | associations | Não | Não |
> | resourceproviders | Sim | Sim |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | jobs | Não | Não |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | availableskus | Não | Não |
> | databoxedgedevices | Não | Não |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | workspaces | Não | Não |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | catalogs | Sim | Sim |
> | datacatalogs | Não | Não |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | connectionmanagers | Não | Não |

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | pacotes | Não | Não |
> | planos | Não | Não |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | datafactories | Sim | Sim |
> | factories | Sim | Sim |

## <a name="microsoftdatalake"></a>Microsoft.DataLake

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | datalakeaccounts | Não | Não |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Sim | Sim |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Sim | Sim |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | services | Não | Não |
> | services/projects | Não | Não |
> | slots | Não | Não |

## <a name="microsoftdataprotection"></a>Microsoft.DataProtection

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | backupvaults | Não | Não |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Sim | Sim |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | servers | Sim | Sim |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | flexibleServers | Sim | Sim |
> | servers | Sim | Sim |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | flexibleServers | Não | Não |
> | servergroups | Não | Não |
> | servers | Sim | Sim |
> | serversv2 | Sim | Sim |
> | singleservers | Sim | Sim |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | artifactsources | Sim | Sim |
> | rollouts | Sim | Sim |
> | servicetopologies | Sim | Sim |
> | servicetopologies/services | Sim | Sim |
> | servicetopologies/services/serviceunits | Sim | Sim |
> | etapas | Sim | Sim |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | applicationgroups | Sim | Sim |
> | hostpools | Sim | Sim |
> | workspaces | Sim | Sim |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | elasticpools | Não | Não |
> | elasticpools/iothubtenants | Não | Não |
> | iothubs | Sim | Sim |
> | provisioningservices | Sim | Sim |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | pipelines | Sim | Sim |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | controladores | Sim | Sim |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | labcenters | Não | Não |
> | labs | Sim | Não |
> | labs/environments | Sim | Sim |
> | labs/servicerunners | Sim | Sim |
> | labs/virtualmachines | Sim | Não |
> | schedules | Sim | Sim |

## <a name="microsoftdigitaltwins"></a>Microsoft.DigitalTwins

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | digitaltwinsinstances | Não | Não |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | databaseaccountnames | Não | Não |
> | databaseaccounts | Sim | Sim |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | domains | Sim | Sim |
> | generatessorequest | Não | Não |
> | topleveldomains | Não | Não |
> | validatedomainregistrationinformation | Não | Não |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | services | Sim | Sim |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | domains | Sim | Sim |
> | eventsubscriptions | Não - não pode ser movido independentemente, apenas automaticamente com recurso assinado. | Não - não pode ser movido independentemente, apenas automaticamente com recurso assinado. |
> | extensiontopics | Não | Não |
> | partnernamespaces | Sim | Sim |
> | partnerregistrations | Não | Não |
> | partnertopics | Sim | Sim |
> | systemtopics | Sim | Sim |
> | topics | Sim | Sim |
> | topictypes | Não | Não |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | Sim | Sim |
> | namespaces | Sim | Sim |
> | sku | Não | Não |

## <a name="microsoftexperimentation"></a>Microsoft.Experimentation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | experimentworkspaces | Não | Não |

## <a name="microsoftfalcon"></a>Microsoft.Falcon

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | namespaces | Sim | Sim |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | featureproviders | Não | Não |
> | recursos | Não | Não |
> | providers | Não | Não |
> | subscriptionfeatureregistrations | Não | Não |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Não | Não |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | automanagedaccounts | Não | Não |
> | automanagedvmconfigurationprofiles | Não | Não |
> | guestconfigurationassignments | Não | Não |
> | software | Não | Não |
> | softwareupdateprofile | Não | Não |
> | softwareupdates | Não | Não |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hanainstances | Não | Não |
> | sapmonitors | Não | Não |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | dedicatedhsms | Não | Não |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!IMPORTANT]
> Você pode mover os clusters HDInsight para uma nova assinatura ou grupo de recursos. No entanto, não é possível mover os recursos de rede vinculados ao cluster HDInsight (por exemplo, a rede virtual, NIC ou balanceador de carga) entre assinaturas. Além disso, não é possível mover uma para um novo grupo de recursos uma NIC que está conectada a uma máquina virtual para o cluster.
>
> Ao mover um cluster HDInsight para uma nova assinatura, mova primeiro os outros recursos (como a conta de armazenamento). Em seguida, mova apenas o cluster HDInsight.

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | Sim | Sim |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | services | Sim | Sim |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | machines | Sim | Sim |
> | machines/extensions | Sim | Sim |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | datamanagers | Sim | Sim |

## <a name="microsofthybridnetwork"></a>Microsoft. HybridNetwork

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | dispositivos | Não | Não |
> | vnfs | Não | Não |

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | components | Não | Não |
> | networkscopes | Não | Não |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | jobs | Sim | Sim |

## <a name="microsoftinsights"></a>microsoft.insights

> [!IMPORTANT]
> Verifique se mover para nova assinatura não excede as [cotas de assinatura](azure-subscription-service-limits.md#azure-monitor-limits).

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | actiongroups | Sim | Sim |
> | activitylogalerts | Não | Não |
> | alertrules | Sim | Sim |
> | autoscalesettings | Sim | Sim |
> | baseline | Não | Não |
> | components | Sim | Sim |
> | datacollectionrules | Não | Não |
> | diagnosticsettings | Não | Não |
> | diagnosticsettingscategories | Não | Não |
> | eventcategories | Não | Não |
> | eventtypes | Não | Não |
> | extendeddiagnosticsettings | Não | Não |
> | guestdiagnosticsettings | Não | Não |
> | listmigrationdate | Não | Não |
> | logdefinitions | Não | Não |
> | logprofiles | Não | Não |
> | logs | Não | Não |
> | metricalerts | Não | Não |
> | metricbaselines | Não | Não |
> | metricbatch | Não | Não |
> | metricdefinitions | Não | Não |
> | metricnamespaces | Não | Não |
> | Métricas | Não | Não |
> | migratealertrules | Não | Não |
> | migratetonewpricingmodel | Não | Não |
> | myworkbooks | Não | Não |
> | notificationgroups | Não | Não |
> | privatelinkscopes | Não | Não |
> | rollbacktolegacypricingmodel | Não | Não |
> | scheduledqueryrules | Sim | Sim |
> | Topologia | Não | Não |
> | transações | Não | Não |
> | vminsightsonboardingstatuses | Não | Não |
> | webtests | Sim | Sim |
> | webtesters/testresultfile | Não | Não |
> | workbooks | Sim | Sim |
> | workbooktemplates | Sim | Sim |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | apptemplates | Não | Não |
> | iotapps | Sim | Sim |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | grafo | Sim | Sim |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!IMPORTANT]
> O Key Vault é usado para criptografia de disco e não pode ser movido para um grupo de recursos na mesma assinatura ou entre assinaturas.

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | deletedvaults | Não | Não |
> | hsmpools | Não | Não |
> | managedhsms | Não | Não |
> | vaults | Sim | Sim |

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | connectedclusters | Sim | Sim |
> | registeredsubscriptions | Não | Não |

## <a name="microsoftkubernetesconfiguration"></a>Microsoft.KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | sourcecontrolconfigurations | Não | Não |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | Sim | Sim |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | labaccounts | Não | Não |
> | users | Não | Não |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Não | Não |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Não | Não |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hostingenvironments | Não | Não |
> | integrationaccounts | Sim | Sim |
> | integrationserviceenvironments | Sim | Não |
> | integrationserviceenvironments/managedapis | Sim | Não |
> | isolatedenvironments | Não | Não |
> | workflows | Sim | Sim |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | commitmentplans | Não | Não |
> | webservices | Sim | Não |
> | workspaces | Sim | Sim |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | operationalizationclusters | Não | Não |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Não | Não |
> | teamaccounts | Não | Não |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Não | Não |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | workspaces | Não | Não |

## <a name="microsoftmaintenance"></a>Microsoft.Maintenance

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | configurationassignments | Não | Não |
> | maintenanceconfigurations | Sim | Sim |
> | updates | Não | Não |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | identidades | Não | Não |
> | userassignedidentities | Não | Não |

## <a name="microsoftmanagednetwork"></a>Microsoft.ManagedNetwork

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | managednetworks | Não | Não |
> | managednetworks/managednetworkgroups | Não | Não |
> | managednetworks/managednetworkpeeringpolicies | Não | Não |
> | notificação | Não | Não |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | marketplaceregistrationdefinitions | Não | Não |
> | registrationassignments | Não | Não |
> | registrationdefinitions | Não | Não |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | getentities | Não | Não |
> | managementgroups | Não | Não |
> | managementgroups/settings | Não | Não |
> | recursos | Não | Não |
> | starttenantbackfill | Não | Não |
> | tenantbackfillstatus | Não | Não |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Sim | Sim |
> | accounts/privateatlases | Sim | Sim |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | offers | Não | Não |
> | offertypes | Não | Não |
> | privategalleryitems | Não | Não |
> | privatestoreclient | Não | Não |
> | privatestores | Não | Não |
> | products | Não | Não |
> | publishers | Não | Não |
> | register | Não | Não |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | classicdevservices | Não | Não |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | Contratos | Não | Não |
> | offertypes | Não | Não |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | mediaservices | Sim | Sim |
> | mediaservices/liveevents | Sim | Sim |
> | mediaservices/streamingendpoints | Sim | Sim |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | appclusters | Não | Não |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | assessmentprojects | Não | Não |
> | migrateprojects | Não | Não |
> | movecollections | Não | Não |
> | projects | Não | Não |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | holographicsbroadcastaccounts | Não | Não |
> | objectunderstandingaccounts | Não | Não |
> | remoterenderingaccounts | Sim | Sim |
> | spatialanchorsaccounts | Sim | Sim |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | netappaccounts | Não | Não |
> | netappaccounts/capacitypools | Não | Não |
> | netappaccounts/capacitypools/volumes | Não | Não |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!IMPORTANT]
> Consulte [Diretrizes de movimentação de rede](./move-limitations/networking-move-limitations.md).

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | applicationgateways | Não | Não |
> | applicationgatewaywebapplicationfirewallpolicies | Não | Não |
> | applicationsecuritygroups | Sim | Sim |
> | azurefirewalls | Não | Não |
> | bastionhosts | Não | Não |
> | bgpservicecommunities | Não | Não |
> | connections | Sim | Sim |
> | ddoscustompolicies | Sim | Sim |
> | ddosprotectionplans | Não | Não |
> | dnszones | Sim | Sim |
> | expressroutecircuits | Não | Não |
> | expressroutegateways | Não | Não |
> | expressrouteserviceproviders | Não | Não |
> | firewallpolicies | Sim | Sim |
> | frontdoors | Não | Não |
> | ipallocations | Sim | Sim |
> | ipgroups | Sim | Sim |
> | loadbalancers | Sim - SKU Básico<br> Sim-SKU padrão | Sim - SKU Básico<br>Não - SKU Standard |
> | localnetworkgateways | Sim | Sim |
> | natgateways | Não | Não |
> | networkexperimentprofiles | Não | Não |
> | networkintentpolicies | Sim | Sim |
> | networkinterfaces | Sim | Sim |
> | networkprofiles | Não | Não |
> | networksecuritygroups | Sim | Sim |
> | networkwatchers | Não | Não |
> | networkwatchers/connectionmonitors | Sim | Não |
> | networkwatchers/flowlogs | Sim | Não |
> | networkwatchers/pingmeshes | Sim | Não |
> | p2svpngateways | Não | Não |
> | privatednszones | Sim | Sim |
> | privatednszones/virtualnetworklinks | Sim | Sim |
> | privatednszonesinternal | Não | Não |
> | privateendpointredirectmaps | Não | Não |
> | privateendpoints | Não | Não |
> | privatelinkservices | Não | Não |
> | publicipaddresses | Sim - SKU Básico<br>Sim-SKU padrão | Sim - SKU Básico<br>Não - SKU Standard |
> | publicipprefixes | Sim | Sim |
> | routefilters | Não | Não |
> | routetables | Sim | Sim |
> | securitypartnerproviders | Sim | Sim |
> | serviceendpointpolicies | Sim | Sim |
> | trafficmanagergeographichierarchies | Não | Não |
> | trafficmanagerprofiles | Sim | Sim |
> | trafficmanagerprofiles/heatmaps | Não | Não |
> | trafficmanagerusermetricskeys | Não | Não |
> | virtualhubs | Não | Não |
> | virtualnetworkgateways | Sim | Sim |
> | virtualnetworks | Sim | Sim |
> | virtualnetworktaps | Não | Não |
> | virtualrouters | Sim | Sim |
> | virtualwans | Não | Não |
> | vpngateways (WAN Virtual) | Não | Não |
> | vpnserverconfigurations | Não | Não |
> | vpnsites (WAN Virtual) | Não | Não |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | namespaces | Sim | Sim |
> | namespaces/notificationhubs | Sim | Sim |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | osnamespaces | Sim | Sim |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hypervsites | Não | Não |
> | importsites | Não | Não |
> | serversites | Não | Não |
> | vmwaresites | Não | Não |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!IMPORTANT]
> Verifique se mover para uma nova assinatura não excede as [cotas de assinatura](azure-subscription-service-limits.md#azure-monitor-limits).
>
> Espaços de trabalho que têm uma conta de automação vinculada não podem ser movidos. Antes de iniciar uma operação de movimentação, não se esqueça de desvincular as contas de automação.

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | Não | Não |
> | deletedworkspaces | Não | Não |
> | linktargets | Não | Não |
> | storageinsightconfigs | Não | Não |
> | workspaces | Sim | Sim |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | managementassociations | Não | Não |
> | managementconfigurations | Sim | Sim |
> | solutions | Sim | Sim |
> | Modos de exibição | Sim | Sim |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | legacypeerings | Não | Não |
> | peerasns | Não | Não |
> | peeringlocations | Não | Não |
> | peerings | Não | Não |
> | peeringservicecountries | Não | Não |
> | peeringservicelocations | Não | Não |
> | peeringserviceproviders | Não | Não |
> | peeringservices | Não | Não |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | policyevents | Não | Não |
> | policystates | Não | Não |
> | policytrackedresources | Não | Não |
> | remediations | Não | Não |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | consoles | Não | Não |
> | dashboards | Sim | Sim |
> | usersettings | Não | Não |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | workspacecollections | Sim | Sim |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | capacities | Sim | Sim |

## <a name="microsoftprojectbabylon"></a>Microsoft.ProjectBabylon

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Não | Não |

## <a name="microsoftproviderhub"></a>Microsoft.ProviderHub

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | availableaccounts | Não | Não |
> | providerregistrations | Não | Não |
> | rollouts | Não | Não |

## <a name="microsoftquantum"></a>Microsoft.Quantum

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | workspaces | Não | Não |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!IMPORTANT]
> Consulte [Diretrizes de movimentação de Serviços de Recuperação](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | replicationeligibilityresults | Não | Não |
> | vaults | Sim | Sim |

## <a name="microsoftredhatopenshift"></a>Microsoft.RedHatOpenShift

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | openshiftclusters | Não | Não |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | namespaces | Sim | Sim |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | consultas | Sim | Sim |
> | resourcechangedetails | Não | Não |
> | resourcechanges | Não | Não |
> | recursos | Não | Não |
> | resourceshistory | Não | Não |
> | subscriptionsstatus | Não | Não |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | childresources | Não | Não |
> | emergingissues | Não | Não |
> | events | Não | Não |
> | metadata | Não | Não |
> | Notificações | Não | Não |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | deployments | Não | Não |
> | deploymentscripts | Não | Não |
> | deploymentscripts/logs | Não | Não |
> | links | Não | Não |
> | providers | Não | Não |
> | resourcegroups | Não | Não |
> | recursos | Não | Não |
> | subscriptions | Não | Não |
> | marcas | Não | Não |
> | templatespecs | Não | Não |
> | templatespecs/versions | Não | Não |
> | tenants | Não | Não |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | de dimensionamento da Web | Sim | Não |
> | saasresources | Não | Não |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!IMPORTANT]
> Não é possível mover vários recursos de Pesquisa em regiões diferentes em uma operação. Em vez disso, mova-os em operações separadas.

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | resourcehealthmetadata | Não | Não |
> | searchservices | Sim | Sim |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | adaptivenetworkhardenings | Não | Não |
> | advancedthreatprotectionsettings | Não | Não |
> | alertas | Não | Não |
> | allowedconnections | Não | Não |
> | applicationwhitelistings | Não | Não |
> | assessmentmetadata | Não | Não |
> | assessments | Não | Não |
> | autodismissalertsrules | Não | Não |
> | automations | Sim | Sim |
> | autoprovisioningsettings | Não | Não |
> | complianceresults | Não | Não |
> | compliances | Não | Não |
> | datacollectionagents | Não | Não |
> | devicesecuritygroups | Não | Não |
> | discoveredsecuritysolutions | Não | Não |
> | externalsecuritysolutions | Não | Não |
> | informationprotectionpolicies | Não | Não |
> | iotsecuritysolutions | Sim | Sim |
> | iotsecuritysolutions/analyticsmodels | Não | Não |
> | iotsecuritysolutions/analyticsmodels/aggregatedalerts | Não | Não |
> | iotsecuritysolutions/analyticsmodels/aggregatedrecommendations | Não | Não |
> | jitnetworkaccesspolicies | Não | Não |
> | políticas | Não | Não |
> | pricings | Não | Não |
> | regulatorycompliancestandards | Não | Não |
> | regulatorycompliancestandards/regulatorycompliancecontrols | Não | Não |
> | regulatorycompliancestandards/regulatorycompliancecontrols/regulatorycomplianceassessments | Não | Não |
> | securitycontacts | Não | Não |
> | securitysolutions | Não | Não |
> | securitysolutionsreferencedata | Não | Não |
> | securitystatuses | Não | Não |
> | securitystatusessummaries | Não | Não |
> | servervulnerabilityassessments | Não | Não |
> | configurações | Não | Não |
> | subassessments | Não | Não |
> | tarefas | Não | Não |
> | topologies | Não | Não |
> | workspacesettings | Não | Não |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | aggregations | Não | Não |
> | alertrules | Não | Não |
> | alertruletemplates | Não | Não |
> | automationrules | Não | Não |
> | bookmarks | Não | Não |
> | cases | Não | Não |
> | dataconnectors | Não | Não |
> | entidades | Não | Não |
> | entityqueries | Não | Não |
> | incidents | Não | Não |
> | officeconsents | Não | Não |
> | configurações | Não | Não |
> | threatintelligence | Não | Não |

## <a name="microsoftserialconsole"></a>Microsoft.SerialConsole

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | consoleservices | Não | Não |

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | gateways | Não | Não |
> | nós | Não | Não |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | namespaces | Sim | Sim |
> | premiummessagingregions | Não | Não |
> | sku | Não | Não |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | de dimensionamento da Web | Não | Não |
> | clusters | Sim | Sim |
> | containergroups | Não | Não |
> | containergroupsets | Não | Não |
> | edgeclusters | Não | Não |
> | managedclusters | Não | Não |
> | networks | Não | Não |
> | secretstores | Não | Não |
> | volumes | Não | Não |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | de dimensionamento da Web | Sim | Sim |
> | containergroups | Não | Não |
> | gateways | Sim | Sim |
> | networks | Sim | Sim |
> | segredos | Sim | Sim |
> | volumes | Sim | Sim |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | rollouts | Não | Não |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | signalr | Sim | Sim |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hybridusebenefits | Não | Não |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | applicationdefinitions | Não | Não |
> | de dimensionamento da Web | Não | Não |
> | jitrequests | Não | Não |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!IMPORTANT]
> Um banco de dados e o servidor devem estar no mesmo grupo de recursos. Quando você move um SQL Server, todos os seus bancos de dados também são movidos. Esse comportamento se aplica ao banco de dados SQL do Azure e aos bancos de dados do Azure Synapse Analytics.

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | instancepools | Não | Não |
> | Locais | Sim | Sim |
> | managedinstances | Não | Não |
> | servers | Sim | Sim |
> | servers/databases | Sim | Sim |
> | servers/databases/backuplongtermretentionpolicies | Sim | Sim |
> | servers/elasticpools | Sim | Sim |
> | servers/jobaccounts | Sim | Sim |
> | servers/jobagents | Sim | Sim |
> | virtualclusters | Sim | Sim |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | sqlvirtualmachinegroups | Sim | Sim |
> | sqlvirtualmachines | Sim | Sim |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | storageaccounts | Sim | Sim |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | caches | Não | Não |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Sim | Sim |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Não | Não |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Não | Não |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | managers | Não | Não |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!IMPORTANT]
> Os trabalhos do Stream Analytics não podem ser movidos durante o estado de execução.

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | clusters | Não | Não |
> | streamingjobs | Sim | Sim |

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | environments | Não | Não |
> | instances | Não | Não |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | subscriptions | Não | Não |

## <a name="microsoftsupport"></a>microsoft.support

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | services | Não | Não |
> | supporttickets | Não | Não |

## <a name="microsoftsynapse"></a>Microsoft.Synapse

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | workspaces | Não | Não |
> | workspaces/bigdatapools | Não | Não |
> | workspaces/sqlpools | Não | Não |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | environments | Sim | Sim |
> | environments/eventsources | Sim | Sim |
> | environments/referencedatasets | Sim | Sim |

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | stores | Sim | Sim |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | imagetemplates | Não | Não |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio

> [!IMPORTANT]
> Para alterar a assinatura do Azure DevOps, consulte [alterar a assinatura do Azure usada para cobrança](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json).

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | account | Não | Não |
> | account/extension | Não | Não |
> | account/project | Não | Não |

## <a name="microsoftvmware"></a>Microsoft.VMware

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | arczones | Não | Não |
> | resourcepools | Não | Não |
> | vcenters | Não | Não |
> | virtualmachines | Não | Não |
> | virtualmachinetemplates | Não | Não |
> | virtualnetworks | Não | Não |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | dedicatedcloudnodes | Não | Não |
> | dedicatedcloudservices | Não | Não |
> | virtualmachines | Não | Não |

## <a name="microsoftvnfmanager"></a>Microsoft.VnfManager

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | dispositivos | Não | Não |
> | vnfs | Não | Não |

## <a name="microsoftvsonline"></a>Microsoft.VSOnline

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | accounts | Não | Não |
> | planos | Não | Não |
> | registeredsubscriptions | Não | Não |

## <a name="microsoftweb"></a>Microsoft.Web

> [!IMPORTANT]
> Consulte [Diretrizes de movimentação do Serviço de Aplicativo](./move-limitations/app-service-move-limitations.md).

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | availablestacks | Não | Não |
> | billingmeters | Não | Não |
> | certificates | Não | Sim |
> | connectiongateways | Sim | Sim |
> | connections | Sim | Sim |
> | customapis | Sim | Sim |
> | deletedsites | Não | Não |
> | deploymentlocations | Não | Não |
> | georegions | Não | Não |
> | hostingenvironments | Não | Não |
> | kubeenvironments | Sim | Sim |
> | publishingusers | Não | Não |
> | filmes | Não | Não |
> | resourcehealthmetadata | Não | Não |
> | runtimes | Não | Não |
> | serverfarms | Sim | Sim |
> | serverfarms/eventgridfilters | Não | Não |
> | sites | Sim | Sim |
> | sites/premieraddons | Sim | Sim |
> | sites/slots | Sim | Sim |
> | sourcecontrols | Não | Não |
> | staticsites | Não | Não |

## <a name="microsoftwindowsesu"></a>Microsoft.WindowsESU

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | multipleactivationkeys | Não | Não |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | deviceservices | Não | Não |

## <a name="microsoftworkloadbuilder"></a>Microsoft. WorkloadBuilder

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | cargas de trabalho | Não | Não |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | components | Não | Não |
> | componentssummary | Não | Não |
> | monitorinstances | Não | Não |
> | monitorinstancessummary | Não | Não |
> | monitors | Não | Não |

## <a name="third-party-services"></a>Serviços de terceiros

No momento, serviços de terceiros não dão suporte à operação de movimentação.

## <a name="next-steps"></a>Próximas etapas

Para ver comandos para mover recursos, confira [Move resources to new resource group or subscription](move-resource-group-and-subscription.md) (Mover recursos para o novo grupo de recursos ou assinatura).

Para obter os mesmos dados como um arquivo de valores separados por vírgula, baixe [move-support-resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv).
