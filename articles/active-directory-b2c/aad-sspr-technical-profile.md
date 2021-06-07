---
title: Perfis técnicos do Azure AD SSPR em políticas personalizadas
titleSuffix: Azure AD B2C
description: Referência de política personalizada para perfis técnicos do Azure AD SSPR no Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/23/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 3e6fcf956639d827a8654c5ee80e7cab8cadf930
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "85383590"
---
# <a name="define-an-azure-ad-sspr-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Definir um perfil técnico do Azure AD SSPR em uma política personalizada Azure AD B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) fornece suporte para verificar um endereço de email para redefinição de senha de autoatendimento (SSPR). Use o perfil técnico do Azure AD SSPR para gerar e enviar um código para um endereço de email e, em seguida, verificar o código. O perfil técnico do Azure AD SSPR também pode retornar uma mensagem de erro. O perfil técnico de validação valida os dados fornecido pelo usuário para que o percurso do usuário continue. Com o perfil técnico de validação, uma mensagem de erro é exibida em uma página autodeclarada.

Este perfil técnico:

- Não fornece uma interface para interagir com o usuário. Em vez disso, a interface do usuário é chamada a partir de um perfil técnico [autodeclarado](self-asserted-technical-profile.md) ou um [controle de exibição](display-controls.md) como um [perfil técnico de validação](validation-technical-profile.md).
- Usa o serviço SSPR do Azure AD para gerar e enviar um código para um endereço de email e, em seguida, verifica o código.  
- Valida um endereço de email por meio de um código de verificação.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="protocol"></a>Protocolo

O atributo **Name** do elemento **Protocol** precisa ser definido como `Proprietary`. O atributo **Handler** deve conter o nome totalmente qualificado do assembly do manipulador de protocolo que é usado pelo Azure ad B2C:

```
Web.TPEngine.Providers.AadSsprProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

O exemplo a seguir mostra um perfil técnico do Azure AD SSPR:

```XML
<TechnicalProfile Id="AadSspr-SendCode">
  <DisplayName>Send Code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AadSsprProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    ...
```

## <a name="send-email"></a>Enviar email

O primeiro modo deste perfil técnico é gerar um código e enviá-lo. As opções a seguir podem ser configuradas para esse modo.

### <a name="input-claims"></a>Declarações de entrada

O elemento **InputClaims** contém uma lista de declarações a serem enviadas ao Azure ad SSPR. Você também pode mapear o nome da sua declaração para o nome definido no perfil técnico do SSPR.

| ClaimReferenceId | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| emailAddress | Sim | O identificador do usuário que possui o endereço de email. A `PartnerClaimType` propriedade da declaração de entrada deve ser definida como `emailAddress` . |


O elemento **InputClaimsTransformations** pode conter uma coleção de elementos **InputClaimsTransformation** que são usados para modificar as declarações de entrada ou gerar novas antes de enviar para o serviço SSPR do Azure AD.

### <a name="output-claims"></a>Declarações de saída

O provedor de protocolo SSPR do Azure AD não retorna nenhum **OutputClaims**, portanto, não é necessário especificar declarações de saída. No entanto, você pode incluir declarações que não são retornadas pelo provedor de protocolo SSPR do Azure AD, desde que você defina o `DefaultValue` atributo.

O elemento **OutputClaimsTransformations** pode conter uma coleção de elementos **OutputClaimsTransformation** usados para modificar as declarações de saída ou gerar novas declarações.

### <a name="metadata"></a>Metadados

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| Operação | Sim | Deve ser **SendCode**.  |

#### <a name="ui-elements"></a>Elementos da interface do usuário

Os metadados a seguir podem ser usados para configurar as mensagens de erro exibidas no envio de falha de SMS. Os metadados devem ser configurados no perfil técnico [autodeclarado](self-asserted-technical-profile.md) . A mensagem de erro pode ser [localizada](localization-string-ids.md#azure-ad-sspr).

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| UserMessageIfInternalError | Não | Mensagem de erro do usuário se o servidor tiver encontrado um erro interno. |
| UserMessageIfThrottled| Não | Mensagem de erro do usuário se uma solicitação tiver sido limitada.|


### <a name="example-send-an-email"></a>Exemplo: enviar um email

O exemplo a seguir mostra um perfil técnico do Azure AD SSPR que é usado para enviar um código por email.

```XML
<TechnicalProfile Id="AadSspr-SendCode">
  <DisplayName>Send Code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AadSsprProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="Operation">SendCode</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="emailAddress"/>
  </InputClaims>
</TechnicalProfile>
```

## <a name="verify-code"></a>Verificar o código

O segundo modo desse perfil técnico é verificar um código. As opções a seguir podem ser configuradas para esse modo.

### <a name="input-claims"></a>Declarações de entrada

O elemento **InputClaims** contém uma lista de declarações a serem enviadas ao Azure ad SSPR. Você também pode mapear o nome da sua declaração para o nome definido no perfil técnico do SSPR.

| ClaimReferenceId | Obrigatório | Descrição |
| --------- | -------- | ----------- | ----------- |
| emailAddress| Sim | Mesmo endereço de email usado anteriormente para enviar um código. Ele também é usado para localizar uma sessão de verificação de email. A `PartnerClaimType` propriedade da declaração de entrada deve ser definida como `emailAddress` .|
| verificationCode  | Sim | O código de verificação fornecido pelo usuário a ser verificado. A `PartnerClaimType` propriedade da declaração de entrada deve ser definida como `verificationCode` . |

O elemento **InputClaimsTransformations** pode conter uma coleção de elementos **InputClaimsTransformation** que são usados para modificar as declarações de entrada ou gerar novas, antes de chamar o serviço SSPR do Azure AD.

### <a name="output-claims"></a>Declarações de saída

O provedor de protocolo SSPR do Azure AD não retorna nenhum **OutputClaims**, portanto, não é necessário especificar declarações de saída. No entanto, você pode incluir declarações que não são retornadas pelo provedor de protocolo SSPR do Azure AD, desde que você defina o `DefaultValue` atributo.

O elemento **OutputClaimsTransformations** pode conter uma coleção de elementos **OutputClaimsTransformation** usados para modificar as declarações de saída ou gerar novas declarações.

### <a name="metadata"></a>Metadados

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| Operação | Sim | Deve ser **VerifyCode** |

#### <a name="ui-elements"></a>Elementos da interface do usuário

Os metadados a seguir podem ser usados para configurar as mensagens de erro exibidas na falha de verificação de código. Os metadados devem ser configurados no perfil técnico [autodeclarado](self-asserted-technical-profile.md) . A mensagem de erro pode ser [localizada](localization-string-ids.md#azure-ad-sspr).

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
|UserMessageIfChallengeExpired | A mensagem a ser exibida para o usuário se a sessão de verificação de código tiver expirado. O código expirou ou o código nunca foi gerado para um determinado identificador.|
|UserMessageIfInternalError | Mensagem de erro do usuário se o servidor tiver encontrado um erro interno.|
|UserMessageIfThrottled | Mensagem de erro do usuário se uma solicitação tiver sido limitada.|
|UserMessageIfVerificationFailedNoRetry | A mensagem a ser exibida para o usuário se ele tiver fornecido um código inválido e o usuário não tiver permissão para fornecer o código correto.|
|UserMessageIfVerificationFailedRetryAllowed | A mensagem a ser exibida para o usuário se ele tiver fornecido um código inválido e o usuário tiver permissão para fornecer o código correto.|

### <a name="example-verify-a-code"></a>Exemplo: verificar um código

O exemplo a seguir mostra um perfil técnico do Azure AD SSPR usado para verificar o código.

```XML
<TechnicalProfile Id="AadSspr-VerifyCode">
  <DisplayName>Verify Code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AadSsprProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="Operation">VerifyCode</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="verificationCode" PartnerClaimType="verificationCode" />
    <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="emailAddress"/>
  </InputClaims>
</TechnicalProfile>
```