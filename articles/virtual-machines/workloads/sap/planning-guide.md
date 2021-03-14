---
title: 'SAP no Azure: Guia de implementação e planejamento'
description: Planejamento e implementação de Máquinas Virtuais do Azure para SAP NetWeaver
author: MSSedusch
manager: juergent
tags: azure-resource-manager
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/17/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017, devx-track-azurecli
ms.openlocfilehash: 8bc289e90470ae9bc8b1996ac08c3144ea78de35
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102504705"
---
# <a name="azure-virtual-machines-planning-and-implementation-for-sap-netweaver"></a>Planejamento e implementação de Máquinas Virtuais do Azure para SAP NetWeaver

[767598]:https://launchpad.support.sap.com/#/notes/767598
[773830]:https://launchpad.support.sap.com/#/notes/773830
[826037]:https://launchpad.support.sap.com/#/notes/826037
[965908]:https://launchpad.support.sap.com/#/notes/965908
[1031096]:https://launchpad.support.sap.com/#/notes/1031096
[1139904]:https://launchpad.support.sap.com/#/notes/1139904
[1173395]:https://launchpad.support.sap.com/#/notes/1173395
[1245200]:https://launchpad.support.sap.com/#/notes/1245200
[1409604]:https://launchpad.support.sap.com/#/notes/1409604
[1558958]:https://launchpad.support.sap.com/#/notes/1558958
[1585981]:https://launchpad.support.sap.com/#/notes/1585981
[1588316]:https://launchpad.support.sap.com/#/notes/1588316
[1590719]:https://launchpad.support.sap.com/#/notes/1590719
[1597355]:https://launchpad.support.sap.com/#/notes/1597355
[1605680]:https://launchpad.support.sap.com/#/notes/1605680
[1619720]:https://launchpad.support.sap.com/#/notes/1619720
[1619726]:https://launchpad.support.sap.com/#/notes/1619726
[1619967]:https://launchpad.support.sap.com/#/notes/1619967
[1750510]:https://launchpad.support.sap.com/#/notes/1750510
[1752266]:https://launchpad.support.sap.com/#/notes/1752266
[1757924]:https://launchpad.support.sap.com/#/notes/1757924
[1757928]:https://launchpad.support.sap.com/#/notes/1757928
[1758182]:https://launchpad.support.sap.com/#/notes/1758182
[1758496]:https://launchpad.support.sap.com/#/notes/1758496
[1772688]:https://launchpad.support.sap.com/#/notes/1772688
[1814258]:https://launchpad.support.sap.com/#/notes/1814258
[1882376]:https://launchpad.support.sap.com/#/notes/1882376
[1909114]:https://launchpad.support.sap.com/#/notes/1909114
[1922555]:https://launchpad.support.sap.com/#/notes/1922555
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1941500]:https://launchpad.support.sap.com/#/notes/1941500
[1956005]:https://launchpad.support.sap.com/#/notes/1956005
[1973241]:https://launchpad.support.sap.com/#/notes/1973241
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2039619]:https://launchpad.support.sap.com/#/notes/2039619
[2069760]:https://launchpad.support.sap.com/#/notes/2069760
[2121797]:https://launchpad.support.sap.com/#/notes/2121797
[2134316]:https://launchpad.support.sap.com/#/notes/2134316
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[azure-cli]:../../../cli-install-nodejs.md
[azure-portal]:https://portal.azure.com
[azure-ps]:/powershell/azure/
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits

[dbms-guide]:dbms-guide.md
[dbms-guide-2.1]:dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f
[dbms-guide-2.2]:dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91
[dbms-guide-2.3]:dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152
[dbms-guide-2]:dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64
[dbms-guide-3]:dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3
[dbms-guide-5.5.1]:dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268
[dbms-guide-5.5.2]:dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b
[dbms-guide-5.6]:dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8
[dbms-guide-5.8]:dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30
[dbms-guide-5]:dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737
[dbms-guide-8.4.1]:dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573
[dbms-guide-8.4.2]:dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d
[dbms-guide-8.4.3]:dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c
[dbms-guide-8.4.4]:dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818
[dbms-guide-900-sap-cache-server-on-premises]:dbms-guide.md#642f746c-e4d4-489d-bf63-73e80177a0a8

[dbms-guide-figure-100]:media/virtual-machines-shared-sap-dbms-guide/100_storage_account_types.png
[dbms-guide-figure-200]:media/virtual-machines-shared-sap-dbms-guide/200-ha-set-for-dbms-ha.png
[dbms-guide-figure-300]:media/virtual-machines-shared-sap-dbms-guide/300-reference-config-iaas.png
[dbms-guide-figure-400]:media/virtual-machines-shared-sap-dbms-guide/400-sql-2012-backup-to-blob-storage.png
[dbms-guide-figure-500]:media/virtual-machines-shared-sap-dbms-guide/500-sql-2012-backup-to-blob-storage-different-containers.png
[dbms-guide-figure-600]:media/virtual-machines-shared-sap-dbms-guide/600-iaas-maxdb.png
[dbms-guide-figure-700]:media/virtual-machines-shared-sap-dbms-guide/700-livecach-prod.png
[dbms-guide-figure-800]:media/virtual-machines-shared-sap-dbms-guide/800-azure-vm-sap-content-server.png
[dbms-guide-figure-900]:media/virtual-machines-shared-sap-dbms-guide/900-sap-cache-server-on-premises.png

[deployment-guide]:deployment-guide.md
[deployment-guide-2.2]:deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94
[deployment-guide-3.1.2]:deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab
[deployment-guide-3.2]:deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281
[deployment-guide-3.3]:deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2
[deployment-guide-3.4]:deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1
[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e
[deployment-guide-4.1]:deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7
[deployment-guide-4.2]:deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e
[deployment-guide-4.3]:deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc
[deployment-guide-4.4.2]:deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542
[deployment-guide-4.4]:deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d
[deployment-guide-4.5.1]:deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4
[deployment-guide-4.5.2]:deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f
[deployment-guide-4.5]:deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca
[deployment-guide-5.1]:deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2
[deployment-guide-5.2]:deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1
[deployment-guide-5.3]:deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8

[deployment-guide-configure-monitoring-scenario-1]:deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b
[deployment-guide-configure-proxy]:deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d
[deployment-guide-figure-100]:media/virtual-machines-shared-sap-deployment-guide/100-deploy-vm-image.png
[deployment-guide-figure-1000]:media/virtual-machines-shared-sap-deployment-guide/1000-service-properties.png
[deployment-guide-figure-11]:deployment-guide.md#figure-11
[deployment-guide-figure-1100]:media/virtual-machines-shared-sap-deployment-guide/1100-azperflib.png
[deployment-guide-figure-1200]:media/virtual-machines-shared-sap-deployment-guide/1200-cmd-test-login.png
[deployment-guide-figure-1300]:media/virtual-machines-shared-sap-deployment-guide/1300-cmd-test-executed.png
[deployment-guide-figure-14]:deployment-guide.md#figure-14
[deployment-guide-figure-1400]:media/virtual-machines-shared-sap-deployment-guide/1400-azperflib-error-servicenotstarted.png
[deployment-guide-figure-300]:media/virtual-machines-shared-sap-deployment-guide/300-deploy-private-image.png
[deployment-guide-figure-400]:media/virtual-machines-shared-sap-deployment-guide/400-deploy-using-disk.png
[deployment-guide-figure-5]:deployment-guide.md#figure-5
[deployment-guide-figure-50]:media/virtual-machines-shared-sap-deployment-guide/50-forced-tunneling-suse.png
[deployment-guide-figure-500]:media/virtual-machines-shared-sap-deployment-guide/500-install-powershell.png
[deployment-guide-figure-6]:deployment-guide.md#figure-6
[deployment-guide-figure-600]:media/virtual-machines-shared-sap-deployment-guide/600-powershell-version.png
[deployment-guide-figure-7]:deployment-guide.md#figure-7
[deployment-guide-figure-700]:media/virtual-machines-shared-sap-deployment-guide/700-install-powershell-installed.png
[deployment-guide-figure-760]:media/virtual-machines-shared-sap-deployment-guide/760-azure-cli-version.png
[deployment-guide-figure-900]:media/virtual-machines-shared-sap-deployment-guide/900-cmd-update-executed.png
[deployment-guide-figure-azure-cli-installed]:deployment-guide.md#402488e5-f9bb-4b29-8063-1c5f52a892d0
[deployment-guide-figure-azure-cli-version]:deployment-guide.md#0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda
[deployment-guide-install-vm-agent-windows]:deployment-guide.md#b2db5c9a-a076-42c6-9835-16945868e866
[deployment-guide-troubleshooting-chapter]:deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b

[deploy-template-cli]:../../../resource-group-template-deploy-cli.md
[deploy-template-portal]:../../../resource-group-template-deploy-portal.md
[deploy-template-powershell]:../../../resource-group-template-deploy.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md
[getting-started-dbms]:get-started.md#1343ffe1-8021-4ce6-a08d-3a1553a4db82
[getting-started-deployment]:get-started.md#6aadadd2-76b5-46d8-8713-e8d63630e955
[getting-started-planning]:get-started.md#3da0389e-708b-4e82-b2a2-e92f132df89c

[getting-started-windows-classic]:../../virtual-machines-windows-classic-sap-get-started.md
[getting-started-windows-classic-dbms]:../../virtual-machines-windows-classic-sap-get-started.md#c5b77a14-f6b4-44e9-acab-4d28ff72a930
[getting-started-windows-classic-deployment]:../../virtual-machines-windows-classic-sap-get-started.md#f84ea6ce-bbb4-41f7-9965-34d31b0098ea
[getting-started-windows-classic-dr]:../../virtual-machines-windows-classic-sap-get-started.md#cff10b4a-01a5-4dc3-94b6-afb8e55757d3
[getting-started-windows-classic-ha-sios]:../../virtual-machines-windows-classic-sap-get-started.md#4bb7512c-0fa0-4227-9853-4004281b1037
[getting-started-windows-classic-planning]:../../virtual-machines-windows-classic-sap-get-started.md#f2a5e9d8-49e4-419e-9900-af783173481c

[ha-guide-classic]:https://go.microsoft.com/fwlink/?LinkId=613056

[install-extension-cli]:virtual-machines-linux-enable-aem.md

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png

[msdn-set-Azvmaemextension]:https://msdn.microsoft.com/library/azure/mt670598.aspx

[planning-guide]:planning-guide.md
[planning-guide-1.2]:planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff
[planning-guide-11]:planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058
[planning-guide-11.4.1]:planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77
[planning-guide-11.5]:planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f
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

[powershell-install-configure]:https://docs.microsoft.com/powershell/azure/install-az-ps
[resource-group-authoring-templates]:../../../resource-group-authoring-templates.md
[resource-group-overview]:../../../azure-resource-manager/management/overview.md
[resource-groups-networking]:../../../networking/networking-overview.md
[sap-pam]:https://support.sap.com/pam
[sap-templates-2-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-2-tier-os-disk]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk%2Fazuredeploy.json
[sap-templates-2-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-3-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image%2Fazuredeploy.json
[storage-azure-cli]:../../../storage/common/storage-azure-cli.md
[storage-azure-cli-copy-blobs]:../../../storage/common/storage-azure-cli.md#copy-blobs
[storage-introduction]:../../../storage/common/storage-introduction.md
[storage-powershell-guide-full-copy-vhd]:../../../storage/common/storage-powershell-guide-full.md
[storage-premium-storage-preview-portal]:../../disks-types.md
[storage-redundancy]:../../../storage/common/storage-redundancy.md
[storage-scalability-targets]:../../../storage/common/scalability-targets-standard-accounts.md
[storage-use-azcopy]:../../../storage/common/storage-use-azcopy.md
[template-201-vm-from-specialized-vhd]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd
[templates-101-simple-windows-vm]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image
[virtual-machines-linux-attach-disk-portal]:../../linux/attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../../../resource-manager-deployment-model.md
[virtual-machines-Az-versus-azuresm]:virtual-machines-linux-compare-deployment-models.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:../../virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:../../linux/cli-deploy-templates.md
[virtual-machines-deploy-rmtemplates-powershell]:../../virtual-machines/windows/index.yml
[virtual-machines-linux-agent-user-guide]:../../extensions/agent-linux.md
[virtual-machines-linux-agent-user-guide-command-line-options]:../../extensions/agent-windows.md#command-line-options
[virtual-machines-linux-capture-image]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager-capture]:../../linux/capture-image.md#step-2-capture-the-vm
[virtual-machines-windows-capture-image-resource-manager]:../../windows/capture-image.md
[virtual-machines-windows-capture-image]:../../virtual-machines-windows-generalize-vhd.md
[virtual-machines-windows-capture-image-prepare-the-vm-for-image-capture]:../../virtual-machines-windows-generalize-vhd.md
[virtual-machines-linux-configure-raid]:../../linux/configure-raid.md
[virtual-machines-linux-configure-lvm]:../../linux/configure-lvm.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]:../../virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]:../../linux/suse-create-upload-vhd.md
[virtual-machines-linux-create-upload-vhd-oracle]:../../linux/oracle-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]:../../linux/redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]:../../linux/add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]:../../linux/add-disk.md#format-and-mount-the-disk
[virtual-machines-linux-tutorial]:../../linux/quick-create-cli.md
[virtual-machines-linux-update-agent]:../../linux/update-agent.md
[virtual-machines-manage-availability]:../../linux/availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:virtual-machines-windows-create-powershell.md
[virtual-machines-sizes-linux]:../../linux/sizes.md
[virtual-machines-sizes-windows]:../../windows/sizes.md
[virtual-machines-windows-classic-ps-sql-alwayson-availability-groups]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md
[virtual-machines-windows-classic-ps-sql-int-listener]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener.md
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]:./../../windows/sql/virtual-machines-windows-sql-high-availability-dr.md
[virtual-machines-sql-server-infrastructure-services]:./../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md
[virtual-machines-sql-server-performance-best-practices]:./../../windows/sql/virtual-machines-windows-sql-performance.md
[virtual-machines-upload-image-windows-resource-manager]:../../virtual-machines-windows-upload-image.md
[virtual-machines-windows-tutorial]:../../virtual-machines-windows-hero-tutorial.md
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/documentation/templates/sql-server-2014-alwayson-dsc/
[virtual-network-deploy-multinic-arm-cli]:../../linux/multiple-nics.md
[virtual-network-deploy-multinic-arm-ps]:../../windows/multiple-nics.md
[virtual-network-deploy-multinic-arm-template]:../../../virtual-network/template-samples.md
[virtual-networks-configure-vnet-to-vnet-connection]:../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md
[virtual-networks-create-vnet-arm-pportal]:../../../virtual-network/manage-virtual-network.md#create-a-virtual-network
[virtual-networks-manage-dns-in-vnet]:../../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md
[virtual-networks-multiple-nics-windows]:../../windows/multiple-nics.md
[virtual-networks-multiple-nics-linux]:../../linux/multiple-nics.md
[virtual-networks-nsg]:../../../virtual-network/security-overview.md
[virtual-networks-reserved-private-ip]:../../../virtual-network/virtual-networks-static-private-ip-arm-ps.md
[virtual-networks-static-private-ip-arm-pportal]:../../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[virtual-networks-udr-overview]:../../../virtual-network/virtual-networks-udr-overview.md
[vpn-gateway-about-vpn-devices]:../../../vpn-gateway/vpn-gateway-about-vpn-devices.md
[vpn-gateway-create-site-to-site-rm-powershell]:../../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md
[vpn-gateway-cross-premises-options]:../../../vpn-gateway/vpn-gateway-about-vpngateways.md
[vpn-gateway-site-to-site-create]:../../../vpn-gateway/vpn-gateway-site-to-site-create.md
[vpn-gateway-vpn-faq]:../../../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]:../../../cli-install-nodejs.md
[xplat-cli-azure-resource-manager]:../../../xplat-cli-azure-resource-manager.md
[capture-image-linux-step-2-create-vm-image]:../../linux/capture-image.md#step-2-create-vm-image



O Microsoft Azure permite que empresas adquiram recursos de computação e armazenamento gastando o mínimo de tempo, sem ciclos de compras longos. O serviço de Máquina Virtual do Azure permite que as empresas implantem aplicativos clássicos no Azure, como aplicativos baseados no SAP NetWeaver, além de aumentar a confiabilidade e disponibilidade desses aplicativos sem que sejam necessários mais recursos disponíveis localmente. Os Serviços da Máquina Virtual do Azure também dão suporte à conectividade entre locais, o que permite que as empresas integrem ativamente as Máquinas Virtuais do Azure em seus domínios locais, suas nuvens privadas e sua estrutura do sistema da SAP.
Este white paper descreve os conceitos básicos de máquina virtual do Microsoft Azure e fornece uma explicação das considerações de planejamento e implementação para instalações da SAP NetWeaver no Azure e, por esse motivo, é o documento que deve ser lido antes de iniciar implantações reais da SAP NetWeaver no Azure.
O documento complementa a documentação de instalação do SAP e as anotações do SAP, que representam os principais recursos para instalações e implantações de software SAP em determinadas plataformas.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="summary"></a>Resumo
Computação em Nuvem é um termo amplamente usado que está adquirindo cada vez mais importância no setor de TI, de pequenas empresas até multinacionais e grandes corporações.

O Microsoft Azure é a Plataforma de Serviços de Nuvem da Microsoft que oferece um amplo espectro de novas possibilidades. Agora, os clientes podem provisionar e desprovisionar com rapidez os aplicativos como um serviço na nuvem, para que não sejam limitados por restrições técnicas ou orçamentárias. Em vez de investir tempo e dinheiro na infraestrutura de hardware, as empresas podem se concentrar no aplicativo, nos processos de negócios e em seus benefícios para clientes e usuários.

Com os Serviços de Máquina Virtual do Microsoft Azure, a Microsoft oferece uma plataforma abrangente de IaaS (Infraestrutura como Serviço). Os aplicativos baseados no SAP NetWeaver têm suporte em Máquinas Virtuais do Azure (IaaS). Este whitepaper descreve como planejar e implementar aplicativos baseados na SAP NetWeaver no Microsoft Azure como a plataforma escolhida.

O documento se concentra em dois aspectos principais:

* A primeira parte descreve dois padrões de implantação com suporte para aplicativos baseados no SAP NetWeaver no Azure. Ele também descreve a manipulação geral do Azure com implantações do SAP em mente.
* A segunda parte detalha a implementação dos diferentes cenários descritos na primeira parte.

Para obter recursos adicionais, confira o capítulo [Recursos][planning-guide-1.2] neste documento.

### <a name="definitions-upfront"></a>Definições prévias
No decorrer do documento, usamos os termos a seguir:

* IaaS: Infraestrutura como Serviço
* PaaS: Plataforma como serviço
* SaaS: Software como Serviço
* Componente SAP: um aplicativo SAP individual como ECC, BW, Solution Manager ou S/4HANA.  Os componentes SAP podem ser baseados em tecnologias ABAP ou Java tradicionais ou em um aplicativo não baseado em NetWeaver, como o Business Objects.
* Ambiente SAP: um ou mais componentes SAP agrupados logicamente para executar uma função de negócios, como Desenvolvimento, QAS, Treinamento, DR ou Produção.
* Estrutura do SAP: Este termo refere-se à totalidade dos ativos SAP na estrutura de TI de um cliente. A estrutura da SAP inclui todos os ambientes de produção e de não produção.
* ID do sistema SAP: A combinação de camada DBMS e camada de aplicativo de, por exemplo, um sistema de desenvolvimento SAP ERP, sistema de teste SAP BW, sistema de produção SAP CRM etc. Em implantações do Azure não há suporte para dividir essas duas camadas entre local e Azure. Isso significa que um sistema SAP é implantado localmente ou no Azure. No entanto, você pode implantar os diferentes sistemas de uma estrutura da SAP no Azure ou localmente. Por exemplo, você poderia implantar os sistemas de desenvolvimento e teste SAP CRM no Azure, mas o sistema de produção CRM SAP localmente.
* Entre instalações ou híbrida: Descreve um cenário em que as VMs são implantadas em uma assinatura do Azure com conectividade site a site, multissite ou de ExpressRoute entre os datacenters locais e o Azure. Na documentação comum do Azure, esses tipos de implantações também são descritas como cenários híbridos ou entre instalações. O motivo para a conexão é estender domínios locais, Active Directory/OpenLDAP local e DNS local para o Azure. A estrutura local é estendida para os ativos do Azure da assinatura. Com esta extensão, as VMs podem ser parte do domínio local. Usuários de domínio do domínio local podem acessar os servidores e podem executar serviços nessas VMs (como serviços DBMS). A comunicação e resolução de nomes entre máquinas virtuais implantadas localmente e VMs implantadas no Azure são possíveis. Esse é o caso mais comum e quase exclusivo de implantação de ativos SAP no Azure. Para obter mais informações, confira [este][vpn-gateway-cross-premises-options] artigo e [este][vpn-gateway-site-to-site-create].
* Extensão de Monitoramento do Azure, Monitoramento Avançado e Extensão para SAP do Azure: descreve o mesmo item. Trata-se de uma extensão de VM que precisa ser implantada por você para fornecer alguns dados básicos sobre a infraestrutura do Azure ao Agente de Host do SAP. As Notas do SAP podem se referir a isso como Extensão de Monitoramento ou Monitoramento Avançado. No Azure, nós nos referimos a isso como **Extensão para SAP do Azure**.

> [!NOTE]
> Implantações entre instalações ou híbridas de sistemas SAP em que máquinas virtuais do Azure que executam sistemas SAP são membros de um domínio local têm suporte para sistemas SAP de produção. Configurações entre locais ou híbridas têm suporte para a implantação de partes ou estruturas da SAP completas no Azure. Até mesmo a execução da estrutura da SAP completa no Azure requer que essas VMs sejam parte do domínio local e ADS/OpenLDAP.
>
>



### <a name="resources"></a><a name="e55d1e22-c2c8-460b-9897-64622a34fdff"></a>Recursos
O ponto de entrada para a carga de trabalho SAP na documentação do Azure é encontrado [aqui](./get-started.md). Começando com esse ponto de entrada, você encontra muitos artigos que abordam os tópicos de:

- SAP NetWeaver e Business One no Azure
- Guias do DBMS do SAP para vários sistemas DBMS no Azure
- Alta disponibilidade e recuperação de desastres para carga de trabalho do SAP no Azure
- Diretrizes específicas para executar o SAP HANA no Azure
- Diretriz específica para Instâncias Grandes do Azure HANA para SAP HANA DBMS


> [!IMPORTANT]
> Sempre que possível, um link para os Guias de Instalação SAP ou outra documentação do SAP é usado (Referência InstGuide-01, veja <http://service.sap.com/instguides>). Quando se tratam de pré-requisitos, o processo de instalação ou detalhes de funcionalidades específicas do SAP a documentação do SAP e guias devem ser lidos sempre com cuidado, como os documentos da Microsoft só abrangem tarefas específicas para o software SAP instalado e operado em uma Máquina Virtual do Microsoft Azure.
>
>

As seguintes Notas do SAP estão relacionadas com o tópico do SAP no Azure:

| Número da observação | Title |
| --- | --- |
| [1928533] |Aplicativos SAP no Azure: Produtos com suporte e dimensionamento |
| [2015553] |SAP no Microsoft Azure: Pré-requisitos de suporte |
| [1999351] |Solução de problemas de monitoramento aprimorado do Azure para SAP |
| [2178632] |Métricas-chave de monitoramento para SAP no Microsoft Azure |
| [1409604] |Virtualização no Windows: Monitoramento Avançado |
| [2191498] |SAP no Linux com o Azure: Monitoramento Avançado |
| [2243692] |Linux na VM (IaaS) do Microsoft Azure: Problemas de licença do SAP |
| [1984787] |SUSE Linux Enterprise Server 12: Notas de instalação |
| [2002167] |Red Hat Enterprise Linux 7.x: Instalação e atualização |
| [2069760] |Atualização e instalação do SAP do Oracle Linux 7.x |
| [1597355] |Recomendação de troca de espaço para Linux |

Leia também a [Wiki de SCN](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes), que contém todas as Notas SAP para Linux.

As limitações gerais padrão e as limitações máximas das assinaturas do Azure podem ser encontradas [neste artigo][azure-resource-manager/management/azure-subscription-service-limits-subscription].

## <a name="possible-scenarios"></a>Cenários possíveis
SAP é normalmente visto como um dos aplicativos mais essenciais nas empresas. A arquitetura e as operações desses aplicativos geralmente são complexas, e é importante que você atenda aos requisitos de disponibilidade e desempenho.

Portanto, as empresas precisam pensar cuidadosamente em qual provedor de nuvem escolher para executar processos comerciais críticos de negócios. O Azure é a plataforma de nuvem pública ideal para processos de negócios e aplicativos SAP comercialmente críticos. Considerando a ampla variedade de infraestrutura do Azure, quase todos os sistemas S/4HANA e SAP NetWeaver existentes podem ser hospedados no Azure atualmente. O Azure fornece VMs com muitos terabytes de memória e mais de 200 CPUs. Além disso, o Azure oferece o [Hana em instâncias grandes](./hana-overview-architecture.md), que permitem implantações de escala vertical do Hana de até 24 tb e SAP Hana implantações em expansão de até 120 TB. É possível declarar hoje que quase todos os cenários SAP locais também podem ser executados no Azure.

Para obter uma descrição geral dos cenários e de alguns cenários sem suporte, confira o documento [Carga de trabalho do SAP em cenários compatíveis com a máquina virtual do Azure](./sap-planning-supported-configurations.md).

Confira esses cenários e algumas das condições que foram indicadas como sem suporte na documentação referenciada ao longo de todo o planejamento e do desenvolvimento da arquitetura que você deseja implantar no Azure.

Em geral, o padrão de implantação mais comum é um cenário entre locais, como exibido

![VPN com conectividade Site a Site (entre instalações)][planning-guide-figure-300]

O motivo para muitos clientes aplicarem um padrão de implantação entre instalações é que ele é mais transparente para que todos os aplicativos se estendam localmente no Azure usando o Azure ExpressRoute e tratem o Azure como um datacenter virtual. À medida que mais ativos forem movidos para o Azure, a infraestrutura de rede e a infraestrutura implantada do Azure aumentarão, e os ativos locais serão reduzidos adequadamente. Tudo transparente para usuários e aplicativos.

Para implantar sistemas SAP no Azure IaaS ou IaaS em geral, é importante entender as diferenças significativas entre as ofertas de terceirizados tradicionais ou hosters e as ofertas de IaaS. Enquanto o terceirizado ou hoster tradicional adapta a infraestrutura (tipo de servidor, rede e armazenamento) para a carga de trabalho que um cliente deseja hospedar, é responsabilidade do cliente ou do parceiro caracterizar a carga de trabalho e escolher os componentes adequados do Azure de VMs, armazenamento e rede para implantações de IaaS.

Para coletar dados para o planejamento de sua implantação no Azure, é importante:

- Avaliar quais produtos SAP têm suporte para execução em VMs do Azure
- Avaliar quais versões específicas do sistema operacional têm suporte com VMs específicas do Azure para esses produtos SAP
- Avaliar quais versões de DBMS têm suporte para seus produtos SAP com VMs específicas do Azure
- Avaliar se algumas das versões necessárias do SO/DBMS exigem que você execute a versão do SAP, a atualização do pacote de suporte e as atualizações de kernel para obter uma configuração com suporte
- Avaliar se você precisa migrar para sistemas operacionais diferentes a fim de implantar no Azure.

Os detalhes sobre os componentes do SAP com suporte no Azure, unidades de infraestrutura do Azure com suporte e versões do sistema operacional relacionadas e versões do DBMS são explicados no artigo [Quais softwares SAP são compatíveis com as implantações do Azure](./sap-supported-product-on-azure.md). Os resultados obtidos da avaliação de versões válidas do SAP, do sistema operacional e das versões do DBMS têm um grande impacto sobre os esforços para mover os sistemas SAP para o Azure. Os resultados dessa avaliação definirão se pode haver esforços de preparação significativos nos casos em que são necessárias atualizações de versão do SAP ou alterações de sistemas operacionais.


## <a name="azure-regions"></a><a name="be80d1b9-a463-4845-bd35-f4cebdb5424a"></a>Regiões do Azure
Os serviços do Azure da Microsoft são coletados nas regiões do Azure. Uma região do Azure é uma coleção de datacenters que contém o hardware e a infraestrutura que executa e hospeda os diferentes serviços do Azure. Essa infraestrutura inclui um grande número de nós que funcionam como nós de computação ou nós de armazenamento ou que executam a funcionalidade de rede.

Para obter uma lista das diferentes regiões do Azure, confira o artigo [Regiões geográficas do Azure](https://azure.microsoft.com/global-infrastructure/geographies/). Nem todas as regiões do Azure oferecem os mesmos serviços. Dependendo do produto SAP que você deseja executar e do sistema operacional e DBMS relacionados a ele, você pode acabar em uma situação em que uma determinada região não oferece os tipos de VM necessários. Isso se aplica sobretudo à execução do SAP HANA, em que, normalmente, você precisa de VMs da série M/Mv2. Essas famílias de VMs são implantadas somente em um subconjunto das regiões. Você pode descobrir exatamente quais VMs, tipos, tipos de armazenamento do Azure ou outros serviços do Azure estão disponíveis em quais regiões com a ajuda do site [Produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/). À medida que você inicia o planejamento e tem certas regiões em mente como a região primária e, eventualmente, a região secundária, você precisa investigar primeiro se os serviços necessários estão disponíveis nessas regiões.

### <a name="availability-zones"></a>Zonas de Disponibilidades
Várias das regiões do Azure implementaram um conceito chamado Zonas de Disponibilidade. As Zonas de disponibilidade são locais separados fisicamente dentro de uma região do Azure. Cada Zona de disponibilidade é composta por um ou mais datacenters equipados com energia, resfriamento e rede independentes. Por exemplo, implantar duas VMs em duas Zonas de Disponibilidade do Azure e implementar uma estrutura de alta disponibilidade para seu sistema do SAP DBMS ou os serviços centrais do SAP oferece o melhor SLA no Azure. Para esse SLA de máquina virtual em particular no Azure, verifique a versão mais recente de [SLAs de Máquina Virtual](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Como as regiões do Azure se desenvolveram e se expandiram rapidamente nos últimos anos, a topologia dessas regiões, o número de datacenters físicos, a distância entre esses datacenters e a distância entre as Zonas de Disponibilidade do Azure podem ser diferentes. E, com isso, a latência de rede.

O princípio de Zonas de Disponibilidade não se aplica ao serviço específico do HANA de [Instâncias Grandes do HANA](./hana-overview-architecture.md). Os contratos de nível de serviço para Instâncias Grandes do HANA podem ser encontrados no artigo [SLA para SAP HANA em Instâncias Grandes do Azure](https://azure.microsoft.com/support/legal/sla/sap-hana-large/)


### <a name="fault-domains"></a><a name="df49dc09-141b-4f34-a4a2-990913b30358"></a>Domínios com falhas
Domínios de falha representam uma unidade física de falha, relacionada fortemente à infraestrutura física contida nos data centers e, embora uma folha ou rack físico possa ser considerado um domínio de falha, não há nenhum mapeamento direto um-para-um entre os dois.

Quando você implanta várias máquinas virtuais como parte de um sistema SAP nos Serviços da Máquina Virtual do Microsoft Azure, pode influenciar o controlador de malha do Azure a implantar seu aplicativo em diferentes domínios de falha, atendendo, assim, aos requisitos mais elevados de SLAs de disponibilidade. No entanto, a distribuição dos domínios de falha em uma unidade de escala do Azure (coleção de centenas de nós de computação ou nós de armazenamento e rede) ou a atribuição de VMs a um domínio de falha específico é algo sobre o que você não tem controle direto. Para comandar o controlador de malha do Azure a implantar um conjunto de VMs em diferentes domínios de falha, é necessário atribuir um conjunto de disponibilidade do Azure para as VMs no momento da implantação. Para saber mais sobre conjuntos de disponibilidade do Azure, confira o capítulo [Conjuntos de disponibilidade do Azure][planning-guide-3.2.3] deste documento.


### <a name="upgrade-domains"></a><a name="fc1ac8b2-e54a-487c-8581-d3cc6625e560"></a>Domínios de Atualização
Domínios de Atualização representam uma unidade lógica que ajudam a determinar como uma máquina virtual em um sistema SAP, que consiste em instâncias SAP em execução em várias VMs, é atualizada. Quando ocorre uma atualização, o Microsoft Azure percorre o processo de atualizar esses domínios de atualização individualmente. Ao distribuir as VMs no momento da implantação em diferentes domínios de atualização, você pode proteger seu sistema SAP parcialmente contra um potencial tempo de inatividade. Para forçar o Azure para implantar as VMs de um sistema SAP espalhados em diferentes domínios de atualização, você precisa definir um atributo específico no momento da implantação de cada VM. Semelhante aos domínios de falha, uma unidade de escala do Azure é dividida em vários domínios de atualização. Para comandar o controlador de malha do Azure a implantar um conjunto de VMs em diferentes domínios de atualização, é necessário atribuir um conjunto de disponibilidade do Azure para as VMs no momento da implantação. Para saber mais sobre conjuntos de disponibilidade do Azure, confira o capítulo [Conjuntos de disponibilidade do Azure][planning-guide-3.2.3] abaixo.


### <a name="azure-availability-sets"></a><a name="18810088-f9be-4c97-958a-27996255c665"></a>Conjuntos de disponibilidade do Azure
As Máquinas Virtuais do Azure dentro de um conjunto de disponibilidade do Azure são distribuídas pelo controlador de malha do Azure em diferentes domínios de falha e de atualização. O objetivo da distribuição por diferentes domínios de falha e de atualização é impedir que todas as VMs de um sistema SAP sejam desligadas em caso de manutenção da infraestrutura ou de uma falha em um domínio de falha. Por padrão, as VMs não fazem parte de um conjunto de disponibilidade. A participação de uma VM em um conjunto de disponibilidade é definida no momento da implantação ou posteriormente, por meio da reconfiguração ou reimplantação de uma VM.

Para entender o conceito dos conjuntos de disponibilidade do Azure e a maneira como eles se relacionam com os Domínios de Atualização e de Falha, leia [este artigo][virtual-machines-manage-availability].

Conforme você define os conjuntos de disponibilidade e tenta combinar várias VMs de diferentes famílias dentro de um conjunto, talvez encontre problemas que impeçam você de incluir determinado tipo de VM em um conjunto de disponibilidade. O motivo é que o conjunto de disponibilidade está associado à unidade de escala que contém determinado tipo de host de computação. E determinado tipo de host de computação só pode executar tipos específicos de famílias de VMs. Por exemplo, se você criar um conjunto de disponibilidade, implantar a primeira VM nesse conjunto, escolher um tipo de VM da família Esv3 e, em seguida, tentar implantar como segunda VM uma da família M, a segunda alocação será rejeitada. O motivo é que as VMs da família Esv3 não estão em execução no mesmo hardware de host que as máquinas virtuais da família M. O mesmo problema pode ocorrer quando você tenta redimensionar VMs e mover uma VM para fora da família Esv3 para um tipo de VM da família M. No caso do redimensionamento para uma família de VMs que não pode ser hospedada no mesmo hardware do host, é preciso desligar todas as VMs em seu conjunto de disponibilidade e redimensioná-las para que possam ser executadas no outro tipo de computador host. Para os SLAs de VMs que são implantados no conjunto de disponibilidade, confira o artigo [SLAs de Máquina Virtual](https://azure.microsoft.com/support/legal/sla/virtual-machines/).

O princípio do conjunto de disponibilidade e de atualização e domínio de falha relacionados não se aplica ao serviço específico do HANA de [Instâncias Grandes do HANA](./hana-overview-architecture.md). Os contratos de nível de serviço para Instâncias Grandes do HANA podem ser encontrados no artigo [SLA para SAP HANA em Instâncias Grandes do Azure](https://azure.microsoft.com/support/legal/sla/sap-hana-large/).

> [!IMPORTANT]
> Os conceitos de Zonas de Disponibilidade do Azure e conjuntos de disponibilidade do Azure são mutuamente exclusivos. Isso significa que você pode implantar um par ou várias VMs em uma Zona de Disponibilidade específica ou em um conjunto de disponibilidade do Azure. Mas não em ambos.

### <a name="azure-paired-regions"></a>Regiões emparelhadas do Azure
O Azure está oferecendo pares de regiões do Azure em que a replicação de determinados dados está habilitada entre esses pares de regiões fixas. O emparelhamento de região está documentado no artigo [continuidade de negócios e recuperação de desastres (BCDR): regiões emparelhadas do Azure](../../../best-practices-availability-paired-regions.md). Como descreve o artigo, a replicação de dados é de tipos de armazenamento do Azure vinculados que podem ser configurados por você para replicar na região emparelhada. Consulte também o artigo [redundância de armazenamento em uma região secundária](../../../storage/common/storage-redundancy.md#redundancy-in-a-secondary-region). Os tipos de armazenamento que permitem essa replicação são tipos de armazenamento, que não são adequados para a carga de trabalho do DBMS. Assim, a usabilidade da replicação do armazenamento do Azure seria limitada ao armazenamento de BLOBs do Azure (como para fins de backup) ou a outros cenários de armazenamento de alta latência. Ao verificar as regiões emparelhadas e os serviços que você deseja usar como sua região primária ou secundária, você pode encontrar situações em que os serviços do Azure e/ou os tipos de VM que você pretende usar em sua região primária não estão disponíveis na região emparelhada. Ou você pode encontrar uma situação em que a região emparelhada do Azure não é aceitável por motivos de conformidade de dados. Nesses casos, você precisa usar uma região não emparelhada como região de recuperação de desastres/secundária. Nesse caso, você precisa tomar cuidado com a replicação de parte dos dados que o Azure replicaria por conta própria. Um exemplo de como replicar seu Active Directory e o DNS para sua região de recuperação de desastres é descrito no artigo [Configurar a recuperação de desastre para Active Directory e DNS](../../../site-recovery/site-recovery-active-directory.md)


## <a name="azure-virtual-machine-services"></a>Serviços de máquinas virtuais do Azure
O Azure oferece uma grande variedade de máquinas virtuais que você pode selecionar para implantar. Não há a necessidade de compras antecipadas de tecnologia e infraestrutura. A oferta de serviço de VM do Azure simplifica a manutenção e operação de aplicativos, fornecendo computação sob demanda e armazenamento para hospedar, dimensionar e gerenciar aplicativos Web e aplicativos conectados. O gerenciamento de infraestrutura é automatizado com uma plataforma projetada para alta disponibilidade e dimensionamento dinâmico, para atender às necessidades de uso com a opção de vários modelos de preços diferentes.

![Posicionamento de Serviços da Máquina Virtual do Microsoft Azure][planning-guide-figure-400]

Com as máquinas virtuais do Azure, a Microsoft permite implantar imagens de servidor personalizadas no Azure como instâncias de IaaS. Ou você pode escolher entre uma ampla seleção de imagens de sistema operacional personalizáveis da galeria de imagens do Azure.

Do ponto de vista operacional, o Serviço da Máquina Virtual do Azure oferece experiências semelhantes a máquinas virtuais implantadas localmente. Você é responsável pela administração, pelas operações e também pela aplicação de patches do sistema operacional específico, em execução em uma VM do Azure e em seus aplicativos nessa VM. A Microsoft não fornece mais serviços além da hospedagem dessa VM em sua infraestrutura do Azure (infraestrutura como serviço – IaaS). Para a carga de trabalho do SAP que você implanta como cliente, a Microsoft não tem nenhuma oferta além das ofertas de IaaS.

A plataforma Microsoft Azure é uma plataforma multilocatário. Como resultado, os recursos de armazenamento, de rede e de computação que hospedam VMs do Azure são, com algumas exceções, compartilhadas entre os locatários. Lógica de cotas e limitação inteligente é usada para evitar que um locatário afete o desempenho de outro locatário (vizinho barulhento) de forma drástica. Especialmente na certificação da plataforma do Azure para SAP HANA, a Microsoft precisa provar o isolamento de recursos para casos em que várias VMs podem ser executadas no mesmo host regularmente para o SAP. Embora a lógica do Azure tente manter as variações de largura de banda experimentadas pequenas, plataformas altamente compartilhadas tendem a apresentar variações maiores na disponibilidade de recurso/largura de banda do que muitos clientes podem encontrar em suas implantações locais. A probabilidade de que um sistema SAP no Azure possa sofrer variações maiores do que aquelas em um sistema local precisa ser levada em conta.

### <a name="azure-virtual-machines-for-sap-workload"></a>Máquinas Virtuais do Azure para carga de trabalho do SAP

Para a carga de trabalho do SAP, reduzimos a seleção para famílias de VM diferentes que são adequadas para carga de trabalho do SAP e carga de trabalho do SAP HANA mais especificamente. A maneira como você encontra o tipo de VM correto e sua capacidade de trabalhar por meio da carga de trabalho do SAP é descrita no documento [Qual software do SAP é compatível com implantações do Azure](./sap-supported-product-on-azure.md).

> [!NOTE]
> Os tipos de VM que são certificados para carga de trabalho do SAP, não há excesso de provisionamento de recursos de CPU e memória.

Além da seleção de tipos de VM puramente suportados, você também precisa verificar se esses tipos de VM estão disponíveis em uma região específica com base nos [produtos de site disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/). Porém, o mais importante é que você precisa avaliar se:

- Recursos de CPU e memória de diferentes tipos de VM
- Largura de banda IOPS de diferentes tipos de VM
- Recursos de rede de diferentes tipos de VM
- Número de discos que podem ser anexados
- Capacidade de aproveitar determinados tipos de armazenamento do Azure

atendem às suas necessidades. A maioria desses dados pode ser encontrada [aqui (Linux)][virtual-machines-sizes-linux] e [aqui (Windows)][virtual-machines-sizes-windows] para um tipo de VM em particular.

Como modelo de preços, você tem várias opções de preços diferentes, listadas como:

- Pré-pago
- Um ano reservado
- Três anos reservados
- Preços de spot

O preço de cada uma das diferentes ofertas com diferentes ofertas de serviço em relação a sistemas operacionais e variadas regiões está disponível no site [Preços de Máquinas Virtuais do Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) e [Preços de Máquinas Virtuais do Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). Para obter informações e a flexibilidade de instâncias reservadas de um ano e de três anos, confira estes artigos:

- [O que são Reservas do Azure?](../../../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Flexibilidade de tamanho de máquina virtual com instâncias de VM reservadas](../../reserved-vm-instance-size-flexibility.md)
- [Como o desconto de reserva do Azure é aplicado a máquinas virtuais](../../../cost-management-billing/manage/understand-vm-reservation-charges.md)

Para obter mais informações sobre preços de spot, leia o artigo [Máquinas virtuais spot do Azure](https://azure.microsoft.com/pricing/spot/). Os preços do mesmo tipo de VM também podem variar entre regiões diferentes do Azure. Para alguns clientes, vale a pena implantar em uma região do Azure menos dispendiosa.

Além disso, o Azure oferece o conceito de host dedicado. Esse conceito proporciona mais controle sobre os ciclos de aplicação de patches feitos pelo Azure. Você pode cronometrar a aplicação de patch de acordo com sua programação. Essa oferta destina-se especificamente a clientes com uma carga de trabalho que pode não seguir o ciclo normal de cargas de trabalho. Para saber sobre os conceitos de ofertas de hosts dedicados do Azure, leia o artigo [Host Dedicado do Azure](../../dedicated-hosts.md). O uso dessa oferta tem suporte na carga de trabalho do SAP e é empregado por vários clientes do SAP que desejam ter mais controle sobre a aplicação de patches de infraestrutura e eventuais planos de manutenção da Microsoft. Para obter mais informações sobre como a Microsoft mantém e corrige a infraestrutura do Azure que hospeda máquinas virtuais, leia o artigo [Manutenção para máquinas virtuais no Azure](../../maintenance-and-updates.md).

#### <a name="generation-1-and-generation-2-virtual-machines"></a>Máquinas virtuais de Geração 1 e de Geração 2.
O hipervisor da Microsoft é capaz de lidar com duas gerações diferentes de máquinas virtuais. Esses formatos são chamados **Geração 1** e **Geração 2**. A **Geração 2** foi introduzida no ano de 2012 com o hipervisor do Windows Server 2012. O Azure começou a usar máquinas virtuais de Geração 1. À medida que você implanta máquinas virtuais do Azure, o padrão ainda é usar o formato de Geração 1. Enquanto isso, você também pode implantar formatos de VM de Geração 2. O artigo [Suporte para VMs de Geração 2 no Azure](../../generation-2.md) lista as famílias de VM do Azure que podem ser implantadas como VM de Geração 2. Este artigo também lista as diferenças funcionais importantes das máquinas virtuais de geração 2, pois elas podem ser executadas na nuvem privada do Hyper-V e no Azure. E o mais importante: este artigo também lista as diferenças funcionais entre máquinas virtuais de Geração 1 e VMs de Geração 2, já que elas são executadas no Azure.

> [!NOTE]
> Há diferenças funcionais de VMs de Geração 1 e Geração 2 em execução no Azure. Leia o artigo [Suporte para VMs de Geração 2 no Azure](../../generation-2.md) para ver uma lista dessas diferenças.

Não é possível mover uma VM existente de uma geração para outra. Para alterar a geração da máquina virtual, você precisa implantar uma nova VM da geração que deseja e reinstalar o software que está executando na máquina virtual da geração. Essa alteração afeta apenas a imagem VHD base da VM e não afeta os discos de dados ou os compartilhamentos de NFS ou SMB conectados. Discos de dados, NFS ou compartilhamentos SMB que originalmente foram atribuídos, por exemplo, em uma VM de geração 1.

> [!NOTE]
> A implantação de VMs da família Mv1 como VMs de Geração 2 é possível desde o início de maio de 2020. Com isso, pode haver menos upsizings e downsizings entre as VMs da família Mv1 e Mv2.


### <a name="storage-microsoft-azure-storage-and-data-disks"></a><a name="a72afa26-4bf4-4a25-8cf7-855d6032157f"></a>Armazenamento: Armazenamento do Microsoft Azure e Discos de Dados
Máquinas Virtuais do Microsoft Azure utilizam diferentes tipos de armazenamento. Ao implementar aplicativos SAP nos Serviços da Máquina Virtual do Azure, é importante entender as diferenças entre esses dois tipos principais de armazenamento:

* Armazenamento volátil não persistente.
* Armazenamento persistente.

As VMs do Azure oferecem discos não persistentes depois que uma VM é implantada. No caso de uma reinicialização da VM, todo o conteúdo nessas unidades será apagado. Portanto, é um dado que arquivos de dados e arquivos de log/restauração de bancos de dados sob nenhuma circunstância encontra-se nessas unidades são persistentes. Pode haver exceções para alguns dos bancos de dados, onde essas unidades não persistentes poderiam ser adequadas para espaços de tabela temporários e tempdb. No entanto, evite usar essas unidades VMs da série A, pois essas unidades não persistentes são limitadas na taxa de transferência dessa família VM. Para obter mais detalhes, leia o artigo [Entendendo a unidade temporária em VMs do Windows no Azure](/archive/blogs/mast/understanding-the-temporary-drive-on-windows-azure-virtual-machines)

---
> ![Logotipo do Windows.][Logo_Windows] Windows
>
> A unidade D:\ em uma VM do Azure é uma unidade não persistente que é apoiada por alguns discos locais no nó de computação do Azure. Como ela não é persistente, isso significa que as alterações feitas no conteúdo na unidade D:\ são perdidas quando a VM é reiniciada. Por “alterações”, como arquivos armazenados, diretórios criados, aplicativos instalados etc.
>
> ![Logotipo do Linux.][Logo_Linux] Linux
>
> As VMs do Azure do Linux montam automaticamente uma unidade em /mnt/resource, que é uma unidade não persistente apoiada por discos locais no nó de computação do Azure. Como ela não é persistente, isso significa que as alterações feitas no conteúdo em /mnt/resource são perdidas quando a VM é reinicializada. Por alterações, como arquivos armazenados, diretórios criados, aplicativos instalados etc.
>
>

#### <a name="azure-storage-accounts"></a>Contas de armazenamento do Azure

Ao implantar serviços ou VMs no Azure, a implantação de VHDs e imagens de VM é organizada em unidades chamadas contas de armazenamento do Azure. [As contas de armazenamento do Azure](../../../storage/common/storage-account-overview.md) têm limitações em IOPS, taxa de transferência ou tamanhos que podem conter. Nas últimas limitações, que estão documentadas em:

- [Metas de escalabilidade para contas de armazenamento Standard](../../../storage/common/scalability-targets-standard-account.md)
- [Metas de escalabilidade para contas de armazenamento de blobs de páginas Premium](../../../storage/blobs/scalability-targets-premium-page-blobs.md)

desempenha um papel importante no planejamento de uma implantação do SAP no Azure. Ele estava em você para gerenciar o número de discos persistentes em uma conta de armazenamento. Você precisava gerenciar as contas de armazenamento e, eventualmente, criar novas contas de armazenamento para criar mais discos persistentes.

Nos últimos anos, a introdução dos [Azure Managed disks](../../managed-disks-overview.md) aliviau você dessas tarefas. A recomendação para implantações do SAP é aproveitar o Azure Managed disks em vez de gerenciar contas de armazenamento do Azure por conta própria. O Azure Managed disks distribuirá discos entre diferentes contas de armazenamento, portanto, os limites das contas de armazenamento individuais não serão excedidos.

Em uma conta de armazenamento, você tem um tipo de conceito de pasta chamado ' contêineres ' que pode ser usado para agrupar determinados discos em contêineres específicos.

No Azure, um nome de disco/VHD segue a seguinte conexão de nomenclatura que precisa fornecer um nome exclusivo para o VHD no Azure:

`http(s)://<storage account name>.blob.core.windows.net/<container name>/<vhd name>`

A cadeia de caracteres acima precisa identificar exclusivamente o disco/VHD que está armazenado no armazenamento do Azure.


#### <a name="azure-persisted-storage-types"></a>Tipos de armazenamento persistente do Azure
O Azure oferece uma variedade de opções de armazenamento persistentes que podem ser usadas para a carga de trabalho do SAP e para componentes específicos do SAP Stack. Para obter mais detalhes, leia o documento  [armazenamento do Azure para cargas de trabalho do SAP](./planning-guide-storage.md).


### <a name="microsoft-azure-networking"></a><a name="61678387-8868-435d-9f8c-450b2424f5bd"></a>Rede do Microsoft Azure

O Microsoft Azure fornece uma infraestrutura de rede que permite o mapeamento de todos os cenários que desejamos criar com software SAP. Os recursos são:

* Acesso externo diretamente às VMs via ssh/VNC ou Serviços de Terminal do Windows
* Acesso a serviços e portas específicas usadas por aplicativos nas VMs
* Comunicação interna e resolução de nomes entre um grupo de VMs implantadas como VMs do Azure
* Conectividade entre instalações entre a rede local de um cliente e a rede do Azure
* Conectividade entre Regiões do Azure ou data centers entre sites do Azure

Encontre mais informações aqui: <https://azure.microsoft.com/documentation/services/virtual-network/>

Há muitas possibilidades diferentes para configurar a resolução de nomes e IPs no Azure. Também há um serviço DNS do Azure que pode ser usado, em vez de configurar seu próprio servidor DNS. Encontre mais informações [neste artigo][virtual-networks-manage-dns-in-vnet] e [nesta página](https://azure.microsoft.com/services/dns/).

Para cenários entre instalações ou híbridos, contamos com o fato de que o OpenLDAP/AD/DNS local foi estendido por meio de VPN ou conexão privada ao Azure. Para determinados cenários, conforme documentado aqui, é necessário ter uma réplica do AD/OpenLDAP instalada no Azure.

Já que a resolução de rede e de nomes é uma parte essencial da implantação de banco de dados para um sistema do SAP, esse conceito é abordado em mais detalhes no [Guia de implantação de DBMS][dbms-guide].

##### <a name="azure-virtual-networks"></a>Redes Virtuais do Azure

Ao criar uma Rede Virtual do Azure, você pode definir o intervalo de endereços dos endereços IP privados alocados pela funcionalidade DHCP do Azure. Em cenários entre instalações, o intervalo de endereços IP definido ainda será alocado pelo Azure, usando DHCP. No entanto, a resolução de nomes de domínio é feita localmente (supondo que as VMs são uma parte de um domínio local) e, portanto, pode resolver endereços além dos diferentes Serviços de Nuvem do Azure.

Cada máquina Virtual no Azure precisa ser conectado a uma Rede Virtual.

Mais detalhes podem ser encontrados [neste artigo][resource-groups-networking] e [nesta página](https://azure.microsoft.com/documentation/services/virtual-network/).


> [!NOTE]
> Por padrão, quando uma VM é implantada, não é possível alterar a configuração de rede virtual. As configurações de TCP/IP devem ser deixadas para o servidor DHCP do Azure. O comportamento padrão é a atribuição de IP dinâmico.
>
>

O endereço MAC da placa de rede virtual pode mudar, por exemplo, depois de redimensionar, e o SO convidado Windows ou Linux seleciona a nova placa de rede e usa automaticamente o DHCP para atribuir os endereços IP e DNS nesse caso.

##### <a name="static-ip-assignment"></a>Atribuição de endereço IP estático
É possível atribuir endereços IP reservados ou fixos às VMs em uma Rede Virtual do Azure. Executar as VMs em uma Rede Virtual do Azure oferece uma ótima possibilidade de aproveitar essa funcionalidade se necessário ou quando exigido por alguns cenários. A atribuição de IPS permanece válida durante toda a existência da VM, independentemente de a VM estar em execução ou desligada. Como resultado, você precisa levar o número total de VMs (VMs em execução e paradas) em consideração ao definir o intervalo de endereços IP para a Rede Virtual. O endereço IP permanecerá atribuído até que a VM seja excluída juntamente com sua interface de rede ou até que o endereço IP seja desatribuído novamente. Para obter mais informações, leia [este artigo][virtual-networks-static-private-ip-arm-pportal].

> [!NOTE]
> Você deve atribuir endereços IP estáticos por meio do Azure para vNICs individuais. Você não deve atribuir endereços IP estáticos dentro do SO convidado para um vNIC. Alguns serviços do Azure, como o serviço de Backup do Azure, dependem do fato de que pelo menos o vNIC primário esteja definido como DHCP, e não para endereços IP estáticos. Veja também o documento [Solucionar problemas de backup da máquina virtual do Azure](../../../backup/backup-azure-vms-troubleshoot.md#networking).
>
>

##### <a name="multiple-nics-per-vm"></a>Várias NICs por VM

Você pode definir várias vNICs (placas de interface de rede virtual) para uma Máquina Virtual do Azure. Com a capacidade de ter várias vNICs, você pode começar a configurar o tráfego de rede no qual, por exemplo, o tráfego do cliente é roteado por meio de uma vNIC e o tráfego de back-end é roteado por meio de uma segunda vNIC. Dependendo do tipo de VM, há diferentes limitações quanto ao número de vNICs que podem ser atribuídas a uma VM. Restrições, funcionalidades e detalhes exatos podem ser encontradas nesses artigos:

* [Criar uma VM Windows com várias NICs][virtual-networks-multiple-nics-windows]
* [Criar uma VM Linux com várias NICs][virtual-networks-multiple-nics-linux]
* [Implantar VMs com diversas NICs usando um modelo][virtual-network-deploy-multinic-arm-template]
* [Implantar VMs com diversas NICs usando o PowerShell][virtual-network-deploy-multinic-arm-ps]
* [Implantar VMs com diversas NICs usando a CLI do Azure][virtual-network-deploy-multinic-arm-cli]

#### <a name="site-to-site-connectivity"></a>Conectividade site a site

Entre instalações refere-se a VMs do Azure e locais vinculadas por uma conexão VPN permanente e transparente. Espera-se que esse se torne o padrão de implantação SAP mais comum no Azure. Pressupõe-se que os procedimentos operacionais e processos com instâncias SAP no Azure funcionem de modo transparente. Isso significa que você deve ser capaz de imprimir a partir desses sistemas, bem como usar o TMS (sistema de gerenciamento de transporte) SAP para transportar alterações de um sistema de desenvolvimento no Azure para um sistema de teste que é implantado localmente. Encontre mais documentações relacionadas ao site a site [neste artigo][vpn-gateway-create-site-to-site-rm-powershell]

##### <a name="vpn-tunnel-device"></a>Dispositivo de túnel VPN

Para criar uma conexão site a site (data center local para data center do Azure), você precisa obter e configurar um dispositivo VPN ou usar o RRAS (Serviço de Roteamento e Acesso Remoto), que foi introduzido como um componente de software com o Windows Server 2012.

* [Criar uma rede virtual com uma conexão VPN site a site usando PowerShell][vpn-gateway-create-site-to-site-rm-powershell]
* [Sobre dispositivos VPN para conexões de Gateway de VPN Site a Site][vpn-gateway-about-vpn-devices]
* [Perguntas frequentes de gateway de VPN][vpn-gateway-vpn-faq]

![Conexão site a site entre locais e o Azure][planning-guide-figure-600]

A Figura acima mostra que duas assinaturas do Azure têm subintervalos de endereços IP reservados para uso em redes virtuais no Azure. A conectividade da rede local para o Azure é estabelecida via VPN.

#### <a name="point-to-site-vpn"></a>VPN ponto a site

VPN ponto a site requer que cada computador cliente conecte-se com seu próprio VPN ao Azure. Para os cenários SAP que estamos vendo, a conectividade ponto a site não é prática. Portanto, não é feita nenhuma referência posterior à conectividade VPN ponto a site.

Mais informações podem ser encontradas aqui
* [Configurar uma conexão Ponto a Site com uma rede virtual usando o Portal do Azure](../../../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)
* [Configurar uma conexão Ponto a Site com uma rede virtual usando o PowerShell](../../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

#### <a name="multi-site-vpn"></a>VPN de múltiplos sites

Hoje em dia, o Azure também oferece a possibilidade de criar a conectividade VPN de múltiplos sites para uma assinatura do Azure. Antes, uma única assinatura era limitada a uma ligação VPN site a site. Essa limitação desapareceu com ligações VPN de múltiplos sites para uma única assinatura. Isso torna possível aproveitar mais de uma Região do Azure para uma assinatura específica por meio de configurações entre instalações.

Para obter mais documentação, confira [este artigo][vpn-gateway-create-site-to-site-rm-powershell]

#### <a name="vnet-to-vnet-connection"></a>Conexão VNet a VNet

Usando VPN de múltiplos sites, você precisa configurar uma Rede Virtual do Azure separada em cada uma das regiões. No entanto, com frequência, você tem o requisito de que os componentes de software em diferentes regiões devem comunicar-se uns com os outros. Idealmente, essa comunicação não deve ser roteada de uma Região do Azure para local e daí para outra Região do Azure. Como atalho, o Azure oferece a possibilidade de configurar uma conexão de uma Rede Virtual do Azure em uma região a outra Rede Virtual do Azure hospedada em outra região. Essa funcionalidade é chamada de conexão de VNet a VNet. Encontre mais detalhes sobre essa funcionalidade aqui: <https://azure.microsoft.com/documentation/articles/vpn-gateway-vnet-vnet-rm-ps/>.

#### <a name="private-connection-to-azure-expressroute"></a>Conexão Privada ao Azure ExpressRoute

O Microsoft Azure ExpressRoute permite a criação de conexões privadas entre data centers do Azure e a infraestrutura de local do cliente, ou então em um ambiente de colocalização. O ExpressRoute é oferecido por diversos provedores VPN MPLS (com pacotes comutados) ou outros Provedores de Serviços de Rede. As conexões de ExpressRoute não passam pela Internet pública. As conexões de ExpressRoute oferecem maior segurança, mais confiabilidade por meio de múltiplos circuitos paralelos, velocidades maiores e latências menores do que conexões típicas pela Internet.

Encontre mais detalhes sobre a Azure ExpressRoute e ofertas aqui:

* <https://azure.microsoft.com/documentation/services/expressroute/>
* <https://azure.microsoft.com/pricing/details/expressroute/>
* <https://azure.microsoft.com/documentation/articles/expressroute-faqs/>

O ExpressRoute permite várias assinaturas do Azure por meio de um circuito de ExpressRoute, conforme documentado aqui

* <https://azure.microsoft.com/documentation/articles/expressroute-howto-linkvnet-arm/>
* <https://azure.microsoft.com/documentation/articles/expressroute-howto-circuit-arm/>

#### <a name="forced-tunneling-in-case-of-cross-premises"></a>Túnel forçado em caso de instalações cruzadas
Para VMs que ingressam em domínios locais por meio de site a site, ponto a site ou ExpressRoute, você precisa garantir que as configurações de proxy da Internet estejam sendo implantadas para todos os usuários nessas VMs também. Por padrão, o software em execução nas VMs ou usuários usando um navegador para acessar a Internet não passariam pelo proxy da empresa, conectariam-se diretamente à Internet por meio do Azure. Mas mesmo a configuração do proxy não é uma solução perfeita para direcionar o tráfego por meio do proxy da empresa, pois verificar o proxy é responsabilidade do software e dos serviços. Se o software em execução na VM não está fazendo isso ou se um administrador manipular as configurações, o tráfego para a Internet poderá ser novamente desviado diretamente através do Azure para a Internet.

Para evitar essa conectividade de internet direta, você pode configurar o Túnel Forçado com conectividade site a site entre locais e o Azure. A descrição detalhada do recurso Túnel Forçado foi publicada aqui: <https://azure.microsoft.com/documentation/articles/vpn-gateway-forced-tunneling-rm/>

O Túnel Forçado com ExpressRoute está habilitado por clientes que anunciam uma rota padrão por meio de sessões de emparelhamento via protocolo BGP do ExpressRoute.

#### <a name="summary-of-azure-networking"></a>Resumo da rede do Azure

Este capítulo continha muitos pontos importantes sobre a Rede do Azure. Eis aqui um resumo dos pontos principais:

* Redes Virtuais do Azure permitem que você coloque uma estrutura de rede em sua implantação do Azure. Redes virtuais podem ser isoladas umas das outras ou, com a ajuda de Grupos de Segurança de Rede, o tráfego entre VNets pode ser controlado.
* As Redes Virtuais do Azure podem ser aproveitadas para atribuir intervalos de endereços IP às VMs ou atribuir endereços IP fixos às VMs
* Para configurar uma conexão site a site ou ponto a site, você precisa primeiro criar uma Rede Virtual do Azure
* Após a implantação de uma máquina virtual, não é possível alterar a Rede Virtual atribuída à VM

### <a name="quotas-in-azure-virtual-machine-services"></a>Cotas nos serviços de máquina virtual do Azure
Precisamos ser claros sobre o fato de que a infraestrutura de rede e de armazenamento é compartilhada entre as VMs que executam uma variedade de serviços na infraestrutura do Azure. Como nos data centers do próprio cliente, o excesso de provisionamento de alguns recursos de infraestrutura ocorre em um certo grau. A plataforma Microsoft Azure usa cotas de disco, CPU, rede e outras para limitar o consumo de recursos e para manter o desempenho consistente e previsível.  Os diferentes tipos de VM (A5, A6 etc.) têm diferentes cotas para o número de discos, CPU, RAM e rede.

> [!NOTE]
> Recursos de CPU e memória dos tipos de VM aos quais o SAP dá suporte são pré-alocados nos nós de host. Isso significa que, após a VM ser implantada, os recursos no host estarão disponíveis conforme definido pelo tipo de VM.
>
>

Ao planejar e dimensionar soluções SAP no Azure, as cotas para cada tamanho de máquina virtual devem ser consideradas. As cotas de VM são descritas [aqui (Linux)][virtual-machines-sizes-linux] e [aqui (Windows)][virtual-machines-sizes-windows].

As cotas descritas representam os valores máximos teóricos.  O limite de IOPS por disco pode ser obtido com pequenas e/SS (8 KB), mas possivelmente pode não ser alcançado com grandes e/SS (1 MB).  O limite de IOPS é aplicado na granularidade de discos individuais.

Como uma árvore de decisão grosseira para decidir se um sistema SAP se ajusta aos Serviços da Máquina Virtual do Azure e seus recursos ou se um sistema existente precisa ser configurado de forma diferente para implantar o sistema no Azure, a árvore de decisão abaixo pode ser usada:

![Árvore de decisão para decidir a capacidade de implantar um sistema SAP no Azure][planning-guide-figure-700]

1. as informações mais importantes para começar são o requisito de SAPS para um determinado sistema do SAP. Os requisitos de SAPS precisam ser divididos entre a parte do DBMS e a parte do aplicativo SAP, mesmo que o sistema SAP já esteja implantado localmente em uma configuração de duas camadas. Para os sistemas existentes, os SAPS relacionados ao hardware em uso geralmente podem ser determinados ou estimados com base nos parâmetros de comparação de SAP existentes. Os resultados podem ser encontrados [aqui](https://sap.com/about/benchmark.html). Para sistemas SAP recém-implantados, você deverá ter feito um exercício de dimensionamento que deve determinar os requisitos de SAPS do sistema.
1. para sistemas existentes, o volume de E/S e operações de E/S por segundo no servidor DBMS devem ser medidos. Para sistemas recém-planejados, o exercício de dimensionamento para o novo sistema também deverá dar uma ideia dos requisitos de E/S no lado do DBMS. Se não tiver certeza, eventualmente, você precisará realizar uma prova de conceito.
1. compare o requisito de SAPS do servidor DBMS com os SAPS que os diferentes tipos de VM do Azure podem fornecer. As informações sobre os SAPS dos diferentes tipos de VM do Azure estão documentadas na Nota do SAP [1928533]. O foco deve ser primeiro na VM de DBMS, já que a camada de banco de dados é a camada em um sistema SAP NetWeaver que não é escalado horizontalmente na maioria das implantações. Por outro lado, a camada do aplicativo SAP pode ser escalada horizontalmente. Se nenhum dos tipos de VM do Azure aos quais a SAP dá suporte podem oferecer os SAPS necessários, a carga de trabalho do sistema SAP planejado não pode ser executada no Azure. Você precisará implantar o sistema localmente ou precisará alterar o volume de carga de trabalho para o sistema.
1. conforme documentado [aqui (Linux)][virtual-machines-sizes-linux] e [aqui (Windows)][virtual-machines-sizes-windows], o Azure impõe uma cota de IOPS por disco independentemente de você usar o Armazenamento Standard ou o Armazenamento Premium. Dependo do tipo de VM, o número de discos que pode ser montado varia. Como resultado, você pode calcular um número de IOPS máximo que pode ser alcançado com cada um dos diferentes tipos de VM. Dependendo do layout do arquivo de banco de dados, você pode distribuir discos para que se tornem um volume no SO convidado. No entanto, se o volume de IOPS atual de um sistema SAP implantado exceder os limites calculados do maior tipo de VM do Azure e se não houver nenhuma chance de compensar com mais memória, a carga de trabalho do sistema SAP poderá ser gravemente afetada. Nesses casos, você pode atingir um ponto em que você não deve implantar o sistema no Azure.
1. especialmente em sistemas do SAP implantados localmente em configurações de duas camadas, as chances são de que o sistema talvez precise ser configurado no Azure em uma configuração de três camadas. Nesta etapa, você precisa verificar se há um componente na camada do aplicativo SAP que não pode ser escalado horizontalmente e que não se ajusta aos recursos de CPU e memória oferecidos pelos diferentes tipos de VM do Azure. Se realmente existir tal componente, o sistema SAP e a carga de trabalho não poderão ser implantados no Azure. No entanto, se for possível escalar horizontalmente os componentes do aplicativo SAP em várias VMs do Azure, o sistema poderá ser implantado no Azure.

se os componentes de camada de aplicativo do DBMS e SAP puderem ser executados em VMs do Azure, a configuração precisará ser definida em relação a:

* Número de VMs do Azure
* Tipos de VM para os componentes individuais
* Número de VHDs na VM de DBMS para fornecer IOPS suficientes

## <a name="managing-azure-assets"></a>Gerenciando ativos do Azure

### <a name="azure-portal"></a>Portal do Azure

O Portal do Azure é uma das três interfaces para gerenciar implantações de VM do Azure. As tarefas básicas de gerenciamento, como implantar VMs com base em imagens, podem ser feitas por meio do Portal do Azure. Além disso, a criação de contas de armazenamento, redes virtuais e outros componentes do Azure também são tarefas que o portal do Azure pode realizar bem. No entanto, funcionalidades como carregar VHDs do local para o Azure ou copiar um VHD dentro do Azure são tarefas que exigem ferramentas de terceiros ou administração por meio do PowerShell ou CLI.

![Portal do Microsoft Azure - Visão geral sobre máquina virtual][planning-guide-figure-800]


Tarefas de administração e configuração para a instância de Máquina Virtual são possíveis de dentro do Portal do Azure.

Além de reiniciar e desligar uma máquina Virtual, você também pode anexar, desanexar e criar discos de dados para a instância de Máquina Virtual, capturar a instância para preparação de imagem e configurar o tamanho da instância de Máquina Virtual.

O Portal do Azure oferece as funcionalidades básicas para implantar e configurar VMs e muitos outros serviços do Azure. No entanto, nem todas as funcionalidades disponíveis são cobertas pelo Portal do Azure. No Portal do Azure, não é possível executar tarefas como:

* Carregar VHDs no Azure
* Copiar VMs


### <a name="management-via-microsoft-azure-powershell-cmdlets"></a>Gerenciamento via cmdlets do Microsoft Azure PowerShell

O Windows PowerShell é uma estrutura poderosa e extensível que foi amplamente adotada por clientes implantando grandes quantidades de sistemas no Azure. Após a instalação dos cmdlets do PowerShell em um desktop, laptop ou estação de gerenciamento dedicada, os cmdlets do PowerShell podem ser executados remotamente.

O processo de habilitação de um laptop/desktop local para usar cmdlets do Azure PowerShell e como configurá-los para uso com as assinaturas do Azure é descrito [neste artigo][powershell-install-configure].

Etapas mais detalhadas sobre como instalar, atualizar e configurar os cmdlets do Azure PowerShell também podem ser encontradas [neste capítulo do Guia de implantação][deployment-guide-4.1].

A experiência do cliente até agora tem sido que o PS (PowerShell) é certamente a ferramenta mais eficiente para implantar VMs e criar etapas personalizadas na implantação de VMs. Todos os clientes que executam instâncias SAP no Azure estão usando cmdlets do PS para complementar as tarefas de gerenciamento que eles realizam no Portal do Azure ou até mesmo usando cmdlets do PS exclusivamente para gerenciar suas implantações no Azure. Já que os cmdlets específicos do Azure compartilham a mesma convenção de nomenclatura que os mais de 2000 cmdlets relacionados do Windows, aproveitar esses cmdlets é uma tarefa fácil para os administradores do Windows.

Veja o exemplo aqui: <https://blogs.technet.com/b/keithmayer/archive/2015/07/07/18-steps-for-end-to-end-iaas-provisioning-in-the-cloud-with-azure-resource-manager-arm-powershell-and-desired-state-configuration-dsc.aspx>


A implantação da Extensão para SAP do Azure (confira o capítulo [Extensão para SAP do Azure][planning-guide-9.1] neste documento) só é possível por meio do PowerShell ou da CLI. Portanto, é obrigatório instalar e configurar o PowerShell ou CLI ao implantar ou administrar um sistema SAP NetWeaver no Azure.

Conforme o Azure fornece mais funcionalidade, serão adicionados novos cmdlets do PS que exigirão uma atualização dos cmdlets. Portanto, faz sentido visitar o site de Download do Azure pelo menos uma vez por mês <https://azure.microsoft.com/downloads/> para obter uma nova versão dos cmdlets. A nova versão é instalada substituindo a versão antiga.

Para obter uma lista geral de comandos do PowerShell relacionados ao Azure, acesse aqui: <https://docs.microsoft.com/powershell/azure/>.

### <a name="management-via-microsoft-azure-cli-commands"></a>Gerenciamento por meio de comandos da CLI do Microsoft Azure

Para clientes que usam Linux e querem gerenciar recursos do Azure, o PowerShell pode não ser uma opção. Como alternativa, a Microsoft oferece a CLI do Azure.
A CLI do Azure fornece um conjunto de comandos entre plataformas de software livre para trabalhar com a Plataforma Azure. A CLI do Azure fornece grande parte das mesmas funcionalidades encontradas no Portal do Azure.

Para obter informações sobre instalação, configuração e como usar os comandos da CLI para realizar tarefas do Azure, consulte

* [Instalar a CLI clássica do Azure][xplat-cli]
* [Implantar e gerenciar máquinas virtuais usando modelos do Azure Resource Manager e a CLI do Azure][../../linux/create-ssh-secured-vm-from-template.md]
* [Usar a CLI clássica do Azure para Mac, Linux e Windows com o Azure Resource Manager][xplat-cli-azure-resource-manager]

Leia também o capítulo [CLI do Azure para VMs Linux][deployment-guide-4.5.2], no [Guia de implantação][planning-guide], a fim de saber como usar a CLI do Azure para implantar a Extensão para SAP do Azure.


## <a name="first-steps-planning-a-deployment"></a>Primeiros passos do planejamento de uma implantação
A primeira etapa do planejamento da implantação NÃO é verificar as VMs disponíveis para executar o SAP. A primeira etapa pode ser um pouco demorada, mas o mais importante é trabalhar com equipes de conformidade e segurança em sua empresa para definir quais são as condições de limite para a implantação de qual tipo de carga de trabalho SAP ou processo comercial em nuvem pública. Caso sua empresa tenha implantado outro software antes do Azure, o processo pode ser fácil. Se a sua empresa estiver mais no início da jornada, poderá haver discussões maiores necessárias para descobrir as condições de limite e de segurança que permitem que determinados dados SAP e processos de negócios SAP sejam hospedados na nuvem pública.

Como uma ajuda útil, você pode acessar as [ofertas de conformidade da Microsoft](/microsoft-365/compliance/offering-home) para obter uma lista de ofertas de conformidade que a Microsoft pode fornecer.

Outras áreas de preocupações, como criptografia de dados para dados em repouso ou outra criptografia no serviço do Azure, estão documentadas em [Visão geral da criptografia do Azure](../../../security/fundamentals/encryption-overview.md).

Não subestime esta fase do projeto em seu planejamento. Somente quando você tiver um acordo e regras sobre esse tópico, precisará ir para a próxima etapa, que é o planejamento da arquitetura de rede implantada no Azure.


## <a name="different-ways-to-deploy-vms-for-sap-in-azure"></a>Diferentes maneiras de implantar VMs para SAP no Azure

Neste capítulo, você aprenderá as diferentes maneiras de implantar uma VM no Azure. Procedimentos adicionais de preparação, bem como a manipulação de VHDs e VMs no Azure, são abordados neste capítulo.

### <a name="deployment-of-vms-for-sap"></a>Implantação de VMs para SAP

O Microsoft Azure oferece várias maneiras de implantar VMs e discos associados. Portanto, é importante entender as diferenças, pois os preparativos das VMs podem ser diferentes dependendo do método de implantação. Em geral, observaremos os seguintes cenários:

#### <a name="moving-a-vm-from-on-premises-to-azure-with-a-non-generalized-disk"></a><a name="4d175f1b-7353-4137-9d2f-817683c26e53"></a>Movendo uma VM do local para o Azure com um disco não generalizado

Você planeja mover um sistema SAP específico do local para o Azure. Isso pode ser feito carregando, no Azure, o VHD que contém os binários do sistema operacional, os binários da SAP e os binários do DBMS, além dos VHDs com os arquivos de dados e arquivos de log do DBMS. Ao contrário do que ocorre no [cenário nº 2 abaixo][planning-guide-5.1.2], você mantém o nome de host, a SID do SAP e as contas de usuário do SAP na VM do Azure, já que eles foram configurados no ambiente local. Portanto, não é necessário generalizar a imagem. Confira os capítulos [Preparação para mover uma VM do local para o Azure com um disco não generalizado][planning-guide-5.2.1] deste documento para obter as etapas de preparação local e de carregamento de VMs ou VHDs não generalizados para o Azure. Leia o capítulo [Cenário 3: mover uma VM do local usando um VHD do Azure não generalizado com SAP][deployment-guide-3.4] no [Guia de implantação][deployment-guide] para obter etapas detalhadas de implantação de uma imagem desse tipo no Azure.

Outra opção que não discutiremos detalhadamente neste guia é usar Azure Site Recovery para replicar servidores de aplicativos SAP NetWeaver e serviços centrais do SAP NetWeaver para o Azure. Não recomendamos usar Azure Site Recovery para a camada de banco de dados e, em vez disso, usar mecanismos de replicação específicos de banco de dados, como a replicação de sistema do HANA. Para obter mais informações, consulte o capítulo [proteger o SAP](../../../site-recovery/site-recovery-workload.md#protect-sap) do guia [sobre recuperação de desastres para aplicativos locais](../../../site-recovery/site-recovery-workload.md) .

#### <a name="deploying-a-vm-with-a-customer-specific-image"></a><a name="e18f7839-c0e2-4385-b1e6-4538453a285c"></a>Implantando uma VM com uma imagem específica do cliente

Devido a requisitos de patch específicos da sua versão do SO ou DBMS, as imagens fornecidas no Azure Marketplace podem não atender às suas necessidades. Portanto, você precisará criar uma VM usando sua própria imagem privada de VM do SO/DBMS, que poderá ser implantada várias vezes posteriormente. Para preparar tal imagem privada para duplicação, os itens a seguir devem ser considerados:

---
> ![Logotipo do Windows.][Logo_Windows] Windows
>
> Veja mais detalhes aqui: <https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed> As configurações do Windows (como o nome de host e SID do Windows) devem ser abstraídas/generalizadas na VM local por meio do comando sysprep.
>
>
> ![Logotipo do Linux.][Logo_Linux] Linux
>
> Siga as etapas descritas nestes artigos para [SUSE][virtual-machines-linux-create-upload-vhd-suse] ou [Red Hat][virtual-machines-linux-redhat-create-upload-vhd] ou [Oracle Linux][virtual-machines-linux-create-upload-vhd-oracle] a fim de preparar um VHD para ser carregado no Azure.
>
>

---
Se já tiver instalado o conteúdo do SAP na VM local (especialmente para sistemas de duas camadas), você poderá adaptar as configurações do sistema SAP após a implantação da VM do Azure por meio do procedimento de renomeação da instância ao qual o Software Provisioning Manager SAP dá suporte (Nota do SAP [1619720]). Confira os capítulos [Preparação para implantar uma VM com uma imagem específica do cliente para SAP][planning-guide-5.2.2] e [Carregar um VHD do local no Azure][planning-guide-5.3.2] deste documento para obter etapas de preparação local e de carregamento de uma VM generalizada para o Azure. Leia o capítulo [Cenário 2: Implantação de uma VM com uma imagem personalizada para SAP][deployment-guide-3.3] no [Guia de implantação][deployment-guide] para obter etapas detalhadas de implantação de uma imagem desse tipo no Azure.

#### <a name="deploying-a-vm-out-of-the-azure-marketplace"></a>Implantando uma VM com origem no Azure Marketplace

Você gostaria de usar uma imagem de VM fornecida pela Microsoft ou por terceiros, partindo do Azure Marketplace, para implantar a VM. Depois de implantar a VM no Azure, siga as mesmas diretrizes e ferramentas para instalar o software SAP e/ou DBMS na VM, como você faria em um ambiente local. Para obter descrição de implantação mais detalhada, leia o capítulo [Cenário 1: implantar uma VM no Azure Marketplace para SAP][deployment-guide-3.2] no [Guia de implantação][deployment-guide].

### <a name="preparing-vms-with-sap-for-azure"></a><a name="6ffb9f41-a292-40bf-9e70-8204448559e7"></a>Preparando VMs com SAP para o Azure

Antes de carregar as VMs no Azure, você precisará certificar-se de que as VMs e VHDs atendem a certos requisitos. Há pequenas diferenças, dependendo do método de implantação usado.

#### <a name="preparation-for-moving-a-vm-from-on-premises-to-azure-with-a-non-generalized-disk"></a><a name="1b287330-944b-495d-9ea7-94b83aff73ef"></a>Preparação para mover uma VM do local para o Azure com um disco não generalizado

Um método de implantação comum é mover, do local para o Azure, uma VM existente que executa um sistema SAP. Essa VM e o sistema SAP presente nela só devem ser executados no Azure usando o mesmo nome de host e, provavelmente, a mesma SID da SAP. Nesse caso, o SO convidado da VM não deve ser generalizado para várias implantações. Se a rede local for estendida para o Azure, será possível usar até as mesmas contas de domínio na VM que eram usadas anteriormente no ambiente local.

Os requisitos ao preparar seu próprio Disco de VM do Azure são:

* Originalmente, o VHD que contém o sistema operacional poderia ter um tamanho máximo de 127 GB. Essa limitação foi eliminada no final de março de 2015. Agora, o VHD que contém o sistema operacional pode ter até 1 TB de tamanho como qualquer outro VHD hospedado do armazenamento do Azure também.
* Ele precisa estar no formato de VHD fixo. VHDs dinâmicos ou VHDs no formato VHDx ainda não têm suporte no Azure. VHDs dinâmicos serão convertidos em VHDs estáticos quando você carregar o VHD com a CLI ou cmdlets do PowerShell
* VHDs que são montados na VM e devem ser montados novamente no Azure para a VM precisam estar em um formato de VHD fixo. Leia [Este artigo](../../managed-disks-overview.md) para obter os limites de tamanho dos discos de dados. VHDs dinâmicos serão convertidos em VHDs estáticos quando você carregar o VHD com a CLI ou cmdlets do PowerShell
* Adicione outra conta local com privilégios de administrador que possa ser usada pelo suporte da Microsoft ou que possa ser atribuída como contexto no qual serviços e aplicativos sejam executados até que a VM seja implantada e mais usuários apropriados possam ser usados.
* Adicione outras contas locais, já que elas podem ser necessárias para o cenário de implantação específico.

---
> ![Logotipo do Windows.][Logo_Windows] Windows
>
> Nesse cenário, nenhuma generalização (sysprep) da VM é necessária para carregar e implantar a VM no Azure.
> Verifique se a unidade D:\ não é usada.
> Defina a montagem automática de disco para discos anexados conforme descrito no capítulo [Definir a montagem automática para os discos anexados][planning-guide-5.5.3] deste documento.
>
> ![Logotipo do Linux.][Logo_Linux] Linux
>
> Nesse cenário, nenhuma generalização (waagent -deprovision) da VM é necessária para carregar e implantar a VM no Azure.
> Certifique-se de que /mnt/resource não é usado e que todos os discos são montados por meio de uuid. Para o disco de SO, certifique-se de que a entrada do carregador de inicialização também reflete a montagem baseada em uuid.
>
>

---
#### <a name="preparation-for-deploying-a-vm-with-a-customer-specific-image-for-sap"></a><a name="57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3"></a>Preparação para implantar uma VM com uma imagem específica do cliente para SAP

Os arquivos VHD que contêm um SO generalizado também são armazenados em contêineres nas Contas de Armazenamento do Azure ou como imagens do Managed Disk. Você pode implantar uma nova VM de uma imagem desse tipo referenciando a imagem VHD ou Managed Disk como uma fonte em seus arquivos de modelo de implantação, conforme descrito no capítulo [Cenário 2: implantar uma VM com uma imagem personalizada para SAP][deployment-guide-3.3] do [Guia de implantação][deployment-guide].

Os requisitos ao preparar sua própria Imagem de VM do Azure são:

* Originalmente, o VHD que contém o sistema operacional poderia ter um tamanho máximo de 127 GB. Essa limitação foi eliminada no final de março de 2015. Agora, o VHD que contém o sistema operacional pode ter até 1 TB de tamanho como qualquer outro VHD hospedado do armazenamento do Azure também.
* Ele precisa estar no formato de VHD fixo. VHDs dinâmicos ou VHDs no formato VHDx ainda não têm suporte no Azure. VHDs dinâmicos serão convertidos em VHDs estáticos quando você carregar o VHD com a CLI ou cmdlets do PowerShell
* VHDs que são montados na VM e devem ser montados novamente no Azure para a VM precisam estar em um formato de VHD fixo. Leia [Este artigo](../../managed-disks-overview.md) para obter os limites de tamanho dos discos de dados. VHDs dinâmicos serão convertidos em VHDs estáticos quando você carregar o VHD com a CLI ou cmdlets do PowerShell
* Adicione outras contas locais, já que elas podem ser necessárias para o cenário de implantação específico.
* Se a imagem contém uma instalação do SAP NetWeaver e renomear o nome do host do nome original no ponto de implantação do Azure é algo provável, é recomendável copiar as versões mais recentes do DVD do Gerenciador de provisionamento de software SAP para o modelo. Isso permitirá que você use a funcionalidade de renomeação SAP fornecida para adaptar o nome do host alterado e/ou alterar a SID do sistema SAP na imagem de VM implantada, assim que uma nova cópia for iniciada.

---
> ![Logotipo do Windows.][Logo_Windows] Windows
>
> Verifique se a unidade D:\ não é usada. Defina a montagem automática de disco para discos anexados conforme descrito no capítulo [Definir a montagem automática para os discos anexados][planning-guide-5.5.3] deste documento.
>
> ![Logotipo do Linux.][Logo_Linux] Linux
>
> Certifique-se de que /mnt/resource não é usado e que todos os discos são montados por meio de uuid. Para o disco de SO, certifique-se de que a entrada do carregador de inicialização também reflete a montagem baseada em uuid.
>
>

---
* A GUI da SAP (para fins administrativos e de instalação) podem ser pré-instalados em um modelo desse tipo.
* Outros softwares necessários para executar as VMs com êxito em cenários entre instalações podem ser instalados, desde que esse software possa trabalhar com a renomeação da VM.

Se a VM estiver suficientemente preparada para ser genérica e eventualmente independente de contas/usuários não disponíveis no cenário de implantação do Azure de destino, a última etapa de preparação, a generalização de uma imagem desse tipo, será realizada.

##### <a name="generalizing-a-vm"></a>Generalizando uma VM
---
> ![Logotipo do Windows.][Logo_Windows] Windows
>
> A última etapa é entrar em uma VM com uma conta de administrador. Abra uma janela de comando do Windows como *administrador*. Vá para %windir%\windows\system32\sysprep e execute sysprep.exe.
> Uma janela pequena será exibida. É importante marcar a opção **Generalizar** (o padrão é desmarcado) e alterar a Opção de Desligamento do seu padrão de 'Reinicializar' para 'Desligar'. Este procedimento pressupõe que o processo sysprep seja executado localmente no SO convidado de uma VM.
> Se desejar executar o procedimento com uma VM já em execução no Azure, siga as etapas descritas [neste artigo](../../windows/capture-image-resource.md).
>
> ![Logotipo do Linux.][Logo_Linux] Linux
>
> [Como capturar uma máquina virtual do Linux para usar como um modelo do Resource Manager][capture-image-linux-step-2-create-vm-image]
>
>

---
### <a name="transferring-vms-and-vhds-between-on-premises-to-azure"></a>Transferindo VMs e VHDs do local para o Azure
Já que carregar discos e imagens de VM para o Azure não é possível por meio do Portal do Azure, você precisa usar a CLI ou cmdlets do Azure PowerShell. Outra possibilidade é o uso da ferramenta 'AzCopy'. Essa ferramenta pode copiar VHDs entre o local e o Azure (em ambas as direções). Ela também pode copiar VHDs entre Regiões do Azure. Consulte [esta documentação][storage-use-azcopy] para download e uso de AzCopy.

Uma terceira alternativa seria usar várias ferramentas de terceiros orientadas por GUI. No entanto, verifique se essas ferramentas dão suporte a Blobs de Páginas do Azure. Para nossos objetivos, precisamos usar o armazenamento de Blob de Páginas do Azure (as diferenças são descritas aqui: <https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>). Além disso, as ferramentas fornecidas pelo Azure são eficientes ao compactar as VMs e VHDs que precisam ser carregados. Isso é importante, porque essa eficiência na compactação reduz o tempo de upload (que varia mesmo assim, dependendo do link de upload do recurso local para a Internet e da região de implantação do Azure de destino). Faz sentido supor que carregar uma VM ou um VHD de um local europeu para os EUA com base em data centers do Azure levará mais tempo do que carregar os mesmos VHDs/VMs para data centers europeus do Azure.

#### <a name="uploading-a-vhd-from-on-premises-to-azure"></a><a name="a43e40e6-1acc-4633-9816-8f095d5a7b6a"></a>Carregando um VHD do local no Azure
Para carregar uma VM ou um VHD existente da rede local, a VM ou o VHD precisa atender aos requisitos listados no capítulo [Preparação para mover uma VM do local para o Azure com um disco não generalizado][planning-guide-5.2.1] deste documento.

Essa VM NÃO precisa ser generalizada e pode ser carregada no estado e forma que tem após o desligamento no lado local. O mesmo é verdadeiro para VHDs adicionais que não contêm nenhum sistema operacional.

##### <a name="uploading-a-vhd-and-making-it-an-azure-disk"></a>Carregando um VHD e tornando-o um Disco do Azure
Nesse caso, desejamos carregar um VHD, com ou sem um SO, e montá-lo em uma VM como um disco de dados ou usá-lo como disco de SO. Este é um processo de várias etapas

**PowerShell**

* Entre na sua assinatura com *Connect-AzAccount*
* Defina a assinatura do contexto com *Set-AzContext* e o parâmetro SubscriptionId ou SubscriptionName – confira <https://docs.microsoft.com/powershell/module/az.accounts/set-Azcontext>
* Carregue o VHD com *Add-AzVhd* em uma Conta de Armazenamento do Azure – confira <https://docs.microsoft.com/powershell/module/az.compute/add-Azvhd>
* (Opcional) Crie um Disco Gerenciado com base no VHD com *New-AzDisk* – confira <https://docs.microsoft.com/powershell/module/az.compute/new-Azdisk>
* Defina o disco do sistema operacional de uma nova configuração de VM com o VHD ou o Disco Gerenciado com *Set-AzVMOSDisk* – confira <https://docs.microsoft.com/powershell/module/az.compute/set-Azvmosdisk>
* Crie uma VM com base na configuração de VM com *New-AzVM* – confira <https://docs.microsoft.com/powershell/module/az.compute/new-Azvm>
* Adicione um disco de dados a uma nova VM com *Add-AzVMDataDisk* – confira <https://docs.microsoft.com/powershell/module/az.compute/add-Azvmdatadisk>

**CLI do Azure**

* Entre na sua assinatura com *az login*
* Selecione a assinatura com o *az account set --subscription `<subscription name or id`>*
* Carregar o VHD com o *az storage blob upload* - consulte [Usar a CLI do Azure com o Armazenamento do Azure][storage-azure-cli]
* (Opcional) Crie um Disco Gerenciado a partir do VHD com *az disk create* - consulte https://docs.microsoft.com/cli/azure/disk
* Criar uma nova VM especificando o VHD ou Managed Disk como disco de SO com *az vm create* e o parâmetro *--attach-os-disk*
* Adicionar um disco de dados a uma nova VM com *az vm disk attach* e o parâmetro *--new*

**Modelo**

* Carregar o VHD com o PowerShell ou a CLI do Azure
* (Opcional) Criar um disco gerenciado do VHD com o PowerShell, CLI do Azure ou o portal do Azure
* Implantar a VM com um modelo JSON referenciando o VHD conforme mostrado [neste modelo JSON de exemplo](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-specialized-vhd-new-or-existing-vnet/azuredeploy.json) ou usando Managed Disks conforme mostrado [neste modelo JSON de exemplo](https://github.com/Azure/azure-quickstart-templates/blob/master/sap-2-tier-user-image-md/azuredeploy.json).

#### <a name="deployment-of-a-vm-image"></a>Implantação de uma imagem de VM
Para carregar uma VM ou um VHD existente da rede local para usá-lo como uma imagem de VM do Azure, a VM ou o VHD precisa atender aos requisitos listados no capítulo [Preparação para implantar uma VM com uma imagem específica do cliente para SAP][planning-guide-5.2.2] deste documento.

* Use *sysprep* no Windows ou *waagent -deprovision* no Linux para generalizar a VM – confira [Sysprep Technical Reference](/previous-versions/windows/it-pro/windows-vista/cc766049(v=ws.10)) (Referência técnica do Sysprep) para Windows ou [Como capturar uma máquina virtual do Linux para usar como um modelo do Resource Manager][capture-image-linux-step-2-create-vm-image] para Linux
* Entre na sua assinatura com *Connect-AzAccount*
* Defina a assinatura do contexto com *Set-AzContext* e o parâmetro SubscriptionId ou SubscriptionName – confira <https://docs.microsoft.com/powershell/module/az.accounts/set-Azcontext>
* Carregue o VHD com *Add-AzVhd* em uma Conta de Armazenamento do Azure – confira <https://docs.microsoft.com/powershell/module/az.compute/add-Azvhd>
* (Opcional) Crie uma Imagem de Disco Gerenciado com base no VHD com *New-AzImage* – confira <https://docs.microsoft.com/powershell/module/az.compute/new-Azimage>
* Definir o disco do sistema operacional de uma nova configuração VM para o
  * VHD com *Set-AzVMOSDisk -SourceImageUri -CreateOption fromImage* – confira <https://docs.microsoft.com/powershell/module/az.compute/set-Azvmosdisk>
  * Imagem de Disco Gerenciado *Set-AzVMSourceImage* – confira <https://docs.microsoft.com/powershell/module/az.compute/set-Azvmsourceimage>
* Crie uma VM com base na configuração de VM com *New-AzVM* – confira <https://docs.microsoft.com/powershell/module/az.compute/new-Azvm>

**CLI do Azure**

* Use *sysprep* no Windows ou *waagent -deprovision* no Linux para generalizar a VM – confira [Sysprep Technical Reference](/previous-versions/windows/it-pro/windows-vista/cc766049(v=ws.10)) (Referência técnica do Sysprep) para Windows ou [Como capturar uma máquina virtual do Linux para usar como um modelo do Resource Manager][capture-image-linux-step-2-create-vm-image] para Linux
* Entre na sua assinatura com *az login*
* Selecione a assinatura com o *az account set --subscription `<subscription name or id`>*
* Carregar o VHD com o *az storage blob upload* - consulte [Usar a CLI do Azure com o Armazenamento do Azure][storage-azure-cli]
* (Opcional) Crie uma Imagem de Disco Gerenciado a partir do VHD com *az image create* - consulte https://docs.microsoft.com/cli/azure/image
* Criar uma nova VM especificando o VHD ou a Imagem do Managed Disk carregados como disco de SO com *az vm create* e o parâmetro *--image*

**Modelo**

* Use *sysprep* no Windows ou *waagent -deprovision* no Linux para generalizar a VM – confira [Sysprep Technical Reference](/previous-versions/windows/it-pro/windows-vista/cc766049(v=ws.10)) (Referência técnica do Sysprep) para Windows ou [Como capturar uma máquina virtual do Linux para usar como um modelo do Resource Manager][capture-image-linux-step-2-create-vm-image] para Linux
* Carregar o VHD com o PowerShell ou a CLI do Azure
* (Opcional) Criar uma Imagem do Disco Gerenciado do VHD com o PowerShell, CLI do Azure ou o portal do Azure
* Implantar a VM com um modelo JSON referenciando o VHD da imagem conforme mostrado [neste modelo JSON de exemplo](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-specialized-vhd-new-or-existing-vnet/azuredeploy.json) ou usando Managed Disks conforme mostrado [neste modelo JSON de exemplo](https://github.com/Azure/azure-quickstart-templates/blob/master/sap-2-tier-user-image-md/azuredeploy.json).

#### <a name="downloading-vhds-or-managed-disks-to-on-premises"></a>Baixando VHDs ou Managed Disks no local
A infraestrutura como serviço do Azure não é uma rua de mão única, em que você pode apenas carregar VHDs e sistemas SAP. Você também pode mover sistemas SAP do Azure de volta para o universo local.

Durante o tempo de download, os VHDs ou Managed Disks não podem estar ativos. Mesmo ao baixar discos montados em VMs, a VM precisa ser desligada e desalocada. Se você quiser apenas baixar o conteúdo do banco de dados, que deverá então ser usado para configurar um novo sistema local, e se for aceitável que o sistema no Azure ainda possa estar operacional durante o tempo de download e instalação do novo sistema, você poderá evitar um longo tempo de inatividade executando um backup de banco de dados compactado em um disco e simplesmente baixando esse disco, em vez de também baixar a VM base do SO.

#### <a name="powershell"></a>PowerShell

* Ao baixar um disco gerenciado, primeiro você precisa obter acesso ao blob subjacente do disco gerenciado. Em seguida, você pode copiar o blob de base para uma nova conta de armazenamento e baixar o blob da conta de armazenamento.

  ```powershell
  $access = Grant-AzDiskAccess -ResourceGroupName <resource group> -DiskName <disk name> -Access Read -DurationInSecond 3600
  $key = (Get-AzStorageAccountKey -ResourceGroupName <resource group> -Name <storage account name>)[0].Value
  $destContext = (New-AzStorageContext -StorageAccountName <storage account name -StorageAccountKey $key)
  Start-AzStorageBlobCopy -AbsoluteUri $access.AccessSAS -DestContainer <container name> -DestBlob <blob name> -DestContext $destContext
  # Wait for blob copy to finish
  Get-AzStorageBlobCopyState -Container <container name> -Blob <blob name> -Context $destContext
  Save-AzVhd -SourceUri <blob in new storage account> -LocalFilePath <local file path> -StorageKey $key
  # Wait for download to finish
  Revoke-AzDiskAccess -ResourceGroupName <resource group> -DiskName <disk name>
  ```

* Ao baixar um VHD depois que o sistema SAP for interrompido e a VM for desligada, você poderá usar o cmdlet do PowerShell `Save-AzVhd` no destino local para baixar os discos VHD de volta para o mundo local. Para fazer isso, é necessária a URL do VHD que você pode encontrar na 'Seção de Armazenamento' do portal do Azure (é necessário navegar até a conta de armazenamento e o contêiner de armazenamento no qual o VHD foi criado) e você precisa saber também para que local o VHD deve ser copiado.

  Em seguida, você pode aproveitar o comando definindo o parâmetro SourceUri como a URL do VHD a baixar e o LocalFilePath como a localização física do VHD (incluindo seu nome). O comando seria semelhante ao seguinte:

  ```powershell
  Save-AzVhd -ResourceGroupName <resource group name of storage account> -SourceUri http://<storage account name>.blob.core.windows.net/<container name>/sapidedata.vhd -LocalFilePath E:\Azure_downloads\sapidesdata.vhd
  ```

  Para obter mais detalhes sobre o cmdlet Save-AzVhd, confira aqui: <https://docs.microsoft.com/powershell/module/az.compute/save-Azvhd>.

#### <a name="azure-cli"></a>CLI do Azure
* Ao baixar um disco gerenciado, primeiro você precisa obter acesso ao blob subjacente do disco gerenciado. Em seguida, você pode copiar o blob de base para uma nova conta de armazenamento e baixar o blob da conta de armazenamento.

  ```azurecli
  az disk grant-access --ids "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>" --duration-in-seconds 3600
  az storage blob download --sas-token "<sas token>" --account-name <account name> --container-name <container name> --name <blob name> --file <local file>
  az disk revoke-access --ids "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>"
  ```

* Ao baixar um VHD depois que o sistema SAP for interrompido e a VM for desligada, você poderá usar o comando CLI do Azure `_azure storage blob download_` no destino local para baixar os discos VHD de volta para o mundo local. Para fazer isso, você precisa do nome e do contêiner do VHD que você pode encontrar na 'Seção de Armazenamento' do Portal do Azure (é necessário navegar até a conta de armazenamento e o contêiner de armazenamento no qual o VHD foi criado) e você precisa saber também para que local o VHD deve ser copiado.

  Em seguida, você pode aproveitar o comando definindo os parâmetros blob e contêiner do VHD a baixar e o destino como a localização física de destino do VHD (incluindo seu nome). O comando seria semelhante ao seguinte:

  ```azurecli
  az storage blob download --name <name of the VHD to download> --container-name <container of the VHD to download> --account-name <storage account name of the VHD to download> --account-key <storage account key> --file <destination of the VHD to download>
  ```

### <a name="transferring-vms-and-disks-within-azure"></a>Transferindo VMs e discos no interior do Azure

#### <a name="copying-sap-systems-within-azure"></a>Copiando sistemas SAP no interior do Azure

Um sistema SAP ou até mesmo um servidor DBMS dedicado com suporte a uma camada de aplicativo SAP provavelmente consistirá em vários discos contendo o SO com os binários ou então os dados e arquivos de log do banco de dados SAP. Nem a funcionalidade do Azure de copiar discos nem a funcionalidade do Azure de salvar discos em um disco local têm um mecanismo de sincronização que capture instantâneos de vários discos de modo consistente. Portanto, o estado dos discos copiados ou salvos, mesmo se eles fossem montados na mesma VM, seria diferente. Isso significa que, no caso concreto de ter diferentes dados e arquivo(s) de log contidos nos diferentes discos, o banco de dados, no final, seria inconsistente.

**Conclusão: para copiar ou salvar discos que fazem parte de uma configuração do sistema SAP, você precisa interromper o sistema SAP e também precisar desligar a VM implantada. Somente então você pode copiar ou baixar o conjunto de discos para criar uma cópia do sistema SAP no Azure ou localmente.**

Os discos de dados podem ser armazenados como arquivos VHD em uma Conta de Armazenamento do Azure e podem ser diretamente anexados a uma máquina virtual ou ser usados como uma imagem. Nesse caso, o VHD é copiado para outro local antes de ser anexado à máquina virtual. O nome completo do arquivo VHD no Azure deve ser exclusivo dentro do Azure. Conforme mencionado anteriormente, o nome em questão é um tipo de nome de três partes com a seguinte aparência:

`http(s)://<storage account name>.blob.core.windows.net/<container name>/<vhd name>`

Discos de dados também podem ser Managed Disks. Nesse caso, o Managed Disk é usado para criar um novo Managed Disk antes de ser anexado à máquina virtual. O nome do Managed Disk deve ser exclusivo dentro de um grupo de recursos.

##### <a name="powershell"></a>PowerShell

Você pode usar os cmdlets do Azure PowerShell para copiar um VHD, conforme mostrado [neste artigo][storage-powershell-guide-full-copy-vhd]. Para criar um Managed Disk, use New-AzDiskConfig e New-AzDisk conforme mostrado no exemplo a seguir.

```powershell
$config = New-AzDiskConfig -CreateOption Copy -SourceUri "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>" -Location <location>
New-AzDisk -ResourceGroupName <resource group name> -DiskName <disk name> -Disk $config
```

##### <a name="azure-cli"></a>CLI do Azure

Você pode usar a CLI do Azure para copiar um VHD. Para criar um novo Managed Disk, use *az disk create* conforme mostrado no exemplo a seguir.

```azurecli
az disk create --source "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>" --name <disk name> --resource-group <resource group name> --location <location>
```

##### <a name="azure-storage-tools"></a>Ferramentas do Armazenamento do Azure

* <https://storageexplorer.com/>

Edições profissionais de Gerenciadores do Armazenamento do Azure que podem ser encontradas aqui:

* <https://www.cerebrata.com/>
* <https://clumsyleaf.com/products/cloudxplorer>

A cópia de um VHD no interior de uma conta de armazenamento é um processo que leva apenas alguns segundos (semelhante ao hardware de SAN criando instantâneos com cópia lenta e cópia em gravação). Depois de ter uma cópia do arquivo VHD, você pode anexá-lo a uma máquina virtual ou usá-lo como uma imagem para anexar cópias do VHD a máquinas virtuais.

##### <a name="powershell"></a>PowerShell

```powershell
# attach a vhd to a vm
$vm = Get-AzVM -ResourceGroupName <resource group name> -Name <vm name>
$vm = Add-AzVMDataDisk -VM $vm -Name newdatadisk -VhdUri <path to vhd> -Caching <caching option> -DiskSizeInGB $null -Lun <lun, for example 0> -CreateOption attach
$vm | Update-AzVM

# attach a managed disk to a vm
$vm = Get-AzVM -ResourceGroupName <resource group name> -Name <vm name>
$vm = Add-AzVMDataDisk -VM $vm -Name newdatadisk -ManagedDiskId <managed disk id> -Caching <caching option> -DiskSizeInGB $null -Lun <lun, for example 0> -CreateOption attach
$vm | Update-AzVM

# attach a copy of the vhd to a vm
$vm = Get-AzVM -ResourceGroupName <resource group name> -Name <vm name>
$vm = Add-AzVMDataDisk -VM $vm -Name <disk name> -VhdUri <new path of vhd> -SourceImageUri <path to image vhd> -Caching <caching option> -DiskSizeInGB $null -Lun <lun, for example 0> -CreateOption fromImage
$vm | Update-AzVM

# attach a copy of the managed disk to a vm
$vm = Get-AzVM -ResourceGroupName <resource group name> -Name <vm name>
$diskConfig = New-AzDiskConfig -Location $vm.Location -CreateOption Copy -SourceUri <source managed disk id>
$disk = New-AzDisk -DiskName <disk name> -Disk $diskConfig -ResourceGroupName <resource group name>
$vm = Add-AzVMDataDisk -VM $vm -Caching <caching option> -Lun <lun, for example 0> -CreateOption attach -ManagedDiskId $disk.Id
$vm | Update-AzVM
```

##### <a name="azure-cli"></a>CLI do Azure

```azurecli

# attach a vhd to a vm
az vm unmanaged-disk attach --resource-group <resource group name> --vm-name <vm name> --vhd-uri <path to vhd>

# attach a managed disk to a vm
az vm disk attach --resource-group <resource group name> --vm-name <vm name> --disk <managed disk id>

# attach a copy of the vhd to a vm
# this scenario is currently not possible with Azure CLI. A workaround is to manually copy the vhd to the destination.

# attach a copy of a managed disk to a vm
az disk create --name <new disk name> --resource-group <resource group name> --location <location of target virtual machine> --source <source managed disk id>
az vm disk attach --disk <new disk name or managed disk id> --resource-group <resource group name> --vm-name <vm name> --caching <caching option> --lun <lun, for example 0>
```

#### <a name="copying-disks-between-azure-storage-accounts"></a><a name="9789b076-2011-4afa-b2fe-b07a8aba58a1"></a>Copiando discos entre Contas de Armazenamento do Azure
Esta tarefa não pode ser realizada no Portal do Azure. Você pode usar cmdlets do Azure PowerShell, a CLI do Azure ou um navegador de armazenamento de terceiros. Os comandos CLI ou cmdlets do PowerShell podem criar e gerenciar blobs, os quais incluem a capacidade de copiar blobs em regiões entre contas de armazenamento de forma assíncrona na assinatura do Azure.

##### <a name="powershell"></a>PowerShell
Você também pode copiar VHDs entre assinaturas. Para obter mais informações, leia [este artigo][storage-powershell-guide-full-copy-vhd].

O fluxo básico da lógica de cmdlet do PS tem esta aparência:

* Crie um contexto de conta de armazenamento para a conta de armazenamento de **origem** com *New-AzStorageContext* – confira <https://docs.microsoft.com/powershell/module/az.storage/new-AzStoragecontext>
* Crie um contexto de conta de armazenamento para a conta de armazenamento de **destino** com *New-AzStorageContext* – confira <https://docs.microsoft.com/powershell/module/az.storage/new-AzStoragecontext>
* Iniciar a cópia com

```powershell
Start-AzStorageBlobCopy -SrcBlob <source blob name> -SrcContainer <source container name> -SrcContext <variable containing context of source storage account> -DestBlob <target blob name> -DestContainer <target container name> -DestContext <variable containing context of target storage account>
```

* Verificar o status da cópia em um loop com

```powershell
Get-AzStorageBlobCopyState -Blob <target blob name> -Container <target container name> -Context <variable containing context of target storage account>
```

* Anexe o novo VHD a uma máquina virtual, conforme descrito acima.

Para obter exemplos, confira [este artigo][storage-powershell-guide-full-copy-vhd].

##### <a name="azure-cli"></a>CLI do Azure
* Iniciar a cópia com

```azurecli
az storage blob copy start --source-blob <source blob name> --source-container <source container name> --source-account-name <source storage account name> --source-account-key <source storage account key> --destination-container <target container name> --destination-blob <target blob name> --account-name <target storage account name> --account-key <target storage account name>
```

* Verificar o status da cópia ainda está em um loop com

```azurecli
az storage blob show --name <target blob name> --container <target container name> --account-name <target storage account name> --account-key <target storage account name>
```

* Anexe o novo VHD a uma máquina virtual, conforme descrito acima.

### <a name="disk-handling"></a>Administração de discos

#### <a name="vmdisk-structure-for-sap-deployments"></a><a name="4efec401-91e0-40c0-8e64-f2dceadff646"></a>Estrutura de VM/disco para implantações SAP

Idealmente, a administração da estrutura de uma VM e os discos associados deve ser simples. Em instalações locais, os clientes desenvolveram muitas formas de estruturar uma instalação de servidor.

* Um disco base que contém o sistema operacional e todos os binários do DBMS e/ou SAP. Desde março de 2015, esse disco pode ter até 1 TB de tamanho em vez de restrições anteriores que o limitou a 127 GB.
* Um ou vários discos contendo o arquivo de log do DBMS do banco de dados SAP e o arquivo de log da área de armazenamento temporário do DBMS (se o DBMS der suporte a isso). Se os requisitos de IOPS do log de banco de dados forem altos, você precisará distribuir vários discos para alcançar o volume de IOPS necessário.
* Um número de discos contendo um ou dois arquivos de banco de dados do banco de dados SAP e também os arquivos de dados temporários do DBMS (se o DBMS der suporte a isso).

![Configuração de referência da VM IaaS do Azure para SAP][planning-guide-figure-1300]


---
> ![Logotipo do Windows.][Logo_Windows] Windows
>
> Com muitos clientes, vimos configurações em que, por exemplo, binários de DBMS e SAP não foram instalados na unidade c:\ na qual o sistema operacional estava instalado. Havia vários motivos para isso, mas quando analisamos a raiz, geralmente era que as unidades eram pequenas e as atualizações de SO precisavam de espaço adicional há 10-15 anos atrás. Nenhuma das condições se aplica com muita frequência nos dias de hoje. Hoje, a unidade c:\ pode ser mapeada em VMs ou discos de grande volume. Para manter as implantações simples em sua estrutura, é recomendável seguir o seguinte padrão de implantação para sistemas SAP NetWeaver no Azure
>
> O arquivo de paginação do sistema operacional Windows deve estar na unidade D: (disco não persistente)
>
> ![Logotipo do Linux.][Logo_Linux] Linux
>
> Coloque o arquivo de troca do Linux em /mnt /mnt/resource no Linux, conforme descrito [neste artigo][virtual-machines-linux-agent-user-guide]. O arquivo de troca pode ser configurado no arquivo de configuração do Agente do Linux /etc/waagent.conf. Adicione ou remova as seguintes configurações:
>
>

```console
ResourceDisk.EnableSwap=y
ResourceDisk.SwapSizeMB=30720
```

Para ativar as alterações, é necessário reiniciar o Agente do Linux com

```console
sudo service waagent restart
```

Leia a observação do SAP [1597355] para obter mais detalhes sobre o tamanho recomendado do arquivo de permuta

---
O número de discos usados para os arquivos de dados do DBMS e o tipo de armazenamento do Azure esses discos são hospedados em deve ser determinado pelos requisitos de IOPS e pela latência necessária. Cotas exatas são descritas [neste artigo (Linux)][virtual-machines-sizes-linux] e [neste artigo (Windows)][virtual-machines-sizes-windows].

Experiência de implantações do SAP nos últimos dois anos nos ensinou algumas lições que podem ser resumidas como:

* O tráfego IOPS para diferentes arquivos de dados nem sempre é o mesmo, já que sistemas de cliente existentes podem ter arquivos de dados dimensionados diferentemente que representam seus bancos de dados SAP. Como resultado, usar uma configuração RAID em vários VHDs para colocar os arquivos de dados formados desses discos pelos LUNs mostrou-se uma melhor opção. Havia situações, especialmente com o Armazenamento Standard do Azure, nas quais uma taxa de IOPS atingiu a cota de um único disco com o log de transações do DBMS. Nesses cenários, é recomendado usar o Armazenamento Premium ou, alternativamente, agregar vários discos de Armazenamento Standard com uma faixa de software.

---
> ![Logotipo do Windows.][Logo_Windows] Windows
>
> * [Práticas recomendadas para o SQL Server em Máquinas Virtuais do Azure][virtual-machines-sql-server-performance-best-practices]
>
> ![Logotipo do Linux.][Logo_Linux] Linux
>
> * [Configurar RAID de software no Linux][virtual-machines-linux-configure-raid]
> * [Configurar o LVM em uma VM Linux no Azure][virtual-machines-linux-configure-lvm]
>
>

---
* O Armazenamento Premium está apresentando desempenho significativamente melhor, especialmente para gravações de log de transações críticas. Para cenários SAP que se espera que forneçam produção como desempenho, é altamente recomendável usar séries de VMs que podem aproveitar o Armazenamento Premium do Azure.

Tenha em mente que o disco, que contém o sistema operacional e, como recomendamos, os binários do SAP e do banco de dados (VM de base) também não está mais limitado a 127 GB. Agora ele pode ter até 1 TB de tamanho. Isso deve ser espaço suficiente para manter todos os arquivos necessários, incluindo, por exemplo, logs de trabalho em lotes SAP.

Para obter mais sugestões e detalhes, especificamente para VMs de DBMS, confira o [Guia de implantação de DBMS][dbms-guide]

#### <a name="disk-handling"></a>Administração de discos

Na maioria dos cenários, você precisa criar discos adicionais para implantar o banco de dados SAP na VM. Falamos sobre as considerações de número de discos no capítulo [Estrutura de VM/disco para implantações SAP][planning-guide-5.5.1] deste documento. O Portal do Azure permite anexar e desanexar discos depois que uma VM base é implantada. Os discos podem ser anexados/desanexados quando a VM está instalada e em execução, bem como quando ela é interrompida. Ao anexar um disco, o portal do Azure oferece a possibilidade de anexar um disco vazio ou um disco existente que nesse momento não está anexado a outra VM.

**Observação**: Discos só podem ser anexados a uma única VM em um determinado momento.

![Anexar/desanexar discos do Armazenamento Standard do Azure][planning-guide-figure-1400]

Durante a implantação de uma nova máquina virtual, você pode decidir se deseja usar Managed Disks ou colocar os discos em Contas de Armazenamento do Azure. Se você quiser usar o Armazenamento Premium, é recomendável usar Managed Disks.

Você precisa decidir se deseja criar um disco novo e vazio ou se você deseja selecionar um disco existente que foi carregado anteriormente e deve ser anexado à VM agora.

**IMPORTANTE**: você **NÃO** deseja usar o Cache de Host com o Armazenamento Standard do Azure. Você deve deixar a preferência de Cache de Host no padrão de NENHUM. Com o Armazenamento Premium do Azure, você deverá habilitar o Cache de Leitura se a característica de E/S for lida principalmente como tráfego típico de E/S em relação os arquivos de dados do banco de dados. No caso do arquivo de log de transações do banco de dados, é recomendado usar a opção sem cache.

---
> ![Logotipo do Windows.][Logo_Windows] Windows
>
> [Como anexar um disco de dados no Portal do Azure][virtual-machines-linux-attach-disk-portal]
>
> Se discos forem anexados, você precisará entrar na VM para abrir o Gerenciador de Disco do Windows. Se a montagem automática não estiver habilitada conforme recomendado no capítulo [Definir a montagem automática para os discos anexados][planning-guide-5.5.3], o volume recém-vinculado precisará ser colocado online e inicializado.
>
> ![Logotipo do Linux.][Logo_Linux] Linux
>
> Se discos forem anexados, você precisará entrar na VM e inicializá-los, conforme descrito [neste artigo][virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]
>
>

---
Se o novo disco for um disco vazio, também será necessário formatar o disco. Para formatação, especialmente para arquivos de dados e de log do DBMS, aplicam-se as mesmas recomendações existentes para implantações bare-metal do DBMS.

Uma conta de Armazenamento do Azure não oferece recursos ilimitados em termos de volume de E/S, IOPS e volume de dados. Geralmente as VMs de DBMS são mais afetadas por isso. Talvez seja melhor usar uma conta de armazenamento separada para cada VM se você tiver algumas VMs com volumes altos de E/S para implantar, para permanecer dentro do limite de volume da Conta de Armazenamento do Azure. Caso contrário, você precisa ver como pode equilibrar essas VMs entre diferentes Contas de Armazenamento sem atingir o limite de cada Conta de Armazenamento individual. Mais detalhes são discutidos no [Guia de implantação de DBMS][dbms-guide]. Você também deve ter essas limitações em mente para VMs de servidor de aplicativos SAP puro ou outras VMs que eventualmente possam exigir VHDs adicionais. Essas restrições não se aplicam se você usar o Managed Disk. Se você planeja usar o Armazenamento Premium, é recomendável usar Managed Disks.

Outro tópico relevante para as Contas de Armazenamento é se os VHDs em uma Conta de Armazenamento estão sendo replicados geograficamente. A replicação geográfica é habilitada ou desabilitada no nível da Conta de Armazenamento e não no nível da VM. Se a replicação geográfica estiver habilitada, os VHDs na Conta de Armazenamento serão replicados para outro data center do Azure na mesma região. Antes de decidir sobre isso, você deve considerar a seguinte restrição:

A replicação geográfica do Azure funciona localmente em cada VHD em uma VM e não replica as E/Ss em ordem cronológica entre vários VHDs em uma VM. Portanto, o VHD que representa a VM base, bem como VHDs adicionais vinculados à VM, são replicados de modo independente uns dos outros. Isso significa que não há nenhuma sincronização entre as alterações nos diferentes VHDs. O fato de que as E/Ss são replicadas de modo independente da ordem em que são gravadas significa que a replicação geográfica não tem valor para servidores de banco de dados com bancos de dados distribuídos entre vários VHDs. Além do DBMS, também pode haver outros aplicativos em que os processos gravam ou manipulam dados em diferentes VHDs e nos quais é importante manter a ordem das alterações. Se isso for um requisito, a replicação geográfica no Azure não deverá ser habilitada. Dependendo de você precisar/querer ou não a replicação geográfica para um conjunto de VMs, mas não para outro conjunto, você já pode categorizar VMs e os VHDs relacionados a elas em diferentes Contas de Armazenamento com replicação geográfica habilitada ou desabilitada.

#### <a name="setting-automount-for-attached-disks"></a><a name="17e0d543-7e8c-4160-a7da-dd7117a1ad9d"></a>Definindo a montagem automática para os discos anexados
---
> ![Logotipo do Windows.][Logo_Windows] Windows
>
> Para VMs que são criadas por meio de imagens ou discos próprios, é necessário verificar e possivelmente definir o parâmetro de montagem automática. Definir esse parâmetro permitirá que a VM, após uma reinicialização ou reimplantação no Azure, monte novamente as unidades anexadas/montadas, de modo automático.
> O parâmetro é definido para as imagens fornecidas pela Microsoft no Azure Marketplace.
>
> Para definir a montagem automática, verifique a documentação do executável de linha de comando diskpart.exe aqui:
>
> * [Opções de linha de comando do DiskPart](/previous-versions/windows/it-pro/windows-xp/bb490893(v=technet.10))
> * [Montagem automática](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/cc753703(v=ws.11))
>
> A janela de linha de comando do Windows deve ser aberta como administrador.
>
> Se discos forem anexados, você precisará entrar na VM para abrir o Gerenciador de Disco do Windows. Se a montagem automática não estiver habilitada conforme recomendado no capítulo [Definir a montagem automática para os discos anexados][planning-guide-5.5.3], o volume recém-vinculado precisará ser colocado online e inicializado.
>
> ![Logotipo do Linux.][Logo_Linux] Linux
>
> Você deve inicializar um disco vazio anexado recentemente, conforme descrito [neste artigo][virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux].
> Você também precisa adicionar novos discos a /etc/fstab.
>
>

---
### <a name="final-deployment"></a>Implantação final

Para obter as etapas exatas e de implantação final, especialmente com relação à implantação da Extensão para SAP do Azure, confira o [Guia de implantação][deployment-guide].

## <a name="accessing-sap-systems-running-within-azure-vms"></a>Acessando sistemas SAP em execução nas VMs do Azure

Para cenários em que você deseja se conectar a esses sistemas SAP pela Internet pública usando a GUI do SAP, os procedimentos a seguir precisam ser aplicados.

Posteriormente neste documento, discutiremos o cenário principal, conectar-se a sistemas SAP em implantações entre instalações que têm uma conexão site a site (túnel VPN) ou uma conexão do Azure ExpressRoute entre os sistemas locais e sistemas do Azure.

### <a name="remote-access-to-sap-systems"></a>Acesso remoto aos serviços SAP

Com o Azure Resource Manager, não há mais pontos de extremidade padrão como havia no antigo modelo clássico. Todas as portas de uma VM do Azure Resource Manager estão abertas, desde que:

1. Nenhum grupo de segurança de rede seja definido para a sub-rede ou a interface de rede. O tráfego de rede para VMs do Azure possa ser protegido por meio dos chamados "Grupos de Segurança de Rede". Para obter mais informações, consulte [O que é um NSG (Grupo de Segurança de Rede)?][virtual-networks-nsg]
2. Nenhum Azure Load Balancer seja definido para a interface de rede

Veja a diferença de arquitetura entre o modelo clássico e o ARM, conforme descrito [neste artigo][virtual-machines-azure-resource-manager-architecture].

#### <a name="configuration-of-the-sap-system-and-sap-gui-connectivity-over-the-internet"></a>Configuração de conectividade do Sistema SAP e da GUI da SAP pela Internet

Consulte este artigo, que descreve os detalhes deste tópico:

<https://docs.microsoft.com/archive/blogs/saponsqlserver/sap-gui-connection-closed-when-connecting-to-sap-system-in-azure>

#### <a name="changing-firewall-settings-within-vm"></a>Alterando as configurações de Firewall na VM

Pode ser necessário configurar o firewall em suas máquinas virtuais para permitir o tráfego de entrada para o sistema SAP.

---
> ![Logotipo do Windows.][Logo_Windows] Windows
>
> Por padrão, o Firewall do Windows em uma VM implantada no Azure é ativado. Agora você precisa permitir que a porta SAP seja aberta, caso contrário a GUI da SAP não poderá se conectar.
> Para fazer isso:
>
> * Abra Painel de Controle\Sistema e Segurança \Firewall do Windows em **Configurações Avançadas**.
> * Agora, clique com o botão direito do mouse em Regras de Entrada e escolha **Nova Regra**.
> * No Assistente que aparece a seguir, escolha criar uma nova regra de **Porta**.
> * Na próxima etapa do assistente, deixe a configuração como TCP e digite o número da porta que deseja abrir. Como a ID da nossa instância SAP é 00, usamos 3200. Se a instância tiver um número de instância diferente, a porta que você definiu anteriormente com base no número da instância deverá ser aberta.
> * Na próxima parte do assistente, você deve deixar o item **Permitir Conexão** marcado.
> * Na próxima etapa do assistente, você deve definir se a regra se aplica à rede de Domínio, Privada e Público. Ajuste-o se necessário às suas necessidades. Entretanto, ao conectar-se com a GUI da SAP do exterior pela rede pública, é necessário ter a regra aplicada à rede pública.
> * Na última etapa do assistente, nomeie a regra e pressione **Concluir** para salvar.
>
> A regra entrará em vigor imediatamente.
>
> ![Definição da regra de porta][planning-guide-figure-1600]
>
> ![Logotipo do Linux.][Logo_Linux] Linux
>
> As imagens do Linux no Azure Marketplace não habilitam o firewall iptables por padrão, e a conexão com o sistema SAP deve funcionar. Se você habilitou o iptables ou outro firewall, confira a documentação do iptables ou do firewall usado para permitir o tráfego tcp de entrada para a porta 32xx (em que xx é o número do sistema de seu sistema SAP).
>
>

---
#### <a name="security-recommendations"></a>Recomendações de segurança

A GUI da SAP não se conecta imediatamente a nenhuma das instâncias da SAP (porta 32xx) em execução, mas se conecta primeiro através da porta aberta para o processo do servidor de mensagens SAP (porta 36xx). No passado, a mesma porta foi usada pelo servidor de mensagens para a comunicação interna com as instâncias do aplicativo. Para impedir que servidores de aplicativo locais se comuniquem inadvertidamente com um servidor de mensagens no Azure, as portas de comunicação interna podem ser alteradas. É altamente recomendável alterar a comunicação interna entre o servidor de mensagens SAP e suas instâncias de aplicativo para um número da porta diferente em sistemas que foram clonados por meio de sistemas locais, como um clone de desenvolvimento para testes de projeto, etc. Isso pode ser feito com o parâmetro de perfil padrão:

> rdisp/msserv_internal
>
>

conforme documentado em [Configurações de segurança para o Servidor de Mensagens SAP](https://help.sap.com/saphelp_nwpi71/helpdata/en/47/c56a6938fb2d65e10000000a42189c/content.htm)


### <a name="single-vm-with-sap-netweaver-demotraining-scenario"></a><a name="3e9c3690-da67-421a-bc3f-12c520d99a30"></a>Cenário de treinamento/demonstração de VM única com SAP NetWeaver

![Executando sistemas de demonstração SAP em VMs únicas com os mesmos nomes de VM, isoladas em serviços de nuvem do Azure][planning-guide-figure-1700]

Neste cenário, estamos implementando um cenário de sistema de treinamento/demonstração típico em que o cenário de treinamento/demonstração completo está contido em uma única VM. Vamos supor que a implantação é feita por meio de modelos de imagem de VM. Também pressupomos que várias dessas VMs de demonstração/treinamento precisam ser implantadas com as VMs tendo o mesmo nome. O sistema de treinamento como um todo não tem conectividade com seus ativos locais e é o oposto de uma implantação híbrida.

Pressupõe-se que você criou uma imagem de VM, conforme descrito em algumas seções do capítulo [Preparar VMs com SAP para o Azure][planning-guide-5.2] deste documento.

A sequência de eventos para implementar o cenário tem esta aparência:

##### <a name="powershell"></a>PowerShell

* Criar um novo grupo de recursos para cada estrutura de treinamento/demonstração

```powershell
$rgName = "SAPERPDemo1"
New-AzResourceGroup -Name $rgName -Location "North Europe"
```

* Criar uma nova conta de armazenamento se você não quiser usar Managed Disks

```powershell
$suffix = Get-Random -Minimum 100000 -Maximum 999999
$account = New-AzStorageAccount -ResourceGroupName $rgName -Name "saperpdemo$suffix" -SkuName Standard_LRS -Kind "Storage" -Location "North Europe"
```

* Crie uma nova rede virtual para cada estrutura de treinamento/demonstração para habilitar o uso do mesmo nome do host e endereços IP. A rede virtual é protegida por um Grupo de Segurança de Rede que permite apenas o tráfego para a porta 3389 para habilitar o acesso de Área de Trabalho Remota e porta 22 para SSH.

```powershell
# Create a new Virtual Network
$rdpRule = New-AzNetworkSecurityRuleConfig -Name SAPERPDemoNSGRDP -Protocol * -SourcePortRange * -DestinationPortRange 3389 -Access Allow -Direction Inbound -SourceAddressPrefix * -DestinationAddressPrefix * -Priority 100
$sshRule = New-AzNetworkSecurityRuleConfig -Name SAPERPDemoNSGSSH -Protocol * -SourcePortRange * -DestinationPortRange 22 -Access Allow -Direction Inbound -SourceAddressPrefix * -DestinationAddressPrefix * -Priority 101
$nsg = New-AzNetworkSecurityGroup -Name SAPERPDemoNSG -ResourceGroupName $rgName -Location  "North Europe" -SecurityRules $rdpRule,$sshRule

$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name Subnet1 -AddressPrefix  10.0.1.0/24 -NetworkSecurityGroup $nsg
$vnet = New-AzVirtualNetwork -Name SAPERPDemoVNet -ResourceGroupName $rgName -Location "North Europe"  -AddressPrefix 10.0.1.0/24 -Subnet $subnetConfig
```

* Criar um novo endereço IP público que pode ser usado para acessar a máquina virtual da Internet

```powershell
# Create a public IP address with a DNS name
$pip = New-AzPublicIpAddress -Name SAPERPDemoPIP -ResourceGroupName $rgName -Location "North Europe" -DomainNameLabel $rgName.ToLower() -AllocationMethod Dynamic
```

* Criar uma nova interface de rede para a máquina virtual

```powershell
# Create a new Network Interface
$nic = New-AzNetworkInterface -Name SAPERPDemoNIC -ResourceGroupName $rgName -Location "North Europe" -Subnet $vnet.Subnets[0] -PublicIpAddress $pip
```

* Crie uma máquina virtual. Para este cenário, todas as VMs terão o mesmo nome. A SID das instâncias do SAP NetWeaver nessas VMs também será a mesma. No Grupo de Recursos do Azure, o nome da VM deve ser exclusivo, mas em diferentes Grupos de Recursos do Azure, você pode executar VMs com o mesmo nome. A conta padrão de “Administrador” do Windows ou “raiz” para Linux não são válidas. Portanto, um novo nome de usuário do administrador deve ser definido, junto com uma senha. O tamanho da VM também deve ser definido.

```powershell
#####
# Create a new virtual machine with an official image from the Azure Marketplace
#####
$cred=Get-Credential -Message "Type the name and password of the local administrator account."
$vmconfig = New-AzVMConfig -VMName SAPERPDemo -VMSize Standard_D11

# select image
$vmconfig = Set-AzVMSourceImage -VM $vmconfig -PublisherName "MicrosoftWindowsServer" -Offer "WindowsServer" -Skus "2012-R2-Datacenter" -Version "latest"
$vmconfig = Set-AzVMOperatingSystem -VM $vmconfig -Windows -ComputerName "SAPERPDemo" -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
# $vmconfig = Set-AzVMSourceImage -VM $vmconfig -PublisherName "SUSE" -Offer "SLES-SAP" -Skus "12-SP1" -Version "latest"
# $vmconfig = Set-AzVMSourceImage -VM $vmconfig -PublisherName "RedHat" -Offer "RHEL" -Skus "7.2" -Version "latest"
# $vmconfig = Set-AzVMSourceImage -VM $vmconfig -PublisherName "Oracle" -Offer "Oracle-Linux" -Skus "7.2" -Version "latest"
# $vmconfig = Set-AzVMOperatingSystem -VM $vmconfig -Linux -ComputerName "SAPERPDemo" -Credential $cred

$vmconfig = Add-AzVMNetworkInterface -VM $vmconfig -Id $nic.Id

$vmconfig = Set-AzVMBootDiagnostics -Disable -VM $vmconfig
$vm = New-AzVM -ResourceGroupName $rgName -Location "North Europe" -VM $vmconfig
```

```powershell
#####
# Create a new virtual machine with a VHD that contains the private image that you want to use
#####
$cred=Get-Credential -Message "Type the name and password of the local administrator account."
$vmconfig = New-AzVMConfig -VMName SAPERPDemo -VMSize Standard_D11

$vmconfig = Add-AzVMNetworkInterface -VM $vmconfig -Id $nic.Id

$diskName="osfromimage"
$osDiskUri=$account.PrimaryEndpoints.Blob.ToString() + "vhds/" + $diskName  + ".vhd"

$vmconfig = Set-AzVMOSDisk -VM $vmconfig -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri <path to VHD that contains the OS image> -Windows
$vmconfig = Set-AzVMOperatingSystem -VM $vmconfig -Windows -ComputerName "SAPERPDemo" -Credential $cred
#$vmconfig = Set-AzVMOSDisk -VM $vmconfig -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri <path to VHD that contains the OS image> -Linux
#$vmconfig = Set-AzVMOperatingSystem -VM $vmconfig -Linux -ComputerName "SAPERPDemo" -Credential $cred

$vmconfig = Set-AzVMBootDiagnostics -Disable -VM $vmconfig
$vm = New-AzVM -ResourceGroupName $rgName -Location "North Europe" -VM $vmconfig
```

```powershell
#####
# Create a new virtual machine with a Managed Disk Image
#####
$cred=Get-Credential -Message "Type the name and password of the local administrator account."
$vmconfig = New-AzVMConfig -VMName SAPERPDemo -VMSize Standard_D11

$vmconfig = Add-AzVMNetworkInterface -VM $vmconfig -Id $nic.Id

$vmconfig = Set-AzVMSourceImage -VM $vmconfig -Id <Id of Managed Disk Image>
$vmconfig = Set-AzVMOperatingSystem -VM $vmconfig -Windows -ComputerName "SAPERPDemo" -Credential $cred
#$vmconfig = Set-AzVMOperatingSystem -VM $vmconfig -Linux -ComputerName "SAPERPDemo" -Credential $cred

$vmconfig = Set-AzVMBootDiagnostics -Disable -VM $vmconfig
$vm = New-AzVM -ResourceGroupName $rgName -Location "North Europe" -VM $vmconfig
```

* Opcionalmente, adicione discos adicionais e restaure o conteúdo necessário. Todos os nomes de blob (URLs para os blobs) devem ser exclusivos no Azure.

```powershell
# Optional: Attach additional VHD data disks
$vm = Get-AzVM -ResourceGroupName $rgName -Name SAPERPDemo
$dataDiskUri = $account.PrimaryEndpoints.Blob.ToString() + "vhds/datadisk.vhd"
Add-AzVMDataDisk -VM $vm -Name datadisk -VhdUri $dataDiskUri -DiskSizeInGB 1023 -CreateOption empty | Update-AzVM

# Optional: Attach additional Managed Disks
$vm = Get-AzVM -ResourceGroupName $rgName -Name SAPERPDemo
Add-AzVMDataDisk -VM $vm -Name datadisk -DiskSizeInGB 1023 -CreateOption empty -Lun 0 | Update-AzVM
```

##### <a name="cli"></a>CLI

O código de exemplo a seguir pode ser usado no Linux. Para Windows, use o PowerShell conforme descrito acima ou adapte o exemplo para usar %rgName% em vez de $rgName e defina a variável de ambiente usando o comando *set* do Windows.

* Criar um novo grupo de recursos para cada estrutura de treinamento/demonstração

```azurecli
rgName=SAPERPDemo1
rgNameLower=saperpdemo1
az group create --name $rgName --location "North Europe"
```

* Criar uma nova conta de armazenamento

```azurecli
az storage account create --resource-group $rgName --location "North Europe" --kind Storage --sku Standard_LRS --name $rgNameLower
```

* Crie uma nova rede virtual para cada estrutura de treinamento/demonstração para habilitar o uso do mesmo nome do host e endereços IP. A rede virtual é protegida por um Grupo de Segurança de Rede que permite apenas o tráfego para a porta 3389 para habilitar o acesso de Área de Trabalho Remota e porta 22 para SSH.

```azurecli
az network nsg create --resource-group $rgName --location "North Europe" --name SAPERPDemoNSG
az network nsg rule create --resource-group $rgName --nsg-name SAPERPDemoNSG --name SAPERPDemoNSGRDP --protocol \* --source-address-prefix \* --source-port-range \* --destination-address-prefix \* --destination-port-range 3389 --access Allow --priority 100 --direction Inbound
az network nsg rule create --resource-group $rgName --nsg-name SAPERPDemoNSG --name SAPERPDemoNSGSSH --protocol \* --source-address-prefix \* --source-port-range \* --destination-address-prefix \* --destination-port-range 22 --access Allow --priority 101 --direction Inbound

az network vnet create --resource-group $rgName --name SAPERPDemoVNet --location "North Europe" --address-prefixes 10.0.1.0/24
az network vnet subnet create --resource-group $rgName --vnet-name SAPERPDemoVNet --name Subnet1 --address-prefix 10.0.1.0/24 --network-security-group SAPERPDemoNSG
```

* Criar um novo endereço IP público que pode ser usado para acessar a máquina virtual da Internet

```azurecli
az network public-ip create --resource-group $rgName --name SAPERPDemoPIP --location "North Europe" --dns-name $rgNameLower --allocation-method Dynamic
```

* Criar uma nova interface de rede para a máquina virtual

```azurecli
az network nic create --resource-group $rgName --location "North Europe" --name SAPERPDemoNIC --public-ip-address SAPERPDemoPIP --subnet Subnet1 --vnet-name SAPERPDemoVNet
```

* Crie uma máquina virtual. Para este cenário, todas as VMs terão o mesmo nome. A SID das instâncias do SAP NetWeaver nessas VMs também será a mesma. No Grupo de Recursos do Azure, o nome da VM deve ser exclusivo, mas em diferentes Grupos de Recursos do Azure, você pode executar VMs com o mesmo nome. A conta padrão de “Administrador” do Windows ou “raiz” para Linux não são válidas. Portanto, um novo nome de usuário do administrador deve ser definido, junto com uma senha. O tamanho da VM também deve ser definido.

```azurecli
#####
# Create virtual machines using storage accounts
#####
az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image SUSE:SLES-SAP:12-SP1:latest --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os --authentication-type password
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image RedHat:RHEL:7.2:latest --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os --authentication-type password
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image "Oracle:Oracle-Linux:7.2:latest" --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os --authentication-type password

#####
# Create virtual machines using Managed Disks
#####
az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image SUSE:SLES-SAP:12-SP1:latest --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os --authentication-type password
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image RedHat:RHEL:7.2:latest --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os --authentication-type password
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image "Oracle:Oracle-Linux:7.2:latest" --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os --authentication-type password
```

```azurecli
#####
# Create a new virtual machine with a VHD that contains the private image that you want to use
#####
az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --os-type Windows --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os --image <path to image vhd>
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --os-type Linux --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os --image <path to image vhd> --authentication-type password

#####
# Create a new virtual machine with a Managed Disk Image
#####
az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os --image <managed disk image id>
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os --image <managed disk image id> --authentication-type password
```

* Opcionalmente, adicione discos adicionais e restaure o conteúdo necessário. Todos os nomes de blob (URLs para os blobs) devem ser exclusivos no Azure.

```azurecli
# Optional: Attach additional VHD data disks
az vm unmanaged-disk attach --resource-group $rgName --vm-name SAPERPDemo --size-gb 1023 --vhd-uri https://$rgNameLower.blob.core.windows.net/vhds/data.vhd  --new

# Optional: Attach additional Managed Disks
az vm disk attach --resource-group $rgName --vm-name SAPERPDemo --size-gb 1023 --disk datadisk --new
```

##### <a name="template"></a>Modelo

Você pode usar os modelos de exemplo no repositório Azure-QuickStart-templates no GitHub.

* [VM Linux Simples](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux)
* [VM Windows Simples](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows)
* [VM de imagem](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

### <a name="implement-a-set-of-vms-that-communicate-within-azure"></a>Implementar um conjunto de VMs que se comuniquem dentro do Azure

Esse cenário não híbrido é um cenário típico para fins de treinamento e demonstração, no qual o software que representa o cenário de demonstração/treinamento é distribuído entre várias VMs. Os diferentes componentes instalados em diferentes VMs precisam se comunicar uns com os outros. Novamente, nesse cenário, nenhuma comunicação de rede local ou cenário entre instalações é necessária.

Esse cenário é uma extensão da instalação descrita no capítulo [Cenário de treinamento/demonstração de VM única com SAP NetWeaver][planning-guide-7.1] deste documento. Nesse caso, mais máquinas virtuais serão adicionadas a um grupo de recursos existente. No exemplo a seguir, a estrutura de treinamento consiste em uma VM SAP ASCS/SCS, uma VM executando um DBMS e uma VM de instância de servidor de aplicativos SAP.

Antes de criar esse cenário, você precisa pensar sobre configurações básicas, conforme já praticado no cenário anterior.

#### <a name="resource-group-and-virtual-machine-naming"></a>Nomeação de grupos de recursos e máquinas virtuais

Todos os nomes de grupos de recursos devem ser exclusivos. Desenvolva seu próprio esquema de nomenclatura de seus recursos, como `<rg-name`>-sufixo.

O nome da máquina virtual deve ser exclusivo dentro do grupo de recursos.

#### <a name="set-up-network-for-communication-between-the-different-vms"></a>Instalação de rede para comunicação entre as diferentes VMs

![Conjunto de VMs em uma Rede Virtual do Azure][planning-guide-figure-1900]

Para evitar conflitos de nome com clones das mesmas estruturas de treinamento/demonstração, você precisa criar uma Rede Virtual do Azure para cada estrutura. A resolução de nomes DNS será fornecida pelo Azure, ou você pode configurar seu próprio servidor DNS fora do Azure (não será mais discutido aqui). Neste cenário, não configuramos nosso próprio DNS. A comunicação por meio de nomes do host será habilitada para todas as máquinas virtuais dentro de uma Rede Virtual do Azure.

Os motivos para separar estruturas de treinamento ou demonstração por redes virtuais, e não apenas pelos grupos de recursos, podem ser:

* A estrutura da SAP, como foi configurada, precisa de sua própria AD/OpenLDAP, e um servidor de domínio precisa fazer parte de cada uma das estruturas.
* A estrutura da SAP, como foi configurada, tem componentes que precisam trabalhar com endereços IP fixos.

Mais detalhes sobre Redes Virtuais do Azure e como defini-las podem ser encontrados [neste artigo][virtual-networks-create-vnet-arm-pportal].

## <a name="deploying-sap-vms-with-corporate-network-connectivity-cross-premises"></a>Implantando VMs SAP com conectividade de rede corporativa (entre instalações)

Você executa uma estrutura da SAP e deseja dividir a implantação entre bare-metal para servidores high-end DBMS, ambientes virtualizados locais para camadas de aplicativo, além de IaaS do Azure e sistemas SAP menores, de duas camadas, configurados. A hipótese fundamental é que os sistemas SAP em uma estrutura da SAP precisem se comunicar entre si e com muitos outros componentes de software implantados na empresa, independentemente da forma de implantação. Não deve haver tampouco nenhuma diferença introduzida pela forma de implantação para o usuário final conectando-se à GUI da SAP ou outras interfaces. Essas condições só podem ser atendidas quando temos o Active Directory/OpenLDAP e serviços DNS locais estendidos aos sistemas do Azure por meio de conectividade de site a site/multissite ou conexões privadas, como a Azure ExpressRoute.



### <a name="scenario-of-an-sap-landscape"></a>Cenário de uma estrutura SAP

O cenário híbrido ou entre instalações pode ser descrito como nos gráficos abaixo:

![Conexão site a site entre ativos locais e no Azure][planning-guide-figure-2100]

O requisito mínimo é o uso de protocolos de comunicação segura, como SSL/TLS, para acesso via navegador ou conexões VPN para acesso ao sistema para os serviços do Azure. A suposição é que as empresas administram a conexão VPN entre sua rede corporativa e o Azure de modo diferente. Algumas empresas podem, cegamente, abrir todas as portas. Outras empresas podem querer ser precisas em relação às portas que precisam abrir, etc.

As portas de comunicação SAP típicas estão listadas na tabela a seguir. Basicamente, é suficiente abrir a porta do gateway da SAP.

<!-- sapms is prefix of a SAP service name and not a spelling error -->

| Serviço | Número da Porta | Exemplo `<nn`> = 01 | Intervalo Padrão (Mín-Máx) | Comentário |
| --- | --- | --- | --- | --- |
| Dispatcher |sapdp`<nn>` confira * |3201 |3200 – 3299 |Dispatcher SAP, usado pela GUI da SAP para Windows e Java |
| Servidor de mensagens |sapms`<sid`> confira ** |3600 |número livre de sapms`<anySID`> |sid = ID do sistema SAP |
| Gateway |sapgw`<nn`> confira * |3301 |livre |Gateway SAP, usado para comunicação CPIC e RFC |
| Roteador SAP |sapdp99 |3299 |livre |Apenas os nomes de serviço CI (instância central) podem ser reatribuídos em /etc/services, para um valor arbitrário, após a instalação. |

*) nn = Número da Instância SAP

**) sid = ID do sistema SAP

Encontre informações mais detalhadas sobre as portas necessárias para os diferentes produtos ou serviços de produtos SAP aqui: <https://scn.sap.com/docs/DOC-17124>.
Com este documento, você deve ser capaz de abrir portas dedicadas no dispositivo VPN necessárias para cenários e produtos específicos da SAP.

Outras medidas de segurança ao implantar VMs em um cenário como esse poderiam ser criar um [Grupo de Segurança de Rede][virtual-networks-nsg] para definir as regras de acesso.



#### <a name="printing-on-a-local-network-printer-from-sap-instance-in-azure"></a>Imprimindo em uma impressora de rede local da instância SAP no Azure

##### <a name="printing-over-tcpip-in-cross-premises-scenario"></a>Imprimindo via TCP/IP no cenário entre instalações

Configurar suas impressoras de rede com base em TCP/IP locais em uma VM do Azure é, no geral, igual ao que ocorre em sua rede corporativa, pressupondo que você tenha um túnel VPN site a site ou conexão de ExpressRoute estabelecida.

---
> ![Logotipo do Windows.][Logo_Windows] Windows
>
> Para fazer isso:
>
> * Algumas impressoras de rede vêm com um assistente de configuração que torna mais fácil configurar sua impressora em uma VM do Azure. Se nenhum software assistente foi distribuído com a impressora, o modo "manual" de configurá-la é criar uma nova porta de impressora TCP/IP.
> * Abra o Painel de Controle -> Dispositivos e Impressoras -> Adicionar uma impressora
> * Escolha Adicionar uma impressora usando um endereço TCP/IP ou nome do host
> * Digite o endereço IP da impressora
> * A porta de impressora padrão é 9100
> * Se necessário, instale manualmente o driver de impressora apropriado.
>
> ![Logotipo do Linux.][Logo_Linux] Linux
>
> * assim como para o Windows, apenas siga o procedimento padrão para instalar uma impressora de rede
> * basta seguir os guias públicos do Linux para [SUSE](https://www.suse.com/documentation/sles-12/book_sle_deployment/data/sec_y2_hw_print.html) ou [Red Hat e Oracle Linux](https://access.redhat.com/documentation/red_hat_enterprise_linux/6/html/deployment_guide/sec-printer_configuration) sobre como adicionar uma impressora.
>
>

---
![Impressão em rede][planning-guide-figure-2200]

##### <a name="host-based-printer-over-smb-shared-printer-in-cross-premises-scenario"></a>Impressora baseada em host por SMB (impressora compartilhada) em cenário entre instalações

Impressoras baseadas em host não são compatíveis com rede por design. Mas uma impressora baseada em host pode ser compartilhada entre computadores em uma rede, desde que a impressora esteja conectada a um computador ligado. Conecte sua rede corporativa via site a site ou ExpressRoute e compartilhe a impressora local. O protocolo SMB usa NetBIOS em vez de DNS como serviço de nomes. O nome do host NetBIOS pode ser diferente do nome do host DNS. O caso padrão é com o nome do host NetBIOS e o nome do host DNS sendo idênticos. O domínio DNS não faz sentido no namespace do NetBIOS. Da mesma forma, o nome do host DNS totalmente qualificado consistindo no nome do host DNS e no domínio DNS não deve ser usado no namespace do NetBIOS.

O compartilhamento de impressora é identificado por um nome exclusivo na rede:

* Nome do host SMB (sempre necessário).
* Nome do host do compartilhamento (sempre necessário).
* Nome do domínio se o compartilhamento de impressora não estiver no mesmo domínio que o sistema SAP.
* Além disso, um nome de usuário e uma senha podem ser necessárias para acessar o compartilhamento de impressora.

Como fazer:

---
> ![Logotipo do Windows.][Logo_Windows] Windows
>
> Compartilhe a impressora local.
> Na VM do Azure, abra o Windows Explorer e digite o nome do compartilhamento da impressora.
> Um assistente de instalação de impressora vai guiá-lo pelo processo de instalação.
>
> ![Logotipo do Linux.][Logo_Linux] Linux
>
> Aqui estão alguns exemplos de documentação sobre configuração de impressoras de rede no Linux ou inclusão de um capítulo sobre impressão no Linux. Isso funcionará da mesma forma em uma VM Linux do Azure, desde que a VM seja parte de um VPN:
>
> * SLES <https://en.opensuse.org/SDB:Printing_via_SMB_(Samba)_Share_or_Windows_Share>
> * RHEL ou Oracle Linux <https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html-single/system_administrators_guide/index#sec-Starting_Print_Settings_Config>
>
>

---
##### <a name="usb-printer-printer-forwarding"></a>Impressora USB (encaminhamento de impressora)

No Azure, a capacidade dos Serviços de Área de Trabalho Remota de fornecer aos usuários acesso a seus dispositivos de impressora local em uma sessão remota não está disponível.

---
> ![Logotipo do Windows.][Logo_Windows] Windows
>
> Encontre mais detalhes sobre a impressão com o Windows aqui: <https://technet.microsoft.com/library/jj590748.aspx>.
>
>

---
#### <a name="integration-of-sap-azure-systems-into-correction-and-transport-system-tms-in-cross-premises"></a>Integração de sistemas SAP do Azure ao Sistema de Correção e Transporte (TMS) para cenáros entre instalações

O Sistema de Alteração e Transporte SAP (TMS) deve ser configurado para exportar e importar solicitações de transporte entre sistemas na estrutura. Suponhamos que as instâncias de desenvolvimento de um sistema SAP (DES) estejam localizadas no Azure enquanto a garantia de qualidade e os sistemas de produção (PRD) sejam locais. Além disso, pressupomos que exista um diretório de transporte central.

##### <a name="configuring-the-transport-domain"></a>Configurando o domínio de transporte

Configure seu domínio de transporte no sistema designado por você como o controlador de domínio de transporte, conforme descrito em [Configurando o controlador de domínio de transporte](https://help.sap.com/erp2005_ehp_04/helpdata/en/44/b4a0b47acc11d1899e0000e829fbbd/content.htm). Um TMSADM de usuário do sistema será criado e o destino RFC necessário será gerado. Você pode verificar essas conexões RFC usando a transação SM59. A resolução do nome do host deve estar habilitada em seu domínio de transporte.

Como fazer:

* Em nosso cenário, decidimos que o sistema QAS local será o controlador de domínio CTS. Chame a transação STMS. A caixa de diálogo TMS é exibida. Uma caixa de diálogo Configurar Domínio de Transporte é exibida. (Essa caixa de diálogo só aparece se você ainda não tiver configurado um domínio de transporte.)
* Verifique se o TMSADM do usuário criado automaticamente está autorizado (SM59 -> Conexão ABAP -> TMSADM@E61.DOMAIN_E61 -> Detalhes -> Utilitários(M) -> Teste de Autorização). A tela inicial da transação STMS deve mostrar que esse sistema SAP está funcionando agora como o controlador do domínio de transporte, conforme mostrado aqui:

![Tela inicial da transação STMS no controlador de domínio][planning-guide-figure-2300]

#### <a name="including-sap-systems-in-the-transport-domain"></a>Incluindo sistemas SAP no domínio de transporte

A sequência de inclusão de um sistema SAP em um domínio de transporte será semelhante ao seguinte:

* No sistema de desenvolvimento no Azure, vá para o sistema de transporte (Cliente 000) e chame a transação STMS. Escolha Outra Configuração na caixa de diálogo e continue com Incluir o Sistema no Domínio. Especifique o controlador de domínio como o host de destino ([Incluindo sistemas SAP no domínio de transporte](https://help.sap.com/erp2005_ehp_04/helpdata/en/44/b4a0c17acc11d1899e0000e829fbbd/content.htm?frameset=/en/44/b4a0b47acc11d1899e0000e829fbbd/frameset.htm)). O sistema está agora aguardando para ser incluído no domínio de transporte.
* Por motivos de segurança, você precisa, em seguida, voltar para o controlador de domínio para confirmar a solicitação. Escolha Visão Geral do Sistema e Aprovação do sistema em espera. Em seguida, confirme o prompt e a configuração será distribuída.

Esse sistema SAP agora contém as informações necessárias sobre todos os outros sistemas SAP no domínio de transporte. Ao mesmo tempo, os dados de endereço do novo sistema SAP são enviados a todos os outros sistemas SAP e o sistema SAP é inserido no perfil de transporte do programa de controle de transporte. Verifique se os RFCs e o acesso ao diretório de transporte do domínio funcionam.

Continue com a configuração do seu sistema de transporte normalmente, conforme descrito na documentação [Sistema de Alteração e Transporte](https://help.sap.com/saphelp_nw70ehp3/helpdata/en/48/c4300fca5d581ce10000000a42189c/content.htm?frameset=/en/44/b4a0b47acc11d1899e0000e829fbbd/frameset.htm).

Como fazer:

* Certificar-se de que o STMS local está configurado corretamente.
* Certificar-se de que o nome do host do controlador de domínio de transporte pode ser resolvido pela máquina virtual no Azure e vice-versa.
* Chamar a transação STMS -> Outras Configurações -> Incluir Sistema no Domínio.
* Confirmar a conexão no sistema TMS local.
* Configurar camadas, grupos e rotas de transporte como de costume.

Em cenários entre instalações conectados site a site, a latência entre locais e o Azure ainda pode ser significativa. Se seguirmos a sequência de transportar objetos através de sistemas de desenvolvimento e teste para produção ou pensarmos sobre como aplicar os transportes ou pacotes de suporte aos diferentes sistemas, perceberemos que, dependendo do local do diretório de transporte central, alguns dos sistemas encontrarão alta latência ao ler ou gravar dados no diretório de transporte central. A situação é semelhante a configurações de estrutura da SAP em que os diferentes sistemas estão distribuídos por diferentes data centers com distância significativa entre os data centers.

Para contornar essa latência e fazer com que os sistemas trabalhem rapidamente na leitura ou gravação de ou para o diretório de transporte, você pode configurar dois domínios de transporte STMS (um com os sistemas locais e o outro com os sistemas no Azure) e então vincular os domínios de transporte. Confira esta documentação, que explica os princípios por trás deste conceito no SAP TMS: <https://help.sap.com/saphelp_me60/helpdata/en/c4/6045377b52253de10000009b38f889/content.htm?frameset=/en/57/38dd924eb711d182bf0000e829fbfe/frameset.htm> .

Como fazer:

* Configurar um domínio de transporte em cada local (local e Azure) usando o STMS de transação <https://help.sap.com/saphelp_nw70ehp3/helpdata/en/44/b4a0b47acc11d1899e0000e829fbbd/content.htm>
* Vincular os domínios com um link de domínio e confirmar o vínculo entre os dois domínios.
  <https://help.sap.com/saphelp_nw73ehp1/helpdata/en/a3/139838280c4f18e10000009b38f8cf/content.htm>
* Distribuir a configuração para o sistema vinculado.

#### <a name="rfc-traffic-between-sap-instances-located-in-azure-and-on-premises-cross-premises"></a>Tráfego RFC entre instâncias SAP localizadas no Azure e localmente (entre instalações)

O tráfego RFC entre os sistemas locais e que estão no Azure precisa funcionar. Para configurar uma conexão, chame a transação SM59 em um sistema de origem em que você precisa definir uma conexão RFC com o sistema de destino. A configuração é semelhante à configuração padrão de uma conexão RFC.

Supomos que no cenário entre instalações, as VMs executando sistemas SAP que precisarem se comunicar uns com os outros estão no mesmo domínio. Portanto, a configuração de uma conexão RFC entre os sistemas SAP não difere das etapas de configuração e entradas em cenários locais.

#### <a name="accessing-local-fileshares-from-sap-instances-located-in-azure-or-vice-versa"></a>Acessar compartilhamentos de arquivos locais de instâncias SAP localizadas no Azure ou vice-versa

As instâncias SAP localizadas no Azure precisam acessar compartilhamentos de arquivos que estão nas instalações corporativas. Além disso, as instâncias SAP locais precisam acessar compartilhamentos de arquivos que estão localizados no Azure. Para habilitar os compartilhamentos de arquivos, você deve configurar as permissões e opções de compartilhamento no sistema local. Certifique-se de abrir as portas na conexão VPN ou ExpressRoute entre o Azure e seu datacenter.

## <a name="supportability"></a>Suporte

### <a name="azure-extension-for-sap"></a><a name="6f0a47f3-a289-4090-a053-2521618a28c3"></a>Extensão para SAP do Azure

Para alimentar alguma parte das informações de infraestrutura do Azure de sistemas SAP críticos para as instâncias do agente de host do SAP, instaladas em VMs, uma extensão do Azure (VM) para SAP precisa ser instalada nas VMs implantadas. Já que as demandas do SAP eram específicas para aplicativos SAP, a Microsoft decidiu não implementar genericamente a funcionalidade necessária no Azure, mas sim deixar para os clientes a tarefa de implantar as configurações e a extensão de VM necessárias em suas máquinas virtuais em execução no Azure. No entanto, o gerenciamento de ciclo de vida e a implantação da Extensão de VM do Azure para SAP serão automatizados principalmente pelo Azure.

#### <a name="solution-design"></a>Design da solução

A solução desenvolvida para habilitar a obtenção das informações necessárias pelo Agente do Host do SAP baseia-se na arquitetura do Agente de VM do Azure e da estrutura de extensão. A ideia do Agente de VM e do framework de extensão é permitir a instalação de aplicativos de software disponíveis na Galeria de extensão de VM do Azure em uma VM. A ideia do princípio por trás desse conceito é permitir (em casos como a extensão do Azure para SAP), a implantação de uma funcionalidade especial em uma VM e a configuração desse software, no momento da implantação.

O 'Agente de VM do Azure' que permite a manipulação de extensões de VM do Azure específicas dentro da VM é injetado em VMs do Windows por padrão na criação de VMs no Portal do Azure. No caso do SUSE ou Red Hat ou Oracle Linux, o agente de VM já é parte da imagem do Azure Marketplace. Caso alguém carregasse uma VM Linux do local para o Azure, o agente de VM deveria ser instalado manualmente.

Os blocos de construção básicos da solução para fornecer informações de infraestrutura do Azure ao agente de host do SAP no Azure têm a seguinte aparência:

![Componentes de extensão do Microsoft Azure][planning-guide-figure-2400]

Conforme mostrado no diagrama de bloco acima, uma parte da solução é hospedada na Imagem de VM do Azure e na Galeria de extensões do Azure, que é um repositório global replicado gerenciado pelo Azure Operations. É responsabilidade da equipe de MS/SAP conjunta trabalhar para que a implementação da SAP no Azure funcione com o Azure Operations para publicar novas versões da Extensão para SAP do Azure.

Quando você implanta uma nova VM do Windows, o Agente de VM do Azure é adicionado automaticamente à VM. A função desse agente é coordenar o carregamento e a configuração das extensões do Azure das VMs. Para VMs Linux, o Agente de VM do Azure já é parte da imagem do SO do Azure Marketplace.

No entanto, há uma etapa que ainda precisa ser executada pelo cliente. Esta é a habilitação e configuração de coleta de desempenho. O processo relacionado à configuração é automatizado por um script do PowerShell ou comando da CLI. O script do PowerShell pode ser baixado no Microsoft Azure Script Center, conforme descrito no [Guia de implantação][deployment-guide].

A arquitetura geral da extensão do Azure para SAP é semelhante a:

![Extensão para SAP do Azure ][planning-guide-figure-2500]

**Para obter instruções exatas e etapas detalhadas de como usar esses cmdlets do PowerShell ou o comando da CLI durante as implantações, siga as instruções fornecidas no [Guia de implantação][deployment-guide].**

### <a name="integration-of-azure-located-sap-instance-into-saprouter"></a>Integração de instância SAP localizada no Azure ao SAProuter

Instâncias SAP em execução no Azure precisam ser acessíveis também do SAProuter.

![Conexão de rede de roteador SAP][planning-guide-figure-2600]

Um SAProuter habilitará a comunicação TCP/IP entre os sistemas participantes se não houver conexão direta por IP. Isso tem a vantagem de que nenhuma conexão de ponta a ponta entre os parceiros de comunicação é necessária no nível da rede. O SAProuter está escutando na porta 3299, por padrão.
Para se conectar a instâncias SAP por meio de um SAProuter, você precisa fornecer o nome do host e a cadeia de caracteres do SAProuter ao realizar qualquer tentativa de conexão.

## <a name="sap-netweaver-as-java"></a>Servidor de Aplicativos para Java do SAP NetWeaver

Até aqui, o foco do documento tem sido o SAP NetWeaver em geral ou a pilha ABAP do SAP NetWeaver. Nesta pequena seção, são listadas as considerações específicas para a pilha Java da SAP. Um dos mais importantes aplicativos baseados exclusivamente em Java do SAP NetWeaver é o SAP Enterprise Portal. Outros aplicativos com base em SAP NetWeaver, como o SAP PI e o SAP Solution Manager usam tanto o ABAP do SAP NetWeaver quanto pilhas Java. Portanto, é certamente necessário considerar também aspectos específicos relacionados à pilha Java do SAP NetWeaver.

### <a name="sap-enterprise-portal"></a>SAP Enterprise Portal

A configuração de um Portal SAP em uma Máquina Virtual do Azure não será diferente de uma instalação local se você estiver implantando em cenários entre instalações. Como o DNS é feito por local, as configurações de porta das instâncias individuais podem ser feitas do mesmo modo que são configuradas localmente. As recomendações e as restrições descritas neste documento até este ponto se aplicam a um aplicativo como o SAP Enterprise Portal ou a pilha Java do SAP NetWeaver em geral.

![Portal SAP exposto][planning-guide-figure-2700]

Um cenário de implantação especial por alguns clientes é a exposição direta do SAP Enterprise Portal à Internet enquanto o host de máquina virtual está conectado à rede da empresa por meio de túnel VPN site a site ou ExpressRoute. Para esse cenário, você precisa certificar-se de que portas específicas estejam abertas e não estejam bloqueadas por um grupo de segurança de rede ou firewall.

O URI inicial do portal é http(s):`<Portalserver`>:5XX00/irj, em que a porta é formada conforme documentado pelo SAP em <https://help.sap.com/saphelp_nw70ehp1/helpdata/de/a2/f9d7fed2adc340ab462ae159d19509/frameset.htm>.

![Configuração de ponto de extremidade][planning-guide-figure-2800]

Se você quiser personalizar a URL e/ou as portas do seu SAP Enterprise Portal, consulte esta documentação:

* [Alterar URL do portal](https://wiki.scn.sap.com/wiki/display/EP/Change+Portal+URL)
* [Alterar os Números de porta padrão, Números de porta do portal](https://wiki.scn.sap.com/wiki/display/NWTech/Change+Default++port+numbers%2C+Portal+port+numbers)

## <a name="high-availability-ha-and-disaster-recovery-dr-for-sap-netweaver-running-on-azure-virtual-machines"></a>HA (alta disponibilidade) e DR (recuperação de desastre) para SAP NetWeaver em execução em máquinas virtuais do Azure

### <a name="definition-of-terminologies"></a>Definição de terminologias

O Termo **alta disponibilidade (HA)** geralmente está relacionado a um conjunto de tecnologias que minimiza as interrupções da TI, proporcionando a continuidade dos negócios dos serviços de TI por meio de componentes redundantes e tolerantes a falhas ou protegidos contra failover no **mesmo** data center. Em nosso caso, dentro de uma Região do Azure.

**A DR (recuperação de desastre)** também está voltada para minimizar as interrupções dos serviços de TI e a recuperação de dados, mas em **diferentes** data centers, que estão normalmente localizados a centenas de quilômetros de distância. Em nosso caso, geralmente entre diferentes Regiões do Azure na mesma região geopolítica ou como estabelecidos por você, como cliente.

### <a name="overview-of-high-availability"></a>Visão Geral de Alta Disponibilidade

É possível separar a discussão sobre a alta disponibilidade da SAP no Azure em duas partes:

* **Alta disponibilidade da infraestrutura do Azure**, por exemplo, alta disponibilidade de computação (VMs), rede, armazenamento etc., e seus benefícios para aumentar a disponibilidade do aplicativo SAP.
* **Alta disponibilidade de aplicativos SAP**, por exemplo, alta disponibilidade de componentes de software SAP:
  * Servidores de aplicativos SAP
  * Instância ASCS/SCS SAP
  * servidor do BD

e como ele pode ser combinado com alta disponibilidade de infraestrutura do Azure.

Alta disponibilidade da SAP no Azure tem algumas diferenças em comparação com alta disponibilidade da SAP em um ambiente físico ou virtual local. O seguinte documento da SAP descreve as configurações padrão de Alta Disponibilidade do SAP nos ambientes virtualizados no Windows: <https://scn.sap.com/docs/DOC-44415>. Há não configuração de HA da SAP integrada com sapinst, para Linux, da mesma maneira que existe para o Windows. Em relação à HA local do SAP para Linux, encontre mais informações aqui: <https://scn.sap.com/docs/DOC-8541>.

### <a name="azure-infrastructure-high-availability"></a>Alta disponibilidade de infraestrutura do Azure

Atualmente, não há um SLA de 99,9% de VM individual. Para ter uma ideia da disponibilidade de uma VM individual, você pode criar o produto dos diferentes SLAs do Azure disponíveis: <https://azure.microsoft.com/support/legal/sla/>.

A base para o cálculo é 30 dias por mês, ou 43.200 minutos. Portanto, 0,05% de tempo de inatividade corresponde a 21,6 minutos. Conforme ocorre normalmente, a disponibilidade dos serviços diferentes se multiplicará da seguinte maneira:

(Serviço de disponibilidade nº 1/100) * (Serviço de disponibilidade nº 2/100) * (Serviço de disponibilidade nº 3/100)

Assim como:

(99,95/100) * (99,9/100) * (99,9/100) = 0,9975 ou uma disponibilidade geral de 99,75%.

#### <a name="virtual-machine-vm-high-availability"></a>Alta disponibilidade de VM (máquina virtual)

Há dois tipos de eventos de plataforma do Azure que podem afetar a disponibilidade das máquinas virtuais: manutenção planejada e manutenção não planejada.

* Eventos de manutenção planejada são atualizações periódicas feitas pela Microsoft na plataforma subjacente do Azure para melhorara a confiabilidade, o desempenho e a segurança geral da infraestrutura da plataforma na qual suas máquinas virtuais são executadas.
* Eventos de manutenção não planejada ocorrem quando o hardware ou a infraestrutura física subjacente à sua máquina virtual apresenta algum tipo de falha. Isso inclui falhas na rede local, falhas no disco local ou outras falhas no nível de rack. Quando tal falha é detectada, a plataforma do Azure migrará automaticamente sua máquina virtual do servidor físico não íntegro hospedando sua máquina virtual para um servidor físico íntegro. Esses eventos são raros, mas podem também reinicializar a sua máquina virtual.

Para obter mais detalhes, consulte [disponibilidade de máquinas virtuais do Windows no Azure](../../availability.md) e [disponibilidade de máquinas virtuais do Linux no Azure](../../availability.md).

#### <a name="azure-storage-redundancy"></a>Redundância de Armazenamento do Azure

Os dados da sua Conta de Armazenamento do Microsoft Azure sempre são replicados para garantir durabilidade e alta disponibilidade, cumprindo o SLA do Armazenamento do Azure mesmo diante de falhas transitórias de hardware.

Já que o armazenamento do Azure está mantendo três imagens dos dados por padrão, RAID5 ou RAID1 em vários discos do Azure não são necessárias.

Para obter mais detalhes, consulte [redundância de armazenamento do Azure](../../../storage/common/storage-redundancy.md).

#### <a name="utilizing-azure-infrastructure-vm-restart-to-achieve-higher-availability-of-sap-applications"></a>Utilizando a Reinicialização de VM da infraestrutura do Azure para atingir maior disponibilidade de aplicativos SAP

Se você decidir não usar as funcionalidades como Clustering de Failover do Windows Server (WSFC) ou Pacemaker Linux (atualmente compatível apenas com SLES 12 ou posterior), a Reinicialização de VM do Azure será usada para proteger o sistema SAP contra tempo de inatividade planejado e não planejado da infraestrutura de servidor físico do Azure e da plataforma subjacente do Azure em geral.

> [!NOTE]
> É importante mencionar que a Reinicialização de VM do Azure protege principalmente as VMs, NÃO os aplicativos. A Reinicialização de VM não oferece alta disponibilidade para aplicativos SAP, mas oferece um certo nível de disponibilidade da infraestrutura e, portanto, indiretamente, maior disponibilidade de sistemas SAP. Não há, tampouco, nenhum SLA para o tempo necessário para reiniciar uma VM após uma interrupção de host planejada ou não planejada. Portanto, esse método de alta disponibilidade não é adequado para componentes críticos de um sistema SAP como (A)SCS ou DBMS.
>
>

Outro elemento de infraestrutura importante para alta disponibilidade é o armazenamento. Por exemplo, o SLA do Armazenamento do Azure tem disponibilidade de 99,9%. Se alguém implantar todas as VMs com seus discos em uma única Conta de Armazenamento do Azure, a indisponibilidade potencial do Armazenamento do Azure causará indisponibilidade de todas as VMs localizadas na Conta de Armazenamento do Azure e também de todos os componentes SAP em execução dentro dessas VMs.

Em vez de colocar todas as VMs em uma única Conta de Armazenamento do Azure, você também pode contas de armazenamento dedicado para cada VM e, dessa forma, aumentar a disponibilidade geral da VM e do aplicativo SAP usando várias Contas de Armazenamento do Azure independentes.

Os Azure Managed disks são colocados automaticamente no domínio de falha da máquina virtual à qual eles estão conectados. Se você colocar duas máquinas virtuais em um conjunto de disponibilidade e usar Managed Disks, a plataforma cuidará da distribuição dos Managed Disks em diferentes Domínios de Falha. Se você planeja usar o Armazenamento Premium, também é altamente recomendável usar Managed Disks.

Uma arquitetura de exemplo de um sistema SAP NetWeaver que usa alta disponibilidade de infraestrutura e contas de armazenamento do Azure poderia ter esta aparência:

![Diagrama que mostra um sistema SAP NetWeaver que usa as contas de armazenamento e HA de infraestrutura do Azure.][planning-guide-figure-2900]

Uma arquitetura de exemplo de um sistema SAP NetWeaver que usa alta disponibilidade de infraestrutura e Managed Disks do Azure poderia ter esta aparência:

![Utilizando a alta disponibilidade da infraestrutura do Azure para atingir maior disponibilidade de aplicativos SAP][planning-guide-figure-2901]

Para componentes críticos da SAP, conseguimos o seguinte até agora:

* Alta disponibilidade de servidores de aplicativos (SA) SAP

  As instâncias do servidor de aplicativos SAP são componentes redundantes. Cada instância de SA SAP está implantada em uma VM própria, que está em execução em um domínio de falha e de atualização diferente do Azure (confira os capítulos [Domínios de falha][planning-guide-3.2.1] e [Domínios de atualização][planning-guide-3.2.2]). Isso é garantido pelo uso de conjuntos de disponibilidade do Azure (confira o capítulo [Conjuntos de Disponibilidade do Azure][planning-guide-3.2.3]). Potencial indisponibilidade planejada ou não planejada de um domínio de falha ou atualização do Azure causará indisponibilidade de um número restrito de VMs com suas instâncias SAP.

  Cada instância de SA SAP será colocada em sua própria conta de Armazenamento do Azure – a indisponibilidade potencial de uma Conta de Armazenamento do Azure causará indisponibilidade de apenas uma VM com a instância de SA SAP. No entanto, lembre-se de que há um limite de Contas de Armazenamento do Azure dentro de uma assinatura do Azure. Para assegurar o início automático da instância do (A)SCS após a reinicialização da VM, não se esqueça de definir o parâmetro de Inicialização automática no perfil de início de instância do (A)SCS descrito no capítulo [Usar a Inicialização automática para instâncias SAP][planning-guide-11.5].
  Leia também o capítulo [alta disponibilidade para servidores de aplicativos SAP][planning-guide-11.4.1] para obter mais detalhes.

  Mesmo que você use Managed Disks, esses discos também são armazenados em uma Conta de Armazenamento do Azure e podem não estar disponíveis no caso de uma interrupção de armazenamento.

* *Maior* disponibilidade de instância do (A)SCS SAP

  Aqui, utilizamos a Reinicialização de VM do Azure para proteger a VM com instância do (A)SCS SAP instalada. No caso de tempo de inatividade planejado ou não planejado de servidores do Azure, as VMs serão reiniciadas em outro servidor disponível. Como mencionado anteriormente, a reinicialização de VM do Azure protege principalmente as VMs e não os aplicativos, nesse caso, a instância (A) SCS. Por meio da Reinicialização de VM, alcançaremos indiretamente maior disponibilidade da instância do SAP (A)SCS. Para assegurar o início automático da instância do (A)SCS após a reinicialização da VM, não se esqueça de definir o parâmetro de Inicialização automática no perfil de início de instância do (A)SCS descrito no capítulo [Usar a inicialização automática para instâncias SAP][planning-guide-11.5]. Isso significa que a instância do (A)SCS como um SPOF (ponto único de falha) em execução em uma única VM será o fator determinante para a disponibilidade de toda a estrutura da SAP.

* *Maior* disponibilidade de servidor DBMS

  Aqui, de modo semelhante ao caso de uso de instância do SAP (A)SCS, utilizamos a Reinicialização de VM do Azure para proteger a VM com software do DBMS instalado e podemos obter maior disponibilidade de software DBMS por meio da Reinicialização de VM.
  DBMS em execução em uma única VM também é um SPOF, e é o fator determinante para a disponibilidade de toda a estrutura da SAP.

### <a name="sap-application-high-availability-on-azure-iaas"></a>Alta disponibilidade de aplicativo SAP no Azure IaaS

Para obter alta disponibilidade total no sistema SAP, é necessário proteger todos os componentes críticos do sistema SAP, por exemplo, de servidores de aplicativos SAP redundantes e componentes exclusivos (por exemplo, ponto único de falha), como a instância do (A)SCS SAP e DBMS.

#### <a name="high-availability-for-sap-application-servers"></a><a name="5d9d36f9-9058-435d-8367-5ad05f00de77"></a>Alta disponibilidade para servidores de aplicativos SAP

Para as instâncias de caixa de diálogo/servidores de aplicativos SAP, não é necessário pensar em uma solução de alta disponibilidade específica. A alta disponibilidade é obtida pela redundância e, portanto, pela existência de um número suficiente deles em máquinas virtuais diferentes. Eles devem ser todos colocados no mesmo conjunto de disponibilidade do Azure, para evitar que as VMs possam ser atualizadas ao mesmo tempo durante o tempo de inatividade da manutenção planejada. A funcionalidade básica baseada nos domínios de falha e de atualização diferentes em uma Unidade de Escala do Azure já foi introduzida no capítulo [Domínios de atualização][planning-guide-3.2.2]. Conjuntos de disponibilidade do Azure foram apresentados no capítulo [Conjuntos de disponibilidade do Azure][planning-guide-3.2.3] deste documento.

Não há um número infinito de domínios de falha e atualização que possam ser usados por um conjunto de disponibilidade do Azure em uma unidade de escala do Azure. Isso significa que, colocando um número de VMs em um conjunto de disponibilidade, mais cedo ou mais tarde, mais de uma VM termina no mesmo domínio de atualização ou falha.

Implantando algumas instâncias do servidor de aplicativos SAP em suas VMs dedicadas e supondo que temos cinco domínios de atualização, o quadro a seguir surge no final. O número máximo real de domínios de falha e atualização dentro de um conjunto de disponibilidade pode mudar no futuro:

![Alta disponibilidade dos servidores de aplicativos SAP no Azure][planning-guide-figure-3000]


#### <a name="high-availability-for-sap-central-services-on-azure"></a>Alta disponibilidade para serviços centrais do SAP no Azure

Para a arquitetura de alta disponibilidade do SAP Central Services no Azure, confira o artigo [Arquitetura e cenários de alta disponibilidade para SAP NetWeaver](./sap-high-availability-architecture-scenarios.md) como informações de entrada. O artigo aponta para descrições mais detalhadas para os sistemas operacionais específicos.

#### <a name="high-availability-for-the-sap-database-instance"></a>Alta disponibilidade para a instância do banco de dados SAP

A configuração típica de alta disponibilidade de DMBS SAP é baseada nas duas VMs DBMS nas quais a funcionalidade de alta disponibilidade de DBMS é usada para replicar dados da instância de DBMS ativa para a segunda VM em uma instância de DBMS passiva.

As funcionalidades de Recuperação de Desastre e Alta Disponibilidade para DBMS em geral, assim como um DBMS específico, são descritos no [Guia de implantação de DBMS][dbms-guide].

#### <a name="end-to-end-high-availability-for-the-complete-sap-system"></a>Alta disponibilidade de ponta a ponta para o sistema SAP completo

Aqui estão dois exemplos de uma arquitetura de alta disponibilidade SAP NetWeaver completa no Azure - um para Windows e outro para Linux.

Somente discos não gerenciados: Os conceitos, conforme explicado abaixo, talvez precisem ser um pouco comprometidos quando você implantar muitos sistemas SAP e o número de VMs implantadas estiver excedendo o limite máximo de contas de armazenamento por assinatura. Nesses casos, os VHDs de VMs devem ser combinados em uma conta de armazenamento. Normalmente você faria isso pela combinação de VHDs de VMs das camadas de aplicativo SAP de sistemas SAP diferentes.  Também combinamos diferentes VHDs de diferentes VMs DBMS de sistemas SAP diferentes em uma conta de armazenamento do Azure. Portanto, mantendo os limites de IOPS das Contas de Armazenamento do Azure em mente (<https://azure.microsoft.com/documentation/articles/storage-scalability-targets>)


##### <a name="windows-logologo_windows-ha-on-windows"></a>![Logotipo do Windows.][Logo_Windows] no Windows

![Diagrama que mostra a arquitetura de HA do aplicativo SAP NetWeaver com SQL Server no Azure IaaS.][planning-guide-figure-3200]

As seguintes construções do Azure são usadas para o sistema SAP NetWeaver, para minimizar o impacto por problemas de infraestrutura e a aplicação de patches ao host:

* O sistema completo é implantado no Azure (obrigatório-camada DBMS, instância (A) SCS e a camada de aplicativo completa precisa ser executada no mesmo local).
* O sistema completo é executado dentro de uma assinatura do Azure (obrigatório).
* O sistema completo é executado dentro de uma rede virtual do Azure (obrigatório).
* A separação das VMs de um sistema SAP em três conjuntos de disponibilidade é possível mesmo com todas as máquinas virtuais pertencendo à mesma rede virtual.
* Cada camada (por exemplo, DBMS, ASCS, servidores de aplicativos) deve usar um conjunto de disponibilidade dedicado.
* Todas as VMs que executam instâncias DBMS de um sistema SAP estão em um conjunto de disponibilidade. Presumimos que haja mais de uma VM executando instâncias de DBMS por sistema, já que recursos nativos de alta disponibilidade de DBMS são usados, como o SQL Server AlwaysOn ou o Oracle Data Guard.
* Todas as VMs que executam instâncias de DBMS usam sua própria conta de armazenamento. Arquivos de log e arquivos de dados de DBMS são replicados de uma conta de armazenamento para outra usando funções de alta disponibilidade de DBMS que sincronizam os dados. Indisponibilidade de uma conta de armazenamento causará indisponibilidade de um nó de cluster do Windows SQL, mas não do serviço do SQL Server inteiro.
* Todas as VMs que executam uma instância do (A)SCS de um sistema SAP estão em um conjunto de disponibilidade. Um Cluster de Failover do Windows Server (WSFC)é configurado no interior dessas VMs para proteger a instância do (A)SCS.
* Todas as VMs que executam instâncias do (A)SCS usam sua própria conta de armazenamento. Os arquivos de instância do (A)SCS e a pasta global do SAP são replicados de uma conta de armazenamento para outra usando a replicação do SIOS DataKeeper. Indisponibilidade de uma conta de armazenamento causará indisponibilidade de um nó de cluster do Windows (A)SCS, mas não do serviço do (A)SCS inteiro.
* TODAS as VMs que representam a camada do servidor de aplicativos SAP estão em um terceiro conjunto de disponibilidade.
* TODAS as VMs executando servidores de aplicativos SAP usam sua própria conta de armazenamento. A indisponibilidade de uma conta de armazenamento causará indisponibilidade de um servidor de aplicativos SAP, em que outros servidores de aplicativos SAP continuam em execução.

A figura a seguir ilustra o mesmo cenário usando Managed Disks.

![Arquitetura de alta disponibilidade de aplicativos SAP NetWeaver com o SQL Server no Azure IaaS][planning-guide-figure-3201]

##### <a name="linux-logologo_linux-ha-on-linux"></a>![Logotipo do Linux.][Logo_Linux] no Linux

A arquitetura para alta disponibilidade da SAP em Linux no Azure é basicamente a mesma que aquela para Windows, conforme descrito acima. Veja a Nota SAP [1928533] para obter uma lista de soluções de alta disponibilidade compatíveis.

### <a name="using-autostart-for-sap-instances"></a><a name="4e165b58-74ca-474f-a7f4-5e695a93204f"></a>Usando a inicialização automática para instâncias SAP

A SAP ofereceu a funcionalidade para iniciar as instâncias SAP imediatamente após a inicialização do SO na VM. As etapas exatas documentadas no artigo da Base de Dados de Conhecimento SAP [1909114]. No entanto, a SAP não recomenda mais o uso da configuração, porque não há nenhum controle na ordem de reinicializações de instâncias, supondo que mais de uma VM tenha sido afetada ou várias instâncias tenham sido executadas por VM. Supondo um cenário típico do Azure de uma instância do servidor de aplicativos SAP em uma VM e o caso de uma única VM eventualmente sendo reiniciada, a Inicialização Automática não é crítica e pode ser habilitada adicionando este parâmetro:

`Autostart = 1`

No perfil de início da instância de SAP ABAP e/ou instância de Java.

> [!NOTE]
> O parâmetro Inicialização automática também pode ter algumas desvantagens. Mais detalhadamente, o parâmetro aciona o início de uma instância de Java ou SAP ABAP quando o serviço do Windows/Linux relacionado da instância é iniciado. Esse é certamente o caso quando o sistema operacional é inicializado. No entanto, reinicializações de serviços SAP também são uma coisa comum para a funcionalidade de gerenciamento de ciclo de vida do Software SAP, como SUM ou outras atualizações. Essas funcionalidades não estão esperando em hipótese nenhuma que uma instância seja reiniciada automaticamente. Portanto, o parâmetro Inicialização automática deve ser desabilitado antes de executar essas tarefas. O parâmetro de Inicialização automática também não deve ser usado para instâncias do SAP que estão clusterizadas, como ASCS/SCS/CI.
>
>

Consulte informações adicionais sobre a inicialização automática para instâncias SAP aqui:

* [Iniciar/parar SAP ao iniciar/parar seu servidor Unix](https://scn.sap.com/community/unix/blog/2012/08/07/startstop-sap-along-with-your-unix-server-startstop)
* [Iniciando e parando agentes de gerenciamento do SAP NetWeaver](https://help.sap.com/saphelp_nwpi711/helpdata/en/49/9a15525b20423ee10000000a421938/content.htm)
* [Como habilitar automaticamente a inicialização automática do banco de dados HANA](http://sapbasisinfo.com/blog/2016/08/15/enabling-autostart-of-sap-hana-database-on-server-boot-situation/)

### <a name="larger-3-tier-sap-systems"></a>Sistemas SAP maiores de 3 camadas
Aspectos de alta disponibilidade de configurações SAP de 3 camadas já foram discutidos nas seções anteriores. Mas e quanto a sistemas em que os requisitos do servidor DBMS são grandes demais para que ele fosse localizado no Azure, mas nos quais a camada do aplicativo SAP poderia ser implantada no Azure?

#### <a name="location-of-3-tier-sap-configurations"></a>Localização de configurações SAP de 3 camadas
Não há suporte para dividir a camada de aplicativo em si ou o aplicativo e a camada DBMS entre o local e o Azure. Um sistema SAP é completamente implantado localmente OU no Azure. Também não há suporte para executar alguns dos servidores de aplicativos localmente e outros no Azure. Esse é o ponto de partida da discussão. Também não damos suporte à implantação dos componentes do DBMS de um sistema SAP e da camada de servidor de aplicativos SAP em duas Regiões do Azure diferentes. Por exemplo, o DBMS no Oeste dos EUA e camada de aplicativo SAP nos EUA Central. O motivo para não dar suporte a essas configurações é a sensibilidade de latência da arquitetura do SAP NetWeaver.

No entanto, ao longo do ano passado, parceiros de data center desenvolveram colocalizações para Regiões do Azure. Essas colocalizações geralmente são próximas dos data centers físicos em uma Região do Azure. A curta distância e a conexão dos ativos na colocalização por meio do ExpressRoute no Azure podem resultar em uma latência com menos de 2 milissegundos. Nesses casos, é possível localizar a camada de DBMS (incluindo o armazenamento SAN/NAS) em uma dessas colocalizações e a camada de aplicativo SAP no Azure. [HANA em Instâncias Grandes](./hana-overview-architecture.md).

### <a name="offline-backup-of-sap-systems"></a>Backup offline de sistemas SAP
Dependendo da configuração SAP escolhida (2 ou 3 camadas), pode haver necessidade de backup. O conteúdo da própria VM, além de ter um backup do banco de dados. Os backups relacionados ao DBMS devem ser feitos com métodos de banco de dados. Uma descrição detalhada dos diferentes bancos de dados pode ser encontrada no [Guia de DBMS][dbms-guide]. Por outro lado, os dados da SAP podem fazer backup de maneira offline (incluindo o conteúdo do banco de dados) conforme descrito nesta seção, ou online, conforme descrito na próxima seção.

O backup offline basicamente exigiria um desligamento da VM por meio do Portal do Azure e uma cópia do disco da VM base, além de todos os discos anexados à VM. Isso preservaria uma imagem da VM e seus discos associados em um ponto no tempo. É recomendável copiar os backups para outra Conta de Armazenamento do Azure. Portanto, o procedimento descrito no capítulo [Copiar discos entre Contas de Armazenamento do Azure][planning-guide-5.4.2] deste documento seria aplicável.


Uma restauração de estado consiste em excluir a VM base, bem como os discos originais da VM base e os discos montados, copiando os discos salvos de volta à Conta de Armazenamento original ou grupo de recursos para managed disks e, em seguida, reimplantando o sistema.
Este artigo mostra um exemplo de como criar um script desse processo no PowerShell: <https://www.westerndevs.com/_/azure-snapshots/>

Certifique-se de instalar uma nova licença do SAP, pois a restauração de um backup de VM, conforme descrito acima, cria uma nova chave de hardware.

### <a name="online-backup-of-an-sap-system"></a>Backup online de um sistema SAP

O backup do DBMS é executado com métodos específicos de DBMS, conforme descrito no [Guia de DBMS][dbms-guide].

O backup de outras VMs no sistema SAP pode ser feito usando a funcionalidade de Backup de máquinas virtuais do Azure. O Backup de Máquina Virtual do Azure é um método padrão para fazer backup de uma VM completa no Azure. O Backup do Azure armazena os backups no Azure e permite uma restauração de uma VM novamente.

> [!NOTE]
> Desde dezembro de 2015 o uso do Backup da VM NÃO mantém a ID exclusiva da VM que é usada para licenciamento SAP. Isso significa que uma restauração de um backup de VM exige a instalação de uma nova chave de licença da SAP, já que a VM restaurada é considerada como uma nova VM e não uma substituta da VM antiga que foi salva.
>
> ![Logotipo do Windows.][Logo_Windows] Windows
>
> Teoricamente, as VMs que executam bancos de dados podem ser copiadas em backup de maneira consistente também se os sistemas DBMS derem suporte ao VSS (Serviço de Cópias de Sombra de Volume <https://msdn.microsoft.com/library/windows/desktop/bb968832(v=vs.85).aspx>) do Windows da mesma forma que o SQL Server, por exemplo.
> No entanto, lembre-se de que com base nos backups de VM do Azure, as restaurações pontuais não são possíveis. Portanto, a recomendação é executar backups de bancos de dados com a funcionalidade do DBMS em vez de depender do backup de VM do Azure.
>
> Para se familiarizar com o Backup de Máquinas Virtuais do Azure, comece aqui: <https://docs.microsoft.com/azure/backup/backup-azure-vms>.
>
> Outras possibilidades são usar uma combinação do Microsoft Data Protection Manager instalado em uma VM do Azure e o Backup do Azure para backup/restauração de bancos de dados. Encontre mais informações aqui: <https://docs.microsoft.com/azure/backup/backup-azure-dpm-introduction>.
>
> ![Logotipo do Linux.][Logo_Linux] Linux
>
> Não há nenhum equivalente ao VSS do Windows em Linux. Portanto, apenas backups consistentes com arquivo são possíveis, enquanto os consistentes com aplicativo não são. O backup do DBMS SAP deve ser feito usando a funcionalidade DBMS. O sistema de arquivos que inclui os dados relacionados ao SAP pode ser salvo, por exemplo, usando o tar, conforme descrito aqui: <https://help.sap.com/saphelp_nw70ehp2/helpdata/en/d3/c0da3ccbb04d35b186041ba6ac301f/content.htm>
>
>

### <a name="azure-as-dr-site-for-production-sap-landscapes"></a>Azure como local de recuperação de desastre para estruturas da SAP de produção

Desde meados de 2014, extensões para vários componentes do Hyper-V, System Center e Azure permitem o uso do Azure como local de DR para VMs executadas localmente com base no Hyper-V.

Um blog que fornece detalhes de como implantar essa solução é documentado aqui: <https://docs.microsoft.com/archive/blogs/saponsqlserver/protecting-sap-solutions-with-azure-site-recovery>.

## <a name="summary-for-high-availability-for-sap-systems"></a>Resumo para alta disponibilidade para sistemas SAP

Os pontos principais de alta disponibilidade para sistemas SAP no Azure são:

* Neste momento, o único ponto de falha da SAP não pode ser protegido exatamente da mesma maneira como pode ser feito em implantações locais. O motivo é que os clusters desse disco compartilhado ainda não podem ser criados no Azure sem o uso de software de terceiros.
* Para a camada DBMS, você precisa usar a funcionalidade do DBMS que não depende da tecnologia de cluster de disco compartilhado. Os detalhes estão documentados no [Guia de DBMS][dbms-guide].
* Para minimizar o impacto de problemas nos domínios de falha na manutenção de host ou de infraestrutura do Azure, você deve usar conjuntos de disponibilidade do Azure:
  * É recomendável ter um conjunto de disponibilidade para a camada do aplicativo SAP.
  * É recomendável ter um conjunto de disponibilidade separado para a camada de DBMS SAP.
  * NÃO é recomendável aplicar o mesmo conjunto de disponibilidade para VMs de sistemas SAP diferentes.
  * É recomendável usar Managed Disks Premium.
* Para fins de Backup da camada do SAP DBMS, confira o [Guia do DBMS][dbms-guide].
* Fazer backup de instâncias de caixas de diálogo SAP não faz muito sentido, já que é normalmente mais rápido reimplantar instâncias de caixa de diálogo simples.
* Fazer backup da VM que contém o diretório global do sistema SAP e, com ela, todos os perfis das diferentes instâncias faz sentido e deve ser executado com o Backup do Windows ou, por exemplo, tar no Linux. Como há diferenças entre o Windows Server 2008 (R2) e o Windows Server 2012 (R2) que facilitam o backup usando versões mais recentes do Windows Server, é recomendável executar o Windows Server 2012 (R2) como sistema operacional convidado Windows.

## <a name="next-steps"></a>Próximas etapas
Leia os artigos:

- [Implantação de Máquinas Virtuais do Azure para SAP NetWeaver](./deployment-guide.md)
- [Considerações para Implantação do DBMS de Máquinas Virtuais do Azure para carga de trabalho do SAP](./dbms_guide_general.md)
- [Configurações de infraestrutura do SAP HANA e operações no Azure](/- azure/virtual-machines/workloads/sap/hana-vm-operations)
