---
title: BuildingBlocks – Azure Active Directory B2C | Microsoft Docs
description: Especifica o elemento BuildingBlocks de uma política personalizada no Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 5ab2b11749aa57065a1a4d688b02fed97731ab7c
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68464738"
---
# <a name="buildingblocks"></a>BuildingBlocks

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O elemento **BuildingBlocks** é adicionado dentro do elemento [TrustFrameworkPolicy](trustframeworkpolicy.md).

```XML
<TrustFrameworkPolicy
  xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="https://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="mytenant.onmicrosoft.com"
  PolicyId="B2C_1A_TrustFrameworkBase"
  PublicPolicyUri="http://mytenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase">

  <BuildingBlocks>
    <ClaimsSchema>
      ...
    </ClaimsSchema>
    <Predicates>
    ...
    </Predicates>
    <PredicateValidations>
    ...
    </PredicateValidations>
    <ClaimsTransformations>
      ...
    </ClaimsTransformations>
    <ContentDefinitions>
      ...
    </ContentDefinitions>
    <Localization>
      ...
    </Localization>
 </BuildingBlocks>
```

O elemento **BuildingBlocks** contém os seguintes elementos que precisam ser especificados na ordem definida:

- [ClaimsSchema](claimsschema.md) – define os tipos de declaração que podem ser referenciados como parte da política. O esquema de declarações é o lugar em que você declara seus tipos de declaração. Um tipo de declaração é semelhante a uma variável em muitas linguagens de programação. Você pode usar o tipo de declaração para coletar dados do usuário do aplicativo, receber declarações de provedores de identidade social, enviar e receber dados de uma API REST personalizada ou armazenar dados internos usados pela política personalizada. 

- [Predicados e PredicateValidationsInput](predicates.md) – permite que você execute um processo de validação para garantir que apenas os dados corretamente formados sejam inseridos em uma declaração.
 
- [ClaimsTransformations](claimstransformations.md) – contém uma lista de transformações de declarações que podem ser usadas em sua política.  Uma transformação de declarações converte uma declaração em outra. Na transformação de declarações, você especifica um método de transformação, como: 
    - Alterando o uso de maiúsculas e minúsculas de uma declaração de cadeia de caracteres para o especificado. Por exemplo, alterando uma cadeia de caracteres de letra minúscula para maiúscula.
    - Comparando duas declarações e retornando uma declaração com true, indicando que as declarações correspondem, caso contrário, false.
    - Criando uma declaração de cadeia de caracteres por meio do parâmetro fornecido na política.
    - Criando uma cadeia de caracteres aleatória usando o gerador de número aleatório.
    - Formatando uma declaração de acordo com a cadeia de caracteres de formato fornecida. Essa transformação usa o método C# `String.Format`.
    
- InputValidation-esse elemento permite que você execute agregações booleanas que são semelhantes a *and* e *or*.

- [ContentDefinitions](contentdefinitions.md) – contém URLs de modelos HTML5 a serem usado em seu percurso do usuário. Em uma política personalizada, uma definição de conteúdo define o URI da página HTML5 que é usado para uma etapa especificada no percurso do usuário. Por exemplo, a redefinição de senha de entrada ou de inscrição ou páginas de erro. Você pode modificar a aparência substituindo o LoadUri pelo arquivo HTML5. Ou você pode criar definições de conteúdo de acordo com suas necessidades. Esse elemento pode conter uma referência de recursos localizados usando uma ID de localização.

- [Localização](localization.md) – permite que você dê suporte a vários idiomas. O suporte de localização nas políticas permite que você configure a lista de idiomas com suporte em uma política e escolha um idioma padrão. Também há suporte para coleções e cadeias de caracteres específicas a um idioma.


