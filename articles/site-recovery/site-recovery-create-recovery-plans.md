---
title: Criar/Personalizar planos de recuperação no Azure Site Recovery
description: Saiba como criar e personalizar planos de recuperação para recuperação de desastres usando o Azure Site Recovery.
ms.topic: how-to
ms.date: 01/23/2020
ms.openlocfilehash: 0dcde98e8dcaef12896c18c25429f0ba7b1b27d4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96009714"
---
# <a name="create-and-customize-recovery-plans"></a>Criar e personalizar planos de recuperação

Este artigo descreve como criar e personalizar um plano de recuperação para failover no [Azure site Recovery](site-recovery-overview.md). Antes de iniciar, [saiba mais](recovery-plan-overview.md) sobre planos de recuperação.

## <a name="create-a-recovery-plan"></a>Criar um plano de recuperação

1. No cofre dos serviços de recuperação, selecione **planos de recuperação (site Recovery)**  >  **+ plano de recuperação**.
2. Em **Criar plano de recuperação**, especifique um nome para o plano.
3. Escolha uma fonte e destino com base nos computadores do plano e selecione **Gerenciador de Recursos** para o modelo de implantação. O local de origem deve ter computadores habilitados para failover e recuperação. 

    **Failover** | **Origem** | **Target (destino)** 
   --- | --- | ---
   Azure para o Azure | Selecione a região do Azure | Selecione a região do Azure
   VMware no Azure | Selecionar o servidor de configuração | Selecionar o Azure
   Computadores físicos para o Azure | Selecionar o servidor de configuração | Selecionar o Azure   
   Hyper-V para Azure | Selecione o nome do site do Hyper-V | Selecionar o Azure
   Hyper-V (gerenciado pelo VMM) para o Azure  | Selecione o servidor do VMM | Selecionar o Azure
  
    Observe o seguinte:
    - Você pode usar um plano de recuperação para failover para o Azure e failback do Azure.
    - O local de origem deve ter computadores habilitados para failover e recuperação.
    - Um plano de recuperação pode conter computadores com a mesma fonte e destino.
    - Você pode incluir VMs do VMware e VMs do Hyper-V gerenciadas pelo VMM, no mesmo plano.
    - As VMs VMware e os servidores físicos podem estar no mesmo plano.

4. Em **Selecionar máquinas virtuais de itens**, selecione as máquinas (ou o grupo de replicação) que deseja adicionar ao plano. Em seguida, clique em **OK**.
    - Os computadores são adicionados a um grupo padrão (Grupo 1) no plano. Após o failover, todos os computadores neste grupo são iniciados ao mesmo tempo.
    - Você só pode selecionar computadores que estejam em locais de origem e destino especificados. 
5. Clique em **OK** para criar o plano.

## <a name="add-a-group-to-a-plan"></a>Adicionar um grupo a um plano

Você cria grupos adicionais e adiciona computadores a diferentes grupos para que possa especificar um comportamento diferente em uma base grupo por grupo. Por exemplo, você pode especificar quando os computadores em um grupo devem ser iniciados após o failover, ou especificar ações personalizadas por grupo.

1. Em **Planos de Recuperação**, clique com o botão direito no plano > **Personalizar**. Por padrão, depois de criar um plano, todos os computadores adicionados a ele são localizados no Grupo 1 padrão.
2. Clique em **+Grupo**. Por padrão, um novo grupo é numerado na ordem em que ele é adicionado. É possível ter até sete grupos.
3. Selecione o computador que você deseja mover para o novo grupo, clique em **Alterar grupo** e, em seguida, selecione o novo grupo. Como alternativa, clique com o botão direito no nome do grupo > **Item Protegido** e adicione computadores ao grupo. Um computador ou grupo de replicação pode pertencer somente a um grupo em um plano de recuperação.


## <a name="add-a-script-or-manual-action"></a>Adicionar uma ação de script ou manual

Você pode personalizar um plano de recuperação com a adição de um script ou uma ação manual. Observe que:

- Se estiver replicando no Azure, você poderá integrar runbooks de automação do Azure ao plano de recuperação. [Saiba mais](site-recovery-runbook-automation.md).
- Se estiver replicando máquinas virtuais Hyper-V gerenciadas pelo System Center VMM, você pode criar um script no servidor VMM local e incluí-lo no plano de recuperação.
- Quando você adiciona um script, ele adiciona um novo conjunto de ações para o grupo. Por exemplo, um conjunto de pré-etapas para o Grupo 1 é criado com o nome *Grupo 1: pré-etapas*. Todas as pré-etapas são listadas dentro desse conjunto. Você poderá adicionar um script no site primário, somente se tiver um servidor VMM implantado.
- Se você adicionar uma ação manual, quando o plano de recuperação for executado, ele será interrompido no ponto em que você inseriu a ação manual. Uma caixa de diálogo solicita que você especificar que a ação manual foi concluída.
- Para criar um script no servidor do VMM, siga as instruções [neste artigo](hyper-v-vmm-recovery-script.md).
- Scripts podem ser aplicados durante o failover para o site secundário e durante o failback do site secundário para o primário. O suporte depende do cenário de replicação:
    
    **Cenário** | **Failover** | **Retorno**
    --- | --- | --- 
    Azure para o Azure  | Runbook | Runbook
    VMware no Azure | Runbook | NA 
    Hyper-V com VMM para Azure | Runbook | Script
    Site do Hyper-V para Azure | Runbook | NA
    VMM para VMM secundário | Script | Script

1. No plano de recuperação, clique na etapa à qual a ação deve ser adicionada e especifique quando a ação deve ocorrer:
    1. Se quiser que a ação ocorra antes de os computadores no grupo serem iniciados após o failover, selecione **Adicionar pré-ação**.
    1. Se quiser que a ação ocorra depois de os computadores no grupo serem iniciados após o failover, selecione **Adicionar pós-ação**. Para mover a posição da ação, selecione os botões **Mover para cima** ou **Mover para baixo**.
2. Em **Inserir ação**, selecione **Script** ou **Ação manual**.
3. Se você quiser adicionar uma ação manual, faça o seguinte:
    1. Digite um nome para a ação e as instruções da ação. A pessoa que está executando o failover verá essas instruções.
    1. Especifique se deseja adicionar a ação manual para todos os tipos de failover (Teste, Failover, Failover planejado (se relevante)). Em seguida, clique em **OK**.
4. Se você quiser adicionar um script, faça o seguinte:
    1. Se estiver adicionando um script do VMM, selecione **Failover para script do VMM** e, em **Caminho do Script**, digite o caminho relativo para o compartilhamento. Por exemplo, se o compartilhamento está localizado em \\\<VMMServerName>\MSSCVMMLibrary\RPScripts, especifique o caminho: \RPScripts\RPScript.PS1.
    1. Se estiver adicionando um runboook da automação do Azure, especifique a **Conta de Automação do Azure** na qual o runbook está localizado e selecione o **Script de Runbook do Azure** apropriado.
5. Execute um failover de teste do plano de recuperação para garantir que o script funciona conforme esperado.

## <a name="watch-a-video"></a>Assistir a um vídeo

Assista a um vídeo que demonstra como criar um plano de recuperação.


> [!VIDEO https://www.youtube.com/embed/1KUVdtvGqw8]

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [execução de failovers](site-recovery-failover.md).  

    
