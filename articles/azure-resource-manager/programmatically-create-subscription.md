---
title: Criar programaticamente assinaturas do Azure Enterprise | Microsoft Docs
description: Aprenda a criar programaticamente assinaturas adicionais do Azure Enterprise ou Enterprise Dev/Test.
services: azure-resource-manager
author: jureid
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 04/10/2019
ms.author: jureid
ms.openlocfilehash: cf325b93c626e0c7f9584449154e2d531995cdc5
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204341"
---
# <a name="programmatically-create-azure-enterprise-subscriptions-preview"></a>Criar programaticamente assinaturas do Azure Enterprise (versão prévia)

Como um cliente do Azure em [Contrato Enterprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/), você pode criar EA (MS-AZR-0017P) e assinaturas de desenvolvimento/teste de EA (MS-AZR-0148P) programaticamente. Neste artigo, você aprenderá a criar assinaturas programaticamente usando o Azure Resource Manager.

Quando você cria uma assinatura do Azure por meio dessa API, essa assinatura é governada pelo contrato sob o qual você obteve serviços do Microsoft Azure da Microsoft ou de um revendedor autorizado. Para saber mais, confira [Informações Legais do Microsoft Azure](https://azure.microsoft.com/support/legal/).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

Você deve ter uma função de proprietário na conta de registro que você deseja criar assinaturas no. Há duas maneiras de obter essas funções:

* O administrador de registro pode [tornar você um proprietário de conta](https://ea.azure.com/helpdocs/addNewAccount) (de entrada necessário) que torna um proprietário da conta do registro. Siga as instruções no email de convite que você recebe para criar manualmente uma assinatura inicial. Confirme a propriedade da conta e criar manualmente uma assinatura de EA inicial antes de prosseguir para a próxima etapa. Apenas adicionar a conta ao registro não é suficiente.

* Um proprietário existente da conta de inscrição pode [conceder acesso a você](grant-access-to-create-subscription.md). Da mesma forma, se você quiser usar uma entidade de serviço para criar a assinatura do EA, você deve [conceder a essa entidade de serviço a capacidade de criar assinaturas](grant-access-to-create-subscription.md).

## <a name="find-accounts-you-have-access-to"></a>Localize as contas às quais você tem acesso

Depois que você for adicionado a um registro de EA do Azure como proprietário da conta, o Azure usa a relação de registro de conta para determinar onde cobrar os encargos de assinatura. Todas as assinaturas criadas sob a conta serão cobradas na direção que a conta está no registro EA. Para criar assinaturas, você deve passar valores sobre a conta de registro e as entidades de usuário a fim de ser dono da assinatura. 

Para executar os comandos a seguir, você deve estar conectado ao *diretório base* do proprietário da conta, que é o diretório em que as assinaturas são criadas por padrão.

## <a name="resttabrest"></a>[REST](#tab/rest)

Solicitação para listar todas as contas de registro que você tem acesso a:

```json
GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
```

Azure responde com uma lista de todas as contas de registro a que você tem acesso:

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

Use a `principalName` propriedade para identificar a conta na qual você deseja que as assinaturas sejam cobradas. Copie o `name` dessa conta. Por exemplo, se você quisesse criar assinaturas sob o SignUpEngineering@contoso.com conta de registro, você poderia copiar ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Esta é a ID de objeto da conta do registro. Cole esse valor em algum lugar para que você pode usá-lo na próxima etapa como `enrollmentAccountObjectId`.

## <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Abra [Azure Cloud Shell](https://shell.azure.com/) e selecione o PowerShell.

Use o cmdlet [Get-AzEnrollmentAccount](/powershell/module/az.billing/get-azenrollmentaccount) para listar todas as contas de registro às quais você tem acesso.

```azurepowershell-interactive
Get-AzEnrollmentAccount
```

O Azure responde com uma lista de contas de registro que você tem acesso a:

```azurepowershell
ObjectId                               | PrincipalName
747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
```
Use a `principalName` propriedade para identificar a conta na qual você deseja que as assinaturas sejam cobradas. Copie o `ObjectId` dessa conta. Por exemplo, se você quisesse criar assinaturas sob o SignUpEngineering@contoso.com conta de registro, você poderia copiar ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Cole a ID de objeto em algum lugar para que você pode usá-lo na próxima etapa, como o `enrollmentAccountObjectId`.

## <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

Use o comando [az billing enrollment-account list](https://aka.ms/EASubCreationPublicPreviewCLI) para listar todas as contas de registro às quais você tem acesso.

```azurecli-interactive 
az billing enrollment-account list
```

O Azure responde com uma lista de contas de registro que você tem acesso a:

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

Use a `principalName` propriedade para identificar a conta na qual você deseja que as assinaturas sejam cobradas. Copie o `name` dessa conta. Por exemplo, se você quisesse criar assinaturas sob o SignUpEngineering@contoso.com conta de registro, você poderia copiar ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Esta é a ID de objeto da conta do registro. Cole esse valor em algum lugar para que você pode usá-lo na próxima etapa como `enrollmentAccountObjectId`.

---

## <a name="create-subscriptions-under-a-specific-enrollment-account"></a>Crie assinaturas em uma conta de registro específico

O exemplo a seguir cria uma assinatura denominada *assinatura de equipe de desenvolvimento* na conta de registro selecionada na etapa anterior. A oferta de assinatura está *MS-AZR - 0017p* (regular Microsoft Enterprise Agreement). Ele também adiciona dois usuários como Proprietários RBAC para a assinatura.

# <a name="resttabrest"></a>[REST](#tab/rest)

Faça o seguinte solicite, substituindo `<enrollmentAccountObjectId>` com o `name` copiado da primeira etapa (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Se você quiser especificar proprietários, saiba [como obter IDs de objeto de usuário](grant-access-to-create-subscription.md#userObjectId).

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

| Nome do elemento  | Obrigatório | Type   | DESCRIÇÃO                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | Não      | Cadeia de caracteres | O nome de exibição da assinatura. Se não for especificado, será definido como o nome da oferta, como "Microsoft Azure Enterprise".                                 |
| `offerType`   | Sim      | Cadeia de caracteres | A oferta da assinatura. As duas opções para EA são [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (uso de produção) e [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (desenvolvimento/teste, precisa ser [ativado usando o portal EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `owners`      | Não       | Cadeia de caracteres | A ID de objeto de qualquer usuário que você deseja adicionar como um proprietário de RBAC na assinatura quando ela é criada.  |

Em resposta, você obtém um objeto `subscriptionOperation` para monitoramento. Quando terminar a criação de assinatura, o objeto `subscriptionOperation` retornaria um objeto `subscriptionLink`, que tem a ID da assinatura.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Primeiro, instale esse módulo de visualização, executando `Install-Module Az.Subscription -AllowPrerelease`. Para certificar-se de que `-AllowPrerelease` funciona, instale uma versão recente do PowerShellGet do [Módulo Get do PowerShellGet](/powershell/gallery/installing-psget).

Execute o [New-AzSubscription](/powershell/module/az.subscription) comando abaixo, substituindo `<enrollmentAccountObjectId>` com o `ObjectId` coletados na primeira etapa (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Se você quiser especificar proprietários, saiba [como obter IDs de objeto de usuário](grant-access-to-create-subscription.md#userObjectId).

```azurepowershell-interactive
New-AzSubscription -OfferType MS-AZR-0017P -Name "Dev Team Subscription" -EnrollmentAccountObjectId <enrollmentAccountObjectId> -OwnerObjectId <userObjectId1>,<servicePrincipalObjectId>
```

| Nome do elemento  | Obrigatório | Type   | DESCRIÇÃO                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `Name` | Não      | Cadeia de caracteres | O nome de exibição da assinatura. Se não for especificado, será definido como o nome da oferta, como "Microsoft Azure Enterprise".                                 |
| `OfferType`   | Sim      | Cadeia de caracteres | A oferta da assinatura. As duas opções para EA são [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (uso de produção) e [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (desenvolvimento/teste, precisa ser [ativado usando o portal EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `EnrollmentAccountObjectId`      | Sim       | Cadeia de caracteres | A ID de Objeto da conta do registro que a assinatura é criada e cobrada. Esse valor é um GUID que você obteve de `Get-AzEnrollmentAccount`. |
| `OwnerObjectId`      | Não       | Cadeia de caracteres | A ID de objeto de qualquer usuário que você deseja adicionar como um proprietário de RBAC na assinatura quando ela é criada.  |
| `OwnerSignInName`    | Não       | Cadeia de caracteres | O endereço de e-mail de qualquer usuário que você gostaria de adicionar como um Proprietário RBAC na assinatura quando ela é criada. Você pode usar este parâmetro em vez de `OwnerObjectId`.|
| `OwnerApplicationId` | Não       | Cadeia de caracteres | O ID do aplicativo de qualquer serviço principal que você gostaria de adicionar como um Proprietário RBAC na assinatura quando ela é criada. Você pode usar este parâmetro em vez de `OwnerObjectId`. Ao usar esse parâmetro, o principal de serviço deve ter [acesso de leitura ao diretório](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole).| 

Para ver a lista completa de todos os parâmetros, confira [New-AzSubscription](/powershell/module/az.subscription.preview).

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

Primeiro, instale a extensão de visualização, executando `az extension add --name subscription`.

Execute o [criar conta az](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create) comando abaixo, substituindo `<enrollmentAccountObjectId>` com o `name` copiadas na primeira etapa (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Se você quiser especificar proprietários, saiba [como obter IDs de objeto de usuário](grant-access-to-create-subscription.md#userObjectId).

```azurecli-interactive 
az account create --offer-type "MS-AZR-0017P" --display-name "Dev Team Subscription" --enrollment-account-object-id "<enrollmentAccountObjectId>" --owner-object-id "<userObjectId>","<servicePrincipalObjectId>"
```

| Nome do elemento  | Obrigatório | Type   | DESCRIÇÃO                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `display-name` | Não      | Cadeia de caracteres | O nome de exibição da assinatura. Se não for especificado, será definido como o nome da oferta, como "Microsoft Azure Enterprise".                                 |
| `offer-type`   | Sim      | Cadeia de caracteres | A oferta da assinatura. As duas opções para EA são [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (uso de produção) e [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (desenvolvimento/teste, precisa ser [ativado usando o portal EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `enrollment-account-object-id`      | Sim       | Cadeia de caracteres | A ID de Objeto da conta do registro que a assinatura é criada e cobrada. Esse valor é um GUID que você obteve de `az billing enrollment-account list`. |
| `owner-object-id`      | Não       | Cadeia de caracteres | A ID de objeto de qualquer usuário que você deseja adicionar como um proprietário de RBAC na assinatura quando ela é criada.  |
| `owner-upn`    | Não       | Cadeia de caracteres | O endereço de e-mail de qualquer usuário que você gostaria de adicionar como um Proprietário RBAC na assinatura quando ela é criada. Você pode usar este parâmetro em vez de `owner-object-id`.|
| `owner-spn` | Não       | Cadeia de caracteres | O ID do aplicativo de qualquer serviço principal que você gostaria de adicionar como um Proprietário RBAC na assinatura quando ela é criada. Você pode usar este parâmetro em vez de `owner-object-id`. Ao usar esse parâmetro, o principal de serviço deve ter [acesso de leitura ao diretório](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole).| 

Para ver uma lista completa de todos os parâmetros, consulte [criar conta az](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create).

---

## <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Limitações da API de criação de assinatura do Azure Enterprise

- Somente as assinaturas do Azure Enterprise podem ser criadas usando esta API.
- Há um limite inicial de 50 assinaturas por conta de registro, mas você pode [criar uma solicitação de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) para aumentar o limite de 200. Depois disso, as assinaturas só podem ser criadas por meio do Centro de contas.
- Precisa ter pelo menos um EA ou EA desenvolvimento/teste assinaturas sob a conta, o que significa que o Proprietário da Conta tenha se inscrito manualmente pelo menos uma vez.
- Os usuários que não são Proprietários da Conta, mas foram adicionados a uma conta de registro por meio do RBAC, não podem criar assinaturas usando o Centro de Contas.
- Você não pode selecionar o locatário para a assinatura a ser criada. A assinatura é sempre criada no locatário inicial do Proprietário da Conta. Para mover a assinatura para um locatário diferente, consulte [alterar o locatário da assinatura](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="next-steps"></a>Próximas etapas

* Para obter um exemplo sobre como criar assinaturas usando .NET, consulte [código de exemplo no GitHub](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Agora que você criou uma assinatura, conceda essa capacidade a outros usuários e entidades de serviço. Para saber mais, veja [Conceder acesso para criar assinaturas do Azure Enterprise (versão prévia)](grant-access-to-create-subscription.md).
* Para saber mais sobre como gerenciar grandes números de assinaturas usando grupos de gerenciamento, consulte [Organizar seus recursos com grupos de gerenciamento do Azure](management-groups-overview.md)
