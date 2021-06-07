---
title: Exemplos de transformação de declarações de cadeia de caracteres para políticas personalizadas
titleSuffix: Azure AD B2C
description: Exemplos de transformação de declarações de cadeias de caracteres para o esquema da Identity Experience Framework (IEF) do Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/08/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 85574b7d33af6d9abfe25f5af4d811255f08ce4b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102452230"
---
# <a name="string-claims-transformations"></a>Transformações de declarações de cadeias de caracteres

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo fornece exemplos para usar as transformações de declarações de cadeias de caracteres do esquema da Identity Experience Framework no Azure Active Directory B2C (Azure AD B2C). Para obter mais informações, confira [ClaimsTransformations](claimstransformations.md).

## <a name="assertstringclaimsareequal"></a>AssertStringClaimsAreEqual

Compare duas declarações e lance uma exceção se não forem iguais de acordo com a comparação especificada inputClaim1, inputClaim2 e stringComparison.

| Item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | string | Tipo da primeira declaração, que será comparado. |
| InputClaim | inputClaim2 | string | Tipo da segunda declaração, que será comparado. |
| InputParameter | stringComparison | string | comparação de cadeia de caracteres, um dos valores: Ordinal, OrdinalIgnoreCase. |

A transformação de declarações **AssertStringClaimsAreEqual** é sempre executada a partir um [perfil técnico de validação](validation-technical-profile.md) que é chamado por um [perfil técnico autodeclarado](self-asserted-technical-profile.md) ou um [DisplayControl](display-controls.md). Os metadados `UserMessageIfClaimsTransformationStringsAreNotEqual` de um perfil técnico autodeclarado controla a mensagem de erro que é apresentada ao usuário. A mensagem de erro pode ser [localizada](localization-string-ids.md#claims-transformations-error-messages).


![Execução do AssertStringClaimsAreEqual](./media/string-transformations/assert-execution.png)

Você pode usar essa transformação de declarações para certificar-se de declarações. Dois ClaimTypes têm o mesmo valor. Caso contrário, uma mensagem de erro será gerada. O exemplo a seguir verifica se o ClaimType **strongAuthenticationEmailAddress** é igual ao ClaimType **email**. Caso contrário, uma mensagem de erro será gerada.

```xml
<ClaimsTransformation Id="AssertEmailAndStrongAuthenticationEmailAddressAreEqual" TransformationMethod="AssertStringClaimsAreEqual">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="strongAuthenticationEmailAddress" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="inputClaim2" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="stringComparison" DataType="string" Value="ordinalIgnoreCase" />
  </InputParameters>
</ClaimsTransformation>
```


O perfil técnico de validação **login-NonInteractive** chama a transformação de declarações **AssertEmailAndStrongAuthenticationEmailAddressAreEqual**.
```xml
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertEmailAndStrongAuthenticationEmailAddressAreEqual" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

O perfil técnico autodeclarado chama o perfil técnico **login-NonInteractive** de validação.

```xml
<TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
  <Metadata>
    <Item Key="UserMessageIfClaimsTransformationStringsAreNotEqual">Custom error message the email addresses you provided are not the same.</Item>
  </Metadata>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="login-NonInteractive" />
  </ValidationTechnicalProfiles>
</TechnicalProfile>
```

### <a name="example"></a>Exemplo

- Declarações de entrada:
  - **inputClaim1**: someone@contoso.com
  - **inputClaim2**: someone@outlook.com
- Parâmetros de entrada:
  - **stringComparison**: ordinalIgnoreCase
- Resultado: Erro gerado

## <a name="changecase"></a>ChangeCase

Altera a declaração fornecida para letra maiúscula ou minúscula, dependendo do operador.

| Item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | string | O ClaimType a ser alterado. |
| InputParameter | toCase | string | Um dos seguintes valores: `LOWER` ou `UPPER`. |
| OutputClaim | outputClaim | string | O ClaimType que é produzido depois de invocar esta transformação de declarações. |

Use essa transformação de declaração para alterar qualquer ClaimType de cadeia de caracteres para letra maiúscula ou minúscula.

```xml
<ClaimsTransformation Id="ChangeToLower" TransformationMethod="ChangeCase">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="inputClaim1" />
  </InputClaims>
<InputParameters>
  <InputParameter Id="toCase" DataType="string" Value="LOWER" />
</InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Declarações de entrada:
  - **email**: SomeOne@contoso.com
- Parâmetros de entrada:
    - **toCase**: LOWER
- Declarações de saída:
  - **email**: someone@contoso.com

## <a name="createstringclaim"></a>CreateStringClaim

Cria uma declaração de cadeia de caracteres do parâmetro de entrada fornecido na transformação.

| Item | TransformationClaimType | Tipo de Dados | Observações |
|----- | ----------------------- | --------- | ----- |
| InputParameter | value | string | A cadeia de caracteres a ser definida. Esse parâmetro de entrada dá suporte a [expressões de transformação de declarações de cadeia de caracteres](string-transformations.md#string-claim-transformations-expressions). |
| OutputClaim | createdClaim | string | O ClaimType que é produzido depois de invocar esta transformação de declaração, com o valor especificado no parâmetro de entrada. |

Use essa transformação de declarações para definir um valor de ClaimType de cadeia de caracteres.

```xml
<ClaimsTransformation Id="CreateTermsOfService" TransformationMethod="CreateStringClaim">
  <InputParameters>
    <InputParameter Id="value" DataType="string" Value="Contoso terms of service..." />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="TOS" TransformationClaimType="createdClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Parâmetro de entrada:
    - **value**: termos de serviço da Contoso…
- Declarações de saída:
    - **createdClaim**: o ClaimType de TOS contém o valor "Termos de serviço da Contoso...".

## <a name="copyclaimifpredicatematch"></a>CopyClaimIfPredicateMatch

Copie o valor de uma declaração para outra se o valor da declaração de entrada corresponder ao predicado de declaração de saída. 

| Item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | InputClaim | string | O tipo de declaração, que deve ser copiado. |
| OutputClaim | outputClaim | string | O tipo de declaração que é produzido após essa transformação de declarações foi invocado. O valor da declaração de entrada é verificado em relação a este predicado de declaração. |

O exemplo a seguir copia o valor de declaração signInName para a declaração phoneNumber, somente se signInName for um número de telefone. Para obter o exemplo completo, consulte o [número de telefone ou](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/blob/master/scenarios/phone-number-passwordless/Phone_Email_Base.xml) a política de inicialização do pacote inicial de entrada de email.

```xml
<ClaimsTransformation Id="SetPhoneNumberIfPredicateMatch" TransformationMethod="CopyClaimIfPredicateMatch">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="signInName" TransformationClaimType="inputClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example-1"></a>Exemplo 1

- Declarações de entrada:
    - **inputClaim**: bob@contoso.com
- Declarações de saída:
    - **outputClaim**: a declaração de saída não será alterada de seu valor original.

### <a name="example-2"></a>Exemplo 2

- Declarações de entrada:
    - **inputClaim**: + 11234567890
- Declarações de saída:
    - **outputClaim**: +11234567890

## <a name="compareclaims"></a>CompareClaims

Determine se uma declaração de cadeia de caracteres é igual à outra. O resultado é um novo ClaimType booliano com um valor de `true` ou `false`.

| Item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | string | Tipo da primeira declaração, que será comparado. |
| InputClaim | inputClaim2 | string | Tipo da segunda declaração, que será comparado. |
| InputParameter | operador | string | Valores possíveis: `EQUAL` ou `NOT EQUAL`. |
| InputParameter | ignoreCase | booleano | Especifica se essa comparação deve ignorar maiúsculas e minúsculas das cadeias de caracteres que estão sendo comparadas. |
| OutputClaim | outputClaim | booleano | O ClaimType que é produzido depois de invocar esta transformação de declarações. |

Use essa transformação de declaração para verificar se uma declaração for igual à outra. Por exemplo, a seguinte transformação de declarações verifica se o valor da declaração **email** é igual à declaração **Verified.Email**.

```xml
<ClaimsTransformation Id="CheckEmail" TransformationMethod="CompareClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="Email" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="Verified.Email" TransformationClaimType="inputClaim2" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="operator" DataType="string" Value="NOT EQUAL" />
    <InputParameter Id="ignoreCase" DataType="string" Value="true" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="SameEmailAddress" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Declarações de entrada:
  - **inputClaim1**: someone@contoso.com
  - **inputClaim2**: someone@outlook.com
- Parâmetros de entrada:
    - **operator**:  NOT EQUAL
    - **ignoreCase**: true
- Declarações de saída:
    - **outputClaim**: true

## <a name="compareclaimtovalue"></a>CompareClaimToValue

Determina se um valor de declaração é igual ao valor do parâmetro de entrada.

| Item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | string | Tipo da declaração, que será comparado. |
| InputParameter | operador | string | Valores possíveis: `EQUAL` ou `NOT EQUAL`. |
| InputParameter | compareTo | string | comparação de cadeia de caracteres, um dos valores: Ordinal, OrdinalIgnoreCase. |
| InputParameter | ignoreCase | booleano | Especifica se essa comparação deve ignorar maiúsculas e minúsculas das cadeias de caracteres que estão sendo comparadas. |
| OutputClaim | outputClaim | booleano | O ClaimType que é produzido depois de invocar esta transformação de declarações. |

É possível usar essa transformação de declarações para verificar se uma declaração é igual ao valor especificado. Por exemplo, a seguinte transformação de declarações verifica se o valor da declaração **termsOfUseConsentVersion** é igual a `v1`.

```xml
<ClaimsTransformation Id="IsTermsOfUseConsentRequiredForVersion" TransformationMethod="CompareClaimToValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="termsOfUseConsentVersion" TransformationClaimType="inputClaim1" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="compareTo" DataType="string" Value="V1" />
    <InputParameter Id="operator" DataType="string" Value="not equal" />
    <InputParameter Id="ignoreCase" DataType="string" Value="true" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="termsOfUseConsentRequired" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo
- Declarações de entrada:
    - **inputClaim1**: v1
- Parâmetros de entrada:
    - **compareTo**: V1
    - **operator**: EQUAL
    - **ignoreCase**: true
- Declarações de saída:
    - **outputClaim**: true

## <a name="createrandomstring"></a>CreateRandomString

Cria uma cadeia de caracteres aleatória usando o gerador de número aleatório. Se o gerador de número aleatório é do tipo `integer`, opcionalmente, um parâmetro de semente e um número máximo podem ser fornecidos. Um parâmetro de formato de cadeia de caracteres opcional permite que a saída seja formatada usando-o, e um parâmetro de base64 opcional especifica se a saída é randomGeneratorType [guid, inteiro] outputClaim (cadeia de caracteres) codificada em base64.

| Item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputParameter | randomGeneratorType | string | Especifica o valor aleatório a ser gerado, `GUID` (ID global exclusivo) ou `INTEGER` (um número). |
| InputParameter | stringFormat | string | [Opcional] Formate o valor aleatório. |
| InputParameter | base64 | booleano | [Opcional] Converta o valor aleatório em base64. Se o formato de cadeia de caracteres for aplicado, o valor após o formato de cadeia de caracteres será codificado em base64. |
| InputParameter | maximumNumber | INT | [Opcional] Para randomGeneratorType `INTEGER` somente. Especifique o número máximo. |
| InputParameter | seed  | INT | [Opcional] Para randomGeneratorType `INTEGER` somente. Especifique a semente para valor aleatório. Observação: a mesma semente produz a mesma sequência de números aleatórios. |
| OutputClaim | outputClaim | string | Os ClaimTypes que serão produzidos depois de invocar esta transformação de declaração. O valor aleatório. |

O exemplo a seguir gera uma ID exclusiva global. Essa transformação de declarações é usada para criar o UPN (nome principal de usuário) aleatório.

```xml
<ClaimsTransformation Id="CreateRandomUPNUserName" TransformationMethod="CreateRandomString">
  <InputParameters>
    <InputParameter Id="randomGeneratorType" DataType="string" Value="GUID" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="upnUserName" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```
### <a name="example"></a>Exemplo

- Parâmetros de entrada:
    - **randomGeneratorType**: GUID
- Declarações de saída:
    - **outputClaim**: bc8bedd2-aaa3-411e-bdee-2f1810b73dfc

O exemplo a seguir gera um valor de inteiro aleatório entre 0 e 1.000. O valor é formatado para OTP_{valor aleatório}.

```xml
<ClaimsTransformation Id="SetRandomNumber" TransformationMethod="CreateRandomString">
  <InputParameters>
    <InputParameter Id="randomGeneratorType" DataType="string" Value="INTEGER" />
    <InputParameter Id="maximumNumber" DataType="int" Value="1000" />
    <InputParameter Id="stringFormat" DataType="string" Value="OTP_{0}" />
    <InputParameter Id="base64" DataType="boolean" Value="false" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="randomNumber" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Parâmetros de entrada:
    - **randomGeneratorType**: INTEGER
    - **maximumNumber**: 1000
    - **stringFormat**: OTP_{0}
    - **base64**: false
- Declarações de saída:
    - **outputClaim**: OTP_853


## <a name="formatlocalizedstring"></a>FormatLocalizedString

Formate várias declarações de acordo com uma cadeia de caracteres de formato localizada fornecida. Essa transformação usa o método C# `String.Format`.


| Item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaims |  |string | A coleção de declarações de entrada que atua como formato de cadeia de caracteres {0} , {1} , {2} parâmetros. |
| InputParameter | stringFormatId | string |  O `StringId` de uma [cadeia de caracteres localizada](localization.md).   |
| OutputClaim | outputClaim | string | O ClaimType que é produzido depois de invocar esta transformação de declarações. |

> [!NOTE]
> O tamanho máximo permitido do formato da cadeia de caracteres é 4000.

Para usar a transformação de declarações FormatLocalizedString:

1. Defina uma [cadeia de caracteres de localização](localization.md)e associe-a a um [perfil técnico autodeclarado](self-asserted-technical-profile.md).
1. O `ElementType` do elemento `LocalizedString` precisa ser configurado como `FormatLocalizedStringTransformationClaimType`.
1. O `StringId` é um identificador exclusivo que você define e usá-lo mais tarde em sua transformação de declarações `stringFormatId` .
1. Na transformação de declarações, especifique a lista de declarações a serem definidas com a cadeia de caracteres localizada. Em seguida, defina o `stringFormatId` para o `StringId` do elemento de cadeia de caracteres localizado. 
1. Em um [perfil técnico autodeclarado](self-asserted-technical-profile.md) ou uma transformação de declaração de entrada ou saída de [controle de exibição](display-controls.md), faça uma referência à transformação de declarações.


O exemplo a seguir gera uma mensagem de erro quando uma conta já está no diretório. O exemplo define cadeias de caracteres localizadas para inglês (padrão) e espanhol.

```xml
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="Append">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
   </SupportedLanguages>

  <LocalizedResources Id="api.localaccountsignup.en">
    <LocalizedStrings>
      <LocalizedString ElementType="FormatLocalizedStringTransformationClaimType" StringId="ResponseMessge_EmailExists">The email '{0}' is already an account in this organization. Click Next to sign in with that account.</LocalizedString>
      </LocalizedStrings>
    </LocalizedResources>
  <LocalizedResources Id="api.localaccountsignup.es">
    <LocalizedStrings>
      <LocalizedString ElementType="FormatLocalizedStringTransformationClaimType" StringId="ResponseMessge_EmailExists">Este correo electrónico "{0}" ya es una cuenta de esta organización. Haga clic en Siguiente para iniciar sesión con esa cuenta.</LocalizedString>
    </LocalizedStrings>
  </LocalizedResources>
</Localization>
```

A transformação declarações cria uma mensagem de resposta com base na cadeia de caracteres localizada. A mensagem contém o endereço de email do usuário inserido no *ResponseMessge_EmailExists* de Stinger localizado.

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

### <a name="example"></a>Exemplo

- Declarações de entrada:
    - **inputClaim**: sarah@contoso.com
- Parâmetros de entrada:
    - **StringFormat**: ResponseMessge_EmailExists
- Declarações de saída:
  - **outputClaim**: o email ' sarah@contoso.com ' já é uma conta nesta organização. Clique em avançar para entrar com essa conta.


## <a name="formatstringclaim"></a>FormatStringClaim

Formate uma declaração de acordo com a cadeia de caracteres de formato fornecida. Essa transformação usa o método C# `String.Format`.

| Item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | InputClaim |string |O ClaimType que atua como o parâmetro {0} do formato da cadeia de caracteres. |
| InputParameter | stringFormat | string | O formato da cadeia de caracteres, incluindo o parâmetro {0}. Esse parâmetro de entrada dá suporte a [expressões de transformação de declarações de cadeia de caracteres](string-transformations.md#string-claim-transformations-expressions).  |
| OutputClaim | outputClaim | string | O ClaimType que é produzido depois de invocar esta transformação de declarações. |

> [!NOTE]
> O tamanho máximo permitido do formato da cadeia de caracteres é 4000.

Use essa transformação de declarações para formatar qualquer cadeia de caracteres com um parâmetro {0}. O exemplo a seguir cria um **userPrincipalName**. Todos os perfis técnicos do provedor de identidade social, como `Facebook-OAUTH` chama **CreateUserPrincipalName** para gerar um **userPrincipalName**.

```xml
<ClaimsTransformation Id="CreateUserPrincipalName" TransformationMethod="FormatStringClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="upnUserName" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="stringFormat" DataType="string" Value="cpim_{0}@{RelyingPartyTenantId}" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="userPrincipalName" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Declarações de entrada:
    - **inputClaim**: 5164db16-3eee-4629-bfda-dcc3326790e9
- Parâmetros de entrada:
    - **stringFormat**: cpim_{0}@{RelyingPartyTenantId}
- Declarações de saída:
  - **outputClaim**: cpim_5164db16-3eee-4629-bfda-dcc3326790e9@b2cdemo.onmicrosoft.com

## <a name="formatstringmultipleclaims"></a>FormatStringMultipleClaims

Formate duas declarações de acordo com a cadeia de caracteres de formato fornecida. Essa transformação usa o método C# `String.Format`.

| Item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | InputClaim |string | O ClaimType que atua como o parâmetro {0} do formato da cadeia de caracteres. |
| InputClaim | InputClaim | string | O ClaimType que atua como o parâmetro {1} do formato da cadeia de caracteres. |
| InputParameter | stringFormat | string | O formato da cadeia de caracteres, incluindo os parâmetros {0} e {1}. Esse parâmetro de entrada dá suporte a [expressões de transformação de declarações de cadeia de caracteres](string-transformations.md#string-claim-transformations-expressions).   |
| OutputClaim | outputClaim | string | O ClaimType que é produzido depois de invocar esta transformação de declarações. |

> [!NOTE]
> O tamanho máximo permitido do formato da cadeia de caracteres é 4000.

Use essa transformação de declarações para formatar qualquer cadeia de caracteres com dois parâmetros, {0} e {1}. O exemplo a seguir cria um **displayName** com o formato especificado:

```xml
<ClaimsTransformation Id="CreateDisplayNameFromFirstNameAndLastName" TransformationMethod="FormatStringMultipleClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="surName" TransformationClaimType="inputClaim2" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="stringFormat" DataType="string" Value="{0} {1}" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="displayName" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Declarações de entrada:
    - **inputClaim1**: Joe
    - **inputClaim2**: Fernando
- Parâmetros de entrada:
    - **stringFormat**: {0} {1}
- Declarações de saída:
    - **outputClaim**: Joe Fernando

## <a name="getlocalizedstringstransformation"></a>GetLocalizedStringsTransformation

Copia cadeias de caracteres localizadas em declarações.

| Item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| OutputClaim | O nome da cadeia de caracteres localizada | string | Lista de tipos de declaração que serão produzidos depois de invocar essa transformação de declaração. |

Para usar a transformação de declarações GetLocalizedStringsTransformation:

1. Defina uma [cadeia de caracteres de localização](localization.md) e associe-a a um [self-asserted-technical-profile](self-asserted-technical-profile.md).
1. O `ElementType` do elemento `LocalizedString` precisa ser configurado como `GetLocalizedStringsTransformationClaimType`.
1. O `StringId` é um identificador exclusivo que você define e usa mais tarde na sua transformação de declarações.
1. Na transformação de declarações, especifique a lista de declarações a serem definidas com a cadeia de caracteres localizada. A referência `ClaimTypeReferenceId` é uma referência a um ClaimType já definido na seção ClaimsSchema na política. O `TransformationClaimType` é o nome da cadeia de caracteres localizada, conforme definido no `StringId` do elemento `LocalizedString`.
1. Em um [perfil técnico autodeclarado](self-asserted-technical-profile.md) ou uma transformação de declaração de entrada ou saída de [controle de exibição](display-controls.md), faça uma referência à transformação de declarações.

![GetLocalizedStringsTransformation](./media/string-transformations/get-localized-strings-transformation.png)

O exemplo a seguir pesquisa o assunto do email, o corpo, sua mensagem de código e a assinatura do email, de cadeias de caracteres localizadas. Essas declarações são usadas posteriormente pelo modelo de verificação de email personalizado.

Defina cadeias de caracteres localizadas em inglês (padrão) e espanhol.

```xml
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="Append">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
   </SupportedLanguages>

  <LocalizedResources Id="api.localaccountsignup.en">
    <LocalizedStrings>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_subject">Contoso account email verification code</LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_message">Thanks for verifying your account!</LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_code">Your code is</LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_signature">Sincerely</LocalizedString>
     </LocalizedStrings>
   </LocalizedResources>
   <LocalizedResources Id="api.localaccountsignup.es">
     <LocalizedStrings>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_subject">Código de verificación del correo electrónico de la cuenta de Contoso</LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_message">Gracias por comprobar la cuenta de </LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_code">Su código es</LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_signature">Atentamente</LocalizedString>
    </LocalizedStrings>
  </LocalizedResources>
</Localization>
```

A transformação de declaração define o valor do tipo de declaração *assunto* com o valor `StringId` *email_subject*.

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

### <a name="example"></a>Exemplo

- Declarações de saída:
  - **subject**: Código de verificação de email da conta da Contoso
  - **Mensagem**: Obrigado por verificar sua conta!
  - **codeIntro**: Seu código é
  - **signature**: Atenciosamente


## <a name="getmappedvaluefromlocalizedcollection"></a>GetMappedValueFromLocalizedCollection

Procurando um item de uma coleção de **Restrição** da declaração.

| Item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | mapFromClaim | string | A declaração que contém o texto a ser pesquisado nas declarações **restrictionValueClaim** com a coleção **Restrição**.  |
| OutputClaim | restrictionValueClaim | string | A declaração que contém a coleção **Restrição**. Depois que a transformação de declarações for invocada, o valor dessa declaração conterá o valor do item selecionado. |

O exemplo a seguir procura a descrição da mensagem de erro com base na chave de erro. A declaração **responseMsg** contém uma coleção de mensagens de erro para apresentar ao usuário final ou a ser enviada para a terceira parte confiável.

```xml
<ClaimType Id="responseMsg">
  <DisplayName>Error message: </DisplayName>
  <DataType>string</DataType>
  <UserInputType>Paragraph</UserInputType>
  <Restriction>
    <Enumeration Text="B2C_V1_90001" Value="You cannot sign in because you are a minor" />
    <Enumeration Text="B2C_V1_90002" Value="This action can only be performed by gold members" />
    <Enumeration Text="B2C_V1_90003" Value="You have not been enabled for this operation" />
  </Restriction>
</ClaimType>
```
A transformação de declarações procura o texto do item e retorna seu valor. Se a restrição é localizada usando `<LocalizedCollection>`, a transformação de declarações retorna o valor localizado.

```xml
<ClaimsTransformation Id="GetResponseMsgMappedToResponseCode" TransformationMethod="GetMappedValueFromLocalizedCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="responseCode" TransformationClaimType="mapFromClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="responseMsg" TransformationClaimType="restrictionValueClaim" />        
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Declarações de entrada:
    - **mapFromClaim**: B2C_V1_90001
- Declarações de saída:
    - **restrictionValueClaim**: Você não pode entrar porque é menor de idade.

## <a name="lookupvalue"></a>LookupValue

Procure um valor de declaração de uma lista de valores com base no valor de outra declaração.

| Item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputParameterId | string | A declaração que contém o valor de pesquisa |
| InputParameter | |string | Coleção de inputParameters. |
| InputParameter | errorOnFailedLookup | booleano | Controlar se um erro é retornado quando nenhuma pesquisa corresponde. |
| OutputClaim | inputParameterId | string | Os ClaimTypes que serão produzidos depois de invocar esta transformação de declaração. O valor da `Id` da correspondência. |

O exemplo a seguir procura o nome de domínio em uma das coleções inputParameters. A transformação de declarações procura o nome de domínio no identificador e retorna seu valor (uma ID de aplicativo).

```xml
 <ClaimsTransformation Id="DomainToClientId" TransformationMethod="LookupValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="domainName" TransformationClaimType="inputParameterId" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="contoso.com" DataType="string" Value="13c15f79-8fb1-4e29-a6c9-be0d36ff19f1" />
    <InputParameter Id="microsoft.com" DataType="string" Value="0213308f-17cb-4398-b97e-01da7bd4804e" />
    <InputParameter Id="test.com" DataType="string" Value="c7026f88-4299-4cdb-965d-3f166464b8a9" />
    <InputParameter Id="errorOnFailedLookup" DataType="boolean" Value="false" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="domainAppId" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Declarações de entrada:
    - **inputParameterId**: test.com
- Parâmetros de entrada:
    - **contoso.com**: 13c15f79-8fb1-4e29-a6c9-be0d36ff19f1
    - **microsoft.com**: 0213308f-17cb-4398-b97e-01da7bd4804e
    - **test.com**: c7026f88-4299-4cdb-965d-3f166464b8a9
    - **errorOnFailedLookup**: false
- Declarações de saída:
    - **outputClaim**:    c7026f88-4299-4cdb-965d-3f166464b8a9

Quando o parâmetro de entrada `errorOnFailedLookup` está definido como `true`, a transformação da declaração **LookupValue** é sempre executada a partir de um [perfil técnico de validação](validation-technical-profile.md) que é chamado por um [perfil técnico autodeclarado](self-asserted-technical-profile.md) ou um [DisplayConrtol](display-controls.md). Os metadados `LookupNotFound` de um perfil técnico autodeclarado controla a mensagem de erro que é apresentada ao usuário.

![Execução do AssertStringClaimsAreEqual](./media/string-transformations/assert-execution.png)

O exemplo a seguir procura o nome de domínio em uma das coleções inputParameters. A transformação de declarações procura o nome de domínio no identificador e retorna o valor (uma ID de aplicativo), ou dá origem a uma mensagem de erro.

```xml
 <ClaimsTransformation Id="DomainToClientId" TransformationMethod="LookupValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="domainName" TransformationClaimType="inputParameterId" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="contoso.com" DataType="string" Value="13c15f79-8fb1-4e29-a6c9-be0d36ff19f1" />
    <InputParameter Id="microsoft.com" DataType="string" Value="0213308f-17cb-4398-b97e-01da7bd4804e" />
    <InputParameter Id="test.com" DataType="string" Value="c7026f88-4299-4cdb-965d-3f166464b8a9" />
    <InputParameter Id="errorOnFailedLookup" DataType="boolean" Value="true" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="domainAppId" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Declarações de entrada:
    - **inputParameterId**: live.com
- Parâmetros de entrada:
    - **contoso.com**: 13c15f79-8fb1-4e29-a6c9-be0d36ff19f1
    - **microsoft.com**: 0213308f-17cb-4398-b97e-01da7bd4804e
    - **test.com**: c7026f88-4299-4cdb-965d-3f166464b8a9
    - **errorOnFailedLookup**: true
- Erro:
    - Nenhuma correspondência encontrada para o valor de declaração de entrada na lista de IDs de parâmetro de entrada e errorOnFailedLookup é true.


## <a name="nullclaim"></a>NullClaim

Limpe o valor de uma determinada declaração.

| Item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| OutputClaim | claim_to_null | string | O valor da declaração é definido como NULL. |

Use essa transformação de declaração para remover dados desnecessários do recipiente de propriedade de declarações, para que o cookie de sessão seja menor. O exemplo a seguir remove o valor do tipo de declaração `TermsOfService`.

```xml
<ClaimsTransformation Id="SetTOSToNull" TransformationMethod="NullClaim">
  <OutputClaims>
  <OutputClaim ClaimTypeReferenceId="TermsOfService" TransformationClaimType="claim_to_null" />
  </OutputClaims>
</ClaimsTransformation>
```

- Declarações de entrada:
    - **outputClaim**: Bem-vindo ao Aplicativo da Contoso. Se você continuar a navegar e usar esse site, você estará concordando em seguir e estará vinculado aos seguintes termos e condições...
- Declarações de saída:
    - **outputClaim**: NULO

## <a name="parsedomain"></a>ParseDomain

Obtém a parte do domínio de um endereço de email.

| Item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | emailAddress | string | O ClaimType que contém o endereço de email. |
| OutputClaim | domínio | string | O ClaimType que é produzido depois de invocar esta transformação de declarações – o domínio. |

Use essa transformação de declarações para analisar o nome de domínio depois do símbolo @ do usuário. A transformação de declarações a seguir demonstra como analisar o nome de domínio de uma declaração de **email**.

```xml
<ClaimsTransformation Id="SetDomainName" TransformationMethod="ParseDomain">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="emailAddress" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="domainName" TransformationClaimType="domain" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Declarações de entrada:
  - **emailAddress**: joe@outlook.com
- Declarações de saída:
    - **domain**: outlook.com

## <a name="setclaimsifregexmatch"></a>SetClaimsIfRegexMatch

Verifica se uma declaração de cadeia de caracteres `claimToMatch` e o parâmetro de entrada `matchTo` são iguais e define as declarações de saída com o valor presente no parâmetro de entrada `outputClaimIfMatched`, juntamente com a declaração de saída do resultado de comparação, que deve ser definida como `true` ou `false` com base no resultado da comparação.

| Item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | claimToMatch | string | Tipo da declaração, que será comparado. |
| InputParameter | matchTo | string | A expressão regular para fazer a correspondência. |
| InputParameter | outputClaimIfMatched | string | O valor a ser definido se as cadeias de caracteres forem iguais. |
| InputParameter | extractGroups | booleano | [Opcional] Especifica se a correspondência do Regex deve extrair valores de grupo. Valores possíveis: `true` ou `false` (padrão). | 
| OutputClaim | outputClaim | string | Se a expressão regular corresponder, essa declaração de saída conterá o valor do parâmetro de entrada `outputClaimIfMatched`. Ou Null, se não houver correspondência. |
| OutputClaim | regexCompareResultClaim | booleano | A expressão regular corresponde ao tipo de declaração de saída do resultado, que deve ser definido como `true` ou `false` com base no resultado da correspondência. |
| OutputClaim| O nome da declaração| string | Se o parâmetro de entrada extractGroups for definido como true, a lista de tipos de declaração produzidos após essa transformação de declarações ter sido invocada. O nome do claimType deve corresponder ao nome do grupo Regex. | 

### <a name="example-1"></a>Exemplo 1

Verifica se o número de telefone fornecido é válido, com base no padrão de expressão regular de número de telefone.

```xml
<ClaimsTransformation Id="SetIsPhoneRegex" TransformationMethod="SetClaimsIfRegexMatch">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phone" TransformationClaimType="claimToMatch" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="matchTo" DataType="string" Value="^[0-9]{4,16}$" />
    <InputParameter Id="outputClaimIfMatched" DataType="string" Value="isPhone" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="validationResult" TransformationClaimType="outputClaim" />
    <OutputClaim ClaimTypeReferenceId="isPhoneBoolean" TransformationClaimType="regexCompareResultClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

- Declarações de entrada:
    - **claimToMatch**: "64854114520"
- Parâmetros de entrada:
    - **matchTo**: "^[0-9]{4,16}$"
    - **outputClaimIfMatched**:  "isPhone"
- Declarações de saída:
    - **outputClaim**: "isPhone"
    - **regexCompareResultClaim**: true

### <a name="example-2"></a>Exemplo 2

Verifica se o endereço de email fornecido é válido e retorna o alias de email.

```xml
<ClaimsTransformation Id="GetAliasFromEmail" TransformationMethod="SetClaimsIfRegexMatch">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="claimToMatch" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="matchTo" DataType="string" Value="(?&lt;mailAlias&gt;.*)@(.*)$" />
    <InputParameter Id="outputClaimIfMatched" DataType="string" Value="isEmail" />
    <InputParameter Id="extractGroups" DataType="boolean" Value="true" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="validationResult" TransformationClaimType="outputClaim" />
    <OutputClaim ClaimTypeReferenceId="isEmailString" TransformationClaimType="regexCompareResultClaim" />
    <OutputClaim ClaimTypeReferenceId="mailAlias" />
  </OutputClaims>
</ClaimsTransformation>
```

- Declarações de entrada:
    - **claimToMatch**: "emily@contoso.com"
- Parâmetros de entrada:
    - **matchTo**: `(?&lt;mailAlias&gt;.*)@(.*)$`
    - **outputClaimIfMatched**:  "isEmail"
    - **extractGroups**: true
- Declarações de saída:
    - **outputClaim**: "isEmail"
    - **regexCompareResultClaim**: true
    - **mailAlias**: emily
    
## <a name="setclaimsifstringsareequal"></a>SetClaimsIfStringsAreEqual

Verifica se uma declaração de cadeia de caracteres e o parâmetro de entrada `matchTo` são iguais, e define as declarações de saída com o valor presente nos parâmetros de entrada `stringMatchMsg` e `stringMatchMsgCode`, juntamente com a declaração de saída do resultado de comparação, que deve ser definida como `true` ou `false` com base no resultado da comparação.

| Item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | InputClaim | string | Tipo da declaração, que será comparado. |
| InputParameter | matchTo | string | A cadeia de caracteres a ser comparada com `inputClaim`. |
| InputParameter | stringComparison | string | Valores possíveis: `Ordinal` ou `OrdinalIgnoreCase`. |
| InputParameter | stringMatchMsg | string | Primeiro valor a ser definido se as cadeias de caracteres forem iguais. |
| InputParameter | stringMatchMsgCode | string | Segundo valor a ser definido se as cadeias de caracteres forem iguais. |
| OutputClaim | outputClaim1 | string | Se as cadeias de caracteres forem iguais, essa declaração de saída conterá o valor do parâmetro de entrada `stringMatchMsg`. |
| OutputClaim | outputClaim2 | string | Se as cadeias de caracteres forem iguais, essa declaração de saída conterá o valor do parâmetro de entrada `stringMatchMsgCode`. |
| OutputClaim | stringCompareResultClaim | booleano | O tipo de declaração de saída do resultado de comparação, que deve ser definido como `true` ou `false` com base no resultado da comparação. |

É possível usar essa transformação de declarações para verificar se uma declaração é igual ao valor especificado. Por exemplo, a seguinte transformação de declarações verifica se o valor da declaração **termsOfUseConsentVersion** é igual a `v1`. Se sim, altere o resultado para `v2`.

```xml
<ClaimsTransformation Id="CheckTheTOS" TransformationMethod="SetClaimsIfStringsAreEqual">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="termsOfUseConsentVersion" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="matchTo" DataType="string" Value="v1" />
    <InputParameter Id="stringComparison" DataType="string" Value="ordinalIgnoreCase" />
    <InputParameter Id="stringMatchMsg" DataType="string" Value="B2C_V1_90005" />
    <InputParameter Id="stringMatchMsgCode" DataType="string" Value="The TOS is upgraded to v2" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="termsOfUseConsentVersion" TransformationClaimType="outputClaim1" />
    <OutputClaim ClaimTypeReferenceId="termsOfUseConsentVersionUpgradeCode" TransformationClaimType="outputClaim2" />
    <OutputClaim ClaimTypeReferenceId="termsOfUseConsentVersionUpgradeResult" TransformationClaimType="stringCompareResultClaim" />
  </OutputClaims>
</ClaimsTransformation>
```
### <a name="example"></a>Exemplo

- Declarações de entrada:
    - **inputClaim**: v1
- Parâmetros de entrada:
    - **matchTo**: V1
    - **stringComparison**: ordinalIgnoreCase
    - **stringMatchMsg**:  B2C_V1_90005
    - **stringMatchMsgCode**:  O TOS é atualizado para v2
- Declarações de saída:
    - **outputClaim1**: B2C_V1_90005
    - **outputClaim2**: O TOS é atualizado para v2
    - **stringCompareResultClaim**: true

## <a name="setclaimsifstringsmatch"></a>SetClaimsIfStringsMatch

Verifica se uma declaração de cadeia de caracteres e o parâmetro de entrada `matchTo` são iguais, e define as declarações de saída com o valor presente no parâmetro de entrada `outputClaimIfMatched`, juntamente com a declaração de saída do resultado de comparação, que deve ser definida como `true` ou `false` com base no resultado da comparação.

| Item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | claimToMatch | string | Tipo da declaração, que será comparado. |
| InputParameter | matchTo | string | A cadeia de caracteres a ser comparada com inputClaim. |
| InputParameter | stringComparison | string | Valores possíveis: `Ordinal` ou `OrdinalIgnoreCase`. |
| InputParameter | outputClaimIfMatched | string | O valor a ser definido se as cadeias de caracteres forem iguais. |
| OutputClaim | outputClaim | string | Se as cadeias de caracteres forem iguais, essa declaração de saída conterá o valor do parâmetro de entrada `outputClaimIfMatched`. Ou nulo se as cadeias de caracteres não corresponderem. |
| OutputClaim | stringCompareResultClaim | booleano | O tipo de declaração de saída do resultado de comparação, que deve ser definido como `true` ou `false` com base no resultado da comparação. |

Por exemplo, a seguinte transformação de declarações verifica se o valor da declaração **ageGroup** é igual a `Minor`. Em caso afirmativo, retorne o valor para `B2C_V1_90001`.

```xml
<ClaimsTransformation Id="SetIsMinor" TransformationMethod="SetClaimsIfStringsMatch">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="ageGroup" TransformationClaimType="claimToMatch" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="matchTo" DataType="string" Value="Minor" />
    <InputParameter Id="stringComparison" DataType="string" Value="ordinalIgnoreCase" />
    <InputParameter Id="outputClaimIfMatched" DataType="string" Value="B2C_V1_90001" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isMinor" TransformationClaimType="outputClaim" />
    <OutputClaim ClaimTypeReferenceId="isMinorResponseCode" TransformationClaimType="stringCompareResultClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Declarações de entrada:
    - **claimToMatch**: Secundária
- Parâmetros de entrada:
    - **matchTo**: Secundária
    - **stringComparison**: ordinalIgnoreCase
    - **outputClaimIfMatched**:  B2C_V1_90001
- Declarações de saída:
    - **isMinorResponseCode**: B2C_V1_90001
    - **isMinor**: true


## <a name="stringcontains"></a>StringContains

Determine se uma substring especificada ocorre na declaração de entrada. O resultado é um novo ClaimType booliano com um valor de `true` ou `false`. `true` se o parâmetro de valor ocorrer nessa cadeia de caracteres, caso contrário, `false`.

| Item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | InputClaim | string | O tipo de declaração, que será pesquisado. |
|InputParameter|contains|string|O valor a pesquisar.|
|InputParameter|ignoreCase|string|Especifica se essa comparação deve ignorar maiúsculas e minúsculas das cadeias de caracteres que estão sendo comparadas.|
| OutputClaim | outputClaim | string | O ClaimType produzido depois de invocar esta ClaimsTransformation. Um indicador booleano se a substring ocorrer na declaração de entrada. |

Use essa transformação de declaração para verificar se um tipo de declaração de cadeia de caracteres contém uma substring. De acordo com o exemplo, verifica se o tipo de declaração da cadeia de caracteres `roles` contém o valor de **admin**.

```xml
<ClaimsTransformation Id="CheckIsAdmin" TransformationMethod="StringContains">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="roles" TransformationClaimType="inputClaim"/>
  </InputClaims>
  <InputParameters>
    <InputParameter  Id="contains" DataType="string" Value="admin"/>
    <InputParameter  Id="ignoreCase" DataType="string" Value="true"/>
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isAdmin" TransformationClaimType="outputClaim"/>
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Declarações de entrada:
    - **inputClaim**: "Admin, Approver, Editor"
- Parâmetros de entrada:
    - **contains**: "admin,"
    - **ignoreCase**: true
- Declarações de saída:
    - **outputClaim**: true

## <a name="stringsubstring"></a>StringSubstring

Extrai partes de um tipo de declaração de cadeia de caracteres, iniciando pelo caractere na posição especificada e retorna o número especificado de caracteres.

| Item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | InputClaim | string | O tipo de declaração que contém a cadeia de caracteres. |
| InputParameter | startIndex | INT | A posição do caractere inicial com base em zero de uma substring nessa instância. |
| InputParameter | comprimento | INT | O número de caracteres na substring. |
| OutputClaim | outputClaim | booleano | Uma cadeia de caracteres equivalente à substring do comprimento que inicia em startIndex nessa instância, ou Empty se startIndex for igual ao comprimento dessa instância e o comprimento for zero. |

Por exemplo, obtenha o prefixo do país ou da região do número de telefone.


```xml
<ClaimsTransformation Id="GetPhonePrefix" TransformationMethod="StringSubstring">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="inputClaim" />
  </InputClaims>
<InputParameters>
  <InputParameter Id="startIndex" DataType="int" Value="0" />
  <InputParameter Id="length" DataType="int" Value="2" />
</InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="phonePrefix" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```
### <a name="example"></a>Exemplo

- Declarações de entrada:
    - **inputClaim**: "+1644114520"
- Parâmetros de entrada:
    - **startIndex**: 0
    - **length**:  2
- Declarações de saída:
    - **outputClaim**: "+1"

## <a name="stringreplace"></a>StringReplace

Pesquisa uma cadeia de caracteres do tipo de declaração em um valor especificado e retorna uma nova cadeia de caracteres do tipo de declaração em que todas as ocorrências de uma cadeia de caracteres especificada na atual são substituídas por outra cadeia de caracteres especificada.

| Item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | InputClaim | string | O tipo de declaração que contém a cadeia de caracteres. |
| InputParameter | oldValue | string | A cadeia de caracteres a ser pesquisada. |
| InputParameter | newValue | string | A cadeia de caracteres para substituir todas as ocorrências de `oldValue` |
| OutputClaim | outputClaim | booleano | Uma cadeia de caracteres equivalente à atual, exceto que todas as instâncias de oldValue foram substituídas por newValue. Se oldValue não for encontrado na instância atual, o método retornará a instância atual inalterada. |

Por exemplo, normalize um número de telefone removendo os caracteres `-`


```xml
<ClaimsTransformation Id="NormalizePhoneNumber" TransformationMethod="StringReplace">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="inputClaim" />
  </InputClaims>
<InputParameters>
  <InputParameter Id="oldValue" DataType="string" Value="-" />
  <InputParameter Id="newValue" DataType="string" Value="" />
</InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```
### <a name="example"></a>Exemplo

- Declarações de entrada:
    - **inputClaim**: "+164-411-452-054"
- Parâmetros de entrada:
    - **oldValue**: "-"
    - **NewValue**: ""
- Declarações de saída:
    - **outputClaim**: "+164411452054"

## <a name="stringjoin"></a>StringJoin

Concatena os elementos de um tipo de declaração de coleção de cadeia de caracteres especificado, usando o separador especificado entre cada elemento ou membro.

| Item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | InputClaim | stringCollection | Uma coleção que contém as cadeias de caracteres a serem concatenadas. |
| InputParameter | delimiter | string | A cadeia de caracteres a ser usada como separador, como vírgula `,`. |
| OutputClaim | outputClaim | string | Uma cadeia de caracteres que consiste dos membros da coleção de cadeia de caracteres `inputClaim`, delimitada pelo parâmetro de entrada `delimiter`. |

O exemplo a seguir usa uma coleção de cadeias de caracteres de funções de usuário e a converte em uma cadeia de caracteres que usa vírgula como delimitador. Você pode usar esse método para armazenar uma coleção de cadeia de caracteres na conta de usuário do Azure Active Directory. Posteriormente, ao ler a conta a partir do diretório, use `StringSplit` para converter a cadeia de caracteres que usa vírgula como delimitador de volta à coleção de cadeia de caracteres.

```xml
<ClaimsTransformation Id="ConvertRolesStringCollectionToCommaDelimiterString" TransformationMethod="StringJoin">
  <InputClaims>
   <InputClaim ClaimTypeReferenceId="roles" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter DataType="string" Id="delimiter" Value="," />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="rolesCommaDelimiterConverted" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Declarações de entrada:
  - **inputClaim**: [ "Admin", "Author", "Reader" ]
- Parâmetros de entrada:
  - **delimiter**: ","
- Declarações de saída:
  - **outputClaim**: "Admin,Author,Reader"


## <a name="stringsplit"></a>StringSplit

Retorna uma matriz de cadeia de caracteres que contém as substrings nessa instância que são delimitadas por elementos de uma cadeia de caracteres especificada.

| Item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | InputClaim | string | Um tipo de declaração de cadeia de caracteres que contém as substrings a serem divididas. |
| InputParameter | delimiter | string | A cadeia de caracteres a ser usada como separador, como vírgula `,`. |
| OutputClaim | outputClaim | stringCollection | Uma coleção de cadeias de caracteres cujos elementos contêm as substrings nessa cadeia de caracteres delimitadas pelo parâmetro de entrada `delimiter`. |

O exemplo a seguir usa uma cadeia de caracteres de funções de usuário que usa vírgula com delimitador e a converte em uma coleção de cadeia de caracteres.

```xml
<ClaimsTransformation Id="ConvertRolesToStringCollection" TransformationMethod="StringSplit">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="rolesCommaDelimiter" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
  <InputParameter DataType="string" Id="delimiter" Value="," />
    </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="roles" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Declarações de entrada:
  - **inputClaim**: "Admin,Author,Reader"
- Parâmetros de entrada:
  - **delimiter**: ","
- Declarações de saída:
  - **outputClaim**: [ "Admin", "Author", "Reader" ]

## <a name="string-claim-transformations-expressions"></a>Expressões de transformações de declaração de cadeia de caracteres
Expressões de transformações de declaração nas políticas personalizadas do Azure AD B2C fornecem informações de contexto sobre a ID do locatário e a ID do perfil técnico.

  | Expression | Descrição | Exemplo |
 | ----- | ----------- | --------|
 | `{TechnicalProfileId}` | O nome do perfil técnico. | Facebook-OAUTH |
 | `{RelyingPartyTenantId}` | A ID do locatário da política de terceira parte confiável. | your-tenant.onmicrosoft.com |
 | `{TrustFrameworkTenantId}` | A ID do locatário e da estrutura de confiança. | your-tenant.onmicrosoft.com |
