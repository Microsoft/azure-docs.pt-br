---
title: Fazer a migração de relatórios Enterprise para as APIs do Azure Resource Manager
description: Este artigo ajuda você a entender as diferenças entre as APIs de Relatório e as APIs do Azure Resource Manager, o que esperar quando você fizer a migração para as APIs do Azure Resource Manager e as novas funcionalidades disponíveis com as novas APIs do Azure Resource Manager.
author: bandersmsft
ms.reviewer: adwise
ms.service: cost-management-billing
ms.subservice: common
ms.topic: reference
ms.date: 11/19/2020
ms.author: banders
ms.openlocfilehash: 93dda4fc3a152b0a07a95ff327c9ea619f25787c
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96355806"
---
# <a name="migrate-from-enterprise-reporting-to-azure-resource-manager-apis"></a>Fazer a migração de relatórios Enterprise para as APIs do Azure Resource Manager

Este artigo ajuda os desenvolvedores que criaram soluções personalizadas usando as [APIs de Relatórios do Azure para clientes Enterprise](../manage/enterprise-api.md) a fazer a migração para as APIs do Azure Resource Manager para Gerenciamento de Custos. O suporte da Entidade de Serviço para as APIs do Azure Resource Manager mais recentes já está em disponibilidade geral. As APIs do Azure Resource Manager estão em desenvolvimento ativo. Considere fazer a migração para elas em vez de usar as APIs de Relatórios do Azure mais antigas para clientes Enterprise. As APIs mais antigas estão sendo preteridas. Este artigo ajuda você a entender as diferenças entre as APIs de Relatório e as APIs do Azure Resource Manager, o que esperar quando você fizer a migração para as APIs do Azure Resource Manager e as novas funcionalidades disponíveis com as novas APIs do Azure Resource Manager.

## <a name="api-differences"></a>Diferenças de API

As informações a seguir descrevem as diferenças entre as APIs de Relatórios para clientes Enterprise mais antigas e as APIs do Azure Resource Manager mais recentes.

| **Uso** | **APIs do Contrato Enterprise** | **APIs do Azure Resource Manager** |
| --- | --- | --- |
| Autenticação | Chave de API provisionada no portal do EA (Contrato Enterprise) | Autenticação do Azure AD (Azure Active Directory) usando tokens de usuário ou entidades de serviço. As entidades de serviço assumem o lugar das chaves de API. |
| Escopos e permissões | Todas as solicitações estão no escopo do registro. As atribuições de permissão da Chave de API determinarão se os dados de todo o registro, um departamento ou uma conta específica serão retornados. Nenhuma autenticação do usuário. | Os usuários ou as entidades de serviço recebem acesso ao escopo de registro, departamento ou conta. |
| Ponto de extremidade do URI | https://consumption.azure.com | https://management.azure.com |
| Status de desenvolvimento | No modo de manutenção. No caminho para a substituição. | Sendo desenvolvidas ativamente |
| APIs disponíveis | Limitadas ao que está disponível no momento | APIs equivalentes estão disponíveis para substituir cada API do EA. <p> As [APIs do Gerenciamento de Custos](/rest/api/cost-management/) também estão disponíveis para você, incluindo: <p> <ul><li>Orçamentos</li><li>Alertas<li>Exportações</li></ul> |

## <a name="migration-checklist"></a>Lista de verificação de migração

- Familiarizar-se com as [APIs REST do Azure Resource Manager](/rest/api/azure).
- Determinar quais APIs do EA você usará e ver para quais APIs do Azure Resource Manager você fará a movimentação em [Mapeamento da API do EA para novas APIs do Azure Resource Manager](#ea-api-mapping-to-new-azure-resource-manager-apis).
- Configurar a autorização e a autenticação de serviço para as APIs do Azure Resource Manager
  - Se você ainda não estiver usando as APIs do Azure Resource Manager, [registre seu aplicativo cliente com o Azure AD](/rest/api/azure/#register-your-client-application-with-azure-ad). O registro cria uma entidade de serviço para você a ser usada para chamar as APIs.
  - Atribuir o acesso da entidade de serviço aos escopos necessários, conforme descrito abaixo.
  - Atualizar qualquer código de programação para usar a [autenticação do Azure AD](/rest/api/azure/#create-the-request) com sua entidade de serviço.
- Testar as APIs e atualizar qualquer código de programação para substituir chamadas à API do EA por chamadas à API do Azure Resource Manager.
- Atualize o tratamento de erro para usar novos códigos de erro. Outras considerações incluem:
  - As APIs do Azure Resource Manager têm um período de tempo limite de 60 segundos.
  - As APIs do Azure Resource Manager têm uma limitação de taxa implementada. Isso resultará em um erro de limitação 429 se as tarifas forem excedidas. Crie suas soluções para que você não realize muitas chamadas à API em um curto período de tempo.
- Examine as outras APIs de Gerenciamento de Custos disponíveis por meio do Azure Resource Manager e avalie o uso posteriormente. Para obter mais informações, confira [Usar APIs do Gerenciamento de Custos adicionais](#use-additional-cost-management-apis).

## <a name="assign-service-principal-access-to-azure-resource-manager-apis"></a>Atribuir o acesso da Entidade de Serviço a APIs do Azure Resource Manager

Depois de criar uma Entidade de Serviço para chamar programaticamente as APIs do Azure Resource Manager, você precisa atribuir a ela as permissões apropriadas para autorizar e executar solicitações no Azure Resource Manager. Há duas estruturas de permissão para cenários diferentes.

### <a name="azure-billing-hierarchy-access"></a>Acesso à hierarquia de cobrança do Azure

Para atribuir permissões da Entidade de Serviço aos seus escopos de Conta de Cobrança, Departamentos ou Contas de Registro Enterprise, use as APIs de [Permissões de Cobrança](/rest/api/billing/2019-10-01-preview/billingpermissions), [Definições de Função de cobrança](/rest/api/billing/2019-10-01-preview/billingroledefinitions) e [Atribuições de Função de Cobrança](/rest/api/billing/2019-10-01-preview/billingroleassignments).

- Use as APIs de Permissões de Cobrança para identificar as permissões que uma Entidade de Serviço já tem em determinado escopo, como uma Conta de Cobrança ou Departamento.
- Use as APIs de Definições de Função de Cobrança para enumerar as funções disponíveis que podem ser atribuídas à sua Entidade de Serviço.
  - Somente as funções Administrador do EA Somente Leitura e Administrador de Departamento Somente Leitura podem ser atribuídas às Entidades de Serviço no momento.
- Use as APIs de Atribuições de Função de Cobrança para atribuir uma função à sua Entidade de Serviço.

O exemplo a seguir mostra como chamar a API de Atribuições de Função para conceder acesso de Entidade de Serviço à sua conta de cobrança. É recomendável usar o [PostMan](https://postman.com) para fazer essas configurações de permissão única.

```json
POST https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/createBillingRoleAssignment?api-version=2019-10-01-preview
```

#### <a name="request-body"></a>Corpo da solicitação

```json
{
  "principalId": "00000000-0000-0000-0000-000000000000",
  "billingRoleDefinitionId": "/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/providers/Microsoft.Billing/billingRoleDefinition/10000000-aaaa-bbbb-cccc-100000000000"
}

```

### <a name="azure-role-based-access-control"></a>Controle de acesso baseado em função do Azure

O novo suporte da Entidade de Serviço estende-se a escopos específicos ao Azure, como grupos de gerenciamento, assinaturas e grupos de recursos. Você pode atribuir permissões de Entidade de Serviço a esses escopos diretamente [no portal do Azure](../../active-directory/develop/howto-create-service-principal-portal.md#assign-a-role-to-the-application) ou usando o [Azure PowerShell](../../active-directory/develop/howto-authenticate-service-principal-powershell.md#assign-the-application-to-a-role).

## <a name="ea-api-mapping-to-new-azure-resource-manager-apis"></a>Mapeamento da API do EA para novas APIs do Azure Resource Manager

Use a tabela abaixo para identificar as APIs do EA que você usa no momento e a API do Azure Resource Manager de substituição a ser usada.

| **Cenário** | **APIs do EA** | **APIs do Azure Resource Manager** |
| --- | --- | --- |
| Resumo do saldo | [/balancesummary](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) |[Microsoft.Consumption/balances](/rest/api/consumption/balances/getbybillingaccount) |
| Tabela de preços | [/pricesheet](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) | [Microsoft.Consumption/pricesheets/default](/rest/api/consumption/pricesheet) – uso para preços negociados <p> [API de Preços de Varejo](/rest/api/cost-management/retail-prices/azure-retail-prices) – uso para preços de varejo |
| Detalhes de Instância Reservada | [/reservationdetails](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) | [Microsoft.CostManagement/generateReservationDetailsReport](/rest/api/cost-management/generatereservationdetailsreport) |
| Resumo de Instância Reservada | [/reservationsummaries](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) | [Microsoft.Consumption/reservationSummaries](/rest/api/consumption/reservationssummaries/list#reservationsummariesdailywithbillingaccountid) |
| Recomendações de Instância Reservada | [/SharedReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation)<p>[/SingleReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation) | [Microsoft.Consumption/reservationRecommendations](/rest/api/consumption/reservationrecommendations/list) |
| Encargos de Instância Reservada | [/reservationcharges](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges) | [Microsoft.Consumption/reservationTransactions](/rest/api/consumption/reservationtransactions/list) |

## <a name="migration-details-by-api"></a>Detalhes da migração por API

As seções a seguir mostram exemplos antigos de solicitação de API com novos exemplos de API de substituição.

### <a name="balance-summary-api"></a>API de Resumo de Saldo

Use os URIs de solicitação a seguir quando chamar a nova API de Resumo de Saldo. O número de registro deve ser usado como billingAccountId.

#### <a name="supported-requests"></a>Solicitações com suporte

[Obter para registro](/rest/api/consumption/balances/getbybillingaccount)

```json
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.Consumption/balances?api-version=2019-10-01
```

### <a name="response-body-changes"></a>Alterações do corpo da resposta

_Corpo da resposta antigo_:

```json
{
        "id": "enrollments/100/billingperiods/201507/balancesummaries",
          "billingPeriodId": 201507,
          "currencyCode": "USD",
          "beginningBalance": 0,
          "endingBalance": 1.1,
          "newPurchases": 1,
          "adjustments": 1.1,
          "utilized": 1.1,
          "serviceOverage": 1,
          "chargesBilledSeparately": 1,
          "totalOverage": 1,
          "totalUsage": 1.1,
          "azureMarketplaceServiceCharges": 1,
          "newPurchasesDetails": [
            {
              "name": "",
              "value": 1
            }
          ],
          "adjustmentDetails": [
            {
              "name": "Promo Credit",
              "value": 1.1
            },
            {
              "name": "SIE Credit",
              "value": 1.0
            }
          ]
    }

```

_Corpo da resposta novo_:

Os mesmos dados agora estão disponíveis no campo `properties` da nova resposta de API. Pode haver pequenas alterações na ortografia em alguns nomes de campo.

```json
{
  "id": "/providers/Microsoft.Billing/billingAccounts/123456/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/balances/balanceId1",
  "name": "balanceId1",
  "type": "Microsoft.Consumption/balances",
  "properties": {
    "currency": "USD  ",
    "beginningBalance": 3396469.19,
    "endingBalance": 2922371.02,
    "newPurchases": 0,
    "adjustments": 0,
    "utilized": 474098.17,
    "serviceOverage": 0,
    "chargesBilledSeparately": 0,
    "totalOverage": 0,
    "totalUsage": 474098.17,
    "azureMarketplaceServiceCharges": 609.82,
    "billingFrequency": "Month",
    "priceHidden": false,
    "newPurchasesDetails": [
      {
        "name": "Promo Purchase",
        "value": 1
      }
    ],
    "adjustmentDetails": [
      {
        "name": "Promo Credit",
        "value": 1.1
      },
      {
        "name": "SIE Credit",
        "value": 1
      }
    ]
  }
}

```

### <a name="price-sheet"></a>Tabela de preços

Use os URIs de solicitação a seguir quando chamar a nova API de Tabela de Preços.

#### <a name="supported-requests"></a>Solicitações com suporte

 Você pode chamar a API usando os seguintes escopos:

- Registro: `providers/Microsoft.Billing/billingAccounts/{billingAccountId}`
- Assinatura: `subscriptions/{subscriptionId}`

[_Obter para Período de Cobrança atual_](/rest/api/consumption/pricesheet/get)


```json
https://management.azure.com/{scope}/providers/Microsoft.Consumption/pricesheets/default?api-version=2019-10-01
```

[_Obter para Período de Cobrança especificado_](/rest/api/consumption/pricesheet/getbybillingperiod)

```json
https://management.azure.com/{scope}/providers/Microsoft.Billing/billingPeriods/{billingPeriodName}/providers/Microsoft.Consumption/pricesheets/default?api-version=2019-10-01
```

#### <a name="response-body-changes"></a>Alterações do corpo da resposta

_Resposta antiga_:

```json
      [
        {
              "id": "enrollments/57354989/billingperiods/201601/products/343/pricesheets",
              "billingPeriodId": "201704",
            "meterId": "dc210ecb-97e8-4522-8134-2385494233c0",
              "meterName": "A1 VM",
              "unitOfMeasure": "100 Hours",
              "includedQuantity": 0,
              "partNumber": "N7H-00015",
              "unitPrice": 0.00,
              "currencyCode": "USD"
        },
        {
              "id": "enrollments/57354989/billingperiods/201601/products/2884/pricesheets",
              "billingPeriodId": "201404",
            "meterId": "dc210ecb-97e8-4522-8134-5385494233c0",
              "meterName": "Locally Redundant Storage Premium Storage - Snapshots - AU East",
              "unitOfMeasure": "100 GB",
              "includedQuantity": 0,
              "partNumber": "N9H-00402",
              "unitPrice": 0.00,
              "currencyCode": "USD"
        },
        ...
    ]

```

_Resposta nova_:

Os dados antigos agora estão no campo `pricesheets` da nova resposta da API. Informações de detalhes de medidor também são fornecidas.

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/pricesheets/default",
  "name": "default",
  "type": "Microsoft.Consumption/pricesheets",
  "properties": {
    "nextLink": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/microsoft.consumption/pricesheets/default?api-version=2018-01-31&$skiptoken=AQAAAA%3D%3D&$expand=properties/pricesheets/meterDetails",
    "pricesheets": [
      {
        "billingPeriodId": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Billing/billingPeriods/201702",
        "meterId": "00000000-0000-0000-0000-000000000000",
        "unitOfMeasure": "100 Hours",
        "includedQuantity": 100,
        "partNumber": "XX-11110",
        "unitPrice": 0.00000,
        "currencyCode": "EUR",
        "offerId": "OfferId 1",
        "meterDetails": {
          "meterName": "Data Transfer Out (GB)",
          "meterCategory": "Networking",
          "unit": "GB",
          "meterLocation": "Zone 2",
          "totalIncludedQuantity": 0,
          "pretaxStandardRate": 0.000
        }
      }
    ]
  }
}

```

### <a name="reserved-instance-usage-details"></a>Detalhes de uso da instância reservada

A Microsoft não está trabalhando ativamente em APIs de Detalhes de Reserva baseadas em configurações síncronas. Recomendamos que você mude para o padrão de chamada à API assíncrona com suporte do SPN mais recente como parte da migração. As solicitações assíncronas lidam melhor com grandes quantidades de dados e reduzirão os erros de tempo limite.

#### <a name="supported-requests"></a>Solicitações com suporte

Use os URIs de solicitação a seguir quando chamar a nova API de Detalhes de Reserva Assíncrona. O número de registro deve ser usado como `billingAccountId`. Você pode chamar a API com os seguintes escopos:

- Registro: `providers/Microsoft.Billing/billingAccounts/{billingAccountId}`

#### <a name="sample-request-to-generate-a-reservation-details-report"></a>Exemplo de solicitação para gerar um relatório de detalhes de reserva

```json
POST 
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.CostManagement/generateReservationDetailsReport?startDate={startDate}&endDate={endDate}&api-version=2019-11-01

```

#### <a name="sample-request-to-poll-report-generation-status"></a>Exemplo de solicitação para sondar o status de geração de relatório

```json
GET
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.CostManagement/reservationDetailsOperationResults/{operationId}?api-version=2019-11-01

```

#### <a name="sample-poll-response"></a>Resposta de sondagem de exemplo

```json
{
  "status": "Completed",
  "properties": {
    "reportUrl": "https://storage.blob.core.windows.net/details/20200911/00000000-0000-0000-0000-000000000000?sv=2016-05-31&sr=b&sig=jep8HT2aphfUkyERRZa5LRfd9RPzjXbzB%2F9TNiQ",
    "validUntil": "2020-09-12T02:56:55.5021869Z"
  }
}

```

#### <a name="response-body-changes"></a>Alterações do corpo da resposta

A resposta da API de Detalhes de Reserva baseada em configurações síncronas mais antiga está abaixo.

_Resposta antiga_:

```json
{
    "reservationOrderId": "00000000-0000-0000-0000-000000000000",
    "reservationId": "00000000-0000-0000-0000-000000000000",
    "usageDate": "2018-02-01T00:00:00",
    "skuName": "Standard_F2s",
    "instanceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/resourvegroup1/providers/microsoft.compute/virtualmachines/VM1",
    "totalReservedQuantity": 18.000000000000000,
    "reservedHours": 432.000000000000000,
    "usedHours": 400.000000000000000
}

```

_Resposta nova_:

A nova API cria um arquivo CSV para você. Confira os campos de arquivo a seguir.

| **Propriedade Antiga** | **Propriedade Nova** | **Observações** |
| --- | --- | --- |
|  | InstanceFlexibilityGroup | Nova propriedade para flexibilidade de instância. |
|  | InstanceFlexibilityRatio | Nova propriedade para flexibilidade de instância. |
| instanceId | InstanceName |  |
|  | Tipo | É uma nova propriedade. O valor é `None`, `Reservation` ou `IncludedQuantity`. |
| reservationId | ReservationId |  |
| reservationOrderId | ReservationOrderId |  |
| reservedHours | ReservedHours |  |
| skuName | SkuName |  |
| totalReservedQuantity | TotalReservedQuantity |  |
| usageDate | UsageDate |  |
| usedHours | UsedHours |  |

### <a name="reserved-instance-usage-summary"></a>Resumo de Uso da Instância Reservada

Use os URIs de solicitação a seguir para chamar a nova API de Resumos de Reserva.

#### <a name="supported-requests"></a>Solicitações com suporte

 Chame a API com os seguintes escopos:

- Registro: `providers/Microsoft.Billing/billingAccounts/{billingAccountId}`

[_Obter Resumo de Reserva Diariamente_](/rest/api/consumption/reservationssummaries/list#reservationsummariesdailywithbillingaccountid)

```json
https://management.azure.com/{scope}/Microsoft.Consumption/reservationSummaries?grain=daily&$filter=properties/usageDate ge 2017-10-01 AND properties/usageDate le 2017-11-20&api-version=2019-10-01
```

[_Obter Resumo de Reserva Mensalmente_](/rest/api/consumption/reservationssummaries/list#reservationsummariesmonthlywithbillingaccountid)

```json
https://management.azure.com/{scope}/Microsoft.Consumption/reservationSummaries?grain=daily&$filter=properties/usageDate ge 2017-10-01 AND properties/usageDate le 2017-11-20&api-version=2019-10-01
```

#### <a name="response-body-changes"></a>Alterações do corpo da resposta

_Resposta antiga_:

```json
[
     {
        "reservationOrderId": "00000000-0000-0000-0000-000000000000",
        "reservationId": "00000000-0000-0000-0000-000000000000",
        "skuName": "Standard_F1s",
        "reservedHours": 24,
        "usageDate": "2018-05-01T00:00:00",
        "usedHours": 23,
        "minUtilizationPercentage": 0,
        "avgUtilizationPercentage": 95.83,
        "maxUtilizationPercentage": 100
    }
]

```

_Resposta nova_:

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/12345/providers/Microsoft.Consumption/reservationSummaries/reservationSummaries_Id1",
      "name": "reservationSummaries_Id1",
      "type": "Microsoft.Consumption/reservationSummaries",
      "tags": null,
      "properties": {
        "reservationOrderId": "00000000-0000-0000-0000-000000000000",
        "reservationId": "00000000-0000-0000-0000-000000000000",
        "skuName": "Standard_B1s",
        "reservedHours": 720,
        "usageDate": "2018-09-01T00:00:00-07:00",
        "usedHours": 0,
        "minUtilizationPercentage": 0,
        "avgUtilizationPercentage": 0,
        "maxUtilizationPercentage": 0
      }
    }
  ]
}

```

### <a name="reserved-instance-recommendations"></a>Recomendações de instância reservada

Use os URIs de solicitação a seguir para chamar a nova API de Recomendações de Reserva.

#### <a name="supported-requests"></a>Solicitações com suporte

 Chame a API com os seguintes escopos:

- Registro: `providers/Microsoft.Billing/billingAccounts/{billingAccountId}`
- Assinatura: `subscriptions/{subscriptionId}`
- Grupos de Recursos: `subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}`

[_Obter Recomendações_](/rest/api/consumption/reservationrecommendations/list)

As recomendações compartilhadas e de escopo único estão disponíveis por meio dessa API. Você também pode filtrar o escopo como um parâmetro de API opcional.

```json
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/123456/providers/Microsoft.Consumption/reservationRecommendations?api-version=2019-10-01
```

#### <a name="response-body-changes"></a>Alterações do corpo da resposta

As recomendações para escopos compartilhados e únicos são combinadas em uma API.

_Resposta antiga_:

```json
[{
    "subscriptionId": "1111111-1111-1111-1111-111111111111",
    "lookBackPeriod": "Last7Days",
    "meterId": "2e3c2132-1398-43d2-ad45-1d77f6574933",
    "skuName": "Standard_DS1_v2",
    "term": "P1Y",
    "region": "westus",
    "costWithNoRI": 186.27634908960002,
    "recommendedQuantity": 9,
    "totalCostWithRI": 143.12931642978083,
    "netSavings": 43.147032659819189,
    "firstUsageDate": "2018-02-19T00:00:00"
}
]

```

_Resposta nova_:

```json
{
  "value": [
    {
      "id": "billingAccount/123456/providers/Microsoft.Consumption/reservationRecommendations/00000000-0000-0000-0000-000000000000",
      "name": "00000000-0000-0000-0000-000000000000",
      "type": "Microsoft.Consumption/reservationRecommendations",
      "location": "westus",
      "sku": "Standard_DS1_v2",
      "kind": "legacy",
      "properties": {
        "meterId": "00000000-0000-0000-0000-000000000000",
        "term": "P1Y",
        "costWithNoReservedInstances": 12.0785105,
        "recommendedQuantity": 1,
        "totalCostWithReservedInstances": 11.4899644807748,
        "netSavings": 0.588546019225182,
        "firstUsageDate": "2019-07-07T00:00:00-07:00",
        "scope": "Shared",
        "lookBackPeriod": "Last7Days",
        "instanceFlexibilityRatio": 1,
        "instanceFlexibilityGroup": "DSv2 Series",
        "normalizedSize": "Standard_DS1_v2",
        "recommendedQuantityNormalized": 1,
        "skuProperties": [
          {
            "name": "Cores",
            "value": "1"
          },
          {
            "name": "Ram",
            "value": "1"
          }
        ]
      }
    },
   ]
}

```

### <a name="reserved-instance-charges"></a>Encargos de instância reservada

Use os URIs de solicitação a seguir para chamar a nova API de Encargos de Instância Reservada.

#### <a name="supported-requests"></a>Solicitações com suporte

[_Obter Encargos de Reserva por Intervalo de Datas_](/rest/api/consumption/reservationtransactions/list)

```json
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.Consumption/reservationTransactions?$filter=properties/eventDate+ge+2020-05-20+AND+properties/eventDate+le+2020-05-30&api-version=2019-10-01
```

#### <a name="response-body-changes"></a>Alterações do corpo da resposta

_Resposta antiga_:

```json
[
    {
        "purchasingEnrollment": "string",
        "armSkuName": "Standard_F1s",
        "term": "P1Y",
        "region": "eastus",
        "PurchasingsubscriptionGuid": "00000000-0000-0000-0000-000000000000",
        "PurchasingsubscriptionName": "string",
        "accountName": "string",
        "accountOwnerEmail": "string",
        "departmentName": "string",
        "costCenter": "",
        "currentEnrollment": "string",
        "eventDate": "string",
        "reservationOrderId": "00000000-0000-0000-0000-000000000000",
        "description": "Standard_F1s eastus 1 Year",
        "eventType": "Purchase",
        "quantity": int,
        "amount": double,
        "currency": "string",
        "reservationOrderName": "string"
    }
]

```
_Resposta nova_:

```json
{
  "value": [
    {
      "id": "/billingAccounts/123456/providers/Microsoft.Consumption/reservationtransactions/201909091919",
      "name": "201909091919",
      "type": "Microsoft.Consumption/reservationTransactions",
      "tags": {},
      "properties": {
        "eventDate": "2019-09-09T19:19:04Z",
        "reservationOrderId": "00000000-0000-0000-0000-000000000000",
        "description": "Standard_DS1_v2 westus 1 Year",
        "eventType": "Cancel",
        "quantity": 1,
        "amount": -21,
        "currency": "USD",
        "reservationOrderName": "Transaction-DS1_v2",
        "purchasingEnrollment": "123456",
        "armSkuName": "Standard_DS1_v2",
        "term": "P1Y",
        "region": "westus",
        "purchasingSubscriptionGuid": "11111111-1111-1111-1111-11111111111",
        "purchasingSubscriptionName": "Infrastructure Subscription",
        "accountName": "Microsoft Infrastructure",
        "accountOwnerEmail": "admin@microsoft.com",
        "departmentName": "Unassigned",
        "costCenter": "",
        "currentEnrollment": "123456",
        "billingFrequency": "recurring"
      }
    },
  ]
}

```

## <a name="use-additional-cost-management-apis"></a>Usar APIs de Gerenciamento de Custos adicionais

Depois de fazer a migração para APIs do Azure Resource Manager em seus cenários de relatório existentes, você também poderá usar muitas outras APIs. As APIs também estão disponíveis por meio do Azure Resource Manager e podem ser automatizadas usando a autenticação baseada em Entidade de Serviço. Este é um breve resumo das novas funcionalidades que você pode usar.

- [Orçamentos](/rest/api/consumption/budgets/createorupdate) – use para definir limites a fim de monitorar proativamente seus custos, alertar os stakeholders relevantes e automatizar ações em resposta a violações de limite.

- [Alertas](/rest/api/cost-management/alerts) – use para ver informações de alerta, incluindo, entre outros, alertas de orçamento, alertas de fatura, alertas de crédito e alertas de cota.

- [Exportações](/rest/api/cost-management/exports) – use para agendar a exportação de dados recorrentes de seus encargos para uma conta do Armazenamento do Azure de sua escolha. É a solução recomendada para clientes com uma grande presença do Azure que desejam analisar os dados e usá-los nos próprios sistemas internos.

## <a name="next-steps"></a>Próximas etapas

- Familiarizar-se com as [APIs REST do Azure Resource Manager](/rest/api/azure).
- Se necessário, determinar quais APIs do EA você usará e ver para quais APIs do Azure Resource Manager você fará a movimentação em [Mapeamento da API do EA para novas APIs do Azure Resource Manager](#ea-api-mapping-to-new-azure-resource-manager-apis).
- Se você ainda não estiver usando as APIs do Azure Resource Manager, [registre seu aplicativo cliente com o Azure AD](/rest/api/azure/#register-your-client-application-with-azure-ad).
- Se necessário, atualizar qualquer código de programação seu para usar a [autenticação do Azure AD](/rest/api/azure/#create-the-request) com sua entidade de serviço.