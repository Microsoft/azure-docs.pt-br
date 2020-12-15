---
title: Gerenciar o SSO e a personalização de token usando políticas personalizadas
titleSuffix: Azure AD B2C
description: Saiba mais sobre como gerenciar SSO e personalização de token usando políticas personalizadas no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 6bfabd13c43501f7539eb3756ffbd06802cee823
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/15/2020
ms.locfileid: "97503464"
---
# <a name="manage-sso-and-token-customization-using-custom-policies-in-azure-active-directory-b2c"></a>Gerenciar a personalização de SSO e de token usando políticas personalizadas no Azure Active Directory B2C

Este artigo fornece informações sobre como você pode gerenciar suas configurações de token, sessão e logon único (SSO) usando [políticas personalizadas](custom-policy-overview.md) no Azure Active Directory B2C (Azure ad B2C).

## <a name="jwt-token-lifetimes-and-claims-configuration"></a>Tempos de vida do token JWT e configuração de declarações

Para alterar as configurações nos tempos de vida de token, você adiciona um elemento [ClaimsProviders](claimsproviders.md) ao arquivo de terceira parte confiável da política que você deseja afetar.  O elemento **ClaimsProviders** é um filho do elemento [TrustFrameworkPolicy](trustframeworkpolicy.md).

Insira o elemento ClaimsProviders entre o elemento BasePolicy e o elemento RelyingParty do arquivo de terceira parte confiável.

Nele, você precisará colocar as informações que afetam os tempos de vida de token. O XML se parece com este exemplo:

```xml
<ClaimsProviders>
  <ClaimsProvider>
    <DisplayName>Token Issuer</DisplayName>
    <TechnicalProfiles>
      <TechnicalProfile Id="JwtIssuer">
        <Metadata>
          <Item Key="token_lifetime_secs">3600</Item>
          <Item Key="id_token_lifetime_secs">3600</Item>
          <Item Key="refresh_token_lifetime_secs">1209600</Item>
          <Item Key="rolling_refresh_token_lifetime_secs">7776000</Item>
          <Item Key="IssuanceClaimPattern">AuthorityAndTenantGuid</Item>
          <Item Key="AuthenticationContextReferenceClaimPattern">None</Item>
        </Metadata>
      </TechnicalProfile>
    </TechnicalProfiles>
  </ClaimsProvider>
</ClaimsProviders>
```

Os seguintes valores são definidos no exemplo anterior:

- **Tempos de vida do token de acesso** – o valor do tempo de vida do token de acesso é definido com o item de metadados **token_lifetime_secs**. O valor padrão é de 3600 segundos (60 minutos).
- **Tempo de vida do token de ID** – o valor do tempo de vida do token de ID é definido com o item de metadados **id_token_lifetime_secs**. O valor padrão é de 3600 segundos (60 minutos).
- **Tempo de vida do token de atualização** – o valor do tempo de vida do token de atualização é definido com o item de metadados **refresh_token_lifetime_secs**. O valor padrão é de 1209600 segundos (14 dias).
- **Tempo de vida do token de atualização de janela deslizante** – se quiser definir um tempo de vida de janela deslizante para o token de atualização, defina o valor do item de metadados **rolling_refresh_token_lifetime_secs**. O valor padrão é 7776000 (90 dias). Se não quiser impor um tempo de vida de janela deslizante, substitua o item por `<Item Key="allow_infinite_rolling_refresh_token">True</Item>`.
- **Declaração iss (Issuer)** – a declaração iss (Issuer) é definida com o item de metadados **IssuanceClaimPattern**. Os valores aplicáveis são `AuthorityAndTenantGuid` e `AuthorityWithTfp`.
- **Configuração de declaração que representa a ID de política** – as opções para definir esse valor são `TFP` (política de confiança da estrutura) e `ACR` (referência do contexto de autenticação). `TFP` é o valor recomendado. Defina **AuthenticationContextReferenceClaimPattern** com o valor de `None`.

    No elemento **ClaimsSchema**, adicione esse elemento:

    ```xml
    <ClaimType Id="trustFrameworkPolicy">
      <DisplayName>Trust framework policy name</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
    ```

    No elemento **OutputClaims**, adicione este elemento:

    ```xml
    <OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />
    ```

    Para o ACR, remova o item **AuthenticationContextReferenceClaimPattern**.

- **Declaração sub (Subject)** – essa opção é padronizada para a ObjectID. Se você quer mudar essa configuração para `Not Supported`, substitua esta linha:

    ```xml
    <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" />
    ```

    com esta linha:

    ```xml
    <OutputClaim ClaimTypeReferenceId="sub" />
    ```

> [!NOTE]
> Aplicativos de página única usando o fluxo de código de autorização com PKCE sempre têm um tempo de vida de token de atualização de 24 horas. [Saiba mais sobre as implicações de segurança de tokens de atualização no navegador](../active-directory/develop/reference-third-party-cookies-spas.md#security-implications-of-refresh-tokens-in-the-browser).

## <a name="provide-optional-claims-to-your-app"></a>Fornecer declarações opcionais para seu aplicativo

As declarações de saída do [perfil técnico da política de terceira parte confiável](relyingparty.md#technicalprofile) são valores retornados para um aplicativo. A adição de declarações de saída emitirá as declarações para o token após um percurso de usuário bem-sucedido e será enviada para o aplicativo. Modifique o elemento de perfil técnico na seção terceira parte confiável para adicionar as declarações desejadas como uma declaração de saída.

1. Abra o arquivo de política personalizada. Por exemplo, SignUpOrSignin.xml.
1. Localize o elemento OutputClaims. Adicione o OutputClaim que você deseja incluir no token. 
1. Defina os atributos de declaração de saída. 

O exemplo a seguir adiciona a `accountBalance` declaração. A declaração accountBalance é enviada para o aplicativo como um saldo. 

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
      <!--Add the optional claims here-->
      <OutputClaim ClaimTypeReferenceId="accountBalance" DefaultValue="" PartnerClaimType="balance" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

O elemento OutputClaim contém os seguintes atributos:

  - **ClaimTypeReferenceId** -o identificador de um tipo de declaração já definido na seção [ClaimsSchema](claimsschema.md) no arquivo de política ou no arquivo de política pai.
  - **PartnerClaimType** -permite que você altere o nome da declaração no token. 
  - **DefaultValue** -um valor padrão. Você também pode definir o valor padrão para um [resolvedor de declaração](claim-resolver-overview.md), como a ID do locatário.
  - **AlwaysUseDefaultValue** -força o uso do valor padrão.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre a [sessão do Azure AD B2C](session-overview.md).
- Saiba como [configurar o comportamento da sessão em políticas personalizadas](session-behavior-custom-policy.md).
- Referência: [JwtIssuer](jwt-issuer-technical-profile.md).
