---
title: Armazenamento confiável dos Serviços de Mídia do Azure
description: Neste tutorial, você aprenderá a habilitar o armazenamento confiável nos Serviços de Mídia do Azure, usar Identidades Gerenciadas para armazenamento confiável e dar acesso aos Serviços do Azure a uma conta de armazenamento ao usar um firewall ou VPN.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: tutorial
ms.date: 2/8/2021
ms.openlocfilehash: f076381333457c9ba2fd4325fa8aa7baad5d8a5b
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106281057"
---
# <a name="tutorial-media-services-trusted-storage"></a>Tutorial: Armazenamento confiável dos Serviços de Mídia

Neste tutorial, você aprenderá:

> [!div class="checklist"]
> - Como habilitar o armazenamento confiável nos Serviços de Mídia do Azure
> - Como usar as Identidades Gerenciadas para o armazenamento confiável
> - Como conceder aos Serviços do Azure acesso a uma conta de armazenamento ao usar um controle de acesso à rede, como um firewall ou uma VPN

Com a API 2020-05-01, você pode habilitar o armazenamento confiável associando uma Identidade Gerenciada a uma conta de Serviços de Mídia.

>[!NOTE]
>O armazenamento confiável só está disponível na API e, no momento, não está habilitado no portal do Azure.

Os Serviços de Mídia podem acessar automaticamente a sua conta de armazenamento usando a autenticação do sistema. Os Serviços de Mídia validam que a conta de Serviços de Mídia e a conta de armazenamento estão na mesma assinatura. Eles também validam que o usuário que está adicionando a associação tenha acesso à conta de armazenamento com o RBAC do Azure Resource Manager.

No entanto, se você quiser usar o controle de acesso à rede para proteger a sua conta de armazenamento e habilitar o armazenamento confiável, a autenticação das [Identidades Gerenciadas](concept-managed-identities.md) será necessária. Ela permite que os Serviços de Mídia acessem a conta de armazenamento que foi configurada com um firewall ou uma restrição de VNet por meio do acesso ao armazenamento confiável.

## <a name="overview"></a>Visão geral

> [!IMPORTANT]
> Use a API 2020-05-01 em todas as solicitações para os Serviços de Mídia.

Estas são as etapas gerais para criar um armazenamento confiável para os Serviços de Mídia:

1. Crie um grupos de recursos.
1. Criar uma conta de armazenamento.
1. Pesquise a conta de armazenamento até que ela esteja pronta. Quando a conta de armazenamento estiver pronta, a solicitação retornará a ID da entidade de serviço.
1. Localize a ID da função *Colaborador de Dados do Blob de Armazenamento*.
1. Chame o provedor de autorização e adicione uma atribuição de função.
1. Atualize a conta dos serviços de mídia para se autenticar à conta de armazenamento usando a Identidade Gerenciada.
1. Exclua os recursos se você não quiser continuar a usá-los nem ser cobrado por eles.

<!-- Link to storage role contributor role access differences information -->

## <a name="prerequisites"></a>Pré-requisitos

É necessária uma assinatura do Azure para começar.  Se você não tiver uma assinatura do Azure, [crie uma conta de avaliação gratuita](https://azure.microsoft.com/free/).

### <a name="get-your-tenant-id-and-subscription-id"></a>Obter a ID de locatário e a ID de assinatura

Se você não souber como obter a sua ID de locatário e a ID de assinatura, confira [Como localizar a sua ID de locatário](setup-azure-tenant-how-to.md) e [Localizar a sua ID de locatário](setup-azure-tenant-how-to.md).

### <a name="create-a-service-principal-and-secret"></a>Criar uma entidade de serviço e um segredo

Se você não souber como criar uma entidade de serviço e um segredo, confira [Obter credenciais para acessar a API dos Serviços de Mídia](access-api-howto.md).

## <a name="use-a-rest-client"></a>Usar um cliente REST

Esse script destina-se ao uso com um cliente REST, como o que está disponível nas extensões do Visual Studio Code.  Adapte-o para o seu ambiente de desenvolvimento.

## <a name="set-initial-variables"></a>Definir variáveis iniciais

Esta parte do script foi projetada para uso em um cliente REST. Você pode usar as variáveis de modo diferente no seu ambiente de desenvolvimento.

```rest
### AAD details
@tenantId = your tenant ID
@servicePrincipalId = the service principal ID
@servicePrincipalSecret = the service principal secret

### AAD resources
@armResource = https%3A%2F%2Fmanagement.core.windows.net%2F
@graphResource = https%3A%2F%2Fgraph.windows.net%2F
@storageResource = https%3A%2F%2Fstorage.azure.com%2F

### Service endpoints
@armEndpoint = management.azure.com
@graphEndpoint = graph.windows.net
@aadEndpoint = login.microsoftonline.com

### ARM details
@subscription = your subscription id
@resourceGroup = the resource group you'll be creating
@storageName = the name of the storage you'll be creating
@accountName = the name of the account you'll be creating
@resourceLocation = East US (or the location that works best for your region)
```

## <a name="get-a-token-for-azure-resource-manager"></a>Obter um token para o Azure Resource Manager

```rest
// @name getArmToken
POST https://{{aadEndpoint}}/{{tenantId}}/oauth2/token
Accept: application/json
Content-Type: application/x-www-form-urlencoded

resource={{armResource}}&client_id={{servicePrincipalId}}&client_secret={{servicePrincipalSecret}}&grant_type=client_credentials
```

## <a name="get-a-token-for-the-graph-api"></a>Obter um token para a API do Graph

Esta parte do script foi projetada para uso em um cliente REST. Você pode usar as variáveis de modo diferente no seu ambiente de desenvolvimento.

```rest
// @name getGraphToken
POST https://{{aadEndpoint}}/{{tenantId}}/oauth2/token
Accept: application/json
Content-Type: application/x-www-form-urlencoded

resource={{graphResource}}&client_id={{servicePrincipalId}}&client_secret={{servicePrincipalSecret}}&grant_type=client_credentials
```

## <a name="get-the-service-principal-details"></a>Obter os detalhes da entidade de serviço

```rest
// @name getServicePrincipals
GET https://{{graphEndpoint}}/{{tenantId}}/servicePrincipals?$filter=appId%20eq%20'{{servicePrincipalId}}'&api-version=1.6
x-ms-client-request-id: cae3e4f7-17a0-476a-a05a-0dab934ba959
Authorization:  Bearer {{getGraphToken.response.body.access_token}}
```

## <a name="store-the-service-principal-id"></a>Armazenar a ID da entidade de serviço

```rest
@servicePrincipalObjectId = {{getServicePrincipals.response.body.value[0].objectId}}
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

```rest
// @name createResourceGroup
PUT https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}
    ?api-version=2016-09-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
    "location": "{{resourceLocation}}"
}
```

## <a name="create-storage-account"></a>Criar Conta de Armazenamento

```rest
// @name createStorageAccount
PUT https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}
    ?api-version=2019-06-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
    "sku": {
    "name": "Standard_GRS"
    },
    "kind": "StorageV2",
    "location": "{{resourceLocation}}",
    "properties": {
    }
}
```

## <a name="get-the-storage-account-status"></a>Obter o status da conta de armazenamento

A conta de armazenamento levará algum tempo para ficar pronta, portanto esta solicitação pesquisa o status dela.  Repita esta solicitação até que a conta de armazenamento esteja pronta.

```rest
// @name getStorageAccountStatus
GET {{createStorageAccount.response.headers.Location}}
Authorization: Bearer {{getArmToken.response.body.access_token}}
```

## <a name="get-the-storage-account-details"></a>Obter os detalhes da conta de armazenamento

Quando a conta de armazenamento estiver pronta, obtenha as propriedades da conta de armazenamento.

```rest
// @name getStorageAccount
GET https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}
    ?api-version=2019-06-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
```

## <a name="get-a-token-for-arm"></a>Obter um token para o ARM

```rest
// @name getStorageToken
POST https://{{aadEndpoint}}/{{tenantId}}/oauth2/token
Accept: application/json
Content-Type: application/x-www-form-urlencoded

resource={{storageResource}}&client_id={{servicePrincipalId}}&client_secret={{servicePrincipalSecret}}&grant_type=client_credentials
```

## <a name="create-a-media-services-account-with-a-system-assigned-managed-identity"></a>Criar uma conta dos Serviços de Mídia com uma identidade gerenciada atribuída pelo sistema

```rest
// @name createMediaServicesAccount
PUT https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Media/mediaservices/{{accountName}}?api-version=2020-05-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
  "identity": {
      "type": "SystemAssigned"
  },
  "properties": {
    "storageAccounts": [
      {
        "id": "{{getStorageAccountStatus.response.body.id}}"
      }
    ],
    "encryption": {
      "type": "SystemKey"
    }
  },
  "location": "{{resourceLocation}}"
}
```

## <a name="get-the-storage-storage-blob-data-role-definition"></a>Obter a definição da função de Dados de Blob de Armazenamento do armazenamento

```rest
// @name getStorageBlobDataContributorRoleDefinition
GET https://management.azure.com/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}/providers/Microsoft.Authorization/roleDefinitions?$filter=roleName%20eq%20%27Storage%20Blob%20Data%20Contributor%27&api-version=2015-07-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
```

### <a name="set-the-storage-role-assignment"></a>Definir a atribuição de função do armazenamento

A atribuição de função diz que a entidade de serviço da conta dos Serviços de Mídia tem a função de armazenamento *Colaborador de Dados de Blob de Armazenamento*.  Isso pode demorar um pouco e é importante aguardar ou a conta dos Serviços de Mídia não será configurada corretamente.

```rest
PUT https://management.azure.com/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}/providers/Microsoft.Authorization/roleAssignments/{{$guid}}?api-version=2020-04-01-preview
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "roleDefinitionId": "/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}/providers/Microsoft.Authorization/roleDefinitions/{{getStorageBlobDataContributorRoleDefinition.response.body.value[0].name}}",
    "principalId": "{{createMediaServicesAccount.response.body.identity.principalId}}"
  }
}
```

## <a name="give-managed-identity-bypass-access-to-the-storage-account"></a>Conceder o acesso de bypass da Identidade Gerenciada à conta de armazenamento

Essa ação altera o acesso da identidade gerenciada pelo sistema para a Identidade Gerenciada. Dessa forma, a conta de armazenamento pode acessar a conta de armazenamento por meio de um firewall, pois os serviços do Azure podem acessar a conta de armazenamento independentemente das regras de acesso de IP (ACLs).

Novamente, aguarde até que a função tenha sido atribuída na conta de armazenamento ou a conta dos Serviços de Mídia será configurada incorretamente.

```rest
// @name setStorageAccountFirewall
PUT https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}
    ?api-version=2019-06-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
    "sku": {
    "name": "Standard_GRS"
    },
    "kind": "StorageV2",
    "location": "{{resourceLocation}}",
    "properties": {
      "minimumTlsVersion": "TLS1_2",
      "networkAcls": {
        "bypass": "AzureServices",
        "virtualNetworkRules": [],
        "ipRules": [],
        "defaultAction": "Deny"
      }
    }
}
```

## <a name="update-the-media-services-account-to-use-the-managed-identity"></a>Atualizar a conta dos Serviços de Mídia para usar a Identidade Gerenciada

Essa solicitação pode precisar ser repetida algumas vezes, uma vez que a atribuição da função de armazenamento pode levar alguns minutos para ser propagada.

```rest
// @name updateMediaServicesAccountWithManagedStorageAuth
PUT https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Media/mediaservices/{{accountName}}?api-version=2020-05-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
  "identity": {
      "type": "SystemAssigned"
  },
  "properties": {
    "storageAccounts": [
      {
        "id": "{{getStorageAccountStatus.response.body.id}}"
      }
    ],
    "storageAuthentication": "ManagedIdentity",
    "encryption": {
      "type": "SystemKey"
    }
  },
  "location": "{{resourceLocation}}"
}
```

## <a name="test-access"></a>Testar o acesso

Teste o acesso criando um ativo na conta de armazenamento.

```rest
// @name createAsset
PUT https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Media/mediaservices/{{accountName}}/assets/testasset{{index}}withoutmi?api-version=2018-07-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
}
```

## <a name="delete-resources"></a>Excluir recursos

Se você não quiser manter os recursos que criou e continuar a ser cobrado por eles, exclua-os.

```rest
### Clean up the Storage account
DELETE https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}
    ?api-version=2019-06-01
Authorization: Bearer {{getArmToken.response.body.access_token}}

### Clean up the Media Services account
DELETE https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Media/mediaservices/{{accountName}}?api-version=2020-05-01
Authorization: Bearer {{getArmToken.response.body.access_token}}

### Clean up the Media Services account
GET https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Media/mediaservices/{{accountName}}?api-version=2020-05-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
```

## <a name="next-steps"></a>Próximas etapas

[Como criar um Ativo](asset-create-asset-how-to.md)
