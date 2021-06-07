---
title: Exclusão completa de modo
description: Mostra como os tipos de recurso lidam com a exclusão de modo completo em modelos do Azure Resource Manager.
ms.topic: conceptual
ms.date: 10/21/2020
ms.openlocfilehash: e0c67bfcda81ad128e0018c4ab37c4b0cbe680f0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96184018"
---
# <a name="deletion-of-azure-resources-for-complete-mode-deployments"></a>Exclusão de recursos do Azure para implantações do modo completo

Este artigo descreve como os tipos de recurso lidam com a exclusão quando ela não ocorre em um modelo implantado no modo completo.

Os tipos de recurso marcados com **Sim** são excluídos quando o tipo não está no modelo implantado com o modo completo.

Os tipos de recurso marcados **sem não são excluídos** automaticamente quando não estão no modelo; no entanto, elas serão excluídas se o recurso pai for excluído. Para obter uma descrição completa do comportamento, consulte [Modos de implantação do Azure Resource Manager](deployment-modes.md).

Se você implantar em [mais de um grupo de recursos em um modelo](./deploy-to-resource-group.md), os recursos no grupo de recursos especificado na operação de implantação poderão ser excluídos. Os recursos nos grupos de recursos secundários não são excluídos.

Os recursos são listados pelo namespace do provedor de recursos. Para corresponder a um namespace de provedor de recursos com seu nome de serviço do Azure, consulte [provedores de recursos para serviços do Azure](../management/azure-services-resource-providers.md).

> [!NOTE]
> Sempre use a [operação What-If](template-deploy-what-if.md) antes de implantar um modelo no modo completo. What-If mostra quais recursos serão criados, excluídos ou modificados. Use What-If para evitar a exclusão acidental de recursos.
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
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | DomainServices | Sim |
> | DomainServices/oucontainer | Não |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | supportProviders | Não |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
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
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | advisorScore | Não |
> | configurações | Não |
> | generateRecommendations | Não |
> | metadata | Não |
> | filmes | Não |
> | suppressions | Não |

## <a name="microsoftagfoodplatform"></a>Microsoft. AgFoodPlatform

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | farmBeats | Sim |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | actionRules | Sim |
> | alertas | Não |
> | alertsList | Não |
> | alertsMetaData | Não |
> | alertsSummary | Não |
> | alertsSummaryList | Não |
> | smartDetectorAlertRules | Sim |
> | smartGroups | Não |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | servers | Sim |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | reportFeedback | Não |
> | serviço | Sim |
> | validateServiceName | Não |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | configurationStores | Sim |
> | configurationStores / eventGridFilters | Não |
> | configurationStores/valores de valor | Não |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | Spring | Sim |
> | Spring/apps | Não |
> | Spring/aplicativos/implantações | Não |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | attestationProviders | Sim |
> | defaultfornecedores | Não |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | accessReviewScheduleDefinitions | Não |
> | accessReviewScheduleSettings | Não |
> | classicAdministrators | Não |
> | Aliases de dataalias | Não |
> | denyAssignments | Não |
> | elevateAccess | Não |
> | findOrphanRoleAssignments | Não |
> | locks | Não |
> | permissões | Não |
> | policyAssignments | Não |
> | policyDefinitions | Não |
> | policyExemptions | Não |
> | policySetDefinitions | Não |
> | privateLinkAssociations | Não |
> | providerOperations | Não |
> | resourceManagementPrivateLinks | Sim |
> | roleAssignments | Não |
> | roleAssignmentsUsageMetrics | Não |
> | roleDefinitions | Não |

## <a name="microsoftautomanage"></a>Microsoft. autogerenci

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | accounts | Sim |
> | configurationProfileAssignments | Não |
> | configurationProfilePreferences | Sim |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | automationaccounts | Sim |
> | automationAccounts/configurações | Sim |
> | automationAccounts/trabalhos | Não |
> | automationAccounts/privateEndpointConnectionProxies | Não |
> | automationAccounts/privateEndpointConnections | Não |
> | automationAccounts/privateLinkResources | Não |
> | automationAccounts / runbooks | Sim |
> | automationAccounts/softwareUpdateConfigurations | Não |
> | automationAccounts / webhooks | Não |

## <a name="microsoftavs"></a>Microsoft.AVS

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | privateClouds | Sim |
> | privateClouds/Complementos | Não |
> | privateClouds/autorizações | Não |
> | privateClouds/clusters | Não |
> | privateClouds / globalReachConnections | Não |
> | privateClouds / hcxEnterpriseSites | Não |
> | privateClouds / workloadNetworks | Não |
> | privateClouds / workloadNetworks / dhcpConfigurations | Não |
> | privateClouds/workloadNetworks/gateways | Não |
> | privateClouds / workloadNetworks / portMirroringProfiles | Não |
> | privateClouds/workloadNetworks/segmentos | Não |
> | privateClouds/workloadNetworks/virtualMachines | Não |
> | privateClouds / workloadNetworks / vmGroups | Não |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | environments | Não |
> | ambientes/contas | Não |
> | ambientes/contas/namespaces | Não |
> | ambientes/contas/namespaces/configurações | Não |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | b2cDirectories | Sim |
> | b2ctenants | Não |
> | guestUsages | Sim |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | datacontrollers | Sim |
> | postgresInstances | Sim |
> | sqlManagedInstances | Sim |
> | sqlServerInstances | Sim |
> | sqlServerRegistrations | Sim |
> | sqlServerRegistrations/sqlservers | Não |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | cloudManifestFiles | Não |
> | edgeSubscriptions | Sim |
> | linkedSubscriptions | Sim |
> | registrations | Sim |
> | registros/customerSubscriptions | Não |
> | registrations/products | Não |

## <a name="microsoftazurestackhci"></a>Microsoft.AzureStackHCI

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | clusters | Sim |

## <a name="microsoftbaremetalinfrastructure"></a>Microsoft. BareMetalInfrastructure

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | bareMetalInstances | Sim |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | batchAccounts | Sim |
> | batchAccounts / certificates | Não |
> | batchAccounts / pools | Não |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | billingAccounts | Não |
> | billingAccounts/contratos | Não |
> | billingAccounts / billingPermissions | Não |
> | billingAccounts / billingProfiles | Não |
> | billingAccounts / billingProfiles / billingPermissions | Não |
> | billingAccounts / billingProfiles / billingRoleAssignments | Não |
> | billingAccounts / billingProfiles / billingRoleDefinitions | Não |
> | billingAccounts / billingProfiles / billingSubscriptions | Não |
> | billingAccounts / billingProfiles / createBillingRoleAssignment | Não |
> | billingAccounts/billingProfiles/clientes | Não |
> | billingAccounts/billingProfiles/instruções | Não |
> | billingAccounts/billingProfiles/faturas | Não |
> | billingAccounts/billingProfiles/faturas/pricesheet | Não |
> | billingAccounts/billingProfiles/faturas/transações | Não |
> | billingAccounts / billingProfiles / invoiceSections | Não |
> | billingAccounts / billingProfiles / invoiceSections / billingPermissions | Não |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleAssignments | Não |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleDefinitions | Não |
> | billingAccounts / billingProfiles / invoiceSections / billingSubscriptions | Não |
> | billingAccounts / billingProfiles / invoiceSections / createBillingRoleAssignment | Não |
> | billingAccounts / billingProfiles / invoiceSections / initiateTransfer | Não |
> | billingAccounts/billingProfiles/invoiceSections/produtos | Não |
> | billingAccounts/billingProfiles/invoiceSections/produtos/transferência | Não |
> | billingAccounts/billingProfiles/invoiceSections/Products/updateAutoRenew | Não |
> | billingAccounts/billingProfiles/invoiceSections/transações | Não |
> | billingAccounts/billingProfiles/invoiceSections/transferências | Não |
> | billingAccounts / BillingProfiles / patchOperations | Não |
> | billingAccounts / billingProfiles / paymentMethods | Não |
> | billingAccounts/billingProfiles/políticas | Não |
> | billingAccounts/billingProfiles/pricesheet | Não |
> | billingAccounts / billingProfiles / pricesheetDownloadOperations | Não |
> | billingAccounts/billingProfiles/produtos | Não |
> | billingAccounts/billingProfiles/reservas | Não |
> | billingAccounts/billingProfiles/transações | Não |
> | billingAccounts / billingProfiles / validateDetachPaymentMethodEligibility | Não |
> | billingAccounts / billingRoleAssignments | Não |
> | billingAccounts / billingRoleDefinitions | Não |
> | billingAccounts / billingSubscriptions | Não |
> | billingAccounts/billingSubscriptions/faturas | Não |
> | billingAccounts / createBillingRoleAssignment | Não |
> | billingAccounts / createInvoiceSectionOperations | Não |
> | billingAccounts/clientes | Não |
> | billingAccounts/clientes/billingPermissions | Não |
> | billingAccounts/clientes/billingSubscriptions | Não |
> | billingAccounts/clientes/initiateTransfer | Não |
> | billingAccounts/clientes/políticas | Não |
> | billingAccounts/clientes/produtos | Não |
> | billingAccounts/clientes/transações | Não |
> | billingAccounts/clientes/transferências | Não |
> | billingAccounts/departamentos | Não |
> | billingAccounts/departamentos/billingPermissions | Não |
> | billingAccounts/departamentos/billingRoleAssignments | Não |
> | billingAccounts/departamentos/billingRoleDefinitions | Não |
> | billingAccounts / enrollmentAccounts | Não |
> | billingAccounts / enrollmentAccounts / billingPermissions | Não |
> | billingAccounts / enrollmentAccounts / billingRoleAssignments | Não |
> | billingAccounts / enrollmentAccounts / billingRoleDefinitions | Não |
> | billingAccounts/faturas | Não |
> | billingAccounts/faturas/transações | Não |
> | billingAccounts / invoiceSections | Não |
> | billingAccounts / invoiceSections / billingSubscriptionMoveOperations | Não |
> | billingAccounts / invoiceSections / billingSubscriptions | Não |
> | billingAccounts/invoiceSections/billingSubscriptions/transferência | Não |
> | billingAccounts/invoiceSections/Elevate | Não |
> | billingAccounts / invoiceSections / initiateTransfer | Não |
> | billingAccounts / invoiceSections / patchOperations | Não |
> | billingAccounts / invoiceSections / productMoveOperations | Não |
> | billingAccounts/invoiceSections/produtos | Não |
> | billingAccounts/invoiceSections/produtos/transferência | Não |
> | billingAccounts/invoiceSections/Products/updateAutoRenew | Não |
> | billingAccounts/invoiceSections/transações | Não |
> | billingAccounts/invoiceSections/transferências | Não |
> | billingAccounts / lineOfCredit | Não |
> | billingAccounts / patchOperations | Não |
> | billingAccounts / paymentMethods | Não |
> | billingAccounts/produtos | Não |
> | billingAccounts/reservas | Não |
> | billingAccounts/transações | Não |
> | billingPeriods | Não |
> | billingPermissions | Não |
> | billingProperty | Não |
> | billingRoleAssignments | Não |
> | billingRoleDefinitions | Não |
> | createBillingRoleAssignment | Não |
> | departments | Não |
> | enrollmentAccounts | Não |
> | invoices | Não |
> | transfers | Não |
> | transferências/acceptTransfer | Não |
> | transferências/declineTransfer | Não |
> | transferências/operationStatus | Não |
> | transferências/validateTransfer | Não |
> | validateAddress | Não |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | mapApis | Sim |
> | updateCommunicationPreference | Não |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | blockchainMembers | Sim |
> | cordaMembers | Sim |
> | watchers | Sim |

## <a name="microsoftblockchaintokens"></a>Microsoft.BlockchainTokens

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | Tokenservices | Sim |
> | Tokenservices/BlockchainNetworks | Não |
> | Tokenservices/grupos | Não |
> | Tokenservices/grupos/contas | Não |
> | Tokenservices/TokenTemplates | Não |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | blueprintAssignments | Não |
> | blueprintAssignments / assignmentOperations | Não |
> | blueprintAssignments/operações | Não |
> | blueprints | Não |
> | blueprints/artifacts | Não |
> | blueprints/versions | Não |
> | blueprints/versions/artifacts | Não |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | botServices | Sim |
> | botServices / channels | Não |
> | botServices/conexões | Não |
> | idiomas | Não |
> | modelos | Não |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | Redis | Sim |
> | Redis/EventGridFilters | Não |
> | Redis/privateEndpointConnectionProxies | Não |
> | Redis/privateEndpointConnectionProxies/validar | Não |
> | Redis/privateEndpointConnections | Não |
> | Redis/privateLinkResources | Não |
> | redisEnterprise | Sim |
> | RedisEnterprise / privateEndpointConnectionProxies | Não |
> | RedisEnterprise/privateEndpointConnectionProxies/validar | Não |
> | RedisEnterprise / privateEndpointConnections | Não |
> | RedisEnterprise / privateLinkResources | Não |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | appliedReservations | Não |
> | autoQuotaIncrease | Não |
> | calculateExchange | Não |
> | calculatePrice | Não |
> | calculatePurchasePrice | Não |
> | catalogs | Não |
> | commercialReservationOrders | Não |
> | câmbio | Não |
> | ownReservations | Não |
> | placePurchaseOrder | Não |
> | reservationOrders | Não |
> | reservationOrders / calculateRefund | Não |
> | reservationOrders/mesclagem | Não |
> | reservationOrders/reservas | Não |
> | reservationOrders/reservas/revisões | Não |
> | reservationOrders/retorno | Não |
> | reservationOrders/divisão | Não |
> | reservationOrders/permuta | Não |
> | reservations | Não |
> | resourceProviders | Não |
> | recursos | Não |
> | validateReservationOrder | Não |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | Não |
> | CdnWebApplicationFirewallPolicies | Sim |
> | edgenodes | Não |
> | perfis | Sim |
> | profiles/endpoints | Sim |
> | profiles/endpoints/customdomains | Não |
> | profiles/endpoints/origingroups | Não |
> | profiles/endpoints/origins | Não |
> | validateProbe | Não |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | certificateOrders | Sim |
> | certificateOrders/certificados | Não |
> | validateCertificateRegistrationInformation | Não |

## <a name="microsoftchangeanalysis"></a>Microsoft.ChangeAnalysis

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | perfil | Não |
> | resourceChanges | Não |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | funcionalidades | Não |
> | domainNames | Sim |
> | nome_do_domínio/recursos | Não |
> | nome_do_domínio/internalLoadBalancers | Não |
> | DomainNames/certificados | Não |
> | nome_do_domínio/Slots | Não |
> | nome_do_domínio/Slots/funções | Não |
> | nome_do_domínio/Slots/funções/metricDefinitions | Não |
> | DomainNames/Slots/funções/métricas | Não |
> | moveSubscriptionResources | Não |
> | operatingSystemFamilies | Não |
> | operatingSystems | Não |
> | quotas | Não |
> | resourceTypes | Não |
> | validateSubscriptionMoveAvailability | Não |
> | virtualMachines | Sim |
> | virtualMachines/diagnosticSettings | Não |
> | virtualMachines/metricDefinitions | Não |
> | virtualMachines/métricas | Não |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | Migração de infraestrutura clássica | Não |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | funcionalidades | Não |
> | expressRouteCrossConnections | Não |
> | expressRouteCrossConnections/emparelhamentos | Não |
> | gatewaySupportedDevices | Não |
> | networkSecurityGroups | Sim |
> | quotas | Não |
> | reservedIps | Sim |
> | virtualNetworks | Sim |
> | virtualNetworks/remoteVirtualNetworkPeeringProxies | Não |
> | virtualNetworks / virtualNetworkPeerings | Não |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | funcionalidades | Não |
> | disks | Não |
> | images | Não |
> | osImages | Não |
> | osPlatformImages | Não |
> | publicImages | Não |
> | quotas | Não |
> | storageAccounts | Sim |
> | storageAccounts / blobServices | Não |
> | storageAccounts / fileServices | Não |
> | storageAccounts/metricDefinitions | Não |
> | storageAccounts/métricas | Não |
> | storageAccounts/queueservices | Não |
> | storageAccounts/serviços | Não |
> | storageAccounts/serviços/diagnosticSettings | Não |
> | storageAccounts/serviços/metricDefinitions | Não |
> | storageAccounts/serviços/métricas | Não |
> | storageAccounts/tabelaservices | Não |
> | storageAccounts/vmImages | Não |
> | vmImages | Não |

## <a name="microsoftcodespaces"></a>Microsoft. Codespaces

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | planos | Sim |
> | registeredSubscriptions | Não |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | accounts | Sim |
> | contas/privateEndpointConnectionProxies | Não |
> | contas/privateEndpointConnections | Não |
> | contas/privateLinkResources | Não |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | RateCard | Não |
> | UsageAggregates | Não |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | availabilitySets | Sim |
> | cloudServices | Sim |
> | nuvens/networkInterfaces | Não |
> | cloudservices/publicIPAddresses | Não |
> | cloudservices/roleInstances | Não |
> | cloudservices/roleInstances/networkInterfaces | Não |
> | cloudservices/funções | Não |
> | diskAccesses | Sim |
> | diskEncryptionSets | Sim |
> | disks | Sim |
> | galleries | Sim |
> | galleries / applications | Não |
> | galerias/aplicativos/versões | Não |
> | galleries/images | Não |
> | galleries/images/versions | Não |
> | Hosts | Sim |
> | Hosts/hosts | Sim |
> | images | Sim |
> | proximityPlacementGroups | Sim |
> | restorePointCollections | Sim |
> | restorePointCollections / restorePoints | Não |
> | sharedVMExtensions | Sim |
> | sharedVMExtensions/versões | Não |
> | sharedVMImages | Sim |
> | sharedVMImages/versões | Não |
> | snapshots | Sim |
> | sshPublicKeys | Sim |
> | virtualMachines | Sim |
> | virtualMachines/extensões | Sim |
> | virtualMachines/metricDefinitions | Não |
> | virtualMachines/runCommands | Sim |
> | virtualMachineScaleSets | Sim |
> | virtualMachineScaleSets/extensões | Não |
> | virtualMachineScaleSets/networkInterfaces | Não |
> | virtualMachineScaleSets/publicIPAddresses | Não |
> | virtualMachineScaleSets/virtualMachines | Não |
> | virtualMachineScaleSets/virtualMachines/networkInterfaces | Não |

## <a name="microsoftconnectedcache"></a>Microsoft. ConnectedCache

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | CacheNodes | Sim |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | AggregatedCost | Não |
> | Saldos | Não |
> | Orçamentos | Não |
> | Charges | Não |
> | CostTags | Não |
> | credits | Não |
> | events | Não |
> | Previsões | Não |
> | lots | Não |
> | Marketplaces | Não |
> | Pricesheets | Não |
> | products | Não |
> | ReservationDetails | Não |
> | ReservationRecommendationDetails | Não |
> | ReservationRecommendations | Não |
> | ReservationSummaries | Não |
> | ReservationTransactions | Não |
> | Marcas | Não |
> | tenants | Não |
> | Termos | Não |
> | UsageDetails | Não |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | containerGroups | Sim |
> | serviceAssociationLinks | Não |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | registries | Sim |
> | registros/agentPools | Sim |
> | registries/builds | Não |
> | registries/builds/cancel | Não |
> | registros/Builds/getLogLink | Não |
> | registries / buildTasks | Sim |
> | registros/buildTasks/etapas | Não |
> | registros/eventGridFilters | Não |
> | registros/exportPipelines | Não |
> | registros/generateCredentials | Não |
> | registros/getBuildSourceUploadUrl | Não |
> | registros/GetCredentials | Não |
> | registros/importImage | Não |
> | registros/importPipelines | Não |
> | registros/pipelineRuns | Não |
> | registros/privateEndpointConnectionProxies | Não |
> | registros/privateEndpointConnectionProxies/Validate | Não |
> | registros/privateEndpointConnections | Não |
> | registros/privateLinkResources | Não |
> | registros/queueBuild | Não |
> | registros/regenerateCredential | Não |
> | registros/regenerateCredentials | Não |
> | registries/replications | Sim |
> | registries/runs | Não |
> | registries/runs/cancel | Não |
> | registros/scheduleRun | Não |
> | registries / scopeMaps | Não |
> | registros/taskRuns | Não |
> | registries/tasks | Sim |
> | registries/tokens | Não |
> | registros/updatePolicies | Não |
> | registries/webhooks | Sim |
> | registros/WebHooks/getCallbackConfig | Não |
> | registries/webhooks/ping | Não |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | containerServices | Sim |
> | managedClusters | Sim |
> | openShiftManagedClusters | Sim |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | Alertas | Não |
> | BillingAccounts | Não |
> | Orçamentos | Não |
> | CloudConnectors | Não |
> | Conectores | Sim |
> | costAllocationRules | Não |
> | Departments | Não |
> | Dimensões | Não |
> | EnrollmentAccounts | Não |
> | Exportações | Não |
> | ExternalBillingAccounts | Não |
> | ExternalBillingAccounts/alertas | Não |
> | ExternalBillingAccounts/dimensões | Não |
> | ExternalBillingAccounts/previsão | Não |
> | ExternalBillingAccounts/consulta | Não |
> | ExternalSubscriptions | Não |
> | ExternalSubscriptions/alertas | Não |
> | ExternalSubscriptions/dimensões | Não |
> | ExternalSubscriptions/previsão | Não |
> | ExternalSubscriptions/consulta | Não |
> | Previsão | Não |
> | Insights | Não |
> | Consulta | Não |
> | register | Não |
> | Reportconfigs | Não |
> | Relatórios | Não |
> | Configurações | Não |
> | showbackRules | Não |
> | Exibições | Não |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | solicitações | Não |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | associations | Não |
> | resourceProviders | Sim |

## <a name="microsoftd365customerinsights"></a>Microsoft. D365CustomerInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | instances | Sim |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | jobs | Sim |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | DataBoxEdgeDevices | Sim |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | workspaces | Sim |
> | espaços de trabalho/dbWorkspaces | Não |
> | espaços de trabalho/virtualNetworkPeerings | Não |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | catalogs | Sim |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | dataFactories | Sim |
> | datafactorings/diagnosticSettings | Não |
> | datafactorings/metricDefinitions | Não |
> | dataFactorySchema | Não |
> | factories | Sim |
> | factories / integrationRuntimes | Não |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | accounts | Sim |
> | accounts / dataLakeStoreAccounts | Não |
> | accounts / storageAccounts | Não |
> | contas/storageAccounts/contêineres | Não |
> | contas/transferAnalyticsUnits | Não |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | accounts | Sim |
> | contas/eventGridFilters | Não |
> | accounts / firewallRules | Não |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | services | Sim |
> | services/projects | Sim |

## <a name="microsoftdataprotection"></a>Microsoft.DataProtection

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | BackupVaults | Sim |
> | ResourceOperationGateKeepers | Sim |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | accounts | Sim |
> | accounts/shares | Não |
> | accounts/shares/datasets | Não |
> | accounts/shares/invitations | Não |
> | accounts/shares/providersharesubscriptions | Não |
> | contas/compartilhamentos/synchronizationSettings | Não |
> | accounts/sharesubscriptions | Não |
> | contas/sharesubscriptions/consumerSourceDataSets | Não |
> | accounts/sharesubscriptions/datasetmappings | Não |
> | accounts/sharesubscriptions/triggers | Não |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | servers | Sim |
> | servers/advisors | Não |
> | servers/keys | Não |
> | servidores/privateEndpointConnectionProxies | Não |
> | servidores/privateEndpointConnections | Não |
> | servidores/privateLinkResources | Não |
> | servidores/queryTexts | Não |
> | servidores/recoverableServers | Não |
> | servidores/iniciar | Não |
> | servidores/parar | Não |
> | servidores/topQueryStatistics | Não |
> | servers / virtualNetworkRules | Não |
> | servidores/waitStatistics | Não |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | flexibleServers | Sim |
> | servers | Sim |
> | servers/advisors | Não |
> | servers/keys | Não |
> | servidores/privateEndpointConnectionProxies | Não |
> | servidores/privateEndpointConnections | Não |
> | servidores/privateLinkResources | Não |
> | servidores/queryTexts | Não |
> | servidores/recoverableServers | Não |
> | servidores/iniciar | Não |
> | servidores/parar | Não |
> | servidores/topQueryStatistics | Não |
> | servidores/atualização | Não |
> | servers / virtualNetworkRules | Não |
> | servidores/waitStatistics | Não |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | flexibleServers | Sim |
> | serverGroups | Sim |
> | servers | Sim |
> | servers/advisors | Não |
> | servers/keys | Não |
> | servidores/privateEndpointConnectionProxies | Não |
> | servidores/privateEndpointConnections | Não |
> | servidores/privateLinkResources | Não |
> | servidores/queryTexts | Não |
> | servidores/recoverableServers | Não |
> | servidores/topQueryStatistics | Não |
> | servers / virtualNetworkRules | Não |
> | servidores/waitStatistics | Não |
> | serversv2 | Sim |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | artifactSources | Sim |
> | rollouts | Sim |
> | pertopologias | Sim |
> | serviços e pertopologias | Sim |
> | pertopologias/serviços/unidades de serviço | Sim |
> | etapas | Sim |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | applicationgroups | Sim |
> | applicationgroups/applications | Não |
> | applicationgroups/desktops | Não |
> | applicationgroups/startmenuitems | Não |
> | hostpools | Sim |
> | hostpools / msixpackages | Não |
> | hostpools/sessionhosts | Não |
> | hostpools/sessionhosts/usersessions | Não |
> | hostpools/usersessions | Não |
> | workspaces | Sim |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | ElasticPools | Sim |
> | ElasticPools / IotHubTenants | Sim |
> | ElasticPools/IotHubTenants/securitySettings | Não |
> | IotHubs | Sim |
> | IotHubs/eventGridFilters | Não |
> | IotHubs/securitySettings | Não |
> | ProvisioningServices | Sim |
> | usages | Não |

## <a name="microsoftdeviceupdate"></a>Microsoft. DeviceUpdate

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | accounts | Sim |
> | contas/instâncias | Sim |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | pipelines | Sim |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | controladores | Sim |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | labcenters | Sim |
> | labs | Sim |
> | labs/environments | Sim |
> | laboratórios/perrunners | Sim |
> | laboratórios/virtualMachines | Sim |
> | schedules | Sim |

## <a name="microsoftdigitaltwins"></a>Microsoft.DigitalTwins

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | digitalTwinsInstances | Sim |
> | digitalTwinsInstances/pontos de extremidade | Não |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | databaseAccounts | Não |
> | databaseAccounts | Sim |
> | restorableDatabaseAccounts | Não |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | domains | Sim |
> | domínios/domainOwnershipIdentifiers | Não |
> | generateSsoRequest | Não |
> | topLevelDomains | Não |
> | validateDomainRegistrationInformation | Não |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | lcsprojects | Não |
> | lcsprojects / clouddeployments | Não |
> | lcsprojects/conectores | Não |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | services | Sim |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | domains | Sim |
> | domains/topics | Não |
> | eventSubscriptions | Não |
> | extensionTopics | Não |
> | partnerNamespaces | Sim |
> | partnerNamespaces/eventChannels | Não |
> | partnerRegistrations | Sim |
> | partnerTopics | Sim |
> | partnerTopics/eventSubscriptions | Não |
> | systemTopics | Sim |
> | systemTopics/eventSubscriptions | Não |
> | topics | Sim |
> | topicTypes | Não |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | clusters | Sim |
> | namespaces | Sim |
> | namespaces/authorizationrules | Não |
> | namespaces/disasterrecoveryconfigs | Não |
> | namespaces/eventhubs | Não |
> | namespaces/eventhubs/authorizationrules | Não |
> | namespaces/eventhubs/consumergroups | Não |
> | namespaces/networkrulesets | Não |
> | namespaces/privateEndpointConnections | Não |

## <a name="microsoftexperimentation"></a>Microsoft.Experimentation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | experimentWorkspaces | Sim |

## <a name="microsoftfalcon"></a>Microsoft.Falcon

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | namespaces | Sim |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | featureProviders | Não |
> | recursos | Não |
> | providers | Não |
> | subscriptionFeatureRegistrations | Não |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | enroll | Não |
> | galleryitems | Não |
> | generateartifactaccessuri | Não |
> | myareas | Não |
> | myddds/áreas | Não |
> | myddds/áreas/áreas | Não |
> | myddds/áreas/áreas/galleryitems | Não |
> | myddds/áreas/galleryitems | Não |
> | myareas/galleryitems | Não |
> | register | Não |
> | recursos | Não |
> | retrieveresourcesbyid | Não |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | accounts | Sim |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | autoManagedAccounts | Sim |
> | autoManagedVmConfigurationProfiles | Sim |
> | configurationProfileAssignments | Não |
> | guestConfigurationAssignments | Não |
> | software | Não |
> | softwareUpdateProfile | Não |
> | softwareUpdates | Não |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | hanaInstances | Sim |
> | sapMonitors | Sim |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | dedicatedHSMs | Sim |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | clusters | Sim |
> | clusters/applications | Não |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | services | Sim |
> | serviços/iomtconnectors | Não |
> | serviços/iomtconnectors/conexões | Não |
> | serviços/iomtconnectors/mapeamentos | Não |
> | serviços/privateEndpointConnectionProxies | Não |
> | serviços/privateEndpointConnections | Não |
> | serviços/privateLinkResources | Não |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | machines | Sim |
> | computadores/assessPatches | Não |
> | machines/extensions | Sim |
> | computadores/installPatches | Não |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | Gerenciadores de DataManager | Sim |

## <a name="microsofthybridnetwork"></a>Microsoft. HybridNetwork

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | dispositivos | Sim |
> | networkFunctions | Sim |
> | networkFunctionVendors | Não |
> | registeredSubscriptions | Não |
> | selecionados | Não |
> | fornecedores/vendorSkus | Não |
> | fornecedores/vendorSkus/previewSubscriptions | Não |
> | virtualNetworkFunctions | Sim |
> | virtualNetworkFunctionVendors | Não |

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | components | Sim |
> | networkScopes | Sim |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | jobs | Sim |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | diagnosticSettings | Não |
> | diagnosticSettingsCategories | Não |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | appTemplates | Não |
> | IoTApps | Sim |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | Grafo | Sim |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | deletedVaults | Não |
> | hsmPools | Sim |
> | managedHSMs | Sim |
> | vaults | Sim |
> | cofres/accessPolicies | Não |
> | cofres/eventGridFilters | Não |
> | cofres/chaves | Não |
> | cofres/chaves/versões | Não |
> | vaults/secrets | Não |

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | connectedClusters | Sim |
> | registeredSubscriptions | Não |

## <a name="microsoftkubernetesconfiguration"></a>Microsoft.KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | extensions | Não |
> | sourceControlConfigurations | Não |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | clusters | Sim |
> | clusters/attacheddatabaseconfigurations | Não |
> | clusters/databases | Não |
> | clusters/databases/dataconnections | Não |
> | clusters/databases/eventhubconnections | Não |
> | clusters/databases/principalassignments | Não |
> | clusters/conexões de dataconexão | Não |
> | clusters/principalassignments | Não |
> | clusters/sharedidentities | Não |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | labaccounts | Sim |
> | users | Não |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | hostingEnvironments | Sim |
> | integrationAccounts | Sim |
> | integrationServiceEnvironments | Sim |
> | integrationServiceEnvironments / managedApis | Sim |
> | isolatedEnvironments | Sim |
> | workflows | Sim |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | commitmentPlans | Sim |
> | webServices | Sim |
> | Workspaces | Sim |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | workspaces | Sim |
> | espaços de trabalho/batchEndpoints | Sim |
> | espaços de trabalho/batchEndpoints/implantações | Sim |
> | espaços de trabalho/códigos | Não |
> | espaços de trabalho/códigos/versões | Não |
> | workspaces/computes | Não |
> | espaços de trabalho/repositórios de armazenamento | Não |
> | espaços de trabalho/eventGridFilters | Não |
> | espaços de trabalho/trabalhos | Não |
> | espaços de trabalho/labelingJobs | Não |
> | espaços de trabalho/linksservices | Não |
> | espaços de trabalho/modelos | Não |
> | espaços de trabalho/modelos/versões | Não |
> | espaços de trabalho/onlineEndpoints | Sim |
> | espaços de trabalho/onlineEndpoints/implantações | Sim |

## <a name="microsoftmaintenance"></a>Microsoft.Maintenance

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | applyUpdates | Não |
> | configurationAssignments | Não |
> | maintenanceConfigurations | Sim |
> | publicMaintenanceConfigurations | Não |
> | updates | Não |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | Identidades | Não |
> | userAssignedIdentities | Sim |

## <a name="microsoftmanagednetwork"></a>Microsoft.ManagedNetwork

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | managedNetworks | Sim |
> | managedNetworks / managedNetworkGroups | Sim |
> | managedNetworks / managedNetworkPeeringPolicies | Sim |
> | notificação | Sim |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | marketplaceRegistrationDefinitions | Não |
> | registrationAssignments | Não |
> | registrationDefinitions | Não |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | getEntities | Não |
> | managementGroups | Não |
> | managementGroups/configurações | Não |
> | recursos | Não |
> | startTenantBackfill | Não |
> | tenantBackfillStatus | Não |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | accounts | Sim |
> | contas/eventGridFilters | Não |
> | contas/privateAtlases | Sim |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | macc | Não |
> | offers | Não |
> | offerTypes | Não |
> | offerTypes/Publicadores | Não |
> | offerTypes/Publicadores/ofertas | Não |
> | offerTypes/Publicadores/ofertas/planos | Não |
> | offerTypes/Publicadores/ofertas/planos/contratos | Não |
> | offerTypes/Publicadores/ofertas/planos/configurações | Não |
> | offerTypes/Publicadores/ofertas/planos/configurações/importImage | Não |
> | privategalleryitems | Não |
> | privateStoreClient | Não |
> | privateStores | Não |
> | privateStores/ofertas | Não |
> | products | Não |
> | publishers | Não |
> | publishers/offers | Não |
> | publishers/offers/amendments | Não |
> | register | Não |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | classicDevServices | Sim |
> | updateCommunicationPreference | Não |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | Contratos | Não |
> | offertypes | Não |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | mediaservices | Sim |
> | mediaservices/accountFilters | Não |
> | mediaservices/assets | Não |
> | mediaservices/ativos/assetFilters | Não |
> | mediaservices/contentKeyPolicies | Não |
> | mediaservices/eventGridFilters | Não |
> | mediaservices/liveEventOperations | Não |
> | mediaservices / liveEvents | Sim |
> | mediaservices / liveEvents / liveOutputs | Não |
> | mediaservices/liveOutputOperations | Não |
> | mediaservices/mediaGraphs | Não |
> | mediaservices/privateEndpointConnectionOperations | Não |
> | mediaservices/privateEndpointConnectionProxies | Não |
> | mediaservices/privateEndpointConnections | Não |
> | mediaservices/streamingEndpointOperations | Não |
> | mediaservices / streamingEndpoints | Sim |
> | mediaservices/streamingLocators | Não |
> | mediaservices/streamingPolicies | Não |
> | mediaservices/transforms | Não |
> | mediaservices/transforms/jobs | Não |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | appClusters | Sim |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | assessmentProjects | Sim |
> | migrateprojects | Sim |
> | moveCollections | Sim |
> | projects | Sim |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | holographicsBroadcastAccounts | Sim |
> | objectUnderstandingAccounts | Sim |
> | remoteRenderingAccounts | Sim |
> | spatialAnchorsAccounts | Sim |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | netAppAccounts | Sim |
> | netAppAccounts / accountBackups | Não |
> | netAppAccounts / capacityPools | Sim |
> | netAppAccounts/capacityPools/volumes | Sim |
> | netAppAccounts/capacityPools/volumes/instantâneos | Não |
## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | applicationGateways | Sim |
> | applicationGatewayWebApplicationFirewallPolicies | Sim |
> | applicationSecurityGroups | Sim |
> | azureFirewallFqdnTags | Não |
> | azureFirewalls | Sim |
> | bastionHosts | Sim |
> | bgpServiceCommunities | Não |
> | connections | Sim |
> | ddosCustomPolicies | Sim |
> | ddosProtectionPlans | Sim |
> | dnsOperationStatuses | Não |
> | dnszones | Sim |
> | dnszones/A | Não |
> | dnszones/AAAA | Não |
> | dnszones/all | Não |
> | dnszones/CAA | Não |
> | dnszones/CNAME | Não |
> | dnszones/MX | Não |
> | dnszones/NS | Não |
> | dnszones/PTR | Não |
> | dnszones/recordsets | Não |
> | dnszones/SOA | Não |
> | dnszones/SRV | Não |
> | dnszones/TXT | Não |
> | expressRouteCircuits | Sim |
> | expressRouteCrossConnections | Sim |
> | expressRouteGateways | Sim |
> | expressRoutePorts | Sim |
> | expressRouteServiceProviders | Não |
> | firewallPolicies | Sim |
> | frontdoors | Sim |
> | frontdoorWebApplicationFirewallManagedRuleSets | Não |
> | frontdoorWebApplicationFirewallPolicies | Sim |
> | getDnsResourceReference | Não |
> | internalNotify | Não |
> | ipGroups | Sim |
> | loadBalancers | Sim |
> | virtualNetworkGateways | Sim |
> | natGateways | Sim |
> | networkIntentPolicies | Sim |
> | networkInterfaces | Sim |
> | networkProfiles | Sim |
> | networkSecurityGroups | Sim |
> | networkWatchers | Sim |
> | networkWatchers / connectionMonitors | Sim |
> | networkWatchers / flowLogs | Sim |
> | networkWatchers/lentes | Sim |
> | networkWatchers / pingMeshes | Sim |
> | p2sVpnGateways | Sim |
> | privateDnsOperationStatuses | Não |
> | privateDnsZones | Sim |
> | privateDnsZones/A | Não |
> | privateDnsZones/AAAA | Não |
> | privateDnsZones/tudo | Não |
> | privateDnsZones/CNAME | Não |
> | privateDnsZones/MX | Não |
> | privateDnsZones/PTR | Não |
> | privateDnsZones/SOA | Não |
> | privateDnsZones/SRV | Não |
> | privateDnsZones/TXT | Não |
> | privateDnsZones / virtualNetworkLinks | Sim |
> | privateEndpoints | Sim |
> | privateLinkServices | Sim |
> | publicIPAddresses | Sim |
> | publicIPPrefixes | Sim |
> | routeFilters | Sim |
> | routeTables | Sim |
> | serviceEndpointPolicies | Sim |
> | trafficManagerGeographicHierarchies | Não |
> | trafficmanagerprofiles | Sim |
> | trafficmanagerprofiles/calor | Não |
> | trafficManagerUserMetricsKeys | Não |
> | virtualWans | Sim |
> | virtualNetworkGateways | Sim |
> | virtualNetworks | Sim |
> | virtualNetworks/sub-redes | Não |
> | virtualNetworkTaps | Sim |
> | virtualWans | Sim |
> | vpnGateways | Sim |
> | vpnSites | Sim |
> | frontdoorWebApplicationFirewallPolicies | Sim |

## <a name="microsoftnotebooks"></a>Microsoft. notebooks

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | NotebookProxies | Não |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | namespaces | Sim |
> | namespaces / notificationHubs | Sim |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | osNamespaces | Sim |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | HyperVSites | Sim |
> | ImportSites | Sim |
> | MasterSites | Sim |
> | ServerSites | Sim |
> | VMwareSites | Sim |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | clusters | Sim |
> | deletedWorkspaces | Não |
> | linkTargets | Não |
> | storageInsightConfigs | Não |
> | workspaces | Sim |
> | espaços de trabalho/exportações | Não |
> | espaços de trabalho/fontes de fonte | Não |
> | espaços de trabalho/linksservices | Não |
> | espaços de trabalho/linkedStorageAccounts | Não |
> | workspaces/metadata | Não |
> | workspaces/query | Não |
> | espaços de trabalho/scopedPrivateLinkProxies | Não |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | managementassociations | Não |
> | managementconfigurations | Sim |
> | solutions | Sim |
> | Modos de exibição | Sim |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | legacyPeerings | Não |
> | peerAsns | Não |
> | peerings | Sim |
> | peeringServiceCountries | Não |
> | peeringServiceProviders | Não |
> | peeringServices | Sim |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | atestados | Não |
> | policyEvents | Não |
> | policyMetadata | Não |
> | policyStates | Não |
> | policyTrackedResources | Não |
> | remediations | Não |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | consoles | Não |
> | dashboards | Sim |
> | userSettings | Não |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | privateLinkServicesForPowerBI | Sim |
> | tenants | Sim |
> | locatários/espaços de trabalho | Não |
> | workspaceCollections | Sim |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | capacities | Sim |

## <a name="microsoftprojectbabylon"></a>Microsoft.ProjectBabylon

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | accounts | Sim |
> | deletedAccounts | Não |

## <a name="microsoftproviderhub"></a>Microsoft.ProviderHub

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | providerRegistrations | Não |
> | providerRegistrations/condistribuições | Não |
> | providerRegistrations / resourceTypeRegistrations | Não |
> | rollouts | Sim |

## <a name="microsoftquantum"></a>Microsoft.Quantum

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | Workspaces | Sim |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | backupProtectedItems | Não |
> | vaults | Sim |

## <a name="microsoftredhatopenshift"></a>Microsoft.RedHatOpenShift

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | OpenShiftClusters | Sim |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | namespaces | Sim |
> | namespaces/authorizationrules | Não |
> | namespaces/hybridconnections | Não |
> | namespaces/hybridconnections/authorizationrules | Não |
> | namespaces/privateEndpointConnections | Não |
> | namespaces/wcfrelays | Não |
> | namespaces/wcfrelays/authorizationrules | Não |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | consultas | Sim |
> | resourceChangeDetails | Não |
> | resourceChanges | Não |
> | recursos | Não |
> | resourcesHistory | Não |
> | subscriptionsStatus | Não |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | availabilityStatuses | Não |
> | childAvailabilityStatuses | Não |
> | childResources | Não |
> | emergingissues | Não |
> | events | Não |
> | impactedResources | Não |
> | metadata | Não |
> | Notificações | Não |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | calculateTemplateHash | Não |
> | deployments | Não |
> | deployments/operations | Não |
> | deploymentScripts | Sim |
> | deploymentScripts/logs | Não |
> | links | Não |
> | notifyResourceJobs | Não |
> | providers | Não |
> | resourceGroups | Não |
> | subscriptions | Não |
> | templateSpecs | Sim |
> | templateSpecs/versões | Sim |
> | tenants | Não |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | de dimensionamento da Web | Sim |
> | saasresources | Não |

## <a name="microsoftscvmm"></a>Microsoft. ScVmm

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | nuvens | Sim |
> | Máquinas Virtuais | Sim |
> | VirtualMachineTemplates | Sim |
> | VirtualNetworks | Sim |
> | vmmservers | Sim |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | resourceHealthMetadata | Não |
> | searchServices | Sim |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | adaptiveNetworkHardenings | Não |
> | advancedThreatProtectionSettings | Não |
> | alertas | Não |
> | alertsSuppressionRules | Não |
> | allowedConnections | Não |
> | applicationWhitelistings | Não |
> | assessmentMetadata | Não |
> | assessments | Não |
> | autoDismissAlertsRules | Não |
> | automations | Sim |
> | AutoProvisioningSettings | Não |
> | Compliances | Não |
> | conectores | Não |
> | dataCollectionAgents | Não |
> | deviceSecurityGroups | Não |
> | discoveredSecuritySolutions | Não |
> | externalSecuritySolutions | Não |
> | InformationProtectionPolicies | Não |
> | iotDefenderSettings | Não |
> | iotSecuritySolutions | Sim |
> | iotSecuritySolutions / analyticsModels | Não |
> | iotSecuritySolutions / analyticsModels / aggregatedAlerts | Não |
> | iotSecuritySolutions / analyticsModels / aggregatedRecommendations | Não |
> | iotSecuritySolutions / iotAlerts | Não |
> | iotSecuritySolutions / iotAlertTypes | Não |
> | iotSecuritySolutions / iotRecommendations | Não |
> | iotSecuritySolutions / iotRecommendationTypes | Não |
> | iotSensors | Não |
> | jitNetworkAccessPolicies | Não |
> | jitPolicies | Não |
> | políticas | Não |
> | pricings | Não |
> | regulatoryComplianceStandards | Não |
> | regulatoryComplianceStandards / regulatoryComplianceControls | Não |
> | regulatoryComplianceStandards / regulatoryComplianceControls / regulatoryComplianceAssessments | Não |
> | secureScoreControlDefinitions | Não |
> | secureScoreControls | Não |
> | secureScores | Não |
> | secureScores / secureScoreControls | Não |
> | securityContacts | Não |
> | securitySolutions | Não |
> | securitySolutionsReferenceData | Não |
> | securityStatuses | Não |
> | securityStatusesSummaries | Não |
> | serverVulnerabilityAssessments | Não |
> | configurações | Não |
> | sqlVulnerabilityAssessments | Não |
> | subavaliações | Não |
> | tarefas | Não |
> | topologies | Não |
> | workspaceSettings | Não |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | diagnosticSettings | Não |
> | diagnosticSettingsCategories | Não |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | aggregations | Não |
> | alertRules | Não |
> | alertRuleTemplates | Não |
> | automationRules | Não |
> | bookmarks | Não |
> | cases | Não |
> | dataconnecters | Não |
> | dataConnectorsCheckRequirements | Não |
> | entidades | Não |
> | entityQueries | Não |
> | incidents | Não |
> | officeConsents | Não |
> | configurações | Não |
> | threatIntelligence | Não |
> | watchlists | Não |

## <a name="microsoftserialconsole"></a>Microsoft.SerialConsole

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | consoleservices | Não |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | namespaces | Sim |
> | namespaces/authorizationrules | Não |
> | namespaces/disasterrecoveryconfigs | Não |
> | namespaces/eventgridfilters | Não |
> | namespaces/networkrulesets | Não |
> | namespaces/privateEndpointConnections | Não |
> | namespaces/queues | Não |
> | namespaces/queues/authorizationrules | Não |
> | namespaces/topics | Não |
> | namespaces/topics/authorizationrules | Não |
> | namespaces/topics/subscriptions | Não |
> | namespaces/topics/subscriptions/rules | Não |
> | premiumMessagingRegions | Não |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | de dimensionamento da Web | Sim |
> | clusters | Sim |
> | clusters/applications | Não |
> | containerGroups | Sim |
> | containerGroupSets | Sim |
> | edgeclusters | Sim |
> | edgeclusters/aplicativos | Não |
> | managedclusters | Sim |
> | managedclusters/NodeTypes | Não |
> | networks | Sim |
> | secretstores | Sim |
> | secretstores/certificados | Não |
> | secretstores/segredos | Não |
> | volumes | Sim |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | de dimensionamento da Web | Sim |
> | containerGroups | Sim |
> | gateways | Sim |
> | networks | Sim |
> | segredos | Sim |
> | volumes | Sim |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | providerRegistrations | Não |
> | providerRegistrations / resourceTypeRegistrations | Não |
> | rollouts | Sim |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | SignalR | Sim |
> | Sinalização/eventGridFilters | Não |

## <a name="microsoftsingularity"></a>Microsoft. singularidade

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | accounts | Sim |
> | contas/accountQuotaPolicies | Não |
> | contas/groupPolicies | Não |
> | contas/trabalhos | Não |
> | contas/storageContainers | Não |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | hybridUseBenefits | Não |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | applicationDefinitions | Sim |
> | de dimensionamento da Web | Sim |
> | jitRequests | Sim |

## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | managedInstances | Sim |
> | managedInstances/bancos de dados | Sim |
> | managedInstances/bancos de dados/backupShortTermRetentionPolicies | Não |
> | managedInstances/bancos de dados/esquemas/tabelas/colunas/sensitivityLabels | Não |
> | managedInstances/bancos de dados/vulnerabilityAssessments | Não |
> | managedInstances/bancos de dados/vulnerabilityAssessments/regras/linhas de base | Não |
> | managedInstances / encryptionProtector | Não |
> | managedInstances/chaves | Não |
> | managedInstances / restorableDroppedDatabases / backupShortTermRetentionPolicies | Não |
> | managedInstances / vulnerabilityAssessments | Não |
> | servers | Sim |
> | servers/administrators | Não |
> | servidores/communicationLinks | Não |
> | servers/databases | Sim |
> | servidores/encryptionProtector | Não |
> | servers / firewallRules | Não |
> | servers/keys | Não |
> | servidores/restorableDroppedDatabases | Não |
> | servers/serviceobjectives | Não |
> | servidores/tdeCertificates | Não |
> | virtualClusters | Não |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | SqlVirtualMachineGroups | Sim |
> | SqlVirtualMachineGroups / AvailabilityGroupListeners | Não |
> | SqlVirtualMachines | Sim |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | deletedAccounts | Não |
> | storageAccounts | Sim |
> | storageAccounts / blobServices | Não |
> | storageAccounts / fileServices | Não |
> | storageAccounts/queueservices | Não |
> | storageAccounts/serviços | Não |
> | storageAccounts/serviços/metricDefinitions | Não |
> | storageAccounts/tabelaservices | Não |
> | usages | Não |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | caches | Sim |
> | caches/storageTargets | Não |
> | usageModels | Não |

## <a name="microsoftstoragereplication"></a>Microsoft. StorageReplication

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | replicationGroups | Não |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | storageSyncServices | Sim |
> | storageSyncServices / registeredServers | Não |
> | storageSyncServices / syncGroups | Não |
> | storageSyncServices / syncGroups / cloudEndpoints | Não |
> | storageSyncServices / syncGroups / serverEndpoints | Não |
> | storageSyncServices/fluxos de trabalho | Não |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | storageSyncServices | Sim |
> | storageSyncServices / registeredServers | Não |
> | storageSyncServices / syncGroups | Não |
> | storageSyncServices / syncGroups / cloudEndpoints | Não |
> | storageSyncServices / syncGroups / serverEndpoints | Não |
> | storageSyncServices/fluxos de trabalho | Não |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | storageSyncServices | Sim |
> | storageSyncServices / registeredServers | Não |
> | storageSyncServices / syncGroups | Não |
> | storageSyncServices / syncGroups / cloudEndpoints | Não |
> | storageSyncServices / syncGroups / serverEndpoints | Não |
> | storageSyncServices/fluxos de trabalho | Não |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | managers | Sim |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | clusters | Sim |
> | clusters/privateEndpoints | Não |
> | streamingjobs | Sim |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | acceptChangeTenant | Não |
> | aliases | Não |
> | cancel | Não |
> | changeTenantRequest | Não |
> | changeTenantStatus | Não |
> | CreateSubscription | Não |
> | enable | Não |
> | renomear | Não |
> | SubscriptionDefinitions | Não |
> | SubscriptionDefinitions | Não |
> | subscriptions | Não |

## <a name="microsoftsynapse"></a>Microsoft.Synapse

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | privateLinkHubs | Sim |
> | workspaces | Sim |
> | espaços de trabalho/bigDataPools | Sim |
> | espaços de trabalho/operationStatuses | Não |
> | espaços de trabalho/bancos de dados | Sim |
> | espaços de trabalho/sqlpools | Sim |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | environments | Sim |
> | environments / accessPolicies | Não |
> | environments/eventsources | Sim |
> | environments / referenceDataSets | Sim |

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | stores | Sim |
> | lojas/accessPolicies | Não |
> | stores/services | Não |
> | stores/services/tokens | Não |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | imageTemplates | Sim |
> | imageTemplates / runOutputs | Não |

## <a name="microsoftvmware"></a>Microsoft.VMware

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | ArcZones | Sim |
> | ResourcePools | Sim |
> | VCenters | Sim |
> | Máquinas Virtuais | Sim |
> | VirtualMachineTemplates | Sim |
> | VirtualNetworks | Sim |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | dedicatedCloudNodes | Sim |
> | dedicatedCloudServices | Sim |
> | virtualMachines | Sim |

## <a name="microsoftvnfmanager"></a>Microsoft.VnfManager

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | dispositivos | Sim |
> | registeredSubscriptions | Não |
> | selecionados | Não |
> | fornecedores/SKUs | Não |
> | fornecedores/vnfs | Não |
> | virtualNetworkFunctionSkus | Não |
> | vnfs | Sim |

## <a name="microsoftvsonline"></a>Microsoft.VSOnline

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | accounts | Sim |
> | planos | Sim |
> | registeredSubscriptions | Não |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | apiManagementAccounts | Não |
> | apiManagementAccounts/apiAcls | Não |
> | apiManagementAccounts/APIs | Não |
> | apiManagementAccounts/APIs/apiAcls | Não |
> | apiManagementAccounts/APIs/connectionAcls | Não |
> | apiManagementAccounts/APIs/conexões | Não |
> | apiManagementAccounts/APIs/conexões/connectionAcls | Não |
> | apiManagementAccounts/APIs/localizedDefinitions | Não |
> | apiManagementAccounts/connectionAcls | Não |
> | apiManagementAccounts/conexões | Não |
> | billingMeters | Não |
> | certificates | Sim |
> | connectionGateways | Sim |
> | connections | Sim |
> | customApis | Sim |
> | deletedSites | Não |
> | hostingEnvironments | Sim |
> | hostingEnvironments/eventGridFilters | Não |
> | hostingEnvironments/multiRolePools | Não |
> | hostingEnvironments/pools | Não |
> | kubeEnvironments | Sim |
> | publishingUsers | Não |
> | filmes | Não |
> | resourceHealthMetadata | Não |
> | runtimes | Não |
> | serverFarms | Sim |
> | serverFarms/eventGridFilters | Não |
> | serverFarms/firstPartyApps | Não |
> | serverFarms/firstPartyApps/keyVaultSettings | Não |
> | sites | Sim |
> | sites/configuração  | Não |
> | sites/eventGridFilters | Não |
> | sites/hostNameBindings | Não |
> | sites/networkConfig | Não |
> | sites/premieraddons | Sim |
> | sites/slots | Sim |
> | sites/Slots/eventGridFilters | Não |
> | sites/Slots/hostNameBindings | Não |
> | sites/Slots/networkConfig | Não |
> | sourceControls | Não |
> | staticSites | Sim |
> | validade | Não |
> | verifyHostingEnvironmentVnet | Não |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | diagnosticSettings | Não |
> | diagnosticSettingsCategories | Não |

## <a name="microsoftwindowsesu"></a>Microsoft.WindowsESU

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | multipleActivationKeys | Sim |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | DeviceServices | Sim |

## <a name="microsoftworkloadbuilder"></a>Microsoft. WorkloadBuilder

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | cargas de trabalho | Sim |
> | cargas de trabalho/instâncias | Não |
> | cargas de trabalho/versões | Não |
> | cargas de trabalho/versões/artefatos | Não |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | components | Não |
> | componentsSummary | Não |
> | monitorInstances | Não |
> | monitorInstancesSummary | Não |
> | monitors | Não |
> | notificationSettings | Não |

## <a name="next-steps"></a>Próximas etapas

Para obter os mesmos dados como um arquivo de valores separados por vírgula, baixe [complete-mode-deletion.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/complete-mode-deletion.csv).