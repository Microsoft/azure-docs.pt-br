---
title: Revise os dados de faturamento de registro empresarial do Azure com a API REST
description: Aprenda a usar as APIs REST do Azure para revisar as informações de faturamento de inscrição empresarial.
author: lleonard-msft
ms.service: cost-management-billing
ms.subservice: enterprise
ms.topic: article
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 777c8549d79f820c1847e711969919df128cebd3
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92132407"
---
# <a name="review-enterprise-enrollment-billing-using-rest-apis"></a>Revise o faturamento de inscrição empresarial usando APIs REST

As APIs de relatórios do Azure ajudam você a revisar e gerenciar seus custos do Azure.

Neste artigo, você aprenderá a recuperar as informações de cobrança associadas às contas de cobrança, ao departamento ou às contas de registro de EA (Contrato Enterprise) usando as APIs REST do Azure.

## <a name="individual-account-billing"></a>Cobrança de conta individual

Para obter detalhes de uso de contas em um departamento:

```http
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.Consumption/usageDetails?api-version=2018-06-30
Content-Type: application/json   
Authorization: Bearer
```

O parâmetro `{billingAccountId}` é obrigatório e deve conter a ID de inscrição da conta.

Os cabeçalhos a seguir são necessários:

|Cabeçalho da solicitação|Descrição|  
|--------------------|-----------------|  
|*Tipo de Conteúdo:*|Obrigatórios. Defina como `application/json`.|  
|*Autorização:*|Obrigatórios. Defina para uma [chave de API](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) `Bearer` válida. |  

Este exemplo mostra uma chamada síncrona que retorna detalhes para o ciclo de faturamento atual. Por motivos de desempenho, as chamadas síncronas retornam informações do último mês.  Você também pode chamar a [API de forma assíncrona](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) para retornar dados por 36 meses.


## <a name="response"></a>Resposta  

O código de status 200 (OK) é retornado para uma resposta bem-sucedida, que contém uma lista de custos detalhados para a conta.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/BillingAccounts/1234/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/usageDetails/usageDetailsId1",
      "name": "usageDetailsId1",
      "type": "Microsoft.Consumption/usageDetails",
      "properties": {
        ...
        "usageStart": "2017-02-13T00:00:00Z",
        "usageEnd": "2017-02-13T23:59:59Z",
        "instanceName": "shared1",
        "instanceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Default-Web-eastasia/providers/Microsoft.Web/sites/shared1",
        "currency": "USD",
        "usageQuantity": 0.00328,
        "billableQuantity": 0.00328,
        "pretaxCost": 0.67,
        "isEstimated": false,
        ...
      }
    }
  ]
}
```  

Este exemplo é abreviado; consulte [Obter detalhes de uso de uma conta de cobrança](/rest/api/consumption/usagedetails/list#billingaccountusagedetailslist-legacy) para obter uma descrição completa de cada campo de resposta e tratamento de erro.

## <a name="department-billing"></a>Cobrança de departamento

Obtenha detalhes de uso agregados de todas as contas em um departamento.

```http
GET https://management.azure.com/providers/Microsoft.Billing/departments/{departmentId}/providers/Microsoft.Consumption/usageDetails?api-version=2018-06-30
Content-Type: application/json   
Authorization: Bearer
```

O parâmetro `{departmentId}` é obrigatório e deve conter a ID do departamento na conta de inscrição.

Os cabeçalhos a seguir são necessários:

|Cabeçalho da solicitação|Descrição|  
|--------------------|-----------------|  
|*Tipo de Conteúdo:*|Obrigatórios. Defina como `application/json`.|  
|*Autorização:*|Obrigatórios. Defina para uma [chave de API](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) `Bearer` válida. |  

Este exemplo mostra uma chamada síncrona que retorna detalhes para o ciclo de faturamento atual. Por motivos de desempenho, as chamadas síncronas retornam informações do último mês.  Você também pode chamar a [API de forma assíncrona](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) para retornar dados por 36 meses.

### <a name="response"></a>Resposta  

O código de status 200 (OK) é retornado para uma resposta bem-sucedida, que contém uma lista de informações detalhadas de uso e custos para um determinado período de cobrança e ID de fatura para o departamento.


O exemplo a seguir mostra a saída da API REST para o departamento `1234`.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/Departments/1234/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/usageDetails/usageDetailsId1",
      "name": "usageDetailsId1",
      "type": "Microsoft.Consumption/usageDetails",
      "properties": {
        "billingPeriodId": "/providers/Microsoft.Billing/Departments/1234/providers/Microsoft.Billing/billingPeriods/201702",
        "invoiceId": "/providers/Microsoft.Billing/Departments/1234/providers/Microsoft.Billing/invoices/201703-123456789",
        "usageStart": "2017-02-13T00:00:00Z",
        "usageEnd": "2017-02-13T23:59:59Z",
        "instanceName": "shared1",
        "instanceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Default-Web-eastasia/providers/Microsoft.Web/sites/shared1",
        "instanceLocation": "eastasia",
        "currency": "USD",
        "usageQuantity": 0.00328,
        "billableQuantity": 0.00328,
        "pretaxCost": 0.67,
        ...
      }
    }
  ]
}
```  

Este exemplo é abreviado; consulte [Obter detalhes de uso de um departamento](/rest/api/consumption/usagedetails/list#departmentusagedetailslist-legacy) para obter uma descrição completa de cada campo de resposta e tratamento de erro.

## <a name="enrollment-account-billing"></a>Cobrança de conta de inscrição

Obtenha os detalhes de uso agregados para a conta de inscrição.

```http
GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/{enrollmentAccountId}/providers/Microsoft.Consumption/usageDetails?api-version=2018-06-30
Content-Type: application/json   
Authorization: Bearer
```

O parâmetro `{enrollmentAccountId}` é obrigatório e deve conter a ID da conta de inscrição.

Os cabeçalhos a seguir são necessários:

|Cabeçalho da solicitação|Descrição|  
|--------------------|-----------------|  
|*Tipo de Conteúdo:*|Obrigatórios. Defina como `application/json`.|  
|*Autorização:*|Obrigatórios. Defina para uma [chave de API](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) `Bearer` válida. |  

Este exemplo mostra uma chamada síncrona que retorna detalhes para o ciclo de faturamento atual. Por motivos de desempenho, as chamadas síncronas retornam informações do último mês.  Você também pode chamar a [API de forma assíncrona](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) para retornar dados por 36 meses.

### <a name="response"></a>Resposta  

O código de status 200 (OK) é retornado para uma resposta bem-sucedida, que contém uma lista de informações detalhadas de uso e custos para um determinado período de cobrança e ID de fatura para o departamento.

O exemplo a seguir mostra a saída da API REST para a inscrição empresarial `1234`.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/EnrollmentAccounts/1234/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/usageDetails/usageDetailsId1",
      "name": "usageDetailsId1",
      "type": "Microsoft.Consumption/usageDetails",
      "properties": {
        "billingPeriodId": "/providers/Microsoft.Billing/EnrollmentAccounts/1234/providers/Microsoft.Billing/billingPeriods/201702",
        "invoiceId": "/providers/Microsoft.Billing/EnrollmentAccounts/1234/providers/Microsoft.Billing/invoices/201703-123456789",
        "usageStart": "2017-02-13T00:00:00Z",
        "usageEnd": "2017-02-13T23:59:59Z",
        ....
        "currency": "USD",
        "usageQuantity": 0.00328,
        "billableQuantity": 0.00328,
        "pretaxCost": 0.67,
        ...
      }
    }
  ]
}
```

Este exemplo é abreviado; consulte [Obter detalhes de uso de uma conta de inscrição](/rest/api/consumption/usagedetails/list#enrollmentaccountusagedetailslist-legacy) para obter uma descrição completa de cada campo de resposta e tratamento de erro.

## <a name="next-steps"></a>Próximas etapas
- Revise a [visão geral de relatórios corporativos](./enterprise-api.md)
- Investigue [API REST do Enterprise Billing](/rest/api/billing/)   
- [Iniciar com a API REST do Azure](/rest/api/azure/)