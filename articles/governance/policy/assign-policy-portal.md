---
title: Nova atribuição de política com o portal
description: Neste início rápido, use o portal do Azure para criar uma atribuição do Azure Policy para identificar recursos sem conformidade.
ms.date: 01/29/2021
ms.topic: quickstart
ms.openlocfilehash: e5cbf31e897b5be404327efa254eb90ead990f5f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99220880"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources"></a>Criar uma atribuição de política para identificar recursos fora de conformidade.

A primeira etapa para compreender a conformidade no Azure é identificar o status de seus recursos.
Este guia de início rápido orienta você no processo de criação de uma atribuição de política para identificar máquinas virtuais que não estão usando discos gerenciados.

No final deste processo, você identificará com êxito quais máquinas virtuais não estão usando discos gerenciados. Eles _não estão em conformidade_ com a atribuição da política.

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-a-policy-assignment"></a>Criar uma atribuição de política

Neste guia de início rápido, crie uma atribuição de política e atribua a definição de política _Auditar VMs que não usam discos gerenciados_.

1. Inicie o serviço do Azure Policy no portal do Azure selecionando **Todos os serviços** e, em seguida, pesquisando e selecionando **Política**.

   :::image type="content" source="./media/assign-policy-portal/search-policy.png" alt-text="Captura de tela da pesquisa de Política em Todos os Serviços." border="false":::

1. Selecione **Atribuições** no lado esquerdo da página de Política do Azure. Uma atribuição é uma política que foi atribuída para entrar em vigor em um escopo específico.

   :::image type="content" source="./media/assign-policy-portal/select-assignments.png" alt-text="Captura de tela da seleção da página Atribuições da página Visão geral da Política." border="false":::

1. Selecione **Atribuir Política** na parte superior da página **Política - Atribuições**.

   :::image type="content" source="./media/assign-policy-portal/select-assign-policy.png" alt-text="Captura de tela da seleção de &quot;Atribuir política&quot; na página Atribuições." border="false":::

1. Na página **Atribuir Política**, defina o **Escopo** selecionando o botão de reticências e escolhendo uma assinatura ou um grupo de gerenciamento. Opcionalmente, selecione um grupo de recursos. Um escopo determina em quais recursos ou agrupamento de recursos a atribuição de política é imposta. Em seguida, use o botão **Selecionar** na parte inferior da página **Escopo**.

   Este exemplo usa a assinatura da **Contoso**. Sua assinatura será diferente.

1. Recursos que podem ser excluídos com base no **Escopo**. **Exclusões** começam um nível abaixo do **Escopo**. **Exclusões** são opcionais, então deixe-as em branco por enquanto.

1. Selecione a reticências **Definição de política** para abrir a lista de definições. O Azure Policy vem com definições de políticas internas que você pode usar. Há muitas disponíveis, por exemplo:

   - Impor marca e seu valor
   - Aplicar marca e seu valor
   - Herdar uma marca do grupo de recursos se ela estiver ausente

   Para ver uma lista parcial das políticas internas disponíveis, confira [Exemplos do Azure Policy](./samples/index.md).

1. Pesquise a lista de definições de política para encontrar a definição _Auditar VMs que não usam discos gerenciados_. Escolha essa política e use o botão **Selecionar**.

   :::image type="content" source="./media/assign-policy-portal/select-available-definition.png" alt-text="Captura de tela de filtragem das definições disponíveis." border="false":::

1. O **Nome da atribuição** é automaticamente preenchido com o nome da política selecionada, mas você pode alterá-lo. Neste exemplo, deixe _Auditar VMs que não usam discos gerenciados_. Você também pode adicionar uma **Descrição** opcional. A descrição fornece detalhes sobre essa atribuição de política.
   **Atribuído por** preencherá automaticamente com base em quem está conectado. Esse campo é opcional, portanto, valores personalizados podem ser inseridos.

1. Mantenha a imposição de política como _Habilitado_. Para obter mais informações, confira [Atribuição de política – modo de imposição](./concepts/assignment-structure.md#enforcement-mode).

1. Selecione **Avançar** na parte inferior da página ou a guia **Parâmetros** na parte superior para ir para o próximo segmento do assistente de atribuição.

1. Se a definição de política selecionada na guia **Informações Básicas** incluíram parâmetros, eles serão configurados nessa guia. Como a opção _Auditar as VMs que não usam discos gerenciados_ não tem parâmetros, selecione **Avançar** na parte inferior da página ou a guia **Correção** na parte superior para ir para o próximo segmento do assistente de atribuição.

1. Deixe a opção **Criar uma identidade gerenciada** desmarcada. Esta caixa _precisa_ ser marcada quando a política ou iniciativa inclui uma política com o efeito [deployIfNotExists](./concepts/effects.md#deployifnotexists) ou [modify](./concepts/effects.md#modify). Já que esse não é o caso da política usada para este início rápido, deixe essa opção em branco. Para obter mais informações, confira [identidades gerenciadas](../../active-directory/managed-identities-azure-resources/overview.md) e [como funciona a segurança de correção](./how-to/remediate-resources.md#how-remediation-security-works).

1. Selecione **Avançar** na parte inferior da página ou a guia **Mensagens de não conformidade** na parte superior para ir para o próximo segmento do assistente de atribuição.

1. Defina a **Mensagem de não conformidade** como _As máquinas virtuais devem usar um disco gerenciado_. Essa mensagem personalizada é exibida quando um recurso é negado ou para recursos sem conformidade durante a avaliação regular.

1. Selecione **Avançar** na parte inferior da página ou a guia **Examinar + Criar** na parte superior para ir para o próximo segmento do assistente de atribuição.

1. Examine as opções selecionadas e escolha **Criar** na parte inferior da página.

Agora você está pronto para identificar recursos fora de conformidade para entender o estado de conformidade do ambiente.

## <a name="identify-non-compliant-resources"></a>Identificar recursos fora de conformidade

Selecione **Conformidade** no lado esquerdo da página. Em seguida, localize as _VMs de auditoria que não usam a atribuição de política de discos gerenciados_ que você criou.

:::image type="content" source="./media/assign-policy-portal/policy-compliance.png" alt-text="Captura de tela dos detalhes de conformidade na página Conformidade com a Política." border="false":::

Se houver recursos sem conformidade com essa nova atribuição, eles aparecerão em **Recursos sem conformidade**.

Quando uma condição é avaliada em relação a seus recursos existentes e resulta ser verdadeira, então esses recursos são marcados como em não conformidade com a política. A tabela a seguir mostra como os diferentes efeitos da política funcionam com a avaliação da condição para o estado de conformidade resultante: Embora você não veja a lógica de avaliação no portal do Azure, os resultados do estado de conformidade são mostrados. O resultado do estado de conformidade pode ser ou compatível ou incompatível.

| Estado do Recurso | Efeito | Avaliação da política | Estado de conformidade |
| --- | --- | --- | --- |
| Novo ou atualizado | Audit, Modify, AuditIfNotExist | True | Sem conformidade |
| Novo ou atualizado | Audit, Modify, AuditIfNotExist | Falso | Em conformidade |
| Exists | Deny, Audit, Append, Modify, DeployIfNotExist, AuditIfNotExist | True | Sem conformidade |
| Exists | Deny, Audit, Append, Modify, DeployIfNotExist, AuditIfNotExist | Falso | Em conformidade |

> [!NOTE]
> Os efeitos DeployIfNotExist e AuditIfNotExist exigem que a instrução IF seja TRUE e a condição de existência seja FALSE para não estar em conformidade. Quando TRUE, a condição IF dispara a avaliação da condição de existência para os recursos relacionados.

## <a name="clean-up-resources"></a>Limpar os recursos

Para remover a atribuição criada, siga estas etapas:

1. Selecione **Conformidade** (ou **Atribuições**) no lado esquerdo da página e localize as _VMs de auditoria que não usam a atribuição  de política de discos gerenciados_ que você criou.

1. Clique com o botão direito do mouse na atribuição de política _Auditar VMs que não usam discos gerenciados_ e selecione **Excluir atribuição**.

   :::image type="content" source="./media/assign-policy-portal/delete-assignment.png" alt-text="Captura de tela do uso do menu de contexto para excluir uma atribuição da página Conformidade." border="false":::

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você atribuiu uma definição de política para um escopo e avaliou seu relatório de conformidade.
A definição de política valida que todos os recursos no escopo estão em conformidade e identifica quais não estão.

Para saber mais sobre a atribuição de políticas para validar que novos recursos estejam em conformidade, continue com o tutorial para:

> [!div class="nextstepaction"]
> [Criando e gerenciando políticas](./tutorials/create-and-manage.md)