---
title: Passar um token de acesso do provedor de identidade para seu aplicativo
titleSuffix: Azure AD B2C
description: Saiba como passar um token de acesso para provedores de identidade OAuth 2,0 como uma declaração em um fluxo de usuário no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/15/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: a99d41f5f9fc9538aaf563bd3ae56075d269c94a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97584639"
---
# <a name="pass-an-identity-provider-access-token-to-your-application-in-azure-active-directory-b2c"></a>Passe um token de acesso do provedor de identidade para seu aplicativo no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Um [fluxo de usuário](user-flow-overview.md) no Azure Active Directory B2C (Azure ad B2C) fornece aos usuários de seu aplicativo uma oportunidade de se inscrever ou entrar com um provedor de identidade. Quando a jornada é iniciada, o Azure AD B2C recebe um [token de acesso](tokens-overview.md) do provedor de identidade. O Azure Active Directory B2C usa esse token para recuperar informações sobre o usuário. Habilitar uma declaração no seu fluxo de usuário para passar o token por meio para os aplicativos que você se registrar no Azure AD B2C.

::: zone pivot="b2c-user-flow"

Azure AD B2C dá suporte à passagem do token de acesso dos provedores de identidade [OAuth 2,0](add-identity-provider.md) , que incluem o [Facebook](identity-provider-facebook.md) e o [Google](identity-provider-google.md). Para todos os outros provedores de identidade, a declaração é retornada em branco.

::: zone-end

::: zone pivot="b2c-custom-policy"

O Azure AD B2C é compatível com passagem do token de acesso dos provedores de identidade [OAuth 2.0](authorization-code-flow.md) e [OpenID Connect](openid-connect.md). Para todos os outros provedores de identidade, a declaração é retornada em branco.

::: zone-end

O diagrama a seguir mostra como um token do provedor de identidade retorna para seu aplicativo: 

![Fluxo de passagem do provedor de identidade](./media/idp-pass-through-user-flow/identity-provider-pass-through-flow.png)

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

::: zone pivot="b2c-user-flow"

## <a name="enable-the-claim"></a>Habilitar a declaração

1. Entre no [portal do Azure](https://portal.azure.com/) como administrador global do locatário Azure AD B2C.
2. Verifique se você está usando o diretório que contém seu locatário do Azure AD B2C. Escolha o filtro **Diretório + assinatura** no menu superior e escolha o diretório que contém o locatário.
3. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
4. Selecione **fluxos de usuário (políticas)** e, em seguida, selecione seu fluxo de usuário. Por exemplo, **B2C_1_signupsignin1**.
5. Selecione **Declarações do aplicativo**.
6. Habilite a declaração de **token de acesso do provedor de identidade** .

    ![Habilitar a declaração de token de acesso do provedor de identidade](./media/idp-pass-through-user-flow/identity-provider-pass-through-app-claim.png)

7. Clique em **Salvar** para salvar o fluxo de usuário.

## <a name="test-the-user-flow"></a>Testar o fluxo de usuário

Ao testar seus aplicativos no Azure AD B2C, pode ser útil ter o token do Azure AD B2C retornado ao `https://jwt.ms` para examinar as declarações.

1. Na página de visão geral do fluxo de usuário, selecione **Executar o fluxo de usuário**.
2. Para **Aplicativo**, selecione seu aplicativo que você registrou anteriormente. Para ver o token no exemplo a seguir, a **URL de resposta** deve mostrar `https://jwt.ms`.
3. Clique em **Executar o fluxo de usuário**, e em seguida, entre com suas credenciais de conta. Você deve ver o token de acesso do provedor de identidade na declaração **idp_access_token**.

    Você deverá ver algo semelhante ao texto a seguir:

    ![Token decodificado em jwt.ms com idp_access_token bloco realçado](./media/idp-pass-through-user-flow/identity-provider-pass-through-token.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="add-the-claim-elements"></a>Adicione os elementos de declaração

1. Abre seu arquivo *TrustframeworkExtensions.xml* e adicione o seguinte elemento **ClaimType** com um identificador de `identityProviderAccessToken` para o elemento **ClaimsSchema**:

    ```xml
    <BuildingBlocks>
      <ClaimsSchema>
        <ClaimType Id="identityProviderAccessToken">
          <DisplayName>Identity Provider Access Token</DisplayName>
          <DataType>string</DataType>
          <AdminHelpText>Stores the access token of the identity provider.</AdminHelpText>
        </ClaimType>
        ...
      </ClaimsSchema>
    </BuildingBlocks>
    ```

2. Adicione o elemento **OutputClaim** ao elemento **TechnicalProfile** para cada provedor de identidade do OAuth 2.0 que você gostaria de ter o token de acesso. O exemplo a seguir mostra o elemento adicionado ao perfil técnico do Facebook:

    ```xml
    <ClaimsProvider>
      <DisplayName>Facebook</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Facebook-OAUTH">
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="identityProviderAccessToken" PartnerClaimType="{oauth2:access_token}" />
          </OutputClaims>
          ...
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

3. Salve o arquivo *TrustFrameworkExtensions.xml*.
4. Abra seu arquivo de política de terceira parte confiável, como *Signuporsignin* e adicione o elemento **OutputClaim** para **TechnicalProfile**:

    ```xml
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
      <TechnicalProfile Id="PolicyProfile">
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="identityProviderAccessToken" PartnerClaimType="idp_access_token"/>
        </OutputClaims>
        ...
      </TechnicalProfile>
    </RelyingParty>
    ```

5. Salve o arquivo da política.

## <a name="test-your-policy"></a>Testar sua política

Ao testar seus aplicativos no Azure AD B2C, pode ser útil ter o token do Azure AD B2C retornado ao `https://jwt.ms` para poder examinar as declarações.

### <a name="upload-the-files"></a>Fazer upload dos arquivos

1. Entre no [portal do Azure](https://portal.azure.com/).
2. Verifique se você está usando o diretório que contém seu locatário de Azure AD B2C clicando no filtro **diretório + assinatura** no menu superior e escolhendo o diretório que contém seu locatário.
3. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.
4. Selecione **Estrutura de Experiência de Identidade**.
5. Na página de políticas personalizadas, clique em **Carregar Política**.
6. Habilite **Substituir a política se ela existir** e, em seguida, navegue até o arquivo *TrustFrameworkExtensions.xml* e selecione-o.
7. Escolha **Carregar**.
8. Repita as etapas 5 a 7 para o arquivo de terceira parte confiável, como *SignUpOrSignIn.xml*.

### <a name="run-the-policy"></a>Executar a política

1. Abra a política que você alterou. Por exemplo, *B2C_1A_signup_signin*.
2. Para **Aplicativo**, selecione seu aplicativo que você registrou anteriormente. Para ver o token no exemplo a seguir, a **URL de resposta** deve mostrar `https://jwt.ms`.
3. Selecione **Executar Agora**.

    Você deverá ver algo semelhante ao texto a seguir:

    ![Token decodificado em jwt.ms com idp_access_token bloco realçado](./media/idp-pass-through-user-flow/identity-provider-pass-through-token-custom.png)

::: zone-end

## <a name="next-steps"></a>Próximas etapas

Saiba mais na [visão geral de tokens de Azure ad B2C](tokens-overview.md).
