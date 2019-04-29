---
title: Instalação de alta disponibilidade do SAP NetWeaver em um cluster de failover do Windows e o compartilhamento de arquivos para instâncias do SAP ASCS/SCS no Azure | Microsoft Docs
description: Instalação da alta disponibilidade do SAP NetWeaver em um cluster de failover do Windows e compartilhamento de arquivos para instâncias ASCS/SCS do SAP
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 71296618-673b-4093-ab17-b7a80df6e9ac
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 04490abb8b7f3f4c39e4134a314429e190db5174
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60714042"
---
# <a name="install-sap-netweaver-high-availability-on-a-windows-failover-cluster-and-file-share-for-sap-ascsscs-instances-on-azure"></a>Instalar alta disponibilidade do SAP NetWeaver em um cluster de failover do Windows e compartilhamento de arquivos para instâncias ASCS/SCS do SAP no Azure

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1596496]:https://launchpad.support.sap.com/#/notes/1596496

[sap-installation-guides]:http://service.sap.com/instguides

[sap-powershell-scrips]:https://github.com/Azure-Samples/sap-powershell

[azure-subscription-service-limits]:../../../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md

[s2d-in-win-2016]:https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview
[sofs-overview]:https://technet.microsoft.com/library/hh831349(v=ws.11).aspx
[new-in-win-2016-storage]:https://docs.microsoft.com/windows-server/storage/whats-new-in-storage

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md

[sap-blog-new-sap-cluster-resource-dll]:https://blogs.sap.com/2017/08/28/new-sap-cluster-resource-dll-is-available/
[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-guide-wsfc-shared-disk]:sap-high-availability-guide-wsfc-shared-disk.md
[sap-high-availability-guide-wsfc-file-share]:sap-high-availability-guide-wsfc-file-share.md
[sap-ascs-high-availability-multi-sid-wsfc]:sap-ascs-high-availability-multi-sid-wsfc.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-infrastructure-wsfc-file-share]:sap-high-availability-infrastructure-wsfc-file-share.md
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md

[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-high-availability-installation-wsfc-shared-disk-create-ascs-virt-host]:sap-high-availability-installation-wsfc-shared-disk.md#a97ad604-9094-44fe-a364-f89cb39bf097
[sap-high-availability-installation-wsfc-shared-disk-add-probe-port]:sap-high-availability-installation-wsfc-shared-disk.md#10822f4f-32e7-4871-b63a-9b86c76ce761

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md

[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-infrastructure-wsfc-shared-disk-vpn]:sap-high-availability-infrastructure-wsfc-shared-disk.md#c87a8d3f-b1dc-4d2f-b23c-da4b72977489
[sap-high-availability-infrastructure-wsfc-shared-disk-change-def-ilb]:sap-high-availability-infrastructure-wsfc-shared-disk.md#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-high-availability-infrastructure-wsfc-shared-disk-setup-wsfc]:sap-high-availability-infrastructure-wsfc-shared-disk.md#0d67f090-7928-43e0-8772-5ccbf8f59aab
[sap-high-availability-infrastructure-wsfc-shared-disk-collect-cluster-config]:sap-high-availability-infrastructure-wsfc-shared-disk.md#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79
[sap-high-availability-infrastructure-wsfc-shared-disk-install-sios]:sap-high-availability-infrastructure-wsfc-shared-disk.md#5c8e5482-841e-45e1-a89d-a05c0907c868
[sap-high-availability-infrastructure-wsfc-shared-disk-add-dot-net]:sap-high-availability-infrastructure-wsfc-shared-disk.md#1c2788c3-3648-4e82-9e0d-e058e475e2a3
[sap-high-availability-infrastructure-wsfc-shared-disk-install-sios-both-nodes]:sap-high-availability-infrastructure-wsfc-shared-disk.md#dd41d5a2-8083-415b-9878-839652812102
[sap-high-availability-infrastructure-wsfc-shared-disk-setup-sios]:sap-high-availability-infrastructure-wsfc-shared-disk.md#d9c1fc8e-8710-4dff-bec2-1f535db7b006

[sap-official-ha-file-share-document]:https://www.sap.com/documents/2017/07/f453332f-c97c-0010-82c7-eda71af511fa.html

[sap-ha-multi-sid-guide]:sap-high-availability-multi-sid.md (Configuração de alta disponibilidade de várias SID do SAP)


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

Este artigo descreve como instalar e configurar um sistema SAP altamente disponível no Azure, com WSFC (Windows Server Failover Cluster) e Servidor de Arquivos de Escalabilidade Horizontal como uma opção para cluster da instância SAP ASCS/SCS.

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar a instalação, revise os seguintes artigos:

* [Guia de arquitetura: Uma instância do SAP ASCS/SCS de cluster em um cluster de failover do Windows usando o compartilhamento de arquivos][sap-high-availability-guide-wsfc-file-share]

* [Preparar a infraestrutura do Azure para alta disponibilidade do SAP usando um cluster de failover do Windows e compartilhamento de arquivos para instâncias ASCS/SCS do SAP][sap-high-availability-infrastructure-wsfc-file-share]

Você precisa dos seguintes executáveis e DLLs do SAP:
* Ferramenta de instalação do SWPM (Gerenciador de Provisionamento de Software) SAP versão SPS21 ou superior.
* Baixe o arquivo-morto NTCLUST.SAR mais recente com a nova DLL de recurso de cluster SAP. As novas DLLs do cluster SAP dão suporte à alta disponibilidade do SAP ASCS/SCS com compartilhamento de arquivos no Cluster de Failover do Windows Server.

  Para obter mais informações sobre o novo recurso de cluster SAP DLL, consulte este blog: [Novo recurso de cluster SAP DLL está disponível! ][sap-blog-new-sap-cluster-resource-dll].

Não descrevemos a configuração do DBMS (Gerenciador de banco de dados) porque as configurações variam dependendo do DBMS que você usa. No entanto, supomos que as preocupações de alta disponibilidade com o DBMS são dissipadas com o suporte às funcionalidades que os diversos fornecedores de DBMS dão para o Azure. Essas funcionalidades incluem o Always On ou o Espelhamento de Banco de Dados para SQL Server e Oracle Data Guard para bancos de dados Oracle. No cenário que usamos neste artigo, não adicionamos outra proteção ao DBMS.

Não existem considerações especiais quando vários serviços DBMS interagem com esse tipo de configuração de SAP ASCS/SCS clusterizada no Azure.

> [!NOTE]
> O procedimento de instalação dos sistemas ABAP, sistemas Java e sistemas ABAP+Java do SAP NetWeaver é quase idêntico. A diferença mais significativa é que um sistema SAP ABAP tem uma instância ASCS. O sistema SAP Java tem uma instância SCS. O sistema ABAP+Java do SAP tem uma instância ASCS e uma instância ABAP+Java em execução no mesmo grupo de cluster de failover da Microsoft. As diferenças de instalação para cada pilha de instalação do SAP NetWeaver serão explicitamente mencionadas. Você pode assumir que todas as outras partes são as mesmas.  
>
>

## <a name="install-an-ascsscs-instance-on-an-ascsscs-cluster"></a>Instalar uma instância do ASCS/SCS em um cluster ASCS/SCS

> [!IMPORTANT]
>
> Atualmente, uma configuração de alta disponibilidade com uma configuração de compartilhamento de arquivos não tem suporte na ferramenta de instalação SAP do SWPM. Portanto, alguma adoção manual é necessária para instalar um sistema SAP ( por exemplo, para instalar e agrupar em cluster uma instância do SAP ASCS/SCS e configurar um host global SAP separado).  
>
> Não há alteração nas outras etapas de instalação para instalar a instância (e cluster) DBMS e os servidores de aplicativos do SAP.
>

### <a name="install-an-ascsscs-instance-on-your-local-drive"></a>Instalar uma instância do ASCS/SCS em sua unidade local

Instalar uma instância SAP ASCS/SCS em *ambos* os nós do cluster ASCS/SCS. Instale-a na unidade local. Em nosso exemplo, a unidade local é C:\\, mas você pode escolher qualquer outra unidade local.  

Para instalar a instância, na ferramenta de instalação do SAP SWPM, vá para:

**\<Produto>** > **\<DBMS ->** > **Instalação ->** > **Servidor de Aplicativos ABAP** (ou **Java**) -> **Sistema Distribuído** > **Instância do ASCS/SCS**

> [!IMPORTANT]
> Atualmente, o cenário de compartilhamento de arquivos não é compatível com a ferramenta de instalação do SAP SWPM. Você *não pode usar* o seguinte caminho de instalação:
>
> **\<Produto>** > **\<DBMS>** > **Instalação** > **Servidor de Aplicativos ABAP** (ou **Java**) > **Sistema de Alta Disponibilidade** > …
>

### <a name="remove-sapmnt-and-create-an-saploc-file-share"></a>Remover SAPMNT e criar o compartilhamento de arquivos SAPLOC

SWMP criou um compartilhamento local SAPMNT na pasta C:\\usr\\sap.

Remova o compartilhamento de arquivos SAPMNT de *ambos* os nós de cluster ASCS/SCS.

Execute o seguinte script do PowerShell:

```powershell
Remove-SmbShare sapmnt -ScopeName * -Force
 ```

Se o compartilhamento SAPLOC não existir, crie um em *ambos* os nós de cluster ASCS/SCS.

Execute o seguinte script do PowerShell:

```powershell
#Create SAPLOC share and set security
$SAPSID = "PR1"
$DomainName = "SAPCLUSTER"
$SAPSIDGlobalAdminGroupName = "$DomainName\SAP_" + $SAPSID + "_GlobalAdmin"
$HostName = $env:computername
$SAPLocalAdminGroupName = "$HostName\SAP_LocalAdmin"
$SAPDisk = "C:"
$SAPusrSapPath = "$SAPDisk\usr\sap"

New-SmbShare -Name saploc -Path c:\usr\sap -FullAccess "BUILTIN\Administrators", $SAPSIDGlobalAdminGroupName , $SAPLocalAdminGroupName  
 ```

## <a name="prepare-an-sap-global-host-on-the-sofs-cluster"></a>Preparar o host global SAP no cluster SOFS

Crie o seguinte volume e compartilhamento de arquivos no cluster SOFS:

* Arquivo SAP GLOBALHOST `C:\ClusterStorage\Volume1\usr\sap\<SID>\SYS\` estrutura no cluster SOFS volume compartilhado (CSV)

* Compartilhamento de arquivos SAPMNT

* Defina a segurança na pasta e no compartilhamento de arquivos SAPMNT com controle total para:
    * O grupo de usuários \<DOMAIN>\SAP_\<SID>_GlobalAdmin
    * Os objetos de computador de nós do Cluster SAP ASCS/SCS \<DOMAIN>\ClusterNode1$ e \<DOMAIN>\ClusterNode2$

Para criar um volume CSV com resiliência de espelho, execute o seguinte cmdlet do PowerShell em um dos nós de cluster SOFS:


```powershell
New-Volume -StoragePoolFriendlyName S2D* -FriendlyName SAPPR1 -FileSystem CSVFS_ReFS -Size 5GB -ResiliencySettingName Mirror
```
Para criar SAPMNT e definir a segurança de pasta e compartilhamento, execute o seguinte script do PowerShell em um de nós de cluster SOFS:

```powershell
# Create SAPMNT on file share
$SAPSID = "PR1"
$DomainName = "SAPCLUSTER"
$SAPSIDGlobalAdminGroupName = "$DomainName\SAP_" + $SAPSID + "_GlobalAdmin"

# SAP ASCS/SCS cluster nodes
$ASCSClusterNode1 = "ascs-1"
$ASCSClusterNode2 = "ascs-2"

# Define SAP ASCS/SCS cluster node computer objects
$ASCSClusterObjectNode1 = "$DomainName\$ASCSClusterNode1$"
$ASCSClusterObjectNode2 = "$DomainName\$ASCSClusterNode2$"

# Create usr\sap\.. folders on CSV
$SAPGlobalFolder = "C:\ClusterStorage\Volume1\usr\sap\$SAPSID\SYS"
New-Item -Path $SAPGlobalFOlder -ItemType Directory

$UsrSAPFolder = "C:\ClusterStorage\Volume1\usr\sap\"

# Create a SAPMNT file share and set share security
New-SmbShare -Name sapmnt -Path $UsrSAPFolder -FullAccess "BUILTIN\Administrators", $SAPSIDGlobalAdminGroupName, $ASCSClusterObjectNode1, $ASCSClusterObjectNode2 -ContinuouslyAvailable $false -CachingMode None -Verbose

# Get SAPMNT file share security settings
Get-SmbShareAccess sapmnt

# Set file and folder security
$Acl = Get-Acl $UsrSAPFolder

# Add a file security object of SAP_<sid>_GlobalAdmin group
$Ar = New-Object  system.security.accesscontrol.filesystemaccessrule($SAPSIDGlobalAdminGroupName,"FullControl", 'ContainerInherit,ObjectInherit', 'None', 'Allow')
$Acl.SetAccessRule($Ar)

# Add  a security object of the clusternode1$ computer object
$Ar = New-Object  system.security.accesscontrol.filesystemaccessrule($ASCSClusterObjectNode1,"FullControl",'ContainerInherit,ObjectInherit', 'None', 'Allow')
$Acl.SetAccessRule($Ar)

# Add a security object of the clusternode2$ computer object
$Ar = New-Object  system.security.accesscontrol.filesystemaccessrule($ASCSClusterObjectNode2,"FullControl",'ContainerInherit,ObjectInherit', 'None', 'Allow')
$Acl.SetAccessRule($Ar)

# Set security
Set-Acl $UsrSAPFolder $Acl -Verbose
 ```
## <a name="stop-ascsscs-instances-and-sap-services"></a>Interromper instâncias ASCS/SCS e serviços SAP

Execute as seguintes etapas:
1. Interrompa instâncias SAP ASCS/SCS em ambos os nós de cluster ASCS/SCS.
2. Interromper os serviços do Windows do SAP ASCS/SCS **SAP\<SID>_\<InstanceNumber>** em ambos os nós de cluster.

## <a name="move-the-sys-folder-to-the-sofs-cluster"></a>Mover a pasta \SYS\.... para o cluster SOFS

Execute as seguintes etapas:
1. Copie a pasta SYS (por exemplo, `C:\usr\sap\<SID>\SYS`) de um dos ASCS/SCS nós de cluster para o cluster SOFS (por exemplo, para `C:\ClusterStorage\Volume1\usr\sap\<SID>\SYS`).
2. Excluir o `C:\usr\sap\<SID>\SYS` pasta de ambos os nós de cluster ASCS/SCS.

## <a name="update-the-cluster-security-setting-on-the-sap-ascsscs-cluster"></a>Atualizar a configuração de segurança de cluster no cluster do SAP ASCS/SCS

Execute o seguinte script do PowerShell em um de nós de cluster do SAP ASCS/SCS:

```powershell
# Grant <DOMAIN>\SAP_<SID>_GlobalAdmin group access to the cluster

$SAPSID = "PR1"
$DomainName = "SAPCLUSTER"
$SAPSIDGlobalAdminGroupName = "$DomainName\SAP_" + $SAPSID + "_GlobalAdmin"

# Set full access for the <DOMAIN>\SAP_<SID>_GlobalAdmin group
Grant-ClusterAccess -User $SAPSIDGlobalAdminGroupName -Full

#Check security settings
Get-ClusterAccess
```

## <a name="create-a-virtual-host-name-for-the-clustered-sap-ascsscs-instance"></a>Criar um nome de host virtual para a instância clusterizada do SAP ASCS/SCS

Crie um nome de rede de cluster do SAP ASCS/SCS (por exemplo, **pr1-ascs [10.0.6.7]**) conforme descrito em [Criar um nome de host virtual para a instância clusterizada do SAP ASCS/SCS][sap-high-availability-installation-wsfc-shared-disk-create-ascs-virt-host].

## <a name="update-the-default-and-sap-ascsscs-instance-profile"></a>Atualizar o perfil de instância padrão e SAP ASCS/SCS

Para usar o novo nome de host virtual SAP ASCS/SCS e nome de host global SAP, você deve atualizar o padrão e o perfil da instância SAP ASCS/SCS \<SID >_ASCS/SCS\<Nr >_\<Host >.


| Valores antigos |  |
| --- | --- |
| Nome de host SAP ASCS/SCS = Host SAP global | ascs-1 |
| Nome do perfil da instância do SAP ASCS/SCS | PR1_ASCS00_ascs-1 |

| Novos valores |  |
| --- | --- |
| Nome de host do SAP ASCS/SCS | **pr1-ascs** |
| Host SAP global | **sapglobal** |
| Nome do perfil da instância do SAP ASCS/SCS | PR1\_ASCS00\_**pr1-ascs** |

### <a name="update-sap-default-profile"></a>Atualizar perfil padrão SAP


| Nome do parâmetro | Valor de parâmetro |
| --- | --- |
| SAPGLOBALHOST | **sapglobal** |
| rdisp/mshost | **pr1-ascs** |
| enque/serverhost | **pr1-ascs** |

### <a name="update-the-sap-ascsscs-instance-profile"></a>Atualizar o perfil da instância do SAP ASCS/SCS

| Nome do parâmetro | Valor de parâmetro |
| --- | --- |
| SAPGLOBALHOST | **sapglobal** |
| DIR_PROFILE | \\\sapglobal\sapmnt\PR1\SYS\profile |
| _PF | $(DIR_PROFILE)\PR1\_ASCS00_ pr1-ascs |
| Restart_Program_02 = local$(_MS) pf=$(_PF) | **Start**_Program_02 = local$(_MS) pf=$(_PF) |
| SAPLOCALHOST | **pr1-ascs** |
| Restart_Program_03 = local$(_EN) pf=$(_PF) | **Start**_Program_03 = local$(_EN) pf=$(_PF) |
| gw/netstat_once | **0** |
| enque/encni/set_so_keepalive  | **true** |
| service/ha_check_node | **1** |

> [!IMPORTANT]
>Você pode usar o cmdlet do PowerShell **Update-SAPASCSSCSProfile** para automatizar a atualização de perfil.
>
>O cmdlet do PowerShell é compatível com as instâncias SAP ABAP ASCS e SAP Java SCS.
>

Copie [**SAPScripts.psm1**][sap-powershell-scrips] para a unidade local C:\tmp e execute o seguinte cmdlet do PowerShell:

```powershell
Import-Module C:\tmp\SAPScripts.psm1

Update-SAPASCSSCSProfile -PathToAscsScsInstanceProfile \\sapglobal\sapmnt\PR1\SYS\profile\PR1_ASCS00_ascs-1 -NewASCSHostName pr1-ascs -NewSAPGlobalHostName sapglobal -Verbose  
```

![Figura 1: Saída SAPScripts.psm1][sap-ha-guide-figure-8012]

_**Figura 1**: Saída SAPScripts.psm1_

## <a name="update-the-sidadm-user-environment-variable"></a>Atualizar a variável de ambiente do usuário \<sid>adm

1. Atualize o novo caminho GLOBALHOST UNC do ambiente do usuário \<sid>adm em *ambos* os nós de cluster ASCS/SCS.
2. Faça logon como usuário \<sid>adm e, em seguida, inicie a ferramenta Regedit.exe.
3. Vá para o **Ambiente** **HKEY_CURRENT_USER** >  e atualize as variáveis para o novo valor:

| Variável | Value |
| --- | --- |
| RSEC_SSFS_DATAPATH | \\\\**sapglobal**\sapmnt\PR1\SYS\global\security\rsecssfs\data |
| RSEC_SSFS_KEYPATH | \\\\**sapglobal**\sapmnt\PR1\SYS\global\security\rsecssfs\key |
| SAPEXE | \\\\**sapglobal**\sapmnt\PR1\SYS\exe\uc\NTAMD64 |
| SAPLOCALHOST  | **pr1-ascs** |


## <a name="install-a-new-saprcdll-file"></a>Instalar um novo arquivo saprc.dll

1. Instale uma nova versão do recurso de cluster do SAP que seja compatível com o cenário de compartilhamento de arquivos.

2. Baixe o pacote NTCLUST. SAR mais recente do SAP Service Marketplace.

3. Desempacote NTCLUS. SAR em um dos nós de cluster ASCS/SCS e execute o seguinte comando do prompt de comando para instalar o novo arquivo saprc.dll:

```
.\NTCLUST\insaprct.exe -yes -install
```

O novo arquivo saprc.dll é instalado em ambos os nós de cluster ASCS/SCS.

Para obter mais informações, consulte [Nota SAP 1596496 – Como atualizar DLLs de tipo de recurso SAP para o Monitor de Recursos de Cluster][1596496].

## <a name="create-a-sap-sid-cluster-group-network-name-and-ip"></a>Criar um SAP \<SID > grupo, nome de rede e IP de cluster

Para criar um grupo de clusters SAP \<SID>, um nome de rede ASCS/SCS e um endereço IP correspondente, execute o seguinte cmdlet do PowerShell:

```powershell
# Create SAP Cluster Group
$SAPSID = "PR1"
$SAPClusterGroupName = "SAP $SAPSID"
$SAPIPClusterResourceName = "SAP $SAPSID IP"
$SAPASCSNetworkName = "pr1-ascs"
$SAPASCSIPAddress = "10.0.6.7"
$SAPASCSSubnetMask = "255.255.255.0"

# Create an SAP ASCS instance virtual IP cluster resource
Add-ClusterGroup -Name $SAPClusterGroupName -Verbose

#Create an SAP ASCS virtual IP address
$SAPIPClusterResource = Add-ClusterResource -Name $SAPIPClusterResourceName -ResourceType "IP Address" -Group $SAPClusterGroupName -Verbose

# Set a static IP address
$param1 = New-Object Microsoft.FailoverClusters.PowerShell.ClusterParameter $SAPIPClusterResource,Address,$SAPASCSIPAddress
$param2 = New-Object Microsoft.FailoverClusters.PowerShell.ClusterParameter $SAPIPClusterResource,SubnetMask,$SAPASCSSubnetMask
$params = $param1,$param2
$params | Set-ClusterParameter

# Create a corresponding network name
$SAPNetworkNameClusterResourceName = $SAPASCSNetworkName
Add-ClusterResource -Name $SAPNetworkNameClusterResourceName -ResourceType "Network Name" -Group $SAPClusterGroupName -Verbose

# Set a network DNS name
$SAPNetworkNameClusterResource = Get-ClusterResource $SAPNetworkNameClusterResourceName
$SAPNetworkNameClusterResource | Set-ClusterParameter -Name Name -Value $SAPASCSNetworkName

#Check the updated values
$SAPNetworkNameClusterResource | Get-ClusterParameter

#Set resource dependencies
Set-ClusterResourceDependency -Resource $SAPNetworkNameClusterResourceName -Dependency "[$SAPIPClusterResourceName]" -Verbose

#Start an SAP <SID> cluster group
Start-ClusterGroup -Name $SAPClusterGroupName -Verbose
```

## <a name="register-the-sap-start-service-on-both-nodes"></a>Registrar o serviço de início de SAP em ambos os nós

Registre novamente o serviço de início de SAP ASCS/SCS para que aponte para o novo perfil e o caminho de perfil.

Você deve executar este novo registro em *ambos* os nós de cluster ASCS/SCS.

No prompt de comandos com privilégios elevados, execute o seguinte comando:

```
C:\usr\sap\PR1\ASCS00\exe\sapstartsrv.exe -r -p \\sapglobal\sapmnt\PR1\SYS\profile\PR1_ASCS00_pr1-ascs -s PR1 -n 00 -U SAPCLUSTER\SAPServicePR1 -P mypasswd12 -e SAPCLUSTER\pr1adm
```

![Figura 2: Reinstale o serviço do SAP][sap-ha-guide-figure-8013]

_**Figura 2**: Reinstale o serviço do SAP_

Certifique-se de que os parâmetros estejam corretos e, em seguida, selecione **Manual** como o **Tipo de inicialização**.

## <a name="stop-the-ascsscs-service"></a>Parar o serviço ASCS/SCS

Interrompa o serviço do SAP ASCS/SCS SAP\<SID>_\<InstanceNumber> em ambos os nós de cluster.

## <a name="create-a-new-sap-service-and-sap-instance-resources"></a>Criar um novo serviço SAP e recursos de instância do SAP

Para finalizar a criação de recursos do grupo de clusters SAP\<SID> do SAP, crie os seguintes recursos:

* Serviço SAP \<SID> \<InstanceNumber>
* Instância SAP \<SID> \<InstanceNumber>

Execute o seguinte cmdlet do PowerShell:

```powershell
$SAPSID = "PR1"
$SAPInstanceNumber = "00"
$SAPNetworkNameClusterResourceName = "pr1-ascs"

$SAPServiceName = "SAP$SAPSID"+ "_" + $SAPInstanceNumber

$SAPClusterGroupName = "SAP $SAPSID"
$SAPServiceClusterResourceName = "SAP $SAPSID $SAPInstanceNumber Service"

$SAPASCSServiceClusterResource = Add-ClusterResource -Name $SAPServiceClusterResourceName -Group $SAPClusterGroupName -ResourceType "SAP Service" -SeparateMonitor -Verbose
$SAPASCSServiceClusterResource  | Set-ClusterParameter  -Name ServiceName -Value $SAPServiceName

#Set resource dependencies
Set-ClusterResourceDependency -Resource $SAPASCSServiceClusterResource  -Dependency "[$SAPNetworkNameClusterResourceName]" -Verbose

$SAPInstanceClusterResourceName = "SAP $SAPSID $SAPInstanceNumber Instance"

# Create SAP instance cluster resource
$SAPASCSServiceClusterResource = Add-ClusterResource -Name $SAPInstanceClusterResourceName -Group $SAPClusterGroupName -ResourceType "SAP Resource" -SeparateMonitor -Verbose

#Set SAP instance cluster resource parameters
$SAPASCSServiceClusterResource  | Set-ClusterParameter  -Name SAPSystemName -Value $SAPSID -Verbose
$SAPASCSServiceClusterResource  | Set-ClusterParameter  -Name SAPSystem -Value $SAPInstanceNumber -Verbose

#Set resource dependencies
Set-ClusterResourceDependency -Resource $SAPASCSServiceClusterResource  -Dependency "[$SAPServiceClusterResourceName]" -Verbose
```

## <a name="add-a-probe-port"></a>Adicionar uma porta de investigação

Configure um recurso de cluster do SAP, a porta de investigação SAP-SID-IP, usando o PowerShell. Execute essa configuração em um dos nós do cluster SAP ASCS/SCS, conforme descrito [nesse artigo][sap-high-availability-installation-wsfc-shared-disk-add-probe-port].

## <a name="install-an-ers-instance-on-both-cluster-nodes"></a>Instalar a instância ERS em ambos os nós de cluster

Instale uma instância de ERS (Servidor de Replicação de Enfileiramento) em *ambos* os nós do cluster ASCS/SCS. No menu de SWPM, siga este caminho de instalação:

**\<Produto** > **\<DBMS>** > **Instalação** > **Instâncias Adicionais do Sistema SAP** > **instância do Servidor de Replicação de Enfileiramento**

## <a name="install-a-dbms-instance-and-sap-application-servers"></a>Instalar instância DBMS e servidores de aplicativos SAP

Finalize a instalação do sistema SAP instalando:
* Uma instância DBMS.
* Um servidor de aplicativos SAP primário.
* Um servidor de aplicativos SAP adicional.

## <a name="next-steps"></a>Próximas etapas

* [Instalar uma instância do ASCS/SCS em um cluster de failover sem discos compartilhados: diretrizes SAP oficiais para compartilhamento de arquivo de alta disponibilidade][sap-official-ha-file-share-document]

* [Espaços de armazenamento direto no Windows Server 2016][s2d-in-win-2016]

* [Visão geral do Servidor de Arquivos de Escalabilidade Horizontal para dados de aplicativo][sofs-overview]

* [O que há de novo no Armazenamento no Windows Server 2016][new-in-win-2016-storage]
