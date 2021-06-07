---
title: Proteja novamente as VMs VMware para um site local com Azure Site Recovery
description: Saiba como proteger novamente as VMs VMware após o failover para o Azure com o Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: mayg
ms.openlocfilehash: 6a11e3d0cb41383b44b76975ecbd1c2ae2825015
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "89441486"
---
# <a name="reprotect-from-azure-to-on-premises"></a>Proteja Novamente do Azure para o local

Depois de [failover](site-recovery-failover.md) de máquinas virtuais do VMware no local ou servidores físicos para o Azure, a primeira etapa com falha para seu site local é Proteja as VMs do Azure que foram criados durante o failover. Este artigo descreve como fazer isso. 

## <a name="before-you-begin"></a>Antes de começar

1. Siga as etapas neste [artigo](vmware-azure-prepare-failback.md) para se preparar para a nova proteção e o failback, incluindo a configuração de um servidor de processo no Azure e um servidor de destino mestre local e a configuração de uma VPN site a site ou um emparelhamento privado de ExpressRoute para failback.
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
    - As VMs do VMware não podem fazer failback para o Hyper-V.
    - VMs que foram migradas.
    - Uma VM que foi movida para outro grupo de recursos.
    - Uma VM do Azure de réplica que foi excluída.
    - Uma VM do Azure de réplica que não está protegida (replicando para o site local).
10. [Examine os tipos de failback](concepts-types-of-failback.md) você pode usar – recuperação de localização original e recuperação de localização alternativa.


## <a name="enable-reprotection"></a>Habilitar nova proteção

Habilitar a replicação. Você pode proteger novamente VMs específicas ou um plano de recuperação:

- Se você proteger novamente um plano de recuperação, deverá fornecer os valores para todos os computadores protegidos.
- Se as VMs pertencerem a um grupo de replicação para consistência de várias VMS, elas só poderão ser protegidas novamente usando um plano de recuperação. As VMs em um grupo de replicação devem usar o mesmo servidor de destino mestre

>[!NOTE]
>A quantidade de dados enviados do Azure para a origem anterior durante a nova proteção pode estar entre 0 bytes e a soma do tamanho do disco para todos os computadores protegidos e não pode ser calculada.

### <a name="before-you-start"></a>Antes de começar

- Depois que uma VM é inicializada no Azure após o failover, leva algum tempo para que o agente seja registrado no servidor de configuração (até 15 minutos). Durante esse tempo, não será capaz de proteger novamente e uma mensagem de erro indica que o agente não está instalado. Se isso acontecer, aguarde alguns minutos e proteja novamente.
- Se você quiser realizar o failback da VM do Azure para uma VM local existente, monte os repositórios de armazenamento de VM locais com acesso de leitura/gravação no host ESXi do servidor de destino mestre.
- Se você quiser realizar o failback para um local alternativo, por exemplo, se a VM local não existir, selecione a unidade de retenção e o repositório de armazenamento que estão configurados para o servidor de destino mestre. Quando você realiza o failback para o site local, as máquinas virtuais do VMware no plano de proteção de failback usam o mesmo repositório de dados que o servidor de destino mestre. Uma VM é criada no vCenter.

Habilite a nova proteção da seguinte maneira:

1. Selecione **cofre** > **replicadas itens**. A máquina virtual que falhou e, em seguida, selecione **proteger novamente**. Ou, os botões de comando, selecione a máquina e, em seguida, selecione **proteger novamente**.
2. Verifique o **do Azure para local** direção de proteção está selecionada.
3. Em **Servidor de Destino Mestre** e **Servidor de Processo**, selecione o servidor de destino mestre local e o servidor de processo.  
4. Para **Repositório de Dados**, selecione o repositório de dados no qual você deseja recuperar os discos localmente. Essa opção é usada quando a máquina virtual no local é excluída e você precisa criar novos discos. Essa opção será ignorada se os discos já existem. Você ainda precisa especificar um valor.
5. Selecione a unidade de retenção.
6. A politica de failback é selecionada automaticamente.
7. Selecione **Okey** para iniciar a nova proteção.

    ![Proteja a caixa de diálogo](./media/vmware-azure-reprotect/reprotectinputs.png)
    
8. Um trabalho começa a replicar a VM do Azure para o site local. Você pode acompanhar o andamento na guia **Trabalhos**.
    - Quando a nova proteção é bem-sucedida, a VM entra em um estado protegido.
    - A VM local é desativada durante o processo de nova proteção. Isso ajuda a garantir a consistência de dados durante a replicação.
    - Não ative a VM local após a conclusão da nova proteção.
   

## <a name="next-steps"></a>Próximas etapas

- Se você encontrar problemas, examine o [artigo de solução de problemas](vmware-azure-troubleshoot-failback-reprotect.md).
- Depois que as VMs do Azure estiverem protegidas, você poderá [executar um failback](vmware-azure-failback.md). O failback desliga a VM do Azure e inicializa a VM local. Espere algum tempo de inatividade para o aplicativo e escolha um horário de failback de acordo.


