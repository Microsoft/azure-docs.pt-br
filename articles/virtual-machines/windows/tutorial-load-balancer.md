---
title: Tutorial – Balancear a carga de máquinas virtuais do Windows no Azure | Microsoft Docs
description: Neste tutorial, você aprenderá a usar o Azure PowerShell para criar um balanceador de carga para um aplicativo altamente disponível e seguro em três máquinas virtuais do Windows
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/03/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 5ca8f07ac1cc4296376904a2ba5ca85ff479a72c
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67708009"
---
# <a name="tutorial-load-balance-windows-virtual-machines-in-azure-to-create-a-highly-available-application-with-azure-powershell"></a>Tutorial: Balancear a carga de máquinas virtuais do Windows no Azure para criar um aplicativo altamente disponível com o Azure PowerShell
O balanceamento de carga fornece um nível mais alto de disponibilidade, distribuindo as solicitações de entrada em várias máquinas virtuais. Neste tutorial, você aprenderá sobre os diferentes componentes do balanceador de carga do Azure que distribuem o tráfego e fornecem alta disponibilidade. Você aprenderá como:

> [!div class="checklist"]
> * Criar um balanceador de carga do Azure
> * Criar uma investigação de integridade do balanceador de carga
> * Criar regras de tráfego para o balanceador de carga
> * Usar a Extensão de Script Personalizado para criar um site do IIS básico
> * Criar máquinas virtuais e anexar a um balanceador de carga
> * Exibir um balanceador de carga em ação
> * Adicionar e remover as VMs de um balanceador de carga

## <a name="azure-load-balancer-overview"></a>Visão geral do Balanceador de Carga do Azure
Um Azure Load Balancer é um balanceador de carga de Camada 4 (TCP, UDP) que fornece alta disponibilidade distribuindo o tráfego de entrada entre VMs íntegros. Uma investigação de integridade do balanceador de carga monitora uma determinada porta em cada VM e distribui o tráfego somente para uma VM operacional.

Você define uma configuração de IP de front-end que contém um ou mais endereços IP públicos. Essa configuração de IP de front-end permite que seu balanceador de carga e os aplicativos estejam acessíveis pela Internet. 

As máquinas virtuais conectam-se a um balanceador de carga usando a placa de interface de rede virtual (NIC). Para distribuir o tráfego para as máquinas virtuais, um pool de endereços de back-end contém os endereços IP das NICs virtuais conectadas ao balanceador de carga.

Para controlar o fluxo do tráfego, você precisará definir regras do balanceador de carga para portas específicas e protocolos que mapeiam para suas VMs.

## <a name="launch-azure-cloud-shell"></a>Iniciar o Azure Cloud Shell

O Azure Cloud Shell é um shell interativo grátis que pode ser usado para executar as etapas neste artigo. Ele tem ferramentas do Azure instaladas e configuradas para usar com sua conta. 

Para abrir o Cloud Shell, basta selecionar **Experimentar** no canto superior direito de um bloco de código. Você também pode iniciar o Cloud Shell em uma guia separada do navegador indo até [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Selecione **Copiar** para copiar os blocos de código, cole o código no Cloud Shell e depois pressione Enter para executá-lo.

## <a name="create-azure-load-balancer"></a>Criar o balanceador de carga do Azure
Esta seção fornece detalhes sobre como criar e configurar cada componente do balanceador de carga. Antes de criar o balanceador de carga, crie um grupo de recursos com [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup). O seguinte exemplo cria um grupo de recursos chamado *myResourceGroupLoadBalancer* no local *EastUS*:

```azurepowershell-interactive
New-AzResourceGroup `
  -ResourceGroupName "myResourceGroupLoadBalancer" `
  -Location "EastUS"
```

### <a name="create-a-public-ip-address"></a>Criar um endereço IP público
Para acessar seu aplicativo na Internet, você precisará de um endereço IP público para o balanceador de carga. Crie um endereço IP público com [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress). O exemplo a seguir cria um endereço IP público chamado *myPublicIP* no grupo de recursos *myResourceGroupLoadBalancer*:

```azurepowershell-interactive
$publicIP = New-AzPublicIpAddress `
  -ResourceGroupName "myResourceGroupLoadBalancer" `
  -Location "EastUS" `
  -AllocationMethod "Static" `
  -Name "myPublicIP"
```

### <a name="create-a-load-balancer"></a>Criar um balanceador de carga
Crie um pool de IPs de front-end com [New-AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig). O seguinte exemplo cria um pool de IPs de front-end chamado *myFrontEndPool* e anexa o endereço *myPublicIP*: 

```azurepowershell-interactive
$frontendIP = New-AzLoadBalancerFrontendIpConfig `
  -Name "myFrontEndPool" `
  -PublicIpAddress $publicIP
```

Crie um pool de endereços de back-end com [New-AzLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig). As VMs são anexadas a este pool de back-end nas etapas restantes. O seguinte exemplo cria um pool de endereços de back-end chamado *myBackEndPool*:

```azurepowershell-interactive
$backendPool = New-AzLoadBalancerBackendAddressPoolConfig `
  -Name "myBackEndPool"
```

Agora crie o balanceador de carga com [New-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancer). O exemplo a seguir cria um balanceador de carga denominado *myLoadBalancer* usando os pools de IP de front-end e back-end criados nas etapas anteriores:

```azurepowershell-interactive
$lb = New-AzLoadBalancer `
  -ResourceGroupName "myResourceGroupLoadBalancer" `
  -Name "myLoadBalancer" `
  -Location "EastUS" `
  -FrontendIpConfiguration $frontendIP `
  -BackendAddressPool $backendPool
```

### <a name="create-a-health-probe"></a>Criar uma investigação de integridade
Para permitir que o balanceador de carga monitore o status de seu aplicativo, use uma investigação de integridade. A investigação de integridade adiciona ou remove dinamicamente VMs da rotação do balanceador de carga com base na resposta às verificações de integridade. Por padrão, uma VM é removida da distribuição do balanceador de carga após duas falhas consecutivas em intervalos de 15 segundos. Crie uma investigação de integridade com base em um protocolo ou página de verificação de integridade específica ao seu aplicativo. 

O exemplo a seguir cria uma investigação de TCP. Você também pode criar investigações de HTTP personalizadas para obter verificações de integridade mais refinadas. Ao usar uma investigação de HTTP personalizada, você deverá criar a página de verificação de integridade, como *healthcheck.aspx*. A investigação deve retornar a reposta **HTTP 200 OK** para o balanceador de carga a fim de manter o host em rotação.

Para criar uma investigação de integridade TCP, use [Add-AzLoadBalancerProbeConfig](https://docs.microsoft.com/powershell/module/az.network/add-azloadbalancerprobeconfig). O exemplo a seguir cria uma investigação de integridade chamada *myHealthProbe* que monitora cada VM na porta *TCP* *80*:

```azurepowershell-interactive
Add-AzLoadBalancerProbeConfig `
  -Name "myHealthProbe" `
  -LoadBalancer $lb `
  -Protocol tcp `
  -Port 80 `
  -IntervalInSeconds 15 `
  -ProbeCount 2
```

Para aplicar a investigação de integridade, atualize o balanceador de carga com [Set-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/set-azloadbalancer):

```azurepowershell-interactive
Set-AzLoadBalancer -LoadBalancer $lb
```

### <a name="create-a-load-balancer-rule"></a>Criar uma regra de balanceador de carga
Uma regra de balanceador de carga é usada para definir como o tráfego é distribuído para as VMs. Definir a configuração de IP de front-end para o tráfego de entrada e o pool de IP de back-end para receber o tráfego, junto com as portas de origem e de destino necessárias. Para ter certeza de que apenas VMs íntegras recebem tráfego, defina também a investigação de integridade a ser usada.

Crie uma regra de balanceador de carga com [Add-AzLoadBalancerRuleConfig](https://docs.microsoft.com/powershell/module/az.network/add-azloadbalancerruleconfig). O seguinte exemplo cria uma regra de balanceador de carga chamada *myLoadBalancerRule* e faz o balanceamento de carga do tráfego na porta *TCP* *80*:

```azurepowershell-interactive
$probe = Get-AzLoadBalancerProbeConfig -LoadBalancer $lb -Name "myHealthProbe"

Add-AzLoadBalancerRuleConfig `
  -Name "myLoadBalancerRule" `
  -LoadBalancer $lb `
  -FrontendIpConfiguration $lb.FrontendIpConfigurations[0] `
  -BackendAddressPool $lb.BackendAddressPools[0] `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80 `
  -Probe $probe
```

Atualize o balanceador de carga com [Set-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/set-azloadbalancer):

```azurepowershell-interactive
Set-AzLoadBalancer -LoadBalancer $lb
```

## <a name="configure-virtual-network"></a>Configurar rede virtual
Antes de implantar algumas VMs e poder testar o balanceador, crie os recursos de suporte de rede virtual. Para saber mais sobre redes virtuais, veja o tutorial [Gerenciar Redes Virtuais do Azure](tutorial-virtual-network.md).

### <a name="create-network-resources"></a>Criar recursos da rede
Crie uma rede virtual com [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork). O exemplo a seguir cria uma rede virtual chamada *myVnet* com uma sub-rede chamada *mySubnet*:

```azurepowershell-interactive
# Create subnet config
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name "mySubnet" `
  -AddressPrefix 192.168.1.0/24

# Create the virtual network
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName "myResourceGroupLoadBalancer" `
  -Location "EastUS" `
  -Name "myVnet" `
  -AddressPrefix 192.168.0.0/16 `
  -Subnet $subnetConfig
```

As NICs virtuais são criadas com [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface). O exemplo a seguir cria três NICs virtuais. (Uma NIC virtual para cada VM criada para seu aplicativo nas etapas a seguir). Você pode criar VMs e NICs virtuais adicionais a qualquer momento e adicioná-las ao balanceador de carga:

```azurepowershell-interactive
for ($i=1; $i -le 3; $i++)
{
   New-AzNetworkInterface `
     -ResourceGroupName "myResourceGroupLoadBalancer" `
     -Name myVM$i `
     -Location "EastUS" `
     -Subnet $vnet.Subnets[0] `
     -LoadBalancerBackendAddressPool $lb.BackendAddressPools[0]
}
```


## <a name="create-virtual-machines"></a>Criar máquinas virtuais
Para melhorar a alta disponibilidade do seu aplicativo, coloque suas VMs em um conjunto de disponibilidade.

Crie um conjunto de disponibilidade com [New-AzAvailabilitySet](https://docs.microsoft.com/powershell/module/az.compute/new-azavailabilityset). O exemplo a seguir cria um conjunto de disponibilidade chamado *myAvailabilitySet*:

```azurepowershell-interactive
$availabilitySet = New-AzAvailabilitySet `
  -ResourceGroupName "myResourceGroupLoadBalancer" `
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

Agora, é possível criar as VMs com [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm). O exemplo a seguir cria três VMs e os componentes de rede virtual exigidos, se eles ainda não existirem:

```azurepowershell-interactive
for ($i=1; $i -le 3; $i++)
{
    New-AzVm `
        -ResourceGroupName "myResourceGroupLoadBalancer" `
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

O parâmetro `-AsJob` cria a VM como uma tarefa em segundo plano, para que os prompts do PowerShell sejam exibidos de volta para você. Você pode exibir os detalhes de trabalhos em segundo plano com o cmdelt `Job`. Demora alguns minutos para criar e configurar todas as três VMs.


### <a name="install-iis-with-custom-script-extension"></a>Instalar o IIS com a extensão de script personalizado
Em um tutorial anterior sobre [Como personalizar uma máquina virtual do Windows](tutorial-automate-vm-deployment.md), você aprendeu a automatizar a personalização de VM com a Extensão do Script Personalizado para Windows. Você pode usar a mesma abordagem para instalar e configurar o IIS em suas VMs.

Use [Set-AzVMExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension) para instalar a extensão de script personalizado. A extensão executa `powershell Add-WindowsFeature Web-Server` para instalar o servidor Web do IIS e, em seguida, atualiza a página *Default.htm* para mostrar o nome do host da VM:

```azurepowershell-interactive
for ($i=1; $i -le 3; $i++)
{
   Set-AzVMExtension `
     -ResourceGroupName "myResourceGroupLoadBalancer" `
     -ExtensionName "IIS" `
     -VMName myVM$i `
     -Publisher Microsoft.Compute `
     -ExtensionType CustomScriptExtension `
     -TypeHandlerVersion 1.8 `
     -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
     -Location EastUS
}
```

## <a name="test-load-balancer"></a>Testar o balanceador de carga
Obtenha o endereço IP público do balanceador de carga com [Get-AzPublicIPAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress). O exemplo a seguir obtém o endereço IP para *myPublicIP* criado anteriormente:

```azurepowershell-interactive
Get-AzPublicIPAddress `
  -ResourceGroupName "myResourceGroupLoadBalancer" `
  -Name "myPublicIP" | select IpAddress
```

Você pode inserir o endereço IP público em um navegador da Web. O site é exibido, incluindo o nome do host da VM para a qual o balanceador de carga distribui o tráfego como no exemplo a seguir:

![Site do IIS em execução](./media/tutorial-load-balancer/running-iis-website.png)

Para ver o balanceador de carga distribuir tráfego entre todas as três VMs que executam seu aplicativo, você poderá forçar a atualização de seu navegador da Web.


## <a name="add-and-remove-vms"></a>Adicionar e remover as VMs
Talvez seja necessário fazer a manutenção nas VMs que executam seu aplicativo, como a instalação de atualizações do sistema operacional. Para lidar com o aumento de tráfego em seu aplicativo, talvez seja necessário adicionar outras VMs. Esta seção mostra como remover ou adicionar uma VM do balanceador de carga.

### <a name="remove-a-vm-from-the-load-balancer"></a>Remover uma VM do balanceador de carga
Obtenha a placa de adaptador de rede com [Get-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/get-aznetworkinterface); em seguida, defina a propriedade *LoadBalancerBackendAddressPools* da NIC virtual como *$null*. Por fim, atualize a NIC virtual:

```azurepowershell-interactive
$nic = Get-AzNetworkInterface `
    -ResourceGroupName "myResourceGroupLoadBalancer" `
    -Name "myVM2"
$nic.Ipconfigurations[0].LoadBalancerBackendAddressPools=$null
Set-AzNetworkInterface -NetworkInterface $nic
```

Para ver o balanceador de carga distribuir tráfego entre as duas VMs restantes que executam seu aplicativo, você poderá forçar a atualização de seu navegador da Web. Agora você pode executar a manutenção na VM, como instalação de atualizações do sistema operacional ou execução de uma reinicialização da VM.

### <a name="add-a-vm-to-the-load-balancer"></a>Adicionar uma VM ao balanceador de carga
Após executar a manutenção da VM ou se precisar expandir a capacidade, defina a propriedade *LoadBalancerBackendAddressPools* da NIC virtual para o *BackendAddressPool* de [Get-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/get-azloadbalancer):

Obtenha o balanceador de carga:

```azurepowershell-interactive
$lb = Get-AzLoadBalancer `
    -ResourceGroupName myResourceGroupLoadBalancer `
    -Name myLoadBalancer 
$nic.IpConfigurations[0].LoadBalancerBackendAddressPools=$lb.BackendAddressPools[0]
Set-AzNetworkInterface -NetworkInterface $nic
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou um balanceador de carga e anexou VMs. Você aprendeu como:

> [!div class="checklist"]
> * Criar um balanceador de carga do Azure
> * Criar uma investigação de integridade do balanceador de carga
> * Criar regras de tráfego para o balanceador de carga
> * Usar a Extensão de Script Personalizado para criar um site do IIS básico
> * Criar máquinas virtuais e anexar a um balanceador de carga
> * Exibir um balanceador de carga em ação
> * Adicionar e remover as VMs de um balanceador de carga

Avance para o próximo tutorial para aprender a gerenciar a rede de VM.

> [!div class="nextstepaction"]
> [Gerencie VMs e redes virtuais](./tutorial-virtual-network.md)
