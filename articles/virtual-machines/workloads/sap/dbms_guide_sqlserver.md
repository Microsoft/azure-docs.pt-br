---
title: Implantação do DBMS de Máquinas de Virtuais do SQL Server Azure para carga de trabalho do SAP | Microsoft Docs
description: Implantação do DBMS de Máquinas de Virtuais do SQL Server Azure para carga de trabalho do SAP
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/26/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0c12c75bd5c357613d55e04aed67c0cc901135e6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60835499"
---
# <a name="sql-server-azure-virtual-machines-dbms-deployment-for-sap-netweaver"></a>Implantação do DBMS de Máquinas de Virtuais do SQL Server Azure para NetWeaver do SAP

[767598]:https://launchpad.support.sap.com/#/notes/767598
[773830]:https://launchpad.support.sap.com/#/notes/773830
[826037]:https://launchpad.support.sap.com/#/notes/826037
[965908]:https://launchpad.support.sap.com/#/notes/965908
[1031096]:https://launchpad.support.sap.com/#/notes/1031096
[1114181]:https://launchpad.support.sap.com/#/notes/1114181
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
[2171857]:https://launchpad.support.sap.com/#/notes/2171857
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[azure-cli]:../../../cli-install-nodejs.md
[azure-portal]:https://portal.azure.com
[azure-ps]:/powershell/azureps-cmdlets-docs
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-subscription-service-limits]:../../../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md#subscription-limits

[dbms-guide]:dbms-guide_general.md 
[dbms-guide-2.1]:dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f 
[dbms-guide-2.2]:dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 
[dbms-guide-2.3]:dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 
[dbms-guide-2]:dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 
[dbms-guide-3]:dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 
[dbms-guide-5.5.1]:dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 
[dbms-guide-5.5.2]:dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b 
[dbms-guide-5.6]:dbms_guide_sqlserver.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 
[dbms-guide-5.8]:dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 
[dbms-guide-5]:dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 
[dbms-guide-8.4.1]:dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573 
[dbms-guide-8.4.2]:dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d 
[dbms-guide-8.4.3]:dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c 
[dbms-guide-8.4.4]:dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818 
[dbms-guide-900-sap-cache-server-on-premises]:dbms-guide.md#642f746c-e4d4-489d-bf63-73e80177a0a8
[dbms-guide-managed-disks]:dbms-guide.md#f42c6cb5-d563-484d-9667-b07ae51bce29

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

[msdn-set-azurermvmaemextension]:https://msdn.microsoft.com/library/azure/mt670598.aspx

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
[planning-guide-figure-2500]:media/virtual-machines-shared-sap-planning-guide/2500-vm-extension-details.png
[planning-guide-figure-2600]:media/virtual-machines-shared-sap-planning-guide/2600-sap-router-connection.png
[planning-guide-figure-2700]:media/virtual-machines-shared-sap-planning-guide/2700-exposed-sap-portal.png
[planning-guide-figure-2800]:media/virtual-machines-shared-sap-planning-guide/2800-endpoint-config.png
[planning-guide-figure-2900]:media/virtual-machines-shared-sap-planning-guide/2900-azure-ha-sap-ha.png
[planning-guide-figure-300]:media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png
[planning-guide-figure-3000]:media/virtual-machines-shared-sap-planning-guide/3000-sap-ha-on-azure.png
[planning-guide-figure-3200]:media/virtual-machines-shared-sap-planning-guide/3200-sap-ha-with-sql.png
[planning-guide-figure-400]:media/virtual-machines-shared-sap-planning-guide/400-vm-services.png
[planning-guide-figure-600]:media/virtual-machines-shared-sap-planning-guide/600-s2s-details.png
[planning-guide-figure-700]:media/virtual-machines-shared-sap-planning-guide/700-decision-tree-deploy-to-azure.png
[planning-guide-figure-800]:media/virtual-machines-shared-sap-planning-guide/800-portal-vm-overview.png
[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd 
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f 

[resource-group-authoring-templates]:../../../resource-group-authoring-templates.md
[resource-group-overview]:../../../azure-resource-manager/resource-group-overview.md
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
[storage-powershell-guide-full-copy-vhd]:../../../storage/common/storage-powershell-guide-full.md#how-to-copy-blobs-from-one-storage-container-to-another
[storage-premium-storage-preview-portal]:../../windows/disks-types.md
[storage-redundancy]:../../../storage/common/storage-redundancy.md
[storage-scalability-targets]:../../../storage/common/storage-scalability-targets.md
[storage-use-azcopy]:../../../storage/common/storage-use-azcopy.md
[template-201-vm-from-specialized-vhd]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd
[templates-101-simple-windows-vm]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image
[virtual-machines-linux-attach-disk-portal]:../../linux/attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../../../resource-manager-deployment-model.md
[virtual-machines-azurerm-versus-azuresm]:../../../resource-manager-deployment-model.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:../../virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:../../linux/cli-deploy-templates.md 
[virtual-machines-deploy-rmtemplates-powershell]:../../virtual-machines-windows-ps-manage.md 
[virtual-machines-linux-agent-user-guide]:../../linux/agent-user-guide.md
[virtual-machines-linux-agent-user-guide-command-line-options]:../../linux/agent-user-guide.md#command-line-options
[virtual-machines-linux-capture-image]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager-capture]:../../linux/capture-image.md#step-2-capture-the-vm
[virtual-machines-linux-configure-raid]:../../linux/configure-raid.md
[virtual-machines-linux-configure-lvm]:../../linux/configure-lvm.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]:../../virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]:../../linux/suse-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]:../../linux/redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]:../../linux/add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]:../../linux/add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk
[virtual-machines-linux-tutorial]:../../linux/quick-create-cli.md
[virtual-machines-linux-update-agent]:../../linux/update-agent.md
[virtual-machines-manage-availability-linux]:../../linux/manage-availability.md
[virtual-machines-manage-availability-windows]:../../windows/manage-availability.md
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
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/eresources/templates/sql-server-2014-alwayson-existing-vnet-and-ad/
[virtual-network-deploy-multinic-arm-cli]:../linux/multiple-nics.md
[virtual-network-deploy-multinic-arm-ps]:../windows/multiple-nics.md
[virtual-network-deploy-multinic-arm-template]:../../../virtual-network/template-samples.md
[virtual-networks-configure-vnet-to-vnet-connection]:../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md
[virtual-networks-create-vnet-arm-pportal]:../../../virtual-network/manage-virtual-network.md#create-a-virtual-network
[virtual-networks-manage-dns-in-vnet]:../../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md
[virtual-networks-multiple-nics]:../../../virtual-network/virtual-network-deploy-multinic-classic-ps.md
[virtual-networks-nsg]:../../../virtual-network/security-overview.md
[virtual-networks-reserved-private-ip]:../../../virtual-network/virtual-networks-static-private-ip-arm-ps.md
[virtual-networks-static-private-ip-arm-pportal]:../../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[virtual-networks-udr-overview]:../../../virtual-network/virtual-networks-udr-overview.md
[vpn-gateway-about-vpn-devices]:../../../vpn-gateway/vpn-gateway-about-vpn-devices.md
[vpn-gateway-create-site-to-site-rm-powershell]:../../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md
[vpn-gateway-cross-premises-options]:../../../vpn-gateway/vpn-gateway-plan-design.md
[vpn-gateway-site-to-site-create]:../../../vpn-gateway/vpn-gateway-site-to-site-create.md
[vpn-gateway-vpn-faq]:../../../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]:../../../cli-install-nodejs.md
[xplat-cli-azure-resource-manager]:../../../xplat-cli-azure-resource-manager.md




Este documento aborda várias áreas diferentes a serem consideradas ao implantar o SQL Server para carga de trabalho do SAP no IaaS do Azure. Como uma pré-condição para este documento, você deve ler o documento [Considerações para implantação de DBMS de Máquinas Virtuais do Azure para carga de trabalho do SAP](dbms_guide_general.md), bem como outros guias de [carga de trabalho do SAP na documentação do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started). 



> [!IMPORTANT]
> O escopo deste documento é a versão do Windows no SQL Server. SAP já não suporta a versão do Linux do SQL Server com qualquer um dos software SAP. O documento não está discutindo o Banco de Dados SQL do Microsoft Azure, que é uma oferta de Plataforma como Serviço da Plataforma Microsoft Azure. A discussão neste artigo é sobre a execução do produto SQL Server como é conhecido para implantações locais nas máquinas virtuais do Azure, aproveitando a funcionalidade de Infraestrutura como Serviço do Azure. As funcionalidades e recursos do banco de dados entre essas duas ofertas são muito diferentes e não devem ser misturados entre si. Consulte também: <https://azure.microsoft.com/services/sql-database/>
> 
>

Em geral, você deve considerar usar o SQL Server mais recente para executar a carga de trabalho SAP no IaaS do Azure. As versões mais recentes do SQL Server oferecem a melhor integração com alguns dos serviços do Azure e funcionalidade. Ou tem alterações que otimizam as operações em uma infraestrutura de IaaS do Azure.

É recomendável examinar [esta][virtual-machines-sql-server-infrastructure-services] documentação antes de continuar.

Nas seções a seguir, trechos de partes da documentação no link acima são agregados e mencionados. Informações especificas do SAP também são mencionadas e alguns conceitos são descritos mais detalhadamente. No entanto, é altamente recomendável percorrer a documentação acima primeiro antes de ler a documentação específica do SQL Server.

Há algumas informações específicas do SQL Server no IaaS que você deve conhecer antes de continuar:

* **Suporte de Versão do SQL**: Para clientes SAP, há suporte para o SQL Server 2008 R2 e posterior na Máquina Virtual do Microsoft Azure. Não há suporte para edições anteriores. Examine esta [Instrução de suporte](https://support.microsoft.com/kb/956893) geral para obter mais detalhes. Em geral, o SQL Server 2008 também tem suporte da Microsoft. No entanto, devido à funcionalidade significativa para SAP que foi introduzida com o SQL Server 2008 R2, o SQL Server 2008 R2 é a versão mínima para SAP. Em geral, você deve considerar usar o SQL Server mais recente para executar a carga de trabalho SAP no IaaS do Azure. As versões mais recentes do SQL Server oferecem a melhor integração com alguns dos serviços do Azure e funcionalidade. Ou tem alterações que otimizam as operações em uma infraestrutura de IaaS do Azure. Portanto, o documento é restrito ao SQL Server 2016 e SQL Server 2017.
* **Desempenho do SQL**: As Máquinas Virtuais hospedadas do Microsoft Azure têm um bom desempenho em comparação com outras ofertas de virtualização de nuvem pública, mas os resultados individuais podem variar. Confira o artigo [Práticas recomendadas de desempenho para o SQL Server em Máquinas Virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-performance).
* **Usando imagens do Azure Marketplace**: A maneira mais rápida de implantar uma nova VM do Microsoft Azure é usar uma imagem do Azure Marketplace. Há imagens no Azure Marketplace que contêm o SQL Server mais recente. As imagens em que o SQL Server já está instalado não podem ser usadas imediatamente para aplicativos SAP NetWeaver. O motivo é que a ordenação do SQL Server padrão é instalada dentro dessas imagens e não a ordenação exigida pelos sistemas SAP NetWeaver. Para usar essas imagens, verifique as etapas documentadas no capítulo [Como usar uma imagem do SQL Server fora do Microsoft Azure Marketplace][dbms-guide-5.6]. 


## <a name="recommendations-on-vmvhd-structure-for-sap-related-sql-server-deployments"></a>Recomendações sobre a estrutura de VM/VHD para implantações do SQL Server relacionadas ao SAP
De acordo com a descrição geral, os executáveis do SQL Server devem ser localizados ou instalados na unidade de sistema do disco do SO da VM (unidade C:\).  Normalmente, a maioria dos bancos de dados de sistema do SQL Server não é utilizada em um alto nível pela carga de trabalho do SAP NetWeaver. Como resultado, os bancos de dados de sistema do SQL Server (mestre, msdb e modelo) podem permanecer na unidade C:\ também. Uma exceção poderia ser o tempdb, que, no caso de algumas cargas de trabalho de SAP, pode exigir um volume de dados maior ou um volume de operações de E/S maior. Carga de trabalho de e/s, que não deve ser aplicada para o VHD do SO. Para esses sistemas, as etapas a seguir devem ser executadas:


* Com todos os tipos de VM certificadas pela SAP (consulte a Nota SAP [1928533]), exceto que VMs da série A, dados tempdb e arquivos de log podem ser colocados na unidade D:\ não-persistente. 
* No entanto, é recomendável usar vários arquivos de dados de tempdb. Esteja ciente de que os volumes da unidade D:\ são diferentes com base no tipo de VM. Para obter tamanhos exatos da unidade D:\ das VMs diferentes, verifique o artigo [máquinas virtuais de tamanhos para Windows no Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).

Essas configurações permitem que o tempdb consuma mais espaço do que a unidade do sistema é capaz de fornecer. A unidade D:\ não persistente também oferece melhor latência de e/s e taxa de transferência (com exceção de VMs da série). Para determinar o tamanho adequado de tempdb, é possível verificar os tamanhos de tempdb nos sistemas existentes. 

>[!NOTE]
> no caso de você coloca os arquivos de dados tempdb e o arquivo de log em uma pasta na unidade D:\ que você criou, você precisa certificar-se de que a pasta existe após uma reinicialização da VM. Uma vez que a unidade D:\ é inicializada recentemente após uma reinicialização da VM todas as estruturas de arquivos e diretórios são apagadas. Uma possibilidade de recriar as estruturas de diretório eventual na unidade D:\ antes do início do serviço do SQL Server é documentado em [este artigo](https://www.sqlserver.co.uk/index.php/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/).

Uma configuração de VM que executa o SQL Server com um banco de dados SAP e em que os arquivos de log e dados do tempdb estão na unidade D:\ teria a seguinte aparência:

![Diagrama da configuração de disco de VM simples para o SQL Server](./media/dbms_sqlserver_deployment_guide/Simple_disk_structure.PNG)

O diagrama acima exibe um caso simples. Como o momento, passou para o artigo [considerações para implantação de DBMS de máquinas virtuais do Azure para a carga de trabalho SAP](dbms_guide_general.md), o número e tamanho dos discos de armazenamento Premium é dependente de diferentes fatores. Mas, em geral, recomendamos:

- Usando espaços de armazenamento para formar um ou um pequeno número de volumes, que contêm os arquivos de dados do SQL Server. O motivo por trás desta configuração é que, na vida real, há vários bancos de dados do SAP com arquivos de banco de dados de tamanhos diferentes com diferentes cargas de trabalho de e/s.
- Usando espaços de armazenamento para fornecer IOPS suficiente e para o arquivo de log de transações do SQL Server. Carga de trabalho IOPS potencial geralmente é a linha de orientação para o tamanho do volume de log de transações e não o volume potencial do volume de transações do SQL Server
- Use o D:\drive para tempdb, desde que o desempenho seja bom o suficiente. Se a carga de trabalho geral é limitada no desempenho pelo tmepdb que está sendo localizado na unidade D:\ você talvez precise considerar mover tempdb para separar os discos de armazenamento Premium conforme recomendado em [este artigo](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-performance).


### <a name="special-for-m-series-vms"></a>Especial para VMs da série M
Para a VM da série M do Azure, a latência de gravação nos logs de transação pode ser reduzida por fatores, comparados ao desempenho do armazenamento Premium do Azure, ao usar o Acelerador de gravação do Azure. Portanto, você deve implantar o Acelerador de gravação do Azure para os VHDs que formam o volume para os logs de transação do SQL Server. Detalhes podem ser lidos no documento [Acelerador de Gravação](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator).
  

### <a name="formatting-the-disks"></a>Formatação dos discos
Para o SQL Server, o tamanho do bloco NTFS para discos contendo arquivos de log e de dados do SQL Server deve ser de 64KB. Não é necessário formatar a unidade D:\. Essa unidade vem pré-formatada.

Para se certificar de que a restauração ou a criação de bancos de dados não está inicializando os arquivos de dados zerando o conteúdo dos arquivos, é necessário se assegurar de que o contexto de usuário em que o serviço do SQL Server está em execução tem uma determinada permissão. Normalmente, os usuários do grupo Administrador do Windows têm essas permissões. Se o serviço do SQL Server for executado no contexto do usuário que não é Administrador do Windows, você precisará atribuir ao usuário o direito **Executar tarefas de manutenção de volume**.  Confira os detalhes neste artigo da Base de Dados de Conhecimento da Microsoft: <https://support.microsoft.com/kb/2574695>

### <a name="impact-of-database-compression"></a>Impacto da compactação do banco de dados
Em configurações em que a largura de banda de E/S pode se tornar um fator limitante, todas as medidas que reduzem IOPS podem ajudar a ampliar a carga de trabalho que pode ser executada em um cenário de IaaS como o Azure. Portanto, caso você ainda não tenha feito isso, a SAP e Microsoft recomendam aplicar a compactação de PÁGINA do SQL Server antes de carregar um banco de dados SAP existentes para o Azure.

A recomendação para executar a compactação do banco de dados antes de carregar no Azure é fornecida por duas razões:

* A quantidade de dados a ser carregada é menor.
* A duração da execução de compactação é menor, presumindo-se que seja possível usar um hardware mais poderoso com mais CPUs ou maior largura de banda de E/S ou menor latência de E/S local.
* Tamanhos menores de banco de dados podem levar a menos custos de alocação de disco

A compactação de banco de dados funciona bem nas máquinas virtuais do Azure como o faz localmente. Para obter mais detalhes sobre como compactar bancos de dados do SQL Server do SAP NetWeaver existentes, consulte o artigo [ferramenta de compactação aprimorada SAP MSSCOMPRESS](https://blogs.msdn.microsoft.com/saponsqlserver/2016/11/25/improved-sap-compression-tool-msscompress/). 

## <a name="sql-server-2014-and-more-recent---storing-database-files-directly-on-azure-blob-storage"></a>SQL Server 2014 e mais recente – armazenando arquivos do banco de dados arquivos diretamente no Armazenamento de Blobs do Azure
O SQL Server 2014 e versões mais recentes abrem a possibilidade para armazenar arquivos de banco de dados diretamente no Armazenamento de Blobs do Azure sem o ‘wrapper’ de um VHD em torno deles. Especialmente com o uso do Armazenamento do Azure Standard ou tipos de VM menores, este tipo de implantação possibilita cenários em que você pode superar os limites de IOPS que seriam impostos por um número limitado de discos que podem ser montados a alguns tipos de VM menores. Esta maneira de implantação funciona para bancos de dados de usuário, no entanto, não para bancos de dados de sistema do SQL Server. Ele também funciona para arquivos de log e dados do SQL Server. Se desejar implantar um banco de dados do SQL Server SAP dessa forma, em vez de aplicar 'wrappers' nele nos VHDs, tenha em mente:

* A conta de armazenamento usada precisa estar na mesma região do Azure que a usada para implantar a VM em que o SQL Server está sendo executado.
* As considerações listadas anteriormente em relação à distribuição de VHDs em diferentes Contas de Armazenamento do Azure também se aplicam a esse método de implantações. Significa a contagem de operações de E/S em relação aos limites da Conta de Armazenamento do Azure.
* Em vez de contabilização em relação à cota de e/s de armazenamento da VM, o tráfego em blobs de armazenamento que representam os arquivos de log e de dados do SQL Server será contabilizado na largura de banda de rede da VM do tipo específico de VM. Para obter a largura de banda de rede e de armazenamento de um tipo específico de VM, confira o artigo [Tamanhos das máquinas virtuais do Windows no Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).
* Como resultado do envio por push da E/S de arquivo por meio da cota de rede, você está deixando de lado grande parte da cota de armazenamento e, com isso, usando a largura de banda geral da VM apenas parcialmente.
* As metas de desempenho de taxa de transferência e/s e IOPS que tem o armazenamento Premium do Azure para os tamanhos de disco diferentes não se aplicam mais. Mesmo se os blobs que você criou estejam localizados no armazenamento Premium do Azure. Os destinos são documentados no artigo [Armazenamento Premium de alto desempenho e discos gerenciados para VMs](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage). Como resultado de colocar arquivos de dados do SQL Server e arquivos de log diretamente nos blobs que são armazenados no armazenamento Premium do Azure, as características de desempenho podem ser diferentes em comparação com VHDs no armazenamento Premium do Azure.
* O cache baseado em host disponível para discos de Armazenamento Premium do Azure não está disponível para arquivos de dados do SQL Server colocados diretamente nos blobs do Azure.
* Em VMs da série M, acelerador de gravação do Azure não pode ser usado para dar suporte a gravações de submilissegundo contra o arquivo de log de transações do SQL Server. 

Os detalhes dessa funcionalidade podem ser encontrados no artigo [arquivos de dados do SQL Server no Microsoft Azure](https://docs.microsoft.com/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure?view=sql-server-2017)

Recomendação para sistemas de produção é para evitar essa configuração em vez disso, escolha os posicionamentos de dados do SQL Server e arquivos de log em VHDs do armazenamento Premium do Azure, em vez de diretamente nos blobs do Azure.


## <a name="sql-server-2014-buffer-pool-extension"></a>Extensão do pool de buffers do SQL Server 2014
O SQL Server 2014 introduziu um novo recurso, chamado [Extensão do Pool de Buffers](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension?view=sql-server-2017). Essa funcionalidade estende o pool de buffers do SQL Server, que é mantida na memória com um cache de segundo nível apoiado por SSDs local de um servidor ou VM. A extensão do pool de buffers permite manter um conjunto de trabalho maior de dados 'na memória'. Em comparação ao acesso ao Armazenamento Standard do Azure, o acesso à extensão do pool de buffers, que é armazenado em SSDs locais de uma VM do Azure, é muito mais rápido. Comparando a extensão do Pool de buffers do Cache de leitura de armazenamento do Azure Premium, conforme recomendado para arquivos de dados do SQL Server, não há vantagens significativas são esperadas para as extensões do Pool de Buffer. O motivo é que ambos os caches (extensão do pool de buffers do SQL Server e o cache de leitura do Armazenamento Premium) estão usando os discos locais dos nós de computação do Azure.

Experiências obtidas nesse meio tempo com a extensão do Pool de buffers do SQL Server com carga de trabalho do SAP é misto e ainda não permitir recomendações claras se deve usá-lo em todos os casos. O caso ideal é que o conjunto de trabalho que requer que o aplicativo SAP se ajuste na memória principal. Com o Azure, entretanto, oferecendo VMs que vêm com até 4 TB de memória, deve ser possível manter o conjunto de trabalho na memória. Portanto, o uso da extensão do Pool de buffers é limitado a alguns casos raros e não deve ser um caso de base.  

## <a name="backuprecovery-considerations-for-sql-server"></a>Considerações sobre backup e recuperação para o SQL Server
Ao implantar o SQL Server no Azure, sua metodologia de backup deve ser examinada. Mesmo se o sistema não for um sistema produtivo, o banco de dados SAP hospedado pelo SQL Server deverá ser submetido a backup periodicamente. Como o armazenamento do Azure mantém três imagens, um backup agora é menos importante no que diz respeito à compensação de uma falha de armazenamento. A razão de prioridade para manter um plano de backup e recuperação adequado é mais que você pode compensar erros lógicos/manuais fornecendo funcionalidades de recuperação pontual. Portanto, o objetivo é usar backups para restaurar o banco de dados para um determinado momento ou usar os backups no Azure para propagar outro sistema copiando o banco de dados existente. 

Para examinar diferentes possibilidades de backup de SQL Server no Azure, leia o artigo [Backup e Restauração do SQL Server em máquinas virtuais Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery). O artigo aborda várias possibilidades diferentes.

### <a name="manual-backups"></a>Backups manuais
Você tem várias possibilidades para executar backups 'manuais' por:

1. Executando backups convencionais do SQL Server para os discos do Azure anexados diretos. Esse método tem a vantagem de que você tenha os backups disponíveis com rapidez para atualizações do sistema e criar de novos sistemas como cópias de sistemas SAP existentes
2.  O SQL Server 2012 CU4 e superior podem fazer o backup de bancos de dados para uma URL de armazenamento do Azure.
3.  Backups de instantâneos de arquivos para arquivos de banco de dados no Armazenamento de Blobs do Azure. Esse método só funciona quando os arquivos de log e dados do SQL Server estão localizados no armazenamento de blobs do Azure

O primeiro método é bem conhecida e aplicadas em muitos casos, no mundo local. No entanto, ele deixa você com a tarefa para resolver o local de backup de prazo mais longo. Como você não deseja manter seus backups para 30 ou mais dias no armazenamento do Azure conectado localmente, você tem a necessidade de usar os serviços de Backup do Azure ou outra ferramenta de backup/recuperação de terceiros que inclui o acesso e gerenciamento de retenção para seus backups. Ou, você cria um servidor de arquivos grandes no Azure usando espaços de armazenamento do Windows.

O segundo método é descrito mais próximos no artigo [SQL Server Backup para URL](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url?view=sql-server-2017). Diferentes versões do SQL Server têm algumas variações nessa funcionalidade. Portanto, você deve verificar a documentação para sua verificação de versão específica do SQL Server. É importante observar que este artigo lista muitas restrições. Você tem a possibilidade de realizar o backup contra:

- Um único blob de páginas do Azure, que, em seguida, limita o tamanho do backup para 1000 GB. Isso também limita a taxa de transferência que você pode atingir.
- Vários blobs de blocos do Azure (até 64), que permitem que um tamanho do backup teórico de 12 TB. No entanto, testes com bancos de dados do cliente revelaram que o tamanho máximo de backup pode ser menor do que seu limite teórico. Nesse caso, você é responsável por gerenciar também os backups de retenção de backups e o acesso.


### <a name="automated-backup-for-sql-server"></a>Backup automatizado para SQL Server
O Backup Automatizado fornece um serviço de backup automático para edições do SQL Server Standard e Enterprise em execução em uma VM do Windows no Azure. Esse serviço é fornecido pela [Extensão do SQL Server IaaS Agent](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension), que é instalado automaticamente em imagens de máquinas virtuais do Windows do SQL Server no Portal do Azure. Se você implantar suas próprias imagens de sistema operacional com o SQL Server instalado, você precisará instalar as extensões de VM separadamente. As etapas necessárias estão documentadas neste [artigo](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension).

Mais detalhes sobre os recursos desse método podem ser encontrados nesses artigos:

- SQL Server 2014: [Backup Automatizado para Máquinas Virtuais do SQL Server 2014 (Resource Manager)](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-automated-backup)
- SQL Server 2016/2017: [Backup Automatizado v2 para Máquinas Virtuais do Azure (Resource Manager)](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-automated-backup-v2)

Observando a documentação, você pode ver que a funcionalidade com as versões mais recentes do SQL Server foi aprimorada. Mais alguns detalhes sobre backups automatizados são liberados no artigo do SQL Server [SQL Server Managed Backup to Microsoft Azure](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure?view=sql-server-2017). O limite de tamanho do backup teórico é 12 TB.  Os backups automatizados podem ser um bom método para tamanhos de backup de até 12 TB. Uma vez que vários blobs são gravados em paralelo, você pode esperar uma produtividade maior que 100 MB/s. 
 

### <a name="azure-backup-for-sql-server-vms"></a>Backup do Azure para VMs de SQL Server
Esse novo método de backups do SQL Server é oferecido a partir de junho de 2018 como visualização pública pelos serviços de Backup do Azure. O método de backup do SQL Server é o mesmo que outras ferramentas de terceiros estão usando, ou seja, a interface VSS/VDI do SQL Server para backups de fluxo para um local de destino. Nesse caso, o local de destino é o cofre do serviço de recuperação do Azure.

Uma descrição mais detalhada desse método de backup, que adiciona várias vantagens de configurações de backup centrais, monitoramento, e a administração está disponível [aqui](https://docs.microsoft.com/azure/backup/backup-azure-sql-database). 


### <a name="third-party-backup-solutions"></a>Soluções de backup de terceiros
Para um grande número de clientes do SAP, não houve nenhuma possibilidade de recomeçar e introduzir completas novas soluções de backup da parte do seu cenário SAP que estava em execução no Azure. Como resultado, as soluções existentes de backup necessárias para serem usadas e estendidas para o Azure. Estendendo soluções de backup existentes para o Azure normalmente funcionava bem com a maioria dos principais fornecedores neste espaço. 


## <a name="1b353e38-21b3-4310-aeb6-a77e7c8e81c8"></a>Como usar imagens do SQL Server do Microsoft Azure Marketplace
A Microsoft oferece VMs no Azure Marketplace que já contêm versões do SQL Server. Para os clientes SAP que necessitam de licenças para o SQL Server e Windows, usar essas imagens pode ser uma oportunidade para cobrir a necessidade de licenças gerando VMs com o SQL Server já instalado. Para usar essas imagens para SAP, as considerações a seguir precisam ser feitas:

* As versões do SQL Server que não são de avaliação acarretam em custos mais elevados do que uma VM ‘Somente Windows’ implantada do Azure Marketplace. Consulte estes artigos para comparar os preços: <https://azure.microsoft.com/pricing/details/virtual-machines/windows/> e <https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/>. 
* Você pode usar apenas versões do SQL Server que têm suporte da SAP.
* A ordenação da instância do SQL Server que é instalada nas VMs oferecidas no Azure Marketplace não é a ordenação que o SAP NetWeaver requer que a instância do SQL Server execute. No entanto, você pode alterar a ordenação com as instruções na seção a seguir.

### <a name="changing-the-sql-server-collation-of-a-microsoft-windowssql-server-vm"></a>Alterando a ordenação do SQL Server de uma VM Microsoft Windows/SQL Server
Uma vez que as imagens do SQL Server no Azure Marketplace não estão configuradas para usarem a ordenação, o que é exigido pelos aplicativos SAP NetWeaver, elas precisam ser alteradas imediatamente após a implantação. Para o SQL Server, esta mudança de ordenação pode ser feita com as etapas a seguir assim que a VM tiver sido implantada e um administrador for capaz de fazer logon nela:

* Abra uma janela Comando do Windows como administrador.
* Altere o diretório para C:\Arquivos de Programas\Microsoft SQL Server\110\Setup Bootstrap\SQLServer2012.
* Execute o comando: Setup.exe /QUIET /ACTION=REBUILDDATABASE /INSTANCENAME=MSSQLSERVER /SQLSYSADMINACCOUNTS=`<local_admin_account_name`> /SQLCOLLATION=SQL_Latin1_General_Cp850_BIN2   
  * `<local_admin_account_name`> é a conta definida como a conta Administrador ao implantar a VM pela primeira vez por meio da galeria.

O processo deve levar apenas alguns minutos. Para verificar se a etapa terminou com o resultado correto, execute as seguintes etapas:

* Abra o SQL Server Management Studio.
* Abra uma Janela de Consulta.
* Execute o comando sp_helpsort no banco de dados mestre do SQL Server.

O resultado desejado deve ter uma aparência semelhante a essa:

    Latin1-General, binary code point comparison sort for Unicode Data, SQL Server Sort Order 40 on Code Page 850 for non-Unicode Data

Se o resultado for diferente, INTERROMPA a implantação do SAP e investigue por que o comando de instalação não funcionou conforme o esperado. A implantação de aplicativos SAP NetWeaver na instância do SQL Server com páginas de código do SQL Server diferentes da mencionada acima **NÃO** tem suporte.

## <a name="sql-server-high-availability-for-sap-in-azure"></a>Alta disponibilidade do SQL Server para SAP no Azure
Usando o SQL Server em implantações de IaaS do Azure para SAP, você tem várias possibilidades diferentes para adicionar para implantar a camada de DBMS altamente disponível. Conforme discutido em [considerações para implantação de DBMS de máquinas virtuais do Azure para a carga de trabalho SAP](dbms_guide_general.md) já, o Azure fornece diferentes SLAs de tempo de atividade para uma única VM e um par de máquinas virtuais implantadas em um conjunto de disponibilidade do Azure. Pressupõe-se a unidade para o SLA de tempo de atividade para suas implantações de produção que requer a implantação em conjuntos de disponibilidade do Azure. Nesse caso, você precisa implantar um mínimo de duas VMs em tal conjunto de disponibilidade. Uma máquina virtual executará a instância do SQL Server Active Directory. A outra VM executará a instância passiva

### <a name="sql-server-clustering-using-windows-scale-out-file-server"></a>Clustering do SQL Server usando o Servidor de Arquivos de Escalabilidade Horizontal do Windows
Com o Windows Server 2016, a Microsoft introduziu [Espaços de Armazenamento Diretos](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview). Com base na implantação direta de espaços de armazenamento, o clustering de FCI do SQL Server tem suporte. Detalhes podem ser encontrado no artigo [Configurar a instância de Cluster de Failover do SQL Server em máquinas virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-create-failover-cluster). A solução requer um balanceador de carga do Azure também para lidar com o endereço IP virtual os recursos de cluster. Os arquivos de banco de dados do SQL Server são armazenados nos espaços de armazenamento. Portanto, é um dado que você seria necessário para criar os espaços de armazenamento do Windows com base em armazenamento Premium do Azure. Como esta solução foi suportada por não muito tempo ainda, não há clientes SAP conhecidos que usam essa solução em cenários de produção SAP.  

### <a name="sql-server-log-shipping"></a>Envio de logs do SQL Server
Um dos métodos de HA (alta disponibilidade) é o envio de logs do SQL Server. Se as VMs que participam da configuração de HA tiverem uma resolução de nome funcionando, não haverá problema e a configuração no Azure não será diferente de nenhuma configuração feita localmente. No que diz respeito à configuração do Envio de Logs e dos princípios em torno disso. Detalhes de envio de logs do SQL Server podem ser encontrados no artigo [sobre o Log de envio (SQL Server)](https://docs.microsoft.com/sql/database-engine/log-shipping/about-log-shipping-sql-server?view=sql-server-2017).

Funcionalidade de envio de log do SQL Server foi mal usada no Azure para alcançar alta disponibilidade dentro de uma região do Azure. No entanto nos seguintes cenários clientes SAP estavam usando o envio de logs com êxito em conjunto com o Azure:

- Cenários de recuperação de desastre de uma região do Azure em outra região do Azure
- Configuração de recuperação de desastre do local em uma região do Azure
- Cenários de migração do local para o Azure. Nesses casos, o envio de logs é usado para sincronizar a nova implantação de DBMS no Azure com a produção contínua de sistema local. No momento do corte, a produção é encerrada e é garantido que os últimos e mais recentes backups de log de transação foram transferidos para a implantação do DBMS do Azure. Em seguida, a implantação de DBMS do Azure é aberta para a produção.  



### <a name="database-mirroring"></a>Espelhamento de banco de dados
O espelhamento de banco de dados conforme o que tem suporte pelo SAP (consulte a Nota SAP [965908]) se baseia na definição de um parceiro de failover na cadeia de conexão SAP. Para os casos entre instalações, presumimos que as duas VMs estão no mesmo domínio e que o contexto de usuário sob o qual as duas instâncias do SQL Server estão sendo executadas é o usuário de domínio e tem privilégios suficientes nas duas instâncias do SQL Server envolvidas. Portanto, a configuração do espelhamento de banco de dados no Azure não difere entre uma configuração/instalação local típica.

A partir de implantações somente em nuvem, o método mais fácil é ter outra configuração de domínio no Azure para que essas VMs de DBMS (e VMs do SAP idealmente dedicadas) dentro de um domínio.

Se um domínio não for possível, também será possível usar certificados para pontos de extremidade de espelhamento de banco de dados, conforme descrito aqui: <https://docs.microsoft.com/sql/database-engine/database-mirroring/use-certificates-for-a-database-mirroring-endpoint-transact-sql>

Um tutorial para configurar o Espelhamento de Banco de Dados no Azure pode ser encontrado aqui: <https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server> 

### <a name="sql-server-always-on"></a>SQL Server Always On
Uma vez que Always On tem suporte para SAP local (consulte a Nota SAP [1772688]), ele tem suporte em combinação com o SAP no Azure. Há algumas considerações especiais para implantar o ouvinte de grupo de disponibilidade do SQL Server (não deve ser confundido com o conjunto de disponibilidade do Azure) uma vez que o Azure neste momento não permite criar um objeto AD/DNS como é possível localmente. Portanto, algumas etapas de instalação diferentes são necessárias para superar o comportamento específico do Azure.

Algumas considerações sobre o uso de um ouvinte de grupo de disponibilidade são:

* O uso de um ouvinte de grupo de disponibilidade é possível apenas com o Windows Server 2012 ou superior como o SO convidado da VM. Para o Windows Server 2012, é necessário certificar-se de que este patch foi aplicado: <https://support.microsoft.com/kb/2854082> 
* Para o Windows Server 2008 R2, esse patch não existe e Always On precisaria ser usado da mesma maneira que o espelhamento de banco de dados especificando um parceiro de failover na cadeia de conexões (feito por meio do parâmetro default.pfl do SAP dbs/mss/server – consulte a Nota SAP [965908]).
* Ao usar um ouvinte de grupo de disponibilidade, as VMs de banco de dados precisam estar conectadas a um balanceador de carga dedicado. Para evitar que o Azure atribua novos endereços IP em casos em que ambas as VMs sejam desligadas incidentalmente, deve-se atribuir endereços IP estáticos aos adaptadores de rede das VMs na configuração Always On (a definição de um endereço IP estático é descrita [neste][virtual-networks-reserved-private-ip] artigo)
* Há etapas especiais necessárias ao criar a configuração de cluster de WSFC em que o cluster precisa de um endereço IP especial atribuído, pois o Azure com sua funcionalidade atual atribuiria ao nome do cluster o mesmo endereço IP que o nó em que o cluster foi criado. Isso significa que uma etapa manual deve ser executada para atribuir um endereço IP diferente ao cluster.
* O ouvinte do grupo de disponibilidade será criado no Azure com pontos de extremidade TCP/IP atribuídos às VMs executando as réplicas primária e secundária do grupo de disponibilidade.
* Pode haver a necessidade de proteger esses pontos de extremidade com ACLs.

Lista a documentação detalhada sobre como implantar Always On com o SQL Server em VMs do Azure, como:

- [Introdução aos grupos de disponibilidade do AlwaysOn do SQL Server em máquinas virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview).
- [Configurar um Grupo de Disponibilidade Always On em máquinas virtuais do Azure em diferentes regiões](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-dr).
- [Configurar um balanceador de carga para um grupo de disponibilidade Always On no Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener).

>[!NOTE]
> Se você estiver configurando o balanceador de carga do Azure para o endereço IP virtual do ouvinte do grupo de disponibilidade, certifique-se de que o DirectServerReturn está configurado. a configuração dessa opção pode reduzir a rede de latência de viagem entre a camada de aplicativo SAP e a camada de DBMS de ida e volta. 

O SQL Server Always On é a funcionalidade de recuperação de desastre e alta disponibilidade usada mais comumente usada nas implantações de carga de trabalho do Azure para SAP. A maioria dos clientes usa o AlwaysOn para alta disponibilidade em uma única região do Azure. Se a implantação é restrita a apenas dois nós, você terá duas opções de conectividade:

- Usando o Ouvinte do Grupo de Disponibilidade. Com o ouvinte do grupo de disponibilidade, você deve implantar um balanceador de carga do Azure. Este geralmente é o método padrão de implantação. Aplicativos SAP devem ser configurados para conectarem-se contra o ouvinte do grupo de disponibilidade e não um único nó
- Usando os parâmetros de conectividade do espelhamento de banco de dados do SQL Server. Nesse caso, você precisará configurar a conectividade dos aplicativos SAP de uma forma em que ambos os nomes de nó sejam nomeados. Os detalhes exatos de tal configuração do lado do SAP estão documentadas na nota do SAP [#965908](https://launchpad.support.sap.com/#/notes/965908). Ao usar essa opção, você não precisaria configurar um ouvinte do Grupo de Disponibilidade. E com que nenhum balanceador de carga do Azure para a alta disponibilidade do SQL Server. Como resultado, a latência de rede entre a camada de aplicativo do SAP e a camada de DBMS é menor, pois o tráfego de entrada para a instância do SQL Server não é roteado por meio do balanceador de carga do Azure. Mas lembre-se de que essa opção só funcionará se você restringir o grupo de disponibilidade para abranger as duas instâncias. 

Muitos clientes estão aproveitando a funcionalidade do SQL Server Always On para a funcionalidade de recuperação de desastres adicionais entre regiões do Azure. Vários clientes também usam a capacidade de executar backups de uma réplica secundária. 

## <a name="sql-server-transparent-data-encryption"></a>Transparent Data Encryption do SQL Server
Há um número de clientes que estão usando o SQL Server [Criptografia de Dados Transparente (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017) quando implantar o seu SQL Server do SAP bancos de dados no Azure. A funcionalidade de TDE do SQL Server é totalmente suportada pelo SAP (consulte a nota SAP [#1380493](https://launchpad.support.sap.com/#/notes/1380493)). 

### <a name="applying-sql-server-tde"></a>Aplicação de TDE do SQL Server
Em casos em que você executar uma migração heterogênea de outro DBMS, em execução no local, para Windows/SQL Server em execução no Azure, você deve criar seu banco de dados de destino vazio no SQL Server antes do tempo. Como próxima etapa, você aplicaria a funcionalidade de TDE do SQL Server. Enquanto ainda estiver executando seu sistema de produção local. Motivo para executar a essa sequência é que o processo de criptografia de banco de dados vazio pode levar bastante tempo. Os processos de importação do SAP seriam, em seguida, importar os dados para o banco de dados criptografado durante a fase de tempo de inatividade. A sobrecarga da importação para um banco de dados criptografado tem um impacto de tempo menor de forma que criptografar o banco de dados após a fase de exportação a busca fase de tempo. Experiências negativas foram feitas ao tentar aplicar o TDE com a carga de trabalho do SAP em execução no topo do banco de dados. Portanto, a recomendação está tratando a implementação do TDE como uma atividade que precisa ser executada sem a carga de trabalho do SAP no banco de dados específico.

Em casos em que mover os bancos de dados do SQL Server SAP do local para o Azure, é recomendável testar sobre qual infraestrutura é possível aproveitar a criptografia aplicada o mais rápido. Para isso, tenha esses fatos em mente:

- Você não pode definir quantos threads são usados para aplicar a criptografia de dados no banco de dados. O número de threads é majoritariamente dependente do número de arquivos de log e dados do SQL Server são distribuídos ao longo de volumes de disco. Significa que quanto mais volumes distintos (letras de unidade), mais threads serão envolvidas em paralelo para executar a criptografia. Essa configuração contradiz um pouco com sugestão de configuração de disco anterior sobre a criação de um ou um número menor de espaços de armazenamento para arquivos de banco de dados do SQL Server em VMs do Azure. Uma configuração com um pequeno número de volumes poderia levar a um pequeno número de threads executando a criptografia. Um único thread com a criptografia está lendo as extensões de 64KB, criptografando-as e, em seguida, gravando um registro no arquivo de log de transações, informando que a extensão foi criptografada. Como resultado, a carga no log de transações é moderada.
- Nas versões mais antigas do SQL Server, compactação de backup não obteve eficiência mais quando você criptografou o seu banco de dados do SQL Server. Esse comportamento poderá desenvolver um problema quando o plano foi criptografar seu SQL Server banco de dados local e, em seguida, um backup de cópia no Azure para restaurar o banco de dados no Azure. Geralmente, compactação de backup do SQL Server atinge uma taxa de compactação de fator de 4.
- Com o SQL Server 2016, SQL Server introduziu a nova funcionalidade que permite a compactação também bancos de dados criptografados de forma eficiente. Ver [esse blogs](https://blogs.msdn.microsoft.com/sqlcat/2016/06/20/sqlsweet16-episode-1-backup-compression-for-tde-enabled-databases/) para alguns detalhes.
 
Tratando o aplicativo de criptografia da TDE com não para pouca carga de trabalho SAP somente, você deve testar em sua configuração específica se é melhor para aplicar a TDE para o seu SAP banco de dados local ou para fazer isso no Azure. No Azure, você certamente tem mais flexibilidade em termos de infraestrutura em excesso de provisionamento e reduzir a infra-estrutura depois TDE foi aplicada.

### <a name="using-azure-key-vault"></a>Usando o Azure Key Vault
O Azure oferece o serviço de uma [Key Vault](https://azure.microsoft.com/services/key-vault/) para armazenar chaves de criptografia. SQL Server no outro lado oferece um conector para aproveitar o Azure Key Vault como repositório para os certificados TDE.

Lista de mais detalhes para usar o Azure Key Vault para a TDE do SQL Server, como:

- [Gerenciamento extensível de chaves Usando o Azure Key Vault (SQL Server)](https://docs.microsoft.com/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server?view=sql-server-2017).
- [Gerenciamento extensível de chaves de TDE do SQL Server usando o Azure Key Vault - Etapas de Configuração](https://docs.microsoft.com/sql/relational-databases/security/encryption/setup-steps-for-extensible-key-management-using-the-azure-key-vault?view=sql-server-2017).
- [SQL Server Connector manutenção e solução de problemas](https://docs.microsoft.com/sql/relational-databases/security/encryption/sql-server-connector-maintenance-troubleshooting?view=sql-server-2017).
- [Mais perguntas dos clientes sobre o SQL Server Transparent Data Encryption – TDE + Azure Key Vault](https://blogs.msdn.microsoft.com/saponsqlserver/2017/04/04/more-questions-from-customers-about-sql-server-transparent-data-encryption-tde-azure-key-vault/).


>[!IMPORTANT]
>Usando a TDE do SQL Server, especialmente com o Azure key Vault, é recomendável usar os patches mais recentes do SQL Server 2014, SQL Server 2016 e SQL Server 2017. Razão é que com base nos comentários dos clientes, as otimizações e correções são aplicadas ao código. Por exemplo, verifique [KBA #4058175](https://support.microsoft.com/help/4058175/tde-enabled-backup-and-restore-slow-if-encryption-key-is-stored-in-ekm).
>  

## <a name="9053f720-6f3b-4483-904d-15dc54141e30"></a>Resumo do SQL Server para SAP no Azure geral
Há muitas recomendações neste guia e recomendamos que você o leia mais de uma vez antes de planejar sua implantação do Azure. Em geral, no entanto, não se esqueça de seguir os dez principais pontos específicos recomendados do DBMS no Azure gerais:

1. Use a versão mais recente do DBMS, como SQL Server 2017, que tem mais vantagens no Azure. 
2. Planeje cuidadosamente sua estrutura de sistema da SAP no Azure para balancear o layout do arquivo de dados e as restrições do Azure:
   * Não tenha discos demais, mas tenha espaço suficiente para garantir que você possa atingir seu IOPS necessário.
   * Se você não usar Managed Disks, lembre-se de que IOPS também estão limitados pela Conta de Armazenamento do Azure e que as Contas de Armazenamento são limitadas em cada assinatura do Azure ([mais detalhes][azure-subscription-service-limits]). 
   * Somente divida entre discos se você precisar obter uma maior taxa de transferência.
3. Nunca instale software nem coloque nenhum arquivo que exija persistência na unidade D:\, uma vez que ela não é permanente e tudo nessa unidade é perdido em uma reinicialização do Windows.
4. Não use cache de disco para o Armazenamento Standard do Azure.
5. Não use contas de armazenamento do Azure Standard com replicação de área geográfica do Azure.  Use Localmente Redundante para cargas de trabalho do DBMS.
6. Use a solução de HA/DR do seu fornecedor do DBMS para replicar dados do banco de dados.
7. Sempre use a resolução de nome, não confie em endereços IP.
8. Usando a TDE do SQL Server, aplique os patches mais recentes do SQL Server.
9. Use a maior compactação de banco de dados possível. Qual é a compactação de página para SQL Server.
10. Tenha cuidado ao usar imagens do SQL Server do Azure Marketplace. Se você usar o SQL Server um, deverá alterar a ordenação de instância antes de instalar qualquer sistema SAP NetWeaver nele.
11. Instale e configure o Monitoramento de Host do SAP para Azure, conforme descrito no [Guia de Implantação][deployment-guide].
