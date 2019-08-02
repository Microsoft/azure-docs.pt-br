---
title: Início Rápido – Direcionar o tráfego da Web com o Gateway de Aplicativo do Azure – Azure PowerShell | Microsoft Docs
description: Saiba como usar o Azure PowerShell para criar um Gateway de Aplicativo do Azure que direciona o tráfego da Web para máquinas virtuais em um pool de back-end.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 07/17/2019
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: a55f602833cacd27cd82adafd888c67c544564c2
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359981"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---azure-powershell"></a>Início Rápido: Direcionar o tráfego da Web com o Gateway de Aplicativo do Azure – Azure PowerShell

Este início rápido mostra como usar o Azure PowerShell para criar um gateway de aplicativo rapidamente.  Após criar o gateway de aplicativo, teste-o para verificar se está funcionando corretamente. Com o Gateway de Aplicativo do Azure, você direciona seu tráfego de Web de aplicativo para recursos específicos designando ouvintes para portas, criando regras e adicionando recursos a um pool de back-end. Para simplificar, este artigo usa uma configuração simples com um IP de front-end público, um ouvinte básico para hospedar um único site nesse gateway de aplicativo, duas máquinas virtuais usadas para o pool de back-end e uma regra de roteamento de solicitações básica.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Pré-requisitos

### <a name="azure-powershell-module"></a>Módulo do Azure PowerShell

Se você optar por instalar e usar o Azure PowerShell localmente, este tutorial exigirá o módulo do Azure PowerShell versão 1.0.0 ou posterior.

1. Para saber qual é a versão, execute `Get-Module -ListAvailable Az`. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-az-ps). 
2. Para criar uma conexão com o Azure execute `Login-AzAccount`.

### <a name="resource-group"></a>Resource group

No Azure, você pode alocar recursos relacionados a um grupo de recursos. Você pode usar um grupo de recursos existente ou criar um novo. Neste exemplo, você vai criar um novo grupo de recursos usando o cmdlet [New-AzureRmResourceGroup](/powershell/module/Az.resources/new-Azresourcegroup) conforme a seguir: 

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupAG -Location eastus
```

### <a name="required-network-resources"></a>Recursos de rede necessários

Para que o Azure se comunique entre os recursos que você cria, ele precisa de uma rede virtual.  A sub-rede de gateway de aplicativo pode conter apenas gateways de aplicativo. Nenhum outro recurso é permitido.  Você pode criar uma nova sub-rede do Gateway de aplicativo ou usar uma existente. Crie duas sub-redes neste exemplo: uma para o gateway de aplicativo e outra para os servidores de back-end. É possível configurar o IP de front-end do Gateway de Aplicativo como Público ou Privado, de acordo com o caso de uso. Neste exemplo, você escolherá um IP público de front-end.

1. Crie as configurações de sub-rede chamando [New-AzVirtualNetworkSubnetConfig](/powershell/module/Az.network/new-Azvirtualnetworksubnetconfig).
2. Crie a rede virtual com as configurações de sub-rede chamando [New-AzVirtualNetwork](/powershell/module/Az.network/new-Azvirtualnetwork). 
3. Crie o endereço IP público chamando [New-AzPublicIpAddress](/powershell/module/Az.network/new-Azpublicipaddress). 

```azurepowershell-interactive
$agSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myAGSubnet `
  -AddressPrefix 10.0.1.0/24
$backendSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -AddressPrefix 10.0.2.0/24
New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $agSubnetConfig, $backendSubnetConfig
New-AzPublicIpAddress `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myAGPublicIPAddress `
  -AllocationMethod Static `
  -Sku Standard
```
### <a name="backend-servers"></a>Servidores de back-end

O back-end pode ser formado por NICs, conjuntos de dimensionamento de máquinas virtuais, IPs públicos, IPs internos, FQDN (nomes de domínio totalmente qualificados) e back-ends multilocatário como Serviço de Aplicativo do Azure. Neste exemplo, você cria duas máquinas virtuais para que o Azure use como servidores de back-end para o gateway de aplicativo. Você também pode instalar o IIS nas máquinas virtuais para verificar se o Azure criou o gateway de aplicativo com êxito.

#### <a name="create-two-virtual-machines"></a>Criar duas máquinas virtuais

1. Crie uma interface de rede com [New-AzNetworkInterface](/powershell/module/Az.network/new-Aznetworkinterface). 
2. Crie uma configuração de máquina virtual com [New-AzVMConfig](/powershell/module/Az.compute/new-Azvmconfig).
3. Crie a máquina virtual com [New-AzVM](/powershell/module/Az.compute/new-Azvm).

Quando você executa o exemplo de código a seguir para criar as máquinas virtuais, o Azure solicita credenciais. Digite *azureuser* para o nome de usuário e *Azure123456!* para a senha:
    
```azurepowershell-interactive
$vnet   = Get-AzVirtualNetwork -ResourceGroupName myResourceGroupAG -Name myVNet
$subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name myBackendSubnet
$cred = Get-Credential
for ($i=1; $i -le 2; $i++)
{
  $nic = New-AzNetworkInterface `
    -Name myNic$i `
    -ResourceGroupName myResourceGroupAG `
    -Location EastUS `
    -SubnetId $subnet.Id
  $vm = New-AzVMConfig `
    -VMName myVM$i `
    -VMSize Standard_DS2_v2
  Set-AzVMOperatingSystem `
    -VM $vm `
    -Windows `
    -ComputerName myVM$i `
    -Credential $cred
  Set-AzVMSourceImage `
    -VM $vm `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest
  Add-AzVMNetworkInterface `
    -VM $vm `
    -Id $nic.Id
  Set-AzVMBootDiagnostic `
    -VM $vm `
    -Disable
  New-AzVM -ResourceGroupName myResourceGroupAG -Location EastUS -VM $vm
  Set-AzVMExtension `
    -ResourceGroupName myResourceGroupAG `
    -ExtensionName IIS `
    -VMName myVM$i `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
    -Location EastUS
}
```

## <a name="create-an-application-gateway"></a>Criar um Gateway de Aplicativo

### <a name="create-the-ip-configurations-and-frontend-port"></a>Criar as configurações de IP e porta de front-end

1. Use [New-AzApplicationGatewayIPConfiguration](/powershell/module/Az.network/new-Azapplicationgatewayipconfiguration) para criar a configuração que se associa à sub-rede que você criou com o gateway de aplicativo. 
2. Use [New-AzApplicationGatewayFrontendIPConfig](/powershell/module/Az.network/new-Azapplicationgatewayfrontendipconfig) para criar a configuração que atribui o endereço IP público que você criou anteriormente para o gateway de aplicativo. 
3. Use [New-AzApplicationGatewayFrontendPort](/powershell/module/Az.network/new-Azapplicationgatewayfrontendport) para atribuir a porta 80 para acessar o gateway de aplicativo.

```azurepowershell-interactive
$vnet   = Get-AzVirtualNetwork -ResourceGroupName myResourceGroupAG -Name myVNet
$subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name myAGSubnet
$pip    = Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress 
$gipconfig = New-AzApplicationGatewayIPConfiguration `
  -Name myAGIPConfig `
  -Subnet $subnet
$fipconfig = New-AzApplicationGatewayFrontendIPConfig `
  -Name myAGFrontendIPConfig `
  -PublicIPAddress $pip
$frontendport = New-AzApplicationGatewayFrontendPort `
  -Name myFrontendPort `
  -Port 80
```

### <a name="create-the-backend-pool"></a>Criar o pool de back-end

1. Use [New-AzApplicationGatewayBackendAddressPool](/powershell/module/Az.network/new-Azapplicationgatewaybackendaddresspool) para criar o pool de back-end para o gateway do aplicativo. 
2. Defina as configurações para o pool de back-end usando [New-AzApplicationGatewayBackendHttpSetting](/powershell/module/Az.network/new-Azapplicationgatewaybackendhttpsetting).

```azurepowershell-interactive
$address1 = Get-AzNetworkInterface -ResourceGroupName myResourceGroupAG -Name myNic1
$address2 = Get-AzNetworkInterface -ResourceGroupName myResourceGroupAG -Name myNic2
$backendPool = New-AzApplicationGatewayBackendAddressPool `
  -Name myAGBackendPool `
  -BackendIPAddresses $address1.ipconfigurations[0].privateipaddress, $address2.ipconfigurations[0].privateipaddress
$poolSettings = New-AzApplicationGatewayBackendHttpSetting `
  -Name myPoolSettings `
  -Port 80 `
  -Protocol Http `
  -CookieBasedAffinity Enabled `
  -RequestTimeout 120
```

### <a name="create-the-listener-and-add-a-rule"></a>Criar o ouvinte e adicionar uma regra

O Azure requer um ouvinte para habilitar o gateway de aplicativo para rotear o tráfego corretamente para o pool de back-end. O Azure também requer uma regra para o ouvinte saber qual pool de back-end deve ser usado para tráfego de entrada. 

1. Crie um ouvinte usando [New-AzApplicationGatewayHttpListener](/powershell/module/Az.network/new-Azapplicationgatewayhttplistener) com a configuração de front-end e a porta de front-end que você criou anteriormente. 
2. Use [New-AzApplicationGatewayRequestRoutingRule](/powershell/module/Az.network/new-Azapplicationgatewayrequestroutingrule) para criar uma regra denominada *rule1*. 

```azurepowershell-interactive
$defaultlistener = New-AzApplicationGatewayHttpListener `
  -Name myAGListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendport
$frontendRule = New-AzApplicationGatewayRequestRoutingRule `
  -Name rule1 `
  -RuleType Basic `
  -HttpListener $defaultlistener `
  -BackendAddressPool $backendPool `
  -BackendHttpSettings $poolSettings
```

### <a name="create-the-application-gateway"></a>Criar o gateway de aplicativo

Agora que você criou os recursos de suporte necessário, crie o gateway de aplicativo:

1. Use [New-AzApplicationGatewaySku](/powershell/module/Az.network/new-Azapplicationgatewaysku) para especificar parâmetros para o gateway de aplicativo.
2. Use [New-AzApplicationGateway](/powershell/module/Az.network/new-Azapplicationgateway) para criar o gateway de aplicativo.

```azurepowershell-interactive
$sku = New-AzApplicationGatewaySku `
  -Name Standard_v2 `
  -Tier Standard_v2 `
  -Capacity 2
New-AzApplicationGateway `
  -Name myAppGateway `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -BackendAddressPools $backendPool `
  -BackendHttpSettingsCollection $poolSettings `
  -FrontendIpConfigurations $fipconfig `
  -GatewayIpConfigurations $gipconfig `
  -FrontendPorts $frontendport `
  -HttpListeners $defaultlistener `
  -RequestRoutingRules $frontendRule `
  -Sku $sku
```

## <a name="test-the-application-gateway"></a>Testar o gateway de aplicativo

Embora o IIS não seja exigido para criar o gateway de aplicativo, você o instalou neste início rápido para verificar se o Azure criou o gateway de aplicativo com êxito. Use o IIS para testar o gateway de aplicativo:

1. Execute [Get-AzPublicIPAddress](/powershell/module/Az.network/get-Azpublicipaddress) para obter o endereço IP público do gateway de aplicativo. 
2. Copie e cole o endereço IP público na barra de endereços do seu navegador. Quando atualizar o navegador, você deverá ver o nome da máquina virtual. Uma resposta válida verifica se o gateway de aplicativo foi criado com êxito e é capaz de conectar-se com êxito ao back-end.

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

![Teste o gateway de aplicativo](./media/quick-create-powershell/application-gateway-iistest.png)


## <a name="clean-up-resources"></a>Limpar recursos

Quando não precisar mais dos recursos que você criou com o gateway de aplicativo, remova o grupo de recursos. Ao remover o grupo de recursos, você também remove o gateway de aplicativo e todos os recursos relacionados. 

Para remover o grupo de recursos, chame o cmdlet [Remove-AzResourceGroup](/powershell/module/Az.resources/remove-Azresourcegroup) conforme a seguir:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupAG
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Gerenciar o tráfego da web com um gateway de aplicativo usando o Azure PowerShell](./tutorial-manage-web-traffic-powershell.md)

