---
title: SAP MaxDB, liveCache e implantação do Servidor de Conteúdo nas VMs do Azure | Microsoft Docs
description: SAP MaxDB, liveCache e implantação do Servidor de Conteúdo no Azure
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
ms.date: 07/12/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 83319118c778d89749b1eb5d5fd792a5200c19c5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60835988"
---
# <a name="sap-maxdb-livecache-and-content-server-deployment-on-azure-vms"></a>SAP MaxDB, liveCache e implantação do Servidor de Conteúdo nas VMs do Azure

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
[dbms-guide-figure-900]:media/dbms_maxdb_deployment_guide/900-sap-cache-server-on-premises.png

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




Este documento aborda várias áreas diferentes a serem consideradas ao implantar o MaxDB, liveCache, e o Servidor de Conteúdo no IaaS do Azure. Como uma pré-condição para este documento, você deve ler o documento [Considerações para implantação de DBMS de Máquinas Virtuais do Azure para carga de trabalho do SAP](dbms_guide_general.md), bem como outros guias de [carga de trabalho do SAP na documentação do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started). 

## <a name="specifics-for-the-sap-maxdb-deployments-on-windows"></a>Informações específicas para a implantação SAP MaxDB no Windows
### <a name="sap-maxdb-version-support-on-azure"></a>Suporte de versão do SAP MaxDB no Azure
No momento, o SAP dá suporte ao SAP MaxDB versão 7.9 ou superior para o uso com produtos baseados no SAP NetWeaver no Azure. Todas as atualizações para o servidor SAP MaxDB ou drivers JDBC e ODBC a serem usadas com produtos baseados no SAP NetWeaver são fornecidas exclusivamente pelo SAP Service Marketplace em <https://support.sap.com/swdc>.
Informações gerais sobre como executar o SAP NetWeaver no SAP MaxDB podem ser encontradas em <https://www.sap.com/community/topic/maxdb.html>.

### <a name="supported-microsoft-windows-versions-and-azure-vm-types-for-sap-maxdb-dbms"></a>Versões do Microsoft Windows e tipos de VM do Azure com suporte para DBMS do SAP MaxDB
Para localizar a versão com suporte do Microsoft Windows para DBMS do SAP MaxDB no Azure, consulte:

* [PAM (Matriz de Disponibilidade de Produto) da SAP][sap-pam]
* Nota SAP [1928533]

É altamente recomendável usar a versão mais recente do sistema operacional Microsoft Windows, que é o Microsoft Windows 2016.

### <a name="available-sap-maxdb-documentation-for-maxdb"></a>Documentação do SAP MaxDB disponível para MaxDB
Você pode encontrar a lista atualizada da documentação do SAP MaxDB na seguinte Nota SAP [767598]

### <a name="sap-maxdb-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Diretrizes de configuração do SAP MaxDB para instalações do SAP em VMs do Azure
#### <a name="b48cfe3b-48e9-4f5b-a783-1d29155bd573"></a>Configuração de armazenamento
As práticas recomendadas de armazenamento do Azure para SAP MaxDB seguem as recomendações gerais mencionadas no capítulo [Estrutura de armazenamento de uma VM para Implantação do RDBMS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general#65fa79d6-a85f-47ee-890b-22e794f51a64).

> [!IMPORTANT]
> Como outros bancos de dados, o SAP MaxDB também tem arquivos de log e de dados. No entanto, na terminologia do SAP MaxDB o termo correto é "volume" (não "arquivo"). Por exemplo, há volumes de dados e volumes de log do SAP MaxDB. Não os confunda com volumes de disco do SO. 
> 
> 

Em resumo, você precisa:

* Se você usar contas do Armazenamento do Azure, defina a conta de armazenamento do Azure que contém os volumes de log e dados do SAP MaxDB (arquivos de log e dados) como **LRS (Armazenamento Local Redundante)**, conforme especificado em [Considerações para Máquinas Virtuais do Azure para implementação DBMS para carga de trabalho do SAP](dbms_guide_general.md).
* Separe o caminho de E/S para volumes de dados (arquivos de dados) do SAP MaxDB do caminho de E/S para volumes de log (arquivos de logs). Isso significa que os volumes de dados (arquivos de dados) do SAP MaxDB precisam ser instalado em uma unidade lógica e os volumes de log (arquivos de log) do SAP MaxDB precisam ser instalado em outra unidade lógica.
* Defina o tipo de cache adequado para cada disco, dependendo de se você o usa para volumes de log ou de dados do SAP MaxDB (arquivos de log e dados) e se você usa o Armazenamento Standard ou Premium do Azure, conforme descrito em [Considerações para Máquinas Virtuais do Azure para implementação DBMS para carga de trabalho do SAP](dbms_guide_general.md).
* Desde que a cota de IOPS por disco atenda aos requisitos, é possível armazenar todos os volumes de dados em um único disco montado e também armazenar todos os volumes de log do banco de dados em outro disco único montado.
* Se mais IOPS e/ou espaço forem necessários, é recomendável usar pools de armazenamento do Microsoft Windows (disponíveis apenas no Microsoft Windows Server 2012 e superior) para criar um grande dispositivo lógico em vários discos montados. Para mais detalhes, confira também [Considerações para implantação de DBMS de Máquinas Virtuais do Azure para a carga de trabalho SAP](dbms_guide_general.md). Essa abordagem simplifica a sobrecarga administrativa para gerenciar o espaço em disco e evita o esforço de distribuir manualmente os arquivos entre vários discos montados.
* é altamente recomendável usar o armazenamento Premium do Azure para implantações do MaxDB. 

![Configuração de referência da VM IaaS do Azure para DBMS do SAP MaxDB](./media/dbms_maxdb_deployment_guide/Simple_disk_structure_maxdb.PNG)


#### <a name="23c78d3b-ca5a-4e72-8a24-645d141a3f5d"></a>Backup e restauração
Ao implantar o SAP MaxDB no Azure, você deve examinar sua metodologia de backup. Mesmo se o sistema não for um sistema produtivo, o banco de dados SAP hospedado pelo SAP MaxDB deverá ser submetido a backup periodicamente. Como o armazenamento do Azure mantém três imagens, um backup agora é menos importante em termos de proteger seu sistema contra falhas de armazenamento e falhas administrativas ou operacionais mais importantes. A razão principal para manter um plano de backup e restauração adequado é para que você possa compensar erros lógicos ou manuais fornecendo funcionalidades de recuperação pontual. Portanto, o objetivo é usar backups para restaurar o banco de dados para um determinado momento ou usar os backups no Azure para propagar outro sistema copiando o banco de dados existente. 

A realização do backup e a restauração de um banco de dados no Azure funcionam da mesma maneira que para sistemas locais, então você pode usar ferramentas standard de backup/restauração do MaxDB SAP, que são descritas em um dos documentos da documentação do SAP MaxDB listados na Nota SAP [767598]. 

#### <a name="77cd2fbb-307e-4cbf-a65f-745553f72d2c"></a>Considerações de desempenho para backup e restauração
Como em implantações bare-metal, o desempenho de backup e restauração depende de quantos volumes podem ser lidos em paralelo e a taxa de transferência desses volumes. Portanto, é possível supor que:

* Quanto menor for o número de discos usados para armazenar dispositivos de banco de dados, menor será a taxa de transferência de leitura geral
* Quanto menos destinos (Diretórios de Faixa, discos) nos quais gravar o backup, menor é a taxa de transferência

Para aumentar o número de destinos nos quais gravar, existem duas opções que podem ser usadas, possivelmente em combinação, dependendo das suas necessidades:

* Dedicando volumes separados para backup
* Dividir o volume de destino de backup em vários discos montados para melhorar a taxa de transferência de IOPS nesse volume de disco distribuído
* Com dispositivos de disco lógico dedicados separados para:
  * Volumes de backup (ou seja, arquivos) do SAP MaxDB
  * Volumes de dados do SAP MaxDB (ou seja, arquivos)
  * Volumes de log do SAP MaxDB (ou seja, arquivos)

Distribuição de um volume em vários discos montados foi discutida anteriormente em [considerações para implantação de DBMS de máquinas virtuais do Azure para a carga de trabalho SAP](dbms_guide_general.md). 

#### <a name="f77c1436-9ad8-44fb-a331-8671342de818"></a>Outras considerações
Todas as outras áreas gerais, como o monitoramento do SAP ou Conjuntos de Disponibilidade do Azure se aplicam como descritas no documento [Considerações para a implantação DBMS de Máquinas Virtuais do Azure para carga de trabalho SAP](dbms_guide_general.md).  para implantações de VMs com o banco de dados do SAP MaxDB.
Outras configurações específicas do SAP MaxDB são transparentes para VMs do Azure e são descritas em diferentes documentos listados na Nota SAP [767598] e nestas Notas SAP:

* [826037] 
* [1139904]
* [1173395]

## <a name="specifics-for-sap-livecache-deployments-on-windows"></a>Informações específicas para implantações de SAP liveCache no Windows
### <a name="sap-livecache-version-support"></a>Suporte de versão do SAP liveCache
A versão mínima do SAP liveCache com suporte nas Máquinas Virtuais do Azure é **SAP LC/LCAPPS 10.0 SP 25**, incluindo **liveCache 7.9.08.31** e **LCA-Build 25**, lançado para **EhP 2 for SAP SCM 7.0** e versões posteriores.

### <a name="supported-microsoft-windows-versions-and-azure-vm-types-for-sap-livecache-dbms"></a>Versões do Microsoft Windows e tipos de VM do Azure com suporte para DBMS do SAP liveCache
Para localizar a versão com suporte do Microsoft Windows para SAP liveCache no Azure, consulte:

* [PAM (Matriz de Disponibilidade de Produto) da SAP][sap-pam]
* Nota SAP [1928533]

É altamente recomendável usar a versão mais recente do sistema operacional Microsoft Windows Server. 

### <a name="sap-livecache-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Diretrizes de configuração do SAP liveCache para instalações do SAP em VMs do Azure
#### <a name="recommended-azure-vm-types-for-livecache"></a>Tipos de VM do Azure recomendados para liveCache
Como SAP liveCache é um aplicativo que executa cálculos enormes, a quantidade e a velocidade de RAM e CPU têm uma grande influência no desempenho do SAP liveCache. 

Para os tipos de VM do Azure com suporte pelo SAP (Nota SAP [1928533]), todos os recursos de CPU virtual alocados para a máquina virtual são apoiados por recursos de CPU físicos dedicados do hipervisor. Não ocorre o excesso de provisionamento (e, portanto, nenhuma competição por recursos de CPU).

Da mesma forma, para todos os tipos de instância VM do Azure com suporte pelo SAP, a memória VM é 100% mapeada para a memória física; o excesso de provisionamento (sobreposição), por exemplo, não é usado.

Sob essa perspectiva, é altamente recomendável usar o Dv2 mais recente, Dv3, Ev3 e VMs da série M. A escolha de diferentes tipos de VM depende da memória necessária para liveCache e os recursos de CPU necessários. Como com todas as outras implantações de DBMS é aconselhável utilizar o armazenamento Premium do Azure para volumes críticos de desempenho.

#### <a name="storage-configuration-for-livecache-in-azure"></a>Configuração de armazenamento para o liveCache no Azure
Como o SAP liveCache se baseia na tecnologia SAP MaxDB, todas as práticas recomendadas de armazenamento do Azure mencionadas para o SAP MaxDB descritas neste documento são também são válidas para o SAP liveCache. 

#### <a name="dedicated-azure-vm-for-livecache-scenario"></a>Cenário de VM do Azure dedicada para o liveCache
Como o SAP liveCache usa intensivamente a potência computacional, para o uso produtivo é altamente recomendável implantar em uma máquina virtual do Azure dedicada. 

![VM do Azure dedicada para liveCache para caso de uso produtivo](./media/dbms_maxdb_deployment_guide/700-livecach-prod.PNG)


#### <a name="backup-and-restore-for-livecache-in-azure"></a>Backup e restauração para o liveCache no Azure
O backup e a restauração, incluindo considerações de desempenho, já estão descritos nos capítulos relevantes do SAP MaxDB. 

#### <a name="other-considerations"></a>Outras considerações
Todas as outras áreas gerais já estão descritas no capítulo relevante sobre o SAP MaxDB. 

## <a name="specifics-for-the-sap-content-server-deployment-on-windows-in-azure"></a>Informações específicas para a implantação SAP Content Server no Windows no Azure
O SAP Content Server é um componente baseado em servidor separado para armazenar conteúdos como documentos eletrônicos em diferentes formatos. O SAP Content Server é fornecido pelo desenvolvimento de tecnologia e deve ser usada entre aplicativos para todos os aplicativos SAP. Ele é instalado em um sistema separado. O conteúdo normal é o material de treinamento e a documentação do Depósito de Dados de Conhecimento ou desenhos técnicos provenientes do Sistema de Gerenciamento de Documentos do mySAP PLM. 

### <a name="sap-content-server-version-support-for-azure-vms"></a>Suporte de versão do SAP Content Server para VMs do Azure
No momento o SAP dá suporte ao:

* **SAP Content Server** com a versão **6.50 (e superiores)**
* **SAP MaxDB versão 7.9**
* **IIS (Servidor de informações da Internet) da Microsoft versão 8.0 (e posterior)**

É altamente recomendável usar a versão mais recente do SAP Content Server, e a versão mais recente do **IIS da Microsoft**. 

Verifique as versões mais recentes com suporte do SAP Content Server e do IIS da Microsoft no [PAM (Matriz de Disponibilidade de Produto) da SAP][sap-pam].

### <a name="supported-microsoft-windows-and-azure-vm-types-for-sap-content-server"></a>Tipos de VM do Azure e Microsoft Windows com suporte para o SAP Content Server
Para descobrir a versão com suporte do Windows para o SAP Content Server no Azure, consulte:

* [PAM (Matriz de Disponibilidade de Produto) da SAP][sap-pam]
* Nota SAP [1928533]

É altamente recomendável usar a versão mais recente do Microsoft Windows Server.

### <a name="sap-content-server-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Diretrizes de configuração do SAP Content Server para instalações do SAP em VMs do Azure
#### <a name="storage-configuration-for-content-server-in-azure"></a>Configuração de armazenamento para o servidor de conteúdo no Azure
Se você configurar o SAP Content Server para armazenar arquivos no banco de dados do SAP MaxDB, todas as práticas recomendadas de armazenamento do Azure mencionadas para o SAP MaxDB neste documento também serão válidas para o cenário do SAP Content Server. 

Se você configurar o SAP Content Server para armazenar arquivos no sistema de arquivos, será recomendável usar uma unidade lógica dedicada. O uso de Espaços de Armazenamento do Windows permite que você também aumente o tamanho do disco lógico e a taxa de transferência de IOPS, conforme descrito em [Considerações para a implantação DBMS de Máquinas Virtuais do Azure para carga de trabalho SAP](dbms_guide_general.md). 

#### <a name="sap-content-server-location"></a>Local do SAP Content Server
O SAP Content Server deve ser implantado na mesma região do Azure e VNET do Azure em que o sistema SAP está implantado. Você é livre para decidir se deseja implantar os componentes do SAP Content Server em uma VM do Azure dedicada ou na mesma VM em que o sistema SAP está sendo executado. 

![VM do Azure dedicada para SAP Content Server](./media/dbms_maxdb_deployment_guide/800-azure-vm-sap-content-server.png)


#### <a name="sap-cache-server-location"></a>Local do SAP Cache Server
O SAP Cache Server é um componente adicional com base em servidor para fornecer acesso a documentos (em cache) localmente. O SAP Cache Server armazena em cache os documentos de um SAP Content Server. Isso é para otimizar o tráfego de rede se documentos precisam ser recuperados de mais de uma vez de locais diferentes. A regra geral é que o SAP Cache Server precisa estar fisicamente próximo ao cliente que acessa o SAP Cache Server. 

Aqui você tem duas opções:

1. **O cliente é um sistema SAP de back-end** Se um sistema SAP de back-end estiver configurado para acessar o SAP Content Server, o sistema SAP será um cliente. Como o sistema SAP e o SAP Content Server são implantados na mesma região do Azure, no mesmo datacenter do Azure, eles estão fisicamente próximos. Portanto, não é necessário ter um SAP Cache Server dedicado. Os clientes da interface do usuário do SAP (GUI do SAP ou navegador da Web) acessam o sistema SAP diretamente e o sistema SAP recupera documentos do SAP Content Server.
2. **O cliente é um navegador da Web local** O SAP Content Server pode ser configurado para ser acessado diretamente pelo navegador da Web. Nesse caso, um navegador da Web em execução local é um cliente do SAP Content Server. O datacenter local e o datacenter do Azure são colocados em diferentes locais físicos (idealmente próximos entre si). Seu datacenter local é conectado ao Azure por meio do ExpressRoute ou VPN site a site do Azure. Embora as duas opções ofereçam uma conexão de rede VPN segura para o Azure, a conexão de rede site a site não oferece uma largura de banda de rede e SLA de latência entre o datacenter local e o datacenter do Azure. Para acelerar o acesso a documentos, você pode realizar uma das seguintes ações:
   1. Instale o SAP Cache Server local, próximo ao navegador da Web local (opção na figura abaixo)
   2. Configure a Azure ExpressRoute, que oferece uma conexão de rede dedicada de alta velocidade e baixa latência entre o datacenter local e o datacenter do Azure.

![Opção de instalação local do SAP Cache Server](./media/dbms_maxdb_deployment_guide/900-sap-cache-server-on-premises.png)
<a name="642f746c-e4d4-489d-bf63-73e80177a0a8"></a>

#### <a name="backup--restore"></a>Backup/restauração
Se você configurar o SAP Content Server para armazenar arquivos no banco de dados do SAP MaxDB, as considerações de desempenho e o procedimento de backup/restauração já estão descritas nos capítulos deste documento. 

Se você configura o SAP Content Server para armazenar arquivos no sistema de arquivos, uma opção é executar o backup/restauração manual da estrutura do arquivo inteiro na qual os documentos estão localizados. Semelhante ao backup/restauração do SAP MaxDB, é recomendável ter um volume de disco dedicado para fins de backup. 

#### <a name="other"></a>Outros
Outras configurações específicas do SAP Content Server são transparentes para VMs do Azure e são descritas em vários documentos e Notas SAP:

* <https://service.sap.com/contentserver> 
* Nota SAP [1619726]  
