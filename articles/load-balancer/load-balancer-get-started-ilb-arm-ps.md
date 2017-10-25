---
title: Criar um balanceador de carga interno do Azure - PowerShell | Microsoft Docs
description: Saiba como criar um balanceador de carga interno no Gerenciador de Recursos usando o PowerShell
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
tags: azure-resource-manager
ms.assetid: c6c98981-df9d-4dd7-a94b-cc7d1dc99369
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 1215ca8ff4d45e3b910b8e0ec0bd6833e4bfc308
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-internal-load-balancer-using-powershell"></a>Criar um balanceador de carga interno usando o PowerShell

> [!div class="op_single_selector"]
> * [Portal do Azure](../load-balancer/load-balancer-get-started-ilb-arm-portal.md)
> * [PowerShell](../load-balancer/load-balancer-get-started-ilb-arm-ps.md)
> * [CLI do Azure](../load-balancer/load-balancer-get-started-ilb-arm-cli.md)
> * [Modelo](../load-balancer/load-balancer-get-started-ilb-arm-template.md)

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

[!INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

> [!NOTE]
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Gerenciador de Recursos e clássico](../azure-resource-manager/resource-manager-deployment-model.md).  Este artigo aborda usando o modelo de implantação do Gerenciador de Recursos, que a Microsoft recomenda para a maioria das novas implantações em vez de do [modelo de implantação clássico](load-balancer-get-started-ilb-classic-ps.md).

[!INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

As etapas a seguir explicam como criar um balanceador de carga interno usando o Azure Resource Manager com PowerShell. Com o Azure Resource Manager, os itens para criar um balanceador de carga interno são configurados individualmente e combinados para criar um balanceador de carga.

Crie e configure os seguintes objetos para implantar um balanceador de carga:

* Configuração do IP de front-end - configura o endereço IP privado para tráfego de rede de entrada.
* Pool de endereços de back-end - vai configurar as interfaces de rede que receberão o tráfego com balanceamento de carga proveniente do pool de IPs de front-end.
* Regras de balanceamento de carga - configuração de porta local e de origem para o balanceador de carga.
* Investigações - configura a investigação de status de integridade para instâncias de Máquina Virtual.
* Regras NAT de entrada - configura as regras de porta para acessar diretamente uma das instâncias de Máquina Virtual.

É possível obter mais informações sobre componentes do balanceador de carga com o gerenciador de recursos do Azure em [Suporte do Azure Resource Manager para o balanceador de carga](load-balancer-arm.md).

As etapas a seguir explicam como configurar um balanceador de carga entre duas máquinas virtuais.

## <a name="set-up-powershell-to-use-resource-manager"></a>Configurar o PowerShell para usar o Gerenciador de Recursos

Certifique-se de ter a versão de produção mais recente do módulo do Azure para PowerShell e de configurar corretamente o PowerShell para acessar sua assinatura do Azure.

### <a name="step-1"></a>Etapa 1

```powershell
Login-AzureRmAccount
```

### <a name="step-2"></a>Etapa 2

Verificar as assinaturas da conta

```powershell
Get-AzureRmSubscription
```

Você deve se Autenticar com suas credenciais.

### <a name="step-3"></a>Etapa 3

Escolha quais das suas assinaturas do Azure deseja usar.

```powershell
Select-AzureRmSubscription -Subscriptionid "GUID of subscription"
```

### <a name="create-resource-group-for-load-balancer"></a>Criar grupo de recursos para o balanceador de carga

Crie um grupo de recursos (pule esta etapa se você estiver usando um grupo de recursos existente)

```powershell
New-AzureRmResourceGroup -Name NRP-RG -location "West US"
```

O Gerenciador de Recursos do Azure requer que todos os grupos de recursos especifiquem um local. Esse local é usado como o padrão para os recursos do grupo de recursos em questão. Certifique-se de que todos os comandos para criar um balanceador de carga usem o mesmo grupo de recursos.

No exemplo anterior, criamos um grupo de recursos denominado **NRP-RG** e o local **Oeste dos EUA**.

## <a name="create-a-virtual-network-and-a-private-ip-address-for-a-front-end-ip-pool"></a>Crie uma rede virtual e um endereço IP privado para um pool de IPs de front-end

Cria uma sub-rede para a rede virtual e atribui à variável $backendSubnet

```powershell
$backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24
```

Criar uma rede virtual:

```powershell
$vnet= New-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet
```

Cria a rede virtual e adiciona a sub-rede lb-subnet-be à rede virtual NRPVNet e atribui à variável $vnet.

## <a name="create-a-front-end-ip-pool-and-back-end-address-pool"></a>Criar um pool de IPs de front-end e um pool de endereços de back-end

Configure um pool de IP front-end para o tráfego de rede do balanceador de carga de entrada e pool de endereços de back-end para receber o tráfego balanceado de carga.

### <a name="step-1"></a>Etapa 1

Crie um pool de IPs de front-end usando o endereço IP privado 10.0.2.5 para a sub-rede 10.0.2.0/24, que será o ponto de extremidade do tráfego de rede de entrada.

```powershell
$frontendIP = New-AzureRmLoadBalancerFrontendIpConfig -Name LB-Frontend -PrivateIpAddress 10.0.2.5 -SubnetId $vnet.subnets[0].Id
```

### <a name="step-2"></a>Etapa 2

Configure um pool de endereços de back-end usado para receber o tráfego de entrada de pool de IP front-end:

```powershell
$beaddresspool= New-AzureRmLoadBalancerBackendAddressPoolConfig -Name "LB-backend"
```

## <a name="create-load-balancing-rules-nat-rules-probe-and-load-balancer"></a>Criar regras de balanceamento de carga, regras de NAT, sonda e balanceador de carga

Depois de criar o pool de IP front-end e o pool de endereços de back-end, você precisará criar as regras que pertencem o recurso de balanceador de carga:

### <a name="step-1"></a>Etapa 1

```powershell
$inboundNATRule1= New-AzureRmLoadBalancerInboundNatRuleConfig -Name "RDP1" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389

$inboundNATRule2= New-AzureRmLoadBalancerInboundNatRuleConfig -Name "RDP2" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3442 -BackendPort 3389

$healthProbe = New-AzureRmLoadBalancerProbeConfig -Name "HealthProbe" -RequestPath "HealthProbe.aspx" -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

$lbrule = New-AzureRmLoadBalancerRuleConfig -Name "HTTP" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80
```

O exemplo anterior é a criação dos seguintes itens:

* Regra de NAT em que todo o tráfego de entrada para a porta 3441 irá para a porta 3389.
* uma segunda regra de NAT em que todo o tráfego de entrada para a porta 3442 irá para a porta 3389.
* uma regra que balanceará a carga de todo o tráfego de entrada na porta pública 80 para a porta local 80 no pool de endereços de back-end.
* uma regra de investigação que verificará o status de integridade para o caminho "HealthProbe.aspx"

### <a name="step-2"></a>Etapa 2

Criar o balanceador de carga adicionando todos os objetos (regras de NAT, regras do balanceador de carga, configurações de teste) juntos:

```powershell
$NRPLB = New-AzureRmLoadBalancer -ResourceGroupName "NRP-RG" -Name "NRP-LB" -Location "West US" -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule1,$inboundNatRule2 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe
```

## <a name="create-network-interfaces"></a>Criar interfaces de rede

Depois de criar o balanceador de carga interno, você precisa definir quais interfaces de rede poderão receber o tráfego de rede com balanceamento de carga entrada, regras de NAT e teste. Nesse caso, a interface de rede é configurada individualmente e pode ser atribuída a uma máquina virtual posteriormente.

### <a name="step-1"></a>Etapa 1

Obtenha a rede virtual do recurso e a sub-rede para criar interfaces de rede:

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG

$backendSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet
```

Esta etapa cria uma interface de rede que pertencerá ao pool de back-end do balanceador de carga e associará a primeira regra NAT para RDP para essa interface de rede:

```powershell
$backendnic1= New-AzureRmNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic1-be -Location "West US" -PrivateIpAddress 10.0.2.6 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[0]
```

### <a name="step-2"></a>Etapa 2

Criar uma segunda chamada de interface de rede chamada LB-Nic2-BE:

Esta etapa cria uma segunda interface de rede, atribuindo o mesmo pool de back-end do balanceador de carga e associando a segunda regra de NAT criada para RDP:

```powershell
$backendnic2= New-AzureRmNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic2-be -Location "West US" -PrivateIpAddress 10.0.2.7 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[1]
```

O resultado final mostra a saída a seguir:

    $backendnic1

Saída esperada:

    Name                 : lb-nic1-be
    ResourceGroupName    : NRP-RG
    Location             : westus
    Id                   : /subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be
    Etag                 : W/"d448256a-e1df-413a-9103-a137e07276d1"
    ProvisioningState    : Succeeded
    Tags                 :
    VirtualMachine       : null
    IpConfigurations     : [
                         {
                           "PrivateIpAddress": "10.0.2.6",
                           "PrivateIpAllocationMethod": "Static",
                           "Subnet": {
                             "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/virtualNetworks/NRPVNet/subnets/LB-Subnet-BE"
                           },
                           "PublicIpAddress": {
                             "Id": null
                           },
                           "LoadBalancerBackendAddressPools": [
                             {
                               "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/loadBalancers/NRPlb/backendAddressPools/LB-backend"
                             }
                           ],
                           "LoadBalancerInboundNatRules": [
                             {
                               "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/loadBalancers/NRPlb/inboundNatRules/RDP1"
                             }
                           ],
                           "ProvisioningState": "Succeeded",
                           "Name": "ipconfig1",
                           "Etag": "W/\"d448256a-e1df-413a-9103-a137e07276d1\"",
                           "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be/ipConfigurations/ipconfig1"
                         }
                       ]
    DnsSettings          : {
                         "DnsServers": [],
                         "AppliedDnsServers": []
                       }
    AppliedDnsSettings   :
    NetworkSecurityGroup : null
    Primary              : False



### <a name="step-3"></a>Etapa 3

Use o comando Add-AzureRmVMNetworkInterface para atribuir a NIC a uma máquina virtual.

Você pode encontrar as instruções passo a passo para criar uma máquina virtual e atribuir a uma NIC de acordo com a documentação: [Criar uma VM do Azure usando o PowerShell](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json).

## <a name="add-the-network-interface"></a>Adicionar a interface de rede

Se já tiver uma máquina virtual criada, você poderá adicionar a interface de rede com as etapas a seguir:

### <a name="step-1"></a>Etapa 1

Carregue o recurso de balanceador de carga em uma variável (se ainda não tiver feito isso). A variável usada é chamada de $lb e usa os mesmos nomes do recurso de balanceador de carga criado nas etapas anteriores.

```powershell
$lb = Get-AzureRmLoadBalancer –name NRP-LB -resourcegroupname NRP-RG
```

### <a name="step-2"></a>Etapa 2

Carregue a configuração de back-end em uma variável.

```powershell
$backend = Get-AzureRmLoadBalancerBackendAddressPoolConfig -name LB-backend -LoadBalancer $lb
```

### <a name="step-3"></a>Etapa 3

Carregue a interface de rede já criada em uma variável. O nome da variável usada é $nic. O nome da interface de rede usada é o mesmo do exemplo anterior.

```powershell
$nic = Get-AzureRmNetworkInterface –name lb-nic1-be -resourcegroupname NRP-RG
```

### <a name="step-4"></a>Etapa 4

Altere a configuração de back-end na interface de rede.

```powershell
$nic.IpConfigurations[0].LoadBalancerBackendAddressPools=$backend
```

### <a name="step-5"></a>Etapa 5

Salve o objeto de interface de rede.

```powershell
Set-AzureRmNetworkInterface -NetworkInterface $nic
```

Depois que uma interface de rede é adicionada ao pool de back-end do balanceador de carga, ela começa a receber tráfego de rede com base nas regras de balanceamento de carga para esse recurso de balanceador de carga.

## <a name="update-an-existing-load-balancer"></a>Atualizar um balanceador de carga existente

### <a name="step-1"></a>Etapa 1
Usando o balanceador de carga do exemplo anterior, atribua o objeto balanceador de carga à variável $slb usando Get-AzureRmLoadBalancer

```powershell
$slb = Get-AzureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG
```

### <a name="step-2"></a>Etapa 2

No exemplo a seguir, você adicionará uma nova regra de NAT de entrada usando a porta 81 no front-end e a porta 8181 do pool de back-end a um balanceador de carga existente

```powershell
$slb | Add-AzureRmLoadBalancerInboundNatRuleConfig -Name NewRule -FrontendIpConfiguration $slb.FrontendIpConfigurations[0] -FrontendPort 81  -BackendPort 8181 -Protocol Tcp
```

### <a name="step-3"></a>Etapa 3

Salvar a nova configuração usando Set-AzureLoadBalancer

```powershell
$slb | Set-AzureRmLoadBalancer
```

## <a name="remove-a-load-balancer"></a>Remover um balanceador de carga

Use o comando Remove-AzureRmLoadBalancer para excluir um balanceador de carga criado anteriormente denominado "NRP-LB" em um grupo de recursos chamado "NRP-RG"

```powershell
Remove-AzureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG
```

> [!NOTE]
> Você pode usar a opção -Force para evitar a solicitação de exclusão.

## <a name="next-steps"></a>Próximas etapas

[Configurar um modo de distribuição do balanceador de carga](load-balancer-distribution-mode.md)

[Definir configurações de tempo limite de TCP ocioso para o balanceador de carga](load-balancer-tcp-idle-timeout.md)
