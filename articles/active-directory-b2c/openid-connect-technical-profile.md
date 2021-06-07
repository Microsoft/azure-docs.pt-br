---
title: Definir um perfil técnico do OpenID Connect em uma política personalizada
titleSuffix: Azure AD B2C
description: Defina um perfil técnico do OpenID Connect em uma política personalizada no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/04/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 670fbeeb006d21e29675f88895018d1a453a1c54
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102120293"
---
# <a name="define-an-openid-connect-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definir um perfil técnico do OpenID Connect em uma política personalizada de Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O Azure Active Directory B2C (Azure AD B2C) fornece suporte para o provedor de identidade do protocolo [OpenID Connect](https://openid.net/2015/04/17/openid-connect-certification-program/) . O OpenID Connect 1.0 define uma camada de identidade com base em OAuth 2.0 e representa a estado de última geração em protocolos de autenticação modernos. Com um perfil técnico do OpenID Connect, você pode federar com um provedor de identidade baseado em OpenID Connect, como o Azure AD. A Federação com um provedor de identidade permite que os usuários entrem com suas identidades sociais ou empresariais existentes.

## <a name="protocol"></a>Protocolo

O atributo **Name** do elemento **Protocol** precisa ser definido como `OpenIdConnect`. Por exemplo, o protocolo para o perfil técnico **MSA-OIDC** é `OpenIdConnect`:

```xml
<TechnicalProfile Id="MSA-OIDC">
  <DisplayName>Microsoft Account</DisplayName>
  <Protocol Name="OpenIdConnect" />
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

O elemento **OutputClaims** contém uma lista de declarações retornadas pelo provedor de identidade do OpenID Connect. Talvez seja necessário mapear o nome da declaração definida em sua política para o nome definido no provedor de identidade. Você também pode incluir declarações que não são retornadas pelo provedor de identidade, desde que defina o atributo `DefaultValue`.

O elemento **OutputClaimsTransformations** pode conter uma coleção de elementos **OutputClaimsTransformation** usados para modificar as declarações de saída ou gerar novas declarações.

O exemplo a seguir mostra as declarações retornadas pelo provedor de identidade de Conta Microsoft:

- A **subdeclaração que** é mapeada para a Declaração **issuerUserId** .
- A declaração **name** que é mapeada para a declaração **displayName**.
- O **email** sem mapeamento de nome.

O perfil técnico também retorna declarações que não são retornadas pelo provedor de identidade:

- A declaração **identityProvider** que contém o nome do provedor de identidade.
- A declaração **authenticationSource** com um valor padrão de **socialIdpAuthentication**.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="live.com" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
  <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email" />
</OutputClaims>
```

## <a name="metadata"></a>Metadados

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| client_id | Sim | O identificador do aplicativo do provedor de identidade. |
| IdTokenAudience | Não | O público-alvo do id_token. Se especificado, Azure AD B2C verifica se a `aud` declaração em um token retornado pelo provedor de identidade é igual à especificada nos metadados IdTokenAudience.  |
| METADATA | Sim | Uma URL que aponta para um documento de configuração do provedor de identidade do OpenID Connect, que também é conhecido como ponto de extremidade de configuração bem conhecido do OpenID. A URL pode conter a `{tenant}` expressão, que é substituída pelo nome do locatário.  |
| authorization_endpoint | Não | Uma URL que aponta para um ponto de extremidade de autorização de configuração do provedor de identidade do OpenID Connect. O valor de authorization_endpoint metadados tem precedência sobre o `authorization_endpoint` especificado no ponto de extremidade de configuração bem conhecido do OpenID. A URL pode conter a `{tenant}` expressão, que é substituída pelo nome do locatário. |
| end_session_endpoint | Não | A URL do ponto de extremidade da sessão final. O valor de authorization_endpoint metadados tem precedência sobre o `end_session_endpoint` especificado no ponto de extremidade de configuração bem conhecido do OpenID. |
| emissor | Não | O identificador exclusivo de um provedor de identidade do OpenID Connect. O valor dos metadados do emissor tem precedência sobre o `issuer` especificado no ponto de extremidade de configuração bem conhecido do OpenID.  Se especificado, Azure AD B2C verifica se a `iss` declaração em um token retornado pelo provedor de identidade é igual à especificada nos metadados do emissor. |
| ProviderName | Não | O nome do provedor de identidade.  |
| response_types | Não | O tipo de resposta de acordo com a especificação do OpenID Connect Core 1.0. Valores possíveis: `id_token`, `code` ou `token`. |
| response_mode | Não | O método que o provedor de identidade usa para enviar o resultado de volta ao Azure AD B2C. Valores possíveis: `query`, `form_post` (padrão) ou `fragment`. |
| scope | Não | O escopo da solicitação que é definido de acordo com a especificação do OpenID Connect Core 1,0. Como `openid`, `profile` e `email`. |
| HttpBinding | Não | A associação HTTP esperada para o token de acesso e pontos de extremidade do token de declarações. Valores possíveis: `GET` ou `POST`.  |
| ValidTokenIssuerPrefixes | Não | Uma chave que pode ser usada para entrar em cada um dos locatários ao usando um provedor de identidade multilocatário como o Azure Active Directory. |
| UsePolicyInRedirectUri | Não | Indica se deve ser usada uma política ao criar o URI de redirecionamento. Quando você configura seu aplicativo no provedor de identidade, precisa especificar o URI de redirecionamento. O URI de redirecionamento aponta para Azure AD B2C, `https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp` .  Se você especificar `true`, precisará adicionar um URI de redirecionamento a cada política que você usar. Por exemplo: `https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/{policy-name}/oauth2/authresp`. |
| MarkAsFailureOnStatusCode5xx | Não | Indica se uma solicitação para um serviço externo deverá ser marcada como uma falha se o código de status Http estiver no intervalo 5xx. O padrão é `false`. |
| DiscoverMetadataByTokenIssuer | Não | Indica se os metadados OIDC devem ser descobertos usando o emissor no token JWT. |
| IncludeClaimResolvingInClaimsHandling  | Não | Para declarações de entrada e saída, especifica se a [resolução de declarações](claim-resolver-overview.md) está incluída no perfil técnico. Valores possíveis: `true` ou `false` (padrão). Se você quiser usar um resolvedor de declarações no perfil técnico, defina como `true` . |
| token_endpoint_auth_method | Não | Especifica como Azure AD B2C envia o cabeçalho de autenticação para o ponto de extremidade do token. Valores possíveis: `client_secret_post` (padrão) e `client_secret_basic` (visualização pública). Para obter mais informações, consulte a [seção autenticação de cliente OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html#ClientAuthentication). |
| token_signing_algorithm | Não | O algoritmo de assinatura usado para asserções de cliente quando os metadados de **token_endpoint_auth_method** são definidos como `private_key_jwt` . Valores possíveis: `RS256` (padrão). |
| SingleLogoutEnabled | Não | Indica se, durante a entrada, o perfil técnico tenta sair de provedores de identidade federada. Para obter mais informações, consulte [Azure ad B2C sessão](./session-behavior.md#sign-out)sair.  Valores possíveis: `true` (padrão) ou `false` . |
|ReadBodyClaimsOnIdpRedirect| Não| Defina como `true` para ler declarações do corpo da resposta no redirecionamento do provedor de identidade. Esses metadados são usados com a [ID da Apple](identity-provider-apple-id.md), em que as declarações retornam na carga de resposta.|

```xml
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
```

### <a name="ui-elements"></a>Elementos da interface do usuário
 
As configurações a seguir podem ser usadas para configurar a mensagem de erro exibida após a falha. Os metadados devem ser configurados no perfil técnico do OpenID Connect. A mensagem de erro pode ser [localizada](localization-string-ids.md#sign-up-or-sign-in-error-messages).

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| UserMessageIfClaimsPrincipalDoesNotExist | Não | A mensagem a ser exibida para o usuário se uma conta com o nome de usuário fornecido não for encontrada no diretório. |
| UserMessageIfInvalidPassword | Não | A mensagem a ser exibida para o usuário se a senha estiver incorreta. |
| UserMessageIfOldPasswordUsed| Não |  A mensagem a ser exibida para o usuário se uma senha antiga for usada.|

## <a name="cryptographic-keys"></a>Chaves criptográficas

O elemento **CryptographicKeys** contém o seguinte atributo:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| client_secret | Sim | O segredo do cliente do aplicativo do provedor de identidade. Essa chave de criptografia será necessária somente se o **response_types** metadados estiver definido como `code` e **token_endpoint_auth_method** será definido como `client_secret_post` ou `client_secret_basic` . Nesse caso, o Azure AD B2C faz outra chamada para trocar o código de autorização para um token de acesso. Se os metadados forem definidos como `id_token`, você poderá omitir a chave de criptografia.  |
| assertion_signing_key | Sim | A chave privada RSA que será usada para assinar a asserção do cliente. Essa chave de criptografia será necessária somente se os metadados de **token_endpoint_auth_method** estiverem definidos como `private_key_jwt` . |

## <a name="redirect-uri"></a>URI de redirecionamento

Ao configurar o URI de redirecionamento do seu provedor de identidade, insira `https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp`. Certifique-se de substituir `{your-tenant-name}` pelo nome do locatário. O URI de redirecionamento deve ser todo em letras minúsculas.

Exemplos:

- [Adicionar MSA (Conta Microsoft) como um provedor de identidade usando políticas personalizadas](identity-provider-microsoft-account.md)
- [Entrar usando contas do Azure AD](identity-provider-azure-ad-single-tenant.md)
- [Permitir que os usuários entrem em um provedor de identidade multilocatário do Azure AD usando políticas personalizadas](identity-provider-azure-ad-multi-tenant.md)
