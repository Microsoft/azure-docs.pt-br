---
title: Referência de controle de exibição
titleSuffix: Azure AD B2C
description: Referência para Azure AD B2C controles de exibição. Use controles de exibição para personalizar as viagens do usuário definidas em suas políticas personalizadas.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 441a77823c77305e567e9e1436715bc51ca48c11
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97387047"
---
# <a name="display-controls"></a>Controles de exibição

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Um **controle de exibição** é um elemento de interface do usuário que tem funcionalidade especial e interage com o serviço de back-end Azure Active Directory B2C (Azure ad B2C). Ele permite que o usuário execute ações na página que invocam um [perfil técnico de validação](validation-technical-profile.md) no back-end. Os controles de exibição são exibidos na página e são referenciados por um [perfil técnico autodeclarado](self-asserted-technical-profile.md).

A imagem a seguir ilustra uma página de inscrição autodeclarada com dois controles de exibição que validam um endereço de email primário e secundário.

![Exemplo de controle de exibição renderizado](media/display-controls/display-control-email.png)

## <a name="prerequisites"></a>Pré-requisitos

 Na seção de [metadados](self-asserted-technical-profile.md#metadata) de um [perfil técnico autodeclarado](self-asserted-technical-profile.md), o [ContentDefinition](contentdefinitions.md) referenciado precisa ter `DataUri` definido como a versão do contrato de página 2.0.0 ou superior. Por exemplo:

```xml
<ContentDefinition Id="api.selfasserted">
  <LoadUri>~/tenant/default/selfAsserted.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:selfasserted:2.0.0</DataUri>
  ...
```

## <a name="defining-display-controls"></a>Definindo controles de exibição

O elemento **DisplayControl** contém os seguintes atributos:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| ID | Sim | Um identificador que é usado para o controle de exibição. Ele pode ser [referenciado](#referencing-display-controls). |
| UserInterfaceControlType | Sim | O tipo do controle de exibição. Atualmente, há suporte para [VerificationControl](display-control-verification.md) |

O elemento **DisplayControl** contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| InputClaims | 0:1 | **InputClaims** são usados para preencher previamente o valor das declarações a serem coletadas do usuário. Para obter mais informações, consulte elemento [InputClaims](technicalprofiles.md#input-claims) . |
| DisplayClaims | 0:1 | **DisplayClaims** são usados para representar as declarações a serem coletadas do usuário. Para obter mais informações, consulte elemento [DisplayClaim](technicalprofiles.md#displayclaim) .|
| OutputClaims | 0:1 | **OutputClaims** são usados para representar as declarações a serem salvas temporariamente para este **DisplayControl**. Para obter mais informações, consulte elemento [OutputClaims](technicalprofiles.md#output-claims) .|
| Ações | 0:1 | As **ações** são usadas para listar os perfis técnicos de validação a serem invocados para ações do usuário que ocorrem no front-end. |

### <a name="input-claims"></a>Declarações de entrada

Em um controle de exibição, você pode usar elementos **InputClaims** para preencher previamente o valor das declarações a serem coletadas do usuário na página. Qualquer **InputClaimsTransformations** pode ser definido no perfil técnico autodeclarado que faz referência a esse controle de exibição.

O exemplo a seguir popula o endereço de email a ser verificado com o endereço já presente.

```xml
<DisplayControl Id="emailControl" UserInterfaceControlType="VerificationControl">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="emailAddress" />
  </InputClaims>
  ...
```

### <a name="display-claims"></a>Exibir declarações

Cada tipo de controle de exibição requer um conjunto diferente de declarações de exibição, [declarações de saída](#output-claims)e [ações](#display-control-actions) a serem executadas.

Semelhante às **declarações de exibição** definidas em um [perfil técnico autodeclarado](self-asserted-technical-profile.md#display-claims), as declarações de exibição representam as declarações a serem coletadas do usuário dentro do controle de exibição. O elemento **ClaimType** referenciado precisa especificar o elemento **userinputtype** para um tipo de entrada de usuário com suporte pelo Azure ad B2C, como `TextBox` ou `DropdownSingleSelect` . Se um valor de declaração de exibição for exigido por uma **ação**, defina o atributo **Required** como `true` para forçar o usuário a fornecer um valor para essa declaração de exibição específica.

Determinadas declarações de exibição são necessárias para determinados tipos de controle de exibição. Por exemplo, **VerificationCode** é necessário para o controle de exibição do tipo **VerificationControl**. Use o atributo **ControlClaimType** para especificar qual DisplayClaim é designado para essa declaração necessária. Por exemplo:

```xml
<DisplayClaim ClaimTypeReferenceId="otpCode" ControlClaimType="VerificationCode" Required="true" />
```

### <a name="output-claims"></a>Declarações de saída

As **declarações de saída** de um controle de exibição não são enviadas para a próxima etapa de orquestração. Eles são salvos temporariamente apenas para a sessão de controle de exibição atual. Essas declarações temporárias podem ser compartilhadas entre as diferentes ações do mesmo controle de exibição.

Para emergir as declarações de saída para a próxima etapa de orquestração, use o **OutputClaims** do perfil técnico autodeclarado real que faz referência a esse controle de exibição.

### <a name="display-control-actions"></a>Exibir ações de controle

As **ações** de um controle de exibição são procedimentos que ocorrem no back-end de Azure ad B2C quando um usuário executa uma determinada ação no lado do cliente (o navegador). Por exemplo, as validações a serem executadas quando o usuário seleciona um botão na página.

Uma ação define uma lista de **perfis técnicos de validação**. Eles são usados para validar algumas ou todas as declarações de exibição do controle de exibição. O perfil técnico de validação valida a entrada do usuário e pode retornar um erro para o usuário. Você pode usar **ContinueOnError**, **ContinueOnSuccess** e **pré-condições** na ação de controle de exibição semelhante à forma como são usados em [perfis técnicos de validação](validation-technical-profile.md) em um perfil técnico autodeclarado.

#### <a name="actions"></a>Ações

O elemento **Actions** contém o seguinte elemento:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| Ação | 1:n | Lista de ações a serem executadas. |

#### <a name="action"></a>Ação

O elemento **Action** contém o seguinte atributo:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| ID | Sim | O tipo de operação. Valores possíveis: `SendCode` ou `VerifyCode`. O `SendCode` valor envia um código para o usuário. Essa ação pode conter dois perfis técnicos de validação: um para gerar um código e outro para enviá-lo. O `VerifyCode` valor verifica o código que o usuário digitou na caixa de texto de entrada. |

O elemento **Action** contém o seguinte elemento:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| ValidationClaimsExchange | 1:1 | Os identificadores de perfis técnicos que são usados para validar algumas ou todas as declarações de exibição do perfil técnico de referência. Todas as declarações de entrada do perfil técnico referenciado devem aparecer nas declarações de exibição do perfil técnico de referência. |

#### <a name="validationclaimsexchange"></a>ValidationClaimsExchange

O elemento **ValidationClaimsExchange** contém o seguinte elemento:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| ValidationTechnicalProfile | 1:n | Um perfil técnico a ser usado para validar algumas ou todas as declarações de exibição do perfil técnico de referência. |

O elemento **ValidationTechnicalProfile** contém os seguintes atributos:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| ReferenceId | Sim | Um identificador de um perfil técnico já definido na política ou política pai. |
|ContinueOnError|Não| Indica se a validação de quaisquer perfis técnicos de validação subsequentes deve continuar se esse perfil técnico de validação gerar um erro. Valores possíveis: `true` ou `false` (padrão, o processamento de perfis de validação adicionais será interrompido e um erro será retornado). |
|ContinueOnSuccess | Não | Indica se a validação de quaisquer perfis de validação subsequentes deve continuar se esse perfil técnico de validação tiver sucesso. Valores possíveis: `true` ou `false`. O padrão é `true`, significando que continuará o processamento dos perfis de validação adicional. |

O elemento **ValidationTechnicalProfile** contém o seguinte elemento:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| Pré-condições | 0:1 | Uma lista de pré-condições deve ser atendida para o perfil técnico de validação para executar. |

O elemento de **pré-condição** contém os seguintes atributos:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| `Type` | Sim | O tipo de verificação ou consulta ser executada para a pré-condição. Valores possíveis: `ClaimsExist` ou `ClaimEquals`. `ClaimsExist` Especifica que as ações devem ser executadas se as declarações especificadas existirem no conjunto de declarações atual do usuário. `ClaimEquals` Especifica que as ações devem ser executadas se a declaração especificada existir e seu valor for igual ao valor especificado. |
| `ExecuteActionsIf` | Sim | Indica se as ações na pré-condição devem ser executadas se o teste for verdadeiro ou falso. |

O elemento **Precondition** contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| Valor | 1:n | Os dados que são usados pela verificação. Se o tipo dessa verificação for `ClaimsExist`, este campo especifica um ClaimTypeReferenceId para consultar. Se o tipo dessa verificação for `ClaimEquals`, este campo especifica um ClaimTypeReferenceId para consultar. Especifique o valor a ser verificado em outro elemento de valor.|
| Ação | 1:1 | A ação que deverá ser executada se a verificação de pré-condição dentro de uma etapa de orquestração for verdadeira. O valor da **ação** é definido como `SkipThisValidationTechnicalProfile` , que especifica que o perfil técnico de validação associado não deve ser executado. |

O exemplo a seguir envia e verifica o endereço de email usando o [perfil técnico do Azure ad SSPR](aad-sspr-technical-profile.md).

```xml
<DisplayControl Id="emailVerificationControl" UserInterfaceControlType="VerificationControl">
  <InputClaims></InputClaims>
  <DisplayClaims>
    <DisplayClaim ClaimTypeReferenceId="email" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="verificationCode" ControlClaimType="VerificationCode" Required="true" />
  </DisplayClaims>
  <OutputClaims></OutputClaims>
  <Actions>
    <Action Id="SendCode">
      <ValidationClaimsExchange>
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="AadSspr-SendCode" />
      </ValidationClaimsExchange>
    </Action>
    <Action Id="VerifyCode">
      <ValidationClaimsExchange>
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="AadSspr-VerifyCode" />
      </ValidationClaimsExchange>
    </Action>
  </Actions>
</DisplayControl>
```

O exemplo a seguir envia um código no email ou SMS com base na seleção do usuário da Declaração **mfaType** com pré-condições.

```xml
<Action Id="SendCode">
  <ValidationClaimsExchange>
    <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="AzureMfa-SendSms">
      <Preconditions>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
          <Value>mfaType</Value>
          <Value>email</Value>
          <Action>SkipThisValidationTechnicalProfile</Action>
        </Precondition>
      </Preconditions>
    </ValidationClaimsExchangeTechnicalProfile>
    <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="AadSspr-SendEmail">
      <Preconditions>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
          <Value>mfaType</Value>
          <Value>phone</Value>
          <Action>SkipThisValidationTechnicalProfile</Action>
        </Precondition>
      </Preconditions>
    </ValidationClaimsExchangeTechnicalProfile>
  </ValidationClaimsExchange>
</Action>
```

## <a name="referencing-display-controls"></a>Referenciando controles de exibição

Os controles de exibição são referenciados nas [declarações de exibição](self-asserted-technical-profile.md#display-claims) do [perfil técnico autodeclarado](self-asserted-technical-profile.md).

Por exemplo:

```xml
<TechnicalProfile Id="SelfAsserted-ProfileUpdate">
  ...
  <DisplayClaims>
    <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
    <DisplayClaim DisplayControlReferenceId="PhoneVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
```

## <a name="next-steps"></a>Próximas etapas

Para obter exemplos de como usar o controle de exibição, consulte: 

- [Verificação de email personalizada com Mailjet](custom-email-mailjet.md)
- [Verificação de email personalizada com SendGrid](custom-email-sendgrid.md)
