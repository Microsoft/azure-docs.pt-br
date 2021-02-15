---
title: Revise os dados de faturamento da assinatura do Azure com a API REST
description: Aprenda a usar as APIs REST do Azure para revisar os detalhes de faturamento da assinatura. Você pode usar filtros para ajudar a personalizar os resultados.
author: lleonard-msft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: article
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: b4d6502e49fdd30a68188a1e580a1c137984c89f
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92132373"
---
# <a name="review-subscription-billing-using-rest-apis"></a>Revise o faturamento de assinatura usando APIs REST

As APIs de relatórios do Azure ajudam você a revisar e gerenciar seus custos do Azure.

Os filtros ajudam a personalizar os resultados para atender às suas necessidades.

Aqui, você aprende a usar uma API REST para retornar detalhes de faturamento da assinatura em um determinado período.

``` http
GET https://management.azure.com/subscriptions/${subscriptionID}/providers/Microsoft.Billing/billingPeriods/${billingPeriod}/providers/Microsoft.Consumption/usageDetails?$filter=properties/usageEnd ge '${startDate}' AND properties/usageEnd le '${endDate}'
Content-Type: application/json
Authorization: Bearer
```

## <a name="build-the-request"></a>Criar a solicitação

O parâmetro `{subscriptionID}` é obrigatório e identifica a assinatura de destino.

O parâmetro `{billingPeriod}` é obrigatório e especifica um período de faturamento [atual](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods).

Os parâmetros `${startDate}` e `${endDate}` são obrigatórios para este exemplo, mas opcionais para o terminal. Eles especificam o intervalo de datas como sequências na forma de AAAA-MM-DD (exemplos: `'20180501'` e `'20180615'`).

Os cabeçalhos a seguir são necessários:

|Cabeçalho da solicitação|Descrição|
|--------------------|-----------------|
|*Tipo de Conteúdo:*|Obrigatórios. Defina como `application/json`.|
|*Autorização:*|Obrigatórios. Defina como um [token de acesso](/rest/api/azure/#authorization-code-grant-interactive-clients) `Bearer` válido. |

## <a name="response"></a>Resposta

O código de status 200 (OK) é retornado para uma resposta bem-sucedida, que contém uma lista de custos detalhados para sua conta.

``` json
{
  "value": [
    {
      "id": "/subscriptions/{$subscriptionID}/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/usageDetails/{$detailsID}",
      "name": "{$detailsID}",
      "type": "Microsoft.Consumption/usageDetails",
      "properties": {
        "billingPeriodId": "/subscriptions/${subscriptionID}/providers/Microsoft.Billing/billingPeriods/${billingPeriod}",
        "invoiceId": "/subscriptions/${subscriptionID}/providers/Microsoft.Billing/invoices/${invoiceID}",
        "usageStart": "${startDate}}",
        "usageEnd": "${endDate}",
        "currency": "USD",
        "usageQuantity": "${usageQuantity}",
        "billableQuantity": "${billableQuantity}",
        "pretaxCost": "${cost}",
        "meterId": "${meterID}",
        "meterDetails": "${meterDetails}"
      }
    }
  ],
  "nextLink": "${nextLinkURL}"
}
```

Cada item no **valor** representa detalhes sobre o uso de um serviço:

|Propriedade de resposta|Descrição|
|----------------|----------|
|**subscriptionGuid** | ID exclusivo global da assinatura. |
|**startDate** | Data em que o uso foi iniciado. |
|**endDate** | Data em que o uso foi encerrado. |
|**useageQuantity** | Quantidade utilizada. |
|**billableQuantity** | Quantidade faturada de fato. |
|**pretaxCost** | Custo faturado, antes dos impostos aplicáveis. |
|**meterDetails** | Informações detalhadas sobre o uso. |
|**nextLink**| Quando definido, especifica um URL para a próxima "página" de detalhes. Em branco quando a página é a última. |

Este exemplo é abreviado; consulte [Listar detalhes de uso](/rest/api/consumption/usagedetails/list#usagedetailslistforbillingperiod-legacy) para obter uma descrição completa de cada campo de resposta.

Outros códigos de status indicam condições de erro. Nestes casos, o objeto de resposta explica por que a solicitação falhou.

``` json
{
  "error": [
    {
      "code": "Error type.",
      "message": "Error response describing why the operation failed."
    }
  ]
}
```

## <a name="next-steps"></a>Próximas etapas
- Revise a [visão geral de relatórios corporativos](./enterprise-api.md)
- Investigue [API REST do Enterprise Billing](/rest/api/billing/)
- [Iniciar com a API REST do Azure](/rest/api/azure/)