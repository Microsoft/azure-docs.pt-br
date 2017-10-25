---
title: "Pré-requisitos para replicação no Azure usando o Azure Site Recovery | Microsoft Docs"
description: "Saiba mais sobre os pré-requisitos para replicação de VMs e computadores físicos no Azure usando o serviço do Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rajani-janaki-ram
manager: jwhit
editor: tysonn
ms.assetid: e24eea6c-50a7-4cd5-aab4-2c5c4d72ee2d
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/23/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: 1868e5fd0427a5e1b1eeed244c80a570a39eb6a9
ms.openlocfilehash: 490833c14b6856cdaf6f6bfd2f67ce54fb0414a2
ms.contentlocale: pt-br
ms.lasthandoff: 09/19/2017

---

#  <a name="prerequisites-for-replication-from-on-premises-to-azure-by-using-site-recovery"></a>Pré-requisitos para replicação do local para o Azure usando o Site Recovery

> [!div class="op_single_selector"]
> * [Replicar do Azure para o Azure](site-recovery-azure-to-azure-prereq.md)
> * [Replicar do local para o Azure](site-recovery-prereq.md)

O Azure Site Recovery pode contribuir para sua estratégia de BCDR (continuidade de negócios e recuperação de desastre) orquestrando a replicação uma VM (máquina virtual) do Azure para outra região do Azure. O Site Recovery também replica os servidores físicos locais e VMs para a nuvem (Azure) ou para um datacenter secundário. Em caso de interrupção em seu local primário, você pode fazer failover em um local secundário para manter os aplicativos e cargas de trabalho disponíveis. Você pode fazer failback para seu local primário quando este retomar as operações normais. Para obter mais informações sobre o Site Recovery, consulte [O que é o Site Recovery?](site-recovery-overview.md).

Neste artigo, resumimos os pré-requisitos para iniciar a replicação do Site Recovery de um computador local para o Azure.

Poste seus comentários na parte inferior deste artigo. Você também pode fazer perguntas técnicas no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="azure-requirements"></a>Requisitos do Azure

**Requisito** | **Detalhes**
--- | ---
**Conta do Azure** | Uma [conta do Microsoft Azure](http://azure.microsoft.com/). Você pode começar com uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
**Serviço do Site Recovery** | Para obter mais informações sobre os preços do serviço do Azure Site Recovery, consulte [Preços do Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/). |
**Armazenamento do Azure** | Você precisa de uma conta de Armazenamento do Azure para armazenar os dados replicados. A conta de armazenamento deve estar na mesma região que o cofre dos Serviços de Recuperação do Azure. VMs do Azure são criadas quando ocorre um failover.<br/><br/> Dependendo do modelo de recurso que você deseja usar para o failover de VM do Azure, você poderá configurar uma conta usando o [modelo de implantação do Azure Resource Manager](../storage/common/storage-create-storage-account.md) ou o [modelo clássico de implantação](../storage/common/storage-create-storage-account.md).<br/><br/>Você pode usar [armazenamento com redundância geográfica](../storage/common/storage-redundancy.md#geo-redundant-storage) ou armazenamento com redundância local. Recomendamos usar armazenamento com redundância geográfica. Com o armazenamento com redundância geográfica, os dados são resilientes em caso de interrupção ou se a região primária não puder ser recuperada.<br/><br/> Você pode usar uma conta de armazenamento padrão do Azure ou pode usar o Armazenamento Premium do Azure. O [Armazenamento Premium](https://docs.microsoft.com/azure/storage/storage-premium-storage) normalmente é usado para VMs que precisam de alto desempenho de E/S e baixa latência consistentemente. Com o Armazenamento Premium, uma VM pode hospedar cargas de trabalho com E/S intensivo. Se você usar o Armazenamento Premium para os dados replicados, também precisará de uma conta de Armazenamento Standard. Uma conta de Armazenamento Standard armazena os logs de replicação que capturam as alterações contínuas nos dados locais.<br/><br/>
**Limitações de armazenamento** | Não é possível mover contas de armazenamento usadas no Site Recovery para um grupo de recursos diferente, nem movê-la ou usá-la em outra assinatura.<br/><br/> No momento, não há suporte para replicação em contas de Armazenamento Premium na Índia Central e no Sul da Índia.
**Rede do Azure** | Você precisa de uma rede do Azure, para a qual as VMs do Azure criadas após o failover se conectarão. A rede do Azure deve estar na mesma região do que o cofre dos Serviços de Recuperação.<br/><br/> No Portal do Azure, é possível criar uma rede do Azure usando o [modelo de implantação do Resource Manager](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) ou [modelo de implantação clássico](../virtual-network/virtual-networks-create-vnet-classic-pportal.md).<br/><br/> Se você replicar do System Center Virtual Machine Manager (VMM) para o Azure, será necessário definir o mapeamento de rede entre as redes de VM do VMM e do Azure. Isso garante que as VMs do Azure se conectem às redes apropriadas após o failover.
**Limitações de rede** | Não é possível mover contas de rede usadas no Site Recovery para um grupo de recursos diferente, nem movê-la ou usá-la com outra assinatura.
**Mapeamento de rede** | Se você replicar VMs do Microsoft Hyper-V nas nuvens do VMM, será necessário definir o mapeamento de rede. Isso garante que as VMs do Azure se conectem às redes apropriadas quando forem criadas após o failover.

> [!NOTE]
> As seções a seguir descrevem os pré-requisitos de vários componentes do ambiente do cliente. Para saber mais sobre o suporte para configurações específicas, consulte a [matriz de suporte](site-recovery-support-matrix.md).
>

## <a name="disaster-recovery-of-vmware-vms-or-physical-windows-or-linux-servers-to-azure"></a>Recuperação de desastre de VMs VMware ou servidores físicos do Windows ou Linux para o Azure
Os seguintes componentes são necessários para recuperação de desastre de VMs do VMware ou de servidores físicos do Windows ou Linux. Esses componentes são adicionais aos descritos nos [requisitos do Azure](#azure-requirements).


### <a name="configuration-server-or-additional-process-server"></a>Servidor de configuração ou servidor em processo adicional

Defina um computador local como o servidor de configuração para orquestrar a comunicação entre o site local e o Azure. A tabela abaixo aborda os requisitos do requisitos e de software de uma máquina virtual que pode ser configurada como servidor de configuração.

> [!IMPORTANT]
> Ao implantar um Servidor de Configuração para proteger máquinas virtuais VMware, recomendamos que você implante-o como máquina virtual **altamente disponível (HA)**.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

### <a name="vmware-vcenter-or-vsphere-host-prerequisites"></a>Pré-requisitos de host do VMware vCenter ou vSphere

| **Componente** | **Requisitos** |
| --- | --- |
| **vSphere** | Você precisa de um ou mais hipervisores do VMware vSphere.<br/><br/>Os hipervisores devem executar o vSphere versão 6.0, 5.5 ou 5.1 com as últimas atualizações.<br/><br/>Recomendamos que os hosts vSphere e os servidores vCenter estejam ambos na mesma rede que o servidor em processo. A menos que você tenha configurado um servidor de processo dedicado, essa é a rede na qual o servidor de configuração está localizado. |
| **vCenter** | Recomendamos que você implante um servidor VMware vCenter para gerenciar seus hosts vSphere. Ele deve executar o vCenter versão 6.0 ou 5.5 com as atualizações mais recentes.<br/><br/>**Limitação**: o Site Recovery não dá suporte à replicação entre instâncias do vCenter vMotion. Também não há suporte para DRS e vMotion de Armazenamento em uma VM de destino mestre após uma operação de nova proteção.|

### <a name="replicated-machine-prerequisites"></a>Pré-requisitos de computadores replicados

| **Componente** | **Requisitos** |
| --- | --- |
| **Computadores locais** (VMs VMware) | As VMs replicadas devem ter as ferramentas de VMware instaladas e em execução.<br/><br/> As VMs devem atender aos [Pré-requisitos do Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements) para a criação de uma VM do Azure.<br/><br/>A capacidade do disco em cada computador protegido não pode passar de 1.023 GB. <br/><br/>Um mínimo de 2 GB de espaço disponível na unidade de instalação é necessário para a instalação do componente.<br/><br/>Se você desejar habilitar a consistência de múltiplas VMs, a porta 20004 deverá ser aberta no firewall local da VM.<br/><br/>Os nomes dos computadores devem conter entre 1 e 63 caracteres (você pode usar letras, números e hifens). O nome deve começar com uma letra ou número e terminar com uma letra ou número. <br/><br/>Depois de habilitar a replicação em um computador, você poderá modificar o nome do Azure.<br/><br/> |
| **Computadores Windows** (físicos ou VMware) | O computador deve executar um dos seguintes sistemas de operacionais de 64 bits com suporte: <br/>– Windows Server 2012 R2<br/>– Windows Server 2012<br/>– Windows Server 2008 R2 com SP1 ou uma versão posterior<br/><br/> O sistema operacional deve ser instalado na unidade C. O disco do sistema operacional deve ser um disco básico do Windows e não dinâmico. O disco de dados pode ser dinâmico.<br/><br/>|
| **Computadores com Linux** (físicos ou VMware) | O computador deve executar um dos seguintes sistemas de operacionais de 64 bits com suporte: <br/>- Red Hat Enterprise Linux 5.2 a 5.11, 6.1 a 6.9, 7.0 a 7.3<br/>- CentOS 5.2 a 5.11, 6.1 a 6.9, 7.0 a 7.3<br/>– Servidor Ubuntu 14.04 LTS (para ver uma lista das versões do kernel para Ubuntu com suporte, consulte [sistemas operacionais com suporte](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions))<br/>- Servidor Ubuntu 16.04 LTS (para obter uma lista das versões do kernel para Ubuntu com suporte, consulte [sistemas operacionais com suporte](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions))<br/>- Debian 7 ou Debian 8<br/>– Oracle Enterprise Linux 6.5 ou 6.4, executando kernel compatível com Red Hat ou UEK3 (Unbreakable Enterprise Kernel Versão 3)<br/>– SUSE Linux Enterprise Server 11 SP4 ou SUSE Linux Enterprise Server 11 SP3<br/><br/>Seus arquivos /etc/hosts nos computadores protegidos devem conter entradas que mapeiam o nome do host local para endereços IP associados a todos os adaptadores de rede.<br/><br/>Após o failover, se você quiser se conectar a uma VM do Azure que executa Linux usando um cliente SSH (Secure Shell), verifique se o serviço SSH no computador protegido está definido para iniciar automaticamente ao inciar o sistema. Verifique também que as regras de firewall permitem uma conexão SSH no computador protegido.<br/><br/>O nome do host, os pontos de montagem, os nomes dos dispositivos, os caminhos do sistema Linux e os nomes dos arquivos (por exemplo: /etc/ e /usr) devem estar somente em inglês.<br/><br/>Os diretórios a seguir (se configurados como partições ou sistemas de arquivos separados) devem estar no mesmo disco (o disco do sistema operacional) no servidor de origem:<br/>- / (root)<br/>- /boot<br/>- /usr<br/>- /usr/local<br/>- /var<br/>- /etc<br/><br/>No momento, não há suporte para os recursos de XFS v5, como soma de verificação de metadados, pelo Site Recovery em sistemas de arquivos XFS. Verifique se seus sistemas de arquivos XFS não estão usando recursos da v5. Você pode usar o utilitário xfs_info para verificar o super-bloco XFS da partição. Se **ftype** for definido como **1**, os recursos de XFS v5 estarão sendo usados.<br/><br/>Em servidores Red Hat Enterprise Linux 7 e CentOS 7, o utilitário lsof deve estar instalado e disponível.<br/><br/>


## <a name="disaster-recovery-of-hyper-v-vms-to-azure-no-vmm"></a>Recuperação de desastre de VMs do Hyper-V para o Azure (sem VMM)

Os seguintes componentes são necessários para recuperação de desastre de VMs do Hyper-V em nuvens do VMM. Esses componentes são adicionais aos descritos nos [requisitos do Azure](#azure-requirements).

| **Pré-requisito** | **Detalhes** |
| --- | --- |
| **Host Hyper-V** |Um ou mais servidores locais devem executar o Windows Server 2012 R2 com a função de Hyper-V e as atualizações mais recentes habilitadas ou o Microsoft Hyper-V Server 2012 R2.<br/><br/>Servidores Hyper-V devem ter uma ou mais VMs.<br/><br/>Os servidores Hyper-V devem estar conectados à Internet, diretamente ou por meio de um proxy.<br/><br/>Servidores Hyper-V devem ter as correções descritas no artigo da Base de Dados de Conhecimento [2961977](https://support.microsoft.com/kb/2961977) instaladas.
|**Provedor e agente**| Durante a implantação do Site Recovery, você instalará o provedor do Azure Site Recovery. A instalação do provedor também instalará o Agente dos Serviços de Recuperação do Azure em cada servidor Hyper-V que executar as VMs que você deseja proteger. <br/><br/>Todos os servidores Hyper-V em um cofre do Site Recovery devem ter as mesmas versões de provedor e de agente.<br/><br/>O provedor precisará se conectar ao Site Recovery pela Internet. O tráfego pode ser enviado diretamente ou por meio de um proxy. Não há suporte para o proxy com base em HTTPS. O servidor proxy deve permitir o acesso para as seguintes URLs:<br/><br/> [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]<br/><br/>Se você tiver regras de firewall baseadas em endereço IP no servidor, verifique se as regras permitem a comunicação com o Azure.<br/><br/> Permita os [intervalos de IP do datacenter do Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) e HTTPS (porta 443).<br/><br/> Permita os intervalos de endereço IP para a região do Azure da sua assinatura e para o Oeste dos EUA (usados para controle de acesso e gerenciamento de identidade).


## <a name="disaster-recovery-of-hyper-v-vms-in-vmm-clouds-to-azure"></a>Recuperação de desastre de VMs Hyper-V em nuvens do VMM para o Azure

Os seguintes componentes são necessários para recuperação de desastre de VMs do Hyper-V em nuvens do VMM. Esses componentes são adicionais aos descritos nos [requisitos do Azure](#azure-requirements).

| **Pré-requisito** | **Detalhes** |
| --- | --- |
| **Virtual Machine Manager** |Você deverá ter um ou mais servidores VMM em execução no System Center 2012 R2 ou uma versão posterior. Cada servidor VMM deve ter uma ou mais nuvens configuradas. <br/><br/>Uma nuvem deve ter:<br/>- Um ou mais grupos de hosts do VMM.<br/>- Um ou mais servidores host Hyper-V ou clusters em cada grupo de hosts.<br/><br/>Para obter mais informações sobre como configurar nuvens do VMM, consulte [Como criar uma nuvem no Virtual Machine Manager 2012](http://social.technet.microsoft.com/wiki/contents/articles/2729.how-to-create-a-cloud-in-vmm-2012.aspx). |
| **Hyper-V** |Os servidores de host Hyper-V devem executar pelo menos o Windows Server 2012 R2 com a função Hyper-V habilitada ou o Microsoft Hyper-V Server 2012 R2. As atualizações mais recentes devem estar instaladas.<br/><br/> Um servidor Hyper-V deve ter uma ou mais VMs.<br/><br/> Um servidor ou cluster de host Hyper-V que inclui as VMs que você deseja replicar deve ser gerenciado em uma nuvem do VMM.<br/><br/>Os servidores Hyper-V devem estar conectados à Internet, diretamente ou por meio de um proxy.<br/><br/>Servidores Hyper-V devem ter as correções descritas no artigo da Base de Dados de Conhecimento [2961977](https://support.microsoft.com/kb/2961977) instaladas.<br/><br/>Os servidores de host do Hyper-V precisam de acesso à Internet para a replicação de dados no Azure. |
| **Provedor e agente** |Durante a implantação do Azure Site Recovery, instale o Provedor do Azure Site Recovery no servidor VMM. Instale o Agente dos Serviços de Recuperação em hosts do Hyper-V. O provedor e o agente precisam se conectar ao Azure diretamente pela Internet ou por meio de um proxy. Não há suporte para um proxy com base em HTTPS. O servidor proxy no servidor do VMM e os hosts do Hyper-V devem permitir o acesso a: <br/><br/>[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)] <br/><br/>Se você tiver regras de firewall baseadas em endereço IP no servidor de VMM, verifique se as regras permitem a comunicação com o Azure.<br/><br/> Permita os [intervalos de IP do datacenter do Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) e HTTPS (porta 443).<br/><br/>Permita os intervalos de endereço IP para a região do Azure da sua assinatura e para o Oeste dos EUA (usados para controle de acesso e gerenciamento de identidade).<br/><br/> |

### <a name="replicated-machine-prerequisites"></a>Pré-requisitos de computadores replicados

| **Componente** | **Detalhes** |
| --- | --- |
| **VMs protegidas** | O Site Recovery é compatível com todos os sistemas operacionais que são aceitos pelo [Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx).<br/><br/>As VMs devem atender aos [Pré-requisitos do Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements) para a criação de uma VM do Azure. Os nomes dos computadores devem conter entre 1 e 63 caracteres (você pode usar letras, números e hifens). O nome deve começar com uma letra ou número e terminar com uma letra ou número. <br/><br/>É possível modificar o nome da VM depois de habilitar a replicação para ela. <br/><br/> A capacidade do disco para cada computador protegido não pode passar de 1.023 GB. Uma VM pode ter até 16 discos (portanto, até 16 TB).<br/><br/>


## <a name="disaster-recovery-of-hyper-v-vms-in-vmm-clouds-to-a-customer-owned-site"></a>Recuperação de desastre de VMs do Hyper-V em nuvens de VMM para um site de propriedade do cliente

Os seguintes componentes são necessários para recuperação de desastre de VMs do Hyper-V em nuvens do VMM para um site de propriedade do cliente. Esses componentes são adicionais aos descritos nos [requisitos do Azure](#azure-requirements).

| **Componente** | **Detalhes** |
| --- | --- |
| **Virtual Machine Manager** |  Recomendamos a implantação de um servidor VMM tanto no site primário quanto no site secundário.<br/><br/> Você pode [replicar entre nuvens em um único servidor VMM](site-recovery-vmm-to-vmm.md#prepare-for-single-server-deployment). Para replicar entre nuvens em um único servidor VMM, você precisará de pelo menos duas nuvens configuradas no servidor VMM.<br/><br/> Os servidores VMM devem executar pelo menos o System Center 2012 SP1 com as atualizações mais recentes.<br/><br/> Cada servidor VMM deve ter uma ou mais nuvens. Todas as nuvens devem ter o Perfil de Capacidade do Hyper-V definido. <br/><br/>As nuvens devem ter um ou mais grupos de hosts do VMM. Para obter mais informações sobre como configurar nuvens do VMM, consulte [Preparar para a implantação do Azure Site Recovery](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric). |
| **Hyper-V** | Os servidores Hyper-V devem executar, pelo menos, o Windows Server 2012 com a função Hyper-V habilitada e ter as últimas atualizações instaladas.<br/><br/> Um servidor Hyper-V deve ter uma ou mais VMs.<br/><br/>  Os servidores host do Hyper-V devem estar localizados em grupos de hosts nas nuvens primárias e secundárias do VMM.<br/><br/> Se você executar o Hyper-V em um cluster no Windows Server 2012 R2, é recomendável que você instale a atualização descrita no artigo da Base de Dados de Conhecimento [2961977](https://support.microsoft.com/kb/2961977).<br/><br/> Se você executar o Hyper-V em um cluster no Windows Server 2012 e tiver um cluster baseado em endereço IP estático, o agente de cluster não será criado automaticamente. Você deverá configurar o agente de cluster manualmente. Para obter mais informações sobre o agente de cluster, consulte [Configurar a função de agente de réplica para replicação cluster a cluster](http://social.technet.microsoft.com/wiki/contents/articles/18792.configure-replica-broker-role-cluster-to-cluster-replication.aspx). |
| **Provedor** | Durante a implantação do Site Recovery, instale o provedor do Azure Site Recovery em servidores VMM. O provedor se comunica com o Site Recovery por HTTPS (porta 443) para orquestrar a replicação. A replicação de dados ocorre entre os servidores primário e secundário do Hyper-V por LAN ou por meio de uma conexão VPN.<br/><br/> O provedor em execução no servidor VMM precisa de acesso às seguintes URLs:<br/><br/>[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)] <br/><br/>O provedor do Site Recovery deve permitir a comunicação do firewall de servidores VMM para [intervalos IP do datacenter do Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) e permitir o protocolo HTTPS (porta 443). |


## <a name="url-access"></a>Acesso à URL
Essas URLs devem estar disponíveis no VMware, no VMM e nos servidores host do Hyper-V:

|**URL** | **VMM no VMM** | **VMM para Azure** | **Hyper-V para Azure** | **VMware no Azure** |
|--- | --- | --- | --- | --- |
|``*.accesscontrol.windows.net`` | PERMITIR | Permitir | Permitir | Permitir |
|``*.backup.windowsazure.com`` | Não requerido | Permitir | Permitir | Permitir |
|``*.hypervrecoverymanager.windowsazure.com`` | Permitir | Permitir | Permitir | Permitir |
|``*.store.core.windows.net`` | Permitir | Permitir | Permitir | Permitir |
|``*.blob.core.windows.net`` | Não requerido | Permitir | Permitir | Permitir |
|``https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi`` | Não requerido | Não requerido | Não requerido | Permitir download do SQL |
|``time.windows.com`` | PERMITIR | Permitir | Permitir | Permitir|
|``time.nist.gov`` | Permitir | Permitir | Permitir | PERMITIR |

