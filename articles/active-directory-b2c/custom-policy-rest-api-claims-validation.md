---
title: Trocas de declarações da API REST como validação
titleSuffix: Azure AD B2C
description: Uma explicação sobre como criar uma jornada de usuário Azure AD B2C que interage com os serviços RESTful.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 761bc4db7760ef5e84e3fc3c8a5deea5d4508f51
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94951920"
---
# <a name="walkthrough-integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-to-validate-user-input"></a>Walkthrough: integrar as trocas de declarações da API REST em sua jornada do usuário Azure AD B2C para validar a entrada do usuário

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

A IEF (estrutura de experiência de identidade) que o Azure Active Directory B2C (Azure AD B2C) permite que os desenvolvedores de identidade integrem uma interação com uma API RESTful em uma jornada do usuário.  No final deste passo a passo, você poderá criar uma jornada de usuário Azure AD B2C que interage com os [serviços RESTful](custom-policy-rest-api-intro.md) para validar a entrada do usuário.

Nesse cenário, adicionaremos a capacidade para os usuários inserirem um número de fidelidade na página de inscrição Azure AD B2C. Vamos validar se essa combinação de email e número de fidelidade está mapeada para um código promocional enviando esses dados a uma API REST. Se a API REST encontrar um código promocional para esse usuário, ela será retornada para Azure AD B2C. Por fim, o código promocional será inserido nas declarações de token para que o aplicativo consuma.

Você também pode criar a interação como uma etapa de orquestração. Isso é adequado quando a API REST não estará Validando dados na tela e sempre retornar declarações. Para obter mais informações, consulte [Passo a passo: Integrar as trocas de declarações da API REST no percurso do usuário do Azure AD B2C como uma etapa de orquestração](custom-policy-rest-api-claims-exchange.md).

## <a name="prerequisites"></a>Pré-requisitos

- Conclua as etapas em [Introdução às políticas personalizadas](custom-policy-get-started.md). Você deve ter uma política personalizada funcional para inscrição e conexão com contas locais.
- Saiba como [Integrar trocas de declarações da API REST em sua política personalizada no Azure AD B2C](custom-policy-rest-api-intro.md).

## <a name="prepare-a-rest-api-endpoint"></a>Preparar um ponto de extremidade da API REST

Para esta explicação, você deve ter uma API REST que valida se um endereço de email está registrado em seu sistema de back-end com uma ID de fidelidade. Se registrado, a API REST deve retornar um código de promoção de registro, que o cliente pode usar para comprar bens dentro de seu aplicativo. Caso contrário, a API REST deverá retornar uma mensagem de erro HTTP 409: "a ID de fidelidade ' {ID de fidelidade} ' não está associada ao endereço de email ' {email} '.".

O código JSON a seguir ilustra os dados que o Azure AD B2C enviará para o ponto de extremidade da API REST. 

```json
{
    "email": "User email address",
    "language": "Current UI language",
    "loyaltyId": "User loyalty ID"
}
```

Depois que a API REST validar os dados, ela deverá retornar um HTTP 200 (Ok), com os seguintes dados JSON:

```json
{
    "promoCode": "24534"
}
```

Se a validação falhar, a API REST deverá retornar um HTTP 409 (conflito), com o `userMessage` elemento JSON. O IEF espera a `userMessage` declaração que a API REST retorna. Essa declaração será apresentada como uma cadeia de caracteres para o usuário se a validação falhar.

```json
{
    "version": "1.0.1",
    "status": 409,
    "userMessage": "LoyaltyId ID '1234' is not associated with 'david@contoso.com' email address."
}
```

A configuração do ponto de extremidade da API REST está fora do escopo deste artigo. Criamos um exemplo do [Azure Functions](../azure-functions/functions-reference.md). Você pode acessar o código completo de função do Azure no [GitHub](https://github.com/azure-ad-b2c/rest-api/tree/master/source-code/azure-function).

## <a name="define-claims"></a>Definir declarações

Uma declaração fornece armazenamento temporário de dados durante uma execução de política do Azure AD B2C. Você pode definir declarações na seção de [esquema de declarações](claimsschema.md). 

1. Abra o arquivo de extensões da sua política. Por exemplo, <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>.
1. Pesquise o elemento [BuildingBlocks](buildingblocks.md). Se o elemento não existir, adicione-o.
1. Localize o elemento [ClaimsSchema](claimsschema.md). Se o elemento não existir, adicione-o.
1. Adicione as seguintes declarações ao elemento **ClaimsSchema**.  

```xml
<ClaimType Id="loyaltyId">
  <DisplayName>Your loyalty ID</DisplayName>
  <DataType>string</DataType>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
<ClaimType Id="promoCode">
  <DisplayName>Your promo code</DisplayName>
  <DataType>string</DataType>
  <UserInputType>Paragraph</UserInputType>
</ClaimType>
  <ClaimType Id="userLanguage">
  <DisplayName>User UI language (used by REST API to return localized error messages)</DisplayName>
  <DataType>string</DataType>
</ClaimType>
```

## <a name="add-the-restful-api-technical-profile"></a>Adicionar o perfil técnico da API RESTful 

Um [perfil técnico RESTful](restful-technical-profile.md) fornece suporte para a interface do seu próprio serviço RESTful. O Azure AD B2C envia dados para o serviço RESTful em uma coleção`InputClaims` e recebe dados de volta em uma coleção`OutputClaims`. Localize o elemento **ClaimsProviders** e adicione um novo provedor de declarações da seguinte maneira:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-ValidateProfile">
      <DisplayName>Check loyaltyId Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <!-- Set the ServiceUrl with your own REST API endpoint -->
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/ValidateProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <!-- Set AuthenticationType to Basic or ClientCertificate in production environments -->
        <Item Key="AuthenticationType">None</Item>
        <!-- REMOVE the following line in production environments -->
        <Item Key="AllowInsecureAuthInProduction">true</Item>
      </Metadata>
      <InputClaims>
        <!-- Claims sent to your REST API -->
        <InputClaim ClaimTypeReferenceId="loyaltyId" />
        <InputClaim ClaimTypeReferenceId="email" />
        <InputClaim ClaimTypeReferenceId="userLanguage" PartnerClaimType="lang" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
      </InputClaims>
      <OutputClaims>
        <!-- Claims parsed from your REST API -->
        <OutputClaim ClaimTypeReferenceId="promoCode" />
      </OutputClaims>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

Neste exemplo, `userLanguage` será enviado para o serviço REST como `lang` no conteúdo do JSON. O valor da declaração `userLanguage` contém a ID de idioma do usuário atual. Para obter mais informações, confira [resolvedor de declarações](claim-resolver-overview.md).

### <a name="configure-the-restful-api-technical-profile"></a>Configurar o perfil técnico da API RESTful 

Depois de implantar sua API REST, defina os metadados do `REST-ValidateProfile` perfil técnico para refletir sua própria API REST, incluindo:

- **ServiceUrl**. Defina a URL do ponto de extremidade da API REST.
- **SendClaimsIn**. Especifique como as declarações de entrada são enviadas para o provedor de declarações RESTful.
- **AuthenticationType**. Defina o tipo de autenticação que está sendo executada pelo provedor de declarações RESTful. 
- **AllowInsecureAuthInProduction**. Em um ambiente de produção, certifique-se de definir esses metadados para `true`
    
Consulte os [metadados do perfil técnico RESTful](restful-technical-profile.md#metadata) para obter mais configurações.

Os comentários acima de `AuthenticationType` e `AllowInsecureAuthInProduction` especificam as alterações que você deve fazer ao mudar para um ambiente de produção. Para saber como proteger suas APIs RESTful para produção, confira [Proteger API RESTful](secure-rest-api.md).

## <a name="validate-the-user-input"></a>Validar a entrada do usuário

Para obter o número de fidelidade do usuário durante a inscrição, você deve permitir que o usuário insira esses dados na tela. Adicione a declaração de saída do **lealdadeid** à página de inscrição adicionando-a ao elemento da seção de perfil técnico de inscrição existente `OutputClaims` . Especifique a lista completa de declarações de saída para controlar a ordem em que as declarações são apresentadas na tela.  

Adicione a referência de perfil técnico de validação ao perfil técnico de inscrição, que chama o `REST-ValidateProfile` . O novo perfil técnico de validação será adicionado à parte superior da `<ValidationTechnicalProfiles>` coleção definida na política de base. Esse comportamento significa que somente após a validação bem-sucedida, Azure AD B2C se move para criar a conta no diretório.   

1. Localize o elemento **ClaimsProviders**. Adicione um novo provedor de declarações da seguinte maneira:

    ```xml
    <ClaimsProvider>
      <DisplayName>Local Account</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="Verified.Email" Required="true"/>
            <OutputClaim ClaimTypeReferenceId="newPassword" Required="true"/>
            <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true"/>
            <OutputClaim ClaimTypeReferenceId="displayName"/>
            <OutputClaim ClaimTypeReferenceId="givenName"/>
            <OutputClaim ClaimTypeReferenceId="surName"/>
            <!-- Required to present the text box to collect the data from the user -->
            <OutputClaim ClaimTypeReferenceId="loyaltyId"/>
            <!-- Required to pass the promoCode returned from "REST-ValidateProfile" 
            to subsequent orchestration steps and token issuance-->
            <OutputClaim ClaimTypeReferenceId="promoCode" />
          </OutputClaims>
          <ValidationTechnicalProfiles>
            <ValidationTechnicalProfile ReferenceId="REST-ValidateProfile" />
          </ValidationTechnicalProfiles>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    <ClaimsProvider>
      <DisplayName>Self Asserted</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="SelfAsserted-Social">
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="email" />
          </InputClaims>
            <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="email" />
            <OutputClaim ClaimTypeReferenceId="displayName"/>
            <OutputClaim ClaimTypeReferenceId="givenName"/>
            <OutputClaim ClaimTypeReferenceId="surname"/>
            <!-- Required to present the text box to collect the data from the user -->
            <OutputClaim ClaimTypeReferenceId="loyaltyId"/>
            <!-- Required to pass the promoCode returned from "REST-ValidateProfile" 
            to subsequent orchestration steps and token issuance-->
            <OutputClaim ClaimTypeReferenceId="promoCode" />
          </OutputClaims>
          <ValidationTechnicalProfiles>
            <ValidationTechnicalProfile ReferenceId="REST-ValidateProfile"/>
          </ValidationTechnicalProfiles>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

## <a name="include-a-claim-in-the-token"></a>Incluir uma declaração no token 

Para retornar a declaração de código promocional de volta para o aplicativo de terceira parte confiável, adicione uma declaração de saída ao <em>`SocialAndLocalAccounts/`**`SignUpOrSignIn.xml`**</em> arquivo. A declaração de saída permitirá que a declaração seja adicionada ao token após um percurso de usuário bem-sucedido e será enviada para o aplicativo. Modifique o elemento de perfil técnico na seção terceira parte confiável para adicionar o `promoCode` como uma declaração de saída.
 
```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="displayName" />
      <OutputClaim ClaimTypeReferenceId="givenName" />
      <OutputClaim ClaimTypeReferenceId="surname" />
      <OutputClaim ClaimTypeReferenceId="email" />
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="identityProvider" />
      <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
      <OutputClaim ClaimTypeReferenceId="promoCode" DefaultValue="" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

## <a name="test-the-custom-policy"></a>Teste a política personalizada

1. Entre no [portal do Azure](https://portal.azure.com).
1. Verifique se você está usando o diretório que contém o locatário do Azure Active Directory escolhendo o filtro **Diretório + assinatura** no menu superior e escolhendo o diretório que contém o locatário do Azure Active Directory.
1. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure e pesquise e selecione **Registros de aplicativo**.
1. Selecione **Estrutura de Experiência de Identidade**.
1. Selecione **carregar política personalizada** e, em seguida, carregue os arquivos de política que você alterou: *TrustFrameworkExtensions.xml* e *SignUpOrSignin.xml*. 
1. Selecione a política de inscrição ou de entrada carregada e clique no botão **Executar agora**.
1. Você deverá conseguir se inscrever usando um endereço de email.
1. Clique no link **inscrever-se agora** .
1. Na **sua ID de fidelidade**, digite 1234 e clique em **continuar**. Neste ponto, você deve obter uma mensagem de erro de validação.
1. Altere para outro valor e clique em **continuar**.
1. O token enviado de volta ao seu aplicativo inclui a declaração `promoCode`.

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1584295703,
  "nbf": 1584292103,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1584292103,
  "auth_time": 1584292103,
  "name": "Emily Smith",
  "email": "emily@outlook.com",
  "given_name": "Emily",
  "family_name": "Smith",
  "promoCode": "84362"
  ...
}
```

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como proteger suas APIs, confira os seguintes arquivos:

- [Passo a passo: Integrar as trocas de declarações da API REST no percurso do usuário do Azure AD B2C como uma etapa de orquestração](custom-policy-rest-api-claims-exchange.md)
- [Proteger sua API RESTful](secure-rest-api.md)
- [Referência: Perfil técnico RESTful](restful-technical-profile.md)