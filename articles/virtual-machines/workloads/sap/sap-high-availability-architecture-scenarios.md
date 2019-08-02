---
title: Arquitetura e cenários de alta disponibilidade de Máquinas Virtuais do Azure para SAP NetWeaver | Microsoft Docs
description: Arquitetura e cenários de alta disponibilidade para SAP NetWeaver em Máquinas Virtuais do Azure
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: gwallace
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 887caaec-02ba-4711-bd4d-204a7d16b32b
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 01/21/2019
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f6c329a2b50c946e873391db431c1cd5ff30ab4f
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67709082"
---
# <a name="high-availability-architecture-and-scenarios-for-sap-netweaver"></a>Arquitetura de alta disponibilidade e cenários para SAP NetWeaver

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png


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
[sap-ascs-ha-multi-sid-wsfc-file-share]:sap-ascs-ha-multi-sid-wsfc-file-share.md
[sap-ascs-ha-multi-sid-wsfc-shared-disk]:sap-ascs-ha-multi-sid-wsfc-shared-disk.md
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md
[sap-suse-ascs-ha-anf]:high-availability-guide-suse-netapp-files.md
[sap-higher-availability]:sap-higher-availability-architecture-scenarios.md

[planning-guide]:planning-guide.md  
[planning-guide-1.2]:planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff
[planning-guide-11]:planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058
[planning-guide-11.4.1]:planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77
[planning-guide-11.5]:planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10
[planning-guide-3.1]:planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a
[planning-guide-3.2.1]:planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358
[planning-guide-3.2.2]:planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560
[planning-guide-3.2.3]:planning-guide.md#18810088-f9be-4c97-958a-27996255c665
[planning-guide-3.2]:planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77
[planning-guide-3.3.2]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92
[planning-guide-5.1.1]:planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53
[planning-guide-5.1.2]:planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c
[planning-guide-5.2.1]:planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef
[planning-guide-5.2.2]:planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3
[planning-guide-5.2]:planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7
[planning-guide-5.3.1]:planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79
[planning-guide-5.3.2]:planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a
[planning-guide-5.4.2]:planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1
[planning-guide-5.5.1]:planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646
[planning-guide-5.5.3]:planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d
[planning-guide-7.1]:planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30
[planning-guide-7]:planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14
[planning-guide-9.1]:planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3
[planning-guide-azure-premium-storage]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92

[virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]:../../windows/sql/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md
[virtual-machines-windows-portal-sql-alwayson-int-listener]:../../windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md

[sap-ha-bc-virtual-env-hyperv-vmware-white-paper]:https://scn.sap.com/docs/DOC-44415
[sap-ha-partner-information]:https://scn.sap.com/docs/DOC-8541
[azure-sla]:https://azure.microsoft.com/support/legal/sla/
[azure-virtual-machines-manage-availability]:https://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability
[azure-storage-redundancy]:https://azure.microsoft.com/documentation/articles/storage-redundancy/
[azure-storage-managed-disks-overview]:https://docs.microsoft.com/azure/storage/storage-managed-disks-overview

[planning-guide-figure-100]:media/virtual-machines-shared-sap-planning-guide/100-single-vm-in-azure.png
[planning-guide-figure-1300]:media/virtual-machines-shared-sap-planning-guide/1300-ref-config-iaas-for-sap.png
[planning-guide-figure-1400]:media/virtual-machines-shared-sap-planning-guide/1400-attach-detach-disks.png
[planning-guide-figure-1600]:media/virtual-machines-shared-sap-planning-guide/1600-firewall-port-rule.png
[planning-guide-figure-1700]:media/virtual-machines-shared-sap-planning-guide/1700-single-vm-demo.png
[planning-guide-figure-1900]:media/virtual-machines-shared-sap-planning-guide/1900-vm-set-vnet.png
[planning-guide-figure-200]:media/virtual-machines-shared-sap-planning-guide/200-multiple-vms-in-azure.png
[planning-guide-figure-2100]:media/virtual-machines-shared-sap-planning-guide/2100-s2s.png
[planning-guide-figure-2200]:media/virtual-machines-shared-sap-planning-guide/2200-network-printing.png
[planning-guide-figure-2300]:media/virtual-machines-shared-sap-planning-guide/2300-sapgui-stms.png
[planning-guide-figure-2400]:media/virtual-machines-shared-sap-planning-guide/2400-vm-extension-overview.png
[planning-guide-figure-2500]:media/virtual-machines-shared-sap-planning-guide/planning-monitoring-overview-2502.png
[planning-guide-figure-2600]:media/virtual-machines-shared-sap-planning-guide/2600-sap-router-connection.png
[planning-guide-figure-2700]:media/virtual-machines-shared-sap-planning-guide/2700-exposed-sap-portal.png
[planning-guide-figure-2800]:media/virtual-machines-shared-sap-planning-guide/2800-endpoint-config.png
[planning-guide-figure-2900]:media/virtual-machines-shared-sap-planning-guide/2900-azure-ha-sap-ha.png
[planning-guide-figure-2901]:media/virtual-machines-shared-sap-planning-guide/2901-azure-ha-sap-ha-md.png
[planning-guide-figure-300]:media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png
[planning-guide-figure-3000]:media/virtual-machines-shared-sap-planning-guide/3000-sap-ha-on-azure.png
[planning-guide-figure-3200]:media/virtual-machines-shared-sap-planning-guide/3200-sap-ha-with-sql.png
[planning-guide-figure-3201]:media/virtual-machines-shared-sap-planning-guide/3201-sap-ha-with-sql-md.png
[planning-guide-figure-400]:media/virtual-machines-shared-sap-planning-guide/400-vm-services.png
[planning-guide-figure-600]:media/virtual-machines-shared-sap-planning-guide/600-s2s-details.png
[planning-guide-figure-700]:media/virtual-machines-shared-sap-planning-guide/700-decision-tree-deploy-to-azure.png
[planning-guide-figure-800]:media/virtual-machines-shared-sap-planning-guide/800-portal-vm-overview.png
[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

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

[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps-md%2Fazuredeploy.json

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/resource-group-overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md


## <a name="terminology-definitions"></a>Definições de terminologia

**Alta disponibilidade**: Refere-se a um conjunto de tecnologias que minimiza as interrupções de TI proporcionando a continuidade dos negócios de serviços de TI por meio de componentes redundantes e tolerantes a falhas ou protegidos contra failover no *mesmo* data center. Em nosso caso, o data center reside em uma região do Azure.

**Recuperação de desastre**: Também se refere à redução da interrupção dos serviços de TI e sua recuperação, mas em *vários* data centers que podem estar a centenas de quilômetros de distância uns dos outros. Em nosso caso, os data centers podem residir em várias regiões do Azure na mesma região geopolítica ou em locais estabelecidos por você como cliente.


## <a name="overview-of-high-availability"></a>Visão geral de alta disponibilidade
A alta disponibilidade do SAP no Azure pode ser classificada em três tipos:

* **Alta disponibilidade de infraestrutura do Azure**: 

    Por exemplo, a alta disponibilidade pode incluir VMs (computação), rede ou armazenamento e seus benefícios para aumentar a disponibilidade de aplicativos SAP.

* **Utilizando a Reinicialização de VM da infraestrutura do Azure para atingir *maior disponibilidade* de aplicativos SAP**: 

    Se você decidir não usar as funcionalidades, como o WSFC (Clustering de Failover do Windows Server) ou o Pacemaker no Linux, a reinicialização de VM do Azure será utilizada. Protege os sistemas SAP contra tempo de inatividade planejado e não planejado da infraestrutura do servidor físico do Azure e de toda a plataforma subjacente do Azure.

* **Alta disponibilidade de aplicativo SAP**: 

    Para obter toda a alta disponibilidade do sistema SAP, você precisa proteger todos os componentes essenciais do sistema SAP. Por exemplo:
    * Servidores de aplicativos SAP redundantes.
    * Componentes exclusivos. Um exemplo pode ser um componente SPOF (ponto único de falha), como uma instância SAP ASCS/SCS ou um DBMS (sistema de gerenciamento de banco de dados).

A alta disponibilidade do SAP no Azure é diferente da alta disponibilidade do SAP em um ambiente físico ou virtual local. O documento a seguir [alta disponibilidade do SAP NetWeaver e continuidade de negócios em ambientes virtuais com VMware e Hyper-V no Microsoft Windows][sap-ha-bc-virtual-env-hyperv-vmware-white-paper] descreve as configurações de alta disponibilidade SAP padrão em virtualizado ambientes do Windows.

Há não configuração de alta disponibilidade da SAP integrada com sapinst para Linux da mesma maneira que existe para o Windows. Para obter informações sobre a alta disponibilidade SAP no local para o Linux, consulte [informações do parceiro de alta disponibilidade][sap-ha-partner-information].

## <a name="azure-infrastructure-high-availability"></a>Alta disponibilidade de infraestrutura do Azure

### <a name="sla-for-single-instance-virtual-machines"></a>SLA para máquinas virtuais de instância única

Atualmente, não há um SLA de VM individual de 99,9% com armazenamento premium. Para obter uma ideia sobre o que pode ser a disponibilidade de uma única VM, você pode compilar o produto dos vários disponíveis [contratos de nível de serviço do Azure][azure-sla].

A base para o cálculo é 30 dias por mês, ou 43.200 minutos. Por exemplo, um tempo de inatividade de 0,05% corresponde a 21,6 minutos. Normalmente, a disponibilidade dos serviços é calculada da seguinte maneira:

(Serviço de disponibilidade nº 1/100) * (Serviço de disponibilidade nº 2/100) * (Serviço de disponibilidade nº 3/100) \*…

Por exemplo:

(99,95/100) * (99,9/100) * (99,9/100) = 0,9975 ou uma disponibilidade geral de 99,75%.

### <a name="multiple-instances-of-virtual-machines-in-the-same-availability-set"></a>Várias instâncias de máquinas virtuais no mesmo conjunto de disponibilidade
Para todas as máquinas virtuais que tenham duas ou mais instâncias implantadas no mesmo *conjunto de disponibilidade*, garantimos que você terá Conectividade de máquina virtual em pelo menos 99,95% do tempo.

Quando duas ou mais VMs são parte do mesmo conjunto de disponibilidade, cada máquina virtual em um conjunto de disponibilidade recebe um *domínio de atualização* e um *domínio de falha* da plataforma subjacente do Azure.

* Os **Domínios de atualização** fazem com que várias VMs não sejam reinicializadas ao mesmo tempo durante a manutenção planejada da infraestrutura do Azure. Apenas uma VM é reiniciada por vez.

* Os **Domínios de falha** fazem com que as VMs sejam implantadas em componentes de hardware que não compartilham fonte de energia e chave de rede. Quando os servidores, as chaves de rede ou as fontes de alimentação passam por tempo de inatividade não planejado, apenas uma VM é afetada.

Para obter mais informações, consulte [gerenciar a disponibilidade das máquinas de virtuais do Windows no Azure][azure-virtual-machines-manage-availability].

Um conjunto de disponibilidade é usado para alcançar alta disponibilidade de:

* Servidores de aplicativos SAP redundantes.  
* Clusters com dois ou mais nós (VMs, por exemplo) que protegem SPOFs como uma instância SAP ASCS/SCS ou um DBMS.


### <a name="azure-availability-zones"></a>Zonas de Disponibilidade do Azure
O Azure está no processo de distribuir um conceito de [Zonas de Disponibilidade do Azure](https://docs.microsoft.com/azure/availability-zones/az-overview) em diferentes [Regiões do Azure](https://azure.microsoft.com/global-infrastructure/regions/). Nas regiões do Azure em que as Zonas de Disponibilidade são oferecidas, as regiões do Azure têm vários data centers, que são independentes em relação a fonte de alimentação, resfriamento e rede. O motivo para a oferta de diferentes zonas em uma única região do Azure é permitir que você implante aplicativos em duas ou três Zonas de Disponibilidade oferecidas. Supondo que os problemas em fontes de alimentação e/ou rede afetem apenas uma infraestrutura de Zona de Disponibilidade, a implantação do aplicativo dentro de uma região do Azure permanece totalmente funcional. Eventualmente com alguma capacidade reduzida, pois algumas máquinas virtuais em uma região podem ser perdidas. Mas as VMs nas outras duas zonas ainda estão em execução. As regiões do Azure que oferecem zonas são listadas em [Zonas de Disponibilidade do Azure](https://docs.microsoft.com/azure/availability-zones/az-overview).

Ao usar Zonas de Disponibilidade, há alguns aspectos a serem considerados. A lista de considerações é semelhante à seguinte:

- Não é possível implantar Conjuntos de Disponibilidade do Azure em uma Zona de Disponibilidade. Você precisa escolher uma Zona de Disponibilidade ou um Conjunto de Disponibilidade como o quadro de implantação de uma VM.
- Não é possível usar o [Basic Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview#skus) para criar soluções de cluster de failover baseadas em Serviços de Cluster de Failover do Windows ou no Linux Pacemaker. Em vez disso, você precisará usar o [SKU do Azure Standard Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones)
- As Zonas de Disponibilidade do Azure não fornecem nenhuma garantia de determinada distância entre diferentes zonas em uma região
- A latência da rede entre diferentes Zonas de Disponibilidade do Azure em diferentes regiões do Azure poderá variar conforme a região do Azure. Haverá casos em que você, como cliente, poderá executar razoavelmente a camada de aplicativo SAP implantada em diferentes zonas, pois a latência da rede de uma zona para a VM ativa do DBMS ainda é aceitável de um impacto do processo empresarial. Em outros casos, haverá cenários de cliente em que a latência entre a VM ativa do DBMS em uma zona e uma instância do aplicativo SAP em uma VM em outra zona poderá ser muito invasiva e não aceitável para os processos empresariais do SAP. Como resultado, as arquiteturas de implantação precisam ser diferentes com uma arquitetura ativa/ativa para o aplicativo ou uma arquitetura ativa/passiva, caso a latência seja muito alta.
- O uso do [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/) é obrigatório para a implantação em Zonas de Disponibilidade do Azure 


### <a name="planned-and-unplanned-maintenance-of-virtual-machines"></a>Manutenção planejada e não planejada de máquinas virtuais

Dois tipos de eventos da plataforma Azure podem afetar a disponibilidade das máquinas virtuais:

* Os eventos de **Manutenção planejada** são atualizações periódicas da plataforma subjacente do Azure feitas pela Microsoft. As atualizações melhoram a confiabilidade, o desempenho e a segurança da infraestrutura de plataforma onde as máquinas virtuais são executadas.

* Os eventos de **manutenção não planejada** ocorrem quando o hardware ou a infraestrutura física subjacente à sua máquina virtual apresentou algum tipo de falha. Isso pode incluir falhas na rede local, falhas no disco local ou outras falhas no nível de rack. Quando tal falha é detectada, a plataforma do Azure migra automaticamente sua máquina virtual do servidor físico não íntegro que hospeda sua máquina virtual para um servidor físico íntegro. Esses eventos são raros, mas podem também causar a reinicialização da sua máquina virtual.

Para obter mais informações, consulte [gerenciar a disponibilidade das máquinas de virtuais do Windows no Azure][azure-virtual-machines-manage-availability].

### <a name="azure-storage-redundancy"></a>Redundância do Armazenamento do Azure
Os dados em sua conta de armazenamento sempre são replicados para garantir durabilidade e alta disponibilidade, cumprindo o SLA do Armazenamento do Azure mesmo diante de falhas transitórias de hardware.

Como o Armazenamento do Azure mantém três imagens dos dados por padrão, o uso de RAID 5 ou RAID 1 em vários discos do Azure é desnecessário.

Para obter mais informações, consulte [Replicação do Armazenamento do Azure][azure-storage-redundancy].

### <a name="azure-managed-disks"></a>Azure Managed Disks
O Managed Disks é um tipo de recurso do Azure Resource Manager cujo uso é recomendado em relação aos VHDs (discos rígidos virtuais), que são armazenados em contas de armazenamento do Azure. O Managed Disks se alinha automaticamente a um conjunto de disponibilidade do Azure da máquina virtual à qual ele está anexado. Eles aumentam a disponibilidade da máquina virtual e os serviços que estão sendo executados nela.

Para obter mais informações, consulte [visão geral do Azure Managed Disks][azure-storage-managed-disks-overview].

Recomendamos que você use discos gerenciados porque eles simplificam a implantação e o gerenciamento de suas máquinas virtuais.



## <a name="utilizing-azure-infrastructure-high-availability-to-achieve-higher-availability-of-sap-applications"></a>Utilizando a alta disponibilidade da infraestrutura do Azure para atingir *alta disponibilidade* de aplicativos SAP

Se você decidir não usar funcionalidades como WSFC ou Pacemaker no Linux (atualmente há suporte apenas para o SLES (SUSE Linux Enterprise Server) 12 e versões posteriores), a reinicialização de VM do Azure será utilizada. Protege os sistemas SAP contra tempo de inatividade planejado e não planejado da infraestrutura do servidor físico do Azure e de toda a plataforma subjacente do Azure.

Para obter mais informações sobre essa abordagem, consulte [reinicialização de VM da infraestrutura de utilizar o Azure para atingir maior disponibilidade do sistema SAP][sap-higher-availability].

## <a name="baed0eb3-c662-4405-b114-24c10a62954e"></a>Alta disponibilidade de aplicativos SAP no Azure IaaS

Para obter toda a alta disponibilidade do sistema SAP, você precisa proteger todos os componentes essenciais do sistema SAP. Por exemplo:
  * Servidores de aplicativos SAP redundantes.
  * Componentes exclusivos. Um exemplo pode ser um componente SPOF (ponto único de falha), como uma instância SAP ASCS/SCS ou um DBMS (sistema de gerenciamento de banco de dados).

As seções a seguir abordam como alcançar alta disponibilidade para todos os três componentes essenciais do sistema SAP.

### <a name="high-availability-architecture-for-sap-application-servers"></a>Arquitetura de alta disponibilidade para servidores de aplicativos SAP

> Esta seção aplica-se a:
>
> ![Windows][Logo_Windows] Windows e ![Linux][Logo_Linux] Linux
>

Normalmente, não é necessária uma solução específica de alta disponibilidade para servidor de aplicativos SAP e instâncias de diálogo. Você atinge alta disponibilidade por redundância e configura várias instâncias de caixa de diálogo em várias instâncias de máquinas virtuais do Azure. Você deve ter pelo menos duas instâncias do aplicativo SAP instaladas em duas máquinas virtuais do Azure.

![Figura 1: Servidor de aplicativos SAP de alta disponibilidade][sap-ha-guide-figure-2000]

_**Figura 1:** Servidor de aplicativos SAP de alta disponibilidade_

Você deve colocar todas as máquinas virtuais que hospedam instâncias do servidor de aplicativos SAP no mesmo conjunto de disponibilidade do Azure. Um conjunto de disponibilidade do Azure garante que:

* Todas as máquinas virtuais são parte do mesmo domínio de atualização.  
    Um domínio de atualização faz com que as máquinas virtuais não sejam atualizadas ao mesmo tempo durante o tempo de inatividade de manutenção planejada.

    A funcionalidade básica baseada nos domínios de falha em uma unidade de escala do Azure e de atualização diferentes, já foi introduzida na [domínios de atualização][planning-guide-3.2.2] seção.

* Todas as máquinas virtuais são parte do mesmo domínio de falha.  
    Um domínio de falha faz com que as máquinas virtuais sejam implantadas de forma que nenhum ponto único de falha afete a disponibilidade de todas as máquinas virtuais.

O número de domínios de falha e atualização que podem ser usados por um conjunto de disponibilidade do Azure em uma unidade de escala do Azure é limitado. Se você continuar adicionando máquinas virtuais a um mesmo conjunto de disponibilidade, duas ou mais VMs eventualmente ficarão no mesmo domínio de falha ou atualização.

Se você implanta algumas instâncias do servidor de aplicativos SAP em suas VMs dedicadas e supondo que temos cinco domínios de atualização, o quadro a seguir surge no final. O número máximo real de domínios de falha e atualização dentro de um conjunto de disponibilidade pode vir a mudar no futuro:

![Figura 2: Alta disponibilidade dos servidores de aplicativos SAP em um conjunto de disponibilidade do Azure][planning-guide-figure-3000]
 _**Figura 2:** Alta disponibilidade dos servidores de aplicativos SAP em um conjunto de disponibilidade do Azure_

Para obter mais informações, consulte [gerenciar a disponibilidade das máquinas de virtuais do Windows no Azure][azure-virtual-machines-manage-availability].

Para obter mais informações, consulte o [conjuntos de disponibilidade do Azure][planning-guide-3.2.3] seção de máquinas virtuais do Azure de planejamento e implementação para o documento do SAP NetWeaver.

**Somente discos não gerenciados:** Como a conta de armazenamento do Azure é um possível ponto único de falha, é importante ter pelo menos duas contas de armazenamento do Azure, nas quais pelo menos duas máquinas virtuais estejam distribuídas. Em uma configuração ideal, os discos de cada máquina virtual que está executando uma instância de diálogo SAP deveria ser implantada em uma conta de armazenamento diferente.

> [!IMPORTANT]
> Recomendamos que você use os discos gerenciados do Azure nas instalações de alta disponibilidade do SAP. Como os discos gerenciados alinham-se automaticamente ao conjunto de disponibilidade da máquina virtual à qual eles estão conectados, eles aumentam a disponibilidade da sua máquina virtual e dos serviços que estão em execução nela.  
>

### <a name="high-availability-architecture-for-an-sap-ascsscs-instance-on-windows"></a>Arquitetura de alta disponibilidade para uma instância do SAP ASCS/SCS no Windows

> ![Windows][Logo_Windows] Windows
>

Você pode usar uma solução WSFC para proteger a instância SAP ASCS/SCS. A solução tem duas variantes:

* **Agrupar a instância do SAP ASCS/SCS em um cluster usando discos compartilhados clusterizados**: Para obter mais informações sobre essa arquitetura, consulte [clusterizar uma instância do SAP ASCS/SCS em um cluster de failover do Windows usando um disco compartilhado de cluster][sap-high-availability-guide-wsfc-shared-disk].   

* **Agrupar a instância do SAP ASCS/SCS em um cluster usando um compartilhamento de arquivo**: Para obter mais informações sobre essa arquitetura, consulte [clusterizar uma instância do SAP ASCS/SCS em um cluster de failover do Windows usando o compartilhamento de arquivos][sap-high-availability-guide-wsfc-file-share].

### <a name="high-availability-architecture-for-an-sap-ascsscs-instance-on-linux"></a>Arquitetura de alta disponibilidade para uma instância do SAP ASCS/SCS no Linux

> ![Linux][Logo_Linux] Linux
> 
> Para obter mais informações sobre clustering de instância SAP ASCS/SCS usando a estrutura de cluster SLES, confira [alta disponibilidade do SAP NetWeaver em VMs do Azure no SUSE Linux Enterprise Server para aplicativos SAP][sap-suse-ascs-ha]. For alternative HA architecture on SLES, which doesn't require highly available NFS see [High-availability guide for SAP NetWeaver on SUSE Linux Enterprise Server with Azure NetApp Files for SAP applications][sap-suse-ascs-ha-anf].

Para obter mais informações sobre como agrupar a instância do SAP ASCS/SCS em um cluster usando a estrutura de cluster do Red Hat, confira [Alta disponibilidade das Máquinas Virtuais do Azure para o SAP NetWeaver no Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel)


### <a name="sap-netweaver-multi-sid-configuration-for-a-clustered-sap-ascsscs-instance"></a>Configuração multi-SID do SAP NetWeaver para uma instância do SAP ASCS/SCS em cluster

> ![Windows][Logo_Windows] Windows
> 
> Atualmente, o multi-SID tem suporte apenas com o WSFC. Há suporte para multi-SID usando compartilhamento de arquivos e disco compartilhado.
> 
> Para saber mais sobre a arquitetura de alta disponibilidade multi-SID, confira:

* [Alta disponibilidade de vários SIDs da instância SAP ASCS/SCS para Clustering de Failover do Windows Server e compartilhamento de arquivos][sap-ascs-ha-multi-sid-wsfc-file-share]

* [Alta disponibilidade de vários SIDs da instância SAP ASCS/SCS para Clustering de Failover do Windows Server e disco compartilhado][sap-ascs-ha-multi-sid-wsfc-shared-disk]

### <a name="high-availability-dbms-instance"></a>Instância do DBMS de alta disponibilidade

O DBMS também é um ponto único de contato em um sistema SAP. Você precisa protegê-lo usando uma solução de alta disponibilidade. A figura a seguir mostra uma solução de alta disponibilidade Always On do SQL Server no Clustering de Failover do Windows Server e no balanceador interno de carga do Azure. O AlwaysOn do SQL Server replica os arquivos de log e dados do DBMS usando sua própria replicação DBMS. Nesse caso, você não precisa de disco compartilhado de cluster, o que simplifica toda a configuração.

![Figura 3: Exemplo de um DBMS SAP de alta disponibilidade, com o AlwaysOn do SQL Server][sap-ha-guide-figure-2003]

_**Figura 3:** Exemplo de um DBMS SAP de alta disponibilidade, com o AlwaysOn do SQL Server_

Para saber mais sobre o clustering do DBMS do SQL Server no Azure usando o modelo de implantação do Azure Resource Manager, confira estes artigos:

* [Configurar um grupo de disponibilidade AlwaysOn em máquinas virtuais do Azure manualmente usando o Gerenciador de recursos][virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]

* [Configurar um balanceador de carga interno do Azure para um grupo de disponibilidade AlwaysOn no Azure][virtual-machines-windows-portal-sql-alwayson-int-listener]

Para obter mais informações sobre clustering DBMS do SAP HANA no Azure usando o modelo de implantação do Azure Resource Manager, consulte [alta disponibilidade do SAP HANA em máquinas virtuais (VMs)][sap-hana-ha].
