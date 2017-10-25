---
title: Usando o Gateway de Aplicativo do Azure com o balanceador de carga interno - PowerShell | Microsoft Docs
description: "Esta página oferece instruções para criar, configurar, iniciar e excluir um gateway de aplicativo do Azure com um ILB (balanceador de carga interno) usando o Gerenciador de Recursos do Azure"
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.assetid: 75cfd5a2-e378-4365-99ee-a2b2abda2e0d
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: davidmu
ms.openlocfilehash: 8d96af009055a5c0349f0ac17054bebee4e54d36
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-application-gateway-with-an-internal-load-balancer-ilb-by-using-azure-resource-manager"></a>Criar um gateway de aplicativo com um ILB (balanceador de carga interno) usando o Gerenciador de Recursos do Azure

> [!div class="op_single_selector"]
> * [Azure Classic PowerShell](application-gateway-ilb.md)
> * [PowerShell do Azure Resource Manager](application-gateway-ilb-arm.md)

O Gateway de Aplicativo do Azure pode ser configurado com um VIP voltado para a Internet ou com um ponto de extremidade interno não exposto à Internet, também conhecido como um ponto de extremidade ILB (balanceador de carga interno). Configurar o gateway como um ILB é útil para aplicativos de linha de negócios internos não expostos à Internet. Isso também é útil para serviços e camadas em um aplicativo multicamada que reside em um limite de segurança não exposto à Internet, mas que ainda exige distribuição de carga round robin, adesão da sessão ou terminação SSL.

Este artigo o orienta ao longo das etapas para configurar um gateway de aplicativo com um ILB.

## <a name="before-you-begin"></a>Antes de começar

1. Instale a versão mais recente dos cmdlets do Azure PowerShell usando o Web Platform Installer. Você pode baixar e instalar a versão mais recente da seção **Windows PowerShell** da [página Downloads](https://azure.microsoft.com/downloads/).
2. Você cria uma rede virtual e uma sub-rede para o Gateway de Aplicativo. Verifique se não há máquinas virtuais ou implantações em nuvem usando a sub-rede. O Gateway de Aplicativo deve estar sozinho em uma sub-rede de rede virtual.
3. Os servidores que você configura para usar o gateway de aplicativo devem existir ou ter seus pontos de extremidade criados na rede virtual ou com um IP/VIP público atribuído.

## <a name="what-is-required-to-create-an-application-gateway"></a>O que é necessário para criar um gateway de aplicativo?

* **Pool de servidores back-end:** a lista de endereços IP dos servidores back-end. Os endereços IP listados devem pertencer à rede virtual, mas em uma sub-rede diferente para o gateway de aplicativo, ou devem ser um IP/VIP público.
* **Configurações do pool de servidores back-end:** cada pool tem configurações como porta, protocolo e afinidade baseada em cookie. Essas configurações são vinculadas a um pool e aplicadas a todos os servidores no pool.
* **Porta front-end:** essa porta é a porta pública aberta no gateway de aplicativo. O tráfego atinge essa porta e é redirecionado para um dos servidores back-end.
* **Ouvinte:** o ouvinte tem uma porta front-end, um protocolo (HTTP ou HTTPS, que diferencia maiúsculas de minúsculas) e o nome do certificado SSL (caso esteja configurando o descarregamento SSL).
* **Regra:** a regra vincula o ouvinte e o pool de servidores back-end e define à qual pool de servidores back-end o tráfego deve ser direcionado quando atinge um ouvinte específico. Atualmente, há suporte apenas para a regra *basic* . A regra *básica* é a distribuição de carga round robin.

## <a name="create-an-application-gateway"></a>Criar um Gateway de Aplicativo

A diferença entre usar o Azure Classic e o Azure Resource Manager é a ordem em que você cria o gateway de aplicativo e os itens que precisam ser configurados.
Com o Gerenciador de Recursos, todos os itens que compõem um gateway de aplicativo são configurados individualmente e, em seguida, reunidos para criar o recurso do gateway de aplicativo.

A seguir, as etapas necessárias para criar um gateway de aplicativo:

1. Criar um grupo de recursos para o Gerenciador de Recursos
2. Criar uma rede virtual e uma sub-rede para o gateway de aplicativo
3. Criar um objeto de configuração do gateway de aplicativo
4. Criar um recurso do gateway de aplicativo

## <a name="create-a-resource-group-for-resource-manager"></a>Criar um grupo de recursos para o Gerenciador de Recursos

Alterne para o modo PowerShell para usar os cmdlets do Gerenciador de Recursos do Azure. Mais informações estão disponíveis em [Usando o Windows PowerShell com o Gerenciador de Recursos](../powershell-azure-resource-manager.md).

### <a name="step-1"></a>Etapa 1

```powershell
Login-AzureRmAccount
```

### <a name="step-2"></a>Etapa 2

Verificar as assinaturas da conta.

```powershell
Get-AzureRmSubscription
```

Você deve se autenticar com suas credenciais.

### <a name="step-3"></a>Etapa 3

Escolha quais das suas assinaturas do Azure deseja usar.

```powershell
Select-AzureRmSubscription -Subscriptionid "GUID of subscription"
```

### <a name="step-4"></a>Etapa 4

Crie um novo grupo de recursos (ignore esta etapa se você estiver usando um grupo de recursos existente).

```powershell
New-AzureRmResourceGroup -Name appgw-rg -location "West US"
```

O Gerenciador de Recursos do Azure requer que todos os grupos de recursos especifiquem um local. Ele é usado como o local padrão para os recursos do grupo de recursos em questão. Verifique se todos os comandos para criar um gateway de aplicativo usam o mesmo grupo de recursos.

No exemplo anterior, criamos um grupo de recursos denominado "appgw-rg" e o local "Oeste dos EUA".

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Criar uma rede virtual e uma sub-rede para o gateway de aplicativo

O exemplo a seguir mostra como criar uma rede virtual usando o Gerenciador de Recursos:

### <a name="step-1"></a>Etapa 1

```powershell
$subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24
```

Essa etapa atribui o intervalo de endereços 10.0.0.0/24 a uma variável de sub-rede a ser usada para criar uma rede virtual.

### <a name="step-2"></a>Etapa 2

```powershell
$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnetconfig
```

Essa etapa cria uma rede virtual chamada "appgwvnet" no grupo de recursos "appgw-rg" para a região Oeste dos EUA usando o prefixo 10.0.0.0/16 com a sub-rede 10.0.0.0/24.

### <a name="step-3"></a>Etapa 3

```powershell
$subnet = $vnet.subnets[0]
```

Essa etapa atribui o objeto de sub-rede à variável $subnet para as próximas etapas.

## <a name="create-an-application-gateway-configuration-object"></a>Criar um objeto de configuração do gateway de aplicativo

### <a name="step-1"></a>Etapa 1

```powershell
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet
```

Essa etapa cria uma configuração de IP do gateway de aplicativo chamada "gatewayIP01". Quando o Gateway de Aplicativo é iniciado, ele escolhe um endereço IP na sub-rede configurada e no tráfego de rede da rota para os endereços IP no pool de IPs de back-end. Tenha em mente que cada instância usa um endereço IP.

### <a name="step-2"></a>Etapa 2

```powershell
$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 10.1.1.8,10.1.1.9,10.1.1.10
```

Essa etapa configura o pool de endereços IP de back-end denominado "pool01" com os endereços IP "10.1.1.8, 10.1.1.9, 10.1.1.10". Esses são os endereços IP que receberão o tráfego de rede proveniente do ponto de extremidade do IP de front-end. Você substitui os endereços IP anteriores para adicionar seus próprios pontos de extremidade de endereço IP do aplicativo.

### <a name="step-3"></a>Etapa 3

```powershell
$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled
```

Essa etapa define a configuração "poolsetting01" do gateway de aplicativo para o tráfego de rede com carga balanceada no pool de back-end.

### <a name="step-4"></a>Etapa 4

```powershell
$fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 80
```

Essa etapa configura a porta do IP de front-end denominada "frontendport01" para o ILB.

### <a name="step-5"></a>Etapa 5

```powershell
$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -Subnet $subnet
```

Essa etapa cria a configuração de IP de front-end chamada "fipconfig01" e a associa a um IP privado da sub-rede da rede virtual atual.

### <a name="step-6"></a>Etapa 6

```powershell
$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp
```

Essa etapa cria o ouvinte chamado "listener01" e associa a porta de front-end à configuração de IP de front-end.

### <a name="step-7"></a>Etapa 7

```powershell
$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
```

Essa etapa cria a regra de roteamento do balanceador de carga chamada "rule01", configurando o comportamento do balanceador de carga.

### <a name="step-8"></a>Etapa 8

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2
```

Essa etapa configura o tamanho da instância do gateway de aplicativo.

> [!NOTE]
> O valor padrão para *InstanceCount* é 2, com um valor máximo de 10. O valor padrão para *GatewaySize* é Medium. Você pode escolher entre Standard_Small, Standard_Medium e Standard_Large.

## <a name="create-an-application-gateway-by-using-new-azureapplicationgateway"></a>Criar um gateway de aplicativo usando New-AzureApplicationGateway

Cria um gateway de aplicativo com todos os itens de configuração das etapas anteriores. Neste exemplo, o gateway de aplicativo é chamado de "appgwtest".

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku
```

Essa etapa cria um gateway de aplicativo com todos os itens de configuração das etapas anteriores. No exemplo, o gateway de aplicativo se chama "appgwtest".

## <a name="delete-an-application-gateway"></a>Excluir um gateway de aplicativo

Para excluir um gateway de aplicativo, você precisa seguir estas etapas em ordem:

1. Use o cmdlet `Stop-AzureRmApplicationGateway` para parar o gateway.
2. Use o cmdlet `Remove-AzureRmApplicationGateway` para remover o gateway.
3. Verifique se o gateway foi removido usando o cmdlet `Get-AzureApplicationGateway`.

### <a name="step-1"></a>Etapa 1

Obtenha o objeto do gateway de aplicativo e associe-o a uma variável "$getgw".

```powershell
$getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg
```

### <a name="step-2"></a>Etapa 2

Use o `Stop-AzureRmApplicationGateway` para parar o gateway de aplicativo. Este exemplo mostra o cmdlet `Stop-AzureRmApplicationGateway` na primeira linha, seguido pela saída.

```powershell
Stop-AzureRmApplicationGateway -ApplicationGateway $getgw  
```

```
VERBOSE: 9:49:34 PM - Begin Operation: Stop-AzureApplicationGateway
VERBOSE: 10:10:06 PM - Completed Operation: Stop-AzureApplicationGateway
Name       HTTP Status Code     Operation ID                             Error
----       ----------------     ------------                             ----
Successful OK                   ce6c6c95-77b4-2118-9d65-e29defadffb8
```

Depois que o gateway de aplicativo estiver em um estado parado, use o cmdlet `Remove-AzureRmApplicationGateway` para remover o serviço.

```powershell
Remove-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Force
```

```
VERBOSE: 10:49:34 PM - Begin Operation: Remove-AzureApplicationGateway
VERBOSE: 10:50:36 PM - Completed Operation: Remove-AzureApplicationGateway
Name       HTTP Status Code     Operation ID                             Error
----       ----------------     ------------                             ----
Successful OK                   055f3a96-8681-2094-a304-8d9a11ad8301
```

> [!NOTE]
> A opção **-force** pode ser usada para suprimir a mensagem de confirmação da remoção.

Para verificar se o serviço foi removido, você pode usar o cmdlet `Get-AzureRmApplicationGateway`. Essa etapa não é necessária.

```powershell
Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg
```

```
VERBOSE: 10:52:46 PM - Begin Operation: Get-AzureApplicationGateway

Get-AzureApplicationGateway : ResourceNotFound: The gateway does not exist.
```

## <a name="next-steps"></a>Próximas etapas

Se desejar configurar o descarregamento SSL, confira [Configurar um gateway de aplicativo para descarregamento SSL](application-gateway-ssl.md).

Se desejar configurar um Gateway de Aplicativo para usar com um ILB, veja [Criar um gateway de aplicativo com um ILB (balanceador de carga interno)](application-gateway-ilb.md).

Se deseja obter mais informações sobre as opções de balanceamento de carga no geral, consulte:

* [Balanceador de carga do Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Gerenciador de Tráfego do Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)

