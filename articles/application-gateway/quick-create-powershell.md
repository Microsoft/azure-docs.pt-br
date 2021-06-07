---
title: 'Início Rápido: Direcionar o tráfego da Web usando o PowerShell'
titleSuffix: Azure Application Gateway
description: Neste guia de início rápido, você aprenderá como usar o Azure PowerShell para criar um Gateway de Aplicativo do Azure que direciona o tráfego da Web para máquinas virtuais em um pool de back-end.
services: application-gateway
author: vhorne
ms.author: victorh
ms.date: 01/19/2021
ms.topic: quickstart
ms.service: application-gateway
ms.custom:
- mvc
- mode-api
ms.openlocfilehash: a4aa16ba4334e5f1e035face9549f4c03ad3a14f
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538563"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway-using-azure-powershell"></a>Início Rápido: Direcionar o tráfego da Web com o Gateway de Aplicativo do Azure usando o Azure PowerShell

Neste início rápido, você usará o Azure PowerShell para criar um gateway de aplicativo. Em seguida, você o testará para verificar se ele funciona corretamente. 

O gateway de aplicativo direciona o tráfego da Web do aplicativo para recursos específicos em um pool de back-end. Você atribuirá ouvintes a portas, criará regras e adicionará recursos a um pool de back-end. Para simplificar, este artigo usa uma configuração simples com um endereço IP de front-end público, um ouvinte básico para hospedar um site no gateway de aplicativo, uma regra básica de roteamento de solicitação e duas máquinas virtuais no pool de back-end.

Conclua também este início rápido usando a [CLI do Azure](quick-create-cli.md) ou o [portal do Azure](quick-create-portal.md).

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Azure PowerShell versão 1.0.0 ou posterior](/powershell/azure/install-az-ps) (se você executar o Azure PowerShell localmente).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="connect-to-azure"></a>Conectar-se ao Azure

Para se conectar com o Azure, execute `Connect-AzAccount`.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

No Azure, você pode alocar recursos relacionados a um grupo de recursos. Você pode usar um grupo de recursos existente ou criar um novo.

Para criar um grupo de recursos, use o cmdlet `New-AzResourceGroup`: 

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupAG -Location eastus
```
## <a name="create-network-resources"></a>Criar recursos da rede

Para que o Azure se comunique entre os recursos que você cria, ele precisa de uma rede virtual.  A sub-rede de gateway de aplicativo pode conter apenas gateways de aplicativo. Nenhum outro recurso é permitido.  Você pode criar uma nova sub-rede do Gateway de aplicativo ou usar uma existente. Crie duas sub-redes neste exemplo: uma para o gateway de aplicativo e outra para os servidores de back-end. É possível configurar o endereço IP de front-end do Gateway de Aplicativo como Público ou Privado, de acordo com o caso de uso. Neste exemplo, você escolherá um endereço IP público de front-end.

1. Crie as configurações de sub-rede usando `New-AzVirtualNetworkSubnetConfig`.
2. Crie a rede virtual com as configurações de sub-rede usando `New-AzVirtualNetwork`. 
3. Crie o endereço IP público usando `New-AzPublicIpAddress`. 
> [!NOTE]
> [As políticas de ponto de extremidade de serviço de rede virtual](../virtual-network/virtual-network-service-endpoint-policies-overview.md) atualmente não têm suporte em uma sub-rede do Gateway de Aplicativo.

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
## <a name="create-an-application-gateway"></a>Criar um Gateway de Aplicativo

### <a name="create-the-ip-configurations-and-frontend-port"></a>Criar as configurações de IP e porta de front-end

1. Use `New-AzApplicationGatewayIPConfiguration` para criar a configuração que associa a sub-rede que você criou ao gateway de aplicativo. 
2. Use `New-AzApplicationGatewayFrontendIPConfig` para criar a configuração que atribui o endereço IP público que você criou anteriormente para o gateway de aplicativo. 
3. Use `New-AzApplicationGatewayFrontendPort` para atribuir a porta 80 para acessar o gateway de aplicativo.

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

1. Use `New-AzApplicationGatewayBackendAddressPool` para criar o pool de back-end para o gateway de aplicativo. O pool de back-end está vazio por enquanto. Ao criar as NICs do servidor de back-end na próxima seção, você as adicionará ao pool de back-end.
2. Defina as configurações para o pool de back-end com `New-AzApplicationGatewayBackendHttpSetting`.

```azurepowershell-interactive
$backendPool = New-AzApplicationGatewayBackendAddressPool `
  -Name myAGBackendPool
$poolSettings = New-AzApplicationGatewayBackendHttpSetting `
  -Name myPoolSettings `
  -Port 80 `
  -Protocol Http `
  -CookieBasedAffinity Enabled `
  -RequestTimeout 30
```

### <a name="create-the-listener-and-add-a-rule"></a>Criar o ouvinte e adicionar uma regra

O Azure requer um ouvinte para habilitar o gateway de aplicativo para rotear o tráfego corretamente para o pool de back-end. O Azure também requer uma regra para o ouvinte saber qual pool de back-end deve ser usado para tráfego de entrada. 

1. Crie um ouvinte usando `New-AzApplicationGatewayHttpListener` com a configuração de front-end e a porta de front-end que você criou anteriormente. 
2. Use `New-AzApplicationGatewayRequestRoutingRule` para criar uma regra chamada *rule1*. 

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

1. Use `New-AzApplicationGatewaySku` para especificar parâmetros para o gateway de aplicativo.
2. Use `New-AzApplicationGateway` para criar o gateway de aplicativo.

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

### <a name="backend-servers"></a>Servidores de back-end

Agora que você criou o Gateway de Aplicativo, crie as máquinas virtuais de back-end que hospedarão os sites. Um back-end pode ser formado por NICs, conjuntos de dimensionamento de máquinas virtuais, endereços IP públicos, endereços IP internos, FQDN (nomes de domínio totalmente qualificados) e back-ends multilocatário como Serviço de Aplicativo do Azure. 

Neste exemplo, você cria duas máquinas virtuais para serem usadas como servidores de back-end para o Gateway de Aplicativo. Você também pode instalar o IIS nas máquinas virtuais para verificar se o Azure criou o gateway de aplicativo com êxito.

#### <a name="create-two-virtual-machines"></a>Criar duas máquinas virtuais

1. Obtenha a configuração do pool de back-end do Gateway de Aplicativo criado recentemente com `Get-AzApplicationGatewayBackendAddressPool`.
2. Crie um adaptador de rede com `New-AzNetworkInterface`.
3. Crie uma configuração de máquina virtual com `New-AzVMConfig`.
4. Crie a máquina virtual com `New-AzVM`.

Quando você executa o exemplo de código a seguir para criar as máquinas virtuais, o Azure solicita credenciais. Insira um nome de usuário e uma senha:
    
```azurepowershell-interactive
$appgw = Get-AzApplicationGateway -ResourceGroupName myResourceGroupAG -Name myAppGateway
$backendPool = Get-AzApplicationGatewayBackendAddressPool -Name myAGBackendPool -ApplicationGateway $appgw
$vnet   = Get-AzVirtualNetwork -ResourceGroupName myResourceGroupAG -Name myVNet
$subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name myBackendSubnet
$cred = Get-Credential
for ($i=1; $i -le 2; $i++)
{
  $nic = New-AzNetworkInterface `
    -Name myNic$i `
    -ResourceGroupName myResourceGroupAG `
    -Location EastUS `
    -Subnet $subnet `
    -ApplicationGatewayBackendAddressPool $backendpool
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

## <a name="test-the-application-gateway"></a>Testar o gateway de aplicativo

Embora o IIS não seja exigido para criar o gateway de aplicativo, você o instalou neste início rápido para verificar se o Azure criou o gateway de aplicativo com êxito.

Use o IIS para testar o gateway de aplicativo:

1. Execute `Get-AzPublicIPAddress` para obter o endereço IP público do gateway de aplicativo. 
2. Copie e cole o endereço IP público na barra de endereços do seu navegador. Quando atualizar o navegador, você deverá ver o nome da máquina virtual. Uma resposta válida verifica se o gateway de aplicativo foi criado com êxito e é capaz de conectar-se com êxito ao back-end.

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

![Teste o gateway de aplicativo](./media/quick-create-powershell/application-gateway-iistest.png)


## <a name="clean-up-resources"></a>Limpar os recursos

Quando não precisar mais dos recursos que você criou com o gateway de aplicativo, exclua o grupo de recursos. Ao excluir o grupo de recursos, exclua também o gateway de aplicativo e todos os recursos relacionados a ele. 

Para excluir o grupo de recursos, chame o cmdlet `Remove-AzResourceGroup`:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupAG
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Gerenciar o tráfego da web com um gateway de aplicativo usando o Azure PowerShell](./tutorial-manage-web-traffic-powershell.md)
