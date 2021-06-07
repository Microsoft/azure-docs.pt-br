---
title: Instalar a alta disponibilidade do SAP NetWeaver em um cluster de failover do Windows e em um disco compartilhado para uma instância do SAP ASCS/SCS no Azure | Microsoft Docs
description: Saiba como instalar a alta disponibilidade do SAP NetWeaver em um cluster de failover do Windows e em um disco compartilhado para uma instância do SAP ASCS/SCS.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 6209bcb3-5b20-4845-aa10-1475c576659f
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/12/2020
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 12cfd39cf1358dcef79c3843627f0b45dc688c9e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101667937"
---
# <a name="install-sap-netweaver-ha-on-a-windows-failover-cluster-and-shared-disk-for-an-sap-ascsscs-instance-in-azure"></a>Instalar a alta disponibilidade do SAP NetWeaver em um cluster de failover do Windows e em um disco compartilhado para uma instância do SAP ASCS/SCS no Azure

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides

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
[sap-ha-guide-8.9]:high-availability-guide.md#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-ha-guide-8.11]:high-availability-guide.md#661035b2-4d0f-4d31-86f8-dc0a50d78158
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md

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
[sap-ha-guide-figure-3046-ers2]:./media/virtual-machines-shared-sap-high-availability-guide/3046-dns-entry-sap-ers2-virtual-name-ip.png
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

Este artigo descreve como instalar e configurar um sistema SAP de alta disponibilidade no Azure usando um cluster de failover do Windows Server e o disco compartilhado de cluster para clustering de uma instância do SAP ASCS/SCS. Conforme descrito no [Guia de arquitetura: cluster de uma instância do SAP ASCS/SCS em um cluster de failover do Windows usando um disco compartilhado do cluster][sap-high-availability-guide-wsfc-shared-disk], há duas alternativas para o *disco compartilhado do cluster*:

- [Discos compartilhados do Azure](../../disks-shared.md)
- Usando o [sios Datakeeper Cluster Edition](https://us.sios.com/products/datakeeper-cluster/) para criar um armazenamento espelhado, que simulará o disco compartilhado clusterizado 

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar a instalação, analise estes documentos:

* [Clustering de uma instância do SAP ASCS/SCS em um cluster de failover do Windows usando o disco compartilhado de cluster no Azure][sap-high-availability-guide-wsfc-shared-disk]

* [Preparar a infraestrutura do Azure para alta disponibilidade do SAP usando um cluster de failover do Windows e um disco compartilhado para uma instância do SAP ASCS/SCS][sap-high-availability-infrastructure-wsfc-shared-disk]

Não descreveremos a instalação de DBMS neste artigo, pois as configurações variam conforme o sistema DBMS que você usa. Supomos que as preocupações de alta disponibilidade com o DBMS são dissipadas com o suporte às funcionalidades que os diferentes fornecedores de DBMS dão para o Azure. Os exemplos são o Always On ou o Espelhamento de Banco de Dados para SQL Server e Oracle Data Guard para bancos de dados Oracle. Os cenários de alta disponibilidade para o DBMS não são abordados neste artigo.

Não existem considerações especiais quando diferentes serviços DBMS interagem com uma configuração de SAP ASCS/SCS clusterizada no Azure.

> [!NOTE]
> O procedimento de instalação dos sistemas ABAP, sistemas Java e sistemas ABAP+Java do SAP NetWeaver é quase idêntico. A diferença mais significativa é que um sistema SAP ABAP tem uma instância ASCS. O sistema SAP Java tem uma instância SCS. O sistema ABAP+Java do SAP tem uma instância ASCS e uma instância ABAP+Java em execução no mesmo grupo de cluster de failover da Microsoft. As diferenças de instalação para cada pilha de instalação do SAP NetWeaver serão explicitamente mencionadas. Você pode assumir que o restante das etapas é o mesmo.  

## <a name="install-sap-with-a-high-availability-ascsscs-instance"></a><a name="31c6bd4f-51df-4057-9fdf-3fcbc619c170"></a>Instalar o SAP com uma instância ASCS/SCS de alta disponibilidade

> [!IMPORTANT]
> Se você usar SIOS para apresentar o disco compartilhado, não coloque o arquivo de paginação nos volumes espelhados do SIOS datakeeper. Você pode deixar o arquivo de página na unidade D temporária de uma máquina virtual do Azure, o que é o padrão. Se já não estiver lá, mova o arquivo da página do Windows para a unidade D da máquina virtual do Azure.  

Instalar o SAP com uma instância ASCS/SCS de alta disponibilidade envolve as seguintes tarefas:

* Criar um nome de host virtual para a instância clusterizada do SAP ASCS/SCS.
* Instale o SAP no primeiro nó de cluster.
* Modificar o perfil SAP da instância do ASCS/SCS.
* Adicionar uma porta de investigação.
* Abrir a porta de investigação do Firewall do Windows.

### <a name="create-a-virtual-host-name-for-the-clustered-sap-ascsscs-instance"></a><a name="a97ad604-9094-44fe-a364-f89cb39bf097"></a>Criar um nome de host virtual para a instância clusterizada do SAP ASCS/SCS

1. No Gerenciador de DNS do Windows, crie uma entrada DNS para o nome de host virtual da instância ASCS/SCS.

   > [!IMPORTANT]
   > O endereço IP que você atribui ao nome de host virtual da instância ASCS/SCS deve ser o mesmo que o endereço IP que você atribuiu a Azure Load Balancer.  

   ![Figura 1: Definir a entrada DNS para o nome virtual do cluster do SAP ASCS/SCS e endereço TCP/IP][sap-ha-guide-figure-3046]

   _Definir a entrada DNS para o nome virtual do cluster do SAP ASCS/SCS e endereço TCP/IP_

2. Se estiver usando o novo servidor de replicação de enfileiramento SAP 2, que também é instância clusterizada, você precisará reservar no DNS um nome de host virtual para ERS2 também. 

   > [!IMPORTANT]
   > O endereço IP que você atribui ao nome de host virtual da instância ERS2 deve ser o segundo endereço IP que você atribuiu para Azure Load Balancer.    

   ![Figura 1A: definir a entrada DNS para o nome virtual do cluster do SAP ASCS/SCS e o endereço TCP/IP][sap-ha-guide-figure-3046-ers2]

   _Definir a entrada DNS para o nome virtual do cluster SAP ERS2 e o endereço TCP/IP_

3. Para definir o endereço IP atribuído ao nome de host virtual, selecione domínio do **Gerenciador DNS**  >  .

   ![Figura 2: Novo nome virtual e endereço TCP/IP para a configuração de cluster do SAP ASCS/SCS][sap-ha-guide-figure-3047]

   _Novo nome virtual e endereço TCP/IP para configuração de cluster do SAP ASCS/SCS_

### <a name="install-the-sap-first-cluster-node"></a><a name="eb5af918-b42f-4803-bb50-eff41f84b0b0"></a> Instalar o primeiro nó do cluster do SAP

1. Execute a primeira opção de nó de cluster no nó A do cluster. Selecione:

   * **Sistema ABAP**: **ASCS** com número de instância **00**
   * **Sistema Java**: **SCS** com número de instância **01**
   * **Sistema ABAP+Java**: **ASCS** com número de instância **00** e **SCS** com número de instância **01**

   > [!IMPORTANT]
   > Tenha em mente que a configuração nas regras de balanceamento de carga do balanceador de carga interno do Azure (se estiver usando a SKU básica) e os números de instância do SAP selecionados devem corresponder.

2. Siga o procedimento de instalação do SAP descrito. Certifique-se de iniciar a opção de instalação "primeiro nó de cluster", para escolher "disco compartilhado de cluster" como opção de configuração.

> [!TIP]
> A documentação de instalação do SAP descreve como instalar o primeiro nó ASCS/SCS do cluster.

### <a name="modify-the-sap-profile-of-the-ascsscs-instance"></a><a name="e4caaab2-e90f-4f2c-bc84-2cd2e12a9556"></a> Modificar o perfil SAP da instância do ASCS/SCS

Se você tiver o Queue Replication Server 1, adicione o parâmetro SAP profile `enque/encni/set_so_keepalive` conforme descrito abaixo. O parâmetro de perfil impede conexões entre os processos de trabalho do SAP e o servidor de enfileiramento de fechar quando estão ociosas por muito tempo. O parâmetro SAP não é necessário para ERS2. 

1. Adicione esse parâmetro de perfil ao perfil da instância do SAP ASCS/SCS, se estiver usando ERS1.

   ```
   enque/encni/set_so_keepalive = true
   ```

   Para ERS1 e ERS2, verifique se os parâmetros do `keepalive` sistema operacional estão definidos conforme descrito na observação do SAP [1410736](https://launchpad.support.sap.com/#/notes/1410736).   

2. Para aplicar as alterações de parâmetro do perfil SAP, reinicie a instância do SAP ASCS/SCS.

### <a name="add-a-probe-port"></a><a name="10822f4f-32e7-4871-b63a-9b86c76ce761"></a> Adicionar uma porta de investigação

Use a funcionalidade de investigação do balanceador interno de carga para fazer com que toda a configuração do cluster funcione com o Azure Load Balancer. Normalmente, um balanceador de carga interno do Azure distribui a carga de trabalho de entrada igualmente entre as máquinas virtuais participantes.

No entanto, isso não funcionará em algumas configurações de cluster porque apenas uma instância está ativa. A outra instância é passiva e não pode aceitar parte da carga de trabalho. Uma funcionalidade de investigação ajuda quando o balanceador de carga interno do Azure detecta qual instância está ativa e só tem como alvo a instância ativa.  

> [!IMPORTANT]
> Nesta configuração de exemplo, **ProbePort** é definido como 620 **NR**. Para a instância do SAP ASCS com o número **00** , é 620 **00**. Você precisará ajustar a configuração para corresponder aos números de instância do SAP e ao seu SID do SAP.

Para adicionar uma porta de investigação, execute este módulo do PowerShell em uma das VMs do cluster:

- No caso da instância do SAP ASC/SCS 
   ```powershell
   Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID SID -ProbePort 62000
   ```

- Se estiver usando ERS2, que é clusterizado. Não é necessário configurar a porta de investigação para ERS1, pois ela não é clusterizada.  
   ```powershell
   Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID SID -ProbePort 62001 -IsSAPERSClusteredInstance $True
   ```

 O código para a função `Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource` ficaria assim:
   ```powershell
    function Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource {

    <#
    .SYNOPSIS 
    Set-AzureLoadBalancerHealthProbePortOnSAPClusterIPResource will set a new Azure Load Balancer Health Probe Port on 'SAP $SAPSID IP' cluster resource.

    .DESCRIPTION
    Set-AzureLoadBalancerHealthProbePortOnSAPClusterIPResource will set a new Azure Load Balancer Health Probe Port on 'SAP $SAPSID IP' cluster resource.
    It will also restart SAP Cluster group (default behavior), to activate the changes. 

    You need to run it on one of the SAP ASCS/SCS Windows cluster nodes.

    Expectation is that SAP group is installed with official SWPM installation tool, which will set default expected naming convention for:
    - SAP Cluster Group:               'SAP $SAPSID'
    - SAP Cluster IP Address Resource: 'SAP $SAPSID IP' 

    .PARAMETER SAPSID 
    SAP SID - 3 characters staring with letter.

    .PARAMETER ProbePort 
    Azure Load Balancer Health Check Probe Port.

    .PARAMETER RestartSAPClusterGroup 
    Optional parameter. Default value is '$True', so SAP cluster group will be restarted to activate the changes.

    .PARAMETER IsSAPERSClusteredInstance 
    Optional parameter.Default value is '$False'.
    If set to $True , then handle clsutered new SAP ERS2 instance.

    .EXAMPLE 
    # Set probe port to 62000, on SAP cluster resource 'SAP AB1 IP', and restart the SAP cluster group 'SAP AB1', to activate the changes.
    Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID AB1 -ProbePort 62000 

    .EXAMPLE 
    # Set probe port to 62000, on SAP cluster resource 'SAP AB1 IP'. SAP cluster group 'SAP AB1' IS NOT restarted, therefore changes are NOT active.
    # To activate the changes you need to manualy restart 'SAP AB1' cluster group.
    Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID AB1 -ProbePort 62000 -RestartSAPClusterGroup $False

    .EXAMPLE 
    # Set probe port to 62001, on SAP cluster resource 'SAP AB1 ERS IP'. SAP cluster group 'SAP AB1 ERS' IS restarted, to activate the changes.
    Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID AB1 -ProbePort 62000 -IsSAPERSClusteredInstance $True

    #> 

        [CmdletBinding()]
        param(

            [Parameter(Mandatory=$True)]
            [ValidateNotNullOrEmpty()]  
            [ValidateLength(3,3)]      
            [string]$SAPSID,

            [Parameter(Mandatory=$True)]
            [ValidateNotNullOrEmpty()]        
            [int] $ProbePort,

            [Parameter(Mandatory=$False)] 
            [bool] $RestartSAPClusterGroup = $True,

            [Parameter(Mandatory=$False)] 
            [bool] $IsSAPERSClusteredInstance = $False
        )

        BEGIN{}

        PROCESS{
            try{                                      

                if($IsSAPERSClusteredInstance){
                    #Handle clustered SAP ERS Instance
                    $SAPClusterRoleName = "SAP $SAPSID ERS"
                    $SAPIPresourceName = "SAP $SAPSID ERS IP"            
                }else{
                    #Handle clustered SAP ASCS/SCS Instance
                    $SAPClusterRoleName = "SAP $SAPSID"
                    $SAPIPresourceName = "SAP $SAPSID IP"
                }

                $SAPIPResourceClusterParameters =  Get-ClusterResource $SAPIPresourceName | Get-ClusterParameter
                $IPAddress = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Address" }).Value
                $NetworkName = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Network" }).Value
                $SubnetMask = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "SubnetMask" }).Value
                $OverrideAddressMatch = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "OverrideAddressMatch" }).Value
                $EnableDhcp = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "EnableDhcp" }).Value
                $OldProbePort = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "ProbePort" }).Value

                $var = Get-ClusterResource | Where-Object {  $_.name -eq $SAPIPresourceName  }
                Write-Output "Current configuration parameters for SAP IP cluster resource '$SAPIPresourceName' are:" 

                Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter

                Write-Output " "
                Write-Output "Current probe port property of the SAP cluster resource '$SAPIPresourceName' is '$OldProbePort'." 
                Write-Output " "
                Write-Output "Setting the new probe port property of the SAP cluster resource '$SAPIPresourceName' to '$ProbePort' ..." 
                Write-Output " "

                $var | Set-ClusterParameter -Multiple @{"Address"=$IPAddress;"ProbePort"=$ProbePort;"Subnetmask"=$SubnetMask;"Network"=$NetworkName;"OverrideAddressMatch"=$OverrideAddressMatch;"EnableDhcp"=$EnableDhcp}

                Write-Output " "

                if($RestartSAPClusterGroup){
                    Write-Output ""
                    Write-Output "Activating changes..." 

                    Write-Output " "
                    Write-Output "Taking SAP cluster IP resource '$SAPIPresourceName' offline ..."
                    Stop-ClusterResource -Name $SAPIPresourceName
                    sleep 5

                    Write-Output "Starting SAP cluster role '$SAPClusterRoleName' ..."
                    Start-ClusterGroup -Name $SAPClusterRoleName

                    Write-Output "New ProbePort parameter is active." 
                    Write-Output " "

                    Write-Output "New configuration parameters for SAP IP cluster resource '$SAPIPresourceName':" 
                    Write-Output " " 
                    Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter
                }else
                {
                    Write-Output "SAP cluster role '$SAPClusterRoleName' is not restarted, therefore changes are not activated."
                }
            }
            catch{
               Write-Error  $_.Exception.Message
           }
        }
        END {}
    }

   ```

### <a name="open-the-windows-firewall-probe-port"></a><a name="4498c707-86c0-4cde-9c69-058a7ab8c3ac"></a> Abrir a porta de investigação do Firewall do Windows

Abra a porta de investigação do firewall do Windows nos dois nós de cluster. Use o script a seguir para abrir uma porta de investigação no firewall do Windows. Atualize as variáveis do PowerShell para seu ambiente.  
Se estiver usando ERS2, também será necessário abrir a porta do firewall para a porta de investigação do ERS2.  

  ```powershell
    $ProbePort = 62000   # ProbePort of the Azure internal load balancer
    New-NetFirewallRule -Name AzureProbePort -DisplayName "Rule for Azure Probe Port" -Direction Inbound -Action Allow -Protocol TCP -LocalPort $ProbePort
  ```

## <a name="install-the-database-instance"></a><a name="85d78414-b21d-4097-92b6-34d8bcb724b7"></a> Instalar a instância de banco de dados

Para instalar a instância de banco de dados, siga o processo descrito na documentação de instalação do SAP.

## <a name="install-the-second-cluster-node"></a><a name="8a276e16-f507-4071-b829-cdc0a4d36748"></a>Instalar o segundo nó do cluster

Para instalar o segundo cluster, execute as etapas descritas no guia de instalação do SAP.

## <a name="install-the-sap-primary-application-server"></a><a name="2477e58f-c5a7-4a5d-9ae3-7b91022cafb5"></a> Instalar o servidor de aplicativos primário SAP

Instale a instância do PAS (servidor de aplicativos primário) \<SID\> -di-0 na máquina virtual que você designou para hospedar o Pas. Não há dependências no Azure. Se estiver usando SIOS, não haverá configurações específicas do datakeeper.

## <a name="install-the-sap-additional-application-server"></a><a name="0ba4a6c1-cc37-4bcf-a8dc-025de4263772"></a> Instalar o servidor de aplicativos SAP adicional

Instale um AAS (Servidor de Aplicativos Adicional) SAP em todas as máquinas virtuais que você designou para hospedar um Servidor de Aplicativos SAP. 

## <a name="test-the-sap-ascsscs-instance-failover"></a><a name="18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9"></a> Testar o failover da instância do SAP ASCS/SCS

Para os testes de failover descritos, presumimos que o SAP ASCS está ativo no nó A.  

1. Verifique se o sistema SAP pode fazer failover com êxito do nó A para o nó B escolha uma destas opções para iniciar um failover do \<SID\> grupo de clusters SAP do nó a do cluster para o nó b do cluster:
    - Gerenciador de Cluster de Failover  
    - PowerShell de Cluster de Failover

    ```powershell
    $SAPSID = "PR1"     # SAP <SID>

    $SAPClusterGroup = "SAP $SAPSID"
    Move-ClusterGroup -Name $SAPClusterGroup

    ```

2. Reinicie o nó A do cluster no sistema operacional Windows convidado. Isso inicia um failover automático do \<SID\> grupo de clusters SAP do nó a para o nó B.  
3. Reinicie o nó A do cluster no Portal do Azure. Isso inicia um failover automático do \<SID\> grupo de clusters SAP do nó a para o nó B.  
4. Reinicie o nó A do cluster usando o Azure PowerShell. Isso inicia um failover automático do \<SID\> grupo de clusters SAP do nó a para o nó B.

5. Verificação
   - Após o failover, verifique se o \<SID\> grupo de clusters SAP está em execução no nó B do cluster. 

      ![Figura 8: No Gerenciador de Cluster de Failover, o grupo de clusters SAP \<SID\> está em execução no nó B de cluster][sap-ha-guide-figure-5002]

      _No Gerenciador de Cluster de Failover, o \<SID\> grupo de clusters SAP está em execução no nó B do cluster_

   - Após o failover, verifique se o disco compartilhado agora está montado no nó B do cluster. 
   - Após o failover, se estiver usando SIOS, verifique se o SIOS datakeeper está replicando dados da unidade do volume de origem S no nó B do cluster para a unidade de volume de destino S no nó A do cluster. 

      ![Figura 9: o SIOS DataKeeper replica o volume local do nó B de cluster para o nó A de cluster][sap-ha-guide-figure-5003]

      _O SIOS datakeeper Replica o volume local do nó B do cluster para o nó A do cluster_