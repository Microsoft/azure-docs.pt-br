---
title: Entender o esquema e as expressões personalizadas do Azure AD
description: Este artigo descreve o esquema do Azure AD, os atributos que o agente de provisionamento flui e expressões personalizadas.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/18/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 750bc2f4f535238dbb757afc8b615fa71063d74d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98613072"
---
# <a name="understand-the-azure-ad-schema"></a>Entender o esquema do AD do Azure
Um objeto no Azure Active Directory (Azure AD), como qualquer diretório, é uma construção de dados de alto nível programática que representa itens como usuários, grupos e contatos. Ao criar um novo usuário ou contato no Azure AD, você está criando uma nova instância desse objeto. Essas instâncias podem ser diferenciadas com base em suas propriedades.

As propriedades no Azure AD são os elementos responsáveis por armazenar informações sobre uma instância de um objeto no Azure AD.

O esquema do AD do Azure define as regras para as quais as propriedades podem ser usadas em uma entrada, os tipos de valores que essas propriedades podem ter e como os usuários podem interagir com esses valores. 

O Azure AD tem dois tipos de propriedades:
- **Propriedades internas**: propriedades predefinidas pelo esquema do Azure AD. Essas propriedades fornecem usos diferentes e podem ou não estar acessíveis.
- **Extensões de diretório**: propriedades que são fornecidas para que você possa personalizar o Azure ad para seu próprio uso. Por exemplo, se você estendeu sua Active Directory local com um determinado atributo e deseja fluir para esse atributo, você pode usar uma das propriedades personalizadas fornecidas. 

## <a name="attributes-and-expressions"></a>Atributos e expressões
Quando um objeto, como um usuário, é provisionado no Azure AD, uma nova instância do objeto de usuário é criada. Essa criação inclui as propriedades desse objeto, que também são conhecidas como atributos. Inicialmente, o objeto recém-criado tem seus atributos definidos como valores que são determinados pelas regras de sincronização. Esses atributos são então mantidos atualizados por meio do agente de provisionamento de nuvem.

![Provisionamento de objeto](media/concept-attributes/attribute-1.png)

Por exemplo, um usuário pode fazer parte de um departamento de marketing. Seu atributo de departamento do Azure AD é inicialmente criado quando são provisionados, e o valor é definido como marketing. Seis meses depois, se eles mudarem para vendas, seu atributo de departamento de Active Directory local será alterado para vendas. Essa alteração sincroniza com o Azure AD e é refletida em seu objeto de usuário do Azure AD.

A sincronização de atributo pode ser direta, em que o valor no Azure AD é diretamente definido como o valor do atributo local. Ou, uma expressão programática pode manipular a sincronização. Uma expressão programática é necessária nos casos em que alguma lógica ou uma determinação deve ser feita para popular o valor.

Por exemplo, se você tivesse o atributo de email " john.smith@contoso.com " e necessário para retirar a @contoso.com parte "" e fluir apenas o valor "John. Smith", você usaria algo assim:

`Replace([mail], "@contoso.com", , ,"", ,)`  

**Entrada/saída de exemplo:** <br>

* **INPUT** (mail): "john.smith@contoso.com"
* **Saída**: "John. Smith"

Para obter mais informações sobre como escrever expressões personalizadas e a sintaxe, consulte [escrevendo expressões para mapeamentos de atributo em Azure Active Directory](../app-provisioning/functions-for-customizing-application-data.md).

A tabela a seguir lista os atributos comuns e como eles são sincronizados com o Azure AD.


|Active Directory local|Tipo de mapeamento|Azure AD|
|-----|-----|-----|
|cn|Direto|CommonName
|countryCode|Direto|countryCode|
|displayName|Direto|displayName|
|givenName|Expression|givenName|
|objectGUID|Direto|sourceAnchorBinary|  
|userprincipalName|Direto|userPrincipalName|
|ProxyAdress|Direto|ProxyAddress|

## <a name="view-the-schema"></a>Exibir o esquema
> [!WARNING]
> A configuração de sincronização de nuvem cria uma entidade de serviço. A entidade de serviço está visível no portal do Azure. Você não deve modificar os mapeamentos de atributo usando a experiência de entidade de serviço no portal do Azure.  Isso não tem suporte.

Para exibir o esquema e verificá-lo, siga estas etapas.

1.  Vá para o [Gerenciador de gráficos](https://developer.microsoft.com/graph/graph-explorer).
1.  Entre com sua conta de administrador global.
1.  À esquerda, selecione **Modificar permissões** e verifique se **Directory. ReadWrite. All** está *consentido*.
1.  Execute a consulta `https://graph.microsoft.com/beta/serviceprincipals/?$filter=startswith(Displayname,'Active')` . Essa consulta retorna uma lista filtrada de entidades de serviço.
1.  Localize `"appDisplayName": "Active Directory to Azure Active Directory Provisioning"` e anote o valor de `"id"` .
    ```
    "value": [
            {
                "id": "00d41b14-7958-45ad-9d75-d52fa29e02a1",
                "deletedDateTime": null,
                "accountEnabled": true,
                "appDisplayName": "Active Directory to Azure Active Directory Provisioning",
                "appId": "1a4721b3-e57f-4451-ae87-ef078703ec94",
                "applicationTemplateId": null,
                "appOwnerOrganizationId": "47df5bb7-e6bc-4256-afb0-dd8c8e3c1ce8",
                "appRoleAssignmentRequired": false,
                "displayName": "Active Directory to Azure Active Directory Provisioning",
                "errorUrl": null,
                "homepage": "https://account.activedirectory.windowsazure.com:444/applications/default.aspx?metadata=AD2AADProvisioning|ISV9.1|primary|z",
                "loginUrl": null,
                "logoutUrl": null,
                "notificationEmailAddresses": [],
                "preferredSingleSignOnMode": null,
                "preferredTokenSigningKeyEndDateTime": null,
                "preferredTokenSigningKeyThumbprint": null,
                "publisherName": "Active Directory Application Registry",
                "replyUrls": [],
                "samlMetadataUrl": null,
                "samlSingleSignOnSettings": null,
                "servicePrincipalNames": [
                    "http://adapplicationregistry.onmicrosoft.com/adprovisioningtoaad/primary",
                    "1a4721b3-e57f-4451-ae87-ef078703ec94"
                ],
                "signInAudience": "AzureADMultipleOrgs",
                "tags": [
                    "WindowsAzureActiveDirectoryIntegratedApp"
                ],
                "addIns": [],
                "api": {
                    "resourceSpecificApplicationPermissions": []
                },
                "appRoles": [
                    {
                        "allowedMemberTypes": [
                            "User"
                        ],
                        "description": "msiam_access",
                        "displayName": "msiam_access",
                        "id": "a0326856-1f51-4311-8ae7-a034d168eedf",
                        "isEnabled": true,
                        "origin": "Application",
                        "value": null
                    }
                ],
                "info": {
                    "termsOfServiceUrl": null,
                    "supportUrl": null,
                    "privacyStatementUrl": null,
                    "marketingUrl": null,
                    "logoUrl": null
                },
                "keyCredentials": [],
                "publishedPermissionScopes": [
                    {
                        "adminConsentDescription": "Allow the application to access Active Directory to Azure Active Directory Provisioning on behalf of the signed-in user.",
                        "adminConsentDisplayName": "Access Active Directory to Azure Active Directory Provisioning",
                        "id": "d40ed463-646c-4efe-bb3e-3fa7d0006688",
                        "isEnabled": true,
                        "type": "User",
                        "userConsentDescription": "Allow the application to access Active Directory to Azure Active Directory Provisioning on your behalf.",
                        "userConsentDisplayName": "Access Active Directory to Azure Active Directory Provisioning",
                        "value": "user_impersonation"
                    }
                ],
                "passwordCredentials": []
            },
    ```
1. Substitua `{Service Principal id}` pelo valor e execute a consulta `https://graph.microsoft.com/beta/serviceprincipals/{Service Principal id}/synchronization/jobs/` .
1. Localize `"id": "AD2AADProvisioning.fd1c9b9e8077402c8bc03a7186c8f976"` e anote o valor de `"id"` .
    ```
    {
                "id": "AD2AADProvisioning.fd1c9b9e8077402c8bc03a7186c8f976",
                "templateId": "AD2AADProvisioning",
                "schedule": {
                    "expiration": null,
                    "interval": "PT2M",
                    "state": "Active"
                },
                "status": {
                    "countSuccessiveCompleteFailures": 0,
                    "escrowsPruned": false,
                    "code": "Active",
                    "lastSuccessfulExecutionWithExports": null,
                    "quarantine": null,
                    "steadyStateFirstAchievedTime": "2019-11-08T15:48:05.7360238Z",
                    "steadyStateLastAchievedTime": "2019-11-20T16:17:24.7957721Z",
                    "troubleshootingUrl": "",
                    "lastExecution": {
                        "activityIdentifier": "2dea06a7-2960-420d-931e-f6c807ebda24",
                        "countEntitled": 0,
                        "countEntitledForProvisioning": 0,
                        "countEscrowed": 15,
                        "countEscrowedRaw": 15,
                        "countExported": 0,
                        "countExports": 0,
                        "countImported": 0,
                        "countImportedDeltas": 0,
                        "countImportedReferenceDeltas": 0,
                        "state": "Succeeded",
                        "error": null,
                        "timeBegan": "2019-11-20T16:15:21.116098Z",
                        "timeEnded": "2019-11-20T16:17:24.7488681Z"
                    },
                    "lastSuccessfulExecution": {
                        "activityIdentifier": null,
                        "countEntitled": 0,
                        "countEntitledForProvisioning": 0,
                        "countEscrowed": 0,
                        "countEscrowedRaw": 0,
                        "countExported": 5,
                        "countExports": 0,
                        "countImported": 0,
                        "countImportedDeltas": 0,
                        "countImportedReferenceDeltas": 0,
                        "state": "Succeeded",
                        "error": null,
                        "timeBegan": "0001-01-01T00:00:00Z",
                        "timeEnded": "2019-11-20T14:09:46.8855027Z"
                    },
                    "progress": [],
                    "synchronizedEntryCountByType": [
                        {
                            "key": "group to Group",
                            "value": 33
                        },
                        {
                            "key": "user to User",
                            "value": 3
                        }
                    ]
                },
                "synchronizationJobSettings": [
                    {
                        "name": "Domain",
                        "value": "{\"DomainFQDN\":\"contoso.com\",\"DomainNetBios\":\"CONTOSO\",\"ForestFQDN\":\"contoso.com\",\"ForestNetBios\":\"CONTOSO\"}"
                    },
                    {
                        "name": "DomainFQDN",
                        "value": "contoso.com"
                    },
                    {
                        "name": "DomainNetBios",
                        "value": "CONTOSO"
                    },
                    {
                        "name": "ForestFQDN",
                        "value": "contoso.com"
                    },
                    {
                        "name": "ForestNetBios",
                        "value": "CONTOSO"
                    },
                    {
                        "name": "QuarantineTooManyDeletesThreshold",
                        "value": "500"
                    }
                ]
            }
    ```
1. Agora, execute a consulta `https://graph.microsoft.com/beta/serviceprincipals/{Service Principal Id}/synchronization/jobs/{AD2AAD Provisioning id}/schema` .
 
    Exemplo: https://graph.microsoft.com/beta/serviceprincipals/653c0018-51f4-4736-a3a3-94da5dcb6862/synchronization/jobs/AD2AADProvisioning.e9287a7367e444c88dc67a531c36d8ec/schema

   Substitua `{Service Principal Id}` e `{AD2ADD Provisioning Id}` por seus valores.

1. Essa consulta retorna o esquema.

   ![Esquema retornado](media/concept-attributes/schema-1.png)
 
## <a name="next-steps"></a>Próximas etapas

- [O que é provisionamento?](what-is-provisioning.md)
- [O que é a sincronização de nuvem do Azure AD Connect?](what-is-cloud-sync.md)