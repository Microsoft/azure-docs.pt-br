---
title: Preparar VMs VMware para nova proteção e failback com Azure Site Recovery
description: Preparar para failback de VMs do VMware após o failover com Azure Site Recovery
ms.topic: conceptual
ms.date: 12/24/2019
ms.openlocfilehash: 5a330f8cba31640d0116ca3d5ccab352ce5b3509
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85847730"
---
# <a name="prepare-for-reprotection-and-failback-of-vmware-vms"></a>Preparar para nova proteção e failback das VMs do VMware

Após o [failover](site-recovery-failover.md) de VMs VMware locais ou servidores físicos para o Azure, você protege novamente as VMs do Azure criadas após o failover, para que elas sejam replicadas de volta para o site local. Com a replicação do Azure para o local no local, você pode fazer failback executando um failover do Azure para o local quando estiver pronto.

Antes de continuar, obtenha uma visão geral rápida com este vídeo sobre como fazer failback do Azure para um site local.<br /><br />
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]

## <a name="reprotectionfailback-components"></a>Componentes de nova proteção/failback

Você precisa de vários componentes e configurações em vigor antes de poder proteger novamente e fazer failback do Azure.

**Componente**| **Detalhes**
--- | ---
**Servidor de configuração local** | O servidor de configuração local deve estar em execução e conectado ao Azure.<br/><br/> A VM para a qual você está realizando o failback deve existir no banco de dados do servidor de configuração. Se o desastre afetar o servidor de configuração, restaure-o com o mesmo endereço IP para garantir que o failback funcione.<br/><br/>  Se os endereços IP de máquinas replicadas foram retidos no failover, a conectividade site a site (ou conectividade de ExpressRoute) deve ser estabelecida entre máquinas de VMs do Azure e a NIC de failback do servidor de configuração. Para endereços IP retidos, o servidor de configuração precisa de dois NICs-um para a conectividade do computador de origem e outro para a conectividade de failback do Azure. Isso evita a sobreposição de intervalos de endereços de sub-rede para a origem e as VMs com failover.
**Servidor de processo no Azure** | Você precisa de um servidor de processo no Azure antes de fazer failback para o site local.<br/><br/> O servidor de processo recebe dados da VM do Azure protegida e os envia para o site local.<br/><br/> Você precisa de uma rede de baixa latência entre o servidor de processo e a VM protegida, portanto, recomendamos que você implante o servidor de processo no Azure para obter um desempenho de replicação superior.<br/><br/> Para prova de conceito, você pode usar o servidor de processo local e o ExpressRoute com emparelhamento privado.<br/><br/> O servidor de processo deve estar na rede do Azure na qual a VM com failover está localizada. O servidor de processo também deve ser capaz de se comunicar com o servidor de configuração local e com o servidor de destino mestre.
**Servidor de destino mestre separado** | O servidor de destino Mestre recebe dados de failback e, por padrão, um servidor de destino mestre do Windows é executado no servidor de configuração local.<br/><br/> Um servidor de destino mestre pode ter até 60 discos anexados a ele. As VMs com failback têm mais do que um total coletivo de 60 discos, ou se você estiver realizando o failback de grandes volumes de tráfego, crie um servidor de destino mestre separado para realizar o failover.<br/><br/> Se os computadores forem coletados em um grupo de replicação para consistência de várias VMS, as VMs deverão ser todas Windows ou devem ser Linux. Por quê? Como todas as VMs em um grupo de replicação devem usar o mesmo servidor de destino mestre e o servidor de destino mestre deve ter o mesmo sistema operacional (com a mesma ou uma versão superior) do que aquelas dos computadores replicados.<br/><br/> O servidor de destino mestre não deve ter nenhum instantâneo em seus discos, caso contrário, a nova proteção e o failback não funcionarão.<br/><br/> O destino mestre não pode ter um controlador SCSI Paravirtual. O controlador só pode ser um controlador LSI Logic. Sem um controlador de Lógica LSI, a nova proteção falha.
**Política de replicação de failback** | Para replicar de volta para o site local, você precisa de uma política de failback. Essa política é criada automaticamente quando você cria uma política de replicação para o Azure.<br/><br/> A política é associada automaticamente ao servidor de configuração. Ele é definido como um limite de RPO de 15 minutos, retenção de ponto de recuperação de 24 horas e frequência de instantâneo consistente com o aplicativo é de 60 minutos. A política não pode ser editada. 
**Emparelhamento privado VPN/ExpressRoute de site a site** | A nova proteção e o failback precisam de uma conexão VPN site a site ou do emparelhamento privado do ExpressRoute para replicar dados. 


## <a name="ports-for-reprotectionfailback"></a>Portas para nova proteção/failback

Um número de portas deve estar aberto para nova proteção/failback. O gráfico a seguir ilustra as portas e o fluxo de reproteção/failback.

![Portas para failover e failback](./media/vmware-azure-reprotect/failover-failback.png)


## <a name="deploy-a-process-server-in-azure"></a>Implantar um servidor em processo no Azure

1. [Configure um servidor de processo](vmware-azure-set-up-process-server-azure.md) no Azure para failback.
2. Verifique se as VMs do Azure podem alcançar o servidor de processo. 
3. Verifique se a conexão VPN site a site ou a rede de emparelhamento privado do ExpressRoute tem largura de banda suficiente para enviar dados do servidor de processo para o site local.

## <a name="deploy-a-separate-master-target-server"></a>Implantar um servidor de destino mestre separado

1. Observe os [requisitos e as limitações](#reprotectionfailback-components)do servidor de destino mestre.
2. Crie um servidor de destino mestre do [Windows](site-recovery-plan-capacity-vmware.md#deploy-additional-master-target-servers) ou [Linux](vmware-azure-install-linux-master-target.md) para corresponder ao sistema operacional das VMs que você deseja proteger novamente e fazer failback.
3. Verifique se você não usa o Storage vMotion para o servidor de destino mestre ou o failback pode falhar. A máquina VM não pode ser iniciada porque os discos não estão disponíveis para ela.
    - Para evitar isso, exclua o servidor de destino mestre da sua lista do vMotion.
    - Se um destino mestre passar por uma tarefa de armazenamento do vMotion após a nova proteção, os discos de VM protegidos anexados ao servidor de destino mestre migrarão para o destino da tarefa do vMotion. Se você tentar realizar o failback após isso, a desanexação de disco falhará porque os discos não foram encontrados. Em seguida, é difícil encontrar os discos em suas contas de armazenamento. Se isso ocorrer, localize-os manualmente e anexe-os à VM. Depois disso, a VM local pode ser inicializada.

4. Adicione uma unidade de retenção ao servidor de destino mestre do Windows existente. Adicione um novo disco e formate a unidade. A unidade de retenção é usada para interromper os pontos no tempo em que a VM é replicada de volta para o site local. Observe esses critérios. Se eles não forem atendidos, a unidade não será listada para o servidor de destino mestre:
    - O volume não é usado para nenhuma outra finalidade, como um destino de replicação, e não está no modo de bloqueio.
    - O volume não é um volume de cache. O volume de instalação personalizado para o destino mestre e servidor de processo não está qualificado para um volume de retenção. Quando o servidor de processo e o destino mestre são instalados em um volume, o volume é um cache de destino mestre.
    - Não é o tipo de sistema de arquivos do volume FAT ou FAT32.
    - A capacidade do volume é diferente de zero.
    - O volume de retenção padrão para o Windows é o volume R.
    - O volume de retenção padrão para o Linux é /mnt/retention.

5. Adicione uma unidade se você estiver usando um servidor de processo existente. A nova unidade deve atender aos requisitos na última etapa. Se a unidade de retenção não estiver presente, ele não aparecer na lista suspensa de seleção no portal. Depois de adicionar uma unidade ao destino mestre local, levará até 15 minutos para que a unidade apareça na seleção no portal. Você pode atualizar o servidor de configuração se a unidade não aparecer após 15 minutos.
6. Instale as ferramentas do VMware ou o open-vm-tools no servidor de destino principal. Sem as ferramentas, os datastores no host ESXi do destino mestre não podem ser detectados.
7. Defina o disco. EnableUUID = true configurada nos parâmetros de configuração da VM de destino mestre no VMware. Se essa linha não existir, adicione-o. Essa configuração é necessária para fornecer um UUID consistente para o VMDK para que ele monta corretamente.
8. Verifique os requisitos de acesso do vCenter Server:
    - Se a VM para a qual você está realizando o failback estiver em um host ESXi gerenciado pelo VMware vCenter Server, o servidor de destino mestre precisará acessar o arquivo de disco de máquina virtual (VMDK) da VM local, para gravar os dados replicados nos discos da máquina virtual. Verifique se o repositório de armazenamento de VM local está montado no host de destino mestre com acesso de leitura/gravação.
    - Se a VM não estiver em um host ESXi gerenciado por um VMware vCenter Server, Site Recovery criará uma nova VM durante a reproteção. Essa VM é criada no host ESXi no qual você cria a VM do servidor de destino mestre. Escolha o host ESXi cuidadosamente para criar a VM no host que você deseja. O disco rígido da VM precisa estar em um armazenamento de dados acessível para o host no qual o servidor de destino mestre está em execução.
    - Outra opção, se a VM local já existir para failback, ela deverá ser excluída antes da execução de um failback. Em seguida, o failback cria uma nova VM no mesmo host que o host ESXi de destino mestre. Quando você realiza o failback para um local alternativo, os dados são recuperados para o mesmo armazenamento e o mesmo host ESXi usado pelo servidor de destino mestre local.
9. Para computadores físicos que executam failback em VMs VMware, você deve concluir a descoberta do host no qual o servidor de destino mestre está em execução, antes de proteger novamente o computador.
10. Verifique se o host ESXi no qual a VM de destino mestre tem pelo menos um repositório de armazenamento de sistemas de arquivos de máquina virtual (VMFS) anexado a ele. Se nenhum repositório de dados do VMFS estiver anexado, a entrada do repositório do armazenamento nas configurações de nova proteção estará vazia e você não poderá continuar.


## <a name="next-steps"></a>Próximas etapas

[Proteger](vmware-azure-reprotect.md) novamente uma VM.
