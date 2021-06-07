---
title: Implantação de Máquinas Virtuais do Azure para SAP NetWeaver | Microsoft Docs
description: Saiba como implantar o software SAP em máquinas virtuais Linux no Azure.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: MSSedusch
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 1c4f1951-3613-4a5a-a0af-36b85750c84e
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/16/2020
ms.author: sedusch
ms.openlocfilehash: 5d6ea75936383388a57a7822f054e0ea7297471e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101695508"
---
# <a name="azure-virtual-machines-deployment-for-sap-netweaver"></a>Implantação de Máquinas Virtuais do Azure para SAP NetWeaver

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
[2367194]:https://launchpad.support.sap.com/#/notes/2367194

[azure-cli]:../../../cli-install-nodejs.md
[azure-cli-2]:https://docs.microsoft.com/cli/azure/install-azure-cli
[azure-portal]:https://portal.azure.com
[azure-ps]:/powershell/azure/
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits

[dbms-guide]:dbms-guide.md (Implantação do DBMS de Máquinas Virtuais do Azure para SAP)
[dbms-guide-2.1]:dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f (Cache para VMs e VHDs)
[dbms-guide-2.2]:dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 (RAID de software)
[dbms-guide-2.3]:dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 (Armazenamento do Microsoft Azure)
[dbms-guide-2]:dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 (Estrutura de uma implantação do RDBMS)
[dbms-guide-3]:dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 (Alta disponibilidade e recuperação de desastre com VMs do Azure)
[dbms-guide-5.5.1]:dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 (SQL Server 2012 SP1 CU4 e posterior)
[dbms-guide-5.5.2]:dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b (SQL Server 2012 SP1 CU3 e versões anteriores)
[dbms-guide-5.6]:dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 (Usando uma imagem do SQL Server no Azure Marketplace)
[dbms-guide-5.8]:dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 (Resumo geral do SQL Server para o SAP no Azure)
[dbms-guide-5]:dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 (Especificações do RDBMS do SQL Server)
[dbms-guide-8.4.1]:dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573 (Configuração de armazenamento)
[dbms-guide-8.4.2]:dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d (Backup e restauração)
[dbms-guide-8.4.3]:dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c (Considerações sobre desempenho para backup e restauração)
[dbms-guide-8.4.4]:dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818 (Outros)
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

[deployment-guide]:deployment-guide.md (Implantação de Máquinas Virtuais do Azure para SAP)
[deployment-guide-2.2]:deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 (Recursos do SAP)
[deployment-guide-3.1.2]:deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab (Implantando uma VM usando uma imagem personalizada)
[deployment-guide-3.2]:deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281 (Cenário 1: Implantando uma VM por meio do Azure Marketplace para o SAP)
[deployment-guide-3.3]:deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2 (Cenário 2: Implantando uma VM com uma imagem personalizada para SAP)
[deployment-guide-3.4]:deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1 (Cenário 3: Movendo uma VM do local usando um VHD do Azure não generalizado com o SAP)
[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e (Cenários de implantação de VMs para o SAP no Microsoft Azure)
[deployment-guide-4.1]:deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 (Implantando cmdlets do Azure PowerShell)
[deployment-guide-4.2]:deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e (Baixar e importar os cmdlets do PowerShell relevantes ao SAP)
[deployment-guide-4.3]:deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc (Ingressar uma VM em um domínio local – somente Windows)
[deployment-guide-4.4.2]:deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542 (Linux)
[deployment-guide-4.4]:deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d (Baixar, instalar e habilitar o Agente de VM do Azure)
[deployment-guide-4.5.1]:deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4 (Azure PowerShell)
[deployment-guide-4.5.2]:deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f (CLI do Azure)
[deployment-guide-4.5]:deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca (Configurar a Extensão para SAP do Azure)
[deployment-guide-configure-new-extension-ps]:deployment-guide.md#2ad55a0d-9937-4943-9dd2-69bc2b5d3de0 (Configurar a nova extensão do Azure para SAP com Azure PowerShell)
[deployment-guide-configure-new-extension-cli]:deployment-guide.md#c8749c24-fada-42ad-b114-f9aae2dc37da (Configurar a nova extensão do Azure para SAP com CLI do Azure)
[deployment-guide-5.1]:deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2 (Verificação de preparação para a Extensão para SAP do Azure)
[deployment-guide-5.1-new]:deployment-guide.md#7bf24f59-7347-4c7a-b094-4693e4687ee5 (Verificação de preparação para a nova extensão do Azure para SAP)
[deployment-guide-5.2]:deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1 (Verificação de integridade da configuração da Extensão para SAP do Azure)
[deployment-guide-5.2-new]:deployment-guide.md#464ac96d-7d3c-435d-a5ae-3faf3bfef4b3 (Verificação de integridade para a nova extensão do Azure para a configuração do SAP)
[deployment-guide-5.3]:deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8 (Solução de problemas da Extensão para SAP do Azure)
[deployment-guide-5.3-new]:deployment-guide.md#b7afb8ef-a64c-495d-bb37-2af96688c530 (Solucionando problemas da nova extensão do Azure para SAP)
[deployment-guide-contact-support]:deployment-guide.md#3ba34cfc-c9bb-4648-9c3c-88e8b9130ca2 (Solução de problemas de extensão do Azure para SAP – contate o suporte)
[deployment-guide-run-the-script]:deployment-guide.md#0d2847ad-865d-4a4c-a405-f9b7baaa00c7 (Solução de problemas da extensão do Azure para SAP – executar o script de instalação)
[deployment-guide-redeploy-after-sysprep]:deployment-guide.md#2cd61f22-187d-42ed-bb8c-def0c983d756 (Solucionando problemas da extensão do Azure para SAP – reimplantar após Sysprep)
[deployment-guide-fix-internet-connection]:deployment-guide.md#e92bc57d-80d9-4a2b-a2f4-16713a22ad89 ( Solução de problemas de extensão do Azure para SAP – corrigir conexão com a Internet)


[deployment-guide-configure-monitoring-scenario-1]:deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b (Configurar Extensão da VM)
[deployment-guide-configure-proxy]:deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d (Configurar o proxy)
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
[deployment-guide-troubleshooting-chapter]:deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b (Verificações e solução de problemas)

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

[msdn-set-Azvmaemextension]:https://docs.microsoft.com/powershell/module/az.compute/set-azvmaemextension

[planning-guide]:planning-guide.md (Planejamento e implementação de Máquinas Virtuais do Azure para o SAP)
[planning-guide-1.2]:planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff (Recursos)
[planning-guide-11]:planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058 (Alta disponibilidade e recuperação de desastre para o SAP NetWeaver em execução em Máquinas Virtuais do Azure)
[planning-guide-11.4.1]:planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77 (Alta disponibilidade para servidores de aplicativos SAP)
[planning-guide-11.5]:planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f (Usando a inicialização automática para instâncias do SAP)
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803 (Somente em nuvem – implantações de máquinas virtuais no Azure sem dependências na rede do cliente local)
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10 (Entre locais – implantação de uma ou várias VMs do SAP no Azure totalmente integradas à rede local)
[planning-guide-3.1]:planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a (Regiões do Azure)
[planning-guide-3.2.1]:planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358 (Domínios de falha)
[planning-guide-3.2.2]:planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560 (Domínios de atualização)
[planning-guide-3.2.3]:planning-guide.md#18810088-f9be-4c97-958a-27996255c665 (Conjuntos de disponibilidade do Azure)
[planning-guide-3.2]:planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77 (Conceito de máquinas virtuais do Microsoft Azure)
[planning-guide-5.1.1]:planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53 (Movendo uma VM do local para o Azure com um disco não generalizado)
[planning-guide-5.1.2]:planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c (Implantando uma VM com uma imagem específica do cliente)
[planning-guide-5.2.1]:planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef (Preparação para mover uma VM do local para o Azure com um disco não generalizado)
[planning-guide-5.2.2]:planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3 (Preparação para implantar uma VM com uma imagem específica do cliente para o SAP)
[planning-guide-5.2]:planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7 (Preparando VMs com o SAP para o Azure)
[planning-guide-5.3.1]:planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79 (Diferença entre um disco e uma imagem do Azure)
[planning-guide-5.3.2]:planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a (Carregando um VHD por meio do local no Azure)
[planning-guide-5.4.2]:planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1 (Copiando discos entre contas de Armazenamento do Azure)
[planning-guide-5.5.1]:planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646 (Estrutura de VM/VHD para implantações SAP)
[planning-guide-5.5.3]:planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d (Configurando a montagem automática para discos anexados)
[planning-guide-9.1]:planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3 (Solução de monitoramento do Azure para o SAP)
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
[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd (Rede do Microsoft Azure)
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f (Armazenamento: Armazenamento do Microsoft Azure e discos de dados)

[resource-group-authoring-templates]:../../../resource-group-authoring-templates.md
[resource-group-overview]:../../../azure-resource-manager/management/overview.md
[resource-groups-networking]:../../../networking/network-overview.md
[sap-pam]:https://support.sap.com/pam (Matriz de Disponibilidade de Produto SAP)
[sap-templates-2-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-2-tier-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image-md%2Fazuredeploy.json
[sap-templates-2-tier-os-disk]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk%2Fazuredeploy.json
[sap-templates-2-tier-os-disk-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk-md%2Fazuredeploy.json
[sap-templates-2-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image%2Fazuredeploy.json
[sap-templates-2-tier-user-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image-md%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-md%2Fazuredeploy.json
[sap-templates-3-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-user-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image-md%2Fazuredeploy.json
[storage-azure-cli]:../../../storage/common/storage-azure-cli.md
[storage-azure-cli-copy-blobs]:../../../storage/common/storage-azure-cli.md#copy-blobs
[storage-introduction]:../../../storage/common/storage-introduction.md
[storage-powershell-guide-full-copy-vhd]:../../../storage/common/storage-powershell-guide-full.md#how-to-copy-blobs-from-one-storage-container-to-another
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
[virtual-machines-linux-cli-deploy-templates]:../../linux/cli-deploy-templates.md (Implantar e gerenciar máquinas virtuais usando modelos do Azure Resource Manager e a CLI do Azure)
[virtual-machines-deploy-rmtemplates-powershell]:../../virtual-machines-windows-ps-manage.md (Gerenciar máquinas virtuais usando o Azure Resource Manager e o PowerShell)
[virtual-machines-windows-agent-user-guide]:../../extensions/agent-windows.md
[virtual-machines-linux-agent-user-guide]:../../extensions/agent-linux.md
[virtual-machines-linux-agent-user-guide-command-line-options]:../../extensions/agent-linux.md#command-line-options
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
[virtual-machines-manage-availability]:../../linux/manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:../../windows/quick-create-powershell.md
[virtual-machines-sizes]:../../linux/sizes.md
[virtual-machines-windows-classic-ps-sql-alwayson-availability-groups]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md
[virtual-machines-windows-classic-ps-sql-int-listener]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener.md
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]:../../../azure-sql/virtual-machines/windows/business-continuity-high-availability-disaster-recovery-hadr-overview.md
[virtual-machines-sql-server-infrastructure-services]:../../../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md
[virtual-machines-sql-server-performance-best-practices]:../../../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md
[virtual-machines-upload-image-windows-resource-manager]:../../windows/upload-image.md
[virtual-machines-windows-tutorial]:../../windows/quick-create-portal.md
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/documentation/templates/sql-server-2014-alwayson-dsc/
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
[qs-configure-powershell-windows-vm]:../../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md
[qs-configure-cli-windows-vm]:../../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md
[howto-assign-access-powershell]:../../../active-directory/managed-identities-azure-resources/howto-assign-access-powershell.md
[howto-assign-access-cli]:../../../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

As Máquinas Virtuais do Azure são a solução para as organizações que precisam de recursos de computação e armazenamento, no mínimo de tempo e sem ciclos de compra longos. Você pode usar máquinas virtuais do Azure para implantar aplicativos clássicos, como aplicativos baseados no SAP NetWeaver, no Azure. Estenda a confiabilidade e a disponibilidade de um aplicativo sem recursos locais adicionais. As Máquinas Virtuais do Azure dão suporte à conectividade entre locais, o que permite que você integre as Máquinas Virtuais do Azure aos seus domínios locais, a nuvens privadas e à estrutura do sistema SAP.

Neste artigo, abordaremos as etapas para implantar aplicativos SAP em VMs (máquinas virtuais) no Azure, incluindo opções alternativas de implantação e solução de problemas. Este artigo se baseia nas informações de [Planejamento e implementação de Máquinas Virtuais do Azure para SAP NetWeaver][planning-guide]. Isso também complementa a documentação de instalação do SAP e as Notas SAP, que são os principais recursos de instalação e implantação de software SAP.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

A configuração de uma máquina virtual do Azure para implantação de software SAP envolve várias etapas e recursos. Antes de começar, verifique se você atende aos pré-requisitos para instalar o software SAP em máquinas virtuais no Azure.

### <a name="local-computer"></a>Computador local

Para gerenciar VMs Windows ou Linux, você pode usar um script do PowerShell e o portal do Azure. Para ambas as ferramentas, você precisa de um computador local que execute o Windows 7 ou uma versão posterior do Windows. Se desejar gerenciar apenas VMs do Linux e usar um computador Linux para essa tarefa, você poderá usar a CLI do Azure.

### <a name="internet-connection"></a>Conexão com a Internet

Para baixar e executar as ferramentas e scripts que são necessários para a implantação de software SAP, você deve estar conectado à Internet. A VM do Azure que está executando a Extensão para SAP do Azure também precisa de acesso à Internet. Se a VM do Azure fizer parte de um domínio local ou de rede virtual do Azure, verifique se as configurações de proxy relevantes estão definidas, conforme descrito em [Configurar o proxy][deployment-guide-configure-proxy].

### <a name="microsoft-azure-subscription"></a>Uma assinatura do Microsoft Azure

Você precisa de uma conta ativa do Azure.

### <a name="topology-and-networking"></a>Topologia e rede

Você precisa definir a topologia e a arquitetura da implantação do SAP no Azure:

* Contas de armazenamento do Azure a serem usadas
* Rede virtual em que você deseja implantar o sistema SAP
* Grupo de recursos no qual você deseja implantar o sistema SAP
* Região do Azure em que você deseja implantar o sistema SAP
* Configuração do SAP (duas ou três camadas)
* Tamanhos de VM e o número de discos de dados adicional a serem montados para as VMs
* Configuração do SAP CTS (Correction and Transport System)

Crie e configure contas de armazenamento do Azure (se necessário) ou redes virtuais do Azure antes de começar o processo de implantação de software SAP. Para obter informações sobre como criar e configurar esses recursos, confira [Planejamento e implementação de Máquinas Virtuais do Azure para SAP NetWeaver][planning-guide].

### <a name="sap-sizing"></a>Dimensionamento do SAP

Saiba as informações a seguir para dimensionamento do SAP:

* Carga de trabalho projetada do SAP, por exemplo, usando a ferramenta SAP Quick Sizer e o número SAPS (SAP Application Performance Standard)
* Consumo necessário de memória e recursos de CPU do sistema SAP
* Operações de entrada/saída (E/S) necessárias por segundo
* Largura de banda necessária de comunicação eventual entre VMs no Azure
* Largura de banda necessária entre ativos locais e do sistema SAP implantado do Azure

### <a name="resource-groups"></a>Grupos de recursos

No Azure Resource Manager, você pode usar grupos de recursos para gerenciar todos os recursos do aplicativo em sua assinatura do Azure. Para saber mais, consulte [Visão geral do Azure Resource Manager][resource-group-overview].

## <a name="resources"></a>Recursos

### <a name="sap-resources"></a><a name="42ee2bdb-1efc-4ec7-ab31-fe4c22769b94"></a>Recursos do SAP

Ao configurar a implantação de software SAP, são necessários os seguintes recursos do SAP:

* A Nota SAP [1928533], que tem:
  * Lista de tamanhos de VM do Azure que têm suporte para a implantação de software SAP
  * Informações importantes sobre capacidade para tamanhos de VM do Azure
  * Software SAP e combinações de SO (sistema operacional) e banco de dados com suporte
  * A versão do kernel do SAP necessária para Windows e para Linux no Microsoft Azure

* A Nota SAP [2015553] lista pré-requisitos para implantações de software SAP com suporte do SAP no Azure.
* A Nota SAP [2178632] contém informações detalhadas sobre todas as métricas de monitoramentos relatadas para o SAP no Azure.
* A Nota SAP [1409604] tem a versão necessária do SAP Host Agent para Windows no Azure.
* A Nota SAP [2191498] tem a versão necessária do SAP Host Agent para Linux no Azure.
* A Nota SAP [2243692] tem informações sobre o licenciamento do SAP no Linux no Azure.
* A Nota SAP [1984787] tem informações gerais sobre o SUSE Linux Enterprise Server 12.
* A Nota SAP [2002167] tem informações gerais sobre o Red Hat Enterprise Linux 7.x.
* A Nota SAP [2069760] tem informações gerais sobre o Oracle Linux 7.x.
* A nota SAP [1999351] tem informações adicionais de solução de problemas para a extensão do Azure para SAP.
* A Nota SAP [1597355] tem informações gerais sobre o espaço de troca para Linux.
* A [página de SCN do SAP no Azure](https://wiki.scn.sap.com/wiki/x/Pia7Gg) tem notícias e uma coleção de recursos úteis.
* [WIKI da comunidade do SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) tem todas as Notas SAP necessárias para Linux.
* Cmdlets do PowerShell específicos para SAP que fazem parte do [Azure PowerShell][azure-ps].
* Comandos de CLI do Azure específicos do SAP que fazem parte da [CLI do Azure][azure-cli].

### <a name="windows-resources"></a><a name="42ee2bdb-1efc-4ec7-ab31-fe4c22769b94"></a>Recursos do Windows

Estes artigos da Microsoft abordam as implantações do SAP no Azure:

* [Planejamento e implementação de Máquinas Virtuais do Azure para SAP NetWeaver][planning-guide]
* [Implantação de Máquinas Virtuais do Azure para SAP NetWeaver (este artigo)][deployment-guide]
* [Implantação de Máquinas Virtuais do Azure do DBMS para SAP NetWeaver][dbms-guide]

## <a name="deployment-scenarios-for-sap-software-on-azure-vms"></a><a name="b3253ee3-d63b-4d74-a49b-185e76c4088e"></a>Cenários de implantação do software SAP em VMs do Azure

Você tem várias opções para implantar máquinas virtuais e discos associados no Azure. É importante entender as diferenças entre as opções de implantação, pois você pode seguir diferentes etapas para preparar as VMs para implantação com base no tipo de implantação que escolher.

### <a name="scenario-1-deploying-a-vm-from-the-azure-marketplace-for-sap"></a><a name="db477013-9060-4602-9ad4-b0316f8bb281"></a>Cenário 1: Implantando uma VM por meio do Azure Marketplace para o SAP

Você pode usar uma imagem fornecida pela Microsoft ou por terceiros no Azure Marketplace para implantar a VM. O Marketplace oferece algumas imagens de SO padrão do Windows Server e diferentes distribuições do Linux. Você também pode implantar uma imagem que inclua SKUs de DBMS (gerenciamento de banco de dados do sistema), por exemplo, o Microsoft SQL Server. Para obter mais informações sobre como usar imagens com SKUs do DBMS, confira [Implantação de DBMS de Máquinas Virtuais do Azure para SAP NetWeaver][dbms-guide].

O seguinte fluxograma mostra a sequência de etapas específicas do SAP para implantar uma VM do Azure Marketplace:

![Fluxograma de implantação de VM para sistemas SAP usando uma imagem de VM do Azure Marketplace][deployment-guide-figure-100]

#### <a name="create-a-virtual-machine-by-using-the-azure-portal"></a>Criar uma máquina virtual usando o portal do Azure

A maneira mais fácil de criar uma nova máquina virtual com uma imagem com origem no Azure Marketplace é usar o Portal do Azure.

1.  Ir para <https://portal.azure.com/#create/hub>.  Ou, no menu do portal do Azure, selecione **+Novo**.
1.  Selecione **Computação** e selecione o tipo de sistema operacional que você deseja implantar. Por exemplo, o Windows Server 2012 R2, o SUSE Linux Enterprise Server 12 (SLES 12), o Red Hat Enterprise Linux 7.2 (RHEL 7.2) ou o Oracle Linux 7.2. O modo de exibição de lista padrão não mostra todos os sistemas operacionais com suporte. Selecione **ver todos** para obter uma lista completa. Para obter mais informações sobre sistemas operacionais com suporte para implantação de software SAP, confira a Nota SAP [1928533].
1.  Na próxima página, examine os termos e as condições.
1.  Na caixa **Selecionar um modelo de implantação**, selecione **Gerenciador de Recursos**.
1.  Selecione **Criar**.

O assistente orienta você durante a configuração de parâmetros necessários para criar a máquina virtual, além de todos os recursos necessários, como interfaces de rede e contas de armazenamento. Alguns desses parâmetros são:

1. **Noções básicas**:
   * **Name**: O nome do recurso (o nome da máquina virtual).
   * **Tipo de disco da VM**: Selecione o tipo de disco como disco do sistema operacional. Se você quiser usar o Armazenamento Premium para os discos de dados, é recomendável usar o Armazenamento Premium para o disco do SO também.
   * **Nome de usuário e senha** ou **Chave pública SSH**: Insira o nome de usuário e a senha do usuário criado durante o provisionamento. Para uma máquina virtual Linux, você pode inserir a chave pública do SSH (Secure Shell) que usa para entrar na máquina.
   * **Assinatura**: Selecione a assinatura que deseja usar para provisionar a nova máquina virtual.
   * **Grupo de recursos**: O nome do grupo de recursos da VM. Você pode inserir o nome de um novo grupo de recursos ou o nome de um grupo de recursos que já existe.
   * **Localização**: Onde implantar a nova máquina virtual. Se quiser conectar a máquina virtual à a rede local, selecione o local da rede virtual que conecta o Azure à a rede local. Para obter mais informações, confira [Rede do Microsoft Azure][planning-guide-microsoft-azure-networking] em [Planejamento e implementação de Máquinas Virtuais do Azure para SAP NetWeaver][planning-guide].
1. **Size**:

     Para obter uma lista dos tipos de VM com suporte, confira a Nota SAP [1928533]. Selecione o tipo correto de VM se você quiser usar o Armazenamento Premium do Azure. Nem todos os tipos VM dão suporte ao Armazenamento Premium. Para obter mais informações, consulte [armazenamento: armazenamento do Microsoft Azure e discos de dados][planning-guide-storage-microsoft-azure-storage-and-data-disks] e [armazenamento do Azure para cargas de trabalho do SAP](./planning-guide-storage.md) em [planejamento e implementação de máquinas virtuais do Azure para SAP NetWeaver][planning-guide].

1. **Configurações**:
   * **Storage**
     * **Tipo de disco**: Selecione o tipo de disco como disco do sistema operacional. Se você quiser usar o Armazenamento Premium para os discos de dados, é recomendável usar o Armazenamento Premium para o disco do SO também.
     * **Usar Managed Disks**: Se você desejar usar Managed Disks, selecione Sim. Para obter mais informações sobre Managed Disks, confira o capítulo [Managed Disks](./planning-guide-storage.md#microsoft-azure-storage-resiliency) no guia de planejamento.
     * **Conta de armazenamento**: Selecione uma conta de armazenamento existente ou crie uma nova. Nem todos os tipos de armazenamento funcionam para a execução de aplicativos SAP. Para obter mais informações sobre os tipos de armazenamento, consulte [Estrutura de armazenamento de uma VM para implantações de RDBMS](./dbms_guide_general.md#65fa79d6-a85f-47ee-890b-22e794f51a64).
   * **Rede**
     * **Rede virtual** e **Sub-rede**: Para integrar a máquina virtual à intranet, selecione a rede virtual que está conectada à rede local.
     * **Endereço IP público**: Selecione o endereço IP público que você deseja usar ou insira os parâmetros para criar um novo endereço IP público. Você pode usar um endereço IP público para acessar a máquina virtual pela Internet. Verifique se você também pode criar um grupo de segurança de rede para ajudar a proteger o acesso à máquina virtual.
     * **Grupo de segurança de rede**: Para obter mais informações, confira [Controlar o fluxo de tráfego de rede com grupos de segurança de rede][virtual-networks-nsg].
   * **Extensões**: Você pode instalar extensões de máquina virtual adicionando-as à implantação. Você não precisa adicionar extensões nesta etapa. As extensões necessárias para o suporte da SAP são instaladas posteriormente. Confira o capítulo [Configurar a Extensão para SAP do Azure][deployment-guide-4.5] neste guia.
   * **Alta disponibilidade**: Selecione um conjunto de disponibilidade ou insira os parâmetros para criar um novo conjunto de disponibilidade. Para obter mais informações, confira [Conjuntos de disponibilidade do Azure][planning-guide-3.2.3].
   * **Monitoring**
     * **Diagnóstico de inicialização**: Você pode selecionar **Desabilitar** para o diagnóstico de inicialização.
     * **Diagnóstico do SO convidado**: Você pode selecionar **Desabilitar** para o monitoramento de diagnóstico.

1. **Resumo**:

   Examine suas seleções e selecione **OK**.

Sua máquina virtual é implantada no grupo de recursos selecionado.

#### <a name="create-a-virtual-machine-by-using-a-template"></a>Criar uma máquina virtual usando um modelo

Você pode criar uma máquina virtual usando um dos modelos do SAP publicados no [Repositório do GitHub azure-quickstart-templates][azure-quickstart-templates-github]. Você também pode criar manualmente uma máquina virtual usando o [portal do Azure][virtual-machines-windows-tutorial], o [PowerShell][virtual-machines-ps-create-preconfigure-windows-resource-manager-vms] ou a [CLI do Azure][virtual-machines-linux-tutorial].

* [**Modelo de configuração de duas camadas (apenas uma máquina virtual)** (sap-2-tier-marketplace-image)][sap-templates-2-tier-marketplace-image]

  Para criar um sistema de duas camadas, usando apenas uma máquina virtual, use este modelo.
* [**Modelo de configuração de duas camadas (apenas uma máquina virtual) – Managed Disks** (sap-2-tier-marketplace-image-md)][sap-templates-2-tier-marketplace-image-md]

  Para criar um sistema de duas camadas usando apenas uma máquina virtual e Managed Disks, use este modelo.
* [**Modelo de configuração de três camadas (várias máquinas virtuais)** (sap-3-tier-marketplace-image)][sap-templates-3-tier-marketplace-image]

  Para criar um sistema de três camadas com várias máquinas virtuais, use este modelo.
* [**Modelo de configuração de três camadas (várias máquinas virtuais) – Managed Disks** (sap-3-tier-marketplace-image-md)][sap-templates-3-tier-marketplace-image-md]

  Para criar um sistema de três camadas usando várias máquinas virtuais e Managed Disks, use este modelo.

No portal do Azure, insira os seguintes parâmetros para o modelo:

1. **Noções básicas**:
   * **Assinatura**: A assinatura a ser usada para implantar o modelo.
   * **Grupo de recursos**: O grupo de recursos a ser usado para implantar o modelo. Você pode criar um novo grupo de recursos ou selecionar um existente na assinatura selecionada.
   * **Localização**: Onde implantar o modelo. Se você tiver selecionado um grupo de recursos existente, a localização desse grupo de recursos será usada.

1. **Configurações**:
   * **ID do sistema SAP**: A SID (ID do sistema) do SAP.
   * **Tipo de sistema operacional**: O sistema operacional que você deseja implantar, por exemplo, Windows Server 2012 R2, SUSE Linux Enterprise Server 12 (SLES 12), Red Hat Enterprise Linux 7.2 (RHEL 7.2) ou Oracle Linux 7.2.

     O modo de exibição de lista não mostra todos os sistemas operacionais com suporte. Para obter mais informações sobre sistemas operacionais com suporte para implantação de software SAP, confira a Nota SAP [1928533].
   * **Tamanho do sistema SAP**: O tamanho do sistema SAP.

     O número de SAPS que o novo sistema fornece. Se não tiver certeza de quantos SAPS o sistema precisará, pergunte ao Parceiro de Tecnologia SAP ou ao Integrador de Sistemas.
   * **Disponibilidade do sistema** (somente modelo de três camadas): A disponibilidade do sistema.

     Selecione **HA** para obter uma configuração adequada a uma instalação de alta disponibilidade. Dois servidores de banco de dados e dois servidores para ASCS (ABAP SAP Central Services) são criados.
   * **Tipo de armazenamento** (somente modelo de duas camadas): O tipo de armazenamento a ser usado.

     Em sistemas maiores, é altamente recomendável usar o Armazenamento do Azure Premium. Para obter mais informações sobre tipos de armazenamento, confira estes recursos:
      * [Usar o Armazenamento SSD Premium do Azure para instância do SAP DBMS][2367194]
      * [Estrutura de armazenamento de uma VM para implantações de RDBMS](./dbms_guide_general.md#65fa79d6-a85f-47ee-890b-22e794f51a64)
      * [Armazenamento Premium: Armazenamento de alto desempenho para cargas de trabalho da Máquina Virtual do Azure][storage-premium-storage-preview-portal]
      * [Introdução ao Armazenamento do Microsoft Azure][storage-introduction]
   * **Nome de usuário administrador** e **Senha de administrador**: Um nome de usuário e uma senha.
     Um novo usuário é criado para entrar na máquina virtual.
   * **Sub-rede nova ou existente**: Determina se uma nova rede virtual e uma sub-rede devem ser criadas ou se uma sub-rede existente é usada. Se já tiver uma rede virtual conectada à rede local, selecione **Existente**.
   * **ID da Sub-rede**: Se você deseja implantar a VM em uma rede virtual existente em que você tem uma sub-rede definida para a qual a VM deve ser designada, nomeie a identificação dessa sub-rede específica. A ID geralmente tem esta aparência: /subscriptions/&lt;id da assinatura>/resourceGroups/&lt;nome do grupo de recursos>/providers/Microsoft.Network/virtualNetworks/&lt;nome de rede virtual> /subnets/&lt;nome da sub-rede>

1. **Termos e condições**:  
    Examine e aceite o termos legais.

1. Selecione **Comprar**.

O Agente de VM do Azure é implantado por padrão quando você usa uma imagem do Azure Marketplace.

#### <a name="configure-proxy-settings"></a>Definir configurações de proxy

Dependendo de como a rede local estiver configurada, talvez você precise configurar o proxy na VM. Se a sua VM estiver conectada à rede local via VPN ou ExpressRoute, a VM poderá não conseguir acessar a Internet e não poderá baixar as extensões de VM necessárias nem coletar informações de infraestrutura do Azure para o Agente de Host do SAP por meio da extensão SAP para o Azure. Para obter mais informações, confira [Configurar o proxy][deployment-guide-configure-proxy].

#### <a name="join-a-domain-windows-only"></a>Ingressar em um domínio (somente Windows)

Se a implantação do Azure estiver conectada a uma instância do DNS ou Active Directory local por meio de uma conexão VPN site a site do Azure ou ExpressRoute (isso é chamado de *entre instalações* em [planejamento e implementação de Máquinas Virtuais do Azure para SAP NetWeaver][planning-guide]), espera-se que a VM ingresse em um domínio local. Para obter mais informações sobre as considerações para essa tarefa, confira [Associar uma VM a um domínio local (somente Windows)][deployment-guide-4.3].

#### <a name="configure-vm-extension"></a><a name="ec323ac3-1de9-4c3a-b770-4ff701def65b"></a>Configurar Extensão da VM

Para garantir que o SAP ofereça suporte ao seu ambiente, configure a Extensão para SAP do Azure, conforme descrito em [Configurar a Extensão para SAP do Azure][deployment-guide-4.5]. Verifique os pré-requisitos para SAP e as versões mínimas necessárias do Kernel do SAP e do Agente de Host do SAP nos recursos listados em [Recursos de SAP][deployment-guide-2.2].

#### <a name="vm-extension-for-sap-check"></a>Verificação da Extensão da VM para SAP

Verifique se a extensão de VM para SAP está funcionando, conforme descrito em [verificações e solução de problemas][deployment-guide-troubleshooting-chapter].

#### <a name="post-deployment-steps"></a>Etapas de pós-implantação

Após criar a VM e implantá-la, você precisa instalar os componentes de software necessários na VM. Devido a sequência de instalação de implantação de software nesse tipo de implantação de VM, o software a ser instalado já deve estar disponível no Azure, em outra VM ou como um disco que pode ser anexado. Ou então, considere o uso de um cenário entre locais, em que a conectividade aos ativos locais (compartilhamentos de instalação) é fornecida.

Depois de implantar a VM no Azure, siga as mesmas diretrizes e ferramentas para instalar o software SAP na VM como você faria em um ambiente local. Para instalar o software SAP em uma VM do Azure, tanto a SAP quanto a Microsoft recomendam carregar e armazenar a mídia de instalação SAP em VHDs do Azure ou Managed Disks ou criar uma VM do Azure que funcione como um servidor de arquivos com toda a mídia de instalação SAP necessária.

### <a name="scenario-2-deploying-a-vm-with-a-custom-image-for-sap"></a><a name="54a1fc6d-24fd-4feb-9c57-ac588a55dff2"></a>Cenário 2: Implantando uma VM com uma imagem personalizada para SAP

Como diferentes versões de um sistema operacional ou DBMS têm requisitos de patch diferentes, as imagens que você encontrar no Azure Marketplace poderão não atender às suas necessidades. Em vez disso, talvez você queira criar uma VM usando sua própria imagem de VM do DBMS/sistema operacional, que pode implantar novamente mais tarde.
Você usa diferentes etapas para criar uma imagem privada para Linux em vez de criar uma para o Windows.

---
> ![Logotipo do Windows.][Logo_Windows] Windows
>
> Para preparar uma imagem do Windows que você pode usar para implantar várias máquinas virtuais, as configurações do Windows (como o nome de host e SID do Windows) devem ser abstraídas ou generalizadas na VM local. Você pode usar [sysprep](/previous-versions/windows/it-pro/windows-8.1-and-8/hh825084(v=win.10)) para fazer isso.
>
> ![Logotipo do Linux.][Logo_Linux] Linux
>
> Para preparar uma imagem do Linux que você pode usar para implantar várias máquinas virtuais, algumas configurações do Linux devem ser abstraídas ou generalizadas na VM local. Você pode usar `waagent -deprovision` para fazer isso. Para obter mais informações, confira [Capturar uma máquina virtual Linux em execução no Azure][virtual-machines-linux-capture-image] e [Guia do usuário do agente Linux do Azure][virtual-machines-linux-agent-user-guide-command-line-options].
>
>

---
Você pode preparar e criar uma imagem personalizada e usá-la para criar várias novas VMs. Isso é descrito em [Planejamento e implementação de Máquinas Virtuais do Azure para SAP NetWeaver][planning-guide]. Configure o conteúdo do banco de dados usando o Gerenciador de Provisionamento de Software SAP para instalar um novo sistema SAP (restaura um backup de banco de dados de um disco anexado à máquina virtual) ou restaurando diretamente um backup de banco de dados do Armazenamento do Azure, se o DBMS der suporte a isso. Para obter mais informações, consulte [Implantação de DBMS das Máquinas Virtuais do Azure para SAP NetWeaver][dbms-guide]. Se um sistema SAP já estiver instalado na VM local (especialmente para sistemas de duas camadas), você poderá adaptar as configurações do sistema SAP após a implantação da VM do Azure usando o procedimento de Renomeação do Sistema ao qual o Gerenciador de Provisionamento de Software SAP dá suporte (Nota SAP [1619720]). Caso contrário, você pode instalar o software SAP depois de implantar a VM do Azure.

O seguinte fluxograma mostra a sequência de etapas para implantar uma VM por meio de uma imagem personalizada específica do SAP:

![Fluxograma de implantação de VM para sistemas SAP usando uma imagem de VM do Marketplace privado][deployment-guide-figure-300]

#### <a name="create-a-virtual-machine-by-using-the-azure-portal"></a>Criar uma máquina virtual usando o portal do Azure

A maneira mais fácil de criar uma nova máquina virtual usando uma imagem de Disco Gerenciado é usando o portal do Azure. Para obter mais informações sobre como criar uma Imagem de Disco Gerenciado, leia [Capturar uma imagem gerenciada de uma VM generalizada no Azure](../../windows/capture-image-resource.md)

1.  Ir para <https://ms.portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Compute%2Fimages>. Ou, no menu do portal do Azure, selecione **Imagens**.
1.  Selecione a imagem de Disco Gerenciado que você deseja implantar e clique em **Criar VM**

O assistente orienta você durante a configuração de parâmetros necessários para criar a máquina virtual, além de todos os recursos necessários, como interfaces de rede e contas de armazenamento. Alguns desses parâmetros são:

1. **Noções básicas**:
   * **Name**: O nome do recurso (o nome da máquina virtual).
   * **Tipo de disco da VM**: Selecione o tipo de disco como disco do sistema operacional. Se você quiser usar o Armazenamento Premium para os discos de dados, é recomendável usar o Armazenamento Premium para o disco do SO também.
   * **Nome de usuário e senha** ou **Chave pública SSH**: Insira o nome de usuário e a senha do usuário criado durante o provisionamento. Para uma máquina virtual Linux, você pode inserir a chave pública do SSH (Secure Shell) que usa para entrar na máquina.
   * **Assinatura**: Selecione a assinatura que deseja usar para provisionar a nova máquina virtual.
   * **Grupo de recursos**: O nome do grupo de recursos da VM. Você pode inserir o nome de um novo grupo de recursos ou o nome de um grupo de recursos que já existe.
   * **Localização**: Onde implantar a nova máquina virtual. Se quiser conectar a máquina virtual à a rede local, selecione o local da rede virtual que conecta o Azure à a rede local. Para obter mais informações, confira [Rede do Microsoft Azure][planning-guide-microsoft-azure-networking] em [Planejamento e implementação de Máquinas Virtuais do Azure para SAP NetWeaver][planning-guide].
1. **Size**:

     Para obter uma lista dos tipos de VM com suporte, confira a Nota SAP [1928533]. Selecione o tipo correto de VM se você quiser usar o Armazenamento Premium do Azure. Nem todos os tipos VM dão suporte ao Armazenamento Premium. Para obter mais informações, consulte [armazenamento: armazenamento do Microsoft Azure e discos de dados][planning-guide-storage-microsoft-azure-storage-and-data-disks] e [armazenamento do Azure para cargas de trabalho do SAP](./planning-guide-storage.md) em [planejamento e implementação de máquinas virtuais do Azure para SAP NetWeaver][planning-guide].

1. **Configurações**:
   * **Storage**
     * **Tipo de disco**: Selecione o tipo de disco como disco do sistema operacional. Se você quiser usar o Armazenamento Premium para os discos de dados, é recomendável usar o Armazenamento Premium para o disco do SO também.
     * **Usar Managed Disks**: Se você desejar usar Managed Disks, selecione Sim. Para obter mais informações sobre Managed Disks, confira o capítulo [Managed Disks](./planning-guide-storage.md#microsoft-azure-storage-resiliency) no guia de planejamento.
   * **Rede**
     * **Rede virtual** e **Sub-rede**: Para integrar a máquina virtual à intranet, selecione a rede virtual que está conectada à rede local.
     * **Endereço IP público**: Selecione o endereço IP público que você deseja usar ou insira os parâmetros para criar um novo endereço IP público. Você pode usar um endereço IP público para acessar a máquina virtual pela Internet. Verifique se você também pode criar um grupo de segurança de rede para ajudar a proteger o acesso à máquina virtual.
     * **Grupo de segurança de rede**: Para obter mais informações, confira [Controlar o fluxo de tráfego de rede com grupos de segurança de rede][virtual-networks-nsg].
   * **Extensões**: Você pode instalar extensões de máquina virtual adicionando-as à implantação. Você não precisa adicionar uma extensão nesta etapa. As extensões necessárias para o suporte da SAP são instaladas posteriormente. Confira o capítulo [Configurar a Extensão para SAP do Azure][deployment-guide-4.5] neste guia.
   * **Alta disponibilidade**: Selecione um conjunto de disponibilidade ou insira os parâmetros para criar um novo conjunto de disponibilidade. Para obter mais informações, confira [Conjuntos de disponibilidade do Azure][planning-guide-3.2.3].
   * **Monitoring**
     * **Diagnóstico de inicialização**: Você pode selecionar **Desabilitar** para o diagnóstico de inicialização.
     * **Diagnóstico do SO convidado**: Você pode selecionar **Desabilitar** para o monitoramento de diagnóstico.

1. **Resumo**:

   Examine suas seleções e selecione **OK**.

Sua máquina virtual é implantada no grupo de recursos selecionado.

#### <a name="create-a-virtual-machine-by-using-a-template"></a>Criar uma máquina virtual usando um modelo

Para criar uma implantação usando uma imagem de SO particular do portal do Azure, use um dos modelos do SAP a seguir. Esses modelos são publicados no [repositório azure-quickstart-templates do GitHub][azure-quickstart-templates-github]. Você também pode criar manualmente uma máquina virtual usando o [PowerShell][virtual-machines-upload-image-windows-resource-manager].

* [**Modelo de configuração de duas camadas (apenas uma máquina virtual)** (sap-2-tier-user-image)][sap-templates-2-tier-user-image]

  Para criar um sistema de duas camadas, usando apenas uma máquina virtual, use este modelo.
* [**Modelo de configuração de duas camadas (apenas uma máquina virtual) – Imagem de Disco Gerenciado** (sap-2-tier-user-image-md)][sap-templates-2-tier-user-image-md]

  Para criar um sistema de duas camadas usando apenas uma máquina virtual e uma imagem de Disco Gerenciado, use este modelo.
* [**Modelo de configuração de três camadas (várias máquinas virtuais)** (sap-3-tier-user-image)][sap-templates-3-tier-user-image]

  Para criar um sistema de três camadas usando várias máquinas virtuais ou sua própria imagem de SO, use este modelo.
* [**Modelo de configuração de três camadas (várias máquinas virtuais) – Imagem de Disco Gerenciado** (sap-3-tier-user-image-md)][sap-templates-3-tier-user-image-md]

  Para criar um sistema de três camadas usando várias máquinas virtuais ou sua própria imagem de SO e uma imagem de Disco Gerenciado, use este modelo.

No portal do Azure, insira os seguintes parâmetros para o modelo:

1. **Noções básicas**:
   * **Assinatura**: A assinatura a ser usada para implantar o modelo.
   * **Grupo de recursos**: O grupo de recursos a ser usado para implantar o modelo. Você pode criar um novo grupo de recursos ou selecionar um existente na assinatura.
   * **Localização**: Onde implantar o modelo. Se você tiver selecionado um grupo de recursos existente, a localização desse grupo de recursos será usada.
1. **Configurações**:
   * **ID do sistema SAP**: A ID do sistema SAP.
   * **Tipo de sistema operacional**: O tipo de sistema operacional que você deseja implantar (Windows ou Linux).
   * **Tamanho do sistema SAP**: O tamanho do sistema SAP.

     O número de SAPS que o novo sistema fornece. Se não tiver certeza de quantos SAPS o sistema precisará, pergunte ao Parceiro de Tecnologia SAP ou ao Integrador de Sistemas.
   * **Disponibilidade do sistema** (somente modelo de três camadas): A disponibilidade do sistema.

     Selecione **HA** para obter uma configuração adequada a uma instalação de alta disponibilidade. Dois servidores de banco de dados e dois para ASCS são criados.
   * **Tipo de armazenamento** (somente modelo de duas camadas): O tipo de armazenamento a ser usado.

     Em sistemas maiores, é altamente recomendável usar o Armazenamento do Azure Premium. Para obter mais informações sobre tipos de armazenamento, confira os seguintes recursos:
      * [Usar o Armazenamento SSD Premium do Azure para instância do SAP DBMS][2367194]
      * [Estrutura de armazenamento de uma VM para implantações de RDBMS](./dbms_guide_general.md#65fa79d6-a85f-47ee-890b-22e794f51a64)
      * [Armazenamento Premium: Armazenamento de alto desempenho para cargas de trabalho de máquina virtual do Azure][storage-premium-storage-preview-portal]
      * [Introdução ao Armazenamento do Microsoft Azure][storage-introduction]
   * **URI do VHD de imagem de usuário** (somente modelo de imagem de disco não gerenciado): O URI do VHD de imagem do sistema operacional privada, por exemplo, https://&lt;accountname>.blob.core.windows.net/vhds/userimage.vhd.
   * **Conta de armazenamento de imagens do usuário** (somente modelo de imagem de disco não gerenciado): O nome da conta de armazenamento em que a imagem do sistema operacional privada é armazenada, por exemplo, &lt;accountname> in https://&lt;accountname>.blob.core.windows.net/vhds/userimage.vhd.
   * **Userimageid** (somente modelo de imagem de disco gerenciado): ID da imagem de disco gerenciado que você deseja usar
   * **Nome de usuário administrador** e **Senha de administrador**: O nome de usuário e senha.

     Um novo usuário é criado para entrar na máquina virtual.
   * **Sub-rede nova ou existente**: Determina se uma nova rede virtual e uma sub-rede devem ser criadas ou se uma sub-rede existente é usada. Se já tiver uma rede virtual conectada à rede local, selecione **Existente**.
   * **ID da Sub-rede**: Se você deseja implantar a VM em uma rede virtual existente em que você tem uma sub-rede definida para a qual a VM deve ser designada, nomeie a identificação dessa sub-rede específica. A ID geralmente tem esta aparência: /subscriptions/&lt;id da assinatura>/resourceGroups/&lt;nome do grupo de recursos>/providers/Microsoft.Network/virtualNetworks/&lt;nome de rede virtual> /subnets/&lt;nome da sub-rede>

1. **Termos e condições**:  
    Examine e aceite o termos legais.

1. Selecione **Comprar**.

#### <a name="install-the-vm-agent-linux-only"></a>Instale o Agente VM (somente no Linux)

Para usar os modelos descritos na seção anterior, o agente do Linux já deve estar instalado na imagem do usuário, ou a implantação falhará. Baixe e instale o Agente de VM na imagem do usuário, conforme descrito em [Baixar, instalar e habilitar o Agente de VM do Azure][deployment-guide-4.4]. Se você não usar os modelos, também poderá instalar o Agente de VM mais tarde.

#### <a name="join-a-domain-windows-only"></a>Ingressar em um domínio (somente Windows)

Se a implantação do Azure estiver conectada a uma instância do DNS ou do Active Directory local por meio de uma conexão VPN site a site do Azure ou Azure ExpressRoute (isso é chamado de *entre instalações* em [planejamento e implementação de Máquinas Virtuais do Azure para SAP NetWeaver][planning-guide]), espera-se que a VM ingresse em um domínio local. Para obter mais informações sobre as considerações para essa etapa, confira [Associar uma VM a um domínio local (somente Windows)][deployment-guide-4.3].

#### <a name="configure-proxy-settings"></a>Definir configurações de proxy

Dependendo de como a rede local estiver configurada, talvez você precise configurar o proxy na VM. Se a sua VM estiver conectada à sua rede local via VPN ou ExpressRoute, a VM poderá não conseguir acessar a Internet e não poderá baixar as extensões de VM necessárias ou coletar informações de infraestrutura do Azure para o Agente de Host do SAP por meio da extensão SAP para o Azure, confira [Configurar o proxy][deployment-guide-configure-proxy].

#### <a name="configure-azure-vm-extension-for-sap"></a>Configurar a Extensão da VM do Azure para SAP

Para garantir que o SAP ofereça suporte ao seu ambiente, configure a Extensão para SAP do Azure, conforme descrito em [Configurar a Extensão para SAP do Azure][deployment-guide-4.5]. Verifique os pré-requisitos para SAP e as versões mínimas necessárias do Kernel do SAP e do Agente de Host do SAP nos recursos listados em [Recursos de SAP][deployment-guide-2.2].

#### <a name="sap-vm-extension-check"></a>Verificação da Extensão da VM para SAP

Verifique se a extensão de VM para SAP está funcionando, conforme descrito em [verificações e solução de problemas][deployment-guide-troubleshooting-chapter].


### <a name="scenario-3-moving-an-on-premises-vm-by-using-a-non-generalized-azure-vhd-with-sap"></a><a name="a9a60133-a763-4de8-8986-ac0fa33aa8c1"></a>Cenário 3: Movendo uma VM local usando um VHD do Azure não generalizado com o SAP

Nesse cenário, você planeja mover um sistema SAP específico de um ambiente local para o Azure. Você pode fazer isso carregando o VHD que tenha o sistema operacional, os binários SAP e, eventualmente, os binários DBMS, mais os VHDs com os arquivos de dados e de log do DBMS no Azure. Diferentemente do cenário descrito no [cenário 2: Implantar uma VM com uma imagem personalizada para SAP][deployment-guide-3.3], nesse caso, mantenha o nome de host, o SID do SAP e contas de usuário SAP na VM do Azure, pois eles foram configurados no ambiente local. Você não precisa generalizar o sistema operacional. Esse cenário se aplica com mais frequência a cenários entre locais em que parte da estrutura SAP é executada no local e parte dela é executado no Azure.

Nesse cenário, o Agente de VM **não** é instalado automaticamente durante a implantação. Uma vez que o Agente de VM e a Extensão para SAP do Azure são necessários para executar o SAP NetWeaver no Azure, você precisa baixar, instalar e habilitar os dois componentes manualmente depois de criar a máquina virtual.

Para obter mais informações sobre o Agente de VM do Azure, confira os recursos a seguir.

---
> ![Logotipo do Windows.][Logo_Windows] Windows
>
> [Visão geral do Agente de Máquina Virtual do Azure][virtual-machines-windows-agent-user-guide]
>
> ![Logotipo do Linux.][Logo_Linux] Linux
>
> [Guia do usuário do agente Linux para o Azure][virtual-machines-linux-agent-user-guide]
>
>

---

O seguinte fluxograma mostra a sequência de etapas para mover uma VM local usando um VHD do Azure não generalizado:

![Fluxograma de implantação de VM para sistemas SAP usando um disco de VM][deployment-guide-figure-400]

Se o disco já tiver sido carregado e definido no Azure (confira [Planejamento e implementação de Máquinas Virtuais do Azure para SAP NetWeaver][planning-guide]), execute as tarefas descritas nas próximas seções.

#### <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Para criar uma implantação usando um disco de sistema operacional privado por meio do portal do Azure, use o modelo SAP publicado no [repositório azure-quickstart-templates do GitHub][azure-quickstart-templates-github]. Você também pode criar manualmente uma máquina virtual usando o PowerShell.

* [**Modelo de configuração de duas camadas (apenas uma máquina virtual)** (sap-2-tier-user-disk)][sap-templates-2-tier-os-disk]

  Para criar um sistema de duas camadas, usando apenas uma máquina virtual, use este modelo.
* [**Modelo de configuração de duas camadas (apenas uma máquina virtual) – Disco Gerenciado** (sap-2-tier-user-disk-md)][sap-templates-2-tier-os-disk-md]

  Para criar um sistema de duas camadas usando apenas uma máquina virtual e um Disco Gerenciado, use este modelo.

No portal do Azure, insira os seguintes parâmetros para o modelo:

1. **Noções básicas**:
   * **Assinatura**: A assinatura a ser usada para implantar o modelo.
   * **Grupo de recursos**: O grupo de recursos a ser usado para implantar o modelo. Você pode criar um novo grupo de recursos ou selecionar um existente na assinatura.
   * **Localização**: Onde implantar o modelo. Se você tiver selecionado um grupo de recursos existente, a localização desse grupo de recursos será usada.
1. **Configurações**:
   * **ID do sistema SAP**: A ID do sistema SAP.
   * **Tipo de sistema operacional**: O tipo de sistema operacional que você deseja implantar (Windows ou Linux).
   * **Tamanho do sistema SAP**: O tamanho do sistema SAP.

     O número de SAPS que o novo sistema fornece. Se não tiver certeza de quantos SAPS o sistema precisará, pergunte ao Parceiro de Tecnologia SAP ou ao Integrador de Sistemas.
   * **Tipo de armazenamento** (somente modelo de duas camadas): O tipo de armazenamento a ser usado.

     Em sistemas maiores, é altamente recomendável usar o Armazenamento do Azure Premium. Para obter mais informações sobre tipos de armazenamento, confira os seguintes recursos:
      * [Usar o Armazenamento SSD Premium do Azure para instância do SAP DBMS][2367194]
      * [Estrutura de armazenamento de uma VM para implantações de RDBMS](./dbms_guide_general.md#65fa79d6-a85f-47ee-890b-22e794f51a64)
      * [Armazenamento Premium: Armazenamento de alto desempenho para cargas de trabalho da Máquina Virtual do Azure][storage-premium-storage-preview-portal]
      * [Introdução ao Armazenamento do Microsoft Azure][storage-introduction]
   * **URI do VHD de disco do sistema operacional**: (somente modelo de disco não gerenciado): O URI do disco do sistema operacional privado, por exemplo, https://&lt;accountname>.blob.core.windows.net/vhds/osdisk.vhd.
   * **ID do disco gerenciado do disco do so** (somente modelo de disco gerenciado): a ID do disco do sistema operacional do disco gerenciado,/subscriptions/92d102f7-81a5-4df7-9877-54987ba97dd9/resourceGroups/Group/Providers/Microsoft.Compute/disks/Win
   * **Sub-rede nova ou existente**: Determina se uma nova rede virtual e uma sub-rede devem ser criadas ou se uma sub-rede existente é usada. Se já tiver uma rede virtual conectada à rede local, selecione **Existente**.
   * **ID da Sub-rede**: Se você deseja implantar a VM em uma rede virtual existente em que você tem uma sub-rede definida para a qual a VM deve ser designada, nomeie a identificação dessa sub-rede específica. A ID geralmente tem esta aparência: /subscriptions/&lt;id da assinatura>/resourceGroups/&lt;nome do grupo de recursos>/providers/Microsoft.Network/virtualNetworks/&lt;nome de rede virtual> /subnets/&lt;nome da sub-rede>

1. **Termos e condições**:  
    Examine e aceite o termos legais.

1. Selecione **Comprar**.

#### <a name="install-the-vm-agent"></a>Instalar o Agente VM

Para usar os modelos descritos na seção anterior, o Agente de VM deve estar instalado no disco do sistema operacional, ou a implantação falhará. Baixe e instale o Agente de VM na VM, conforme descrito em [Baixar, instalar e habilitar o Agente de VM do Azure][deployment-guide-4.4].

Se você não usar os modelos descritos na seção anterior, também poderá instalar o Agente da VM mais tarde.

#### <a name="join-a-domain-windows-only"></a>Ingressar em um domínio (somente Windows)

Se a implantação do Azure estiver conectada a uma instância do DNS ou Active Directory local por meio de uma conexão VPN site a site do Azure ou ExpressRoute (isso é chamado de *entre instalações* em [planejamento e implementação de Máquinas Virtuais do Azure para SAP NetWeaver][planning-guide]), espera-se que a VM ingresse em um domínio local. Para obter mais informações sobre as considerações para essa tarefa, confira [Associar uma VM a um domínio local (somente Windows)][deployment-guide-4.3].

#### <a name="configure-proxy-settings"></a>Definir configurações de proxy

Dependendo de como a rede local estiver configurada, talvez você precise configurar o proxy na VM. Se a sua VM estiver conectada à sua rede local via VPN ou ExpressRoute, a VM poderá não conseguir acessar a Internet e não poderá baixar as extensões de VM necessárias ou coletar informações de infraestrutura do Azure para o Agente de Host do SAP por meio da extensão SAP para o Azure, confira [Configurar o proxy][deployment-guide-configure-proxy].

#### <a name="configure-azure-vm-extension-for-sap"></a>Configurar a Extensão da VM do Azure para SAP

Para garantir que o SAP ofereça suporte ao seu ambiente, configure a Extensão para SAP do Azure, conforme descrito em [Configurar a Extensão para SAP do Azure][deployment-guide-4.5]. Verifique os pré-requisitos para SAP e as versões mínimas necessárias do Kernel do SAP e do Agente de Host do SAP nos recursos listados em [Recursos de SAP][deployment-guide-2.2].

#### <a name="sap-vm-check"></a>Verificação da Extensão da VM para SAP

Verifique se a extensão de VM para SAP está funcionando, conforme descrito em [verificações e solução de problemas][deployment-guide-troubleshooting-chapter].

## <a name="update-the-configuration-of-azure-extension-for-sap"></a>Atualizar a configuração da Extensão para SAP do Azure

Atualize a configuração da Extensão para SAP do Azure em qualquer um dos seguintes cenários:
* A equipe Microsoft/SAP conjunta estende os recursos de extensão da VM e solicita mais ou menos contadores.
* A Microsoft apresenta uma nova versão da infraestrutura subjacente do Azure que fornece os dados, e a Extensão para SAP do Azure precisa se adaptar a essas alterações.
* Você monta discos de dados adicionais para sua VM do Azure ou remove um disco de dados. Nesse cenário, atualize a coleta de dados relacionados ao armazenamento. A alteração da configuração adicionando ou excluindo pontos de extremidade ou atribuindo endereços IP a uma VM não afeta a configuração da extensão.
* Altere o tamanho da VM do Azure, por exemplo, do tamanho A5 para qualquer outro tamanho de VM.
* Você adiciona novas interfaces de rede à a VM do Azure.

Para atualizar as configurações, atualize a configuração da Extensão para SAP do Azure seguindo as etapas em [Configurar a Extensão para SAP do Azure][deployment-guide-4.5].

## <a name="detailed-tasks-for-sap-software-deployment"></a>Tarefas detalhadas para a implantação de software SAP

Esta seção tem etapas detalhadas para realizar tarefas específicas no processo de configuração e implantação.

### <a name="deploy-azure-powershell-cmdlets"></a><a name="604bcec2-8b6e-48d2-a944-61b0f5dee2f7"></a>Implantar cmdlets do Azure PowerShell

Siga as etapas descritas no artigo [instalar o Azure PowerShell Module](/powershell/azure/install-az-ps)

Verifique frequentemente se há atualizações nos cmdlets do PowerShell, que geralmente são atualizados mensalmente. Siga as etapas descritas [neste artigo.](/powershell/azure/install-az-ps#update-the-azure-powershell-module) Salvo indicação em contrário na Nota SAP [1928533] ou na Nota SAP [2015553], é recomendável que você trabalhe com a versão mais recente dos cmdlets do Azure PowerShell.

Para verificar a versão dos cmdlets do Azure PowerShell que estão instalados em seu computador, execute este comando do PowerShell:

```powershell
(Get-Module Az.Compute).Version
```

### <a name="deploy-azure-cli"></a><a name="1ded9453-1330-442a-86ea-e0fd8ae8cab3"></a>Implantar a CLI do Azure

Siga as etapas descritas no artigo [instalar o CLI do Azure](/cli/azure/install-azure-cli)

Verifique frequentemente se há atualizações na CLI do Azure, que geralmente é atualizada mensalmente.

Para verificar a versão da CLI do Azure que está instalada no computador, execute este comando:

```console
az --version
```

### <a name="join-a-vm-to-an-on-premises-domain-windows-only"></a><a name="31d9ecd6-b136-4c73-b61e-da4a29bbc9cc"></a>Associar uma VM a um domínio local (somente Windows)

Se você implantar VMs SAP em um cenário entre locais, em que o DNS e Active Directory local são estendidos no Azure, espera-se que as VMs ingressem em um domínio local. As etapas detalhadas para associar uma VM a um domínio local e o software adicional necessário para ser membro de um domínio local variam de acordo com o cliente. Normalmente, para associar uma VM a um domínio local, você precisa instalar software adicional, como o software antimalware e software de backup ou de monitoramento.

Nesse cenário, você também precisa garantir que se as configurações de proxy da Internet forem forçadas quando uma VM ingressar em um domínio no seu ambiente, a Conta do Sistema Local do Windows (S-1-5-18) na VM convidada tenha as mesmas configurações de proxy. A opção mais fácil é forçar o proxy usando uma Política de Grupo de domínio, que se aplica aos sistemas no domínio.

### <a name="download-install-and-enable-the-azure-vm-agent"></a><a name="c7cbb0dc-52a4-49db-8e03-83e7edc2927d"></a>Baixar, instalar e habilitar o Agente de VM do Azure

Para máquinas virtuais implantadas por meio de uma imagem do sistema operacional que não seja generalizada (por exemplo, uma imagem que não se origina na ferramenta de Preparação do Sistema do Windows, ou sysprep,), você precisa baixar, instalar e habilitar o Agente de VM do Azure manualmente.

Se você implantar uma VM do Azure Marketplace, esta etapa não será necessária. Imagens do Azure Marketplace já têm o Agente de VM do Azure.

#### <a name="windows"></a><a name="b2db5c9a-a076-42c6-9835-16945868e866"></a>Windows

1. Baixe o Agente de VM do Azure:
   1.  Baixe o [pacote do instalador do Agente de VM do Azure](https://go.microsoft.com/fwlink/?LinkId=394789).
   1.  Armazene o pacote de MSI do Agente de VM localmente em um servidor ou computador pessoal.
1. Instale o Agente de VM do Azure:
   1.  Conecte-se à VM do Azure implantada usando o RDP (Protocolo de Área de Trabalho Remota).
   1.  Abra uma janela do Windows Explorer na VM e selecione um diretório de destino para o arquivo MSI do Agente de VM.
   1.  Arraste o arquivo MSI do Instalador do Agente de VM do Azure para o computador local/servidor, para o diretório de destino do Agente de VM na VM.
   1.  Clique duas vezes no arquivo MSI na VM.
1. Para VMs ingressadas em domínios locais, verifique se as configurações de proxy da Internet eventuais também se aplicam à conta do Sistema Local do Windows (S-1-5-18) na VM, conforme descrito em [Configurar o proxy][deployment-guide-configure-proxy]. O Agente de VM é executado nesse contexto e precisa ser capaz de se conectar ao Azure.

Nenhuma interação do usuário é necessária para atualizar o Agente de VM do Azure. O Agente de VM é atualizado automaticamente e não requer uma reinicialização da VM.

#### <a name="linux"></a><a name="6889ff12-eaaf-4f3c-97e1-7c9edc7f7542"></a>Linux

Use os seguintes comandos para instalar o Agente de VM para Linux:

* **SUSE Linux Enterprise Server (SLES)**

  ```console
  sudo zypper install WALinuxAgent
  ```

* **Red Hat Enterprise Linux (RHEL) ou Oracle Linux**

  ```console
  sudo yum install WALinuxAgent
  ```

Se o agente já estiver instalado, para atualizar o agente Linux do Azure, siga as etapas descritas em [Atualizar o Agente Linux do Azure em uma VM para a versão mais recente do GitHub][virtual-machines-linux-update-agent].

### <a name="configure-the-proxy"></a><a name="baccae00-6f79-4307-ade4-40292ce4e02d"></a>Configurar o proxy

As etapas para configurar o proxy do Windows são diferentes da maneira como você configura o proxy no Linux.

#### <a name="windows"></a>Windows

As configurações de proxy devem ser definidas corretamente para que a conta do Sistema Local acesse a Internet. Se as configurações de proxy não forem definidas pela Política de Grupo, você poderá configurá-las para a conta LocalSystem.

1. Vá para **Iniciar**, digite **gpedit.msc** e selecione **Enter**.
1. Selecione **Configuração do computador** > **Modelos Administrativos** > **Componentes do Windows** > **Internet Explorer**. Verifique se a configuração **Definir as configurações de proxy por computador (não por usuário)** está desabilitada ou não definida.
1. Em **Painel de Controle**, acesse **Central de Rede e Compartilhamento** > **Opções da Internet**.
1. Na guia **Conexões**, marque o botão **Configurações da LAN**.
1. Desmarque a caixa de seleção **Detectar automaticamente as configurações**.
1. Marque acaixa de seleção **Usar um servidor proxy para a rede local** e digite o endereço de proxy e a porta.
1. Selecione o botão **Avançado**.
1. Na caixa **Exceções**, digite o endereço IP **168.63.129.16**. Selecione **OK**.

#### <a name="linux"></a>Linux

Defina o proxy correto no arquivo de configuração do Agente Convidado do Microsoft Azure, que fica localizado em \\etc\\waagent.conf.

Defina os seguintes parâmetros:

1. **Host de proxy HTTP**. Por exemplo, defina como **proxy.corp.local**.

   ```console
   HttpProxy.Host=<proxy host>

   ```

1. **Porta de proxy HTTP**. Por exemplo, defina como **80**.

   ```console
   HttpProxy.Port=<port of the proxy host>

   ```

1. Reinicie o agente.

   ```console
   sudo service waagent restart
   ```

As configurações de proxy em \\etc\\waagent.conf também se aplicam às extensões de VM necessárias. Se quiser usar os repositórios do Azure, certifique-se de que o tráfego para esses repositórios não passe pela intranet local. Se tiver criado rotas definidas pelo usuário para habilitar o túnel forçado, adicione uma rota que encaminhe o tráfego para os repositórios diretamente pela Internet, sem passar por sua conexão VPN site a site.

* **SLES**

  Você também precisa adicionar rotas para os endereços IP listados em \\etc\\regionserverclnt.cfg. A seguinte figura mostra um exemplo:

  ![Túnel forçado][deployment-guide-figure-50]


* **RHEL**

  Também é necessário adicionar rotas para os endereços IP dos hosts listados em \\etc\\yum.repos.d\\rhui-load-balancers. Para obter um exemplo, confira a figura anterior.

* **Oracle Linux**

  Não há repositórios para Oracle Linux no Azure. Você precisa configurar seus próprio repositórios para Oracle Linux ou usar os repositórios públicos.

Para saber mais sobre as rotas definidas pelo usuário, confira [Rotas definidas pelo usuário e encaminhamento IP][virtual-networks-udr-overview].

### <a name="configure-the-azure-extension-for-sap"></a><a name="d98edcd3-f2a1-49f7-b26a-07448ceb60ca"></a>Configurar a Extensão para SAP do Azure

> [!NOTE]
> Instrução de suporte geral:  
> O suporte para a extensão do Azure para SAP é fornecido por meio de canais de suporte do SAP. Se você precisar de assistência com a extensão do Azure para SAP, abra um caso de suporte com o [suporte do SAP](https://support.sap.com/). 

Quando você tiver preparado a VM conforme descrito em [Cenários de implantação de VMs para SAP no Azure][deployment-guide-3], o agente estará instalado na máquina virtual. A próxima etapa é implantar a Extensão para SAP do Azure, que está disponível no Repositório de extensões do Azure em data centers globais do Azure. Para obter mais informações, confira [Planejamento e implementação de Máquinas Virtuais do Azure para SAP NetWeaver][planning-guide-9.1].

Estamos no processo de lançamento de uma nova versão da extensão do Azure para SAP. A nova extensão usa a identidade atribuída pelo sistema da máquina virtual para obter informações sobre os discos anexados, as interfaces de rede e a própria máquina virtual. Para poder acessar esses recursos, a identidade do sistema da máquina virtual precisa de permissão de leitor para a máquina virtual, o disco do sistema operacional, os discos de dados e as interfaces de rede. Atualmente, recomendamos que você instale apenas a nova extensão nos seguintes cenários:

1. Você deseja instalar a extensão com Terraform, modelos de Azure Resource Manager ou com outros meios que CLI do Azure ou Azure PowerShell
1. Você deseja instalar a extensão no SUSE SLES 15 ou superior.
1. O suporte da Microsoft ou da SAP solicita que você instale a nova extensão
1. Você deseja usar o Azure ultra Disk ou Standard Managed Disks

Para esses cenários, siga as etapas no capítulo [Configurar a nova extensão do Azure para SAP com Azure PowerShell][deployment-guide-configure-new-extension-ps] para Azure PowerShell ou [Configurar a nova extensão do Azure para sap com o CLI do Azure][deployment-guide-configure-new-extension-cli] para CLI do Azure.

Siga [Azure PowerShell][deployment-guide-4.5.1] ou [CLI do Azure][deployment-guide-4.5.2] para instalar e configurar a versão padrão da extensão do Azure para SAP.

#### <a name="azure-powershell-for-linux-and-windows-vms"></a><a name="987cf279-d713-4b4c-8143-6b11589bb9d4"></a>Azure PowerShell para VMs Linux e Windows

Para instalar a Extensão para SAP do Azure usando o PowerShell:

1. Verifique se tem a versão mais recente do cmdlet do Azure PowerShell instalada. Para obter mais informações, confira [Implantar cmdlets do Azure PowerShell][deployment-guide-4.1].  
1. Execute o seguinte cmdlet do PowerShell.
    Para obter uma lista de ambientes disponíveis, execute o cmdlet `Get-AzEnvironment` . Se quiser usar o Azure global, o ambiente será **AzureCloud**. Para o Azure China 21Vianet, selecione **AzureChinaCloud**.

    ```powershell
    $env = Get-AzEnvironment -Name <name of the environment>
    Connect-AzAccount -Environment $env
    Set-AzContext -SubscriptionName <subscription name>

    Set-AzVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
    ```

Depois de inserir os dados da conta, o script implantará as extensões necessárias e habilitará os recursos necessários. Isso pode levar vários minutos.
Para obter mais informações sobre `Set-AzVMAEMExtension`, confira [Set-AzVMAEMExtension][msdn-set-Azvmaemextension].

![Execução bem-sucedida do cmdlet do Azure específico para SAP Set-AzVMAEMExtension][deployment-guide-figure-900]

A configuração `Set-AzVMAEMExtension` realiza todas as etapas para configurar a coleta de dados do host para SAP.

A saída do script inclui as seguintes informações:

* Confirmação de que a coleta de dados de disco do SO e todos os discos de dados adicionais foi configurada.
* As duas próximas mensagens confirmam a configuração das métricas de armazenamento para uma conta de armazenamento específica.
* Uma linha de saída fornece o status da atualização real da configuração da Extensão da VM para SAP.
* Outra linha de saída confirma que a configuração foi implantada ou atualizada.
* A última linha da saída é informativa. Ele mostra as opções para testar a configuração da Extensão da VM para SAP.
* Para verificar se todas as etapas da configuração da Extensão da VM do Azure para SAP foram executadas com êxito e se a infraestrutura do Azure fornece os dados necessários, faça a verificação de preparação para a Extensão do Azure para SAP, conforme descrito em [Verificação de preparação para a Extensão para SAP do Azure][deployment-guide-5.1].
* Aguarde 15 a 30 minutos para que o Diagnóstico do Azure colete os dados relevantes.

#### <a name="azure-cli-for-linux-vms"></a><a name="408f3779-f422-4413-82f8-c57a23b4fc2f"></a>CLI do Azure para VMs Linux

Para instalar a Extensão para SAP do Azure usando a CLI do Azure:

   1. Instale a CLI clássica do Azure, conforme descrito em [Instalar a CLI clássica do Azure][azure-cli].
   1. Entre usando sua conta do Azure:

      ```console
      azure login
      ```

   1. Mude para o modo do Azure Resource Manager:

      ```console
      azure config mode arm
      ```

   1. Habilite a Extensão para SAP do Azure:

      ```console
      azure vm enable-aem <resource-group-name> <vm-name>
      ```

1. Instalar usando a CLI 2.0 do Azure

   1. Instale a CLI 2.0 do Azure, conforme descrito em [Instalar a CLI 2.0 do Azure][azure-cli-2].
   1. Entre usando sua conta do Azure:

      ```azurecli
      az login
      ```

   1. Instalar a extensão de AEM da CLI do Azure
  
      ```azurecli
      az extension add --name aem
      ```
  
   1. Instalar a extensão com
  
      ```azurecli
      az vm aem set -g <resource-group-name> -n <vm name>
      ```

1. Verifique se a Extensão para SAP do Azure está ativa na VM Linux do Azure. Verifique se o arquivo \\var\\lib\\AzureEnhancedMonitor\\PerfCounters existe. Se ele existir, no prompt de comando, execute este comando para exibir as informações coletadas pela Extensão para SAP do Azure:

   ```console
   cat /var/lib/AzureEnhancedMonitor/PerfCounters
   ```

   A saída se parece com esta:

   ```output
   ...
   2;cpu;Current Hw Frequency;;0;2194.659;MHz;60;1444036656;saplnxmon;
   2;cpu;Max Hw Frequency;;0;2194.659;MHz;0;1444036656;saplnxmon;
   ...
   ```

#### <a name="configure-the-new-azure-extension-for-sap-with-azure-powershell"></a><a name="2ad55a0d-9937-4943-9dd2-69bc2b5d3de0"></a>Configurar a nova extensão do Azure para SAP com Azure PowerShell

A nova extensão de VM para SAP usa uma identidade gerenciada atribuída à VM para acessar os dados de monitoramento e configuração da VM. Para instalar a nova extensão do Azure para SAP usando o PowerShell, primeiro você precisa atribuir essa identidade à VM e conceder a essa identidade acesso a todos os recursos que estão sendo usados por essa VM, por exemplo, discos e interfaces de rede.

> [!NOTE]
> As etapas a seguir exigem privilégios de proprietário sobre o grupo de recursos ou recursos individuais (máquina virtual, discos de dados, etc.)

1. Certifique-se de usar o agente de host do SAP 7,21 PL 47 ou superior.
1. Certifique-se de desinstalar a versão atual da extensão de VM para SAP. Não há suporte para instalar as duas versões da extensão de VM para SAP na mesma máquina virtual.
1. Verifique se você instalou a versão mais recente do cmdlet Azure PowerShell (pelo menos o 4.3.0). Para obter mais informações, confira [Implantar cmdlets do Azure PowerShell][deployment-guide-4.1].
1. Execute o seguinte cmdlet do PowerShell.
    Para obter uma lista de ambientes disponíveis, execute o cmdlet `Get-AzEnvironment` . Se quiser usar o Azure global, o ambiente será **AzureCloud**. Para o Azure China 21Vianet, selecione **AzureChinaCloud**.

    ```powershell
    $env = Get-AzEnvironment -Name <name of the environment>
    Connect-AzAccount -Environment $env
    Set-AzContext -SubscriptionName <subscription name>

    Set-AzVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name> -InstallNewExtension
    ```

#### <a name="configure-the-new-azure-extension-for-sap-with-azure-cli"></a><a name="c8749c24-fada-42ad-b114-f9aae2dc37da"></a>Configurar a nova extensão do Azure para SAP com CLI do Azure

A nova extensão de VM para SAP usa uma identidade gerenciada atribuída à VM para acessar os dados de monitoramento e configuração da VM. Para instalar a nova extensão do Azure para SAP usando CLI do Azure, primeiro você precisa atribuir essa identidade à VM e conceder a essa identidade acesso a todos os recursos que estão sendo usados por essa VM, por exemplo, discos e interfaces de rede.

> [!NOTE]
> As etapas a seguir exigem privilégios de proprietário sobre o grupo de recursos ou recursos individuais (máquina virtual, discos de dados, etc.)

1. Certifique-se de usar o agente de host do SAP 7,21 PL 47 ou superior.
1. Certifique-se de desinstalar a versão atual da extensão de VM para SAP. Não há suporte para instalar as duas versões da extensão de VM para SAP na mesma máquina virtual.
1. Instale a CLI 2.0 do Azure, conforme descrito em [Instalar a CLI 2.0 do Azure][azure-cli-2].

1. Entre usando sua conta do Azure:

   ```azurecli
   az login
   ```

1. Instale a extensão CLI do Azure AEM. Certifique-se de usar pelo menos a versão 0.2.0 ou superior.
  
   ```azurecli
   az extension add --name aem
   ```
  
1. Instalar a nova extensão com
  
   ```azurecli
   az vm aem set -g <resource-group-name> -n <vm name> --install-new-extension
   ```

## <a name="checks-and-troubleshooting"></a><a name="564adb4f-5c95-4041-9616-6635e83a810b"></a>Verificações e solução de problemas

Depois de ter implantado a VM do Azure e ter configurado a Extensão do Azure para SAP, verifique se todos os componentes da extensão estão funcionando conforme esperado.

Execute a verificação de preparação para a Extensão para SAP do Azure, conforme descrito em [Verificação de preparação para a Extensão para SAP do Azure][deployment-guide-5.1]. Se todos os resultados da verificação de preparação forem positivos e todos os contadores de desempenho relevantes parecerem corretos, a Extensão para SAP do Azure terá sido configurada com êxito. Você pode prosseguir com a instalação do Agente de Host SAP descrita nas Notas SAP em [Recursos SAP][deployment-guide-2.2]. Se a verificação de preparação indicar que os contadores estão ausentes, execute a verificação de integridade da Extensão para SAP do Azure, conforme descrito em [Verificação de integridade da configuração da Extensão para SAP do Azure][deployment-guide-5.2]. Para obter mais opções de solução de problemas, confira [Solução de problemas da Extensão para SAP do Azure][deployment-guide-5.3].

### <a name="readiness-check-for-the-azure-extension-for-sap"></a><a name="bb61ce92-8c5c-461f-8c53-39f5e5ed91f2"></a>Verificação de preparação para a Extensão para SAP do Azure

> [!NOTE]
> Há duas versões da extensão de VM. Este capítulo aborda a extensão de VM padrão. Se você tiver instalado a nova extensão de VM, consulte [a verificação de preparação do capítulo para a nova extensão do Azure para SAP][deployment-guide-5.1-new]

Essa verificação garante que todas as métricas de desempenho que aparecem no aplicativo SAP sejam fornecidas pela Extensão para SAP do Azure subjacente.

#### <a name="run-the-readiness-check-on-a-windows-vm"></a>Executar a verificação de preparação em uma VM Windows

1. Entre na máquina virtual do Azure (não é necessário usar uma conta do administrador).
1. Abra uma janela de Prompt de Comando.
1. No prompt de comando, altere o diretório para a pasta de instalação da Extensão para SAP do Azure: C:\\Packages\\Plugins\\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\\&lt;version>\\drop

   A *versão* no caminho para a extensão pode variar. Se houver pastas para várias versões da extensão na pasta de instalação, verifique a configuração do serviço AzureEnhancedMonitoring Windows e alterne para a pasta indicada como *Caminho do executável*.

   ![Propriedades do serviço que executa a Extensão para SAP do Azure][deployment-guide-figure-1000]

1. No prompt de comando, execute **azperflib.exe** sem parâmetros.

   > [!NOTE]
   > Azperflib.exe é executado em um loop e atualiza os contadores coletados a cada 60 segundos. Para encerrar o loop, feche a janela de Prompt de comando.
   >
   >

Se a Extensão para SAP do Azure não estiver instalada ou o serviço AzureEnhancedMonitoring não estiver em execução, a extensão não foi configurada corretamente. Para obter informações detalhadas sobre como implantar a extensão, confira [Solução de problemas da Extensão para SAP do Azure][deployment-guide-5.3].

> [!NOTE]
> O Azperflib.exe é um componente que não pode ser usado para finalidades próprias. Ele é um componente que fornece dados de infraestrutura do Azure relacionados à VM para o Agente de Host do SAP exclusivamente.
> 

##### <a name="check-the-output-of-azperflibexe"></a>Verifique a saída de azperflib.exe

A saída de azperflib.exe mostra que todos os contadores de desempenho do Azure para SAP populados. Na parte inferior da lista de contadores coletados, um indicador de integridade e resumo mostra a Extensão para SAP do Azure.

![Saída da verificação de integridade executando azperflib.exe, que indica que não há problemas][deployment-guide-figure-1100]
<a name="figure-11"></a>

Verifique o resultado retornado para a saída de **Total de contadores**, que é relatada como vazia, e para **Status de integridade**, conforme mostrado na figura anterior.

Interprete os valores resultantes da seguinte maneira:

| Valores resultantes de Azperflib.exe | Status da integridade da Extensão para SAP do Azure |
| --- | --- |
| **Chamadas à API – não disponíveis** | Os contadores que não estão disponíveis podem não ser aplicáveis à configuração de máquina virtual ou podem ser erros. Confira **Status de integridade**. |
| **Total de contadores - vazio** |Os seguintes dois contadores de armazenamento do Azure podem estar vazios: <ul><li>Latência do servidor de operações de leitura de armazenamento (ms)</li><li>Latência E2E de operações de leitura de armazenamento (ms)</li></ul>Todos os outros contadores devem ter valores. |
| **Status de integridade** |OK somente se o status retornado mostrar **OK**. |
| **Diagnóstico** |Informações detalhadas sobre o status da integridade. |

Se o valor de **Status de integridade** não for **OK**, siga as instruções em [Verificação de integridade da configuração da Extensão para SAP do Azure][deployment-guide-5.2].

#### <a name="run-the-readiness-check-on-a-linux-vm"></a>Executar a verificação de preparação em uma VM Linux

1. Conecte-se à Máquina Virtual do Azure usando o SSH.

1. Verifique a saída da Extensão para SAP do Azure.

   a.  Execute `more /var/lib/AzureEnhancedMonitor/PerfCounters`

   **Resultado esperado**: Retorna a lista dos contadores de desempenho. O arquivo não deve estar vazio.

   b. Execute `cat /var/lib/AzureEnhancedMonitor/PerfCounters | grep Error`

   **Resultado esperado**: Retorna a linha em que o erro é **none**, por exemplo, **3;config;Error;;0;0;none;0;1456416792;tst-servercs;**

   c. Execute `more /var/lib/AzureEnhancedMonitor/LatestErrorRecord`

   **Resultado esperado**: Retorna como vazio ou não existe.

Se a verificação anterior não for bem-sucedida, execute estas verificações adicionais:

1. Verifique se waagent está instalado e habilitado.

   a.  Execute `sudo ls -al /var/lib/waagent/`

     **Resultado esperado**: Lista o conteúdo do diretório waagent.

   b.  Execute `ps -ax | grep waagent`

   **Resultado esperado**: Exibe uma entrada semelhante a: `python /usr/sbin/waagent -daemon`

1. Verifique se a Extensão para SAP do Azure está instalada e em execução.

   a.  Execute `sudo sh -c 'ls -al /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-*/'`

   **Resultado esperado**: Lista o conteúdo do diretório da Extensão para SAP do Azure.

   b. Execute `ps -ax | grep AzureEnhanced`

   **Resultado esperado**: Exibe uma entrada semelhante a: `python /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-2.0.0.2/handler.py daemon`

1. Instale o Agente de Host do SAP conforme descrito na Nota SAP [1031096] e verifique a saída de `saposcol`.

   a.  Execute `/usr/sap/hostctrl/exe/saposcol -d`

   b.  Execute `dump ccm`

   c.  Verifique se a métrica **Virtualization_Configuration\Enhanced monitoramento acesso** é **true**.

Se você já tiver um servidor de aplicativos ABAP do SAP NetWeaver instalado, abra a transação ST06 e verifique se o monitoramento está habilitado.

Se qualquer uma dessas verificações falhar, e para obter informações detalhadas sobre como reimplantar a extensão, confira [Solução de problemas da Extensão para SAP do Azure][deployment-guide-5.3].

### <a name="readiness-check-for-the-new-azure-extension-for-sap"></a><a name="7bf24f59-7347-4c7a-b094-4693e4687ee5"></a>Verificação de preparação para a nova extensão do Azure para SAP

> [!NOTE]
> Há duas versões da extensão de VM. Este capítulo aborda a nova extensão de VM. Se você tiver instalado a extensão de VM padrão, consulte a [verificação de preparação do capítulo para a extensão do Azure para SAP][deployment-guide-5.1].

Essa verificação garante que todas as métricas de desempenho que aparecem no aplicativo SAP sejam fornecidas pela Extensão para SAP do Azure subjacente.

#### <a name="run-the-readiness-check-on-a-windows-vm"></a>Executar a verificação de preparação em uma VM Windows

1. Entre na máquina virtual do Azure (não é necessário usar uma conta do administrador).
1. Abra um navegador da Web e navegue até http://127.0.0.1:11812/azure4sap/metrics
1. O navegador deve exibir ou baixar um arquivo XML que contém os dados de monitoramento de sua máquina virtual. Se esse não for o caso, verifique se a extensão do Azure para SAP está instalada.

##### <a name="check-the-content-of-the-xml-file"></a>Verificar o conteúdo do arquivo XML

O arquivo XML que você pode acessar em http://127.0.0.1:11812/azure4sap/metrics contém todos os contadores de desempenho do Azure preenchidos para SAP. Ele também contém um indicador de resumo e integridade do status da extensão do Azure para SAP.

Verifique o valor do elemento **Descrição do provedor de integridade** . Se o valor não estiver **OK**, siga as instruções em [verificação de integridade para a configuração da nova extensão do SAP][deployment-guide-5.2-new].

#### <a name="run-the-readiness-check-on-a-linux-vm"></a>Executar a verificação de preparação em uma VM Linux

1. Conecte-se à Máquina Virtual do Azure usando o SSH.

1. Verifique a saída do comando a seguir

    ```console
    curl http://127.0.0.1:11812/azure4sap/metrics
    ```
    
   **Resultado esperado**: retorna um documento XML que contém as informações de monitoramento da máquina virtual, seus discos e interfaces de rede.

Se a verificação anterior não for bem-sucedida, execute estas verificações adicionais:

1. Verifique se waagent está instalado e habilitado.

   a.  Execute `sudo ls -al /var/lib/waagent/`

     **Resultado esperado**: Lista o conteúdo do diretório waagent.

   b.  Execute `ps -ax | grep waagent`

   **Resultado esperado**: Exibe uma entrada semelhante a: `python /usr/sbin/waagent -daemon`

1. Verifique se a Extensão para SAP do Azure está instalada e em execução.

   a.  Execute `sudo sh -c 'ls -al /var/lib/waagent/Microsoft.AzureCAT.AzureEnhancedMonitoring.MonitorX64Linux-*/'`

   **Resultado esperado**: Lista o conteúdo do diretório da Extensão para SAP do Azure.

   b. Execute `ps -ax | grep AzureEnhanced`

   **Resultado esperado**: Exibe uma entrada semelhante a: `/var/lib/waagent/Microsoft.AzureCAT.AzureEnhancedMonitoring.MonitorX64Linux-1.0.0.82/AzureEnhancedMonitoring -monitor`

1. Instale o Agente de Host do SAP conforme descrito na Nota SAP [1031096] e verifique a saída de `saposcol`.

   a.  Execute `/usr/sap/hostctrl/exe/saposcol -d`

   b.  Execute `dump ccm`

   c.  Verifique se a métrica **Virtualization_Configuration\Enhanced monitoramento acesso** é **true**.

Se você já tiver um servidor de aplicativos ABAP do SAP NetWeaver instalado, abra a transação ST06 e verifique se o monitoramento está habilitado.

Se qualquer uma dessas verificações falhar e para obter informações detalhadas sobre como reimplantar a extensão, consulte [Solucionando problemas da nova extensão do Azure para SAP][deployment-guide-5.3-new].

### <a name="health-check-for-the-azure-extension-for-sap-configuration"></a><a name="e2d592ff-b4ea-4a53-a91a-e5521edb6cd1"></a>Verificação de integridade da configuração da Extensão para SAP do Azure

> [!NOTE]
> Há duas versões da extensão de VM. Este capítulo aborda a extensão de VM padrão. Se você tiver instalado a nova extensão de VM, consulte [o capítulo verificação de integridade para a nova extensão do Azure para a configuração do SAP][deployment-guide-5.2-new].

Se parte dos dados de infraestrutura não for entregue corretamente, conforme indicado pelo teste descrito em [Verificação de preparação para a Extensão para SAP do Azure][deployment-guide-5.1] acima, execute o cmdlet `Test-AzVMAEMExtension` para verificar se a infraestrutura do Azure e a Extensão para SAP do Azure estão configuradas corretamente.

1. Verifique se você instalou a última versão do cmdlet do Azure PowerShell, conforme descrito em [Implantar cmdlets do Azure PowerShell][deployment-guide-4.1].
1. Execute o seguinte cmdlet do PowerShell. Para obter a lista de ambientes disponíveis, execute o cmdlet `Get-AzEnvironment`. Para usar o Azure global, selecione o ambiente **AzureCloud**. Para o Azure China 21Vianet, selecione **AzureChinaCloud**.

   ```powershell
   $env = Get-AzEnvironment -Name <name of the environment>
   Connect-AzAccount -Environment $env
   Set-AzContext -SubscriptionName <subscription name>
   Test-AzVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
   ```

1. O script testa a configuração da máquina virtual que você seleciona.

   ![Saída do teste bem-sucedido da Extensão do Azure para SAP][deployment-guide-figure-1300]

Verifique se cada resultado da verificação de integridade é **OK**. Se algumas verificações não exibirem **OK**, execute o cmdlet de atualização, conforme descrito em [Configurar a Extensão para SAP do Azure][deployment-guide-4.5]. Aguarde 15 minutos e repita as verificações descritas em [Verificação de preparação para a Extensão para SAP do Azure][deployment-guide-5.1] e [Verificação de integridade da configuração da Extensão para SAP do Azure][deployment-guide-5.2]. Se as verificações ainda indicarem um problema em alguns dos contadores ou todos eles, confira [Solução de problemas da Extensão para SAP do Azure][deployment-guide-5.3].

> [!Note]
> Você pode encontrar alguns avisos em casos em que usar Discos do Azure Standard Gerenciados. Os avisos serão exibidos em vez dos testes retornando "OK". Isso é normal e intencional no caso desse tipo de disco. Confira também [Solução de problemas da Extensão para SAP do Azure][deployment-guide-5.3]
> 

### <a name="health-check-for-the-new-azure-extension-for-sap-configuration"></a><a name="464ac96d-7d3c-435d-a5ae-3faf3bfef4b3"></a>Verificação de integridade para a nova extensão do Azure para a configuração do SAP

> [!NOTE]
> Há duas versões da extensão de VM. Este capítulo aborda a nova extensão de VM. Se você instalou a extensão de VM padrão, consulte o capítulo [Health Check para a configuração da extensão do Azure para SAP][deployment-guide-5.2].

Se alguns dos dados de infraestrutura não forem entregues corretamente conforme indicado pelo teste descrito em [verificação de preparação para a extensão do Azure para SAP][deployment-guide-5.1-new], execute o `Get-AzVMExtension` cmdlet para verificar se a extensão do Azure para SAP está instalada. O `Test-AzVMAEMExtension` ainda não oferece suporte à nova extensão. Depois que o cmdlet oferecer suporte à nova extensão, atualizaremos este artigo.

1. Verifique se você instalou a última versão do cmdlet do Azure PowerShell, conforme descrito em [Implantar cmdlets do Azure PowerShell][deployment-guide-4.1].
1. Execute o seguinte cmdlet do PowerShell. Para obter a lista de ambientes disponíveis, execute o cmdlet `Get-AzEnvironment`. Para usar o Azure global, selecione o ambiente **AzureCloud**. Para o Azure China 21Vianet, selecione **AzureChinaCloud**.

   ```powershell
   $env = Get-AzEnvironment -Name <name of the environment>
   Connect-AzAccount -Environment $env
   Set-AzContext -SubscriptionName <subscription name>
   Test-AzVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
   ```

1. O cmdlet testa a configuração da extensão de VM para SAP na máquina virtual que você selecionar.

### <a name="troubleshooting-azure-extension-for-sap"></a><a name="fe25a7da-4e4e-4388-8907-8abc2d33cfd8"></a>Solução de problemas da Extensão para SAP do Azure

> [!NOTE]
> Há duas versões da extensão de VM. Este capítulo aborda a extensão de VM padrão. Se você tiver instalado a nova extensão de VM, consulte [o capítulo Solucionando problemas da nova extensão do Azure para SAP][deployment-guide-5.3-new].

#### <a name="windows-logologo_windows-azure-performance-counters-do-not-show-up-at-all"></a>![Logotipo do Windows.][Logo_Windows] Os contadores de desempenho do Azure não aparecem

O serviço AzureEnhancedMonitoring Windows coleta métricas de desempenho no Azure. Se o serviço não tiver sido instalado corretamente ou não estiver em execução na VM, nenhuma métrica de desempenho poderá ser coletada.

##### <a name="the-installation-directory-of-the-azure-extension-for-sap-is-empty"></a>O diretório de instalação da Extensão para SAP do Azure está vazio

###### <a name="issue"></a>Problema

O diretório de instalação C:\\Packages\\Plugins\\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\\&lt;version>\\drop está vazio.

###### <a name="solution"></a>Solução

A extensão não está instalada. Determine se esse é um problema de proxy (conforme descrito anteriormente). Talvez seja necessário reiniciar a máquina ou executar novamente o script de configuração `Set-AzVMAEMExtension`.

##### <a name="service-for-azure-extension-for-sap-does-not-exist"></a>O serviço para a Extensão para SAP do Azure não existe

###### <a name="issue"></a>Problema

O serviço AzureEnhancedMonitoring Windows não existe.

A saída de Azperflib.exe gera um erro:

![A execução de azperflib.exe indica que o serviço da Extensão para SAP do Azure não está em execução][deployment-guide-figure-1400]
<a name="figure-14"></a>

###### <a name="solution"></a>Solução

Caso o serviço não exista, a Extensão para SAP do Azure não foi instalada corretamente. Reimplante a extensão usando as etapas descritas para seu cenário de implantação em [Cenários de implantação de VMs para SAP no Azure][deployment-guide-3].

Depois de implantar a extensão, após uma hora, verifique novamente se os contadores de desempenho do Azure são fornecidos na VM do Azure.

##### <a name="service-for-azure-extension-for-sap-exists-but-fails-to-start"></a>O serviço de Extensão para SAP do Azure existe, mas não pode ser iniciado

###### <a name="issue"></a>Problema

O serviço Windows AzureEnhancedMonitoring existe e está habilitado, mas não é iniciado. Para obter mais informações, confira o log de eventos do aplicativo.

###### <a name="solution"></a>Solução

A configuração está incorreta. Reinicie a Extensão para SAP do Azure na VM, conforme descrito em [Configurar a Extensão para SAP do Azure][deployment-guide-4.5].

#### <a name="windows-logologo_windows-some-azure-performance-counters-are-missing"></a>![Logotipo do Windows.][Logo_Windows] Faltam alguns contadores de desempenho do Azure

O serviço AzureEnhancedMonitoring Windows coleta métricas de desempenho no Azure. O serviço obtém dados de várias fontes. Alguns dados de configuração são coletados localmente e algumas métricas de desempenho são lidas do Diagnóstico do Azure. Contadores de armazenamento são utilizados de seu logon no nível de assinatura de armazenamento.

Se estiver solucionando problemas usando a Nota SAP [1999351] e isso não resolver o problema, execute novamente o script de configuração `Set-AzVMAEMExtension`. Talvez você precise esperar por uma hora porque os contadores de diagnóstico ou análise de armazenamento podem não ser criados imediatamente depois de terem sido habilitados. Se o problema persistir, abra uma mensagem de suporte ao cliente SAP no componente BC-OP-NT-AZR para Windows ou BC-OP-LNX-AZR para uma máquina virtual Linux.

#### <a name="linux-logologo_linux-azure-performance-counters-do-not-show-up-at-all"></a>![Logotipo do Linux.][Logo_Linux] Os contadores de desempenho do Azure não aparecem

Métricas de desempenho no Azure são coletadas por um daemon. Se o daemon não estiver em execução, nenhuma métrica de desempenho poderá ser coletada.

##### <a name="the-installation-directory-of-the-azure-extension-for-sap-is-empty"></a>O diretório de instalação da Extensão para SAP do Azure está vazio

###### <a name="issue"></a>Problema

O diretório \\var\\lib\\waagent\\ não tem um subdiretório para a Extensão para SAP do Azure.

###### <a name="solution"></a>Solução

A extensão não está instalada. Determine se esse é um problema de proxy (conforme descrito anteriormente). Talvez seja necessário reiniciar a máquina e/ou executar novamente o script de configuração `Set-AzVMAEMExtension`.

##### <a name="the-execution-of-set-azvmaemextension-and-test-azvmaemextension-show-warning-messages-stating-that-standard-managed-disks-are-not-supported"></a>A execução de Set-AzVMAEMExtension e Test-AzureRmVMAEMExtension mostra mensagens de aviso indicando que os Managed Disks Standard não são compatíveis

###### <a name="issue"></a>Problema

Ao executar Set-AzVMAEMExtension ou Test-AzVMAEMExtension mensagens como estas são exibidas:

<pre><code>
WARNING: [WARN] Standard Managed Disks are not supported. Extension will be installed but no disk metrics will be available.
WARNING: [WARN] Standard Managed Disks are not supported. Extension will be installed but no disk metrics will be available.
WARNING: [WARN] Standard Managed Disks are not supported. Extension will be installed but no disk metrics will be available.
</code></pre>

Ao executar o azperfli.exe como descrito anteriormente, você pode obter um resultado que indica um estado não íntegro. 

###### <a name="solution"></a>Solução

As mensagens são causadas pelo fato de que os Managed Disks Standard não estão fornecendo as APIs usadas pela Extensão para SAP do Azure para verificar as estatísticas das Contas de Armazenamento do Azure Standard. Isso não é motivo de preocupação. O motivo para introduzir os dados de coleta para contas de Armazenamento em Disco padrão foi a limitação de entradas e saídas que ocorreram com frequência. Os discos gerenciados evitarão essa limitação limitando o número de discos em uma conta de armazenamento. Portanto, não ter esse tipo de dados não é crítico.


#### <a name="linux-logologo_linux-some-azure-performance-counters-are-missing"></a>![Logotipo do Linux.][Logo_Linux] Faltam alguns contadores de desempenho do Azure

Métricas de desempenho no Azure são coletadas por um daemon, que obtém os dados de várias fontes. Alguns dados de configuração são coletados localmente e algumas métricas de desempenho são lidas do Diagnóstico do Azure. Contadores de armazenamento vêm dos logs em sua assinatura de armazenamento.

Para obter uma lista completa e atualizada dos problemas conhecidos, confira a Nota SAP [1999351], que tem informações adicionais de solução de problemas para a Extensão para SAP do Azure.

Se estiver solucionando problemas usando a Nota SAP [1999351] e isso não resolver o problema, execute novamente o script de configuração `Set-AzVMAEMExtension`, conforme descrito em [Configurar a Extensão para SAP do Azure][deployment-guide-4.5]. Talvez você precise esperar por uma hora porque os contadores de diagnóstico ou análise de armazenamento podem não ser criados imediatamente depois de terem sido habilitados. Se o problema persistir, abra uma mensagem de suporte ao cliente SAP no componente BC-OP-NT-AZR para Windows ou BC-OP-LNX-AZR para uma máquina virtual Linux.

### <a name="troubleshooting-the-new-azure-extension-for-sap"></a><a name="b7afb8ef-a64c-495d-bb37-2af96688c530"></a>Solucionando problemas da nova extensão do Azure para SAP

> [!NOTE]
> Há duas versões da extensão de VM. Este capítulo aborda a nova extensão de VM. Se você tiver instalado a extensão de VM padrão, consulte [o capítulo Solucionando problemas da extensão do Azure para SAP][deployment-guide-5.3].

#### <a name="windows-logologo_windows-azure-performance-counters-do-not-show-up-at-all"></a>![Logotipo do Windows.][Logo_Windows] Os contadores de desempenho do Azure não aparecem

O processo AzureEnhancedMonitoring coleta métricas de desempenho no Azure. Se o processo não estiver em execução em sua VM, nenhuma métrica de desempenho poderá ser coletada.

##### <a name="the-installation-directory-of-the-azure-extension-for-sap-is-empty"></a>O diretório de instalação da Extensão para SAP do Azure está vazio

###### <a name="issue"></a>Problema

O diretório de instalação C: \\ Packages \\ plugins \\ Microsoft. AzureCAT. AzureEnhancedMonitoring. MonitorX64Windows \\ &lt; versão> está vazio.

###### <a name="solution"></a>Solução

A extensão não está instalada. Determine se esse é um problema de proxy (conforme descrito anteriormente). Talvez seja necessário reiniciar o computador ou instalar a extensão de VM novamente.

#### <a name="windows-logologo_windows-some-azure-performance-counters-are-missing"></a>![Logotipo do Windows.][Logo_Windows] Faltam alguns contadores de desempenho do Azure

O processo do Windows AzureEnhancedMonitoring coleta métricas de desempenho no Azure. O processo obtém dados de várias fontes. Alguns dados de configuração são coletados localmente e algumas métricas de desempenho são lidas de Azure Monitor.

Se a solução de problemas usando o SAP Note [1999351] não resolver o problema, abra uma mensagem de suporte ao cliente do SAP no componente BC-op-NT-AZR para Windows ou BC-op-lnx-AZR para uma máquina virtual Linux. Anexe o arquivo de log C: \\ Packages \\ plugins \\ Microsoft. AzureCAT. AzureEnhancedMonitoring. MonitorX64Windows \\ &lt; versão>\\logapp.txt ao incidente.

#### <a name="linux-logologo_linux-azure-performance-counters-do-not-show-up-at-all"></a>![Logotipo do Linux.][Logo_Linux] Os contadores de desempenho do Azure não aparecem

Métricas de desempenho no Azure são coletadas por um daemon. Se o daemon não estiver em execução, nenhuma métrica de desempenho poderá ser coletada.

##### <a name="the-installation-directory-of-the-azure-extension-for-sap-is-empty"></a>O diretório de instalação da Extensão para SAP do Azure está vazio

###### <a name="issue"></a>Problema

O diretório \\var\\lib\\waagent\\ não tem um subdiretório para a Extensão para SAP do Azure.

###### <a name="solution"></a>Solução

A extensão não está instalada. Determine se esse é um problema de proxy (conforme descrito anteriormente). Talvez seja necessário reiniciar o computador e/ou instalar a extensão de VM novamente.

#### <a name="linux-logologo_linux-some-azure-performance-counters-are-missing"></a>![Logotipo do Linux.][Logo_Linux] Faltam alguns contadores de desempenho do Azure

Métricas de desempenho no Azure são coletadas por um daemon, que obtém os dados de várias fontes. Alguns dados de configuração são coletados localmente e algumas métricas de desempenho são lidas de Azure Monitor.

Para obter uma lista completa e atualizada dos problemas conhecidos, confira a Nota SAP [1999351], que tem informações adicionais de solução de problemas para a Extensão para SAP do Azure.

Se a solução de problemas usando o SAP Note [1999351] não resolver o problema, instale a extensão novamente, conforme descrito em [Configurar a extensão do Azure para SAP][deployment-guide-4.5]. Se o problema persistir, abra uma mensagem de suporte ao cliente SAP no componente BC-OP-NT-AZR para Windows ou BC-OP-LNX-AZR para uma máquina virtual Linux. Anexe o arquivo de log/var/lib/waagent/Microsoft.AzureCAT.AzureEnhancedMonitoring.MonitorX64Linux- &lt; versão>/logapp.txt ao incidente.

## <a name="azure-extension-error-codes"></a>Códigos de erro de extensão do Azure

| ID do erro | Descrição do erro | Solução |
|---|---|---|
| <a name="cfg_018"></a>cfg/018 | A configuração do aplicativo está ausente. | [Executar script de instalação][deployment-guide-run-the-script] |
| <a name="cfg_019"></a>cfg/019 | Nenhuma ID de implantação na configuração do aplicativo. | [Contatar o suporte][deployment-guide-contact-support] |
| <a name="cfg_020"></a>cfg/020 | Nenhum RoleInstanceId na configuração do aplicativo. | [Contatar o suporte][deployment-guide-contact-support] |
| <a name="cfg_022"></a>cfg/022 | Nenhum RoleInstanceId na configuração do aplicativo. | [Contatar o suporte][deployment-guide-contact-support] |
| <a name="cfg_031"></a>cfg/031 | Não é possível ler a configuração do Azure. | [Contatar o suporte][deployment-guide-contact-support] |
| <a name="cfg_021"></a>cfg/021 | O arquivo de configuração do aplicativo está ausente. | [Executar script de instalação][deployment-guide-run-the-script] |
| <a name="cfg_015"></a>cfg/015 | Nenhum tamanho de VM na configuração do aplicativo. | [Executar script de instalação][deployment-guide-run-the-script] |
| <a name="cfg_016"></a>cfg/016 | Falha no contador GlobalMemoryStatusEx. | [Contatar o suporte][deployment-guide-contact-support] |
| <a name="cfg_023"></a>cfg/023 | Falha no contador MaxHwFrequency. | [Contatar o suporte][deployment-guide-contact-support] |
| <a name="cfg_024"></a>cfg/024 | Falha nos contadores da NIC. | [Contatar o suporte][deployment-guide-contact-support] |
| <a name="cfg_025"></a>cfg/025 | Falha no contador de mapeamento de disco. | [Contatar o suporte][deployment-guide-contact-support] |
| <a name="cfg_026"></a>cfg/026 | Falha no contador de nome do processador. | [Contatar o suporte][deployment-guide-contact-support] |
| <a name="cfg_027"></a>cfg/027 | Falha no contador de mapeamento de disco. | [Contatar o suporte][deployment-guide-contact-support] |
| <a name="cfg_038"></a>cfg/038 | A métrica ' tipo de disco ' está ausente no arquivo de configuração de extensão config.xml. O ' tipo de disco ', juntamente com alguns outros contadores, foi introduzido no v 2.2.0.68 12/16/2015. Se você implantou a extensão antes de 12/16/2015, ela usa o arquivo de configuração antigo. A estrutura de extensão do Azure atualiza automaticamente a extensão para uma versão mais recente, mas a config.xml permanece inalterada. Para atualizar a configuração, baixe e execute o script de instalação mais recente do PowerShell. | [Executar script de instalação][deployment-guide-run-the-script] |
| <a name="cfg_039"></a>cfg/039 | Nenhum cache de disco. | [Executar script de instalação][deployment-guide-run-the-script] |
| <a name="cfg_036"></a>cfg/036 | Nenhuma taxa de transferência de SLA de disco. | [Executar script de instalação][deployment-guide-run-the-script] |
| <a name="cfg_037"></a>cfg/037 | Nenhum IOPS de SLA de disco. | [Executar script de instalação][deployment-guide-run-the-script] |
| <a name="cfg_028"></a>cfg/028 | Falha no contador de mapeamento de disco. | [Contatar o suporte][deployment-guide-contact-support] |
| <a name="cfg_029"></a>cfg/029 | Falha no último contador de alteração de hardware. | [Contatar o suporte][deployment-guide-contact-support] |
| <a name="cfg_030"></a>cfg/030 | Contadores de NIC com falha | [Contatar o suporte][deployment-guide-contact-support] |
| <a name="cfg_017"></a>cfg/017 | Devido ao Sysprep da VM, seu SID do Windows foi alterado. | [reimplantar após Sysprep][deployment-guide-redeploy-after-sysprep] |
| <a name="str_007"></a>Str/007 | Falha no acesso à análise de armazenamento. <br /><br />Como a população de dados de análise de armazenamento em uma VM recém-criada pode precisar de até meia hora, o erro pode desaparecer após algum tempo. Se o erro ainda aparecer, execute novamente o script de instalação. | [Executar script de instalação][deployment-guide-run-the-script] |
| <a name="str_010"></a>Str/010 | Nenhum contador de Análise de Armazenamento. | [Executar script de instalação][deployment-guide-run-the-script] |
| <a name="str_009"></a>Str/009 | Falha na Análise de Armazenamento. | [Executar script de instalação][deployment-guide-run-the-script] |
| <a name="wad_004"></a>wad/004 | Configuração de WAD incorreta. | [Executar script de instalação][deployment-guide-run-the-script] |
| <a name="wad_002"></a>wad/002 | Formato WAD inesperado. | [Contatar o suporte][deployment-guide-contact-support] |
| <a name="wad_001"></a>wad/001 | Nenhum contador de WAD encontrado. | [Executar script de instalação][deployment-guide-run-the-script] |
| <a name="wad_040"></a>wad/040 | Contadores de WAD obsoletos encontrados. | [Contatar o suporte][deployment-guide-contact-support] |
| <a name="wad_003"></a>wad/003 | Não é possível ler a tabela WAD. Não há conexão com a tabela WAD. Pode haver várias causas:<br /><br /> 1) configuração desatualizada <br />2) nenhuma conexão de rede com o Azure <br />3) problemas com a configuração do WAD | [Executar script de instalação][deployment-guide-run-the-script]<br />[corrigir conexão com a Internet][deployment-guide-fix-internet-connection]<br />[Contatar o suporte][deployment-guide-contact-support] |
| <a name="prf_011"></a>PRF/011 | Falha nas métricas de NIC Perfmon. | [Contatar o suporte][deployment-guide-contact-support] |
| <a name="prf_012"></a>PRF/012 | Falha nas métricas do disco Perfmon. | [Contatar o suporte][deployment-guide-contact-support] |
| <a name="prf_013"></a>PRF/013 | Algumas métricas prefmon falharam. | [Contatar o suporte][deployment-guide-contact-support] |
| <a name="prf_014"></a>PRF/014 | O PerfMon não pôde criar um contador. | [Contatar o suporte][deployment-guide-contact-support] |
| <a name="cfg_035"></a>cfg/035 | Não há provedores de métrica configurados. | [Contatar o suporte][deployment-guide-contact-support] |
| <a name="str_006"></a>Str/006 | Configuração de Análise de Armazenamento inadequada. | [Executar script de instalação][deployment-guide-run-the-script] |
| <a name="str_032"></a>Str/032 | Falha ao Análise de Armazenamento métricas. | [Executar script de instalação][deployment-guide-run-the-script] |
| <a name="cfg_033"></a>cfg/033 | Um dos provedores de métrica falhou. | [Executar script de instalação][deployment-guide-run-the-script] |
| <a name="str_034"></a>Str/034 | Falha no thread do provedor. | [Contatar o suporte][deployment-guide-contact-support] |

### <a name="detailed-guidelines-on-solutions-provided"></a>Diretrizes detalhadas sobre as soluções fornecidas

#### <a name="run-the-setup-script"></a><a name="0d2847ad-865d-4a4c-a405-f9b7baaa00c7"></a>Executar o script de instalação

Siga as etapas no capítulo [Configurar a extensão do Azure para SAP][deployment-guide-4.5] neste guia para instalar a extensão novamente. Observe que alguns contadores podem precisar de até 30 minutos para o provisionamento.

Se os erros não desaparecerem, [contate o suporte][deployment-guide-contact-support].

#### <a name="contact-support"></a><a name="3ba34cfc-c9bb-4648-9c3c-88e8b9130ca2"></a>Contatar o suporte

Erro inesperado ou não há solução conhecida. Colete o arquivo AzureEnhancedMonitoring_service. log localizado na pasta C:\Packages\Plugins\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler \\ \<version\> \Drop (Windows) ou/var/log/Azure/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux (Linux) e entre em contato com o suporte SAP para obter assistência adicional.

#### <a name="redeploy-after-sysprep"></a><a name="2cd61f22-187d-42ed-bb8c-def0c983d756"></a>Reimplantar após Sysprep

Se você planeja criar uma imagem generalizada do so Sysprep (que pode incluir o software SAP), é recomendável que essa imagem não inclua a extensão do Azure para SAP. Você deve instalar a extensão do Azure para SAP depois que a nova instância da imagem do sistema operacional generalizada tiver sido implantada.

No entanto, se sua imagem de so generalizada e Sysprep já contiver a extensão do Azure para SAP, você poderá aplicar a seguinte solução alternativa para reconfigurar a extensão, na instância de VM recém implantada:

* Na instância de VM implantada recentemente, exclua o conteúdo das seguintes pastas:  
  C:\Packages\Plugins\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler \\ \<version\> \RuntimeSettings C:\Packages\Plugins\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler \\ \<version\> \Status

* Siga as etapas no capítulo [Configurar a extensão do Azure para SAP][deployment-guide-4.5] neste guia para instalar a extensão novamente.

#### <a name="fix-internet-connection"></a><a name="e92bc57d-80d9-4a2b-a2f4-16713a22ad89"></a>Corrigir conexão com a Internet

O Microsoft Azure máquina virtual que executa a extensão do Azure para SAP requer acesso à Internet. Se essa VM do Azure fizer parte de uma rede virtual do Azure ou de um domínio local, verifique se as configurações de proxy relevantes estão definidas. Essas configurações também devem ser válidas para a conta LocalSystem acessar a Internet. Siga [o capítulo configurar o proxy][deployment-guide-configure-proxy] neste guia.

Além disso, se você precisar definir um endereço IP estático para sua VM do Azure, não defina-o manualmente dentro da VM do Azure, mas defina-o usando [Azure PowerShell](../../../virtual-network/virtual-networks-static-private-ip-arm-ps.md) [CLI do Azure](../../../virtual-network/virtual-networks-static-private-ip-arm-cli.md) [portal do Azure](../../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md). O IP estático é propagado por meio do serviço DHCP do Azure.

A configuração manual de um endereço IP estático dentro da VM do Azure não é suportada e pode levar a problemas com a extensão do Azure para SAP.
