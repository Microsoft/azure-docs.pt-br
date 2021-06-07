---
title: Infraestrutura do Azure para SAP ASCS/SCS com WSFC&disco compartilhado | Microsoft Docs
description: Aprenda a preparação da infraestrutura do Azure para alta disponibilidade do SAP usando o cluster de failover do Windows e o disco compartilhado para a instância SAP ASCS/SCS.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ec976257-396b-42a0-8ea1-01c97f820fa6
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/16/2020
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4218b4c00b79d78965eaf6e73028e63f52b1ff17
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101673618"
---
# <a name="prepare-the-azure-infrastructure-for-sap-ha-by-using-a-windows-failover-cluster-and-shared-disk-for-sap-ascsscs"></a>Prepare a infraestrutura do Azure para alta disponibilidade do SAP usando o cluster de failover do Windows e o disco compartilhado para a instância SAP ASCS/SCS

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides
[tuning-failover-cluster-network-thresholds]:https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834

[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md

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

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md


> ![Sistema operacional Windows][Logo_Windows] Windows


Este artigo descreve as etapas necessárias para preparar a infraestrutura do Azure para instalar e configurar uma instância do SAP ASCS/SCS de alta disponibilidade em um cluster de failover do Windows usando um *disco compartilhado de cluster* como uma opção para clustering de uma instância do SAP ASCS.
Duas alternativas para o *disco compartilhado do cluster* são apresentadas na documentação:

- [Discos compartilhados do Azure](../../disks-shared.md)
- Usando o [sios Datakeeper Cluster Edition](https://us.sios.com/products/datakeeper-cluster/) para criar um armazenamento espelhado, que simulará o disco compartilhado clusterizado 

A configuração apresentada está contando com os [PPG (grupos de posicionamento de proximidade do Azure)](./sap-proximity-placement-scenarios.md) para obter a latência de rede ideal para cargas de trabalho do SAP. A documentação não cobre a camada de banco de dados.  

> [!NOTE]
> Os grupos de posicionamento de proximidade do Azure são pré-requisitos para usar o disco compartilhado do Azure.
 

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar a instalação, analise este artigo:

* [Clustering de uma instância do SAP ASCS/SCS em um cluster de failover do Windows usando o disco compartilhado de cluster no Azure][sap-high-availability-guide-wsfc-shared-disk]

## <a name="create-the-ascs-vms"></a>Criar as VMs ASCS

Para o cluster do SAP ASCS/SCS, implante duas VMs no conjunto de disponibilidade do Azure. Implante as VMs no mesmo grupo de posicionamento de proximidade. Depois que as VMs forem implantadas:  
- Criar Load Balancer interno do Azure para a instância do SAP ASCS/SCS 
- Adicionar VMs do Windows ao domínio do AD

Os nomes de host e os endereços IP para o cenário apresentado são:

| Função de nome de host | Nome do host | Endereço IP estático | Conjunto de disponibilidade | Grupo de posicionamento de proximidade |
| --- | --- | --- |---| ---|
| primeiro cluster do ASCS/SCS do nó do cluster |PR1-ASCs-10 |10.0.0.4 |PR1-ASCs-avset |PR1PPG |
| segundo cluster do nó do cluster ASCS/SCS |PR1-ASCs-11 |10.0.0.5 |PR1-ASCs-avset |PR1PPG |
| Nome da rede de clusters | pr1clust |10.0.0.42 (**somente** para cluster do Win 2016) | N/D | N/D |
| Nome da rede do cluster ASCS | PR1-ascscl |10.0.0.43 | N/D | N/D |
| Nome de rede de cluster ERS (**somente** para ERS2) | PR1-erscl |10.0.0.44 | N/D | N/D |


## <a name="create-azure-internal-load-balancer"></a><a name="fe0bd8b5-2b43-45e3-8295-80bee5415716"></a> Criar um balanceador de carga interno do Azure

SAP ASCS, SAP SCS e o novo SAP ERS2, use o nome de host virtual e endereços IP virtuais. No Azure, é necessário um [balanceador de carga](../../../load-balancer/load-balancer-overview.md) para usar um endereço IP virtual. É altamente recomendável usar o [balanceador de carga padrão](../../../load-balancer/quickstart-load-balancer-standard-public-portal.md). 

> [!IMPORTANT]
> Não há suporte para IP flutuante em uma configuração de IP secundário de NIC em cenários de balanceamento de carga. Para obter detalhes, consulte [limitações do Azure Load Balancer](../../../load-balancer/load-balancer-multivip-overview.md#limitations). Se você precisar de um endereço IP adicional para a VM, implante uma segunda NIC.    


A lista a seguir mostra a configuração do balanceador de carga (A) SCS/ERS. A configuração para SAP ASCS e ERS2 no executada no mesmo balanceador de carga do Azure.  

**(A)SCS**
- Configuração de front-end
    - Endereço IP estático ASCS/SCS **10.0.0.43**
- Configuração de back-end  
    Adicione todas as máquinas virtuais que devem ser parte do cluster (A) SCS/ERS. Neste exemplo, VMs **PR1-ASCs-10** e **PR1-ASCs-11**.
- Porta de Investigação
    - Porta 620 **NR** deixe a opção padrão para protocolo (TCP), intervalo (5), limite não íntegro (2)
- Regras de balanceamento de carga
    - Se estiver usando o Standard Load Balancer, selecione portas de HA
    - Se estiver usando o Load Balancer Básico, crie regras de balanceamento de carga para as portas a seguir
        - 32 **nr** TCP
        - 36 **nr** TCP
        - 39 **nr** TCP
        - 81 **nr** TCP
        - 5 **nr** 13 TCP
        - 5 **nr** 14 TCP
        - 5 **nr** 16 TCP

    - Verifique se o tempo limite de ociosidade (minutos) está definido para o valor máximo 30 e se o IP flutuante (retorno de servidor direto) está habilitado.

**ERS2**

Como o enqueue Replication Server 2 (ERS2) também é clusterizado, o endereço IP virtual do ERS2 também deve ser configurado no ILB do Azure, além do IP do SAP ASCS/SCS acima. Esta seção só se aplica se você estiver usando a arquitetura enqueue Replication Server 2.  
- segunda configuração de front-end
    - Endereço IP estático do SAP ERS2 **10.0.0.44**

- Configuração de back-end  
  As VMs já foram adicionadas ao pool de back-end ILB.  

- 2ª porta de investigação
    - Porta 621 **nr**  
    Deixe a opção padrão para protocolo (TCP), intervalo (5), limite não íntegro (2)

- segundo regras de balanceamento de carga
    - Se estiver usando o Standard Load Balancer, selecione portas de HA
    - Se estiver usando o Load Balancer Básico, crie regras de balanceamento de carga para as portas a seguir
        - 32 **nr** TCP
        - 33 **nr** TCP
        - 5 **nr** 13 TCP
        - 5 **nr** 14 TCP
        - 5 **nr** 16 TCP

    - Verifique se o tempo limite de ociosidade (minutos) está definido para o valor máximo 30 e se o IP flutuante (retorno de servidor direto) está habilitado.


> [!TIP]
> Com o [modelo de Azure Resource Manager para o WSFC para a instância do SAP ASCS/SCS com o disco compartilhado do Azure](https://github.com/robotechredmond/301-shared-disk-sap), você pode automatizar a preparação da infraestrutura, usando o disco compartilhado do Azure para um SID do SAP com ERS1.  
> O modelo do ARM do Azure criará duas VMs do Windows 2019 ou 2016, criará um disco compartilhado do Azure e anexará às VMs. Os Load Balancer internos do Azure também serão criados e configurados. Para obter detalhes-consulte o modelo ARM. 

## <a name="add-registry-entries-on-both-cluster-nodes-of-the-ascsscs-instance"></a><a name="661035b2-4d0f-4d31-86f8-dc0a50d78158"></a> Adicionar entradas do registro em ambos os nós de cluster da instância do ASCS/SCS

Azure Load Balancer pode fechar conexões, se as conexões estiverem ociosas por um período e excederem o tempo limite de ociosidade. Os processos de trabalho do SAP abrem conexões com o processo de enfileiramento da SAP assim que a primeira solicitação de enfileiramento/retirada da fila precisa ser enviada. Para evitar a interrupção dessas conexões, altere os valores de Keeptime TCP/IP e KeepAliveInterval em ambos os nós de cluster. Se estiver usando ERS1, também será necessário adicionar parâmetros de perfil do SAP, conforme descrito posteriormente neste artigo.
As seguintes entradas de registro devem ser alteradas em ambos os nós de cluster:

- KeepAlivetime
- KeepAliveInterval

| Caminho| Nome da variável | Tipo de variável  | Valor | Documentação |
| --- | --- | --- |---| ---|
| HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |KeepAlivetime |REG_DWORD (Decimal) |120000 |[KeepAlivetime](/previous-versions/windows/it-pro/windows-2000-server/cc957549(v=technet.10)) |
| HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |KeepAliveInterval |REG_DWORD (Decimal) |120000 |[KeepAliveInterval](/previous-versions/windows/it-pro/windows-2000-server/cc957548(v=technet.10)) |


Para aplicar as alterações, reinicie os dois nós do cluster.
  
## <a name="add-the-windows-vms-to-the-domain"></a><a name="e69e9a34-4601-47a3-a41c-d2e11c626c0c"></a> Adicionar as VMs do Windows ao domínio
Depois de atribuir endereços IP estáticos às máquinas virtuais, adicione as máquinas virtuais ao domínio. 

## <a name="install-and-configure--windows-failover-cluster"></a><a name="0d67f090-7928-43e0-8772-5ccbf8f59aab"></a> Instalar e configurar o cluster de failover do Windows 

### <a name="install-the-windows-failover-cluster-feature"></a>Instalar o recurso de cluster de failover do Windows

Execute este comando em um dos nós do cluster:

   ```powershell
    # Hostnames of the Win cluster for SAP ASCS/SCS
    $SAPSID = "PR1"
    $ClusterNodes = ("pr1-ascs-10","pr1-ascs-11")
    $ClusterName = $SAPSID.ToLower() + "clust"
    
    # Install Windows features.
    # After the feature installs, manually reboot both nodes
    Invoke-Command $ClusterNodes {Install-WindowsFeature Failover-Clustering, FS-FileServer -IncludeAllSubFeature -IncludeManagementTools }
   ```

Depois que a instalação do recurso for concluída, reinicialize os dois nós do cluster.  

### <a name="test-and-configure-windows-failover-cluster"></a>Testar e configurar o cluster de failover do Windows 

No Windows 2019, o cluster reconhecerá automaticamente que ele está em execução no Azure, e como uma opção padrão para o IP de gerenciamento de cluster, ele usará o nome de rede distribuída. Portanto, ele usará qualquer um dos nós de cluster endereços IP locais. Como resultado, não há necessidade de um nome de rede dedicado (virtual) para o cluster e não há necessidade de configurar esse endereço IP no Load Balancer interno do Azure.

Para obter mais informações, consulte os [novos recursos do clustering de failover do Windows Server 2019](https://techcommunity.microsoft.com/t5/failover-clustering/windows-server-2019-failover-clustering-new-features/ba-p/544029) execute este comando em um dos nós do cluster:

   ```powershell
    # Hostnames of the Win cluster for SAP ASCS/SCS
    $SAPSID = "PR1"
    $ClusterNodes = ("pr1-ascs-10","pr1-ascs-11")
    $ClusterName = $SAPSID.ToLower() + "clust"
    
    # IP adress for cluster network name is needed ONLY on Windows Server 2016 cluster
    $ClusterStaticIPAddress = "10.0.0.42"
        
    # Test cluster
    Test-Cluster –Node $ClusterNodes -Verbose
    
    $ComputerInfo = Get-ComputerInfo
    
    $WindowsVersion = $ComputerInfo.WindowsProductName
    
    if($WindowsVersion -eq "Windows Server 2019 Datacenter"){
        write-host "Configuring Windows Failover Cluster on Windows Server 2019 Datacenter..."
        New-Cluster –Name $ClusterName –Node  $ClusterNodes -Verbose
    }elseif($WindowsVersion -eq "Windows Server 2016 Datacenter"){
        write-host "Configuring Windows Failover Cluster on Windows Server 2016 Datacenter..."
        New-Cluster –Name $ClusterName –Node  $ClusterNodes –StaticAddress $ClusterStaticIPAddress -Verbose 
    }else{
        Write-Error "Not supported Windows version!"
    }
   ```

### <a name="configure-cluster-cloud-quorum"></a>Configurar quorum de nuvem de cluster
Ao usar o Windows Server 2016 ou 2019, recomendamos configurar a [testemunha de nuvem do Azure](/windows-server/failover-clustering/deploy-cloud-witness)como quorum de cluster.

Execute este comando em um dos nós do cluster:

   ```powershell
    $AzureStorageAccountName = "cloudquorumwitness"
    Set-ClusterQuorum –CloudWitness –AccountName $AzureStorageAccountName -AccessKey <YourAzureStorageAccessKey> -Verbose
   ```

### <a name="tuning-the-windows-failover-cluster-thresholds"></a>Ajustando os limites do cluster de failover do Windows
 
Depois de instalar com êxito o cluster de failover do Windows, você precisa ajustar alguns limites para ser adequado para clusters implantados no Azure. Os parâmetros a serem alterados estão documentados no blog [Ajustar os limites de rede de cluster de failover](https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834). Supondo que as duas VMs que criam a configuração de Cluster do Windows para ASCS/SCS estejam na mesma sub-rede, os parâmetros a seguir precisam ser alterados para estes valores:
- SameSubNetDelay = 2000
- SameSubNetThreshold = 15
- RoutingHistoryLength = 30

Essas configurações foram testadas com os clientes e oferecem um bom comprometimento. Elas são resistentes o suficiente, mas também fornecem failover rápido o suficiente para condições de erro reais em cargas de trabalho do SAP ou falha da VM.  

## <a name="configure-azure-shared-disk"></a>Configurar o disco compartilhado do Azure
Esta seção só será aplicável se você estiver usando o disco compartilhado do Azure. 

### <a name="create-and-attach-azure-shared-disk-with-powershell"></a>Criar e anexar o disco compartilhado do Azure com o PowerShell
Execute este comando em um dos nós de cluster. Você precisará ajustar os valores para seu grupo de recursos, região do Azure, SAPSid e assim por diante.  

   ```powershell
    #############################
    # Create Azure Shared Disk
    #############################
    
    $ResourceGroupName = "MyResourceGroup"
    $location = "MyAzureRegion"
    $SAPSID = "PR1"
    
    $DiskSizeInGB = 512
    $DiskName = "$($SAPSID)ASCSSharedDisk"
    
    # With parameter '-MaxSharesCount', we define the maximum number of cluster nodes to attach the shared disk
    $NumberOfWindowsClusterNodes = 2
            
    $diskConfig = New-AzDiskConfig -Location $location -SkuName Premium_LRS  -CreateOption Empty  -DiskSizeGB $DiskSizeInGB -MaxSharesCount $NumberOfWindowsClusterNodes
    $dataDisk = New-AzDisk -ResourceGroupName $ResourceGroupName -DiskName $DiskName -Disk $diskConfig
    
    ##################################
    ## Attach the disk to cluster VMs
    ##################################
    # ASCS Cluster VM1
    $ASCSClusterVM1 = "$SAPSID-ascs-10"
    
    # ASCS Cluster VM2
    $ASCSClusterVM2 = "$SAPSID-ascs-11"
    
    # Add the Azure Shared Disk to Cluster Node 1
    $vm = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $ASCSClusterVM1 
    $vm = Add-AzVMDataDisk -VM $vm -Name $DiskName -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun 0
    Update-AzVm -VM $vm -ResourceGroupName $ResourceGroupName -Verbose
    
    # Add the Azure Shared Disk to Cluster Node 2
    $vm = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $ASCSClusterVM2
    $vm = Add-AzVMDataDisk -VM $vm -Name $DiskName -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun 0
    Update-AzVm -VM $vm -ResourceGroupName $ResourceGroupName -Verbose
   ```

### <a name="format-the-shared-disk-with-powershell"></a>Formatar o disco compartilhado com o PowerShell
1. Obter o número do disco. Execute estes comandos do PowerShell em um dos nós do cluster:

   ```powershell
    Get-Disk | Where-Object PartitionStyle -Eq "RAW"  | Format-Table -AutoSize 
    # Example output
    # Number Friendly Name     Serial Number HealthStatus OperationalStatus Total Size Partition Style
    # ------ -------------     ------------- ------------ ----------------- ---------- ---------------
    # 2      Msft Virtual Disk               Healthy      Online                512 GB RAW            

   ```
2. Formate o disco. Neste exemplo, é o número de disco 2. 

   ```powershell
    # Format SAP ASCS Disk number '2', with drive letter 'S'
    $SAPSID = "PR1"
    $DiskNumber = 2
    $DriveLetter = "S"
    $DiskLabel = "$SAPSID" + "SAP"
    
    Get-Disk -Number $DiskNumber | Where-Object PartitionStyle -Eq "RAW" | Initialize-Disk -PartitionStyle GPT -PassThru |  New-Partition -DriveLetter $DriveLetter -UseMaximumSize | Format-Volume  -FileSystem ReFS -NewFileSystemLabel $DiskLabel -Force -Verbose
    # Example outout
    # DriveLetter FileSystemLabel FileSystem DriveType HealthStatus OperationalStatus SizeRemaining      Size
    # ----------- --------------- ---------- --------- ------------ ----------------- -------------      ----
    # S           PR1SAP          ReFS       Fixed     Healthy      OK                    504.98 GB 511.81 GB
   ```

3. Verifique se o disco agora está visível como um disco de cluster.  
   ```powershell
    # List all disks
    Get-ClusterAvailableDisk -All
    # Example output
    # Cluster    : pr1clust
    # Id         : 88ff1d94-0cf1-4c70-89ae-cbbb2826a484
    # Name       : Cluster Disk 1
    # Number     : 2
    # Size       : 549755813888
    # Partitions : {\\?\GLOBALROOT\Device\Harddisk2\Partition2\}
   ```
4. Registre o disco no cluster.  
   ```powershell
    # Add the disk to cluster 
    Get-ClusterAvailableDisk -All | Add-ClusterDisk
    # Example output     
    # Name           State  OwnerGroup        ResourceType 
    # ----           -----  ----------        ------------ 
    # Cluster Disk 1 Online Available Storage Physical Disk
   ```

## <a name="sios-datakeeper-cluster-edition-for-the-sap-ascsscs-cluster-share-disk"></a><a name="5c8e5482-841e-45e1-a89d-a05c0907c868"></a> SIOS datakeeper Cluster Edition para o disco de compartilhamento de cluster do SAP ASCS/SCS
Esta seção só será aplicável se você estiver usando o software de terceiros SIOS datakeeper Cluster Edition para criar um armazenamento espelhado que simula o disco compartilhado do cluster.  

Agora, você tem uma configuração de clustering de failover do Windows Server em funcionamento no Azure. Mas para instalar uma instância do SAP ASCS/SCS, você precisa de um recurso de disco compartilhado. Uma das opções é usar o SIOS datakeeper Cluster Edition é uma solução de terceiros que você pode usar para criar recursos de disco compartilhados.  

Instalar o SIOS DataKeeper Cluster Edition para o disco de compartilhamento de cluster do SAP ASCS/SCS envolve as seguintes tarefas:
- Adicione Microsoft .NET Framework, se necessário. Consulte a [documentação da SIOS] (( https://us.sios.com/products/datakeeper-cluster/) para obter os requisitos mais atualizados do .NET Framework 
-  Instalar SIOS DataKeeper
- Configurar o SIOS datakeeper

### <a name="install-sios-datakeeper"></a> Instalar SIOS DataKeeper
Instale o SIOS DataKeeper Cluster Edition em cada nó no cluster. Para criar um armazenamento compartilhado virtual com o SIOS DataKeeper, crie um espelho sincronizado e simule o armazenamento compartilhado do cluster.

Antes de instalar o software SIOS, crie o usuário de domínio DataKeeperSvc.

> [!NOTE]
> Adicione esse usuário DataKeeperSvc ao grupo Administradores locais em ambos os nós do cluster.
>

1. Instale o software SIOS em ambos os nós do cluster.

   ![Instalador do SIOS][sap-ha-guide-figure-3030]

   ![Figura 31: Primeira página da instalação do SIOS DataKeeper][sap-ha-guide-figure-3031]

   _Primeira página da instalação do SIOS datakeeper_

2. Na caixa de diálogo, selecione **Sim**.

   ![Figura 32: O DataKeeper informa a você que um serviço será desabilitado][sap-ha-guide-figure-3032]

   _O datakeeper informa que um serviço será desabilitado_

3. Na caixa de diálogo, recomendamos que você selecione **Conta de domínio ou servidor**.

   ![Figura 33: Seleção do usuário para o SIOS DataKeeper][sap-ha-guide-figure-3033]

   _Seleção do usuário para o SIOS DataKeeper_

4. Insira o nome de usuário da conta de domínio e senhas que você criou para o SIOS DataKeeper.

   ![Figura 34: Inserir o nome de usuário de domínio e a senha para a instalação do SIOS DataKeeper][sap-ha-guide-figure-3034]

   _Inserir o nome de usuário de domínio e a senha para a instalação do SIOS DataKeeper_

5. Instale a chave de licença para o SIOS DataKeeper conforme mostrado na Figura 35.

   ![Figura 35: insira a chave da licença do SIOS DataKeeper][sap-ha-guide-figure-3035]

   _Insira sua chave de licença do SIOS datakeeper_

6. Quando solicitado, reinicie a máquina virtual.

### <a name="configure-sios-datakeeper"></a>Configurar o SIOS datakeeper
Depois de instalar o SIOS DataKeeper em ambos os nós, você precisará iniciar a configuração. O objetivo da configuração é ter a replicação de dados síncrona entre os discos adicionais anexados a cada uma das máquinas virtuais.

1. Inicie a ferramenta de Configuração e Gerenciamento de DataKeeper e selecione **Conectar Servidor**.

   ![Figura 36: Ferramenta de Gerenciamento e Configuração do SIOS DataKeeper][sap-ha-guide-figure-3036]

   _Ferramenta de Gerenciamento e Configuração do SIOS DataKeeper_

2. Insira o nome ou endereço TCP/IP do primeiro nó ao qual a ferramenta de Gerenciamento e Configuração deve se conectar e, em uma segunda etapa, as informações do segundo nó.

   ![Figura 37: inserir o nome ou endereço TCP/IP do primeiro nó ao qual a ferramenta de gerenciamento deve se conectar e, em uma segunda etapa, o segundo nó][sap-ha-guide-figure-3037]

   _Insira o nome ou endereço TCP/IP do primeiro nó ao qual a ferramenta de gerenciamento e configuração deve se conectar e, em uma segunda etapa, o segundo nó_

3. Crie o trabalho de replicação entre os dois nós.

   ![Figura 38: Criar trabalho de replicação][sap-ha-guide-figure-3038]

   _Criar um trabalho de replicação_

   Um assistente orienta você pelo processo de criação de um trabalho de replicação.

4. Definir o nome do trabalho de replicação.

   ![Figura 39: Definir o nome do trabalho de replicação][sap-ha-guide-figure-3039]

   _Definir o nome do trabalho de replicação_

   ![Figura 40: Definir os dados de base do nó que deve ser o nó de origem atual][sap-ha-guide-figure-3040]

   _Definir os dados base para o nó, que deve ser o nó de origem atual_

5. Defina o nome, o endereço TCP/IP e o volume de disco do nó de destino.

   ![Figura 41: Defina o nome, o endereço TCP/IP e o volume de disco do nó de destino][sap-ha-guide-figure-3041]

   _definir o nome, o endereço TCP/IP e o volume de disco do nó de destino_

6. Defina os algoritmos de compactação. Em nosso exemplo, recomendamos que você compacte o fluxo de replicação. Particularmente em situações de ressincronização, a compactação do fluxo de replicação reduz consideravelmente o tempo de ressincronização. A compactação utiliza os recursos de CPU e RAM de uma máquina virtual. Conforme aumenta a taxa de compactação, aumenta o volume de recursos de CPU usados. Você pode ajustar essa configuração mais tarde.

7. Outra configuração que precisa ser verificada é se a replicação é executada de modo síncrono ou assíncrono. Quando você protege configurações SAP ASCS/SCS, deve usar a replicação síncrona.  

   ![Figura 42: Definir detalhes de replicação][sap-ha-guide-figure-3042]

   _Definir detalhes de replicação_

8. Defina se o volume que é replicado pelo trabalho de replicação deve ser representado em uma configuração de cluster Clustering de Failover do Windows Server como um disco compartilhado. Para a configuração do SAP ASCS/SCS, escolha **Sim** para que o cluster do Windows veja o volume replicado como disco compartilhado que pode ser usado como volume de cluster.

   ![Figura 43: Selecionar Sim para definir o volume replicado como um volume de cluster][sap-ha-guide-figure-3043]

   _Selecione **Sim** para definir o volume replicado como um volume de cluster_

   Depois que o volume é criado, a ferramenta de Configuração e Gerenciamento do DataKeeper mostra que o trabalho de replicação está ativo.

   ![Figura 44: O espelhamento síncrono do DataKeeper para o disco de compartilhamento do SAP ASCS/SCS está ativo][sap-ha-guide-figure-3044]

   _O espelhamento síncrono do DataKeeper para o disco de compartilhamento do SAP ASCS/SCS está ativo_

   O Gerenciador de Cluster de Failover agora mostra o disco como um disco do DataKeeper, conforme ilustrado na Figura 45:

   ![Figura 45: O Gerenciador de Cluster de Failover mostra o disco do DataKeeper replicado][sap-ha-guide-figure-3045]

   _O Gerenciador de Cluster de Failover mostra o disco do DataKeeper replicado_


## <a name="next-steps"></a>Próximas etapas

* [Instalação de alta disponibilidade do SAP NetWeaver no cluster de failover do Windows e compartilhamento de arquivos para a instância do SAP ASCS/SCS][sap-high-availability-installation-wsfc-shared-disk]