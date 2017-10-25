---
title: "Criar um gateway de aplicativo para hospedar vários sites | Microsoft Docs"
description: "Esta página fornece instruções para criar e configurar um Gateway de Aplicativo do Azure para hospedar vários aplicativos Web no mesmo gateway."
documentationcenter: na
services: application-gateway
author: amsriva
manager: rossort
editor: amsriva
ms.assetid: b107d647-c9be-499f-8b55-809c4310c783
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/12/2016
ms.author: amsriva
ms.openlocfilehash: d42efa7d359f5c87c14afbfd138328b37c8ae6c2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-application-gateway-for-hosting-multiple-web-applications"></a>Criar um gateway de aplicativo para hospedar vários aplicativos Web

> [!div class="op_single_selector"]
> * [Portal do Azure](application-gateway-create-multisite-portal.md)
> * [PowerShell do Azure Resource Manager](application-gateway-create-multisite-azureresourcemanager-powershell.md)

A hospedagem de vários sites permite que você implante mais de um aplicativo Web no mesmo gateway de aplicativo. Ela depende da presença do cabeçalho de host na solicitação HTTP de entrada, para determinar quais ouvintes devem receber tráfego. O ouvinte, em seguida, direciona o tráfego ao pool de back-end apropriado conforme configurado na definição de regras do gateway. No caso de aplicativos Web habilitados com SSL, o gateway de aplicativo depende da extensão de SNI (Indicação de Nome de Servidor) para escolher o ouvinte correto para o tráfego da Web. Um uso comum para a hospedagem de vários sites é balancear a carga das solicitações para domínios da Web diferentes para pools de servidor back-end diferentes. Da mesma forma, vários subdomínios do mesmo domínio raiz também podem ser hospedados no mesmo Gateway de Aplicativo.

## <a name="scenario"></a>Cenário

No exemplo a seguir, o gateway de aplicativo está fornecendo o tráfego para contoso.com e fabrikam.com com dois pools de servidor back-end: o pool de servidores contoso e o pool de servidores fabrikam. É possível usar uma configuração semelhante para hospedar subdomínios, como app.contoso.com e blog.contoso.com.

![imageURLroute](./media/application-gateway-create-multisite-azureresourcemanager-powershell/multisite.png)

## <a name="before-you-begin"></a>Antes de começar

1. Instale a versão mais recente dos cmdlets do Azure PowerShell usando o Web Platform Installer. Você pode baixar e instalar a versão mais recente da seção **Windows PowerShell** da [página Downloads](https://azure.microsoft.com/downloads/).
2. Os servidores adicionados ao pool de back-end a fim de usar o gateway de aplicativo devem existir ou ter seus pontos de extremidade criados na rede virtual em uma sub-rede separada ou com um IP/VIP público atribuído.

## <a name="requirements"></a>Requisitos

* **Pool de servidores back-end:** a lista de endereços IP dos servidores back-end. Os endereços IP listados devem pertencer à sub-rede da rede virtual ou devem ser um IP/VIP público. O FQDN também pode ser usado.
* **Configurações do pool de servidores back-end:** cada pool tem configurações como porta, protocolo e afinidade baseada em cookie. Essas configurações são vinculadas a um pool e aplicadas a todos os servidores no pool.
* **Porta front-end:** essa porta é a porta pública aberta no gateway de aplicativo. O tráfego atinge essa porta e é redirecionado para um dos servidores back-end.
* **Ouvinte:** o ouvinte tem uma porta front-end, um protocolo (HTTP ou HTTPS, esses valores diferenciam maiúsculas de minúsculas) e o nome do certificado SSL (caso esteja configurando o descarregamento SSL). Para Application Gateways habilitados para vários sites, os indicadores de SNI e nome do host também são adicionados.
* **Regra:** a regra vincula o ouvinte e o pool de servidores back-end e define a qual pool de servidores back-end o tráfego deve ser direcionado ao atingir um ouvinte específico. As regras são processadas na ordem em que são listadas e o tráfego será direcionado por meio da primeira regra correspondente, independentemente de especificidade. Por exemplo, se você tiver uma regra usando um ouvinte básico e outra usando um ouvinte multissite, ambas na mesma porta, a regra com o ouvinte multissite deverá ser listada antes daquela com o ouvinte básico, para que a função multissite funcione conforme esperado.

## <a name="create-an-application-gateway"></a>Criar um gateway de aplicativo

A seguir estão as etapas necessárias para criar um gateway de aplicativo:

1. Criar um grupo de recursos para o Gerenciador de Recursos.
2. Criar uma rede virtual, uma sub-rede e um IP público para o gateway de aplicativo.
3. Criar um objeto de configuração do gateway de aplicativo.
4. Criar um recurso de gateway de aplicativo.

## <a name="create-a-resource-group-for-resource-manager"></a>Criar um grupo de recursos para o Gerenciador de Recursos

Use a versão mais recente do Azure PowerShell. Há mais informações disponíveis em [Usando o Windows PowerShell com o Gerenciador de Recursos](../powershell-azure-resource-manager.md).

### <a name="step-1"></a>Etapa 1

Fazer logon no Azure

```powershell
Login-AzureRmAccount
```
Você deve se autenticar com suas credenciais.

### <a name="step-2"></a>Etapa 2

Verificar as assinaturas da conta.

```powershell
Get-AzureRmSubscription
```

### <a name="step-3"></a>Etapa 3

Escolha quais das suas assinaturas do Azure deseja usar.

```powershell
Select-AzureRmSubscription -Subscriptionid "GUID of subscription"
```

### <a name="step-4"></a>Etapa 4

Crie um grupo de recursos (ignore esta etapa se você estiver usando um grupo de recursos existente).

```powershell
New-AzureRmResourceGroup -Name appgw-RG -location "West US"
```

Como alternativa, também é possível pode criar marcas para um grupo de recursos para o gateway de aplicativo:

```powershell
$resourceGroup = New-AzureRmResourceGroup -Name appgw-RG -Location "West US" -Tags @{Name = "testtag"; Value = "Application Gateway multiple site"}
```

O Gerenciador de Recursos do Azure requer que todos os grupos de recursos especifiquem um local. Esse local é usado como o local padrão para os recursos do grupo de recursos em questão. Verifique se todos os comandos para criar um gateway de aplicativo usam o mesmo grupo de recursos.

No exemplo acima, criamos um grupo de recursos denominado **appgw-RG** com o local **Oeste dos EUA**.

> [!NOTE]
> Se você precisar configurar uma investigação personalizada para o gateway de aplicativo, veja [Criar um gateway de aplicativo com investigações personalizadas usando o PowerShell](application-gateway-create-probe-ps.md). Confira [investigações personalizadas e monitoramento de integridade](application-gateway-probe-overview.md) para saber mais.

## <a name="create-a-virtual-network-and-subnets"></a>Crie a rede virtual e as sub-redes

O exemplo a seguir mostra como criar uma rede virtual usando o Gerenciador de Recursos. Duas sub-redes são criadas nesta etapa. A primeira sub-rede é para o próprio gateway de aplicativo. O gateway de aplicativo requer sua própria sub-rede para armazenar suas instâncias. Somente outros gateways de aplicativo podem ser implantados na sub-rede. A segunda sub-rede é usada para manter os servidores back-end de aplicativo.

### <a name="step-1"></a>Etapa 1

Atribua o intervalo de endereços 10.0.0.0/24 à variável de sub-rede a ser usada para manter a gateway de aplicativo.

```powershell
$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name appgatewaysubnet -AddressPrefix 10.0.0.0/24
```
### <a name="step-2"></a>Etapa 2

Atribua o intervalo de endereços 10.0.1.0/24 à variável de sub-rede 2 a ser usada para os pools de back-end.

```powershell
$subnet2 = New-AzureRmVirtualNetworkSubnetConfig -Name backendsubnet -AddressPrefix 10.0.1.0/24
```

### <a name="step-3"></a>Etapa 3

Crie uma rede virtual chamada **appgwvnet** no grupo de recursos **appgw-rg** para a região Oeste dos EUA usando o prefixo 10.0.0.0/16 com a sub-rede 10.0.0.0/24 e 10.0.1.0/24.

```powershell
$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet,$subnet2
```

### <a name="step-4"></a>Etapa 4

Atribua uma variável de sub-rede para as próximas etapas, o que criará um gateway de aplicativo.

```powershell
$appgatewaysubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name appgatewaysubnet -VirtualNetwork $vnet
$backendsubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name backendsubnet -VirtualNetwork $vnet
```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Criar um endereço IP público para a configuração de front-end

Crie um recurso IP público **publicIP01** no grupo de recursos **appgw-rg** para a região Oeste dos EUA.

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-RG -name publicIP01 -location "West US" -AllocationMethod Dynamic
```

Um endereço IP é atribuído ao gateway de aplicativo quando o serviço é iniciado.

## <a name="create-application-gateway-configuration"></a>Criar uma configuração do gateway de aplicativo

Você precisa configurar todos os itens de configuração antes de criar o gateway de aplicativo. As etapas a seguir criam os itens de configuração necessários para um recurso de gateway de aplicativo.

### <a name="step-1"></a>Etapa 1

Crie uma configuração de IP do gateway de aplicativo chamada **gatewayIP01**. Quando o Gateway de Aplicativo é iniciado, ele escolhe um endereço IP na sub-rede configurada e no tráfego de rede da rota para os endereços IP no pool de IPs de back-end. Tenha em mente que cada instância usa um endereço IP.

```powershell
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $appgatewaysubnet
```

### <a name="step-2"></a>Etapa 2

Configure o pool de endereços IP de back-end denominado **pool01** e **pool2** com os endereços IP **134.170.185.46**, **134.170.188.221**, **134.170.185.50** para o **pool1** e **134.170.186.46**, **134.170.189.221**, **134.170.186.50** para o **pool2**.

```powershell
$pool1 = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 10.0.1.100, 10.0.1.101, 10.0.1.102
$pool2 = New-AzureRmApplicationGatewayBackendAddressPool -Name pool02 -BackendIPAddresses 10.0.1.103, 10.0.1.104, 10.0.1.105
```

Neste exemplo, haverá dois pools de back-end para rotear o tráfego de rede com base no site solicitado. Um pool recebe o tráfego do site "contoso.com" e outro pool recebe o tráfego do site "fabrikam.com". Você precisa substituir os endereços IP anteriores para adicionar seus próprios pontos de extremidade de endereço IP do aplicativo. Em vez de endereços IP internos, você também pode usar endereços IP públicos, FQDN ou NIC da VM para instâncias de back-end. Para especificar FQDNs em vez de IPs no PowerShell, use o parâmetro "-BackendFQDNs".

### <a name="step-3"></a>Etapa 3

Defina as configurações de **poolsetting01** e **poolsetting02** do gateway de aplicativo para o tráfego de rede com carga balanceada no pool de back-end. Neste exemplo, defina as configurações diferentes de pool de back-end para os pools de back-end. Cada pool de back-end pode ter sua própria configuração de pool de back-end.

```powershell
$poolSetting01 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting01" -Port 80 -Protocol Http -CookieBasedAffinity Disabled -RequestTimeout 120
$poolSetting02 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting02" -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 240
```

### <a name="step-4"></a>Etapa 4

Configure o IP front-end com o ponto de extremidade IP público.

```powershell
$fipconfig01 = New-AzureRmApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip
```

### <a name="step-5"></a>Etapa 5

Configure a porta front-end para um gateway de aplicativo.

```powershell
$fp01 = New-AzureRmApplicationGatewayFrontendPort -Name "fep01" -Port 443
```

### <a name="step-6"></a>Etapa 6

Configure dois certificados SSL para os dois sites para os quais daremos suporte nesse exemplo. Um certificado é para tráfego de contoso.com e o outro é para o tráfego de fabrikam.com. Esses certificados devem ser certificados emitidos por uma Autoridade de Certificação para seus sites. Os certificados autoassinados têm suporte, mas não são recomendados para o tráfego de produção.

```powershell
$cert01 = New-AzureRmApplicationGatewaySslCertificate -Name contosocert -CertificateFile <file path> -Password <password>
$cert02 = New-AzureRmApplicationGatewaySslCertificate -Name fabrikamcert -CertificateFile <file path> -Password <password>
```

### <a name="step-7"></a>Etapa 7

Configure dois ouvintes para os dois sites neste exemplo. Esta etapa configura os ouvintes para o endereço IP público, a porta e o host usados para receber o tráfego de entrada. O parâmetro HostName é necessário para o suporte a vários sites e deve ser definido para o site apropriado no qual o tráfego é recebido. O parâmetro RequireServerNameIndication deve ser definido como true para sites que precisam de suporte para SSL em um cenário de vários hosts. Se o suporte a SSL for necessário, você também precisará especificar o certificado SSL que é usado para proteger o tráfego para aquele aplicativo Web. A combinação de FrontendIPConfiguration, FrontendPort e HostName deve ser exclusiva para um ouvinte. Cada ouvinte pode dar suporte a um certificado.

```powershell
$listener01 = New-AzureRmApplicationGatewayHttpListener -Name "listener01" -Protocol Https -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -HostName "contoso11.com" -RequireServerNameIndication true  -SslCertificate $cert01
$listener02 = New-AzureRmApplicationGatewayHttpListener -Name "listener02" -Protocol Https -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -HostName "fabrikam11.com" -RequireServerNameIndication true -SslCertificate $cert02
```

### <a name="step-8"></a>Etapa 8

Crie duas configurações de regra para os dois aplicativos Web nesse exemplo. Uma regra vincula ouvintes, pools de back-end e as configurações de http. Esta etapa configura o gateway de aplicativo para usar a regra de roteamento Básica, uma para cada site. O tráfego para cada site é recebido pelo seu ouvinte configurado e é direcionado para seu pool de back-end configurado, usando as propriedades especificadas no BackendHttpSettings.

```powershell
$rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule01" -RuleType Basic -HttpListener $listener01 -BackendHttpSettings $poolSetting01 -BackendAddressPool $pool1
$rule02 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule02" -RuleType Basic -HttpListener $listener02 -BackendHttpSettings $poolSetting02 -BackendAddressPool $pool2
```

### <a name="step-9"></a>Etapa 9

Configure o número de instâncias e o tamanho do gateway de aplicativo.

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name "Standard_Medium" -Tier Standard -Capacity 2
```

## <a name="create-application-gateway"></a>Criar um gateway de aplicativo

Crie um gateway de aplicativo com todos os objetos de configuração das etapas anteriores.

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-RG -Location "West US" -BackendAddressPools $pool1,$pool2 -BackendHttpSettingsCollection $poolSetting01, $poolSetting02 -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener01, $listener02 -RequestRoutingRules $rule01, $rule02 -Sku $sku -SslCertificates $cert01, $cert02
```

> [!IMPORTANT]
> O provisionamento do Gateway de Aplicativo é uma operação demorada e pode levar algum tempo para ser concluída.
> 
> 

## <a name="get-application-gateway-dns-name"></a>Obter um nome DNS de gateway de aplicativo

Depois de criar o gateway, a próxima etapa será configurar o front-end para comunicação. Ao usar um IP público, o gateway de aplicativo requer um nome DNS atribuído dinamicamente, o que não é amigável. Para garantir que os usuários finais possam alcançar o gateway de aplicativo, um registro CNAME pode ser usado para apontar para o ponto de extremidade público do gateway de aplicativo. [Configurando um nome de domínio personalizado no Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). Para isso, recupere detalhes do Gateway de Aplicativo e seu nome DNS/IP associado usando o elemento PublicIPAddress anexado ao Gateway de Aplicativo. O nome DNS do Gateway de Aplicativo deve ser usado para criar um registro CNAME que aponta os dois aplicativos Web para esse nome DNS. O uso de registros A não é recomendável, pois o VIP pode mudar na reinicialização do gateway de aplicativo.

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

Saiba como proteger seus sites com o [Gateway de Aplicativo - Firewall de Aplicativo Web](application-gateway-webapplicationfirewall-overview.md)

