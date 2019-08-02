---
title: Preparação da infraestrutura do Azure para alta disponibilidade do SAP usando o cluster de failover do Windows e o disco compartilhado para a instância SAP ASCS/SCS | Microsoft Docs
description: Aprenda a preparação da infraestrutura do Azure para alta disponibilidade do SAP usando o cluster de failover do Windows e o disco compartilhado para a instância SAP ASCS/SCS.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: gwallace
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ec976257-396b-42a0-8ea1-01c97f820fa6
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b4e107da9d8e5019ba51769d283f3faa34839380
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67709250"
---
# <a name="prepare-the-azure-infrastructure-for-sap-ha-by-using-a-windows-failover-cluster-and-shared-disk-for-sap-ascsscs"></a>Prepare a infraestrutura do Azure para alta disponibilidade do SAP usando o cluster de failover do Windows e o disco compartilhado para a instância SAP ASCS/SCS

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides
[tuning-failover-cluster-network-thresholds]:https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834

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
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-ha-guide-9.1.1]:high-availability-guide.md#a97ad604-9094-44fe-a364-f89cb39bf097
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f


[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-infrastructure-wsfc-shared-disk-vpn]:sap-high-availability-infrastructure-wsfc-shared-disk.md#c87a8d3f-b1dc-4d2f-b23c-da4b72977489
[sap-high-availability-infrastructure-wsfc-shared-disk-change-def-ilb]:sap-high-availability-infrastructure-wsfc-shared-disk.md#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-high-availability-infrastructure-wsfc-shared-disk-setup-wsfc]:sap-high-availability-infrastructure-wsfc-shared-disk.md#0d67f090-7928-43e0-8772-5ccbf8f59aab
[sap-high-availability-infrastructure-wsfc-shared-disk-collect-cluster-config]:sap-high-availability-infrastructure-wsfc-shared-disk.md#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79
[sap-high-availability-infrastructure-wsfc-shared-disk-install-sios]:sap-high-availability-infrastructure-wsfc-shared-disk.md#5c8e5482-841e-45e1-a89d-a05c0907c868
[sap-high-availability-infrastructure-wsfc-shared-disk-add-dot-net]:sap-high-availability-infrastructure-wsfc-shared-disk.md#1c2788c3-3648-4e82-9e0d-e058e475e2a3
[sap-high-availability-infrastructure-wsfc-shared-disk-install-sios-both-nodes]:sap-high-availability-infrastructure-wsfc-shared-disk.md#dd41d5a2-8083-415b-9878-839652812102
[sap-high-availability-infrastructure-wsfc-shared-disk-setup-sios]:sap-high-availability-infrastructure-wsfc-shared-disk.md#d9c1fc8e-8710-4dff-bec2-1f535db7b006

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

[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps-md%2Fazuredeploy.json

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/resource-group-overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md


> ![Windows][Logo_Windows] Windows
>

Este artigo descreve as etapas para preparar a infraestrutura do Azure para instalar e configurar um sistema SAP de alta disponibilidade em um cluster de failover do Windows usando um *disco compartilhado de cluster* como uma opção para clustering de um Instância SAP ASCS.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar a instalação, analise este artigo:

* [Guia de arquitetura: Uma instância do SAP ASCS/SCS de cluster em um cluster de failover do Windows usando um disco compartilhado de cluster][sap-high-availability-guide-wsfc-shared-disk]

## <a name="prepare-the-infrastructure-for-architectural-template-1"></a>Preparar a infraestrutura para o Modelo Arquitetônico 1
Os modelos do Azure Resource Manager para SAP ajudam a simplificar a implantação dos recursos necessários.

Os modelos de três camadas no Azure Resource Manager também dão suporte a cenários de alta disponibilidade, tal como o Modelo Arquitetônico 1, que tem dois clusters. Por exemplo, 1 arquitetura de modelo tem dois clusters. Cada cluster é um ponto único de falha de SAP para SAP ASCS/SCS e DBMS.

É aqui que você pode obter os modelos do Azure Resource Manager para o cenário de exemplo descrito neste artigo:

* [Imagem do Azure Marketplace](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image)  
* [Imagem do Marketplace do Azure usando Managed Disks](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-md)  
* [Imagem personalizada](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image)
* [Imagem personalizada usando Managed Disks](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-md)

Para preparar a infraestrutura para o Modelo Arquitetônico 1:

- No Portal do Azure, na folha **Parâmetros**, na caixa **SYSTEMAVAILABILITY**, selecione **HA**.

  ![Figura 1: Definir os parâmetros de SAP de alta disponibilidade do Azure Resource Manager][sap-ha-guide-figure-3000]

_**Figura 1:** Definir os parâmetros de SAP de alta disponibilidade do Azure Resource Manager_


  Os modelos criam:

  * **Máquinas virtuais**:
    * Máquinas virtuais do Servidor de Aplicativos SAP: \<SAPSystemSID\>-di-\<número\>
    * Máquinas virtuais de cluster ASCS/SCS: \<SAPSystemSID\>-ascs-\<número\>
    * Cluster do DBMS: \<SAPSystemSID\>-db-\<número\>

  * **Placas de rede para todas as máquinas virtuais, com endereços IP associados**:
    * \<SAPSystemSID\>- nic-di -\<número\>
    * \<SAPSystemSID\>- nic-ascs -\<número\>
    * \<SAPSystemSID\>- nic-db -\<número\>

  * **Contas de armazenamento do Azure (apenas discos não gerenciados)** :

  * **Grupos de Disponibilidade** para:
    * Máquinas virtuais do Servidor de Aplicativos SAP: \<SAPSystemSID\>-avset-di
    * Máquinas virtuais de cluster do SAP ASCS/SCS: \<SAPSystemSID\>-avset-ascs
    * Máquinas virtuais do cluster DBMS: \<SAPSystemSID\>-avset-db

  * **Balanceador de carga interno do Azure**:
    * Com todas as portas para a instância ASCS/SCS e endereço IP <\<SAPSystemSID\>>-lb-ascs
    * Com todas as portas para o DBMS do SQL Server e endereço IP <\<SAPSystemSID\>>-lb-db

  * **Grupo de segurança de rede**: \<SAPSystemSID\>-nsg-ascs-0  
    * Com uma porta de protocolo RDP externa aberta para o <\<SAPSystemSID\>>-ascs-0 virtual machine

> [!NOTE]
> Todos os endereços IP das placas de rede e dos balanceadores de carga interno do Azure são dinâmicos por padrão. Altere-os para endereços IP Estáticos. Descrevemos como fazer isso posteriormente neste artigo.
>
>

## <a name="c87a8d3f-b1dc-4d2f-b23c-da4b72977489"></a> Implantar máquinas virtuais com conectividade de rede corporativa (entre locais) para uso na produção
Para sistemas SAP de produção, implante máquinas virtuais do Azure com [conectividade de rede corporativa (entre instalações)][planning-guide-2.2] usando o Gateway de VPN do Azure ou Azure ExpressRoute.

> [!NOTE]
> Você pode usar a instância da Rede Virtual do Azure. A rede virtual e a sub-rede já foram criadas e preparadas.
>
>

1. No Portal do Azure, na folha **Parâmetros**, na caixa **NEWOREXISTINGSUBNET**, selecione **existente**.
2. Na caixa **SUBNETID**, adicione a cadeia de caracteres completa da sua SubnetID da rede do Azure preparada na qual você planeja implantar suas máquinas virtuais do Azure.
3. Para obter uma lista de todas as sub-redes da rede do Azure, execute este comando do PowerShell:

   ```powershell
   (Get-AzVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets
   ```

   O campo **ID** mostra o SUBNETID.
4. Para obter uma lista de todos os valores SUBNETID, execute este comando do PowerShell:

   ```powershell
   (Get-AzVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets.Id
   ```

   SUBNETID tem esta aparência:

   ```
   /subscriptions/<subscription ID>/resourceGroups/<VPN name>/providers/Microsoft.Network/virtualNetworks/azureVnet/subnets/<subnet name>
   ```

## <a name="7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310"></a> Implantar instâncias de SAP somente na nuvem para teste e demonstração
Você pode implantar o sistema SAP de alta disponibilidade em um modelo de implantação somente na nuvem. Esse tipo de implantação é principalmente útil para casos de uso de demonstração e teste. Ele não é adequado para casos de uso de produção.

- No Portal do Azure, na folha **Parâmetros**, na caixa **NEWOREXISTINGSUBNET**, selecione **existente**. Deixe o campo **SUBNETID** vazio.

  O modelo do Resource Manager de SAP cria automaticamente a rede virtual e a sub-rede do Azure.

> [!NOTE]
> Você também precisa implantar pelo menos uma máquina virtual dedicada para o Active Directory e DNS na mesma instância de rede virtual do Azure. O modelo não cria essas máquinas virtuais.
>
>


## <a name="prepare-the-infrastructure-for-architectural-template-2"></a>Preparar a infraestrutura para o Modelo Arquitetônico 2

Você pode usar esse modelos do Azure Resource Manager para SAP para ajudar a simplificar a implantação dos recursos de infraestrutura necessários para o Modelo Arquitetônico 2 de SAP.

Aqui é onde você pode obter os modelos do Azure Resource Manager para esse cenário de implantação:

* [Imagem do Azure Marketplace](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-converged)  
* [Imagem do Marketplace do Azure usando Managed Disks](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-converged-md)  
* [Imagem personalizada](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-converged)
* [Imagem personalizada usando Managed Disks](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-converged-md)


## <a name="prepare-the-infrastructure-for-architectural-template-3"></a>Preparar a infraestrutura para o Modelo Arquitetônico 3

Você pode preparar a infraestrutura e configurar SAP para vários SID. Por exemplo, você pode adicionar uma instância SAP ASCS/SCS adicional em uma configuração de cluster *existente*. Para obter mais informações, consulte [configurar uma instância do SAP ASCS/SCS adicional para uma configuração de cluster existente criar uma configuração de multi-SID do SAP no Azure Resource Manager][sap-ha-multi-sid-guide].

Se você quiser criar um novo cluster com múltiplos SID, use os [modelos de início rápido de vários SID no GitHub](https://github.com/Azure/azure-quickstart-templates).

Para criar um novo cluster de vários SID, você precisa implantar os três modelos a seguir:

* [Modelo de ASCS/SCS](#ASCS-SCS-template)
* [Modelo de banco de dados](#database-template)
* [Modelo dos servidores de aplicativo](#application-servers-template)

As seções a seguir têm mais detalhes sobre os modelos e os parâmetros que você precisa fornecer nos modelos.

### <a name="ASCS-SCS-template"></a> Modelo de ASCS/SCS

O modelo de ASCS/SCS implanta duas máquinas virtuais que podem ser usadas para criar um cluster de failover do Windows Server que hospeda várias instâncias de ASCS/SCS.

Para configurar o modelo de vários SID de ASCS/SCS, nos [modelo de vários SID de ASCS/SCS][sap-templates-3-tier-multisid-xscs-marketplace-image] or [ASCS/SCS multi-SID template by using Managed Disks][sap-templates-3-tier-multisid-xscs-marketplace-image-md], insira valores para os seguintes parâmetros:

- **Prefixo do recurso**:  Defina o prefixo do recurso, que é usado para prefixar todos os recursos que são criados durante a implantação. Como os recursos não pertencem apenas a um sistema SAP, o prefixo do recurso não é o SID de um sistema SAP.  O prefixo deve ter entre três e seis caracteres.
- **Tipo de pilha**: Selecione o tipo de pilha do sistema SAP. Dependendo do tipo de pilha, o Azure Load Balancer tem um (ABAP ou Java apenas) ou dois (ABAP+Java) endereços IP privados por sistema de SAP.
- **Tipo de SO**: Selecione o sistema operacional das máquinas virtuais.
- **Contagem do sistema SAP**: Escolha o número de sistemas SAP que você deseja instalar no cluster.
- **Disponibilidade do Sistema**: Selecione **HA**.
- **Nome de Usuário do Administrador e Senha de Administrador**: Cria um novo usuário que pode ser usado para se conectar no computador.
- **Sub-rede nova ou existente**: determine a criação de uma nova rede virtual e uma sub-rede ou o uso de uma sub-rede existente. Se você já tiver uma rede virtual conectada à sua rede local, selecione a rede **existente**.
- **ID da sub-rede**: Se você deseja implantar a VM em uma rede virtual existente em que você tem uma sub-rede definida para a qual a VM deve ser designada, nomeie a identificação dessa sub-rede específica. A ID geralmente tem esta aparência:

  /subscriptions/\<subscription id>\>/resourceGroups/\<resource group name\>/providers/Microsoft.Network/virtualNetworks/\<virtual network name\>/subnets/\<subnet name\>

O modelo implanta uma instância do Azure Load Balancer que dá suporte a vários sistemas SAP:

- As instâncias ASCS são configuradas para o número de instância 00, 10, 20...
- As instâncias SCS são configuradas para o número de instância 01, 11, 21...
- As instâncias ASCS ERS (Enqueue Replication Server) (somente Linux) são configuradas para o número de instância 02, 12, 22...
- As instâncias SCS ERS (somente Linux) são configuradas para o número de instância 03, 13, 23...

O balanceador de carga contém 1 (2 para Linux) VIPs, 1x VIP para ASCS/SCS e 1x VIP para ERS (somente no Linux).

#### <a name="0f3ee255-b31e-4b8a-a95a-d9ed6200468b"></a> SID do SAP ASCS/SCS
A lista a seguir contém todas as regras de balanceamento de carga (em que x é o número do sistema SAP, por exemplo, 1, 2, 3...):
- Portas específicas do Windows para cada o sistema SAP: 445, 5985
- Portas ASCS (número de instância x0): 32x0, 36x0, 39x0, 81x0, 5x013, 5x014, 5x016
- Portas SSCS (número de instância x1): 32x1, 33x1, 39x1, 81x1, 5x113, 5x114, 5x116
- Portas ASCS ERS no Linux (número de instância x2): 33x2, 5x213, 5x214, 5x216
- Portas SCS ERS no Linux (número de instância x3): 33x3, 5x313, 5x314, 5x316

O balanceador de carga será configurado para usar as seguintes portas de investigação (em que x é o número do sistema SAP, por exemplo, 1, 2, 3...):
- Porta de investigação do balanceador de carga interno ASCS/SCS: 620x0
- Porta de investigação do balanceador de carga interno ERS (somente Linux): 621x2

### <a name="database-template"></a> Modelo de banco de dados

O modelo de banco de dados implanta uma ou duas máquinas virtuais que você pode usar para instalar o RDBMS (sistema de gerenciamento de banco de dados relacional) para um sistema SAP. Se, por exemplo, você implantou um modelo ASCS/SCS para cinco sistemas SAP, precisará implantar esse modelo cinco vezes.

Para configurar o modelo de vários SID de banco de dados, além de [modelo de vários SID de banco de dados][sap-templates-3-tier-multisid-db-marketplace-image] or [database multi-SID template by using Managed Disks][sap-templates-3-tier-multisid-db-marketplace-image-md], insira valores para os seguintes parâmetros:

- **ID do sistema SAP**: insira a ID do sistema SAP relativa ao sistema SAP que você deseja instalar. A ID é usada como um prefixo para os recursos que serão implantados.
- **Tipo de SO**: Selecione o sistema operacional das máquinas virtuais.
- **Dbtype**: escolha o tipo de banco de dados que você deseja instalar no cluster. Selecione **SQL** se você deseja instalar o Microsoft SQL Server. Selecione **HANA** se você planeja instalar SAP HANA nas máquinas virtuais. Certifique-se de que você selecione o tipo de sistema operacional correto. Selecione **Windows** para SQL e selecione uma distribuição de Linux para HANA. O Azure Load Balancer que está conectado às máquinas virtuais é configurado para suporte ao tipo de banco de dados selecionado:
  * **SQL**: O balanceador de carga balanceia a carga da porta 1433. Use essa porta para a instalação do SQL Server Always On.
  * **HANA**: O balanceador de carga balanceia a carga das portas 35015 e 35017. Instale o SAP HANA com o número de instância **50**.
  O balanceador de carga usa a porta de investigação 62550.
- **Tamanho do Sistema SAP**: Defina o número de SAPS que o novo sistema fornece. Se não tiver certeza de quantos SAPS o sistema precisará, pergunte ao Parceiro de Tecnologia SAP ou ao Integrador de Sistemas.
- **Disponibilidade do Sistema**: Selecione **HA**.
- **Nome de Usuário do Administrador e Senha de Administrador**: Cria um novo usuário que pode ser usado para se conectar no computador.
- **ID da sub-rede**: Insira a ID da sub-rede que você usou durante a implantação do modelo ASCS/SCS ou a ID da sub-rede que foi criada como parte da implantação do modelo ASCS/SCS.

### <a name="application-servers-template"></a> Modelo dos servidores de aplicativos

O modelo de servidores de aplicativo implanta duas ou mais máquinas virtuais que podem ser usadas como instâncias do Servidor de Aplicativos SAP para um sistema SAP. Se, por exemplo, você implantou um modelo ASCS/SCS para cinco sistemas SAP, precisará implantar esse modelo cinco vezes.

Para configurar o modelo de vários SID de servidores de aplicativos, além de [modelo de multi-SID de servidores de aplicativos][sap-templates-3-tier-multisid-apps-marketplace-image] or [application servers multi-SID template  by using Managed Disks][sap-templates-3-tier-multisid-apps-marketplace-image-md], insira valores para os seguintes parâmetros:

  -  **ID do sistema SAP**: insira a ID do sistema SAP relativa ao sistema SAP que você deseja instalar. A ID é usada como um prefixo para os recursos que serão implantados.
  -  **Tipo de SO**: Selecione o sistema operacional das máquinas virtuais.
  -  **Tamanho do Sistema SAP**: O número de SAPS que o novo sistema fornece. Se não tiver certeza de quantos SAPS o sistema precisará, pergunte ao Parceiro de Tecnologia SAP ou ao Integrador de Sistemas.
  -  **Disponibilidade do Sistema**: Selecione **HA**.
  -  **Nome de Usuário do Administrador e Senha de Administrador**: Cria um novo usuário que pode ser usado para se conectar no computador.
  -  **ID da sub-rede**: Insira a ID da sub-rede que você usou durante a implantação do modelo ASCS/SCS ou a ID da sub-rede que foi criada como parte da implantação do modelo ASCS/SCS.


## <a name="47d5300a-a830-41d4-83dd-1a0d1ffdbe6a"></a> Rede virtual do Azure
Em nosso exemplo, o espaço de endereço da rede virtual do Azure é 10.0.0.0/16. Há uma sub-rede chamada Subnet, com um intervalo de endereços de 10.0.0.0/24. Todas as máquinas virtuais e balanceadores de carga internos são implantados nessa rede virtual.

> [!IMPORTANT]
> Não faça nenhuma alteração nas configurações de rede dentro do sistema operacional convidado. Isso inclui endereços IP, servidores DNS e sub-rede. Configure todas as configurações de rede no Azure. O serviço do protocolo DHCP propaga as configurações.
>
>

## <a name="b22d7b3b-4343-40ff-a319-097e13f62f9e"></a> Endereços IP de DNS

Para definir os endereços IP de DNS necessários, execute as seguintes etapas:

1. No Portal do Azure, na folha de **Servidores DNS**, verifique se sua opção de rede virtual **Servidores DNS** está definida para **DNS Personalizado**.
2. Selecione suas configurações com base no seu tipo de rede. Para obter mais informações, consulte os seguintes recursos:
   * [Conectividade de rede corporativa (entre instalações)][planning-guide-2.2]: adicione os endereços IP dos servidores DNS locais.  
   Você pode estender os servidores DNS locais às máquinas virtuais que estão em execução no Azure. Nesse cenário, você pode adicionar os endereços IP das máquinas virtuais do Azure nos quais executa o serviço DNS.
   * Para implantações de VM que estão isoladas no Azure: implantar uma máquina virtual adicional na mesma instância de Rede Virtual que serve como servidor DNS. Adicione os endereços IP das máquinas virtuais do Azure que você configurou para executar o serviço DNS.

   ![Figura 2: Configurar servidores DNS da Rede Virtual do Azure][sap-ha-guide-figure-3001]

   _**Figura 2:** Configurar servidores DNS da Rede Virtual do Azure_

   > [!NOTE]
   > Se você mudar os endereços IP dos servidores DNS, será preciso reinicializar as máquinas virtuais do Azure de modo a aplicar a alteração e propagar os novos servidores DNS.
   >
   >

Em nosso exemplo, o serviço DNS está instalado e configurado nas seguintes máquinas virtuais do Windows:

| Função da máquina virtual | Nome do host de máquina virtual | Nome da placa de rede | Endereço IP estático |
| --- | --- | --- | --- |
| Primeiro servidor DNS |domcontr-0 |pr1-nic-domcontr-0 |10.0.0.10 |
| Segundo servidor DNS |domcontr-1 |pr1-nic-domcontr-1 |10.0.0.11 |

## <a name="9fbd43c0-5850-4965-9726-2a921d85d73f"></a> Nomes de host e endereços IP estáticos para a instância clusterizada do SAP ASCS/SCS e a instância clusterizada do DBMS

Para implantação local, você precisa destes endereços IP e nomes de host reservados:

| Função de nome de host virtual | Nome de host virtual | Endereço IP estático virtual |
| --- | --- | --- |
| Nome de host virtual do primeiro cluster SAP ASCS/SCS (para gerenciamento de cluster) |pr1-ascs-vir |10.0.0.42 |
| Nome de host virtual da instância do SAP ASCS/SCS |pr1-ascs-sap |10.0.0.43 |
| Nome de host virtual do segundo cluster SAP DBMS (gerenciamento de cluster) |pr1-dbms-vir |10.0.0.32 |

Quando você criar o cluster, crie os nomes de host virtual pr1-ascs-vir e pr1-dbms- vir e os endereços IP associados que gerenciam o cluster. Para obter informações sobre como fazer isso, consulte [coletar nós de cluster em uma configuração de cluster][sap-high-availability-infrastructure-wsfc-shared-disk-collect-cluster-config].

Você pode criar manualmente os outros dois nomes de host virtuais, pr1-ascs-sap e pr1-dbms-sap, e os endereços IP associados no servidor DNS. A instância clusterizada da instância SAP ASCS/SCS e a instância clusterizada do DBMS usam esses recursos. Para obter informações sobre como fazer isso, consulte [criar um nome de host virtual para uma instância clusterizada do SAP ASCS/SCS][sap-ha-guide-9.1.1].

## <a name="84c019fe-8c58-4dac-9e54-173efd4b2c30"></a> Definir endereços IP estáticos para as máquinas virtuais de SAP
Depois de implantar as máquinas virtuais para usar no seu cluster, você precisará definir endereços IP estáticos para todas as máquinas virtuais. Faça isso na configuração da Rede Virtual do Azure e não no sistema operacional convidado.

1. No Portal do Azure, selecione **Grupo de Recursos** > **Placa de Rede** > **Configurações** > **Endereço IP**.
2. Na folha **Endereços IP**, em **Atribuição**, selecione **Estático**. Na caixa **Endereço IP**, digite o endereço IP que você deseja usar.

   > [!NOTE]
   > Se você mudar o endereço IP da placa de rede, será preciso reinicializar as máquinas virtuais do Azure para aplicar a alteração.  
   >
   >

   ![Figura 3: Definir endereços IP estáticos para a placa de rede de cada máquina virtual][sap-ha-guide-figure-3002]

   _**Figura 3:** Definir endereços IP estáticos para a placa de rede de cada máquina virtual_

   Repita essa etapa para todas as interfaces de rede, isto é, para todas as máquinas virtuais, incluindo as máquinas virtuais que você deseja usar para seu serviço DNS/Active Directory.

Em nosso exemplo, temos estas máquinas virtuais e endereços IP estáticos:

| Função da máquina virtual | Nome do host de máquina virtual | Nome da placa de rede | Endereço IP estático |
| --- | --- | --- | --- |
| Primeira instância do Servidor de Aplicativos SAP |pr1-di-0 |pr1-nic-di-0 |10.0.0.50 |
| Segunda instância do Servidor de Aplicativos SAP |pr1-di-1 |pr1-nic-di-1 |10.0.0.51 |
| ... |... |... |... |
| Última instância do Servidor de Aplicativos SAP |pr1-di-5 |pr1-nic-di-5 |10.0.0.55 |
| Primeiro nó de cluster para a instância do ASCS/SCS |pr1-ascs-0 |pr1-nic-ascs-0 |10.0.0.40 |
| Segundo nó de cluster para a instância do ASCS/SCS |pr1-ascs-1 |pr1-nic-ascs-1 |10.0.0.41 |
| Primeiro nó de cluster para a instância do DBMS |pr1-db-0 |pr1-nic-db-0 |10.0.0.30 |
| Segundo nó de cluster para a instância do DBMS |pr1-db-1 |pr1-nic-db-1 |10.0.0.31 |

## <a name="7a8f3e9b-0624-4051-9e41-b73fff816a9e"></a> Defina um endereço IP estático para o balanceador interno de carga do Azure

O modelo do Azure Resource Manager para SAP cria um balanceador de carga interno do Azure usado para o cluster da instância do ASCS/SCS e para o cluster do DBMS do SAP.

> [!IMPORTANT]
> O endereço IP do nome de host virtual do SAP ASCS/SCS é o mesmo que o endereço IP do balanceador de carga interno SAP ASCS/SCS: pr1-lb-ascs.
> O endereço IP do nome de host virtual do SAP ASCS/SCS é o mesmo que o endereço IP do balanceador de carga interno SAP ASCS/SCS: pr1-lb-ascs.
>
>

Para definir um endereço IP estático para o balanceador interno de carga do Azure:

1. A implantação inicial define o endereço IP do balanceador de carga interno como **Dinâmico**. No Portal do Azure, na folha **Endereços IP**, em **Atribuição**, selecione **Estático**.
2. Defina o endereço IP do balanceador interno de carga, **pr1-lb-ascs**, para o endereço IP do nome de host virtual da instância do SAP ASCS/SCS.
3. Defina o endereço IP do balanceador interno de carga, **pr1-lb-dbms**, para o endereço IP do nome de host virtual da instância do DBMS.

   ![Figura 4: Definir endereços IP estáticos para o balanceador de carga interno da instância SAP ASCS/SCS][sap-ha-guide-figure-3003]

   _**Figura 4:** Definir endereços IP estáticos para o balanceador de carga interno da instância SAP ASCS/SCS_

Em nosso exemplo, temos dois balanceadores de carga internos do Azure que têm esses endereços IP estáticos:

| Função do balanceador de carga interno do Azure | Nome balanceador de carga interno do Azure | Endereço IP estático |
| --- | --- | --- |
| Balanceador de carga interno da instância SAP ASCS/SCS |pr1-lb-ascs |10.0.0.43 |
| Balanceador de carga interno de DBMS SAP |pr1-lb-dbms |10.0.0.33 |


## <a name="f19bd997-154d-4583-a46e-7f5a69d0153c"></a> Regras do balanceamento de carga do ASCS/SCS padrão para o balanceador de carga interno do Azure

O modelo do Azure Resource Manager de SAP cria as portas de que você precisa:
* Uma instância do ASCS ABAP com número de instância padrão 00
* Uma instância do SCS Java com número de instância padrão 01

Quando você instala sua instância SAP ASCS/SCS, deve usar o número de instância padrão 00 para sua instância ASCS ABAP e o número de instância padrão 01 para sua instância SCS Java.

Em seguida, crie os pontos de extremidade de balanceamento interno de carga necessários para as portas do SAP NetWeaver.

Para criar os pontos de extremidade de balanceamento interno de carga, crie esses pontos de extremidade do balanceamento de carga para as portas do ASCS ABAP do SAP NetWeaver:

| Nome da regra do balanceamento de carga/serviço | Números de porta padrão | Portas concretas para (instância do ASCS com número de instância 00) (ERS com 10) |
| --- | --- | --- |
| Enfileirar Servidor / *lbrule3200* |32\<InstanceNumber\> |3200 |
| Servidor de Mensagens ABAP / *lbrule3600* |36\<InstanceNumber\> |3600 |
| Mensagem interna ABAP / *lbrule3900* |39\<InstanceNumber\> |3900 |
| HTTP do Servidor de Mensagens / *Lbrule8100* |81\<InstanceNumber\> |8100 |
| HTTP do SAP para Iniciar Serviço ASCS / *Lbrule50013* |5\<InstanceNumber\>13 |50013 |
| HTTPS do SAP para Iniciar Serviço ASCS / *Lbrule50014* |5\<InstanceNumber\>14 |50014 |
| Enfileirar Replicação / *Lbrule50016* |5\<InstanceNumber\>16 |50016 |
| HTTP do SAP para Iniciar Serviço ERS *Lbrule51013* |5\<InstanceNumber\>13 |51013 |
| HTTP do SAP para Iniciar Serviço ERS *Lbrule51014* |5\<InstanceNumber\>14 |51014 |
| Gerenciamento remoto do Windows (WinRM) *Lbrule5985* | |5985 |
| Compartilhamento de Arquivos *Lbrule445* | |445 |

**Tabela 1:** números de porta das instâncias do ASCS ABAP do SAP NetWeaver

Em seguida, crie os pontos de extremidade do balanceamento de carga para as portas do SCS Java do SAP NetWeaver:

| Nome da regra do balanceamento de carga/serviço | Números de porta padrão | Portas concretas para (instância do SCS com número de instância 01) (ERS com 11) |
| --- | --- | --- |
| Enfileirar Servidor / *lbrule3201* |32\<InstanceNumber\> |3201 |
| Servidor do Gateway / *lbrule3301* |33\<InstanceNumber\> |3301 |
| Servidor de Mensagens Java / *lbrule3900* |39\<InstanceNumber\> |3901 |
| HTTP do Servidor de Mensagens / *Lbrule8101* |81\<InstanceNumber\> |8101 |
| HTTP do SAP para Iniciar Serviço SCS / *Lbrule50113* |5\<InstanceNumber\>13 |50113 |
| HTTPS do SAP para Iniciar Serviço SCS / *Lbrule50114* |5\<InstanceNumber\>14 |50114 |
| Enfileirar Replicação / *Lbrule50116* |5\<InstanceNumber\>16 |50116 |
| HTTP do SAP para Iniciar Serviço ERS *Lbrule51113* |5\<InstanceNumber\>13 |51113 |
| HTTP do SAP para Iniciar Serviço ERS *Lbrule51114* |5\<InstanceNumber\>14 |51114 |
| Gerenciamento Remoto do Windows *Lbrule5985* | |5985 |
| Compartilhamento de Arquivos *Lbrule445* | |445 |

**Tabela 2:** números de porta das instâncias do SCS Java do SAP NetWeaver

![Figura 5: Regras do balanceamento de carga do ASCS/SCS padrão para o balanceador de carga interno do Azure][sap-ha-guide-figure-3004]

_**Figura 5:** Regras do balanceamento de carga do ASCS/SCS padrão para o balanceador de carga interno do Azure_

Defina o endereço IP do balanceador de carga pr1-lb-dbms como o endereço IP do nome de host virtual da instância do DBMS.

### <a name="fe0bd8b5-2b43-45e3-8295-80bee5415716"></a> Alterar as regras do balanceamento de carga padrão do ASCS/SCS para o balanceador de carga interno do Azure

Se você quiser usar números diferentes para as instâncias SAP ASCS ou SCS, precisará alterar os nomes e os valores das portas do padrão.

1. No Portal do Azure, selecione **\<SID\>-lb-ascs**  > **Regras de Balanceamento de Carga**.
2. Para todas as regras de balanceamento de carga que pertencem à instância do SAP ASCS ou SCS, altere estes valores:

   * NOME
   * Porta
   * Porta de back-end

   Por exemplo, se você quer alterar o número de instância do ASCS padrão de 00 para 31, precisa fazer as alterações em todas as portas listadas na Tabela 1.

   Veja um exemplo de uma atualização para a porta *lbrule3200*.

   ![Figura 6: Altere as regras de balanceamento de carga padrão do ASCS/SCS para o balanceador de carga interno do Azure][sap-ha-guide-figure-3005]

   _**Figura 6:** Altere as regras de balanceamento de carga padrão do ASCS/SCS para o balanceador de carga interno do Azure_

## <a name="e69e9a34-4601-47a3-a41c-d2e11c626c0c"></a> Adicionar máquinas virtuais do Windows ao domínio

Depois de atribuir um endereço IP estático às máquinas virtuais, adicione as máquinas virtuais ao domínio.

![Figura 7: Adicionar uma máquina virtual a um domínio][sap-ha-guide-figure-3006]

_**Figura 7:** Adicionar uma máquina virtual a um domínio_

## <a name="661035b2-4d0f-4d31-86f8-dc0a50d78158"></a> Adicionar entradas de registro em ambos os nós de cluster da instância do SAP ASCS/SCS

O Azure Load Balancer tem um balanceador de carga interno que fecha conexões quando elas estão ociosas por determinado período de tempo (um tempo limite de ociosidade). Os processos de trabalho do SAP em instâncias de caixa de diálogo abrem conexões com o processo de enfileiramento do SAP assim que a primeira solicitação para enfileirar/desenfileirar precisa ser enviada. Essas conexões geralmente permanecem estabelecidas até que o processo de trabalho ou o processo de enfileiramento seja reiniciado. No entanto, se a conexão ficar ociosa por um certo período, o balanceador interno de carga do Azure fechará as conexões. Isso não é um problema porque o processo de trabalho do SAP restabelece a conexão com o processo de enfileiramento se ele não existe mais. Essas atividades são documentadas nos rastreamentos de desenvolvedor dos processos da SAP, mas criam uma grande quantidade de conteúdo extra nesses rastreamentos. É uma boa ideia para alterar o TCP/IP `KeepAliveTime` e `KeepAliveInterval` em ambos os nós de cluster. Combine essas alterações nos parâmetros de TCP/IP com parâmetros de perfil do SAP, descritos posteriormente neste artigo.

Para adicionar entradas de Registro em ambos os nós de cluster da instância SAP ASCS/SCS, primeiro adicione essas entradas de Registro do Windows a ambos os nós de cluster do Windows para SAP ASCS/SCS:

| Path | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Nome da variável |`KeepAliveTime` |
| Tipo de variável |REG_DWORD (Decimal) |
| Valor |120000 |
| Vincular à documentação |[https://technet.microsoft.com/library/cc957549.aspx](https://technet.microsoft.com/library/cc957549.aspx) |

**Tabela 3:** alterar o primeiro parâmetro de TCP/IP

Em seguida, adicione as seguintes entradas de Registro em nós de cluster do Windows para SAP ASCS/SCS:

| Path | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Nome da variável |`KeepAliveInterval` |
| Tipo de variável |REG_DWORD (Decimal) |
| Valor |120000 |
| Vincular à documentação |[https://technet.microsoft.com/library/cc957548.aspx](https://technet.microsoft.com/library/cc957548.aspx) |

**Tabela 4:** alterar o segundo parâmetro de TCP/IP

Para aplicar as alterações, reinicie os dois nós do cluster.

## <a name="0d67f090-7928-43e0-8772-5ccbf8f59aab"></a> Configurar um cluster Windows Server Failover Clustering para uma instância do SAP ASCS/SCS

Configurar um cluster Windows Server Failover Clustering para uma instância do SAP ASCS/SCS:

- Coletar nós de cluster em uma configuração do cluster.
- Configurar a testemunha de compartilhamento de arquivos do cluster.

### <a name="5eecb071-c703-4ccc-ba6d-fe9c6ded9d79"></a> Coletar nós de cluster em uma configuração do cluster

1. O Assistente para Adicionar Funções e Recursos adiciona clustering de failover a ambos os nós de cluster.
2. Configure o cluster de failover usando o Gerenciador de Cluster de Failover. No Gerenciador de Cluster de Failover, selecione **Criar Cluster** e adicione apenas o nome do primeiro nó A do cluster. Não adicione o segundo nó ainda; Você pode adicionar o segundo nó em uma etapa posterior.

   ![Figura 8: Adicionar o nome de servidor ou máquina virtual do primeiro nó de cluster][sap-ha-guide-figure-3007]

   _**Figura 8:** Adicionar o nome de servidor ou máquina virtual do primeiro nó de cluster_

3. Insira o nome de rede (nome de host virtual) do cluster.

   ![Figura 9: Inserir o nome do cluster][sap-ha-guide-figure-3008]

   _**Figura 9:** Inserir o nome do cluster_

4. Depois de criar o cluster, execute um teste de validação de cluster.

   ![Figura 10: Executar a verificação de validação de cluster][sap-ha-guide-figure-3009]

   _**Figura 10:** Executar a verificação de validação de cluster_

   Você pode ignorar os avisos sobre discos nessa altura do processo. Você adicionará uma testemunha de compartilhamento de arquivo e discos compartilhados do SIOS mais tarde. Neste estágio, você não precisa se preocupar em ter um quorum.

   ![Figura 11: Nenhum disco de quorum é encontrado][sap-ha-guide-figure-3010]

   _**Figura 11:** Nenhum disco de quorum é encontrado_

   ![Figura 12: um recurso de cluster principal precisa de um novo endereço IP][sap-ha-guide-figure-3011]

   _**Figura 12:** um recurso de cluster principal precisa de um novo endereço IP_

5. Altere o endereço IP do serviço de cluster principal. O cluster não pode iniciar até você alterar o endereço IP do serviço de cluster principal, pois o endereço IP do servidor aponta para um dos nós da máquina virtual. Faça isso na página **Propriedades** do recurso IP do serviço de cluster principal.

   Por exemplo, precisamos atribuir um endereço IP (em nosso exemplo 10.0.0.42) ao nome de host virtual do cluster pr1-ascs-vir.

   ![Figura 13: Na caixa de diálogo Propriedades, altere o endereço IP][sap-ha-guide-figure-3012]

   _**Figura 13:** Na caixa de diálogo **Propriedades**, altere o endereço IP_

   ![Figura 14: Atribuir o endereço IP reservado para o cluster][sap-ha-guide-figure-3013]

   _**Figura 14:** Atribuir o endereço IP reservado para o cluster_

6. Coloque o nome do host virtual de cluster online.

   ![Figura 15: o serviço principal do cluster está funcionando com o endereço IP correto][sap-ha-guide-figure-3014]

   _**Figura 15:** o serviço principal do cluster está funcionando com o endereço IP correto_

7. Adicione o segundo nó de cluster.

   Agora que o serviço principal do cluster está em execução, você pode adicionar o segundo nó do cluster.

   ![Figura 16: Adicionar o segundo nó de cluster][sap-ha-guide-figure-3015]

   _**Figura 16:** Adicionar o segundo nó de cluster_

8. Insira um nome para o segundo host do nó de cluster.

   ![Figura 17: Inserir o segundo nome de host do nó de cluster][sap-ha-guide-figure-3016]

   _**Figura 17:** Inserir o segundo nome de host do nó de cluster_

   > [!IMPORTANT]
   > Verifique se a caixa de seleção **Adicionar todo o armazenamento qualificado ao cluster** *não* está marcada.  
   >
   >

   ![Figura 18: Não marque a caixa de seleção][sap-ha-guide-figure-3017]

   _**Figura 18:** *Não* marque a caixa de seleção_

   Você pode ignorar os avisos sobre quorum e discos. Você configurará o quorum e compartilhará o disco mais tarde, conforme descrito em [instalar o SIOS DataKeeper Cluster Edition para um disco de compartilhamento de cluster SAP ASCS/SCS][sap-high-availability-infrastructure-wsfc-shared-disk-install-sios].

   ![Figura 19: Ignorar os avisos sobre o quorum de disco][sap-ha-guide-figure-3018]

   _**Figura 19:** Ignorar os avisos sobre o quorum de disco_


#### <a name="e49a4529-50c9-4dcf-bde7-15a0c21d21ca"></a> Configurar a testemunha de compartilhamento de arquivos do cluster

Configurar a testemunha de compartilhamento de arquivos do cluster envolve as seguintes tarefas:

- Criar um compartilhamento de arquivos.
- Definir o quorum de testemunha de compartilhamento de arquivos no Gerenciador de Cluster de Failover.

#### <a name="06260b30-d697-4c4d-b1c9-d22c0bd64855"></a> Criar um compartilhamento de arquivos

1. Selecione uma testemunha de compartilhamento de arquivos em vez de um disco de quorum. O SIOS DataKeeper dá suporte a essa opção.

   Nos exemplos citados neste artigo, a testemunha de compartilhamento de arquivo está no servidor DNS/Active Directory em execução no Azure. A testemunha de compartilhamento de arquivo é chamada domcontr-0. Como uma conexão VPN seria configurada para o Azure (via Gateway de VPN Site a Site ou Azure ExpressRoute), o serviço Active Directory/DNS é local e não é adequado para executar uma testemunha de compartilhamento de arquivos.

   > [!NOTE]
   > Se seu serviço DNS/Active Directory é executado somente no local, não configure a testemunha de compartilhamento de arquivo no sistema operacional Windows do Active Directory/DNS que está sendo executado no local. A latência de rede entre os nós de cluster em execução no Azure e o Active Directory/DNS local pode ser muito grande e causar problemas de conectividade. Não deixe de configurar a testemunha de compartilhamento de arquivo em uma máquina virtual do Azure que está em execução perto do nó do cluster.  
   >
   >

   A unidade de quorum precisa de pelo menos 1.024 MB de espaço livre. É recomendável ter 2.048 MB de espaço livre na unidade de quorum.

2. Adicione o objeto do nome do cluster.

   ![Figura 20: Atribuir as permissões no compartilhamento para o objeto de nome do cluster][sap-ha-guide-figure-3019]

   _**Figura 20:** Atribuir as permissões no compartilhamento para o objeto de nome do cluster_

   Verifique se as permissões incluem o poder de alterar dados no compartilhamento para o objeto de nome de cluster (em nosso exemplo pr1-ascs-vir$).

3. Para adicionar o objeto de nome do cluster à lista, selecione **Adicionar**. Altere o filtro para procurar objetos de computador além daqueles mostrados na Figura 22.

   ![Figura 21: alterar os Tipos de objetos para incluir computadores][sap-ha-guide-figure-3020]

   _**Figura 21:** alterar os **Tipos de objetos** para incluir computadores_

   ![Figura 22: Marcar a caixa de seleção Computadores][sap-ha-guide-figure-3021]

   _**Figura 22:** Marcar a caixa de seleção **Computadores**_

4. Insira o objeto de nome do cluster conforme mostrado na Figura 21. Como o registro já foi criado, você pode alterar as permissões conforme mostrado na Figura 20.

5. Selecione a guia **Segurança** do compartilhamento e defina permissões mais detalhadas para o objeto de nome do cluster.

   ![Figura 23: Definir os atributos de segurança para o objeto de nome do cluster no quórum de compartilhamento de arquivos][sap-ha-guide-figure-3022]

   _**Figura 23:** Definir os atributos de segurança para o objeto de nome do cluster no quórum de compartilhamento de arquivos_

#### <a name="4c08c387-78a0-46b1-9d27-b497b08cac3d"></a> Definir o quorum de testemunha de compartilhamento de arquivos no Gerenciador de Cluster de Failover

1. Abra o Assistente para Definir a Configuração de Quorum.

   ![Figura 24: Iniciar o Assistente de configuração de quorum do cluster][sap-ha-guide-figure-3023]

   _**Figura 24:** Iniciar o Assistente de configuração de quorum do cluster_

2. Na página **Selecionar Configuração de Quorum**, escolha **Selecionar a testemunha de quorum**.

   ![Figura 25: Configurações de quorum à sua escolha][sap-ha-guide-figure-3024]

   _**Figura 25:** Configurações de quorum à sua escolha_

3. Na página **Selecionar Testemunha de Quorum**, escolha **Configurar uma testemunha de compartilhamento de arquivos**.

   ![Figura 26: Selecionar a testemunha de compartilhamento de arquivo][sap-ha-guide-figure-3025]

   _**Figura 26:** Selecionar a testemunha de compartilhamento de arquivo_

4. Digite o caminho UNC para o compartilhamento de arquivos (em nosso exemplo, \\domcontr-0\FSW). Para ver uma lista das alterações que você pode fazer, selecione **Avançar**.

   ![Figura 27: Definir o local do compartilhamento de arquivos para a testemunha de compartilhamento][sap-ha-guide-figure-3026]

   _**Figura 27:** Definir o local do compartilhamento de arquivos para a testemunha de compartilhamento_

5. Selecione as alterações que você deseja e selecione **Avançar**. Você precisa redefinir com êxito a configuração do cluster, conforme mostrado na Figura 28:  

   ![Figura 28: Confirmação de que você reconfigurou o cluster][sap-ha-guide-figure-3027]

   _**Figura 28:** Confirmação de que você reconfigurou o cluster_

Depois de instalar com êxito o Cluster de Failover do Windows, é necessário alterar alguns limites para adaptar a detecção de failover às condições no Azure. Os parâmetros a serem alterados estão documentados em [ajuste os limites de rede de cluster de failover][tuning-failover-cluster-network-thresholds]. Supondo que as duas VMs que criam a configuração de Cluster do Windows para ASCS/SCS estejam na mesma sub-rede, os parâmetros a seguir precisam ser alterados para estes valores:

- SameSubNetDelay = 2000
- SameSubNetThreshold = 15
- RoutingHistoryLength = 30

Essas configurações foram testadas com clientes e oferecem um boa comprometimento. Elas são flexíveis o suficiente, mas eles também fornecem failover rápido em condições de erro real em um software SAP ou em um nó ou uma falha VM.

### <a name="5c8e5482-841e-45e1-a89d-a05c0907c868"></a> Instalar SIOS DataKeeper Cluster Edition para disco de compartilhamento de cluster do SAP ASCS/SCS

Agora você tem uma configuração do Clustering de Failover do Windows Server em funcionamento no Azure. Mas para instalar uma instância do SAP ASCS/SCS, você precisa de um recurso de disco compartilhado. Não é possível criar os recursos de disco compartilhado que você precisa no Azure. O SIOS DataKeeper Cluster Edition é uma solução de terceiros que você pode usar para criar recursos de disco compartilhados.

Instalar o SIOS DataKeeper Cluster Edition para o disco de compartilhamento de cluster do SAP ASCS/SCS envolve as seguintes tarefas:

- Microsoft .NET Framework 3.5.
- Instalar SIOS DataKeeper.
- Configurar SIOS DataKeeper.

### <a name="1c2788c3-3648-4e82-9e0d-e058e475e2a3"></a> Adicionar .NET framework 3.5, 3.5.1 e 3.5
O Microsoft .NET Framework 3.5 não é automaticamente ativado ou instalado no Windows Server 2012 R2. Como SIOS DataKeeper requer que o .NET Framework esteja em todos os nós nos quais o DataKeeper for instalado, será necessário instalar o .NET Framework 3.5 no sistema operacional convidado de todas as máquinas virtuais no cluster.

Há duas maneiras de adicionar o .Net Framework 3.5:

- Uma delas é usar o Assistente para Adicionar Funções e Recursos no Windows, como mostrado na Figura 29:

  ![Figura 29: instalar o .NET Framework 3.5 usando o Assistente para adicionar funções e recursos][sap-ha-guide-figure-3028]

  _**Figura 29:** instalar o .NET Framework 3.5 usando o Assistente para adicionar funções e recursos_

  ![Figura 30: barra de progresso da instalação no momento em que você instala o .NET Framework 3.5 usando o Assistente para adicionar funções e recursos][sap-ha-guide-figure-3029]

  _**Figura 30:** barra de progresso da instalação no momento em que você instala o .NET Framework 3.5 usando o Assistente para adicionar funções e recursos_

- Use a ferramenta de linha de comando dism.exe. Para esse tipo de instalação, você precisa acessar o diretório SxS na mídia de instalação do Windows. Execute este comando em um prompt de comando elevado:

  ```
  Dism /online /enable-feature /featurename:NetFx3 /All /Source:installation_media_drive:\sources\sxs /LimitAccess
  ```

### <a name="dd41d5a2-8083-415b-9878-839652812102"></a> Instalar SIOS DataKeeper

Instale o SIOS DataKeeper Cluster Edition em cada nó no cluster. Para criar um armazenamento compartilhado virtual com o SIOS DataKeeper, crie um espelho sincronizado e simule o armazenamento compartilhado do cluster.

Antes de instalar o software SIOS, crie o usuário de domínio DataKeeperSvc.

> [!NOTE]
> Adicione esse usuário DataKeeperSvc ao grupo Administradores locais em ambos os nós do cluster.
>
>

Para instalar SIOS DataKeeper:

1. Instale o software SIOS em ambos os nós do cluster.

   ![Instalador do SIOS][sap-ha-guide-figure-3030]

   ![Figura 31: Primeira página da instalação do SIOS DataKeeper][sap-ha-guide-figure-3031]

   _**Figura 31:** Primeira página da instalação do SIOS DataKeeper_

2. Na caixa de diálogo, selecione **Sim**.

   ![Figura 32: O DataKeeper informa a você que um serviço será desabilitado][sap-ha-guide-figure-3032]

   _**Figura 32:** O DataKeeper informa a você que um serviço será desabilitado_

3. Na caixa de diálogo, recomendamos que você selecione **Conta de domínio ou servidor**.

   ![Figura 33: Seleção do usuário para o SIOS DataKeeper][sap-ha-guide-figure-3033]

   _**Figura 33:** Seleção do usuário para o SIOS DataKeeper_

4. Insira o nome de usuário da conta de domínio e senhas que você criou para o SIOS DataKeeper.

   ![Figura 34: Inserir o nome de usuário de domínio e a senha para a instalação do SIOS DataKeeper][sap-ha-guide-figure-3034]

   _**Figura 34:** Inserir o nome de usuário de domínio e a senha para a instalação do SIOS DataKeeper_

5. Instale a chave de licença para o SIOS DataKeeper conforme mostrado na Figura 35.

   ![Figura 35: Insira a chave da licença do SIOS DataKeeper][sap-ha-guide-figure-3035]

   _**Figura 35:** Insira a chave da licença do SIOS DataKeeper_

6. Quando solicitado, reinicie a máquina virtual.

### <a name="d9c1fc8e-8710-4dff-bec2-1f535db7b006"></a> Configurar SIOS DataKeeper

Depois de instalar o SIOS DataKeeper em ambos os nós, você precisará iniciar a configuração. O objetivo da configuração é ter a replicação de dados síncrona entre os discos adicionais anexados a cada uma das máquinas virtuais.

1. Inicie a ferramenta de Configuração e Gerenciamento de DataKeeper e selecione **Conectar Servidor**.

   ![Figura 36: Ferramenta de Gerenciamento e Configuração do SIOS DataKeeper][sap-ha-guide-figure-3036]

   _**Figura 36:** Ferramenta de Gerenciamento e Configuração do SIOS DataKeeper_

2. Insira o nome ou endereço TCP/IP do primeiro nó ao qual a ferramenta de Gerenciamento e Configuração deve se conectar e, em uma segunda etapa, as informações do segundo nó.

   ![Figura 37: Inserir o nome ou endereço TCP/IP do primeiro nó ao qual a ferramenta de Gerenciamento e Configuração deve se conectar e, em uma segunda etapa, as informações do segundo nó][sap-ha-guide-figure-3037]

   _**Figura 37:** Inserir o nome ou endereço TCP/IP do primeiro nó ao qual a ferramenta de Gerenciamento e Configuração deve se conectar e, em uma segunda etapa, as informações do segundo nó_

3. Crie o trabalho de replicação entre os dois nós.

   ![Figura 38: Criar trabalho de replicação][sap-ha-guide-figure-3038]

   _**Figura 38:** Criar trabalho de replicação_

   Um assistente orienta você pelo processo de criação de um trabalho de replicação.

4. Definir o nome do trabalho de replicação.

   ![Figura 39: Definir o nome do trabalho de replicação][sap-ha-guide-figure-3039]

   _**Figura 39:** Definir o nome do trabalho de replicação_

   ![Figura 40: Definir os dados de base para o nó, que deve ser o nó de origem atual][sap-ha-guide-figure-3040]

   _**Figura 40:** Definir os dados de base para o nó, que deve ser o nó de origem atual_

5. Defina o nome, o endereço TCP/IP e o volume de disco do nó de destino.

   ![Figura 41: definir o nome, o endereço TCP/IP e o volume de disco do nó de destino][sap-ha-guide-figure-3041]

   _**Figura 41:** definir o nome, o endereço TCP/IP e o volume de disco do nó de destino_

6. Defina os algoritmos de compactação. Em nosso exemplo, recomendamos que você compacte o fluxo de replicação. Particularmente em situações de ressincronização, a compactação do fluxo de replicação reduz consideravelmente o tempo de ressincronização. A compactação utiliza os recursos de CPU e RAM de uma máquina virtual. Conforme aumenta a taxa de compactação, aumenta o volume de recursos de CPU usados. Você pode ajustar essa configuração mais tarde.

7. Outra configuração que precisa ser verificada é se a replicação é executada de modo síncrono ou assíncrono. Quando você protege configurações SAP ASCS/SCS, deve usar a replicação síncrona.  

   ![Figura 42: Definir detalhes de replicação][sap-ha-guide-figure-3042]

   _**Figura 42:** Definir detalhes de replicação_

8. Defina se o volume que é replicado pelo trabalho de replicação deve ser representado em uma configuração de cluster Clustering de Failover do Windows Server como um disco compartilhado. Para a configuração do SAP ASCS/SCS, escolha **Sim** para que o cluster do Windows veja o volume replicado como disco compartilhado que pode ser usado como volume de cluster.

   ![Figura 43: Selecionar Sim para definir o volume replicado como um volume de cluster][sap-ha-guide-figure-3043]

   _**Figura 43:** Selecionar **Sim** para definir o volume replicado como um volume de cluster_

   Depois que o volume é criado, a ferramenta de Configuração e Gerenciamento do DataKeeper mostra que o trabalho de replicação está ativo.

   ![Figura 44: O espelhamento síncrono do DataKeeper para o disco de compartilhamento do SAP ASCS/SCS está ativo][sap-ha-guide-figure-3044]

   _**Figura 44:** O espelhamento síncrono do DataKeeper para o disco de compartilhamento do SAP ASCS/SCS está ativo_

   O Gerenciador de Cluster de Failover agora mostra o disco como um disco do DataKeeper, conforme ilustrado na Figura 45:

   ![Figura 45: O Gerenciador de Cluster de Failover mostra o disco do DataKeeper replicado][sap-ha-guide-figure-3045]

   _**Figura 45:** O Gerenciador de Cluster de Failover mostra o disco do DataKeeper replicado_

## <a name="next-steps"></a>Próximas etapas

* [Instalar o SAP NetWeaver HA usando um cluster de failover do Windows e disco compartilhado para uma instância do SAP ASCS/SCS][sap-high-availability-installation-wsfc-shared-disk]
