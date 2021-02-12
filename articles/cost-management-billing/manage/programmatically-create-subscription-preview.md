---
title: Criar assinaturas do Azure de maneira programática com APIs em versão prévia
description: Saiba como criar assinaturas adicionais do Azure de maneira programática usando as versões prévias da API REST, da CLI do Azure e do Azure PowerShell.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 11/17/2020
ms.reviewer: andalmia
ms.author: banders
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 68d890386d53b4115c773b128f8678bac9579e53
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/18/2020
ms.locfileid: "94844326"
---
# <a name="programmatically-create-azure-subscriptions-with-preview-apis"></a>Criar assinaturas do Azure de maneira programática com APIs em versão prévia

Este artigo ajuda você a criar assinaturas do Azure de maneira programática usando nossa API em versão prévia mais antiga. Neste artigo, você aprenderá a criar assinaturas programaticamente usando o Azure Resource Manager.

Temos novos artigos para a versão mais recente da API para uso com diferentes tipos de assinatura do contrato do Azure:

- [Criar assinaturas do EA de modo programático com a API mais recente](programmatically-create-subscription-enterprise-agreement.md)
- [Criar assinaturas do MCA de modo programático com a API mais recente](programmatically-create-subscription-microsoft-customer-agreement.md)
- [Criar assinaturas do MPA de modo programático com a API mais recente](Programmatically-create-subscription-microsoft-customer-agreement.md)

No entanto, você ainda pode usar as informações deste artigo se não quiser usar a versão mais recente da API.

Os clientes do Azure com uma conta de cobrança dos seguintes tipos de contrato poderão criar assinaturas de modo programático:

- Contrato Enterprise
- MCA (Contrato de Cliente da Microsoft)
- MPA (Contrato de Parceiro da Microsoft)

Quando você cria uma assinatura do Azure de maneira programática, essa assinatura é regida pelo contrato sob o qual você obteve os serviços Azure da Microsoft ou de um revendedor autorizado. Para obter mais informações, confira [Informações Legais do Microsoft Azure](https://azure.microsoft.com/support/legal/).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="create-subscriptions-for-an-ea-billing-account"></a>Criar assinaturas para uma conta de cobrança do EA

Use as informações nas seções a seguir para criar assinaturas de EA.

### <a name="prerequisites"></a>Pré-requisitos

Você precisa ter uma função de Proprietário em uma Conta de Registro para criar uma assinatura. Há duas maneiras de obter a função:

* O administrador corporativo de seu registro pode [fazer de você Proprietário da Conta](https://ea.azure.com/helpdocs/addNewAccount) e entrar, o que faz de você um Proprietário da Conta de Registro.
* Um proprietário existente da conta de inscrição pode [conceder acesso a você](grant-access-to-create-subscription.md). Do mesmo modo, para usar uma entidade de serviço com o objetivo de criar uma assinatura de EA, você deverá [conceder a essa entidade de serviço a capacidade de criar assinaturas](grant-access-to-create-subscription.md).

### <a name="find-accounts-you-have-access-to"></a>Localize as contas às quais você tem acesso

Depois que você for adicionado a uma Conta de Registro associada a um Proprietário da Conta, o Azure usa a relação de conta para registro para determinar quando cobrar os encargos de assinatura. Todas as assinaturas criadas na conta serão cobradas no registro de EA do qual a conta faz parte. Para criar assinaturas, você deve passar valores sobre a conta de registro e as entidades de usuário a fim de ser dono da assinatura.

Para executar os comandos a seguir, você precisa estar conectado ao *diretório base* do Proprietário da Conta, que é o diretório em que as assinaturas são criadas por padrão.

### <a name="rest"></a>[REST](#tab/rest)

Solicitação para listar todas as contas de registro às quais você tem acesso:

```json
GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
```

A resposta da API lista todas as contas de registro às quais você tem acesso:

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Billing/enrollmentAccounts",
      "properties": {
        "principalName": "SignUpEngineering@contoso.com"
      }
    },
    {
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Billing/enrollmentAccounts",
      "properties": {
        "principalName": "BillingPlatformTeam@contoso.com"
      }
    }
  ]
}
```

Use a `principalName` propriedade para identificar a conta na qual você deseja que as assinaturas sejam cobradas. Copie o `name` dessa conta. Por exemplo, para criar assinaturas na conta de registro SignUpEngineering@contoso.com, copie ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. O identificador é a ID de objeto da conta de registro. Cole esse valor em algum lugar para usá-lo na próxima etapa como `enrollmentAccountObjectId`.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Abra o [Azure Cloud Shell](https://shell.azure.com/) e selecione PowerShell.

Use o cmdlet [Get-AzEnrollmentAccount](/powershell/module/az.billing/get-azenrollmentaccount) para listar todas as contas de registro às quais você tem acesso.

```azurepowershell-interactive
Get-AzEnrollmentAccount
```

O Azure responde com uma lista das contas de registro às quais você tem acesso:

```azurepowershell
ObjectId                               | PrincipalName
747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
```
Use a `principalName` propriedade para identificar a conta na qual você deseja que as assinaturas sejam cobradas. Copie o `ObjectId` dessa conta. Por exemplo, para criar assinaturas na conta de registro SignUpEngineering@contoso.com, copie ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Cole a ID do objeto em algum lugar para usá-la na próxima etapa como `enrollmentAccountObjectId`.

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Use o comando [az billing enrollment-account list](/cli/azure/billing) para listar todas as contas de registro às quais você tem acesso.

```azurecli-interactive
az billing enrollment-account list
```

O Azure responde com uma lista das contas de registro às quais você tem acesso:

```json
[
  {
    "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "name": "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "principalName": "SignUpEngineering@contoso.com",
    "type": "Microsoft.Billing/enrollmentAccounts",
  },
  {
    "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "principalName": "BillingPlatformTeam@contoso.com",
    "type": "Microsoft.Billing/enrollmentAccounts",
  }
]
```

Use a `principalName` propriedade para identificar a conta na qual você deseja que as assinaturas sejam cobradas. Copie o `name` dessa conta. Por exemplo, para criar assinaturas na conta de registro SignUpEngineering@contoso.com, copie ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. O identificador é a ID de objeto da conta de registro. Cole esse valor em algum lugar para usá-lo na próxima etapa como `enrollmentAccountObjectId`.

---

### <a name="create-subscriptions-under-a-specific-enrollment-account"></a>Crie assinaturas em uma conta de registro específico

O exemplo a seguir cria uma assinatura denominada *Assinatura da Equipe de Desenvolvimento* na conta de registro selecionada na etapa anterior. A oferta de assinatura é *MS-AZR-0017P* (Contrato Enterprise da Microsoft regular). Ela também adiciona dois usuários como Proprietários do Azure RBAC à assinatura.

### <a name="rest"></a>[REST](#tab/rest)

Faça a solicitação a seguir, substituindo `<enrollmentAccountObjectId>` pela `name` copiada na primeira etapa (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Para especificar os proprietários, veja [como obter as IDs de objeto do usuário](grant-access-to-create-subscription.md#userObjectId).

```json
POST https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>/providers/Microsoft.Subscription/createSubscription?api-version=2018-03-01-preview

{
  "displayName": "Dev Team Subscription",
  "offerType": "MS-AZR-0017P",
  "owners": [
    {
      "objectId": "<userObjectId>"
    },
    {
      "objectId": "<servicePrincipalObjectId>"
    }
  ]
}
```

| Nome do elemento  | Obrigatório | Type   | Descrição                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | Não      | String | O nome de exibição da assinatura. Se não for especificado, será definido como o nome da oferta, como "Microsoft Azure Enterprise".                                 |
| `offerType`   | Sim      | String | A oferta da assinatura. As duas opções para EA são [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (uso de produção) e [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (desenvolvimento/teste, precisa ser [ativado usando o portal EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `owners`      | Não       | String | A ID do Objeto de qualquer usuário que você deseja adicionar como Proprietário do Azure RBAC na assinatura quando ela é criada.  |

Na resposta, como parte do cabeçalho `Location`, você recebe uma URL que pode consultar para obter o status na operação de criação da assinatura. Quando terminar a criação de assinatura, um GET na URL de `Location` retornará um objeto `subscriptionLink`, que tem a ID da assinatura. Para obter mais detalhes, veja a [Documentação da API de assinatura](/rest/api/subscription/)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para instalar a versão mais recente do módulo que contém o cmdlet `New-AzSubscription`, execute `Install-Module Az.Subscription`. Para instalar uma versão recente do PowerShellGet, confira [Obter o Módulo PowerShellGet](/powershell/scripting/gallery/installing-psget).

Execute o comando [New-AzSubscription](/powershell/module/az.subscription) abaixo, substituindo `<enrollmentAccountObjectId>` pelo `ObjectId` coletado na primeira etapa (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Para especificar os proprietários, veja [como obter as IDs de objeto do usuário](grant-access-to-create-subscription.md#userObjectId).

```azurepowershell-interactive
New-AzSubscription -OfferType MS-AZR-0017P -Name "Dev Team Subscription" -EnrollmentAccountObjectId <enrollmentAccountObjectId> -OwnerObjectId <userObjectId1>,<servicePrincipalObjectId>
```

| Nome do elemento  | Obrigatório | Type   | Descrição |
|---------------|----------|--------|----|
| `Name` | Não      | String | O nome de exibição da assinatura. Se não for especificado, será definido como o nome da oferta, por exemplo, *Microsoft Azure Enterprise*. |
| `OfferType`   | Sim      | String | A oferta da assinatura. As duas opções para EA são [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (uso de produção) e [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (desenvolvimento/teste, precisa ser [ativado usando o portal EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `EnrollmentAccountObjectId`      | Sim       | String | A ID de Objeto da conta do registro que a assinatura é criada e cobrada. O valor é um GUID que você obtém de `Get-AzEnrollmentAccount`. |
| `OwnerObjectId`      | Não       | String | A ID do Objeto de qualquer usuário a ser adicionado como Proprietário do Azure RBAC na assinatura quando ela é criada.  |
| `OwnerSignInName`    | Não       | String | O endereço de email de qualquer usuário a ser adicionado como Proprietário do Azure RBAC na assinatura quando ela é criada. Você pode usar o parâmetro em vez de `OwnerObjectId`.|
| `OwnerApplicationId` | Não       | String | A ID do aplicativo de qualquer entidade de serviço a ser adicionado como um Proprietário do Azure RBAC na assinatura quando ela é criada. Você pode usar o parâmetro em vez de `OwnerObjectId`. Ao usar o parâmetro, a entidade de serviço deve ter [acesso de leitura ao diretório](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole&preserve-view=true).|

Para ver a lista completa de todos os parâmetros, confira [New-AzSubscription](/powershell/module/az.subscription/New-AzSubscription).

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Primeiro, instale a extensão de versão prévia executando `az extension add --name subscription`.

Execute o comando [az account create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create&preserve-view=true) abaixo, substituindo `<enrollmentAccountObjectId>` pelo `name` que você copiou na primeira etapa (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Para especificar os proprietários, veja [como obter as IDs de objeto do usuário](grant-access-to-create-subscription.md#userObjectId).

```azurecli-interactive
az account create --offer-type "MS-AZR-0017P" --display-name "Dev Team Subscription" --enrollment-account-object-id "<enrollmentAccountObjectId>" --owner-object-id "<userObjectId>","<servicePrincipalObjectId>"
```

| Nome do elemento  | Obrigatório | Type   | Descrição |
|---------------|----------|--------|------------|
| `display-name` | Não      | String | O nome de exibição da assinatura. Se não for especificado, será definido como o nome da oferta, por exemplo, *Microsoft Azure Enterprise*.|
| `offer-type`   | Sim      | String | A oferta da assinatura. As duas opções para EA são [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (uso de produção) e [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (desenvolvimento/teste, precisa ser [ativado usando o portal EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `enrollment-account-object-id`      | Sim       | String | A ID de Objeto da conta do registro que a assinatura é criada e cobrada. O valor é um GUID que você obtém de `az billing enrollment-account list`. |
| `owner-object-id`      | Não       | String | A ID do Objeto de qualquer usuário a ser adicionado como Proprietário do Azure RBAC na assinatura quando ela é criada.  |
| `owner-upn`    | Não       | String | O endereço de email de qualquer usuário a ser adicionado como Proprietário do Azure RBAC na assinatura quando ela é criada. Você pode usar o parâmetro em vez de `owner-object-id`.|
| `owner-spn` | Não       | String | A ID do aplicativo de qualquer entidade de serviço a ser adicionado como um Proprietário do Azure RBAC na assinatura quando ela é criada. Você pode usar o parâmetro em vez de `owner-object-id`. Ao usar o parâmetro, a entidade de serviço deve ter [acesso de leitura ao diretório](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole&preserve-view=true).|

Para ver uma lista completa de todos os parâmetros, consulte [criar conta az](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create&preserve-view=true).

---

### <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Limitações da API de criação de assinatura do Azure Enterprise

- Somente assinaturas do Azure Enterprise podem ser criadas usando a API.
- Há um limite de 2.000 assinaturas por conta de registro. Depois disso, mais assinaturas da conta podem ser criadas apenas no portal do Azure. Se você quiser criar mais assinaturas por meio da API, crie outra conta de registro. Assinaturas canceladas, excluídas e transferidas são consideradas para o limite de 2000.
- Os usuários que não são Proprietários da Conta, mas foram adicionados a uma conta de registro com o Azure RBAC, não podem criar assinaturas no portal do Azure.
- Você não pode selecionar o locatário para a assinatura a ser criada. A assinatura é sempre criada no locatário inicial do Proprietário da Conta. Para mover a assinatura para um locatário diferente, consulte [alterar o locatário da assinatura](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).


## <a name="create-subscriptions-for-an-mca-account"></a>Criar assinaturas para uma conta do MCA

Use as informações nas seções a seguir para criar assinaturas para uma conta do MCA.

### <a name="prerequisites"></a>Pré-requisitos

Para criar assinaturas, você precisa ter uma função de proprietário, de colaborador ou de criador de assinatura do Azure em uma seção de fatura; ou uma função de proprietário ou de colaborador em um perfil de cobrança ou uma conta de cobrança. Para obter mais informações, confira [Funções e tarefas da cobrança de assinatura](understand-mca-roles.md#subscription-billing-roles-and-tasks).

Os exemplos a seguir usam APIs REST. Atualmente, não há suporte para o PowerShell nem para a CLI do Azure.

### <a name="find-billing-accounts-that-you-have-access-to"></a>Localizar as contas de cobrança às quais você tem acesso

Faça a solicitação a seguir para listar todas as contas de cobrança.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2019-10-01-preview
```
A resposta da API lista as contas de cobrança às quais você tem acesso.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "displayName": "Contoso",
        "hasReadAccess": true,
        "organizationId": "41b29574-xxxx-xxxx-xxxx-xxxxxxxxxxxxx_xxxx-xx-xx"
      },
      "type": "Microsoft.Billing/billingAccounts"
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "displayName": "Fabrikam",
        "hasReadAccess": true,
        "organizationId": "41b29574-xxxx-xxxx-xxxx-xxxxxxxxxxxxx_xxxx-xx-xx"
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}

```
Use a propriedade `displayName` para identificar a conta de cobrança para a qual você deseja criar assinaturas. Verifique se o agreementType da conta é *MicrosoftCustomerAgreement*. Copie o `name` da conta.  Por exemplo, para criar uma assinatura para a conta de cobrança `Contoso`, copie `5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx`. Cole esse valor em algum lugar para usá-lo na próxima etapa.

### <a name="find-invoice-sections-to-create-subscriptions"></a>Localizar as seções da fatura para criar assinaturas

Os preços da sua assinatura serão exibidos em uma seção da fatura do perfil de cobrança. Use a API a seguir para obter a lista de seções da fatura e perfis de cobrança nos quais você tem permissão para criar assinaturas do Azure.

Faça a solicitação a seguir, substituindo `<billingAccountName>` pela `name` copiada na primeira etapa (```5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx```).

```json
POST https://management.azure.com/providers/Microsoft.Billing/billingAccounts/<billingAccountName>/listInvoiceSectionsWithCreateSubscriptionPermission?api-version=2019-10-01-preview
```

A resposta da API lista todas as seções de fatura e os respectivos perfis de cobrança aos quais você tem acesso para criar assinaturas:

```json
{
    "value": [{
        "billingProfileDisplayName": "Contoso finance",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx",
        "enabledAzurePlans": [{
            "productId": "DZH318Z0BPS6",
            "skuId": "0001",
            "skuDescription": "Microsoft Azure Plan"
        }, {
            "productId": "DZH318Z0BPS6",
            "skuId": "0002",
            "skuDescription": "Microsoft Azure Plan for DevTest"
        }],
        "invoiceSectionDisplayName": "Development",
        "invoiceSectionId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx/invoiceSections/GJ77-xxxx-xxx-xxx"
    }, {
        "billingProfileDisplayName": "Contoso finance",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx",
        "enabledAzurePlans": [{
            "productId": "DZH318Z0BPS6",
            "skuId": "0001",
            "skuDescription": "Microsoft Azure Plan"
        }, {
            "productId": "DZH318Z0BPS6",
            "skuId": "0002",
            "skuDescription": "Microsoft Azure Plan for DevTest"
        }],
        "invoiceSectionDisplayName": "Testing",
        "invoiceSectionId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX"
  }]
}

```

Use a propriedade `invoiceSectionDisplayName` para identificar a seção da fatura para a qual você deseja criar assinaturas. Copie `invoiceSectionId`, `billingProfileId` e um dos `skuId` da seção da fatura. Por exemplo, para criar uma assinatura do tipo `Microsoft Azure plan` para a seção da fatura `Development`, copie `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX`, `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-xxxx-xxx-xxx` e `0001`. Cole os valorem em algum lugar para usá-los na próxima etapa.

### <a name="create-a-subscription-for-an-invoice-section"></a>Criar uma assinatura de uma seção da fatura

O exemplo a seguir cria uma assinatura denominada *assinatura da Equipe de Desenvolvimento* do tipo *Plano do Microsoft Azure* para a seção da fatura *Desenvolvimento*. A assinatura é cobrada no perfil de cobrança *Finanças da Contoso* e aparece na seção *Desenvolvimento* da fatura.

Faça a solicitação a seguir, substituindo `<invoiceSectionId>` pela `invoiceSectionId` copiada na segunda etapa (```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX```). Passe o `billingProfileId` e o `skuId` copiados da segunda etapa nos parâmetros de solicitação da API. Para especificar os proprietários, veja [como obter as IDs de objeto do usuário](grant-access-to-create-subscription.md#userObjectId).

```json
POST https://management.azure.com<invoiceSectionId>/providers/Microsoft.Subscription/createSubscription?api-version=2018-11-01-preview
```

```json
'{"displayName": "Dev Team subscription",
  "billingProfileId": "<billingProfileId>",
  "skuId": "<skuId>",
  "owners": [
      {
        "objectId": "<userObjectId>"
      },
      {
        "objectId": "<servicePrincipalObjectId>"
      }
    ],
  "costCenter": "35683",
  "managementGroupId": "/providers/Microsoft.Management/managementGroups/xxxxxxx",",
}'

```

| Nome do elemento  | Obrigatório | Type   | Descrição                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | Sim      | String | O nome de exibição da assinatura.|
| `billingProfileId`   | Sim      | String | A ID do perfil de cobrança que é cobrado pelos preços da assinatura.  |
| `skuId` | Sim      | String | A ID do SKU que determina o tipo de plano do Azure. |
| `owners`      | Não       | String | A ID de Objeto de qualquer entidade de serviço ou usuário a ser adicionado como um Proprietário do Azure RBAC na assinatura quando ela é criada.  |
| `costCenter` | Não      | String | O centro de custo associado à assinatura. Ele aparece no arquivo CSV de uso. |
| `managementGroupId` | Não      | String | A ID do grupo de gerenciamento ao qual a assinatura será adicionada. Para obter a lista de grupos de gerenciamento, confira [Grupos de Gerenciamento – Listar API](/rest/api/resources/managementgroups/list). Use a ID de um grupo de gerenciamento da API. |

Em resposta, você obtém um objeto `subscriptionCreationResult` para monitoramento. Quando a criação da assinatura é concluída, o objeto `subscriptionCreationResult` retorna um objeto `subscriptionLink`, que tem a ID da assinatura.

## <a name="create-subscriptions-for-an-mpa-billing-account"></a>Criar assinaturas para uma conta de cobrança do MPA

Use as informações nas seções a seguir para criar assinaturas para uma conta de cobrança do MPA.

### <a name="prerequisites"></a>Pré-requisitos

Você precisa ter uma função de Administrador Global ou de Agente Administrador na conta do Provedor de Soluções de Nuvem de sua organização para criar uma assinatura para sua conta de cobrança. Para obter mais informações, confira [Partner Center – Atribuir funções e permissões de usuários](/partner-center/permissions-overview).

Os exemplos a seguir usam APIs REST. Atualmente, não há suporte para o PowerShell nem para a CLI do Azure.

### <a name="find-the-billing-accounts-that-you-have-access-to"></a>Localizar as contas de cobrança às quais você tem acesso

Faça a solicitação abaixo para listar todas as contas de cobrança às quais você tem acesso.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2019-10-01-preview
```

A resposta da API lista as contas de cobrança.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftPartnerAgreement",
        "displayName": "Contoso",
        "hasReadAccess": true,
        "organizationId": "1d100e69-xxxx-xxxx-xxxx-xxxxxxxxxxxxx_xxxx-xx-xx"
      },
      "type": "Microsoft.Billing/billingAccounts"
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "displayName": "Fabrikam",
        "hasReadAccess": true,
        "organizationId": "1d100e69-xxxx-xxxx-xxxx-xxxxxxxxxxxxx_xxxx-xx-xx"
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}

```
Use a propriedade `displayName` para identificar a conta de cobrança para a qual você deseja criar assinaturas. Verifique se o agreementType da conta é o *MicrosoftPartnerAgreement*. Copie o `name` para a conta. Por exemplo, para criar uma assinatura para a conta de cobrança `Contoso`, copie `99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx`. Cole esse valor em algum lugar para usá-lo na próxima etapa.

### <a name="find-customers-that-have-azure-plans"></a>Localizar clientes que têm planos do Azure

Faça a solicitação a seguir, substituindo `<billingAccountName>` pelo `name` copiado na primeira etapa (```5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx```) para listar todos os clientes na conta de cobrança para os quais você pode criar assinaturas do Azure.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/<billingAccountName>/customers?api-version=2019-10-01-preview
```
A resposta da API lista os clientes na conta de cobrança com os planos do Azure. Você pode criar assinaturas para os clientes.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "properties": {
        "billingProfileDisplayName": "Contoso USD",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/JUT6-xxxx-xxxx-xxxx",
        "displayName": "Fabrikam toys"
      },
      "type": "Microsoft.Billing/billingAccounts/customers"
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/97c3fac4-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "97c3fac4-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "properties": {
        "billingProfileDisplayName": "Fabrikam sports",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/JUT6-xxxx-xxxx-xxxx",
        "displayName": "Fabrikam bakery"
      },
      "type": "Microsoft.Billing/billingAccounts/customers"
    }]
}

```

Use a propriedade `displayName` para identificar o cliente para o qual você deseja criar assinaturas. Copie o `id` do cliente. Por exemplo, para criar uma assinatura para `Fabrikam toys`, copie `/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx`. Cole esse valor em algum lugar para usá-lo em etapas posteriores.

### <a name="optional-for-indirect-providers-get-the-resellers-for-a-customer"></a>Opcional para Provedores indiretos: Obter os revendedores de um cliente

Se você for um Provedor indireto no modelo de dois níveis do CSP, poderá especificar um revendedor ao criar assinaturas para clientes.

Faça a solicitação a seguir, substituindo `<customerId>` pelo `id` copiado da segunda etapa (```/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx```) para listar todos os revendedores disponíveis para um cliente.

```json
GET https://management.azure.com<customerId>?$expand=resellers&api-version=2019-10-01-preview
```
A resposta da API lista os revendedores do cliente:

```json
{
  "value": [{
  "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2ed2c490-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "name": "2ed2c490-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.Billing/billingAccounts/customers",
  "properties": {
    "displayName": "Fabrikam toys",
    "resellers": [
      {
        "resellerId": "3xxxxx",
        "description": "Wingtip"
      }
    ]
  }
},
{
  "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/4ed2c793-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "name": "4ed2c793-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.Billing/billingAccounts/customers",
  "properties": {
    "displayName": "Fabrikam toys",
    "resellers": [
      {
        "resellerId": "5xxxxx",
        "description": "Tailspin"
      }
    ]
  }
}]
}
```

Use a propriedade `description` para identificar o revendedor associado à assinatura. Copie o `resellerId` do revendedor. Por exemplo, para associar `Wingtip`, copie `3xxxxx`. Cole esse valor em algum lugar para usá-lo na próxima etapa.

### <a name="create-a-subscription-for-a-customer"></a>Criar uma assinatura para um cliente

O exemplo a seguir cria uma assinatura denominada *assinatura da Equipe de Desenvolvimento* para *Fabrikam Toys* e associa o revendedor *Wingtip* à assinatura. 

Faça a solicitação a seguir, substituindo `<customerId>` pela `id` copiada na segunda etapa (```/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Passe o *resellerId* opcional copiado da segunda etapa nos parâmetros de solicitação da API.

```json
POST https://management.azure.com<customerId>/providers/Microsoft.Subscription/createSubscription?api-version=2018-11-01-preview
```

```json
'{"displayName": "Dev Team subscription",
  "skuId": "0001",
  "resellerId": "<resellerId>",
}'
```

| Nome do elemento  | Obrigatório | Type   | Descrição                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | Sim      | String | O nome de exibição da assinatura.|
| `skuId` | Sim      | String | A ID de SKU do plano do Azure. Usar *0001* para assinaturas do tipo Plano do Microsoft Azure |
| `resellerId`      | Não       | String | A ID do MPN do revendedor que será associada à assinatura.  |

Em resposta, você obtém um objeto `subscriptionCreationResult` para monitoramento. Quando a criação da assinatura é concluída, o objeto `subscriptionCreationResult` retorna um objeto `subscriptionLink`. Ele tem a ID da assinatura.

## <a name="next-steps"></a>Próximas etapas

- Para ver um exemplo de como criar uma assinatura do EA (Contrato Enterprise) usando .NET, confira [código de exemplo no GitHub](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Agora que você criou uma assinatura, conceda essa capacidade a outros usuários e entidades de serviço. Para saber mais, veja [Conceder acesso para criar assinaturas do Azure Enterprise (versão prévia)](grant-access-to-create-subscription.md).
* Para obter mais informações sobre como gerenciar um grande número de assinaturas usando grupos de gerenciamento, confira como [Organizar seus recursos com os grupos de gerenciamento do Azure](../../governance/management-groups/overview.md).
