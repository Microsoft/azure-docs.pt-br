---
title: Exclusão de modo completo por tipo de recurso do Azure Resource Manager
description: Mostra como os tipos de recurso lidam com a exclusão de modo completo em modelos do Azure Resource Manager.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 04/24/2019
ms.author: tomfitz
ms.openlocfilehash: 5ac442c0ae1e397fd1e8b58fdbcf61eb8712046c
ms.sourcegitcommit: af58483a9c574a10edc546f2737939a93af87b73
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68302851"
---
# <a name="deletion-of-azure-resources-for-complete-mode-deployments"></a>Exclusão de recursos do Azure para implantações do modo completo
Este artigo descreve como os tipos de recurso lidam com a exclusão quando ela não ocorre em um modelo implantado no modo completo.

Os tipos de recurso marcados com `Yes` são excluídos quando o tipo não está no modelo implantado com o modo completo. 

Os tipos de recurso marcados com `No` não são excluídos automaticamente quando não estão no modelo; no entanto, são excluídos quando o recurso pai é excluído. Para obter uma descrição completa do comportamento, consulte [Modos de implantação do Azure Resource Manager](deployment-modes.md).


## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | DomainServices | Sim | 
> | DomainServices/oucontainer | Não | 

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | diagnosticSettings | Não | 
> | diagnosticSettingsCategories | Não | 

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
> | services | Não | 

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | configurações | Não | 
> | generateRecommendations | Não | 
> | filmes | Não | 
> | suppressions | Não | 

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | actionRules | Não | 
> | alertas | Não | 
> | alertsList | Não | 
> | alertsSummary | Não | 
> | alertsSummaryList | Não | 
> | smartDetectorAlertRules | Não | 
> | smartDetectorRuntimeEnvironments | Não | 
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

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | attestationProviders | Não | 

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | classicAdministrators | Não | 
> | denyAssignments | Não | 
> | elevateAccess | Não | 
> | locks | Não | 
> | permissões | Não | 
> | policyAssignments | Não | 
> | policyDefinitions | Não | 
> | policySetDefinitions | Não | 
> | providerOperations | Não | 
> | roleAssignments | Não | 
> | roleDefinitions | Não | 

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | automationaccounts | Sim | 
> | automationAccounts/configurations | Sim | 
> | automationAccounts/runbooks | Não | 
> | automationAccounts/runbooks | Sim | 
> | automationAccounts/configurations | Não | 
> | automationAccounts/runbooks | Não | 

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | environments | Não | 
> | environments/accounts | Não | 
> | environments/accounts/namespaces | Não | 
> | environments/accounts/namespaces/configurations | Não | 

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | b2cDirectories | Sim | 

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | registrations | Sim | 
> | registrations/customerSubscriptions | Não | 
> | registrations/products | Não | 

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | batchAccounts | Sim | 

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | billingAccounts | Não | 
> | billingAccounts/billingProfiles | Não | 
> | billingAccounts/billingProfiles/billingSubscriptions | Não | 
> | billingAccounts/billingProfiles/invoices | Não | 
> | billingAccounts/billingProfiles/invoices/pricesheet | Não | 
> | billingAccounts/billingProfiles/operationStatus | Não | 
> | billingAccounts/billingProfiles/paymentMethods | Não | 
> | billingAccounts/billingProfiles/policies | Não | 
> | billingAccounts/billingProfiles/pricesheet | Não | 
> | billingAccounts/billingProfiles/products | Não | 
> | billingAccounts/billingProfiles/transactions | Não | 
> | billingAccounts/billingSubscriptions | Não | 
> | billingAccounts/departments | Não | 
> | billingAccounts/eligibleOffers | Não | 
> | billingAccounts/enrollmentAccounts | Não | 
> | billingAccounts/invoices | Não | 
> | billingAccounts/invoiceSections | Não | 
> | billingAccounts/invoiceSections/billingSubscriptions | Não | 
> | billingAccounts/invoiceSections/billingSubscriptions/transfer | Não | 
> | billingAccounts/invoiceSections/importRequests | Não | 
> | billingAccounts/invoiceSections/initiateImportRequest | Não | 
> | billingAccounts/invoiceSections/initiateTransfer | Não | 
> | billingAccounts/invoiceSections/operationStatus | Não | 
> | billingAccounts/invoiceSections/products | Não | 
> | billingAccounts/invoiceSections/transfers | Não | 
> | billingAccounts/products | Não | 
> | billingAccounts/projects | Não | 
> | billingAccounts/projects/billingSubscriptions | Não | 
> | billingAccounts/projects/importRequests | Não | 
> | billingAccounts/projects/initiateImportRequest | Não | 
> | billingAccounts/projects/operationStatus | Não | 
> | billingAccounts/projects/products | Não | 
> | billingAccounts/transactions | Não | 
> | billingPeriods | Não | 
> | BillingPermissions | Não | 
> | billingProperty | Não | 
> | BillingRoleAssignments | Não | 
> | BillingRoleDefinitions | Não | 
> | CreateBillingRoleAssignment | Não | 
> | departments | Não | 
> | enrollmentAccounts | Não | 
> | importRequests | Não | 
> | importRequests/acceptImportRequest | Não | 
> | importRequests/declineImportRequest | Não | 
> | invoices | Não | 
> | transfers | Não | 
> | transfers/acceptTransfer | Não | 
> | transfers/declineTransfer | Não | 
> | transfers/operationStatus | Não | 
> | usagePlans | Não | 

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | mapApis | Sim | 
> | updateCommunicationPreference | Não | 

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | BizTalk | Sim | 

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | blueprintAssignments | Não | 
> | blueprintAssignments/assignmentOperations | Não | 
> | blueprintAssignments/operations | Não | 
> | blueprints | Não | 
> | blueprints/artifacts | Não | 
> | blueprints/versions | Não | 
> | blueprints/versions/artifacts | Não | 

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | botServices | Sim | 
> | botServices/channels | Não | 
> | botServices/connections | Não | 

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | Redis | Sim | 
> | RedisConfigDefinition | Não | 

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | appliedReservations | Não | 
> | calculatePrice | Não | 
> | catalogs | Não | 
> | commercialReservationOrders | Não | 
> | reservationOrders | Não | 
> | reservationOrders/calculateRefund | Não | 
> | reservationOrders/merge | Não | 
> | reservationOrders/reservations | Não | 
> | reservationOrders/reservations/revisions | Não | 
> | reservationOrders/return | Não | 
> | reservationOrders/split | Não | 
> | reservationOrders/swap | Não | 
> | reservations | Não | 
> | recursos | Não | 
> | validateReservationOrder | Não | 

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | edgenodes | Não | 
> | profiles | Sim | 
> | profiles/endpoints | Sim | 
> | profiles/endpoints/origins | Não | 
> | profiles/endpoints/origins | Não | 
> | validateProbe | Não | 

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | certificateOrders | Sim | 
> | certificateOrders/certificates | Não | 
> | validateCertificateRegistrationInformation | Não | 

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | recursos | Não | 
> | domainNames | Não | 
> | domainNames/capabilities | Não | 
> | domainNames/internalLoadBalancers | Não | 
> | domainNames/serviceCertificates | Não | 
> | domainNames/slots | Não | 
> | domainNames/slots | Não | 
> | moveSubscriptionResources | Não | 
> | operatingSystemFamilies | Não | 
> | operatingSystems | Não | 
> | quotas | Não | 
> | resourceTypes | Não | 
> | validateSubscriptionMoveAvailability | Não | 
> | virtualMachines | Não | 
> | virtualMachines/diagnosticSettings | Não | 

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | Migração de infraestrutura clássica | Não | 

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | recursos | Não | 
> | expressRouteCrossConnections | Não | 
> | expressRouteCrossConnections/peerings | Não | 
> | gatewaySupportedDevices | Não | 
> | networkSecurityGroups | Não | 
> | quotas | Não | 
> | reservedIps | Não | 
> | virtualNetworks | Não | 
> | virtualNetworks/virtualNetworkPeerings | Não | 
> | virtualNetworks/virtualNetworkPeerings | Não | 

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | recursos | Não | 
> | disks | Não | 
> | images | Não | 
> | osImages | Não | 
> | osPlatformImages | Não | 
> | publicImages | Não | 
> | quotas | Não | 
> | storageAccounts | Não | 
> | storageAccounts/services | Não | 
> | storageAccounts/services/diagnosticSettings | Não | 
> | storageAccounts/vmImages | Não | 
> | vmImages | Não | 

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | accounts | Sim | 

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
> | disks | Sim | 
> | images | Sim | 
> | restorePointCollections | Sim | 
> | restorePointCollections/restorePoints | Não | 
> | sharedVMImages | Sim | 
> | sharedVMImages/versions | Sim | 
> | snapshots | Sim | 
> | virtualMachines | Sim | 
> | virtualMachines/diagnosticSettings | Não | 
> | virtualMachines/extensions | Sim | 
> | virtualMachineScaleSets | Sim | 
> | virtualMachines/extensions | Não | 
> | virtualMachineScaleSets/networkInterfaces | Não | 
> | virtualMachineScaleSets/publicIPAddresses | Não | 
> | virtualMachineScaleSets/virtualMachines | Não | 
> | virtualMachineScaleSets/networkInterfaces | Não | 

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
> | ReservationRecommendations | Não | 
> | ReservationSummaries | Não | 
> | ReservationTransactions | Não | 
> | Marcas | Não | 
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
> | registries/builds | Não | 
> | registries/builds/cancel | Não | 
> | registries/builds/getLogLink | Não | 
> | registries/buildTasks | Sim | 
> | registries/buildTasks/steps | Não | 
> | registries/eventGridFilters | Não | 
> | registries/getBuildSourceUploadUrl | Não | 
> | registries/GetCredentials | Não | 
> | registries/importImage | Não | 
> | registries/queueBuild | Não | 
> | registries/regenerateCredential | Não | 
> | registries/regenerateCredentials | Não | 
> | registries/replications | Sim | 
> | registries/runs | Não | 
> | registries/runs/cancel | Não | 
> | registries/scheduleRun | Não | 
> | registries/tasks | Sim | 
> | registries/updatePolicies | Não | 
> | registries/webhooks | Sim | 
> | registries/webhooks/getCallbackConfig | Não | 
> | registries/webhooks/ping | Não | 

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | containerServices | Sim | 
> | managedClusters | Sim | 

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | de dimensionamento da Web | Sim | 
> | updateCommunicationPreference | Não | 

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | accounts | Sim | 

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | Alertas | Não | 
> | BillingAccounts | Não | 
> | Conectores | Sim | 
> | Departments | Não | 
> | Dimensões | Não | 
> | EnrollmentAccounts | Não | 
> | Consultar | Não | 
> | register | Não | 
> | Reportconfigs | Não | 
> | Relatórios | Não | 

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | hubs | Sim | 
> | hubs/authorizationPolicies | Não | 
> | hubs/connectors | Não | 
> | hubs/connectors/mappings | Não | 
> | hubs/interactions | Não | 
> | hubs/kpi | Não | 
> | hubs/links | Não | 
> | hubs/profiles | Não | 
> | hubs/roleAssignments | Não | 
> | hubs/roles | Não | 
> | hubs/suggestTypeSchema | Não | 
> | hubs/views | Não | 
> | hubs/widgetTypes | Não | 

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
> | workspaces/virtualNetworkPeerings | Não | 

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | catalogs | Sim | 

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | connectionManagers | Sim | 

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | dataFactories | Sim | 
> | dataFactories/diagnosticSettings | Não | 
> | dataFactorySchema | Não | 
> | factories | Sim | 
> | factories/integrationRuntimes | Não | 

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | accounts | Sim | 
> | accounts/dataLakeStoreAccounts | Não | 
> | accounts/storageAccounts | Não | 
> | accounts/storageAccounts/containers | Não | 

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | accounts | Sim | 
> | accounts/eventGridFilters | Não | 
> | accounts/firewallRules | Não | 

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | services | Sim | 
> | services/projects | Sim | 

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | servers | Sim | 
> | servers/recoverableServers | Não | 
> | servers/virtualNetworkRules | Não | 

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | servers | Sim | 
> | servers/recoverableServers | Não | 
> | servers/virtualNetworkRules | Não | 

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | servers | Sim | 
> | servers/advisors | Não | 
> | servers/queryTexts | Não | 
> | servers/recoverableServers | Não | 
> | servers/topQueryStatistics | Não | 
> | servers/virtualNetworkRules | Não | 
> | servers/waitStatistics | Não | 

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | IotHubs | Sim | 
> | IotHubs/eventGridFilters | Não | 
> | ProvisioningServices | Sim | 
> | usages | Não | 

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | controladores | Sim | 

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | labs | Sim | 
> | labs/serviceRunners | Sim | 
> | labs/virtualMachines | Sim | 
> | schedules | Sim | 

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | databaseAccounts | Não | 
> | databaseAccounts | Sim | 

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | domains | Sim | 
> | domains/domainOwnershipIdentifiers | Não | 
> | generateSsoRequest | Não | 
> | topLevelDomains | Não | 
> | validateDomainRegistrationInformation | Não | 

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | lcsprojects | Não | 
> | lcsprojects/clouddeployments | Não | 
> | lcsprojects/connectors | Não | 

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | domains | Sim | 
> | domains/topics | Não | 
> | eventSubscriptions | Não | 
> | extensionTopics | Não | 
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

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | recursos | Não | 
> | providers | Não | 

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | enroll | Não | 
> | galleryitems | Não | 
> | generateartifactaccessuri | Não | 
> | myareas | Não | 
> | myareas/areas | Não | 
> | myareas/areas/areas | Não | 
> | myareas/areas/areas/galleryitems | Não | 
> | myareas/areas/galleryitems | Não | 
> | myareas/galleryitems | Não | 
> | register | Não | 
> | recursos | Não | 
> | retrieveresourcesbyid | Não | 

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | guestConfigurationAssignments | Não | 
> | software | Não | 

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | hanaInstances | Sim | 

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | clusters | Sim | 
> | clusters/applications | Não | 

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | jobs | Sim | 

## <a name="microsoftinformationprotection"></a>Microsoft.InformationProtection

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | labelGroups | Não | 
> | labelGroups/labels | Não | 
> | labelGroups/labels/conditions | Não | 
> | labelGroups/labels/subLabels | Não | 
> | labelGroups/labels/subLabels/conditions | Não | 

## <a name="microsoftinsights"></a>microsoft.insights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | actiongroups | Sim | 
> | activityLogAlerts | Sim | 
> | alertrules | Sim | 
> | automatedExportSettings | Não | 
> | autoscalesettings | Sim | 
> | baseline | Não | 
> | calculatebaseline | Não | 
> | components | Sim | 
> | components/metrics | Não | 
> | components/pricingPlans | Não | 
> | components/query | Não | 
> | diagnosticSettings | Não | 
> | diagnosticSettingsCategories | Não | 
> | eventCategories | Não | 
> | eventtypes | Não | 
> | extendedDiagnosticSettings | Não | 
> | logDefinitions | Não | 
> | logprofiles | Não | 
> | logs | Não | 
> | metricAlerts | Sim |
> | migrateToNewPricingModel | Não | 
> | myWorkbooks | Não | 
> | consultas | Não | 
> | rollbackToLegacyPricingModel | Não | 
> | scheduledqueryrules | Sim | 
> | vmInsightsOnboardingStatuses | Não | 
> | webtests | Sim | 
> | workbooks | Sim | 

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
> | vaults | Sim | 
> | vaults/accessPolicies | Não | 
> | vaults/secrets | Não | 

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | clusters | Sim | 
> | clusters/databases | Não | 
> | clusters/databases/dataconnections | Não | 
> | clusters/databases/eventhubconnections | Não | 

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | labaccounts | Sim | 
> | users | Não | 

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | accounts | Sim | 

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | accounts | Sim | 

## <a name="microsoftloganalytics"></a>Microsoft.LogAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | logs | Não | 

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | integrationAccounts | Sim | 
> | workflows | Sim | 

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | commitmentPlans | Sim | 
> | webServices | Sim | 
> | Workspaces | Sim | 

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | accounts | Sim | 
> | accounts/workspaces | Sim | 
> | accounts/workspaces/projects | Sim | 
> | teamAccounts | Sim | 
> | teamAccounts/workspaces | Sim | 
> | teamAccounts/workspaces/projects | Sim | 

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | accounts | Sim | 

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices
> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | workspaces | Sim | 
> | workspaces/computes | Não | 

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | Identidades | Não | 
> | userAssignedIdentities | Sim | 

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | getEntities | Não | 
> | managementGroups | Não | 
> | recursos | Não | 
> | startTenantBackfill | Não | 
> | tenantBackfillStatus | Não | 

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | accounts | Sim | 
> | accounts/eventGridFilters | Não | 

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | offers | Não | 
> | offerTypes | Não | 
> | offerTypes/publishers | Não | 
> | offerTypes/publishers/offers | Não | 
> | offerTypes/publishers/offers/plans | Não | 
> | offerTypes/publishers/offers/plans/agreements | Não | 
> | offerTypes/publishers/offers/plans/configs | Não | 
> | offerTypes/publishers/offers/plans/configs/importImage | Não | 
> | privategalleryitems | Não | 
> | products | Não | 

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
> | mediaservices/assets/assetFilters | Não | 
> | mediaservices/contentKeyPolicies | Não | 
> | mediaservices/eventGridFilters | Não | 
> | mediaservices/liveEvents | Não | 
> | mediaservices/liveEvents | Sim | 
> | mediaservices/liveEvents/liveOutputs | Não | 
> | mediaservices/liveEvents | Não | 
> | mediaservices/streamingEndpoints | Não | 
> | mediaservices/streamingEndpoints | Sim | 
> | mediaservices/streamingLocators | Não | 
> | mediaservices/streamingLocators | Não | 
> | mediaservices/transforms | Não | 
> | mediaservices/transforms | Não | 

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | projects | Sim | 

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | applicationGateways | Sim | 
> | applicationSecurityGroups | Sim | 
> | azureFirewallFqdnTags | Não | 
> | azureFirewalls | Sim | 
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
> | expressRouteServiceProviders | Não | 
> | frontdoors | Sim | 
> | frontdoorWebApplicationFirewallPolicies | Sim | 
> | getDnsResourceReference | Não | 
> | interfaceEndpoints | Sim | 
> | internalNotify | Não | 
> | loadBalancers | Sim | 
> | virtualNetworkGateways | Sim | 
> | natGateways | Sim | 
> | networkIntentPolicies | Sim | 
> | networkInterfaces | Sim | 
> | networkProfiles | Sim | 
> | networkSecurityGroups | Sim | 
> | networkWatchers | Sim | 
> | networkWatchers/connectionMonitors | Sim | 
> | networkWatchers/lenses | Sim | 
> | networkWatchers/lenses | Sim | 
> | privateLinkServices | Sim | 
> | publicIPAddresses | Sim | 
> | publicIPPrefixes | Sim | 
> | routeFilters | Sim | 
> | routeTables | Sim | 
> | serviceEndpointPolicies | Sim | 
> | trafficManagerGeographicHierarchies | Não | 
> | trafficmanagerprofiles | Sim | 
> | trafficmanagerprofiles/heatMaps | Não | 
> | virtualWans | Sim | 
> | virtualNetworkGateways | Sim | 
> | virtualNetworks | Sim | 
> | virtualNetworkTaps | Sim | 
> | virtualWans | Sim | 
> | vpnGateways | Sim | 
> | vpnSites | Sim | 
> | frontdoorWebApplicationFirewallPolicies | Sim | 

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | namespaces | Sim | 
> | namespaces/notificationHubs | Sim | 

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | dispositivos | Não | 
> | linkTargets | Não | 
> | storageInsightConfigs | Não | 
> | workspaces | Sim | 
> | workspaces/dataSources | Não | 
> | workspaces/linkedServices | Não | 
> | workspaces/query | Não | 

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | managementassociations | Não | 
> | managementconfigurations | Sim | 
> | solutions | Sim | 
> | Modos de exibição | Sim | 

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | policyEvents | Não | 
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
> | workspaceCollections | Sim | 

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | capacities | Sim | 

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | accounts | Sim | 

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | backupProtectedItems | Não | 
> | vaults | Sim | 

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | namespaces | Sim | 
> | namespaces/authorizationrules | Não | 
> | namespaces/hybridconnections | Não | 
> | namespaces/hybridconnections/authorizationrules | Não | 
> | namespaces/wcfrelays | Não | 
> | namespaces/wcfrelays/authorizationrules | Não | 

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | recursos | Não | 
> | subscriptionsStatus | Não | 

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | availabilityStatuses | Não | 
> | childAvailabilityStatuses | Não | 
> | childResources | Não | 
> | events | Não | 
> | impactedResources | Não | 
> | Notificações | Não | 

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | deployments | Não | 
> | deployments/operations | Não | 
> | links | Não | 
> | notifyResourceJobs | Não | 
> | providers | Não | 
> | resourceGroups | Não | 
> | recursos | Não | 
> | subscriptions | Não | 
> | subscriptions/providers | Não | 
> | subscriptions/resourceGroups | Não | 
> | subscriptions/resourcegroups/resources | Não | 
> | subscriptions/resources | Não | 
> | subscriptions/tagnames | Não | 
> | subscriptions/tagNames/tagValues | Não | 
> | tenants | Não | 

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | de dimensionamento da Web | Sim | 
> | saasresources | Não | 

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | flows | Sim | 
> | jobcollections | Sim | 

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
> | advancedThreatProtectionSettings | Não | 
> | alertas | Não | 
> | allowedConnections | Não | 
> | appliances | Não | 
> | applicationWhitelistings | Não | 
> | AutoProvisioningSettings | Não | 
> | Compliances | Não | 
> | dataCollectionAgents | Não | 
> | discoveredSecuritySolutions | Não | 
> | externalSecuritySolutions | Não | 
> | InformationProtectionPolicies | Não | 
> | jitNetworkAccessPolicies | Não | 
> | monitoring | Não | 
> | monitoring/antimalware | Não | 
> | monitoring/baseline | Não | 
> | monitoring/patch | Não | 
> | políticas | Não | 
> | pricings | Não | 
> | securityContacts | Não | 
> | securitySolutions | Não | 
> | securitySolutionsReferenceData | Não | 
> | securityStatus | Não | 
> | securityStatus/endpoints | Não | 
> | securityStatus/subnets | Não | 
> | securityStatus/virtualMachines | Não | 
> | securityStatuses | Não | 
> | securityStatusesSummaries | Não | 
> | configurações | Não | 
> | tarefas | Não | 
> | topologies | Não | 
> | workspaceSettings | Não | 

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | diagnosticSettings | Não | 
> | diagnosticSettingsCategories | Não | 

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | namespaces | Sim | 
> | namespaces/authorizationrules | Não | 
> | namespaces/disasterrecoveryconfigs | Não | 
> | namespaces/eventgridfilters | Não | 
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
> | clusters | Sim | 
> | clusters/applications | Não | 

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | de dimensionamento da Web | Sim | 
> | gateways | Sim | 
> | networks | Sim | 
> | segredos | Sim | 
> | volumes | Sim | 

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | SignalR | Sim | 

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | applianceDefinitions | Sim | 
> | appliances | Sim | 
> | applicationDefinitions | Sim | 
> | de dimensionamento da Web | Sim | 
> | jitRequests | Sim | 

## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | managedInstances | Sim |
> | managedInstances/databases | Sim |
> | managedInstances/databases/backupShortTermRetentionPolicies | Não |
> | managedInstances/databases/schemas/tables/columns/sensitivityLabels | Não |
> | managedInstances/databases/vulnerabilityAssessments | Não |
> | managedInstances/databases/vulnerabilityAssessments/rules/baselines | Não |
> | managedInstances/encryptionProtector | Não |
> | managedInstances/keys | Não |
> | managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies | Não |
> | managedInstances/vulnerabilityAssessments | Não |
> | servers | Sim | 
> | servers/administrators | Não | 
> | servers/communicationLinks | Não | 
> | servers/databases | Sim | 
> | servers/encryptionProtector | Não | 
> | servers/firewallRules | Não | 
> | servers/keys | Não | 
> | servers/restorableDroppedDatabases | Não | 
> | servers/serviceobjectives | Não | 
> | servers/tdeCertificates | Não | 


## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | SqlVirtualMachineGroups | Sim | 
> | SqlVirtualMachineGroups/AvailabilityGroupListeners | Não | 
> | SqlVirtualMachines | Sim | 

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | storageAccounts | Sim | 
> | storageAccounts/blobServices | Não | 
> | storageAccounts/blobServices | Não | 
> | storageAccounts/blobServices | Não | 
> | storageAccounts/services | Não | 
> | storageAccounts/blobServices | Não | 
> | usages | Não | 

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | storageSyncServices | Sim | 
> | storageSyncServices/registeredServers | Não | 
> | storageSyncServices/syncGroups | Não | 
> | storageSyncServices/syncGroups/cloudEndpoints | Não | 
> | storageSyncServices/syncGroups/cloudEndpoints | Não | 
> | storageSyncServices/workflows | Não | 

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | managers | Sim | 

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | streamingjobs | Sim | 
> | streamingjobs/diagnosticSettings | Não | 

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | CreateSubscription | Não | 
> | SubscriptionDefinitions | Não | 
> | SubscriptionDefinitions | Não | 

## <a name="microsoftsupport"></a>microsoft.support

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | supporttickets | Não | 

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | providerRegistrations | Sim | 
> | recursos | Sim | 

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | environments | Sim | 
> | environments/accessPolicies | Não | 
> | environments/eventsources | Sim | 
> | environments/referenceDataSets | Sim | 

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | conta | Sim | 
> | account/extension | Sim | 
> | account/project | Sim | 

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | apiManagementAccounts | Não | 
> | apiManagementAccounts/apis/connections/connectionAcls | Não | 
> | apiManagementAccounts/apis | Não | 
> | apiManagementAccounts/apis/connections/connectionAcls | Não | 
> | apiManagementAccounts/apis/connections/connectionAcls | Não | 
> | apiManagementAccounts/apis/connections/connectionAcls | Não | 
> | apiManagementAccounts/apis/connections/connectionAcls | Não | 
> | apiManagementAccounts/apis/localizedDefinitions | Não | 
> | apiManagementAccounts/connectionAcls | Não | 
> | apiManagementAccounts/connections | Não | 
> | billingMeters | Não | 
> | certificates | Sim | 
> | connectionGateways | Sim | 
> | connections | Sim | 
> | customApis | Sim | 
> | deletedSites | Não | 
> | funções | Não | 
> | hostingEnvironments | Sim | 
> | hostingEnvironments/multiRolePools | Não | 
> | hostingEnvironments/multiRolePools/instances | Não | 
> | hostingEnvironments/multiRolePools | Não | 
> | hostingEnvironments/workerPools/instances | Não | 
> | publishingUsers | Não | 
> | filmes | Não | 
> | resourceHealthMetadata | Não | 
> | runtimes | Não | 
> | serverFarms | Sim | 
> | serverFarms/workers | Não | 
> | sites | Sim | 
> | sites/domainOwnershipIdentifiers | Não | 
> | sites/hostNameBindings | Não | 
> | sites/instances | Não | 
> | sites/instances/extensions | Não | 
> | sites/premieraddons | Sim | 
> | sites/recommendations | Não | 
> | sites/resourceHealthMetadata | Não | 
> | sites/slots | Sim | 
> | sites/hostNameBindings | Não | 
> | sites/instances | Não | 
> | sites/instances/extensions | Não | 
> | sourceControls | Não | 
> | validade | Não | 
> | verifyHostingEnvironmentVnet | Não | 

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | diagnosticSettings | Não | 
> | diagnosticSettingsCategories | Não | 

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Exclusão completa de modo |
> | ------------- | ----------- |
> | DeviceServices | Sim | 

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