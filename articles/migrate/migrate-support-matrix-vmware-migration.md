---
title: Suporte para migração do VMware nas migrações para Azure
description: Saiba mais sobre o suporte para migração de VM do VMware nas migrações para Azure.
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: 4fb2ea534954ae6c64d0da2d992ce8b1c8a62c0c
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105557557"
---
# <a name="support-matrix-for-vmware-migration"></a>Matriz de suporte para migração do VMware

Este artigo resume as configurações de suporte e as limitações para migrar VMs VMware com [migrações para Azure: migração de servidor](migrate-services-overview.md#azure-migrate-server-migration-tool) . Se você estiver procurando informações sobre como avaliar VMs VMware para migração para o Azure, examine a [matriz de suporte de avaliação](migrate-support-matrix-vmware.md).


## <a name="migration-options"></a>Opções de migração

Você pode migrar VMs VMware de duas maneiras:

- **Usando a migração sem agente**: migre as máquinas virtuais independentemente de precisar instalar nada nelas. Você implanta o [dispositivo migrações para Azure](migrate-appliance.md) para migração sem agente.
- **Usando a migração baseada em agente**: Instale um agente na VM para replicação. Para a migração baseada em agente, você implanta um [dispositivo de replicação](migrate-replication-appliance.md).

Examine [Este artigo](server-migrate-overview.md) para descobrir qual método você deseja usar.

## <a name="agentless-migration"></a>Migração sem agente 

Esta seção resume os requisitos de migração de VM do VMware sem agente para o Azure.

### <a name="vmware-requirements-agentless"></a>Requisitos do VMware (sem agente)

A tabela resume os requisitos de hipervisor do VMware.

**VMware** | **Detalhes**
--- | ---
**VMware vCenter Server** | Versão 5,5, 6,0, 6,5, 6,7, 7,0.
**VMware vSphere host ESXI** | Versão 5,5, 6,0, 6,5, 6,7, 7,0.
**vCenter Server permissões** | A migração sem agente usa o [dispositivo de migração](migrate-appliance.md). O dispositivo precisa dessas permissões no vCenter Server:<br/><br/> - **Datastore. procurar** (datastore – > procurar armazenamento de datastore): permitir a navegação de arquivos de log da VM para solucionar problemas de criação e exclusão de instantâneos.<br/><br/> - **Armazenamento de arquivos. filemanagement** (> operações de arquivo de nível baixo): permitir operações de leitura/gravação/exclusão/renomeação no navegador do repositório de armazenamento, para solucionar problemas de criação e exclusão de instantâneos.<br/><br/> - **VirtualMachine.Config. ChangeTracking** (máquina virtual-> o controle de alterações do disco): permitir habilitar ou desabilitar o controle de alterações de discos de VM para efetuar pull de blocos de dados alterados entre instantâneos.<br/><br/> - **VirtualMachine.Config. DiskLease** (concessão de disco de > de máquina virtual): permitir operações de concessão de disco para uma VM, ler o disco usando o VMware vSphere kit de desenvolvimento de disco virtual (VDDK).<br/><br/> - **VirtualMachine. Provisioning. DiskRandomRead** (> de provisionamento de máquina Virtual-> permitir acesso de disco somente leitura): permitir a abertura de um disco em uma VM, para ler o disco usando o VDDK.<br/><br/> - **VirtualMachine. Provisioning. DiskRandomAccess** (> de provisionamento de máquina Virtual-> permitir acesso ao disco): permitir a abertura de um disco em uma VM, para ler o disco usando o VDDK.<br/><br/> - **VirtualMachine. Provisioning. GetVmFiles** (> de provisionamento de máquina virtual-> permitir download de máquina virtual): permite operações de leitura em arquivos associados a uma VM, para baixar os logs e solucionar problemas se ocorrer falha.<br/><br/> - **VirtualMachine. State. \* *_ (Virtual Machine-> snapshot Management): permitir a criação e o gerenciamento de instantâneos de VM para replicação. <br/> <br/> -_* VirtualMachine. interaja. estado desligado** (interação de > de máquina Virtual – > desligar): permitir que a VM seja desligada durante a migração para o Azure.



### <a name="vm-requirements-agentless"></a>Requisitos de VM (sem agente)

A tabela resume os requisitos de migração sem agente para VMs VMware.

**Suporte** | **Detalhes**
--- | ---
**Sistemas operacionais com suporte** | Você pode migrar os sistemas operacionais [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) e [Linux](../virtual-machines/linux/endorsed-distros.md) com suporte no Azure.
**VMs do Windows no Azure** | Talvez seja necessário [fazer algumas alterações](prepare-for-migration.md#verify-required-changes-before-migrating) nas VMs antes da migração. 
**VMs do Linux no Azure** | Algumas VMs podem precisar de alterações para que possam ser executadas no Azure.<br/><br/> Para o Linux, as migrações para Azure fazem as alterações automaticamente para esses sistemas operacionais:<br/> -Red Hat Enterprise Linux 7,8, 7,7, 7,6, 7,5, 7,4, 7,0, 6. x<br/> -Cent OS 7,7, 7,6, 7,5, 7,4, 6. x</br> -SUSE Linux Enterprise Server 12 SP1 +<br/> -SUSE Linux Enterprise Server 15 SP1 <br/>-Ubuntu 19, 4, 19,10, 14.04 LTS, 16.04 LTS, 18.04 LTS<br/> -Debian 7, 8, 9 <br/> Oracle Linux 7.7, 7.7-CI<br/> Para outros sistemas operacionais, faça as [alterações necessárias](prepare-for-migration.md#verify-required-changes-before-migrating) manualmente.
**Inicialização do Linux** | Se/boot estiver em uma partição dedicada, ele deverá residir no disco do sistema operacional e não poderá ser distribuído em vários discos.<br/> Se/boot fizer parte da partição raiz (/), a partição '/' deverá estar no disco do sistema operacional e não poderá abranger outros discos.
**Inicialização UEFI** | Com suporte. As VMs baseadas em UEFI serão migradas para VMs do Azure geração 2. 
**Tamanho do disco** | disco do sistema operacional de 2 TB;  32 TB para discos de dados.
**Limites de disco** |  Até 60 discos por VM.
**Discos/volumes criptografados** | VMs com discos/volumes criptografados não têm suporte para migração.
**Cluster de disco compartilhado** | Não há suporte.
**Discos independentes** | Não há suporte.
**Discos de RDM/PassThrough** | Se as VMs tiverem discos RDM ou de passagem, esses discos não serão replicados para o Azure.
**NFS** | Volumes NFS montados como volumes nas VMs não serão replicados.
**destinos iSCSI** | Não há suporte para VMs com destinos iSCSI para migração sem agente.
**E/s de vários caminhos** | Não há suporte.
**VMotion de armazenamento** | Não há suporte. A replicação não funcionará se uma VM usar o Storage vMotion.
**NICs agrupadas** | Não há suporte.
**IPv6** | Não há suporte.
**Disco de destino** | As VMs só podem ser migradas para discos gerenciados (HDD padrão, SSD Standard, SSD Premium) no Azure.
**Replicação simultânea** | Até 300 replicando VMs simultaneamente por vCenter Server com 1 dispositivo. Até 500 replicando VMs simultaneamente por vCenter Server quando um dispositivo de [expansão](./how-to-scale-out-for-migration.md) adicional é implantado. 
**Instalação automática do agente de VM do Azure (agente do Windows e Linux)** | Com suporte para o Windows Server 2008 R2 em diante. <br/> Com suporte para RHEL6, RHEL7, CentOS7, Ubuntu 14, 4, Ubuntu 16, 4, Ubuntu 18.04. Examine a lista de [pacotes necessários](../virtual-machines/extensions/agent-linux.md#requirements)) para esses sistemas operacionais Linux.

> [!TIP]
>  Usando o portal do Azure você poderá selecionar até 10 VMs de cada vez para configurar a replicação. Para replicar mais VMs, você pode usar o portal e adicionar as VMs a serem replicadas em vários lotes de 10 VMs ou usar a interface do PowerShell de migrações para Azure para configurar a replicação. Verifique se você não configurou a replicação simultânea em mais do que o número máximo de VMs com suporte para replicações simultâneas.

### <a name="appliance-requirements-agentless"></a>Requisitos de dispositivo (sem agente)

A migração sem agente usa o [dispositivo de migrações para Azure](migrate-appliance.md). Você pode implantar o dispositivo como uma VM VMware usando um modelo OVA, importado para vCenter Server ou usando um [script do PowerShell](deploy-appliance-script.md).

- Saiba mais sobre os [requisitos de dispositivo](migrate-appliance.md#appliance---vmware) para VMware.
- Saiba mais sobre as URLs do Azure que o dispositivo precisa acessar nas nuvens [públicas](migrate-appliance.md#public-cloud-urls) e [governamentais](migrate-appliance.md#government-cloud-urls).
- No Azure Governamental, você deve implantar o dispositivo [usando um script](deploy-appliance-script-government.md).

### <a name="port-requirements-agentless"></a>Requisitos de porta (sem agente)

**Dispositivo** | **Conexão**
--- | ---
Dispositivo | Conexões de saída na porta 443 para carregar dados replicados no Azure e para se comunicar com os serviços de migração do Azure orquestrando a replicação e a migração.
Servidor vCenter | Conexões de entrada na porta 443 para permitir que o dispositivo coordene a replicação-criar instantâneos, copiar dados, liberar instantâneos
host vSphere/ESXI | Entrada na porta TCP 902 para o dispositivo replicar dados de instantâneos.

## <a name="agent-based-migration"></a>Migração baseada em agente 


Esta seção resume os requisitos para a migração baseada em agente.


### <a name="vmware-requirements-agent-based"></a>Requisitos do VMware (baseados em agente)

Esta tabela resume o suporte de avaliação e as limitações para servidores de virtualização VMware.

**Requisitos da VMware** | **Detalhes**
--- | ---
**VMware vCenter Server** | Versão 5,5, 6,0, 6,5 ou 6,7.
**VMware vSphere host ESXI** | Versão 5,5, 6,0, 6,5 ou 6,7.
**vCenter Server permissões** | Uma conta somente leitura para vCenter Server.

### <a name="vm-requirements-agent-based"></a>Requisitos de VM (baseado em agente)

A tabela resume o suporte de VM do VMware para VMs VMware que você deseja migrar usando a migração baseada em agente.

**Suporte** | **Detalhes**
--- | ---
**Carga de trabalho do computador** | As migrações para Azure dão suporte à migração de qualquer carga de trabalho (digamos Active Directory, SQL Server, etc.) em execução em um computador com suporte.
**Sistemas operacionais** | Para obter as informações mais recentes, examine o [suporte do sistema operacional](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines) para site Recovery. As migrações para Azure fornecem suporte ao sistema operacional de VM idêntico.
**Armazenamento de sistema de arquivos/convidado do Linux** | Para obter as informações mais recentes, examine o [suporte do sistema de arquivos do Linux](../site-recovery/vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) para site Recovery. As migrações para Azure têm suporte ao sistema de arquivos Linux idêntico.
**Rede/armazenamento** | Para obter as informações mais recentes, examine os pré-requisitos de [rede](../site-recovery/vmware-physical-azure-support-matrix.md#network) e [armazenamento](../site-recovery/vmware-physical-azure-support-matrix.md#storage) para site Recovery. As migrações para Azure fornecem requisitos de rede/armazenamento idênticos.
**Requisitos do Azure** | Para obter as informações mais recentes, examine os requisitos de rede, [armazenamento](../site-recovery/vmware-physical-azure-support-matrix.md#azure-storage)e [computação](../site-recovery/vmware-physical-azure-support-matrix.md#azure-compute) do [Azure](../site-recovery/vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover)para site Recovery. As migrações para Azure têm requisitos idênticos para a migração do VMware.
**Serviço de mobilidade** | O agente do serviço de mobilidade deve ser instalado em cada VM que você deseja migrar.
**Inicialização UEFI** | Com suporte. As VMs baseadas em UEFI serão migradas para VMs do Azure geração 2. 
**Inicialização segura de UEFI**         | Sem suporte para migração.
**Disco de destino** | As VMs só podem ser migradas para discos gerenciados (HDD padrão, SSD Standard, SSD Premium) no Azure.
**Tamanho do disco** | disco do sistema operacional de 2 TB; 32 TB para discos de dados.
**Limites de disco** |  Até 63 discos por VM.
**Discos/volumes criptografados** | VMs com discos/volumes criptografados não têm suporte para migração.
**Cluster de disco compartilhado** | Não há suporte.
**Discos independentes** | Com suporte.
**Discos de passagem** | Com suporte.
**NFS** | Volumes NFS montados como volumes nas VMs não serão replicados.
**destinos iSCSI** | Com suporte.
**E/s de vários caminhos** | Não há suporte.
**VMotion de armazenamento** | Com suporte
**NICs agrupadas** | Não há suporte.
**IPv6** | Não há suporte.




### <a name="appliance-requirements-agent-based"></a>Requisitos de dispositivo (baseados em agente)

Quando você configura o dispositivo de replicação usando o modelo OVA fornecido no Hub migrações para Azure, o dispositivo executa o Windows Server 2016 e está em conformidade com os requisitos de suporte. Se você configurar o dispositivo de replicação manualmente em um servidor físico, verifique se ele está em conformidade com os requisitos.

- Saiba mais sobre [os requisitos do dispositivo de replicação](migrate-replication-appliance.md#appliance-requirements) para VMware.
- MySQL deve estar instalado no dispositivo. Saiba mais sobre [as opções de instalação](migrate-replication-appliance.md#mysql-installation).
- Saiba mais sobre URLs que o dispositivo de replicação precisa acessar em nuvens [públicas](migrate-replication-appliance.md#url-access) e [governamentais](migrate-replication-appliance.md#azure-government-url-access) .
- Examine as [portas](migrate-replication-appliance.md#port-access) que o dispositivo de replicação precisa acessar.

### <a name="port-requirements-agent-based"></a>Requisitos de porta (baseados em agente)

**Dispositivo** | **Conexão**
--- | ---
VMs | O serviço de mobilidade em execução nas VMs se comunica com o dispositivo de replicação local (servidor de configuração) na porta HTTPS 443 de entrada, para o gerenciamento de replicação.<br/><br/> As VMs que estão sendo replicadas enviam dados de replicação para o servidor de processo (em execução no computador do servidor de configuração) na porta 9443 HTTPS de entrada. Essa porta pode ser modificada.
Dispositivo de replicação | O dispositivo de replicação orquestra a replicação com o Azure sobre a porta HTTPS 443 de saída.
Servidor de processo | O servidor de processo recebe dados de replicação, otimiza-os e criptografa-os e envia-os para o armazenamento do Azure pela porta 443 de saída.<br/> Por padrão, o servidor de processo é executado no dispositivo de replicação.

## <a name="azure-vm-requirements"></a>Requisitos de VM do Azure

Todas as VMs locais replicadas para o Azure (com migração baseada em agente ou sem agente) devem atender aos requisitos de VM do Azure resumidos nesta tabela. 

**Componente** | **Requisitos** 
--- | --- | ---
Sistema operacional convidado | Verifica os sistemas operacionais de VM VMware com suporte para migração.<br/> Você pode migrar qualquer carga de trabalho em execução em um sistema operacional com suporte. 
Arquitetura do sistema operacional convidado | 64 bits. 
Tamanho do disco do sistema operacional | Até 2.048 GB. 
Contagem do disco do sistema operacional | 1 
Contagem de disco de dados | 64 ou menos. 
Tamanho do disco de dados | Até 32 TB
Adaptadores de rede | Há suporte para vários adaptadores.
VHD compartilhado | Não há suporte. 
Disco FC | Não há suporte. 
BitLocker | Não há suporte.<br/><br/> O BitLocker deve ser desabilitado antes da migração do computador.
Nome da VM | De 1 a 63 caracteres.<br/><br/> Restrito a letras, números e hifens.<br/><br/> O nome do computador precisa começar e terminar com uma letra ou um número. 
Conectar após a migração-Windows | Para se conectar às VMs do Azure que executam o Windows após a migração:<br/><br/> -Antes da migração, habilite o RDP na VM local.<br/><br/> Certifique-se de que as regras TCP e UDP são adicionadas ao perfil **Público** e que o RDP é permitido no **Firewall do Windows** > **Aplicativos Permitidos** para todos os perfis.<br/><br/> Para acesso VPN site a site, habilite o RDP e permita que o RDP no **Firewall do Windows** tenha  ->  **aplicativos e recursos permitidos** para redes privadas e de **domínio** .<br/><br/> Além disso, verifique se a política de SAN do sistema operacional está definida como **OnlineAll**. [Saiba mais](prepare-for-migration.md).
Conectar após a migração-Linux | Para se conectar às VMs do Azure após a migração usando SSH:<br/><br/> Antes da migração, no computador local, verifique se o serviço Secure Shell está definido como iniciar e se as regras de firewall permitem uma conexão SSH.<br/><br/> Após o failover, na VM do Azure, permita conexões de entrada para a porta SSH para as regras do grupo de segurança de rede na VM com failover e para a sub-rede do Azure à qual ela está conectada.<br/><br/> Além disso, adicione um endereço IP público para a VM.  


## <a name="next-steps"></a>Próximas etapas

[Selecione](server-migrate-overview.md) uma opção de migração VMware.