---
title: Suporte à marca do Azure Resource Manager para recursos
description: Mostra quais tipos de recursos do Azure suportam tags. Fornece detalhes para todos os serviços do Azure.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 06/07/2019
ms.author: tomfitz
ms.openlocfilehash: 72bb11cd064c90c2bbe1e9e6452dcbf07fe37817
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68304864"
---
# <a name="tag-support-for-azure-resources"></a>Suporte a marcas para recursos do Azure
Este artigo descreve se um tipo de recurso dá suporte a [marcas](resource-group-using-tags.md). A coluna rotulada **dá suporte a marcas** indica se o tipo de recurso tem uma propriedade para a marca. A coluna rotulada como **marca no relatório de custo** indica se esse tipo de recurso passa a marca para o relatório de custo.

Para obter os mesmos dados como um arquivo de valores separados por vírgula, baixe [tag-support.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/tag-support.csv).

## <a name="microsoftaad"></a>Microsoft.AAD
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| DomainServices | Sim | Sim |
| DomainServices/oucontainer | Não | Não |

## <a name="microsoftaadiam"></a>microsoft.aadiam
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| diagnosticSettings | Não |  Não |
| diagnosticSettingsCategories | Não |  Não |

## <a name="microsoftaddons"></a>Microsoft.Addons
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| supportProviders | Não |  Não |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| aadsupportcases | Não |  Não |
| addsservices | Não |  Não |
| agentes | Não |  Não |
| anonymousapiusers | Não |  Não |
| configuração | Não |  Não |
| logs | Não |  Não |
| relatórios | Não |  Não |
| services | Não |  Não |

## <a name="microsoftadvisor"></a>Microsoft.Advisor
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| configurações | Não |  Não |
| generateRecommendations | Não |  Não |
| filmes | Não |  Não |
| suppressions | Não |  Não |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| actionRules | Não |  Não |
| alertas | Não |  Não |
| alertsList | Não |  Não |
| alertsSummary | Não |  Não |
| alertsSummaryList | Não |  Não |
| smartDetectorAlertRules | Não |  Não |
| smartDetectorRuntimeEnvironments | Não |  Não |
| smartGroups | Não |  Não |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| servers | Sim | Sim |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| reportFeedback | Não |  Não |
| serviço | Sim | Sim |
| validateServiceName | Não |  Não |

## <a name="microsoftattestation"></a>Microsoft.Attestation
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| attestationProviders | Não |  Não |

## <a name="microsoftauthorization"></a>Microsoft.Authorization
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| classicAdministrators | Não |  Não |
| denyAssignments | Não |  Não |
| elevateAccess | Não |  Não |
| locks | Não |  Não |
| permissões | Não |  Não |
| policyAssignments | Não |  Não |
| policyDefinitions | Não |  Não |
| policySetDefinitions | Não |  Não |
| providerOperations | Não |  Não |
| roleAssignments | Não |  Não |
| roleDefinitions | Não |  Não |

## <a name="microsoftautomation"></a>Microsoft.Automation
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| automationaccounts | Sim | Sim |
| automationAccounts/configurations | Sim | Sim |
| automationAccounts/runbooks | Não |  Não |
| automationAccounts/runbooks | Sim | Sim |
| automationAccounts/configurations | Não | Não |
| automationAccounts/runbooks | Não |  Não |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| environments | Não |  Não |
| environments/accounts | Não |  Não |
| environments/accounts/namespaces | Não |  Não |
| environments/accounts/namespaces/configurations | Não |  Não |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| b2cDirectories | Sim | Não |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| registrations | Sim | Sim |
| registrations/customerSubscriptions | Não |  Não |
| registrations/products | Não |  Não |

## <a name="microsoftbatch"></a>Microsoft.Batch
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| batchAccounts | Sim | Sim |

## <a name="microsoftbilling"></a>Microsoft.Billing
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| billingAccounts | Não |  Não |
| billingAccounts/billingProfiles | Não |  Não |
| billingAccounts/billingProfiles/billingSubscriptions | Não |  Não |
| billingAccounts/billingProfiles/invoices | Não |  Não |
| billingAccounts/billingProfiles/invoices/pricesheet | Não |  Não |
| billingAccounts/billingProfiles/operationStatus | Não |  Não |
| billingAccounts/billingProfiles/paymentMethods | Não |  Não |
| billingAccounts/billingProfiles/policies | Não |  Não |
| billingAccounts/billingProfiles/pricesheet | Não |  Não |
| billingAccounts/billingProfiles/products | Não |  Não |
| billingAccounts/billingProfiles/transactions | Não |  Não |
| billingAccounts/billingSubscriptions | Não |  Não |
| billingAccounts/departments | Não |  Não |
| billingAccounts/eligibleOffers | Não |  Não |
| billingAccounts/enrollmentAccounts | Não |  Não |
| billingAccounts/invoices | Não |  Não |
| billingAccounts/invoiceSections | Não |  Não |
| billingAccounts/invoiceSections/billingSubscriptions | Não |  Não |
| billingAccounts/invoiceSections/billingSubscriptions/transfer | Não |  Não |
| billingAccounts/invoiceSections/importRequests | Não |  Não |
| billingAccounts/invoiceSections/initiateImportRequest | Não |  Não |
| billingAccounts/invoiceSections/initiateTransfer | Não |  Não |
| billingAccounts/invoiceSections/operationStatus | Não |  Não |
| billingAccounts/invoiceSections/products | Não |  Não |
| billingAccounts/invoiceSections/transfers | Não |  Não |
| billingAccounts/products | Não |  Não |
| billingAccounts/projects | Não |  Não |
| billingAccounts/projects/billingSubscriptions | Não |  Não |
| billingAccounts/projects/importRequests | Não |  Não |
| billingAccounts/projects/initiateImportRequest | Não |  Não |
| billingAccounts/projects/operationStatus | Não |  Não |
| billingAccounts/projects/products | Não |  Não |
| billingAccounts/transactions | Não |  Não |
| billingPeriods | Não |  Não |
| BillingPermissions | Não |  Não |
| billingProperty | Não |  Não |
| BillingRoleAssignments | Não |  Não |
| BillingRoleDefinitions | Não |  Não |
| CreateBillingRoleAssignment | Não |  Não |
| departments | Não |  Não |
| enrollmentAccounts | Não |  Não |
| importRequests | Não |  Não |
| importRequests/acceptImportRequest | Não |  Não |
| importRequests/declineImportRequest | Não |  Não |
| invoices | Não |  Não |
| transfers | Não |  Não |
| transfers/acceptTransfer | Não |  Não |
| transfers/declineTransfer | Não |  Não |
| transfers/operationStatus | Não |  Não |
| usagePlans | Não |  Não |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| mapApis | Sim | Sim |
| updateCommunicationPreference | Não |  Não |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| BizTalk | Sim | Sim |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| blueprintAssignments | Não |  Não |
| blueprintAssignments/assignmentOperations | Não |  Não |
| blueprintAssignments/operations | Não |  Não |
| blueprints | Não |  Não |
| blueprints/artifacts | Não |  Não |
| blueprints/versions | Não |  Não |
| blueprints/versions/artifacts | Não |  Não |

## <a name="microsoftbotservice"></a>Microsoft.BotService
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| botServices | Sim | Sim |
| botServices/channels | Não |  Não |
| botServices/connections | Não |  Não |

## <a name="microsoftcache"></a>Microsoft.Cache
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| Redis | Sim | Sim |
| RedisConfigDefinition | Não |  Não |

## <a name="microsoftcapacity"></a>Microsoft.Capacity
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| appliedReservations | Não |  Não |
| calculatePrice | Não |  Não |
| catalogs | Não |  Não |
| commercialReservationOrders | Não |  Não |
| reservationOrders | Não |  Não |
| reservationOrders/calculateRefund | Não |  Não |
| reservationOrders/merge | Não |  Não |
| reservationOrders/reservations | Não |  Não |
| reservationOrders/reservations/revisions | Não |  Não |
| reservationOrders/return | Não |  Não |
| reservationOrders/split | Não |  Não |
| reservationOrders/swap | Não |  Não |
| reservations | Não |  Não |
| recursos | Não |  Não |
| validateReservationOrder | Não |  Não |

## <a name="microsoftcdn"></a>Microsoft.Cdn
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| edgenodes | Não |  Não |
| profiles | Sim | Sim |
| profiles/endpoints | Sim | Sim |
| profiles/endpoints/origins | Não |  Não |
| profiles/endpoints/origins | Não |  Não |
| validateProbe | Não |  Não |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| certificateOrders | Sim | Sim |
| certificateOrders/certificates | Não |  Não |
| validateCertificateRegistrationInformation | Não |  Não |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| recursos | Não |  Não |
| domainNames | Não |  Não |
| domainNames/capabilities | Não |  Não |
| domainNames/internalLoadBalancers | Não |  Não |
| domainNames/serviceCertificates | Não |  Não |
| domainNames/slots | Não |  Não |
| domainNames/slots | Não |  Não |
| moveSubscriptionResources | Não |  Não |
| operatingSystemFamilies | Não |  Não |
| operatingSystems | Não |  Não |
| quotas | Não |  Não |
| resourceTypes | Não |  Não |
| validateSubscriptionMoveAvailability | Não |  Não |
| virtualMachines | Não |  Não |
| virtualMachines/diagnosticSettings | Não |  Não |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| Migração de infraestrutura clássica | Não |  Não |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| recursos | Não |  Não |
| expressRouteCrossConnections | Não |  Não |
| expressRouteCrossConnections/peerings | Não |  Não |
| gatewaySupportedDevices | Não |  Não |
| networkSecurityGroups | Não |  Não |
| quotas | Não |  Não |
| reservedIps | Não |  Não |
| virtualNetworks | Não |  Não |
| virtualNetworks/virtualNetworkPeerings | Não |  Não |
| virtualNetworks/virtualNetworkPeerings | Não |  Não |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| recursos | Não |  Não |
| disks | Não |  Não |
| images | Não |  Não |
| osImages | Não |  Não |
| osPlatformImages | Não |  Não |
| publicImages | Não |  Não |
| quotas | Não |  Não |
| storageAccounts | Não |  Não |
| storageAccounts/services | Não |  Não |
| storageAccounts/services/diagnosticSettings | Não |  Não |
| storageAccounts/vmImages | Não |  Não |
| vmImages | Não |  Não |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| accounts | Sim | Sim |

## <a name="microsoftcommerce"></a>Microsoft.Commerce
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| RateCard | Não |  Não |
| UsageAggregates | Não |  Não |

## <a name="microsoftcompute"></a>Microsoft.Compute
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| availabilitySets | Sim | Sim |
| disks | Sim | Sim |
| images | Sim | Sim |
| restorePointCollections | Sim | Sim |
| restorePointCollections/restorePoints | Não |  Não |
| sharedVMImages | Sim | Sim |
| sharedVMImages/versions | Sim | Sim |
| snapshots | Sim | Sim |
| virtualMachines | Sim | Sim |
| virtualMachines/diagnosticSettings | Não |  Não |
| virtualMachines/extensions | Sim | Sim |
| virtualMachineScaleSets | Sim | Sim |
| virtualMachines/extensions | Não |  Não |
| virtualMachineScaleSets/networkInterfaces | Não |  Não |
| virtualMachineScaleSets/publicIPAddresses | Não |  Não |
| virtualMachineScaleSets/virtualMachines | Não |  Não |
| virtualMachineScaleSets/networkInterfaces | Não |  Não |

## <a name="microsoftconsumption"></a>Microsoft.Consumption
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| AggregatedCost | Não |  Não |
| Saldos | Não |  Não |
| Orçamentos | Não |  Não |
| Charges | Não |  Não |
| CostTags | Não |  Não |
| credits | Não |  Não |
| events | Não |  Não |
| Previsões | Não |  Não |
| lots | Não |  Não |
| Marketplaces | Não |  Não |
| Pricesheets | Não |  Não |
| products | Não |  Não |
| ReservationDetails | Não |  Não |
| ReservationRecommendations | Não |  Não |
| ReservationSummaries | Não |  Não |
| ReservationTransactions | Não |  Não |
| Marcas | Não |  Não |
| Termos | Não |  Não |
| UsageDetails | Não |  Não |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| containerGroups | Sim | Sim |
| serviceAssociationLinks | Não |  Não |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| registries | Sim | Sim |
| registries/builds | Não |  Não |
| registries/builds/cancel | Não |  Não |
| registries/builds/getLogLink | Não |  Não |
| registries/buildTasks | Sim | Sim |
| registries/buildTasks/steps | Não |  Não |
| registries/eventGridFilters | Não |  Não |
| registries/getBuildSourceUploadUrl | Não |  Não |
| registries/GetCredentials | Não |  Não |
| registries/importImage | Não |  Não |
| registries/queueBuild | Não |  Não |
| registries/regenerateCredential | Não |  Não |
| registries/regenerateCredentials | Não |  Não |
| registries/replications | Sim | Sim |
| registries/runs | Não |  Não |
| registries/runs/cancel | Não |  Não |
| registries/scheduleRun | Não |  Não |
| registries/tasks | Sim | Sim |
| registries/updatePolicies | Não |  Não |
| registries/webhooks | Sim | Sim |
| registries/webhooks/getCallbackConfig | Não |  Não |
| registries/webhooks/ping | Não |  Não |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| containerServices | Sim | Sim |
| managedClusters | Sim | Sim |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| de dimensionamento da Web | Sim | Sim |
| updateCommunicationPreference | Não |  Não |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| accounts | Sim | Sim |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| Alertas | Não |  Não |
| BillingAccounts | Não |  Não |
| Conectores | Sim | Sim |
| Departments | Não |  Não |
| Dimensões | Não |  Não |
| EnrollmentAccounts | Não |  Não |
| Consultar | Não |  Não |
| register | Não |  Não |
| Reportconfigs | Não |  Não |
| Relatórios | Não |  Não |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| hubs | Sim | Sim |
| hubs/authorizationPolicies | Não |  Não |
| hubs/connectors | Não |  Não |
| hubs/connectors/mappings | Não |  Não |
| hubs/interactions | Não |  Não |
| hubs/kpi | Não |  Não |
| hubs/links | Não |  Não |
| hubs/profiles | Não |  Não |
| hubs/roleAssignments | Não |  Não |
| hubs/roles | Não |  Não |
| hubs/suggestTypeSchema | Não |  Não |
| hubs/views | Não |  Não |
| hubs/widgetTypes | Não |  Não |

## <a name="microsoftdatabox"></a>Microsoft.DataBox
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| jobs | Sim | Sim |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| DataBoxEdgeDevices | Sim | Sim |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| workspaces | Sim | Não |
| workspaces/virtualNetworkPeerings | Não |  Não |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| catalogs | Sim | Sim |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| connectionManagers | Sim | Sim |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| dataFactories | Sim | Não |
| dataFactories/diagnosticSettings | Não |  Não |
| dataFactorySchema | Não |  Não |
| factories | Sim | Não |
| factories/integrationRuntimes | Não |  Não |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| accounts | Sim | Sim |
| accounts/dataLakeStoreAccounts | Não |  Não |
| accounts/storageAccounts | Não |  Não |
| accounts/storageAccounts/containers | Não |  Não |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| accounts | Sim | Sim |
| accounts/eventGridFilters | Não |  Não |
| accounts/firewallRules | Não |  Não |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| services | Sim | Sim |
| services/projects | Sim | Sim |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| servers | Sim | Sim |
| servers/recoverableServers | Não |  Não |
| servers/virtualNetworkRules | Não |  Não |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| servers | Sim | Sim |
| servers/recoverableServers | Não |  Não |
| servers/virtualNetworkRules | Não |  Não |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| servers | Sim | Sim |
| servers/advisors | Não |  Não |
| servers/queryTexts | Não |  Não |
| servers/recoverableServers | Não |  Não |
| servers/topQueryStatistics | Não |  Não |
| servers/virtualNetworkRules | Não |  Não |
| servers/waitStatistics | Não |  Não |

## <a name="microsoftdevices"></a>Microsoft.Devices
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| IotHubs | Sim | Sim |
| IotHubs/eventGridFilters | Não |  Não |
| ProvisioningServices | Sim | Sim |
| usages | Não |  Não |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| controladores | Sim | Sim |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| labs | Sim | Sim |
| labs/serviceRunners | Sim | Sim |
| labs/virtualMachines | Sim | Sim |
| schedules | Sim | Sim |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| databaseAccounts | Não |  Não |
| databaseAccounts | Sim | Sim |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| domains | Sim | Sim |
| domains/domainOwnershipIdentifiers | Não |  Não |
| generateSsoRequest | Não |  Não |
| topLevelDomains | Não |  Não |
| validateDomainRegistrationInformation | Não |  Não |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| lcsprojects | Não |  Não |
| lcsprojects/clouddeployments | Não |  Não |
| lcsprojects/connectors | Não |  Não |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| domains | Sim | Não |
| domains/topics | Não |  Não |
| eventSubscriptions | Não |  Não |
| extensionTopics | Não |  Não |
| topics | Sim | Não |
| topicTypes | Não |  Não |

## <a name="microsofteventhub"></a>Microsoft.EventHub
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| clusters | Sim | Não |
| namespaces | Sim | Não |
| namespaces/authorizationrules | Não |  Não |
| namespaces/disasterrecoveryconfigs | Não |  Não |
| namespaces/eventhubs | Não |  Não |
| namespaces/eventhubs/authorizationrules | Não |  Não |
| namespaces/eventhubs/consumergroups | Não |  Não |

## <a name="microsoftfeatures"></a>Microsoft.Features
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| recursos | Não |  Não |
| providers | Não |  Não |

## <a name="microsoftgallery"></a>Microsoft.Gallery
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| enroll | Não |  Não |
| galleryitems | Não |  Não |
| generateartifactaccessuri | Não |  Não |
| myareas | Não |  Não |
| myareas/areas | Não |  Não |
| myareas/areas/areas | Não |  Não |
| myareas/areas/areas/galleryitems | Não |  Não |
| myareas/areas/galleryitems | Não |  Não |
| myareas/galleryitems | Não |  Não |
| register | Não |  Não |
| recursos | Não |  Não |
| retrieveresourcesbyid | Não |  Não |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| guestConfigurationAssignments | Não |  Não |
| software | Não |  Não |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| hanaInstances | Sim |  Sim |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| clusters | Sim | Sim |
| clusters/applications | Não |  Não |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| jobs | Sim | Sim |

## <a name="microsoftinformationprotection"></a>Microsoft.InformationProtection
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| labelGroups | Não |  Não |
| labelGroups/labels | Não |  Não |
| labelGroups/labels/conditions | Não |  Não |
| labelGroups/labels/subLabels | Não |  Não |
| labelGroups/labels/subLabels/conditions | Não |  Não |

## <a name="microsoftinsights"></a>microsoft.insights
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| actiongroups | Sim | Sim |
| activityLogAlerts | Sim | Sim |
| alertrules | Sim | Sim |
| automatedExportSettings | Não |  Não |
| autoscalesettings | Sim | Sim |
| baseline | Não |  Não |
| calculatebaseline | Não |  Não |
| components | Sim | Sim |
| components/metrics | Não |  Não |
| components/pricingPlans | Não |  Não |
| components/query | Não |  Não |
| diagnosticSettings | Não |  Não |
| diagnosticSettingsCategories | Não |  Não |
| eventCategories | Não |  Não |
| eventtypes | Não |  Não |
| extendedDiagnosticSettings | Não |  Não |
| logDefinitions | Não |  Não |
| logprofiles | Não |  Não |
| logs | Não |  Não |
| metricAlerts | Sim | Sim |
| migrateToNewPricingModel | Não |  Não |
| myWorkbooks | Não |  Não |
| consultas | Não |  Não |
| rollbackToLegacyPricingModel | Não |  Não |
| scheduledqueryrules | Sim | Sim |
| vmInsightsOnboardingStatuses | Não |  Não |
| webtests | Sim | Sim |
| workbooks | Sim | Sim |

## <a name="microsoftintune"></a>Microsoft.Intune
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| diagnosticSettings | Não |  Não |
| diagnosticSettingsCategories | Não |  Não |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| IoTApps | Sim | Sim |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| Grafo | Sim | Sim |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| deletedVaults | Não |  Não |
| vaults | Sim | Sim |
| vaults/accessPolicies | Não |  Não |
| vaults/secrets | Não |  Não |

## <a name="microsoftkusto"></a>Microsoft.Kusto
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| clusters | Sim | Sim |
| clusters/databases | Não |  Não |
| clusters/databases/dataconnections | Não |  Não |
| clusters/databases/eventhubconnections | Não |  Não |

## <a name="microsoftlabservices"></a>Microsoft.LabServices
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| labaccounts | Sim | Sim |
| users | Não |  Não |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| accounts | Sim | Sim |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| accounts | Sim | Sim |

## <a name="microsoftloganalytics"></a>Microsoft.LogAnalytics
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| logs | Não |  Não |

## <a name="microsoftlogic"></a>Microsoft.Logic
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| integrationAccounts | Sim | Sim |
| workflows | Sim | Sim |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| commitmentPlans | Sim | Sim |
| webServices | Sim | Sim |
| Workspaces | Sim | Sim |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| accounts | Sim | Sim |
| accounts/workspaces | Sim | Sim |
| accounts/workspaces/projects | Sim | Sim |
| teamAccounts | Sim | Sim |
| teamAccounts/workspaces | Sim | Sim |
| teamAccounts/workspaces/projects | Sim | Sim |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| accounts | Sim | Sim |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| workspaces | Sim | Sim |
| workspaces/computes | Não |  Não |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| Identidades | Não |  Não |
| userAssignedIdentities | Sim | 

## <a name="microsoftmanagement"></a>Microsoft.Management
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| getEntities | Não |  Não |
| managementGroups | Não |  Não |
| recursos | Não |  Não |
| startTenantBackfill | Não |  Não |
| tenantBackfillStatus | Não |  Não |

## <a name="microsoftmaps"></a>Microsoft.Maps
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| accounts | Sim | Sim |
| accounts/eventGridFilters | Não |  Não |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| offers | Não |  Não |
| offerTypes | Não |  Não |
| offerTypes/publishers | Não |  Não |
| offerTypes/publishers/offers | Não |  Não |
| offerTypes/publishers/offers/plans | Não |  Não |
| offerTypes/publishers/offers/plans/agreements | Não |  Não |
| offerTypes/publishers/offers/plans/configs | Não |  Não |
| offerTypes/publishers/offers/plans/configs/importImage | Não |  Não |
| privategalleryitems | Não |  Não |
| products | Não |  Não |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| classicDevServices | Sim | Sim |
| updateCommunicationPreference | Não |  Não |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| Contratos | Não |  Não |
| offertypes | Não |  Não |

## <a name="microsoftmedia"></a>Microsoft.Media
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| mediaservices | Sim | Sim |
| mediaservices/accountFilters | Não |  Não |
| mediaservices/assets | Não |  Não |
| mediaservices/assets/assetFilters | Não |  Não |
| mediaservices/contentKeyPolicies | Não |  Não |
| mediaservices/eventGridFilters | Não |  Não |
| mediaservices/liveEvents | Não |  Não |
| mediaservices/liveEvents | Sim | Sim |
| mediaservices/liveEvents/liveOutputs | Não |  Não |
| mediaservices/liveEvents | Não |  Não |
| mediaservices/streamingEndpoints | Não |  Não |
| mediaservices/streamingEndpoints | Sim | Sim |
| mediaservices/streamingLocators | Não |  Não |
| mediaservices/streamingLocators | Não |  Não |
| mediaservices/transforms | Não |  Não |
| mediaservices/transforms | Não |  Não |

## <a name="microsoftmigrate"></a>Microsoft.Migrate
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| projects | Sim | Sim |

## <a name="microsoftnetwork"></a>Microsoft.Network
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| applicationGateways | Sim | Não |
| applicationSecurityGroups | Sim | Sim |
| azureFirewallFqdnTags | Não |  Não |
| azureFirewalls | Sim | Não |
| bgpServiceCommunities | Não |  Não |
| connections | Sim | Sim |
| ddosCustomPolicies | Sim | Sim |
| ddosProtectionPlans | Sim | Sim |
| dnsOperationStatuses | Não |  Não |
| dnszones | Sim | Sim |
| dnszones/A | Não |  Não |
| dnszones/AAAA | Não |  Não |
| dnszones/all | Não |  Não |
| dnszones/CAA | Não |  Não |
| dnszones/CNAME | Não |  Não |
| dnszones/MX | Não |  Não |
| dnszones/NS | Não |  Não |
| dnszones/PTR | Não |  Não |
| dnszones/recordsets | Não |  Não |
| dnszones/SOA | Não |  Não |
| dnszones/SRV | Não |  Não |
| dnszones/TXT | Não |  Não |
| expressRouteCircuits | Sim  | Não |
| expressRouteServiceProviders | Não |  Não |
| frontdoors | Sim, mas limitado (veja a [Observação abaixo](#frontdoor)) | Sim |
| frontdoorWebApplicationFirewallPolicies | Sim, mas limitado (veja a [Observação abaixo](#frontdoor)) | Sim |
| getDnsResourceReference | Não |  Não |
| interfaceEndpoints | Sim | Sim |
| internalNotify | Não |  Não |
| loadBalancers | Sim | Não |
| virtualNetworkGateways | Sim | Sim |
| natGateways | Sim | Sim |
| networkIntentPolicies | Sim | Sim |
| networkInterfaces | Sim | Sim |
| networkProfiles | Sim | Sim |
| networkSecurityGroups | Sim | Sim |
| networkWatchers | Sim | Não |
| networkWatchers/connectionMonitors | Sim | Não |
| networkWatchers/lenses | Sim | Não |
| networkWatchers/lenses | Sim | Não |
| privateLinkServices | Sim | Sim |
| publicIPAddresses | Sim | Sim |
| publicIPPrefixes | Sim | Sim |
| routeFilters | Sim | Sim |
| routeTables | Sim | Sim |
| serviceEndpointPolicies | Sim | Sim |
| trafficManagerGeographicHierarchies | Não |  Não |
| trafficmanagerprofiles | Sim | Sim |
| trafficmanagerprofiles/heatMaps | Não |  Não |
| virtualWans | Sim | Sim |
| virtualNetworkGateways | Sim | Sim |
| virtualNetworks | Sim | Sim |
| virtualNetworks/subnets | Não |  Não |
| virtualNetworkTaps | Sim | Sim |
| virtualWans | Sim | Sim |
| vpnGateways | Sim | Não |
| vpnSites | Sim | Sim |
| frontdoorWebApplicationFirewallPolicies | Sim | Sim |

<a id="frontdoor" />

Para o serviço de porta frontal do Azure, você pode aplicar marcas ao criar o recurso, mas a atualização ou adição de marcas não tem suporte no momento.

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| namespaces | Sim | Não |
| namespaces/notificationHubs | Sim | Não |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| dispositivos | Não |  Não |
| linkTargets | Não |  Não |
| storageInsightConfigs | Não |  Não |
| workspaces | Sim | Sim |
| workspaces/dataSources | Não |  Não |
| workspaces/linkedServices | Não |  Não |
| workspaces/query | Não |  Não |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| managementassociations | Não |  Não |
| managementconfigurations | Sim | Sim |
| solutions | Sim | Sim |
| Modos de exibição | Sim | Sim |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| policyEvents | Não |  Não |
| policyStates | Não |  Não |
| policyTrackedResources | Não |  Não |
| remediations | Não |  Não |

## <a name="microsoftportal"></a>Microsoft.Portal
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| consoles | Não |  Não |
| dashboards | Sim | Sim |
| userSettings | Não |  Não |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| workspaceCollections | Sim | Sim |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| capacities | Sim | Sim |

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| accounts | Sim | Sim |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| backupProtectedItems | Não |  Não |
| vaults | Sim | Sim |

## <a name="microsoftrelay"></a>Microsoft.Relay
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| namespaces | Sim | Sim |
| namespaces/authorizationrules | Não |  Não |
| namespaces/hybridconnections | Não |  Não |
| namespaces/hybridconnections/authorizationrules | Não |  Não |
| namespaces/wcfrelays | Não |  Não |
| namespaces/wcfrelays/authorizationrules | Não |  Não |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| recursos | Não |  Não |
| subscriptionsStatus | Não |  Não |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| availabilityStatuses | Não |  Não |
| childAvailabilityStatuses | Não |  Não |
| childResources | Não |  Não |
| events | Não |  Não |
| impactedResources | Não |  Não |
| Notificações | Não |  Não |

## <a name="microsoftresources"></a>Microsoft.Resources
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| deployments | Não |  Não |
| deployments/operations | Não |  Não |
| links | Não |  Não |
| notifyResourceJobs | Não |  Não |
| providers | Não |  Não |
| resourceGroups | Não |  Não |
| recursos | Não |  Não |
| subscriptions | Não |  Não |
| subscriptions/providers | Não |  Não |
| subscriptions/resourceGroups | Não |  Não |
| subscriptions/resourcegroups/resources | Não |  Não |
| subscriptions/resources | Não |  Não |
| subscriptions/tagnames | Não |  Não |
| subscriptions/tagNames/tagValues | Não |  Não |
| tenants | Não |  Não |

## <a name="microsoftsaas"></a>Microsoft.SaaS
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| de dimensionamento da Web | Sim | Sim |
| saasresources | Não |  Não |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| flows | Sim | Sim |
| jobcollections | Sim | Sim |

## <a name="microsoftsearch"></a>Microsoft.Search
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| resourceHealthMetadata | Não |  Não |
| searchServices | Sim | Sim |

## <a name="microsoftsecurity"></a>Microsoft.Security
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| advancedThreatProtectionSettings | Não |  Não |
| alertas | Não |  Não |
| allowedConnections | Não |  Não |
| appliances | Não |  Não |
| applicationWhitelistings | Não |  Não |
| AutoProvisioningSettings | Não |  Não |
| Compliances | Não |  Não |
| dataCollectionAgents | Não |  Não |
| discoveredSecuritySolutions | Não |  Não |
| externalSecuritySolutions | Não |  Não |
| InformationProtectionPolicies | Não |  Não |
| jitNetworkAccessPolicies | Não |  Não |
| monitoring | Não |  Não |
| monitoring/antimalware | Não |  Não |
| monitoring/baseline | Não |  Não |
| monitoring/patch | Não |  Não |
| políticas | Não |  Não |
| pricings | Não |  Não |
| securityContacts | Não |  Não |
| securitySolutions | Não |  Não |
| securitySolutionsReferenceData | Não |  Não |
| securityStatus | Não |  Não |
| securityStatus/endpoints | Não |  Não |
| securityStatus/subnets | Não |  Não |
| securityStatus/virtualMachines | Não |  Não |
| securityStatuses | Não |  Não |
| securityStatusesSummaries | Não |  Não |
| configurações | Não |  Não |
| tarefas | Não |  Não |
| topologies | Não |  Não |
| workspaceSettings | Não |  Não |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| diagnosticSettings | Não |  Não |
| diagnosticSettingsCategories | Não |  Não |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| namespaces | Sim | Não |
| namespaces/authorizationrules | Não |  Não |
| namespaces/disasterrecoveryconfigs | Não |  Não |
| namespaces/eventgridfilters | Não |  Não |
| namespaces/queues | Não |  Não |
| namespaces/queues/authorizationrules | Não |  Não |
| namespaces/topics | Não |  Não |
| namespaces/topics/authorizationrules | Não |  Não |
| namespaces/topics/subscriptions | Não |  Não |
| namespaces/topics/subscriptions/rules | Não |  Não |
| premiumMessagingRegions | Não |  Não |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| clusters | Sim | Sim |
| clusters/applications | Não |  Não |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| de dimensionamento da Web | Sim | Sim |
| gateways | Sim | Sim |
| networks | Sim | Sim |
| segredos | Sim | Sim |
| volumes | Sim | Sim |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| SignalR | Sim | Sim |

## <a name="microsoftsolutions"></a>Microsoft.Solutions
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| applianceDefinitions | Sim | Sim |
| appliances | Sim | Sim |
| applicationDefinitions | Sim | Sim |
| de dimensionamento da Web | Sim | Sim |
| jitRequests | Sim | Sim |

## <a name="microsoftsql"></a>Microsoft.SQL
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| managedInstances | Sim | Sim |
| managedInstances/databases | Sim (veja a observação a seguir) | Sim |
| managedInstances/databases/backupShortTermRetentionPolicies | Não | Não |
| managedInstances/databases/schemas/tables/columns/sensitivityLabels | Não | Não |
| managedInstances/databases/vulnerabilityAssessments | Não | Não |
| managedInstances/databases/vulnerabilityAssessments/rules/baselines | Não | Não |
| managedInstances/encryptionProtector | Não | Não |
| managedInstances/keys | Não | Não |
| managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies | Não | Não |
| managedInstances/vulnerabilityAssessments | Não | Não |
| servers | Sim | Sim |
| servers/administrators | Não |  Não |
| servers/communicationLinks | Não |  Não |
| servers/databases | Sim (veja a observação a seguir) | Sim |
| servers/encryptionProtector | Não |  Não |
| servers/firewallRules | Não |  Não |
| servers/keys | Não |  Não |
| servers/restorableDroppedDatabases | Não |  Não |
| servers/serviceobjectives | Não |  Não |
| servers/tdeCertificates | Não |  Não |

> [!NOTE]
> O banco de dados Mestre não oferece suporte a marcas, mas outros bancos de dados, incluindo os bancos de dados do SQL Data Warehouse do Azure, sim. Os bancos de dados do SQL Data Warehouse do Azure devem estar no estado Ativo (não Pausado).


## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| SqlVirtualMachineGroups | Sim | Sim |
| SqlVirtualMachineGroups/AvailabilityGroupListeners | Não |  Não |
| SqlVirtualMachines | Sim | Sim |

## <a name="microsoftstorage"></a>Microsoft.Storage
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| storageAccounts | Sim | Sim |
| storageAccounts/blobServices | Não |  Não |
| storageAccounts/blobServices | Não |  Não |
| storageAccounts/blobServices | Não |  Não |
| storageAccounts/services | Não |  Não |
| storageAccounts/blobServices | Não |  Não |
| usages | Não |  Não |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| storageSyncServices | Sim | Sim |
| storageSyncServices/registeredServers | Não |  Não |
| storageSyncServices/syncGroups | Não |  Não |
| storageSyncServices/syncGroups/cloudEndpoints | Não |  Não |
| storageSyncServices/syncGroups/cloudEndpoints | Não |  Não |
| storageSyncServices/workflows | Não |  Não |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| managers | Sim | Sim |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| streamingjobs | Sim (veja a observação a seguir) | Sim |
| streamingjobs/diagnosticSettings | Não |  Não |

> [!NOTE]
> Você não pode adicionar uma marca quando streamingjobs estiver em execução. Pare o recurso para adicionar uma marca.

## <a name="microsoftsubscription"></a>Microsoft.Subscription
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| CreateSubscription | Não |  Não |
| SubscriptionDefinitions | Não |  Não |
| SubscriptionDefinitions | Não |  Não |

## <a name="microsoftsupport"></a>microsoft.support
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| supporttickets | Não |  Não |

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| providerRegistrations | Sim | Sim |
| recursos | Sim | Sim |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| environments | Sim | Não |
| environments/accessPolicies | Não |  Não |
| environments/eventsources | Sim | Não |
| environments/referenceDataSets | Sim | Não |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| conta | Sim | Sim |
| account/extension | Sim | Sim |
| account/project | Sim | Sim |

## <a name="microsoftweb"></a>Microsoft.Web
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| apiManagementAccounts | Não |  Não |
| apiManagementAccounts/apis/connections/connectionAcls | Não |  Não |
| apiManagementAccounts/apis | Não |  Não |
| apiManagementAccounts/apis/connections/connectionAcls | Não |  Não |
| apiManagementAccounts/apis/connections/connectionAcls | Não |  Não |
| apiManagementAccounts/apis/connections/connectionAcls | Não |  Não |
| apiManagementAccounts/apis/connections/connectionAcls | Não |  Não |
| apiManagementAccounts/apis/localizedDefinitions | Não |  Não |
| apiManagementAccounts/connectionAcls | Não |  Não |
| apiManagementAccounts/connections | Não |  Não |
| billingMeters | Não |  Não |
| certificates | Sim | Sim |
| connectionGateways | Sim | Sim |
| connections | Sim | Sim |
| customApis | Sim | Sim |
| deletedSites | Não |  Não |
| funções | Não |  Não |
| hostingEnvironments | Sim | Sim |
| hostingEnvironments/multiRolePools | Não |  Não |
| hostingEnvironments/multiRolePools/instances | Não |  Não |
| hostingEnvironments/multiRolePools | Não |  Não |
| hostingEnvironments/workerPools/instances | Não |  Não |
| publishingUsers | Não |  Não |
| filmes | Não |  Não |
| resourceHealthMetadata | Não |  Não |
| runtimes | Não |  Não |
| serverFarms | Sim | Sim |
| serverFarms/workers | Não |  Não |
| sites | Sim | Sim |
| sites/domainOwnershipIdentifiers | Não |  Não |
| sites/hostNameBindings | Não |  Não |
| sites/instances | Não |  Não |
| sites/instances/extensions | Não |  Não |
| sites/premieraddons | Sim | Sim |
| sites/recommendations | Não |  Não |
| sites/resourceHealthMetadata | Não |  Não |
| sites/slots | Sim | Sim |
| sites/hostNameBindings | Não |  Não |
| sites/instances | Não |  Não |
| sites/instances/extensions | Não |  Não |
| sourceControls | Não |  Não |
| validade | Não |  Não |
| verifyHostingEnvironmentVnet | Não |  Não |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| diagnosticSettings | Não |  Não |
| diagnosticSettingsCategories | Não |  Não |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| DeviceServices | Sim | Sim |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor
| Tipo de recurso | Suporte de Tags | Marca no relatório de custo |
| ------------- | ----------- | ----------- |
| components | Não |  Não |
| componentsSummary | Não |  Não |
| monitorInstances | Não |  Não |
| monitorInstancesSummary | Não |  Não |
| monitors | Não |  Não |
| notificationSettings | Não |  Não |

## <a name="next-steps"></a>Próximas etapas
Para saber como aplicar tags a recursos, consulte [Use tags para organizar seus recursos do Azure](resource-group-using-tags.md).
