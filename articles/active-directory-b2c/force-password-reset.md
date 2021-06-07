---
title: Configurar um fluxo de redefinição de senha forçada no Azure AD B2C
titleSuffix: Azure AD B2C
description: Saiba como configurar um fluxo de redefinição de senha forçado no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/03/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 0477153b466702bec0fa2d5221fee1e054d06314
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102033752"
---
# <a name="set-up-a-force-password-reset-flow-in-azure-active-directory-b2c"></a>Configurar um fluxo de redefinição de senha forçada no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Como administrador, você pode [redefinir a senha de um usuário](manage-users-portal.md#reset-a-users-password) se o usuário esquecer sua senha. Ou você gostaria de forçá-los a redefinir a senha. Neste artigo, você aprenderá a forçar uma redefinição de senha nesses cenários.

## <a name="overview"></a>Visão geral

Quando um administrador redefine a senha de um usuário por meio do portal do Azure, o valor do atributo [forceChangePasswordNextSignIn](user-profile-attributes.md#password-profile-property) é definido como `true` .

A [jornada de entrada e de inscrição](add-sign-up-and-sign-in-policy.md) verifica o valor desse atributo. Depois que o usuário concluir a entrada, se o atributo for definido como `true` , o usuário deverá redefinir sua senha. Em seguida, o valor do atributo é definido como back `false` .

![Forçar o fluxo de redefinição de senha](./media/force-password-reset/force-password-reset-flow.png)

O fluxo de redefinição de senha é aplicável a contas locais em Azure AD B2C que usam um [endereço de email](identity-provider-local.md#email-sign-in) ou [nome de usuário](identity-provider-local.md#username-sign-in) com uma senha para entrar.

### <a name="force-a-password-reset-after-90-days"></a>Forçar uma redefinição de senha após 90 dias

Como administrador, você pode definir a expiração da senha de um usuário para 90 dias, usando o [MS Graph](microsoft-graph-operations.md). Após 90 dias, o valor do atributo [forceChangePasswordNextSignIn](user-profile-attributes.md#password-profile-property) é definido automaticamente como `true` . Para obter mais informações sobre como definir a política de expiração de senha de um usuário, consulte [atributo de política de senha](user-profile-attributes.md#password-policy-attribute).

Depois que uma política de expiração de senha tiver sido definida, você também deverá configurar o fluxo de redefinição de senha forçada, conforme descrito neste artigo.  

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="configure-your-policy"></a>Configurar sua política

::: zone pivot="b2c-user-flow"

Para habilitar a configuração de **redefinição de senha forçada** em um fluxo de usuário de inscrição ou entrada:

1. Entre no [portal do Azure](https://portal.azure.com).
1. Selecione o ícone **Diretório + Assinatura** na barra de ferramentas do portal e selecione o diretório que contém o locatário do Azure AD B2C.
1. No portal do Azure, pesquise e selecione **Azure AD B2C**.
1. Escolha **Fluxos de usuário**.
1. Selecione a inscrição e a entrada ou o fluxo de usuário de entrada (do tipo **recomendado**) que você deseja personalizar.
1. No menu à esquerda em **configurações**, selecione **Propriedades**.
1. Em **complexidade da senha**, selecione **redefinição de senha forçada**.
1. Clique em **Salvar**.

### <a name="test-the-user-flow"></a>Testar o fluxo de usuário

1. Entre no [portal do Azure](https://portal.azure.com) como um administrador de usuário ou um administrador de senha. Para obter mais informações sobre as funções disponíveis, consulte [atribuindo funções de administrador no Azure Active Directory](../active-directory/roles/permissions-reference.md#all-roles).
1. Selecione o ícone **Diretório + Assinatura** na barra de ferramentas do portal e selecione o diretório que contém o locatário do Azure AD B2C.
1. No portal do Azure, pesquise e selecione **Azure AD B2C**.
1. Selecione **Usuários**. Pesquise e selecione o usuário que você usará para testar a redefinição de senha e, em seguida, selecione **Redefinir senha**.
1. No portal do Azure, pesquise e selecione **Azure AD B2C**.
1. Escolha **Fluxos de usuário**.
1. Selecione um fluxo de usuário de inscrição ou entrada (do tipo **recomendado**) que você deseja testar.
1. Selecione **Executar fluxo de usuário**.
1. Para **Aplicativo**, selecione o aplicativo Web denominado *webapp1* que você registrou anteriormente. A **URL de resposta** deve mostrar `https://jwt.ms`.
1. Selecione **Executar fluxo de usuário**.
1. Entre com a conta de usuário para a qual você redefiniu a senha.
1. Agora você deve alterar a senha para o usuário. Altere a senha e selecione **Continuar**. O token é retornado para `https://jwt.ms` e deve ser exibido para você.

::: zone-end

::: zone pivot="b2c-custom-policy"

1. Obtenha o exemplo de uma redefinição de senha forçada no [GitHub](https://github.com/azure-ad-b2c/samples/tree/master/policies/force-password-reset).
1. Em cada arquivo, substitua a cadeia de caracteres `yourtenant` pelo nome do seu locatário do Azure AD B2C. Por exemplo, se o nome do seu locatário B2C for *contosob2c*, todas as instâncias de `yourtenant.onmicrosoft.com` se tornarão `contosob2c.onmicrosoft.com`.
1. Carregue os arquivos de política na seguinte ordem: a política de extensão `TrustFrameworkExtensionsCustomForcePasswordReset.xml` e, em seguida, a política de terceira parte confiável `SignUpOrSigninCustomForcePasswordReset.xml` .

### <a name="test-the-policy"></a>Testar a política

1. Entre no [portal do Azure](https://portal.azure.com) como um administrador de usuário ou um administrador de senha. Para obter mais informações sobre as funções disponíveis, consulte [atribuindo funções de administrador no Azure Active Directory](../active-directory/roles/permissions-reference.md#all-roles).
1. Selecione o ícone **Diretório + Assinatura** na barra de ferramentas do portal e selecione o diretório que contém o locatário do Azure AD B2C.
1. No portal do Azure, pesquise e selecione **Azure AD B2C**.
1. Selecione **Usuários**. Pesquise e selecione o usuário que você usará para testar a redefinição de senha e, em seguida, selecione **Redefinir senha**.
1. No portal do Azure, pesquise e selecione **Azure AD B2C**.
1. Em **Políticas**, selecione **Identity Experience Framework**.
1. Selecione a `B2C_1A_signup_signin_Custom_ForcePasswordReset` política para abri-la. 
1. Para **aplicativo**, selecione um aplicativo Web que você [registrou anteriormente](troubleshoot-custom-policies.md#troubleshoot-the-runtime). A **URL de resposta** deve mostrar `https://jwt.ms`.
1. Selecione o botão **executar agora** .
1. Entre com a conta de usuário para a qual você redefiniu a senha.
1. Agora você deve alterar a senha para o usuário. Altere a senha e selecione **Continuar**. O token é retornado para `https://jwt.ms` e deve ser exibido para você.

::: zone-end

## <a name="next-steps"></a>Próximas etapas

Configure uma [redefinição de senha de autoatendimento](add-password-reset-policy.md).
