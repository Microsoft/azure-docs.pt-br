---
title: Entrada direta usando o Microsoft Azure Active Directory B2C | Microsoft Docs
description: Saiba como preencher previamente o nome de usuário ou redirecionar diretamente para um provedor de identidade social.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/14/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 35e8efa269ab72477b06e86824d368d0a3dced03
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2021
ms.locfileid: "103197318"
---
# <a name="set-up-direct-sign-in-using-azure-active-directory-b2c"></a>Entrada direta usando o Microsoft Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Durante a configuração de entrada para seu aplicativo usando o Azure Active Directory (AD) B2C, você pode preencher previamente o nome de usuário ou logon direto em um provedor de identidade social específica, como Facebook, LinkedIn ou uma conta da Microsoft.

## <a name="prepopulate-the-sign-in-name"></a>Preencher previamente o nome de usuário

Durante um percurso do usuário de entrada, um aplicativo de terceira parte confiável pode ser direcionado a um nome de usuário ou domínio específico. Ao direcionar a um usuário, um aplicativo pode especificar, na solicitação de autorização, o `login_hint` parâmetro com o nome de logon do usuário de consulta. O Azure Active Directory B2C preenche automaticamente o nome de usuário, enquanto o usuário só precisa fornecer a senha.

![Página de entrada de entrada com login_hint parâmetro de consulta realçado na URL](./media/direct-signin/login-hint.png)

O usuário é capaz de alterar o valor na caixa de entrada.

::: zone pivot="b2c-custom-policy"

Para dar suporte ao parâmetro de dica de logon, substitua o `SelfAsserted-LocalAccountSignin-Email` perfil técnico. Na `<InputClaims>` seção, defina o valor padrão da declaração signInName a `{OIDC:LoginHint}`. O `{OIDC:LoginHint}` variável contém o valor da `login_hint` parâmetro. O Azure Active Directory B2C lê o valor da declaração signInName e preenche a caixa de texto signInName.

```xml
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
      <InputClaims>
        <!-- Add the login hint value to the sign-in names claim type -->
        <InputClaim ClaimTypeReferenceId="signInName" DefaultValue="{OIDC:LoginHint}" />
      </InputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

::: zone-end

## <a name="redirect-sign-in-to-a-social-provider"></a>Redirecionar a entrada para um provedor social

Se você configurou o percurso de entrada para seu aplicativo para incluir contas sociais, como Facebook, LinkedIn ou do Google, você pode especificar o `domain_hint` parâmetro. Esse parâmetro de consulta fornece uma dica para o Azure AD B2C sobre o provedor de identidade social que deve ser usado para entrar. Por exemplo, se o aplicativo especifica `domain_hint=facebook.com`, a entrada vai diretamente para a página de logon do Facebook.

![Página de entrada de entrada com domain_hint parâmetro de consulta realçado na URL](./media/direct-signin/domain-hint.png)

::: zone pivot="b2c-user-flow"

O parâmetro de cadeia de caracteres de consulta de dica de domínio pode ser definido como um dos seguintes domínios:

- amazon.com
- facebook.com
- github.com
- google.com
- linkedin.com
- microsoft.com
- qq.com
- twitter.com
- wechat.com
- weibo.com 
- Para o [OpenID Connect genérico](identity-provider-generic-openid-connect.md), consulte [dica de domínio](identity-provider-generic-openid-connect.md#response-mode).

::: zone-end

::: zone pivot="b2c-custom-policy"

Para dar suporte ao parâmetro de dica de domínio, você pode configurar o nome de domínio usando o `<Domain>domain name</Domain>` elemento XML de qualquer um `<ClaimsProvider>` .

```xml
<ClaimsProvider>
    <!-- Add the domain hint value to the claims provider -->
    <Domain>facebook.com</Domain>
    <DisplayName>Facebook</DisplayName>
    <TechnicalProfiles>
    ...
```

::: zone-end

