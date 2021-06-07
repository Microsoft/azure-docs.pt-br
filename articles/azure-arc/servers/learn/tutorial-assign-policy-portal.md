---
title: Tutorial – Nova atribuição de política com o portal do Azure
description: Neste tutorial, use o portal do Azure para criar uma atribuição do Azure Policy para identificar recursos sem conformidade.
ms.topic: tutorial
ms.date: 10/07/2020
ms.openlocfilehash: 9a07e490525ce532f8f843b30b3b83715e65ce3c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91826603"
---
# <a name="tutorial-create-a-policy-assignment-to-identify-non-compliant-resources"></a>Tutorial: Criar uma atribuição de política para identificar recursos sem conformidade

A primeira etapa para compreender a conformidade no Azure é identificar o status de seus recursos. O Azure Policy dá suporte à auditoria do estado do servidor habilitado para Arc com as políticas de Configuração de Convidado. As políticas de Configuração de Convidado não aplicam as configurações, elas apenas auditam as configurações dentro do computador. Este tutorial orienta você pelo processo de criação e atribuição de uma política, identificando quais servidores habilitados para Arc não têm o agente do Log Analytics instalado.

No final desse processo, você identificará com êxito os computadores que não têm o agente do Log Analytics para Windows ou Linux instalado. Eles _não estão em conformidade_ com a atribuição da política.

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-a-policy-assignment"></a>Criar uma atribuição de política

Neste tutorial, você cria uma atribuição de política e atribui _\[Versão Prévia]: o agente do Log Analytics deve ser instalado nos seus computadores do Azure Arc do Linux_.

1. Inicie o serviço de Azure Policy no portal do Azure clicando em **Todos os serviços**, em seguida pesquisando e selecionando **Política**.

   :::image type="content" source="./media/tutorial-assign-policy-portal/search-policy.png" alt-text="Pesquisar Política em Todos os Serviços" border="false":::

1. Selecione **Atribuições** no lado esquerdo da página de Política do Azure. Uma atribuição é uma política que foi atribuída para entrar em vigor em um escopo específico.

   :::image type="content" source="./media/tutorial-assign-policy-portal/select-assignment.png" alt-text="Selecionar a página Atribuições na página Visão Geral da Política" border="false":::

1. Selecione **Atribuir Política** na parte superior da página **Política - Atribuições**.

   :::image type="content" source="./media/tutorial-assign-policy-portal/select-assign-policy.png" alt-text="Atribuir uma definição de política na página Atribuições" border="false":::

1. Na página **Atribuir Política**, selecione o **Escopo** clicando no botão de reticências e selecionando uma assinatura ou então um grupo de gerenciamento. Opcionalmente, selecione um grupo de recursos. Um escopo determina em quais recursos ou agrupamento de recursos a atribuição de política é imposta. Em seguida, clique em **Selecionar** na parte inferior da página **Escopo**.

   Este exemplo usa a assinatura do **Parnell Aerospace**. Sua assinatura será diferente.

1. Recursos que podem ser excluídos com base no **Escopo**. **Exclusões** começam um nível abaixo do **Escopo**. **Exclusões** são opcionais, então deixe-as em branco por enquanto.

1. Selecione a reticências **Definição de política** para abrir a lista de definições. O Azure Policy vem com definições de políticas internas que você pode usar. Há muitas disponíveis, por exemplo:

   - Impor marca e seu valor
   - Aplicar marca e seu valor
   - Herdar uma marca do grupo de recursos se ela estiver ausente

   Para ver uma lista parcial das políticas internas disponíveis, confira [Exemplos do Azure Policy](../../../governance/policy/samples/index.md).

1. Pesquise na lista definições de política para localizar a definição _\[Versão Prévia]: o agente do Log Analytics deve ser instalado nos computadores Windows com Azure Arc_ se você habilitou o agente de servidores habilitados para Arc em um computador baseado no Windows. Para um computador baseado no Linux, localize a definição de política correspondente _\[Versão prévia]: o agente do Log Analytics deve ser instalado nos seus computadores do Azure Arc do Linux_. Clique na política e clique em **Selecionar**.

   :::image type="content" source="./media/tutorial-assign-policy-portal/select-available-definition.png" alt-text="Encontre a definição de política correta" border="false":::

1. O **Nome da atribuição** é automaticamente preenchido com o nome da política selecionada, mas você pode alterá-lo. Neste exemplo, saia de _\[Versão prévia]: o agente do Log Analytics deve ser instalado nos seus computadores Windows com Azure Arc_ ou _\[Versão prévia]: o agente do Log Analytics deve ser instalado nos seus computadores do Azure Arc do Linux_, dependendo de qual deles você selecionou. Você também pode adicionar uma **Descrição** opcional. A descrição fornece detalhes sobre essa atribuição de política.
   **Atribuído por** preencherá automaticamente com base em quem está conectado. Esse campo é opcional, portanto, valores personalizados podem ser inseridos.

1. Deixe a opção **Criar uma identidade gerenciada** desmarcada. Esta caixa _precisa_ ser marcada quando a política ou iniciativa inclui uma política com o efeito [deployIfNotExists](../../../governance/policy/concepts/effects.md#deployifnotexists). Já que esse não é o caso da política usada para este início rápido, deixe essa opção em branco. Para obter mais informações, confira [identidades gerenciadas](../../../active-directory/managed-identities-azure-resources/overview.md) e [como funciona a segurança de correção](../../../governance/policy/how-to/remediate-resources.md#how-remediation-security-works).

1. Clique em **Atribuir**.

Agora você está pronto para identificar recursos fora de conformidade para entender o estado de conformidade do ambiente.

## <a name="identify-non-compliant-resources"></a>Identificar recursos fora de conformidade

Selecione **Conformidade** no lado esquerdo da página. Em seguida, localize a atribuição de política **\[Versão prévia]: o agente do Log Analytics deve ser instalado nos seus computadores Windows com Azure Arc** ou **\[Versão prévia]: o agente do Log Analytics deve ser instalado nos seus computadores do Azure Arc do Linux** que você criou.

:::image type="content" source="./media/tutorial-assign-policy-portal/policy-compliance.png" alt-text="Detalhes de conformidade na página Conformidade com a Política" border="false":::

Se houver recursos sem conformidade com essa nova atribuição, eles aparecerão em **Recursos sem conformidade**.

Quando uma condição é avaliada em relação a seus recursos existentes e resulta ser verdadeira, então esses recursos são marcados como em não conformidade com a política. A tabela a seguir mostra como os diferentes efeitos da política funcionam com a avaliação da condição para o estado de conformidade resultante: Embora você não veja a lógica de avaliação no portal do Azure, os resultados do estado de conformidade são mostrados. O resultado do estado de conformidade pode ser ou compatível ou incompatível.

| **Estado do recurso** | **Efeito** | **Avaliação da política** | **Estado de conformidade** |
| --- | --- | --- | --- |
| Exists | Negar, Auditoria, Acrescentar\*, DeployIfNotExist\*, AuditIfNotExist\* | True | Sem conformidade |
| Exists | Negar, Auditoria, Acrescentar\*, DeployIfNotExist\*, AuditIfNotExist\* | Falso | Em conformidade |
| Novo | Auditoria, AuditIfNotExist\* | True | Sem conformidade |
| Novo | Auditoria, AuditIfNotExist\* | Falso | Em conformidade |

\* Os efeitos de Acrescentar, DeployIfNotExist e AuditIfNotExist exigem que a instrução IF seja TRUE.
Os efeitos também exigem que a condição de existência seja FALSE para não estar em conformidade. Quando TRUE, a condição IF dispara a avaliação da condição de existência para os recursos relacionados.

## <a name="clean-up-resources"></a>Limpar os recursos

Para remover a atribuição criada, siga estas etapas:

1. Selecione **Conformidade** (ou **Atribuições**) no lado esquerdo da página Azure Policy e localize a atribuição de política **\[Versão prévia]: o agente do Log Analytics deve ser instalado nos seus computadores Windows com Azure Arc** ou **\[Versão prévia]: o agente do Log Analytics deve ser instalado nos seus computadores do Azure Arc do Linux** que você criou.

1. Clique com o botão direito do mouse na atribuição de política e selecione **Excluir atribuição**.

   :::image type="content" source="./media/tutorial-assign-policy-portal/delete-assignment.png" alt-text="Excluir uma atribuição da página Conformidade" border="false":::

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você atribuiu uma definição de política para um escopo e avaliou o relatório de conformidade. A definição de política valida que todos os recursos no escopo estão em conformidade e identifica quais não estão. Agora você está pronto para monitorar o seu computador de servidores habilitados para Azure Arc com o Azure Monitor para VMs.

Para saber como monitorar e exibir o desempenho, o processo de execução e as dependências dele no seu computador, prossiga para o tutorial:

> [!div class="nextstepaction"]
> [Habilitar o Azure Monitor para VMs](tutorial-enable-vm-insights.md)