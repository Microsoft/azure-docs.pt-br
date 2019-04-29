---
title: Sobre a recuperação de desastres de VMs VMware para o Azure usando o Azure Site Recovery | Microsoft Docs
description: Este artigo fornece uma visão geral da recuperação de desastres de VMs VMware para o Azure usando o serviço Azure Site Recovery.
author: raynew
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 3/18/2019
ms.author: raynew
ms.openlocfilehash: 9d7b94500320315c1379ba3dfb8b6460bc105b49
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61272686"
---
# <a name="about-disaster-recovery-of-vmware-vms-to-azure"></a>Sobre a recuperação de desastres de VMs do VMware para o Azure

Este artigo fornece uma visão geral da recuperação de desastres para VMs VMware locais no Azure usando o serviço [Azure Site Recovery](site-recovery-overview.md).

## <a name="what-is-bcdr"></a>O que é BCDR?

Uma estratégia de continuidade de negócios e recuperação de desastres (BCDR) ajuda a manter seus negócios em funcionamento. Durante o tempo de inatividade planejado e interrupções inesperadas, o BCDR mantém os dados seguros e disponíveis e garante que os aplicativos continuem em execução. Além dos recursos da plataforma BCDR, como o emparelhamento regional e o armazenamento de alta disponibilidade, o Azure fornece os Serviços de recuperação como parte integrante da sua solução BCDR. Serviços de recuperação incluem: 

- [O Backup do Azure](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup) faz o backup de seus dados locais e da VM do Azure. Você pode fazer backup de arquivos e pastas, cargas de trabalho específicas ou uma VM inteira. 
- [O Azure Site Recovery](site-recovery-overview.md) fornece resiliência e recuperação de desastre para aplicativos e cargas de trabalho em execução em máquinas locais ou VMs de IaaS do Azure. O Site Recovery orquestra a replicação e lida com o failover para o Azure quando ocorrem interrupções. Ele também lida com a recuperação do Azure para seu site principal. 

## <a name="how-does-site-recovery-do-disaster-recovery"></a>Como o Site Recovery fazer a recuperação de desastres?

1. Depois de preparar o Azure e seu site local, você configura e habilita a replicação para suas máquinas locais.
2. O Site Recovery orquestra a replicação inicial da máquina, de acordo com suas configurações de política.
3. Após a replicação inicial, o Site Recovery replica as alterações delta no Azure. 
4. Quando tudo está sendo replicado como esperado, você executa um drill recovery de recuperação de desastres.
    - A análise ajuda a garantir que o failover funcione conforme o esperado quando surge uma necessidade real.
    - A análise executa um failover de teste sem afetar seu ambiente de produção.
5. Se ocorrer uma interrupção, você executar um failover completo para o Azure. Você pode fazer failover de um único computador, ou você pode criar um plano de recuperação que executa o failover de várias máquinas ao mesmo tempo.
6. Durante o failover, VMs do Azure são criadas usando os dados VM em discos gerenciados ou contas de armazenamento. Os usuários podem continuar acessando aplicativos e cargas de trabalho da VM do Azure
7. Quando seu site local estiver disponível novamente, você fará o failback do Azure.
8. Depois que você fizer o failback e estiver trabalhando novamente em seu site principal, você começará a replicar as VMs locais para o Azure novamente.


## <a name="how-do-i-know-if-my-environment-is-suitable-for-disaster-recovery-to-azure"></a>Como sei se meu ambiente é adequado para recuperação de desastre no Azure?

O Site Recovery pode replicar qualquer carga de trabalho em execução em uma VM VMware ou servidor físico suportado. Aqui estão as coisas que você precisa verificar em seu ambiente:

- Se você está replicando VMs VMware, está executando as versões corretas dos servidores de virtualização VMware? [Verifique aqui](vmware-physical-azure-support-matrix.md#on-premises-virtualization-servers).
- São as máquinas que você deseja replicar executando um sistema operacional com suporte? [Verifique aqui](vmware-physical-azure-support-matrix.md#replicated-machines).
- Para recuperação de desastre do Linux, as máquinas estão executando um sistema de arquivos / armazenamento de convidado com suporte? [Marque aqui](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage)
- Os computadores que você deseja replicar atendem aos requisitos do Azure? [Verifique aqui](vmware-physical-azure-support-matrix.md#azure-vm-requirements).
- Sua configuração de rede é suportada? [Verifique aqui](vmware-physical-azure-support-matrix.md#network).
- Há suporte para sua configuração de armazenamento? [Verifique aqui](vmware-physical-azure-support-matrix.md#storage).


## <a name="what-do-i-need-to-set-up-in-azure-before-i-start"></a>O que é necessário configurar no Azure antes de começar?

No Azure, você precisa preparar o seguinte:

1. Verifique se sua conta do Azure tem permissões para criar VMs no Azure.
2. Crie uma rede do Azure que as VMs do Azure serão Unidos quando elas forem criadas após o failover de contas de armazenamento ou discos gerenciados.
3. Configure um cofre do Azure Recovery Services para o Site Recovery. O vault reside no portal do Azure e é usado para implantar, configurar, orquestrar, monitorar e solucionar problemas de sua implantação do Site Recovery.

*Precisa de mais ajuda?*

Saiba como configurar o Azure por [verificar sua conta](tutorial-prepare-azure.md#verify-account-permissions), criando uma [rede](tutorial-prepare-azure.md#set-up-an-azure-network), e [como configurar um cofre](tutorial-prepare-azure.md#create-a-recovery-services-vault).



## <a name="what-do-i-need-to-set-up-on-premises-before-i-start"></a>O que preciso configurar no local antes de começar?

Aqui está o que você precisa fazer:

1. Você precisa configurar duas contas:

    - Se você estiver replicando as VMs VMware, será necessária uma conta para o Site Recovery acessar os hosts do vCenter SErver ou do vSphere ESXi para descobrir automaticamente as VMs.
    - É necessária uma conta para instalar o agente de serviço do Site Recovery Mobility em cada máquina física ou VM que você deseja replicar.

2. Você precisa verificar a compatibilidade da sua infraestrutura VMware, se você não fez isso anteriormente.
3. Verifique se você pode se conectar às VMs do Azure após um failover. Você configura o RDP em máquinas Windows locais ou o SSH em máquinas Linux.

*Precisa de mais ajuda?*
- Prepare contas para [descoberta automática](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) e para [instalação do serviço de Mobilidade](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-mobility-service-installation).
- [Verifique se](vmware-azure-tutorial-prepare-on-premises.md#check-vmware-requirements) que suas configurações de VMware são compatíveis.
- [Preparar](vmware-azure-tutorial-prepare-on-premises.md#prepare-to-connect-to-azure-vms-after-failover) para que você se conectar no Azure após o failover.
- Se você quiser mais ajuda aprofundada sobre como configurar o endereçamento IP para VMs do Azure após o failover, [leia este artigo](concepts-on-premises-to-azure-networking.md).

## <a name="how-do-i-set-up-disaster-recovery"></a>Como configurar a recuperação de desastres?

Depois de ter sua infraestrutura do Azure e local em vigor, você pode configurar a recuperação de desastres.

1. Para entender os componentes que você precisará implantar, revise o [VMware para a arquitetura do Azure](vmware-azure-architecture.md) e o [físico para a arquitetura do Azure](physical-azure-architecture.md). Há vários componentes, por isso é importante entender como eles se encaixam.
2. **Ambiente de origem**: como uma primeira etapa na implantação, configure seu ambiente de origem de replicação. Você especifica o que você deseja replicar e onde você deseja replicar.
3. **Servidor de configuração**: é preciso configurar um servidor de configuração em seu ambiente de origem local:
    - O servidor de configuração é uma única máquina local. Para recuperação de desastre VMware, recomendamos que você a implante como uma VM VMware que pode ser implantada a partir de um modelo OVF para download.
    - O servidor de configuração coordena as comunicações entre o local e o Azure
    - Alguns outros componentes são executados na máquina do servidor de configuração.
        - O servidor de processo recebe, otimiza e envia dados de replicação para a conta de armazenamento de cache no Azure. Ele também lida com a instalação automática do serviço Mobility em máquinas que você deseja replicar e executa a descoberta automática de VMs em servidores VMware.
        - O servidor de destino mestre lida com os dados de replicação durante o failback do Azure.
    - A configuração inclui o registro do servidor de configuração no cofre, o download do servidor MySQL e do VMware PowerCLI e a especificação das contas criadas para a descoberta automática e a instalação do serviço Mobilidade.
4. **Ambiente de destino**: Você configurar o destino do ambiente do Azure, especificando sua assinatura do Azure e as configurações de rede.
5. **Política de replicação**: especifique como a replicação deve ocorrer. As configurações incluem a frequência com que os pontos de recuperação são criados e armazenados e se os instantâneos consistentes com o aplicativo devem ser criados.
6. **Habilite a replicação**. Você habilita a replicação para máquinas locais. Se você criou uma conta para instalar o serviço Mobility, ela será instalada quando você habilitar a replicação para uma máquina. 

*Precisa de mais ajuda?*

- Para uma rápida explicação dessas etapas, você pode testar nosso [tutorial do VMware](vmware-azure-tutorial.md) e [o passo a passo do servidor físico](physical-azure-disaster-recovery.md).
- [Saiba mais](vmware-azure-set-up-source.md) sobre como configurar seu ambiente de origem.
- [Saiba mais sobre](vmware-azure-deploy-configuration-server.md) requisitos do servidor de configuração e como configurar o servidor de configuração com um modelo OVF para replicação do VMware. Se por alguma razão você não pode usar um modelo, ou você estiver replicando servidores físicos [use estas instruções](physical-azure-set-up-source.md#set-up-the-source-environment).
- [Saiba mais](vmware-azure-set-up-target.md) sobre as configurações de destino.
- [Obtenha mais informações](vmware-azure-set-up-replication.md) sobre como configurar uma política de replicação.
- [Aprenda](vmware-azure-enable-replication.md) como habilitar a replicação e [exclua](vmware-azure-exclude-disk.md) discos da replicação.


## <a name="something-went-wrong-how-do-i-troubleshoot"></a>Algo deu errado, como solucionar?

- Como primeiro passo, tente [ monitorar sua implantação ](site-recovery-monitor-and-troubleshoot.md) para verificar o status de itens replicados, tarefas e problemas de infraestrutura e identificar erros.
- Se você não consegue concluir a replicação inicial ou a replicação em andamento não está funcionando conforme o esperado, [Examine este artigo](vmware-azure-troubleshoot-replication.md) para erros comuns e dicas de solução de problemas.
- Se você estiver tendo problemas com a instalação automática do serviço de mobilidade em computadores que você deseja replicar, revise os erros comuns nos [deste artigo](vmware-azure-troubleshoot-push-install.md).
- Se o failover não está funcionando conforme o esperado, verifique os erros comuns no [deste artigo](site-recovery-failover-to-azure-troubleshoot.md).
- Se o failback não estiver funcionando, verifique se o problema aparece em [este artigo](vmware-azure-troubleshoot-failback-reprotect.md).



## <a name="next-steps"></a>Próximas etapas

Com a replicação agora em vigor, você deve [executar uma simulação de recuperação de desastre](tutorial-dr-drill-azure.md) para garantir que o failover funcione conforme o esperado. 
