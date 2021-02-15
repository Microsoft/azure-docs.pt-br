---
title: Exemplos de transformação de declarações StringCollection para políticas personalizadas
titleSuffix: Azure AD B2C
description: Exemplos de transformação de declarações StringCollection para o esquema IEF (Identity Experience Framework) de Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 04/21/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 1ad9cc3d6d07c8d744ec667e2fffb035848121b4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85203241"
---
# <a name="stringcollection-claims-transformations"></a>Transformações de declarações StringCollection

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo fornece exemplos de como usar as transformações de declarações de coleção de cadeia de caracteres do esquema de estrutura de experiência de identidade em Azure Active Directory B2C (Azure AD B2C). Para obter mais informações, confira [ClaimsTransformations](claimstransformations.md).

## <a name="additemtostringcollection"></a>AddItemToStringCollection

Adiciona uma declaração de cadeia de caracteres a uma nova declaração de StringCollection de valores exclusivos.

| Item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | item | string | O ClaimType a ser adicionado à declaração de saída. |
| InputClaim | collection | stringCollection | [Opcional] Se especificada, a transformação de declarações copiará os itens desta coleção e adicionará o item ao final da declaração da coleção de saída. |
| OutputClaim | collection | stringCollection | O ClaimType que é produzido após essa transformação de declarações foi invocado, com o valor especificado na declaração de entrada. |

Use essa transformação de declaração para adicionar uma cadeia de caracteres a uma stringCollection nova ou existente. Ele é normalmente usado em um perfil técnico do **AAD-UserWriteUsingAlternativeSecurityId**. Antes que uma nova conta social seja criada, a transformação da declaração **CreateOtherMailsFromEmail** lê o ClaimType e adiciona o valor ao ClaimType **otherMails**.

A transformação de declarações a seguir adiciona o ClaimType **email** ao ClaimType **otherMails**.

```xml
<ClaimsTransformation Id="CreateOtherMailsFromEmail" TransformationMethod="AddItemToStringCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="item" />
    <InputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Declarações de entrada:
  - **coleção**: [" someone@outlook.com "]
  - **Item**: " admin@contoso.com "
- Declarações de saída:
  - **coleção**: [" someone@outlook.com ", " admin@contoso.com "]

## <a name="addparametertostringcollection"></a>AddParameterToStringCollection

Adiciona um parâmetro de cadeia de caracteres a uma nova declaração StringCollection de valores exclusivos.

| Item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | collection | stringCollection | [Opcional] Se especificada, a transformação de declarações copiará os itens desta coleção e adicionará o item ao final da declaração da coleção de saída. |
| InputParameter | item | string | O valor a ser adicionado à declaração de saída. |
| OutputClaim | collection | stringCollection | O ClaimType que é produzido depois de invocar esta transformação de declaração, com o valor especificado no parâmetro de entrada. |

Use essa transformação de declaração para adicionar um valor de cadeia de caracteres a uma stringCollection nova ou existente. O exemplo a seguir adiciona um endereço de email constante (admin@contoso.com) à declaração **otherMails**.

```xml
<ClaimsTransformation Id="SetCompanyEmail" TransformationMethod="AddParameterToStringCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="item" DataType="string" Value="admin@contoso.com" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Declarações de entrada:
  - **coleção**: [" someone@outlook.com "]
- Parâmetros de entrada
  - **Item**: " admin@contoso.com "
- Declarações de saída:
  - **coleção**: [" someone@outlook.com ", " admin@contoso.com "]

## <a name="getsingleitemfromstringcollection"></a>GetSingleItemFromStringCollection

Obtém o primeiro item da coleção de cadeia de caracteres fornecida.

| Item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | collection | stringCollection | Os ClaimTypes que são usados pela transformação de declarações para obter o item. |
| OutputClaim | extractedItem | string | Os ClaimTypes produzidos depois de invocar este ClaimsTransformation. O primeiro item na coleção. |

O exemplo a seguir lê a declaração **otherMails** e retorna o primeiro item para a declaração **email**.

```xml
<ClaimsTransformation Id="CreateEmailFromOtherMails" TransformationMethod="GetSingleItemFromStringCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" TransformationClaimType="extractedItem" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Declarações de entrada:
  - **coleção**: [" someone@outlook.com ", " someone@contoso.com "]
- Declarações de saída:
  - **extractedItem**: " someone@outlook.com "


## <a name="stringcollectioncontains"></a>StringCollectionContains

Verifica se um tipo de declaração StringCollection contém um elemento

| Item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | InputClaim | stringCollection | O tipo de declaração que deve ser pesquisada. |
|InputParameter|item|string|O valor a pesquisar.|
|InputParameter|ignoreCase|string|Especifica se essa comparação deve ignorar maiúsculas e minúsculas das cadeias de caracteres que estão sendo comparadas.|
| OutputClaim | outputClaim | booleano | O ClaimType produzido depois de invocar esta ClaimsTransformation. Um indicador booliano se a coleção contiver uma cadeia de caracteres desse tipo |

O exemplo a seguir verifica se o `roles` tipo de declaração StringCollection contém o valor de **admin**.

```xml
<ClaimsTransformation Id="IsAdmin" TransformationMethod="StringCollectionContains">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="roles" TransformationClaimType="inputClaim"/>
  </InputClaims>
  <InputParameters>
    <InputParameter  Id="item" DataType="string" Value="Admin"/>
    <InputParameter  Id="ignoreCase" DataType="string" Value="true"/>
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isAdmin" TransformationClaimType="outputClaim"/>
  </OutputClaims>
</ClaimsTransformation>
```

- Declarações de entrada:
    - **inputClaim**: ["leitor", "autor", "admin"]
- Parâmetros de entrada:
    - **Item**: "admin"
    - **IgnoreCase**: "true"
- Declarações de saída:
    - **outputClaim**: "verdadeiro"

## <a name="stringcollectioncontainsclaim"></a>StringCollectionContainsClaim

Verifica se um tipo de declaração StringCollection contém um valor de declaração.

| Item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | collection | stringCollection | O tipo de declaração que deve ser pesquisada. |
| InputClaim | item|string| O tipo de declaração que contém o valor a ser pesquisado.|
|InputParameter|ignoreCase|string|Especifica se essa comparação deve ignorar maiúsculas e minúsculas das cadeias de caracteres que estão sendo comparadas.|
| OutputClaim | outputClaim | booleano | O ClaimType produzido depois de invocar esta ClaimsTransformation. Um indicador booliano se a coleção contiver uma cadeia de caracteres desse tipo |

O exemplo a seguir verifica se o `roles` tipo de declaração StringCollection contém o valor do `role` tipo de declaração.

```xml
<ClaimsTransformation Id="HasRequiredRole" TransformationMethod="StringCollectionContainsClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="roles" TransformationClaimType="collection" />
    <InputClaim ClaimTypeReferenceId="role" TransformationClaimType="item" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="ignoreCase" DataType="string" Value="true" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="hasAccess" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation> 
```

- Declarações de entrada:
    - **coleção**: ["leitor", "autor", "admin"]
    - **Item**: "admin"
- Parâmetros de entrada:
    - **IgnoreCase**: "true"
- Declarações de saída:
    - **outputClaim**: "verdadeiro"
