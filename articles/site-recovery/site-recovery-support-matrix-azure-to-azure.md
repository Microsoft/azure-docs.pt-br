---
title: "Matriz de suporte do Azure Site Recovery para replicação do Azure para o Azure | Microsoft Docs"
description: "Resume os sistemas operacionais com suporte e as configurações de replicação do Azure Site Recovery de VMs (máquinas virtuais) do Azure de uma região para outra, de acordo com as necessidades de DR (recuperação de desastre)."
services: site-recovery
documentationcenter: 
author: sujayt
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 08/31/2017
ms.author: sujayt
ms.openlocfilehash: f52520f88d36914d4cad7c8aea4fb21e44b3cc9d
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-site-recovery-support-matrix-for-replicating-from-azure-to-azure"></a>Matriz de suporte do Azure Site Recovery para replicação do Azure para o Azure


>[!NOTE]
>
> Atualmente, a replicação do Site Recovery para máquinas virtuais do Azure está em versão prévia.

Este artigo resume as configurações e os componentes com suporte no Azure Site Recovery ao replicar e recuperar máquinas virtuais do Azure de uma região para outra.

## <a name="user-interface-options"></a>Opções de interface do usuário

**Interface do usuário** |  **Com suporte/Sem suporte**
--- | ---
**Portal do Azure** | Suportado
**Portal clássico** | Sem suporte
**PowerShell** | Sem suporte no momento
**API REST** | Sem suporte no momento
**CLI** | Sem suporte no momento


## <a name="resource-move-support"></a>Suporte de movimentação de recursos

**Tipo de movimentação de recursos** | **Com suporte/Sem suporte** | **Comentários**  
--- | --- | ---
**Mover cofre entre grupos de recursos** | Sem suporte |Não é possível mover o cofre dos Serviços de Recuperação entre grupos de recursos.
**Mover Computação, Armazenamento e Rede entre grupos de recursos** | Sem suporte |Se você mover uma máquina virtual (ou seus componentes associados, como armazenamento e rede) depois de habilitar a replicação, precisará desabilitar a replicação e habilitá-la novamente na máquina virtual.



## <a name="support-for-deployment-models"></a>Suporte para modelos de implantação

**Modelo de implantação** | **Com suporte/Sem suporte** | **Comentários**  
--- | --- | ---
**Clássico** | Suportado | É possível replicar apenas uma máquina virtual clássica e recuperá-la como uma máquina virtual clássica. Não é possível recuperá-la como uma máquina de virtual do Resource Manager. Se você implantar uma VM clássica sem uma rede virtual e diretamente em uma região do Azure, não haverá suporte para essa opção.
**Gerenciador de Recursos** | Suportado |

>[!NOTE]
>
> 1. Não há suporte para a replicação de máquinas virtuais do Azure de uma assinatura para outra em cenários de recuperação de desastre.
> 2. Não há suporte para migrar máquinas virtuais do Azure entre assinaturas.
> 3. Não há suporte para migrar máquinas virtuais do Azure dentro da mesma região.
> 4. Não há suporte para migrar máquinas virtuais do Azure do modelo de implantação clássico para o modelo de implantação do Resource Manager.

## <a name="support-for-replicated-machine-os-versions"></a>Suporte para versões do SO do computador replicado

O suporte abaixo é aplicável a qualquer carga de trabalho em execução no sistema operacional mencionado.

#### <a name="windows"></a>Windows

- Windows Server 2016 (Server Core e Servidor com Experiência Desktop)*
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2 com, no mínimo, SP1

>[!NOTE]
>
> \* Não há suporte para o Windows Server 2016 Nano Server.

#### <a name="linux"></a>Linux

- Red Hat Enterprise Linux 6.7, 6.8, 6.9, 7.0, 7.1, 7.2, 7.3
- CentOS 6.5, 6.6, 6.7, 6.8, 6.9, 7.0, 7.1, 7.2, 7.3
- Ubuntu 14.04 LTS Server [ (versões de kernel com suporte)](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
- Ubuntu 16.04 LTS Server [ (versões de kernel com suporte)](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
- Debian 7
- Debian 8
- Oracle Enterprise Linux 6.4, 6.5 que executa o kernel compatível com Red Hat ou o UEK3 (Unbreakable Enterprise Kernel Versão 3)
- SUSE Linux Enterprise Server 11 SP3
- SUSE Linux Enterprise Server 11 SP4

(Não há suporte para a atualização de computadores de replicação de SLES 11 SP3 até SLES 11 SP4. Se um computador replicado tiver sido atualizado do SLES 11 SP3 para o SLES 11 SP4, você precisará desabilitar a replicação e proteger o computador novamente após a atualização).

>[!NOTE]
>
> A autenticação baseada em senha e logon pode ser desabilitada no failover em servidores Ubuntu que a utilizam junto com o pacote cloud-init para configurar máquinas virtuais de nuvem, pode ter o logon baseado em senha desabilitado no failover (dependendo da configuração de cloudinit). O logon baseado em senha pode ser reabilitado na máquina virtual redefinindo a senha no menu configurações (na seção SUPORTE + SOLUÇÃO DE PROBLEMAS) da máquina virtual de failover no Portal do Azure.

### <a name="supported-ubuntu-kernel-versions-for-azure-virtual-machines"></a>Versões com suporte do kernel Ubuntu para máquinas virtuais do Azure

**Versão** | **Versão de serviço de mobilidade** | **Versão do kernel** |
--- | --- | --- |
14.04 LTS | 9.9 | 3.13.0-24-Generic para 3.13.0-117-generic,<br/>3.16.0-25-generic para 3.16.0-77-generic,<br/>3.19.0-18-generic para 3.19.0-80-generic,<br/>4.2.0-18-generic para 4.2.0-42-generic,<br/>4.4.0-21-generic para 4.4.0-75-generic |
14.04 LTS | 9.10 | 3.13.0-24-generic a 3.13.0-121-generic,<br/>3.16.0-25-generic para 3.16.0-77-generic,<br/>3.19.0-18-generic para 3.19.0-80-generic,<br/>4.2.0-18-generic para 4.2.0-42-generic,<br/>4.4.0-21-generic a 4.4.0-81-generic |
14.04 LTS | 9.11 | 3.13.0-24-generic a 3.13.0-125-generic,<br/>3.16.0-25-generic para 3.16.0-77-generic,<br/>3.19.0-18-generic para 3.19.0-80-generic,<br/>4.2.0-18-generic para 4.2.0-42-generic,<br/>4.4.0-21-generic a 4.4.0-83-generic |
16.04 LTS | 9.10 | 4.4.0-21-generic a 4.4.0-81-generic,<br/>4.8.0-34-generic a 4.8.0-56-generic,<br/>4.10.0-14-generic a 4.10.0-24-generic |
16.04 LTS | 9.11 | 4.4.0-21-generic a 4.4.0-83-generic,<br/>4.8.0-34-generic a 4.8.0-58-generic,<br/>4.10.0-14-generic a 4.10.0-27-generic |

## <a name="supported-file-systems-and-guest-storage-configurations-on-azure-virtual-machines-running-linux-os"></a>Sistemas de arquivos com suporte e configurações de armazenamento de convidado em máquinas virtuais do Azure que executam o sistema operacional Linux

* Sistemas de arquivos: ext3, ext4, ReiserFS (somente Suse Linux Enterprise Server), XFS
* Gerenciador de volumes: LVM2
* Software Multipath: Device Mapper

## <a name="region-support"></a>Suporte de regiões

É possível replicar e recuperar VMs entre duas regiões quaisquer dentro do mesmo cluster geográfico.

**Cluster geográfico** | **Regiões do Azure**
-- | --
América | Leste do Canadá, Canadá Central, Centro-Sul dos EUA, Centro-Oeste dos EUA, Leste dos EUA, Leste dos EUA 2, Oeste dos EUA, Oeste dos EUA 2, EUA Central, Centro-Norte dos EUA
Europa | Oeste do Reino Unido, Sul do Reino Unido, Europa Setentrional, Europa Ocidental
Ásia | Sul da Índia, Índia Central, Sudeste Asiático, Ásia Oriental, Leste do Japão, Oeste do Japão, Coreia Central, Sul da Coreia
Austrália   | Leste da Austrália, Sudeste da Austrália

>[!NOTE]
>
> Para a região Sul do Brasil, é possível apenas replicar e fazer failover para uma das regiões Centro-Sul dos EUA, Centro-Oeste dos EUA, Leste dos EUA, Leste dos EUA 2, Oeste dos EUA, Oeste dos EUA 2 e Centro-Norte dos EUA e fazer failback.


## <a name="support-for-compute-configuration"></a>Suporte para configuração de Computação

**Configuração** | **Com suporte/Sem suporte** | **Comentários**
--- | --- | ---
Tamanho | Qualquer tamanho de VM do Azure com, no mínimo, 2 núcleos de CPU e 1 GB de RAM | Consulte [Tamanhos de máquina virtual do Azure](../virtual-machines/windows/sizes.md)
Conjuntos de disponibilidade | Suportado | Se você usar a opção padrão durante a etapa “Habilitar replicação” no portal, o conjunto de disponibilidade será automaticamente criado de acordo com a configuração da região de origem. Altere o conjunto de disponibilidade de destino definido em “Item replicado > Configurações > Computação e Rede > Conjunto de disponibilidade” a qualquer momento.
VMs do HUB (Benefício de Uso Híbrido) | Suportado | Se a VM de origem tiver a licença do HUB habilitada, a VM de Failover de teste ou de Failover também usará a licença do HUB.
conjuntos de escala de máquina virtual | Sem suporte |
Imagens da Galeria do Azure – publicadas pela Microsoft | Suportado | Com suporte, desde que a VM seja executada em um sistema operacional com suporte pelo Site Recovery
Imagens da Galeria do Azure – publicadas por terceiros | Suportado | Com suporte, desde que a VM seja executada em um sistema operacional com suporte pelo Site Recovery.
Imagens personalizadas – publicadas por terceiros | Suportado | Com suporte, desde que a VM seja executada em um sistema operacional com suporte pelo Site Recovery.
VMs migradas com o Site Recovery | Suportado | Se ela for um computador Físico/VMware migrado para o Azure com o Site Recovery, você precisará desinstalar a versão mais antiga do serviço de mobilidade e reiniciar o computador antes de replicá-lo para outra região do Azure.

## <a name="support-for-storage-configuration"></a>Suporte para configuração de Armazenamento

**Configuração** | **Com suporte/Sem suporte** | **Comentários**
--- | --- | ---
Tamanho máximo do disco do sistema operacional | 1023 GB | Consulte [Discos usados pelas VMs.](../virtual-machines/windows/about-disks-and-vhds.md#disks-used-by-vms)
Tamanho máximo do disco de dados | 1023 GB | Consulte [Discos usados pelas VMs.](../virtual-machines/windows/about-disks-and-vhds.md#disks-used-by-vms)
Número de discos de dados | Até 64, com suporte em um tamanho específico de VM do Azure | Consulte [Tamanhos de máquina virtual do Azure](../virtual-machines/windows/sizes.md)
Disco temporário | Sempre excluído da replicação | O disco temporário sempre é excluído da replicação. Você não deve colocar nenhum dado persistente no disco temporário, de acordo com as diretrizes do Azure. Consulte [Disco temporário em VMs do Azure](../virtual-machines/windows/about-disks-and-vhds.md#temporary-disk) para obter mais detalhes.
Taxa de alteração de dados no disco | Máximo de 6 MBps por disco | Se a taxa média de alteração de dados no disco estiver acima de 6 MBps de forma contínua, a replicação não será atualizada. No entanto, se essa for uma intermitência de dados ocasional e a taxa de alteração de dados for maior que 6 MBps por algum tempo e ficar inoperante, a replicação será atualizada. Nesse caso, talvez você veja os pontos de recuperação um pouco atrasados.
Discos em contas de armazenamento Standard | Suportado |
Discos em contas de armazenamento Premium | Suportado | Se uma VM tiver discos distribuídos em contas de armazenamento Standard e Premium, você poderá selecionar uma conta de armazenamento de destino diferente para cada disco, a fim de garantir que você tem a mesma configuração de armazenamento na região de destino
Managed Disks Standard | Sem suporte |  
Managed Disks Premium | Sem suporte |
Espaços de armazenamento | Suportado |         
Criptografia em repouso (SSE) | Suportado | Para contas de armazenamento de cache e de destino, você pode selecionar uma conta de armazenamento habilitada para SSE.     
ADE (Azure Disk Encryption) | Sem suporte |
Adição/remoção de disco a quente | Sem suporte | Se você adicionar ou remover um disco de dados da VM, precisará desabilitar a replicação e habilitá-la novamente na VM.
Exclusão de disco | Sem suporte|   O disco temporário é excluído por padrão.
LRS | Suportado |
GRS | Suportado |
RA-GRS | Suportado |
ZRS | Sem suporte |  
Armazenamento Frio e Quente | Sem suporte | Não há suporte para discos de máquina virtual no armazenamento frio e quente

>[!IMPORTANT]
> Siga as [diretrizes de armazenamento](../storage/common/storage-scalability-targets.md#scalability-targets-for-virtual-machine-disks) para as máquinas virtuais de origem do Azure para evitar problemas de desempenho. Se você seguir as configurações padrão, o Site Recovery criará as contas de armazenamento necessárias com base na configuração de fonte. Caso você personalize e selecione suas próprias configurações, siga o (../storage/common/storage-scalability-targets.md#scalability-targets-for-virtual-machine-disks) como as VMs de origem.

## <a name="support-for-network-configuration"></a>Suporte para configuração de Rede
**Configuração** | **Com suporte/Sem suporte** | **Comentários**
--- | --- | ---
NIC (adaptador de rede) | Até o número máximo de NICs com suporte em um tamanho específico de VM do Azure | As NICs são criadas quando a VM é criada como parte de uma operação de Failover de teste ou de Failover. O número de NICs na VM de failover depende do número de NICs que a VM de origem tem no momento da habilitação da replicação. Se você adicionar ou remover uma NIC depois de habilitar a replicação, isso não afetará a contagem de NICs na VM de failover.
Balanceador de Carga de Internet | Suportado | Você precisa associar o balanceador de carga pré-configurado usando um script de automação do Azure em um plano de recuperação.
Balanceador de carga interno | Suportado | Você precisa associar o balanceador de carga pré-configurado usando um script de automação do Azure em um plano de recuperação.
IP público| Suportado | Você precisa associar um IP público já existente a NIC ou criar um e associá-lo a NIC usando um script de automação do Azure em um plano de recuperação.
NSG na NIC (Resource Manager)| Suportado | Você precisa associar o NSG a NIC usando um script de automação do Azure em um plano de recuperação.  
NSG na sub-rede (Resource Manager e Clássico)| Suportado | Você precisa associar o NSG a NIC usando um script de automação do Azure em um plano de recuperação.
NSG na VM (Clássico)| Suportado | Você precisa associar o NSG a NIC usando um script de automação do Azure em um plano de recuperação.
IP Reservado (IP Estático)/Reter o IP de origem | Suportado | Se a NIC da VM de origem tiver a configuração de IP estático e a sub-rede de destino tiver o mesmo IP disponível, ela será atribuída à VM de failover. Se a sub-rede de destino não tiver o mesmo IP disponível, um dos IPs disponíveis na sub-rede será reservado para essa VM. Você pode especificar um IP fixo de sua escolha em “Item replicado > Configurações > Computação e Rede > Adaptadores de rede”. Você pode selecionar a NIC e especificar a sub-rede e o IP de sua escolha.
IP Dinâmico| Suportado | Se a NIC da VM de origem tiver a configuração de IP dinâmico, a NIC da VM de failover também será Dinâmica por padrão. Você pode especificar um IP fixo de sua escolha em “Item replicado > Configurações > Computação e Rede > Adaptadores de rede”. Você pode selecionar a NIC e especificar a sub-rede e o IP de sua escolha.
Integração do Gerenciador de Tráfego | Suportado | Você pode pré-configurar o Gerenciador de Tráfego de tal forma que o tráfego seja encaminhado para o ponto de extremidade na região de origem com regularidade e para o ponto de extremidade na região de destino em caso de failover.
DNS gerenciado do Azure | Suportado |
DNS Personalizado  | Suportado |    
Proxy não autenticado | Suportado | Consulte o [documento de diretrizes de rede.](site-recovery-azure-to-azure-networking-guidance.md)    
Proxy autenticado | Sem suporte | Se a VM estiver usando um proxy autenticado para a conectividade de saída, ela não poderá ser replicada com o Azure Site Recovery.    
VPN Site a Site com local (com ou sem o ExpressRoute)| Suportado | Verifique se as UDRs e os NSGs são configurados de tal forma que o tráfego do Site Recovery não seja encaminhado para o local. Consulte o [documento de diretrizes de rede.](site-recovery-azure-to-azure-networking-guidance.md)  
Conexão VNET a VNET | Suportado | Consulte o [documento de diretrizes de rede.](site-recovery-azure-to-azure-networking-guidance.md)  


## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre as [diretrizes de rede para replicação de VMs do Azure](site-recovery-azure-to-azure-networking-guidance.md)
- Inicie a proteção de suas cargas de trabalho [replicando VMs do Azure](site-recovery-azure-to-azure.md)
