---
title: "Como funciona a replicação de computadores locais para um site local secundário no Azure Site Recovery? | Microsoft Docs"
description: "Este artigo fornece uma visão geral dos componentes e da arquitetura usada ao replicar VMs e servidores físicos locais para um site secundário com o serviço Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: c413efcd-d750-4b22-b34b-15bcaa03934a
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/29/2017
ms.author: raynew
ms.openlocfilehash: fca95c63964b955db7ddfbe53250702cc8af122e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-does-on-premises-machine-replication-to-a-secondary-site-work-in-site-recovery"></a>Como funciona a replicação de computadores locais para um site secundário no Site Recovery?

Este artigo descreve os componentes e os processos envolvidos ao replicar máquinas virtuais e servidores físicos locais para o Azure, usando o serviço [Azure Site Recovery](site-recovery-overview.md).

Você pode replicar o seguinte em um site local secundário:
- VMs do Hyper-V em clusters Hyper-V e hosts autônomos gerenciadas em nuvens VMM (System Center Virtual Machine Manager).
- VMs VMware locais e servidores físicos do Windows/Linux. Neste cenário, a replicação é gerenciada pelo Scout.

Poste comentários na parte inferior deste artigo ou no [Fórum de Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="replicate-hyper-v-vms-to-a-secondary-on-premises-site"></a>Replicar máquinas virtuais do Hyper-V em um site local secundário


### <a name="architectural-components"></a>Componentes de arquitetura

Veja o que você precisa para replicar VMs Hyper-V em um site secundário.

**Componente** | **Localidade** | **Detalhes**
--- | --- | ---
**As tabelas** | Você precisa de uma conta na Microsoft. |
**Servidor VMM** | Recomendamos um servidor VMM no site primário e um no site secundário | Cada servidor VMM devem estar conectados à Internet.<br/><br/> Cada servidor deve ter pelo menos uma nuvem privada de VMM, com o conjunto de perfis de funcionalidade do Hyper-V.<br/><br/> Instale o Provedor do Azure Site Recovery no servidor do VMM. O Provedor coordena e organiza a replicação com o serviço Recuperação de Site pela Internet. As comunicações entre o Provedor e o Azure são protegidas e criptografadas.
**Servidor Hyper-V** |  Um ou mais servidores de host do Hyper-V nas nuvens do VMM primárias e secundárias.<br/><br/> Os servidores devem estar conectados à Internet.<br/><br/> Os dados são replicados entre os servidores de host Hyper-V primários e secundários pela LAN ou VPN usando a autenticação Kerberos ou autenticação de certificado.  
**VMs Hyper-V** | Localizado no servidor de host Hyper-V de origem. | O servidor host de origem deve ter pelo menos uma VM que você deseja replicar.

### <a name="replication-process"></a>Processo de replicação

1. Você configura a conta do Azure.
2. Crie um cofre de Serviços de Replicação para Recuperação de Site e defina configurações de cofre, incluindo:

    - Origem e destino de replicação (sites primários e secundários).
    - Instalação do provedor do Azure Site Recovery e do agente dos Serviços de Recuperação do Microsoft Azure. O provedor está instalado nos servidores do VMM, e o agente em cada host Hyper-V.
    - Você cria uma política de replicação para a nuvem do VMM de origem. A política é aplicada a todas as VMs localizadas nos hosts na nuvem.
    - Você habilita a replicação para VMs do Hyper-V. A replicação inicial ocorre de acordo com as configurações de política de replicação.
4. As alterações de dados são acompanhadas, e a replicação de alterações delta começa após a replicação inicial terminar. As alterações acompanhadas para um item são mantidas em um arquivo .hrl.
5. Você executa um failover de teste para verificar se tudo está funcionando.

**Figura 1: Replicação de VMM para VMM**

![Local para o próprio local](./media/site-recovery-components/arch-onprem-onprem.png)

### <a name="failover-and-failback-process"></a>Processo de failover e failback

1. Você pode executar um [failover](site-recovery-failover.md) planejado ou não planejado entre sites locais. Se você executar um failover planejado, as VMs de origem serão desligadas para evitar a perda de dados.
2. Você pode fazer o failover de um único computador ou criar [planos de recuperação](site-recovery-create-recovery-plans.md) para orquestrar o failover de vários computadores.
4. Se você executar um failover não planejado para um site secundário, depois as máquinas de failover no local secundário não serão habilitadas para replicação ou proteção. Depois de executar um failover planejado, os computadores no local secundário são protegidos.
5. Em seguida, você confirma o failover para começar a acessar a carga de trabalho na VM de réplica.
6. Quando seu site primário estiver disponível novamente, você poderá iniciar a replicação inversa para replicar do site secundário para o primário. A replicação inversa coloca as máquinas virtuais em um estado protegido, mas o datacenter secundário permanece sendo o local ativo.
7. Para transformar o site primário em local ativo novamente, inicie um failover planejado do site secundário para o primário, seguido por outra replicação inversa.




## <a name="replicate-vmware-vmsphysical-servers-to-a-secondary-site"></a>Replicar VMs VMware/servidores físicos para um site secundário

Replique VMs VMware ou servidores físicos para um site secundário usando o InMage Scout, usando estes componentes de arquitetura:


### <a name="architectural-components"></a>Componentes de arquitetura

**Componente** | **Localidade** | **Detalhes**
--- | --- | ---
**As tabelas** | InMage Scout. | Para obter o InMage Scout, você precisa de uma assinatura do Azure.<br/><br/> Depois de criar um cofre de Serviços de Recuperação, baixe o InMage Scout e instale as atualizações mais recentes para configurar a implantação.
**Servidor de processo** | Localizado no site primário | Implante o servidor de processo para manipular o caching, a compactação e a otimização de dados.<br/><br/> Ele também trata da instalação por push do Agente Unificado nas máquinas que você deseja proteger.
**Servidor de configuração** | Localizado no site secundário | O servidor de configuração gerencia, configura e monitora sua implantação, usando o site de gerenciamento ou o console do vContinuum.
**Servidor vContinuum** | Opcional. Instalado no mesmo local que o servidor de configuração. | Ele fornece um console para o gerenciamento e monitoramento de seu ambiente protegido.
**Servidor de destino mestre** | Localizado no site secundário | O servidor de destino mestre armazena os dados replicados. Ele recebe os dados do servidor de processo, cria uma máquina de réplica no site secundário e mantém os pontos de retenção dos dados.<br/><br/> O número de servidores de destino mestre necessário depende do número de máquinas que você está protegendo.<br/><br/> Se você quiser realizar o failback no site primário, também precisará de um servidor de destino mestre nesse local. O Agente Unificado está instalado neste servidor.
**Servidor VMware ESX/ESXi e vCenter** |  VMs são hospedadas em hosts ESX/ESXi. Hosts são gerenciados com um servidor do vCenter | Você precisa de uma infraestrutura do VMware para replicar VMs VMware.
**VMs/servidores físicos** |  Agente Unificado instalado em VMs VMware ou em servidores físicos que você deseja replicar. | O agente atua como um provedor de comunicação entre todos os componentes.


### <a name="replication-process"></a>Processo de replicação

1. Configure os servidores de componente em cada site (configuração, processo, destino mestre) e instale o Agente Unificado nos computadores que você deseja replicar.
2. Após a replicação inicial, os agentes em cada computador enviam as alterações de replicação delta para o servidor de processo.
3. O servidor de processo otimiza os dados e os transfere para o servidor de destino mestre no site secundário. O servidor de configuração gerencia o processo de replicação.

**Figura 2: Replicação do VMware para o VMware**

![VMware para VMware](./media/site-recovery-components/vmware-to-vmware.png)


## <a name="next-steps"></a>Próximas etapas

Examine a [matriz de suporte](site-recovery-support-matrix-to-sec-site.md)
