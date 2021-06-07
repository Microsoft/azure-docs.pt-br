---
title: Configurar failover e failback para servidores físicos com Site Recovery
description: Saiba como executar failover de servidores físicos para o Azure e failback para o site local para recuperação de desastre com o Azure Site Recovery
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 12/17/2019
ms.author: raynew
ms.openlocfilehash: 2994f68e4159c7c4aa7d82bef7a5891deb5055a0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96017416"
---
# <a name="fail-over-and-fail-back-physical-servers-replicated-to-azure"></a>Executar failover e failback de servidores físicos replicados para Azure

Este tutorial descreve como fazer failover de servidores físicos locais que estão replicando para o Azure com o [Azure site Recovery](site-recovery-overview.md). Após o failover, você faz o failback do Azure para seu site local quando ele está disponível.

## <a name="before-you-start"></a>Antes de começar

- [Saiba mais](failover-failback-overview.md) sobre o processo de failover na recuperação de desastre.
- Se você quiser fazer failover de vários computadores, [Aprenda](recovery-plan-overview.md) a reunir computadores em um plano de recuperação.
- Antes de fazer um failover completo, execute uma [análise de recuperação de desastre](site-recovery-test-failover-to-azure.md) para garantir que tudo esteja funcionando conforme o esperado.
- Siga [estas instruções](site-recovery-failover.md#prepare-to-connect-after-failover) para se preparar para se conectar às VMs do Azure após o failover.



## <a name="run-a-failover"></a>Executar um failover

### <a name="verify-server-properties"></a>Verificar as propriedades do servidor

Confira as propriedades do servidor e verifique se ele está em conformidade com os [Requisitos do Azure](vmware-physical-azure-support-matrix.md#replicated-machines) quanto a VMs do Azure.

1. Em **Itens Protegidos**, clique em **Itens Replicados** e selecione o computador.
2. No painel **Item Replicado**, há um resumo das informações do computador, o status de integridade e os últimos pontos de recuperação disponíveis. Clique em **Propriedades** para exibir mais detalhes.
3. Em **computação e rede**, você pode modificar o nome do Azure, o grupo de recursos, o tamanho do destino, o [conjunto de disponibilidade](../virtual-machines/windows/tutorial-availability-sets.md)e as configurações de disco gerenciado
4. Você pode exibir ou modificar as configurações de rede, incluindo a rede/sub-rede na qual a VM do Azure estará localizada após o failover e o endereço IP que será atribuído a ela.
5. Em **Discos**, é possível visualizar as informações sobre o sistema operacional e os discos de dados do computador.

### <a name="fail-over-to-azure"></a>Fazer failover para o Azure

1. Em **Configurações** > **Itens replicados** clique no computador > **Failover**.
2. Em **Failover**, selecione um **Ponto de Recuperação** para executar o failover. Você pode usar uma das seguintes opções:
   - **Mais recente**: essa opção primeiro processa todos os dados enviados ao Site Recovery. Ela fornece o RPO (objetivo de ponto de recuperação) mais baixo porque a VM do Azure criada após o failover tem todos os dados que foram replicados para o Site Recovery quando o failover foi disparado.
   - **Últimos processados**: essa opção executa failover do computador para o último ponto de recuperação processado pelo Site Recovery. Essa opção fornece um RTO (Objetivo do Tempo de Recuperação) baixo porque não há tempo gasto para processar dados não processados.
   - **Consistente com o aplicativo mais recente**: essa opção executa failover do computador para o ponto de recuperação consistente com o aplicativo mais recente pelo Site Recovery.
   - **Personalizado**: especifica um ponto de recuperação.

3. Selecione **Desligar o computador antes do início do failover** se quiser que o Site Recovery tente realizar um desligamento o computador de origem antes de disparar o failover. O failover continuará mesmo o desligamento falhar. Você pode acompanhar o progresso do failover na página **Trabalhos** .
4. Se você preparou a conexão com a VM do Azure, conecte-se para validá-la após o failover.
5. Depois de verificar, **Confirme** o failover. Essa ação exclui todos os pontos de recuperação disponíveis.

> [!WARNING]
> Não cancele um failover em andamento. Antes do início do failover, a replicação do computador é interrompido. Se você cancelar o failover, ele será interrompido, mas o computador não replicará novamente.
> Para servidores físicos, o processamento adicional de failover pode levar cerca de oito a dez minutos para concluir.

## <a name="automate-actions-during-failover"></a>Automatizar ações durante o failover

Talvez você queira automatizar ações durante o failover. Para fazer isso, você pode usar scripts ou runbooks de automação do Azure em planos de recuperação.

- [Saiba mais](site-recovery-create-recovery-plans.md) sobre como criar e personalizar planos de recuperação, incluindo a adição de scripts.
- [Saiba](site-recovery-runbook-automation.md) está confinado no adicionando Runbooks de automação do Azure aos planos de recuperação.

## <a name="configure-settings-after-failover"></a>Definir configurações após o failover

Após o failover, você precisa [definir as configurações do Azure](site-recovery-failover.md#prepare-in-azure-to-connect-after-failover) para se conectar às VMs do Azure replicadas. Além disso, configure o endereçamento IP [interno e público](site-recovery-failover.md#set-up-ip-addressing) .

## <a name="prepare-for-reprotection-and-failback"></a>Preparar para nova proteção e failback

Após o failover para o Azure, você protege novamente as VMs do Azure replicando-as para o site local. Depois de replicar, você pode fazer failback deles de volta para o local, executando um failover do Azure para seu site local.

1. Servidores físicos replicados no Azure usando o Site Recovery só podem falhar como VMs VMware. Você precisa de uma infraestrutura de VMware local para executar o failback. Siga as etapas neste [artigo](vmware-azure-prepare-failback.md) para se preparar para a nova proteção e o failback, incluindo a configuração de um servidor de processo no Azure e um servidor de destino mestre local e a configuração de uma VPN site a site ou um emparelhamento privado de ExpressRoute para failback.
2. Verifique se o servidor de configuração local está em execução e conectado ao Azure. Durante o failover para o Azure, o site local pode não estar acessível e o servidor de configuração pode estar indisponível ou desligado. Durante o failback, a VM deve existir no banco de dados do servidor de configuração. Caso contrário, o failback será malsucedido.
3. Exclua todos os instantâneos no servidor de destino mestre local. A nova proteção não funcionará se houver instantâneos.  Os instantâneos na VM são mesclados automaticamente durante um trabalho de nova proteção.
4. Se você estiver protegendo novamente as VMs coletadas em um grupo de replicação para consistência de várias VMS, verifique se todas elas têm o mesmo sistema operacional (Windows ou Linux) e se o servidor de destino mestre implantado tem o mesmo tipo de sistema operacional. Todas as VMs em um grupo de replicação devem usar o mesmo servidor de destino mestre.
5. Abra [as portas necessárias](vmware-azure-prepare-failback.md#ports-for-reprotectionfailback) para failback.
6. Verifique se o vCenter Server está conectado antes do failback. Caso contrário, desconectando discos e anexá-los de volta para a máquina virtual falha.
7. Se um vCenter Server gerenciar as VMs para as quais você fará failback, verifique se você tem as permissões necessárias. Se você executar a descoberta de vCenter de Usuário somente leitura e proteger as máquinas virtuais, a proteção terá êxito e o failover funcionará. No entanto, durante a nova proteção, o failover falha porque os repositórios de armazenamento não podem ser descobertos e não são listados durante a nova proteção. Para resolver esse problema, você pode atualizar as credenciais do vCenter com uma [conta/permissões apropriadas](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) e, depois, repita o trabalho. 
8. Se você usou um modelo para criar suas máquinas virtuais, verifique se cada VM tem os próprios UUIDs para os discos. Se o UUID de VM local conflitar com o UUID do servidor de destino mestre porque ambos foram criados a partir do mesmo modelo, a nova proteção falhará. Implantar de um modelo diferente.
9. Se você estiver fazendo failback para um vCenter Server alternativo, o novo vCenter Server e o servidor de destino mestre deverão ser descobertos. Normalmente, se não forem, os armazenamentos de dados não estarão acessíveis ou não estarão visíveis em **Proteger Novamente**.
10. Verifique os seguintes cenários em que você não pode realizar failback:
    - Se você estiver usando a edição gratuita ESXi 5.5 ou a edição gratuita do hipervisor vSphere 6. Atualize para uma versão diferente.
    - Se você tem um servidor físico do Windows Server 2008 R2 SP1.
    - VMs que foram migradas.
    - Uma VM que foi movida para outro grupo de recursos.
    - Uma VM do Azure de réplica que foi excluída.
    - Uma VM do Azure de réplica que não está protegida (replicando para o site local).
10. [Examine os tipos de failback](concepts-types-of-failback.md) você pode usar – recuperação de localização original e recuperação de localização alternativa.


## <a name="reprotect-azure-vms-to-an-alternate-location"></a>Proteger novamente as VMs do Azure para um local alternativo

Este procedimento pressupõe que a VM local não está disponível.

1. No cofre > **configurações**  >  **itens replicados**, clique com o botão direito do mouse no computador que passou por failover > **proteja novamente**.
2. Em **Proteger novamente**, verifique se **Do Azure para local** está selecionado.
3. Especifique o servidor de destino mestre local e o servidor de processo.
4. Em **Armazenamento de dado**, selecione o armazenamento de dados de destino mestre para o qual deseja recuperar os discos locais.
       - Use esta opção se a VM local tiver sido excluída ou não existir, e você precisar criar novos discos.
       - Essa configuração será ignorada se os discos já existirem, mas você precisará especificar um valor.
5. Selecione a unidade de retenção de destino mestre. A politica de failback é selecionada automaticamente.
6. Clique em **OK** para iniciar a nova proteção. Um trabalho começa a replicar a VM do Azure para o site local. Você pode acompanhar o andamento na guia **Trabalhos**.

> [!NOTE]
> Se você deseja recuperar a VM do Azure para uma VM local existente, monte o armazenamento de dados da máquina virtual local com acesso de leitura/gravação no host ESXi do servidor de destino mestre.


## <a name="fail-back-from-azure"></a>Failback do Azure

Execute o failover da seguinte maneira:

1. Na página **Itens duplicados**, clique como botão direito do mouse no computador > **Failover Não Planejado**.
2. Em **Confirmar Failover**, verifique se a direção do failover é do Azure.
3. Selecione o ponto de recuperação que você deseja usar para o failover.
    - Recomendamos que você use o ponto de recuperação **Mais recente**. O ponto consistente do aplicativo está atrás do último ponto no tempo e causa a perda de alguns dados.
    - O **Mais recente**, há um ponto de recuperação consistente em termos de falha.
    - Quando o failover é executado, o Site Recovery encerra as VMs do Azure e inicializa a VM local. Haverá em certo tempo de inatividade, portanto escolha um momento apropriado.
4. Com o botão direito do mouse no computador e, em seguida, clique em **Confirmar**. Isso dispara um trabalho que remove as VMs do Azure.
5. Verifique se as VMs do Azure foram desligadas conforme o esperado.


## <a name="reprotect-on-premises-machines-to-azure"></a>Proteger novamente as máquinas locais para o Azure

Os dados agora devem estar de volta no site local, mas eles não estão sendo replicados para o Azure. Você pode iniciar a replicação para o Azure novamente da seguinte maneira:

1. Em cofre > **Configurações** >**Itens replicados**, selecione as VMs com failback e clique em **Proteger novamente**.
2. Selecione o servidor de processo que é usado para enviar os dados replicados para o Azure e, em seguida, clique em **OK**.


## <a name="next-steps"></a>Próximas etapas

Após a conclusão do trabalho de nova proteção, a VM local está replicando para o Azure. Conforme necessário, você pode [executar outro failover](site-recovery-failover.md) para o Azure.
