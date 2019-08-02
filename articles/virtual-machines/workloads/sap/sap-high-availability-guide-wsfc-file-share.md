---
title: Clustering de uma instância do SAP ASCS/SCS em um cluster de failover do Windows usando o arquivo compartilhado de cluster no Azure | Microsoft Docs
description: Aprenda o Clustering de uma instância do SAP ASCS/SCS em um cluster de failover do Windows usando o arquivo compartilhado de cluster no Azure.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: gwallace
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/24/2019
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 70f9264357ca1a0c1a612481f4254e86f05e41d8
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479190"
---
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[2492395]:https://launchpad.support.sap.com/#/notes/2492395

[kb4025334]:https://support.microsoft.com/help/4025334/windows-10-update-kb4025334

[dv2-series]:https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dv2-series
[ds-series]:https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general

[sap-installation-guides]:http://service.sap.com/instguides

[azure-subscription-service-limits]:../../../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md

[ha-guide]:sap-high-availability-guide.md
[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-guide-wsfc-shared-disk]:sap-high-availability-guide-wsfc-shared-disk.md
[sap-high-availability-guide-wsfc-file-share]:sap-high-availability-guide-wsfc-file-share.md
[sap-ascs-high-availability-multi-sid-wsfc]:sap-ascs-high-availability-multi-sid-wsfc.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-infrastructure-wsfc-file-share]:sap-high-availability-infrastructure-wsfc-file-share.md
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md

[planning-volumes-s2d-choosing-filesystem]:https://docs.microsoft.com/windows-server/storage/storage-spaces/plan-volumes#choosing-the-filesystem
[choosing-the-size-of-volumes-s2d]:https://docs.microsoft.com/windows-server/storage/storage-spaces/plan-volumes#choosing-the-size-of-volumes
[deploy-sofs-s2d-in-azure]:https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment
[s2d-in-win-2016]:https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview
[deep-dive-volumes-in-s2d]:https://blogs.technet.microsoft.com/filecab/2016/08/29/deep-dive-volumes-in-spaces-direct/

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

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/resource-group-overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md

[1869038]:https://launchpad.support.sap.com/#/notes/1869038 

# <a name="cluster-an-sap-ascsscs-instance-on-a-windows-failover-cluster-by-using-a-file-share-in-azure"></a>Clustering de uma instância do SAP ASCS/SCS em um cluster de failover do Windows usando o arquivo compartilhado de cluster no Azure

> ![Windows][Logo_Windows] Windows
>

O Windows Server Failover Clustering é a base de uma instalação do SAP ASCS/SCS de alta disponibilidade e DBMS no Windows.

Um cluster de failover é um grupo servidores 1+n independentes (nós) que trabalham juntos para aumentar a disponibilidade de aplicativos e serviços. Se ocorrer uma falha de nó, o clustering de failover do Windows Server calculará o número de falhas que podem ocorrer e manterá um cluster íntegro para fornecer serviços e aplicativos. Você pode escolher dentre diferentes modos de quorum para obter o clustering de failover.

## <a name="prerequisites"></a>Pré-requisitos
Antes de iniciar as tarefas descritas neste artigo, complete os seguintes pré-requisitos:

* [Arquitetura e cenários de alta disponibilidade de máquinas virtuais do Azure para SAP NetWeaver][sap-high-availability-architecture-scenarios]

> [!IMPORTANT]
> Clustering de instâncias SAP ASCS/SCS com compartilhamento de arquivos tem suporte para produtos SAP NetWeaver 7.40 (e superior) com SAP Kernel 7.49 (e superior).
>


## <a name="windows-server-failover-clustering-in-azure"></a>Clustering de Failover do Windows Server no Azure

Comparados a implantações de nuvem privada ou bare metal, as Máquinas Virtuais do Azure exigem etapas adicionais para configurar o clustering de failover do Windows Server. Quando você compila um cluster, precisa definir vários endereços IP e nomes de host virtual para a instância do SAP ASCS/SCS.

### <a name="name-resolution-in-azure-and-the-cluster-virtual-host-name"></a>Resolução de nomes no Azure e nome do host virtual do cluster

A plataforma de nuvem do Azure não oferece a opção de configurar endereços IP virtual, como endereços IPs flutuantes. Você precisa de uma solução alternativa para configurar um endereço IP virtual a fim de alcançar o recurso de cluster na nuvem. 

O serviço Azure Load Balancer fornece um *balanceador de carga interno* para o Azure. Com o balanceador de carga interno, os clientes alcançam o cluster pelo endereço IP virtual do cluster. 

Implante o balanceador de carga interno no grupo de recursos que contém os nós do cluster. Em seguida, configure todas as regras necessárias de encaminhamento de porta usando as portas de investigação do balanceador de carga interno. Os clientes podem se conectar por meio do nome de host virtual. O servidor DNS resolve o endereço IP do cluster. O balanceador de carga interno trata da porta que encaminha para o nó ativo do cluster.

![Figura 1: Configuração de Clustering de Failover do Windows Server no Azure sem um disco compartilhado][sap-ha-guide-figure-1001]

_**Figura 1:** Configuração de clustering de failover do Windows Server no Azure sem um disco compartilhado_

## <a name="sap-ascsscs-ha-with-file-share"></a>SAP ASCS/SCS HA com compartilhamento de arquivos

SAP desenvolveu uma nova abordagem e alternativa para o cluster de discos compartilhados para fazedr cluster de instância SAP ASCS/SCS no Windows Failover Cluster. Em vez de usar discos de cluster compartilhado, você pode usar um compartilhamento de arquivos SMB para implantar arquivos de host global do SAP.

> [!NOTE]
> O compartilhamento de arquivos SMB é uma alternativa para discos compartilhados de cluster de instâncias SAP ASCS/SCS de cluster.  
>

O que é específico para essa arquitetura é o seguinte:

* Serviços SAP centrais (com processos de estrutura e mensagens e enfileiramento do próprio arquivo) são separados dos arquivos Host global do SAP.
* Serviços de central SAP executados na instância do SAP ASCS/SCS.
* Instância SAP ASCS/SCS estiver em cluster e está acessível usando o \<nome de host virtual ASCS/SCS\> nome de host virtual.
* Os arquivos SAP globais são colocados no compartilhamento de arquivos SMB e são acessados usando o nome do \<host global SAP\>: \\\\&lt;host global SAP&gt;\sapmnt\\&lt;SID&gt;\SYS\..
* A instância do SAP ASCS/SCS é instalada em um disco local em ambos os nós de cluster.
* O \<nome de host virtual ASCS/SCS\> nome de rede é diferente do &lt;host global SAP&gt;.

![Figura 2: Arquitetura de HA do SAP ASCS/SCS com um disco compartilhado SMB][sap-ha-guide-figure-8004]

_**Figura 2:** Nova arquitetura de HA do SAP ASCS/SCS com um disco compartilhado SMB_

Pré-requisitos para o compartilhamento de arquivos SMB:

* Protocolo SMB 3.0 (ou posterior).
* Capacidade de definir a lista de controle de acesso (ACLs) do Active Directory (AD) para grupos de usuários do AD e objeto de computador `computer$`.
* O compartilhamento de arquivos deve ser habilitado para HA:
    * Os discos usados para armazenar os arquivos não devem ser um ponto único de falha.
    * Servidor ou tempo de inatividade da VM não faz com que o tempo de inatividade no compartilhamento de arquivos.

Agora, a função de cluster \<SAP\> não contêm discos compartilhados pelo cluster nem recursos de cluster do Compartilhamento de Arquivo Genérico.


![Figura 3: Recursos de função do cluster SAP \<SID\> para uso de um compartilhamento de arquivos][sap-ha-guide-figure-8005]

_**Figura 3:** Recursos de função do cluster SAP &lt;SID&gt; para uso de um compartilhamento de arquivos_


## <a name="scale-out-file-shares-with-storage-spaces-direct-in-azure-as-an-sapmnt-file-share"></a>Expansão de compartilhamento de arquivos com Espaços de Armazenamento Diretos no Azure como compartilhamento de arquivos SAPMNT

Você pode usar uma expansão de arquivos de escalabilidade horizontal para hospedar e proteger os arquivos de host global do SAP. Um compartilhamento de arquivos de expansão também oferece um serviço de compartilhamento de arquivo SAPMNT altamente disponível.

![Figura 4: Compartilhamento de arquivos de expansão usado para proteger arquivos de host global do SAP][sap-ha-guide-figure-8006]

_**Figura 4:** Um compartilhamento de arquivos de expansão usado para proteger arquivos de host global do SAP_

> [!IMPORTANT]
> Compartilhamentos de arquivos de expansão totalmente têm suporte na nuvem do Microsoft Azure e em ambientes locais.
>

Uma expansão de arquivos altamente disponível e escalonável horizontalmente SAPMNT.

Espaços de armazenamento diretos é usado como um disco compartilhado para expansão de arquivos de escalabilidade horizontal. Espaços de Armazenamento Diretos, permite a criação de armazenamento altamente disponível e escalonável usando servidores com armazenamento local. Armazenamento que é usado para um compartilhamento de arquivos de expansão compartilhado, como SAP global para arquivos de host, não é um ponto único de falha.

Ao escolher Espaços de Armazenamento Diretos, considere estes casos de uso:

- As máquinas virtuais usadas para criar o cluster de Espaços de Armazenamento Diretos precisam ser implantadas em um conjunto de disponibilidade do Azure.
- Para a recuperação de desastre de um cluster Espaços de Armazenamento Diretos, você pode usar os [serviços Azure site Recovery](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#replicated-machines---storage).
- Não há suporte para a ampliação do cluster direto de espaço de armazenamento entre Zonas de Disponibilidade do Azure diferentes.

### <a name="sap-prerequisites-for-scale-out-file-shares-in-azure"></a>Pré-requisitos do SAP para expansão de arquivos de escalabilidade horizontal no Azure

Para usar uma expansão de arquivos de escalabilidade horizontal, o sistema deve atender aos seguintes requisitos:

* Pelo menos dois nós para um compartilhamento de arquivos de expansão de cluster.
* Cada nó deve ter pelo menos dois discos locais.
* Por questões de desempenho, você deve usar *resiliência de espelhamento*:
    * Para uma expansão de arquivos de escalabilidade horizontal com dois nós de cluster de espelhamento bidirecional.
    * Para um compartilhamento de arquivos de escalabilidade horizontal com três nós (ou mais) de cluster de espelhamento bidirecional.
* Recomendamos que nós de cluster de três (ou mais) para um compartilhamento de arquivos de expansão, com o espelhamento de três vias.
    Essa configuração oferece mais escalabilidade e resiliência de expansão que a instalação com 2 nós de cluster e espelhamento de 2 vias.
* Você deve usar disco Premium do Azure.
* Recomendamos que você use o Azure Managed Disks.
* É recomendável que você formate os volumes usando o sistema de arquivos resiliente (ReFS).
    * Para obter mais informações, consulte [Observação do SAP 1869038 – suporte SAP para sistema de arquivos][1869038] and the [Choosing the file system][planning-volumes-s2d-choosing-filesystem] ReFs do artigo planejando volumes em espaços de armazenamento diretos.
    * Certifique-se de instalar a [atualização cumulativa do Microsoft KB4025334][kb4025334].
* Você pode usar tamanhos de VM do Azure da série DS ou série DSv2.
* Para ter bom desempenho da rede VM interno necessário para sincronização de Espaços de Armazenamento Diretos de disco, você deve usar um tipo de VM que tenha pelo menos uma largura de banda de rede "alta".
    Para obter mais informações, consulte as especificações da [série][dv2-series] and [DS-Series][ds-series] DSv2.
* Recomendamos que você reserve alguns capacidade não alocada no pool de armazenamento. Deixar alguma capacidade não alocada no pool de armazenamento fornece espaço em volumes para reparar "no local", se um disco falhar. Isso melhora o desempenho e segurança dos dados.  Para obter mais informações, consulte [escolhendo o tamanho do volume][choosing-the-size-of-volumes-s2d].
* Você não precisa configurar o balanceador de carga interno do Azure para o nome de rede do compartilhamento de arquivos de expansão, como para \<host global SAP\>. Isso é feito para o \<nome de host virtual ASCS/SCS\> da instância SAP ASCS/SCS ou para o DBMS. Um compartilhamento de arquivos de expansão seja dimensionável a carga em todos os nós de cluster. \<Host global SAP\> usa o endereço IP local para todos os nós de cluster.


> [!IMPORTANT]
> Não é possível renomear o compartilhamento de arquivos SAPMNT, que aponta para \<host global SAP\>. SAP suporta apenas o compartilhamento de nome "sapmnt."
>
> Para obter mais informações, consulte [SAP Note 2492395 – pode ser que o nome do compartilhamento sapmnt seja alterado?][2492395]

### <a name="configure-sap-ascsscs-instances-and-a-scale-out-file-share-in-two-clusters"></a>Configurar instâncias SAP ASCS/SCS e um expansão compartilhamento de arquivos em dois clusters

Você tem a possibilidade de implantar instâncias SAP ASCS/SCS em um cluster, com sua própria função de cluster SAP \<SID\>. Nesse caso, você deve configurar o compartilhamento de arquivos de expansão horizontal em outro cluster, com outra função de cluster.

> [!IMPORTANT]
>Nesse cenário, a instância do SAP (A)SCS está configurada para acessar GLOBALHost SAP usando o caminho UNC \\\\&lt;SAPGLOBALHost&gt;\sapmnt\\&lt;SID&gt;\SYS\.
>

![Figura 5: Instância SAP ASCS/SCS e um compartilhamento de arquivos de expansão implantados em dois clusters][sap-ha-guide-figure-8007]

_**Figura 5:** Uma instância SAP ASCS/SCS e um compartilhamento de arquivos de expansão implantado em dois clusters_

> [!IMPORTANT]
> Na nuvem do Azure, cada cluster usado para SAP e compartilhamentos de arquivos de expansão deve ser implantado em seu próprio conjunto de disponibilidade do Azure ou entre Zonas de Disponibilidade do Azure. Isso garante distribuído posicionamento de máquinas virtuais do cluster em toda a infraestrutura do Azure subjacente. Há suporte para implantações de zona de disponibilidade com essa tecnologia.
>

## <a name="generic-file-share-with-sios-datakeeper-as-cluster-shared-disks"></a>Compartilhamento de arquivo genérico com SIOS como discos compartilhados de Cluster


O Compartilhamento de Arquivos Genérico é outra opção para alcançar o compartilhamento de arquivo altamente disponível.

Aqui, como o disco compartilhado de um cluster, você pode usar solução do SIOS de terceiros.

## <a name="next-steps"></a>Próximas etapas

* [Preparar a infraestrutura do Azure para alta disponibilidade do SAP usando um cluster de failover do Windows e um compartilhamento de arquivos para uma instância do SAP ASCS/SCS][sap-high-availability-infrastructure-wsfc-file-share]
* [Instalar a alta disponibilidade do SAP NetWeaver em um cluster de failover do Windows e um compartilhamento de arquivos para uma instância do SAP ASCS/SCS][sap-high-availability-installation-wsfc-shared-disk]
* [Implantar um servidor de arquivos de escalabilidade horizontal de dois nós Espaços de Armazenamento Diretos para o armazenamento UPD no Azure][deploy-sofs-s2d-in-azure]
* [Espaços de Armazenamento Diretos no Windows Server 2016][s2d-in-win-2016]
* [Aprofundamento: Volumes no Espaços de Armazenamento Diretos][deep-dive-volumes-in-s2d]
