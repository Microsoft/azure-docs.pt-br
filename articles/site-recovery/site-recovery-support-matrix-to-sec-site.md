---
title: "Suporte à matriz para replicação em um site secundário com o Azure Site Recovery | Microsoft Docs"
description: Resume os sistemas operacionais e componentes com suporte para o Azure Site Recovery
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 05/24/2017
ms.author: raynew
ms.openlocfilehash: 69c5d09b6608484210870e1a69c51b112b497810
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="support-matrix-for-replication-to-a-secondary-site-with-azure-site-recovery"></a>Suporte a matriz para replicação em um site secundário com o Azure Site Recovery

Este artigo resume o que tem suporte quando você usa o Azure Site Recovery para replicar em um site local secundário.

## <a name="deployment-options"></a>Opções de implantação

**Implantação** | **Servidor VMware/físico** | **Hyper-V (com/sem o SCVMM)**
--- | --- | --- | ---
**Portal do Azure** | VMs do VMware locais para o site secundário do VMware.<br/><br/> Baixe o [guia do usuário do InMage Scout](http://download.microsoft.com/download/E/0/8/E08B3BCE-3631-4CED-8E65-E3E7D252D06D/InMage_Scout_Standard_User_Guide_8.0.1.pdf) (não disponível no Portal do Azure). | VMs do Hyper-V locais em nuvens de VMM para uma nuvem de VMM secundária.<br></br> Sem suporte sem o SCVMM  <br/><br/> Somente Replicação do Hyper-V padrão. Sem suporte para SAN.
**Portal clássico** | Somente modo de manutenção. Não é possível criar novos cofres. | Somente no modo de manutenção<br></br> Sem suporte sem o SCVMM
**PowerShell** | Sem suporte | Suportado<br></br> Sem suporte sem o SCVMM

## <a name="on-premises-servers"></a>Servidores locais

### <a name="virtualization-servers"></a>Servidores de virtualização

**Implantação** | **Suporte**
--- | ---
**Servidor da VM VMware/físico** | vSphere 6.0, 5.5 ou 5.1 com a atualização mais recente
**Hyper-V (com o VMM)** | VMM 2016 e VMM 2012 R2

  >[!Note]
  > No momento, não há suporte para nuvens VMM 2016 com uma combinação de hosts do Windows Server 2016 e 2012 R2.
  > As configurações que incluem o upgrade de um SCVMM 2012 R2 existente para 2016 não são suportadas atualmente.
### <a name="host-servers"></a>Servidores de host

**Implantação** | **Suporte**
--- | ---
**Servidor da VM VMware/físico** | vCenter 5.5 ou 6.0 (suporte somente a recursos do 5.5) 
**Hyper-V (sem VMM)** | Não é uma configuração com suporte de replicação para um site secundário
**Hyper-V com o VMM** | Windows Server 2016 e Windows Server 2012 R2 com as atualizações mais recentes.<br/><br/> Os hosts do Windows Server 2016 devem ser gerenciados pelo VMM 2016.

## <a name="support-for-replicated-machine-os-versions"></a>Suporte para versões do SO do computador replicado
A tabela a seguir resume o suporte a sistemas operacionais replicados em vários cenários de implantação encontrados durante o uso do Azure Site Recovery. Esse suporte é aplicável a qualquer carga de trabalho em execução no SO mencionado.

**Servidor VMware/físico** | **Hyper-V (com o VMM)**
--- | --- | ---
Windows Server 2012 R2 de 64 bits, Windows Server 2012, Windows Server 2008 R2 com pelo menos SP1<br/><br/> Red Hat Enterprise Linux 6.7, 7.1 e 7.2 <br/><br/> Centos 6.5, 6.6, 6.7, 7.0, 7.1, 7.2 <br/><br/> Oracle Enterprise Linux 6.4 ou 6.5 que executa o kernel compatível com Red Hat ou o UEK3 (Unbreakable Enterprise Kernel Versão 3) <br/><br/> SUSE Linux Enterprise Server 11 SP3 | Qualquer sistema operacional convidado [com suporte do Hyper-V](https://technet.microsoft.com/library/mt126277.aspx)

>[!Note]
>Somente os computadores Linux com o armazenamento a seguir podem ser replicados: sistema de arquivos (EXT3, ETX4, ReiserFS, XFS); Multipath software-device Mapper; Gerenciador de volumes: (LVM2).
>Não há suporte a servidores físicos com o armazenamento de controlador HP CCISS.
>O sistema de arquivos ReiserFS só tem suporte no SUSE Linux Enterprise Server 11 SP3.

## <a name="network-configuration"></a>Configuração de rede

### <a name="hosts"></a>Hosts

**Configuração** | **Servidor VMware/físico** | **Hyper-V (com o VMM)**
--- | --- | ---
Agrupamento NIC | Sim | Sim
VLAN | Sim | Sim
IPv4 | Sim | Sim
IPv6 | Não | Não

### <a name="guest-vms"></a>VMs convidadas

**Configuração** | **Servidor VMware/físico** | **Hyper-V (com o VMM)**
--- | --- | ---
Agrupamento NIC | Não | Não
IPv4 | Sim | Sim
IPv6 | Não | Não
IP estático (Windows) | Sim | Sim
IP estático (Linux) | Sim | Sim
NIC múltipla | Sim | Sim


## <a name="storage"></a>Armazenamento

### <a name="host-storage"></a>Armazenamento de host

**Armazenamento (host)** | **Servidor VMware/físico** | **Hyper-V (com o VMM)**
--- | --- | ---
NFS | Sim | N/D
SMB 3.0 | N/D | Sim
SAN (ISCSI) | Sim | Sim
Múltiplos caminhos (MPIO) | Sim | Sim

### <a name="guest-or-physical-server-storage"></a>Armazenamento do servidor físico ou convidado

**Configuração** | **Servidor VMware/físico** | **Hyper-V (com o VMM)**
--- | --- | ---
VMDK | Sim | N/D
VHD/VHDX | N/D | Sim (até 16 discos)
VM ger 2 | N/D | Sim
Disco de cluster compartilhado | Sim  | Não
Disco criptografado | Não | Não
UEFI| Não | N/D
NFS | Não | Não
SMB 3.0 | Não | Não
RDM | Sim | N/D
Disco > 1 TB | Não | Sim
Volume com discos distribuídos > 1 TB<br/><br/> LVM | Sim | Sim
Espaços de Armazenamento | Não | Sim
Adição/remoção de disco a quente | Não | Não
Exclusão de disco | Não | Sim
Múltiplos caminhos (MPIO) | N/D | Sim

## <a name="vaults"></a>Cofres

**Ação** | **Servidor VMware/físico** | **Hyper-V (com o VMM)**
--- | --- | ---
Mover cofres entre grupos de recursos (dentro de uma assinatura ou entre assinaturas) | Não | Não
Mover armazenamento, rede, VMs do Azure entre grupos de recursos (dentro de uma assinatura ou entre assinaturas) | Não | Não

## <a name="provider-and-agent"></a>Provedor e agente

**Nome** | **Descrição** | **Última versão** | **Detalhes**
--- | --- | --- | --- | ---
**Provedor do Azure Site Recovery** | Coordena as comunicações entre servidores locais e o Azure <br/><br/> Instalado em servidores VMM locais ou servidores Hyper-V, se não houver nenhum servidor VMM | 5.1.19 ([disponível no portal](http://aka.ms/downloaddra)) | [Recursos e correções mais recentes](https://support.microsoft.com/kb/3155002)
**Serviço de mobilidade** | Coordena a replicação entre servidores VMware locais ou servidores físicos e o site secundário<br/><br/> Instalado na VM VMware ou nos servidores físicos que você deseja replicar  | N/D (disponível no portal) | N/D


## <a name="next-steps"></a>Próximas etapas

- [Replicar as VMs do Hyper-V em nuvens de VMM para um site secundário](site-recovery-vmm-to-vmm.md)
- [Replicar VMs VMware e servidores físicos para um site secundário](site-recovery-vmware-to-vmware.md)
