---
title: Instalar a alta disponibilidade do SAP NetWeaver em um cluster de failover do Windows e em um disco compartilhado para uma instância do SAP ASCS/SCS no Azure | Microsoft Docs
description: Saiba como instalar a alta disponibilidade do SAP NetWeaver em um cluster de failover do Windows e em um disco compartilhado para uma instância do SAP ASCS/SCS.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: gwallace
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 6209bcb3-5b20-4845-aa10-1475c576659f
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c1a7d3d3a8f66cfbb3ed649ac645520f39cbb1e4
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67709005"
---
# <a name="install-sap-netweaver-ha-on-a-windows-failover-cluster-and-shared-disk-for-an-sap-ascsscs-instance-in-azure"></a>Instalar a alta disponibilidade do SAP NetWeaver em um cluster de failover do Windows e em um disco compartilhado para uma instância do SAP ASCS/SCS no Azure

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

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

Este artigo descreve como instalar e configurar um sistema SAP de alta disponibilidade no Azure usando um cluster de failover do Windows Server e o disco compartilhado de cluster para clustering de uma instância do SAP ASCS/SCS.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar a instalação, analise estes documentos:

* [Guia de arquitetura: Uma instância do SAP ASCS/SCS de cluster em um cluster de failover do Windows usando um disco compartilhado de cluster][sap-high-availability-guide-wsfc-shared-disk]

* [Preparar a infraestrutura do Azure para SAP de HA por meio de um cluster de failover do Windows e disco compartilhado para uma instância do SAP ASCS/SCS][sap-high-availability-infrastructure-wsfc-shared-disk]

Não descreveremos a instalação de DBMS neste artigo, pois as configurações variam conforme o sistema DBMS que você usa. Supomos que as preocupações de alta disponibilidade com o DBMS são dissipadas com o suporte às funcionalidades que os diferentes fornecedores de DBMS dão para o Azure. Os exemplos são o Always On ou o Espelhamento de Banco de Dados para SQL Server e Oracle Data Guard para bancos de dados Oracle. No cenário que usamos neste artigo, não adicionamos outra proteção ao DBMS.

Não existem considerações especiais quando diferentes serviços DBMS interagem com uma configuração de SAP ASCS/SCS clusterizada no Azure.

> [!NOTE]
> O procedimento de instalação dos sistemas ABAP, sistemas Java e sistemas ABAP+Java do SAP NetWeaver é quase idêntico. A diferença mais significativa é que um sistema SAP ABAP tem uma instância ASCS. O sistema SAP Java tem uma instância SCS. O sistema ABAP+Java do SAP tem uma instância ASCS e uma instância ABAP+Java em execução no mesmo grupo de cluster de failover da Microsoft. As diferenças de instalação para cada pilha de instalação do SAP NetWeaver serão explicitamente mencionadas. Você pode assumir que todas as outras partes são as mesmas.  
>
>

## <a name="31c6bd4f-51df-4057-9fdf-3fcbc619c170"></a>Instalar o SAP com uma instância ASCS/SCS de alta disponibilidade

> [!IMPORTANT]
> Não coloque o arquivo da página em volumes espelhados do SIOS DataKeeper. O DataKeeper não dá suporte a volumes espelhados. Você pode deixar o arquivo de página na unidade D temporária de uma máquina virtual do Azure, o que é o padrão. Se já não estiver lá, mova o arquivo da página do Windows para a unidade D da máquina virtual do Azure.
>
>

Instalar o SAP com uma instância ASCS/SCS de alta disponibilidade envolve as seguintes tarefas:

* Criar um nome de host virtual para a instância clusterizada do SAP ASCS/SCS.
* Instalar o primeiro nó do cluster do SAP.
* Modificar o perfil SAP da instância do ASCS/SCS.
* Adicionar uma porta de investigação.
* Abrir a porta de investigação do Firewall do Windows.

### <a name="a97ad604-9094-44fe-a364-f89cb39bf097"></a>Criar um nome de host virtual para a instância clusterizada do SAP ASCS/SCS

1. No Gerenciador de DNS do Windows, crie uma entrada DNS para o nome de host virtual da instância ASCS/SCS.

   > [!IMPORTANT]
   > O endereço IP que você atribui ao nome de host virtual da instância ASCS/SCS deve ser o mesmo que o endereço IP que você atribuiu ao Azure Load Balancer (\<SID\>-lb-ascs).  
   >
   >

   O endereço IP do nome de host virtual do SAP ASCS/SCS (pr1-ascs sap) é o mesmo que o endereço IP do Azure Load Balancer (pr1-lb-ascs).

   ![Figura 1: Definir a entrada DNS para o nome virtual do cluster do SAP ASCS/SCS e endereço TCP/IP][sap-ha-guide-figure-3046]

   _**Figura 1:** Definir a entrada DNS para o nome virtual do cluster do SAP ASCS/SCS e endereço TCP/IP_

2. Para definir o endereço IP atribuído ao nome de host virtual, selecione **Gerenciador de DNS** > **Domínio**.

   ![Figura 2: Novo nome virtual e endereço TCP/IP para a configuração de cluster do SAP ASCS/SCS][sap-ha-guide-figure-3047]

   _**Figura 2:** Novo nome virtual e endereço TCP/IP para a configuração de cluster do SAP ASCS/SCS_

### <a name="eb5af918-b42f-4803-bb50-eff41f84b0b0"></a> Instalar o primeiro nó do cluster do SAP

1. Execute a primeira opção do nó de cluster no nó A de cluster, por exemplo, no host pr1-ascs-0*.
2. Para manter as portas padrão para o balanceador interno de carga do Azure, escolha:

   * **Sistema ABAP**: **ASCS** com número de instância **00**
   * **Sistema Java**: **SCS** com número de instância **01**
   * **Sistema ABAP+Java**: **ASCS** com número de instância **00** e **SCS** com número de instância **01**

   Para usar números de instância diferentes de 00 para a instância do ASCS ABAP, e 01 para a instância de Java SCS, primeiro, altere as regras de balanceamento de carga padrão do balanceador de carga interno do Azure. Para obter mais informações, consulte [alterar a regras para o balanceador de carga interno do Azure de balanceamento de carga do ASCS/SCS padrão][sap-ha-guide-8.9].

As próximas tarefas não são descritas na documentação de instalação padrão do SAP.

> [!NOTE]
> A documentação de instalação do SAP descreve como instalar o primeiro nó ASCS/SCS do cluster.
>
>

### <a name="e4caaab2-e90f-4f2c-bc84-2cd2e12a9556"></a> Modificar o perfil SAP da instância do ASCS/SCS

Primeiro, adicione um novo parâmetro de perfil. O parâmetro de perfil impede conexões entre os processos de trabalho do SAP e o servidor de enfileiramento de fechar quando estão ociosas por muito tempo. Mencionamos o cenário de problema em [adicionar entradas de registro em ambos os nós de cluster da instância SAP ASCS/SCS][sap-ha-guide-8.11]. Nessa seção, também apresentamos duas alterações para alguns parâmetros básicos de conexão TCP/IP. Na segunda etapa, você precisará configurar o servidor de enfileiramento para enviar um sinal `keep_alive` para que as conexões não atinjam o limite de ociosidade do balanceador interno de carga do Azure.

Para modificar o perfil SAP da instância do ASCS/SCS:

1. Adicione este parâmetro de perfil ao perfil da instância do SAP ASCS/SCS:

   ```
   enque/encni/set_so_keepalive = true
   ```
   Em nosso exemplo, o caminho é:

   `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_ASCS00_pr1-ascs-sap`

   Por exemplo, para o perfil da instância do SAP SCS e o caminho correspondente:

   `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_SCS01_pr1-ascs-sap`

2. Para aplicar as alterações, reinicie a instância SAP ASCS/SCS.

### <a name="10822f4f-32e7-4871-b63a-9b86c76ce761"></a> Adicionar uma porta de investigação

Use a funcionalidade de investigação do balanceador interno de carga para fazer com que toda a configuração do cluster funcione com o Azure Load Balancer. Normalmente, um balanceador de carga interno do Azure distribui a carga de trabalho de entrada igualmente entre as máquinas virtuais participantes.

 No entanto, isso não funcionará em algumas configurações de cluster porque apenas uma instância está ativa. A outra instância é passiva e não pode aceitar parte da carga de trabalho. A funcionalidade de investigação ajuda quando o balanceador de carga interno do Azure atribui o trabalho apenas a uma instância ativa. Com a funcionalidade de investigação, o balanceador de carga interno pode detectar quais instâncias estão ativas e mirar apenas na instância com a carga de trabalho.

Para adicionar uma porta de investigação:

1. Verifique o valor de **ProbePort** atual executando o seguinte comando do PowerShell:

   ```powershell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPNetworkIPClusterName = "SAP $SAPSID IP"
   Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter
   ```

   Execute o comando dentro de uma das máquinas virtuais na configuração do cluster.

2. Defina uma porta de investigação. O número da porta de investigação padrão é 0. Em nosso exemplo, usamos a porta de investigação 62000.

   ![Figura 3: A porta de investigação da configuração de cluster é 0 por padrão][sap-ha-guide-figure-3048]

   _**Figura 3:** A porta de investigação da configuração de cluster padrão é 0_

   O número da porta é definido nos modelos do Azure Resource Manager para SAP. Você pode atribuir o número da porta no PowerShell.

   Para definir um novo valor de ProbePort para o recurso de cluster SAP \<SID\> IP, execute o seguinte script do PowerShell para atualizar as variáveis do PowerShell para o seu ambiente:

   ```powershell
   $SAPSID = "PR1"      # SAP <SID>
   $ProbePort = 62000   # ProbePort of the Azure internal load balancer

   Clear-Host
   $SAPClusterRoleName = "SAP $SAPSID"
   $SAPIPresourceName = "SAP $SAPSID IP"
   $SAPIPResourceClusterParameters =  Get-ClusterResource $SAPIPresourceName | Get-ClusterParameter
   $IPAddress = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Address" }).Value
   $NetworkName = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Network" }).Value
   $SubnetMask = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "SubnetMask" }).Value
   $OverrideAddressMatch = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "OverrideAddressMatch" }).Value
   $EnableDhcp = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "EnableDhcp" }).Value
   $OldProbePort = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "ProbePort" }).Value

   $var = Get-ClusterResource | Where-Object {  $_.name -eq $SAPIPresourceName  }

   Write-Host "Current configuration parameters for SAP IP cluster resource '$SAPIPresourceName' are:" -ForegroundColor Cyan
   Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter

   Write-Host
   Write-Host "Current probe port property of the SAP cluster resource '$SAPIPresourceName' is '$OldProbePort'." -ForegroundColor Cyan
   Write-Host
   Write-Host "Setting the new probe port property of the SAP cluster resource '$SAPIPresourceName' to '$ProbePort' ..." -ForegroundColor Cyan
   Write-Host

   $var | Set-ClusterParameter -Multiple @{"Address"=$IPAddress;"ProbePort"=$ProbePort;"Subnetmask"=$SubnetMask;"Network"=$NetworkName;"OverrideAddressMatch"=$OverrideAddressMatch;"EnableDhcp"=$EnableDhcp}

   Write-Host

   $ActivateChanges = Read-Host "Do you want to take restart SAP cluster role '$SAPClusterRoleName', to activate the changes (yes/no)?"

   if($ActivateChanges -eq "yes"){
   Write-Host
   Write-Host "Activating changes..." -ForegroundColor Cyan

   Write-Host
   write-host "Taking SAP cluster IP resource '$SAPIPresourceName' offline ..." -ForegroundColor Cyan
   Stop-ClusterResource -Name $SAPIPresourceName
   sleep 5

   Write-Host "Starting SAP cluster role '$SAPClusterRoleName' ..." -ForegroundColor Cyan
   Start-ClusterGroup -Name $SAPClusterRoleName

   Write-Host "New ProbePort parameter is active." -ForegroundColor Green
   Write-Host

   Write-Host "New configuration parameters for SAP IP cluster resource '$SAPIPresourceName':" -ForegroundColor Cyan
   Write-Host
   Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter
   }else
   {
   Write-Host "Changes are not activated."
   }
   ```

   Depois de colocar a função de cluster SAP \<SID\> online, verifique se **ProbePort** está definido como o novo valor.

   ```powershell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPNetworkIPClusterName = "SAP $SAPSID IP"
   Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter

   ```
   Depois que o script é executado, será solicitado que você reinicie o grupo de clusters do SAP para ativar as alterações.

   ![Figura 4: Investigar a porta de cluster depois de definir o novo valor][sap-ha-guide-figure-3049]

   _**Figura 4:** Investigar a porta de cluster depois de definir o novo valor_

### <a name="4498c707-86c0-4cde-9c69-058a7ab8c3ac"></a> Abrir a porta de investigação do Firewall do Windows

Abra a porta de investigação do firewall do Windows nos dois nós de cluster. Use o script a seguir para abrir uma porta de investigação no firewall do Windows. Atualize as variáveis do PowerShell para seu ambiente.

  ```powershell
  $ProbePort = 62000   # ProbePort of the Azure internal load balancer

  New-NetFirewallRule -Name AzureProbePort -DisplayName "Rule for Azure Probe Port" -Direction Inbound -Action Allow -Protocol TCP -LocalPort $ProbePort
  ```

**ProbePort** é definida como **62000**. Agora, é possível acessar o compartilhamento de arquivos \\\ascsha-clsap\sapmnt de outros hosts como ascsha-dbas.

## <a name="85d78414-b21d-4097-92b6-34d8bcb724b7"></a> Instalar a instância de banco de dados

Para instalar a instância de banco de dados, siga o processo descrito na documentação de instalação do SAP.

## <a name="8a276e16-f507-4071-b829-cdc0a4d36748"></a>Instalar o segundo nó do cluster

Para instalar o segundo cluster, execute as etapas descritas no guia de instalação do SAP.

## <a name="094bc895-31d4-4471-91cc-1513b64e406a"></a> Alterar o tipo de início da instância do serviço Windows ERS do SAP

Altere o tipo de inicialização do serviço Windows do SAP ERS para **Automático (Atraso na Inicialização)** em ambos os nós do cluster.

![Figura 5: Alterar o tipo de serviço da instância ERS do SAP para atraso automático][sap-ha-guide-figure-3050]

_**Figura 5:** Alterar o tipo de serviço da instância ERS do SAP para atraso automático_

## <a name="2477e58f-c5a7-4a5d-9ae3-7b91022cafb5"></a> Instalar o servidor de aplicativos primário SAP

Instalar a instância \<SID\>-di-0 do PAS (Servidor de Aplicativos primário) na máquina virtual que você designou para hospedar o PAS. Não há dependências no Azure. Não há configurações específicas ao DataKeeper.

## <a name="0ba4a6c1-cc37-4bcf-a8dc-025de4263772"></a> Instalar o servidor de aplicativos SAP adicional

Instale um AAS (Servidor de Aplicativos Adicional) SAP em todas as máquinas virtuais que você designou para hospedar um Servidor de Aplicativos SAP. Por exemplo, em \<SID\>-di-1 para \<SID\>-di-&lt;n&gt;.

> [!NOTE]
> Isso conclui a instalação de um sistema SAP NetWeaver de alta disponibilidade. Em seguida, continue com o teste de failover.
>


## <a name="18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9"></a> Testar o failover da instância do SAP ASCS/SCS e a replicação do SIOS
É muito fácil testar e monitorar um failover de instância de SAP ASCS/SCS e a replicação do disco SIOS usando o Gerenciador de Cluster de Failover e a ferramenta de Gerenciamento e Configuração do SIOS DataKeeper.

### <a name="65fdef0f-9f94-41f9-b314-ea45bbfea445"></a> A instância do SAP ASCS/SCS está em execução no Nó A do Cluster

O grupo de clusters SAP PR1 está em execução no nó A do cluster. Por exemplo, em pr1-ascs-0. Atribua a unidade de disco compartilhado S, que é parte do grupo de cluster SAP PR1, ao cluster A do nó. A instância ASCS/SCS também usa a unidade de disco S. 

![Figura 6: Gerenciador de Cluster de Failover: O SAP \<SID\> grupo de cluster está em execução no nó A do cluster][sap-ha-guide-figure-5000]

_**Figura 6:** Gerenciador de Cluster de Failover: O SAP \<SID\> grupo de cluster está em execução no nó A do cluster_

Na ferramenta de Gerenciamento e Configuração do DataKeeper SIOS, você pode ver que os dados do disco compartilhado são replicados de modo síncrono da unidade do volume de origem S em um nó A de cluster para a unidade do volume de destino S no nó B de cluster. Por exemplo, é replicado de pr1-ascs-0 [10.0.0.40] a pr1-ascs-1 [10.0.0.41].

![Figura 7: No SIOS DataKeeper, replique o volume local do nó de cluster A para o nó de cluster B][sap-ha-guide-figure-5001]

_**Figura 7:** No SIOS DataKeeper, replique o volume local do nó de cluster A para o nó de cluster B_

### <a name="5e959fa9-8fcd-49e5-a12c-37f6ba07b916"></a> Failover do nó A para o nó B

1. Você pode usar estas opções para iniciar um failover do grupo de clusters \<SID\> do SAP do nó A para o nó B de cluster:
   - Gerenciador de Cluster de Failover  
   - PowerShell de Cluster de Failover

   ```powershell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPClusterGroup = "SAP $SAPSID"
   Move-ClusterGroup -Name $SAPClusterGroup

   ```
2. Reinicie o nó A do cluster no sistema operacional Windows convidado. Isso iniciará um failover automático do grupo de clusters SAP \<SID\> do nó A para o nó B.  
3. Reinicie o nó A do cluster no Portal do Azure. Isso iniciará um failover automático do grupo de clusters SAP \<SID\> do nó A para o nó B.  
4. Reinicie o nó A do cluster usando o Azure PowerShell. Isso iniciará um failover automático do grupo de clusters SAP \<SID\> do nó A para o nó B.

   Após o failover, o grupo de clusters SAP \<SID\> está em execução no nó de cluster B. Por exemplo, em pr1-ascs-1.

   ![Figura 8: No Gerenciador de Cluster de Failover, o SAP \<SID\> grupo de cluster está em execução no nó B do cluster][sap-ha-guide-figure-5002]

   _**Figura 8**: No Gerenciador de Cluster de Failover, o SAP \<SID\> grupo de cluster está em execução no nó B do cluster_

   O disco compartilhado agora é montado no nó de cluster B. O SIOS DataKeeper está replicando dados da unidade do volume de origem S no nó de cluster B para a unidade do volume de destino S no nó de cluster A. Por exemplo, ele está replicando pr1-ascs-1 [10.0.0.41] a pr1-ascs-0 [10.0.0.40].

   ![Figura 9: O SIOS DataKeeper replica o volume local do nó de cluster B para o nó de cluster A][sap-ha-guide-figure-5003]

   _**Figura 9:** O SIOS DataKeeper replica o volume local do nó de cluster B para o nó de cluster A_
