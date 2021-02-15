---
title: Definir um perfil técnico de fator de telefone em uma política personalizada
titleSuffix: Azure AD B2C
description: Defina um perfil técnico de fator de telefone em uma política personalizada no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/12/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 322e4b78fbfb38f1822fb7a7cdcdbfcc0738b303
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/12/2020
ms.locfileid: "91950387"
---
# <a name="define-a-phone-factor-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definir um perfil técnico de fator de telefone em uma Azure Active Directory B2C política personalizada

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O Azure Active Directory B2C (Azure AD B2C) fornece suporte para registro e verificação de números de telefone. Este perfil técnico:

- Fornece uma interface do usuário para interagir com o usuário para verificar ou registrar um número de telefone.
- Dá suporte a chamadas telefônicas e mensagens de texto para validar o número de telefone.
- Dá suporte a vários números de telefone. O usuário pode selecionar um dos números de telefone para verificar.  
- Retorna uma declaração que indica se o usuário forneceu um novo número de telefone. Você pode usar essa declaração para decidir se o número de telefone deve persistir para o Azure AD B2C perfil de usuário.  
- Usa uma [definição de conteúdo](contentdefinitions.md) para controlar a aparência.

## <a name="protocol"></a>Protocolo

O atributo **Name** do elemento **Protocol** precisa ser definido como `Proprietary`. O atributo do **manipulador** deve conter o nome totalmente qualificado do assembly do manipulador de protocolo que é usado pelo Azure ad B2C para o fator de telefone: `Web.TPEngine.Providers.PhoneFactorProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

O exemplo a seguir mostra um perfil técnico de fator de telefone para registro e validação:

```xml
<TechnicalProfile Id="PhoneFactor-InputOrVerify">
  <DisplayName>PhoneFactor</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.PhoneFactorProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

## <a name="input-claims-transformations"></a>Transformações de declarações de entrada

O elemento InputClaimsTransformations pode conter uma coleção de transformações de declarações de entrada que são usadas para modificar as declarações de entrada ou gerar novas. A transformação de declarações de entrada a seguir gera uma `UserId` declaração que é usada posteriormente na coleção de declarações de entrada.

```xml
<InputClaimsTransformations>
  <InputClaimsTransformation ReferenceId="CreateUserIdForMFA" />
</InputClaimsTransformations>
```

## <a name="input-claims"></a>Declarações de entrada

O elemento InputClaims deve conter as declarações a seguir. Você também pode mapear o nome da sua declaração para o nome definido no perfil técnico do fator de telefone. 

|  Tipo de dados| Necessária | Descrição |
| --------- | -------- | ----------- | 
| string| Sim | Um identificador exclusivo do usuário. O nome da declaração ou PartnerClaimType deve ser definido como `UserId` . Essa declaração não deve conter informações de identificação pessoal.|
| cadeia de caracteres| Sim | Lista de tipos de declaração. Cada declaração contém um número de telefone. Se qualquer uma das declarações de entrada não contiverem um número de telefone, o usuário será solicitado a registrar e verificar um novo número de telefone. O número de telefone validado é retornado como uma declaração de saída. Se uma das declarações de entrada contiver um número de telefone, o usuário será solicitado a verificá-lo. Se várias declarações de entrada contiverem um número de telefone, o usuário será solicitado a escolher e verificar um dos números de telefone. |

O exemplo a seguir demonstra como usar vários números de telefone. Para obter mais informações, consulte [exemplo de política](https://github.com/azure-ad-b2c/samples/tree/master/policies/mfa-add-secondarymfa).

```xml
<InputClaims>
  <InputClaim ClaimTypeReferenceId="userIdForMFA" PartnerClaimType="UserId" />
  <InputClaim ClaimTypeReferenceId="strongAuthenticationPhoneNumber" />
  <InputClaim ClaimTypeReferenceId="secondaryStrongAuthenticationPhoneNumber" />
</InputClaims>
```

## <a name="output-claims"></a>Declarações de saída

O elemento OutputClaims contém uma lista de declarações retornadas pelo perfil técnico do fator de telefone.

|  Tipo de dados| Necessária | Descrição |
|  -------- | ----------- |----------- |
| booleano | Sim | Indica se o novo número de telefone foi inserido pelo usuário. O nome da declaração ou PartnerClaimType deve ser definido como `newPhoneNumberEntered`|
| cadeia de caracteres| Sim | O número de telefone verificado. O nome da declaração ou PartnerClaimType deve ser definido como `Verified.OfficePhone` .|

O elemento OutputClaimsTransformations pode conter uma coleção de elementos OutputClaimsTransformation que são usados para modificar as declarações de saída ou gerar novas.

## <a name="cryptographic-keys"></a>Chaves criptográficas

O elemento **CryptographicKeys** não será usado.


## <a name="metadata"></a>Metadados

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| ContentDefinitionReferenceId | Sim | O identificador da [definição de conteúdo](contentdefinitions.md) associada com este perfil técnico. |
| ManualPhoneNumberEntryAllowed| Não | Especifique se um usuário tem ou não permissão para inserir manualmente um número de telefone. Valores possíveis: `true` ou `false` (padrão).|
| Configurando. AuthenticationMode | Não | O método para validar o número de telefone. Valores possíveis: `sms` , `phone` ou `mixed` (padrão).|
| configuração. AutoDial| Não| Especifique se o perfil técnico deve discar automaticamente ou enviar automaticamente um SMS. Valores possíveis: `true` ou `false` (padrão). A discagem automática exige que os `setting.authenticationMode` metadados sejam definidos como `sms` , ou `phone` . A coleção de declarações de entrada deve ter um único número de telefone. |

### <a name="ui-elements"></a>Elementos da interface do usuário

Os elementos da interface do usuário da página de autenticação do fator de telefone podem ser [localizados](localization-string-ids.md#phone-factor-authentication-page-user-interface-elements).

## <a name="next-steps"></a>Próximas etapas

- Verifique as [contas sociais e locais com](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccountsWithMfa) o pacote de inicialização MFA.
