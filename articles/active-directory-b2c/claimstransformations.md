---
title: ClaimsTransformations – Azure Active Directory B2C | Microsoft Docs
description: Definição do elemento ClaimsTransformations no esquema do Identity Experience Framework do Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 37d9bd78a80ac52d2a790537bf47e33807720349
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85202952"
---
# <a name="claimstransformations"></a>ClaimsTransformations

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O elemento **ClaimsTransformations** contém uma lista de funções de transformação de declarações que podem ser usadas nos percursos do usuário como parte de uma [política personalizada](custom-policy-overview.md). Uma transformação de declarações converte uma determinada declaração em outra. Na transformação de declarações, especifique o método de transformação, por exemplo, adicionando um item a uma coleção de cadeia de caracteres ou alterando o uso de maiúsculas e minúsculas em uma cadeia de caracteres.

Para incluir a lista de funções de transformação de declarações que podem ser usadas nos percursos do usuário, um elemento XML ClaimsTransformations precisa ser declarado na seção BuildingBlocks da política.

```xml
<ClaimsTransformations>
  <ClaimsTransformation Id="<identifier>" TransformationMethod="<method>">
    ...
  </ClaimsTransformation>
</ClaimsTransformations>
```

O elemento **ClaimsTransformation** contém os seguintes atributos:

| Atributo |Obrigatório | Descrição |
| --------- |-------- | ----------- |
| ID |Sim | Um identificador que é usado para identificar exclusivamente a transformação de declaração. O identificador é referenciado de outros elementos XML na política. |
| TransformationMethod | Sim | O método de transformação a ser usado na transformação de declarações. Cada transformação de declaração tem seus próprios valores. Confira a [Referência de transformação de declarações](#claims-transformations-reference) para obter uma lista completa dos valores disponíveis. |

## <a name="claimstransformation"></a>ClaimsTransformation

O elemento **ClaimsTransformation** contém os seguintes elementos:

```xml
<ClaimsTransformation Id="<identifier>" TransformationMethod="<method>">
  <InputClaims>
    ...
  </InputClaims>
  <InputParameters>
    ...
  </InputParameters>
  <OutputClaims>
    ...
  </OutputClaims>
</ClaimsTransformation>
```


| Elemento | Ocorrências | Descrição |
| ------- | -------- | ----------- |
| InputClaims | 0:1 | Uma lista dos elementos **InputClaim** que especificam os tipos de declaração que são usados como entrada para as transformação de declarações. Cada um desses elementos contém uma referência a um ClaimType já definido na seção ClaimsSchema na política. |
| InputParameters | 0:1 | Uma lista dos elementos **InputParameter** que são fornecidos como entrada para a transformação de declarações.
| OutputClaims | 0:1 | Uma lista dos elementos **OutputClaim** que especificam os tipos de declaração que são gerados depois que ClaimsTransformation é invocado. Cada um desses elementos contém uma referência a um ClaimType já definido na seção ClaimsSchema. |

### <a name="inputclaims"></a>InputClaims

O elemento **InputClaims** contém o seguinte elemento:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| InputClaim | 1:n | Um tipo de declaração de entrada esperado. |

#### <a name="inputclaim"></a>InputClaim

O elemento **InputClaim** contém os seguintes atributos:

| Atributo |Obrigatório | Descrição |
| --------- | ----------- | ----------- |
| ClaimTypeReferenceId |Sim | Uma referência a um ClaimType já definido na seção ClaimsSchema na política. |
| TransformationClaimType |Sim | Um identificador para fazer referência a um tipo de declaração de transformação. Cada transformação de declaração tem seus próprios valores. Confira a [Referência de transformação de declarações](#claims-transformations-reference) para obter uma lista completa dos valores disponíveis. |

### <a name="inputparameters"></a>InputParameters

O elemento **InputParameters** contém o seguinte elemento:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| InputParameter | 1:n | Um parâmetro de entrada esperado. |

#### <a name="inputparameter"></a>InputParameter

| Atributo | Obrigatório |Descrição |
| --------- | ----------- |----------- |
| ID | Sim | Um identificador que é uma referência a um parâmetro do método de transformação de declarações. Cada método de transformação de declarações tem seus próprios valores. Confira a tabela de transformação de declarações para obter uma lista completa dos valores disponíveis. |
| Tipo de dados | Sim | O tipo de dados do parâmetro, como String, Boolean, Int ou DateTime, de acordo com a enumeração DataType no esquema XML da política personalizada. Esse tipo é usado para executar operações aritméticas corretamente. Cada transformação de declaração tem seus próprios valores. Confira a [Referência de transformação de declarações](#claims-transformations-reference) para obter uma lista completa dos valores disponíveis. |
| Valor | Sim | Um valor que é passado de forma textual para a transformação. Alguns dos valores são arbitrários e alguns deles você seleciona no método de transformação de declarações. |

### <a name="outputclaims"></a>OutputClaims

O elemento **OutputClaims** contém o seguinte elemento:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| OutputClaim | 0:n | Um tipo de declaração de saída esperado. |

#### <a name="outputclaim"></a>OutputClaim

O elemento **OutputClaim** contém os seguintes atributos:

| Atributo |Obrigatório | Descrição |
| --------- | ----------- |----------- |
| ClaimTypeReferenceId | Sim | Uma referência a um ClaimType já definido na seção ClaimsSchema na política.
| TransformationClaimType | Sim | Um identificador para fazer referência a um tipo de declaração de transformação. Cada transformação de declaração tem seus próprios valores. Confira a [Referência de transformação de declarações](#claims-transformations-reference) para obter uma lista completa dos valores disponíveis. |

Se a declaração de saída e a declaração de entrada forem do mesmo tipo (String ou Boolean), você poderá usar a mesma declaração de entrada que a declaração de saída. Nesse caso, a transformação de declarações altera a declaração de entrada com o valor de saída.

## <a name="example"></a>Exemplo

Por exemplo, você pode armazenar a última versão dos termos de serviços que o usuário aceitou. Ao atualizar os termos de serviços, você pode solicitar que o usuário aceite a nova versão. No exemplo a seguir, a transformação de declarações **HasTOSVersionChanged** compara o valor da declaração **TOSVersion** com o valor da declaração **LastTOSAcceptedVersion** e, em seguida, retorna a declaração **TOSVersionChanged** booliana.

```xml
<BuildingBlocks>
  <ClaimsSchema>
    <ClaimType Id="TOSVersionChanged">
      <DisplayName>Indicates if the TOS version accepted by the end user is equal to the current version</DisplayName>
      <DataType>boolean</DataType>
    </ClaimType>
    <ClaimType Id="TOSVersion">
      <DisplayName>TOS version</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
    <ClaimType Id="LastTOSAcceptedVersion">
      <DisplayName>TOS version accepted by the end user</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
  </ClaimsSchema>

  <ClaimsTransformations>
    <ClaimsTransformation Id="HasTOSVersionChanged" TransformationMethod="CompareClaims">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="TOSVersion" TransformationClaimType="inputClaim1" />
        <InputClaim ClaimTypeReferenceId="LastTOSAcceptedVersion" TransformationClaimType="inputClaim2" />
      </InputClaims>
      <InputParameters>
        <InputParameter Id="operator" DataType="string" Value="NOT EQUAL" />
      </InputParameters>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="TOSVersionChanged" TransformationClaimType="outputClaim" />
      </OutputClaims>
    </ClaimsTransformation>
  </ClaimsTransformations>
</BuildingBlocks>
```

## <a name="claims-transformations-reference"></a>Referência de transformações de declarações

Para obter exemplos de transformações de declarações, confira as seguintes páginas de referência:

- [Booliano](boolean-transformations.md)
- [Data](date-transformations.md)
- [Inteiro](integer-transformations.md)
- [JSON](json-transformations.md)
- [Número de telefone](phone-number-claims-transformations.md)
- [Geral](general-transformations.md)
- [Conta social](social-transformations.md)
- [Cadeia de caracteres](string-transformations.md)
- [StringCollection](stringcollection-transformations.md)

