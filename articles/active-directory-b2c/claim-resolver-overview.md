---
title: Solicitar resolvedores em políticas personalizadas
titleSuffix: Azure AD B2C
description: Saiba como usar resolvedores de declarações em uma política personalizada no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/28/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 682b83d7016a89b27b5c936853abda1438f59c28
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/15/2020
ms.locfileid: "97508009"
---
# <a name="about-claim-resolvers-in-azure-active-directory-b2c-custom-policies"></a>Sobre resolvedores de declaração em políticas personalizadas do Azure Active Directory B2C

Os resolvedores de declaração em [políticas personalizadas](custom-policy-overview.md) de Azure Active Directory B2C (Azure ad B2C) fornecem informações de contexto sobre uma solicitação de autorização, como o nome da política, ID de correlação de solicitação, idioma da interface do usuário e muito mais.

Para usar um resolvedor de declaração em uma declaração de entrada ou saída, você define uma cadeia de caracteres **ClaimType**, no elemento [ClaimsSchema](claimsschema.md) e, em seguida, define **DefaultValue** como o resolvedor de declaração no elemento de declaração de entrada ou saída. O Azure AD B2C lê o valor do resolvedor de declaração e usa o valor no perfil técnico.

No exemplo a seguir, um tipo de declaração denominado `correlationId` é definido com um **DataType** de `string`.

```xml
<ClaimType Id="correlationId">
  <DisplayName>correlationId</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Request correlation Id</UserHelpText>
</ClaimType>
```

No perfil técnico, mapeie o resolvedor de declaração para o tipo de declaração. O Azure AD B2C preenche o valor do resolvedor de declaração `{Context:CorrelationId}` para a declaração `correlationId` e envia a declaração para o perfil técnico.

```xml
<InputClaim ClaimTypeReferenceId="correlationId" DefaultValue="{Context:CorrelationId}" />
```

## <a name="claim-resolver-types"></a>Tipos de resolvedor de declaração

As seções a seguir listam os resolvedores de declarações disponíveis.

### <a name="culture"></a>Cultura

| Declaração | Descrição | Exemplo |
| ----- | ----------- | --------|
| {Culture:LanguageName} | As duas letras do código ISO para o idioma. | en |
| {Culture:LCID}   | O LCID do código de idioma. | 1046 |
| {Culture:RegionName} | As duas letras do código ISO para a região. | US |
| {Culture:RFC5646} | O código de idioma RFC5646. | en-US |

### <a name="policy"></a>Política

| Declaração | Descrição | Exemplo |
| ----- | ----------- | --------|
| {Policy:PolicyId} | O nome da política de terceira parte confiável. | B2C_1A_signup_signin |
| {Policy:RelyingPartyTenantId} | A ID do locatário da política de terceira parte confiável. | your-tenant.onmicrosoft.com |
| {Policy:TenantObjectId} | A ID do objeto do locatário da política de terceira parte confiável. | 00000000-0000-0000-0000-000000000000 |
| {Policy:TrustFrameworkTenantId} | A ID do locatário e da estrutura de confiança. | your-tenant.onmicrosoft.com |

### <a name="openid-connect"></a>OpenID Connect

| Declaração | Descrição | Exemplo |
| ----- | ----------- | --------|
| {OIDC:AuthenticationContextReferences} |O parâmetro de cadeia de caracteres da consulta `acr_values`. | N/D |
| {OIDC:ClientId} |O parâmetro de cadeia de caracteres da consulta `client_id`. | 00000000-0000-0000-0000-000000000000 |
| {OIDC:DomainHint} |O parâmetro de cadeia de caracteres da consulta `domain_hint`. | facebook.com |
| {OIDC:LoginHint} |  O parâmetro de cadeia de caracteres da consulta `login_hint`. | someone@contoso.com |
| {OIDC:MaxAge} | O `max_age`. | N/D |
| {OIDC:Nonce} |O parâmetro de cadeia de caracteres da consulta `Nonce`. | defaultNonce |
| {OIDC: senha}| A senha do usuário do [fluxo de credenciais de senha do proprietário do recurso](ropc-custom.md) .| password1| 
| {OIDC:Prompt} | O parâmetro de cadeia de caracteres da consulta `prompt`. | login |
| {OIDC: RedirectUri} |O parâmetro de cadeia de caracteres da consulta `redirect_uri`. | https://jwt.ms |
| {OIDC:Resource} |O parâmetro de cadeia de caracteres da consulta `resource`. | N/D |
| {OIDC: escopo} |O parâmetro de cadeia de caracteres da consulta `scope`. | openid |
| {OIDC: username}| O nome de usuário do [fluxo de credenciais de senha do proprietário do recurso](ropc-custom.md) .| emily@contoso.com| 

### <a name="context"></a>Contexto

| Declaração | Descrição | Exemplo |
| ----- | ----------- | --------|
| {Context:BuildNumber} | A versão do Identity Experience Framework (número de build).  | 1.0.507.0 |
| {Context:CorrelationId} | ID de correlação.  | 00000000-0000-0000-0000-000000000000 |
| {Context:DateTimeInUtc} |A data e hora em UTC.  | 10/10/2018 12:00:00 PM |
| {Context:DeploymentMode} |O modo de implantação de política.  | Produção |
| {Context:IPAddress} | O endereço IP do usuário. | 11.111.111.11 |
| {Context: KMSI} | Indica se a caixa [de seleção manter-me conectado](session-behavior.md?pivots=b2c-custom-policy#enable-keep-me-signed-in-kmsi) está marcada. |  true |

### <a name="claims"></a>Declarações 

| Declaração | Descrição | Exemplo |
| ----- | ----------- | --------|
| {Declaração: tipo de declaração} | Um identificador de um tipo de declaração já definido na seção de ClaimsSchema no arquivo de política ou de política pai.  Por exemplo: `{Claim:displayName}` , ou `{Claim:objectId}` . | Um valor de tipo de declaração.|


### <a name="oauth2-key-value-parameters"></a>Parâmetros de chave-valor de OAuth2

Qualquer nome de parâmetro incluído como parte de uma solicitação OIDC ou OAuth2 pode ser mapeado para uma declaração no percurso do usuário. Por exemplo, a solicitação do aplicativo pode incluir um parâmetro da cadeia de consulta com um nome de `app_session`, `loyalty_number` ou qualquer cadeia de consulta personalizada.

| Declaração | Descrição | Exemplo |
| ----- | ----------------------- | --------|
| {OAUTH-KV:campaignId} | Um parâmetro de cadeia de consulta. | Hawaii |
| {OAUTH-KV:app_session} | Um parâmetro de cadeia de consulta. | A3C5R |
| {OAUTH-KV:loyalty_number} | Um parâmetro de cadeia de consulta. | 1234 |
| {OAUTH-KV:any custom query string} | Um parâmetro de cadeia de consulta. | N/D |

### <a name="oauth2"></a>OAuth2

| Declaração | Descrição | Exemplo |
| ----- | ----------------------- | --------|
| {oauth2:access_token} | O token de acesso. | N/D |
| {oauth2: refresh_token} | O token de atualização. | N/D |


### <a name="saml"></a>SAML

| Declaração | Descrição | Exemplo |
| ----- | ----------- | --------|
| {SAML: AuthnContextClassReferences} | O `AuthnContextClassRef` valor do elemento, da solicitação SAML. | urn: Oasis: names: TC: SAML: 2.0: AC: classes: PasswordProtectedTransport |
| {SAML: NameIdPolicyFormat} | O `Format` atributo, do `NameIDPolicy` elemento da solicitação SAML. | urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress |
| {SAML: emissor} |  O `Issuer` valor do elemento SAML da solicitação SAML.| `https://contoso.com` |
| {SAML: AllowCreate} | O `AllowCreate` valor do atributo, do `NameIDPolicy` elemento da solicitação SAML. | Verdadeiro |
| {SAML: ForceAuthn} | O `ForceAuthN` valor do atributo, do `AuthnRequest` elemento da solicitação SAML. | Verdadeiro |
| {SAML: ProviderName} | O `ProviderName` valor do atributo, do `AuthnRequest` elemento da solicitação SAML.| Contoso.com |
| {SAML: Relaystate} | O parâmetro de cadeia de caracteres da consulta `RelayState`.| 
| {SAML: subject} | O `Subject` do elemento NameID da solicitação do SAML Authn.| 

## <a name="using-claim-resolvers"></a>Usando resolvedores de declaração

Você pode usar resolvedores de declarações com os seguintes elementos:

| Item | Elemento | Configurações |
| ----- | ----------------------- | --------|
|Perfil técnico do Application Insights |`InputClaim` | |
|[Azure Active Directory](active-directory-technical-profile.md) o perfil técnico| `InputClaim`, `OutputClaim`| 1, 2|
|Perfil técnico do [OAuth2](oauth2-technical-profile.md)| `InputClaim`, `OutputClaim`| 1, 2|
|Perfil técnico do [OpenID Connect](openid-connect-technical-profile.md)| `InputClaim`, `OutputClaim`| 1, 2|
|Perfil técnico de [transformação de declarações](claims-transformation-technical-profile.md)| `InputClaim`, `OutputClaim`| 1, 2|
|Perfil técnico do [provedor RESTful](restful-technical-profile.md)| `InputClaim`| 1, 2|
|Perfil técnico do [provedor de identidade SAML](saml-identity-provider-technical-profile.md)| `OutputClaim`| 1, 2|
|Perfil técnico [autodeclarado](self-asserted-technical-profile.md)| `InputClaim`, `OutputClaim`| 1, 2|
|[ContentDefinition](contentdefinitions.md)| `LoadUri`| |
|[ContentDefinitionParameters](relyingparty.md#contentdefinitionparameters)| `Parameter` | |
|Perfil técnico do [RelyingParty](relyingparty.md#technicalprofile)| `OutputClaim`| 2 |

Configurações:
1. Os `IncludeClaimResolvingInClaimsHandling` metadados devem ser definidos como `true` .
1. O atributo de declarações de entrada ou saída `AlwaysUseDefaultValue` deve ser definido como `true` .

## <a name="claim-resolvers-samples"></a>Exemplos de resolvedores de declaração

### <a name="restful-technical-profile"></a>Perfil técnico RESTful

Em um perfil técnico [RESTful](restful-technical-profile.md), você talvez queira enviar o idioma do usuário, o nome da política, o escopo e a ID do cliente. Com base nas declarações, a API REST pode executar a lógica de negócios personalizada e, se necessário, gerar uma mensagem de erro localizada.

O exemplo a seguir mostra um perfil técnico RESTful com este cenário:

```xml
<TechnicalProfile Id="REST">
  <DisplayName>Validate user input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app.azurewebsites.net/api/identity</Item>
    <Item Key="AuthenticationType">None</Item>
    <Item Key="SendClaimsIn">Body</Item>
    <Item Key="IncludeClaimResolvingInClaimsHandling">true</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userLanguage" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
    <InputClaim ClaimTypeReferenceId="policyName" DefaultValue="{Policy:PolicyId}" AlwaysUseDefaultValue="true" />
    <InputClaim ClaimTypeReferenceId="scope" DefaultValue="{OIDC:Scope}" AlwaysUseDefaultValue="true" />
    <InputClaim ClaimTypeReferenceId="clientId" DefaultValue="{OIDC:ClientId}" AlwaysUseDefaultValue="true" />
  </InputClaims>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

### <a name="direct-sign-in"></a>Entrada direta

Usando resolvedores de declaração, você pode preencher previamente o nome de entrada ou as credenciais diretas em um provedor de identidade social específico, como Facebook, LinkedIn ou uma conta Microsoft. Para obter mais informações, confira [Configurar entrada direta usando o Azure Active Directory B2C](direct-signin.md).

### <a name="dynamic-ui-customization"></a>Personalização de interface do usuário dinâmica

Azure AD B2C permite passar parâmetros de cadeia de caracteres de consulta para seus pontos de extremidade de definição de conteúdo HTML para renderizar dinamicamente o conteúdo da página. Por exemplo, esse recurso permite a capacidade de modificar a imagem de plano de fundo na página Azure AD B2C inscrição ou entrada com base em um parâmetro personalizado que você passa de seu aplicativo Web ou móvel. Para obter mais informações, confira [Configurar dinamicamente a interface do usuário usando políticas personalizadas no Azure Active Directory B2C](customize-ui-with-html.md#configure-dynamic-custom-page-content-uri). Você também pode localizar sua página HTML com base em um parâmetro de idioma, ou pode alterar o conteúdo com base na ID do cliente.

O exemplo a seguir passa o parâmetro de cadeia de caracteres de consulta chamado **campaignid** com um valor de `Hawaii` , um código de **idioma** `en-US` , e o **aplicativo** que representa a ID do cliente:

```xml
<UserJourneyBehaviors>
  <ContentDefinitionParameters>
    <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
    <Parameter Name="language">{Culture:RFC5646}</Parameter>
    <Parameter Name="app">{OIDC:ClientId}</Parameter>
  </ContentDefinitionParameters>
</UserJourneyBehaviors>
```

Como resultado, Azure AD B2C envia os parâmetros acima para a página de conteúdo HTML:

```
/selfAsserted.aspx?campaignId=hawaii&language=en-US&app=0239a9cc-309c-4d41-87f1-31288feb2e82
```

### <a name="content-definition"></a>Definição de conteúdo

Em um [ContentDefinition](contentdefinitions.md) `LoadUri` , você pode enviar resolvedores de declaração para efetuar pull de conteúdo de locais diferentes, com base nos parâmetros usados.

```xml
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://contoso.blob.core.windows.net/{Culture:LanguageName}/myHTML/unified.html</LoadUri>
  ...
</ContentDefinition>
```

### <a name="application-insights-technical-profile"></a>Perfil técnico do Application Insights

Com o Azure Application Insights e resolvedores de declaração, você pode obter insights sobre o comportamento do usuário. No perfil técnico do Application Insights, você envia declarações de entrada que persistem no Azure Application Insights. Para obter mais informações, confira [Acompanhar o comportamento do usuário em jornadas do Azure AD B2C usando o Application Insights](analytics-with-application-insights.md). O exemplo a seguir envia a ID de política, a ID de correlação, o idioma e a ID do cliente para o Azure Application Insights.

```xml
<TechnicalProfile Id="AzureInsights-Common">
  <DisplayName>Alternate Email</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
    <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:CorrelationId}" DefaultValue="{Context:CorrelationId}" />
    <InputClaim ClaimTypeReferenceId="language" PartnerClaimType="{property:language}" DefaultValue="{Culture:RFC5646}" />
    <InputClaim ClaimTypeReferenceId="AppId" PartnerClaimType="{property:App}" DefaultValue="{OIDC:ClientId}" />
  </InputClaims>
</TechnicalProfile>
```

### <a name="relying-party-policy"></a>Política de terceira parte confiável

Em um perfil técnico de política de terceira parte [confiável](relyingparty.md) , talvez você queira enviar a ID de locatário ou a ID de correlação para o aplicativo de terceira parte confiável dentro do JWT.

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
        <OutputClaim ClaimTypeReferenceId="correlationId" AlwaysUseDefaultValue="true" DefaultValue="{Context:CorrelationId}" />
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
```
