---
title: "Matriz de suporte do Azure Site Recovery para replicação no Azure | Microsoft Docs"
description: Resume os sistemas operacionais e componentes com suporte para o Azure Site Recovery
services: site-recovery
documentationcenter: 
author: Rajani-Janaki-Ram
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/04/2017
ms.author: rajanaki
ms.openlocfilehash: b4bb049577feb416f498a428bae80ecea418180e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-site-recovery-support-matrix-for-replicating-from-on-premises-to-azure"></a>Matriz de suporte do Azure Site Recovery para replicação do local para o Azure


Este artigo resume os componentes e as configurações com suporte do Azure Site Recovery durante a replicação e recuperação no Azure. Para saber mais sobre os requisitos do Azure Site Recovery, confira os [pré-requisitos](site-recovery-prereq.md).

> [!NOTE]
> Atualize para a versão mais recente do provedor e do agente de Site Recovery para obter compatibilidade com as atualizações na matriz de suporte.


## <a name="support-for-deployment-options"></a>Suporte para opções de implantação

**Implantação** | **Servidor VMware/físico** | **Hyper-V (com/sem o Virtual Machine Manager)** |
--- | --- | ---
**Portal do Azure** | VMs VMware locais para armazenamento do Azure, com redes e armazenamento clássicos ou do Azure Resource Manager.<br/><br/> Failover em VMs clássicas ou baseadas no Resource Manager. | VMs do Hyper-V locais para o Armazenamento do Azure, com armazenamento e redes clássicas ou do Resource Manager.<br/><br/> Failover em VMs clássicas ou baseadas no Resource Manager.
**Portal clássico** | Somente modo de manutenção. Não é possível criar novos cofres. | Somente modo de manutenção.
**PowerShell** | Não há suporte no momento. | Suportado


## <a name="support-for-datacenter-management-servers"></a>Suporte para servidores de gerenciamento do datacenter

### <a name="virtualization-management-entities"></a>Entidades de gerenciamento de virtualização

**Implantação** | **Suporte**
--- | ---
**Servidor da VM VMware/físico** | vCenter 6.5, 6.0 ou 5.5
**Hyper-V (com Virtual Machine Manager)** | System Center Virtual Machine Manager 2016 e System Center Virtual Machine Manager 2012 R2

  >[!Note]
  > No momento, não há suporte para uma nuvem do System Center Virtual Machine Manager 2016 com uma combinação de hosts do Windows Server 2016 e 2012 R2.
  > As configurações que incluem o upgrade de um SCVMM 2012 R2 existente para 2016 não são suportadas atualmente.
### <a name="host-servers"></a>Servidores de host

**Implantação** | **Suporte**
--- | ---
**Servidor da VM VMware/físico** | vSphere 6.5, 6.0, 5.5
**Hyper-V (com/sem o Virtual Machine Manager)** | Windows Server 2016, Windows Server 2012 R2 com as atualizações mais recentes.<br></br>Se for usado o SCVMM, os hosts do Windows Server 2016 deverão ser gerenciados pelo SCVMM 2016.


  >[!Note]
  >No momento, não há suporte para um site do Hyper-V que combine hosts que executam o Windows Server 2016 e 2012 R2. Atualmente, não há suporte para recuperação de VMs em um host do Windows Server 2016 para um local alternativo.

## <a name="support-for-replicated-machine-os-versions"></a>Suporte para versões do SO do computador replicado

As máquinas virtuais protegidas devem atender aos [requisitos do Azure](#failed-over-azure-vm-requirements) ao replicar no Azure.
A tabela a seguir resume o suporte a sistemas operacionais replicados em vários cenários de implantação durante o uso do Azure Site Recovery. Esse suporte é aplicável a qualquer carga de trabalho em execução no SO mencionado.

 **Servidor VMware/físico** | **Hyper-V (com/sem o VMM)** |
--- | --- |
Windows Server 2012 R2 de 64 bits, Windows Server 2012, Windows Server 2008 R2 com pelo menos SP1<br/>*Windows Server 2016* – sem suporte no momento em máquinas virtuais VMware e servidores físicos. <br/><br/> Red Hat Enterprise Linux: 5.2 a 5.11, 6.1 a 6.9, 7.0 a 7.3 <br/><br/>CentOS: 5.2 a 5.11, 6.1 a 6.9, 7.0 a 7.3 <br/><br/>Servidor do Ubuntu 14.04 LTS[ (suporte para versões de kernel)](#supported-ubuntu-kernel-versions-for-vmwarephysical-servers)<br/><br/>Servidor LTS do Ubuntu 16.04[ (versões de kernel com suporte)](#supported-ubuntu-kernel-versions-for-vmwarephysical-servers)<br/><br/>Debian 7 <br/><br/>Debian 8<br/><br/>Oracle Enterprise Linux 6.4, 6.5 que executa o kernel compatível com Red Hat ou o UEK3 (Unbreakable Enterprise Kernel Versão 3) <br/><br/>SUSE Linux Enterprise Server 11 SP3 <br/><br/>SUSE Linux Enterprise Server 11 SP4 <br/>(Não há suporte para a atualização de computadores de replicação de SLES 11 SP3 até SLES 11 SP4. Se um computador replicado tiver sido atualizado do SLES 11 SP3 para o SLES 11 SP4, você precisará desabilitar a replicação e proteger o computador novamente após a atualização). | Qualquer SO convidado [com suporte do Azure](https://technet.microsoft.com/library/cc794868.aspx)


>[!IMPORTANT]
>(Aplicável a servidores VMware/físicos replicando no Azure)
>
> No Servidor Red Hat Enterprise Linux 7+ e em servidores CentOS 7+, o kernel versão 3.10.0-514 tem suporte a partir da versão 9.8 do serviço de mobilidade do Azure Site Recovery.<br/><br/>
> Os clientes no kernel 3.10.0-514 com uma versão do serviço de mobilidade inferior à versão 9.8 terão que desabilitar a replicação, atualizar a versão do serviço de mobilidade para versão 9.8 e habilitar a replicação novamente.


### <a name="supported-ubuntu-kernel-versions-for-vmwarephysical-servers"></a>Versões com suporte do kernel Ubuntu para servidores VMware/físicos

**Versão** | **Versão de serviço de mobilidade** | **Versão do kernel** |
--- | --- | --- |
14.04 LTS | 9.9 | 3.13.0-24-Generic para 3.13.0-117-generic,<br/>3.16.0-25-generic para 3.16.0-77-generic,<br/>3.19.0-18-generic para 3.19.0-80-generic,<br/>4.2.0-18-generic para 4.2.0-42-generic,<br/>4.4.0-21-generic para 4.4.0-75-generic |
14.04 LTS | 9.10 | 3.13.0-24-generic a 3.13.0-121-generic,<br/>3.16.0-25-generic para 3.16.0-77-generic,<br/>3.19.0-18-generic para 3.19.0-80-generic,<br/>4.2.0-18-generic para 4.2.0-42-generic,<br/>4.4.0-21-generic a 4.4.0-81-generic |
14.04 LTS | 9.11 | 3.13.0-24-generic a 3.13.0-128-generic,<br/>3.16.0-25-generic para 3.16.0-77-generic,<br/>3.19.0-18-generic para 3.19.0-80-generic,<br/>4.2.0-18-generic para 4.2.0-42-generic,<br/>4.4.0-21-generic a 4.4.0-91-generic |
16.04 LTS | 9.10 | 4.4.0-21-generic a 4.4.0-81-generic,<br/>4.8.0-34-generic a 4.8.0-56-generic,<br/>4.10.0-14-generic a 4.10.0-24-generic |
16.04 LTS | 9.11 | 4.4.0-21-generic a 4.4.0-91-generic,<br/>4.8.0-34-generic a 4.8.0-58-generic,<br/>4.10.0-14-generic a 4.10.0-32-generic |

## <a name="supported-file-systems-and-guest-storage-configurations-on-linux-vmwarephysical-servers"></a>Sistemas de arquivos e configurações de armazenamento de convidado com suporte no Linux (servidores VMware/físicos)

Os seguintes sistemas de arquivos e software de configuração de armazenamento têm suporte em servidores Linux em execução nos servidores VMware ou físicos:
* Sistemas de arquivos: ext3, ext4, ReiserFS (somente Suse Linux Enterprise Server), XFS
* Gerenciador de volumes: LVM2
* Software Multipath: Device Mapper

Não há suporte para dispositivos de armazenamento paravirtualizados (dispositivos exportados por drivers paravirtualizados).<br/>
Não há suporte para dispositivos de E/S de bloco de várias filas.<br/>
Não há suporte para servidores físicos com o controlador de armazenamento CCISS da HP.<br/>

>[!Note]
> Em servidores Linux, os diretórios a seguir (se configurados como partições/sistemas de arquivos separados) devem estar no mesmo disco (disco do sistema operacional) no servidor de origem: / (raiz), /boot, /usr, /usr/local, /var e /etc<br/><br/>
> Recursos de XFSv5 em sistemas de arquivos XFS, como soma de verificação de metadados, têm suporte da versão 9.10 em diante do Serviço de Mobilidade. Se você estiver usando recursos XFSv5, execute o Serviço de Mobilidade versão 9.10 ou mais recente. Você pode usar o utilitário xfs_info para verificar o super-bloco XFS da partição. Se ftype está definido como 1, os recursos XFSv5 estão sendo usados.
>


## <a name="support-for-network-configuration"></a>Suporte para configuração da rede
As tabelas a seguir resumem o suporte à configuração de rede em vários cenários de implantação que usam o Azure Site Recovery para replicar no Azure.

### <a name="host-network-configuration"></a>Configuração de rede do host

**Configuração** | **Servidor VMware/físico** | **Hyper-V (com/sem o Virtual Machine Manager)**
--- | --- | ---
Agrupamento NIC | Sim<br/><br/>Não há suporte quando computadores físicos são replicados| Sim
VLAN | Sim | Sim
IPv4 | Sim | Sim
IPv6 | Não | Não

### <a name="guest-vm-network-configuration"></a>Configuração de rede da VM convidada

**Configuração** | **Servidor VMware/físico** | **Hyper-V (com/sem o Virtual Machine Manager)**
--- | --- | ---
Agrupamento NIC | Não | Não
IPv4 | Sim | Sim
IPv6 | Não | Não
IP estático (Windows) | Sim | Sim
IP estático (Linux) | Sim <br/><br/>Máquinas virtuais são configuradas para usar DHCP em failback  | Não
NIC múltipla | Sim | Sim

### <a name="failed-over-azure-vm-network-configuration"></a>Configuração de rede da VM do Azure após failover

**Rede do Azure** | **Servidor VMware/físico** | **Hyper-V (com/sem o Virtual Machine Manager)**
--- | --- | ---
ExpressRoute | Sim | Sim
ILB | Sim | Sim
ELB | Sim | Sim
Gerenciador de Tráfego | Sim | Sim
NIC múltipla | Sim | Sim
IP Reservado | Sim | Sim
IPv4 | Sim | Sim
Reter o IP de origem | Sim | Sim


## <a name="support-for-storage"></a>Suporte para armazenamento
As tabelas a seguir resumem o suporte à configuração de armazenamento em vários cenários de implantação que usam o Azure Site Recovery para replicar no Azure.

### <a name="host-storage-configuration"></a>Configuração de armazenamento do host

**Configuração** | **Servidor VMware/físico** | **Hyper-V (com/sem o Virtual Machine Manager)**
--- | --- | --- | ---
NFS | Sim para VMware<br/><br/> Não para servidores físicos | N/D
SMB 3.0 | N/D | Sim
SAN (ISCSI) | Sim | Sim
Múltiplos caminhos (MPIO)<br></br>Testado com: Microsoft DSM, EMC PowerPath 5.7 SP4, EMC PowerPath DSM for CLARiiON | Sim | Sim

### <a name="guest-or-physical-server-storage-configuration"></a>Configuração de armazenamento do servidor físico ou convidado

**Configuração** | **Servidor VMware/físico** | **Hyper-V (com/sem o Virtual Machine Manager)**
--- | --- | ---
VMDK | Sim | N/D
VHD/VHDX | N/D | Sim
VM ger 2 | N/D | Sim
EFI/UEFI| Não | Sim
Disco de cluster compartilhado | Não | Não
Disco criptografado | Não | Não
NFS | Não | N/D
SMB 3.0 | Não | Não
RDM | Sim<br/><br/> N/D para servidores físicos | N/D
Disco > 1 TB | Sim<br/><br/>Até 4.095 GB | Sim<br/><br/>Até 4.095 GB
Disco com tamanho de setor de 4K | Sim | Sim, há suporte para VMs da geração 1<br/><br/>Não há suporte para VMs da geração 2.
Volume com discos distribuídos > 1 TB<br/><br/> Gerenciamento de Volume lógico LVM | Sim | Sim
Espaços de Armazenamento | Não | Sim
Adição/remoção de disco a quente | Não | Não
Exclusão de disco | Sim | Sim
Múltiplos caminhos (MPIO) | N/D | Sim

**Armazenamento do Azure** | **Servidor VMware/físico** | **Hyper-V (com/sem o Virtual Machine Manager)**
--- | --- | ---
LRS | Sim | Sim
GRS | Sim | Sim
RA-GRS | Sim | Sim
Armazenamento frio | Não | Não
Armazenamento quente| Não | Não
Criptografia em repouso (SSE)| Sim | Sim
Armazenamento Premium | Sim | Sim
Serviço de importação/exportação | Não | Não


## <a name="support-for-azure-compute-configuration"></a>Suporte para configuração de computação do Azure

**Recurso de computação** | **Servidor VMware/físico** | **Hyper-V (com/sem o Virtual Machine Manager)**
--- | --- | ---
Conjuntos de disponibilidade | Sim | Sim
HUB | Sim | Sim  
Discos gerenciados | Sim | Sim<br/><br/>No momento, não há suporte para failback para o local da VM do Azure com discos gerenciados.

## <a name="failed-over-azure-vm-requirements"></a>Requisitos de VM do Azure após failover

Você pode implantar a Recuperação de Site para replicar máquinas virtuais e servidores físicos executando qualquer sistema operacional com suporte do Azure. Isso inclui a maioria das versões do Windows e do Linux. As VMs locais que você deseja replicar devem estar em conformidade com os requisitos do Azure a seguir durante a replicação no Azure.

**Entidade** | **Requisitos** | **Detalhes**
--- | --- | ---
**Sistema operacional convidado** | Replicação do Hyper-V para o Azure: a Recuperação de Site dá suporte para todos os sistemas operacionais [com suporte no Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx). <br/><br/> Para replicação de VMware e servidor físico: verifique os [pré-requisitos](site-recovery-vmware-to-azure-classic.md) | A verificação de pré-requisitos falhará se não houver suporte.
**Arquitetura do sistema operacional convidado** | 64 bits | A verificação de pré-requisitos falhará se não houver suporte
**Tamanho de disco do sistema operacional** | Até 2.048 GB se você estiver replicando **VMs VMware ou servidores físicos para o Azure**.<br/><br/>Até 2.048 GB para VMs **Hyper-V Geração 1**.<br/><br/>Até 300 GB para VMs **Hyper-V Geração 2**.  | A verificação de pré-requisitos falhará se não houver suporte
**Contagem de discos do sistema operacional** | 1 | A verificação de pré-requisitos falhará se não houver suporte.
**Contagem de discos de dados** | 64 ou menos se você estiver replicando **VMs do VMware no Azure**; 16 ou menos se você estiver replicando **VMs do Hyper-V no Azure** | A verificação de pré-requisitos falhará se não houver suporte
**Tamanho do VHD do disco de dados** | Até 4.095 GB | A verificação de pré-requisitos falhará se não houver suporte
**Adaptadores de rede** | Há suporte para vários adaptadores |
**VHD compartilhado** | Sem suporte | A verificação de pré-requisitos falhará se não houver suporte
**Disco FC** | Sem suporte | A verificação de pré-requisitos falhará se não houver suporte
**Formato de disco rígido** | VHD  <br/><br/> VHDX | Apesar de atualmente o VHDX não ter suporte no Azure, a Recuperação de Site converterá automaticamente o VHDX em VHD ao realizar o failover para o Azure. Quando você executa o failback para o local, as máquinas virtuais continuam a usar o formato VHDX.
**Bitlocker** | Sem suporte | O BitLocker deve ser desabilitado antes de proteger uma máquina virtual.
**Nome da VM** | Entre 1 e 63 caracteres. Restrito a letras, números e hifens. O nome da VM deve começar e terminar com uma letra ou um número. | Atualize o valor nas propriedades da máquina virtual no Site Recovery.
**Tipo de VM** | Geração 1<br/><br/> Geração 2 - Windows | VMs da Geração 2 com um tipo de disco básico de SO (que inclui um ou dois volumes de dados formatados como VHDX) e suporte para menos de 300 GB de espaço em disco.<br></br>Não há suporte para VMs Linux da Geração 2. [Saiba mais](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/)|

## <a name="support-for-recovery-services-vault-actions"></a>Suporte para ações do cofre dos Serviços de Recuperação

**Ação** | **Servidor VMware/físico** | **Hyper-V (sem Virtual Machine Manager)** | **Hyper-V (com Virtual Machine Manager)**
--- | --- | --- | ---
Mover cofre entre grupos de recursos<br/><br/> Dentro e entre as assinaturas | Não | Não | Não
Mover armazenamento, rede, VMs do Azure entre grupos de recursos<br/><br/> Dentro e entre as assinaturas | Não | Não | Não


## <a name="support-for-provider-and-agent"></a>Suporte para o Provedor e o Agente

**Nome** | **Descrição** | **Última versão** | **Detalhes**
--- | --- | --- | --- | ---
**Provedor do Azure Site Recovery** | Coordena as comunicações entre servidores locais e o Azure <br/><br/> Instalado em servidores locais do Virtual Machine Manager ou em servidores Hyper-V, se não houver nenhum servidor do Virtual Machine Manager | 5.1.19 ([disponível no portal](http://aka.ms/downloaddra)) | [Recursos e correções mais recentes](https://support.microsoft.com/kb/3155002)
**Configuração unificada do Azure Site Recovery (VMware para Azure)** | Coordena as comunicações entre servidores VMware locais e o Azure  <br/><br/> Instalado nos servidores VMware no locais | 9.3.4246.1 (disponível no portal) | [Recursos e correções mais recentes](https://support.microsoft.com/kb/3155002)
**Serviço de mobilidade** | Coordena a replicação entre servidores VMware/servidores físicos locais e o Azure/site secundário<br/><br/> Instalado na VM VMware ou nos servidores físicos que você deseja replicar  | N/D (disponível no portal) | N/D
**Agente de MARS (Serviços de Recuperação do Microsoft Azure)** | Coordena a replicação entre VMs Hyper-V e o Azure<br/><br/> Instalado em servidores Hyper-V locais (com ou sem um servidor do Virtual Machine Manager) | Agente mais recente ([disponível no portal](http://aka.ms/latestmarsagent)) |






## <a name="next-steps"></a>Próximas etapas
[Verificar pré-requisitos](site-recovery-prereq.md)
