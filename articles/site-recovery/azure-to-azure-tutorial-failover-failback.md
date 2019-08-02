---
title: Fazer failover e proteger novamente VMs do Azure replicadas para uma região secundária do Azure para recuperação de desastre com o serviço Azure Site Recovery.
description: Saiba como fazer failover e proteger novamente VMs do Azure replicadas para uma região secundária do Azure para recuperação de desastre, com o serviço Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 07/01/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 836b6bb76ff608fed0f34e40fe450d593fec540a
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67514163"
---
# <a name="fail-over-and-reprotect-azure-vms-between-regions"></a>Fazer failover e proteger novamente VMs do Azure entre regiões

Este tutorial descreve como fazer failover de uma máquina virtual (VM) do Azure para uma região secundária do Azure com o serviço do [Azure Site Recovery](site-recovery-overview.md). Após fazer failover, proteja novamente a VM. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Fazer failover da VM do Azure
> * Proteja novamente a VM do Azure secundária para que ela seja replicada para a região primária.

> [!NOTE]
> Este tutorial contém o caminho mais simples com configurações padrão e personalização mínima. Para ver cenários mais complexos, use os artigos em “Como” para VMs do Azure.


## <a name="prerequisites"></a>Pré-requisitos

- Antes de começar, reveja as [perguntas frequentes](site-recovery-faq.md#failover) sobre failover.
- É importante realizar uma [simulação de recuperação de desastre](azure-to-azure-tutorial-dr-drill.md) para verificar se tudo está funcionando conforme o esperado.
- Verifique as propriedades da VM antes de executar o failover de teste. A VM deve atender aos [requisitos do Azure](azure-to-azure-support-matrix.md#replicated-machine-operating-systems).

## <a name="run-a-failover-to-the-secondary-region"></a>Executar um failover para a região secundária

1. Em **Itens replicados**, selecione a VM cujo failover você deseja fazer > **Failover**

   ![Failover](./media/azure-to-azure-tutorial-failover-failback/failover.png)

2. Em **Failover**, selecione um **Ponto de Recuperação** para o qual fazer o failover. Você pode usar uma das seguintes opções:

   * **Mais recente** (padrão): processa todos os dados no serviço Site Recovery e fornece o menor RPO (Objetivo de Ponto de Recuperação).
   * **Mais recente processado**: Reverte a máquina virtual para o ponto de recuperação mais recente que foi processado pelo serviço Site Recovery.
   * **Personalizado**: Faz failover para um determinado ponto de recuperação. Essa opção é útil para fazer um failover de teste.

3. Selecione **Desligar o computador antes do início do failover** se quiser que o Site Recovery tente realizar um desligamento das VMs de origem antes de disparar o failover. O desligamento ajuda a evitar a perda de dados. O failover continuará mesmo o desligamento falhar. O Site Recovery não limpa a origem após o failover.

4. Acompanhe o progresso do failover na página **Trabalhos**.

5. Após o failover, valide a máquina virtual, efetuando logon nela. Se desejar ir a outro ponto de recuperação para a máquina virtual, você pode usar a opção **Alterar ponto de recuperação**.

6. Quando estiver satisfeito com a máquina virtual que passou por failover, você pode **Confirmar** o failover.
   Confirmar exclui todos os pontos de recuperação disponíveis com o serviço. Agora não será possível alterar o ponto de recuperação.

> [!NOTE]
> Quando você faz o failover de uma máquina virtual à qual você adiciona um disco após habilitar a replicação da VM, os pontos da replicação mostrarão os discos que estão disponíveis para recuperação. Por exemplo, se uma VM tiver um único disco e você adicionar um novo, os pontos de replicação que foram criados antes de você adicionar o disco mostrará que o ponto de replicação consiste em "1 de 2 discos".

![Fazer failover com um disco adicionado](./media/azure-to-azure-tutorial-failover-failback/failover-added.png)

## <a name="reprotect-the-secondary-vm"></a>Proteger novamente a VM secundária

Após o failover da VM, você precisa protegê-la novamente para que ela seja replicada de volta para a região primária.

1. Verifique se a VM está no estado **Failover confirmado** e verifique se a região primária está disponível e se você é capaz de criar e acessar novos recursos nela.
2. Em **Cofre** > **Itens replicados**, clique com o botão direito do mouse na VM em que o failover foi executado e, em seguida, selecione **Proteger Novamente**.

   ![Clicar com o botão direito do mouse para proteger novamente](./media/azure-to-azure-tutorial-failover-failback/reprotect.png)

2. Verifique se a direção da proteção, da região secundária para a primária, já está selecionada.
3. Examine as informações de **Grupo de recursos, Rede, Armazenamento e Conjuntos de disponibilidade**. Quaisquer recursos marcados como novos são criados como parte da operação de proteger novamente.
4. Clique em **OK** para disparar um trabalho de proteger novamente. Esse trabalho propaga os dados mais recentes para o site de destino. Em seguida, ele replica os deltas para a região primária. A VM agora está em um estado protegido.

## <a name="next-steps"></a>Próximas etapas
- Após proteger novamente, [saiba como](azure-to-azure-tutorial-failback.md) realizar failback para a região primária quando ela estiver disponível.
- [Saiba mais](azure-to-azure-how-to-reprotect.md#what-happens-during-reprotection) sobre o fluxo de nova proteção.
