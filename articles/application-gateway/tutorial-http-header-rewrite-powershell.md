---
title: Reescrever cabeçalhos HTTP no Gateway de Aplicativo do Azure
description: Este artigo fornece informações sobre como criar um Gateway de Aplicativo do Azure e reescrever cabeçalhos HTTP usando o Azure PowerShell
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 12/20/2018
ms.author: absha
ms.openlocfilehash: 4747d824dcf531ed883d476a0daad182ea081c39
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60715089"
---
# <a name="tutorial-create-an-application-gateway-and-rewrite-http-headers"></a>Tutorial: Criar um gateway de aplicativo e reescrever cabeçalhos HTTP

É possível usar o Azure PowerShell para configurar [regras reescrever os cabeçalhos de solicitação e de resposta HTTP](rewrite-http-headers.md) quando você cria o novo [SKU de gateway de aplicativo com dimensionamento automático e redundância de zona](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant)

> [!IMPORTANT] 
> O dimensionamento automático e o gateway de aplicativo com redundância de zona SKU está atualmente em visualização pública. Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Veja os [Termos de Uso Adicionais para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter detalhes. 

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
>
> * Criar uma rede virtual de dimensionamento automático
> * Criar um IP público reservado
> * Configurar a infraestrutura do gateway de aplicativo
> * Especificar a configuração da regra de reescrita do cabeçalho HTTP
> * Especificar o dimensionamento automático
> * Criar o gateway de aplicativo
> * Testar o gateway de aplicativo

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial requer que você execute o Azure PowerShell localmente. É necessário ter instalado o módulo Az versão 1.0.0 ou posterior. Execute `Import-Module Az` e, em seguida, `Get-Module Az` para localizar a versão. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps). Depois de verificar a versão do PowerShell, execute `Login-AzAccount` para criar uma conexão com o Azure.

## <a name="sign-in-to-azure"></a>Entrar no Azure

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos
Crie um grupo de recursos em um dos locais disponíveis.

```azurepowershell
$location = "East US 2"
$rg = "<rg name>"

#Create a new Resource Group
New-AzResourceGroup -Name $rg -Location $location
```

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

Crie uma rede virtual com uma sub-rede dedicada para um gateway de aplicativo com dimensionamento automático. Atualmente, apenas um gateway de aplicativo com dimensionamento automático pode ser implantado em cada sub-rede dedicada.

```azurepowershell
#Create VNet with two subnets
$sub1 = New-AzVirtualNetworkSubnetConfig -Name "AppGwSubnet" -AddressPrefix "10.0.0.0/24"
$sub2 = New-AzVirtualNetworkSubnetConfig -Name "BackendSubnet" -AddressPrefix "10.0.1.0/24"
$vnet = New-AzvirtualNetwork -Name "AutoscaleVNet" -ResourceGroupName $rg `
       -Location $location -AddressPrefix "10.0.0.0/16" -Subnet $sub1, $sub2
```

## <a name="create-a-reserved-public-ip"></a>Criar um IP público reservado

Especifique o método de alocação de PublicIPAddress como **Estático**. O VIP de um gateway de aplicativo com dimensionamento automático só pode ser estático. Não há suporte para IPs dinâmicos. Há suporte somente para o SKU de PublicIpAddress padrão.

```azurepowershell
#Create static public IP
$pip = New-AzPublicIpAddress -ResourceGroupName $rg -name "AppGwVIP" `
       -location $location -AllocationMethod Static -Sku Standard
```

## <a name="retrieve-details"></a>Recuperar detalhes

Recupere detalhes do grupo de recursos, da sub-rede e do IP em um objeto local para criar os detalhes de configuração do IP do gateway de aplicativo.

```azurepowershell
$resourceGroup = Get-AzResourceGroup -Name $rg
$publicip = Get-AzPublicIpAddress -ResourceGroupName $rg -name "AppGwVIP"
$vnet = Get-AzvirtualNetwork -Name "AutoscaleVNet" -ResourceGroupName $rg
$gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name "AppGwSubnet" -VirtualNetwork $vnet
```

## <a name="configure-the-infrastructure"></a>Configurar a infraestrutura

Configure o IP config, front-end IP config, pool de back-end, configurações HTTP, certificado, porta e ouvinte em formato idêntico ao do gateway de aplicativo padrão existente. O novo SKU segue o mesmo modelo de objeto do SKU Standard.

```azurepowershell
$ipconfig = New-AzApplicationGatewayIPConfiguration -Name "IPConfig" -Subnet $gwSubnet
$fip = New-AzApplicationGatewayFrontendIPConfig -Name "FrontendIPCOnfig" -PublicIPAddress $publicip
$pool = New-AzApplicationGatewayBackendAddressPool -Name "Pool1" `
       -BackendIPAddresses testbackend1.westus.cloudapp.azure.com, testbackend2.westus.cloudapp.azure.com
$fp01 = New-AzApplicationGatewayFrontendPort -Name "HTTPPort" -Port 80

$listener01 = New-AzApplicationGatewayHttpListener -Name "HTTPListener" `
             -Protocol Http -FrontendIPConfiguration $fip -FrontendPort $fp01

$setting = New-AzApplicationGatewayBackendHttpSettings -Name "BackendHttpSetting1" `
          -Port 80 -Protocol Http -CookieBasedAffinity Disabled
```

## <a name="specify-your-http-header-rewrite-rule-configuration"></a>Especificar a configuração da regra de reescrita do cabeçalho HTTP

Configure os novos objetos necessários para reescrever os cabeçalhos HTTP:

- **RequestHeaderConfiguration**: este objeto é usado para especificar os campos de cabeçalho de solicitação que você pretende reescrever e o novo valor que deve ser reescrito nos cabeçalhos originais.
- **ResponseHeaderConfiguration**: este objeto é usado para especificar os campos de cabeçalho de resposta que você pretende reescrever e o novo valor que deve ser reescrito nos cabeçalhos originais.
- **ActionSet**: este objeto contém as configurações dos cabeçalhos de solicitação e de resposta especificados acima. 
- **RewriteRule**: este objeto contém todos os *actionSets* especificados acima. 
- **RewriteRuleSet**- este objeto contém todos os *rewriteRules* e deverá ser anexado a uma regra de roteamento solicitação - básica ou com base no caminho.

   ```azurepowershell
   $requestHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "X-isThroughProxy" -HeaderValue "True"
   $responseHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Strict-Transport-Security" -HeaderValue "max-age=31536000"
   $actionSet = New-AzApplicationGatewayRewriteRuleActionSet -RequestHeaderConfiguration $requestHeaderConfiguration -ResponseHeaderConfiguration $responseHeaderConfiguration    
   $rewriteRule = New-AzApplicationGatewayRewriteRule -Name rewriteRule1 -ActionSet $actionSet    
   $rewriteRuleSet = New-AzApplicationGatewayRewriteRuleSet -Name rewriteRuleSet1 -RewriteRule $rewriteRule
   ```

## <a name="specify-the-routing-rule"></a>Especificar a regra de roteamento

Criar uma regra de roteamento de solicitação. Depois de criada, essa configuração de reescrita é anexada ao ouvinte de origem por meio de uma regra de roteamento. Ao usar uma regra de roteamento básica, a configuração de redirecionamento é associada a um ouvinte de origem e será uma reescrita de cabeçalho global. Quando uma regra de roteamento com base em caminho é usada, a configuração de redirecionamento será definida no mapa de caminho de URL. Então ela se aplica somente à área de caminho específica de um site. Abaixo, uma regra de roteamento básica é criada e o conjunto de regras de reescrita é anexado.

```azurepowershell
$rule01 = New-AzApplicationGatewayRequestRoutingRule -Name "Rule1" -RuleType basic `
         -BackendHttpSettings $setting -HttpListener $listener01 -BackendAddressPool $pool -RewriteRuleSet $rewriteRuleSet
```

## <a name="specify-autoscale"></a>Especificar o dimensionamento automático

Agora, você pode especificar a configuração de dimensionamento automático do gateway de aplicativo. Há suporte para dois tipos de configuração de dimensionamento automático:

* **Modo de capacidade fixa**. Nesse modo, o gateway de aplicativo não é dimensionado automaticamente e opera com uma capacidade de unidade de escala fixa.

   ```azurepowershell
   $sku = New-AzApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2 -Capacity 2
   ```

* **Modo de dimensionamento automático**. Neste modo, o gateway de aplicativo é dimensionado automaticamente com base no padrão de tráfego de aplicativo.

   ```azurepowershell
   $autoscaleConfig = New-AzApplicationGatewayAutoscaleConfiguration -MinCapacity 2
   $sku = New-AzApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2
   ```

## <a name="create-the-application-gateway"></a>Criar o gateway de aplicativo

Crie o gateway de aplicativo e inclua zonas de redundância e a configuração de dimensionamento automático.

```azurepowershell
$appgw = New-AzApplicationGateway -Name "AutoscalingAppGw" -Zone 1,2,3 -ResourceGroupName $rg -Location $location -BackendAddressPools $pool -BackendHttpSettingsCollection $setting -GatewayIpConfigurations $ipconfig -FrontendIpConfigurations $fip -FrontendPorts $fp01 -HttpListeners $listener01 -RequestRoutingRules $rule01 -Sku $sku -AutoscaleConfiguration $autoscaleConfig -RewriteRuleSet $rewriteRuleSet
```

## <a name="test-the-application-gateway"></a>Testar o gateway de aplicativo

Use Get-AzPublicIPAddress para obter o endereço IP público do gateway de aplicativo. Copie o endereço IP público ou o nome DNS e cole-o na barra de endereços do seu navegador.

```azurepowershell
Get-AzPublicIPAddress -ResourceGroupName $rg -Name AppGwVIP
```



## <a name="clean-up-resources"></a>Limpar recursos

Primeiro, explore os recursos criados com o gateway de aplicativo. Em seguida, quando não for mais necessário, você pode usar o comando `Remove-AzResourceGroup` para remover o grupo de recursos, o gateway de aplicativo e todos os recursos relacionados.

`Remove-AzResourceGroup -Name $rg`

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar um gateway de aplicativo com regras de roteamento baseadas em caminhos de URL](./tutorial-url-route-powershell.md)
