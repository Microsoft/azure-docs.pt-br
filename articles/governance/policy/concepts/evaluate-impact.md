---
title: Avaliar o impacto de uma nova definição de Azure Policy
description: Entenda o processo a ser seguido ao introduzir uma nova definição de política em seu ambiente do Azure.
ms.date: 10/05/2020
ms.topic: conceptual
ms.openlocfilehash: 9d73d703c38dce1335a471bfad9171d8b30a83c5
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91873860"
---
# <a name="evaluate-the-impact-of-a-new-azure-policy-definition"></a>Avaliar o impacto de uma nova definição de Azure Policy

O Azure Policy é uma ferramenta poderosa para gerenciar seus recursos do Azure, fazendo com que eles atendam a padrões de negócios e necessidades de conformidade. Quando as pessoas, os processos ou os pipelines criam ou atualizam recursos, o Azure Policy examina a solicitação. Quando o efeito de definição de política é [Modificar](./effects.md#modify), [acrescentar](./effects.md#deny) ou [DeployIfNotExists](./effects.md#deployifnotexists), a política altera a solicitação ou a adiciona a ela. Quando o efeito de definição de política é [Audit](./effects.md#audit) ou [AuditIfNotExists](./effects.md#auditifnotexists), Policy faz com que uma entrada do log de atividades seja criada para recursos novos e atualizados. E quando o efeito de definição de política é [Negar](./effects.md#deny), o Policy interrompe a criação ou alteração da solicitação.

Esses resultados são exatamente os desejados quando você sabe que a política está definida corretamente. No entanto, é importante validar que uma nova política funcione como pretendido antes de permitir que ela altere ou bloqueie o trabalho. A validação deve garantir que apenas os recursos pretendidos sejam determinados como fora de conformidade e que nenhum recurso em conformidade seja incluído incorretamente (conhecido como um _falso positivo_) nos resultados.

A abordagem recomendada para validar uma nova definição de política é seguir estas etapas:

- Definir sua política de maneira estrita
- Auditar os recursos existentes
- Auditar solicitações de recursos novas ou atualizadas
- Implantar sua política para recursos
- Monitoramento contínuo

## <a name="tightly-define-your-policy"></a>Definir sua política de maneira estrita

É importante entender como a política de negócios é implementada como uma definição de política e a relação dos recursos do Azure com outros serviços do Azure. Essa etapa é realizada [identificando os requisitos](../tutorials/create-custom-policy-definition.md#identify-requirements) e [determinando as propriedades do recurso](../tutorials/create-custom-policy-definition.md#determine-resource-properties).
Mas também é importante ver além da definição superficial de sua política de negócios. Por exemplo, por acaso sua política declara que "Todas as máquinas virtuais precisam..."? E quanto a outros serviços do Azure que fazem uso de VMs, como HDInsight ou AKS? Ao definir uma política, devemos considerar como ela afeta os recursos que são usados por outros serviços.

Por esse motivo, as definições de política devem ser tão focadas nos recursos e nas propriedades que você precisa avaliar para obter a conformidade e tão estritamente definidas quanto possível.

## <a name="audit-existing-resources"></a>Auditar recursos existentes

Antes de procurar gerenciar recursos novos ou atualizados com sua nova definição de política, é melhor ver como ela avalia um subconjunto limitado de recursos existentes, como um grupo de recursos de teste. Use o [modo de imposição](./assignment-structure.md#enforcement-mode) _Desabilitado_ (DoNotEnforce) em sua atribuição de política para impedir que o [efeito](./effects.md) seja disparado ou que entradas do log de atividades sejam criadas.

Esta etapa lhe dá a chance de avaliar os resultados de conformidade da nova política nos recursos existentes sem afetar o fluxo de trabalho. Verifique se nenhum recurso em conformidade está marcado como fora de conformidade (_falso positivo_) e se todos os recursos que você espera que não estejam em conformidade estão marcados corretamente.
Depois que o subconjunto inicial de recursos é validado conforme o esperado, expanda lentamente a avaliação para todos os recursos existentes.

A avaliação de recursos existentes realizada desse modo também oferece uma oportunidade de corrigir recursos fora de conformidade antes da implementação completa da nova política. Essa limpeza poderá ser feita manualmente ou por meio de uma [tarefa de correção](../how-to/remediate-resources.md) se o efeito de definição de política for _DeployIfNotExists_.

## <a name="audit-new-or-updated-resources"></a>Auditar recursos novos ou atualizados

Depois de validar que sua nova definição de política está relatando corretamente os recursos existentes, é hora de examinar o impacto da política quando os recursos são criados ou atualizados. Se a definição de política der suporte à parametrização de efeito, use [Audit](./effects.md#audit). Essa configuração permite que você monitore a criação e a atualização de recursos para ver se a nova definição de política dispara uma entrada no log de atividades do Azure para um recurso que não está em conformidade sem afetar o trabalho ou as solicitações existentes.

É recomendável atualizar e criar recursos que correspondam à definição de política para ver que o efeito _Audit_ efeito está sendo disparado corretamente quando esperado. Esteja atento a solicitações de recursos que não devem ser afetadas pela nova definição de política e que disparam o efeito _Audit_.
Esses recursos afetados são outro exemplo de _falsos positivos_ e precisam ser corrigidos na definição de política antes da implementação completa.

No caso de a definição de política ser alterada neste estágio de teste, é recomendável iniciar o processo de validação com a auditoria de recursos existentes. Uma alteração na definição de política para um _falso positivo_ em recursos novos ou atualizados provavelmente também terá um impacto sobre os recursos existentes.

## <a name="deploy-your-policy-to-resources"></a>Implantar sua política para recursos

Depois de concluir a validação da nova definição de política com recursos existentes e solicitações de recursos novas ou atualizadas, você inicia o processo de implementação da política. É recomendável criar a atribuição de política da nova definição de política primeiro para um subconjunto de todos os recursos, como um grupo de recursos. Depois de validar a implantação inicial, estenda o escopo da política para níveis cada vez mais amplos, como assinaturas e grupos de gerenciamento. Essa expansão é obtida removendo a atribuição e criando outra nos escopos de destino até que ela seja atribuída ao escopo completo dos recursos que devem ser cobertos pela nova definição de política.

Durante a distribuição, se houver recursos localizados que deveriam ser isentos da nova definição de política, solucione a situação deles de uma das seguintes maneiras:

- Atualizar a definição de política para ser mais explícita a fim de reduzir o impacto indesejado
- Alterar o escopo da atribuição de política (removendo a atribuição e criando outra)
- Adicionar o grupo de recursos à lista de exclusões para a atribuição de política

Todas as alterações no escopo (nível ou exclusões) devem ser totalmente validadas e comunicadas com suas organizações de segurança e conformidade para garantir que não haja nenhuma lacuna na cobertura.

## <a name="monitor-your-policy-and-compliance"></a>Monitore sua política e conformidade

A implementação e a atribuição da definição de política não é a etapa final. Monitore continuamente o nível de [conformidade](../how-to/get-compliance-data.md) dos recursos para sua nova definição de política e configure [alertas e notificações apropriados do Azure Monitor](../../../azure-monitor/platform/alerts-overview.md) para quando dispositivos não compatíveis forem identificados. Também é recomendável avaliar a definição de política e as atribuições relacionadas segundo um agendamento para validar que a definição de política esteja atendendo às necessidades de conformidade e à política de negócios. As políticas devem ser removidas se não forem mais necessárias. As políticas também precisam ser atualizadas de tempos em tempos à medida que os recursos subjacentes do Azure evoluem e adicionam novas propriedades e funcionalidades.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre a [estrutura da definição de política](./definition-structure.md).
- Saiba mais sobre a [estrutura de atribuição de política](./assignment-structure.md).
- Entenda como [criar políticas de maneira programática](../how-to/programmatically-create.md).
- Saiba como [obter dados de conformidade](../how-to/get-compliance-data.md).
- Saiba como [corrigir recursos fora de conformidade](../how-to/remediate-resources.md).
- Veja o que é um grupo de gerenciamento com [Organizar seus recursos com grupos de gerenciamento do Azure](../../management-groups/overview.md).