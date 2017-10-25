---
title: APIs REST do Gerenciador de Recursos| Microsoft Docs
description: "Uma visão geral dos exemplos de autenticação e de uso de APIs REST do Gerenciador de Recursos"
services: azure-resource-manager
documentationcenter: na
author: navalev
manager: timlt
editor: 
ms.assetid: e8d7a1d2-1e82-4212-8288-8697341408c5
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/13/2017
ms.author: navale;tomfitz;
ms.openlocfilehash: 2f7ba23775545637de865f9ef63680ae22c62164
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="resource-manager-rest-apis"></a>APIs REST do Gerenciador de Recursos
> [!div class="op_single_selector"]
> * [PowerShell do Azure](powershell-azure-resource-manager.md)
> * [CLI do Azure](xplat-cli-azure-resource-manager.md)
> * [Portal](resource-group-portal.md) 
> * [API REST](resource-manager-rest-api.md)
> 
> 

Por trás de todas as chamadas ao Azure Resource Manager, de cada modelo implantado e de todas as contas de armazenamento configuradas há uma ou mais chamadas à API RESTful do Azure Resource Manager. Este tópico é dedicado a essas APIs e como você pode chamá-las sem precisar usar o SDK. Essa abordagem pode ser útil se quiser ter controle total sobre todas as solicitações no Azure ou se o SDK para seu idioma não estiver disponível ou não der suporte às operações que você precisa.

Este artigo não abordará todas as APIs expostas no Azure, mas sim usará algumas operações como exemplo de como você pode conectá-las. Se você compreender os fundamentos básicos, poderá ler a [Referência das APIs REST do Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/) para encontrar informações detalhadas sobre como usar o restante das APIs.

## <a name="authentication"></a>Autenticação
A autenticação para o Resource Manager é tratada pelo Azure Active Directory (AD). Para se conectar a uma API, você primeiro precisa se autenticar com o Azure AD para receber um token de autenticação que poderá ser passado em cada solicitação. Como estamos descrevendo uma chamada simples diretamente para as APIs REST, presumimos que você não deseja autenticar quando um nome de usuário e senha forem solicitados. Também supomos que você não está usando mecanismos de autenticação de dois fatores. Portanto, criaremos o que chamamos de Aplicativo do Azure AD e uma entidade de serviço que será usada para fazer logon. Mas lembre-se de que o Azure AD dá suporte a vários procedimentos de autenticação e todos eles podem ser usados para recuperar esse token de autenticação necessário para solicitações subsequentes de API.
Siga [Criar aplicativo do Azure AD e Entidade de Serviço](resource-group-create-service-principal-portal.md) para obter etapas detalhadas.

### <a name="generating-an-access-token"></a>Gerando um token de acesso
A autenticação no Azure AD é feita chamando o Azure AD localizado em login.microsoftonline.com. Para autenticar, você precisa ter as seguintes informações:

* A ID do Locatário do Azure AD (o nome deste Azure AD que você está usando para fazer logon, geralmente o mesmo da sua empresa, mas não necessariamente)
* ID do Aplicativo (obtida durante a etapa de criação do aplicativo do Azure AD)
* Senha (que você selecionou ao criar o Aplicativo do Azure AD)

Na solicitação HTTP a seguir, lembre-se de substituir “ID de Locatário do Azure AD”, “ID do Aplicativo” e “Senha” pelos valores corretos.

**Solicitação HTTP Genérica:**

```HTTP
POST /<Azure AD Tenant ID>/oauth2/token?api-version=1.0 HTTP/1.1 HTTP/1.1
Host: login.microsoftonline.com
Cache-Control: no-cache
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials&resource=https%3A%2F%2Fmanagement.core.windows.net%2F&client_id=<Application ID>&client_secret=<Password>
```

... resultará (se a autenticação for bem-sucedida) em uma resposta semelhante à seguinte:

```json
{
  "token_type": "Bearer",
  "expires_in": "3600",
  "expires_on": "1448199959",
  "not_before": "1448196059",
  "resource": "https://management.core.windows.net/",
  "access_token": "eyJ0eXAiOiJKV1QiLCJhb...86U3JI_0InPUk_lZqWvKiEWsayA"
}
```
(O access_token na resposta acima foi reduzido para elevar a legibilidade)

**Gerando um token de acesso usando Bash:**

```console
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d "grant_type=client_credentials&resource=https://management.core.windows.net/&client_id=<application id>&client_secret=<password you selected for authentication>" https://login.microsoftonline.com/<Azure AD Tenant ID>/oauth2/token?api-version=1.0
```

**Gerando um token de acesso usando o PowerShell:**

```powershell
Invoke-RestMethod -Uri https://login.microsoftonline.com/<Azure AD Tenant ID>/oauth2/token?api-version=1.0 -Method Post
 -Body @{"grant_type" = "client_credentials"; "resource" = "https://management.core.windows.net/"; "client_id" = "<application id>"; "client_secret" = "<password you selected for authentication>" }
```

A resposta contém um token de acesso, informações sobre o tempo de validade desse token e sobre quais recursos podem ser usados com o token.
O token de acesso que você recebeu na chamada HTTP anterior deve ser passado em para todas as solicitações para a API do Resource Manager. Você pode passá-lo como um valor de cabeçalho denominado “Autorização” com o valor “Portador YOUR_ACCESS_TOKEN”. Observe o espaço entre “Portador” e seu token de acesso.

Como você pode ver no Resultado HTTP acima, o token é válido por um período durante o qual você deve armazená-lo em cache e reutilizá-lo. Mesmo se fosse possível autenticar no Azure AD para cada chamada à API, isso seria muito ineficiente.

## <a name="calling-resource-manager-rest-apis"></a>Chamar APIs REST do Resource Manager
Este tópico usa apenas algumas APIs para explicar o uso básico das operações REST. Para obter informações sobre todas as operações, consulte [Referência das APIs REST do Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/).

### <a name="list-all-subscriptions"></a>Listar todas as assinaturas
Listar as assinaturas disponíveis que você pode acessar é uma das operações mais simples de realizar. Na solicitação a seguir, você pode ver como o token de acesso é passado como um cabeçalho:

(Substitua YOUR_ACCESS_TOKEN por seu Token de Acesso real.)

```HTTP
GET /subscriptions?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json
```

... e, como resultado, você obterá uma lista de assinaturas que essa entidade de serviço tem permissão para acessar

(As IDs de Assinatura abaixo foram reduzidas para facilitar a leitura)

```json
{
  "value": [
    {
      "id": "/subscriptions/3a8555...555995",
      "subscriptionId": "3a8555...555995",
      "displayName": "Azure Subscription",
      "state": "Enabled",
      "subscriptionPolicies": {
        "locationPlacementId": "Internal_2014-09-01",
        "quotaId": "Internal_2014-09-01"
      }
    }
  ]
}
```

### <a name="list-all-resource-groups-in-a-specific-subscription"></a>Listar todos os grupos de recursos em uma assinatura específica
Todos os recursos disponíveis com as APIs do Resource Manager estão aninhados dentro de um grupo de recursos. Você pode consultar grupos de recursos existentes do Resource Manager em sua assinatura usando a seguinte solicitação HTTP GET. Observe como a ID da assinatura é passada como parte da URL dessa vez.

(Substitua YOUR_ACCESS_TOKEN e SUBSCRIPTION_ID pelo seu token de acesso e ID de assinatura)

```HTTP
GET /subscriptions/SUBSCRIPTION_ID/resourcegroups?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json
```

A resposta dependerá de você ter ou não grupos de recursos definidos e, se tiver, quantos.

(As IDs de Assinatura abaixo foram reduzidas para facilitar a leitura)

```json
{
    "value": [
        {
            "id": "/subscriptions/3a8555...555995/resourceGroups/myfirstresourcegroup",
            "name": "myfirstresourcegroup",
            "location": "eastus",
            "properties": {
                "provisioningState": "Succeeded"
            }
        },
        {
            "id": "/subscriptions/3a8555...555995/resourceGroups/mysecondresourcegroup",
            "name": "mysecondresourcegroup",
            "location": "northeurope",
            "tags": {
                "tagname1": "My first tag"
            },
            "properties": {
                "provisioningState": "Succeeded"
            }
        }
    ]
}
```

### <a name="create-a-resource-group"></a>Criar um grupo de recursos
Até agora, consultamos apenas APIs do Resource Manager para obter informações. É hora criamos alguns recursos e vamos começar pelo mais simples de todos, um grupo de recursos. A solicitação HTTP a seguir cria um grupo de recursos em um região/local de sua escolha e adiciona uma marcação a ele.

(Substitua YOUR_ACCESS_TOKEN, SUBSCRIPTION_ID, RESOURCE_GROUP_NAME pelo Token de Acesso e ID de Assinatura reais e pelo nome do Grupo de Recursos que deseja criar)

```HTTP
PUT /subscriptions/SUBSCRIPTION_ID/resourcegroups/RESOURCE_GROUP_NAME?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json

{
  "location": "northeurope",
  "tags": {
    "tagname1": "test-tag"
  }
}
```

Se o comando tiver êxito, você receberá uma resposta semelhante à seguinte:

```json
{
  "id": "/subscriptions/3a8555...555995/resourceGroups/RESOURCE_GROUP_NAME",
  "name": "RESOURCE_GROUP_NAME",
  "location": "northeurope",
  "tags": {
    "tagname1": "test-tag"
  },
  "properties": {
    "provisioningState": "Succeeded"
  }
}
```

Você criou um grupo de recursos no Azure com êxito. Parabéns!

### <a name="deploy-resources-to-a-resource-group-using-a-resource-manager-template"></a>Implantar recursos em um grupo de recursos usando um Modelo do Resource Manager
Com o Resource Manager, você pode implantar os recursos usando modelos. Um modelo define vários recursos e suas dependências. Para esta seção, vamos supor que você já esteja familiarizado com Modelos do Resource Manager e mostraremos como fazer uma chamada à API para iniciar uma implantação. Para obter mais informações sobre como construir modelos, consulte [Criação de Modelos do Azure Resource Manager](resource-group-authoring-templates.md).

A implantação de um modelo de não é muito diferente da ação de chamar outras APIs. Um aspecto importante é que a implantação de um modelo pode levar muito tempo. A chamada à API apenas retorna, cabendo a você como desenvolvedor consultar o status da implantação para saber quando ela foi concluída. Para obter mais informações, consulte [Rastrear operações assíncronas de Azure](resource-manager-async-operations.md).

Neste exemplo, usaremos um modelo exposto publicamente disponível no [GitHub](https://github.com/Azure/azure-quickstart-templates). O modelo que usamos implanta uma VM Linux para a região Oeste dos EUA. Embora esse exemplo use o modelo disponível em um repositório público como o GitHub, você também pode passar o modelo completo como parte da solicitação. Observe que fornecemos valores de parâmetro como parte da solicitação que serão usados dentro do modelo implantado.

(Substitua SUBSCRIPTION_ID, RESOURCE_GROUP_NAME, DEPLOYMENT_NAME, YOUR_ACCESS_TOKEN, GLOBALY_UNIQUE_STORAGE_ACCOUNT_NAME, ADMIN_USER_NAME, ADMIN_PASSWORD e DNS_NAME_FOR_PUBLIC_IP pelos valores apropriados para sua solicitação)

```HTTP
PUT /subscriptions/SUBSCRIPTION_ID/resourcegroups/RESOURCE_GROUP_NAME/providers/microsoft.resources/deployments/DEPLOYMENT_NAME?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json

{
  "properties": {
    "templateLink": {
      "uri": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-linux-vm/azuredeploy.json",
      "contentVersion": "1.0.0.0",
    },
    "mode": "Incremental",
    "parameters": {
        "newStorageAccountName": {
          "value": "GLOBALY_UNIQUE_STORAGE_ACCOUNT_NAME"
        },
        "adminUsername": {
          "value": "ADMIN_USER_NAME"
        },
        "adminPassword": {
          "value": "ADMIN_PASSWORD"
        },
        "dnsNameForPublicIP": {
          "value": "DNS_NAME_FOR_PUBLIC_IP"
        },
        "ubuntuOSVersion": {
          "value": "15.04"
        }
    }
  }
}
```

A resposta JSON longa para essa solicitação foi omitida para melhorar a legibilidade deste documento. A resposta contém informações sobre a implantação do modelo que você criou.

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre como lidar com operações assíncronas de REST, confira [Rastrear operações assíncronas do Azure](resource-manager-async-operations.md).
