---
title: Criar um balanceador de carga voltado para a Internet do Azure - PowerShell | Microsoft Docs
description: Saiba como criar um balanceador de carga para a Internet no Gerenciador de Recursos usando o PowerShell
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
tags: azure-resource-manager
ms.assetid: 8257f548-7019-417f-b15f-d004a1eec826
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 0347e90fa97a083865e8b783c96034d30a859031
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="get-started"></a>Criar um balanceador de carga para a Internet no Resource Manager usando o PowerShell

> [!div class="op_single_selector"]
> * [Portal](../load-balancer/load-balancer-get-started-internet-portal.md)
> * [PowerShell](../load-balancer/load-balancer-get-started-internet-arm-ps.md)
> * [CLI do Azure](../load-balancer/load-balancer-get-started-internet-arm-cli.md)
> * [Modelo](../load-balancer/load-balancer-get-started-internet-arm-template.md)

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

[!INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Este artigo aborda o modelo de implantação do Gerenciador de Recursos. Também é possível [Saber como criar um balanceador de carga para a Internet usando o modelo de implantação clássica](load-balancer-get-started-internet-classic-cli.md).

[!INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## <a name="deploying-the-solution-by-using-azure-powershell"></a>Implantando a solução usando o Azure PowerShell

Os procedimentos a seguir explicam como criar um balanceador de carga para a Internet usando o Azure Resource Manager com o PowerShell. Com o Azure Resource Manager, todos os recursos são criados e configurados individualmente e colocados juntos para criar um balanceador de carga.

Você precisa criar e configurar os seguintes objetos para implantar um balanceador de carga:

* Configuração de IP de front-end: contém PIPs ( endereços IP públicos) para o tráfego de rede de entrada.
* Pool de endereços de back-end: contém NICs (interfaces de rede) para que as máquinas virtuais recebam o tráfego de rede do balanceador de carga.
* Regras de balanceamento de carga: contém regras que mapeiam uma porta pública no balanceador de carga para uma porta no pool de endereços de back-end.
* Regras NAT de entrada: contém regras que mapeiam uma porta pública no balanceador de carga para uma porta de uma máquina virtual específica no pool de endereços de back-end.
* Investigações: contém investigações de integridade usadas para verificar a disponibilidade de instâncias de máquinas virtuais no pool de endereços de back-end.

Para saber mais, confira [Suporte do Azure Resource Manager para Balanceador de Carga](load-balancer-arm.md).

## <a name="set-up-powershell-to-use-resource-manager"></a>Configurar o PowerShell para usar o Gerenciador de Recursos

Verifique se você tem a versão de produção mais recente do módulo Azure Resource Manager para o PowerShell:

1. Entre no Azure.

    ```powershell
    Login-AzureRmAccount
    ```

    Insira suas credenciais quando solicitado.

2. Verificar as assinaturas da conta.

    ```powershell
    Get-AzureRmSubscription
    ```

3. Escolha quais das suas assinaturas do Azure deseja usar.

    ```powershell
    Select-AzureRmSubscription -SubscriptionId 'GUID of subscription'
    ```

4. Crie um grupos de recursos. (Ignore esta etapa se está usando um grupo de recursos existente.)

    ```powershell
    New-AzureRmResourceGroup -Name NRP-RG -location "West US"
    ```

## <a name="create-a-virtual-network-and-a-public-ip-address-for-the-front-end-ip-pool"></a>Criar uma rede virtual e um endereço IP público para o pool de IPs de front-end

1. Criar uma sub-rede e uma rede virtual.

    ```powershell
    $backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24
    New-AzureRmvirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet
    ```

2. Crie um recurso de endereço IP público do Azure chamado **PublicIP** a ser usado por um pool de IPs de front-end com o nome DNS **loadbalancernrp.westus.cloudapp.azure.com**. O comando a seguir usa o tipo de alocação estática.

    ```powershell
    $publicIP = New-AzureRmPublicIpAddress -Name PublicIp -ResourceGroupName NRP-RG -Location 'West US' -AllocationMethod Static -DomainNameLabel loadbalancernrp
    ```

   > [!IMPORTANT]
   > O balanceador de carga usa o rótulo de domínio do IP público como prefixo para seu FQDN. Isso é diferente do modelo de implantação clássica, que usa o serviço de nuvem como o FQDN do balanceador de carga.
   > Neste exemplo, o FQDN é **loadbalancernrp.westus.cloudapp.azure.com**.

## <a name="create-a-front-end-ip-pool-and-a-back-end-address-pool"></a>Criar um pool de IPs de front-end e um pool de endereços de back-end

1. Crie um pool de IPs de front-end chamado **LB-Frontend** que usa o recurso **PublicIp**.

    ```powershell
    $frontendIP = New-AzureRmLoadBalancerFrontendIpConfig -Name LB-Frontend -PublicIpAddress $publicIP
    ```

2. Crie um pool de endereços de back-end chamado **LB-backend**.

    ```powershell
    $beaddresspool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name LB-backend
    ```

## <a name="create-nat-rules-a-load-balancer-rule-a-probe-and-a-load-balancer"></a>Criar regras NAT, uma regra de balanceador de carga, uma investigação e um balanceador de carga

Este exemplo cria os seguintes itens:

* Uma regra NAT para converter todo o tráfego de entrada na porta 3441 para a porta 3389
* Uma regra NAT para converter todo o tráfego de entrada na porta 3442 para a porta 3389
* Uma regra de teste que verifica o status de integridade em uma página denominada **HealthProbe.aspx**
* Uma regra de balanceador de carga para equilibrar todo o tráfego de entrada na porta 80 para a porta 80 de endereços no pool de back-end
* Um balanceador de carga que usa todos esses objetos

Siga estas etapas:

1. Criar regras NAT.

    ```powershell
    $inboundNATRule1= New-AzureRmLoadBalancerInboundNatRuleConfig -Name RDP1 -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389

    $inboundNATRule2= New-AzureRmLoadBalancerInboundNatRuleConfig -Name RDP2 -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3442 -BackendPort 3389
    ```

2. Crie um teste de integridade. Há duas maneiras de configurar uma investigação:

    Investigação HTTP

    ```powershell
    $healthProbe = New-AzureRmLoadBalancerProbeConfig -Name HealthProbe -RequestPath 'HealthProbe.aspx' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2
    ```

    Investigação TCP

    ```powershell
    $healthProbe = New-AzureRmLoadBalancerProbeConfig -Name HealthProbe -Protocol Tcp -Port 80 -IntervalInSeconds 15 -ProbeCount 2
    ```

3. Crie uma regra de balanceador de carga.

    ```powershell
    $lbrule = New-AzureRmLoadBalancerRuleConfig -Name HTTP -FrontendIpConfiguration $frontendIP -BackendAddressPool  $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80
    ```

4. Crie o balanceador de carga usando os objetos criados anteriormente.

    ```powershell
    $NRPLB = New-AzureRmLoadBalancer -ResourceGroupName NRP-RG -Name NRP-LB -Location 'West US' -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule1,$inboundNatRule2 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe
    ```

## <a name="create-nics"></a>Criar NICs

Crie interfaces de rede (ou modificar as existentes) e as associe às regras NAT, às regras do balanceador de carga e às investigações:

1. Obtenha a rede virtual e a sub-rede da rede virtual, onde as NICs precisam ser criadas.

    ```powershell
    $vnet = Get-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG
    $backendSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet
    ```

2. Crie uma NIC chamada **lb-nic1-be**e associe-a à primeira regra NAT e ao primeiro (e único) pool de endereços de back-end.

    ```powershell
    $backendnic1= New-AzureRmNetworkInterface -ResourceGroupName NRP-RG -Name lb-nic1-be -Location 'West US' -PrivateIpAddress 10.0.2.6 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[0]
    ```

3. Crie uma NIC chamada **lb-nic2-be**e a associe à segunda regra NAT e ao primeiro (e único) pool de endereços de back-end.

    ```powershell
    $backendnic2= New-AzureRmNetworkInterface -ResourceGroupName NRP-RG -Name lb-nic2-be -Location 'West US' -PrivateIpAddress 10.0.2.7 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[1]
    ```

4. Conferir as NICs.

        $backendnic1

    Saída esperada:

        Name                 : lb-nic1-be
        ResourceGroupName    : NRP-RG
        Location             : westus
        Id                   : /subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be
        Etag                 : W/"d448256a-e1df-413a-9103-a137e07276d1"
        ResourceGuid         : 896cac4f-152a-40b9-b079-3e2201a5906e
        ProvisioningState    : Succeeded
        Tags                 :
        VirtualMachine       : null
        IpConfigurations     : [
                            {
                            "Name": "ipconfig1",
                            "Etag": "W/\"d448256a-e1df-413a-9103-a137e07276d1\"",
                            "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be/ipConfigurations/ipconfig1",
                            "PrivateIpAddress": "10.0.2.6",
                            "PrivateIpAllocationMethod": "Static",
                            "Subnet": {
                                "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/virtualNetworks/NRPVNet/subnets/LB-Subnet-BE"
                            },
                            "ProvisioningState": "Succeeded",
                            "PrivateIpAddressVersion": "IPv4",
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
                            "Primary": true,
                            "ApplicationGatewayBackendAddressPools": []
                            }
                        ]
        DnsSettings          : {
                            "DnsServers": [],
                            "AppliedDnsServers": [],
                            "InternalDomainNameSuffix": "prcwibzcuvie5hnxav0yjks2cd.dx.internal.cloudapp.net"
                        }
        EnableIPForwarding   : False
        NetworkSecurityGroup : null
        Primary              :

5. Use o cmdlet `Add-AzureRmVMNetworkInterface` para atribuir as NICs a VMs diferentes.

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Para obter orientação sobre como criar uma máquina virtual e atribuir uma NIC, confira [Criar uma VM do Azure usando o PowerShell](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json).

## <a name="add-the-network-interface-to-the-load-balancer"></a>Adicionar a interface de rede ao balanceador de carga

1. Recupere o balanceador de carga do Azure.

    Carregue o recurso de balanceador de carga em uma variável (se ainda não tiver feito isso). A variável é chamada **$lb**. Use os mesmos nomes de recurso balanceador de carga que você criou anteriormente.

    ```powershell
    $lb= get-azurermloadbalancer -name NRP-LB -resourcegroupname NRP-RG
    ```

2. Carregue a configuração de back-end em uma variável.

    ```powershell
    $backend=Get-AzureRmLoadBalancerBackendAddressPoolConfig -name LB-backend -LoadBalancer $lb
    ```

3. Carregue a interface de rede já criada em uma variável. O nome da variável é **$nic**. O nome da interface de rede é o mesmo do exemplo anterior.

    ```powershell
    $nic =get-azurermnetworkinterface -name lb-nic1-be -resourcegroupname NRP-RG
    ```

4. Altere a configuração de back-end na interface de rede.

    ```powershell
    $nic.IpConfigurations[0].LoadBalancerBackendAddressPools=$backend
    ```

5. Salve o objeto de interface de rede.

    ```powershell
    Set-AzureRmNetworkInterface -NetworkInterface $nic
    ```

    Depois que uma interface de rede é adicionada ao pool de back-end do balanceador de carga, ela começa a receber tráfego de rede com base nas regras de balanceamento de carga para esse recurso de balanceador de carga.

## <a name="update-an-existing-load-balancer"></a>Atualizar um balanceador de carga existente

1. Usando o balanceador de carga do exemplo anterior, atribua um objeto do balanceador de carga à variável **$slb** usando `Get-AzureLoadBalancer`.

    ```powershell
    $slb = get-AzureRmLoadBalancer -Name NRP-LB -ResourceGroupName NRP-RG
    ```

2. No exemplo a seguir, você adiciona uma regra de NAT de entrada, usando a porta 81 no pool de front-end e a porta 8181 no pool de back-end, a um balanceador de carga existente.

    ```powershell
    $slb | Add-AzureRmLoadBalancerInboundNatRuleConfig -Name NewRule -FrontendIpConfiguration $slb.FrontendIpConfigurations[0] -FrontendPort 81  -BackendPort 8181 -Protocol TCP
    ```

3. Salve a nova configuração usando `Set-AzureLoadBalancer`.

    ```powershell
    $slb | Set-AzureRmLoadBalancer
    ```

## <a name="remove-a-load-balancer"></a>Remover um balanceador de carga

Use o comando `Remove-AzureLoadBalancer` para excluir um balanceador de carga criado anteriormente chamado **NRP-LB** em um grupo de recursos chamado **NRP-RG**.

```powershell
Remove-AzureRmLoadBalancer -Name NRP-LB -ResourceGroupName NRP-RG
```

> [!NOTE]
> Você pode usar a opção **-Force** para evitar a solicitação de exclusão.

## <a name="next-steps"></a>Próximas etapas

[Introdução à configuração de um balanceador de carga interno](load-balancer-get-started-ilb-arm-ps.md)

[Configurar um modo de distribuição do balanceador de carga](load-balancer-distribution-mode.md)

[Definir configurações de tempo limite de TCP ocioso para o balanceador de carga](load-balancer-tcp-idle-timeout.md)
