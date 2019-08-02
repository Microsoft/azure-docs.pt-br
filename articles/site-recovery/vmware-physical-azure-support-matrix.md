---
title: Matriz de suporte para recuperação de desastre de VMs VMware e servidores físicos para Azure com Azure Site Recovery | Microsoft Docs
description: Resume o suporte para recuperação de desastre de VMs VMware e servidores físicos no Azure usando o Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/07/2019
ms.author: raynew
ms.openlocfilehash: 1d54f2847f7c37631374653bf291aadf091f3328
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67805944"
---
# <a name="support-matrix-for-disaster-recovery--of-vmware-vms-and-physical-servers-to-azure"></a>Matriz de suporte para recuperação de desastre de VMs VMware e servidores físicos para o Azure

Este artigo resume os componentes compatíveis e as configurações de recuperação de desastre de VMs VMware e servidores físicos no Azure usando [Azure Site Recovery](site-recovery-overview.md).

- [Saiba mais](vmware-azure-architecture.md) sobre arquitetura de recuperação de desastres do servidor VM VMware/físico.
- Siga nossos [tutoriais](tutorial-prepare-azure.md) para experimentar a recuperação de desastres.

## <a name="deployment-scenarios"></a>Cenários de implantação

**Cenário** | **Detalhes**
--- | ---
Recuperação de desastre de VMs VMware | Replicação de VMs VMware locais para Azure. É possível implantar este cenário no portal do Azure ou usando o [PowerShell](vmware-azure-disaster-recovery-powershell.md).
Recuperação de desastre de servidores físicos | Replicação de servidores físicos Windows/Linux locais para Azure. Implante esse cenário no portal do Azure.

## <a name="on-premises-virtualization-servers"></a>Servidores de virtualização locais

**Servidor** | **Requisitos** | **Detalhes**
--- | --- | ---
vCenter Server | Versão 6.7, 6.5, 6.0 ou 5.5 | É recomendável que você use um servidor do vCenter em sua implantação de recuperação de desastres.
hosts vSphere | Versão 6.7, 6.5, 6.0 ou 5.5 | Recomendamos que os hosts vSphere e os servidores vCenter estejam localizados na mesma rede que o servidor de processo. Por padrão o servidor de processo é executado no servidor de configuração. [Saiba mais](vmware-physical-azure-config-process-server-overview.md).


## <a name="site-recovery-configuration-server"></a>Servidor de configuração do Azure Site Recovery

O servidor de configuração é um computador local que executa componentes do Site Recovery, incluindo o servidor de configuração, servidor de processo e o servidor de destino mestre.

- Para VMs do VMware para definir o servidor de configuração, baixar um modelo de OVF para criar uma VM do VMware.
- Para servidores físicos, você configurar o computador do servidor de configuração manualmente.

**Componente** | **Requisitos**
--- |---
Núcleos de CPU | 8
RAM | 16 GB
Número de discos | 3 discos<br/><br/> Os discos incluem o disco do sistema operacional, disco de cache do servidor de processo e a unidade de retenção para failback.
Espaço livre em disco | 600 GB de espaço para o cache do servidor de processo.
Espaço livre em disco | 600 GB de espaço para a unidade de retenção.
Sistema operacional  | Windows Server 2012 R2 ou Windows Server 2016 com experiência Desktop |
Localidade do sistema operacional | Inglês (en-us)
[PowerCLI](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) | Não é necessária para a versão do servidor de configuração [9,14](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery) ou posterior. 
Funções do Windows Server | Não habilite os serviços de domínio do Active Directory; Serviços de informações da Internet (IIS) ou Hyper-V. 
Políticas de grupo| - Impedir o acesso ao prompt de comando. <br/> - Impedir o acesso às ferramentas de edição do registro. <br/> - Lógica de confiança para anexos de arquivo. <br/> - Ativar a execução do script. <br/> - [Saiba Mais](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)|
IIS | Verifique se você:<br/><br/> - Não tem um site padrão preexistente <br/> - Habilitar [autenticação anônima](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br/> - Habilitar configuração [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx)  <br/> - Não tem site/aplicativo preexistente ouvindo na porta 443<br/>
Tipo de NIC | VMXNET3 (quando implantado como uma VM VMware)
Tipo de endereço IP | Estático
Portas | 443 usada para orquestração do canal de controle<br/>9443 para o transporte de dados

## <a name="replicated-machines"></a>Computadores replicados

O Site Recovery dá suporte para replicação de qualquer carga de trabalho em execução em um computador com suporte.

**Componente** | **Detalhes**
--- | ---
Configurações do computador | Os computadores que são replicados para o Azure precisam atender aos [requisitos do Azure](#azure-vm-requirements).
Carga de trabalho do computador | O Site Recovery dá suporte para replicação de qualquer carga de trabalho em execução em um computador com suporte. [Saiba mais](https://aka.ms/asr_workload).
Windows | -Windows Server 2019 (suporte a partir do [Update Rollup 34](https://support.microsoft.com/help/4490016) (versão 9.22 do serviço de mobilidade) em diante.<br/> – Windows Server 2016 (64-bit Server Core, Server com experiência Desktop)<br/> - Windows Server 2012 R2, Windows Server 2012<br/> -Windows Server 2008 R2 com pelo menos SP1.<br/> -Windows Server 2008, 64 e 32 bits ao menos o SP2]. Suporte para migração somente. [Saiba mais](migrate-tutorial-windows-server-2008.md).<br/> -Windows 10, Windows 8.1, Windows 8, Windows 7 64 bits (com suporte da [Update Rollup 36](https://support.microsoft.com/help/4503156) (versão 9.22 do serviço de mobilidade em diante). Windows 7 RTM não é suportado. 
Linux | Há suporte para o único sistema de 64 bits. Não há suporte para o sistema de 32 bits.<br/><br/>Todos os servidores Linux devem ter [componentes do Integration Services LIS (Linux)](https://www.microsoft.com/download/details.aspx?id=55106) instalado. Ele é necessário para inicializar o servidor no Azure após failover/failover de teste. Se estiverem faltando componentes LIS, certifique-se para instalar o [componentes](https://www.microsoft.com/download/details.aspx?id=55106) antes de habilitar a replicação para os computadores de inicialização no Azure. <br/><br/> O Site Recovery orquestra o failover para executar servidores Linux no Azure. No entanto, os fornecedores de Linux podem limitar o suporte a apenas versões de distribuição que não atingiram o fim da vida útil.<br/><br/> Nas distribuições Linux, apenas os kernels de estoque que fazem parte da versão/atualização de versão secundária de distribuição têm suporte.<br/><br/> Não há suporte para atualização de computadores protegidos nas versões principais de distribuição do Linux. Para atualizar, desabilite a replicação, atualize o sistema operacional e, em seguida, habilite a replicação novamente.<br/><br/> [Saiba mais](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure) sobre o suporte para Linux e tecnologia de software livre no Azure.
Linux Red Hat Enterprise | 5.2 a 5.11</b><br/> 6.1 para 6.10</b><br/> 7.0 para 7.6<br/> <br/> Não tem servidores que executam o Red Hat Enterprise Linux 5.2 a 5.11 & 6.1 6.10 [componentes do Integration Services LIS (Linux)](https://www.microsoft.com/download/details.aspx?id=55106) pré-instalado. Certifique-se de instalar o [componentes](https://www.microsoft.com/download/details.aspx?id=55106) antes de habilitar a replicação para os computadores de inicialização no Azure.
Linux: CentOS | 5.2 a 5.11</b><br/> 6.1 para 6.10</b><br/> 7.0 para 7.6<br/> <br/> Não tem servidores executando o CentOS 5.2 a 5.11 & 6.1 6.10 [componentes do Integration Services LIS (Linux)](https://www.microsoft.com/download/details.aspx?id=55106) pré-instalado. Certifique-se de instalar o [componentes](https://www.microsoft.com/download/details.aspx?id=55106) antes de habilitar a replicação para os computadores de inicialização no Azure.
Ubuntu | Servidor do Ubuntu 14.04 LTS [(versões de kernel com suporte de revisão)](#ubuntu-kernel-versions)<br/><br/>Servidor do Ubuntu 16.04 LTS [(versões de kernel com suporte de revisão)](#ubuntu-kernel-versions)
Debian | Debian 7/Debian 8 [(versões de kernel com suporte de revisão)](#debian-kernel-versions)
SUSE Linux | SUSE Linux Enterprise Server 12 SP1, SP2, SP3, SP4 [(versões de kernel com suporte de revisão)](#suse-linux-enterprise-server-12-supported-kernel-versions)<br/> SUSE Linux Enterprise Server 11 SP3, SUSE Linux Enterprise Server 11 SP4<br/> Atualizando as máquinas replicadas do SUSE Linux Enterprise Server 11 SP3 para SP4 não é suportado. Para atualizar, desabilite a replicação e habilite novamente após a atualização.
Oracle Linux | 6.4, 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6<br/><br/> Executando o kernel compatível do Red Hat ou o Unbreakable Enterprise Kernel versão 3, 4 e 5 (UEK3, UEK4, UEK5) 


### <a name="ubuntu-kernel-versions"></a>Versões de kernel do Ubuntu


**Versão com suporte** | **Versão de serviço de mobilidade** | **Versão do kernel** |
--- | --- | --- |
14.04 LTS | [9.26][9.26 UR]| 3.13.0-24-Generic para 3.13.0-170-generic,<br/>3.16.0-25-generic para 3.16.0-77-generic,<br/>3.19.0-18-generic para 3.19.0-80-generic,<br/>4.2.0-18-generic para 4.2.0-42-generic,<br/>4.4.0-21 para 4.4.0-148-generic,<br/>4.15.0-1023-Azure para 4.15.0-1045-azure |
14.04 LTS | [9.25][9.25 UR]  | 3.13.0-24-Generic para 3.13.0-169-generic,<br/>3.16.0-25-generic para 3.16.0-77-generic,<br/>3.19.0-18-generic para 3.19.0-80-generic,<br/>4.2.0-18-generic para 4.2.0-42-generic,<br/>4.4.0-21 para 4.4.0-146-generic,<br/>4.15.0-1023-Azure para 4.15.0-1042-azure |
14.04 LTS | [9.24][9.24 UR] | 3.13.0-24-Generic para 3.13.0-167-generic,<br/>3.16.0-25-generic para 3.16.0-77-generic,<br/>3.19.0-18-generic para 3.19.0-80-generic,<br/>4.2.0-18-generic para 4.2.0-42-generic,<br/>4.4.0-21 para 4.4.0-143-generic,<br/>4.15.0-1023-Azure para 4.15.0-1040-azure |
14.04 LTS | [9.23][9.23 UR] | 3.13.0-24-Generic para 3.13.0-165-generic,<br/>3.16.0-25-generic para 3.16.0-77-generic,<br/>3.19.0-18-generic para 3.19.0-80-generic,<br/>4.2.0-18-generic para 4.2.0-42-generic,<br/>4.4.0-21 para 4.4.0-142-generic,<br/>4.15.0-1023-Azure para 4.15.0-1037-azure |
|||
16.04 LTS | [9.26][9.26 UR] | 4.4.0-21 para 4.4.0-148-generic,<br/>4.8.0-34-generic a 4.8.0-58-generic,<br/>4.10.0-14-generic para 4.10.0-42-generic,<br/>4.11.0-13-generic para 4.11.0-14-generic,<br/>4.13.0-16-generic a 4.13.0-45-generic,<br/>4.15.0-13-Generic para 4.15.0-50-generic<br/>4.11.0-1009-azure para 4.11.0-1016-azure,<br/>4.13.0-1005-azure a 4.13.0-1018-azure <br/>4.15.0-1012-Azure para 4.15.0-1045-azure|
16.04 LTS | [9.25][9.25 UR] | 4.4.0-21 para 4.4.0-146-generic,<br/>4.8.0-34-generic a 4.8.0-58-generic,<br/>4.10.0-14-generic para 4.10.0-42-generic,<br/>4.11.0-13-generic para 4.11.0-14-generic,<br/>4.13.0-16-generic a 4.13.0-45-generic,<br/>4.15.0-13-Generic para 4.15.0-48-generic<br/>4.11.0-1009-azure para 4.11.0-1016-azure,<br/>4.13.0-1005-azure a 4.13.0-1018-azure <br/>4.15.0-1012-Azure para 4.15.0-1042-azure|
16.04 LTS | [9.24][9.24 UR] | 4.4.0-21 para 4.4.0-143-generic,<br/>4.8.0-34-generic a 4.8.0-58-generic,<br/>4.10.0-14-generic para 4.10.0-42-generic,<br/>4.11.0-13-generic para 4.11.0-14-generic,<br/>4.13.0-16-generic a 4.13.0-45-generic,<br/>4.15.0-13-Generic para 4.15.0-46-generic<br/>4.11.0-1009-azure para 4.11.0-1016-azure,<br/>4.13.0-1005-azure a 4.13.0-1018-azure <br/>4.15.0-1012-Azure para 4.15.0-1040-azure|
16.04 LTS | [9.23][9.23 UR] | 4.4.0-21 para 4.4.0-142-generic,<br/>4.8.0-34-generic a 4.8.0-58-generic,<br/>4.10.0-14-generic para 4.10.0-42-generic,<br/>4.11.0-13-generic para 4.11.0-14-generic,<br/>4.13.0-16-generic a 4.13.0-45-generic,<br/>4.15.0-13-Generic para 4.15.0-45-generic<br/>4.11.0-1009-azure para 4.11.0-1016-azure,<br/>4.13.0-1005-azure a 4.13.0-1018-azure <br/>4.15.0-1012-Azure para 4.15.0-1037-azure|

### <a name="debian-kernel-versions"></a>Versões de Kernel do Debian


**Versão com suporte** | **Versão de serviço de mobilidade** | **Versão do kernel** |
--- | --- | --- |
Debian 7 | [9.21][9.21 UR], [9.22][9.22 UR],[9.23][UR 9.23], [9,24][9.24 UR]| 3.2.0-4-amd64 a 3.2.0-6-amd64, 3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | [9.25][9.25 UR] | 3.16.0-4-amd64 to 3.16.0-8-amd64, 4.9.0-0.bpo.4-amd64 to 4.9.0-0.bpo.8-amd64 |
Debian 8 | [9.22][9.22 UR],[9.23][9.23 UR], [9,24][9.24 UR] | 3.16.0-4-amd64 a 3.16.0-7-amd64, 4.9.0-0.bpo.4-amd64 a 4.9.0-0.bpo.8-amd64 |


### <a name="suse-linux-enterprise-server-12-supported-kernel-versions"></a>SUSE Linux Enterprise Server 12 versões de kernel com suporte

**Versão** | **Versão de serviço de mobilidade** | **Versão do kernel** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3,SP4) | [9.26][9.26 UR] | SP1 3.12.49-11-default a 3.12.74-60.64.40-default</br></br> 3\.12.74-60.64.45-default SP1(LTSS) para 3.12.74-60.64.110-default</br></br> SP2 4.4.21-69-default a 4.4.120-92.70-default</br></br>4\.4.121-92.73-default SP2(LTSS) para 4.4.121-92.109-default</br></br>4\.4.73-5-default SP3 para 4.4.178-94.91-default</br></br>4\.4.138-4.7-azure SP3 para 4.4.178-4.28-azure</br></br>4\.12.14-94.41-default SP4 para 4.12.14-95.16-default</br>4\.12.14-6.3-azure SP4 para 4.12.14-6.9-azure |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3,SP4) | [9.25][9.25 UR] | SP1 3.12.49-11-default a 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default a 3.12.74-60.64.107-default</br></br> SP2 4.4.21-69-default a 4.4.120-92.70-default</br></br>4\.4.121-92.73-default SP2(LTSS) para 4.4.121-92.104-default</br></br>4\.4.73-5-default SP3 para 4.4.176-94.88-default</br></br>4\.4.138-4.7-azure SP3 para 4.4.176-4.25-azure</br></br>4\.12.14-94.41-default SP4 para 4.12.14-95.13-default</br>4\.12.14-6.3-azure SP4 para 4.12.14-6.9-azure |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3,SP4) | [9.24][9.24 UR] | SP1 3.12.49-11-default a 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default a 3.12.74-60.64.107-default</br></br> SP2 4.4.21-69-default a 4.4.120-92.70-default</br></br>4\.4.121-92.73-default SP2(LTSS) para 4.4.121-92.101-default</br></br>4\.4.73-5-default SP3 para 4.4.175-94.79-default</br></br>4\.12.14-94.41-default SP4 para 4.12.14-95.6-default |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3,SP4) | [9.23][9.23 UR] | SP1 3.12.49-11-default a 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default a 3.12.74-60.64.107-default</br></br> SP2 4.4.21-69-default a 4.4.120-92.70-default</br></br>4\.4.121-92.73-default SP2(LTSS) para 4.4.121-92.101-default</br></br>SP3 4.4.73-5-default a 4.4.162-94.69-default</br></br>4\.12.14-94.41-default SP4 para 4.12.14-95.6-default |


## <a name="linux-file-systemsguest-storage"></a>Sistemas de arquivos Linux/armazenamento convidado

**Componente** | **Com suporte**
--- | ---
Sistemas de arquivos | ext3, ext4, XFS
Gerenciador de volumes | -Há suporte para LVM.<br/> - / inicialização sobre o LVM tem suporte desde [Update Rollup 31](https://support.microsoft.com/help/4478871/) (versão 9,20 do serviço de mobilidade) em diante. Ele não tem suporte em versões anteriores de serviço de mobilidade.<br/> -Não há suporte para vários discos do sistema operacional.
Dispositivos de armazenamento paravirtualizados | Não há suporte para dispositivos exportados por drivers paravirtualizados.
Dispositivos de E/S de bloqueio de várias filas | Sem suporte.
Servidores físicos com o controlador de armazenamento CCISS da HP | Sem suporte.
Convenção de nomenclatura de ponto de montagem/dispositivo | O nome do dispositivo ou o nome do ponto de montagem deve ser exclusivo.<br/> Certifique-se de que não há dois pontos de montagem/dispositivos têm nomes diferencia maiusculas de minúsculas. Por exemplo, nomenclatura dispositivos para a mesma VM que *device1* e *Device1* não tem suporte.
Diretórios | Se você estiver executando uma versão do serviço Mobility anterior à versão 9,20 (lançado em [Update Rollup 31](https://support.microsoft.com/help/4478871/)), em seguida, essas restrições se aplicam:<br/><br/> -Esses diretórios (se o conjunto de backup como separar partições/sistemas de arquivos) devem estar no mesmo disco do sistema operacional no servidor de origem: / (raiz), /boot, /usr, /usr/local, /var, /etc.</br> -O diretório /boot deve estar em uma partição de disco e não ser um volume LVM.<br/><br/> Da versão 9,20 em diante, essas restrições não se aplicam. 
Diretório de inicialização | -Vários discos de inicialização têm suporte em uma VM <br/> -há suporte a inicialização em um volume LVM em mais de um disco.<br/> -Um computador sem um disco de inicialização não pode ser replicado.
Requisitos de espaço livre| 2 GB na /partição raiz <br/><br/> 250 MB na pasta de instalação
XFSv5 | Recursos XFSv5 em sistemas de arquivos XFS, como soma de verificação de metadados são com suporte (Mobility service versão 9.10 em diante).<br/> Use o utilitário xfs_info para verificar o superbloco XFS da partição. Se `ftype` é definido como 1, em seguida, recursos de XFSv5 estão em uso.
BTRFS | BTRFS é compatível com a [Update Rollup 34](https://support.microsoft.com/help/4490016) (versão 9.22 do serviço de mobilidade) em diante. BTRFS não é suportado se:<br/><br/> -O subvolume de sistema de arquivo BTRFS é alterado depois de habilitar a proteção.</br> -O sistema de arquivos BTRFS é distribuído entre vários discos.</br> -O sistema de arquivos BTRFS dá suporte a RAID.

## <a name="vmdisk-management"></a>Gerenciamento de VM/disco

**Ação** | **Detalhes**
--- | ---
Redimensionar o disco na VM replicada | Com suporte.
Adicionar disco na VM replicada | Sem suporte.<br/> Desabilite a replicação para a VM, adicione o disco e, em seguida, reabilitar a replicação.

## <a name="network"></a>Rede

**Componente** | **Com suporte**
--- | ---
Agrupamento NIC da rede do host | Compatível com VMs da VMware. <br/><br/>Sem suporte para a replicação de computador físico.
VLAN da rede do host | Sim.
IPv4 da rede do host | Sim.
IPv6 da rede do host | Nº
Agrupamento NIC da rede do convidado/servidor | Nº
IPv4 da rede do convidado/servidor | Sim.
IPv6 da rede do convidado/servidor | Nº
IP estático da rede do convidado/servidor (Windows) | Sim.
IP estático da rede do convidado/servidor (Linux) | Sim. <br/><br/>As VMs são configuradas para usar o DHCP no failback.
Várias NICs da rede do convidado/servidor | Sim.


## <a name="azure-vm-network-after-failover"></a>Rede VM do Azure (após o failover)

**Componente** | **Com suporte**
--- | ---
Azure ExpressRoute | Sim
ILB | Sim
ELB | Sim
Gerenciador de Tráfego do Azure | Sim
NIC múltipla | Sim
Endereço IP Reservado | Sim
IPv4 | Sim
Manter endereço IP de origem | Sim
Pontos de extremidade de serviço de rede virtual do Azure<br/> | Sim
Redes aceleradas | Não

## <a name="storage"></a>Armazenamento
**Componente** | **Com suporte**
--- | ---
Dados dinâmicos | Disco do sistema operacional deve ser um disco básico. <br/><br/>Os discos de Dados podem ser discos dinâmicos
Configuração de disco do Docker | Não
NFS do host | Sim para VMware<br/><br/> Não para servidores físicos
Host SAN iSCSI/FC) | Sim
Host vSAN | Sim para VMware<br/><br/> N/D para servidores físicos
MPIO (Múltiplos caminhos) do host | Sim, testado com Microsoft DSM, EMC PowerPath 5.7 SP4, EMC PowerPath DSM for CLARiiON
Volumes de host Virtual (VVols) | Sim para VMware<br/><br/> N/D para servidores físicos
VMDK do convidado/servidor | Sim
Disco de cluster compartilhado do convidado/servidor | Não
Disco criptografado do convidado/servidor | Não
NFS do convidado/servidor | Não
Convidado/servidor iSCSI | Não
SMB 3.0 do convidado/servidor | Não
RDM do convidado/servidor | Sim<br/><br/> N/D para servidores físicos
Disco do convidado/servidor > 1 TB | Sim<br/><br/>Até 4.095 GB<br/><br/> O disco deve ser maior que 1024 MB.
Disco do convidado/servidor com tamanho de setor lógico e físico de 4.000 cada | Não
Disco do convidado/servidor com lógico de 4K e tamanho de setor físico de 512 bytes | Não
Volume do convidado/servidor com discos distribuídos >4 TB <br/><br/>Gerenciamento de volumes lógicos (LVM)| Sim
Convidado/servidor - espaços de armazenamento | Não
Adicionar/remover disco a quente por convidado/servidor | Não
Convidado/servidor - excluir disco | Sim
MPIO (Múltiplos caminhos) de convidado/servidor | Não
Partições GPT do convidado/servidor | Cinco partições têm suporte no [Update Rollup 37](https://support.microsoft.com/help/4508614/) (versão 9.25 do serviço de mobilidade) em diante. Anteriormente, havia compatibilidade com quatro.
Inicialização EFI/UEFI do convidado/servidor | -Suporte quando você estiver executando a versão do serviço de mobilidade 9.13 ou posterior.<br/> -Suporte ao migrar VMs VMware ou servidores físicos que executam o Windows Server 2012 ou posterior para o Azure.<br/> -Você só pode replicar VMs para a migração. Não há suporte para failback no local.<br/> -NTFS somente há suporte para <br/> -Não há suporte para o tipo de inicialização UEFI seguro. <br/> -Tamanho de setor do disco deve ser de 512 bytes por setor físico.

## <a name="replication-channels"></a>Canais de replicação

|**Tipo de replicação**   |**Com suporte**  |
|---------|---------|
|Transferências de dados descarregados (ODX)    |       Não  |
|Propagação Offline        |   Não      |
| Azure Data Box | Não

## <a name="azure-storage"></a>Armazenamento do Azure

**Componente** | **Com suporte**
--- | ---
Armazenamento com redundância local | Sim
Armazenamento com redundância geográfica | Sim
Armazenamento com redundância geográfica com acesso de leitura | Sim
Armazenamento frio | Não
Armazenamento quente| Não
Blobs de bloco | Não
Criptografia em repouso (SSE)| Sim
Armazenamento Premium | Sim
Serviço de importação/exportação | Não
Firewalls do armazenamento do Azure para redes virtuais | Sim.<br/> Configurado na conta de armazenamento de cache/armazenamento de destino (usada para armazenar dados de replicação).
Contas de armazenamento de uso geral v2 (camadas hot e cool) | Sim (transação custos são consideravelmente maiores para a V2 em comparação comparada V1)

## <a name="azure-compute"></a>Computação do Azure

**Recurso** | **Com suporte**
--- | ---
Conjuntos de disponibilidade | Sim
Zonas de disponibilidade | Não
HUB | Sim
Discos gerenciados | Sim

## <a name="azure-vm-requirements"></a>Requisitos de VM do Azure

Locais VMs replicadas no Azure devem atender os requisitos de VM do Azure resumidos nesta tabela. Quando o Site Recovery é executado uma verificação de pré-requisitos para replicação, a verificação falhará se alguns dos requisitos não forem atendidos.

**Componente** | **Requisitos** | **Detalhes**
--- | --- | ---
Sistema operacional convidado | Verificar [sistemas operacionais com suporte](#replicated-machines) para computadores replicados. | A verificação falha se não tiver suporte.
Arquitetura do sistema operacional convidado | 64 bits. | A verificação falha se não tiver suporte.
Tamanho do disco do sistema operacional | Até 2.048 GB. | A verificação falha se não tiver suporte.
Contagem do disco do sistema operacional | 1 | A verificação falha se não tiver suporte.
Contagem de disco de dados | 64 ou menos. | A verificação falha se não tiver suporte.
Tamanho do disco de dados | Até 4.095 GB | A verificação falha se não tiver suporte.
Adaptadores de rede | Há suporte para vários adaptadores. |
VHD compartilhado | Sem suporte. | A verificação falha se não tiver suporte.
Disco FC | Sem suporte. | A verificação falha se não tiver suporte.
BitLocker | Sem suporte. | O BitLocker precisa ser desabilitado antes de habilitar a replicação em um computador. |
Nome da VM | De 1 a 63 caracteres.<br/><br/> Restrito a letras, números e hifens.<br/><br/> O nome do computador precisa começar e terminar com uma letra ou um número. |  Atualize o valor nas propriedades do computador no Site Recovery.

## <a name="churn-limits"></a>Limites de variação

A tabela a seguir fornece os limites do Azure Site Recovery. 
- Esses limites são baseados em nossos testes, mas não abrangem todas as combinações de e/s do aplicativo possível.
- Os resultados reais podem variar dependendo da combinação de E/S do aplicativo.
- Para obter melhores resultados, é altamente recomendável que você execute as [ferramenta Planejador de implantação](site-recovery-deployment-planner.md)e executar extensos testes de aplicativos usando o teste os failovers para obter a imagem real do desempenho para seu aplicativo.

**Destino de replicação** | **Tamanho de E/S de disco de origem médio** |**Variação nos dados média do disco de origem** | **Total de variação de dados de disco de origem por dia**
---|---|---|---
Armazenamento Standard | 8 KB | 2 MB/s | 168 GB por disco
Disco Premium P10 ou P15 | 8 KB  | 2 MB/s | 168 GB por disco
Disco Premium P10 ou P15 | 16 KB | 4 MB/s |  336 GB por disco
Disco Premium P10 ou P15 | 32 KB ou maior | 8 MB/s | 672 GB por disco
Disco Premium P20 ou P30 ou P40 ou P50 | 8 KB    | 5 MB/s | 421 GB por disco
Disco Premium P20 ou P30 ou P40 ou P50 | 16 KB ou maior |20 MB/s | 1684 GB por disco


**Variação de dados de origem** | **Limite máximo**
---|---
Média de variação de dados por VM| 25 MB/s
Variação de dados de pico entre todos os discos em uma VM | 54 MB/s
Variação máxima de dados por dia com suporte de um Servidor de Processo | 2 TB

- Esses são números médios, pressupondo uma sobreposição de E/S de 30%.
- O Site Recovery pode lidar com uma maior taxa de transferência com base na taxa de sobreposição, em tamanhos maiores de gravação e em comportamento de E/S de carga de trabalho real.
- Esses números supõem uma lista de pendências típica de aproximadamente cinco minutos. Ou seja, depois que os dados são carregados, eles são processados, e um ponto de recuperação é criado dentro de cinco minutos.

## <a name="vault-tasks"></a>Tarefas do Vault

**Ação** | **Com suporte**
--- | ---
Mover cofre entre grupos de recursos | Não
Mover cofre dentro e entre assinaturas | Não
Mover armazenamento, rede, VMs do Azure entre grupos de recursos | Não
Mover armazenamento, rede, VMs do Azure dentro e entre assinaturas. | Não


## <a name="obtain-latest-components"></a>Obter os componentes mais recentes

**Nome** | **Descrição** | **Detalhes**
--- | --- | ---
Servidor de configuração | Instalado localmente.<br/> Coordena as comunicações entre servidores do VMware locais ou máquinas físicas e Azure. | - [Saiba mais sobre](vmware-physical-azure-config-process-server-overview.md) o servidor de configuração.<br/> - [Saiba mais sobre](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server) atualizar para a versão mais recente.<br/> - [Saiba mais sobre](vmware-azure-deploy-configuration-server.md) como configurar o servidor de configuração. 
Servidor de processo | Instalado por padrão no servidor de configuração.<br/> Recebe dados de replicação, otimiza-os com caching, compactação e criptografia e envia-o para o Azure.<br/> À medida que cresce a sua implantação, você pode adicionar servidores de processo adicionais para lidar com volumes maiores de tráfego de replicação. | - [Saiba mais sobre](vmware-physical-azure-config-process-server-overview.md) o servidor de processo.<br/> - [Saiba mais sobre](vmware-azure-manage-process-server.md#upgrade-a-process-server) atualizar para a versão mais recente.<br/> - [Saiba mais sobre](vmware-physical-large-deployment.md#set-up-a-process-server) Configurando servidores de processo de escalonamento horizontal.
Serviço de Mobilidade | Instalado na VM VMware ou servidores físicos que você deseja replicar.<br/> Coordena a replicação entre locais servidores VMware/servidores físicos e o Azure.| - [Saiba mais sobre](vmware-physical-mobility-service-overview.md) o serviço de mobilidade.<br/> - [Saiba mais sobre](vmware-physical-manage-mobility-service.md#update-mobility-service-from-azure-portal) atualizar para a versão mais recente.<br/> 



## <a name="next-steps"></a>Próximas etapas
[Saiba como](tutorial-prepare-azure.md) para preparar o Azure para recuperação de desastres de máquinas virtuais da VMware.

[9.26 UR]: https://support.microsoft.com/en-in/help/4508614/update-rollup-37-for-azure-site-recovery
[9.25 UR]: https://support.microsoft.com/en-in/help/4508614/update-rollup-37-for-azure-site-recovery
[9.24 UR]: https://support.microsoft.com/en-in/help/4503156
[9.23 UR]: https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery
[9.22 UR]: https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery
[9.21 UR]: https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery
[9.20 UR]: https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery
[9.19 UR]: https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30
[9.18 UR]: https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery
