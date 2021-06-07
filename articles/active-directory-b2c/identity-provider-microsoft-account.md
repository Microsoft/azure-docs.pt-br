---
title: Configurar a inscrição e a entrada com uma conta Microsoft
titleSuffix: Azure AD B2C
description: Forneça inscrição e entrada aos consumidores com contas Microsoft em seus aplicativos usando o Azure Active Directory B2C.
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
ms.openlocfilehash: 12e8222ad59aae31baa7a549519c97550b711703
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104579936"
---
# <a name="set-up-sign-up-and-sign-in-with-a-microsoft-account-using-azure-active-directory-b2c"></a>Configurar a inscrição e entrada com a conta da Microsoft usando o Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-a-microsoft-account-application"></a>Criar um aplicativo de conta da Microsoft

Para habilitar a entrada de usuários com um conta Microsoft no Azure AD B2C (Azure Active Directory B2C), você precisa criar um aplicativo no [portal do Azure](https://portal.azure.com). Para obter mais informações, confira [Registrar um aplicativo na plataforma de identidade da Microsoft](../active-directory/develop/quickstart-register-app.md). Se ainda não tiver uma conta Microsoft, obtenha uma em [https://www.live.com/](https://www.live.com/).

1. Entre no [portal do Azure](https://portal.azure.com).
1. Verifique se você está usando o diretório que contém o locatário do Azure Active Directory escolhendo o filtro **Diretório + assinatura** no menu superior e escolhendo o diretório que contém o locatário do Azure Active Directory.
1. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure e pesquise e selecione **Registros de aplicativo**.
1. Selecione **Novo registro**.
1. Insira um **Nome** para seu aplicativo. Por exemplo, *MSAapp1*.
1. Em **Tipos de conta com suporte**, selecione **Contas em qualquer diretório organizacional (Qualquer diretório do Azure AD – Multilocatário) e contas Microsoft pessoais (por exemplo, Skype, Xbox)** .

   Confira mais informações sobre as diferentes seleções de tipo de conta em [Início Rápido: registrar um aplicativo na plataforma de identidade da Microsoft](../active-directory/develop/quickstart-register-app.md).
1. Em **URI de Redirecionamento (opcional)** , selecione **Web** e insira `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`. Se você usa um [domínio personalizado](custom-domain.md), insira `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp`. Substitua `your-tenant-name` pelo nome do locatário e `your-domain-name` pelo nome do domínio personalizado.
1. Escolha **Registrar**
1. Registre a **ID do Aplicativo (do cliente)** mostrada na página Visão Geral do aplicativo. Você precisará da ID do cliente para configurar o provedor de identidade na próxima seção.
1. Escolha **Certificados e Segredos**
1. Clique em **Novo segredo do cliente**
1. Insira uma **Descrição** para o segredo, por exemplo *Senha do aplicativo 1* e clique em **Adicionar**.
1. Registre a senha do aplicativo mostrada na coluna **Valor**. Você precisará do segredo do cliente para configurar o provedor de identidade na próxima seção.

::: zone pivot="b2c-user-flow"

## <a name="configure-microsoft-as-an-identity-provider"></a>Configurar a Microsoft como provedor de identidade

1. Entre no [portal do Azure](https://portal.azure.com/) como administrador global do locatário Azure AD B2C.
1. Verifique se você está usando o diretório que contém o locatário do Azure AD B2C selecionando o filtro **Diretório + assinatura** no menu superior e escolhendo o diretório que contém o locatário.
1. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
1. Selecione **Provedores de identidade** e depois **Conta Microsoft**.
1. Insira um **Nome**. Por exemplo, *MSA*.
1. Para a **ID do cliente**, insira a ID (do cliente) do aplicativo do Azure AD criado anteriormente.
1. Para o **Segredo do cliente**, insira o segredo que você registrou.
1. Clique em **Salvar**.

## <a name="add-microsoft-identity-provider-to-a-user-flow"></a>Adicionar o provedor de identidade da Microsoft a um fluxo de usuário 

Neste ponto, o provedor de identidade da Microsoft foi configurado, mas ainda não está disponível em nenhuma das páginas de entrada. Para adicionar o provedor de identidade da Microsoft a um fluxo de usuário:

1. No locatário do Azure AD B2C, selecione **Fluxos dos usuários**.
1. Clique no fluxo do usuário para o qual deseja adicionar o provedor de identidade da Microsoft.
1. Em **Provedores de identidade social**, selecione **Conta Microsoft**.
1. Selecione **Salvar**.
1. Para testar a política, selecione **Executar fluxo de usuário**.
1. Para **Aplicativo**, selecione o aplicativo Web denominado *testapp1* registrado anteriormente. A **URL de resposta** deve mostrar `https://jwt.ms`.
1. Selecione o botão **Executar fluxo de usuário**.
1. Na página de inscrição ou de entrada, selecione **Microsoft** para entrar com a conta Microsoft.

Se o processo de entrada for bem-sucedido, seu navegador será redirecionado para `https://jwt.ms`, que exibe o conteúdo do token retornado pelo Azure AD B2C.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="configuring-optional-claims"></a>Como configurar as declarações opcionais

Se quiser obter as declarações `family_name` e `given_name` do Azure Active Directory, você poderá configurar declarações opcionais para seu aplicativo na interface do usuário do portal do Azure ou no manifesto do aplicativo. Para obter mais informações, veja [Como fornecer declarações opcionais ao aplicativo Azure Active Directory (visualização pública)](../active-directory/develop/active-directory-optional-claims.md).

1. Entre no [portal do Azure](https://portal.azure.com). Pesquise **Azure Active Directory** e selecione-o.
1. Na seção **Gerenciar**, escolha **Registros de aplicativo**.
1. Escolha o aplicativo para o qual você deseja configurar declarações opcionais na lista.
1. Na seção **Gerenciar**, escolha **Configuração de token (versão preliminar)** .
1. Escolha **Adicionar declaração opcional**.
1. Escolha o tipo de token que você deseja configurar.
1. Escolha as declarações opcionais a serem adicionadas.
1. Clique em **Adicionar**.

## <a name="create-a-policy-key"></a>Criar uma chave de política

Agora que você criou o aplicativo em seu locatário do Azure Active Directory, é preciso armazenar o segredo do cliente do aplicativo em seu locatário Azure AD B2C.

1. Entre no [portal do Azure](https://portal.azure.com/).
1. Verifique se você está usando o diretório que contém seu locatário do Azure AD B2C. Escolha o filtro **Diretório + assinatura** no menu superior e escolha o diretório que contém o locatário.
1. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.
1. Na página de Visão Geral, selecione **Estrutura de Experiência de Identidade**.
1. Selecione **Chaves de Política** e, em seguida, escolha **Adicionar**.
1. Para **Opções**, escolha `Manual`.
1. Insira um **Nome** para a chave de política. Por exemplo, `MSASecret`. O prefixo `B2C_1A_` será adicionado automaticamente ao nome da chave.
1. Em **Segredo**, insira o segredo do cliente que você registrou na seção anterior.
1. Para **Uso de chave**, selecione `Signature`.
1. Clique em **Criar**.

## <a name="configure-microsoft-as-an-identity-provider"></a>Configurar a Microsoft como provedor de identidade

Para permitir que os usuários entrem usando uma conta Microsoft, defina a conta como um provedor de declarações com o qual o Azure AD B2C pode se comunicar por meio de um ponto de extremidade. O ponto de extremidade fornece um conjunto de declarações que são usadas pelo Azure AD B2C para verificar se um usuário específico foi autenticado.

Você pode definir o Azure AD como um provedor de declarações, adicionando o elemento **ClaimsProvider** no arquivo de extensão de sua política.

1. Abra o arquivo de política *TrustFrameworkExtensions.xml*.
1. Localize o elemento **ClaimsProviders**. Se ele não existir, adicione-o sob o elemento raiz.
1. Adicione um novo **ClaimsProvider** da seguinte maneira:

    ```xml
    <ClaimsProvider>
      <Domain>live.com</Domain>
      <DisplayName>Microsoft Account</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="MSA-MicrosoftAccount-OpenIdConnect">
          <DisplayName>Microsoft Account</DisplayName>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <Item Key="ProviderName">https://login.live.com</Item>
            <Item Key="METADATA">https://login.live.com/.well-known/openid-configuration</Item>
            <Item Key="response_types">code</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="scope">openid profile email</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="client_id">Your Microsoft application client ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_MSASecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="oid" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
            <OutputClaim ClaimTypeReferenceId="email" />
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

1. Substitua o valor de **client_id** pela *ID do aplicativo (cliente)*  do aplicativo Azure Active Directory que você registrou anteriormente.
1. Salve o arquivo.

Até este ponto, você configurou sua política para que o Azure Active Directory B2C saiba como se comunicar com seu aplicativo com conta Microsoft no Azure Active Directory.

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="MicrosoftAccountExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="MicrosoftAccountExchange" TechnicalProfileReferenceId="MSA-MicrosoftAccount-OpenIdConnect" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>Testar sua política personalizada

1. Selecione a política de terceira parte confiável, por exemplo, `B2C_1A_signup_signin`.
1. Para **Aplicativo**, selecione um aplicativo Web que você [registrou anteriormente](troubleshoot-custom-policies.md#troubleshoot-the-runtime). A **URL de resposta** deve mostrar `https://jwt.ms`.
1. Selecione o botão **Executar agora**.
1. Na página de inscrição ou de entrada, selecione **Microsoft** para entrar com a conta Microsoft.

Se o processo de entrada for bem-sucedido, seu navegador será redirecionado para `https://jwt.ms`, que exibe o conteúdo do token retornado pelo Azure AD B2C.

::: zone-end
