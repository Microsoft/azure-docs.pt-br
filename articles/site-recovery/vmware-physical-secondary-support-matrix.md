---
title: Matriz de suporte para recuperação de desastre de VMs VMware ou servidores físicos em um site VMware secundário com o Azure Site Recovery | Microsoft Docs
description: Resume o suporte para recuperação de desastres de VMs VMware e servidores físicos para um site secundário com o Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: article
ms.date: 04/08/2019
ms.author: raynew
ms.openlocfilehash: b316d6a8293d9f23eb89e8b6fffedac316759df4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60564843"
---
# <a name="support-matrix-for-disaster-recovery-of-vmware-vms-and-physical-servers-to-a-secondary-site"></a>Matriz de suporte para recuperação de desastre de VMs VMware e servidores físicos para um site secundário

Este artigo resume o que é suportado quando você usa o serviço [Azure Site Recovery](site-recovery-overview.md) para recuperação de desastre de VMs VMware ou servidores físicos Windows / Linux em um site VMware secundário.

- Se você quiser replicar VMs VMware ou servidores físicos para Azure, analise [esta matriz de suporte](vmware-physical-azure-support-matrix.md).
- Se você quiser replicar VMs Hyper-V para um site secundário, analise [esta matriz de suporte](hyper-v-azure-support-matrix.md).

> [!NOTE]
> A replicação de VMs VMware e servidores físicos locais é fornecida pelo InMage Scout. O InMage Scout está incluído na assinatura para o serviço Azure Site Recovery.


## <a name="host-servers"></a>Servidores de host

**Sistema operacional** | **Detalhes**
--- | ---
Servidor vCenter | vCenter 5.5, 6.0 e 6.5<br/><br/> Se você executar 6.0 ou 6.5, observe que apenas os recursos de 5.5 têm suporte.


## <a name="replicated-vm-support"></a>Suporte de VM replicada

A tabela a seguir resume o suporte de sistema operacional para computadores replicados com o Site Recovery. Qualquer carga de trabalho pode ser executada no sistema operacional com suporte.

**Sistema operacional** | **Detalhes**
--- | ---
Windows Server | Windows Server 2016 R2 de 64 bits, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 com pelo menos SP1.
Linux | Red Hat Enterprise Linux 6.7, 6.8, 6.9, 7.1, 7.2 <br/><br/> CentOS 6.5, 6.6, 6.7, 6.8, 6.9, 7.0, 7.1, 7.2 <br/><br/> Oracle Enterprise Linux 6.4, 6.5, 6.8 que executa Red Hat compatível com kernel ou o Unbreakable Enterprise Kernel Versão 3 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3, 11 SP4 


## <a name="linux-machine-storage"></a>Armazenamento de computador do Linux

Somente computadores Linux com o armazenamento a seguir podem ser replicados:

- Sistema de arquivos (EXT3, ETX4, ReiserFS, XFS).
- Software Multipath – Mapeador de dispositivos.
- Gerenciador de volumes (LVM2).
- Não há suporte a servidores físicos com o armazenamento de controlador HP CCISS.
- O sistema de arquivos ReiserFS só tem suporte no SUSE Linux Enterprise Server 11 SP3.

## <a name="network-configuration---hostguest-vm"></a>Configuração de rede - VM Host/Convidada

**Configuração** | **Com suporte**  
--- | --- 
Host - Agrupamento NIC | Sim 
Host - VLAN | Sim 
Host - IPv4 | Sim 
Host - IPv6 | Não  
VM Convidada - Agrupamento NIC | Não 
VM Convidada - IPv4 | Sim
VM Convidada - IPv6 | Não 
VM convidada – Windows/Linux – Endereço IP estático | Sim
VM Convidada - Multi-NIC | Sim


## <a name="storage"></a>Armazenamento

### <a name="host-storage"></a>Armazenamento de host

**Armazenamento (host)** | **Com suporte** 
--- | --- 
NFS | Sim 
SMB 3.0 | N/D 
SAN (ISCSI) | Sim 
Múltiplos caminhos (MPIO) | Sim 

### <a name="guest-or-physical-server-storage"></a>Armazenamento do servidor físico ou convidado

**Configuração** | **Com suporte** 
--- | --- 
VMDK | Sim 
VHD/VHDX | N/D 
VM ger 2 | N/D 
Disco de cluster compartilhado | Sim 
Disco criptografado | Não  
UEFI| Sim 
NFS | Não  
SMB 3.0 | Não  
RDM | Sim 
Disco > 1 TB | Sim 
Volume com discos distribuídos > 1 TB<br/><br/> LVM | Sim 
Espaços de Armazenamento | Não  
Adição/remoção de disco a quente | Sim 
Exclusão de disco | Sim 
Múltiplos caminhos (MPIO) | N/D 

## <a name="vaults"></a>Cofres

**Ação** | **Com suporte** 
--- | --- 
Mover cofres entre grupos de recursos (dentro de uma assinatura ou entre assinaturas) | Não  
Mover armazenamento, rede, VMs do Azure entre grupos de recursos (dentro de uma assinatura ou entre assinaturas) | Não  

## <a name="mobility-service-and-updates"></a>Serviço de mobilidade e atualizações

O serviço de Mobilidade coordena a replicação entre servidores VMware locais ou servidores físicos e o site secundário. Ao configurar a replicação, certifique-se de que possui a última versão do serviço de mobilidade e de outros componentes.

| **Atualização** | **Detalhes** |
| --- | --- |
|Atualizações do Scout | As atualizações do Scout são cumulativas. <br/><br/> [Saiba mais e baixe](vmware-physical-secondary-disaster-recovery.md#updates) as últimas atualizações do Scout |
|Atualizações de componentes | As atualizações do Scout incluem atualizações para todos os componentes, incluindo o servidor RX, servidor de configuração, servidores de destino mestre e processo, servidores vContinuum e servidores de origem que deseja proteger.<br/><br/> [Saiba mais](vmware-physical-secondary-disaster-recovery.md#download-and-install-component-updates).|


## <a name="next-steps"></a>Próximas etapas

Baixe o [guia do usuário do InMage Scout](https://aka.ms/asr-scout-user-guide)

- [Replicar as VMs do Hyper-V em nuvens de VMM para um site secundário](tutorial-vmm-to-vmm.md)
- [Replicar VMs VMware e servidores físicos para um site secundário](tutorial-vmware-to-vmware.md)
