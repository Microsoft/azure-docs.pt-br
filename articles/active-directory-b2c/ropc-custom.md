---
title: Configurar o fluxo de credenciais de senha de proprietário do recurso no Azure Active Directory B2C | Microsoft Docs
description: Aprenda a configurar o fluxo de credenciais de senha de proprietário do recurso no Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: d01e8ce894bfb1ece3555eddc714d2d3a80e44b5
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/17/2019
ms.locfileid: "67164848"
---
# <a name="configure-the-resource-owner-password-credentials-flow-in-azure-active-directory-b2c-using-a-custom-policy"></a>Configurar o fluxo de credenciais de senha de proprietário do recurso no Azure Active Directory B2C usando uma política personalizada

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

No Azure Active Directory (Microsoft Azure Active Directory) B2C, o fluxo de credenciais (ROPC) de senha de proprietário do recurso é um fluxo de autenticação padrão do OAuth. Nesse fluxo, um aplicativo, também conhecido como a terceira parte confiável, troca de credenciais válidas para tokens. As credenciais incluem um ID de usuário e senha. Os tokens retornados são um ID de token, um token de acesso e um token de atualização.

Há suporte para as seguintes opções no fluxo ROPC:

- **Native Client** - Interação do usuário durante a autenticação ocorre quando o código é executado em um dispositivo do lado do usuário.
- **Fluxo de cliente público** - Somente credenciais de usuário, coletadas por um aplicativo, são enviadas na chamada de API. As credenciais do aplicativo não são enviadas.
- **Adicionar novas declarações** -O conteúdo do token de ID pode ser alterado para adicionar novas declarações.

Não há suporte para os fluxos a seguir:

- **Servidor-para-servidor** - O sistema de proteção de identidade precisa de um endereço IP confiável coletado pelo chamador (o cliente nativo) como parte da interação. Em uma chamada de API do lado do servidor, somente o endereço IP do servidor é usado. Se um número excessivo de entradas falharem, o sistema de proteção de identidade pode considerar um endereço IP repetido como um invasor.
- **Aplicativo de página única** - Um aplicativo de front-end é principalmente escrito em JavaScript. Frequentemente, o aplicativo é escrito utilizando uma estrutura como AngularJS, Ember.js ou Durandal.
- **Fluxo de cliente confidencial**: A ID do cliente do aplicativo é validada, mas o segredo do aplicativo não é validado.

## <a name="prerequisites"></a>Pré-requisitos

Conclua as etapas em [Introdução às políticas personalizadas no Azure Active Directory B2C](active-directory-b2c-get-started-custom.md).

## <a name="register-an-application"></a>Registrar um aplicativo

1. Entre no [Portal do Azure](https://portal.azure.com/).
2. Verifique se você está usando o diretório que contém o locatário do Azure AD B2C clicando no **filtro Diretório e assinatura** no menu superior e escolhendo o diretório que contém seu locatário.
3. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.
4. Selecione **Aplicativos** e, em seguida, selecione **Adicionar**.
5. Insira um nome para o aplicativo, como *ROPC_Auth_app*.
6. Selecione **Não** para **Aplicativo Web/API da Web** e então selecione **Sim** para **Cliente nativo**.
7. Deixe todos os outros valores como estão e então selecione **Criar**.
8. Selecione o novo aplicativo e anote a ID do aplicativo para usar depois.

##  <a name="create-a-resource-owner-policy"></a>Criar uma política de proprietário de recurso

1. Abra o arquivo *TrustFrameworkExtensions.xml*.
2. Se ele ainda não existir, adicione um elemento **ClaimsSchema** e seus elementos filho como o primeiro elemento sob o elemento **BuildingBlocks**:

    ```XML
    <ClaimsSchema>
      <ClaimType Id="logonIdentifier">
        <DisplayName>User name or email address that the user can use to sign in</DisplayName>
        <DataType>string</DataType>
      </ClaimType>
      <ClaimType Id="resource">
        <DisplayName>The resource parameter passes to the ROPC endpoint</DisplayName>
        <DataType>string</DataType>
      </ClaimType>
      <ClaimType Id="refreshTokenIssuedOnDateTime">
        <DisplayName>An internal parameter used to determine whether the user should be permitted to authenticate again using their existing refresh token.</DisplayName>
        <DataType>string</DataType>
      </ClaimType>
      <ClaimType Id="refreshTokensValidFromDateTime">
        <DisplayName>An internal parameter used to determine whether the user should be permitted to authenticate again using their existing refresh token.</DisplayName>
        <DataType>string</DataType>
      </ClaimType>
    </ClaimsSchema>
    ```

3. Após **ClaimsSchema**, adicione um elemento **ClaimsTransformations** e seus elementos filho para o elemento **BuildingBlocks**:

    ```XML
    <ClaimsTransformations>
      <ClaimsTransformation Id="CreateSubjectClaimFromObjectID" TransformationMethod="CreateStringClaim">
        <InputParameters>
          <InputParameter Id="value" DataType="string" Value="Not supported currently. Use oid claim." />
        </InputParameters>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="sub" TransformationClaimType="createdClaim" />
        </OutputClaims>
      </ClaimsTransformation>
    
      <ClaimsTransformation Id="AssertRefreshTokenIssuedLaterThanValidFromDate" TransformationMethod="AssertDateTimeIsGreaterThan">
        <InputClaims>
          <InputClaim ClaimTypeReferenceId="refreshTokenIssuedOnDateTime" TransformationClaimType="leftOperand" />
          <InputClaim ClaimTypeReferenceId="refreshTokensValidFromDateTime" TransformationClaimType="rightOperand" />
        </InputClaims>
        <InputParameters>
          <InputParameter Id="AssertIfEqualTo" DataType="boolean" Value="false" />
          <InputParameter Id="AssertIfRightOperandIsNotPresent" DataType="boolean" Value="true" />
        </InputParameters>
      </ClaimsTransformation>
    </ClaimsTransformations>
    ```

4. Localize o elemento **ClaimsProvider** que tem um **DisplayName** de `Local Account SignIn` e adicione o seguinte perfil técnico:

    ```XML
    <TechnicalProfile Id="ResourceOwnerPasswordCredentials-OAUTH2">
      <DisplayName>Local Account SignIn</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <Metadata>
        <Item Key="UserMessageIfClaimsPrincipalDoesNotExist">We can't seem to find your account</Item>
        <Item Key="UserMessageIfInvalidPassword">Your password is incorrect</Item>
        <Item Key="UserMessageIfOldPasswordUsed">Looks like you used an old password</Item>
        <Item Key="DiscoverMetadataByTokenIssuer">true</Item>
        <Item Key="ValidTokenIssuerPrefixes">https://sts.windows.net/</Item>
        <Item Key="METADATA">https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration</Item>
        <Item Key="authorization_endpoint">https://login.microsoftonline.com/{tenant}/oauth2/token</Item>
        <Item Key="response_types">id_token</Item>
        <Item Key="response_mode">query</Item>
        <Item Key="scope">email openid</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="logonIdentifier" PartnerClaimType="username" Required="true" DefaultValue="{OIDC:Username}"/>
        <InputClaim ClaimTypeReferenceId="password" Required="true" DefaultValue="{OIDC:Password}" />
        <InputClaim ClaimTypeReferenceId="grant_type" DefaultValue="password" />
        <InputClaim ClaimTypeReferenceId="scope" DefaultValue="openid" />
        <InputClaim ClaimTypeReferenceId="nca" PartnerClaimType="nca" DefaultValue="1" />
        <InputClaim ClaimTypeReferenceId="client_id" DefaultValue="00000000-0000-0000-0000-000000000000" />
        <InputClaim ClaimTypeReferenceId="resource_id" PartnerClaimType="resource" DefaultValue="00000000-0000-0000-0000-000000000000" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="oid" />
        <OutputClaim ClaimTypeReferenceId="userPrincipalName" PartnerClaimType="upn" />
      </OutputClaims>
      <OutputClaimsTransformations>
        <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromObjectID" />
      </OutputClaimsTransformations>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>
    ```

    Substitua os **DefaultValue** dos **client_id** com a ID do aplicativo do aplicativo ProxyIdentityExperienceFramework que você criou no tutorial de pré-requisito. Em seguida, substitua **DefaultValue** dos **resource_id** com a ID do aplicativo do aplicativo IdentityExperienceFramework que você também criou no tutorial de pré-requisito.  

5. Adicionar os seguintes elementos **ClaimsProvider** com seus perfis técnicos para o elemento **ClaimsProviders**:

    ```XML
    <ClaimsProvider>
      <DisplayName>Azure Active Directory</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="AAD-UserReadUsingObjectId-CheckRefreshTokenDate">
          <Metadata>
            <Item Key="Operation">Read</Item>
            <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
          </Metadata>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
          </InputClaims>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="objectId" />
            <OutputClaim ClaimTypeReferenceId="refreshTokensValidFromDateTime" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="AssertRefreshTokenIssuedLaterThanValidFromDate" />
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromObjectID" />
          </OutputClaimsTransformations>
          <IncludeTechnicalProfile ReferenceId="AAD-Common" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>

    <ClaimsProvider>
      <DisplayName>Session Management</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="SM-RefreshTokenReadAndSetup">
          <DisplayName>Trustframework Policy Engine Refresh Token Setup Technical Profile</DisplayName>
          <Protocol Name="None" />
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="objectId" />
            <OutputClaim ClaimTypeReferenceId="refreshTokenIssuedOnDateTime" />
          </OutputClaims>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>

    <ClaimsProvider>
      <DisplayName>Token Issuer</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="JwtIssuer">
          <Metadata>
            <!-- Point to the redeem refresh token user journey-->
            <Item Key="RefreshTokenUserJourneyId">ResourceOwnerPasswordCredentials-RedeemRefreshToken</Item>
          </Metadata>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

6. Adicionar um elemento **UserJourneys** e seus elementos filho para o elemento **TrustFrameworkPolicy**:

    ```XML
    <UserJourney Id="ResourceOwnerPasswordCredentials">
      <PreserveOriginalAssertion>false</PreserveOriginalAssertion>
      <OrchestrationSteps>
        <OrchestrationStep Order="1" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="ResourceOwnerFlow" TechnicalProfileReferenceId="ResourceOwnerPasswordCredentials-OAUTH2" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="2" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="3" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
      </OrchestrationSteps>
    </UserJourney>
    <UserJourney Id="ResourceOwnerPasswordCredentials-RedeemRefreshToken">
      <PreserveOriginalAssertion>false</PreserveOriginalAssertion>
      <OrchestrationSteps>
        <OrchestrationStep Order="1" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="RefreshTokenSetupExchange" TechnicalProfileReferenceId="SM-RefreshTokenReadAndSetup" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="2" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="CheckRefreshTokenDateFromAadExchange" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId-CheckRefreshTokenDate" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="3" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
      </OrchestrationSteps>
    </UserJourney>
    ```

7. Na página **Políticas Personalizadas** em seu locatário do Azure AD B2C, selecione **Carregar Política**.
8. Habilite **Substitua a política se ela existir** e, em seguida, navegue até o arquivo *TrustFrameworkExtensions.xml* e selecione-o.
9. Clique em **Carregar**.

## <a name="create-a-relying-party-file"></a>Criar um arquivo de terceira parte confiável

Em seguida, atualize o arquivo de terceira parte confiável que iniciará o percurso do usuário que você criou:

1. Faça uma cópia do arquivo *SignUpOrSignIn.xml* no seu diretório de trabalho e, em seguida, renomeie ele como *ROPC_Auth.xml*.
2. Abra o novo arquivo e altere o valor do atributo **PolicyId** para **TrustFrameworkPolicy** com um valor exclusivo. O ID da política é o nome da sua política. Por exemplo, **B2C_1A_ROPC_Auth**.
3. Altere o valor do atributo **ReferenceId** no **DefaultUserJourney** para `ResourceOwnerPasswordCredentials`.
4. Altere o elemento **OutputClaims** para conter apenas as seguintes declarações:
    
    ```XML
    <OutputClaim ClaimTypeReferenceId="sub" />
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="displayName" DefaultValue="" />
    <OutputClaim ClaimTypeReferenceId="givenName" DefaultValue="" />
    <OutputClaim ClaimTypeReferenceId="surname" DefaultValue="" />
    ```

5. Na página **Políticas Personalizadas** em seu locatário do Azure AD B2C, selecione **Carregar Política**.
6. Habilitar **substituir a política caso ela exista**e, em seguida, navegue até e selecione o *ROPC_Auth.xml* arquivo.
7. Clique em **Carregar**.

## <a name="test-the-policy"></a>Testar a política

Use seu aplicativo favorito de desenvolvimento de API para gerar uma chamada de API e analise a resposta para depurar sua política. Construa uma chamada de exemplo com as seguintes informações no corpo da solicitação POST:

`https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/v2.0/token?p=B2C_1_ROPC_Auth`

- Substitua `your-tenant-name` pelo nome de seu locatário do Azure AD B2C.
- Substitua `B2C_1A_ROPC_Auth` com o nome completo da política de credenciais de senha do proprietário do recurso.

| Chave | Value |
| --- | ----- |
| username | `user-account` |
| password | `password1` |
| grant_type | password |
| scope | OpenID `application-id` offline_access |
| client_id | `application-id` |
| response_type | token id_token |

- Substitua `user-account` com o nome de uma conta de usuário no seu locatário.
- Substitua `password1` pela senha da conta de usuário.
- Substitua `application-id` como a ID do aplicativo no registro de aplicativo *ROPC_Auth_app*.
- *Offline_access* será opcional se você desejar receber um token de atualização.

A solicitação POST real tem a aparência do exemplo a seguir:

```HTTPS
POST /yourtenant.onmicrosoft.com/oauth2/v2.0/token?B2C_1_ROPC_Auth HTTP/1.1
Host: yourtenant.b2clogin.com
Content-Type: application/x-www-form-urlencoded

username=contosouser.outlook.com.ws&password=Passxword1&grant_type=password&scope=openid+bef22d56-552f-4a5b-b90a-1988a7d634ce+offline_access&client_id=bef22d56-552f-4a5b-b90a-1988a7d634ce&response_type=token+id_token
```

Uma resposta bem-sucedida com acesso offline se parece com o seguinte exemplo:

```JSON
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik9YQjNhdTNScWhUQWN6R0RWZDM5djNpTmlyTWhqN2wxMjIySnh6TmgwRlki...",
    "token_type": "Bearer",
    "expires_in": "3600",
    "refresh_token": "eyJraWQiOiJacW9pQlp2TW5pYVc2MUY0TnlfR3REVk1EVFBLbUJLb0FUcWQ1ZWFja1hBIiwidmVyIjoiMS4wIiwiemlwIjoiRGVmbGF0ZSIsInNlciI6Ij...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik9YQjNhdTNScWhUQWN6R0RWZDM5djNpTmlyTWhqN2wxMjIySnh6TmgwRlki..."
}
```

## <a name="redeem-a-refresh-token"></a>Resgatar um token de atualização

Construa uma chamada POST como esta. Use as informações na tabela a seguir como o corpo da solicitação:

`https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/v2.0/token?p=B2C_1_ROPC_Auth`

- Substitua `your-tenant-name` pelo nome de seu locatário do Azure AD B2C.
- Substitua `B2C_1A_ROPC_Auth` com o nome completo da política de credenciais de senha do proprietário do recurso.

| Chave | Value |
| --- | ----- |
| grant_type | refresh_token |
| response_type | id_token |
| client_id | `application-id` |
| resource | `application-id` |
| refresh_token | `refresh-token` |

- Substitua `application-id` como a ID do aplicativo no registro de aplicativo *ROPC_Auth_app*.
- Substitua `refresh-token` com o **refresh_token** que foi enviada de volta na resposta anterior.

Uma resposta bem-sucedida se parece com o seguinte exemplo:

```JSON
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ilg1ZVhrNHh5b2pORnVtMWtsMll0djhkbE5QNC1jNTdkTzZRR1RWQndhT...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ilg1ZVhrNHh5b2pORnVtMWtsMll0djhkbE5QNC1jNTdkTzZRR1RWQn...",
    "token_type": "Bearer",
    "not_before": 1533672990,
    "expires_in": 3600,
    "expires_on": 1533676590,
    "resource": "bef2222d56-552f-4a5b-b90a-1988a7d634c3",
    "id_token_expires_in": 3600,
    "profile_info": "eyJ2ZXIiOiIxLjAiLCJ0aWQiOiI1MTZmYzA2NS1mZjM2LTRiOTMtYWE1YS1kNmVlZGE3Y2JhYzgiLCJzdWIiOm51bGwsIm5hbWUiOiJEYXZpZE11IiwicHJlZmVycmVkX3VzZXJuYW1lIjpudWxsLCJpZHAiOiJMb2NhbEFjY291bnQifQ",
    "refresh_token": "eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMCIsInppcCI6IkRlZmxhdGUiLCJzZXIiOiIxLjAi...",
    "refresh_token_expires_in": 1209600
}
```

## <a name="use-a-native-sdk-or-app-auth"></a>Usar um SDK nativo ou a autenticação do aplicativo

Os padrões do Azure AD B2C atendem aos padrões OAuth 2.0 para credenciais de senha de proprietário de cliente público e deve ser compatível com a maioria dos SDKs clientes. Para obter informações mais recentes, consulte [nativo do SDK de aplicativo OAuth 2.0 e OpenID Connect implementando práticas recomendadas modernas](https://appauth.io/).

## <a name="next-steps"></a>Próximas etapas

- Veja um exemplo completo deste cenário no [pacote de início de política personalizado do Azure Active Directory B2C](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/source/aadb2c-ief-ropc).
- Saiba mais sobre os tokens que são usados pelo Azure Active Directory B2C na [referência de Token](active-directory-b2c-reference-tokens.md).
