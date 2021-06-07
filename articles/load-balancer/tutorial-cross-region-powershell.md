---
title: 'Tutorial: Criar um balanceador de carga entre regiões usando o Azure PowerShell'
titleSuffix: Azure Load Balancer
description: Comece a usar este tutorial implantando um Azure Load Balancer entre regiões com o Azure PowerShell.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: tutorial
ms.date: 02/10/2021
ms.openlocfilehash: 88e400cea764be84521c003a681aa74885dc29ce
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101721337"
---
# <a name="tutorial-create-a-cross-region-azure-load-balancer-using-azure-powershell"></a>Tutorial: Criar um Azure Load Balancer entre regiões usando o Azure PowerShell

Um balanceador de carga entre regiões garante que um serviço esteja disponível globalmente em várias regiões do Azure. Se uma região falhar, o tráfego será roteado para o balanceador de carga regional íntegro mais próximo.  

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um balanceador de carga entre regiões.
> * Crie uma regra de balanceador de carga.
> * Criar um pool de back-end que contém dois balanceadores de carga regionais.
> * Testar o balanceador de carga.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Azure.
- Dois Azure Load Balancers de SKU **Standard** com pools de back-end implantados em duas regiões diferentes do Azure.
    - Para obter informações sobre como criar um Standard Load Balancer regional e máquinas virtuais para pools de back-end, confira [Guia de Início Rápido: Crie um balanceador de carga público para balancear cargas de VMs usando o Azure PowerShell](quickstart-load-balancer-standard-public-powershell.md).
        - Acrescente o nome dos balanceadores de carga e das máquinas virtuais em cada região com **-R1** e **-R2**. 
- O Azure PowerShell instalado localmente ou o Azure Cloud Shell.


Se você optar por instalar e usar o PowerShell localmente, este artigo exigirá o módulo do Azure PowerShell versão 5.4.1 ou posterior. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-Az-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Connect-AzAccount` para criar uma conexão com o Azure.

## <a name="create-cross-region-load-balancer"></a>Criar um balanceador de carga entre regiões


### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados.

Crie um grupo de recursos com [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).


```azurepowershell-interactive
$rg = @{
    Name = 'MyResourceGroupLB-CR'
    Location = 'westus'
}
New-AzResourceGroup @rg

```

### <a name="create-cross-region-load-balancer-resources"></a>Criar recursos do balanceador de carga entre regiões

Nesta seção, você criará os recursos necessários para o balanceador de carga entre regiões.

Um IP público de SKU padrão global é usado no front-end do balanceador de carga entre regiões.

* Use [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) para criar o endereço IP público.

* Crie uma configuração de IP de front-end com [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig).

* Crie um pool de endereços de back-end com [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig).

* Crie uma regra de balanceador de carga com [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig).

* Crie um balanceador de carga entre regiões com [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer).

```azurepowershell-interactive
## Create global IP address for load balancer ##
$ip = @{
    Name = 'myPublicIP-CR'
    ResourceGroupName = 'MyResourceGroupLB-CR'
    Location = 'westus'
    Sku = 'Standard'
    Tier = 'Global'
    AllocationMethod = 'Static'
}
$publicIP = New-AzPublicIpAddress @ip

## Create frontend configuration ##
$fe = @{
    Name = 'myFrontEnd-CR'
    PublicIpAddress = $publicIP
}
$feip = New-AzLoadBalancerFrontendIpConfig @fe

## Create back-end address pool ##
$be = @{
    Name = 'myBackEndPool-CR'
}
$bepool = New-AzLoadBalancerBackendAddressPoolConfig @be

## Create the load balancer rule ##
$rul = @{
    Name = 'myHTTPRule-CR'
    Protocol = 'tcp'
    FrontendPort = '80'
    BackendPort = '80'
    FrontendIpConfiguration = $feip
    BackendAddressPool = $bepool
}
$rule = New-AzLoadBalancerRuleConfig @rul

## Create cross-region load balancer resource ##
$lbp = @{
    ResourceGroupName = 'myResourceGroupLB-CR'
    Name = 'myLoadBalancer-CR'
    Location = 'westus'
    Sku = 'Standard'
    Tier = 'Global'
    FrontendIpConfiguration = $feip
    BackendAddressPool = $bepool
    LoadBalancingRule = $rule
}
$lb = New-AzLoadBalancer @lbp
```

## <a name="configure-backend-pool"></a>Configurar o pool de back-end

Nesta seção, você adicionará dois balanceadores de carga Standard regionais ao pool de back-end do balanceador de carga entre regiões.

> [!IMPORTANT]
> Para concluir essas etapas, verifique se dois balanceadores de carga regionais com pools de back-end foram implantados na sua assinatura.  Para obter mais informações, confira **[Guia de Início Rápido: Crie um balanceador de carga público para balancear cargas de VMs usando o Azure PowerShell](quickstart-load-balancer-standard-public-powershell.md)** .

* Use [Get-AzLoadBalancer](/powershell/module/az.network/get-azloadbalancer) e [Get-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/get-azloadbalancerfrontendipconfig) para armazenar as informações regionais do balanceador de carga em variáveis.

* Use o [New-AzLoadBalancerBackendAddressConfig](/powershell/module/az.network/new-azloadbalancerbackendaddressconfig) para criar a configuração de pool de endereços de back-end para o balanceador de carga.

* Use [Set-AzLoadBalancerBackendAddressPool](/powershell/module/az.network/new-azloadbalancerbackendaddresspool) para adicionar o front-end do balanceador de carga regional ao pool de back-ends entre regiões.

```azurepowershell-interactive
## Place the region one load balancer configuration in a variable ##
$region1 = @{
    Name = 'myLoadBalancer-R1'
    ResourceGroupName = 'CreatePubLBQS-rg-r1'
}
$R1 = Get-AzLoadBalancer @region1

## Place the region two load balancer configuration in a variable ##
$region2 = @{
    Name = 'myLoadBalancer-R2'
    ResourceGroupName = 'CreatePubLBQS-rg-r2'
}
$R2 = Get-AzLoadBalancer @region2

## Place the region one load balancer front-end configuration in a variable ##
$region1fe = @{
    Name = 'MyFrontEnd-R1'
    LoadBalancer = $R1
}
$R1FE = Get-AzLoadBalancerFrontendIpConfig @region1fe

## Place the region two load balancer front-end configuration in a variable ##
$region2fe = @{
    Name = 'MyFrontEnd-R2'
    LoadBalancer = $R2
}
$R2FE = Get-AzLoadBalancerFrontendIpConfig @region2fe

## Create the cross-region backend address pool configuration for region 1 ##
$region1ap = @{
    Name = 'MyBackendPoolConfig-R1'
    LoadBalancerFrontendIPConfigurationId = $R1FE.Id
}
$beaddressconfigR1 = New-AzLoadBalancerBackendAddressConfig @region1ap

## Create the cross-region backend address pool configuration for region 2 ##
$region2ap = @{
    Name = 'MyBackendPoolConfig-R2'
    LoadBalancerFrontendIPConfigurationId = $R2FE.Id
}
$beaddressconfigR2 = New-AzLoadBalancerBackendAddressConfig @region2ap

## Apply the backend address pool configuration for the cross-region load balancer ##
$bepoolcr = @{
    ResourceGroupName = 'myResourceGroupLB-CR'
    LoadBalancerName = 'myLoadBalancer-CR'
    Name = 'myBackEndPool-CR'
    LoadBalancerBackendAddress = $beaddressconfigR1,$beaddressconfigR2
}
Set-AzLoadBalancerBackendAddressPool @bepoolcr

```

## <a name="test-the-load-balancer"></a>Testar o balanceador de carga

Nesta seção, você testará o balanceador de carga entre regiões. Você se conectará ao endereço IP público em um navegador da Web.  Você interromperá as máquinas virtuais em um dos pools de back-end do balanceador de carga regional e observará o failover.

1. Use [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) para obter o endereço IP público do balanceador de carga:

```azurepowershell-interactive
$ip = @{
    Name = 'myPublicIP-CR'
    ResourceGroupName = 'myResourceGroupLB-CR'
}  
Get-AzPublicIPAddress @ip | select IpAddress

```
2. Copie o endereço IP público e cole-o na barra de endereços do seu navegador. A página padrão do servidor Web do IIS é exibida no navegador.

3. Interrompa as máquinas virtuais no pool de back-end de um dos balanceadores de carga regionais.

4. Atualize o navegador da Web e observe o failover da conexão no outro balanceador de carga regional.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não forem mais necessários, você poderá usar o comando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos, o balanceador de carga e os recursos restantes.

```azurepowershell-interactive
Remove-AzResourceGroup -Name 'myResourceGroupLB-CR'
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você:

* Um endereço IP global foi criado.
* Criou um balanceador de carga entre regiões.
* Criou uma regra de balanceamento de carga.
* Adicionou balanceadores de carga regionais ao pool de back-end do balanceador de carga entre regiões.
* Testou o balanceador de carga.


Prossiga para o próximo artigo para saber como...
> [!div class="nextstepaction"]
> [VMs do balanceador de carga em zonas de disponibilidade](tutorial-load-balancer-standard-public-zone-redundant-portal.md)