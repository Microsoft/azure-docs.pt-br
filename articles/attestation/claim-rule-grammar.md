---
title: Gramática da regra de declaração do Atestado do Azure
description: Detalhes sobre as declarações de política e as regras de declaração do Atestado do Azure.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 3ed5c3f8232047787c6f05628f1eef35a7533999
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91285411"
---
# <a name="claim-and-claim-rules"></a>Declaração e regras de declaração

Para entender a gramática das regras de declaração, primeiro você precisará entender as declarações de política de atestado.

## <a name="claim"></a>Declaração

Uma declaração é um conjunto de propriedades agrupadas para fornecer informações relevantes. No Atestado do Azure, uma declaração contém as seguintes propriedades:

- **type**: um valor de cadeia de caracteres que representa o tipo da declaração.
- **value**: um valor booliano, um inteiro ou um valor de cadeia de caracteres que representa o valor da declaração.
- **valueType**: o tipo de dados das informações armazenadas na propriedade value. Os tipos compatíveis são String, Integer e Boolean. Se o tipo não estiver definido, o valor padrão será "String".
- **issuer**: informações sobre o emissor da declaração. O emissor será um dos seguintes tipos:
  - **AttestationService**: algumas declarações são disponibilizadas para o autor da política pelo Atestado do Azure, que podem ser usadas pelo autor da política de atestado para criar a política apropriada.
  - **AttestationPolicy**: a própria política (conforme definido pelo administrador) pode adicionar declarações à evidência de entrada durante o processamento. O emissor, nesse caso, é definido como "AttestationPolicy".
  - **CustomClaim**: o atestador (cliente) também pode adicionar outras declarações à evidência de atestado. O emissor, nesse caso, é definido como "CustomClaim".

Se ele não estiver definido. o valor padrão será "CustomClaim".

## <a name="claim-rule"></a>Regra de declaração

O conjunto de declarações de entrada é usado pelo mecanismo de política para calcular o resultado do atestado. Uma regra de declaração é um conjunto de condições usadas para validar as declarações de entrada e executar a ação definida.

```
Conditions list => Action (Claim)
```

A avaliação do Atestado do Azure de uma regra de declaração envolve as seguintes etapas:

- Se a lista de condições não estiver presente, execute a ação com a declaração especificada 
- Caso contrário, avalie as condições na lista de condições.
- Se a lista de condições for avaliada como falsa, interrompa o procedimento. Caso contrário, continue.

As condições de uma regra de declaração são usadas para determinar se a ação precisa ser executada. A lista de condições é uma sequência de condições separadas pelo operador "&&".

A lista de condições é estruturada como:

```
Condition && Condition && ...
```

A condição é estruturada como:

```
Identifier:[ClaimPropertyCondition, ClaimPropertyCondition,…]
```

A lista de condições é composta por condições individuais em várias propriedades de uma declaração. Uma condição pode ter um identificador opcional, que pode ser usado para referenciar as declarações que atendem à condição. Essa referência pode ser usada em outras condições ou na ação da mesma regra.

Por exemplo,

```
F1:[type=="OSName" , issuer=="CustomClaim"] && 
[type=="OSName" , issuer=="AttestationService", value== F1.value ] 
=> issueproperty(type="report_validity_in_minutes", value=1440);

F1:[type=="OSName" , issuer=="CustomClaim"] && 
C2:[type=="OSName" , issuer=="AttestationService", value== F1.value ] 
=> issue(claim = C2);
```

Estes são os operadores que podem ser usados para verificar as condições:

| Valuetype | Operações compatíveis |
|--|--|
| Integer | == (igual a), \!= (diferente de), <= (inferior ou igual a), < (menor que), >= (superior ou igual a), > (maior que) |
| String | == (igual a), \!= (diferente de) |
| Boolean | == (igual a), \!= (diferente de) |

Avaliação da lista de condições:

- A presença do operador "&&" implica que uma lista de condições será avaliada como verdadeira somente se todas as condições da lista forem avaliadas como verdadeiras.
- Uma condição representa os critérios de filtragem no conjunto de declarações. A própria condição é considerada como verdadeira se há, pelo menos, uma declaração que atenda à condição.
- Uma declaração atende ao critério de filtragem representado pela condição se cada uma das propriedades atende às condições da propriedade de declaração correspondentes presentes na condição.  

O conjunto de ações permitidas em uma política é descrito abaixo.

| Verbo de ação | Descrição | Seções da política às quais elas se aplicam |
|--|--|--|
| permit() | O conjunto de declarações de entrada pode ser usado para calcular **issuancerules**. Não usa nenhuma declaração como parâmetro | **authorizationrules** |
| deny() | O conjunto de declarações de entrada não deve ser usado para calcular **issuancerules**. Não usa nenhuma declaração como parâmetro | **authorizationrules** |
| add(claim) | Adiciona a declaração ao conjunto de declarações de entrada. Qualquer declaração adicionada ao conjunto de declarações de entrada estará disponível para as próximas regras de declaração. |**authorizationrules**, **issuancerules** |
| issue(claim) | Adiciona a declaração ao conjunto de declarações de entrada e saída | **issuancerules** |
| issueproperty(claim) | Adiciona a declaração ao conjunto de declarações de entrada da propriedade | **issuancerules**

## <a name="next-steps"></a>Próximas etapas

- [Como criar e assinar uma política de atestado](author-sign-policy.md)
- [Configurar o Atestado do Azure usando o PowerShell](quickstart-powershell.md)

