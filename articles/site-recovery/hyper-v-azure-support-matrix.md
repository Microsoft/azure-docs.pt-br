---
title: Suporte para recuperação de desastre de VMs do Hyper-V para o Azure com Azure Site Recovery
description: Resume os componentes compatíveis e os requisitos para recuperação de desastre de VM Hyper-V para o Azure com o Azure Site Recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 7/14/2020
ms.author: raynew
ms.openlocfilehash: 8d748f93337a770e0d565bab79fdfb3625bda70d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101735515"
---
# <a name="support-matrix-for-disaster-recovery-of-on-premises-hyper-v-vms-to-azure"></a>Matriz de suporte para recuperação de desastre de VMs do Hyper-V locais para o Azure

Este artigo resume os componentes compatíveis e as configurações de recuperação de desastres de VMs Hyper-V locais para o Azure usando o [Azure Site Recovery](site-recovery-overview.md).

>[!NOTE]
> Site Recovery não move nem armazena os dados do cliente fora da região de destino, na qual a recuperação de desastres foi configurada para os computadores de origem. Os clientes poderão selecionar um cofre dos serviços de recuperação em uma região diferente, se escolherem. O cofre dos serviços de recuperação contém metadados, mas não dados reais do cliente.

## <a name="supported-scenarios"></a>Cenários com suporte

**Cenário** | **Detalhes**
--- | ---
Hyper-V com Virtual Machine Manager <br> <br>| Você pode executar a recuperação de desastres para o Azure para VMs em execução em hosts Hyper-V que são gerenciados na malha do System Center Virtual Machine Manager.<br/><br/> É possível implantar este cenário no portal do Azure ou usando o PowerShell.<br/><br/> Quando hosts Hyper-V são gerenciadas pelo Virtual Machine Manager, você também pode executar a recuperação de desastres em um site local secundário. Para saber mais sobre este cenário, leia [este tutorial](hyper-v-vmm-disaster-recovery.md).
Hyper-V sem Virtual Machine Manager | Você pode executar a recuperação de desastres para o Azure para VMs em execução em hosts Hyper-V que não são gerenciados pelo Virtual Machine Manager.<br/><br/> É possível implantar este cenário no portal do Azure ou usando o PowerShell.

## <a name="on-premises-servers"></a>Servidores locais

**Servidor** | **Requisitos** | **Detalhes**
--- | --- | ---
Hyper-V (executando sem Virtual Machine Manager) |  Windows Server 2019, Windows Server 2016, Windows Server 2012 R2 com as atualizações mais recentes <br/><br/> **Observação:** Também há suporte para a instalação do Server Core desses sistemas operacionais. | Se você já tiver configurado o Windows Server 2012 R2 com/ou o SCVMM 2012 R2 com o Azure Site Recovery e pretender atualizar o sistema operacional, siga as diretrizes na [documentação](upgrade-2012R2-to-2016.md).
Hyper-V (executando sem Virtual Machine Manager) | Virtual Machine Manager 2019, Virtual Machine Manager 2016, Virtual Machine Manager 2012 R2 <br/><br/> **Observação:** Também há suporte para a instalação do Server Core desses sistemas operacionais.  | Se Virtual Machine Manager for usado, os hosts do Windows Server 2019 deverão ser gerenciados em Virtual Machine Manager 2019. Da mesma forma, os hosts do Windows Server 2016 devem ser gerenciados no Virtual Machine Manager 2016.

> [!NOTE]
> Verifique se .NET Framework 4.6.2 ou superior está presente no servidor local.

## <a name="replicated-vms"></a>VMs replicadas


A tabela a seguir resume o suporte de VMs. O Site Recovery é compatível com qualquer carga de trabalho em execução em um sistema operacional compatível.

 **Componente** | **Detalhes**
--- | ---
Configuração da VM | VMs que são replicadas para o Azure devem atender aos [requisitos do Azure](#azure-vm-requirements).
Sistema operacional convidado | Qualquer SO convidado [com suporte para Azure](../cloud-services/cloud-services-guestos-update-matrix.md#family-5-releases).<br/><br/> O Windows Server 2016 Nano Server não é compatível.


## <a name="vmdisk-management"></a>Gerenciamento de VM/disco

**Ação** | **Detalhes**
--- | ---
Redimensionar o disco na VM replicada do Hyper-V | Sem suporte. Desabilite a replicação, faça a alteração e reabilite a replicação para a VM.
Adicionar disco na VM replicada do Hyper-V | Sem suporte. Desabilite a replicação, faça a alteração e reabilite a replicação para a VM.

## <a name="hyper-v-network-configuration"></a>Configuração de rede Hyper-V

**Componente** | **Hyper-V com Virtual Machine Manager** | **Hyper-V sem Virtual Machine Manager**
--- | --- | ---
Rede do host: Agrupamento NIC | Sim | Sim
Rede do host: VLAN | Sim | Sim
Rede do host: IPv4 | Sim | Sim
Rede do host: IPv6 | Não | Não
Rede da VM convidada: Agrupamento NIC | Não | Não
Rede da VM convidada: IPv4 | Sim | Sim
Rede da VM convidada: IPv6 | Não | Sim
Rede da VM convidada: IP estático (Windows) | Sim | Sim
Rede da VM convidada: IP estático (Linux) | Não | Não
Rede da VM convidada: Multi-NIC | Sim | Sim
Proxy HTTPS | Não | Não
Acesso de link privado ao serviço de Site Recovery | Sim. [Saiba mais](hybrid-how-to-enable-replication-private-endpoints.md). | Sim. [Saiba mais](hybrid-how-to-enable-replication-private-endpoints.md).




## <a name="azure-vm-network-configuration-after-failover"></a>Configuração de rede da VM do Azure (após failover)

**Componente** | **Hyper-V com Virtual Machine Manager** | **Hyper-V sem Virtual Machine Manager**
--- | --- | ---
Azure ExpressRoute | Sim | Sim
ILB | Sim | Sim
ELB | Sim | Sim
Gerenciador de Tráfego do Azure | Sim | Sim
NIC múltipla | Sim | Sim
IP Reservado | Sim | Sim
IPv4 | Sim | Sim
Manter endereço IP de origem | Sim | Sim
Pontos de extremidade de serviço de Rede Virtual do Azure<br/> (sem Firewalls de Armazenamento do Azure) | Sim | Sim
Rede Acelerada | Não | Não


## <a name="hyper-v-host-storage"></a>Armazenamento de host do Hyper-V

**Storage** | **Hyper-V com Virtual Machine Manager** | **Hyper-V sem Virtual Machine Manager**
--- | --- | --- 
NFS | NA | NA
SMB 3.0 | Sim | Sim
SAN (ISCSI) | Sim | Sim
Múltiplos caminhos (MPIO). Testado com:<br></br> Microsoft DSM, EMC PowerPath 5,7 SP4, EMC PowerPath DSM para CLARiiON | Sim | Sim

## <a name="hyper-v-vm-guest-storage"></a>Armazenamento de convidado da VM do Hyper-V

**Storage** | **Hyper-V com Virtual Machine Manager** | **Hyper-V sem Virtual Machine Manager**
--- | --- | ---
VMDK | NA | NA
VHD/VHDX | Sim | Sim
VM geração 2 | Sim | Sim
EFI/UEFI<br></br>A VM migrada no Azure será convertida automaticamente em uma VM de inicialização do BIOS. A VM deve estar executando apenas o Windows Server 2012 e posterior. O disco do sistema operacional deve ter até cinco partições ou menos e o tamanho do disco do sistema operacional deve ser inferior a 300 GB.| Sim | Sim
Disco de cluster compartilhado | Não | Não
Disco criptografado | Não | Não
NFS | NA | NA
SMB 3.0 | Não | Não
RDM | NA | NA
Disco >1 TB | Sim, até 4.095 GB | Sim, até 4.095 GB
Disco: setor de lógica e física de 4K | Não compatível: Gen 1/Gen 2 | Não compatível: Gen 1/Gen 2
Disco: 4K lógico e 512-bytes de setor físico | Sim |  Sim
Gerenciamento de volumes lógicos (LVM). Há suporte para o LVM para discos de dados somente. As VMs do Azure tem apenas um único disco de sistema operacional. | Sim | Sim
Volume com discos distribuídos >1 TB | Sim | Sim
Espaços de Armazenamento | Não | Não
Adição/remoção de disco a quente | Não | Não
Exclusão de disco | Sim | Sim
Múltiplos caminhos (MPIO) | Sim | Sim

## <a name="azure-storage"></a>Armazenamento do Azure

**Componente** | **Hyper-V com Virtual Machine Manager** | **Hyper-V sem Virtual Machine Manager**
--- | --- | ---
Armazenamento com redundância local | Sim | Sim
Armazenamento com redundância geográfica | Sim | Sim
Armazenamento com redundância geográfica com acesso de leitura | Sim | Sim
Armazenamento com redundância de zona | Não | Não
Armazenamento frio | Não | Não
Armazenamento quente| Não | Não
Blobs de bloco | Não | Não
Criptografia em repouso (SSE)| Sim | Sim
Criptografia em repouso (CMK) <br></br> (Somente para failover em discos gerenciados)| Sim (por meio do PowerShell AZ 3.3.0 Module em diante) | Sim (por meio do PowerShell AZ 3.3.0 Module em diante)
Criptografia dupla em repouso <br></br> (Somente para failover em discos gerenciados) <br></br> Saiba mais sobre as regiões com suporte para [Windows](../virtual-machines/disk-encryption.md) e [Linux](../virtual-machines/disk-encryption.md) | Sim (por meio do PowerShell AZ 3.3.0 Module em diante) | Sim (por meio do PowerShell AZ 3.3.0 Module em diante)
Armazenamento Premium | Sim | Sim
Armazenamento Standard | Sim | Sim
Serviço de importação/exportação | Não | Não
Contas de armazenamento do Azure com firewall habilitado | Sim. Para armazenamento e cache de destino. | Sim. Para armazenamento e cache de destino.
Modificar a conta de armazenamento | Não. A conta de armazenamento do Azure de destino não pode ser modificada depois de habilitar a replicação. Para modificar, desabilitar e habilitar novamente a recuperação de desastres. | Não
Opção de transferência segura | Sim | Sim


## <a name="azure-compute-features"></a>Recursos de computação do Azure

**Recurso** | **Hyper-V com Virtual Machine Manager** | **Hyper-V sem Virtual Machine Manager**
--- | --- | ---
Conjuntos de disponibilidade | Sim | Sim
HUB | Sim | Sim  
Discos gerenciados | Sim, para failover.<br/><br/> O failback de discos gerenciados não é compatível. | Sim, para failover.<br/><br/> O failback de discos gerenciados não é compatível.

## <a name="azure-vm-requirements"></a>Requisitos de VM do Azure

VMs locais que são replicados para o Azure devem atender aos requisitos de VM do Azure resumidos nesta tabela.

**Componente** | **Requisitos** | **Detalhes**
--- | --- | ---
Sistema operacional convidado | O Site Recovery é compatível com todos os sistemas operacionais que têm [suporte do Azure](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc794868(v=ws.10)).  | A verificação de pré-requisitos falha quando não há suporte para ela.
Arquitetura do sistema operacional convidado | 32 bits (Windows Server 2008)/64-bit | A verificação de pré-requisitos falha quando não há suporte para ela.
Tamanho do disco do sistema operacional | Até 2.048 GB para máquinas virtuais de Geração 1.<br/><br/> Até 300 GB para máquinas virtuais de Geração 2.  | A verificação de pré-requisitos falha quando não há suporte para ela.
Contagem do disco do sistema operacional | 1 | A verificação de pré-requisitos falha quando não há suporte para ela.
Contagem de disco de dados | 16 ou menos  | A verificação de pré-requisitos falha quando não há suporte para ela.
Tamanho do VHD do disco de dados | Até 4.095 GB | A verificação de pré-requisitos falha quando não há suporte para ela.
Adaptadores de rede | Há suporte para vários adaptadores |
VHD compartilhado | Sem suporte | A verificação de pré-requisitos falha quando não há suporte para ela.
Disco FC | Sem suporte | A verificação de pré-requisitos falha quando não há suporte para ela.
Formato de disco rígido | VHD  <br/><br/>  VHDX | O Site Recovery converterá automaticamente o VHDX em VHD ao realizar o failover para o Azure. Quando você executa o failback para o local, as máquinas virtuais continuam a usar o formato VHDX.
BitLocker | Sem suporte | O Bitlocker deve ser desabilitado antes de habilitar a replicação para uma VM.
Nome da VM | Entre 1 e 63 caracteres. Restrito a letras, números e hifens. O nome da VM deve começar e terminar com uma letra ou um número. | Atualize o valor nas propriedades da VM no Site Recovery.
Tipo de VM | Geração 1<br/><br/> Geração 2--Windows | VMs da Geração 2 com um tipo de disco básico de SO (que inclui um ou dois volumes de dados formatados como VHDX) e suporte para menos de 300 GB de espaço em disco.<br></br>Não há suporte para VMs Linux da Geração 2. [Saiba mais](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/).|

## <a name="recovery-services-vault-actions"></a>Ações de cofre dos Serviços de Recuperação

**Ação** |  **Hyper-V com o VMM** | **Hyper-V sem VMM**
--- | --- | ---
Mover cofre entre grupos de recursos<br/><br/> Dentro e entre as assinaturas | Não | Não
Mover armazenamento, rede, VMs do Azure entre grupos de recursos<br/><br/> Dentro e entre as assinaturas | Não | Não

> [!NOTE]
> Ao replicar Hyper-VMs do local para o Azure, você pode replicar para apenas um locatário do AD de um ambiente específico – site Hyper-V ou Hyper-V com o VMM, conforme aplicável.


## <a name="provider-and-agent"></a>Provedor e agente

Para certificar-se de que a implantação seja compatível com as configurações neste artigo, verifique se você está executando as versões de agente e provedor mais recentes.

**Nome** | **Descrição** | **Detalhes**
--- | --- | --- 
Provedor do Azure Site Recovery | Coordena as comunicações entre servidores locais e o Azure <br/><br/> Hyper-V com o Virtual Machine Manager: instalados nos servidores do Virtual Machine Manager<br/><br/> Hyper-V sem o Virtual Machine Manager: instalado nos hosts Hyper-V| Versão mais recente: 5.1.2700.1 (disponível no portal do Azure)<br/><br/> [Recursos e correções mais recentes](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery)
Agente dos Serviços de Recuperação do Microsoft Azure | Coordena a replicação entre VMs Hyper-V e o Azure<br/><br/> Instalado em servidores Hyper-V locais (com ou sem Virtual Machine Manager) | Agente mais recente disponível no portal






## <a name="next-steps"></a>Próximas etapas
Saiba como [preparar o Azure](tutorial-prepare-azure.md) para a recuperação de desastre de VMs locais do Hyper-V.