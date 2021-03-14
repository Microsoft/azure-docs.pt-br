---
title: Determinar as causas de não conformidade
description: Quando um recurso não está em conformidade, há muitos motivos possíveis. Saiba como descobrir o que causou a não conformidade.
ms.date: 09/30/2020
ms.topic: how-to
ms.openlocfilehash: a8168bf22aceaf5cbdec4b1346801aa62b7aa4ee
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102439826"
---
# <a name="determine-causes-of-non-compliance"></a>Determinar as causas de não conformidade

Quando um recurso do Azure é determinado como estando em não conformidade com uma regra de política, é importante entender a parte da regra com a qual o recurso não está em conformidade. Também é útil entender qual alteração alterou um recurso de forma que ele se ficasse em não conformidade. Há duas maneiras de encontrar essas informações:

- [Detalhes de conformidade](#compliance-details)
- [Alterar histórico (versão preliminar)](#change-history)

## <a name="compliance-details"></a>Detalhes de conformidade

Quando um recurso não está em conformidade, os detalhes de conformidade desse recurso estão disponíveis na página **Conformidade da política**. O painel de detalhes de conformidade inclui as seguintes informações:

- Detalhes do recurso, como nome, tipo, local e ID do recurso
- Estado de conformidade e carimbo de data/hora da última avaliação para a atribuição de política atual
- Uma lista de _motivos_ para a não conformidade do recurso

> [!IMPORTANT]
> Como os detalhes de conformidade de um recurso em _não conformidade_ mostram o valor atual das propriedades nesse recurso, o usuário deve ter a operação de **leitura** para o **tipo** do recurso. Por exemplo, se o recurso de em _não conformidade_ for **Microsoft.Compute/virtualMachines**, o usuário deverá ter a operação **Microsoft.Compute/virtualMachines/read**. Se o usuário não tiver a operação necessária, um erro de acesso será exibido.

Para exibir os detalhes de conformidade, siga estas etapas:

1. Inicie o serviço do Azure Policy no portal do Azure selecionando **Todos os serviços** e, em seguida, pesquisando e selecionando **Política**.

1. Na página **Visão geral** ou **Conformidade**, escolha uma política em um **estado de conformidade** que seja de _Não conformidade_.

1. Na guia **Conformidade de recursos** da página **Conformidade de política**, clique com o botão direito do mouse ou selecione as reticências de um recurso em um **estado de conformidade** que seja de _Não conformidade_. Em seguida, selecione **Exibir detalhes de conformidade**.

   :::image type="content" source="../media/determine-non-compliance/view-compliance-details.png" alt-text="Captura de tela do link ' Exibir detalhes de conformidade ' na guia conformidade de recursos." border="false":::

1. O painel **Detalhes de conformidade** exibe informações da avaliação mais recente do recurso para a atribuição de política atual. Neste exemplo, o campo **Microsoft.Sql/servers/version** é considerado como _12.0_ enquanto a definição de política esperada era de _14.0_. Se o recurso não estiver em conformidade por vários motivos, cada um será listado nesse painel.

   :::image type="content" source="../media/determine-non-compliance/compliance-details-pane.png" alt-text="Captura de tela do painel detalhes de conformidade e motivos para não conformidade que o valor atual é doze e o valor de destino é quatorze." border="false":::

   Para uma definição de política **auditIfNotExists** ou **deployIfNotExists**, os detalhes incluem a propriedade **details.type** e todas as propriedades opcionais. Para obter uma lista, confira [Propriedades auditIfNotExists](../concepts/effects.md#auditifnotexists-properties) e [Propriedades deployIfNotExists](../concepts/effects.md#deployifnotexists-properties). **Último recurso avaliado** é um recurso relacionado da seção **detalhes** da definição.

   Exemplo de definição **deployIfNotExists** parcial:

   ```json
   {
       "if": {
           "field": "type",
           "equals": "[parameters('resourceType')]"
       },
       "then": {
           "effect": "DeployIfNotExists",
           "details": {
               "type": "Microsoft.Insights/metricAlerts",
               "existenceCondition": {
                   "field": "name",
                   "equals": "[concat(parameters('alertNamePrefix'), '-', resourcegroup().name, '-', field('name'))]"
               },
               "existenceScope": "subscription",
               "deployment": {
                   ...
               }
           }
       }
   }
   ```

   :::image type="content" source="../media/determine-non-compliance/compliance-details-pane-existence.png" alt-text="Captura de tela do painel detalhes de conformidade para ifNotExists, incluindo a contagem de recursos avaliados." border="false":::

> [!NOTE]
> Para proteger os dados, quando um valor de propriedade for um _segredo_, o valor atual exibirá asteriscos.

Esses detalhes explicam por que um recurso não está em conformidade no momento, mas não mostram quando foi feita a alteração no recurso que fez com que ele se passasse a não estar em conformidade. Para saber mais, confira [Histórico de alterações (versão preliminar)](#change-history) abaixo.

### <a name="compliance-reasons"></a>Motivos de conformidade

A matriz a seguir mapeia cada _motivo_ possível para a [condição](../concepts/definition-structure.md#conditions) responsável na definição de política:

|Motivo | Condição |
|-|-|
|O valor atual deve conter o valor de destino como uma chave. |containsKey ou **not** notContainsKey |
|O valor atual deve conter o valor de destino. |contains ou **not** notContains |
|O valor atual deve ser igual ao valor de destino. |equals ou **not** notEquals |
|O valor atual deve ser menor do que o valor de destino. |less ou **not** greaterOrEquals |
|O valor atual deve ser maior ou igual ao valor de destino. |greaterOrEquals ou **not** less |
|O valor atual deve ser maior que o valor de destino. |greater ou **not** lessOrEquals |
|O valor atual deve ser menor ou igual ao valor de destino. |lessOrEquals ou **not** greater |
|O valor atual deve existir. |exists |
|O valor atual deve estar no valor de destino. |in ou **not** notIn |
|O valor atual deve ser como o valor de destino. |like ou **not** notLike |
|O valor atual precisa corresponder ao valor de destino diferenciando maiúsculas de minúsculas. |match ou **not** notMatch |
|O valor atual precisa corresponder ao valor de destino sem diferenciar maiúsculas de minúsculas. |matchInsensitively ou **not** notMatchInsensitively |
|O valor atual não deve conter o valor de destino como uma chave. |notContainsKey ou **not** containsKey|
|O valor atual não deve conter o valor de destino. |notContains ou **not** contains |
|O valor atual não deve ser igual ao valor de destino. |notEquals ou **not** equals |
|O valor atual não deve existir. |**not** exists  |
|O valor atual não deve estar no valor de destino. |notIn ou **not** in |
|O valor atual não deve ser como o valor de destino. |notLike ou **not** like |
|O valor atual não pode corresponder ao valor de destino diferenciando maiúsculas de minúsculas. |notMatch ou **not** match |
|O valor atual não pode corresponder ao valor de destino sem diferenciar maiúsculas de minúsculas. |notMatchInsensitively ou **not** matchInsensitively |
|Não há recursos relacionados que correspondam aos detalhes de efeito na definição de política. |Um recurso do tipo definido em **then.details.type** e relacionado ao recurso definido na parte **if** da regra de política não existe. |

## <a name="component-details-for-resource-provider-modes"></a>Detalhes do componente para modos de provedor de recursos

Para atribuições com um [modo de provedor de recursos](../concepts/definition-structure.md#resource-manager-modes), selecione o recurso _sem conformidade_ para abrir uma exibição mais profunda. Na guia **conformidade do componente** , são informações adicionais específicas do modo do provedor de recursos na política atribuída, mostrando o **componente** _não compatível_ e a **ID do componente**.

:::image type="content" source="../media/getting-compliance-data/compliance-components.png" alt-text="Captura de tela da guia conformidade do componente e detalhes de conformidade para uma atribuição de modo do provedor de recursos." border="false":::

## <a name="compliance-details-for-guest-configuration"></a>Detalhes de conformidade para configuração de convidado

Para políticas de _auditIfNotExists_ na categoria de _configuração de convidado_ , pode haver várias configurações avaliadas dentro da máquina virtual e você precisará exibir os detalhes por configuração. Por exemplo, se você estiver auditando uma lista de políticas de senha e apenas uma delas tiver o status de _Não conformidade_, será preciso saber quais políticas de senha específicas estão fora de conformidade e por quê.

Você também pode não ter acesso para entrar na máquina virtual diretamente, mas precisa relatar por que a máquina virtual _não está em conformidade_.

### <a name="azure-portal"></a>Portal do Azure

Comece seguindo as mesmas etapas da seção acima para exibir os detalhes de conformidade da política.

Na exibição do painel detalhes de conformidade, selecione o link **último recurso avaliado**.

:::image type="content" source="../media/determine-non-compliance/guestconfig-auditifnotexists-compliance.png" alt-text="Captura de tela da exibição dos detalhes de conformidade da definição de auditIfNotExists." border="false":::

A página **Atribuição de convidado** exibe todos os detalhes de conformidade disponíveis. Cada linha na exibição representa uma avaliação que foi executada dentro da máquina. Na coluna **Motivo**, uma frase é mostrada descrevendo por que a atribuição de convidado é _não está em conformidade_. Por exemplo, se você estiver auditando políticas de senha, a coluna **Motivo** exibirá texto, incluindo o valor atual para cada configuração.

:::image type="content" source="../media/determine-non-compliance/guestconfig-compliance-details.png" alt-text="Captura de tela dos detalhes de conformidade da atribuição de convidado." border="false":::

## <a name="change-history-preview"></a><a name="change-history"></a>Alterar histórico (versão preliminar)

Como parte de uma nova **versão preliminar pública**, os últimos 14 dias de histórico de alterações estão disponíveis para todos os recursos do Azure que dão suporte à [exclusão de modo completo](../../../azure-resource-manager/templates/complete-mode-deletion.md). O histórico de alterações fornece detalhes sobre quando uma alteração foi detectada e uma _comparação visual_ para cada alteração. Uma detecção de alteração é disparada quando as propriedades de Azure Resource Manager são adicionadas, removidas ou alteradas.

1. Inicie o serviço do Azure Policy no portal do Azure selecionando **Todos os serviços** e, em seguida, pesquisando e selecionando **Política**.

1. Na página **Visão geral** ou **Conformidade**, escolha uma política em qualquer **estado de conformidade**.

1. Na guia **Conformidade do recurso** da página **Conformidade com a política**, escolha um recurso.

1. Escolha a guia **Histórico de Alterações (versão prévia)** na página **Conformidade do Recurso**. Se houver uma lista de alterações detectadas, ela será exibida.

   :::image type="content" source="../media/determine-non-compliance/change-history-tab.png" alt-text="Captura de tela da guia histórico de alterações e detectados tempos de alteração na página conformidade de recursos." border="false":::

1. Escolha uma das alterações detectadas. A _comparação visual_ para o recurso é apresentada na página **Histórico de alterações**.

   :::image type="content" source="../media/determine-non-compliance/change-history-visual-diff.png" alt-text="Captura de tela da diferença visual do histórico de alterações do estado anterior e posterior das propriedades na página Histórico de alterações." border="false":::

A _comparação visual_ ajuda a identificar alterações em um recurso. As alterações detectadas podem não estar relacionadas ao estado de conformidade atual do recurso.

Os dados do histórico de alterações são fornecidos pelo [Azure Resource Graph](../../resource-graph/overview.md). Para consultar essas informações fora do portal do Azure, confira [Obter alterações de recurso](../../resource-graph/how-to/get-resource-changes.md).

## <a name="next-steps"></a>Próximas etapas

- Revise os exemplos em [Exemplos do Azure Policy](../samples/index.md).
- Revise a [estrutura de definição do Azure Policy](../concepts/definition-structure.md).
- Revisar [Compreendendo os efeitos da política](../concepts/effects.md).
- Entenda como [criar políticas de forma programática](programmatically-create.md).
- Saiba como [obter dados de conformidade](get-compliance-data.md).
- Saiba como [corrigir recursos fora de conformidade](remediate-resources.md).
- Veja o que é um grupo de gerenciamento com [Organizar seus recursos com grupos de gerenciamento do Azure](../../management-groups/overview.md).
