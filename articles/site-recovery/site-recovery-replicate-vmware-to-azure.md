---
title: Replicar aplicativos (VMware para o Azure) | Microsoft Docs
description: "Este artigo descreve como configurar a replicação de máquinas virtuais que são executadas no VMware dentro do Azure."
services: site-recovery
documentationcenter: 
author: asgang
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/29/2017
ms.author: asgang
ms.openlocfilehash: a0d146081b552ee181fdf93fb60790c27f108888
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="replicate-applications-running-on-vmware-vms-to-azure"></a>Replicar aplicativos em execução no VMware VMs da Azure



Este artigo descreve como configurar a replicação de máquinas virtuais que são executadas no VMware dentro do Azure.
## <a name="prerequisites"></a>Pré-requisitos

O artigo supõe que você já tenha

1.  [Configurar o ambiente de origem local](site-recovery-set-up-vmware-to-azure.md)
2.  [Configurar o ambiente de destino no Azure](site-recovery-prepare-target-vmware-to-azure.md)


## <a name="enable-replication"></a>Habilitar a replicação
#### <a name="before-you-start"></a>Antes de começar
Ao replicar máquinas virtuais VMware, observe o seguinte:

* Sua conta de usuário do Azure precisa ter certas [permissões](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) para habilitar a replicação de uma nova máquina virtual para o Azure.
* VMs VMware são descobertas a cada 15 minutos. Pode levar 15 minutos ou mais para que elas sejam exibidas no portal após a descoberta. Da mesma forma, a descoberta pode levar 15 minutos ou mais quando você adiciona um novo servidor vCenter ou host vSphere.
* As alterações de ambiente na máquina virtual (como instalação de ferramentas VMware) podem levar 15 minutos ou mais para serem atualizadas no portal.
* Você pode verificar a hora da última descoberta das VMs VMware no campo **Último Contato Às** do servidor vCenter/host vSphere, na página **Servidores de Configuração**.
* Para adicionar computadores para replicação sem precisar esperar pela descoberta agendada, realce o servidor de configuração (não clique nele) e clique no botão **Atualizar**.
* Quando você habilitar a replicação, se o computador estiver preparado, o servidor em processo instalará automaticamente o serviço de Mobilidade nele.


**Agora habilite a replicação da seguinte maneira**:

1. Clique em **Etapa 2: replicar aplicativo** > **Origem**. Depois de habilitar a replicação pela primeira vez, clique em **+Replicar** no cofre para habilitar a replicação para outros computadores.
2. Na página **Origem** > **Origem**, selecione o servidor de configuração.
3. Em **Tipo de computador**, selecione **Máquinas Virtuais** ou **Computadores Físicos**.
4. Em **Hipervisor do vCenter/vSphere**, selecione o servidor vCenter que gerencia o host vSphere ou selecione o host. Essa configuração não será relevante se você estiver replicando computadores físicos.
5. Selecione o servidor de processo. Se você não criou nenhum servidor de processo adicional, esse será o nome do servidor de configuração. Em seguida, clique em **OK**.

    ![Habilitar a replicação](./media/site-recovery-vmware-to-azure/enable-replication2.png)

6. Em **Destino**, selecione a assinatura e o grupo de recursos no qual você deseja criar as máquinas virtuais do failover. Escolha o modelo de implantação que você deseja usar no Azure (clássico ou gerenciamento de recursos) para as máquinas virtuais do failover.
7. Selecione a conta de armazenamento do Azure que você deseja usar para replicar os dados. Observe que:

   * Você pode selecionar uma conta de armazenamento padrão ou premium. Se você selecionar uma conta premium, precisará especificar uma conta de armazenamento standard adicional para logs de replicação contínuos. As contas devem estar na mesma região que o cofre dos Serviços de Recuperação.
   * Se você deseja usar uma conta de armazenamento diferente daquela que você tem, você pode criar um*link de espaço reservado para criar uma conta de armazenamento usando o Gerenciador de Recursos, o que será abordado na introdução*. Para criar uma conta de armazenamento usando o Resource Manager, clique em **Criar nova**. Se você quiser criar uma conta de armazenamento usando o modelo clássico, terá de fazer isso no [portal do Azure](../storage/common/storage-create-storage-account.md).

8. Selecione a rede e a sub-rede do Azure às quais as VMs do Azure se conectarão quando forem iniciadas após o failover. A rede deve estar na mesma região do que o cofre de Recuperação de Site. Selecione **Configurar agora para computadores selecionados** para aplicar a configuração de rede a todos os computadores selecionados para proteção. Selecione **Configurar mais tarde** para selecionar a rede do Azure por computador. Se você não tiver uma rede, precisará [criar uma](#set-up-an-azure-network). Para criar uma rede usando o Resource Manager, clique em **Criar nova**. Se você quiser criar uma rede usando o modelo clássico, terá de fazer isso [no portal do Azure](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). Selecione uma sub-rede, se aplicável. Em seguida, clique em **OK**.

    ![Habilitar a replicação](./media/site-recovery-vmware-to-azure/enable-rep3.png)
9. Em **Máquinas Virtuais** > **Selecionar máquinas virtuais**, clique e selecione cada máquina que você deseja replicar. Você só pode selecionar computadores para os quais a replicação pode ser habilitada. Em seguida, clique em **OK**.

    ![Habilitar a replicação](./media/site-recovery-vmware-to-azure/enable-replication5.png)
10. Em **Propriedades** > **Configurar propriedades**, selecione a conta que será usada pelo servidor de processo para instalar automaticamente o serviço de Mobilidade no computador.  
11. Por padrão, todos os discos são replicados. Para excluir discos da replicação, clique em **Todos os Discos** e desmarque quaisquer discos que você não deseje replicar.  Em seguida, clique em **OK**. Você pode definir propriedades adicionais posteriormente. [Saiba mais](site-recovery-exclude-disk.md) sobre a exclusão de discos.

    ![Habilitar a replicação](./media/site-recovery-vmware-to-azure/enable-replication6.png)

12. Em **Configurações de replicação** > **Definir configurações de replicação**, verifique se a política de replicação correta está selecionada. Você pode modificar as configurações da política de replicação em **Configurações** > **Políticas de replicação** > nome da política > **Editar Configurações**. Alterações aplicadas a uma política serão aplicadas a computadores novos e de replicação.
13. Habilite **Consistência de várias VMs** se você quiser reunir computadores em um grupo de replicação e especifique um nome para o grupo. Em seguida, clique em **OK**. Observe que:

    * Os computadores no grupo de replicação são replicados em conjunto e possuem pontos de recuperação consistentes compartilhados com o aplicativo e com falhas quando executam failover.
    * É recomendável que você colete VMs e servidores físicos para que espelhem suas cargas de trabalho. Habilitar a consistência de várias VMs pode afetar o desempenho da carga de trabalho e só deve ser usada se os computadores estão executando a mesma carga de trabalho e precisam de consistência.

    ![Habilitar a replicação](./media/site-recovery-vmware-to-azure/enable-replication7.png)
14. Clique em **Habilitar a Replicação**. Você pode acompanhar o progresso do trabalho **Habilitar Proteção** em **Configurações** > **Trabalhos** > **Trabalhos de Recuperação de Site**. Após o trabalho de **Finalizar Proteção** ser executado, o computador estará pronto para failover.

> [!NOTE]
> Se o computador estiver preparado para a instalação por push, o componente de serviço de Mobilidade será instalado quando a proteção estiver habilitada. Depois do componente estiver instalado no computador, um trabalho de proteção é iniciado e falha. Apos a falha, você precisa reiniciar manualmente cada computador. Após a reinicialização, o trabalho de proteção começa novamente e a replicação inicial ocorrerá.
>
>

## <a name="view-and-manage-vm-properties"></a>Exibir e gerenciar as propriedades da VM

É recomendável que você verifique as propriedades do computador de origem. Lembre-se de que o nome da VM do Azure deve estar em conformidade com os [Requisitos de máquina virtual do Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).

1. Clique em **Configurações** > **Itens replicados** > e selecione o computador. A página **Informações gerais** mostra as informações sobre as configurações e o status dos computadores.
2. Em **Propriedades**, você pode exibir informações de replicação e de failover para a VM.
3. Em **Computação e Rede** > **Propriedades de computação**, você pode especificar o nome da VM do Azure e o tamanho de destino. Modifique o nome para que ele fique em conformidade com os requisitos do Azure, se for necessário.
    ![Habilitar a replicação](./media/site-recovery-vmware-to-azure/vmproperties.png)

4.  É possível selecionar um [grupo de recursos](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-resource-groups-guidelines) do qual o computador fará parte após o failover. Você pode alterar essa configuração a qualquer momento antes do failover. Após o failover, se você migrar o computador para outro grupo de recursos, as configurações de proteção de um computador serão interrompidas.
5. É possível selecionar um [conjunto de disponibilidade](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines) se o seu computador precisar fazer parte de um pós-failover. Durante a seleção de um conjunto de disponibilidade, tenha em mente que:

    * Apenas os conjuntos de disponibilidade que pertencem ao grupo de recursos especificado serão listados  
    * Computadores com redes virtuais diferentes não podem fazer parte do mesmo conjunto de disponibilidade
    * Somente máquinas virtuais do mesmo tamanho podem fazer parte de um mesmo conjunto de disponibilidade
5. Você também pode exibir e adicionar as informações sobre a rede de destino, a sub-rede e o endereço IP que será atribuído à VM do Azure.
6. Em **Discos**, é possível ver o sistema operacional e os discos de dados na VM que serão replicados.

### <a name="network-adapters-and-ip-addressing"></a>Adaptadores de rede e endereçamento IP

- Você pode definir o endereço IP de destino. Se você não fornecer um endereço, o computador com failover usará o DHCP. Se você definir um endereço que não esteja disponível no failover, o failover não funcionará. O mesmo endereço IP de destino poderá ser usado para failover de teste caso o endereço esteja disponível na rede de failover de teste.
- O número de adaptadores de rede é determinado pelo tamanho especificado para a máquina virtual de destino, como a seguir:
    - Se o número de adaptadores de rede na máquina de origem for menor ou igual ao número de adaptadores permitido para o tamanho da máquina de destino, o destino terá o mesmo número de adaptadores que a origem.
    - Se o número de adaptadores para máquina virtual de origem exceder o número permitido para o tamanho de destino e o tamanho máximo de destino será usado.
    - Por exemplo, se uma máquina de origem tiver dois adaptadores de rede e o tamanho da máquina de destino oferecer suporte a quatro, a máquina de destino terá dois adaptadores. Se a máquina de origem tiver dois adaptadores, mas o tamanho de destino com suporte oferecer suporte apenas a uma máquina de destino, ela terá apenas um adaptador.
    - Se a máquina virtual tiver vários adaptadores de rede, todos eles se conectarão à mesma rede.
    - Se a máquina virtual tiver vários adaptadores de rede, o primeiro deles mostrado na lista se tornará o adaptador de rede *Padrão* na máquina virtual do Azure.

### <a name="azure-hybrid-use-benefit"></a>Benefício de Uso do Azure Híbrido

Clientes do Microsoft Software Assurance podem aproveitar o Benefício de Uso do Azure híbrido para economizar nos custos de licenciamento para máquinas do Windows Server que são migradas para o Azure, ou que usam o Azure para recuperação de desastres. Se você possui o direito de usar o Benefício de Uso Híbrido do Azure, você pode especificar que a máquina virtual que o Azure Site Recovery cria no Azure, no caso de um failover, seja atribuída a esse benefício. Para fazer isso,
- Vá para a seção de propriedades de Computação e Rede da máquina virtual replicada.
- Responda à pergunta que pergunta se você tem uma licença do Windows Server que faz com que você tenha direito ao Benefício de Uso Híbrido do Azure.
- Marque a caixa de seleção para confirmar que você tem uma licença do Windows Server com o Software Assurance que você pode usar para aplicar o Benefício de Uso Híbrido no computador que será criado durante o failover.
- Salve as configurações para o computador replicado.

Saiba mais sobre o [Benefício de Uso Híbrido do Azure.](https://aka.ms/azure-hybrid-use-benefit-pricing)

## <a name="common-issues"></a>Problemas comuns

* Cada disco deve ter menos de 1 TB de tamanho.
* O disco do sistema operacional deve ser um disco básico e não dinâmico
* Para máquinas virtuais habilitadas pela geração 2/UEFI, a família do sistema operacional deve ser o Windows e o disco de inicialização deve ser menor que 300 GB

## <a name="next-steps"></a>Próximas etapas

Quando a proteção for concluída e a máquina estiver protegida, você pode tentar um [failover](site-recovery-failover.md) para verificar se o seu aplicativo é mostrado no Azure ou não.

Caso você deseje desabilitar a proteção, confira como [limpar as configurações de registro e de proteção](site-recovery-manage-registration-and-protection.md)
