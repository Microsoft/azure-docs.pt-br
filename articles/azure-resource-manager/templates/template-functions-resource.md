---
title: Funções de modelo – recursos
description: Descreve as funções a serem usadas em um modelo de Azure Resource Manager (modelo ARM) para recuperar valores sobre recursos.
ms.topic: conceptual
ms.date: 02/10/2021
ms.openlocfilehash: efd7c554e6da8b60d4834d1a1290407a6b9e94d4
ms.sourcegitcommit: 44edde1ae2ff6c157432eee85829e28740c6950d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/25/2021
ms.locfileid: "105544104"
---
# <a name="resource-functions-for-arm-templates"></a>Funções de recurso para modelos do ARM

O Gerenciador de recursos fornece as seguintes funções para obter valores de recurso em seu modelo de Azure Resource Manager (modelo ARM):

* [extensionResourceId](#extensionresourceid)
* [list*](#list)
* [pickZones](#pickzones)
* [providers](#providers)
* [reference](#reference)
* [resourceGroup](#resourcegroup)
* [resourceId](#resourceid)
* [subscription](#subscription)
* [subscriptionResourceId](#subscriptionresourceid)
* [tenantResourceId](#tenantresourceid)

Para obter valores de parâmetros, de variáveis ou da implantação atual, veja [Funções de valor de implantação](template-functions-deployment.md).

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="extensionresourceid"></a>extensionResourceId

`extensionResourceId(resourceId, resourceType, resourceName1, [resourceName2], ...)`

Retorna a ID de recurso para um [recurso de extensão](../management/extension-resource-types.md), que é um tipo de recurso aplicado a outro recurso para adicionar aos seus recursos.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Type | Descrição |
|:--- |:--- |:--- |:--- |
| resourceId |Sim |string |A ID de recurso para o recurso ao qual o recurso de extensão é aplicado. |
| resourceType |Sim |string |Tipo de recurso, incluindo o namespace do provedor de recursos. |
| resourceName1 |Sim |string |Nome do recurso. |
| resourceName2 |Não |string |Próximo segmento de nome de recurso, se necessário. |

Continue adicionando nomes de recursos como parâmetros quando o tipo de recurso incluir mais segmentos.

### <a name="return-value"></a>Valor retornado

O formato básico da ID do recurso retornado por essa função é:

```json
{scope}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

O segmento de escopo varia conforme o recurso que está sendo estendido.

Quando o recurso de extensão é aplicado a um **recurso**, a ID de recurso é retornada no seguinte formato:

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{baseResourceProviderNamespace}/{baseResourceType}/{baseResourceName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

Quando o recurso de extensão é aplicado a um **grupo de recursos**, o formato é:

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

Quando o recurso de extensão é aplicado a uma **assinatura**, o formato é:

```json
/subscriptions/{subscriptionId}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

Quando o recurso de extensão é aplicado a um **grupo de gerenciamento**, o formato é:

```json
/providers/Microsoft.Management/managementGroups/{managementGroupName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

### <a name="extensionresourceid-example"></a>Exemplo de extensionResourceId

O exemplo a seguir retorna a ID de recurso para um bloqueio de grupo de recursos.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "lockName": {
      "type": "string"
    }
  },
  "variables": {},
  "resources": [],
  "outputs": {
    "lockResourceId": {
      "type": "string",
      "value": "[extensionResourceId(resourceGroup().Id , 'Microsoft.Authorization/locks', parameters('lockName'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param lockName string

output lockResourceId string = extensionResourceId(resourceGroup().Id, 'Microsoft.Authorization/locks', lockName)
```

---

Uma definição de política personalizada implantada em um grupo de gerenciamento é implementada como um recurso de extensão. Para criar e atribuir uma política, implante o modelo a seguir em um grupo de gerenciamento.

# <a name="json"></a>[JSON](#tab/json)

:::code language="json" source="~/quickstart-templates/managementgroup-deployments/mg-policy/azuredeploy.json":::

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param targetMG string
param allowedLocations array = [
  'australiaeast'
  'australiasoutheast'
  'australiacentral'
]

var mgScope = tenantResourceId('Microsoft.Management/managementGroups', targetMG)
var policyDefinition = 'LocationRestriction'

resource myDefinition 'Microsoft.Authorization/policyDefinitions@2019-09-01' = {
  name: policyDefinition
  properties: {
    policyType: 'Custom'
    mode: 'All'
    parameters: {}
    policyRule: {
      'if': {
        'not': {
          'field': 'location'
          'in': allowedLocations
        }
      }
      'then': {
        'effect': 'deny'
      }
    }
  }
}

resource myAssignment 'Microsoft.Authorization/policyAssignments@2019-09-01' = {
  name: 'location-lock'
  properties: {
    scope: mgScope
    policyDefinitionId: extensionResourceId(mgScope, 'Microsoft.Authorization/policyDefinitions', policyDefinition)
  }
}
```

---

As definições de política internas são recursos de nível de locatário. Para obter um exemplo de implantação de uma definição de política interna, consulte [tenantResourceId](#tenantresourceid).

<a id="listkeys"></a>
<a id="list"></a>

## <a name="list"></a>lista*

`list{Value}(resourceName or resourceIdentifier, apiVersion, functionValues)`

A sintaxe dessa função varia de acordo com o nome das operações de lista. Cada implementação retorna valores para o tipo de recurso compatível com uma operação de lista. O nome da operação deve começar com `list` e pode ter um sufixo. Alguns usos comuns são `list` ,, `listKeys` `listKeyValue` e `listSecrets` .

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Type | Descrição |
|:--- |:--- |:--- |:--- |
| resourceName ou resourceIdentifier |Sim |string |Identificador exclusivo para o recurso. |
| apiVersion |Sim |string |Versão de API do estado de runtime do recurso. Normalmente, no formato **aaaa-mm-dd**. |
| functionValues |Não |objeto | Um objeto que tem valores para a função. Fornecer apenas este objeto para funções que dão suporte ao recebimento de um objeto com valores de parâmetro, como **listAccountSas** em uma conta de armazenamento. Um exemplo de passar valores de função é mostrado neste artigo. |

### <a name="valid-uses"></a>Usos válidos

As funções de lista podem ser usadas nas propriedades de uma definição de recurso. Não use uma função de lista que exponha informações confidenciais na seção de saídas de um modelo. Os valores de saída são armazenados no histórico de implantação e podem ser recuperados por um usuário mal-intencionado.

Quando usado com [iteração de propriedade](copy-properties.md), você pode usar as funções de lista para `input` porque a expressão é atribuída à propriedade de recurso. Você não pode usá-las com `count` porque a contagem deve ser determinada antes que a função list seja resolvida.

### <a name="implementations"></a>Implementações

Os possíveis usos de lista* são mostrados na tabela a seguir.

| Tipo de recurso | Nome da função |
| ------------- | ------------- |
| Microsoft. addons/supportProviders | listsupportplaninfo |
| Microsoft.AnalysisServices/servers | [listGatewayStatus](/rest/api/analysisservices/servers/listgatewaystatus) |
| Microsoft. ApiManagement/Service/authorizationServers | [listSecrets](/rest/api/apimanagement/2019-12-01/authorizationserver/listsecrets) |
| Microsoft. ApiManagement/Service/gateways | [listKeys](/rest/api/apimanagement/2019-12-01/gateway/listkeys) |
| Microsoft. ApiManagement/Service/identityProviders | [listSecrets](/rest/api/apimanagement/2019-12-01/identityprovider/listsecrets) |
| Microsoft. ApiManagement/Service/namedValues | [lista de](/rest/api/apimanagement/2019-12-01/namedvalue/listvalue) |
| Microsoft. ApiManagement/Service/openidConnectProviders | [listSecrets](/rest/api/apimanagement/2019-12-01/openidconnectprovider/listsecrets) |
| Microsoft. ApiManagement/Service/subscriptions | [listSecrets](/rest/api/apimanagement/2019-12-01/subscription/listsecrets) |
| Microsoft.AppConfiguration/configurationStores | [ListKeys](/rest/api/appconfiguration/configurationstores/listkeys) |
| Microsoft.AppPlatform/Spring | [listTestKeys](/rest/api/azurespringcloud/services/listtestkeys) |
| Microsoft.Automation/automationAccounts | [listKeys](/rest/api/automation/keys/listbyautomationaccount) |
| Microsoft.Batch/batchAccounts | [listkeys](/rest/api/batchmanagement/batchaccount/getkeys) |
| Microsoft.BatchAI/workspaces/experiments/jobs | [listoutputfiles](/rest/api/batchai/jobs/listoutputfiles) |
| Microsoft.Blockchain/blockchainMembers | [listApiKeys](/rest/api/blockchain/2019-06-01-preview/blockchainmembers/listapikeys) |
| Microsoft.Blockchain/blockchainMembers/transactionNodes | [listApiKeys](/rest/api/blockchain/2019-06-01-preview/transactionnodes/listapikeys) |
| Microsoft. BotService/botServices/Channels | [listChannelWithKeys](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/botservice/resource-manager/Microsoft.BotService/stable/2020-06-02/botservice.json#L553) |
| Microsoft.Cache/redis | [listKeys](/rest/api/redis/redis/listkeys) |
| Microsoft.CognitiveServices/accounts | [listKeys](/rest/api/cognitiveservices/accountmanagement/accounts/listkeys) |
| Microsoft.ContainerRegistry/registries | [listBuildSourceUploadUrl](/rest/api/containerregistry/registries%20(tasks)/getbuildsourceuploadurl) |
| Microsoft.ContainerRegistry/registries | [listCredentials](/rest/api/containerregistry/registries/listcredentials) |
| Microsoft.ContainerRegistry/registries | [listUsages](/rest/api/containerregistry/registries/listusages) |
| Microsoft. ContainerRegistry/registros/agentpools | listQueueStatus |
| Microsoft. ContainerRegistry/registros/buildTasks | listSourceRepositoryProperties |
| Microsoft. ContainerRegistry/registros/buildTasks/etapas | listBuildArguments |
| Microsoft. ContainerRegistry/registros/taskruns | listDetails |
| Microsoft.ContainerRegistry/registries/webhooks | [listEvents](/rest/api/containerregistry/webhooks/listevents) |
| Microsoft.ContainerRegistry/registries/runs | [listLogSasUrl](/rest/api/containerregistry/runs/getlogsasurl) |
| Microsoft.ContainerRegistry/registries/tasks | [listDetails](/rest/api/containerregistry/tasks/getdetails) |
| Microsoft.ContainerService/managedClusters | [listClusterAdminCredential](/rest/api/aks/managedclusters/listclusteradmincredentials) |
| Microsoft.ContainerService/managedClusters | [listClusterMonitoringUserCredential](/rest/api/aks/managedclusters/listclustermonitoringusercredentials) |
| Microsoft.ContainerService/managedClusters | [listClusterUserCredential](/rest/api/aks/managedclusters/listclusterusercredentials) |
| Microsoft.ContainerService/managedClusters/accessProfiles | [listCredential](/rest/api/aks/managedclusters/getaccessprofile) |
| Microsoft.DataBox/jobs | listCredentials |
| Microsoft.DataFactory/datafactories/gateways | listauthkeys |
| Microsoft.DataFactory/factories/integrationruntimes | [listauthkeys](/rest/api/datafactory/integrationruntimes/listauthkeys) |
| Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers | [listSasTokens](/rest/api/datalakeanalytics/storageaccounts/listsastokens) |
| Microsoft.DataShare/accounts/shares | [listSynchronizations](/rest/api/datashare/shares/listsynchronizations) |
| Microsoft.DataShare/accounts/shareSubscriptions | [listSourceShareSynchronizationSettings](/rest/api/datashare/sharesubscriptions/listsourcesharesynchronizationsettings) |
| Microsoft.DataShare/accounts/shareSubscriptions | [listSynchronizationDetails](/rest/api/datashare/sharesubscriptions/listsynchronizationdetails) |
| Microsoft.DataShare/accounts/shareSubscriptions | [listSynchronizations](/rest/api/datashare/sharesubscriptions/listsynchronizations) |
| Microsoft.Devices/iotHubs | [listkeys](/rest/api/iothub/iothubresource/listkeys) |
| Microsoft.Devices/iotHubs/iotHubKeys | [listkeys](/rest/api/iothub/iothubresource/getkeysforkeyname) |
| Microsoft.Devices/provisioningServices/keys | [listkeys](/rest/api/iot-dps/iotdpsresource/listkeysforkeyname) |
| Microsoft.Devices/provisioningServices | [listkeys](/rest/api/iot-dps/iotdpsresource/listkeys) |
| Microsoft.DevTestLab/labs | [ListVhds](/rest/api/dtl/labs/listvhds) |
| Microsoft.DevTestLab/labs/schedules | [ListApplicable](/rest/api/dtl/schedules/listapplicable) |
| Microsoft.DevTestLab/labs/users/serviceFabrics | [ListApplicableSchedules](/rest/api/dtl/servicefabrics/listapplicableschedules) |
| Microsoft.DevTestLab/labs/virtualMachines | [ListApplicableSchedules](/rest/api/dtl/virtualmachines/listapplicableschedules) |
| Microsoft.DocumentDB/databaseAccounts | [listConnectionStrings](/rest/api/cosmos-db-resource-provider/2021-03-01-preview/databaseaccounts/listconnectionstrings) |
| Microsoft.DocumentDB/databaseAccounts | [listKeys](/rest/api/cosmos-db-resource-provider/2021-03-01-preview/databaseaccounts/listkeys) |
| Microsoft.DocumentDB/databaseAccounts/notebookWorkspaces | [listConnectionInfo](/rest/api/cosmos-db-resource-provider/2021-03-01-preview/notebookworkspaces/listconnectioninfo) |
| Microsoft.DomainRegistration | [listDomainRecommendations](/rest/api/appservice/domains/listrecommendations) |
| Microsoft.DomainRegistration/topLevelDomains | [listAgreements](/rest/api/appservice/topleveldomains/listagreements) |
| Microsoft.EventGrid/domains | [listKeys](/rest/api/eventgrid/version2020-06-01/domains/listsharedaccesskeys) |
| Microsoft.EventGrid/topics | [listKeys](/rest/api/eventgrid/version2020-06-01/topics/listsharedaccesskeys) |
| Microsoft.EventHub/namespaces/authorizationRules | [listkeys](/rest/api/eventhub) |
| Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules | [listkeys](/rest/api/eventhub) |
| Microsoft.EventHub/namespaces/eventhubs/authorizationRules | [listkeys](/rest/api/eventhub) |
| Microsoft.ImportExport/jobs | [listBitLockerKeys](/rest/api/storageimportexport/bitlockerkeys/list) |
| Microsoft.Kusto/Clusters/Databases | [ListPrincipals](/rest/api/azurerekusto/databases/listprincipals) |
| Microsoft.LabServices/users | [ListEnvironments](/rest/api/labservices/globalusers/listenvironments) |
| Microsoft.LabServices/users | [ListLabs](/rest/api/labservices/globalusers/listlabs) |
| Microsoft.Logic/integrationAccounts/agreements | [listContentCallbackUrl](/rest/api/logic/agreements/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts/assemblies | [listContentCallbackUrl](/rest/api/logic/integrationaccountassemblies/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts | [listCallbackUrl](/rest/api/logic/integrationaccounts/getcallbackurl) |
| Microsoft.Logic/integrationAccounts | [listKeyVaultKeys](/rest/api/logic/integrationaccounts/listkeyvaultkeys) |
| Microsoft.Logic/integrationAccounts/maps | [listContentCallbackUrl](/rest/api/logic/maps/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts/partners | [listContentCallbackUrl](/rest/api/logic/partners/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts/schemas | [listContentCallbackUrl](/rest/api/logic/schemas/listcontentcallbackurl) |
| Microsoft.Logic/workflows | [listCallbackUrl](/rest/api/logic/workflows/listcallbackurl) |
| Microsoft.Logic/workflows | [listSwagger](/rest/api/logic/workflows/listswagger) |
| Microsoft.Logic/workflows/runs/actions | [listExpressionTraces](/rest/api/logic/workflowrunactions/listexpressiontraces) |
| Microsoft.Logic/workflows/runs/actions/repetitions | [listExpressionTraces](/rest/api/logic/workflowrunactionrepetitions/listexpressiontraces) |
| Microsoft.Logic/workflows/triggers | [listCallbackUrl](/rest/api/logic/workflowtriggers/listcallbackurl) |
| Microsoft.Logic/workflows/versions/triggers | [listCallbackUrl](/rest/api/logic/workflowversions/listcallbackurl) |
| Microsoft.MachineLearning/webServices | [listkeys](/rest/api/machinelearning/webservices/listkeys) |
| Microsoft.MachineLearning/Workspaces | listworkspacekeys |
| Microsoft.MachineLearningServices/workspaces/computes | [listKeys](/rest/api/azureml/workspacesandcomputes/machinelearningcompute/listkeys) |
| Microsoft.MachineLearningServices/workspaces/computes | [listNodes](/rest/api/azureml/workspacesandcomputes/machinelearningcompute/listnodes) |
| Microsoft.MachineLearningServices/workspaces | [listKeys](/rest/api/azureml/workspacesandcomputes/workspaces/listkeys) |
| Microsoft.Maps/accounts | [listKeys](/rest/api/maps-management/accounts/listkeys) |
| Microsoft.Media/mediaservices/assets | [listContainerSas](/rest/api/media/assets/listcontainersas) |
| Microsoft.Media/mediaservices/assets | [listStreamingLocators](/rest/api/media/assets/liststreaminglocators) |
| Microsoft.Media/mediaservices/streamingLocators | [listContentKeys](/rest/api/media/streaminglocators/listcontentkeys) |
| Microsoft.Media/mediaservices/streamingLocators | [listPaths](/rest/api/media/streaminglocators/listpaths) |
| Microsoft.Network/applicationSecurityGroups | listIpConfigurations |
| Microsoft.NotificationHubs/Namespaces/authorizationRules | [listkeys](/rest/api/notificationhubs/namespaces/listkeys) |
| Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules | [listkeys](/rest/api/notificationhubs/notificationhubs/listkeys) |
| Microsoft.OperationalInsights/workspaces | [list](/rest/api/loganalytics/workspaces/list) |
| Microsoft.OperationalInsights/workspaces | listKeys |
| Microsoft.PolicyInsights/remediations | [listDeployments](/rest/api/policy/remediations/listdeploymentsatresourcegroup) |
| Microsoft. RedHatOpenShift/openShiftClusters | [listCredentials](/rest/api/openshift/openshiftclusters/listcredentials) |
| Microsoft.Relay/namespaces/authorizationRules | [listkeys](/rest/api/relay/namespaces/listkeys) |
| Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules | listkeys |
| Microsoft.Relay/namespaces/HybridConnections/authorizationRules | [listkeys](/rest/api/relay/hybridconnections/listkeys) |
| Microsoft.Relay/namespaces/WcfRelays/authorizationRules | [listkeys](/rest/api/relay/wcfrelays/listkeys) |
| Microsoft.Search/searchServices | [listAdminKeys](/rest/api/searchmanagement/adminkeys/get) |
| Microsoft.Search/searchServices | [listQueryKeys](/rest/api/searchmanagement/querykeys/listbysearchservice) |
| Microsoft.ServiceBus/namespaces/authorizationRules | [listkeys](/rest/api/servicebus/stable/namespaces%20-%20authorization%20rules/listkeys) |
| Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules | [listkeys](/rest/api/servicebus/stable/disasterrecoveryconfigs/listkeys) |
| Microsoft.ServiceBus/namespaces/queues/authorizationRules | [listkeys](/rest/api/servicebus/stable/queues%20-%20authorization%20rules/listkeys) |
| Microsoft.ServiceBus/namespaces/topics/authorizationRules | [listkeys](/rest/api/servicebus/stable/topics%20–%20authorization%20rules/listkeys) |
| Microsoft.SignalRService/SignalR | [listkeys](/rest/api/signalr/signalr/listkeys) |
| Microsoft.Storage/storageAccounts | [listAccountSas](/rest/api/storagerp/storageaccounts/listaccountsas) |
| Microsoft.Storage/storageAccounts | [listkeys](/rest/api/storagerp/storageaccounts/listkeys) |
| Microsoft.Storage/storageAccounts | [listServiceSas](/rest/api/storagerp/storageaccounts/listservicesas) |
| Microsoft.StorSimple/managers/devices | [listFailoverSets](/rest/api/storsimple/devices/listfailoversets) |
| Microsoft.StorSimple/managers/devices | [listFailoverTargets](/rest/api/storsimple/devices/listfailovertargets) |
| Microsoft.StorSimple/managers | [listActivationKey](/rest/api/storsimple/managers/getactivationkey) |
| Microsoft.StorSimple/managers | [listPublicEncryptionKey](/rest/api/storsimple/managers/getpublicencryptionkey) |
| Microsoft. Synapse/Workspaces/integrationRuntimes | [listAuthKeys](/rest/api/synapse/integrationruntimeauthkeys/list) |
| Microsoft.Web/connectionGateways | ListStatus |
| microsoft.web/connections | listconsentlinks |
| Microsoft.Web/customApis | listWsdlInterfaces |
| microsoft.web/locations | listwsdlinterfaces |
| microsoft.web/apimanagementaccounts/apis/connections | listconnectionkeys |
| microsoft.web/apimanagementaccounts/apis/connections | listsecrets |
| microsoft.web/sites/backups | [list](/rest/api/appservice/webapps/listbackups) |
| Microsoft.Web/sites/config | [list](/rest/api/appservice/webapps/listconfigurations) |
| microsoft.web/sites/functions | [listkeys](/rest/api/appservice/webapps/listfunctionkeys)
| microsoft.web/sites/functions | [listsecrets](/rest/api/appservice/webapps/listfunctionsecrets) |
| microsoft.web/sites/hybridconnectionnamespaces/relays | [listkeys](/rest/api/appservice/appserviceplans/listhybridconnectionkeys) |
| microsoft.web/sites | [listsyncfunctiontriggerstatus](/rest/api/appservice/webapps/listsyncfunctiontriggers) |
| microsoft.web/sites/slots/functions | [listsecrets](/rest/api/appservice/webapps/listfunctionsecretsslot) |
| microsoft.web/sites/slots/backups | [list](/rest/api/appservice/webapps/listbackupsslot) |
| Microsoft.Web/sites/slots/config | [list](/rest/api/appservice/webapps/listconfigurationsslot) |
| microsoft.web/sites/slots/functions | [listsecrets](/rest/api/appservice/webapps/listfunctionsecretsslot) |

Para determinar quais tipos de recursos têm uma operação de lista, use as seguintes opções:

* Veja as [operações da API REST](/rest/api/) de um provedor de recursos e procure por operações de lista. Por exemplo, as contas de armazenamento têm a [operação listKeys](/rest/api/storagerp/storageaccounts).
* Use o cmdlet do PowerShell [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation). O exemplo a seguir obtém todas as operações de lista de contas de armazenamento:

  ```powershell
  Get-AzProviderOperation -OperationSearchString "Microsoft.Storage/*" | where {$_.Operation -like "*list*"} | FT Operation
  ```

* Use o seguinte comando da CLI do Azure para filtrar apenas as operações de lista:

  ```azurecli
  az provider operation show --namespace Microsoft.Storage --query "resourceTypes[?name=='storageAccounts'].operations[].name | [?contains(@, 'list')]"
  ```

### <a name="return-value"></a>Valor retornado

O objeto retornado varia de acordo com a função de lista que você usa. Por exemplo, o listKeys para uma conta de armazenamento retorna o seguinte formato:

```json
{
  "keys": [
    {
      "keyName": "key1",
      "permissions": "Full",
      "value": "{value}"
    },
    {
      "keyName": "key2",
      "permissions": "Full",
      "value": "{value}"
    }
  ]
}
```

Outras funções de lista têm formatos diferentes de retorno. Para ver o formato de uma função, inclua-a na seção de saídas, conforme mostra o exemplo de modelo.

### <a name="remarks"></a>Comentários

Especifique o recurso usando o nome de recurso ou o [função resourceId](#resourceid). Ao usar uma função de lista no mesmo modelo que implanta o recurso referenciado, use o nome do recurso.

Se você usar a função **list** em um recurso implantado condicionalmente, a função será avaliada mesmo que o recurso não seja implantado. Você receberá um erro se a função **list** se referir a um recurso que não existe. Use a função **if** para garantir que a função seja avaliada apenas quando o recurso estiver sendo implantado. Consulte a [função if](template-functions-logical.md#if) para um modelo de exemplo que usa if e list com um recurso implantado condicionalmente.

### <a name="list-example"></a>Exemplo de lista

O exemplo a seguir usa listKeys ao definir um valor para [scripts de implantação](deployment-script-template.md).

# <a name="json"></a>[JSON](#tab/json)

```json
"storageAccountSettings": {
  "storageAccountName": "[variables('storageAccountName')]",
  "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), '2019-06-01').keys[0].value]"
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
storageAccountSettings: {
  storageAccountName: storageAccountName
  storageAccountKey: listKeys(resourceId('Microsoft.Storage/storageAccounts', storageAccountName), '2019-06-01').keys[0].value
}

```

---

O exemplo a seguir mostra uma função de lista que usa um parâmetro. Nesse caso, a função é **listAccountSas**. Passe um objeto para a hora de expiração. A data de validade deve ser futura.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "accountSasProperties": {
    "type": "object",
    "defaultValue": {
      "signedServices": "b",
      "signedPermission": "r",
      "signedExpiry": "2020-08-20T11:00:00Z",
      "signedResourceTypes": "s"
    }
  }
},
...
"sasToken": "[listAccountSas(parameters('storagename'), '2018-02-01', parameters('accountSasProperties')).accountSasToken]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param accountSasProperties object {
  default: {
    signedServices: 'b'
    signedPermission: 'r'
    signedExpiry: '2020-08-20T11:00:00Z'
    signedResourceTypes: 's'
  }
}
...
sasToken: listAccountSas(storagename, '2018-02-01', accountSasProperties).accountSasToken
```

---

## <a name="pickzones"></a>pickZones

`pickZones(providerNamespace, resourceType, location, [numberOfZones], [offset])`

Determina se um tipo de recurso dá suporte a zonas para uma região.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Type | Descrição |
|:--- |:--- |:--- |:--- |
| providerNamespace | Sim | string | O namespace do provedor de recursos para o tipo de recurso para verificar o suporte de zona. |
| resourceType | Sim | string | O tipo de recurso para verificar o suporte de zona. |
| local | Sim | string | A região para verificar o suporte de zona. |
| numberOfZones | Não | inteiro | O número de zonas lógicas a serem retornadas. O padrão é 1. O número deve ser um inteiro positivo de 1 a 3.  Use 1 para recursos de zona única. Para recursos de várias zonas, o valor deve ser menor ou igual ao número de zonas com suporte. |
| deslocamento | Não | inteiro | O deslocamento da zona lógica inicial. A função retornará um erro se offset mais numberOfZones exceder o número de zonas com suporte. |

### <a name="return-value"></a>Retornar valor

Uma matriz com as zonas com suporte. Ao usar os valores padrão para offset e numberOfZones, um tipo de recurso e uma região que dão suporte a zonas retornam a seguinte matriz:

```json
[
    "1"
]
```

Quando o `numberOfZones` parâmetro é definido como 3, ele retorna:

```json
[
    "1",
    "2",
    "3"
]
```

Quando o tipo de recurso ou a região não dá suporte a zonas, uma matriz vazia é retornada.

```json
[
]
```

### <a name="pickzones-example"></a>exemplo de pickZones

O modelo a seguir mostra três resultados para usar a função pickZones.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "functions": [],
  "variables": {},
  "resources": [],
  "outputs": {
    "supported": {
      "type": "array",
      "value": "[pickZones('Microsoft.Compute', 'virtualMachines', 'westus2')]"
    },
    "notSupportedRegion": {
      "type": "array",
      "value": "[pickZones('Microsoft.Compute', 'virtualMachines', 'northcentralus')]"
    },
    "notSupportedType": {
      "type": "array",
      "value": "[pickZones('Microsoft.Cdn', 'profiles', 'westus2')]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output supported array = pickZones('Microsoft.Compute', 'virtualMachines', 'westus2')
output notSupportedRegion array = pickZones('Microsoft.Compute', 'virtualMachines', 'northcentralus')
output notSupportedType array = pickZones('Microsoft.Cdn', 'profiles', 'westus2')
```

---

A saída dos exemplos anteriores retorna três matrizes.

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| com suporte | array | ["1"] |
| notSupportedRegion | array | [] |
| Sem suporte | array | [] |

Você pode usar a resposta de pickZones para determinar se deve fornecer NULL para zonas ou atribuir máquinas virtuais a diferentes zonas. O exemplo a seguir define um valor para a zona com base na disponibilidade de zonas.

# <a name="json"></a>[JSON](#tab/json)

```json
"zones": {
  "value": "[if(not(empty(pickZones('Microsoft.Compute', 'virtualMachines', 'westus2'))), string(add(mod(copyIndex(),3),1)), json('null'))]"
},
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

> [!NOTE]
> Loops e copyIndex () ainda não foram implementados.  Consulte [loops](https://github.com/Azure/bicep/blob/main/docs/spec/loops.md).

---

## <a name="providers"></a>providers

`providers(providerNamespace, [resourceType])`

Retorna informações sobre um provedor de recursos e seus tipos de recursos com suporte. Se você não fornecer um tipo de recurso, a função retornará todos os tipos com suporte para o provedor de recursos.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Type | Descrição |
|:--- |:--- |:--- |:--- |
| providerNamespace |Sim |string |Namespace do provedor |
| resourceType |Não |string |O tipo de recurso no namespace especificado. |

### <a name="return-value"></a>Valor retornado

Cada tipo com suporte é retornado no seguinte formato:

```json
{
  "resourceType": "{name of resource type}",
  "locations": [ all supported locations ],
  "apiVersions": [ all supported API versions ]
}
```

A ordenação de matriz dos valores retornados não é garantida.

### <a name="providers-example"></a>Exemplo de provedores

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/providers.json) a seguir mostra como usar a função do provedor:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "providerNamespace": {
      "type": "string"
    },
    "resourceType": {
      "type": "string"
    }
  },
  "resources": [],
  "outputs": {
    "providerOutput": {
      "type": "object",
      "value": "[providers(parameters('providerNamespace'), parameters('resourceType'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param providerNamespace string
param resourceType string

output providerOutput array = providers(providerNamespace, resourceType)
```

---

Para o provedor de recursos **Microsoft.Web** e o tipo de recurso **sites**, o exemplo anterior retorna um objeto no seguinte formato:

```json
{
  "resourceType": "sites",
  "locations": [
    "South Central US",
    "North Europe",
    "West Europe",
    "Southeast Asia",
    ...
  ],
  "apiVersions": [
    "2016-08-01",
    "2016-03-01",
    "2015-08-01-preview",
    "2015-08-01",
    ...
  ]
}
```

## <a name="reference"></a>reference

`reference(resourceName or resourceIdentifier, [apiVersion], ['Full'])`

Retorna um objeto que representa o estado de runtime de um recurso.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Type | Descrição |
|:--- |:--- |:--- |:--- |
| resourceName ou resourceIdentifier |Sim |string |Nome ou identificador exclusivo de um recurso. Ao referenciar um recurso no modelo atual, forneça apenas o nome do recurso como parâmetro. Ao fazer referência a um recurso implantado anteriormente ou quando o nome do recurso for ambíguo, forneça a ID do recurso. |
| apiVersion |Não |string |Versão da API do recurso especificado. **Esse parâmetro é exigido quando o recurso não estiver provisionado no mesmo modelo.** Normalmente, no formato **aaaa-mm-dd**. Para obter as versões de API válidas para seu recurso, confira [referência de modelo](/azure/templates/). |
| 'Full' |Não |string |Valor que especifica se o objeto de recurso completo deve ser retornado. Se você não especificar `'Full'`, apenas o objeto de propriedades do recurso será retornado. O objeto completo inclui valores como a ID do recurso e o local. |

### <a name="return-value"></a>Valor retornado

Cada tipo de recurso retorna propriedades diferentes para a função de referência. A função não retorna um único formato predefinido. Além disso, o valor retornado difere com base no valor do argumento `'Full'`. Para ver as propriedades de um tipo de recurso, retorne o objeto na seção de saída, conforme mostra o exemplo.

### <a name="remarks"></a>Comentários

A função de referência recupera o estado de runtime de um recurso implantado anteriormente ou um recurso implantado no modelo atual. Este artigo mostra exemplos de ambos os cenários.

Normalmente, você usa a função **reference** para retornar um valor específico de um objeto, como o URI do ponto de extremidade de blob ou o nome de domínio totalmente qualificado.

# <a name="json"></a>[JSON](#tab/json)

```json
"outputs": {
  "BlobUri": {
    "type": "string",
    "value": "[reference(resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))).primaryEndpoints.blob]"
  },
  "FQDN": {
    "type": "string",
    "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', parameters('ipAddressName'))).dnsSettings.fqdn]"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output BlobUri string = reference(resourceId('Microsoft.Storage/storageAccounts', storageAccountName)).primaryEndpoints.blob
output FQDN string = reference(resourceId('Microsoft.Network/publicIPAddresses', ipAddressName)).dnsSettings.fqdn
```

---

Use `'Full'` quando precisar de valores de recurso que não fizerem parte do esquema de propriedades. Por exemplo, para definir políticas de acesso ao cofre de chaves, obtenha as propriedades de identidade de uma máquina virtual.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "type": "Microsoft.KeyVault/vaults",
  "apiVersion": "2019-09-01",
  "name": "vaultName",
  "properties": {
    "tenantId": "[subscription().tenantId]",
    "accessPolicies": [
      {
        "tenantId": "[reference(resourceId('Microsoft.Compute/virtualMachines', variables('vmName')), '2019-03-01', 'Full').identity.tenantId]",
        "objectId": "[reference(resourceId('Microsoft.Compute/virtualMachines', variables('vmName')), '2019-03-01', 'Full').identity.principalId]",
        "permissions": {
          "keys": [
            "all"
          ],
          "secrets": [
            "all"
          ]
        }
      }
    ],
    ...
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resource myVault 'Microsoft.KeyVault/vaults@2019-09-01' = {
  name: 'vaultName'
  properties: {
    tenantId: subscription().tenantId
    accessPolicies: [
      {
        'tenantId': reference(resourceId('Microsoft.Compute/virtualMachines', vmName), '2019-03-01', 'Full').identity.tenantId
        'objectId': reference(resourceId('Microsoft.Compute/virtualMachines', vmName), '2019-03-01', 'Full').identity.principalId
        'permissions': {
          'keys': [
            'all'
          ]
          'secrets': [
            'all'
          ]
        }
      }
    ]
  }
}
```

---

### <a name="valid-uses"></a>Usos válidos

A função de referência pode ser usada somente nas propriedades de uma definição de recurso e na seção de saídas de um modelo ou uma implantação. Quando usado com [iteração de propriedade](copy-properties.md), você pode usar a função de referência para `input` porque a expressão é atribuída à propriedade de recurso.

Você não pode usar a função de referência para definir o valor da propriedade `count` em um loop de cópia. Você pode usar para definir outras propriedades no loop. A referência está bloqueada para a propriedade de contagem porque essa propriedade deve ser determinada antes que a função de referência seja resolvida.

Para usar a função de referência ou qualquer função de lista * na seção de saídas de um modelo aninhado, você deve definir o  ```expressionEvaluationOptions``` para usar a avaliação de [escopo interno](linked-templates.md#expression-evaluation-scope-in-nested-templates) ou usar um link vinculado em vez de um modelo aninhado.

Se você usar a função **reference** em um recurso implantado condicionalmente, a função será avaliada mesmo que o recurso não seja implantado.  Você receberá um erro se a função **reference** se referir a um recurso que não existe. Use a função **if** para garantir que a função seja avaliada apenas quando o recurso estiver sendo implantado. Consulte a [função if](template-functions-logical.md#if) para um modelo de exemplo que usa if e reference com um recurso implantado condicionalmente.

### <a name="implicit-dependency"></a>Dependência implícita

Usando a função de referência, você implicitamente declarar que um recurso depende de outro recurso se o recurso referenciado é provisionado no mesmo modelo e consulte o recurso pelo seu nome (não a ID de recurso). Você não precisa usar a propriedade dependsOn também. A função não é avaliada até que o recurso referenciado conclua a implantação.

### <a name="resource-name-or-identifier"></a>Identificador ou nome do recurso

Ao fazer referência a um recurso implantado no mesmo modelo, forneça o nome do recurso.

# <a name="json"></a>[JSON](#tab/json)

```json
"value": "[reference(parameters('storageAccountName'))]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
value: reference(storageAccountName)
```

---

Ao fazer referência a um recurso que não está implantado no mesmo modelo, forneça a ID de recurso e `apiVersion`.

# <a name="json"></a>[JSON](#tab/json)

```json
"value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2018-07-01')]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
value: reference(resourceId(storageResourceGroup, 'Microsoft.Storage/storageAccounts', storageAccountName), '2018-07-01')]"
```

---

Para evitar ambiguidade sobre qual recurso você está referenciando, forneça um identificador de recurso totalmente qualificado.

# <a name="json"></a>[JSON](#tab/json)

```json
"value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', parameters('ipAddressName')))]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
value: reference(resourceId('Microsoft.Network/publicIPAddresses', ipAddressName))
```

---

Ao construir uma referência totalmente qualificada a um recurso, a ordem para combinação dos segmentos do tipo e do nome não é simplesmente uma concatenação dos dois. Em vez disso, após o namespace, use uma sequência de pares *tipo/nome* do menos específico para o mais específico:

**{resource-provider-namespace}/{parent-resource-type}/{parent-resource-name}[/{child-resource-type}/{child-resource-name}]**

Por exemplo:

`Microsoft.Compute/virtualMachines/myVM/extensions/myExt`está correto, `Microsoft.Compute/virtualMachines/extensions/myVM/myExt` não está correto

Para simplificar a criação de qualquer ID de recurso, use as funções `resourceId()` descritas neste documento, em vez da função `concat()`.

### <a name="get-managed-identity"></a>Obter identidade gerenciada

As [identidades gerenciadas para recursos do Azure](../../active-directory/managed-identities-azure-resources/overview.md) são [tipos de recursos de extensão](../management/extension-resource-types.md) criados implicitamente para alguns recursos. Como a identidade gerenciada não é definida explicitamente no modelo, você deve referenciar o recurso ao qual a identidade é aplicada. Use `Full` para obter todas as propriedades, incluindo a identidade criada implicitamente.

O padrão é:

`"[reference(resourceId(<resource-provider-namespace>, <resource-name>, <API-version>, 'Full').Identity.propertyName]"`

Por exemplo, para obter a ID da entidade de segurança para uma identidade gerenciada que é aplicada a uma máquina virtual, use:

# <a name="json"></a>[JSON](#tab/json)

```json
"[reference(resourceId('Microsoft.Compute/virtualMachines', variables('vmName')),'2019-12-01', 'Full').identity.principalId]",
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
reference(resourceId('Microsoft.Compute/virtualMachines', vmName),'2019-12-01', 'Full').identity.principalId
```

---

Ou, para obter a ID de locatário para uma identidade gerenciada que é aplicada a um conjunto de dimensionamento de máquinas virtuais, use:

# <a name="json"></a>[JSON](#tab/json)

```json
"[reference(resourceId('Microsoft.Compute/virtualMachineScaleSets',  variables('vmNodeType0Name')), 2019-12-01, 'Full').Identity.tenantId]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
reference(resourceId('Microsoft.Compute/virtualMachineScaleSets',  vmNodeType0Name), 2019-12-01, 'Full').Identity.tenantId
```

---

### <a name="reference-example"></a>Exemplo de referência

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/referencewithstorage.json) a seguir implanta um recurso e faz referência a esse recurso.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountName": {
      "type": "string"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-12-01",
      "name": "[parameters('storageAccountName')]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "tags": {},
      "properties": {
      }
    }
  ],
  "outputs": {
      "referenceOutput": {
        "type": "object",
        "value": "[reference(parameters('storageAccountName'))]"
      },
      "fullReferenceOutput": {
        "type": "object",
        "value": "[reference(parameters('storageAccountName'), '2016-12-01', 'Full')]"
      }
    }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param storageAccountName string

resource myStorage 'Microsoft.Storage/storageAccounts@2016-12-01' = {
  name: storageAccountName
  location: resourceGroup().location
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
  tags: {}
  properties: {}
}

output referenceOutput object = reference(storageAccountName)
output fullReferenceOutput object = reference(storageAccountName, '2016-12-01', 'Full')
```

---

O exemplo anterior retorna os dois objetos. O objeto de propriedades tem o seguinte formato:

```json
{
   "creationTime": "2017-10-09T18:55:40.5863736Z",
   "primaryEndpoints": {
     "blob": "https://examplestorage.blob.core.windows.net/",
     "file": "https://examplestorage.file.core.windows.net/",
     "queue": "https://examplestorage.queue.core.windows.net/",
     "table": "https://examplestorage.table.core.windows.net/"
   },
   "primaryLocation": "southcentralus",
   "provisioningState": "Succeeded",
   "statusOfPrimary": "available",
   "supportsHttpsTrafficOnly": false
}
```

O objeto completo tem o seguinte formato:

```json
{
  "apiVersion":"2016-12-01",
  "location":"southcentralus",
  "sku": {
    "name":"Standard_LRS",
    "tier":"Standard"
  },
  "tags":{},
  "kind":"Storage",
  "properties": {
    "creationTime":"2017-10-09T18:55:40.5863736Z",
    "primaryEndpoints": {
      "blob":"https://examplestorage.blob.core.windows.net/",
      "file":"https://examplestorage.file.core.windows.net/",
      "queue":"https://examplestorage.queue.core.windows.net/",
      "table":"https://examplestorage.table.core.windows.net/"
    },
    "primaryLocation":"southcentralus",
    "provisioningState":"Succeeded",
    "statusOfPrimary":"available",
    "supportsHttpsTrafficOnly":false
  },
  "subscriptionId":"<subscription-id>",
  "resourceGroupName":"functionexamplegroup",
  "resourceId":"Microsoft.Storage/storageAccounts/examplestorage",
  "referenceApiVersion":"2016-12-01",
  "condition":true,
  "isConditionTrue":true,
  "isTemplateResource":false,
  "isAction":false,
  "provisioningOperation":"Read"
}
```

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/reference.json) a seguir faz referência a uma conta de armazenamento que não está implantada nesse modelo. A conta de armazenamento já existe na mesma assinatura.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageResourceGroup": {
      "type": "string"
    },
    "storageAccountName": {
      "type": "string"
    }
  },
  "resources": [],
  "outputs": {
    "ExistingStorage": {
      "type": "object",
      "value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2018-07-01')]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param storageResourceGroup string
param storageAccountName string

output ExistingStorage object = reference(resourceId(storageAccountName), 'Microsoft.Storage/storageAccounts', storageAccountName), '2018-07-01')
```

---

## <a name="resourcegroup"></a>resourceGroup

`resourceGroup()`

Retorna um objeto que representa o grupo de recursos atual.

### <a name="return-value"></a>Valor retornado

O objeto retornado está no seguinte formato:

```json
{
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
  "name": "{resourceGroupName}",
  "type":"Microsoft.Resources/resourceGroups",
  "location": "{resourceGroupLocation}",
  "managedBy": "{identifier-of-managing-resource}",
  "tags": {
  },
  "properties": {
    "provisioningState": "{status}"
  }
}
```

A propriedade **managedBy** é retornada somente para grupos de recursos que contêm recursos gerenciados por outro serviço. Para aplicativos gerenciados, databricks e AKS, o valor da propriedade é a ID de recurso do recurso de gerenciamento.

### <a name="remarks"></a>Comentários

A função `resourceGroup()` não pode ser usada em um modelo que está [implantado no nível da assinatura](deploy-to-subscription.md). Ela só pode ser usada em modelos que são implantados em um grupo de recursos. Você pode usar a função `resourceGroup()` em um [modelo vinculado ou aninhado (com escopo interno)](linked-templates.md) que se destina a um grupo de recursos, mesmo quando o modelo pai é implantado na assinatura. Nesse cenário, o modelo vinculado ou aninhado é implantado no nível do grupo de recursos. Para obter mais informações sobre como direcionar um grupo de recursos em uma implantação em nível de assinatura, confira [implantar recursos do Azure em mais de uma assinatura ou grupo de recursos](./deploy-to-resource-group.md).

Um uso comum da função resourceGroup é criar recursos no mesmo local que o grupo de recursos. O exemplo a seguir usa o local do grupo de recursos para um valor de parâmetro padrão.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "location": {
    "type": "string",
    "defaultValue": "[resourceGroup().location]"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param location string = resourceGroup().location
```

---

Você também pode usar a função resourceGroup para aplicar marcas do grupo de recursos a um recurso. Para obter mais informações, confira [Aplicar marcas do grupo de recursos](../management/tag-resources.md#apply-tags-from-resource-group).

Ao usar modelos aninhados para implantar em vários grupos de recursos, você pode especificar o escopo para avaliar a função resourceGroup. Para saber mais, consulte [Implantar recursos do Azure em mais de uma assinatura ou grupo de recursos](./deploy-to-resource-group.md).

### <a name="resource-group-example"></a>Exemplo de grupo de recursos

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/resourcegroup.json) a seguir retorna as propriedades do grupo de recursos.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "resourceGroupOutput": {
      "type" : "object",
      "value": "[resourceGroup()]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output resourceGroupOutput object = resourceGroup()
```

---

O exemplo anterior retorna um objeto no seguinte formato:

```json
{
  "id": "/subscriptions/{subscription-id}/resourceGroups/examplegroup",
  "name": "examplegroup",
  "type":"Microsoft.Resources/resourceGroups",
  "location": "southcentralus",
  "properties": {
    "provisioningState": "Succeeded"
  }
}
```

## <a name="resourceid"></a>resourceId

`resourceId([subscriptionId], [resourceGroupName], resourceType, resourceName1, [resourceName2], ...)`

Retorna o identificador exclusivo de um recurso. Você pode usar essa função quando o nome do recurso é ambíguo ou não provisionado no mesmo modelo. O formato do identificador retornado varia dependendo de se a implantação ocorre no escopo de um grupo de recursos, de assinatura, de um grupo de gerenciamento ou do locatário.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Type | Descrição |
|:--- |:--- |:--- |:--- |
| subscriptionId |Não |string (no formato GUID) |O valor padrão é a assinatura atual. Especifique esse valor quando você precisar recuperar um recurso em outra assinatura. Forneça esse valor apenas ao implantar no escopo de um grupo de recursos ou assinatura. |
| resourceGroupName |Não |string |O valor padrão é o grupo de recursos atual. Especifique esse valor quando você precisar recuperar um recurso em outro grupo de recursos. Forneça esse valor apenas ao implantar no escopo de um grupo de recursos. |
| resourceType |Sim |string |Tipo de recurso, incluindo o namespace do provedor de recursos. |
| resourceName1 |Sim |string |Nome do recurso. |
| resourceName2 |Não |string |Próximo segmento de nome de recurso, se necessário. |

Continue adicionando nomes de recursos como parâmetros quando o tipo de recurso incluir mais segmentos.

### <a name="return-value"></a>Valor retornado

Quando o modelo é implantado no escopo de um grupo de recursos, a ID do recurso é retornada no seguinte formato:

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

Você pode usar a função ResourceId para outros escopos de implantação, mas o formato da ID é alterado.

Se você usar ResourceId durante a implantação em uma assinatura, a ID do recurso será retornada no seguinte formato:

```json
/subscriptions/{subscriptionId}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

Se você usar ResourceId durante a implantação em um grupo de gerenciamento ou locatário, a ID do recurso será retornada no seguinte formato:

```json
/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

Para evitar confusão, recomendamos que você não use ResourceId ao trabalhar com recursos implantados na assinatura, no grupo de gerenciamento ou no locatário. Em vez disso, use a função ID que foi projetada para o escopo.

Para [recursos de nível de assinatura](deploy-to-subscription.md), use a função [subscriptionResourceId](#subscriptionresourceid) .

Para [recursos de nível de grupo de gerenciamento](deploy-to-management-group.md), use a função [extensionResourceId](#extensionresourceid) para fazer referência a um recurso que é implementado como uma extensão de um grupo de gerenciamento. Por exemplo, as definições de política personalizadas que são implantadas em um grupo de gerenciamento são extensões do grupo de gerenciamento. Use a função [tenantResourceId](#tenantresourceid) para fazer referência a recursos que são implantados no locatário, mas disponíveis no seu grupo de gerenciamento. Por exemplo, as definições de política internas são implementadas como recursos de nível de locatário.

Para [recursos de nível de locatário](deploy-to-tenant.md), use a função [tenantResourceId](#tenantresourceid) . Use tenantResourceId para definições de política internas porque elas são implementadas no nível do locatário.

### <a name="remarks"></a>Comentários

O número de parâmetros que você fornece varia dependendo de o recurso ser pai ou filho e de estar na mesma assinatura ou grupo de recursos.

Para obter a ID de recurso de um recurso pai na mesma assinatura e grupo de recursos, forneça o tipo e o nome do recurso.

# <a name="json"></a>[JSON](#tab/json)

```json
"[resourceId('Microsoft.ServiceBus/namespaces', 'namespace1')]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resourceId('Microsoft.ServiceBus/namespaces', 'namespace1')
```

---

Para obter a ID de recurso de um recurso filho, preste atenção ao número de segmentos no tipo de recurso. Forneça um nome de recurso para cada segmento do tipo de recurso. O nome do segmento corresponde ao recurso que existe para essa parte da hierarquia.

# <a name="json"></a>[JSON](#tab/json)

```json
"[resourceId('Microsoft.ServiceBus/namespaces/queues/authorizationRules', 'namespace1', 'queue1', 'auth1')]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resourceId('Microsoft.ServiceBus/namespaces/queues/authorizationRules', 'namespace1', 'queue1', 'auth1')
```

---

Para obter a ID de recurso de um recurso na mesma assinatura, mas em um grupo de recursos diferente, forneça o nome do grupo de recursos.

# <a name="json"></a>[JSON](#tab/json)

```json
"[resourceId('otherResourceGroup', 'Microsoft.Storage/storageAccounts', 'examplestorage')]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resourceId('otherResourceGroup', 'Microsoft.Storage/storageAccounts', 'examplestorage')
```

---

Para obter a ID de recurso de um recurso em uma assinatura e um grupo de recursos diferentes, forneça a ID da assinatura e o nome do grupo de recursos.

# <a name="json"></a>[JSON](#tab/json)

```json
"[resourceId('xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', 'otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resourceId('xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', 'otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')
```

---

Frequentemente, você precisa usar essa função ao usar uma conta de armazenamento ou rede virtual em um grupo de recursos alternativo. O exemplo a seguir mostra como um recurso de um grupo de recursos externo pode ser facilmente usado:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string"
    },
    "virtualNetworkName": {
      "type": "string"
    },
    "virtualNetworkResourceGroup": {
      "type": "string"
    },
    "subnet1Name": {
      "type": "string"
    },
    "nicName": {
      "type": "string"
    }
  },
  "variables": {
    "subnet1Ref": "[resourceId(parameters('virtualNetworkResourceGroup'), 'Microsoft.Network/virtualNetworks/subnets', parameters('virtualNetworkName'), parameters('subnet1Name'))]"
  },
  "resources": [
    {
      "type": "Microsoft.Network/networkInterfaces",
      "apiVersion": "2015-05-01-preview",
      "name": "[parameters('nicName')]",
      "location": "[parameters('location')]",
      "properties": {
        "ipConfigurations": [
          {
            "name": "ipconfig1",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
              "subnet": {
                "id": "[variables('subnet1Ref')]"
              }
            }
          }
        ]
      }
    }
  ]
}
```
# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param location string
param virtualNetworkName string
param virtualNetworkResourceGroup string
param subnet1Name string
param nicName string

var subnet1Ref = resourceId('virtualNetworkResourceGroup', 'Microsoft.Network/virtualNetworks/subnets', 'virtualNetworkName', 'subnet1Name')

resource myInterface 'Microsoft.Network/networkInterfaces@2015-05-01-preview' = {
  name: nicName
  location: location
  properties: {
    ipConfigurations: [
      {
        name: 'ipconfig1'
        properties: {
          privateIPAllocationMethod: 'Dynamic'
          subnet: {
            id: subnet1Ref
          }
        }
      }
    ]
  }
}
```

---


### <a name="resource-id-example"></a>Exemplo de ID de recurso

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/resourceid.json) a seguir retorna a ID de recurso de uma conta de armazenamento no grupo de recursos:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "sameRGOutput": {
      "type": "string",
      "value": "[resourceId('Microsoft.Storage/storageAccounts','examplestorage')]"
    },
    "differentRGOutput": {
      "type": "string",
      "value": "[resourceId('otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]"
    },
    "differentSubOutput": {
      "type": "string",
      "value": "[resourceId('11111111-1111-1111-1111-111111111111', 'otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]"
    },
    "nestedResourceOutput": {
      "type": "string",
      "value": "[resourceId('Microsoft.SQL/servers/databases', 'serverName', 'databaseName')]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output sameRGOutput string = resourceId('Microsoft.Storage/storageAccounts','examplestorage')
output differentRGOutput string = resourceId('otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')
output differentSubOutput string = resourceId('11111111-1111-1111-1111-111111111111', 'otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')
output nestedResourceOutput string = resourceId('Microsoft.SQL/servers/databases', 'serverName', 'databaseName')
```

---

A saída do exemplo anterior com os valores padrão é:

| Nome | Tipo | Valor |
| ---- | ---- | ----- |
| sameRGOutput | String | /subscriptions/{current-sub-id}/resourceGroups/examplegroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| differentRGOutput | String | /subscriptions/{current-sub-id}/resourceGroups/otherResourceGroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| differentSubOutput | String | /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/otherResourceGroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| nestedResourceOutput | String | /subscriptions/{current-sub-id}/resourceGroups/examplegroup/providers/Microsoft.SQL/servers/serverName/databases/databaseName |

## <a name="subscription"></a>subscription

`subscription()`

Retorna detalhes sobre a assinatura da implantação atual.

### <a name="return-value"></a>Valor retornado

A função retorna o seguinte formato:

```json
{
  "id": "/subscriptions/{subscription-id}",
  "subscriptionId": "{subscription-id}",
  "tenantId": "{tenant-id}",
  "displayName": "{name-of-subscription}"
}
```

### <a name="remarks"></a>Comentários

Ao usar modelos aninhados para implantar em várias assinaturas, você pode especificar o escopo para avaliar a função de assinatura. Para saber mais, consulte [Implantar recursos do Azure em mais de uma assinatura ou grupo de recursos](./deploy-to-resource-group.md).

### <a name="subscription-example"></a>Exemplo de assinatura

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/subscription.json) a seguir mostra a função de assinatura chamada na seção de saídas.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "subscriptionOutput": {
      "value": "[subscription()]",
      "type" : "object"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output subscriptionOutput object = subscription()
```

---

## <a name="subscriptionresourceid"></a>subscriptionResourceId

`subscriptionResourceId([subscriptionId], resourceType, resourceName1, [resourceName2], ...)`

Retorna o identificador exclusivo de um recurso implantado no nível da assinatura.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Type | Descrição |
|:--- |:--- |:--- |:--- |
| subscriptionId |Não |string (no formato GUID) |O valor padrão é a assinatura atual. Especifique esse valor quando você precisar recuperar um recurso em outra assinatura. |
| resourceType |Sim |string |Tipo de recurso, incluindo o namespace do provedor de recursos. |
| resourceName1 |Sim |string |Nome do recurso. |
| resourceName2 |Não |string |Próximo segmento de nome de recurso, se necessário. |

Continue adicionando nomes de recursos como parâmetros quando o tipo de recurso incluir mais segmentos.

### <a name="return-value"></a>Valor retornado

O identificador é retornado no seguinte formato:

```json
/subscriptions/{subscriptionId}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

### <a name="remarks"></a>Comentários

Use essa função para obter a ID de recurso dos recursos que são [implantados na assinatura](deploy-to-subscription.md), em vez de um grupo de recursos. A ID retornada difere do valor retornado pela função [resourceId](#resourceid) ao não incluir um valor de grupo de recursos.

### <a name="subscriptionresourceid-example"></a>Exemplo de subscriptionResourceID

O modelo a seguir atribui uma função interna. Você pode implantá-lo em um grupo de recursos ou assinatura. Ele usa a função subscriptionResourceId para obter a ID de recurso para funções internas.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "principalId": {
      "type": "string",
      "metadata": {
        "description": "The principal to assign the role to"
      }
    },
    "builtInRoleType": {
      "type": "string",
      "allowedValues": [
        "Owner",
        "Contributor",
        "Reader"
      ],
      "metadata": {
        "description": "Built-in role to assign"
      }
    },
    "roleNameGuid": {
      "type": "string",
      "defaultValue": "[newGuid()]",
      "metadata": {
        "description": "A new GUID used to identify the role assignment"
      }
    }
  },
  "variables": {
    "Owner": "[subscriptionResourceId('Microsoft.Authorization/roleDefinitions', '8e3af657-a8ff-443c-a75c-2fe8c4bcb635')]",
    "Contributor": "[subscriptionResourceId('Microsoft.Authorization/roleDefinitions', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
    "Reader": "[subscriptionResourceId('Microsoft.Authorization/roleDefinitions', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
  },
  "resources": [
    {
      "type": "Microsoft.Authorization/roleAssignments",
      "apiVersion": "2018-09-01-preview",
      "name": "[parameters('roleNameGuid')]",
      "properties": {
        "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
        "principalId": "[parameters('principalId')]"
      }
    }
  ]
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param principalId string {
  metadata: {
    'description': 'principalId'
  }
}
param builtInRoleType string {
  'allowed': [
    'Owner'
    'Contributor'
    'Reader'
  ]
  'metadata': {
      'description': 'Built-in role to assign'
  }
}
param roleNameGuid string {
  default: newGuid()
  metadata: {
    'description': 'A new GUID used to identify the role assignment'
  }
}

var roleDefinitionId = {
  Owner: {
    id: subscriptionResourceId('Microsoft.Authorization/roleDefinitions', '8e3af657-a8ff-443c-a75c-2fe8c4bcb635')
  }
  Contributor: {
    id: subscriptionResourceId('Microsoft.Authorization/roleDefinitions', 'b24988ac-6180-42a0-ab88-20f7382dd24c')
  }
  Reader: {
    id: subscriptionResourceId('Microsoft.Authorization/roleDefinitions', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')
  }
}

resource myRoleAssignment 'Microsoft.Authorization/roleAssignments@2018-09-01-preview' = {
  name: roleNameGuid
  properties: {
    roleDefinitionId: roleDefinitionId[builtInRoleType].id
    principalId: principalId
  }
}
```

---

## <a name="tenantresourceid"></a>tenantResourceId

`tenantResourceId(resourceType, resourceName1, [resourceName2], ...)`

Retorna o identificador exclusivo de um recurso implantado no nível do locatário.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Type | Descrição |
|:--- |:--- |:--- |:--- |
| resourceType |Sim |string |Tipo de recurso, incluindo o namespace do provedor de recursos. |
| resourceName1 |Sim |string |Nome do recurso. |
| resourceName2 |Não |string |Próximo segmento de nome de recurso, se necessário. |

Continue adicionando nomes de recursos como parâmetros quando o tipo de recurso incluir mais segmentos.

### <a name="return-value"></a>Valor retornado

O identificador é retornado no seguinte formato:

```json
/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

### <a name="remarks"></a>Comentários

Você usa essa função para obter a ID do recurso para um recurso que é implantado no locatário. A ID retornada difere dos valores retornados por outras funções de ID de recurso ao não incluir os valores de grupo de recursos ou de assinatura.

### <a name="tenantresourceid-example"></a>exemplo de tenantResourceId

As definições de política internas são recursos de nível de locatário. Para implantar uma atribuição de política que faz referência a uma definição de política interna, use a função tenantResourceId.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "policyDefinitionID": {
      "type": "string",
      "defaultValue": "0a914e76-4921-4c19-b460-a2d36003525a",
      "metadata": {
        "description": "Specifies the ID of the policy definition or policy set definition being assigned."
      }
    },
    "policyAssignmentName": {
      "type": "string",
      "defaultValue": "[guid(parameters('policyDefinitionID'), resourceGroup().name)]",
      "metadata": {
        "description": "Specifies the name of the policy assignment, can be used defined or an idempotent name as the defaultValue provides."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Authorization/policyAssignments",
      "name": "[parameters('policyAssignmentName')]",
      "apiVersion": "2019-09-01",
      "properties": {
        "scope": "[subscriptionResourceId('Microsoft.Resources/resourceGroups', resourceGroup().name)]",
        "policyDefinitionId": "[tenantResourceId('Microsoft.Authorization/policyDefinitions', parameters('policyDefinitionID'))]"
      }
    }
  ]
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param policyDefinitionID string{
  default: '0a914e76-4921-4c19-b460-a2d36003525a'
  metadata: {
    'description': 'Specifies the ID of the policy definition or policy set definition being assigned.'
  }
}

param policyAssignmentName string {
  default: guid(policyDefinitionID, resourceGroup().name)
  metadata: {
    'description': 'Specifies the name of the policy assignment, can be used defined or an idempotent name as the defaultValue provides.'
  }
}

resource myPolicyAssignment 'Microsoft.Authorization/policyAssignments@2019-09-01' = {
  name: policyAssignmentName
  properties: {
    scope: subscriptionResourceId('Microsoft.Resources/resourceGroups', resourceGroup().name)
    policyDefinitionId: tenantResourceId('Microsoft.Authorization/policyDefinitions', policyDefinitionID)
  }
}
```

---

## <a name="next-steps"></a>Próximas etapas

* Para obter uma descrição das seções em um modelo do ARM, consulte [entender a estrutura e a sintaxe de modelos do ARM](template-syntax.md).
* Para mesclar vários modelos, consulte [usando modelos vinculados e aninhados ao implantar recursos do Azure](linked-templates.md).
* Para iterar um número especificado de vezes ao criar um tipo de recurso, consulte [iteração de recurso em modelos ARM](copy-resources.md).
* Para ver como implantar o modelo que você criou, consulte [implantar recursos com modelos ARM e Azure PowerShell](deploy-powershell.md).
