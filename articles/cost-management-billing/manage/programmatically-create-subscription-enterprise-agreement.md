---
title: Criar assinaturas do Contrato Enterprise do Azure de modo programático com as APIs mais recentes
description: Saiba como criar assinaturas do Contrato Enterprise do Azure de modo programático usando as versões mais recentes da API REST, da CLI do Azure e do Azure PowerShell.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 01/13/2021
ms.reviewer: andalmia
ms.author: banders
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: bd155ea3c98231cf20fa7c62325e3c2ecfb89920
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98185913"
---
# <a name="programmatically-create-azure-enterprise-agreement-subscriptions-with-the-latest-apis"></a>Criar assinaturas do Contrato Enterprise do Azure de modo programático com as APIs mais recentes

Este artigo ajudará você a criar assinaturas do EA (Contrato Enterprise) do Azure de modo programático para uma conta de cobrança do EA usando as versões mais recentes da API. Caso ainda esteja usando a versão prévia mais antiga, confira como [Criar assinaturas do Azure de modo programático com APIs de versão prévia](programmatically-create-subscription-preview.md). 

Neste artigo, você aprenderá a criar assinaturas programaticamente usando o Azure Resource Manager.

Quando você cria uma assinatura do Azure programaticamente, essa assinatura é regida pelo contrato sob o qual você obteve serviços Azure da Microsoft ou de um revendedor autorizado. Para obter mais informações, confira [Informações Legais do Microsoft Azure](https://azure.microsoft.com/support/legal/).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

Você precisa ter uma função de Proprietário em uma Conta de Registro para criar uma assinatura. Há duas maneiras de obter a função:

* O Administrador Corporativo do seu registro pode [fazer de você um Proprietário da Conta](https://ea.azure.com/helpdocs/addNewAccount) (é necessário entrar), o que faz de você um Proprietário da Conta de Registro.
* Um proprietário existente da conta de inscrição pode [conceder acesso a você](/rest/api/billing/2019-10-01-preview/enrollmentaccountroleassignments/put). Do mesmo modo, para usar uma entidade de serviço com o objetivo de criar uma assinatura de EA, você deverá [conceder a essa entidade de serviço a capacidade de criar assinaturas](/rest/api/billing/2019-10-01-preview/enrollmentaccountroleassignments/put). 
  > [!NOTE]
  > Lembre-se de usar a versão da API correta para dar permissões ao proprietário da conta de registro. Para este artigo e para as APIs documentadas nele, use a API [2019-10-01-preview](/rest/api/billing/2019-10-01-preview/enrollmentaccountroleassignments/put). Se estiver fazendo a migração para usar as APIs mais recentes, você precisará conceder ao proprietário a permissão novamente usando [2019-10-01-preview](/rest/api/billing/2019-10-01-preview/enrollmentaccountroleassignments/put). A configuração anterior feita com a [versão 2015-07-01](grant-access-to-create-subscription.md) não é convertida automaticamente para uso com as APIs mais recentes.

## <a name="find-accounts-you-have-access-to"></a>Localize as contas às quais você tem acesso

Depois que você for adicionado a uma Conta de Registro associada a um Proprietário da Conta, o Azure usa a relação de conta para registro para determinar quando cobrar os encargos de assinatura. Todas as assinaturas criadas na conta serão cobradas no registro de EA do qual a conta faz parte. Para criar assinaturas, você deve passar valores sobre a conta de registro e as entidades de usuário a fim de ser dono da assinatura.

Para executar os comandos a seguir, você deve estar conectado ao *diretório base* do proprietário da conta, que é o diretório em que as assinaturas são criadas por padrão.

### <a name="rest"></a>[REST](#tab/rest-getEnrollments)

Solicitação para listar todas as contas de registro às quais você tem acesso:

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingaccounts/?api-version=2020-05-01
```

A resposta da API lista todas as contas de registro às quais você tem acesso:

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/1234567",
      "name": "1234567",
      "properties": {
        "accountStatus": "Unknown",
        "accountType": "Enterprise",
        "agreementType": "EnterpriseAgreement",
        "soldTo": {
          "companyName": "Contoso",
          "country": "US "
        },
        "billingProfiles": {
          "hasMoreResults": false
        },
        "displayName": "Contoso",
        "enrollmentAccounts": [
          {
            "id": "/providers/Microsoft.Billing/billingAccounts/1234567/enrollmentAccounts/7654321",
            "name": "7654321",
            "type": "Microsoft.Billing/enrollmentAccounts",
            "properties": {
              "accountName": "Contoso",
              "accountOwnerEmail": "kenny@contoso.onmicrosoft.com",
              "costCenter": "Test",
              "isDevTest": false
            }
          }
        ],
        "hasReadAccess": false
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}

```

O valor de um escopo do orçamento e de `id` são a mesma coisa. O `id` de sua conta de registro é o escopo do orçamento com o qual a solicitação de assinatura é iniciada. É importante conhecer a ID porque ela é um parâmetro necessário que você usa posteriormente no artigo para criar uma assinatura.

<!-- 
### [PowerShell](#tab/azure-powershell-getEnrollments)

we're still working on enabling PowerShell SDK for billing APIs. Check back soon.

-->


<!--
### [Azure CLI](#tab/azure-cli-getEnrollments)

we're still working on enabling CLI SDK for billing APIs. Check back soon.
-->

---

## <a name="create-subscriptions-under-a-specific-enrollment-account"></a>Crie assinaturas em uma conta de registro específico

O exemplo a seguir cria uma assinatura denominada *Assinatura da Equipe de Desenvolvimento* na conta de registro selecionada na etapa anterior. 

### <a name="rest"></a>[REST](#tab/rest-EA)

Chame a API PUT para criar uma solicitação/um alias de criação de assinatura.

```json
PUT  https://management.azure.com/providers/Microsoft.Subscription/aliases/sampleAlias?api-version=2020-09-01 
```

No corpo da solicitação, forneça o `billingScope` e a `id` de um de seus `enrollmentAccounts`.

```json 
{
  "properties": {
        "billingScope": "/providers/Microsoft.Billing/BillingAccounts/1234567/enrollmentAccounts/7654321",
        "DisplayName": "Dev Team Subscription", //Subscription Display Name
        "Workload": "Production"
  }
}
```

Os valores permitidos para `Workload` são `Production` e `DevTest`.

#### <a name="response"></a>Resposta

```json
{
  "id": "/providers/Microsoft.Subscription/aliases/sampleAlias",
  "name": "sampleAlias",
  "type": "Microsoft.Subscription/aliases",
  "properties": {
    "subscriptionId": "b5bab918-e8a9-4c34-a2e2-ebc1b75b9d74",
    "provisioningState": "Accepted"
  }
}
```

É possível executar uma função GET na mesma URL para obter o status da solicitação.

### <a name="request"></a>Solicitação

```json
GET https://management.azure.com/providers/Microsoft.Subscription/aliases/sampleAlias?api-version=2020-09-01
```

### <a name="response"></a>Resposta

```json
{
  "id": "/providers/Microsoft.Subscription/aliases/sampleAlias",
  "name": "sampleAlias",
  "type": "Microsoft.Subscription/aliases",
  "properties": {
    "subscriptionId": "b5bab918-e8a9-4c34-a2e2-ebc1b75b9d74",
    "provisioningState": "Succeeded"
  }
}
```

Um status em andamento será retornado como um estado `Accepted` em `provisioningState`.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell-EA)

Para instalar a versão mais recente do módulo que contém o cmdlet `New-AzSubscriptionAlias`, execute `Install-Module Az.Subscription`. Para instalar uma versão recente do PowerShellGet, confira [Obter o Módulo PowerShellGet](/powershell/scripting/gallery/installing-psget).

Execute o comando [New-AzSubscriptionAlias](/powershell/module/az.subscription/new-azsubscription) usando o escopo do orçamento `"/providers/Microsoft.Billing/BillingAccounts/1234567/enrollmentAccounts/7654321"`. 

```azurepowershell-interactive
New-AzSubscriptionAlias -AliasName "sampleAlias" -SubscriptionName "Dev Team Subscription" -BillingScope "/providers/Microsoft.Billing/BillingAccounts/1234567/enrollmentAccounts/7654321" -Workload 'Production"
```

Você obterá subscriptionId como parte da resposta do comando.

```azurepowershell
{
  "id": "/providers/Microsoft.Subscription/aliases/sampleAlias",
  "name": "sampleAlias",
  "type": "Microsoft.Subscription/aliases",
  "properties": {
    "provisioningState": "Succeeded",
    "subscriptionId": "4921139b-ef1e-4370-a331-dd2229f4f510"
  }
}
```

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli-EA)

Primeiro, instale a extensão executando `az extension add --name account` e `az extension add --name alias`.

Execute o comando [az account alias create](/cli/azure/ext/account/account/alias?view=azure-cli-latest#ext_account_az_account_alias_create&preserve-view=true), além de fornecer `billing-scope` e `id` de um dos seus `enrollmentAccounts`. 

```azurecli-interactive
az account alias create --name "sampleAlias" --billing-scope "/providers/Microsoft.Billing/billingAccounts/1234567/enrollmentAccounts/654321" --display-name "Dev Team Subscription" --workload "Production"
```

Você obterá subscriptionId como parte da resposta do comando.

```azurecli
{
  "id": "/providers/Microsoft.Subscription/aliases/sampleAlias",
  "name": "sampleAlias",
  "properties": {
    "provisioningState": "Succeeded",
    "subscriptionId": "4921139b-ef1e-4370-a331-dd2229f4f510"
  },
  "type": "Microsoft.Subscription/aliases"
}
```

---

## <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Limitações da API de criação de assinatura do Azure Enterprise

- Somente as assinaturas do Azure Enterprise poderão ser criadas usando essa API.
- Há um limite de 2.000 assinaturas por conta de registro. Depois disso, mais assinaturas da conta podem ser criadas apenas no portal do Azure. Para criar mais assinaturas por meio da API, crie outra conta de registro. Assinaturas canceladas, excluídas e transferidas são consideradas para o limite de 2000.
- Os usuários que não são Proprietários da Conta, mas foram adicionados a uma conta de registro por meio do Azure RBAC, não podem criar assinaturas no portal do Azure.
- Você não pode selecionar o locatário para a assinatura a ser criada. A assinatura é sempre criada no locatário inicial do Proprietário da Conta. Para mover a assinatura para um locatário diferente, consulte [alterar o locatário da assinatura](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).


## <a name="next-steps"></a>Próximas etapas

* Agora que você criou uma assinatura, conceda essa capacidade a outros usuários e entidades de serviço. Para saber mais, veja [Conceder acesso para criar assinaturas do Azure Enterprise (versão prévia)](grant-access-to-create-subscription.md).
* Para obter mais informações sobre como gerenciar um grande número de assinaturas usando grupos de gerenciamento, confira como [Organizar seus recursos com os grupos de gerenciamento do Azure](../../governance/management-groups/overview.md).
