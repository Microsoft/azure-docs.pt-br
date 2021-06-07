---
title: Faça sua primeira chamada à API para acessar dados de análise do Marketplace comercial
description: Exemplos para aprender a usar a API para acessar dados de análise do Marketplace comercial.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 9e5fbdfca80d19f026a014a89ffbf137bacb521c
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105639583"
---
# <a name="make-your-first-api-call-to-access-commercial-marketplace-analytics-data"></a>Faça sua primeira chamada à API para acessar dados de análise do Marketplace comercial

Para obter uma lista das APIs para acessar dados de análise do Marketplace comercial, consulte [APIs para acessar dados de análise do Marketplace comercial](analytics-available-apis.md). Antes de fazer sua primeira chamada à API, certifique-se de que você atende aos [pré-requisitos](analytics-prerequisites.md) para acessar os dados de análise do Marketplace comercial programaticamente.

## <a name="token-generation"></a>Geração de token

Antes de chamar qualquer um dos métodos, você deve primeiro obter um token de acesso Azure Active Directory (AD do Azure). Você precisa passar o token de acesso do Azure AD para o cabeçalho de autorização de cada método na API. Depois de obter um token de acesso, você tem 60 minutos para usá-lo antes de expirar. Depois que o token expirar, você poderá atualizar o token e continuar a usá-lo para chamadas adicionais à API.

Consulte uma solicitação de exemplo abaixo para gerar um token. Os três valores necessários para gerar o token são `clientId` , `clientSecret` e `tenantId` . O `resource` parâmetro deve ser definido como `https://graph.windows.net` .

***Exemplo de solicitação***:

```json
curl --location --request POST 'https://login.microsoftonline.com/{TenantId}/oauth2/token' \
--header 'return-client-request-id: true' \
--header 'Content-Type: application/x-www-form-urlencoded' \
--data-urlencode 'resource=https://graph.windows.net' \
--data-urlencode 'client_id={client_id}' \
--data-urlencode 'client_secret={client_secret}' \
--data-urlencode 'grant_type=client_credentials'
```

***Exemplo de resposta***:

```json
{
    "token_type": "Bearer",
    "expires_in": "3599",
    "ext_expires_in": "3599",
    "expires_on": "1612794445",
    "not_before": "1612790545",
    "resource": "https://graph.windows.net",
    "access_token": {Token}
}
```

Para obter mais informações sobre como obter um token do Azure AD para seu aplicativo, consulte [acessar dados de análise usando os serviços de loja](/windows/uwp/monetize/access-analytics-data-using-windows-store-services#step-2-obtain-an-azure-ad-access-token).

## <a name="programmatic-api-call"></a>Chamada à API programática

Depois de obter o token do Azure AD, conforme descrito na seção anterior, siga estas etapas para criar seu primeiro relatório de acesso programático.

Os dados podem ser baixados do (DataSetName) a seguir:

- ISVCustomer
- ISVMarketplaceInsights
- ISVUsage
- ISVOrder

Nas seções a seguir, veremos exemplos de como acessar programaticamente `OrderId` a partir do conjunto de ISVOrder.

### <a name="step-1-make-a-rest-call-using-the-get-datasets-api"></a>Etapa 1: fazer uma chamada REST usando a API obter conjuntos de valores

A resposta da API fornece o nome do conjunto de relatórios de onde você pode baixar o relatório. Para o conjunto de informações específico, a resposta da API também fornece a lista de colunas selecionáveis que podem ser usadas para seu modelo de relatório personalizado. Você também pode consultar as tabelas a seguir para obter os nomes das colunas:

- [Tabela de detalhes de pedidos](orders-dashboard.md#orders-details-table)
- [Tabela de detalhes de uso](usage-dashboard.md#usage-details-table)
- [Tabela de detalhes do cliente](customer-dashboard.md#customer-details-table)
- [Tabela de detalhes do Marketplace insights](insights-dashboard.md#marketplace-insights-details-table)

***Exemplo de solicitação***:

```json
curl 
--location 
--request GET 'https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledDataset ' \ 
--header 'Authorization: Bearer <AzureADToken>'
```

***Exemplo de resposta***:

```json
{
    "value": [
        {
            "datasetName": "ISVOrder",
            "selectableColumns": [
                "MarketplaceSubscriptionId",
                "MonthStartDate",
                "OfferType",
                "AzureLicenseType",
                "Sku",
                "CustomerCountry",
                "IsPreviewSKU",
                "OrderId",
                "OrderQuantity",
                "CloudInstanceName",
                "IsNewCustomer",
                "OrderStatus",
                "OrderCancelDate",
                "CustomerCompanyName",
                "CustomerName",
                "OrderPurchaseDate",
                "OfferName",
                "TrialEndDate",
                "CustomerId",
                "BillingAccountId"
            ],
            "availableMetrics": [],
            "availableDateRanges": [
                "LAST_MONTH",
                "LAST_3_MONTHS",
                "LAST_6_MONTHS",
                "LIFETIME"
            ]
        },
    ],
    "totalCount": 1,
    "message": "Dataset fetched successfully",
    "statusCode": 200
}
```

### <a name="step-2-create-the-custom-query"></a>Etapa 2: criar a consulta personalizada

Nesta etapa, usaremos a ID do pedido do relatório pedidos para criar uma consulta personalizada para o relatório que desejamos. O padrão `timespan` se não for especificado na consulta é de seis meses.

***Exemplo de solicitação***:

```json
curl 
--location 
--request POST ' https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledQueries' \ 
--header ' Authorization: Bearer <AzureAD_Token>' \ 
--header 'Content-Type: application/json' \ 
--data-raw 
            '{ 
                "Query": "SELECT OrderId from ISVOrder", 
                "Name": "ISVOrderQuery1", 
                "Description": "Get a list of all Order IDs" 
             }'
```

***Exemplo de resposta***:

```json
{
    "value": [
        {
            "queryId": "78be43f2-e35f-491a-8cd5-78fe14194f9c",
            "name": "ISVOrderQuery1",
            "description": "Get a list of all Order IDs”,
            "query": "SELECT OrderId from ISVOrder",
            "type": "userDefined",
            "user": "142344300",
            "createdTime": "2021-01-06T05:38:34",
            "modifiedTime": null
        }
    ],
    "totalCount": 1,
    "message": "Query created successfully",
    "statusCode": 200
}
```

Na execução bem-sucedida da consulta, `queryId` é gerado um que precisa ser usado para gerar o relatório.

### <a name="step-3-execute-test-query-api"></a>Etapa 3: executar a API de consulta de teste

Nesta etapa, usaremos a API de consulta de teste para obter as 100 principais linhas para a consulta que foi criada.

***Exemplo de solicitação***:

```json
curl 
--location 
--request GET 'https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledQueries/testQueryResult?exportQuery=SELECT%20OrderId%20from%20ISVOrder' \ 
--header ' Authorization: Bearer <AzureADToken>'
```

***Exemplo de resposta***:

```json
{
    "value": [
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2ba8"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bb8"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bc8"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bd8"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2be8"
        },
               .
               .
               .

        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bf0"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bf1"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bf2"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bf3"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bf4"
        }
    ],
    "totalCount": 100,
    "message": null,
    "statusCode": 200
}
```

### <a name="step-4-create-the-report"></a>Etapa 4: criar o relatório

Nesta etapa, usaremos o gerado anteriormente `QueryId` para criar o relatório.

***Exemplo de solicitação***:

```json
curl 
--location 
--request POST 'https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport' \ 
--header ' Authorization: Bearer <AzureADToken>' \ 
--header 'Content-Type: application/json' \ 
--data-raw 
                 '{
                   “ReportName": "ISVReport1",
                   "Description": "Report for getting list of Order Ids",
                   "QueryId": "78be43f2-e35f-491a-8cd5-78fe14194f9c",
                   "StartTime": "2021-01-06T19:00:00Z”,
                   "RecurrenceInterval": 48,
                   "RecurrenceCount": 20,
                    "Format": "csv"
                  }'
```

<br>

_**Tabela 1: descrição dos parâmetros usados neste exemplo de solicitação**_

| Parâmetro | Descrição |
| ------------ | ------------- |
| `Description` | Forneça uma breve descrição do relatório que está sendo gerado. |
| `QueryId` | Esse é o `queryId` que foi gerado quando a consulta foi criada na etapa 2: criar consulta personalizada. |
| `StartTime` | Hora em que a primeira execução do relatório foi iniciada. |
| `RecurrenceInterval` | Intervalo de recorrência fornecido durante a criação do relatório. |
| `RecurrenceCount` | Contagem de recorrência fornecida durante a criação do relatório. |
| `Format` | Há suporte para formatos de arquivo CSV e TSV. |
|||

***Exemplo de resposta***:

```json
{
    "value": [
        {
            "reportId": "72fa95ab-35f5-4d44-a1ee-503abbc88003",
            "reportName": "ISVReport1",
            "description": "Report for getting list of Order Ids",
            "queryId": "78be43f2-e35f-491a-8cd5-78fe14194f9c",
            "query": "SELECT OrderId from ISVOrder",
            "user": "142344300",
            "createdTime": "2021-01-06T05:46:00Z",
            "modifiedTime": null,
            "startTime": "2021-01-06T19:00:00Z",
            "reportStatus": "Active",
            "recurrenceInterval": 48,
            "recurrenceCount": 20,
            "callbackUrl": null,
            "format": "csv"
        }
    ],
    "totalCount": 1,
    "message": "Report created successfully",
    "statusCode": 200
}
```

Após a execução bem-sucedida, `reportId` é gerado um que precisa ser usado para agendar um download do relatório.

### <a name="step-5-execute-report-executions-api"></a>Etapa 5: executar a API de execuções de relatório

Para obter o local seguro (URL) do relatório, agora executaremos a API de execuções de relatório.

***Exemplo de solicitação***:

```json
Curl
--location
--request GET 'https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport/execution/72fa95ab-35f5-4d44-a1ee-503abbc88003' \
--header ' Authorization: Bearer <AzureADToken>' \
```

***Exemplo de resposta***:

```json
{
    "value": [
        {
            "executionId": "1f18b53b-df30-4d98-85ee-e6c7e687aeed",
            "reportId": "72fa95ab-35f5-4d44-a1ee-503abbc88003",
            "recurrenceInterval": 48,
            "recurrenceCount": 20,
            "callbackUrl": null,
            "format": "csv",
            "executionStatus": "Pending",
            "reportAccessSecureLink": null,
            "reportExpiryTime": null,
            "reportGeneratedTime": null
        }
    ],
    "totalCount": 1,
    "message": null,
    "statusCode": 200
}
```

## <a name="next-steps"></a>Próximas etapas

- Você pode experimentar as APIs por meio da [URL da API do Swagger](https://swagger.io/docs/specification/api-host-and-base-path/)
- [Paradigma do acesso programático](analytics-programmatic-access.md)
