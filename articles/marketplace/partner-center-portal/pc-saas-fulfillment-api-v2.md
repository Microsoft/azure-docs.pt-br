---
title: APIs de cumprimento de SaaS v2 no marketplace comercial da Microsoft
description: Saiba como criar e gerenciar uma oferta de SaaS no Microsoft AppSource e no Azure Marketplace usando as APIs de cumprimento versão 2.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 06/10/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: 2acf5178e7d1cfdf907146d733150a48e9696a5e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101712344"
---
# <a name="saas-fulfillment-apis-version-2-in-the-commercial-marketplace"></a>APIs de cumprimento de SaaS v2 no marketplace comercial

Este artigo detalha as APIs que os parceiros usam para vender ofertas de SaaS (software como serviço) no Microsoft AppSource e no Azure Marketplace. Um distribuidor precisa implementar a integração com as APIs para publicar uma oferta de SaaS comercializável no Partner Center.

## <a name="managing-the-saas-subscription-life-cycle"></a>Gerenciamento do ciclo de vida de assinaturas de SaaS

O marketplace comercial gerencia todo o ciclo de vida da assinatura de SaaS após sua compra pelo usuário final. Ele usa a página de aterrissagem, as APIs de cumprimento, as APIs de operações e o webhook como mecanismo para direcionar a ativação, o uso, as atualizações e o cancelamento da assinatura de SaaS real. A conta do usuário final é baseada no estado da assinatura de SaaS que a Microsoft mantém. 

### <a name="states-of-a-saas-subscription"></a>Estados das assinaturas de SaaS

O diagrama a seguir mostra os Estados das assinaturas de SaaS e as ações aplicáveis.

![Diagrama mostrando o ciclo de vida de uma assinatura de software como serviço no marketplace.](./media/saas-subscription-lifecycle-api-v2.png)

#### <a name="purchased-but-not-yet-activated-pendingfulfillmentstart"></a>Adquirido, mas ainda não ativado (*PendingFulfillmentStart*)

Quando o usuário final (ou CSP) compra uma oferta de SaaS no marketplace comercial, é necessário informar a compra ao distribuidor. O distribuidor pode criar e configurar uma conta de SaaS no lado dele para o usuário final.

Para a criação da conta:

1. O cliente seleciona o botão **Configurar**, que está disponível para a oferta de SaaS após a compra no Microsoft AppSource ou no portal do Azure. O cliente também pode usar o botão **Configurar** no email que ele recebe logo após a compra.
2. Em seguida, a Microsoft comunica a compra ao parceiro, abrindo a URL da página de aterrissagem em uma guia nova do navegador com o parâmetro de token (o token de identificação de compra do marketplace comercial).

Um exemplo de tal chamada é `https://contoso.com/signup?token=<blob>`, enquanto a URL da página de aterrissagem da oferta de SaaS no Partner Center é `https://contoso.com/signup`. Com o token, o distribuidor tem uma ID que identifica exclusivamente a compra de SaaS e o cliente.

>[!NOTE]
>O distribuidor apenas recebe o aviso da compra de SaaS depois que o cliente inicia o processo de configuração no lado da Microsoft.

A URL da página de aterrissagem deve estar ativa e em execução o dia todo, todos os dias, pronta para receber novas chamadas da Microsoft em todos os momentos. Se a página de aterrissagem ficar não disponível, os clientes não poderão se inscrever no serviço SaaS e começar a usá-lo.

Em seguida, o distribuidor precisa retornar o *token* à Microsoft. Para isso, ele deve chamar a [API de resolução de SaaS](#resolve-a-purchased-subscription) e inserir o token como o valor do parâmetro de cabeçalho `x-ms-marketplace-token header`. Como resultado da chamada à API de resolução, o token é trocado por detalhes da compra de SaaS, como a ID exclusiva da compra, a ID da oferta adquirida e a ID do plano.

Na página de aterrissagem, o cliente deve entrar em uma conta SaaS nova ou atual por meio do SSO (Logon Único) do Azure AD (Azure Active Directory).

O distribuidor deve implementar o SSO para proporcionar a experiência de usuário exigida pela Microsoft nesse fluxo. Use o aplicativo multilocatário do Azure AD e permita contas corporativas e de estudante ou contas Microsoft pessoais ao configurar o SSO. Essa exigência vale apenas para a página de aterrissagem, para os usuários que são redirecionados ao serviço SaaS quando já estão conectados com as credenciais da Microsoft. O SSO não é necessário para todas as entradas no serviço SaaS.

> [!NOTE]
>Se o SSO exigir a permissão de um administrador ao aplicativo, a descrição da oferta no Partner Center deverá declarar que o acesso de nível de administrador é necessário. Essa declaração é para cumprir com [as políticas de certificação do marketplace comercial](/legal/marketplace/certification-policies#10003-authentication-options).

Após a entrada, o cliente deve concluir a configuração de SaaS no lado do distribuidor. Em seguida, o distribuidor deve chamar a [API ativar assinatura](#activate-a-subscription) para enviar um sinal ao Azure Marketplace indicando o término do provisionamento da conta de SaaS.
Essa ação inicia o ciclo de cobrança do cliente. Se a chamada à API ativar assinatura não for bem-sucedida, a compra não será cobrada ao cliente.


![Diagrama mostrando as chamadas de API do cenário de provisionamento.](./media/saas-update-api-v2-calls-from-saas-service-a.png) 

#### <a name="active-subscribed"></a>Ativo (*assinado*)

*Ativo (assinado)* é o estado estacionário de uma assinatura de SaaS provisionada. Depois que o lado da Microsoft processa a chamada à [API ativar assinatura](#activate-a-subscription), a assinatura de SaaS é marcada como *assinada*. O cliente agora pode usar o serviço SaaS no lado do distribuidor e receberá a cobrança.

Quando uma assinatura de SaaS já está ativa, o cliente pode selecionar **Gerenciar experiência de SaaS** no portal do Azure ou no Centro de Administração do Microsoft 365. Essa ação também faz com que a Microsoft chame a **URL da página de aterrissagem** com o parâmetro de *token*, como acontece no fluxo de ativação. O distribuidor deve distinguir entre as novas compras e o gerenciamento de contas SaaS antigas e tratar essa chamada de URL da página de aterrissagem adequadamente.

#### <a name="being-updated-subscribed"></a>Sendo atualizado (*assinado*)

Essa ação significa que uma atualização de uma assinatura SaaS ativa está sendo processada pela Microsoft e pelo distribuidor. Essa atualização pode ser iniciada por:

- O cliente no marketplace comercial.
- O CSP no marketplace comercial.
- O cliente no site de SaaS do distribuidor (mas não para compras feitas pelo CSP).

Dois tipos de atualizações estão disponíveis para uma assinatura de SaaS:

- Atualizar plano, quando o cliente escolhe outro plano para a assinatura.
- Atualizar a quantidade, quando o cliente altera o número de estações compradas da assinatura.

É possível atualizar apenas as assinaturas ativas. Durante a atualização da assinatura, seu estado permanece ativo no lado da Microsoft.

##### <a name="update-initiated-from-the-commercial-marketplace"></a>Atualização iniciada no marketplace comercial

Nesse fluxo, o cliente altera o plano de assinatura ou a quantidade de estações no portal do Azure ou no Centro de Administração do Microsoft 365.

1. Após a inserção da atualização, a Microsoft chama a URL do webhook do distribuidor, configurada no campo **Webhook de conexão** do Partner Center, com um valor apropriado para a *ação* e outros parâmetros relevantes. 
1. O lado do distribuidor deve fazer as alterações necessárias no serviço SaaS e notificar a Microsoft quando terminar chamando a [API atualizar status da operação](#update-the-status-of-an-operation).
1. Se o patch for enviado com o status de *falha*, o processo de atualização não será concluído no lado da Microsoft. A assinatura de SaaS manterá o plano e a quantidade de estações atuais.

> [!NOTE]
> O distribuidor deve invocar PATCH para [API atualizar status da operação](#update-the-status-of-an-operation) com uma resposta de falha/êxito *até dez segundos* depois de receber a notificação de webhook. Se o PATCH do status da operação não for recebido dentro de dez segundos, o plano de alteração será *automaticamente corrigido como êxito*. 

A sequência de chamadas à API para um cenário de atualização iniciado no marketplace comercial é mostrada no diagrama a seguir.

![Diagrama mostrando as chamadas de API para uma atualização iniciada pelo marketplace.](./media/saas-update-status-api-v2-calls-marketplace-side.png)

##### <a name="update-initiated-from-the-publisher"></a>Atualização iniciada pelo distribuidor

Nesse fluxo, o cliente altera o plano de assinatura ou a quantidade de estações adquiridas no próprio serviço SaaS. 

1. O código do distribuidor deve chamar a [API alterar plano](#change-the-plan-on-the-subscription) e/ou a [API alterar quantidade](#change-the-quantity-of-seats-on-the-saas-subscription) antes de fazer a alteração solicitada no lado do distribuidor. 

1. A Microsoft aplica a alteração à assinatura e notifica o distribuidor por meio da **conexão do webhook** para aplicar a mesma alteração.

1. Somente então o distribuidor faz a alteração necessária na assinatura de SaaS e notifica à Microsoft após fazê-la chamando a [API atualizar status de operação](#update-the-status-of-an-operation).

A sequência de chamadas à API para um cenário de atualização iniciado no lado do distribuidor é mostrada no diagrama a seguir.

![Diagrama mostrando as chamadas de API para uma atualização iniciada no lado do distribuidor.](./media/saas-update-status-api-v2-calls-publisher-side.png)

#### <a name="suspended-suspended"></a>Suspenso (*suspenso*)

Esse estado indica que o pagamento do cliente pelo serviço SaaS não foi recebido. O distribuidor é avisado dessa alteração no status da assinatura de SaaS pela Microsoft. A notificação é feita por meio de uma chamada para o webhook com o parâmetro *ação* definido como *SUSPENSO*.

O distribuidor pode ou não fazer alterações no serviço SaaS no lado do distribuidor. Recomendamos que o distribuidor disponibilize essas informações ao cliente suspenso e limite ou bloqueie o acesso do cliente ao serviço SaaS. Existe a chance de que o pagamento nunca seja recebido.

A Microsoft dá ao cliente um período de carência de 30 dias antes de cancelar automaticamente a assinatura. Quando a assinatura está no estado *suspenso*:

* O parceiro ou ISV deve manter a conta de SaaS em um estado recuperável, que permita restaurar a funcionalidade completa sem perda de dados ou configurações.
* O parceiro ou ISV deve esperar uma solicitação para restabelecer a assinatura, caso o pagamento seja recebido durante o período de carência, ou uma solicitação para desprovisionar a assinatura ao final do período de carência. As duas solicitações serão enviadas por meio do mecanismo de webhook.

O estado da assinatura é alterado para suspenso no lado da Microsoft antes de o distribuidor executar qualquer ação. É possível suspender apenas assinaturas ativas.

#### <a name="reinstated-suspended"></a>Restabelecido (*suspenso*)

Essa ação indica que o meio de pagamento do cliente se tornou válido novamente, o pagamento da assinatura de SaaS foi feito e a assinatura está sendo restabelecida. Nesse caso: 

1. A Microsoft chama o webhook com um parâmetro de *ação* com o valor *restabelecer*.
1. O distribuidor garante que a assinatura esteja totalmente operacional novamente no lado dele.
1. O distribuidor chama a [API patch de operação](#update-the-status-of-an-operation) com o status de êxito.
1. O processo de restabelecimento é bem-sucedido, e a assinatura de SaaS é cobrada novamente ao cliente. 

Se o patch for enviado com o status *falha*, o processo de restabelecimento não será concluído no lado da Microsoft, e a assinatura permanecerá *suspensa*.

É possível restabelecer apenas assinaturas suspensas. A assinatura de SaaS suspensa permanece no estado *suspenso* durante o restabelecimento. Após a conclusão da operação, o status da assinatura passa a ser *ativo*.

#### <a name="renewed-subscribed"></a>Renovado (*assinado*)

A assinatura de SaaS é renovada automaticamente pela Microsoft no final do período de assinatura de um mês ou um ano. O padrão da configuração de renovação automática é *verdadeiro* para todas as assinaturas de SaaS. As assinaturas de SaaS ativas são renovadas regularmente. A Microsoft não notifica o distribuidor quando uma assinatura está sendo renovada. Um cliente pode desativar a renovação automática de uma assinatura de SaaS por meio do Portal de Administração do Microsoft 365. Nesse caso, a assinatura de SaaS será cancelada automaticamente no final do período de cobrança atual. Os clientes também podem cancelar a assinatura de SaaS a qualquer momento.

Somente as assinaturas ativas são renovadas automaticamente. As assinaturas permanecerão ativas durante o processo de renovação e se a renovação automática for realizada com sucesso. Após a renovação, as datas de início e de término do período da assinatura são atualizadas para as datas do novo período.

Se a renovação automática falhar devido a um problema com o pagamento, a assinatura será *suspensa*, e o distribuidor será notificado.

#### <a name="canceled-unsubscribed"></a>Cancelado (*assinatura cancelada*) 

As assinaturas atingem esse estado em resposta a uma ação explícita do cliente ou do CSP de cancelar a assinatura no site do distribuidor, no portal do Azure ou no Centro de Administração do Microsoft 365. As assinaturas também podem ser canceladas implicitamente, como resultado de não pagamento de dívidas, depois de estar no estado *suspenso* por 30 dias.

Depois de receber uma chamada de webhook de cancelamento, o distribuidor deve manter os dados do cliente por pelo menos sete dias para recuperação mediante solicitação. Os dados do cliente podem ser excluídos apenas após esse período.

Uma assinatura de SaaS pode ser cancelada a qualquer momento em seu ciclo de vida. Não é possível reativar a assinatura após o cancelamento.

## <a name="api-reference"></a>Referência de API

Esta seção documenta as APIs de assinatura e de operações de SaaS.

As **APIs de assinatura** devem ser usadas para lidar com o ciclo de vida de assinatura de SaaS desde a compra até o cancelamento.

As **APIs de operações** devem ser usadas para:

* Verificar e reconhecer as chamadas de webhook processadas.
* Obter uma lista de operações pendentes de aplicativos que estão aguardando confirmação pelo distribuidor.

> [!NOTE]
> A versão do TLS 1.2 será imposta em breve como a versão mínima para comunicações HTTPS. Use essa versão do TLS em seu código. As versões 1.0 e 1.1 do TLS serão preteridas em breve.

### <a name="subscription-apis"></a>APIs de Assinatura

#### <a name="resolve-a-purchased-subscription"></a>Resolver uma assinatura comprada

O ponto de extremidade de resolução permite que o distribuidor troque o token de identificação de compra do marketplace comercial (chamado de *token* em [Adquirido, mas ainda não ativado](#purchased-but-not-yet-activated-pendingfulfillmentstart)) por uma ID de assinatura de SaaS adquirida persistente e seus detalhes.

Quando um cliente é redirecionado para a URL da página de aterrissagem do parceiro, o token de identificação do cliente é passado como o parâmetro de *token* nessa chamada de URL. O parceiro deve usar o token e fazer uma solicitação para resolvê-lo. A resposta da API de resolução contém a ID de assinatura de SaaS e outros detalhes para identificar exclusivamente a compra. O *token* fornecido com a chamada da URL da página de aterrissagem geralmente é válido por 24 horas. Se o *token* recebido já tiver expirado, recomendamos apresentar as seguintes diretrizes ao usuário final:

"Não foi possível identificar a compra. Reabra a assinatura de SaaS no portal do Azure ou no Centro de Administração do Microsoft 365 e selecione "Configurar conta" ou "Gerenciar conta" novamente."

A chamada à API de resolução retorna os detalhes da assinatura e o status das assinaturas de SaaS em todos os status com suporte.

##### <a name="post-httpsmarketplaceapimicrosoftcomapisaassubscriptionsresolveapi-versionapiversion"></a>Postar `https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=<ApiVersion>`

*Parâmetros de consulta:*

|  Parâmetro         | Valor            |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Use 2018-08-31.   |

*Cabeçalhos de solicitação:*

|  Parâmetro         | Valor             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json` |
|  `x-ms-requestid`    |  Um valor de cadeia de caracteres exclusivo para acompanhamento da solicitação do cliente, preferencialmente um GUID. Se esse valor não for fornecido, ele será gerado e fornecido nos cabeçalhos de resposta. |
|  `x-ms-correlationid` |  Um valor de cadeia de caracteres exclusiva para a operação no cliente. Esse parâmetro correlaciona todos os eventos da operação do cliente com eventos no lado do servidor. Se esse valor não for fornecido, ele será gerado e fornecido nos cabeçalhos de resposta.  |
|  `authorization`     |  Um token de acesso exclusivo que identifica o distribuidor que está fazendo a chamada à API. O formato é `"Bearer <accessaccess_token>"` quando o valor do token é recuperado pelo distribuidor, conforme explicado em [Obter um token com base no Aplicativo Azure AD](./pc-saas-registration.md#get-the-token-with-an-http-post). |
|  `x-ms-marketplace-token`  | O parâmetro de *token* de identificação de compra a ser resolvido.  O token é passado na chamada da URL da página de aterrissagem quando o cliente é redirecionado para o site do parceiro de SaaS (por exemplo: `https://contoso.com/signup?token=<token><authorization_token>`). <br> <br>  O valor do *token* que está sendo codificado é parte da URL da página de aterrissagem, portanto, é necessário decodificá-lo antes de usá-lo como parâmetro nessa chamada à API.  <br> <br> Aqui está um exemplo de uma cadeia de caracteres codificada na URL: `contoso.com/signup?token=ab%2Bcd%2Fef`, em que *token* é `ab%2Bcd%2Fef`.  O mesmo token decodificado será: `Ab+cd/ef` |
| | |

*Códigos de resposta:*

Código: 200 retorna identificadores de assinatura de SaaS exclusivos com base no `x-ms-marketplace-token` fornecido.

Exemplo de corpo da resposta:

```json
{
  "id": "<guid>", // purchased SaaS subscription ID
  "subscriptionName": "Contoso Cloud Solution", // SaaS subscription name
  "offerId": "offer1", // purchased offer ID
  "planId": "silver", // purchased offer's plan ID
  "quantity": "20", // number of purchased seats, might be empty if the plan is not per seat
  "subscription": { // full SaaS subscription details, see Get Subscription APIs response body for full description
    "id": "<guid>",
    "publisherId": "contoso",
    "offerId": "offer1",
    "name": "Contoso Cloud Solution",
    "saasSubscriptionStatus": " PendingFulfillmentStart ",
    "beneficiary": {
      "emailId": "test@test.com",
      "objectId": "<guid>",
      "tenantId": "<guid>",
      "pid": "<ID of the user>"
    },
    "purchaser": {
      "emailId": "test@test.com",
      "objectId": "<guid>",
      "tenantId": "<guid>",
      "pid": "<ID of the user>"
    },
    "planId": "silver",
    "term": {
      "termUnit": "P1M",
      "startDate": "2019-05-31",
      "endDate": "2019-06-29"
    },
    "isTest": true,
    "isFreeTrial": false,
    "allowedCustomerOperations": ["Delete", "Update", "Read"],
    "sandboxType": "None",
    "sessionMode": "None"
  }
}

```

Código: 400 Solicitação incorreta. `x-ms-marketplace-token` está ausente, malformado, inválido ou expirou.

Código: 403 Proibido. O token de autorização é inválido, expirou ou não foi fornecido.  A solicitação está tentando acessar uma assinatura de SaaS de uma oferta que foi publicada com uma ID de aplicativo do Azure AD diferente daquela usada para criar o token de autorização.

Esse erro geralmente é um sintoma de erros na realização do [registro de SaaS](pc-saas-registration.md).

Código: 500 Erro interno do servidor.  Repita a chamada à API.  Se o problema persistir, contate o [Suporte da Microsoft](https://partner.microsoft.com/support/v2/?stage=1).

#### <a name="activate-a-subscription"></a>Ativar uma assinatura

Após a configuração da conta de SaaS para o usuário final, o distribuidor deve chamar a API ativar assinatura no lado da Microsoft.  O cliente apenas receberá a cobrança se essa chamada à API for bem-sucedida.

##### <a name="post-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidactivateapi-versionapiversion"></a>Postar `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/activate?api-version=<ApiVersion>`

*Parâmetros de consulta:*

|  Parâmetro         | Valor             |
|  --------   |  ---------------  |
| `ApiVersion`  |  Use 2018-08-31.   |
| `subscriptionId` | O identificador exclusivo da assinatura SaaS adquirida.  Essa ID é obtida depois de resolver o token de autorização do marketplace comercial usando a [API de resolução](#resolve-a-purchased-subscription).
 |

*Cabeçalhos de solicitação:*

|  Parâmetro         | Valor             |
|  ---------------   |  ---------------  |
| `content-type`       |  `application/json`  |
| `x-ms-requestid`     |  Um valor de cadeia de caracteres exclusivo para acompanhamento da solicitação do cliente, preferencialmente um GUID.  Se esse valor não for fornecido, ele será gerado e fornecido nos cabeçalhos de resposta. |
| `x-ms-correlationid` |  Um valor de cadeia de caracteres exclusiva para a operação no cliente.  Essa cadeia de caracteres correlaciona todos os eventos da operação do cliente com eventos no lado do servidor.  Se esse valor não for fornecido, ele será gerado e fornecido nos cabeçalhos de resposta. |
| `authorization`      |  Um token de acesso exclusivo que identifica o distribuidor que está fazendo a chamada à API. O formato é `"Bearer <access_token>"` quando o valor do token é recuperado pelo distribuidor, conforme explicado em [Obter um token com base no Aplicativo Azure AD](./pc-saas-registration.md#get-the-token-with-an-http-post). |

*Solicitar exemplo de conteúdo:*

```json
{  // needed for validation of the activation request
  "planId": "gold", // purchased plan, cannot be empty
  "quantity": "" // purchased number of seats, can be empty if plan is not per seat
}
```

*Códigos de resposta:*

Código: 200 a assinatura foi marcada como assinada no lado da Microsoft.

Essa chamada não tem corpo de resposta.

Código: 400 Solicitação incorreta: falha na validação.

* `planId` não existe no conteúdo da solicitação.
* `planId` no conteúdo da solicitação não corresponde àquela que foi adquirida.
* `quantity` no conteúdo da solicitação não corresponde àquela que foi adquirida
* A assinatura de SaaS está no estado *assinado* ou *suspenso*.

Código: 403 Proibido. O token de autorização é inválido, expirou ou não foi fornecido. A solicitação está tentando acessar uma assinatura de SaaS de uma oferta que foi publicada com uma ID de aplicativo do Azure AD diferente daquela usada para criar o token de autorização.

Esse erro geralmente é um sintoma de erros na realização do [registro de SaaS](pc-saas-registration.md).

Código: 404 não encontrado. A assinatura de SaaS está no estado *assinatura cancelada*.

Código: 500 Erro interno do servidor.  Repita a chamada à API.  Se o problema persistir, contate o [Suporte da Microsoft](https://partner.microsoft.com/support/v2/?stage=1).

#### <a name="get-list-of-all-subscriptions"></a>Obter a lista de todas as assinaturas

Essa API recupera a lista de todas as assinaturas de SaaS adquiridas de todas as ofertas que o distribuidor publica no marketplace comercial.  Serão retornadas assinaturas de SaaS em todos os status possíveis. Também são retornadas assinaturas de SaaS canceladas, pois essas informações não são excluídas no lado da Microsoft.

A API retorna resultados paginados de 100 por página.

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionsapi-versionapiversion"></a>Obter `https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=<ApiVersion>`

*Parâmetros de consulta:*

|  Parâmetro         | Valor             |
|  --------   |  ---------------  |
| `ApiVersion`  |  Use 2018-08-31.  |
| `continuationToken`  | Parâmetro opcional. Para recuperar a primeira página de resultados, deixe em branco.  Use o valor retornado no parâmetro `@nextLink` para recuperar a página seguinte. |

*Cabeçalhos de solicitação:*

|  Parâmetro         | Valor             |
|  ---------------   |  ---------------  |
| `content-type`       |  `application/json`  |
| `x-ms-requestid`     |  Um valor de cadeia de caracteres exclusivo para acompanhamento da solicitação do cliente, preferencialmente um GUID. Se esse valor não for fornecido, ele será gerado e fornecido nos cabeçalhos de resposta. |
| `x-ms-correlationid` |  Um valor de cadeia de caracteres exclusiva para a operação no cliente.  Esse parâmetro correlaciona todos os eventos da operação do cliente com eventos no lado do servidor.  Se esse valor não for fornecido, ele será gerado e fornecido nos cabeçalhos de resposta. |
| `authorization`      |  Um token de acesso exclusivo que identifica o distribuidor que está fazendo a chamada à API.  O formato é `"Bearer <access_token>"` quando o valor do token é recuperado pelo distribuidor, conforme explicado em [Obter um token com base no Aplicativo Azure AD](./pc-saas-registration.md#get-the-token-with-an-http-post). |

*Códigos de resposta:*

Código: 200 retorna a lista de todas as assinaturas de todas as ofertas feitas por esse distribuidor, com base no token de autorização do distribuidor.

*Exemplo de corpo da resposta:*

```json
{
  "subscriptions": [
    {
      "id": "<guid>", // purchased SaaS subscription ID
      "name": "Contoso Cloud Solution", // SaaS subscription name
      "publisherId": "contoso", // publisher ID
      "offerId": "offer1", // purchased offer ID
      "planId": "silver", // purchased plan ID
      "quantity": "10", // purchased amount of seats, will be empty if plan is not per seat
      "beneficiary": { // email address, user ID and tenant ID for which SaaS subscription was purchased.
        "emailId": " test@contoso.com",
        "objectId": "<guid>",
        "tenantId": "<guid>",
        "pid": "<ID of the user>"
      },
      "purchaser": { // email address, user ID and tenant ID that purchased the SaaS subscription. These could be different from beneficiary information for reseller (CSP) purchase
        "emailId": " test@contoso.com",
        "objectId": "<guid>",
        "tenantId": "<guid>",
        "pid": "<ID of the user>"
      },
      "term": { // The period for which the subscription was purchased.
        "startDate": "2019-05-31", //format: YYYY-MM-DD. This is the date when the subscription was activated by the ISV and the billing started. This field is relevant only for Active and Suspended subscriptions.
        "endDate": "2019-06-30", // This is the last day the subscription is valid. Unless stated otherwise, the automatic renew will happen the next day. This field is relevant only for Active and Suspended subscriptions.
        "termUnit": "P1M" // where P1M is monthly and P1Y is yearly. Also reflected in the startDate and endDate values
      },
      "allowedCustomerOperations": ["Read", "Update", "Delete"], // Indicates operations allowed on the SaaS subscription for beneficiary. For CSP-initiated purchases, this will always be "Read" because the customer cannot update or delete subscription in this flow.  Purchaser can perform all operations on the subscription.
      "sessionMode": "None", // not relevant
      "isFreeTrial": true, // true - the customer subscription is currently in free trial, false - the customer subscription is not currently in free trial. (Optional field -– if not returned, the value is false.)
      "isTest": false, // not relevant
      "sandboxType": "None", // not relevant
      "saasSubscriptionStatus": "Subscribed" // Indicates the status of the operation. Can be one of the following: PendingFulfillmentStart, Subscribed, Suspended or Unsubscribed.
    },
    // next SaaS subscription details, might be a different offer
    {
      "id": "<guid1>",
      "name": "Contoso Cloud Solution1",
      "publisherId": "contoso",
      "offerId": "offer2",
      "planId": "gold",
      "quantity": "",
      "beneficiary": {
        "emailId": " test@contoso.com",
        "objectId": "<guid>",
        "tenantId": "<guid>",
        "pid": "<ID of the user>"
      },
      "purchaser": {
        "emailId": "purchase@csp.com ",
        "objectId": "<guid>",
        "tenantId": "<guid>",
        "pid": "<ID of the user>"
      },
      "term": {
        "startDate": "2019-05-31",
        "endDate": "2020-04-30",
        "termUnit": "P1Y"
      },
      "allowedCustomerOperations": ["Read"],
      "sessionMode": "None",
      "isFreeTrial": false,
      "isTest": false,
      "sandboxType": "None",
      "saasSubscriptionStatus": "Suspended"
    }
  ],
  "@nextLink": "https:// https://marketplaceapi.microsoft.com/api/saas/subscriptions/?continuationToken=%5b%7b%22token%22%3a%22%2bRID%3a%7eYeUDAIahsn22AAAAAAAAAA%3d%3d%23RT%3a1%23TRC%3a2%23ISV%3a1%23FPC%3aAgEAAAAQALEAwP8zQP9%2fFwD%2b%2f2FC%2fwc%3d%22%2c%22range%22%3a%7b%22min%22%3a%22%22%2c%22max%22%3a%2205C1C9CD673398%22%7d%7d%5d&api-version=2018-08-31" // url that contains continuation token to retrieve next page of the SaaS subscriptions list, if empty or absent, this is the last page. ISV can use this url as is to retrieve the next page or extract the value of continuation token from this url.
}
```

Se não forem encontradas assinaturas de SaaS adquiridas desse distribuidor, um corpo de resposta vazio será retornado.

Código: 403 Proibido. O token de autorização não está disponível, é inválido ou expirou.

Esse erro geralmente é um sintoma de erros na realização do [registro de SaaS](pc-saas-registration.md). 

Código: 500 Erro interno do servidor. Repita a chamada à API.  Se o problema persistir, contate o [Suporte da Microsoft](https://partner.microsoft.com/support/v2/?stage=1).

#### <a name="get-subscription"></a>Obter assinatura

Essa API recupera uma assinatura de SaaS adquirida específica de uma oferta de SaaS que o distribuidor publica no marketplace comercial. Use essa chamada para obter todas as informações disponíveis de uma assinatura de SaaS pela ID em vez de chamar a API que é usada para obter uma lista de todas as assinaturas.

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Obter `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Parâmetros de consulta:*

|  Parâmetro         | Valor             |
|  ---------------   |  ---------------  |
| `ApiVersion`        |   Use 2018-08-31. |
| `subscriptionId`     |  O identificador exclusivo da assinatura SaaS adquirida.  Essa ID é obtida depois de resolver o token de autorização do marketplace comercial usando a API de resolução. |

*Cabeçalhos de solicitação:*

|  Parâmetro         | Valor             |
|  ---------------   |  ---------------  |
|  `content-type`      |  `application/json`  |
|  `x-ms-requestid`    |  Um valor de cadeia de caracteres exclusivo para acompanhamento da solicitação do cliente, preferencialmente um GUID. Se esse valor não for fornecido, ele será gerado e fornecido nos cabeçalhos de resposta. |
|  `x-ms-correlationid` |  Um valor de cadeia de caracteres exclusiva para a operação no cliente.  Esse parâmetro correlaciona todos os eventos da operação do cliente com eventos no lado do servidor.  Se esse valor não for fornecido, ele será gerado e fornecido nos cabeçalhos de resposta. |
|  `authorization`     | Um token de acesso exclusivo que identifica o distribuidor que está fazendo a chamada à API. O formato é `"Bearer <access_token>"` quando o valor do token é recuperado pelo distribuidor, conforme explicado em [Obter um token com base no Aplicativo Azure AD](./pc-saas-registration.md#get-the-token-with-an-http-post).  |

*Códigos de resposta:*

Código: 200 retorna os detalhes de uma assinatura de SaaS com base no `subscriptionId` fornecido.

*Exemplo de corpo da resposta:*

```json
{
  "id": "<guid>", // purchased SaaS subscription ID
  "name": "Contoso Cloud Solution", // SaaS subscription name
  "publisherId": "contoso", // publisher ID
  "offerId": "offer1", // purchased offer ID
  "planId": "silver", // purchased plan ID
  "quantity": "10", // purchased amount of seats, will be empty if plan is not per seat
  "beneficiary": { // email address, user ID and tenant ID for which SaaS subscription is purchased.
    "emailId": "test@contoso.com",
    "objectId": "<guid>",
    "tenantId": "<guid>",
    "pid": "<ID of the user>"
  },
  "purchaser": { // email address ,user ID and tenant ID that purchased the SaaS subscription.  These could be different from beneficiary information for reseller (CSP) scenario
    "emailId": "test@test.com",
    "objectId": "<guid>",
    "tenantId": "<guid>",
    "pid": "<ID of the user>"
  },
  "allowedCustomerOperations": ["Read", "Update", "Delete"], // Indicates operations allowed on the SaaS subscription for beneficiary.  For CSP-initiated purchases, this will always be "Read" because the customer cannot update or delete subscription in this flow.  Purchaser can perform all operations on the subscription.
  "sessionMode": "None", // not relevant
  "isFreeTrial": false, // true - the customer subscription is currently in free trial, false - the customer subscription is not currently in free trial. Optional field – if not returned the value is false.
  "isTest": false, // not relevant
  "sandboxType": "None", // not relevant
  "saasSubscriptionStatus": " Subscribed ", // Indicates the status of the operation: PendingFulfillmentStart, Subscribed, Suspended or Unsubscribed.
  "term": { // the period for which the subscription was purchased
    "startDate": "2019-05-31", //format: YYYY-MM-DD. This is the date when the subscription was activated by the ISV and the billing started. This field is relevant only for Active and Suspended subscriptions.
    "endDate": "2019-06-29", // This is the last day the subscription is valid. Unless stated otherwise, the automatic renew will happen the next day. This field is relevant only for Active and Suspended subscriptions.
    "termUnit": "P1M" //where P1M is monthly and P1Y is yearly. Also reflected in the startDate and endDate values.
  }
}
```

Código: 403 Proibido. O token de autorização é inválido, expirou ou não foi fornecido. A solicitação está tentando acessar uma assinatura de SaaS de uma oferta que foi publicada com uma ID de aplicativo do Azure AD diferente daquela usada para criar o token de autorização.

Esse erro geralmente é um sintoma de erros na realização do [registro de SaaS](pc-saas-registration.md). 

Código: 404 não encontrado.  Não foi localizada uma assinatura de SaaS com `subscriptionId` especificada.

Código: 500 Erro interno do servidor.  Repita a chamada à API.  Se o problema persistir, contate o [Suporte da Microsoft](https://partner.microsoft.com/support/v2/?stage=1).

#### <a name="list-available-plans"></a>Listar os planos disponíveis

Essa API recupera todos os planos de uma oferta de SaaS identificada pela `subscriptionId` de uma compra específica da oferta.  Use essa chamada para obter uma lista de todos os planos públicos e privados que o beneficiário de uma assinatura de SaaS pode atualizar para a assinatura.  Os planos retornados estão disponíveis na mesma geografia que o plano já adquirido.

Essa chamada retorna uma lista de planos disponíveis para o cliente, além daquele já adquirido.  A lista pode ser apresentada a um usuário final no site do distribuidor.  O usuário final pode alterar o plano de assinatura para qualquer um dos planos na lista retornada.  A alteração para um plano que não esteja na lista falhará.

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidlistavailableplansapi-versionapiversion"></a>Obter `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/listAvailablePlans?api-version=<ApiVersion>`

*Parâmetros de consulta:*

|  Parâmetro         | Valor             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Use 2018-08-31.  |
|  `subscriptionId`    |  O identificador exclusivo da assinatura SaaS adquirida.  Essa ID é obtida depois de resolver o token de autorização do marketplace comercial usando a API de resolução. |

*Cabeçalhos de solicitação:*

|  Parâmetro         | Valor             |
|  ---------------   |  ---------------  |
|   `content-type`     |  `application/json` |
|   `x-ms-requestid`   |  Um valor de cadeia de caracteres exclusivo para acompanhamento da solicitação do cliente, preferencialmente um GUID.  Se esse valor não for fornecido, ele será gerado e fornecido nos cabeçalhos de resposta. |
|  `x-ms-correlationid`  |  Um valor de cadeia de caracteres exclusiva para a operação no cliente.  Esse parâmetro correlaciona todos os eventos da operação do cliente com eventos no lado do servidor.  Se esse valor não for fornecido, ele será gerado e fornecido nos cabeçalhos de resposta. |
|  `authorization`     |  Um token de acesso exclusivo que identifica o distribuidor que está fazendo a chamada à API.  O formato é `"Bearer <access_token>"` quando o valor do token é recuperado pelo distribuidor, conforme explicado em [Obter um token com base no Aplicativo Azure AD](./pc-saas-registration.md#get-the-token-with-an-http-post).  |

*Códigos de resposta:*

Código: 200 retorna a lista de todos os planos de uma assinatura de SaaS disponíveis, incluindo aquele já adquirido.

Exemplo de corpo da resposta:

```json
{
  "plans": [
    {
      "planId": "Platinum001",
      "displayName": "Private platinum plan for Contoso", // display name of the plan as it appears in the marketplace
      "isPrivate": true //true or false
    },
    {
      "planId": "gold",
      "displayName": "Gold plan for Contoso",
      "isPrivate": false //true or false
    }
  ]
}
```

Se `subscriptionId` não for encontrada, o corpo da resposta será retornado vazio.

Código: 403 Proibido. O token de autorização é inválido, expirou ou não foi fornecido.  Talvez a solicitação esteja tentando acessar uma assinatura de SaaS de uma oferta que foi publicada com uma ID de aplicativo do Azure AD diferente daquela usada para criar o token de autorização.

Esse erro geralmente é um sintoma de erros na realização do [registro de SaaS](pc-saas-registration.md). 

Código: 500 Erro interno do servidor.  Repita a chamada à API.  Se o problema persistir, contate o [Suporte da Microsoft](https://partner.microsoft.com/support/v2/?stage=1).

#### <a name="change-the-plan-on-the-subscription"></a>Alterar o plano na assinatura

Use essa API para atualizar o plano adquirido de uma assinatura de SaaS para um novo plano (público ou privado).  O distribuidor deve chamar essa API quando há alteração, no lado dele, de um plano de uma assinatura de SaaS adquirida no marketplace comercial.

É possível chamar a API apenas para assinaturas *ativas*.  Qualquer plano pode ser alterado para qualquer outro plano existente (público ou privado), mas não para ele mesmo.  Para planos privados, o locatário do cliente deve ser definido como parte do público-alvo do plano no Partner Center.

##### <a name="patch-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Aplicar patch `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Parâmetros de consulta:*

|  Parâmetro         | Valor             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Use 2018-08-31.  |
| `subscriptionId`     | O identificador exclusivo da assinatura SaaS adquirida.  Essa ID é obtida depois de resolver o token de autorização do marketplace comercial usando a API de resolução. |

*Cabeçalhos de solicitação:*
 
|  Parâmetro         | Valor             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json`  |
|  `x-ms-requestid`    | Um valor de cadeia de caracteres exclusivo para acompanhamento da solicitação do cliente, preferencialmente um GUID. Se esse valor não for fornecido, ele será gerado e fornecido nos cabeçalhos de resposta.  |
|  `x-ms-correlationid`  | Um valor de cadeia de caracteres exclusiva para a operação no cliente.  Esse parâmetro correlaciona todos os eventos da operação do cliente com eventos no lado do servidor.  Se esse valor não for fornecido, ele será gerado e fornecido nos cabeçalhos de resposta.  |
|  `authorization`     |  Um token de acesso exclusivo que identifica o distribuidor que está fazendo a chamada à API.  O formato é `"Bearer <access_token>"` quando o valor do token é recuperado pelo distribuidor, conforme explicado em [Obter um token com base no Aplicativo Azure AD](./pc-saas-registration.md#get-the-token-with-an-http-post). |

*Solicitar exemplo de conteúdo:*

```json
{
  "planId": "gold" // the ID of the new plan to be purchased
}
```

*Códigos de resposta:*

Código: 202 a solicitação para alterar o plano foi aceita e manipulada de maneira assíncrona.  O parceiro deve sondar a **URL da Localização da operação** para determinar se a solicitação de alteração do plano teve êxito ou falhou.  A sondagem deve ser feita em intervalos de alguns segundos até determinar o status final da operação: *falha*, *êxito* ou *conflito*.  O status final da operação deve ser retornado rapidamente, mas pode levar vários minutos em alguns casos.

O parceiro também recebe uma notificação de webhook quando a ação está pronta para realização no lado do marketplace comercial.  Somente então o distribuidor faz a alteração do plano no lado dele.

*Cabeçalhos de resposta:*

|  Parâmetro         | Valor             |
|  ---------------   |  ---------------  |
|  `Operation-Location`        |  URL para obter o status da operação.  Por exemplo, `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=2018-08-31`. |

Código: 400 Solicitação incorreta: falhas na validação.

* O novo plano não existe ou não está disponível para esta assinatura de SaaS específica.
* O novo plano é o mesmo que o plano atual.
* O status da assinatura de SaaS não é *assinado*.
* A operação de atualização de uma assinatura de SaaS não foi incluída em `allowedCustomerOperations`.

Código: 403 Proibido. O token de autorização é inválido, expirou ou não foi fornecido.  A solicitação está tentando acessar uma assinatura de SaaS de uma oferta que foi publicada com uma ID de aplicativo do Azure AD diferente daquela usada para criar o token de autorização.

Esse erro geralmente é um sintoma de erros na realização do [registro de SaaS](pc-saas-registration.md).

Código: 404 não encontrado.  Não foi localizada a assinatura de SaaS com a `subscriptionId`.

Código: 500 Erro interno do servidor.  Repita a chamada à API.  Se o problema persistir, contate o [Suporte da Microsoft](https://partner.microsoft.com/support/v2/?stage=1).

>[!NOTE]
>É possível alterar o plano ou a quantidade de estações, mas não ambos ao mesmo tempo.

>[!Note]
>Somente é possível chamar essa API depois de obter aprovação explícita do usuário final para a alteração.

#### <a name="change-the-quantity-of-seats-on-the-saas-subscription"></a>Alterar a quantidade de estações na assinatura de SaaS

Use essa API para atualizar (aumentar ou diminuir) a quantidade de estações compradas em uma assinatura de SaaS.  O distribuidor deve chamar essa API quando há alteração, no lado dele, no número de estações de uma assinatura de SaaS criada no marketplace comercial.

A quantidade de estações não pode ser maior que a quantidade permitida no plano atual.  Nesse caso, o distribuidor deve alterar o plano antes de alterar a quantidade de estações.

##### <a name="patch-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Aplicar patch `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Parâmetros de consulta:*

|  Parâmetro         | Valor             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Use 2018-08-31.  |
|  `subscriptionId`     | O identificador exclusivo da assinatura de SaaS adquirida.  Essa ID é obtida depois de resolver o token de autorização do marketplace comercial usando a API de resolução.  |

*Cabeçalhos de solicitação:*
 
|  Parâmetro         | Valor             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json`  |
|  `x-ms-requestid`    | Um valor de cadeia de caracteres exclusivo para acompanhamento da solicitação do cliente, preferencialmente um GUID.  Se esse valor não for fornecido, ele será gerado e fornecido nos cabeçalhos de resposta.  |
|  `x-ms-correlationid`  | Um valor de cadeia de caracteres exclusiva para a operação no cliente.  Esse parâmetro correlaciona todos os eventos da operação do cliente com eventos no lado do servidor.  Se esse valor não for fornecido, ele será gerado e fornecido nos cabeçalhos de resposta.  |
|  `authorization`     | Um token de acesso exclusivo que identifica o distribuidor que está fazendo a chamada à API.  O formato é `"Bearer <access_token>"` quando o valor do token é recuperado pelo distribuidor, conforme explicado em [Obter um token com base no Aplicativo Azure AD](./pc-saas-registration.md#get-the-token-with-an-http-post).  |

*Solicitar exemplo de conteúdo:*

```json
{
  "quantity": 5 // the new amount of seats to be purchased
}
```

*Códigos de resposta:*

Código: 202 a solicitação para alterar a quantidade foi aceita e manipulada de maneira assíncrona. O parceiro deve sondar a **URL da Localização da Operação** para determinar se a solicitação de alteração da quantidade teve êxito ou falhou.  A sondagem deve ser feita em intervalos de alguns segundos até determinar o status final da operação: *falha*, *êxito* ou *conflito*.  O status final da operação deve ser retornado rapidamente, mas pode levar vários minutos em alguns casos.

O parceiro também recebe uma notificação de webhook quando a ação está pronta para realização no lado do marketplace comercial.  Somente então o distribuidor faz a alteração da quantidade no lado dele.

*Cabeçalhos de resposta:*

|  Parâmetro         | Valor             |
|  ---------------   |  ---------------  |
|  `Operation-Location`        |  Vincule a um recurso para obter o status da operação.  Por exemplo, `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=2018-08-31`.  |

Código: 400 Solicitação incorreta: falhas na validação.

* A nova quantidade é maior ou menor que o limite do plano atual.
* A nova quantidade está ausente.
* A nova quantidade é igual à quantidade atual.
* O status da assinatura de SaaS não é assinado.
* A operação de atualização de uma assinatura de SaaS não foi incluída em `allowedCustomerOperations`.

Código: 403 Proibido.  O token de autorização é inválido, expirou ou não foi fornecido.  A solicitação está tentando acessar uma assinatura que não pertence ao distribuidor atual.

Esse erro geralmente é um sintoma de erros na realização do [registro de SaaS](pc-saas-registration.md). 

Código: 404 não encontrado.  Não foi localizada a assinatura de SaaS com a `subscriptionId`.

Código: 500 Erro interno do servidor.  Repita a chamada à API.  Se o problema persistir, contate o [Suporte da Microsoft](https://partner.microsoft.com/support/v2/?stage=1).

>[!Note]
>É possível alterar o plano ou a quantidade, mas não ambos ao mesmo tempo.

>[!Note]
>Somente é possível chamar essa API depois de obter aprovação explícita do usuário final para a alteração.

#### <a name="cancel-a-subscription"></a>Cancelar uma assinatura

Use essa API para cancelar a assinatura de SaaS especificada.  O distribuidor não precisa usar essa API, e recomendamos encaminhar os clientes ao marketplace comercial para cancelar assinaturas de SaaS.

Se o distribuidor decidir implementar o cancelamento de assinaturas de SaaS adquiridas no marketplace comercial no lado dele, deverá chamar essa API.  Após a conclusão dessa chamada, o status da assinatura passa a ser *assinatura cancelada* no lado da Microsoft.

Não há cobrança ao cliente quando a assinatura é cancelada nos seguintes períodos de carência:

* Vinte e quatro horas após a ativação no caso de uma assinatura mensal.
* Quatorze dias após a ativação no caso de uma assinatura anual.

Há cobrança ao cliente quando a assinatura é cancelada após os períodos de carência acima.  O cliente perderá o acesso à assinatura de SaaS no lado da Microsoft imediatamente após o cancelamento. 

##### <a name="delete-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Excluir `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Parâmetros de consulta:*

|  Parâmetro         | Valor             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Use 2018-08-31.  |
|  `subscriptionId`     | O identificador exclusivo da assinatura SaaS adquirida.  Essa ID é obtida depois de resolver o token de autorização do marketplace comercial usando a API de resolução.  |

*Cabeçalhos de solicitação:*
 
|  Parâmetro         | Valor             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json`  |
|  `x-ms-requestid`    | Um valor de cadeia de caracteres exclusivo para acompanhamento da solicitação do cliente, preferencialmente um GUID.  Se esse valor não for fornecido, ele será gerado e fornecido nos cabeçalhos de resposta.  |
|  `x-ms-correlationid`  | Um valor de cadeia de caracteres exclusiva para a operação no cliente.  Esse parâmetro correlaciona todos os eventos da operação do cliente com eventos no lado do servidor.  Se esse valor não for fornecido, ele será gerado e fornecido nos cabeçalhos de resposta.  |
|  `authorization`     |  Um token de acesso exclusivo que identifica o distribuidor que está fazendo a chamada à API.  O formato é `"Bearer <access_token>"` quando o valor do token é recuperado pelo distribuidor, conforme explicado em [Obter um token com base no Aplicativo Azure AD](./pc-saas-registration.md#get-the-token-with-an-http-post). |

*Códigos de resposta:*

Código: 202 a solicitação de cancelamento da assinatura foi aceita e manipulada de maneira assíncrona.  O parceiro deve sondar a **URL da Localização da Operação** para determinar se a solicitação teve êxito ou falhou.  A sondagem deve ser feita em intervalos de alguns segundos até determinar o status final da operação: *falha*, *êxito* ou *conflito*.  O status final da operação deve ser retornado rapidamente, mas pode levar vários minutos em alguns casos.

O parceiro também recebe uma notificação de webhook quando a ação é realizada no lado do marketplace comercial.  Somente então o distribuidor deve cancelar a assinatura no lado dele.

*Cabeçalhos de resposta:*

|  Parâmetro         | Valor             |
|  ---------------   |  ---------------  |
|  `Operation-Location`        |  Vincule a um recurso para obter o status da operação.  Por exemplo, `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=2018-08-31`. |

Código: 400 Solicitação incorreta.  A exclusão não está na lista `allowedCustomerOperations` da assinatura de SaaS.

Código: 403 Proibido.  O token de autorização é inválido, expirou ou não está disponível. A solicitação está tentando acessar uma assinatura de SaaS de uma oferta que foi publicada com uma ID de aplicativo do Azure AD diferente daquela usada para criar o token de autorização.

Esse erro geralmente é um sintoma de erros na realização do [registro de SaaS](pc-saas-registration.md).

Código: 404 não encontrado.  Não foi localizada a assinatura de SaaS com a `subscriptionId`.

Código: 500 Erro interno do servidor. Repita a chamada à API.  Se o problema persistir, contate o [Suporte da Microsoft](https://partner.microsoft.com/support/v2/?stage=1).

### <a name="operations-apis"></a>APIs de operações

#### <a name="list-outstanding-operations"></a>Listar operações pendentes 

Obter a lista das operações pendentes da assinatura de SaaS especificada.  O distribuidor deve reconhecer as operações retornadas chamando a [API de patch de operação](#update-the-status-of-an-operation).

No momento, apenas as **operações de estabelecimento** são retornadas como resposta para essa chamada à API.

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsapi-versionapiversion"></a>Obter `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations?api-version=<ApiVersion>`

*Parâmetros de consulta:*

|  Parâmetro         | Valor             |
|  ---------------   |  ---------------  |
|    `ApiVersion`    |  Use 2018-08-31.         |
|    `subscriptionId` | O identificador exclusivo da assinatura SaaS adquirida.  Essa ID é obtida depois de resolver o token de autorização do marketplace comercial usando a API de resolução.  |

*Cabeçalhos de solicitação:*
 
|  Parâmetro         | Valor             |
|  ---------------   |  ---------------  |
|  `content-type`     |  `application/json` |
|  `x-ms-requestid`    |  Um valor de cadeia de caracteres exclusivo para acompanhamento da solicitação do cliente, preferencialmente um GUID.  Se esse valor não for fornecido, ele será gerado e fornecido nos cabeçalhos de resposta.  |
|  `x-ms-correlationid` |  Um valor de cadeia de caracteres exclusiva para a operação no cliente.  Esse parâmetro correlaciona todos os eventos da operação do cliente com eventos no lado do servidor.  Se esse valor não for fornecido, ele será gerado e fornecido nos cabeçalhos de resposta.  |
|  `authorization`     |  O formato é `"Bearer <access_token>"` quando o valor do token é recuperado pelo distribuidor, conforme explicado em [Obter um token com base no Aplicativo Azure AD](./pc-saas-registration.md#get-the-token-with-an-http-post).  |

*Códigos de resposta:*

Código: 200 retorna a operação de restabelecimento pendente da assinatura de SaaS especificada.

*Exemplo de conteúdo de resposta:*

```json
{
  "operations": [
    {
      "id": "<guid>", //Operation ID, should be provided in the operations patch API call
      "activityId": "<guid>", //not relevant
      "subscriptionId": "<guid>", // subscriptionId of the SaaS subscription that is being reinstated
      "offerId": "offer1", // purchased offer ID
      "publisherId": "contoso",
      "planId": "silver", // purchased plan ID
      "quantity": "20", // purchased amount of seats, will be empty is not relevant
      "action": "Reinstate",
      "timeStamp": "2018-12-01T00:00:00", // UTC
      "status": "InProgress" // the only status that can be returned in this case
    }
  ]
}
```

Retorna um JSON vazio se nenhuma operação de restabelecimento estiver pendente.

Código: 400 Solicitação incorreta: falhas na validação.

Código: 403 Proibido. O token de autorização é inválido, expirou ou não foi fornecido.  A solicitação está tentando acessar uma assinatura de SaaS de uma oferta que foi publicada com uma ID de aplicativo do Azure AD diferente daquela usada para criar o token de autorização.

Esse erro geralmente é um sintoma de erros na realização do [registro de SaaS](pc-saas-registration.md). 

Código: 404 não encontrado.  Não foi localizada a assinatura de SaaS com a `subscriptionId`.

Código: 500 Erro interno do servidor. Repita a chamada à API.  Se o problema persistir, contate o [Suporte da Microsoft](https://partner.microsoft.com/support/v2/?stage=1).

#### <a name="get-operation-status"></a>Obter status da operação

Essa API permite que o distribuidor acompanhe o status da operação assíncrona especificada: **cancelar assinatura**, **ChangePlan** ou **ChangeQuantity**.

É possível obter a `operationId` dessa chamada à API usando o valor retornado pela **Localização da Operação**, a chamada à API obter operações pendentes ou o valor de parâmetro `<id>` recebido em uma chamada de webhook.

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Obter `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*Parâmetros de consulta:*

|  Parâmetro         | Valor             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Use 2018-08-31.  |
|  `subscriptionId`    |  O identificador exclusivo da assinatura SaaS adquirida.  Essa ID é obtida depois de resolver o token de autorização do marketplace comercial usando a API de resolução. |
|  `operationId`       |  O identificador exclusivo da operação que está sendo recuperada. |

*Cabeçalhos de solicitação:*

|  Parâmetro         | Valor             |
|  ---------------   |  ---------------  |
|  `content-type`      |  `application/json`   |
|  `x-ms-requestid`    |  Um valor de cadeia de caracteres exclusivo para acompanhamento da solicitação do cliente, preferencialmente um GUID.  Se esse valor não for fornecido, ele será gerado e fornecido nos cabeçalhos de resposta. |
|  `x-ms-correlationid` |  Um valor de cadeia de caracteres exclusiva para a operação no cliente.  Esse parâmetro correlaciona todos os eventos da operação do cliente com eventos no lado do servidor.  Se esse valor não for fornecido, ele será gerado e fornecido nos cabeçalhos de resposta.  |
|  `authorization`     |  Um token de acesso exclusivo que identifica o distribuidor que está fazendo a chamada à API.  O formato é `"Bearer <access_token>"` quando o valor do token é recuperado pelo distribuidor, conforme explicado em [Obter um token com base no Aplicativo Azure AD](./pc-saas-registration.md#get-the-token-with-an-http-post).  |

*Códigos de resposta:*

Código: 200 obtém os detalhes da operação SaaS especificada. 

*Exemplo de conteúdo de resposta:*

```json
Response body:
{
  "id  ": "<guid>", //Operation ID, should be provided in the patch operation API call
  "activityId": "<guid>", //not relevant
  "subscriptionId": "<guid>", // subscriptionId of the SaaS subscription for which this operation is relevant
  "offerId": "offer1", // purchased offer ID
  "publisherId": "contoso",
  "planId": "silver", // purchased plan ID
  "quantity": "20", // purchased amount of seats
  "action": "ChangePlan", // Can be ChangePlan, ChangeQuantity or Reinstate
  "timeStamp": "2018-12-01T00:00:00", // UTC
  "status": "InProgress", // Possible values: NotStarted, InProgress, Failed, Succeeded, Conflict (new quantity / plan is the same as existing)
  "errorStatusCode": "",
  "errorMessage": ""
}
```

Código: 403 Proibido. O token de autorização é inválido, expirou ou não foi fornecido.  A solicitação está tentando acessar uma assinatura de SaaS de uma oferta que foi publicada com uma ID de aplicativo do Azure AD diferente daquela usada para criar o token de autorização.

Esse erro geralmente é um sintoma de erros na realização do [registro de SaaS](pc-saas-registration.md). 

Código: 404 não encontrado.  

* Não foi localizada a assinatura com a `subscriptionId`.
* Não foi localizada a operação com a `operationId`.

Código: 500 Erro interno do servidor.  Repita a chamada à API.  Se o problema persistir, contate o [Suporte da Microsoft](https://partner.microsoft.com/support/v2/?stage=1).

#### <a name="update-the-status-of-an-operation"></a>Atualizar o status de uma operação

Use essa API para atualizar o status de uma operação pendente, a fim de indicar o êxito ou a falha dela no lado do distribuidor.

É possível obter a `operationId` dessa chamada à API usando o valor retornado pela **Localização da Operação**, a chamada à API obter operações pendentes ou o valor de parâmetro `<id>` recebido em uma chamada de webhook.

##### <a name="patch-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Aplicar patch `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*Parâmetros de consulta:*

|  Parâmetro         | Valor             |
|  ---------------   |  ---------------  |
|   `ApiVersion`       |  Use 2018-08-31.  |
|   `subscriptionId`   |  O identificador exclusivo da assinatura SaaS adquirida.  Essa ID é obtida depois de resolver o token de autorização do marketplace comercial usando a API de resolução.  |
|   `operationId`      |  O identificador exclusivo da operação que está sendo concluída. |

*Cabeçalhos de solicitação:*

|  Parâmetro         | Valor             |
|  ---------------   |  ---------------  |
|   `content-type`   | `application/json`   |
|   `x-ms-requestid`   |  Um valor de cadeia de caracteres exclusivo para acompanhamento da solicitação do cliente, preferencialmente um GUID.  Se esse valor não for fornecido, ele será gerado e fornecido nos cabeçalhos de resposta. |
|   `x-ms-correlationid` |  Um valor de cadeia de caracteres exclusiva para a operação no cliente.  Esse parâmetro correlaciona todos os eventos da operação do cliente com eventos no lado do servidor.  Se esse valor não for fornecido, ele será gerado e fornecido nos cabeçalhos de resposta. |
|  `authorization`     |  Um token de acesso exclusivo que identifica o distribuidor que está fazendo a chamada à API.  O formato é `"Bearer <access_token>"` quando o valor do token é recuperado pelo distribuidor, conforme explicado em [Obter um token com base no Aplicativo Azure AD](./pc-saas-registration.md#get-the-token-with-an-http-post). |

*Solicitar exemplo de conteúdo:*

```json
{
  "status": "Success" // Allowed Values: Success/Failure. Indicates the status of the operation on ISV side.
}
```

*Códigos de resposta:*

Código: 200 uma chamada para informar a conclusão de uma operação no lado do parceiro.  Por exemplo, essa resposta pode sinalizar a conclusão da alteração de estações ou de planos no lado do distribuidor.

Código: 403
- Negado.  O token de autorização não está disponível, é inválido ou expirou. Talvez a solicitação esteja tentando acessar uma assinatura que não pertence ao distribuidor atual.
- Negado.  O token de autorização é inválido, expirou ou não foi fornecido.  A solicitação está tentando acessar uma assinatura de SaaS de uma oferta que foi publicada com uma ID de aplicativo do Azure AD diferente daquela usada para criar o token de autorização.

Esse erro geralmente é um sintoma de erros na realização do [registro de SaaS](pc-saas-registration.md).

Código: 404 não encontrado.

* Não foi localizada a assinatura com a `subscriptionId`.
* Não foi localizada a operação com a `operationId`.

Código: 409 Conflito.  Por exemplo, uma atualização mais recente já foi atendida.

Código: 500 Erro interno do servidor.  Repita a chamada à API.  Se o problema persistir, contate o [Suporte da Microsoft](https://partner.microsoft.com/support/v2/?stage=1).

## <a name="implementing-a-webhook-on-the-saas-service"></a>Implementação de webhook no serviço de SaaS

Ao criar uma oferta de SaaS comercializável no Partner Center, o parceiro informa a URL do **webhook de conexão** a ser usada como um ponto de extremidade de HTTP.  A Microsoft chama esse webhook por meio da chamada POST de HTTP para comunicar ao lado do distribuidor a ocorrência dos seguintes eventos no lado da Microsoft:

* Quando a assinatura de SaaS está no estado *assinado*:
    * ChangePlan 
    * ChangeQuantity
    * Suspend
    * Cancelar assinatura
* Quando a assinatura de SaaS está no status *suspenso*:
    * Restabelecer
    * Cancelar assinatura

O distribuidor deve implementar um webhook no serviço de SaaS para manter o status da assinatura de SaaS consistente com o lado da Microsoft.  O serviço de SaaS é necessário para chamar a API Operação Get para validar e autorizar a chamada ao webhook e os dados do conteúdo antes de tomar medidas com base na notificação do webhook.  O distribuidor deve retornar HTTP 200 à Microsoft assim que a chamada de webhook for processada.  Esse valor confirma que o distribuidor recebeu a chamada ao webhook.

>[!Note]
>O serviço de URL do webhook deve estar em execução 24 horas, pronto para receber novas chamadas da Microsoft a qualquer momento.  A Microsoft tem uma política de repetição da chamada ao webhook (500 tentativas ao longo de 8 horas), mas, se o distribuidor não aceitar a chamada e retornar uma resposta, a operação referente à notificação do webhook acabará falhando no lado da Microsoft.

*Exemplos de conteúdo de webhook:*

```json
// end user changed a quantity of purchased seats for a plan on Microsoft side
{
  "id": "<guid>", // this is the operation ID to call with get operation API
  "activityId": "<guid>", // do not use
  "subscriptionId": "guid", // The GUID identifier for the SaaS resource which status changes
  "publisherId": "contoso", // A unique string identifier for each publisher
  "offerId": "offer1", // A unique string identifier for each offer
  "planId": "silver", // the most up-to-date plan ID
  "quantity": " 25", // the most up-to-date number of seats, can be empty if not relevant
  "timeStamp": "2019-04-15T20:17:31.7350641Z", // UTC time when the webhook was called
  "action": "ChangeQuantity", // the operation the webhook notifies about
  "status": "Success" // Can be either InProgress or Success
}
```

```json
// end user's payment instrument became valid again, after being suspended, and the SaaS subscription is being reinstated
{
  "id": "<guid>",
  "activityId": "<guid>",
  "subscriptionId": "<guid>",
  "publisherId": "contoso",
  "offerId": "offer2 ",
  "planId": "gold",
  "quantity": " 20",
  "timeStamp": "2019-04-15T20:17:31.7350641Z",
  "action": "Reinstate",
  "status": "In Progress"
}
```

## <a name="development-and-testing"></a>Desenvolvimento e teste

Para iniciar o processo de desenvolvimento, recomendamos criar respostas de API fictícias no lado do distribuidor.  As respostas podem ser baseadas nos exemplos contidos neste artigo.

Quando o distribuidor está pronto para o teste de ponta a ponta:

* Publique uma oferta de SaaS para um público-alvo de versão prévia limitado e mantenha-a na fase de versão prévia.
* Defina o preço do plano como 0 para evitar cobranças reais durante o teste.  Outra opção é definir um preço diferente de zero e cancelar todas as compras de teste dentro de 24 horas.
* Chame todos os fluxos de ponta a ponta, para simular um cenário de cliente real.
* Se o parceiro quiser testar o fluxo completo de compra e cobrança, faça isso com uma oferta com preço acima de US$ 0.  Haverá cobrança pela compra e geração de fatura.

É possível iniciar o fluxo de compra no portal do Azure ou nos sites do Microsoft AppSource, dependendo da publicação da oferta.

As ações *alterar plano*, *alterar quantidade* e *cancelar assinatura* são testadas no lado do distribuidor.  Do lado da Microsoft, *cancelar assinatura* pode ser iniciado no portal do Azure ou no Centro de Administração (o portal em que as compras do Microsoft AppSource são gerenciadas).  *Alterar a quantidade e o plano* só pode ser iniciado no Centro de Administração.

## <a name="get-support"></a>Obter suporte

Veja as opções de suporte ao distribuidor em [Suporte para o programa do marketplace comercial no Partner Center](../support.md).

## <a name="next-steps"></a>Próximas etapas

Veja mais opções de SaaS do marketplace comercial em [APIs do serviço de medição do marketplace comercial](marketplace-metering-service-apis.md).

Examine e use os [clientes para diferentes linguagens de programação e exemplos](https://github.com/microsoft/commercial-marketplace-samples).
