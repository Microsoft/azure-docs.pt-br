---
title: Configurar ouvintes de grupo de disponibilidade e balanceador de carga (PowerShell)
description: Configure os ouvintes do grupo de disponibilidade no modelo de Azure Resource Manager, usando um balanceador de carga interno com um ou mais endereços IP.
services: virtual-machines
documentationcenter: na
author: MashaMSFT
editor: monicar
ms.assetid: 14b39cde-311c-4ddf-98f3-8694e01a7d3b
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/06/2019
ms.author: mathoma
ms.custom: seo-lt-2019
ms.openlocfilehash: 9337d1c2767923e6dc7c6b267e0c180b460a116e
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2020
ms.locfileid: "97359414"
---
# <a name="configure-one-or-more-always-on-availability-group-listeners---resource-manager"></a>Configurar um ou mais ouvintes de grupo de disponibilidade AlwaysOn – Resource Manager

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Este documento mostra como usar o PowerShell para executar uma das seguintes tarefas:
- criar um balanceador de carga
- Adicione endereços IP a um balanceador de carga existente para SQL Server grupos de disponibilidade.

Um ouvinte de grupo de disponibilidade é um nome de rede virtual à qual os clientes se conectam para acessar o banco de dados. Em máquinas virtuais do Azure, um balanceador de carga mantém o endereço IP para o ouvinte. O balanceador de carga encaminha o tráfego para a instância do SQL Server que está escutando na porta de investigação. Normalmente, um grupo de disponibilidade usa um balanceador de carga interno. Um balanceador de carga interno do Azure pode hospedar um ou vários endereços IP. Cada endereço IP usa uma porta de investigação específica. 

A capacidade de atribuir vários endereços IP a um balanceador de carga interno é nova no Azure e só está disponível no modelo do Resource Manager. Para concluir essa tarefa, você precisa ter um grupo de disponibilidade SQL Server implantado em máquinas virtuais do Azure no modelo do Resource Manager. As máquinas virtuais do SQL Server devem pertencer ao mesmo conjunto de disponibilidade. Você pode usar o [modelo da Microsoft](./availability-group-quickstart-template-configure.md) para criar automaticamente o grupo de disponibilidade no Azure Resource Manager. Este modelo cria automaticamente o grupo de disponibilidade, incluindo o balanceador de carga interno para você. Se preferir, você poderá [configurar manualmente um grupo de disponibilidade AlwaysOn](availability-group-manually-configure-tutorial.md).

Para concluir as etapas neste artigo, seus grupos de disponibilidade precisam estar já configurados.  

Os tópicos relacionados incluem:

* [Configurar os Grupos de Disponibilidade AlwaysOn na VM do Azure (GUI)](availability-group-manually-configure-tutorial.md)   
* [Configurar uma conexão de rede virtual com rede virtual usando o PowerShell e o Azure Resource Manager](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

[!INCLUDE [Start your PowerShell session](../../../../includes/sql-vm-powershell.md)]

## <a name="verify-powershell-version"></a>Verificar a versão do PowerShell

Os exemplos neste artigo foram testados usando o módulo do Azure PowerShell versão 5.4.1.

Verifique se o módulo do PowerShell é 5.4.1 ou posterior.

Confira [Instalar o módulo do Azure PowerShell](/powershell/azure/install-az-ps).

## <a name="configure-the-windows-firewall"></a>Configurar o Firewall do Windows

Configure o Firewall do Windows para permitir acesso ao SQL Server. As regras de firewall permitem conexões TCP com as portas por instância do SQL Server, bem como a investigação do ouvinte. Para obter instruções detalhadas, confira [Configurar um Firewall do Windows para acesso ao Mecanismo de Banco de Dados](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access#Anchor_1). Crie uma regra de entrada para a porta do SQL Server e para a porta de investigação.

Se você estiver restringindo o acesso a um Grupo de Segurança de Rede do Azure, verifique se as regras de permissão incluem os endereços IP da VM do SQL Server de back-end e os endereços IP flutuantes do balanceador de carga para o ouvinte da AG e o endereço IP do núcleo do cluster, se aplicável.

## <a name="determine-the-load-balancer-sku-required"></a>Determinar o SKU do balanceador de carga necessário

O [Azure Load Balancer](../../../load-balancer/load-balancer-overview.md) está disponível em duas SKUs: Basic & Standard. O Standard Load Balancer é recomendado. Se as máquinas virtuais estiverem em um conjunto de disponibilidade, o balanceador de carga básico será permitido. Se as máquinas virtuais estiverem em uma zona de disponibilidade, um Standard Load Balancer será necessário. O Standard Load Balancer exige que todos os endereços IP da VM usem endereços IP padrão.

O atual [modelo da Microsoft](./availability-group-quickstart-template-configure.md) para um grupo de disponibilidade usa um balanceador de carga básico com endereços IP básicos.

   > [!NOTE]
   > Você precisará configurar um [ponto de extremidade de serviço](../../../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network) se usar um Standard Load Balancer e o Armazenamento do Azure para a testemunha de nuvem. 
   > 

Os exemplos neste artigo especificam um Standard Load Balancer. Nos exemplos, o script inclui `-sku Standard`.

```powershell
$ILB= New-AzLoadBalancer -Location $Location -Name $ILBName -ResourceGroupName $ResourceGroupName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -LoadBalancingRule $ILBRule -Probe $SQLHealthProbe -sku Standard
```

Para criar um balanceador de carga básico, remova `-sku Standard` da linha que cria o balanceador de carga. Por exemplo:

```powershell
$ILB= New-AzLoadBalancer -Location $Location -Name $ILBName -ResourceGroupName $ResourceGroupName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -LoadBalancingRule $ILBRule -Probe $SQLHealthProbe
```

## <a name="example-script-create-an-internal-load-balancer-with-powershell"></a>Script de exemplo: criar um balanceador de carga interno com o PowerShell

> [!NOTE]
> Se você criou o grupo de disponibilidade com o [modelo Microsoft](./availability-group-quickstart-template-configure.md), o balanceador de carga interno já foi criado.

O script do PowerShell a seguir cria um balanceador de carga interno, configura as regras de balanceamento de carga e define um endereço IP para o balanceador de carga. Para executar o script, abra ISE do Windows PowerShell e cole o script no painel de script. Use `Connect-AzAccount` para fazer logon no PowerShell. Se você tiver várias assinaturas do Azure, use `Select-AzSubscription` para definir a assinatura. 

```powershell
# Connect-AzAccount
# Select-AzSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>

$ResourceGroupName = "<Resource Group Name>" # Resource group name
$VNetName = "<Virtual Network Name>"         # Virtual network name
$SubnetName = "<Subnet Name>"                # Subnet name
$ILBName = "<Load Balancer Name>"            # ILB name
$Location = "<Azure Region>"                 # Azure location
$VMNames = "<VM1>","<VM2>"                   # Virtual machine names

$ILBIP = "<n.n.n.n>"                         # IP address
[int]$ListenerPort = "<nnnn>"                # AG listener port
[int]$ProbePort = "<nnnn>"                   # Probe port

$LBProbeName ="ILBPROBE_$ListenerPort"       # The Load balancer Probe Object Name              
$LBConfigRuleName = "ILBCR_$ListenerPort"    # The Load Balancer Rule Object Name

$FrontEndConfigurationName = "FE_SQLAGILB_1" # Object name for the front-end configuration 
$BackEndConfigurationName ="BE_SQLAGILB_1"   # Object name for the back-end configuration

$VNet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName 

$Subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName 

$FEConfig = New-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.id

$BEConfig = New-AzLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName 

$SQLHealthProbe = New-AzLoadBalancerProbeConfig -Name $LBProbeName -Protocol tcp -Port $ProbePort -IntervalInSeconds 15 -ProbeCount 2

$ILBRule = New-AzLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -Probe $SQLHealthProbe -Protocol tcp -FrontendPort $ListenerPort -BackendPort $ListenerPort -LoadDistribution Default -EnableFloatingIP 

$ILB= New-AzLoadBalancer -Location $Location -Name $ILBName -ResourceGroupName $ResourceGroupName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -LoadBalancingRule $ILBRule -Probe $SQLHealthProbe 

$bepool = Get-AzLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB 

foreach($VMName in $VMNames)
    {
        $VM = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $VMName 
        $NICName = ($vm.NetworkProfile.NetworkInterfaces.Id.split('/') | select -last 1)
        $NIC = Get-AzNetworkInterface -name $NICName -ResourceGroupName $ResourceGroupName
        $NIC.IpConfigurations[0].LoadBalancerBackendAddressPools = $BEPool
        Set-AzNetworkInterface -NetworkInterface $NIC
        start-AzVM -ResourceGroupName $ResourceGroupName -Name $VM.Name 
    }
```

## <a name="example-script-add-an-ip-address-to-an-existing-load-balancer-with-powershell"></a><a name="Add-IP"></a> Script de exemplo: adicionar um endereço IP a um balanceador de carga existente com o PowerShell

Para usar mais de um grupo de disponibilidade, inclua um endereço IP adicional ao balanceador de carga. Cada endereço IP requer sua própria regra de balanceamento de carga, porta de investigação e porta frontal.

A porta de front-end é a que os aplicativos usam para se conectar à instância do SQL Server. Endereços IP para grupos de disponibilidade diferentes podem usar a mesma porta de front-end.

> [!NOTE]
> Para grupos de disponibilidade do SQL Server, cada endereço IP exige uma porta de investigação específica. Por exemplo, se um endereço IP em um balanceador de carga usa a porta de investigação 59999, nenhum outro endereço IP nesse balanceador de carga pode usar essa porta de investigação.

* Para saber mais sobre os limites do balanceador de carga, confira **IP privado front-end por balanceador de carga** em [Limites de Rede – Azure Resource Manager](../../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits).
* Para saber mais sobre os limites de grupo de disponibilidade, confira [Restrições (Grupos de Disponibilidade)](/sql/database-engine/availability-groups/windows/prereqs-restrictions-recommendations-always-on-availability#RestrictionsAG).

O script a seguir adiciona um novo endereço IP a um balanceador de carga existente. O ILB usa a porta do ouvinte para a porta de front-end de balanceamento de carga. Essa porta pode ser aquela que o SQL Server está escutando. Para instâncias padrão do SQL Server, a porta é a 1433. A regra de balanceamento de carga para um grupo de disponibilidade requer um IP flutuante (retorno de servidor direto) para que a porta de back-end seja a mesma que a porta de front-end. Atualize as variáveis para o seu ambiente. 

```powershell
# Connect-AzAccount
# Select-AzSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>

$ResourceGroupName = "<ResourceGroup>"          # Resource group name
$VNetName = "<VirtualNetwork>"                  # Virtual network name
$SubnetName = "<Subnet>"                        # Subnet name
$ILBName = "<ILBName>"                          # ILB name                      

$ILBIP = "<n.n.n.n>"                            # IP address
[int]$ListenerPort = "<nnnn>"                   # AG listener port
[int]$ProbePort = "<nnnnn>"                     # Probe port 

$ILB = Get-AzLoadBalancer -Name $ILBName -ResourceGroupName $ResourceGroupName 

$count = $ILB.FrontendIpConfigurations.Count+1
$FrontEndConfigurationName ="FE_SQLAGILB_$count"  

$LBProbeName = "ILBPROBE_$count"
$LBConfigrulename = "ILBCR_$count"

$VNet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName 
$Subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName

$ILB | Add-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.Id 

$ILB | Add-AzLoadBalancerProbeConfig -Name $LBProbeName  -Protocol Tcp -Port $Probeport -ProbeCount 2 -IntervalInSeconds 15  | Set-AzLoadBalancer 

$ILB = Get-AzLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName

$FEConfig = get-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB

$SQLHealthProbe  = Get-AzLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

$BEConfig = Get-AzLoadBalancerBackendAddressPoolConfig -Name $ILB.BackendAddressPools[0].Name -LoadBalancer $ILB 

$ILB | Add-AzLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig  -BackendAddressPool $BEConfig -Probe $SQLHealthProbe -Protocol tcp -FrontendPort  $ListenerPort -BackendPort $ListenerPort -LoadDistribution Default -EnableFloatingIP | Set-AzLoadBalancer   
```

## <a name="configure-the-listener"></a>Configurar o ouvinte

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

## <a name="set-the-listener-port-in-sql-server-management-studio"></a>Definir a porta do ouvinte no SQL Server Management Studio

1. Inicie o SQL Server Management Studio e conecte-se à réplica principal.

1. Navegue até **Alta Disponibilidade do AlwaysOn** > **Grupos de Disponibilidade** > **Ouvintes do Grupo de Disponibilidade**. 

1. Agora você deve ver o nome do ouvinte que você criou no Gerenciador de Cluster de Failover. Clique com o botão direito do mouse no nome do ouvinte e selecione **Propriedades**.

1. Na caixa **porta** , especifique o número da porta para o ouvinte do grupo de disponibilidade usando o $EndpointPort usado anteriormente (1433 era o padrão) e selecione **OK**.

## <a name="test-the-connection-to-the-listener"></a>Testar a conexão com o ouvinte

Para testar a conexão:

1. Use protocolo RDP (RDP) para se conectar a um SQL Server que esteja na mesma rede virtual, mas que não possui a réplica. Pode ser o outro SQL Server no cluster.

1. Use o utilitário **sqlcmd** para testar a conexão. Por exemplo, o script a seguir estabelece uma conexão de **sqlcmd** com a réplica primária por meio do ouvinte com autenticação do Windows:
   
    ```
    sqlcmd -S <listenerName> -E
    ```
   
    Se o ouvinte estiver usando uma porta diferente da porta padrão (1433), especifique a porta na cadeia de conexão. Por exemplo, o comando sqlcmd a seguir conecta-se a um ouvinte na porta 1435: 
   
    ```
    sqlcmd -S <listenerName>,1435 -E
    ```

A conexão SQLCMD se conecta automaticamente a qualquer instância do SQL Server que hospede a réplica primária. 

> [!NOTE]
> Verifique se a porta especificada está aberta no firewall dos servidores SQL. Ambos os servidores exigem uma regra de entrada para a porta TCP que você usa. Para saber mais, confira [Adicionar ou Editar Regra de Firewall](/previous-versions/orphan-topics/ws.11/cc753558(v=ws.11)). 
> 

## <a name="guidelines-and-limitations"></a>Diretrizes e limitações

Observe as diretrizes a seguir no ouvinte do grupo de disponibilidade no Azure usando o balanceador de carga interno:

* Com um balanceador de carga interno, você só pode acessar ao ouvinte de dentro da mesma rede virtual.

* Se você estiver restringindo o acesso com um grupo de segurança de rede do Azure, verifique se as regras de permissão incluem:
  - O back-end SQL Server endereços IP da VM
  - Os endereços IP flutuantes do balanceador de carga para o ouvinte AG
  - O endereço IP do núcleo do cluster, se aplicável.

* Crie um ponto de extremidade de serviço ao usar um Standard Load Balancer com o Armazenamento do Azure para a testemunha de nuvem. Para obter mais informações, confira [Permitir acesso em uma rede virtual](../../../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network).

## <a name="for-more-information"></a>Para obter mais informações

Para saber mais, confira [Configure Always On availability group in Azure VM manually](availability-group-manually-configure-tutorial.md) (Configurar grupo de disponibilidade Always On na VM do Azure manualmente).

## <a name="powershell-cmdlets"></a>Cmdlets do PowerShell

Use os seguintes cmdlets do PowerShell para criar um balanceador de carga interno para máquinas virtuais do Azure.

* [New-AzLoadBalancer](/powershell/module/Azurerm.Network/New-AzureRmLoadBalancer) cria um balanceador de carga. 
* [New-AzLoadBalancerFrontendIpConfig](/powershell/module/Azurerm.Network/New-AzureRmLoadBalancerFrontendIpConfig) cria uma configuração de IP de front-end para um balanceador de carga. 
* [New-AzLoadBalancerRuleConfig](/powershell/module/Azurerm.Network/New-AzureRmLoadBalancerRuleConfig) cria uma configuração de regra para um balanceador de carga. 
* [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/Azurerm.Network/New-AzureRmLoadBalancerBackendAddressPoolConfig) cria uma configuração de pool de endereços de back-end para um balanceador de carga. 
* [New-AzLoadBalancerProbeConfig](/powershell/module/Azurerm.Network/New-AzureRmLoadBalancerProbeConfig) cria uma configuração de investigação para um balanceador de carga.
* [Remove-AzLoadBalancer](/powershell/module/Azurerm.Network/Remove-AzureRmLoadBalancer) remove um balanceador de carga de um grupo de recursos do Azure.