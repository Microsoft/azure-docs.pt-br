---
title: Fazer failover de VMs do VMware no Azure com o Site Recovery
description: Saiba como fazer failover de VMs do VMware no Azure usando o Azure Site Recovery
ms.service: site-recovery
ms.topic: tutorial
ms.date: 12/16/2019
ms.custom: MVC
ms.openlocfilehash: bf47f08ac555cf60f59ba2b1a84750b6a9e2e0a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "86131997"
---
# <a name="fail-over--vmware-vms"></a>Fazer failover de VMs do VMware

Este artigo descreve como fazer failover de uma VM (máquina virtual) do VMware local para o Azure com o [Azure Site Recovery](site-recovery-overview.md).

Este é o quinto tutorial em uma série que mostra como configurar a recuperação de desastres para o Azure para computadores locais.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Verifique se as propriedades da VM VMware estão em conformidade com os requisitos do Azure.
> * Faça failover de VMs específicas no Azure.

> [!NOTE]
> Os tutoriais mostram o caminho de implantação mais simples para um cenário. Eles usam opções padrão quando possível e não mostram todas as configurações e caminhos possíveis. Se você quiser saber mais sobre failover em detalhes, confira [Fazer failover de VMs e servidores físicos](site-recovery-failover.md).

[Saiba mais sobre](failover-failback-overview.md#types-of-failover) diferentes tipos de failover. Se você quiser fazer failover de várias VMs em um plano de recuperação, examine [este artigo](site-recovery-failover.md).

## <a name="before-you-start"></a>Antes de começar

Conclua os tutoriais anteriores:

1. Verifique se você [configurou o Azure](tutorial-prepare-azure.md) para recuperação de desastre local de VMs VMware, VMs do Hyper-V e computadores físicos para o Azure.
2. Prepare seu ambiente [VMware](vmware-azure-tutorial-prepare-on-premises.md) local para a recuperação de desastre. 
3. Configure a recuperação de desastre para [VMs do VMware](vmware-azure-tutorial.md).
4. Execute uma [simulação de recuperação de desastres](tutorial-dr-drill-azure.md) para verificar se tudo está funcionando conforme o esperado.

## <a name="verify-vm-properties"></a>Verificar as propriedades da VM

Antes de executar um failover, verifique as propriedades da VM para assegurar se as VMs estão em conformidade com os [requisitos do Azure](vmware-physical-azure-support-matrix.md#replicated-machines).

Verifique as propriedades da seguinte maneira:

1. Nos **itens protegidos**, selecione **itens replicados** e, em seguida, selecione a VM que você deseja verificar.

2. No painel **Item Replicado**, há um resumo das informações da VM, o status de integridade e os últimos pontos de recuperação disponíveis. Selecione **Propriedades** para exibir mais detalhes.

3. Na **Computação e Rede**, você pode modificar essas propriedades conforme necessário:
    * Nome do Azure
    * Resource group
    * Tamanho de destino
    * [Conjunto de disponibilidade](../virtual-machines/windows/tutorial-availability-sets.md)
    * Configurações de disco gerenciado

4. É possível exibir e modificar as configurações de rede, incluindo:

    * A rede e a sub-rede na qual a VM do Azure ficará localizada após o failover.
    * O endereço IP que será atribuído a ela.

5. Em **Discos**, é possível visualizar as informações sobre o sistema operacional e os discos de dados na VM.

## <a name="run-a-failover-to-azure"></a>Executar um failover para o Azure

1. Em **Configurações** > **Itens replicados**, selecione a VM cujo failover você deseja fazer e, em seguida, selecione **Failover**.
2. Em **Failover**, selecione um **Ponto de Recuperação** para o qual fazer o failover. Você pode usar uma das seguintes opções:
   * **Mais recente**: essa opção primeiro processa todos os dados enviados ao Site Recovery. Ela fornece o RPO (objetivo de ponto de recuperação) mais baixo porque a VM do Azure criada após o failover tem todos os dados que foram replicados para o Site Recovery quando o failover foi disparado.
   * **Mais recente processado**: Essa opção executa failover da VM para o último ponto de recuperação processado pelo Site Recovery. Essa opção fornece um RTO (Objetivo do Tempo de Recuperação) baixo porque não há tempo gasto para processar dados não processados.
   * **Consistente com o aplicativo mais recente**: Essa opção faz failover da VM para o ponto de recuperação consistente com o aplicativo mais recente pelo Site Recovery.
   * **Personalizado**: Essa opção permite especificar um ponto de recuperação.

3. Selecione **Desligar o computador antes de iniciar o failover** para tentar desligar as VMs de origem antes de disparar o failover. O failover continuará, mesmo se o desligamento falhar. Você pode acompanhar o progresso do failover na página **Trabalhos** .

Em alguns cenários, o failover requer um processamento adicional que leva cerca de oito a dez minutos para ser concluído. Você pode observar tempos de failover de teste mais longos para:

* VMs VMware executando uma versão do Serviço de Mobilidade anterior à 9.8.
* Servidores físicos.
* VMs VMware Linux.
* VMs Hyper-V protegidas como servidores físicos.
* VMs VMware que não têm o serviço DHCP habilitado.
* VMs VMware que não têm os seguintes drivers de inicialização: storvsc, vmbus, storflt, intelide, atapi.

> [!WARNING]
> Não cancele um failover em andamento. Antes de iniciar o failover, a replicação de VM será interrompida. Se você cancelar um failover em andamento, o failover será interrompido, mas a VM não será replicada novamente.

## <a name="connect-to-failed-over-vm"></a>Conectar-se à VM cujo failover foi realizado

1. Se você quiser se conectar a VMs do Azure após fazer failover usando o protocolo RDP e o SSH (Secure Shell), [verifique se os requisitos foram atendidos](failover-failback-overview.md#connect-to-azure-after-failover).
2. Após o failover, vá para a VM e valide [conectando](../virtual-machines/windows/connect-logon.md)-se a ela.
3. Use **Alterar ponto de recuperação** se você quiser usar um ponto de recuperação diferente após o failover. Após você confirmar o failover na próxima etapa, essa opção não estará mais disponível.
4. Após a validação, selecione **Confirmar** para finalizar o ponto de recuperação da VM após o failover.
5. Após a confirmação, todos os outros pontos de recuperação disponíveis são excluídos. A etapa conclui o failover.

>[!TIP]
> Se você encontrar problemas de conectividade após o failover, siga o [guia de solução de problemas](site-recovery-failover-to-azure-troubleshoot.md).

## <a name="next-steps"></a>Próximas etapas

Após o failover, proteja novamente as VMs do Azure para o local. Em seguida, depois que as VMs estiverem protegidas novamente e replicando para o site local, faça failback do Azure quando estiver pronto.

> [!div class="nextstepaction"]
> [Proteger novamente VMs do Azure](vmware-azure-reprotect.md)
> [Failback do Azure](vmware-azure-failback.md)
