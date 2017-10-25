---
title: Migrar para o Azure com o Site Recovery | Microsoft Docs
description: "Este artigo fornece uma visão geral da migração de VMs e servidores físicos para o Azure com o Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: c413efcd-d750-4b22-b34b-15bcaa03934a
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/05/2017
ms.author: raynew
ms.openlocfilehash: f4dfe430fba51bd009431ca72279a21be55e3a40
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="migrate-to-azure-with-site-recovery"></a>Migrar para o Azure com o Site Recovery

Leia este artigo para obter uma visão geral do uso do serviço Azure Site Recovery para a migração de máquinas virtuais e servidores físicos.

A Recuperação de Site é um serviço do Azure que colabora com sua estratégia de BCDR por meio da coordenação da replicação de servidores físicos e máquinas virtuais locais na nuvem (Azure) ou em um datacenter secundário. Quando ocorrem paralisações em seu local primário, você realiza o failover em um local secundário a fim de manter os aplicativos e cargas de trabalho disponíveis. Quando o local primário retoma as operações normais, você realiza o failback. Saiba mais em [O que é Recuperação de Site?](site-recovery-overview.md) Você também pode usar o Site Recovery para migrar suas cargas de trabalho locais existentes para o Azure para agilizar sua jornada de nuvem e se beneficiar da matriz de recursos oferecidos pelo Azure.

Para obter uma rápida visão geral de como realizar uma migração, consulte este vídeo.
>[!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/ASRHowTo-Video2-Migrate-Virtual-Machines-to-Azure/player]

Este artigo descreve a implantação no [portal do Azure](https://portal.azure.com). O [portal clássico do Azure](https://manage.windowsazure.com/) pode ser usado para manter os cofres de Recuperação de Site existentes, mas não é possível criar novos cofres.

Poste comentários na parte inferior deste artigo. Faça perguntas técnicas no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="what-do-we-mean-by-migration"></a>O que queremos dizer por migração?

Você pode implantar o Site Recovery para a replicação de VMs locais e servidores físicos para o Azure ou para um site secundário. Replique computadores, faça o failover deles no site primário quando ocorrem interrupções e faça failback para o site primário ao recuperar-se da interrupção. Além disso, você pode usar o Site Recovery para migrar VMs e servidores físicos para o Azure, para que os usuários possam acessá-los como VMs do Azure. A migração envolve a replicação e o failover do site primário para o Azure e um gesto de conclusão da migração.

## <a name="what-can-site-recovery-migrate"></a>O que o Site Recovery pode migrar?

Você pode:

- Migre cargas de trabalho em execução em VMs do Hyper-V locais, VMs VMware e servidores físicos para execução em VMs do Azure. Você também pode fazer a replicação completa e o failback nesse cenário.
- Migre [VMs IaaS do Azure](site-recovery-migrate-azure-to-azure.md) entre regiões do Azure. Atualmente, apenas a migração tem suporte nesse cenário, o que significa que não há suporte para failback.
- Migre [instâncias do Windows AWS](site-recovery-migrate-aws-to-azure.md) para VMs do IaaS do Azure. Atualmente, apenas a migração tem suporte nesse cenário, o que significa que não há suporte para failback.

## <a name="migrate-on-premises-vms-and-physical-servers"></a>Migrar servidores físicos e VMs locais

Para migrar VMs Hyper-V locais, VMs VMware e servidores físicos, você segue quase as mesmas etapas usadas para a replicação normal.

1. Configurar um cofre dos Serviços de Recuperação
2. Configure os servidores de gerenciamento requeridos (VMware, VMM, Hyper-V, dependendo do que você deseja migrar), adicione-os ao cofre e especifique as configurações de replicação.
3. Habilitar a replicação para as máquinas que você deseja migrar
4. Após a migração inicial, execute um failover de teste rápido para garantir que tudo está funcionando como deveria.
5. Após verificar se o ambiente de replicação está funcionando, você usa um failover planejado ou não, dependendo do [que tem suporte](site-recovery-failover.md) para seu cenário. Recomendamos que você use um failover planejado, quando possível.
6. Para a migração, você não precisa confirmar um failover nem exclui-lo. Em vez disso, você seleciona a opção **Migração Completa** para cada máquina que deseja migrar.
     - Em **Itens Replicados**, clique com botão direito na VM e clique em **Concluir a Migração**. Clique em **OK** para concluir. Você pode acompanhar o progresso nas propriedades da VM, monitorando o trabalho de Migração Completa em **trabalhos do Site Recovery**.
     - A ação **Concluir Migração** conclui o processo de migração, remove a replicação da máquina e interrompe a cobrança de Recuperação de Site para a máquina.

![completemigration](./media/site-recovery-hyper-v-site-to-azure/migrate.png)

## <a name="migrate-between-azure-regions"></a>Migrar entre regiões do Azure

Você pode migrar VMs do Azure entre regiões usando o Site Recovery. Nesse cenário, apenas a migração tem suporte. Em outras palavras, você pode replicar as VMs do Azure e fazer failover para outra região, mas não pode fazer failback. Nesse cenário, você configura um cofre de serviços de recuperação, implanta um servidor de configuração no local para gerenciar a replicação, adiciona-o ao cofre e especifica as configurações de replicação. Você pode habilitar a replicação para os computadores que deseja migrar e executar um failover de teste rápido. Em seguida, execute um failover não planejado com a opção **Migração Completa**.

## <a name="migrate-aws-to-azure"></a>Migrar AWS para o Azure

Você pode migrar instâncias do AWS para VMs do Azure. Nesse cenário, apenas a migração tem suporte. Em outras palavras, você pode replicar as instâncias do AWS e realizar failover no Azure, mas você não pode realizar failback. Instâncias do AWS são tratadas da mesma forma como os servidores físicos para fins de migração. Você configura um cofre de serviços de recuperação, implanta um servidor de configuração no local para gerenciar a replicação, adiciona-o ao cofre e especifica as configurações de replicação. Você pode habilitar a replicação para os computadores que deseja migrar e executar um failover de teste rápido. Em seguida, execute um failover não planejado com a opção **Migração Completa**.




## <a name="next-steps"></a>Próximas etapas

- [Migrar VMs do VMware para o Azure](site-recovery-vmware-to-azure.md)
- [Migrar VMs Hyper-V em nuvens VMM para o Azure](site-recovery-vmm-to-azure.md)
- [Migrar VMs do Hyper-V sem VMM para o Azure](site-recovery-hyper-v-site-to-azure.md)
- [Migrar VMs do Azure entre regiões do Azure](site-recovery-migrate-azure-to-azure.md)
- [Migrar instâncias do AWS para o Azure](site-recovery-migrate-aws-to-azure.md)
- [Prepare as máquinas migradas para habilitar a replicação](site-recovery-azure-to-azure-after-migration.md) em outra região para necessidades de recuperação de desastres.
- Inicie a proteção de suas cargas de trabalho ao [replicar máquinas virtuais do Azure.](site-recovery-azure-to-azure.md)
