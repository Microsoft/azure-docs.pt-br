---
title: Habilitar a replicação de VMs VMware para recuperação de desastres no Azure com o Azure Site Recovery | Microsoft Docs
description: Este artigo descreve como habilitar a VMs do VMware para replicação no Azure para recuperação de desastres usando o Azure Site Recovery.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.date: 06/28/2019
ms.topic: conceptual
ms.author: ramamill
ms.openlocfilehash: 3f4e4afb4d94a7b2e2a6b246a371cf6234577463
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491730"
---
# <a name="enable-replication-to-azure-for-vmware-vms"></a>Habilitar a replicação no Azure de VMs VMware

Este artigo descreve como habilitar replicação de VMs VMware locais no Azure.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo supõe que você:

- [Configurar seu ambiente de origem local](vmware-azure-set-up-source.md).
- [Configurar seu ambiente de destino no Azure](vmware-azure-set-up-target.md).

## <a name="before-you-start"></a>Antes de começar
Quando você estiver replicando máquinas virtuais VMware, tenha essas informações em mente:

* Sua conta de usuário do Azure precisa ter certas [permissões](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) para habilitar a replicação de uma nova máquina virtual para o Azure.
* VMs VMware são descobertas a cada 15 minutos. Pode levar 15 minutos ou mais para máquinas virtuais aparecer no portal do Azure após a descoberta. Da mesma forma, a descoberta pode levar 15 minutos ou mais quando você adiciona um novo vCenter server ou host vSphere.
* Pode levar 15 minutos ou mais para que as alterações de ambiente na máquina virtual (como instalação de ferramentas VMware) a ser atualizado no portal.
* Você pode verificar a hora da última descoberta das VMs VMware: Consulte a **último contato às** campo o **servidores de configuração** página para o servidor vCenter/host vSphere.
* Para adicionar máquinas virtuais para replicação sem precisar esperar pela descoberta agendada, realce o servidor de configuração (mas não clique nele) e selecione **Refresh**.
* Quando você habilitar a replicação, se a máquina virtual estiver preparada, o servidor de processo instalará automaticamente o serviço de mobilidade do Azure Site Recovery nele.

## <a name="enable-replication"></a>Habilitar a replicação

Antes de seguir as etapas nesta seção, observe as seguintes informações:
* O Azure Site Recovery replica agora diretamente em discos gerenciados para todas as replicações de novo. O servidor de processo grava os logs de replicação para uma conta de armazenamento de cache na região de destino. Esses logs são usados para criar pontos de recuperação em discos gerenciados de réplica que tem a convenção de nomenclatura de asrseeddisk.
* Suporte do PowerShell para replicar para o managed disks está disponível no [Az.RecoveryServices módulo versão 2.0.0 e posteriores](https://www.powershellgallery.com/packages/Az.RecoveryServices/2.0.0-preview) 
* No momento do failover, o ponto de recuperação selecionado é usado para criar o disco gerenciado de destino.
* As VMs que foram previamente configuradas para replicar para contas de armazenamento de destino não são afetadas.
* A replicação para contas de armazenamento para uma nova máquina virtual só está disponível por meio de uma transferência REST (Representational State) API e Powershell. Use a API de REST do Azure versão 2016-08-10 ou 2018-01-10 para a replicação para contas de armazenamento.

Siga as etapas abaixo para habilitar a replicação:
1. Vá para **etapa 2: Replicar aplicativo** > **Origem**. Depois de habilitar a replicação pela primeira vez, selecione **+ replicar** no cofre para habilitar a replicação para máquinas virtuais adicionais.
2. Na página **Origem** > **Origem**, selecione o servidor de configuração.
3. Para **tipo de computador**, selecione **máquinas virtuais** ou **máquinas físicas**.
4. Em **Hipervisor do vCenter/vSphere**, selecione o servidor vCenter que gerencia o host vSphere ou selecione o host. Essa configuração não é relevante se você estiver replicando computadores físicos.
5. Selecione o servidor de processo. Se não houver nenhum servidor de processo adicional criado, o servidor incorporados ao processo do servidor de configuração estará disponível na lista suspensa. O status de integridade de cada servidor de processo é indicado de acordo com os limites recomendados e outros parâmetros. Escolha um servidor de processo íntegro. Um servidor de processo [crítico](vmware-physical-azure-monitor-process-server.md#process-server-alerts) não pode ser escolhido. Você pode [solucionar problemas e resolver](vmware-physical-azure-troubleshoot-process-server.md) os erros **ou** configurar um [servidor de processo de expansão](vmware-azure-set-up-process-server-scale.md).
    ![Habilitar janela de origem de replicação](media/vmware-azure-enable-replication/ps-selection.png)

> [!NOTE]
> Partir [9.24 versões](service-updates-how-to.md#links-to-currently-supported-update-rollups), alertas adicionais são introduzidas para aprimorar os alertas de integridade do servidor de processo. Atualize os componentes do Site Recovery para 9.24 versões ou superior para todos os alertas sejam gerados.

6. Para **destino**, selecione o grupo de recursos e assinatura em que você deseja criar as máquinas virtuais de failover. Escolha o modelo de implantação que você deseja usar no Azure para as VMs de failover.
2. Selecione a rede do Azure e a sub-rede que irão se conectar a VMs do Azure após o failover. A rede deve estar na mesma região que o cofre do serviço de recuperação de Site.

   Selecione **configurar agora para computadores selecionados** para aplicar a configuração de rede para todas as máquinas virtuais que você selecionou para proteção. Selecione **configurar mais tarde** para selecionar a rede do Azure por máquina virtual. Se você não tiver uma rede, precisará criar uma. Para criar uma rede usando o Azure Resource Manager, selecione **criar novo**. Selecione uma sub-rede, se aplicável e, em seguida, selecione **Okey**.
   
   ![Habilitar janela de destino de replicação](./media/vmware-azure-enable-replication/enable-rep3.png)

1. Para **máquinas virtuais** > **selecionar máquinas virtuais**, selecione cada máquina virtual que você deseja replicar. Você só pode selecionar máquinas virtuais para os quais a replicação pode ser habilitada. Depois, selecione **OK**. Se você não pode ver ou seleciona qualquer máquina virtual específica, consulte [computador de origem não está listado no portal do Azure](https://aka.ms/doc-plugin-VM-not-showing) para resolver o problema.

    ![Habilitar janela Selecione as máquinas virtuais de replicação](./media/vmware-azure-enable-replication/enable-replication5.png)

1. Para **propriedades** > **configurar propriedades**, selecione a conta usada pelo servidor de processo para instalar automaticamente o serviço de mobilidade do Site Recovery na máquina virtual. Além disso, escolha o tipo de disco gerenciado de destino para replicar para padrões de variação com base em seus dados.
10. Por padrão, todos os discos de uma máquina virtual de origem são replicados. Para excluir discos da replicação, desmarque a **Include** caixa de seleção para todos os discos que você não deseja replicar. Depois, selecione **OK**. Você pode definir propriedades adicionais posteriormente. Saiba mais sobre [exclusão de discos](vmware-azure-exclude-disk.md).

    ![Habilitar a replicação configurar a janela Propriedades](./media/vmware-azure-enable-replication/enable-replication6.png)

1. No **as configurações de replicação** > **definir configurações de replicação**, verifique se que a política de replicação correta está selecionada. Você pode modificar as configurações de política de replicação no **as configurações** > **políticas de replicação** > ***nome da política***  >  **Editar configurações**. As alterações aplicadas a uma política também se aplicam a novos e replicadas máquinas virtuais.
1. Habilitar **consistência de várias VMs** se você quiser reunir as máquinas virtuais em um grupo de replicação. Especifique um nome para o grupo e, em seguida, selecione **Okey**.

    > [!NOTE]
    >    * Máquinas virtuais em um grupo de replicação replicar juntos e compartilhou pontos de recuperação consistentes com o aplicativo e com falhas quando executam failover.
    >    * Colete VMs e servidores físicos para que espelhem suas cargas de trabalho. Habilitar a consistência de várias VMs pode afetar o desempenho da carga de trabalho. Faça isso apenas se as máquinas virtuais estão executando a mesma carga de trabalho e precisam de consistência.

    ![Habilitar janela de replicação](./media/vmware-azure-enable-replication/enable-replication7.png)
    
1. Selecione **Habilitar Replicação**. Você pode acompanhar o progresso do **Habilitar proteção** de trabalho no **configurações** > **trabalhos** > **trabalhos de recuperação de Site**. Após a execução do trabalho **Finalizar Proteção**, a máquina virtual estará pronta para failover.

## <a name="view-and-manage-vm-properties"></a>Exibir e gerenciar as propriedades da VM

Em seguida, verifique se as propriedades da máquina virtual de origem. Lembre-se de que o nome da VM do Azure precisa estar em conformidade com os [Requisitos de máquina virtual do Azure](vmware-physical-azure-support-matrix.md#replicated-machines).

1. Vá para **as configurações** > **itens replicados**e, em seguida, selecione a máquina virtual. O **Essentials** página mostra informações sobre as configurações e o status da VM.
1. Em **Propriedades**, você pode exibir informações de replicação e de failover para a VM.
1. Na **computação e rede** > **propriedades de computação**, você pode alterar várias propriedades da VM. 

    ![Janela de propriedades de computação e rede](./media/vmware-azure-enable-replication/vmproperties.png)

    * Nome da VM do Azure: Modifique o nome para atender aos requisitos do Azure, se necessário.
    * Tamanho da VM de destino ou o tipo de VM: O tamanho da VM padrão é escolhido com base em alguns parâmetros que incluem a contagem de discos, contagem de NIC, contagem de núcleos CPU, memória e tamanhos de função VM disponíveis na região do Azure de destino. O Azure Site Recovery escolhe o primeiro tamanho VM disponível que atende a todos os critérios. Você pode selecionar um tamanho VM diferente com base em suas necessidades, a qualquer momento antes do failover. Observe que o tamanho do disco VM também se baseia no tamanho do disco de origem, e ele só pode ser alterado após o failover. Saiba mais sobre tamanhos de disco e taxas de IOPS [metas de desempenho e escalabilidade para discos VM no Windows](../virtual-machines/windows/disk-scalability-targets.md).

    *  Grupo de recursos: Você pode selecionar um [grupo de recursos](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-resource-groups-guidelines), do qual uma máquina virtual se torna parte de um failover de postagem. Você pode alterar essa configuração a qualquer momento antes do failover. Após o failover, se você migrar a máquina virtual para outro grupo de recursos, interromper as configurações de proteção para a máquina virtual.
    * Conjunto de disponibilidade: Você pode selecionar um [conjunto de disponibilidade](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines) se sua máquina virtual precisa ser uma parte de um failover de postagem. Quando você seleciona um conjunto de disponibilidade, lembre-se as informações a seguir:

        * São listados apenas conjuntos de disponibilidade que pertencem ao grupo de recursos especificado.  
        * As VMs que estão em redes virtuais diferentes não podem ser uma parte do mesmo conjunto de disponibilidade.
        * Somente máquinas virtuais do mesmo tamanho podem fazer parte de um mesmo conjunto de disponibilidade.
1. Você também pode adicionar informações sobre a rede de destino, a sub-rede e o endereço IP que é atribuído à VM do Azure.
2. Em **Discos**, é possível ver o sistema operacional e os discos de dados na VM que serão replicados.

### <a name="configure-networks-and-ip-addresses"></a>Configurar redes e endereços IP

Você pode definir o endereço IP de destino. Se você não fornecer um endereço, a máquina virtual de failover usa o DHCP. Se você definir um endereço que não esteja disponível no failover, o failover não funcionará. Se o endereço está disponível na rede de failover de teste, você pode usar o mesmo endereço IP de destino para failover de teste.

O número de adaptadores de rede é determinado pelo tamanho que você especificar para a máquina virtual de destino, da seguinte maneira:

- Se o número de adaptadores de rede na máquina virtual de origem for menor ou igual ao número de adaptadores que são permitidos para o tamanho da VM de destino, o destino tem o mesmo número de adaptadores de como a origem.
- Se o número de adaptadores para máquina virtual de origem exceder o número permitido para o tamanho da VM de destino, o tamanho máximo de destino será usado. Por exemplo, se uma máquina virtual de origem tiver dois adaptadores de rede e o tamanho da VM de destino der suporte a quatro, a máquina virtual de destino tem dois adaptadores. Se a VM de origem tiver dois adaptadores, mas o tamanho de destino dá suporte apenas a um, a VM de destino terá apenas um adaptador.
- Se a máquina virtual tiver vários adaptadores de rede, todos eles se conectarão à mesma rede. Além disso, o primeiro adaptador que é mostrado na lista se tornará o *padrão* adaptador de rede na máquina virtual do Azure. 

### <a name="azure-hybrid-benefit"></a>Benefício Híbrido do Azure

Clientes do Microsoft Software Assurance podem usar o benefício híbrido do Azure para economizar nos custos de licenciamento para computadores com Windows Server que são migradas para o Azure. O benefício também se aplica a recuperação de desastre do Azure. Se você está qualificado, você pode atribuir o benefício para a máquina virtual que o Site Recovery cria se houver um failover. Para fazer isso, siga estas etapas:
1. Vá para o **propriedades de rede e computador** da máquina virtual replicada.
2. Responder quando perguntado se você tem uma licença do Windows Server que faz com que você qualificados do benefício híbrido do Azure.
3. Confirme se você tem uma licença do Windows Server com Software Assurance que você pode usar para aplicar o benefício à VM que será criada no failover.
4. Salve as configurações para a máquina virtual replicada.

Saiba mais sobre o [Benefício Híbrido do Azure](https://aka.ms/azure-hybrid-benefit-pricing).

## <a name="resolve-common-issues"></a>Resolver problemas comuns

* Cada disco deve ser menor que 4 TB.
* O disco do sistema operacional deve ser um disco básico, não um disco dinâmico.
* Para a geração 2/UEFI-máquinas virtuais habilitadas para, a família do sistema operacional deve ser o Windows e o disco de inicialização deve ser menor que 300 GB.

## <a name="next-steps"></a>Próximas etapas

Depois que a máquina virtual atingir um estado protegido, tente uma [failover](site-recovery-failover.md) para verificar se seu aplicativo aparece no Azure.

* Saiba como [limpar as configurações de registro e proteção](site-recovery-manage-registration-and-protection.md) para desabilitar a replicação.
* Saiba como [automatizar a replicação para as máquinas virtuais usando o Powershell](vmware-azure-disaster-recovery-powershell.md).
