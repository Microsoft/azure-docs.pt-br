---
title: 'Início Rápido: criar um balanceador de carga Básico – Azure PowerShell'
titlesuffix: Azure Load Balancer
description: Este início rápido mostra como criar um Balanceador de Carga Básico usando o PowerShell
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: I want to create a Basic Load balancer so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2019
ms.author: allensu
ms:custom: seodec18
ms.openlocfilehash: 58b36265a5e440dbf33a5d6fb85e791abbd006a8
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68274239"
---
# <a name="get-started"></a>Início Rápido: criar um balanceador de carga público usando o Azure PowerShell

Este início rápido mostra como criar um Balanceador de Carga Básico usando o Azure PowerShell. Para testar o balanceador de carga, implante duas VMs (máquinas virtuais) que executam o Windows Server e balanceie a carga de um aplicativo Web entre as VMs.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar o PowerShell localmente, este artigo exigirá o módulo do Azure PowerShell versão 5.4.1 ou posterior. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-Az-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Connect-AzAccount` para criar uma conexão com o Azure.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Antes de criar o balanceador de carga, é necessário criar um grupo de recursos com [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). O seguinte exemplo cria um grupo de recursos chamado *myResourceGroupLB* no local *EastUS*:

```azurepowershell-interactive
New-AzResourceGroup `
  -ResourceGroupName "myResourceGroupLB" `
  -Location "EastUS"
```

## <a name="create-a-public-ip-address"></a>Criar um endereço IP público
Para acessar seu aplicativo na Internet, você precisará de um endereço IP público para o balanceador de carga. Crie um endereço IP público com [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress). O seguinte exemplo cria um endereço IP público chamado *myPublicIP* no grupo de recursos *myResourceGroupLB*:

```azurepowershell-interactive
$publicIP = New-AzPublicIpAddress `
  -ResourceGroupName "myResourceGroupLB" `
  -Location "EastUS" `
  -AllocationMethod "Static" `
  -Name "myPublicIP"
```

## <a name="create-basic-load-balancer"></a>Criar o balanceador de carga básico

Nesta seção, você configura o IP de front-end e o pool de endereços de back-end para o balanceador de carga e, em seguida, cria o Balanceador de Carga Básico.

### <a name="create-front-end-ip"></a>Criar IP de front-end

Crie um IP de front-end com [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig). O seguinte exemplo cria uma configuração de IP de front-end chamada *myFrontEnd* e anexa o endereço *myPublicIP*:

```azurepowershell-interactive
$frontendIP = New-AzLoadBalancerFrontendIpConfig `
  -Name "myFrontEnd" `
  -PublicIpAddress $publicIP
```

### <a name="configure-back-end-address-pool"></a>Configurar o pool de endereços de back-end

Crie um pool de endereços de back-end com [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig). As VMs são anexadas a este pool de back-end nas etapas restantes. O seguinte exemplo cria um pool de endereços de back-end chamado *myBackEndPool*:

```azurepowershell-interactive
$backendPool = New-AzLoadBalancerBackendAddressPoolConfig -Name "myBackEndPool"
```

### <a name="create-a-health-probe"></a>Criar uma investigação de integridade
Para permitir que o balanceador de carga monitore o status de seu aplicativo, use uma investigação de integridade. A investigação de integridade adiciona ou remove dinamicamente VMs da rotação do balanceador de carga com base na resposta às verificações de integridade. Por padrão, uma VM é removida da distribuição do balanceador de carga após duas falhas consecutivas em intervalos de 15 segundos. Crie uma investigação de integridade com base em um protocolo ou página de verificação de integridade específica ao seu aplicativo. 

O exemplo a seguir cria uma investigação de TCP. Você também pode criar investigações de HTTP personalizadas para obter verificações de integridade mais refinadas. Ao usar uma investigação de HTTP personalizada, você deverá criar a página de verificação de integridade, como *healthcheck.aspx*. A investigação deve retornar a reposta **HTTP 200 OK** para o balanceador de carga a fim de manter o host em rotação.

Para criar uma investigação de integridade TCP, use [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig). O seguinte exemplo cria uma investigação de integridade chamada *myHealthProbe* que monitora cada VM na porta *HTTP* *80*:

```azurepowershell-interactive
$probe = New-AzLoadBalancerProbeConfig `
  -Name "myHealthProbe" `
  -RequestPath healthcheck2.aspx `
  -Protocol http `
  -Port 80 `
  -IntervalInSeconds 16 `
  -ProbeCount 2
  ```

### <a name="create-a-load-balancer-rule"></a>Criar uma regra de balanceador de carga

Uma regra de balanceador de carga é usada para definir como o tráfego é distribuído para as VMs. Defina a configuração de IP de front-end para o tráfego de entrada e o pool de IPs de back-end para receber o tráfego, junto com as portas de origem e de destino necessárias. Para ter certeza de que apenas VMs íntegras recebem tráfego, defina também a investigação de integridade a ser usada.

Crie uma regra de balanceador de carga com [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig). O seguinte exemplo cria uma regra de balanceador de carga chamada *myLoadBalancerRule* e faz o balanceamento de carga do tráfego na porta *TCP* *80*:

```azurepowershell-interactive
$lbrule = New-AzLoadBalancerRuleConfig `
  -Name "myLoadBalancerRule" `
  -FrontendIpConfiguration $frontendIP `
  -BackendAddressPool $backendPool `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80 `
  -Probe $probe
```

### <a name="create-the-nat-rules"></a>Criar as regras NAT

Crie regras NAT com [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig). O seguinte exemplo cria regras NAT chamadas *myLoadBalancerRDP1* e *myLoadBalancerRDP2* para permitir conexões RDP aos servidores back-end com as portas 4221 e 4222:

```azurepowershell-interactive
$natrule1 = New-AzLoadBalancerInboundNatRuleConfig `
-Name 'myLoadBalancerRDP1' `
-FrontendIpConfiguration $frontendIP `
-Protocol tcp `
-FrontendPort 4221 `
-BackendPort 3389

$natrule2 = New-AzLoadBalancerInboundNatRuleConfig `
-Name 'myLoadBalancerRDP2' `
-FrontendIpConfiguration $frontendIP `
-Protocol tcp `
-FrontendPort 4222 `
-BackendPort 3389
```

### <a name="create-load-balancer"></a>Criar um balanceador de carga

Crie o Balanceador de Carga Básico com [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer). O seguinte exemplo cria um Balanceador de Carga Básico público chamado myLoadBalancer usando a configuração de IP de front-end, o pool de back-end, a investigação de integridade, a regra de balanceamento de carga e as regras NAT criadas nas etapas anteriores:

```azurepowershell-interactive
$lb = New-AzLoadBalancer `
-ResourceGroupName 'myResourceGroupLB' `
-Name 'MyLoadBalancer' `
-Location 'eastus' `
-FrontendIpConfiguration $frontendIP `
-BackendAddressPool $backendPool `
-Probe $probe `
-LoadBalancingRule $lbrule `
-InboundNatRule $natrule1,$natrule2
```

## <a name="create-network-resources"></a>Criar recursos da rede

Antes de implantar algumas VMs e testar o balanceador, você deve criar recursos de rede de suporte – rede virtual e NICs virtuais. 

### <a name="create-a-virtual-network"></a>Criar uma rede virtual

Crie uma rede virtual com [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). O exemplo a seguir cria uma rede virtual chamada *myVnet* com uma sub-rede chamada *mySubnet*:

```azurepowershell-interactive
# Create subnet config
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name "mySubnet" `
  -AddressPrefix 10.0.2.0/24

# Create the virtual network
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName "myResourceGroupLB" `
  -Location "EastUS" `
  -Name "myVnet" `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $subnetConfig
```

### <a name="create-network-security-group"></a>Criar um grupo de segurança de rede

Crie um grupo de segurança de rede para definir conexões de entrada para sua rede virtual.

#### <a name="create-a-network-security-group-rule-for-port-3389"></a>Criar uma regra do grupo de segurança de rede para a porta 3389

Crie uma regra do grupo de segurança de rede para permitir conexões RDP pela porta 3389 com [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig).

```azurepowershell-interactive
$rule1 = New-AzNetworkSecurityRuleConfig `
-Name 'myNetworkSecurityGroupRuleRDP' `
-Description 'Allow RDP' `
-Access Allow `
-Protocol Tcp `
-Direction Inbound `
-Priority 1000 `
-SourceAddressPrefix Internet `
-SourcePortRange * `
-DestinationAddressPrefix * `
-DestinationPortRange 3389
```

#### <a name="create-a-network-security-group-rule-for-port-80"></a>Criar uma regra do grupo de segurança de rede para a porta 80

Crie uma regra do grupo de segurança de rede para permitir conexões de entrada pela porta 80 com [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig).

```azurepowershell-interactive
$rule2 = New-AzNetworkSecurityRuleConfig `
-Name 'myNetworkSecurityGroupRuleHTTP' `
-Description 'Allow HTTP' `
-Access Allow `
-Protocol Tcp `
-Direction Inbound `
-Priority 2000 `
-SourceAddressPrefix Internet `
-SourcePortRange * `
-DestinationAddressPrefix * `
-DestinationPortRange 80
```

#### <a name="create-a-network-security-group"></a>Criar um grupo de segurança de rede

Crie um grupo de segurança de rede com [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup).

```azurepowershell-interactive
$nsg = New-AzNetworkSecurityGroup `
-ResourceGroupName 'myResourceGroupLB' `
-Location 'EastUS' `
-Name 'myNetworkSecurityGroup' `
-SecurityRules $rule1,$rule2
```

### <a name="create-nics"></a>Criar NICs

Crie as NICs virtuais com [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface). O exemplo a seguir cria duas NICs virtuais. (Uma NIC virtual para cada VM criada para seu aplicativo nas etapas a seguir). Você pode criar VMs e NICs virtuais adicionais a qualquer momento e adicioná-las ao balanceador de carga:

```azurepowershell-interactive
# Create NIC for VM1
$nicVM1 = New-AzNetworkInterface `
-ResourceGroupName 'myResourceGroupLB' `
-Location 'EastUS' `
-Name 'MyVM1' `
-LoadBalancerBackendAddressPool $backendPool `
-NetworkSecurityGroup $nsg `
-LoadBalancerInboundNatRule $natrule1 `
-Subnet $vnet.Subnets[0]

# Create NIC for VM2
$nicVM2 = New-AzNetworkInterface `
-ResourceGroupName 'myResourceGroupLB' `
-Location 'EastUS' `
-Name 'MyVM2' `
-LoadBalancerBackendAddressPool $backendPool `
-NetworkSecurityGroup $nsg `
-LoadBalancerInboundNatRule $natrule2 `
-Subnet $vnet.Subnets[0]
```

### <a name="create-virtual-machines"></a>Criar máquinas virtuais

Para melhorar a alta disponibilidade do seu aplicativo, coloque suas VMs em um conjunto de disponibilidade.

Crie um conjunto de disponibilidade com [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset). O exemplo a seguir cria um conjunto de disponibilidade chamado *myAvailabilitySet*:

```azurepowershell-interactive
$availabilitySet = New-AzAvailabilitySet `
  -ResourceGroupName "myResourceGroupLB" `
  -Name "myAvailabilitySet" `
  -Location "EastUS" `
  -Sku aligned `
  -PlatformFaultDomainCount 2 `
  -PlatformUpdateDomainCount 2
```

Defina o nome de usuário e a senha de um administrador para as VMs com [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Agora, é possível criar as VMs com [New-AzVM](/powershell/module/az.compute/new-azvm). O exemplo a seguir cria duas VMs e os componentes de rede virtual necessários, caso ainda não existam. Neste exemplo, as NICs (*VM1* e *VM2*) criadas na etapa anterior são automaticamente atribuídas às máquinas virtuais *VM1* e *VM2*, já elas que têm nomes idênticos e a mesma rede virtual (*myVnet*) e sub-rede (*mySubnet*) são atribuídas a elas. Além disso, já que as NICs estão associadas ao pool de back-end do balanceador de carga, as VMs são automaticamente adicionadas ao pool de back-end.

```azurepowershell-interactive
for ($i=1; $i -le 2; $i++)
{
    New-AzVm `
        -ResourceGroupName "myResourceGroupLB" `
        -Name "myVM$i" `
        -Location "East US" `
        -VirtualNetworkName "myVnet" `
        -SubnetName "mySubnet" `
        -SecurityGroupName "myNetworkSecurityGroup" `
        -OpenPorts 80 `
        -AvailabilitySetName "myAvailabilitySet" `
        -Credential $cred `
        -AsJob
}
```

O parâmetro `-AsJob` cria a VM como uma tarefa em segundo plano, para que os prompts do PowerShell sejam exibidos de volta para você. Você pode exibir os detalhes de trabalhos em segundo plano com o cmdelt `Job`. São necessários alguns minutos para criar e configurar as duas VMs.

### <a name="install-iis-with-custom-web-page"></a>Instalar o IIS com uma página da Web Personalizada
 
Instale o IIS com uma página da Web personalizada em ambas as VMs de back-end da seguinte maneira:

1. Obtenha o endereço IP Público do Balanceador de Carga. Usando `Get-AzPublicIPAddress`, obtenha o endereço IP Público do Balanceador de Carga.

   ```azurepowershell-interactive
    Get-AzPublicIPAddress `
    -ResourceGroupName "myResourceGroupLB" `
    -Name "myPublicIP" | select IpAddress
   ```
2. Crie uma conexão de área de trabalho remota com VM1 usando o endereço IP Público obtido na etapa anterior. 

   ```azurepowershell-interactive

      mstsc /v:PublicIpAddress:4221  
  
   ```
3. Insira as credenciais para *VM1* para iniciar a sessão do RDP.
4. Inicie o Windows PowerShell na VM1 e usando os comandos a seguir para instalar o servidor do IIS e atualizar o arquivo htm padrão.
    ```azurepowershell-interactive
    # Install IIS
      Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello from" + $env:computername)
    ```
5. Feche a conexão RDP com *myVM1*.
6. Crie uma conexão RDP com *myVM2* executando o comando `mstsc /v:PublicIpAddress:4222` e repita a etapa 4 para *VM2*.

## <a name="test-load-balancer"></a>Testar o balanceador de carga
Obtenha o endereço IP público do balanceador de carga com [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress). O exemplo a seguir obtém o endereço IP para *myPublicIP* criado anteriormente:

```azurepowershell-interactive
Get-AzPublicIPAddress `
  -ResourceGroupName "myResourceGroupLB" `
  -Name "myPublicIP" | select IpAddress
```

Você pode inserir o endereço IP público em um navegador da Web. O site é exibido, incluindo o nome do host da VM para a qual o balanceador de carga distribui o tráfego como no exemplo a seguir:

![Testar o balanceador de carga](media/quickstart-create-basic-load-balancer-powershell/load-balancer-test.png)

Para ver o balanceador de carga distribuir o tráfego entre ambas as VMs que executam o aplicativo, você poderá forçar a atualização do navegador da Web.

## <a name="clean-up-resources"></a>Limpar recursos

Quando não forem mais necessários, você poderá usar o comando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos, a VM e todos os recursos relacionados.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupLB
```

## <a name="next-steps"></a>Próximas etapas

Neste início rápido você criou o Load Balancer Básico, anexou VMs, configurou a regra de tráfego do balanceador de carga, investigação de integridade e em seguida, testou o balanceador de carga. Para saber mais sobre o Azure Load Balancer, continue nos tutoriais do Azure Load Balancer.

> [!div class="nextstepaction"]
> [Tutoriais do Azure Load Balancer](tutorial-load-balancer-basic-internal-portal.md)