---
title: "Executar failover e failback de VMs VMware e de servidores físicos replicados para Azure com o Site Recovery | Microsoft Docs"
description: "Saiba como executar failover de VMs VMware e de servidores físicos para o Azure e o failback para o site local, com o Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/18/2017
ms.author: raynew
ms.openlocfilehash: 45f1c61189b338d38681c8e93be01953da65913f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="fail-over-and-fail-back-vmware-vms-and-physical-servers-replicated-to-azure"></a>Executar failover e failback de VMs VMware e de servidores físicos replicados para Azure

Este tutorial descreve como efetuar failover de uma VM VMware para o Azure. Depois de executar failover, execute failback para o site local quando ele estiver disponível. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Verificar as propriedades da VM VMware para saber se elas estão em conformidade com os requisitos do Azure
> * Executar um failover para o Azure
> * Criar um servidor de processo e o servidor de destino mestre para failback
> * Proteger novamente as VMs do Azure para o site local
> * Executar failover do Azure para o local
> * Proteger novamente as VMs locais, para iniciar novamente a replicação para o Azure

Este é o quinto tutorial de uma série. Este tutorial presume que você já tenha concluído as tarefas nos tutoriais anteriores.

1. [Preparar o Azure](tutorial-prepare-azure.md)
2. [Preparar o VMware local](tutorial-prepare-on-premises-vmware.md)
3. [Configurar a recuperação de desastre](tutorial-vmware-to-azure.md)
4. [Realizar uma simulação de recuperação de desastre](tutorial-dr-drill-azure.md)

## <a name="preparing-for-failover-and-failback"></a>Preparando para failover e failback

Verifique se não há nenhum instantâneo na VM. A VM local é desativada durante o processo de nova proteção.
Isso ajuda a garantir a consistência de dados durante a replicação. Não ative a VM após a conclusão da nova proteção. Use o mesmo servidor de destino mestre para proteger novamente um grupo de replicação. Se você usar um servidor de destino mestre diferente para proteger novamente um grupo de replicação, o servidor não poderá fornecer um ponto comum no tempo.

O failover e o failback têm quatro fases:

1. **Failover para o Azure**: faz failover de computadores do site local para o Azure.
2. **Proteger novamente as VMs do Azure**: proteja as VMs do Azure novamente, para que elas comecem a replicação para as VMs VMware locais.
3. **Executar failover para o local**: execute um failover, para executar failback do Azure.
4. **Proteger novamente as VMs locais**: após o failback do dados, proteja as VMs locais para as quais o failback foi executado, para que elas iniciem a replicação para o Azure.

## <a name="verify-vm-properties"></a>Verificar as propriedades da VM

Confira as propriedades da VM e verifique se a VM está em conformidade com os [Requisitos do Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).

1. Em **Itens Protegidos**, clique em **Itens Replicados** > VM.

2. No painel **Item Replicado**, há um resumo das informações da VM, o status de integridade e os últimos pontos de recuperação disponíveis. Clique em **Propriedades** para exibir mais detalhes.

3. Em **Computação e Rede**, você pode modificar o nome do Azure, o grupo de recursos, o tamanho do destino, o [conjunto de disponibilidade](../virtual-machines/windows/tutorial-availability-sets.md) e as [configurações de disco gerenciado](#managed-disk-considerations)

4. Você pode exibir ou modificar as configurações de rede, incluindo a rede/sub-rede na qual a VM do Azure estará localizada após o failover e o endereço IP que será atribuído a ela.

5. Em **Discos**, é possível visualizar as informações sobre o sistema operacional e os discos de dados na VM.

## <a name="run-a-failover-to-azure"></a>Executar um failover para o Azure

1. Em **Configurações** > **Itens replicados** clique em VM > **Failover**.

2. Em **Failover**, selecione um **Ponto de Recuperação** para executar o failover. Você pode usar uma das seguintes opções:
   - **Último** (padrão): essa opção primeiro processa todos os dados enviados ao Site Recovery. Ela fornece o RPO (objetivo de ponto de recuperação) mais baixo porque a VM do Azure criada após o failover tem todos os dados que foram replicados para o Site Recovery quando o failover foi disparado.
   - **Últimos processados**: essa opção executa failover da VM para o último ponto de recuperação processado pelo Site Recovery. Essa opção fornece um RTO (Objetivo do Tempo de Recuperação) baixo porque não há tempo gasto para processar dados não processados.
   - **Consistente com o aplicativo mais recente**: essa opção executa failover de todas as VMs para o ponto de recuperação consistente com o aplicativo mais recente pelo Site Recovery.
   - **Personalizado**: especifica um ponto de recuperação.

3. Selecione **Desligar o computador antes de iniciar o failover** para tentar fazer um desligamento das máquinas virtuais de origem antes de disparar o failover. O failover continuará mesmo o desligamento falhar. Você pode acompanhar o progresso do failover na página **Trabalhos** .

4. Se você preparou a conexão com a VM do Azure, conecte-se para validá-la após o failover.

5. Depois de verificar, **Confirme** o failover. Essa ação exclui todos os pontos de recuperação disponíveis.

> [!WARNING]
> **Não cancelar um failover em andamento**: antes de iniciar o failover, a replicação da VM é interrompida.
> Se você cancelar um failover em andamento, o failover será interrompido, mas a VM não será replicada novamente.

Em alguns cenários, o failover requer um processamento adicional que leva cerca de oito a dez minutos para ser concluído. É possível observar um tempo de failover de teste mais longo para servidores físicos, máquinas VMware Linux, VMs VMware que não têm o serviço DHCP habilitado e VMs VMware que não têm os seguintes drivers de inicialização: storvsc, vmbus, storflt, intelide, atapi.

## <a name="create-a-process-server-in-azure"></a>Criar um servidor de processo no Azure

O servidor de processo recebe dados da VM do Azure e envia-os para o site local. Uma rede de baixa latência é necessária entre o servidor de processo e a VM protegida.

- Para fins de teste, se você tiver uma conexão Azure ExpressRoute, será possível usar o servidor de processo local que é instalado automaticamente no servidor de configuração.
- Se você tiver uma conexão VPN ou estiver executando o failback em um ambiente de produção, você deverá configurar uma VM do Azure como um servidor de processo baseado no Azure para failback.
- Para configurar um servidor de processo no Azure, siga as instruções [neste artigo](site-recovery-vmware-setup-azure-ps-resource-manager.md).

## <a name="configure-the-master-target-server"></a>Configurar o servidor de destino mestre

Por padrão, o servidor de destino mestre é executado no servidor de configuração local. Para fins deste tutorial, estamos usando o mestre padrão. O servidor de destino mestre recebe dados de failback.

Se a VM estiver em um host ESXi gerenciado por um vCenter Server, o servidor de destino mestre deve ter acesso ao armazenamento de dados da VM (VMDK), para gravar os dados replicados nos discos da VM. Verifique se o armazenamento de dados da VM está montado no host de destino mestre com acesso de leitura/gravação.

Se a VM estiver em um ESXi que não seja gerenciado por um vCenter Server, o serviço Site Recovery criará uma nova VM durante o processo de nova proteção. A VM é criada no host ESX na qual você cria o destino mestre.
O disco rígido da VM deve estar em um armazenamento de dados acessível para o host no qual o servidor de destino mestre está em execução.

Se a VM não usa o vCenter, você deverá concluir a descoberta do host no qual o servidor de destino mestre está em execução para que seja possível proteger o computador novamente. Isso também vale para failback de servidores físicos. Outra opção, se a VM local existir, ela deverá ser excluída antes da execução de um failback. O failback vai criar uma nova VM no mesmo host que o host ESX de destino mestre. Ao realizar o failback para um local alternativo, os dados são recuperados no mesmo armazenamento de dados e no mesmo host ESX usados pelo servidor de destino mestre local.

Você não pode usar o Storage vMotion no servidor de destino mestre. Se você fizer isso, o failback não funcionará, porque os discos não estarão disponíveis para ele. Exclua os servidores de destino mestres de sua lista do vMotion.

## <a name="reprotect-azure-vms"></a>Proteger as VMs do Azure

Este procedimento considera que a VM local não está disponível e que você está protegendo novamente para um local alternativo.

1. Em **Configurações** > **Itens replicados**, clique com o botão direito do mouse na VM com failover > **Proteger novamente**.
2. Em **Proteger novamente**, verifique se **Do Azure para local** está selecionado.
3. Especifique o servidor de destino mestre local e o servidor de processo.

4. Em **Armazenamento de dado**, selecione o armazenamento de dados de destino mestre para o qual deseja recuperar os discos locais. Use esta opção quando a VM local tiver sido excluída e você precisar criar novos discos. Essa configuração será ignorada se os discos já existirem, mas você precisa especificar um valor.
5. Selecione a unidade de retenção de destino mestre. A politica de failback é selecionada automaticamente.
6. Clique em **OK** para iniciar a nova proteção. Um trabalho começará a replicar a máquina virtual do Azure para o site local. Você pode acompanhar o andamento na guia **Trabalhos** .

> [!NOTE]
> Se você deseja recuperar a VM do Azure para uma VM local existente, monte o armazenamento de dados da máquina virtual local com acesso de leitura/gravação no host ESXi do servidor de destino mestre.


## <a name="run-a-failover-from-azure-to-on-premises"></a>Executar um failover do Azure para o local

Para replicar novamente para o local, uma política de failback é usada. Essa política foi criada automaticamente quando você criou uma política de replicação para a replicação para o Azure:

- A política é associada automaticamente ao servidor de configuração.
- A política não pode ser modificada.
- Os valores da política são:
    - Limite RPO = 15 minutos
    - Retenção de ponto de recuperação = 24 horas
    - Frequência de instantâneos consistentes com o aplicativo = 60 minutos

Execute o failover da seguinte maneira:

1. Na página **Itens duplicados**, clique como botão direito do mouse no computador > **Failover Não Planejado**.
2. Em **Confirmar Failover**, verifique se a direção do failover é do Azure.

3. Selecione o ponto de recuperação que você deseja usar para o failover. Um ponto de recuperação consistente com o aplicativo ocorre antes do ponto mais recente no tempo, e vai causar perda de dados. Quando o failover é executado, o Site Recovery encerra as VMs do Azure e inicializa a VM local. Haverá em certo tempo de inatividade, portanto escolha um momento apropriado.
4. Com o botão direito do mouse no computador e, em seguida, clique em **Confirmar**. Isso dispara um trabalho que remove as VMs do Azure.
5. Verifique se as VMs do Azure foram desligadas conforme o esperado.


## <a name="reprotect-on-premises-machines-to-azure"></a>Proteger novamente as máquinas locais para o Azure

Os dados agora devem estar de volta no site local, mas eles não estão sendo replicados para o Azure. Você pode iniciar a replicação para o Azure novamente da seguinte maneira:

1. Em cofre > **Configurações** >**Itens replicados**, selecione as VMs com failback e clique em **Proteger novamente**.
2. Selecione o servidor de processo que é usado para enviar os dados replicados para o Azure e, em seguida, clique em **OK**.

Depois que a nova proteção for concluída, a VM será replicada novamente para o Azure e você poderá executar failover conforme o necessário.
