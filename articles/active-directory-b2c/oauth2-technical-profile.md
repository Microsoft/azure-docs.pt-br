---
title: Definir um perfil técnico do OAuth2 em uma política personalizada
titleSuffix: Azure AD B2C
description: Defina um perfil técnico do OAuth2 em uma política personalizada no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: f79360269c19f6770fa12120ec34497b29015e7e
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99050678"
---
# <a name="define-an-oauth2-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definir um perfil técnico do OAuth2 em uma política personalizada Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) fornece suporte para o provedor de identidade do protocolo OAuth2. OAuth2 é o principal protocolo para autorização e autenticação delegada. Para obter mais informações, confira [RFC 6749 O Framework de Autorização OAuth 2.0](https://tools.ietf.org/html/rfc6749). Com um perfil técnico do OAuth2, você pode federar com um provedor de identidade baseado em OAuth2, como o Facebook. A Federação com um provedor de identidade permite que os usuários entrem com suas identidades sociais ou empresariais existentes.

## <a name="protocol"></a>Protocolo

O atributo **Name** do elemento **Protocol** precisa ser definido como `OAuth2`. Por exemplo, o protocolo para o perfil técnico **Facebook-OAUTH** é `OAuth2`:

```xml
<TechnicalProfile Id="Facebook-OAUTH">
  <DisplayName>Facebook</DisplayName>
  <Protocol Name="OAuth2" />
  ...
```

## <a name="input-claims"></a>Declarações de entrada

Os elementos **InputClaims** e **InputClaimsTransformations** não são necessários. Mas talvez você queira enviar parâmetros adicionais para seu provedor de identidade. O exemplo a seguir adiciona o parâmetro de cadeia de caracteres de consulta **domain_hint** com o valor de `contoso.com` à solicitação de autorização.

```xml
<InputClaims>
  <InputClaim ClaimTypeReferenceId="domain_hint" DefaultValue="contoso.com" />
</InputClaims>
```

## <a name="output-claims"></a>Declarações de saída

O elemento **OutputClaims** contém uma lista de declarações retornadas pelo provedor de identidade OAuth2. Talvez seja necessário mapear o nome da declaração definida em sua política para o nome definido no provedor de identidade. Você também pode incluir declarações que não são retornadas pelo provedor de identidade, desde que você defina o atributo `DefaultValue`.

O elemento **OutputClaimsTransformations** pode conter uma coleção de elementos **OutputClaimsTransformation** usados para modificar as declarações de saída ou gerar novas declarações.

O exemplo a seguir mostra as declarações retornadas pelo provedor de identidade do Facebook:

- A declaração **first_name** é mapeada para a declaração **givenName**.
- A declaração **last_name** é mapeada para a declaração **surname**.
- A Declaração **DisplayName** sem mapeamento de nome.
- A declaração **email** sem mapeamento de nome.

O perfil técnico também retorna declarações que não são retornadas pelo provedor de identidade:

- A declaração **identityProvider** que contém o nome do provedor de identidade.
- A declaração **authenticationSource** com um valor padrão de **socialIdpAuthentication**.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="id" />
  <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="first_name" />
  <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="last_name" />
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
  <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="facebook.com" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
</OutputClaims>
```

## <a name="metadata"></a>Metadados

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| client_id | Yes | O identificador do aplicativo do provedor de identidade. |
| IdTokenAudience | No | O público-alvo do id_token. Se for especificado, o Azure AD B2C verificará se o token está em uma declaração retornada pelo provedor de identidade e é igual ao especificado. |
| authorization_endpoint | Yes | A URL do ponto de extremidade da autorização, de acordo com RFC 6749. |
| AccessTokenEndpoint | Yes | A URL do ponto de extremidade do token, de acordo com RFC 6749. |
| ClaimsEndpoint | Yes | A URL do ponto de extremidade de informações do usuário, de acordo com RFC 6749. |
| end_session_endpoint | Yes | A URL do ponto de extremidade da sessão final de acordo com a RFC 6749. |
| AccessTokenResponseFormat | No | O formato da chamada de ponto de extremidade do token de acesso. Por exemplo, o Facebook requer um método HTTP GET, mas a resposta do token de acesso está no formato JSON. |
| AdditionalRequestQueryParameters | No | Parâmetros de consulta de solicitação adicionais. Por exemplo, talvez você queira enviar parâmetros adicionais para seu provedor de identidade. Você pode incluir vários parâmetros usando o delimitador de vírgula. |
| ClaimsEndpointAccessTokenName | No | O nome do que o parâmetro de cadeia de caracteres de consulta do token de acesso. Os pontos de extremidade de declaração de alguns provedores de identidade dão suporte a solicitação HTTP GET. Nesse caso, o token de portador é enviado usando um parâmetro de cadeia de caracteres de consulta em vez do cabeçalho de autorização. Valor padrão: `access_token` . |
| ClaimsEndpointFormatName | No | O nome do que o parâmetro de cadeia de caracteres de consulta do formato. Por exemplo, você pode definir o nome como `format` neste ponto de extremidade de declarações do LinkedIn `https://api.linkedin.com/v1/people/~?format=json`. |
| ClaimsEndpointFormat | No | O valor do que o parâmetro de cadeia de caracteres de consulta do formato. Por exemplo, você pode definir o valor como `json` neste ponto de extremidade de declarações do LinkedIn `https://api.linkedin.com/v1/people/~?format=json`. |
| BearerTokenTransmissionMethod | No | Especifica como o token é enviado. O método padrão é uma cadeia de caracteres de consulta. Para enviar o token como um cabeçalho de solicitação, defina como `AuthorizationHeader` . |
| ProviderName | No | O nome do provedor de identidade. |
| response_mode | No | O método que o provedor de identidade usa para enviar o resultado de volta ao Azure AD B2C. Valores possíveis: `query`, `form_post` (padrão) ou `fragment`. |
| escopo | No | O escopo da solicitação que é definido de acordo com a especificação do provedor de identidade OAuth2. Como `openid`, `profile` e `email`. |
| HttpBinding | No | A associação HTTP esperada para o token de acesso e pontos de extremidade do token de declarações. Valores possíveis: `GET` ou `POST`.  |
| ResponseErrorCodeParamName | No | O nome do parâmetro que contém a mensagem de erro retornada por HTTP 200 (OK). |
| ExtraParamsInAccessTokenEndpointResponse | No | Contém os parâmetros extra que podem ser retornados na resposta de **AccessTokenEndpoint** por alguns provedores de identidade. Por exemplo, a resposta de **AccessTokenEndpoint** contém um parâmetro extra, como `openid`, que é um parâmetro obrigatório, além de access_token em uma cadeia de caracteres de consulta de solicitação **ClaimsEndpoint**. Vários nomes de parâmetro devem ter um escape e ser separados pelo delimitador de vírgula ','. |
| ExtraParamsInClaimsEndpointRequest | No | Contém os parâmetros extra que podem ser retornados na solicitação **ClaimsEndpoint** por alguns provedores de identidade. Vários nomes de parâmetro devem ter um escape e ser separados pelo delimitador de vírgula ','. |
| IncludeClaimResolvingInClaimsHandling  | No | Para declarações de entrada e saída, especifica se a [resolução de declarações](claim-resolver-overview.md) está incluída no perfil técnico. Valores possíveis: `true` ou `false` (padrão). Se você quiser usar um resolvedor de declarações no perfil técnico, defina como `true` . |
| ResolveJsonPathsInJsonTokens  | No | Indica se o perfil técnico resolve caminhos JSON. Valores possíveis: `true` ou `false` (padrão). Use esses metadados para ler dados de um elemento JSON aninhado. Em um [OutputClaim](technicalprofiles.md#output-claims), defina o `PartnerClaimType` como o elemento de caminho JSON que você deseja gerar. Por exemplo: `firstName.localized` , ou `data.0.to.0.email` .|
|token_endpoint_auth_method| No| Especifica como Azure AD B2C envia o cabeçalho de autenticação para o ponto de extremidade do token. Valores possíveis: `client_secret_post` (padrão) e `client_secret_basic` (visualização pública). Para obter mais informações, consulte a [seção autenticação de cliente OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html#ClientAuthentication). |
|SingleLogoutEnabled| No| Indica se, durante a entrada, o perfil técnico tenta sair de provedores de identidade federada. Para obter mais informações, consulte [Azure ad B2C sessão](session-behavior.md#sign-out)sair. Valores possíveis: `true` (padrão) ou `false` .|
| UsePolicyInRedirectUri | No | Indica se deve ser usada uma política ao criar o URI de redirecionamento. Quando você configura seu aplicativo no provedor de identidade, precisa especificar o URI de redirecionamento. O URI de redirecionamento aponta para Azure AD B2C, `https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp` . Se você especificar `true`, precisará adicionar um URI de redirecionamento a cada política que você usar. Por exemplo: `https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/{policy-name}/oauth2/authresp`. |

## <a name="cryptographic-keys"></a>Chaves criptográficas

O elemento **CryptographicKeys** contém o seguinte atributo:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| client_secret | Yes | O segredo do cliente do aplicativo do provedor de identidade. A chave de criptografia será necessária apenas se os metadados **response_types** estiverem definidos como `code`. Nesse caso, o Azure AD B2C faz outra chamada para trocar o código de autorização para um token de acesso. Se os metadados estiverem definidos como `id_token` , você poderá omitir a chave criptográfica. |

## <a name="redirect-uri"></a>URI de redirecionamento

Ao configurar o URI de redirecionamento do seu provedor de identidade, insira `https://{tenant-name}.b2clogin.com/{tenant-name}.onmicrosoft.com/oauth2/authresp`. Certifique-se de substituir `{tenant-name}` pelo nome do locatário (por exemplo, contosob2c). O URI de redirecionamento deve ser todo em letras minúsculas.

Exemplos:

- [Adicionar Google+ como um provedor de identidade OAuth2 usando políticas personalizadas](identity-provider-google.md)
