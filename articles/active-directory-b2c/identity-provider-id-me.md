---
title: Configurar a inscrição e a entrada com uma conta do ID.me
titleSuffix: Azure AD B2C
description: Forneça inscrição e entrada para clientes com contas do ID.me em seus aplicativos usando Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/08/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 39896d88aeab4c592c74ded2d6edddfb46d2d203
ms.sourcegitcommit: f6193c2c6ce3b4db379c3f474fdbb40c6585553b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2021
ms.locfileid: "102448278"
---
# <a name="set-up-sign-up-and-sign-in-with-a-idme-account-using-azure-active-directory-b2c"></a>Configurar a inscrição e a entrada com uma conta do ID.me usando Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"


## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]


## <a name="create-an-idme-application"></a>Criar um aplicativo ID.me

Para habilitar a entrada para usuários com uma conta do ID.me no Azure Active Directory B2C (Azure AD B2C), você precisa criar um aplicativo em [recursos do ID.me Developer para API & SDK](https://developers.id.me/). Para obter mais informações, consulte [Guia de integração do OAuth](https://developers.id.me/documentation/oauth/overview/kyc). Se você ainda não tiver uma conta de desenvolvedor ID.me, poderá se inscrever em [https://developers.id.me/registration/new](https://developers.id.me/registration/new) .

1. Entre nos recursos do [desenvolvedor do ID.me para API & SDK](https://developers.id.me/) com suas credenciais de conta do ID.me.
1. Selecione **exibir meus aplicativos** e selecione **continuar**.
1. Selecione **Criar**
    1. Insira um **nome** e um **nome para exibição**.
    1. No **URI de redirecionamento**, insira `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` . Substitua `your-tenant-name` pelo nome do seu locatário. 
1. Clique em **Continuar**.
1. Copie os valores da **ID do cliente** e do **segredo do cliente**. Você precisará delas para adicionar o provedor de identidade ao locatário.

## <a name="create-a-policy-key"></a>Criar uma chave de política

Você precisa armazenar o segredo do cliente que registrou anteriormente no seu locatário do Azure AD B2C.

1. Entre no [portal do Azure](https://portal.azure.com/).
2. Verifique se você está usando o diretório que contém seu locatário do Azure AD B2C. Escolha o filtro **Diretório + assinatura** no menu superior e escolha o diretório que contém o locatário.
3. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.
4. Na página de Visão Geral, selecione **Estrutura de Experiência de Identidade**.
5. Selecione **Chaves de Política** e, em seguida, escolha **Adicionar**.
6. Para **Opções**, escolha `Manual`.
7. Insira um **Nome** para a chave de política. Por exemplo, `IdMeSecret`. O prefixo `B2C_1A_` será adicionado automaticamente ao nome da chave.
8. Em **Segredo**, insira o segredo do cliente que você registrou anteriormente.
9. Para **Uso de chave**, selecione `Signature`.
10. Clique em **Criar**.

## <a name="configure-idme-as-an-identity-provider"></a>Configurar o ID.me como um provedor de identidade

Para permitir que os usuários entrem usando uma conta do ID.me, você precisa definir a conta como um provedor de declarações com o qual Azure AD B2C pode se comunicar por meio de um ponto de extremidade. O ponto de extremidade fornece um conjunto de declarações que são usadas pelo Azure AD B2C para verificar se um usuário específico foi autenticado.

Você pode definir uma conta ID.me como um provedor de declarações adicionando-a ao elemento **ClaimsProviders** no arquivo de extensão da política.

1. Abra *TrustFrameworkExtensions.xml*.
2. Localize o elemento **ClaimsProviders**. Se ele não existir, adicione-o sob o elemento raiz.
3. Adicione um novo **ClaimsProvider** da seguinte maneira:

    ```xml
    <ClaimsProvider>
      <Domain>id.me</Domain>
      <DisplayName>ID.me</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="IdMe-OAuth2">
          <DisplayName>IdMe</DisplayName>
          <Protocol Name="OAuth2" />
          <Metadata>
            <Item Key="ProviderName">api.id.me</Item>
            <Item Key="authorization_endpoint">https://api.id.me/oauth/authorize</Item>
            <Item Key="AccessTokenEndpoint">https://api.id.me/oauth/token</Item>
            <Item Key="ClaimsEndpoint">https://api.id.me/api/public/v2/attributes.json</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="scope">openid alumni</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <!-- Update the Client ID below to the Application ID -->
            <Item Key="client_id">Your ID.me application ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_IdMeSecret"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="uuid" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="fname" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lname" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="me.id.com" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
            <OutputClaimsTransformation ReferenceId="CreateDisplayNameFromFirstNameAndLastName" />
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. Defina **client_id** para a ID do aplicativo de registro de aplicativo.
5. Salve o arquivo.

### <a name="add-the-claims-transformations"></a>Adicionar as transformações de declarações

Em seguida, você precisa de uma transformação declarações para criar a declaração displayName. Adicione a transformação de declarações a seguir ao `<ClaimsTransformations>` elemento no `<BuildingBlocks>` . 

```xml
 <ClaimsTransformations>
  <ClaimsTransformation Id="CreateDisplayNameFromFirstNameAndLastName" TransformationMethod="FormatStringMultipleClaims">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="inputClaim1" />
      <InputClaim ClaimTypeReferenceId="surName" TransformationClaimType="inputClaim2" />
    </InputClaims>
    <InputParameters>
      <InputParameter Id="stringFormat" DataType="string" Value="{0} {1}" />
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="displayName" TransformationClaimType="outputClaim" />
    </OutputClaims>
   </ClaimsTransformation>
</ClaimsTransformations>
```

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="IdMeExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="IdMeExchange" TechnicalProfileReferenceId="IdMe-OAuth2" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>Testar sua política personalizada

1. Selecione a política de terceira parte confiável, por exemplo `B2C_1A_signup_signin` .
1. Para **aplicativo**, selecione um aplicativo Web que você [registrou anteriormente](troubleshoot-custom-policies.md#troubleshoot-the-runtime). A **URL de resposta** deve mostrar `https://jwt.ms`.
1. Selecione o botão **executar agora** .
1. Na página inscrever-se ou entrar, selecione **ID.me** para entrar com a conta do ID.me.

Se o processo de entrada for bem-sucedido, seu navegador será redirecionado para `https://jwt.ms` , que exibe o conteúdo do token retornado por Azure ad B2C.

::: zone-end