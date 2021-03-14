---
title: APIs de cumprimento de SaaS V2 no Microsoft Commercial Marketplace
description: Saiba como criar e gerenciar uma oferta de SaaS no Microsoft AppSource e no Azure Marketplace usando as APIs de preenchimento versão 2.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 06/10/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: 2acf5178e7d1cfdf907146d733150a48e9696a5e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101712344"
---
# <a name="saas-fulfillment-apis-version-2-in-the-commercial-marketplace"></a>APIs de preenchimento de SaaS versão 2 no Marketplace comercial

Este artigo detalha as APIs que permitem que os parceiros vendam suas ofertas de SaaS (software como serviço) no Microsoft AppSource e no Azure Marketplace. Um Publicador é necessário para implementar a integração com essas APIs para publicar uma oferta de SaaS transactável no Partner Center.

## <a name="managing-the-saas-subscription-life-cycle"></a>Gerenciando o ciclo de vida da assinatura SaaS

O Marketplace comercial gerencia todo o ciclo de vida de uma assinatura de SaaS após sua compra pelo usuário final. Ele usa a página de aterrissagem, as APIs de preenchimento, as APIs de operações e o webhook como um mecanismo para direcionar a ativação, o uso, as atualizações e o cancelamento da assinatura de SaaS real. A conta do usuário final é baseada no estado da assinatura de SaaS que a Microsoft mantém. 

### <a name="states-of-a-saas-subscription"></a>Estados de uma assinatura de SaaS

O diagrama a seguir mostra os Estados de uma assinatura de SaaS e as ações aplicáveis.

![Diagrama mostrando o ciclo de vida de uma assinatura de software como serviço no Marketplace.](./media/saas-subscription-lifecycle-api-v2.png)

#### <a name="purchased-but-not-yet-activated-pendingfulfillmentstart"></a>Adquirido mas ainda não ativado (*PendingFulfillmentStart*)

Depois que um usuário final (ou CSP) comprar uma oferta de SaaS no Marketplace comercial, o Publicador deverá ser notificado sobre a compra. O Publicador pode criar e configurar uma nova conta SaaS no lado do Publicador para o usuário final.

Para que a criação da conta aconteça:

1. O cliente seleciona o botão **Configurar** que está disponível para uma oferta de SaaS após sua compra bem-sucedida no Microsoft AppSource ou no portal do Azure. Como alternativa, o cliente pode usar o botão **Configurar** no email que ele receberá logo após a compra.
2. Em seguida, a Microsoft notifica o parceiro sobre a compra abrindo na nova guia do navegador a URL da página de aterrissagem com o parâmetro de token (o token de identificação de compra do Marketplace comercial).

Um exemplo de tal chamada é `https://contoso.com/signup?token=<blob>` , enquanto a URL da página de aterrissagem para essa oferta de SaaS no Partner Center é configurada como `https://contoso.com/signup` . Esse token fornece ao Publicador uma ID que identifica exclusivamente a compra de SaaS e o cliente.

>[!NOTE]
>O Publicador não será notificado sobre a compra de SaaS até que o cliente inicie o processo de configuração do lado da Microsoft.

A URL da página de aterrissagem deve estar ativa e em execução o dia todo, todos os dias e pronto para receber novas chamadas da Microsoft em todos os momentos. Se a página de aterrissagem ficar indisponível, os clientes não poderão se inscrever no serviço SaaS e começar a usá-lo.

Em seguida, o Publicador deve passar o *token* de volta para a Microsoft chamando a [API de resolução de SaaS](#resolve-a-purchased-subscription)e inserindo o token como o valor do parâmetro de `x-ms-marketplace-token header` cabeçalho. Como resultado da chamada à API de resolução, o token é trocado para obter detalhes da compra de SaaS, como ID exclusiva da compra, ID da oferta adquirida e ID do plano comprado.

Na página de aterrissagem, o cliente deve estar conectado à conta SaaS nova ou existente por meio do SSO (logon único) do Azure Active Directory (Azure AD).

O Publicador deve implementar o SSO para fornecer a experiência do usuário exigida pela Microsoft para esse fluxo. Certifique-se de usar o aplicativo multilocatário do Azure AD e permitir contas corporativas e de estudante ou contas pessoais da Microsoft ao configurar o SSO. Esse requisito se aplica somente à página de aterrissagem para usuários que são redirecionados para o serviço SaaS quando já estão conectados com as credenciais da Microsoft. O SSO não é necessário para todas as entradas no serviço SaaS.

> [!NOTE]
>Se o SSO exigir que um administrador Conceda permissão a um aplicativo, a descrição da oferta no Partner Center deverá divulgar o acesso de nível de administrador necessário. Essa divulgação é para estar em conformidade com [as políticas de certificação do Marketplace comercial](/legal/marketplace/certification-policies#10003-authentication-options).

Após a entrada, o cliente deve concluir a configuração de SaaS no lado do Publicador. Em seguida, o Publicador deve chamar a [API ativar assinatura](#activate-a-subscription) para enviar um sinal ao Azure Marketplace de que o provisionamento da conta SaaS foi concluído.
Essa ação iniciará o ciclo de cobrança do cliente. Se a chamada à API ativar assinatura não for bem-sucedida, o cliente não será cobrado pela compra.


![Diagrama mostrando as chamadas P I para um cenário de provisionamento.](./media/saas-update-api-v2-calls-from-saas-service-a.png) 

#### <a name="active-subscribed"></a>Ativo (*assinado*)

*Ativo (assinado)* é o estado estacionário de uma assinatura SaaS provisionada. Depois que o lado da Microsoft tiver processado a chamada à [API ativar assinatura](#activate-a-subscription) , a assinatura de SaaS será marcada como *assinada*. O cliente agora pode usar o serviço SaaS no lado do Publicador e será cobrado.

Quando uma assinatura de SaaS já estiver ativa, o cliente poderá selecionar **gerenciar experiência de SaaS** no centro de administração portal do Azure ou Microsoft 365. Essa ação também faz com que a Microsoft chame a **URL da página de aterrissagem** com o parâmetro de *token* , como acontece no fluxo de ativação. O Publicador deve distinguir entre as novas compras e o gerenciamento de contas SaaS existentes e manipular essa chamada de URL da página de aterrissagem adequadamente.

#### <a name="being-updated-subscribed"></a>Sendo atualizado (*assinado*)

Essa ação significa que uma atualização para uma assinatura SaaS ativa existente está sendo processada pela Microsoft e pelo Publicador. Essa atualização pode ser iniciada por:

- O cliente do Marketplace comercial.
- O CSP do Marketplace comercial.
- O cliente do site SaaS do Publicador (mas não para compras feitas pelo CSP).

Dois tipos de atualizações estão disponíveis para uma assinatura de SaaS:

- Atualizar plano quando o cliente escolher outro plano para a assinatura.
- Atualize a quantidade quando o cliente alterar o número de estações compradas para a assinatura.

Somente uma assinatura ativa pode ser atualizada. Enquanto a assinatura está sendo atualizada, seu estado permanece ativo no lado da Microsoft.

##### <a name="update-initiated-from-the-commercial-marketplace"></a>Atualização iniciada do Marketplace comercial

Nesse fluxo, o cliente altera o plano de assinatura ou a quantidade de estações do portal do Azure ou Microsoft 365 centro de administração.

1. Depois que uma atualização for inserida, a Microsoft chamará a URL do webhook do editor, configurada no campo **conexão do webhook** no Partner Center, com um valor apropriado para a *ação* e outros parâmetros relevantes. 
1. O lado do Publicador deve fazer as alterações necessárias no serviço SaaS e notificar a Microsoft quando terminar chamando o [status de atualização da API de operação](#update-the-status-of-an-operation).
1. Se o patch for enviado com o status de *falha* , o processo de atualização não será concluído no lado da Microsoft. A assinatura de SaaS manterá o plano e a quantidade de estações existentes.

> [!NOTE]
> O Publicador deve invocar PATCH para [atualizar o status da API de operação](#update-the-status-of-an-operation) com uma resposta de falha/êxito *em uma janela de 10 segundos* depois de receber a notificação de webhook. Se o PATCH do status da operação não for recebido dentro de 10 segundos, o plano de alteração será *automaticamente corrigido como êxito*. 

A sequência de chamadas à API para um cenário de atualização iniciado no Marketplace comercial é mostrada no diagrama a seguir.

![Diagrama mostrando as chamadas P I para uma atualização iniciada pelo Marketplace.](./media/saas-update-status-api-v2-calls-marketplace-side.png)

##### <a name="update-initiated-from-the-publisher"></a>Atualização iniciada a partir do Publicador

Nesse fluxo, o cliente altera o plano de assinatura ou a quantidade de estações adquiridas do próprio serviço SaaS. 

1. O código do editor deve chamar a [API do plano de alteração](#change-the-plan-on-the-subscription) e/ou a API da quantidade de [alteração](#change-the-quantity-of-seats-on-the-saas-subscription) antes de fazer a alteração solicitada no lado do Publicador. 

1. A Microsoft aplicará a alteração à assinatura e, em seguida, notificará o Publicador por meio da **conexão do webhook** para aplicar a mesma alteração.

1. Somente então o Publicador fará a alteração necessária na assinatura de SaaS e notificará a Microsoft quando a alteração for feita chamando o [status de atualização da API de operação](#update-the-status-of-an-operation).

A sequência de chamadas à API para um cenário de atualização iniciado no lado do Publicador é mostrada no diagrama a seguir.

![Diagrama mostrando as chamadas P I para uma atualização iniciada pelo Publicador.](./media/saas-update-status-api-v2-calls-publisher-side.png)

#### <a name="suspended-suspended"></a>Suspenso (*suspenso*)

Esse estado indica que o pagamento de um cliente para o serviço SaaS não foi recebido. O Publicador será notificado dessa alteração no status da assinatura de SaaS pela Microsoft. A notificação é feita por meio de uma chamada para webhook com o parâmetro *Action* definido como *SUSPENDED*.

O Publicador pode ou não fazer alterações no serviço SaaS no lado do Publicador. Recomendamos que o Publicador disponibilize essas informações para o cliente suspenso e limite ou bloqueie o acesso do cliente ao serviço SaaS. Há uma probabilidade de que o pagamento nunca seja recebido.

A Microsoft dá ao cliente um período de carência de 30 dias antes de cancelar automaticamente a assinatura. Quando uma assinatura está no estado *suspenso* :

* O parceiro ou ISV deve manter a conta SaaS em um estado recuperável, para que a funcionalidade completa possa ser restaurada sem qualquer perda de dados ou configurações.
* O parceiro ou ISV deve esperar uma solicitação para restabelecer a assinatura, se o pagamento for recebido durante o período de carência ou uma solicitação para desprovisionar a assinatura no final do período de carência. As duas solicitações serão enviadas por meio do mecanismo de webhook.

O estado da assinatura é alterado para suspenso no lado da Microsoft antes de o Publicador executar qualquer ação. Somente assinaturas ativas podem ser suspensas.

#### <a name="reinstated-suspended"></a>Restabelecido (*suspenso*)

Essa ação indica que o instrumento de pagamento do cliente se tornou válido novamente, um pagamento foi feito para a assinatura de SaaS e a assinatura está sendo restabelecida. Nesse caso: 

1. A Microsoft chama o webhook com um parâmetro de *ação* definido para o valor de *restabelecimento* .
1. O Publicador garante que a assinatura esteja totalmente operacional novamente no lado do Publicador.
1. O Publicador chama a [API de operação de patch](#update-the-status-of-an-operation) com o status de êxito.
1. O processo de restabelecimento é bem-sucedido e o cliente é cobrado novamente para a assinatura de SaaS. 

Se o patch for enviado com status de *falha* , o processo reinstatement não será concluído no lado da Microsoft e a assinatura permanecerá *suspensa*.

Somente uma assinatura suspensa pode ser restabelecida. A assinatura SaaS suspensa permanece em um estado *suspenso* enquanto está sendo restabelecida. Após a conclusão dessa operação, o status da assinatura ficará *ativo*.

#### <a name="renewed-subscribed"></a>Renovado (*assinado*)

A assinatura de SaaS é renovada automaticamente pela Microsoft no final do período de assinatura de um mês ou um ano. O padrão para a configuração de renovação automática é *verdadeiro* para todas as assinaturas de SaaS. As assinaturas SaaS ativas continuarão a ser renovadas com uma cadência regular. A Microsoft não notifica o Publicador quando uma assinatura está sendo renovada. Um cliente pode desativar a renovação automática de uma assinatura de SaaS por meio do portal de administração do Microsoft 365. Nesse caso, a assinatura de SaaS será cancelada automaticamente no final da condição de cobrança atual. Os clientes também podem cancelar a assinatura de SaaS a qualquer momento.

Somente as assinaturas ativas são renovadas automaticamente. As assinaturas permanecem ativas durante o processo de renovação e, se a renovação automática for realizada com sucesso. Após a renovação, as datas de início e de término do período da assinatura são atualizadas para as datas do novo termo.

Se uma renovação automática falhar devido a um problema com o pagamento, a assinatura será *suspensa* e o Publicador será notificado.

#### <a name="canceled-unsubscribed"></a>Cancelado (*cancelar assinatura*) 

As assinaturas atingem esse estado em resposta a uma ação explícita de cliente ou CSP pelo cancelamento de uma assinatura no site do Publicador, no portal do Azure ou no centro de administração Microsoft 365. Uma assinatura também pode ser cancelada implicitamente, como resultado de não pagamento de dívidas, depois de estar no estado *suspenso* por 30 dias.

Depois que o Publicador recebe uma chamada de webhook de cancelamento, ele deve manter os dados do cliente para recuperação na solicitação por pelo menos sete dias. Somente os dados do cliente podem ser excluídos.

Uma assinatura de SaaS pode ser cancelada a qualquer momento em seu ciclo de vida. Depois que uma assinatura é cancelada, ela não pode ser reativada.

## <a name="api-reference"></a>Referência de API

Esta seção documenta as APIs de assinatura e de operações de SaaS.

As **APIs de assinatura** devem ser usadas para lidar com o ciclo de vida de assinatura de SaaS da compra para o cancelamento.

**As APIs de operações** devem ser usadas para:

* Verifique e confirme as chamadas de webhook processadas.
* Obtenha uma lista de operações pendentes de aplicativos que estão aguardando para serem confirmadas pelo Publicador.

> [!NOTE]
> A versão do TLS versão 1,2 será imposta em breve como a versão mínima para comunicações HTTPS. Certifique-se de usar essa versão de TLS em seu código. As versões 1,0 e 1,1 de TLS serão preteridas em breve.

### <a name="subscription-apis"></a>APIs de assinatura

#### <a name="resolve-a-purchased-subscription"></a>Resolver uma assinatura comprada

O ponto de extremidade de resolução permite que o Publicador troque o token de identificação de compra do Marketplace comercial (conhecido como *token* em [comprado, mas ainda não ativado](#purchased-but-not-yet-activated-pendingfulfillmentstart)) para uma ID de assinatura SaaS adquirida persistente e seus detalhes.

Quando um cliente é redirecionado para a URL da página de aterrissagem do parceiro, o token de identificação do cliente é passado como o parâmetro de *token* nesta chamada de URL. O parceiro deve usar esse token e fazer uma solicitação para resolvê-lo. A resposta resolver API contém a ID de assinatura de SaaS e outros detalhes para identificar exclusivamente a compra. O *token* fornecido com a chamada URL da página de aterrissagem geralmente é válido por 24 horas. Se o *token* que você receber já tiver expirado, recomendamos que você forneça as seguintes diretrizes para o usuário final:

"Não foi possível identificar esta compra. Reabra essa assinatura de SaaS no portal do Azure ou no centro de administração Microsoft 365 e selecione "configurar conta" ou "gerenciar conta" novamente. "

Chamar a API de resolução retornará os detalhes da assinatura e o status das assinaturas de SaaS em todos os status com suporte.

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
|  `authorization`     |  Um token de acesso exclusivo que identifica o Publicador que faz essa chamada à API. O formato é `"Bearer <accessaccess_token>"` quando o valor do token é recuperado pelo Publicador, conforme explicado em [obter um token baseado no aplicativo do Azure ad](./pc-saas-registration.md#get-the-token-with-an-http-post). |
|  `x-ms-marketplace-token`  | O parâmetro de *token* de identificação de compra a ser resolvido.  O token é passado na chamada da URL da página de aterrissagem quando o cliente é redirecionado para o site do parceiro SaaS (por exemplo: `https://contoso.com/signup?token=<token><authorization_token>` ). <br> <br>  Observe que o valor do *token* que está sendo codificado é parte da URL da página de aterrissagem, portanto, ele precisa ser decodificado antes de ser usado como um parâmetro nesta chamada à API.  <br> <br> Aqui está um exemplo de uma cadeia de caracteres codificada na URL: `contoso.com/signup?token=ab%2Bcd%2Fef` , em que *token* é `ab%2Bcd%2Fef` .  O mesmo token decodificado será: `Ab+cd/ef` |
| | |

*Códigos de resposta:*

Código: 200 retorna identificadores de assinatura SaaS exclusivos com base no `x-ms-marketplace-token` fornecido.

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

Código: 400 solicitação inadequada. `x-ms-marketplace-token` está ausente, malformado, inválido ou expirou.

Código: 403 Proibido. O token de autorização é inválido, expirou ou não foi fornecido.  A solicitação está tentando acessar uma assinatura de SaaS para uma oferta que foi publicada com uma ID de aplicativo do Azure AD diferente da usada para criar o token de autorização.

Esse erro geralmente é um sintoma de não executar corretamente o [registro de SaaS](pc-saas-registration.md) .

Código: 500 erro interno do servidor.  Repita a chamada à API.  Se o erro persistir, contate o [suporte da Microsoft](https://partner.microsoft.com/support/v2/?stage=1).

#### <a name="activate-a-subscription"></a>Ativar uma assinatura

Depois que a conta SaaS é configurada para um usuário final, o Publicador deve chamar a API ativar assinatura no lado da Microsoft.  O cliente não será cobrado, a menos que essa chamada de API seja bem-sucedida.

##### <a name="post-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidactivateapi-versionapiversion"></a>Postar `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/activate?api-version=<ApiVersion>`

*Parâmetros de consulta:*

|  Parâmetro         | Valor             |
|  --------   |  ---------------  |
| `ApiVersion`  |  Use 2018-08-31.   |
| `subscriptionId` | O identificador exclusivo da assinatura SaaS adquirida.  Essa ID é obtida depois de resolver o token de autorização do Marketplace comercial usando a [API de resolução](#resolve-a-purchased-subscription).
 |

*Cabeçalhos de solicitação:*

|  Parâmetro         | Valor             |
|  ---------------   |  ---------------  |
| `content-type`       |  `application/json`  |
| `x-ms-requestid`     |  Um valor de cadeia de caracteres exclusivo para acompanhamento da solicitação do cliente, preferencialmente um GUID.  Se esse valor não for fornecido, ele será gerado e fornecido nos cabeçalhos de resposta. |
| `x-ms-correlationid` |  Um valor de cadeia de caracteres exclusiva para a operação no cliente.  Essa cadeia de caracteres correlaciona todos os eventos da operação do cliente com eventos no lado do servidor.  Se esse valor não for fornecido, ele será gerado e fornecido nos cabeçalhos de resposta. |
| `authorization`      |  Um token de acesso exclusivo que identifica o Publicador que faz essa chamada à API. O formato é `"Bearer <access_token>"` quando o valor do token é recuperado pelo Publicador, conforme explicado em [obter um token baseado no aplicativo do Azure ad](./pc-saas-registration.md#get-the-token-with-an-http-post). |

*Exemplo de carga de solicitação:*

```json
{  // needed for validation of the activation request
  "planId": "gold", // purchased plan, cannot be empty
  "quantity": "" // purchased number of seats, can be empty if plan is not per seat
}
```

*Códigos de resposta:*

Código: 200 a assinatura foi marcada como assinada no lado da Microsoft.

Não há corpo de resposta para esta chamada.

Código: 400 solicitação inadequada: falha na validação.

* `planId` Não existe na carga de solicitação.
* `planId` a carga de solicitação não corresponde à que foi adquirida.
* `quantity` a carga de solicitação não corresponde à que foi adquirida
* A assinatura de SaaS está em um estado *assinado* ou *suspenso* .

Código: 403 Proibido. O token de autorização é inválido, expirou ou não foi fornecido. A solicitação está tentando acessar uma assinatura de SaaS para uma oferta que foi publicada com uma ID de aplicativo do Azure AD diferente da usada para criar o token de autorização.

Esse erro geralmente é um sintoma de não executar corretamente o [registro de SaaS](pc-saas-registration.md) .

Código: 404 não encontrado. A assinatura de SaaS está em um estado não *assinado* .

Código: 500 erro interno do servidor.  Repita a chamada à API.  Se o erro persistir, contate o [suporte da Microsoft](https://partner.microsoft.com/support/v2/?stage=1).

#### <a name="get-list-of-all-subscriptions"></a>Obter lista de todas as assinaturas

Essa API recupera uma lista de todas as assinaturas de SaaS adquiridas para todas as ofertas que o Publicador publica no Marketplace comercial.  As assinaturas de SaaS em todos os status possíveis serão retornadas. As assinaturas SaaS não assinadas também são retornadas, pois essas informações não são excluídas no lado da Microsoft.

A API retorna resultados paginados de 100 por página.

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionsapi-versionapiversion"></a>Obter `https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=<ApiVersion>`

*Parâmetros de consulta:*

|  Parâmetro         | Valor             |
|  --------   |  ---------------  |
| `ApiVersion`  |  Use 2018-08-31.  |
| `continuationToken`  | Parâmetro opcional. Para recuperar a primeira página de resultados, deixe em branco.  Use o valor retornado no `@nextLink` parâmetro para recuperar a próxima página. |

*Cabeçalhos de solicitação:*

|  Parâmetro         | Valor             |
|  ---------------   |  ---------------  |
| `content-type`       |  `application/json`  |
| `x-ms-requestid`     |  Um valor de cadeia de caracteres exclusivo para acompanhamento da solicitação do cliente, preferencialmente um GUID. Se esse valor não for fornecido, ele será gerado e fornecido nos cabeçalhos de resposta. |
| `x-ms-correlationid` |  Um valor de cadeia de caracteres exclusiva para a operação no cliente.  Esse parâmetro correlaciona todos os eventos da operação do cliente com eventos no lado do servidor.  Se esse valor não for fornecido, ele será gerado e fornecido nos cabeçalhos de resposta. |
| `authorization`      |  Um token de acesso exclusivo que identifica o Publicador que está fazendo esta chamada à API.  O formato é `"Bearer <access_token>"` quando o valor do token é recuperado pelo Publicador, conforme explicado em [obter um token baseado no aplicativo do Azure ad](./pc-saas-registration.md#get-the-token-with-an-http-post). |

*Códigos de resposta:*

Código: 200 retorna a lista de todas as assinaturas existentes para todas as ofertas feitas por esse Publicador, com base no token de autorização do editor.

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

Se nenhuma assinatura de SaaS adquirida for encontrada para este Publicador, o corpo de resposta vazio será retornado.

Código: 403 Proibido. O token de autorização não está disponível, é inválido ou expirou.

Esse erro geralmente é um sintoma de não executar corretamente o [registro de SaaS](pc-saas-registration.md) . 

Código: 500 erro interno do servidor. Repita a chamada à API.  Se o erro persistir, contate o [suporte da Microsoft](https://partner.microsoft.com/support/v2/?stage=1).

#### <a name="get-subscription"></a>Obter assinatura

Essa API recupera uma assinatura de SaaS adquirida especificada para uma oferta de SaaS que o Publicador publica no Marketplace comercial. Use essa chamada para obter todas as informações disponíveis para uma assinatura SaaS específica por sua ID em vez de chamar a API que é usada para obter uma lista de todas as assinaturas.

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Obter `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Parâmetros de consulta:*

|  Parâmetro         | Valor             |
|  ---------------   |  ---------------  |
| `ApiVersion`        |   Use 2018-08-31. |
| `subscriptionId`     |  O identificador exclusivo da assinatura SaaS adquirida.  Essa ID é obtida depois de resolver o token de autorização do Marketplace comercial usando a API de resolução. |

*Cabeçalhos de solicitação:*

|  Parâmetro         | Valor             |
|  ---------------   |  ---------------  |
|  `content-type`      |  `application/json`  |
|  `x-ms-requestid`    |  Um valor de cadeia de caracteres exclusivo para acompanhamento da solicitação do cliente, preferencialmente um GUID. Se esse valor não for fornecido, ele será gerado e fornecido nos cabeçalhos de resposta. |
|  `x-ms-correlationid` |  Um valor de cadeia de caracteres exclusiva para a operação no cliente.  Esse parâmetro correlaciona todos os eventos da operação do cliente com eventos no lado do servidor.  Se esse valor não for fornecido, ele será gerado e fornecido nos cabeçalhos de resposta. |
|  `authorization`     | Um token de acesso exclusivo que identifica o Publicador que está fazendo esta chamada à API. O formato é `"Bearer <access_token>"` quando o valor do token é recuperado pelo Publicador, conforme explicado em [obter um token baseado no aplicativo do Azure ad](./pc-saas-registration.md#get-the-token-with-an-http-post).  |

*Códigos de resposta:*

Código: 200 retorna detalhes para uma assinatura de SaaS com base no `subscriptionId` fornecido.

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

Código: 403 Proibido. O token de autorização é inválido, expirou ou não foi fornecido. A solicitação está tentando acessar uma assinatura de SaaS para uma oferta que é publicada com uma ID de aplicativo do Azure AD diferente da usada para criar o token de autorização.

Esse erro geralmente é um sintoma de não executar corretamente o [registro de SaaS](pc-saas-registration.md) . 

Código: 404 não encontrado.  A assinatura de SaaS com o especificado `subscriptionId` não pode ser encontrada.

Código: 500 erro interno do servidor.  Repita a chamada à API.  Se o erro persistir, contate o [suporte da Microsoft](https://partner.microsoft.com/support/v2/?stage=1).

#### <a name="list-available-plans"></a>Listar planos disponíveis

Essa API recupera todos os planos para uma oferta de SaaS identificada pelo `subscriptionId` de uma compra específica dessa oferta.  Use essa chamada para obter uma lista de todos os planos públicos e privados que o beneficiário de uma assinatura de SaaS pode atualizar para a assinatura.  Os planos retornados estarão disponíveis na mesma geografia que o plano já adquirido.

Essa chamada retorna uma lista de planos disponíveis para esse cliente, além do já adquirido.  A lista pode ser apresentada a um usuário final no site do Publicador.  Um usuário final pode alterar o plano de assinatura para qualquer um dos planos na lista retornada.  A alteração do plano para um que não esteja na lista falhará.

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidlistavailableplansapi-versionapiversion"></a>Obter `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/listAvailablePlans?api-version=<ApiVersion>`

*Parâmetros de consulta:*

|  Parâmetro         | Valor             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Use 2018-08-31.  |
|  `subscriptionId`    |  O identificador exclusivo da assinatura SaaS adquirida.  Essa ID é obtida depois de resolver o token de autorização do Marketplace comercial usando a API de resolução. |

*Cabeçalhos de solicitação:*

|  Parâmetro         | Valor             |
|  ---------------   |  ---------------  |
|   `content-type`     |  `application/json` |
|   `x-ms-requestid`   |  Um valor de cadeia de caracteres exclusivo para acompanhamento da solicitação do cliente, preferencialmente um GUID.  Se esse valor não for fornecido, ele será gerado e fornecido nos cabeçalhos de resposta. |
|  `x-ms-correlationid`  |  Um valor de cadeia de caracteres exclusiva para a operação no cliente.  Esse parâmetro correlaciona todos os eventos da operação do cliente com eventos no lado do servidor.  Se esse valor não for fornecido, ele será gerado e fornecido nos cabeçalhos de resposta. |
|  `authorization`     |  Um token de acesso exclusivo que identifica o Publicador que está fazendo esta chamada à API.  O formato é `"Bearer <access_token>"` quando o valor do token é recuperado pelo Publicador, conforme explicado em [obter um token baseado no aplicativo do Azure ad](./pc-saas-registration.md#get-the-token-with-an-http-post).  |

*Códigos de resposta:*

Código: 200 retorna uma lista de todos os planos disponíveis para uma assinatura de SaaS existente, incluindo aquele já adquirido.

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

Se `subscriptionId` não for encontrado, o corpo da resposta vazia será retornado.

Código: 403 Proibido. O token de autorização é inválido, expirou ou não foi fornecido.  A solicitação pode estar tentando acessar uma assinatura de SaaS para uma oferta que é publicada com uma ID de aplicativo do Azure AD diferente da usada para criar o token de autorização.

Esse erro geralmente é um sintoma de não executar corretamente o [registro de SaaS](pc-saas-registration.md) . 

Código: 500 erro interno do servidor.  Repita a chamada à API.  Se o erro persistir, contate o [suporte da Microsoft](https://partner.microsoft.com/support/v2/?stage=1).

#### <a name="change-the-plan-on-the-subscription"></a>Alterar o plano na assinatura

Use esta API para atualizar o plano existente adquirido para uma assinatura de SaaS para um novo plano (público ou privado).  O Publicador deve chamar essa API quando um plano é alterado no lado do Publicador para uma assinatura de SaaS adquirida no Marketplace comercial.

Essa API pode ser chamada somente para assinaturas *ativas* .  Qualquer plano pode ser alterado para qualquer outro plano existente (público ou privado), mas não para ele mesmo.  Para planos privados, o locatário do cliente deve ser definido como parte do público do plano no Partner Center.

##### <a name="patch-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Distribuído `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Parâmetros de consulta:*

|  Parâmetro         | Valor             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Use 2018-08-31.  |
| `subscriptionId`     | O identificador exclusivo da assinatura SaaS adquirida.  Essa ID é obtida depois de resolver o token de autorização do Marketplace comercial usando a API de resolução. |

*Cabeçalhos de solicitação:*
 
|  Parâmetro         | Valor             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json`  |
|  `x-ms-requestid`    | Um valor de cadeia de caracteres exclusivo para acompanhamento da solicitação do cliente, preferencialmente um GUID. Se esse valor não for fornecido, ele será gerado e fornecido nos cabeçalhos de resposta.  |
|  `x-ms-correlationid`  | Um valor de cadeia de caracteres exclusiva para a operação no cliente.  Esse parâmetro correlaciona todos os eventos da operação do cliente com eventos no lado do servidor.  Se esse valor não for fornecido, ele será gerado e fornecido nos cabeçalhos de resposta.  |
|  `authorization`     |  Um token de acesso exclusivo que identifica o Publicador que está fazendo esta chamada à API.  O formato é `"Bearer <access_token>"` quando o valor do token é recuperado pelo Publicador, conforme explicado em [obter um token baseado no aplicativo do Azure ad](./pc-saas-registration.md#get-the-token-with-an-http-post). |

*Exemplo de carga de solicitação:*

```json
{
  "planId": "gold" // the ID of the new plan to be purchased
}
```

*Códigos de resposta:*

Código: 202 a solicitação para alterar o plano foi aceita e manipulada de forma assíncrona.  O parceiro deve sondar a **URL do local de operação** para determinar um êxito ou uma falha da solicitação do plano de alteração.  A sondagem deve ser feita a cada vários segundos até que o status final de *falha*, *êxito* ou *conflito* seja recebido para a operação.  O status final da operação deve ser retornado rapidamente, mas pode levar vários minutos em alguns casos.

O parceiro também receberá uma notificação de webhook quando a ação estiver pronta para ser concluída com êxito no lado do mercado comercial.  Somente então o Publicador faz a alteração do plano no lado do Publicador.

*Cabeçalhos de resposta:*

|  Parâmetro         | Valor             |
|  ---------------   |  ---------------  |
|  `Operation-Location`        |  URL para obter o status da operação.  Por exemplo, `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=2018-08-31`. |

Código: 400 solicitação inadequada: falhas de validação.

* O novo plano não existe ou não está disponível para esta assinatura SaaS específica.
* O novo plano é o mesmo que o plano atual.
* O status da assinatura SaaS não está *assinado*.
* A operação de atualização para uma assinatura de SaaS não está incluída no `allowedCustomerOperations` .

Código: 403 Proibido. O token de autorização é inválido, expirou ou não foi fornecido.  A solicitação está tentando acessar uma assinatura de SaaS para uma oferta que é publicada com uma ID de aplicativo do Azure AD diferente da usada para criar o token de autorização.

Esse erro geralmente é um sintoma de não executar corretamente o [registro de SaaS](pc-saas-registration.md) .

Código: 404 não encontrado.  A assinatura de SaaS com `subscriptionId` não foi encontrada.

Código: 500 erro interno do servidor.  Repita a chamada à API.  Se o erro persistir, contate o [suporte da Microsoft](https://partner.microsoft.com/support/v2/?stage=1).

>[!NOTE]
>O plano ou a quantidade de estações podem ser alterados ao mesmo tempo, não ambos.

>[!Note]
>Essa API pode ser chamada somente depois de obter aprovação explícita para a alteração do usuário final.

#### <a name="change-the-quantity-of-seats-on-the-saas-subscription"></a>Alterar a quantidade de estações na assinatura de SaaS

Use essa API para atualizar (aumentar ou diminuir) a quantidade de estações compradas para uma assinatura de SaaS.  O Publicador deve chamar essa API quando o número de estações for alterado do lado do editor para uma assinatura de SaaS criada no Marketplace comercial.

A quantidade de assentos não pode ser maior que a quantidade permitida no plano atual.  Nesse caso, o Publicador deve alterar o plano antes de alterar a quantidade de estações.

##### <a name="patch-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Distribuído `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Parâmetros de consulta:*

|  Parâmetro         | Valor             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Use 2018-08-31.  |
|  `subscriptionId`     | Um identificador exclusivo da assinatura SaaS adquirida.  Essa ID é obtida depois de resolver o token de autorização do Marketplace comercial usando a API de resolução.  |

*Cabeçalhos de solicitação:*
 
|  Parâmetro         | Valor             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json`  |
|  `x-ms-requestid`    | Um valor de cadeia de caracteres exclusivo para acompanhamento da solicitação do cliente, preferencialmente um GUID.  Se esse valor não for fornecido, ele será gerado e fornecido nos cabeçalhos de resposta.  |
|  `x-ms-correlationid`  | Um valor de cadeia de caracteres exclusiva para a operação no cliente.  Esse parâmetro correlaciona todos os eventos da operação do cliente com eventos no lado do servidor.  Se esse valor não for fornecido, ele será gerado e fornecido nos cabeçalhos de resposta.  |
|  `authorization`     | Um token de acesso exclusivo que identifica o Publicador que está fazendo esta chamada à API.  O formato é `"Bearer <access_token>"` quando o valor do token é recuperado pelo Publicador, conforme explicado em [obter um token baseado no aplicativo do Azure ad](./pc-saas-registration.md#get-the-token-with-an-http-post).  |

*Exemplo de carga de solicitação:*

```json
{
  "quantity": 5 // the new amount of seats to be purchased
}
```

*Códigos de resposta:*

Código: 202 a solicitação para alterar a quantidade foi aceita e manipulada de forma assíncrona. O parceiro deve sondar a **URL do local de operação** para determinar um êxito ou uma falha da solicitação de alteração de quantidade.  A sondagem deve ser feita a cada vários segundos até que o status final de *falha*, *êxito* ou *conflito* seja recebido para a operação.  O status final da operação deve ser retornado rapidamente, mas pode levar vários minutos em alguns casos.

O parceiro também receberá uma notificação de webhook quando a ação estiver pronta para ser concluída com êxito no lado do mercado comercial.  Somente então o Publicador fará a alteração de quantidade no lado do Publicador.

*Cabeçalhos de resposta:*

|  Parâmetro         | Valor             |
|  ---------------   |  ---------------  |
|  `Operation-Location`        |  Link para um recurso para obter o status da operação.  Por exemplo, `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=2018-08-31`.  |

Código: 400 solicitação inadequada: falhas de validação.

* A nova quantidade é maior ou menor que o limite do plano atual.
* A nova quantidade está ausente.
* A nova quantidade é igual à quantidade atual.
* O status da assinatura SaaS não está assinado.
* A operação de atualização para uma assinatura de SaaS não está incluída no `allowedCustomerOperations` .

Código: 403 Proibido.  O token de autorização é inválido, expirou ou não foi fornecido.  A solicitação está tentando acessar uma assinatura que não pertence ao Publicador atual.

Esse erro geralmente é um sintoma de não executar corretamente o [registro de SaaS](pc-saas-registration.md) . 

Código: 404 não encontrado.  A assinatura de SaaS com `subscriptionId` não foi encontrada.

Código: 500 erro interno do servidor.  Repita a chamada à API.  Se o erro persistir, contate o [suporte da Microsoft](https://partner.microsoft.com/support/v2/?stage=1).

>[!Note]
>Somente um plano ou uma quantidade pode ser alterado ao mesmo tempo, não ambos.

>[!Note]
>Essa API pode ser chamada somente depois de obter aprovação explícita do usuário final para a alteração.

#### <a name="cancel-a-subscription"></a>Cancelar uma assinatura

Use essa API para cancelar a inscrição de uma assinatura SaaS especificada.  O Publicador não precisa usar essa API e recomendamos que os clientes sejam direcionados para o mercado comercial para cancelar assinaturas de SaaS.

Se o Publicador decidir implementar o cancelamento de uma assinatura de SaaS adquirida no Marketplace comercial no lado do Publicador, ele deverá chamar essa API.  Após a conclusão dessa chamada, o status da assinatura ficará *desassinado* no lado da Microsoft.

O cliente não será cobrado se uma assinatura for cancelada nos seguintes períodos de carência:

* Vinte e quatro horas para uma assinatura mensal após a ativação.
* Quatorze dias para uma assinatura anual após a ativação.

O cliente será cobrado se uma assinatura for cancelada após os períodos de carência anteriores.  O cliente perderá o acesso à assinatura de SaaS no lado da Microsoft imediatamente após o cancelamento. 

##### <a name="delete-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Apagar `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Parâmetros de consulta:*

|  Parâmetro         | Valor             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Use 2018-08-31.  |
|  `subscriptionId`     | O identificador exclusivo da assinatura SaaS adquirida.  Essa ID é obtida depois de resolver o token de autorização do Marketplace comercial usando a API de resolução.  |

*Cabeçalhos de solicitação:*
 
|  Parâmetro         | Valor             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json`  |
|  `x-ms-requestid`    | Um valor de cadeia de caracteres exclusivo para acompanhamento da solicitação do cliente, preferencialmente um GUID.  Se esse valor não for fornecido, ele será gerado e fornecido nos cabeçalhos de resposta.  |
|  `x-ms-correlationid`  | Um valor de cadeia de caracteres exclusiva para a operação no cliente.  Esse parâmetro correlaciona todos os eventos da operação do cliente com eventos no lado do servidor.  Se esse valor não for fornecido, ele será gerado e fornecido nos cabeçalhos de resposta.  |
|  `authorization`     |  Um token de acesso exclusivo que identifica o Publicador que faz essa chamada à API.  O formato é `"Bearer <access_token>"` quando o valor do token é recuperado pelo Publicador, conforme explicado em [obter um token baseado no aplicativo do Azure ad](./pc-saas-registration.md#get-the-token-with-an-http-post). |

*Códigos de resposta:*

Código: 202 a solicitação para cancelar a assinatura foi aceita e manipulada de forma assíncrona.  O parceiro deve sondar a **URL do local de operação** para determinar um êxito ou uma falha dessa solicitação.  A sondagem deve ser feita a cada vários segundos até que o status final de *falha*, *êxito* ou *conflito* seja recebido para a operação.  O status final da operação deve ser retornado rapidamente, mas pode levar vários minutos em alguns casos.

O parceiro também receberá uma notificação de webhook quando a ação for concluída com êxito no lado do mercado comercial.  Somente então o Publicador deve cancelar a assinatura no lado do Publicador.

*Cabeçalhos de resposta:*

|  Parâmetro         | Valor             |
|  ---------------   |  ---------------  |
|  `Operation-Location`        |  Link para um recurso para obter o status da operação.  Por exemplo, `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=2018-08-31`. |

Código: 400 solicitação inadequada.  Excluir não está na `allowedCustomerOperations` lista para esta assinatura de SaaS.

Código: 403 Proibido.  O token de autorização é inválido, expirou ou não está disponível. A solicitação está tentando acessar uma assinatura de SaaS para uma oferta que é publicada com uma ID de aplicativo do Azure AD diferente da usada para criar o token de autorização.

Esse erro geralmente é um sintoma de não executar corretamente o [registro de SaaS](pc-saas-registration.md) .

Código: 404 não encontrado.  A assinatura de SaaS com `subscriptionId` não foi encontrada.

Código: 500 erro interno do servidor. Repita a chamada à API.  Se o erro persistir, contate o [suporte da Microsoft](https://partner.microsoft.com/support/v2/?stage=1).

### <a name="operations-apis"></a>APIs de operações

#### <a name="list-outstanding-operations"></a>Listar operações pendentes 

Obter a lista das operações pendentes para a assinatura SaaS especificada.  O Publicador deve reconhecer as operações retornadas chamando a [API de patch de operação](#update-the-status-of-an-operation).

No momento, apenas **as operações de rerestabelecimento** são retornadas como resposta para essa chamada à API.

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsapi-versionapiversion"></a>Obter `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations?api-version=<ApiVersion>`

*Parâmetros de consulta:*

|  Parâmetro         | Valor             |
|  ---------------   |  ---------------  |
|    `ApiVersion`    |  Use 2018-08-31.         |
|    `subscriptionId` | O identificador exclusivo da assinatura SaaS adquirida.  Essa ID é obtida depois de resolver o token de autorização do Marketplace comercial usando a API de resolução.  |

*Cabeçalhos de solicitação:*
 
|  Parâmetro         | Valor             |
|  ---------------   |  ---------------  |
|  `content-type`     |  `application/json` |
|  `x-ms-requestid`    |  Um valor de cadeia de caracteres exclusivo para acompanhamento da solicitação do cliente, preferencialmente um GUID.  Se esse valor não for fornecido, ele será gerado e fornecido nos cabeçalhos de resposta.  |
|  `x-ms-correlationid` |  Um valor de cadeia de caracteres exclusiva para a operação no cliente.  Esse parâmetro correlaciona todos os eventos da operação do cliente com eventos no lado do servidor.  Se esse valor não for fornecido, ele será gerado e fornecido nos cabeçalhos de resposta.  |
|  `authorization`     |  O formato é `"Bearer <access_token>"` quando o valor do token é recuperado pelo Publicador, conforme explicado em [obter um token baseado no aplicativo do Azure ad](./pc-saas-registration.md#get-the-token-with-an-http-post).  |

*Códigos de resposta:*

Código: 200 retorna a operação de reaplicar pendente na assinatura SaaS especificada.

*Exemplo de carga de resposta:*

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

Código: 400 solicitação inadequada: falhas de validação.

Código: 403 Proibido. O token de autorização é inválido, expirou ou não foi fornecido.  A solicitação está tentando acessar uma assinatura de SaaS para uma oferta que é publicada com uma ID de aplicativo do Azure AD diferente da usada para criar o token de autorização.

Esse erro geralmente é um sintoma de não executar corretamente o [registro de SaaS](pc-saas-registration.md) . 

Código: 404 não encontrado.  A assinatura de SaaS com `subscriptionId` não foi encontrada.

Código: 500 erro interno do servidor. Repita a chamada à API.  Se o erro persistir, contate o [suporte da Microsoft](https://partner.microsoft.com/support/v2/?stage=1).

#### <a name="get-operation-status"></a>Obter status da operação

Essa API permite que o Publicador acompanhe o status da operação assíncrona especificada:  **cancelamento de assinatura**, **ChangePlan** ou **ChangeQuantity**.

O `operationId` para essa chamada à API pode ser recuperado do valor retornado pelo **local de operação**, a chamada de API de operações de obtenção pendente ou o `<id>` valor de parâmetro recebido em uma chamada de webhook.

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Obter `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*Parâmetros de consulta:*

|  Parâmetro         | Valor             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Use 2018-08-31.  |
|  `subscriptionId`    |  O identificador exclusivo da assinatura SaaS adquirida.  Essa ID é obtida depois de resolver o token de autorização do Marketplace comercial usando a API de resolução. |
|  `operationId`       |  O identificador exclusivo da operação que está sendo recuperada. |

*Cabeçalhos de solicitação:*

|  Parâmetro         | Valor             |
|  ---------------   |  ---------------  |
|  `content-type`      |  `application/json`   |
|  `x-ms-requestid`    |  Um valor de cadeia de caracteres exclusivo para acompanhamento da solicitação do cliente, preferencialmente um GUID.  Se esse valor não for fornecido, ele será gerado e fornecido nos cabeçalhos de resposta. |
|  `x-ms-correlationid` |  Um valor de cadeia de caracteres exclusiva para a operação no cliente.  Esse parâmetro correlaciona todos os eventos da operação do cliente com eventos no lado do servidor.  Se esse valor não for fornecido, ele será gerado e fornecido nos cabeçalhos de resposta.  |
|  `authorization`     |  Um token de acesso exclusivo que identifica o Publicador que faz essa chamada à API.  O formato é `"Bearer <access_token>"` quando o valor do token é recuperado pelo Publicador, conforme explicado em [obter um token baseado no aplicativo do Azure ad](./pc-saas-registration.md#get-the-token-with-an-http-post).  |

*Códigos de resposta:*

Código: 200 Obtém detalhes para a operação SaaS especificada. 

*Exemplo de carga de resposta:*

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

Código: 403 Proibido. O token de autorização é inválido, expirou ou não foi fornecido.  A solicitação está tentando acessar uma assinatura de SaaS para uma oferta que é publicada com uma ID de aplicativo do Azure AD diferente da usada para criar o token de autorização.

Esse erro geralmente é um sintoma de não executar corretamente o [registro de SaaS](pc-saas-registration.md) . 

Código: 404 não encontrado.  

* A assinatura com `subscriptionId` não foi encontrada.
* A operação com `operationId` não foi encontrada.

Código: 500 erro interno do servidor.  Repita a chamada à API.  Se o erro persistir, contate o [suporte da Microsoft](https://partner.microsoft.com/support/v2/?stage=1).

#### <a name="update-the-status-of-an-operation"></a>Atualizar o status de uma operação

Use essa API para atualizar o status de uma operação pendente para indicar o êxito ou a falha da operação no lado do Publicador.

O `operationId` para essa chamada à API pode ser recuperado do valor retornado pelo **local de operação**, a chamada de API de operações de obtenção pendente ou o `<id>` valor de parâmetro recebido em uma chamada de webhook.

##### <a name="patch-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Distribuído `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*Parâmetros de consulta:*

|  Parâmetro         | Valor             |
|  ---------------   |  ---------------  |
|   `ApiVersion`       |  Use 2018-08-31.  |
|   `subscriptionId`   |  O identificador exclusivo da assinatura SaaS adquirida.  Essa ID é obtida depois de resolver o token de autorização do Marketplace comercial usando a API de resolução.  |
|   `operationId`      |  O identificador exclusivo da operação que está sendo concluída. |

*Cabeçalhos de solicitação:*

|  Parâmetro         | Valor             |
|  ---------------   |  ---------------  |
|   `content-type`   | `application/json`   |
|   `x-ms-requestid`   |  Um valor de cadeia de caracteres exclusivo para acompanhamento da solicitação do cliente, preferencialmente um GUID.  Se esse valor não for fornecido, ele será gerado e fornecido nos cabeçalhos de resposta. |
|   `x-ms-correlationid` |  Um valor de cadeia de caracteres exclusiva para a operação no cliente.  Esse parâmetro correlaciona todos os eventos da operação do cliente com eventos no lado do servidor.  Se esse valor não for fornecido, ele será gerado e fornecido nos cabeçalhos de resposta. |
|  `authorization`     |  Um token de acesso exclusivo que identifica o Publicador que está fazendo esta chamada à API.  O formato é `"Bearer <access_token>"` quando o valor do token é recuperado pelo Publicador, conforme explicado em [obter um token baseado no aplicativo do Azure ad](./pc-saas-registration.md#get-the-token-with-an-http-post). |

*Exemplo de carga de solicitação:*

```json
{
  "status": "Success" // Allowed Values: Success/Failure. Indicates the status of the operation on ISV side.
}
```

*Códigos de resposta:*

Código: 200 uma chamada para informar a conclusão de uma operação no lado do parceiro.  Por exemplo, essa resposta pode sinalizar a conclusão da alteração de estações ou planos no lado do Publicador.

Código: 403
- Negado.  O token de autorização não está disponível, é inválido ou expirou. A solicitação pode estar tentando acessar uma assinatura que não pertence ao Publicador atual.
- Negado.  O token de autorização é inválido, expirou ou não foi fornecido.  A solicitação está tentando acessar uma assinatura de SaaS para uma oferta que é publicada com uma ID de aplicativo do Azure AD diferente da usada para criar o token de autorização.

Esse erro geralmente é um sintoma de não executar corretamente o [registro de SaaS](pc-saas-registration.md) .

Código: 404 não encontrado.

* A assinatura com `subscriptionId` não foi encontrada.
* A operação com `operationId` não foi encontrada.

Código: 409 conflito.  Por exemplo, uma atualização mais recente já foi atendida.

Código: 500 erro interno do servidor.  Repita a chamada à API.  Se o erro persistir, contate o [suporte da Microsoft](https://partner.microsoft.com/support/v2/?stage=1).

## <a name="implementing-a-webhook-on-the-saas-service"></a>Implementando um webhook no serviço SaaS

Ao criar uma oferta de SaaS transactável no Partner Center, o parceiro fornece a URL de **webhook de conexão** a ser usada como um ponto de extremidade http.  Esse webhook é chamado pela Microsoft usando a chamada POST HTTP para notificar o lado do Publicador sobre os seguintes eventos que ocorrem no lado da Microsoft:

* Quando a assinatura de SaaS estiver em status de *assinatura* :
    * ChangePlan 
    * ChangeQuantity
    * Suspend
    * Cancelar assinatura
* Quando a assinatura SaaS está em status *suspenso* :
    * Restabelecer
    * Cancelar assinatura

O Publicador deve implementar um webhook no serviço SaaS para manter o status da assinatura SaaS consistente com o lado da Microsoft.  O serviço SaaS é necessário para chamar a API de operação get para validar e autorizar os dados de chamada e carga de webhook antes de tomar medidas com base na notificação de webhook.  O Publicador deve retornar o HTTP 200 para a Microsoft assim que a chamada de webhook for processada.  Esse valor confirma que a chamada de webhook foi recebida com êxito pelo Publicador.

>[!Note]
>O serviço de URL do webhook deve estar em execução 24x7 e pronto para receber novas chamadas do tempo da Microsoft em todos os momentos.  A Microsoft tem uma política de repetição para a chamada de webhook (500 tentativas mais de 8 horas), mas se o Publicador não aceitar a chamada e retornar uma resposta, a operação pela qual o webhook notificará eventualmente falhará no lado da Microsoft.

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

Para iniciar o processo de desenvolvimento, é recomendável criar respostas de API fictícias no lado do Publicador.  Essas respostas podem ser baseadas em respostas de exemplo fornecidas neste artigo.

Quando o Publicador estiver pronto para o teste de ponta a ponta:

* Publicar uma oferta de SaaS em um público de visualização limitado e mantê-la no estágio de visualização.
* Defina o preço do plano como 0 para evitar o disparo das despesas de cobrança reais durante o teste.  Outra opção é definir um preço diferente de zero e cancelar todas as compras de teste dentro de 24 horas.
* Verifique se todos os fluxos são invocados de ponta a ponta, para simular um cenário de cliente real.
* Se o parceiro quiser testar a compra completa e o fluxo de cobrança, faça isso com a oferta que tem o preço acima de $0.  A compra será cobrada e uma fatura será gerada.

Um fluxo de compra pode ser disparado a partir do portal do Azure ou Microsoft AppSource sites, dependendo de onde a oferta está sendo publicada.

As ações *Alterar plano*, *Alterar quantidade* e *cancelar assinatura* são testadas no lado do Publicador.  Do lado da Microsoft, o *cancelamento da assinatura* pode ser disparado tanto do portal do Azure quanto do centro de administração (o portal onde Microsoft AppSource compras são gerenciadas).  *Alterar a quantidade e o plano* só podem ser disparados no centro de administração.

## <a name="get-support"></a>Obter suporte

Consulte [suporte para o programa do Marketplace comercial no Partner Center](../support.md) para obter opções de suporte do Publicador.

## <a name="next-steps"></a>Próximas etapas

Consulte as [APIs do serviço de medição do Marketplace comercial](marketplace-metering-service-apis.md) para obter mais opções de ofertas de SaaS no Marketplace comercial.

Revise e use os [clientes para diferentes linguagens de programação e exemplos](https://github.com/microsoft/commercial-marketplace-samples).
