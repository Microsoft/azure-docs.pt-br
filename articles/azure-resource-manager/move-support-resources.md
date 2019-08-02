---
title: Mover o suporte da operação por tipo de recurso do Azure
description: Lista os tipos de recursos do Azure que podem ser movidos para um novo grupo de recursos ou assinatura.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 7/9/2019
ms.author: tomfitz
ms.openlocfilehash: 22493ad7998e9014c88c79e6ac5eee3bf1216119
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "68226809"
---
# <a name="move-operation-support-for-resources"></a>Mover o suporte de operação para recursos
Este artigo lista se um tipo de recurso do Azure é compatível com a operação de movimentação. Ele também fornece informações sobre condições especiais a serem consideradas ao mover um recurso.

Ir para um namespace do provedor de recursos:
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [microsoft.aadiam](#microsoftaadiam)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft.AppService](#microsoftappservice)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft. backup](#microsoftbackup)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.BatchAI](#microsoftbatchai)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.BizTalkServices](#microsoftbiztalkservices)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft.Container](#microsoftcontainer)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.ContentModerator](#microsoftcontentmoderator)
> - [Microsoft.CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CustomerInsights](#microsoftcustomerinsights)
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
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [microsoft.dns](#microsoftdns)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [microsoft.insights](#microsoftinsights)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.LocationBasedServices](#microsoftlocationbasedservices)
> - [Microsoft.LocationServices](#microsoftlocationservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningCompute](#microsoftmachinelearningcompute)
> - [Microsoft.MachineLearningExperimentation](#microsoftmachinelearningexperimentation)
> - [Microsoft.MachineLearningModelManagement](#microsoftmachinelearningmodelmanagement)
> - [Microsoft.MachineLearningOperationalization](#microsoftmachinelearningoperationalization)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.Media](#microsoftmedia)
> - [Microsoft.Migrate](#microsoftmigrate)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft.Peering](#microsoftpeering)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PortalSdk](#microsoftportalsdk)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.ProjectOxford](#microsoftprojectoxford)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Scheduler](#microsoftscheduler)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.ServerManagement](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.SiteRecovery](#microsoftsiterecovery)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft.Sql](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.SqlVM](#microsoftsqlvm)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft.StorageCache](#microsoftstoragecache)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.StreamAnalyticsExplorer](#microsoftstreamanalyticsexplorer)
> - [Microsoft.TerraformOSS](#microsoftterraformoss)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.Token](#microsofttoken)
> - [Microsoft.VirtualMachineImages](#microsoftvirtualmachineimages)
> - [microsoft.visualstudio](#microsoftvisualstudio)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft.WindowsVirtualDesktop](#microsoftwindowsvirtualdesktop)

## <a name="microsoftaad"></a>Microsoft.AAD
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| domainservices | Não | Não |

## <a name="microsoftaadiam"></a>microsoft.aadiam
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| tenants | Não | Não |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| actionrules | Sim | Sim |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| servers | Sim | Sim |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| serviço | Sim | Sim |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| configurationstores | Sim | Sim |

## <a name="microsoftappservice"></a>Microsoft.AppService
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| apiapps | Não | Não |
| appidentities | Não | Não |
| gateways | Não | Não |

> [!IMPORTANT]
> Consulte [diretrizes de movimentação do serviço de aplicativo](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftauthorization"></a>Microsoft.Authorization
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| policyassignments | Não | Não |

## <a name="microsoftautomation"></a>Microsoft.Automation
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| automationaccounts | Sim | Sim |
| automationaccounts/configurations | Sim | Sim |
| automationaccounts/runbooks | Sim | Sim |

> [!IMPORTANT]
> Os Runbooks devem existir no mesmo grupo de recursos que a conta de automação.

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| b2cdirectories | Sim | Sim |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| registrations | Sim | Sim |

## <a name="microsoftbackup"></a>Microsoft.Backup
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| backupvault | Não | Não |

## <a name="microsoftbatch"></a>Microsoft.Batch
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| batchaccounts | Sim | Sim |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| clusters | Não | Não |
| fileservers | Não | Não |
| jobs | Não | Não |
| workspaces | Não | Não |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| mapapis | Não | Não |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| biztalk | Sim | Sim |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| blockchainmembers | Sim | Sim |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| blueprintassignments | Não | Não |

## <a name="microsoftbotservice"></a>Microsoft.BotService
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| botservices | Sim | Sim |

## <a name="microsoftcache"></a>Microsoft.Cache
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| redis | Sim | Sim |

> [!IMPORTANT]
> Se o cache do Azure para instância Redis estiver configurado com uma rede virtual, a instância não poderá ser movida para uma assinatura diferente. Consulte [limitações de movimentação de redes virtuais](./move-limitations/virtual-network-move-limitations.md).

## <a name="microsoftcdn"></a>Microsoft.Cdn
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| profiles | Sim | Sim |
| profiles/endpoints | Sim | Sim |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| certificateorders | Sim | Sim |

> [!IMPORTANT]
> Consulte [diretrizes de movimentação do serviço de aplicativo](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| domainnames | Sim | Não |
| virtualmachines | Sim | Não |

> [!IMPORTANT]
> Consulte [diretrizes de movimentação da implantação clássica](./move-limitations/classic-model-move-limitations.md). Os recursos de implantação clássicos podem ser movidos entre assinaturas com uma operação específica para esse cenário.

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| networksecuritygroups | Não | Não |
| reservedips | Não | Não |
| virtualnetworks | Não | Não |

> [!IMPORTANT]
> Consulte [diretrizes de movimentação da implantação clássica](./move-limitations/classic-model-move-limitations.md). Os recursos de implantação clássicos podem ser movidos entre assinaturas com uma operação específica para esse cenário.

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| storageaccounts | Sim | Não |

> [!IMPORTANT]
> Consulte [diretrizes de movimentação da implantação clássica](./move-limitations/classic-model-move-limitations.md). Os recursos de implantação clássicos podem ser movidos entre assinaturas com uma operação específica para esse cenário.

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| accounts | Sim | Sim |

## <a name="microsoftcompute"></a>Microsoft.Compute
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| availabilitysets | Sim | Sim |
| disks | Sim | Sim |
| galleries | Não | Não |
| galleries/images | Não | Não |
| galleries/images/versions | Não | Não |
| hosts | Não | Não |
| hosts/hosts | Não | Não |
| images | Sim | Sim |
| proximityplacementgroups | Não | Não |
| restorepointcollections | Não | Não |
| sharedvmimages | Não | Não |
| sharedvmimages/versions | Não | Não |
| snapshots | Sim | Sim |
| virtualmachines | Sim | Sim |
| virtualmachines/extensions | Sim | Sim |
| virtualmachinescalesets | Sim | Sim |

> [!IMPORTANT]
> Consulte as [diretrizes de movimentação de máquinas virtuais](./move-limitations/virtual-machines-move-limitations.md).

## <a name="microsoftcontainer"></a>Microsoft.Container
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| containergroups | Não | Não |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| containergroups | Não | Não |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| registries | Sim | Sim |
| registries/buildtasks | Sim | Sim |
| registries/replications | Sim | Sim |
| registries/tasks | Sim | Sim |
| registries/webhooks | Sim | Sim |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| containerservices | Não | Não |
| managedclusters | Não | Não |
| openshiftmanagedclusters | Não | Não |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| de dimensionamento da Web | Sim | Sim |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| accounts | Não | Não |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| conectores | Sim | Sim |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| hubs | Sim | Sim |

## <a name="microsoftdatabox"></a>Microsoft.DataBox
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| jobs | Não | Não |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| databoxedgedevices | Não | Não |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| workspaces | Não | Não |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| catalogs | Sim | Sim |
| catálogos de | Não | Não |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| connectionmanagers | Não | Não |

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| pacotes | Não | Não |
| planos | Não | Não |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| datafactories | Sim | Sim |
| factories | Sim | Sim |

## <a name="microsoftdatalake"></a>Microsoft.DataLake
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| datalakeaccounts | Não | Não |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| accounts | Sim | Sim |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| accounts | Sim | Sim |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| services | Não | Não |
| services/projects | Não | Não |
| slots | Não | Não |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| servers | Sim | Sim |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| servers | Sim | Sim |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| servergroups | Não | Não |
| servers | Sim | Sim |
| serversv2 | Sim | Sim |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| artifactsources | Sim | Sim |
| rollouts | Sim | Sim |
| servicetopologies | Sim | Sim |
| servicetopologies/services | Sim | Sim |
| servicetopologies/services/serviceunits | Sim | Sim |
| etapas | Sim | Sim |

## <a name="microsoftdevices"></a>Microsoft.Devices
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| elasticpools | Não | Não |
| elasticpools/iothubtenants | Não | Não |
| iothubs | Sim | Sim |
| provisioningservices | Sim | Sim |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| controladores | Não | Não |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| labcenters | Não | Não |
| labs | Sim | Não |
| laboratórios/ambientes | Sim | Sim |
| labs/servicerunners | Sim | Sim |
| labs/virtualmachines | Sim | Não |
| schedules | Sim | Sim |

## <a name="microsoftdns"></a>microsoft.dns
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| dnszones | Não | Não |
| dnszones/a | Não | Não |
| dnszones/aaaa | Não | Não |
| dnszones/cname | Não | Não |
| dnszones/mx | Não | Não |
| dnszones/ptr | Não | Não |
| dnszones/srv | Não | Não |
| dnszones/txt | Não | Não |
| trafficmanagerprofiles | Não | Não |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| databaseaccounts | Sim | Sim |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| domains | Sim | Sim |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| services | Sim | Sim |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| domains | Sim | Sim |
| topics | Sim | Sim |

## <a name="microsofteventhub"></a>Microsoft.EventHub
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| clusters | Sim | Sim |
| namespaces | Sim | Sim |

## <a name="microsoftgenomics"></a>Microsoft.Genomics
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| accounts | Não | Não |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| hanainstances | Sim | Sim |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| clusters | Sim | Sim |

> [!IMPORTANT]
> Você pode mover os clusters HDInsight para uma nova assinatura ou grupo de recursos. No entanto, não é possível mover os recursos de rede vinculados ao cluster HDInsight (por exemplo, a rede virtual, NIC ou balanceador de carga) entre assinaturas. Além disso, não é possível mover uma para um novo grupo de recursos uma NIC que está conectada a uma máquina virtual para o cluster.
>
> Ao mover um cluster HDInsight para uma nova assinatura, mova primeiro os outros recursos (como a conta de armazenamento). Em seguida, mova apenas o cluster HDInsight.

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| services | Sim | Sim |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| maquina | Não | Não |

## <a name="microsofthybriddata"></a>Microsoft.HybridData
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| datamanagers | Sim | Sim |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| jobs | Sim | Sim |

## <a name="microsoftinsights"></a>microsoft.insights
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| accounts | Não | Não |
| actiongroups | Sim | Sim |
| activitylogalerts | Não | Não |
| alertrules | Sim | Sim |
| autoscalesettings | Sim | Sim |
| components | Sim | Sim |
| guestdiagnosticsettings | Não | Não |
| metricalerts | Não | Não |
| notificationgroups | Não | Não |
| notificationrules | Não | Não |
| scheduledqueryrules | Sim | Sim |
| webtests | Sim | Sim |
| workbooks | Sim | Sim |

> [!IMPORTANT]
> Certifique-se de que a mudança para a nova assinatura não exceda as cotas de [assinatura](../azure-subscription-service-limits.md#azure-monitor-limits).

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| iotapps | Sim | Sim |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| checknameavailability | Sim | Sim |
| graph | Sim | Sim |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| hsmpools | Não | Não |
| vaults | Sim | Sim |

> [!IMPORTANT]
> Os cofres de chaves usados para criptografia de disco não podem ser movidos para um grupo de recursos na mesma assinatura ou entre assinaturas.

## <a name="microsoftkusto"></a>Microsoft.Kusto
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| clusters | Sim | Sim |

## <a name="microsoftlabservices"></a>Microsoft.LabServices
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| labaccounts | Não | Não |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| accounts | Sim | Sim |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| accounts | Não | Não |

## <a name="microsoftlogic"></a>Microsoft.Logic
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| hostingenvironments | Não | Não |
| integrationaccounts | Sim | Sim |
| integrationserviceenvironments | Não | Não |
| isolatedenvironments | Não | Não |
| workflows | Sim | Sim |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| commitmentplans | Sim | Sim |
| webservices | Sim | Não |
| workspaces | Sim | Sim |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| operationalizationclusters | Sim | Sim |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| accounts | Não | Não |
| accounts/workspaces | Não | Não |
| accounts/workspaces/projects | Não | Não |
| teamaccounts | Não | Não |
| teamaccounts/workspaces | Não | Não |
| teamaccounts/workspaces/projects | Não | Não |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| accounts | Sim | Sim |

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft.MachineLearningOperationalization
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| hostingaccounts | Não | Não |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| workspaces | Não | Não |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| userassignedidentities | Não | Não |

## <a name="microsoftmaps"></a>Microsoft.Maps
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| accounts | Sim | Sim |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| classicdevservices | Não | Não |

## <a name="microsoftmedia"></a>Microsoft.Media
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| mediaservices | Sim | Sim |
| mediaservices/liveevents | Sim | Sim |
| mediaservices/streamingendpoints | Sim | Sim |

## <a name="microsoftmigrate"></a>Microsoft.Migrate
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| assessmentprojects | Não | Não |
| migrateprojects | Não | Não |
| projects | Não | Não |

## <a name="microsoftnetapp"></a>Microsoft.NetApp
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| netappaccounts | Não | Não |
| netappaccounts/capacitypools | Não | Não |
| netappaccounts/capacitypools/volumes | Não | Não |
| netappaccounts/capacitypools/volumes/mounttargets | Não | Não |
| netappaccounts/capacitypools/volumes/snapshots | Não | Não |

## <a name="microsoftnetwork"></a>Microsoft.Network
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| applicationgateways | Não | Não |
| applicationgatewaywebapplicationfirewallpolicies | Não | Não |
| applicationsecuritygroups | Sim | Sim |
| azurefirewalls | Sim | Sim |
| bastionhosts | Não | Não |
| connections | Sim | Sim |
| ddoscustompolicies | Sim | Sim |
| ddosprotectionplans | Não | Não |
| dnszones | Sim | Sim |
| expressroutecircuits | Não | Não |
| expressroutecrossconnections | Não | Não |
| expressroutegateways | Não | Não |
| expressrouteports | Não | Não |
| frontdoors | Não | Não |
| frontdoorwebapplicationfirewallpolicies | Não | Não |
| loadbalancers | Sim-SKU básico<br>Não-SKU padrão | Sim-SKU básico<br>Não-SKU padrão |
| localnetworkgateways | Sim | Sim |
| natgateways | Sim | Sim |
| networkintentpolicies | Sim | Sim |
| networkinterfaces | Sim | Sim |
| networkprofiles | Não | Não |
| networksecuritygroups | Sim | Sim |
| networkwatchers | Sim | Sim |
| networkwatchers/connectionmonitors | Sim | Sim |
| networkwatchers/lenses | Sim | Sim |
| networkwatchers/pingmeshes | Sim | Sim |
| p2svpngateways | Não | Não |
| privatednszones | Sim | Sim |
| privatednszones/virtualnetworklinks | Sim | Sim |
| privateendpoints | Não | Não |
| privatelinkservices | Não | Não |
| publicipaddresses | Sim-SKU básico<br>Não-SKU padrão | Sim-SKU básico<br>Não-SKU padrão |
| publicipprefixes | Sim | Sim |
| routefilters | Não | Não |
| routetables | Sim | Sim |
| securegateways | Sim | Sim |
| serviceendpointpolicies | Sim | Sim |
| trafficmanagerprofiles | Sim | Sim |
| virtualhubs | Não | Não |
| virtualnetworkgateways | Sim | Sim |
| virtualnetworks | Sim | Sim |
| virtualnetworktaps | Não | Não |
| virtualwans | Não | Não |
| vpngateways | Não | Não |
| vpnsites | Não | Não |
| webapplicationfirewallpolicies | Sim | Sim |

> [!IMPORTANT]
> Consulte [diretrizes de movimentação de redes virtuais](./move-limitations/virtual-network-move-limitations.md).

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| namespaces | Sim | Sim |
| namespaces/notificationhubs | Sim | Sim |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| workspaces | Sim | Sim |

> [!IMPORTANT]
> Certifique-se de que a mudança para a nova assinatura não exceda as cotas de [assinatura](../azure-subscription-service-limits.md#azure-monitor-limits).

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| managementconfigurations | Sim | Sim |
| solutions | Sim | Sim |
| Modos de exibição | Sim | Sim |

## <a name="microsoftpeering"></a>Microsoft.Peering
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| emparelhamentos | Não | Não |

## <a name="microsoftportal"></a>Microsoft.Portal
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| dashboards | Sim | Sim |

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| rootresources | Não | Não |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| workspacecollections | Sim | Sim |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| capacities | Sim | Sim |

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| accounts | Não | Não |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| vaults | Sim | Sim |

> [!IMPORTANT]
> Consulte [diretrizes de movimentação dos serviços de recuperação](../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftrelay"></a>Microsoft.Relay
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| namespaces | Sim | Sim |

## <a name="microsoftsaas"></a>Microsoft.SaaS
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| de dimensionamento da Web | Sim | Não |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| flows | Sim | Sim |
| jobcollections | Sim | Sim |

## <a name="microsoftsearch"></a>Microsoft.Search
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| searchservices | Sim | Sim |

> [!IMPORTANT]
> Você não pode mover vários recursos de pesquisa em regiões diferentes em uma única operação. Em vez disso, mova-os em operações separadas.

## <a name="microsoftsecurity"></a>Microsoft.Security
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| iotsecuritysolutions | Sim | Sim |

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| gateways | Não | Não |
| nós | Não | Não |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| namespaces | Sim | Sim |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| de dimensionamento da Web | Não | Não |
| clusters | Sim | Sim |
| containergroups | Não | Não |
| containergroupsets | Não | Não |
| edgeclusters | Não | Não |
| networks | Não | Não |
| secretstores | Não | Não |
| volumes | Não | Não |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| de dimensionamento da Web | Sim | Sim |
| containergroups | Não | Não |
| gateways | Sim | Sim |
| networks | Sim | Sim |
| segredos | Sim | Sim |
| volumes | Sim | Sim |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| signalr | Sim | Sim |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| siterecoveryvault | Não | Não |

> [!IMPORTANT]
> Consulte [diretrizes de movimentação dos serviços de recuperação](../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftsolutions"></a>Microsoft.Solutions
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| appliancedefinitions | Não | Não |
| appliances | Não | Não |
| applicationdefinitions | Não | Não |
| de dimensionamento da Web | Não | Não |
| jitrequests | Não | Não |

## <a name="microsoftsql"></a>Microsoft.Sql
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| instancepools | Não | Não |
| managedinstances | Não | Não |
| managedinstances/databases | Não | Não |
| servers | Sim | Sim |
| servers/databases | Sim | Sim |
| servidores/elasticpools | Sim | Sim |
| virtualclusters | Sim | Sim |

> [!IMPORTANT]
> Um banco de dados e um servidor devem estar no mesmo grupo de recursos. Quando você move um SQL Server, todos os seus bancos de dados também são movidos. Este comportamento se aplica ao Banco de Dados SQL do Azure e ao banco de dados SQL Data Warehouse do Azure.

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| sqlvirtualmachinegroups | Sim | Sim |
| sqlvirtualmachines | Sim | Sim |

## <a name="microsoftsqlvm"></a>Microsoft.SqlVM
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| dwvm | Não | Não |

## <a name="microsoftstorage"></a>Microsoft.Storage
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| storageaccounts | Sim | Sim |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| caches | Não | Não |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| storagesyncservices | Sim | Sim |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| storagesyncservices | Não | Não |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| storagesyncservices | Não | Não |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| managers | Não | Não |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| streamingjobs | Sim | Sim |

> [!IMPORTANT]
> Stream Analytics trabalhos não podem ser movidos quando estiver em estado de execução.

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| environments | Não | Não |
| environments/eventsources | Não | Não |
| instances | Não | Não |
| instances/environments | Não | Não |
| instances/environments/eventsources | Não | Não |

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| providerregistrations | Não | Não |
| recursos | Não | Não |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| environments | Sim | Sim |
| environments/eventsources | Sim | Sim |
| environments/referencedatasets | Sim | Sim |

## <a name="microsofttoken"></a>Microsoft.Token
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| mercado | Não | Não |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| imagetemplates | Não | Não |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| conta | Sim | Sim |
| account/extension | Sim | Sim |
| account/project | Sim | Sim |

> [!IMPORTANT]
> Para alterar a assinatura do Azure DevOps, consulte [alterar a assinatura do Azure usada para cobrança](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| dedicatedcloudnodes | Sim | Sim |
| dedicatedcloudservices | Sim | Sim |
| virtualmachines | Sim | Sim |

## <a name="microsoftweb"></a>Microsoft.Web
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| certificates | Não | Sim |
| connectiongateways | Sim | Sim |
| connections | Sim | Sim |
| customapis | Sim | Sim |
| hostingenvironments | Não | Não |
| serverfarms | Sim | Sim |
| sites | Sim | Sim |
| sites/premieraddons | Sim | Sim |
| sites/slots | Sim | Sim |

> [!IMPORTANT]
> Consulte [diretrizes de movimentação do serviço de aplicativo](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| deviceservices | Não | Não |

## <a name="microsoftwindowsvirtualdesktop"></a>Microsoft.WindowsVirtualDesktop
| Tipo de recurso | Grupo de recursos | Assinatura |
| ------------- | ----------- | ---------- |
| applicationgroups | Não | Não |
| hostpools | Não | Não |
| workspaces | Não | Não |

## <a name="third-party-services"></a>Serviços de terceiros

No momento, serviços de terceiros não dão suporte à operação de movimentação.

## <a name="next-steps"></a>Próximas etapas
Para ver comandos para mover recursos, confira [Move resources to new resource group or subscription](resource-group-move-resources.md) (Mover recursos para o novo grupo de recursos ou assinatura).

Para obter os mesmos dados como um arquivo de valores separados por vírgula, baixe [move-support-resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv).
