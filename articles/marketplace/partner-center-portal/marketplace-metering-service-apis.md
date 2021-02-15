---
title: APIs de serviço de medição – Marketplace comercial da Microsoft
description: A API de evento de uso permite emitir eventos de uso para ofertas de SaaS no Microsoft AppSource e no Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/26/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: b75964f8cfc41efc35858284dbffded3aa406eb6
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96436055"
---
# <a name="marketplace-metered-billing-apis"></a>APIs de cobrança limitada do Marketplace

As APIs de cobrança limitada devem ser usadas quando o Publicador cria dimensões de medição personalizadas para uma oferta a ser publicada no Partner Center. A integração com as APIs de cobrança limitadas é necessária para qualquer oferta adquirida que tenha um ou mais planos com dimensões personalizadas para emitir eventos de uso.

Para obter mais informações sobre como criar dimensões de medição personalizadas para SaaS, consulte [cobrança limitada de SaaS](saas-metered-billing.md).

Para obter mais informações sobre como criar dimensões de medição personalizadas para uma oferta de Aplicativo Azure com um plano de aplicativo gerenciado, consulte [configurar os detalhes da configuração da oferta de aplicativo do Azure](../create-new-azure-apps-offer.md#configure-your-azure-application-offer-setup-details).

## <a name="enforcing-tls-12-note"></a>Impondo a nota TLS 1,2

A versão do TLS versão 1,2 é imposta como a versão mínima para comunicações HTTPS. Certifique-se de usar essa versão de TLS em seu código. O TLS versão 1,0 e 1,1 são preteridos e as tentativas de conexão serão recusadas.

## <a name="metered-billing-single-usage-event"></a>Evento de uso único de cobrança medido

A API de evento de uso deve ser chamada pelo Publicador para emitir eventos de uso em um recurso ativo (assinado) para o plano adquirido pelo cliente específico. O evento de uso é emitido separadamente para cada dimensão personalizada do plano definido pelo publicador ao publicar a oferta.

Somente um evento de uso pode ser emitido para cada hora de um dia do calendário. Por exemplo, em 8:5:15 hoje, você pode emitir um evento de uso. Se esse evento for aceito, o próximo evento de uso será aceito de 9:00 hoje. Se você enviar um evento adicional entre 8:15 e 8:59:59 hoje, ele será rejeitado como duplicado. Você deve acumular todas as unidades consumidas em uma hora e, em seguida, emiti-las em um único evento.

Somente um evento de uso pode ser emitido para cada hora de um dia de calendário por recurso. Se mais de uma unidade for consumida em uma hora, o acumulará todas as unidades consumidas na hora e a emitirá em um único evento. Os eventos de uso só podem ser emitidos para as últimas 24 horas. Se você emitir um evento de uso a qualquer momento entre 8:00 e 8:59:59 (e ele for aceito) e enviar um evento adicional para o mesmo dia entre 8:00 e 8:59:59, ele será rejeitado como duplicado.

**POST**: `https://marketplaceapi.microsoft.com/api/usageEvent?api-version=<ApiVersion>`

*Parâmetros de consulta:*

| Parâmetro | Recomendação          |
| ---------- | ---------------------- |
| `ApiVersion` | Use 2018-08-31. |
| | |

*Cabeçalhos de solicitação:*

| Content-type       | Use `application/json`.  |
| ------------------ | ---------------------------- |
| `x-ms-requestid`     | Valor de cadeia de caracteres exclusivo para acompanhamento da solicitação do cliente, preferencialmente um GUID. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
| `x-ms-correlationid` | Valor de cadeia de caracteres exclusivo para a operação no cliente. Esse parâmetro correlaciona todos os eventos da operação do cliente com eventos no lado do servidor. Se esse valor não for fornecido, ele será gerado e fornecido nos cabeçalhos de resposta. |
| `authorization`   | Um token de acesso exclusivo que identifica o ISV que está fazendo essa chamada à API. O formato é `"Bearer <access_token>"` quando o valor do token é recuperado pelo Publicador, conforme explicado para <br> <ul> <li> SaaS em [obter o token com um http post](./pc-saas-registration.md#get-the-token-with-an-http-post). </li> <li> Aplicativo gerenciado em [estratégias de autenticação](./marketplace-metering-service-authentication.md). </li> </ul> |
| | |

*Exemplo de corpo da solicitação:*

```json
{
  "resourceId": <guid>, // unique identifier of the resource against which usage is emitted. 
  "quantity": 5.0, // how many units were consumed for the date and hour specified in effectiveStartTime, must be greater than 0, can be integer or float value
  "dimension": "dim1", // custom dimension identifier
  "effectiveStartTime": "2018-12-01T08:30:14", // time in UTC when the usage event occurred, from now and until 24 hours back
  "planId": "plan1", // id of the plan purchased for the offer
}
```

>[!NOTE]
>`resourceId` tem significado diferente para o aplicativo SaaS e para o aplicativo gerenciado que emite o medidor personalizado. 

Para Aplicativo Azure planos de aplicativos gerenciados, o `resourceId` é o aplicativo gerenciado `resource group Id` . Um script de exemplo para buscá-los pode ser encontrado em [usando o token de identidades gerenciadas pelo Azure](./marketplace-metering-service-authentication.md#using-the-azure-managed-identities-token). 

Para ofertas de SaaS, a `resourceId` é a ID de assinatura do SaaS. Para obter mais detalhes sobre assinaturas de SaaS, veja [listar assinaturas](./pc-saas-fulfillment-api-v2.md#get-list-of-all-subscriptions).

### <a name="responses"></a>Respostas

Código: 200<br>
OK. A emissão de uso foi aceita e registrada no lado da Microsoft para processamento adicional e cobrança.

Exemplo de carga de resposta: 

```json
{
  "usageEventId": <guid>, // unique identifier associated with the usage event in Microsoft records
  "status": "Accepted" // this is the only value in case of single usage event
  "messageTime": "2020-01-12T13:19:35.3458658Z", // time in UTC this event was accepted
  "resourceId": <guid>, // unique identifier of the resource against which usage is emitted. For SaaS it's the subscriptionId.
  "quantity": 5.0, // amount of emitted units as recorded by Microsoft
  "dimension": "dim1", // custom dimension identifier
  "effectiveStartTime": "2018-12-01T08:30:14", // time in UTC when the usage event occurred, as sent by the ISV
  "planId": "plan1", // id of the plan purchased for the offer
}
```

Código: 400 <br>
Solicitação inválida.

* Dados de solicitação ausentes ou inválidos fornecidos.
* `effectiveStartTime` Há mais de 24 horas no passado. O evento expirou.
* A assinatura SaaS não está no status assinado.

Exemplo de carga de resposta: 

```json
{
  "message": "One or more errors have occurred.",
  "target": "usageEventRequest",
  "details": [
    {
      "message": "The resourceId is required.",
      "target": "ResourceId",
      "code": "BadArgument"
    }
  ],
  "code": "BadArgument"
}
```

Código: 403<br>

Negado. O token de autorização não foi fornecido, é inválido ou expirou.  Ou a solicitação está tentando acessar uma assinatura para uma oferta que foi publicada com uma ID de Aplicativo Azure AD diferente da usada para criar o token de autorização.

Código: 409<br>
Conflito. Um evento de uso já foi relatado com êxito para a ID de recurso especificada, a data e a hora de uso efetivo.

Exemplo de carga de resposta: 

```json
{
  "additionalInfo": {
    "acceptedMessage": {
      "usageEventId": "<guid>", //unique identifier associated with the usage event in Microsoft records
      "status": "Duplicate",
      "messageTime": "2020-01-12T13:19:35.3458658Z",
      "resourceId": "<guid>", //unique identifier of the resource against which usage is emitted.
      "quantity": 1.0,
      "dimension": "dim1",
      "effectiveStartTime": "2020-01-12T11:03:28.14Z",
      "planId": "plan1"
    }
  },
  "message": "This usage event already exist.",
  "code": "Conflict"
}
```

## <a name="metered-billing-batch-usage-event"></a>Evento de uso do lote de cobrança medido

A API de evento de uso do lote permite que você emita eventos de uso para mais de um recurso comprado de uma só vez. Ele também permite que você emita vários eventos de uso para o mesmo recurso, desde que eles estejam em horas de calendário diferentes. O número máximo de eventos em um único lote é 25.

**POST:** `https://marketplaceapi.microsoft.com/api/batchUsageEvent?api-version=<ApiVersion>`

*Parâmetros de consulta:*

| Parâmetro  | Recomendação     |
| ---------- | -------------------- |
| `ApiVersion` | Use 2018-08-31. |

*Cabeçalhos de solicitação:*

| Content-type       | Use `application/json`.       |
| ------------------ | ------ |
| `x-ms-requestid`     | Valor de cadeia de caracteres exclusivo para acompanhamento da solicitação do cliente, preferencialmente um GUID. Se esse valor não for fornecido, ele será gerado e fornecido nos cabeçalhos de resposta. |
| `x-ms-correlationid` | Valor de cadeia de caracteres exclusivo para a operação no cliente. Esse parâmetro correlaciona todos os eventos da operação do cliente com eventos no lado do servidor. Se esse valor não for fornecido, ele será gerado e fornecido nos cabeçalhos de resposta. |
| `authorization`      | Um token de acesso exclusivo que identifica o ISV que está fazendo essa chamada à API. O formato é `Bearer <access_token>` quando o valor do token é recuperado pelo Publicador, conforme explicado para <br> <ul> <li> SaaS em [obter o token com um http post](./pc-saas-registration.md#get-the-token-with-an-http-post). </li> <li> Aplicativo gerenciado em [estratégias de autenticação](./marketplace-metering-service-authentication.md). </li> </ul> |
| | |


*Exemplo de corpo da solicitação:*

```json
{
  "request": [ // list of usage events for the same or different resources of the publisher
    { // first event
      "resourceId": "<guid1>", // Unique identifier of the resource against which usage is emitted. 
      "quantity": 5.0, // how many units were consumed for the date and hour specified in effectiveStartTime, must be greater than 0, can be integer or float value
      "dimension": "dim1", //Custom dimension identifier
      "effectiveStartTime": "2018-12-01T08:30:14",//Time in UTC when the usage event occurred, from now and until 24 hours back
      "planId": "plan1", // id of the plan purchased for the offer
    },
    { // next event
      "resourceId": "<guid2>", 
      "quantity": 39.0, 
      "dimension": "email", 
      "effectiveStartTime": "2018-11-01T23:33:10
      "planId": "gold", // id of the plan purchased for the offer
    }
  ]
}
```

>[!NOTE]
>`resourceId` tem significado diferente para o aplicativo SaaS e para o aplicativo gerenciado que emite o medidor personalizado. 

Para Aplicativo Azure planos de aplicativos gerenciados, o `resourceId` é o aplicativo gerenciado `resource group Id` . Um script de exemplo para buscá-los pode ser encontrado em [usando o token de identidades gerenciadas pelo Azure](./marketplace-metering-service-authentication.md#using-the-azure-managed-identities-token). 

Para ofertas de SaaS, a `resourceId` é a ID de assinatura do SaaS. Para obter mais detalhes sobre assinaturas de SaaS, veja [listar assinaturas](./pc-saas-fulfillment-api-v2.md#get-list-of-all-subscriptions).

### <a name="responses"></a>Respostas

Código: 200<br>
OK. A emissão de uso do lote foi aceita e registrada no lado da Microsoft para processamento adicional e cobrança. A lista de respostas é retornada com o status de cada evento individual no lote. Você deve iterar o conteúdo da resposta para entender as respostas para cada evento de uso individual enviado como parte do evento de lote.

Exemplo de carga de resposta: 

```json
{
  "count": 2, // number of records in the response
  "result": [
    { // first response
      "usageEventId": "<guid>", // unique identifier associated with the usage event in Microsoft records
      "status": "Accepted" // see list of possible statuses below,
      "messageTime": "2020-01-12T13:19:35.3458658Z", // Time in UTC this event was accepted by Microsoft,
      "resourceId": "<guid1>", // unique identifier of the resource against which usage is emitted.
      "quantity": 5.0, // amount of emitted units as recorded by Microsoft 
      "dimension": "dim1", // custom dimension identifier
      "effectiveStartTime": "2018-12-01T08:30:14",// time in UTC when the usage event occurred, as sent by the ISV
      "planId": "plan1", // id of the plan purchased for the offer
    },
    { // second response
      "status": "Duplicate",
      "messageTime": "0001-01-01T00:00:00",
      "error": {
        "additionalInfo": {
          "acceptedMessage": {
            "usageEventId": "<guid>",
            "status": "Duplicate",
            "messageTime": "2020-01-12T13:19:35.3458658Z",
            "resourceId": "<guid2>",
            "quantity": 1.0,
            "dimension": "email",
            "effectiveStartTime": "2020-01-12T11:03:28.14Z",
            "planId": "gold"
          }
        },
        "message": "This usage event already exist.",
        "code": "Conflict"
      },
      "resourceId": "<guid2>",
      "quantity": 1.0,
      "dimension": "email",
      "effectiveStartTime": "2020-01-12T11:03:28.14Z",
      "planId": "gold"
    }
  ]
}
```

Descrição do código de status referenciado na resposta da API `BatchUsageEvent`:

| Código de status  | Descrição |
| ---------- | -------------------- |
| `Accepted` | Aceita. |
| `Expired` | Uso expirado. |
| `Duplicate` | Uso duplicado fornecido. |
| `Error` | Código do erro. |
| `ResourceNotFound` | O recurso de uso fornecido é inválido. |
| `ResourceNotAuthorized` | Você não está autorizado a fornecer uso para este recurso. |
| `InvalidDimension` | A dimensão para a qual o uso foi passado é inválida para esta oferta/plano. |
| `InvalidQuantity` | A quantidade passada é menor ou igual a 0. |
| `BadArgument` | A entrada está ausente ou mal formulada. |

Código: 400<br>
Solicitação inválida. O lote continha mais de 25 eventos de uso.

Código: 403<br>
Negado. O token de autorização não foi fornecido, é inválido ou expirou.  Ou a solicitação está tentando acessar uma assinatura para uma oferta que foi publicada com uma ID de Aplicativo Azure AD diferente da usada para criar o token de autorização.

## <a name="development-and-testing-best-practices"></a>Práticas recomendadas de desenvolvimento e teste

Para testar a emissão de medidores personalizados, implemente a integração com a API de medição, crie um plano para sua oferta de SaaS publicada com dimensões personalizadas definidas com preço zero por unidade. E publicar essa oferta como visualização para que apenas usuários limitados possam acessar e testar a integração.

Você também pode usar o plano privado para uma oferta dinâmica existente para limitar o acesso a esse plano durante o teste para um público limitado.

## <a name="get-support"></a>Obter suporte

Siga a instrução em [suporte para o programa do Marketplace comercial no Partner Center](../support.md) para entender as opções de suporte do Publicador e abrir um tíquete de suporte com a Microsoft.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre APIs de serviço de medição, consulte [perguntas frequentes sobre APIs do serviço de medição do Marketplace](./marketplace-metering-service-apis-faq.md).
