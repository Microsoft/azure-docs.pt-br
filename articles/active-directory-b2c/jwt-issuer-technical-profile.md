---
title: Definir um perfil técnico para um emissor JWT em uma política personalizada
titleSuffix: Azure AD B2C
description: Defina um perfil técnico para um emissor JWT (token Web JSON) em uma política personalizada no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/04/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 4faa7e68b50b83368837b75cd04be566d816f6d3
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102119800"
---
# <a name="define-a-technical-profile-for-a-jwt-token-issuer-in-an-azure-active-directory-b2c-custom-policy"></a>Definir um perfil técnico para um emissor de token JWT em uma política personalizada do Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O Azure AD B2C (Azure Active Directory B2C) emite vários tipos de tokens de segurança ao processar cada fluxo de autenticação. Um perfil técnico para um emissor de token JWT emite um token JWT que é retornado para o aplicativo de terceira parte confiável. Normalmente, esse perfil técnico é a última etapa de orquestração no percurso do usuário.

## <a name="protocol"></a>Protocolo

O atributo **Name** do elemento **Protocol** precisa ser definido como `OpenIdConnect`. Defina o elemento **OutputTokenFormat** como `JWT`.

O exemplo a seguir mostra um perfil técnico para `JwtIssuer`:

```xml
<TechnicalProfile Id="JwtIssuer">
  <DisplayName>JWT Issuer</DisplayName>
  <Protocol Name="OpenIdConnect" />
  <OutputTokenFormat>JWT</OutputTokenFormat>
  <Metadata>
    <Item Key="client_id">{service:te}</Item>
    <Item Key="issuer_refresh_token_user_identity_claim_type">objectId</Item>
    <Item Key="SendTokenResponseBodyWithJsonNumbers">true</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
    <Key Id="issuer_refresh_token_key" StorageReferenceId="B2C_1A_TokenEncryptionKeyContainer" />
  </CryptographicKeys>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-jwt-issuer" />
</TechnicalProfile>
```

## <a name="input-output-and-persist-claims"></a>Entrada, saída e manter declarações

Os elementos **InputClaims**, **OutputClaims** e **PersistClaims** estão vazios ou ausentes. Os elementos **InutputClaimsTransformations** e **OutputClaimsTransformations** também estão ausentes.

## <a name="metadata"></a>Metadados

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| issuer_refresh_token_user_identity_claim_type | Sim | A declaração que deve ser usada como a declaração de identidade do usuário nos códigos de autorização OAuth2 e tokens de atualização. Por padrão, você deve defini-la como `objectId`, a menos que especifique um tipo de declaração SubjectNamingInfo diferente. |
| SendTokenResponseBodyWithJsonNumbers | Não | Sempre defina como `true`. Para formatos herdados em que os valores numéricos são fornecidos como cadeias de caracteres em vez de números JSON, defina como `false`. Esse atributo é necessário para clientes que adotaram uma dependência em uma implementação anterior que retornou propriedades como cadeias de caracteres. |
| token_lifetime_secs | Não | Tempos de vida do token de acesso. O tempo de vida do token de portador do OAuth 2.0 usado para obter acesso a um recurso protegido. O padrão é 3.600 segundos (1 hora). O mínimo (inclusive) é 300 segundos (5 minutos). O máximo (inclusive) é 86.400 segundos (24 horas). |
| id_token_lifetime_secs | Não | Tempos de vida do token de ID. O padrão é 3.600 segundos (1 hora). O mínimo (inclusive) é 300 segundos (5 minutos). O máximo (inclusive) é 86.400 segundos (24 horas). |
| refresh_token_lifetime_secs | Não | Tempos de vida do token de atualização. O período de tempo máximo que um token de atualização poderá ser usado para adquirir um novo token de acesso se o aplicativo tiver recebido o escopo offline_access. O padrão é 120,9600 segundos (14 dias). O mínimo (inclusive) é 86.400 segundos (24 horas). O máximo (inclusive) é 7.776.000 segundos (90 dias). |
| rolling_refresh_token_lifetime_secs | Não | Tempo de vida da janela deslizante do token de atualização. Após a passagem desse período, o usuário será forçado a autenticar-se novamente, independentemente do período de validade do token de atualização mais recente adquirido pelo aplicativo. Se você não quiser impor um tempo de vida de janela deslizante, defina o valor de allow_infinite_rolling_refresh_token como `true`. O padrão é 7.776.000 segundos (90 dias). O mínimo (inclusive) é 86.400 segundos (24 horas). O máximo (inclusive) é 31.536.000 segundos (365 dias). |
| allow_infinite_rolling_refresh_token | Não | Se definido como `true`, o tempo de vida da janela deslizante do token de atualização nunca expira. |
| IssuanceClaimPattern | Não | Controla a declaração do emissor (iss). Um dos valores:<ul><li>AuthorityAndTenantGuid-a declaração ISS inclui seu nome de domínio, como `login.microsoftonline` ou `tenant-name.b2clogin.com` , e seu identificador de locatário https: \/ /login.microsoftonline.com/00000000-0000-0000-0000-000000000000/v2.0/</li><li>AuthorityWithTfp – A declaração de iss inclui seu nome de domínio, como `login.microsoftonline` ou `tenant-name.b2clogin.com`, seu identificador de locatário e o nome da política da terceira parte confiável. https: \/ /login.microsoftonline.com/TFP/00000000-0000-0000-0000-000000000000/b2c_1a_tp_sign-up-or-Sign-in/v2.0/</li></ul> Valor padrão: AuthorityAndTenantGuid |
| AuthenticationContextReferenceClaimPattern | Não | Controla o valor da declaração `acr`.<ul><li>Nenhum – O Azure AD B2C não emite a declaração do ACR</li><li>PolicyId – A declaração `acr` contém o nome da política</li></ul>As opções para definir esse valor são TFP (política de estrutura confiável) e ACR (referência do contexto de autenticação). É recomendável definir esse valor como TFP. Para definir o valor, verifique se o `<Item>` com o `Key="AuthenticationContextReferenceClaimPattern"` existe e o valor é `None`. Em sua política de terceira parte confiável, adicione o item `<OutputClaims>` e este elemento `<OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />`. Além disso, verifique se a sua política contém o tipo de declaração `<ClaimType Id="trustFrameworkPolicy">   <DisplayName>trustFrameworkPolicy</DisplayName>     <DataType>string</DataType> </ClaimType>` |
|RefreshTokenUserJourneyId| Não | O identificador de um percurso do usuário que deve ser executado durante a solicitação [atualizar uma](authorization-code-flow.md#4-refresh-the-token) postagem de token de acesso para o `/token` ponto de extremidade. |

## <a name="cryptographic-keys"></a>Chaves de criptografia

O elemento CryptographicKeys contém os seguintes atributos:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| issuer_secret | Sim | O certificado X509 (conjunto de chaves RSA) a ser usado para assinar o token JWT. Essa é a `B2C_1A_TokenSigningKeyContainer` chave que você configura em introdução [às políticas personalizadas](custom-policy-get-started.md). |
| issuer_refresh_token_key | Sim | O certificado X509 (conjunto de chaves RSA) a ser usado para criptografar o token de atualização. Você configurou a chave `B2C_1A_TokenEncryptionKeyContainer` em [Introdução às políticas personalizadas](custom-policy-get-started.md) |

## <a name="session-management"></a>Gerenciamento da sessão

Para configurar as sessões de Azure AD B2C entre Azure AD B2C e um aplicativo de terceira parte confiável, no atributo do `UseTechnicalProfileForSessionManagement` elemento, adicione uma referência à sessão de SSO do [OAuthSSOSessionProvider](custom-policy-reference-sso.md#oauthssosessionprovider) .














