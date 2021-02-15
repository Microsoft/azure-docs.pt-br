---
title: Atualizar uma atribuição existente do portal
description: Saiba mais sobre o mecanismo de atualização de uma atribuição de Blueprint existente do portal em plantas do Azure.
ms.date: 01/27/2021
ms.topic: how-to
ms.openlocfilehash: c383ebedaf83b3a52062c91f98b816c3baf6618e
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98919386"
---
# <a name="how-to-update-an-existing-blueprint-assignment"></a>Como atualizar uma atribuição de blueprint existente

Quando um blueprint é atribuído, a atribuição pode ser atualizada. Há vários motivos para atualizar uma atribuição existente, como:

- Adicionar ou remover um [bloqueio de recurso](../concepts/resource-locking.md)
- Alterar o valor de [parâmetros dinâmicos](../concepts/parameters.md#dynamic-parameters)
- Atualizar a atribuição para uma versão **Publicada** mais recente do blueprint

## <a name="updating-assignments"></a>Atualizando atribuições

1. Selecione **Todos os serviços** no painel esquerdo. Pesquise e selecione **Blueprints**.

1. Selecione **Blueprints atribuídos** na página à esquerda.

1. Na lista de plantas, selecione a atribuição Blueprint. Em seguida, use o botão **Atualizar atribuição** ou clique com o botão direito do mouse na atribuição Blueprint e selecione **Atualizar atribuição**.

   :::image type="content" source="../media/update-existing-assignments/update-assignment.png" alt-text="Captura de tela da página de atribuição do Blueprint com o botão ' atualizar atribuição ' realçado." border="false":::

1. A página **atribuir Blueprint** é carregada previamente preenchida com todos os valores da atribuição original. Você pode alterar a **versão de definição do blueprint**, o estado **Bloquear Atribuição** e qualquer um dos parâmetros dinâmicos que existem na definição do blueprint. Selecione **atribuir** quando terminar de fazer alterações.

1. Na página de detalhes de atribuição atualizada, veja o novo status. Neste exemplo, adicionamos **Bloqueio** à atribuição.

   :::image type="content" source="../media/update-existing-assignments/updated-assignment.png" alt-text="Captura de tela de uma atribuição de plano gráfico atualizada mostrando o modo de bloqueio alterado." border="false":::

1. Explore os detalhes sobre outras **operações de atribuição** usando a lista suspensa. A tabela de atualizações de **recursos gerenciados** por operação de atribuição selecionada.

   :::image type="content" source="../media/update-existing-assignments/assignment-operations.png" alt-text="Captura de tela de uma atribuição de plano gráfico atualizada mostrando as operações de atribuição e seu status." border="false":::

## <a name="rules-for-updating-assignments"></a>Regras para atualizar atribuições

A implantação das atribuições atualizadas segue algumas regras importantes. Essas regras determinam o que acontece com os recursos já implantados. A alteração solicitada e o tipo de artefato de recurso que está sendo implantado ou atualizado determinam quais ações são executadas.

- Atribuições de função
  - Se a função ou o destinatário da função (usuário, grupo ou aplicativo) for alterado, uma nova atribuição de função será criada. A atribuição de função já implantada permanece em vigor.
- Atribuições de Políticas
  - Se os parâmetros da atribuição de política forem alterados, a atribuição existente será atualizada.
  - Se a definição da atribuição de política for alterada, uma nova atribuição de política será criada.
    Atribuições de política implantadas anteriormente são deixadas no lugar.
  - Se o artefato de atribuição de política for removido do plano gráfico, implantado atribuições são deixadas no lugar de política.
- Modelos do ARM (modelos do Azure Resource Manager)
  - O modelo é processado por meio do Resource Manager como **PUT**. Como cada tipo de recurso manipula isso de uma forma diferente, examine a documentação de cada recurso incluído para determinar o impacto dessa ação quando executada por blueprints.

## <a name="possible-errors-on-updating-assignments"></a>Possíveis erros na atualização de atribuições

Ao atualizar atribuições, é possível fazer alterações que falham ao serem executadas. Um exemplo disso é alterar o local de um grupo de recursos depois que ele já foi implantado. Qualquer alteração com suporte do [Resource Manager](../../../azure-resource-manager/management/overview.md) pode ser feita, mas qualquer alteração que resulte em um erro por meio do Resource Manager também resultará na falha da atribuição.

Não há nenhum limite para o número de vezes que uma atribuição pode ser atualizada. Se ocorrer um erro, determine o erro e faça outra atualização da atribuição.  Cenários de erro de exemplo:

- Parâmetro inválido
- Um objeto já existente
- Uma alteração sem suporte pelo Resource Manager

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [ciclo de vida do blueprint](../concepts/lifecycle.md).
- Saiba como usar [parâmetros estáticos e dinâmicos](../concepts/parameters.md).
- Saiba como personalizar a [ordem de sequenciamento de blueprint](../concepts/sequencing-order.md).
- Saiba como usar o [bloqueio de recurso de blueprint](../concepts/resource-locking.md).
- Resolver problemas durante a atribuição de blueprint com [solução de problemas gerais](../troubleshoot/general.md).