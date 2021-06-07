---
title: Configurar a inscrição e a entrada com uma conta do Google
titleSuffix: Azure AD B2C
description: Forneça a inscrição e entrada aos consumidores com contas do Google em seus aplicativos usando o Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/17/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 7d5786c4188db63efc3012e565071f8fd410b92f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104579953"
---
# <a name="set-up-sign-up-and-sign-in-with-a-google-account-using-azure-active-directory-b2c"></a>Configurar a inscrição e entrada com a conta do Google usando o Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]


## <a name="create-a-google-application"></a>Criar um aplicativo do Google

Para habilitar a entrada de usuários com um conta do Google no Azure AD B2C (Azure Active Directory B2C), você precisa criar um aplicativo no [Google Developers Console](https://console.developers.google.com/). Para obter mais informações, confira [Configurando o OAuth 2.0](https://support.google.com/googleapi/answer/6158849). Se ainda não tiver uma conta do Google, inscreva-se em [https://accounts.google.com/SignUp](https://accounts.google.com/SignUp).

1. Entre no [Console de Desenvolvedores do Google](https://console.developers.google.com/) com suas credenciais de conta do Google.
1. No canto superior esquerdo da página, selecione a lista de projetos e selecione **Novo Projeto**.
1. Insira um **Nome do Projeto** e selecione **Criar**.
1. Verifique se você está usando o novo projeto selecionando a lista suspensa projeto no canto superior esquerdo da tela. Selecione o projeto pelo nome e selecione **Abrir**.
1. Selecione a **Tela de consentimento do OAuth** no menu à esquerda, selecione **Externo** e escolha **Criar**.
Insira um **Nome** para seu aplicativo. Insira *b2clogin.com* na seção **Domínios autorizados** e selecione **Salvar**.
1. Selecione **Credenciais** no menu à esquerda e, em seguida, selecione **Criar Credenciais** > **ID do cliente Oauth**.
1. Em **Tipo de aplicativo**, selecione **Aplicativo Web**.
    1. Insira um **Nome** para seu aplicativo.
    1. Para as **Origens de JavaScript Autorizadas**, insira `https://your-tenant-name.b2clogin.com`. Se você usa um [domínio personalizado](custom-domain.md), insira `https://your-domain-name`.
    1. Para os **URIs de Redirecionamento Autorizados**, insira `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`. Se você usa um [domínio personalizado](custom-domain.md), insira `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp`. Substitua `your-domain-name` pelo domínio personalizado e `your-tenant-name` pelo nome do locatário. Use todas as letras minúsculas, ao inserir o nome do locatário, mesmo se o locatário estiver definido com letras maiúsculas no Azure AD B2C.
1. Clique em **Criar**.
1. Copie os valores de **ID do cliente** e **Segredo do cliente**. Você precisará de ambos para configurar o Google como um provedor de identidade no seu locatário. **Segredo do cliente** é uma credencial de segurança importante.

::: zone pivot="b2c-user-flow"

## <a name="configure-google-as-an-identity-provider"></a>Configurar o Google como um provedor de identidade

1. Entre no [portal do Azure](https://portal.azure.com/) como administrador global do locatário Azure AD B2C.
1. Verifique se você está usando o diretório que contém o locatário do Azure AD B2C selecionando o filtro **Diretório + assinatura** no menu superior e escolhendo o diretório que contém o locatário.
1. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
1. Selecione **Provedores de identidade** e escolha **Google**.
1. Insira um **Nome**. Por exemplo, *Google*.
1. Para **ID do Cliente**, insira a ID do Cliente do aplicativo Google criado anteriormente.
1. Para o **Segredo do cliente**, insira o Segredo do Cliente que você registrou.
1. Selecione **Salvar**.

## <a name="add-google-identity-provider-to-a-user-flow"></a>Adicionar o provedor de identidade do Google a um fluxo de usuário 

Neste ponto, o provedor de identidade do Google foi configurado, mas ainda não está disponível em nenhuma das páginas de entrada. Para adicionar o provedor de identidade do Google a um fluxo de usuário:


1. No locatário do Azure AD B2C, selecione **Fluxos dos usuários**.
1. Clique no fluxo de usuário para o qual deseja adicionar o provedor de identidade do Google.
1. Em **Provedores de identidade social**, selecione **Google**.
1. Selecione **Salvar**.
1. Para testar a política, selecione **Executar fluxo de usuário**.
1. Para **Aplicativo**, selecione o aplicativo Web denominado *testapp1* registrado anteriormente. A **URL de resposta** deve mostrar `https://jwt.ms`.
1. Selecione o botão **Executar fluxo de usuário**.
1. Na página de inscrição ou de entrada, selecione **Google** para entrar com a conta do Google.

Se o processo de entrada for bem-sucedido, seu navegador será redirecionado para `https://jwt.ms`, que exibe o conteúdo do token retornado pelo Azure AD B2C.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>Criar uma chave de política

Você precisa armazenar o segredo do cliente que registrou anteriormente no seu locatário do Azure AD B2C.

1. Entre no [portal do Azure](https://portal.azure.com/).
2. Verifique se você está usando o diretório que contém seu locatário do Azure AD B2C. Escolha o filtro **Diretório + assinatura** no menu superior e escolha o diretório que contém o locatário.
3. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.
4. Na página de Visão Geral, selecione **Estrutura de Experiência de Identidade**.
5. Selecione **Chaves de Política** e, em seguida, escolha **Adicionar**.
6. Para **Opções**, escolha `Manual`.
7. Insira um **Nome** para a chave de política. Por exemplo, `GoogleSecret`. O prefixo `B2C_1A_` será adicionado automaticamente ao nome da chave.
8. Em **Segredo**, insira o segredo do cliente que você registrou anteriormente.
9. Para **Uso de chave**, selecione `Signature`.
10. Clique em **Criar**.

## <a name="configure-google-as-an-identity-provider"></a>Configurar o Google como um provedor de identidade

Para permitir que os usuários entrem usando uma conta do Google, defina a conta como um provedor de declarações com o qual o Azure AD B2C pode se comunicar por meio de um ponto de extremidade. O ponto de extremidade fornece um conjunto de declarações que são usadas pelo Azure AD B2C para verificar se um usuário específico foi autenticado.

Você pode definir uma conta do Google como um provedor de declarações, adicionando-o ao elemento **ClaimsProviders** no arquivo de extensão da política.

1. Abra *TrustFrameworkExtensions.xml*.
2. Localize o elemento **ClaimsProviders**. Se ele não existir, adicione-o sob o elemento raiz.
3. Adicione um novo **ClaimsProvider** da seguinte maneira:

    ```xml
    <ClaimsProvider>
      <Domain>google.com</Domain>
      <DisplayName>Google</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Google-OAuth2">
          <DisplayName>Google</DisplayName>
          <Protocol Name="OAuth2" />
          <Metadata>
            <Item Key="ProviderName">google</Item>
            <Item Key="authorization_endpoint">https://accounts.google.com/o/oauth2/auth</Item>
            <Item Key="AccessTokenEndpoint">https://accounts.google.com/o/oauth2/token</Item>
            <Item Key="ClaimsEndpoint">https://www.googleapis.com/oauth2/v1/userinfo</Item>
            <Item Key="scope">email profile</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="client_id">Your Google application ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_GoogleSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="id" />
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="google.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId" />
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. Defina **client_id** para a ID do aplicativo de registro de aplicativo.
5. Salve o arquivo.

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="GoogleExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAuth2" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>Testar sua política personalizada

1. Selecione a política de terceira parte confiável, por exemplo, `B2C_1A_signup_signin`.
1. Para **Aplicativo**, selecione um aplicativo Web que você [registrou anteriormente](troubleshoot-custom-policies.md#troubleshoot-the-runtime). A **URL de resposta** deve mostrar `https://jwt.ms`.
1. Selecione o botão **Executar agora**.
1. Na página de inscrição ou de entrada, selecione **Google** para entrar com a conta do Google.

Se o processo de entrada for bem-sucedido, seu navegador será redirecionado para `https://jwt.ms`, que exibe o conteúdo do token retornado pelo Azure AD B2C.

::: zone-end

## <a name="next-steps"></a>Próximas etapas

Saiba como [passar um token do Google para seu aplicativo](idp-pass-through-user-flow.md).