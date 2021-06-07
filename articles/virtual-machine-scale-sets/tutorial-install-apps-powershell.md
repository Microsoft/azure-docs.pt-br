---
title: Tutorial – Instalar aplicativos em um conjunto de dimensionamento com o Azure PowerShell
description: Saiba como usar o Azure PowerShell para instalar aplicativos em conjuntos de dimensionamento de máquinas virtuais com a Extensão de Script Personalizado
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.date: 11/08/2018
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 7c365e83c0208a5105d9396fb788966bbd4643ac
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105934573"
---
# <a name="tutorial-install-applications-in-virtual-machine-scale-sets-with-azure-powershell"></a>Tutorial: instalar aplicativos em conjuntos de dimensionamento de máquinas virtuais com o Azure PowerShell

Para executar aplicativos em instâncias de VM (máquina virtual) em um conjunto de dimensionamento, primeiro é necessário instalar os componentes de aplicativo e os arquivos necessários. Em um tutorial anterior, você aprendeu a criar e usar uma imagem de VM personalizada para implantar suas instâncias de VM. Essa imagem personalizada incluía instalações manuais de aplicativos e configurações. Você também pode automatizar a instalação de aplicativos para um conjunto de dimensionamento após a implantação de cada instância de VM ou atualizar um aplicativo que já é executado em um conjunto de dimensionamento. Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Instalar automaticamente os aplicativos para o conjunto de dimensionamento
> * Use a Extensão de Script Personalizado do Azure
> * Atualizar um aplicativo em execução em um conjunto de dimensionamento

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]


## <a name="what-is-the-azure-custom-script-extension"></a>O que é a Extensão de Script Personalizado do Azure?
A extensão de script personalizado baixa e executa scripts em VMs do Azure. Essa extensão é útil para a configuração de implantação de postagem, instalação de software ou qualquer outra configuração/tarefa de gerenciamento. Os scripts podem ser baixados do armazenamento do Azure ou do GitHub, ou fornecidos ao Portal do Azure no tempo de execução da extensão.

A extensão de Script Personalizado se integra com os modelos do Azure Resource Manager. Ele também pode ser usado com a CLI do Azure, o Azure PowerShell, o portal do Azure ou com a API REST. Para obter mais informações, consulte a [Visão geral da Extensão de Script Personalizado](../virtual-machines/extensions/custom-script-windows.md).

Para ver a Extensão de Script Personalizado em ação, crie um conjunto de dimensionamento que instala o servidor Web IIS e gera o nome do host da instância de VM do conjunto de dimensionamento. A definição de Extensão de Script Personalizado baixa um script de exemplo do GitHub, instala os pacotes necessários e grava o nome de host da instância de VM em uma página HTML básica.


## <a name="create-a-scale-set"></a>Criar um conjunto de escala
Agora crie um conjunto de dimensionamento de máquinas virtuais com [New-AzVmss](/powershell/module/az.compute/new-azvmss). Para distribuir o tráfego para as instâncias de VM individuais, um balanceador de carga também é criado. O balanceador de carga inclui regras para distribuir o tráfego na porta TCP 80. Ele também permite o tráfego de área de trabalho remota na porta TCP 3389 e comunicação remota do PowerShell na porta TCP 5985. Quando solicitado, você poderá fornecer suas próprias credenciais administrativas para as instâncias de VM no conjunto de dimensionamento:

```azurepowershell-interactive
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myScaleSet" `
  -Location "EastUS" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic"
```

Leva alguns minutos para criar e configurar todos os recursos e as VMs do conjunto de dimensionamento.


## <a name="create-custom-script-extension-definition"></a>Criar definição de Extensão de Script Personalizado
O Azure PowerShell usa uma tabela de hash para armazenar o arquivo a baixar e o comando a executar. No exemplo a seguir, é usado um script de exemplo do GitHub. Primeiro, crie esse objeto de configuração da seguinte maneira:

```azurepowershell-interactive
$customConfig = @{
  "fileUris" = (,"https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate-iis.ps1");
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File automate-iis.ps1"
}
```


Agora, aplique a extensão de Script Personalizado com [Add-AzVmssExtension](/powershell/module/az.Compute/Add-azVmssExtension). O objeto de configuração definido anteriormente é passado para a extensão. Atualize e execute a extensão nas instâncias de VM usando [Update-AzVmss](/powershell/module/az.compute/update-azvmss).


```azurepowershell-interactive
# Get information about the scale set
$vmss = Get-AzVmss `
          -ResourceGroupName "myResourceGroup" `
          -VMScaleSetName "myScaleSet"

# Add the Custom Script Extension to install IIS and configure basic website
$vmss = Add-AzVmssExtension `
  -VirtualMachineScaleSet $vmss `
  -Name "customScript" `
  -Publisher "Microsoft.Compute" `
  -Type "CustomScriptExtension" `
  -TypeHandlerVersion 1.9 `
  -Setting $customConfig

# Update the scale set and apply the Custom Script Extension to the VM instances
Update-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Name "myScaleSet" `
  -VirtualMachineScaleSet $vmss
```

Cada instância de VM no conjunto de dimensionamento baixa e executa o script do GitHub. Em um exemplo mais complexo, vários componentes de aplicativos e arquivos poderiam ser instalados. Se o conjunto de dimensionamento estiver escalado verticalmente, as novas instâncias de VM serão aplicadas automaticamente à mesma definição de Extensão de Script Personalizado e instalarão o aplicativo necessário.


## <a name="allow-traffic-to-application"></a>Permitir o tráfego para o aplicativo

Para permitir o acesso ao aplicativo Web básico, crie um grupo de segurança de rede com [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) e [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup). Para saber mais, confira [Rede para os conjuntos de dimensionamento de máquinas virtuais do Azure](virtual-machine-scale-sets-networking.md).

```azurepowershell-interactive

#Create a rule to allow traffic over port 80
$nsgFrontendRule = New-AzNetworkSecurityRuleConfig `
  -Name myFrontendNSGRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 200 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80 `
  -Access Allow

#Create a network security group and associate it with the rule
$nsgFrontend = New-AzNetworkSecurityGroup `
  -ResourceGroupName  "myResourceGroup" `
  -Location EastUS `
  -Name myFrontendNSG `
  -SecurityRules $nsgFrontendRule

$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName  "myResourceGroup" `
  -Name myVnet

$frontendSubnet = $vnet.Subnets[0]

$frontendSubnetConfig = Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $vnet `
  -Name mySubnet `
  -AddressPrefix $frontendSubnet.AddressPrefix `
  -NetworkSecurityGroup $nsgFrontend

Set-AzVirtualNetwork -VirtualNetwork $vnet

```



## <a name="test-your-scale-set"></a>Testar seu conjunto de dimensionamento
Para ver seu servidor Web em ação, obtenha o endereço IP público do balanceador de carga com [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress). O exemplo a seguir exibe o endereço IP criado no grupo de recursos *myResourceGroup*:

```azurepowershell-interactive
Get-AzPublicIpAddress -ResourceGroupName "myResourceGroup" | Select IpAddress
```

Insira o endereço IP público do balanceador de carga em um navegador da Web. O balanceador de carga distribui o tráfego para uma de suas instâncias de VM, conforme mostrado no exemplo a seguir:

![Página da Web básica em IIS](media/tutorial-install-apps-powershell/running-iis.png)

Deixe o navegador da Web aberto para que você possa ver uma versão atualizada na próxima etapa.


## <a name="update-app-deployment&quot;></a>Atualizar a implantação do aplicativo
Em todo o ciclo de vida de um conjunto de dimensionamento, talvez seja necessário implantar uma versão atualizada de seu aplicativo. Com a Extensão de Script Personalizado, você pode fazer referência a um script de implantação atualizado e, em seguida, aplicar novamente a extensão ao seu conjunto de dimensionamento. Quando o conjunto de dimensionamento foi criado em uma etapa anterior, o `-UpgradePolicyMode` foi definido como *Automático*. Essa configuração permite que as instâncias de VM no conjunto de dimensionamento se atualizem automaticamente e apliquem a versão mais recente de seu aplicativo.

Crie uma nova definição de configuração denominada *customConfigv2*. Essa definição executa uma versão *v2* atualizada do script de instalação do aplicativo:

```azurepowershell-interactive
$customConfigv2 = @{
  &quot;fileUris&quot; = (,&quot;https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate-iis-v2.ps1");
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File automate-iis-v2.ps1"
}
```

Atualize a Configuração de Extensão de Script personalizado às instâncias de VM no conjunto de dimensionamento. A definição *customConfigv2.json* é usada para aplicar a versão atualizada do aplicativo:

```azurepowershell-interactive
$vmss = Get-AzVmss `
          -ResourceGroupName "myResourceGroup" `
          -VMScaleSetName "myScaleSet"
 
$vmss.VirtualMachineProfile.ExtensionProfile[0].Extensions[0].Settings = $customConfigv2
 
Update-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Name "myScaleSet" `
  -VirtualMachineScaleSet $vmss
```

Todas as instâncias de VM no conjunto de dimensionamento são atualizadas automaticamente com a versão mais recente da página da Web de exemplo. Para ver a versão atualizada, atualize o site em seu navegador:

![Página da Web atualizada em IIS](media/tutorial-install-apps-powershell/running-iis-updated.png)


## <a name="clean-up-resources"></a>Limpar os recursos
Para remover o conjunto de dimensionamento e os recursos adicionais, exclua o grupo de recursos e todos os seus recursos com [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup). O parâmetro `-Force` confirma que você deseja excluir os recursos sem um prompt adicional para fazer isso. O parâmetro `-AsJob` retorna o controle ao prompt sem aguardar a conclusão da operação.

```azurepowershell-interactive
Remove-AzResourceGroup -Name "myResourceGroup" -Force -AsJob
```


## <a name="next-steps"></a>Próximas etapas
Neste tutorial você aprendeu a instalar e atualizar aplicativos automaticamente em seu conjunto de dimensionamento com o Azure PowerShell:

> [!div class="checklist"]
> * Instalar automaticamente os aplicativos para o conjunto de dimensionamento
> * Use a Extensão de Script Personalizado do Azure
> * Atualizar um aplicativo em execução em um conjunto de dimensionamento

Avance para o próximo tutorial para aprender a dimensionar automaticamente seu conjunto de dimensionamento.

> [!div class="nextstepaction"]
> [Dimensionar automaticamente seus conjuntos de dimensionamento](tutorial-autoscale-powershell.md)
