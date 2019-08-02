---
title: APIs do serviço de medição do Marketplace | Azure Marketplace
description: Evento de uso para ofertas de SaaS no Azure Marketplace.
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.topic: conceptual
ms.date: 07/11/2019
ms.openlocfilehash: 35e6c61a8e8537035d70323c85dfc7a76f87cbcd
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/13/2019
ms.locfileid: "67869554"
---
# <a name="marketplace-metering-service-apis"></a>APIs de serviço de medição do Marketplace

A API de evento de uso permite que você emita eventos de uso para uma entidade comprada específica. A solicitação de evento de uso faz referência à dimensão de serviços de medição definida pelo publicador ao publicar a oferta.

## <a name="usage-event"></a>Evento de uso

**POST**:`https://marketplaceapi.microsoft.com/api/usageEvent?api-version=<ApiVersion>`

*Parâmetros de consulta:*

|            |          |
| ---------- | ---------------------- |
| `ApiVersion` | A versão da operação a ser usada para esta solicitação. A versão mais recente da API é 2018-08-31. |

*Cabeçalhos de solicitação:*

| Content-type       | `application/json`    |
| ------------------ | ---------------------------- |
| `x-ms-requestid`     | Valor de cadeia de caracteres exclusiva para acompanhar a solicitação do cliente, preferivelmente um GUID. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
| `x-ms-correlationid` | Valor de cadeia de caracteres exclusiva para a operação no cliente. Esse parâmetro correlaciona todos os eventos da operação do cliente com eventos no lado do servidor. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
| `authorization`   | [Obter token de portador JWT (token Web JSON).](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app) Observação: Ao fazer a solicitação HTTP, coloque `Bearer` o prefixo para o token obtido do link referenciado. |

*Solicitação:*

```json
{
  "resourceId": "Identifier of the resource against which usage is emitted",
  "quantity": 5.0,
  "dimension": "Dimension identifier",
  "effectiveStartTime": "Time in UTC when the usage event occurred",
  "planId": "Plan associated with the purchased offer"
}
```

### <a name="responses"></a>Responses

Código: 200<br>
OK 

```json
{
  "usageEventId": "Unique identifier associated with the usage event",
  "status": "Accepted",
  "messageTime": "Time this message was created in UTC",
  "resourceId": "Identifier of the resource against which usage is emitted",
  "quantity": 5.0,
  "dimension": "Dimension identifier",
  "effectiveStartTime": "Time in UTC when the usage event occurred",
  "planId": "Plan associated with the purchased offer"
}
```

Código: 400 <br>
Solicitação inválida, dados ausentes ou inválidos fornecidos ou expirados

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
Solicitação inválida, dados ausentes ou inválidos fornecidos ou expirados

```json
{
  "code": "Forbidden",
  "message": "User is not allowed authorized to call this"
}
```

Código: 409<br>
Conflito, quando recebemos a chamada de uso para a ID de recurso de uso e o uso efetivo, que já existe. A resposta `additionalInfo` conterá o campo que contém informações sobre a mensagem aceita.

```json
{
  "code": "Conflict",
  "additionalInfo": {
    "usageEventId": "Unique identifier associated with the usage event",
    "status": "Accepted|NotProcessed|Expired",
    "messageTime": "Time this message was created in UTC",
    "resourceId": "Identifier of the resource against which usage is emitted",
    "quantity": 5.0,
    "dimension": "Dimension identifier",
    "effectiveStartTime": "Time in UTC when the usage event occurred",
    "planId": "Plan associated with the purchased offer"
  }
}
```

## <a name="batch-usage-event"></a>Evento de uso do lote

A API de evento de uso do lote permite que você emita eventos de uso para mais de uma entidade comprada de uma só vez. A solicitação de evento de uso do lote faz referência à dimensão de serviços de medição definida pelo publicador ao publicar a oferta.

>[!Note]
>Você pode registrar várias ofertas de SaaS no Marketplace comercial da Microsoft. Cada oferta de SaaS registrada tem um aplicativo exclusivo do Azure AD que é registrado para fins de autenticação e autorização. Os eventos emitidos no lote devem pertencer a ofertas com o mesmo aplicativo do Azure AD no momento do registro da oferta.

**POST:** `https://marketplaceapi.microsoft.com/api/batchUsageEvent?api-version=<ApiVersion>`

*Parâmetros de consulta:*

|            |     |
| ---------- | -------------------- |
| `ApiVersion` | A versão da operação a ser usada para esta solicitação. A versão mais recente da API é 2018-08-31. |

*Cabeçalhos de solicitação:*

| Content-type       | `application/json`       |
| ------------------ | ------ |
| `x-ms-requestid`     | Valor de cadeia de caracteres exclusiva para acompanhar a solicitação do cliente, preferivelmente um GUID. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
| `x-ms-correlationid` | Valor de cadeia de caracteres exclusiva para a operação no cliente. Esse parâmetro correlaciona todos os eventos da operação do cliente com eventos no lado do servidor. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
| `authorization`      | [Obter token de portador JWT (token Web JSON).](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app) Observação: Ao fazer a solicitação HTTP, coloque `Bearer` o prefixo para o token obtido do link referenciado.  |

*Solicitação:*
```json
{
  "request": [
    {
      "resourceId": "Identifier of the resource against which usage is emitted",
      "quantity": 5.0,
      "dimension": "Dimension identifier",
      "effectiveStartTime": "Time in UTC when the usage event occurred",
      "planId": "Plan associated with the purchased offer"
    },
    {
      "resourceId": "Identifier of the resource against which usage is emitted",
      "quantity": 5.0,
      "dimension": "Dimension identifier",
      "effectiveStartTime": "Time in UTC when the usage event occurred",
      "planId": "Plan associated with the purchased offer"
    }
  ]
}
```
### <a name="responses"></a>Responses

Código: 200<br>
OK

```json
{
  "count": 2,
  "result": [
    {
      "usageEventId": "Unique identifier associated with the usage event",
      "status": "Accepted|Expired|Duplicate|Error|ResourceNotFound|ResourceNotAuthorized|InvalidDimension|BadArgument",
      "messageTime": "Time this message was created in UTC",
      "resourceId": "Identifier of the resource against which usage is emitted",
      "quantity": 5.0,
      "dimension": "Dimension identifier",
      "effectiveStartTime": "Time in UTC when the usage event occurred",
      "planId": "Plan associated with the purchased offer",
      "error": "Error object (optional)"
    },
    {
      "usageEventId": "Unique identifier associated with the usage event",
      "status": "Accepted|Expired|Duplicate|Error|ResourceNotFound|ResourceNotAuthorized|InvalidDimension|BadArgument",
      "messageTime": "Time this message was created in UTC",
      "resourceId": "Identifier of the resource against which usage is emitted",
      "quantity": 5.0,
      "dimension": "Dimension identifier",
      "effectiveStartTime": "Time in UTC when the usage event occurred",
      "planId": "Plan associated with the purchased offer",
      "error": "Error object (optional)"
    }
  ]
}
```

Descrição do código de status referenciado na resposta da `BatchUsageEvent` API:

| Código de status  | DESCRIÇÃO |
| ---------- | -------------------- |
| `Accepted` | Código aceito. |
| `Expired` | Uso expirado. |
| `Duplicate` | Uso duplicado fornecido. |
| `Error` | Código do erro. |
| `ResourceNotFound` | O recurso de uso fornecido é inválido. |
| `ResourceNotAuthorized` | Você não está autorizado a fornecer uso para este recurso. |
| `InvalidDimension` | A dimensão para a qual o uso é passado é inválida para esta oferta/plano. |
| `InvalidQuantity` | A quantidade passada é < 0. |
| `BadArgument` | A entrada está ausente ou malformada. |

Código: 400<br>
Solicitação inválida, dados ausentes ou inválidos fornecidos ou expirados

```json
{
  "message": "One or more errors have occurred.",
  "target": "usageEventRequest",
  "details": [
    {
      "message": "Invalid data format.",
      "target": "usageEventRequest",
      "code": "BadArgument"
    }
  ],
  "code": "BadArgument"
}
```
Código: 403<br>
O usuário não está autorizado a fazer esta chamada

```json
{
  "code": "Forbidden",
  "message": "User is not allowed to call this"
}
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte [cobrança limitada de SaaS](./saas-metered-billing.md).
