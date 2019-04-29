---
title: Novidades do Azure Site Recovery | Microsoft Docs
description: Fornece um resumo dos novos recursos introduzidos no Azure Site Recovery
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: raynew
ms.openlocfilehash: 5ee1328dddb6ae1e1c878384097b0e10aa32feeb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60776281"
---
# <a name="whats-new-in-site-recovery"></a>Novidades do Site Recovery

O serviço [Azure Site Recovery](site-recovery-overview.md) é atualizado e aprimorado continuamente. Para ajudar você a se manter atualizado, este artigo fornece informações sobre as últimas versões, novos recursos e novo conteúdo. Esta página é atualizada regularmente.

Caso você tenha sugestões de recursos do Site Recovery, adoraríamos [ouvir seus comentários](https://feedback.azure.com/forums/256299-site-recovery).

## <a name="q1-2019"></a>T1 2019 

### <a name="update-rollup-34-february-2019"></a>Pacote cumulativo de atualizações 34 (fevereiro de 2019)

[Atualização cumulativa 34](https://support.microsoft.com/help/4490016/update-rollup-34-for-azure-site-recovery) fornece as seguintes atualizações.

**Atualização** | **Detalhes**
--- | ---
**Provedores e agentes** | Uma atualização para os agentes de recuperação de Site e provedores (conforme detalhado no valor acumulado)
**Correções de problema** | Uma série de correções e aprimoramentos (conforme detalhado no valor acumulado)



### <a name="update-rollup-33-february-2019"></a>Pacote cumulativo de atualizações 33 (fevereiro de 2019)

[Atualização cumulativa 33](https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery) fornece as seguintes atualizações.

**Atualização** | **Detalhes**
--- | ---
**Provedores e agentes** | Uma atualização para os agentes de recuperação de Site e provedores (conforme detalhado no valor acumulado)
**Correções de problema** | Uma série de correções e aprimoramentos (conforme detalhado no valor acumulado)
**Mapeamento de rede** | Para recuperação de desastres de VM do Azure, agora você pode usar qualquer rede de destino disponíveis quando você habilita a replicação. 
**SSD Standard** | Você pode configurar a recuperação de desastre para VMs do Azure usando agora [discos SSD Standard](https://docs.microsoft.com/azure/virtual-machines/windows/disks-standard-ssd).
**Espaços de armazenamento diretos** | Você pode configurar a recuperação de desastre para aplicativos em execução nos aplicativos de VM do Azure por meio [espaços de armazenamento diretos](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview) para alta disponibilidade.
**Sistema de arquivos BRTFS** | Suporte para VMs do VMware, além das VMs do Azure.<br/><br/> Não tem suporte se: O volume de subpropriedades de sistema de arquivos BTRFS for alterado depois de habilitar a replicação, o sistema de arquivos é distribuído entre vários discos, ou se o sistema de arquivos os BTRFS dá suporte a RAID.



### <a name="update-rollup-32-january-2019"></a>Pacote cumulativo de atualizações 32 (de janeiro de 2019)

[Atualização cumulativa 31](https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery) fornece as seguintes atualizações.

**Atualização** | **Detalhes**
--- | ---
**Provedores e agentes** | Uma atualização para os agentes de recuperação de Site e provedores (conforme detalhado no valor acumulado)
**Correções de problema** | Uma série de correções e aprimoramentos (conforme detalhado no valor acumulado)
**Recuperação de desastre para Linux** | **VMs do Azure**: Estação de trabalho do RedHat 6 horas, 7; suporte para novas versões de kernel para Ubuntu, Debian e SUSE.<br/><br/> **VMs VMware/servidores físicos**: RedHat Enterprise Linux 7.6; Estação de trabalho do RedHat 6 horas, 7; Oracle Linux 6.10/7.6; oferece suporte para novas versões de kernel para Ubuntu, Debian e SUSE.


### <a name="update-rollup-31-january-2019"></a>Pacote cumulativo de atualizações 31 (de janeiro de 2019)

[Atualização cumulativa 31](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery) fornece as seguintes atualizações.

**Atualização** | **Detalhes**
--- | ---
**Provedores e agentes** | Uma atualização para os agentes de recuperação de Site e provedores (conforme detalhado no valor acumulado)
**Correções de problema** | Uma série de correções e aprimoramentos (conforme detalhado no valor acumulado)
**Recuperação de desastre para Linux** | **VMs do Azure**: Oracle Linux 6.8 e 6.9/7.0; suporte para kernels UEK5.<br/><br/> **VMs VMware/servidores físicos**: Oracle Linux 6.8 e 6.9/7.0; suporte para UEK5 kernel.
**Sistema de arquivos BRTFS** | Suporte para VMs do Azure.
**LVM** | Suporte adicionado para volumes LVM e LVM2.<br/><br/> Há suporte para o diretório de /boot em uma partição de disco e nos volumes LVM.
**Diretórios** | Suporte adicionado para esses diretórios seet como partições separadas ou sistemas de arquivos que não estão no mesmo disco do sistema: / (raiz), /boot, /usr, /usr/local, /var, /etc.
**Windows Server 2008** | Suporte adicionado para discos dinâmicos.
**Failover de VM do VMware** | Tempo de failover aprimorado para VMs do VMware em que storvsc e vsbus não são drivers de inicialização.
**Suporte para UEFI** | As VMs do Azure não dão suporte a tipo de inicialização UEFI. Agora você pode migrar servidores físicos locais com a UEFI para o Azure com o Site Recovery. Site Recovery migra o servidor ao converter o tipo de inicialização em BIOS antes da migração. Recuperação de site anteriormente essa conversão só suporte para VMs. O suporte está disponível para servidores físicos que executam o Windows Server 2012 ou posterior.
**VMs do Azure em zonas de disponibilidade** | Você pode habilitar a replicação para outra região para VMs do Azure implantadas em zonas de disponibilidade. UO agora pode habilitar a replicação para uma VM do Azure e definir o destino de failover para uma única instância VM, uma VM em um conjunto de disponibilidade ou uma VM em uma zona de disponibilidade. A configuração não afeta a replicação. [Leia](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region/) o comunicado.


## <a name="q4-2018"></a>T4 2018

### <a name="update-rollup-30-october-2018"></a>Pacote cumulativo de atualizações 30 (outubro de 2018)

[Atualização cumulativa 30](https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30) fornece as seguintes atualizações.

**Atualização** | **Detalhes**
--- | ---
**Provedores e agentes** | Uma atualização para os agentes de recuperação de Site e provedores (conforme detalhado no valor acumulado)
**Correções de problema** | Uma série de correções e aprimoramentos (conforme detalhado no valor acumulado)
**Suporte de região** | Suporte adicionado para a Austrália Central 1 e na Austrália Central 2 a recuperação de site.
**Suporte para criptografia de disco** | Suporte adicionado para recuperação de desastre de VMs do Azure criptografada com o Azure Disk Encryption (ADE) com o aplicativo do Azure AD. [Saiba mais](azure-to-azure-how-to-enable-replication-ade-vms.md).
**Exclusão de disco** | Discos não inicializados agora são excluídos automaticamente durante a replicação de VM do Azure.
**Habilitado para firewall de armazenamento** | Suporte adicionado para [contas de armazenamento habilitado para firewall](https://docs.microsoft.com/azure/storage/common/storage-network-security).<br/><br/> Você pode replicar VMs do Azure com discos não gerenciados nas contas de armazenamento firewall habilitado para outra região do Azure para recuperação de desastres.<br/><br/> Você pode usar contas de armazenamento habilitado para firewall como contas de armazenamento de destino para discos não gerenciados.<br/><br/> Suporte para apenas usando o PowerShell.


### <a name="update-rollup-29-october-2018"></a>Pacote cumulativo de atualizações 29 (outubro de 2018)

[Atualização cumulativa 29](https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery) fornece as seguintes atualizações.

**Atualização** | **Detalhes**
--- | ---
**Provedores e agentes** | Uma atualização para os agentes de recuperação de Site e provedores (conforme detalhado no valor acumulado)
**Correções de problema** | Uma série de correções e aprimoramentos (conforme detalhado no valor acumulado)

### <a name="automatic-updates-for-the-mobility-service-extension"></a>Atualizações automáticas para a extensão do Serviço de Mobilidade

O Site Recovery adicionou uma opção para atualizações automáticas à extensão do Serviço de Mobilidade. A extensão do Serviço de Mobilidade é instalada em cada VM do Azure replicada pelo Site Recovery. Ao habilitar a replicação, selecione se deseja permitir que o Site Recovery gerencie as atualizações para a extensão. As atualizações não exigem uma reinicialização de VM e não afetam a replicação. [Saiba mais](azure-to-azure-autoupdate.md).

### <a name="disaster-recovery-for-vms-using-accelerated-networking"></a>Recuperação de desastre para VMs que usam a rede acelerada

A rede acelerada habilita o SR-IOV (virtualização de E/S de raiz única) em uma VM, melhorando o desempenho de rede. Quando você habilita a replicação em uma VM do Azure, o Site Recovery detecta se a rede acelerada está habilitada. Nesse caso, após o failover, o Site Recovery configura automaticamente a rede acelerada na VM do Azure de réplica de destino para o [Windows](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell#enable-accelerated-networking-on-existing-vms) e o [Linux](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli#enable-accelerated-networking-on-existing-vms). [Saiba mais](azure-vm-disaster-recovery-with-accelerated-networking.md).

### <a name="pricing-calculator-for-azure-vm-disaster-recovery"></a>Calculadora de Preços para recuperação de desastre da VM do Azure

A recuperação de desastre para VMs do Azure gera custos de licenciamento da VM e custos de rede e armazenamento. O Azure fornece uma [Calculadora de Preços](https://aka.ms/a2a-cost-estimator) para ajudar você a entender esses custos. O Site Recovery agora fornece uma [estimativa de preço de exemplo](https://aka.ms/a2a-cost-estimator) que precifica uma implantação de exemplo com base em um aplicativo de três camadas usando seis VMs com 12 discos HDD Standard e 6 discos SSD Premium. A amostra presume uma alteração de dados de 10 GB por dia para Standard e 20 GB para Premium. Para sua implantação específica, você pode alterar as variáveis para estimar os custos. Você pode especificar o número de VMs, o número e o tipo de discos gerenciados e a taxa total esperada de alteração de dados entre as VMs. Além disso, você pode aplicar um fator de compactação para estimar os custos de largura de banda. [Leia](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/) o comunicado.



## <a name="q3-2018"></a>T3 2018 


### <a name="update-rollup-28-august-2018"></a>Pacote cumulativo de atualizações 28 (agosto de 2018)

[Atualização cumulativa 28](https://support.microsoft.com/help/4460079/update-rollup-28-for-azure-site-recovery) fornece as seguintes atualizações.

**Atualização** | **Detalhes**
--- | ---
**Provedores e agentes** | Uma atualização para os agentes de recuperação de Site e provedores (conforme detalhado no valor acumulado)
**Recuperação de desastre para Linux** | **VMs do Azure**: Adição de suporte para 6.10 de Linux do RedHat Enterprise; CentOS 6.10.<br/><br/> **VMs VMware**: RedHat Enterprise Linux 6.10; CentOS 6.10.<br/><br/> VMs com base em Linux, que agora há suporte para usar o estilo de partição GUID partição GPT (tabela) no modo de compatibilidade BIOS herdado. Ver [perguntas frequentes sobre discos de VM IaaS do Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/faq-for-disks) para obter mais informações. 
**Suporte de nuvem** | Suporte para recuperação de desastre para VMs do Azure no Germany cloud.
**Recuperação de desastres entre assinaturas** | Suporte para replicar VMs do Azure em uma região para outra região em uma assinatura diferente, no mesmo locatário do Active Directory do Azure. [Saiba mais](https://aka.ms/cross-sub-blog).
**Windows Server 2008** | Suporte para a migração de máquinas em execução do Windows Server 2008 R2/2008 64 bits e 32 bits.<br/><br/> Somente migração (replicação e failover). Não há suporte para failback.

### <a name="update-rollup-27-july-2018"></a>Pacote cumulativo de atualizações 27 (julho de 2018)

[Atualização cumulativa 27](https://support.microsoft.com/help/4055712/update-rollup-27-for-azure-site-recovery) fornece as seguintes atualizações.

**Atualização** | **Detalhes**
--- | ---
**Provedores e agentes** | Uma atualização para os agentes de recuperação de Site e provedores (conforme detalhado no valor acumulado)
**Recuperação de desastre para Linux** | **VMs do Azure**: Red Hat Enterprise Linux 7.5<br/><br/> **VMs VMware/servidores físicos**: Red Hat Enterprise Linux 7.5, SUSE Linux Enterprise Server 12




## <a name="next-steps"></a>Próximas etapas

Mantenha-se atualizado com nossas atualizações na página [Atualizações do Azure](https://azure.microsoft.com/updates/?product=site-recovery).




 









