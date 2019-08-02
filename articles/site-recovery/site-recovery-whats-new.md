---
title: Novidades do Azure Site Recovery | Microsoft Docs
description: Fornece um resumo dos novos recursos introduzidos no Azure Site Recovery
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/18/2019
ms.author: raynew
ms.openlocfilehash: 0bfaf848b0b700c8d745acf8c6a63c898dc91140
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67272874"
---
# <a name="whats-new-in-site-recovery"></a>Novidades do Site Recovery

O serviço [Azure Site Recovery](site-recovery-overview.md) é atualizado e aprimorado continuamente. Para ajudar você a se manter atualizado, este artigo fornece informações sobre as últimas versões, novos recursos e novo conteúdo. Esta página é atualizada regularmente.

Caso você tenha sugestões de recursos do Site Recovery, adoraríamos [ouvir seus comentários](https://feedback.azure.com/forums/256299-site-recovery).


## <a name="updates-june-2019"></a>Atualizações (junho de 2019)

### <a name="update-rollup-37"></a>Pacote cumulativo de atualizações 37

[Atualização cumulativa 37](https://support.microsoft.com/help/4508614/) fornece as seguintes atualizações.

**Atualização** | **Detalhes**
--- | ---
**Provedores e agentes** | Atualizações de agentes de recuperação de Site e nos provedores (conforme detalhado no valor acumulado)
**Correções/melhorias dos problemas** | Uma série de correções e aprimoramentos (conforme detalhado no valor acumulado)


### <a name="vmwarephysical-server-disaster-recovery"></a>Recuperação de desastres do VMware/servidor físico

Recursos adicionados neste mês são resumidas na tabela.

**Recurso** | **Detalhes**
--- | ---
**Partições GPT** | De Update Rollup 37 em diante (Mobility service versão 9.25.5241.1), até cinco GPT partições têm suporte na UEFI. Antes dessa atualização, quatro tinham suporte.



## <a name="updates-may-2019"></a>Atualizações (maio de 2019)

### <a name="update-rollup-36"></a>Pacote cumulativo de atualizações 36

[Atualização cumulativa 36](https://support.microsoft.com/help/4503156) fornece as seguintes atualizações.

**Atualização** | **Detalhes**
--- | ---
**Provedores e agentes** | Uma atualização para os agentes de recuperação de Site e provedores (conforme detalhado no valor acumulado)
**Correções/melhorias dos problemas** | Uma série de correções e aprimoramentos (conforme detalhado no valor acumulado)

### <a name="azure-vm-disaster-recovery"></a>Recuperação de desastres VM do Azure

Recursos adicionados neste mês são resumidas na tabela.

**Recurso** | **Detalhes**
--- | ---
**Replicar discos adicionados** | Habilite a replicação para discos de dados adicionado a uma VM do Azure que já está habilitado para recuperação de desastres. [Saiba mais](azure-to-azure-enable-replication-added-disk.md).
**Atualizações automáticas** | Ao configurar as atualizações automáticas para a extensão de serviço de mobilidade que é executado em VMs do Azure habilitado para recuperação de desastres, agora você pode selecionar uma conta de automação existente para usar, em vez de usar a conta padrão criada pelo Site Recovery. [Saiba mais](azure-to-azure-autoupdate.md).


### <a name="vmwarephysical-server-disaster-recovery"></a>Recuperação de desastres do VMware/servidor físico

Recursos adicionados neste mês são resumidas na tabela.

**Recurso** | **Detalhes**
--- | ---
**Monitoramento do servidor de processo** | Recuperação de desastres de VMs do VMware locais e servidores físicos, monitorar e solucionar problemas de servidor de processo com alertas e relatórios de integridade de servidor aprimoradas. [Saiba mais](vmware-physical-azure-monitor-process-server.md). 





## <a name="updates-march-2019"></a>Atualizações (março de 2019)

### <a name="update-rollup-35"></a>Pacote cumulativo de atualizações 35

[Atualização cumulativa 35](https://support.microsoft.com/en-us/help/4494485/update-rollup-35-for-azure-site-recovery) fornece as seguintes atualizações.

**Atualização** | **Detalhes**
--- | ---
**Provedores e agentes** | Uma atualização para os agentes de recuperação de Site e provedores (conforme detalhado no valor acumulado)
**Correções/melhorias dos problemas** | Uma série de correções e aprimoramentos (conforme detalhado no valor acumulado)

### <a name="vmwarephysical-server-disaster-recovery"></a>Recuperação de desastres do VMware/servidor físico

Recursos adicionados neste mês são resumidas na tabela.

**Recurso** | **Detalhes**
--- | ---
**Discos gerenciados** | Replicação de VMs do VMware locais e servidores físicos agora está diretamente para o managed disks no Azure. Locais dados são enviados para uma conta de armazenamento de cache no Azure e pontos de recuperação são criados em discos gerenciados no local de destino. Isso garante que você não precisa gerenciar várias contas de armazenamento de destino.
**Servidor de configuração** | Agora, o site Recovery dá suporte a um servidor de configuração com várias NICs. Você deve adicionar os adaptadores adicionais à VM do servidor de configuração antes de registrar o servidor de configuração no cofre. Se você adicionar posteriormente, você precisará registrar novamente o servidor no cofre.


## <a name="updates-february-2019"></a>Atualizações (fevereiro de 2019)

### <a name="update-rollup-34"></a>Pacote cumulativo de atualizações 34 

[Atualização cumulativa 34](https://support.microsoft.com/help/4490016/update-rollup-34-for-azure-site-recovery) fornece as seguintes atualizações.

**Atualização** | **Detalhes**
--- | ---
**Provedores e agentes** | Uma atualização para os agentes de recuperação de Site e provedores (conforme detalhado no pacote cumulativo de atualizações).
**Correções/melhorias dos problemas** | Um número de correções e aprimoramentos (conforme detalhado no pacote cumulativo de atualizações).


### <a name="update-rollup-33"></a>Pacote cumulativo de atualizações 33 

[Atualização cumulativa 33](https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery) fornece as seguintes atualizações.

**Atualização** | **Detalhes**
--- | ---
**Provedores e agentes** | Uma atualização para os agentes de recuperação de Site e provedores (conforme detalhado no pacote cumulativo de atualizações).
**Correções/melhorias dos problemas** | Um número de correções e aprimoramentos (conforme detalhado no pacote cumulativo de atualizações).


### <a name="azure-vm-disaster-recovery"></a>Recuperação de desastres VM do Azure 
Recursos adicionados neste mês são resumidas na tabela.

**Recurso** | **Detalhes**
--- | ---
**Mapeamento de rede** | Para recuperação de desastres de VM do Azure, agora você pode usar qualquer rede de destino disponíveis quando você habilita a replicação. 
**SSD Standard** | Você pode configurar a recuperação de desastre para VMs do Azure usando agora [discos SSD Standard](https://docs.microsoft.com/azure/virtual-machines/windows/disks-standard-ssd).
**Espaços de armazenamento diretos** | Você pode configurar a recuperação de desastre para aplicativos em execução nos aplicativos de VM do Azure por meio [espaços de armazenamento diretos](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview) para alta disponibilidade.  Usar espaços de armazenamento diretos (S2D) junto com o Site Recovery fornece proteção abrangente de cargas de trabalho de VM do Azure. S2D permite hospedar um cluster de convidado no Azure. Isso é especialmente útil quando uma VM hospeda um aplicativo crítico, como camada ASCS do SAP, SQL Server ou servidor de arquivos de escalabilidade horizontal.


### <a name="vmwarephysical-server-disaster-recovery"></a>Recuperação de desastres do VMware/servidor físico
Recursos adicionados neste mês são resumidas na tabela.

**Recurso** | **Detalhes**
--- | ---
**Sistema de arquivos Linux BRTFS** | Agora, o site Recovery dá suporte a replicação de VMs VMware com o sistema de arquivos BRTFS. A replicação não é suportada se:<br/><br/>-O volume de subpropriedades de sistema de arquivos BTRFS é alterado após a habilitação da replicação.<br/><br/>-O sistema de arquivos é distribuído entre vários discos.<br/><br/>-O sistema de arquivos BTRFS dá suporte a RAID.
**Windows Server 2019** | Suporte adicionado para computadores executando o Windows Server 2019.


## <a name="updates-january-2019"></a>Atualizações (de janeiro de 2019)


### <a name="accelerated-networking-azure-vms"></a>Rede acelerada (VMs do Azure)

A rede acelerada habilita o SR-IOV (virtualização de E/S de raiz única) em uma VM, melhorando o desempenho de rede. Quando você habilita a replicação em uma VM do Azure, o Site Recovery detecta se a rede acelerada está habilitada. Nesse caso, após o failover, o Site Recovery configura automaticamente a rede acelerada na VM do Azure de réplica de destino para o [Windows](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell#enable-accelerated-networking-on-existing-vms) e o [Linux](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli#enable-accelerated-networking-on-existing-vms).

[Saiba mais](azure-vm-disaster-recovery-with-accelerated-networking.md).

### <a name="update-rollup-32"></a>Pacote cumulativo de atualizações 32 

[Atualização cumulativa 32](https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery) fornece as seguintes atualizações.

**Atualização** | **Detalhes**
--- | ---
**Provedores e agentes** | Uma atualização para os agentes de recuperação de Site e provedores (conforme detalhado no pacote cumulativo de atualizações).
**Correções/melhorias dos problemas** | Um número de correções e aprimoramentos (conforme detalhado no pacote cumulativo de atualizações).

### <a name="azure-vm-disaster-recovery"></a>Recuperação de desastres VM do Azure

Recursos adicionados neste mês são resumidas na tabela.

**Recurso** | **Detalhes**
--- | ---
**Suporte para Linux** | Foi adicionado suporte para a estação de trabalho do RedHat 6 horas, 7 e novas versões de kernel para Ubuntu, Debian e SUSE.
**Espaços de armazenamento diretos** | Site Recovery dá suporte a VMs do Azure usando espaços de armazenamento diretos (S2D).

### <a name="vmware-vmsphysical-servers-disaster-recovery"></a>Recuperação de desastre de servidores de VMs VMware/físicos

Recursos adicionados neste mês são resumidas na tabela.
 
**Recurso** | **Detalhes**
--- | ---
**Suporte para Linux** | Foi adicionado suporte para Redhat Enterprise Linux 7.6, estação de trabalho do RedHat 6 horas, 7, Oracle Linux 6.10/7.6 e novas versões de kernel para Ubuntu, Debian e SUSE.


### <a name="update-rollup-31"></a>Pacote cumulativo de atualizações 31 

[Atualização cumulativa 31](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery) fornece as seguintes atualizações.

**Atualização** | **Detalhes**
--- | ---
**Provedores e agentes** | Uma atualização para os agentes de recuperação de Site e provedores (conforme detalhado no pacote cumulativo de atualizações).
**Correções/melhorias dos problemas** | Um número de correções e aprimoramentos (conforme detalhado no pacote cumulativo de atualizações).

### <a name="vmware-vmsphysical-servers-replication"></a>Replicação de servidores de VMs VMware/físicos 
Recursos adicionados neste mês são resumidas na tabela.
**Recurso** | **Detalhes**
--- | ---
**Suporte para Linux** | Foi adicionado suporte para Oracle Linux 6.8 e 6.9/7.0 e para o kernel UEK5.
**LVM** | Suporte adicionado para volumes LVM e LVM2.<br/><br/> Agora há suporte para o diretório de /boot em uma partição de disco e nos volumes LVM.
**Diretórios** | Foi adicionado suporte para esses diretórios configurados como partições separadas ou sistemas de arquivos que não estão no mesmo disco do sistema:<br/><br/> /(root), /boot, /usr, /usr/local, /var, /etc.
**Windows Server 2008** | Suporte adicionado para discos dinâmicos.
**Failover** | Tempo de failover aprimorado para VMs do VMware em que storvsc e vsbus não são drivers de inicialização.
**Suporte para UEFI** | As VMs do Azure não dão suporte a tipo de inicialização UEFI. Agora você pode migrar servidores físicos locais com a UEFI para o Azure com o Site Recovery. Site Recovery migra o servidor ao converter o tipo de inicialização em BIOS antes da migração. Recuperação de site anteriormente essa conversão só suporte para VMs. O suporte está disponível para servidores físicos que executam o Windows Server 2012 ou posterior.

### <a name="azure-vm-disaster-recovery"></a>Recuperação de desastres VM do Azure
Recursos adicionados neste mês são resumidas na tabela.

**Recurso** | **Detalhes**
--- | ---
**Suporte para Linux** | Com suporte foi adicionado para o Oracle Linux 6.8 e 6.9/7.0; e para o kernel UEK5.
**Sistema de arquivos Linux BRTFS** | Suporte para VMs do Azure.
**VMs do Azure em zonas de disponibilidade** | Você pode habilitar a replicação para outra região para VMs do Azure implantadas em zonas de disponibilidade. Agora você pode habilitar a replicação em uma VM do Azure e definir o destino de failover como uma única instância de VM, uma VM em um conjunto de disponibilidade ou uma VM em uma zona de disponibilidade. A configuração não afeta a replicação. [Leia](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region/) o comunicado.
**Habilitado para firewall de armazenamento (portal/PowerShell)** | Suporte adicionado para [contas de armazenamento habilitado para firewall](https://docs.microsoft.com/azure/storage/common/storage-network-security).<br/><br/> Você pode replicar VMs do Azure com discos não gerenciados nas contas de armazenamento firewall habilitado para outra região do Azure para recuperação de desastres.<br/><br/> Você pode usar contas de armazenamento habilitado para firewall como contas de armazenamento de destino para discos não gerenciados.<br/><br/> Usando o PowerShell e com suporte no portal.

## <a name="updates-december-2018"></a>Atualizações (dezembro de 2018)

### <a name="automatic-updates-for-the-mobility-service-azure-vms"></a>Atualizações automáticas para o serviço de mobilidade (VMs do Azure)

O Site Recovery adicionou uma opção para atualizações automáticas à extensão do Serviço de Mobilidade. A extensão do Serviço de Mobilidade é instalada em cada VM do Azure replicada pelo Site Recovery. Ao habilitar a replicação, selecione se deseja permitir que o Site Recovery gerencie as atualizações para a extensão.

As atualizações não exigem uma reinicialização de VM e não afetam a replicação. [Saiba mais](azure-to-azure-autoupdate.md).

### <a name="pricing-calculator-for-azure-vm-disaster-recovery"></a>Calculadora de Preços para recuperação de desastre da VM do Azure

Recuperação de desastre de VMs do Azure incorre em custos de licenciamento da VM e os custos de armazenamento e rede. O Azure fornece uma [Calculadora de Preços](https://aka.ms/a2a-cost-estimator) para ajudar você a entender esses custos. O Site Recovery agora fornece uma [estimativa de preço de exemplo](https://aka.ms/a2a-cost-estimator) que precifica uma implantação de exemplo com base em um aplicativo de três camadas usando seis VMs com 12 discos HDD Standard e 6 discos SSD Premium.

- O exemplo supõe que uma alteração de dados de 10 GB por dia para standard e 20 GB para premium.
- Para sua implantação específica, você pode alterar as variáveis para estimar os custos.
- Você pode especificar o número de VMs, o número e o tipo de discos gerenciados e a taxa total esperada de alteração de dados entre as VMs.
- Além disso, você pode aplicar um fator de compactação para estimar os custos de largura de banda.

[Leia](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/) o comunicado.


## <a name="updates-october-2018"></a>Atualizações (outubro de 2018)

### <a name="update-rollup-30"></a>Pacote cumulativo de atualizações 30 

[Atualização cumulativa 30](https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30) fornece as seguintes atualizações.

**Atualização** | **Detalhes**
--- | ---
**Provedores e agentes** | Uma atualização para os agentes de recuperação de Site e provedores (conforme detalhado no pacote cumulativo de atualizações).
**Correções/melhorias dos problemas** | Um número de correções e aprimoramentos (conforme detalhado no pacote cumulativo de atualizações).

### <a name="azure-vm-disaster-recovery"></a>Recuperação de desastres VM do Azure
Recursos adicionados neste mês são resumidas na tabela.

**Recurso** | **Detalhes**
--- | ---
**Suporte de região** | Suporte adicionado para a Austrália Central 1 e na Austrália Central 2 a recuperação de site.
**Suporte para criptografia de disco** | Suporte adicionado para recuperação de desastre de VMs do Azure criptografada com o Azure Disk Encryption (ADE) com o aplicativo do Azure AD. [Saiba mais](azure-to-azure-how-to-enable-replication-ade-vms.md).
**Exclusão de disco** | Discos não inicializados agora são excluídos automaticamente durante a replicação de VM do Azure.
**Habilitado para firewall de armazenamento (PowerShell)** | Suporte adicionado para [contas de armazenamento habilitado para firewall](https://docs.microsoft.com/azure/storage/common/storage-network-security).<br/><br/> Você pode replicar VMs do Azure com discos não gerenciados nas contas de armazenamento firewall habilitado para outra região do Azure para recuperação de desastres.<br/><br/> Você pode usar contas de armazenamento habilitado para firewall como contas de armazenamento de destino para discos não gerenciados.<br/><br/> Suporte para apenas usando o PowerShell.


### <a name="update-rollup-29"></a>Pacote cumulativo de atualizações 29 

[Atualização cumulativa 29](https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery) fornece as seguintes atualizações.

**Atualização** | **Detalhes**
--- | ---
**Provedores e agentes** | Uma atualização para os agentes de recuperação de Site e provedores (conforme detalhado no pacote cumulativo de atualizações).
**Correções/melhorias dos problemas** | Um número de correções e aprimoramentos (conforme detalhado no pacote cumulativo de atualizações).


## <a name="updates-august-2018"></a>Atualizações (agosto de 2018)

### <a name="update-rollup-28"></a>Pacote cumulativo de atualizações 28 

[Atualização cumulativa 28](https://support.microsoft.com/help/4460079/update-rollup-28-for-azure-site-recovery) fornece as seguintes atualizações.

**Atualização** | **Detalhes**
--- | ---
**Provedores e agentes** | Uma atualização para os agentes de recuperação de Site e provedores (conforme detalhado no pacote cumulativo de atualizações).
**Correções/melhorias dos problemas** | Um número de correções e aprimoramentos (conforme detalhado no pacote cumulativo de atualizações).

### <a name="azure-vms-disaster-recovery"></a>Recuperação de desastre de VMs do Azure 
Recursos adicionados neste mês são resumidas na tabela.

**Recurso** | **Detalhes**
--- | ---
**Suporte para Linux** | Adição de suporte para 6.10 de Linux do RedHat Enterprise; CentOS 6.10.<br/><br/>
**Suporte de nuvem** | Suporte para recuperação de desastre para VMs do Azure no Germany cloud.
**Recuperação de desastres entre assinaturas** | Suporte para replicar VMs do Azure em uma região para outra região em uma assinatura diferente, no mesmo locatário do Active Directory do Azure. [Saiba mais](https://aka.ms/cross-sub-blog).

### <a name="vmware-vmphysical-server-disaster-recovery"></a>Recuperação de desastres de VM do VMware/servidor físico 
Recursos adicionados neste mês são resumidas na tabela.

**Recurso** | **Detalhes**
--- | ---
**Suporte para Linux** | Suporte adicionado para 6.10 de Linux RedHat Enterprise, CentOS 6.10.<br/><br/> VMs com base em Linux, que agora há suporte para usar o estilo de partição GUID partição GPT (tabela) no modo de compatibilidade BIOS herdado. Examine os [perguntas frequentes sobre a VM do Azure](https://docs.microsoft.com/azure/virtual-machines/linux/faq-for-disks) para obter mais informações. 
**Recuperação de desastre para VMs após a migração** | Suporte para habilitar a recuperação de desastre para uma região secundária para uma VM do VMware no local migrado para o Azure, sem a necessidade de desinstalar a mobilidade de serviço na máquina virtual antes de habilitar a replicação.
**Windows Server 2008** | Suporte para a migração de máquinas em execução do Windows Server 2008 R2/2008 64 bits e 32 bits.<br/><br/> Somente migração (replicação e failover). Não há suporte para failback.

## <a name="updates-july-2018"></a>Atualizações (julho de 2018)

### <a name="update-rollup-27-july-2018"></a>Pacote cumulativo de atualizações 27 (julho de 2018)

[Atualização cumulativa 27](https://support.microsoft.com/help/4055712/update-rollup-27-for-azure-site-recovery) fornece as seguintes atualizações.

**Atualização** | **Detalhes**
--- | ---
**Provedores e agentes** | Uma atualização para os agentes de recuperação de Site e provedores (conforme detalhado no pacote cumulativo de atualizações).
**Correções/melhorias dos problemas** | Um número de correções e aprimoramentos (conforme detalhado no pacote cumulativo de atualizações).

### <a name="azure-vms-disaster-recovery"></a>Recuperação de desastre de VMs do Azure 

Recursos adicionados neste mês são resumidas na tabela.

**Recurso** | **Detalhes**
--- | ---
**Suporte para Linux** | Suporte adicionado para Red Hat Enterprise Linux 7.5.

### <a name="vmware-vmphysical-server-disaster-recovery"></a>Recuperação de desastres de VM do VMware/servidor físico 

Recursos adicionados neste mês são resumidas na tabela.

**Recurso** | **Detalhes**
--- | ---
**Suporte para Linux** | Suporte adicionado para Red Hat Enterprise Linux 7.5 SUSE Linux Enterprise Server 12.



## <a name="next-steps"></a>Próximas etapas

Mantenha-se atualizado com nossas atualizações na página [Atualizações do Azure](https://azure.microsoft.com/updates/?product=site-recovery).
