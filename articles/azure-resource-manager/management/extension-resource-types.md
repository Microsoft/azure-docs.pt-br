---
title: Tipos de recursos de extensão
description: Lista os tipos de recursos do Azure são usados para estender os recursos de outros tipos de recursos.
ms.topic: conceptual
ms.date: 11/14/2020
ms.openlocfilehash: 5561c480dd5a2849588ed2288eb5bcc35fc1446c
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658444"
---
# <a name="resource-types-that-extend-capabilities-of-other-resources"></a>Tipos de recursos que estendem funcionalidades de outros recursos

Um recurso de extensão é um recurso que o adiciona aos recursos de outro recurso. Por exemplo, o bloqueio de recurso é um recurso de extensão. Você aplica um bloqueio de recurso a outro recurso para impedir que ele seja excluído ou modificado. Não faz sentido criar um bloqueio de recurso por si só. Um recurso de extensão sempre é aplicado a outro recurso.

## <a name="microsoftadvisor"></a>Microsoft.Advisor

- Microsoft. Advisor/Configurations
- Microsoft. Advisor/Recommendations
- Microsoft. Advisor/supressões

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

- Microsoft. AlertsManagement/alertas

## <a name="microsoftauthorization"></a>Microsoft.Authorization

- Microsoft. Authorization/denyAssignments
- Microsoft.Authorization/locks
- Microsoft.Authorization/policyAssignments
- Microsoft. Authorization/policyDefinitions
- Microsoft. Authorization/policyExemptions
- Microsoft. Authorization/policySetDefinitions
- Microsoft. Authorization/privateLinkAssociations
- Microsoft.Authorization/roleAssignments
- Microsoft. Authorization/roleDefinitions

## <a name="microsoftautomanage"></a>Microsoft. autogerenci

- Microsoft. automanage/configurationProfileAssignments

## <a name="microsoftbilling"></a>Microsoft.Billing

- Microsoft. billing/billingPeriods
- Microsoft. billing/billingPermissions
- Microsoft. billing/billingRoleAssignments
- Microsoft. billing/billingRoleDefinitions
- Microsoft. billing/createBillingRoleAssignment

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

- Microsoft. Blueprint/blueprintAssignments
- Microsoft. Blueprint/plantas

## <a name="microsoftconsumption"></a>Microsoft.Consumption

- Microsoft. consumo/AggregatedCost
- Microsoft. consumo/saldos
- Microsoft. consumo/orçamentos
- Microsoft. consumo/encargos
- Microsoft. consumo/CostTags
- Microsoft. consumo/créditos
- Microsoft. consumo/eventos
- Microsoft. consumo/previsões
- Microsoft. consumo/muitos
- Microsoft. consumo/Marketplaces
- Microsoft. consumo/Pricesheets
- Microsoft. consumo/produtos
- Microsoft. consumo/ReservationDetails
- Microsoft. consumo/ReservationRecommendationDetails
- Microsoft. consumo/ReservationRecommendations
- Microsoft. consumo/ReservationSummaries
- Microsoft. consumo/ReservationTransactions

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

- Microsoft. ContainerInstance/serviceAssociationLinks

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

- Microsoft. CostManagement/alertas
- Microsoft. CostManagement/orçamentos
- Microsoft. CostManagement/Dimensions
- Microsoft. CostManagement/Exports
- Microsoft. CostManagement/ExternalSubscriptions
- Microsoft. CostManagement/Forecast
- Microsoft. CostManagement/insights
- Microsoft. CostManagement/Query
- Microsoft. CostManagement/Reportconfigs
- Microsoft. CostManagement/Reports
- Microsoft. CostManagement/views

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

- Microsoft.CustomProviders/associations

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

- Microsoft.EventGrid/eventSubscriptions
- Microsoft.EventGrid/extensionTopics

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

- Microsoft. GuestConfiguration/configurationProfileAssignments
- Microsoft. GuestConfiguration/guestConfigurationAssignments
- Microsoft. GuestConfiguration/software

## <a name="microsoftinsights"></a>microsoft.insights

- Microsoft. insights/linha de base
- Microsoft. insights/dataCollectionRuleAssociations
- Microsoft. insights/diagnosticSettings
- Microsoft. insights/diagnosticSettingsCategories
- Microsoft. insights/EventTypes
- Microsoft. insights/extendedDiagnosticSettings
- Microsoft. insights/guestDiagnosticSettingsAssociation
- Microsoft. insights/logDefinitions
- Microsoft. insights/logs
- Microsoft. insights/metricbaselines
- Microsoft. insights/metricDefinitions
- Microsoft. insights/metricNamespaces
- Microsoft. insights/métricas
- Microsoft. insights/minhas pastas de trabalho
- Microsoft. insights/topologia
- Microsoft. insights/transações

## <a name="microsoftkubernetesconfiguration"></a>Microsoft.KubernetesConfiguration

- Microsoft. KubernetesConfiguration/Extensions
- Microsoft. KubernetesConfiguration/sourceControlConfigurations

## <a name="microsoftmaintenance"></a>Microsoft.Maintenance

- Microsoft. Maintenance/applyUpdates
- Microsoft. Maintenance/configurationAssignments
- Microsoft. Maintenance/updates

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

- Microsoft. ManagedIdentity/Identities

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

- Microsoft. Managedservices/registrationAssignments
- Microsoft. Managedservices/registrationDefinitions

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

- Microsoft. OperationalInsights/storageInsightConfigs

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

- Microsoft. OperationsManagement/managementassociations

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

- Microsoft. PolicyInsights/atestados
- Microsoft. PolicyInsights/policyEvents
- Microsoft. PolicyInsights/policyStates
- Microsoft. PolicyInsights/policyTrackedResources
- Microsoft.PolicyInsights/remediations

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

- Microsoft. Recoveryservices/backupProtectedItems
- Microsoft. Recoveryservices/replicationEligibilityResults

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

- Microsoft. ResourceHealth/childResources
- Microsoft. ResourceHealth/eventos
- Microsoft. ResourceHealth/impactedResources
- Microsoft. ResourceHealth/Notifications

## <a name="microsoftresources"></a>Microsoft.Resources

- Microsoft. Resources/links
- Microsoft. Resources/Tags

## <a name="microsoftsecurity"></a>Microsoft.Security

- Microsoft. Security/adaptiveNetworkHardenings
- Microsoft. Security/advancedThreatProtectionSettings
- Microsoft. Security/assessmentMetadata
- Microsoft. Security/avaliações
- Segurança/conformidade da Microsoft
- Microsoft. Security/dataCollectionAgents
- Microsoft. Security/Devices
- Microsoft. Security/deviceSecurityGroups
- Microsoft. Security/InformationProtectionPolicies
- Microsoft. Security/iotSensors
- Microsoft. Security/jitPolicies
- Microsoft. Security/serverVulnerabilityAssessments
- Microsoft. Security/sqlVulnerabilityAssessments

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

- Microsoft. SecurityInsights/agregações
- Microsoft. SecurityInsights/alertRules
- Microsoft. SecurityInsights/alertRuleTemplates
- Microsoft. SecurityInsights/automationRules
- Microsoft. SecurityInsights/bookmarks
- Microsoft. SecurityInsights/casos
- Microsoft. SecurityInsights/dataconnecters
- Microsoft. SecurityInsights/dataConnectorsCheckRequirements
- Microsoft. SecurityInsights/Entities
- Microsoft. SecurityInsights/Incidents
- Microsoft. SecurityInsights/Settings
- Microsoft. SecurityInsights/threatIntelligence
- Microsoft. SecurityInsights/watchlists

## <a name="microsoftserialconsoleppe"></a>Microsoft. SerialConsole. PPE

- Microsoft. SerialConsole. PPE/serialPorts

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

- Microsoft. SoftwarePlan/hybridUseBenefits

## <a name="microsoftsupport"></a>microsoft.support

- Microsoft. support/supporttickets

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

- Microsoft. WorkloadMonitor/Components
- Microsoft. WorkloadMonitor/monitorInstances
- Microsoft. WorkloadMonitor/monitores
- Microsoft. WorkloadMonitor/notificationSettings

## <a name="next-steps"></a>Próximas etapas

- Para obter a ID de recurso para um recurso de extensão em um modelo de Azure Resource Manager, use o [extensionResourceId](../templates/template-functions-resource.md#extensionresourceid).
- Para obter um exemplo de como criar um recurso de extensão em um modelo, consulte [assinaturas de evento de grade de eventos](/azure/templates/microsoft.eventgrid/2019-06-01/eventsubscriptions).
