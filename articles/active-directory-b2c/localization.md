---
title: Azure Active Directory B2C de localização
description: Especifica o elemento Localization de uma política personalizada no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/08/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 3a5afcd8c0ef0c31353cd2369ead332675c9877f
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102453114"
---
# <a name="localization-element"></a>Elemento de localização

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O elemento **Localization** permite que você dê suporte a várias localidades ou idiomas na política para os percursos do usuário. O suporte de localização em políticas permite que você:

- Configure a lista explícita de idiomas com suporte em uma política e escolha um idioma padrão.
- Forneça coleções e cadeias de caracteres específicas a um idioma.

```xml
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="ReplaceAll">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
  </SupportedLanguages>
  <LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedResources Id="api.localaccountsignup.es">
  ...
```

O elemento **Localization** contém os seguintes atributos:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| habilitado | Não | Valores possíveis: `true` ou `false`. |

O elemento **Localization** contém os seguintes elementos XML

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| SupportedLanguages | 1:n | Lista de idiomas com suporte. |
| LocalizedResources | 0:n | Lista de recursos localizados. |

## <a name="supportedlanguages"></a>SupportedLanguages

O elemento **SupportedLanguages** contém os seguintes atributos:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| DefaultLanguage | Sim | O idioma a ser usado como o padrão para recursos localizados. |
| MergeBehavior | Não | Valores de enumeração de valores mesclados junto com qualquer ClaimType presente em uma política pai com o mesmo identificador. Use esse atributo quando substituir uma declaração especificada na política de base. Valores possíveis: `Append`, `Prepend` ou `ReplaceAll`. O valor `Append` especifica que a coleta de dados presente deve ser acrescentada ao final da coleção especificada na política pai. O valor `Prepend` especifica que a coleta de dados presente deve ser adicionada antes da coleção especificada na política pai. O valor `ReplaceAll` especifica que a coleta de dados definida na política pai deve ser ignorada, em vez disso, usando, em vez disso, os dados definidos na política atual. |

### <a name="supportedlanguages"></a>SupportedLanguages

O elemento **SupportedLanguages** contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| SupportedLanguage | 1:n | Exibe o conteúdo que está em conformidade com uma marca de idioma conforme RFC 5646 – Marcas para Identificar Idiomas. |

## <a name="localizedresources"></a>LocalizedResources

O elemento **LocalizedResources** contém os seguintes atributos:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| ID | Sim | Um identificador usado para identificar exclusivamente os recursos localizados. |

O elemento **LocalizedResources** contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| LocalizedCollections | 0:n | Define coleções inteiras em várias culturas. Uma coleção pode ter um número diferente de itens e diferentes cadeias de caracteres para várias culturas. Exemplos de coleções incluem enumerações que aparecem em tipos de declaração. Por exemplo, é mostrada uma lista de país/região para o usuário em uma lista suspensa. |
| LocalizedStrings | 0:n | Define todas as cadeias de caracteres, exceto pelas cadeias de caracteres exibidas em coleções, em várias culturas. |

### <a name="localizedcollections"></a>LocalizedCollections

O elemento **LocalizedCollections** contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| LocalizedCollection | 1:n | Lista de idiomas com suporte. |

#### <a name="localizedcollection"></a>LocalizedCollection

O elemento **LocalizedCollection** contém os seguintes atributos:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| ElementType | Sim | Faz referência a um elemento ClaimType ou um elemento de interface do usuário no arquivo de política. |
| ElementId | Sim | Uma cadeia de caracteres que contém uma referência a um tipo de declaração já definido na seção ClaimsSchema usada se **ElementType** está definido como um ClaimType. |
| TargetCollection | Sim | A coleção de destino. |

O elemento **LocalizedCollection** contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| Item | 0:n | Define uma opção disponível para o usuário selecionar para uma declaração de interface do usuário, como um valor em uma lista suspensa. |

O elemento **Item** contém os seguintes atributos:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| Texto | Sim | A cadeia de caracteres de exibição amigável deve ser mostrada ao usuário na interface do usuário para essa opção. |
| Valor | Sim | O valor da declaração da cadeia de caracteres associada com essa opção. |
| SelectByDefault | Não | Indica se esta opção deve ser selecionada ou não por padrão na interface do usuário. Os valores possíveis: True ou False. |

O exemplo a seguir mostra o uso do elemento **LocalizedCollections**. Ele contém dois elementos **LocalizedCollection**, um para inglês e outro para espanhol. Ambos definem a coleção de **Restrição** da declaração `Gender` com uma lista de itens para inglês e espanhol.

```xml
<LocalizedResources Id="api.selfasserted.en">
 <LocalizedCollections>
   <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
      <Item Text="Female" Value="F" />
      <Item Text="Male" Value="M" />
    </LocalizedCollection>
</LocalizedCollections>

<LocalizedResources Id="api.selfasserted.es">
 <LocalizedCollections>
   <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
      <Item Text="Femenino" Value="F" />
      <Item Text="Masculino" Value="M" />
    </LocalizedCollection>
</LocalizedCollections>
```

### <a name="localizedstrings"></a>LocalizedStrings

O elemento **LocalizedStrings** contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| LocalizedString | 1:n | Uma cadeia de caracteres localizada. |

O elemento **LocalizedString** contém os seguintes atributos:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| ElementType | Sim | Valores possíveis: [claimproperties](#claimsprovider), [ClaimType](#claimtype), [ErrorMessage](#errormessage), [GetLocalizedStringsTransformationClaimType](#getlocalizedstringstransformationclaimtype), [FormatLocalizedStringTransformationClaimType](#formatlocalizedstringtransformationclaimtype), [predicado](#predicate), [InputValidation](#inputvalidation)ou [UxElement](#uxelement).   | 
| ElementId | Sim | Se **ElementType** for definido como `ClaimType` , `Predicate` ou `InputValidation` , esse elemento conterá uma referência a um tipo de declaração já definido na seção ClaimsSchema. |
| StringId | Sim | Se **ElementType** for definido como `ClaimType`, esse elemento conterá uma referência a um atributo de um tipo de declaração. Valores possíveis: `DisplayName`, `AdminHelpText` ou `PatternHelpText`. O valor `DisplayName` é usado para definir o nome de exibição de declaração. O valor `AdminHelpText` é usado para definir o nome de texto de ajuda do usuário de declaração. O valor `PatternHelpText` é usado para definir o texto de ajuda do padrão de declaração. Se **ElementType** for definido como `UxElement`, esse elemento conterá uma referência a um atributo de um elemento de interface do usuário. Se **ElementType** for definido como `ErrorMessage`, esse elemento especificará o identificador de uma mensagem de erro. Veja [IDs de cadeia de localização](localization-string-ids.md) para obter uma lista completa de identificadores `UxElement`.|

## <a name="elementtype"></a>ElementType

A referência ElementType para um tipo de declaração, uma transformação de declaração ou um elemento de interface do usuário na política a ser localizado.

| Elemento a ser localizado | ElementType | ElementId |StringId |
| --------- | -------- | ----------- |----------- |
| Nome do provedor de identidade |`ClaimsProvider`| | A ID do elemento ClaimsExchange|
| Atributos de tipo de declaração|`ClaimType`|Nome do tipo de declaração| O atributo da declaração a ser localizada. Valores possíveis: `AdminHelpText` , `DisplayName` , `PatternHelpText` e `UserHelpText` .|
|Mensagem de erro|`ErrorMessage`||A ID da mensagem de erro |
|Copia cadeias de caracteres localizadas em declarações|`GetLocalizedStringsTra nsformationClaimType`||O nome da declaração de saída|
|Mensagem de usuário de predicado|`Predicate`|O nome do predicado| O atributo do predicado a ser localizado. Valores possíveis: `HelpText` .|
|Mensagem de usuário do grupo de predicado|`InputValidation`|A ID do elemento PredicateValidation.|A ID do elemento de predicado. O grupo de predicado deve ser um filho do elemento de validação de predicado, conforme definido no ElementID.|
|Elementos da interface do usuário |`UxElement` | | A ID do elemento de interface do usuário a ser localizado.|
|[Controle de exibição](display-controls.md) |`DisplayControl` |A ID do controle de exibição. | A ID do elemento de interface do usuário a ser localizado.|

## <a name="examples"></a>Exemplos

### <a name="claimsprovider"></a>ClaimsProvider

O valor de Claims é usado para localizar um dos provedores de declaração nome de exibição. 

```xml
<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
    <ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAUTH" />
  </ClaimsExchanges>
</OrchestrationStep>

```

O exemplo a seguir mostra como localizar o nome de exibição dos provedores de declarações.

```xml
<LocalizedString ElementType="ClaimsProvider" StringId="FacebookExchange">Facebook</LocalizedString>
<LocalizedString ElementType="ClaimsProvider" StringId="GoogleExchange">Google</LocalizedString>
<LocalizedString ElementType="ClaimsProvider" StringId="LinkedInExchange">LinkedIn</LocalizedString>
```

### <a name="claimtype"></a>ClaimType

O valor ClaimType é usado para localizar um dos atributos de declaração. 

```xml
<ClaimType Id="email">
  <DisplayName>Email Address</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Email address that can be used to contact you.</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```

O exemplo a seguir mostra como localizar os atributos DisplayName, userhelptext e PatternHelpText do tipo de declaração de email.

```xml
<LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Email</LocalizedString>
<LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Please enter your email</LocalizedString>
<LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Please enter a valid email address</LocalizedString>
```

### <a name="errormessage"></a>ErrorMessage

O valor ErrorMessage é usado para localizar uma das mensagens de erro do sistema. 

```xml
<TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="RaiseErrorIfClaimsPrincipalAlreadyExists">true</Item>
    <Item Key="UserMessageIfClaimsPrincipalAlreadyExists">You are already registered, please press the back button and sign in instead.</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

O exemplo a seguir mostra como localizar a mensagem de erro UserMessageIfClaimsPrincipalAlreadyExists.


```xml
<LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">The account you are trying to create already exists, please sign-in.</LocalizedString>
```

### <a name="formatlocalizedstringtransformationclaimtype"></a>FormatLocalizedStringTransformationClaimType

O valor de FormatLocalizedStringTransformationClaimType é usado para formatar declarações em uma cadeia de caracteres localizada. Para obter mais informações, consulte [FormatLocalizedString Claims Transformation](string-transformations.md#formatlocalizedstring)


```xml
<ClaimsTransformation Id="SetResponseMessageForEmailAlreadyExists" TransformationMethod="FormatLocalizedString">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="stringFormatId" DataType="string" Value="ResponseMessge_EmailExists" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="responseMsg" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

O exemplo a seguir mostra como localizar o formato de cadeia de caracteres da transformação declarações FormatLocalizedStringTransformationClaimType.

```xml
<LocalizedString ElementType="FormatLocalizedStringTransformationClaimType" StringId="ResponseMessge_EmailExists">The email '{0}' is already an account in this organization. Click Next to sign in with that account.</LocalizedString>
```

### <a name="getlocalizedstringstransformationclaimtype"></a>GetLocalizedStringsTransformationClaimType

O valor de GetLocalizedStringsTransformationClaimType é usado para copiar cadeias de caracteres localizadas em declarações. Para obter mais informações, consulte [GetLocalizedStringsTransformation Claims Transformation](string-transformations.md#getlocalizedstringstransformation)


```xml
<ClaimsTransformation Id="GetLocalizedStringsForEmail" TransformationMethod="GetLocalizedStringsTransformation">
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="subject" TransformationClaimType="email_subject" />
    <OutputClaim ClaimTypeReferenceId="message" TransformationClaimType="email_message" />
    <OutputClaim ClaimTypeReferenceId="codeIntro" TransformationClaimType="email_code" />
    <OutputClaim ClaimTypeReferenceId="signature" TransformationClaimType="email_signature" />
   </OutputClaims>
</ClaimsTransformation>
```

O exemplo a seguir mostra como localizar as declarações de saída da transformação declarações GetLocalizedStringsTransformation.

```xml
<LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_subject">Contoso account email verification code</LocalizedString>
<LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_message">Thanks for verifying your account!</LocalizedString>
<LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_code">Your code is</LocalizedString>
<LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_signature">Sincerely</LocalizedString>
```

### <a name="predicate"></a>Predicado

O valor do predicado é usado para localizar uma das mensagens de erro de [predicado](predicates.md) . 

```xml
<Predicates>
  <Predicate Id="LengthRange" Method="IsLengthRange"  HelpText="The password must be between 6 and 64 characters.">
    <Parameters>
      <Parameter Id="Minimum">6</Parameter>
      <Parameter Id="Maximum">64</Parameter>
    </Parameters>
  </Predicate>
  <Predicate Id="Lowercase" Method="IncludesCharacters" HelpText="a lowercase letter">
    <Parameters>
      <Parameter Id="CharacterSet">a-z</Parameter>
    </Parameters>
  </Predicate>
  <Predicate Id="Uppercase" Method="IncludesCharacters" HelpText="an uppercase letter">
    <Parameters>
      <Parameter Id="CharacterSet">A-Z</Parameter>
    </Parameters>
  </Predicate>
</Predicates>
```

O exemplo a seguir mostra como localizar o texto de ajuda de predicados.

```xml
<LocalizedString ElementType="Predicate" ElementId="LengthRange" StringId="HelpText">The password must be between 6 and 64 characters.</LocalizedString>
<LocalizedString ElementType="Predicate" ElementId="Lowercase" StringId="HelpText">a lowercase letter</LocalizedString>
<LocalizedString ElementType="Predicate" ElementId="Uppercase" StringId="HelpText">an uppercase letter</LocalizedString>
```

### <a name="inputvalidation"></a>InputValidation

O valor InputValidation é usado para localizar uma das mensagens de erro do grupo [PredicateValidation](predicates.md) . 

```xml
<PredicateValidations>
  <PredicateValidation Id="CustomPassword">
    <PredicateGroups>
      <PredicateGroup Id="LengthGroup">
        <PredicateReferences MatchAtLeast="1">
          <PredicateReference Id="LengthRange" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="CharacterClasses">
        <UserHelpText>The password must have at least 3 of the following:</UserHelpText>
        <PredicateReferences MatchAtLeast="3">
          <PredicateReference Id="Lowercase" />
          <PredicateReference Id="Uppercase" />
          <PredicateReference Id="Number" />
          <PredicateReference Id="Symbol" />
        </PredicateReferences>
      </PredicateGroup>
    </PredicateGroups>
  </PredicateValidation>
</PredicateValidations>
```

O exemplo a seguir mostra como localizar um texto de ajuda do grupo de validação de predicado.

```xml
<LocalizedString ElementType="InputValidation" ElementId="CustomPassword" StringId="CharacterClasses">The password must have at least 3 of the following:</LocalizedString>
```

### <a name="uxelement"></a>UxElement

O valor UxElement é usado para localizar um dos elementos da interface do usuário. O exemplo a seguir mostra como localizar os botões continue e Cancel.

```xml
<LocalizedString ElementType="UxElement" StringId="button_continue">Create new account</LocalizedString>
<LocalizedString ElementType="UxElement" StringId="button_cancel">Cancel</LocalizedString>
```

### <a name="displaycontrol"></a>DisplayControl

O valor de DisplayControl é usado para localizar um dos elementos da interface do usuário do [controle de exibição](display-controls.md) . Quando habilitado, o controle de exibição localizedStrings usa a *** precedência** _ sobre algumas das _ *UxElement** StringIDs como **ver_but_send**, **ver_but_edit**, **ver_but_resend** e **ver_but_verify**. O exemplo a seguir mostra como localizar os botões enviar e verificar. 

```xml
<LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="but_send_code">Send verification code</LocalizedString>
<LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="but_verify_code">Verify code</LocalizedString>
```

Na seção de metadados de um perfil técnico autodeclarado, o ContentDefinition referenciado precisa ter DataUri definido como versão de [layout de página](page-layout.md) 2.1.0 ou superior. Por exemplo:

```xml
<ContentDefinition Id="api.selfasserted">
  <DataUri>urn:com:microsoft:aad:b2c:elements:selfasserted:2.1.0</DataUri>
  ...
```

## <a name="next-steps"></a>Próximas etapas

Consulte os seguintes artigos para obter exemplos de localização:

- [Personalização de idioma com política personalizada no Azure Active Directory B2C](language-customization.md)
- [Personalização de idioma com fluxos de usuário no Azure Active Directory B2C](language-customization.md)
