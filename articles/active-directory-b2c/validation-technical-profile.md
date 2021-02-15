---
title: Definir um perfil técnico de validação em uma política personalizada
titleSuffix: Azure AD B2C
description: Valide as declarações usando um perfil técnico de validação em uma política personalizada no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/16/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2d4c538a9292698fecc8b44c055ab201748e292c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85202986"
---
# <a name="define-a-validation-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Defina um perfil técnico de validação em uma política personalizada do Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Um perfil técnico de validação é um perfil técnico comum de qualquer protocolo, como [Azure Active Directory](active-directory-technical-profile.md) ou um [API REST](restful-technical-profile.md). O perfil técnico de validação retorna declarações de saída ou retorna o código de status HTTP 4xx, com os dados a seguir. Para obter mais informações, consulte [retornando mensagem de erro](restful-technical-profile.md#returning-validation-error-message)

```json
{
    "version": "1.0.0",
    "status": 409,
    "userMessage": "Your error message"
}
```

O escopo das declarações de saída de um perfil técnico de validação é limitado ao [perfil técnico autodeclarado](self-asserted-technical-profile.md) que invoca o perfil técnico de validação e seus perfis técnicos de validação. Se você quiser usar as declarações de saída na próxima etapa de orquestração, adicione as declarações de saída ao perfil técnico autodeclarado que invoca o perfil técnico de validação.

Perfis técnicos de validação são executados na sequência que aparecem no elemento **ValidationTechnicalProfiles**. É possível configurar em um perfil técnico de validação se a execução dos perfis técnicos de validação subsequente devem continuar se os perfis técnicos de validação gerarem um erro ou forem bem sucedidos.

Um perfil técnico de validaçaõ pode ser executado condicionalmente com base em pré-condições definidas no elemento **ValidationTechnicalProfile**. Por exemplo, você pode verificar se uma declaração específica existe ou se uma declaração é igual ou não ao valor especificado.

Um perfil técnico autodeclarado pode definir um perfil técnico de validação a ser usado para validar algumas ou todas as suas declarações de saída. Todas as declarações de entrada do perfil técnico referenciado precisam aparecer nas declarações de saída do perfil técnico de referência.

> [!NOTE]
> Somente perfis técnicos autodeclarados podem usar perfis técnicos de validação. Se você precisar validar as declarações de saída de perfis técnicos não autodeclarados, considere o uso de uma etapa de orquestração adicional no percurso do usuário para acomodar o perfil técnico responsável pela validação.

## <a name="validationtechnicalprofiles"></a>ValidationTechnicalProfiles

O elemento **ValidationTechnicalProfiles** contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| ValidationTechnicalProfile | 1:n | Um perfil técnico a ser usado para validar algumas ou todas as declarações de saída do perfil técnico de referência. |

O elemento **ValidationTechnicalProfile** contém o seguinte atributo:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| ReferenceId | Sim | Um identificador de um perfil técnico já definido na política ou política pai. |
|ContinueOnError|Não| Indicando se a validação de quaisquer perfis técnicos de validação subsequentes deve continuar se esse perfil técnico de validação gerar um erro. Valores possíveis: `true` ou `false` (padrão, o processamento de perfis de validação adicional será interrompido e retornará um erro). |
|ContinueOnSuccess | Não | Indica se a validação de qualquer perfil de validação subsequente deve continuar se esse perfil técnico de validação for bem-sucedio. Valores possíveis: `true` ou `false`. O padrão é `true`, significando que continuará o processamento dos perfis de validação adicional. |

O elemento **ValidationTechnicalProfile** contém o seguinte elemento:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| Pré-condições | 0:1 | Uma lista de pré-condições deve ser atendida para o perfil técnico de validação para executar. |

O elemento **Precondition** contém os seguinte atributo:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| `Type` | Sim | O tipo de verificação ou consulta ser executada para a pré-condição. Qualquer um dos `ClaimsExist` é especificado para garantir que as ações devem ser realizadas se as declarações especificadas existem no atual conjunto de declarações do usuário, ou `ClaimEquals` for especificado que as ações devem ser executadas se a declaração especificada existe e seu valor é igual ao valor especificado. |
| `ExecuteActionsIf` | Sim | Indica se as ações na pré-condição devem ser executadas se o teste for verdadeiro ou falso. |

O elemento **Precondition** contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| Valor | 1:n | Os dados que são usados pela verificação. Se o tipo dessa verificação for `ClaimsExist`, este campo especifica um ClaimTypeReferenceId para consultar. Se o tipo dessa verificação for `ClaimEquals`, este campo especifica um ClaimTypeReferenceId para consultar. Enquanto outro elemento de valor contém o valor a ser verificado.|
| Ação | 1:1 | A ação que deverá ser executada se a verificação de pré-condição dentro de uma etapa de orquestração for verdadeira. O valor da **Ação** é definido como `SkipThisValidationTechnicalProfile`. Especifica que o perfil técnico de validação associada não deve ser executado. |

### <a name="example"></a>Exemplo

O exemplo a seguir usa esses perfis técnicos de validação:

1. O primeiro perfil técnico de validação verifica as credenciais do usuário e não continua se ocorrer um erro, como o nome de usuário inválido ou senha incorreta.
2. O próximo perfil técnico de validação, não é executado se a declaração userType não existir ou se o valor de userType for `Partner`. O perfil técnico de validação tenta ler o perfil do usuário do banco de dados do cliente interno e continuar se ocorrer um erro, como não disponíveis do serviço de API REST ou qualquer erro interno.
3. O último perfil técnico de validação, não é executado se a declaração userType não existia ou se o valor de userType for `Customer`. O perfil técnico de validação tenta ler o perfil do usuário do banco de dados interno do parceiro e continuará, se ocorrer um erro como serviço de API REST não disponível ou qualquer erro interno.

```xml
<ValidationTechnicalProfiles>
  <ValidationTechnicalProfile ReferenceId="login-NonInteractive" ContinueOnError="false" />
  <ValidationTechnicalProfile ReferenceId="REST-ReadProfileFromCustomertsDatabase" ContinueOnError="true" >
    <Preconditions>
      <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
        <Value>userType</Value>
        <Action>SkipThisValidationTechnicalProfile</Action>
      </Precondition>
      <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
        <Value>userType</Value>
        <Value>Partner</Value>
        <Action>SkipThisValidationTechnicalProfile</Action>
      </Precondition>
    </Preconditions>
  </ValidationTechnicalProfile>
  <ValidationTechnicalProfile ReferenceId="REST-ReadProfileFromPartnersDatabase" ContinueOnError="true" >
    <Preconditions>
      <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
        <Value>userType</Value>
        <Action>SkipThisValidationTechnicalProfile</Action>
      </Precondition>
      <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
        <Value>userType</Value>
        <Value>Customer</Value>
        <Action>SkipThisValidationTechnicalProfile</Action>
      </Precondition>
    </Preconditions>
  </ValidationTechnicalProfile>
</ValidationTechnicalProfiles>
```
