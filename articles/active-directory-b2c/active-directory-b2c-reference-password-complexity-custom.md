---
title: "Complexidade da senha em políticas personalizadas – Azure AD B2C | Microsoft Docs"
description: "Como configurar os requisitos de complexidade para senhas na Política Personalizada"
services: active-directory-b2c
documentationcenter: 
author: saeedakhter-msft
manager: krassk
editor: parakhj
ms.assetid: 53ef86c4-1586-45dc-9952-dbbd62f68afc
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/16/2017
ms.author: saeeda
ms.openlocfilehash: 5be521a08ab70be891ddd5fcb374adc52d5c0705
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="configure-password-complexity-in-custom-policies"></a>Configurar a complexidade de senha em políticas personalizada

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo é uma descrição avançada de como funciona a complexidade da senha e é habilitada por meio de políticas personalizadas do Azure AD B2C.

## <a name="azure-ad-b2c-configure-complexity-requirements-for-passwords"></a>Azure AD B2C: como configurar os requisitos de complexidade de senhas

O Azure Active Directory B2C (Azure AD B2C) oferece suporte à alteração de requisitos de complexidade para senhas fornecidas por um usuário final ao criar uma conta.  Por padrão, o Azure AD B2C usa senhas **Fortes**.  O Azure AD B2C também oferece suporte a opções de configuração para controlar a complexidade de senhas que os clientes podem usar.  Este artigo aborda como configurar a complexidade de senha em políticas personalizadas.  Também é possível usar [configurar a complexidade de senha em políticas internas](active-directory-b2c-reference-password-complexity.md).

## <a name="prerequisites"></a>Pré-requisitos

Um locatário do Azure AD B2C configurado para concluir uma inscrição/entrada de conta local, conforme descrito em [Introdução](active-directory-b2c-get-started-custom.md).

## <a name="how-to-configure-password-complexity-in-custom-policy"></a>Como configurar a complexidade de senha na políticas personalizada

Para configurar a complexidade de senha na política personalizada, a estrutura geral de sua política personalizada deve incluir um elemento `ClaimsSchema`, `Predicates` e `InputValidations` dentro de `BuildingBlocks`.

```XML
  <BuildingBlocks>
    <ClaimsSchema>...</ClaimsSchema>
    <Predicates>...</Predicates>
    <InputValidations>...</InputValidations>
  </BuildingBlocks>
```

O propósito desses elementos é o seguinte:

- Cada elemento `Predicate` define uma verificação de validação de cadeias de caracteres básica que retorna true ou false.
- O elemento `InputValidations` contém um ou mais elementos `InputValidation`.  Cada `InputValidation` é construído usando uma série de elementos `Predicate`. Esse elemento permite a execução de agregações boolianas (semelhante a `and` e `or`).
- O `ClaimsSchema` define qual declaração está sendo validada.  Em seguida, define qual regra `InputValidation` é usada para validar essa declaração.

### <a name="defining-a-predicate-element"></a>Definir um elemento de predicado

Os predicados têm dois tipos de método: IsLengthRange ou MatchesRegex. Vamos analisar um exemplo de cada um.  Primeiro, temos um exemplo de MatchesRegex, que é usado para corresponder a uma expressão regular.  Neste exemplo, ele corresponde a cadeia de caracteres que contém os números.

```XML
      <Predicate Id="PIN" Method="MatchesRegex" HelpText="The password must be a pin.">
        <Parameters>
          <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
        </Parameters>
      </Predicate>
```

Em seguida, vamos analisar um exemplo de IsLengthRange.  Esse método usa um comprimento de cadeia de caracteres mínimo e máximo.

```XML
      <Predicate Id="Length" Method="IsLengthRange" HelpText="The password must be between 8 and 16 characters.">
        <Parameters>
          <Parameter Id="Minimum">8</Parameter>
          <Parameter Id="Maximum">16</Parameter>
        </Parameters>
      </Predicate>
```

Use o atributo `HelpText` para fornecer uma mensagem de erro para os usuários finais se a verificação falhar.  Essa cadeia de caracteres pode ser localizada usando o [recurso de personalização de idioma](active-directory-b2c-reference-language-customization.md).

### <a name="defining-an-inputvalidation-element"></a>Definir um elemento InputValidation

Uma `InputValidation` é uma agregação de `PredicateReferences`. Cada `PredicateReferences` devem ser verdadeiro para que o `InputValidation` seja bem-sucedido.  No entanto, dentro do elemento `PredicateReferences` use um atributo chamado `MatchAtLeast` para especificar quantas verificações de `PredicateReference` devem retornar true.  Opcionalmente, defina um atributo `HelpText` para substituir a mensagem de erro definida nos elementos `Predicate` que faz referência a ele.

```XML
      <InputValidation Id="PasswordValidation">
        <PredicateReferences Id="LengthGroup" MatchAtLeast="1">
          <PredicateReference Id="Length" />
        </PredicateReferences>
        <PredicateReferences Id="3of4" MatchAtLeast="3" HelpText="You must have at least 3 of the following character classes:">
          <PredicateReference Id="Lowercase" />
          <PredicateReference Id="Uppercase" />
          <PredicateReference Id="Number" />
          <PredicateReference Id="Symbol" />
        </PredicateReferences>
      </InputValidation>
```

### <a name="defining-a-claimsschema-element"></a>Definir um elemento ClaimsSchema

Os tipos de declaração `newPassword` e `reenterPassword` são considerados especiais, portanto, não altere os nomes.  A interface do usuário valida se o usuário reinseriu corretamente sua senha durante a criação da conta com base nesses elementos `ClaimType`.  Para localizar os mesmos elementos `ClaimType`, examine o TrustFrameworkBase.xml em seu pacote inicializador.  A novidade neste exemplo é que estamos substituindo esses elementos para definir um `InputValidationReference`. O atributo `ID` desse novo elemento está apontando para o elemento `InputValidation` que definimos.

```XML
    <ClaimsSchema>
      <ClaimType Id="newPassword">
        <Restriction>
          <Pattern RegularExpression="^.*$" HelpText="" />
        </Restriction>
        <InputValidationReference Id="PasswordValidation" />
      </ClaimType>
      <ClaimType Id="reenterPassword">
        <Restriction>
          <Pattern RegularExpression="^.*$" HelpText="" />
        </Restriction>
        <InputValidationReference Id="PasswordValidation" />
      </ClaimType>
    </ClaimsSchema>
```

### <a name="putting-it-all-together"></a>Juntando as peças

Este exemplo mostra como todas as partes funcionam em conjunto para formar uma política de trabalho.  Para usar este exemplo:

1. Siga as instruções na [Introdução](active-directory-b2c-get-started-custom.md) dos pré-requisitos para baixar, configurar e carregar TrustFrameworkBase.xml e TrustFrameworkExtensions.xml
1. Crie um arquivo SignUporSignIn.xml usando o conteúdo de exemplo nesta seção.
1. Atualize SignUporSignIn.xml substituindo `yourtenant` pelo nome do locatário do Azure AD B2C.
1. Carregue o arquivo da política de SignUporSignIn.xml por último.

Este exemplo contém uma validação para senhas de pin e outra para senhas fortes:

- Procure `PINpassword`. Esse elemento `InputValidation` valida um pin de qualquer comprimento.  Não é usado no momento, porque ele não é referenciado no elemento `InputValidationReference` dentro de `ClaimType`. 
- Procure `PasswordValidation`. Esse elemento `InputValidation` valida se uma senha tem oito a 16 caracteres e contém três de quatro números, letras maiúsculas, minúsculas ou símbolos.  Ele é referenciado em `ClaimType`.  Portanto, essa regra está sendo imposta nesta política.

```XML
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<TrustFrameworkPolicy
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="http://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="yourtenant.onmicrosoft.com"
  PolicyId="B2C_1A_signup_signin"
  PublicPolicyUri="http://yourtenant.onmicrosoft.com/B2C_1A_signup_signin">
 <BasePolicy>
    <TenantId>yourtenant.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>
  <BuildingBlocks>
    <ClaimsSchema>
      <ClaimType Id="newPassword">
        <Restriction>
          <Pattern RegularExpression="^.*$" HelpText="" />
        </Restriction>
        <InputValidationReference Id="PasswordValidation" />
      </ClaimType>
      <ClaimType Id="reenterPassword">
        <Restriction>
          <Pattern RegularExpression="^.*$" HelpText="" />
        </Restriction>
        <InputValidationReference Id="PasswordValidation" />
      </ClaimType>
    </ClaimsSchema>
    <Predicates>
      <Predicate Id="Lowercase" Method="MatchesRegex" HelpText="a lowercase">
        <Parameters>
          <Parameter Id="RegularExpression">^[a-z]+$</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Uppercase" Method="MatchesRegex" HelpText="an uppercase">
        <Parameters>
          <Parameter Id="RegularExpression">^[A-Z]+$</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Number" Method="MatchesRegex" HelpText="a number">
        <Parameters>
          <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Symbol" Method="MatchesRegex" HelpText="a symbol">
        <Parameters>
          <Parameter Id="RegularExpression">^[!@#$%^*()]+$</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Length" Method="IsLengthRange" HelpText="The password must be between 8 and 16 characters.">
        <Parameters>
          <Parameter Id="Minimum">8</Parameter>
          <Parameter Id="Maximum">16</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="PIN" Method="MatchesRegex" HelpText="The password must be a pin.">
        <Parameters>
          <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
        </Parameters>
      </Predicate>
    </Predicates>
    <InputValidations>
      <InputValidation Id="PasswordValidation">
        <PredicateReferences Id="LengthGroup" MatchAtLeast="1">
          <PredicateReference Id="Length" />
        </PredicateReferences>
        <PredicateReferences Id="3of4" MatchAtLeast="3" HelpText="You must have at least 3 of the following character classes:">
          <PredicateReference Id="Lowercase" />
          <PredicateReference Id="Uppercase" />
          <PredicateReference Id="Number" />
          <PredicateReference Id="Symbol" />
        </PredicateReferences>
      </InputValidation>
      <InputValidation Id="PINpassword">
        <PredicateReferences Id="PINGroup">
          <PredicateReference Id="PIN" />
        </PredicateReferences>
      </InputValidation>
    </InputValidations>
  </BuildingBlocks>
  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration, DisableStrongPassword" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
</TrustFrameworkPolicy>
```
