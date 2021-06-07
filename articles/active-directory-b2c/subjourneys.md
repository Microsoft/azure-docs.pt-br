---
title: Sub-viagens em Azure Active Directory B2C | Microsoft Docs
description: Especifique o elemento sub jornadas de uma política personalizada em Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 8f037d4283b4b05081ef47e7223495f6e19d460e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97386860"
---
# <a name="sub-journeys"></a>Sub-viagens

As subjornadas podem ser usadas para organizar e simplificar o fluxo de etapas de orquestração dentro de um percurso do usuário. [Percursos do usuário](userjourneys.md) especificam caminhos explícitos por meio dos quais uma política permite que um aplicativo de terceira parte confiável obtenhas as declarações desejadas para um usuário. O usuário passa por esses caminhos para recuperar as declarações que devem ser apresentadas para a terceira parte confiável. Em outras palavras, os percursos do usuário definem a lógica de negócios pela qual o usuário final passa conforme o Framework de Experiência de Identidade do Azure AD B2C processa a solicitação. Um percurso do usuário é representado como uma sequência de orquestração que deve ser seguida para ter uma transação bem-sucedida. O elemento [ClaimsExchange](userjourneys.md#claimsexchanges) de uma etapa de orquestração está vinculado a um único [perfil técnico](technicalprofiles.md) que é executado.

Uma sub-rotina é um agrupamento de etapas de orquestração que podem ser invocadas em qualquer ponto dentro de um percurso do usuário. Você pode usar sub jornadas para criar sequências de etapas reutilizáveis ou implementar a ramificação para representar melhor a lógica de negócios.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="user-journey-branching"></a>Ramificação de jornada do usuário

As contrajornadas se comportam como percursos do [usuário](userjourneys.md), pois ambas são representadas como uma sequência de orquestração que deve ser seguida por uma transação bem-sucedida. Os percursos do usuário podem ser chamados por conta própria e exigem uma etapa SendClaims para executar. As subjornadas são componentes de percursos do usuário e não podem ser invocados de forma independente e são sempre chamados de um percurso do usuário.

O principal componente da ramificação é permitir um melhor processamento de lógica de negócios em uma jornada do usuário. As etapas de orquestração comuns são agrupadas em partes individuais para serem chamadas separadamente. Uma sub-rotina pode simplificar uma jornada em que várias etapas de orquestração são agrupadas (tendo as mesmas pré-condições). Uma sub-rotina é chamada apenas de um percurso do usuário, ela não deve chamar outra sub-rotina.

Há dois tipos de trajetos:

- **Call** -retorna o controle para o chamador. A sub-rotina é executada e, em seguida, o controle é retornado para a etapa de orquestração que está sendo executada no momento do percurso do usuário.
- **Transferência** – transfere o controle para a sub-rotina (ramificação irreversível). A sub-rotina deve ter uma etapa SendClaims para retornar as declarações de volta para o aplicativo de terceira parte confiável.

## <a name="example-scenarios"></a>Cenários de exemplo

### <a name="call-sub-journey"></a>Chamar sub jornada

Uma sub-rotina de chamada é útil nos seguintes cenários:

- Retenção de idade: para a retenção de idade, há muitos componentes compartilhados entre os percursos do usuário. A ramificação permite compilar os elementos comuns em componentes compartilháveis.  
- Consentimento dos pais: a ramificação permite a conveniência no design de consentimento dos pais, permitindo que possamos acessar as declarações do percurso do usuário com a pequena execução, juntamente com a possibilidade de ramificação em um percurso do usuário de consentimento pelos pais depois de encontrar o usuário que exige consentimento. 
- Inscrever-se para entrar: Considere um cenário em que um usuário já existe no diretório, mas pode ter esquecido que eles tinham na verdade uma conta criada. Pode ser desejável que, em vez de informar ao usuário que as credenciais inseridas já existam e forçar o usuário a reiniciar a jornada de que a política é capaz de executar uma mudança de um fluxo de inscrição para um fluxo de entrada para esse usuário.  

### <a name="transfer-sub-journey"></a>Transferir subjornada

Uma sub-rotina de transferência é útil nos seguintes cenários:

- Mostrando uma página de bloco.
- Teste a/B roteando a solicitação para uma sub-rotina a ser executada e emitir um token.

## <a name="adding-a-subjourneys-element"></a>Adicionando um elemento subjornadas

Veja a seguir um exemplo de um `SubJourney` elemento do tipo `Call` , que retorna o controle de volta para o percurso do usuário.

```xml
<SubJourneys>
  <SubJourney Id="ConditionalAccess_Evaluation" Type="Call">
    <OrchestrationSteps>
      <OrchestrationStep Order="1" Type="ClaimsExchange">
       <ClaimsExchanges>
        <ClaimsExchange Id="ConditionalAccessEvaluation" TechnicalProfileReferenceId="ConditionalAccessEvaluation" />
       </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="2" Type="ClaimsExchange">
        <Preconditions>
          <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
            <Value>conditionalAccessClaimCollection</Value>
            <Action>SkipThisOrchestrationStep</Action>
          </Precondition>
        </Preconditions>
        <ClaimsExchanges>
          <ClaimsExchange Id="GenerateCAClaimFlags" TechnicalProfileReferenceId="GenerateCAClaimFlags" />
        </ClaimsExchanges>
      </OrchestrationStep>
    </OrchestrationSteps>
  </SubJourney>
</SubJourneys>
```

Veja a seguir um exemplo de um `SubJourney` elemento do tipo `Transfer` , que retorna um token de volta para o aplicativo de terceira parte confiável.

```xml
<SubJourneys>
  <SubJourney Id="B" Type="Transfer">
    <OrchestrationSteps>
      ...
      <OrchestrationStep Order="5" Type="SendClaims">
    </OrchestrationSteps>
  </SubJourney>
</SubJourneys>
```

### <a name="invoke-a-sub-journey-step"></a>Invocar uma etapa de sub jornada

Uma nova etapa de orquestração do tipo `InvokeSubJourney` é usada para executar uma sub-rotina. Veja a seguir um exemplo que mostra todos os elementos de execução desta etapa de orquestração.

```xml
<OrchestrationStep Order="5" Type="InvokeSubJourney">
  <JourneyList>
    <Candidate SubJourneyReferenceId="ConditionalAccess_Evaluation" />
  </JourneyList>
</OrchestrationStep>
```

> [!NOTE]
> Uma subjornada não deve chamar outra sub-rotina.

## <a name="components"></a>Componentes

Para definir as contrajornadas com suporte pela política, adicione um elemento **Subjornadas** sob o elemento de nível superior do arquivo de política.

O elemento **Subjornadas** contém o seguinte elemento:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| Subjornada | 1:n | Uma sub-rotina que define todas as construções necessárias para um fluxo de usuário completo. |

O elemento **Subjornadas** contém os seguintes atributos:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| ID | Sim | O identificador de sub jornada que pode ser usado pelo percurso do usuário para fazer referência à sub-rotina na política. O elemento **SubJourneyReferenceId** do elemento [candidato](userjourneys.md#journeylist) aponta para este atributo. |
| Type | Sim | Valores possíveis: `Call` , ou `Transfer` . Para obter mais informações, consulte [ramificação de jornada do usuário](#user-journey-branching)|

O elemento **subjornada** contém o seguinte elemento:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| OrchestrationSteps | 1:n | Uma sequência de orquestração que deve ser seguida para ter uma transação bem-sucedida. Cada percurso do usuário consiste de uma lista ordenada de etapas de orquestração que são executadas em sequência. Se alguma delas falhar, a transação falhará. |

## <a name="orchestrationsteps"></a>OrchestrationSteps

Para obter a lista completa dos elementos da etapa de orquestração, consulte [Userjornadas](userjourneys.md).

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o [Userjornadas](userjourneys.md)