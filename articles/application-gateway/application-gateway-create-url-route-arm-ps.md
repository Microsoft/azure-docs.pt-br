---
title: Criar um gateway de aplicativo usando as regras de roteamento de URL | Microsoft Docs
description: "Esta página oferece instruções para criar e configurar um gateway de aplicativo do Azure usando as regras de roteamento de URL."
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.assetid: d141cfbb-320a-4fc9-9125-10001c6fa4cf
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/03/2017
ms.author: davidmu
ms.openlocfilehash: f2797864d7f0bda35d4d84ee78b157879451f889
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-application-gateway-by-using-path-based-routing"></a>Criar um gateway de aplicativo usando roteamento baseado em caminho

> [!div class="op_single_selector"]
> * [Portal do Azure](application-gateway-create-url-route-portal.md)
> * [PowerShell do Azure Resource Manager](application-gateway-create-url-route-arm-ps.md)
> * [CLI 2.0 do Azure](application-gateway-create-url-route-cli.md)

O roteamento baseado em caminho associa rotas com base no caminho da URL de uma solicitação HTTP. Ele verifica se há uma rota para um pool de back-ends configurado para a URL apresentada no gateway de aplicativo e envia o tráfego de rede para o pool de back-ends definido. Um uso comum para o roteamento baseado em URL é balancear a carga das solicitações para tipos de conteúdo diferentes para pools de servidores back-end diferentes.

O Gateway de Aplicativo do Azure tem dois tipos de regra: roteamento básico e roteamento baseado no caminho. O básico fornece serviço de round robin para os pools de back-end. O roteamento baseado no caminho, além da distribuição round robin, usa o padrão de caminho da URL de solicitação para escolher o pool de back-ends.

## <a name="scenario"></a>Cenário

No exemplo a seguir, o Gateway de Aplicativo fornece o tráfego para contoso.com com dois pools de servidor back-end: o pool de servidores de vídeo e o pool de servidores de imagem.

As solicitações para http://contoso.com/image* são roteadas para o pool de servidores de imagem (**pool1**), e as solicitações para http://contoso.com/video* são roteadas para o pool de servidores de vídeo (**pool2**). Um pool de servidores padrão (**pool1**) será selecionado se nenhum dos padrões de caminho for correspondido.

![Roteamento de URL](./media/application-gateway-create-url-route-arm-ps/figure1.png)

## <a name="before-you-begin"></a>Antes de começar

1. Instale a versão mais recente dos cmdlets do Azure PowerShell usando o Web Platform Installer. Você pode baixar e instalar a versão mais recente na seção **Windows PowerShell** da [página Downloads](https://azure.microsoft.com/downloads/).
2. Crie uma rede virtual e uma sub-rede para um gateway de aplicativo. Verifique se não há máquinas virtuais ou implantações em nuvem usando essa sub-rede. O gateway de aplicativo deve estar sozinho em uma sub-rede de rede virtual.
3. Verifique se os servidores adicionados ao pool de back-ends para o gateway de aplicativo existem, ou se os pontos de extremidade deles foram criados na rede virtual ou com um IP/VIP público atribuído.

## <a name="requirements-to-create-an-application-gateway"></a>Requisitos para criar um gateway de aplicativo

* **Pool de servidores back-end**: a lista de endereços IP dos servidores back-end. Os endereços IP listados devem pertencer à sub-rede da rede virtual ou ser um IP/VIP público.
* **Configurações do pool de servidores back-end**: como porta, protocolo e afinidade baseada em cookie. Essas configurações são vinculadas a um pool e aplicadas a todos os servidores no pool.
* **Porta front-end**: a porta pública aberta no gateway de aplicativo. O tráfego atinge essa porta e é redirecionado a um dos servidores back-end.
* **Ouvinte**: o ouvinte tem uma porta front-end, um protocolo (HTTP ou HTTPS, que diferencia maiúsculas de minúsculas) e o nome do certificado SSL (caso esteja configurando o descarregamento SSL).
* **Regra**: a regra vincula o ouvinte e o pool de servidores back-end e define à qual pool o tráfego deve ser direcionado quando atinge um ouvinte.

## <a name="create-an-application-gateway"></a>Criar um Gateway de Aplicativo

A diferença entre usar o portal clássico do Azure e o Azure Resource Manager é a ordem em que você cria o gateway de aplicativo e os itens que precisam ser configurados.

Com o Gerenciador de Recursos, todos os itens que compõem um gateway de aplicativo serão configurados individualmente e, em seguida, reunidos para criar o recurso do gateway de aplicativo.

Execute estas etapas para excluir um gateway de aplicativo:

1. Criar um grupo de recursos para o Gerenciador de Recursos.
2. Criar uma rede virtual, uma sub-rede e um IP público para o gateway de aplicativo.
3. Criar um objeto de configuração do gateway de aplicativo.
4. Criar um recurso de gateway de aplicativo.

## <a name="create-a-resource-group-for-resource-manager"></a>Criar um grupo de recursos para o Gerenciador de Recursos

Use a versão mais recente do Azure PowerShell. Há mais informações disponíveis em [Como usar o Windows PowerShell com o Gerenciador de Recursos](../powershell-azure-resource-manager.md).

### <a name="step-1"></a>Etapa 1

Fazer logon no Azure.

```powershell
Login-AzureRmAccount
```

Você receberá uma solicitação para se autenticar com suas credenciais.<BR>

### <a name="step-2"></a>Etapa 2

Verificar as assinaturas da conta.

```powershell
Get-AzureRmSubscription
```

### <a name="step-3"></a>Etapa 3

Escolha quais das suas assinaturas do Azure deseja usar. <BR>

```powershell
Select-AzureRmSubscription -Subscriptionid "GUID of subscription"
```

### <a name="step-4"></a>Etapa 4

Crie um grupos de recursos. (Ignore esta etapa se está usando um grupo de recursos existente.)

```powershell
$resourceGroup = New-AzureRmResourceGroup -Name appgw-RG -Location "West US"
```

Como alternativa, é possível criar marcas para um grupo de recursos para o gateway de aplicativo:

```powershell
$resourceGroup = New-AzureRmResourceGroup -Name appgw-RG -Location "West US" -Tags @{Name = "testtag"; Value = "Application Gateway URL routing"} 
```

O Azure Resource Manager exige que os grupos de recursos especifiquem um local padrão, usado para todos os recursos nesse grupo. Verifique se todos os comandos para criar um Gateway de Aplicativo usam o mesmo grupo de recursos.

No exemplo anterior, criamos um grupo de recursos denominado "appgw-RG" e usamos o local "Oeste dos EUA".

> [!NOTE]
> Se você precisar configurar uma investigação personalizada para o gateway de aplicativo, acesse [Criar um gateway de aplicativo com investigações personalizadas usando o PowerShell](application-gateway-create-probe-ps.md). Para saber mais, confira a [Visão geral do monitoramento de integridade do Gateway de Aplicativo](application-gateway-probe-overview.md).
> 
> 

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Criar uma rede virtual e uma sub-rede para o gateway de aplicativo

O exemplo a seguir mostra como criar uma rede virtual usando o Gerenciador de Recursos. Este exemplo cria uma rede virtual para o gateway de aplicativo. O Gateway de Aplicativo exige sua própria sub-rede. Por esse motivo, a sub-rede criada para o gateway de aplicativo é menor do que o espaço de endereço de rede virtual. Isso permite que outros recursos, incluindo, mas não limitado a servidores Web, sejam configurados na mesma rede virtual.

### <a name="step-1"></a>Etapa 1

Atribua o intervalo de endereços 10.0.0.0/24 à variável de sub-rede a ser usada para criar uma rede virtual.  Isso cria o objeto de configuração de sub-rede para o gateway de aplicativo usado no exemplo a seguir.

```powershell
$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24
```

### <a name="step-2"></a>Etapa 2

Crie uma rede virtual chamada **appgwvnet** no grupo de recursos **appgw-rg** para a região Oeste dos EUA usando o prefixo 10.0.0.0/16 com a sub-rede 10.0.0.0/24. Isso conclui a configuração da rede virtual com uma única sub-rede na qual o gateway de aplicativo deve residir.

```powershell
$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet
```

### <a name="step-3"></a>Etapa 3

Atribua a variável de sub-rede para as próximas etapas. Ela será enviada ao cmdlet `New-AzureRMApplicationGateway` em uma etapa futura.

```powershell
$subnet=$vnet.Subnets[0]
```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Criar um endereço IP público para a configuração de front-end

Crie um recurso IP público **publicIP01** no grupo de recursos **appgw-rg** para a região Oeste dos EUA. O Gateway de Aplicativo pode usar um endereço IP público, interno ou de ambos os tipos para receber solicitações de balanceamento de carga.  Este exemplo usa apenas um endereço IP público. No exemplo a seguir, nenhum nome DNS está configurado para criar o endereço IP público, pois o Gateway de Aplicativo não é compatível com nomes DNS personalizados em endereços IP públicos.  Se um nome personalizado for exigido para o ponto de extremidade público, crie um registro CNAME para indicar o nome DNS gerado automaticamente para o endereço IP público.

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-RG -name publicIP01 -location "West US" -AllocationMethod Dynamic
```

Um endereço IP é atribuído ao gateway de aplicativo quando o serviço é iniciado.

## <a name="create-the-application-gateway-configuration"></a>Criar a configuração do gateway de aplicativo

Todos os itens de configuração devem estar configurados antes de criar o gateway de aplicativo. As etapas a seguir criam os itens de configuração necessários para um recurso de gateway de aplicativo.

### <a name="step-1"></a>Etapa 1

Crie uma configuração de IP do gateway de aplicativo chamada **gatewayIP01**. Quando o Gateway de Aplicativo é iniciado, ele escolhe um endereço IP da sub-rede configurada e encaminha o tráfego de rede para os endereços IP no pool de IPs de back-end. Tenha em mente que cada instância usa um endereço IP.

```powershell
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet
```

### <a name="step-2"></a>Etapa 2

Configure o pool de endereços IP de back-end denominado **pool1** e **pool2** com os endereços IP para o **pool1** e **pool2**. Esses são os endereços IP dos recursos que hospedam o aplicativo Web que será protegido pelo gateway de aplicativo. A integridade desses membros do pool de back-ends é validada por investigações básicas ou personalizadas. O tráfego é encaminhado para eles quando a solicitação chega no gateway de aplicativo. Os pools de back-end podem ser usados por várias regras no gateway de aplicativo. Isso significa que um pool de back-ends pode ser usado para vários aplicativos Web que residem no mesmo host.

```powershell
$pool1 = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221, 134.170.185.50

$pool2 = New-AzureRmApplicationGatewayBackendAddressPool -Name pool02 -BackendIPAddresses 134.170.186.47, 134.170.189.222, 134.170.186.51
```

Neste exemplo, dois pools de back-end fazem o roteamento do tráfego de rede baseado no caminho da URL. Um pool recebe o tráfego do caminho de URL "/video", e o outro pool recebe o tráfego do caminho "/image". Substitua os endereços IP anteriores para adicionar seus próprios pontos de extremidade de endereço IP do aplicativo. 

### <a name="step-3"></a>Etapa 3

Defina as configurações de **poolsetting01** e **poolsetting02** do gateway de aplicativo para o tráfego de rede com carga balanceada no pool de back-end. Neste exemplo, defina as configurações diferentes de pool de back-end para os pools de back-end. Cada pool de back-end pode ter sua própria configuração.  As regras usam as configurações HTTP de back-end para rotear o tráfego para os membros do pool de back-end corretos. Isso determina o protocolo e a porta usados para enviar tráfego aos membros do pool de back-ends. As sessões baseadas em cookies também são determinadas pelas configurações HTTP de back-end. Se habilitada, a afinidade de sessão baseada em cookies envia o tráfego para o mesmo back-end das solicitações anteriores para cada pacote.

```powershell
$poolSetting01 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting01" -Port 80 -Protocol Http -CookieBasedAffinity Disabled -RequestTimeout 120

$poolSetting02 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting02" -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 240
```

### <a name="step-4"></a>Etapa 4

Configure o IP front-end com pontos de extremidade de IP públicos. Um ouvinte usa o objeto de configuração de IP front-end para relacionar o endereço IP voltado para fora com o ouvinte.

```powershell
$fipconfig01 = New-AzureRmApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip
```

### <a name="step-5"></a>Etapa 5

Configure a porta front-end para um gateway de aplicativo. O ouvinte usa o objeto de configuração de porta front-end para definir a porta cujo tráfego deve ser ouvido pelo gateway de aplicativo.

```powershell
$fp01 = New-AzureRmApplicationGatewayFrontendPort -Name "fep01" -Port 80
```

### <a name="step-6"></a>Etapa 6

Configure o ouvinte para o endereço IP público, e a porta usada para receber o tráfego de rede. O exemplo a seguir usa a configuração de IP de front-end definida anteriormente, uma configuração de porta front-end e um protocolo (Http ou Https, que diferencia maiúsculas de minúsculas) e configura o ouvinte. Neste exemplo, o ouvinte ouve o tráfego HTTP na porta 80 no endereço IP público que foi criado anteriormente.

```powershell
$listener = New-AzureRmApplicationGatewayHttpListener -Name "listener01" -Protocol Http -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01
```

### <a name="step-7"></a>Etapa 7

Configure os caminhos de regra de URL para os pools de back-end. Esta etapa configura o caminho relativo usado pelo Gateway de Aplicativo e define o mapeamento entre o caminho da URL e o pool de back-end que será atribuído para lidar com o tráfego de entrada.

> [!IMPORTANT]
> Cada caminho deve começar com uma "/", e um asterisco só é permitido no final. Exemplos válidos são /xyz, /xyz *, ou /xyz/*. A cadeia de caracteres inserida no correspondente de caminho não inclui qualquer texto após o primeiro "?" ou "#", e esses caracteres não são permitidos. 

O exemplo a seguir cria duas regras: uma para o caminho "/imagem/" que faz o roteamento do tráfego para o back-end **pool1** e outra para o caminho "/video/" que faz o roteamento para o back-end **pool2**. Essas regras garantem que o tráfego para cada conjunto de URLs seja roteado para o back-end. Por exemplo, http://contoso.com/image/figure1.jpg vai para o **pool1** e http://contoso.com/video/example.mp4 vai para o **pool2**.

```powershell
$imagePathRule = New-AzureRmApplicationGatewayPathRuleConfig -Name "pathrule1" -Paths "/image/*" -BackendAddressPool $pool1 -BackendHttpSettings $poolSetting01

$videoPathRule = New-AzureRmApplicationGatewayPathRuleConfig -Name "pathrule2" -Paths "/video/*" -BackendAddressPool $pool2 -BackendHttpSettings $poolSetting02
```

A configuração de mapa do caminho de regra também configurará um pool de endereços de back-end padrão se o caminho não corresponder a nenhuma das regras de caminho predefinidas. Por exemplo, http://contoso.com/shoppingcart/test.html vai para o **pool1**, já que ele está definido como o pool padrão para tráfego sem correspondência.

```powershell
$urlPathMap = New-AzureRmApplicationGatewayUrlPathMapConfig -Name "urlpathmap" -PathRules $videoPathRule, $imagePathRule -DefaultBackendAddressPool $pool1 -DefaultBackendHttpSettings $poolSetting02
```

### <a name="step-8"></a>Etapa 8

Crie uma configuração de regra. Esta etapa configura o gateway de aplicativo a fim de usar o roteamento de com base no caminho de URL. A variável `$urlPathMap` definida na etapa anterior é usada agora para criar a regra baseada no caminho. Nesta etapa, associe a regra com um ouvinte e o mapeamento de caminho de URL criado anteriormente.

```powershell
$rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType PathBasedRouting -HttpListener $listener -UrlPathMap $urlPathMap
```

### <a name="step-9"></a>Etapa 9

Configure o número de instâncias e o tamanho do gateway de aplicativo.

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name "Standard_Small" -Tier Standard -Capacity 2
```

## <a name="create-an-application-gateway"></a>Criar um Gateway de Aplicativo

Crie um gateway de aplicativo com todos os objetos de configuração das etapas anteriores.

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-RG -Location "West US" -BackendAddressPools $pool1,$pool2 -BackendHttpSettingsCollection $poolSetting01, $poolSetting02 -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener -UrlPathMaps $urlPathMap -RequestRoutingRules $rule01 -Sku $sku
```

## <a name="get-an-application-gateway-dns-name"></a>Como obter um nome DNS do gateway de aplicativo

Após a criação do gateway, você configurará o front-end para comunicação. Ao usar um IP público, o Gateway de Aplicativo requer um nome DNS atribuído dinamicamente, o que não é amigável. Para garantir que os clientes possam acessar o gateway de aplicativo, você poderá usar um registro CNAME para apontar para o ponto de extremidade público do gateway de aplicativo. Para saber mais, confira [Configurar um nome de domínio personalizado nos serviços de nuvem do Azure](../cloud-services/cloud-services-custom-domain-name-portal.md).

Para configurar o registro CNAME de IP de front-end, recupere detalhes do Gateway de Aplicativo e seu nome DNS/IP associado usando o elemento PublicIPAddress anexado ao Gateway de Aplicativo. Use o nome DNS do gateway de aplicativo para criar um registro CNAME. Não recomendamos o uso de registros A, pois o VIP pode ser alterado na reinicialização do Gateway de Aplicativo.

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName appgw-RG -Name publicIP01
```

```
Name                     : publicIP01
ResourceGroupName        : appgw-RG
Location                 : westus
Id                       : /subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/publicIPAddresses/publicIP01
Etag                     : W/"00000d5b-54ed-4907-bae8-99bd5766d0e5"
ResourceGuid             : 00000000-0000-0000-0000-000000000000
ProvisioningState        : Succeeded
Tags                     : 
PublicIpAllocationMethod : Dynamic
IpAddress                : xx.xx.xxx.xx
PublicIpAddressVersion   : IPv4
IdleTimeoutInMinutes     : 4
IpConfiguration          : {
                                "Id": "/subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/applicationGateways/appgwtest/frontendIP
                            Configurations/frontend1"
                            }
DnsSettings              : {
                                "Fqdn": "00000000-0000-xxxx-xxxx-xxxxxxxxxxxx.cloudapp.net"
                            }
```

## <a name="next-steps"></a>Próximas etapas

Se você quiser aprender sobre o descarregamento de protocolo SSL, confira [Configurar um gateway de aplicativo para descarregamento SSL usando o Azure Resource Manager](application-gateway-ssl-arm.md).

