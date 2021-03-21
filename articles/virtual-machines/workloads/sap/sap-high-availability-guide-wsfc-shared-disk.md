---
title: Instância do SAP ASCS/SCS do cluster no WSFC usando disco compartilhado no Azure | Microsoft Docs
description: Saiba como clusterizar uma instância do SAP ASCS/SCS em um cluster de failover do Windows usando um disco compartilhado de cluster.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: f6fb85f8-c77a-4af1-bde8-1de7e4425d2e
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/16/2020
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c903cf06981e1336ae30942775de11d09bb1299b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101675353"
---
# <a name="cluster-an-sap-ascsscs-instance-on-a-windows-failover-cluster-by-using-a-cluster-shared-disk-in-azure"></a>Clusterize uma instância do SAP ASCS/SCS em um cluster de failover do Windows usando um disco compartilhado de cluster no Azure

> ![Sistema operacional Windows][Logo_Windows] Windows
>

O Windows Server Failover Clustering é a base de uma instalação do SAP ASCS/SCS de alta disponibilidade e DBMS no Windows.

Um cluster de failover é um grupo de 1+n servidores independentes (nós) que funcionam juntos para aumentar a disponibilidade de aplicativos e serviços. Se ocorrer uma falha de nó, o clustering de failover do Windows Server calculará o número de falhas que podem ocorrer e manterá um cluster íntegro para fornecer serviços e aplicativos. Você pode escolher dentre diferentes modos de quorum para obter o clustering de failover.

## <a name="prerequisites"></a>Pré-requisitos
Antes de iniciar as tarefas neste artigo, examine o seguinte artigo:

* [Arquitetura e Cenários de Alta disponibilidade de Máquinas Virtuais do Azure para SAP NetWeaver][sap-high-availability-architecture-scenarios]


## <a name="windows-server-failover-clustering-in-azure"></a>Clustering de Failover do Windows Server no Azure

O clustering de failover do Windows Server com máquinas virtuais do Azure requer etapas de configuração adicionais. Quando você compila um cluster, precisa definir vários endereços IP e nomes de host virtual para a instância do SAP ASCS/SCS.

### <a name="name-resolution-in-azure-and-the-cluster-virtual-host-name"></a>Resolução de nomes no Azure e nome do host virtual do cluster

A plataforma de nuvem do Azure não oferece a opção de configurar endereços IP virtual, como endereços IPs flutuantes. Você precisa de uma solução alternativa para configurar um endereço IP virtual a fim de alcançar o recurso de cluster na nuvem. 

O serviço Azure Load Balancer fornece um *balanceador de carga interno* para o Azure. Com o balanceador de carga interno, os clientes alcançam o cluster pelo endereço IP virtual do cluster. 

Implante o balanceador de carga interno no grupo de recursos que contém os nós do cluster. Em seguida, configure todas as regras necessárias de encaminhamento de porta usando as portas de investigação do balanceador de carga interno. Os clientes podem se conectar por meio do nome de host virtual. O servidor DNS resolve o endereço IP do cluster e o balanceador de carga interno trata da porta que encaminha para o nó ativo do cluster.

> [!IMPORTANT]
> Não há suporte para IP flutuante em uma configuração de IP secundário de NIC em cenários de balanceamento de carga. Para obter detalhes, consulte [limitações do Azure Load Balancer](../../../load-balancer/load-balancer-multivip-overview.md#limitations). Se você precisar de um endereço IP adicional para a VM, implante uma segunda NIC.  

![Figura 1: Configuração do clustering de failover do Windows Server no Azure sem um disco compartilhado][sap-ha-guide-figure-1001]

_Configuração do Windows Server failover clustering no Azure sem um disco compartilhado_

### <a name="sap-ascsscs-ha-with-cluster-shared-disks"></a>Alta disponibilidade do SAP ASCS/SCS com discos compartilhados do cluster
No Windows, uma instância do SAP ASCS/SCS contém os serviços centrais do SAP, o servidor de mensagens do SAP, os processos de servidor de enfileiramento e os arquivos de host global do SAP. Os arquivos de host global SAP armazenam arquivos centrais para todo o sistema SAP.

Uma instância do SAP ASCS/SCS tem os seguintes componentes:

* Serviços centrais do SAP:
    * Dois processos, um servidor de mensagens e de enfileiramento e um \<ASCS/SCS virtual host name> , que é usado para acessar esses dois processos.
    * Estrutura de arquivos: S:\usr\sap \\ &lt; SID &gt; \ ASCS/SCS\<instance number\>


* Arquivos de host global do SAP:
  * Estrutura de arquivos: S:\usr\sap\\&lt;SID&gt;\SYS\..
  * O compartilhamento do arquivo sapmnt, que habilita o acesso a esses arquivos S:\usr\sap\\&lt;SID&gt;\SYS\.. globais usando o seguinte caminho UNC:

    \\\\<nome do host virtual do ASCS/SCS \> \Sapmnt \\ &lt; SID &gt; \SYS \. ..


![Figura 2: Processos, estrutura de arquivos e compartilhamento de arquivos sapmnt de uma instância do SAP ASCS/SCS][sap-ha-guide-figure-8001]

_Processos, estrutura de arquivos e compartilhamento de arquivos sapmnt de host global de uma instância do SAP ASCS/SCS_

Em uma configuração de alta disponibilidade, você clusteriza instâncias do SAP ASCS/SCS. Nós usamos os *discos compartilhados clusterizados* (unidade S, em nosso exemplo), para posicionarmos os arquivos de host global do SAP ASCS/SCS e do SAP.

![Figura 3: Arquitetura de alta disponibilidade do SAP ASCS/SCS com disco compartilhado][sap-ha-guide-figure-8002]

_Arquitetura de alta disponibilidade do SAP ASCS/SCS com disco compartilhado_


Com a arquitetura de replicação de servidor de enfileiramento 1:
* O mesmo \<ASCS/SCS virtual host name> é usado para acessar a mensagem SAP e enfileirar processos de servidor e os arquivos de host global do SAP por meio do compartilhamento de arquivos sapmnt.
* A mesma unidade de disco S compartilhada de cluster é compartilhada entre eles.  

Com a arquitetura de replicação de servidor de enfileiramento 2: 
* O mesmo \<ASCS/SCS virtual host name> é usado para acessar o processo do servidor de mensagens SAP e os arquivos de host global do SAP por meio do compartilhamento de arquivos sapmnt.
* A mesma unidade de disco S compartilhada de cluster é compartilhada entre eles.
* Há uma separação \<ERS virtual host name> para acessar o processo do servidor de enfileiramento  

![Figura 4: Arquitetura de alta disponibilidade do SAP ASCS/SCS com disco compartilhado][sap-ha-guide-figure-8003]

_Arquitetura de alta disponibilidade do SAP ASCS/SCS com disco compartilhado_

#### <a name="shared-disk-and-enqueue-replication-server"></a>Disco compartilhado e servidor de replicação de enfileiramento 

1. O disco compartilhado é compatível com a arquitetura do Queue Server Replication 1, em que a instância do ERS (Enqueue Replication Server):   

   - Não está clusterizado
   - usa o `localhost` nome
   - é implantado em discos locais em cada um dos nós de cluster

2. O disco compartilhado também tem suporte com a arquitetura de replicação do servidor de enfileiramento 2, em que a instância do ERS2 (Queue Replication Server 2):  

   - está clusterizado
   - usa o nome de host de rede/virtual dedicado
   - precisa que o endereço IP do nome de host virtual ERS seja configurado no Load Balancer interno do Azure, além do endereço IP (A) SCS
   - é implantado em **discos locais** em cada um dos nós clusterizados, portanto, não há necessidade de disco compartilhado

   > [!TIP]
   > Você pode encontrar mais informações sobre o Queue Replication Server 1 e 2 (ERS1 e ERS2) aqui:  
   > [Enfileirar o servidor de replicação em um cluster de failover da Microsoft](https://help.sap.com/viewer/3741bfe0345f4892ae190ee7cfc53d4c/CURRENT_VERSION_SWPM20/en-US/8abd4b52902d4b17a105c2fabdf5c0cf.html)  
   > [Novo replicador de enfileiramento em ambientes de cluster de failover](https://blogs.sap.com/2019/03/19/new-enqueue-replicator-in-failover-cluster-environments/)  

#### <a name="options-for-shared-disk-in-azure-for-sap-workloads"></a>Opções de disco compartilhado no Azure para cargas de trabalho do SAP

Há duas opções de disco compartilhado em um cluster de failover do Windows no Azure:

- [Discos compartilhados do Azure](../../disks-shared.md) – recurso, que permite anexar o disco gerenciado do Azure a várias VMs simultaneamente. 
- Usando o software de terceiros [sios Datakeeper Cluster Edition](https://us.sios.com/products/datakeeper-cluster) para criar um armazenamento espelhado que simula o armazenamento compartilhado do cluster. 

Ao selecionar a tecnologia para o disco compartilhado, tenha em mente as seguintes considerações:

**Disco compartilhado do Azure para cargas de trabalho do SAP**
- Permite anexar o disco gerenciado do Azure a várias VMs simultaneamente, sem a necessidade de software adicional para manter e operar 
- Você estará operando com um único disco compartilhado do Azure em um cluster de armazenamento. Isso tem um impacto sobre a confiabilidade da solução SAP.
- Atualmente, a única implantação com suporte é com o disco Premium compartilhado do Azure no conjunto de disponibilidade. Não há suporte para o disco compartilhado do Azure na implantação zonal.     
- Certifique-se de provisionar o disco Premium do Azure com um tamanho de disco mínimo, conforme especificado em [intervalos de SSD Premium](../../disks-shared.md#disk-sizes) para ser capaz de anexar ao número necessário de VMs simultaneamente (normalmente 2 para cluster de failover do Windows ASCS do SAP). 
- O ultra Disk compartilhado do Azure não tem suporte para cargas de trabalho do SAP, pois não dá suporte à implantação no conjunto de disponibilidade ou na implantação zonal.  
 
**SIOS**
- A solução SIOS fornece replicação de dados síncrona em tempo real entre dois discos
- Com a solução SIOS que você opera com dois discos gerenciados e, se estiver usando conjuntos de disponibilidade ou zonas de disponibilidade, os discos gerenciados ficarão em diferentes clusters de armazenamento. 
- Há suporte para a implantação em zonas de disponibilidade
- Requer a instalação e a operação de software de terceiros, que você precisará comprar Adicionalmente

### <a name="shared-disk-using-azure-shared-disk"></a>Disco compartilhado usando o disco compartilhado do Azure

A Microsoft está oferecendo [discos compartilhados do Azure](../../disks-shared.md), que podem ser usados para implementar alta disponibilidade do SAP ASCS/SCS com uma opção de disco compartilhado.

#### <a name="prerequisites-and-limitations"></a>Pré-requisitos e limitações

No momento, você pode usar os discos do Azure SSD Premium como um disco compartilhado do Azure para a instância do SAP ASCS/SCS. As seguintes limitações estão atualmente em vigor:

-  O [ultra Disk do Azure](../../disks-types.md#ultra-disk) não tem suporte como disco compartilhado do Azure para cargas de trabalho do SAP. Atualmente, não é possível posicionar VMs do Azure, usando o ultra Disk do Azure no conjunto de disponibilidade
-  O [disco compartilhado do Azure](../../disks-shared.md) com discos SSD Premium só tem suporte com VMs no conjunto de disponibilidade. Não há suporte na implantação Zonas de Disponibilidade. 
-  O valor do disco compartilhado do Azure [maxShares](../../disks-shared-enable.md?tabs=azure-cli#disk-sizes) determina quantos nós de cluster podem usar o disco compartilhado. Normalmente, para a instância do SAP ASCS/SCS, você configurará dois nós no cluster de failover do Windows, portanto, o valor de `maxShares` deve ser definido como dois.
-  Todas as VMs de cluster do SAP ASCS/SCS devem ser implantadas no mesmo [grupo de posicionamento de proximidade do Azure](../../windows/proximity-placement-groups.md).   
   Embora você possa implantar VMs de cluster do Windows no conjunto de disponibilidade com o disco compartilhado do Azure sem PPG, o PPG garantirá o fechamento da proximidade física dos discos compartilhados do Azure e das VMs do cluster, portanto, alcançando a latência mais baixa entre as VMs e a camada de armazenamento.    

Para obter mais detalhes sobre as limitações do disco compartilhado do Azure, Examine cuidadosamente a seção [limitações](../../disks-shared.md#limitations) da documentação do disco compartilhado do Azure.

> [!IMPORTANT]
> Ao implantar o cluster de failover do Windows do SAP ASCS/SCS com o disco compartilhado do Azure, lembre-se de que sua implantação estará operando com um único disco compartilhado em um cluster de armazenamento. A instância do SAP ASCS/SCS seria afetada, em caso de problemas com o cluster de armazenamento, em que o disco compartilhado do Azure é implantado.    

> [!TIP]
> Examine o [Guia de planejamento do SAP NetWeaver no Azure](./planning-guide.md) e o [Guia de armazenamento do Azure para cargas de trabalho do SAP](./planning-guide-storage.md) para obter considerações importantes, ao planejar a implantação do SAP.

### <a name="supported-os-versions"></a>Versões compatíveis do sistema operacional

Há suporte para o Windows Server 2016 e 2019 (use as últimas imagens de data center).

É altamente recomendável usar o **Windows Server 2019 datacenter**, como:
- O serviço de cluster de failover do Windows 2019 está ciente do Azure
- Há integração e reconhecimento adicionais de manutenção de host do Azure e experiência aprimorada por meio do monitoramento de eventos de agenda do Azure.
- É possível usar o nome de rede distribuída (é a opção padrão). Portanto, não é necessário ter um endereço IP dedicado para o nome de rede do cluster. Além disso, não é necessário configurar esse endereço IP no Load Balancer interno do Azure. 

### <a name="shared-disks-in-azure-with-sios-datakeeper"></a>Discos compartilhados no Azure com SIOS DataKeeper

Outra opção para o disco compartilhado é usar o software de terceiros SIOS datakeeper Cluster Edition para criar um armazenamento espelhado que simula o armazenamento compartilhado do cluster. A solução SIOS fornece replicação síncrona de dados em tempo real.

Para criar um recurso de disco compartilhado para um cluster:

1. Anexe um disco adicional a cada uma das máquinas virtuais em uma configuração de cluster do Windows.
2. Execute o SIOS DataKeeper Cluster Edition em ambos os nós da máquina virtual.
3. Configure o SIOS DataKeeper Cluster Edition para que ele reflita o conteúdo do volume anexado do disco adicional da máquina virtual de origem para o volume anexado do disco adicional da máquina virtual de destino. O SIOS DataKeeper abstrai os volumes locais de origem e de destino e os apresenta ao clustering de failover do Windows Server como um disco compartilhado.

Saiba mais sobre [SIOS DataKeeper](https://us.sios.com/products/datakeeper-cluster/).

![Figura 5: Configuração do clustering de failover do Windows Server no Azure com o SIOS DataKeeper][sap-ha-guide-figure-1002]

_Configuração do clustering de failover do Windows no Azure com SIOS datakeeper_

> [!NOTE]
> Você não precisa de discos compartilhados de alta disponibilidade com alguns produtos de DBMS, como o SQL Server. O AlwaysOn do SQL Server replica os arquivos de log e dados do DBMS do disco local de um nó do cluster para o disco local de outro nó do cluster. Nesse caso, a configuração de cluster do Windows não precisa de um disco compartilhado.
>

## <a name="next-steps"></a>Próximas etapas

* [Preparar a infraestrutura do Azure para alta disponibilidade do SAP usando o cluster de failover do Windows e o disco compartilhado para a instância SAP ASCS/SCS][sap-high-availability-infrastructure-wsfc-shared-disk]

* [Instalar a alta disponibilidade do SAP NetWeaver em um cluster de failover do Windows e em um disco compartilhado para uma instância do SAP ASCS/SCS][sap-high-availability-installation-wsfc-shared-disk]


[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides

[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md
[ha-guide]:sap-high-availability-guide.md
[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-ha-guide]:sap-high-availability-guide.md
[sap-ha-guide-2]:#42b8f600-7ba3-4606-b8a5-53c4f026da08
[sap-ha-guide-4]:#8ecf3ba0-67c0-4495-9c14-feec1a2255b7
[sap-ha-guide-8]:#78092dbe-165b-454c-92f5-4972bdbef9bf
[sap-ha-guide-8.1]:#c87a8d3f-b1dc-4d2f-b23c-da4b72977489
[sap-ha-guide-8.9]:#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-ha-guide-8.11]:#661035b2-4d0f-4d31-86f8-dc0a50d78158
[sap-ha-guide-8.12]:#0d67f090-7928-43e0-8772-5ccbf8f59aab
[sap-ha-guide-8.12.1]:#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79
[sap-ha-guide-8.12.3]:#5c8e5482-841e-45e1-a89d-a05c0907c868
[sap-ha-guide-8.12.3.1]:#1c2788c3-3648-4e82-9e0d-e058e475e2a3
[sap-ha-guide-8.12.3.2]:#dd41d5a2-8083-415b-9878-839652812102
[sap-ha-guide-8.12.3.3]:#d9c1fc8e-8710-4dff-bec2-1f535db7b006
[sap-ha-guide-9]:#a06f0b49-8a7a-42bf-8b0d-c12026c5746b
[sap-ha-guide-9.1]:#31c6bd4f-51df-4057-9fdf-3fcbc619c170
[sap-ha-guide-9.1.1]:#a97ad604-9094-44fe-a364-f89cb39bf097

[sap-ha-multi-sid-guide]:sap-high-availability-multi-sid.md (Configuração de alta disponibilidade de várias SID do SAP)

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png

[sap-ha-guide-figure-1000]:./media/virtual-machines-shared-sap-high-availability-guide/1000-wsfc-for-sap-ascs-on-azure.png
[sap-ha-guide-figure-1001]:./media/virtual-machines-shared-sap-high-availability-guide/1001-wsfc-on-azure-ilb.png
[sap-ha-guide-figure-1002]:./media/virtual-machines-shared-sap-high-availability-guide/1002-wsfc-sios-on-azure-ilb.png
[sap-ha-guide-figure-2000]:./media/virtual-machines-shared-sap-high-availability-guide/2000-wsfc-sap-as-ha-on-azure.png
[sap-ha-guide-figure-2001]:./media/virtual-machines-shared-sap-high-availability-guide/2001-wsfc-sap-ascs-ha-on-azure.png
[sap-ha-guide-figure-2003]:./media/virtual-machines-shared-sap-high-availability-guide/2003-wsfc-sap-dbms-ha-on-azure.png
[sap-ha-guide-figure-2004]:./media/virtual-machines-shared-sap-high-availability-guide/2004-wsfc-sap-ha-e2e-archit-template1-on-azure.png
[sap-ha-guide-figure-2005]:./media/virtual-machines-shared-sap-high-availability-guide/2005-wsfc-sap-ha-e2e-arch-template2-on-azure.png

[sap-ha-guide-figure-3000]:./media/virtual-machines-shared-sap-high-availability-guide/3000-template-parameters-sap-ha-arm-on-azure.png
[sap-ha-guide-figure-3001]:./media/virtual-machines-shared-sap-high-availability-guide/3001-configuring-dns-servers-for-Azure-vnet.png
[sap-ha-guide-figure-3002]:./media/virtual-machines-shared-sap-high-availability-guide/3002-configuring-static-IP-address-for-network-card-of-each-vm.png
[sap-ha-guide-figure-3003]:./media/virtual-machines-shared-sap-high-availability-guide/3003-setup-static-ip-address-ilb-for-ascs-instance.png
[sap-ha-guide-figure-3004]:./media/virtual-machines-shared-sap-high-availability-guide/3004-default-ascs-scs-ilb-balancing-rules-for-azure-ilb.png
[sap-ha-guide-figure-3005]:./media/virtual-machines-shared-sap-high-availability-guide/3005-changing-ascs-scs-default-ilb-rules-for-azure-ilb.png
[sap-ha-guide-figure-3006]:./media/virtual-machines-shared-sap-high-availability-guide/3006-adding-vm-to-domain.png
[sap-ha-guide-figure-3007]:./media/virtual-machines-shared-sap-high-availability-guide/3007-config-wsfc-1.png
[sap-ha-guide-figure-3008]:./media/virtual-machines-shared-sap-high-availability-guide/3008-config-wsfc-2.png
[sap-ha-guide-figure-3009]:./media/virtual-machines-shared-sap-high-availability-guide/3009-config-wsfc-3.png
[sap-ha-guide-figure-3010]:./media/virtual-machines-shared-sap-high-availability-guide/3010-config-wsfc-4.png
[sap-ha-guide-figure-3011]:./media/virtual-machines-shared-sap-high-availability-guide/3011-config-wsfc-5.png
[sap-ha-guide-figure-3012]:./media/virtual-machines-shared-sap-high-availability-guide/3012-config-wsfc-6.png
[sap-ha-guide-figure-3013]:./media/virtual-machines-shared-sap-high-availability-guide/3013-config-wsfc-7.png
[sap-ha-guide-figure-3014]:./media/virtual-machines-shared-sap-high-availability-guide/3014-config-wsfc-8.png
[sap-ha-guide-figure-3015]:./media/virtual-machines-shared-sap-high-availability-guide/3015-config-wsfc-9.png
[sap-ha-guide-figure-3016]:./media/virtual-machines-shared-sap-high-availability-guide/3016-config-wsfc-10.png
[sap-ha-guide-figure-3017]:./media/virtual-machines-shared-sap-high-availability-guide/3017-config-wsfc-11.png
[sap-ha-guide-figure-3018]:./media/virtual-machines-shared-sap-high-availability-guide/3018-config-wsfc-12.png
[sap-ha-guide-figure-3019]:./media/virtual-machines-shared-sap-high-availability-guide/3019-assign-permissions-on-share-for-cluster-name-object.png
[sap-ha-guide-figure-3020]:./media/virtual-machines-shared-sap-high-availability-guide/3020-change-object-type-include-computer-objects.png
[sap-ha-guide-figure-3021]:./media/virtual-machines-shared-sap-high-availability-guide/3021-check-box-for-computer-objects.png
[sap-ha-guide-figure-3022]:./media/virtual-machines-shared-sap-high-availability-guide/3022-set-security-attributes-for-cluster-name-object-on-file-share-quorum.png
[sap-ha-guide-figure-3023]:./media/virtual-machines-shared-sap-high-availability-guide/3023-call-configure-cluster-quorum-setting-wizard.png
[sap-ha-guide-figure-3024]:./media/virtual-machines-shared-sap-high-availability-guide/3024-selection-screen-different-quorum-configurations.png
[sap-ha-guide-figure-3025]:./media/virtual-machines-shared-sap-high-availability-guide/3025-selection-screen-file-share-witness.png
[sap-ha-guide-figure-3026]:./media/virtual-machines-shared-sap-high-availability-guide/3026-define-file-share-location-for-witness-share.png
[sap-ha-guide-figure-3027]:./media/virtual-machines-shared-sap-high-availability-guide/3027-successful-reconfiguration-cluster-file-share-witness.png
[sap-ha-guide-figure-3028]:./media/virtual-machines-shared-sap-high-availability-guide/3028-install-dot-net-framework-35.png
[sap-ha-guide-figure-3029]:./media/virtual-machines-shared-sap-high-availability-guide/3029-install-dot-net-framework-35-progress.png
[sap-ha-guide-figure-3030]:./media/virtual-machines-shared-sap-high-availability-guide/3030-sios-installer.png
[sap-ha-guide-figure-3031]:./media/virtual-machines-shared-sap-high-availability-guide/3031-first-screen-sios-data-keeper-installation.png
[sap-ha-guide-figure-3032]:./media/virtual-machines-shared-sap-high-availability-guide/3032-data-keeper-informs-service-be-disabled.png
[sap-ha-guide-figure-3033]:./media/virtual-machines-shared-sap-high-availability-guide/3033-user-selection-sios-data-keeper.png
[sap-ha-guide-figure-3034]:./media/virtual-machines-shared-sap-high-availability-guide/3034-domain-user-sios-data-keeper.png
[sap-ha-guide-figure-3035]:./media/virtual-machines-shared-sap-high-availability-guide/3035-provide-sios-data-keeper-license.png
[sap-ha-guide-figure-3036]:./media/virtual-machines-shared-sap-high-availability-guide/3036-data-keeper-management-config-tool.png
[sap-ha-guide-figure-3037]:./media/virtual-machines-shared-sap-high-availability-guide/3037-tcp-ip-address-first-node-data-keeper.png
[sap-ha-guide-figure-3038]:./media/virtual-machines-shared-sap-high-availability-guide/3038-create-replication-sios-job.png
[sap-ha-guide-figure-3039]:./media/virtual-machines-shared-sap-high-availability-guide/3039-define-sios-replication-job-name.png
[sap-ha-guide-figure-3040]:./media/virtual-machines-shared-sap-high-availability-guide/3040-define-sios-source-node.png
[sap-ha-guide-figure-3041]:./media/virtual-machines-shared-sap-high-availability-guide/3041-define-sios-target-node.png
[sap-ha-guide-figure-3042]:./media/virtual-machines-shared-sap-high-availability-guide/3042-define-sios-synchronous-replication.png
[sap-ha-guide-figure-3043]:./media/virtual-machines-shared-sap-high-availability-guide/3043-enable-sios-replicated-volume-as-cluster-volume.png
[sap-ha-guide-figure-3044]:./media/virtual-machines-shared-sap-high-availability-guide/3044-data-keeper-synchronous-mirroring-for-SAP-gui.png
[sap-ha-guide-figure-3045]:./media/virtual-machines-shared-sap-high-availability-guide/3045-replicated-disk-by-data-keeper-in-wsfc.png
[sap-ha-guide-figure-3046]:./media/virtual-machines-shared-sap-high-availability-guide/3046-dns-entry-sap-ascs-virtual-name-ip.png
[sap-ha-guide-figure-3047]:./media/virtual-machines-shared-sap-high-availability-guide/3047-dns-manager.png
[sap-ha-guide-figure-3048]:./media/virtual-machines-shared-sap-high-availability-guide/3048-default-cluster-probe-port.png
[sap-ha-guide-figure-3049]:./media/virtual-machines-shared-sap-high-availability-guide/3049-cluster-probe-port-after.png
[sap-ha-guide-figure-3050]:./media/virtual-machines-shared-sap-high-availability-guide/3050-service-type-ers-delayed-automatic.png
[sap-ha-guide-figure-5000]:./media/virtual-machines-shared-sap-high-availability-guide/5000-wsfc-sap-sid-node-a.png
[sap-ha-guide-figure-5001]:./media/virtual-machines-shared-sap-high-availability-guide/5001-sios-replicating-local-volume.png
[sap-ha-guide-figure-5002]:./media/virtual-machines-shared-sap-high-availability-guide/5002-wsfc-sap-sid-node-b.png
[sap-ha-guide-figure-5003]:./media/virtual-machines-shared-sap-high-availability-guide/5003-sios-replicating-local-volume-b-to-a.png

[sap-ha-guide-figure-6003]:./media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png


[sap-ha-guide-figure-8001]:./media/virtual-machines-shared-sap-high-availability-guide/8001.png
[sap-ha-guide-figure-8002]:./media/virtual-machines-shared-sap-high-availability-guide/8002.png
[sap-ha-guide-figure-8003]:./media/virtual-machines-shared-sap-high-availability-guide/8003.png
[sap-ha-guide-figure-8004]:./media/virtual-machines-shared-sap-high-availability-guide/8004.png
[sap-ha-guide-figure-8005]:./media/virtual-machines-shared-sap-high-availability-guide/8005.png
[sap-ha-guide-figure-8006]:./media/virtual-machines-shared-sap-high-availability-guide/8006.png
[sap-ha-guide-figure-8007]:./media/virtual-machines-shared-sap-high-availability-guide/8007.png
[sap-ha-guide-figure-8008]:./media/virtual-machines-shared-sap-high-availability-guide/8008.png
[sap-ha-guide-figure-8009]:./media/virtual-machines-shared-sap-high-availability-guide/8009.png
[sap-ha-guide-figure-8010]:./media/virtual-machines-shared-sap-high-availability-guide/8010.png
[sap-ha-guide-figure-8011]:./media/virtual-machines-shared-sap-high-availability-guide/8011.png
[sap-ha-guide-figure-8012]:./media/virtual-machines-shared-sap-high-availability-guide/8012.png
[sap-ha-guide-figure-8013]:./media/virtual-machines-shared-sap-high-availability-guide/8013.png
[sap-ha-guide-figure-8014]:./media/virtual-machines-shared-sap-high-availability-guide/8014.png
[sap-ha-guide-figure-8015]:./media/virtual-machines-shared-sap-high-availability-guide/8015.png
[sap-ha-guide-figure-8016]:./media/virtual-machines-shared-sap-high-availability-guide/8016.png
[sap-ha-guide-figure-8017]:./media/virtual-machines-shared-sap-high-availability-guide/8017.png
[sap-ha-guide-figure-8018]:./media/virtual-machines-shared-sap-high-availability-guide/8018.png
[sap-ha-guide-figure-8019]:./media/virtual-machines-shared-sap-high-availability-guide/8019.png
[sap-ha-guide-figure-8020]:./media/virtual-machines-shared-sap-high-availability-guide/8020.png
[sap-ha-guide-figure-8021]:./media/virtual-machines-shared-sap-high-availability-guide/8021.png
[sap-ha-guide-figure-8022]:./media/virtual-machines-shared-sap-high-availability-guide/8022.png
[sap-ha-guide-figure-8023]:./media/virtual-machines-shared-sap-high-availability-guide/8023.png
[sap-ha-guide-figure-8024]:./media/virtual-machines-shared-sap-high-availability-guide/8024.png
[sap-ha-guide-figure-8025]:./media/virtual-machines-shared-sap-high-availability-guide/8025.png


[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps-md%2Fazuredeploy.json

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md