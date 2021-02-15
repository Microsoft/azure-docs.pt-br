---
title: Habilitar a verificação de OTP (senha de uso único)
titleSuffix: Azure AD B2C
description: Saiba como configurar um cenário de OTP (senha de uso único) usando Azure AD B2C políticas personalizadas.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/19/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 12b9639342e2e35b9229aa15bb9cfb4695427606
ms.sourcegitcommit: 6d6030de2d776f3d5fb89f68aaead148c05837e2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/05/2021
ms.locfileid: "97881184"
---
# <a name="define-a-one-time-password-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Definir um perfil técnico de senha de uso único em uma política personalizada de Azure AD B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O Azure Active Directory B2C (Azure AD B2C) fornece suporte para gerenciar a geração e a verificação de uma senha de uso único. Use um perfil técnico para gerar um código e, em seguida, verifique esse código posteriormente.

O perfil técnico de senha de uso único também pode retornar uma mensagem de erro durante a verificação de código. Projete a integração com a senha de uso único usando um **perfil técnico de validação**. Um perfil técnico de validação chama o perfil técnico de senha de uso único para verificar um código. O perfil técnico de validação valida os dados fornecido pelo usuário para que o percurso do usuário continue. Com o perfil técnico de validação, uma mensagem de erro é exibida em uma página autodeclarada.

## <a name="protocol"></a>Protocolo

O atributo **Name** do elemento **Protocol** precisa ser definido como `Proprietary`. O atributo **Handler** deve conter o nome totalmente qualificado do assembly do manipulador de protocolo que é usado pelo Azure ad B2C:

```xml
Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

O exemplo a seguir mostra um perfil técnico de senha de uso único:

```xml
<TechnicalProfile Id="VerifyCode">
  <DisplayName>Validate user input verification code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
```

## <a name="generate-code"></a>Gerar código

O primeiro modo deste perfil técnico é gerar um código. Abaixo estão as opções que podem ser configuradas para esse modo. Os códigos gerados e as tentativas são controlados na sessão. 

### <a name="input-claims"></a>Declarações de entrada

O elemento **InputClaims** contém uma lista de declarações necessárias para enviar para o provedor de protocolo de senha de uso único. Você também pode mapear o nome da sua declaração para o nome definido abaixo.

| ClaimReferenceId | Necessária | Descrição |
| --------- | -------- | ----------- |
| identificador | Sim | O identificador para identificar o usuário que precisa verificar o código posteriormente. Normalmente, ele é usado como o identificador do destino para o qual o código é entregue, por exemplo, endereço de email ou número de telefone. |

O elemento **InputClaimsTransformations** pode conter uma coleção de elementos **InputClaimsTransformation** que são usados para modificar as declarações de entrada ou gerar novas antes de enviar para o provedor de protocolo de senha de uso único.

### <a name="output-claims"></a>Declarações de saída

O elemento **OutputClaims** contém uma lista de declarações geradas pelo provedor de protocolo de senha de uso único. Você também pode mapear o nome da sua declaração para o nome definido abaixo.

| ClaimReferenceId | Necessária | Descrição |
| --------- | -------- | ----------- |
| otpGenerated | Sim | O código gerado cuja sessão é gerenciada pelo Azure AD B2C. |

O elemento **OutputClaimsTransformations** pode conter uma coleção de elementos **OutputClaimsTransformation** usados para modificar as declarações de saída ou gerar novas declarações.

### <a name="metadata"></a>Metadados

As configurações a seguir podem ser usadas para configurar o modo de geração de código:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| CodeExpirationInSeconds | Não | Tempo em segundos até a expiração do código. Mínimo: `60` ; Máximo: `1200` ; Padrão: `600` . Toda vez que um código é fornecido (mesmo código usando `ReuseSameCode` ou um novo código), a expiração do código é estendida. Esse tempo também é usado para definir o tempo limite de repetição (depois que as tentativas máximas forem atingidas, o usuário será bloqueado da tentativa de obter novos códigos até que esse tempo expire) |
| CodeLength | Não | Comprimento do código. O valor padrão é `6`. |
| CharacterSet | Não | O conjunto de caracteres para o código formatado para uso em uma expressão regular. Por exemplo, `a-z0-9A-Z`. O valor padrão é `0-9`. O conjunto de caracteres deve incluir um mínimo de 10 caracteres diferentes no conjunto especificado. |
| NumRetryAttempts | Não | O número de tentativas de verificação antes de o código ser considerado inválido. O valor padrão é `5`. |
| NumCodeGenerationAttempts | Não | O número de tentativas de geração de código máximo por identificador. O valor padrão é 10, se não especificado. |
| Operação | Sim | A operação a ser executada. Valor possível: `GenerateCode` . |
| ReuseSameCode | Não | Se o mesmo código deve ser fornecido em vez de gerar um novo código quando determinado código não tiver expirado e ainda for válido. O valor padrão é `false`.  |



### <a name="example"></a>Exemplo

O exemplo a seguir `TechnicalProfile` é usado para gerar um código:

```xml
<TechnicalProfile Id="GenerateCode">
  <DisplayName>Generate Code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="Operation">GenerateCode</Item>
    <Item Key="CodeExpirationInSeconds">600</Item>
    <Item Key="CodeLength">6</Item>
    <Item Key="CharacterSet">0-9</Item>
    <Item Key="NumRetryAttempts">5</Item>
    <Item Key="NumCodeGenerationAttempts">15</Item>
    <Item Key="ReuseSameCode">false</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="identifier" PartnerClaimType="identifier" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="otpGenerated" PartnerClaimType="otpGenerated" />
  </OutputClaims>
</TechnicalProfile>
```

## <a name="verify-code"></a>Verificar o código

O segundo modo desse perfil técnico é verificar um código. Abaixo estão as opções que podem ser configuradas para esse modo.

### <a name="input-claims"></a>Declarações de entrada

O elemento **InputClaims** contém uma lista de declarações necessárias para enviar para o provedor de protocolo de senha de uso único. Você também pode mapear o nome da sua declaração para o nome definido abaixo.

| ClaimReferenceId | Necessária | Descrição |
| --------- | -------- | ----------- |
| identificador | Sim | O identificador para identificar o usuário que gerou um código anteriormente. Normalmente, ele é usado como o identificador do destino para o qual o código é entregue, por exemplo, endereço de email ou número de telefone. |
| otpToVerify | Sim | O código de verificação fornecido pelo usuário. |

O elemento **InputClaimsTransformations** pode conter uma coleção de elementos **InputClaimsTransformation** que são usados para modificar as declarações de entrada ou gerar novas antes de enviar para o provedor de protocolo de senha de uso único.

### <a name="output-claims"></a>Declarações de saída

Não há declarações de saída fornecidas durante a verificação de código desse provedor de protocolo.

O elemento **OutputClaimsTransformations** pode conter uma coleção de elementos **OutputClaimsTransformation** usados para modificar as declarações de saída ou gerar novas declarações.

### <a name="metadata"></a>Metadados

As configurações a seguir podem ser usadas para o modo de verificação de código:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| Operação | Sim | A operação a ser executada. Valor possível: `VerifyCode` . |


### <a name="ui-elements"></a>Elementos da interface do usuário

Os metadados a seguir podem ser usados para configurar as mensagens de erro exibidas na falha de verificação de código. Os metadados devem ser configurados no perfil técnico [autodeclarado](self-asserted-technical-profile.md) . A mensagem de erro pode ser [localizada](localization-string-ids.md#one-time-password-error-messages).

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| UserMessageIfSessionDoesNotExist | Não | A mensagem a ser exibida para o usuário se a sessão de verificação de código tiver expirado. O código expirou ou o código nunca foi gerado para um determinado identificador. |
| UserMessageIfMaxRetryAttempted | Não | A mensagem a ser exibida para o usuário se ele tiver excedido o máximo de tentativas de verificação permitidas. |
| UserMessageIfMaxNumberOfCodeGenerated | Não | A mensagem a ser exibida para o usuário se a geração de código exceder o número máximo permitido de tentativas. |
| UserMessageIfInvalidCode | Não | A mensagem a ser exibida para o usuário se ele tiver fornecido um código inválido. |
| UserMessageIfVerificationFailedRetryAllowed | Não | A mensagem a ser exibida para o usuário se ele tiver fornecido um código inválido e o usuário tiver permissão para fornecer o código correto.  |
|UserMessageIfSessionConflict|Não| A mensagem a ser exibida para o usuário se o código não puder ser verificado.|

### <a name="example"></a>Exemplo

O exemplo a seguir `TechnicalProfile` é usado para verificar um código:

```xml
<TechnicalProfile Id="VerifyCode">
  <DisplayName>Verify Code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="Operation">VerifyCode</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="identifier" PartnerClaimType="identifier" />
    <InputClaim ClaimTypeReferenceId="otpGenerated" PartnerClaimType="otpToVerify" />
  </InputClaims>
</TechnicalProfile>
```

## <a name="next-steps"></a>Próximas etapas

Consulte o seguinte artigo para obter um exemplo de como usar o perfil técnico de senha de uso único com a verificação de email personalizada:

- Verificação de email personalizada no Azure Active Directory B2C ([Mailjet](custom-email-mailjet.md), [SendGrid](custom-email-sendgrid.md))

