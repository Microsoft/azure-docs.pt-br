---
title: Perfis técnicos do Azure AD MFA em políticas personalizadas
titleSuffix: Azure AD B2C
description: Referência de política personalizada para perfis técnicos da MFA (autenticação multifator) do Azure AD no Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e81ac35555e6653cecb602e5af2f19aa3e2f05e9
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/18/2020
ms.locfileid: "94840586"
---
# <a name="define-an-azure-ad-mfa-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Definir um perfil técnico do Azure AD MFA em uma política personalizada Azure AD B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) fornece suporte para verificar um número de telefone usando a MFA (autenticação multifator) do Azure AD. Use este perfil técnico para gerar e enviar um código para um número de telefone e, em seguida, verificar o código. O perfil técnico do Azure AD MFA também pode retornar uma mensagem de erro.  O perfil técnico de validação valida os dados fornecido pelo usuário para que o percurso do usuário continue. Com o perfil técnico de validação, uma mensagem de erro é exibida em uma página autodeclarada.

Este perfil técnico:

- Não fornece uma interface para interagir com o usuário. Em vez disso, a interface do usuário é chamada a partir de um perfil técnico [autodeclarado](self-asserted-technical-profile.md) ou um [controle de exibição](display-controls.md) como um [perfil técnico de validação](validation-technical-profile.md).
- Usa o serviço do Azure AD MFA para gerar e enviar um código para um número de telefone e, em seguida, verifica o código.  
- Valida um número de telefone por meio de mensagens de texto.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="protocol"></a>Protocolo

O atributo **Name** do elemento **Protocol** precisa ser definido como `Proprietary`. O atributo **Handler** deve conter o nome totalmente qualificado do assembly do manipulador de protocolo que é usado pelo Azure ad B2C:

```
Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

O exemplo a seguir mostra um perfil técnico do Azure AD MFA:

```xml
<TechnicalProfile Id="AzureMfa-SendSms">
    <DisplayName>Send Sms</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    ...
```

## <a name="send-sms"></a>Enviar SMS

O primeiro modo deste perfil técnico é gerar um código e enviá-lo. As opções a seguir podem ser configuradas para esse modo.

### <a name="input-claims"></a>Declarações de entrada

O elemento **InputClaims** contém uma lista de declarações a serem enviadas para o Azure ad MFA. Você também pode mapear o nome da sua declaração para o nome definido no perfil técnico do MFA.

| ClaimReferenceId | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| userPrincipalName | Sim | O identificador do usuário que possui o número de telefone. |
| phoneNumber | Sim | O número de telefone para o qual enviar um código SMS. |
| companyName | Não |O nome da empresa no SMS. Se não for fornecido, o nome do seu aplicativo será usado. |
| localidade | Não | A localidade do SMS. Se não for fornecido, a localidade do navegador do usuário será usada. |

O elemento **InputClaimsTransformations** pode conter uma coleção de elementos **InputClaimsTransformation** que são usados para modificar as declarações de entrada ou gerar novas antes de enviar para o serviço de MFA do Azure AD.

### <a name="output-claims"></a>Declarações de saída

O provedor de protocolo MFA do Azure AD não retorna nenhum **OutputClaims**, portanto, não é necessário especificar declarações de saída. No entanto, você pode incluir declarações que não são retornadas pelo provedor de identidade do Azure AD MFA contanto que você defina o `DefaultValue` atributo.

O elemento **OutputClaimsTransformations** pode conter uma coleção de elementos **OutputClaimsTransformation** usados para modificar as declarações de saída ou gerar novas declarações.

### <a name="metadata"></a>Metadados

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| Operação | Sim | Deve ser **OneWaySMS**.  |

#### <a name="ui-elements"></a>Elementos da interface do usuário

Os metadados a seguir podem ser usados para configurar as mensagens de erro exibidas no envio de falha de SMS. Os metadados devem ser configurados no perfil técnico [autodeclarado](self-asserted-technical-profile.md) . A mensagem de erro pode ser [localizada](localization-string-ids.md#azure-ad-mfa-error-messages).

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| UserMessageIfCouldntSendSms | Não | Mensagem de erro do usuário se o número de telefone fornecido não aceitar SMS. |
| UserMessageIfInvalidFormat | Não | Mensagem de erro do usuário se o número de telefone fornecido não for um número de telefone válido. |
| UserMessageIfServerError | Não | Mensagem de erro do usuário se o servidor tiver encontrado um erro interno. |
| UserMessageIfThrottled| Não | Mensagem de erro do usuário se uma solicitação tiver sido limitada.|

### <a name="example-send-an-sms"></a>Exemplo: enviar um SMS

O exemplo a seguir mostra um perfil técnico do Azure AD MFA que é usado para enviar um código via SMS.

```xml
<TechnicalProfile Id="AzureMfa-SendSms">
  <DisplayName>Send Sms</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="Operation">OneWaySMS</Item>
  </Metadata>
  <InputClaimsTransformations>
    <InputClaimsTransformation ReferenceId="CombinePhoneAndCountryCode" />
    <InputClaimsTransformation ReferenceId="ConvertStringToPhoneNumber" />
  </InputClaimsTransformations>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userPrincipalName" />
    <InputClaim ClaimTypeReferenceId="fullPhoneNumber" PartnerClaimType="phoneNumber" />
  </InputClaims>
</TechnicalProfile>
```

## <a name="verify-code"></a>Verificar o código

O segundo modo desse perfil técnico é verificar um código. As opções a seguir podem ser configuradas para esse modo.

### <a name="input-claims"></a>Declarações de entrada

O elemento **InputClaims** contém uma lista de declarações a serem enviadas para o Azure ad MFA. Você também pode mapear o nome da sua declaração para o nome definido no perfil técnico do MFA.

| ClaimReferenceId | Obrigatório | Descrição |
| --------- | -------- | ----------- | ----------- |
| phoneNumber| Sim | Mesmo número de telefone usado anteriormente para enviar um código. Ele também é usado para localizar uma sessão de verificação de telefone. |
| verificationCode  | Sim | O código de verificação fornecido pelo usuário a ser verificado |

O elemento **InputClaimsTransformations** pode conter uma coleção de elementos **InputClaimsTransformation** que são usados para modificar as declarações de entrada ou gerar novas antes de chamar o serviço de MFA do Azure AD.

### <a name="output-claims"></a>Declarações de saída

O provedor de protocolo MFA do Azure AD não retorna nenhum **OutputClaims**, portanto, não é necessário especificar declarações de saída. No entanto, você pode incluir declarações que não são retornadas pelo provedor de identidade do Azure AD MFA contanto que você defina o `DefaultValue` atributo.

O elemento **OutputClaimsTransformations** pode conter uma coleção de elementos **OutputClaimsTransformation** usados para modificar as declarações de saída ou gerar novas declarações.

### <a name="metadata"></a>Metadados

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| Operação | Sim | Deve ser **verificar** |

#### <a name="ui-elements"></a>Elementos da interface do usuário

Os metadados a seguir podem ser usados para configurar as mensagens de erro exibidas na falha de verificação de código. Os metadados devem ser configurados no perfil técnico [autodeclarado](self-asserted-technical-profile.md) . A mensagem de erro pode ser [localizada](localization-string-ids.md#azure-ad-mfa-error-messages).

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| UserMessageIfMaxAllowedCodeRetryReached| Não | Mensagem de erro do usuário se o usuário tiver tentado um código de verificação muitas vezes. |
| UserMessageIfServerError | Não | Mensagem de erro do usuário se o servidor tiver encontrado um erro interno. |
| UserMessageIfThrottled| Não | Mensagem de erro do usuário se a solicitação for limitada.|
| UserMessageIfWrongCodeEntered| Não| Mensagem de erro do usuário se o código digitado para verificação estiver errado.|

### <a name="example-verify-a-code"></a>Exemplo: verificar um código

O exemplo a seguir mostra um perfil técnico do Azure AD MFA usado para verificar o código.

```xml
<TechnicalProfile Id="AzureMfa-VerifySms">
    <DisplayName>Verify Sms</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <Metadata>
        <Item Key="Operation">Verify</Item>
    </Metadata>
    <InputClaims>
        <InputClaim ClaimTypeReferenceId="phoneNumber" PartnerClaimType="phoneNumber" />
        <InputClaim ClaimTypeReferenceId="verificationCode" />
    </InputClaims>
</TechnicalProfile>
```
