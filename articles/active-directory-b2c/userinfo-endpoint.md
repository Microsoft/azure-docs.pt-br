---
title: Ponto de extremidade de UserInfo | Microsoft Docs
description: Defina um ponto de extremidade UserInfo em uma política personalizada no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/09/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: c060a029b1cdbdd890ced96cab732966cb652de0
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102500573"
---
# <a name="userinfo-endpoint"></a>Ponto de extremidade UserInfo

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

O ponto de extremidade de UserInfo faz parte da especificação do OIDC ( [OpenID Connect Standard](https://openid.net/specs/openid-connect-core-1_0.html#UserInfo) ) e foi projetado para retornar declarações sobre o usuário autenticado. O ponto de extremidade de UserInfo é definido na política de terceira parte confiável usando o elemento de [ponto de extremidade](relyingparty.md#endpoints) .  

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]

## <a name="userinfo-endpoint-overview"></a>Visão geral do ponto de extremidade de UserInfo

As informações de usuário userjornada especificam:

- **Autorização**: o ponto de extremidade de UserInfo é protegido com um token de portador. Um token de acesso emitido é apresentado no cabeçalho Authorization para o ponto de extremidade UserInfo. A política especifica o perfil técnico que valida o token de entrada e extrai declarações, como o objectId do usuário. O objectId do usuário é usado para recuperar as declarações a serem retornadas na resposta da jornada do ponto de extremidade de UserInfo. 
- **Etapa de orquestração**: 
  - Uma etapa de orquestração é usada para coletar informações sobre o usuário. Com base nas declarações dentro do token de acesso de entrada, o percurso do usuário invoca um [perfil técnico Azure Active Directory](active-directory-technical-profile.md) para recuperar dados sobre o usuário, por exemplo, lendo o usuário pelo ObjectID. 
  - **Etapas de orquestração opcionais** -você pode adicionar mais etapas de orquestração, como um perfil técnico da API REST, para recuperar mais informações sobre o usuário. 
  - **Emissor de UserInfo** – especifica a lista de declarações que o ponto de extremidade de UserInfo retorna.

## <a name="create-a-userinfo-endpoint"></a>Criar um ponto de extremidade de UserInfo

### <a name="1-add-the-token-issuer-technical-profile"></a>1. Adicionar o perfil técnico do emissor do token

1. Abra o arquivo *TrustFrameworkExtensions.xml*.
1. Se ele ainda não existir, adicione um elemento Claimprovider e seus elementos filho como o primeiro elemento sob o elemento BuildingBlocks.
1. Adicione o seguinte provedor de declarações:

    ```xml
    <!-- 
    <ClaimsProviders> -->
      <ClaimsProvider>
        <DisplayName>Token Issuer</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="UserInfoIssuer">
            <DisplayName>JSON Issuer</DisplayName>
            <Protocol Name="None" />
            <OutputTokenFormat>JSON</OutputTokenFormat>
            <CryptographicKeys>
              <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
            </CryptographicKeys>
            <!-- The Below claims are what will be returned on the UserInfo Endpoint if in the Claims Bag-->
            <InputClaims>
              <InputClaim ClaimTypeReferenceId="objectId"/>
              <InputClaim ClaimTypeReferenceId="givenName"/>
              <InputClaim ClaimTypeReferenceId="surname"/>
              <InputClaim ClaimTypeReferenceId="displayName"/>
              <InputClaim ClaimTypeReferenceId="signInNames.emailAddress"/>
            </InputClaims>
          </TechnicalProfile>
          <TechnicalProfile Id="UserInfoAuthorization">
            <DisplayName>UserInfo authorization</DisplayName>
            <Protocol Name="None" />
            <InputTokenFormat>JWT</InputTokenFormat>
            <Metadata>
              <!-- Update the Issuer and Audience below -->
              <!-- Audience is optional, Issuer is required-->
              <Item Key="issuer">https://yourtenant.b2clogin.com/11111111-1111-1111-1111-111111111111/v2.0/</Item>
              <Item Key="audience">[ "22222222-2222-2222-2222-222222222222", "33333333-3333-3333-3333-333333333333" ]</Item>
              <Item Key="client_assertion_type">urn:ietf:params:oauth:client-assertion-type:jwt-bearer</Item>
            </Metadata>
            <CryptographicKeys>
              <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
            </CryptographicKeys>
            <OutputClaims>
              <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
              <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" PartnerClaimType="email"/>
              <!-- Optional claims to read from the access token. -->
              <!-- <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
                 <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
                 <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name"/> -->
            </OutputClaims>
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
    <!-- 
    </ClaimsProviders> -->
    ``` 

1. A seção InputClaims no perfil técnico do **UserInfoIssuer** especifica os atributos que você deseja retornar. O perfil técnico do UserInfoIssuer é chamado no final da jornada do usuário. 
1. O perfil técnico do **UserInfoAuthorization** valida a assinatura, o nome do emissor e o público do token e extrai a declaração do token de entrada. Altere os metadados a seguir para refletir seu ambiente:
    1. **emissor** -esse valor deve ser idêntico à `iss` declaração dentro da declaração de token de acesso. Tokens emitidos por Azure AD B2C usam um emissor no formato `https://yourtenant.b2clogin.com/your-tenant-id/v2.0/` . Saiba mais sobre a [personalização de token](configure-tokens.md).
    1. **IdTokenAudience** -deve ser idêntico à `aud` declaração dentro da declaração de token de acesso. Em Azure AD B2C a `aud` declaração é a ID do seu aplicativo de terceira parte confiável. Esse valor é uma coleção e dá suporte a vários valores usando um delimitador de vírgula.

        No token de acesso a seguir, o `iss` valor da declaração é `https://contoso.b2clogin.com/11111111-1111-1111-1111-111111111111/v2.0/` . O `aud` valor da declaração é `22222222-2222-2222-2222-222222222222` .

        ```json
        {
          "exp": 1605549468,
          "nbf": 1605545868,
          "ver": "1.0",
          "iss": "https://contoso.b2clogin.com/11111111-1111-1111-1111-111111111111/v2.0/",
          "sub": "44444444-4444-4444-4444-444444444444",
          "aud": "22222222-2222-2222-2222-222222222222",
          "acr": "b2c_1a_signup_signin",
          "nonce": "defaultNonce",
          "iat": 1605545868,
          "auth_time": 1605545868,
          "name": "John Smith",
          "given_name": "John",
          "family_name": "Smith",
          "tid": "11111111-1111-1111-1111-111111111111"
        }
        ```
    
1.  O elemento OutputClaims do perfil técnico **UserInfoAuthorization** especifica os atributos que você deseja ler do token de acesso. O **ClaimTypeReferenceId** é a referência a um tipo de declaração. O **PartnerClaimType** opcional é o nome do da declaração definida no token de acesso.



### <a name="2-add-the-userjourney-element"></a>2. Adicionar o elemento userjornada 

O elemento [UserJourney](userjourneys.md) define o caminho usado pelo usuário ao interagir com seu aplicativo. Adicione o elemento **UserJourneys** se ele não existir com o **UserJourney** identificado como `UserInfoJourney`:

```xml
<!-- 
<UserJourneys> -->
  <UserJourney Id="UserInfoJourney" DefaultCpimIssuerTechnicalProfileReferenceId="UserInfoIssuer">
    <Authorization>
      <AuthorizationTechnicalProfiles>
        <AuthorizationTechnicalProfile ReferenceId="UserInfoAuthorization" />
      </AuthorizationTechnicalProfiles>
    </Authorization>
    <OrchestrationSteps >
      <OrchestrationStep Order="1" Type="ClaimsExchange">
        <Preconditions>
          <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
            <Value>objectId</Value>
            <Action>SkipThisOrchestrationStep</Action>
          </Precondition>
        </Preconditions>
        <ClaimsExchanges UserIdentity="false">
          <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
        </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="2" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="UserInfoIssuer" />
    </OrchestrationSteps>
  </UserJourney>
<!-- 
</UserJourneys> -->
```

### <a name="3-include-the-endpoint-to-the-relying-party-policy"></a>3. incluir o ponto de extremidade para a política de terceira parte confiável

Para incluir o ponto de extremidade de UserInfo no aplicativo de terceira parte confiável, adicione um elemento de [ponto de extremidade](relyingparty.md#endpoints) ao arquivo *SocialAndLocalAccounts/SignUpOrSignIn.xml* . 

```xml
<!--
<RelyingParty> -->
  <Endpoints>
    <Endpoint Id="UserInfo" UserJourneyReferenceId="UserInfoJourney" />
  </Endpoints>
<!-- 
</RelyingParty> -->
```

O elemento de terceira parte confiável concluído será o seguinte:

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<TrustFrameworkPolicy xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="http://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06" PolicySchemaVersion="0.3.0.0" TenantId="yourtenant.onmicrosoft.com" PolicyId="B2C_1A_signup_signin" PublicPolicyUri="http://yourtenant.onmicrosoft.com/B2C_1A_signup_signin">
  <BasePolicy>
    <TenantId>yourtenant.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>
  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <Endpoints>
      <Endpoint Id="UserInfo" UserJourneyReferenceId="UserInfoJourney" />
    </Endpoints>
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
</TrustFrameworkPolicy>
```

### <a name="4-upload-the-files"></a>4. carregar os arquivos

1. Entre no [portal do Azure](https://portal.azure.com/).
1. Verifique se você está usando o diretório que contém o locatário do Azure AD B2C selecionando o filtro **Diretório + assinatura** no menu superior e escolhendo o diretório que contém o locatário.
1. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.
1. Selecione **Estrutura de Experiência de Identidade**.
1. Na página **Políticas personalizadas**, escolha **Carregar política personalizada**.
1. Selecione **substituir a política personalizada se ela já existir** e, em seguida, procure e selecione o arquivo de *TrustframeworkExtensions.xml* .
1. Clique em **Carregar**.
1. Repita as etapas 5 a 7 para o arquivo de terceira parte confiável, como *SignUpOrSignIn.xml*.

## <a name="calling-the-userinfo-endpoint"></a>Chamando o ponto de extremidade de UserInfo

O ponto de extremidade de UserInfo usa a API de token de portador OAuth2 padrão, chamada usando o token de acesso recebido ao obter um token para seu aplicativo. Ele retorna uma resposta JSON que contém declarações sobre o usuário. O ponto de extremidade de UserInfo é hospedado em Azure AD B2C em:

```http
https://yourtenant.b2clogin.com/yourtenant.onmicrosoft.com/policy-name/openid/v2.0/userinfo
```

O/.well-known configurar o ponto de extremidade (documento de descoberta do OpenID Connect) lista o `userinfo_endpoint` campo. Você pode descobrir programaticamente o ponto de extremidade do UserInfo usando o ponto de extremidade de configuração do/.well-known em: 

```http
https://yourtenant.b2clogin.com/yourtenant.onmicrosoft.com/policy-name/v2.0/.well-known/openid-configuration 
```

### <a name="test-the-policy"></a>Testar a política

1. Em **políticas personalizadas**, selecione a política com a qual você integrou o ponto de extremidade UserInfo. Por exemplo, *B2C_1A_SignUpOrSignIn*.
1. Selecione **Executar Agora**. 
1. Em **Selecionar aplicativo**, selecione o aplicativo que você registrou anteriormente. Para **selecionar URL de resposta**, escolha `https://jwt.ms` . Para obter mais informações, consulte [registrar um aplicativo Web no Azure Active Directory B2C](tutorial-register-applications.md).
1. Inscreva-se ou entre com um endereço de email ou uma conta social.
1. Copie o id_token em seu formato codificado do [https://jwt.ms](https://jwt.ms) site. Você pode usar isso para enviar uma solicitação GET para o ponto de extremidade do UserInfo e recuperar as informações do usuário.
1. Envie uma solicitação GET para o ponto de extremidade do UserInfo e recupere as informações do usuário.

```http
GET /yourtenant.onmicrosoft.com/b2c_1a_signup_signin/openid/v2.0/userinfo
Host: b2cninja.b2clogin.com
Authorization: Bearer <your access token>
```

Uma resposta bem-sucedida teria A seguinte aparência:

```json
{
    "objectId": "44444444-4444-4444-4444-444444444444",
    "givenName": "John",
    "surname": "Smith",
    "displayName": "John Smith",
    "signInNames.emailAddress": "john.s@contoso.com"
}
```

## <a name="next-steps"></a>Próximas etapas

- Você pode encontrar um exemplo de uma política de ponto de extremidade de UserInfo no [GitHub](https://github.com/azure-ad-b2c/samples/tree/master/policies/user-info-endpoint).

::: zone-end
