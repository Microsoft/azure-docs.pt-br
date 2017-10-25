---
title: "Implantação de Máquinas Virtuais do Azure do DBMS para SAP NetWeaver | Microsoft Docs"
description: "Implantação de Máquinas Virtuais do Azure do DBMS para SAP NetWeaver"
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: 
author: MSSedusch
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: 5654dac7-4204-4387-b312-3d8b2898eb3a
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 11/08/2016
ms.author: sedusch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1046d32a0b4b6ede027ef1931314a188c64c94bb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-virtual-machines-dbms-deployment-for-sap-netweaver"></a>Implantação de Máquinas Virtuais do Azure do DBMS para SAP NetWeaver
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

[dr-guide-classic]:http://go.microsoft.com/fwlink/?LinkID=521971

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

[ha-guide-classic]:http://go.microsoft.com/fwlink/?LinkId=613056

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

[powershell-install-configure]:https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[resource-group-authoring-templates]:../../../resource-group-authoring-templates.md
[resource-group-overview]:../../../azure-resource-manager/resource-group-overview.md
[resource-groups-networking]:../../../virtual-network/resource-groups-networking.md
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
[storage-premium-storage-preview-portal]:../../../storage/common/storage-premium-storage.md
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
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/documentation/templates/sql-server-2014-alwayson-dsc/
[virtual-network-deploy-multinic-arm-cli]:../../../virtual-network/virtual-network-deploy-multinic-arm-cli.md
[virtual-network-deploy-multinic-arm-ps]:../../../virtual-network/virtual-network-deploy-multinic-arm-ps.md
[virtual-network-deploy-multinic-arm-template]:../../../virtual-network/virtual-network-deploy-multinic-arm-template.md
[virtual-networks-configure-vnet-to-vnet-connection]:../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md
[virtual-networks-create-vnet-arm-pportal]:../../../virtual-network/virtual-networks-create-vnet-arm-pportal.md
[virtual-networks-manage-dns-in-vnet]:../../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md
[virtual-networks-multiple-nics]:../../../virtual-network/virtual-networks-multiple-nics.md
[virtual-networks-nsg]:../../../virtual-network/virtual-networks-nsg.md
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

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Este guia faz parte da documentação sobre como implementar e implantar o software SAP no Microsoft Azure. Antes de ler este guia, leia o [Guia de planejamento e implementação][planning-guide]. Este documento aborda a implantação de vários RDBMS e produtos relacionados em combinação com o SAP em VMs (Máquinas Virtuais) do Microsoft Azure usando as funcionalidades de IaaS (Infraestrutura como serviço) do Azure.

O documento complementa a documentação de instalação do SAP e do SAP Notes, que representam os recursos primários para instalações e implantações de software SAP em determinadas plataformas.

## <a name="general-considerations"></a>Considerações gerais
Neste capítulo, são introduzidas considerações sobre a execução de sistemas DBMS relacionados ao SAP em VMs do Azure. Há algumas referências a sistemas DBMS específicos neste capítulo. Em vez disso, os sistemas DBMS específicos são abordados neste documento, após este capítulo.

### <a name="definitions-upfront"></a>Definições prévias
No decorrer do documento, usamos os termos a seguir:

* IaaS: infraestrutura como serviço.
* PaaS: plataforma como serviço.
* SaaS: software como serviço.
* Componente SAP: um aplicativo SAP individual como ECC, BW, Solution Manager ou EP.  Os componentes SAP podem ser baseados em tecnologias ABAP ou Java tradicionais ou em um aplicativo não baseado em NetWeaver, como o Business Objects.
* Ambiente SAP: um ou mais componentes SAP agrupados logicamente para executar uma função de negócios, como Desenvolvimento, QAS, Treinamento, DR ou Produção.
* Estrutura da SAP: refere-se à totalidade dos ativos SAP na estrutura de TI de um cliente. A estrutura da SAP inclui todos os ambientes de produção e de não produção.
* Sistema SAP: a combinação de camada DBMS e camada de aplicativo de, por exemplo, um sistema de desenvolvimento SAP ERP, sistema de teste SAP BW, sistema de produção SAP CRM etc. Em implantações do Azure não há suporte para dividir essas duas camadas entre local e Azure. Isso significa que um sistema SAP é implantado localmente ou no Azure. No entanto, você pode implantar os diferentes sistemas de uma estrutura da SAP no Azure ou de forma local. Por exemplo, você poderia implantar os sistemas de desenvolvimento e teste SAP CRM no Azure, mas o sistema de produção CRM SAP localmente.
* Implantação somente em nuvem: uma implantação em que a assinatura do Azure não está conectada por meio de uma conexão site a site ou de ExpressRoute à infraestrutura de rede local. Na documentação comum do Azure, esses tipos de implantações também são descritos como implantações 'Somente em nuvem'. As máquinas virtuais implantadas com esse método são acessadas por meio da Internet e pontos de extremidade da Internet públicos atribuídos às VMs no Azure. O AD (Active Directory) local e o DNS não se estendem ao Azure nesses tipos de implantações. Portanto, as VMs não fazem parte do Active Directory local. Observação: as implantações somente em nuvem neste documento são definidas como estruturas da SAP completas que estão sendo executadas exclusivamente no Azure sem extensão do Active Directory nem resolução de nomes do local para a nuvem pública. Não há suporte para configurações somente em nuvem para sistemas SAP de produção ou configurações nas quais o SAP STMS ou outros recursos locais precisem ser usados entre sistemas SAP hospedados no Azure e recursos locais.
* Entre instalações: descreve um cenário em que as VMs são implantadas em uma assinatura do Azure com conectividade site a site, multissite ou de ExpressRoute entre os datacenters locais e o Azure. Na documentação comum do Azure, esses tipos de implantações também são descritas como cenários entre instalações. O motivo para a conexão é estender domínios locais, Active Directory local e DNS local para o Azure. A estrutura local é estendida para os ativos do Azure da assinatura. Com esta extensão, as VMs podem ser parte do domínio local. Usuários de domínio do domínio local podem acessar os servidores e podem executar serviços nessas VMs (como serviços DBMS). A comunicação e resolução de nomes entre VMs implantadas de forma local e VMs implantadas no Azure são possíveis. Esperamos que este seja o cenário mais comum para a implantação de ativos SAP no Azure. Para obter mais informações, confira [este artigo][vpn-gateway-cross-premises-options] e [este][vpn-gateway-site-to-site-create].

> [!NOTE]
> Implantações entre instalações de sistemas SAP em que máquinas virtuais do Azure que executam sistemas SAP são membros de um domínio local têm suporte para sistemas SAP de produção. Configurações entre locais têm suporte para a implantação de partes ou estruturas da SAP completas no Azure. Até mesmo a execução da estrutura da SAP completa no Azure requer que essas VMs sejam parte do domínio local e ADS. Em versões anteriores da documentação, falamos sobre cenários de TI híbridos, em que o termo “híbrido” está enraizado no fato de que há uma conectividade entre instalações entre o local e o Azure. Nesse caso, ‘Híbrido’ também significa que as VMs no Azure são parte do Active Directory local.
> 
> 

Alguma documentação da Microsoft descreve cenários entre instalações de modo um pouco diferente, especialmente para configurações de HA do DBMS. No caso de documentos relacionados ao SAP, o cenário entre locais se resume a ter uma conectividade de site a site ou privada (ExpressRoute) e ao fato de que a estrutura da SAP é distribuída entre os locais e o Azure.

### <a name="resources"></a>Recursos
Os seguintes guias estão disponíveis para o tópico das implantações da SAP no Azure:

* [Planejamento e implementação de Máquinas Virtuais do Azure para SAP NetWeaver][planning-guide]
* [Implantação de Máquinas Virtuais do Azure para SAP NetWeaver][deployment-guide]
* [Implantação do DBMS de Máquinas Virtuais do Azure para SAP NetWeaver][dbms-guide]

As seguintes Notas do SAP estão relacionadas com o tópico do SAP no Azure:

| Número da observação | Title |
| --- | --- |
| [1928533] |Aplicativos SAP no Azure: tipos de VM do Azure e produtos com suporte |
| [2015553] |SAP no Microsoft Azure: pré-requisitos de suporte |
| [1999351] |Solução de problemas de monitoramento aprimorado do Azure para SAP |
| [2178632] |Métricas-chave de monitoramento para SAP no Microsoft Azure |
| [1409604] |Virtualização no Windows: monitoramento avançado |
| [2191498] |SAP no Linux com o Azure: monitoramento avançado |
| [2039619] |Aplicativos SAP no Microsoft Azure usando o Banco de Dados Oracle: versões e produtos com suporte |
| [2233094] |DB6: Aplicativos SAP no Azure usando o IBM DB2 para Linux, UNIX e Windows - informações adicionais |
| [2243692] |Linux na VM do Microsoft Azure (IaaS): problemas de licença SAP |
| [1984787] |SUSE LINUX Enterprise Server 12: notas de instalação |
| [2002167] |Red Hat Enterprise Linux 7.x: instalação e atualização |
| [2069760] |Atualização e instalação do SAP do Oracle Linux 7.x |
| [1597355] |Recomendação de troca de espaço para Linux |
| [2171857] |Oracle Database 12c – suporte do sistema de arquivos em Linux |
| [1114181] |Oracle Database 11g – suporte do sistema de arquivos em Linux |


Leia também a [Wiki de SCN](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes), que contém todas as Notas SAP para Linux.

Você deve ter um conhecimento prático sobre a arquitetura do Microsoft Azure e como as Máquinas Virtuais do Microsoft Azure são implantadas e operadas. Você pode encontrar mais informações em <https://azure.microsoft.com/documentation/>

> [!NOTE]
> **Não** estamos discutindo as ofertas de PaaS (Plataforma como Serviço) do Microsoft Azure da plataforma Microsoft Azure. Este artigo é sobre a execução de um DBMS (sistema de gerenciamento de banco de dados) em Máquinas Virtuais do Microsoft Azure (IaaS) exatamente como você executaria o DBMS em seus ambientes locais. As funcionalidades e recursos do banco de dados entre essas duas ofertas são muito diferentes e não devem ser misturados uns com os outros. Confira também: <https://azure.microsoft.com/services/sql-database/>
> 
> 

Uma vez que estamos discutindo IaaS, em geral, as instalação e a configuração do Windows, do Linux e do DBMS são essencialmente as mesmas que em qualquer máquina virtual ou computador bare metal que você instalaria localmente. No entanto, há algumas decisões de implementação de gerenciamento de sistema e arquitetura que são diferentes ao utilizar IaaS. A finalidade deste documento é explicar as diferenças de gerenciamento de sistema e de arquitetura específicas para as quais você deve estar preparado ao usar o IaaS.

Em geral, as áreas gerais de diferença discutidas neste documento são:

* Planejamento do layout adequado de VM/disco dos sistemas SAP para garantir que você tenha o layout de arquivo de dados adequado e possa obter IOPS suficientes para sua carga de trabalho.
* Considerações de rede ao usar IaaS.
* Recursos de banco de dados específico a serem usados para otimizar o layout do banco de dados.
* Considerações sobre backup e restauração no IaaS.
* Uso de diferentes tipos de imagens para implantação.
* Alta disponibilidade no Azure IaaS.

## <a name="65fa79d6-a85f-47ee-890b-22e794f51a64"></a>Estrutura de uma implantação do RDBMS
Para seguir este capítulo, é necessário compreender o que foi apresentado [neste][deployment-guide-3] capítulo do [Guia de Implantação][deployment-guide]. O conhecimento sobre as diferentes séries de VM e suas diferenças e as diferenças do Armazenamento Premium e Standard do Azure deve ser compreendido e conhecido antes de ler este capítulo.

Até março de 2015, os discos contendo um sistema operacional eram limitados a um tamanho de 127 GB. Essa limitação foi removida em março de 2015 (para obter informações, confira <https://azure.microsoft.com/blog/2015/03/25/azure-vm-os-drive-limit-octupled/>). A partir de então, discos contendo o sistema operacional podem ter o mesmo tamanho de qualquer outro disco. No entanto, ainda preferimos uma estrutura de implantação na qual o sistema operacional, o DBMS e eventuais binários SAP são separados dos arquivos de banco de dados. Portanto, esperamos que os sistemas SAP em execução em Máquinas Virtuais do Azure tenham a VM base (ou disco) instalada com o sistema operacional, os executáveis do sistema de gerenciamento de banco de dados e os executáveis SAP. Os arquivos de dados e de log do DBMS são armazenados no Armazenamento do Azure (Armazenamento Standard ou Premium) em discos separados e anexados como discos lógicos à VM da imagem do sistema operacional do Azure original. 

Dependendo do aproveitamento do Armazenamento Premium ou Standard do Azure (por exemplo, usando VMs da série GS ou DS), há outras cotas no Azure que estão documentadas [aqui(Linux)][virtual-machines-sizes-linux] e [aqui (Windows)][virtual-machines-sizes-windows]. Ao planejar seu layout de disco, você precisa encontrar o melhor equilíbrio das cotas para os seguintes itens:

* O número de arquivos de dados.
* O número de discos que contêm os arquivos.
* As cotas IOPS de um único disco.
* A taxa de transferência de dados por disco.
* O número de discos de dados adicionais possíveis por tamanho da VM.
* A taxa de transferência geral do armazenamento que uma VM pode fornecer.

O Azure impõe uma cota de IOPS por disco de dados. Essas cotas são diferentes para discos hospedados no Armazenamento Standard e no Armazenamento Premium do Azure. As latências de E/S também são muito diferentes entre os dois tipos de armazenamento, sendo que o Armazenamento Premium fornece fatores de melhores latências de E/S. Cada um dos diferentes tipos de VM tem um número limitado de discos de dados que você pode anexar. Outra restrição é que somente determinados tipos de VM podem aproveitar o Armazenamento Premium do Azure. Isso significa que a decisão para um determinado tipo de VM pode ser orientada não apenas pelos requisitos de CPU e memória, mas também pelo IOPS, pelos requisitos de taxa de transferência do disco e pela latência, que normalmente são dimensionados conforme o número de discos ou o tipo de discos de Armazenamento Premium. Especialmente com o Armazenamento Premium, o tamanho de um disco também pode ser determinado pelo número de IOPS e pela taxa de transferência que precisam ser atingidos em cada disco.

O fato de que a taxa IOPS geral, o número de discos montados e o tamanho da VM estão todos interligados pode fazer com que uma configuração do Azure de um sistema SAP seja diferente da sua implantação local. Os limites de IOPS por LUN geralmente são configuráveis em implantações locais. Enquanto com o Armazenamento do Azure esses limites são fixos ou como o Armazenamento Premium depende do tipo de disco. Assim como ocorre com implantações locais, vemos configurações de cliente de servidores de banco de dados que usam muitos volumes diferentes para executáveis especiais, como SAP e DBMS, ou volumes especiais para espaços de tabela ou bancos de dados temporários. Quando tal sistema local é movido para o Azure, isso pode levar a um desperdício de largura de banda IOPS em potencial devido ao desperdício de um disco para executáveis ou bancos de dados que executam pouco ou nenhum IOPS. Portanto, em VMs do Azure é recomendável que os executáveis do DBMS e SAP estejam instalados no disco do SO se possível.

O posicionamento de arquivos de banco de dados e arquivos de log e o tipo de Armazenamento do Azure usado devem ser definidos por requisitos de taxa de transferência, latência e IOPS. Para ter IOPS suficiente para o log de transações, você poderá ser forçado a aproveitar vários discos para o arquivo de log de transações ou usar um disco de Armazenamento Premium maior. Nesse caso, seria possível criar um RAID de software (por exemplo, pool de armazenamento do Windows para Windows ou MDADM ou LVM [Logical Volume Manager] para Linux) com os discos que conteria o log de transações.

- - -
> ![Windows][Logo_Windows] Windows
> 
> A unidade D:\ em uma VM do Azure é uma unidade não persistente que é apoiada por alguns discos locais no nó de computação do Azure. Como ela não é persistente, isso significa que as alterações feitas no conteúdo na unidade D:\ são perdidas quando a VM é reiniciada. Por "alterações", queremos dizer arquivos salvos, diretórios criados, aplicativos instalados etc.
> 
> ![Linux][Logo_Linux] Linux
> 
> As VMs do Azure do Linux montam automaticamente uma unidade em /mnt/resource, que é uma unidade não persistente apoiada por discos locais no nó de computação do Azure. Como ela não é persistente, isso significa que as alterações feitas no conteúdo em /mnt/resource são perdidas quando a VM é reinicializada. Por alterações, queremos dizer arquivos salvos, diretórios criados, aplicativos instalados etc.
> 
> 

- - -
Dependendo da série da VM do Azure, os discos locais no nó de computação mostram diferentes desempenhos, que podem ser categorizados como:

* A0-A7: desempenho muito limitado. Não pode ser usado para nada além do arquivo de paginação do Windows
* A8-A11: características de desempenho excelentes, com aproximadamente dez mil IOPS e taxa de transferência superior a 1 GB/s
* Série D: características de desempenho excelentes, com aproximadamente dez mil IOPS e taxa de transferência superior a 1 GB/s
* Série DS: características de desempenho excelentes, com aproximadamente dez mil IOPS e taxa de transferência superior a 1 GB/s
* Série G: características de desempenho excelentes, com aproximadamente dez mil IOPS e taxa de transferência superior a 1 GB/s
* Série GS: características de desempenho excelentes, com aproximadamente dez mil IOPS e taxa de transferência superior a 1 GB/s

As instruções acima estão sendo aplicadas aos tipos de VM que são certificados com a SAP. A série de VMs com IOPS e taxa de transferência excelentes se qualifica para o aproveitamento por alguns recursos do DBMS, como tempdb ou espaço de tabela temporário.

### <a name="c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f"></a>Cache de VMs e discos de dados
Quando criamos discos de dados por meio do portal ou quando montamos discos carregados em VMs, podemos escolher se o tráfego de E/S entre a VM e os discos no Armazenamento do Azure são armazenadas em cache. O Armazenamento Standard e Premium do Azure usam duas tecnologias diferentes para esse tipo de cache. Em ambos os casos, o cache em si terá o backup em disco feito nas mesmas unidades usadas pelo disco temporário (D:\ no Windows ou /mnt/resource no Linux) da VM.

Para o Armazenamento Standard do Azure, os possíveis tipos de cache são:

* Sem cache
* Cache de leitura
* Cache de leitura e gravação

Para obter um desempenho consistente e determinístico, você deve definir o cache no Armazenamento Standard do Azure para todos os discos contendo **Arquivos de dados relacionados ao DBMS, arquivos de log e espaço de tabela como ‘NONE’**. O caching da VM pode permanecer com o padrão.

Para o Armazenamento Premium do Azure, existem as seguintes opções de caching:

* Sem cache
* Cache de leitura

A recomendação para o Armazenamento Premium do Azure é aproveitar o **Cache de leitura para arquivos de dados** do banco de dados SAP e escolher **Sem cache para discos de arquivos de log**.

### <a name="c8e566f9-21b7-4457-9f7f-126036971a91"></a>RAID de software
Como já foi dito acima, você precisará equilibrar o número de IOPS necessários para os arquivos de banco de dados entre o número de discos que pode configurar e o máximo de IOPS que uma VM do Azure fornecerá por tipo de disco de Armazenamento Premium ou disco. A maneira mais fácil de lidar com a carga de IOPS em discos é criar um RAID de software nos diferentes discos. Em seguida, colocar um número de arquivos de dados do SAP DBMS no LUNS retirado do RAID de software. Dependendo dos requisitos, talvez você queira considerar o uso do Armazenamento Premium também, já que dois dos três discos diferentes do Armazenamento Premium fornecem uma cota IOPS maior que os discos baseados no Armazenamento Standard. Além da latência de E/S significativamente melhor fornecida pelo Armazenamento Premium do Azure. 

O mesmo se aplica ao log de transações dos diferentes sistemas DBMS. Com muitos deles apenas adicionando mais arquivos Tlog não ajuda, uma vez que os sistemas DBMS gravam em um dos arquivos de cada vez. Se forem necessárias taxas de IOPS mais altas do que um único disco baseado no Armazenamento Standard pode oferecer, você poderá distribuir entre vários discos de Armazenamento Standard ou poderá usar um tipo de disco de Armazenamento Premium maior que, além de taxas de IOPS mais altas, também ofereça latência menor de fatores para as E/Ss de gravação no log de transações.

As situações ocorridas em implantações do Azure que seriam beneficiadas pelo uso de um RAID de software são:

* O Log de Transações/Log de Refazer requerem mais IOPS do que o Azure fornece para um único disco. Conforme mencionado acima, isso pode ser resolvido com a criação de um LUN em vários discos usando um RAID de software.
* Distribuição de carga de trabalho de E/S desigual nos diferentes arquivos de dados do banco de dados SAP. Nesses casos, pode ocorrer de um arquivo de dados atingir a cota com bastante frequência. Enquanto outros arquivos de dados não estão nem mesmo se aproximando da cota IOPS de um único disco. Nesses casos, a solução mais fácil é criar um LUN em vários discos usando um RAID de software. 
* Você não sabe qual é a carga de trabalho de E/S exata por arquivo de dados e sabe apenas aproximadamente qual é a carga de trabalho de IOPS geral no DBMS. O mais fácil a se fazer é criar um LUN com a ajuda de um RAID de software. A soma das cotas de vários discos por trás desse LUN deve atender à taxa de IOPS conhecida.

- - -
> ![Windows][Logo_Windows] Windows
> 
> É recomendável usar Espaços de Armazenamento do Windows se a execução for feita em Windows Server 2012 ou superior. É mais eficiente do que a Divisão do Windows de versões anteriores do Windows. Esteja ciente de que você pode precisar criar os Pools de Armazenamento do Windows e os Espaços de Armazenamento por comandos do PowerShell ao usar o Windows Server 2012 como sistema operacional. Os comandos do PowerShell podem ser encontrados aqui <https://technet.microsoft.com/library/jj851254.aspx>
> 
> ![Linux][Logo_Linux] Linux
> 
> Há suporte apenas para MDADM e LVM (Logical Volume Manager) para criar um RAID de software no Linux. Para obter mais informações, leia os seguintes artigos:
> 
> * [Configurar RAID de software no Linux][virtual-machines-linux-configure-raid] (para MDADM)
> * [Configurar o LVM em uma VM Linux no Azure][virtual-machines-linux-configure-lvm]
> 
> 

- - -
As considerações para aproveitar a série de VM que funciona com o Armazenamento Premium do Azure geralmente são:

* Demandas de latências de E/S que estão próximas às que os dispositivos SAN/NAS fornecem.
* Demanda de fatores de melhores latências de E/S que o Armazenamento Standard do Azure pode fornecer.
* Maior IOPS por VM do que o que pode ser obtido com vários discos de Armazenamento Standard em um determinado tipo de VM.

Como o Armazenamento do Azure subjacente replica cada disco para pelo menos três nós de armazenamento, é possível usar a divisão de RAID 0 simples. Não há nenhuma necessidade de implementar o RAID5 ou o RAID1.

### <a name="10b041ef-c177-498a-93ed-44b3441ab152"></a>Armazenamento do Microsoft Azure
O Armazenamento do Microsoft Azure armazena a VM de base (com o SO) e os discos ou BLOBs em pelo menos três nós de armazenamento separados. Ao criar uma conta de armazenamento ou disco gerenciado, há uma opção de proteção, conforme mostrado aqui:

![Replicação geográfica habilitada para a conta de armazenamento do Azure][dbms-guide-figure-100]

A Replicação Local do Armazenamento do Azure (localmente redundante) fornece níveis de proteção contra a perda de dados devido à falha de infraestrutura que alguns clientes podem ter condições de implantar. Conforme mostrado acima, há quatro opções diferentes, com uma quinta sendo uma variação de uma das três primeiras. Analisando-as mais de perto, podemos distinguir:

* **LRS (Armazenamento com Redundância Local) Premium**: o Armazenamento Premium do Azure fornece alto desempenho, dá suporte a disco de baixa latência para máquinas virtuais executando cargas de trabalho intensas de E/S. Há três réplicas dos dados dentro do mesmo datacenter do Azure de uma região do Azure. As cópias estão em diferentes Domínios de Falha e de Atualização (para conhecer os conceitos, veja [este][planning-guide-3.2] capítulo no [Guia de planejamento][planning-guide]). No caso de uma réplica dos dados sendo retirada de serviço devido a uma falha de nó de armazenamento ou a falha de disco, uma nova réplica é gerada automaticamente.
* **LRS (Armazenamento com Redundância Local)**: nesse caso, há três réplicas dos dados dentro do mesmo datacenter do Azure de uma região do Azure. As cópias estão em diferentes Domínios de Falha e de Atualização (para conhecer os conceitos, veja [este][planning-guide-3.2] capítulo no [Guia de planejamento][planning-guide]). No caso de uma réplica dos dados sendo retirada de serviço devido a uma falha de nó de armazenamento ou a falha de disco, uma nova réplica é gerada automaticamente. 
* **GRS (Armazenamento de Redundância Geográfica)**: nesse caso, há uma replicação assíncrona que alimentará três réplicas adicionais dos dados em outra região do Azure, que, na maioria dos casos, está na mesma região geográfica (como Europa Setentrional e Europa Ocidental). Isso resulta em três réplicas adicionais, de modo que há seis réplicas no total. Uma variação disso é uma adição em que os dados na região do Azure replicada geograficamente podem ser usados para fins de leitura (Redundância Geográfica com Acesso de Leitura).
* **ZRS (Armazenamento com Redundância de Zona)**: nesse caso, as três réplicas dos dados permanecem na mesma região do Azure. Conforme explicado [neste][planning-guide-3.1] capítulo do [Guia de Planejamento][planning-guide], uma região do Azure pode ser um número de datacenters próximos uns dos outros. No caso do LRS, as réplicas devem ser distribuídas em diferentes datacenters que compõem uma região do Azure.

Mais informações podem ser encontradas [aqui][storage-redundancy].

> [!NOTE]
> Para implantações de DBMS, o uso do Armazenamento de Redundância Geográfica não é recomendado
> 
> A replicação geográfica do Armazenamento do Azure é assíncrona. A replicação de discos individuais montados em uma única VM não é sincronizada na etapa de bloqueio. Portanto, não é adequado replicar arquivos do DBMS que são distribuídos em diferentes discos ou implantados em um RAID de software com base em vários discos. O software DBMS requer que o armazenamento em disco persistente seja sincronizado com precisão em LUNs diferentes e discos subjacentes/eixo. O software DBMS usa vários mecanismos para sequenciar atividades de gravação de E/S e um DBMS relatará que o armazenamento de disco alvo da replicação está corrompido se variarem sequer por alguns milissegundos. Portanto, se você realmente quiser uma configuração de banco de dados com um banco de dados estendido por vários discos com replicação geográfica, tal replicação precisará ser executada com meios e funcionalidade de banco de dados. Não se deve confiar na replicação geográfica do Armazenamento do Azure para realizar esse trabalho. 
> 
> O problema é mais simples de ser explicado com um sistema de exemplo. Vamos supor que você tenha um sistema SAP carregado no Azure com oito discos contendo arquivos de dados do DBMS, mais um disco contendo o arquivo de log de transações. Cada um desses nove discos tem dados gravados neles em um método consistente de acordo com o DBMS, quer os dados estejam sendo gravados em arquivos de log de transações ou de dados.
> 
> Para replicar geograficamente de forma correta os dados e manter uma imagem de banco de dados consistente, o conteúdo de todos os nove discos precisaria ser replicado geograficamente na ordem exata em que as operações de E/S foram executadas em relação aos nove discos diferentes. No entanto, a replicação geográfica do Armazenamento do Azure não permite declarar dependências entre discos. Isso significa que a replicação geográfica do Armazenamento do Microsoft Azure não conhece o fato de que os conteúdos desses nove discos diferentes estão relacionados uns aos outros e que as alterações de dados são consistentes apenas ao replicar na ordem em que as operações de E/S ocorreu entre todos os nove discos.
> 
> Além de serem altas as chances de as imagens replicadas geograficamente no cenário não fornecerem uma imagem consistente do banco de dados, também há uma penalidade de desempenho que surge com o armazenamento de redundância geográfica que pode afetar seriamente o desempenho. Em resumo, não use esse tipo de redundância de armazenamento para cargas de trabalho do tipo DBMS.
> 
> 

#### <a name="mapping-vhds-into-azure-virtual-machine-service-storage-accounts"></a>Mapeando VHDs em contas de armazenamento do serviço de máquina virtual do Azure
Este capítulo só se aplica a Contas de Armazenamento do Azure. Se você planeja usar Managed Disks, as limitações mencionadas neste capítulo não se aplicarão. Para obter mais informações sobre Managed Disks, leia o capítulo [Managed Disks][dbms-guide-managed-disks] deste guia.

Uma Conta de Armazenamento do Azure não é apenas um constructo administrativo, mas também uma entidade de limitações. Enquanto as limitações variam se falamos de uma Conta de Armazenamento Standard do Azure ou uma Conta de Armazenamento Premium do Azure. As funcionalidades e limitações exatas são listadas [aqui][storage-scalability-targets]

Portanto, para o Armazenamento Standard do Azure, é importante observar que há um limite no IOPS por conta de armazenamento (linha contendo “Taxa de Solicitação Total” [no artigo][storage-scalability-targets]). Além disso, há um limite inicial de 100 contas de armazenamento por assinatura do Azure (a partir de julho de 2015). Portanto, é recomendável equilibrar o IOPS de VMs entre várias contas de armazenamento ao usar o Armazenamento Standard do Azure. Enquanto uma única VM idealmente usa uma conta de armazenamento se possível. Então, se falamos sobre implantações de DBMS em que cada VHD que é hospedado no Armazenamento Standard do Azure pode atingir seu limite de cota, você deve implantar apenas 30 a 40 VHDs por Conta de Armazenamento do Azure que usa o Armazenamento Standard do Azure. Por outro lado, se você utiliza o Armazenamento Premium do Azure e deseja armazenar grandes volumes de banco de dados, pode estar bem em termos de IOPS. Mas uma Conta de Armazenamento Premium do Azure é muito mais restritiva no volume de dados do que uma Conta de Armazenamento Standard do Azure. Como resultado, você pode implantar apenas um número limitado de VHDs em uma Conta de Armazenamento Premium do Azure antes de atingir o limite do volume de dados. No fim, pense em uma Conta de Armazenamento do Azure como uma “SAN virtual” com funcionalidades limitadas no IOPS e/ou capacidade. Como resultado, a tarefa permanece, como em implantações locais, para definir o layout dos VHDs dos diferentes sistemas SAP nos diferentes 'dispositivos SAN imaginários' ou Contas de Armazenamento do Azure.

Para o Armazenamento Standard do Azure, não é recomendável apresentar o armazenamento de diferentes contas de armazenamento para uma única VM, se possível.

Ao usar a série DS ou GS de VMs do Azure, é possível montar VHDs de Contas de Armazenamento Standard e Contas de Armazenamento Premium do Azure. Casos de uso como a gravação de backups em VHDs apoiados pelo Armazenamento Standard e ter dados DBMS e arquivos de log no Armazenamento Premium vêm à mente quando esse armazenamento heterogêneo poderia ser aproveitado. 

Com base em implantações de clientes e testes, em torno de 30 a 40 VHDs contendo arquivos de dados do banco de dados e arquivos de log podem ser provisionados em uma única Conta de Armazenamento Standard do Azure. Como mencionado anteriormente, a limitação de uma Conta de Armazenamento Premium do Azure provavelmente é a capacidade de dados que ela pode conter, não o IOPS.

Como com dispositivos locais SAN, o compartilhamento requer algum monitoramento para, eventualmente, detectar afunilamentos em uma Conta de Armazenamento do Azure. A extensão de monitoramento do Azure para SAP e o portal do Azure são ferramentas que podem ser usadas para detectar Contas de Armazenamento do Azure ocupadas que podem estar gerando um desempenho de E/S abaixo do ideal.  Se essa situação for detectada, será recomendável mover as VMs ocupadas para outra Conta de Armazenamento do Azure. Consulte o [Guia de Implantação][deployment-guide] para obter detalhes sobre como ativar as funcionalidades de monitoramento de host do SAP.

Outro artigo que resume as práticas recomendadas em relação ao Armazenamento Standard e às Contas de Armazenamento Standard do Azure pode ser encontrado aqui <https://blogs.msdn.com/b/mast/archive/2014/10/14/configuring-azure-virtual-machines-for-optimal-storage-performance.aspx>

#### <a name="f42c6cb5-d563-484d-9667-b07ae51bce29"></a>Managed Disks
Managed Disks são um novo tipo de recurso no Azure Resource Manager que pode ser usado em vez de VHDs armazenados em Contas de Armazenamento do Azure. Managed Disks alinham-se automaticamente ao Conjunto de Disponibilidade da máquina virtual à qual eles estão conectados e, portanto, aumentam a disponibilidade da sua máquina virtual e dos serviços que estão em execução na máquina virtual. Para saber mais, leia o [artigo de visão geral](https://docs.microsoft.com/azure/storage/storage-managed-disks-overview).

A SAP atualmente dá suporte apenas a Managed Disks Premium. Leia a Nota SAP [1928533] para obter mais detalhes.

#### <a name="moving-deployed-dbms-vms-from-azure-standard-storage-to-azure-premium-storage"></a>Movendo VMs DBMS implantadas do Armazenamento Standard do Azure para o Armazenamento Premium do Azure
Podemos encontrar alguns cenários em que você como cliente deseja mover uma VM implantada do Armazenamento Standard do Azure para o Armazenamento Premium do Azure. Se os discos forem armazenados em contas de armazenamento do Azure, isso não será possível sem mover os dados fisicamente. Há várias maneiras atingir o objetivo:

* Você pode simplesmente copiar todos os VHDs, VHD base, bem como VHDs de dados em uma nova Conta de Armazenamento Premium do Azure. Com frequência, você escolhe o número de VHDs no Armazenamento Standard do Azure não porque precisa do volume de dados. Mas precisava daquela quantidade de VHDs por causa do IOPS. Agora que você passou para o Armazenamento Premium do Azure, pode usar muito menos VHDs para obter a mesma taxa de transferência de IOPS. Devido ao fato de que no Armazenamento Standard do Azure você paga pelos dados usados e não pelo tamanho do disco nominal, o número de VHDs realmente não importava em termos de custos. No entanto, com o Armazenamento Premium do Azure, você pagaria pelo tamanho do disco nominal. Portanto, a maioria dos clientes tenta manter o número de VHDs do Azure no Armazenamento Premium no número necessário para alcançar a taxa de transferência de IOPS necessária. Dessa forma, a maioria dos clientes decide contra a forma de uma cópia simples de 1:1.
* Se ainda não estiver montado, você montará um único VHD que pode conter um backup de banco de dados do seu banco de dados SAP. Depois do backup, você desmonta todos os VHDs, incluindo o VHD que contém o backup e copia o VHD base e o VHD com o backup em uma Conta de Armazenamento Premium do Azure. Em seguida, você deve implantar a VM com base no VHD base e montar o VHD com o backup. Agora você cria discos de Armazenamento Premium vazios adicionais para a VM que são usados como o local da restauração do banco de dados. Isso pressupõe que o DBMS permite alterar os caminhos para os arquivos de log e de dados como parte do processo de restauração.
* Outra possibilidade é uma variação do processo anterior, em que você simplesmente copia o VHD de backup no Armazenamento Premium do Azure e o anexa em uma VM que recentemente implantou e instalou.
* A quarta possibilidade você escolhe quando precisa alterar o número de arquivos de dados do seu banco de dados. Nesse caso, você executaria uma cópia homogênea do sistema SAP usando a exportação/importação. Coloque os arquivos da exportação em um VHD que é copiado para uma Conta de Armazenamento Premium do Azure e anexe-o a uma VM usada para executar os processos de importação. Os clientes usam essa possibilidade principalmente quando desejam diminuir o número de arquivos de dados.

Se você usar Managed Disks, poderá migrar para o Armazenamento Premium:

1. Desalocar a máquina virtual
2. Se necessário, redimensione a máquina virtual para um tamanho que dê suporte ao Armazenamento Premium (por exemplo, DS ou GS)
3. Altere o tipo de conta do Disco Gerenciado para Premium (SSD)
4. Inicie a máquina virtual

### <a name="deployment-of-vms-for-sap-in-azure"></a>Implantação de VMs para SAP no Azure
O Microsoft Azure oferece várias maneiras de implantar VMs e discos associados. Assim, é importante entender as diferenças, uma vez que as preparações das VMs podem ser diferentes dependendo da forma de implantação. Em geral, analisamos os cenários descritos nos capítulos a seguir.

#### <a name="deploying-a-vm-from-the-azure-marketplace"></a>Implantando uma VM do Azure Marketplace
Você gostaria de usar uma imagem fornecida pela Microsoft ou por terceiros no Azure Marketplace para implantar a VM. Depois de implantar a VM no Azure, siga as mesmas diretrizes e ferramentas para instalar o software SAP na VM, como você faria em um ambiente local. Para instalar o software SAP na VM do Azure, a SAP e a Microsoft recomendam carregar e armazenar a mídia de instalação do SAP em discos de mídia ou criar uma VM do Azure funcionando como um "Servidor de arquivos" contendo todas as mídias de instalação SAP necessárias.

#### <a name="deploying-a-vm-with-a-customer-specific-generalized-image"></a>Como implantar uma VM com uma imagem generalizada específica do cliente
Devido a requisitos de patch específicos da sua versão do SO ou do DBMS, as imagens fornecidas no Azure Marketplace podem não atender às suas necessidades. Portanto, você precisará criar uma VM usando sua própria imagem “privada” de VM do SO/DBMS, que poderá ser implantada várias vezes depois. Para preparar essa imagem “privada” para duplicação, o SO deve ser generalizado na VM local. Consulte o [Guia de implantação][deployment-guide] para obter detalhes sobre como generalizar uma VM.

Se já tiver instalado o conteúdo do SAP na VM local (especialmente para sistemas de duas camadas), você poderá adaptar as configurações do sistema SAP após a implantação da VM do Azure por meio do procedimento de renomeação da instância ao qual o Software Provisioning Manager SAP dá suporte (Nota do SAP [1619720]). Caso contrário, você pode instalar o software SAP posteriormente, após a implantação da VM do Azure.

Por meio do conteúdo do banco de dados usado pelo aplicativo SAP, você pode gerar o conteúdo recentemente por uma instalação do SAP ou pode importar seu conteúdo para o Azure usando um VHD com um backup de banco de dados DBMS ou aproveitando os recursos do DBMS para fazer o backup diretamente no Armazenamento do Microsoft Azure. Nesse caso, você também pode preparar VHDs com os arquivos de log e de dados do DBMS locais e, em seguida, importá-los como discos no Azure. Mas a transferência de dados do DBMS que estão sendo carregados do local para o Azure funcionaria em discos VHD que precisassem ser preparados localmente.

#### <a name="moving-a-vm-from-on-premises-to-azure-with-a-non-generalized-disk"></a>Movendo uma VM do local para o Azure com um disco não generalizado
Você planeja mover um sistema SAP específico do local para o Azure (lift and shift). Isso pode ser feito carregando o disco que contém o SO, os binários SAP e eventuais binários DBMS, mais os discos com os arquivos de log e de dados do DBMS para o Azure. Ao contrário do cenário nº 2 acima, você mantém o nome de host, a SID da SAP e as contas de usuário SAP na VM do Azure, já que elas foram configuradas no ambiente local. Portanto, não é necessário generalizar a imagem. Esse caso se aplicará principalmente a cenários Entre Instalações em que uma parte da estrutura SAP é executada localmente e outra parte, no Azure.

## <a name="871dfc27-e509-4222-9370-ab1de77021c3"></a>Alta disponibilidade e recuperação de desastre com VMs do Azure
O Azure oferece as seguintes funcionalidades de HA (Alta Disponibilidade) e DR (Recuperação de Desastre) que se aplicam a diferentes componentes que usaríamos para implantações SAP e DBMS

### <a name="vms-deployed-on-azure-nodes"></a>VMs implantadas em nós do Azure
A plataforma Azure não oferece recursos como Migração ao Vivo para VMs implantadas. Isso significa que se for necessária a manutenção em um cluster de servidores no qual uma VM está implantada, a VM precisará ser parada e reiniciada. A manutenção no Azure é realizada usando os chamados domínios de atualização em clusters de servidores. A manutenção é realizada em apenas um domínio de atualização de cada vez. Durante tal reinicialização há uma interrupção de serviço enquanto a VM está desligada, a manutenção é realizada e a VM é reiniciada. No entanto, a maioria dos fornecedores do DBMS oferece a funcionalidade de Alta Disponibilidade e Recuperação de Desastre que reinicia rapidamente os serviços do DBMS em outro nó quando o nó primário não está disponível. A Plataforma do Azure oferece a funcionalidade de distribuir as VMs, Armazenamento e outros serviços do Azure entre Domínios de Atualização para garantir que falhas de infraestrutura ou manutenção planejada afetem apenas um pequeno subconjunto de VMs ou serviços.  Com um planejamento cuidadoso é possível alcançar níveis de disponibilidade comparáveis aos de infraestruturas locais.

Os Conjuntos de Disponibilidade do Microsoft Azure são agrupamentos lógicos de VMs ou de Serviços que garantem que as VMs e os outros serviços sejam distribuídos em diferentes Domínios de Falha e de Atualização em um cluster de modo que haja apenas um desligamento de nó em um determinado momento (leia [este artigo (Linux)][virtual-machines-manage-availability-linux] ou [este (Windows)][virtual-machines-manage-availability-windows] para obter mais detalhes).

Eles precisam ser configurados por finalidade ao distribuir VMs, como visto aqui:

![Definição de conjunto de disponibilidade para configurações de HA do DBMS][dbms-guide-figure-200]

Se quisermos criar configurações de alta disponibilidade de implantações de DBMS (independentemente da funcionalidade de HA do DBMS utilizada), as VMs de DBMS precisarão:

* Adicione as VMs à mesma Rede Virtual do Azure (<https://azure.microsoft.com/documentation/services/virtual-network/>)
* As VMs da configuração de HA também devem estar na mesma sub-rede. A resolução de nomes entre as diferentes sub-redes não é possível em implantações Somente em Nuvem, apenas a resolução de IP funciona. Usando a conectividade de ExpressRoute ou site a site para implantações Entre Instalações, uma rede com pelo menos uma sub-rede já está estabelecida. A resolução de nomes é feita de acordo com a infraestrutura de rede e as políticas do AD locais. 

[comment]: <> (Teste TODO MSSedusch se ainda for verdadeiro no ARM)

#### <a name="ip-addresses"></a>Endereços IP
É altamente recomendável definir as VMs para configurações de HA de forma resiliente. Confiar em endereços IP para endereçar os parceiros de HA na configuração de HA não é confiável no Azure a menos que sejam usados endereços IP estáticos. Há dois conceitos de "Desligamento" no Azure:

* Desligamento por meio do portal do Azure ou do cmdlet Stop-AzureRmVM do Azure PowerShell: neste caso a máquina virtual é desligada e desalocada. Sua conta do Azure não será mais cobrada por essa VM, de modo os encargos incorridos sejam apenas para o armazenamento usado. No entanto, se o endereço IP privado da interface da rede não for estático, o endereço IP será liberado e não haverá garantia de que a interface de rede obterá o endereço IP antigo atribuído novamente após a reinicialização da VM. Realizar o desligamento por meio do portal do Azure ou chamar Stop-AzureRmVM automaticamente causa desalocação. Se você não quiser desalocar o computador, use Stop-AzureRmVM -StayProvisioned 
* Se você desligar a VM de um nível de SO, a VM será desligada e NÃO desalocada. No entanto, neste caso, sua conta do Azure ainda será cobrada pela VM, embora ela esteja desligada. Nesse caso, a atribuição do endereço IP a uma VM parada permanecerá intacta. Desligar a VM de dentro não força a desalocação automaticamente.

Mesmo para cenários Entre Instalações, por padrão, um desligamento e uma desalocação significam a desatribuição dos endereços IP da VM, mesmo que as políticas locais no DHCP sejam diferentes. 

* Uma exceção é quando ocorre a atribuição de um endereço IP estático a um adaptador de rede, conforme descrito [aqui][virtual-networks-reserved-private-ip].
* Nesse caso o endereço IP permanece fixo contanto que a interface de rede não seja excluída.

> [!IMPORTANT]
> Para manter toda a implantação simples e gerenciável, a recomendação clara é configurar a parceria de VMs em uma configuração de HA ou DR de DBMS no Azure de uma maneira que haja uma resolução de nome funcionando entre as diferentes VMs envolvidas.
> 
> 

## <a name="deployment-of-host-monitoring"></a>Implantação do monitoramento de host
Para o uso produtivo de aplicativos SAP nas máquinas virtuais do Azure, o SAP requer a capacidade de obter dados de monitoramento dos hosts físicos executando as máquinas virtuais do Azure. É necessário um nível de patch do Agente de Host SAP específico que habilite essa funcionalidade no SAPOSCOL e no Agente de Host SAP. O nível de patch exato está documentado na Nota SAP [1409604].

Para obter detalhes sobre a implantação de componentes que fornecem dados de host ao SAPOSCOL e ao Agente de Host SAP, bem como sobre o gerenciamento de ciclo de vida desses componentes, consulte o [Guia de implantação][deployment-guide]

## <a name="3264829e-075e-4d25-966e-a49dad878737"></a>Informações específicas para o Microsoft SQL Server
### <a name="sql-server-iaas"></a>IaaS do SQL Server
Começando com o Microsoft Azure, você pode facilmente migrar seus aplicativos existentes do SQL Server criados na plataforma Windows Server para máquinas virtuais do Azure. O SQL Server em uma Máquina Virtual permite reduzir o custo total de propriedade de implantação, gerenciamento e manutenção de aplicativos de amplitude empresarial migrando facilmente esses aplicativos para o Microsoft Azure. Com o SQL Server em uma máquina virtual do Azure, os administradores e desenvolvedores ainda podem usar as mesmas ferramentas de desenvolvimento e administração que estão disponíveis localmente. 

> [!IMPORTANT]
> Não estamos discutindo o Banco de Dados SQL do Microsoft Azure, que é uma oferta de Plataforma como Serviço da Plataforma Microsoft Azure. A discussão neste artigo é sobre a execução do produto SQL Server como é conhecido para implantações locais nas máquinas virtuais do Azure, aproveitando a funcionalidade de Infraestrutura como Serviço do Azure. As funcionalidades e recursos do banco de dados entre essas duas ofertas são muito diferentes e não devem ser misturados entre si. Confira também: <https://azure.microsoft.com/services/sql-database/>
> 
> 

É altamente recomendável examinar [esta][virtual-machines-sql-server-infrastructure-services] documentação antes de continuar.

Nas seções a seguir, trechos de partes da documentação no link acima são agregados e mencionados. Informações especificas do SAP também são mencionadas e alguns conceitos são descritos mais detalhadamente. No entanto, é altamente recomendável percorrer a documentação acima primeiro antes de ler a documentação específica do SQL Server.

Há algumas informações específicas do SQL Server no IaaS que você deve conhecer antes de continuar:

* **SLA da Máquina Virtual**: há um SLA para Máquinas Virtuais em execução no Azure, que pode ser encontrado aqui: <https://azure.microsoft.com/support/legal/sla/>  
* **Suporte de versão do SQL**: para clientes SAP, damos suporte para SQL Server 2008 R2 e superior na máquina virtual do Microsoft Azure. Não há suporte para edições anteriores. Examine esta [Instrução de suporte](https://support.microsoft.com/kb/956893) geral para obter mais detalhes. Observe que, em geral, o SQL Server 2008 também tem suporte da Microsoft. No entanto, devido à funcionalidade significativa para SAP que foi introduzida com o SQL Server 2008 R2, o SQL Server 2008 R2 é a versão mínima para SAP. Tenha em mente que o SQL Server 2012 e 2014 foram estendidos com uma maior integração com o cenário de IaaS (como a realização do backup diretamente no Armazenamento do Azure). Portanto, nós restringimos este documento para SQL Server 2012 e 2014 com seu nível de patch mais recente para o Azure.
* **Suporte ao recurso de SQL**: a maioria dos recursos do SQL Server tem suporte em Máquinas Virtuais do Microsoft Azure com algumas exceções. **Não há suporte para o clustering de failover do SQL Server usando discos compartilhados**.  Tecnologias distribuídas, como o espelhamento de banco de dados, grupos de disponibilidade AlwaysOn, replicação, envio de logs e Service Broker, têm suporte dentro de uma única região do Azure. O AlwaysOn do SQL Server também tem suporte entre diferentes regiões do Azure, conforme documentado aqui: <https://blogs.technet.com/b/dataplatforminsider/archive/2014/06/19/sql-server-alwayson-availability-groups-supported-between-microsoft-azure-regions.aspx>.  Examine esta [Instrução de suporte](https://support.microsoft.com/kb/956893) para obter mais detalhes. Um exemplo de como implantar uma configuração AlwaysOn é mostrado [neste][virtual-machines-workload-template-sql-alwayson] artigo. Além disso, confira as práticas recomendadas documentadas [aqui][virtual-machines-sql-server-infrastructure-services] 
* **Desempenho do SQL**: temos certeza de que as Máquinas Virtuais hospedadas do Microsoft Azure têm um ótimo desempenho em comparação com outras ofertas de virtualização de nuvem pública, mas os resultados individuais podem variar. Confira [este][virtual-machines-sql-server-performance-best-practices] artigo.
* **Uso de imagens do Azure Marketplace**: a maneira mais rápida de implantar uma nova VM do Microsoft Azure é usar uma imagem do Azure Marketplace. Há imagens no Azure Marketplace que contêm o SQL Server. As imagens em que o SQL Server já está instalado não podem ser usadas imediatamente para aplicativos SAP NetWeaver. O motivo é que o agrupamento do SQL Server padrão é instalado dentro dessas imagens e não o agrupamento exigido pelos sistemas SAP NetWeaver. Para usar essas imagens, verifique as etapas documentadas no capítulo [Como usar uma imagem do SQL Server fora do Microsoft Azure Marketplace][dbms-guide-5.6]. 
* Consulte os [Detalhes de preço](https://azure.microsoft.com/pricing/) para obter mais informações. O [Guia de licenciamento do SQL Server 2012](https://download.microsoft.com/download/7/3/C/73CAD4E0-D0B5-4BE5-AB49-D5B886A5AE00/SQL_Server_2012_Licensing_Reference_Guide.pdf) e o [Guia de licenciamento do SQL Server 2014](https://download.microsoft.com/download/B/4/E/B4E604D9-9D38-4BBA-A927-56E4C872E41C/SQL_Server_2014_Licensing_Guide.pdf) também são recursos importantes.

### <a name="sql-server-configuration-guidelines-for-sap-related-sql-server-installations-in-azure-vms"></a>Diretrizes de configuração do SQL Server para instalações do SQL Server relacionadas ao SAP em VMs do Azure
#### <a name="recommendations-on-vmvhd-structure-for-sap-related-sql-server-deployments"></a>Recomendações sobre a estrutura de VM/VHD para implantações do SQL Server relacionadas ao SAP
De acordo com a descrição geral, os executáveis do SQL Server devem ser localizados ou instalados na unidade de sistema do disco do SO da VM (unidade C:\).  Normalmente, a maioria dos bancos de dados de sistema do SQL Server não é utilizada em um alto nível pela carga de trabalho do SAP NetWeaver. Portanto, os bancos de dados de sistema do SQL Server (mestre, msdb e modelo) podem permanecer na unidade C:\ também. Uma exceção poderia ser o tempdb, que, no caso de algumas cargas de trabalho do SAP ERP e todas as do BW, pode exigir um volume de dados maior ou um volume de operações de E/S maior que não cabe na VM original. Para esses sistemas, as etapas a seguir devem ser executadas:

* Mova os arquivos de dados de tempdb principais para a mesma unidade lógica que os arquivos de dados primário do banco de dados SAP.
* Adicione os arquivos de dados de tempdb adicionais a cada uma das outras unidades lógicas que contém um arquivo de dados do banco de dados de usuário SAP.
* Adicione o arquivo de log tempdb à unidade lógica que contém o arquivo de log do banco de dados do usuário.
* **Exclusivamente para tipos de VM que usam SSDs locais** no nó de computação, os arquivos de log e de dados de tempdb devem ser colocados na unidade D:\. No entanto, pode ser recomendável usar vários arquivos de dados de tempdb. Esteja ciente de que os volumes da unidade D:\ são diferentes com base no tipo de VM.

Essas configurações permitem que o tempdb consuma mais espaço do que a unidade do sistema é capaz de fornecer. Para determinar o tamanho adequado de tempdb, é possível verificar os tamanhos de tempdb nos sistemas existentes, que são executados localmente. Além disso, tal configuração possibilitaria números de IOPS em relação ao tempdb, o que não pode ser fornecido com a unidade do sistema. Novamente, os sistemas que estão em execução local podem ser usados para monitorar a carga de trabalho de E/S em relação ao tempdb para que você possa obter os números de IOPS que espera ver no tempdb.

Uma configuração de VM que executa o SQL Server com um banco de dados SAP e em que os arquivos de log e dados do tempdb estão na unidade D:\ teria a seguinte aparência:

![Configuração de referência da VM IaaS do Azure para SAP][dbms-guide-figure-300]

Lembre-se de que a unidade D:\ tem diferentes tamanhos dependendo do tipo de VM. Dependendo do requisito de tamanho de tempdb você pode ser forçado a parear os arquivos de log e de dados de tempdb com os arquivos de log e de dados do banco de dados SAP em casos em que a unidade D:\ é muito pequena.

#### <a name="formatting-the-disks"></a>Formatação dos discos
Para o SQL Server, o tamanho do bloco NTFS para discos contendo arquivos de log e de dados do SQL Server deve ser de 64K. Não é necessário formatar a unidade D:\. Essa unidade vem pré-formatada.

Para se certificar de que a restauração ou a criação de bancos de dados não está inicializando os arquivos de dados zerando o conteúdo dos arquivos, é necessário se assegurar de que o contexto de usuário em que o serviço do SQL Server está em execução tem uma determinada permissão. Normalmente, os usuários do grupo Administrador do Windows têm essas permissões. Se o serviço do SQL Server for executado no contexto do usuário que não é Administrador do Windows, você precisará atribuir ao usuário o direito 'Executar tarefas de manutenção de volume'.  Confira os detalhes neste artigo da Base de Dados de Conhecimento Microsoft: <https://support.microsoft.com/kb/2574695>

#### <a name="impact-of-database-compression"></a>Impacto da compactação do banco de dados
Em configurações em que a largura de banda de E/S pode se tornar um fator limitante, todas as medidas que reduzem IOPS podem ajudar a ampliar a carga de trabalho que pode ser executada em um cenário de IaaS como o Azure. Portanto, caso você ainda não tenha feito isso, a SAP e Microsoft recomendam fortemente aplicar a compactação de PÁGINA do SQL Server antes de carregar um banco de dados SAP existentes para o Azure.

A recomendação para executar a compactação do banco de dados antes de carregar no Azure é fornecida por duas razões:

* A quantidade de dados a ser carregada é menor.
* A duração da execução de compactação é menor, presumindo-se que seja possível usar um hardware mais poderoso com mais CPUs ou maior largura de banda de E/S ou menor latência de E/S local.
* Tamanhos menores de banco de dados podem levar a menos custos de alocação de disco

A compactação de banco de dados funciona bem nas máquinas virtuais do Azure como o faz localmente. Para obter mais detalhes sobre como compactar um banco de dados SQL Server para SAP existente, acesse: <https://blogs.msdn.com/b/saponsqlserver/archive/2010/10/08/compressing-an-sap-database-using-report-msscompress.aspx>

### <a name="sql-server-2014--storing-database-files-directly-on-azure-blob-storage"></a>SQL Server 2014 – armazenando arquivos do banco de dados arquivos diretamente no Armazenamento de Blobs do Azure
O SQL Server 2014 abre a possibilidade para armazenar arquivos de banco de dados diretamente no Armazenamento de Blobs do Azure sem o ‘wrapper’ de um VHD em torno deles. Especialmente com o uso do Armazenamento do Azure Standard ou tipos de VM menores, isso possibilita cenários em que você pode superar os limites de IOPS que seriam impostos por um número limitado de discos que podem ser montados a alguns tipos de VM menores. Isso funciona para bancos de dados de usuário, no entanto, não para bancos de dados de sistema do SQL Server. Ele também funciona para arquivos de log e dados do SQL Server. Se desejar implantar um banco de dados do SQL Server SAP dessa forma, em vez de aplicar “wrappers” nele nos VHDs, tenha o seguinte em mente:

* A conta de armazenamento usada precisa estar na mesma região do Azure que a usada para implantar a VM em que o SQL Server está sendo executado.
* As considerações listadas anteriormente em relação à distribuição de VHDs em diferentes Contas de Armazenamento do Azure também se aplicam a esse método de implantações. Significa a contagem de operações de E/S em relação aos limites da Conta de Armazenamento do Azure.

[comment]: <> (MSSedusch TODO Porém, isso usará largura de banda de rede e não largura de banda de armazenamento, não é?)

Os detalhes sobre esse tipo de implantação estão listados aqui: <https://docs.microsoft.com/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure>

Para armazenar arquivos de dados do SQL Server diretamente no Armazenamento Premium do Azure, você precisa ter uma versão mínima de patch do SQL Server 2014, que está documentada aqui: <https://support.microsoft.com/kb/3063054>. O armazenamento de arquivos de dados do SQL Server no Armazenamento Standard do Azure funciona com a versão lançada do SQL Server 2014. No entanto, os mesmos patches contêm outra série de correções, que tornam mais confiável o uso direto do Armazenamento de Blobs do Azure para arquivos de dados do SQL Server e backups. Portanto, é recomendável usar esses patches em geral.

### <a name="sql-server-2014-buffer-pool-extension"></a>Extensão do pool de buffers do SQL Server 2014
O SQL Server 2014 introduziu um novo recurso, chamado Extensão do Pool de Buffers. Essa funcionalidade estende o pool de buffers do SQL Server, que é mantida na memória com um cache de segundo nível apoiado por SSDs local de um servidor ou VM. Isso permite manter um conjunto de trabalho maior de dados ‘na memória’. Em comparação ao acesso ao Armazenamento Standard do Azure, o acesso à extensão do pool de buffers, que é armazenado em SSDs locais de uma VM do Azure, é muito mais rápido.  Portanto, aproveitar a unidade D:\ local dos tipos de VM que têm excelentes IOPS e taxa de transferência pode ser uma maneira bastante razoável para reduzir a carga de IOPS no Armazenamento do Azure e para melhorar os tempos de resposta de consultas drasticamente. Isso se aplica especialmente quando não usar o Armazenamento Premium. No caso do Armazenamento Premium e do uso do Cache de Leitura do Azure Premium no nó de computação, conforme recomendado para arquivos de dados, não são esperadas diferenças significativas. O motivo é que ambos os caches (extensão do pool de buffers do SQL Server e o cache de leitura do Armazenamento Premium) estão usando os discos locais dos nós de computação.
Para obter mais detalhes sobre essa funcionalidade, consulte esta documentação: <https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension> 

### <a name="backuprecovery-considerations-for-sql-server"></a>Considerações sobre backup e recuperação para o SQL Server
Ao implantar o SQL Server no Azure, sua metodologia de backup deve ser examinada. Mesmo se o sistema não for um sistema produtivo, o banco de dados SAP hospedado pelo SQL Server deverá ser submetido a backup periodicamente. Como o armazenamento do Azure mantém três imagens, um backup agora é menos importante no que diz respeito à compensação de uma falha de armazenamento. A razão de prioridade para manter um plano de backup e recuperação adequado é mais que você pode compensar erros lógicos/manuais fornecendo funcionalidades de recuperação pontual. Portanto, o objetivo é usar backups para restaurar o banco de dados para um determinado momento ou usar os backups no Azure para propagar outro sistema copiando o banco de dados existente. Por exemplo, você pode transferir de uma configuração do SAP de duas camadas para uma configuração de sistema de três camadas do mesmo sistema restaurando um backup.

Há três maneiras diferentes de fazer o backup do SQL Server para o Armazenamento do Azure:

1. O SQL Server 2012 CU4 e superior podem fazer o backup de bancos de dados para uma URL de forma nativa. Isso é detalhado no blog de [New functionality in SQL Server 2014 – Part 5 – Backup/Restore Enhancements](https://blogs.msdn.com/b/saponsqlserver/archive/2014/02/15/new-functionality-in-sql-server-2014-part-5-backup-restore-enhancements.aspx)(Nova funcionalidade no SQL Server 2014, parte 5, aprimoramentos de backup e restauração). Veja o capítulo [SQL Server 2012 SP1 CU4 e posterior][dbms-guide-5.5.1].
2. Versões do SQL Server anteriores ao SQL 2012 CU4 podem usar uma funcionalidade de redirecionamento para fazer o backup em um VHD e basicamente mover o fluxo de gravação para um local de Armazenamento do Azure que tenha sido configurado. Veja o capítulo [SQL Server 2012 SP1 CU3 e versões anteriores][dbms-guide-5.5.2].
3. O método final é executar um comando de backup convencional do SQL Server para disco em um dispositivo de disco. Isso é idêntico ao padrão de implantação local e não é discutido em detalhes neste documento.

#### <a name="0fef0e79-d3fe-4ae2-85af-73666a6f7268"></a>SQL Server 2012 SP1 CU4 e posterior
Essa funcionalidade permite que você faça o backup diretamente no Armazenamento de Blobs do Azure. Sem esse método, você precisa fazer backup para outros discos, o que consome capacidade de disco e IOPS. A ideia é basicamente essa:

 ![Usando o backup do SQL Server 2012 para o Blob de Armazenamento do Microsoft Azure][dbms-guide-figure-400]

A vantagem nesse caso é que não é necessário gastar discos para armazenar backups do SQL Server. Então você tem menos discos alocados e a largura de banda total de IOPS do disco pode ser usada para arquivos de log e de dados. Observe que o tamanho máximo de um backup é limitado a um máximo de 1 TB, conforme documentado na seção “Limitações” deste artigo: <https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url#limitations>. Se o tamanho do backup, apesar do uso da compactação de backup do SQL Server, exceder 1 TB, a funcionalidade descrita no capítulo [SQL Server 2012 SP1 CU3 e versões anteriores][dbms-guide-5.5.2] neste documento deverá ser usada.

A [documentação relacionada](https://docs.microsoft.com/sql/relational-databases/backup-restore/restoring-from-backups-stored-in-microsoft-azure) descrevendo a restauração de bancos de dados de backups no Armazenamento de Blobs do Azure recomenda não realizar a restauração diretamente do Armazenamento de Blobs do Azure se o backup for >25 GB. A recomendação neste artigo é baseada simplesmente em considerações de desempenho e não devido a restrições funcionais. Portanto, diferentes condições podem se aplicar caso a caso.

A documentação sobre como esse tipo de backup é configurado e utilizado pode ser encontrada [neste](https://docs.microsoft.com/sql/relational-databases/tutorial-use-azure-blob-storage-service-with-sql-server-2016) tutorial

Um exemplo da sequência de etapas pode ser lido [aqui](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url).

A automatização de backups é da mais alta importância para se certificar de que os blobs para cada backup estejam nomeados de forma diferente. Caso contrário, eles serão substituídos e a cadeia restauração será interrompida.

Para não misturar o conteúdo entre os três tipos diferentes de backups, é aconselhável criar contêineres diferentes sob a conta de armazenamento usada para backups. Os contêineres podem ser apenas por VM ou por VM e tipo de backup. O esquema seria semelhante ao seguinte:

 ![Usando o backup do SQL Server 2012 para o Blob de Armazenamento do Microsoft Azure – Diferentes contêineres na conta de armazenamento separada][dbms-guide-figure-500]

No exemplo acima, os backups não seriam realizados na mesma conta de armazenamento em que as VMs são implantadas. Haveria uma nova conta de armazenamento especificamente para os backups. Em contas de armazenamento, haveria diferentes contêineres criados com uma matriz do tipo de backup e o nome da VM. Essa segmentação torna mais fácil administrar os backups das VMs diferentes.

Os BLOBs nos quais os backups são gravados diretamente não são contribuindo para a contagem de discos de dados de uma VM. Portanto, é possível maximizar o máximo de discos de dados montados do SKU de VM específico para os arquivos de log de transações e dados e ainda executar um backup em um contêiner de armazenamento. 

#### <a name="f9071eff-9d72-4f47-9da4-1852d782087b"></a>SQL Server 2012 SP1 CU3 e versões anteriores
A primeira etapa que você deve executar para realizar um backup diretamente no Armazenamento do Azure é baixar o msi que está vinculado a [este](https://www.microsoft.com/download/details.aspx?id=40740) artigo da KBA.

Baixe o arquivo de instalação x64 e a documentação. O arquivo instala um programa chamado: 'Microsoft SQL Server Backup to Microsoft Azure Tool'. Leia a documentação do produto completamente.  A ferramenta basicamente funciona da seguinte maneira:

* No lado do SQL Server, um local de disco para o backup do SQL Server é definido (não use a unidade D:\ para isso).
* A ferramenta permite que você defina regras que podem ser usadas para direcionar diferentes tipos de backups para diferentes contêineres de Armazenamento do Azure.
* Depois que as regras estão em vigor, a ferramenta redireciona o fluxo de gravação do backup para um dos VHDs/discos para o local de Armazenamento do Azure definido anteriormente.
* A ferramenta deixa um pequeno arquivo stub de alguns KB de tamanho no VHD/disco definido para o backup do SQL Server. **Esse arquivo deve ser deixado no local de armazenamento, já que é necessário para restaurar novamente do armazenamento do Azure.**
  * Se você tiver perdido o arquivo stub (por exemplo, devido à perda da mídia de armazenamento que continha o arquivo stub) e tiver escolhido a opção de backup em uma conta de Armazenamento do Microsoft Azure, poderá recuperar o arquivo stub por meio do Armazenamento do Microsoft Azure baixando-o do contêiner de armazenamento no qual ele foi colocado. Em seguida, você deverá colocar o arquivo stub em uma pasta no computador local em que a ferramenta está configurada para detectar e carregar no mesmo contêiner com a mesma senha de criptografia se a criptografia tiver sido usada com a regra original. 

Isso significa que o esquema conforme descrito acima para versões mais recentes do SQL Server pode ser colocado em vigor também para versões do SQL Server que não permitem endereçar diretamente um local de armazenamento do Azure.

Esse método não deve ser usado com versões mais recentes do SQL Server que dão suporte à realização do backup nativamente no armazenamento do Azure. As exceções existem onde as limitações do backup nativo no Azure estão impedindo a execução do backup nativo no Azure.

#### <a name="other-possibilities-to-backup-sql-server-databases"></a>Outras possibilidades para fazer o backup de bancos de dados SQL Server
Outras possibilidades para bancos de dados de backup é anexar discos de dados adicionais a uma VM que você usa para armazenar backups. Nesse caso, você precisaria se certificar de que os discos não estão ficando cheios. Se esse for o caso, será necessário desmontar os discos e, por assim dizer, “arquivá-lo” e substituí-lo por um novo disco vazio. Se seguir esse caminho, você desejará manter esses VHDs em contas de armazenamento do Azure separadas daquelas dos VHDs com os arquivos de banco de dados.

Uma segunda possibilidade é usar uma VM grande que possa ter muitos discos anexados, por exemplo, D14 com 32 VHDs. Use espaços de armazenamento para criar um ambiente flexível em que você pode criar compartilhamentos que são usados como destinos de backup para os diferentes servidores DBMS.

Algumas práticas recomendadas também foram documentadas [aqui](https://blogs.msdn.com/b/sqlcat/archive/2015/02/26/large-sql-server-database-backup-on-an-azure-vm-and-archiving.aspx) . 

#### <a name="performance-considerations-for-backupsrestores"></a>Considerações de desempenho para backups/restaurações
Como em implantações bare-metal, o desempenho de backup/restauração depende de quantos volumes podem ser lidos em paralelo e qual pode ser a taxa de transferência desses volumes. Além disso, o consumo de CPU usado pela compactação de backup pode desempenhar uma função significativa em VMs com apenas até oito threads de CPU. Portanto, é possível supor que:

* Quanto menor for o número de discos usados para armazenar os arquivos de dados, menor será a taxa de transferência geral na leitura.
* Quanto menor o número de threads de CPU na VM, mais severo é o impacto da compactação de backup.
* Quanto menos destinos (blobs, VHDs ou discos) forem usados para gravar o backup, menor será a taxa de transferência.
* Quanto menor o tamanho da VM, menor é a gravação e leitura da cota de taxa de transferência do Armazenamento do Azure. Independentemente de se os backups são armazenados diretamente no Blob do Azure ou se eles são armazenados em VHDs que novamente são armazenados em Blobs do Azure.

Ao usar um blob de Armazenamento do Microsoft Azure como o destino de backup em versões mais recentes, você está restrito a designar apenas uma URL de destino para cada backup específico.

Mas ao usar o 'Microsoft SQL Server Backup to Microsoft Azure Tool' em versões mais antigas, você pode definir mais de um destino de arquivo. Com mais de um destino, o backup pode ser dimensionado e a taxa de transferência do backup pode ser maior. Isso resultaria, então, em vários arquivos também na conta de armazenamento do Azure. Em nossos testes, usando vários destinos de arquivo, é possível atingir definitivamente a taxa de transferência que poderia ser atingida com as extensões de backup implementadas por meio do SQL Server 2012 SP1 CU4. Você também não é bloqueado pelo limite de 1 TB como no backup nativo no Azure.

No entanto, tenha em mente que a taxa de transferência também é depende do local da conta de armazenamento do Azure usada para o backup. Uma ideia pode ser localizar a conta de armazenamento em uma região diferente daquela em que as VMs estão em execução. Por exemplo, você executaria a configuração da VM no Oeste da Europa, mas colocaria a Conta de Armazenamento que usa para fazer backup no Norte da Europa. Isso certamente tem impacto sobre a taxa de transferência de backup e é improvável que gere uma taxa de transferência de 150 MB/s, como parece ser possível em casos em que o armazenamento de destino e as VMs estão em execução no mesmo datacenter regional.

#### <a name="managing-backup-blobs"></a>Gerenciando blobs de Backup
Há um requisito para gerenciar os backups por conta própria. Como a expectativa é que muitos blobs sejam criados pela execução de backups de log de transações frequentes, a administração desses blobs poderá facilmente sobrecarregar o portal do Azure. Portanto, é recomendável utilizar um gerenciador de armazenamento do Azure. Existem vários bons disponíveis que podem ajudar a gerenciar uma conta de armazenamento do Azure

* Microsoft Visual Studio com o SDK do Azure instalado (<https://azure.microsoft.com/downloads/>)
* Gerenciador de Armazenamento do Microsoft Azure (<https://azure.microsoft.com/downloads/>)
* Ferramentas de terceiros

Para uma discussão mais completa sobre Backup e SAP no Azure, consulte o [Guia de backup de SAP](sap-hana-backup-guide.md) para obter mais informações.

### <a name="1b353e38-21b3-4310-aeb6-a77e7c8e81c8"></a>Como usar imagens do SQL Server do Microsoft Azure Marketplace
A Microsoft oferece VMs no Azure Marketplace que já contêm versões do SQL Server. Para os clientes SAP que necessitam de licenças para o SQL Server e Windows, essa pode ser uma oportunidade para cobrir basicamente a necessidade de licenças gerando VMs com o SQL Server já instalado. Para usar essas imagens para SAP, as considerações a seguir precisam ser feitas:

* As versões do SQL Server que não são de avaliação acarretam em custos mais elevados do que uma VM ‘Somente Windows’ implantada do Azure Marketplace. Confira estes artigos para comparar preços: <https://azure.microsoft.com/pricing/details/virtual-machines/windows/> e <https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/>. 
* Você pode usar apenas versões do SQL Server que têm suporte da SAP, como o SQL Server 2012.
* O agrupamento da instância do SQL Server que é instalado nas VMs oferecidas no Azure Marketplace não é o agrupamento que o SAP NetWeaver requer que a instância do SQL Server execute. No entanto, você pode alterar o agrupamento com as instruções na seção a seguir.

#### <a name="changing-the-sql-server-collation-of-a-microsoft-windowssql-server-vm"></a>Alterando o agrupamento do SQL Server de uma VM Microsoft Windows/SQL Server
Uma vez que as imagens do SQL Server no Azure Marketplace não estão configuradas para usarem o agrupamento, o que é exigido pelos aplicativos SAP NetWeaver, elas precisam ser alteradas imediatamente após a implantação. Para o SQL Server 2012, isso pode ser feito com as etapas a seguir assim que a VM tiver sido implantada e um administrador for capaz de fazer logon nela:

* Abra uma janela de comando do Windows ‘como administrador’.
* Altere o diretório para C:\Arquivos de Programas\Microsoft SQL Server\110\Setup Bootstrap\SQLServer2012.
* Execute o comando: Setup.exe /QUIET /ACTION=REBUILDDATABASE /INSTANCENAME=MSSQLSERVER /SQLSYSADMINACCOUNTS=`<local_admin_account_name`> /SQLCOLLATION=SQL_Latin1_General_Cp850_BIN2   
  * `<local_admin_account_name`> é a conta definida como a conta Administrador ao implantar a VM pela primeira vez por meio da galeria.

O processo deve levar apenas alguns minutos. Para verificar se a etapa terminou com o resultado correto, execute as seguintes etapas:

* Abra o SQL Server Management Studio.
* Abra uma Janela de Consulta.
* Execute o comando sp_helpsort no banco de dados mestre do SQL Server.

O resultado desejado deve ter uma aparência semelhante a essa:

    Latin1-General, binary code point comparison sort for Unicode Data, SQL Server Sort Order 40 on Code Page 850 for non-Unicode Data

Se esse não for o resultado, INTERROMPA a implantação do SAP e investigue por que o comando de instalação não funcionou conforme o esperado. A implantação de aplicativos SAP NetWeaver na instância do SQL Server com páginas de código do SQL Server diferentes da mencionada acima **NÃO** tem suporte.

### <a name="sql-server-high-availability-for-sap-in-azure"></a>Alta disponibilidade do SQL Server para SAP no Azure
Conforme mencionado anteriormente neste artigo, não há nenhuma possibilidade de criar um armazenamento compartilhado, o que é necessário para o uso da funcionalidade de alta disponibilidade do SQL Server mais antigo. Essa funcionalidade instalaria duas ou mais instâncias do SQL Server em um WSFC (Cluster de Failover do Windows Server) usando um disco compartilhado para os bancos de dados do usuário (e, eventualmente, tempdb). Esse é o método de alta disponibilidade padrão de tempo longo, que também tem suporte pelo SAP. Como o Azure não dá suporte ao armazenamento compartilhado, as configurações de alta disponibilidade do SQL Server com uma configuração de cluster de disco compartilhado não podem ser realizadas. No entanto, muitos outros métodos de alta disponibilidade ainda são possíveis e são descritos nas seções a seguir.

#### <a name="sql-server-log-shipping"></a>Envio de logs do SQL Server
Um dos métodos de HA (alta disponibilidade) é o envio de logs do SQL Server. Se as VMs que participam da configuração de HA tiverem uma resolução de nome funcionando, não haverá problema e a configuração no Azure não será diferente de nenhuma configuração feita localmente. Não é recomendável confiar apenas na resolução de IP. No que diz respeito à configuração do Envio de Logs e dos princípios em torno disso, consulte esta documentação:

<https://docs.microsoft.com/sql/database-engine/log-shipping/about-log-shipping-sql-server>

Para realmente atingir a alta disponibilidade, é necessário implantar VMs que estejam dentro de tal configuração de Envio de Logs para que fiquem dentro do mesmo Conjunto de Disponibilidade do Azure.

#### <a name="database-mirroring"></a>Espelhamento de banco de dados
O espelhamento de banco de dados conforme o que tem suporte pelo SAP (consulte a Nota SAP [965908]) se baseia na definição de um parceiro de failover na cadeia de conexão SAP. Para os casos entre instalações, presumimos que as duas VMs estão no mesmo domínio e que o contexto de usuário sob o qual as duas instâncias do SQL Server estão sendo executadas é o usuário de domínio e tem privilégios suficientes nas duas instâncias do SQL Server envolvidas. Portanto, a configuração do espelhamento de banco de dados no Azure não difere entre uma configuração/instalação local típica.

A partir de implantações somente em nuvem, o método mais fácil é ter outra configuração de domínio no Azure para que essas VMs de DBMS (e VMs do SAP idealmente dedicadas) dentro de um domínio.

Se um domínio não for possível, também poderemos usar certificados para os pontos de extremidade de espelhamento de banco de dados, conforme descrito aqui: <https://docs.microsoft.com/sql/database-engine/database-mirroring/use-certificates-for-a-database-mirroring-endpoint-transact-sql>

Um tutorial para configurar o Espelhamento de Banco de Dados no Azure pode ser encontrado aqui: <https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server> 

#### <a name="sql-server-always-on"></a>SQL Server Always On
Uma vez que Always On tem suporte para SAP local (consulte a Nota SAP [1772688]), ele tem suporte para ser usado em combinação com o SAP no Azure. O fato de que você não pode criar discos compartilhados no Azure não significa que não seja possível criar uma configuração de WSFC (Cluster de Failover do Windows Server) do Always On entre diferentes VMs. Isso significa apenas que você não tem a possibilidade de usar um disco compartilhado como um quórum na configuração do cluster. Portanto, você pode criar uma configuração de WSFC do Always On no Azure e simplesmente não selecionar o tipo de quórum que usa o disco compartilhado. O ambiente do Azure em que essa VMs são implantadas deve resolver as VMs por nome e as VMs devem estar no mesmo domínio. Isso é verdadeiro para implantações somente do Azure e entre instalações. Há algumas considerações especiais para implantar o ouvinte de grupo de disponibilidade do SQL Server (não deve ser confundido com o conjunto de disponibilidade do Azure) uma vez que o Azure neste momento não permite simplesmente criar um objeto AD/DNS como é possível localmente. Portanto, algumas etapas de instalação diferentes são necessárias para superar o comportamento específico do Azure.

Algumas considerações sobre o uso de um ouvinte de grupo de disponibilidade são:

* O uso de um ouvinte de grupo de disponibilidade é possível apenas com o Windows Server 2012 ou superior como o SO convidado da VM. Para o Windows Server 2012, você precisa se certificar de que o patch foi aplicado: <https://support.microsoft.com/kb/2854082> 
* Para o Windows Server 2008 R2, esse patch não existe e Always On precisaria ser usado da mesma maneira que o espelhamento de banco de dados especificando um parceiro de failover na cadeia de conexões (feito por meio do parâmetro default.pfl do SAP dbs/mss/server; consulte a Nota SAP [965908]).
* Ao usar um ouvinte de grupo de disponibilidade, as VMs de banco de dados precisam estar conectadas a um balanceador de carga dedicado. A resolução de nomes em implantações Somente em Nuvem exigiria que todas as VMs do sistema SAP (servidores de aplicativo, servidor DBMS e servidor (A)SCS) estivessem na mesma rede virtual ou exigiria a manutenção do arquivo etc\host da camada de aplicativo SAP para resolver os nomes das VMs do SQL Server. Para evitar que o Azure atribua novos endereços IP em casos em que ambas as VMs sejam desligadas incidentalmente, deve-se atribuir endereços IP estáticos aos adaptadores de rede das VMs na configuração Always On (a definição de um endereço IP estático é descrita [neste][virtual-networks-reserved-private-ip] artigo)

[comment]: <> (Blogs antigos)
[comment]: <> (<https://blogs.msdn.com/b/alwaysonpro/archive/2014/08/29/recommendations-and-best-practices-when-deploying-sql-server-alwayson-availability-groups-in-windows-azure-iaas.aspx>, <https://blogs.technet.com/b/rmilne/archive/2015/07/27/how-to-set-static-ip-on-azure-vm.aspx>) 
* Há etapas especiais necessárias ao criar a configuração de cluster de WSFC em que o cluster precisa de um endereço IP especial atribuído, pois o Azure com sua funcionalidade atual atribuiria ao nome do cluster o mesmo endereço IP que o nó em que o cluster foi criado. Isso significa que uma etapa manual deve ser executada para atribuir um endereço IP diferente ao cluster.
* O ouvinte do grupo de disponibilidade será criado no Azure com pontos de extremidade TCP/IP atribuídos às VMs executando as réplicas primária e secundária do grupo de disponibilidade.
* Pode haver a necessidade de proteger esses pontos de extremidade com ACLs.

[comment]: <> (Blog antigo TODO)
[comment]: <> (As necessidades e etapas detalhadas de instalação de uma configuração AlwaysOn no Azure são mais bem entendidas com o passo a passo do tutorial disponível [aqui][virtual-machines-windows-classic-ps-sql-alwayson-availability-groups])
[comment]: <> (Instalação pré-configurada do AlwaysOn por meio da galeria do Azure <https://blogs.technet.com/b/dataplatforminsider/archive/2014/08/25/sql-server-alwayson-offering-in-microsoft-azure-portal-gallery.aspx>)
[comment]: <> (A criação de um Ouvinte do Grupo de Disponibilidade é mais bem descrita [neste][virtual-machines-windows-classic-ps-sql-int-listener] tutorial)
[comment]: <> (A proteção dos pontos de extremidade da rede com ACLs é explicada aqui:)
[comment]: <> (*    <https://michaelwasham.com/windows-azure-powershell-reference-guide/network-access-control-list-capability-in-windows-azure-powershell/>)
[comment]: <> (*    <https://blogs.technet.com/b/heyscriptingguy/archive/2013/08/31/weekend-scripter-creating-acls-for-windows-azure-endpoints-part-1-of-2.aspx> )
[comment]: <> (*    <https://blogs.technet.com/b/heyscriptingguy/archive/2013/09/01/weekend-scripter-creating-acls-for-windows-azure-endpoints-part-2-of-2.aspx>)  
[comment]: <> (*    <https://blogs.technet.com/b/heyscriptingguy/archive/2013/09/18/creating-acls-for-windows-azure-endpoints.aspx>) 

É possível implantar um grupo de disponibilidade Always On do SQL Server em diferentes regiões do Azure também. Essa funcionalidade aproveita a conectividade Vnet a Vnet do Azure ([mais detalhes][virtual-networks-configure-vnet-to-vnet-connection]).

[comment]: <> (Blog antigo TODO)
[comment]: <> (A configuração dos Grupos de Disponibilidade AlwaysOn do SQL Server em tal cenário é descrita aqui: <https://blogs.technet.com/b/dataplatforminsider/archive/2014/06/19/sql-server-alwayson-availability-groups-supported-between-microsoft-azure-regions.aspx>.) 

#### <a name="summary-on-sql-server-high-availability-in-azure"></a>Resumo da alta disponibilidade do SQL Server no Azure
Devido ao fato de que o armazenamento do Azure está protegendo o conteúdo, há um motivo a menos para insistir em uma imagem de espera ativa. Isso significa que seu cenário de alta disponibilidade precisa proteger apenas contra os seguintes casos:

* Indisponibilidade da VM como um todo devido à manutenção no cluster de servidores no Azure ou por outros motivos
* Problemas de software na instância do SQL Server
* Proteção contra erros manuais em que os dados são excluídos e a recuperação pontual é necessária

Examinando as tecnologias correspondentes, é possível argumentar que os dois primeiros casos podem ser abrangidos pelo Espelhamento de Banco de Dados ou o Always On, enquanto o terceiro caso pode ser abrangido apenas pelo Envio de Logs.

Você precisa equilibrar a configuração mais complexa do Always On em comparação ao Espelhamento de Banco de Dados com as vantagens do Always On. É possível listar essas vantagens como:

* Replicas secundárias legíveis.
* Backups de réplicas secundárias.
* Melhor escalabilidade.
* Mais de uma réplica secundária.

### <a name="9053f720-6f3b-4483-904d-15dc54141e30"></a>Resumo do SQL Server para SAP no Azure geral
Há muitas recomendações neste guia e recomendamos que você o leia mais de uma vez antes de planejar sua implantação do Azure. Em geral, no entanto, não se esqueça de seguir os dez principais pontos específicos do DBMS no Azure gerais:

[comment]: <> (Taxa de transferência superior a 2.3 do que o que? De um VHD?)
1. Use a versão mais recente do DBMS, como SQL Server 2014, que tem mais vantagens no Azure. Para o SQL Server, é o SQL Server 2012 SP1 CU4, que inclui o recurso de backup no armazenamento do Azure. No entanto, em conjunto com o SAP, recomendamos pelo menos o SQL Server 2014 SP1 CU1 ou o SQL Server 2012 SP2 e o CU mais recente.
2. Planeje cuidadosamente sua estrutura de sistema da SAP no Azure para balancear o layout do arquivo de dados e as restrições do Azure:
   * Não tenha discos demais, mas tenha espaço suficiente para garantir que você possa atingir seu IOPS necessário.
   * Se você não usar Managed Disks, lembre-se de que IOPS também estão limitados pela Conta de Armazenamento do Azure e que as Contas de Armazenamento são limitadas em cada assinatura do Azure ([mais detalhes][azure-subscription-service-limits]). 
   * Somente divida entre discos se você precisar obter uma maior taxa de transferência.
3. Nunca instale software nem coloque nenhum arquivo que exija persistência na unidade D:\, uma vez que ela não é permanente e tudo nessa unidade é perdido em uma reinicialização do Windows.
4. Não use cache de disco para o Armazenamento Standard do Azure.
5. Não use contas de armazenamento com replicação geográfica do Azure.  Use Localmente Redundante para cargas de trabalho do DBMS.
6. Use a solução de HA/DR do seu fornecedor do DBMS para replicar dados do banco de dados.
7. Sempre use a resolução de nome, não confie em endereços IP.
8. Use a maior compactação de banco de dados possível. Para o SQL Server, essa é a compactação de página.
9. Tenha cuidado ao usar imagens do SQL Server do Azure Marketplace. Se você usar o SQL Server um, deverá alterar o agrupamento de instância antes de instalar qualquer sistema SAP NetWeaver nele.
10. Instale e configure o Monitoramento de Host do SAP para Azure, conforme descrito no [Guia de Implantação][deployment-guide].

## <a name="specifics-to-sap-ase-on-windows"></a>Informações específicas para o SAP ASE no Windows
Começando com o Microsoft Azure, você pode facilmente migrar seus aplicativos SAP ASE existentes para máquinas virtuais do Azure. O SAP ASE em uma Máquina Virtual permite reduzir o custo total de propriedade de implantação, gerenciamento e manutenção de aplicativos de amplitude empresarial migrando facilmente esses aplicativos para o Microsoft Azure. Com o SAP ASE em uma máquina virtual do Azure, os administradores e desenvolvedores ainda podem usar as mesmas ferramentas de desenvolvimento e administração que estão disponíveis localmente.

Há um SLA para Máquinas Virtuais do Azure, que pode ser encontrado aqui: <https://azure.microsoft.com/support/legal/sla/virtual-machines>

Temos certeza de que as máquinas virtuais hospedadas do Microsoft Azure têm um ótimo desempenho em comparação a outras ofertas de virtualização de nuvem pública, mas os resultados individuais podem variar. Os números de SAPS do dimensionamento do SAP dos diferentes SKUs de VM certificados do SAP são fornecidos em uma Nota SAP [1928533] separada.

As instruções e recomendações sobre o uso do armazenamento do Azure, implantação de VMs SAP ou monitoramento do SAP se aplicam às implantações do SAP ASE em conjunto com aplicativos SAP conforme indicado em todos os quatro primeiros capítulos deste documento.

### <a name="sap-ase-version-support"></a>Suporte de versão do SAP ASE
No momento, a SAP dá suporte ao SAP ASE versão 16.0 para uso com produtos SAP Business Suite. Todas as atualizações para o servidor SAP ASE ou drivers JDBC e ODBC a serem usadas com produtos SAP Business Suite são fornecidas exclusivamente pelo SAP Service Marketplace em: <https://support.sap.com/swdc>.

Para instalações locais, não baixe atualizações para o servidor SAP ASE ou para os drivers JDBC e ODBC diretamente de sites do Sybase. Para obter informações detalhadas sobre os patches que têm suporte para uso com produtos SAP Business Suite locais e em máquinas virtuais do Azure, consulte as seguintes Notas SAP:

* [1590719]
* [1973241]

Informações gerais sobre como executar o SAP Business Suite em SAP ASE podem ser encontradas no [SCN](https://www.sap.com/community/topic/ase.html)

### <a name="sap-ase-configuration-guidelines-for-sap-related-sap-ase-installations-in-azure-vms"></a>Diretrizes de configuração do SAP ASE para instalações do SAP ASE relacionadas ao SAP em VMs do Azure
#### <a name="structure-of-the-sap-ase-deployment"></a>Estrutura da implantação do SAP ASE
De acordo com a descrição geral, os executáveis do SAP ASE devem estar localizados ou instalados na unidade do sistema do disco do SO da VM (unidade C:\). Normalmente, a maioria dos bancos de dados de ferramentas e sistema do SAP ASE não é realmente utilizada de forma concreta pela carga de trabalho do SAP NetWeaver. Portanto, os bancos de dados de ferramentas e sistema (mestre, modelo, saptools, sybmgmtdb, sybsystemdb) podem permanecer na unidade C:\ também. 

Uma exceção pode ser o banco de dados temporário que contém todas as tabelas de trabalho e tabelas temporárias criadas pelo SAP ASE, que, no caso de algumas cargas de trabalho do SAP ERP e todas do BW, pode exigir um volume de dados ou um volume de operações de E/S maior, o que pode não caber no disco do SO da VM original (unidade c:\).

Dependendo da versão do SAPInst/SWPM usada para instalar o sistema, o banco de dados pode conter:

* Um único tempdb do SAP ASE, que é criado durante a instalação do SAP ASE
* Um tempdb do SAP ASE criado pela instalação do SAP ASE e um saptempdb adicional criado pela rotina de instalação do SAP
* Um tempdb do SAP ASE criado pela instalação do SAP ASE e um tempdb adicional criado manualmente (por exemplo, seguindo a Nota SAP [1752266]) para atender aos requisitos de tempdb específicos do ERP/BW

No caso de cargas de trabalho específicas do ERP ou todas do BW, faz sentido, em relação ao desempenho, manter os dispositivos de tempdb do tempdb adicionalmente criado (por SWPM ou manualmente) em uma unidade diferente da C:\. Caso não exista nenhum tempdb adicional, é recomendável criar um (Nota SAP [1752266]).

Para esses sistemas, as etapas a seguir devem ser executadas para o tempdb criado adicionalmente:

* Mova o primeiro dispositivo de tempdb para o primeiro dispositivo do banco de dados SAP
* Adicione dispositivos tempdb para cada um dos VHDs contendo um dispositivo do banco de dados SAP

Essa configuração permite que o tempdb consuma mais espaço do que a unidade do sistema é capaz de fornecer. Como referência, é possível verificar os tamanhos de dispositivo tempdb em sistemas existentes, que são executados localmente. Ou tal configuração habilitaria números de IOPS em relação ao tempdb, que não poderiam ser fornecidos com a unidade do sistema. Novamente, sistemas que são executados localmente podem ser usados para monitorar a carga de trabalho de E/S no tempdb.

Nunca coloque nenhum dispositivo do SAP ASE na unidade D:\ da VM. Isso também se aplica ao tempdb, mesmo que os objetos mantidos no tempdb sejam apenas temporários.

#### <a name="impact-of-database-compression"></a>Impacto da compactação do banco de dados
Em configurações em que a largura de banda de E/S pode se tornar um fator limitante, todas as medidas que reduzem IOPS podem ajudar a ampliar a carga de trabalho que pode ser executada em um cenário de IaaS como o Azure. Portanto, é altamente recomendável para se certificar de que a compactação do SAP ASE seja usada antes de carregar um banco de dados SAP existente no Azure.

A recomendação de executar a compactação antes de carregar no Azure, se ainda não estiver implementada, é indicada por vários motivos:

* A quantidade de dados a ser carregada no Azure é menor
* A duração da execução de compactação é menor, presumindo-se que seja possível usar um hardware mais poderoso com mais CPUs ou maior largura de banda de E/S ou menor latência de E/S local
* Tamanhos menores de banco de dados podem levar a menos custos de alocação de disco

A compactação de LOB e dados funciona em uma VM hospedada em máquinas virtuais do Azure como o faz localmente. Para obter mais detalhes sobre como verificar se a compactação já está em uso em um banco de dados SAP ASE existente, consulte a Nota SAP [1750510].

#### <a name="using-dbacockpit-to-monitor-database-instances"></a>Usando o DBACockpit para monitorar instâncias do banco de dados
Para sistemas SAP que estão usando o SAP ASE como plataforma de banco de dados, o DBACockpit está acessível como janelas de navegador inseridas no DBACockpit da transação ou como Webdynpro. No entanto, a funcionalidade completa para monitorar e administrar o banco de dados está disponível na implementação do Webdynpro do DBACockpit apenas.

Como com sistemas locais, são necessárias várias etapas para habilitar toda a funcionalidade do SAP NetWeaver usada pela implementação do Webdynpro do DBACockpit. Siga a Nota SAP [1245200] para habilitar o uso de webdynpros e gerar os necessários. Ao seguir as instruções nas notas acima, você também configura o ICM (Gerenciador de Comunicação da Internet) junto com as portas a serem usadas para conexões http e https. A configuração padrão para http tem esta aparência:

> icm/server_port_0 = PROT=HTTP,PORT=8000,PROCTIMEOUT=600,TIMEOUT=600
> 
> icm/server_port_1 = PROT=HTTPS,PORT=443$$,PROCTIMEOUT=600,TIMEOUT=600
> 
> 

e os links gerados no DBACockpit da transação serão semelhantes a este:

> https://`<fullyqualifiedhostname`>:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http://`<fullyqualifiedhostname`>:8000/sap/bc/webdynpro/sap/dba_cockpit
> 
> 

Dependendo de se e como a máquina virtual do Azure que hospeda o sistema SAP está conectado via site a site, multissite ou ExpressRoute (implantação entre instalações), você precisa se certificar de que o ICM esteja usando um nome de host totalmente qualificado que pode ser resolvido na máquina virtual da qual você está tentando abrir o DBACockpit. Consulte a Nota SAP [773830] para entender como o ICM determina o nome do host totalmente qualificado de acordo com os parâmetros de perfil e defina o parâmetro icm/host_name_full explicitamente, se necessário.

Se você implantou a VM em um cenário de somente na nuvem sem conectividade entre instalações entre locais e o Azure, você precisa definir um endereço IP público e um domainlabel. O formato do nome DNS público da VM tem esta aparência:

> `<custom domainlabel`>.`<azure region`>.cloudapp.azure.com
> 
> 

É possível encontrar mais detalhes relacionados ao nome DNS [aqui][virtual-machines-azurerm-versus-azuresm].

Definindo o parâmetro de perfil do SAP icm/host_name_full para o nome DNS da VM do Azure, o link pode ser semelhante a:

> https://mydomainlabel.westeurope.cloudapp.net:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http://mydomainlabel.westeurope.cloudapp.net:8000/sap/bc/webdynpro/sap/dba_cockpit
> 
> 

Nesse caso, você precisa se certificar de:

* Adicione regras de entrada ao Grupo de Segurança de Rede no portal do Azure para as portas TCP/IP usadas para comunicação com o ICM
* Adicionar regras de entrada para a configuração do Firewall do Windows para as portas TCP/IP usadas para se comunicar com o ICM

Para um processo importado automatizado de todas as correções disponíveis, recomenda-se aplicar periodicamente a Nota SAP da coleção de correções aplicável à sua versão do SAP:

* [1558958]
* [1619967]
* [1882376]

Mais informações sobre o DBA Cockpit para SAP ASE podem ser encontradas nas seguintes Notas SAP:

* [1605680]
* [1757924]
* [1757928]
* [1758182]
* [1758496]    
* [1814258]
* [1922555]
* [1956005]

#### <a name="backuprecovery-considerations-for-sap-ase"></a>Considerações sobre backup/recuperação para o SAP ASE
Ao implantar o SAP ASE no Azure, sua metodologia de backup deve ser examinada. Mesmo se o sistema não for um sistema produtivo, o banco de dados SAP hospedado pelo SAP ASE deverá ser submetido a backup periodicamente. Como o armazenamento do Azure mantém três imagens, um backup agora é menos importante no que diz respeito à compensação de uma falha de armazenamento. A razão principal para manter um plano de backup e restauração adequado é mais que você pode compensar erros lógicos/manuais fornecendo funcionalidades de recuperação pontual. Portanto, o objetivo é usar backups para restaurar o banco de dados para um determinado momento ou usar os backups no Azure para propagar outro sistema copiando o banco de dados existente. Por exemplo, você pode transferir de uma configuração do SAP de duas camadas para uma configuração de sistema de três camadas do mesmo sistema restaurando um backup.

A realização do backup e a recuperação de um banco de dados no Azure funcionam da mesma maneira que localmente. Consulte as observações do SAP:

* [1588316]
* [1585981]

para obter detalhes sobre como criar configurações de despejo e agendar backups. Dependendo da sua estratégia e das suas necessidades, você pode configurar despejos de log e banco de dados em um dos discos existentes ou adicionar um disco extra para o backup. Para reduzir o risco de perda de dados em caso de erro, é recomendável usar um disco em que não esteja localizado nenhum dispositivo de banco de dados.

Além da compactação do LOB e dados, o SAP ASE também oferece a compactação de backup. Para ocupar menos espaço com os despejos de banco de dados e de log, é recomendável usar a compactação de backup. Para obter mais informações, consulte a Nota SAP [1588316]. A compactação do backup também é crucial para reduzir a quantidade de dados a ser transferida se você planeja baixar os backups ou VHDs contendo despejos de backup da máquina virtual do Azure para o local.

Não use a unidade D:\ como destino de despejo de log ou banco de dados.

#### <a name="performance-considerations-for-backupsrestores"></a>Considerações de desempenho para backups/restaurações
Como em implantações bare-metal, o desempenho de backup/restauração depende de quantos volumes podem ser lidos em paralelo e qual pode ser a taxa de transferência desses volumes. Além disso, o consumo de CPU usado pela compactação de backup pode desempenhar uma função significativa em VMs com apenas até oito threads de CPU. Portanto, é possível supor que:

* Quanto menor o número de discos usados para armazenar os dispositivos de banco de dados, menor é a taxa de transferência geral na leitura
* Quanto menor o número de threads de CPU na VM, mais severo é o impacto da compactação de backup
* Quanto menos destinos (Diretórios de Faixa, discos) nos quais gravar o backup houver, menor será a taxa de transferência

Para aumentar o número de destinos nos quais gravar, existem duas opções que podem ser usadas/combinadas, dependendo das suas necessidades:

* Como dividir o volume de destino de backup em vários discos montados para melhorar a taxa de transferência de IOPS nesse volume dividido
* Como criar uma configuração de despejo no nível do SAP ASE, que usa mais de um diretório de destino no qual gravar o despejo

A divisão de um volume em vários discos montados foi discutida anteriormente nesse guia. Para obter mais informações sobre como usar vários diretórios na configuração de despejo do ASE SAP, confira a documentação sobre o procedimento armazenado sp_config_dump que é usado para criar a configuração de despejo no [Infocenter do Sybase](http://infocenter.sybase.com/help/index.jsp).

### <a name="disaster-recovery-with-azure-vms"></a>Recuperação de desastre com VMs do Azure
#### <a name="data-replication-with-sap-sybase-replication-server"></a>Replicação de dados com o Servidor de Replicação do SAP Sybase
Com o SRS (Servidor de Replicação SAP Sybase), o SAP ASE fornece uma solução de espera passiva para transferir transações de banco de dados para um local distante de forma assíncrona. 

A instalação e operação do SRS funcionam tão bem funcionalmente em uma VM hospedada nos Serviços de Máquina Virtual do Azure como localmente.

O ASE HADR por meio do servidor de replicação do SAP é planejado com uma versão futura. Ele será testado com e liberado para plataformas Microsoft Azure assim que estiver disponível.

## <a name="specifics-to-sap-ase-on-linux"></a>Informações específicas para o SAP ASE no Linux
Começando com o Microsoft Azure, você pode facilmente migrar seus aplicativos SAP ASE existentes para máquinas virtuais do Azure. O SAP ASE em uma Máquina Virtual permite reduzir o custo total de propriedade de implantação, gerenciamento e manutenção de aplicativos de amplitude empresarial migrando facilmente esses aplicativos para o Microsoft Azure. Com o SAP ASE em uma máquina virtual do Azure, os administradores e desenvolvedores ainda podem usar as mesmas ferramentas de desenvolvimento e administração que estão disponíveis localmente.

Para implantar VMs do Azure, é importante conhecer os SLAs oficiais, que podem ser encontrados aqui: <https://azure.microsoft.com/support/legal/sla>

As informações de dimensionamento do SAP e uma lista dos SKUs de VM certificados do SAP são fornecidas na Nota SAP [1928533]. Documentos adicionais sobre dimensionamento de SAP para máquinas virtuais do Azure podem ser encontrados aqui <http://blogs.msdn.com/b/saponsqlserver/archive/2015/06/19/how-to-size-sap-systems-running-on-azure-vms.aspx> e aqui <http://blogs.msdn.com/b/saponsqlserver/archive/2015/12/01/new-white-paper-on-sizing-sap-solutions-on-azure-public-cloud.aspx>

As instruções e recomendações sobre o uso do armazenamento do Azure, implantação de VMs SAP ou monitoramento do SAP se aplicam às implantações do SAP ASE em conjunto com aplicativos SAP conforme indicado em todos os quatro primeiros capítulos deste documento.

As duas Notas SAP a seguir incluem informações gerais sobre o ASE no Linux e ASE na Nuvem:

* [2134316]
* [1941500]

### <a name="sap-ase-version-support"></a>Suporte de versão do SAP ASE
No momento, a SAP dá suporte ao SAP ASE versão 16.0 para uso com produtos SAP Business Suite. Todas as atualizações para o servidor SAP ASE ou drivers JDBC e ODBC a serem usadas com produtos SAP Business Suite são fornecidas exclusivamente pelo SAP Service Marketplace em: <https://support.sap.com/swdc>.

Para instalações locais, não baixe atualizações para o servidor SAP ASE ou para os drivers JDBC e ODBC diretamente de sites do Sybase. Para obter informações detalhadas sobre os patches que têm suporte para uso com produtos SAP Business Suite locais e em máquinas virtuais do Azure, consulte as seguintes Notas SAP:

* [1590719]
* [1973241]

Informações gerais sobre como executar o SAP Business Suite em SAP ASE podem ser encontradas no [SCN](https://www.sap.com/community/topic/ase.html)

### <a name="sap-ase-configuration-guidelines-for-sap-related-sap-ase-installations-in-azure-vms"></a>Diretrizes de configuração do SAP ASE para instalações do SAP ASE relacionadas ao SAP em VMs do Azure
#### <a name="structure-of-the-sap-ase-deployment"></a>Estrutura da implantação do SAP ASE
De acordo com a descrição geral, os executáveis do SAP ASE devem ser localizados ou instalados no sistema de arquivos raiz da VM (/sybase ). Normalmente, a maioria dos bancos de dados de ferramentas e sistema do SAP ASE não é realmente utilizada de forma concreta pela carga de trabalho do SAP NetWeaver. Portanto, os bancos de dados de ferramentas e sistema (mestre, modelo, saptools, sybmgmtdb, sybsystemdb) podem permanecer no sistema de arquivos raiz também. 

Uma exceção pode ser o banco de dados temporário que contém todas as tabelas de trabalho e tabelas temporárias criadas pelo SAP ASE, que, no caso de algumas cargas de trabalho do SAP ERP e todas do BW, pode exigir um volume de dados ou um volume de operações de E/S maior, o que pode não caber no disco do SO da VM original.

Dependendo da versão do SAPInst/SWPM usada para instalar o sistema, o banco de dados pode conter:

* Um único tempdb do SAP ASE, que é criado durante a instalação do SAP ASE
* Um tempdb do SAP ASE criado pela instalação do SAP ASE e um saptempdb adicional criado pela rotina de instalação do SAP
* Um tempdb do SAP ASE criado pela instalação do SAP ASE e um tempdb adicional criado manualmente (por exemplo, seguindo a Nota SAP [1752266]) para atender aos requisitos de tempdb específicos do ERP/BW

No caso de cargas de trabalho do ERP específicas ou todas do BW, faz sentido, em relação ao desempenho, manter os dispositivos tempdb do tempdb criado adicionalmente (por SWPM ou manualmente) em um sistema de arquivos separado que possa ser representado por um único disco de dados do Azure ou um RAID do Linux que abranja vários discos de dados do Azure. Caso não exista nenhum tempdb adicional, é recomendável criar um (Nota SAP [1752266]).

Para esses sistemas, as etapas a seguir devem ser executadas para o tempdb criado adicionalmente:

* Mova o primeiro diretório do tempdb para o primeiro sistema de arquivos do banco de dados SAP
* Adicione diretórios tempdb a cada um dos discos contendo um sistema de arquivos do banco de dados SAP

Essa configuração permite que o tempdb consuma mais espaço do que a unidade do sistema é capaz de fornecer. Como referência, é possível verificar os tamanhos de diretório tempdb em sistemas existentes de execução local. Ou tal configuração habilitaria números de IOPS em relação ao tempdb, que não poderiam ser fornecidos com a unidade do sistema. Novamente, sistemas que são executados localmente podem ser usados para monitorar a carga de trabalho de E/S no tempdb.

Nunca coloque qualquer diretório do SAP ASE em /mnt ou /mnt/resource da VM. Isso também se aplica ao tempdb, mesmo que os objetos mantidos no tempdb sejam apenas temporários, pois /mnt ou /mnt/resource é um espaço temporário da VM do Azure padrão, que não é persistente. Mais detalhes sobre o espaço temporário da VM do Azure podem ser encontrados [neste artigo][virtual-machines-linux-how-to-attach-disk]

#### <a name="impact-of-database-compression"></a>Impacto da compactação do banco de dados
Em configurações em que a largura de banda de E/S pode se tornar um fator limitante, todas as medidas que reduzem IOPS podem ajudar a ampliar a carga de trabalho que pode ser executada em um cenário de IaaS como o Azure. Portanto, é altamente recomendável para se certificar de que a compactação do SAP ASE seja usada antes de carregar um banco de dados SAP existente no Azure.

A recomendação de executar a compactação antes de carregar no Azure, se ainda não estiver implementada, é indicada por vários motivos:

* A quantidade de dados a ser carregada no Azure é menor
* A duração da execução de compactação é menor, presumindo-se que seja possível usar um hardware mais poderoso com mais CPUs ou maior largura de banda de E/S ou menor latência de E/S local
* Tamanhos menores de banco de dados podem levar a menos custos de alocação de disco

A compactação de LOB e dados funciona em uma VM hospedada em máquinas virtuais do Azure como o faz localmente. Para obter mais detalhes sobre como verificar se a compactação já está em uso em um banco de dados SAP ASE existente, consulte a Nota SAP [1750510]. Para obter informações adicionais sobre a compactação de banco de dados, consulte a Nota SAP [2121797].

#### <a name="using-dbacockpit-to-monitor-database-instances"></a>Usando o DBACockpit para monitorar instâncias do banco de dados
Para sistemas SAP que estão usando o SAP ASE como plataforma de banco de dados, o DBACockpit está acessível como janelas de navegador inseridas no DBACockpit da transação ou como Webdynpro. No entanto, a funcionalidade completa para monitorar e administrar o banco de dados está disponível na implementação do Webdynpro do DBACockpit apenas.

Como com sistemas locais, são necessárias várias etapas para habilitar toda a funcionalidade do SAP NetWeaver usada pela implementação do Webdynpro do DBACockpit. Siga a Nota SAP [1245200] para habilitar o uso de webdynpros e gerar os necessários. Ao seguir as instruções nas notas acima, você também configura o ICM (Gerenciador de Comunicação da Internet) junto com as portas a serem usadas para conexões http e https. A configuração padrão para http tem esta aparência:

> icm/server_port_0 = PROT=HTTP,PORT=8000,PROCTIMEOUT=600,TIMEOUT=600
> 
> icm/server_port_1 = PROT=HTTPS,PORT=443$$,PROCTIMEOUT=600,TIMEOUT=600
> 
> 

e os links gerados no DBACockpit da transação serão semelhantes a este:

> https://`<fullyqualifiedhostname`>:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http://`<fullyqualifiedhostname`>:8000/sap/bc/webdynpro/sap/dba_cockpit
> 
> 

Dependendo de se e como a máquina virtual do Azure que hospeda o sistema SAP está conectado via site a site, multissite ou ExpressRoute (implantação entre instalações), você precisa se certificar de que o ICM esteja usando um nome de host totalmente qualificado que pode ser resolvido na máquina virtual da qual você está tentando abrir o DBACockpit. Consulte a Nota SAP [773830] para entender como o ICM determina o nome do host totalmente qualificado de acordo com os parâmetros de perfil e defina o parâmetro icm/host_name_full explicitamente, se necessário.

Se você implantou a VM em um cenário de somente na nuvem sem conectividade entre instalações entre locais e o Azure, você precisa definir um endereço IP público e um domainlabel. O formato do nome DNS público da VM tem esta aparência:

> `<custom domainlabel`>.`<azure region`>.cloudapp.azure.com
> 
> 

É possível encontrar mais detalhes relacionados ao nome DNS [aqui][virtual-machines-azurerm-versus-azuresm].

Definindo o parâmetro de perfil do SAP icm/host_name_full para o nome DNS da VM do Azure, o link pode ser semelhante a:

> https://mydomainlabel.westeurope.cloudapp.net:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http://mydomainlabel.westeurope.cloudapp.net:8000/sap/bc/webdynpro/sap/dba_cockpit
> 
> 

Nesse caso, você precisa se certificar de:

* Adicione regras de entrada ao Grupo de Segurança de Rede no portal do Azure para as portas TCP/IP usadas para comunicação com o ICM
* Adicionar regras de entrada para a configuração do Firewall do Windows para as portas TCP/IP usadas para se comunicar com o ICM

Para um processo importado automatizado de todas as correções disponíveis, recomenda-se aplicar periodicamente a Nota SAP da coleção de correções aplicável à sua versão do SAP:

* [1558958]
* [1619967]
* [1882376]

Mais informações sobre o DBA Cockpit para SAP ASE podem ser encontradas nas seguintes Notas SAP:

* [1605680]
* [1757924]
* [1757928]
* [1758182]
* [1758496]    
* [1814258]
* [1922555]
* [1956005]

#### <a name="backuprecovery-considerations-for-sap-ase"></a>Considerações sobre backup/recuperação para o SAP ASE
Ao implantar o SAP ASE no Azure, sua metodologia de backup deve ser examinada. Mesmo se o sistema não for um sistema produtivo, o banco de dados SAP hospedado pelo SAP ASE deverá ser submetido a backup periodicamente. Como o armazenamento do Azure mantém três imagens, um backup agora é menos importante no que diz respeito à compensação de uma falha de armazenamento. A razão principal para manter um plano de backup e restauração adequado é mais que você pode compensar erros lógicos/manuais fornecendo funcionalidades de recuperação pontual. Portanto, o objetivo é usar backups para restaurar o banco de dados para um determinado momento ou usar os backups no Azure para propagar outro sistema copiando o banco de dados existente. Por exemplo, você pode transferir de uma configuração do SAP de duas camadas para uma configuração de sistema de três camadas do mesmo sistema restaurando um backup.

A realização do backup e a recuperação de um banco de dados no Azure funcionam da mesma maneira que localmente. Consulte as observações do SAP:

* [1588316]
* [1585981]

para obter detalhes sobre como criar configurações de despejo e agendar backups. Dependendo da sua estratégia e das suas necessidades, você pode configurar despejos de log e banco de dados em um dos discos existentes ou adicionar um disco extra para o backup. Para reduzir o risco de perda de dados em caso de erro, é recomendável usar um disco em que não esteja localizado nenhum diretório/arquivo de banco de dados.

Além da compactação do LOB e dados, o SAP ASE também oferece a compactação de backup. Para ocupar menos espaço com os despejos de banco de dados e de log, é recomendável usar a compactação de backup. Para obter mais informações, consulte a Nota SAP [1588316]. A compactação do backup também é crucial para reduzir a quantidade de dados a ser transferida se você planeja baixar os backups ou VHDs contendo despejos de backup da máquina virtual do Azure para o local.

Não use o espaço temporário da VM do Azure /mnt ou /mnt/resource como o destino do despejo de log ou banco de dados.

#### <a name="performance-considerations-for-backupsrestores"></a>Considerações de desempenho para backups/restaurações
Como em implantações bare-metal, o desempenho de backup/restauração depende de quantos volumes podem ser lidos em paralelo e qual pode ser a taxa de transferência desses volumes. Além disso, o consumo de CPU usado pela compactação de backup pode desempenhar uma função significativa em VMs com apenas até oito threads de CPU. Portanto, é possível supor que:

* Quanto menor o número de discos usados para armazenar os dispositivos de banco de dados, menor é a taxa de transferência geral na leitura
* Quanto menor o número de threads de CPU na VM, mais severo é o impacto da compactação de backup
* Quanto menos destinos (RAID de software do Linux, discos) nos quais gravar o backup houver, menor será a taxa de transferência

Para aumentar o número de destinos nos quais gravar, existem duas opções que podem ser usadas/combinadas, dependendo das suas necessidades:

* Como dividir o volume de destino de backup em vários discos montados para melhorar a taxa de transferência de IOPS nesse volume dividido
* Como criar uma configuração de despejo no nível do SAP ASE, que usa mais de um diretório de destino no qual gravar o despejo

A divisão de um volume em vários discos montados foi discutida anteriormente nesse guia. Para obter mais informações sobre como usar vários diretórios na configuração de despejo do ASE SAP, confira a documentação sobre o procedimento armazenado sp_config_dump que é usado para criar a configuração de despejo no [Infocenter do Sybase](http://infocenter.sybase.com/help/index.jsp).

### <a name="disaster-recovery-with-azure-vms"></a>Recuperação de desastre com VMs do Azure
#### <a name="data-replication-with-sap-sybase-replication-server"></a>Replicação de dados com o Servidor de Replicação do SAP Sybase
Com o SRS (Servidor de Replicação SAP Sybase), o SAP ASE fornece uma solução de espera passiva para transferir transações de banco de dados para um local distante de forma assíncrona. 

A instalação e operação do SRS funcionam tão bem funcionalmente em uma VM hospedada nos Serviços de Máquina Virtual do Azure como localmente.

O ASE HADR por meio do servidor de replicação do SAP NÃO tem suporte neste momento. Ele pode ser testado com e liberado para plataformas Microsoft Azure no futuro.

## <a name="specifics-to-oracle-database-on-windows"></a>Informações específicas do Banco de Dados Oracle no Windows
O software Oracle tem suporte pela Oracle para ser executado no Microsoft Windows Hyper-V e no Azure. Para obter detalhes sobre o suporte geral do Microsoft Hyper-V e Azure, consulte: <https://blogs.oracle.com/cloud/entry/oracle_and_microsoft_join_forces> 

Após o suporte geral, o cenário específico de aplicativos SAP que utilizam os Bancos de Dados Oracle também tem suporte. Os detalhes são indicados nesta parte do documento.

### <a name="oracle-version-support"></a>Suporte de versão do Oracle
As versões do Oracle e as versões de SO correspondentes que têm suporte para execução do SAP no Oracle nas Máquinas Virtuais do Azure podem ser encontrados na Nota SAP [2039619].

Informações gerais sobre como executar o SAP Business Suite em Oracle podem ser encontradas em 1DX: <https://www.sap.com/community/topic/oracle.html>

### <a name="oracle-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Diretrizes de configuração do Oracle para instalações do SAP em VMs do Azure
#### <a name="storage-configuration"></a>Configuração de armazenamento
Há suporte a apenas uma única instância do Oracle usando discos formatados em NTFS. Todos os arquivos de banco de dados devem ser armazenados no sistema de arquivos NTFS com base em VHDs ou Managed Disks. Esses discos são montados na VM do Azure e são baseados no Armazenamento de BLOBs da Página do Azure (<https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>) ou em Managed Disks (<https://docs.microsoft.com/azure/storage/storage-managed-disks-overview>). Qualquer variante de unidades de rede ou compartilhamentos remotos como serviços de arquivo do Azure:

* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx> 
* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx>

**NÃO** têm suporte para arquivos de banco de dados Oracle!

Usando discos baseados no Armazenamento de Blobs de Páginas do Azure ou em Managed Disks, as declarações feitas neste documento no capítulo [Cache para VMs e discos de dados][dbms-guide-2.1] e [Armazenamento do Microsoft Azure][dbms-guide-2.3] aplicam-se a implantações com o Oracle Database também.

Conforme explicado anteriormente na parte geral do documento, existem cotas na taxa de transferência de IOPS para discos do Azure. As cotas exatas dependem do tipo de VM usado. Uma lista de tipos VM com suas cotas pode ser encontrada [aqui (Linux)][virtual-machines-sizes-linux] e [aqui (Windows)][virtual-machines-sizes-windows].

Para identificar os tipos de VM do Azure com suporte, consulte a Nota SAP [1928533].

Desde que a cota de IOPS por disco atual atenda aos requisitos, é possível armazenar todos os arquivos de BD em um único disco montado. 

Se mais IOPS forem necessários, é altamente recomendável usar Pools de Armazenamento do Windows (disponíveis apenas no Windows Server 2012 e superior) ou divisão do Windows para o Windows 2008 R2 para criar um grande dispositivo lógico em vários discos montados (consulte também o capítulo [RAID de software][dbms-guide-2.2] deste documento). Essa abordagem minimiza a sobrecarga administrativa para gerenciar o espaço em disco e evita o esforço para distribuir manualmente os arquivos entre vários discos montados.

#### <a name="backup--restore"></a>Backup/restauração
Para a funcionalidade de backup/restauração, o SAP BR*Tools para Oracle tem suporte da mesma maneira que o Hyper-V e sistemas operacionais Windows Server standard. O RMAN (Gerenciador de Recuperação) da Oracle também tem suporte para backups em disco e restaurações do disco.

#### <a name="high-availability"></a>Alta disponibilidade
O Oracle Data Guard tem suporte para fins de recuperação de desastre e alta disponibilidade. É possível encontrar detalhes [nesta][virtual-machines-windows-classic-configure-oracle-data-guard] documentação.

#### <a name="other"></a>outro
Todos os outros tópicos gerais, como o monitoramento do SAP ou conjuntos de disponibilidade do Azure, se aplicam como descrito nos três primeiros capítulos desse documento para implantações de VMs com o Banco de Dados Oracle.

## <a name="specifics-to-oracle-database-on-oracle-linux"></a>Especificidades do Oracle Database no Oracle Linux
O software Oracle tem suporte pela Oracle para ser executado no Microsoft Windows Hyper-V e no Azure. Para obter detalhes sobre o suporte geral do Microsoft Hyper-V e Azure, consulte: <https://blogs.oracle.com/cloud/entry/oracle_and_microsoft_join_forces> 

Após o suporte geral, o cenário específico de aplicativos SAP que utilizam os Bancos de Dados Oracle também tem suporte. Os detalhes são indicados nesta parte do documento.

### <a name="oracle-version-support"></a>Suporte de versão do Oracle
As versões do Oracle e as versões de SO correspondentes que têm suporte para execução do SAP no Oracle nas Máquinas Virtuais do Azure podem ser encontrados na Nota SAP [2039619].

Informações gerais sobre como executar o SAP Business Suite em Oracle podem ser encontradas em 1DX: <https://www.sap.com/community/topic/oracle.html>

### <a name="oracle-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Diretrizes de configuração do Oracle para instalações do SAP em VMs do Azure
#### <a name="storage-configuration"></a>Configuração de armazenamento
Há suporte para uma única instância do Oracle usando discos formatados em ext3, ext4 e xfs. Todos os arquivos de banco de dados devem ser armazenados no sistemas de arquivos baseados em VHDs ou Managed Disks. Esses discos são montados na VM do Azure e são baseados no Armazenamento de BLOBs da Página do Azure (<https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>) ou em Managed Disks (<https://docs.microsoft.com/azure/storage/storage-managed-disks-overview>). Qualquer variante de unidades de rede ou compartilhamentos remotos como serviços de arquivo do Azure:

* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx> 
* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx>

**NÃO** têm suporte para arquivos de banco de dados Oracle!

Usando discos baseados no Armazenamento de Blobs de Páginas do Azure ou em Managed Disks, as declarações feitas neste documento no capítulo [Cache para VMs e discos de dados][dbms-guide-2.1] e [Armazenamento do Microsoft Azure][dbms-guide-2.3] aplicam-se a implantações com o Oracle Database também.

Conforme explicado anteriormente na parte geral do documento, existem cotas na taxa de transferência de IOPS para discos do Azure. As cotas exatas dependem do tipo de VM usado. Uma lista de tipos VM com suas cotas pode ser encontrada [aqui (Linux)][virtual-machines-sizes-linux] e [aqui (Windows)][virtual-machines-sizes-windows].

Para identificar os tipos de VM do Azure com suporte, consulte a Nota SAP [1928533]

Desde que a cota de IOPS por disco atual atenda aos requisitos, é possível armazenar todos os arquivos de BD em um único disco montado. 

Se mais IOPS forem necessários, é altamente recomendável usar LVM (Gerenciador de Volume lógico) ou MDADM para criar um grande volume lógico usando vários discos montados. Veja também o capítulo [RAID de Software][dbms-guide-2.2] deste documento. Essa abordagem minimiza a sobrecarga administrativa para gerenciar o espaço em disco e evita o esforço para distribuir manualmente os arquivos entre vários discos montados.

#### <a name="backup--restore"></a>Backup/restauração
Para funcionalidade de backup/restauração, há suporte para SAP BR*Tools para Oracle da mesma maneira que em Hyper-V e bare-metal. O RMAN (Gerenciador de Recuperação) da Oracle também tem suporte para backups em disco e restaurações do disco.

#### <a name="high-availability"></a>Alta disponibilidade
O Oracle Data Guard tem suporte para fins de recuperação de desastre e alta disponibilidade. É possível encontrar detalhes [nesta][virtual-machines-windows-classic-configure-oracle-data-guard] documentação.

#### <a name="other"></a>outro
Todos os outros tópicos gerais, como o monitoramento do SAP ou conjuntos de disponibilidade do Azure, se aplicam como descrito nos três primeiros capítulos desse documento para implantações de VMs com o Banco de Dados Oracle.

## <a name="specifics-for-the-sap-maxdb-database-on-windows"></a>Informações específicas para o banco de dados SAP MaxDB no Windows
### <a name="sap-maxdb-version-support"></a>Suporte de versão do SAP MaxDB
No momento, o SAP dá suporte ao SAP MaxDB versão 7.9 para o uso com produtos baseados no SAP NetWeaver no Azure. Todas as atualizações para o servidor SAP MaxDB ou drivers JDBC e ODBC a serem usadas com produtos baseados no SAP NetWeaver são fornecidas exclusivamente pelo SAP Service Marketplace em <https://support.sap.com/swdc>.
Informações gerais sobre como executar o SAP NetWeaver no SAP MaxDB podem ser encontradas em <https://www.sap.com/community/topic/maxdb.html>.

### <a name="supported-microsoft-windows-versions-and-azure-vm-types-for-sap-maxdb-dbms"></a>Versões do Microsoft Windows e tipos de VM do Azure com suporte para DBMS do SAP MaxDB
Para localizar a versão com suporte do Microsoft Windows para DBMS do SAP MaxDB no Azure, consulte:

* [PAM (Matriz de Disponibilidade de Produto) da SAP][sap-pam]
* Nota SAP [1928533]

É altamente recomendável usar a versão mais recente do sistema operacional Microsoft Windows, que é o Microsoft Windows 2012 R2.

### <a name="available-sap-maxdb-documentation"></a>Documentação do SAP MaxDB disponível
Você pode encontrar a lista atualizada da documentação do SAP MaxDB na seguinte Nota SAP [767598]

### <a name="sap-maxdb-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Diretrizes de configuração do SAP MaxDB para instalações do SAP em VMs do Azure
#### <a name="b48cfe3b-48e9-4f5b-a783-1d29155bd573"></a>Configuração de armazenamento
As práticas recomendadas de armazenamento do Azure para SAP MaxDB seguem as recomendações gerais mencionadas no capítulo [Estrutura de uma implantação do RDBMS][dbms-guide-2].

> [!IMPORTANT]
> Como outros bancos de dados, o SAP MaxDB também tem arquivos de log e de dados. No entanto, na terminologia do SAP MaxDB o termo correto é "volume" (não "arquivo"). Por exemplo, há volumes de dados e volumes de log do SAP MaxDB. Não os confunda com volumes de disco do SO. 
> 
> 

Em resumo, você precisa:

* Se você usar contas do Armazenamento do Azure, defina a conta de armazenamento do Azure que contém os volumes de log e dados do SAP MaxDB (ou seja, arquivos) como **LRS (Armazenamento Local Redundante)**, conforme especificado no capítulo [Armazenamento do Microsoft Azure][dbms-guide-2.3].
* Separe o caminho de E/S para volumes de dados (ou seja, arquivos) do SAP MaxDB do caminho de E/S para volumes de log (ou seja, arquivos). Isso significa que os volumes de dados (ou seja, arquivos) do SAP MaxDB precisam ser instalado em uma unidade lógica e os volumes de log (ou seja, arquivos) do SAP MaxDB precisam ser instalado em outra unidade lógica.
* Defina o tipo de cache adequado para cada disco, dependendo de se você o usa para volumes de log ou de dados do SAP MaxDB (ou seja, arquivos) e se você usa o Armazenamento Standard ou Premium do Azure, conforme descrito no capítulo [Cache para VMs e discos de dados][dbms-guide-2.1].
* Desde que a cota de IOPS por disco atenda aos requisitos, é possível armazenar todos os volumes de dados em um único disco montado e também armazenar todos os volumes de log do banco de dados em outro disco único montado.
* Se mais IOPS e/ou espaço forem necessários, é altamente recomendável usar pools de armazenamento do Microsoft Windows (disponíveis apenas no Microsoft Windows Server 2012 e superior) ou distribuição do Microsoft Windows para o Microsoft Windows 2008 R2 para criar um grande dispositivo lógico em vários discos montados. Veja também o capítulo [RAID de Software][dbms-guide-2.2] deste documento. Essa abordagem simplifica a sobrecarga administrativa para gerenciar o espaço em disco e evita o esforço de distribuir manualmente os arquivos entre vários discos montados.
* Para os requisitos de IOPS mais altos, você pode usar o Armazenamento Premium do Azure, que está disponível nas VMs das séries DS e GS.

![Configuração de referência da VM IaaS do Azure para DBMS do SAP MaxDB][dbms-guide-figure-600]

#### <a name="23c78d3b-ca5a-4e72-8a24-645d141a3f5d"></a>Backup e restauração
Ao implantar o SAP MaxDB no Azure, você deve examinar sua metodologia de backup. Mesmo se o sistema não for um sistema produtivo, o banco de dados SAP hospedado pelo SAP MaxDB deverá ser submetido a backup periodicamente. Como o armazenamento do Azure mantém três imagens, um backup agora é menos importante em termos de proteger seu sistema contra falhas de armazenamento e falhas administrativas ou operacionais mais importantes. A razão principal para manter um plano de backup e restauração adequado é para que você possa compensar erros lógicos ou manuais fornecendo funcionalidades de recuperação pontual. Portanto, o objetivo é usar backups para restaurar o banco de dados para um determinado momento ou usar os backups no Azure para propagar outro sistema copiando o banco de dados existente. Por exemplo, você pode transferir de uma configuração do SAP de duas camadas para uma configuração de sistema de três camadas do mesmo sistema restaurando um backup.

A realização do backup e a restauração de um banco de dados no Azure funcionam da mesma maneira que para sistemas locais, então você pode usar ferramentas standard de backup/restauração do MaxDB SAP, que são descritas em um dos documentos da documentação do SAP MaxDB listados na Nota SAP [767598]. 

#### <a name="77cd2fbb-307e-4cbf-a65f-745553f72d2c"></a>Considerações de desempenho para backup e restauração
Como em implantações bare-metal, o desempenho de backup e restauração depende de quantos volumes podem ser lidos em paralelo e a taxa de transferência desses volumes. Além disso, o consumo de CPU usado pela compactação de backup pode desempenhar uma função significativa em VMs com até oito threads de CPU. Portanto, é possível supor que:

* Quanto menor for o número de discos usados para armazenar dispositivos de banco de dados, menor será a taxa de transferência de leitura geral
* Quanto menor o número de threads de CPU na VM, mais severo é o impacto da compactação de backup
* Quanto menos destinos (Diretórios de Faixa, discos) nos quais gravar o backup, menor é a taxa de transferência

Para aumentar o número de destinos nos quais gravar, existem duas opções que podem ser usadas, possivelmente em combinação, dependendo das suas necessidades:

* Dedicando volumes separados para backup
* Dividir o volume de destino de backup em vários discos montados para melhorar a taxa de transferência de IOPS nesse volume de disco distribuído
* Com dispositivos de disco lógico dedicados separados para:
  * Volumes de backup (ou seja, arquivos) do SAP MaxDB
  * Volumes de dados do SAP MaxDB (ou seja, arquivos)
  * Volumes de log do SAP MaxDB (ou seja, arquivos)

A divisão de um volume em vários discos montados foi discutida anteriormente no capítulo [RAID de software][dbms-guide-2.2] deste documento. 

#### <a name="f77c1436-9ad8-44fb-a331-8671342de818"></a>Outros
Todos os outros tópicos gerais, como o monitoramento do SAP ou conjuntos de disponibilidade do Azure, também se aplicam como descrito nos três primeiros capítulos desse documento para implantações de VMs com o banco de dados SAP MaxDB.
Outras configurações específicas do SAP MaxDB são transparentes para VMs do Azure e são descritas em diferentes documentos listados na Nota SAP [767598] e nestas Notas SAP:

* [826037] 
* [1139904]
* [1173395]

## <a name="specifics-for-sap-livecache-on-windows"></a>Informações específicas para o SAP liveCache no Windows
### <a name="sap-livecache-version-support"></a>Suporte de versão do SAP liveCache
A versão mínima do SAP liveCache com suporte nas Máquinas Virtuais do Azure é **SAP LC/LCAPPS 10.0 SP 25**, incluindo **liveCache 7.9.08.31** e **LCA-Build 25**, lançado para **EhP 2 for SAP SCM 7.0** e superior.

### <a name="supported-microsoft-windows-versions-and-azure-vm-types-for-sap-livecache-dbms"></a>Versões do Microsoft Windows e tipos de VM do Azure com suporte para DBMS do SAP liveCache
Para localizar a versão com suporte do Microsoft Windows para SAP liveCache no Azure, consulte:

* [PAM (Matriz de Disponibilidade de Produto) da SAP][sap-pam]
* Nota SAP [1928533]

É altamente recomendável usar a versão mais recente do sistema operacional Microsoft Windows Server. 

### <a name="sap-livecache-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Diretrizes de configuração do SAP liveCache para instalações do SAP em VMs do Azure
#### <a name="recommended-azure-vm-types"></a>Tipos de VM do Azure recomendados
Como SAP liveCache é um aplicativo que executa cálculos enormes, a quantidade e a velocidade de RAM e CPU têm uma grande influência no desempenho do SAP liveCache. 

Para os tipos de VM do Azure com suporte pelo SAP (Nota SAP [1928533]), todos os recursos de CPU virtual alocados para a máquina virtual são apoiados por recursos de CPU físicos dedicados do hipervisor. Não ocorre o excesso de provisionamento (e, portanto, nenhuma competição por recursos de CPU).

Da mesma forma, para todos os tipos de instância VM do Azure com suporte pelo SAP, a memória VM é 100% mapeada para a memória física; o excesso de provisionamento (sobreposição), por exemplo, não é usado.

Dessa perspectiva, é altamente recomendável usar o novo tipo de VM do Azure série D ou série DS (em combinação com o Armazenamento Premium do Azure), pois essas séries têm processadores 60% mais rápidos que a série A. Para a carga de RAM e CPU mais alta, você pode usar VMs das séries G e GS (em combinação com o Armazenamento Premium do Azure) com a família E5 v3 de processadores Intel® Xeon® mais recente, que tem o dobro de memória e quatro vezes o SSDs (armazenamento da unidade de estado sólido) da série D/DS.

#### <a name="storage-configuration"></a>Configuração de armazenamento
Como o SAP liveCache se baseia na tecnologia SAP MaxDB, todas as práticas recomendadas de armazenamento do Azure mencionadas para o SAP MaxDB no capítulo [Configuração de armazenamento][dbms-guide-8.4.1] também são válidas para o SAP liveCache. 

#### <a name="dedicated-azure-vm-for-livecache"></a>VM do Azure dedicada para o liveCache
Como o SAP liveCache usa intensivamente a potência computacional, para o uso produtivo é altamente recomendável implantar em uma máquina virtual do Azure dedicada. 

![VM do Azure dedicada para liveCache para caso de uso produtivo][dbms-guide-figure-700]

#### <a name="backup-and-restore"></a>Backup e restauração
O backup e a restauração, incluindo considerações de desempenho, já estão descritos nos capítulos relevantes do SAP MaxDB, [Backup e restauração][dbms-guide-8.4.2] e [Considerações de desempenho para backup e restauração][dbms-guide-8.4.3]. 

#### <a name="other"></a>outro
Todos os outros tópicos gerais já estão descritos [neste][dbms-guide-8.4.4] capítulo relevante sobre o SAP MaxDB. 

## <a name="specifics-for-the-sap-content-server-on-windows"></a>Informações específicas para o SAP Content Server no Windows
O SAP Content Server é um componente baseado em servidor separado para armazenar conteúdos como documentos eletrônicos em diferentes formatos. O SAP Content Server é fornecido pelo desenvolvimento de tecnologia e deve ser usada entre aplicativos para todos os aplicativos SAP. Ele é instalado em um sistema separado. O conteúdo normal é o material de treinamento e a documentação do Depósito de Dados de Conhecimento ou desenhos técnicos provenientes do Sistema de Gerenciamento de Documentos do mySAP PLM. 

### <a name="sap-content-server-version-support"></a>Suporte de versão do SAP Content Server
No momento o SAP dá suporte ao:

* **SAP Content Server** com a versão **6.50 (e superiores)**
* **SAP MaxDB versão 7.9**
* **IIS (Servidor de informações da Internet) da Microsoft versão 8.0 (e posterior)**

É altamente recomendável usar a versão mais recente do SAP Content Server, que no momento da redação deste documento é **6.50 SP4**, e a versão mais recente do **IIS da Microsoft 8.5**. 

Verifique as versões mais recentes com suporte do SAP Content Server e do IIS da Microsoft no [PAM (Matriz de Disponibilidade de Produto) da SAP][sap-pam].

### <a name="supported-microsoft-windows-and-azure-vm-types-for-sap-content-server"></a>Tipos de VM do Azure e Microsoft Windows com suporte para o SAP Content Server
Para descobrir a versão com suporte do Windows para o SAP Content Server no Azure, consulte:

* [PAM (Matriz de Disponibilidade de Produto) da SAP][sap-pam]
* Nota SAP [1928533]

É altamente recomendável usar a versão mais recente do Microsoft Windows Server.

### <a name="sap-content-server-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Diretrizes de configuração do SAP Content Server para instalações do SAP em VMs do Azure
#### <a name="storage-configuration"></a>Configuração de armazenamento
Se você configurar o SAP Content Server para armazenar arquivos no banco de dados do SAP MaxDB, todas as práticas recomendadas de armazenamento do Azure mencionadas para o SAP MaxDB no capítulo [Configuração de armazenamento][dbms-guide-8.4.1] também serão válidas para o cenário do SAP Content Server. 

Se você configurar o SAP Content Server para armazenar arquivos no sistema de arquivos, será recomendável usar uma unidade lógica dedicada. O uso de Espaços de Armazenamento do Windows permite que você também aumente o tamanho do disco lógico e a taxa de transferência de IOPS, conforme descrito no capítulo [RAID de software][dbms-guide-2.2]. 

#### <a name="sap-content-server-location"></a>Local do SAP Content Server
O SAP Content Server deve ser implantado na mesma região do Azure e VNET do Azure em que o sistema SAP está implantado. Você é livre para decidir se deseja implantar os componentes do SAP Content Server em uma VM do Azure dedicada ou na mesma VM em que o sistema SAP está sendo executado. 

![VM do Azure dedicada para SAP Content Server][dbms-guide-figure-800]

#### <a name="sap-cache-server-location"></a>Local do SAP Cache Server
O SAP Cache Server é um componente adicional com base em servidor para fornecer acesso a documentos (em cache) localmente. O SAP Cache Server armazena em cache os documentos de um SAP Content Server. Isso é para otimizar o tráfego de rede se documentos precisam ser recuperados de mais de uma vez de locais diferentes. A regra geral é que o SAP Cache Server precisa estar fisicamente próximo ao cliente que acessa o SAP Cache Server. 

Aqui você tem duas opções:

1. **O cliente é um sistema SAP de back-end** Se um sistema SAP de back-end estiver configurado para acessar o SAP Content Server, o sistema SAP será um cliente. Como o sistema SAP e o SAP Content Server são implantados na mesma região do Azure, no mesmo datacenter do Azure, eles estão fisicamente próximos. Portanto, não é necessário ter um SAP Cache Server dedicado. Os clientes da interface do usuário do SAP (GUI do SAP ou navegador da Web) acessam o sistema SAP diretamente e o sistema SAP recupera documentos do SAP Content Server.
2. **O cliente é um navegador da Web local** O SAP Content Server pode ser configurado para ser acessado diretamente pelo navegador da Web. Nesse caso, um navegador da Web em execução no local é um cliente do SAP Content Server. O datacenter local e o datacenter do Azure são colocados em diferentes locais físicos (idealmente próximos entre si). Seu datacenter local é conectado ao Azure por meio do ExpressRoute ou VPN site a site do Azure. Embora as duas opções ofereçam uma conexão de rede VPN segura para o Azure, a conexão de rede site a site não oferece uma largura de banda de rede e SLA de latência entre o datacenter local e o datacenter do Azure. Para acelerar o acesso a documentos, você pode realizar uma das seguintes ações:
   1. Instale o SAP Cache Server local, próximo ao navegador da Web local (opção [nesta][dbms-guide-900-sap-cache-server-on-premises] Figura)
   2. Configure o ExpressRoute do Azure, que oferece uma conexão de rede dedicada de alta velocidade e baixa latência entre o datacenter local e o datacenter do Azure.

![Opção de instalação local do SAP Cache Server][dbms-guide-figure-900]
<a name="642f746c-e4d4-489d-bf63-73e80177a0a8"></a>

#### <a name="backup--restore"></a>Backup/restauração
Se você configurar o SAP Content Server para armazenar arquivos no banco de dados do SAP MaxDB, as considerações de desempenho e o procedimento de backup/restauração já estão descritas nos capítulos [Backup e restauração][dbms-guide-8.4.2] e [Considerações de desempenho para backup e restauração][dbms-guide-8.4.3] sobre o SAP MaxDB. 

Se você configura o SAP Content Server para armazenar arquivos no sistema de arquivos, uma opção é executar o backup/restauração manual da estrutura do arquivo inteiro na qual os documentos estão localizados. Semelhante ao backup/restauração do SAP MaxDB, é recomendável ter um volume de disco dedicado para fins de backup. 

#### <a name="other"></a>Outros
Outras configurações específicas do SAP Content Server são transparentes para VMs do Azure e são descritas em vários documentos e Notas SAP:

* <https://service.sap.com/contentserver> 
* Nota SAP [1619726]  

## <a name="specifics-to-ibm-db2-for-luw-on-windows"></a>Informações específicas para o IBM DB2 para LUW no Windows
Com o Microsoft Azure, você pode facilmente migrar seu aplicativo SAP existente em execução no IBM DB2 para LUW (Linux, UNIX e Windows) para máquinas virtuais do Azure. Com o SAP no IBM DB2 para LUW, os administradores e desenvolvedores ainda podem usar as mesmas ferramentas de desenvolvimento e administração que estão disponíveis localmente.
As informações gerais sobre como executar o SAP Business Suite no IBM DB2 para LUW podem ser encontradas na SCN (SAP Community Network) em <https://www.sap.com/community/topic/db2-for-linux-unix-and-windows.html>.

Para obter informações adicionais e atualizações sobre o SAP no DB2 para LUW no Azure, consulte a Nota SAP [2233094]. 

### <a name="ibm-db2-for-linux-unix-and-windows-version-support"></a>Suporte de versão do IBM DB2 para Linux, UNIX e Windows
Há suporte para o SAP no IBM DB2 para LUW nos serviços de máquina virtual do Microsoft Azure a partir da versão 10.5 do DB2.

Para obter informações sobre os tipos de VM do Azure e produtos SAP com suporte, consulte a Nota SAP [1928533].

### <a name="ibm-db2-for-linux-unix-and-windows-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Diretrizes de configuração do IBM DB2 para Linux, UNIX e Windows para instalações do SAP em VMs do Azure
#### <a name="storage-configuration"></a>Configuração de armazenamento
Todos os arquivos de banco de dados devem ser armazenados no sistema de arquivos NTFS com base em discos anexados diretamente. Esses discos são montados na VM do Azure e são baseados no Armazenamento de BLOBs de Páginas do Azure (<https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>) ou em Managed Disks (<https://docs.microsoft.com/azure/storage/storage-managed-disks-overview>). Qualquer variante de unidades de rede ou compartilhamentos remotos, como os seguintes serviços de arquivo do Azure **NÃO** tem suporte para arquivos de banco de dados: 

* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx>
* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx>

Se você estiver usando discos com base no Armazenamento de BLOBs de Páginas do Azure ou Managed Disks, as declarações feitas no capítulo [Estrutura de uma implantação do RDBMS][dbms-guide-2] deste documento também se aplicarão a implantações com o Banco de Dados IBM DB2 para LUW. 

Conforme explicado anteriormente na parte geral do documento, existem cotas na taxa de transferência de IOPS para discos. As cotas exatas dependem do tipo de VM usado. Uma lista de tipos VM com suas cotas pode ser encontrada [aqui (Linux)][virtual-machines-sizes-linux] e [aqui (Windows)][virtual-machines-sizes-windows].

Desde que a cota de IOPS por disco atual seja suficiente, é possível armazenar todos os arquivos de banco de dados no único disco montado. 

Para considerações sobre o desempenho, consulte também o capítulo “Data Safety and Performance Considerations for Database Directories” (Considerações sobre segurança de dados e desempenho para diretórios de banco de dados) nos guias de instalação SAP.

Como alternativa, você pode usar Pools de Armazenamento do Windows (disponíveis apenas no Windows Server 2012 e superior) ou a divisão do Windows para o Windows 2008 R2, conforme descrito no capítulo [RAID de software][dbms-guide-2.2] deste documento, para criar um grande dispositivo lógico em vários discos.
Para os discos que contém os caminhos de armazenamento do DB2 para os sapdata e saptmp, você deve especificar um tamanho de setor do disco físico de 512 KB. Ao usar Pools de Armazenamento do Windows, você deve criá-los manualmente por meio da interface de linha de comando usando o parâmetro "-LogicalSectorSizeDefault". Para obter mais informações, consulte <https://technet.microsoft.com/itpro/powershell/windows/storage/new-storagepool>.

#### <a name="backuprestore"></a>Backup/restauração
A funcionalidade de backup/restauração para o IBM DB2 para LUW tem suporte da mesma maneira que no Hyper-V e sistemas operacionais Windows Server standard.

Você deve se certificar de que tenha uma estratégia de backup do banco de dados válida em vigor. 

Como em implantações bare-metal, o desempenho de backup/restauração depende de quantos volumes podem ser lidos em paralelo e qual pode ser a taxa de transferência desses volumes. Além disso, o consumo de CPU usado pela compactação de backup pode desempenhar uma função significativa em VMs com apenas até oito threads de CPU. Portanto, é possível supor que:

* Quanto menor o número de discos usados para armazenar os dispositivos de banco de dados, menor é a taxa de transferência geral na leitura
* Quanto menor o número de threads de CPU na VM, mais severo é o impacto da compactação de backup
* Quanto menos destinos (Diretórios de Faixa, discos) nos quais gravar o backup, menor é a taxa de transferência

Para aumentar o número de destinos nos quais gravar, é possível usar/combinar duas opções dependendo das suas necessidades:

* Como dividir o volume de destino de backup em vários discos para melhorar a taxa de transferência de IOPS nesse volume dividido
* Usando mais de um diretório de destino no qual gravar o backup

#### <a name="high-availability-and-disaster-recovery"></a>Alta disponibilidade e recuperação de desastre
Não há suporte para o MSCS (Microsoft Cluster Server).

Há suporte para HADR (alta disponibilidade e recuperação de desastre) do DB2. Se as máquinas virtuais da configuração de HA tiverem uma resolução de nome funcionando, a configuração no Azure não será diferente de nenhuma configuração feita localmente. Não é recomendável confiar apenas na resolução de IP.

Não use a replicação geográfica para as contas de armazenamento que armazenam os discos de banco de dados. Para obter mais informações, confira o capítulo [Armazenamento do Microsoft Azure][dbms-guide-2.3] e o capítulo [Alta disponibilidade e recuperação de desastre com VMs do Azure][dbms-guide-3].

#### <a name="other"></a>Outros
Todos os outros tópicos gerais, como o monitoramento do SAP ou conjuntos de disponibilidade do Azure, se aplicam como descrito nos três primeiros capítulos desse documento para implantações de VMs com o IBM DB2 para LUW. 

Veja também o capítulo [Resumo geral do SQL Server para SAP no Azure][dbms-guide-5.8].

## <a name="specifics-to-ibm-db2-for-luw-on-linux"></a>Informações específicas para o IBM DB2 para LUW no Linux
Com o Microsoft Azure, você pode facilmente migrar seu aplicativo SAP existente em execução no IBM DB2 para LUW (Linux, UNIX e Windows) para máquinas virtuais do Azure. Com o SAP no IBM DB2 para LUW, os administradores e desenvolvedores ainda podem usar as mesmas ferramentas de desenvolvimento e administração que estão disponíveis localmente. As informações gerais sobre como executar o SAP Business Suite no IBM DB2 para LUW podem ser encontradas na SCN (SAP Community Network) em <https://www.sap.com/community/topic/db2-for-linux-unix-and-windows.html>.

Para obter informações adicionais e atualizações sobre o SAP no DB2 para LUW no Azure, consulte a Nota SAP [2233094].

### <a name="ibm-db2-for-linux-unix-and-windows-version-support"></a>Suporte de versão do IBM DB2 para Linux, UNIX e Windows
Há suporte para o SAP no IBM DB2 para LUW nos serviços de máquina virtual do Microsoft Azure a partir da versão 10.5 do DB2.

Para obter informações sobre os tipos de VM do Azure e produtos SAP com suporte, consulte a Nota SAP [1928533].

### <a name="ibm-db2-for-linux-unix-and-windows-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Diretrizes de configuração do IBM DB2 para Linux, UNIX e Windows para instalações do SAP em VMs do Azure
#### <a name="storage-configuration"></a>Configuração de armazenamento
Todos os arquivos de banco de dados devem ser armazenados no sistema de arquivos com base em discos de conexão direta. Esses discos são montados na VM do Azure e são baseados no Armazenamento de BLOBs de Páginas do Azure (<https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>) ou em Managed Disks (<https://docs.microsoft.com/azure/storage/storage-managed-disks-overview>). Qualquer variante de unidades de rede ou compartilhamentos remotos, como os seguintes serviços de arquivo do Azure **NÃO** tem suporte para arquivos de banco de dados:

* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx>
* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx>

Se você estiver usando discos com base no Armazenamento de BLOBs de Páginas do Azure, as declarações feitas no capítulo [Estrutura de uma implantação do RDBMS][dbms-guide-2] deste documento também se aplicarão a implantações com o banco de dados do IBM DB2 para LUW.

Conforme explicado anteriormente na parte geral do documento, existem cotas na taxa de transferência de IOPS para discos. As cotas exatas dependem do tipo de VM usado. Uma lista de tipos VM com suas cotas pode ser encontrada [aqui (Linux)][virtual-machines-sizes-linux] e [aqui (Windows)][virtual-machines-sizes-windows].

Desde que a cota de IOPS por disco atual seja suficiente, é possível armazenar todos os arquivos de banco de dados no único disco.

Para considerações sobre o desempenho, consulte também o capítulo “Data Safety and Performance Considerations for Database Directories” (Considerações sobre segurança de dados e desempenho para diretórios de banco de dados) nos guias de instalação SAP.

Como alternativa, você pode usar o LVM (Gerenciador de Volume Lógico) ou o MDADM, conforme descrito no capítulo [RAID de software][dbms-guide-2.2] deste documento para criar um dispositivo lógico grande em vários discos.
Para os discos que contém os caminhos de armazenamento do DB2 para os sapdata e saptmp, você deve especificar um tamanho de setor do disco físico de 512 KB.

#### <a name="backuprestore"></a>Backup/restauração
A funcionalidade de backup/restauração para o IBM DB2 para LUW tem suporte da mesma maneira que na instalação padrão do Linux local.

Você deve se certificar de que tenha uma estratégia de backup do banco de dados válida em vigor.

Como em implantações bare-metal, o desempenho de backup/restauração depende de quantos volumes podem ser lidos em paralelo e qual pode ser a taxa de transferência desses volumes. Além disso, o consumo de CPU usado pela compactação de backup pode desempenhar uma função significativa em VMs com apenas até oito threads de CPU. Portanto, é possível supor que:

* Quanto menor o número de discos usados para armazenar os dispositivos de banco de dados, menor é a taxa de transferência geral na leitura
* Quanto menor o número de threads de CPU na VM, mais severo é o impacto da compactação de backup
* Quanto menos destinos (Diretórios de Faixa, discos) nos quais gravar o backup, menor é a taxa de transferência

Para aumentar o número de destinos nos quais gravar, é possível usar/combinar duas opções dependendo das suas necessidades:

* Como dividir o volume de destino de backup em vários discos para melhorar a taxa de transferência de IOPS nesse volume dividido
* Usando mais de um diretório de destino no qual gravar o backup

#### <a name="high-availability-and-disaster-recovery"></a>Alta disponibilidade e recuperação de desastre
Há suporte para HADR (alta disponibilidade e recuperação de desastre) do DB2. Se as máquinas virtuais da configuração de HA tiverem uma resolução de nome funcionando, a configuração no Azure não será diferente de nenhuma configuração feita localmente. Não é recomendável confiar apenas na resolução de IP.

Não use a replicação geográfica para as contas de armazenamento que armazenam os discos de banco de dados. Para obter mais informações, confira o capítulo [Armazenamento do Microsoft Azure][dbms-guide-2.3] e o capítulo [Alta disponibilidade e recuperação de desastre com VMs do Azure][dbms-guide-3].

#### <a name="other"></a>Outros
Todos os outros tópicos gerais, como o monitoramento do SAP ou conjuntos de disponibilidade do Azure, se aplicam como descrito nos três primeiros capítulos desse documento para implantações de VMs com o IBM DB2 para LUW.

Veja também o capítulo [Resumo geral do SQL Server para SAP no Azure][dbms-guide-5.8].

