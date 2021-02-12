---
title: Como localizar sua ID de locatário-Azure Active Directory
description: Instruções sobre como localizar e Azure Active Directory a ID de locatário para uma assinatura existente do Azure.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 10/30/2020
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, devx-track-azurecli
ms.collection: M365-identity-device-management
ms.openlocfilehash: 910e68e5b8933875f252faf65fb58e514dee8254
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96326583"
---
# <a name="how-to-find-your-azure-active-directory-tenant-id"></a>Como localizar sua ID de locatário Azure Active Directory

As assinaturas do Azure têm uma relação de confiança com Azure Active Directory (Azure AD). O Azure AD é confiável para autenticar usuários, serviços e dispositivos para a assinatura. Cada assinatura tem uma ID de locatário associada a ela e há algumas maneiras de encontrar a ID de locatário para sua assinatura.

## <a name="find-tenant-id-through-the-azure-portal"></a>Localizar a ID do locatário por meio do portal do Azure

1. Entre no [portal do Azure](https://portal.azure.com).
 
1. Selecione **Azure Active Directory**.

1. Selecione **Propriedades**.

1. Em seguida, role para baixo até o campo **ID do locatário** . Sua ID de locatário estará na caixa.

:::image type="content" source="media/active-directory-how-to-find-tenant/portal-tenant-id.png" alt-text="Azure Active Directory-Properties-ID de locatário-campo de ID de locatário":::

## <a name="find-tenant-id-with-powershell"></a>Localizar ID de locatário com o PowerShell

Você também pode encontrar o locatário programaticamente. Para localizar a ID de locatário com Azure PowerShell, use o cmdlet `Get-AzTenant` .

```azurepowershell-interactive
Connect-AzAccount
Get-AzTenant
```
   
Para obter mais informações, consulte esta Azure PowerShell referência de cmdlet para [Get-AzTenant](/powershell/module/az.accounts/get-aztenant).


## <a name="find-tenant-id-with-cli"></a>Localizar ID de locatário com a CLI
Se você quiser usar uma interface de linha de comando para localizar a ID do locatário, poderá fazer isso com [CLI do Azure](/cli/azure/install-azure-cli) ou [Microsoft 365 CLI](https://pnp.github.io/cli-microsoft365/). 

Para CLI do Azure, use um dos comandos **AZ login**, **lista de contas AZ** ou **AZ Account locatário List** , conforme mostrado no exemplo a seguir. Observe a propriedade **tenantid** para cada uma de suas assinaturas na saída de cada comando.

```azurecli-interactive
az login
az account list
az account tenant list
```

Para obter mais informações, consulte referência de comando [AZ login](/cli/azure/reference-index#az_login) , referência de comando de [conta](/cli/azure/ext/account/account) AZ ou referência de comando [AZ Account locatário](/cli/azure/ext/account/account/tenant) .


Para Microsoft 365 CLI, use a **ID de locatário** do cmdlet, conforme mostrado no exemplo a seguir:
 
```cli
m365 tenant id get
```

Para obter mais informações, consulte a referência de comando [get Microsoft 365 ID de locatário](https://pnp.github.io/cli-microsoft365/cmd/tenant/id/id-get/) .


## <a name="next-steps"></a>Próximas etapas

- Para criar um novo locatário do Azure AD, consulte [início rápido: criar um novo locatário no Azure Active Directory](active-directory-access-create-new-tenant.md).

- Para saber como associar ou adicionar uma assinatura a um locatário, consulte [associar ou adicionar uma assinatura do Azure ao seu locatário Azure Active Directory](active-directory-how-subscriptions-associated-directory.md).

- Para saber como localizar a ID de objeto, consulte [localizar a ID de objeto de usuário](/partner-center/find-ids-and-domain-names#find-the-user-object-id).