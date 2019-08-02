---
title: API de preenchimento de SaaS v2 | Azure Marketplace
description: Este artigo explica como criar e gerenciar uma oferta de SaaS no AppSource e no Azure Marketplace usando as APIs de cumprimento associado v2.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: reference
ms.date: 05/23/2019
ms.author: evansma
ms.openlocfilehash: a8196370a93a6ce8eed83002397c2f09efbc777f
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68358589"
---
# <a name="saas-fulfillment-apis-version-2"></a>APIs de preenchimento de SaaS, versão 2 

Este artigo detalha as APIs que permitem que os parceiros vendam seus aplicativos SaaS no AppSource Marketplace e no Azure Marketplace. Essas APIs são um requisito para ofertas de SaaS transactáveis no AppSource e no Azure Marketplace.

## <a name="managing-the-saas-subscription-life-cycle"></a>Gerenciando o ciclo de vida da assinatura SaaS

O SaaS do Azure gerencia todo o ciclo de vida de uma compra de assinatura de SaaS. Ele usa as APIs de preenchimento como um mecanismo para orientar o cumprimento real, as alterações em planos e a exclusão da assinatura com o parceiro. A conta do cliente é baseada no estado da assinatura de SaaS que a Microsoft mantém. O diagrama a seguir ilustra os Estados e as operações que orientam as alterações entre os Estados.

![Estados do ciclo de vida da assinatura SaaS](./media/saas-subscription-lifecycle-api-v2.png)


### <a name="states-of-a-saas-subscription"></a>Estados de uma assinatura de SaaS

A tabela a seguir lista os Estados de provisionamento para uma assinatura de SaaS, incluindo uma descrição e um diagrama de sequência para cada (se aplicável). 

#### <a name="provisioning"></a>Provisionamento

Quando um cliente inicia uma compra, o parceiro recebe essas informações em um código de autorização em uma página da Web interativa do cliente que usa um parâmetro de URL. Um exemplo é `https://contoso.com/signup?token=..`, enquanto a URL da página de aterrissagem no `https://contoso.com/signup`Partner Center é. O código de autorização pode ser validado e trocado para obter os detalhes do serviço de provisionamento chamando a API de resolução.  Quando um serviço SaaS conclui o provisionamento, ele envia uma chamada de ativação para sinalizar que o preenchimento foi concluído e que o cliente pode ser cobrado. 

O diagrama a seguir mostra a sequência de chamadas de API para um cenário de provisionamento.  

![Chamadas à API para provisionamento de um serviço SaaS](./media/saas-post-provisioning-api-v2-calls.png)

#### <a name="provisioned"></a>Provisionado

Esse estado é o estado estacionário de um serviço provisionado.

##### <a name="provisioning-for-update"></a>Provisionamento para atualização 

Esse Estado significa que uma atualização para um serviço existente está pendente. Essa atualização pode ser iniciada pelo cliente, seja no Marketplace ou no serviço SaaS (somente para transações de direto para cliente).

##### <a name="provisioning-for-update-when-its-initiated-from-the-marketplace"></a>Provisionamento para atualização (quando ele é iniciado no Marketplace)

O diagrama a seguir mostra a sequência de ações quando uma atualização é iniciada no Marketplace.

![Chamadas à API quando a atualização é iniciada no Marketplace](./media/saas-update-api-v2-calls-from-marketplace-a.png)

##### <a name="provisioning-for-update-when-its-initiated-from-the-saas-service"></a>Provisionamento para atualização (quando é iniciado a partir do serviço SaaS)

O diagrama a seguir mostra as ações quando uma atualização é iniciada do serviço SaaS. (A chamada de webhook é substituída por uma atualização da assinatura iniciada pelo serviço SaaS.) 

![Chamadas à API quando a atualização é iniciada a partir do serviço SaaS](./media/saas-update-api-v2-calls-from-saas-service-a.png) 

#### <a name="suspended"></a>Suspenso

Esse estado indica que o pagamento de um cliente não foi recebido. Por política, forneceremos ao cliente um período de carência antes de cancelar a assinatura. Quando uma assinatura está nesse Estado: 

- Como parceiro, você pode optar por degradar ou bloquear o acesso do usuário ao serviço.
- A assinatura deve ser mantida em um estado recuperável que possa restaurar a funcionalidade completa sem qualquer perda de dados ou configurações. 
- Espere obter uma solicitação de reaplicar para essa assinatura por meio das APIs de preenchimento ou uma solicitação de desprovisionamento no final do período de carência. 

#### <a name="unsubscribed"></a>Assinatura cancelada 

As assinaturas atingem esse estado em resposta a uma solicitação de cliente explícita ou a não pagamento de dívidas. A expectativa do parceiro é que os dados do cliente sejam retidos para recuperação na solicitação por um determinado número de dias e, em seguida, excluídos. 


## <a name="api-reference"></a>Referência da API

Esta seção documenta a *API da assinatura* SaaS e a *API de operações*.  O valor do parâmetro `api-version` para as APIs da versão 2 `2018-08-31`é.  


### <a name="parameter-and-entity-definitions"></a>Definições de parâmetro e entidade

A tabela a seguir lista as definições para parâmetros comuns e entidades usadas por APIs de preenchimento.

|     Entidade/parâmetro     |     Definição                         |
|     ----------------     |     ----------                         |
| `subscriptionId`         | O identificador de GUID para um recurso de SaaS.  |
| `name`                   | Um nome amigável fornecido para esse recurso pelo cliente. |
| `publisherId`            | Um identificador de cadeia de caracteres exclusivo para cada Publicador (por exemplo: "contoso"). |
| `offerId`                | Um identificador de cadeia de caracteres exclusivo para cada oferta (por exemplo: "offer1").  |
| `planId`                 | Um identificador de cadeia de caracteres exclusivo para cada plano/SKU (por exemplo: "prata"). |
| `operationId`            | O identificador GUID de uma operação específica.  |
|  `action`                | A ação que está sendo executada em um recurso `unsubscribe`, `suspend` `reinstate`ou,, `changePlan`ou `changeQuantity`, `transfer`,.  |
|   |   |

Identificadores globais exclusivos ([GUIDs](https://en.wikipedia.org/wiki/Universally_unique_identifier)) são números de 128 bits (32-hexadecimal) que normalmente são gerados automaticamente. 

#### <a name="resolve-a-subscription"></a>Resolver uma assinatura 

O ponto de extremidade de resolução permite que o Publicador resolva um token do Marketplace para uma ID de recurso persistente. A ID de recurso é o identificador exclusivo para uma assinatura de SaaS. Quando um usuário é redirecionado para o site de um parceiro, a URL contém um token nos parâmetros de consulta. O parceiro deve usar esse token e fazer uma solicitação para resolvê-lo. A resposta contém a ID de assinatura SaaS exclusiva, o nome, a ID da oferta e o plano para o recurso. Esse token é válido somente por uma hora. 

##### <a name="postbrhttpsmarketplaceapimicrosoftcomapisaassubscriptionsresolveapi-versionapiversion"></a>Postar<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=<ApiVersion>`

*Parâmetros de consulta:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  A versão da operação a ser usada para esta solicitação.  |

*Cabeçalhos de solicitação:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json` |
|  x-ms-requestid    |  Um valor de cadeia de caracteres exclusivo para acompanhamento da solicitação do cliente, preferencialmente um GUID. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
|  x-ms-correlationid |  Um valor de cadeia de caracteres exclusiva para a operação no cliente. Esse parâmetro correlaciona todos os eventos da operação do cliente com eventos no lado do servidor. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.  |
|  authorization     |  [Obter token de portador JWT (token Web JSON)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app). Por exemplo: "`Bearer <access_token>`". |
|  x-ms-marketplace-token  |  O parâmetro de consulta de token na URL quando o usuário é redirecionado para o site do parceiro de SaaS do Azure (por `https://contoso.com/signup?token=..`exemplo:). *Observação:* A URL decodifica o valor do token do navegador antes de usá-lo.  |

*Códigos de resposta:*

Código: 200<br>
Resolve o token opaco para uma assinatura de SaaS. Corpo da resposta:
 

```json
{
    "id": "<guid>",  
    "subscriptionName": "Contoso Cloud Solution",
    "offerId": "offer1",
    "planId": "silver",
    "quantity": "20" 
}
```

Código: 400<br>
Solicitação inválida. x-MS-Marketplace-token está ausente, malformado ou expirou.

Código: 403<br>
Não autorizado. O token de autenticação não foi fornecido ou é inválido ou a solicitação está tentando acessar uma aquisição que não pertence ao Publicador atual.

Código: 404<br>
Não encontrado.

Código: 500<br>
Erro no servidor interno.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

### <a name="subscription-api"></a>API de assinatura

A API de assinatura do oferece suporte às seguintes operações HTTPS: **Get**, **post**, **patch**e **delete**.


#### <a name="list-subscriptions"></a>Listar assinaturas

Lista todas as assinaturas de SaaS para um Publicador.

##### <a name="getbrhttpsmarketplaceapimicrosoftcomapisaassubscriptionsapi-versionapiversion"></a>Obter<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=<ApiVersion>`

*Parâmetros de consulta:*

|             |                   |
|  --------   |  ---------------  |
| ApiVersion  |  A versão da operação a ser usada para esta solicitação.  |

*Cabeçalhos de solicitação:*

|                    |                   |
|  ---------------   |  ---------------  |
| Content-Type       |  `application/json`  |
| x-ms-requestid     |  Um valor de cadeia de caracteres exclusivo para acompanhamento da solicitação do cliente, preferencialmente um GUID. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
| x-ms-correlationid |  Um valor de cadeia de caracteres exclusiva para a operação no cliente. Esse parâmetro correlaciona todos os eventos da operação do cliente com eventos no lado do servidor. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.  |
| authorization      |  [Obter token de portador JWT (token Web JSON)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app). Por exemplo: "`Bearer <access_token>`".  |

*Códigos de resposta:*

Código: 200 <br/>
Obtém o Publicador e as assinaturas correspondentes para todas as ofertas do Publicador, com base no token de autenticação.
Carga de resposta:<br>

```json
{
  [
      {
          "id": "<guid>",
          "name": "Contoso Cloud Solution",
          "publisherId": "contoso",
          "offerId": "offer1",
          "planId": "silver",
          "quantity": "10",
          "beneficiary": { // Tenant for which SaaS subscription is purchased.
              "tenantId": "<guid>"
          },
          "purchaser": { // Tenant that purchased the SaaS subscription. These could be different for reseller scenario
              "tenantId": "<guid>"
          },
            "term": {
                "startDate": "2019-05-31",
                "endDate": "2019-06-29",
                "termUnit": "P1M"
          },
          "allowedCustomerOperations": [
              "Read" // Possible Values: Read, Update, Delete.
          ], // Indicates operations allowed on the SaaS subscription. For CSP-initiated purchases, this will always be Read.
          "sessionMode": "None", // Possible Values: None, DryRun (Dry Run indicates all transactions run as Test-Mode in the commerce stack)
          "isFreeTrial": "true", // true – the customer subscription is currently in free trial, false – the customer subscription is not currently in free trial.
          "saasSubscriptionStatus": "Subscribed" // Indicates the status of the operation: [NotStarted, PendingFulfillmentStart, Subscribed, Suspended, Unsubscribed]
      }
  ],
  "continuationToken": ""
}
```

O token de continuação estará presente somente se houver "páginas" adicionais de planos para recuperar. 

Código: 403 <br>
Não autorizado. O token de autenticação não foi fornecido ou é inválido ou a solicitação está tentando acessar uma aquisição que não pertence ao Publicador atual. 

Código: 500<br>
Erro no servidor interno.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

#### <a name="get-subscription"></a>Obter assinatura

Obtém a assinatura SaaS especificada. Use esta chamada para obter informações de licença e informações de plano.

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Obter<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId?api-version=<ApiVersion>`

*Parâmetros de consulta:*

|                    |                   |
|  ---------------   |  ---------------  |
| subscriptionId     |   Um identificador exclusivo da assinatura de SaaS que é obtido após a resolução do token por meio da API de resolução.   |
|  ApiVersion        |   A versão da operação a ser usada para esta solicitação.   |

*Cabeçalhos de solicitação:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      |  `application/json`  |
|  x-ms-requestid    |  Um valor de cadeia de caracteres exclusivo para acompanhamento da solicitação do cliente, preferencialmente um GUID. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
|  x-ms-correlationid |  Um valor de cadeia de caracteres exclusiva para a operação no cliente. Esse parâmetro correlaciona todos os eventos da operação do cliente com eventos no lado do servidor. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.  |
|  authorization     |  [Obter token de portador JWT (token Web JSON)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app). Por exemplo: "`Bearer <access_token>`".  |

*Códigos de resposta:*

Código: 200<br>
Obtém a assinatura de SaaS do identificador. Carga de resposta:<br>

```json
Response Body:
{ 
        "id":"",
        "name":"Contoso Cloud Solution",
        "publisherId": "contoso",
        "offerId": "offer1",
        "planId": "silver",
        "quantity": "10",
          "beneficiary": { // Tenant for which SaaS subscription is purchased.
              "tenantId": "<guid>"
          },
          "purchaser": { // Tenant that purchased the SaaS subscription. These could be different for reseller scenario
              "tenantId": "<guid>"
          },
        "allowedCustomerOperations": ["Read"], // Indicates operations allowed on the SaaS subscription. For CSP-initiated purchases, this will always be Read.
        "sessionMode": "None", // Dry Run indicates all transactions run as Test-Mode in the commerce stack
        "isFreeTrial": "true", // true – customer subscription is currently in free trial, false – customer subscription is not currently in free trial.
        "status": "Subscribed", // Indicates the status of the operation.
          "term": { //This gives the free trial term start and end date
            "startDate": "2019-05-31",
            "endDate": "2019-06-29",
            "termUnit": "P1M"
        },
}
```

Código: 403<br>
Não autorizado. O token de autenticação não foi fornecido ou é inválido ou a solicitação está tentando acessar uma aquisição que não pertence ao Publicador atual.

Código: 404<br>
Não encontrado.<br> 

Código: 500<br>
Erro no servidor interno.<br>

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }  
```

#### <a name="list-available-plans"></a>Listar planos disponíveis

Use essa chamada para descobrir se há alguma oferta privada ou pública para o Publicador atual.

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidlistavailableplansapi-versionapiversion"></a>Obter<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/listAvailablePlans?api-version=<ApiVersion>`

*Parâmetros de consulta:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |   A versão da operação a ser usada para esta solicitação.  |

*Cabeçalhos de solicitação:*

|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     |  `application/json` |
|   x-ms-requestid   |   Um valor de cadeia de caracteres exclusivo para acompanhamento da solicitação do cliente, preferencialmente um GUID. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
|  x-ms-correlationid  | Um valor de cadeia de caracteres exclusiva para a operação no cliente. Esse parâmetro correlaciona todos os eventos da operação do cliente com eventos no lado do servidor. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
|  authorization     |  [Obter token de portador JWT (token Web JSON)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  Por exemplo: "`Bearer <access_token>`". |

*Códigos de resposta:*

Código: 200<br>
Obtém uma lista de planos disponíveis para um cliente. Corpo da resposta:

```json
{
    "plans": [{
        "planId": "Platinum001",
        "displayName": "Private platinum plan for Contoso",
        "isPrivate": true
    }]
}
```

Código: 404<br>
Não encontrado.<br> 

Código: 403<br>
Não autorizado. O token de autenticação não foi fornecido ou é inválido ou a solicitação está tentando acessar uma aquisição que não pertence ao Publicador atual. <br> 

Código: 500<br>
Erro no servidor interno.<br>

```json
{ 
    "error": { 
      "code": "UnexpectedError", 
      "message": "An unexpected error has occurred." 
    } 
```

#### <a name="activate-a-subscription"></a>Ativar uma assinatura

##### <a name="postbrhttpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidactivateapi-versionapiversion"></a>Postar<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/activate?api-version=<ApiVersion>`

*Parâmetros de consulta:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  A versão da operação a ser usada para esta solicitação.  |
| subscriptionId     | Um identificador exclusivo da assinatura de SaaS que é obtido após a resolução do token usando a API de resolução.  |

*Cabeçalhos de solicitação:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json`  |
|  x-ms-requestid    | Um valor de cadeia de caracteres exclusivo para acompanhamento da solicitação do cliente, preferencialmente um GUID. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.  |
|  x-ms-correlationid  | Um valor de cadeia de caracteres exclusiva para a operação no cliente. Essa cadeia de caracteres correlaciona todos os eventos da operação do cliente com eventos no lado do servidor. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.  |
|  authorization     |  [Obter token de portador JWT (token Web JSON)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  Por exemplo: "`Bearer <access_token>`". |

*Carga da solicitação:*

```json
{
    "planId": "gold",
    "quantity": ""
}
```

*Códigos de resposta:*

Código: 200<br>
Ativa a assinatura.<br>

Código: 400<br>
Solicitação inadequada: falhas de validação.

Código: 403<br>
Não autorizado. O token de autenticação não foi fornecido ou é inválido ou a solicitação está tentando acessar uma aquisição que não pertence ao Publicador atual.

Código: 404<br>
Não encontrado.

Código: 500<br>
Erro no servidor interno.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

#### <a name="change-the-plan-on-the-subscription"></a>Alterar o plano na assinatura

Atualize o plano na assinatura.

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Patch<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Parâmetros de consulta:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  A versão da operação a ser usada para esta solicitação.  |
| subscriptionId     | Um identificador exclusivo da assinatura de SaaS que é obtido após a resolução do token usando a API de resolução.  |

*Cabeçalhos de solicitação:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json` |
|  x-ms-requestid    |   Um valor de cadeia de caracteres exclusivo para acompanhamento da solicitação do cliente, preferencialmente um GUID. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.  |
|  x-ms-correlationid  |  Um valor de cadeia de caracteres exclusiva para a operação no cliente. Esse parâmetro correlaciona todos os eventos da operação do cliente com eventos no lado do servidor. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.    |
| authorization      |  [Obter token de portador JWT (token Web JSON)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  Por exemplo: "`Bearer <access_token>`".  |

*Carga da solicitação:*

```json
Request Body:
{
    "planId": "gold"
}
```

*Cabeçalhos de solicitação:*

|                    |                   |
|  ---------------   |  ---------------  |
| Operation-Location | O link para um recurso para obter o status da operação.   |

*Códigos de resposta:*

Código: 202<br>
A solicitação para alterar o plano foi aceita. O parceiro deve sondar o local da operação para determinar um êxito ou uma falha. <br>

Código: 400<br>
Solicitação inadequada: falhas de validação.

Código: 403<br>
Não autorizado. O token de autenticação não foi fornecido ou é inválido ou a solicitação está tentando acessar uma aquisição que não pertence ao Publicador atual.

Código: 404<br>
Não encontrado.

Código: 500<br>
Erro no servidor interno.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

>[!Note]
>Somente um plano ou uma quantidade pode ser corrigida ao mesmo tempo, não ambos. As edições em uma assinatura com a **atualização** não `allowedCustomerOperations`estão em.

#### <a name="change-the-quantity-on-the-subscription"></a>Alterar a quantidade na assinatura

Atualize a quantidade na assinatura.

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Distribuído<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Parâmetros de consulta:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  A versão da operação a ser usada para esta solicitação.  |
| subscriptionId     | Um identificador exclusivo da assinatura de SaaS que é obtido após a resolução do token usando a API de resolução.  |

*Cabeçalhos de solicitação:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json` |
|  x-ms-requestid    |   Um valor de cadeia de caracteres exclusivo para acompanhamento da solicitação do cliente, preferencialmente um GUID. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.  |
|  x-ms-correlationid  |  Um valor de cadeia de caracteres exclusiva para a operação no cliente. Esse parâmetro correlaciona todos os eventos da operação do cliente com eventos no lado do servidor. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.    |
| authorization      |  [Obter token de portador JWT (token Web JSON)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  Por exemplo: "`Bearer <access_token>`".  |

*Carga da solicitação:*

```json
Request Body:
{
    "quantity": 5
}
```

*Cabeçalhos de solicitação:*

|                    |                   |
|  ---------------   |  ---------------  |
| Operation-Location | Link para um recurso para obter o status da operação.   |

*Códigos de resposta:*

Código: 202<br>
A solicitação para alterar a quantidade foi aceita. O parceiro deve sondar o local da operação para determinar um êxito ou uma falha. <br>

Código: 400<br>
Solicitação inadequada: falhas de validação.


Código: 403<br>
Não autorizado. O token de autenticação não foi fornecido ou é inválido ou a solicitação está tentando acessar uma aquisição que não pertence ao Publicador atual.

Código: 404<br>
Não encontrado.

Código: 500<br>
Erro no servidor interno.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

>[!Note]
>Somente um plano ou uma quantidade pode ser corrigida ao mesmo tempo, não ambos. As edições em uma assinatura com a **atualização** não `allowedCustomerOperations`estão em.

#### <a name="delete-a-subscription"></a>Excluir uma assinatura

Cancelar assinatura e excluir a assinatura especificada.

##### <a name="deletebr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionid-api-versionapiversion"></a>Excluir<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId> ?api-version=<ApiVersion>`

*Parâmetros de consulta:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  A versão da operação a ser usada para esta solicitação.  |
| subscriptionId     | Um identificador exclusivo da assinatura de SaaS que é obtido após a resolução do token usando a API de resolução.  |

*Cabeçalhos de solicitação:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     |  `application/json` |
|  x-ms-requestid    |   Um valor de cadeia de caracteres exclusivo para acompanhamento da solicitação do cliente, preferencialmente um GUID. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.   |
|  x-ms-correlationid  |  Um valor de cadeia de caracteres exclusiva para a operação no cliente. Esse parâmetro correlaciona todos os eventos da operação do cliente com eventos no lado do servidor. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.   |
|  authorization     |  [Obter token de portador JWT (token Web JSON)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  Por exemplo: "`Bearer <access_token>`".  |

*Códigos de resposta:*

Código: 202<br>
O parceiro iniciou uma chamada para cancelar a inscrição de uma assinatura de SaaS.<br>

Código: 400<br>
Excluir em uma assinatura com **excluir** não em `allowedCustomerOperations`.

Código: 403<br>
Não autorizado. O token de autenticação não foi fornecido ou é inválido ou a solicitação está tentando acessar uma aquisição que não pertence ao Publicador atual.

Código: 404<br>
Não encontrado.

Código: 500<br>
Erro no servidor interno.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```


### <a name="operations-api"></a>API de operações

A API de operações dá suporte às operações de Get e patch a seguir.

#### <a name="list-outstanding-operations"></a>Listar operações pendentes 

Lista as operações pendentes para o Publicador atual. 

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsapi-versionapiversion"></a>Obter<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations?api-version=<ApiVersion>`

*Parâmetros de consulta:*

|             |        |
|  ---------------   |  ---------------  |
|    ApiVersion                |   A versão da operação a ser usada para esta solicitação.                |
| subscriptionId     | Um identificador exclusivo da assinatura de SaaS que é obtido após a resolução do token usando a API de resolução.  |

*Cabeçalhos de solicitação:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     |  `application/json` |
|  x-ms-requestid    |  Um valor de cadeia de caracteres exclusivo para acompanhamento da solicitação do cliente, preferencialmente um GUID. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.  |
|  x-ms-correlationid |  Um valor de cadeia de caracteres exclusiva para a operação no cliente. Esse parâmetro correlaciona todos os eventos da operação do cliente com eventos no lado do servidor. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.  |
|  authorization     |  [Obter token de portador JWT (token Web JSON)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  Por exemplo: "`Bearer <access_token>`".  |

*Códigos de resposta:*

Código: 200<br> Obtém a lista de operações pendentes em uma assinatura. Carga de resposta:

```json
[{
    "id": "<guid>",  
    "activityId": "<guid>",
    "subscriptionId": "<guid>",
    "offerId": "offer1",
    "publisherId": "contoso",  
    "planId": "silver",
    "quantity": "20",
    "action": "Convert",
    "timeStamp": "2018-12-01T00:00:00",  
    "status": "NotStarted"  
}]
```


Código: 400<br>
Solicitação inadequada: falhas de validação.

Código: 403<br>
Não autorizado. O token de autenticação não foi fornecido ou é inválido ou a solicitação está tentando acessar uma aquisição que não pertence ao Publicador atual.

Código: 404<br>
Não encontrado.

Código: 500<br>
Erro no servidor interno.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```

#### <a name="get-operation-status"></a>Obter status da operação

Permite que o Publicador acompanhe o status da operação assíncrona acionada especificada ( `subscribe`como `unsubscribe` `changePlan`,, ou `changeQuantity`).

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Obter<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*Parâmetros de consulta:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  A versão da operação a ser usada para esta solicitação.  |

*Cabeçalhos de solicitação:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      |  `application/json`   |
|  x-ms-requestid    |   Um valor de cadeia de caracteres exclusivo para acompanhamento da solicitação do cliente, preferencialmente um GUID. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.  |
|  x-ms-correlationid |  Um valor de cadeia de caracteres exclusiva para a operação no cliente. Esse parâmetro correlaciona todos os eventos da operação do cliente com eventos no lado do servidor. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.  |
|  authorization     |  [Obter token de portador JWT (token Web JSON)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app). Por exemplo: "`Bearer <access_token>`".  |

*Códigos de resposta:*<br>

Código: 200<br> Obtém a operação SaaS pendente especificada. Carga de resposta:

```json
Response body:
{
    "id  ": "<guid>",
    "activityId": "<guid>",
    "subscriptionId":"<guid>",
    "offerId": "offer1",
    "publisherId": "contoso",  
    "planId": "silver",
    "quantity": "20",
    "action": "Convert",
    "timeStamp": "2018-12-01T00:00:00",
    "status": "NotStarted"
}

```

Código: 400<br>
Solicitação inadequada: falhas de validação.

Código: 403<br>
Não autorizado. O token de autenticação não foi fornecido ou é inválido ou a solicitação está tentando acessar uma aquisição que não pertence ao Publicador atual.
 
Código: 404<br>
Não encontrado.

Código: 500<br> Erro no servidor interno.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```
#### <a name="update-the-status-of-an-operation"></a>Atualizar o status de uma operação

Atualize o status de uma operação para indicar êxito ou falha com os valores fornecidos.

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Patch<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*Parâmetros de consulta:*

|                    |                   |
|  ---------------   |  ---------------  |
|   ApiVersion       |  A versão da operação a ser usada para esta solicitação.  |
| subscriptionId     | Um identificador exclusivo da assinatura de SaaS que é obtido após a resolução do token usando a API de resolução.  |
|  operationId       | A operação que está sendo concluída. |

*Cabeçalhos de solicitação:*

|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     | `application/json`   |
|   x-ms-requestid   |   Um valor de cadeia de caracteres exclusivo para acompanhamento da solicitação do cliente, preferencialmente um GUID. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
|  x-ms-correlationid |  Um valor de cadeia de caracteres exclusiva para a operação no cliente. Esse parâmetro correlaciona todos os eventos da operação do cliente com eventos no lado do servidor. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
|  authorization     |  [Obter token de portador JWT (token Web JSON)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  Por exemplo: "`Bearer <access_token>`".  |

*Carga da solicitação:*

```json
{
    "planId": "offer1",
    "quantity": "44",
    "status": "Success"    // Allowed Values: Success/Failure. Indicates the status of the operation.
}

```

*Códigos de resposta:*

Código: 200<br> Uma chamada para informar a conclusão de uma operação no lado do parceiro. Por exemplo, essa resposta pode sinalizar a alteração de estações ou planos.

Código: 400<br>
Solicitação inadequada: falhas de validação.

Código: 403<br>
Não autorizado. O token de autenticação não foi fornecido ou é inválido ou a solicitação está tentando acessar uma aquisição que não pertence ao Publicador atual.

Código: 404<br>
Não encontrado.

Código: 409<br>
Houver. Por exemplo, uma transação mais recente já foi cumprida.

Código: 500<br> Erro no servidor interno.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```

## <a name="implementing-a-webhook-on-the-saas-service"></a>Implementando um webhook no serviço SaaS

O Publicador deve implementar um webhook nesse serviço SaaS para notificar os usuários de alterações em seu serviço proativamente. Espera-se que o serviço SaaS chame a API de operações para validar e autorizar antes de tomar uma ação na notificação de webhook.

```json
{
  "id": "<this is a GUID operation id, you can call operations API with this to get status>",
  "activityId": "<this is a Guid correlation id>",
  "subscriptionId": "<Guid to uniquely identify this resource>",
  "publisherId": "<this is the publisher’s name>",
  "offerId": "<this is the offer name>",
  "planId": "<this is the plan id>",
  "quantity": "<the number of seats, will be null if not per-seat saas offer>",
  "timeStamp": "2019-04-15T20:17:31.7350641Z",
  "action": "Unsubscribe",
  "status": "NotStarted"  

}
```
Onde a ação pode ser uma das seguintes: 
- `unsubscribe`(quando o recurso tiver sido excluído)
- `changePlan`(quando a operação do plano de alteração for concluída)
- `changeQuantity`(quando a operação de alteração de quantidade for concluída)
- `suspend`(quando o recurso tiver sido suspenso)
- `reinstate`(quando o recurso tiver sido restabelecido após a suspensão)

Em que o status pode ser um dos seguintes: 
- **NotStarted** <br>
 - **InProgress** <br>
- **Êxito** <br>
- **Com falha** <br>
- **Houver** <br>

Em uma notificação de webhook, os status acionáveis são **bem-sucedidos** e **falharam**. O ciclo de vida de uma operação é de não ser **iniciado** para um estado de terminal como **êxito**, **falha**ou **conflito**. Se você receber  não inicializado ou estiver inprogredindo, continue solicitando o status por meio da API Get até que a operação alcance um estado terminal antes de tomar uma ação. 

## <a name="mock-apis"></a>APIs de simulação

Você pode usar nossas APIs fictícias para ajudá-lo a se familiarizar com o desenvolvimento, especialmente com protótipos, bem como para testar projetos. 

Ponto de extremidade `https://marketplaceapi.microsoft.com/api` do host: (nenhuma autenticação necessária)<br/>
Versão da API:`2018-09-15`<br/>
URI de exemplo:`https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=2018-09-15` <br/>

Os caminhos de ponto de extremidade de API são os mesmos em APIs de simulação e reais, mas as versões de API são diferentes. A versão é `2018-09-15` para a versão de simulação `2018-08-31` e para a versão de produção. 

Qualquer uma das chamadas à API neste artigo pode ser feita no ponto de extremidade do host fictício. Em geral, espere obter dados fictícios de volta como uma resposta. As chamadas para os métodos de assinatura de atualização na API de simulação sempre retornam 500. 

## <a name="next-steps"></a>Próximas etapas

Os desenvolvedores também podem recuperar e manipular programaticamente cargas de trabalho, ofertas e perfis de Publicador usando as [APIs REST do portal do Cloud Partner](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview).
