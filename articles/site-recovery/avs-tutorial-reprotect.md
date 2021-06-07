---
title: Proteger novamente as VMs do Azure para uma nuvem privada da Solução VMware no Azure com Azure Site Recovery
description: Saiba como proteger novamente as VMs da Solução VMware no Azure após o failover para o Azure com o Azure Site Recovery.
author: Harsha-CS
manager: rochakm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 09/30/2020
ms.author: harshacs
ms.custom: MVC
ms.openlocfilehash: 80ff2f3f3d5fdcf61770889dcdaaf075941b90ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91814164"
---
# <a name="reprotect-from-azure-to-azure-vmware-solution-private-cloud"></a>Proteger novamente do Azure para a nuvem privada da Solução VMware no Azure

Depois de [failover](avs-tutorial-failover.md) de VMs da Solução VMware no Azure para o Azure, a primeira etapa ao fazer failback para sua nuvem privada da Solução VMware no Azure é proteger novamente as VMs do Azure que foram criadas durante o failover. Este artigo descreve como fazer isso. 

## <a name="before-you-begin"></a>Antes de começar

1. Siga as etapas [neste artigo](vmware-azure-prepare-failback.md) para se preparar para a nova proteção e o failback, incluindo a configuração de um servidor de processo no Azure e um servidor de destino mestre de nuvem privada da Solução VMware no Azure e a configuração de uma VPN site a site ou um emparelhamento privado do ExpressRoute para failback.
2. Verifique se o servidor de configuração de nuvem privada da Solução VMware no Azure está em execução e conectado ao Azure. Durante o failback, a VM deve existir no banco de dados do servidor de configuração. Caso contrário, o failback será malsucedido.
3. Exclua todos os instantâneos no servidor de destino mestre de nuvem privada da Solução VMware no Azure. A nova proteção não funcionará se houver instantâneos.  Os instantâneos na VM são mesclados automaticamente durante um trabalho de nova proteção.
4. Se você estiver protegendo novamente as VMs coletadas em um grupo de replicação para consistência de várias VMS, verifique se todas elas têm o mesmo sistema operacional (Windows ou Linux) e se o servidor de destino mestre implantado tem o mesmo tipo de sistema operacional. Todas as VMs em um grupo de replicação devem usar o mesmo servidor de destino mestre.
5. Abra [as portas necessárias](vmware-azure-prepare-failback.md#ports-for-reprotectionfailback) para failback.
6. Verifique se o vCenter Server está conectado antes do failback. Caso contrário, desconectando discos e anexá-los de volta para a máquina virtual falha.
7. Se um vCenter Server gerenciar as VMs para as quais você fará failback, verifique se você tem as permissões necessárias. Se você executar a descoberta de vCenter de Usuário somente leitura e proteger as máquinas virtuais, a proteção terá êxito e o failover funcionará. No entanto, durante a nova proteção, o failover falha porque os repositórios de armazenamento não podem ser descobertos e não são listados durante a nova proteção. Para resolver esse problema, você pode atualizar as credenciais do vCenter com uma [conta/permissões apropriadas](avs-tutorial-prepare-avs.md#prepare-an-account-for-automatic-discovery) e, depois, repita o trabalho. 
8. Se você usou um modelo para criar suas máquinas virtuais, verifique se cada VM tem os próprios UUIDs para os discos. Se o UUID da VM da Solução VMware no Azure estiver em conflito com o UUID do servidor de destino mestre porque ambos foram criados com base no mesmo modelo, a nova proteção falhará. Implantar de um modelo diferente.
9. Se você estiver fazendo failback para um vCenter Server alternativo, o novo vCenter Server e o servidor de destino mestre deverão ser descobertos. Normalmente, se não forem, os armazenamentos de dados não estarão acessíveis ou não estarão visíveis em **Proteger Novamente**.
10. Verifique os seguintes cenários em que você não pode realizar failback:
    - Se você estiver usando a edição gratuita ESXi 5.5 ou a edição gratuita do hipervisor vSphere 6. Atualize para uma versão diferente.
    - Se você tem um servidor físico do Windows Server 2008 R2 SP1.
    - As VMs do VMware não podem fazer failback para o Hyper-V.
    - VMs que foram migradas.
    - Uma VM que foi movida para outro grupo de recursos.
    - Uma VM do Azure de réplica que foi excluída.
    - Uma VM do Azure de réplica que não está protegida.
10. [Examine os tipos de failback](concepts-types-of-failback.md) você pode usar – recuperação de localização original e recuperação de localização alternativa.


## <a name="enable-reprotection"></a>Habilitar nova proteção

Habilitar a replicação. Você pode proteger novamente VMs específicas ou um plano de recuperação:

- Se você proteger novamente um plano de recuperação, deverá fornecer os valores para todos os computadores protegidos.
- Se as VMs pertencerem a um grupo de replicação para consistência de várias VMS, elas só poderão ser protegidas novamente usando um plano de recuperação. As VMs em um grupo de replicação devem usar o mesmo servidor de destino mestre

>[!NOTE]
>A quantidade de dados enviados do Azure para a origem anterior durante a nova proteção pode estar entre 0 bytes e a soma do tamanho do disco para todos os computadores protegidos e não pode ser calculada.

### <a name="before-you-start"></a>Antes de começar

- Depois que uma VM é inicializada no Azure após o failover, leva algum tempo para que o agente seja registrado no servidor de configuração (até 15 minutos). Durante esse tempo, não será capaz de proteger novamente e uma mensagem de erro indica que o agente não está instalado. Se isso acontecer, aguarde alguns minutos e proteja novamente.
- Se você quiser fazer failback da VM do Azure para uma VM de Solução VMware no Azure, monte os armazenamentos de dados da VM com acesso de leitura/gravação no host ESXi do servidor de destino mestre.
- Se você quiser realizar o failback para uma localização alternativa, por exemplo, se a VM da Solução VMware no Azure não existir, selecione a unidade de retenção e o armazenamento de dados que estão configurados para o servidor de destino mestre. Quando você realiza o failback para a nuvem privada da Solução VMware no Azure, as máquinas virtuais do VMware no plano de proteção de failback usam o mesmo armazenamento de dados que o servidor de destino mestre. Uma VM é criada no vCenter.

Habilite a nova proteção da seguinte maneira:

1. Selecione **cofre** > **replicadas itens**. A máquina virtual que falhou e, em seguida, selecione **proteger novamente**. Ou, os botões de comando, selecione a máquina e, em seguida, selecione **proteger novamente**.
2. Verifique o **do Azure para local** direção de proteção está selecionada.
3. Em **Servidor de Destino Mestre** e **Servidor de Processo**, selecione o servidor de destino mestre local e o servidor de processo.  
4. Para **Armazenamento de Dados**, selecione o armazenamento de dados no qual você deseja recuperar os discos na Solução VMware no Azure. Essa opção é usada quando a VM da Solução VMware no Azure é excluída e você precisa criar discos. Essa opção será ignorada se os discos já existem. Você ainda precisa especificar um valor.
5. Selecione a unidade de retenção.
6. A politica de failback é selecionada automaticamente.
7. Selecione **Okey** para iniciar a nova proteção.

    ![Proteja a caixa de diálogo](./media/vmware-azure-reprotect/reprotectinputs.png)
    
8. Um trabalho começa a replicar a VM do Azure para a nuvem privada da Solução VMware no Azure. Você pode acompanhar o andamento na guia **Trabalhos**.
    - Quando a nova proteção é bem-sucedida, a VM entra em um estado protegido.
    - A VM da Solução VMware no Azure é desativada durante a nova proteção. Isso ajuda a garantir a consistência de dados durante a replicação.
    - Não ative a VM da Solução VMware no Azure após a conclusão da nova proteção.
   

## <a name="next-steps"></a>Próximas etapas

- Se você encontrar problemas, examine o [artigo de solução de problemas](vmware-azure-troubleshoot-failback-reprotect.md).
- Depois que as VMs do Azure estiverem protegidas, você poderá [executar um failback](avs-tutorial-failback.md). O failback desliga a VM do Azure e inicializa a VM da Solução VMware no Azure. Espere algum tempo de inatividade para o aplicativo e escolha um horário de failback de acordo.


