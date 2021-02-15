---
title: Suporte a marcas para recursos
description: Mostra quais tipos de recursos do Azure suportam tags. Fornece detalhes para todos os serviços do Azure.
ms.topic: conceptual
ms.date: 10/21/2020
ms.openlocfilehash: 12a57577241e3b2206aab9c3badb29502e7b0301
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97656964"
---
# <a name="tag-support-for-azure-resources"></a>Suporte a marcas para recursos do Azure
Este artigo descreve se um tipo de recurso dá suporte a [marcas](tag-resources.md). A coluna rotulada **dá suporte a marcas** indica se o tipo de recurso tem uma propriedade para a marca. A coluna rotulada como **marca no relatório de custo** indica se esse tipo de recurso passa a marca para o relatório de custo. Você pode exibir os custos por marcas na [análise de custo de gerenciamento de custos](../../cost-management-billing/costs/group-filter.md) e na [fatura de cobrança do Azure e nos dados de uso diário](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md).

Para obter os mesmos dados como um arquivo de valores separados por vírgula, baixe [tag-support.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/tag-support.csv).

Pule para um namespace de provedor de recursos:
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [Microsoft.Addons](#microsoftaddons)
> - [Microsoft.ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft.Advisor](#microsoftadvisor)
> - [Microsoft. AgFoodPlatform](#microsoftagfoodplatform)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft.AppPlatform](#microsoftappplatform)
> - [Microsoft.Attestation](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft. autogerenci](#microsoftautomanage)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.AVS](#microsoftavs)
> - [Microsoft.Azure.Geneva](#microsoftazuregeneva)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.AzureStackHCI](#microsoftazurestackhci)
> - [Microsoft. BareMetalInfrastructure](#microsoftbaremetalinfrastructure)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.Billing](#microsoftbilling)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.BlockchainTokens](#microsoftblockchaintokens)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Capacity](#microsoftcapacity)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ChangeAnalysis](#microsoftchangeanalysis)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft.ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft. Codespaces](#microsoftcodespaces)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Commerce](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft. ConnectedCache](#microsoftconnectedcache)
> - [Microsoft.Consumption](#microsoftconsumption)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CustomerLockbox](#microsoftcustomerlockbox)
> - [Microsoft.CustomProviders](#microsoftcustomproviders)
> - [Microsoft. D365CustomerInsights](#microsoftd365customerinsights)
> - [Microsoft.DataBox](#microsoftdatabox)
> - [Microsoft.DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft.Databricks](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [Microsoft.DataFactory](#microsoftdatafactory)
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
> - [Microsoft. DeviceUpdate](#microsoftdeviceupdate)
> - [Microsoft.DevOps](#microsoftdevops)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DigitalTwins](#microsoftdigitaltwins)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.DynamicsLcs](#microsoftdynamicslcs)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Experimentation](#microsoftexperimentation)
> - [Microsoft.Falcon](#microsoftfalcon)
> - [Microsoft.Features](#microsoftfeatures)
> - [Microsoft.Gallery](#microsoftgallery)
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
> - [Microsoft. insights](#microsoftinsights)
> - [Microsoft.Intune](#microsoftintune)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kubernetes](#microsoftkubernetes)
> - [Microsoft.KubernetesConfiguration](#microsoftkubernetesconfiguration)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
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
> - [Microsoft. notebooks](#microsoftnotebooks)
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
> - [Microsoft. ScVmm](#microsoftscvmm)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.SecurityGraph](#microsoftsecuritygraph)
> - [Microsoft.SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft.SerialConsole](#microsoftserialconsole)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.Services](#microsoftservices)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft. singularidade](#microsoftsingularity)
> - [Microsoft.SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft. SQL](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft.StorageCache](#microsoftstoragecache)
> - [Microsoft. StorageReplication](#microsoftstoragereplication)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.Subscription](#microsoftsubscription)
> - [Microsoft.Synapse](#microsoftsynapse)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.Token](#microsofttoken)
> - [Microsoft.VirtualMachineImages](#microsoftvirtualmachineimages)
> - [Microsoft.VMware](#microsoftvmware)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.VnfManager](#microsoftvnfmanager)
> - [Microsoft.VSOnline](#microsoftvsonline)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsDefenderATP](#microsoftwindowsdefenderatp)
> - [Microsoft.WindowsESU](#microsoftwindowsesu)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft. WorkloadBuilder](#microsoftworkloadbuilder)
> - [Microsoft.WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | DomainServices | Sim | Sim |
> | DomainServices/oucontainer | Não | Não |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | supportProviders | Não | Não |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
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
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | advisorScore | Não | Não |
> | configurações | Não | Não |
> | generateRecommendations | Não | Não |
> | metadata | Não | Não |
> | filmes | Não | Não |
> | suppressions | Não | Não |

## <a name="microsoftagfoodplatform"></a>Microsoft. AgFoodPlatform

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | farmBeats | Sim | Sim |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | actionRules | Sim | Sim |
> | alertas | Não | Não |
> | alertsList | Não | Não |
> | alertsMetaData | Não | Não |
> | alertsSummary | Não | Não |
> | alertsSummaryList | Não | Não |
> | smartDetectorAlertRules | Sim | Sim |
> | smartGroups | Não | Não |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | servers | Sim | Sim |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | reportFeedback | Não | Não |
> | serviço | Sim | Sim |
> | validateServiceName | Não | Não |

> [!NOTE]
> O gerenciamento de API do Azure só dá suporte à criação de um máximo de 15 pares de nome/valor de marca para cada serviço.

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | configurationStores | Sim | Não |
> | configurationStores / eventGridFilters | Não | Não |
> | configurationStores/valores de valor | Não | Não |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | Spring | Sim | Sim |
> | Spring/apps | Não | Não |
> | Spring/aplicativos/implantações | Não | Não |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | attestationProviders | Sim | Sim |
> | defaultfornecedores | Não | Não |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | accessReviewScheduleDefinitions | Não | Não |
> | accessReviewScheduleSettings | Não | Não |
> | classicAdministrators | Não | Não |
> | Aliases de dataalias | Não | Não |
> | denyAssignments | Não | Não |
> | elevateAccess | Não | Não |
> | findOrphanRoleAssignments | Não | Não |
> | locks | Não | Não |
> | permissões | Não | Não |
> | policyAssignments | Não | Não |
> | policyDefinitions | Não | Não |
> | policyExemptions | Não | Não |
> | policySetDefinitions | Não | Não |
> | privateLinkAssociations | Não | Não |
> | providerOperations | Não | Não |
> | resourceManagementPrivateLinks | Sim | Sim |
> | roleAssignments | Não | Não |
> | roleAssignmentsUsageMetrics | Não | Não |
> | roleDefinitions | Não | Não |

## <a name="microsoftautomanage"></a>Microsoft. autogerenci

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | accounts | Sim | Sim |
> | configurationProfileAssignments | Não | Não |
> | configurationProfilePreferences | Sim | Sim |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | automationaccounts | Sim | Sim |
> | automationAccounts/configurações | Sim | Sim |
> | automationAccounts/trabalhos | Não | Não |
> | automationAccounts/privateEndpointConnectionProxies | Não | Não |
> | automationAccounts/privateEndpointConnections | Não | Não |
> | automationAccounts/privateLinkResources | Não | Não |
> | automationAccounts / runbooks | Sim | Sim |
> | automationAccounts/softwareUpdateConfigurations | Não | Não |
> | automationAccounts / webhooks | Não | Não |

> [!NOTE]
> A automação do Azure só dá suporte à criação de, no máximo, 15 pares de nome/valor de marca para cada recurso de automação.

## <a name="microsoftavs"></a>Microsoft.AVS

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | privateClouds | Sim | Sim |
> | privateClouds/Complementos | Não | Não |
> | privateClouds/autorizações | Não | Não |
> | privateClouds/clusters | Não | Não |
> | privateClouds / globalReachConnections | Não | Não |
> | privateClouds / hcxEnterpriseSites | Não | Não |
> | privateClouds / workloadNetworks | Não | Não |
> | privateClouds / workloadNetworks / dhcpConfigurations | Não | Não |
> | privateClouds/workloadNetworks/gateways | Não | Não |
> | privateClouds / workloadNetworks / portMirroringProfiles | Não | Não |
> | privateClouds/workloadNetworks/segmentos | Não | Não |
> | privateClouds/workloadNetworks/virtualMachines | Não | Não |
> | privateClouds / workloadNetworks / vmGroups | Não | Não |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | environments | Não | Não |
> | ambientes/contas | Não | Não |
> | ambientes/contas/namespaces | Não | Não |
> | ambientes/contas/namespaces/configurações | Não | Não |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | b2cDirectories | Sim | Não |
> | b2ctenants | Não | Não |
> | guestUsages | Sim | Sim |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | datacontrollers | Sim | Sim |
> | postgresInstances | Sim | Sim |
> | sqlManagedInstances | Sim | Sim |
> | sqlServerInstances | Sim | Sim |
> | sqlServerRegistrations | Sim | Sim |
> | sqlServerRegistrations/sqlservers | Não | Não |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | cloudManifestFiles | Não | Não |
> | edgeSubscriptions | Sim | Sim |
> | linkedSubscriptions | Sim | Sim |
> | registrations | Sim | Sim |
> | registros/customerSubscriptions | Não | Não |
> | registrations/products | Não | Não |

## <a name="microsoftazurestackhci"></a>Microsoft.AzureStackHCI

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | clusters | Sim | Sim |

## <a name="microsoftbaremetalinfrastructure"></a>Microsoft. BareMetalInfrastructure

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | bareMetalInstances | Sim | Sim |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | batchAccounts | Sim | Sim |
> | batchAccounts / certificates | Não | Não |
> | batchAccounts / pools | Não | Não |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | billingAccounts | Não | Não |
> | billingAccounts/contratos | Não | Não |
> | billingAccounts / billingPermissions | Não | Não |
> | billingAccounts / billingProfiles | Não | Não |
> | billingAccounts / billingProfiles / billingPermissions | Não | Não |
> | billingAccounts / billingProfiles / billingRoleAssignments | Não | Não |
> | billingAccounts / billingProfiles / billingRoleDefinitions | Não | Não |
> | billingAccounts / billingProfiles / billingSubscriptions | Não | Não |
> | billingAccounts / billingProfiles / createBillingRoleAssignment | Não | Não |
> | billingAccounts/billingProfiles/clientes | Não | Não |
> | billingAccounts/billingProfiles/instruções | Não | Não |
> | billingAccounts/billingProfiles/faturas | Não | Não |
> | billingAccounts/billingProfiles/faturas/pricesheet | Não | Não |
> | billingAccounts/billingProfiles/faturas/transações | Não | Não |
> | billingAccounts / billingProfiles / invoiceSections | Não | Não |
> | billingAccounts / billingProfiles / invoiceSections / billingPermissions | Não | Não |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleAssignments | Não | Não |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleDefinitions | Não | Não |
> | billingAccounts / billingProfiles / invoiceSections / billingSubscriptions | Não | Não |
> | billingAccounts / billingProfiles / invoiceSections / createBillingRoleAssignment | Não | Não |
> | billingAccounts / billingProfiles / invoiceSections / initiateTransfer | Não | Não |
> | billingAccounts/billingProfiles/invoiceSections/produtos | Não | Não |
> | billingAccounts/billingProfiles/invoiceSections/produtos/transferência | Não | Não |
> | billingAccounts/billingProfiles/invoiceSections/Products/updateAutoRenew | Não | Não |
> | billingAccounts/billingProfiles/invoiceSections/transações | Não | Não |
> | billingAccounts/billingProfiles/invoiceSections/transferências | Não | Não |
> | billingAccounts / BillingProfiles / patchOperations | Não | Não |
> | billingAccounts / billingProfiles / paymentMethods | Não | Não |
> | billingAccounts/billingProfiles/políticas | Não | Não |
> | billingAccounts/billingProfiles/pricesheet | Não | Não |
> | billingAccounts / billingProfiles / pricesheetDownloadOperations | Não | Não |
> | billingAccounts/billingProfiles/produtos | Não | Não |
> | billingAccounts/billingProfiles/reservas | Não | Não |
> | billingAccounts/billingProfiles/transações | Não | Não |
> | billingAccounts / billingProfiles / validateDetachPaymentMethodEligibility | Não | Não |
> | billingAccounts / billingRoleAssignments | Não | Não |
> | billingAccounts / billingRoleDefinitions | Não | Não |
> | billingAccounts / billingSubscriptions | Não | Não |
> | billingAccounts/billingSubscriptions/faturas | Não | Não |
> | billingAccounts / createBillingRoleAssignment | Não | Não |
> | billingAccounts / createInvoiceSectionOperations | Não | Não |
> | billingAccounts/clientes | Não | Não |
> | billingAccounts/clientes/billingPermissions | Não | Não |
> | billingAccounts/clientes/billingSubscriptions | Não | Não |
> | billingAccounts/clientes/initiateTransfer | Não | Não |
> | billingAccounts/clientes/políticas | Não | Não |
> | billingAccounts/clientes/produtos | Não | Não |
> | billingAccounts/clientes/transações | Não | Não |
> | billingAccounts/clientes/transferências | Não | Não |
> | billingAccounts/departamentos | Não | Não |
> | billingAccounts/departamentos/billingPermissions | Não | Não |
> | billingAccounts/departamentos/billingRoleAssignments | Não | Não |
> | billingAccounts/departamentos/billingRoleDefinitions | Não | Não |
> | billingAccounts / enrollmentAccounts | Não | Não |
> | billingAccounts / enrollmentAccounts / billingPermissions | Não | Não |
> | billingAccounts / enrollmentAccounts / billingRoleAssignments | Não | Não |
> | billingAccounts / enrollmentAccounts / billingRoleDefinitions | Não | Não |
> | billingAccounts/faturas | Não | Não |
> | billingAccounts/faturas/transações | Não | Não |
> | billingAccounts / invoiceSections | Não | Não |
> | billingAccounts / invoiceSections / billingSubscriptionMoveOperations | Não | Não |
> | billingAccounts / invoiceSections / billingSubscriptions | Não | Não |
> | billingAccounts/invoiceSections/billingSubscriptions/transferência | Não | Não |
> | billingAccounts/invoiceSections/Elevate | Não | Não |
> | billingAccounts / invoiceSections / initiateTransfer | Não | Não |
> | billingAccounts / invoiceSections / patchOperations | Não | Não |
> | billingAccounts / invoiceSections / productMoveOperations | Não | Não |
> | billingAccounts/invoiceSections/produtos | Não | Não |
> | billingAccounts/invoiceSections/produtos/transferência | Não | Não |
> | billingAccounts/invoiceSections/Products/updateAutoRenew | Não | Não |
> | billingAccounts/invoiceSections/transações | Não | Não |
> | billingAccounts/invoiceSections/transferências | Não | Não |
> | billingAccounts / lineOfCredit | Não | Não |
> | billingAccounts / patchOperations | Não | Não |
> | billingAccounts / paymentMethods | Não | Não |
> | billingAccounts/produtos | Não | Não |
> | billingAccounts/reservas | Não | Não |
> | billingAccounts/transações | Não | Não |
> | billingPeriods | Não | Não |
> | billingPermissions | Não | Não |
> | billingProperty | Não | Não |
> | billingRoleAssignments | Não | Não |
> | billingRoleDefinitions | Não | Não |
> | createBillingRoleAssignment | Não | Não |
> | departments | Não | Não |
> | enrollmentAccounts | Não | Não |
> | invoices | Não | Não |
> | transfers | Não | Não |
> | transferências/acceptTransfer | Não | Não |
> | transferências/declineTransfer | Não | Não |
> | transferências/operationStatus | Não | Não |
> | transferências/validateTransfer | Não | Não |
> | validateAddress | Não | Não |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | mapApis | Sim | Sim |
> | updateCommunicationPreference | Não | Não |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | blockchainMembers | Sim | Sim |
> | cordaMembers | Sim | Sim |
> | watchers | Sim | Sim |

## <a name="microsoftblockchaintokens"></a>Microsoft.BlockchainTokens

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | Tokenservices | Sim | Sim |
> | Tokenservices/BlockchainNetworks | Não | Não |
> | Tokenservices/grupos | Não | Não |
> | Tokenservices/grupos/contas | Não | Não |
> | Tokenservices/TokenTemplates | Não | Não |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | blueprintAssignments | Não | Não |
> | blueprintAssignments / assignmentOperations | Não | Não |
> | blueprintAssignments/operações | Não | Não |
> | blueprints | Não | Não |
> | blueprints/artifacts | Não | Não |
> | blueprints/versions | Não | Não |
> | blueprints/versions/artifacts | Não | Não |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | botServices | Sim | Sim |
> | botServices / channels | Não | Não |
> | botServices/conexões | Não | Não |
> | idiomas | Não | Não |
> | modelos | Não | Não |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | Redis | Sim | Sim |
> | Redis/EventGridFilters | Não | Não |
> | Redis/privateEndpointConnectionProxies | Não | Não |
> | Redis/privateEndpointConnectionProxies/validar | Não | Não |
> | Redis/privateEndpointConnections | Não | Não |
> | Redis/privateLinkResources | Não | Não |
> | redisEnterprise | Sim | Sim |
> | RedisEnterprise / privateEndpointConnectionProxies | Não | Não |
> | RedisEnterprise/privateEndpointConnectionProxies/validar | Não | Não |
> | RedisEnterprise / privateEndpointConnections | Não | Não |
> | RedisEnterprise / privateLinkResources | Não | Não |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | appliedReservations | Não | Não |
> | autoQuotaIncrease | Não | Não |
> | calculateExchange | Não | Não |
> | calculatePrice | Não | Não |
> | calculatePurchasePrice | Não | Não |
> | catalogs | Não | Não |
> | commercialReservationOrders | Não | Não |
> | câmbio | Não | Não |
> | ownReservations | Não | Não |
> | placePurchaseOrder | Não | Não |
> | reservationOrders | Não | Não |
> | reservationOrders / calculateRefund | Não | Não |
> | reservationOrders/mesclagem | Não | Não |
> | reservationOrders/reservas | Não | Não |
> | reservationOrders/reservas/revisões | Não | Não |
> | reservationOrders/retorno | Não | Não |
> | reservationOrders/divisão | Não | Não |
> | reservationOrders/permuta | Não | Não |
> | reservations | Não | Não |
> | resourceProviders | Não | Não |
> | recursos | Não | Não |
> | validateReservationOrder | Não | Não |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | Não | Não |
> | CdnWebApplicationFirewallPolicies | Sim | Sim |
> | edgenodes | Não | Não |
> | perfis | Sim | Sim |
> | profiles/endpoints | Sim | Sim |
> | profiles/endpoints/customdomains | Não | Não |
> | profiles/endpoints/origingroups | Não | Não |
> | profiles/endpoints/origins | Não | Não |
> | validateProbe | Não | Não |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | certificateOrders | Sim | Sim |
> | certificateOrders/certificados | Não | Não |
> | validateCertificateRegistrationInformation | Não | Não |

## <a name="microsoftchangeanalysis"></a>Microsoft.ChangeAnalysis

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | perfil | Não | Não |
> | resourceChanges | Não | Não |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | funcionalidades | Não | Não |
> | domainNames | Não | Não |
> | nome_do_domínio/recursos | Não | Não |
> | nome_do_domínio/internalLoadBalancers | Não | Não |
> | DomainNames/certificados | Não | Não |
> | nome_do_domínio/Slots | Não | Não |
> | nome_do_domínio/Slots/funções | Não | Não |
> | nome_do_domínio/Slots/funções/metricDefinitions | Não | Não |
> | DomainNames/Slots/funções/métricas | Não | Não |
> | moveSubscriptionResources | Não | Não |
> | operatingSystemFamilies | Não | Não |
> | operatingSystems | Não | Não |
> | quotas | Não | Não |
> | resourceTypes | Não | Não |
> | validateSubscriptionMoveAvailability | Não | Não |
> | virtualMachines | Não | Não |
> | virtualMachines/diagnosticSettings | Não | Não |
> | virtualMachines/metricDefinitions | Não | Não |
> | virtualMachines/métricas | Não | Não |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | Migração de infraestrutura clássica | Não | Não |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | funcionalidades | Não | Não |
> | expressRouteCrossConnections | Não | Não |
> | expressRouteCrossConnections/emparelhamentos | Não | Não |
> | gatewaySupportedDevices | Não | Não |
> | networkSecurityGroups | Não | Não |
> | quotas | Não | Não |
> | reservedIps | Não | Não |
> | virtualNetworks | Não | Não |
> | virtualNetworks/remoteVirtualNetworkPeeringProxies | Não | Não |
> | virtualNetworks / virtualNetworkPeerings | Não | Não |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | funcionalidades | Não | Não |
> | disks | Não | Não |
> | images | Não | Não |
> | osImages | Não | Não |
> | osPlatformImages | Não | Não |
> | publicImages | Não | Não |
> | quotas | Não | Não |
> | storageAccounts | Não | Não |
> | storageAccounts / blobServices | Não | Não |
> | storageAccounts / fileServices | Não | Não |
> | storageAccounts/metricDefinitions | Não | Não |
> | storageAccounts/métricas | Não | Não |
> | storageAccounts/queueservices | Não | Não |
> | storageAccounts/serviços | Não | Não |
> | storageAccounts/serviços/diagnosticSettings | Não | Não |
> | storageAccounts/serviços/metricDefinitions | Não | Não |
> | storageAccounts/serviços/métricas | Não | Não |
> | storageAccounts/tabelaservices | Não | Não |
> | storageAccounts/vmImages | Não | Não |
> | vmImages | Não | Não |

## <a name="microsoftcodespaces"></a>Microsoft. Codespaces

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | planos | Sim | Não |
> | registeredSubscriptions | Não | Não |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | accounts | Sim | Sim |
> | contas/privateEndpointConnectionProxies | Não | Não |
> | contas/privateEndpointConnections | Não | Não |
> | contas/privateLinkResources | Não | Não |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | RateCard | Não | Não |
> | UsageAggregates | Não | Não |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | availabilitySets | Sim | Sim |
> | cloudServices | Sim | Sim |
> | nuvens/networkInterfaces | Não | Não |
> | cloudservices/publicIPAddresses | Não | Não |
> | cloudservices/roleInstances | Não | Não |
> | cloudservices/roleInstances/networkInterfaces | Não | Não |
> | cloudservices/funções | Não | Não |
> | diskAccesses | Sim | Sim |
> | diskEncryptionSets | Sim | Sim |
> | disks | Sim | Sim |
> | galleries | Sim | Sim |
> | galleries / applications | Não | Não |
> | galerias/aplicativos/versões | Não | Não |
> | galleries/images | Não | Não |
> | galleries/images/versions | Não | Não |
> | Hosts | Sim | Sim |
> | Hosts/hosts | Sim | Sim |
> | images | Sim | Sim |
> | proximityPlacementGroups | Sim | Sim |
> | restorePointCollections | Sim | Sim |
> | restorePointCollections / restorePoints | Não | Não |
> | sharedVMExtensions | Sim | Sim |
> | sharedVMExtensions/versões | Não | Não |
> | sharedVMImages | Sim | Sim |
> | sharedVMImages/versões | Não | Não |
> | snapshots | Sim | Sim |
> | sshPublicKeys | Sim | Sim |
> | virtualMachines | Sim | Sim |
> | virtualMachines/extensões | Sim | Sim |
> | virtualMachines/metricDefinitions | Não | Não |
> | virtualMachines/runCommands | Sim | Sim |
> | virtualMachineScaleSets | Sim | Sim |
> | virtualMachineScaleSets/extensões | Não | Não |
> | virtualMachineScaleSets/networkInterfaces | Não | Não |
> | virtualMachineScaleSets/publicIPAddresses | Sim | Não |
> | virtualMachineScaleSets/virtualMachines | Não | Não |
> | virtualMachineScaleSets/virtualMachines/networkInterfaces | Não | Não |

> [!NOTE]
> Você não pode adicionar uma marca a uma máquina virtual que foi marcada como generalizada. Você marca uma máquina virtual como generalizada com [set-AzVm-generaled](/powershell/module/Az.Compute/Set-AzVM) ou [AZ VM generalizate](/cli/azure/vm#az-vm-generalize).

## <a name="microsoftconnectedcache"></a>Microsoft. ConnectedCache

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | CacheNodes | Sim | Sim |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | AggregatedCost | Não | Não |
> | Saldos | Não | Não |
> | Orçamentos | Não | Não |
> | Charges | Não | Não |
> | CostTags | Não | Não |
> | credits | Não | Não |
> | events | Não | Não |
> | Previsões | Não | Não |
> | lots | Não | Não |
> | Marketplaces | Não | Não |
> | Pricesheets | Não | Não |
> | products | Não | Não |
> | ReservationDetails | Não | Não |
> | ReservationRecommendationDetails | Não | Não |
> | ReservationRecommendations | Não | Não |
> | ReservationSummaries | Não | Não |
> | ReservationTransactions | Não | Não |
> | Marcas | Não | Não |
> | tenants | Não | Não |
> | Termos | Não | Não |
> | UsageDetails | Não | Não |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | containerGroups | Sim | Sim |
> | serviceAssociationLinks | Não | Não |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | registries | Sim | Sim |
> | registros/agentPools | Sim | Sim |
> | registries/builds | Não | Não |
> | registries/builds/cancel | Não | Não |
> | registros/Builds/getLogLink | Não | Não |
> | registries / buildTasks | Sim | Sim |
> | registros/buildTasks/etapas | Não | Não |
> | registros/eventGridFilters | Não | Não |
> | registros/exportPipelines | Não | Não |
> | registros/generateCredentials | Não | Não |
> | registros/getBuildSourceUploadUrl | Não | Não |
> | registros/GetCredentials | Não | Não |
> | registros/importImage | Não | Não |
> | registros/importPipelines | Não | Não |
> | registros/pipelineRuns | Não | Não |
> | registros/privateEndpointConnectionProxies | Não | Não |
> | registros/privateEndpointConnectionProxies/Validate | Não | Não |
> | registros/privateEndpointConnections | Não | Não |
> | registros/privateLinkResources | Não | Não |
> | registros/queueBuild | Não | Não |
> | registros/regenerateCredential | Não | Não |
> | registros/regenerateCredentials | Não | Não |
> | registries/replications | Sim | Sim |
> | registries/runs | Não | Não |
> | registries/runs/cancel | Não | Não |
> | registros/scheduleRun | Não | Não |
> | registries / scopeMaps | Não | Não |
> | registros/taskRuns | Não | Não |
> | registries/tasks | Sim | Sim |
> | registries/tokens | Não | Não |
> | registros/updatePolicies | Não | Não |
> | registries/webhooks | Sim | Sim |
> | registros/WebHooks/getCallbackConfig | Não | Não |
> | registries/webhooks/ping | Não | Não |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | containerServices | Sim | Sim |
> | managedClusters | Sim | Sim |
> | openShiftManagedClusters | Sim | Sim |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | Alertas | Não | Não |
> | BillingAccounts | Não | Não |
> | Orçamentos | Não | Não |
> | CloudConnectors | Não | Não |
> | Conectores | Sim | Sim |
> | costAllocationRules | Não | Não |
> | Departments | Não | Não |
> | Dimensões | Não | Não |
> | EnrollmentAccounts | Não | Não |
> | Exportações | Não | Não |
> | ExternalBillingAccounts | Não | Não |
> | ExternalBillingAccounts/alertas | Não | Não |
> | ExternalBillingAccounts/dimensões | Não | Não |
> | ExternalBillingAccounts/previsão | Não | Não |
> | ExternalBillingAccounts/consulta | Não | Não |
> | ExternalSubscriptions | Não | Não |
> | ExternalSubscriptions/alertas | Não | Não |
> | ExternalSubscriptions/dimensões | Não | Não |
> | ExternalSubscriptions/previsão | Não | Não |
> | ExternalSubscriptions/consulta | Não | Não |
> | Previsão | Não | Não |
> | Insights | Não | Não |
> | Consulta | Não | Não |
> | register | Não | Não |
> | Reportconfigs | Não | Não |
> | Relatórios | Não | Não |
> | Configurações | Não | Não |
> | showbackRules | Não | Não |
> | Exibições | Não | Não |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | solicitações | Não | Não |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | associations | Não | Não |
> | resourceProviders | Sim | Sim |

## <a name="microsoftd365customerinsights"></a>Microsoft. D365CustomerInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | instances | Sim | Sim |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | jobs | Sim | Sim |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | DataBoxEdgeDevices | Sim | Sim |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | workspaces | Sim | Sim |
> | espaços de trabalho/dbWorkspaces | Não | Não |
> | espaços de trabalho/virtualNetworkPeerings | Não | Não |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | catalogs | Sim | Sim |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | dataFactories | Sim | Sim |
> | datafactorings/diagnosticSettings | Não | Não |
> | datafactorings/metricDefinitions | Não | Não |
> | dataFactorySchema | Não | Não |
> | factories | Sim | Sim |
> | factories / integrationRuntimes | Não | Não |

> [!NOTE]
> Se você tiver tempos de execução de integração do Azure-SSIS em seu data factory, o custo de execução será marcado com data factory marcas. A execução de tempos de execução de integração do Azure-SSIS deve ser interrompida e reiniciada para que novas marcas de data factory sejam aplicadas ao custo em execução.

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | accounts | Sim | Sim |
> | accounts / dataLakeStoreAccounts | Não | Não |
> | accounts / storageAccounts | Não | Não |
> | contas/storageAccounts/contêineres | Não | Não |
> | contas/transferAnalyticsUnits | Não | Não |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | accounts | Sim | Sim |
> | contas/eventGridFilters | Não | Não |
> | accounts / firewallRules | Não | Não |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | services | Não | Não |
> | services/projects | Não | Não |

## <a name="microsoftdataprotection"></a>Microsoft.DataProtection

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | BackupVaults | Sim | Sim |
> | ResourceOperationGateKeepers | Sim | Sim |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | accounts | Sim | Sim |
> | accounts/shares | Não | Não |
> | accounts/shares/datasets | Não | Não |
> | accounts/shares/invitations | Não | Não |
> | accounts/shares/providersharesubscriptions | Não | Não |
> | contas/compartilhamentos/synchronizationSettings | Não | Não |
> | accounts/sharesubscriptions | Não | Não |
> | contas/sharesubscriptions/consumerSourceDataSets | Não | Não |
> | accounts/sharesubscriptions/datasetmappings | Não | Não |
> | accounts/sharesubscriptions/triggers | Não | Não |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | servers | Sim | Sim |
> | servers/advisors | Não | Não |
> | servers/keys | Não | Não |
> | servidores/privateEndpointConnectionProxies | Não | Não |
> | servidores/privateEndpointConnections | Não | Não |
> | servidores/privateLinkResources | Não | Não |
> | servidores/queryTexts | Não | Não |
> | servidores/recoverableServers | Não | Não |
> | servidores/iniciar | Não | Não |
> | servidores/parar | Não | Não |
> | servidores/topQueryStatistics | Não | Não |
> | servers / virtualNetworkRules | Não | Não |
> | servidores/waitStatistics | Não | Não |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | flexibleServers | Sim | Sim |
> | servers | Sim | Sim |
> | servers/advisors | Não | Não |
> | servers/keys | Não | Não |
> | servidores/privateEndpointConnectionProxies | Não | Não |
> | servidores/privateEndpointConnections | Não | Não |
> | servidores/privateLinkResources | Não | Não |
> | servidores/queryTexts | Não | Não |
> | servidores/recoverableServers | Não | Não |
> | servidores/iniciar | Não | Não |
> | servidores/parar | Não | Não |
> | servidores/topQueryStatistics | Não | Não |
> | servidores/atualização | Não | Não |
> | servers / virtualNetworkRules | Não | Não |
> | servidores/waitStatistics | Não | Não |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | flexibleServers | Sim | Sim |
> | serverGroups | Sim | Sim |
> | servers | Sim | Sim |
> | servers/advisors | Não | Não |
> | servers/keys | Não | Não |
> | servidores/privateEndpointConnectionProxies | Não | Não |
> | servidores/privateEndpointConnections | Não | Não |
> | servidores/privateLinkResources | Não | Não |
> | servidores/queryTexts | Não | Não |
> | servidores/recoverableServers | Não | Não |
> | servidores/topQueryStatistics | Não | Não |
> | servers / virtualNetworkRules | Não | Não |
> | servidores/waitStatistics | Não | Não |
> | serversv2 | Sim | Sim |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | artifactSources | Sim | Sim |
> | rollouts | Sim | Sim |
> | pertopologias | Sim | Sim |
> | serviços e pertopologias | Sim | Sim |
> | pertopologias/serviços/unidades de serviço | Sim | Sim |
> | etapas | Sim | Sim |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | applicationgroups | Sim | Sim |
> | applicationgroups/applications | Não | Não |
> | applicationgroups/desktops | Não | Não |
> | applicationgroups/startmenuitems | Não | Não |
> | hostpools | Sim | Sim |
> | hostpools / msixpackages | Não | Não |
> | hostpools/sessionhosts | Não | Não |
> | hostpools/sessionhosts/usersessions | Não | Não |
> | hostpools/usersessions | Não | Não |
> | workspaces | Sim | Sim |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | ElasticPools | Sim | Sim |
> | ElasticPools / IotHubTenants | Sim | Sim |
> | ElasticPools/IotHubTenants/securitySettings | Não | Não |
> | IotHubs | Sim | Sim |
> | IotHubs/eventGridFilters | Não | Não |
> | IotHubs/securitySettings | Não | Não |
> | ProvisioningServices | Sim | Sim |
> | usages | Não | Não |

## <a name="microsoftdeviceupdate"></a>Microsoft. DeviceUpdate

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | accounts | Sim | Sim |
> | contas/instâncias | Sim | Sim |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | pipelines | Sim | Sim |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | controladores | Sim | Sim |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | labcenters | Sim | Sim |
> | labs | Sim | Sim |
> | labs/environments | Sim | Sim |
> | laboratórios/perrunners | Sim | Sim |
> | laboratórios/virtualMachines | Sim | Sim |
> | schedules | Sim | Sim |

## <a name="microsoftdigitaltwins"></a>Microsoft.DigitalTwins

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | digitalTwinsInstances | Sim | Sim |
> | digitalTwinsInstances/pontos de extremidade | Não | Não |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | databaseAccounts | Não | Não |
> | databaseAccounts | Sim | Sim |
> | restorableDatabaseAccounts | Não | Não |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | domains | Sim | Sim |
> | domínios/domainOwnershipIdentifiers | Não | Não |
> | generateSsoRequest | Não | Não |
> | topLevelDomains | Não | Não |
> | validateDomainRegistrationInformation | Não | Não |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | lcsprojects | Não | Não |
> | lcsprojects / clouddeployments | Não | Não |
> | lcsprojects/conectores | Não | Não |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | services | Sim | Sim |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | domains | Sim | Sim |
> | domains/topics | Não | Não |
> | eventSubscriptions | Não | Não |
> | extensionTopics | Não | Não |
> | partnerNamespaces | Sim | Sim |
> | partnerNamespaces/eventChannels | Não | Não |
> | partnerRegistrations | Sim | Sim |
> | partnerTopics | Sim | Sim |
> | partnerTopics/eventSubscriptions | Não | Não |
> | systemTopics | Sim | Sim |
> | systemTopics/eventSubscriptions | Não | Não |
> | topics | Sim | Sim |
> | topicTypes | Não | Não |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | clusters | Sim | Sim |
> | namespaces | Sim | Sim |
> | namespaces/authorizationrules | Não | Não |
> | namespaces/disasterrecoveryconfigs | Não | Não |
> | namespaces/eventhubs | Não | Não |
> | namespaces/eventhubs/authorizationrules | Não | Não |
> | namespaces/eventhubs/consumergroups | Não | Não |
> | namespaces/networkrulesets | Não | Não |
> | namespaces/privateEndpointConnections | Não | Não |

## <a name="microsoftexperimentation"></a>Microsoft.Experimentation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | experimentWorkspaces | Sim | Sim |

## <a name="microsoftfalcon"></a>Microsoft.Falcon

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | namespaces | Sim | Sim |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | featureProviders | Não | Não |
> | recursos | Não | Não |
> | providers | Não | Não |
> | subscriptionFeatureRegistrations | Não | Não |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | enroll | Não | Não |
> | galleryitems | Não | Não |
> | generateartifactaccessuri | Não | Não |
> | myareas | Não | Não |
> | myddds/áreas | Não | Não |
> | myddds/áreas/áreas | Não | Não |
> | myddds/áreas/áreas/galleryitems | Não | Não |
> | myddds/áreas/galleryitems | Não | Não |
> | myareas/galleryitems | Não | Não |
> | register | Não | Não |
> | recursos | Não | Não |
> | retrieveresourcesbyid | Não | Não |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | accounts | Sim | Sim |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | autoManagedAccounts | Sim | Sim |
> | autoManagedVmConfigurationProfiles | Sim | Sim |
> | configurationProfileAssignments | Não | Não |
> | guestConfigurationAssignments | Não | Não |
> | software | Não | Não |
> | softwareUpdateProfile | Não | Não |
> | softwareUpdates | Não | Não |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | hanaInstances | Sim | Sim |
> | sapMonitors | Sim | Sim |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | dedicatedHSMs | Sim | Sim |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | clusters | Sim | Sim |
> | clusters/applications | Não | Não |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | services | Sim | Sim |
> | serviços/iomtconnectors | Não | Não |
> | serviços/iomtconnectors/conexões | Não | Não |
> | serviços/iomtconnectors/mapeamentos | Não | Não |
> | serviços/privateEndpointConnectionProxies | Não | Não |
> | serviços/privateEndpointConnections | Não | Não |
> | serviços/privateLinkResources | Não | Não |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | machines | Sim | Sim |
> | computadores/assessPatches | Não | Não |
> | machines/extensions | Sim | Sim |
> | computadores/installPatches | Não | Não |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | Gerenciadores de DataManager | Sim | Sim |

## <a name="microsofthybridnetwork"></a>Microsoft. HybridNetwork

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | dispositivos | Sim | Sim |
> | networkFunctions | Sim | Sim |
> | networkFunctionVendors | Não | Não |
> | registeredSubscriptions | Não | Não |
> | selecionados | Não | Não |
> | fornecedores/vendorSkus | Não | Não |
> | fornecedores/vendorSkus/previewSubscriptions | Não | Não |
> | virtualNetworkFunctions | Sim | Sim |
> | virtualNetworkFunctionVendors | Não | Não |

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | components | Sim | Sim |
> | networkScopes | Sim | Sim |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | jobs | Sim | Sim |

## <a name="microsoftinsights"></a>Microsoft.insights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | actionGroups | Sim | Sim |
> | activityLogAlerts | Sim | Sim |
> | alertrules | Sim | Sim |
> | autoscalesettings | Sim | Sim |
> | components | Sim | Sim |
> | componentes/linkedStorageAccounts | Não | Não |
> | componentes/ProactiveDetectionConfigs | Não | Não |
> | diagnosticSettings | Não | Não |
> | guestDiagnosticSettings | Sim | Sim |
> | guestDiagnosticSettingsAssociation | Sim | Sim |
> | logprofiles | Sim | Sim |
> | metricAlerts | Sim | Sim |
> | privateLinkScopes | Sim | Sim |
> | privateLinkScopes / privateEndpointConnections | Não | Não |
> | privateLinkScopes / scopedResources | Não | Não |
> | queryPacks | Sim | Sim |
> | queryPacks/consultas | Não | Não |
> | scheduledQueryRules | Sim | Sim |
> | webtests | Sim | Sim |
> | workbooks | Sim | Sim |
> | workbooktemplates | Sim | Sim |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | Não | Não |
> | diagnosticSettingsCategories | Não | Não |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | appTemplates | Não | Não |
> | IoTApps | Sim | Sim |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | Grafo | Sim | Sim |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | deletedVaults | Não | Não |
> | hsmPools | Sim | Sim |
> | managedHSMs | Sim | Sim |
> | vaults | Sim | Sim |
> | cofres/accessPolicies | Não | Não |
> | cofres/eventGridFilters | Não | Não |
> | cofres/chaves | Não | Não |
> | cofres/chaves/versões | Não | Não |
> | vaults/secrets | Não | Não |

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | connectedClusters | Sim | Sim |
> | registeredSubscriptions | Não | Não |

## <a name="microsoftkubernetesconfiguration"></a>Microsoft.KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | extensions | Não | Não |
> | sourceControlConfigurations | Não | Não |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | clusters | Sim | Sim |
> | clusters/attacheddatabaseconfigurations | Não | Não |
> | clusters/databases | Não | Não |
> | clusters/databases/dataconnections | Não | Não |
> | clusters/databases/eventhubconnections | Não | Não |
> | clusters/databases/principalassignments | Não | Não |
> | clusters/conexões de dataconexão | Não | Não |
> | clusters/principalassignments | Não | Não |
> | clusters/sharedidentities | Não | Não |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | labaccounts | Sim | Não |
> | users | Não | Não |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | hostingEnvironments | Sim | Sim |
> | integrationAccounts | Sim | Sim |
> | integrationServiceEnvironments | Sim | Sim |
> | integrationServiceEnvironments / managedApis | Não | Não |
> | isolatedEnvironments | Sim | Sim |
> | workflows | Sim | Sim |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | commitmentPlans | Sim | Sim |
> | webServices | Sim | Sim |
> | Workspaces | Sim | Sim |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | workspaces | Sim | Sim |
> | espaços de trabalho/batchEndpoints | Sim | Sim |
> | espaços de trabalho/batchEndpoints/implantações | Sim | Sim |
> | espaços de trabalho/códigos | Não | Não |
> | espaços de trabalho/códigos/versões | Não | Não |
> | workspaces/computes | Não | Não |
> | espaços de trabalho/repositórios de armazenamento | Não | Não |
> | espaços de trabalho/eventGridFilters | Não | Não |
> | espaços de trabalho/trabalhos | Não | Não |
> | espaços de trabalho/labelingJobs | Não | Não |
> | espaços de trabalho/linksservices | Não | Não |
> | espaços de trabalho/modelos | Não | Não |
> | espaços de trabalho/modelos/versões | Não | Não |
> | espaços de trabalho/onlineEndpoints | Sim | Sim |
> | espaços de trabalho/onlineEndpoints/implantações | Sim | Sim |

## <a name="microsoftmaintenance"></a>Microsoft.Maintenance

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | applyUpdates | Não | Não |
> | configurationAssignments | Não | Não |
> | maintenanceConfigurations | Sim | Sim |
> | publicMaintenanceConfigurations | Não | Não |
> | updates | Não | Não |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | Identidades | Não | Não |
> | userAssignedIdentities | Sim | Sim |

## <a name="microsoftmanagednetwork"></a>Microsoft.ManagedNetwork

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | managedNetworks | Sim | Sim |
> | managedNetworks / managedNetworkGroups | Sim | Sim |
> | managedNetworks / managedNetworkPeeringPolicies | Sim | Sim |
> | notificação | Sim | Sim |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | marketplaceRegistrationDefinitions | Não | Não |
> | registrationAssignments | Não | Não |
> | registrationDefinitions | Não | Não |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | getEntities | Não | Não |
> | managementGroups | Não | Não |
> | managementGroups/configurações | Não | Não |
> | recursos | Não | Não |
> | startTenantBackfill | Não | Não |
> | tenantBackfillStatus | Não | Não |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | accounts | Sim | Sim |
> | contas/eventGridFilters | Não | Não |
> | contas/privateAtlases | Sim | Sim |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | macc | Não | Não |
> | offers | Não | Não |
> | offerTypes | Não | Não |
> | offerTypes/Publicadores | Não | Não |
> | offerTypes/Publicadores/ofertas | Não | Não |
> | offerTypes/Publicadores/ofertas/planos | Não | Não |
> | offerTypes/Publicadores/ofertas/planos/contratos | Não | Não |
> | offerTypes/Publicadores/ofertas/planos/configurações | Não | Não |
> | offerTypes/Publicadores/ofertas/planos/configurações/importImage | Não | Não |
> | privategalleryitems | Não | Não |
> | privateStoreClient | Não | Não |
> | privateStores | Não | Não |
> | privateStores/ofertas | Não | Não |
> | products | Não | Não |
> | publishers | Não | Não |
> | publishers/offers | Não | Não |
> | publishers/offers/amendments | Não | Não |
> | register | Não | Não |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | classicDevServices | Sim | Sim |
> | updateCommunicationPreference | Não | Não |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | Contratos | Não | Não |
> | offertypes | Não | Não |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | mediaservices | Sim | Sim |
> | mediaservices/accountFilters | Não | Não |
> | mediaservices/assets | Não | Não |
> | mediaservices/ativos/assetFilters | Não | Não |
> | mediaservices/contentKeyPolicies | Não | Não |
> | mediaservices/eventGridFilters | Não | Não |
> | mediaservices/liveEventOperations | Não | Não |
> | mediaservices / liveEvents | Sim | Sim |
> | mediaservices / liveEvents / liveOutputs | Não | Não |
> | mediaservices/liveOutputOperations | Não | Não |
> | mediaservices/mediaGraphs | Não | Não |
> | mediaservices/privateEndpointConnectionOperations | Não | Não |
> | mediaservices/privateEndpointConnectionProxies | Não | Não |
> | mediaservices/privateEndpointConnections | Não | Não |
> | mediaservices/streamingEndpointOperations | Não | Não |
> | mediaservices / streamingEndpoints | Sim | Sim |
> | mediaservices/streamingLocators | Não | Não |
> | mediaservices/streamingPolicies | Não | Não |
> | mediaservices/transforms | Não | Não |
> | mediaservices/transforms/jobs | Não | Não |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | appClusters | Sim | Sim |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | assessmentProjects | Sim | Sim |
> | migrateprojects | Sim | Sim |
> | moveCollections | Sim | Sim |
> | projects | Sim | Sim |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | holographicsBroadcastAccounts | Sim | Sim |
> | objectUnderstandingAccounts | Sim | Sim |
> | remoteRenderingAccounts | Sim | Sim |
> | spatialAnchorsAccounts | Sim | Sim |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | netAppAccounts | Sim | Não |
> | netAppAccounts / accountBackups | Não | Não |
> | netAppAccounts / capacityPools | Sim | Não |
> | netAppAccounts/capacityPools/volumes | Sim | Não |
> | netAppAccounts/capacityPools/volumes/instantâneos | Não | Não |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | applicationGateways | Sim | Sim |
> | applicationGatewayWebApplicationFirewallPolicies | Sim | Sim |
> | applicationSecurityGroups | Sim | Sim |
> | azureFirewallFqdnTags | Não | Não |
> | azureFirewalls | Sim | Não |
> | bastionHosts | Sim | Não |
> | bgpServiceCommunities | Não | Não |
> | connections | Sim | Sim |
> | ddosCustomPolicies | Sim | Sim |
> | ddosProtectionPlans | Sim | Sim |
> | dnsOperationStatuses | Não | Não |
> | dnszones | Sim | Sim |
> | dnszones/A | Não | Não |
> | dnszones/AAAA | Não | Não |
> | dnszones/all | Não | Não |
> | dnszones/CAA | Não | Não |
> | dnszones/CNAME | Não | Não |
> | dnszones/MX | Não | Não |
> | dnszones/NS | Não | Não |
> | dnszones/PTR | Não | Não |
> | dnszones/recordsets | Não | Não |
> | dnszones/SOA | Não | Não |
> | dnszones/SRV | Não | Não |
> | dnszones/TXT | Não | Não |
> | expressRouteCircuits | Sim | Sim |
> | expressRouteCrossConnections | Sim | Sim |
> | expressRouteGateways | Sim | Sim |
> | expressRoutePorts | Sim | Sim |
> | expressRouteServiceProviders | Não | Não |
> | firewallPolicies | Sim | Sim |
> | frontdoors | Sim, mas limitado (veja a [Observação abaixo](#frontdoor)) | Yes |
> | frontdoorWebApplicationFirewallManagedRuleSets | Sim, mas limitado (veja a [Observação abaixo](#frontdoor)) | No |
> | frontdoorWebApplicationFirewallPolicies | Sim, mas limitado (veja a [Observação abaixo](#frontdoor)) | Yes |
> | getDnsResourceReference | Não | Não |
> | internalNotify | Não | Não |
> | ipGroups | Sim | Sim |
> | loadBalancers | Sim | Sim |
> | virtualNetworkGateways | Sim | Sim |
> | natGateways | Sim | Sim |
> | networkIntentPolicies | Sim | Sim |
> | networkInterfaces | Sim | Sim |
> | networkProfiles | Sim | Sim |
> | networkSecurityGroups | Sim | Sim |
> | networkWatchers | Sim | Sim |
> | networkWatchers / connectionMonitors | Sim | Não |
> | networkWatchers / flowLogs | Não | Não |
> | networkWatchers/lentes | Sim | Não |
> | networkWatchers / pingMeshes | Sim | Não |
> | p2sVpnGateways | Sim | Sim |
> | privateDnsOperationStatuses | Não | Não |
> | privateDnsZones | Sim | Sim |
> | privateDnsZones/A | Não | Não |
> | privateDnsZones/AAAA | Não | Não |
> | privateDnsZones/tudo | Não | Não |
> | privateDnsZones/CNAME | Não | Não |
> | privateDnsZones/MX | Não | Não |
> | privateDnsZones/PTR | Não | Não |
> | privateDnsZones/SOA | Não | Não |
> | privateDnsZones/SRV | Não | Não |
> | privateDnsZones/TXT | Não | Não |
> | privateDnsZones / virtualNetworkLinks | Sim | Sim |
> | privateEndpoints | Sim | Sim |
> | privateLinkServices | Sim | Sim |
> | publicIPAddresses | Sim | Sim |
> | publicIPPrefixes | Sim | Sim |
> | routeFilters | Sim | Sim |
> | routeTables | Sim | Sim |
> | serviceEndpointPolicies | Sim | Sim |
> | trafficManagerGeographicHierarchies | Não | Não |
> | trafficmanagerprofiles | Sim | Sim |
> | trafficmanagerprofiles/heatMaps | Não | Não |
> | trafficManagerUserMetricsKeys | Não | Não |
> | virtualWans | Sim | Sim |
> | virtualNetworkGateways | Sim | Sim |
> | virtualNetworks | Sim | Sim |
> | virtualNetworks/sub-redes | Não | Não |
> | virtualNetworkTaps | Sim | Sim |
> | virtualWans | Sim | Não |
> | vpnGateways | Sim | Sim |
> | vpnSites | Sim | Sim |
> | frontdoorWebApplicationFirewallPolicies | Sim | Sim |

<a id="frontdoor"></a>

> [!NOTE]
> Para o serviço de porta frontal do Azure, você pode aplicar marcas ao criar o recurso, mas a atualização ou adição de marcas não tem suporte no momento.


## <a name="microsoftnotebooks"></a>Microsoft. notebooks

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | NotebookProxies | Não | Não |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | namespaces | Sim | Não |
> | namespaces / notificationHubs | Sim | Não |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | osNamespaces | Sim | Sim |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | HyperVSites | Sim | Sim |
> | ImportSites | Sim | Sim |
> | MasterSites | Sim | Sim |
> | ServerSites | Sim | Sim |
> | VMwareSites | Sim | Sim |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | clusters | Sim | Sim |
> | deletedWorkspaces | Não | Não |
> | linkTargets | Não | Não |
> | storageInsightConfigs | Não | Não |
> | workspaces | Sim | Sim |
> | espaços de trabalho/exportações | Não | Não |
> | espaços de trabalho/fontes de fonte | Não | Não |
> | espaços de trabalho/linksservices | Não | Não |
> | espaços de trabalho/linkedStorageAccounts | Não | Não |
> | workspaces/metadata | Não | Não |
> | workspaces/query | Não | Não |
> | espaços de trabalho/scopedPrivateLinkProxies | Não | Não |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | managementassociations | Não | Não |
> | managementconfigurations | Sim | Sim |
> | solutions | Sim | Sim |
> | Modos de exibição | Sim | Sim |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | legacyPeerings | Não | Não |
> | peerAsns | Não | Não |
> | peerings | Sim | Sim |
> | peeringServiceCountries | Não | Não |
> | peeringServiceProviders | Não | Não |
> | peeringServices | Sim | Sim |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | atestados | Não | Não |
> | policyEvents | Não | Não |
> | policyMetadata | Não | Não |
> | policyStates | Não | Não |
> | policyTrackedResources | Não | Não |
> | remediations | Não | Não |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | consoles | Não | Não |
> | dashboards | Sim | Sim |
> | userSettings | Não | Não |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | privateLinkServicesForPowerBI | Sim | Sim |
> | tenants | Sim | Sim |
> | locatários/espaços de trabalho | Não | Não |
> | workspaceCollections | Sim | Sim |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | capacities | Sim | Sim |

## <a name="microsoftprojectbabylon"></a>Microsoft.ProjectBabylon

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | accounts | Sim | Sim |
> | deletedAccounts | Não | Não |

## <a name="microsoftproviderhub"></a>Microsoft.ProviderHub

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | providerRegistrations | Não | Não |
> | providerRegistrations/condistribuições | Não | Não |
> | providerRegistrations / resourceTypeRegistrations | Não | Não |
> | rollouts | Sim | Sim |

## <a name="microsoftquantum"></a>Microsoft.Quantum

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | Workspaces | Sim | Sim |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | backupProtectedItems | Não | Não |
> | vaults | Sim | Sim |

## <a name="microsoftredhatopenshift"></a>Microsoft.RedHatOpenShift

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | OpenShiftClusters | Sim | Sim |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | namespaces | Sim | Sim |
> | namespaces/authorizationrules | Não | Não |
> | namespaces/hybridconnections | Não | Não |
> | namespaces/hybridconnections/authorizationrules | Não | Não |
> | namespaces/privateEndpointConnections | Não | Não |
> | namespaces/wcfrelays | Não | Não |
> | namespaces/wcfrelays/authorizationrules | Não | Não |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | consultas | Sim | Sim |
> | resourceChangeDetails | Não | Não |
> | resourceChanges | Não | Não |
> | recursos | Não | Não |
> | resourcesHistory | Não | Não |
> | subscriptionsStatus | Não | Não |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | availabilityStatuses | Não | Não |
> | childAvailabilityStatuses | Não | Não |
> | childResources | Não | Não |
> | emergingissues | Não | Não |
> | events | Não | Não |
> | impactedResources | Não | Não |
> | metadata | Não | Não |
> | Notificações | Não | Não |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | calculateTemplateHash | Não | Não |
> | deployments | Sim | Não |
> | deployments/operations | Não | Não |
> | deploymentScripts | Sim | Sim |
> | deploymentScripts/logs | Não | Não |
> | links | Não | Não |
> | notifyResourceJobs | Não | Não |
> | providers | Não | Não |
> | resourceGroups | Sim | Não |
> | subscriptions | Sim | Não |
> | templateSpecs | Sim | Sim |
> | templateSpecs/versões | Sim | Sim |
> | tenants | Não | Não |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | de dimensionamento da Web | Sim | Sim |
> | saasresources | Não | Não |

## <a name="microsoftscvmm"></a>Microsoft. ScVmm

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | nuvens | Sim | Sim |
> | Máquinas Virtuais | Sim | Sim |
> | VirtualMachineTemplates | Sim | Sim |
> | VirtualNetworks | Sim | Sim |
> | vmmservers | Sim | Sim |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | resourceHealthMetadata | Não | Não |
> | searchServices | Sim | Sim |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | adaptiveNetworkHardenings | Não | Não |
> | advancedThreatProtectionSettings | Não | Não |
> | alertas | Não | Não |
> | alertsSuppressionRules | Não | Não |
> | allowedConnections | Não | Não |
> | applicationWhitelistings | Não | Não |
> | assessmentMetadata | Não | Não |
> | assessments | Não | Não |
> | autoDismissAlertsRules | Não | Não |
> | automations | Sim | Sim |
> | AutoProvisioningSettings | Não | Não |
> | Compliances | Não | Não |
> | conectores | Não | Não |
> | dataCollectionAgents | Não | Não |
> | deviceSecurityGroups | Não | Não |
> | discoveredSecuritySolutions | Não | Não |
> | externalSecuritySolutions | Não | Não |
> | InformationProtectionPolicies | Não | Não |
> | iotDefenderSettings | Não | Não |
> | iotSecuritySolutions | Sim | Sim |
> | iotSecuritySolutions / analyticsModels | Não | Não |
> | iotSecuritySolutions / analyticsModels / aggregatedAlerts | Não | Não |
> | iotSecuritySolutions / analyticsModels / aggregatedRecommendations | Não | Não |
> | iotSecuritySolutions / iotAlerts | Não | Não |
> | iotSecuritySolutions / iotAlertTypes | Não | Não |
> | iotSecuritySolutions / iotRecommendations | Não | Não |
> | iotSecuritySolutions / iotRecommendationTypes | Não | Não |
> | iotSensors | Não | Não |
> | jitNetworkAccessPolicies | Não | Não |
> | jitPolicies | Não | Não |
> | políticas | Não | Não |
> | pricings | Não | Não |
> | regulatoryComplianceStandards | Não | Não |
> | regulatoryComplianceStandards / regulatoryComplianceControls | Não | Não |
> | regulatoryComplianceStandards / regulatoryComplianceControls / regulatoryComplianceAssessments | Não | Não |
> | secureScoreControlDefinitions | Não | Não |
> | secureScoreControls | Não | Não |
> | secureScores | Não | Não |
> | secureScores / secureScoreControls | Não | Não |
> | securityContacts | Não | Não |
> | securitySolutions | Não | Não |
> | securitySolutionsReferenceData | Não | Não |
> | securityStatuses | Não | Não |
> | securityStatusesSummaries | Não | Não |
> | serverVulnerabilityAssessments | Não | Não |
> | configurações | Não | Não |
> | sqlVulnerabilityAssessments | Não | Não |
> | subavaliações | Não | Não |
> | tarefas | Não | Não |
> | topologies | Não | Não |
> | workspaceSettings | Não | Não |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | Não | Não |
> | diagnosticSettingsCategories | Não | Não |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | aggregations | Não | Não |
> | alertRules | Não | Não |
> | alertRuleTemplates | Não | Não |
> | automationRules | Não | Não |
> | bookmarks | Não | Não |
> | cases | Não | Não |
> | dataconnecters | Não | Não |
> | dataConnectorsCheckRequirements | Não | Não |
> | entidades | Não | Não |
> | entityQueries | Não | Não |
> | incidents | Não | Não |
> | officeConsents | Não | Não |
> | configurações | Não | Não |
> | threatIntelligence | Não | Não |
> | watchlists | Não | Não |

## <a name="microsoftserialconsole"></a>Microsoft.SerialConsole

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | consoleservices | Não | Não |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | namespaces | Sim | Sim |
> | namespaces/authorizationrules | Não | Não |
> | namespaces/disasterrecoveryconfigs | Não | Não |
> | namespaces/eventgridfilters | Não | Não |
> | namespaces/networkrulesets | Não | Não |
> | namespaces/privateEndpointConnections | Não | Não |
> | namespaces/queues | Não | Não |
> | namespaces/queues/authorizationrules | Não | Não |
> | namespaces/topics | Não | Não |
> | namespaces/topics/authorizationrules | Não | Não |
> | namespaces/topics/subscriptions | Não | Não |
> | namespaces/topics/subscriptions/rules | Não | Não |
> | premiumMessagingRegions | Não | Não |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | de dimensionamento da Web | Sim | Sim |
> | clusters | Sim | Sim |
> | clusters/applications | Não | Não |
> | containerGroups | Sim | Sim |
> | containerGroupSets | Sim | Sim |
> | edgeclusters | Sim | Sim |
> | edgeclusters/aplicativos | Não | Não |
> | managedclusters | Sim | Sim |
> | managedclusters/NodeTypes | Não | Não |
> | networks | Sim | Sim |
> | secretstores | Sim | Sim |
> | secretstores/certificados | Não | Não |
> | secretstores/segredos | Não | Não |
> | volumes | Sim | Sim |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | de dimensionamento da Web | Sim | Sim |
> | containerGroups | Sim | Sim |
> | gateways | Sim | Sim |
> | networks | Sim | Sim |
> | segredos | Sim | Sim |
> | volumes | Sim | Sim |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | providerRegistrations | Não | Não |
> | providerRegistrations / resourceTypeRegistrations | Não | Não |
> | rollouts | Sim | Sim |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | SignalR | Sim | Sim |
> | Sinalização/eventGridFilters | Não | Não |

## <a name="microsoftsingularity"></a>Microsoft. singularidade

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | accounts | Sim | Sim |
> | contas/accountQuotaPolicies | Não | Não |
> | contas/groupPolicies | Não | Não |
> | contas/trabalhos | Não | Não |
> | contas/storageContainers | Não | Não |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | hybridUseBenefits | Não | Não |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | applicationDefinitions | Sim | Sim |
> | de dimensionamento da Web | Sim | Sim |
> | jitRequests | Sim | Sim |


## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | managedInstances | Sim | Sim |
> | managedInstances/bancos de dados | Não | Não |
> | managedInstances/bancos de dados/backupShortTermRetentionPolicies | Não | Não |
> | managedInstances/bancos de dados/esquemas/tabelas/colunas/sensitivityLabels | Não | Não |
> | managedInstances/bancos de dados/vulnerabilityAssessments | Não | Não |
> | managedInstances/bancos de dados/vulnerabilityAssessments/regras/linhas de base | Não | Não |
> | managedInstances / encryptionProtector | Não | Não |
> | managedInstances/chaves | Não | Não |
> | managedInstances / restorableDroppedDatabases / backupShortTermRetentionPolicies | Não | Não |
> | managedInstances / vulnerabilityAssessments | Não | Não |
> | servers | Sim | Sim |
> | servers/administrators | Não | Não |
> | servidores/communicationLinks | Não | Não |
> | servers/databases | Sim (veja a [Observação abaixo](#sqlnote)) | Yes |
> | servidores/encryptionProtector | Não | Não |
> | servers / firewallRules | Não | Não |
> | servers/keys | Não | Não |
> | servidores/restorableDroppedDatabases | Não | Não |
> | servers/serviceobjectives | Não | Não |
> | servidores/tdeCertificates | Não | Não |
> | virtualClusters | Não | Não |

<a id="sqlnote"></a>

> [!NOTE]
> O banco de dados mestre não dá suporte a marcas, mas outros bancos de dados, incluindo bancos de dados do Azure Synapse Analytics, marcas de suporte. Os bancos de dados do Azure Synapse Analytics devem estar no estado ativo (não pausado).

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | SqlVirtualMachineGroups | Sim | Sim |
> | SqlVirtualMachineGroups / AvailabilityGroupListeners | Não | Não |
> | SqlVirtualMachines | Sim | Sim |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | deletedAccounts | Não | Não |
> | storageAccounts | Sim | Sim |
> | storageAccounts / blobServices | Não | Não |
> | storageAccounts / fileServices | Não | Não |
> | storageAccounts/queueservices | Não | Não |
> | storageAccounts/serviços | Não | Não |
> | storageAccounts/serviços/metricDefinitions | Não | Não |
> | storageAccounts/tabelaservices | Não | Não |
> | usages | Não | Não |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | caches | Sim | Sim |
> | caches/storageTargets | Não | Não |
> | usageModels | Não | Não |

## <a name="microsoftstoragereplication"></a>Microsoft. StorageReplication

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | replicationGroups | Não | Não |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Sim | Sim |
> | storageSyncServices / registeredServers | Não | Não |
> | storageSyncServices / syncGroups | Não | Não |
> | storageSyncServices / syncGroups / cloudEndpoints | Não | Não |
> | storageSyncServices / syncGroups / serverEndpoints | Não | Não |
> | storageSyncServices/fluxos de trabalho | Não | Não |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Sim | Sim |
> | storageSyncServices / registeredServers | Não | Não |
> | storageSyncServices / syncGroups | Não | Não |
> | storageSyncServices / syncGroups / cloudEndpoints | Não | Não |
> | storageSyncServices / syncGroups / serverEndpoints | Não | Não |
> | storageSyncServices/fluxos de trabalho | Não | Não |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Sim | Sim |
> | storageSyncServices / registeredServers | Não | Não |
> | storageSyncServices / syncGroups | Não | Não |
> | storageSyncServices / syncGroups / cloudEndpoints | Não | Não |
> | storageSyncServices / syncGroups / serverEndpoints | Não | Não |
> | storageSyncServices/fluxos de trabalho | Não | Não |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | managers | Sim | Sim |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | clusters | Sim | Sim |
> | clusters/privateEndpoints | Não | Não |
> | streamingjobs | Sim (veja a observação a seguir) | Yes |

> [!NOTE]
> Você não pode adicionar uma marca quando streamingjobs estiver em execução. Pare o recurso para adicionar uma marca.

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | acceptChangeTenant | Não | Não |
> | aliases | Não | Não |
> | cancel | Não | Não |
> | changeTenantRequest | Não | Não |
> | changeTenantStatus | Não | Não |
> | CreateSubscription | Não | Não |
> | enable | Não | Não |
> | renomear | Não | Não |
> | SubscriptionDefinitions | Não | Não |
> | SubscriptionDefinitions | Não | Não |
> | subscriptions | Não | Não |

## <a name="microsoftsynapse"></a>Microsoft.Synapse

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | privateLinkHubs | Sim | Sim |
> | workspaces | Sim | Sim |
> | espaços de trabalho/bigDataPools | Sim | Sim |
> | espaços de trabalho/operationStatuses | Não | Não |
> | espaços de trabalho/bancos de dados | Sim | Sim |
> | espaços de trabalho/sqlpools | Sim | Sim |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | environments | Sim | Não |
> | environments / accessPolicies | Não | Não |
> | environments/eventsources | Sim | Não |
> | environments / referenceDataSets | Sim | Não |

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | stores | Sim | Sim |
> | lojas/accessPolicies | Não | Não |
> | stores/services | Não | Não |
> | stores/services/tokens | Não | Não |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | imageTemplates | Sim | Sim |
> | imageTemplates / runOutputs | Não | Não |

## <a name="microsoftvmware"></a>Microsoft.VMware

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | ArcZones | Sim | Sim |
> | ResourcePools | Sim | Sim |
> | VCenters | Sim | Sim |
> | Máquinas Virtuais | Sim | Sim |
> | VirtualMachineTemplates | Sim | Sim |
> | VirtualNetworks | Sim | Sim |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | dedicatedCloudNodes | Sim | Sim |
> | dedicatedCloudServices | Sim | Sim |
> | virtualMachines | Sim | Sim |

## <a name="microsoftvnfmanager"></a>Microsoft.VnfManager

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | dispositivos | Sim | Sim |
> | registeredSubscriptions | Não | Não |
> | selecionados | Não | Não |
> | fornecedores/SKUs | Não | Não |
> | fornecedores/vnfs | Não | Não |
> | virtualNetworkFunctionSkus | Não | Não |
> | vnfs | Sim | Sim |

## <a name="microsoftvsonline"></a>Microsoft.VSOnline

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | accounts | Sim | Não |
> | planos | Sim | Não |
> | registeredSubscriptions | Não | Não |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | apiManagementAccounts | Não | Não |
> | apiManagementAccounts/apiAcls | Não | Não |
> | apiManagementAccounts/APIs | Não | Não |
> | apiManagementAccounts/APIs/apiAcls | Não | Não |
> | apiManagementAccounts/APIs/connectionAcls | Não | Não |
> | apiManagementAccounts/APIs/conexões | Não | Não |
> | apiManagementAccounts/APIs/conexões/connectionAcls | Não | Não |
> | apiManagementAccounts/APIs/localizedDefinitions | Não | Não |
> | apiManagementAccounts/connectionAcls | Não | Não |
> | apiManagementAccounts/conexões | Não | Não |
> | billingMeters | Não | Não |
> | certificates | Sim | Sim |
> | connectionGateways | Sim | Sim |
> | connections | Sim | Sim |
> | customApis | Sim | Sim |
> | deletedSites | Não | Não |
> | hostingEnvironments | Sim | Sim |
> | hostingEnvironments/eventGridFilters | Não | Não |
> | hostingEnvironments/multiRolePools | Não | Não |
> | hostingEnvironments/pools | Não | Não |
> | kubeEnvironments | Sim | Sim |
> | publishingUsers | Não | Não |
> | filmes | Não | Não |
> | resourceHealthMetadata | Não | Não |
> | runtimes | Não | Não |
> | serverFarms | Sim | Sim |
> | serverFarms/eventGridFilters | Não | Não |
> | serverFarms/firstPartyApps | Não | Não |
> | serverFarms/firstPartyApps/keyVaultSettings | Não | Não |
> | sites | Sim | Sim |
> | sites/configuração  | Não | Não |
> | sites/eventGridFilters | Não | Não |
> | sites/hostNameBindings | Não | Não |
> | sites/networkConfig | Não | Não |
> | sites/premieraddons | Sim | Sim |
> | sites/slots | Sim | Sim |
> | sites/Slots/eventGridFilters | Não | Não |
> | sites/Slots/hostNameBindings | Não | Não |
> | sites/Slots/networkConfig | Não | Não |
> | sourceControls | Não | Não |
> | staticSites | Sim | Sim |
> | validade | Não | Não |
> | verifyHostingEnvironmentVnet | Não | Não |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | Não | Não |
> | diagnosticSettingsCategories | Não | Não |

## <a name="microsoftwindowsesu"></a>Microsoft.WindowsESU

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | multipleActivationKeys | Sim | Sim |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | DeviceServices | Sim | Sim |

## <a name="microsoftworkloadbuilder"></a>Microsoft. WorkloadBuilder

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | cargas de trabalho | Sim | Sim |
> | cargas de trabalho/instâncias | Não | Não |
> | cargas de trabalho/versões | Não | Não |
> | cargas de trabalho/versões/artefatos | Não | Não |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
> | ------------- | ----------- | ----------- |
> | components | Não | Não |
> | componentsSummary | Não | Não |
> | monitorInstances | Não | Não |
> | monitorInstancesSummary | Não | Não |
> | monitors | Não | Não |
> | notificationSettings | Não | Não |

## <a name="next-steps"></a>Próximas etapas

Para saber como aplicar tags a recursos, consulte [Use tags para organizar seus recursos do Azure](tag-resources.md).
