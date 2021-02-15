---
title: Alterar padrões de tempo de vida do token para aplicativos personalizados do Azure AD
description: Como atualizar as políticas de tempo de vida do token para o aplicativo que você está desenvolvendo no Azure AD
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 10/23/2020
ms.author: ryanwi
ms.custom: aaddev, seoapril2019
ROBOTS: NOINDEX
ms.openlocfilehash: d39f378171443f028ef6b549b120b22f2a3405c4
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99582934"
---
# <a name="how-to-change-the-token-lifetime-defaults-for-a-custom-developed-application"></a>Como alterar os padrões de tempo de vida do token para um aplicativo personalizado

Este artigo mostra como usar o PowerShell do Azure AD para definir uma política de tempo de vida do token de acesso. O Azure AD Premium permite que desenvolvedores de aplicativos e administradores de locatários configurem o tempo de vida de tokens emitidos para clientes não confidenciais. As políticas de tempo de vida do token são definidas com base em todos os locatários ou nos recursos que estão sendo acessados.

> [!IMPORTANT]
> Depois de maio de 2020, os locatários não poderão mais configurar a atualização e tempos de vida de token de sessão.  Azure Active Directory deixará de respeitar a configuração existente e o token de sessão em políticas após 30 de janeiro de 2021. Você ainda pode configurar tempos de vida de token de acesso após a reprovação. Para obter mais informações, leia [tempos de vida de token configuráveis no Azure ad](./active-directory-configurable-token-lifetimes.md).
> Implementamos os [recursos de gerenciamento de sessão de autenticação](../conditional-access/howto-conditional-access-session-lifetime.md)   no acesso condicional do Azure AD. Você pode usar esse novo recurso para configurar tempos de vida de token de atualização definindo a frequência de entrada.  

Para definir uma política de tempo de vida do token de acesso, baixe o [módulo do PowerShell do Azure ad](https://www.powershellgallery.com/packages/AzureADPreview).
Execute o comando **Connect-AzureAD -Confirm**.

Aqui está um exemplo de política que exige que os usuários se autentiquem com mais frequência em seu aplicativo Web. Essa política define o tempo de vida do acesso à entidade de serviço do seu aplicativo Web. Crie a política e atribua-a à sua entidade de serviço. Você também precisará da ObjectId de sua entidade de serviço.

```powershell
$policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"02:00:00"}}') -DisplayName "WebPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"

$sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"

Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
```

## <a name="next-steps"></a>Próximas etapas

* Consulte [Tempos de vida de token configuráveis no Azure AD](./active-directory-configurable-token-lifetimes.md) para saber como configurar tempos de vida de token emitidos pelo Azure AD, incluindo como definir tempos de vida de token para todos os aplicativos na organização, para um aplicativo de vários locatários ou para uma entidade de serviço específica na organização. 
* [Referência de token do Azure AD](./id-tokens.md)
