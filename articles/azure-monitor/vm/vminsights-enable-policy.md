---
title: Habilitar o insights de VM usando Azure Policy
description: Descreve como habilitar o insights de VM para várias máquinas virtuais do Azure ou conjuntos de dimensionamento de máquinas virtuais usando Azure Policy.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: 51baf009543208fbbfe091238d0215a24761641d
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102031949"
---
# <a name="enable-vm-insights-by-using-azure-policy"></a>Habilitar o insights de VM usando Azure Policy
Este artigo explica como habilitar as informações de VM para máquinas virtuais do Azure ou máquina virtual híbrida conectada com o Arc do Azure (versão prévia) usando Azure Policy. Azure Policy permite que você atribua definições de política que instalam os agentes necessários para as informações da VM em todo o ambiente do Azure e habilitam automaticamente o monitoramento de VMs à medida que cada máquina virtual é criada. O Revisions da VM fornece um recurso que permite que você descubra e corrija VMs não compatíveis em seu ambiente. Use esse recurso em vez de trabalhar diretamente com Azure Policy.

Se você não estiver familiarizado com Azure Policy, obtenha uma breve introdução em [implantar Azure monitor em escala usando Azure Policy](../deploy-scale.md).

> [!NOTE]
> Para usar Azure Policy com conjuntos de dimensionamento de máquinas virtuais do Azure ou para trabalhar com Azure Policy diretamente para habilitar máquinas virtuais do Azure, consulte [implantar Azure monitor em escala usando Azure Policy](../deploy-scale.md#vm-insights).

## <a name="prerequisites"></a>Pré-requisitos
- [Criar e configurar um espaço de trabalho log Analytics](./vminsights-configure-workspace.md).
- Consulte [sistemas operacionais com suporte](./vminsights-enable-overview.md#supported-operating-systems) para garantir que o sistema operacional da máquina virtual ou do conjunto de dimensionamento de máquinas virtuais que você está habilitando tem suporte. 


## <a name="vm-insights-initiative"></a>Iniciativa de informações da VM
As informações de VM fornecem definições de política interna para instalar o agente de Log Analytics e o agente de dependência em máquinas virtuais do Azure. A iniciativa **habilitar as informações de VM** inclui cada uma dessas definições de política. Atribua essa iniciativa a um grupo de gerenciamento, assinatura ou grupo de recursos para instalar automaticamente os agentes em qualquer máquina virtual do Windows ou Linux do Azure nesse escopo.

## <a name="open-policy-coverage-feature"></a>Abrir recurso de cobertura de política
Para acessar a **cobertura de política de informações de VM**, acesse as **máquinas virtuais** no menu **Azure monitor** na portal do Azure. Selecione **outras opções de integração** e, em seguida, **habilitar** em **habilitar usando a política**.

[![Guia de introdução de Azure Monitor de VMs](./media/vminsights-enable-policy/get-started-page.png)](./media/vminsights-enable-policy/get-started-page.png#lightbox)

## <a name="create-new-assignment"></a>Criar nova atribuição
Se você ainda não tiver uma atribuição, crie uma nova clicando em **atribuir política**.

[![Criar atribuição](media/vminsights-enable-policy/create-assignment.png)](media/vminsights-enable-policy/create-assignment.png#lightbox)

Essa é a mesma página para atribuir uma iniciativa no Azure Policy, exceto que ela é codificada com o escopo que você selecionou e a definição habilitar a iniciativa de **insights de VM** . Opcionalmente, você pode alterar o **nome da atribuição** e adicionar uma **Descrição**. Selecione **exclusões** se desejar fornecer uma exclusão para o escopo. Por exemplo, seu escopo pode ser um grupo de gerenciamento, e você pode especificar uma assinatura nesse grupo de gerenciamento a ser excluída da atribuição.

[![Atribuir iniciativa](media/vminsights-enable-policy/assign-initiative.png)](media/vminsights-enable-policy/assign-initiative.png#lightbox)

Na página **parâmetros** , selecione um **espaço de trabalho log Analytics** a ser usado por todas as máquinas virtuais na atribuição. Se você quiser especificar espaços de trabalho diferentes para máquinas virtuais diferentes, deverá criar várias atribuições, cada uma com seu próprio escopo. 

   > [!NOTE]
   > Se o workspace estiver fora do escopo da atribuição, conceda as permissões *Colaborador do Log Analytics* à ID de Entidade de Segurança da atribuição de política. Se você não fizer isso, poderá ver uma falha de implantação como `The client '343de0fe-e724-46b8-b1fb-97090f7054ed' with object id '343de0fe-e724-46b8-b1fb-97090f7054ed' does not have authorization to perform action 'microsoft.operationalinsights/workspaces/read' over scope ...`

[![Espaço](media/vminsights-enable-policy/assignment-workspace.png)](media/vminsights-enable-policy/assignment-workspace.png#lightbox)

Clique em **revisar + criar** para revisar os detalhes da atribuição antes de clicar em **criar** para criá-la. Não crie uma tarefa de correção neste ponto, pois você provavelmente precisará de várias tarefas de correção para habilitar as máquinas virtuais existentes. Consulte [corrigir os resultados de conformidade](#remediate-compliance-results) abaixo.

## <a name="review-compliance"></a>Examinar a conformidade
Depois que uma atribuição é criada, você pode examinar e gerenciar a cobertura para a iniciativa **habilitar as informações de VM** em seus grupos de gerenciamento e assinaturas. Isso mostrará quantas máquinas virtuais existem em cada um dos grupos de gerenciamento ou assinaturas e seu status de conformidade.

[![Página de política de gerenciamento de informações de VM](media/vminsights-enable-policy/manage-policy-page-01.png)](media/vminsights-enable-policy/manage-policy-page-01.png#lightbox)


A tabela a seguir fornece uma descrição das informações neste modo de exibição.

| Função | Descrição | 
|----------|-------------| 
| **Escopo** | O grupo de gerenciamento e as assinaturas que você tem ou acesso herdado com a capacidade de detalhar a hierarquia do grupo de gerenciamento.|
| **Função** | Sua função no escopo, que pode ser leitor, proprietário ou colaborador. Isso estará em branco se você tiver acesso à assinatura, mas não ao grupo de gerenciamento ao qual ela pertence. Essa função determina quais dados você pode ver e ações que podem ser executadas em termos de atribuição de políticas ou iniciativas (proprietário), edição deles ou exibição da conformidade. |
| **Total de VMs** | Número total de VMs nesse escopo, independentemente de seu status. Para um grupo de gerenciamento, essa é uma soma total das VMs aninhadas nas assinaturas ou grupos de gerenciamento filho. |
| **Cobertura de atribuição** | Porcentagem de VMs cobertas pela iniciativa. |
| **Status da atribuição** | **Êxito** -todas as VMs no escopo têm a log Analytics e os agentes de dependência implantados nelas.<br>**Aviso** -a assinatura não está em um grupo de gerenciamento.<br>**Não iniciado** -uma nova atribuição foi adicionada.<br>**Bloqueio** -você não tem privilégios suficientes para o grupo de gerenciamento.<br>**Em branco** -não existem VMs ou uma política não está atribuída. |
| **VMs em conformidade** | Número de VMs que são compatíveis, que é o número de VMs que têm o agente Log Analytics e o agente de dependência instalados. Isso estará em branco se não houver nenhuma atribuição, nenhuma VM no escopo nem permissões adequadas. |
| **Conformidade** | O número de conformidade geral é a soma de recursos distintos que são compatíveis divididos pela soma de todos os recursos distintos. |
| **Estado de conformidade** | Em **conformidade** – todas as VMs no escopo máquinas virtuais têm a log Analytics e os agentes de dependência implantados neles ou quaisquer novas VMs no escopo sujeitas à atribuição ainda não foram avaliadas.<br>**Não compatível** – há VMs que foram avaliadas, mas não estão habilitadas e podem exigir correção.<br>**Não iniciado** -uma nova atribuição foi adicionada.<br>**Bloqueio** -você não tem privilégios suficientes para o grupo de gerenciamento.<br>**Em branco** -nenhuma política é atribuída.  |


Quando você atribui a iniciativa, o escopo selecionado na atribuição pode ser o escopo listado ou um subconjunto dele. Por exemplo, você pode ter criado uma atribuição para uma assinatura (escopo de política) e não um grupo de gerenciamento (escopo de cobertura). Nesse caso, o valor da **cobertura de atribuição** indica as VMs no escopo da iniciativa dividida pelas VMs no escopo de cobertura. Em outro caso, você pode ter excluído algumas VMs, grupos de recursos ou uma assinatura do escopo da política. Se o valor estiver em branco, indica que a política ou iniciativa não existe ou você não tem permissão. As informações são fornecidas sob o **status da atribuição**.


## <a name="remediate-compliance-results"></a>Corrigir resultados de conformidade
A iniciativa será aplicada às máquinas virtuais à medida que elas forem criadas ou modificadas, mas não serão aplicadas a VMs existentes. Se sua atribuição não mostrar a conformidade de 100%, crie tarefas de correção para avaliar e habilitar as VMs existentes, selecione **Exibir conformidade** selecionando as reticências (...).

[![Exibir conformidade](media/vminsights-enable-policy/view-compliance.png)](media/vminsights-enable-policy/view-compliance.png#lightbox)

A página **conformidade** lista as atribuições que correspondem ao filtro especificado e se elas estão em conformidade. Clique em uma atribuição para exibir seus detalhes.

[![Conformidade com a política para máquinas virtuais do Azure](./media/vminsights-enable-policy/policy-view-compliance.png)](./media/vminsights-enable-policy/policy-view-compliance.png#lightbox)

A página de **conformidade da iniciativa** lista as definições de política na iniciativa e se cada uma está em conformidade.

[![Detalhes de conformidade](media/vminsights-enable-policy/compliance-details.png)](media/vminsights-enable-policy/compliance-details.png#lightbox)

Clique em uma definição de política para exibir seus detalhes. Os cenários que as definições de política serão mostradas como fora de conformidade incluem o seguinte:

* O agente de Log Analytics ou o agente de dependência não está implantado. Crie uma tarefa de correção para mitigar.
* A imagem de VM (SO) não é identificada na definição de política. Os critérios da política de implantação incluem apenas as VMs implantadas com base em imagens de VM conhecidas do Azure. Verifique a documentação para ver se o sistema operacional da VM é compatível.
* As VMs não estão registrando no espaço de trabalho Log Analytics especificado. Algumas VMs no escopo de iniciativa são conectadas a um espaço de trabalho Log Analytics diferente daquele especificado na atribuição de política.

[![Detalhes de conformidade da política](media/vminsights-enable-policy/policy-compliance-details.png)](media/vminsights-enable-policy/policy-compliance-details.png#lightbox)

Para criar uma tarefa de correção para atenuar problemas de conformidade, clique em **criar tarefa de correção**. 

[![Nova tarefa de correção](media/vminsights-enable-policy/new-remediation-task.png)](media/vminsights-enable-policy/new-remediation-task.png#lightbox)

Clique em **corrigir** para criar a tarefa de correção e, em seguida, **corrigir** para iniciá-la. Provavelmente, será necessário criar várias tarefas de correção, uma para cada definição de política. Você não pode criar uma tarefa de correção para uma iniciativa.

[![Captura de tela mostra o painel de correção de política para o monitor | Máquinas virtuais.](media/vminsights-enable-policy/remediation.png)](media/vminsights-enable-policy/remediation.png#lightbox)


Depois que as tarefas de correção forem concluídas, suas VMs deverão estar em conformidade com os agentes instalados e habilitados para o VM insights. 

## <a name="next-steps"></a>Próximas etapas

Agora que o monitoramento está habilitado para suas máquinas virtuais, essas informações estão disponíveis para análise com o VM insights. 

- Para exibir dependências de aplicativo descobertas, consulte [Exibir o mapa de informações de VM](vminsights-maps.md). 
- Para identificar afunilamentos e a utilização geral com o desempenho da VM, consulte [Exibir o desempenho da VM do Azure](vminsights-performance.md).
