---
title: Implantação do DBMS de Máquinas de Virtuais do Oracle do Azure para carga de trabalho do SAP | Microsoft Docs
description: Implantação do DBMS de Máquinas Virtuais do Oracle Azure para carga de trabalho do SAP
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
ms.date: 12/14/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5182b621779cf31f3c7da99674ab24fe6efe702d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60835253"
---
# <a name="azure-virtual-machines-dbms-deployment-for-sap-workload"></a>Implantação do DBMS de Máquinas Virtuais do Azure para carga de trabalho do SAP

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
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/resources/templates/sql-server-2014-alwayson-existing-vnet-and-ad/
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


Este documento aborda várias áreas diferentes a serem consideradas ao implantar o Oracle Database para carga de trabalho do SAP no IaaS do Azure. Antes de ler este documento, é recomendável que você leia [Considerações para implantação do DBMS de Máquinas Virtuais do Azure para carga de trabalho do SAP](dbms_guide_general.md). Também recomendamos que você leia outros guias na [documentação do Azure sobre carga de trabalho do SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started). 

Você pode encontrar informações sobre versões do Oracle e do SO correspondentes compatíveis para executar o SAP em Oracle no Azure na nota SAP [2039619].

Informações gerais sobre como executar o SAP Business Suite no Oracle podem ser encontradas em [SAP no Oracle](https://www.sap.com/community/topic/oracle.html).
O software Oracle tem suporte pela Oracle para ser executado no Microsoft Azure. Para saber mais sobre o suporte geral do Windows Hyper-V e do Azure, confira as [Perguntas frequentes sobre o Oracle e o Microsoft Azure](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html). 

## <a name="sap-notes-relevant-for-oracle-sap-and-azure"></a>Notas do SAP relevantes para Oracle, SAP e Azure 

As Notas de SAP a seguir estão relacionadas ao SAP no Azure.

| Número da observação | Title |
| --- | --- |
| [1928533] |Aplicativos SAP no Azure: produtos com suporte e tipos de VM do Azure |
| [2015553] |SAP no Microsoft Azure: pré-requisitos de suporte |
| [1999351] |Solução de problemas de monitoramento aprimorado do Azure para SAP |
| [2178632] |Métricas-chave de monitoramento para SAP no Microsoft Azure |
| [2191498] |SAP no Linux com o Azure: monitoramento aprimorado |
| [2039619] |Os aplicativos SAP no Microsoft Azure que usam o Oracle Database: produtos e versões com suporte |
| [2243692] |Linux na VM (IaaS) do Microsoft Azure: Problemas de licença do SAP |
| [2069760] |Atualização e instalação do SAP do Oracle Linux 7.x |
| [1597355] |Recomendação de troca de espaço para Linux |
| [2171857] |Oracle Database 12c – suporte do sistema de arquivos em Linux |
| [1114181] |Oracle Database 11g – suporte do sistema de arquivos em Linux |

A configuração exata e funcionalidade com suporte da Oracle e SAP no Azure estão documentados na nota SAP [#2039619](https://launchpad.support.sap.com/#/notes/2039619).

O Windows e o Oracle Linux são os únicos sistemas operacionais com suporte da Oracle e SAP no Azure. Não há suporte para as distribuições SLES e RHEL do Linux amplamente usadas para implantar componentes do Oracle no Azure. Os componentes do Oracle incluem o cliente do Oracle Database, que é usado por aplicativos SAP para se conectar ao Oracle DBMS. 

As exceções, de acordo com a Nota SAP [#2039619](https://launchpad.support.sap.com/#/notes/2039619), são os componentes do SAP que não usam o cliente do Oracle Database. Tais componentes do SAP são enfileiramentos autônomos do SAP, o servidor de mensagens, os serviços de replicação de enfileiramento, o WebDispatcher e o Gateway de SAP.  

Mesmo se você executar seu DBMS do Oracle e as instâncias de aplicativos de SAP no Oracle Linux, é possível executar seus serviços da Central SAP em SLES ou RHEL e protegê-los com um cluster baseado em Pacemaker. Não há suporte para o Pacemaker como uma estrutura de alta disponibilidade no Oracle Linux.

## <a name="specifics-for-oracle-database-on-windows"></a>Informações específicas para o Oracle Database no Windows

### <a name="oracle-configuration-guidelines-for-sap-installations-in-azure-vms-on-windows"></a>Diretrizes de configuração do Oracle para instalações do SAP em VMs do Azure no Windows

De acordo com o manual de instalação do SAP, os arquivos relacionados ao Oracle não devem ser instalados ou localizados no driver do sistema para o disco de SO da VM (unidade C:). As máquinas virtuais de tamanhos variados são compatíveis com um número variado de discos anexados. Os tipos de máquinas virtuais menores podem são compatíveis com um número menor de discos anexados. 

Se você tiver VMs menores, é recomendável instalar/localizar a página inicial, o estágio, "saptrace", "saparch", "sapbackup", "sapcheck" ou "sapreorg" do Oracle no disco do SO. Essas partes dos componentes do DBMS do Oracle não fazem uso intenso de E/S e da taxa de transferência de E/S. Isso significa que o disco do SO pode lidar com os requisitos de E/S. O tamanho padrão do disco do SO é de 127 GB. 

Se não houver espaço livre disponível suficiente, o disco poderá ser [redimensionado](https://docs.microsoft.com/azure/virtual-machines/windows/expand-os-disk) para 2.048 GB. Os arquivos do Oracle Database e de log da fase refazer precisam ser armazenados em discos de dados separados. Há uma exceção no espaço de tabela temporária do Oracle. Tempfiles podem ser criados em D:/ (unidade não persistente). A unidade D:\ não persistente também oferece melhor latência de e/s e taxa de transferência (com exceção de VMs da série). 

Para determinar a quantidade certa de espaço para os tempfiles, você pode verificar os tamanhos dos tempfiles nos sistemas existentes.

### <a name="storage-configuration"></a>Configuração de armazenamento
Apenas a instância única do Oracle usando discos formatados em NTFS é compatível. Todos os arquivos de banco de dados devem ser armazenados no sistema de arquivos NTFS em Managed Disks (recomendado) ou em VHDs. Esses discos são montados na VM do Azure e são baseados no [Armazenamento de blob de página do Azure](https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs) ou nos [Azure Managed Disks](https://docs.microsoft.com/azure/storage/storage-managed-disks-overview). 

É altamente recomendável usar o [Azure Managed Disks](https://docs.microsoft.com/azure/storage/storage-managed-disks-overview). Também é altamente recomendável usar o [SSDs premium](../../windows/disks-types.md) para suas implantações do Oracle Database.

As unidades de rede ou de compartilhamentos remoto como os serviços de arquivo do Azure não são compatíveis com os arquivos do Oracle Database. Para obter mais informações, consulte:

- [Apresentando o serviço de arquivo do Microsoft Azure](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)

- [Persistindo conexões para arquivos do Microsoft Azure](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx)


Se você estiver usando discos baseados no armazenamento de blobs de páginas do Azure ou Managed Disks, as declarações nas [Considerações para implantação de DBMS de máquinas virtuais do Azure para a carga de trabalho SAP](dbms_guide_general.md) também se aplicarão a implantações com o Oracle Database.

Existem cotas na taxa de transferência IOPS para discos do Azure. Este conceito é explicado nas [Considerações para implantação de DBMS de máquinas virtuais do Azure para a carga de trabalho SAP](dbms_guide_general.md). As cotas exatas dependem do tipo de VM usado. Uma lista dos tipos de VMs com suas cotas pode ser encontrada em [Tamanhos para máquinas virtuais do Windows no Azure][virtual-machines-sizes-windows].

Para identificar os tipos de VM do Azure com suporte, confira a Nota SAP [1928533].

A configuração mínima é a seguinte: 

| Componente | Disco | Cache | Pool de armazenamento |
| --- | ---| --- | --- |
| \oracle\<SID>\origlogaA & mirrlogB | Premium | Nenhum | Não é necessário |
| \oracle\<SID>\origlogaB & mirrlogA | Premium | Nenhum | Não é necessário |
| \oracle\<SID>\sapdata1...n | Premium | Somente leitura | Pode ser usado |
| \oracle\<SID>\oraarch | Standard | Nenhum | Não é necessário |
| Oracle Home, saptrace, ... | Disco do sistema operacional | | Não é necessário |


A seleção de discos para hospedagem de logs de restauração online deve ser controlada pelos requisitos de IOPs. É possível armazenar todos os sapdata1... n (espaços de tabela) em um único disco montado, desde que o tamanho, o IOPS e a taxa de transferência atendam aos requisitos. 

A configuração de desempenho é a seguinte:

| Componente | Disco | Cache | Pool de armazenamento |
| --- | ---| --- | --- |
| \oracle\<SID>\origlogaA | Premium | Nenhum | Pode ser usado  |
| \oracle\<SID>\origlogaB | Premium | Nenhum | Pode ser usado |
| \oracle\<SID>\mirrlogAB | Premium | Nenhum | Pode ser usado |
| \oracle\<SID>\mirrlogBA | Premium | Nenhum | Pode ser usado |
| \oracle\<SID>\sapdata1...n | Premium | Somente leitura | Recomendadas  |
| \oracle\SID\sapdata(n+1)* | Premium | Nenhum | Pode ser usado |
| \oracle\<SID>\oraarch* | Premium | Nenhum | Não é necessário |
| Oracle Home, saptrace, ... | Disco do sistema operacional | Não é necessário |

*(n+1): hospedagem dos espaços de tabela SYSTEM, TEMP e UNDO. O padrão de E/S dos espaços de tabela System e Undo são diferentes dos outros espaços de tabela que hospedam dados de aplicativo. “Sem cache” é a melhor opção para o desempenho dos espaços de tabela System e Undo.

*oraarch: do ponto de vista do desempenho, o pool de armazenamento não é necessário. Ele pode ser usado para obter mais espaço.

Se mais IOPS forem necessários, é recomendável usar pools de armazenamento do Windows (disponíveis apenas no Windows Server 2012 e posteriores) para criar um grande dispositivo lógico em vários discos montados. Essa abordagem simplifica a sobrecarga administrativa de gerenciar o espaço em disco e ajuda a evitar o esforço de distribuir manualmente os arquivos entre vários discos montados.


#### <a name="write-accelerator"></a>Acelerador de Gravação
Para as VMs da série M do Azure, a latência de gravação nos logs online da fase refazer pode ser reduzida por fatores quando comparados ao Armazenamento Premium do Azure. Habilite o Acelerador de Gravação do Azure para os discos (VHDs) com base no Armazenamento Premium do Azure que são usados para arquivos de log de restauração online. Para saber mais, confira [Acelerador de Gravação](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator).


### <a name="backuprestore"></a>Backup/restauração
Para a funcionalidade de backup/restauração, o SAP BR*Tools para Oracle recebe suporte da mesma maneira que nos sistemas operacionais Windows Server padrão. O RMAN (Gerenciador de Recuperação) da Oracle também tem suporte para backups em disco e restaurações do disco.

Você também pode usar Backup do Azure para executar um backup de VM consistente do aplicativo. O artigo [Planejar sua infraestrutura de backup de VM no Azure](https://docs.microsoft.com/azure/backup/backup-azure-vms-introduction) explica como o Backup do Azure usa a funcionalidade do VSS do Windows para executar backups consistentes de aplicativos. As versões do DBMS do Oracle que têm suporte no Azure pela SAP podem aproveitar a funcionalidade do VSS para backups. Para saber mais, confira a documentação do Oracle [Conceitos básicos de backup de banco de dados e recuperação com o VSS](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/ntqrf/basic-concepts-of-database-backup-and-recovery-with-vss.html#GUID-C085101B-237F-4773-A2BF-1C8FD040C701).


### <a name="high-availability"></a>Alta disponibilidade
O Oracle Data Guard tem suporte para fins de recuperação de desastre e alta disponibilidade. Para fazer o failover automático no Data Guard, é necessário usar o FSFA (Failover de início rápido). O observador (FSFA) dispara o failover. Se você não usar o FSFA, só poderá usar uma configuração de failover manual.

Para saber mais sobre a recuperação de desastres para bancos de dados Oracle no Azure, confira [Recuperação de desastre para um banco de dados Oracle Database 12c em um ambiente do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/oracle-disaster-recovery).

### <a name="accelerated-networking"></a>Redes aceleradas
Para implantações do Oracle no Windows, é altamente recomendável usar a rede acelerada, como descrito em [Rede acelerada do Azure](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/). Também considere as recomendações feitas em [Considerações para implantação de DBMS de Máquinas Virtuais do Azure para a carga de trabalho SAP](dbms_guide_general.md). 
### <a name="other"></a>Outros
As [Considerações para implantação de DBMS de Máquinas Virtuais do Azure para a carga de trabalho SAP](dbms_guide_general.md) descrevem outros conceitos importantes relacionados às implantações de VMs com o Oracle Database, incluindo os conjuntos de disponibilidade do Azure e o monitoramento do SAP.

## <a name="specifics-for-oracle-database-on-oracle-linux"></a>Especificidades do Oracle Database no Oracle Linux
O software Oracle tem suporte pela Oracle para ser executado no Microsoft Azure com Linux Oracle como o SO convidado. Para saber mais sobre o suporte geral do Windows Hyper-V e do Azure, confira as [Perguntas frequentes sobre o Oracle e o Azure](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html). 

O cenário específico de aplicativos SAP que utilizam os Oracle Databases também tem suporte. Os detalhes são discutidos na próxima parte do documento.

### <a name="oracle-version-support"></a>Suporte de versão do Oracle
Para obter informações sobre quais versões do Oracle e as versões de SO correspondentes são compatíveis para execução do SAP no Oracle nas Máquinas Virtuais do Azure, confira a Nota SAP [2039619].

Informações gerais sobre como executar o SAP Business Suite no Oracle podem ser encontradas na [página da comunidade do SAP no Oracle](https://www.sap.com/community/topic/oracle.html).

### <a name="oracle-configuration-guidelines-for-sap-installations-in-azure-vms-on-linux"></a>Diretrizes de configuração do Oracle para instalações do SAP em VMs do Azure no Linux

De acordo com os manuais de instalação do SAP, os arquivos relacionados ao Oracle não devem ser instalados ou localizados no driver do sistema para o disco de inicialização da VM. Vários tamanhos de máquinas virtuais são compatíveis com um número variado de discos anexados. Os tipos de máquinas virtuais menores podem são compatíveis com um número menor de discos anexados. 

Nesse caso, é recomendável instalar/localizar a página inicial, o estágio, saptrace, saparch, sapbackup, sapcheck ou sapreorg no disco de inicialização. Essas partes dos componentes do DBMS do Oracle não fazem uso intenso de E/S e da taxa de transferência de E/S. Isso significa que o disco do SO pode lidar com os requisitos de E/S. O tamanho padrão do disco do SO é de 30 GB. Você pode expandir o disco de inicialização usando o portal, o PowerShell ou a CLI do Azure. Depois de expandir o disco de inicialização, você pode adicionar uma partição adicional para binários do Oracle.


### <a name="storage-configuration"></a>Configuração de armazenamento

Há suporte para os sistemas de arquivos ext4, xfs ou Oracle ASM em arquivos do Oracle Database no Azure. Todos os arquivos de banco de dados devem ser armazenados no sistemas de arquivos baseados em VHDs ou Managed Disks. Esses discos são montados na VM do Azure e são baseados no [Armazenamento de blob de página do Azure](<https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>) ou nos [Azure Managed Disks](../../windows/managed-disks-overview.md).

Para os kernels do Oracle Linux UEK, a versão mínima 4 do UEK é necessária para dar suporte aos [SSDs premium do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-caching).

É altamente recomendável usar os [Azure Managed Disks](../../windows/managed-disks-overview.md). Também é altamente recomendável usar os [SSDs premium do Azure](../../windows/disks-types.md) para suas implantações do Oracle Database.

As unidades de rede ou de compartilhamentos remoto como os serviços de arquivo do Azure não são compatíveis com os arquivos do Oracle Database. Para saber mais, consulte o seguinte:  

- [Apresentando o serviço de arquivo do Microsoft Azure](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)

- [Persistindo conexões para arquivos do Microsoft Azure](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx)

Caso esteja usando discos baseados no Armazenamento de blob de página do Azure ou Managed Disks, as declarações feitas nas [Considerações para implantação de DBMS de máquinas virtuais do Azure para a carga de trabalho SAP](dbms_guide_general.md) também se aplicarão a implantações com o Oracle Database.

 Existem cotas na taxa de transferência IOPS para discos do Azure. Esse conceito é explicado em [Considerações para implantação de DBMS de máquinas virtuais do Azure para a carga de trabalho SAP](dbms_guide_general.md). As cotas exatas dependem do tipo de VM usado. Para obter uma lista dos tipos de VM com suas cotas, confira [Tamanhos para máquinas virtuais Linux no Azure][virtual-machines-sizes-linux].

Para identificar os tipos de VM do Azure com suporte, confira a Nota SAP [1928533].

Configuração mínima:

| Componente | Disco | Cache | Stripping* |
| --- | ---| --- | --- |
| /Oracle/\<SID > / origlogaA & mirrlogB | Premium | Nenhum | Não é necessário |
| /Oracle/\<SID > / origlogaB & mirrlogA | Premium | Nenhum | Não é necessário |
| /Oracle/\<SID > / sapdata1... n | Premium | Somente leitura | Pode ser usado |
| /Oracle/\<SID > / oraarch | Standard | Nenhum | Não é necessário |
| Oracle Home, saptrace, ... | Disco do sistema operacional | | Não é necessário |

*Stripping: Faixa de LVM ou MDADM usando RAID0

A seleção de discos para hospedagem de logs de restauração online do Oracle deve ser controlada pelos requisitos de IOPs. É possível armazenar todos os sapdata1...n (espaços de tabela) em um único disco montado, desde que o volume, o IOPS e a taxa de transferência atendam aos requisitos. 

Configuração de desempenho:

| Componente | Disco | Cache | Stripping* |
| --- | ---| --- | --- |
| /Oracle/\<SID > / origlogaA | Premium | Nenhum | Pode ser usado  |
| /Oracle/\<SID > / origlogaB | Premium | Nenhum | Pode ser usado |
| /Oracle/\<SID > / mirrlogAB | Premium | Nenhum | Pode ser usado |
| /Oracle/\<SID > / mirrlogBA | Premium | Nenhum | Pode ser usado |
| /Oracle/\<SID > / sapdata1... n | Premium | Somente leitura | Recomendadas  |
| /oracle/\<SID>/sapdata(n+1)* | Premium | Nenhum | Pode ser usado |
| /oracle/\<SID>/oraarch* | Premium | Nenhum | Não é necessário |
| Oracle Home, saptrace, ... | Disco do sistema operacional | Não é necessário |

*Stripping: Faixa de LVM ou MDADM usando RAID0

*(n+1): hospedagem dos espaços de tabela SYSTEM, TEMP e UNDO: O padrão de E/S dos espaços de tabela System e Undo são diferentes dos outros espaços de tabela que hospedam dados de aplicativo. “Sem cache” é a melhor opção para o desempenho dos espaços de tabela System e Undo.

*oraarch: do ponto de vista do desempenho, o pool de armazenamento não é necessário.


Se mais IOPS forem necessários, é recomendável usar LVM (Gerenciador de Volume lógico) ou MDADM para criar um grande volume lógico usando vários discos montados. Para saber mais, confira [Considerações para implantação de DBMS de máquinas virtuais do Azure para a carga de trabalho SAP](dbms_guide_general.md) sobre diretrizes e ponteiros de como utilizar LVM ou MDADM. Essa abordagem simplifica a sobrecarga administrativa de gerenciar o espaço em disco e ajuda a evitar o esforço de distribuir manualmente os arquivos entre vários discos montados.


#### <a name="write-accelerator"></a>Acelerador de Gravação
Para as VMs da série M do Azure, ao usar o Acelerador de Gravação do Azure, a latência de gravação nos logs online de refazer pode ser reduzida por fatores, comparados ao desempenho do Armazenamento Premium do Azure. Habilite o Acelerador de Gravação do Azure para os discos (VHDs) com base no Armazenamento Premium do Azure que são usados para arquivos de log de restauração online. Para saber mais, confira [Acelerador de Gravação](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator).


### <a name="backuprestore"></a>Backup/restauração
Para funcionalidade de backup/restauração, há suporte para SAP BR*Tools para Oracle da mesma maneira que para Hyper-V e bare-metal. O RMAN (Gerenciador de Recuperação) da Oracle também tem suporte para backups em disco e restaurações do disco.

Para saber mais sobre como usar os serviços de Backup e Recuperação do Azure para fazer backup e recuperar bancos de dados Oracle, confira [Fazer backup e recuperar um banco de dados Oracle Database 12c em uma máquina virtual do Azure Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/oracle-backup-recovery).

### <a name="high-availability"></a>Alta disponibilidade
O Oracle Data Guard tem suporte para fins de recuperação de desastre e alta disponibilidade. Para fazer o failover automático no Data Guard, é necessário usar o FSFA (Failover de início rápido). A funcionalidade de Observador (FSFA) dispara o failover. Se você não usar o FSFA, só poderá usar uma configuração de failover manual. Para saber mais, confira [Implementando o Oracle Data Guard em uma máquina virtual do Linux no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/configure-oracle-dataguard).


Aspectos da recuperação de desastres para bancos de dados Oracle no Azure são apresentados no artigo [recuperação de desastre para um banco de dados banco de dados Oracle 12c em um ambiente do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/oracle-disaster-recovery).

### <a name="accelerated-networking"></a>Redes aceleradas
Suporte para rede acelerada do Azure no Oracle Linux é fornecido com o Oracle Linux 7 atualização 5 (Oracle Linux 7.5). Se você não puder atualizar para a versão mais recente do Oracle Linux 7.5, poderá haver uma solução alternativa usando o RHCK (RedHat Compatible Kernel) em vez do kernel da Oracle UEK. 

Usando o kernel RHEL no Oracle Linux tem suporte de acordo com a nota SAP [#1565179](https://launchpad.support.sap.com/#/notes/1565179). Para a Rede Acelerada do Azure, a versão mínima do kernel do RHCKL precisa ser 3.10.0-862.13.1.el7. Se você usar o kernel do UEK no Oracle Linux em conjunto com a [Rede Acelerada do Azure](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/), será necessário usar a versão 5 do kernel do Oracle UEK.

Se você estiver implantando as VMs de uma imagem que não se baseia no Azure Marketplace, será necessário copiar os arquivos de configurações adicionais para a VM executando o código a seguir: 
<pre><code># Copy settings from GitHub to the correct place in the VM
sudo curl -so /etc/udev/rules.d/68-azure-sriov-nm-unmanaged.rules https://raw.githubusercontent.com/LIS/lis-next/master/hv-rhel7.x/hv/tools/68-azure-sriov-nm-unmanaged.rules 
</code></pre>


### <a name="other"></a>Outros
As [Considerações para implantação de DBMS de Máquinas Virtuais do Azure para a carga de trabalho SAP](dbms_guide_general.md) descrevem outros conceitos importantes relacionados às implantações de VMs com o Oracle Database, incluindo os conjuntos de disponibilidade do Azure e o monitoramento do SAP.
