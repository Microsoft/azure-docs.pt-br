---
title: Criar uma configuração multi-SID do SAP no Azure | Microsoft Docs
description: Guia para configuração multi-SID do SAP NetWeaver de alta disponibilidade em máquinas virtuais do Windows
services: virtual-machines-windows, virtual-network, storage
documentationcenter: saponazure
author: goraco
manager: gwallace
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 0b89b4f8-6d6c-45d7-8d20-fe93430217ca
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fddb0f7cceea167885f56cf6ff5e8639bec1d937
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67710347"
---
# <a name="create-an-sap-netweaver-multi-sid-configuration"></a>Criar uma configuração multi-SID do SAP NetWeaver

[load-balancer-multivip-overview]:../../../load-balancer/load-balancer-multivip-overview.md
[sap-ha-guide]:sap-high-availability-guide.md 
[sap-ha-guide-figure-6001]:./media/virtual-machines-shared-sap-high-availability-guide/6001-sap-multi-sid-ascs-scs-sid1.png
[sap-ha-guide-figure-6002]:./media/virtual-machines-shared-sap-high-availability-guide/6002-sap-multi-sid-ascs-scs.png
[sap-ha-guide-figure-6003]:./media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png
[sap-ha-guide-figure-6004]:./media/virtual-machines-shared-sap-high-availability-guide/6004-sap-multi-sid-dns.png
[sap-ha-guide-figure-6005]:./media/virtual-machines-shared-sap-high-availability-guide/6005-sap-multi-sid-azure-portal.png
[sap-ha-guide-figure-6006]:./media/virtual-machines-shared-sap-high-availability-guide/6006-sap-multi-sid-sios-replication.png
[networking-limits-azure-resource-manager]:../../../azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits
[sap-ha-guide-9.1.1]:sap-high-availability-guide.md#a97ad604-9094-44fe-a364-f89cb39bf097 
[sap-ha-guide-8.8]:sap-high-availability-guide.md#f19bd997-154d-4583-a46e-7f5a69d0153c
[sap-ha-guide-8.12.3.3]:sap-high-availability-guide.md#d9c1fc8e-8710-4dff-bec2-1f535db7b006 
[sap-ha-guide-9]:sap-high-availability-guide.md#a06f0b49-8a7a-42bf-8b0d-c12026c5746b
[sap-ha-guide-9.1]:sap-high-availability-guide.md#31c6bd4f-51df-4057-9fdf-3fcbc619c170 
[sap-ha-guide-9.1.2]:sap-high-availability-guide.md#eb5af918-b42f-4803-bb50-eff41f84b0b0 
[sap-ha-guide-9.1.3]:sap-high-availability-guide.md#e4caaab2-e90f-4f2c-bc84-2cd2e12a9556 
[sap-ha-guide-9.1.4]:sap-high-availability-guide.md#10822f4f-32e7-4871-b63a-9b86c76ce761 
[sap-ha-guide-9.4]:sap-high-availability-guide.md#094bc895-31d4-4471-91cc-1513b64e406a 
[sap-ha-guide-9.5]:sap-high-availability-guide.md#2477e58f-c5a7-4a5d-9ae3-7b91022cafb5 
[sap-ha-guide-9.6]:sap-high-availability-guide.md#0ba4a6c1-cc37-4bcf-a8dc-025de4263772 
[sap-ha-guide-10]:sap-high-availability-guide.md#18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9

Em setembro de 2016, a Microsoft lançou um recurso com o qual é possível gerenciar vários endereços IP virtual usando um balanceador de carga interno do Azure. Essa funcionalidade já existe no balanceador externo de carga do Azure.

Se você tiver uma implantação do SAP, você pode usar um balanceador de carga interno para criar uma configuração de cluster do Windows para SAP ASCS/SCS, conforme documentado na [guia para alta disponibilidade do SAP NetWeaver em VMs do Windows][sap-ha-guide].

Este artigo aborda como mudar de uma instalação ASCS/SCS única para uma configuração Multi-SID do SAP com a instalação de instâncias em cluster do SAP ASCS/SCS adicionais em um cluster WSFC (Windows Server Failover Clustering) existente. Quando esse processo for concluído, você já terá um cluster multi-SID do SAP configurado.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos
Você já tiver configurado um cluster WSFC que é usado para uma instância do SAP ASCS/SCS, conforme discutido na [guia para alta disponibilidade do SAP NetWeaver em VMs do Windows][sap-ha-guide] e conforme mostrado neste diagrama.

![Instância do SAP ASCS/SCS de alta disponibilidade][sap-ha-guide-figure-6001]

## <a name="target-architecture"></a>Arquitetura de destino

A meta é instalar várias instâncias clusterizadas do SAP ABAP ASCS ou do SAP Java SCS no mesmo cluster WSFC, conforme ilustrado aqui:

![Várias instâncias clusterizadas do SAP ASCS/SCS no Azure][sap-ha-guide-figure-6002]

> [!NOTE]
>Há um limite no número de IPs de front-end privado para cada balanceador de carga interno do Azure.
>
>O número máximo de instâncias do SAP ASCS/SCS em um cluster WSFC é igual ao número máximo de IPs de front-end privado para cada balanceador de carga interno do Azure.
>

Para obter mais informações sobre limites do balanceador de carga, confira “IP de front-end privado por balanceador de carga” em [Limites de rede: Azure Resource Manager][networking-limits-azure-resource-manager].

O cenário completo com dois sistemas SAP de alta disponibilidade seria assim:

![Instalação multi-SID de alta disponibilidade do SAP com dois SIDs do sistema SAP][sap-ha-guide-figure-6003]

> [!IMPORTANT]
> A instalação deve atender às seguintes condições:
> - As instâncias do SAP ASCS/SCS deverão compartilhar o mesmo cluster WSFC.
> - Cada DBMS SID deverá ter seu próprio cluster WSFC dedicado.
> - Os servidores de aplicativos SAP que pertencem a um SID do sistema SAP deverão ter suas próprias VMs dedicadas.


## <a name="prepare-the-infrastructure"></a>Preparar a infraestrutura
Para preparar sua infra-estrutura, é possível instalar uma instância do SAP ASCS/SCS adicional com os seguintes parâmetros:

| Nome do parâmetro | Valor |
| --- | --- |
| SID do SAP ASCS/SCS |pr1-lb-ascs |
| Balanceador de carga interno de DBMS SAP | PR5 |
| Nome do host virtual do SAP | pr5-sap-cl |
| Endereço IP do host virtual do SAP ASCS/SCS (endereço IP adicional do Azure Load Balancer) | 10.0.0.50 |
| Número da instância do SAP ASCS/SCS | 50 |
| Porta de investigação ILB para uma instância do SAP ASCS/SCS adicional | 62350 |

> [!NOTE]
> Para instâncias de cluster SAP ASCS/SCS, cada endereço IP exige uma porta de investigação única. Por exemplo, se um endereço IP em um balanceador de carga interno do Azure usar a porta de investigação 62300, nenhum outro endereço IP nesse balanceador de carga poderá usar a porta de investigação 62300.
>
>Para nosso fim, como a porta de investigação 62300 já está reservada, estamos usando a porta de investigação 62350.

É possível instalar instâncias do SAP ASCS/SCS adicionais no cluster WSFC existente com dois nós:

| Função da máquina virtual | Nome do host de máquina virtual | Endereço IP estático |
| --- | --- | --- |
| 1º nó de cluster para a instância do ASCS/SCS |pr1-ascs-0 |10.0.0.10 |
| 2º nó de cluster para a instância do ASCS/SCS |pr1-ascs-1 |10.0.0.9 |

### <a name="create-a-virtual-host-name-for-the-clustered-sap-ascsscs-instance-on-the-dns-server"></a>Criar um nome de host virtual para a instância do SAP ASCS/SCS clusterizada no servidor DNS

É possível criar uma entrada DNS para o nome de host virtual da instância do ASCS/SCS usando os seguintes parâmetros:

| Novo nome de host virtual do SAP ASCS/SCS | Endereço IP associado |
| --- | --- |
|pr5-sap-cl |10.0.0.50 |

O novo nome de host e endereço IP são exibidos no Gerenciador DNS, conforme mostrado na seguinte captura de tela:

![Lista do Gerenciador DNS que destaca a entrada DNS definida para o nome virtual do novo cluster SAP ASCS/SCS e para o endereço TCP/IP][sap-ha-guide-figure-6004]

O procedimento para criar uma entrada DNS também é descrito em detalhes no principal [guia para alta disponibilidade do SAP NetWeaver em VMs do Windows][sap-ha-guide-9.1.1].

> [!NOTE]
> O novo endereço IP atribuído ao nome de host virtual da instância do ASCS/SCS adicional deve ser igual ao novo endereço IP atribuído ao Azure Load Balancer do SAP.
>
>Em nosso cenário, o endereço IP é 10.0.0.50.

### <a name="add-an-ip-address-to-an-existing-azure-internal-load-balancer-by-using-powershell"></a>Adicionar um endereço IP a um balanceador de carga interno do Azure existente usando o PowerShell

Para criar mais de uma instância do SAP ASCS/SCS no mesmo cluster WSFC, use o PowerShell para adicionar um endereço IP a um balanceador de carga interno do Azure existente. Cada endereço IP requer suas próprias regras de balanceamento de carga, porta de investigação, pool IP de front-end e pool de back-end.

O script a seguir adiciona um novo endereço IP a um balanceador de carga existente. Atualize as variáveis do PowerShell para seu ambiente. O script criará todas as regras de balanceamento de carga necessárias para todas as portas do SAP ASCS /SCS.

```powershell

# Select-AzSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>
Clear-Host
$ResourceGroupName = "SAP-MULTI-SID-Landscape"      # Existing resource group name
$VNetName = "pr2-vnet"                        # Existing virtual network name
$SubnetName = "Subnet"                        # Existing subnet name
$ILBName = "pr2-lb-ascs"                      # Existing ILB name                      
$ILBIP = "10.0.0.50"                          # New IP address
$VMNames = "pr2-ascs-0","pr2-ascs-1"          # Existing cluster virtual machine names
$SAPInstanceNumber = 50                       # SAP ASCS/SCS instance number: must be a unique value for each cluster
[int]$ProbePort = "623$SAPInstanceNumber"     # Probe port: must be a unique value for each IP and load balancer

$ILB = Get-AzLoadBalancer -Name $ILBName -ResourceGroupName $ResourceGroupName

$count = $ILB.FrontendIpConfigurations.Count + 1
$FrontEndConfigurationName ="lbFrontendASCS$count"
$LBProbeName = "lbProbeASCS$count"

# Get the Azure VNet and subnet
$VNet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName
$Subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName

# Add second front-end and probe configuration
Write-Host "Adding new front end IP Pool '$FrontEndConfigurationName' ..." -ForegroundColor Green
$ILB | Add-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.Id
$ILB | Add-AzLoadBalancerProbeConfig -Name $LBProbeName  -Protocol Tcp -Port $Probeport -ProbeCount 2 -IntervalInSeconds 10  | Set-AzLoadBalancer

# Get new updated configuration
$ILB = Get-AzLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName
# Get new updated LP FrontendIP COnfig
$FEConfig = Get-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB
$HealthProbe  = Get-AzLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

# Add new back-end configuration into existing ILB
$BackEndConfigurationName  = "backendPoolASCS$count"
Write-Host "Adding new backend Pool '$BackEndConfigurationName' ..." -ForegroundColor Green
$BEConfig = Add-AzLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB | Set-AzLoadBalancer

# Get new updated config
$ILB = Get-AzLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName

# Assign VM NICs to backend pool
$BEPool = Get-AzLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB
foreach($VMName in $VMNames){
        $VM = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $VMName
        $NICName = ($VM.NetworkInterfaceIDs[0].Split('/') | select -last 1)        
        $NIC = Get-AzNetworkInterface -name $NICName -ResourceGroupName $ResourceGroupName                
        $NIC.IpConfigurations[0].LoadBalancerBackendAddressPools += $BEPool
        Write-Host "Assigning network card '$NICName' of the '$VMName' VM to the backend pool '$BackEndConfigurationName' ..." -ForegroundColor Green
        Set-AzNetworkInterface -NetworkInterface $NIC
        #start-AzVM -ResourceGroupName $ResourceGroupName -Name $VM.Name
}


# Create load-balancing rules
$Ports = "445","32$SAPInstanceNumber","33$SAPInstanceNumber","36$SAPInstanceNumber","39$SAPInstanceNumber","5985","81$SAPInstanceNumber","5$SAPInstanceNumber`13","5$SAPInstanceNumber`14","5$SAPInstanceNumber`16"
$ILB = Get-AzLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName
$FEConfig = get-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB
$BEConfig = Get-AzLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB
$HealthProbe  = Get-AzLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

Write-Host "Creating load balancing rules for the ports: '$Ports' ... " -ForegroundColor Green

foreach ($Port in $Ports) {

        $LBConfigrulename = "lbrule$Port" + "_$count"
        Write-Host "Creating load balancing rule '$LBConfigrulename' for the port '$Port' ..." -ForegroundColor Green

        $ILB | Add-AzLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig  -BackendAddressPool $BEConfig -Probe $HealthProbe -Protocol tcp -FrontendPort  $Port -BackendPort $Port -IdleTimeoutInMinutes 30 -LoadDistribution Default -EnableFloatingIP   
}

$ILB | Set-AzLoadBalancer

Write-Host "Successfully added new IP '$ILBIP' to the internal load balancer '$ILBName'!" -ForegroundColor Green

```
Depois que o script tiver sido executado, os resultados serão exibidos no portal do Azure, conforme mostrado na seguinte captura de tela:

![Novo pool IP front-end no portal do Azure][sap-ha-guide-figure-6005]

### <a name="add-disks-to-cluster-machines-and-configure-the-sios-cluster-share-disk"></a>Adicionar discos a máquinas de cluster e configurar o disco de compartilhamento do cluster SIOS

É necessário adicionar um novo disco de compartilhamento de cluster para cada nova instância do SAP ASCS/SCS adicional. Para o Windows Server 2012 R2, o disco de compartilhamento do cluster WSFC atualmente em uso é a solução de software SIOS DataKeeper.

Faça o seguinte:
1. Inclua discos adicionais com o mesmo tamanho (que você precisa distribuir) para cada um dos nós de cluster e formate-os.
2. Configure a replicação de armazenamento com SIOS DataKeeper.

Este procedimento pressupõe que você já tenha instalado o SIOS DataKeeper em computadores do cluster WSFC. Se você o tiver instalado, agora será necessário configurar a replicação entre os computadores. O processo é descrito em detalhes no principal [guia para alta disponibilidade do SAP NetWeaver em VMs do Windows][sap-ha-guide-8.12.3.3].  

![Espelhamento síncrono do DataKeeper para o novo disco de compartilhamento do SAP ASCS/SCS][sap-ha-guide-figure-6006]

### <a name="deploy-vms-for-sap-application-servers-and-dbms-cluster"></a>Implantar VMs em servidores de aplicativos SAP e cluster DBMS

Para concluir a preparação da infraestrutura para o segundo sistema SAP, faça o seguinte:

1. Implante VMs dedicadas para servidores de aplicativos SAP e coloque-as no seu próprio grupo de disponibilidade dedicado.
2. Implante VMs dedicadas para o cluster DBMS e coloque-as no seu próprio grupo de disponibilidade dedicado.


## <a name="install-the-second-sap-sid2-netweaver-system"></a>Instalar o segundo sistema SAP SID2 NetWeaver

O processo completo de instalação de um segundo sistema SAP SID2 é descrito no principal [guia para alta disponibilidade do SAP NetWeaver em VMs do Windows][sap-ha-guide-9].

O procedimento de alto nível é o seguinte:

1. [Instalar o primeiro nó de cluster do SAP][sap-ha-guide-9.1.2].  
 Nesta etapa, instale o SAP com uma instância do ASCS/SCS de alta disponibilidade no **nó de cluster WSFC EXISTENTE 1**.

2. [Modificar o perfil SAP da instância do ASCS/SCS][sap-ha-guide-9.1.3].

3. [Configurar uma porta de investigação][sap-ha-guide-9.1.4].  
 Nesta etapa, você configurará uma porta de investigação SAP-SID2-IP do recurso do cluster SAP usando o PowerShell. Execute essa configuração em um dos nós do cluster SAP ASCS/SCS.

4. [Instale a instância do banco de dados][sap-ha-guide-9.2].  
 Nesta etapa, você instalará o DBMS em um cluster WSFC dedicado.

5. [Instale o segundo nó de cluster][sap-ha-guide-9.3].  
 Nesta etapa, instale o SAP com uma instância do ASCS/SCS de alta disponibilidade no nó de cluster WSFC existente 2.

6. Abra portas do Firewall do Windows para a instância do SAP ASCS /SCS e ProbePort.  
 Em ambos os nós de cluster usados para as instâncias do SAP ASCS/SCS, abra todas as portas do Firewall do Windows usadas pelo SAP ASCS/SCS. Essas portas são listadas na [guia para alta disponibilidade do SAP NetWeaver em VMs do Windows][sap-ha-guide-8.8].  
 Além disso, abra a porta de investigação do balanceador de carga interno do Azure, que, neste cenário, é 62350.

7. [Alterar o tipo de inicialização da instância do serviço Windows do SAP ERS][sap-ha-guide-9.4].

8. [Instalar o servidor de aplicativos primário SAP][sap-ha-guide-9.5] na nova VM dedicada.

9. [Instalar o servidor de aplicativos adicional SAP][sap-ha-guide-9.6] na nova VM dedicada.

10. [Testar o failover da instância SAP ASCS/SCS e a replicação do SIOS][sap-ha-guide-10].

## <a name="next-steps"></a>Próximas etapas

- [Limites de rede: Azure Resource Manager][networking-limits-azure-resource-manager]
- [Múltiplos VIPs para o Azure Load Balancer][load-balancer-multivip-overview]
- [Guia de alta disponibilidade do SAP NetWeaver em VMs do Windows][sap-ha-guide]
