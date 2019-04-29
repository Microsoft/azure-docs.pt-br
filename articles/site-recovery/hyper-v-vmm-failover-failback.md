---
title: Failover e failback VMs do Hyper-V replicadas para um data center secundário durante a recuperação de desastre com o Azure Site Recovery | Microsoft Docs
description: Saiba como fazer o failover de VMs do Hyper-V em seu site local secundário e fazer failback para o site primário, durante a recuperação de desastre com o Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/27/2018
ms.author: raynew
ms.openlocfilehash: 3890283f70bfd922d0c436f76b38d7f81d7b8c6f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61471512"
---
# <a name="fail-over-and-fail-back-hyper-v-vms-replicated-to-your-secondary-on-premises-site"></a>Executar failover e failback em VMs do Hyper-V replicadas para o site local secundário

O serviço [Azure Site Recovery](site-recovery-overview.md) gerencia e orquestra a replicação, o failover e o failback de máquinas locais e de VMs (máquinas virtuais) do Azure.

Este artigo descreve como executar failover de uma VM do Hyper-V gerenciada em uma nuvem do System Center VMM (Virtual Machine Manager), para um site do VMM secundário. Depois de executar failover, execute failback para o site local quando ele estiver disponível. Neste artigo, você aprenderá a:

> [!div class="checklist"]
> * Executar failover de uma VM do Hyper-V de uma nuvem do VMM primária para uma nuvem do VMM secundária
> * Proteger novamente do site secundário para o primário e executar failback
> * Se desejar, iniciar a replicação do primário para o secundário novamente

## <a name="failover-and-failback"></a>Failover e failback

O failover e o failback têm três estágios:

1. **Executar failover para um site secundário**: Failover de máquinas do site primário para o secundário.
2. **Failback do site secundário**: Replicar as VMs do secundário para o primário e executar um failover planejado para failback.
3. Após o failover planejado, se desejar inicie a replicação do site primário para o secundário novamente.


## <a name="prerequisites"></a>Pré-requisitos

- Verifique se você concluiu uma [simulação de recuperação de desastre](hyper-v-vmm-test-failover.md) para verificar se tudo está funcionando conforme o esperado.
- Para concluir o failback, verifique se os servidores VMM primário e secundário estão conectados ao Site Recovery.



## <a name="run-a-failover-from-primary-to-secondary"></a>Executar um failover do primário para o secundário

Você pode executar um failover planejado ou regular para VMs do Hyper-V.

- Use um failover regular nas interrupções inesperadas. Quando você executa esse failover, o Site Recovery cria e liga uma VM no site secundário. Pode ocorrer perda de dados dependendo de dados pendentes que ainda não foram sincronizados.
- Um failover planejado pode ser usado para manutenção ou durante uma interrupção esperada. Essa opção não causa perda de dados. Quando um failover planejado é disparado, as VMs de origem são desligadas. Os dados não sincronizados são sincronizados e o failover é disparado. 
- 
  Este procedimento descreve como executar um failover regular.


1. Em **Configurações** > **Itens replicados** clique em VM > **Failover**.
1. Selecione **Desligar o computador antes do início do failover** se quiser que o Site Recovery tente realizar um desligamento das VMs de origem antes de disparar o failover. O Site Recovery também tenta sincronizar os dados locais que ainda não foram enviados para o site secundário antes de disparar o failover. Observe que o failover continuará mesmo se o desligamento falhar. Você pode acompanhar o progresso do failover na página **Trabalhos** .
2. Agora a VM deve ser exibida na nuvem do VMM secundária.
3. Depois de verificar se VM, **Confirme** o failover. Essa ação exclui todos os pontos de recuperação disponíveis.

> [!WARNING]
> **Não cancele um failover em andamento**: Antes de iniciar o failover, a replicação de VM será interrompida. Se você cancelar um failover em andamento, o failover será interrompido, mas a VM não será replicada novamente.  


## <a name="reverse-replicate-and-failover"></a>Replicação inversa e failover

Inicie a replicação do site secundário para o primário e o failback para o site primário. Depois que as VMs estiverem em execução no site primário novamente, você poderá replicá-las para o site secundário.  

 
1. Clique em VM > clique em **Replicação Inversa**.
2. Quando o trabalho estiver concluído, clique na VM. Em **Failover**, verifique a direção do failover (da nuvem do VMM secundário) e selecione os locais de origem e destino. 
4. Inicie o failover. Você pode acompanhar o progresso do failover na guia **Trabalhos** .
5. Na nuvem do VMM primária, verifique se a VM está disponível.
6. Se você deseja reiniciar a replicação da VM primária para o site secundário novamente, clique em **Replicação Inversa**.

## <a name="next-steps"></a>Próximas etapas
[Reveja a etapa](hyper-v-vmm-disaster-recovery.md) de replicação de VMs Hyper-V em um site secundário.
