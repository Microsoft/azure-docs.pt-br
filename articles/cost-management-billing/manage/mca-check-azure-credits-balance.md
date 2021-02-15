---
title: Acompanhar o saldo de crédito Azure para um Contrato de Cliente da Microsoft
description: Saiba como verificar o saldo de crédito Azure para um Contrato de Cliente da Microsoft.
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 08/20/2019
ms.author: banders
ms.openlocfilehash: cf5772e9cf08eaa7f34acd59a9c96da6f20cd03d
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92131404"
---
# <a name="track-microsoft-customer-agreement-azure-credit-balance"></a>Acompanhar o saldo de crédito Azure no Contrato de Cliente da Microsoft

Verifique o saldo de crédito Azure de sua conta de cobrança para um Contrato de Cliente da Microsoft no portal do Azure ou por meio de APIs REST.

Na conta de cobrança de um Contrato de Cliente da Microsoft, os créditos são atribuídos a um perfil de cobrança. Cada perfil de cobrança tem créditos próprios, que são aplicados automaticamente aos encargos na respectiva fatura. Você precisa ter uma função de proprietário, colaborador, leitor ou gerenciador de faturas no perfil de cobrança ou uma função de proprietário, colaborador ou leitor na conta de cobrança para exibir o saldo de crédito Azure de um perfil de cobrança. Para saber mais sobre as funções, confira [Entender as funções administrativas do Contrato de Cliente da Microsoft no Azure](understand-mca-roles.md).

> [!NOTE]
> Um novo crédito pode levar até 24 horas para aparecer no portal do Azure. Se você obtiver um novo crédito e não conseguir vê-lo no portal, aguarde 24 horas para ele aparecer.

Este artigo aplica-se a uma conta de cobrança para um Contrato de Cliente da Microsoft. [Verifique se você tem acesso a um Contrato de Cliente da Microsoft](#check-access-to-a-microsoft-customer-agreement).

## <a name="check-your-credit-balance"></a>Verificar seu saldo de crédito

### <a name="azure-portal"></a>[Azure portal](#tab/portal)

1. Entre no [portal do Azure](https://portal.azure.com).

2. Pesquise **Gerenciamento de Custos + Cobrança**.

    ![Captura de tela que mostra a pesquisa de gerenciamento de custos e cobrança no portal.](./media/mca-check-azure-credits-balance/billing-search-cost-management-billing.png)

3. Na página de escopos do orçamento, selecione a conta de cobrança para a qual você deseja acompanhar o saldo de crédito. A conta de cobrança deve ser do tipo **Contrato de Cliente da Microsoft**.

    ![Captura de tela que mostra os Escopos do Orçamento.](./media/mca-check-azure-credits-balance/list-of-scopes.png)

    > [!NOTE]
    >
    > O portal do Azure lembra o último escopo do orçamento que você acessou e exibirá o escopo na próxima vez que você visitar a página Gerenciamento de Custos + Cobrança. Você não verá a página de escopos do orçamento se tiver visitado a página de Gerenciamento de Custos + Cobrança anteriormente. Nesse caso, verifique se você está no [escopo certo](#check-access-to-a-microsoft-customer-agreement). Se não estiver, [alterne o escopo](view-all-accounts.md#switch-billing-scope-in-the-azure-portal) para selecionar a conta de cobrança para um Contrato de Cliente da Microsoft.

3. Selecione **Formas de pagamento** do lado esquerdo e, em seguida, selecione **Créditos Azure**.

   ![Captura de tela de saldos estimados e atuais.](./media/mca-check-azure-credits-balance/mca-payment-methods.png)

4. A página de Créditos Azure tem as seguintes seções:

   #### <a name="balance"></a>Saldo

   A seção de saldo exibe o resumo do seu saldo de créditos Azure.

   ![Captura de tela do saldo de crédito de um perfil de cobrança](./media/mca-check-azure-credits-balance/mca-credit-balance.png)

   | Termo               | Definição                           |
   |--------------------|--------------------------------------------------------|
   | Saldo estimado  | O valor estimado de créditos que você terá depois de considerar todas as transações faturadas e pendentes |
   | Saldo atual    | Valor de créditos da última fatura. Ele não inclui nenhuma transação pendente |

   Quando o saldo estimado cair para 0, você será cobrado por todo o seu uso, incluindo os produtos qualificados para créditos.

   #### <a name="credits-list"></a>Lista de créditos

   A seção de lista de créditos exibe a lista de créditos Azure.

   ![Captura de tela da listas de créditos de um perfil de cobrança](./media/mca-check-azure-credits-balance/mca-credits-list.png)

   | Termo | Definição |
   |---|---|
   | Fonte | A origem da aquisição do crédito |
   | Data de início | A data em que você adquiriu o crédito |
   | Data de validade | A data em que o crédito expira |
   | Saldo atual | O saldo de sua última fatura |
   | Valor original | O valor original do crédito |
   | Status | O status atual do crédito. O status pode ser ativo, usado, expirado ou a expirar |

   #### <a name="transactions"></a>Transactions

   A seção transações exibe todas as transações que afetaram o seu saldo de crédito.

   ![Captura de tela das transações de crédito de um perfil de cobrança](./media/mca-check-azure-credits-balance/mca-credits-transactions.png)

   | Termo | Definição |
   |---|---|
   | Data da transação | A data em que a transação ocorreu |
   | Descrição | A descrição da transação |
   | Amount| A quantidade de transações |
   | Saldo | O saldo após a transação |

    > [!NOTE]
    >
    > Se você não vê créditos Azure na página de formas de pagamento, significa que você não tem créditos ou não selecionou o escopo certo. Selecione a conta de cobrança que tem créditos ou um dos respectivos perfis de cobrança. Para saber como alterar os escopos, confira [Alternar os escopos do orçamento no portal do Azure](view-all-accounts.md#switch-billing-scope-in-the-azure-portal).

5. Se você estiver exibindo créditos Azure no escopo da conta de cobrança e a conta de cobrança tiver mais de um perfil de cobrança, a página de créditos Azure mostrará uma tabela com um resumo dos créditos Azure para cada perfil de cobrança. Selecione um perfil de cobrança na lista, selecione as formas de pagamento e os créditos Azure para exibir detalhes de um perfil de cobrança.

    ![Captura de tela da lista de crédito de uma conta de cobrança](./media/mca-check-azure-credits-balance/mca-account-credit-list.png)

### <a name="rest-api"></a>[REST API](#tab/rest)

Use as APIs de [Cobrança do Azure](/rest/api/billing/) e [Consumo](/rest/api/consumption/) para obter o saldo de crédito de sua conta de cobrança de forma programática.

Os exemplos mostrados abaixo usam APIs REST. Atualmente, não há suporte para o PowerShell e a CLI do Azure.

### <a name="find-billing-profiles-you-have-access-to"></a>Encontrar perfis de cobrança aos quais você tem acesso

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?$expand=billingProfiles&api-version=2019-10-01-preview
```
A resposta da API retorna uma lista de contas de cobrança e os perfis de cobrança delas.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx",
      "name": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "billingProfiles": [
          {
            "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx",
            "name": "PBFV-xxxx-xxx-xxx",
            "properties": {
              "address": {
                "addressLine1": "AddressLine1",
                "city": "City",
                "companyName": "CompanyName",
                "country": "Country",
                "postalCode": "xxxxx",
                "region": "Region"
              },
              "currency": "USD",
              "displayName": "Development",
              "hasReadAccess": true,
              "invoiceDay": 5,
              "invoiceEmailOptIn": true
            },
            "type": "Microsoft.Billing/billingAccounts/billingProfiles"
          }
        ],
        "displayName": "Contoso",
        "hasReadAccess": true,
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}
```

Use a propriedade `displayName` do perfil de cobrança para identificar o perfil de cobrança para o qual deseja verificar o saldo de crédito. Copie a `id` do perfil de cobrança. Por exemplo, caso deseje verificar o saldo de crédito do perfil de cobrança **Desenvolvimento**, copie ```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx```. Cole esse valor em algum lugar, de modo que possa usá-lo na próxima etapa.

### <a name="get-azure-credit-balance"></a>Obter o saldo de crédito Azure

Faça a solicitação a seguir, substituindo `<billingProfileId>` pela `id` copiada na primeira etapa (```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx```).

```json
GET https://management.azure.com<billingProfileId>/providers/Microsoft.Consumption/credits/balanceSummary?api-version=2019-10-01
```

A resposta da API retorna o saldo estimado e atual do perfil de cobrança.

```json
{
  "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx/providers/Microsoft.Consumption/credits/balanceSummary/57c2e8df-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "name": "57c2e8df-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.Consumption/credits/balanceSummary",
  "eTag": null,
  "properties": {
    "balanceSummary": {
      "estimatedBalance": {
        "currency": "USD",
        "value": 996.13
      },
      "currentBalance": {
        "currency": "USD",
        "value": 997.87
      }
    },
    "pendingCreditAdjustments": {
      "currency": "USD",
      "value": 0.0
    },
    "expiredCredit": {
      "currency": "USD",
      "value": 0.0
    },
    "pendingEligibleCharges": {
      "currency": "USD",
      "value": -1.74
    }
  }
}
```

| Nome do elemento  | Descrição                                                                           |
|---------------|---------------------------------------------------------------------------------------|
| `estimatedBalance` | O valor estimado de créditos que você terá depois de considerar todas as transações cobradas e pendentes. |
| `currentBalance`   | O valor de créditos após a última fatura. Ele não inclui nenhuma transação pendente.    |
| `pendingCreditAdjustments`      | Os ajustes como reembolsos que ainda não foram faturados.  |
| `expiredCredit`      |  O crédito que expirou desde a sua última fatura.  |
| `pendingEligibleCharges`  | Os encargos qualificados para crédito que ainda não foram faturados.   |

### <a name="get-list-of-credits"></a>Obter a lista de créditos

Faça a solicitação a seguir, substituindo `<billingProfileId>` pela `id` copiada na primeira etapa (```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx```).

```json
GET https://management.azure.com<billingProfileId>/providers/Microsoft.Consumption/lots?api-version=2019-10-01
```
A resposta da API retorna listas de créditos Azure de um perfil de cobrança.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx/providers/Microsoft.Consumption/lots/f2ecfd94-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "f2ecfd94-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Consumption/lots",
      "eTag": null,
      "properties": {
        "originalAmount": {
          "currency": "USD",
          "value": 500.0
        },
        "closedBalance": {
          "currency": "USD",
          "value": 500.0
        },
        "source": "Azure Promotional Credit",
        "startDate": "09/18/2019 21:47:31",
        "expirationDate": "09/18/2020 21:47:30",
        "poNumber": ""
      }
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/xxxx-xxxx-xxx-xxx/providers/Microsoft.Consumption/lots/4ea40eb5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "4ea40eb5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Consumption/lots",
      "eTag": null,
      "properties": {
        "originalAmount": {
          "currency": "USD",
          "value": 500.0
        },
        "closedBalance": {
          "currency": "USD",
          "value": 497.87
        },
        "source": "Azure Promotional Credit",
        "startDate": "09/18/2019 21:47:31",
        "expirationDate": "09/18/2020 21:47:30",
        "poNumber": ""
      }
    }
  ]
}
```
| Nome do elemento  | Descrição                                                                                               |
|---------------|-----------------------------------------------------------------------------------------------------------|
| `originalAmount` | O valor original do crédito. |
| `closedBalance`   | O saldo da última fatura.    |
| `source`      | A origem que define quem adquiriu o crédito. |
| `startDate`      |  A data em que o crédito se tornou ativo.  |
| `expirationDate`  | A data em que o crédito expira.   |
| `poNumber`  | O número da ordem de compra da fatura na qual o crédito foi cobrado.   |

### <a name="get-transactions-that-affected-credit-balance"></a>Obter as transações que afetaram o saldo de crédito

Faça a solicitação a seguir, substituindo `<billingProfileId>` pela `id` copiada na primeira etapa (```providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx```). Você precisará passar uma **startDate** e uma **endDate** para obter transações para a duração necessária.

```json
GET https://management.azure.com<billingProfileId>/providers/Microsoft.Consumption/events?api-version=2019-10-01&startDate=2018-10-01T00:00:00.000Z&endDate=2019-10-11T12:00:00.000Z?api-version=2019-10-01
```
A resposta da API retorna todas as transações que afetaram o saldo de crédito de seu perfil de cobrança.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx`/providers/Microsoft.Consumption/events/e2032eb5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "e2032eb5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Consumption/events",
      "eTag": null,
      "properties": {
        "transactionDate": "10/11/2019",
        "description": "Credit eligible charges as of 10/11/2019",
        "newCredit": {
          "currency": "USD",
          "value": 0.0
        },
        "adjustments": {
          "currency": "USD",
          "value": 0.0
        },
        "creditExpired": {
          "currency": "USD",
          "value": 0.0
        },
        "charges": {
          "currency": "USD",
          "value": -1.74
        },
        "closedBalance": {
          "currency": "USD",
          "value": 998.26
        },
        "eventType": "PendingCharges",
        "invoiceNumber": ""
      }
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx/providers/Microsoft.Consumption/events/381efd80-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "381efd80-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Consumption/events",
      "eTag": null,
      "properties": {
        "transactionDate": "09/18/2019",
        "description": "New credit added on 09/18/2019",
        "newCredit": {
          "currency": "USD",
          "value": 500.0
        },
        "adjustments": {
          "currency": "USD",
          "value": 0.0
        },
        "creditExpired": {
          "currency": "USD",
          "value": 0.0
        },
        "charges": {
          "currency": "USD",
          "value": 0.0
        },
        "closedBalance": {
          "currency": "USD",
          "value": 1000.0
        },
        "eventType": "PendingNewCredit",
        "invoiceNumber": ""
      }
    }
  ]
}
```
| Nome do elemento  | Descrição                                                                                               |
|---------------|-----------------------------------------------------------------------------------------------------------|
| `transactionDate` | A data em que a transação ocorreu. |
| `description` | A descrição da transação. |
| `adjustments`   | Os ajustes de crédito para a transação.    |
| `creditExpired`      | O valor de crédito expirado. |
| `charges`      |  Os encargos da transação.  |
| `closedBalance`  | O saldo após a transação.   |
| `eventType`  | O tipo de transação.   |
| `invoiceNumber`  | O número da fatura na qual a transação é cobrada. Ele estará vazio para a transação pendente.   |

---

## <a name="how-credits-are-used"></a>Como os créditos são usados

Em uma conta de cobrança para um Contrato de Cliente da Microsoft, você usa perfis de cobrança para gerenciar suas faturas e suas formas de pagamento. Uma fatura mensal é gerada para cada perfil de cobrança e você usa as formas de pagamento para pagar a fatura.

Você atribui créditos adquiridos a um perfil de cobrança. Quando uma fatura é gerada para o perfil de cobrança, os créditos são automaticamente aplicados aos encargos totais para calcular o valor que você precisa pagar. Você paga o valor restante com formas de pagamento como cheque/transferência eletrônica ou cartão de crédito.

## <a name="products-that-arent-covered-by-azure-credits"></a>Produtos que não são cobertos pelos créditos Azure

 Os produtos a seguir não são cobertos pelos créditos Azure. Você é cobrado por usar esses produtos, independentemente de seu saldo de crédito:

- Canônico
- Citrix XenApp Essentials
- Citrix XenDesktop
- Usuário registrado
- OpenLogic
- Direitos de Acesso Remoto XenApp Essentials
Usuário Registrado
- Ubuntu Advantage
- Visual Studio Enterprise (mensal)
- Visual Studio Enterprise (anual)
- Visual Studio Professional (mensal)
- Visual Studio Professional (anual)
- Produtos do Azure Marketplace
- Planos de Suporte do Azure

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Verificar o acesso ao Contrato de Cliente da Microsoft
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Precisa de ajuda? Entre em contato com o suporte.

Se precisar de ajuda, [contate o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.

## <a name="next-steps"></a>Próximas etapas

- [Entender a conta de cobrança para um Contrato de Cliente da Microsoft](../understand/mca-overview.md)
- [Entender os termos em sua fatura do Contrato de Cliente da Microsoft](../understand/mca-understand-your-invoice.md)