---
title: Projetar os fluxos de trabalho do Azure Policy como código
description: Aprenda a criar fluxos de trabalho para implantar suas definições do Azure Policy como código e validar automaticamente os recursos.
ms.date: 10/20/2020
ms.topic: conceptual
ms.openlocfilehash: 74d2097e4db4442e6e65f30541864fb554f7379d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94359673"
---
# <a name="design-azure-policy-as-code-workflows"></a>Projetar os fluxos de trabalho do Azure Policy como código

À media que você avança no percurso com Governança de Nuvem, convém mudar de gerenciar manualmente cada definição de política no portal do Azure ou por meio de vários SDKs para algo mais gerenciável e repetível em escala empresarial. Duas das abordagens predominantes para gerenciar sistemas em escala na nuvem são:

- Infraestrutura como código: a prática de tratar o conteúdo que define seus ambientes, tudo, desde modelos de Azure Resource Manager (modelos ARM) a Azure Policy definições para plantas do Azure, como código-fonte.
- DevOps: a união de pessoas, processos e produtos para permitir a entrega contínua de valor aos nossos usuários finais.

Azure Policy como código é a combinação dessas ideias. Essencialmente, mantenha suas definições de política no controle do código-fonte e sempre que uma alteração for feita, teste e valide essa alteração. No entanto, isso não deve ser a extensão do envolvimento das políticas com a Infraestrutura como Código ou DevOps.

A etapa de validação também deve ser um componente de outros fluxos de trabalho de integração contínua ou de implantação contínua. Os exemplos incluem a implantação de um ambiente de aplicativo ou de uma infraestrutura virtual. Ao fazer Azure Policy validação de um componente inicial do processo de compilação e implantação, as equipes de aplicativos e operações descobrirão se suas alterações não estiverem em conformidade, muito tempo antes que elas sejam muito atrasadas e estejam tentando implantar na produção.

## <a name="definitions-and-foundational-information"></a>Definições e informações básicas

Antes de entrar nos detalhes de Azure Policy como fluxo de trabalho de código, examine as seguintes definições e exemplos:

- [Definição de política](./definition-structure.md)
- [Definição de iniciativa](./initiative-definition-structure.md)

Os nomes de arquivo se alinham a partes da definição de política ou iniciativa:
- `policy(set).json` -Toda a definição
- `policy(set).parameters.json` -A `properties.parameters` parte da definição
- `policy.rules.json` -A `properties.policyRule` parte da definição
- `policyset.definitions.json` -A `properties.policyDefinitions` parte da definição

Exemplos desses formatos de arquivo estão disponíveis no [repositório GitHub Azure Policy](https://github.com/Azure/azure-policy/):

- Definição de política: [Adicionar uma marca aos recursos](https://github.com/Azure/azure-policy/tree/master/samples/Tags/add-tag)
- Definição de iniciativa: [marcas de cobrança](https://github.com/Azure/azure-policy/tree/master/samples/PolicyInitiatives/multiple-billing-tags)

## <a name="workflow-overview"></a>Visão geral do fluxo de trabalho

O fluxo de trabalho geral recomendado de Azure Policy como código é semelhante a este diagrama:

:::image type="complex" source="../media/policy-as-code/policy-as-code-workflow.png" alt-text="Diagrama mostrando Azure Policy caixas de fluxo de trabalho de código de criar para testar para implantar." border="false":::
   O diagrama que mostra o Azure Policy como caixas de fluxo de trabalho de código. Criar abrange a criação das definições de política e iniciativa. O teste aborda a atribuição com o modo de imposição desabilitado. Uma verificação de gateway para o status de conformidade é seguida, concedendo as permissões M S I e remediando os recursos.  Implantar abrange a atualização da atribuição com o modo de imposição habilitado.
:::image-end:::

### <a name="create-and-update-policy-definitions"></a>Criar e atualizar definições de política

As definições de política são criadas usando JSON e armazenadas no controle do código-fonte. Cada política tem seu próprio conjunto de arquivos, como parâmetros, regras e parâmetros de ambiente, que devem ser armazenados na mesma pasta. A estrutura a seguir é uma forma recomendada de manter suas definições de política no controle do código-fonte.

```text
.
|
|- policies/  ________________________ # Root folder for policy resources
|  |- policy1/  ______________________ # Subfolder for a policy
|     |- policy.json _________________ # Policy definition
|     |- policy.parameters.json ______ # Policy definition of parameters
|     |- policy.rules.json ___________ # Policy rule
|     |- assign.<name1>.json _________ # Assignment 1 for this policy definition
|     |- assign.<name2>.json _________ # Assignment 2 for this policy definition
|  |- policy2/  ______________________ # Subfolder for a policy
|     |- policy.json _________________ # Policy definition
|     |- policy.parameters.json ______ # Policy definition of parameters
|     |- policy.rules.json ___________ # Policy rule
|     |- assign.<name1>.json _________ # Assignment 1 for this policy definition
|     |- assign.<name2>.json _________ # Assignment 2 for this policy definition
|
```

Quando uma nova política é adicionada ou uma já existente atualizada, o fluxo de trabalho deve atualizar automaticamente a definição de política no Azure. O teste da definição de política nova ou atualizada vem em uma etapa posterior.

Além disso, examine [exportar recursos de Azure Policy](../how-to/export-resources.md) para obter suas definições e atribuições existentes no [GitHub](https://www.github.com)do ambiente de gerenciamento de código-fonte.

### <a name="create-and-update-initiative-definitions"></a>Criar e atualizar definições de iniciativa

Da mesma forma, as iniciativas têm seu próprio arquivo JSON e os arquivos relacionados que devem ser armazenados na mesma pasta. A definição de iniciativa requer que a definição de política já exista, portanto, não pode ser criada nem atualizada até que a origem da política tenha sido atualizada no controle do código-fonte e, em seguida, atualizada no Azure. A seguinte estrutura é uma forma recomendada de manter suas definições de iniciativa no controle do código-fonte:

```text
.
|
|- initiatives/ ______________________ # Root folder for initiatives
|  |- init1/ _________________________ # Subfolder for an initiative
|     |- policyset.json ______________ # Initiative definition
|     |- policyset.definitions.json __ # Initiative list of policies
|     |- policyset.parameters.json ___ # Initiative definition of parameters
|     |- assign.<name1>.json _________ # Assignment 1 for this policy initiative
|     |- assign.<name2>.json _________ # Assignment 2 for this policy initiative
|
|  |- init2/ _________________________ # Subfolder for an initiative
|     |- policyset.json ______________ # Initiative definition
|     |- policyset.definitions.json __ # Initiative list of policies
|     |- policyset.parameters.json ___ # Initiative definition of parameters
|     |- assign.<name1>.json _________ # Assignment 1 for this policy initiative
|     |- assign.<name2>.json _________ # Assignment 2 for this policy initiative
|
```

Assim como as definições de política, ao adicionar ou atualizar uma iniciativa existente, o fluxo de trabalho deve atualizar automaticamente a definição de iniciativa no Azure. O teste da definição de iniciativa nova ou atualizada vem em uma etapa posterior.

### <a name="test-and-validate-the-updated-definition"></a>Testar e validar a definição atualizada

Depois que a automação tiver obtido suas definições de política ou de iniciativa recém-criadas ou atualizadas e feito a atualização para o objeto no Azure, é hora de testar as alterações. A política ou as iniciativas da qual faz parte deve ser atribuída a recursos no ambiente mais distante da produção. Normalmente, esse ambiente é _Desenvolvimento_.

A atribuição deve usar [enforcementMode](./assignment-structure.md#enforcement-mode) de _disabled_ para que a criação de recursos e as atualizações não sejam bloqueadas, mas que os recursos existentes ainda sejam auditados quanto à conformidade com a definição de política atualizada. Mesmo com enforcementMode, é recomendável que o escopo de atribuição seja um grupo de recursos ou uma assinatura que seja especificamente para validar políticas.

> [!NOTE]
> Embora o modo de imposição seja útil, não é uma substituição para um teste completo de uma definição de política sob várias condições. A definição de política deve ser testada com chamadas à API REST `PUT` e `PATCH`, recursos compatíveis e não compatíveis e casos de borda como uma propriedade ausente do recurso.

Depois que a atribuição for implantada, use o SDK do Azure Policy, a [ação Azure Policy verificação de conformidade do GitHub](https://github.com/marketplace/actions/azure-policy-compliance-scan)ou a [tarefa de avaliação de segurança do Azure pipelines](/azure/devops/pipelines/tasks/deploy/azure-policy) para [obter dados de conformidade](../how-to/get-compliance-data.md) para a nova atribuição. O ambiente usado para testar as políticas e atribuições deve ter recursos compatíveis e não compatíveis.
Como um bom teste de unidade para o código, você deseja testar se os recursos estão conforme o esperado e se você também não tem falsos positivos ou falsos negativos. Se você testar e validar apenas o que espera, poderá haver um impacto inesperado e não identificado da política. Para obter mais informações, confira [Avaliar o impacto de uma nova definição de Azure Policy](./evaluate-impact.md).

### <a name="enable-remediation-tasks"></a>Habilitar tarefas de correção

Se a validação da atribuição atender às expectativas, a próxima etapa será validar a correção.
As políticas que usam o [deployIfNotExists](./effects.md#deployifnotexists) ou [modify](./effects.md#modify) podem ser transformadas em uma tarefa de correção e corrigir os recursos de um estado sem conformidade.

A primeira etapa para corrigir recursos é conceder à atribuição da política a atribuição de função definida na definição de política. Essa atribuição de função dá à identidade gerenciada da política direitos suficientes para fazer as alterações necessárias a fim de tornar o recurso compatível.

Depois que a atribuição de política tiver direitos apropriados, use o SDK do Policy para disparar uma tarefa de correção em um conjunto de recursos conhecidos como sem conformidade. Três testes devem ser concluídos em relação a essas tarefas remediadas antes de continuar:

- Validar que a tarefa de correção foi concluída com êxito
- Executar a avaliação de política para ver que os resultados de conformidade de política foram atualizados conforme o esperado
- Executar um teste de unidade de ambiente em relação aos recursos diretamente para validar que suas propriedades foram alteradas

Testar os resultados da avaliação da política atualizada e o ambiente fornecem diretamente a confirmação de que as tarefas de correção mudaram o que era esperado e que a definição da política observou a alteração de conformidade conforme o esperado.

### <a name="update-to-enforced-assignments"></a>Atualizar para atribuições impostas

Após a conclusão de todos os portões de validação, atualize a atribuição para usar **enforcementMode** de _enabled_. É recomendável fazer essa alteração inicialmente no mesmo ambiente longe da produção. Depois que o ambiente é validado como funcionando conforme o esperado, a alteração deve ser delimitada para incluir o próximo ambiente e assim por diante, até que a política seja implantada nos recursos de produção.

## <a name="process-integrated-evaluations"></a>Processar avaliações integradas

O fluxo de trabalho geral para Azure Policy como código é para desenvolver e implantar políticas e iniciativas em um ambiente em escala. No entanto, a avaliação de política deve fazer parte do processo de implantação para qualquer fluxo de trabalho que implanta ou cria recursos no Azure, como a implantação de aplicativos ou a execução de modelos ARM para criar a infraestrutura.

Nesses casos, depois que a implantação do aplicativo ou da infraestrutura for realizada em um grupo de recursos ou em uma assinatura de teste, a avaliação da política deverá ser feita para esse escopo verificando a validação de todas as políticas e iniciativas existentes. Embora elas possam ser configuradas como **enforcementMode** _disabled_ nesse ambiente, é útil saber antecipadamente se uma implantação de aplicativo ou de infraestrutura está violando as definições de política no início. Essa avaliação de política deve, portanto, ser uma etapa nesses fluxos de trabalho e falha nas implantações que criam recursos sem conformidade.

## <a name="review"></a>Revisão

Este artigo aborda o fluxo de trabalho geral para Azure Policy como código e também onde a avaliação da política deve fazer parte de outros fluxos de trabalho de implantação. Esse fluxo de trabalho pode ser usado em qualquer ambiente que dê suporte a etapas e automação com script com base em gatilhos. Para obter um tutorial sobre como usar esse fluxo de trabalho no GitHub, consulte [tutorial: implementar Azure Policy como código com o GitHub](../tutorials/policy-as-code-github.md).

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre a [estrutura da definição de política](./definition-structure.md).
- Saiba mais sobre a [estrutura de atribuição de política](./assignment-structure.md).
- Entenda como [criar políticas de maneira programática](../how-to/programmatically-create.md).
- Saiba como [obter dados de conformidade](../how-to/get-compliance-data.md).
- Saiba como [corrigir recursos fora de conformidade](../how-to/remediate-resources.md).
- Veja o que é um grupo de gerenciamento com [Organizar seus recursos com grupos de gerenciamento do Azure](../../management-groups/overview.md).
